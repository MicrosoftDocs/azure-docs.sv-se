---
title: Beräkna kostnader för förbruknings plan i Azure Functions
description: Lär dig hur du bättre uppskattar kostnaderna som du kan stöta på när du kör din Function-app i en förbruknings plan i Azure.
ms.date: 9/20/2019
ms.topic: conceptual
ms.openlocfilehash: 430804d478df718f51ae1da9adb6693f597157a9
ms.sourcegitcommit: 2aa52d30e7b733616d6d92633436e499fbe8b069
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/06/2021
ms.locfileid: "97934891"
---
# <a name="estimating-consumption-plan-costs"></a>Uppskatta förbruknings Plans kostnader

Det finns för närvarande tre typer av värd planer för en app som körs i Azure Functions, med varje plan med en egen pris modell: 

| Planera | Beskrivning |
| ---- | ----------- |
| [**Förbrukning**](consumption-plan.md) | Du debiteras bara för den tid som din Function-App körs. Den här planen innehåller en[pris sida] för [gratis beviljande av bidrag]per prenumeration.|
| [**Premium**](functions-premium-plan.md) | Ger dig samma funktioner och skalnings funktion som förbruknings planen, men med förbättrad prestanda och VNET-åtkomst. Kostnaden baseras på din valda pris nivå. Läs mer i [Azure Functions Premium-plan](functions-premium-plan.md). |
| [**Dedikerad (App Service)**](dedicated-plan.md) <br/>(Basic-nivån eller högre) | När du behöver köra i dedikerade virtuella datorer eller isolera, använder du anpassade avbildningar eller vill använda den överskjutande App Service plan kapaciteten. Använder [regelbunden App Service plan fakturering](https://azure.microsoft.com/pricing/details/app-service/). Kostnaden baseras på din valda pris nivå.|

Du väljer den plan som bäst stöder dina prestanda-och kostnads krav. Läs mer i [Azure Functions skala och vara värd](functions-scale.md).

Den här artikeln behandlar endast förbruknings planen eftersom den här planen resulterar i rörliga kostnader. Den här artikeln ersätter artikeln [vanliga frågor och svar om förbruknings planer](https://github.com/Azure/Azure-Functions/wiki/Consumption-Plan-Cost-Billing-FAQ) .

Durable Functions kan också köras i en förbruknings plan. Mer information om kostnads överväganden när du använder Durable Functions finns i [Durable Functions fakturering](./durable/durable-functions-billing.md).

## <a name="consumption-plan-costs"></a>Kostnader för förbrukningsplan

Körnings *kostnaden* för en enskild funktions körning mäts i *GB-sekunder*. Körnings kostnaden beräknas genom att kombinera minnes användningen med dess körnings tid. En funktion som körs för längre kostnader, precis som en funktion som använder mer minne. 

Tänk på ett fall där mängden minne som används av funktionen förblir konstant. I det här fallet är beräkningen av kostnaden Enkel multiplikation. Anta till exempel att din funktion förbrukade 0,5 GB i 3 sekunder. Därefter är körnings kostnaden `0.5GB * 3s = 1.5 GB-seconds` . 

Eftersom minnes användning ändras med tiden, är beräkningen i grunden minnes användningen över tid.  Systemet utför den här beräkningen genom att sampla processens minnes användning (tillsammans med underordnade processer) med jämna mellanrum. Som vi nämnt på [sidan prissättning]avrundas minnes användningen uppåt till närmaste 128-MB-Bucket. När din process använder 160 MB debiteras du för 256 MB. Beräkningen tar hänsyn till samtidighet i kontot, vilket är flera samtidiga funktions körningar i samma process.

> [!NOTE]
> Även om CPU-användningen inte direkt beaktas vid körnings kostnader kan det påverka kostnaden när den påverkar körnings tiden för funktionen.

För en HTTP-utlöst funktion debiteras du inte för en körning om ett fel inträffar innan funktions koden börjar köras. Det innebär att 401 svar från plattformen på grund av API-nyckel validering eller App Service autentisering/auktorisering-funktionen inte räknas mot din körnings kostnad. På samma sätt räknas inte svar för 5xx status koder när de inträffar i plattformen innan en funktion bearbetar begäran. Ett 5XX-svar som genereras av plattformen efter att funktions koden har börjat köras räknas fortfarande som en körning, även om felet inte uppstår i funktions koden.

## <a name="other-related-costs"></a>Övriga relaterade kostnader

När du uppskattar den totala kostnaden för att köra dina funktioner i en plan måste du komma ihåg att funktions körningen använder flera andra Azure-tjänster, vilket faktureras separat. När du beräknar priser för Function-appar, måste alla utlösare och bindningar som integreras med andra Azure-tjänster skapa och betala för dessa ytterligare tjänster. 

För funktioner som körs i en förbruknings plan är den totala kostnaden körnings kostnaden för funktionerna samt kostnaden för bandbredd och ytterligare tjänster. 

När du uppskattar den totala kostnaden för din Function-app och relaterade tjänster använder du [pris Kalkylatorn för Azure](https://azure.microsoft.com/pricing/calculator/?service=functions). 

| Relaterad kostnad | Beskrivning |
| ------------ | ----------- |
| **Lagringskonto** | Varje Function-app kräver att du har ett associerat Generell användning [Azure Storage konto](../storage/common/storage-introduction.md#types-of-storage-accounts), som [faktureras separat](https://azure.microsoft.com/pricing/details/storage/). Det här kontot används internt av Functions-körningen, men du kan också använda det för lagrings utlösare och bindningar. Om du inte har ett lagrings konto skapas ett åt dig när Function-appen skapas. Mer information finns i [krav för lagrings konton](storage-considerations.md#storage-account-requirements).|
| **Application Insights** | Funktioner förlitar sig på [Application Insights](../azure-monitor/app/app-insights-overview.md) för att tillhandahålla en övervaknings upplevelse med hög prestanda för dina funktions appar. Om det inte behövs bör du [aktivera Application Insights-integrering](configure-monitoring.md#enable-application-insights-integration). En kostnads fri beviljande av telemetri data ingår varje månad. Mer information finns [på sidan med Azure Monitor priser](https://azure.microsoft.com/pricing/details/monitor/). |
| **Nätverksbandbredd** | Du betalar inte för data överföring mellan Azure-tjänster i samma region. Du kan dock debiteras kostnader för utgående data överföringar till en annan region eller utanför Azure. Mer information finns i [pris information för bandbredd](https://azure.microsoft.com/pricing/details/bandwidth/). |

## <a name="behaviors-affecting-execution-time"></a>Beteenden som påverkar körnings tid

Följande beteenden för dina funktioner kan påverka körnings tiden:

+ **Utlösare och bindningar**: den tid det tar att läsa indata från och skriva utdata till [funktions bindningarna](functions-triggers-bindings.md) räknas som körnings tid. Om din funktion exempelvis använder en utgående bindning för att skriva ett meddelande till en Azure Storage-kö, innehåller din körnings tid den tid det tar att skriva meddelandet till kön, som ingår i beräkningen av funktions kostnaden. 

+ **Asynkron körning**: den tid som funktionen väntar på att resultatet av en asynkron begäran ( `await` i C#) räknas som körnings tid. Beräkningen på GB-sekunden baseras på Start-och slut tiden för funktionen och minnes användningen under den perioden. Vad som händer under den tiden i förhållande till processor aktivitet är inte en faktor i beräkningen. Du kanske kan minska kostnaderna under asynkrona åtgärder med hjälp av [Durable Functions](durable/durable-functions-overview.md). Du faktureras inte för tid som ägnats åt att vänta i Orchestrator-funktioner.

## <a name="viewing-cost-related-data"></a>Visa kostnads relaterade data

I [din faktura](../cost-management-billing/understand/download-azure-invoice.md)kan du Visa kostnads relaterade data för **Totalt antal körningar – funktioner** och **körnings tid**, tillsammans med de faktiska fakturerade kostnaderna. Dessa faktura data är dock en månatlig samling för en tidigare faktura period. 

### <a name="function-app-level-metrics"></a>Function-mått på program nivå

Om du vill få bättre förståelse för dina funktioner kan du använda Azure Monitor för att visa kostnads relaterade mått som för närvarande genereras av dina funktions appar. Du kan använda antingen [Azure Monitor Metrics Explorer](../azure-monitor/platform/metrics-getting-started.md) i [Azure Portal] eller REST-API: er för att hämta dessa data.

#### <a name="monitor-metrics-explorer"></a>Övervaka Metrics Explorer

Använd [Azure Monitor Metrics Explorer](../azure-monitor/platform/metrics-getting-started.md) för att visa kostnads relaterade data för dina förbruknings Plans funktions program i ett grafiskt format. 

1. Överst i [Azure Portal] i **Sök tjänster, resurser och dokument söker du**  efter `monitor` och väljer **övervaka** under **tjänster**.

1. Till vänster väljer du **mått**  >  **Välj en resurs** och använder sedan inställningarna under bilden för att välja din Function-app.

    ![Välj din Function-app-resurs](media/functions-consumption-costing/select-a-resource.png)

      
    |Inställning  |Föreslaget värde  |Beskrivning  |
    |---------|---------|---------|
    | Prenumeration    |  Din prenumeration  | Prenumerationen med din Function-app.  |
    | Resursgrupp     | Din resursgrupp  | Resurs gruppen som innehåller din Function-app.   |
    | Resurstyp     |  App Services | Function-appar visas som App Services instanser i övervakaren. |
    | Resurs     |  Din Function-app  | Function-appen som ska övervakas.        |

1. Välj **tillämpa** för att välja din funktions-app som den resurs som ska övervakas.

1. Från **mått** väljer du **funktions körnings antal** och **Summa** för **agg regering**. Detta lägger till summan av antalet körningar under vald period i diagrammet.

    ![Definiera ett mått för functions-appen som ska läggas till i diagrammet](media/functions-consumption-costing/monitor-metrics-add-metric.png)

1. Välj **Lägg till mått** och upprepa steg 2-4 för att lägga till **funktionens körnings enheter** i diagrammet. 

Det resulterande diagrammet innehåller summan för båda körnings måtten inom det valda tidsintervallet, som i det här fallet är två timmar.

![Diagram över antal och körnings enheter för funktions körning](media/functions-consumption-costing/monitor-metrics-execution-sum.png)

Eftersom antalet körnings enheter är så mycket större än antalet körningar visar diagrammet bara körnings enheter.

Det här diagrammet visar totalt 1 110 000 000 som `Function Execution Units` använts under en period på två timmar mätt i MB-millisekunder. Om du vill konvertera till GB-sekunder dividerar du med 1024000. I det här exemplet förbrukade Function `1110000000 / 1024000 = 1083.98` -appen GB – sekunder. Du kan ta detta värde och multiplicera det med det aktuella priset för körnings tid på prissättnings sidan för [funktioner prissättning][, som]ger dig kostnaden för dessa två timmar, förutsatt att du redan har använt kostnads fria bidrag för körnings tid. 

#### <a name="azure-cli"></a>Azure CLI

[Azure CLI](/cli/azure/) innehåller kommandon för att hämta mått. Du kan använda CLI från en lokal kommando miljö eller direkt från portalen med [Azure Cloud Shell](../cloud-shell/overview.md). Följande AZ för övervakning av [mått lista](/cli/azure/monitor/metrics#az-monitor-metrics-list) returnerar till exempel Tim data över samma tids period som används tidigare.

Ersätt `<AZURE_SUBSCRIPTON_ID>` med ditt Azure-prenumerations-ID genom att köra kommandot.

```azurecli-interactive
az monitor metrics list --resource /subscriptions/<AZURE_SUBSCRIPTION_ID>/resourceGroups/metrics-testing-consumption/providers/Microsoft.Web/sites/metrics-testing-consumption --metric FunctionExecutionUnits,FunctionExecutionCount --aggregation Total --interval PT1H --start-time 2019-09-11T21:46:00Z --end-time 2019-09-11T23:18:00Z
```

Det här kommandot returnerar en JSON-nyttolast som ser ut som i följande exempel:

```json
{
  "cost": 0.0,
  "interval": "1:00:00",
  "namespace": "Microsoft.Web/sites",
  "resourceregion": "centralus",
  "timespan": "2019-09-11T21:46:00Z/2019-09-11T23:18:00Z",
  "value": [
    {
      "id": "/subscriptions/XXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXX/resourceGroups/metrics-testing-consumption/providers/Microsoft.Web/sites/metrics-testing-consumption/providers/Microsoft.Insights/metrics/FunctionExecutionUnits",
      "name": {
        "localizedValue": "Function Execution Units",
        "value": "FunctionExecutionUnits"
      },
      "resourceGroup": "metrics-testing-consumption",
      "timeseries": [
        {
          "data": [
            {
              "average": null,
              "count": null,
              "maximum": null,
              "minimum": null,
              "timeStamp": "2019-09-11T21:46:00+00:00",
              "total": 793294592.0
            },
            {
              "average": null,
              "count": null,
              "maximum": null,
              "minimum": null,
              "timeStamp": "2019-09-11T22:46:00+00:00",
              "total": 316576256.0
            }
          ],
          "metadatavalues": []
        }
      ],
      "type": "Microsoft.Insights/metrics",
      "unit": "Count"
    },
    {
      "id": "/subscriptions/XXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXX/resourceGroups/metrics-testing-consumption/providers/Microsoft.Web/sites/metrics-testing-consumption/providers/Microsoft.Insights/metrics/FunctionExecutionCount",
      "name": {
        "localizedValue": "Function Execution Count",
        "value": "FunctionExecutionCount"
      },
      "resourceGroup": "metrics-testing-consumption",
      "timeseries": [
        {
          "data": [
            {
              "average": null,
              "count": null,
              "maximum": null,
              "minimum": null,
              "timeStamp": "2019-09-11T21:46:00+00:00",
              "total": 33538.0
            },
            {
              "average": null,
              "count": null,
              "maximum": null,
              "minimum": null,
              "timeStamp": "2019-09-11T22:46:00+00:00",
              "total": 13040.0
            }
          ],
          "metadatavalues": []
        }
      ],
      "type": "Microsoft.Insights/metrics",
      "unit": "Count"
    }
  ]
}
```
Detta specifika svar visar att `2019-09-11T21:46` `2019-09-11T23:18` appen förbrukade 1110000000 MB-millisekunder (1083,98 GB-sekunder).

### <a name="function-level-metrics"></a>Mått på funktions nivå

Funktions körnings enheter är en kombination av körnings tid och minnes användning, vilket gör det svårt att förstå minnes användningen. Minnes data är inte ett mått som för närvarande är tillgängligt via Azure Monitor. Men om du vill optimera minnes användningen för din app kan använda prestanda räknar data som samlas in av Application Insights.  

Om du inte redan har gjort det [aktiverar du Application Insights i din Function-app](configure-monitoring.md#enable-application-insights-integration). Med den här integreringen aktive rad kan du [fråga dessa telemetridata i portalen](analyze-telemetry-data.md#query-telemetry-data). 

[!INCLUDE [functions-consumption-metrics-queries](../../includes/functions-consumption-metrics-queries.md)]

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Lär dig mer om att övervaka Function-appar](functions-monitoring.md)

[Sidan prissättning]:https://azure.microsoft.com/pricing/details/functions/
[Azure-portalen]: https://portal.azure.com
