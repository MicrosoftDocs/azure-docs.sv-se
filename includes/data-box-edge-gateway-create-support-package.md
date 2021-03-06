---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 03/05/2019
ms.author: alkohli
ms.openlocfilehash: b657ee32e76dd90671f7e91337ced01b925889a1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "67187802"
---
Om du får problem med enheten kan du skapa ett support paket från system loggarna. Microsoft Support använder det här paketet för att felsöka problemen. Följ de här stegen för att skapa ett support paket:

1. [Anslut till PowerShell-gränssnittet på enheten](#connect-to-the-powershell-interface).
2. Använd `Get-HcsNodeSupportPackage` kommandot för att skapa ett support paket. Användningen av cmdleten är följande:

    ```powershell
    Get-HcsNodeSupportPackage [-Path] <string> [-Zip] [-ZipFileName <string>] [-Include {None | RegistryKeys | EtwLogs
            | PeriodicEtwLogs | LogFiles | DumpLog | Platform | FullDumps | MiniDumps | ClusterManagementLog | ClusterLog |
            UpdateLogs | CbsLogs | StorageCmdlets | ClusterCmdlets | ConfigurationCmdlets | KernelDump | RollbackLogs |
            Symbols | NetworkCmdlets | NetworkCmds | Fltmc | ClusterStorageLogs | UTElement | UTFlag | SmbWmiProvider |
            TimeCmds | LocalUILogs | ClusterHealthLogs | BcdeditCommand | BitLockerCommand | DirStats | ComputeRolesLogs |
            ComputeCmdlets | DeviceGuard | Manifests | MeasuredBootLogs | Stats | PeriodicStatLogs | MigrationLogs |
            RollbackSupportPackage | ArchivedLogs | Default}] [-MinimumTimestamp <datetime>] [-MaximumTimestamp <datetime>]
            [-IncludeArchived] [-IncludePeriodicStats] [-Credential <pscredential>]  [<CommonParameters>]
    ```

    Cmdlet: en samlar in loggar från enheten och kopierar dessa loggar till ett angivet nätverk eller en lokal resurs.

    De parametrar som används är följande:

    - `-Path` -Ange nätverket eller den lokala sökvägen för att kopiera support paketet till. kunna
    - `-Credential` – Ange autentiseringsuppgifterna för att få åtkomst till den skyddade sökvägen.
    - `-Zip` -Ange om du vill generera en zip-fil.
    - `-Include` -Ange om du vill inkludera de komponenter som ska ingå i support paketet. Om inget anges `Default` antas detta.
    - `-IncludeArchived` -Ange om du vill inkludera arkiverade loggar i support paketet.
    - `-IncludePeriodicStats` -Ange om du vill inkludera periodiska stat loggar i support paketet.

    
