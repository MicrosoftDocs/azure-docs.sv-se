---
title: 'Snabbstart: Textanalys v3-klientbibliotek för Node.js | Microsoft Docs'
description: Kom igång med v3 Textanalys klientbibliotek för Node.js.
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 02/09/2021
ms.author: aahi
ms.reviewer: sumeh, assafi
ms.custom: devx-track-js
ms.openlocfilehash: 3640a03f8ac814fec2823a761e651ab386438c5c
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2021
ms.locfileid: "107327040"
---
<a name="HOLTop"></a>

# <a name="version-31-preview"></a>[Version 3.1 –förhandsversion](#tab/version-3-1)

[v3-referensdokumentation](/javascript/api/overview/azure/ai-text-analytics-readme?preserve-view=true&view=azure-node-preview)  |  [v3-bibliotekskällkod](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/textanalytics/ai-text-analytics)  |  [v3-paket (NPM)](https://www.npmjs.com/package/@azure/ai-text-analytics)  |  [v3-exempel](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/textanalytics/ai-text-analytics/samples)


# <a name="version-30"></a>[Version 3.0](#tab/version-3)

[v3-referensdokumentation](/javascript/api/overview/azure/ai-text-analytics-readme)  |  [v3-bibliotekskällkod](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/textanalytics/ai-text-analytics)  |  [v3-paket (NPM)](https://www.npmjs.com/package/@azure/ai-text-analytics)  |  [v3-exempel](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/textanalytics/ai-text-analytics/samples)


---

## <a name="prerequisites"></a>Förutsättningar

* Azure-prenumeration [– Skapa en kostnadsfritt](https://azure.microsoft.com/free/cognitive-services)
* Den aktuella versionen av [Node.js](https://nodejs.org/).
* När du har din Azure-prenumeration skapar Textanalys en Textanalys resurs i Azure Portal för att <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics"  title=" hämta din nyckel och "  target="_blank"> </a> slutpunkt. När den har distribuerats klickar du **på Gå till resurs**.
    * Du behöver nyckeln och slutpunkten från den resurs som du skapar för att ansluta ditt program till Textanalys-API:et. Du klistrar in din nyckel och slutpunkt i koden nedan senare i snabbstarten.
    * Du kan använda den kostnadsfria prisnivån ( `F0` ) för att prova tjänsten och senare uppgradera till en betald nivå för produktion.
* Om du vill använda funktionen Analysera behöver du Textanalys resurs med prisnivån standard (S).

## <a name="setting-up"></a>Inrätta

### <a name="create-a-new-nodejs-application"></a>Skapa ett nytt Node.js-program

I ett konsolfönster (till exempel cmd, PowerShell eller Bash) skapar du en ny katalog för din app och navigerar till den. 

```console
mkdir myapp 

cd myapp
```

Kör kommandot `npm init` för att skapa ett nodprogram med en `package.json` fil. 

```console
npm init
```
### <a name="install-the-client-library"></a>Installera klientbiblioteket

# <a name="version-31-preview"></a>[Version 3.1 –förhandsversion](#tab/version-3-1)

Installera `@azure/ai-text-analytics` NPM-paketen:

```console
npm install --save @azure/ai-text-analytics@5.1.0-beta.5
```

> [!TIP]
> Vill du visa hela snabbstartskodfilen samtidigt? Du hittar den på [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/TextAnalytics/text-analytics-v3-client-library.js), som innehåller kodexe exemplen i den här snabbstarten. 


# <a name="version-30"></a>[Version 3.0](#tab/version-3)

Installera `@azure/ai-text-analytics` NPM-paketen:

```console
npm install --save @azure/ai-text-analytics@5.0.0
```

> [!TIP]
> Vill du visa hela snabbstartskodfilen samtidigt? Du hittar den på [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/TextAnalytics/text-analytics-v3-client-library.js), som innehåller kodexe exemplen i den här snabbstarten. 

---

Appens `package.json` fil uppdateras med beroendena.
Skapa en fil med `index.js` namnet och lägg till följande:

# <a name="version-31-preview"></a>[Förhandsversion 3.1](#tab/version-3-1)

```javascript
"use strict";

const { TextAnalyticsClient, AzureKeyCredential } = require("@azure/ai-text-analytics");
```

# <a name="version-30"></a>[Version 3.0](#tab/version-3)

```javascript
"use strict";

const { TextAnalyticsClient, AzureKeyCredential } = require("@azure/ai-text-analytics");
```

---

Skapa variabler för resursens Azure-slutpunkt och nyckel.

[!INCLUDE [text-analytics-find-resource-information](../find-azure-resource-info.md)]

```javascript
const key = '<paste-your-text-analytics-key-here>';
const endpoint = '<paste-your-text-analytics-endpoint-here>';
```

## <a name="object-model"></a>Objektmodell

Den Textanalys klienten är ett `TextAnalyticsClient` objekt som autentiserar till Azure med hjälp av din nyckel. Klienten har flera metoder för att analysera text, som en sträng eller en batch.

Text skickas till API:et som en lista över , som är objekt som innehåller en kombination av `documents` `dictionary` `id` `text` attributen , och `language` beroende på vilken metod som används. Attributet `text` lagrar texten som ska analyseras i ursprunget , och kan vara vilket värde som `language` `id` helst. 

Svarsobjektet är en lista som innehåller analysinformationen för varje dokument. 

## <a name="code-examples"></a>Kodexempel

* [Klientautentisering](#client-authentication)
* [Attitydanalys](#sentiment-analysis) 
* [Yttrandeutvinning](#opinion-mining)
* [Språkidentifiering](#language-detection)
* [Igenkänning av namngiven entitet](#named-entity-recognition-ner)
* [Entitetslänkning](#entity-linking)
* Personligt identifierbar information
* [Extrahering av nyckelfraser](#key-phrase-extraction)

## <a name="client-authentication"></a>Klientautentisering

# <a name="version-31-preview"></a>[Version 3.1 –förhandsversion](#tab/version-3-1)

Skapa ett nytt `TextAnalyticsClient` objekt med din nyckel och slutpunkt som parametrar.

```javascript
const textAnalyticsClient = new TextAnalyticsClient(endpoint,  new AzureKeyCredential(key));
```

# <a name="version-30"></a>[Version 3.0](#tab/version-3)

Skapa ett nytt `TextAnalyticsClient` objekt med din nyckel och slutpunkt som parametrar.

```javascript
const textAnalyticsClient = new TextAnalyticsClient(endpoint,  new AzureKeyCredential(key));
```

---

## <a name="sentiment-analysis"></a>Attitydanalys

# <a name="version-31-preview"></a>[Version 3.1 –förhandsversion](#tab/version-3-1)

Skapa en matris med strängar som innehåller det dokument som du vill analysera. Anropa klientens -metod `analyzeSentiment()` och hämta det returnerade `SentimentBatchResult` objektet. Iterera genom listan med resultat och skriv ut varje dokuments ID, attityd på dokumentnivå med förtroendepoäng. För varje dokument innehåller resultatet attityd på meningsnivå tillsammans med förskjutningar, längd och förtroendepoäng.

```javascript
async function sentimentAnalysis(client){

    const sentimentInput = [
        "I had the best day of my life. I wish you were there with me."
    ];
    const sentimentResult = await client.analyzeSentiment(sentimentInput);

    sentimentResult.forEach(document => {
        console.log(`ID: ${document.id}`);
        console.log(`\tDocument Sentiment: ${document.sentiment}`);
        console.log(`\tDocument Scores:`);
        console.log(`\t\tPositive: ${document.confidenceScores.positive.toFixed(2)} \tNegative: ${document.confidenceScores.negative.toFixed(2)} \tNeutral: ${document.confidenceScores.neutral.toFixed(2)}`);
        console.log(`\tSentences Sentiment(${document.sentences.length}):`);
        document.sentences.forEach(sentence => {
            console.log(`\t\tSentence sentiment: ${sentence.sentiment}`)
            console.log(`\t\tSentences Scores:`);
            console.log(`\t\tPositive: ${sentence.confidenceScores.positive.toFixed(2)} \tNegative: ${sentence.confidenceScores.negative.toFixed(2)} \tNeutral: ${sentence.confidenceScores.neutral.toFixed(2)}`);
        });
    });
}
sentimentAnalysis(textAnalyticsClient)
```

Kör koden med `node index.js` i konsolfönstret.

### <a name="output"></a>Utdata

```console
ID: 0
        Document Sentiment: positive
        Document Scores:
                Positive: 1.00  Negative: 0.00  Neutral: 0.00
        Sentences Sentiment(2):
                Sentence sentiment: positive
                Sentences Scores:
                Positive: 1.00  Negative: 0.00  Neutral: 0.00
                Sentence sentiment: neutral
                Sentences Scores:
                Positive: 0.21  Negative: 0.02  Neutral: 0.77
```

### <a name="opinion-mining"></a>Yttrandeutvinning

För att kunna göra attitydanalys med yttrandeutvinning skapar du en matris med strängar som innehåller det dokument som du vill analysera. Anropa klientens -metod `analyzeSentiment()` med att lägga till alternativflaggan och hämta det `includeOpinionMining: true` returnerade `SentimentBatchResult` objektet. Iterera genom listan med resultat och skriv ut varje dokuments ID, attityd på dokumentnivå med förtroendepoäng. För varje dokument innehåller resultatet inte bara attityd på meningsnivå som ovan, utan även attityd på aspekt- och yttrandenivå.

```javascript
async function sentimentAnalysisWithOpinionMining(client){

  const sentimentInput = [
    {
      text: "The food and service were unacceptable, but the concierge were nice",
      id: "0",
      language: "en"
    }
  ];
  const results = await client.analyzeSentiment(sentimentInput, { includeOpinionMining: true });

  for (let i = 0; i < results.length; i++) {
    const result = results[i];
    console.log(`- Document ${result.id}`);
    if (!result.error) {
      console.log(`\tDocument text: ${sentimentInput[i].text}`);
      console.log(`\tOverall Sentiment: ${result.sentiment}`);
      console.log("\tSentiment confidence scores:", result.confidenceScores);
      console.log("\tSentences");
      for (const { sentiment, confidenceScores, opinions } of result.sentences) {
        console.log(`\t- Sentence sentiment: ${sentiment}`);
        console.log("\t  Confidence scores:", confidenceScores);
        console.log("\t  Mined opinions");
        for (const { target, assessments } of opinions) {
          console.log(`\t\t- Target text: ${target.text}`);
          console.log(`\t\t  Target sentiment: ${target.sentiment}`);
          console.log("\t\t  Target confidence scores:", target.confidenceScores);
          console.log("\t\t  Target assessments");
          for (const { text, sentiment } of assessments) {
            console.log(`\t\t\t- Text: ${text}`);
            console.log(`\t\t\t  Sentiment: ${sentiment}`);
          }
        }
      }
    } else {
      console.error(`\tError: ${result.error}`);
    }
  }
}
sentimentAnalysisWithOpinionMining(textAnalyticsClient)
```

Kör koden med `node index.js` i konsolfönstret.

### <a name="output"></a>Utdata

```console
- Document 0
        Document text: The food and service were unacceptable, but the concierge were nice
        Overall Sentiment: positive
        Sentiment confidence scores: { positive: 0.84, neutral: 0, negative: 0.16 }
        Sentences
        - Sentence sentiment: positive
          Confidence scores: { positive: 0.84, neutral: 0, negative: 0.16 }
          Mined opinions
                - Target text: food
                  Target sentiment: negative
                  Target confidence scores: { positive: 0.01, negative: 0.99 }
                  Target assessments
                        - Text: unacceptable
                          Sentiment: negative
                - Target text: service
                  Target sentiment: negative
                  Target confidence scores: { positive: 0.01, negative: 0.99 }
                  Target assessments
                        - Text: unacceptable
                          Sentiment: negative
                - Target text: concierge
                  Target sentiment: positive
                  Target confidence scores: { positive: 1, negative: 0 }
                  Target assessments
                        - Text: nice
                          Sentiment: positive
```

# <a name="version-30"></a>[Version 3.0](#tab/version-3)

Skapa en matris med strängar som innehåller det dokument som du vill analysera. Anropa klientens -metod `analyzeSentiment()` och hämta det returnerade `SentimentBatchResult` objektet. Iterera igenom listan med resultat och skriv ut varje dokuments ID, attityd på dokumentnivå med förtroendepoäng. För varje dokument innehåller resultatet sentiment på meningsnivå tillsammans med förskjutningar, längd och förtroendepoäng.

```javascript
async function sentimentAnalysis(client){

    const sentimentInput = [
        "I had the best day of my life. I wish you were there with me."
    ];
    const sentimentResult = await client.analyzeSentiment(sentimentInput);

    sentimentResult.forEach(document => {
        console.log(`ID: ${document.id}`);
        console.log(`\tDocument Sentiment: ${document.sentiment}`);
        console.log(`\tDocument Scores:`);
        console.log(`\t\tPositive: ${document.confidenceScores.positive.toFixed(2)} \tNegative: ${document.confidenceScores.negative.toFixed(2)} \tNeutral: ${document.confidenceScores.neutral.toFixed(2)}`);
        console.log(`\tSentences Sentiment(${document.sentences.length}):`);
        document.sentences.forEach(sentence => {
            console.log(`\t\tSentence sentiment: ${sentence.sentiment}`)
            console.log(`\t\tSentences Scores:`);
            console.log(`\t\tPositive: ${sentence.confidenceScores.positive.toFixed(2)} \tNegative: ${sentence.confidenceScores.negative.toFixed(2)} \tNeutral: ${sentence.confidenceScores.neutral.toFixed(2)}`);
        });
    });
}
sentimentAnalysis(textAnalyticsClient)
```

Kör koden med `node index.js` i konsolfönstret.

### <a name="output"></a>Utdata

```console
ID: 0
        Document Sentiment: positive
        Document Scores:
                Positive: 1.00  Negative: 0.00  Neutral: 0.00
        Sentences Sentiment(2):
                Sentence sentiment: positive
                Sentences Scores:
                Positive: 1.00  Negative: 0.00  Neutral: 0.00
                Sentence sentiment: neutral
                Sentences Scores:
                Positive: 0.21  Negative: 0.02  Neutral: 0.77
```

---

## <a name="language-detection"></a>Språkidentifiering

# <a name="version-31-preview"></a>[Förhandsversion 3.1](#tab/version-3-1)

Skapa en matris med strängar som innehåller det dokument som du vill analysera. Anropa klientens -metod `detectLanguage()` och hämta den returnerade `DetectLanguageResultCollection` . Iterera sedan igenom resultaten och skriv ut varje dokument-ID med respektive primärt språk.

```javascript
async function languageDetection(client) {

    const languageInputArray = [
        "Ce document est rédigé en Français."
    ];
    const languageResult = await client.detectLanguage(languageInputArray);

    languageResult.forEach(document => {
        console.log(`ID: ${document.id}`);
        console.log(`\tPrimary Language ${document.primaryLanguage.name}`)
    });
}
languageDetection(textAnalyticsClient);
```

Kör koden med `node index.js` i konsolfönstret.

### <a name="output"></a>Utdata

```console
ID: 0
        Primary Language French
```

# <a name="version-30"></a>[Version 3.0](#tab/version-3)

Skapa en matris med strängar som innehåller det dokument som du vill analysera. Anropa klientens -metod `detectLanguage()` och hämta den returnerade `DetectLanguageResultCollection` . Iterera sedan igenom resultaten och skriv ut varje dokument-ID med respektive primärt språk.

```javascript
async function languageDetection(client) {

    const languageInputArray = [
        "Ce document est rédigé en Français."
    ];
    const languageResult = await client.detectLanguage(languageInputArray);

    languageResult.forEach(document => {
        console.log(`ID: ${document.id}`);
        console.log(`\tPrimary Language ${document.primaryLanguage.name}`)
    });
}
languageDetection(textAnalyticsClient);
```

Kör koden med `node index.js` i konsolfönstret.

### <a name="output"></a>Utdata

```console
ID: 0
        Primary Language French
```

---

## <a name="named-entity-recognition-ner"></a>Igenkänning av namngiven entitet (NER)

# <a name="version-31-preview"></a>[Förhandsversion 3.1](#tab/version-3-1)

> [!NOTE]
> I version `3.1` :
> * Entitetslänkning är en separat begäran än NER.

Skapa en matris med strängar som innehåller det dokument som du vill analysera. Anropa klientens `recognizeEntities()` -metod och hämta `RecognizeEntitiesResult` -objektet. Iterera genom listan med resultat och skriv ut entitetsnamnet, typen, undertypen, förskjutningen, längden och poängen.

```javascript
async function entityRecognition(client){

    const entityInputs = [
        "Microsoft was founded by Bill Gates and Paul Allen on April 4, 1975, to develop and sell BASIC interpreters for the Altair 8800",
        "La sede principal de Microsoft se encuentra en la ciudad de Redmond, a 21 kilómetros de Seattle."
    ];
    const entityResults = await client.recognizeEntities(entityInputs);

    entityResults.forEach(document => {
        console.log(`Document ID: ${document.id}`);
        document.entities.forEach(entity => {
            console.log(`\tName: ${entity.text} \tCategory: ${entity.category} \tSubcategory: ${entity.subCategory ? entity.subCategory : "N/A"}`);
            console.log(`\tScore: ${entity.confidenceScore}`);
        });
    });
}
entityRecognition(textAnalyticsClient);
```

Kör koden med `node index.js` i konsolfönstret.

### <a name="output"></a>Utdata

```console
Document ID: 0
        Name: Microsoft         Category: Organization  Subcategory: N/A
        Score: 0.29
        Name: Bill Gates        Category: Person        Subcategory: N/A
        Score: 0.78
        Name: Paul Allen        Category: Person        Subcategory: N/A
        Score: 0.82
        Name: April 4, 1975     Category: DateTime      Subcategory: Date
        Score: 0.8
        Name: 8800      Category: Quantity      Subcategory: Number
        Score: 0.8
Document ID: 1
        Name: 21        Category: Quantity      Subcategory: Number
        Score: 0.8
        Name: Seattle   Category: Location      Subcategory: GPE
        Score: 0.25
```

### <a name="entity-linking"></a>Entity Linking

Skapa en matris med strängar som innehåller det dokument som du vill analysera. Anropa klientens `recognizeLinkedEntities()` -metod och hämta `RecognizeLinkedEntitiesResult` -objektet. Iterera genom resultatlistan och skriv ut entitetsnamn, ID, datakälla, URL och matchningar. Varje objekt i `matches` matrisen innehåller förskjutning, längd och poäng för matchningen.

```javascript
async function linkedEntityRecognition(client){

    const linkedEntityInput = [
        "Microsoft was founded by Bill Gates and Paul Allen on April 4, 1975, to develop and sell BASIC interpreters for the Altair 8800. During his career at Microsoft, Gates held the positions of chairman, chief executive officer, president and chief software architect, while also being the largest individual shareholder until May 2014."
    ];
    const entityResults = await client.recognizeLinkedEntities(linkedEntityInput);

    entityResults.forEach(document => {
        console.log(`Document ID: ${document.id}`);
        document.entities.forEach(entity => {
            console.log(`\tName: ${entity.name} \tID: ${entity.dataSourceEntityId} \tURL: ${entity.url} \tData Source: ${entity.dataSource}`);
            console.log(`\tMatches:`)
            entity.matches.forEach(match => {
                console.log(`\t\tText: ${match.text} \tScore: ${match.confidenceScore.toFixed(2)}`);
        })
        });
    });
}
linkedEntityRecognition(textAnalyticsClient);
```

Kör koden med `node index.js` i konsolfönstret.

### <a name="output"></a>Utdata

```console
Document ID: 0
        Name: Altair 8800       ID: Altair 8800         URL: https://en.wikipedia.org/wiki/Altair_8800  Data Source: Wikipedia
        Matches:
                Text: Altair 8800       Score: 0.88
        Name: Bill Gates        ID: Bill Gates  URL: https://en.wikipedia.org/wiki/Bill_Gates   Data Source: Wikipedia
        Matches:
                Text: Bill Gates        Score: 0.63
                Text: Gates     Score: 0.63
        Name: Paul Allen        ID: Paul Allen  URL: https://en.wikipedia.org/wiki/Paul_Allen   Data Source: Wikipedia
        Matches:
                Text: Paul Allen        Score: 0.60
        Name: Microsoft         ID: Microsoft   URL: https://en.wikipedia.org/wiki/Microsoft    Data Source: Wikipedia
        Matches:
                Text: Microsoft         Score: 0.55
                Text: Microsoft         Score: 0.55
        Name: April 4   ID: April 4     URL: https://en.wikipedia.org/wiki/April_4      Data Source: Wikipedia
        Matches:
                Text: April 4   Score: 0.32
        Name: BASIC     ID: BASIC       URL: https://en.wikipedia.org/wiki/BASIC        Data Source: Wikipedia
        Matches:
                Text: BASIC     Score: 0.33
```

### <a name="personally-identifying-information-pii-recognition"></a>Igenkänning av personligt identifierande information (PII)

Skapa en matris med strängar som innehåller det dokument som du vill analysera. Anropa klientens `recognizePiiEntities()` -metod och hämta `RecognizePIIEntitiesResult` -objektet. Iterera genom listan med resultat och skriv ut entitetens namn, typ och poäng.

```javascript
async function piiRecognition(client) {

    const documents = [
        "The employee's phone number is (555) 555-5555."
    ];

    const results = await client.recognizePiiEntities(documents, "en");
    for (const result of results) {
        if (result.error === undefined) {
            console.log("Redacted Text: ", result.redactedText);
            console.log(" -- Recognized PII entities for input", result.id, "--");
            for (const entity of result.entities) {
                console.log(entity.text, ":", entity.category, "(Score:", entity.confidenceScore, ")");
            }
        } else {
            console.error("Encountered an error:", result.error);
        }
    }
}
piiRecognition(textAnalyticsClient)
```

Kör koden med `node index.js` i konsolfönstret.

### <a name="output"></a>Utdata

```console
Redacted Text:  The employee's phone number is **************.
 -- Recognized PII entities for input 0 --
(555) 555-5555 : Phone Number (Score: 0.8 )
```

# <a name="version-30"></a>[Version 3.0](#tab/version-3)

> [!NOTE]
> I version `3.0` :
> * Entitetslänkning är en separat begäran än NER.

Skapa en matris med strängar som innehåller det dokument som du vill analysera. Anropa klientens `recognizeEntities()` -metod och hämta `RecognizeEntitiesResult` -objektet. Iterera genom listan med resultat och skriv ut entitetsnamnet, typen, undertypen, förskjutningen, längden och poängen.

```javascript
async function entityRecognition(client){

    const entityInputs = [
        "Microsoft was founded by Bill Gates and Paul Allen on April 4, 1975, to develop and sell BASIC interpreters for the Altair 8800",
        "La sede principal de Microsoft se encuentra en la ciudad de Redmond, a 21 kilómetros de Seattle."
    ];
    const entityResults = await client.recognizeEntities(entityInputs);

    entityResults.forEach(document => {
        console.log(`Document ID: ${document.id}`);
        document.entities.forEach(entity => {
            console.log(`\tName: ${entity.text} \tCategory: ${entity.category} \tSubcategory: ${entity.subCategory ? entity.subCategory : "N/A"}`);
            console.log(`\tScore: ${entity.confidenceScore}`);
        });
    });
}
entityRecognition(textAnalyticsClient);
```

Kör koden med `node index.js` i konsolfönstret.

### <a name="output"></a>Utdata

```console
Document ID: 0
        Name: Microsoft         Category: Organization  Subcategory: N/A
        Score: 0.29
        Name: Bill Gates        Category: Person        Subcategory: N/A
        Score: 0.78
        Name: Paul Allen        Category: Person        Subcategory: N/A
        Score: 0.82
        Name: April 4, 1975     Category: DateTime      Subcategory: Date
        Score: 0.8
        Name: 8800      Category: Quantity      Subcategory: Number
        Score: 0.8
Document ID: 1
        Name: 21        Category: Quantity      Subcategory: Number
        Score: 0.8
        Name: Seattle   Category: Location      Subcategory: GPE
        Score: 0.25
```

### <a name="entity-linking"></a>Entity Linking

Skapa en matris med strängar som innehåller det dokument som du vill analysera. Anropa klientens `recognizeLinkedEntities()` -metod och hämta `RecognizeLinkedEntitiesResult` -objektet. Iterera genom listan över resultat och skriv ut entitetsnamn, ID, datakälla, URL och matchningar. Varje objekt i `matches` matrisen kommer att innehålla förskjutning, längd och poäng för matchningen.

```javascript
async function linkedEntityRecognition(client){

    const linkedEntityInput = [
        "Microsoft was founded by Bill Gates and Paul Allen on April 4, 1975, to develop and sell BASIC interpreters for the Altair 8800. During his career at Microsoft, Gates held the positions of chairman, chief executive officer, president and chief software architect, while also being the largest individual shareholder until May 2014."
    ];
    const entityResults = await client.recognizeLinkedEntities(linkedEntityInput);

    entityResults.forEach(document => {
        console.log(`Document ID: ${document.id}`);
        document.entities.forEach(entity => {
            console.log(`\tName: ${entity.name} \tID: ${entity.dataSourceEntityId} \tURL: ${entity.url} \tData Source: ${entity.dataSource}`);
            console.log(`\tMatches:`)
            entity.matches.forEach(match => {
                console.log(`\t\tText: ${match.text} \tScore: ${match.confidenceScore.toFixed(2)}`);
        })
        });
    });
}
linkedEntityRecognition(textAnalyticsClient);
```

Kör koden med `node index.js` i konsolfönstret.

### <a name="output"></a>Utdata

```console
Document ID: 0
        Name: Altair 8800       ID: Altair 8800         URL: https://en.wikipedia.org/wiki/Altair_8800  Data Source: Wikipedia
        Matches:
                Text: Altair 8800       Score: 0.88
        Name: Bill Gates        ID: Bill Gates  URL: https://en.wikipedia.org/wiki/Bill_Gates   Data Source: Wikipedia
        Matches:
                Text: Bill Gates        Score: 0.63
                Text: Gates     Score: 0.63
        Name: Paul Allen        ID: Paul Allen  URL: https://en.wikipedia.org/wiki/Paul_Allen   Data Source: Wikipedia
        Matches:
                Text: Paul Allen        Score: 0.60
        Name: Microsoft         ID: Microsoft   URL: https://en.wikipedia.org/wiki/Microsoft    Data Source: Wikipedia
        Matches:
                Text: Microsoft         Score: 0.55
                Text: Microsoft         Score: 0.55
        Name: April 4   ID: April 4     URL: https://en.wikipedia.org/wiki/April_4      Data Source: Wikipedia
        Matches:
                Text: April 4   Score: 0.32
        Name: BASIC     ID: BASIC       URL: https://en.wikipedia.org/wiki/BASIC        Data Source: Wikipedia
        Matches:
                Text: BASIC     Score: 0.33
```


---

## <a name="key-phrase-extraction"></a>Extrahering av nyckelfraser

# <a name="version-31-preview"></a>[Förhandsversion 3.1](#tab/version-3-1)

Skapa en matris med strängar som innehåller det dokument som du vill analysera. Anropa klientens -metod `extractKeyPhrases()` och hämta det returnerade `ExtractKeyPhrasesResult` objektet. Iterera igenom resultaten och skriv ut varje dokuments ID och eventuella identifierade nyckelfraser.

```javascript
async function keyPhraseExtraction(client){

    const keyPhrasesInput = [
        "My cat might need to see a veterinarian.",
    ];
    const keyPhraseResult = await client.extractKeyPhrases(keyPhrasesInput);
    
    keyPhraseResult.forEach(document => {
        console.log(`ID: ${document.id}`);
        console.log(`\tDocument Key Phrases: ${document.keyPhrases}`);
    });
}
keyPhraseExtraction(textAnalyticsClient);
```

Kör koden med `node index.js` i konsolfönstret.

### <a name="output"></a>Utdata

```console
ID: 0
        Document Key Phrases: cat,veterinarian
```

# <a name="version-30"></a>[Version 3.0](#tab/version-3)

Skapa en matris med strängar som innehåller det dokument som du vill analysera. Anropa klientens -metod `extractKeyPhrases()` och hämta det returnerade `ExtractKeyPhrasesResult` objektet. Iterera igenom resultaten och skriv ut varje dokuments ID och eventuella identifierade nyckelfraser.

```javascript
async function keyPhraseExtraction(client){

    const keyPhrasesInput = [
        "My cat might need to see a veterinarian.",
    ];
    const keyPhraseResult = await client.extractKeyPhrases(keyPhrasesInput);
    
    keyPhraseResult.forEach(document => {
        console.log(`ID: ${document.id}`);
        console.log(`\tDocument Key Phrases: ${document.keyPhrases}`);
    });
}
keyPhraseExtraction(textAnalyticsClient);
```

Kör koden med `node index.js` i konsolfönstret.

### <a name="output"></a>Utdata

```console
ID: 0
        Document Key Phrases: cat,veterinarian
```


---

## <a name="use-the-api-asynchronously-with-the-analyze-operation"></a>Använda API:et asynkront med åtgärden Analysera

# <a name="version-31-preview"></a>[Förhandsversion 3.1](#tab/version-3-1)

> [!CAUTION]
> Om du vill använda Analysåtgärder måste du Textanalys en resurs med prisnivån standard (S).  

Skapa en ny funktion med `analyze_example()` namnet , som anropar funktionen `beginAnalyze()` . Resultatet blir en långvarig åtgärd som avsöks efter resultat.

```javascript
async function analyze_example(client) {
  const documents = [
    "Microsoft was founded by Bill Gates and Paul Allen.",
  ];

  const actions = {
    recognizeEntitiesActions: [{ modelVersion: "latest" }],
  };
  const poller = await client.beginAnalyzeBatchActions(documents, actions, "en");

  console.log(
    `The analyze batch actions operation was created on ${poller.getOperationState().createdOn}`
  );
  console.log(
    `The analyze batch actions operation results will expire on ${
      poller.getOperationState().expiresOn
    }`
  );
  const resultPages = await poller.pollUntilDone();
  for await (const page of resultPages) {
    const entitiesAction = page.recognizeEntitiesResults[0];
    if (!entitiesAction.error) {
      for (const doc of entitiesAction.results) {
        console.log(`- Document ${doc.id}`);
        if (!doc.error) {
          console.log("\tEntities:");
          for (const entity of doc.entities) {
            console.log(`\t- Entity ${entity.text} of type ${entity.category}`);
          }
        } else {
          console.error("\tError:", doc.error);
        }
      }
    }
  }
}

analyze_example(textAnalyticsClient);
```

### <a name="output"></a>Utdata

```console
The analyze batch actions operation was created on Fri Mar 12 2021 09:53:49 GMT-0800 (Pacific Standard Time)
The analyze batch actions operation results will expire on Sat Mar 13 2021 09:53:49 GMT-0800 (Pacific Standard Time)
- Document 0
        Entities:
        - Entity Microsoft of type Organization
        - Entity Bill Gates of type Person
        - Entity Paul Allen of type Person
```

Du kan också använda åtgärden Analysera för att identifiera PII, identifiera länkade entiteter och extrahering av nyckelfraser. Se Analysera exempel för [JavaScript och](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/textanalytics/ai-text-analytics/samples/v5/javascript) [TypeScript](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/textanalytics/ai-text-analytics/samples/v5/typescript/src) på GitHub.

# <a name="version-30"></a>[Version 3.0](#tab/version-3)

Den här funktionen är inte tillgänglig i version 3.0.

---

## <a name="run-the-application"></a>Kör programmet

Kör programmet med kommandot `node` i snabbstartsfilen.

```console
node index.js
```
