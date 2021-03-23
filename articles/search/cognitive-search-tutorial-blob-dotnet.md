---
title: C#-självstudie med AI på Azure-blobbar
titleSuffix: Azure Cognitive Search
description: Stega genom ett exempel på text extrahering och naturlig språk bearbetning över innehåll i blob-lagring med C# och Azure Kognitiv sökning .NET SDK.
manager: nitinme
author: MarkHeff
ms.author: maheff
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 01/23/2021
ms.custom: devx-track-csharp
ms.openlocfilehash: 6cbfa01788f1897264b4197798aa5879cf724db3
ms.sourcegitcommit: 2c1b93301174fccea00798df08e08872f53f669c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/22/2021
ms.locfileid: "104770962"
---
# <a name="tutorial-use-net-and-ai-to-generate-searchable-content-from-azure-blobs"></a>Självstudie: använda .NET och AI för att generera sökbart innehåll från Azure-blobbar

Om du har ostrukturerad text eller avbildningar i Azure Blob Storage kan en [AI-pipeline](cognitive-search-concept-intro.md) utvinna information och skapa nytt innehåll för full texts ökning eller kunskaps utvinnings scenarier. 

I den här självstudien får du lära dig hur man:

> [!div class="checklist"]
> * Konfigurera en utvecklings miljö
> * Definiera en pipeline som använder OCR, språk identifiering och enhets-och nyckel fras igenkänning.
> * Kör pipelinen för att anropa transformationer och för att skapa och läsa in ett sökindex.
> * Utforska resultat med fullständig texts ökning och en omfattande frågesyntax.

Om du inte har någon Azure-prenumeration kan du öppna ett [kostnads fritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="overview"></a>Översikt

I den här självstudien används C# och [ **Azure.Search.Documents** -klient biblioteket](/dotnet/api/overview/azure/search.documents-readme) för att skapa en data källa, index, indexerare och färdigheter.

Indexeraren ansluter till en BLOB-behållare som anges i objektet data källa och skickar allt indexerat innehåll till ett befintligt sökindex.

Färdigheter är kopplad till indexeraren. Den använder inbyggda kunskaper från Microsoft för att hitta och extrahera information. Stegen i pipelinen inkluderar optisk tecken läsning (OCR) på bilder, språk identifiering på text, extrahering av nyckel fraser och enhets igenkänning (organisationer). Ny information som skapas av pipelinen lagras i nya fält i ett index. När indexet har fyllts kan du använda fälten i frågor, ansikts och filter.

## <a name="prerequisites"></a>Förutsättningar

* [Visual Studio](https://visualstudio.microsoft.com/downloads/)
* [Azure.Search.Documents 11. x NuGet-paket](https://www.nuget.org/packages/Azure.Search.Documents) 
* [Azure Storage](https://azure.microsoft.com/services/storage/)
* [Azure Cognitive Search](https://azure.microsoft.com/services/search/)

> [!Note]
> Du kan använda den kostnads fria Sök tjänsten för den här självstudien. En kostnads fri Sök tjänst begränsar dig till tre index, tre indexerare och tre data källor. I den här kursen skapar du en av varje. Innan du börjar bör du kontrol lera att du har utrymme på tjänsten för att godkänna de nya resurserna.

## <a name="download-sample-data"></a>Hämta exempeldata

Exempel data består av 14 filer av blandad innehålls typ som du kommer att överföra till Azure Blob Storage i ett senare steg.

1. Öppna den här [OneDrive-mappen](https://1drv.ms/f/s!As7Oy81M_gVPa-LCb5lC_3hbS-4) och klicka på **Ladda ned** i det övre vänstra hörnet för att kopiera filerna till datorn. 

1. Högerklicka på zip-filen och välj **extrahera alla**. Det finns 14 filer av olika typer. Du använder 7 för den här övningen.

Du kan också ladda ned käll koden för den här självstudien. Käll koden finns i **självstudien – AI-anrikning/V11** -mappen i [Azure-Search-dotNet-samples](https://github.com/Azure-Samples/azure-search-dotnet-samples) -lagringsplatsen.

## <a name="1---create-services"></a>1 – skapa tjänster

I den här självstudien används Azure Kognitiv sökning för indexering och frågor, Cognitive Services på Server delen för AI-anrikning och Azure Blob Storage för att tillhandahålla data. Den här självstudien finns kvar under den kostnads fria allokeringen av 20 transaktioner per indexerare per dag på Cognitive Services, så de enda tjänsterna du behöver skapa är Sök och lagring.

Skapa om möjligt både i samma region och resurs grupp för närhet och hanterbarhet. I praktiken kan ditt Azure Storage-konto finnas i vilken region som helst.

### <a name="start-with-azure-storage"></a>Börja med Azure Storage

1. [Logga](https://portal.azure.com/) in på Azure Portal och klicka på **+ skapa resurs**.

1. Sök efter *lagrings konto* och välj Microsofts erbjudande för lagrings konto.

   ![Skapa lagrings konto](media/cognitive-search-tutorial-blob/storage-account.png "Skapa lagrings konto")

1. På fliken grundläggande måste följande objekt vara obligatoriska. Acceptera standardvärdena för allt annat.

   * **Resursgrupp**. Välj en befintlig eller skapa en ny, men Använd samma grupp för alla tjänster så att du kan hantera dem tillsammans.

   * **Namn på lagringskonto**. Om du tror att du kan ha flera resurser av samma typ, använder du namnet på disambiguate efter typ och region, till exempel *blobstoragewestus*. 

   * **Plats**. Om möjligt väljer du samma plats som används för Azure Kognitiv sökning och Cognitive Services. Med en enda plats annulleras bandbredds avgifter.

   * **Typ av konto**. Välj standard, *StorageV2 (generell användning v2)*.

1. Klicka på **Granska + skapa** för att skapa tjänsten.

1. När den har skapats klickar **du på gå till resursen** för att öppna översikts sidan.

1. Klicka på **blobs** -tjänsten.

1. Klicka på **+ container** för att skapa en behållare och ge den namnet *kugg hjuls-search-demo*.

1. Välj *kugg hjuls-search-demo* och klicka sedan på **Ladda upp** för att öppna mappen där du sparade nedladdnings filerna. Välj alla fjorton filer och klicka på **OK** för att ladda upp.

   ![Ladda upp exempelfiler](media/cognitive-search-quickstart-blob/sample-data.png "Ladda upp exempelfiler")

1. Innan du lämnar Azure Storage får du en anslutnings sträng så att du kan formulera en anslutning i Azure Kognitiv sökning. 

   1. Gå tillbaka till sidan Översikt för ditt lagrings konto (vi använde *blobstoragewestus* som exempel). 

   1. I det vänstra navigerings fönstret väljer du **åtkomst nycklar** och kopierar en av anslutnings strängarna. 

   Anslutnings strängen är en URL som liknar följande exempel:

      ```http
      DefaultEndpointsProtocol=https;AccountName=blobstoragewestus;AccountKey=<your account key>;EndpointSuffix=core.windows.net
      ```

1. Spara anslutnings strängen i anteckningar. Du behöver det senare när du konfigurerar anslutningen till data källan.

### <a name="cognitive-services"></a>Cognitive Services

AI-berikning backas upp av Cognitive Services, inklusive Textanalys och Visuellt innehåll för naturligt språk och bild bearbetning. Om målet var att slutföra en faktisk prototyp eller ett projekt, skulle du i den här punkten etablera Cognitive Services (i samma region som Azure Kognitiv sökning) så att du kan koppla den till indexerings åtgärder.

I den här övningen kan du hoppa över resurs etableringen eftersom Azure Kognitiv sökning kan ansluta till Cognitive Services bakom kulisserna och ge dig 20 kostnads fria transaktioner per indexerare. Eftersom den här självstudien använder 14 transaktioner är den kostnads fria fördelningen tillräckligt. För större projekt bör du planera för etablering Cognitive Services på S0-nivån betala per användning. Mer information finns i [bifoga Cognitive Services](cognitive-search-attach-cognitive-services.md).

### <a name="azure-cognitive-search"></a>Azure Cognitive Search

Den tredje komponenten är Azure Kognitiv sökning, som du kan [skapa i portalen](search-create-service-portal.md) eller [hitta en befintlig Sök tjänst](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) i din prenumeration.

Du kan använda den kostnads fria nivån för att slutföra den här genom gången. 

### <a name="copy-an-admin-api-key-and-url-for-azure-cognitive-search"></a>Kopiera en Admin API-nyckel och URL för Azure Kognitiv sökning

Om du vill interagera med din Azure Kognitiv sökning-tjänst behöver du tjänst-URL: en och en åtkomst nyckel. En Sök tjänst skapas med båda, så om du har lagt till Azure-Kognitiv sökning till din prenumeration följer du dessa steg för att få den information som krävs:

1. [Logga](https://portal.azure.com/)in på Azure Portal och hämta URL: en på sidan **Översikt över** Sök tjänsten. Här följer ett exempel på hur en slutpunkt kan se ut: `https://mydemo.search.windows.net`.

1. I **Inställningar**  >  **nycklar**, kopierar du en administratörs nyckel för fullständiga rättigheter till tjänsten. Det finns två utbytbara administratörs nycklar, som tillhandahålls för affärs kontinuitet om du behöver rulla en över. Du kan använda antingen den primära eller sekundära nyckeln på begär Anden för att lägga till, ändra och ta bort objekt.

   Hämta även frågans nyckel. Det är en bra idé att utfärda förfrågningar med skrivskyddad åtkomst.

   ![Hämta tjänstens namn och administratör och fråge nycklar](media/search-get-started-javascript/service-name-and-keys.png)

En giltig nyckel upprättar förtroende, i varje begäran, mellan programmet som skickar begäran och tjänsten som hanterar den.

## <a name="2---set-up-your-environment"></a>2 – Konfigurera din miljö

Börja med att öppna Visual Studio och skapa ett nytt konsol program som kan köras på .NET Core.

### <a name="install-azuresearchdocuments"></a>Installera Azure.Search.Documents

[Azure kognitiv sökning .NET SDK](/dotnet/api/overview/azure/search) består av ett klient bibliotek som gör att du kan hantera dina index, data källor, indexerare och färdighetsuppsättningar, samt överföra och hantera dokument och köra frågor, allt utan att du behöver hantera informationen om http och JSON. Det här klient biblioteket distribueras som ett NuGet-paket.

För det här projektet installerar du version 11 eller senare av `Azure.Search.Documents` och den senaste versionen av `Microsoft.Extensions.Configuration` .

1. I Visual Studio väljer du **verktyg**  >  **NuGet Package Manager**  >  **Hantera NuGet-paket för lösning...**

1. Bläddra efter [Azure.Search.Document](https://www.nuget.org/packages/Azure.Search.Documents).

1. Välj den senaste versionen och klicka sedan på **Installera**.

1. Upprepa föregående steg för att installera [Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration) och [Microsoft.Extensions.Configuration.Jspå](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.Json).

### <a name="add-service-connection-information"></a>Lägg till information om tjänst anslutning

1. Högerklicka på ditt projekt i Solution Explorer och välj **Lägg till**  >  **nytt objekt.** ... 

1. Ge filen ett namn `appsettings.json` och välj **Lägg till**. 

1. Ta med den här filen i din utmatnings katalog.
    1. Högerklicka på `appsettings.json` och välj **Egenskaper**. 
    1. Ändra värdet för **Kopiera till utdatakatalogen** för att **Kopiera om** det är nyare.

1. Kopiera nedanstående JSON till din nya JSON-fil.

    ```json
    {
      "SearchServiceUri": "Put your search service URI here",
      "SearchServiceAdminApiKey": "Put your primary or secondary API key here",
      "SearchServiceQueryApiKey": "Put your query API key here",
      "AzureBlobConnectionString": "Put your Azure Blob connection string here",
    }
    ```

Lägg till din Sök tjänst och information om Blob Storage-kontot. Kom ihåg att du kan hämta den här informationen från tjänst etablerings stegen som anges i föregående avsnitt.

Ange den fullständiga URL: en för **SearchServiceUri**.

### <a name="add-namespaces"></a>Lägg till namn områden

I `Program.cs` lägger du till följande namn rymder.

```csharp
using Azure;
using Azure.Search.Documents.Indexes;
using Azure.Search.Documents.Indexes.Models;
using Microsoft.Extensions.Configuration;
using System;
using System.Collections.Generic;
using System.Linq;

namespace EnrichwithAI
```

### <a name="create-a-client"></a>Skapa en klient

Skapa en instans av en `SearchIndexClient` och en `SearchIndexerClient` under `Main` .

```csharp
public static void Main(string[] args)
{
    // Create service client
    IConfigurationBuilder builder = new ConfigurationBuilder().AddJsonFile("appsettings.json");
    IConfigurationRoot configuration = builder.Build();

    string searchServiceUri = configuration["SearchServiceUri"];
    string adminApiKey = configuration["SearchServiceAdminApiKey"];
    string cognitiveServicesKey = configuration["CognitiveServicesKey"];

    SearchIndexClient indexClient = new SearchIndexClient(new Uri(searchServiceUri), new AzureKeyCredential(adminApiKey));
    SearchIndexerClient indexerClient = new SearchIndexerClient(new Uri(searchServiceUri), new AzureKeyCredential(adminApiKey));
}
```

> [!NOTE]
> Klienterna ansluter till din Sök tjänst. För att undvika att öppna för många anslutningar bör du försöka att dela en enda instans i programmet om det är möjligt. Metoderna är tråd säkra för att aktivera sådan delning.
> 

### <a name="add-function-to-exit-the-program-during-failure"></a>Lägg till funktion för att avsluta programmet under ett haveri läge

Den här självstudien är avsedd att hjälpa dig att förstå varje steg i indexerings pipelinen. Om det finns ett allvarligt problem som hindrar programmet från att skapa data källan, färdigheter, index eller indexerare, kommer programmet att generera fel meddelandet och avsluta så att problemet kan tolkas och åtgärdas.

Lägg till i `ExitProgram` `Main` för att hantera scenarier som kräver att programmet avslutas.

```csharp
private static void ExitProgram(string message)
{
    Console.WriteLine("{0}", message);
    Console.WriteLine("Press any key to exit the program...");
    Console.ReadKey();
    Environment.Exit(0);
}
```

## <a name="3---create-the-pipeline"></a>3 – skapa pipelinen

I Azure Kognitiv sökning sker AI-bearbetning under indexering (eller data inmatning). I den här delen av genom gången skapas fyra objekt: data källa, index definition, färdigheter, indexerare. 

### <a name="step-1-create-a-data-source"></a>Steg 1: Skapa en datakälla

`SearchIndexerClient` har en [`DataSourceName`](/dotnet/api/azure.search.documents.indexes.models.searchindexer.datasourcename) egenskap som du kan ange till ett `SearchIndexerDataSourceConnection` objekt. Det här objektet innehåller alla metoder som du behöver för att skapa, Visa, uppdatera eller ta bort Azure Kognitiv sökning data källor.

Skapa en ny `SearchIndexerDataSourceConnection` instans genom att anropa `indexerClient.CreateOrUpdateDataSourceConnection(dataSource)` . Följande kod skapar en data källa av typen `AzureBlob` .

```csharp
private static SearchIndexerDataSourceConnection CreateOrUpdateDataSource(SearchIndexerClient indexerClient, IConfigurationRoot configuration)
{
    SearchIndexerDataSourceConnection dataSource = new SearchIndexerDataSourceConnection(
        name: "demodata",
        type: SearchIndexerDataSourceType.AzureBlob,
        connectionString: configuration["AzureBlobConnectionString"],
        container: new SearchIndexerDataContainer("cog-search-demo"))
    {
        Description = "Demo files to demonstrate cognitive search capabilities."
    };

    // The data source does not need to be deleted if it was already created
    // since we are using the CreateOrUpdate method
    try
    {
        indexerClient.CreateOrUpdateDataSourceConnection(dataSource);
    }
    catch (Exception ex)
    {
        Console.WriteLine("Failed to create or update the data source\n Exception message: {0}\n", ex.Message);
        ExitProgram("Cannot continue without a data source");
    }

    return dataSource;
}
```

För att begäran ska lyckas returnerar metoden den data källa som skapades. Om det uppstår ett problem med begäran, till exempel en ogiltig parameter, kommer metoden att utlösa ett undantag.

Nu kan du lägga till en rad i `Main` för att anropa den `CreateOrUpdateDataSource` funktion som du just har lagt till.

```csharp
// Create or Update the data source
Console.WriteLine("Creating or updating the data source...");
SearchIndexerDataSourceConnection dataSource = CreateOrUpdateDataSource(indexerClient, configuration);
```

Skapa och kör lösningen. Eftersom det här är din första förfrågan kontrollerar du Azure Portal för att bekräfta att data källan har skapats i Azure Kognitiv sökning. På sidan Search Service-Översikt kontrollerar du att listan med data källor har ett nytt objekt. Du kan behöva vänta några minuter medan portalsidan uppdateras.

  ![Panelen data källor i portalen](./media/cognitive-search-tutorial-blob/data-source-tile.png "Panelen data källor i portalen")

### <a name="step-2-create-a-skillset"></a>Steg 2: skapa en färdigheter

I det här avsnittet definierar du en uppsättning med anriknings steg som du vill använda för dina data. Varje anriknings steg kallas en *färdighet* och en uppsättning av anriknings steg, en *färdigheter*. I den här självstudien används [inbyggda kognitiva kunskaper](cognitive-search-predefined-skills.md) för färdigheter:

* [Optisk tecken läsning](cognitive-search-skill-ocr.md) för att identifiera skriven och handskriven text i bildfiler.

* [Text sammanslagning](cognitive-search-skill-textmerger.md) för att konsolidera text från en samling fält till ett enda fält.

* [Språkidentifiering](cognitive-search-skill-language-detection.md) för att identifiera innehållets språk.

* [Text delning](cognitive-search-skill-textsplit.md) för att dela upp stor mängd innehåll i mindre segment innan du anropar nyckel frasens extraherings kunskap och enhets igenkännings kompetensen. Extrahering av nyckel fraser och entitets igenkänning accepterar indata på högst 50 000 tecken. Några av exempelfilerna måste delas upp för att rymmas inom gränsen.

* [Enhets igenkänning](cognitive-search-skill-entity-recognition.md) för extrahering av namn på organisationer från innehåll i BLOB-behållaren.

* [Extrahering av nyckelfraser](cognitive-search-skill-keyphrases.md) för att hämta viktigaste nyckelfraserna.

Under den inledande bearbetningen knäcker Azure Kognitiv sökning varje dokument för att extrahera innehåll från olika fil format. Text som kommer från käll filen placeras i ett genererat `content` fält, en för varje dokument. Därför måste du ange inmatade värden som `"/document/content"` Använd den här texten. Bild innehåll placeras i ett genererat `normalized_images` fält som anges i en färdigheter som `/document/normalized_images/*` .

Utdata kan mappas till ett index som används som indata till en underordnad kunskap, eller både, vilket är fallet med språkkod. I indexet kan en språkkod användas för filtrering. Som indata används språkkoden av textanalyskunskaper för att informera om de språkliga reglerna kring ordnedbrytning.

Mer information om grunderna i kunskapsuppsättningar finns i [Definiera en kunskapsuppsättning](cognitive-search-defining-skillset.md).

### <a name="ocr-skill"></a>OCR-kunskaper

**OCR** -kompetensen extraherar text från bilder. Den här kunskapen förutsätter att det finns ett normalized_images fält. Om du vill generera det här fältet senare i självstudien ställer vi in ```"imageAction"``` konfigurationen i indexerings definitionen till ```"generateNormalizedImages"``` .

```csharp
private static OcrSkill CreateOcrSkill()
{
    List<InputFieldMappingEntry> inputMappings = new List<InputFieldMappingEntry>();
    inputMappings.Add(new InputFieldMappingEntry("image")
    {
        Source = "/document/normalized_images/*"
    });

    List<OutputFieldMappingEntry> outputMappings = new List<OutputFieldMappingEntry>();
    outputMappings.Add(new OutputFieldMappingEntry("text")
    {
        TargetName = "text"
    });

    OcrSkill ocrSkill = new OcrSkill(inputMappings, outputMappings)
    {
        Description = "Extract text (plain and structured) from image",
        Context = "/document/normalized_images/*",
        DefaultLanguageCode = OcrSkillLanguage.En,
        ShouldDetectOrientation = true
    };

    return ocrSkill;
}
```

### <a name="merge-skill"></a>Sammanfoga kunskaper

I det här avsnittet ska du skapa en **sammanfogad** färdighet som sammanfogar dokumentets innehålls fält med den text som har producerats av OCR-kunskaper.

```csharp
private static MergeSkill CreateMergeSkill()
{
    List<InputFieldMappingEntry> inputMappings = new List<InputFieldMappingEntry>();
    inputMappings.Add(new InputFieldMappingEntry("text")
    {
        Source = "/document/content"
    });
    inputMappings.Add(new InputFieldMappingEntry("itemsToInsert")
    {
        Source = "/document/normalized_images/*/text"
    });
    inputMappings.Add(new InputFieldMappingEntry("offsets")
    {
        Source = "/document/normalized_images/*/contentOffset"
    });

    List<OutputFieldMappingEntry> outputMappings = new List<OutputFieldMappingEntry>();
    outputMappings.Add(new OutputFieldMappingEntry("mergedText")
    {
        TargetName = "merged_text"
    });

    MergeSkill mergeSkill = new MergeSkill(inputMappings, outputMappings)
    {
        Description = "Create merged_text which includes all the textual representation of each image inserted at the right location in the content field.",
        Context = "/document",
        InsertPreTag = " ",
        InsertPostTag = " "
    };

    return mergeSkill;
}
```

### <a name="language-detection-skill"></a>Kunskap om språk identifiering

**Språkidentifiering** -kunskapen identifierar språket i inmatad text och rapporterar en enda språkkod för varje dokument som skickas på begäran. Vi ska använda utdata från **språkidentifiering** -kompetensen som en del av indata för **text delnings** färdigheten.

```csharp
private static LanguageDetectionSkill CreateLanguageDetectionSkill()
{
    List<InputFieldMappingEntry> inputMappings = new List<InputFieldMappingEntry>();
    inputMappings.Add(new InputFieldMappingEntry("text")
    {
        Source = "/document/merged_text"
    });

    List<OutputFieldMappingEntry> outputMappings = new List<OutputFieldMappingEntry>();
    outputMappings.Add(new OutputFieldMappingEntry("languageCode")
    {
        TargetName = "languageCode"
    });

    LanguageDetectionSkill languageDetectionSkill = new LanguageDetectionSkill(inputMappings, outputMappings)
    {
        Description = "Detect the language used in the document",
        Context = "/document"
    };

    return languageDetectionSkill;
}
```

### <a name="text-split-skill"></a>Text delnings kunskaper

Den **delade** kunskapen nedan delar upp text efter sidor och begränsar sid längden till 4 000 tecken som mäts av `String.Length` . Algoritmen försöker dela upp texten i segment som har störst `maximumPageLength` storlek. I det här fallet gör algoritmen det bästa sättet att dela upp meningen på en mening, så storleken på segmentet kan vara något mindre än `maximumPageLength` .

```csharp
private static SplitSkill CreateSplitSkill()
{
    List<InputFieldMappingEntry> inputMappings = new List<InputFieldMappingEntry>();
    inputMappings.Add(new InputFieldMappingEntry("text")
    {
        Source = "/document/merged_text"
    });
    inputMappings.Add(new InputFieldMappingEntry("languageCode")
    {
        Source = "/document/languageCode"
    });

    List<OutputFieldMappingEntry> outputMappings = new List<OutputFieldMappingEntry>();
    outputMappings.Add(new OutputFieldMappingEntry("textItems")
    {
        TargetName = "pages",
    });

    SplitSkill splitSkill = new SplitSkill(inputMappings, outputMappings)
    {
        Description = "Split content into pages",
        Context = "/document",
        TextSplitMode = TextSplitMode.Pages,
        MaximumPageLength = 4000,
        DefaultLanguageCode = SplitSkillLanguage.En
    };

    return splitSkill;
}
```

### <a name="entity-recognition-skill"></a>Kompetens för enhets igenkänning

Den här `EntityRecognitionSkill` instansen är inställd på att identifiera kategori typ `organization` . **Entitetens igenkännings** förmåga kan också identifiera kategori typer `person` och `location` .

Observera att fältet "context" är inställt på ```"/document/pages/*"``` med en asterisk, vilket innebär att ett anriknings steg anropas för varje sida under ```"/document/pages"``` .

```csharp
private static EntityRecognitionSkill CreateEntityRecognitionSkill()
{
    List<InputFieldMappingEntry> inputMappings = new List<InputFieldMappingEntry>();
    inputMappings.Add(new InputFieldMappingEntry("text")
    {
        Source = "/document/pages/*"
    });

    List<OutputFieldMappingEntry> outputMappings = new List<OutputFieldMappingEntry>();
    outputMappings.Add(new OutputFieldMappingEntry("organizations")
    {
        TargetName = "organizations"
    });

    EntityRecognitionSkill entityRecognitionSkill = new EntityRecognitionSkill(inputMappings, outputMappings)
    {
        Description = "Recognize organizations",
        Context = "/document/pages/*",
        DefaultLanguageCode = EntityRecognitionSkillLanguage.En
    };
    entityRecognitionSkill.Categories.Add(EntityCategory.Organization);

    return entityRecognitionSkill;
}
```

### <a name="key-phrase-extraction-skill"></a>Extraherings färdighet för nyckel fraser

Precis som den `EntityRecognitionSkill` instans som precis skapades, anropas **extrahering av diskussionsämne** -kompetens för varje sida i dokumentet.

```csharp
private static KeyPhraseExtractionSkill CreateKeyPhraseExtractionSkill()
{
    List<InputFieldMappingEntry> inputMappings = new List<InputFieldMappingEntry>();
    inputMappings.Add(new InputFieldMappingEntry("text")
    {
        Source = "/document/pages/*"
    });
    inputMappings.Add(new InputFieldMappingEntry("languageCode")
    {
        Source = "/document/languageCode"
    });

    List<OutputFieldMappingEntry> outputMappings = new List<OutputFieldMappingEntry>();
    outputMappings.Add(new OutputFieldMappingEntry("keyPhrases")
    {
        TargetName = "keyPhrases"
    });

    KeyPhraseExtractionSkill keyPhraseExtractionSkill = new KeyPhraseExtractionSkill(inputMappings, outputMappings)
    {
        Description = "Extract the key phrases",
        Context = "/document/pages/*",
        DefaultLanguageCode = KeyPhraseExtractionSkillLanguage.En
    };

    return keyPhraseExtractionSkill;
}
```

### <a name="build-and-create-the-skillset"></a>Skapa och skapa färdigheter

Skapa `Skillset` med hjälp av de kunskaper du har skapat.

```csharp
private static SearchIndexerSkillset CreateOrUpdateDemoSkillSet(SearchIndexerClient indexerClient, IList<SearchIndexerSkill> skills,string cognitiveServicesKey)
{
    SearchIndexerSkillset skillset = new SearchIndexerSkillset("demoskillset", skills)
    {
        Description = "Demo skillset",
        CognitiveServicesAccount = new CognitiveServicesAccountKey(cognitiveServicesKey)
    };

    // Create the skillset in your search service.
    // The skillset does not need to be deleted if it was already created
    // since we are using the CreateOrUpdate method
    try
    {
        indexerClient.CreateOrUpdateSkillset(skillset);
    }
    catch (RequestFailedException ex)
    {
        Console.WriteLine("Failed to create the skillset\n Exception message: {0}\n", ex.Message);
        ExitProgram("Cannot continue without a skillset");
    }

    return skillset;
}
```

Lägg till följande rader i `Main` .

```csharp
// Create the skills
Console.WriteLine("Creating the skills...");
OcrSkill ocrSkill = CreateOcrSkill();
MergeSkill mergeSkill = CreateMergeSkill();
EntityRecognitionSkill entityRecognitionSkill = CreateEntityRecognitionSkill();
LanguageDetectionSkill languageDetectionSkill = CreateLanguageDetectionSkill();
SplitSkill splitSkill = CreateSplitSkill();
KeyPhraseExtractionSkill keyPhraseExtractionSkill = CreateKeyPhraseExtractionSkill();

// Create the skillset
Console.WriteLine("Creating or updating the skillset...");
List<SearchIndexerSkill> skills = new List<SearchIndexerSkill>();
skills.Add(ocrSkill);
skills.Add(mergeSkill);
skills.Add(languageDetectionSkill);
skills.Add(splitSkill);
skills.Add(entityRecognitionSkill);
skills.Add(keyPhraseExtractionSkill);

SearchIndexerSkillset skillset = CreateOrUpdateDemoSkillSet(indexerClient, skills, cognitiveServicesKey);
```

### <a name="step-3-create-an-index"></a>Steg 3: skapa ett index

I det här avsnittet definierar du indexschemat genom att ange vilka fält som ska ingå i det sökbara indexet och sökattributen för varje fält. Fält har en typ och kan ta attribut som bestämmer hur fältet ska användas (sökbart, sorteringsbart och så vidare). Fältnamn i ett index krävs inte för att matcha fältnamn identiskt i källan. I ett senare steg lägger du till fältmappningar i en indexerare för att ansluta källa-mål-fält. För det här steget definiera indexet med fältnamnkonventioner som är relevanta för ditt sökprogram.

Den här övningen använder följande fält och fälttyp:

| Fält namn | Fälttyper |
| --- | --- |
| `id` | Edm.String |
| `content` | Edm.String |
| `languageCode` | Edm.String |
| `keyPhrases` | List<Edm.String> |
| `organizations` | List<Edm.String> |

#### <a name="create-demoindex-class"></a>Skapa DemoIndex-klass

Fält för det här indexet definieras med en modell klass. Varje egenskap i modellklassen har attribut som avgör motsvarande indexfälts sökrelaterade beteende. 

Vi ska lägga till modell klassen i en ny C#-fil. Högerklicka på projektet och välj **Lägg till**  >  **nytt objekt...**, välj "klass" och ge filen ett namn och `DemoIndex.cs` Välj sedan **Lägg till**.

Se till att ange att du vill använda typer från `Azure.Search.Documents.Indexes` `System.Text.Json.Serialization` namn områdena och.

Lägg till modell klass definitionen nedan `DemoIndex.cs` och inkludera den i samma namn område där du skapar indexet.

```csharp
using Azure.Search.Documents.Indexes;
using System.Text.Json.Serialization;

namespace EnrichwithAI
{
    // The SerializePropertyNamesAsCamelCase is currently unsupported as of this writing. 
    // Replace it with JsonPropertyName
    public class DemoIndex
    {
        [SearchableField(IsSortable = true, IsKey = true)]
        [JsonPropertyName("id")]
        public string Id { get; set; }

        [SearchableField]
        [JsonPropertyName("content")]
        public string Content { get; set; }

        [SearchableField]
        [JsonPropertyName("languageCode")]
        public string LanguageCode { get; set; }

        [SearchableField]
        [JsonPropertyName("keyPhrases")]
        public string[] KeyPhrases { get; set; }

        [SearchableField]
        [JsonPropertyName("organizations")]
        public string[] Organizations { get; set; }
    }
}
```

Nu när du har definierat en modell klass `Program.cs` kan du skapa en index definition på ett ganska enkelt sätt i igen. Namnet på det här indexet kommer att vara `demoindex` . Om det redan finns ett index med det namnet tas det bort.

```csharp
private static SearchIndex CreateDemoIndex(SearchIndexClient indexClient)
{
    FieldBuilder builder = new FieldBuilder();
    var index = new SearchIndex("demoindex")
    {
        Fields = builder.Build(typeof(DemoIndex))
    };

    try
    {
        indexClient.GetIndex(index.Name);
        indexClient.DeleteIndex(index.Name);
    }
    catch (RequestFailedException ex) when (ex.Status == 404)
    {
        //if the specified index not exist, 404 will be thrown.
    }

    try
    {
        indexClient.CreateIndex(index);
    }
    catch (RequestFailedException ex)
    {
        Console.WriteLine("Failed to create the index\n Exception message: {0}\n", ex.Message);
        ExitProgram("Cannot continue without an index");
    }

    return index;
}
```

Under testningen kanske du upptäcker att du försöker skapa indexet mer än en gång. Därför bör du kontrol lera om det index som du håller på att skapa redan finns innan du försöker skapa det.

Lägg till följande rader i `Main` .

```csharp
// Create the index
Console.WriteLine("Creating the index...");
SearchIndex demoIndex = CreateDemoIndex(indexClient);
```

Lägg till följande using-instruktion för att lösa disambiguate-referensen.

```csharp
using Index = Azure.Search.Documents.Indexes.Models;
```

Mer information om index koncept finns i [skapa index (REST API)](/rest/api/searchservice/create-index).

### <a name="step-4-create-and-run-an-indexer"></a>Steg 4: skapa och köra en indexerare

Hittills har du skapat en datakälla, en kunskapsuppsättning och ett index. De här tre komponenterna blir en del av en [indexerare](search-indexer-overview.md) som sammanför varje del till en enda åtgärd i flera faser. Om du vill sammanfoga dem i en indexerare måste du definiera fältmappningar.

* FieldMappings bearbetas före färdigheter och mappar käll fälten från data källan till mål fälten i ett index. Om fält namn och typer är desamma i båda ändar, krävs ingen mappning.

* OutputFieldMappings bearbetas efter färdigheter, refererar till sourceFieldNames som inte finns förrän dokument sprickor eller berikning skapar dem. TargetFieldName är ett fält i ett index.

Förutom att koppla upp indata till utdata kan du också använda fält mappningar för att förenkla data strukturer. Mer information finns i [så här mappar du berikade fält till ett sökbart index](cognitive-search-output-field-mapping.md).

```csharp
private static SearchIndexer CreateDemoIndexer(SearchIndexerClient indexerClient, SearchIndexerDataSourceConnection dataSource, SearchIndexerSkillset skillSet, SearchIndex index)
{
    IndexingParameters indexingParameters = new IndexingParameters()
    {
        MaxFailedItems = -1,
        MaxFailedItemsPerBatch = -1,
    };
    indexingParameters.Configuration.Add("dataToExtract", "contentAndMetadata");
    indexingParameters.Configuration.Add("imageAction", "generateNormalizedImages");

    SearchIndexer indexer = new SearchIndexer("demoindexer", dataSource.Name, index.Name)
    {
        Description = "Demo Indexer",
        SkillsetName = skillSet.Name,
        Parameters = indexingParameters
    };

    FieldMappingFunction mappingFunction = new FieldMappingFunction("base64Encode");
    mappingFunction.Parameters.Add("useHttpServerUtilityUrlTokenEncode", true);

    indexer.FieldMappings.Add(new FieldMapping("metadata_storage_path")
    {
        TargetFieldName = "id",
        MappingFunction = mappingFunction

    });
    indexer.FieldMappings.Add(new FieldMapping("content")
    {
        TargetFieldName = "content"
    });

    indexer.OutputFieldMappings.Add(new FieldMapping("/document/pages/*/organizations/*")
    {
        TargetFieldName = "organizations"
    });
    indexer.OutputFieldMappings.Add(new FieldMapping("/document/pages/*/keyPhrases/*")
    {
        TargetFieldName = "keyPhrases"
    });
    indexer.OutputFieldMappings.Add(new FieldMapping("/document/languageCode")
    {
        TargetFieldName = "languageCode"
    });

    try
    {
        indexerClient.GetIndexer(indexer.Name);
        indexerClient.DeleteIndexer(indexer.Name);
    }
    catch (RequestFailedException ex) when (ex.Status == 404)
    {
        //if the specified indexer not exist, 404 will be thrown.
    }

    try
    {
        indexerClient.CreateIndexer(indexer);
    }
    catch (RequestFailedException ex)
    {
        Console.WriteLine("Failed to create the indexer\n Exception message: {0}\n", ex.Message);
        ExitProgram("Cannot continue without creating an indexer");
    }

    return indexer;
}
```

Lägg till följande rader i `Main` .

```csharp
// Create the indexer, map fields, and execute transformations
Console.WriteLine("Creating the indexer and executing the pipeline...");
SearchIndexer demoIndexer = CreateDemoIndexer(indexerClient, dataSource, skillset, demoIndex);
```

Det kan ta lite tid att slutföra indexerings bearbetningen. Trots att datauppsättningen är liten är analytiska kunskaper beräkningsintensiva. Vissa kunskaper, som bildanalys, är tidskrävande.

> [!TIP]
> När en indexerare skapas anropas pipelinen. Om det uppstår problem med att ansluta till data, mappningsindata eller -utdata eller ordningen på åtgärder visas dem i det här stadiet.

### <a name="explore-creating-the-indexer"></a>Utforska hur du skapar indexeraren

Kod uppsättningen `"maxFailedItems"`  till-1, som instruerar indexerings motorn att ignorera fel vid data import. Detta är användbart eftersom det finns det så få dokument i demo-datakällan. För en större datakälla skulle du ställa in värdet på större än 0.

Observera också `"dataToExtract"` att är inställt på `"contentAndMetadata"` . Den här instruktionen anger att indexeraren automatiskt ska extrahera innehållet från olika filformat samt metadata som är relaterade till varje fil.

När innehållet har extraherats kan du ställa in `imageAction` på att extrahera text från avbildningar som hittades i datakällan. `"imageAction"` `"generateNormalizedImages"` Konfigurationen är inställd på konfiguration, kombinerat med OCR-kunskaper och text kopplings kunskap, och anger att indexeraren ska extrahera text från bilderna (till exempel ordet "Stop" från ett trafik stopp) och bädda in det som en del av innehålls fältet. Det här beteendet gäller både avbildningarna som är inbäddade i dokumenten (tänk på en avbildning i en PDF) samt avbildningar som hittas i datakällan, till exempel en JPG-fil.

<a name="check-indexer-status"></a>

## <a name="4---monitor-indexing"></a>4 – övervaka indexering

När du har definierat indexeraren körs den automatiskt när du skickar din begäran. Beroende på vilka kognitiva kunskaper du har definierat kan indexeringen ta längre tid än väntat. Använd metoden för att ta reda på om indexeraren fortfarande körs `GetStatus` .

```csharp
private static void CheckIndexerOverallStatus(SearchIndexerClient indexerClient, SearchIndexer indexer)
{
    try
    {
        var demoIndexerExecutionInfo = indexerClient.GetIndexerStatus(indexer.Name);

        switch (demoIndexerExecutionInfo.Value.Status)
        {
            case IndexerStatus.Error:
                ExitProgram("Indexer has error status. Check the Azure Portal to further understand the error.");
                break;
            case IndexerStatus.Running:
                Console.WriteLine("Indexer is running");
                break;
            case IndexerStatus.Unknown:
                Console.WriteLine("Indexer status is unknown");
                break;
            default:
                Console.WriteLine("No indexer information");
                break;
        }
    }
    catch (RequestFailedException ex)
    {
        Console.WriteLine("Failed to get indexer overall status\n Exception message: {0}\n", ex.Message);
    }
}
```

`demoIndexerExecutionInfo` visar aktuell status och körnings historik för en indexerare.

Varningar är vanliga med vissa källfils- och kunskapskombinationer och är inte alltid tecken på problem. I den här självstudien är varningarna ofarliga (till exempel inga textindata från JPEG-filerna).

Lägg till följande rader i `Main` .

```csharp
// Check indexer overall status
Console.WriteLine("Check the indexer overall status...");
CheckIndexerOverallStatus(indexerClient, demoIndexer);
```

## <a name="5---search"></a>5-Sök

I Azure Kognitiv sökning själv studie konsol appar lägger vi vanligt vis till en fördröjning på 2 sekunder innan du kör frågor som returnerar resultat, men eftersom det tar flera minuter att slutföra den här processen, stängs konsolen och en annan metod används i stället.

Det enklaste alternativet är [Sök Utforskaren](search-explorer.md) i portalen. Du kan först köra en tom fråga som returnerar alla dokument eller en mer riktad sökning som returnerar nytt fält innehåll som skapats av pipelinen. 

1. I Azure Portal går du till sidan Sök översikt och väljer **index**.

1. Sök **`demoindex`** i listan. Den bör ha 14 dokument. Om antalet dokument är noll körs inte indexeraren eller så har sidan ännu inte uppdaterats. 

1. Välj **`demoindex`**. Sök Utforskaren är den första fliken.

1. Innehållet är sökbart så snart det första dokumentet har lästs in. Om du vill kontrol lera att innehållet finns kör du en ospecificerad fråga genom att klicka på **Sök**. Den här frågan returnerar alla för tillfället indexerade dokument, vilket ger dig en uppfattning om vad indexet innehåller.

1. Klistra sedan in följande sträng för mer hanterbara resultat: `search=*&$select=id, languageCode, organizations`

<a name="reset"></a>

## <a name="reset-and-rerun"></a>Återställa och köra igen

I de tidiga experiment stegen i utvecklingen är den mest praktiska metoden för design upprepning att ta bort objekten från Azure Kognitiv sökning och tillåta att koden återskapas. Resursnamn är unika. Om du tar bort ett objekt kan du återskapa det med samma namn.

Exempel koden för den här självstudien kontrollerar om det finns befintliga objekt och tar bort dem så att du kan köra koden igen. Du kan också använda portalen för att ta bort index, indexerare, data källor och färdighetsuppsättningar.

## <a name="takeaways"></a>Lärdomar

Den här självstudien demonstrerade de grundläggande stegen för att skapa en omfattande indexerings pipeline genom att skapa komponent delar: en data källa, färdigheter, index och indexerare.

[Inbyggda kunskaper](cognitive-search-predefined-skills.md) introducerades, tillsammans med färdigheter-definitionen och Mechanics för länkning av färdigheter tillsammans genom indata och utdata. Du har också lärt dig att `outputFieldMappings` i index definitions definitionen krävs för att dirigera berikade värden från pipelinen till ett sökbart index i en Azure kognitiv sökning-tjänst.

Slutligen lärde du dig att testa resultat och återställa systemet för ytterligare iterationer. Du har lärt dig att när du utfärdar frågor mot indexet returneras utdata som skapades av pipelinen för berikande indexering. Du har också lärt dig att kontrollera indexerarstatus, och vilka objekt du ska ta bort innan du kör en pipeline igen.

## <a name="clean-up-resources"></a>Rensa resurser

När du arbetar i din egen prenumeration är det en bra idé att ta bort de resurser som du inte längre behöver i slutet av projektet. Resurser som fortsätter att köras kostar pengar. Du kan ta bort resurser individuellt eller ta bort resursgruppen om du vill ta bort hela uppsättningen resurser.

Du kan hitta och hantera resurser i portalen med hjälp av länken alla resurser eller resurs grupper i det vänstra navigerings fönstret.

## <a name="next-steps"></a>Nästa steg

Nu när du är bekant med alla objekt i en pipeline för AI-anrikning, tar vi en närmare titt på färdigheter-definitioner och enskilda kunskaper.

> [!div class="nextstepaction"]
> [Så här skapar du en färdigheter](cognitive-search-defining-skillset.md)