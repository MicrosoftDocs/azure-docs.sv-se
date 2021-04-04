---
title: Azure App konfiguration REST API-nyckel-värde-revisioner
description: Referens sidor för att arbeta med nyckel/värde-revisioner med hjälp av Azure App konfigurations REST API
author: AlexandraKemperMS
ms.author: alkemper
ms.service: azure-app-configuration
ms.topic: reference
ms.date: 08/17/2020
ms.openlocfilehash: cfa117d1ed017170c279b7c4e0a146ae4edac108
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96932480"
---
# <a name="key-value-revisions"></a>Nyckel/värde-revisioner

En *nyckel värdes revision* definierar den historiska representationen av en nyckel värdes resurs. Revisioner upphör att gälla efter 7 dagar för lagring på kostnads fri nivå, eller 30 dagar för lager på standard nivå. Revisioner stöder `List` åtgärden.

För alla åtgärder ``key`` är en valfri parameter. Om det utelämnas, betyder det vilken nyckel som helst.

För alla åtgärder ``label`` är en valfri parameter. Om den utelämnas, betyder det vilken etikett som helst.

Den här artikeln gäller API version 1,0.

## <a name="prerequisites"></a>Förutsättningar

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-rest-api-prereqs.md)]

## <a name="list-revisions"></a>Lista revisioner

```http
GET /revisions?label=*&api-version={api-version} HTTP/1.1
```

**Registrera**

```http
HTTP/1.1 200 OK
Content-Type: application/vnd.microsoft.appconfig.kvset+json; charset=utf-8"
Accept-Ranges: items
```

```json
{
    "items": [
        {
          "etag": "4f6dd610dd5e4deebc7fbaef685fb903",
          "key": "{key}",
          "label": "{label}",
          "content_type": null,
          "value": "example value",
          "last_modified": "2017-12-05T02:41:26.4874615+00:00",
          "tags": []
        },
        ...
    ],
    "@nextLink": "{relative uri}"
}
```

## <a name="pagination"></a>Sidnumrering

Resultatet blir en sid brytning om antalet returnerade objekt överskrider svars gränsen. Följ det valfria ``Link`` svars huvudet och Använd ``rel="next"`` för navigering. Alternativt innehåller innehållet en nästa länk i form av ``@nextLink`` egenskapen.

```http
GET /revisions?api-version={api-version} HTTP/1.1
```

**Svarade**

```http
HTTP/1.1 OK
Content-Type: application/vnd.microsoft.appconfig.kvs+json; charset=utf-8
Accept-Ranges: items
Link: <{relative uri}>; rel="next"
```

```json
{
    "items": [
        ...
    ],
    "@nextLink": "{relative uri}"
}
```

## <a name="list-subset-of-revisions"></a>Lista över del versioner av revisioner

Använd `Range` rubriken för begäran. Svaret innehåller ett `Content-Range`-huvud. Om servern inte kan uppfylla det begärda intervallet svarar den med HTTP `416` ( `RangeNotSatisfiable` ).

```http
GET /revisions?api-version={api-version} HTTP/1.1
Range: items=0-2
```

**Response**

```http
HTTP/1.1 206 Partial Content
Content-Type: application/vnd.microsoft.appconfig.revs+json; charset=utf-8
Content-Range: items 0-2/80
```

## <a name="filtering"></a>Filtrering

En kombination av `key` och `label` filtrering stöds.
Använd valfria `key` parametrar och `label` frågesträngar.

```http
GET /revisions?key={key}&label={label}&api-version={api-version}
```

### <a name="supported-filters"></a>Filter som stöds

|Nyckel filter|Effekt|
|--|--|
|`key` utelämnas eller `key=*`|Matchar **alla** nycklar|
|`key=abc`|Matchar en nyckel med namnet  **ABC**|
|`key=abc*`|Matchar namn på nycklar som börjar med **ABC**|
|`key=*abc`|Matchar namn på nycklar som slutar med **ABC**|
|`key=*abc*`|Matchar namn på nycklar som innehåller **ABC**|
|`key=abc,xyz`|Matchar nycklarnas namn **ABC** eller **XYZ** (begränsat till 5 CSV)|

|Etikett filter|Effekt|
|--|--|
|`label` utelämnas eller `label=`|Matchar post utan etikett|
|`label=*`|Matchar **alla** etiketter|
|`label=prod`|Matchar etiketten **Prod**|
|`label=prod*`|Matchar etiketter som börjar med **Prod**|
|`label=*prod`|Matchar etiketter som slutar med **Prod**|
|`label=*prod*`|Matchar etiketter som innehåller **Prod**|
|`label=prod,test`|Matchar etiketter **Prod** eller **test** (begränsat till 5 CSV)|

### <a name="reserved-characters"></a>Reserverade tecken

De reserverade tecknen är:

`*`, `\`, `,`

Om ett reserverat tecken ingår i värdet måste det undantas med hjälp av `\{Reserved Character}` . Icke-reserverade tecken kan också undantas.

### <a name="filter-validation"></a>Filter verifiering

Om ett filter verifierings fel inträffar är svaret HTTP `400` med fel information:

```http
HTTP/1.1 400 Bad Request
Content-Type: application/problem+json; charset=utf-8
```

```json
{
  "type": "https://azconfig.io/errors/invalid-argument",
  "title": "Invalid request parameter '{filter}'",
  "name": "{filter}",
  "detail": "{filter}(2): Invalid character",
  "status": 400
}
```

### <a name="examples"></a>Exempel

- Vissa

    ```http
    GET /revisions
    ```

- Objekt där nyckel namnet börjar med **ABC**:

    ```http
    GET /revisions?key=abc*&api-version={api-version}
    ```

- Objekt där nyckel namnet antingen är **ABC** eller **XYZ**, och etiketter innehåller **Prod**:

    ```http
    GET /revisions?key=abc,xyz&label=*prod*&api-version={api-version}
    ```

## <a name="request-specific-fields"></a>Begär vissa fält

Använd den valfria frågesträngparametern `$select` och ange en kommaavgränsad lista med begärda fält. Om `$select` parametern utelämnas innehåller svaret standard uppsättningen.

```http
GET /revisions?$select=value,label,last_modified&api-version={api-version} HTTP/1.1
```

## <a name="time-based-access"></a>Tidsbaserad åtkomst

Få en representation av resultatet som det var vid en tidigare tidpunkt. Mer information finns i [http Framework för Time-Based åtkomst till resurs tillstånd--påminnelse](https://tools.ietf.org/html/rfc7089#section-2.1), section 2.1.1.

```http
GET /revisions?api-version={api-version} HTTP/1.1
Accept-Datetime: Sat, 12 May 2018 02:10:00 GMT
```

**Svarade**

```http
HTTP/1.1 200 OK
Content-Type: application/vnd.microsoft.appconfig.revs+json"
Memento-Datetime: Sat, 12 May 2018 02:10:00 GMT
Link: <{relative uri}>; rel="original"
```

```json
{
    "items": [
        ....
    ]
}
```
