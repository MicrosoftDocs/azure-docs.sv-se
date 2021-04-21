---
title: 'Snabbstart: Textanalys v3-klientbibliotek för Java-| Microsoft Docs'
description: Kom igång med v3 Textanalys klientbiblioteket för Java.
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 04/19/2021
ms.custom: devx-track-java
ms.author: aahi
ms.reviewer: tasharm, assafi, sumeh
ms.openlocfilehash: d39f40d4059b43246f523b4d01ca92c0360bc574
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107765118"
---
<a name="HOLTop"></a>

# <a name="version-31-preview"></a>[Förhandsversion 3.1](#tab/version-3-1)

[Referensdokumentation](/java/api/overview/azure/ai-textanalytics-readme?preserve-view=true&view=azure-java-preview)  |  [Bibliotekskällkod](https://github.com/Azure/azure-sdk-for-java/blob/azure-ai-textanalytics_5.1.0-beta.5/sdk/textanalytics/azure-ai-textanalytics)  |  [Paket](https://mvnrepository.com/artifact/com.azure/azure-ai-textanalytics/5.1.0-beta.5)  |  [Exempel](https://github.com/Azure/azure-sdk-for-java/tree/azure-ai-textanalytics_5.1.0-beta.5/sdk/textanalytics/azure-ai-textanalytics/src/samples/java/com/azure/ai/textanalytics)

# <a name="version-30"></a>[Version 3.0](#tab/version-3)

[Referensdokumentation](/java/api/overview/azure/ai-textanalytics-readme)  |  [Bibliotekskällkod](https://github.com/Azure/azure-sdk-for-java/blob/azure-ai-textanalytics_5.0.0/sdk/textanalytics/azure-ai-textanalytics)  |  [Paket](https://mvnrepository.com/artifact/com.azure/azure-ai-textanalytics/5.0.0)  |  [Exempel](https://github.com/Azure/azure-sdk-for-java/tree/azure-ai-textanalytics_5.0.0/sdk/textanalytics/azure-ai-textanalytics/src/samples/java/com/azure/ai/textanalytics)

---

## <a name="prerequisites"></a>Förutsättningar

* Azure-prenumeration [– Skapa en utan kostnad](https://azure.microsoft.com/free/cognitive-services)
* [Java Development Kit](https://www.oracle.com/technetwork/java/javase/downloads/index.html) (JDK) med version 8 eller senare
* När du har din Azure-prenumeration skapar du en Textanalys-resurs för att skapa Textanalys resurs i Azure Portal för att <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics"  title=" hämta din nyckel och "  target="_blank"> </a> slutpunkt.  När den har distribuerats klickar du **på Gå till resurs**.
    * Du behöver nyckeln och slutpunkten från den resurs som du skapar för att ansluta ditt program till Textanalys-API:et. Du klistrar in nyckeln och slutpunkten i koden nedan senare i snabbstarten.
    * Du kan använda den kostnadsfria prisnivån ( `F0` ) för att prova tjänsten och senare uppgradera till en betald nivå för produktion.
* Om du vill använda funktionen Analysera behöver du Textanalys resurs med prisnivån standard (S).

## <a name="setting-up"></a>Inrätta

### <a name="add-the-client-library"></a>Lägga till klientbiblioteket

# <a name="version-31-preview"></a>[Förhandsversion 3.1](#tab/version-3-1)

Skapa ett Maven-projekt i önskad IDE- eller utvecklingsmiljö. Lägg sedan till följande beroende till projektets *pom.xml* fil. Du hittar implementeringssyntaxen [för andra byggverktyg](https://mvnrepository.com/artifact/com.azure/azure-ai-textanalytics/5.1.0-beta.5) online.

```xml
<dependencies>
     <dependency>
        <groupId>com.azure</groupId>
        <artifactId>azure-ai-textanalytics</artifactId>
        <version>5.1.0-beta.5</version>
    </dependency>
</dependencies>
```

# <a name="version-30"></a>[Version 3.0](#tab/version-3)

Skapa ett Maven-projekt i önskad IDE eller utvecklingsmiljö. Lägg sedan till följande beroende till projektets *pom.xml* fil. Du hittar implementeringssyntaxen [för andra byggverktyg](https://mvnrepository.com/artifact/com.azure/azure-ai-textanalytics/5.0.0) online.

```xml
<dependencies>
     <dependency>
        <groupId>com.azure</groupId>
        <artifactId>azure-ai-textanalytics</artifactId>
        <version>5.0.0</version>
    </dependency>
</dependencies>
```

> [!TIP]
> Vill du visa hela snabbstartskodfilen samtidigt? Du hittar den på [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/java/TextAnalytics/TextAnalyticsSamples.java), som innehåller kodexe exemplen i den här snabbstarten. 

---

Skapa en Java-fil med namnet `TextAnalyticsSamples.java` . Öppna filen och lägg till följande `import` -instruktioner:

```java
import com.azure.core.credential.AzureKeyCredential;
import com.azure.ai.textanalytics.models.*;
import com.azure.ai.textanalytics.TextAnalyticsClientBuilder;
import com.azure.ai.textanalytics.TextAnalyticsClient;
```

I Java-filen lägger du till en ny klass och lägger till azure-resursens nyckel och slutpunkt enligt nedan.

[!INCLUDE [text-analytics-find-resource-information](../find-azure-resource-info.md)]

```java
public class TextAnalyticsSamples {
    private static String KEY = "<replace-with-your-text-analytics-key-here>";
    private static String ENDPOINT = "<replace-with-your-text-analytics-endpoint-here>";
}
```

Lägg till följande main-metod i klassen . Du definierar metoderna som anropas här senare.

# <a name="version-31-preview"></a>[Version 3.1 (förhandsversion)](#tab/version-3-1)

```java
public static void main(String[] args) {
    //You will create these methods later in the quickstart.
    TextAnalyticsClient client = authenticateClient(KEY, ENDPOINT);

    sentimentAnalysisWithOpinionMiningExample(client)
    detectLanguageExample(client);
    recognizeEntitiesExample(client);
    recognizeLinkedEntitiesExample(client);
    recognizePiiEntitiesExample(client);
    extractKeyPhrasesExample(client);
}
```

# <a name="version-30"></a>[Version 3.0](#tab/version-3)

```java
public static void main(String[] args) {
    //You will create these methods later in the quickstart.
    TextAnalyticsClient client = authenticateClient(KEY, ENDPOINT);

    sentimentAnalysisExample(client);
    detectLanguageExample(client);
    recognizeEntitiesExample(client);
    recognizeLinkedEntitiesExample(client);
    extractKeyPhrasesExample(client);
        AnalyzeOperationExample(client)
}
```

---


## <a name="object-model"></a>Objektmodell

Klienten Textanalys är ett objekt som autentiserar till Azure med din nyckel och tillhandahåller funktioner för att acceptera text som enkla `TextAnalyticsClient` strängar eller som en batch. Du kan skicka text till API:et synkront eller asynkront. Svarsobjektet innehåller analysinformationen för varje dokument som du skickar. 

## <a name="code-examples"></a>Kodexempel

* [Autentisera klienten](#authenticate-the-client)
* [Attitydanalys](#sentiment-analysis) 
* [Yttrandeutvinning](#opinion-mining)
* [Språkidentifiering](#language-detection)
* [Igenkänning av namngiven entitet](#named-entity-recognition-ner)
* [Entitetslänkning](#entity-linking)
* [Extrahering av nyckelfraser](#key-phrase-extraction)

## <a name="authenticate-the-client"></a>Autentisera klienten

Skapa en metod för att `TextAnalyticsClient` instansiera objektet med nyckeln och slutpunkten för Textanalys resurs. Det här exemplet är detsamma för versionerna 3.0 och 3.1 av API:et.

```java
static TextAnalyticsClient authenticateClient(String key, String endpoint) {
    return new TextAnalyticsClientBuilder()
        .credential(new AzureKeyCredential(key))
        .endpoint(endpoint)
        .buildClient();
}
```


I programmets -metod `main()` anropar du autentiseringsmetoden för att instansiera klienten.

## <a name="sentiment-analysis"></a>Attitydanalys

# <a name="version-31-preview"></a>[Förhandsversion 3.1](#tab/version-3-1)

> [!NOTE]
> I version `3.1` :
> * Attitydanalys innehåller En Analys av Yttrande Mining, som är en valfri flagga. 
> * Yttrandeutvinning innehåller attityder på aspekt- och yttrandenivå. 

Skapa en ny funktion med `sentimentAnalysisExample()` namnet som tar klienten som du skapade tidigare och anropa dess `analyzeSentiment()` funktion. Det `AnalyzeSentimentResult` returnerade objektet innehåller `documentSentiment` och `sentenceSentiments` om det lyckas, eller om `errorMessage` inte. 

```java
static void sentimentAnalysisExample(TextAnalyticsClient client)
{
    // The text that need be analyzed.
    String text = "I had the best day of my life. I wish you were there with me.";

    DocumentSentiment documentSentiment = client.analyzeSentiment(text);
    System.out.printf(
        "Recognized document sentiment: %s, positive score: %s, neutral score: %s, negative score: %s.%n",
        documentSentiment.getSentiment(),
        documentSentiment.getConfidenceScores().getPositive(),
        documentSentiment.getConfidenceScores().getNeutral(),
        documentSentiment.getConfidenceScores().getNegative());

    for (SentenceSentiment sentenceSentiment : documentSentiment.getSentences()) {
        System.out.printf(
            "Recognized sentence sentiment: %s, positive score: %s, neutral score: %s, negative score: %s.%n",
            sentenceSentiment.getSentiment(),
            sentenceSentiment.getConfidenceScores().getPositive(),
            sentenceSentiment.getConfidenceScores().getNeutral(),
            sentenceSentiment.getConfidenceScores().getNegative());
        }
    }
}
```

### <a name="output"></a>Utdata

```console
Recognized document sentiment: positive, positive score: 1.0, neutral score: 0.0, negative score: 0.0.
Recognized sentence sentiment: positive, positive score: 1.0, neutral score: 0.0, negative score: 0.0.
Recognized sentence sentiment: neutral, positive score: 0.21, neutral score: 0.77, negative score: 0.02.
```

### <a name="opinion-mining"></a>Yttrandeutvinning

Om du vill utföra attitydanalys med yttrandeutvinning skapar du en ny funktion med namnet som tar klienten som du skapade tidigare och anropar dess funktion `sentimentAnalysisWithOpinionMiningExample()` `analyzeSentiment()` med inställningsalternativobjektet `AnalyzeSentimentOptions` . Det `AnalyzeSentimentResult` returnerade objektet innehåller `documentSentiment` och `sentenceSentiments` om det lyckas, eller om `errorMessage` inte. 


```java
 static void sentimentAnalysisWithOpinionMiningExample(TextAnalyticsClient client)
 {
     // The document that needs be analyzed.
     String document = "Bad atmosphere. Not close to plenty of restaurants, hotels, and transit! Staff are not friendly and helpful.";

     System.out.printf("Document = %s%n", document);

     AnalyzeSentimentOptions options = new AnalyzeSentimentOptions().setIncludeOpinionMining(true);
     final DocumentSentiment documentSentiment = client.analyzeSentiment(document, "en", options);
     SentimentConfidenceScores scores = documentSentiment.getConfidenceScores();
     System.out.printf(
             "Recognized document sentiment: %s, positive score: %f, neutral score: %f, negative score: %f.%n",
             documentSentiment.getSentiment(), scores.getPositive(), scores.getNeutral(), scores.getNegative());


     documentSentiment.getSentences().forEach(sentenceSentiment -> {
         SentimentConfidenceScores sentenceScores = sentenceSentiment.getConfidenceScores();
         System.out.printf("\tSentence sentiment: %s, positive score: %f, neutral score: %f, negative score: %f.%n",
                 sentenceSentiment.getSentiment(), sentenceScores.getPositive(), sentenceScores.getNeutral(), sentenceScores.getNegative());
         sentenceSentiment.getOpinions().forEach(opinion -> {
             TargetSentiment targetSentiment = opinion.getTarget();
             System.out.printf("\t\tTarget sentiment: %s, target text: %s%n", targetSentiment.getSentiment(),
                     targetSentiment.getText());
             for (AssessmentSentiment assessmentSentiment : opinion.getAssessments()) {
                 System.out.printf("\t\t\t'%s' assessment sentiment because of \"%s\". Is the assessment negated: %s.%n",
                         assessmentSentiment.getSentiment(), assessmentSentiment.getText(), assessmentSentiment.isNegated());
             }
         });
     });
 }
```

### <a name="output"></a>Utdata

```console
Document = Bad atmosphere. Not close to plenty of restaurants, hotels, and transit! Staff are not friendly and helpful.
Recognized document sentiment: negative, positive score: 0.010000, neutral score: 0.140000, negative score: 0.850000.
    Sentence sentiment: negative, positive score: 0.000000, neutral score: 0.000000, negative score: 1.000000.
        Target sentiment: negative, target text: atmosphere
            'negative' assessment sentiment because of "bad". Is the assessment negated: false.
    Sentence sentiment: negative, positive score: 0.020000, neutral score: 0.440000, negative score: 0.540000.
    Sentence sentiment: negative, positive score: 0.000000, neutral score: 0.000000, negative score: 1.000000.
        Target sentiment: negative, target text: Staff
            'negative' assessment sentiment because of "friendly". Is the assessment negated: true.
            'negative' assessment sentiment because of "helpful". Is the assessment negated: true.
```

# <a name="version-30"></a>[Version 3.0](#tab/version-3)

Skapa en ny funktion med `sentimentAnalysisExample()` namnet som tar klienten som du skapade tidigare och anropa dess `analyzeSentiment()` funktion. Det `AnalyzeSentimentResult` returnerade objektet innehåller `documentSentiment` och `sentenceSentiments` om det lyckas, eller om `errorMessage` inte. 

```java
static void sentimentAnalysisExample(TextAnalyticsClient client)
{
    // The text that need be analyzed.
    String text = "I had the best day of my life. I wish you were there with me.";

    DocumentSentiment documentSentiment = client.analyzeSentiment(text);
    System.out.printf(
        "Recognized document sentiment: %s, positive score: %s, neutral score: %s, negative score: %s.%n",
        documentSentiment.getSentiment(),
        documentSentiment.getConfidenceScores().getPositive(),
        documentSentiment.getConfidenceScores().getNeutral(),
        documentSentiment.getConfidenceScores().getNegative());

    for (SentenceSentiment sentenceSentiment : documentSentiment.getSentences()) {
        System.out.printf(
            "Recognized sentence sentiment: %s, positive score: %s, neutral score: %s, negative score: %s.%n",
            sentenceSentiment.getSentiment(),
            sentenceSentiment.getConfidenceScores().getPositive(),
            sentenceSentiment.getConfidenceScores().getNeutral(),
            sentenceSentiment.getConfidenceScores().getNegative());
        }
    }
}
```

### <a name="output"></a>Utdata

```console
Recognized document sentiment: positive, positive score: 1.0, neutral score: 0.0, negative score: 0.0.
Recognized sentence sentiment: positive, positive score: 1.0, neutral score: 0.0, negative score: 0.0.
Recognized sentence sentiment: neutral, positive score: 0.21, neutral score: 0.77, negative score: 0.02.
```

---

## <a name="language-detection"></a>Språkidentifiering

Skapa en ny funktion med `detectLanguageExample()` namnet som tar klienten som du skapade tidigare och anropa dess `detectLanguage()` funktion. Det `DetectLanguageResult` returnerade objektet innehåller ett primärt språk som identifierats, en lista över andra språk som identifierats om det lyckades eller ett `errorMessage` om inte. Det här exemplet är detsamma för versionerna 3.0 och 3.1 av API:et.

> [!Tip]
> I vissa fall kan det vara svårt att disambiuera språk baserat på indata. Du kan använda `countryHint` parametern för att ange en landskod med två bokstäver. Som standard använder API:et "US" som standard countryHint. Om du vill ta bort det här beteendet kan du återställa den här parametern genom att ange värdet till tom sträng `countryHint = ""` . Om du vill ange ett annat `TextAnalyticsClientOptions.DefaultCountryHint` standardvärde anger du egenskapen och skickar den under klientens initiering.

```java
static void detectLanguageExample(TextAnalyticsClient client)
{
    // The text that need be analyzed.
    String text = "Ce document est rédigé en Français.";

    DetectedLanguage detectedLanguage = client.detectLanguage(text);
    System.out.printf("Detected primary language: %s, ISO 6391 name: %s, score: %.2f.%n",
        detectedLanguage.getName(),
        detectedLanguage.getIso6391Name(),
        detectedLanguage.getConfidenceScore());
}
```

### <a name="output"></a>Utdata

```console
Detected primary language: French, ISO 6391 name: fr, score: 1.00.
```

## <a name="named-entity-recognition-ner"></a>Igenkänning av namngiven entitet (NER)

# <a name="version-31-preview"></a>[Förhandsversion 3.1](#tab/version-3-1)

> [!NOTE]
> I version `3.1` :
> * NER innehåller separata metoder för att identifiera personlig information. 
> * Entitetslänkning är en separat begäran än NER.

Skapa en ny funktion med `recognizeEntitiesExample()` namnet som tar klienten som du skapade tidigare och anropa dess `recognizeEntities()` funktion. Det `CategorizedEntityCollection` returnerade objektet innehåller en lista över `CategorizedEntity` om det lyckas, eller om `errorMessage` inte.

```java
static void recognizeEntitiesExample(TextAnalyticsClient client)
{
    // The text that need be analyzed.
    String text = "I had a wonderful trip to Seattle last week.";

    for (CategorizedEntity entity : client.recognizeEntities(text)) {
        System.out.printf(
            "Recognized entity: %s, entity category: %s, entity sub-category: %s, score: %s, offset: %s, length: %s.%n",
            entity.getText(),
            entity.getCategory(),
            entity.getSubcategory(),
            entity.getConfidenceScore(),
            entity.getOffset(),
            entity.getLength());
    }
}
```

### <a name="output"></a>Utdata

```console
Recognized entity: trip, entity category: Event, entity sub-category: null, score: 0.61, offset: 8, length: 4.
Recognized entity: Seattle, entity category: Location, entity sub-category: GPE, score: 0.82, offset: 16, length: 7.
Recognized entity: last week, entity category: DateTime, entity sub-category: DateRange, score: 0.8, offset: 24, length: 9.
```

### <a name="entity-linking"></a>Entitetslänkning

Skapa en ny funktion med `recognizeLinkedEntitiesExample()` namnet som tar klienten som du skapade tidigare och anropa dess `recognizeLinkedEntities()` funktion. Det `LinkedEntityCollection` returnerade objektet innehåller en lista över `LinkedEntity` om det lyckas, eller om `errorMessage` inte. Eftersom länkade entiteter identifieras unikt grupperas förekomster av samma entitet under ett `LinkedEntity` objekt som en lista med `LinkedEntityMatch` objekt.


```java
static void recognizeLinkedEntitiesExample(TextAnalyticsClient client)
{
    // The text that need be analyzed.
    String text = "Microsoft was founded by Bill Gates and Paul Allen on April 4, 1975, " +
        "to develop and sell BASIC interpreters for the Altair 8800. " +
        "During his career at Microsoft, Gates held the positions of chairman, " +
        "chief executive officer, president and chief software architect, " +
        "while also being the largest individual shareholder until May 2014.";

    System.out.printf("Linked Entities:%n");
    for (LinkedEntity linkedEntity : client.recognizeLinkedEntities(text)) {
        System.out.printf("Name: %s, ID: %s, URL: %s, Data Source: %s.%n",
            linkedEntity.getName(),
            linkedEntity.getDataSourceEntityId(),
            linkedEntity.getUrl(),
            linkedEntity.getDataSource());
        System.out.printf("Matches:%n");
        for (LinkedEntityMatch linkedEntityMatch : linkedEntity.getMatches()) {
            System.out.printf("Text: %s, Score: %.2f, Offset: %s, Length: %s%n",
            linkedEntityMatch.getText(),
            linkedEntityMatch.getConfidenceScore(),
            linkedEntityMatch.getOffset(),
            linkedEntityMatch.getLength());
        }
    }
}
```

### <a name="output"></a>Utdata

```console
Linked Entities:
Name: Microsoft, ID: Microsoft, URL: https://en.wikipedia.org/wiki/Microsoft, Data Source: Wikipedia.
Matches:
Text: Microsoft, Score: 0.55, Offset: 9, Length: 0
Text: Microsoft, Score: 0.55, Offset: 9, Length: 150
Name: Bill Gates, ID: Bill Gates, URL: https://en.wikipedia.org/wiki/Bill_Gates, Data Source: Wikipedia.
Matches:
Text: Bill Gates, Score: 0.63, Offset: 10, Length: 25
Text: Gates, Score: 0.63, Offset: 5, Length: 161
Name: Paul Allen, ID: Paul Allen, URL: https://en.wikipedia.org/wiki/Paul_Allen, Data Source: Wikipedia.
Matches:
Text: Paul Allen, Score: 0.60, Offset: 10, Length: 40
Name: April 4, ID: April 4, URL: https://en.wikipedia.org/wiki/April_4, Data Source: Wikipedia.
Matches:
Text: April 4, Score: 0.32, Offset: 7, Length: 54
Name: BASIC, ID: BASIC, URL: https://en.wikipedia.org/wiki/BASIC, Data Source: Wikipedia.
Matches:
Text: BASIC, Score: 0.33, Offset: 5, Length: 89
Name: Altair 8800, ID: Altair 8800, URL: https://en.wikipedia.org/wiki/Altair_8800, Data Source: Wikipedia.
Matches:
Text: Altair 8800, Score: 0.88, Offset: 11, Length: 116
```


### <a name="personally-identifiable-information-recognition"></a>Personligt identifierbar informationsigenkänning

Skapa en ny funktion med `recognizePiiEntitiesExample()` namnet som tar klienten som du skapade tidigare och anropa dess `recognizePiiEntities()` funktion. Det `PiiEntityCollection` returnerade objektet innehåller en lista över `PiiEntity` om det lyckas, eller om `errorMessage` inte. Den innehåller också den redakterade texten, som består av indatatexten med alla identifierbara entiteter ersatta med `*****` .

```java
static void recognizePiiEntitiesExample(TextAnalyticsClient client)
{
    // The text that need be analyzed.
    String document = "My SSN is 859-98-0987";
    PiiEntityCollection piiEntityCollection = client.recognizePiiEntities(document);
    System.out.printf("Redacted Text: %s%n", piiEntityCollection.getRedactedText());
    piiEntityCollection.forEach(entity -> System.out.printf(
        "Recognized Personally Identifiable Information entity: %s, entity category: %s, entity subcategory: %s,"
            + " confidence score: %f.%n",
        entity.getText(), entity.getCategory(), entity.getSubcategory(), entity.getConfidenceScore()));
}
```

### <a name="output"></a>Utdata

```console
Redacted Text: My SSN is ***********
Recognized Personally Identifiable Information entity: 859-98-0987, entity category: U.S. Social Security Number (SSN), entity subcategory: null, confidence score: 0.650000.
```

# <a name="version-30"></a>[Version 3.0](#tab/version-3)

> [!NOTE]
> I version `3.0` :
> * NER innehåller separata metoder för att identifiera personlig information. 
> * Entitetslänkning är en separat begäran än NER.

Skapa en ny funktion med `recognizeEntitiesExample()` namnet som tar klienten som du skapade tidigare och anropa dess `recognizeEntities()` funktion. Det `CategorizedEntityCollection` returnerade objektet innehåller en lista över `CategorizedEntity` om det lyckas, eller om `errorMessage` inte.

```java
static void recognizeEntitiesExample(TextAnalyticsClient client)
{
    // The text that need be analyzed.
    String text = "I had a wonderful trip to Seattle last week.";

    for (CategorizedEntity entity : client.recognizeEntities(text)) {
        System.out.printf(
            "Recognized entity: %s, entity category: %s, entity sub-category: %s, score: %s.%n",
            entity.getText(),
            entity.getCategory(),
            entity.getSubcategory(),
            entity.getConfidenceScore());
    }
}
```

### <a name="output"></a>Utdata

```console
Recognized entity: trip, entity category: Event, entity sub-category: null, score: 0.61.
Recognized entity: Seattle, entity category: Location, entity sub-category: GPE, score: 0.82.
Recognized entity: last week, entity category: DateTime, entity sub-category: DateRange, score: 0.8.
```

### <a name="entity-linking"></a>Entitetslänkning

Skapa en ny funktion med `recognizeLinkedEntitiesExample()` namnet som tar klienten som du skapade tidigare och anropa dess `recognizeLinkedEntities()` funktion. Det `LinkedEntityCollection` returnerade objektet innehåller en lista över `LinkedEntity` om det lyckas, eller om `errorMessage` inte. Eftersom länkade entiteter identifieras unikt grupperas förekomster av samma entitet under ett `LinkedEntity` objekt som en lista med `LinkedEntityMatch` objekt.

```java
static void recognizeLinkedEntitiesExample(TextAnalyticsClient client)
{
    // The text that need be analyzed.
    String text = "Microsoft was founded by Bill Gates and Paul Allen on April 4, 1975, " +
        "to develop and sell BASIC interpreters for the Altair 8800. " +
        "During his career at Microsoft, Gates held the positions of chairman, " +
        "chief executive officer, president and chief software architect, " +
        "while also being the largest individual shareholder until May 2014.";

    System.out.printf("Linked Entities:%n");
    for (LinkedEntity linkedEntity : client.recognizeLinkedEntities(text)) {
        System.out.printf("Name: %s, ID: %s, URL: %s, Data Source: %s.%n",
            linkedEntity.getName(),
            linkedEntity.getDataSourceEntityId(),
            linkedEntity.getUrl(),
            linkedEntity.getDataSource());
        System.out.printf("Matches:%n");
        for (LinkedEntityMatch linkedEntityMatch : linkedEntity.getMatches()) {
            System.out.printf("Text: %s, Score: %.2f%n",
            linkedEntityMatch.getText(),
            linkedEntityMatch.getConfidenceScore());
        }
    }
}
```

### <a name="output"></a>Utdata

```console
Linked Entities:
Name: Altair 8800, ID: Altair 8800, URL: https://en.wikipedia.org/wiki/Altair_8800, Data Source: Wikipedia.
Matches:
Text: Altair 8800, Score: 0.88
Name: Bill Gates, ID: Bill Gates, URL: https://en.wikipedia.org/wiki/Bill_Gates, Data Source: Wikipedia.
Matches:
Text: Bill Gates, Score: 0.63
Text: Gates, Score: 0.63
Name: Paul Allen, ID: Paul Allen, URL: https://en.wikipedia.org/wiki/Paul_Allen, Data Source: Wikipedia.
Matches:
Text: Paul Allen, Score: 0.60
Name: Microsoft, ID: Microsoft, URL: https://en.wikipedia.org/wiki/Microsoft, Data Source: Wikipedia.
Matches:
Text: Microsoft, Score: 0.55
Text: Microsoft, Score: 0.55
Name: April 4, ID: April 4, URL: https://en.wikipedia.org/wiki/April_4, Data Source: Wikipedia.
Matches:
Text: April 4, Score: 0.32
Name: BASIC, ID: BASIC, URL: https://en.wikipedia.org/wiki/BASIC, Data Source: Wikipedia.
Matches:
Text: BASIC, Score: 0.33
```

---

## <a name="key-phrase-extraction"></a>Extrahering av nyckelfraser

Skapa en ny funktion med `extractKeyPhrasesExample()` namnet som tar klienten som du skapade tidigare och anropa dess `extractKeyPhrases()` funktion. Det `ExtractKeyPhraseResult` returnerade objektet innehåller en lista över nyckelfraser om det lyckas, eller om `errorMessage` inte. Det här exemplet är detsamma för version 3.0 och 3.1 av API:et.

```java
static void extractKeyPhrasesExample(TextAnalyticsClient client)
{
    // The text that need be analyzed.
    String text = "My cat might need to see a veterinarian.";

    System.out.printf("Recognized phrases: %n");
    for (String keyPhrase : client.extractKeyPhrases(text)) {
        System.out.printf("%s%n", keyPhrase);
    }
}
```

### <a name="output"></a>Utdata

```console
Recognized phrases: 
cat
veterinarian
```
---

## <a name="use-the-api-asynchronously-with-the-analyze-operation"></a>Använda API:et asynkront med åtgärden Analysera

# <a name="version-31-preview"></a>[Förhandsversion 3.1](#tab/version-3-1)

[!INCLUDE [Analyze Batch Action pricing](../analyze-operation-pricing-caution.md)]

Skapa en ny funktion med `analyzeBatchActionsExample()` namnet , som anropar funktionen `beginAnalyzeBatchActions()` . Resultatet blir en långvarig åtgärd som avsöks efter resultat.

```java
static void analyzeBatchActionsExample(TextAnalyticsClient client)
{
        List<TextDocumentInput> documents = Arrays.asList(
                        new TextDocumentInput("0", "Microsoft was founded by Bill Gates and Paul Allen.")
                        );

        
        SyncPoller<AnalyzeBatchActionsOperationDetail, PagedIterable<AnalyzeBatchActionsResult>> syncPoller =
                client.beginAnalyzeBatchActions(documents,
                        new TextAnalyticsActions().setDisplayName("Analyze Batch Actions Quickstart")
                                .setRecognizeEntitiesOptions(new RecognizeEntitiesOptions()),
                        new AnalyzeBatchActionsOptions().setIncludeStatistics(false),
                        Context.NONE);

        // Task operation statistics
        while (syncPoller.poll().getStatus() == LongRunningOperationStatus.IN_PROGRESS) {
            final AnalyzeBatchActionsOperationDetail operationResult = syncPoller.poll().getValue();
            System.out.printf("Action display name: %s, Successfully completed actions: %d, in-process actions: %d, failed actions: %d, total actions: %d%n",
                    operationResult.getDisplayName(), operationResult.getActionsSucceeded(),
                    operationResult.getActionsInProgress(), operationResult.getActionsFailed(),
                    operationResult.getActionsInTotal());
        }

        syncPoller.waitForCompletion();

        Iterable<PagedResponse<AnalyzeBatchActionsResult>> pagedResults = syncPoller.getFinalResult().iterableByPage();
        for (PagedResponse<AnalyzeBatchActionsResult> page : pagedResults) {
            System.out.printf("Response code: %d, Continuation Token: %s.%n", page.getStatusCode(), page.getContinuationToken());
            page.getElements().forEach(analyzeBatchActionsResult -> {
                System.out.println("Entities recognition action results:");
                IterableStream<RecognizeEntitiesActionResult> recognizeEntitiesActionResults =
                        analyzeBatchActionsResult.getRecognizeEntitiesActionResults();
                if (recognizeEntitiesActionResults != null) {
                    recognizeEntitiesActionResults.forEach(actionResult -> {
                        if (!actionResult.isError()) {
                            // Recognized entities for each of documents from a batch of documents
                            AtomicInteger counter = new AtomicInteger();
                            for (RecognizeEntitiesResult documentResult : actionResult.getResult()) {
                                System.out.printf("%n%s%n", documents.get(counter.getAndIncrement()));
                                if (documentResult.isError()) {
                                    // Erroneous document
                                    System.out.printf("Cannot recognize entities. Error: %s%n",
                                            documentResult.getError().getMessage());
                                } else {
                                    // Valid document
                                    documentResult.getEntities().forEach(entity -> System.out.printf(
                                            "Recognized entity: %s, entity category: %s, entity subcategory: %s, confidence score: %f.%n",
                                            entity.getText(), entity.getCategory(), entity.getSubcategory(), entity.getConfidenceScore()));
                                }
                            }
                        } else {
                            TextAnalyticsError actionError = actionResult.getError();
                            // Erroneous action
                            System.out.printf("Cannot execute Entities Recognition action. Error: %s%n", actionError.getMessage());
                        }
                    });
                }
            });
        }
    }
```

När du har lagt till det här exemplet i programmet anropar du det i metoden `main()` .

```java
analyzeBatchActionsExample(client);
```

### <a name="output"></a>Utdata

```console
Action display name: Analyze Batch Actions Quickstart, Successfully completed actions: 0, in-process actions: 1, failed actions: 0, total actions: 1
Response code: 200, Continuation Token: null.
Entities recognition action results:

Text = Microsoft was founded by Bill Gates and Paul Allen., Id = 0, Language = null
Recognized entity: Microsoft, entity category: Organization, entity subcategory: null, confidence score: 0.970000.
Recognized entity: Bill Gates, entity category: Person, entity subcategory: null, confidence score: 1.000000.
Recognized entity: Paul Allen, entity category: Person, entity subcategory: null, confidence score: 0.990000.
```

Du kan också använda åtgärden Analysera för att identifiera PII, identifiera länkade entiteter och extrahering av nyckelfraser. Se exemplet [Analysera](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/textanalytics/azure-ai-textanalytics/src/samples/java/com/azure/ai/textanalytics/lro) på GitHub.

# <a name="version-30"></a>[Version 3.0](#tab/version-3)

Den här funktionen är inte tillgänglig i version 3.0.

---
