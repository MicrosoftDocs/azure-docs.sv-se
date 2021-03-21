---
title: Import och data inmatning i Sök index
titleSuffix: Azure Cognitive Search
description: Fylla i och ladda upp data till ett index i Azure Kognitiv sökning från externa data källor.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/05/2020
ms.openlocfilehash: aa44a27fa5bf6b7b4ea649e1a9b9a69ef8cd78d3
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102049329"
---
# <a name="data-import-overview---azure-cognitive-search"></a>Översikt över data import – Azure Kognitiv sökning

I Azure Kognitiv sökning körs frågor över ditt innehåll som läses in i och sparas i ett [sökindex](search-what-is-an-index.md). Den här artikeln går igenom de två grundläggande metoderna för att fylla ett index: *Skicka* data till indexet program mässigt eller peka en [Azure kognitiv sökning-indexerare](search-indexer-overview.md) på en data källa som stöds för att *Hämta* data.

Med båda metoderna är målet att läsa in data från en extern data källa till ett Azure Kognitiv sökning-index. Med Azure Kognitiv sökning kan du skapa ett tomt index, men tills du skickar eller hämtar data till den är det inte möjligt att fråga.

> [!NOTE]
> Om [AI-berikning](cognitive-search-concept-intro.md) är ett lösnings krav måste du använda pull-modellen (indexerarna) för att läsa in ett index. Extern bearbetning stöds bara via färdighetsuppsättningar som är kopplade till en indexerare.

## <a name="pushing-data-to-an-index"></a>Skicka data till ett index

Push-modellen, som används för att program mässigt skicka data till Azure Kognitiv sökning, är den mest flexibla metoden. För det första finns det inga begränsningar på vilken typ av datakälla som får användas. Alla data uppsättningar som består av JSON-dokument kan skickas till ett Azure Kognitiv sökning-index, förutsatt att varje dokument i data uppsättningen har fält mappning till fält som definierats i index schemat. För det andra finns det inga begränsningar på körningsfrekvensen. Du kan skicka ändringar till ett index så ofta du vill. För program som har mycket låga fördröjningskrav (t.ex. om det är viktigt att sökåtgärder är synkroniserade med dynamiska inventeringsdatabaser) är push-modellen ditt enda alternativ.

Den här metoden är mer flexibel än pull-modellen eftersom du kan ladda upp dokument individuellt eller i batchar (upp till 1 000 per batch eller 16 MB, beroende på vilken gräns som nås först). Med push-modellen kan du också ladda upp dokument till Azure Kognitiv sökning oavsett var dina data finns.

### <a name="how-to-push-data-to-an-azure-cognitive-search-index"></a>Skicka data till ett Azure Kognitiv sökning-index

Du kan använda följande API:er för att läsa in ett eller flera dokument i ett index:

+ [Lägg till, uppdatera eller ta bort dokument (REST API)](/rest/api/searchservice/AddUpdate-or-Delete-Documents)
+ [IndexDocumentsAction-klass](/dotnet/api/azure.search.documents.models.indexdocumentsaction) eller [IndexDocumentsBatch-klass](/dotnet/api/azure.search.documents.models.indexdocumentsbatch) 

Det finns för närvarande inget verktygsstöd för att skicka data via portalen.

En introduktion till varje metod finns i [snabb start: skapa ett azure kognitiv sökning-index med PowerShell](./search-get-started-powershell.md) eller [C# snabb start: skapa ett Azure kognitiv sökning-index med .NET SDK](search-get-started-dotnet.md).

<a name="indexing-actions"></a>

### <a name="indexing-actions-upload-merge-mergeorupload-delete"></a>Indexerings åtgärder: Ladda upp, sammanfoga, mergeOrUpload, ta bort

Du kan styra vilken typ av indexerings åtgärd som ska användas för varje dokument, ange om dokumentet ska överföras fullständigt, slås samman med befintligt dokument innehåll eller tas bort.

I REST API skickar du HTTP POST-begäranden med JSON-begäranden till URL: en för Azure Kognitiv sökning indexets slut punkt. Varje JSON-objekt i "value"-matrisen innehåller dokumentets nyckel och anger om en indexerings åtgärd lägger till, uppdaterar eller tar bort dokument innehåll. Ett kod exempel finns i [läsa in dokument](search-get-started-dotnet.md#load-documents).

I .NET SDK ska du paketera dina data i ett `IndexBatch` objekt. En `IndexBatch` kapslar in en samling `IndexAction` objekt som innehåller ett dokument och en egenskap som talar om för Azure kognitiv sökning vilka åtgärder som ska utföras i dokumentet. Ett kod exempel finns i [snabb start för C#](search-get-started-dotnet.md).


| @search.action | Beskrivning | Nödvändiga fält för varje dokument | Kommentarer |
| -------------- | ----------- | ---------------------------------- | ----- |
| `upload` |En `upload`-åtgärd liknar en ”upsert” där dokumentet infogas om det är nytt och uppdateras/ersätts om det finns. |nyckel plus eventuella andra fält som du vill definiera |När du uppdaterar och ersätter ett befintligt dokument tilldelas alla fält som inte angetts i begäran `null`. Detta sker även om fältet tidigare hade ett värde som inte var null. |
| `merge` |Uppdaterar ett befintligt dokument med de angivna fälten. Sammanfogningen misslyckas om dokumentet inte finns i indexet. |nyckel plus eventuella andra fält som du vill definiera |Alla fält som du anger i en sammanfogning ersätter det befintliga fältet i dokumentet. I .NET SDK omfattar detta fält av typen `DataType.Collection(DataType.String)` . I REST API innehåller detta fält av typen `Collection(Edm.String)` . Om dokumentet till exempel innehåller ett `tags`-fält med värdet `["budget"]` och du utför en sammanfogning med värdet `["economy", "pool"]` för `tags` så blir det slutliga värdet för fältet `tags``["economy", "pool"]`. Det blir inte `["budget", "economy", "pool"]`. |
| `mergeOrUpload` |Den här åtgärden fungerar som `merge` om ett dokument med den angivna nyckeln redan finns i indexet. Om dokumentet inte finns fungerar den som `upload` med ett nytt dokument. |nyckel plus eventuella andra fält som du vill definiera |- |
| `delete` |Tar bort det angivna dokumentet från indexet. |endast nyckel |Andra fält som du anger än nyckelfältet ignoreras. Om du vill ta bort ett enstaka fält från ett dokument använder du `merge` i stället och anger bara fältet till null. |

### <a name="formulate-your-query"></a>Formulera frågan

Du kan [söka i ditt index med hjälp av REST-API:et](/rest/api/searchservice/Search-Documents) på två sätt. Ett sätt är att skicka en HTTP POST-begäran där dina frågeparametrar definieras i ett JSON-objekt i begärandetexten. Det andra sättet är att skicka en HTTP GET-begäran där dina frågeparametrar definieras i URL:en för begäran. POST har mindre [restriktiva gränser](/rest/api/searchservice/Search-Documents) vad gäller frågeparametrarnas storlek än GET. Av den anledningen rekommenderar vi att du använder POST såvida det inte finns särskilda omständigheter som gör att GET är lämpligare.

För både POST och GET måste du ange *tjänst namn*, *index namn* och en *API-version* i fråge-URL: en. 

För GET anger du frågeparametrarna i *frågesträngen* i slutet av URL:en. Se URL-formatet nedan:

```http
    https://[service name].search.windows.net/indexes/[index name]/docs?[query string]&api-version=2019-05-06
```

Formatet för POST är detsamma, men med `api-version` i Frågesträngens parametrar.

## <a name="pulling-data-into-an-index"></a>Hämta in data till ett index

Datahämtningsmodellen crawlar en datakälla som stöds och överför automatiskt data till ditt index. I Azure Kognitiv sökning implementeras den här funktionen genom *indexerare* som för närvarande är tillgängliga för dessa plattformar:

+ [Blob Storage](search-howto-indexing-azure-blob-storage.md)
+ [Table Storage](search-howto-indexing-azure-tables.md)
+ [Azure Cosmos DB](search-howto-index-cosmosdb.md)
+ [Azure SQL Database, SQL-hanterad instans och SQL Server på virtuella Azure-datorer](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)
+ [SharePoint Online (för hands version)](search-howto-index-sharepoint-online.md)
+ [Azure Data Lake Storage Gen2 (för hands version)](search-howto-index-azure-data-lake-storage.md)

Indexerare ansluter ett index till en datakälla (vanligtvis en tabell, vy eller motsvarande struktur) och mappar källfält till motsvarande fält i indexet. Under körningen omvandlas raduppsättningen automatiskt till JSON och läses in i det angivna indexet. Alla indexerare stöder schemaläggning så att du kan ange hur ofta data ska uppdateras. De flesta indexerare tillhandahåller ändringsspårning om datakällan har stöd för det. Indexerare spårar ändringar och borttagningar av befintliga dokument och identifierar nya dokument, vilket gör att du slipper hantera dina data i indexet aktivt.

### <a name="how-to-pull-data-into-an-azure-cognitive-search-index"></a>Så här hämtar du data till ett Azure Kognitiv sökning-index

Indexerarfunktioner exponeras på [Azure Portal](search-import-data-portal.md), i [REST-API:et](/rest/api/searchservice/Indexer-operations) och i [.NET SDK](/dotnet/api/azure.search.documents.indexes.searchindexerclient).

En fördel med att använda portalen är att Azure Kognitiv sökning vanligt vis kan generera ett standard index schema åt dig genom att läsa metadata för käll data uppsättningen. Du kan ändra det genererade indexet tills indexet har bearbetats. Därefter är de enda schemaändringarna som tillåts de som inte kräver omindexering. Om de ändringar som du vill göra påverkar schemat direkt måste indexet återskapas. 

## <a name="verify-data-import-with-search-explorer"></a>Verifiera data import med Sök Utforskaren

Ett snabbt sätt att utföra en preliminär kontroll av dokument överföringen är att använda **Sök Utforskaren** i portalen. Med utforskaren kan du köra frågor mot ett index utan att behöva skriva kod. Sök upplevelsen baseras på standardinställningarna, till exempel den [enkla syntaxen](/rest/api/searchservice/simple-query-syntax-in-azure-search) och standard [parametern searchMode](/rest/api/searchservice/search-documents). Resultaten returneras i JSON så att du kan gå igenom hela dokumentet.

> [!TIP]
> Många [exempel på Azure kognitiv sökning-kod](https://github.com/Azure-Samples/?utf8=%E2%9C%93&query=search) är inbäddade eller lättillgängliga data uppsättningar, vilket ger ett enkelt sätt att komma igång. Portalen innehåller också en exempelindexerare och datakälla som består av en liten datauppsättning med fastighetsinformation (med namnet ”realestate-us-sample”). När du kör den förkonfigurerade indexeraren i exempel data källan skapas ett index som läses in med dokument som sedan kan frågas i Sök Utforskaren eller efter kod som du skriver.

## <a name="see-also"></a>Se även

+ [Översikt över indexeraren](search-indexer-overview.md)
+ [Steg-för-steg-beskrivning av portalen: skapa, läsa in och avfråga ett index](search-get-started-portal.md)