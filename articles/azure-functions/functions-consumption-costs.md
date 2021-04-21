---
title: Beräkna kostnader för förbrukningsplan i Azure Functions
description: Lär dig hur du bättre beräknar de kostnader som kan uppstå när du kör funktionsappen i en förbrukningsplan i Azure.
ms.date: 9/20/2019
ms.topic: conceptual
ms.openlocfilehash: 648be6325cce5bad36795b113c8bbccb3e21d37b
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107774011"
---
# <a name="estimating-consumption-plan-costs"></a>Beräkna kostnader för förbrukningsplan

Det finns för närvarande tre typer av värdplaner för en app som körs i Azure Functions, där varje plan har en egen prismodell: 

| Planera | Description |
| ---- | ----------- |
| [**Förbrukning**](consumption-plan.md) | Du debiteras bara för den tid som funktionsappen körs. Den här planen innehåller [en sida][med kostnadsfria priser] för beviljande per prenumeration.|
| [**Premium**](functions-premium-plan.md) | Ger dig samma funktioner och skalningsmekanismer som förbrukningsplanen, men med bättre prestanda och VNET-åtkomst. Kostnaden baseras på din valda prisnivå. Mer information finns i [Azure Functions Premium-plan](functions-premium-plan.md). |
| [**Dedikerad (App Service)**](dedicated-plan.md) <br/>(basic-nivå eller högre) | När du behöver köra i dedikerade virtuella datorer eller isolerat använder du anpassade avbildningar eller vill använda din överflödiga App Service plan kapacitet. Använder [vanlig App Service plan fakturering.](https://azure.microsoft.com/pricing/details/app-service/) Kostnaden baseras på din valda prisnivå.|

Du har valt den plan som bäst stöder dina krav på funktionsprestanda och kostnader. Mer information finns i Azure Functions [skala och vara värd för](functions-scale.md).

Den här artikeln behandlar endast förbrukningsplanen, eftersom den här planen resulterar i varierande kostnader. Den här artikeln ersätter artikeln Med vanliga frågor [och svar om kostnadsfakturering för förbrukningsplan.](https://github.com/Azure/Azure-Functions/wiki/Consumption-Plan-Cost-Billing-FAQ)

Durable Functions kan också köras i en förbrukningsplan. Mer information om kostnadsöverväganden när du använder Durable Functions finns i [Durable Functions fakturering.](./durable/durable-functions-billing.md)

## <a name="consumption-plan-costs"></a>Kostnader för förbrukningsplan

*Körningskostnaden* för en enskild funktionskörning mäts i *GB-sekunder.* Körningskostnaden beräknas genom att kombinera minnesanvändningen med körningstiden. En funktion som körs längre kostar mer, precis som en funktion som förbrukar mer minne. 

Tänk dig ett fall där mängden minne som används av funktionen förblir konstant. I det här fallet är beräkningen av kostnaden enkel multiplikation. Säg till exempel att funktionen förbrukade 0,5 GB i 3 sekunder. Då är körningskostnaden `0.5GB * 3s = 1.5 GB-seconds` . 

Eftersom minnesanvändningen ändras över tid är beräkningen i stort sett den integrerade minnesanvändningen över tid.  Systemet gör den här beräkningen genom att sampling av processens minnesanvändning (tillsammans med underordnade processer) med jämna mellanrum. Som vi nämnde på [prissättningssidan]avrundas minnesanvändningen uppåt till närmaste bucket på 128 MB. När din process använder 160 MB debiteras du för 256 MB. Beräkningen tar hänsyn till samtidighet, vilket är flera samtidiga funktionskörningar i samma process.

> [!NOTE]
> Processoranvändningen beaktas inte direkt i körningskostnaden, men den kan påverka kostnaden när den påverkar körningstiden för funktionen.

När ett fel inträffar innan funktionskoden börjar köras för en HTTP-utlöst funktion debiteras du inte för en körning. Det innebär att 401-svar från plattformen på grund av API-nyckelvalidering eller funktionen App Service-autentisering/auktorisering inte räknas mot din körningskostnad. På samma sätt räknas inte 5xx-statuskodsvar när de inträffar på plattformen innan en funktion bearbetar begäran. Ett 5xx-svar som genereras av plattformen när funktionskoden har börjat köras räknas fortfarande som en körning, även om felet inte utlöses av funktionskoden.

## <a name="other-related-costs"></a>Övriga relaterade kostnader

När du beräknar den totala kostnaden för att köra dina funktioner i en plan bör du komma ihåg att Functions-körningen använder flera andra Azure-tjänster, som var och en faktureras separat. När du beräknar prissättningen för funktionsappar kräver alla utlösare och bindningar som du har som integrerar med andra Azure-tjänster att du skapar och betalar för dessa ytterligare tjänster. 

För funktioner som körs i en förbrukningsplan är den totala kostnaden körningskostnaden för dina funktioner, plus kostnaden för bandbredd och ytterligare tjänster. 

När du beräknar de totala kostnaderna för din funktionsapp och relaterade tjänster använder du [priskalkylatorn för Azure.](https://azure.microsoft.com/pricing/calculator/?service=functions) 

| Relaterad kostnad | Beskrivning |
| ------------ | ----------- |
| **Lagringskonto** | Varje funktionsapp kräver att du har ett Generell användning [Azure Storage konto](../storage/common/storage-introduction.md#types-of-storage-accounts), [som faktureras separat](https://azure.microsoft.com/pricing/details/storage/). Det här kontot används internt av Functions-körningen, men du kan också använda det för Storage-utlösare och bindningar. Om du inte har något lagringskonto skapas ett åt dig när funktionsappen skapas. Mer information finns i Krav [för lagringskonto.](storage-considerations.md#storage-account-requirements)|
| **Application Insights** | Functions är beroende [Application Insights för](../azure-monitor/app/app-insights-overview.md) att tillhandahålla en övervakningsupplevelse med höga prestanda för dina funktionsappar. Även om det inte krävs bör [du aktivera Application Insights integrering](configure-monitoring.md#enable-application-insights-integration). Ett kostnadsfritt beviljande av telemetridata ingår varje månad. Mer information finns på [Azure Monitor prissättningssidan.](https://azure.microsoft.com/pricing/details/monitor/) |
| **Nätverksbandbredd** | Du betalar inte för dataöverföring mellan Azure-tjänster i samma region. Du kan dock medföra kostnader för utgående dataöverföringar till en annan region eller utanför Azure. Mer information finns i [Prisinformation för bandbredd.](https://azure.microsoft.com/pricing/details/bandwidth/) |

## <a name="behaviors-affecting-execution-time"></a>Beteenden som påverkar körningstiden

Följande funktionsbeteenden kan påverka körningstiden:

+ **Utlösare och bindningar:** Den tid det tar att läsa indata från och skriva utdata till dina [funktionsbindningar räknas](functions-triggers-bindings.md) som körningstid. När din funktion till exempel använder en utdatabindning för att skriva ett meddelande till en Azure Storage-kö inkluderar körningstiden den tid det tar att skriva meddelandet till kön, vilket ingår i beräkningen av funktionskostnaden. 

+ **Asynkron körning:** Den tid då funktionen väntar på resultatet av en asynkron begäran ( i `await` C#) räknas som körningstid. Beräkningen på GB-sekund baseras på start- och sluttiden för funktionen och minnesanvändningen under den perioden. Vad som händer under den tiden vad gäller CPU-aktivitet räknas inte in i beräkningen. Du kan kanske minska kostnaderna under asynkrona åtgärder genom att använda [Durable Functions](durable/durable-functions-overview.md). Du debiteras inte för tid som spenderas på awaits i Orchestrator Functions.

## <a name="viewing-cost-related-data"></a>Visa kostnadsrelaterade data

På [din faktura](../cost-management-billing/understand/download-azure-invoice.md)kan du visa kostnadsrelaterade data för Totala **körningar – Funktioner** och **Körningstid – Funktioner**, tillsammans med de faktiska fakturerade kostnaderna. Dessa fakturadata är dock ett månatligt aggregat för en tidigare fakturaperiod. 

### <a name="function-app-level-metrics"></a>Mått på funktionsappnivå

För att bättre förstå kostnadspåverkan för dina funktioner kan du använda Azure Monitor för att visa kostnadsrelaterade mått som för närvarande genereras av dina funktionsappar. Du kan använda [antingen Azure Monitor Metrics Explorer](../azure-monitor/essentials/metrics-getting-started.md) i [Azure Portal-] eller REST-API:er för att hämta dessa data.

#### <a name="monitor-metrics-explorer"></a>Övervaka Metrics Explorer

Använd [Azure Monitor Metrics Explorer för](../azure-monitor/essentials/metrics-getting-started.md) att visa kostnadsrelaterade data för dina förbrukningsplanfunktionsappar i ett grafiskt format. 

1. Längst upp i Azure Portal [sök] **efter söktjänster, resurser** och dokument och `monitor` välj **Övervaka** under **Tjänster.**

1. Till vänster väljer du **Mått Välj** en resurs  >  **och använder** sedan inställningarna under bilden för att välja din funktionsapp.

    ![Välj resurs för funktionsappen](media/functions-consumption-costing/select-a-resource.png)

      
    |Inställning  |Föreslaget värde  |Beskrivning  |
    |---------|---------|---------|
    | Prenumeration    |  Din prenumeration  | Prenumerationen med din funktionsapp.  |
    | Resursgrupp     | Din resursgrupp  | Den resursgrupp som innehåller funktionsappen.   |
    | Resurstyp     |  App Services | Funktionsappar visas som App Services instanser i Monitor. |
    | Resurs     |  Din funktionsapp  | Funktionsappen som ska övervakas.        |

1. Välj **Använd** för att välja funktionsappen som resurs att övervaka.

1. Från **Mått väljer** du Antal **funktionskörningar och** Summa **för** **Sammansättning.** Detta lägger till summan av antalet körningar under den valda perioden i diagrammet.

    ![Definiera ett mått för en functions-app som ska läggas till i diagrammet](media/functions-consumption-costing/monitor-metrics-add-metric.png)

1. Välj **Lägg till mått** och upprepa steg 2–4 för att lägga till **funktionskörningsenheter** i diagrammet. 

Det resulterande diagrammet innehåller summorna för båda körningsmåtten i det valda tidsperioden, vilket i det här fallet är två timmar.

![Diagram över antal funktionskörningar och körningsenheter](media/functions-consumption-costing/monitor-metrics-execution-sum.png)

Eftersom antalet körningsenheter är så mycket större än antalet körningar visar diagrammet bara körningsenheter.

Det här diagrammet visar totalt 1,11 miljarder förbrukade under en tvåtimmarsperiod, mätt i `Function Execution Units` MB-millisekunder. Om du vill konvertera till GB-sekunder dividera med 1024000. I det här exemplet förbrukade `1110000000 / 1024000 = 1083.98` funktionsappen GB-sekunder. Du kan ta det här värdet och multiplicera med det aktuella priset för körningstiden på prissättningssidan för [Functions,][]som ger dig kostnaden för dessa två timmar, förutsatt att du redan har använt några kostnadsfria bidrag för körningstid. 

#### <a name="azure-cli"></a>Azure CLI

[Azure CLI har](/cli/azure/) kommandon för att hämta mått. Du kan använda CLI från en lokal kommandomiljö eller direkt från portalen med hjälp av [Azure Cloud Shell](../cloud-shell/overview.md). Till exempel returnerar följande [az monitor metrics list-kommando](/cli/azure/monitor/metrics#az_monitor_metrics_list) timdata under samma tidsperiod som användes tidigare.

Ersätt med ditt `<AZURE_SUBSCRIPTON_ID>` Azure-prenumerations-ID som kör kommandot .

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
Det här specifika svaret visar att från till förbrukade appen `2019-09-11T21:46` `2019-09-11T23:18` 1110000000 MB-millisekunder (1 083,98 GB sekunder).

### <a name="function-level-metrics"></a>Mått på funktionsnivå

Funktionskörningsenheter är en kombination av körningstid och minnesanvändning, vilket gör det svårt att förstå minnesanvändningen. Minnesdata är för närvarande inte ett mått som är tillgängligt via Azure Monitor. Men om du vill optimera minnesanvändningen för din app kan du använda prestandaräknardata som samlas in av Application Insights.  

Om du inte redan har gjort det aktiverar [du Application Insights i funktionsappen](configure-monitoring.md#enable-application-insights-integration). När den här integreringen är aktiverad [kan du köra frågor mot dessa telemetridata i portalen](analyze-telemetry-data.md#query-telemetry-data). 

[!INCLUDE [functions-consumption-metrics-queries](../../includes/functions-consumption-metrics-queries.md)]

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Läs mer om övervakningsfunktionsappar](functions-monitoring.md)

[prissättningssida]:https://azure.microsoft.com/pricing/details/functions/
[Azure-portalen]: https://portal.azure.com
