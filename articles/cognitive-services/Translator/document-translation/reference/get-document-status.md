---
title: Dokument översättning hämta dokument status metod
titleSuffix: Azure Cognitive Services
description: Metoden hämta dokument status returnerar status för ett angivet dokument.
services: cognitive-services
author: jann-skotdal
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 03/25/2021
ms.author: v-jansk
ms.openlocfilehash: 79bc3d076c1a7e164cab9c3231b29be84370e04a
ms.sourcegitcommit: c94e282a08fcaa36c4e498771b6004f0bfe8fb70
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2021
ms.locfileid: "105613153"
---
# <a name="document-translation-get-document-status"></a>Dokument översättning: Hämta dokument status

Metoden hämta dokument status returnerar status för ett angivet dokument. Metoden returnerar översättnings statusen för ett särskilt dokument baserat på begärande-ID och dokument-ID.

## <a name="request-url"></a>Begärans-URL

Skicka en `GET` begäran till:
```HTTP
GET https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0-preview.1/batches/{id}/documents/{documentId}
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
|documentId|Sant|Dokument-ID: t.|
|id|Sant|Batch-ID.|
## <a name="request-headers"></a>Begärandehuvuden

Begärandehuvuden är:

|Sidhuvuden|Description|
|--- |--- |
|Ocp-Apim-Subscription-Key|Nödvändigt begär ande huvud|

## <a name="response-status-codes"></a>Svars status koder

Följande är de möjliga HTTP-statuskod som en begäran returnerar.

|Statuskod|Description|
|--- |--- |
|200|OK. Lyckad begäran och den accepteras av tjänsten. Åtgärds informationen returneras. HeadersRetry – efter: integerETag: sträng|
|401|Tillstånd. Kontrol lera dina autentiseringsuppgifter.|
|404|Hittades inte. Det gick inte att hitta resursen.|
|500|Internt Server fel.|
|Andra status koder|<ul><li>För många begär Anden</li><li>Servern är tillfälligt otillgänglig</li></ul>|

## <a name="get-document-status-response"></a>Hämta svar på dokument status

### <a name="successful-get-document-status-response"></a>Slutför svar för att hämta dokument status

|Namn|Typ|Description|
|--- |--- |--- |
|path|sträng|Plats för dokumentet eller mappen.|
|createdDateTimeUtc|sträng|Åtgärden skapades datum och tid.|
|lastActionDateTimeUtc|sträng|Datum och tid då åtgärdens status har uppdaterats.|
|status|Sträng|Lista med möjliga status värden för jobb eller dokument: <ul><li>Avbrutna</li><li>Avbryter</li><li>Misslyckad</li><li>NotStarted</li><li>Körs</li><li>Lyckades</li><li>ValidationFailed</li></ul>|
|på|sträng|Språk koden för två bokstäver på till språk. Se listan över språk.|
|progress|antal|Förlopp för översättningen om det är tillgängligt|
|id|sträng|Dokument-ID.|
|characterCharged|heltal|Tecken som debiteras av API: et.|

### <a name="error-response"></a>Fel svar

|Namn|Typ|Description|
|--- |--- |--- |
|kod|sträng|Uppräkningar som innehåller fel koder på hög nivå. Möjliga värden:<br/><ul><li>InternalServerError</li><li>InvalidArgument</li><li>InvalidRequest</li><li>RequestRateTooHigh</li><li>ResourceNotFound</li><li>ServiceUnavailable</li><li>Behörighet saknas</li></ul>|
|meddelande|sträng|Hämtar fel meddelande på hög nivå.|
|innerError|InnerErrorV2|Nytt inre fel format, som följer Cognitive Services API-riktlinjer. Det innehåller obligatoriska egenskaper för fel, meddelande och valfria egenskaper, information (nyckel värde par), inre fel (kan kapslas).|
|innerError. Code|sträng|Hämtar kod fel sträng.|
|innerError. Message|sträng|Hämtar fel meddelande på hög nivå.|

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
