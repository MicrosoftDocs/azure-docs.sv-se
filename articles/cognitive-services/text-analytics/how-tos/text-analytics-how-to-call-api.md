---
title: Anropa API:t för textanalys
titleSuffix: Azure Cognitive Services
description: Den här artikeln förklarar hur du kan anropa Azure-Cognitive Services Textanalys REST API och Postman.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 12/17/2020
ms.author: aahi
ms.custom: references_regions
ms.openlocfilehash: 9302bde13a303dda2107900dc0c10cc180669a18
ms.sourcegitcommit: 227b9a1c120cd01f7a39479f20f883e75d86f062
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/18/2021
ms.locfileid: "100650736"
---
# <a name="how-to-call-the-text-analytics-rest-api"></a>Så här anropar du Textanalys REST API

I den här artikeln använder vi Textanalys REST API och [Postman](https://www.postman.com/downloads/) för att demonstrera viktiga begrepp. API: et tillhandahåller flera synkrona och asynkrona slut punkter för att använda funktionerna i tjänsten. 

## <a name="create-a-text-analytics-resource"></a>Skapa en Textanalys resurs

> [!NOTE]
> * Du behöver en Textanalys-resurs som använder en standard [pris nivå](https://azure.microsoft.com/pricing/details/cognitive-services/text-analytics/) (S) om du vill använda- `/analyze` eller- `/health` slut punkterna. `/analyze`Slut punkten ingår i [pris nivån](https://azure.microsoft.com/pricing/details/cognitive-services/text-analytics/).

Innan du använder API för textanalys måste du skapa en Azure-resurs med en nyckel och slut punkt för dina program. 

1.  Börja med att gå till [Azure Portal](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics) och skapa en ny textanalys resurs, om du inte redan har en. Välj en [pris nivå](https://azure.microsoft.com/pricing/details/cognitive-services/text-analytics/).

2.  Välj den region som du vill använda för slut punkten.  Observera att `/analyze` och `/health` slut punkterna endast är tillgängliga i följande regioner: västra USA 2, östra USA 2, centrala USA, norra Europa och Västeuropa.

3.  Skapa Textanalys resursen och gå till bladet "nycklar och slut punkt" till vänster på sidan. Kopiera nyckeln som ska användas senare när du anropar API: erna. Du lägger till detta senare som ett värde för `Ocp-Apim-Subscription-Key` sidhuvudet.

## <a name="change-your-pricing-tier"></a>Ändra din pris nivå 

Om du har en befintlig Textanalys resurs med hjälp av pris nivån S0 till S4, bör du uppdatera den för att använda [pris nivån](https://azure.microsoft.com/pricing/details/cognitive-services/text-analytics/)standard (S). Pris nivåerna S0 till S4 kommer att dras tillbaka. Så här uppdaterar du din resurs priser:

1. Navigera till din Textanalys-resurs i [Azure Portal](https://portal.azure.com/).
2. Välj **pris nivå** i den vänstra navigerings menyn. Den kommer att vara under **resurs hantering**. 
3. Välj pris nivån standard (S). Klicka sedan på **Välj**.

Du kan också skapa en ny Textanalys resurs med pris nivån standard (S) och migrera dina program så att de använder autentiseringsuppgifterna för den nya resursen. 

## <a name="using-the-api-synchronously"></a>Använda API: et synkront

Du kan anropa Textanalys synkront (för scenarier med låg latens). Du måste anropa varje API (funktion) separat när du använder synkront API. Om du behöver anropa flera funktioner kan du läsa avsnittet nedan om hur du anropar Textanalys asynkront. 

## <a name="using-the-api-asynchronously"></a>Använda API asynkront

Från och med v 3.1 – för hands version. 3 tillhandahåller API för textanalys två asynkrona slut punkter: 

* Med `/analyze` slut punkten för textanalys kan du analysera samma uppsättning text dokument med flera text analys funktioner i ett API-anrop. Om du tidigare vill använda flera funktioner måste du göra separata API-anrop för varje åtgärd. Överväg den här funktionen när du behöver analysera stora uppsättningar med dokument med mer än en Textanalys-funktion.

* `/health`Slut punkten för textanalys för hälsa, som kan extrahera och märka relevant medicinsk information från kliniska dokument.  

Se tabellen nedan för att se vilka funktioner som kan användas asynkront. Observera att endast några få funktioner kan anropas från `/analyze` slut punkten. 

| Funktion | Synkront | Asynkrona |
|--|--|--|
| Språkidentifiering | ✔ |  |
| Attitydanalys | ✔ |  |
| Åsikts utvinning | ✔ |  |
| Extrahering av nyckelfraser | ✔ | ✔* |
| Identifiering av namngiven entitet (inklusive PII och PHI) | ✔ | ✔* |
| Textanalys för hälsa (container) | ✔ |  |
| Textanalys för hälso tillstånd (API) |  | ✔  |

`*` -Anropade asynkront via `/analyze` slut punkten.


[!INCLUDE [text-analytics-api-references](../includes/text-analytics-api-references.md)]

<a name="json-schema"></a>

## <a name="api-request-formats"></a>Format för API-begäran

Du kan skicka både synkrona och asynkrona anrop till API för textanalys.

#### <a name="synchronous"></a>[Synkront](#tab/synchronous)

### <a name="synchronous-requests"></a>Synkrona begär Anden

Formatet för API-begäranden är detsamma för alla synkrona åtgärder. Dokument skickas i ett JSON-objekt som rå ostrukturerad text. XML stöds inte. JSON-schemat består av de element som beskrivs nedan.

| Element | Giltiga värden | Obligatoriskt? | Användning |
|---------|--------------|-----------|-------|
|`id` |Data typen är sträng, men i dokument-ID: n är det vanligt vis heltal. | Obligatorisk | Systemet använder de ID: n som du anger för att strukturera utdata. Språk koder, nyckel fraser och sentiment resultat genereras för varje ID i begäran.|
|`text` | Ostrukturerad rå text, upp till 5 120 tecken. | Obligatorisk | För språk identifiering kan text uttryckas på valfritt språk. För sentiment analys, extrahering av nyckel fraser och enhets identifiering måste texten vara på ett [språk som stöds](../language-support.md). |
|`language` | 2 teckens [ISO 639-1](https://en.wikipedia.org/wiki/List_of_ISO_639-1_codes) -kod för ett [språk som stöds](../language-support.md) | Det varierar | Krävs för sentiment-analys, extrahering av nyckel fraser och länkning av entiteter. valfritt för språk identifiering. Det finns inget fel om du exkluderar det, men analysen har minskats utan den. Språk koden ska motsvara den `text` du anger. |

Följande är ett exempel på en API-begäran för synkrona Textanalys-slutpunkter. 

```json
{
  "documents": [
    {
      "language": "en",
      "id": "1",
      "text": "Sample text to be sent to the text analytics api."
    }
  ]
}
```

#### <a name="asynchronous"></a>[Asynkrona](#tab/asynchronous)

### <a name="asynchronous-requests-to-the-analyze-endpoint"></a>Asynkrona begär anden till `/analyze` slut punkten

> [!NOTE]
> Den senaste för hands versionen av Textanalys klient biblioteket gör att du kan anropa asynkrona analys åtgärder med ett klient objekt. Du kan hitta exempel på GitHub:
* [C#](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/textanalytics/Azure.AI.TextAnalytics)
* [Python](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/textanalytics/azure-ai-textanalytics/)
* [Java](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/textanalytics/azure-ai-textanalytics)

Med `/analyze` slut punkten kan du välja vilken av de textanalys funktioner som stöds som du vill använda i ett enda API-anrop. Den här slut punkten stöder för närvarande:

* extrahering av nyckel fraser 
* Identifiering av namngiven entitet (inklusive PII och PHI)

| Element | Giltiga värden | Obligatoriskt? | Användning |
|---------|--------------|-----------|-------|
|`displayName` | Sträng | Valfritt | Används som visnings namn för den unika identifieraren för jobbet.|
|`analysisInput` | Innehåller `documents` fältet nedan | Obligatorisk | Innehåller informationen för de dokument som du vill skicka. |
|`documents` | Innehåller `id` fälten och `text` nedan | Obligatorisk | Innehåller information för varje dokument som skickas och dokumentets rå text. |
|`id` | Sträng | Obligatorisk | De ID: n som du anger används för att strukturera utdata. |
|`text` | Ostrukturerad rå text, upp till 125 000 tecken. | Obligatorisk | Måste vara på det engelska språket, vilket är det enda språk som stöds för närvarande. |
|`tasks` | Innehåller följande Textanalys funktioner: `entityRecognitionTasks` , `keyPhraseExtractionTasks` eller `entityRecognitionPiiTasks` . | Obligatorisk | En eller flera av de Textanalys-funktioner som du vill använda. Observera att `entityRecognitionPiiTasks` har en valfri `domain` parameter som kan anges till `pii` eller `phi` . Om inget anges används standardvärdet i systemet `pii` . |
|`parameters` | Innehåller `model-version` fälten och `stringIndexType` nedan | Obligatorisk | Det här fältet ingår i ovanstående funktions uppgifter som du väljer. De innehåller information om modell versionen som du vill använda och index typen. |
|`model-version` | Sträng | Obligatorisk | Ange vilken version av modellen som du vill använda.  |
|`stringIndexType` | Sträng | Obligatorisk | Ange den text avkodare som matchar din programmerings miljö.  De typer som stöds är `textElement_v8` (standard), `unicodeCodePoint` , `utf16CodeUnit` . Mer information finns i [artikeln text förskjutning](../concepts/text-offsets.md#offsets-in-api-version-31-preview) .  |
|`domain` | Sträng | Valfritt | Gäller endast för en parameter för `entityRecognitionPiiTasks` uppgiften och kan anges till `pii` eller `phi` . Den används som standard `pii` om inget värde anges.  |

```json
{
    "displayName": "My Job",
    "analysisInput": {
        "documents": [
            {
                "id": "doc1",
                "text": "It's incredibly sunny outside! I'm so happy"
            },
            {
                "id": "doc2",
                "text": "Pike place market is my favorite Seattle attraction."
            }
        ]
    },
    "tasks": {
        "entityRecognitionTasks": [
            {
                "parameters": {
                    "model-version": "latest",
                    "stringIndexType": "TextElements_v8"
                }
            }
        ],
        "keyPhraseExtractionTasks": [{
            "parameters": {
                "model-version": "latest"
            }
        }],
        "entityRecognitionPiiTasks": [{
            "parameters": {
                "model-version": "latest"
            }
        }]
    }
}

```

### <a name="asynchronous-requests-to-the-health-endpoint"></a>Asynkrona begär anden till `/health` slut punkten

Formatet för API-begäranden till Textanalys för hälso värdbaserade API: er är detsamma som för dess behållare. Dokument skickas i ett JSON-objekt som rå ostrukturerad text. XML stöds inte. JSON-schemat består av de element som beskrivs nedan.  Fyll i och skicka [formuläret Cognitive Services begäran](https://aka.ms/csgate) för att begära åtkomst till textanalys för offentlig för hands version av hälso tillstånd. Du debiteras inte för Textanalys för hälso tillstånds användning. 

| Element | Giltiga värden | Obligatoriskt? | Användning |
|---------|--------------|-----------|-------|
|`id` |Data typen är sträng, men i dokument-ID: n är det vanligt vis heltal. | Obligatorisk | Systemet använder de ID: n som du anger för att strukturera utdata. |
|`text` | Ostrukturerad rå text, upp till 5 120 tecken. | Obligatorisk | Observera att endast engelsk text stöds för närvarande. |
|`language` | 2 teckens [ISO 639-1](https://en.wikipedia.org/wiki/List_of_ISO_639-1_codes) -kod för ett [språk som stöds](../language-support.md) | Obligatorisk | `en`Stöds för närvarande inte. |

Följande är ett exempel på en API-begäran för Textanalys för hälso slut punkter. 

```json
example.json

{
  "documents": [
    {
      "language": "en",
      "id": "1",
      "text": "Subject was administered 100mg remdesivir intravenously over a period of 120 min"
    }
  ]
}
```

---

>[!TIP]
> Se artikeln [data och frekvens begränsningar](../concepts/data-limits.md) för information om priser och storleks gränser för att skicka Data till API för textanalys.


## <a name="set-up-a-request"></a>Konfigurera en begäran 

I Postman (eller något annat verktyg för webb-API-testning) lägger du till slut punkten för den funktion som du vill använda. Använd tabellen nedan för att hitta rätt slut punkts format och Ersätt `<your-text-analytics-resource>` med resurs slut punkten. Exempel:

`https://my-resource.cognitiveservices.azure.com/text/analytics/v3.0/languages`

#### <a name="synchronous"></a>[Synkront](#tab/synchronous)

### <a name="endpoints-for-sending-synchronous-requests"></a>Slut punkter för att skicka synkrona begär Anden

| Funktion | Typ av begäran | Resurs slut punkter |
|--|--|--|
| Språkidentifiering | POST | `<your-text-analytics-resource>/text/analytics/v3.0/languages` |
| Attitydanalys | POST | `<your-text-analytics-resource>/text/analytics/v3.0/sentiment` |
| Åsikts utvinning | POST | `<your-text-analytics-resource>/text/analytics/v3.0/sentiment?opinionMining=true` |
| Extrahering av nyckelfraser | POST | `<your-text-analytics-resource>/text/analytics/v3.0/keyPhrases` |
| Igenkänning av namngiven entitet – allmänt | POST | `<your-text-analytics-resource>/text/analytics/v3.0/entities/recognition/general` |
| Igenkänning av namngivna enheter – PII | POST | `<your-text-analytics-resource>/text/analytics/v3.0/entities/recognition/pii` |
| Igenkänning av namngiven entitet – Fi | POST |  `<your-text-analytics-resource>/text/analytics/v3.0/entities/recognition/pii?domain=phi` |

#### <a name="asynchronous"></a>[Asynkrona](#tab/asynchronous)

### <a name="endpoints-for-sending-asynchronous-requests-to-the-analyze-endpoint"></a>Slut punkter för att skicka asynkrona begär anden till `/analyze` slut punkten

| Funktion | Typ av begäran | Resurs slut punkter |
|--|--|--|
| Skicka analys jobb | POST | `https://<your-text-analytics-resource>/text/analytics/v3.1-preview.3/analyze` |
| Hämta analys status och resultat | GET | `https://<your-text-analytics-resource>/text/analytics/v3.1-preview.3/analyze/jobs/<Operation-Location>` |

### <a name="endpoints-for-sending-asynchronous-requests-to-the-health-endpoint"></a>Slut punkter för att skicka asynkrona begär anden till `/health` slut punkten

| Funktion | Typ av begäran | Resurs slut punkter |
|--|--|--|
| Skicka Textanalys för hälso tillstånds jobb  | POST | `https://<your-text-analytics-resource>/text/analytics/v3.1-preview.3/entities/health/jobs` |
| Hämta jobb status och resultat | GET | `https://<your-text-analytics-resource>/text/analytics/v3.1-preview.3/entities/health/jobs/<Operation-Location>` |
| Avbryt jobb | DELETE | `https://<your-text-analytics-resource>/text/analytics/v3.1-preview.3/entities/health/jobs/<Operation-Location>` |

--- 

När du har slut punkten, i Postman (eller något annat verktyg för webb-API-testning):

1. Välj typ av begäran för den funktion som du vill använda.
2. Klistra in i slut punkten för den korrekta åtgärd som du vill använda från tabellen ovan.
3. Ange de tre begärandehuvuden:

   + `Ocp-Apim-Subscription-Key`: din åtkomst nyckel, hämtas från Azure Portal
   + `Content-Type`: Application/JSON
   + `Accept`: Application/JSON

    Om du använder Postman bör din begäran se ut ungefär som på följande skärm, förutsatt att en `/keyPhrases` slut punkt används.
    
    ![Skärm bild för begäran med slut punkt och sidhuvud](../media/postman-request-keyphrase-1.png)
    
4. Välj **RAW** som **brödtext**
    
    ![Skärm bild för begäran med inställningar för brödtext](../media/postman-request-body-raw.png)

5. Klistra in vissa JSON-dokument i ett giltigt format. Använd exemplen i avsnittet **API Request format** ovan och mer information och exempel finns i avsnitten nedan:

      + [Språk identifiering](text-analytics-how-to-language-detection.md)
      + [Extrahering av nyckel fraser](text-analytics-how-to-keyword-extraction.md)
      + [Sentiment-analys](text-analytics-how-to-sentiment-analysis.md)
      + [Enhets igenkänning](text-analytics-how-to-entity-linking.md)

## <a name="send-the-request"></a>Skicka begäran

Skicka API-begäran. Om du har gjort anropet till en synkron slut punkt visas svaret omedelbart, som ett enda JSON-dokument med ett objekt för varje dokument-ID som anges i begäran.

Om du har gjort anropet till asynkrona `/analyze` eller `/health` slut punkter kontrollerar du att du har fått en 202-svarskod. Du måste få svaret att visa resultaten:

1. I API-svaret hittar du `Operation-Location` från-huvudet, som identifierar det jobb som du skickade till API: et. 
2. Skapa en GET-begäran för den slut punkt som du använde. Se [tabellen ovan](#set-up-a-request) för slut punkts formatet och granska [API-referens dokumentationen](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1-preview-3/operations/AnalyzeStatus). Exempel:

    `https://my-resource.cognitiveservices.azure.com/text/analytics/v3.1-preview.3/analyze/jobs/<Operation-Location>`

3. Lägg till i `Operation-Location` begäran.

4. Svaret är ett enda JSON-dokument med ett objekt för varje dokument-ID som anges i begäran.

Observera att `/analyze` `/health` resultaten från get-begäran i steg 2 är tillgängliga i 24 timmar från den tidpunkt då jobbet skapades.  Den här tiden anges av `expirationDateTime` värdet i get-svaret.  Efter den här tids perioden rensas resultatet och är inte längre tillgängligt för hämtning.    

## <a name="example-api-responses"></a>Exempel-API-svar
 
# <a name="synchronous"></a>[Synkront](#tab/synchronous)

### <a name="example-responses-for-synchronous-operation"></a>Exempel svar för synkron åtgärd

Svaren på den synkrona slut punkten varierar beroende på vilken slut punkt du använder. Se följande artiklar för exempel svar.

+ [Språk identifiering](text-analytics-how-to-language-detection.md#step-3-view-the-results)
+ [Extrahering av nyckel fraser](text-analytics-how-to-keyword-extraction.md#step-3-view-results)
+ [Sentiment-analys](text-analytics-how-to-sentiment-analysis.md#view-the-results)
+ [Enhets igenkänning](text-analytics-how-to-entity-linking.md#view-results)

# <a name="asynchronous"></a>[Asynkrona](#tab/asynchronous)

### <a name="example-responses-for-asynchronous-operations"></a>Exempel svar för asynkrona åtgärder

Om det lyckas, kommer GET-begäran till `/analyze` slut punkten returnera ett objekt som innehåller de tilldelade aktiviteterna. Till exempel `keyPhraseExtractionTasks`. Dessa aktiviteter innehåller objektet Response från lämplig Textanalys funktion. Se följande artiklar för mer information.

+ [Extrahering av nyckel fraser](text-analytics-how-to-keyword-extraction.md#step-3-view-results)
+ [Enhets igenkänning](text-analytics-how-to-entity-linking.md#view-results)
+ [Textanalys för hälsa](text-analytics-for-health.md#hosted-asynchronous-web-api-response)

--- 

## <a name="see-also"></a>Se även

* [Översikt över Textanalys](../overview.md)
* [Vanliga frågor och svar (FAQ)](../text-analytics-resource-faq.md)</br>
* [Produktsida för textanalys](//go.microsoft.com/fwlink/?LinkID=759712)
* [Använda klient biblioteket för Textanalys](../quickstarts/client-libraries-rest-api.md)
* [Nyheter](../whats-new.md)
