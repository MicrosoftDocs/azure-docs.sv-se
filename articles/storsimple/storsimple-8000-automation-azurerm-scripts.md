---
title: Använda AzureRM PowerShell-skript för att hantera StorSimple-enheter
description: Lär dig hur du använder Azure Resource Manager SDK-baserade skript för att hantera en enhet med StorSimple 8000-serien.
author: alkohli
ms.service: storsimple
ms.topic: how-to
ms.date: 10/03/2017
ms.author: alkohli
ms.openlocfilehash: e3339f883d177366a64187e0c090752527802c8d
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "94968782"
---
# <a name="use-azure-resource-manager-sdk-based-scripts-to-manage-storsimple-devices"></a>Använd Azure Resource Manager SDK-baserade skript för att hantera StorSimple-enheter

Den här artikeln beskriver hur Azure Resource Manager SDK-baserade skript kan användas för att hantera din enhet för StorSimple 8000-serien. Ett exempel skript ingår också för att vägleda dig genom stegen för att konfigurera din miljö för att köra skripten.

Den här artikeln gäller endast StorSimple 8000-serien het som körs i Azure Portal.

## <a name="sample-scripts"></a>Exempelskript

Följande exempel skript är tillgängliga för att automatisera olika StorSimple-jobb.

#### <a name="table-of-azure-resource-manager-sdk-based-sample-scripts"></a>Tabell med Azure Resource Manager SDK-baserade exempel skript

| Azure Resource Manager skript                    | Beskrivning                                                                                                                                                                                                       |
|--------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| [Authorize-ServiceEncryptionRollover.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Authorize-ServiceEncryptionRollover.ps1)          | Med det här skriptet kan du auktorisera din StorSimple-enhet för att ändra krypterings nyckeln för tjänst data.                                                                                                           |
| [Create-StorSimpleCloudAppliance.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Create-StorSimpleCloudAppliance.ps1)              | Det här skriptet skapar en 8010-eller 8020-StorSimple Cloud Appliance. Moln installationen kan sedan konfigureras och registreras med din StorSimple Data Manager-tjänst.                                                       |
| [CreateOrUpdate-Volume.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/CreateOrUpdate-Volume.ps1)                        | Det här skriptet skapar eller ändrar StorSimple-volymer.                                                                                                                                                             |
| [Get-DeviceBackup.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Get-DeviceBackup.ps1)                             | Det här skriptet visar en lista över alla säkerhets kopior för en enhet som är registrerad i StorSimple Enhetshanteraren-tjänsten.                                                                                                          |
| [Get-DeviceBackupPolicy.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Get-DeviceBackupPolicy.ps1)                       | Detta skript alla säkerhets kopierings principer för din StorSimple-enhet.                                                                                                                                                 |
| [Get-DeviceJobs.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Get-DeviceJobs.ps1)                               | Det här skriptet hämtar alla StorSimple-jobb som körs på din StorSimple Enhetshanteraren-tjänst.                                                                                                                     |
| [Get-DeviceUpdateAvailability.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Get-DeviceUpdateAvailability.ps1)                 | Det här skriptet genomsöker uppdaterings servern och låter dig veta om det finns uppdateringar som är tillgängliga för installation på din StorSimple-enhet.                                                                                          |
| [Install-DeviceUpdate.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Install-DeviceUpdate.ps1)                         | Det här skriptet installerar de tillgängliga uppdateringarna på din StorSimple-enhet.                                                                                                                                           |
| [Manage-CloudSnapshots.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Manage-CloudSnapshots.ps1)                        | Det här skriptet startar en manuell ögonblicks bild av molnet och tar bort moln ögonblicks bilder som är äldre än angivna bevarande dagar.                                                                                                   |
| [Monitor-Backups.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Monitor-Backups.ps1)                              | Detta Azure Automation Runbook PowerShell-skriptet rapporterar status för alla säkerhets kopierings jobb.                                                                                                              |
| [Remove-DeviceBackup.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Remove-DeviceBackup.ps1)                          | Det här skriptet tar bort ett enda säkerhets kopierings objekt.                                                                                                                                                           |
| [Start-DeviceBackupJob.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Start-DeviceBackupJob.ps1)                        | Det här skriptet startar en manuell säkerhets kopiering på din StorSimple-enhet.                                                                                                                                       |
| [Update-CloudApplianceServiceEncryptionKey.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Update-CloudApplianceServiceEncryptionKey.ps1)    | Det här skriptet uppdaterar krypterings nyckeln för tjänst data för alla moln enheter för 8010/8020-StorSimple som registrerats med din StorSimple Enhetshanteraren-tjänst.                                     |
| [Verify-BackupScheduleAndBackup.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Verify-BackupScheduleAndBackup.ps1)               | Det här skriptet visar de säkerhets kopior som saknas efter analys av alla scheman som är associerade med säkerhets kopierings principer. Den verifierar också säkerhets kopierings katalogen med listan över tillgängliga säkerhets kopior.             |




## <a name="configure-and-run-a-sample-script"></a>Konfigurera och köra ett exempel skript

Det här avsnittet tar ett exempel skript och innehåller information om de olika stegen som krävs för att köra skriptet.

### <a name="prerequisites"></a>Förutsättningar

Innan du börjar ska du kontrol lera att du har:

*   Azure PowerShell installerat. Så här installerar du Azure PowerShell-moduler:
    * I en Windows-miljö följer du stegen i [Installera och konfigurera Azure PowerShell](/powershell/azure/install-az-ps). Du kan installera Azure PowerShell på Windows Server-värden för din StorSimple om du använder en sådan.
    * I en Linux-eller MacOS-miljö följer du stegen i [Installera och konfigurera Azure PowerShell på MacOS eller Linux](/powershell/azure/install-az-ps).

Om du vill ha mer information om hur du använder Azure PowerShell går du till [komma igång med att använda Azure PowerShell](/powershell/azure/get-started-azureps).

### <a name="run-azure-powershell-script"></a>Kör Azure PowerShell skript

Skriptet som används i det här exemplet visar en lista över alla jobb på en StorSimple-enhet. Detta inkluderar de jobb som lyckades, misslyckades eller pågår. Utför följande steg för att ladda ned och köra skriptet.

1. Starta Azure PowerShell. Skapa en ny mapp och ändra katalogen till den nya mappen.

    ```
        mkdir C:\scripts\StorSimpleSDKTools
        cd C:\scripts\StorSimpleSDKTools
    ```    
2. [Hämta NUGET CLI](https://www.nuget.org/downloads) under den mapp som skapades i föregående steg. Det finns olika versioner av _nuget.exe_. Välj den version som motsvarar din SDK. Varje nedladdnings länk pekar direkt till en _. exe_ -fil. Tänk på att högerklicka och spara filen på datorn i stället för att köra den från webbläsaren.

    Du kan också köra följande kommando för att ladda ned och lagra skriptet i samma mapp som du skapade tidigare.
    
    ```
        wget https://dist.nuget.org/win-x86-commandline/latest/nuget.exe -Out C:\scripts\StorSimpleSDKTools\nuget.exe
    ```
3. Hämta det beroende SDK: n.

    ```
        C:\scripts\StorSimpleSDKTools\nuget.exe install Microsoft.Azure.Management.Storsimple8000series
        C:\scripts\StorSimpleSDKTools\nuget.exe install Microsoft.IdentityModel.Clients.ActiveDirectory -Version 2.28.3
        C:\scripts\StorSimpleSDKTools\nuget.exe install Microsoft.Rest.ClientRuntime.Azure.Authentication -Version 2.2.9-preview
    ```    
4. Hämta skriptet från exemplet GitHub Project.

    ```
        wget https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Get-DeviceJobs.ps1 -Out Get-DeviceJobs.ps1

    ```

5. Kör skriptet. Ange dina autentiseringsuppgifter för Azure när du uppmanas att autentisera. Det här skriptet ska generera en filtrerad lista över alla jobb på din StorSimple-enhet.
           
    ```           
        .\Get-StorSimpleJob.ps1 -SubscriptionId [subid] -TenantId [tenant id] -DeviceName [name of device] -ResourceGroupName [name of resource group] -ManagerName[name of device manager] -FilterByStatus [Filter for job status] -FilterByJobType [Filter for job type] -FilterByStartTime [Filter for start date time] -FilterByEndTime [Filter for end date time]

    ```

### <a name="sample-output"></a>Exempelutdata

Följande utdata visas när exempel skriptet körs. Utdata innehåller alla jobb som kördes på en registrerad enhet som startade den 25 september 2017 och slutfördes den 2 oktober 2017.

```
-----------------------------------------
Windows PowerShell
Copyright (C) 2016 Microsoft Corporation. All rights reserved.

PS C:\Scripts\StorSimpleSDKTools> wget https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Get-DeviceJobs.ps1 -Out Get-DeviceJobs.ps1
PS C:\Scripts\StorSimpleSDKTools> .\Get-DeviceJobs.ps1 -SubscriptionId 1234ab5c-678d-910e-9fc4-0accc9c0166e -TenantId 12a345bc-67d8-91ef-01ab-2c7cd123ef45 -DeviceName 8600-ABC1234567D89EF -ResourceGroupName Contoso -ManagerName ContosoDeviceMgr -FilterByStartTime "09/25/2017 08:10:02" -FilterByEndTime "10/02/2017 08:10:02"


Status            : Succeeded
StartTime         : 10/2/2017 10:30:02 PM
EndTime           : 10/2/2017 10:31:36 PM
PercentComplete   : 100
Error             :
JobType           : ScheduledBackup
DataStats         : Microsoft.Azure.Management.StorSimple8000Series.Models.DataStatistics
EntityLabel       : ss-asr-policy1
EntityType        : Microsoft.StorSimple/managers/devices/backupPolicies
JobStages         :
DeviceId          : /subscriptions/1234ab5c-678d-910e-9fc4-0accc9c0166e/resourceGroups/Contoso/providers/Microsoft.Stor
                    Simple/managers/ContosoDeviceMgr/devices/8600-SHG0997877L71FC
IsCancellable     : True
BackupType        : CloudSnapshot
SourceDeviceId    :
BackupPointInTime : 1/1/0001 12:00:00 AM
Id                : /subscriptions/1234ab5c-678d-910e-9fc4-0accc9c0166e/resourceGroups/Contoso/providers/Microsoft.Stor
                    Simple/managers/ContosoDeviceMgr/devices/8600-SHG0997877L71FC/jobs/75905c48-b153-4af1-8b21-4b9a2ff9
                    825b
Name              : 75905c48-b153-4af1-8b21-4b9a2ff9825b
Type              : Microsoft.StorSimple/managers/devices/jobs
Kind              : Series8000
-------------------------------------------
CUT              CUT  
-------------------------------------------
Status            : Succeeded
StartTime         : 9/26/2017 5:00:02 PM
EndTime           : 9/26/2017 5:01:20 PM
PercentComplete   : 100
Error             :
JobType           : ScheduledBackup
DataStats         : Microsoft.Azure.Management.StorSimple8000Series.Models.DataStatistics
EntityLabel       : 8010 policy
EntityType        : Microsoft.StorSimple/managers/devices/backupPolicies
JobStages         :
DeviceId          : /subscriptions/1234ab5c-678d-910e-9fc4-0accc9c0166e/resourceGroups/Contoso/providers/Microsoft.Stor
                    Simple/managers/ContosoDeviceMgr/devices/8600-ABC1234567D89EF
IsCancellable     : True
BackupType        : CloudSnapshot
SourceDeviceId    :
BackupPointInTime : 1/1/0001 12:00:00 AM
Id                : /subscriptions/1234ab5c-678d-910e-9fc4-0accc9c0166e/resourceGroups/Contoso/providers/Microsoft.Stor
                    Simple/managers/ContosoDeviceMgr/devices/8600-ABC1234567D89EF/jobs/3cfd8108-db60-4e9a-a8da-6d8fe457
                    8d2b
Name              : 3cfd8108-db60-4e9a-a8da-6d8fe4578d2b
Type              : Microsoft.StorSimple/managers/devices/jobs
Kind              : Series8000



PS C:\Scripts\StorSimpleSDKTools>
--------------------------------------------

```


## <a name="next-steps"></a>Nästa steg

[Använd StorSimple Enhetshanteraren-tjänsten för att hantera din StorSimple-enhet](storsimple-8000-manager-service-administration.md).