---
title: Fakturering av varaktiga funktioner – Azure Functions
description: Lär dig mer om de interna funktionerna i Durable Functions och hur de påverkar faktureringen för Azure Functions.
author: cgillum
ms.topic: overview
ms.date: 08/31/2019
ms.author: azfuncdf
ms.openlocfilehash: 2ec1b080c195a47caafd0120240b5fb61ede062b
ms.sourcegitcommit: 2aa52d30e7b733616d6d92633436e499fbe8b069
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/06/2021
ms.locfileid: "97932290"
---
# <a name="durable-functions-billing"></a>Durable Functions fakturering

[Durable Functions](durable-functions-overview.md) faktureras på samma sätt som Azure Functions. Mer information finns i [prissättning för Azure Functions](https://azure.microsoft.com/pricing/details/functions/).

När du kör Orchestrator Functions i Azure Functions [förbruknings plan](../consumption-plan.md)måste du vara medveten om vissa fakturerings beteenden. I följande avsnitt beskrivs dessa beteenden och deras inverkan i detalj.

## <a name="orchestrator-function-replay-billing"></a>Återuppspelning av Orchestrator-funktion

[Orchestrator-funktioner](durable-functions-orchestrations.md) kan spelas upp flera gånger under ett Dirigerings livs längd. Varje repetition visas av Azure Functions runtime som ett distinkt funktions anrop. Därför debiteras du för varje repetition av en Orchestrator-funktion i Azure Functions förbruknings plan. Andra typer av abonnemang debiteras inte för att spela upp Orchestrator-funktionen.

## <a name="awaiting-and-yielding-in-orchestrator-functions"></a>Väntar och ger upphov till Orchestrator-funktioner

När en Orchestrator-funktion väntar på att en asynkron åtgärd ska slutföras **med hjälp av** Waiting i C# eller **avkastning** i Java Script, anser körningen att den specifika körningen har slutförts. Faktureringen för Orchestrator-funktionen stoppas vid den tidpunkten. Den återupptas inte förrän nästa Orchestrator-funktion spelas upp igen. Du debiteras inte för någon tid som ägnats åt att vänta eller ge en Orchestrator-funktion.

> [!NOTE]
> Funktioner som anropar andra funktioner betraktas som en del som ett antimönster. Detta beror på ett problem som är känt som _dubbel fakturering_. När en funktion anropar en annan funktion direkt körs båda på samma gång. Den anropade funktionen kör kod aktivt medan anrops funktionen väntar på ett svar. I så fall måste du betala för den tid som den anropande funktionen ägnar åt att vänta på att den anropade funktionen ska köras.
>
> Det finns ingen dubbel fakturering i Orchestrator-funktioner. En Orchestrator-funktions fakturering stoppas när den väntar på resultatet av en aktivitets funktion eller under dirigering.

## <a name="durable-http-polling"></a>Beständig HTTP-avsökning

Orchestrator-funktioner kan göra tids krävande HTTP-anrop till externa slut punkter enligt beskrivningen i [artikeln http-funktioner](durable-functions-http-features.md). Metoden **CallHttpAsync** i C# och **callHttp** -metoden i Java Script kan internt avsöka en http-slutpunkt när den följer det [asynkrona 202-mönstret](durable-functions-http-features.md#http-202-handling).

Det finns för närvarande ingen direkt fakturering för interna HTTP-avsöknings åtgärder. Intern avsökning kan dock orsaka att Orchestrator-funktionen upprepas regelbundet. Du debiteras standard avgifter för de här interna funktions omuppspelningarna.

## <a name="azure-storage-transactions"></a>Azure Storage transaktioner

Durable Functions använder Azure Storage som standard för att hålla status beständigt, bearbeta meddelanden och hantera partitioner via BLOB-lån. Eftersom du äger det här lagrings kontot debiteras alla transaktionskostnader till din Azure-prenumeration. Mer information om Azure Storage artefakter som används av Durable Functions finns i [artikeln aktivitets nav](durable-functions-task-hubs.md).

Flera faktorer bidrar till de faktiska Azure Storage kostnader som uppstår i Durable Functions-appen:

* En enda Function-app är associerad med en enda aktivitets hubb som delar en uppsättning Azure Storage-resurser. Dessa resurser används av alla varaktiga funktioner i en Function-app. Det faktiska antalet funktioner i Function-appen har ingen inverkan på Azure Storage transaktionskostnader.
* Varje Function App-instans avsöker internt flera köer i lagrings kontot med hjälp av en backoff avsöknings algoritm. En inaktiv App-instans avsöker köerna mindre ofta än en aktiv app, vilket resulterar i färre transaktionskostnader. Mer information om hur du Durable Functions funktionen för att söka efter köer finns i [avsnittet om kö-avsökning i artikeln om prestanda och skalning](durable-functions-perf-and-scale.md#queue-polling).
* När du kör i Azure Functions förbruknings-eller Premium-prenumerationer, avsöker [Azure Functionss kontroll enheten](../event-driven-scaling.md) regelbundet alla aktivitets nav köer i bakgrunden. Om en Function-app är under ljus till måttlig skala, kommer bara en enskild skalnings kontroll instans att avsöka dessa köer. Om Function-appen skalar ut till ett stort antal instanser kan fler instanser av skalnings kontroll läggas till. Dessa ytterligare instanser av skalnings styrenheten kan öka de totala kostnaderna för Queue-transaktioner.
* Varje Function App-instans konkurrerar om en uppsättning BLOB-lån. De här instanserna kommer regelbundet att ringa till Azure-Blob Service antingen förnyade lån eller försöka skaffa nya lån. Aktivitets hubbens konfigurerade antal partitioner avgör antalet BLOB-lån. Om du skalar ut till ett större antal funktions program instanser ökar förmodligen Azure Storage transaktionskostnader som är kopplade till dessa leasing åtgärder.

Du hittar mer information om Azure Storage prissättning i [Azure Storage prissättnings](https://azure.microsoft.com/pricing/details/storage/) dokumentation. 

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Läs mer om Azure Functions priser](https://azure.microsoft.com/pricing/details/functions/)
