---
title: Distribution utan drift avbrott för Durable Functions
description: Lär dig hur du aktiverar Durable Functions-dirigering för distributioner utan drift avbrott.
author: tsushi
ms.topic: conceptual
ms.date: 10/10/2019
ms.author: azfuncdf
ms.custom: fasttrack-edit
ms.openlocfilehash: 707d624c47c536e00e98910a8902772703733515
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "102558771"
---
# <a name="zero-downtime-deployment-for-durable-functions"></a>Distribution utan drift avbrott för Durable Functions

Den [tillförlitliga körnings modellen](./durable-functions-orchestrations.md) för Durable Functions kräver att dirigeringar är deterministiska, vilket skapar en ytterligare utmaning att tänka på när du distribuerar uppdateringar. När en distribution innehåller ändringar i aktivitets funktionens signaturer eller Orchestrator-logik, går det inte att utföra Dirigerings instanser i flygning. Den här situationen är särskilt ett problem för instanser av långvariga dirigeringar som kan representera timmar eller arbets dagar.

För att förhindra att de här felen inträffar har du två alternativ: 
- Fördröj distributionen tills alla pågående Dirigerings instanser har slutförts.
- Se till att alla pågående Orchestration-instanser använder befintliga versioner av dina funktioner. 

Följande diagram jämför de tre huvud strategierna för att uppnå en distribution utan avbrott för Durable Functions: 

| Strategi |  När du ska använda detta | Fördelar | Nackdelar |
| -------- | ------------ | ---- | ---- |
| [Versionshantering](#versioning) |  Program som inte upplever frekventa [ändringar.](durable-functions-versioning.md) | Enkelt att implementera. |  Ökad funktions program storlek i minnet och antalet funktioner.<br/>Kod duplicering. |
| [Status kontroll med plats](#status-check-with-slot) | Ett system som inte har långvariga dirigeringar som varar mer än 24 timmar eller ofta överlappande dirigeringar. | Enkel kodbas.<br/>Kräver inte ytterligare hantering av funktions program. | Kräver ytterligare lagrings konto eller aktivitets NAVs hantering.<br/>Kräver tids perioder då inga dirigeringar körs. |
| [Program dirigering](#application-routing) | Ett system som inte har några tids perioder när Orchestration inte körs, till exempel de tids perioder med samordningar som senast 24 timmar eller med ofta överlappande samordning. | Hanterar nya versioner av system med kontinuerlig körning av dirigeringar som har avbrutit ändringar. | Kräver en intelligent programrouter.<br/>Det gick inte att maximera antalet funktions program som tillåts av din prenumeration. Standardvärdet är 100. |

## <a name="versioning"></a>Versionshantering

Definiera nya versioner av funktionerna och lämna de gamla versionerna i din Function-app. Som du kan se i diagrammet blir en funktions version en del av dess namn. Eftersom tidigare versioner av Functions bevaras, kan Dirigerings instanser i flyg plan fortsätta att referera till dem. Efter frågas begär Anden om nya Orchestration-instanser att anropa den senaste versionen, vilken Orchestration-klienten kan referera till från en app-inställning.

![Strategi för versions hantering](media/durable-functions-zero-downtime-deployment/versioning-strategy.png)

I den här strategin måste alla funktioner kopieras och dess referenser till andra funktioner måste uppdateras. Du kan göra det enklare genom att skriva ett skript. Här är ett [exempel projekt](https://github.com/TsuyoshiUshio/DurableVersioning) med ett migreringsarkiv.

>[!NOTE]
>Den här strategin använder distributions platser för att undvika drift stopp under distributionen. Mer detaljerad information om hur du skapar och använder nya distributions platser finns i [Azure Functions distributions fack](../functions-deployment-slots.md).

## <a name="status-check-with-slot"></a>Status kontroll med plats

När den aktuella versionen av din Function-App körs på produktions platsen, kan du distribuera den nya versionen av din Function-app till mellanlagringsplatsen. Innan du byter ut produktions-och mellanlagringsplatserna kontrollerar du om det finns några pågående Dirigerings instanser. När alla Dirigerings instanser har slutförts kan du göra växlingen. Den här strategin fungerar när du har förutsägbara perioder när inga Dirigerings instanser är i flygning. Detta är den bästa metoden när dina dirigeringar inte är långvariga och när dina Dirigerings körningar inte ofta överlappar varandra.

### <a name="function-app-configuration"></a>Function-app-konfiguration

Använd följande procedur för att konfigurera det här scenariot.

1. [Lägg till distributions platser](../functions-deployment-slots.md#add-a-slot) i din Function-app för mellanlagring och produktion.

1. För varje plats ställer du in [program inställningen AzureWebJobsStorage](../functions-app-settings.md#azurewebjobsstorage) på anslutnings strängen för ett delat lagrings konto. Den här anslutnings strängen för lagrings kontot används av Azure Functions Runtime. Det här kontot används av Azure Functions Runtime och hanterar funktionens nycklar.

1. Skapa en ny app-inställning för varje plats, till exempel `DurableManagementStorage` . Ange värdet för anslutnings strängen för olika lagrings konton. Dessa lagrings konton används av Durable Functions-tillägget för [tillförlitlig körning](./durable-functions-orchestrations.md). Använd ett separat lagrings konto för varje plats. Markera inte den här inställningen som en distributions plats inställning.

1. I din Function-appens [host.jspå filens durableTask-avsnitt](durable-functions-bindings.md#hostjson-settings)anger du `connectionStringName` (tålig 2. x) eller `azureStorageConnectionStringName` (varaktig 1. x) som namn på den app-inställning som du skapade i steg 3.

Följande diagram visar den beskrivna konfigurationen av distributions platser och lagrings konton. I det här scenariot för för distribution körs version 2 av en Function-app på produktions platsen, medan version 1 är kvar på mellanlagringsplatsen.

![Distributions platser och lagrings konton](media/durable-functions-zero-downtime-deployment/deployment-slot.png)

### <a name="hostjson-examples&quot;></a>host.jspå exempel

Följande JSON-fragment är exempel på inställningen för anslutnings strängen i *host.js* i filen.

#### <a name=&quot;functions-20&quot;></a>Functions 2,0

```json
{
  &quot;version&quot;: 2.0,
  &quot;extensions&quot;: {
    &quot;durableTask&quot;: {
      &quot;hubName&quot;: &quot;MyTaskHub&quot;,
      &quot;storageProvider&quot;: {
        &quot;connectionStringName&quot;: &quot;DurableManagementStorage&quot;
      }
    }
  }
}
```

#### <a name=&quot;functions-1x&quot;></a>Functions 1.x

```json
{
  &quot;durableTask&quot;: {
    &quot;azureStorageConnectionStringName&quot;: &quot;DurableManagementStorage&quot;
  }
}
```

### <a name=&quot;cicd-pipeline-configuration&quot;></a>Konfiguration av konfiguration av CI/CD-pipeline

Konfigurera din CI/CD-pipeline så att den bara distribueras när Function-appen inte har några väntande eller pågående Dirigerings instanser. När du använder Azure-pipeliner kan du skapa en funktion som söker efter dessa villkor, som i följande exempel:

```csharp
[FunctionName(&quot;StatusCheck")]
public static async Task<IActionResult> StatusCheck(
    [HttpTrigger(AuthorizationLevel.Function, "get", "post")] HttpRequestMessage req,
    [DurableClient] IDurableOrchestrationClient client,
    ILogger log)
{
    var runtimeStatus = new List<OrchestrationRuntimeStatus>();

    runtimeStatus.Add(OrchestrationRuntimeStatus.Pending);
    runtimeStatus.Add(OrchestrationRuntimeStatus.Running);

    var result = await client.ListInstancesAsync(new OrchestrationStatusQueryCondition() { RuntimeStatus = runtimeStatus }, CancellationToken.None);
    return (ActionResult)new OkObjectResult(new { HasRunning = result.DurableOrchestrationState.Any() });
}
```

Konfigurera sedan mellanlagrings porten så att den väntar tills inga dirigeringar körs. Mer information finns i avsnittet om [distribution av distributions kontroll med hjälp av portar](/azure/devops/pipelines/release/approvals/gates)

![Distributionsport](media/durable-functions-zero-downtime-deployment/deployment-gate.png)

Azure-pipeline kontrollerar din app för att köra Orchestration-instanser innan distributionen startar.

![Distributions grind (körs)](media/durable-functions-zero-downtime-deployment/deployment-gate-2.png)

Nu bör den nya versionen av din Function-app distribueras till mellanlagringsplatsen.

![Mellanlagringsplats](media/durable-functions-zero-downtime-deployment/deployment-slot-2.png)

Slutligen kan du växla fack. 

Program inställningar som inte har marker ATS som distributions plats inställningar växlas också, så version 2-appen behåller sin referens till lagrings konto A. Eftersom Dirigerings status spåras i lagrings kontot fortsätter alla dirigeringar som körs på version 2-appen att köras på den nya platsen utan avbrott.

![Distributionsplats](media/durable-functions-zero-downtime-deployment/deployment-slot-3.png)

Om du vill använda samma lagrings konto för båda platserna kan du ändra namnen på dina aktivitets nav. I så fall måste du hantera statusen för dina platser och appens HubName-inställningar. Mer information finns [i aktivitets nav i Durable Functions](durable-functions-task-hubs.md).

## <a name="application-routing"></a>Program dirigering

Den här strategin är den mest komplexa. Det kan dock användas för Function-appar som inte har tid mellan att köra dirigeringar.

För den här strategin måste du skapa en *programrouter* framför din Durable functions. Den här routern kan implementeras med Durable Functions. Routern har ansvaret för att:

* Distribuera Function-appen.
* Hantera versionen av Durable Functions. 
* Dirigera Dirigerings begär anden till Function-appar.

Första gången en Dirigerings förfrågan tas emot gör routern följande uppgifter:

1. Skapar en ny function-app i Azure.
2. Distribuerar din Function app-kod till den nya Function-appen i Azure.
3. Vidarebefordrar Orchestration-begäran till den nya appen.

Routern hanterar det tillstånd för vilken version av appens kod distribueras till vilken Function-app i Azure.

![Programroutning (första gången)](media/durable-functions-zero-downtime-deployment/application-routing.png)

Routern dirigerar distributions-och Dirigerings förfrågningar till lämplig Function-app baserat på den version som skickas med begäran. Korrigerings versionen ignoreras.

När du distribuerar en ny version av din app utan en avbrytande ändring kan du öka korrigerings versionen. Routern distribuerar till din befintliga Function-app och skickar begär Anden för de gamla och nya versionerna av koden, som dirigeras till samma Function-app.

![Programroutning (ingen brytande ändring)](media/durable-functions-zero-downtime-deployment/application-routing-2.png)

När du distribuerar en ny version av din app med en avbrytande ändring kan du öka den övre eller lägre versionen. Programroutern skapar sedan en ny function-app i Azure, distribuerar till den och dirigerar begär Anden för den nya versionen av din app till den. I följande diagram ska körningen av dirigering i 1.0.1-versionen av appen fortsätta att köras, men förfrågningar för 1.1.0-versionen dirigeras till den nya Function-appen.

![Programroutning (bryta ändring)](media/durable-functions-zero-downtime-deployment/application-routing-3.png)

Routern övervakar status för dirigeringar i 1.0.1-versionen och tar bort appar när alla dirigeringar har avslut ATS. 

### <a name="tracking-store-settings"></a>Spårar butiks inställningar

Varje Function-app bör använda separata schemaläggnings köer, eventuellt i separata lagrings konton. Om du vill fråga alla Dirigerings instanser i alla versioner av programmet kan du dela instans-och historik tabeller i dina funktions appar. Du kan dela tabeller genom att konfigurera `trackingStoreConnectionStringName` `trackingStoreNamePrefix` inställningarna och i [host.jspå inställnings](durable-functions-bindings.md#host-json) filen så att alla använder samma värden.

Mer information finns i [Hantera instanser i Durable Functions i Azure](durable-functions-instance-management.md).

![Spårar butiks inställningar](media/durable-functions-zero-downtime-deployment/tracking-store-settings.png)

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Versions Durable Functions](durable-functions-versioning.md)
