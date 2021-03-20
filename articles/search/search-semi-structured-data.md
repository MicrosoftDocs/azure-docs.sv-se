---
title: 'Självstudie: indexera halv strukturerade data i JSON-blobar'
titleSuffix: Azure Cognitive Search
description: 'Lär dig att indexera och söka i halv strukturerade Azure JSON-blobbar med Azure Kognitiv sökning REST-API: er och Postman.'
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 01/25/2021
ms.openlocfilehash: a7a010e3c60d6b96947597878fcd870e9845b2b3
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98746144"
---
# <a name="tutorial-index-json-blobs-from-azure-storage-using-rest"></a>Självstudie: indexera JSON-blobbar från Azure Storage med REST

Azure Kognitiv sökning kan indexera JSON-dokument och matriser i Azure Blob Storage med hjälp av en [indexerare](search-indexer-overview.md) som vet hur man kan läsa semi-strukturerade data. Halvstrukturerade data innehåller taggar eller märkord som separerar innehållet i data. Den delar skillnaden mellan ostrukturerade data som måste vara fullständigt indexerad och formellt strukturerade data som följer en data modell, till exempel ett Relations databas schema, som kan indexeras per fält.

I den här självstudien används Postman och [Sök REST-API: er](/rest/api/searchservice/) för att utföra följande uppgifter:

> [!div class="checklist"]
> * Konfigurera en Azure Kognitiv sökning-datakälla för en Azure Blob-behållare
> * Skapa ett Azure Kognitiv sökning-index som innehåller sökbart innehåll
> * Konfigurera och kör en indexerare för att läsa behållaren och extrahera sökbart innehåll från Azure Blob Storage
> * Söka i indexet som du precis skapade

Om du inte har någon Azure-prenumeration kan du [skapa ett kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

+ [Azure Storage](../storage/common/storage-account-create.md)
+ [Skrivbordsappen Postman](https://www.getpostman.com/)
+ [Skapa](search-create-service-portal.md) eller [hitta en befintlig Sök tjänst](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) 

> [!Note]
> Du kan använda den kostnads fria tjänsten för den här självstudien. En kostnads fri Sök tjänst begränsar dig till tre index, tre indexerare och tre data källor. I den här kursen skapar du en av varje. Innan du börjar bör du kontrol lera att du har utrymme på tjänsten för att godkänna de nya resurserna.

## <a name="download-files"></a>Ladda ned filer

[Clinical-trials-json.zip](https://github.com/Azure-Samples/storage-blob-integration-with-cdn-search-hdi/raw/master/clinical-trials-json.zip) innehåller de data som används i den här självstudien. Ladda ned och zippa upp den här filen till en egen mapp. Data härstammar från [ClinicalTrials.gov](https://clinicaltrials.gov/ct2/results), konverteras till JSON för den här självstudien.

## <a name="1---create-services"></a>1 – skapa tjänster

I den här självstudien används Azure Kognitiv sökning för indexering och frågor, och Azure Blob Storage för att tillhandahålla data. 

Skapa om möjligt både i samma region och resurs grupp för närhet och hanterbarhet. I praktiken kan ditt Azure Storage-konto finnas i vilken region som helst.

### <a name="start-with-azure-storage"></a>Börja med Azure Storage

1. [Logga](https://portal.azure.com/) in på Azure Portal och klicka på **+ skapa resurs**.

1. Sök efter *lagrings konto* och välj Microsofts erbjudande för lagrings konto.

   :::image type="content" source="media/cognitive-search-tutorial-blob/storage-account.png" alt-text="Skapa lagrings konto" border="false":::

1. På fliken grundläggande måste följande objekt vara obligatoriska. Acceptera standardvärdena för allt annat.

   + **Resursgrupp**. Välj en befintlig eller skapa en ny, men Använd samma grupp för alla tjänster så att du kan hantera dem tillsammans.

   + **Namn på lagringskonto**. Om du tror att du kan ha flera resurser av samma typ, använder du namnet på disambiguate efter typ och region, till exempel *blobstoragewestus*. 

   + **Plats**. Om möjligt väljer du samma plats som används för Azure Kognitiv sökning och Cognitive Services. Med en enda plats annulleras bandbredds avgifter.

   + **Typ av konto**. Välj standard, *StorageV2 (generell användning v2)*.

1. Klicka på **Granska + skapa** för att skapa tjänsten.

1. När den har skapats klickar **du på gå till resursen** för att öppna översikts sidan.

1. Klicka på **blobs** -tjänsten.

1. [Skapa en BLOB-behållare](../storage/blobs/storage-quickstart-blobs-portal.md) som innehåller exempel data. Du kan ställa in den offentliga åtkomst nivån på alla giltiga värden.

1. När behållaren har skapats öppnar du den och väljer **Ladda upp** i kommando fältet.

   :::image type="content" source="media/search-semi-structured-data/upload-command-bar.png" alt-text="Ladda upp i kommando fältet" border="false":::

1. Navigera till mappen som innehåller exempelfilerna. Markera alla och klicka sedan på **överför**.

   :::image type="content" source="media/search-semi-structured-data/clinicalupload.png" alt-text="Ladda upp filer" border="false":::

När överföringen är klar ska filerna visas i en egen undermapp i datacontainern.

### <a name="azure-cognitive-search"></a>Azure Cognitive Search

Nästa resurs är Azure-Kognitiv sökning, som du kan [skapa i portalen](search-create-service-portal.md). Du kan använda den kostnads fria nivån för att slutföra den här genom gången. 

Som med Azure Blob Storage kan du ägna en stund åt att samla in åtkomst nyckeln. Vidare måste du, när du börjar strukturera förfrågningar, ange slut punkt och Admin-API-nyckel som används för att autentisera varje begäran.

### <a name="get-a-key-and-url"></a>Hämta en nyckel och URL

För att kunna göra REST-anrop behöver du tjänstens webbadress och en åtkomstnyckel för varje begäran. En Sök tjänst skapas med båda, så om du har lagt till Azure-Kognitiv sökning till din prenumeration följer du dessa steg för att få den information som krävs:

1. [Logga](https://portal.azure.com/)in på Azure Portal och hämta URL: en på sidan **Översikt över** Sök tjänsten. Här följer ett exempel på hur en slutpunkt kan se ut: `https://mydemo.search.windows.net`.

1. I **Inställningar**  >  **nycklar**, hämtar du en administratörs nyckel för fullständiga rättigheter till tjänsten. Det finns två utbytbara administratörs nycklar, som tillhandahålls för affärs kontinuitet om du behöver rulla en över. Du kan använda antingen den primära eller sekundära nyckeln på begär Anden för att lägga till, ändra och ta bort objekt.

   :::image type="content" source="media/search-get-started-rest/get-url-key.png" alt-text="Hämta en HTTP-slutpunkt och åtkomst nyckel" border="false":::

Alla begär Anden kräver en API-nyckel på varje begäran som skickas till din tjänst. En giltig nyckel upprättar förtroende, i varje begäran, mellan programmet som skickar begäran och tjänsten som hanterar den.

## <a name="2---set-up-postman"></a>2 – Konfigurera PostMan

Starta Postman och konfigurera en HTTP-begäran. Om du inte känner till det här verktyget kan du läsa [skapa ett Sök index med hjälp av REST API: er](search-get-started-rest.md).

Metoderna för begäran för varje anrop i den här självstudien är **post** och **Get**. Du gör tre API-anrop till din Sök tjänst för att skapa en data källa, ett index och en indexerare. Datakällan innehåller en pekare till ditt lagringskonto och dina JSON-data. Din söktjänst gör anslutningen vid inläsning av data.

I sidhuvud anger du "Content-Type" till `application/json` och anger `api-key` admin-API-nyckeln för din Azure kognitiv sökning-tjänst. När du har angett rubrikerna kan du använda dem för varje begäran i den här övningen.

  :::image type="content" source="media/search-get-started-rest/postman-url.png" alt-text="URL och rubrik för Postman-begäran" border="false":::

URI: er måste ange en API-version och varje anrop ska returnera en **201 som skapats**. Den allmänt tillgängliga API-versionen för att använda JSON-matriser är `2020-06-30` .

## <a name="3---create-a-data-source"></a>3-skapa en data Källa

Med [skapa data källans API](/rest/api/searchservice/create-data-source) skapas ett Azure kognitiv sökning-objekt som anger vilka data som ska indexeras.

1. Ange slut punkten för det här anropet till `https://[service name].search.windows.net/datasources?api-version=2020-06-30` . Ersätt `[service name]` med namnet på söktjänsten. 

1. Kopiera följande JSON till begär ande texten.

    ```json
    {
        "name" : "clinical-trials-json-ds",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "DefaultEndpointsProtocol=https;AccountName=[storage account name];AccountKey=[storage account key];" },
        "container" : { "name" : "[blob container name]"}
    }
    ```

1. Ersätt anslutnings strängen med en giltig sträng för ditt konto.

1. Ersätt "[BLOB container Name]" med den behållare som du skapade för exempel data. 

1. Skicka begäran. Svaret ska se ut så här:

    ```json
    {
        "@odata.context": "https://exampleurl.search.windows.net/$metadata#datasources/$entity",
        "@odata.etag": "\"0x8D505FBC3856C9E\"",
        "name": "clinical-trials-json-ds",
        "description": null,
        "type": "azureblob",
        "subtype": null,
        "credentials": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=[mystorageaccounthere];AccountKey=[[myaccountkeyhere]]];"
        },
        "container": {
            "name": "[mycontainernamehere]",
            "query": null
        },
        "dataChangeDetectionPolicy": null,
        "dataDeletionDetectionPolicy": null
    }
    ```

## <a name="4---create-an-index"></a>4 – skapa ett index

Det andra anropet är [skapa index-API](/rest/api/searchservice/create-index), vilket skapar ett Azure kognitiv sökning-index som lagrar alla sökbara data. Ett index anger alla parametrar och deras attribut.

1. Ange slut punkten för det här anropet till `https://[service name].search.windows.net/indexes?api-version=2020-06-30` . Ersätt `[service name]` med namnet på söktjänsten.

1. Kopiera följande JSON till begär ande texten.

    ```json
    {
      "name": "clinical-trials-json-index",  
      "fields": [
      {"name": "FileName", "type": "Edm.String", "searchable": false, "retrievable": true, "facetable": false, "filterable": false, "sortable": true},
      {"name": "Description", "type": "Edm.String", "searchable": true, "retrievable": false, "facetable": false, "filterable": false, "sortable": false},
      {"name": "MinimumAge", "type": "Edm.Int32", "searchable": false, "retrievable": true, "facetable": true, "filterable": true, "sortable": true},
      {"name": "Title", "type": "Edm.String", "searchable": true, "retrievable": true, "facetable": false, "filterable": true, "sortable": true},
      {"name": "URL", "type": "Edm.String", "searchable": false, "retrievable": false, "facetable": false, "filterable": false, "sortable": false},
      {"name": "MyURL", "type": "Edm.String", "searchable": false, "retrievable": true, "facetable": false, "filterable": false, "sortable": false},
      {"name": "Gender", "type": "Edm.String", "searchable": false, "retrievable": true, "facetable": true, "filterable": true, "sortable": false},
      {"name": "MaximumAge", "type": "Edm.Int32", "searchable": false, "retrievable": true, "facetable": true, "filterable": true, "sortable": true},
      {"name": "Summary", "type": "Edm.String", "searchable": true, "retrievable": true, "facetable": false, "filterable": false, "sortable": false},
      {"name": "NCTID", "type": "Edm.String", "key": true, "searchable": true, "retrievable": true, "facetable": false, "filterable": true, "sortable": true},
      {"name": "Phase", "type": "Edm.String", "searchable": false, "retrievable": true, "facetable": true, "filterable": true, "sortable": false},
      {"name": "Date", "type": "Edm.String", "searchable": false, "retrievable": true, "facetable": false, "filterable": false, "sortable": true},
      {"name": "OverallStatus", "type": "Edm.String", "searchable": false, "retrievable": true, "facetable": true, "filterable": true, "sortable": false},
      {"name": "OrgStudyId", "type": "Edm.String", "searchable": true, "retrievable": true, "facetable": false, "filterable": true, "sortable": false},
      {"name": "HealthyVolunteers", "type": "Edm.String", "searchable": false, "retrievable": true, "facetable": true, "filterable": true, "sortable": false},
      {"name": "Keywords", "type": "Collection(Edm.String)", "searchable": true, "retrievable": true, "facetable": true, "filterable": false, "sortable": false},
      {"name": "metadata_storage_last_modified", "type":"Edm.DateTimeOffset", "searchable": false, "retrievable": true, "filterable": true, "sortable": false},
      {"name": "metadata_storage_size", "type":"Edm.String", "searchable": false, "retrievable": true, "filterable": true, "sortable": false},
      {"name": "metadata_content_type", "type":"Edm.String", "searchable": true, "retrievable": true, "filterable": true, "sortable": false}
      ]
    }
   ```

1. Skicka begäran. Svaret ska se ut så här:

    ```json
    {
        "@odata.context": "https://exampleurl.search.windows.net/$metadata#indexes/$entity",
        "@odata.etag": "\"0x8D505FC00EDD5FA\"",
        "name": "clinical-trials-json-index",
        "fields": [
            {
                "name": "FileName",
                "type": "Edm.String",
                "searchable": false,
                "filterable": false,
                "retrievable": true,
                "sortable": true,
                "facetable": false,
                "key": false,
                "indexAnalyzer": null,
                "searchAnalyzer": null,
                "analyzer": null,
                "synonymMaps": []
            },
            {
                "name": "Description",
                "type": "Edm.String",
                "searchable": true,
                "filterable": false,
                "retrievable": false,
                "sortable": false,
                "facetable": false,
                "key": false,
                "indexAnalyzer": null,
                "searchAnalyzer": null,
                "analyzer": null,
                "synonymMaps": []
            },
            ...
          }
    ```

## <a name="5---create-and-run-an-indexer"></a>5 – skapa och kör en indexerare

En indexerare ansluter till data källan, importerar data till mål Sök indexet och tillhandahåller eventuellt ett schema för att automatisera data uppdateringen. REST API är [skapa indexerare](/rest/api/searchservice/create-indexer).

1. Ange URI för det här anropet `https://[service name].search.windows.net/indexers?api-version=2020-06-30` . Ersätt `[service name]` med namnet på söktjänsten.

1. Kopiera följande JSON till begär ande texten.

    ```json
    {
      "name" : "clinical-trials-json-indexer",
      "dataSourceName" : "clinical-trials-json-ds",
      "targetIndexName" : "clinical-trials-json-index",
      "parameters" : { "configuration" : { "parsingMode" : "jsonArray" } }
    }
    ```

1. Skicka begäran. Begäran bearbetas omedelbart. När svaret kommer tillbaka har du ett index som är full text sökbar. Svaret ska se ut så här:

    ```json
    {
        "@odata.context": "https://exampleurl.search.windows.net/$metadata#indexers/$entity",
        "@odata.etag": "\"0x8D505FDE143D164\"",
        "name": "clinical-trials-json-indexer",
        "description": null,
        "dataSourceName": "clinical-trials-json-ds",
        "targetIndexName": "clinical-trials-json-index",
        "schedule": null,
        "parameters": {
            "batchSize": null,
            "maxFailedItems": null,
            "maxFailedItemsPerBatch": null,
            "base64EncodeKeys": null,
            "configuration": {
                "parsingMode": "jsonArray"
            }
        },
        "fieldMappings": [],
        "enrichers": [],
        "disabled": null
    }
    ```

## <a name="6---search-your-json-files"></a>6 – Sök i dina JSON-filer

Du kan börja söka så snart det första dokumentet har lästs in.

1. Ändra verbet som ska **hämtas**.

1. Ange URI för det här anropet `https://[service name].search.windows.net/indexes/clinical-trials-json-index/docs?search=*&api-version=2020-06-30&$count=true` . Ersätt `[service name]` med namnet på söktjänsten.

1. Skicka begäran. Det här är en ospecificerad text Sök fråga som returnerar alla fält som är markerade som hämtnings bara i indexet, tillsammans med ett dokument antal. Svaret ska se ut så här:

    ```json
    {
        "@odata.context": "https://exampleurl.search.windows.net/indexes('clinical-trials-json-index')/$metadata#docs(*)",
        "@odata.count": 100,
        "value": [
            {
                "@search.score": 1.0,
                "FileName": "NCT00000102.txt",
                "MinimumAge": 14,
                "Title": "Congenital Adrenal Hyperplasia: Calcium Channels as Therapeutic Targets",
                "MyURL": "https://azure.storagedemos.com/clinical-trials/NCT00000102.txt",
                "Gender": "Both",
                "MaximumAge": 35,
                "Summary": "This study will test the ability of extended release nifedipine (Procardia XL), a blood pressure medication, to permit a decrease in the dose of glucocorticoid medication children take to treat congenital adrenal hyperplasia (CAH).",
                "NCTID": "NCT00000102",
                "Phase": "Phase 1/Phase 2",
                "Date": "ClinicalTrials.gov processed this data on October 25, 2016",
                "OverallStatus": "Completed",
                "OrgStudyId": "NCRR-M01RR01070-0506",
                "HealthyVolunteers": "No",
                "Keywords": [],
                "metadata_storage_last_modified": "2019-04-09T18:16:24Z",
                "metadata_storage_size": "33060",
                "metadata_content_type": null
            },
            . . . 
    ```

1. Lägg till `$select` Frågeparametern för att begränsa resultatet till färre fält: `https://[service name].search.windows.net/indexes/clinical-trials-json-index/docs?search=*&$select=Gender,metadata_storage_size&api-version=2020-06-30&$count=true` .  För den här frågan matchar 100 dokument, men som standard returnerar Azure Kognitiv sökning bara 50 i resultaten.

   :::image type="content" source="media/search-semi-structured-data/lastquery.png" alt-text="Parametriserad fråga" border="false":::

1. Ett exempel på en mer komplex fråga skulle innehålla `$filter=MinimumAge ge 30 and MaximumAge lt 75` , som endast returnerar resultat där parametrarna minimum är större än eller lika med 30 och Max värdet är mindre än 75. Ersätt `$select` uttrycket med `$filter` uttrycket.

   :::image type="content" source="media/search-semi-structured-data/metadatashort.png" alt-text="Halvstrukturerad sökning" border="false":::

Du kan också använda logiska operatorer (och, eller, inte) och jämförelse operatorer (EQ, Ne, gt, lt, ge, Le). Strängjämförelser är skiftlägeskänsliga. Mer information och exempel finns i [skapa en enkel fråga](search-query-simple-examples.md).

> [!NOTE]
> Parametern `$filter` fungerar endast med metadata som markerades som filtrerbara när indexet skapades.

## <a name="reset-and-rerun"></a>Återställa och köra igen

I de tidiga experiment stegen i utvecklingen är den mest praktiska metoden för design upprepning att ta bort objekten från Azure Kognitiv sökning och tillåta att koden återskapas. Resursnamn är unika. Om du tar bort ett objekt kan du återskapa det med samma namn.

Du kan använda portalen för att ta bort index, indexerare och data källor. Eller Använd **ta bort** och ange URL: er för varje objekt. Följande kommando tar bort en indexerare.

```http
DELETE https://[YOUR-SERVICE-NAME].search.windows.net/indexers/clinical-trials-json-indexer?api-version=2020-06-30
```

Statuskod 204 returneras vid borttagning.

## <a name="clean-up-resources"></a>Rensa resurser

När du arbetar i din egen prenumeration är det en bra idé att ta bort de resurser som du inte längre behöver i slutet av projektet. Resurser som fortsätter att köras kostar pengar. Du kan ta bort resurser individuellt eller ta bort resursgruppen om du vill ta bort hela uppsättningen resurser.

Du kan hitta och hantera resurser i portalen med hjälp av länken alla resurser eller resurs grupper i det vänstra navigerings fönstret.

## <a name="next-steps"></a>Nästa steg

Nu när du är bekant med grunderna för Azure Blob-indexering, tar vi en närmare titt på indexerings konfigurationen för JSON-blobbar i Azure Storage.

> [!div class="nextstepaction"]
> [Konfigurera JSON BLOB-indexering](search-howto-index-json-blobs.md)