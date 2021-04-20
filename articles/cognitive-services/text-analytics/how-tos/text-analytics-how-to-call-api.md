---
title: Anropa API:t för textanalys
titleSuffix: Azure Cognitive Services
description: Den här artikeln förklarar hur du kan anropa Azure Cognitive Services Textanalys REST API och Postman.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 03/01/2021
ms.author: aahi
ms.custom: references_regions
ms.openlocfilehash: 5790c7c62b9d97df9683773170301b6e09a47667
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/19/2021
ms.locfileid: "107728490"
---
# <a name="how-to-call-the-text-analytics-rest-api"></a>Så här anropar du Textanalys REST API

I den här artikeln använder vi Textanalys REST API [Postman för att](https://www.postman.com/downloads/) demonstrera viktiga begrepp. API:et tillhandahåller flera synkrona och asynkrona slutpunkter för att använda tjänstens funktioner. 

## <a name="create-a-text-analytics-resource"></a>Skapa en Textanalys resurs

> [!NOTE]
> * Du behöver en Textanalys resurs med prisnivån [](https://azure.microsoft.com/pricing/details/cognitive-services/text-analytics/) Standard (S) om du vill använda `/analyze` `/health` slutpunkterna eller . Slutpunkten `/analyze` ingår i [din prisnivå.](https://azure.microsoft.com/pricing/details/cognitive-services/text-analytics/)

Innan du använder Textanalys API måste du skapa en Azure-resurs med en nyckel och slutpunkt för dina program. 

1.  Gå först till [Azure Portal](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics) och skapa en Textanalys resurs, om du inte redan har en. Välj [en prisnivå.](https://azure.microsoft.com/pricing/details/cognitive-services/text-analytics/)

2.  Välj den region som du vill använda för slutpunkten.  Observera att slutpunkterna och endast är tillgängliga i följande `/analyze` `/health` regioner: USA, västra 2, USA, östra 2, USA, centrala, Europa, norra och Europa, västra.

3.  Skapa Textanalys resurs och gå till "nycklar och slutpunktsbladet" till vänster på sidan. Kopiera nyckeln som ska användas senare när du anropar API:erna. Du lägger till detta senare som ett värde för `Ocp-Apim-Subscription-Key` rubriken.

4. Så här kontrollerar du antalet textposter som har skickats med din Textanalys resurs:

    1. Gå till Textanalys resurs i Azure Portal. 
    2. Klicka **på Mått**, som finns under **Övervakning** i den vänstra navigeringsmenyn. 
    3. Välj *Bearbetade textposter* i listrutan för **Mått.**
    
En textpost är 1 000 tecken.

## <a name="change-your-pricing-tier"></a>Ändra prisnivå 

Om du har en Textanalys resurs som använder prisnivån S0 till S4 bör du uppdatera den så att den använder [prisnivån](https://azure.microsoft.com/pricing/details/cognitive-services/text-analytics/)Standard (S). Prisnivåerna S0 till S4 dras tillbaka. Så här uppdaterar du resursens priser:

1. Gå till Textanalys resurs i [Azure Portal](https://portal.azure.com/).
2. Välj **Prisnivå i** den vänstra navigeringsmenyn. Den kommer att vara under **RESURSHANTERING.** 
3. Välj prisnivån Standard (S). Klicka sedan på **Välj**.

Du kan också skapa en Textanalys resurs med prisnivån Standard (S) och migrera dina program för att använda autentiseringsuppgifterna för den nya resursen. 

## <a name="using-the-api-synchronously"></a>Använda API:et synkront

Du kan anropa Textanalys synkront (för scenarier med låg latens). Du måste anropa varje API (funktion) separat när du använder synkront API. Om du behöver anropa flera funktioner kan du läsa avsnittet nedan om hur du anropar Textanalys asynkront. 

## <a name="using-the-api-asynchronously"></a>Använda API:et asynkront

Från och med v3.1-preview.3 Textanalys API:et två asynkrona slutpunkter: 

* Med slutpunkten för Textanalys kan du analysera samma `/analyze` uppsättning textdokument med flera textanalysfunktioner i ett API-anrop. För att kunna använda flera funktioner skulle du tidigare behöva göra separata API-anrop för varje åtgärd. Överväg den här funktionen när du behöver analysera stora uppsättningar dokument med mer än en Textanalys funktion.

* Slutpunkten `/health` för Textanalys för hälsotillstånd, som kan extrahera och märka relevant medicinsk information från medicinska dokument.  

Observera att slutpunkterna /analyze och /health endast är tillgängliga i följande regioner: USA, västra 2, USA, östra 2, USA, centrala, Europa, norra och Europa, västra.

Se tabellen nedan för att se vilka funktioner som kan användas asynkront. Observera att endast några få funktioner kan anropas från `/analyze` slutpunkten. 

| Funktion | Synkront | Asynkrona |
|--|--|--|
| Språkidentifiering | ✔ |  |
| Attitydanalys | ✔ |  |
| Yttrandeutvinning | ✔ |  |
| Extrahering av nyckelfraser | ✔ | ✔* |
| Igenkänning av namngiven entitet (inklusive PII och PHI) | ✔ | ✔* |
| Entitetslänkning | ✔ | ✔* |
| Textanalys för hälsa (container) | ✔ |  |
| Textanalys för hälsa (API) |  | ✔  |

`*` – Anropas asynkront via `/analyze` slutpunkten.


[!INCLUDE [text-analytics-api-references](../includes/text-analytics-api-references.md)]

<a name="json-schema"></a>

## <a name="api-request-formats"></a>FORMAT för API-begäran

Du kan skicka både synkrona och asynkrona anrop till Textanalys API.

#### <a name="synchronous"></a>[Synkront](#tab/synchronous)

### <a name="synchronous-requests"></a>Synkrona begäranden

Formatet för API-begäranden är detsamma för alla synkrona åtgärder. Dokument skickas i ett JSON-objekt som ostrukturerad råtext. XML stöds inte. JSON-schemat består av elementen som beskrivs nedan.

| Element | Giltiga värden | Obligatoriskt? | Användning |
|---------|--------------|-----------|-------|
|`id` |Datatypen är sträng, men i praktiken brukar dokument-ID:erna vara heltal. | Obligatorisk | Systemet använder de -ID:er som du anger för att strukturera utdata. Språkkoder, nyckelfraser och sentimentpoäng genereras för varje ID i begäran.|
|`text` | Ostrukturerad råtext, upp till 5 120 tecken. | Obligatorisk | För språkidentifiering kan text uttryckas på alla språk. För attitydanalys, extrahering av nyckelfraser och entitetsidentifiering måste texten finnas på ett [språk som stöds.](../language-support.md) |
|`language` | ISO [639-1-kod](https://en.wikipedia.org/wiki/List_of_ISO_639-1_codes) med två tecken för ett språk [som stöds](../language-support.md) | Det varierar | Krävs för attitydanalys, extrahering av nyckelfraser och entitetslänkning; valfritt för språkidentifiering. Det uppstår inget fel om du undantar det, men analysen har utelämnats utan det. Språkkoden ska motsvara den `text` du anger. |

Följande är ett exempel på en API-begäran för de synkrona Textanalys slutpunkterna. 

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

### <a name="asynchronous-requests-to-the-analyze-endpoint"></a>Asynkrona begäranden till `/analyze` slutpunkten

> [!NOTE]
> I den senaste förhandsversionen av Textanalys klientbiblioteket kan du anropa Asynkrona analysåtgärder med hjälp av ett klientobjekt. Du hittar exempel på GitHub:
* [C#](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/textanalytics/Azure.AI.TextAnalytics)
* [Python](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/textanalytics/azure-ai-textanalytics/)
* [Java](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/textanalytics/azure-ai-textanalytics)

Med `/analyze` slutpunkten kan du välja vilken av de funktioner Textanalys som du vill använda i ett enda API-anrop. Den här slutpunkten stöder för närvarande:

* Extrahering av nyckelfraser 
* Igenkänning av namngiven entitet (inklusive PII och PHI)
* Entity Linking

| Element | Giltiga värden | Obligatoriskt? | Användning |
|---------|--------------|-----------|-------|
|`displayName` | Sträng | Valfritt | Används som visningsnamn för jobbets unika identifierare.|
|`analysisInput` | Innehåller `documents` fältet nedan | Obligatorisk | Innehåller informationen för de dokument som du vill skicka. |
|`documents` | Innehåller `id` fälten `text` och nedan | Obligatorisk | Innehåller information för varje dokument som skickas och dokumentets råtext. |
|`id` | Sträng | Obligatorisk | De-ID:er som du anger används för att strukturera utdata. |
|`text` | Ostrukturerad råtext, upp till 125 000 tecken. | Obligatorisk | Måste vara på engelska, vilket är det enda språk som stöds för närvarande. |
|`tasks` | Innehåller följande Textanalys funktioner: `entityRecognitionTasks` `entityLinkingTasks` , `keyPhraseExtractionTasks` eller `entityRecognitionPiiTasks` . | Obligatorisk | En eller flera av Textanalys som du vill använda. Observera att `entityRecognitionPiiTasks` har en valfri parameter som kan anges till eller och för identifiering av valda `domain` `pii` `phi` `pii-categories` entitetstyper. Om `domain` parametern är ospecificerad får systemet som standard `pii` . |
|`parameters` | Innehåller fälten `model-version` `stringIndexType` och nedan | Obligatorisk | Det här fältet ingår i ovanstående funktionsaktiviteter som du väljer. De innehåller information om den modellversion som du vill använda och indextypen. |
|`model-version` | Sträng | Obligatorisk | Ange vilken version av modellen som anropas som du vill använda.  |
|`stringIndexType` | Sträng | Obligatorisk | Ange den textavkodare som matchar din programmeringsmiljö.  Typer som stöds `textElement_v8` är (standard), `unicodeCodePoint` , `utf16CodeUnit` . Mer information [finns i artikeln Om förskjutningar](../concepts/text-offsets.md#offsets-in-api-version-31-preview) av text.  |
|`domain` | Sträng | Valfritt | Gäller endast som en parameter för `entityRecognitionPiiTasks` uppgiften och kan anges till eller `pii` `phi` . Standardvärdet är `pii` om det inte anges.  |

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
        "entityLinkingTasks": [
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
                "model-version": "latest",
                "stringIndexType": "TextElements_v8",
                "domain": "phi",
                "pii-categories":"default"
            }
        }]
    }
}

```

### <a name="asynchronous-requests-to-the-health-endpoint"></a>Asynkrona begäranden till `/health` slutpunkten

Formatet för API-begäranden till Textanalys för hälso-värd-API är samma som för dess container. Dokument skickas i ett JSON-objekt som ostrukturerad råtext. XML stöds inte. JSON-schemat består av elementen som beskrivs nedan.  Fyll i och skicka formuläret [för Cognitive Services för att](https://aka.ms/csgate) begära åtkomst till Textanalys den offentliga hälsoförhandsvisningen. Du debiteras inte för Textanalys hälsoanvändning. 

| Element | Giltiga värden | Obligatoriskt? | Användning |
|---------|--------------|-----------|-------|
|`id` |Datatypen är sträng, men i praktiken brukar dokument-ID:erna vara heltal. | Obligatorisk | Systemet använder de-ID:er som du anger för att strukturera utdata. |
|`text` | Ostrukturerad råtext, upp till 5 120 tecken. | Obligatorisk | Observera att endast engelsk text stöds för närvarande. |
|`language` | ISO [639-1-kod](https://en.wikipedia.org/wiki/List_of_ISO_639-1_codes) med två tecken för ett språk [som stöds](../language-support.md) | Obligatorisk | Endast `en` stöds för närvarande. |

Följande är ett exempel på en API-begäran för Textanalys för hälsoslutpunkter. 

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
> Se artikeln [Data- och hastighetsbegränsningar](../concepts/data-limits.md) för information om priser och storleksgränser för att skicka data till Textanalys-API:et.


## <a name="set-up-a-request"></a>Konfigurera en begäran 

I Postman (eller något annat testverktyg för webb-API) lägger du till slutpunkten för den funktion som du vill använda. Använd tabellen nedan för att hitta rätt slutpunktsformat och ersätt `<your-text-analytics-resource>` med din resursslutpunkt. Exempel:

`https://my-resource.cognitiveservices.azure.com/text/analytics/v3.0/languages`

#### <a name="synchronous"></a>[Synkront](#tab/synchronous)

### <a name="endpoints-for-sending-synchronous-requests"></a>Slutpunkter för att skicka synkrona begäranden

| Funktion | Typ av begäran | Resursslutpunkter |
|--|--|--|
| Språkidentifiering | POST | `<your-text-analytics-resource>/text/analytics/v3.0/languages` |
| Attitydanalys | POST | `<your-text-analytics-resource>/text/analytics/v3.0/sentiment` |
| Yttrandeutvinning | POST | `<your-text-analytics-resource>/text/analytics/v3.0/sentiment?opinionMining=true` |
| Extrahering av nyckelfraser | POST | `<your-text-analytics-resource>/text/analytics/v3.0/keyPhrases` |
| Igenkänning av namngiven entitet – allmänt | POST | `<your-text-analytics-resource>/text/analytics/v3.0/entities/recognition/general` |
| Igenkänning av namngiven entitet – PII | POST | `<your-text-analytics-resource>/text/analytics/v3.0/entities/recognition/pii` |
| Igenkänning av namngiven entitet – PHI | POST |  `<your-text-analytics-resource>/text/analytics/v3.0/entities/recognition/pii?domain=phi` |

#### <a name="asynchronous"></a>[Asynkrona](#tab/asynchronous)

### <a name="endpoints-for-sending-asynchronous-requests-to-the-analyze-endpoint"></a>Slutpunkter för att skicka asynkrona begäranden till `/analyze` slutpunkten

| Funktion | Typ av begäran | Resursslutpunkter |
|--|--|--|
| Skicka analysjobb | POST | `https://<your-text-analytics-resource>/text/analytics/v3.1-preview.4/analyze` |
| Hämta analysstatus och resultat | GET | `https://<your-text-analytics-resource>/text/analytics/v3.1-preview.4/analyze/jobs/<Operation-Location>` |

### <a name="endpoints-for-sending-asynchronous-requests-to-the-health-endpoint"></a>Slutpunkter för att skicka asynkrona begäranden till `/health` slutpunkten

| Funktion | Typ av begäran | Resursslutpunkter |
|--|--|--|
| Skicka Textanalys för hälsojobb  | POST | `https://<your-text-analytics-resource>/text/analytics/v3.1-preview.4/entities/health/jobs` |
| Hämta jobbstatus och resultat | GET | `https://<your-text-analytics-resource>/text/analytics/v3.1-preview.4/entities/health/jobs/<Operation-Location>` |
| Avbryta jobb | DELETE | `https://<your-text-analytics-resource>/text/analytics/v3.1-preview.4/entities/health/jobs/<Operation-Location>` |

--- 

När du har din slutpunkt går du till Postman (eller något annat testverktyg för webb-API):

1. Välj typ av begäran för den funktion som du vill använda.
2. Klistra in slutpunkten för den åtgärd som du vill använda från tabellen ovan.
3. Ange de tre begärandehuvudena:

   + `Ocp-Apim-Subscription-Key`: din åtkomstnyckel som hämtas från Azure Portal
   + `Content-Type`: application/json
   + `Accept`: application/json

    Om du använder Postman bör din begäran se ut ungefär som på följande skärmbild, förutsatt att det finns en `/keyPhrases` slutpunkt.
    
    ![Skärmbild av begäran med slutpunkter och rubriker](../media/postman-request-keyphrase-1.png)
    
4. Välj **rådata** som format för **brödtexten**
    
    ![Skärmbild av begäran med brödtextinställningar](../media/postman-request-body-raw.png)

5. Klistra in vissa JSON-dokument i ett giltigt format. Använd exemplen i **avsnittet API-begärandeformat** ovan. Mer information och exempel finns i avsnitten nedan:

      + [Språkidentifiering](text-analytics-how-to-language-detection.md)
      + [Extrahering av nyckelfraser](text-analytics-how-to-keyword-extraction.md)
      + [Attitydanalys](text-analytics-how-to-sentiment-analysis.md)
      + [Entitetsigenkänning](text-analytics-how-to-entity-linking.md)

## <a name="send-the-request"></a>Skicka begäran

Skicka API-begäran. Om du gjorde anropet till en synkron slutpunkt visas svaret omedelbart, som ett enda JSON-dokument, med ett objekt för varje dokument-ID som anges i begäran.

Om du gjorde anropet till de asynkrona slutpunkterna eller slutpunkterna kontrollerar du `/analyze` att du har fått en `/health` 202-svarskod. du måste få svaret för att visa resultatet:

1. I API-svaret hittar du `Operation-Location` från -huvudet, som identifierar jobbet som du skickade till API:et. 
2. Skapa en GET-begäran för den slutpunkt som du använde. Se tabellen ovan [för slutpunktsformatet](#set-up-a-request) och läs API-referensdokumentationen . [](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1-preview-3/operations/AnalyzeStatus) Exempel:

    `https://my-resource.cognitiveservices.azure.com/text/analytics/v3.1-preview.4/analyze/jobs/<Operation-Location>`

3. Lägg till `Operation-Location` i begäran.

4. Svaret blir ett enda JSON-dokument med ett objekt för varje dokument-ID som anges i begäran.

Observera att för både asynkrona åtgärder och åtgärder är resultaten från GET-begäran i steg 2 ovan tillgängliga i 24 timmar från det att `/analyze` `/health` jobbet skapades.  Den här gången anges av värdet `expirationDateTime` i GET-svaret.  Efter den här tidsperioden rensas resultaten och är inte längre tillgängliga för hämtning.    

## <a name="example-api-responses"></a>Exempel på API-svar
 
# <a name="synchronous"></a>[Synkront](#tab/synchronous)

### <a name="example-responses-for-synchronous-operation"></a>Exempelsvar för synkron åtgärd

De synkrona slutpunktssvaren varierar beroende på vilken slutpunkt du använder. Se följande artiklar för exempelsvar.

+ [Språkidentifiering](text-analytics-how-to-language-detection.md#step-3-view-the-results)
+ [Extrahering av nyckelfraser](text-analytics-how-to-keyword-extraction.md#step-3-view-results)
+ [Attitydanalys](text-analytics-how-to-sentiment-analysis.md#view-the-results)
+ [Entitetsigenkänning](text-analytics-how-to-entity-linking.md#view-results)

# <a name="asynchronous"></a>[Asynkrona](#tab/asynchronous)

### <a name="example-responses-for-asynchronous-operations"></a>Exempelsvar för asynkrona åtgärder

Om det lyckas returnerar GET-begäran `/analyze` till slutpunkten ett objekt som innehåller de tilldelade uppgifterna. Till exempel `keyPhraseExtractionTasks`. Dessa uppgifter innehåller svarsobjektet från lämplig Textanalys funktion. Mer information finns i följande artiklar.

+ [Extrahering av nyckelfraser](text-analytics-how-to-keyword-extraction.md#step-3-view-results)
+ [Entitetsigenkänning](text-analytics-how-to-entity-linking.md#view-results)
+ [Textanalys för hälsa](text-analytics-for-health.md#hosted-asynchronous-web-api-response)

--- 

## <a name="see-also"></a>Se även

* [Översikt över Textanalys](../overview.md)
* [Vanliga frågor och svar (FAQ)](../text-analytics-resource-faq.md)</br>
* [Produktsida för textanalys](//go.microsoft.com/fwlink/?LinkID=759712)
* [Använda Textanalys klientbibliotek](../quickstarts/client-libraries-rest-api.md)
* [Nyheter](../whats-new.md)
