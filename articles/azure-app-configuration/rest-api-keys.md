---
title: Azure App konfiguration REST API-nycklar
description: Referens sidor för att arbeta med nycklar med hjälp av Azure App konfigurations REST API
author: AlexandraKemperMS
ms.author: alkemper
ms.service: azure-app-configuration
ms.topic: reference
ms.date: 08/17/2020
ms.openlocfilehash: 65ca190d7fbd6d8d4df473fbe2112eafbd031fde
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96932548"
---
# <a name="keys"></a>Nycklar

API-version: 1,0

Följande syntax representerar en nyckel resurs:

```http
{
    "name": [string]             // Name of the key
}
```

## <a name="operations"></a>Operations

Viktiga resurser har stöd för följande åtgärd:

- Lista

För alla åtgärder `name` är en valfri filter parameter. Om det utelämnas, betyder det vilken nyckel som *helst* .

## <a name="prerequisites"></a>Förutsättningar

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-rest-api-prereqs.md)]

## <a name="list-keys"></a>Lista nycklar

```http
GET /keys?api-version={api-version} HTTP/1.1
```

**Registrera**

```http
HTTP/1.1 200 OK
Content-Type: application/vnd.microsoft.appconfig.keyset+json; charset=utf-8"
```

```json
{
    "items": [
        {
          "name": "{key-name}"
        },
        ...
    ],
    "@nextLink": "{relative uri}"
}
```

## <a name="pagination"></a>Sidnumrering

Resultatet blir en sid brytning om antalet returnerade objekt överskrider svars gränsen. Följ de valfria svarshuvuden `Link` och Använd `rel="next"` för navigering. Alternativt innehåller innehållet en nästa länk i form av `@nextLink` egenskapen. Nästa länk innehåller `api-version` parameter.

```http
GET /keys?api-version={api-version} HTTP/1.1
```

**Svarade**

```http
HTTP/1.1 OK
Content-Type: application/vnd.microsoft.appconfig.keyset+json; charset=utf-8
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

## <a name="filtering"></a>Filtrering

Filtrering med ```name``` stöds.

```http
GET /keys?name={key-name}&api-version={api-version}
```

Följande filter stöds:

|Nyckel filter|Effekt|
|--|--|
|`name` utelämnas eller `name=*`|Matchar **alla** nycklar|
|`name=abc`|Matchar en nyckel med namnet  **ABC**|
|`name=abc*`|Matchar nyckelnamn som börjar med **abc**|
|`name=abc,xyz`|Matchar nyckel namn **ABC** eller **XYZ** (begränsad till 5 CSV)|

Följande tecken är reserverade: `*` , `\` , `,`

Om ett reserverat tecken ingår i värdet måste det föregås av `\{Reserved Character}` . Icke-reserverade tecken kan också undantas.

## <a name="filter-validation"></a>Filter verifiering

Om ett filter verifierings fel används är svaret HTTP `400` med fel information:

```http
HTTP/1.1 400 Bad Request
Content-Type: application/problem+json; charset=utf-8
```

```json
{
  "type": "https://azconfig.io/errors/invalid-argument",
  "title": "Invalid request parameter 'name'",
  "name": "name",
  "detail": "name(2): Invalid character",
  "status": 400
}
```

## <a name="examples"></a>Exempel

- Alla

    ```http
    GET /keys?api-version={api-version}
    ```

- Nyckel namnet börjar med **ABC**

    ```http
    GET  /keys?name=abc*&api-version={api-version}
    ```

- Nyckel namnet är antingen **ABC** eller **XYZ**

    ```http
    GET /keys?name=abc,xyz&api-version={api-version}
    ```

## <a name="request-specific-fields"></a>Begär vissa fält

Använd den valfria frågesträngparametern `$select` och ange en kommaavgränsad lista med begärda fält. Om `$select` parametern utelämnas innehåller svaret standard uppsättningen.

```http
GET /keys?$select=name&api-version={api-version} HTTP/1.1
```

## <a name="time-based-access"></a>Time-Based åtkomst

Få en representation av resultatet som det var vid en tidigare tidpunkt. Se del [2.1.1](https://tools.ietf.org/html/rfc7089#section-2.1)

```http
GET /keys&api-version={api-version} HTTP/1.1
Accept-Datetime: Sat, 12 May 2018 02:10:00 GMT
```

**Svarade**

```http
HTTP/1.1 200 OK
Content-Type: application/vnd.microsoft.appconfig.keyset+json"
Memento-Datetime: Sat, 12 May 2018 02:10:00 GMT
Link: <relative uri>; rel="original"
```

```json
{
    "items": [
        ....
    ]
}
```
