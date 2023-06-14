---
id: kmxycjxb
title: Windows 10 22H2 v22 Deployment
file_version: 1.1.2
app_version: 1.10.2
---

<!--MERMAID {width:100}-->
```mermaid
%%{init: {'maxTextSize': 99000, 'stateDiagram-v2' : { 'curve' : 'catmullRom' } } }%%
stateDiagram-v2
[\*] --> Start\_Task\_Sequence:::start
%%Start\_Task\_Sequence:::start
Main:::group
Initialization:::group
Start\_Task\_Sequence-->Main
Main-->Initialization
Set\_TS\_Start\_Time:::step
TSBackgroundInit:::step
TSBConnectionInfo:::step
GatherLocal:::step
TSBIOSConfiguration:::tsvariable
TSInstallSoftware:::tsvariable
TSInstallUpdates:::tsvariable
state Main {
state TSBIOSConfiguration <<choice>>
Initialization --> TSBIOSConfiguration
TSBIOSConfiguration --> 01\_BIOS\_Configuration : True
TSBIOSConfiguration --> 02\_OS\_Installation : False
01\_BIOS\_Configuration --> 02\_OS\_Installation
state TSInstallSoftware <<choice>>
02\_OS\_Installation --> TSInstallSoftware
TSInstallSoftware --> 04\_Software\_Install : True
TSInstallSoftware --> TSInstallUpdates : False
%%04\_Software\_Install --> 05\_Install\_Updates
state TSInstallUpdates <<choice>>
04\_Software\_Install --> TSInstallUpdates
TSInstallUpdates --> 05\_Install\_Updates : True
TSInstallUpdates --> 06\_Finalizing\_OS : False
05\_Install\_Updates --> 06\_Finalizing\_OS
}
state Initialization {
direction LR
Set\_TS\_Start\_Time --> TSBackgroundInit
TSBackgroundInit --> TSBConnectionInfo
TSBConnectionInfo --> GatherLocal
}
01\_BIOS\_Configuration:::group : 01 BIOS Configuration
Status\_01\_BIOS\_Settings:::step
Module\_KOW\_BIOS\_Configuration:::subTaskSequence
state "01 BIOS Configuration" as 01\_BIOS\_Configuration {
Status\_01\_BIOS\_Settings --> Module\_KOW\_BIOS\_Configuration
}
state "Module | KOW | BIOS Configuration" as Module\_KOW\_BIOS\_Configuration {
direction LR
Init
}
02\_OS\_Installation:::group
Status\_02\_OS\_Installation:::step
Module\_KOW\_OS\_Installation:::subTaskSequence
state "02 OS Installation" as 02\_OS\_Installation {
Status\_02\_OS\_Installation --> Module\_KOW\_OS\_Installation
}
state "Module | KOW | OS Installation" as Module\_KOW\_OS\_Installation {
direction LR
Init2
}
04\_Software\_Install:::group
Status\_04\_Software\_Install:::step
Module\_KOW\_Software\_Installation:::subTaskSequence
state "04 Software Install" as 04\_Software\_Install {
Status\_04\_Software\_Install --> Module\_KOW\_Software\_Installation
}
state "Module | KOW | Software Installation" as Module\_KOW\_Software\_Installation {
direction LR
Init4
}
05\_Install\_Updates:::group
Status\_05\_Install\_Updates:::step
Install\_Updates:::step
Restart\_Computer:::step
state "05 Install Updates" as 05\_Install\_Updates {
direction LR
Status\_05\_Install\_Updates --> Install\_Updates
Install\_Updates --> Restart\_Computer
}
06\_Finalizing\_OS:::group
Status\_06\_Finalizing\_OS:::step
Module\_KOW\_Windows\_Customization:::subTaskSequence
state "06 Finalizing OS" as 06\_Finalizing\_OS {
Status\_06\_Finalizing\_OS --> Module\_KOW\_Windows\_Customization
}
state "Module | KOW | Windows Customization" as Module\_KOW\_Windows\_Customization {
Init6
}
classDef start stroke-width:0px, fill:#ff8000, stroke:#f1c40f, font-size:18px, , font-weight:bold, box-shadow: 1px
classDef group stroke-width:2px, color:#ffffff, fill:transparent, stroke:#ffff00, font-size:16px, font-weight:bold
classDef step stroke-width:0px, color:#000000, fill:#00ff00, font-size:12px
classDef subTaskSequence stroke-width:2px, color:#000000, fill:#ff80ff, font-size:16px, font-weight:bold
classDef tsvariable stroke-width:1px, color:#000, fill:#80ffff, font-size:12px
```
<!--MCONTENT {content: "%%{init: {'maxTextSize': 99000, 'stateDiagram-v2' : { 'curve' : 'catmullRom' } } }%%<br/>\nstateDiagram-v2<br/>\n\\[\\*\\] \\-\\-\\> Start\\_Task\\_Sequence:::start<br/>\n%%Start\\_Task\\_Sequence:::start<br/>\nMain:::group<br/>\nInitialization:::group<br/>\nStart\\_Task\\_Sequence\\-\\-\\>Main<br/>\nMain\\-\\-\\>Initialization<br/>\nSet\\_TS\\_Start\\_Time:::step<br/>\nTSBackgroundInit:::step<br/>\nTSBConnectionInfo:::step<br/>\nGatherLocal:::step<br/>\nTSBIOSConfiguration:::tsvariable<br/>\nTSInstallSoftware:::tsvariable<br/>\nTSInstallUpdates:::tsvariable<br/>\nstate Main {<br/>\nstate TSBIOSConfiguration <<choice>><br/>\nInitialization \\-\\-\\> TSBIOSConfiguration<br/>\nTSBIOSConfiguration \\-\\-\\> 01\\_BIOS\\_Configuration : True<br/>\nTSBIOSConfiguration \\-\\-\\> 02\\_OS\\_Installation : False<br/>\n01\\_BIOS\\_Configuration \\-\\-\\> 02\\_OS\\_Installation<br/>\nstate TSInstallSoftware <<choice>><br/>\n02\\_OS\\_Installation \\-\\-\\> TSInstallSoftware<br/>\nTSInstallSoftware \\-\\-\\> 04\\_Software\\_Install : True<br/>\nTSInstallSoftware \\-\\-\\> TSInstallUpdates : False<br/>\n%%04\\_Software\\_Install \\-\\-\\> 05\\_Install\\_Updates<br/>\nstate TSInstallUpdates <<choice>><br/>\n04\\_Software\\_Install \\-\\-\\> TSInstallUpdates<br/>\nTSInstallUpdates \\-\\-\\> 05\\_Install\\_Updates : True<br/>\nTSInstallUpdates \\-\\-\\> 06\\_Finalizing\\_OS : False<br/>\n05\\_Install\\_Updates \\-\\-\\> 06\\_Finalizing\\_OS<br/>\n}<br/>\nstate Initialization {<br/>\ndirection LR<br/>\nSet\\_TS\\_Start\\_Time \\-\\-\\> TSBackgroundInit<br/>\nTSBackgroundInit \\-\\-\\> TSBConnectionInfo<br/>\nTSBConnectionInfo \\-\\-\\> GatherLocal<br/>\n}<br/>\n01\\_BIOS\\_Configuration:::group : 01 BIOS Configuration<br/>\nStatus\\_01\\_BIOS\\_Settings:::step<br/>\nModule\\_KOW\\_BIOS\\_Configuration:::subTaskSequence<br/>\nstate \"01 BIOS Configuration\" as 01\\_BIOS\\_Configuration {<br/>\nStatus\\_01\\_BIOS\\_Settings \\-\\-\\> Module\\_KOW\\_BIOS\\_Configuration<br/>\n}<br/>\nstate \"Module | KOW | BIOS Configuration\" as Module\\_KOW\\_BIOS\\_Configuration {<br/>\ndirection LR<br/>\nInit<br/>\n}<br/>\n02\\_OS\\_Installation:::group<br/>\nStatus\\_02\\_OS\\_Installation:::step<br/>\nModule\\_KOW\\_OS\\_Installation:::subTaskSequence<br/>\nstate \"02 OS Installation\" as 02\\_OS\\_Installation {<br/>\nStatus\\_02\\_OS\\_Installation \\-\\-\\> Module\\_KOW\\_OS\\_Installation<br/>\n}<br/>\nstate \"Module | KOW | OS Installation\" as Module\\_KOW\\_OS\\_Installation {<br/>\ndirection LR<br/>\nInit2<br/>\n}<br/>\n04\\_Software\\_Install:::group<br/>\nStatus\\_04\\_Software\\_Install:::step<br/>\nModule\\_KOW\\_Software\\_Installation:::subTaskSequence<br/>\nstate \"04 Software Install\" as 04\\_Software\\_Install {<br/>\nStatus\\_04\\_Software\\_Install \\-\\-\\> Module\\_KOW\\_Software\\_Installation<br/>\n}<br/>\nstate \"Module | KOW | Software Installation\" as Module\\_KOW\\_Software\\_Installation {<br/>\ndirection LR<br/>\nInit4<br/>\n}<br/>\n05\\_Install\\_Updates:::group<br/>\nStatus\\_05\\_Install\\_Updates:::step<br/>\nInstall\\_Updates:::step<br/>\nRestart\\_Computer:::step<br/>\nstate \"05 Install Updates\" as 05\\_Install\\_Updates {<br/>\ndirection LR<br/>\nStatus\\_05\\_Install\\_Updates \\-\\-\\> Install\\_Updates<br/>\nInstall\\_Updates \\-\\-\\> Restart\\_Computer<br/>\n}<br/>\n06\\_Finalizing\\_OS:::group<br/>\nStatus\\_06\\_Finalizing\\_OS:::step<br/>\nModule\\_KOW\\_Windows\\_Customization:::subTaskSequence<br/>\nstate \"06 Finalizing OS\" as 06\\_Finalizing\\_OS {<br/>\nStatus\\_06\\_Finalizing\\_OS \\-\\-\\> Module\\_KOW\\_Windows\\_Customization<br/>\n}<br/>\nstate \"Module | KOW | Windows Customization\" as Module\\_KOW\\_Windows\\_Customization {<br/>\nInit6<br/>\n}<br/>\nclassDef start stroke-width:0px, fill:#ff8000, stroke:#f1c40f, font-size:18px, , font-weight:bold, box-shadow: 1px<br/>\nclassDef group stroke-width:2px, color:#ffffff, fill:transparent, stroke:#ffff00, font-size:16px, font-weight:bold<br/>\nclassDef step stroke-width:0px, color:#000000, fill:#00ff00, font-size:12px<br/>\nclassDef subTaskSequence stroke-width:2px, color:#000000, fill:#ff80ff, font-size:16px, font-weight:bold<br/>\nclassDef tsvariable stroke-width:1px, color:#000, fill:#80ffff, font-size:12px<br/>"} --->

<br/>

# Task Sequence Steps

1.  Initialization

2.  BIOS Configuration

3.  OS Installation

4.  Software Installation

5.  Update Installation

6.  Finalizing OS
<br/>

<br/>

This file was generated by Swimm. [Click here to view it in the app](https://app.swimm.io/repos/Z2l0aHViJTNBJTNBRG9rdW1lbnRhdGlvbiUzQSUzQVRoYW1pZWxpcw==/docs/kmxycjxb).
