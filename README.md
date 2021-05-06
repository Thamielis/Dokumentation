---
description: Automatisierte Pflege von Microsoft Office 365-Netzwerken
---

# Sophos UTM RESTful-API

**Viele Unternehmen nutzen inzwischen Dienste von Office 365. Die Client-Anwendungen dieser Dienste reagieren teilweise sehr „allergisch“ darauf, wenn der Datentransfer mittels eines Proxys entschlüsselt und geprüft werden, so auch bei einem unserer Kunden. Microsoft empfiehlt hier, dass seine Dienste direkt erreichbar sein sollen, um eine optimale Nutzererfahrung zu bieten. Unser Kunde nutzt selbstverständlich einen Proxy, sodass seine Nutzererfahrung nicht optimal war. Wir stellen Ihnen hier eine Möglichkeit vor, wie wir das Problem mit unserem Kunden gelöst haben um den Datenverkehr zu den Office 365 Diensten ohne Nutzung des Proxys zu erlauben.**

## Vorbereitungen

Für diesen Blogbeitrag gehen wir davon aus, dass die RESTful-API der Sophos UTM erreichbar ist, als das auch ein **Token für die Nutzung** dieser erstellt wurde. Mehr dazu erfahren Sie in unserem [letzten Blogbeitrag zur SOPHOS UTM RESTful API.](http://xn--sophos%20utm%20%20konfiguration%20mit%20restful%20api-ml07a/)

Für die erfolgreiche Ausführung dieses Scripts benötigt der Benutzer mindestens das Recht **„Network Protection Manager“**.

Über das Script werden wir eine Netzwerk Gruppe der UTM mit den offiziellen Office 365 Netzwerken von Microsoft füllen. Für die RESTful-API benötigen wir wieder die **Referenz des Objektes** \(unserer Referenzgruppe\), welche wir uns über den bekannten Befehl „config-watch.plx -v“ in einer **SSH-Sitzung** anzeigen lassen.

```bash
192:/root \# confd-watch.plx -v# 
  watching the Confd storage on ASG 9.704002 
s 1 caught USR1 signal(s) 
vc 69 70 data version change detected at Thu Sep 17 16:06:13 2020 
o+ REF\_NetGroO365networ network group created
```

Unsere Referenz lautet also „REF\_NetGroO365networ“. Diese werden wir später mittels einer Eingabedatei **an das Script übermitteln.** Nun benötigen wir noch eine **Firewall-Regel**, damit der Datenverkehr zu den gewünschten Zielnetzwerken ermöglicht wird.

[![Sophos UTM RESTful-API: Firewall-Regel](https://blog.to.com/wp-content/uploads/2020/10/000114.png)](https://blog.to.com/sophos-utm-rest-api-office365-netzwerke/#)

Eine weitere Notwendigkeit ist, das dass **System auf welchem das Script ausgeführt** wird, die [Referenz-Webseite](https://endpoints.office.com/) von Microsoft erreicht.

## Das Script

```text
[CmdletBinding(SupportsShouldProcess=$True)]
Param (
    [Parameter(Mandatory = $false)]
    [ValidateNotNullOrEmpty()]
    [String] $UTMFile = ".\utmliste.txt",

    [Parameter(Mandatory = $false)]
    [ValidateSet('Worldwide', 'Germany')]
    [String] $O365_Instance = "Worldwide",

    [Parameter(Mandatory = $false)]
    [ValidateNotNullOrEmpty()]
    [guid] $O365_ClientRequestId = (new-guid)
)

[String] $O365_BaseURL = "https://endpoints.office.com/endpoints"

Class O365Network
{
    [String]$ip
    [String]$name
    [String]$utm_ref
    O365Network (
        [String]$ip,
        [String]$name,
        [String]$utm_ref
    ) {
        $this.ip = $ip
        $this.name = $name
        $this.utm_ref = $utm_ref
    }
}

function Read-UTM-Hosts {
    return (Import-Csv -Path $UTMFile)
}

function Get-O365-URL {
    return (
        "{0}/{1}?noipv6&ClientRequestId={2}" -f ($O365_BaseURL,  $O365_Instance, $O365_ClientRequestId)
    )
}

function Get-O365-Data {
    [String] $url = Get-O365-URL
    try {
        $data = Invoke-RestMethod -Uri $url
        $iplist = $data.ips | sort -Unique
    }
    catch {
        $iplist = $null
    }
    return $iplist
}

function Get-O365-Networks {
    $iplist = Get-O365-Data
    if($iplist) {
        $netobjlist = (
            $iplist | % { [O365Network]::new($_, "O365_{0}" -f $_, $null); }
        )
    }
    else {
        $netobjlist = $iplist
    }
    return $netobjlist
}

function UTM-API-Call {
    Param (
        [Parameter(Mandatory = $true)]
        [ValidateNotNullOrEmpty()]
        [String] $Path,

        [Parameter(Mandatory = $false)]
        [ValidateNotNullOrEmpty()]
        [String] $Ref = $null,

        [Parameter(Mandatory = $false)]
        [AllowNull()]
        [Object] $Body = $null,

        [Parameter(Mandatory = $true)]
        [ValidateSet('GET', 'POST', 'PATCH', 'DELETE')]
        [String] $Method = 'GET'
    )
    $token = [Convert]::ToBase64String([System.Text.Encoding]::Default.GetBytes("token:{0}" -f $current_utm_host.token))
    $headers = @{}
    $headers.add("Authorization",'Basic ' + $token)
    $headers.add("Content-Type", "application/json")
    $headers.add("Accept", "application/json")

    if ($method -eq 'DELETE') {
        $headers.add("X-Restd-Err-Ack", "all")
    }

    [String] $api_base_url = "https://{0}" -f $current_utm_host.socket

    # base_url + "/api/" + request path
    [String] $api_url = "{0}/api/{1}" -f ($api_base_url, $Path)
    # if a ref has been given, append it to the API url
    if ($Ref) { $api_url = "{0}{1}" -f ($api_url, $Ref); }

    # construct arguments for Invoke-RestMethod call
    $kwargs = @{
        Headers = $headers
        Method = $Method
        Uri = $api_url
    }

    if ($Body) {$kwargs["Body"] = $Body; }
    return (Invoke-RestMethod @kwargs)
}


function Get-UTM-Networks {
    $group = UTM-API-Call -Method 'GET' -Path "objects/network/group/" -Ref $current_utm_host.group_ref

    $networkUTMlist = @()
    foreach ($member_ref in $group.members){
        $network = UTM-API-Call -Method 'GET' -Path "objects/network/network/" -Ref $member_ref
        $networkUTMlist += [O365Network]::new($null, $network.name, $network._ref)
    }
    return $networkUTMlist
}

function Delete-UTM-Networks {
    Param (
        [Parameter(Mandatory = $true)]
        [Object] $NetworksDelete
    )
    foreach ($network in $NetworksDelete) {
        UTM-API-Call -Method 'DELETE' -Path "objects/network/network/" -Ref $network.utm_ref
    }
}

function Add-UTM-Networks {
    Param (
        [Parameter(Mandatory = $true)]
        [Object] $NetworksAdd
    )

    foreach ($network in $NetworksAdd.Values) {
        if (-not $network.utm_ref) {
            $net = $network.ip.split("/")
            $body = '{"name":"' + $network.name + '","address":"' + $net[0] + '","netmask":"' + $net[1] + '","resolved":true}'
            $result = UTM-API-Call -Method 'POST' -Path "objects/network/network/" -Body $body
            if ($result) {
                $network.utm_ref = $result._ref
            }
        }
    }
}

function Update-UTM-Group {
    Param (
        [Parameter(Mandatory = $true)]
        [Object] $NetworksUpdate
    )

    $netref = (
        $NetworksUpdate.Values.utm_ref | Where-Object { $_; } | % { '"{0}"' -f $_; }
    ) -join ","

    $body = '{"members":[' + $netref + ']}'
    UTM-API-Call -Method 'PATCH' -Path "objects/network/group/" -Ref $current_utm_host.group_ref -Body $body | out-null
}

#Begin des Scripts
$o365_networks = Get-O365-Networks

#Anpassen des SSL Verhaltens der Powershell
Add-Type @"
    using System.Net;
    using System.Security.Cryptography.X509Certificates;
    public class TrustAllCertsPolicy : ICertificatePolicy {
        public bool CheckValidationResult(
             ServicePoint srvPoint, X509Certificate certificate,
             WebRequest request, int certificateProblem) {
            return true;
        }
    }
"@

[System.Net.ServicePointManager]::CertificatePolicy = New-Object TrustAllCertsPolicy
if ($o365_networks) {
    foreach ($current_utm_host in (Read-UTM-Hosts)) {
        write-host $current_utm_host.socket
        $utm_networks = Get-UTM-Networks
        $networks = @{}
        $networks_delete = @()
        foreach ($network in $o365_networks){
            # reset UTM_REF
            $network.utm_ref = $null
            $networks.add($network.name, $network)
        }
        foreach ($network in $utm_networks){
            if ($networks.ContainsKey($network.name)) {
                $mod = $networks[$network.name]
                $mod.utm_ref = $network.utm_ref
            }
            else {

                $networks_delete += $network
            }
        }
        if ($networks_delete){
            Delete-UTM-Networks -NetworksDelete $networks_delete
        }
            Add-UTM-Networks -NetworksAdd $networks
            Update-UTM-Group -NetworksUpdate $networks
        }
    }
else {
    write-host "Fehler im Abruf der O365-Referenz Datei."
    write-host "Prüfen sie die Internetverbindung für den Aufruf der Webseite https://endpoints.office.com/endpoints/worldwide?clientrequestid=insert your ID"

}
```

## Funktionsweise des Scripts „in a Nutshell“

Das Script arbeitet nun folgende Schritte ab:

### **1. Schritt**

Die **aktuelle Liste der IP-Netzwerke** wird von der offiziellen Microsoft Referenzwebseite abgerufen und aufbereitet, sodass alle Datensätze im Format „IP-Adresse“/“Subnetzmaske“ vorliegen und einzigartig sind.

### 2. Schritt

Alle Referenzen der Objekte, welche sich in der zu befüllenden Gruppe befinden, werden abgerufen und **für die weitere Verwendung aufbereitet**.

### 3. Schritt

Die zurückgelieferten Werte aus Schritt eins und Schritt zwei werden nun anhand folgender Logik **verglichen und ausgewertet**:

* hinzuzufügende Netzwerke \(in Schritt eins gelieferte Netzwerke, doch nicht in Schritt zwei\)
* bereits vorhandene Netzwerke \(in Schritt eins und Schritt zwei gelieferte Netzwerke\)
* zu löschenden Netzwerke \(nur Schritt zwei gelieferte Netzwerke\)

### 4. Schritt

Die nicht mehr benötigten Netzwerke werden **gelöscht**. Die neu hinzuzufügenden Netzwerke werden hinzugefügt \(alle Netzwerke erhalten im Namen den Präfix „O365\_“\). Die Referenzgruppe wird aktualisiert.

Wichtig ist an dieser Stelle zu erwähnen, dass wir in allen von Schritt eins zurückgelieferten Werte Netzwerke sehen, auch wenn dies eine einzelne IP-Adresse ist.  
Ebenfalls wichtig ist, dass alle Objekte, die jemals in der Referenzgruppe beinhaltet sind, bei **Ausführung des Scripts ohne Nachfrage gelöscht** werden, wenn diese nicht durch Schritt eins zurückgeliefert werden. Dies soll „Datenmüll“ auf der UTM vermeiden, **verhindert** aber auch eine **weitere Nutzung der Netzwerkobjekte**, welche in unserer Referenzgruppe beinhaltet sind. In der Gruppe sind dadurch nur noch Objekte vorhanden, welche durch das Script erstellt wurden. Die **Gruppe** selbst kann natürlich **frei verwendet werden**.

## Die Nutzung des Scripts

Das Script kann über Parameter aufgerufen werden. Die Parameter lauten:

* UTMFile
* O365\_Instance
* O365\_ClientRequestId

Für alle Parameter sind innerhalb des Scripts **Standardwerte** definiert.

Für die korrekte Funktion des Scripts wird eine **Eingabedatei** namens „utmliste.txt“ benötigt und muss standardmäßig **im selben Verzeichnis** wie das Script abgespeichert sein. Wenn dies nicht der Fall ist, muss der Speicherpfad der „utmliste.txt“ mittels des Parameters „UTMFile“ an das Script übermittelt werden.

Der **Inhalt der Datei** ist folgender:

* socket -&gt; IP-Adresse und Port des WebAdmin Portal der Sophos UTM
* group\_ref -&gt; Referenz zu dem Gruppenobjekt
* token -&gt; der Api Token

Die Anfangszeile der Datei darf **nicht verändert** werden. Hier ein Beispielinhalt der „utmliste.txt“:

```text
socket,group_ref,token 
192.168.2.4:4444,REF_NetGroO365networ, XXXxxxXxxxXXXXxxxx
```

Da das Script dafür konzipiert wurde, automatisiert und regelmäßig auf einem System ausgeführt zu werden, sind die **Ausgaben des Scripts** innerhalb der Powershell **minimal**.

### Hierzu ein Beispiel an einem Testgerät:

Für Ausgangssituation haben wir alle **Voraussetzungen** geschaffen.

* REST API User inklusive Token
* Die Referenz zur Gruppe für die Office 365-Netzwerke
* Das ausführende System hat Zugriff hat Zugriff auf den WebAdmin der UTM
* Das ausführende System hat Zugriff zur Webseite [https://endpoints.office.com](https://endpoints.office.com/endpoints)

Unsere **Ausganssituation** stellt sich im WebAdmin also wie folgt dar:

[![Sophos UTM RESTful-API: WebAdmin](https://blog.to.com/wp-content/uploads/2020/10/000118.png)](https://blog.to.com/sophos-utm-rest-api-office365-netzwerke/#)

Wenn das Script nun ausgeführt wurde sehen wir, dass **51 Objekte** angelegt wurden:

[![Sophos UTM RESTful-API: WebAdmin 2](https://blog.to.com/wp-content/uploads/2020/10/000117.png)](https://blog.to.com/wp-content/uploads/2020/10/000117.png)

Und die reduzierte **Ausgabe der SSH-Sitzung**:

```bash
192:/root # confd-watch.plx -v

   watching the Confd storage on ASG 9.704002




s  1  caught USR1 signal(s)

vc 67 68  data version change detected at Fri Sep 18 16:05:29 2020

o+ REF_NetNetO365201901 network network  created

   netmask6 = 128

   name = O365_20.190.128.0/18

   resolved = 1

   resolved6 = 0

   address6 = Empty SCALAR

   comment = Empty SCALAR

   interface = Empty SCALAR

   netmask = 18

   address = 20.190.128.0




s  1  caught USR1 signal(s)

vc 68 69  data version change detected at Fri Sep 18 16:05:29 2020

o+ REF_NetNetO365522381 network network  created




...




s  1  caught USR1 signal(s)

vc 117 118  data version change detected at Fri Sep 18 16:05:40 2020

o+ REF_NetNetO365408115 network network  created

   netmask6 = 128

   name = O365_40.81.156.154/32

   resolved = 1

   resolved6 = 0

   address6 = Empty SCALAR

   comment = Empty SCALAR

   interface = Empty SCALAR

   netmask = 32

   address = 40.81.156.154




s  1  caught USR1 signal(s)

vc 118 119  data version change detected at Fri Sep 18 16:05:40 2020

oc REF_NetGroO365networ network group members,types  changed

   members = [ REF_NetNetO365104146, REF_NetNetO365104422, REF_NetNetO365104470, REF_NetNetO365131071, REF_NetNetO3651310712, REF_NetNetO3651310713, REF_NetNetO3651310714, REF_NetNetO365131076, REF_NetNetO3651310762, REF_NetNetO3651310763, REF_NetNetO3651310764, REF_NetNetO365131077, REF_NetNetO365131079, REF_NetNetO365131253, REF_NetNetO365132245, REF_NetNetO365138012, REF_NetNetO365139191, REF_NetNetO365150171, REF_NetNetO3651501712, REF_NetNetO365157551, REF_NetNetO3651575512, REF_NetNetO365157552, REF_NetNetO365201901, REF_NetNetO365204791, REF_NetNetO365231031, REF_NetNetO365401040, REF_NetNetO365401070, REF_NetNetO365401081, REF_NetNetO365401260, REF_NetNetO365408115, REF_NetNetO365409021, REF_NetNetO365409200, REF_NetNetO365409600, REF_NetNetO365521000, REF_NetNetO365521040, REF_NetNetO365521080, REF_NetNetO365521120, REF_NetNetO365521200, REF_NetNetO365521745, REF_NetNetO365521837, REF_NetNetO365521841, REF_NetNetO365522381, REF_NetNetO3655223812, REF_NetNetO365522387, REF_NetNetO365522441, REF_NetNetO365522442, REF_NetNetO3655224422, REF_NetNetO3655224423, REF_NetNetO365522443, REF_NetNetO365522471, REF_NetNetO365529600 ]

   types = [ network ]
```

Bei unserem Kunden wird das Script durch ein **weiteres Script zur automatisierten Erstellung einer PAC/WPAD-Datei** abgerundet. Dieses Script basiert auf ein, durch Microsoft zur Verfügung gestelltes, Script \([https://www.powershellgallery.com/packages/Get-PacFile/1.0.4](https://www.powershellgallery.com/packages/Get-PacFile/1.0.4)\) und **erweitert in unserem Fall ein Template des Kunden**, damit nicht nur die Office 365-Adressen in der PAC/WPAD-Konfigurationsdatei beinhaltet sind. Wenn Sie Fragen zum beschriebenen Scrip haben, melden Sie sich gerne bei uns oder kommentieren diesen Beitrag.

[Weitere Informationen und Referenzen zu erfolgreich durchgeführten Kundenprojekten finden Sie auch auf unserer Webseite.](https://security.to.com/referenzen)

