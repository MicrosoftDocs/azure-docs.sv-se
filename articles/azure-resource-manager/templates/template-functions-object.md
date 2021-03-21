---
title: Mall funktioner – objekt
description: Beskriver de funktioner som används i en Azure Resource Manager mall (ARM-mall) för att arbeta med objekt.
ms.topic: conceptual
ms.date: 11/18/2020
ms.openlocfilehash: 5e13177db1a7cf2f19a822363cb3884474566add
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96920456"
---
# <a name="object-functions-for-arm-templates"></a>Objekt funktioner för ARM-mallar

Resource Manager innehåller flera funktioner för att arbeta med objekt i din Azure Resource Manager-mall (ARM-mall):

* [ingår](#contains)
* [createObject](#createobject)
* [tomt](#empty)
* [överlappning](#intersection)
* [utgör](#json)
* [length](#length)
* [null](#null)
* [Union](#union)

[!INCLUDE [Bicep preview](../../../includes/resource-manager-bicep-preview.md)]

## <a name="contains"></a>innehåller

`contains(container, itemToFind)`

Kontrollerar om en matris innehåller ett värde, ett objekt innehåller en nyckel eller en sträng innehåller en under sträng. Sträng jämförelsen är Skift läges känslig. Men när du testar om ett objekt innehåller en nyckel är jämförelsen Skift läges okänslig.

### <a name="parameters"></a>Parametrar

| Parameter | Krävs | Typ | Beskrivning |
|:--- |:--- |:--- |:--- |
| container |Ja |matris, objekt eller sträng |Värdet som innehåller värdet som ska hittas. |
| itemToFind |Ja |sträng eller heltal |Det värde som ska hittas. |

### <a name="return-value"></a>Returvärde

**Sant** om objektet hittas; annars **false**.

### <a name="example"></a>Exempel

I följande [exempel mall](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/contains.json) visas hur du använder med olika typer:

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "stringToTest": {
      "type": "string",
      "defaultValue": "OneTwoThree"
    },
    "objectToTest": {
      "type": "object",
      "defaultValue": {
        "one": "a",
        "two": "b",
        "three": "c"
      }
    },
    "arrayToTest": {
      "type": "array",
      "defaultValue": [ "one", "two", "three" ]
    }
  },
  "resources": [
  ],
  "outputs": {
    "stringTrue": {
      "type": "bool",
      "value": "[contains(parameters('stringToTest'), 'e')]"
    },
    "stringFalse": {
      "type": "bool",
      "value": "[contains(parameters('stringToTest'), 'z')]"
    },
    "objectTrue": {
      "type": "bool",
      "value": "[contains(parameters('objectToTest'), 'one')]"
    },
    "objectFalse": {
      "type": "bool",
      "value": "[contains(parameters('objectToTest'), 'a')]"
    },
    "arrayTrue": {
      "type": "bool",
      "value": "[contains(parameters('arrayToTest'), 'three')]"
    },
    "arrayFalse": {
      "type": "bool",
      "value": "[contains(parameters('arrayToTest'), 'four')]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param stringToTest string = 'OneTwoThree'
param objectToTest object = {
  'one': 'a'
  'two': 'b'
  'three': 'c'
}
param arrayToTest array = [
  'one'
  'two'
  'three'
]

output stringTrue bool = contains(stringToTest, 'e')
output stringFalse bool = contains(stringToTest, 'z')
output objectTrue bool = contains(objectToTest, 'one')
output objectFalse bool = contains(objectToTest, 'a')
output arrayTrue bool = contains(arrayToTest, 'three')
output arrayFalse bool = contains(arrayToTest, 'four')
```

---

Utdata från föregående exempel med standardvärdena är:

| Namn | Typ | Värde |
| ---- | ---- | ----- |
| stringTrue | Bool | Sant |
| stringFalse | Bool | Falskt |
| objectTrue | Bool | Sant |
| objectFalse | Bool | Falskt |
| arrayTrue | Bool | Sant |
| arrayFalse | Bool | Falskt |

## <a name="createobject"></a>createObject

`createObject(key1, value1, key2, value2, ...)`

Skapar ett objekt från nycklar och värden. `createObject`Funktionen stöds inte av bicep.  Skapa ett objekt med hjälp av `{}` .

### <a name="parameters"></a>Parametrar

| Parameter | Krävs | Typ | Beskrivning |
|:--- |:--- |:--- |:--- |
| key1 |Inga |sträng |Namnet på nyckeln. |
| value1 |Inga |int, boolesk, sträng, objekt eller matris |Värdet för nyckeln. |
| ytterligare nycklar |Inga |sträng |Ytterligare namn på nycklarna. |
| ytterligare värden |Inga |int, boolesk, sträng, objekt eller matris |Ytterligare värden för nycklarna. |

Funktionen accepterar bara ett jämnt antal parametrar. Varje nyckel måste ha ett matchande värde.

### <a name="return-value"></a>Returvärde

Ett objekt med varje nyckel och värde-par.

### <a name="example"></a>Exempel

I följande exempel skapas ett objekt av olika typer av värden.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "resources": [
  ],
  "outputs": {
    "newObject": {
      "type": "object",
      "value": "[createObject('intProp', 1, 'stringProp', 'abc', 'boolProp', true(), 'arrayProp', createArray('a', 'b', 'c'), 'objectProp', createObject('key1', 'value1'))]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
output newObject object = {
  'intProp': 1
  'stringProp': 'abc'
  'boolProp': true
  'arrayProp': [
    'a'
    'b'
    'c'
  ]
  'objectProp': {
    'key1': 'value1'
  }
}
```

---

Utdata från föregående exempel med standardvärdena är ett objekt med namnet `newObject` med följande värde:

```json
{
  "intProp": 1,
  "stringProp": "abc",
  "boolProp": true,
  "arrayProp": ["a", "b", "c"],
  "objectProp": {"key1": "value1"}
}
```

## <a name="empty"></a>tomt

`empty(itemToTest)`

Anger om en matris, ett objekt eller en sträng är tom.

### <a name="parameters"></a>Parametrar

| Parameter | Krävs | Typ | Beskrivning |
|:--- |:--- |:--- |:--- |
| itemToTest |Ja |matris, objekt eller sträng |Värdet för att kontrol lera om det är tomt. |

### <a name="return-value"></a>Returvärde

Returnerar **Sant** om värdet är tomt. annars **false**.

### <a name="example"></a>Exempel

Följande [exempel mal len](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/empty.json) kontrollerar om en matris, ett objekt och en sträng är tomma.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "testArray": {
      "type": "array",
      "defaultValue": []
    },
    "testObject": {
      "type": "object",
      "defaultValue": {}
    },
    "testString": {
      "type": "string",
      "defaultValue": ""
    }
  },
  "resources": [
  ],
  "outputs": {
    "arrayEmpty": {
      "type": "bool",
      "value": "[empty(parameters('testArray'))]"
    },
    "objectEmpty": {
      "type": "bool",
      "value": "[empty(parameters('testObject'))]"
    },
    "stringEmpty": {
      "type": "bool",
      "value": "[empty(parameters('testString'))]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param testArray array = []
param testObject object = {}
param testString string = ''

output arrayEmpty bool = empty(testArray)
output objectEmpty bool = empty(testObject)
output stringEmpty bool = empty(testString)
```

---

Utdata från föregående exempel med standardvärdena är:

| Namn | Typ | Värde |
| ---- | ---- | ----- |
| arrayEmpty | Bool | Sant |
| objectEmpty | Bool | Sant |
| stringEmpty | Bool | Sant |

## <a name="intersection"></a>överlappning

`intersection(arg1, arg2, arg3, ...)`

Returnerar en enskild matris eller ett objekt med de gemensamma elementen från parametrarna.

### <a name="parameters"></a>Parametrar

| Parameter | Krävs | Typ | Beskrivning |
|:--- |:--- |:--- |:--- |
| arg1 |Ja |matris eller objekt |Det första värdet som ska användas för att hitta vanliga element. |
| arg2 |Ja |matris eller objekt |Det andra värdet som ska användas för att hitta vanliga element. |
| ytterligare argument |Inga |matris eller objekt |Ytterligare värden som ska användas för att hitta vanliga element. |

### <a name="return-value"></a>Returvärde

En matris eller ett objekt med gemensamma element.

### <a name="example"></a>Exempel

I följande [exempel mall](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/intersection.json) visas hur du använder snitt med matriser och objekt:

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "firstObject": {
      "type": "object",
      "defaultValue": {
        "one": "a",
        "two": "b",
        "three": "c"
      }
    },
    "secondObject": {
      "type": "object",
      "defaultValue": {
        "one": "a",
        "two": "z",
        "three": "c"
      }
    },
    "firstArray": {
      "type": "array",
      "defaultValue": [ "one", "two", "three" ]
    },
    "secondArray": {
      "type": "array",
      "defaultValue": [ "two", "three" ]
    }
  },
  "resources": [
  ],
  "outputs": {
    "objectOutput": {
      "type": "object",
      "value": "[intersection(parameters('firstObject'), parameters('secondObject'))]"
    },
    "arrayOutput": {
      "type": "array",
      "value": "[intersection(parameters('firstArray'), parameters('secondArray'))]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param firstObject object = {
  'one': 'a'
  'two': 'b'
  'three': 'c'
}
param secondObject object = {
  'one': 'a'
  'two': 'z'
  'three': 'c'
}
param firstArray array = [
  'one'
  'two'
  'three'
]
param secondArray array = [
  'two'
  'three'
]

output objectOutput object = intersection(firstObject, secondObject)
output arrayOutput array = intersection(firstArray, secondArray)
```

---

Utdata från föregående exempel med standardvärdena är:

| Namn | Typ | Värde |
| ---- | ---- | ----- |
| objectOutput | Objekt | {"One": "a", "tre": "c"} |
| arrayOutput | Matris | ["två", "tre"] |

<a id="json"></a>

## <a name="json"></a>json

`json(arg1)`

Konverterar en giltig JSON-sträng till en JSON-datatyp.

### <a name="parameters"></a>Parametrar

| Parameter | Krävs | Typ | Beskrivning |
|:--- |:--- |:--- |:--- |
| arg1 |Ja |sträng |Värdet som ska konverteras till JSON. Strängen måste vara en korrekt formaterad JSON-sträng. |

### <a name="return-value"></a>Returvärde

Data typen JSON från den angivna strängen, eller ett tomt värde när **Null** har angetts.

### <a name="remarks"></a>Kommentarer

Om du behöver inkludera ett parameter värde eller en variabel i JSON-objektet använder du funktionen [concat](template-functions-string.md#concat) för att skapa den sträng som du skickar till funktionen.

Du kan också använda [Null ()](#null) för att få ett null-värde.

### <a name="example"></a>Exempel

I följande [exempel mall](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/json.json) visas hur du använder JSON-funktionen. Observera att du kan skicka in **Null** för ett tomt objekt.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "jsonEmptyObject": {
      "type": "string",
      "defaultValue": "null"
    },
    "jsonObject": {
      "type": "string",
      "defaultValue": "{\"a\": \"b\"}"
    },
    "jsonString": {
      "type": "string",
      "defaultValue": "\"test\""
    },
    "jsonBoolean": {
      "type": "string",
      "defaultValue": "true"
    },
    "jsonInt": {
      "type": "string",
      "defaultValue": "3"
    },
    "jsonArray": {
      "type": "string",
      "defaultValue": "[[1,2,3 ]"
    },
    "concatValue": {
      "type": "string",
      "defaultValue": "demo value"
    }
  },
  "resources": [
  ],
  "outputs": {
    "emptyObjectOutput": {
      "type": "bool",
      "value": "[empty(json(parameters('jsonEmptyObject')))]"
    },
    "objectOutput": {
      "type": "object",
      "value": "[json(parameters('jsonObject'))]"
    },
    "stringOutput": {
      "type": "string",
      "value": "[json(parameters('jsonString'))]"
    },
    "booleanOutput": {
      "type": "bool",
      "value": "[json(parameters('jsonBoolean'))]"
    },
    "intOutput": {
      "type": "int",
      "value": "[json(parameters('jsonInt'))]"
    },
    "arrayOutput": {
      "type": "array",
      "value": "[json(parameters('jsonArray'))]"
    },
    "concatObjectOutput": {
      "type": "object",
      "value": "[json(concat('{\"a\": \"', parameters('concatValue'), '\"}'))]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param jsonEmptyObject string = 'null'
param jsonObject string = '{\'a\': \'b\'}'
param jsonString string = '\'test\''
param jsonBoolean string = 'true'
param jsonInt string = '3'
param jsonArray string = '[[1,2,3]]'
param concatValue string = 'demo value'

output emptyObjectOutput bool = empty(json(jsonEmptyObject))
output objectOutput object = json(jsonObject)
output stringOutput string =json(jsonString)
output booleanOutput bool = json(jsonBoolean)
output intOutput int = json(jsonInt)
output arrayOutput array = json(jsonArray)
output concatObjectOutput object = json(concat('{"a": "', concatValue, '"}'))
```

---

Utdata från föregående exempel med standardvärdena är:

| Namn | Typ | Värde |
| ---- | ---- | ----- |
| emptyObjectOutput | Boolesk | Sant |
| objectOutput | Objekt | {"a": "b"} |
| stringOutput | Sträng | test |
| booleanOutput | Boolesk | Sant |
| intOutput | Integer | 3 |
| arrayOutput | Matris | [1, 2, 3] |
| concatObjectOutput | Objekt | {"a": "demo värde"} |

## <a name="length"></a>length

`length(arg1)`

Returnerar antalet element i en matris, tecken i en sträng eller på rot nivå egenskaper i ett objekt.

### <a name="parameters"></a>Parametrar

| Parameter | Krävs | Typ | Beskrivning |
|:--- |:--- |:--- |:--- |
| arg1 |Ja |matris, sträng eller objekt |Den matris som ska användas för att hämta antalet element, strängen som ska användas för att hämta antalet tecken, eller objektet som ska användas för att hämta antalet rot nivå egenskaper. |

### <a name="return-value"></a>Returvärde

En int.

### <a name="example"></a>Exempel

I följande [exempel mall](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/length.json) visas hur du använder length med en matris och en sträng:

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "arrayToTest": {
      "type": "array",
      "defaultValue": [
        "one",
        "two",
        "three"
      ]
    },
    "stringToTest": {
      "type": "string",
      "defaultValue": "One Two Three"
    },
    "objectToTest": {
      "type": "object",
      "defaultValue": {
        "propA": "one",
        "propB": "two",
        "propC": "three",
        "propD": {
          "propD-1": "sub",
          "propD-2": "sub"
        }
      }
    }
  },
  "resources": [],
  "outputs": {
    "arrayLength": {
      "type": "int",
      "value": "[length(parameters('arrayToTest'))]"
    },
    "stringLength": {
      "type": "int",
      "value": "[length(parameters('stringToTest'))]"
    },
    "objectLength": {
      "type": "int",
      "value": "[length(parameters('objectToTest'))]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param arrayToTest array = [
  'one'
  'two'
  'three'
]
param stringToTest string = 'One Two Three'
param objectToTest object = {
  'propA': 'one'
  'propB': 'two'
  'propC': 'three'
  'propD': {
      'propD-1': 'sub'
      'propD-2': 'sub'
  }
}

output arrayLength int = length(arrayToTest)
output stringLength int = length(stringToTest)
output objectLength int = length(objectToTest)
```

---

Utdata från föregående exempel med standardvärdena är:

| Namn | Typ | Värde |
| ---- | ---- | ----- |
| arrayLength | Int | 3 |
| stringLength | Int | 13 |
| objectLength | Int | 4 |

## <a name="null"></a>null

`null()`

Returnerar null. `null`Funktionen är inte tillgänglig i bicep. Använd `null` nyckelordet i stället.

### <a name="parameters"></a>Parametrar

Funktionen null accepterar inte några parametrar.

### <a name="return-value"></a>Returvärde

Ett värde som alltid är null.

### <a name="example"></a>Exempel

I följande exempel används funktionen null.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "resources": [],
  "outputs": {
    "emptyOutput": {
      "type": "bool",
      "value": "[empty(null())]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
output emptyOutput bool = empty(null)
```

---

Utdata från föregående exempel är:

| Namn | Typ | Värde |
| ---- | ---- | ----- |
| emptyOutput | Bool | Sant |

## <a name="union"></a>Union

`union(arg1, arg2, arg3, ...)`

Returnerar en enskild matris eller ett objekt med alla element från parametrarna. Duplicerade värden eller nycklar ingår bara en gång.

### <a name="parameters"></a>Parametrar

| Parameter | Krävs | Typ | Beskrivning |
|:--- |:--- |:--- |:--- |
| arg1 |Ja |matris eller objekt |Det första värdet som ska användas för att koppla ihop element. |
| arg2 |Ja |matris eller objekt |Det andra värdet som ska användas för att koppla ihop element. |
| ytterligare argument |Inga |matris eller objekt |Ytterligare värden som ska användas för att koppla ihop element. |

### <a name="return-value"></a>Returvärde

En matris eller ett objekt.

### <a name="example"></a>Exempel

I följande [exempel mall](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/union.json) visas hur du använder union med matriser och objekt:

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "firstObject": {
      "type": "object",
      "defaultValue": {
        "one": "a",
        "two": "b",
        "three": "c1"
      }
    },
    "secondObject": {
      "type": "object",
      "defaultValue": {
        "three": "c2",
        "four": "d",
        "five": "e"
      }
    },
    "firstArray": {
      "type": "array",
      "defaultValue": [ "one", "two", "three" ]
    },
    "secondArray": {
      "type": "array",
      "defaultValue": [ "three", "four" ]
    }
  },
  "resources": [
  ],
  "outputs": {
    "objectOutput": {
      "type": "object",
      "value": "[union(parameters('firstObject'), parameters('secondObject'))]"
    },
    "arrayOutput": {
      "type": "array",
      "value": "[union(parameters('firstArray'), parameters('secondArray'))]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param firstObject object = {
  'one': 'a'
  'two': 'b'
  'three': 'c1'
}

param secondObject object = {
  'three': 'c2'
  'four': 'd'
  'five': 'e'
}

param firstArray array = [
  'one'
  'two'
  'three'
]

param secondArray array = [
  'three'
  'four'
]

output objectOutput object = union(firstObject, secondObject)
output arrayOutput array = union(firstArray, secondArray)
```

---

Utdata från föregående exempel med standardvärdena är:

| Namn | Typ | Värde |
| ---- | ---- | ----- |
| objectOutput | Objekt | {"One": "a", "två": "b", "tre": "C2", "fyra": "d", "fem": "e"} |
| arrayOutput | Matris | ["One", "två", "tre", "fyra"] |

## <a name="next-steps"></a>Nästa steg

* En beskrivning av avsnitten i en ARM-mall finns i [förstå strukturen och syntaxen för ARM-mallar](template-syntax.md).
