---
title: Skapa konverterings funktioner för GRÄNSSNITTs definition
description: Beskriver de funktioner som används när du konverterar värden mellan data typer och kodningar.
author: tfitzmac
ms.topic: conceptual
ms.date: 07/13/2020
ms.author: tomfitz
ms.openlocfilehash: b69cd35b27b343da08727b4c4ee9b4fd025e1df7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "87099927"
---
# <a name="createuidefinition-conversion-functions"></a>Funktioner för CreateUiDefinition-konvertering

Dessa funktioner kan användas för att konvertera värden mellan JSON-datatyper och-kodningar.

## <a name="bool"></a>boolesk

Konverterar parametern till ett booleskt värde. Den här funktionen stöder parametrar av typen Number, String och Boolean. Liknar booleska värden i Java Script, alla värden utom `0` eller `'false'` returnerar `true` .

Följande exempel returnerar `true` :

```json
"[bool(1)]"
```

Följande exempel returnerar `false` :

```json
"[bool(0)]"
```

Följande exempel returnerar `true` :

```json
"[bool(true)]"
```

Följande exempel returnerar `true` :

```json
"[bool('true')]"
```

## <a name="decodebase64"></a>decodeBase64

Avkodar parametern från en Base-64-kodad sträng. Den här funktionen stöder endast parametrar av typen sträng.

Följande exempel returnerar `"Contoso"` :

```json
"[decodeBase64('Q29udG9zbw==')]"
```

## <a name="decodeuricomponent"></a>decodeUriComponent

Avkodar parametern från en URL-kodad sträng. Den här funktionen stöder endast parametrar av typen sträng.

Följande exempel returnerar `"https://portal.azure.com/"` :

```json
"[decodeUriComponent('https%3A%2F%2Fportal.azure.com%2F')]"
```

## <a name="encodebase64"></a>encodeBase64

Kodar parametern till en Base-64-kodad sträng. Den här funktionen stöder endast parametrar av typen sträng.

Följande exempel returnerar `"Q29udG9zbw=="` :

```json
"[encodeBase64('Contoso')]"
```

## <a name="encodeuricomponent"></a>encodeUriComponent

Kodar parametern till en URL-kodad sträng. Den här funktionen stöder endast parametrar av typen sträng.

Följande exempel returnerar `"https%3A%2F%2Fportal.azure.com%2F"` :

```json
"[encodeUriComponent('https://portal.azure.com/')]"
```

## <a name="float"></a>flyt

Konverterar parametern till en svävande punkt. Den här funktionen stöder parametrar av typen Number och String.

Följande exempel returnerar `1.0` :

```json
"[float('1.0')]"
```

Följande exempel returnerar `2.9` :

```json
"[float(2.9)]"
```

## <a name="int"></a>int

Konverterar parametern till ett heltal. Den här funktionen stöder parametrar av typen Number och String.

Följande exempel returnerar `1` :

```json
"[int('1')]"
```

Följande exempel returnerar `2` :

```json
"[int(2.9)]"
```

## <a name="parse"></a>parse

Konverterar parametern till en ursprunglig typ. Funktionen är med andra ord inversen till `string()` . Den här funktionen stöder endast parametrar av typen sträng.

Följande exempel returnerar `1` :

```json
"[parse('1')]"
```

Följande exempel returnerar `true` :

```json
"[parse('true')]"
```

Följande exempel returnerar `[1,2,3]` :

```json
"[parse('[1,2,3]')]"
```

Följande exempel returnerar `{"type":"webapp"}` :

```json
"[parse('{\"type\":\"webapp\"}')]"
```

## <a name="string"></a>sträng

Konverterar parametern till en sträng. Den här funktionen stöder parametrar av alla JSON-datatyper.

Följande exempel returnerar `"1"` :

```json
"[string(1)]"
```

Följande exempel returnerar `"2.9"` :

```json
"[string(2.9)]"
```

Följande exempel returnerar `"[1,2,3]"` :

```json
"[string([1,2,3])]"
```

Följande exempel returnerar `"{"type":"webapp"}"` :

```json
"[string({\"type\":\"webapp\"})]"
```

## <a name="next-steps"></a>Nästa steg

* En introduktion till Azure Resource Manager finns i [Azure Resource Manager översikt](../management/overview.md).
