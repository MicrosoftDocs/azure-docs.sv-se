---
title: Data insamlings regler i Azure Monitor (för hands version)
description: Översikt över data insamlings regler (DCRs) i Azure Monitor inklusive innehåll och struktur och hur du kan skapa och arbeta med dem.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 01/19/2021
ms.openlocfilehash: a0c5e9f89b983871224e79c2fc4f518a15d42a6f
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/04/2021
ms.locfileid: "102039622"
---
# <a name="data-collection-rules-in-azure-monitor-preview"></a>Data insamlings regler i Azure Monitor (för hands version)
Data insamlings regler (DCR) definierar data som kommer till Azure Monitor och anger var data ska skickas eller lagras. Den här artikeln innehåller en översikt över data insamlings regler, inklusive deras innehåll och struktur och hur du kan skapa och arbeta med dem.

## <a name="input-sources"></a>Inmatade källor
Data insamlings regler stöder för närvarande följande inmatnings källor:

- Virtuell Azure-dator med Azure Monitor agenten. Se [Konfigurera data insamling för Azure Monitor agenten (för hands version)](../agents/data-collection-rule-azure-monitor-agent.md).



## <a name="components-of-a-data-collection-rule"></a>Komponenter i en data insamlings regel
En data insamlings regel innehåller följande komponenter.

| Komponent | Beskrivning |
|:---|:---|
| Datakällor | Unik källa för övervakning av data med eget format och metod för att exponera data. Exempel på en data källa är Windows-händelseloggen, prestanda räknare och syslog. Varje data källa matchar en viss typ av data källa enligt beskrivningen nedan. |
| Strömmar | Unik referens som beskriver en uppsättning data källor som omvandlas och schematiserade som en typ. Varje data källa kräver en eller flera strömmar, och en data ström kan användas av flera data källor. Alla data källor i en data ström delar ett gemensamt schema. Använd flera strömmar till exempel när du vill skicka en viss data källa till flera tabeller i samma Log Analytics-arbetsyta. |
| Mål | En uppsättning destinationer där data ska skickas. Exempel på detta är Log Analytics arbets yta, Azure Monitor mått och Azure-Event Hubs. | 
| Dataflöden | Definition av vilka strömmar som ska skickas till vilka mål. | 

Följande diagram visar komponenterna i en data insamlings regel och deras relation

[![Diagram över DCR](media/data-collection-rule-overview/data-collection-rule-components.png)](media/data-collection-rule-overview/data-collection-rule-components.png#lightbox)

### <a name="data-source-types"></a>Typer av datakälla
Varje data källa har en typ av data källa. Varje typ definierar en unik uppsättning egenskaper som måste anges för varje data källa. De data käll typer som är tillgängliga visas i följande tabell.

| Typ av data Källa | Beskrivning | 
|:---|:---|
| utöka | VM-baserad data Källa |
| performanceCounters | Prestanda räknare för både Windows och Linux |
| syslog | Syslog-händelser på Linux |
| windowsEventLogs | Händelse loggen i Windows |


## <a name="limits"></a>Gränser
Begränsningar som gäller för varje data insamlings regel finns i [Azure Monitor tjänst begränsningar](../service-limits.md#data-collection-rules).


## <a name="create-a-dcr"></a>Skapa en DCR
Du kan för närvarande använda någon av följande metoder för att skapa en DCR:

- [Använd Azure Portal](../agents/data-collection-rule-azure-monitor-agent.md) för att skapa en data insamlings regel och associera den med en eller flera virtuella datorer.
- Redigera data insamlings regeln direkt i JSON och [skicka med REST API](/rest/api/monitor/datacollectionrules).
- Skapa DCR och associationer med [Azure CLI](https://github.com/Azure/azure-cli-extensions/blob/master/src/monitor-control-service/README.md).
- Skapa DCR och associationer med Azure PowerShell.
  - [Get-AzDataCollectionRule](https://github.com/Azure/azure-powershell/blob/master/src/Monitor/Monitor/help/Get-AzDataCollectionRule.md)
  - [New-AzDataCollectionRule](https://github.com/Azure/azure-powershell/blob/master/src/Monitor/Monitor/help/New-AzDataCollectionRule.md)
  - [Set-AzDataCollectionRule](https://github.com/Azure/azure-powershell/blob/master/src/Monitor/Monitor/help/Set-AzDataCollectionRule.md)
  - [Uppdatera – AzDataCollectionRule](https://github.com/Azure/azure-powershell/blob/master/src/Monitor/Monitor/help/Update-AzDataCollectionRule.md)
  - [Remove-AzDataCollectionRule](https://github.com/Azure/azure-powershell/blob/master/src/Monitor/Monitor/help/Remove-AzDataCollectionRule.md)
  - [Get-AzDataCollectionRuleAssociation](https://github.com/Azure/azure-powershell/blob/master/src/Monitor/Monitor/help/Get-AzDataCollectionRuleAssociation.md)
  - [New-AzDataCollectionRuleAssociation](https://github.com/Azure/azure-powershell/blob/master/src/Monitor/Monitor/help/New-AzDataCollectionRuleAssociation.md)
  - [Remove-AzDataCollectionRuleAssociation](https://github.com/Azure/azure-powershell/blob/master/src/Monitor/Monitor/help/Remove-AzDataCollectionRuleAssociation.md)

## <a name="sample-data-collection-rule"></a>Exempel på data insamlings regel
Exempel data insamlings regeln nedan är för virtuella datorer med Azures hanterings agent och har följande information:

- Prestandadata
  - Samlar in vissa räknare för processor, minne, logisk disk och fysisk disk var 15: e sekund och laddar upp varje minut.
  - Samlar in vissa process räknare var 30: e sekund och laddar upp var femte minut.
- Windows-händelser
  - Samlar in säkerhets händelser i Windows och laddar upp varje minut.
  - Samlar in Windows-program och system händelser och laddar upp var femte minut.
- Syslog
  - Samlar in fel söknings-, kritiska-och nöd händelser från cron-anläggningen.
  - Samlar in aviseringar, kritiska händelser och nöd händelser från syslog-anläggningen.
- Mål
  - Skickar alla data till en Log Analytics arbets yta med namnet centralWorkspace.

```json
{
    "location": "eastus",
    "properties": {
      "dataSources": {
        "performanceCounters": [
          {
            "name": "cloudTeamCoreCounters",
            "streams": [
              "Microsoft-Perf"
            ],
            "scheduledTransferPeriod": "PT1M",
            "samplingFrequencyInSeconds": 15,
            "counterSpecifiers": [
              "\\Processor(_Total)\\% Processor Time",
              "\\Memory\\Committed Bytes",
              "\\LogicalDisk(_Total)\\Free Megabytes",
              "\\PhysicalDisk(_Total)\\Avg. Disk Queue Length"
            ]
          },
          {
            "name": "appTeamExtraCounters",
            "streams": [
              "Microsoft-Perf"
            ],
            "scheduledTransferPeriod": "PT5M",
            "samplingFrequencyInSeconds": 30,
            "counterSpecifiers": [
              "\\Process(_Total)\\Thread Count"
            ]
          }
        ],
        "windowsEventLogs": [
          {
            "name": "cloudSecurityTeamEvents",
            "streams": [
              "Microsoft-Event"
            ],
            "scheduledTransferPeriod": "PT1M",
            "xPathQueries": [
              "Security!*"
            ]
          },
          {
            "name": "appTeam1AppEvents",
            "streams": [
              "Microsoft-Event"
            ],
            "scheduledTransferPeriod": "PT5M",
            "xPathQueries": [
              "System!*[System[(Level = 1 or Level = 2 or Level = 3)]]",
              "Application!*[System[(Level = 1 or Level = 2 or Level = 3)]]"
            ]
          }
        ],
        "syslog": [
          {
            "name": "cronSyslog",
            "streams": [
              "Microsoft-Syslog"
            ],
            "facilityNames": [
              "cron"
            ],
            "logLevels": [
              "Debug",
              "Critical",
              "Emergency"
            ]
          },
          {
            "name": "syslogBase",
            "streams": [
              "Microsoft-Syslog"
            ],
            "facilityNames": [
              "syslog"
            ],
            "logLevels": [
              "Alert",
              "Critical",
              "Emergency"
            ]
          }
        ]
      },
      "destinations": {
        "logAnalytics": [
          {
            "workspaceResourceId": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/my-resource-group/providers/Microsoft.OperationalInsights/workspaces/my-workspace",
            "name": "centralWorkspace"
          }
        ]
      },
      "dataFlows": [
        {
          "streams": [
            "Microsoft-Perf",
            "Microsoft-Syslog",
            "Microsoft-Event"
          ],
          "destinations": [
            "centralWorkspace"
          ]
        }
      ]
    }
  }
```


## <a name="next-steps"></a>Nästa steg

- [Skapa en data insamlings regel](data-collection-rule-azure-monitor-agent.md) och en association till den från en virtuell dator med hjälp av Azure Monitor agenten.
