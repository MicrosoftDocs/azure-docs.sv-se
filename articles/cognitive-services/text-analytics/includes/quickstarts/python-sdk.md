---
author: aahill
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 04/19/2021
ms.author: aahi
ms.openlocfilehash: d36666ea35a4ce3fc894e35dd8ca3860900ecef6
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2021
ms.locfileid: "107879853"
---
<a name="HOLTop"></a>

# <a name="version-31-preview"></a>[Förhandsversion 3.1](#tab/version-3-1)

[Referensdokumentation för v3.1](/python/api/azure-ai-textanalytics/azure.ai.textanalytics)  |  [v3.1-bibliotekskällkod](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/textanalytics/azure-ai-textanalytics)  |  [v3.1-paket (PiPy)](https://pypi.org/project/azure-ai-textanalytics/)  |  [v3.1-exempel](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/textanalytics/azure-ai-textanalytics/samples)

# <a name="version-30"></a>[Version 3.0](#tab/version-3)

[Referensdokumentation för v3](/python/api/azure-ai-textanalytics/azure.ai.textanalytics)  |  [v3-bibliotekskällkod](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/textanalytics)  |  [v3-paket (PiPy)](https://pypi.org/project/azure-ai-textanalytics/)  |  [v3-exempel](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/textanalytics/azure-ai-textanalytics/samples)

---

## <a name="prerequisites"></a>Förutsättningar

* Azure-prenumeration [– Skapa en utan kostnad](https://azure.microsoft.com/free/cognitive-services)
* [Python 3.x](https://www.python.org/)
* När du har din Azure-prenumeration skapar du en Textanalys-resurs för att skapa Textanalys resurs i Azure Portal för att <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics"  title=" hämta din nyckel och "  target="_blank"> </a> slutpunkt. När den har distribuerats klickar du **på Gå till resurs**.
    * Du behöver nyckeln och slutpunkten från resursen som du skapar för att ansluta ditt program till Textanalys API. Du klistrar in nyckeln och slutpunkten i koden nedan senare i snabbstarten.
    * Du kan använda den kostnadsfria prisnivån ( `F0` ) för att prova tjänsten och uppgradera senare till en betald nivå för produktion.
* Om du vill använda funktionen Analysera behöver du Textanalys resurs med prisnivån standard (S).

## <a name="setting-up"></a>Inrätta

### <a name="install-the-client-library"></a>Installera klientbiblioteket

När du har installerat Python kan du installera klientbiblioteket med:

# <a name="version-31-preview"></a>[Förhandsversion 3.1](#tab/version-3-1)

```console
pip install azure-ai-textanalytics --pre
```

> [!TIP]
> Vill du visa hela snabbstartskodfilen samtidigt? Du hittar den på [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/TextAnalytics/python-v3-client-library.py), som innehåller kodexe exemplen i den här snabbstarten. 

# <a name="version-30"></a>[Version 3.0](#tab/version-3)

```console
pip install --upgrade azure-ai-textanalytics
```

> [!TIP]
> Vill du visa hela snabbstartskodfilen samtidigt? Du hittar den på [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/TextAnalytics/python-v3-client-library.py), som innehåller kodexe exemplen i den här snabbstarten. 


---

### <a name="create-a-new-python-application"></a>Skapa ett nytt Python-program

Skapa en ny Python-fil och skapa variabler för resursens Azure-slutpunkt och prenumerationsnyckel.

[!INCLUDE [text-analytics-find-resource-information](../find-azure-resource-info.md)]

```python
key = "<paste-your-text-analytics-key-here>"
endpoint = "<paste-your-text-analytics-endpoint-here>"
```


## <a name="object-model"></a>Objektmodell

# <a name="version-31-preview"></a>[Version 3.1 –förhandsversion](#tab/version-3-1)

Klienten Textanalys är ett `TextAnalyticsClient` objekt som autentiserar till Azure. Klienten tillhandahåller flera metoder för att analysera text. 

När bearbetning av text skickas till API:et som en lista över , som antingen är som en lista med sträng, en lista över `documents` dict-liknande representation eller som en lista över `TextDocumentInput/DetectLanguageInput` . Ett `dict-like` -objekt innehåller en kombination `id` av , och `text` `language/country_hint` . Attributet `text` lagrar den text som ska analyseras i `country_hint` ursprunget , och kan vara vilket `id` värde som helst. 

Svarsobjektet är en lista som innehåller analysinformationen för varje dokument. 

# <a name="version-30"></a>[Version 3.0](#tab/version-3)

Klienten Textanalys är ett `TextAnalyticsClient` objekt som autentiserar till Azure med hjälp av din nyckel. Klienten tillhandahåller flera metoder för att analysera text som en batch. 

När batchbearbetningstext skickas till API:et som en lista över , som är objekt som innehåller en kombination av `documents` `dictionary` `id` `text` attributen , och `language` beroende på vilken metod som används. Attributet `text` lagrar den text som ska analyseras i `language` ursprunget , och kan vara vilket `id` värde som helst. 

Svarsobjektet är en lista som innehåller analyserad information för varje dokument. 

---

## <a name="code-examples"></a>Kodexempel

De här kodfragmenten visar hur du utför följande uppgifter med Textanalys klientbibliotek för Python:

# <a name="version-31-preview"></a>[Version 3.1 –förhandsversion](#tab/version-3-1)

* [Autentisera klienten](#authenticate-the-client)
* [Attitydanalys](#sentiment-analysis)
* [Yttrandeutvinning](#opinion-mining)
* [Språkidentifiering](#language-detection)
* [Igenkänning av namngiven entitet](#named-entity-recognition-ner) 
* [Igenkänning av personligt identifierbar information](#personally-identifiable-information-recognition) 
* [Entitetslänkning](#entity-linking)
* [Extrahering av nyckelfraser](#key-phrase-extraction)


# <a name="version-30"></a>[Version 3.0](#tab/version-3)

* [Autentisera klienten](#authenticate-the-client)
* [Attitydanalys](#sentiment-analysis)
* [Språkidentifiering](#language-detection)
* [Igenkänning av namngiven entitet](#named-entity-recognition-ner) 
* [Entitetslänkning](#entity-linking)
* [Extrahering av nyckelfraser](#key-phrase-extraction)

---

## <a name="authenticate-the-client"></a>Autentisera klienten

# <a name="version-31-preview"></a>[Förhandsversion 3.1](#tab/version-3-1)

Skapa en funktion för att instansiera `TextAnalyticsClient` objektet med and som skapades `key` `endpoint` ovan. Skapa sedan en ny klient. 

```python
from azure.ai.textanalytics import TextAnalyticsClient
from azure.core.credentials import AzureKeyCredential

def authenticate_client():
    ta_credential = AzureKeyCredential(key)
    text_analytics_client = TextAnalyticsClient(
            endpoint=endpoint, 
            credential=ta_credential)
    return text_analytics_client

client = authenticate_client()
```

# <a name="version-30"></a>[Version 3.0](#tab/version-3)

Skapa en funktion för att instansiera `TextAnalyticsClient` objektet med and som skapades `key` `endpoint` ovan. Skapa sedan en ny klient. Observera att `api_version=TextAnalyticsApiVersion.V3_0` ska definieras för användning av version 3.0.

```python
# use this code if you're using SDK version is 5.0.0
from azure.ai.textanalytics import TextAnalyticsClient
from azure.core.credentials import AzureKeyCredential

def authenticate_client():
    ta_credential = AzureKeyCredential(key)
    text_analytics_client = TextAnalyticsClient(
            endpoint=endpoint, 
            credential=ta_credential) 
    return text_analytics_client

client = authenticate_client()
```

Om du har installerat v5.1.0 i klientbiblioteket med kan du ange `pip install azure-ai-textanalytics --pre` v3.0 för Textanalys-API:et med klientens `api_version` parameter. Använd bara följande `authenticate_client()` metod om klienten är v5.1.0 eller senare.

```python
# Only use the following code sample if you're using v5.1.0 of the client library, 
# and are looking to specify v3.0 of the Text Analytics API for your client
from azure.ai.textanalytics import TextAnalyticsClient, TextAnalyticsApiVersion
from azure.core.credentials import AzureKeyCredential
def authenticate_client():
   ta_credential = AzureKeyCredential(key)
   text_analytics_client = TextAnalyticsClient(
     endpoint=endpoint,
     credential=ta_credential,
     api_version=TextAnalyticsApiVersion.V3_0
   )
   
client = authenticate_client()
```

--- 

## <a name="sentiment-analysis"></a>Attitydanalys

# <a name="version-31-preview"></a>[Version 3.1 –förhandsversion](#tab/version-3-1)

Skapa en ny funktion med `sentiment_analysis_example()` namnet som tar klienten som ett argument och anropar sedan funktionen `analyze_sentiment()` . Det returnerade svarsobjektet innehåller sentimentetiketten och poängen för hela indatadokumentet, samt en attitydanalys för varje mening.


```python
def sentiment_analysis_example(client):

    documents = ["I had the best day of my life. I wish you were there with me."]
    response = client.analyze_sentiment(documents=documents)[0]
    print("Document Sentiment: {}".format(response.sentiment))
    print("Overall scores: positive={0:.2f}; neutral={1:.2f}; negative={2:.2f} \n".format(
        response.confidence_scores.positive,
        response.confidence_scores.neutral,
        response.confidence_scores.negative,
    ))
    for idx, sentence in enumerate(response.sentences):
        print("Sentence: {}".format(sentence.text))
        print("Sentence {} sentiment: {}".format(idx+1, sentence.sentiment))
        print("Sentence score:\nPositive={0:.2f}\nNeutral={1:.2f}\nNegative={2:.2f}\n".format(
            sentence.confidence_scores.positive,
            sentence.confidence_scores.neutral,
            sentence.confidence_scores.negative,
        ))
          
sentiment_analysis_example(client)
```

### <a name="output"></a>Utdata

```console
Document Sentiment: positive
Overall scores: positive=1.00; neutral=0.00; negative=0.00 

Sentence: I had the best day of my life.
Sentence 1 sentiment: positive
Sentence score:
Positive=1.00
Neutral=0.00
Negative=0.00

Sentence: I wish you were there with me.
Sentence 2 sentiment: neutral
Sentence score:
Positive=0.21
Neutral=0.77
Negative=0.02
```

## <a name="opinion-mining"></a>Yttrandeutvinning

För att kunna göra attitydanalys med yttrandeutvinning skapar du en ny funktion med namnet som tar klienten som argument och anropar sedan `sentiment_analysis_with_opinion_mining_example()` `analyze_sentiment()` funktionen med alternativflaggan `show_opinion_mining=True` . Det returnerade svarsobjektet innehåller inte bara sentimentetiketten och poängen för hela indatadokumentet med attitydanalys för varje mening, utan även attitydanalys på aspekt- och attitydnivå.


```python
def sentiment_analysis_with_opinion_mining_example(client):

    documents = [
        "The food and service were unacceptable, but the concierge were nice"
    ]

    result = client.analyze_sentiment(documents, show_opinion_mining=True)
    doc_result = [doc for doc in result if not doc.is_error]

    positive_reviews = [doc for doc in doc_result if doc.sentiment == "positive"]
    negative_reviews = [doc for doc in doc_result if doc.sentiment == "negative"]

    positive_mined_opinions = []
    mixed_mined_opinions = []
    negative_mined_opinions = []

    for document in doc_result:
        print("Document Sentiment: {}".format(document.sentiment))
        print("Overall scores: positive={0:.2f}; neutral={1:.2f}; negative={2:.2f} \n".format(
            document.confidence_scores.positive,
            document.confidence_scores.neutral,
            document.confidence_scores.negative,
        ))
        for sentence in document.sentences:
            print("Sentence: {}".format(sentence.text))
            print("Sentence sentiment: {}".format(sentence.sentiment))
            print("Sentence score:\nPositive={0:.2f}\nNeutral={1:.2f}\nNegative={2:.2f}\n".format(
                sentence.confidence_scores.positive,
                sentence.confidence_scores.neutral,
                sentence.confidence_scores.negative,
            ))
            for mined_opinion in sentence.mined_opinions:
                target = mined_opinion.target
                print("......'{}' target '{}'".format(target.sentiment, target.text))
                print("......Target score:\n......Positive={0:.2f}\n......Negative={1:.2f}\n".format(
                    target.confidence_scores.positive,
                    target.confidence_scores.negative,
                ))
                for assessment in mined_opinion.assessments:
                    print("......'{}' assessment '{}'".format(assessment.sentiment, assessment.text))
                    print("......Assessment score:\n......Positive={0:.2f}\n......Negative={1:.2f}\n".format(
                        assessment.confidence_scores.positive,
                        assessment.confidence_scores.negative,
                    ))
            print("\n")
        print("\n")
          
sentiment_analysis_with_opinion_mining_example(client)
```

### <a name="output"></a>Utdata

```console
Document Sentiment: positive
Overall scores: positive=0.84; neutral=0.00; negative=0.16

Sentence: The food and service were unacceptable, but the concierge were nice
Sentence sentiment: positive
Sentence score:
Positive=0.84
Neutral=0.00
Negative=0.16

......'negative' target 'food'
......Target score:
......Positive=0.01
......Negative=0.99

......'negative' assessment 'unacceptable'
......Assessment score:
......Positive=0.01
......Negative=0.99

......'negative' target 'service'
......Target score:
......Positive=0.01
......Negative=0.99

......'negative' assessment 'unacceptable'
......Assessment score:
......Positive=0.01
......Negative=0.99

......'positive' target 'concierge'
......Target score:
......Positive=1.00
......Negative=0.00

......'positive' assessment 'nice'
......Assessment score:
......Positive=1.00
......Negative=0.00





Press any key to continue . . .

```

# <a name="version-30"></a>[Version 3.0](#tab/version-3)

Skapa en ny funktion med `sentiment_analysis_example()` namnet som tar klienten som ett argument och anropar sedan `analyze_sentiment()` funktionen. Det returnerade svarsobjektet innehåller sentimentetiketten och poängen för hela indatadokumentet, samt en attitydanalys för varje mening.


```python
def sentiment_analysis_example(client):

    documents = ["I had the best day of my life. I wish you were there with me."]
    response = client.analyze_sentiment(documents = documents)[0]
    print("Document Sentiment: {}".format(response.sentiment))
    print("Overall scores: positive={0:.2f}; neutral={1:.2f}; negative={2:.2f} \n".format(
        response.confidence_scores.positive,
        response.confidence_scores.neutral,
        response.confidence_scores.negative,
    ))
    for idx, sentence in enumerate(response.sentences):
        print("Sentence: {}".format(sentence.text))
        print("Sentence {} sentiment: {}".format(idx+1, sentence.sentiment))
        print("Sentence score:\nPositive={0:.2f}\nNeutral={1:.2f}\nNegative={2:.2f}\n".format(
            sentence.confidence_scores.positive,
            sentence.confidence_scores.neutral,
            sentence.confidence_scores.negative,
        ))
          
sentiment_analysis_example(client)
```

### <a name="output"></a>Utdata

```console
Document Sentiment: positive
Overall scores: positive=1.00; neutral=0.00; negative=0.00 

Sentence: I had the best day of my life.
Sentence 1 sentiment: positive
Sentence score:
Positive=1.00
Neutral=0.00
Negative=0.00

Sentence: I wish you were there with me.
Sentence 2 sentiment: neutral
Sentence score:
Positive=0.21
Neutral=0.77
Negative=0.02
```

---

## <a name="language-detection"></a>Språkidentifiering

# <a name="version-31-preview"></a>[Version 3.1 –förhandsversion](#tab/version-3-1)

Skapa en ny funktion med `language_detection_example()` namnet som tar klienten som ett argument och anropar sedan `detect_language()` funktionen. Det returnerade svarsobjektet innehåller det identifierade språket i `primary_language` om det lyckas och ett om `error` inte.

> [!Tip]
> I vissa fall kan det vara svårt att disamuera språk baserat på indata. Du kan använda `country_hint` parametern för att ange en landskod med två bokstäver. Som standard använder API:et "US" som standard countryHint för att ta bort det här beteendet kan du återställa den här parametern genom att ange värdet till tom sträng `country_hint : ""` . 

```python
def language_detection_example(client):
    try:
        documents = ["Ce document est rédigé en Français."]
        response = client.detect_language(documents = documents, country_hint = 'us')[0]
        print("Language: ", response.primary_language.name)

    except Exception as err:
        print("Encountered exception. {}".format(err))
language_detection_example(client)
```


### <a name="output"></a>Utdata

```console
Language:  French
```

# <a name="version-30"></a>[Version 3.0](#tab/version-3)

Skapa en ny funktion med `language_detection_example()` namnet som tar klienten som ett argument och anropar sedan `detect_language()` funktionen. Det returnerade svarsobjektet innehåller det identifierade språket i `primary_language` om det lyckas och ett om `error` inte.

> [!Tip]
> I vissa fall kan det vara svårt att disamuera språk baserat på indata. Du kan använda `country_hint` parametern för att ange en landskod med två bokstäver. Som standard använder API:et "US" som standard countryHint. Om du vill ta bort det här beteendet kan du återställa den här parametern genom att ange värdet till tom sträng `country_hint : ""` . 

```python
def language_detection_example(client):
    try:
        documents = ["Ce document est rédigé en Français."]
        response = client.detect_language(documents = documents, country_hint = 'us')[0]
        print("Language: ", response.primary_language.name)

    except Exception as err:
        print("Encountered exception. {}".format(err))
language_detection_example(client)
```


### <a name="output"></a>Utdata

```console
Language:  French
```


---

## <a name="named-entity-recognition-ner"></a>Igenkänning av namngiven entitet (NER)

# <a name="version-31-preview"></a>[Förhandsversion 3.1](#tab/version-3-1)

> [!NOTE]
> I version `3.1` : 
> * Entitetslänkning är en separat begäran än NER.

Skapa en ny funktion `entity_recognition_example` med namnet som tar klienten som ett argument, anropar sedan funktionen och `recognize_entities()` itererar genom resultatet. Det returnerade svarsobjektet innehåller listan över identifierade entiteter i om `entity` det lyckas och om `error` inte. För varje identifierat entitet skriver du ut dess Kategori och Sub-Category om finns.

```python
def entity_recognition_example(client):

    try:
        documents = ["I had a wonderful trip to Seattle last week."]
        result = client.recognize_entities(documents = documents)[0]

        print("Named Entities:\n")
        for entity in result.entities:
            print("\tText: \t", entity.text, "\tCategory: \t", entity.category, "\tSubCategory: \t", entity.subcategory,
                    "\n\tConfidence Score: \t", round(entity.confidence_score, 2), "\tLength: \t", entity.length, "\tOffset: \t", entity.offset, "\n")

    except Exception as err:
        print("Encountered exception. {}".format(err))
entity_recognition_example(client)
```

### <a name="output"></a>Utdata

```console
Named Entities:

        Text:    trip   Category:        Event  SubCategory:     None
        Confidence Score:        0.61   Length:          4      Offset:          18

        Text:    Seattle        Category:        Location       SubCategory:     GPE
        Confidence Score:        0.82   Length:          7      Offset:          26

        Text:    last week      Category:        DateTime       SubCategory:     DateRange
        Confidence Score:        0.8    Length:          9      Offset:          34
```

### <a name="entity-linking"></a>Entity Linking

Skapa en ny funktion `entity_linking_example()` med namnet som tar klienten som ett argument, anropar sedan funktionen och `recognize_linked_entities()` itererar genom resultatet. Det returnerade svarsobjektet innehåller listan över identifierade entiteter i om `entities` det lyckas och om `error` inte. Eftersom länkade entiteter identifieras unikt grupperas förekomster av samma entitet under ett `entity` objekt som en lista med `match` objekt.

```python
def entity_linking_example(client):

    try:
        documents = ["""Microsoft was founded by Bill Gates and Paul Allen on April 4, 1975, 
        to develop and sell BASIC interpreters for the Altair 8800. 
        During his career at Microsoft, Gates held the positions of chairman,
        chief executive officer, president and chief software architect, 
        while also being the largest individual shareholder until May 2014."""]
        result = client.recognize_linked_entities(documents = documents)[0]

        print("Linked Entities:\n")
        for entity in result.entities:
            print("\tName: ", entity.name, "\tId: ", entity.data_source_entity_id, "\tUrl: ", entity.url,
            "\n\tData Source: ", entity.data_source)
            print("\tMatches:")
            for match in entity.matches:
                print("\t\tText:", match.text)
                print("\t\tConfidence Score: {0:.2f}".format(match.confidence_score))
                print("\t\tOffset: {}".format(match.offset))
                print("\t\tLength: {}".format(match.length))
            
    except Exception as err:
        print("Encountered exception. {}".format(err))
entity_linking_example(client)
```

### <a name="output"></a>Utdata

```console
Linked Entities:

        Name:  Microsoft        Id:  Microsoft  Url:  https://en.wikipedia.org/wiki/Microsoft
        Data Source:  Wikipedia
        Matches:
                Text: Microsoft
                Confidence Score: 0.55
                Offset: 0
                Length: 9
                Text: Microsoft
                Confidence Score: 0.55
                Offset: 168
                Length: 9
        Name:  Bill Gates       Id:  Bill Gates         Url:  https://en.wikipedia.org/wiki/Bill_Gates
        Data Source:  Wikipedia
        Matches:
                Text: Bill Gates
                Confidence Score: 0.63
                Offset: 25
                Length: 10
                Text: Gates
                Confidence Score: 0.63
                Offset: 179
                Length: 5
        Name:  Paul Allen       Id:  Paul Allen         Url:  https://en.wikipedia.org/wiki/Paul_Allen
        Data Source:  Wikipedia
        Matches:
                Text: Paul Allen
                Confidence Score: 0.60
                Offset: 40
                Length: 10
        Name:  April 4  Id:  April 4    Url:  https://en.wikipedia.org/wiki/April_4
        Data Source:  Wikipedia
        Matches:
                Text: April 4
                Confidence Score: 0.32
                Offset: 54
                Length: 7
        Name:  BASIC    Id:  BASIC      Url:  https://en.wikipedia.org/wiki/BASIC
        Data Source:  Wikipedia
        Matches:
                Text: BASIC
                Confidence Score: 0.33
                Offset: 98
                Length: 5
        Name:  Altair 8800      Id:  Altair 8800        Url:  https://en.wikipedia.org/wiki/Altair_8800
        Data Source:  Wikipedia
        Matches:
                Text: Altair 8800
                Confidence Score: 0.88
                Offset: 125
                Length: 11
```

### <a name="personally-identifiable-information-recognition"></a>Igenkänning av personligt identifierbar information

Skapa en ny funktion `pii_recognition_example` med namnet som tar klienten som ett argument, anropar sedan funktionen och `recognize_pii_entities()` itererar genom resultatet. Det returnerade svarsobjektet innehåller listan över identifierade entiteter i om `entity` det lyckas och om `error` inte. För varje identifierat entitet skriver du ut dess Kategori och Sub-Category om finns.

```python
def pii_recognition_example(client):
    documents = [
        "The employee's SSN is 859-98-0987.",
        "The employee's phone number is 555-555-5555."
    ]
    response = client.recognize_pii_entities(documents, language="en")
    result = [doc for doc in response if not doc.is_error]
    for doc in result:
        print("Redacted Text: {}".format(doc.redacted_text))
        for entity in doc.entities:
            print("Entity: {}".format(entity.text))
            print("\tCategory: {}".format(entity.category))
            print("\tConfidence Score: {}".format(entity.confidence_score))
            print("\tOffset: {}".format(entity.offset))
            print("\tLength: {}".format(entity.length))
pii_recognition_example(client)
```

### <a name="output"></a>Utdata

```console
Redacted Text: The employee's SSN is ***********.
Entity: 859-98-0987
        Category: U.S. Social Security Number (SSN)
        Confidence Score: 0.65
        Offset: 22
        Length: 11
Redacted Text: The employee's phone number is ************.
Entity: 555-555-5555
        Category: Phone Number
        Confidence Score: 0.8
        Offset: 31
        Length: 12
```

# <a name="version-30"></a>[Version 3.0](#tab/version-3)

> [!NOTE]
> I version `3.0` : 
> * Entitetslänkning är en separat begäran än NER.

Skapa en ny funktion `entity_recognition_example` med namnet som tar klienten som ett argument, anropar sedan funktionen och `recognize_entities()` itererar genom resultatet. Det returnerade svarsobjektet innehåller listan över identifierade entiteter i `entity` om det lyckas och om `error` inte. För varje identifierat entitet skriver du ut dess Kategori och Sub-Category om det finns.

```python
def entity_recognition_example(client):

    try:
        documents = ["I had a wonderful trip to Seattle last week."]
        result = client.recognize_entities(documents = documents)[0]

        print("Named Entities:\n")
        for entity in result.entities:
            print("\tText: \t", entity.text, "\tCategory: \t", entity.category, "\tSubCategory: \t", entity.subcategory,
                    "\n\tConfidence Score: \t", round(entity.confidence_score, 2), "\n")

    except Exception as err:
        print("Encountered exception. {}".format(err))
entity_recognition_example(client)
```

### <a name="output"></a>Utdata

```console
Named Entities:

        Text:    trip   Category:        Event  SubCategory:     None
        Confidence Score:        0.61

        Text:    Seattle        Category:        Location       SubCategory:     GPE
        Confidence Score:        0.82

        Text:    last week      Category:        DateTime       SubCategory:     DateRange
        Confidence Score:        0.8
```

### <a name="entity-linking"></a>Entity Linking

Skapa en ny funktion `entity_linking_example()` med namnet som tar klienten som ett argument och anropar `recognize_linked_entities()` sedan funktionen och itererar genom resultatet. Det returnerade svarsobjektet innehåller listan över identifierade entiteter i `entities` om det lyckas och om `error` inte. Eftersom länkade entiteter identifieras unikt grupperas förekomster av samma entitet under ett `entity` objekt som en lista med `match` objekt.

```python
def entity_linking_example(client):

    try:
        documents = ["""Microsoft was founded by Bill Gates and Paul Allen on April 4, 1975, 
        to develop and sell BASIC interpreters for the Altair 8800. 
        During his career at Microsoft, Gates held the positions of chairman,
        chief executive officer, president and chief software architect, 
        while also being the largest individual shareholder until May 2014."""]
        result = client.recognize_linked_entities(documents = documents)[0]

        print("Linked Entities:\n")
        for entity in result.entities:
            print("\tName: ", entity.name, "\tId: ", entity.data_source_entity_id, "\tUrl: ", entity.url,
            "\n\tData Source: ", entity.data_source)
            print("\tMatches:")
            for match in entity.matches:
                print("\t\tText:", match.text)
                print("\t\tConfidence Score: {0:.2f}".format(match.confidence_score))
            
    except Exception as err:
        print("Encountered exception. {}".format(err))
entity_linking_example(client)
```

### <a name="output"></a>Utdata

```console
Linked Entities:

        Name:  Altair 8800      Id:  Altair 8800        Url:  https://en.wikipedia.org/wiki/Altair_8800
        Data Source:  Wikipedia
        Matches:
                Text: Altair 8800
                Confidence Score: 0.88
        Name:  Bill Gates       Id:  Bill Gates         Url:  https://en.wikipedia.org/wiki/Bill_Gates
        Data Source:  Wikipedia
        Matches:
                Text: Bill Gates
                Confidence Score: 0.63
                Text: Gates
                Confidence Score: 0.63
        Name:  Paul Allen       Id:  Paul Allen         Url:  https://en.wikipedia.org/wiki/Paul_Allen
        Data Source:  Wikipedia
        Matches:
                Text: Paul Allen
                Confidence Score: 0.60
        Name:  Microsoft        Id:  Microsoft  Url:  https://en.wikipedia.org/wiki/Microsoft
        Data Source:  Wikipedia
        Matches:
                Text: Microsoft
                Confidence Score: 0.55
                Text: Microsoft
                Confidence Score: 0.55
        Name:  April 4  Id:  April 4    Url:  https://en.wikipedia.org/wiki/April_4
        Data Source:  Wikipedia
        Matches:
                Text: April 4
                Confidence Score: 0.32
        Name:  BASIC    Id:  BASIC      Url:  https://en.wikipedia.org/wiki/BASIC
        Data Source:  Wikipedia
        Matches:
                Text: BASIC
                Confidence Score: 0.33
```

---

### <a name="key-phrase-extraction"></a>Extrahering av nyckelfraser

# <a name="version-31-preview"></a>[Version 3.1 –förhandsversion](#tab/version-3-1)

Skapa en ny funktion med `key_phrase_extraction_example()` namnet som tar klienten som ett argument och anropar sedan funktionen `extract_key_phrases()` . Resultatet innehåller en lista över identifierade nyckelfraser i om `key_phrases` det lyckas och en om `error` inte. Skriv ut identifierade nyckelfraser.

```python
def key_phrase_extraction_example(client):

    try:
        documents = ["My cat might need to see a veterinarian."]

        response = client.extract_key_phrases(documents = documents)[0]

        if not response.is_error:
            print("\tKey Phrases:")
            for phrase in response.key_phrases:
                print("\t\t", phrase)
        else:
            print(response.id, response.error)

    except Exception as err:
        print("Encountered exception. {}".format(err))
        
key_phrase_extraction_example(client)
```


### <a name="output"></a>Utdata

```console
    Key Phrases:
         cat
         veterinarian
```

# <a name="version-30"></a>[Version 3.0](#tab/version-3)

Skapa en ny funktion med `key_phrase_extraction_example()` namnet som tar klienten som ett argument och anropar sedan `extract_key_phrases()` funktionen. Resultatet innehåller en lista över identifierade nyckelfraser i om `key_phrases` det lyckas, och en `error` om inte. Skriv ut identifierade nyckelfraser.

```python
def key_phrase_extraction_example(client):

    try:
        documents = ["My cat might need to see a veterinarian."]

        response = client.extract_key_phrases(documents = documents)[0]

        if not response.is_error:
            print("\tKey Phrases:")
            for phrase in response.key_phrases:
                print("\t\t", phrase)
        else:
            print(response.id, response.error)

    except Exception as err:
        print("Encountered exception. {}".format(err))
        
key_phrase_extraction_example(client)
```


### <a name="output"></a>Utdata

```console
    Key Phrases:
         cat
         veterinarian
```


---

## <a name="use-the-api-asynchronously-with-the-batch-analyze-operation"></a>Använda API:et asynkront med batchanalysåtgärden

# <a name="version-31-preview"></a>[Version 3.1 –förhandsversion](#tab/version-3-1)

[!INCLUDE [Analyze operation pricing](../analyze-operation-pricing-caution.md)]

Skapa en ny funktion med `analyze_batch_example()` namnet som tar klienten som ett argument och anropar sedan funktionen `begin_analyze_batch_actions()` . Resultatet blir en långvarig åtgärd som avsöks efter resultat.

```python
from azure.ai.textanalytics import (
    RecognizeEntitiesAction
)

def analyze_batch_example(client):
        documents = [
            "Microsoft was founded by Bill Gates and Paul Allen."
        ]

        poller = client.begin_analyze_batch_actions(
            documents,
            display_name="Sample Text Analysis",
            actions=[RecognizeEntitiesAction()]
        )

        result = poller.result()
        action_results = [action_result for action_result in list(result) if not action_result.is_error]

        entities_recognition_task_result = action_results[0]
        print("Results of Entities Recognition action:")
        docs = [doc for doc in entities_recognition_task_result.document_results if not doc.is_error]

        for idx, doc in enumerate(docs):
            print("\nDocument text: {}".format(documents[idx]))
            for entity in doc.entities:
                print("Entity: {}".format(entity.text))
                print("...Category: {}".format(entity.category))
                print("...Confidence Score: {}".format(entity.confidence_score))
                print("...Offset: {}".format(entity.offset))
            print("------------------------------------------")

analyze_batch_example(client)
```

### <a name="output"></a>Utdata

```console
Results of Entities Recognition task:
Document text: Microsoft was founded by Bill Gates and Paul Allen.
Entity: Microsoft
...Category: Organization
...Confidence Score: 0.83
...Offset: 0
Entity: Bill Gates
...Category: Person
...Confidence Score: 0.85
...Offset: 25
Entity: Paul Allen
...Category: Person
...Confidence Score: 0.9
...Offset: 40
------------------------------------------
```

Du kan också använda batchanalysåtgärden för att identifiera PII och utföra extrahering av nyckelfraser. Se [exemplet för batchanalys](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/textanalytics/azure-ai-textanalytics/samples/sample_analyze_batch_actions.py) på GitHub.

# <a name="version-30"></a>[Version 3.0](#tab/version-3)

Den här funktionen är inte tillgänglig i version 3.0.

---
