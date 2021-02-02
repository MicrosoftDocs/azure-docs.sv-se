---
title: Felsök vanliga fel i Azure Cosmos DB s API för mongo DB
description: Det här dokumentet innehåller information om hur du felsöker vanliga problem som uppstått i Azure Cosmos DB s API för MongoDB.
author: christopheranderson
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: troubleshooting
ms.date: 07/15/2020
ms.author: chrande
ms.openlocfilehash: f4ed242dced4798f5f416dae90ef2d6b6bde0e06
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/02/2021
ms.locfileid: "99258192"
---
# <a name="troubleshoot-common-issues-in-azure-cosmos-dbs-api-for-mongodb"></a>Felsök vanliga problem i Azure Cosmos DBs API för MongoDB
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

I följande artikel beskrivs vanliga fel och lösningar för distributioner med hjälp av Azure Cosmos DB API för MongoDB.

>[!Note]
> Azure Cosmos DB är inte värd för MongoDB-motorn. Det ger en implementering av MongoDB-kabel protokollet. Därför finns vissa av dessa fel bara i Azure Cosmos DB s API för MongoDB. 

## <a name="common-errors-and-solutions"></a>Vanliga fel och lösningar

| Kod       | Fel                | Beskrivning  | Lösning  |
|------------|----------------------|--------------|-----------|
| 2 | Index Sök vägen som motsvarar den angivna order by-posten är exkluderad eller så har inte order by-frågan ett motsvarande sammansatt index som det kan hanteras från. | Frågan begär en sortering av ett fält som inte är indexerat. | Skapa ett matchande index (eller sammansatt index) för sorterings frågan. |
| 13 | Behörighet saknas | Begäran saknar behörighet att slutföra. | Se till att du anger rätt behörigheter för din databas och samling.  |
| 16 | InvalidLength | Den angivna begäran har en ogiltig längd. | Om du använder funktionen förklaring () måste du se till att du bara anger en åtgärd. |
| 26 | NamespaceNotFound | Det går inte att hitta databasen eller samlingen som refereras i frågan. | Kontrol lera att namnet på din databas/samling matchar namnet i frågan.|
| 50 | ExceededTimeLimit | Förfrågan har överskridit tidsgränsen på 60 sekunder. |  Det kan finnas många orsaker till det här felet. En av orsakerna är att kapaciteten för för närvarande allokerade begär ande enheter inte räcker för att slutföra begäran. Det här kan du lösa genom att öka antalet förfrågningsenheter för samlingen eller databasen. I andra fall kan det här felet hanteras – genom att dela upp en stor begäran i mindre. Om du försöker utföra en Skriv åtgärd som har tagit emot det här felet kan det leda till dubbla Skriv åtgärder. <br><br>Om du försöker ta bort stora mängder data utan att påverka ru: er: <br>-Överväg att använda TTL (baserat på tidsstämpel): [förfaller data med Azure Cosmos DB s API för MongoDB](https://docs.microsoft.com/azure/cosmos-db/mongodb-time-to-live) <br>– Använd markören/batchstorleken för att utföra borttagningen. Du kan hämta ett enskilt dokument i taget och ta bort det via en loop. På så sätt kan du ta bort data långsamt utan att påverka produktions programmet.|
| 61 | ShardKeyNotFound | Dokumentet i din begäran innehöll inte samlingens Shard-nyckel (Azure Cosmos DB partitionsnyckel). | Se till att samlingens Shard-nyckel används i begäran.|
| 66 | ImmutableField | Begäran försöker ändra fältet oföränderligt | "ID"-fält är oföränderliga. Se till att din begäran inte försöker uppdatera fältet. |
| 67 | CannotCreateIndex | Det går inte att slutföra begäran om att skapa ett index. | Upp till 500 enstaka fält index kan skapas i en behållare. Upp till åtta fält kan ingå i ett sammansatt index (sammansatta index stöds i version 3.6 +). |
| 115 | CommandNotSupported | Begäran som skulle utföras stöds inte. | Ytterligare information ska ges i fel meddelandet. Om den här funktionen är viktig för dina distributioner kan du meddela oss genom att skapa ett support ärende i [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade). |
| 11000 | DuplicateKey | Shard-nyckeln (Azure Cosmos DB partitionsnyckel) för det dokument som du infogar finns redan i samlingen eller så har en unik index fält begränsning överskridits. | Använd funktionen Update () för att uppdatera ett befintligt dokument. Om den unika index fält begränsningen har överskridits infogar eller uppdaterar du dokumentet med ett fält värde som inte finns i Shard/partitionen ännu. |
| 16500 | TooManyRequests  | Det totala antalet förbrukade förfrågansenheter överskrider samlingens etablerade enhetsfrekvens och en begränsning har införts. | Överväg att skala upp det tilldelade dataflödet för en container eller en uppsättning containrar i Azure-portalen, eller försök utföra åtgärden igen. Om du [aktiverar SSR](prevent-rate-limiting-errors.md) (på Server sidan) försöker Azure Cosmos DB automatiskt att försöka utföra de begär Anden som inte fungerar på grund av det här felet. |
| 16501 | ExceededMemoryLimit | Som en tjänst för flera innehavare har åtgärden gått över klientens minnes tilldelning. | Minska åtgärds området genom mer restriktiva frågevillkor eller kontakta supporten från [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade). Exempel: `db.getCollection('users').aggregate([{$match: {name: "Andy"}}, {$sort: {age: -1}}]))` |
| 40324 | Okänt namn på pipeline-fas. | Det gick inte att identifiera fas namnet i din begäran om agg regerings pipeline. | Se till att alla sammansättnings pipelinens namn är giltiga i din begäran. |
| - | Problem med MongoDB-trådversion | De äldre versionerna av MongoDB-drivrutinerna kan inte identifiera Azure Cosmos-kontots namn i anslutnings strängarna. | Lägg till *APPNAME = @**accountName** @* i slutet av din Cosmos DBS API för MongoDB anslutnings sträng, där ***accountName** _ är ditt Cosmos DB konto namn. |
| - | Nätverks problem i MongoDB-klienten (t. ex. socket eller endOfStream-undantag)| Nätverks förfrågan misslyckades. Detta orsakas ofta av en inaktiv TCP-anslutning som MongoDB-klienten försöker använda. MongoDB-drivrutiner använder ofta anslutningspoolen, vilket resulterar i en slumpmässig anslutning som väljs från poolen som används för en begäran. Inaktiva anslutningar är vanligt vis timeout på Azure Cosmos DB slutar efter fyra minuter. | Du kan antingen göra om dessa misslyckade förfrågningar i program koden, ändra inställningarna för MongoDB-klienten (driv rutin) till Teardown inaktiva TCP-anslutningar innan tids gränsen på fyra minuter eller konfigurera dina OS keepalive-inställningar så att TCP-anslutningarna upprätthålls i ett aktivt tillstånd.<br><br>För att undvika anslutningsmeddelanden bör du ändra anslutningssträngen genom att ange maxConnectionIdleTime till 1–2 minuter.<br>-Mongo-driv rutin: Konfigurera _maxIdleTimeMS = 120000 * <br>-Node.JS: konfigurera *socketTimeoutMS = 120000*, *autoReconnect* = True, *keepAlive* = True, *keepAliveInitialDelay* = 3 minuter

## <a name="next-steps"></a>Nästa steg

- Lär dig hur du [använder Studio 3T](mongodb-mongochef.md) med Azure Cosmos DB:s API för MongoDB.
- Lär dig hur du [använder Robo 3T](mongodb-robomongo.md) med Azure Cosmos DB:s API för MongoDB.
- Utforska MongoDB-[exempel](mongodb-samples.md) med Azure Cosmos DB:s API för MongoDB.
