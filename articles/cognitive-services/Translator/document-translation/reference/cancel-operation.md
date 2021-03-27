---
title: Åtgärds metod för att avbryta dokument Översättning
titleSuffix: Azure Cognitive Services
description: Metoden Cancel Operations avbryter en pågående bearbetning eller en köad åtgärd.
services: cognitive-services
author: jann-skotdal
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 03/25/2021
ms.author: v-jansk
ms.openlocfilehash: 39730f118dd93a972f238f85ef890f4dc54ca91a
ms.sourcegitcommit: c94e282a08fcaa36c4e498771b6004f0bfe8fb70
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2021
ms.locfileid: "105613165"
---
# <a name="document-translation-cancel-operations"></a>Dokument översättning: Avbryt åtgärder

Avbryt en pågående bearbetning eller en köad åtgärd. En åtgärd avbryts inte om den redan har slutförts eller misslyckats eller avbrutits. En felaktig begäran kommer att returneras. Alla dokument som har slutfört översättningen avbryts inte och kommer att debiteras. Alla väntande dokument avbryts om det är möjligt.

## <a name="request-url"></a>Begärans-URL

Skicka en `DELETE` begäran till:
```DELETE HTTP
https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0-preview.1/batches/{id}
```

Lär dig hur du hittar ditt [anpassade domän namn](../get-started-with-document-translation.md#find-your-custom-domain-name).

> [!IMPORTANT]
>
> * **Alla API-förfrågningar till dokument översättnings tjänsten kräver en anpassad domän slut punkt**.
> * Du kan inte använda slut punkten som finns på Azure Portal resurs _nycklar och slut punkts_ sida eller den globala Translator-slutpunkten – `api.cognitive.microsofttranslator.com` för att göra HTTP-förfrågningar till dokument översättning.

## <a name="request-parameters"></a>Parametrar för begäran

Parametrarna för begäran som skickades till frågesträngen är:

|Frågeparameter|Krävs|Beskrivning|
|-----|-----|-----|
|id|Sant|Åtgärds-ID.|

## <a name="request-headers"></a>Begärandehuvuden

Begärandehuvuden är:

|Sidhuvuden|Description|
|-----|-----|
|Ocp-Apim-Subscription-Key|Nödvändigt begär ande huvud|

## <a name="response-status-codes"></a>Svars status koder

Följande är de möjliga HTTP-statuskod som en begäran returnerar.

| Statuskod| Description|
|-----|-----|
|200|OK. Cancel-begäran har skickats|
|401|Tillstånd. Kontrol lera dina autentiseringsuppgifter.|
|404|Hittades inte. Det gick inte att hitta resursen. 
|500|Internt Server fel.
|Andra status koder|<ul><li>För många begär Anden</li><li>Servern är tillfälligt otillgänglig</li></ul>|

## <a name="cancel-operations-response"></a>Avbryt åtgärds svar

### <a name="successful-response"></a>Lyckat svar

Följande information returneras i ett lyckat svar.

|Namn|Typ|Description|
|--- |--- |--- |
|id|sträng|Åtgärdens ID.|
|createdDateTimeUtc|sträng|Åtgärden skapades datum och tid.|
|lastActionDateTimeUtc|sträng|Datum och tid då åtgärdens status har uppdaterats.|
|status|Sträng|Lista med möjliga status värden för jobb eller dokument: <ul><li>Avbrutna</li><li>Avbryter</li><li>Misslyckad</li><li>NotStarted</li><li>Körs</li><li>Lyckades</li><li>ValidationFailed</li></ul>|
|sammanfattning|StatusSummary|Sammanfattning som innehåller information som anges nedan.|
|Sammanfattning. totalt|heltal|Antal dokument totalt.|
|Sammanfattning. misslyckades|heltal|Antal dokument som misslyckades.|
|Sammanfattning. lyckades|heltal|Antal dokument som har översatts.|
|Sammanfattning. förlopp|heltal|Antal dokument som pågår.|
|Sammanfattning. notYetStarted|heltal|Antal dokument som ännu inte har startat bearbetningen.|
|Sammanfattning. avbruten|heltal|Antal avbrutna.|
|Sammanfattning. totalCharacterCharged|heltal|Totalt antal tecken som debiteras av API: et.|

### <a name="error-response"></a>Fel svar

|Namn|Typ|Description|
|--- |--- |--- |
|kod|sträng|Uppräkningar som innehåller fel koder på hög nivå. Möjliga värden:<br/><ul><li>InternalServerError</li><li>InvalidArgument</li><li>InvalidRequest</li><li>RequestRateTooHigh</li><li>ResourceNotFound</li><li>ServiceUnavailable</li><li>Behörighet saknas</li></ul>|
|meddelande|sträng|Hämtar fel meddelande på hög nivå.|
|fokusera|sträng|Hämtar källan till felet. Det skulle till exempel vara "dokument" eller "dokument-ID" för ett ogiltigt dokument.|
|innerError|InnerErrorV2|Nytt inre fel format, som följer Cognitive Services API-riktlinjer. Det innehåller obligatoriska egenskaper för fel, meddelande och valfria egenskaper, information (nyckel värde par), inre fel (kan kapslas).|
|innerError. Code|sträng|Hämtar kod fel sträng.|
|innersta. Felkoden. Message|sträng|Hämtar fel meddelande på hög nivå.|

## <a name="examples"></a>Exempel

### <a name="example-successful-response"></a>Exempel på lyckat svar

Följande JSON-objekt är ett exempel på ett lyckat svar.

Status kod: 200

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

Status kod: 500

```JSON
{
  "error": {
    "code": "InternalServerError",
    "message": "Internal Server Error",
    "target": "Operation",
    "innerError": {
      "code": "InternalServerError",
      "message": "Unexpected internal server error has occurred"
    }
  }
}
```

## <a name="next-steps"></a>Nästa steg

Följ vår snabb start för att lära dig mer om hur du använder dokument översättning och klient biblioteket.

> [!div class="nextstepaction"]
> [Kom igång med dokument Översättning](../get-started-with-document-translation.md)
