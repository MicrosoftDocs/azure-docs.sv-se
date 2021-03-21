---
title: 'Självstudie: python och AI över Azure-blobbar'
titleSuffix: Azure Cognitive Search
description: 'Stega genom ett exempel på text extrahering och naturlig språk bearbetning över innehåll i Blob Storage med en Jupyter python-anteckningsbok och Azure Kognitiv sökning REST-API: er.'
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.devlang: python
ms.topic: tutorial
ms.date: 09/25/2020
ms.custom: devx-track-python
ms.openlocfilehash: ea1cc022cbea7dbf3d1fa12cb83cfe3084b28560
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "92788092"
---
# <a name="tutorial-use-python-and-ai-to-generate-searchable-content-from-azure-blobs"></a>Självstudie: Använd python och AI för att generera sökbart innehåll från Azure-blobbar

Om du har ostrukturerad text eller avbildningar i Azure Blob Storage kan en [AI-pipeline](cognitive-search-concept-intro.md) utvinna information och skapa nytt innehåll som är användbart för full texts ökning eller kunskaps utvinnings scenarier. Även om en pipeline kan bearbeta bilder fokuserar den här python-kursen på text, använder språk identifiering och bearbetning av naturligt språk för att skapa nya fält som du kan använda i frågor, ansikts och filter.

Den här självstudien använder python och [Sök REST-API: er](/rest/api/searchservice/) för att utföra följande uppgifter:

> [!div class="checklist"]
> * Börja med hela dokument (ostrukturerad text) som PDF, HTML, DOCX och PPTX i Azure Blob Storage.
> * Definiera en pipeline som extraherar text, identifierar språk, identifierar entiteter och identifierar viktiga fraser.
> * Definiera ett index för att lagra utdata (RAW-innehåll, plus pipeline-genererade namn-värdepar).
> * Kör pipelinen för att starta omvandlingar och analys och för att skapa och läsa in indexet.
> * Utforska resultat med fullständig texts ökning och en omfattande frågesyntax.

Om du inte har någon Azure-prenumeration kan du öppna ett [kostnads fritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

+ [Azure Storage](https://azure.microsoft.com/services/storage/)
+ [Anaconda 3,7](https://www.anaconda.com/distribution/#download-section)
+ [Skapa](search-create-service-portal.md) eller [hitta en befintlig Sök tjänst](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) 

> [!Note]
> Du kan använda den kostnads fria tjänsten för den här självstudien. En kostnads fri Sök tjänst begränsar dig till tre index, tre indexerare och tre data källor. I den här kursen skapar du en av varje. Innan du börjar bör du kontrol lera att du har utrymme på tjänsten för att godkänna de nya resurserna.

## <a name="download-files"></a>Ladda ned filer

1. Öppna den här [OneDrive-mappen](https://1drv.ms/f/s!As7Oy81M_gVPa-LCb5lC_3hbS-4) och klicka på **Ladda ned** i det övre vänstra hörnet för att kopiera filerna till datorn. 

1. Högerklicka på zip-filen och välj **extrahera alla**. Det finns 14 filer av olika typer. Du använder 7 för den här övningen.

## <a name="1---create-services"></a>1 – skapa tjänster

I den här självstudien används Azure Kognitiv sökning för indexering och frågor, Cognitive Services på Server delen för AI-anrikning och Azure Blob Storage för att tillhandahålla data. Den här självstudien finns kvar under den kostnads fria allokeringen av 20 transaktioner per indexerare per dag på Cognitive Services, så de enda tjänsterna du behöver skapa är Sök och lagring.

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

1. Klicka på **+ container** för att skapa en behållare och ge den namnet *kugg hjuls-search-demo*.

1. Välj *kugg hjuls-search-demo* och klicka sedan på **Ladda upp** för att öppna mappen där du sparade nedladdnings filerna. Välj alla icke-bildfiler. Du bör ha 7 filer. Klicka på **OK** för att ladda upp.

   :::image type="content" source="media/cognitive-search-tutorial-blob/sample-files.png" alt-text="Ladda upp exempelfiler" border="false":::

1. Innan du lämnar Azure Storage får du en anslutnings sträng så att du kan formulera en anslutning i Azure Kognitiv sökning. 

   1. Gå tillbaka till sidan Översikt för ditt lagrings konto (vi använde *blobstragewestus* som exempel). 
   
   1. I det vänstra navigerings fönstret väljer du **åtkomst nycklar** och kopierar en av anslutnings strängarna. 

   Anslutnings strängen är en URL som liknar följande exempel:

      ```http
      DefaultEndpointsProtocol=https;AccountName=<storageaccountname>;AccountKey=<your account key>;EndpointSuffix=core.windows.net
      ```

1. Spara anslutnings strängen i anteckningar. Du behöver det senare när du konfigurerar anslutningen till data källan.

### <a name="cognitive-services"></a>Cognitive Services

AI-berikning backas upp av Cognitive Services, inklusive Textanalys och Visuellt innehåll för naturligt språk och bild bearbetning. Om målet var att slutföra en faktisk prototyp eller ett projekt, skulle du i den här punkten etablera Cognitive Services (i samma region som Azure Kognitiv sökning) så att du kan koppla den till indexerings åtgärder.

Eftersom den här självstudien bara använder 7 transaktioner kan du hoppa över resurs etableringen eftersom Azure Kognitiv sökning kan ansluta till Cognitive Services för 20 kostnads fria transaktioner per indexerare. Den kostnads fria allokeringen räcker. För större projekt bör du planera för etablering Cognitive Services på S0-nivån betala per användning. Mer information finns i [bifoga Cognitive Services](cognitive-search-attach-cognitive-services.md).

### <a name="azure-cognitive-search"></a>Azure Cognitive Search

Den tredje komponenten är Azure-Kognitiv sökning, som du kan [skapa i portalen](search-create-service-portal.md). Du kan använda den kostnads fria nivån för att slutföra den här genom gången. 

Som med Azure Blob Storage kan du ägna en stund åt att samla in åtkomst nyckeln. Vidare måste du, när du börjar strukturera förfrågningar, ange slut punkt och Admin-API-nyckel som används för att autentisera varje begäran.

### <a name="get-an-admin-api-key-and-url-for-azure-cognitive-search"></a>Hämta en Admin API-nyckel och URL för Azure Kognitiv sökning

1. [Logga](https://portal.azure.com/)in på Azure Portal och hämta namnet på din Sök tjänst på sidan **Översikt över** Sök tjänsten. Du kan bekräfta tjänst namnet genom att granska slut punkts-URL: en. Om slut punkts-URL: en var `https://mydemo.search.windows.net` , är tjänstens namn `mydemo` .

2. I **Inställningar**  >  **nycklar**, hämtar du en administratörs nyckel för fullständiga rättigheter till tjänsten. Det finns två utbytbara administratörs nycklar, som tillhandahålls för affärs kontinuitet om du behöver rulla en över. Du kan använda antingen den primära eller sekundära nyckeln på begär Anden för att lägga till, ändra och ta bort objekt.

   Hämta även frågans nyckel. Det är en bra idé att utfärda förfrågningar med skrivskyddad åtkomst.

   :::image type="content" source="media/search-get-started-javascript/service-name-and-keys.png" alt-text="Hämta tjänstens namn och administratör och fråge nycklar" border="false":::

Alla begär Anden kräver en API-nyckel i rubriken för varje begäran som skickas till din tjänst. En giltig nyckel upprättar förtroende per begäran mellan programmet som skickar begäran och tjänsten som hanterar den.

## <a name="2---start-a-notebook"></a>2 – Starta en bärbar dator

Skapa antecknings boken med hjälp av följande instruktioner eller ladda ned en färdig bärbar dator från [Azure-Search-python-samples lagrings platsen](https://github.com/Azure-Samples/azure-search-python-samples/tree/master/Tutorial-AI-Enrichment).

Använd Anaconda Navigator för att starta Jupyter Notebook och skapa en ny python 3-anteckningsbok.

I din bärbara dator kör du det här skriptet för att läsa in de bibliotek som används för att arbeta med JSON och utforma HTTP-begäranden.

```python
import json
import requests
from pprint import pprint
```

I samma antecknings bok definierar du namnen på data källan, indexet, Indexer och färdigheter. Kör det här skriptet för att konfigurera namnen för den här självstudien.

```python
# Define the names for the data source, skillset, index and indexer
datasource_name = "cogsrch-py-datasource"
skillset_name = "cogsrch-py-skillset"
index_name = "cogsrch-py-index"
indexer_name = "cogsrch-py-indexer"
```

I följande skript ersätter du plats hållarna för Sök tjänsten (ditt-SEARCH-SERVICE-NAME) och Admin API-nyckeln (din-ADMIN-API-nyckel) och kör sedan den för att konfigurera Sök tjänstens slut punkt.

```python
# Setup the endpoint
endpoint = 'https://<YOUR-SEARCH-SERVICE-NAME>.search.windows.net/'
headers = {'Content-Type': 'application/json',
           'api-key': '<YOUR-ADMIN-API-KEY>'}
params = {
    'api-version': '2020-06-30'
}
```

## <a name="3---create-the-pipeline"></a>3 – skapa pipelinen

I Azure Kognitiv sökning sker AI-bearbetning under indexering (eller data inmatning). I den här delen av gå igenom skapas fyra objekt: data källa, index definition, färdigheter, indexerare. 

### <a name="step-1-create-a-data-source"></a>Steg 1: Skapa en datakälla

Ett [data käll objekt](/rest/api/searchservice/create-data-source) ger anslutnings strängen till BLOB-behållaren som innehåller filerna.

I följande skript ersätter du plats hållaren YOUR-BLOB-RESOURCE-STRING med anslutnings strängen för blobben som du skapade i föregående steg. Ersätt platshållartexten för behållaren. Kör sedan skriptet för att skapa en data källa med namnet `cogsrch-py-datasource` .

```python
# Create a data source
datasourceConnectionString = "<YOUR-BLOB-RESOURCE-CONNECTION-STRING>"
datasource_payload = {
    "name": datasource_name,
    "description": "Demo files to demonstrate cognitive search capabilities.",
    "type": "azureblob",
    "credentials": {
        "connectionString": datasourceConnectionString
    },
    "container": {
        "name": "<YOUR-BLOB-CONTAINER-NAME>"
    }
}
r = requests.put(endpoint + "/datasources/" + datasource_name,
                 data=json.dumps(datasource_payload), headers=headers, params=params)
print(r.status_code)
```

Begäran ska returnera status koden 201 som bekräftar att det lyckades.

På sidan för Sök tjänstens instrument panel i Azure Portal kontrollerar du att cogsrch-py-DataSource visas i listan **data källor** . Klicka på **Uppdatera** för att uppdatera sidan.

:::image type="content" source="media/cognitive-search-tutorial-blob-python/py-data-source-tile.png" alt-text="Panelen data källor i portalen" border="false":::

### <a name="step-2-create-a-skillset"></a>Steg 2: skapa en färdigheter

I det här steget ska du definiera en uppsättning med anriknings steg som ska tillämpas på dina data. Du kallar varje berikande steg för en *kunskap*, och uppsättningen med berikande steg för en *kunskapsuppsättning*. I den här självstudien används [inbyggda kognitiva kunskaper](cognitive-search-predefined-skills.md) för färdigheter:

+ [Enhets igenkänning](cognitive-search-skill-entity-recognition.md) för extrahering av namn på organisationer från innehåll i BLOB-behållaren.

+ [Språkidentifiering](cognitive-search-skill-language-detection.md) för att identifiera innehållets språk.

+ [Textuppdelning](cognitive-search-skill-textsplit.md) för att dela upp stort innehåll i mindre delar innan du anropar färdigheten extrahering av nyckelfraser. Extrahering av nyckelfraser accepterar indata på 50 000 tecken eller mindre. Några av exempelfilerna måste delas upp för att rymmas inom gränsen.

+ [Extrahering av nyckelfraser](cognitive-search-skill-keyphrases.md) för att hämta viktigaste nyckelfraserna. 

Kör följande skript för att skapa en färdigheter som heter `cogsrch-py-skillset` .

```python
# Create a skillset
skillset_payload = {
    "name": skillset_name,
    "description":
    "Extract entities, detect language and extract key-phrases",
    "skills":
    [
        {
            "@odata.type": "#Microsoft.Skills.Text.EntityRecognitionSkill",
            "categories": ["Organization"],
            "defaultLanguageCode": "en",
            "inputs": [
                {
                    "name": "text", 
                    "source": "/document/content"
                }
            ],
            "outputs": [
                {
                    "name": "organizations", 
                    "targetName": "organizations"
                }
            ]
        },
        {
            "@odata.type": "#Microsoft.Skills.Text.LanguageDetectionSkill",
            "inputs": [
                {
                    "name": "text", 
                    "source": "/document/content"
                }
            ],
            "outputs": [
                {
                    "name": "languageCode",
                    "targetName": "languageCode"
                }
            ]
        },
        {
            "@odata.type": "#Microsoft.Skills.Text.SplitSkill",
            "textSplitMode": "pages",
            "maximumPageLength": 4000,
            "inputs": [
                {
                    "name": "text",
                    "source": "/document/content"
                },
                {
                    "name": "languageCode",
                    "source": "/document/languageCode"
                }
            ],
            "outputs": [
                {
                    "name": "textItems",
                    "targetName": "pages"
                }
            ]
        },
        {
            "@odata.type": "#Microsoft.Skills.Text.KeyPhraseExtractionSkill",
            "context": "/document/pages/*",
            "inputs": [
                {
                    "name": "text", 
                    "source": "/document/pages/*"
                },
                {
                    "name": "languageCode", 
                    "source": "/document/languageCode"
                }
            ],
            "outputs": [
                {
                    "name": "keyPhrases",
                    "targetName": "keyPhrases"
                }
            ]
        }
    ]
}

r = requests.put(endpoint + "/skillsets/" + skillset_name,
                 data=json.dumps(skillset_payload), headers=headers, params=params)
print(r.status_code)
```

Begäran ska returnera status koden 201 som bekräftar att det lyckades.

Extraherings kunskaper för nyckel fraser används för varje sida. Genom att ställa in kontexten för `"document/pages/*"` kör du den här berikaren för varje medlem i matrisen dokument/sidor (för varje sida i dokumentet).

Varje kunskap körs på innehållet i dokumentet. Under bearbetningen kommer Azure Kognitiv sökning att knäcka varje dokument för att läsa innehåll från olika fil format. Text som hittas i käll filen placeras i ett `content` fält, en för varje dokument. Ange därför inmatade som `"/document/content"` .

En grafisk representation av kunskapsuppsättningen visas nedan.

:::image type="content" source="media/cognitive-search-tutorial-blob/skillset.png" alt-text="Förstå en färdigheter" border="false":::

Utdata kan mappas till ett index, användas som indata till en underordnad färdighet eller båda, som är fallet med språkkod. I indexet kan en språkkod användas för filtrering. Som indata används språkkoden av textanalyskunskaper för att informera om de språkliga reglerna kring ordnedbrytning.

Mer information om grunderna i kunskapsuppsättningar finns i [Definiera en kunskapsuppsättning](cognitive-search-defining-skillset.md).

### <a name="step-3-create-an-index"></a>Steg 3: skapa ett index

I det här avsnittet definierar du index schemat genom att ange de fält som ska ingå i det sökbara indexet och ange sökattributen för varje fält. Fält har en typ och kan ta attribut som bestämmer hur fältet ska användas (sökbart, sorteringsbart och så vidare). Fältnamn i ett index krävs inte för att matcha fältnamn identiskt i källan. I ett senare steg lägger du till fältmappningar i en indexerare för att ansluta källa-mål-fält. För det här steget definiera indexet med fältnamnkonventioner som är relevanta för ditt sökprogram.

Den här övningen använder följande fält och fälttyp:

| fält-namn: | ID         | innehåll   | languageCode | keyPhrases         | organisationer     |
|--------------|----------|-------|----------|--------------------|-------------------|
| fält-typer: | Edm.String|Edm.String| Edm.String| List<Edm.String>  | List<Edm.String>  |

Kör det här skriptet för att skapa indexet med namnet `cogsrch-py-index` .

```python
# Create an index
index_payload = {
    "name": index_name,
    "fields": [
        {
            "name": "id",
            "type": "Edm.String",
            "key": "true",
            "searchable": "true",
            "filterable": "false",
            "facetable": "false",
            "sortable": "true"
        },
        {
            "name": "content",
            "type": "Edm.String",
            "sortable": "false",
            "searchable": "true",
            "filterable": "false",
            "facetable": "false"
        },
        {
            "name": "languageCode",
            "type": "Edm.String",
            "searchable": "true",
            "filterable": "false",
            "facetable": "false"
        },
        {
            "name": "keyPhrases",
            "type": "Collection(Edm.String)",
            "searchable": "true",
            "filterable": "false",
            "facetable": "false"
        },
        {
            "name": "organizations",
            "type": "Collection(Edm.String)",
            "searchable": "true",
            "sortable": "false",
            "filterable": "false",
            "facetable": "false"
        }
    ]
}

r = requests.put(endpoint + "/indexes/" + index_name,
                 data=json.dumps(index_payload), headers=headers, params=params)
print(r.status_code)
```

Begäran ska returnera status koden 201 som bekräftar att det lyckades.

Mer information om hur du definierar ett index finns i [skapa index (Azure Kognitiv sökning REST API)](/rest/api/searchservice/create-index).

### <a name="step-4-create-and-run-an-indexer"></a>Steg 4: skapa och köra en indexerare

En [indexerare](/rest/api/searchservice/create-indexer) driver pipelinen. De tre komponenter som du har skapat hittills (data källa, färdigheter, index) är indata till en indexerare. Att skapa indexeraren på Azure Kognitiv sökning är den händelse som placerar hela pipelinen i rörelse. 

Om du vill koppla ihop dessa objekt i en indexerare måste du definiera fält mappningar.

+ `"fieldMappings"`Bearbetas före färdigheter och mappar käll fälten från data källan till mål fälten i ett index. Om fält namn och typer är desamma i båda ändar, krävs ingen mappning.

+ `"outputFieldMappings"`Bearbetas efter färdigheter, som refererar till `"sourceFieldNames"` som inte finns förrän dokumentets sprickor eller berikning skapar dem. `"targetFieldName"`Är ett fält i ett index.

Förutom att koppla in indata till utdata kan du också använda fält mappningar för att förenkla data strukturer. Mer information finns i [så här mappar du berikade fält till ett sökbart index](cognitive-search-output-field-mapping.md).

Kör det här skriptet för att skapa en indexerare med namnet `cogsrch-py-indexer` .

```python
# Create an indexer
indexer_payload = {
    "name": indexer_name,
    "dataSourceName": datasource_name,
    "targetIndexName": index_name,
    "skillsetName": skillset_name,
    "fieldMappings": [
        {
            "sourceFieldName": "metadata_storage_path",
            "targetFieldName": "id",
            "mappingFunction":
            {"name": "base64Encode"}
        },
        {
            "sourceFieldName": "content",
            "targetFieldName": "content"
        }
    ],
    "outputFieldMappings":
    [
        {
            "sourceFieldName": "/document/organizations",
            "targetFieldName": "organizations"
        },
        {
            "sourceFieldName": "/document/pages/*/keyPhrases/*",
            "targetFieldName": "keyPhrases"
        },
        {
            "sourceFieldName": "/document/languageCode",
            "targetFieldName": "languageCode"
        }
    ],
    "parameters":
    {
        "maxFailedItems": -1,
        "maxFailedItemsPerBatch": -1,
        "configuration":
        {
            "dataToExtract": "contentAndMetadata",
            "imageAction": "generateNormalizedImages"
        }
    }
}

r = requests.put(endpoint + "/indexers/" + indexer_name,
                 data=json.dumps(indexer_payload), headers=headers, params=params)
print(r.status_code)
```

Begäran bör returnera status koden 201 snart, men bearbetningen kan ta flera minuter att slutföra. Även om data uppsättningen är liten, är analys kunskaper, till exempel bild analys, i beräknings intensiva och tar tid.

Du kan [övervaka indexerings status](#check-indexer-status) för att fastställa när indexeraren körs eller är färdig.

> [!TIP]
> När en indexerare skapas anropas pipelinen. Om det uppstår problem med att komma åt data, mappa indata och utdata eller med ordningen på åtgärder, visas det i det här skedet. Om du vill köra pipelinen igen med kod-eller skript ändringar kan du behöva ta bort objekten först. Mer information finns i [Reset and re-run](#reset) (Återställa och köra om).

#### <a name="about-the-request-body"></a>Om begär ande texten

Skriptet ställer in `"maxFailedItems"`  på -1, vilket instruerar indexeringsmotorn att ignorera fel under dataimport. Detta är användbart eftersom det finns det så få dokument i demo-datakällan. För en större datakälla skulle du ställa in värdet på större än 0.

Observera också `"dataToExtract":"contentAndMetadata"`-instruktionen i konfigurationsparametrarna. Den här instruktionen instruerar indexeraren att extrahera innehållet från olika fil format och de metadata som är relaterade till varje fil.

När innehållet har extraherats kan du ställa in `imageAction` på att extrahera text från avbildningar som hittades i datakällan. Konfigurationen av `"imageAction":"generateNormalizedImages"`, tillsammans med OCR-färdigheten och färdigheten för textsammanslagning, talar om för indexeraren att den ska extrahera text från bilderna (exempelvis ordet ”stopp” från en trafikstoppskylt) och bädda in den som en del av innehållsfältet. Det här beteendet gäller för båda de bilder som är inbäddade i dokumenten (Tänk på en bild i en PDF) och bilder som finns i data källan, t. ex. en JPG-fil.

<a name="check-indexer-status"></a>

## <a name="4---monitor-indexing"></a>4 – övervaka indexering

När du har definierat indexeraren körs den automatiskt när du skickar din begäran. Beroende på vilka kognitiva kunskaper du har definierat kan indexeringen ta längre tid än väntat. Kör följande skript för att ta reda på om bearbetningen av indexeraren är slutförd.

```python
# Get indexer status
r = requests.get(endpoint + "/indexers/" + indexer_name +
                 "/status", headers=headers, params=params)
pprint(json.dumps(r.json(), indent=1))
```

I svaret övervakar du `"lastResult"` för dess och- `"status"` `"endTime"` värden. Kör skriptet regelbundet för att kontrol lera statusen. När indexeraren har slutförts ändras statusen till "lyckades", en "slut tid" anges och svaret innehåller eventuella fel och varningar som uppstått under anrikningen.

:::image type="content" source="media/cognitive-search-tutorial-blob-python/py-indexer-is-created.png" alt-text="Indexeraren skapas" border="false":::

Varningar är vanliga med vissa källfils- och kunskapskombinationer och är inte alltid tecken på problem. Många varningar är ofarliga. Om du till exempel indexerar en JPEG-fil som saknar text visas varningen i den här skärm bilden.

:::image type="content" source="media/cognitive-search-tutorial-blob-python/py-indexer-warning-example.png" alt-text="Exempel på indexerings varning" border="false":::

## <a name="5---search"></a>5-Sök

När indexeringen är klar kör du frågor som returnerar innehållet i enskilda fält. Som standard returnerar Azure Kognitiv sökning de översta 50 resultaten. Exempeldata är små, så standardinställningen fungerar gott och väl. Men när du arbetar med större datamängder kanske du måste inkludera parametrar i frågesträngen för att returnera fler resultat. Anvisningar finns i [så här visar du sid resultat i Azure kognitiv sökning](search-pagination-page-layout.md).

Som ett verifierings steg hämtar du index definitionen som visar alla fält.

```python
# Query the service for the index definition
r = requests.get(endpoint + "/indexes/" + index_name,
                 headers=headers, params=params)
pprint(json.dumps(r.json(), indent=1))
```

Resultatet bör se ut som i följande exempel. Skärm bilden visar bara en del av svaret.

:::image type="content" source="media/cognitive-search-tutorial-blob-python/py-query-index-for-fields.png" alt-text="Fråga efter index för alla fält" border="false":::

Utdata är indexeringsschema med namn, typ och attribut för varje fält.

Skicka en ny fråga för `"*"` för att returnera hela innehållet i ett enda fält som `organizations`.

```python
# Query the index to return the contents of organizations
r = requests.get(endpoint + "/indexes/" + index_name +
                 "/docs?&search=*&$select=organizations", headers=headers, params=params)
pprint(json.dumps(r.json(), indent=1))
```

Resultatet bör se ut som i följande exempel. Skärm bilden visar bara en del av svaret.

:::image type="content" source="media/cognitive-search-tutorial-blob-python/py-query-index-for-organizations.png" alt-text="Fråga efter index för organisationers innehåll" border="false":::

Upprepa för ytterligare fält: `content` , `languageCode` , `keyPhrases` och `organizations` i den här övningen. Du kan returnera flera fält via `$select` med hjälp av en kommaavgränsad lista.

Du kan använda GET eller POST, beroende på frågesträngens komplexitet och längd. Mer information finns i [Query using the REST API](/rest/api/searchservice/search-documents) (Fråga med REST API).

<a name="reset"></a>

## <a name="reset-and-rerun"></a>Återställa och köra igen

I de tidiga experiment stegen i utvecklingen är den mest praktiska metoden för design upprepning att ta bort objekten från Azure Kognitiv sökning och tillåta att koden återskapas. Resursnamn är unika. Om du tar bort ett objekt kan du återskapa det med samma namn.

Du kan använda portalen för att ta bort index, indexerare, data källor och färdighetsuppsättningar. När du tar bort indexeraren kan du välja att selektivt ta bort index, färdigheter och data källan samtidigt.

:::image type="content" source="media/cognitive-search-tutorial-blob-python/py-delete-indexer-delete-all.png" alt-text="Ta bort Sök objekt i portalen" border="false":::

Du kan också ta bort dem med hjälp av ett skript. Följande skript visar hur du tar bort en färdigheter. 

```python
# delete the skillset
r = requests.delete(endpoint + "/skillsets/" + skillset_name,
                    headers=headers, params=params)
pprint(json.dumps(r.json(), indent=1))
```

Statuskod 204 returneras vid borttagning.

## <a name="takeaways"></a>Lärdomar

Den här självstudien visar de grundläggande stegen för att skapa en utökad indexeringspipeline genom att skapa komponentdelar: en datakälla, kunskapsuppsättning, index och indexerare.

[Inbyggda kunskaper](cognitive-search-predefined-skills.md) introducerades, tillsammans med färdigheter-definitioner och ett sätt att kedja samman färdigheter genom indata och utdata. Du har också lärt dig att `outputFieldMappings` i index definitions definitionen krävs för att dirigera berikade värden från pipelinen till ett sökbart index i en Azure kognitiv sökning-tjänst.

Slutligen har du lärt dig hur du testar resultaten och återställer systemet för ytterligare iterationer. Du har lärt dig att när du utfärdar frågor mot indexet returneras utdata som skapades av pipelinen för berikande indexering. I den här versionen finns det en mekanism för att visa interna konstruktioner (berikade dokument som skapats av systemet). Du har också lärt dig hur du kontrollerar indexerings status och vilka objekt som måste tas bort innan du kör en pipeline igen.

## <a name="clean-up-resources"></a>Rensa resurser

När du arbetar i din egen prenumeration är det en bra idé att ta bort de resurser som du inte längre behöver i slutet av projektet. Resurser som fortsätter att köras kostar pengar. Du kan ta bort resurser individuellt eller ta bort resursgruppen om du vill ta bort hela uppsättningen resurser.

Du kan hitta och hantera resurser i portalen med hjälp av länken alla resurser eller resurs grupper i det vänstra navigerings fönstret.

## <a name="next-steps"></a>Nästa steg

Nu när du är bekant med alla objekt i en pipeline för AI-anrikning, tar vi en närmare titt på färdigheter-definitioner och enskilda kunskaper.

> [!div class="nextstepaction"]
> [Så här skapar du en färdigheter](cognitive-search-defining-skillset.md)