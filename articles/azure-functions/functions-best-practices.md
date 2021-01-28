---
title: Metod tips för Azure Functions
description: Lär dig metod tips och mönster för Azure Functions.
ms.assetid: 9058fb2f-8a93-4036-a921-97a0772f503c
ms.topic: conceptual
ms.date: 12/17/2019
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: f05afb3c23fc720bb0100a751a6943d7bb03453f
ms.sourcegitcommit: 4e70fd4028ff44a676f698229cb6a3d555439014
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/28/2021
ms.locfileid: "98954791"
---
# <a name="optimize-the-performance-and-reliability-of-azure-functions"></a>Optimera säkerheten och tillförlitligheten för Azure Functions

Den här artikeln innehåller rikt linjer för att förbättra prestanda och tillförlitlighet för dina program utan [Server](https://azure.microsoft.com/solutions/serverless/) funktioner.  

## <a name="general-best-practices"></a>Allmän bästa praxis

Följande är metod tips för hur du skapar och utvecklar lösningar utan server med hjälp av Azure Functions.

### <a name="avoid-long-running-functions"></a>Undvik tids krävande funktioner

Stora, långvariga funktioner kan orsaka oväntade timeout-problem. Om du vill veta mer om tids gränsen för en specifik värd plan, se [funktion appens timeout-varaktighet](functions-scale.md#timeout). 

En funktion kan bli stor på grund av många Node.js beroenden. Att importera beroenden kan också orsaka ökade inläsnings tider som leder till oväntade tids gränser. Beroenden läses in både explicit och implicit. En enda modul som läses in av koden kan läsa in sina egna ytterligare moduler. 

När det är möjligt kan åter förfalla stora funktioner till mindre funktions uppsättningar som arbetar tillsammans och returnerar svar snabbt. En webhook-eller HTTP-utlösnings funktion kan till exempel kräva ett bekräftelse svar inom en viss tids gräns. Det är vanligt att webhookar kräver omedelbara svar. Du kan skicka HTTP-utlösaren till en kö som ska bearbetas av en funktion i en Queue-utlösare. Med den här metoden kan du skjuta upp det faktiska arbetet och returnera ett omedelbart svar.


### <a name="cross-function-communication"></a>Kors funktions kommunikation

[Durable Functions](durable/durable-functions-overview.md) och [Azure Logic Apps](../logic-apps/logic-apps-overview.md) är utformade för att hantera tillstånds över gångar och kommunikation mellan flera funktioner.

Om du inte använder Durable Functions eller Logic Apps för att integrera med flera funktioner, är det bäst att använda lagrings köer för kommunikation mellan funktioner. Huvud orsaken är att lagrings köer är billigare och enklare att etablera än andra lagrings alternativ. 

Enskilda meddelanden i en lagrings kö har begränsad storlek till 64 KB. Om du behöver skicka större meddelanden mellan funktioner kan en Azure Service Bus kö användas för att stödja meddelande storlekar upp till 256 KB på standard nivån och upp till 1 MB på Premium nivån.

Service Bus ämnen är användbara om du behöver filtrering av meddelanden före bearbetning.

Event Hub är användbara för att stödja hög volym kommunikation.


### <a name="write-functions-to-be-stateless"></a>Skriv funktioner som tillstånds lösa 

Funktioner ska vara tillstånds lösa och idempotenta om möjligt. Koppla all nödvändig tillståndsinformation till dina data. En beställning som bearbetas skulle till exempel ha en associerad `state` medlem. En funktion kan bearbeta en ordning baserat på det läget, medan själva funktionen fortfarande är tillstånds lös. 

Idempotenta-funktioner rekommenderas särskilt för timer-utlösare. Om du till exempel har något som är absolut måste köra en gång om dagen skriver du det så att det kan köras när som helst under dagen med samma resultat. Funktionen kan avslutas när det inte finns något arbete för en viss dag. Även om en tidigare körning inte kunde slutföras, ska nästa körning fortsätta där den slutade.


### <a name="write-defensive-functions"></a>Skriv försvars funktioner

Anta att din funktion kan stöta på ett undantag när som helst. Utforma dina funktioner med möjlighet att fortsätta från en tidigare misslyckad punkt under nästa körning. Överväg ett scenario som kräver följande åtgärder:

1. Fråga efter 10 000 rader i en databas.
2. Skapa ett Queue-meddelande för var och en av dessa rader för att bearbeta ytterligare en rad.
 
Beroende på hur komplex systemet är kan du ha: berörda underordnade tjänster fungerar dåligt, nätverks avbrott eller kvot gränser, osv. Alla dessa kan påverka din funktion när som helst. Du måste utforma dina funktioner för att förbereda dig för den.

Hur reagerar din kod om ett fel inträffar när du har infogat 5 000 av dessa objekt i en kö för bearbetning? Spåra objekt i en uppsättning som du har slutfört. Annars kan du infoga dem igen nästa gång. Den här dubbel infogningen kan ha en allvarlig inverkan på ditt arbets flöde, så [gör dina funktioner idempotenta](functions-idempotent.md). 

Om ett köobjekt redan bearbetats kan du tillåta att din funktion är en no-op.

Dra nytta av försvars åtgärder som redan har angetts för komponenter som du använder i Azure Functionss plattformen. Se till exempel **hantering av meddelanden om Poison-kön** i dokumentationen för [Azure Storage köa utlösare och bindningar](functions-bindings-storage-queue-trigger.md#poison-messages). 

## <a name="scalability-best-practices"></a>Metod tips för skalbarhet

Det finns ett antal faktorer som påverkar hur instanser av Function-appen skalas. Informationen finns i dokumentationen för [funktions skalning](functions-scale.md).  Här följer några metod tips för att säkerställa optimal skalbarhet för en Function-app.

### <a name="share-and-manage-connections"></a>Dela och hantera anslutningar

Återanvänd anslutningar till externa resurser när det är möjligt. Se [hantera anslutningar i Azure Functions](./manage-connections.md).

### <a name="avoid-sharing-storage-accounts"></a>Undvik att dela lagrings konton

När du skapar en Function-app måste du associera den med ett lagrings konto. Lagrings konto anslutningen upprätthålls i [program inställningen AzureWebJobsStorage](./functions-app-settings.md#azurewebjobsstorage). 

[!INCLUDE [functions-shared-storage](../../includes/functions-shared-storage.md)]

### <a name="dont-mix-test-and-production-code-in-the-same-function-app"></a>Blanda inte test-och produktions kod i samma Function-app

Funktioner i en Function-app delar resurser. Till exempel delas minnet. Om du använder en Function-app i produktion ska du inte lägga till test-relaterade funktioner och resurser i den. Det kan orsaka oväntad kostnader vid körning av produktions kod.

Se till att du läser in dina produktions funktions appar. Minne är genomsnittet för varje funktion i appen.

Om du har en delad sammansättning som refereras i flera .NET-funktioner kan du använda den i en gemensam delad mapp. Annars kan du av misstag distribuera flera versioner av samma binärfil som beter sig annorlunda mellan functions.

Använd inte utförlig loggning i produktions kod, vilket ger en negativ inverkan på prestanda.

### <a name="use-async-code-but-avoid-blocking-calls"></a>Använd asynkron kod men Undvik att blockera anrop

Asynkron programmering är en rekommenderad metod, särskilt när du blockerar I/O-åtgärder.

I C# ska du alltid undvika att referera till `Result` egenskapen eller anropa `Wait` metoden på en `Task` instans. Den här metoden kan leda till tråd överbelastning.

[!INCLUDE [HTTP client best practices](../../includes/functions-http-client-best-practices.md)]

### <a name="use-multiple-worker-processes"></a>Använd flera arbets processer

Som standard använder alla värd instanser för functions en enda arbets process. För att förbättra prestanda, särskilt med enkla körningar som python, använder du [FUNCTIONS_WORKER_PROCESS_COUNT](functions-app-settings.md#functions_worker_process_count) för att öka antalet arbets processer per värd (upp till 10). Azure Functions försöker sedan jämnt distribuera samtidiga funktions anrop över dessa arbetare. 

FUNCTIONS_WORKER_PROCESS_COUNT gäller för varje värd som fungerar när du skalar ditt program för att möta efter frågan. 

### <a name="receive-messages-in-batch-whenever-possible"></a>Ta emot meddelanden i batch när det är möjligt

Vissa utlösare som Event Hub aktiverar att ta emot en batch med meddelanden på ett enda anrop.  Batching-meddelanden har mycket bättre prestanda.  Du kan konfigurera Max storleken för batch i `host.json` filen enligt beskrivningen i [host.jsi referens dokumentation](functions-host-json.md)

För C#-funktioner kan du ändra typen till en starkt angiven matris.  I stället för `EventData sensorEvent` metoden Signature kan till exempel vara `EventData[] sensorEvent` .  För andra språk måste du uttryckligen ange egenskapen kardinalitet i din `function.json` till för `many` att aktivera batchbearbetning [som det visas här](https://github.com/Azure/azure-webjobs-sdk-templates/blob/df94e19484fea88fc2c68d9f032c9d18d860d5b5/Functions.Templates/Templates/EventHubTrigger-JavaScript/function.json#L10).

### <a name="configure-host-behaviors-to-better-handle-concurrency"></a>Konfigurera värd beteenden för att bättre hantera samtidighet

`host.json`Filen i Function-appen möjliggör konfiguration av värd körning och utlösnings beteenden.  Förutom batching-beteenden kan du hantera samtidighet för ett antal utlösare. Att justera värdena i dessa alternativ kan ofta hjälpa varje instans skala på lämpligt sätt för kraven hos de anropade funktionerna.

Inställningarna i host.jspå filen gäller för alla funktioner i appen, inom en *enda instans* av funktionen. Om du till exempel har en Function-app med två HTTP-funktioner och [`maxConcurrentRequests`](functions-bindings-http-webhook-output.md#hostjson-settings) begär Anden som har angetts till 25, räknas en begäran till HTTP-utlösaren mot de delade 25 samtidiga förfrågningarna.  När den här funktionen är skalad till 10 instanser kan de tio funktionerna effektivt tillåta 250 samtidiga begär Anden (10 instanser * 25 samtidiga begär Anden per instans). 

Andra värd konfigurations alternativ finns i [ artikelnhost.jsi konfigurations artikeln](functions-host-json.md).

## <a name="next-steps"></a>Nästa steg

Mer information finns i följande resurser:

* [Hantera anslutningar i Azure Functions](manage-connections.md)
* [Metod tips för Azure App Service](../app-service/app-service-best-practices.md)
