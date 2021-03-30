---
title: Skapa funktioner för definitions samling för gränssnitt
description: Beskriver de funktioner som används när du arbetar med samlingar, t. ex. matriser och objekt.
author: tfitzmac
ms.topic: conceptual
ms.date: 07/13/2020
ms.author: tomfitz
ms.openlocfilehash: 2a075c5c99f457681cd49e75014487bf9cca263c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "87099928"
---
# <a name="createuidefinition-collection-functions"></a>Funktioner för CreateUiDefinition-samling

Dessa funktioner kan användas med samlingar, t. ex. JSON-strängar, matriser och objekt.

## <a name="contains"></a>innehåller

Returnerar `true` om en sträng innehåller den angivna under strängen, om en matris innehåller det angivna värdet, eller om ett objekt innehåller den angivna nyckeln.

### <a name="example-string-contains"></a>Exempel: strängen innehåller

Följande exempel returnerar `true` :

```json
"[contains('webapp', 'web')]"
```

### <a name="example-array-contains"></a>Exempel: matris innehåller

Antag `element1` returnerar `[1, 2, 3]` . Följande exempel returnerar `false` :

```json
"[contains(steps('demoStep').element1, 4)]"
```

### <a name="example-object-contains"></a>Exempel: objekt innehåller

Antag `element1` returnerar:

```json
{
  "key1": "Linux",
  "key2": "Windows"
}
```

Följande exempel returnerar `true` :

```json
"[contains(steps('demoStep').element1, 'key1')]"
```

## <a name="empty"></a>tomt

Returnerar `true` om strängen, matrisen eller objektet är null eller tomt.

### <a name="example-string-empty"></a>Exempel: sträng tom

Följande exempel returnerar `true` :

```json
"[empty('')]"
```

### <a name="example-array-empty"></a>Exempel: matris tom

Antag `element1` returnerar `[1, 2, 3]` . Följande exempel returnerar `false` :

```json
"[empty(steps('demoStep').element1)]"
```

### <a name="example-object-empty"></a>Exempel: objekt tomt

Antag `element1` returnerar:

```json
{
  "key1": "Linux",
  "key2": "Windows"
}
```

Följande exempel returnerar `false` :

```json
"[empty(steps('demoStep').element1)]"
```

### <a name="example-null-and-undefined"></a>Exempel: null och odefinierat

Antag `element1` är `null` eller är odefinierat. Följande exempel returnerar `true` :

```json
"[empty(steps('demoStep').element1)]"
```

## <a name="filter"></a>filter

Returnerar en ny matris efter att ha använt den filtrerings logik som angetts som en lambda-funktion. Den första parametern är den matris som ska användas för filtrering. Den andra parametern är lambda-funktionen som anger filtrerings logiken.

I följande exempel returneras matrisen `[ { "name": "abc" } ]` .

```json
"[filter(parse('[{\"name\":\"abc\"},{\"name\":\"xyz\"}]'), (item) => contains(item.name, 'abc'))]"
```

## <a name="first"></a>förstagångskörningen

Returnerar det första tecken i den angivna strängen. första värdet för den angivna matrisen; eller den första nyckeln och värdet för det angivna objektet.

### <a name="example-string-first"></a>Exempel: String först

Följande exempel returnerar `"c"` :

```json
"[first('contoso')]"
```

### <a name="example-array-first"></a>Exempel: matris först

Antag `element1` returnerar `[1, 2, 3]` . Följande exempel returnerar `1` :

```json
"[first(steps('demoStep').element1)]"
```

#### <a name="example-object-first"></a>Exempel: objekt först

Antag `element1` returnerar:

```json
{
  "key1": "Linux",
  "key2": "Windows"
}
```

Följande exempel returnerar `{"key1": "Linux"}` :

```json
"[first(steps('demoStep').element1)]"
```

## <a name="last"></a>pågå

Returnerar det sista tecken i den angivna strängen, det sista värdet för den angivna matrisen eller den sista nyckeln och värdet för det angivna objektet.

### <a name="example-string-last"></a>Exempel: sträng senast

Följande exempel returnerar `"o"` :

```json
"[last('contoso')]"
```

### <a name="example-array-last"></a>Exempel: matris senast

Antag `element1` returnerar `[1, 2, 3]` . Följande exempel returnerar `3` :

```json
"[last(steps('demoStep').element1)]"
```

### <a name="example-object-last"></a>Exempel: objekt senaste

Antag `element1` returnerar:

```json
{
  "key1": "Linux",
  "key2": "Windows"
}
```

Följande exempel returnerar `{"key2": "Windows"}` :

```json
"[last(steps('demoStep').element1)]"
```

## <a name="length"></a>length

Returnerar antalet tecken i en sträng, antalet värden i en matris eller antalet nycklar i ett objekt.

### <a name="example-string-length"></a>Exempel: sträng längd

Följande exempel returnerar `7` :

```json
"[length('Contoso')]"
```

### <a name="example-array-length"></a>Exempel: mat ris längd

Antag `element1` returnerar `[1, 2, 3]` . Följande exempel returnerar `3` :

```json
"[length(steps('demoStep').element1)]"
```

### <a name="example-object-length"></a>Exempel: objekt längd

Antag `element1` returnerar:

```json
{
  "key1": "Linux",
  "key2": "Windows"
}
```

Följande exempel returnerar `2` :

```json
"[length(steps('demoStep').element1)]"
```

## <a name="map"></a>map

Returnerar en ny matris efter anrop av en lambda-funktion på en angiven matris. Den första parametern är den matris som ska användas för Lambda-funktionen. Den andra parametern är lambda-funktionen.

Följande exempel returnerar en ny matris med varje värde Double. Resultatet är `[2, 4, 6]` .

```json
"[map(parse('[1, 2, 3]'), (item) => mul(2, item))]"
```

Följande exempel returnerar en ny matris `["abc", "xyz"]` .

```json
"[map(parse('[{\"name\":\"abc\"},{\"name\":\"xyz\"}]'), (item) => item.name)]"
```

## <a name="skip"></a>hoppa över

Hoppar över ett angivet antal element i en samling och returnerar sedan de återstående elementen.

### <a name="example-string-skip"></a>Exempel: sträng hoppa över

Följande exempel returnerar `"app"` :

```json
"[skip('webapp', 3)]"
```

### <a name="example-array-skip"></a>Exempel: matrisen Skip

Antag `element1` returnerar `[1, 2, 3]` . Följande exempel returnerar `[3]` :

```json
"[skip(steps('demoStep').element1, 2)]"
```

### <a name="example-object-skip"></a>Exempel: objekt Skip

Antag `element1` returnerar:

```json
{
  "key1": "Linux",
  "key2": "Windows"
}
```
Följande exempel returnerar `{"key2": "Windows"}` :

```json
"[skip(steps('demoStep').element1, 1)]"
```

## <a name="split"></a>split

Returnerar en sträng mat ris som innehåller del strängarna för indatatypen avgränsade med avgränsaren.

I följande exempel returneras matrisen `[ "555", "867", "5309" ]` .

```json
"[split('555-867-5309', '-')]"
```

## <a name="take"></a>take

Returnerar ett angivet antal sammanhängande tecken från början av strängen, ett angivet antal sammanhängande värden från början av matrisen, eller ett angivet antal sammanhängande nycklar och värden från början av objektet.

### <a name="example-string-take"></a>Exempel: strängen tar

Följande exempel returnerar `"web"` :

```json
"[take('webapp', 3)]"
```

### <a name="example-array-take"></a>Exempel: matrisen tar

Antag `element1` returnerar `[1, 2, 3]` . Följande exempel returnerar `[1, 2]` :

```json
"[take(steps('demoStep').element1, 2)]"
```

### <a name="example-object-take"></a>Exempel: objekt tar

Antag `element1` returnerar:

```json
{
  "key1": "Linux",
  "key2": "Windows"
}
```

Följande exempel returnerar `{"key1": "Linux"}` :

```json
"[take(steps('demoStep').element1, 1)]"
```

## <a name="next-steps"></a>Nästa steg

* En introduktion till Azure Resource Manager finns i [Azure Resource Manager översikt](../management/overview.md).
