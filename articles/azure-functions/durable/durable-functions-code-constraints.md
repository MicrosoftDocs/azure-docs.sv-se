---
title: Tåliga Orchestrator Code-begränsningar – Azure Functions
description: Orchestration-funktionen repetitions-och kod begränsningar för Azure Durable Functions.
author: cgillum
ms.topic: conceptual
ms.date: 11/02/2019
ms.author: azfuncdf
ms.openlocfilehash: 63db8375379144b2ede78d9e7010a350b3f69b12
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/03/2021
ms.locfileid: "101726418"
---
# <a name="orchestrator-function-code-constraints"></a>Begränsningar för Orchestrator-funktions kod

Durable Functions är en utökning av [Azure Functions](../functions-overview.md) som gör att du kan bygga tillstånds känsliga appar. Du kan använda en [Orchestrator-funktion](durable-functions-orchestrations.md) för att dirigera körningen av andra varaktiga funktioner i en Function-app. Orchestrator-funktioner är tillstånds känsliga, pålitliga och potentiellt långvariga.

## <a name="orchestrator-code-constraints"></a>Begränsningar för orkestrerarkod

Orchestrator-funktioner använder [händelse källor](/azure/architecture/patterns/event-sourcing) för att säkerställa tillförlitlig körning och upprätthålla lokal variabel status. [Repetitions beteendet](durable-functions-orchestrations.md#reliability) i Orchestrator Code skapar begränsningar för den typ av kod som du kan skriva i en Orchestrator-funktion. Till exempel måste Orchestrator-funktioner vara *deterministiska*: en Orchestrator-funktion kommer att spelas upp flera gånger och det måste ge samma resultat varje gång.

### <a name="using-deterministic-apis"></a>Använda deterministiska API: er

Det här avsnittet innehåller några enkla rikt linjer som hjälper dig att se till att koden är deterministisk.

Orchestrator-funktioner kan anropa alla API: er på deras mål språk. Det är dock viktigt att Orchestrator-funktioner bara anropar deterministiska API: er. Ett *deterministiskt API* är ett API som alltid returnerar samma värde med samma ineffekt, oavsett när eller hur ofta det anropas.

I följande tabell visas exempel på API: er som du bör undvika eftersom de *inte* är deterministiska. Dessa begränsningar gäller endast för Orchestrator-funktioner. Andra funktions typer har inte sådana begränsningar.

| API-kategori | Anledning | Lösning |
| ------------ | ------ | ---------- |
| Datum och tider  | API: er som returnerar aktuellt datum eller tid är icke-deterministiska eftersom det returnerade värdet är olika för varje omuppspelning. | Använd egenskapen [CurrentUtcDateTime](/dotnet/api/microsoft.azure.webjobs.extensions.durabletask.idurableorchestrationcontext.currentutcdatetime) i .net, `currentUtcDateTime` API: et i Java Script eller `current_utc_datetime` API i python, som är säkert för uppspelning. |
| GUID och UUID: er  | API: er som returnerar ett slumpmässigt GUID eller UUID är icke deterministiska eftersom det genererade värdet är olika för varje omuppspelning. | Använd [NewGuid](/dotnet/api/microsoft.azure.webjobs.extensions.durabletask.idurableorchestrationcontext.newguid) i .net, `newGuid` i Java Script och `new_guid` i python för att på ett säkert sätt generera slumpmässiga GUID. |
| Slumpmässiga siffror | API: er som returnerar slumpmässiga tal är icke-deterministiska eftersom det genererade värdet är olika för varje omuppspelning. | Använd en aktivitets funktion för att returnera slumptal till ett Orchestration-tal. De returnerade värdena för aktivitets funktioner är alltid säkra för uppspelning. |
| Bindningar | Både indata och utdata-bindningar är I/O och är icke-deterministiska. En Orchestrator-funktion får inte använda direkt ens [Dirigerings klienten](durable-functions-bindings.md#orchestration-client) och [enhets klient](durable-functions-bindings.md#entity-client) bindningarna. | Använda indata och utgående bindningar i klient-eller aktivitets funktioner. |
| Nätverk | Nätverks anrop omfattar externa system och är icke-deterministiska. | Använd aktivitets funktioner för att göra nätverks anrop. Om du behöver göra ett HTTP-anrop från din Orchestrator-funktion kan du också använda de [bestående HTTP-API: erna](durable-functions-http-features.md#consuming-http-apis). |
| Blockera API: er | Blockering av API: er som `Thread.Sleep` i .net och liknande API: er kan orsaka prestanda-och skalnings problem för Orchestrator-funktioner och bör undvikas. I Azure Functions förbruknings planen kan de även leda till onödiga körnings avgifter. | Använd alternativ för att blockera API: er när de är tillgängliga. Använd  `CreateTimer` till exempel för att införa fördröjningar i Orchestration-körningen. Fördröjda [timer](durable-functions-timers.md) -fördröjningar räknas inte mot körnings tiden för en Orchestrator-funktion. |
| Asynkrona API: er | Orchestrator Code får aldrig starta en asynkron åtgärd förutom att använda `IDurableOrchestrationContext` API: t, `context.df` API: et i Java Script eller `context` API i python. Du kan till exempel inte använda `Task.Run` , `Task.Delay` , och `HttpClient.SendAsync` i .net eller `setTimeout` och `setInterval` i Java Script. Det tåliga aktivitets ramverket Kör Orchestrator-kod på en enda tråd. Den kan inte samverka med andra trådar som kan anropas av andra asynkrona API: er. | En Orchestrator-funktion bör endast göra varaktiga asynkrona anrop. Aktivitets funktioner bör göra andra asynkrona API-anrop. |
| Asynkrona JavaScript-funktioner | Du kan inte deklarera Java Script Orchestrator-funktioner som `async` eftersom node.js runtime inte garanterar att asynkrona funktioner är deterministiska. | Deklarera JavaScript Orchestrator-funktioner som synkrona Generator funktioner |
| Python-rutiner | Du kan inte deklarera python Orchestrator-funktioner som antirutiner, dvs. deklarera dem med `async` nyckelordet eftersom samordnad semantik inte överensstämmer med den Durable Functions uppspelnings modellen. | Deklarera python Orchestrator-funktioner som generatorer, vilket innebär att du bör förvänta dig att `context` API: et ska användas `yield` i stället för `await` .   |
| Tråd kopplings-API: er | Det tåliga aktivitets ramverket Kör Orchestrator-kod på en enskild tråd och kan inte samverka med andra trådar. Att introducera nya trådar i en Dirigerings körning kan resultera i icke deterministisk körning eller död lägen. | Orchestrator-funktioner bör nästan aldrig använda tråd-API: er. I .NET bör du till exempel undvika att använda `ConfigureAwait(continueOnCapturedContext: false)` . Detta säkerställer att uppgifts fortsättningen körs på Orchestrator-funktionens ursprungliga `SynchronizationContext` . Om dessa API: er är nödvändiga begränsar du deras användning till endast aktivitets funktioner. |
| Statiska variabler | Undvik att använda icke-konstant statiska variabler i Orchestrator-funktioner eftersom deras värden kan ändras över tid, vilket leder till icke-deterministisk körnings beteende. | Använd konstanter eller begränsa användningen av statiska variabler till aktivitets funktioner. |
| Miljövariabler | Använd inte miljövariabler i Orchestrator-funktioner. Deras värden kan ändras över tid, vilket leder till icke-deterministisk körnings beteende. | Miljövariabler får endast refereras från i klient funktioner eller aktivitets funktioner. |
| Oändliga slingor | Undvik oändlig loop i Orchestrator-funktioner. Eftersom den varaktiga aktivitets ramverket sparar körnings historiken när Orchestration-funktionen fortskrider, kan en oändlig loop orsaka att en Orchestrator-instans tar slut på minne. | Använd API: er som `ContinueAsNew` i .net, `continueAsNew` i Java Script eller i python för oändliga upprepnings scenarier för `continue_as_new` att starta om funktionen och ta bort tidigare körnings historik. |

Även om det kan verka svårt att tillämpa dessa begränsningar i praktiken är de enkla att följa.

Det beständiga aktivitets ramverket försöker identifiera överträdelser av föregående regler. Om en överträdelse hittas genererar ramverket ett **NonDeterministicOrchestrationException** -undantag. Detta identifierings beteende fångar dock inte upp alla överträdelser och du bör inte vara beroende av den.

## <a name="versioning"></a>Versionshantering

En varaktig dirigering kan köras kontinuerligt i dagar, månader, år eller till och med [Eternally](durable-functions-eternal-orchestrations.md). Alla kod uppdateringar som görs i Durable Functions appar som påverkar oavslutade dirigeringar kan leda till att omuppspelnings beteendet för dirigeringen bryts. Det är därför det är viktigt att planera noggrant när du gör uppdateringar av kod. En mer detaljerad beskrivning av hur du använder version av koden finns i [artikeln versions hantering](durable-functions-versioning.md).

## <a name="durable-tasks"></a>Varaktiga uppgifter

> [!NOTE]
> I det här avsnittet beskrivs interna implementerings Detaljer för det varaktiga aktivitets ramverket. Du kan använda varaktiga funktioner utan att känna till den här informationen. Den är endast avsedd för att hjälpa dig att förstå repetitions beteendet.

Aktiviteter som på ett säkert sätt kan vänta i Orchestrator-funktioner kallas ibland för *varaktiga aktiviteter*. Det varaktiga aktivitets ramverket skapar och hanterar dessa uppgifter. Exempel är de uppgifter som returneras av **CallActivityAsync**, **WaitForExternalEvent** och **CreateTimer** i .net Orchestrator-funktioner.

Dessa varaktiga uppgifter hanteras internt av en lista med `TaskCompletionSource` objekt i .net. Under uppspelningen skapas dessa uppgifter som en del av Orchestrator-kod körningen. De är klara eftersom Dispatchern räknar upp motsvarande historik händelser.

Aktiviteterna körs synkront med en enda tråd tills alla historiken har spelats upp. Fördelade uppgifter som inte slutförs i slutet av historik uppspelningen har lämpliga åtgärder utförda. Ett meddelande kan till exempel vara placerat i kö för att anropa en aktivitets funktion.

Det här avsnittet beskriver körnings beteendet för att hjälpa dig att förstå varför en Orchestrator-funktion inte kan använda `await` eller `yield` i en inhållbar uppgift. Det finns två orsaker: dispatcher-tråden kan inte vänta på att uppgiften slutförs och alla återanrop från den aktiviteten kan eventuellt skada spårnings läget för Orchestrator-funktionen. Vissa körnings kontroller är på plats för att hjälpa till att identifiera dessa överträdelser.

Om du vill veta mer om hur det varaktiga aktivitets ramverket Kör Orchestrator-funktioner kan du läsa den [varaktiga aktivitets käll koden på GitHub](https://github.com/Azure/durabletask). I synnerhet, se [TaskOrchestrationExecutor.cs](https://github.com/Azure/durabletask/blob/master/src/DurableTask.Core/TaskOrchestrationExecutor.cs) och [TaskOrchestrationContext.cs](https://github.com/Azure/durabletask/blob/master/src/DurableTask.Core/TaskOrchestrationContext.cs).

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Läs om hur du anropar under-Orchestration](durable-functions-sub-orchestrations.md)

> [!div class="nextstepaction"]
> [Lär dig hur du hanterar versions hantering](durable-functions-versioning.md)
