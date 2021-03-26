---
title: Referens för Azure Application Insights .net Agent API
description: Application Insights Agent-API-referens. Övervaka webbplatsens prestanda utan att omdistribuera webbplatsen. Fungerar med ASP.NET-webbappar som finns lokalt, i virtuella datorer eller på Azure.
ms.topic: conceptual
author: TimothyMothra
ms.author: tilee
ms.date: 04/23/2019
ms.openlocfilehash: 7c5c2f1d055ec6270892873548872b20b17b4158
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2021
ms.locfileid: "105566905"
---
# <a name="azure-monitor-application-insights-agent-api-reference"></a>Azure Monitor Application Insights Agent-API-referens

Den här artikeln beskriver en cmdlet som är medlem i [PowerShell-modulen AZ. ApplicationMonitor](https://www.powershellgallery.com/packages/Az.ApplicationMonitor/).

> [!NOTE] 
> - Du behöver en Instrumentation-nyckel för att komma igång. Mer information finns i [skapa en resurs](create-new-resource.md#copy-the-instrumentation-key).
> - Denna cmdlet kräver att du granskar och godkänner vår licens-och sekretess policy.

> [!IMPORTANT] 
> Denna cmdlet kräver en PowerShell-session med administratörs behörighet och en förhöjd princip för körning. Mer information finns i [kör PowerShell som administratör med en förhöjd körnings princip](status-monitor-v2-detailed-instructions.md#run-powershell-as-admin-with-an-elevated-execution-policy).
> - Denna cmdlet kräver att du granskar och godkänner vår licens-och sekretess policy.
> - Instrumentation-motorn lägger till ytterligare kostnader och är inaktive rad som standard.


## <a name="enable-instrumentationengine"></a>Enable-InstrumentationEngine

Aktiverar Instrumentation-motorn genom att ange vissa register nycklar.
Starta om IIS för att ändringarna ska börja gälla.

Instrumentation-motorn kan komplettera data som samlas in av .NET SDK: er.
Den samlar in händelser och meddelanden som beskriver körningen av en hanterad process. Dessa händelser och meddelanden innehåller beroende resultat koder, HTTP-verb och SQL- [kommando text](asp-net-dependencies.md#advanced-sql-tracking-to-get-full-sql-query).

Aktivera Instrumentation Engine om:
- Du har redan aktiverat övervakning med cmdleten Enable men inte aktiverat Instrumentation-motorn.
- Du har manuellt instrumenterat din app med .NET-SDK: er och vill samla in ytterligare telemetri.

### <a name="examples"></a>Exempel

```powershell
PS C:\> Enable-InstrumentationEngine
```

### <a name="parameters"></a>Parametrar

#### <a name="-acceptlicense"></a>-AcceptLicense
**Valfritt.** Använd den här växeln för att godkänna licens-och sekretess policyn i konsolbaserade installationer.

#### <a name="-verbose"></a>– Utförlig
**Gemensam parameter.** Använd den här växeln för att skriva ut detaljerade loggar.

### <a name="output"></a>Utdata


##### <a name="example-output-from-successfully-enabling-the-instrumentation-engine"></a>Exempel på utdata från har aktiverat Instrumentation Engine

```
Configuring IIS Environment for instrumentation engine...
Configuring registry for instrumentation engine...
```

## <a name="enable-applicationinsightsmonitoring"></a>Enable-ApplicationInsightsMonitoring

Möjliggör kod kopplings övervakning av IIS-appar på en måldator.

Denna cmdlet kommer att ändra IIS-applicationHost.config och ange vissa register nycklar.
En applicationinsights.ikey.config-fil skapas också, som definierar Instrumentation-nyckeln som används av varje app.
IIS läser in RedfieldModule vid start, som kommer att mata in Application Insights SDK i program när programmen startas.
Starta om IIS för att ändringarna ska börja gälla.

När du har aktiverat övervakning rekommenderar vi att du använder [Live-mått](live-stream.md) för att snabbt kontrol lera om din app skickar oss telemetri.

### <a name="examples"></a>Exempel

#### <a name="example-with-a-single-instrumentation-key"></a>Exempel med en enda Instrumentation-nyckel
I det här exemplet tilldelas alla appar på den aktuella datorn en enda Instrumentation-nyckel.

```powershell
PS C:\> Enable-ApplicationInsightsMonitoring -InstrumentationKey xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
```

#### <a name="example-with-an-instrumentation-key-map"></a>Exempel med en instrumentande nyckel karta
I det här exemplet:
- `MachineFilter` matchar den aktuella datorn med `'.*'` jokertecken.
- `AppFilter='WebAppExclude'` tillhandahåller en `null` Instrumentation-nyckel. Den angivna appen instrumenteras inte.
- `AppFilter='WebAppOne'` tilldelar den angivna appen en unik Instrumentation-nyckel.
- `AppFilter='WebAppTwo'` tilldelar den angivna appen en unik Instrumentation-nyckel.
- Slutligen `AppFilter` använder `'.*'` jokertecken jokertecken för att matcha alla webbappar som inte matchar de tidigare reglerna och tilldelar en standard Instrumentation-nyckel.
- Blank steg har lagts till för läsbarhet.

```powershell
PS C:\> Enable-ApplicationInsightsMonitoring -InstrumentationKeyMap 
    @(@{MachineFilter='.*';AppFilter='WebAppExclude'},
      @{MachineFilter='.*';AppFilter='WebAppOne';InstrumentationSettings=@{InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx1'}},
      @{MachineFilter='.*';AppFilter='WebAppTwo';InstrumentationSettings=@{InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx2'}},
      @{MachineFilter='.*';AppFilter='.*';InstrumentationSettings=@{InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxdefault'}})

```

> [!NOTE]
> Namngivningen av AppFilter i den här kontexten kan vara förvirrande, `AppFilter` ställer in program namnets regex-filter (HostingEnvironment. webbplats namn i fallet med .net i IIS). `VirtualPathFilter` anger regex-filtret för virtuell sökväg (HostingEnvironment. ApplicationVirtualPath om det gäller .net i IIS). För att kunna instrumentera en enda app använder du VirtualPathFilter på följande sätt: `Enable-ApplicationInsightsMonitoring -InstrumentationKeyMap @(@{VirtualPathFilter="^/MyAppName$"; InstrumentationSettings=@{InstrumentationKey='<your ikey>'}})`

### <a name="parameters"></a>Parametrar

#### <a name="-instrumentationkey"></a>– InstrumentationKey
**Kunna.** Använd den här parametern för att ange en enskild Instrumentation-nyckel som kan användas av alla appar på mål datorn.

#### <a name="-instrumentationkeymap"></a>-InstrumentationKeyMap
**Kunna.** Använd den här parametern för att ange flera instrument nycklar och en mappning av de Instrumentation-nycklar som används av varje app.
Du kan skapa ett enda installations skript för flera datorer genom att ange `MachineFilter` .

> [!IMPORTANT]
> Apparna kommer att matchas mot regler i den ordning som reglerna anges. Därför bör du ange de mest aktuella reglerna först och de mest allmänna reglerna sist.

##### <a name="schema"></a>Schema
`@(@{MachineFilter='.*';AppFilter='.*';InstrumentationSettings=@{InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx'}})`

- **MachineFilter** är ett obligatoriskt C#-regex av datorn eller namnet på den virtuella datorn.
    - ". *" matchar alla
    - ComputerName matchar bara datorer med det angivna namnet.
- **AppFilter** är ett obligatoriskt C#-regex av IIS-webbplatsens namn. Du kan hämta en lista över platser på servern genom att köra kommandot [Get-iissite](/powershell/module/iisadministration/get-iissite).
    - ". *" matchar alla
    - "Webbplats namn" kommer bara att matcha IIS-platsen med det angivna namnet.
- **InstrumentationKey** krävs för att kunna övervaka appar som matchar de föregående två filtren.
    - Lämna värdet null om du vill definiera regler för att undanta övervakning.


#### <a name="-enableinstrumentationengine"></a>-EnableInstrumentationEngine
**Valfritt.** Använd den här växeln för att låta instrument motorn samla in händelser och meddelanden om vad som händer under körningen av en hanterad process. Dessa händelser och meddelanden innehåller beroende resultat koder, HTTP-verb och SQL-kommando text.

Instrumentation-motorn lägger till overhead och är inaktive rad som standard.

#### <a name="-acceptlicense"></a>-AcceptLicense
**Valfritt.** Använd den här växeln för att godkänna licens-och sekretess policyn i konsolbaserade installationer.

#### <a name="-ignoresharedconfig"></a>-IgnoreSharedConfig
När du har ett kluster av webb servrar kan du använda en [delad konfiguration](/iis/web-hosting/configuring-servers-in-the-windows-web-platform/shared-configuration_211).
Det går inte att mata in HttpModule i denna delade konfiguration.
Det här skriptet kommer inte att fungera med meddelandet om att extra installations steg krävs.
Använd den här växeln för att ignorera den här kontrollen och fortsätta installera nödvändiga komponenter. Mer information finns i [känd konflikt med-IIS-Shared-Configuration](status-monitor-v2-troubleshoot.md#conflict-with-iis-shared-configuration)

#### <a name="-verbose"></a>– Utförlig
**Gemensam parameter.** Använd den här växeln för att visa detaljerade loggar.

#### <a name="-whatif"></a>-WhatIf 
**Gemensam parameter.** Använd den här växeln för att testa och validera dina indataparametrar utan att aktivera övervakning.

### <a name="output"></a>Utdata

#### <a name="example-output-from-a-successful-enablement"></a>Exempel på utdata från en lyckad aktivering

```powershell
Initiating Disable Process
Applying transformation to 'C:\Windows\System32\inetsrv\config\applicationHost.config'
'C:\Windows\System32\inetsrv\config\applicationHost.config' backed up to 'C:\Windows\System32\inetsrv\config\applicationHost.config.backup-2019-03-26_08-59-52z'
in :1,237
No element in the source document matches '/configuration/location[@path='']/system.webServer/modules/add[@name='ManagedHttpModuleHelper']'
Not executing RemoveAll (transform line 1, 546)
Transformation to 'C:\Windows\System32\inetsrv\config\applicationHost.config' was successfully applied. Operation: 'disable'
GAC Module will not be removed, since this operation might cause IIS instabilities
Configuring IIS Environment for codeless attach...
Registry: skipping non-existent 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\IISADMIN[Environment]
Registry: skipping non-existent 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\W3SVC[Environment]
Registry: skipping non-existent 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\WAS[Environment]
Configuring IIS Environment for instrumentation engine...
Registry: skipping non-existent 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\IISADMIN[Environment]
Registry: skipping non-existent 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\W3SVC[Environment]
Registry: skipping non-existent 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\WAS[Environment]
Configuring registry for instrumentation engine...
Successfully disabled Application Insights Status Monitor
Installing GAC module 'C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\0.2.0\content\Runtime\Microsoft.AppInsights.IIS.ManagedHttpModuleHelper.dll'
Applying transformation to 'C:\Windows\System32\inetsrv\config\applicationHost.config'
Found GAC module Microsoft.AppInsights.IIS.ManagedHttpModuleHelper.ManagedHttpModuleHelper, Microsoft.AppInsights.IIS.ManagedHttpModuleHelper, Version=1.0.0.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35
'C:\Windows\System32\inetsrv\config\applicationHost.config' backed up to 'C:\Windows\System32\inetsrv\config\applicationHost.config.backup-2019-03-26_08-59-52z_1'
Transformation to 'C:\Windows\System32\inetsrv\config\applicationHost.config' was successfully applied. Operation: 'enable'
Configuring IIS Environment for codeless attach...
Configuring IIS Environment for instrumentation engine...
Configuring registry for instrumentation engine...
Updating app pool permissions...
Successfully enabled Application Insights Status Monitor
```

## <a name="disable-instrumentationengine"></a>Disable-InstrumentationEngine

Inaktiverar Instrumentation-motorn genom att ta bort vissa register nycklar.
Starta om IIS för att ändringarna ska börja gälla.

### <a name="examples"></a>Exempel

```powershell
PS C:\> Disable-InstrumentationEngine
```

### <a name="parameters"></a>Parametrar 

#### <a name="-verbose"></a>– Utförlig
**Gemensam parameter.** Använd den här växeln för att skriva ut detaljerade loggar.

### <a name="output"></a>Utdata


##### <a name="example-output-from-successfully-disabling-the-instrumentation-engine"></a>Exempel på utdata från att inaktivera Instrumentation-motorn

```powershell
Configuring IIS Environment for instrumentation engine...
Registry: removing 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\IISADMIN[Environment]'
Registry: removing 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\W3SVC[Environment]'
Registry: removing 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\WAS[Environment]'
Configuring registry for instrumentation engine...
```

## <a name="disable-applicationinsightsmonitoring"></a>Disable-ApplicationInsightsMonitoring

Inaktiverar övervakning på mål datorn.
Denna cmdlet tar bort ändringar i IIS-applicationHost.config och tar bort register nycklar.

### <a name="examples"></a>Exempel

```powershell
PS C:\> Disable-ApplicationInsightsMonitoring
```

### <a name="parameters"></a>Parametrar 

#### <a name="-verbose"></a>– Utförlig
**Gemensam parameter.** Använd den här växeln för att visa detaljerade loggar.

### <a name="output"></a>Utdata


##### <a name="example-output-from-successfully-disabling-monitoring"></a>Exempel på utdata från att inaktivera övervakning

```powershell
Initiating Disable Process
Applying transformation to 'C:\Windows\System32\inetsrv\config\applicationHost.config'
'C:\Windows\System32\inetsrv\config\applicationHost.config' backed up to 'C:\Windows\System32\inetsrv\config\applicationHost.config.backup-2019-03-26_08-59-00z'
in :1,237
No element in the source document matches '/configuration/location[@path='']/system.webServer/modules/add[@name='ManagedHttpModuleHelper']'
Not executing RemoveAll (transform line 1, 546)
Transformation to 'C:\Windows\System32\inetsrv\config\applicationHost.config' was successfully applied. Operation: 'disable'
GAC Module will not be removed, since this operation might cause IIS instabilities
Configuring IIS Environment for codeless attach...
Registry: skipping non-existent 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\IISADMIN[Environment]
Registry: skipping non-existent 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\W3SVC[Environment]
Registry: skipping non-existent 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\WAS[Environment]
Configuring IIS Environment for instrumentation engine...
Registry: skipping non-existent 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\IISADMIN[Environment]
Registry: skipping non-existent 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\W3SVC[Environment]
Registry: skipping non-existent 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\WAS[Environment]
Configuring registry for instrumentation engine...
Successfully disabled Application Insights Status Monitor
```


## <a name="get-applicationinsightsmonitoringconfig"></a>Get-ApplicationInsightsMonitoringConfig

Hämtar konfigurations filen och skriver ut värdena till-konsolen.

### <a name="examples"></a>Exempel

```powershell
PS C:\> Get-ApplicationInsightsMonitoringConfig
```

### <a name="parameters"></a>Parametrar

Inga parametrar krävs.

### <a name="output"></a>Utdata


##### <a name="example-output-from-reading-the-config-file"></a>Exempel på utdata från att läsa konfigurations filen

```
RedfieldConfiguration:
Filters:
0)InstrumentationKey:  AppFilter: WebAppExclude MachineFilter: .*
1)InstrumentationKey: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx2 AppFilter: WebAppTwo MachineFilter: .*
2)InstrumentationKey: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxdefault AppFilter: .* MachineFilter: .*
```

## <a name="get-applicationinsightsmonitoringstatus"></a>Get-ApplicationInsightsMonitoringStatus

Denna cmdlet innehåller felsöknings information om Statusövervakare.
Använd den här cmdleten för att undersöka övervaknings status, version av PowerShell-modulen och för att kontrol lera processen som körs.
Denna cmdlet kommer att rapportera versions information och information om viktiga filer som krävs för övervakning.

### <a name="examples"></a>Exempel

#### <a name="example-application-status"></a>Exempel: program status

Kör kommandot `Get-ApplicationInsightsMonitoringStatus` för att Visa övervaknings statusen för webbplatser.

```powershell

PS C:\Windows\system32> Get-ApplicationInsightsMonitoringStatus

IIS Websites:

SiteName               : Default Web Site
ApplicationPoolName    : DefaultAppPool
SiteId                 : 1
SiteState              : Stopped

SiteName               : DemoWebApp111
ApplicationPoolName    : DemoWebApp111
SiteId                 : 2
SiteState              : Started
ProcessId              : not found

SiteName               : DemoWebApp222
ApplicationPoolName    : DemoWebApp222
SiteId                 : 3
SiteState              : Started
ProcessId              : 2024
Instrumented           : true
InstrumentationKey     : xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxx123

SiteName               : DemoWebApp333
ApplicationPoolName    : DemoWebApp333
SiteId                 : 4
SiteState              : Started
ProcessId              : 5184
AppAlreadyInstrumented : true
```

I det här exemplet;
- **Dator** -ID är ett anonymt ID som används för att identifiera servern unikt. Om du skapar en support förfrågan behöver vi detta ID för att hitta loggar för servern.
- **Standard webbplatsen** stoppas i IIS
- **DemoWebApp111** har startats i IIS, men har inte tagit emot några förfrågningar. Den här rapporten visar att det inte finns någon process som körs (ProcessId: hittades inte).
- **DemoWebApp222** körs och övervakas (instrumenterat: sant). Baserat på användar konfigurationen matchades Instrumentation-nyckeln XXXXXXXX-XXXX-XXXX-XXXX-xxxxxxxxx123 för den här platsen.
- **DemoWebApp333** har instrumenterats manuellt med hjälp av Application Insights SDK. Statusövervakare identifierade SDK och övervakar inte den här platsen.


#### <a name="example-powershell-module-information"></a>Exempel: information om PowerShell-modul

Kör kommandot `Get-ApplicationInsightsMonitoringStatus -PowerShellModule` för att visa information om den aktuella modulen:

```powershell

PS C:\> Get-ApplicationInsightsMonitoringStatus -PowerShellModule

PowerShell Module version:
0.4.0-alpha

Application Insights SDK version:
2.9.0.3872

Executing PowerShell Module Assembly:
Microsoft.ApplicationInsights.Redfield.Configurator.PowerShell, Version=2.8.14.11432, Culture=neutral, PublicKeyToken=31bf3856ad364e35

PowerShell Module Directory:
C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\0.2.2\content\PowerShell

Runtime Paths:
ParentDirectory (Exists: True)
C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content

ConfigurationPath (Exists: True)
C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\applicationInsights.ikey.config

ManagedHttpModuleHelperPath (Exists: True)
C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.AppInsights.IIS.ManagedHttpModuleHelper.dll

RedfieldIISModulePath (Exists: True)
C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.ApplicationInsights.RedfieldIISModule.dll

InstrumentationEngine86Path (Exists: True)
C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Instrumentation32\MicrosoftInstrumentationEngine_x86.dll

InstrumentationEngine64Path (Exists: True)
C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Instrumentation64\MicrosoftInstrumentationEngine_x64.dll

InstrumentationEngineExtensionHost86Path (Exists: True)
C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Instrumentation32\Microsoft.ApplicationInsights.ExtensionsHost_x86.dll

InstrumentationEngineExtensionHost64Path (Exists: True)
C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Instrumentation64\Microsoft.ApplicationInsights.ExtensionsHost_x64.dll

InstrumentationEngineExtensionConfig86Path (Exists: True)
C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Instrumentation32\Microsoft.InstrumentationEngine.Extensions.config

InstrumentationEngineExtensionConfig64Path (Exists: True)
C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Instrumentation64\Microsoft.InstrumentationEngine.Extensions.config

ApplicationInsightsSdkPath (Exists: True)
C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.ApplicationInsights.dll
```

#### <a name="example-runtime-status"></a>Exempel: körnings status

Du kan kontrol lera processen på den instrumenterade datorn för att se om alla DLL-filer har lästs in. Om övervakningen fungerar bör minst 12 DLL-filer läsas in.

Kör kommandot `Get-ApplicationInsightsMonitoringStatus -InspectProcess`:


```
PS C:\> Get-ApplicationInsightsMonitoringStatus -InspectProcess

iisreset.exe /status
Status for IIS Admin Service ( IISADMIN ) : Running
Status for Windows Process Activation Service ( WAS ) : Running
Status for Net.Msmq Listener Adapter ( NetMsmqActivator ) : Running
Status for Net.Pipe Listener Adapter ( NetPipeActivator ) : Running
Status for Net.Tcp Listener Adapter ( NetTcpActivator ) : Running
Status for World Wide Web Publishing Service ( W3SVC ) : Running

handle64.exe -accepteula -p w3wp
BF0: File  (R-D)   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.AI.ServerTelemetryChannel.dll
C58: File  (R-D)   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.AI.AzureAppServices.dll
C68: File  (R-D)   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.AI.DependencyCollector.dll
C78: File  (R-D)   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.AI.WindowsServer.dll
C98: File  (R-D)   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.AI.Web.dll
CBC: File  (R-D)   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.AI.PerfCounterCollector.dll
DB0: File  (R-D)   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.AI.Agent.Intercept.dll
B98: File  (R-D)   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.ApplicationInsights.RedfieldIISModule.dll
BB4: File  (R-D)   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.ApplicationInsights.RedfieldIISModule.Contracts.dll
BCC: File  (R-D)   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.ApplicationInsights.Redfield.Lightup.dll
BE0: File  (R-D)   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.ApplicationInsights.dll

listdlls64.exe -accepteula w3wp
0x0000000019ac0000  0x127000  C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Instrumentation64\MicrosoftInstrumentationEngine_x64.dll
0x00000000198b0000  0x4f000   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Instrumentation64\Microsoft.ApplicationInsights.ExtensionsHost_x64.dll
0x000000000c460000  0xb2000   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Instrumentation64\Microsoft.ApplicationInsights.Extensions.Base_x64.dll
0x000000000ad60000  0x108000  C:\Windows\TEMP\2.4.0.0.Microsoft.ApplicationInsights.Extensions.Intercept_x64.dll
```

### <a name="parameters"></a>Parametrar

#### <a name="no-parameters"></a>(Inga parametrar)

Som standard rapporterar denna cmdlet övervaknings statusen för webb program.
Använd det här alternativet om du vill läsa om ditt program har instrumenterats.
Du kan också granska vilken Instrumentation-nyckel som matchade din webbplats.


#### <a name="-powershellmodule"></a>-PowerShellModule
**Valfritt**. Använd den här växeln för att rapportera versions nummer och sökvägar för dll: er som krävs för övervakning.
Använd det här alternativet om du behöver identifiera versionen av någon DLL, inklusive Application Insights SDK.

#### <a name="-inspectprocess"></a>-InspectProcess

**Valfritt**. Använd den här växeln för att rapportera om IIS körs.
Du kan också hämta externa verktyg för att avgöra om de nödvändiga DLL-filerna har lästs in i IIS-körningen.


Om den här processen Miss lyckas av någon anledning kan du köra dessa kommandon manuellt:
- iisreset.exe/status
- [handle64.exe](/sysinternals/downloads/handle) -p W3wp | Findstr/I "InstrumentationEngine AI. ApplicationInsights
- [listdlls64.exe](/sysinternals/downloads/listdlls) W3wp | Findstr/I "InstrumentationEngine AI ApplicationInsights"


#### <a name="-force"></a>-Force

**Valfritt**. Används endast med InspectProcess. Använd den här växeln för att hoppa över den användar fråga som visas innan ytterligare verktyg laddas ned.


## <a name="set-applicationinsightsmonitoringconfig"></a>Set-ApplicationInsightsMonitoringConfig

Anger konfigurations filen utan att göra en fullständig ominstallation.
Starta om IIS för att ändringarna ska börja gälla.

> [!IMPORTANT] 
> Denna cmdlet kräver en PowerShell-session med administratörs behörighet.


### <a name="examples"></a>Exempel

#### <a name="example-with-a-single-instrumentation-key"></a>Exempel med en enda Instrumentation-nyckel
I det här exemplet kommer alla appar på den aktuella datorn att tilldelas en enda Instrumentation-nyckel.

```powershell
PS C:\> Enable-ApplicationInsightsMonitoring -InstrumentationKey xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
```

#### <a name="example-with-an-instrumentation-key-map"></a>Exempel med en instrumentande nyckel karta
I det här exemplet:
- `MachineFilter` matchar den aktuella datorn med `'.*'` jokertecken.
- `AppFilter='WebAppExclude'` tillhandahåller en `null` Instrumentation-nyckel. Den angivna appen instrumenteras inte.
- `AppFilter='WebAppOne'` tilldelar den angivna appen en unik Instrumentation-nyckel.
- `AppFilter='WebAppTwo'` tilldelar den angivna appen en unik Instrumentation-nyckel.
- Slutligen `AppFilter` använder `'.*'` jokertecken jokertecken för att matcha alla webbappar som inte matchar de tidigare reglerna och tilldelar en standard Instrumentation-nyckel.
- Blank steg har lagts till för läsbarhet.

```powershell
Enable-ApplicationInsightsMonitoring -InstrumentationKeyMap `
       @(@{MachineFilter='.*';AppFilter='WebAppExclude'},
          @{MachineFilter='.*';AppFilter='WebAppOne';InstrumentationSettings=@{InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx1'}},
          @{MachineFilter='.*';AppFilter='WebAppTwo';InstrumentationSettings=@{InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx2'}},
          @{MachineFilter='.*';AppFilter='.*';InstrumentationSettings=@{InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxdefault'}})
```

### <a name="parameters"></a>Parametrar

#### <a name="-instrumentationkey"></a>– InstrumentationKey
**Kunna.** Använd den här parametern för att ange en enskild Instrumentation-nyckel som kan användas av alla appar på mål datorn.

#### <a name="-instrumentationkeymap"></a>-InstrumentationKeyMap
**Kunna.** Använd den här parametern för att ange flera instrument nycklar och en mappning av de Instrumentation-nycklar som används av varje app.
Du kan skapa ett enda installations skript för flera datorer genom att ange `MachineFilter` .

> [!IMPORTANT]
> Apparna kommer att matchas mot regler i den ordning som reglerna anges. Därför bör du ange de mest aktuella reglerna först och de mest allmänna reglerna sist.

##### <a name="schema"></a>Schema
`@(@{MachineFilter='.*';AppFilter='.*';InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx'})`

- **MachineFilter** är ett obligatoriskt C#-regex av datorn eller namnet på den virtuella datorn.
    - ". *" matchar alla
    - ComputerName kommer bara att matcha datorer med det angivna namnet.
- **AppFilter** är ett obligatoriskt C#-regex av datorn eller namnet på den virtuella datorn.
    - ". *" matchar alla
    - ApplicationName kommer bara att matcha IIS-appar med det angivna namnet.
- **InstrumentationKey** krävs för att aktivera övervakning av de appar som matchar de föregående två filtren.
    - Lämna värdet null om du vill definiera regler för att undanta övervakning.


#### <a name="-verbose"></a>– Utförlig
**Gemensam parameter.** Använd den här växeln för att visa detaljerade loggar.


### <a name="output"></a>Utdata

Som standard visas inga utdata.

##### <a name="example-verbose-output-from-setting-the-config-file-via--instrumentationkey"></a>Exempel på utförlig utdata från att ställa in konfigurations filen via-InstrumentationKey

```
VERBOSE: Operation: InstallWithIkey
VERBOSE: InstrumentationKeyMap parsed:
Filters:
0)InstrumentationKey: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx AppFilter: .* MachineFilter: .*
VERBOSE: set config file
VERBOSE: Config File Path:
C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\applicationInsights.ikey.config
```

##### <a name="example-verbose-output-from-setting-the-config-file-via--instrumentationkeymap"></a>Exempel på utförlig utdata från att ställa in konfigurations filen via-InstrumentationKeyMap

```
VERBOSE: Operation: InstallWithIkeyMap
VERBOSE: InstrumentationKeyMap parsed:
Filters:
0)InstrumentationKey:  AppFilter: WebAppExclude MachineFilter: .*
1)InstrumentationKey: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx2 AppFilter: WebAppTwo MachineFilter: .*
2)InstrumentationKey: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxdefault AppFilter: .* MachineFilter: .*
VERBOSE: set config file
VERBOSE: Config File Path:
C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\applicationInsights.ikey.config
```

## <a name="start-applicationinsightsmonitoringtrace"></a>Start-ApplicationInsightsMonitoringTrace

Samlar in [ETW-händelser](/windows/desktop/etw/event-tracing-portal) från koden för kod bundna bilagor. Denna cmdlet är ett alternativ till att köra [PerfView](https://github.com/microsoft/perfview).

Insamlade händelser skrivs ut till konsolen i real tid och sparas i en ETL-fil. Den utgående ETL-filen kan öppnas av [PerfView](https://github.com/microsoft/perfview) för ytterligare undersökning.

Denna cmdlet körs tills den når timeout-tiden (standard 5 minuter) eller stoppas manuellt ( `Ctrl + C` ).

### <a name="examples"></a>Exempel

#### <a name="how-to-collect-events"></a>Samla in händelser

Normalt skulle vi be dig att samla in händelser för att undersöka varför ditt program inte instrumenteras.

Koden för kod bundna bilagor genererar ETW-händelser när IIS startas och när programmet startas.

Så här samlar du in följande händelser:
1. I en cmd-konsol med administratörs behörighet kör `iisreset /stop` du för att stänga av IIS och alla webbappar.
2. Kör denna cmdlet
3. I en cmd-konsol med administratörs behörighet kör `iisreset /start` du för att starta IIS.
4. Försök att bläddra till din app.
5. När din app har slutfört inläsningen kan du stoppa den manuellt ( `Ctrl + C` ) eller vänta tills tids gränsen uppnåddes.

#### <a name="what-events-to-collect"></a>Vilka händelser som ska samlas in

Du har tre alternativ när du samlar in händelser:
1. Använd växeln `-CollectSdkEvents` för att samla in händelser som har avsänts från Application Insights SDK.
2. Använd växeln `-CollectRedfieldEvents` för att samla in händelser som har avsänts av statusövervakare och Redfield Runtime. Dessa loggar är användbara när du ska diagnostisera IIS och program start.
3. Använd båda växlar för att samla in båda händelse typerna.
4. Som standard, om ingen växel har angetts, kommer båda händelse typerna att samlas in.


### <a name="parameters"></a>Parametrar

#### <a name="-maxdurationinminutes"></a>-MaxDurationInMinutes
**Valfritt.** Använd den här parametern för att ange hur länge det här skriptet ska samla in händelser. Standardvärdet är 5 minuter.

#### <a name="-logdirectory"></a>-LogDirectory
**Valfritt.** Använd den här växeln för att ange den resulterande katalogen för ETL-filen. Som standard skapas den här filen i katalogen PowerShell-moduler. Den fullständiga sökvägen visas när skript körningen körs.


#### <a name="-collectsdkevents"></a>-CollectSdkEvents
**Valfritt.** Använd den här växeln för att samla in Application Insights SDK-händelser.

#### <a name="-collectredfieldevents"></a>-CollectRedfieldEvents
**Valfritt.** Använd den här växeln om du vill samla in händelser från Statusövervakare och Redfield-körningsmiljön.

#### <a name="-verbose"></a>– Utförlig
**Gemensam parameter.** Använd den här växeln för att skriva ut detaljerade loggar.



### <a name="output"></a>Utdata


#### <a name="example-of-application-startup-logs"></a>Exempel på program start loggar
```powershell
PS C:\Windows\system32> Start-ApplicationInsightsMonitoringTrace -ColectRedfieldEvents
Starting...
Log File: C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\logs\20190627_144217_ApplicationInsights_ETW_Trace.etl
Tracing enabled, waiting for events.
Tracing will timeout in 5 minutes. Press CTRL+C to cancel.

2:42:31 PM EVENT: Microsoft-ApplicationInsights-IIS-ManagedHttpModuleHelper Trace Resolved variables to: MicrosoftAppInsights_ManagedHttpModulePath='C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.ApplicationInsights.RedfieldIISModule.dll', MicrosoftAppInsights_ManagedHttpModuleType='Microsoft.ApplicationInsights.RedfieldIISModule.RedfieldIISModule'
2:42:31 PM EVENT: Microsoft-ApplicationInsights-IIS-ManagedHttpModuleHelper Trace Resolved variables to: MicrosoftDiagnosticServices_ManagedHttpModulePath2='', MicrosoftDiagnosticServices_ManagedHttpModuleType2=''
2:42:31 PM EVENT: Microsoft-ApplicationInsights-IIS-ManagedHttpModuleHelper Trace Environment variable 'MicrosoftDiagnosticServices_ManagedHttpModulePath2' or 'MicrosoftDiagnosticServices_ManagedHttpModuleType2' is null, skipping managed dll loading
2:42:31 PM EVENT: Microsoft-ApplicationInsights-IIS-ManagedHttpModuleHelper Trace MulticastHttpModule.constructor, success, 70 ms
2:42:31 PM EVENT: Microsoft-ApplicationInsights-RedfieldIISModule Trace Current assembly 'Microsoft.ApplicationInsights.RedfieldIISModule, Version=2.8.18.27202, Culture=neutral, PublicKeyToken=f23a46de0be5d6f3' location 'C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.ApplicationInsights.RedfieldIISModule.dll'
2:42:31 PM EVENT: Microsoft-ApplicationInsights-RedfieldIISModule Trace Matched filter '.*'~'STATUSMONITORTE', '.*'~'DemoWithSql'
2:42:31 PM EVENT: Microsoft-ApplicationInsights-RedfieldIISModule Trace Lightup assembly calculated path: 'C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.ApplicationInsights.Redfield.Lightup.dll'
2:42:31 PM EVENT: Microsoft-ApplicationInsights-FrameworkLightup Trace Loaded applicationInsights.config from assembly's resource Microsoft.ApplicationInsights.Redfield.Lightup, Version=2.8.18.27202, Culture=neutral, PublicKeyToken=f23a46de0be5d6f3/Microsoft.ApplicationInsights.Redfield.Lightup.ApplicationInsights-recommended.config
2:42:34 PM EVENT: Microsoft-ApplicationInsights-FrameworkLightup Trace Successfully attached ApplicationInsights SDK
2:42:34 PM EVENT: Microsoft-ApplicationInsights-RedfieldIISModule Trace RedfieldIISModule.LoadLightupAssemblyAndGetLightupHttpModuleClass, success, 2687 ms
2:42:34 PM EVENT: Microsoft-ApplicationInsights-RedfieldIISModule Trace RedfieldIISModule.CreateAndInitializeApplicationInsightsHttpModules(lightupHttpModuleClass), success
2:42:34 PM EVENT: Microsoft-ApplicationInsights-IIS-ManagedHttpModuleHelper Trace ManagedHttpModuleHelper, multicastHttpModule.Init() success, 3288 ms
2:42:35 PM EVENT: Microsoft-ApplicationInsights-IIS-ManagedHttpModuleHelper Trace Resolved variables to: MicrosoftAppInsights_ManagedHttpModulePath='C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.ApplicationInsights.RedfieldIISModule.dll', MicrosoftAppInsights_ManagedHttpModuleType='Microsoft.ApplicationInsights.RedfieldIISModule.RedfieldIISModule'
2:42:35 PM EVENT: Microsoft-ApplicationInsights-IIS-ManagedHttpModuleHelper Trace Resolved variables to: MicrosoftDiagnosticServices_ManagedHttpModulePath2='', MicrosoftDiagnosticServices_ManagedHttpModuleType2=''
2:42:35 PM EVENT: Microsoft-ApplicationInsights-IIS-ManagedHttpModuleHelper Trace Environment variable 'MicrosoftDiagnosticServices_ManagedHttpModulePath2' or 'MicrosoftDiagnosticServices_ManagedHttpModuleType2' is null, skipping managed dll loading
2:42:35 PM EVENT: Microsoft-ApplicationInsights-IIS-ManagedHttpModuleHelper Trace MulticastHttpModule.constructor, success, 0 ms
2:42:35 PM EVENT: Microsoft-ApplicationInsights-RedfieldIISModule Trace RedfieldIISModule.CreateAndInitializeApplicationInsightsHttpModules(lightupHttpModuleClass), success
2:42:35 PM EVENT: Microsoft-ApplicationInsights-IIS-ManagedHttpModuleHelper Trace ManagedHttpModuleHelper, multicastHttpModule.Init() success, 0 ms
Timeout Reached. Stopping...
```

## <a name="next-steps"></a>Nästa steg

  Visa telemetrin:
 - [Utforska mått](../essentials/metrics-charts.md) för att övervaka prestanda och användning.
- [Sök efter händelser och loggar](./diagnostic-search.md) för att diagnostisera problem.
- Använd [Analytics](../logs/log-query-overview.md) för mer avancerade frågor.
- [Skapa instrument paneler](./overview-dashboard.md).
 
 Lägg till mer telemetri:
 - [Skapa webbtester](monitor-web-app-availability.md) så att du är säker på att webbplatsen är aktiv.
- [Lägg till telemetri för webb klienter](./javascript.md) om du vill visa undantag från webb sidans kod och aktivera spårnings anrop.
- [Lägg till Application Insights SDK till din kod](./asp-net.md) så att du kan infoga spårnings-och logg anrop.
 
 Gör mer med Application Insights agent:
 - Använd vår guide för att [felsöka](status-monitor-v2-troubleshoot.md) Application Insights-agenten.

