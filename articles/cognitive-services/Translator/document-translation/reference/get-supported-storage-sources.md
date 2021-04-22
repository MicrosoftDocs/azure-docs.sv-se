---
title: Hämta metod för lagringskällor som stöds
titleSuffix: Azure Cognitive Services
description: Metoden get supported storage sources (Hämta lagringskällor som stöds) returnerar en lista över lagringskällor som stöds.
services: cognitive-services
author: jann-skotdal
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 04/21/2021
ms.author: v-jansk
ms.openlocfilehash: 03203206da6ae3ea9a1174aebafda0b58e22ea41
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2021
ms.locfileid: "107864855"
---
# <a name="get-supported-storage-sources"></a>Hämta lagringskällor som stöds

Metoden Hämta lagringskällor som stöds returnerar en lista över lagringskällor/alternativ som stöds av tjänsten För dokumentöversättning.

## <a name="request-url"></a>Begärans-URL

Skicka en `GET` begäran till:
```HTTP
GET https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0-preview.1/storagesources
```

Lär dig hur du hittar [ditt anpassade domännamn](../get-started-with-document-translation.md#find-your-custom-domain-name).

> [!IMPORTANT]
>
> * **Alla API-begäranden till tjänsten För dokumentöversättning krävs en slutpunkt för en anpassad domän.**
> * Du kan inte använda slutpunkten som finns  på sidan Azure Portal nycklar och slutpunkter eller slutpunkten för global översättning– – för att göra `api.cognitive.microsofttranslator.com` HTTP-begäranden till dokumentöversättning.

## <a name="request-headers"></a>Begärandehuvuden

Begärandehuvudena är:

|Sidhuvuden|Description|
|--- |--- |
|Ocp-Apim-Subscription-Key|Begärandehuvud som krävs|

## <a name="response-status-codes"></a>Statuskoder för svar

Följande är möjliga HTTP-statuskoder som en begäran returnerar.

|Statuskod|Description|
|--- |--- |
|200|OK. En lyckad begäran och returnerar listan över lagringskällor.|
|500|Internt serverfel.|
|Andra statuskoder|<ul><li>För många begäranden</li><li>Tillfälligt otillgänglig server</li></ul>|

## <a name="get-supported-storage-sources-response"></a>Svar från Hämta lagringskällor som stöds

### <a name="successful-get-supported-storage-sources-response"></a>Svar från get supported storage sources (Hämta lagringskällor som stöds)
Bastyp för listretur i API:et Hämta lagringskällor som stöds.

|Namn|Typ|Description|
|--- |--- |--- |
|värde|sträng []|Lista över objekt.|


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
    "AzureBlob"
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