---
title: Skapa en indexerare
titleSuffix: Azure Cognitive Search
description: Ange egenskaper för en indexerare för att fastställa data ursprung och mål. Du kan ange parametrar för att ändra körnings beteenden.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/28/2021
ms.openlocfilehash: 5fc47599d09e5be60311dbda15868d87de4d91d2
ms.sourcegitcommit: b85ce02785edc13d7fb8eba29ea8027e614c52a2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/03/2021
ms.locfileid: "99509392"
---
# <a name="creating-indexers-in-azure-cognitive-search"></a>Skapa indexerare i Azure Kognitiv sökning

En Sök Indexer innehåller ett automatiserat arbets flöde för att överföra dokument och innehåll från en extern data källa till ett sökindex på din Sök tjänst. Som ursprungligen har utformats extraherar text och metadata från en Azure-datakälla, serialiserar dokument till JSON och överför de resulterande dokumenten till en sökmotor för indexering. Det har sedan utökats för att stödja [AI-anrikning](cognitive-search-concept-intro.md) för djup innehålls bearbetning. 

Användningen av indexerare minskar avsevärt mängden och komplexiteten i koden som du behöver skriva. Den här artikeln fokuserar på Mechanics för att skapa en indexerare som förberedelse för mer avancerade arbeten med verksamhetsspecifika indexerare och [färdighetsuppsättningar](cognitive-search-working-with-skillsets.md).

## <a name="whats-an-indexer-definition"></a>Vad är en indexerare-definition?

Indexerare används för textbaserade index som hämtar alfanumeriskt innehåll från käll fält till index fält eller AI-baserad bearbetning som analyserar en ej differentierad text för struktur eller analyserar bilder för text och information, vilket även lägger till innehållet i ett index. Följande index definitioner är typiska för vad du kan skapa för båda scenarierna.

### <a name="indexers-for-text-content"></a>Indexerare för text innehåll

Det ursprungliga syftet med en indexerare var att förenkla den komplexa processen med att läsa in ett index genom att tillhandahålla en mekanism för att ansluta till och läsa text och numeriskt innehåll från fält i en data källa, serialisera innehållet som JSON-dokument och lämna dokumenten till sökmotorn för indexering. Detta är fortfarande ett primärt användnings fall och för den här åtgärden måste du skapa en indexerare med de egenskaper som definierats i följande exempel.

```json
{
  "name": (required) String that uniquely identifies the indexer,
  "dataSourceName": (required) String indicated which existing data source to use,
  "targetIndexName": (required) String,
  "parameters": {
    "batchSize": null,
    "maxFailedItems": null,
    "maxFailedItemsPerBatch": null
  },
  "fieldMappings": [ optional unless there are field discrepancies that need resolution]
}
```

**`name`** Egenskaperna, **`dataSourceName`** och **`targetIndexName`** är obligatoriska, och beroende på hur du skapar indexeraren måste både data källan och indexet redan finnas på tjänsten innan du kan köra indexeraren. 

**`parameters`** Egenskapen ändrar körnings tids beteenden, till exempel hur många fel som ska accepteras innan hela jobbet avbryts. Parametrar är också hur du anger verksamhetsspecifika beteenden. Om källan till exempel är Blob Storage kan du ange en parameter som filtrerar på fil namns tillägg: `"parameters" : { "configuration" : { "indexedFileNameExtensions" : ".pdf,.docx" } }` .

**`field mappings`** Egenskapen används för att explicit mappa käll-till-mål-fält om dessa fält skiljer sig efter namn eller typ. Andra egenskaper (visas inte), används för att [Ange ett schema](search-howto-schedule-indexers.md), skapa indexeraren i inaktiverat tillstånd eller ange en [krypterings nyckel](search-security-manage-encryption-keys.md) för kompletterande kryptering av data i vila.

### <a name="indexers-for-ai-indexing"></a>Indexerare för AI-indexering

Eftersom indexerare är den mekanism som används av en Sök tjänst för att utföra utgående begär Anden, utökat indexerare för att stödja AI-anrikninger, lägga till infrastruktur och objekt för att implementera det här användnings fallet.

Alla ovanstående egenskaper och parametrar gäller för indexerare som utför AI-berikning. Följande egenskaper är speciella för AI-anrikning: **`skillSets`** , **`outputFieldMappings`** , **`cache`** (endast för hands version och endast rest). 

```json
{
  "name": (required) String that uniquely identifies the indexer,
  "dataSourceName": (required) String, name of an existing data source,
  "targetIndexName": (required) String, name of an existing index,
  "skillsetName" : (required for AI enrichment) String, name of an existing skillset,
  "cache":  {
    "storageConnectionString" : (required for caching AI enriched content) Connection string to a blob container,
    "enableReprocessing": true
    },
  "parameters": {
    "batchSize": null,
    "maxFailedItems": null,
    "maxFailedItemsPerBatch": null
  },
  "fieldMappings": [],
  "outputFieldMappings" : (required for AI enrichment) { ... },
}
```

AI-berikning ligger utanför den här artikelns omfattning. För mer information, börja med dessa artiklar: [AI-anrikning](cognitive-search-concept-intro.md), [färdighetsuppsättningar i Azure kognitiv sökning](cognitive-search-working-with-skillsets.md)och [skapa färdigheter (rest)](/rest/api/searchservice/create-skillset).

## <a name="choose-an-indexer-client-and-create-the-indexer"></a>Välj en indexerare-klient och skapa indexeraren

När du är redo att skapa en indexerare på en fjärran sluten search-tjänst behöver du en Sök klient i form av ett verktyg som Azure Portal eller Postman eller kod som instansierar en indexerare-klient. Vi rekommenderar Azure Portal-eller REST-API: er för tidig utveckling och koncept validerings testning.

### <a name="permissions"></a>Behörigheter

Alla åtgärder relaterade till indexerare, inklusive GET-begäranden för status eller definitioner, kräver en [admin-API-nyckel](search-security-api-keys.md) på begäran.

### <a name="limits"></a>Gränser

Alla [tjänst nivåer begränsar](search-limits-quotas-capacity.md#indexer-limits) antalet objekt som du kan skapa. Om du experimenterar på den kostnads fria nivån kan du bara ha tre objekt av varje typ och 2 minuter med indexerare bearbetning (inte inklusive färdigheter bearbetning).

### <a name="use-azure-portal-to-create-an-indexer"></a>Använda Azure Portal för att skapa en indexerare

Portalen innehåller två alternativ för att skapa en indexerare: [**guiden Importera data**](search-import-data-portal.md) och en **ny indexerare** som innehåller fält för att ange en indexerare-definition. Guiden är unik i att den skapar alla nödvändiga element. Andra metoder kräver att du har fördefinierat en data källa och ett index.

Följande skärm bild visar var du kan hitta de här funktionerna i portalen. 

  :::image type="content" source="media/search-howto-create-indexers/portal-indexer-client.png" alt-text="Hotell indexerare" border="true":::

### <a name="use-a-rest-client"></a>Använd en REST-klient

Både Postman och Visual Studio Code (med ett tillägg för Azure Kognitiv sökning) kan fungera som en indexerare-klient. Med något av verktygen kan du ansluta till Sök tjänsten och skicka begäran om att [skapa indexerare (rest)](/rest/api/searchservice/create-indexer) . Det finns flera självstudier och exempel som demonstrerar REST-klienter för att skapa objekt. 

Börja med någon av dessa artiklar för att lära dig om varje klient:

+ [Skapa ett Sök index med REST och Postman](search-get-started-rest.md)
+ [Kom igång med Visual Studio Code och Azure Kognitiv sökning](search-get-started-vs-code.md)

I avsnittet om [indexerings åtgärder (rest)](/rest/api/searchservice/Indexer-operations) kan du se hjälp med att utforma indexerare-begäranden.

### <a name="use-an-sdk"></a>Använda en SDK

För Kognitiv sökning implementerar Azure SDK: er allmänt tillgängliga funktioner. Därför kan du använda alla SDK: er för att skapa indexerare-relaterade objekt. Alla tillhandahåller en **SearchIndexerClient** som har metoder för att skapa indexerare och relaterade objekt, inklusive färdighetsuppsättningar.

| Azure SDK | Klient | Exempel |
|-----------|--------|----------|
| .NET | [SearchIndexerClient](/dotnet/api/azure.search.documents.indexes.searchindexerclient) | [DotNetHowToIndexers](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToIndexers) |
| Java | [SearchIndexerClient](/java/api/com.azure.search.documents.indexes.searchindexerclient) | [CreateIndexerExample. java](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/search/azure-search-documents/src/samples/java/com/azure/search/documents/indexes/CreateIndexerExample.java) |
| JavaScript | [SearchIndexerClient](/javascript/api/@azure/search-documents/searchindexerclient) | [Indexerare](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/samples/javascript/src/indexers) |
| Python | [SearchIndexerClient](/python/api/azure-search-documents/azure.search.documents.indexes.searchindexerclient) | [sample_indexers_operations. py](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/search/azure-search-documents/samples/sample_indexers_operations.py) |

## <a name="run-the-indexer"></a>Köra indexeraren

En indexerare körs automatiskt när du skapar indexeraren på tjänsten. Det här är för tillfället en sanningen där du får reda på om det finns anslutnings fel för data källan, problem med fält mappning eller färdigheter problem. 

Det finns flera sätt att köra en indexerare:

+ Skicka en HTTP-begäran för att [skapa indexerare](/rest/api/searchservice/create-indexer) eller [Uppdatera indexeraren](/rest/api/searchservice/update-indexer) för att lägga till eller ändra definitionen och köra indexeraren.

+ Skicka en HTTP-begäran för [körning av indexerare](/rest/api/searchservice/run-indexer) för att köra en indexerare utan ändringar i definitionen.

+ Kör ett program som anropar SearchIndexerClient-metoder för att skapa, uppdatera eller köra.

> [!NOTE]
> För att undvika att omedelbart köra en indexerare när den skapas, ta med **`disabled=true`** i index definition.

Du kan också lägga indexeraren [enligt ett schema](search-howto-schedule-indexers.md) för att anropa bearbetningen med jämna mellanrum. 

Schemalagd bearbetning sammanfaller vanligt vis med ett behov av stegvis indexering av ändrat innehåll. Ändra identifierings logik är en funktion som är inbyggd i käll plattformarna. Ändringar i en BLOB-behållare identifieras automatiskt av indexeraren. Vägledning om hur du använder ändrings identifiering i andra data källor finns i Indexer-dokumenten för vissa data Källor:

+ [Azure SQL-databas](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)
+ [Azure Data Lake Storage Gen2](search-howto-index-azure-data-lake-storage.md)
+ [Azure Table Storage](search-howto-indexing-azure-tables.md)
+ [Azure Cosmos DB](search-howto-index-cosmosdb.md)

## <a name="know-your-data"></a>Känna till dina data

Indexerare förväntar sig en rad med tabell uppsättningar, där varje rad blir ett fullständigt eller delvis sökdokument i indexet. Det finns ofta en en-till-en-korrespondens mellan en rad och det resulterande sökdokumentet, där alla fält i rad uppsättningen fyller hela dokumentet. Men du kan använda indexerare för att generera bara en del av ett dokument, till exempel om du använder flera indexerare eller metoder för att bygga upp indexet. 

Om du vill förenkla Relations data till en rad uppsättning bör du skapa en SQL-vy eller skapa en fråga som returnerar överordnade och underordnade poster på samma rad. Till exempel är den inbyggda exempel data uppsättningen för hotell en SQL-databas som har 50 poster (en för varje hotell), länkade till rums poster i en relaterad tabell. Frågan som fören klar samlad data till en rad uppsättning bäddar in all rums information i JSON-dokument i varje hotell post. Informationen om det inbäddade rummet genereras av en fråga som använder en **for JSON Auto** -sats. Du kan lära dig mer om den här tekniken i [definiera en fråga som returnerar inbäddad JSON](index-sql-relational-data.md#define-a-query-that-returns-embedded-json). Detta är bara ett exempel. Du kan hitta andra metoder som ger samma resultat.

Förutom förenklade data är det viktigt att bara hämta sökbara data. Sökbara data är alfanumeriska. Kognitiv sökning kan inte söka i binära data i något format, även om det kan extrahera och härleda text beskrivningar av bildfiler (se [AI-anrikning](cognitive-search-concept-intro.md)) för att skapa sökbart innehåll. På samma sätt kan en stor text analyseras av naturliga språk modeller för att hitta struktur eller relevant information, vilket genererar nytt innehåll som du kan lägga till i ett Sök dokument.

Med tanke på att indexerarna inte åtgärdar data problem kan andra former av rengöring eller manipulering av data behövas. Mer information finns i produkt dokumentationen för din [Azure Database-produkt](/azure/?product=databases).

## <a name="know-your-index"></a>Känna till ditt index

Kom ihåg att indexerarna skickar Sök dokumenten till sökmotorn för indexering. Precis som indexerare har egenskaper som bestämmer körnings beteendet, har ett index schema egenskaper som inte kan användas för att påverka hur strängar indexeras (endast strängar analyseras och token). Beroende på Analyzer-tilldelningar kan indexerade strängar skilja sig från vad du skickade. Du kan utvärdera effekterna av analys verktyg med [analysera text (rest)](/rest/api/searchservice/test-analyzer). Mer information om analys verktyg finns i [analys verktyg för text bearbetning](search-analyzers.md).

I termer av hur indexerare interagerar med ett index, kontrollerar en indexerare endast fält namn och typer. Det finns inget validerings steg som garanterar att inkommande innehåll är korrekt för motsvarande sökfält i indexet. Som ett verifierings steg kan du köra frågor på det ifyllda indexet som returnerar hela dokument eller markerade fält. Mer information om hur du frågar innehållet i ett index finns i [skapa en enkel fråga](search-query-create.md).

## <a name="next-steps"></a>Nästa steg

+ [Schema för indexerare](search-howto-schedule-indexers.md)
+ [Definiera fält mappningar](search-indexer-field-mappings.md)
+ [Övervaka indexerings status](search-howto-monitor-indexers.md)
+ [Anslut med hanterade identiteter](search-howto-managed-identities-data-sources.md)