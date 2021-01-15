---
title: Indexerare för crawlning av data under import
titleSuffix: Azure Cognitive Search
description: Crawla Azure SQL Database, SQL-hanterad instans, Azure Cosmos DB eller Azure-lagring för att extrahera sökbara data och fylla ett Azure Kognitiv sökning-index.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/11/2020
ms.custom: fasttrack-edit
ms.openlocfilehash: 5861e79054bed0d9d75258dfa9cb39b198f0f93d
ms.sourcegitcommit: d59abc5bfad604909a107d05c5dc1b9a193214a8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/14/2021
ms.locfileid: "98216452"
---
# <a name="indexers-in-azure-cognitive-search"></a>Indexerare i Azure Cognitive Search

En *indexerare* i Azure kognitiv sökning är en Crawler som extraherar sökbara data och metadata från en extern Azure-datakälla och fyller ett sökindex med fält-till-fält-mappningar mellan källdata och ditt index. Den här metoden kallas ibland för "pull-modell" eftersom tjänsten hämtar data i utan att du behöver skriva kod som lägger till data i ett index.

Indexerare är enbart Azure, med enskilda indexerare för [Azure SQL](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md), [Azure Cosmos DB](search-howto-index-cosmosdb.md), [Azure Table Storage](search-howto-indexing-azure-tables.md) och [Blob Storage](search-howto-indexing-azure-blob-storage.md). När du konfigurerar en indexerare anger du en data källa (ursprung), samt ett index (mål). Flera källor, till exempel Blob Storage, har ytterligare konfigurations egenskaper som är speciella för innehålls typen.

Du kan köra indexerare på begäran eller enligt ett återkommande data uppdaterings schema som körs så ofta som var femte minut. För frekventa uppdateringar krävs en push-modell som samtidigt uppdaterar data i både Azure-Kognitiv sökning och den externa data källan.

## <a name="usage-scenarios"></a>Användningsscenarier

Du kan använda en indexerare som enda metod för att mata in data eller använda en kombination av tekniker som bara läser in några av fälten i ditt index, om du vill omvandla eller lägga till innehåll på vägen. I följande tabell sammanfattas huvud scenarierna.

| Scenario |Strategi |
|----------|---------|
| Enskild källa | Det här mönstret är det enklaste: en data källa är den enda innehålls leverantören för ett sökindex. Från källan identifierar du ett fält som innehåller unika värden som ska fungera som dokument nyckel i Sök indexet. Det unika värdet kommer att användas som en identifierare. Alla andra käll fält mappas implicit eller uttryckligen till motsvarande fält i ett index. </br></br>En viktig sak är att värdet för en dokument nyckel kommer från data källan. En Sök tjänst genererar inte nyckel värden. Vid efterföljande körningar läggs inkommande dokument med nya nycklar till, medan inkommande dokument med befintliga nycklar antingen sammanfogas eller skrivs över, beroende på om index fält är null eller har fyllts i. |
| Flera källor| Ett index kan ta emot innehåll från flera källor, där varje körning ger nytt innehåll från en annan källa. </br></br>Ett resultat kan vara ett index som får dokument när varje indexerare har körts, med hela dokument som har skapats helt från varje källa. Dokument 1-100 kommer till exempel från Blob Storage, dokument 101-200 från Azure SQL och så vidare. Utmaningen för det här scenariot är att designa ett index schema som fungerar för alla inkommande data och en dokument nyckel struktur som är enhetlig i Sök indexet. Internt kan de värden som unikt identifierar ett dokument metadata_storage_path i en BLOB-behållare och en primär nyckel i en SQL-tabell. Du kan föreställa dig att en eller båda källorna måste ändras för att tillhandahålla nyckel värden i ett gemensamt format, oavsett innehållets ursprung. I det här scenariot bör du förvänta dig att utföra en viss nivå av för bearbetning för att homogenisera data så att de kan hämtas till ett enda index.</br></br>Ett alternativt resultat kan vara Sök efter dokument som delvis fyllts i den första körningen och som sedan fylls i automatiskt genom efterföljande körningar för att hämta värden från andra källor. Fält 1-10 är till exempel från Blob Storage, 11-20 från Azure SQL och så vidare. Utmaningen med det här mönstret ser till att varje indexerings körning är riktad mot samma dokument. Sammanslagning av fält i ett befintligt dokument kräver en matchning på dokument nyckeln. En demonstration av det här scenariot finns i [Självstudier: index från flera data källor](tutorial-multiple-data-sources.md). |
| Innehålls omvandling | Kognitiv sökning stöder valfria [AI-anriknings](cognitive-search-concept-intro.md) beteenden som lägger till bild analys och naturlig språk bearbetning för att skapa ett nytt sökbart innehåll och en ny struktur. AI-berikning är indexerare driven, via en ansluten [färdigheter](cognitive-search-working-with-skillsets.md). För att kunna utföra AI-berikning behöver indexeraren fortfarande ha ett index och en data källa, men i det här scenariot lägger till färdigheter-bearbetning till indexerare-körning. |

## <a name="approaches-for-creating-and-managing-indexers"></a>Metoder för att skapa och hantera indexerare

Du kan skapa och hantera indexerare med hjälp av följande metoder:

+ [Portal > guiden Importera data](search-import-data-portal.md)
+ [Tjänsten REST API](/rest/api/searchservice/Indexer-operations)
+ [.NET SDK](/dotnet/api/azure.search.documents.indexes.models.searchindexer)

Om du använder ett SDK skapar du en [SearchIndexerClient](/dotnet/api/azure.search.documents.indexes.searchindexerclient) för att arbeta med indexerare, data källor och färdighetsuppsättningar. Länken ovan gäller för .NET SDK, men alla SDK: er tillhandahåller en SearchIndexerClient och liknande API: er.

De nya data källorna inlednings vis visas som förhands gransknings funktioner och är bara REST. Efter att ha gått över till allmän tillgänglighet är fullständig support inbyggd i portalen och i de olika SDK: erna som finns på deras egna versions scheman.

## <a name="permissions"></a>Behörigheter

Alla åtgärder som rör indexerare, inklusive GET-begäranden för status eller definitioner, kräver en [admin-API-nyckel](search-security-api-keys.md).

<a name="supported-data-sources"></a>

## <a name="supported-data-sources"></a>Datakällor som stöds

Indexerare söker efter data lager i Azure.

+ [Azure Blob Storage](search-howto-indexing-azure-blob-storage.md)
+ [Azure Data Lake Storage Gen2](search-howto-index-azure-data-lake-storage.md) (för hands version)
+ [Azure Table Storage](search-howto-indexing-azure-tables.md)
+ [Azure Cosmos DB](search-howto-index-cosmosdb.md)
+ [Azure SQL Database](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)
+ [SQL-hanterad instans](search-howto-connecting-azure-sql-mi-to-azure-search-using-indexers.md)
+ [SQL Server på Azure Virtual Machines](search-howto-connecting-azure-sql-iaas-to-azure-search-using-indexers.md)

## <a name="stages-of-indexing"></a>Indexerings steg

Vid en inledande körning kommer en indexerare att läsas i alla data som anges i tabellen eller behållaren när indexet är tomt. Vid efterföljande körningar kan indexeraren vanligt vis identifiera och hämta bara de data som har ändrats. För BLOB-data är ändrings identifieringen automatisk. För andra data källor som Azure SQL eller Cosmos DB måste ändrings identifiering vara aktiverat.

För varje dokument som matas in, implementerar en indexerare eller samordnar flera steg, från dokument hämtning till en sista sökmotor "leverans" för indexering. Om du vill kan en indexerare också instrumentell vid körning av färdigheter och utdata, förutsatt att en färdigheter har definierats.

:::image type="content" source="media/search-indexer-overview/indexer-stages.png" alt-text="Indexerings steg" border="false":::

### <a name="stage-1-document-cracking"></a>Steg 1: dokument sprickor

Dokument sprickor är processen att öppna filer och extrahera innehåll. Beroende på typen av data källa kommer indexeraren att försöka utföra olika åtgärder för att extrahera potentiellt indexerat innehåll.  

Exempel:  

+ När dokumentet är en post i en [Azure SQL-datakälla](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)extraherar indexeraren varje fält för posten.
+ När dokumentet är en PDF-fil i en [Azure Blob Storage data källa](search-howto-indexing-azure-blob-storage.md)extraherar indexeraren text, bilder och metadata.
+ När dokumentet är en post i en [Cosmos db data källa](search-howto-index-cosmosdb.md)extraherar indexeraren fälten och under fälten från Cosmos DB dokumentet.

### <a name="stage-2-field-mappings"></a>Steg 2: fält mappningar 

En indexerare extraherar text från ett käll fält och skickar det till ett målfält i ett index eller kunskaps lager. När fält namn och typer är klara, är sökvägen avmarkerad. Du kan dock vilja ha olika namn eller typer i utdata, och i så fall måste du berätta för indexeraren hur fältet ska mappas. Det här steget inträffar efter dokument sprickor, men före omvandlingar när indexeraren läser från käll dokumenten. När du definierar en [fält mappning](search-indexer-field-mappings.md)skickas värdet för käll fältet som-är till mål fältet utan ändringar. Fält mappningar är valfria.

### <a name="stage-3-skillset-execution"></a>Steg 3: färdigheter-körning

Färdigheter-körning är ett valfritt steg som anropar inbyggd eller anpassad AI-bearbetning. Du kanske behöver det för OCR (optisk tecken läsning) i form av bild analys, eller så kan du behöva översättning av språk. Oavsett transformeringen är färdigheter-körningen där berikning sker. Om en indexerare är en pipeline kan du tänka på en [färdigheter](cognitive-search-defining-skillset.md) som en "pipeline i pipelinen". En färdigheter har en egen sekvens med steg som kallas kunskaper.

### <a name="stage-4-output-field-mappings"></a>Steg 4: mappningar för utgående fält

Om du inkluderar en färdigheter måste du förmodligen inkludera mappningar för utdatakolumner. Utdatan från en färdigheter är egentligen ett träd med information som kallas för ett berikat dokument. Med mappningar av utdata fält kan du välja vilka delar av trädet som ska mappas till fält i ditt index. Lär dig hur du [definierar mappningar av utdata-fält](cognitive-search-output-field-mapping.md).

Fält mappningar associerar orda Grant-värden från data källan till mål fälten och anger indexeraren för att koppla de transformerade värdena i det berikade dokumentet till mål fälten i indexet. Till skillnad från fält mappningar, som betraktas som valfria, så behöver du alltid definiera en mappning av utdata fält för alla transformerat innehåll som måste finnas i ett index.

Nästa bild visar en exempel på en [fel söknings session](cognitive-search-debug-session.md) för indexerare: dokument sprickor, fält mappningar, färdigheter körning och fält mappningar för utdata.

:::image type="content" source="media/search-indexer-overview/sample-debug-session.png" alt-text="exempel på en felsökningssession" lightbox="media/search-indexer-overview/sample-debug-session.png":::

## <a name="basic-configuration-steps"></a>Grundläggande konfigurationssteg

Indexerare kan erbjuda funktioner som är unika för datakällan. I detta avseende varierar vissa aspekter av indexerarna och datakällskonfigurationen kan variera efter indexerartyp. Alla indexerare delar dock samma grundläggande sammansättning och krav. De steg som är gemensamma för alla indexerare beskrivs nedan.

### <a name="step-1-create-a-data-source"></a>Steg 1: Skapa en datakälla

En indexerare hämtar data källans lutning från ett *data käll* objekt. Definitionen av data källan tillhandahåller en anslutnings sträng och eventuellt autentiseringsuppgifter. Anropa [create DataSource](/rest/api/searchservice/create-data-source) -REST API eller [SearchIndexerDataSourceConnection-klassen](/dotnet/api/azure.search.documents.indexes.models.searchindexerdatasourceconnection) för att skapa resursen.

Datakällor konfigureras och hanteras oberoende av indexerarna som använder dem, vilket innebär att en datakälla kan användas av flera indexerare för att läsa in mer än ett index i taget.

### <a name="step-2-create-an-index"></a>Steg 2: Skapa ett index

En indexerare automatiserar vissa uppgifter som rör datapåfyllning, men att skapa ett index är vanligtvis inte en av dem. Som krav måste du ha ett fördefinierat index med fält som matchar de i din externa datakälla. Fält måste matcha efter namn och datatyp. Mer information om hur du strukturerar ett index finns i [skapa ett index (Azure Kognitiv sökning REST API) eller en SearchIndex-](/rest/api/searchservice/Create-Index) [klass](/dotnet/api/azure.search.documents.indexes.models.searchindex). Hjälp med fält kopplingar finns i [fält mappningar i Azure kognitiv sökning indexerare](search-indexer-field-mappings.md).

> [!Tip]
> Indexerare kan inte generera ett index åt dig, men du kan få hjälp av guiden **Importera data** i portalen. I de flesta fall kan guiden härleda ett indexschema från befintliga metadata i källan, vilket skapar ett preliminärt indexschema som du kan redigera direkt när guiden är aktiv. När indexet har skapats i tjänsten är ytterligare redigeringar i portalen i huvudsak begränsade till tillägg av nya fält. Överväg att använda guiden för att skapa, men inte revidera, ett index. I [steg-för-steg-beskrivningen för portalen](search-get-started-portal.md) kan du få en praktisk genomgång.

### <a name="step-3-create-and-schedule-the-indexer"></a>Steg 3: Skapa och schemalägg indexeraren

Indexer-definitionen är en konstruktion som sammanställer alla element som är relaterade till data inmatning. De element som krävs är en data källa och ett index. Valfria element innehåller ett schema och fält mappningar. Fält mappningar är bara valfria om käll fält och index fält tydligt motsvarar. Mer information om hur du strukturerar en indexerare finns i [skapa indexerare (Azure Kognitiv sökning REST API)](/rest/api/searchservice/Create-Indexer).

<a id="RunIndexer"></a>

## <a name="run-indexers-on-demand"></a>Köra indexerare på begäran

Även om det är vanligt att schemalägga indexering kan en indexerare också anropas på begäran med [kommandot kör](/rest/api/searchservice/run-indexer):

```http
POST https://[service name].search.windows.net/indexers/[indexer name]/run?api-version=2020-06-30
api-key: [Search service admin key]
```

> [!NOTE]
> När körnings-API returnerar en lyckad kod har indexerings anropet schemalagts, men den faktiska bearbetningen sker asynkront. 

Du kan övervaka indexerings status i portalen eller genom att [Hämta indexerings status-API](/rest/api/searchservice/get-indexer-status). 

<a name="GetIndexerStatus"></a>

## <a name="get-indexer-status"></a>Hämta indexerings status

Du kan hämta status och körnings historik för en indexerare med hjälp av [status kommandot Get indexerare](/rest/api/searchservice/get-indexer-status):

```http
GET https://[service name].search.windows.net/indexers/[indexer name]/status?api-version=2020-06-30
api-key: [Search service admin key]
```

Svaret innehåller övergripande indexerings status, det sista (eller inaktuella) indexerings anropet och historiken för senaste indexerare-anrop.

```output
{
    "status":"running",
    "lastResult": {
        "status":"success",
        "errorMessage":null,
        "startTime":"2018-11-26T03:37:18.853Z",
        "endTime":"2018-11-26T03:37:19.012Z",
        "errors":[],
        "itemsProcessed":11,
        "itemsFailed":0,
        "initialTrackingState":null,
        "finalTrackingState":null
     },
    "executionHistory":[ {
        "status":"success",
         "errorMessage":null,
        "startTime":"2018-11-26T03:37:18.853Z",
        "endTime":"2018-11-26T03:37:19.012Z",
        "errors":[],
        "itemsProcessed":11,
        "itemsFailed":0,
        "initialTrackingState":null,
        "finalTrackingState":null
    }]
}
```

Körnings historiken innehåller upp till 50 senaste slutförda körningar, som sorteras i omvänd kronologisk ordning (så att den senaste körningen kommer först i svaret).

## <a name="next-steps"></a>Nästa steg

Nu när du har lagt grunden är nästa steg att granska krav och uppgifter som är specifika för varje typ av datakälla.

+ [Azure SQL Database, SQL-hanterad instans eller SQL Server på en virtuell Azure-dator](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)
+ [Azure Cosmos DB](search-howto-index-cosmosdb.md)
+ [Azure Blob Storage](search-howto-indexing-azure-blob-storage.md)
+ [Azure Table Storage](search-howto-indexing-azure-tables.md)
+ [Indexera CSV-blobar med Azure Kognitiv sökning BLOB-indexeraren](search-howto-index-csv-blobs.md)
+ [Indexera JSON-blobbar med Azure Kognitiv sökning BLOB-indexeraren](search-howto-index-json-blobs.md)