---
title: Skapa användar gränssnittets definitions datum funktioner
description: Beskriver de funktioner som används när du arbetar med datum värden.
author: tfitzmac
ms.topic: conceptual
ms.date: 07/13/2020
ms.author: tomfitz
ms.openlocfilehash: 80484fd15e51bae7036c43bd3ca8fe8167387ede
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "87099924"
---
# <a name="createuidefinition-date-functions"></a>CreateUiDefinition datum funktioner

De funktioner som ska användas när du arbetar med datum värden.

## <a name="addhours"></a>addHours

Lägger till ett heltals antal timmar för den angivna tidsstämpeln.

Följande exempel returnerar `"1991-01-01T00:59:59.000Z"` :

```json
"[addHours('1990-12-31T23:59:59Z', 1)]"
```

## <a name="addminutes"></a>addMinutes

Lägger till ett integral antal minuter i den angivna tidsstämpeln.

Följande exempel returnerar `"1991-01-01T00:00:59.000Z"` :

```json
"[addMinutes('1990-12-31T23:59:59Z', 1)]"
```

## <a name="addseconds"></a>addSeconds
Lägger till ett heltals antal sekunder till den angivna tidsstämpeln.

Följande exempel returnerar `"1991-01-01T00:00:00.000Z"` :

```json
"[addSeconds('1990-12-31T23:59:60Z', 1)]"
```

## <a name="utcnow"></a>utcNow

Returnerar en sträng i ISO 8601-formatet för aktuellt datum och aktuell tid på den lokala datorn.

Följande exempel kan returnera `"1990-12-31T23:59:59.000Z"` :

```json
"[utcNow()]"
```

## <a name="next-steps"></a>Nästa steg

* En introduktion till Azure Resource Manager finns i [Azure Resource Manager översikt](../management/overview.md).
