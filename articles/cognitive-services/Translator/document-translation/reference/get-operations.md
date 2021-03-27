---
title: Hämta åtgärder för dokument Översättning
titleSuffix: Azure Cognitive Services
description: Metoden GET Operations returnerar en lista med batch-begäranden som skickats och status för varje begäran.
services: cognitive-services
author: jann-skotdal
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 03/25/2021
ms.author: v-jansk
ms.openlocfilehash: c42f3081a831c267c7bc605267b99e2a916ea3d8
ms.sourcegitcommit: c94e282a08fcaa36c4e498771b6004f0bfe8fb70
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2021
ms.locfileid: "105613090"
---
# <a name="document-translation-get-operations"></a>Dokument översättning: Hämta åtgärder

Metoden GET Operations returnerar en lista med batch-begäranden som skickats och status för varje begäran. Den här listan innehåller bara batch-begäranden som skickats av användaren (baserat på prenumerationen). Statusen för varje begäran sorteras efter ID.

Om antalet förfrågningar överskrider vår växlings gräns används sid växling på Server sidan. Sid brytnings svar indikerar ett partiellt resultat och inkluderar en fortsättnings-token i svaret. Avsaknad av en fortsättnings-token innebär att inga ytterligare sidor är tillgängliga.

Du kan använda parametrarna $top och $skip Query för att ange ett antal resultat som ska returneras och en förskjutning för samlingen.

Servern följer de värden som anges av klienten. Klienterna måste dock vara för beredda för att hantera svar som innehåller en annan sid storlek eller som innehåller en tilläggs-token.

När både $top och $skip ingår bör servern först tillämpa $skip och sedan $top i samlingen. 

> [!NOTE]
> Om servern inte kan svara på $top och/eller $skip måste servern returnera ett fel till klienten som informerar om den i stället för att bara ignorera frågealternativen. Detta minskar risken för att klienten gör antaganden om de data som returneras.

## <a name="request-url"></a>Begärans-URL

Skicka en `GET` begäran till:
```HTTP
GET https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0-preview.1/batches
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
|$skip|Falskt|Hoppa över $skip poster i samlingen. När både $top och $skip anges tillämpas $skip först.|
|$top|Falskt|Ta $top posterna i samlingen. När både $top och $skip anges tillämpas $skip först.|

## <a name="request-headers"></a>Begärandehuvuden

Begärandehuvuden är:

|Sidhuvuden|Description|
|--- |--- |
|Ocp-Apim-Subscription-Key|Nödvändigt begär ande huvud|

## <a name="response-status-codes"></a>Svars status koder

Följande är de möjliga HTTP-statuskod som en begäran returnerar.

|Statuskod|Description|
|--- |--- |
|200|OK. Lyckad begäran och returnerar statusen för alla åtgärder. HeadersRetry – efter: integerETag: sträng|
|400|Felaktig begäran. Ogiltig begäran. Kontrol lera indataparametrarna.|
|401|Tillstånd. Kontrol lera dina autentiseringsuppgifter.|
|500|Internt Server fel.|
|Andra status koder|<ul><li>För många begär Anden</li><li>Servern är tillfälligt otillgänglig</li></ul>|

## <a name="get-operations-response"></a>Hämta åtgärds svar

### <a name="successful-get-operations-response"></a>Svar på Get-åtgärder slutfördes

Följande information returneras i ett lyckat svar.

|Namn|Typ|Description|
|--- |--- |--- |
|id|sträng|Åtgärdens ID.|
|createdDateTimeUtc|sträng|Åtgärden skapades datum och tid.|
|lastActionDateTimeUtc|sträng|Datum och tid då åtgärdens status har uppdaterats.|
|status|Sträng|Lista med möjliga status värden för jobb eller dokument: <ul><li>Avbrutna</li><li>Avbryter</li><li>Misslyckad</li><li>NotStarted</li><li>Körs</li><li>Lyckades</li><li>ValidationFailed</li></ul>|
|sammanfattning|StatusSummary[]|Sammanfattning som innehåller information som anges nedan.|
|Sammanfattning. totalt|heltal|Antal dokument totalt.|
|Sammanfattning. misslyckades|heltal|Antal dokument som misslyckades.|
|Sammanfattning. lyckades|heltal|Antal dokument som har översatts.|
|Sammanfattning. förlopp|heltal|Antal dokument som pågår.|
|Sammanfattning. notYetStarted|heltal|Antal dokument som ännu inte har startat bearbetningen.|
|Sammanfattning. avbruten|heltal|Antal avbrutna dokument.|
|Sammanfattning. totalCharacterCharged|heltal|Totalt antal tecken som debiteras.|

###<a name="error-response"></a>Fel svar

|Namn|Typ|Description|
|--- |--- |--- |
|kod|sträng|Uppräkningar som innehåller fel koder på hög nivå. Möjliga värden:<br/><ul><li>InternalServerError</li><li>InvalidArgument</li><li>InvalidRequest</li><li>RequestRateTooHigh</li><li>ResourceNotFound</li><li>ServiceUnavailable</li><li>Behörighet saknas</li></ul>|
|meddelande|sträng|Hämtar fel meddelande på hög nivå.|
|fokusera|sträng|Hämtar källan till felet. Det skulle till exempel vara "dokument" eller "dokument-ID" för ett ogiltigt dokument.|
|innerError|InnerErrorV2|Nytt inre fel format, som följer Cognitive Services API-riktlinjer. Den innehåller de obligatoriska egenskaperna ErrorCode, meddelande och valfria egenskaper mål, information (nyckel värde par), inre fel (detta kan kapslas).|
|innerError. Code|sträng|Hämtar kod fel sträng.|
|innerError. Message|sträng|Hämtar fel meddelande på hög nivå.|

## <a name="examples"></a>Exempel

### <a name="example-successful-response"></a>Exempel på lyckat svar

Följande är ett exempel på ett lyckat svar.

```JSON
{
  "value": [
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
  ]
}
```

### <a name="example-error-response"></a>Exempel fel svar

Följande är ett exempel på ett fel svar. Schemat för andra fel koder är detsamma.

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
