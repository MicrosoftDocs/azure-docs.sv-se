---
title: Hämta dokumentstatusmetod
titleSuffix: Azure Cognitive Services
description: Metoden för att hämta dokumentstatus returnerar status för ett specifikt dokument.
services: cognitive-services
author: jann-skotdal
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 03/25/2021
ms.author: v-jansk
ms.openlocfilehash: 36cd10a0b04be21e9f332832b4381662eeedd01d
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2021
ms.locfileid: "107833678"
---
# <a name="get-document-status"></a>Hämta dokumentstatus

Metoden Hämta dokumentstatus returnerar status för ett visst dokument. Metoden returnerar översättningsstatusen för ett specifikt dokument baserat på begäran-ID och dokument-ID.

## <a name="request-url"></a>Begärans-URL

Skicka en `GET` begäran till:
```HTTP
GET https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0-preview.1/batches/{id}/documents/{documentId}
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
|documentId|Sant|Dokument-ID:t.|
|id|Sant|Batch-ID:t.|
## <a name="request-headers"></a>Begärandehuvuden

Begärandehuvudena är:

|Sidhuvuden|Description|
|--- |--- |
|Ocp-Apim-Subscription-Key|Begärandehuvud som krävs|

## <a name="response-status-codes"></a>Statuskoder för svar

Följande är möjliga HTTP-statuskoder som en begäran returnerar.

|Statuskod|Description|
|--- |--- |
|200|OK. En lyckad begäran godkänns av tjänsten. Åtgärdsinformationen returneras. HeadersRetry-After: heltalETag: sträng|
|401|Obehörig. Kontrollera dina autentiseringsuppgifter.|
|404|Hittades inte. Resursen hittades inte.|
|500|Internt serverfel.|
|Andra statuskoder|<ul><li>För många begäranden</li><li>Tillfälligt otillgänglig server</li></ul>|

## <a name="get-document-status-response"></a>Hämta svar på dokumentstatus

### <a name="successful-get-document-status-response"></a>Svar om att hämta dokumentstatus lyckades

|Namn|Typ|Description|
|--- |--- |--- |
|path|sträng|Platsen för dokumentet eller mappen.|
|createdDateTimeUtc|sträng|Datum/tid för åtgärden skapades.|
|lastActionDateTimeUtc|sträng|Datum/tid då åtgärdens status har uppdaterats.|
|status|Sträng|Lista över möjliga statusar för jobb eller dokument: <ul><li>Avbrutna</li><li>Avbryta</li><li>Misslyckad</li><li>Inte startad</li><li>Körs</li><li>Lyckades</li><li>ValidationFailed</li></ul>|
|på|sträng|Språkkod med två bokstäver för To Language. Se listan över språk.|
|progress|antal|Översättningens förlopp om tillgängligt|
|id|sträng|Dokument-ID.|
|characterCharged|heltal|Tecken som debiteras av API:et.|

### <a name="error-response"></a>Felsvar

|Namn|Typ|Description|
|--- |--- |--- |
|kod|sträng|Uppräkningar som innehåller felkoder på hög nivå. Möjliga värden:<br/><ul><li>InternalServerError</li><li>InvalidArgument</li><li>InvalidRequest</li><li>RequestRateTooHigh</li><li>ResourceNotFound</li><li>ServiceUnavailable</li><li>Behörighet saknas</li></ul>|
|meddelande|sträng|Hämtar felmeddelande på hög nivå.|
|innerError|InnerErrorV2|Nytt format för inre fel som överensstämmer Cognitive Services API-riktlinjer. Den innehåller de obligatoriska egenskaperna ErrorCode, message och optional properties target, details(key value pair), inner error (kan kapslas).|
|innerError.code|sträng|Hämtar kodfelsträngen.|
|innerError.message|sträng|Hämtar felmeddelande på hög nivå.|

## <a name="examples"></a>Exempel

### <a name="example-successful-response"></a>Exempel på lyckat svar
Följande JSON-objekt är ett exempel på ett lyckat svar.

```JSON
{
  "path": "https://myblob.blob.core.windows.net/destinationContainer/fr/mydoc.txt",
  "createdDateTimeUtc": "2020-03-26T00:00:00Z",
  "lastActionDateTimeUtc": "2020-03-26T01:00:00Z",
  "status": "Running",
  "to": "fr",
  "progress": 0.1,
  "id": "273622bd-835c-4946-9798-fd8f19f6bbf2",
  "characterCharged": 0
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
