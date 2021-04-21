---
title: 'Snabbstart: Textanalys klientbibliotek för C#-| Microsoft Docs'
description: 'Kom igång med Textanalys klientbibliotek för C #'
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 04/19/2021
ms.author: aahi
ms.reviewer: assafi
ms.openlocfilehash: 1fd102f0f94f1ce53bebfba94d4f4c1a1f9e3812
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107765150"
---
<a name="HOLTop"></a>

# <a name="version-31-preview"></a>[Förhandsversion 3.1](#tab/version-3-1)

[Referensdokumentation för v3.1](/dotnet/api/azure.ai.textanalytics?preserve-view=true&view=azure-dotnet-preview)  |  [v3.1-bibliotekskällkod](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/textanalytics/Azure.AI.TextAnalytics)  |  [v3.1-paket (NuGet)](https://www.nuget.org/packages/Azure.AI.TextAnalytics/5.1.0-beta.5)  |  [v3.1-exempel](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/textanalytics/Azure.AI.TextAnalytics/samples)

# <a name="version-30"></a>[Version 3.0](#tab/version-3)

[Referensdokumentation för v3](/dotnet/api/azure.ai.textanalytics)  |  [v3-bibliotekskällkod](https://github.com/Azure/azure-sdk-for-net/tree/Azure.AI.TextAnalytics_5.0.0/sdk/textanalytics/Azure.AI.TextAnalytics)  |  [v3-paket (NuGet)](https://www.nuget.org/packages/Azure.AI.TextAnalytics)  |  [v3-exempel](https://github.com/Azure/azure-sdk-for-net/tree/Azure.AI.TextAnalytics_5.0.0/sdk/textanalytics/Azure.AI.TextAnalytics/samples)

---

## <a name="prerequisites"></a>Förutsättningar

* Azure-prenumeration [– Skapa en utan kostnad](https://azure.microsoft.com/free/cognitive-services)
* Den [Visual Studio IDE](https://visualstudio.microsoft.com/vs/)
* När du har din Azure-prenumeration skapar du en Textanalys-resurs för att skapa Textanalys resurs i Azure Portal för att <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics"  title=" hämta din nyckel och "  target="_blank"> </a> slutpunkt.  När den har distribuerats klickar du **på Gå till resurs**.
    * Du behöver nyckeln och slutpunkten från resursen som du skapar för att ansluta ditt program till Textanalys API. Du klistrar in nyckeln och slutpunkten i koden nedan senare i snabbstarten.
    * Du kan använda den kostnadsfria prisnivån ( `F0` ) för att prova tjänsten och uppgradera senare till en betald nivå för produktion.
* Om du vill använda funktionen Analysera behöver du Textanalys resurs med prisnivån standard (S).

## <a name="setting-up"></a>Inrätta

### <a name="create-a-new-net-core-application"></a>Skapa ett nytt .NET Core-program

Med hjälp Visual Studio IDE skapar du en ny .NET Core-konsolapp. Då skapas ett "Hello World"-projekt med en enda C#-källfil: *program.cs*.

# <a name="version-31-preview"></a>[Förhandsversion 3.1](#tab/version-3-1)

Installera klientbiblioteket genom att högerklicka på lösningen i Solution Explorer **och** välja **Hantera NuGet-paket.** I pakethanteraren som öppnas väljer du **Bläddra** och söker efter `Azure.AI.TextAnalytics` . Markera rutan **inkludera prerelase,** välj version `5.1.0-beta.5` och sedan **Installera**. Du kan också använda [Package Manager-konsolen](/nuget/consume-packages/install-use-packages-powershell#find-and-install-a-package).

# <a name="version-30"></a>[Version 3.0](#tab/version-3)

Installera klientbiblioteket genom att högerklicka på lösningen i Solution Explorer **och** välja **Hantera NuGet-paket.** I pakethanteraren som öppnas väljer du **Bläddra** och söker efter `Azure.AI.TextAnalytics` . Välj version `5.0.0` och sedan **Installera**. Du kan också använda [Package Manager-konsolen](/nuget/consume-packages/install-use-packages-powershell#find-and-install-a-package).


> [!TIP]
> Vill du visa hela snabbstartskodfilen samtidigt? Du hittar den på [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/dotnet/TextAnalytics/program.cs), som innehåller kodexe exemplen i den här snabbstarten. 

---

# <a name="version-31-preview"></a>[Version 3.1 –förhandsversion](#tab/version-3-1)

Öppna *filen program.cs* och lägg till följande `using` direktiv:

```csharp
using Azure;
using System;
using System.Globalization;
using Azure.AI.TextAnalytics;
```

I programmets `Program` -klass skapar du variabler för resursens nyckel och slutpunkt.

[!INCLUDE [text-analytics-find-resource-information](../find-azure-resource-info.md)]

```csharp
private static readonly AzureKeyCredential credentials = new AzureKeyCredential("<replace-with-your-text-analytics-key-here>");
private static readonly Uri endpoint = new Uri("<replace-with-your-text-analytics-endpoint-here>");
```

Ersätt programmets `Main` -metod. Du definierar metoderna som anropas här senare.

```csharp
static void Main(string[] args)
{
    var client = new TextAnalyticsClient(endpoint, credentials);
    // You will implement these methods later in the quickstart.
    SentimentAnalysisExample(client);
    SentimentAnalysisWithOpinionMiningExample(client);
    LanguageDetectionExample(client);
    EntityRecognitionExample(client);
    EntityLinkingExample(client);
    RecognizePIIExample(client);
    KeyPhraseExtractionExample(client);

    Console.Write("Press any key to exit.");
    Console.ReadKey();
}
```

# <a name="version-30"></a>[Version 3.0](#tab/version-3)

Öppna *filen program.cs* och lägg till följande `using` direktiv:

```csharp
using Azure;
using System;
using System.Globalization;
using Azure.AI.TextAnalytics;
```

I programmets `Program` -klass skapar du variabler för resursens nyckel och slutpunkt.

[!INCLUDE [text-analytics-find-resource-information](../find-azure-resource-info.md)]

```csharp
private static readonly AzureKeyCredential credentials = new AzureKeyCredential("<replace-with-your-text-analytics-key-here>");
private static readonly Uri endpoint = new Uri("<replace-with-your-text-analytics-endpoint-here>");
```

Ersätt programmets `Main` -metod. Du definierar metoderna som anropas här senare.

```csharp
static void Main(string[] args)
{
    var client = new TextAnalyticsClient(endpoint, credentials);
    // You will implement these methods later in the quickstart.
    SentimentAnalysisExample(client);
    LanguageDetectionExample(client);
    EntityRecognitionExample(client);
    EntityLinkingExample(client);
    KeyPhraseExtractionExample(client);

    Console.Write("Press any key to exit.");
    Console.ReadKey();
}
```

---

## <a name="object-model"></a>Objektmodell

Den Textanalys klienten är ett objekt som autentiserar till Azure med hjälp av din nyckel och innehåller funktioner för att acceptera text som enkla `TextAnalyticsClient` strängar eller som en batch. Du kan skicka text till API:et synkront eller asynkront. Svarsobjektet innehåller analysinformationen för varje dokument som du skickar. 

Om du använder en version av tjänsten kan du använda en valfri instans för att initiera klienten med olika standardinställningar (till exempel standardspråk eller `3.x` `TextAnalyticsClientOptions` lands-/regionstips). Du kan också autentisera med en Azure Active Directory token. 

## <a name="code-examples"></a>Kodexempel

* [Attitydanalys](#sentiment-analysis)
* [Yttrandeutvinning](#opinion-mining)
* [Språkidentifiering](#language-detection)
* [Igenkänning av namngiven enhet](#named-entity-recognition-ner)
* [Entitetslänkning](#entity-linking)
* [Extrahering av nyckelfraser](#key-phrase-extraction)

## <a name="authenticate-the-client"></a>Autentisera klienten

# <a name="version-31-preview"></a>[Förhandsversion 3.1](#tab/version-3-1)

Kontrollera att main-metoden från tidigare skapar ett nytt klientobjekt med din slutpunkt och dina autentiseringsuppgifter.

```csharp
var client = new TextAnalyticsClient(endpoint, credentials);
```

# <a name="version-30"></a>[Version 3.0](#tab/version-3)

Kontrollera att main-metoden från tidigare skapar ett nytt klientobjekt med din slutpunkt och dina autentiseringsuppgifter.

```csharp
var client = new TextAnalyticsClient(endpoint, credentials);
```

---

## <a name="sentiment-analysis"></a>Attitydanalys

# <a name="version-31-preview"></a>[Förhandsversion 3.1](#tab/version-3-1)

Skapa en ny funktion med `SentimentAnalysisExample()` namnet som tar klienten som du skapade tidigare och anropa dess `AnalyzeSentiment()` funktion. Det returnerade objektet innehåller sentimentetiketten och poängen för hela indatadokumentet, samt en attitydanalys för varje mening om `Response<DocumentSentiment>` det lyckas. Om det uppstod ett fel, kommer det att skapa ett `RequestFailedException` .

```csharp
static void SentimentAnalysisExample(TextAnalyticsClient client)
{
    string inputText = "I had the best day of my life. I wish you were there with me.";
    DocumentSentiment documentSentiment = client.AnalyzeSentiment(inputText);
    Console.WriteLine($"Document sentiment: {documentSentiment.Sentiment}\n");

    foreach (var sentence in documentSentiment.Sentences)
    {
        Console.WriteLine($"\tText: \"{sentence.Text}\"");
        Console.WriteLine($"\tSentence sentiment: {sentence.Sentiment}");
        Console.WriteLine($"\tPositive score: {sentence.ConfidenceScores.Positive:0.00}");
        Console.WriteLine($"\tNegative score: {sentence.ConfidenceScores.Negative:0.00}");
        Console.WriteLine($"\tNeutral score: {sentence.ConfidenceScores.Neutral:0.00}\n");
    }
}
```

### <a name="output"></a>Utdata

```console
Document sentiment: Positive

        Text: "I had the best day of my life."
        Sentence sentiment: Positive
        Positive score: 1.00
        Negative score: 0.00
        Neutral score: 0.00

        Text: "I wish you were there with me."
        Sentence sentiment: Neutral
        Positive score: 0.21
        Negative score: 0.02
        Neutral score: 0.77
```

### <a name="opinion-mining"></a>Yttrandeutvinning

Skapa en ny funktion med `SentimentAnalysisWithOpinionMiningExample()` namnet som tar klienten som du skapade tidigare och anropa dess funktion med alternativet i `AnalyzeSentimentBatch()` `IncludeOpinionMining` `AnalyzeSentimentOptions` påsen. Det `AnalyzeSentimentResultCollection` returnerade objektet innehåller samlingen `AnalyzeSentimentResult` där representerar `Response<DocumentSentiment>` . Skillnaden mellan och `SentimentAnalysis()` är att den senare innehåller i varje mening, som visar ett `SentimentAnalysisWithOpinionMiningExample()` `SentenceOpinion` analyserat mål och relaterade utvärderingarna. Om det uppstod ett fel 00:00. `RequestFailedException`

```csharp
static void SentimentAnalysisWithOpinionMiningExample(TextAnalyticsClient client)
{
    var documents = new List<string>
    {
        "The food and service were unacceptable, but the concierge were nice."
    };

    AnalyzeSentimentResultCollection reviews = client.AnalyzeSentimentBatch(documents, options: new AnalyzeSentimentOptions()
    {
        IncludeOpinionMining = true
    });

    foreach (AnalyzeSentimentResult review in reviews)
    {
        Console.WriteLine($"Document sentiment: {review.DocumentSentiment.Sentiment}\n");
        Console.WriteLine($"\tPositive score: {review.DocumentSentiment.ConfidenceScores.Positive:0.00}");
        Console.WriteLine($"\tNegative score: {review.DocumentSentiment.ConfidenceScores.Negative:0.00}");
        Console.WriteLine($"\tNeutral score: {review.DocumentSentiment.ConfidenceScores.Neutral:0.00}\n");
        foreach (SentenceSentiment sentence in review.DocumentSentiment.Sentences)
        {
            Console.WriteLine($"\tText: \"{sentence.Text}\"");
            Console.WriteLine($"\tSentence sentiment: {sentence.Sentiment}");
            Console.WriteLine($"\tSentence positive score: {sentence.ConfidenceScores.Positive:0.00}");
            Console.WriteLine($"\tSentence negative score: {sentence.ConfidenceScores.Negative:0.00}");
            Console.WriteLine($"\tSentence neutral score: {sentence.ConfidenceScores.Neutral:0.00}\n");

            foreach (SentenceOpinion sentenceOpinion in sentence.Opinions)
            {
                Console.WriteLine($"\tTarget: {sentenceOpinion.Target.Text}, Value: {sentenceOpinion.Target.Sentiment}");
                Console.WriteLine($"\tTarget positive score: {sentenceOpinion.Target.ConfidenceScores.Positive:0.00}");
                Console.WriteLine($"\tTarget negative score: {sentenceOpinion.Target.ConfidenceScores.Negative:0.00}");
                foreach (AssessmentSentiment assessment in sentenceOpinion.Assessments)
                {
                    Console.WriteLine($"\t\tRelated Assessment: {assessment.Text}, Value: {assessment.Sentiment}");
                    Console.WriteLine($"\t\tRelated Assessment positive score: {assessment.ConfidenceScores.Positive:0.00}");
                    Console.WriteLine($"\t\tRelated Assessment negative score: {assessment.ConfidenceScores.Negative:0.00}");
                }
            }
        }
        Console.WriteLine($"\n");
    }
}
```

### <a name="output"></a>Utdata

```console
Document sentiment: Positive

        Positive score: 0.84
        Negative score: 0.16
        Neutral score: 0.00

        Text: "The food and service were unacceptable, but the concierge were nice."
        Sentence sentiment: Positive
        Sentence positive score: 0.84
        Sentence negative score: 0.16
        Sentence neutral score: 0.00

        Target: food, Value: Negative
        Target positive score: 0.01
        Target negative score: 0.99
                Related Assessment: unacceptable, Value: Negative
                Related Assessment positive score: 0.01
                Related Assessment negative score: 0.99
        Target: service, Value: Negative
        Target positive score: 0.01
        Target negative score: 0.99
                Related Assessment: unacceptable, Value: Negative
                Related Assessment positive score: 0.01
                Related Assessment negative score: 0.99
        Target: concierge, Value: Positive
        Target positive score: 1.00
        Target negative score: 0.00
                Related Assessment: nice, Value: Positive
                Related Assessment positive score: 1.00
                Related Assessment negative score: 0.00

Press any key to exit.
```

# <a name="version-30"></a>[Version 3.0](#tab/version-3)

Skapa en ny funktion med `SentimentAnalysisExample()` namnet som tar den klient som du skapade tidigare och anropa dess `AnalyzeSentiment()` funktion. Det returnerade objektet innehåller sentimentetiketten och poängen för hela indatadokumentet, samt en attitydanalys för varje mening om `Response<DocumentSentiment>` det lyckas. Om det uppstod ett fel 00:00. `RequestFailedException`

```csharp
static void SentimentAnalysisExample(TextAnalyticsClient client)
{
    string inputText = "I had the best day of my life. I wish you were there with me.";
    DocumentSentiment documentSentiment = client.AnalyzeSentiment(inputText);
    Console.WriteLine($"Document sentiment: {documentSentiment.Sentiment}\n");

    foreach (var sentence in documentSentiment.Sentences)
    {
        Console.WriteLine($"\tText: \"{sentence.Text}\"");
        Console.WriteLine($"\tSentence sentiment: {sentence.Sentiment}");
        Console.WriteLine($"\tPositive score: {sentence.ConfidenceScores.Positive:0.00}");
        Console.WriteLine($"\tNegative score: {sentence.ConfidenceScores.Negative:0.00}");
        Console.WriteLine($"\tNeutral score: {sentence.ConfidenceScores.Neutral:0.00}\n");
    }
}
```

### <a name="output"></a>Utdata

```console
Document sentiment: Positive

        Text: "I had the best day of my life."
        Sentence sentiment: Positive
        Positive score: 1.00
        Negative score: 0.00
        Neutral score: 0.00

        Text: "I wish you were there with me."
        Sentence sentiment: Neutral
        Positive score: 0.21
        Negative score: 0.02
        Neutral score: 0.77
```

---

## <a name="language-detection"></a>Språkidentifiering

# <a name="version-31-preview"></a>[Version 3.1 –förhandsversion](#tab/version-3-1)


Skapa en ny funktion med `LanguageDetectionExample()` namnet som tar klienten som du skapade tidigare och anropa dess  `DetectLanguage()` funktion. Det `Response<DetectedLanguage>` returnerade objektet innehåller det identifierade språket tillsammans med dess namn och ISO-6391-kod. Om det uppstod ett fel 2018 000 000 `RequestFailedException` .

> [!Tip]
> I vissa fall kan det vara svårt att disamuera språk baserat på indata. Du kan använda `countryHint` parametern för att ange en lands-/regionskod med två bokstäver. Som standard använder API:et "US" som standard countryHint. För att ta bort det här beteendet kan du återställa den här parametern genom att ange värdet till tom sträng `countryHint = ""` . Om du vill ange ett annat `TextAnalyticsClientOptions.DefaultCountryHint` standardvärde anger du egenskapen och skickar den under klientens initiering.

```csharp
static void LanguageDetectionExample(TextAnalyticsClient client)
{
    DetectedLanguage detectedLanguage = client.DetectLanguage("Ce document est rédigé en Français.");
    Console.WriteLine("Language:");
    Console.WriteLine($"\t{detectedLanguage.Name},\tISO-6391: {detectedLanguage.Iso6391Name}\n");
}
```

### <a name="output"></a>Utdata

```console
Language:
        French, ISO-6391: fr
```

# <a name="version-30"></a>[Version 3.0](#tab/version-3)


Skapa en ny funktion med `LanguageDetectionExample()` namnet som tar den klient som du skapade tidigare och anropa dess  `DetectLanguage()` funktion. Det `Response<DetectedLanguage>` returnerade objektet innehåller det identifierade språket tillsammans med dess namn och ISO-6391-kod. Om det uppstod ett fel 2018 000 000 `RequestFailedException` .

> [!Tip]
> I vissa fall kan det vara svårt att disambiuera språk baserat på indata. Du kan använda `countryHint` parametern för att ange en lands-/regionskod med två bokstäver. Som standard använder API:et "US" som standard countryHint. Om du vill ta bort det här beteendet kan du återställa den här parametern genom att ange värdet till tom sträng `countryHint = ""` . Om du vill ange ett annat `TextAnalyticsClientOptions.DefaultCountryHint` standardvärde anger du egenskapen och skickar den under klientens initiering.

```csharp
static void LanguageDetectionExample(TextAnalyticsClient client)
{
    DetectedLanguage detectedLanguage = client.DetectLanguage("Ce document est rédigé en Français.");
    Console.WriteLine("Language:");
    Console.WriteLine($"\t{detectedLanguage.Name},\tISO-6391: {detectedLanguage.Iso6391Name}\n");
}
```

### <a name="output"></a>Utdata

```console
Language:
        French, ISO-6391: fr
```


---

## <a name="named-entity-recognition-ner"></a>Igenkänning av namngiven entitet (NER)

# <a name="version-31-preview"></a>[Förhandsversion 3.1](#tab/version-3-1)


Skapa en ny funktion med `EntityRecognitionExample()` namnet som tar klienten som du skapade tidigare, anropa dess funktion och `RecognizeEntities()` iterera genom resultatet. Det `Response<CategorizedEntityCollection>` returnerade objektet innehåller samlingen identifierade entiteter `CategorizedEntity` . Om det uppstod ett fel, kommer det att skapa ett `RequestFailedException` .

```csharp
static void EntityRecognitionExample(TextAnalyticsClient client)
{
    var response = client.RecognizeEntities("I had a wonderful trip to Seattle last week.");
    Console.WriteLine("Named Entities:");
    foreach (var entity in response.Value)
    {
        Console.WriteLine($"\tText: {entity.Text},\tCategory: {entity.Category},\tSub-Category: {entity.SubCategory}");
        Console.WriteLine($"\t\tScore: {entity.ConfidenceScore:F2},\tLength: {entity.Length},\tOffset: {entity.Offset}\n");
    }
}
```

### <a name="output"></a>Utdata

```console
Named Entities:
        Text: trip,     Category: Event,        Sub-Category:
                Score: 0.61,    Length: 4,      Offset: 18

        Text: Seattle,  Category: Location,     Sub-Category: GPE
                Score: 0.82,    Length: 7,      Offset: 26

        Text: last week,        Category: DateTime,     Sub-Category: DateRange
                Score: 0.80,    Length: 9,      Offset: 34
```

### <a name="entity-linking"></a>Entitetslänkning

Skapa en ny funktion med `EntityLinkingExample()` namnet som tar klienten som du skapade tidigare, anropa dess funktion och `RecognizeLinkedEntities()` iterera genom resultatet. Det `Response<LinkedEntityCollection>` returnerade objektet innehåller samlingen identifierade entiteter `LinkedEntity` . Om det uppstod ett fel, kommer det att skapa ett `RequestFailedException` . Eftersom länkade entiteter identifieras unikt grupperas förekomster av samma entitet under ett `LinkedEntity` objekt som en lista med `LinkedEntityMatch` objekt.

```csharp
static void EntityLinkingExample(TextAnalyticsClient client)
{
    var response = client.RecognizeLinkedEntities(
        "Microsoft was founded by Bill Gates and Paul Allen on April 4, 1975, " +
        "to develop and sell BASIC interpreters for the Altair 8800. " +
        "During his career at Microsoft, Gates held the positions of chairman, " +
        "chief executive officer, president and chief software architect, " +
        "while also being the largest individual shareholder until May 2014.");
    Console.WriteLine("Linked Entities:");
    foreach (var entity in response.Value)
    {
        Console.WriteLine($"\tName: {entity.Name},\tID: {entity.DataSourceEntityId},\tURL: {entity.Url}\tData Source: {entity.DataSource}");
        Console.WriteLine("\tMatches:");
        foreach (var match in entity.Matches)
        {
            Console.WriteLine($"\t\tText: {match.Text}");
            Console.WriteLine($"\t\tScore: {match.ConfidenceScore:F2}");
            Console.WriteLine($"\t\tLength: {match.Length}");
            Console.WriteLine($"\t\tOffset: {match.Offset}\n");
        }
    }
}
```

### <a name="output"></a>Utdata

```console
Linked Entities:
        Name: Microsoft,        ID: Microsoft,  URL: https://en.wikipedia.org/wiki/Microsoft    Data Source: Wikipedia
        Matches:
                Text: Microsoft
                Score: 0.55
                Length: 9
                Offset: 0

                Text: Microsoft
                Score: 0.55
                Length: 9
                Offset: 150

        Name: Bill Gates,       ID: Bill Gates, URL: https://en.wikipedia.org/wiki/Bill_Gates   Data Source: Wikipedia
        Matches:
                Text: Bill Gates
                Score: 0.63
                Length: 10
                Offset: 25

                Text: Gates
                Score: 0.63
                Length: 5
                Offset: 161

        Name: Paul Allen,       ID: Paul Allen, URL: https://en.wikipedia.org/wiki/Paul_Allen   Data Source: Wikipedia
        Matches:
                Text: Paul Allen
                Score: 0.60
                Length: 10
                Offset: 40

        Name: April 4,  ID: April 4,    URL: https://en.wikipedia.org/wiki/April_4      Data Source: Wikipedia
        Matches:
                Text: April 4
                Score: 0.32
                Length: 7
                Offset: 54

        Name: BASIC,    ID: BASIC,      URL: https://en.wikipedia.org/wiki/BASIC        Data Source: Wikipedia
        Matches:
                Text: BASIC
                Score: 0.33
                Length: 5
                Offset: 89

        Name: Altair 8800,      ID: Altair 8800,        URL: https://en.wikipedia.org/wiki/Altair_8800  Data Source: Wikipedia
        Matches:
                Text: Altair 8800
                Score: 0.88
                Length: 11
                Offset: 116
```

### <a name="personally-identifiable-information-recognition"></a>Igenkänning av personligt identifierbar information

Skapa en ny funktion med `RecognizePIIExample()` namnet som tar klienten som du skapade tidigare, anropa dess funktion och `RecognizePiiEntities()` iterera genom resultatet. Returnerade `PiiEntityCollection` representerar listan över identifierade PII-entiteter. Om det uppstod ett fel, kommer det att skapa ett `RequestFailedException` .

```csharp
static void RecognizePIIExample(TextAnalyticsClient client)
{
    string document = "A developer with SSN 859-98-0987 whose phone number is 800-102-1100 is building tools with our APIs.";

    PiiEntityCollection entities = client.RecognizePiiEntities(document).Value;

    Console.WriteLine($"Redacted Text: {entities.RedactedText}");
    if (entities.Count > 0)
    {
        Console.WriteLine($"Recognized {entities.Count} PII entit{(entities.Count > 1 ? "ies" : "y")}:");
        foreach (PiiEntity entity in entities)
        {
            Console.WriteLine($"Text: {entity.Text}, Category: {entity.Category}, SubCategory: {entity.SubCategory}, Confidence score: {entity.ConfidenceScore}");
        }
    }
    else
    {
        Console.WriteLine("No entities were found.");
    }
}
```

### <a name="output"></a>Utdata

```console
Redacted Text: A developer with SSN *********** whose phone number is ************ is building tools with our APIs.
Recognized 2 PII entities:
Text: 859-98-0987, Category: U.S. Social Security Number (SSN), SubCategory: , Confidence score: 0.65
Text: 800-102-1100, Category: Phone Number, SubCategory: , Confidence score: 0.8
```

# <a name="version-30"></a>[Version 3.0](#tab/version-3)


> [!NOTE]
> Ny i version `3.0` :
> * Entitetslänkning är nu avgränsad från entitetsigenkänning.


Skapa en ny funktion med `EntityRecognitionExample()` namnet som tar klienten som du skapade tidigare, anropa dess funktion och `RecognizeEntities()` iterera genom resultatet. Det `Response<IReadOnlyCollection<CategorizedEntity>>` returnerade objektet innehåller listan över identifierade entiteter. Om det uppstod ett fel 2018 000 000 `RequestFailedException` .

```csharp
static void EntityRecognitionExample(TextAnalyticsClient client)
{
    var response = client.RecognizeEntities("I had a wonderful trip to Seattle last week.");
    Console.WriteLine("Named Entities:");
    foreach (var entity in response.Value)
    {
        Console.WriteLine($"\tText: {entity.Text},\tCategory: {entity.Category},\tSub-Category: {entity.SubCategory}");
        Console.WriteLine($"\t\tScore: {entity.ConfidenceScore:F2}\n");
    }
}
```

### <a name="output"></a>Utdata

```console
Named Entities:
        Text: trip,     Category: Event,        Sub-Category:
                Score: 0.61

        Text: Seattle,  Category: Location,     Sub-Category: GPE
                Score: 0.82

        Text: last week,        Category: DateTime,     Sub-Category: DateRange
                Score: 0.80
```

### <a name="entity-linking"></a>Entitetslänkning

Skapa en ny funktion med `EntityLinkingExample()` namnet som tar klienten som du skapade tidigare, anropa dess funktion och `RecognizeLinkedEntities()` iterera genom resultatet. Returnerade `Response<IReadOnlyCollection<LinkedEntity>>` representerar listan över identifierade entiteter. Om det uppstod ett fel 00:00. `RequestFailedException` Eftersom länkade entiteter identifieras unikt grupperas förekomster av samma entitet under ett `LinkedEntity` objekt som en lista med `LinkedEntityMatch` objekt.

```csharp
static void EntityLinkingExample(TextAnalyticsClient client)
{
    var response = client.RecognizeLinkedEntities(
        "Microsoft was founded by Bill Gates and Paul Allen on April 4, 1975, " +
        "to develop and sell BASIC interpreters for the Altair 8800. " +
        "During his career at Microsoft, Gates held the positions of chairman, " +
        "chief executive officer, president and chief software architect, " +
        "while also being the largest individual shareholder until May 2014.");
    Console.WriteLine("Linked Entities:");
    foreach (var entity in response.Value)
    {
        Console.WriteLine($"\tName: {entity.Name},\tID: {entity.DataSourceEntityId},\tURL: {entity.Url}\tData Source: {entity.DataSource}");
        Console.WriteLine("\tMatches:");
        foreach (var match in entity.Matches)
        {
            Console.WriteLine($"\t\tText: {match.Text}");
            Console.WriteLine($"\t\tScore: {match.ConfidenceScore:F2}\n");
        }
    }
}
```

### <a name="output"></a>Utdata

```console
Linked Entities:
        Name: Altair 8800,      ID: Altair 8800,        URL: https://en.wikipedia.org/wiki/Altair_8800  Data Source: Wikipedia
        Matches:
                Text: Altair 8800
                Score: 0.88

        Name: Bill Gates,       ID: Bill Gates, URL: https://en.wikipedia.org/wiki/Bill_Gates   Data Source: Wikipedia
        Matches:
                Text: Bill Gates
                Score: 0.63

                Text: Gates
                Score: 0.63

        Name: Paul Allen,       ID: Paul Allen, URL: https://en.wikipedia.org/wiki/Paul_Allen   Data Source: Wikipedia
        Matches:
                Text: Paul Allen
                Score: 0.60

        Name: Microsoft,        ID: Microsoft,  URL: https://en.wikipedia.org/wiki/Microsoft    Data Source: Wikipedia
        Matches:
                Text: Microsoft
                Score: 0.55

                Text: Microsoft
                Score: 0.55

        Name: April 4,  ID: April 4,    URL: https://en.wikipedia.org/wiki/April_4      Data Source: Wikipedia
        Matches:
                Text: April 4
                Score: 0.32

        Name: BASIC,    ID: BASIC,      URL: https://en.wikipedia.org/wiki/BASIC        Data Source: Wikipedia
        Matches:
                Text: BASIC
                Score: 0.33
```

--- 


### <a name="key-phrase-extraction"></a>Extrahering av nyckelfraser

# <a name="version-31-preview"></a>[Version 3.1 –förhandsversion](#tab/version-3-1)

Skapa en ny funktion med `KeyPhraseExtractionExample()` namnet som tar den klient som du skapade tidigare och anropa dess `ExtractKeyPhrases()` funktion. Det `<Response<KeyPhraseCollection>` returnerade objektet innehåller listan över identifierade nyckelfraser. Om det uppstod ett fel 2018 000 000 `RequestFailedException` .

```csharp
static void KeyPhraseExtractionExample(TextAnalyticsClient client)
{
    var response = client.ExtractKeyPhrases("My cat might need to see a veterinarian.");

    // Printing key phrases
    Console.WriteLine("Key phrases:");

    foreach (string keyphrase in response.Value)
    {
        Console.WriteLine($"\t{keyphrase}");
    }
}
```

### <a name="output"></a>Utdata

```console
Key phrases:
    cat
    veterinarian
```

# <a name="version-30"></a>[Version 3.0](#tab/version-3)

Skapa en ny funktion med `KeyPhraseExtractionExample()` namnet som tar klienten som du skapade tidigare och anropa dess `ExtractKeyPhrases()` funktion. Det `<Response<IReadOnlyCollection<string>>` returnerade objektet innehåller listan över identifierade nyckelfraser. Om det uppstod ett fel 00:00. `RequestFailedException`

```csharp
static void KeyPhraseExtractionExample(TextAnalyticsClient client)
{
    var response = client.ExtractKeyPhrases("My cat might need to see a veterinarian.");

    // Printing key phrases
    Console.WriteLine("Key phrases:");

    foreach (string keyphrase in response.Value)
    {
        Console.WriteLine($"\t{keyphrase}");
    }
}
```

### <a name="output"></a>Utdata

```console
Key phrases:
    cat
    veterinarian
```

---

## <a name="use-the-api-asynchronously-with-the-analyze-operation"></a>Använda API:et asynkront med analysåtgärden

# <a name="version-31-preview"></a>[Version 3.1 –förhandsversion](#tab/version-3-1)

[!INCLUDE [Analyze operation pricing](../analyze-operation-pricing-caution.md)]

Skapa en ny funktion med `AnalyzeOperationExample()` namnet som tar den klient som du skapade tidigare och anropa dess `StartAnalyzeBatchActionsAsync()` funktion. Det `AnalyzeBatchActionsOperation` returnerade objektet innehåller `Operation` gränssnittsobjektet. Eftersom det är en långvarig åtgärd `await` på för det värde som ska `operation.WaitForCompletionAsync()` uppdateras. När `WaitForCompletionAsync()` är klar ska samlingen uppdateras i `operation.Value` . Om det uppstod ett fel 00:00. `RequestFailedException`


```csharp
static async Task AnalyzeOperationExample(TextAnalyticsClient client)
{
    string inputText = "Microsoft was founded by Bill Gates and Paul Allen.";

    var batchDocuments = new List<string> { inputText };


    TextAnalyticsActions actions = new TextAnalyticsActions()
    {
        RecognizeEntitiesOptions = new List<RecognizeEntitiesOptions>() { new RecognizeEntitiesOptions() },
        DisplayName = "Analyze Operation Quick Start Example"
    };

    AnalyzeBatchActionsOperation operation = await client.StartAnalyzeBatchActionsAsync(batchDocuments, actions);

    await operation.WaitForCompletionAsync();

    Console.WriteLine($"Status: {operation.Status}");
    Console.WriteLine($"Created On: {operation.CreatedOn}");
    Console.WriteLine($"Expires On: {operation.ExpiresOn}");
    Console.WriteLine($"Last modified: {operation.LastModified}");
    if (!string.IsNullOrEmpty(operation.DisplayName))
        Console.WriteLine($"Display name: {operation.DisplayName}");
    Console.WriteLine($"Total actions: {operation.TotalActions}");
    Console.WriteLine($"  Succeeded actions: {operation.ActionsSucceeded}");
    Console.WriteLine($"  Failed actions: {operation.ActionsFailed}");
    Console.WriteLine($"  In progress actions: {operation.ActionsInProgress}");

    await foreach (AnalyzeBatchActionsResult documentsInPage in operation.Value)
    {
        RecognizeEntitiesResultCollection entitiesResult = documentsInPage.RecognizeEntitiesActionsResults.FirstOrDefault().Result;

        Console.WriteLine("Recognized Entities");

        foreach (RecognizeEntitiesResult result in entitiesResult)
        {
            Console.WriteLine($"  Recognized the following {result.Entities.Count} entities:");

            foreach (CategorizedEntity entity in result.Entities)
            {
                Console.WriteLine($"  Entity: {entity.Text}");
                Console.WriteLine($"  Category: {entity.Category}");
                Console.WriteLine($"  Offset: {entity.Offset}");
                Console.WriteLine($"  Length: {entity.Length}");
                Console.WriteLine($"  ConfidenceScore: {entity.ConfidenceScore}");
                Console.WriteLine($"  SubCategory: {entity.SubCategory}");
            }
            Console.WriteLine("");
        }
    }
}
```

När du har lagt till det här exemplet i programmet anropar du i metoden `main()` med `await` .

```csharp
await AnalyzeOperationExample(client).ConfigureAwait(false);
```
### <a name="output"></a>Utdata

```console
Status: succeeded
Created On: 3/10/2021 2:25:01 AM +00:00
Expires On: 3/11/2021 2:25:01 AM +00:00
Last modified: 3/10/2021 2:25:05 AM +00:00
Display name: Analyze Operation Quick Start Example
Total actions: 1
  Succeeded actions: 1
  Failed actions: 0
  In progress actions: 0
Recognized Entities
    Recognized the following 3 entities:
    Entity: Microsoft
    Category: Organization
    Offset: 0
    ConfidenceScore: 0.83
    SubCategory: 
    Entity: Bill Gates
    Category: Person
    Offset: 25
    ConfidenceScore: 0.85
    SubCategory: 
    Entity: Paul Allen
    Category: Person
    Offset: 40
    ConfidenceScore: 0.9
    SubCategory: 
```

Du kan också använda åtgärden Analysera för att identifiera extrahering av PII och nyckelfraser. Se Exemplet [Analysera](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/textanalytics/Azure.AI.TextAnalytics/samples) på GitHub.

# <a name="version-30"></a>[Version 3.0](#tab/version-3)

Den här funktionen är inte tillgänglig i version 3.0.

---
