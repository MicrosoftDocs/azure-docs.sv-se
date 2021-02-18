---
title: Skicka data från Windows Azure Diagnostics-tillägget till Azure Event Hubs
description: Konfigurera Diagnostics extension i Azure Monitor att skicka data till Azure Event Hub så att du kan vidarebefordra det till platser utanför Azure.
ms.subservice: diagnostic-extension
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 02/18/2020
ms.openlocfilehash: 979535b1f9a237f6975908178fb1e5ed819181b0
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/17/2021
ms.locfileid: "100624154"
---
# <a name="send-data-from-windows-azure-diagnostics-extension-to-azure-event-hubs"></a>Skicka data från Windows Azure Diagnostics-tillägget till Azure Event Hubs
Tillägget Azure Diagnostics är en agent i Azure Monitor som samlar in övervaknings data från gäst operativ systemet och arbets belastningar på virtuella Azure-datorer och andra beräknings resurser. Den här artikeln beskriver hur du skickar data från WAD (Windows Azure Diagnostic Extension) till [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/) så att du kan vidarebefordra till platser utanför Azure.

## <a name="supported-data"></a>Data som stöds

De data som samlas in från gäst operativ systemet som kan skickas till Event Hubs innehåller följande. Andra data källor som samlas in av WAD, inklusive IIS-loggar och krasch dum par, kan inte skickas till Event Hubs.

* ETW-händelser (Event Tracing for Windows)
* Prestandaräknare
* Händelse loggar i Windows, inklusive program loggar i Windows-händelseloggen
* Azure Diagnostics-infrastrukturloggar

## <a name="prerequisites"></a>Förutsättningar

* Windows Diagnostics-tillägget 1,6 eller högre. Se [Azure-diagnostik tilläggs konfiguration schema versioner och historik](diagnostics-extension-versions.md) för en versions historik och [Översikt över Azure-diagnostik tillägg](diagnostics-extension-overview.md) för resurser som stöds.
* Event Hubs namn området måste alltid vara etablerad. Mer information finns i [Kom igång med Event Hubs](../../event-hubs/event-hubs-dotnet-standard-getstarted-send.md) .


## <a name="configuration-schema"></a>Konfigurations schema
Se [Installera och konfigurera Windows Azure Diagnostics Extension (wad)](diagnostics-extension-windows-install.md) för olika alternativ för att aktivera och Konfigurera diagnostik-tillägget och [Azure-diagnostik konfigurations schema](diagnostics-extension-schema-windows.md) för en referens för konfigurations schemat. Resten av den här artikeln beskriver hur du använder den här konfigurationen för att skicka data till en Event Hub. 

Azure-diagnostik skickar alltid loggar och mått till ett Azure Storage-konto. Du kan konfigurera en eller flera *data mottagare* som skickar data till ytterligare platser. Varje mottagare definieras i SinksConfig- [elementet](diagnostics-extension-schema-windows.md#sinksconfig-element) i den offentliga konfigurationen med känslig information i den privata konfigurationen. Den här konfigurationen för Event Hub använder värdena i följande tabell.

| Egenskap | Beskrivning |
|:---|:---|
| Name | Beskrivande namn för mottagaren. Används i konfigurationen för att ange vilka data källor som ska skickas till mottagaren. |
| URL  | Webb adress för händelsehubben i formatet form \<event-hubs-namespace\> . ServiceBus.Windows.net/ \<event-hub-name\> .          |
| SharedAccessKeyName | Namnet på en delad åtkomst princip för händelsehubben som har minst en **send** -auktoritet. |
| SharedAccessKey     | Primär eller sekundär nyckel från den delade åtkomst principen för händelsehubben. |

Exempel på offentliga och privata konfigurationer visas nedan. Detta är en minimal konfiguration med en enda prestanda räknare och händelse logg för att illustrera hur du konfigurerar och använder data mottagaren för Event Hub. Se [Azure-diagnostik konfigurations schema](diagnostics-extension-schema-windows.md) för ett mer komplext exempel.

### <a name="public-configuration"></a>Offentlig konfiguration

```JSON
{
    "WadCfg": {
        "DiagnosticMonitorConfiguration": {
            "overallQuotaInMB": 5120,
            "PerformanceCounters": {
                "scheduledTransferPeriod": "PT1M",
                "sinks": "myEventHub",
                "PerformanceCounterConfiguration": [
                    {
                        "counterSpecifier": "\\Processor(_Total)\\% Processor Time",
                        "sampleRate": "PT3M"
                    }
                ]
            },
            "WindowsEventLog": {
                "scheduledTransferPeriod": "PT1M",
                "sinks": "myEventHub",
                    "DataSource": [
                    {
                        "name": "Application!*[System[(Level=1 or Level=2 or Level=3)]]"
                    }
                ]
            }
        },
        "SinksConfig": {
            "Sink": [
                {
                    "name": "myEventHub",
                    "EventHub": {
                        "Url": "https://diags-mycompany-ns.servicebus.windows.net/diageventhub",
                        "SharedAccessKeyName": "SendRule"
                    }
                }
            ]
        }
    },
    "StorageAccount": "mystorageaccount",
}
```


### <a name="private-configuration"></a>Privat konfiguration

```JSON
{
    "storageAccountName": "mystorageaccount",
    "storageAccountKey": "{base64 encoded key}",
    "storageAccountEndPoint": "https://core.windows.net",
    "EventHub": {
        "Url": "https://diags-mycompany-ns.servicebus.windows.net/diageventhub",
        "SharedAccessKeyName": "SendRule",
        "SharedAccessKey": "{base64 encoded key}"
    }
}
```



## <a name="configuration-options"></a>Konfigurationsalternativ
Om du vill skicka data till en data mottagare anger du attributet **Sinks** på data källans nod. Där du placerar **Sinks** -attributet bestämmer tilldelningens omfattning. I följande exempel definieras attributet **Sinks** för **PerformanceCounters** -noden som gör att alla underordnade prestanda räknare skickas till Event Hub.

```JSON
"PerformanceCounters": {
    "scheduledTransferPeriod": "PT1M",
    "sinks": "MyEventHub",
    "PerformanceCounterConfiguration": [
        {
            "counterSpecifier": "\\Processor(_Total)\\% Processor Time",
            "sampleRate": "PT3M"
        },
        {
            "counterSpecifier": "\\Memory\\Available MBytes",
            "sampleRate": "PT3M"
        },
        {
            "counterSpecifier": "\\Web Service(_Total)\\ISAPI Extension Requests/sec",
            "sampleRate": "PT3M"
        }
    ]
}
```


I följande exempel används attributet **Sinks** direkt på tre räknare, vilket innebär att endast de prestanda räknare skickas till händelsehubben. 

```JSON
"PerformanceCounters": {
    "scheduledTransferPeriod": "PT1M",
    "PerformanceCounterConfiguration": [
        {
            "counterSpecifier": "\\Processor(_Total)\\% Processor Time",
            "sampleRate": "PT3M",
            "sinks": "MyEventHub"
        },
        {
            "counterSpecifier": "\\Memory\\Available MBytes",
            "sampleRate": "PT3M"
        },
        {
            "counterSpecifier": "\\Web Service(_Total)\\ISAPI Extension Requests/sec",
            "sampleRate": "PT3M"
        },
        {
            "counterSpecifier": "\\ASP.NET\\Requests Rejected",
            "sampleRate": "PT3M",
            "sinks": "MyEventHub"
        },
        {
            "counterSpecifier": "\\ASP.NET\\Requests Queued",
            "sampleRate": "PT3M",
            "sinks": "MyEventHub"
        }
    ]
}
```

## <a name="validating-configuration"></a>Verifierar konfiguration
Du kan använda en mängd olika metoder för att verifiera att data skickas till Event Hub. den enklaste metoden är att använda Event Hubs avbildning som beskrivs i [avbilda händelser via azure Event Hubs i azure Blob Storage eller Azure Data Lake Storage](../../event-hubs/event-hubs-capture-overview.md). 


## <a name="troubleshoot-event-hubs-sinks"></a>Felsöka Event Hubs-mottagare

- Titta på Azure Storage Table- **WADDiagnosticInfrastructureLogsTable** som innehåller loggar och fel för Azure-diagnostik. Ett alternativ är att använda ett verktyg som [Azure Storage Explorer](https://www.storageexplorer.com) för att ansluta till det här lagrings kontot, Visa den här tabellen och lägga till en fråga för timestamp under de senaste 24 timmarna. Du kan använda verktyget för att exportera en. csv-fil och öppna den i ett program, till exempel Microsoft Excel. Excel gör det enkelt att söka efter telefon korts strängar, till exempel **EventHubs**, för att se vilket fel som rapporteras.  

- Kontrol lera att händelsehubben har kon figurer ATS. All anslutnings information i **PrivateConfig** -avsnittet i konfigurationen måste matcha värdena för din resurs som visas i portalen. Se till att du har definierat en SAS-princip (*SendRule* i exemplet) i portalen och att *send* -behörighet beviljas.  

## <a name="next-steps"></a>Nästa steg

* [Översikt över Event Hubs](../../event-hubs/event-hubs-about.md)
* [Skapa en händelsehubben](../../event-hubs/event-hubs-create.md)
* [Vanliga frågor och svar om Event Hubs](../../event-hubs/event-hubs-faq.md)

<!-- Images. -->
[0]: ../../event-hubs/media/event-hubs-streaming-azure-diags-data/dashboard.png



