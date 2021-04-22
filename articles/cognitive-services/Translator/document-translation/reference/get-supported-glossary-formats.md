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
ms.date: 04/21/2021
ms.author: v-jansk
ms.openlocfilehash: ea22e6a3afe8ee90cb7b59d1aca0a37fc4fa03d6
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2021
ms.locfileid: "107864927"
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
> * Du kan inte använda slutpunkten som finns  på din Azure Portal resursnycklar och slutpunktssida eller den globala översättningsslutpunkten– – för att `api.cognitive.microsofttranslator.com` göra HTTP-begäranden till dokumentöversättning.

## <a name="request-headers"></a>Begärandehuvuden

Begärandehuvudena är:

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

### <a name="successful-get-supported-glossary-formats-response"></a>Svar från get supported glossary formats (Hämta ordlista som stöds) har lyckats

Bastyp för listretur i API:et Get supported glossary formats (Hämta stödda ordlisteformat).

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
