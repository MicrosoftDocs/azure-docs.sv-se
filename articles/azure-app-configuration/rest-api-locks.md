---
title: Azure App konfiguration REST API-lås
description: Referens sidor för att arbeta med nyckel/värde-lås med hjälp av Azure App konfigurations REST API
author: AlexandraKemperMS
ms.author: alkemper
ms.service: azure-app-configuration
ms.topic: reference
ms.date: 08/17/2020
ms.openlocfilehash: e99ce5595bae8ed64285317d9249da60e0fc1b83
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96932531"
---
# <a name="locks"></a>Lås

Detta API (version 1,0) tillhandahåller låsnings-och upplåsnings-semantik för nyckel värdes resursen. Den har stöd för följande åtgärder:

- Lås
- Ta bort lås

Om det finns `label` en sådan måste det vara ett explicit etikett värde (inte ett jokertecken). För alla åtgärder är det en valfri parameter. Om detta utelämnas, betyder det ingen etikett.

## <a name="prerequisites"></a>Förutsättningar

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-rest-api-prereqs.md)]

## <a name="lock-key-value"></a>Lås nyckel – värde

- Krävs: ``{key}`` , ``{api-version}``  
- Valfritt ``label``

```http
PUT /locks/{key}?label={label}&api-version={api-version} HTTP/1.1
```

**Registrera**

```http
HTTP/1.1 200 OK
Content-Type: application/vnd.microsoft.appconfig.kv+json; charset=utf-8"
```

```json
{
  "etag": "4f6dd610dd5e4deebc7fbaef685fb903",
  "key": "{key}",
  "label": "{label}",
  "content_type": null,
  "value": "example value",
  "created": "2017-12-05T02:41:26.4874615+00:00",
  "locked": true,
  "tags": []
}
```

Om nyckel värdet inte finns returneras följande svar:

```http
HTTP/1.1 404 Not Found
```

## <a name="unlock-key-value"></a>Lås upp nyckel värde

- Krävs: ``{key}`` , ``{api-version}``  
- Valfritt ``label``

```http
DELETE /locks/{key}?label={label}?api-version={api-version} HTTP/1.1
```

**Registrera**

```http
HTTP/1.1 200 OK
Content-Type: application/vnd.microsoft.appconfig.kv+json; charset=utf-8"
```

```json
{
  "etag": "4f6dd610dd5e4deebc7fbaef685fb903",
  "key": "{key}",
  "label": "{label}",
  "content_type": null,
  "value": "example value",
  "created": "2017-12-05T02:41:26.4874615+00:00",
  "locked": true,
  "tags": []
}
```

Om nyckel värdet inte finns returneras följande svar:

```http
HTTP/1.1 404 Not Found
```

## <a name="conditional-lock-and-unlock"></a>Villkorligt lås och lås upp

Använd `If-Match` eller begär huvuden för att förhindra tävlings förhållanden `If-None-Match` . `etag`Argumentet är en del av nyckel representationen. Om `If-Match` eller `If-None-Match` utelämnas är åtgärden ovillkorlig.

Följande begäran tillämpar bara åtgärden om den aktuella nyckel värdes representationen matchar den angivna `etag` :

```http
PUT|DELETE /locks/{key}?label={label}&api-version={api-version} HTTP/1.1
If-Match: "4f6dd610dd5e4deebc7fbaef685fb903"
```

Följande begäran tillämpar bara åtgärden om den aktuella nyckel värdes representationen finns, men inte matchar den angivna `etag` :

```http
PUT|DELETE /kv/{key}?label={label}&api-version={api-version} HTTP/1.1
If-None-Match: "4f6dd610dd5e4deebc7fbaef685fb903"
```
