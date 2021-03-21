---
title: 'Självstudie: REST och AI över Azure-blobbar'
titleSuffix: Azure Cognitive Search
description: 'Stega genom ett exempel på text extrahering och naturlig språk bearbetning över innehåll i blob-lagring med Postman och Azure Kognitiv sökning REST-API: er.'
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 11/17/2020
ms.openlocfilehash: 21f0d141567f17c470732088c6a93a2ae7ed3c67
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "94738058"
---
# <a name="tutorial-use-rest-and-ai-to-generate-searchable-content-from-azure-blobs"></a>Självstudie: Använd REST och AI för att generera sökbart innehåll från Azure-blobbar

Om du har ostrukturerad text eller avbildningar i Azure Blob Storage kan en [AI](cognitive-search-concept-intro.md) -utöknings pipeline extrahera information och skapa nytt innehåll från blobbar som är användbara för full texts ökning eller kunskaps utvinnings scenarier. Även om en pipeline kan bearbeta bilder, fokuserar denna REST-guide på text, använder språk identifiering och bearbetning av naturligt språk för att skapa nya fält som du kan använda i frågor, ansikts och filter.

I den här självstudien används Postman och [Sök REST-API: er](/rest/api/searchservice/) för att utföra följande uppgifter:

> [!div class="checklist"]
> * Konfigurera tjänster och en Postman-samling.
> * Skapa en pipeline för anrikning som extraherar text, identifierar språk, identifierar entiteter och identifierar viktiga fraser.
> * Skapa ett index för att lagra utdata (RAW-innehåll, plus pipeline-genererade namn-värdepar).
> * Kör pipelinen för att utföra omvandlingar och analyser och för att läsa in indexet.
> * Utforska resultat med fullständig texts ökning och en omfattande frågesyntax.

Om du inte har någon Azure-prenumeration kan du öppna ett [kostnads fritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="overview"></a>Översikt

I den här självstudien används C# och Azure Kognitiv sökning REST-API: er för att skapa en data källa, index, indexerare och färdigheter. Du börjar med hela dokument (ostrukturerad text) som PDF, HTML, DOCX och PPTX i Azure Blob Storage, och kör dem sedan via en färdigheter för att extrahera entiteter, nyckel fraser och annan text i innehållsfilerna.

I den här färdigheter används inbyggda kunskaper baserat på API:er för Cognitive Services. Stegen i pipelinen inkluderar språk identifiering på text, extrahering av nyckel fraser och enhets igenkänning (organisationer). Ny information lagras i nya fält som kan utnyttjas i frågor, ansikts och filter.

## <a name="prerequisites"></a>Förutsättningar

+ [Azure Storage](https://azure.microsoft.com/services/storage/)
+ [Skrivbordsappen Postman](https://www.getpostman.com/)
+ [Skapa](search-create-service-portal.md) eller [hitta en befintlig Sök tjänst](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) 

> [!Note]
> Du kan använda den kostnads fria tjänsten för den här självstudien. En kostnads fri Sök tjänst begränsar dig till tre index, tre indexerare och tre data källor. I den här kursen skapar du en av varje. Innan du börjar bör du kontrol lera att du har utrymme på tjänsten för att godkänna de nya resurserna.

## <a name="download-files"></a>Ladda ned filer

1. Öppna den här [OneDrive-mappen](https://1drv.ms/f/s!As7Oy81M_gVPa-LCb5lC_3hbS-4) och klicka på **Ladda ned** i det övre vänstra hörnet för att kopiera filerna till datorn. 

1. Högerklicka på zip-filen och välj **extrahera alla**. Det finns 14 filer av olika typer. Du använder 7 för den här övningen.

Alternativt kan du också ladda ned käll koden, en Postman-samlings fil, för den här självstudien. Du hittar käll koden på [https://github.com/Azure-Samples/azure-search-postman-samples/tree/master/Tutorial](https://github.com/Azure-Samples/azure-search-postman-samples/tree/master/Tutorial) .

## <a name="1---create-services"></a>1 – skapa tjänster

I den här självstudien används Azure Kognitiv sökning för indexering och frågor, Cognitive Services på Server delen för AI-anrikning och Azure Blob Storage för att tillhandahålla data. Den här självstudien finns kvar under den kostnads fria allokeringen av 20 transaktioner per indexerare per dag på Cognitive Services, så de enda tjänsterna du behöver skapa är Sök och lagring.

Skapa om möjligt både i samma region och resurs grupp för närhet och hanterbarhet. I praktiken kan ditt Azure Storage-konto finnas i vilken region som helst.

### <a name="start-with-azure-storage"></a>Börja med Azure Storage

1. [Logga](https://portal.azure.com/) in på Azure Portal och klicka på **+ skapa resurs**.

1. Sök efter *lagrings konto* och välj Microsofts erbjudande för lagrings konto.

   ![Skapa lagrings konto](media/cognitive-search-tutorial-blob/storage-account.png "Skapa lagrings konto")

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

   ![Ladda upp exempelfiler](media/cognitive-search-tutorial-blob/sample-files.png "Ladda upp exempelfiler")

1. Innan du lämnar Azure Storage får du en anslutnings sträng så att du kan formulera en anslutning i Azure Kognitiv sökning. 

   1. Gå tillbaka till sidan Översikt för ditt lagrings konto (vi använde *blobstragewestus* som exempel). 
   
   1. I det vänstra navigerings fönstret väljer du **åtkomst nycklar** och kopierar en av anslutnings strängarna. 

   Anslutnings strängen är en URL som liknar följande exempel:

      ```http
      DefaultEndpointsProtocol=https;AccountName=cogsrchdemostorage;AccountKey=<your account key>;EndpointSuffix=core.windows.net
      ```

1. Spara anslutnings strängen i anteckningar. Du behöver det senare när du konfigurerar anslutningen till data källan.

### <a name="cognitive-services"></a>Cognitive Services

AI-berikning backas upp av Cognitive Services, inklusive Textanalys och Visuellt innehåll för naturligt språk och bild bearbetning. Om målet var att slutföra en faktisk prototyp eller ett projekt, skulle du i den här punkten etablera Cognitive Services (i samma region som Azure Kognitiv sökning) så att du kan koppla den till indexerings åtgärder.

I den här övningen kan du hoppa över resurs etableringen eftersom Azure Kognitiv sökning kan ansluta till Cognitive Services bakom kulisserna och ge dig 20 kostnads fria transaktioner per indexerare. Eftersom den här självstudien använder 7 transaktioner är den kostnads fria fördelningen tillräckligt. För större projekt bör du planera för etablering Cognitive Services på S0-nivån betala per användning. Mer information finns i [bifoga Cognitive Services](cognitive-search-attach-cognitive-services.md).

### <a name="azure-cognitive-search"></a>Azure Cognitive Search

Den tredje komponenten är Azure-Kognitiv sökning, som du kan [skapa i portalen](search-create-service-portal.md). Du kan använda den kostnads fria nivån för att slutföra den här genom gången. 

Som med Azure Blob Storage kan du ägna en stund åt att samla in åtkomst nyckeln. Vidare måste du, när du börjar strukturera förfrågningar, ange slut punkt och Admin-API-nyckel som används för att autentisera varje begäran.

### <a name="copy-an-admin-api-key-and-url-for-azure-cognitive-search"></a>Kopiera en Admin API-nyckel och URL för Azure Kognitiv sökning

1. [Logga](https://portal.azure.com/)in på Azure Portal och hämta namnet på din Sök tjänst på sidan **Översikt över** Sök tjänsten. Du kan bekräfta tjänst namnet genom att granska slut punkts-URL: en. Om slut punkts-URL: en var `https://mydemo.search.windows.net` , är tjänstens namn `mydemo` .

2. I **Inställningar**  >  **nycklar**, hämtar du en administratörs nyckel för fullständiga rättigheter till tjänsten. Det finns två utbytbara administratörs nycklar, som tillhandahålls för affärs kontinuitet om du behöver rulla en över. Du kan använda antingen den primära eller sekundära nyckeln på begär Anden för att lägga till, ändra och ta bort objekt.

   Hämta även frågans nyckel. Det är en bra idé att utfärda förfrågningar med skrivskyddad åtkomst.

   ![Hämta tjänstens namn och administratör och fråge nycklar](media/search-get-started-javascript/service-name-and-keys.png)

Alla begär Anden kräver en API-nyckel i rubriken för varje begäran som skickas till din tjänst. En giltig nyckel upprättar förtroende per begäran mellan programmet som skickar begäran och tjänsten som hanterar den.

## <a name="2---set-up-postman"></a>2 – Konfigurera PostMan

Starta Postman och konfigurera en HTTP-begäran. Om du inte känner till det här verktyget kan du läsa mer i [utforska Azure KOGNITIV sökning REST-API: er](search-get-started-rest.md).

De metoder för begäran som används i den här självstudien är **post**, **placeras** och **hämtas**. Du använder metoderna för att göra fyra API-anrop till din Sök tjänst: skapa en data källa, en färdigheter, ett index och en indexerare.

I sidhuvud anger du "Content-Type" till `application/json` och anger `api-key` admin-API-nyckeln för din Azure kognitiv sökning-tjänst. När du har angett rubrikerna kan du använda dem för varje begäran i den här övningen.

  ![URL och rubrik för Postman-begäran](media/search-get-started-rest/postman-url.png "URL och rubrik för Postman-begäran")

## <a name="3---create-the-pipeline"></a>3 – skapa pipelinen

I Azure Kognitiv sökning sker berikning under indexering (eller data inmatning). I den här delen av genom gången skapas fyra objekt: data källa, index definition, färdigheter, indexerare. 

### <a name="step-1-create-a-data-source"></a>Steg 1: Skapa en datakälla

Ett [data käll objekt](/rest/api/searchservice/create-data-source) ger anslutnings strängen till BLOB-behållaren som innehåller filerna.

1. Använd **post** och följande URL och ersätt ditt-service-namn med det faktiska namnet på din tjänst.

   ```http
   https://[YOUR-SERVICE-NAME].search.windows.net/datasources?api-version=2020-06-30
   ```

1. I **brödtext** i begäran kopierar du följande JSON-definition och ersätter `connectionString` med den faktiska anslutningen av ditt lagrings konto. 

   Kom ihåg att även redigera behållar namnet. Vi föreslog "kugg hjuls-search-demo" för behållar namnet i ett tidigare steg.

    ```json
    {
      "name" : "cog-search-demo-ds",
      "description" : "Demo files to demonstrate cognitive search capabilities.",
      "type" : "azureblob",
      "credentials" :
      { "connectionString" :
        "DefaultEndpointsProtocol=https;AccountName=<YOUR-STORAGE-ACCOUNT>;AccountKey=<YOUR-ACCOUNT-KEY>;"
      },
      "container" : { "name" : "<YOUR-BLOB-CONTAINER-NAME>" }
    }
    ```
1. Skicka begäran. Du bör se status koden 201 som bekräftar att det är klart. 

Om du ser felet 403 eller 404 ska du kontrollera konstruktionen för begäran: `api-version=2020-06-30` ska vara på slutpunkten, `api-key` ska vara i rubriken efter `Content-Type` och dess värde måste vara giltigt för en söktjänst. Du kanske vill köra JSON-dokumentet via en online-JSON-verifierare för att kontrol lera att syntaxen är korrekt. 

### <a name="step-2-create-a-skillset"></a>Steg 2: skapa en färdigheter

Ett [färdigheter-objekt](/rest/api/searchservice/create-skillset) är en uppsättning av anriknings steg som tillämpas på ditt innehåll. 

1. Använd **Lägg** till och följande URL och ersätt ditt-service-namn med det faktiska namnet på din tjänst.

    ```http
    https://[YOUR-SERVICE-NAME].search.windows.net/skillsets/cog-search-demo-sd?api-version=2020-06-30
    ```

1. I begär ande **texten** kopierar du JSON-definitionen nedan. Den här färdigheter består av följande inbyggda kunskaper.

   | Kvalifikation                 | Beskrivning    |
   |-----------------------|----------------|
   | [Igenkänning av enhet](cognitive-search-skill-entity-recognition.md) | Extraherar namn på personer, organisationer och platser från innehållet i BLOB-behållaren. |
   | [Språkidentifiering](cognitive-search-skill-language-detection.md) | Identifierar innehållets språk. |
   | [Textuppdelning](cognitive-search-skill-textsplit.md)  | Delar upp stort innehåll i mindre segment innan nyckel fras extraherings kunskapen anropas. Extrahering av nyckelfraser accepterar indata på 50 000 tecken eller mindre. Några av exempelfilerna måste delas upp för att rymmas inom gränsen. |
   | [Extrahering av diskussionsämne](cognitive-search-skill-keyphrases.md) | Hämtar de viktigaste huvud fraserna. |

   Varje kunskap körs på innehållet i dokumentet. Under bearbetningen kommer Azure Kognitiv sökning att knäcka varje dokument för att läsa innehåll från olika fil format. Text som hittas från källfilen placeras i ett genererat ```content```-fält, ett för varje dokument. Det innebär att indatamängden blir ```"/document/content"``` .

   För extrahering av nyckel fraser, eftersom vi använder text delnings kunskapen för att dela upp större filer i sidor, är kontexten för extraherings kunskaper för nyckel fraser ```"document/pages/*"``` (för varje sida i dokumentet) i stället för ```"/document/content"``` .

    ```json
    {
      "description": "Extract entities, detect language and extract key-phrases",
      "skills":
      [
        {
          "@odata.type": "#Microsoft.Skills.Text.EntityRecognitionSkill",
          "categories": [ "Person", "Organization", "Location" ],
          "defaultLanguageCode": "en",
          "inputs": [
            { "name": "text", "source": "/document/content" }
          ],
          "outputs": [
            { "name": "persons", "targetName": "persons" },
            { "name": "organizations", "targetName": "organizations" },
            { "name": "locations", "targetName": "locations" }
          ]
        },
        {
          "@odata.type": "#Microsoft.Skills.Text.LanguageDetectionSkill",
          "inputs": [
            { "name": "text", "source": "/document/content" }
          ],
          "outputs": [
            { "name": "languageCode", "targetName": "languageCode" }
          ]
        },
        {
          "@odata.type": "#Microsoft.Skills.Text.SplitSkill",
          "textSplitMode" : "pages",
          "maximumPageLength": 4000,
          "inputs": [
            { "name": "text", "source": "/document/content" },
            { "name": "languageCode", "source": "/document/languageCode" }
          ],
          "outputs": [
            { "name": "textItems", "targetName": "pages" }
          ]
        },
        {
          "@odata.type": "#Microsoft.Skills.Text.KeyPhraseExtractionSkill",
          "context": "/document/pages/*",
          "inputs": [
            { "name": "text", "source": "/document/pages/*" },
            { "name":"languageCode", "source": "/document/languageCode" }
          ],
          "outputs": [
            { "name": "keyPhrases", "targetName": "keyPhrases" }
          ]
        }
      ]
    }
    ```
    En grafisk representation av kunskapsuppsättningen visas nedan. 

    ![Förstå en färdigheter](media/cognitive-search-tutorial-blob/skillset.png "Förstå en färdigheter")

1. Skicka begäran. Postman ska returnera status koden 201 som bekräftar att det lyckades. 

> [!NOTE]
> Utdata kan mappas till ett index som används som indata till en underordnad kunskap, eller både, vilket är fallet med språkkod. I indexet kan en språkkod användas för filtrering. Som indata används språkkoden av textanalyskunskaper för att informera om de språkliga reglerna kring ordnedbrytning. Mer information om grunderna i kunskapsuppsättningar finns i [Definiera en kunskapsuppsättning](cognitive-search-defining-skillset.md).

### <a name="step-3-create-an-index"></a>Steg 3: skapa ett index

Ett [index](/rest/api/searchservice/create-index) ger det schema som används för att skapa det fysiska uttrycket för ditt innehåll i inverterade index och andra konstruktioner i Azure kognitiv sökning. Den största komponenten i ett index är fält samlingen där data typen och attributen avgör innehåll och beteenden i Azure Kognitiv sökning.

1. Använd **Lägg** till och följande URL och ersätt ditt-service-namn med det faktiska namnet på din tjänst, för att namnge ditt index.

   ```http
   https://[YOUR-SERVICE-NAME].search.windows.net/indexes/cog-search-demo-idx?api-version=2020-06-30
   ```

1. I **brödtext** i begäran kopierar du följande JSON-definition. I `content` fältet lagras själva dokumentet. Ytterligare fält för `languageCode` , `keyPhrases` och `organizations` representerar ny information (fält och värden) som skapats av färdigheter.

    ```json
    {
      "fields": [
        {
          "name": "id",
          "type": "Edm.String",
          "key": true,
          "searchable": true,
          "filterable": false,
          "facetable": false,
          "sortable": true
        },
        {
          "name": "metadata_storage_name",
          "type": "Edm.String",
          "searchable": false,
          "filterable": false,
          "facetable": false,
          "sortable": false
        },
        {
          "name": "content",
          "type": "Edm.String",
          "sortable": false,
          "searchable": true,
          "filterable": false,
          "facetable": false
        },
        {
          "name": "languageCode",
          "type": "Edm.String",
          "searchable": true,
          "filterable": false,
          "facetable": false
        },
        {
          "name": "keyPhrases",
          "type": "Collection(Edm.String)",
          "searchable": true,
          "filterable": false,
          "facetable": false
        },
        {
          "name": "persons",
          "type": "Collection(Edm.String)",
          "searchable": true,
          "sortable": false,
          "filterable": true,
          "facetable": true
        },
        {
          "name": "organizations",
          "type": "Collection(Edm.String)",
          "searchable": true,
          "sortable": false,
          "filterable": true,
          "facetable": true
        },
        {
          "name": "locations",
          "type": "Collection(Edm.String)",
          "searchable": true,
          "sortable": false,
          "filterable": true,
          "facetable": true
        }
      ]
    }
    ```

1. Skicka begäran. Postman ska returnera status koden 201 som bekräftar att det lyckades. 

### <a name="step-4-create-and-run-an-indexer"></a>Steg 4: skapa och köra en indexerare

En [indexerare](/rest/api/searchservice/create-indexer) driver pipelinen. De tre komponenter som du har skapat hittills (data källa, färdigheter, index) är indata till en indexerare. Att skapa indexeraren på Azure Kognitiv sökning är den händelse som placerar hela pipelinen i rörelse. 

1. Använd **Lägg** till och följande URL och ersätt ditt-service-namn med det faktiska namnet på din tjänst, för att ge indexeraren ett namn.

   ```http
   https://[servicename].search.windows.net/indexers/cog-search-demo-idxr?api-version=2020-06-30
   ```

1. I begär ande **texten** kopierar du JSON-definitionen nedan. Observera fält mappnings elementen. dessa mappningar är viktiga eftersom de definierar data flödet. 

   `fieldMappings`Bearbetas före färdigheter, vilket skickar innehåll från data källan till mål fälten i ett index. Du använder fält mappningar för att skicka befintligt, oförändrat innehåll till indexet. Om fält namn och typer är desamma i båda ändar, krävs ingen mappning.

   `outputFieldMappings`Används för fält som skapats av färdigheter och bearbetas därför när färdigheter har körts. Referenser till `sourceFieldNames` i `outputFieldMappings` finns inte förrän dokuments cracking eller berikning skapar dem. `targetFieldName`Är ett fält i ett index som definieras i index schemat.

    ```json
    {
      "name":"cog-search-demo-idxr",
      "dataSourceName" : "cog-search-demo-ds",
      "targetIndexName" : "cog-search-demo-idx",
      "skillsetName" : "cog-search-demo-ss",
      "fieldMappings" : [
        {
          "sourceFieldName" : "metadata_storage_path",
          "targetFieldName" : "id",
          "mappingFunction" :
            { "name" : "base64Encode" }
        },
        {
          "sourceFieldName" : "metadata_storage_name",
          "targetFieldName" : "metadata_storage_name",
          "mappingFunction" :
            { "name" : "base64Encode" }
        },
        {
          "sourceFieldName" : "content",
          "targetFieldName" : "content"
        }
      ],
      "outputFieldMappings" :
      [
        {
          "sourceFieldName" : "/document/persons",
          "targetFieldName" : "persons"
        },
        {
          "sourceFieldName" : "/document/organizations",
          "targetFieldName" : "organizations"
        },
        {
          "sourceFieldName" : "/document/locations",
          "targetFieldName" : "locations"
        },
        {
          "sourceFieldName" : "/document/pages/*/keyPhrases/*",
          "targetFieldName" : "keyPhrases"
        },
        {
          "sourceFieldName": "/document/languageCode",
          "targetFieldName": "languageCode"
        }
      ],
      "parameters":
      {
        "maxFailedItems":-1,
        "maxFailedItemsPerBatch":-1,
        "configuration":
        {
          "dataToExtract": "contentAndMetadata",
          "parsingMode": "default",
          "firstLineContainsHeaders": false,
          "delimitedTextDelimiter": ","
        }
      }
    }
    ```

1. Skicka begäran. Postman ska returnera status koden 201 för att bekräfta att bearbetningen lyckades. 

   Förvänta dig att steget kan ta flera minuter att slutföra. Trots att datauppsättningen är liten är analytiska kunskaper beräkningsintensiva. 

> [!NOTE]
> När en indexerare skapas anropas pipelinen. Om det uppstår problem med att ansluta till data, mappningsindata eller -utdata eller ordningen på åtgärder visas dem i det här stadiet. Du kan behöva ta bort objekt först om du vill köra pipelinen med kod- eller skriptändringar. Mer information finns i [Reset and re-run](#reset) (Återställa och köra om).

#### <a name="about-indexer-parameters"></a>Om indexerings parametrar

Skriptet ställer in ```"maxFailedItems"```  på -1, vilket instruerar indexeringsmotorn att ignorera fel under dataimport. Detta är acceptabelt eftersom det finns några dokument i demonstrations data källan. För en större datakälla skulle du ställa in värdet på större än 0.

```"dataToExtract":"contentAndMetadata"```Instruktionen instruerar indexeraren att automatiskt extrahera innehållet från olika fil format samt metadata som är relaterade till varje fil. 

När innehållet har extraherats kan du ställa in ```imageAction``` på att extrahera text från avbildningar som hittades i datakällan. Konfigurationen av ```"imageAction":"generateNormalizedImages"```, tillsammans med OCR-färdigheten och färdigheten för textsammanslagning, talar om för indexeraren att den ska extrahera text från bilderna (exempelvis ordet ”stopp” från en trafikstoppskylt) och bädda in den som en del av innehållsfältet. Det här beteendet gäller både avbildningarna som är inbäddade i dokumenten (tänk på en avbildning i en PDF) samt avbildningar som hittas i datakällan, till exempel en JPG-fil.

## <a name="4---monitor-indexing"></a>4 – övervaka indexering

Indexering och anrikning börjar så snart du skickar en begäran om att skapa indexerare. Indexering kan ta en stund beroende på vilka kognitiva kunskaper du definierar. Skicka följande begäran för att kontrollera indexerarens status om du vill ta reda på om indexeraren fortfarande körs.

1. Använd **Get** och följande URL och ersätt ditt-service-namn med det faktiska namnet på din tjänst, för att ge indexeraren ett namn.

   ```http
   https://[YOUR-SERVICE-NAME].search.windows.net/indexers/cog-search-demo-idxr/status?api-version=2020-06-30
   ```

1. Granska svaret för att lära dig om indexeraren körs, eller om du vill visa information om fel och varningar.  

Om du använder den kostnads fria nivån förväntas följande meddelande: "" Det gick inte att extrahera innehåll eller metadata från dokumentet. Trunkerad extraherad text till "32768" tecken ". Det här meddelandet visas eftersom BLOB-indexering på den kostnads fria nivån har en[32k-gräns för tecken extrahering](search-limits-quotas-capacity.md#indexer-limits). Det här meddelandet visas inte för den här data uppsättningen på högre nivåer. 

> [!NOTE]
> Varningar är vanliga i vissa situationer och indikerar inte alltid ett problem. Om en BLOB-behållare t. ex. innehåller bildfiler och pipelinen inte hanterar bilder, får du en varning om att bilderna inte har bearbetats.

## <a name="5---search"></a>5-Sök

Nu när du har skapat nya fält och information kan vi köra några frågor för att förstå värdet för kognitiv sökning, som relaterar till ett typiskt Sök scenario.

Kom ihåg att vi startade med BLOB-innehåll, där hela dokumentet paketeras i ett enda `content` fält. Du kan söka i det här fältet och hitta matchningar för dina frågor.

1. Använd **Get** och följande URL och ersätt ditt-service-namn med det faktiska namnet på din tjänst, för att söka efter instanser av en term eller fras, returnerar `content` fältet och antalet matchande dokument.

   ```http
   https://[YOUR-SERVICE-NAME].search.windows.net/indexes/cog-search-demo-idx/docs?search=*&$count=true&$select=content&api-version=2020-06-30
   ```
   
   Resultatet av den här frågan returnerar dokument innehåll, vilket är samma resultat som du får om du använder BLOB-indexeraren utan den kognitiva Sök pipelinen. Det här fältet är sökbart, men kan inte användas om du vill använda ansikte, filter eller Autoavsluta.

   ![Utdata för innehålls fält](media/cognitive-search-tutorial-blob/content-output.png "Utdata för innehålls fält")
   
1. Returnera några av de nya fälten som skapats av pipelinen (personer, organisationer, platser, languageCode) för den andra frågan. Vi utesluter det kortfattat, men du bör ta med det om du vill se dessa värden.

   ```http
   https://[YOUR-SERVICE-NAME].search.windows.net/indexes/cog-search-demo-idx/docs?search=*&$count=true&$select=metadata_storage_name,persons,organizations,locations,languageCode&api-version=2020-06-30
   ```
   Fälten i $select-instruktionen innehåller ny information som skapats från den naturliga språk bearbetnings funktionen i Cognitive Services. Som du kan förväntar dig finns det störningar i resultatet och variationen i dokumenten, men i många fall ger analys modellerna korrekta resultat.

   Följande bild visar resultat för Satya Nadellas öppna brev vid antagande av VD-rollen hos Microsoft.

   ![Pipeline-utdata](media/cognitive-search-tutorial-blob/pipeline-output.png "Pipeline-utdata")

1. Om du vill se hur du kan dra nytta av dessa fält lägger du till en aspekt parameter för att returnera en agg regering av matchande dokument efter plats.

   ```http
   https://[YOUR-SERVICE-NAME].search.windows.net/indexes/cog-search-demo-idx/docs?search=*&facet=locations&api-version=2020-06-30
   ``` 

   I det här exemplet finns det två eller tre matchningar för varje plats.

   ![Fasett-utdata](media/cognitive-search-tutorial-blob/facet-output.png "Fasett-utdata")
   

1. I det här sista exemplet ska du använda ett filter för organisations samlingen och returnera två matchningar för filter villkor baserat på NASDAQ.

   ```http
   https://[YOUR-SERVICE-NAME].search.windows.net/indexes/cog-search-demo-idx/docs?search=*&$filter=organizations/any(organizations: organizations eq 'NASDAQ')&$select=metadata_storage_name,organizations&$count=true&api-version=2020-06-30
   ```

Dessa frågor illustrerar några av de olika sätten att arbeta med frågesyntax och filter på nya fält som skapats av kognitiv sökning. Fler fråge exempel finns i [exemplen i search documents REST API](/rest/api/searchservice/search-documents#bkmk_examples), [Simple syntax Query exempel](search-query-simple-examples.md)och [fullständiga Lucene-fråge exempel](search-query-lucene-examples.md).

<a name="reset"></a>

## <a name="reset-and-rerun"></a>Återställa och köra igen

I de tidiga experiment stegen i utvecklingen är den mest praktiska metoden för design upprepning att ta bort objekten från Azure Kognitiv sökning och tillåta att koden återskapas. Resursnamn är unika. Om du tar bort ett objekt kan du återskapa det med samma namn.

Du kan använda portalen för att ta bort index, indexerare, data källor och färdighetsuppsättningar. När du tar bort indexeraren kan du välja att selektivt ta bort index, färdigheter och data källan samtidigt.

![Ta bort Sök objekt](./media/cognitive-search-tutorial-blob-python/py-delete-indexer-delete-all.png "Ta bort Sök objekt i portalen")

Eller Använd **ta bort** och ange URL: er för varje objekt. Följande kommando tar bort en indexerare.

```http
DELETE https://[YOUR-SERVICE-NAME].search.windows.net/indexers/cog-search-demo-idxr?api-version=2020-06-30
```

Statuskod 204 returneras vid borttagning.

## <a name="takeaways"></a>Lärdomar

Den här självstudien visar de grundläggande stegen för att skapa en utökad indexeringspipeline genom att skapa komponentdelar: en datakälla, kunskapsuppsättning, index och indexerare.

[Inbyggda kunskaper](cognitive-search-predefined-skills.md) introducerades, tillsammans med färdigheter-definitionen och Mechanics för länkning av färdigheter tillsammans genom indata och utdata. Du har också lärt dig att `outputFieldMappings` i index definitions definitionen krävs för att dirigera berikade värden från pipelinen till ett sökbart index i en Azure kognitiv sökning-tjänst.

Slutligen lärde du dig att testa resultat och återställa systemet för ytterligare iterationer. Du har lärt dig att när du utfärdar frågor mot indexet returneras utdata som skapades av pipelinen för berikande indexering. 

## <a name="clean-up-resources"></a>Rensa resurser

När du arbetar i din egen prenumeration är det en bra idé att ta bort de resurser som du inte längre behöver i slutet av projektet. Resurser som fortsätter att köras kostar pengar. Du kan ta bort resurser individuellt eller ta bort resursgruppen om du vill ta bort hela uppsättningen resurser.

Du kan hitta och hantera resurser i portalen med hjälp av länken alla resurser eller resurs grupper i det vänstra navigerings fönstret.

## <a name="next-steps"></a>Nästa steg

Nu när du är bekant med alla objekt i en pipeline för AI-anrikning, tar vi en närmare titt på färdigheter-definitioner och enskilda kunskaper.

> [!div class="nextstepaction"]
> [Så här skapar du en färdigheter](cognitive-search-defining-skillset.md)
