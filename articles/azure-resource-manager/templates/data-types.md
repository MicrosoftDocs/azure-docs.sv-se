---
title: Datatyper i mallar
description: Beskriver de datatyper som är tillgängliga i Azure Resource Manager mallar.
ms.topic: conceptual
ms.author: tomfitz
author: tfitzmac
ms.date: 03/04/2021
ms.openlocfilehash: 4d6c8306b3dbdfe895055dc008d81cc0d85d8d6c
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2021
ms.locfileid: "107538068"
---
# <a name="data-types-in-arm-templates"></a>Datatyper i ARM-mallar

I den här artikeln beskrivs de datatyper som stöds i Azure Resource Manager (ARM-mallar). Den omfattar både JSON- och Bicep-datatyper.

## <a name="supported-types"></a>Typer som stöds

I en ARM-mall kan du använda följande datatyper:

* matris
* boolesk
* int
* objekt
* secureObject – anges av modifieraren i Bicep
* secureString – anges av modifieraren i Bicep
* sträng

## <a name="arrays"></a>Matriser

Matriser börjar med en vänster hakparentes ( `[` ) och slutar med en höger hakparentes ( `]` ).

I JSON kan en matris deklareras på en enda rad eller flera rader. Varje element avgränsas med ett kommatecken.

I Bicep måste en matris deklareras i flera rader. Använd inte kommatecken mellan värden.

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

När du anger booleska värden använder `true` du eller `false` . Omge inte värdet med citattecken.

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

Använd inte citattecken när du anger heltalsvärden.

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

För heltal som skickas som infogade parametrar kan intervallet med värden begränsas av SDK:n eller kommandoradsverktyget som du använder för distribution. När du till exempel använder PowerShell för att distribuera en mall kan heltalstyperna vara mellan -2147483648 och 2147483647. För att undvika den här begränsningen anger du stora heltalsvärden i [en parameterfil](parameter-files.md). Resurstyper tillämpar sina egna gränser för heltalsegenskaper.

## <a name="objects"></a>Objekt

Objekt börjar med en vänsterparentes ( `{` ) och slutar med en högerparentes ( `}` ). Varje egenskap i ett objekt består av nyckel och värde. Nyckeln och värdet avgränsas med ett kolon ( `:` ).

# <a name="json"></a>[JSON](#tab/json)

I JSON omges nyckeln av dubbla citattecken. Varje egenskap avgränsas med ett kommatecken.

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

I Bicep omges inte nyckeln av citattecken. Använd inte kommatecken mellan egenskaper.

```bicep
param exampleObject object = {
  name: 'test name'
  id: '123-abc'
  isCurrent: true
  tier: 1
}
```

Egenskapsåtkomst används för att komma åt egenskaper för ett objekt. De konstrueras med `.` operatorn . Exempel:

```bicep
var x = {
  y: {
    z: 'Hello`
    a: true
  }
  q: 42
}
```

I den föregående deklarationen utvärderas uttrycket x.y.z till literalsträngen "Hello". På samma sätt utvärderas uttrycket x.q till heltalslitteralen 42.

Egenskapsåtkomster kan användas med alla objekt. Detta inkluderar parametrar och variabler för objekttyper och objektlitteraler. Att använda en egenskapsåtkomstor för ett uttryck av icke-objekttyp är ett fel.

---

## <a name="strings"></a>Strängar

I JSON markeras strängar med dubbla citattecken. I Bicep markeras strängar med enkla citattecken.

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

Säker sträng använder samma format som sträng, och skyddat objekt använder samma format som -objektet. När du anger en parameter till en säker sträng eller ett säkert objekt sparas inte värdet för parametern i distributionshistoriken och loggas inte. Men om du anger det säkra värdet till en egenskap som inte förväntar sig ett säkert värde skyddas inte värdet. Om du till exempel anger en säker sträng till en tagg lagras det värdet som oformaterad text. Använd säkra strängar för lösenord och hemligheter.

Med Bicep lägger du till `@secure()` modifieraren i en sträng eller ett objekt.

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

Mer information om mallsyntaxen finns i [Förstå strukturen och syntaxen för ARM-mallar.](template-syntax.md)
