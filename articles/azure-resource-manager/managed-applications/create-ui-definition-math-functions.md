---
title: Skapa GRÄNSSNITTs definition matematik funktioner
description: Beskriver de funktioner som ska användas när du utför matematik åtgärder.
author: tfitzmac
ms.topic: conceptual
ms.date: 07/13/2020
ms.author: tomfitz
ms.openlocfilehash: 022e59d847a4d89b4243223637fc6fd1e73255a9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "87099916"
---
# <a name="createuidefinition-math-functions"></a>CreateUiDefinition matematiska funktioner

Med funktionerna kan du utföra matematik åtgärder.

## <a name="add"></a>add

Adderar två tal och returnerar resultatet.

Följande exempel returnerar `3` :

```json
"[add(1, 2)]"
```

## <a name="ceil"></a>ceil

Returnerar det största heltal som är större än eller lika med det angivna talet.

Följande exempel returnerar `4` :

```json
"[ceil(3.14)]"
```

## <a name="div"></a>div

Dividerar det första talet med det andra talet och returnerar resultatet. Resultatet är alltid ett heltal.

Följande exempel returnerar `2` :

```json
"[div(6, 3)]"
```

## <a name="floor"></a>maximal

Returnerar det största heltal som är mindre än eller lika med det angivna talet.

Följande exempel returnerar `3` :

```json
"[floor(3.14)]"
```

## <a name="max"></a>max

Returnerar det större talet av de två talen.

Följande exempel returnerar `2` :

```json
"[max(1, 2)]"
```

## <a name="min"></a>min

Returnerar den mindre av de två talen.

Följande exempel returnerar `1` :

```json
"[min(1, 2)]"
```

## <a name="mod"></a>rest

Dividerar det första talet med det andra talet och returnerar resten.

Följande exempel returnerar `0` :

```json
"[mod(6, 3)]"
```

Följande exempel returnerar `2` :

```json
"[mod(6, 4)]"
```

## <a name="mul"></a>mul

Multiplicerar två tal och returnerar resultatet.

Följande exempel returnerar `6` :

```json
"[mul(2, 3)]"
```

## <a name="rand"></a>slump

Returnerar ett slumptal inom det angivna intervallet. Den här funktionen genererar inte kryptografiskt säkra slumptal.

Följande exempel kan returnera `42` :

```json
"[rand(-100, 100)]"
```

## <a name="range"></a>intervall

Genererar en sekvens med heltal inom det angivna intervallet.

Följande exempel returnerar `[1,2,3]` :

```json
"[range(1, 3)]"
```

## <a name="sub"></a>Build

Subtraherar den andra siffran från det första talet och returnerar resultatet.

Följande exempel returnerar `1` :

```json
"[sub(3, 2)]"
```

## <a name="next-steps"></a>Nästa steg

* En introduktion till Azure Resource Manager finns i [Azure Resource Manager översikt](../management/overview.md).
