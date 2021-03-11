---
title: Azure-resursloggar
description: Lär dig hur du direktuppspelar Azures resurs loggar till en Log Analytics arbets yta i Azure Monitor.
author: bwren
services: azure-monitor
ms.topic: conceptual
ms.date: 07/17/2019
ms.author: bwren
ms.openlocfilehash: 2435e4ed16889d9d4701b6047c0a1f602ee7ae91
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/10/2021
ms.locfileid: "102558703"
---
# <a name="azure-resource-logs"></a>Azure-resursloggar
Azures resurs loggar är [plattforms loggar](../essentials/platform-logs-overview.md) som ger inblick i åtgärder som utförts i en Azure-resurs. Innehållet i resurs loggar varierar beroende på Azure-tjänsten och resurs typen. Resurs loggar samlas inte in som standard. Du måste skapa en diagnostisk inställning för varje Azure-resurs för att skicka resurs loggarna till en Log Analytics arbets yta som ska användas med [Azure Monitor loggar](../logs/data-platform-logs.md), Azure Event Hubs som ska vidarebefordras utanför Azure, eller för att Azure Storage arkivering.

Se [skapa diagnostikinställningar för att skicka plattforms loggar och mått till olika destinationer](../essentials/diagnostic-settings.md) för information om hur du skapar en diagnostisk inställning och [distribuerar Azure Monitor i skala med Azure policy](../deploy-scale.md) för information om hur du använder Azure policy för att automatiskt skapa en diagnostisk inställning för varje Azure-resurs som du skapar.

## <a name="send-to-log-analytics-workspace"></a>Skicka till Log Analytics-arbetsytan
 Skicka resurs loggar till en Log Analytics-arbetsyta för att aktivera funktionerna i [Azure Monitor loggar](../logs/data-platform-logs.md) som innehåller följande:

- Korrelera resurs logg data med andra övervaknings data som samlas in av Azure Monitor.
- Konsolidera logg poster från flera Azure-resurser, prenumerationer och klienter till en plats för analys.
- Använd logg frågor för att utföra komplexa analyser och få djupgående insikter om loggdata.
- Använd logg aviseringar med komplex aviserings logik.

[Skapa en diagnostisk inställning](../essentials/diagnostic-settings.md) för att skicka resurs loggar till en Log Analytics-arbetsyta. Dessa data lagras i tabeller enligt beskrivningen i [strukturen i Azure Monitor loggar](../logs/data-platform-logs.md). Tabellerna som används av resurs loggar beror på vilken typ av samling resursen använder:

- Azure Diagnostics – alla data som skrivs är till [AzureDiagnostics](/azure/azure-monitor/reference/tables/azurediagnostics) -tabellen.
- Resurs specifika data skrivs till en enskild tabell för varje resurs kategori.

### <a name="azure-diagnostics-mode"></a>Azure Diagnostics-läge 
I det här läget samlas alla data från alla diagnostiska inställningar i [AzureDiagnostics](/azure/azure-monitor/reference/tables/azurediagnostics) -tabellen. Detta är den äldre metoden som används idag av de flesta Azure-tjänster. Eftersom flera resurs typer skickar data till samma tabell, är schemats supermängd till schemana för alla olika data typer som samlas in. Se [AzureDiagnostics-referens](/azure/azure-monitor/reference/tables/azurediagnostics) för information om tabell strukturen och hur den fungerar med det här potentiellt stora antalet kolumner.

Tänk på följande exempel där diagnostikinställningar samlas in i samma arbets yta för följande data typer:

- Gransknings loggar för tjänst 1 (med ett schema som består av kolumnerna A, B och C)  
- Fel loggarna för tjänsten 1 (med ett schema bestående av kolumnerna D, E och F)  
- Gransknings loggar för tjänst 2 (med ett schema som består av kolumnerna G, H och I)  

AzureDiagnostics-tabellen ser ut så här:  

| ResourceProvider    | Kategori     | A  | B  | C  | D  | E  | F  | G  | H  | I  |
| -- | -- | -- | -- | -- | -- | -- | -- | -- | -- | -- |
| Microsoft. Service1 | AuditLogs    | PCIe | Y1 | z1 |    |    |    |    |    |    |
| Microsoft. Service1 | ErrorLogs    |    |    |    | bästa | W1 | E1 |    |    |    |
| Microsoft. service2 | AuditLogs    |    |    |    |    |    |    | j1 | K1 | L1 |
| Microsoft. Service1 | ErrorLogs    |    |    |    | Q2 | w2 | E2 |    |    |    |
| Microsoft. service2 | AuditLogs    |    |    |    |    |    |    | j3 | k3 | L3 |
| Microsoft. Service1 | AuditLogs    | GPS | y5 värdefält | z5 |    |    |    |    |    |    |
| ... |

### <a name="resource-specific"></a>Resurs-/regionsspecifika
I det här läget skapas enskilda tabeller i den valda arbets ytan för varje kategori som väljs i den diagnostiska inställningen. Den här metoden rekommenderas eftersom det gör det mycket enklare att arbeta med data i logg frågor, vilket ger bättre identifiering av scheman och deras struktur, förbättrar prestandan för både svars tid och fråge tider samt möjligheten att bevilja Azure RBAC-rättigheter i en speciell tabell. Alla Azure-tjänster kommer slutligen att migreras till Resource-Specific läge. 

Exemplet ovan skulle resultera i att tre tabeller skapas:
 
- Tabell *Service1AuditLogs* enligt följande:

    | Resurs leverantör | Kategori | A | B | C |
    | -- | -- | -- | -- | -- |
    | Service1 | AuditLogs | PCIe | Y1 | z1 |
    | Service1 | AuditLogs | GPS | y5 värdefält | z5 |
    | ... |

- Tabell *Service1ErrorLogs* enligt följande:  

    | Resurs leverantör | Kategori | D | E | F |
    | -- | -- | -- | -- | -- | 
    | Service1 | ErrorLogs |  bästa | W1 | E1 |
    | Service1 | ErrorLogs |  Q2 | w2 | E2 |
    | ... |

- Tabell *Service2AuditLogs* enligt följande:  

    | Resurs leverantör | Kategori | G | H | I |
    | -- | -- | -- | -- | -- |
    | Service2 | AuditLogs | j1 | K1 | L1|
    | Service2 | AuditLogs | j3 | k3 | L3|
    | ... |



### <a name="select-the-collection-mode"></a>Välj samlings läge
De flesta Azure-resurser kommer att skriva data till arbets ytan i **Azure-diagnostik** eller **resurs särskilt läge** utan att du behöver välja något. I [dokumentationen för varje tjänst](./resource-logs-schema.md) finns information om vilket läge den använder. Alla Azure-tjänster kommer att använda Resource-Specific läge. Som en del av den här över gången kan vissa resurser välja ett läge i den diagnostiska inställningen. Ange det resursbaserade läget för alla nya diagnostikinställningar eftersom det gör det enklare att hantera data och kan hjälpa dig att undvika komplexa migreringar vid ett senare tillfälle.
  
   ![Läges väljare för diagnostiska inställningar](media/resource-logs/diagnostic-settings-mode-selector.png)

> [!NOTE]
> Ett exempel på hur du anger samlings läget med en Resource Manager-mall finns i [exempel på Resource Manager-mallar för diagnostikinställningar i Azure Monitor](./resource-manager-diagnostic-settings.md#diagnostic-setting-for-recovery-services-vault).


Du kan ändra en befintlig diagnostisk inställning till ett resurs speciellt läge. I det här fallet finns data som redan har samlats in kvar i _AzureDiagnostics_ -tabellen tills den tas bort enligt inställningen för kvarhållning för arbets ytan. Nya data samlas in i den dedikerade tabellen. Använd [union](/azure/kusto/query/unionoperator) -operatorn för att fråga efter data i båda tabellerna.

Fortsätt att titta på [Azures uppdaterings](https://azure.microsoft.com/updates/) blogg för meddelanden om Azure-tjänster som stöder Resource-Specific läge.


## <a name="send-to-azure-event-hubs"></a>Skicka till Azure Event Hubs
Skicka resurs loggar till en händelsehubben för att skicka dem utanför Azure, till exempel en SIEM eller andra Log Analytics-lösningar. Resurs loggar från Event Hub används i JSON-format med ett `records` element som innehåller posterna i varje nytto Last. Schemat är beroende av resurs typen enligt beskrivningen i [gemensamt och tjänstspecifikt schema för Azure-resurs loggar](resource-logs-schema.md).

Följande är exempel på utdata från Event Hubs för en resurs logg:

```json
{
    "records": [
        {
            "time": "2019-07-15T18:00:22.6235064Z",
            "workflowId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-000000000000/RESOURCEGROUPS/JOHNKEMTEST/PROVIDERS/MICROSOFT.LOGIC/WORKFLOWS/JOHNKEMTESTLA",
            "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-000000000000/RESOURCEGROUPS/JOHNKEMTEST/PROVIDERS/MICROSOFT.LOGIC/WORKFLOWS/JOHNKEMTESTLA/RUNS/08587330013509921957/ACTIONS/SEND_EMAIL",
            "category": "WorkflowRuntime",
            "level": "Error",
            "operationName": "Microsoft.Logic/workflows/workflowActionCompleted",
            "properties": {
                "$schema": "2016-04-01-preview",
                "startTime": "2016-07-15T17:58:55.048482Z",
                "endTime": "2016-07-15T18:00:22.4109204Z",
                "status": "Failed",
                "code": "BadGateway",
                "resource": {
                    "subscriptionId": "00000000-0000-0000-0000-000000000000",
                    "resourceGroupName": "JohnKemTest",
                    "workflowId": "243aac67fe904cf195d4a28297803785",
                    "workflowName": "JohnKemTestLA",
                    "runId": "08587330013509921957",
                    "location": "westus",
                    "actionName": "Send_email"
                },
                "correlation": {
                    "actionTrackingId": "29a9862f-969b-4c70-90c4-dfbdc814e413",
                    "clientTrackingId": "08587330013509921958"
                }
            }
        },
        {
            "time": "2019-07-15T18:01:15.7532989Z",
            "workflowId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-000000000000/RESOURCEGROUPS/JOHNKEMTEST/PROVIDERS/MICROSOFT.LOGIC/WORKFLOWS/JOHNKEMTESTLA",
            "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-000000000000/RESOURCEGROUPS/JOHNKEMTEST/PROVIDERS/MICROSOFT.LOGIC/WORKFLOWS/JOHNKEMTESTLA/RUNS/08587330012106702630/ACTIONS/SEND_EMAIL",
            "category": "WorkflowRuntime",
            "level": "Information",
            "operationName": "Microsoft.Logic/workflows/workflowActionStarted",
            "properties": {
                "$schema": "2016-04-01-preview",
                "startTime": "2016-07-15T18:01:15.5828115Z",
                "status": "Running",
                "resource": {
                    "subscriptionId": "00000000-0000-0000-0000-000000000000",
                    "resourceGroupName": "JohnKemTest",
                    "workflowId": "243aac67fe904cf195d4a28297803785",
                    "workflowName": "JohnKemTestLA",
                    "runId": "08587330012106702630",
                    "location": "westus",
                    "actionName": "Send_email"
                },
                "correlation": {
                    "actionTrackingId": "042fb72c-7bd4-439e-89eb-3cf4409d429e",
                    "clientTrackingId": "08587330012106702632"
                }
            }
        }
    ]
}
```

## <a name="send-to-azure-storage"></a>Skicka till Azure Storage
Skicka resurs loggar till Azure Storage för att spara den för arkivering. När du har skapat den diagnostiska inställningen skapas en lagrings behållare i lagrings kontot så snart en händelse inträffar i en av de aktiverade logg kategorierna. Blobarna i behållaren använder följande namngivnings konvention:

```
insights-logs-{log category name}/resourceId=/SUBSCRIPTIONS/{subscription ID}/RESOURCEGROUPS/{resource group name}/PROVIDERS/{resource provider name}/{resource type}/{resource name}/y={four-digit numeric year}/m={two-digit numeric month}/d={two-digit numeric day}/h={two-digit 24-hour clock hour}/m=00/PT1H.json
```

Till exempel kan blobben för en nätverks säkerhets grupp ha ett namn som liknar följande:

```
insights-logs-networksecuritygrouprulecounter/resourceId=/SUBSCRIPTIONS/00000000-0000-0000-0000-000000000000/RESOURCEGROUPS/TESTRESOURCEGROUP/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUP/TESTNSG/y=2016/m=08/d=22/h=18/m=00/PT1H.json
```

Varje PT1H.json-blob innehåller en JSON-blob med händelser som inträffade inom den angivna timmen i blob-URL (till exempel h=12). Under den aktuella timmen läggs händelser till i filen PT1H.json allt eftersom de inträffar. Minut värdet (m = 00) är alltid 00, eftersom resurs logg händelser delas upp i enskilda blobbar per timme.

I PT1H.jsfilen lagras varje händelse med följande format. Detta använder ett gemensamt schema på den översta nivån, men är unikt för varje Azure-tjänst enligt beskrivningen i [resurs loggar schema](./resource-logs-schema.md).

``` JSON
{"time": "2016-07-01T00:00:37.2040000Z","systemId": "46cdbb41-cb9c-4f3d-a5b4-1d458d827ff1","category": "NetworkSecurityGroupRuleCounter","resourceId": "/SUBSCRIPTIONS/s1id1234-5679-0123-4567-890123456789/RESOURCEGROUPS/TESTRESOURCEGROUP/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/TESTNSG","operationName": "NetworkSecurityGroupCounters","properties": {"vnetResourceGuid": "{12345678-9012-3456-7890-123456789012}","subnetPrefix": "10.3.0.0/24","macAddress": "000123456789","ruleName": "/subscriptions/ s1id1234-5679-0123-4567-890123456789/resourceGroups/testresourcegroup/providers/Microsoft.Network/networkSecurityGroups/testnsg/securityRules/default-allow-rdp","direction": "In","type": "allow","matchedConnections": 1988}}
```

> [!NOTE]
> Plattforms loggar skrivs till blob-lagring med hjälp av [JSON-linjer](http://jsonlines.org/), där varje händelse är en rad och rad matnings tecknet indikerar en ny händelse. Det här formatet implementerades i november 2018. Innan detta datum skrevs loggarna till Blob Storage som en JSON-matris med poster enligt beskrivningen i [förbereda för format ändring till Azure Monitor plattforms loggar som arkiverats på ett lagrings konto](resource-logs-blob-format.md).


## <a name="next-steps"></a>Nästa steg

* [Läs mer om resurs loggar](../essentials/platform-logs-overview.md).
* [Skapa diagnostikinställningar för att skicka plattforms loggar och mått till olika mål](../essentials/diagnostic-settings.md).
