---
title: 'Snabb start: Textanalys v3-klient bibliotek för Java | Microsoft Docs'
description: Kom igång med v3-Textanalys klient biblioteket för Java.
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 12/11/2020
ms.custom: devx-track-java
ms.author: aahi
ms.reviewer: tasharm, assafi, sumeh
ms.openlocfilehash: 33d5989103911d6299806b16979d1caeba286a76
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/28/2021
ms.locfileid: "98947505"
---
<a name="HOLTop"></a>

# <a name="version-31-preview"></a>[Version 3,1 Preview](#tab/version-3-1)

[Referens dokumentation](/java/api/overview/azure/ai-textanalytics-readme)  |  [Biblioteks käll kod](https://github.com/Azure/azure-sdk-for-java/blob/azure-ai-textanalytics_5.1.0-beta.3/sdk/textanalytics/azure-ai-textanalytics)  |  [Paket](https://mvnrepository.com/artifact/com.azure/azure-ai-textanalytics/5.1.0-beta.3)  |  [Exempel](https://github.com/Azure/azure-sdk-for-java/tree/azure-ai-textanalytics_5.1.0-beta.3/sdk/textanalytics/azure-ai-textanalytics/src/samples/java/com/azure/ai/textanalytics)

# <a name="version-30"></a>[Version 3,0](#tab/version-3)

[Referens dokumentation](/java/api/overview/azure/ai-textanalytics-readme)  |  [Biblioteks käll kod](https://github.com/Azure/azure-sdk-for-java/blob/azure-ai-textanalytics_5.0.0/sdk/textanalytics/azure-ai-textanalytics)  |  [Paket](https://mvnrepository.com/artifact/com.azure/azure-ai-textanalytics/5.0.0)  |  [Exempel](https://github.com/Azure/azure-sdk-for-java/tree/azure-ai-textanalytics_5.0.0/sdk/textanalytics/azure-ai-textanalytics/src/samples/java/com/azure/ai/textanalytics)

# <a name="version-21"></a>[Version 2,1](#tab/version-2)

Den här artikeln beskriver endast version 3. x av API: et.

---

## <a name="prerequisites"></a>Förutsättningar

* Azure-prenumeration – [skapa en kostnads fritt](https://azure.microsoft.com/free/cognitive-services)
* [Java Development Kit](https://www.oracle.com/technetwork/java/javase/downloads/index.html) (JDK) med version 8 eller senare
* När du har en Azure-prenumeration <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics"  title=" skapar du en textanalys resurs "  target="_blank"> skapa en textanalys resurs <span class="docon docon-navigate-external x-hidden-focus"></span> </a> i Azure Portal för att hämta din nyckel och slut punkt.  När den har distribuerats klickar **du på gå till resurs**.
    * Du behöver nyckeln och slut punkten från den resurs som du skapar för att ansluta ditt program till API för textanalys. Du klistrar in nyckeln och slut punkten i koden nedan i snabb starten.
    * Du kan använda den kostnads fria pris nivån ( `F0` ) för att testa tjänsten och senare uppgradera till en betald nivå för produktion.
* Om du vill använda funktionen analysera behöver du en Textanalys resurs med pris nivån standard (S).

## <a name="setting-up"></a>Konfigurera

### <a name="add-the-client-library"></a>Lägg till klient biblioteket

# <a name="version-31-preview"></a>[Version 3,1 Preview](#tab/version-3-1)

Skapa ett Maven-projekt i önskad IDE-eller utvecklings miljö. Lägg sedan till följande beroende till projektets *pom.xml* -fil. Du kan hitta implementations-syntaxen [för andra build-verktyg](https://mvnrepository.com/artifact/com.azure/azure-ai-textanalytics/5.1.0-beta.1) online.

```xml
<dependencies>
     <dependency>
        <groupId>com.azure</groupId>
        <artifactId>azure-ai-textanalytics</artifactId>
        <version>5.1.0-beta.3</version>
    </dependency>
</dependencies>
```

# <a name="version-30"></a>[Version 3,0](#tab/version-3)

Skapa ett Maven-projekt i önskad IDE-eller utvecklings miljö. Lägg sedan till följande beroende till projektets *pom.xml* -fil. Du kan hitta implementations-syntaxen [för andra build-verktyg](https://mvnrepository.com/artifact/com.azure/azure-ai-textanalytics/5.0.0) online.

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
> Vill du Visa hela snabb starts kod filen samtidigt? Du kan hitta den [på GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/java/TextAnalytics/TextAnalyticsSamples.java), som innehåller kod exemplen i den här snabb starten. 

# <a name="version-21"></a>[Version 2,1](#tab/version-2)

Den här artikeln beskriver endast version 3. x av API: et.

---

Skapa en Java-fil med namnet `TextAnalyticsSamples.java` . Öppna filen och Lägg till följande- `import` uttryck:

```java
import com.azure.core.credential.AzureKeyCredential;
import com.azure.ai.textanalytics.models.*;
import com.azure.ai.textanalytics.TextAnalyticsClientBuilder;
import com.azure.ai.textanalytics.TextAnalyticsClient;
```

I Java-filen lägger du till en ny klass och lägger till Azure-resursens nyckel och slut punkt enligt nedan.

[!INCLUDE [text-analytics-find-resource-information](../find-azure-resource-info.md)]

```java
public class TextAnalyticsSamples {
    private static String KEY = "<replace-with-your-text-analytics-key-here>";
    private static String ENDPOINT = "<replace-with-your-text-analytics-endpoint-here>";
}
```

Lägg till följande huvud metod i-klassen. Du definierar de metoder som anropas här senare.

# <a name="version-31-preview"></a>[Version 3,1 (för hands version)](#tab/version-3-1)

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

# <a name="version-30"></a>[Version 3,0](#tab/version-3)

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

# <a name="version-21"></a>[Version 2,1](#tab/version-2)

Den här artikeln beskriver endast version 3. x av API: et.

---


## <a name="object-model"></a>Objekt modell

Textanalys-klienten är ett `TextAnalyticsClient` objekt som autentiserar till Azure med hjälp av din nyckel och ger funktioner för att acceptera text som enkla strängar eller som en batch. Du kan skicka text till API: et synkront eller asynkront. Objektet Response kommer att innehålla analys informationen för varje dokument som du skickar. 

## <a name="code-examples"></a>Kodexempel

* [Autentisera klienten](#authenticate-the-client)
* [Attitydanalys](#sentiment-analysis) 
* [Åsikts utvinning](#opinion-mining)
* [Språk identifiering](#language-detection)
* [Igenkänning av namngiven entitet](#named-entity-recognition-ner)
* [Länkning av entitet](#entity-linking)
* [Extrahering av nyckel fraser](#key-phrase-extraction)

## <a name="authenticate-the-client"></a>Autentisera klienten

Skapa en metod för att instansiera `TextAnalyticsClient` objektet med nyckeln och slut punkten för din textanalys-resurs. Det här exemplet är detsamma för versionerna 3,0 och 3,1 av API: et.

```java
static TextAnalyticsClient authenticateClient(String key, String endpoint) {
    return new TextAnalyticsClientBuilder()
        .credential(new AzureKeyCredential(key))
        .endpoint(endpoint)
        .buildClient();
}
```


Anropa autentiseringsmetoden i program `main()` metoden för att instansiera klienten.

## <a name="sentiment-analysis"></a>Attitydanalys

# <a name="version-31-preview"></a>[Version 3,1 Preview](#tab/version-3-1)

> [!NOTE]
> I version `3.1` :
> * Attitydanalys omfattar utvinnings analys för bedömning som är valfri flagga. 
> * Utåsikts utvinning innehåller sentiment på aspekt-och åsikts nivå. 

Skapa en ny funktion som anropar `sentimentAnalysisExample()` den klient som du skapade tidigare och anropa dess `analyzeSentiment()` funktion. Det returnerade `AnalyzeSentimentResult` objektet kommer att innehålla `documentSentiment` och `sentenceSentiments` om det lyckas, eller ett `errorMessage` IF-not. 

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

### <a name="opinion-mining"></a>Åsikts utvinning

Om du vill utföra sentiment-analys med utåsikts utvinning skapar du en ny funktion som anropar `sentimentAnalysisWithOpinionMiningExample()` den-klient som du skapade tidigare och anropar `analyzeSentiment()` funktionen med alternativet för att ange alternativ `AnalyzeSentimentOptions` . Det returnerade `AnalyzeSentimentResult` objektet kommer att innehålla `documentSentiment` och `sentenceSentiments` om det lyckas, eller ett `errorMessage` IF-not. 


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
        sentenceSentiment.getMinedOpinions().forEach(minedOpinions -> {
            AspectSentiment aspectSentiment = minedOpinions.getAspect();
            System.out.printf("\t\tAspect sentiment: %s, aspect text: %s%n", aspectSentiment.getSentiment(),
                    aspectSentiment.getText());
            SentimentConfidenceScores aspectScores = aspectSentiment.getConfidenceScores();
            System.out.printf("\t\tAspect positive score: %f, negative score: %f.%n",
                    aspectScores.getPositive(), aspectScores.getNegative());
            for (OpinionSentiment opinionSentiment : minedOpinions.getOpinions()) {
                System.out.printf("\t\t\t'%s' opinion sentiment because of \"%s\". Is the opinion negated: %s.%n",
                        opinionSentiment.getSentiment(), opinionSentiment.getText(), opinionSentiment.isNegated());
                SentimentConfidenceScores opinionScores = opinionSentiment.getConfidenceScores();
                System.out.printf("\t\t\tOpinion positive score: %f, negative score: %f.%n",
                        opinionScores.getPositive(), opinionScores.getNegative());
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
        Aspect sentiment: negative, aspect text: atmosphere
        Aspect positive score: 0.010000, negative score: 0.990000.
            'negative' opinion sentiment because of "bad". Is the opinion negated: false.
            Opinion positive score: 0.010000, negative score: 0.990000.
    Sentence sentiment: negative, positive score: 0.020000, neutral score: 0.440000, negative score: 0.540000.
    Sentence sentiment: negative, positive score: 0.000000, neutral score: 0.000000, negative score: 1.000000.
        Aspect sentiment: negative, aspect text: Staff
        Aspect positive score: 0.000000, negative score: 1.000000.
            'negative' opinion sentiment because of "friendly". Is the opinion negated: true.
            Opinion positive score: 0.000000, negative score: 1.000000.
            'negative' opinion sentiment because of "helpful". Is the opinion negated: true.
            Opinion positive score: 0.000000, negative score: 1.000000.

Process finished with exit code 0
```

# <a name="version-30"></a>[Version 3,0](#tab/version-3)

Skapa en ny funktion som anropar `sentimentAnalysisExample()` den klient som du skapade tidigare och anropa dess `analyzeSentiment()` funktion. Det returnerade `AnalyzeSentimentResult` objektet kommer att innehålla `documentSentiment` och `sentenceSentiments` om det lyckas, eller ett `errorMessage` IF-not. 

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

# <a name="version-21"></a>[Version 2,1](#tab/version-2)

Den här artikeln beskriver endast version 3. x av API: et.

---

## <a name="language-detection"></a>Språkidentifiering

Skapa en ny funktion som anropar `detectLanguageExample()` den klient som du skapade tidigare och anropa dess `detectLanguage()` funktion. Det returnerade `DetectLanguageResult` objektet kommer att innehålla ett primärt språk som identifierats, en lista med andra språk som har identifierats om det lyckas, eller `errorMessage` om det inte. Det här exemplet är detsamma för versionerna 3,0 och 3,1 av API: et.

> [!Tip]
> I vissa fall kan det vara svårt att disambiguate språk baserat på indatamängden. Du kan använda- `countryHint` parametern för att ange en landskod på 2 bokstäver. Som standard använder API: t "US" som standard-countryHint, för att ta bort det här alternativet kan du återställa den här parametern genom att ange värdet till en tom sträng `countryHint = ""` . Om du vill ange en annan standard ställer du in `TextAnalyticsClientOptions.DefaultCountryHint` egenskapen och skickar den under klient initieringen.

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

## <a name="named-entity-recognition-ner"></a>Igenkänning av namngivna enheter (NER)

# <a name="version-31-preview"></a>[Version 3,1 Preview](#tab/version-3-1)

> [!NOTE]
> I version `3.1` :
> * NER innehåller separata metoder för att identifiera personlig information. 
> * Enhets länkning är en separat begäran än NER.

Skapa en ny funktion som anropar `recognizeEntitiesExample()` den klient som du skapade tidigare och anropa dess `recognizeEntities()` funktion. Det returnerade `CategorizedEntityCollection` objektet kommer att innehålla en lista över `CategorizedEntity` om det lyckades, eller en `errorMessage` IF-not.

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

### <a name="entity-linking"></a>Länkning av entitet

Skapa en ny funktion som anropar `recognizeLinkedEntitiesExample()` den klient som du skapade tidigare och anropa dess `recognizeLinkedEntities()` funktion. Det returnerade `LinkedEntityCollection` objektet kommer att innehålla en lista över `LinkedEntity` om det lyckades, eller en `errorMessage` IF-not. Eftersom länkade entiteter identifieras unikt, grupperas förekomster av samma entitet under ett `LinkedEntity` objekt som en lista med `LinkedEntityMatch` objekt.


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


### <a name="personally-identifiable-information-recognition"></a>Personligt identifierbar informations igenkänning

Skapa en ny funktion som anropar `recognizePiiEntitiesExample()` den klient som du skapade tidigare och anropa dess `recognizePiiEntities()` funktion. Det returnerade `PiiEntityCollection` objektet kommer att innehålla en lista över `PiiEntity` om det lyckades, eller en `errorMessage` IF-not. Den innehåller också den avmarkerade texten, som består av inmatad text med alla identifierbara entiteter som ersatts med `*****` .

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

# <a name="version-30"></a>[Version 3,0](#tab/version-3)

> [!NOTE]
> I version `3.0` :
> * NER innehåller separata metoder för att identifiera personlig information. 
> * Enhets länkning är en separat begäran än NER.

Skapa en ny funktion som anropar `recognizeEntitiesExample()` den klient som du skapade tidigare och anropa dess `recognizeEntities()` funktion. Det returnerade `CategorizedEntityCollection` objektet kommer att innehålla en lista över `CategorizedEntity` om det lyckades, eller en `errorMessage` IF-not.

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

### <a name="entity-linking"></a>Länkning av entitet

Skapa en ny funktion som anropar `recognizeLinkedEntitiesExample()` den klient som du skapade tidigare och anropa dess `recognizeLinkedEntities()` funktion. Det returnerade `LinkedEntityCollection` objektet kommer att innehålla en lista över `LinkedEntity` om det lyckades, eller en `errorMessage` IF-not. Eftersom länkade entiteter identifieras unikt, grupperas förekomster av samma entitet under ett `LinkedEntity` objekt som en lista med `LinkedEntityMatch` objekt.

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

# <a name="version-21"></a>[Version 2,1](#tab/version-2)

Den här artikeln beskriver endast version 3. x av API: et.

---

## <a name="key-phrase-extraction"></a>Extrahering av nyckelfraser

Skapa en ny funktion som anropar `extractKeyPhrasesExample()` den klient som du skapade tidigare och anropa dess `extractKeyPhrases()` funktion. Det returnerade `ExtractKeyPhraseResult` objektet kommer att innehålla en lista över nyckel fraser om det lyckas eller `errorMessage` inte. Det här exemplet är detsamma för version 3,0 och 3,1 av API: et.

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

## <a name="use-the-api-asynchronously-with-the-analyze-operation"></a>Använd API asynkront med analys åtgärden

# <a name="version-31-preview"></a>[Version 3,1 Preview](#tab/version-3-1)

> [!CAUTION]
> Om du vill använda analys åtgärder måste du använda en Textanalys resurs med pris nivån standard (S).  

Skapa en ny funktion `analyzeOperationExample()` som kallas, som anropar `beginAnalyzeTasks()` funktionen. Resultatet blir en tids krävande åtgärd som kommer att avsökas efter resultat.

```java
static void analyzeOperationExample(TextAnalyticsClient client)
{
        List<TextDocumentInput> documents = Arrays.asList(
                        new TextDocumentInput("0", "Microsoft was founded by Bill Gates and Paul Allen.")
                        );

        SyncPoller<TextAnalyticsOperationResult, PagedIterable<AnalyzeTasksResult>> syncPoller =
                        client.beginAnalyzeTasks(documents,
                                        new AnalyzeTasksOptions().setDisplayName("{tasks_display_name}")
                                                        .setEntitiesRecognitionTasks(Arrays.asList(new EntitiesTask())),
                                        Context.NONE);

        syncPoller.waitForCompletion();
        PagedIterable<AnalyzeTasksResult> result = syncPoller.getFinalResult();

        result.forEach(analyzeJobState -> {
                System.out.printf("Job Display Name: %s, Job ID: %s.%n", analyzeJobState.getDisplayName(),
                                analyzeJobState.getJobId());
                System.out.printf("Total tasks: %s, completed: %s, failed: %s, in progress: %s.%n",
                                analyzeJobState.getTotal(), analyzeJobState.getCompleted(), analyzeJobState.getFailed(),
                                analyzeJobState.getInProgress());

                List<RecognizeEntitiesResultCollection> entityRecognitionTasks =
                                analyzeJobState.getEntityRecognitionTasks();
                if (entityRecognitionTasks != null) {
                        entityRecognitionTasks.forEach(taskResult -> {
                                // Recognized entities for each of documents from a batch of documents
                                AtomicInteger counter = new AtomicInteger();
                                for (RecognizeEntitiesResult entitiesResult : taskResult) {
                                        System.out.printf("%n%s%n", documents.get(counter.getAndIncrement()));
                                        if (entitiesResult.isError()) {
                                                // Erroneous document
                                                System.out.printf("Cannot recognize entities. Error: %s%n",
                                                                entitiesResult.getError().getMessage());
                                        } else {
                                                // Valid document
                                                entitiesResult.getEntities().forEach(entity -> System.out.printf(
                                                                "Recognized entity: %s, entity category: %s, entity subcategory: %s, "
                                                                                + "confidence score: %f.%n",
                                                                entity.getText(), entity.getCategory(), entity.getSubcategory(),
                                                                entity.getConfidenceScore()));
                                        }
                                }
                        });
                }
        });
    }
```

När du har lagt till det här exemplet i programmet anropar du det i din `main()` metod.

```java
analyzeOperationExample(client);
```

### <a name="output"></a>Utdata

```console
Job Display Name: {tasks_display_name}, Job ID: 84fd4db4-0734-47ec-b263-ac5451e83f2a_637432416000000000.
Total tasks: 1, completed: 1, failed: 0, in progress: 0.

Text = Microsoft was founded by Bill Gates and Paul Allen., Id = 0, Language = null
Recognized entity: Microsoft, entity category: Organization, entity subcategory: null, confidence score: 0.960000.
Recognized entity: Bill Gates, entity category: Person, entity subcategory: null, confidence score: 1.000000.
Recognized entity: Paul Allen, entity category: Person, entity subcategory: null, confidence score: 0.990000.
```

Du kan också använda analys åtgärden för att identifiera personligt identifierbar information och extrahering av nyckel fraser. Se [analys exemplet](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/textanalytics/azure-ai-textanalytics/src/samples/java/com/azure/ai/textanalytics/lro/AnalyzeTasksAsync.java) på GitHub.

# <a name="version-30"></a>[Version 3,0](#tab/version-3)

Den här funktionen är inte tillgänglig i version 3,0.

# <a name="version-21"></a>[Version 2,1](#tab/version-2)

Den här funktionen är inte tillgänglig i version 2,1.

---
