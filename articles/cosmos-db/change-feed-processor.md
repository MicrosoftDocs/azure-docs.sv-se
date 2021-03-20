---
title: Ändringsflödesprocessorn i Azure Cosmos DB
description: Lär dig hur du använder Azure Cosmos DB ändra feed-processorn för att läsa ändrings flödet, komponenterna i bytet av Change feed
author: timsander1
ms.author: tisande
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 10/12/2020
ms.reviewer: sngun
ms.custom: devx-track-csharp
ms.openlocfilehash: 409b51682700a8b13b2840f171642bdcbee6f6d2
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "93340234"
---
# <a name="change-feed-processor-in-azure-cosmos-db"></a>Ändringsflödesprocessorn i Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Processorn för förändrings flödet är en del av [Azure Cosmos DB SDK v3](https://github.com/Azure/azure-cosmos-dotnet-v3). Det fören klar processen med att läsa ändrings flödet och att distribuera händelse bearbetningen över flera konsumenter på ett effektivt sätt.

Den största fördelen med att ändra flödes processor bibliotek är dess feltoleranta beteende som säkerställer en "minst en gång"-leverans av alla händelser i ändrings flödet.

## <a name="components-of-the-change-feed-processor"></a>Komponenter i processorn för Change-feed

Det finns fyra huvudkomponenter i implementeringen av ändringsflödesprocessorn:

1. **Den övervakade containern:** Den övervakade containern har de data som ändringsflödet genereras från. Infogningar och uppdateringar till den övervakade containern visas i containerns ändringsflöde.

1. **Lånecontainern:** Lånecontainern fungerar som en lagerplats för tillstånd och samordnar bearbetning av ändringsflödet över flera arbetsroller. Lånecontainern kan lagras i samma konto som den övervakade containern eller i ett separat konto.

1. **Värden:** En värd är en programinstans som använder ändringsflödesprocessorn för att lyssna efter ändringar. Flera instanser med samma lånekonfiguration kan köras parallellt, men varje instans bör ha ett unikt **instansnamn**.

1. **Ombudet:** Ombudet är den kod som definierar vad du, utvecklaren, vill göra med varje batch av ändringar som ändringsflödesprocessor läser. 

För att bättre förstå hur dessa fyra delar av ändra flödes processor fungerar tillsammans, ska vi titta på ett exempel i följande diagram. Den övervakade behållaren lagrar dokument och använder "ort" som partitionsnyckel. Vi ser att värdena för partitionsnyckel distribueras i intervall som innehåller objekt. Det finns två värd instanser och processorn för ändrings flöden tilldelar olika intervall med nyckel värden till varje instans för att maximera beräknings distributionen. Varje intervall läses parallellt och förloppet bevaras separat från andra intervall i leasing behållaren.

:::image type="content" source="./media/change-feed-processor/changefeedprocessor.png" alt-text="Exempel på ändring av flödes processor" border="false":::

## <a name="implementing-the-change-feed-processor"></a>Implementera bearbetning av Change feeds-processorn

Posten är alltid den övervakade behållaren, från en `Container` instans som du anropar `GetChangeFeedProcessorBuilder` :

[!code-csharp[Main](~/samples-cosmosdb-dotnet-change-feed-processor/src/Program.cs?name=DefineProcessor)]

Där den första parametern är ett distinkt namn som beskriver målet för den här processorn och det andra namnet är den ombuds implementering som ska hantera ändringarna. 

Ett exempel på ett ombud skulle vara:


[!code-csharp[Main](~/samples-cosmosdb-dotnet-change-feed-processor/src/Program.cs?name=Delegate)]

Slutligen definierar du ett namn för processor instansen med `WithInstanceName` och som är behållaren för att underhålla låne status med `WithLeaseContainer` .

`Build`Genom att anropa får du den processor instans som du kan starta genom att anropa `StartAsync` .

## <a name="processing-life-cycle"></a>Bearbetnings livs cykel

Den normala livscykeln för en värdinstans är:

1. Läs ändrings flödet.
1. Om det inte finns några ändringar kan du försätta i vilo läge under en fördefinierad tid (anpassningsbar med `WithPollInterval` i-verktyget) och gå till #1.
1. Om det finns ändringar skickar du dem till **ombudet**.
1. När ombudet har slutfört bearbetningen **av ändringarna uppdaterar** du leasing lagret med den senaste bearbetade tidpunkten och går till #1.

## <a name="error-handling"></a>Felhantering

Processorn för ändrings flöden är elastisk för användar kod fel. Det innebär att om din ombuds implementering har ett ohanterat undantag (steg #4), stoppas tråd bearbetningen av den aktuella batchen av ändringar och en ny tråd skapas. Den nya tråden kontrollerar vilken som är den senaste tidpunkten som leasing lagret har för intervallet av partitionsnyckel, och sedan startar om därifrån, skickar samma batch med ändringar i delegaten. Det här beteendet fortsätter tills ditt ombud bearbetar ändringarna korrekt och det är orsaken till att den ändrade feed-processorn har en "minst en gång"-garanti, eftersom om Delegerings koden genererar ett undantag kommer den att försöka utföra batchen igen.

För att förhindra att en ändrings flödes processor får "fastnat" och samtidigt prova samma batch med ändringar, bör du lägga till logik i din ombuds kod för att skriva dokument, vid undantag, till en kö för obeställbara meddelanden. Den här designen garanterar att du kan hålla koll på obearbetade ändringar samtidigt som du fortfarande kan fortsätta att bearbeta framtida ändringar. Kön för obeställbara meddelanden kan vara en annan Cosmos-behållare. Det exakta data lagret spelar ingen roll, bara att de obearbetade ändringarna är bestående.

Dessutom kan du använda [uppskattningen ändra feed](how-to-use-change-feed-estimator.md) för att övervaka förloppet för dina byte av Change feed processor när de läser ändrings flödet. Du kan använda den här uppskattningen för att förstå om din process för ändrings flöden är "fastnad" eller när du har använt resurser som processor, minne och nätverks bandbredd.

## <a name="deployment-unit"></a>Distributions enhet

En distributions enhet för en enda ändrings container processor består av en eller flera instanser med samma `processorName` konfiguration som den lånade behållaren. Du kan ha många distributions enheter där var och en har ett annat affärs flöde för ändringarna och varje distributions enhet som består av en eller flera instanser. 

Du kan till exempel ha en distributions enhet som utlöser ett externt API när som helst när du gör en ändring i din behållare. En annan distributions enhet kan flytta data i real tid varje tillfälle då en ändring sker. När en ändring sker i den övervakade behållaren får alla dina distributions enheter ett meddelande.

## <a name="dynamic-scaling"></a>Dynamisk skalning

Som nämnts tidigare kan du ha en eller flera instanser inom en distributions enhet. För att kunna dra nytta av beräknings distributionen inom distributions enheten är de enda viktiga kraven:

1. Alla instanser ska ha samma lånecontainerkonfiguration.
1. Alla instanser bör ha samma `processorName` .
1. Varje instans måste ha ett unikt instansnamn (`WithInstanceName`).

Om dessa tre villkor är uppfyllda, använder Change container-processorn med en algoritm för likvärdig distribution och distribuerar alla lån i leasing behållaren över alla instanser som körs av distributions enheten och parallellisera-beräkning. Ett lån kan bara ägas av en instans vid en specifik tidpunkt, så det maximala antalet instanser är lika med antalet lån.

Antalet instanser kan utökas och krympas dynamiskt, och processorn för förändrings flödet justerar belastningen dynamiskt genom att distribuera om enligt detta.

Dessutom kan Processorn för förändrings flöden dynamiskt justera till behållarens skala på grund av data flöde eller lagrings utrymme. När din behållare växer, hanterar Change feed-processorn transparent dessa scenarier genom att dynamiskt öka lånet och distribuera nya lån mellan befintliga instanser.

## <a name="change-feed-and-provisioned-throughput"></a>Ändra feed och tillhandahållet data flöde

Att ändra feed Läs åtgärder på den övervakade behållaren använder ru: er. 

Åtgärder på leasing containern använder ru: er. Ju högre antal instanser som använder samma leasing behållare, desto högre är den potentiella RU-förbrukningen. Kom ihåg att övervaka din RU-förbrukning på behållaren lån om du bestämmer dig för att skala och öka antalet instanser.

## <a name="starting-time"></a>Start tid

Som standard när en ändrings flödes processor startar första gången initieras behållaren lån och startar dess [bearbetnings livs cykel](#processing-life-cycle). Eventuella ändringar som skett i den övervakade behållaren innan processorn för ändrings flöden initierades för första gången identifieras inte.

### <a name="reading-from-a-previous-date-and-time"></a>Läser från ett tidigare datum och en tidigare tidpunkt

Det är möjligt att initiera processen för att ändra feed för att läsa ändringar som börjar vid ett **visst datum och en angiven tidpunkt**, genom att skicka en instans av en `DateTime` till `WithStartTime` Builder-tillägget:

[!code-csharp[Main](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed/Program.cs?name=TimeInitialization)]

Processorn för ändrings flöden initieras för det aktuella datumet och den aktuella tiden och börjar läsa de ändringar som skett efter.

### <a name="reading-from-the-beginning"></a>Läser från början

I andra scenarier, t. ex. datamigrering eller analys av hela historiken för en behållare, måste vi läsa ändrings flödet från **början av behållarens livs längd**. Vi kan göra det med hjälp av `WithStartTime` tillägget Builder, men genom att skicka `DateTime.MinValue.ToUniversalTime()` , vilket genererar UTC-representationen av det lägsta `DateTime` värdet, så här:

[!code-csharp[Main](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed/Program.cs?name=StartFromBeginningInitialization)]

Processorn för ändrings flöden initieras och börjar läsa ändringar från början av containerns livs längd.

> [!NOTE]
> Dessa anpassnings alternativ fungerar bara för att konfigurera start tidpunkten för bytet av Change feed. När låne containern har initierats för första gången har ändringar gjorts.

## <a name="where-to-host-the-change-feed-processor"></a>Var du ska vara värd för Change feed-processorn

Processorn för ändrings flöden kan finnas på alla plattformar som stöder tids krävande processer eller uppgifter:

* En kontinuerlig körning av ett [Azure-webbjobb](/learn/modules/run-web-app-background-task-with-webjobs/).
* En process i en [virtuell Azure-dator](/azure/architecture/best-practices/background-jobs#azure-virtual-machines).
* Ett bakgrunds jobb i [Azure Kubernetes-tjänsten](/azure/architecture/best-practices/background-jobs#azure-kubernetes-service).
* En [värdbaserad ASP.net-tjänst](/aspnet/core/fundamentals/host/hosted-services).

Medan byte av flödes processor kan köras i korta levde miljöer, eftersom Lease-behållaren upprätthåller tillstånd, kommer start cykeln för de här miljöerna att lägga till fördröjning för att ta emot meddelanden (på grund av omkostnader för att starta processorn varje gång miljön startas).

## <a name="additional-resources"></a>Ytterligare resurser

* [Azure Cosmos DB SDK](sql-api-sdk-dotnet.md)
* [Slutför exempel programmet på GitHub](https://github.com/Azure-Samples/cosmos-dotnet-change-feed-processor)
* [Ytterligare användnings exempel på GitHub](https://github.com/Azure/azure-cosmos-dotnet-v3/tree/master/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed)
* [Cosmos DB workshop Labs för byte av flödes processor](https://azurecosmosdb.github.io/labs/dotnet/labs/08-change_feed_with_azure_functions.html#consume-cosmos-db-change-feed-via-the-change-feed-processor)

## <a name="next-steps"></a>Nästa steg

Nu kan du fortsätta med att lära dig mer om att ändra flödes processor i följande artiklar:

* [Översikt över ändra feed](change-feed.md)
* [Hämtningsmodell för ändringsflöde](change-feed-pull-model.md)
* [Så här migrerar du från biblioteket Change feed processor](how-to-migrate-from-change-feed-library.md)
* [Använda ändringsflödesövervakaren](how-to-use-change-feed-estimator.md)
* [Starttid för ändringsflödesprocessor](#starting-time)