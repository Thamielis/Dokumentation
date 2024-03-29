---
id: 2iysfj9y
title: TS Windows 10 v22
file_version: 1.1.2
app_version: 1.10.3
---

# TaskSequenz v23

```
%%{init: {'maxTextSize': 99000, 'stateDiagram-v2' : { 'curve' : 'catmullRom' } } }%%

stateDiagram-v2
    [*] --> Start_Task_Sequence:::start
    %%Start_Task_Sequence:::start
    Main:::group
    Initialization:::group

    Start_Task_Sequence-->Main
    Main-->Initialization

    Set_TS_Start_Time:::step
    TSBackgroundInit:::step
    TSBConnectionInfo:::step
    GatherLocal:::step
    
    TSBIOSConfiguration:::tsvariable
    TSInstallSoftware:::tsvariable
    TSInstallUpdates:::tsvariable

    state Main {
        state TSBIOSConfiguration <<choice>>
        Initialization --> TSBIOSConfiguration
        TSBIOSConfiguration --> 01_BIOS_Configuration : True
        TSBIOSConfiguration --> 02_OS_Installation : False
        01_BIOS_Configuration --> 02_OS_Installation

        state TSInstallSoftware <<choice>>
        02_OS_Installation --> TSInstallSoftware
        TSInstallSoftware --> 04_Software_Install : True
        TSInstallSoftware --> TSInstallUpdates : False
        %%04_Software_Install --> 05_Install_Updates

        state TSInstallUpdates <<choice>>
        04_Software_Install --> TSInstallUpdates
        TSInstallUpdates --> 05_Install_Updates : True
        TSInstallUpdates --> 06_Finalizing_OS : False
        05_Install_Updates --> 06_Finalizing_OS

    }

    state Initialization {
        Set_TS_Start_Time --> TSBackgroundInit
        TSBackgroundInit --> TSBConnectionInfo
        TSBConnectionInfo --> GatherLocal
    }

    01_BIOS_Configuration:::group : 01 BIOS Configuration
    Status_01_BIOS_Settings:::step
    Module_KOW_BIOS_Configuration:::subTaskSequence

    state "01 BIOS Configuration" as 01_BIOS_Configuration {
        Status_01_BIOS_Settings --> Module_KOW_BIOS_Configuration
    }

    state "Module | KOW | BIOS Configuration" as Module_KOW_BIOS_Configuration {
        direction LR
        Init
    }

    02_OS_Installation:::group
    Status_02_OS_Installation:::step
    Module_KOW_OS_Installation:::subTaskSequence

    state "02 OS Installation" as 02_OS_Installation {
        Status_02_OS_Installation --> Module_KOW_OS_Installation
    }

    state "Module | KOW | OS Installation" as Module_KOW_OS_Installation {
        direction LR
        Init2
    }

    
    04_Software_Install:::group
    Status_04_Software_Install:::step
    Module_KOW_Software_Installation:::subTaskSequence

    state "04 Software Install" as 04_Software_Install {
        Status_04_Software_Install --> Module_KOW_Software_Installation
    }

    state "Module | KOW | Software Installation" as Module_KOW_Software_Installation {
        direction LR
        Init4
    }


    05_Install_Updates:::group
    Status_05_Install_Updates:::step
    Install_Updates:::step
    Restart_Computer:::step

    state "05 Install Updates" as 05_Install_Updates {
        direction LR
        Status_05_Install_Updates --> Install_Updates
        Install_Updates --> Restart_Computer
    }

    06_Finalizing_OS:::group
    Status_06_Finalizing_OS:::step
    Module_KOW_Windows_Customization:::subTaskSequence

    state "06 Finalizing OS" as 06_Finalizing_OS {
        Status_06_Finalizing_OS --> Module_KOW_Windows_Customization
    }

    state "Module | KOW | Windows Customization" as Module_KOW_Windows_Customization {
        Init6
    }



    classDef start stroke-width:0px, fill:#ff8000, stroke:#f1c40f, font-size:18px, , font-weight:bold, box-shadow: 1px
    classDef group stroke-width:2px, color:#ffffff, fill:transparent, stroke:#ffff00, font-size:16px, font-weight:bold
    classDef step stroke-width:0px, color:#000000, fill:#00ff00, font-size:12px
    classDef subTaskSequence stroke-width:2px, color:#000000, fill:#ff80ff, font-size:16px, font-weight:bold

    classDef tsvariable stroke-width:1px, color:#000, fill:#80ffff, font-size:12px
```

<br/>

This file was generated by Swimm. [Click here to view it in the app](https://app.swimm.io/repos/Z2l0aHViJTNBJTNBRG9rdW1lbnRhdGlvbiUzQSUzQVRoYW1pZWxpcw==/docs/2iysfj9y).
