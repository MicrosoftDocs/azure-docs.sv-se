---
title: Funktions typer i Azure Durable Functions
description: Lär dig mer om de typer av funktioner och roller som stöder funktions-till-funktion-kommunikation i en Durable Functions dirigering i Azure Functions.
author: cgillum
ms.topic: conceptual
ms.date: 08/22/2019
ms.author: azfuncdf
ms.openlocfilehash: 35ef9d8731e169e890f5985ce01215fec5d6e3de
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "84697715"
---
# <a name="durable-functions-types-and-features"></a>Durable Functions typer och funktioner

Durable Functions är en utökning av [Azure Functions](../functions-overview.md). Du kan använda Durable Functions för tillstånds känslig dirigering av funktions körning. En hållbar Function-app är en lösning som består av olika Azure Functions. Funktioner kan spela olika roller i en varaktig funktions dirigering. 

Det finns för närvarande fyra varaktiga funktions typer i Azure Functions: aktivitet, Orchestrator, entitet och klient. Resten av det här avsnittet innehåller mer information om vilka typer av funktioner som ingår i ett dirigering.

## <a name="orchestrator-functions"></a>Orchestrator-funktioner

Orchestrator Functions beskriver hur åtgärder utförs och i vilken ordning åtgärder utförs. Orchestrator Functions beskriver dirigeringen i kod (C# eller Java Script) som visas i [Durable Functions program mönster](durable-functions-overview.md#application-patterns). En dirigering kan ha många olika typer av åtgärder, inklusive [aktivitets funktioner](#activity-functions), [under dirigering](durable-functions-orchestrations.md#sub-orchestrations), [som väntar på externa händelser](durable-functions-orchestrations.md#external-events), [http](durable-functions-http-features.md)och [timers](durable-functions-orchestrations.md#durable-timers). Orchestrator-funktioner kan också samverka med [enhets funktioner](#entity-functions).

> [!NOTE]
> Orchestrator-funktioner skrivs med vanlig kod, men det finns strikta krav på hur du skriver koden. Mer specifikt måste Orchestrator-funktions koden vara *deterministisk*. Om du inte följer dessa determinism-krav kan det leda till att Orchestrator-funktioner inte kan köras på rätt sätt. Detaljerad information om dessa krav och hur du kan lösa dem finns i avsnittet [kod begränsningar](durable-functions-code-constraints.md) .

Mer detaljerad information om Orchestrator-funktioner och deras funktioner finns i artikeln [beständiga Orchestrations](durable-functions-orchestrations.md) artiklar.

## <a name="activity-functions"></a>Aktivitets funktioner

Aktivitets funktionerna är den grundläggande arbets enheten i en varaktig funktions dirigering. Aktivitets funktioner är de funktioner och uppgifter som samordnas i processen. Du kan till exempel skapa en Orchestrator-funktion för att bearbeta en order. Uppgifterna omfattar att kontrol lera inventeringen, debitera kunden och skapa en försändelse. Varje aktivitet är en separat aktivitets funktion. Dessa aktivitets funktioner kan köras seriellt, parallellt eller någon kombination av båda.

Till skillnad från Orchestrator Functions är aktivitets funktioner inte begränsade i den typ av arbete som du kan göra i dem. Aktivitets funktioner används ofta för att göra nätverks anrop eller köra processor intensiva åtgärder. En aktivitets funktion kan också returnera data tillbaka till Orchestrator-funktionen. Det varaktiga aktivitets ramverket garanterar att varje anropad aktivitets funktion utförs *minst en gång* under en Dirigerings körning.

> [!NOTE]
> Eftersom aktivitets funktioner endast garanterar *minst en gång* , rekommenderar vi att du gör din aktivitets funktions logik *idempotenta* närhelst det är möjligt.

Använd en [aktivitets utlösare](durable-functions-bindings.md#activity-trigger) för att definiera en aktivitets funktion. .NET Functions tar emot en `DurableActivityContext` som en parameter. Du kan också binda utlösaren till alla andra JSON-serializeable-objekt för att överföra indata till funktionen. I Java Script kan du komma åt inmatade `<activity trigger binding name>` objekt via egenskapen på [ `context.bindings` objektet](../functions-reference-node.md#bindings). Aktivitets funktioner kan bara ha ett enda värde. Om du vill skicka flera värden måste du använda tupler, matriser eller komplexa typer.

> [!NOTE]
> Du kan endast utlösa en aktivitets funktion från en Orchestrator-funktion.

## <a name="entity-functions"></a>Enhets funktioner

Entitets funktioner definierar åtgärder för att läsa och uppdatera små delar av tillstånd. Vi refererar ofta till dessa tillstånds känsliga entiteter som *varaktiga enheter*. Precis som med Orchestrator Functions är enhets funktionerna funktioner med en särskild utlösnings typ, *enhets utlösare*. De kan också anropas från klient funktioner eller från Orchestrator-funktioner. Till skillnad från Orchestrator-funktioner har enhets funktioner inga speciella kod begränsningar. Enhets funktionerna hanterar också tillstånd explicit snarare än implicit som representerar tillstånd via kontroll flödet.

> [!NOTE]
> Enhets funktioner och relaterade funktioner är bara tillgängliga i Durable Functions 2,0 och senare.

Mer information om entitets funktioner finns i artikeln [beständiga entiteter](durable-functions-entities.md) .

## <a name="client-functions"></a>Klient funktioner

Orchestrator-funktioner utlöses av en [bindning för Orchestration-utlösare](durable-functions-bindings.md#orchestration-trigger) och entiteter utlöses av en [bindning för enhets utlösare](durable-functions-bindings.md#entity-trigger). Båda dessa utlösare fungerar genom att du reagerar på meddelanden som är köade i en [aktivitets hubb](durable-functions-task-hubs.md). Det primära sättet att leverera dessa meddelanden är att använda en [Orchestrator-klient bindning](durable-functions-bindings.md#orchestration-client) eller en [enhets klient bindning](durable-functions-bindings.md#entity-client) från en *klient funktion*. En icke-Orchestrator-funktion kan vara en *klient funktion*. Du kan till exempel utlösa Orchestrator från en HTTP-utlöst funktion, en funktion som utlöses av Azure Event Hub, osv. Det här är en funktion som en *klient funktion* använder för att använda den varaktiga klienten utgående bindning.

> [!NOTE]
> Till skillnad från andra funktions typer kan inte funktionerna för Orchestrator och entitet utlösas direkt med hjälp av knapparna i Azure-portalen. Om du vill testa en Orchestrator-eller Entity-funktion i Azure-portalen måste du i stället köra en *klient funktion* som startar en Orchestrator-eller Entity-funktion som en del av dess implementering. En *manuell utlösnings* funktion rekommenderas för den enklaste test upplevelsen.

Förutom att utlösa Orchestrator-eller entitet-funktioner kan den *varaktiga klient* bindningen användas för att interagera med att köra Orchestration och entiteter. Till exempel kan dirigeringar frågas, avslutas och de kan aktive ras för händelser. Mer information om hur du hanterar Orchestration och entiteter finns i artikeln [instans hantering](durable-functions-instance-management.md) .

## <a name="next-steps"></a>Nästa steg

Kom igång genom att skapa din första tåliga funktion i [C#](durable-functions-create-first-csharp.md) eller [Java Script](quickstart-js-vscode.md).

> [!div class="nextstepaction"]
> [Läs mer om Durable Functions Orchestration](durable-functions-orchestrations.md)