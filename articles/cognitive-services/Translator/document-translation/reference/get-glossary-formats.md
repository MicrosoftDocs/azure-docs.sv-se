---
title: Dokument översättning Hämta ord Forms metod
titleSuffix: Azure Cognitive Services
description: Metoden GET ord List format returnerar listan över ord format som stöds.
services: cognitive-services
author: jann-skotdal
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 03/25/2021
ms.author: v-jansk
ms.openlocfilehash: fa4f89fb312e76d72447552b156d35bf3d0404bc
ms.sourcegitcommit: c94e282a08fcaa36c4e498771b6004f0bfe8fb70
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2021
ms.locfileid: "105613141"
---
# <a name="document-translation-get-glossary-formats"></a>Dokument översättning: Hämta ord lista

Metoden GET ord List format returnerar en lista över ord konverterings format som stöds av dokument översättnings tjänsten. Listan innehåller det gemensamma fil tillägget som används.

## <a name="request-url"></a>Begärans-URL

Skicka en `GET` begäran till:
```HTTP
GET https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0-preview.1/glossaries/formats
```

Lär dig hur du hittar ditt [anpassade domän namn](../get-started-with-document-translation.md#find-your-custom-domain-name).

> [!IMPORTANT]
>
> * **Alla API-förfrågningar till dokument översättnings tjänsten kräver en anpassad domän slut punkt**.
> * Du kan inte använda slut punkten som finns på Azure Portal resurs _nycklar och slut punkts_ sida eller den globala Translator-slutpunkten – `api.cognitive.microsofttranslator.com` för att göra HTTP-förfrågningar till dokument översättning.

## <a name="request-headers"></a>Begärandehuvuden

Begärandehuvuden är:

|Sidhuvuden|Description|
|--- |--- |
|Ocp-Apim-Subscription-Key|Nödvändigt begär ande huvud|

## <a name="response-status-codes"></a>Svars status koder

Följande är de möjliga HTTP-statuskod som en begäran returnerar.

|Statuskod|Description|
|--- |--- |
|200|OK. Returnerar en lista över fil format som stöds.|
|500|Internt Server fel.|
|Andra status koder|<ul><li>För många begär Anden</li><li>Servern är tillfälligt otillgänglig</li></ul>|


## <a name="get-glossary-formats-response"></a>Hämta svar på ord listans format

Bastyp för List retur i API: t get ordboks format.

### <a name="successful-get-glossary-formats-response"></a>Slutför svar på ord listans format

Bastyp för List retur i API: t get ordboks format.

|Statuskod|Description|
|--- |--- |
|200|OK. Returnerar en lista över fil format som stöds.|
|500|Internt Server fel.|
|Andra status koder|För många temporära requestsServer är inte tillgängliga|

### <a name="error-response"></a>Fel svar

|Namn|Typ|Description|
|--- |--- |--- |
|kod|sträng|Uppräkningar som innehåller fel koder på hög nivå. Möjliga värden:<br/><ul><li>InternalServerError</li><li>InvalidArgument</li><li>InvalidRequest</li><li>RequestRateTooHigh</li><li>ResourceNotFound</li><li>ServiceUnavailable</li><li>Behörighet saknas</li></ul>|
|meddelande|sträng|Hämtar fel meddelande på hög nivå.|
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
            "format": "XLIFF",
            "fileExtensions": [
                ".xlf"
            ],
            "contentTypes": [
                "application/xliff+xml"
            ],
            "versions": [
                "1.0",
                "1.1",
                "1.2"
            ]
        },
        {
            "format": "TSV",
            "fileExtensions": [
                ".tsv",
                ".tab"
            ],
            "contentTypes": [
                "text/tab-separated-values"
            ],
            "versions": []
        }
    ]
}
```

### <a name="example-error-response"></a>Exempel fel svar
följande är ett exempel på ett fel svar. Schemat för andra fel koder är detsamma.

Status kod: 500

```JSON
{
  "error": {
    "code": "InternalServerError",
    "message": "Internal Server Error",
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
