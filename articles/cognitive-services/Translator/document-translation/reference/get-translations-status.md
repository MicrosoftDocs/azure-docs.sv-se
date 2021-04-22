---
title: Hämta översättningsstatus
titleSuffix: Azure Cognitive Services
description: Statusmetoden hämta översättningar returnerar en lista över batchbegäranden som skickats och status för varje begäran.
services: cognitive-services
author: jann-skotdal
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 04/21/2021
ms.author: v-jansk
ms.openlocfilehash: c3301283f0a7334a7c207ff7c80b4f71a13de465
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2021
ms.locfileid: "107864837"
---
# <a name="get-translations-status"></a>Hämta översättningsstatus

Statusmetoden Hämta översättningar returnerar en lista över batchbegäranden som skickats och status för varje begäran. Den här listan innehåller endast batchbegäranden som skickats av användaren (baserat på prenumerationen). Statusen för varje begäran sorteras efter ID.

Om antalet begäranden överskrider vår växlingsgräns används sidinsökning på serversidan. Sidnumrerade svar indikerar ett partiellt resultat och inkluderar en fortsättningstoken i svaret. Frånvaron av en fortsättningstoken innebär att inga ytterligare sidor är tillgängliga.

$top och $skip-frågeparametrar kan användas för att ange ett antal resultat som ska returneras och en offset för samlingen.

Servern respekterar de värden som anges av klienten. Klienter måste dock vara förberedda på att hantera svar som innehåller en annan sidstorlek eller som innehåller en fortsättningstoken.

När både $top och $skip ingår bör servern först tillämpa $skip och sedan $top på samlingen. 

> [!NOTE]
> Om servern inte kan respektera $top och/eller $skip måste servern returnera ett fel till klienten som informerar om det i stället för att bara ignorera frågealternativen. Detta minskar risken för att klienten gör antaganden om de data som returneras.

## <a name="request-url"></a>Begärans-URL

Skicka en `GET` begäran till:
```HTTP
GET https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0-preview.1/batches
```

Lär dig hur du hittar [ditt anpassade domännamn](../get-started-with-document-translation.md#find-your-custom-domain-name).

> [!IMPORTANT]
>
> * **Alla API-begäranden till tjänsten för dokumentöversättning kräver en anpassad domänslutpunkt.**
> * Du kan inte använda slutpunkten som finns  på din Azure Portal resursnycklar och slutpunktssida eller den globala översättningsslutpunkten– – för `api.cognitive.microsofttranslator.com` att göra HTTP-begäranden till dokumentöversättning.

## <a name="request-parameters"></a>Begärandeparametrar

Begärandeparametrar som skickas i frågesträngen är:

|Frågeparameter|Krävs|Beskrivning|
|--- |--- |--- |
|$skip|Falskt|Hoppa $skip poster i samlingen. När både $top och $skip anges $skip du först.|
|$top|Falskt|Ta $top posterna i samlingen. När både $top och $skip anges $skip du först.|

## <a name="request-headers"></a>Begärandehuvuden

Begärandehuvudena är:

|Sidhuvuden|Description|
|--- |--- |
|Ocp-Apim-Subscription-Key|Begärandehuvud som krävs|

## <a name="response-status-codes"></a>Statuskoder för svar

Följande är möjliga HTTP-statuskoder som en begäran returnerar.

|Statuskod|Description|
|--- |--- |
|200|OK. En lyckad begäran och returnerar status för alla åtgärder. HeadersRetry-After: heltalETag: sträng|
|400|Felaktig begäran. Ogiltig begäran. Kontrollera indataparametrar.|
|401|Obehörig. Kontrollera dina autentiseringsuppgifter.|
|500|Internt serverfel.|
|Andra statuskoder|<ul><li>För många begäranden</li><li>Tillfälligt otillgänglig server</li></ul>|

## <a name="get-translations-status-response"></a>Hämta svar på översättningsstatus

### <a name="successful-get-translations-status-response"></a>Svar om lyckad hämta översättningsstatus

Följande information returneras i ett lyckat svar.

|Namn|Typ|Description|
|--- |--- |--- |
|id|sträng|ÅTGÄRDENs ID.|
|createdDateTimeUtc|sträng|Åtgärden skapade datum/tid.|
|lastActionDateTimeUtc|sträng|Datum/tid då åtgärdens status har uppdaterats.|
|status|Sträng|Lista över möjliga statusar för jobb eller dokument: <ul><li>Avbrutna</li><li>Avbryta</li><li>Misslyckad</li><li>NotStarted</li><li>Körs</li><li>Lyckades</li><li>ValidationFailed</li></ul>|
|sammanfattning|StatusSummary[]|Sammanfattning som innehåller informationen som anges nedan.|
|summary.total|heltal|Antal dokument totalt.|
|summary.failed|heltal|Antalet dokument misslyckades.|
|summary.success|heltal|Antal dokument som har översatts.|
|summary.inProgress|heltal|Antal dokument som pågår.|
|summary.notYetStarted|heltal|Antal dokument som ännu inte startat bearbetningen.|
|summary.cancelled|heltal|Antal dokument som avbrutits.|
|summary.totalCharacterCharged|heltal|Totalt antal tecken som debiteras.|

### <a name="error-response"></a>Felsvar

|Namn|Typ|Description|
|--- |--- |--- |
|kod|sträng|Uppräkningar som innehåller felkoder på hög nivå. Möjliga värden:<br/><ul><li>InternalServerError</li><li>InvalidArgument</li><li>InvalidRequest</li><li>RequestRateTooHigh</li><li>ResourceNotFound</li><li>ServiceUnavailable</li><li>Behörighet saknas</li></ul>|
|meddelande|sträng|Hämtar felmeddelande på hög nivå.|
|Mål|sträng|Hämtar källan till felet. Det kan till exempel vara "dokument" eller "dokument-ID" om ett ogiltigt dokument används.|
|innerError|InnerErrorV2|Nytt format för inre fel som överensstämmer Cognitive Services API-riktlinjer. Den innehåller de obligatoriska egenskaperna ErrorCode, message och optional properties target, details(key value pair), inner error (detta kan kapslas).|
|innerError.code|sträng|Hämtar kodfelsträngen.|
|innerError.message|sträng|Hämtar felmeddelande på hög nivå.|

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

### <a name="example-error-response"></a>Exempel på felsvar

Följande är ett exempel på ett felsvar. Schemat för andra felkoder är detsamma.

Statuskod: 500

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

Följ vår snabbstart om du vill veta mer om hur du använder dokumentöversättning och klientbiblioteket.

> [!div class="nextstepaction"]
> [Kom igång med dokumentöversättning](../get-started-with-document-translation.md)
