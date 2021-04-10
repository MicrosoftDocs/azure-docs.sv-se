---
title: Dokument översättning Hämta åtgärds dokument status
titleSuffix: Azure Cognitive Services
description: Status metoden Hämta åtgärds dokument returnerar status för alla dokument i en översättnings förfrågan för batch-dokument.
services: cognitive-services
author: jann-skotdal
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 03/25/2021
ms.author: v-jansk
ms.openlocfilehash: c6cf33f32abb473badbb11bf58bd52ffba53d75c
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "105613100"
---
# <a name="document-translation-get-operation-documents-status"></a>Dokument översättning: Hämta åtgärds dokument status

Status metoden Hämta åtgärds dokument returnerar status för alla dokument i en översättnings förfrågan för batch-dokument.

Dokumenten som ingår i svaret sorteras efter dokument-ID i fallande ordning. Om antalet dokument i svaret överskrider vår växlings gräns används sid växling på Server sidan. Sid brytnings svar indikerar ett partiellt resultat och inkluderar en fortsättnings-token i svaret. Avsaknad av en fortsättnings-token innebär att inga ytterligare sidor är tillgängliga.

Du kan använda parametrarna $top och $skip Query för att ange ett antal resultat som ska returneras och en förskjutning för samlingen. Servern följer de värden som anges av klienten. Klienterna måste dock vara för beredda för att hantera svar som innehåller en annan sid storlek eller som innehåller en tilläggs-token.

När både $top och $skip ingår bör servern först tillämpa $skip och sedan $top i samlingen.

> [!NOTE]
> Om servern inte kan svara på $top och/eller $skip måste servern returnera ett fel till klienten som informerar om den i stället för att bara ignorera frågealternativen. Detta minskar risken för att klienten gör antaganden om de data som returneras.

## <a name="request-url"></a>Begärans-URL

Skicka en `GET` begäran till:
```HTTP
GET https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0-preview.1/batches/{id}/documents
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
|$skip|Falskt|Hoppa över $skip poster i samlingen. När både $top och $skip anges tillämpas $skip först.|
|$top|Falskt|Ta $top posterna i samlingen. När både $top och $skip anges tillämpas $skip först.|

## <a name="request-headers"></a>Begärandehuvuden

Begärandehuvuden är:

|Sidhuvuden|Beskrivning|
|--- |--- |
|Ocp-Apim-Subscription-Key|Nödvändigt begär ande huvud|

## <a name="response-status-codes"></a>Svars status koder

Följande är de möjliga HTTP-statuskod som en begäran returnerar.

|Statuskod|Beskrivning|
|--- |--- |
|200|OK. Lyckad begäran och returnerar dokumentets status. HeadersRetry – efter: integerETag: sträng|
|400|Ogiltig begäran. Kontrol lera indataparametrarna.|
|401|Tillstånd. Kontrol lera dina autentiseringsuppgifter.|
|404|Det gick inte att hitta resursen.|
|500|Internt Server fel.|
|Andra status koder|<ul><li>För många begär Anden</li><li>Servern är tillfälligt otillgänglig</li></ul>|


## <a name="get-operation-documents-status-response"></a>Hämta status svar för åtgärds dokument

### <a name="successful-get-operation-documents-status-response"></a>Status svaret för hämtning av åtgärds dokument

Följande information returneras i ett lyckat svar.

|Namn|Typ|Description|
|--- |--- |--- |
|@nextLink|sträng|URL för nästa sida. Null om inga fler sidor är tillgängliga.|
|värde|DocumentStatusDetail []|Detalj status för enskilda dokument i listan nedan.|
|värde. Path|sträng|Plats för dokumentet eller mappen.|
|Value. createdDateTimeUtc|sträng|Åtgärden skapades datum och tid.|
|Value. lastActionDateTimeUt|sträng|Datum och tid då åtgärdens status har uppdaterats.|
|värde. status|status|Lista med möjliga status värden för jobb eller dokument.<ul><li>Avbrutna</li><li>Avbryter</li><li>Misslyckad</li><li>NotStarted</li><li>Körs</li><li>Lyckades</li><li>ValidationFailed</li></ul>|
|value.to|sträng|Till språk.|
|värde. Progress|sträng|Förlopp för översättning om det är tillgängligt.|
|value.id|sträng|Dokument-ID.|
|Value. characterCharged|heltal|Tecken som debiteras av API: et.|

### <a name="error-response"></a>Fel svar

|Namn|Typ|Beskrivning|
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
