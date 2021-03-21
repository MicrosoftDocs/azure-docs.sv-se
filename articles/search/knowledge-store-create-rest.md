---
title: Skapa ett kunskaps lager med REST
titleSuffix: Azure Cognitive Search
description: Använd REST API och Postman för att skapa en Azure Kognitiv sökning kunskaps lager för att spara omfattande innehåll från en AI-pipeline.
author: HeidiSteen
manager: nitinme
ms.author: heidist
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 11/18/2020
ms.openlocfilehash: 6af9b8f97d622ae10cfdbcaa8ca50abb42ec7332
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "94889062"
---
# <a name="create-a-knowledge-store-using-rest-and-postman"></a>Skapa ett kunskaps lager med REST och Postman

Ett kunskaps lager innehåller utdata från en Azure Kognitiv sökning anriknings pipeline för senare analys eller annan efterföljande bearbetning. En AI-fördefinierad pipeline tar emot bildfiler eller ostrukturerade textfiler, indexerar dem med hjälp av Azure Kognitiv sökning, använder AI-anrikninger från Cognitive Services (till exempel bild analys och naturlig språk bearbetning) och sparar sedan resultatet i ett kunskaps lager i Azure Storage. Du kan använda verktyg som Power BI eller Storage Explorer i Azure Portal för att utforska kunskaps lagret.

I den här artikeln använder du REST API-gränssnittet för att mata in, indexera och tillämpa AI-berikare i en uppsättning Hotell recensioner. Hotell granskningarna importeras till Azure Blob Storage. Resultaten sparas som ett kunskaps lager i Azure Table Storage.

När du har skapat kunskaps lagret kan du lära dig mer om hur du kommer åt kunskaps lagret med hjälp av [Storage Explorer](knowledge-store-view-storage-explorer.md) eller [Power BI](knowledge-store-connect-power-bi.md).

Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

> [!TIP]
> Vi rekommenderar att du [skickar Desktop-appen](https://www.getpostman.com/) för den här artikeln. [Käll koden](https://github.com/Azure-Samples/azure-search-postman-samples/tree/master/knowledge-store) för den här artikeln innehåller en Postman-samling som innehåller alla begär Anden. 

## <a name="create-services-and-load-data"></a>Skapa tjänster och läsa in data

I den här snabb starten används Azure Kognitiv sökning Azure Blob Storage och [azure Cognitive Services](https://azure.microsoft.com/services/cognitive-services/) för AI. 

Eftersom arbets belastningen är så liten är Cognitive Services i bakgrunden för att tillhandahålla kostnads fri bearbetning för upp till 20 transaktioner per dag. Eftersom data uppsättningen är så liten kan du hoppa över att skapa eller bifoga en Cognitive Services resurs.

1. [Ladda ned HotelReviews_Free.csv](https://knowledgestoredemo.blob.core.windows.net/hotel-reviews/HotelReviews_Free.csv?sp=r&st=2019-11-04T01:23:53Z&se=2025-11-04T16:00:00Z&spr=https&sv=2019-02-02&sr=b&sig=siQgWOnI%2FDamhwOgxmj11qwBqqtKMaztQKFNqWx00AY%3D). Dessa data är hotell gransknings data som sparats i en CSV-fil (härstammar från Kaggle.com) och innehåller 19 stycken kundfeedback om ett enda hotell. 

1. [Skapa ett Azure Storage-konto](../storage/common/storage-account-create.md?tabs=azure-portal) eller [hitta ett befintligt konto](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Storage%2storageAccounts/) under din aktuella prenumeration. Du använder Azure Storage för både det råa innehåll som ska importeras och kunskaps lagret som är slut resultatet.

   Välj konto typen **StorageV2 (General Purpose v2)** .

1. Öppna BLOB Services-sidorna och skapa en behållare med namnet *Hotell – recensioner*.

1. Klicka på **Överför**.

    ![Överför data](media/knowledge-store-create-portal/upload-command-bar.png "Ladda upp Hotell recensioner")

1. Välj den **HotelReviews-Free.csv** -fil som du laddade ned i det första steget.

    ![Skapa Azure Blob-behållaren](media/knowledge-store-create-portal/hotel-reviews-blob-container.png "Skapa Azure Blob-behållaren")

1. Du är nästan klar med den här resursen, men innan du lämnar dessa sidor använder du en länk i det vänstra navigerings fönstret för att öppna sidan **åtkomst nycklar** . Hämta en anslutnings sträng för att hämta data från Blob Storage. En anslutnings sträng ser ut ungefär som i följande exempel: `DefaultEndpointsProtocol=https;AccountName=<YOUR-ACCOUNT-NAME>;AccountKey=<YOUR-ACCOUNT-KEY>;EndpointSuffix=core.windows.net`

1. Växla till Azure Kognitiv sökning fortfarande i portalen. [Skapa en ny tjänst](search-create-service-portal.md) eller [Sök efter en befintlig tjänst](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices). Du kan använda en kostnads fri tjänst för den här övningen.

## <a name="configure-postman"></a>Konfigurera Postman

Installera och konfigurera Postman.

### <a name="download-and-install-postman"></a>Hämta och installera Postman

1. Hämta [käll koden för Postman-samlingen](https://github.com/Azure-Samples/azure-search-postman-samples/blob/master/knowledge-store/KnowledgeStore.postman_collection.json).
1. Välj **fil**  >  **import** för att importera käll koden till Postman.
1. Välj fliken **samlingar** och välj sedan knappen **...** (ellips).
1. Välj **Redigera**. 
   
   ![Postman-app som visar navigering](media/knowledge-store-create-rest/postman-edit-menu.png "Gå till redigerings menyn i Postman")
1. I dialog rutan **Redigera** väljer du fliken **variabler** . 

På fliken **variabler** kan du lägga till värden som Postman växlar i varje gång det påträffar en speciell variabel inom dubbla klammerparenteser. Postman ersätter exempelvis symbolen `{{admin-key}}` med det aktuella värde som du anger för `admin-key` . Postman gör ersättningen i URL: er, sidhuvuden, begär ande texten och så vidare. 

Om du vill hämta värdet för `admin-key` går du till Azure kognitiv sökning-tjänsten och väljer fliken **nycklar** . ändra `search-service-name` och `storage-account-name` till de värden som du valde i [skapa tjänster](#create-services-and-load-data). Anges `storage-connection-string` med hjälp av värdet på fliken **åtkomst nycklar** för lagrings kontot. Du kan lämna standardvärdena för de andra värdena.

![Fliken Postman-app-variabler](media/knowledge-store-create-rest/postman-variables-window.png "Fönstret för Postman-variabler")


| Variabel    | Hämta det på |
|-------------|-----------------|
| `admin-key` | På sidan **nycklar** i Azure kognitiv sökning-tjänsten.  |
| `api-version` | Lämna som **2020-06-30**. |
| `datasource-name` | Lämna som **Hotell – recensioner – DS**. | 
| `indexer-name` | Lämna som **Hotell – recensioner – IXR**. | 
| `index-name` | Lämna som **Hotell – recensioner – IX**. | 
| `search-service-name` | Namnet på Azure Kognitiv sökning-tjänsten. URL: en är `https://{{search-service-name}}.search.windows.net` . | 
| `skillset-name` | Lämna som **Hotell – recensioner – SS**. | 
| `storage-account-name` | Namnet på Azure Storage-kontot. | 
| `storage-connection-string` | I lagrings kontot går du till fliken **åtkomst nycklar** och väljer **KEY1**-  >  **anslutningssträng**. | 
| `storage-container-name` | Lämna som **Hotell – recensioner**. | 

### <a name="review-the-request-collection-in-postman"></a>Granska Request-samlingen i Postman

När du skapar ett kunskaps lager måste du utfärda fyra HTTP-förfrågningar: 

- **Skicka begäran om att skapa indexet**: det här indexet innehåller de data som Azure kognitiv sökning använder och returnerar.
- **Post-begäran för att skapa data källan**: den här data källan ansluter ditt Azure kognitiv sökning-beteende till data och kunskaps lagrets lagrings konto. 
- **Skicka begäran om att skapa färdigheter**: färdigheter anger de anrikninger som används för dina data och kunskaps lagrets struktur.
- **Skicka begäran om att skapa indexeraren**: att köra indexeraren läser data, tillämpar färdigheter och lagrar resultaten. Du måste köra den här begäran senast.

[Käll koden](https://github.com/Azure-Samples/azure-search-postman-samples/blob/master/knowledge-store/KnowledgeStore.postman_collection.json) innehåller en Postman-samling som har fyra begär Anden. Om du vill utfärda förfrågningarna väljer du fliken för begäran i Postman. Lägg sedan till `api-key` och `Content-Type` begär huvuden. Ange värdet för `api-key` till `{{admin-key}}` . Ange värdet `Content-type` till `application/json` . 

![Skärm bild som visar Postman gränssnitt för rubriker](media/knowledge-store-create-rest/postman-headers-ui.png)

> [!Note]
> Du måste ange `api-key` och `Content-type` rubriker i alla dina begär Anden. Om Postman identifierar en variabel, visas variabeln i orange text, precis som `{{admin-key}}` i föregående skärm bild. Om variabeln är felstavad visas den i röd text.
>

## <a name="create-an-azure-cognitive-search-index"></a>Skapa ett Azure Cognitive Search-index

Skapa ett Azure Kognitiv sökning-index som representerar de data som du är intresse rad av vid sökning, filtrering och användning av förbättringar i. Skapa indexet genom att utfärda en skicka begäran till `https://{{search-service-name}}.search.windows.net/indexes/{{index-name}}?api-version={{api-version}}` . Postman ersätter symboler som omges av dubbla klammerparenteser (till exempel, `{{search-service-name}}` `{{index-name}}` och `{{api-version}}` ) med de värden som du angav i [Konfigurera PostMan](#configure-postman). Om du använder ett annat verktyg för att utfärda REST-kommandon måste du ersätta dessa variabler själv.

Ange strukturen för ditt Azure Kognitiv sökning-index i bröd texten i begäran. När du har angett `api-key` `Content-type` -och-rubrikerna i Postman går du till **text** rutan i begäran. Du bör se följande JSON. Om du inte gör det väljer du **RAW**  >  **JSON (Application/JSON)** och klistrar sedan in följande kod som brödtext:

```JSON
{
    "name": "{{index-name}}",
    "fields": [
        { "name": "address", "type": "Edm.String", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "categories", "type": "Edm.String", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "city", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false },
        { "name": "country", "type": "Edm.String", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "latitude", "type": "Edm.String", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "longitude", "type": "Edm.String", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "name", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false },
        { "name": "postalCode", "type": "Edm.String", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "province", "type": "Edm.String", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "reviews_date", "type": "Edm.DateTimeOffset", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "reviews_dateAdded", "type": "Edm.DateTimeOffset", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "reviews_rating", "type": "Edm.String", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "reviews_text", "type": "Edm.String", "filterable": false,  "sortable": false, "facetable": false },
        { "name": "reviews_title", "type": "Edm.String", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "reviews_username", "type": "Edm.String", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "AzureSearch_DocumentKey", "type": "Edm.String", "searchable": false, "filterable": false, "sortable": false, "facetable": false, "key": true },
        { "name": "metadata_storage_content_type", "type": "Edm.String", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "metadata_storage_size", "type": "Edm.Int64", "searchable": false, "filterable": false, "sortable": false, "facetable": false},
        { "name": "metadata_storage_last_modified", "type": "Edm.DateTimeOffset", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "metadata_storage_name", "type": "Edm.String", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "metadata_storage_path", "type": "Edm.String", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "Sentiment", "type": "Collection(Edm.Double)", "searchable": false, "filterable": true, "retrievable": true, "sortable": false, "facetable": true },
        { "name": "Language", "type": "Edm.String", "filterable": true, "sortable": false, "facetable": true },
        { "name": "Keyphrases", "type": "Collection(Edm.String)", "filterable": true, "sortable": false, "facetable": true }
    ]
}

```

Den här index definitionen är en kombination av data som du vill presentera för användaren (namnet på hotellet, granska innehåll, datum), sökmetadata och AI-förbättringar (sentiment, diskussions fraser och språk).

Välj **Skicka** för att skicka begäran om placering. Du bör se status `201 - Created` . Om du ser en annan status i **text** rutan söker du efter ett JSON-svar som innehåller ett fel meddelande. 

## <a name="create-the-datasource"></a>Skapa data källan

Anslut sedan Azure Kognitiv sökning till hotell data som du har lagrat i Blob Storage. Skicka en POST-begäran till om du vill skapa data källan `https://{{search-service-name}}.search.windows.net/datasources?api-version={{api-version}}` . Du måste ange `api-key` `Content-Type` rubrikerna och enligt beskrivningen ovan. 

I Postman går du till begäran om att **skapa DataSource** och sedan till **text** rutan. Du bör se följande kod:

```json
{
  "name" : "{{datasource-name}}",
  "description" : "Demo files to demonstrate knowledge store capabilities.",
  "type" : "azureblob",
  "credentials" : { "connectionString" : "{{storage-connection-string}}" },
  "container" : { "name" : "{{storage-container-name}}" }
}
```

Välj **Skicka** för att skicka en post-begäran. 

## <a name="create-the-skillset"></a>Skapa färdigheter 

Nästa steg är att ange färdigheter, som anger både de förbättringar som ska tillämpas och det kunskaps lager där resultatet ska lagras. I Postman väljer du fliken **skapa färdigheter** . Den här begäran skickar en skicka till `https://{{search-service-name}}.search.windows.net/skillsets/{{skillset-name}}?api-version={{api-version}}` . Ange `api-key` `Content-type` rubrikerna och som du gjorde tidigare. 

Det finns två stora objekt på översta nivån: `skills` och `knowledgeStore` . Varje objekt i `skills` objektet är en anriknings tjänst. Varje anriknings tjänst har `inputs` och `outputs` . `LanguageDetectionSkill`Har utdata `targetName` från `Language` . Värdet för den här noden används av de flesta andra färdigheter som inmatade. Källan är `document/Language` . Möjligheten att använda utdata från en nod som indata till en annan är ännu mer uppenbart i `ShaperSkill` , vilket anger hur data flödar till kunskaps lagrets tabeller.

`knowledge_store`Objektet ansluter till lagrings kontot via `{{storage-connection-string}}` Postman-variabeln. `knowledge_store` innehåller en uppsättning mappningar mellan det förbättrade dokumentet och tabeller och kolumner i kunskaps lagret. 

Om du vill generera färdigheter väljer du knappen **Skicka** i Postman för att placera begäran:

```json
{
    "name": "{{skillset-name}}",
    "description": "Skillset to detect language, extract key phrases, and detect sentiment",
    "skills": [ 
        {
            "@odata.type": "#Microsoft.Skills.Text.SplitSkill", 
            "context": "/document/reviews_text", "textSplitMode": "pages", "maximumPageLength": 5000,
            "inputs": [ 
                { "name": "text", "source": "/document/reviews_text" },
                { "name": "languageCode", "source": "/document/Language" }
            ],
            "outputs": [
                { "name": "textItems", "targetName": "pages" }
            ]
        },
        {
            "@odata.type": "#Microsoft.Skills.Text.SentimentSkill",
            "context": "/document/reviews_text/pages/*",
            "inputs": [
                { "name": "text", "source": "/document/reviews_text/pages/*" },
                { "name": "languageCode", "source": "/document/Language" }
            ],
            "outputs": [
                { "name": "score", "targetName": "Sentiment" }
            ]
        },
        {
            "@odata.type": "#Microsoft.Skills.Text.LanguageDetectionSkill",
            "context": "/document",
            "inputs": [
                { "name": "text", "source": "/document/reviews_text" }
            ],
            "outputs": [
                { "name": "languageCode", "targetName": "Language" }
            ]
        },
        {
            "@odata.type": "#Microsoft.Skills.Text.KeyPhraseExtractionSkill",
            "context": "/document/reviews_text/pages/*",
            "inputs": [
                { "name": "text",  "source": "/document/reviews_text/pages/*" },
                { "name": "languageCode",  "source": "/document/Language" }
            ],
            "outputs": [
                { "name": "keyPhrases" , "targetName": "Keyphrases" }
            ]
        },
        {
            "@odata.type": "#Microsoft.Skills.Util.ShaperSkill",
            "context": "/document",
            "inputs": [
                { "name": "name",  "source": "/document/name" },
                { "name": "reviews_date",  "source": "/document/reviews_date" },
                { "name": "reviews_rating",  "source": "/document/reviews_rating" },
                { "name": "reviews_text",  "source": "/document/reviews_text" },
                { "name": "reviews_title",  "source": "/document/reviews_title" },
                { "name": "AzureSearch_DocumentKey",  "source": "/document/AzureSearch_DocumentKey" },
                { 
                    "name": "pages",
                    "sourceContext": "/document/reviews_text/pages/*",
                    "inputs": [
                        { "name": "SentimentScore", "source": "/document/reviews_text/pages/*/Sentiment" },
                        { "name": "LanguageCode", "source": "/document/Language" },
                        { "name": "Page", "source": "/document/reviews_text/pages/*" },
                        { 
                            "name": "keyphrase", "sourceContext": "/document/reviews_text/pages/*/Keyphrases/*",
                            "inputs": [
                                { "name": "Keyphrases", "source": "/document/reviews_text/pages/*/Keyphrases/*" }
                            ]
                        }
                    ]
                }
            ],
            "outputs": [
                { "name": "output" , "targetName": "tableprojection" }
            ]
        }
    ],
    "knowledgeStore": {
        "storageConnectionString": "{{storage-connection-string}}",
        "projections": [
            {
                "tables": [
                    { "tableName": "hotelReviewsDocument", "generatedKeyName": "Documentid", "source": "/document/tableprojection" },
                    { "tableName": "hotelReviewsPages", "generatedKeyName": "Pagesid", "source": "/document/tableprojection/pages/*" },
                    { "tableName": "hotelReviewsKeyPhrases", "generatedKeyName": "KeyPhrasesid", "source": "/document/tableprojection/pages/*/keyphrase/*" },
                    { "tableName": "hotelReviewsSentiment", "generatedKeyName": "Sentimentid", "source": "/document/tableprojection/pages/*/sentiment/*" }
                ],
                "objects": []
            },
            {
                "tables": [
                    { 
                        "tableName": "hotelReviewsInlineDocument", "generatedKeyName": "Documentid", "sourceContext": "/document",
                        "inputs": [
                            { "name": "name", "source": "/document/name"},
                            { "name": "reviews_date", "source": "/document/reviews_date"},
                            { "name": "reviews_rating", "source": "/document/reviews_rating"},
                            { "name": "reviews_text", "source": "/document/reviews_text"},
                            { "name": "reviews_title", "source": "/document/reviews_title"},
                            { "name": "AzureSearch_DocumentKey", "source": "/document/AzureSearch_DocumentKey" }
                        ]
                    },
                    { 
                        "tableName": "hotelReviewsInlinePages", "generatedKeyName": "Pagesid", "sourceContext": "/document/reviews_text/pages/*",
                        "inputs": [
                            { "name": "SentimentScore", "source": "/document/reviews_text/pages/*/Sentiment"},
                            { "name": "LanguageCode", "source": "/document/Language"},
                            { "name": "Page", "source": "/document/reviews_text/pages/*" }
                        ]
                    },
                    { 
                        "tableName": "hotelReviewsInlineKeyPhrases", "generatedKeyName": "kpidv2", "sourceContext": "/document/reviews_text/pages/*/Keyphrases/*",
                        "inputs": [
                            { "name": "Keyphrases", "source": "/document/reviews_text/pages/*/Keyphrases/*" }
                        ]
                    }
                ],
                "objects": []
            }
        ]
    }
}
```

## <a name="create-the-indexer"></a>Skapa indexeraren

Det sista steget är att skapa indexeraren. Indexeraren läser data och aktiverar färdigheter. I Postman väljer du begäran om att **skapa indexerare** och granskar sedan bröd texten. Definitionen av indexeraren avser flera andra resurser som du redan har skapat: data källan, indexet och färdigheter. 

`parameters/configuration`Objektet styr hur indexeraren matar in data. I det här fallet är indata i ett enda dokument som har en rubrik rad och kommaavgränsade värden. Dokument nyckeln är en unik identifierare för dokumentet. Före kodningen är dokument nyckeln URL: en för käll dokumentet. Slutligen är färdigheter-utdataparametrar, som språk kod, sentiment och nyckel fraser mappade till deras platser i dokumentet. Även om det finns ett enda värde för `Language` , `Sentiment` används varje element i matrisen `pages` . `Keyphrases` är en matris som också används för varje element i `pages` matrisen.

När du har angett `api-key` `Content-type` rubrikerna och bekräftat att bröd texten i begäran liknar följande käll kod väljer du **Skicka** i Postman. Postman skickar en skicka-begäran till `https://{{search-service-name}}.search.windows.net/indexers/{{indexer-name}}?api-version={{api-version}}` . Azure Kognitiv sökning skapar och kör indexeraren. 

```json
{
    "name": "{{indexer-name}}",
    "dataSourceName": "{{datasource-name}}",
    "skillsetName": "{{skillset-name}}",
    "targetIndexName": "{{index-name}}",
    "parameters": {
        "configuration": {
            "dataToExtract": "contentAndMetadata",
            "parsingMode": "delimitedText",
            "firstLineContainsHeaders": true,
            "delimitedTextDelimiter": ","
        }
    },
    "fieldMappings": [
        {
            "sourceFieldName": "AzureSearch_DocumentKey",
            "targetFieldName": "AzureSearch_DocumentKey",
            "mappingFunction": { "name": "base64Encode" }
        }
    ],
    "outputFieldMappings": [
        { "sourceFieldName": "/document/reviews_text/pages/*/Keyphrases/*", "targetFieldName": "Keyphrases" },
        { "sourceFieldName": "/document/Language", "targetFieldName": "Language" },
        { "sourceFieldName": "/document/reviews_text/pages/*/Sentiment", "targetFieldName": "Sentiment" }
    ]
}
```

## <a name="run-the-indexer"></a>Köra indexeraren 

I Azure Portal går du till **översikts** sidan för Azure kognitiv sökning-tjänsten. Välj fliken **indexerare** och välj sedan **Hotels-Reviews-IXR**. Om indexeraren inte redan har körts väljer du **Kör**. Indexerings aktiviteten kan generera vissa varningar som rör språk igenkänning. Data innehåller vissa granskningar som är skrivna på språk som ännu inte stöds av kognitiva färdigheter. 

## <a name="next-steps"></a>Nästa steg

Nu när du har berikat dina data genom att använda Cognitive Services och projicerat resultaten till ett kunskaps lager, kan du använda Storage Explorer eller Power BI för att utforska din omfattande data uppsättning.

Information om hur du utforskar det här kunskaps lagret med hjälp av Storage Explorer finns i den här genom gången:

> [!div class="nextstepaction"]
> [Visa med Storage Explorer](knowledge-store-view-storage-explorer.md)

Information om hur du ansluter det här kunskaps lagret till Power BI finns i den här genom gången:

> [!div class="nextstepaction"]
> [Anslut med Power BI](knowledge-store-connect-power-bi.md)

Om du vill upprepa den här övningen eller testa en annan AI-förhands granskning tar du bort indexet **hotell reidxrs-** . Om du tar bort indexeraren återställs den kostnads fria dagliga transaktions räknaren till noll.