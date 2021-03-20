---
title: Skapa logiska funktioner för GRÄNSSNITTs definition
description: Beskriver funktionerna för att utföra logiska åtgärder.
author: tfitzmac
ms.topic: conceptual
ms.date: 07/13/2020
ms.author: tomfitz
ms.openlocfilehash: 00d2f0eeb5d353c8ebd7ad30f6866f890d6cb42e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "87099920"
---
# <a name="createuidefinition-logical-functions"></a>CreateUiDefinition logiska funktioner

Dessa funktioner kan användas i villkors uttryck. Vissa funktioner kanske inte stöder alla typer av JSON-data.

## <a name="and"></a>och

Returnerar `true` om alla parametrar utvärderas till `true` . Den här funktionen stöder minst två parametrar av typen Boolean.

Följande exempel returnerar `true` :

```json
"[and(equals(0, 0), equals('web', 'web'), less(1, 2))]"
```

Följande exempel returnerar `false` :

```json
"[and(equals(0, 0), greater(1, 2))]"
```

## <a name="coalesce"></a>coalesce

Returnerar värdet för den första parametern som inte är null. Den här funktionen stöder alla JSON-datatyper.

Antag `element1` och `element2` är odefinierade. Följande exempel returnerar `"Contoso"` :

```json
"[coalesce(steps('demoStep').element1, steps('demoStep').element2, 'Contoso')]"
```

Den här funktionen är särskilt användbar i samband med valfritt anrop som inträffar på grund av användar åtgärd efter att sidan har lästs in. Ett exempel är om begränsningarna som placeras på ett fält i användar gränssnittet är beroende av det valda värdet för ett annat, **från början icke-synligt** fält. I det här fallet `coalesce()` kan användas för att tillåta funktionen att vara syntaktiskt giltig vid sid inläsning och ha önskad påverkan när användaren interagerar med fältet.

Överväg detta `DropDown` , vilket gör att användaren kan välja mellan flera olika databas typer:

```
{
    "name": "databaseType",
    "type": "Microsoft.Common.DropDown",
    "label": "Choose database type",
    "toolTip": "Choose database type",
    "defaultValue": "Oracle Database",
    "visible": "[bool(steps('section_database').connectToDatabase)]"
    "constraints": {
        "allowedValues": [
            {
                "label": "Azure Database for PostgreSQL",
                "value": "postgresql"
            },
            {
                "label": "Oracle Database",
                "value": "oracle"
            },
            {
                "label": "Azure SQL",
                "value": "sqlserver"
            }
        ],
        "required": true
    },
```

Om du vill ange ett annat fälts åtgärd på det aktuella valda värdet för det här fältet använder `coalesce()` du, som visas här:

```
"regex": "[concat('^jdbc:', coalesce(steps('section_database').databaseConnectionInfo.databaseType, ''), '.*$')]",
```

Detta är nödvändigt eftersom `databaseType` är inlednings vis inte visas och därför inte har något värde. Detta gör att hela uttrycket inte utvärderas korrekt.

## <a name="equals"></a>lika med

Returnerar `true` om båda parametrarna har samma typ och värde. Den här funktionen stöder alla JSON-datatyper.

Följande exempel returnerar `true` :

```json
"[equals(0, 0)]"
```

Följande exempel returnerar `true` :

```json
"[equals('web', 'web')]"
```

Följande exempel returnerar `false` :

```json
"[equals('abc', ['a', 'b', 'c'])]"
```

## <a name="greater"></a>större än

Returnerar `true` om den första parametern är exakt större än den andra parametern. Den här funktionen stöder endast parametrar av typen Number och String.

Följande exempel returnerar `false` :

```json
"[greater(1, 2)]"
```

Följande exempel returnerar `true` :

```json
"[greater('9', '10')]"
```

## <a name="greaterorequals"></a>större än eller lika med

Returnerar `true` om den första parametern är större än eller lika med den andra parametern. Den här funktionen stöder endast parametrar av typen Number och String.

Följande exempel returnerar `true` :

```json
"[greaterOrEquals(2, 2)]"
```

## <a name="if"></a>om

Returnerar ett värde baserat på om ett villkor är sant eller falskt. Den första parametern är det villkor som ska testas. Den andra parametern är det värde som ska returneras om villkoret är sant. Den tredje parametern är det värde som ska returneras om villkoret är falskt.

Följande exempel returnerar `yes` .

```json
"[if(equals(42, mul(6, 7)), 'yes', 'no')]"
```

## <a name="less"></a>mindre än

Returnerar `true` om den första parametern är strikt mindre än den andra parametern. Den här funktionen stöder endast parametrar av typen Number och String.

Följande exempel returnerar `true` :

```json
"[less(1, 2)]"
```

Följande exempel returnerar `false` :

```json
"[less('9', '10')]"
```

## <a name="lessorequals"></a>mindre än eller lika med

Returnerar `true` om den första parametern är mindre än eller lika med den andra parametern. Den här funktionen stöder endast parametrar av typen Number och String.

Följande exempel returnerar `true` :

```json
"[lessOrEquals(2, 2)]"
```

## <a name="not"></a>inte

Returnerar `true` om parametern utvärderas till `false` . Den här funktionen stöder endast parametrar av typen Boolean.

Följande exempel returnerar `true` :

```json
"[not(false)]"
```

Följande exempel returnerar `false` :

```json
"[not(equals(0, 0))]"
```

## <a name="or"></a>eller

Returnerar `true` om minst en av parametrarna utvärderas till `true` . Den här funktionen stöder minst två parametrar av typen Boolean.

Följande exempel returnerar `true` :

```json
"[or(equals(0, 0), equals('web', 'web'), less(1, 2))]"
```

Följande exempel returnerar `true` :

```json
"[or(equals(0, 0), greater(1, 2))]"
```

## <a name="next-steps"></a>Nästa steg

* En introduktion till Azure Resource Manager finns i [Azure Resource Manager översikt](../management/overview.md).
