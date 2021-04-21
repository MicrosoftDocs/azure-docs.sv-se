---
title: Hämta översättningsstatus
titleSuffix: Azure Cognitive Services
description: Metoden för att hämta översättningsstatus returnerar status för en begäran om dokumentöversättning.
services: cognitive-services
author: jann-skotdal
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 03/25/2021
ms.author: v-jansk
ms.openlocfilehash: 8b129974396e420948737c9bdf47a5707decab6b
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2021
ms.locfileid: "107836276"
---
# <a name="get-translation-status"></a>Hämta översättningsstatus

Metoden Hämta översättningsstatus returnerar status för en begäran om dokumentöversättning. Statusen innehåller den övergripande begärandestatusen och statusen för dokument som översätts som en del av begäran.

## <a name="request-url"></a>Begärans-URL

Skicka en `GET` begäran till:
```HTTP
GET https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0-preview.1/batches/{id}
```

Lär dig hur du hittar [ditt anpassade domännamn](../get-started-with-document-translation.md#find-your-custom-domain-name).

> [!IMPORTANT]
>
> * **Alla API-begäranden till tjänsten För dokumentöversättning krävs en slutpunkt för en anpassad domän.**
> * Du kan inte använda slutpunkten som finns  på sidan Azure Portal nycklar och slutpunkter eller slutpunkten för global översättning– – för att göra `api.cognitive.microsofttranslator.com` HTTP-begäranden till dokumentöversättning.


## <a name="request-parameters"></a>Begärandeparametrar

Begärandeparametrar som skickas i frågesträngen är:

|Frågeparameter|Krävs|Beskrivning|
|--- |--- |--- |
|id|Sant|Åtgärds-ID:t.|

## <a name="request-headers"></a>Begärandehuvuden

Begärandehuvudena är:

|Sidhuvuden|Description|
|--- |--- |
|Ocp-Apim-Subscription-Key|Begärandehuvud som krävs|

## <a name="response-status-codes"></a>Statuskoder för svar

Följande är möjliga HTTP-statuskoder som en begäran returnerar.

|Statuskod|Description|
|--- |--- |
|200|OK. En lyckad begäran och returnerar status för batchöversättningsåtgärden. HeadersRetry-After: heltalETag: sträng|
|401|Obehörig. Kontrollera dina autentiseringsuppgifter.|
|404|Resursen hittades inte.|
|500|Internt serverfel.|
|Andra statuskoder|<ul><li>För många begäranden</li><li>Tillfälligt otillgänglig server</li></ul>|

## <a name="get-translation-status-response"></a>Svar på översättningsstatus

### <a name="successful-get-translation-status-response"></a>Svar om att hämta översättningsstatus har lyckats

Följande information returneras i ett lyckat svar.

|Namn|Typ|Description|
|--- |--- |--- |
|id|sträng|ÅTGÄRDENs ID.|
|createdDateTimeUtc|sträng|Åtgärden skapade datum/tid.|
|lastActionDateTimeUtc|sträng|Datum/tid då åtgärdens status har uppdaterats.|
|status|Sträng|Lista över möjliga statusar för jobb eller dokument: <ul><li>Avbrutna</li><li>Avbryta</li><li>Misslyckad</li><li>NotStarted</li><li>Körs</li><li>Lyckades</li><li>ValidationFailed</li></ul>|
|sammanfattning|StatusSummary|Sammanfattning som innehåller informationen som anges nedan.|
|summary.total|heltal|Totalt antal.|
|summary.failed|heltal|Antal misslyckades.|
|summary.success|heltal|Antal lyckade.|
|summary.inProgress|heltal|Antal pågående.|
|summary.notYetStarted|heltal|Antal som inte har startats än.|
|summary.cancelled|heltal|Antal avbokningar.|
|summary.totalCharacterCharged|heltal|Totalt antal tecken som debiteras av API:et.|

###<a name="error-response"></a>Felsvar

|Namn|Typ|Description|
|--- |--- |--- |
|kod|sträng|Uppräkningar som innehåller felkoder på hög nivå. Möjliga värden:<br/><ul><li>InternalServerError</li><li>InvalidArgument</li><li>InvalidRequest</li><li>RequestRateTooHigh</li><li>ResourceNotFound</li><li>ServiceUnavailable</li><li>Behörighet saknas</li></ul>|
|meddelande|sträng|Hämtar felmeddelande på hög nivå.|
|Mål|sträng|Hämtar källan till felet. Det skulle till exempel vara "dokument" eller "dokument-ID" för ett ogiltigt dokument.|
|innerError|InnerErrorV2|Nytt format för inre fel, som överensstämmer Cognitive Services API-riktlinjer. Den innehåller de obligatoriska egenskaperna ErrorCode, message och optional properties target, details(key value pair), inner error (kan kapslas).|
|innerError.code|sträng|Hämtar kodfelsträngen.|
|innerError.message|sträng|Hämtar felmeddelande på hög nivå.|

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

### <a name="example-error-response"></a>Exempel på felsvar

Följande JSON-objekt är ett exempel på ett felsvar. Schemat för andra felkoder är detsamma.

Statuskod: 401

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

Följ vår snabbstart om du vill veta mer om hur du använder dokumentöversättning och klientbiblioteket.

> [!div class="nextstepaction"]
> [Kom igång med dokumentöversättning](../get-started-with-document-translation.md)
