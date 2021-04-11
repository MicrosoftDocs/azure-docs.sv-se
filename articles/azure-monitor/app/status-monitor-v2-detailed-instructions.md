---
title: Detaljerade instruktioner för Azure Application Insights-agent | Microsoft Docs
description: Detaljerade anvisningar för att komma igång med Application Insights-agent. Övervaka webbplatsens prestanda utan att omdistribuera webbplatsen. Fungerar med ASP.NET-webbappar som finns lokalt, i virtuella datorer eller på Azure.
ms.topic: conceptual
author: TimothyMothra
ms.author: tilee
ms.date: 04/23/2019
ms.openlocfilehash: 29922f088a51e4876e5e2ec8fe87c3bbce4482f3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "102521687"
---
# <a name="application-insights-agent-formerly-named-status-monitor-v2-detailed-instructions"></a>Application Insights agent (tidigare namngiven Statusövervakare v2): detaljerade anvisningar

Den här artikeln beskriver hur du integrerar till PowerShell-galleriet och laddar ned ApplicationMonitor-modulen.
Här ingår de vanligaste parametrarna som du måste komma igång med.
Vi har också tillhandahållit manuella hämtnings anvisningar om du inte har till gång till Internet.

## <a name="get-an-instrumentation-key"></a>Hämta en Instrumentation-nyckel

Du behöver en Instrumentation-nyckel för att komma igång. Mer information finns i [skapa en Application Insights resurs](create-new-resource.md#copy-the-instrumentation-key).

## <a name="run-powershell-as-admin-with-an-elevated-execution-policy"></a>Kör PowerShell som administratör med en förhöjd körnings princip

### <a name="run-as-admin"></a>Kör som administratör

PowerShell behöver behörigheter på administratörs nivå för att göra ändringar i datorn.
### <a name="execution-policy"></a>Körnings princip
- Beskrivning: som standard är körning av PowerShell-skript inaktiverat. Vi rekommenderar att du endast tillåter RemoteSigned-skript för det aktuella omfånget.
- Referens: [om körnings principer](/powershell/module/microsoft.powershell.core/about/about_execution_policies) och [set-ExecutionPolicy](/powershell/module/microsoft.powershell.security/set-executionpolicy).
- Kommando: `Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope Process` .
- Valfri parameter:
    - `-Force`. Åsidosätter bekräftelse meddelandet.

**Exempel fel**

```output
Install-Module : The 'Install-Module' command was found in the module 'PowerShellGet', but the module could not be
loaded. For more information, run 'Import-Module PowerShellGet'.

Import-Module : File C:\Program Files\WindowsPowerShell\Modules\PackageManagement\1.3.1\PackageManagement.psm1 cannot
be loaded because running scripts is disabled on this system. For more information, see about_Execution_Policies at
https:/go.microsoft.com/fwlink/?LinkID=135170.
```


## <a name="prerequisites-for-powershell"></a>Krav för PowerShell

Granska din instans av PowerShell genom att köra `$PSVersionTable` kommandot.
Det här kommandot ger följande utdata:

```output
Name                           Value
----                           -----
PSVersion                      5.1.17763.316
PSEdition                      Desktop
PSCompatibleVersions           {1.0, 2.0, 3.0, 4.0...}
BuildVersion                   10.0.17763.316
CLRVersion                     4.0.30319.42000
WSManStackVersion              3.0
PSRemotingProtocolVersion      2.3
SerializationVersion           1.1.0.1
```

Dessa instruktioner har skrivits och testats på en dator som kör Windows 10 och de versioner som anges ovan.

## <a name="prerequisites-for-powershell-gallery"></a>Krav för PowerShell-galleriet

De här stegen förbereder servern för att ladda ned moduler från PowerShell-galleriet.

> [!NOTE] 
> PowerShell-galleriet stöds i Windows 10, Windows Server 2016 och PowerShell 6 +.
> Information om tidigare versioner finns i avsnittet om att [Installera PowerShellGet](/powershell/scripting/gallery/installing-psget).


1. Kör PowerShell som administratör med en förhöjd körnings princip.
2. Installera NuGet-paket leverantören.
    - Beskrivning: du behöver den här providern för att interagera med NuGet-baserade databaser som PowerShell-galleriet.
    - Referens: [install-PackageProvider](/powershell/module/packagemanagement/install-packageprovider).
    - Kommando: `Install-PackageProvider -Name NuGet -MinimumVersion 2.8.5.201` .
    - Valfria parametrar:
        - `-Proxy`. Anger en proxyserver för begäran.
        - `-Force`. Åsidosätter bekräftelse meddelandet.
    
    Du får den här frågan om NuGet inte har kon figurer ATS:

    ```output
    NuGet provider is required to continue
    PowerShellGet requires NuGet provider version '2.8.5.201' or newer to interact with NuGet-based repositories. 
    The NuGet provider must be available in 'C:\Program Files\PackageManagement\ProviderAssemblies' or
    'C:\Users\t\AppData\Local\PackageManagement\ProviderAssemblies'. You can also install the NuGet provider by running
    'Install-PackageProvider -Name NuGet -MinimumVersion 2.8.5.201 -Force'. Do you want PowerShellGet to install and import
    the NuGet provider now?
    [Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"):
    ```    

3. Konfigurera PowerShell-galleriet som ett betrott lager.
    - Beskrivning: som standard är PowerShell-galleriet ett ej betrott lager.
    - Referens: [set-PSRepository](/powershell/module/powershellget/set-psrepository).
    - Kommando: `Set-PSRepository -Name "PSGallery" -InstallationPolicy Trusted` .
    - Valfri parameter:
        - `-Proxy`. Anger en proxyserver för begäran.

    Du får den här frågan om PowerShell-galleriet inte är betrott:

    ```output
    Untrusted repository
    You are installing the modules from an untrusted repository. 
    If you trust this repository, change its InstallationPolicy value 
    by running the Set-PSRepository cmdlet. Are you sure you want to 
    install the modules from 'PSGallery'?
    [Y] Yes  [A] Yes to All  [N] No  [L] No to All  [S] Suspend  [?] Help (default is "N"):
    ```

    Du kan bekräfta ändringen och granska alla PSRepositories genom att köra `Get-PSRepository` kommandot.

4. Installera den senaste versionen av PowerShellGet.
    - Beskrivning: den här modulen innehåller verktyg som används för att hämta andra moduler från PowerShell-galleriet. Version 1.0.0.1 levereras med Windows 10 och Windows Server. Version 1.6.0 eller högre krävs. Du kan ta reda på vilken version som är installerad genom att köra `Get-Command -Module PowerShellGet` kommandot.
    - Referens: [installerar PowerShellGet](/powershell/scripting/gallery/installing-psget).
    - Kommando: `Install-Module -Name PowerShellGet` .
    - Valfria parametrar:
        - `-Proxy`. Anger en proxyserver för begäran.
        - `-Force`. Hoppar över varningen "redan installerat" och installerar den senaste versionen.

    Du får det här felet om du inte använder den senaste versionen av PowerShellGet:

    ```output
    Install-Module : A parameter cannot be found that matches parameter name 'AllowPrerelease'.
    At line:1 char:20
    Install-Module abc -AllowPrerelease
                   ~~~~~~~~~~~~~~~~
    CategoryInfo          : InvalidArgument: (:) [Install-Module], ParameterBindingException
    FullyQualifiedErrorId : NamedParameterNotFound,Install-Module
    ```

5. Starta om PowerShell. Du kan inte läsa in den nya versionen i den aktuella sessionen. Nya PowerShell-sessioner kommer att läsa in den senaste versionen av PowerShellGet.

## <a name="download-and-install-the-module-via-powershell-gallery"></a>Hämta och installera modulen via PowerShell-galleriet

De här stegen hämtar modulen AZ. ApplicationMonitor från PowerShell-galleriet.

1. Se till att alla nödvändiga komponenter för PowerShell-galleriet är uppfyllda.
2. Kör PowerShell som administratör med en förhöjd körnings princip.
3. Installera modulen AZ. ApplicationMonitor.
    - Referens: [installera-modul](/powershell/module/powershellget/install-module).
    - Kommando: `Install-Module -Name Az.ApplicationMonitor` .
    - Valfria parametrar:
        - `-Proxy`. Anger en proxyserver för begäran.
        - `-AllowPrerelease`. Tillåter installation av alpha-och beta versioner.
        - `-AcceptLicense`. Hoppar över "acceptera licens"-prompten
        - `-Force`. Hoppar över varningen "ej betrodda databaser".

## <a name="download-and-install-the-module-manually-offline-option"></a>Ladda ned och installera modulen manuellt (alternativet offline)

Om du av någon anledning inte kan ansluta till PowerShell-modulen kan du hämta och installera modulen AZ. ApplicationMonitor manuellt.

### <a name="manually-download-the-latest-nupkg-file"></a>Hämta den senaste nupkg-filen manuellt

1. Gå till https://www.powershellgallery.com/packages/Az.ApplicationMonitor.
2. Välj den senaste versionen av filen i tabellen **versions historik** .
3. Under **installations alternativ** väljer du **manuell nedladdning**.

### <a name="option-1-install-into-a-powershell-modules-directory"></a>Alternativ 1: installera i en katalog med PowerShell-moduler
Installera den manuellt hämtade PowerShell-modulen i en PowerShell-katalog så att den kan identifieras av PowerShell-sessioner.
Mer information finns i [installera en PowerShell-modul](/powershell/scripting/developer/module/installing-a-powershell-module).


#### <a name="unzip-nupkg-as-a-zip-file-by-using-expand-archive-v1010"></a>Zippa upp nupkg som en zip-fil med hjälp av Expand-Archive (v 1.0.1.0)

- Beskrivning: bas versionen av Microsoft. PowerShell. Archive (v 1.0.1.0) kan inte zippa upp nupkg-filer. Byt namn på filen med fil namns tillägget. zip.
- Referens: [Expand-Archive](/powershell/module/microsoft.powershell.archive/expand-archive).
- Kommandoprompt

    ```console
    $pathToNupkg = "C:\az.applicationmonitor.0.3.0-alpha.nupkg"
    $pathToZip = ([io.path]::ChangeExtension($pathToNupkg, "zip"))
    $pathToNupkg | rename-item -newname $pathToZip
    $pathInstalledModule = "$Env:ProgramFiles\WindowsPowerShell\Modules\az.applicationmonitor"
    Expand-Archive -LiteralPath $pathToZip -DestinationPath $pathInstalledModule
    ```

#### <a name="unzip-nupkg-by-using-expand-archive-v1100"></a>Zippa upp nupkg med hjälp av Expand-Archive (v 1.1.0.0)

- Beskrivning: Använd en aktuell version av Expand-Archive för att zippa upp nupkg-filer utan att ändra tillägget.
- Referens: [Expand-Archive](/powershell/module/microsoft.powershell.archive/expand-archive) och [Microsoft. PowerShell. Archive](https://www.powershellgallery.com/packages/Microsoft.PowerShell.Archive/1.1.0.0).
- Kommandoprompt

    ```console
    $pathToNupkg = "C:\az.applicationmonitor.0.2.1-alpha.nupkg"
    $pathInstalledModule = "$Env:ProgramFiles\WindowsPowerShell\Modules\az.applicationmonitor"
    Expand-Archive -LiteralPath $pathToNupkg -DestinationPath $pathInstalledModule
    ```

### <a name="option-2-unzip-and-import-nupkg-manually"></a>Alternativ 2: packa upp och importera nupkg manuellt
Installera den manuellt hämtade PowerShell-modulen i en PowerShell-katalog så att den kan identifieras av PowerShell-sessioner.
Mer information finns i [installera en PowerShell-modul](/powershell/scripting/developer/module/installing-a-powershell-module).

Om du installerar modulen i en annan katalog importerar du modulen manuellt med hjälp av [import-module](/powershell/module/microsoft.powershell.core/import-module).

> [!IMPORTANT] 
> DLL-filer kommer att installeras via relativa sökvägar.
> Lagra innehållet i paketet i den avsedda körnings katalogen och bekräfta att åtkomst behörigheterna tillåter läsa men inte skriva.

1. Ändra tillägget till ". zip" och extrahera innehållet i paketet till din avsedda installations katalog.
2. Hitta fil Sök vägen för Az.ApplicationMonitor.psd1.
3. Kör PowerShell som administratör med en förhöjd körnings princip.
4. Läs in modulen med hjälp av `Import-Module Az.ApplicationMonitor.psd1` kommandot.
    

## <a name="route-traffic-through-a-proxy"></a>Dirigera trafik via en proxy

När du övervakar en dator i ditt privata intranät måste du dirigera HTTP-trafik via en proxy.

PowerShell-kommandon för att hämta och installera AZ. ApplicationMonitor från PowerShell-galleriet stöd för en `-Proxy` parameter.
Granska föregående instruktioner när du skriver installations skripten.

Application Insights SDK måste skicka appens telemetri till Microsoft. Vi rekommenderar att du konfigurerar proxyinställningar för din app i web.config-filen. Mer information finns i [Application Insights FAQ: proxy passthrough](../faq.md#proxy-passthrough).


## <a name="enable-monitoring"></a>Aktivera övervakning

Använd `Enable-ApplicationInsightsMonitoring` kommandot för att aktivera övervakning.

En detaljerad beskrivning av hur du använder den här cmdleten finns i [API-referensen](./status-monitor-v2-api-reference.md#enable-applicationinsightsmonitoring) .



## <a name="next-steps"></a>Nästa steg

 Visa telemetrin:

- [Utforska mått](../essentials/metrics-charts.md) för att övervaka prestanda och användning.
- [Sök efter händelser och loggar](./diagnostic-search.md) för att diagnostisera problem.
- [Använd Analytics](../logs/log-query-overview.md) för mer avancerade frågor.
- [Skapa instrument paneler](./overview-dashboard.md).

 Lägg till mer telemetri:

- [Skapa webbtester](monitor-web-app-availability.md) så att du är säker på att webbplatsen är aktiv.
- [Lägg till telemetri för webb klienter](./javascript.md) om du vill visa undantag från webb sidans kod och aktivera spårnings anrop.
- [Lägg till Application Insights SDK till din kod](./asp-net.md) så att du kan infoga spårnings-och logg anrop.

Gör mer med Application Insights agent:

- Använd vår guide för att [felsöka](status-monitor-v2-troubleshoot.md) Application Insights-agenten.

