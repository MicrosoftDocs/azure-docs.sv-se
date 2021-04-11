---
title: Lista över förhandsgranskningsfunktioner
titleSuffix: Azure Cognitive Search
description: För hands versions funktionerna släpps så att kunderna kan ge feedback om deras design och verktyg. Den här artikeln är en omfattande lista över alla funktioner som för närvarande finns i för hands version.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/25/2021
ms.openlocfilehash: e0bbc9fc1e6259b70e1f1d46b545300a568601d2
ms.sourcegitcommit: 5fd1f72a96f4f343543072eadd7cdec52e86511e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/01/2021
ms.locfileid: "106109806"
---
# <a name="preview-features-in-azure-cognitive-search"></a>Förhands gransknings funktioner i Azure Kognitiv sökning

Den här artikeln är en omfattande lista över alla funktioner som finns i en offentlig för hands version. För hands versions funktionerna tillhandahålls utan service nivå avtal och rekommenderas inte för produktions arbets belastningar. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Förhands gransknings funktioner som övergår till allmän tillgänglighet tas bort från listan. Om en funktion inte visas i listan nedan kan du anta att den är allmänt tillgänglig. Meddelanden om allmän tillgänglighet finns i [tjänst uppdateringar](https://azure.microsoft.com/updates/?product=search) eller [Nyheter](whats-new.md).

|Zoomfunktionen&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  | Kategori | Beskrivning | Tillgänglighet  |
|---------|------------------|-------------|---------------|
| [**Semantisk sökning**](semantic-search-overview.md) | Relevans (Poängsättning) | Semantisk rangordning av resultat, under texter och svar. | [Sök REST API 2020-06-30 – för hands version](/rest/api/searchservice/preview-api/search-documents) och Sök Utforskaren (portal). |
| [**stavningskontroll**](cognitive-search-aml-skill.md) | Fråga | Valfri stavnings korrigering för frågeterm-indata för enkla, fullständiga och semantiska frågor. | [Sök REST API 2020-06-30 – för hands version](/rest/api/searchservice/preview-api/search-documents) |
| [**SharePoint Online-indexerare**](search-howto-index-sharepoint-online.md) | Indexerare data Källa | Ny data källa för indexerad indexering av SharePoint-innehåll. | [Sök REST API 2020-06-30 – för hands version](/rest/api/searchservice/preview-api/create-indexer) |
| [**Azure Machine Learning (AML) kunskap**](cognitive-search-aml-skill.md) | AI-berikning| En ny färdighets typ som integrerar en inferencing-slutpunkt från Azure Machine Learning. Kom igång med [den här självstudien](cognitive-search-tutorial-aml-custom-skill.md). | Använd [sök REST API 2020-06-30 – för hands version](/rest/api/searchservice/) eller 2019-05-06 – för hands version. Det finns även i portalen, i färdigheter design, förutsatt att Kognitiv sökning och Azure ML-tjänster distribueras i samma prenumeration. |
| [**featuresMode-parameter**](/rest/api/searchservice/preview-api/search-documents#query-parameters) | Relevans (Poängsättning) | Utökning av relevans för att inkludera information: per fält likhets poäng, per fält villkors frekvens och per fält antal unika tokens matchade. Du kan använda dessa data punkter i [anpassade bedömnings lösningar](https://github.com/Azure-Samples/search-ranking-tutorial). | Lägg till den här Frågeparametern med [Sök dokument (rest)](/rest/api/searchservice/preview-api/search-documents) med API-version = 2020-06 -30-preview eller 2019-05-06-Preview. |
| [**Felsöka sessioner**](cognitive-search-debug-session.md) | Portal, AI-anrikning (färdigheter) | Ett färdigheter-redigeringsprogram i sessionen som används för att undersöka och lösa problem med en färdigheter. Korrigeringar som tillämpas under en felsökningssession kan sparas i en färdigheter i tjänsten. | Endast Portal, med länkar mellan sidor på översikts sidan för att öppna en felsökningssession. |
| [**Mjuk borttagning av inbyggd BLOB**](search-howto-index-changed-deleted-blobs.md) | Indexerare, Azure-blobar| Azure Blob Storage-indexeraren i Azure Kognitiv sökning identifierar blobbar som är i ett mjukt borttaget läge och tar bort motsvarande sökdokument under indexeringen. | Lägg till den här konfigurations inställningen med hjälp av [skapa indexerare (rest)](/rest/api/searchservice/create-indexer) med API-version = 2020-06 -30-Preview eller API-version = 2019-05 -06-Preview. |
| [**Kompetens för identifiering av PII**](cognitive-search-skill-pii-detection.md) | AI-anrikning (färdigheter) | En kognitiv kunskap som används vid indexering som extraherar personlig information från en indata-text och ger dig möjlighet att maskera den från den texten på olika sätt. | Referera till förhands gransknings kunskapen med färdigheter-redigeraren i portalen eller [skapa färdigheter (rest)](/rest/api/searchservice/create-skillset) med API-version = 2020-06 -30-Preview eller API-version = 2019-05 -06-Preview. |
| [**Inkrementellt berikande**](cognitive-search-incremental-indexing-conceptual.md) | Indexerings konfiguration| Lägger till cachelagring i en anriknings pipeline, så att du kan återanvända befintliga utdata om en viss ändring, till exempel en uppdatering av en färdigheter eller ett annat objekt, inte ändrar innehållet. Cachelagring gäller endast för berikade dokument som produceras av en färdigheter.| Lägg till den här konfigurations inställningen med hjälp av [skapa indexerare (rest)](/rest/api/searchservice/create-indexer) med API-version = 2020-06 -30-Preview eller API-version = 2019-05 -06-Preview. |
| [**Cosmos DB indexerare: MongoDB-API, Gremlin-API, API för Cassandra**](search-howto-index-cosmosdb.md) | Indexerare data Källa | För Cosmos DB är SQL API allmänt tillgängligt, men MongoDB-, Gremlin-och Cassandra-API: er är i för hands version. | [Registrera först](https://aka.ms/azure-cognitive-search/indexer-preview) för Gremlin och Cassandra så att stöd kan aktive ras för din prenumeration på Server delen. MongoDB data källor kan konfigureras i portalen. Annars stöds konfigurationen av data källan för alla tre API: er med hjälp av [skapa data källa (rest)](/rest/api/searchservice/create-data-source) med API-version = 2020-06 -30-Preview eller API-version = 2019-05 -06 – för hands version. |
|  [**Azure Data Lake Storage Gen2 indexerare**](search-howto-index-azure-data-lake-storage.md) | Indexerare data Källa | Indexera innehåll och metadata från Data Lake Storage Gen2.| [Registrering](https://aka.ms/azure-cognitive-search/indexer-preview) krävs för att stöd ska kunna aktive ras för din prenumeration på Server delen. Åtkomst till den här data källan med hjälp av [skapa data källa (rest)](/rest/api/searchservice/create-data-source) med API-version = 2020-06 -30-Preview eller API-version = 2019-05 -06-Preview. |
| [**moreLikeThis**](search-more-like-this.md) | Fråga | Söker efter dokument som är relevanta för ett enskilt dokument. Den här funktionen finns i tidigare för hands versioner. | Lägg till den här frågeparametern i [search Documents (rest)-](/rest/api/searchservice/search-documents) anrop med API-version = 2020-06 -30 – för hands version, 2019-05-06-för hands version, 2016-09-01-för hands version eller 2017-11-11-för hands version. |

## <a name="how-to-call-a-preview-rest-api"></a>Så här anropar du en förhands granskning REST API

Azure Kognitiv sökning alltid för hands versioner av experimentella funktioner via REST API först, sedan till hands versioner av .NET SDK.

För hands versions funktionerna är tillgängliga för testning och experimentering, med målet att samla in feedback om funktions design och implementering. Av den anledningen kan förhands gransknings funktioner ändras med tiden, eventuellt på sätt som bryter mot bakåtkompatibilitet. Detta är i motsats till funktioner i en GA-version, som är stabila och inte kan ändras med undantag för små bakåtkompatibla korrigeringar och förbättringar. För hands versions funktioner görs dessutom inte alltid till en GA-version.

Vissa för hands versions funktioner kan vara tillgängliga i portalen och .NET SDK, men REST API alltid har för hands versions funktioner.

+ För Sök åtgärder [**`2020-06-30-Preview`**](/rest/api/searchservice/index-preview) är den aktuella för hands versionen.

+ För hanterings åtgärder [**`2019-10-01-Preview`**](/rest/api/searchmanagement/index-2019-10-01-preview) är den aktuella för hands versionen.

Äldre förhands visningar fungerar fortfarande, men blir inaktuella över tid. Om dina kod anrop `api-version=2019-05-06-Preview` eller `api-version=2016-09-01-Preview` eller `api-version=2017-11-11-Preview` , är dessa anrop fortfarande giltiga. Men endast den nyaste för hands versionen har uppdaterats med förbättringar.

Följande exempel på syntax illustrerar ett anrop till för hands versionen av API-versionen.

```HTTP
POST https://[service name].search.windows.net/indexes/hotels-idx/docs/search?api-version=2020-06-30-Preview  
  Content-Type: application/json  
  api-key: [admin key]
```

Azure Kognitiv sökning-tjänsten är tillgänglig i flera versioner. Mer information finns i [API-versioner](search-api-versions.md).

## <a name="next-steps"></a>Nästa steg

Läs dokumentationen om att söka i REST-förhandsgranskning API-referens. Om du stöter på problem ber vi oss om hjälp med [Stack Overflow](https://stackoverflow.com/) eller [kontakta supporten](https://azure.microsoft.com/support/community/?product=search).

> [!div class="nextstepaction"]
> [Sök tjänst REST API referens (för hands version)](/rest/api/searchservice/index-preview)