---
title: Data typer i mallar
description: Beskriver de data typer som är tillgängliga i Azure Resource Manager mallar.
ms.topic: conceptual
ms.author: tomfitz
author: tfitzmac
ms.date: 03/04/2021
ms.openlocfilehash: 7d3f15c8852e6e25c621baad9bc6f20c303ffdb9
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102125165"
---
# <a name="data-types-in-arm-templates"></a>Data typer i ARM-mallar

Den här artikeln beskriver de data typer som stöds i Azure Resource Manager mallar (ARM-mallar). Den täcker både JSON-och bicep-datatyper.

## <a name="supported-types"></a>Typer som stöds

I en ARM-mall kan du använda dessa data typer:

* matris
* boolesk
* int
* objekt
* secureObject – anges av modifieraren i bicep
* secureString – anges av modifieraren i bicep
* sträng

## <a name="arrays"></a>Matriser

Matriser börjar med en vänsterparentes ( `[` ) och slutar med en höger hak paren tes ( `]` ).

I JSON kan en matris deklareras i en enskild rad eller flera rader. Varje element avgränsas med ett kommatecken.

I bicep måste en matris deklareras i flera rader. Använd inte kommatecken mellan värden.

# <a name="json"></a>[JSON](#tab/json)

```json
"parameters": {
  "exampleArray": {
    "type": "array",
    "defaultValue": [
      1,
      2,
      3
    ]
  }
},
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param exampleArray array = [
  1
  2
  3
]
```

---

Elementen i en matris kan vara av samma typ eller olika typer.

# <a name="json"></a>[JSON](#tab/json)

```json
"variables": {
  "mixedArray": [
    "[resourceGroup().name]",
    1,
    true,
    "example string"
  ]
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
var mixedArray = [
  resourceGroup().name
  1
  true
  'example string'
]
```

---

## <a name="booleans"></a>Booleska värden

När du anger booleska värden använder `true` eller `false` . Omge inte värdet med citat tecken.

# <a name="json"></a>[JSON](#tab/json)

```json
"parameters": {
  "exampleBool": {
    "type": "bool",
    "defaultValue": true
  }
},
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param exampleBool bool = true
```

---

## <a name="integers"></a>Heltal

Använd inte citat tecken när du anger heltals värden.

# <a name="json"></a>[JSON](#tab/json)

```json
"parameters": {
  "exampleInt": {
    "type": "int",
    "defaultValue": 1
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param exampleInt int = 1
```

---

För heltal som skickas som infogade parametrar kan värde intervallet begränsas av SDK eller kommando rads verktyget som du använder för distribution. När du till exempel använder PowerShell för att distribuera en mall kan heltals typer vara mellan-2147483648 och 2147483647. Undvik den här begränsningen genom att ange stora heltals värden i en [parameter fil](parameter-files.md). Resurs typer tillämpar egna gränser för heltals egenskaper.

## <a name="objects"></a>Objekt

Objekt börjar med en vänster klammerparentes ( `{` ) och slutar med en höger klammerparentes ( `}` ). Varje egenskap i ett objekt består av nyckel och värde. Nyckeln och värdet skiljs åt av kolon ( `:` ).

I JSON är nyckeln omgiven av dubbla citat tecken. Varje egenskap avgränsas med ett kommatecken.

I bicep är nyckeln inte omgiven av citat tecken. Använd inte kommatecken i mellan egenskaper.

# <a name="json"></a>[JSON](#tab/json)

```json
"parameters": {
  "exampleObject": {
    "type": "object",
    "defaultValue": {
      "name": "test name",
      "id": "123-abc",
      "isCurrent": true,
      "tier": 1
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param exampleObject object = {
  name: 'test name'
  id: '123-abc'
  isCurrent: true
  tier: 1
}
```

---

## <a name="strings"></a>Strängar

I JSON är strängarna markerade med dubbla citat tecken. I bicep är strängarna markerade med enkla citat tecken.

# <a name="json"></a>[JSON](#tab/json)

```json
"parameters": {
  "exampleString": {
    "type": "string",
    "defaultValue": "test value"
  }
},
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param exampleString string = 'test value'
```
---

## <a name="secure-strings-and-objects"></a>Säkra strängar och objekt

En säker sträng använder samma format som sträng, och det skyddade objektet använder samma format som objektet. När du anger en parameter till en säker sträng eller ett säkert objekt, sparas inte värdet för parametern i distributions historiken och loggas inte. Men om du anger ett säkert värde till en egenskap som inte förväntar ett säkert värde, skyddas inte värdet. Om du till exempel anger en säker sträng till en tagg lagras värdet som oformaterad text. Använd säkra strängar för lösen ord och hemligheter.

Med bicep lägger du till `@secure()` modifieraren i en sträng eller ett objekt.

I följande exempel visas två säkra parametrar:

# <a name="json"></a>[JSON](#tab/json)

```json
"parameters": {
  "password": {
    "type": "secureString"
  },
  "configValues": {
    "type": "secureObject"
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
@secure()
param password string

@secure()
param configValues object
```

---

## <a name="next-steps"></a>Nästa steg

Läs mer om mallens syntax i [förstå strukturen och syntaxen för ARM-mallar](template-syntax.md).
