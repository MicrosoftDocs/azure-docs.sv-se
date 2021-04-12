---
title: Mall-funktioner – jämförelse
description: Beskriver de funktioner som används i en Azure Resource Manager-mall (ARM-mall) för att jämföra värden.
ms.topic: conceptual
ms.date: 11/18/2020
ms.openlocfilehash: 95655a4c92a1de9bb7a7faebcdaa83fb0fa75696
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "99834008"
---
# <a name="comparison-functions-for-arm-templates"></a>Jämförelse funktioner för ARM-mallar

Resource Manager innehåller flera funktioner för att göra jämförelser i din Azure Resource Manager-mall (ARM-mall):

* [coalesce](#coalesce)
* [är lika med](#equals)
* [större än](#greater)
* [större än eller lika med](#greaterorequals)
* [minskad](#less)
* [mindre än eller lika med](#lessorequals)

[!INCLUDE [Bicep preview](../../../includes/resource-manager-bicep-preview.md)]

## <a name="coalesce"></a>coalesce

`coalesce(arg1, arg2, arg3, ...)`

Returnerar det första värdet som inte är null från parametrarna. Tomma strängar, tomma matriser och tomma objekt är inte null.

### <a name="parameters"></a>Parametrar

| Parameter | Krävs | Typ | Beskrivning |
|:--- |:--- |:--- |:--- |
| arg1 |Ja |heltal, sträng, matris eller objekt |Det första värdet som ska testas för null. |
| ytterligare argument |Inga |heltal, sträng, matris eller objekt |Ytterligare värden att testa för null. |

### <a name="return-value"></a>Returvärde

Värdet för de första icke-null-parametrarna, som kan vara en sträng, en heltals mat ris eller ett objekt. Null om alla parametrar är null.

### <a name="example"></a>Exempel

I följande [exempel mall](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/coalesce.json) visas utdata från olika användnings områden för sammanslagning.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "objectToTest": {
      "type": "object",
      "defaultValue": {
        "null1": null,
        "null2": null,
        "string": "default",
        "int": 1,
        "object": { "first": "default" },
        "array": [ 1 ]
      }
    }
  },
  "resources": [
  ],
  "outputs": {
    "stringOutput": {
      "type": "string",
      "value": "[coalesce(parameters('objectToTest').null1, parameters('objectToTest').null2, parameters('objectToTest').string)]"
    },
    "intOutput": {
      "type": "int",
      "value": "[coalesce(parameters('objectToTest').null1, parameters('objectToTest').null2, parameters('objectToTest').int)]"
    },
    "objectOutput": {
      "type": "object",
      "value": "[coalesce(parameters('objectToTest').null1, parameters('objectToTest').null2, parameters('objectToTest').object)]"
    },
    "arrayOutput": {
      "type": "array",
      "value": "[coalesce(parameters('objectToTest').null1, parameters('objectToTest').null2, parameters('objectToTest').array)]"
    },
    "emptyOutput": {
      "type": "bool",
      "value": "[empty(coalesce(parameters('objectToTest').null1, parameters('objectToTest').null2))]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param objectToTest object = {
  'null1': null
  'null2': null
  'string': 'default'
  'int': 1
  'object': {
    'first': 'default'
  }
  'array': [
    1
  ]
}

output stringOutput string = objectToTest.null1 ?? objectToTest.null2 ?? objectToTest.string
output intOutput int = objectToTest.null1 ?? objectToTest.null2 ?? objectToTest.int
output objectOutput object = objectToTest.null1 ?? objectToTest.null2 ?? objectToTest.object
output arrayOutput array = objectToTest.null1 ?? objectToTest.null2 ?? objectToTest.array
output emptyOutput bool =empty(objectToTest.null1 ?? objectToTest.null2)
```

---

Utdata från föregående exempel med standardvärdena är:

| Namn | Typ | Värde |
| ---- | ---- | ----- |
| stringOutput | Sträng | standard |
| intOutput | Int | 1 |
| objectOutput | Objekt | {"First": "standard"} |
| arrayOutput | Matris |  [1] |
| emptyOutput | Bool | Sant |

## <a name="equals"></a>lika med

`equals(arg1, arg2)`

Kontrollerar om två värden är lika med varandra. `equals`Funktionen stöds inte i bicep. Använd `==` operatorn i stället.

### <a name="parameters"></a>Parametrar

| Parameter | Krävs | Typ | Beskrivning |
|:--- |:--- |:--- |:--- |
| arg1 |Ja |heltal, sträng, matris eller objekt |Det första värdet för att kontrol lera om det är lika. |
| arg2 |Ja |heltal, sträng, matris eller objekt |Det andra värdet för att kontrol lera om det är lika. |

### <a name="return-value"></a>Returvärde

Returnerar **Sant** om värdena är lika. annars **false**.

### <a name="remarks"></a>Kommentarer

Funktionen Equals används ofta tillsammans med `condition` elementet för att testa om en resurs har distribuerats.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "condition": "[equals(parameters('newOrExisting'),'new')]",
  "type": "Microsoft.Storage/storageAccounts",
  "name": "[variables('storageAccountName')]",
  "apiVersion": "2017-06-01",
  "location": "[resourceGroup().location]",
  "sku": {
    "name": "[variables('storageAccountType')]"
  },
  "kind": "Storage",
  "properties": {}
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

> [!NOTE]
> `Conditions` har ännu inte implementerats i bicep. Se [villkor](https://github.com/Azure/bicep/issues/186).

---

### <a name="example"></a>Exempel

I följande [exempel mall](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/equals.json) kontrol leras olika typer av värden för likhet. Alla standardvärden returnerar true.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "firstInt": {
      "type": "int",
      "defaultValue": 1
    },
    "secondInt": {
      "type": "int",
      "defaultValue": 1
    },
    "firstString": {
      "type": "string",
      "defaultValue": "a"
    },
    "secondString": {
      "type": "string",
      "defaultValue": "a"
    },
    "firstArray": {
      "type": "array",
      "defaultValue": [ "a", "b" ]
    },
    "secondArray": {
      "type": "array",
      "defaultValue": [ "a", "b" ]
    },
    "firstObject": {
      "type": "object",
      "defaultValue": { "a": "b" }
    },
    "secondObject": {
      "type": "object",
      "defaultValue": { "a": "b" }
    }
  },
  "resources": [
  ],
  "outputs": {
    "checkInts": {
      "type": "bool",
      "value": "[equals(parameters('firstInt'), parameters('secondInt') )]"
    },
    "checkStrings": {
      "type": "bool",
      "value": "[equals(parameters('firstString'), parameters('secondString'))]"
    },
    "checkArrays": {
      "type": "bool",
      "value": "[equals(parameters('firstArray'), parameters('secondArray'))]"
    },
    "checkObjects": {
      "type": "bool",
      "value": "[equals(parameters('firstObject'), parameters('secondObject'))]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param firstInt int = 1
param secondInt int = 1
param firstString string = 'a'
param secondString string = 'a'
param firstArray array = [
  'a'
  'b'
]
param secondArray array = [
  'a'
  'b'
]
param firstObject object = {
  'a': 'b'
}
param secondObject object = {
  'a': 'b'
}

output checInts bool = firstInt == secondInt
output checkStrings bool = firstString == secondString
output checkArrays bool = firstArray == secondArray
output checkObjects bool = firstObject == secondObject
```

---

Utdata från föregående exempel med standardvärdena är:

| Namn | Typ | Värde |
| ---- | ---- | ----- |
| Incheckningar | Bool | Sant |
| checkStrings | Bool | Sant |
| checkArrays | Bool | Sant |
| checkObjects | Bool | Sant |

Följande [exempel-mall](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/not-equals.json) använder [inte](template-functions-logical.md#not) **lika** med.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "resources": [
  ],
  "outputs": {
    "checkNotEquals": {
      "type": "bool",
      "value": "[not(equals(1, 2))]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
output checkNotEquals bool = ! (1 == 2)
```

---

Utdata från föregående exempel är:

| Namn | Typ | Värde |
| ---- | ---- | ----- |
| checkNotEquals | Bool | Sant |

## <a name="greater"></a>större än

`greater(arg1, arg2)`

Kontrollerar om det första värdet är större än det andra värdet. `greater`Funktionen stöds inte i bicep. Använd `>` operatorn i stället.

### <a name="parameters"></a>Parametrar

| Parameter | Krävs | Typ | Beskrivning |
|:--- |:--- |:--- |:--- |
| arg1 |Ja |int eller string |Det första värdet för större jämförelse. |
| arg2 |Ja |int eller string |Det andra värdet för större jämförelse. |

### <a name="return-value"></a>Returvärde

Returnerar **True** om det första värdet är större än det andra värdet. annars **false**.

### <a name="example"></a>Exempel

Följande [exempel mal len](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/greater.json) kontrollerar om det ena värdet är större än det andra.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "firstInt": {
      "type": "int",
      "defaultValue": 1
    },
    "secondInt": {
      "type": "int",
      "defaultValue": 2
    },
    "firstString": {
      "type": "string",
      "defaultValue": "A"
    },
    "secondString": {
      "type": "string",
      "defaultValue": "a"
    }
  },
  "resources": [
  ],
  "outputs": {
    "checkInts": {
      "type": "bool",
      "value": "[greater(parameters('firstInt'), parameters('secondInt') )]"
    },
    "checkStrings": {
      "type": "bool",
      "value": "[greater(parameters('firstString'), parameters('secondString'))]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param firstInt int = 1
param secondInt int = 2
param firstString string = 'A'
param secondString string = 'a'

output checkInts bool = firstInt > secondInt
output checkStrings bool = firstString > secondString
```

---

Utdata från föregående exempel med standardvärdena är:

| Namn | Typ | Värde |
| ---- | ---- | ----- |
| Incheckningar | Bool | Falskt |
| checkStrings | Bool | Sant |

## <a name="greaterorequals"></a>större än eller lika med

`greaterOrEquals(arg1, arg2)`

Kontrollerar om det första värdet är större än eller lika med det andra värdet. `greaterOrEquals`Funktionen stöds inte i bicep. Använd `>=` operatorn i stället.

### <a name="parameters"></a>Parametrar

| Parameter | Krävs | Typ | Beskrivning |
|:--- |:--- |:--- |:--- |
| arg1 |Ja |int eller string |Det första värdet för större eller lika jämförelse. |
| arg2 |Ja |int eller string |Det andra värdet för större eller lika jämförelse. |

### <a name="return-value"></a>Returvärde

Returnerar **True** om det första värdet är större än eller lika med det andra värdet. annars **false**.

### <a name="example"></a>Exempel

Följande [exempel mal len](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/greaterorequals.json) kontrollerar om det ena värdet är större än eller lika med det andra.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "firstInt": {
      "type": "int",
      "defaultValue": 1
    },
    "secondInt": {
      "type": "int",
      "defaultValue": 2
    },
    "firstString": {
      "type": "string",
      "defaultValue": "A"
    },
    "secondString": {
      "type": "string",
      "defaultValue": "a"
    }
  },
  "resources": [
  ],
  "outputs": {
    "checkInts": {
      "type": "bool",
      "value": "[greaterOrEquals(parameters('firstInt'), parameters('secondInt') )]"
    },
    "checkStrings": {
      "type": "bool",
      "value": "[greaterOrEquals(parameters('firstString'), parameters('secondString'))]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param firstInt int = 1
param secondInt int = 2
param firstString string = 'A'
param secondString string = 'a'

output checkInts bool = firstInt >= secondInt
output checkStrings bool = firstString >= secondString
```

---

Utdata från föregående exempel med standardvärdena är:

| Namn | Typ | Värde |
| ---- | ---- | ----- |
| Incheckningar | Bool | Falskt |
| checkStrings | Bool | Sant |

## <a name="less"></a>mindre än

`less(arg1, arg2)`

Kontrollerar om det första värdet är mindre än det andra värdet. `less`Funktionen stöds inte i bicep. Använd `<` operatorn i stället.

### <a name="parameters"></a>Parametrar

| Parameter | Krävs | Typ | Beskrivning |
|:--- |:--- |:--- |:--- |
| arg1 |Ja |int eller string |Det första värdet för mindre jämförelse. |
| arg2 |Ja |int eller string |Det andra värdet för mindre jämförelse. |

### <a name="return-value"></a>Returvärde

Returnerar **True** om det första värdet är mindre än det andra värdet. annars **false**.

### <a name="example"></a>Exempel

Följande [exempel mal len](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/less.json) kontrollerar om det ena värdet är mindre än det andra.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "firstInt": {
      "type": "int",
      "defaultValue": 1
    },
    "secondInt": {
      "type": "int",
      "defaultValue": 2
    },
    "firstString": {
      "type": "string",
      "defaultValue": "A"
    },
    "secondString": {
      "type": "string",
      "defaultValue": "a"
    }
  },
  "resources": [
  ],
  "outputs": {
    "checkInts": {
      "type": "bool",
      "value": "[less(parameters('firstInt'), parameters('secondInt') )]"
    },
    "checkStrings": {
      "type": "bool",
      "value": "[less(parameters('firstString'), parameters('secondString'))]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param firstInt int = 1
param secondInt int = 2
param firstString string = 'A'
param secondString string = 'a'

output checkInts bool = firstInt < secondInt
output checkStrings bool = firstString < secondString
```

---

Utdata från föregående exempel med standardvärdena är:

| Namn | Typ | Värde |
| ---- | ---- | ----- |
| Incheckningar | Bool | Sant |
| checkStrings | Bool | Falskt |

## <a name="lessorequals"></a>mindre än eller lika med

`lessOrEquals(arg1, arg2)`

Kontrollerar om det första värdet är mindre än eller lika med det andra värdet. `lessOrEquals`Funktionen stöds inte i bicep. Använd `<=` operatorn i stället.

### <a name="parameters"></a>Parametrar

| Parameter | Krävs | Typ | Beskrivning |
|:--- |:--- |:--- |:--- |
| arg1 |Ja |int eller string |Det första värdet för mindre eller lika med jämförelse. |
| arg2 |Ja |int eller string |Det andra värdet för jämförelsen som är mindre eller lika med. |

### <a name="return-value"></a>Returvärde

Returnerar **True** om det första värdet är mindre än eller lika med det andra värdet. annars **false**.

### <a name="example"></a>Exempel

Följande [exempel mal len](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/lessorequals.json) kontrollerar om det ena värdet är mindre än eller lika med det andra.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "firstInt": {
      "type": "int",
      "defaultValue": 1
    },
    "secondInt": {
      "type": "int",
      "defaultValue": 2
    },
    "firstString": {
      "type": "string",
      "defaultValue": "A"
    },
    "secondString": {
      "type": "string",
      "defaultValue": "a"
    }
  },
  "resources": [
  ],
  "outputs": {
    "checkInts": {
      "type": "bool",
      "value": "[lessOrEquals(parameters('firstInt'), parameters('secondInt') )]"
    },
    "checkStrings": {
      "type": "bool",
      "value": "[lessOrEquals(parameters('firstString'), parameters('secondString'))]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param firstInt int = 1
param secondInt int = 2
param firstString string = 'A'
param secondString string = 'a'

output checkInts bool = firstInt <= secondInt
output checkStrings bool = firstString <= secondString
```

---

Utdata från föregående exempel med standardvärdena är:

| Namn | Typ | Värde |
| ---- | ---- | ----- |
| Incheckningar | Bool | Sant |
| checkStrings | Bool | Falskt |

## <a name="next-steps"></a>Nästa steg

* En beskrivning av avsnitten i en ARM-mall finns i [förstå strukturen och syntaxen för ARM-mallar](template-syntax.md).
