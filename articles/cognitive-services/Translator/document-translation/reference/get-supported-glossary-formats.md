---
title: Hämta den metod för ordlistasformat som stöds
titleSuffix: Azure Cognitive Services
description: Metoden get supported glossary formats (Hämta format för ordlista som stöds) returnerar listan över ordlisteformat som stöds.
services: cognitive-services
author: jann-skotdal
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 03/25/2021
ms.author: v-jansk
ms.openlocfilehash: b2419c9d1fe32390afcd81b77d1cf32900b9fe8a
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2021
ms.locfileid: "107836282"
---
# <a name="get-supported-glossary-formats"></a>Hämta ordlisteformat som stöds

Metoden Get supported glossary formats (Hämta ordlisteformat som stöds) returnerar en lista över de ordlisteformat som stöds av tjänsten För dokumentöversättning. Listan innehåller det vanliga filnamnstillägget som används.

## <a name="request-url"></a>Begärans-URL

Skicka en `GET` begäran till:
```HTTP
GET https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0-preview.1/glossaries/formats
```

Lär dig hur du hittar [ditt anpassade domännamn](../get-started-with-document-translation.md#find-your-custom-domain-name).

> [!IMPORTANT]
>
> * **Alla API-begäranden till tjänsten för dokumentöversättning kräver en anpassad domänslutpunkt.**
> * Du kan inte använda slutpunkten som finns  på din Azure Portal resursnycklar och slutpunktssida eller den globala översättningsslutpunkten– – för `api.cognitive.microsofttranslator.com` att göra HTTP-begäranden till dokumentöversättning.

## <a name="request-headers"></a>Begärandehuvuden

Begärandehuvuden är:

|Sidhuvuden|Description|
|--- |--- |
|Ocp-Apim-Subscription-Key|Begärandehuvud som krävs|

## <a name="response-status-codes"></a>Statuskoder för svar

Följande är möjliga HTTP-statuskoder som en begäran returnerar.

|Statuskod|Description|
|--- |--- |
|200|OK. Returnerar listan över ordlistefilformat som stöds.|
|500|Internt serverfel.|
|Andra statuskoder|<ul><li>För många begäranden</li><li>Tillfälligt otillgänglig server</li></ul>|


## <a name="get-supported-glossary-formats-response"></a>Svar från Get supported glossary formats (Hämta stödda ordlisteformat)

Bastyp för listretur i API:et Get supported glossary formats (Hämta stödda ordlisteformat).

### <a name="successful-get-supported-glossary-formats-response"></a>Svar från get supported glossary formats (Hämta ordlisteformat som stöds) lyckades

Bastyp för listretur i API:et Get supported glossary formats (Hämta ordlisteformat som stöds).

|Statuskod|Description|
|--- |--- |
|200|OK. Returnerar listan över ordlistefilformat som stöds.|
|500|Internt serverfel.|
|Andra statuskoder|För många begärandenServer är tillfälligt otillgänglig|

### <a name="error-response"></a>Felsvar

|Namn|Typ|Description|
|--- |--- |--- |
|kod|sträng|Uppräkningar som innehåller felkoder på hög nivå. Möjliga värden:<br/><ul><li>InternalServerError</li><li>InvalidArgument</li><li>InvalidRequest</li><li>RequestRateTooHigh</li><li>ResourceNotFound</li><li>ServiceUnavailable</li><li>Behörighet saknas</li></ul>|
|meddelande|sträng|Hämtar felmeddelande på hög nivå.|
|innerError|InnerErrorV2|Nytt innerfelsformat som följer riktlinjerna för Cognitive Services API. Den innehåller de obligatoriska egenskaperna ErrorCode, message och optional properties target, details(key value pair), inner error (detta kan kapslas).|
|innerError.code|sträng|Hämtar kodfelsträngen.|
|innerError.message|sträng|Hämtar felmeddelande på hög nivå.|

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

### <a name="example-error-response"></a>Exempel på felsvar
följande är ett exempel på ett felsvar. Schemat för andra felkoder är detsamma.

Statuskod: 500

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

Följ vår snabbstart om du vill veta mer om hur du använder dokumentöversättning och klientbiblioteket.

> [!div class="nextstepaction"]
> [Kom igång med dokumentöversättning](../get-started-with-document-translation.md)
