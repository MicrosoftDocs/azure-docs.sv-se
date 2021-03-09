---
title: Fel sökning och kända problem med Azure Application Insights-agenten | Microsoft Docs
description: Kända problem med Application Insights agent och fel söknings exempel. Övervaka webbplatsens prestanda utan att omdistribuera webbplatsen. Fungerar med ASP.NET-webbappar som finns lokalt, i virtuella datorer eller på Azure.
ms.topic: conceptual
author: TimothyMothra
ms.author: tilee
ms.date: 04/23/2019
ms.openlocfilehash: be202a6016842ee82b3230c2841d416d3b53ea15
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/09/2021
ms.locfileid: "102505587"
---
# <a name="troubleshooting-application-insights-agent-formerly-named-status-monitor-v2"></a>Felsöka Application Insights agent (tidigare namngiven Statusövervakare v2)

När du aktiverar övervakning kan det uppstå problem som förhindrar insamling av data.
Den här artikeln innehåller alla kända problem och fel söknings exempel.

## <a name="known-issues"></a>Kända problem

### <a name="conflicting-dlls-in-an-apps-bin-directory"></a>Bibliotek i konflikt i en Apps bin-katalog

Om någon av dessa DLL-filer finns i bin-katalogen kan övervakningen Miss Miss kan:

- Microsoft.ApplicationInsights.dll
- Microsoft.AspNet.TelemetryCorrelation.dll
- System.Diagnostics.DiagnosticSource.dll

Vissa av dessa DLL-filer ingår i standardapparna för Visual Studio, även om din app inte använder dem.
Du kan använda fel söknings verktyg för att Visa symptomatic beteende:

- PerfView
    ```
    ThreadID="7,500" 
    ProcessorNumber="0" 
    msg="Found 'System.Diagnostics.DiagnosticSource, Version=4.0.2.1, Culture=neutral, PublicKeyToken=cc7b13ffcd2ddd51' assembly, skipping attaching redfield binaries" 
    ExtVer="2.8.13.5972" 
    SubscriptionId="" 
    AppName="" 
    FormattedMessage="Found 'System.Diagnostics.DiagnosticSource, Version=4.0.2.1, Culture=neutral, PublicKeyToken=cc7b13ffcd2ddd51' assembly, skipping attaching redfield binaries" 
    ```

- IISReset och app load (utan telemetri). Undersök med Sysinternals (Handle.exe och ListDLLs.exe):
    ```
    .\handle64.exe -p w3wp | findstr /I "InstrumentationEngine AI. ApplicationInsights"
    E54: File  (R-D)   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.ApplicationInsights.RedfieldIISModule.dll

    .\Listdlls64.exe w3wp | findstr /I "InstrumentationEngine AI ApplicationInsights"
    0x0000000009be0000  0x127000  C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Instrumentation64\MicrosoftInstrumentationEngine_x64.dll
    0x0000000009b90000  0x4f000   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Instrumentation64\Microsoft.ApplicationInsights.ExtensionsHost_x64.dll
    0x0000000004d20000  0xb2000   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Instrumentation64\Microsoft.ApplicationInsights.Extensions.Base_x64.dll
    ```

### <a name="powershell-versions"></a>PowerShell-versioner
Den här produkten har skrivits och testats med PowerShell v 5.1.
Den här modulen är inte kompatibel med PowerShell-versionerna 6 eller 7.
Vi rekommenderar att du använder PowerShell v 5.1 tillsammans med nyare versioner. Mer information finns i [använda PowerShell 7 sida vid sida med PowerShell 5,1](/powershell/scripting/install/migrating-from-windows-powershell-51-to-powershell-7#using-powershell-7-side-by-side-with-windows-powershell-51).

### <a name="conflict-with-iis-shared-configuration"></a>Konflikt med delad IIS-konfiguration

Om du har ett kluster av webb servrar kan du använda en [delad konfiguration](/iis/web-hosting/configuring-servers-in-the-windows-web-platform/shared-configuration_211).
Det går inte att mata in HttpModule i denna delade konfiguration.
Kör kommandot enable på varje webb server för att installera-DLL-filen i varje servers GAC.

När du har kört kommandot Aktivera slutför du följande steg:
1. Gå till den delade konfigurations katalogen och hitta applicationHost.configs filen.
2. Lägg till den här raden i avsnittet moduler i konfigurationen:
    ```
    <modules>
        <!-- Registered global managed http module handler. The 'Microsoft.AppInsights.IIS.ManagedHttpModuleHelper.dll' must be installed in the GAC before this config is applied. -->
        <add name="ManagedHttpModuleHelper" type="Microsoft.AppInsights.IIS.ManagedHttpModuleHelper.ManagedHttpModuleHelper, Microsoft.AppInsights.IIS.ManagedHttpModuleHelper, Version=1.0.0.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35" preCondition="managedHandler,runtimeVersionv4.0" />
    </modules>
    ```

### <a name="iis-nested-applications"></a>IIS-kapslade program

Vi instrumenterar inte kapslade program i IIS i version 1,0.

### <a name="advanced-sdk-configuration-isnt-available"></a>Avancerad SDK-konfiguration är inte tillgänglig.

SDK-konfigurationen visas inte för slutanvändaren i version 1,0.

    
    
## <a name="troubleshooting"></a>Felsökning
    
### <a name="troubleshooting-powershell"></a>Felsöka PowerShell

#### <a name="determine-which-modules-are-available"></a>Avgöra vilka moduler som är tillgängliga
Du kan använda `Get-Module -ListAvailable` kommandot för att avgöra vilka moduler som är installerade.

#### <a name="import-a-module-into-the-current-session"></a>Importera en modul till den aktuella sessionen
Om en modul inte har lästs in i en PowerShell-session kan du läsa in den manuellt med hjälp av `Import-Module <path to psd1>` kommandot.


### <a name="troubleshooting-the-application-insights-agent-module"></a>Felsöka modulen Application Insights agent

#### <a name="list-the-commands-available-in-the-application-insights-agent-module"></a>Visa en lista med kommandon som är tillgängliga i modulen Application Insights agent
Kör kommandot `Get-Command -Module Az.ApplicationMonitor` för att hämta tillgängliga kommandon:

```
CommandType     Name                                               Version    Source
-----------     ----                                               -------    ------
Cmdlet          Disable-ApplicationInsightsMonitoring              0.4.0      Az.ApplicationMonitor
Cmdlet          Disable-InstrumentationEngine                      0.4.0      Az.ApplicationMonitor
Cmdlet          Enable-ApplicationInsightsMonitoring               0.4.0      Az.ApplicationMonitor
Cmdlet          Enable-InstrumentationEngine                       0.4.0      Az.ApplicationMonitor
Cmdlet          Get-ApplicationInsightsMonitoringConfig            0.4.0      Az.ApplicationMonitor
Cmdlet          Get-ApplicationInsightsMonitoringStatus            0.4.0      Az.ApplicationMonitor
Cmdlet          Set-ApplicationInsightsMonitoringConfig            0.4.0      Az.ApplicationMonitor
Cmdlet          Start-ApplicationInsightsMonitoringTrace           0.4.0      Az.ApplicationMonitor
```

#### <a name="determine-the-current-version-of-the-application-insights-agent-module"></a>Ta reda på den aktuella versionen av modulen Application Insights agent
Kör `Get-ApplicationInsightsMonitoringStatus -PowerShellModule` kommandot för att visa följande information om modulen:
   - Version av PowerShell-modul
   - Application Insights SDK-version
   - Fil Sök vägar för PowerShell-modulen
    
Se [API-referensen](status-monitor-v2-api-reference.md) för en detaljerad beskrivning av hur du använder den här cmdleten.


### <a name="troubleshooting-running-processes"></a>Felsöka processer som körs

Du kan kontrol lera processerna på den instrumenterade datorn för att avgöra om alla DLL-filer har lästs in.
Om övervakningen fungerar bör minst 12 DLL-filer läsas in.

Använd `Get-ApplicationInsightsMonitoringStatus -InspectProcess` kommandot för att kontrol lera DLL-filerna.

Se [API-referensen](status-monitor-v2-api-reference.md) för en detaljerad beskrivning av hur du använder den här cmdleten.


### <a name="collect-etw-logs-by-using-perfview"></a>Samla in ETW-loggar med PerfView

#### <a name="setup"></a>Installation

1. Ladda ned PerfView.exe och PerfView64.exe från [GitHub](https://github.com/Microsoft/perfview/releases).
2. Starta PerfView64.exe.
3. Expandera **Avancerade alternativ**.
4. Avmarkera de här kryss rutorna:
    - **Komprimera**
    - **Sammanfoga**
    - **Samling med .NET-symboler**
5. Ange följande **ytterligare providers**: `61f6ca3b-4b5f-5602-fa60-759a2a2d1fbd,323adc25-e39b-5c87-8658-2c1af1a92dc5,925fa42b-9ef6-5fa7-10b8-56449d7a2040,f7d60e07-e910-5aca-bdd2-9de45b46c560,7c739bb9-7861-412e-ba50-bf30d95eae36,61f6ca3b-4b5f-5602-fa60-759a2a2d1fbd,323adc25-e39b-5c87-8658-2c1af1a92dc5,252e28f4-43f9-5771-197a-e8c7e750a984`


#### <a name="collecting-logs"></a>Samlar in loggar

1. I en kommando konsol med administratörs behörighet kör du `iisreset /stop` kommandot för att stänga av IIS och alla webbappar.
2. I PerfView väljer du **starta insamling**.
3. I en kommando konsol med administratörs behörighet kör du `iisreset /start` kommandot för att starta IIS.
4. Försök att bläddra till din app.
5. När din app har lästs in återgår du till PerfView och väljer **stoppa insamling**.



## <a name="next-steps"></a>Nästa steg

- Läs [API-referensen](status-monitor-v2-overview.md#powershell-api-reference) om du vill veta mer om parametrar som du kan ha missat.