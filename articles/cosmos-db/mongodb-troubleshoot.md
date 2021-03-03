---
title: Felsök vanliga fel i Azure Cosmos DB s API för mongo DB
description: Det här dokumentet innehåller information om hur du felsöker vanliga problem som uppstått i Azure Cosmos DB s API för MongoDB.
author: christopheranderson
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: troubleshooting
ms.date: 07/15/2020
ms.author: chrande
ms.openlocfilehash: 13ac90ae70262f2f6781f5f40ec67da4bf74ab68
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/02/2021
ms.locfileid: "101661281"
---
# <a name="troubleshoot-common-issues-in-azure-cosmos-dbs-api-for-mongodb"></a>Felsök vanliga problem i Azure Cosmos DBs API för MongoDB
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

I följande artikel beskrivs vanliga fel och lösningar för distributioner med hjälp av Azure Cosmos DB API för MongoDB.

>[!Note]
> Azure Cosmos DB är inte värd för MongoDB-motorn. Den innehåller en implementering av MongoDB [Wire Protocol version 4,0](mongodb-feature-support-40.md), [3,6](mongodb-feature-support-36.md)och Legacy-stöd för [Wired Protocol version 3,2](mongodb-feature-support.md). Därför finns vissa av dessa fel bara i Azure Cosmos DB s API för MongoDB.

## <a name="common-errors-and-solutions"></a>Vanliga fel och lösningar

| Kod       | Fel                | Beskrivning  | Lösning  |
|------------|----------------------|--------------|-----------|
| 2 | Index Sök vägen som motsvarar den angivna order by-posten är exkluderad eller så har inte order by-frågan ett motsvarande sammansatt index som det kan hanteras från. | Frågan begär en sortering av ett fält som inte är indexerat. | Skapa ett matchande index (eller sammansatt index) för den sorteringsfråga som ska köras. |
| 13 | Behörighet saknas | Begäran saknar behörighet att slutföra. | Se till att du anger rätt behörigheter för din databas och samling.  |
| 16 | InvalidLength | Den angivna begäran har en ogiltig längd. | Om du använder funktionen förklaring () måste du se till att du bara anger en åtgärd. |
| 26 | NamespaceNotFound | Det går inte att hitta databasen eller samlingen som refereras i frågan. | Kontrollera att namnet på databasen/samlingen matchar namnet i frågan exakt.|
| 50 | ExceededTimeLimit | Förfrågan har överskridit tidsgränsen på 60 sekunder. |  Det kan finnas många orsaker till det här felet. En av orsakerna är när den allokerade förfrågningskapaciteten inte räcker för att slutföra förfrågan. Det här kan du lösa genom att öka antalet förfrågningsenheter för samlingen eller databasen. I andra fall kan du komma runt felet genom att dela upp en stor förfrågan i flera mindre. Om du försöker utföra en skrivåtgärd som har fått det här felet kan det leda till en dubblettskrivning. <br><br>Om du försöker ta bort stora mängder data utan att påverka RU: <br>-Överväg att använda TTL (baserat på tidsstämpel): [förfaller data med Azure Cosmos DB s API för MongoDB](./mongodb-time-to-live.md) <br>– Använd markören/batchstorleken för att utföra borttagningen. Du kan hämta ett enskilt dokument i taget och ta bort det via en loop. Detta kan hjälpa dig att ta bort data långsamt utan att påverka produktionsprogrammet.|
| 61 | ShardKeyNotFound | Dokumentet i din begäran innehöll inte samlingens shardnyckel (Azure Cosmos DB-partitionsnyckel). | Se till att samlingens shardnyckel används i begäran.|
| 66 | ImmutableField | Begäran försöker ändra ett oföränderligt fält | "ID"-fält är oföränderliga. Se till att din begäran inte försöker uppdatera fältet. |
| 67 | CannotCreateIndex | Det går inte att slutföra begäran om att skapa ett index. | Upp till 500 index för enstaka fält kan skapas i en container. Upp till åtta fält kan ingå i ett sammansatt index (sammansatta index stöds i version 3.6+). |
| 115 | CommandNotSupported | Begäran som skulle utföras stöds inte. | Ytterligare information måste anges i felmeddelandet. Om den här funktionen är viktig för dina distributioner kan du meddela oss genom att skapa ett support ärende i [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade). |
| 11000 | DuplicateKey | Shardnyckeln (Azure Cosmos DB-partitionsnyckel) för det dokument som du infogar finns redan i samlingen eller så har en fältbegränsning för ett unikt index överskridits. | Använd funktionen update() för att uppdatera ett befintligt dokument. Om fältbegränsningen för ett unikt index har överskridits infogar eller uppdaterar du dokumentet med ett fältvärde som inte finns i shard/partitionen ännu. |
| 16500 | TooManyRequests  | Det totala antalet förbrukade förfrågansenheter överskrider samlingens etablerade enhetsfrekvens och en begränsning har införts. | Överväg att skala upp det tilldelade dataflödet för en container eller en uppsättning containrar i Azure-portalen, eller försök utföra åtgärden igen. Om du [aktiverar SSR](prevent-rate-limiting-errors.md) (på Server sidan) försöker Azure Cosmos DB automatiskt att försöka utföra de begär Anden som inte fungerar på grund av det här felet. |
| 16501 | ExceededMemoryLimit | Som en tjänst för flera klientorganisationer har åtgärden överskridit klientens minnestilldelning. | Minska åtgärdens omfång genom mer restriktiva frågekriterier eller kontakta supporten från [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade). Exempel: `db.getCollection('users').aggregate([{$match: {name: "Andy"}}, {$sort: {age: -1}}]))` |
| 40324 | Okänt namn på pipelinefas. | Det gick inte att identifiera fasnamnet i din begäran om sammansättningspipeline. | Se till att alla namn på sammansättningspipeline är giltiga i din begäran. |
| - | Problem med MongoDB-trådversion | De äldre versionerna av MongoDB-drivrutinerna kan inte identifiera namnet på Azure Cosmos-kontot i anslutningssträngarna. | Lägg till *APPNAME = @**accountName** @* i slutet av din Cosmos DBS API för MongoDB-anslutningssträng, där ***accountName*** är ditt Cosmos DB konto namn. |
| - | Nätverksproblem på MongoDB-klienten (t.ex. socket- eller endOfStream-undantag)| Nätverksbegäran misslyckades. Detta orsakas ofta av en inaktiv TCP-anslutning som MongoDB-klienten försöker använda. MongoDB-drivrutiner använder ofta anslutningspoolen, vilket resulterar i en slumpmässig anslutning som väljs från poolen som används för en begäran. Inaktiva anslutningar når vanligtvis tidsgränsen på Azure Cosmos DB-sidan efter fyra minuter. | Du kan antingen göra om dessa misslyckade förfrågningar i program koden, ändra inställningarna för MongoDB-klienten (driv rutin) till Teardown inaktiva TCP-anslutningar innan tids gränsen på fyra minuter eller konfigurera dina OS keepalive-inställningar så att TCP-anslutningarna upprätthålls i ett aktivt tillstånd.<br><br>För att undvika anslutningsmeddelanden bör du ändra anslutningssträngen genom att ange maxConnectionIdleTime till 1–2 minuter.<br>-Mongo-driv rutin: konfigurera *maxIdleTimeMS = 120000* <br>-Node.JS: konfigurera *socketTimeoutMS = 120000*, *autoReconnect* = True, *keepAlive* = True, *keepAliveInitialDelay* = 3 minuter

## <a name="next-steps"></a>Nästa steg

- Lär dig hur du [använder Studio 3T](mongodb-mongochef.md) med Azure Cosmos DB:s API för MongoDB.
- Lär dig hur du [använder Robo 3T](mongodb-robomongo.md) med Azure Cosmos DB:s API för MongoDB.
- Utforska MongoDB-[exempel](mongodb-samples.md) med Azure Cosmos DB:s API för MongoDB.