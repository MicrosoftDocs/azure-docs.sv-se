---
title: Använd entitets igenkänning med API för textanalys
titleSuffix: Azure Cognitive Services
description: Lär dig hur du identifierar och disambiguate identiteten för en entitet som finns i text med Textanalys REST API.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: article
ms.date: 11/19/2020
ms.author: aahi
ms.openlocfilehash: 5b064365a6f0bd8a544f57d67cd6e4beb98bb404
ms.sourcegitcommit: 2ba6303e1ac24287762caea9cd1603848331dd7a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/15/2020
ms.locfileid: "97505247"
---
# <a name="how-to-use-named-entity-recognition-in-text-analytics"></a>Använda namngiven enhets igenkänning i Textanalys

Med API för textanalys kan du ta ostrukturerad text och returnera en lista med disambiguated-entiteter, med länkar till mer information på webben. API: et stöder både NER (Named Entity Recognition) för flera enhets kategorier och entitet länkning.

## <a name="entity-linking"></a>Entity Linking

Länkning av entitet är möjligheten att identifiera och disambiguate identiteten för en entitet som finns i text (till exempel att avgöra om en förekomst av ordet "mars" avser planet eller den romerska god). Den här processen kräver att det finns en kunskaps bas på ett lämpligt språk för att länka identifierade entiteter i text. Enhets länkning använder [Wikipedia](https://www.wikipedia.org/) som den här kunskaps basen.

## <a name="named-entity-recognition-ner"></a>Igenkänning av namngivna enheter (NER)

Med namngiven enhets igenkänning (NER) kan du identifiera olika entiteter i text och kategorisera dem i fördefinierade klasser eller typer, till exempel: person, plats, händelse, produkt och organisation.  

## <a name="personally-identifiable-information-pii"></a>Personligt identifierbar information (PII)

Funktionen PII är en del av NER och kan identifiera och redigera känsliga entiteter i text som är associerade med en enskild person, till exempel: telefonnummer, e-postadress, e-postadress, Passport-nummer.

## <a name="named-entity-recognition-features-and-versions"></a>Funktioner och versioner för namngiven enhets igenkänning

[!INCLUDE [v3 region availability](../includes/v3-region-availability.md)]

| Funktion                                                         | NER v 3.0 | NER v 3.1 – för hands version. 3 |
|-----------------------------------------------------------------|--------|----------|
| Metoder för enkel-och batch-begäranden                          | X      | X        |
| Utökad enhets igenkänning över flera kategorier           | X      | X        |
| Separata slut punkter för att skicka enhets länknings-och NER-begäranden. | X      | X        |
| Redovisning av personliga `PII` enheter () och hälso tillstånd ( `PHI` )        |        | X        |
| Bortredigering av `PII`        |        | X        |

Mer information finns i [språk stöd](../language-support.md) .

Med namnet entitets igenkänning v3 får du utökad identifiering över flera typer. För närvarande kan NER v 3.0 identifiera entiteter i [kategorin Allmänt entiteter](../named-entity-types.md).

Namngiven entitets igenkänning v 3.1 – för hands version. 3 innehåller identifierings funktionerna i v 3.0 och: 
* Möjlighet att identifiera personlig information ( `PII` ) med hjälp av `v3.1-preview.3/entities/recognition/pii` slut punkten. 
* En valfri `domain=phi` parameter för att identifiera konfidentiell hälso information ( `PHI` ).
* [Asynkron åtgärd](text-analytics-how-to-call-api.md) med `/analyze` slut punkten.

Mer information finns i artikeln [entitet Categories](../named-entity-types.md) och avsnittet om [begär slut punkter](#request-endpoints) nedan. 

## <a name="sending-a-rest-api-request"></a>Skicka en REST API-begäran

### <a name="preparation"></a>Förberedelse

Du måste ha JSON-dokument i det här formatet: ID, text, språk.

Varje dokument måste vara under 5 120 tecken och du kan ha upp till 1 000 objekt (ID) per samling. Samlingen skickas i begäranstexten.

### <a name="structure-the-request"></a>Strukturera begäran

Skicka en POST-begäran. Du kan [använda Postman](text-analytics-how-to-call-api.md) eller **API test-konsolen** i följande länkar för att snabbt strukturera och skicka en. 

> [!NOTE]
> Du kan hitta din nyckel och slut punkt för din Textanalys-resurs på Azure-portalen. De kommer att finnas på resursens **snabb start** sida under **resurs hantering**. 


### <a name="request-endpoints"></a>Begär slut punkter

#### <a name="version-31-preview3"></a>[Version 3,1 – för hands version. 3](#tab/version-3-preview)

Igenkänning av namngivna enheter `v3.1-preview.3` använder separata slut punkter för ner, personligt identifierbarhet och begär Anden om att länka entiteter. Använd ett URL-format nedan baserat på din begäran.

**Länkning av entitet**
* `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.1-preview.3/entities/linking`

[Namngiven enhets igenkännings version 3,1 – för hands versions referens för `Linking`](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1-Preview-3/operations/EntitiesLinking)

**Igenkänning av namngiven enhet**
* Allmänna entiteter – `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.1-preview.3/entities/recognition/general`

[Namngiven enhets igenkännings version 3,1 – för hands versions referens för `General`](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1-Preview-3/operations/EntitiesRecognitionGeneral)

**Personligt identifierbar information (PII)**
* Personlig ( `PII` ) information – `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.1-preview.3/entities/recognition/pii`

Du kan också använda den valfria `domain=phi` parametern för att identifiera hälso `PHI` information () i text. 

`https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.1-preview.3/entities/recognition/pii?domain=phi`

Från `v3.1-preview.3` och med, innehåller JSON-svaret en `redactedText` egenskap som innehåller den ändrade indatamängden där de IDENTIFIERAde PII-enheterna ersätts av ett `*` för varje tecken i entiteterna.

[Namngiven enhets igenkännings version 3,1 – för hands versions referens för `PII`](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1-Preview-3/operations/EntitiesRecognitionPii)

#### <a name="version-30"></a>[Version 3,0](#tab/version-3)

Med namnet entitets igenkänning v3 används separata slut punkter för NER och begär Anden om att länka entiteter. Använd ett URL-format nedan baserat på din begäran:

**Länkning av entitet**
* `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0/entities/linking`

[Version 3,0-referens för namngiven enhets igenkänning `Linking`](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0/operations/EntitiesRecognitionGeneral)

**Igenkänning av namngiven enhet**
* `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0/entities/recognition/general`

[Version 3,0-referens för namngiven enhets igenkänning `General`](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0/operations/EntitiesRecognitionGeneral)

---

Ange ett rubrik för begäran som ska innehålla din API för textanalys-nyckel. I begär ande texten anger du de JSON-dokument som du har för berett.

### <a name="example-ner-request"></a>Exempel på NER-begäran 

Följande JSON är ett exempel på innehåll som du kan skicka till API: et. Formatet för begäran är detsamma för båda versionerna av API: et.

```json
{
  "documents": [
    {
        "id": "1",
        "language": "en",
        "text": "Our tour guide took us up the Space Needle during our trip to Seattle last week."
    }
  ]
}

```

## <a name="post-the-request"></a>Publicera begäran

Analysen utförs när begäran har tagits emot. I avsnittet [data begränsningar](../overview.md#data-limits) i översikt finns information om storlek och antal begär Anden som du kan skicka per minut och sekund.

API för textanalys är tillstånds lös. Inga data lagras i ditt konto och resultaten returneras omedelbart i svaret.

## <a name="view-results"></a>Visa resultat

Alla POST-förfrågningar returnerar ett JSON-formaterat svar med ID och identifierade egenskaper för entiteten.

Utdata returneras direkt. Du kan strömma resultaten till ett program som stöder JSON eller spara utdata till en fil på den lokala datorn och sedan importera den till ett program som gör så att du kan sortera, söka och hantera data. På grund av stöd för flerspråkig och emoji kan svaret innehålla text förskjutningar. Mer information finns i [så här bearbetar du text förskjutningar](../concepts/text-offsets.md).

### <a name="example-responses"></a>Exempel svar

Version 3 tillhandahåller separata slut punkter för allmän NER, personligt identifierbarhet och enhets länkning. Svaren för båda åtgärderna nedan. 

#### <a name="version-31-preview"></a>[Version 3,1 – för hands version](#tab/version-3-preview)

Exempel på ett PII-svar:
```json
{
  "documents": [
    {
    "redactedText": "You can even pre-order from their online menu at *************************, call ************ or send email to ***************************!",
    "id": "0",
    "entities": [
        {
        "text": "www.contososteakhouse.com",
        "category": "URL",
        "offset": 49,
        "length": 25,
        "confidenceScore": 0.8
        }, 
        {
        "text": "312-555-0176",
        "category": "Phone Number",
        "offset": 81,
        "length": 12,
        "confidenceScore": 0.8
        }, 
        {
        "text": "order@contososteakhouse.com",
        "category": "Email",
        "offset": 111,
        "length": 27,
        "confidenceScore": 0.8
        }
      ],
    "warnings": []
    }
  ],
  "errors": [],
  "modelVersion": "2020-07-01"
}
```

Exempel på en entitet som länkar svar:

```json
{
  "documents": [
    {
      "id": "1",
      "entities": [
        {
          "bingId": "f8dd5b08-206d-2554-6e4a-893f51f4de7e", 
          "name": "Space Needle",
          "matches": [
            {
              "text": "Space Needle",
              "offset": 30,
              "length": 12,
              "confidenceScore": 0.4
            }
          ],
          "language": "en",
          "id": "Space Needle",
          "url": "https://en.wikipedia.org/wiki/Space_Needle",
          "dataSource": "Wikipedia"
        },
        {
          "bingId": "5fbba6b8-85e1-4d41-9444-d9055436e473",
          "name": "Seattle",
          "matches": [
            {
              "text": "Seattle",
              "offset": 62,
              "length": 7,
              "confidenceScore": 0.25
            }
          ],
          "language": "en",
          "id": "Seattle",
          "url": "https://en.wikipedia.org/wiki/Seattle",
          "dataSource": "Wikipedia"
        }
      ],
      "warnings": []
    }
  ],
  "errors": [],
  "modelVersion": "2020-02-01"
}
```


#### <a name="version-30"></a>[Version 3,0](#tab/version-3)

Exempel på ett allmänt NER-svar:
```json
{
  "documents": [
    {
      "id": "1",
      "entities": [
        {
          "text": "tour guide",
          "category": "PersonType",
          "offset": 4,
          "length": 10,
          "confidenceScore": 0.45
        },
        {
          "text": "Space Needle",
          "category": "Location",
          "offset": 30,
          "length": 12,
          "confidenceScore": 0.38
        },
        {
          "text": "trip",
          "category": "Event",
          "offset": 54,
          "length": 4,
          "confidenceScore": 0.78
        },
        {
          "text": "Seattle",
          "category": "Location",
          "subcategory": "GPE",
          "offset": 62,
          "length": 7,
          "confidenceScore": 0.78
        },
        {
          "text": "last week",
          "category": "DateTime",
          "subcategory": "DateRange",
          "offset": 70,
          "length": 9,
          "confidenceScore": 0.8
        }
      ],
      "warnings": []
    }
  ],
  "errors": [],
  "modelVersion": "2020-04-01"
}
```

---


## <a name="summary"></a>Sammanfattning

I den här artikeln har du lärt dig begrepp och arbets flöde för entitets länkning med Textanalys i Cognitive Services. Sammanfattningsvis:

* JSON-dokument i begär ande texten innehåller ID, text och språkkod.
* POST-begäranden skickas till en eller flera slut punkter, med hjälp av en anpassad [åtkomst nyckel och en slut punkt](../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource) som är giltig för din prenumeration.
* Svars utdata, som består av länkade entiteter (inklusive förtroende poäng, förskjutningar och webb Länkar för varje dokument-ID) kan användas i alla program

## <a name="next-steps"></a>Nästa steg

* [Översikt över Textanalys](../overview.md)
* [Använda klient biblioteket för Textanalys](../quickstarts/client-libraries-rest-api.md)
* [Nyheter](../whats-new.md)
