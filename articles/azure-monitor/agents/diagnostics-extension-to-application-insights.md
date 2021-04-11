---
title: Skicka Azure-diagnostik data till Application Insights
description: Uppdatera Azure-diagnostik offentliga konfigurationen för att skicka data till Application Insights.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/19/2016
ms.openlocfilehash: b9e9d6b1b5939804b24fd523bf8b7444ed41178f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "101708602"
---
# <a name="send-cloud-service-virtual-machine-or-service-fabric-diagnostic-data-to-application-insights"></a>Skicka data från moln tjänst, virtuell dator eller Service Fabric diagnostikdata till Application Insights
Cloud Services, Virtual Machines, Virtual Machine Scale Sets och Service Fabric alla använder Azure-diagnostik-tillägget för att samla in data.  Azure Diagnostics skickar data till Azure Storage tabeller.  Du kan dock också skicka vidare alla eller en delmängd av data till andra platser med Azure-diagnostik-tillägget 1,5 eller senare.

Den här artikeln beskriver hur du skickar data från Azure-diagnostik-tillägget till Application Insights.

## <a name="diagnostics-configuration-explained"></a>Förklaring av diagnostisk konfiguration
Azure Diagnostics Extension 1,5 introducerade mottagare, vilket är ytterligare platser där du kan skicka diagnostikdata.

Exempel på konfiguration av en mottagare för Application Insights:

```XML
<SinksConfig>
    <Sink name="ApplicationInsights">
      <ApplicationInsights>{Insert InstrumentationKey}</ApplicationInsights>
      <Channels>
        <Channel logLevel="Error" name="MyTopDiagData"  />
        <Channel logLevel="Verbose" name="MyLogData"  />
      </Channels>
    </Sink>
</SinksConfig>
```
```JSON
"SinksConfig": {
    "Sink": [
        {
            "name": "ApplicationInsights",
            "ApplicationInsights": "{Insert InstrumentationKey}",
            "Channels": {
                "Channel": [
                    {
                        "logLevel": "Error",
                        "name": "MyTopDiagData"
                    },
                    {
                        "logLevel": "Error",
                        "name": "MyLogData"
                    }
                ]
            }
        }
    ]
}
```
- **Mottagar** *namnets* attribut är ett sträng värde som unikt identifierar mottagaren.

- **ApplicationInsights** -elementet anger Instrumentation-nyckeln till Application Insights-resursen där Azure Diagnostics-data skickas.
    - Om du inte har en befintlig Application Insights resurs, se [skapa en ny Application Insights resurs](../app/create-new-resource.md) för mer information om hur du skapar en resurs och hämtar Instrumentation-nyckeln.
    - Om du utvecklar en moln tjänst med Azure SDK 2,8 och senare fylls den här instrument ande nyckeln i automatiskt. Värdet baseras på **APPINSIGHTS_INSTRUMENTATIONKEY** tjänst konfigurations inställningen när Cloud Service-projektet paketeras. Se [använda Application Insights med Cloud Services](../app/cloudservices.md).

- Elementet **Channels** innehåller ett eller flera **kanal** element.
    - Namnattributet *avser en unik* kanal.
    - Med attributet *LogLevel* kan du ange den loggnings nivå som kanalen tillåter. De tillgängliga logg nivåerna för mest till minsta information är:
        - Verbose
        - Information
        - Varning
        - Fel
        - Kritiskt

En kanal fungerar som ett filter och gör att du kan välja specifika logg nivåer som ska skickas till mål mottagaren. Du kan till exempel samla in utförliga loggar och skicka dem till lagring, men endast skicka fel till mottagaren.

Följande bild visar den här relationen.

![Offentlig konfiguration för diagnostik](media/diagnostics-extension-to-application-insights/AzDiag_Channels_App_Insights.png)

Följande bild sammanfattar konfigurations värden och hur de fungerar. Du kan inkludera flera handfat i konfigurationen på olika nivåer i hierarkin. Mottagaren på den översta nivån fungerar som en global inställning och den som anges vid det enskilda elementet fungerar som en åsidosättning av den globala inställningen.

![Konfiguration av diagnostik handfat med Application Insights](media/diagnostics-extension-to-application-insights/Azure_Diagnostics_Sinks.png)

## <a name="complete-sink-configuration-example"></a>Slutför konfigurations exempel för mottagare
Här är ett fullständigt exempel på den offentliga konfigurations filen som
1. skickar alla fel till Application Insights (anges i noden **DiagnosticMonitorConfiguration** )
2. skickar också utförliga nivå loggar för program loggarna (anges i noden **loggar** ).

```XML
<WadCfg>
  <DiagnosticMonitorConfiguration overallQuotaInMB="4096"
       sinks="ApplicationInsights.MyTopDiagData"> <!-- All info below sent to this channel -->
    <DiagnosticInfrastructureLogs />
    <PerformanceCounters>
      <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT3M" />
      <PerformanceCounterConfiguration counterSpecifier="\Memory\Available MBytes" sampleRate="PT3M" />
    </PerformanceCounters>
    <WindowsEventLog scheduledTransferPeriod="PT1M">
      <DataSource name="Application!*" />
    </WindowsEventLog>
    <Logs scheduledTransferPeriod="PT1M" scheduledTransferLogLevelFilter="Verbose"
            sinks="ApplicationInsights.MyLogData"/> <!-- This specific info sent to this channel -->
  </DiagnosticMonitorConfiguration>

<SinksConfig>
    <Sink name="ApplicationInsights">
      <ApplicationInsights>{Insert InstrumentationKey}</ApplicationInsights>
      <Channels>
        <Channel logLevel="Error" name="MyTopDiagData"  />
        <Channel logLevel="Verbose" name="MyLogData"  />
      </Channels>
    </Sink>
  </SinksConfig>
</WadCfg>
```
```JSON
"WadCfg": {
    "DiagnosticMonitorConfiguration": {
        "overallQuotaInMB": 4096,
        "sinks": "ApplicationInsights.MyTopDiagData", "_comment": "All info below sent to this channel",
        "DiagnosticInfrastructureLogs": {
        },
        "PerformanceCounters": {
            "PerformanceCounterConfiguration": [
                {
                    "counterSpecifier": "\\Processor(_Total)\\% Processor Time",
                    "sampleRate": "PT3M"
                },
                {
                    "counterSpecifier": "\\Memory\\Available MBytes",
                    "sampleRate": "PT3M"
                }
            ]
        },
        "WindowsEventLog": {
            "scheduledTransferPeriod": "PT1M",
            "DataSource": [
                {
                    "name": "Application!*"
                }
            ]
        },
        "Logs": {
            "scheduledTransferPeriod": "PT1M",
            "scheduledTransferLogLevelFilter": "Verbose",
            "sinks": "ApplicationInsights.MyLogData", "_comment": "This specific info sent to this channel"
        }
    },
    "SinksConfig": {
        "Sink": [
            {
                "name": "ApplicationInsights",
                "ApplicationInsights": "{Insert InstrumentationKey}",
                "Channels": {
                    "Channel": [
                        {
                            "logLevel": "Error",
                            "name": "MyTopDiagData"
                        },
                        {
                            "logLevel": "Verbose",
                            "name": "MyLogData"
                        }
                    ]
                }
            }
        ]
    }
}
```
I den tidigare konfigurationen har följande rader följande betydelser:

### <a name="send-all-the-data-that-is-being-collected-by-azure-diagnostics"></a>Skicka alla data som samlas in av Azure Diagnostics

```xml
<DiagnosticMonitorConfiguration overallQuotaInMB="4096" sinks="ApplicationInsights">
```

```json
"DiagnosticMonitorConfiguration": {
    "overallQuotaInMB": 4096,
    "sinks": "ApplicationInsights",
}
```

### <a name="send-only-error-logs-to-the-application-insights-sink"></a>Skicka endast fel loggar till Application Insights-mottagaren

```xml
<DiagnosticMonitorConfiguration overallQuotaInMB="4096" sinks="ApplicationInsights.MyTopDiagdata">
```

```json
"DiagnosticMonitorConfiguration": {
    "overallQuotaInMB": 4096,
    "sinks": "ApplicationInsights.MyTopDiagData",
}
```

### <a name="send-verbose-application-logs-to-application-insights"></a>Skicka utförliga program loggar till Application Insights

```XML
<Logs scheduledTransferPeriod="PT1M" scheduledTransferLogLevelFilter="Verbose" sinks="ApplicationInsights.MyLogData"/>
```
```JSON
"DiagnosticMonitorConfiguration": {
    "overallQuotaInMB": 4096,
    "sinks": "ApplicationInsights.MyLogData",
}
```

## <a name="limitations"></a>Begränsningar

- **Kanalens logg typ och inte prestanda räknare.** Om du anger en kanal med ett prestanda räknar element, ignoreras det.
- **Logg nivån för en kanal får inte överstiga logg nivån för det som samlas in av Azure Diagnostics.** Du kan till exempel inte samla in program logg fel i avsnittet loggar och försöka skicka utförliga loggar till appens Insight-mottagare. Attributet *scheduledTransferLogLevelFilter* måste alltid samla in lika många eller fler loggar än de loggar som du försöker skicka till en mottagare.
- **Du kan inte skicka BLOB-data som samlas in av Azure Diagnostics-tillägget till Application Insights.** Till exempel vad som anges under noden *kataloger* . För krasch dum par skickas den faktiska krasch dumpningen till Blob Storage och endast ett meddelande om att kraschdumpfilen har genererats skickas till Application Insights.

## <a name="next-steps"></a>Nästa steg
* Lär dig hur du [visar Azure Diagnostics-information](../app/cloudservices.md) i Application Insights.
* Använd [PowerShell](../../cloud-services/cloud-services-diagnostics-powershell.md) för att aktivera Azure Diagnostics-tillägget för ditt program.
* Använd [Visual Studio](/visualstudio/azure/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines) för att aktivera Azure Diagnostics-tillägget för ditt program

