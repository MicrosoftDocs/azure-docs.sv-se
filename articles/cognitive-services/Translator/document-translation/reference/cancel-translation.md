---
title: Avbryt översättningsmetoden
titleSuffix: Azure Cognitive Services
description: Översättningsmetoden Avbryt avbryter en pågående bearbetning eller köåtgärd.
services: cognitive-services
author: jann-skotdal
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 04/21/2021
ms.author: v-jansk
ms.openlocfilehash: e3b7da30f54b9d9468b46a2cd0972a3397e5cdce
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2021
ms.locfileid: "107865115"
---
# <a name="cancel-translation"></a>Avbryta översättning

Avbryt en pågående bearbetning eller köåtgärd. En åtgärd avbryts inte om den redan har slutförts, misslyckats eller avbrutits. En felaktig begäran returneras. Alla dokument som har slutfört översättningen avbryts inte och debiteras. Alla väntande dokument avbryts om möjligt.

## <a name="request-url"></a>Begärans-URL

Skicka en `DELETE` begäran till:

```DELETE HTTP
https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0-preview.1/batches/{id}
```

Lär dig hur du hittar [ditt anpassade domännamn](../get-started-with-document-translation.md#find-your-custom-domain-name).

> [!IMPORTANT]
>
> * **Alla API-begäranden till tjänsten För dokumentöversättning krävs en slutpunkt för en anpassad domän.**
> * Du kan inte använda slutpunkten som finns  på sidan Azure Portal nycklar och slutpunkter eller slutpunkten för global översättning– – för att göra `api.cognitive.microsofttranslator.com` HTTP-begäranden till dokumentöversättning.

## <a name="request-parameters"></a>Begärandeparametrar

Begärandeparametrar som skickas i frågesträngen är:

|Frågeparameter|Krävs|Beskrivning|
|-----|-----|-----|
|id|Sant|Åtgärds-ID:t.|

## <a name="request-headers"></a>Begärandehuvuden

Begärandehuvudena är:

|Sidhuvuden|Description|
|-----|-----|
|Ocp-Apim-Subscription-Key|Begärandehuvud som krävs|

## <a name="response-status-codes"></a>Statuskoder för svar

Följande är möjliga HTTP-statuskoder som en begäran returnerar.

| Statuskod| Description|
|-----|-----|
|200|OK. Avbryt begäran har skickats|
|401|Obehörig. Kontrollera dina autentiseringsuppgifter.|
|404|Hittades inte. Resursen hittades inte. 
|500|Internt serverfel.
|Andra statuskoder|<ul><li>För många begäranden</li><li>Tillfälligt otillgänglig server</li></ul>|

## <a name="cancel-translation-response"></a>Avbryt översättningssvar

### <a name="successful-response"></a>Lyckat svar

Följande information returneras i ett lyckat svar.

|Namn|Typ|Description|
|--- |--- |--- |
|id|sträng|ÅTGÄRDENs ID.|
|createdDateTimeUtc|sträng|Åtgärden skapade datum/tid.|
|lastActionDateTimeUtc|sträng|Datum/tid då åtgärdens status har uppdaterats.|
|status|Sträng|Lista över möjliga statusar för jobb eller dokument: <ul><li>Avbrutna</li><li>Avbryta</li><li>Misslyckad</li><li>NotStarted</li><li>Körs</li><li>Lyckades</li><li>ValidationFailed</li></ul>|
|sammanfattning|StatusSummary|Sammanfattning som innehåller informationen som anges nedan.|
|summary.total|heltal|Antal dokument totalt.|
|summary.failed|heltal|Antalet dokument misslyckades.|
|summary.success|heltal|Antal dokument som har översatts.|
|summary.inProgress|heltal|Antal dokument som pågår.|
|summary.notYetStarted|heltal|Antal dokument som ännu inte har börjat bearbetas.|
|summary.cancelled|heltal|Antal avbokningar.|
|summary.totalCharacterCharged|heltal|Totalt antal tecken som debiteras av API:et.|

### <a name="error-response"></a>Felsvar

|Namn|Typ|Description|
|--- |--- |--- |
|kod|sträng|Uppräkningar som innehåller felkoder på hög nivå. Möjliga värden:<br/><ul><li>InternalServerError</li><li>InvalidArgument</li><li>InvalidRequest</li><li>RequestRateTooHigh</li><li>ResourceNotFound</li><li>ServiceUnavailable</li><li>Behörighet saknas</li></ul>|
|meddelande|sträng|Hämtar felmeddelande på hög nivå.|
|Mål|sträng|Hämtar källan till felet. Det skulle till exempel vara "dokument" eller "dokument-ID" för ett ogiltigt dokument.|
|innerError|InnerErrorV2|Nytt innerfelsformat som följer riktlinjerna för Cognitive Services API. Den innehåller de obligatoriska egenskaperna ErrorCode, message och optional properties target, details(key value pair), inner error (kan kapslas).|
|innerError.code|sträng|Hämtar kodfelsträngen.|
|Inre. Eroor.message|sträng|Hämtar felmeddelande på hög nivå.|

## <a name="examples"></a>Exempel

### <a name="example-successful-response"></a>Exempel på lyckat svar

Följande JSON-objekt är ett exempel på ett lyckat svar.

Statuskod: 200

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
