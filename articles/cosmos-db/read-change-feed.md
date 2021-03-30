---
title: Läsa ändringsflödet i Azure Cosmos DB
description: I den här artikeln beskrivs olika alternativ som är tillgängliga för läsning och åtkomst av ändrings flöden i Azure Cosmos DB.
author: timsander1
ms.author: tisande
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 10/27/2020
ms.reviewer: sngun
ms.openlocfilehash: 7021367e1230573343ddf57ccd399d998ad5280e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "93339282"
---
# <a name="reading-azure-cosmos-db-change-feed"></a>Läsa ändringsflödet i Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Du kan arbeta med Azure Cosmos DB ändra feed med antingen en push-modell eller en pull-modell. Med en push-modell skickas push-meddelanden till en klient som har affärs logik för bearbetning av detta arbete. Komplexiteten vid sökning efter arbete och lagrings tillstånd för det senaste bearbetade arbetet hanteras dock i bytet av byte av feed.

Med en pull-modell måste klienten Hämta arbetet från servern. Klienten, i det här fallet, innehåller inte bara affärs logik för bearbetning av arbete, utan även lagring av tillstånd för det senast bearbetade arbetet, hantering av belastnings utjämning över flera klienter bearbetnings arbete parallellt och hantering av fel.

När du läser från Azure Cosmos DB ändra feed rekommenderar vi vanligt vis att du använder en push-modell eftersom du inte behöver oroa dig för:

- Avsöker ändrings flödet för framtida ändringar.
- Lagrar tillstånd för den senast bearbetade ändringen. När du läser från ändrings flödet lagras det automatiskt i en [container](change-feed-processor.md#components-of-the-change-feed-processor).
- Belastnings utjämning över flera klienter som förbrukar ändringar. Till exempel om en klient inte kan fortsätta att bearbeta ändringar och en annan har tillgänglig kapacitet.
- [Hanterings fel](change-feed-processor.md#error-handling). Försök till exempel automatiskt att försöka utföra ändringar som inte bearbetades korrekt efter ett ohanterat undantag i kod eller ett tillfälligt nätverks problem.

De flesta scenarier som använder Azure Cosmos DB ändra feed kommer att använda en av alternativen för push-modellen. Det finns dock vissa scenarier där du kanske vill ha en extra låg nivå kontroll av pull-modellen. Dessa omfattar:

- Läser ändringar från en viss partitionsnyckel
- Styra i vilken takt klienten får ändringar för bearbetning
- Göra en engångs läsning av befintliga data i ändrings flödet (till exempel för att göra en datamigrering)

## <a name="reading-change-feed-with-a-push-model"></a>Läser ändrings flöde med en push-modell

Att använda en push-modell är det enklaste sättet att läsa från ändrings flödet. Det finns två sätt som du kan läsa från ändra feed med en push-modell: [Azure Functions Cosmos DB utlösare](change-feed-functions.md) och [bibliotek för ändrings flödes processor](change-feed-processor.md). Azure Functions använder processorn för Change feeds i bakgrunden, så att dessa båda är mycket likartade sätt att läsa ändrings flödet. Tänk på Azure Functions som helt enkelt en värd plattform för byte-processorn, inte ett helt annat sätt att läsa ändrings flödet.

### <a name="azure-functions"></a>Azure Functions

Azure Functions är det enklaste alternativet om du precis har börjat använda ändrings flödet. På grund av dess enkelhet är det också det rekommenderade alternativet för de flesta användnings fall av ändrings flöden. När du skapar en Azure Functions utlösare för Azure Cosmos DB väljer du den behållare som ska anslutas, och Azure-funktionen utlöses när det finns en ändring i behållaren. Eftersom Azure Functions använder processorn för byte av byte i bakgrunden, parallelizes ändrings bearbetningen automatiskt mellan dina behållares [partitioner](partitioning-overview.md).

Att utveckla med Azure Functions är en enkel upplevelse och kan vara snabbare än att distribuera bytet av byte av byte på egen hand. Utlösare kan skapas med hjälp av Azure Functions portalen eller via programmering med SDK: er. Visual Studio och VS Code ger stöd för att skriva Azure Functions, och du kan även använda Azure Functions CLI för utveckling över plattformar. Du kan skriva och felsöka koden på Skriv bordet och sedan distribuera funktionen med ett klick. Läs mer i [data behandling utan server med hjälp av Azure Functions](serverless-computing-database.md) och [Använd ändra feed med Azure Functions](change-feed-functions.md) artiklar.

### <a name="change-feed-processor-library"></a>Ändra bibliotek för flödes processor

Processorn för ändrings flöden ger dig större kontroll över ändrings flödet och döljer fortfarande den viktigaste komplexiteten. Biblioteket för ändrings flödets processor följer mönstret för observationer, där din bearbetnings funktion anropas av biblioteket. Biblioteket för ändrings matnings processor söker automatiskt efter ändringar och, om det finns ändringar, "push" dessa till klienten. Om du har en hög data flödes ändrings flöde kan du instansiera flera klienter för att läsa ändrings flödet. Med biblioteket Change feed processor kan du automatiskt dela in belastningen mellan de olika klienterna. Du behöver inte implementera någon logik för belastnings utjämning över flera klienter eller någon logik för att upprätthålla låne statusen.

I biblioteket Change feed processor garanteras en "minst en gång"-leverans av alla ändringar. Om du använder biblioteket Change feed processor, kommer din bearbetnings funktion att anropas för alla objekt i ändrings flödet. Om det finns ett ohanterat undantag i affärs logiken i din bearbetnings funktion kommer de misslyckade ändringarna att göras tills de har bearbetats. Om du vill förhindra att en ändrings flödes processor får "fastnat" och försöker igen med samma ändringar, lägger du till logik i din bearbetnings funktion för att skriva dokument, vid undantag, till en kö för obeställbara meddelanden. Läs mer om [fel hantering](change-feed-processor.md#error-handling).

I Azure Functions är rekommendationen för att hantera fel samma. Du bör fortfarande lägga till logik i din ombuds kod för att skriva dokument, vid undantag, till en kö för obeställbara meddelanden. Men om det finns ett ohanterat undantag i din Azure-funktion kommer inte den ändring som genererade undantaget automatiskt att göras om. Om det finns ett ohanterat undantag i affärs logiken kommer Azure-funktionen att gå vidare för att bearbeta nästa ändring. Azure-funktionen försöker inte igen samma ändring som misslyckades.

Precis som för Azure Functions är det enkelt att utveckla med det förändrade flödets processor bibliotek. Du är dock ansvarig för att distribuera en eller flera värdar för bytet av Change-feed. En värd är en program instans som använder en Change feed-processor för att lyssna efter ändringar. Även om Azure Functions har funktioner för automatisk skalning, ansvarar du för att skala dina värdar. Läs mer i [använda Change feed-processorn](change-feed-processor.md#dynamic-scaling). Biblioteket Change feed processor är en del av [Azure Cosmos DB SDK v3](https://github.com/Azure/azure-cosmos-dotnet-v3).

## <a name="reading-change-feed-with-a-pull-model"></a>Läser ändrings flöde med en pull-modell

Med [pull-modellen för ändrings flöden](change-feed-pull-model.md) kan du använda ändrings flödet i din egen takt. Ändringar måste begäras av klienten och det finns ingen automatisk sökning efter ändringar. Om du vill att "bok märket" ska vara permanent "den senast bearbetade ändringen (liknar push-modellens leasing behållare) måste du [Spara en fortsättnings-token](change-feed-pull-model.md#saving-continuation-tokens).

Med hjälp av pull-modellen för ändrings flöden får du mer låg kontroll över ändrings flödet. När du läser ändrings flödet med pull-modellen har du tre alternativ:

- Läs ändringar för en hel behållare
- Läs ändringar för en speciell [FeedRange](change-feed-pull-model.md#using-feedrange-for-parallelization)
- Läs ändringar för ett enskilt partitionerings nyckel värde

Du kan parallellisera bearbetning av ändringar över flera klienter, precis som du kan med ändra feed-processorn. Pull-modellen hanterar dock inte automatiskt belastnings utjämning mellan klienter. När du använder pull-modellen för att parallellisera bearbetning av ändrings flödet får du först en lista över FeedRanges. En FeedRange omfattar en rad nyckel värden. Du måste ha en Orchestrator-process som erhåller FeedRanges och distribuerar dem mellan dina datorer. Du kan sedan använda dessa FeedRanges om du vill att flera datorer ska kunna läsa ändrings flödet parallellt.

Det finns ingen fördefinierad "minst en gång"-leverans garanti med pull-modellen. Hämtnings modellen ger dig kontroll på låg nivå för att bestämma hur du vill hantera fel.

> [!NOTE]
> Pull-modellen för ändrings flöden är för närvarande [en för hands version i Azure Cosmos dB .NET SDK](https://www.nuget.org/packages/Microsoft.Azure.Cosmos/3.15.0-preview) . För hands versionen är inte tillgänglig ännu för andra SDK-versioner.

## <a name="change-feed-in-apis-for-cassandra-and-mongodb"></a>Ändra feed i API: er för Cassandra och MongoDB

Funktionen för att ändra feed fungerar som ändrings strömmar i MongoDB-API och fråga med predikat i API för Cassandra. Mer information om implementerings informationen för MongoDB-API finns i [ändrings strömmar i Azure Cosmos DB API för MongoDB](mongodb-change-streams.md).

Inbyggd Apache Cassandra tillhandahåller registrering av ändrings data (CDC), en mekanism för att flagga specifika tabeller för arkivering och avvisning av skrivningar till dessa tabeller när en konfigurerbar storlek-på disk för CDC-loggen nås. Funktionen ändra feed i Azure Cosmos DB API för Cassandra förbättrar möjligheten att fråga ändringarna med predikat via CQL. Mer information om implementerings informationen finns i [ändra feed i Azure Cosmos DB API för Cassandra](cassandra-change-feed.md).

## <a name="next-steps"></a>Nästa steg

Nu kan du fortsätta att lära dig mer om ändrings flöden i följande artiklar:

* [Översikt över ändra feed](change-feed.md)
* [Använda ändra feed med Azure Functions](change-feed-functions.md)
* [Använda biblioteket Change feed processor](change-feed-processor.md)
