---
title: Azure App konfiguration REST API-etiketter
description: Referens sidor för att arbeta med etiketter med hjälp av Azure App konfigurations REST API
author: AlexandraKemperMS
ms.author: alkemper
ms.service: azure-app-configuration
ms.topic: reference
ms.date: 08/17/2020
ms.openlocfilehash: 5a59f5910d44f2a2b4cd75e7a1d51c2ed5dd51a4
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96932514"
---
# <a name="labels"></a>Etiketter

API-version: 1,0

**Etikett** resursen definieras enligt följande:

```json
{
      "name": [string]             // Name of the label
}
```

Stöder följande åtgärder:

- Lista

För alla åtgärder ``name`` är en valfri filter parameter. Om detta utelämnas **förutsätts** etiketten.

## <a name="prerequisites"></a>Förutsättningar

- Alla HTTP-begäranden måste autentiseras. Se avsnittet [Authentication](./rest-api-authentication-index.md) .
- Alla HTTP-begäranden måste innehålla explicit `api-version` . Se avsnittet [versions hantering](./rest-api-versioning.md) .

## <a name="list-labels"></a>List etiketter

```http
GET /labels?api-version={api-version} HTTP/1.1
```

**Registrera**

```http
HTTP/1.1 200 OK
Content-Type: application/vnd.microsoft.appconfig.labelset+json; charset=utf-8"
```

```json
{
    "items": [
        {
          "name": "{label-name}"
        },
        ...
    ],
    "@nextLink": "{relative uri}"
}
```

## <a name="pagination"></a>Sidnumrering

Resultatet blir en sid brytning om antalet returnerade objekt överskrider svars gränsen. Följ de valfria svarshuvuden `Link` och Använd `rel="next"` för navigering. Alternativt innehåller innehållet en nästa länk i form av `@nextLink` egenskapen. Nästa länk innehåller `api-version` parameter.

```http
GET /labels?api-version={api-version} HTTP/1.1
```

**Svarade**

```http
HTTP/1.1 OK
Content-Type: application/vnd.microsoft.appconfig.labelset+json; charset=utf-8
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

## <a name="filtering"></a>Filtrering

Filtrering med `name` stöds.

```http
GET /labels?name={label-name}&api-version={api-version}
```

### <a name="supported-filters"></a>Filter som stöds

|Nyckel filter|Effekt|
|--|--|
|`name` utelämnas eller `name=*`|Matchar **alla** etiketter|
|`name=abc`|Matchar en etikett med namnet  **ABC**|
|`name=abc*`|Matchar etikett namn som börjar med **ABC**|
|`name=abc,xyz`|Matchar etikett namn **ABC** eller **XYZ** (begränsat till 5 CSV)|

### <a name="reserved-characters"></a>Reserverade tecken

`*`, `\`, `,`

Om ett reserverat tecken ingår i värdet måste det föregås av `\{Reserved Character}` . Icke-reserverade tecken kan också undantas.

### <a name="filter-validation"></a>Filter verifiering

Om ett filter verifierings fel inträffar är svaret HTTP `400` med fel information:

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

### <a name="examples"></a>Exempel

- Alla

    ```http
    GET /labels?api-version={api-version}
    ```

- Etikett namn börjar med **ABC**

    ```http
    GET  /labels?name=abc*&api-version={api-version}
    ```

- Etikett namnet är antingen **ABC** eller **XYZ**

    ```http
    GET /labels?name=abc,xyz&api-version={api-version}
    ```

## <a name="request-specific-fields"></a>Begär vissa fält

Använd den valfria frågesträngparametern `$select` och ange en kommaavgränsad lista med begärda fält. Om `$select` parametern utelämnas innehåller svaret standard uppsättningen.

```http
GET /labels?$select=name&api-version={api-version} HTTP/1.1
```

## <a name="time-based-access"></a>Time-Based åtkomst

Få en representation av resultatet som det var vid en tidigare tidpunkt. Se del [2.1.1](https://tools.ietf.org/html/rfc7089#section-2.1)

```http
GET /labels&api-version={api-version} HTTP/1.1
Accept-Datetime: Sat, 12 May 2018 02:10:00 GMT
```

**Svarade**

```http
HTTP/1.1 200 OK
Content-Type: application/vnd.microsoft.appconfig.labelset+json"
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
