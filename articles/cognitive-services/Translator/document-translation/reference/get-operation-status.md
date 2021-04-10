---
title: Åtgärds status för hämtning av dokument Översättning
titleSuffix: Azure Cognitive Services
description: Metoden Hämta åtgärds status returnerar status för en begäran om dokument översättning.
services: cognitive-services
author: jann-skotdal
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 03/25/2021
ms.author: v-jansk
ms.openlocfilehash: e8aa9b93dc089cc05dd5157a7ac84cceb5f6fcd5
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "105613087"
---
# <a name="document-translation-get-operation-status"></a>Dokument översättning: Hämta åtgärds status

Status metoden Hämta åtgärds dokument returnerar status för en begäran om dokument översättning. Statusen omfattar den övergripande status för begäran och status för dokument som översätts som en del av denna begäran.

## <a name="request-url"></a>Begärans-URL

Skicka en `GET` begäran till:
```HTTP
GET https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0-preview.1/batches/{id}
```

Lär dig hur du hittar ditt [anpassade domän namn](../get-started-with-document-translation.md#find-your-custom-domain-name).

> [!IMPORTANT]
>
> * **Alla API-förfrågningar till dokument översättnings tjänsten kräver en anpassad domän slut punkt**.
> * Du kan inte använda slut punkten som finns på Azure Portal resurs _nycklar och slut punkts_ sida eller den globala Translator-slutpunkten – `api.cognitive.microsofttranslator.com` för att göra HTTP-förfrågningar till dokument översättning.


## <a name="request-parameters"></a>Parametrar för begäran

Parametrarna för begäran som skickades till frågesträngen är:

|Frågeparameter|Krävs|Beskrivning|
|--- |--- |--- |
|id|Sant|Åtgärds-ID.|

## <a name="request-headers"></a>Begärandehuvuden

Begärandehuvuden är:

|Sidhuvuden|Beskrivning|
|--- |--- |
|Ocp-Apim-Subscription-Key|Nödvändigt begär ande huvud|

## <a name="response-status-codes"></a>Svars status koder

Följande är de möjliga HTTP-statuskod som en begäran returnerar.

|Statuskod|Beskrivning|
|--- |--- |
|200|OK. Lyckad begäran och returnerar status för batch-förflyttningsverktyget. HeadersRetry – efter: integerETag: sträng|
|401|Tillstånd. Kontrol lera dina autentiseringsuppgifter.|
|404|Det gick inte att hitta resursen.|
|500|Internt Server fel.|
|Andra status koder|<ul><li>För många begär Anden</li><li>Servern är tillfälligt otillgänglig</li></ul>|

## <a name="get-operation-status-response"></a>Hämta åtgärds status svar

### <a name="successful-get-operation-status-response"></a>Svar på åtgärds status för hämtning slutfördes

Följande information returneras i ett lyckat svar.

|Namn|Typ|Beskrivning|
|--- |--- |--- |
|id|sträng|Åtgärdens ID.|
|createdDateTimeUtc|sträng|Åtgärden skapades datum och tid.|
|lastActionDateTimeUtc|sträng|Datum och tid då åtgärdens status har uppdaterats.|
|status|Sträng|Lista med möjliga status värden för jobb eller dokument: <ul><li>Avbrutna</li><li>Avbryter</li><li>Misslyckad</li><li>NotStarted</li><li>Körs</li><li>Lyckades</li><li>ValidationFailed</li></ul>|
|sammanfattning|StatusSummary|Sammanfattning som innehåller information som anges nedan.|
|Sammanfattning. totalt|heltal|Totalt antal.|
|Sammanfattning. misslyckades|heltal|Antal misslyckade.|
|Sammanfattning. lyckades|heltal|Antal lyckade.|
|Sammanfattning. förlopp|heltal|Antal pågående.|
|Sammanfattning. notYetStarted|heltal|Antal inte startat än.|
|Sammanfattning. avbruten|heltal|Antal avbrutna.|
|Sammanfattning. totalCharacterCharged|heltal|Totalt antal tecken som debiteras av API: et.|

###<a name="error-response"></a>Fel svar

|Namn|Typ|Beskrivning|
|--- |--- |--- |
|kod|sträng|Uppräkningar som innehåller fel koder på hög nivå. Möjliga värden:<br/><ul><li>InternalServerError</li><li>InvalidArgument</li><li>InvalidRequest</li><li>RequestRateTooHigh</li><li>ResourceNotFound</li><li>ServiceUnavailable</li><li>Behörighet saknas</li></ul>|
|meddelande|sträng|Hämtar fel meddelande på hög nivå.|
|fokusera|sträng|Hämtar källan till felet. Det skulle till exempel vara "dokument" eller "dokument-ID" för ett ogiltigt dokument.|
|innerError|InnerErrorV2|Nytt inre fel format, som följer Cognitive Services API-riktlinjer. Det innehåller obligatoriska egenskaper för fel, meddelande och valfria egenskaper, information (nyckel värde par), inre fel (kan kapslas).|
|innerError. Code|sträng|Hämtar kod fel sträng.|
|innerError. Message|sträng|Hämtar fel meddelande på hög nivå.|

## <a name="examples"></a>Exempel

### <a name="example-successful-response"></a>Exempel på lyckat svar

Följande JSON-objekt är ett exempel på ett lyckat svar.

```JSON
{
  "id": "727bf148-f327-47a0-9481-abae6362f11e",
  "createdDateTimeUtc": "2020-03-26T00:00:00Z",
  "lastActionDateTimeUtc": "2020-03-26T01:00:00Z",
  "status": "Succeeded",
  "summary": {
    "total": 10,
    "failed": 1,
    "success": 9,
    "inProgress": 0,
    "notYetStarted": 0,
    "cancelled": 0,
    "totalCharacterCharged": 0
  }
}
```

### <a name="example-error-response"></a>Exempel fel svar

Följande JSON-objekt är ett exempel på ett fel svar. Schemat för andra fel koder är detsamma.

Status kod: 401

```JSON
{
  "error": {
    "code": "Unauthorized",
    "message": "User is not authorized",
    "target": "Document",
    "innerError": {
      "code": "Unauthorized",
      "message": "Operation is not authorized"
    }
  }
}
```

## <a name="next-steps"></a>Nästa steg

Följ vår snabb start för att lära dig mer om hur du använder dokument översättning och klient biblioteket.

> [!div class="nextstepaction"]
> [Kom igång med dokument Översättning](../get-started-with-document-translation.md)
