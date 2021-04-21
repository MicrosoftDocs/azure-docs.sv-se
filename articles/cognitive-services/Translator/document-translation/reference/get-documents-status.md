---
title: Hämta dokumentstatus
titleSuffix: Azure Cognitive Services
description: Statusmetoden get documents returnerar status för alla dokument i en batchdokumentöversättningsbegäran.
services: cognitive-services
author: jann-skotdal
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 03/25/2021
ms.author: v-jansk
ms.openlocfilehash: 1e52d4ff5ccb5047ed82cca2764e98a8bd212305
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2021
ms.locfileid: "107836285"
---
# <a name="get-documents-status"></a>Hämta dokumentstatus

Statusmetoden Hämta dokument returnerar status för alla dokument i en begäran om batchdokumentöversättning.

Dokumenten som ingår i svaret sorteras efter dokument-ID i fallande ordning. Om antalet dokument i svaret överskrider vår sidnumreringsgräns används sidinväxling på serversidan. Sidnumrerade svar indikerar ett partiellt resultat och inkluderar en fortsättningstoken i svaret. Frånvaron av en fortsättningstoken innebär att inga ytterligare sidor är tillgängliga.

$top och $skip kan användas för att ange ett antal resultat som ska returneras och en offset för samlingen. Servern respekterar de värden som anges av klienten. Klienter måste dock vara förberedda på att hantera svar som innehåller en annan sidstorlek eller som innehåller en fortsättningstoken.

När både $top och $skip ingår bör servern först tillämpa $skip och sedan $top på samlingen.

> [!NOTE]
> Om servern inte kan respektera $top och/eller $skip måste servern returnera ett fel till klienten som informerar om det i stället för att bara ignorera frågealternativen. Detta minskar risken för att klienten gör antaganden om de data som returneras.

## <a name="request-url"></a>Begärans-URL

Skicka en `GET` begäran till:
```HTTP
GET https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0-preview.1/batches/{id}/documents
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
|$skip|Falskt|Hoppa $skip poster i samlingen. När både $top och $skip anges $skip tillämpas först.|
|$top|Falskt|Ta $top posterna i samlingen. När både $top och $skip anges $skip tillämpas först.|

## <a name="request-headers"></a>Begärandehuvuden

Begärandehuvuden är:

|Sidhuvuden|Description|
|--- |--- |
|Ocp-Apim-Subscription-Key|Begärandehuvud som krävs|

## <a name="response-status-codes"></a>Statuskoder för svar

Följande är möjliga HTTP-statuskoder som en begäran returnerar.

|Statuskod|Description|
|--- |--- |
|200|OK. En lyckad begäran och returnerar status för dokumenten. HeadersRetry-After: heltalETag: sträng|
|400|Ogiltig begäran. Kontrollera indataparametrar.|
|401|Obehörig. Kontrollera dina autentiseringsuppgifter.|
|404|Resursen hittades inte.|
|500|Internt serverfel.|
|Andra statuskoder|<ul><li>För många begäranden</li><li>Tillfälligt otillgänglig server</li></ul>|


## <a name="get-documents-status-response"></a>Hämta dokumentstatussvar

### <a name="successful-get-documents-status-response"></a>Statussvar för hämta dokument lyckades

Följande information returneras i ett lyckat svar.

|Namn|Typ|Description|
|--- |--- |--- |
|@nextLink|sträng|URL för nästa sida. Null om inga fler sidor är tillgängliga.|
|värde|DocumentStatusDetail []|Informationsstatusen för enskilda dokument som anges nedan.|
|value.path|sträng|Platsen för dokumentet eller mappen.|
|value.createdDateTimeUtc|sträng|Datum/tid för åtgärden skapades.|
|value.lastActionDateTimeUt|sträng|Datum/tid då åtgärdens status har uppdaterats.|
|value.status|status|Lista över möjliga statusar för jobb eller dokument.<ul><li>Avbrutna</li><li>Avbryta</li><li>Misslyckad</li><li>Inte startad</li><li>Körs</li><li>Lyckades</li><li>ValidationFailed</li></ul>|
|value.to|sträng|Till språk.|
|value.progress|sträng|Översättningens förlopp om tillgängligt.|
|value.id|sträng|Dokument-ID.|
|value.characterCharged|heltal|Tecken som debiteras av API:et.|

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
      "path": "https://myblob.blob.core.windows.net/destinationContainer/fr/mydoc.txt",
      "createdDateTimeUtc": "2020-03-26T00:00:00Z",
      "lastActionDateTimeUtc": "2020-03-26T01:00:00Z",
      "status": "Running",
      "to": "fr",
      "progress": 0.1,
      "id": "273622bd-835c-4946-9798-fd8f19f6bbf2",
      "characterCharged": 0
    }
  ],
  "@nextLink": "https://westus.cognitiveservices.azure.com/translator/text/batch/v1.0.preview.1/operation/0FA2822F-4C2A-4317-9C20-658C801E0E55/documents?$top=5&$skip=15"
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
