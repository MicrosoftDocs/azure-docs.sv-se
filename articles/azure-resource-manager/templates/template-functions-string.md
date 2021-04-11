---
title: Template Functions-String
description: Beskriver de funktioner som används i en Azure Resource Manager mall (ARM-mall) för att arbeta med strängar.
ms.topic: conceptual
ms.date: 03/02/2021
ms.openlocfilehash: cff1424562b45bc722f87fa3ec896c1c641ee758
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "105108850"
---
# <a name="string-functions-for-arm-templates"></a>Sträng funktioner för ARM-mallar

Resource Manager innehåller följande funktioner för att arbeta med strängar i din Azure Resource Manager-mall (ARM-mall):

* [base64](#base64)
* [base64ToJson](#base64tojson)
* [base64ToString](#base64tostring)
* [concat](#concat)
* [ingår](#contains)
* [dataUri](#datauri)
* [dataUriToString](#datauritostring)
* [tomt](#empty)
* [endsWith](#endswith)
* [förstagångskörningen](#first)
* [formatering](#format)
* [guid](#guid)
* [indexOf](#indexof)
* [utgör](#json)
* [pågå](#last)
* [lastIndexOf](#lastindexof)
* [length](#length)
* [newGuid](#newguid)
* [padLeft](#padleft)
* [bytt](#replace)
* [Ignorera](#skip)
* [del](#split)
* [startsWith](#startswith)
* [sträng](#string)
* [under sträng](#substring)
* [gå](#take)
* [toLower](#tolower)
* [toUpper](#toupper)
* [reducera](#trim)
* [uniqueString](#uniquestring)
* [URI](#uri)
* [uriComponent](#uricomponent)
* [uriComponentToString](#uricomponenttostring)

[!INCLUDE [Bicep preview](../../../includes/resource-manager-bicep-preview.md)]

## <a name="base64"></a>base64

`base64(inputString)`

Returnerar den base64-representation av Indatasträngen.

### <a name="parameters"></a>Parametrar

| Parameter | Krävs | Typ | Beskrivning |
|:--- |:--- |:--- |:--- |
| inputString |Ja |sträng |Värdet som ska returneras som en Base64-representation. |

### <a name="return-value"></a>Returvärde

En sträng som innehåller base64-representation.

### <a name="examples"></a>Exempel

I följande [exempel mall](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/base64.json) visas hur du använder base64-funktionen.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "stringData": {
      "type": "string",
      "defaultValue": "one, two, three"
    },
    "jsonFormattedData": {
      "type": "string",
      "defaultValue": "{'one': 'a', 'two': 'b'}"
    }
  },
  "variables": {
    "base64String": "[base64(parameters('stringData'))]",
    "base64Object": "[base64(parameters('jsonFormattedData'))]"
  },
  "resources": [
  ],
  "outputs": {
    "base64Output": {
      "type": "string",
      "value": "[variables('base64String')]"
    },
    "toStringOutput": {
      "type": "string",
      "value": "[base64ToString(variables('base64String'))]"
    },
    "toJsonOutput": {
      "type": "object",
      "value": "[base64ToJson(variables('base64Object'))]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param stringData string = 'one, two, three'
param jsonFormattedData string = '{\'one\': \'a\', \'two\': \'b\'}'

var base64String = base64(stringData)
var base64Object = base64(jsonFormattedData)

output base64Output string = base64String
output toStringOutput string = base64ToString(base64String)
output toJsonOutput object = base64ToJson(base64Object)
```

---

Utdata från föregående exempel med standardvärdena är:

| Namn | Typ | Värde |
| ---- | ---- | ----- |
| base64Output | Sträng | b25lLCB0d28sIHRocmVl |
| toStringOutput | Sträng | Ett två tre |
| toJsonOutput | Objekt | {"One": "a", "två": "b"} |

## <a name="base64tojson"></a>base64ToJson

`base64tojson`

Konverterar en Base64-representation till ett JSON-objekt.

### <a name="parameters"></a>Parametrar

| Parameter | Krävs | Typ | Beskrivning |
|:--- |:--- |:--- |:--- |
| base64Value |Ja |sträng |Den base64-representation som ska konverteras till ett JSON-objekt. |

### <a name="return-value"></a>Returvärde

Ett JSON-objekt.

### <a name="examples"></a>Exempel

Följande [exempel mall](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/base64.json) använder funktionen base64ToJson för att konvertera ett base64-värde:

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "stringData": {
      "type": "string",
      "defaultValue": "one, two, three"
    },
    "jsonFormattedData": {
      "type": "string",
      "defaultValue": "{'one': 'a', 'two': 'b'}"
    }
  },
  "variables": {
    "base64String": "[base64(parameters('stringData'))]",
    "base64Object": "[base64(parameters('jsonFormattedData'))]"
  },
  "resources": [
  ],
  "outputs": {
    "base64Output": {
      "type": "string",
      "value": "[variables('base64String')]"
    },
    "toStringOutput": {
      "type": "string",
      "value": "[base64ToString(variables('base64String'))]"
    },
    "toJsonOutput": {
      "type": "object",
      "value": "[base64ToJson(variables('base64Object'))]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param stringData string = 'one, two, three'
param jsonFormattedData string = '{\'one\': \'a\', \'two\': \'b\'}'

var base64String = base64(stringData)
var base64Object = base64(jsonFormattedData)

output base64Output string = base64String
output toStringOutput string = base64ToString(base64String)
output toJsonOutput object = base64ToJson(base64Object)

```

---

Utdata från föregående exempel med standardvärdena är:

| Namn | Typ | Värde |
| ---- | ---- | ----- |
| base64Output | Sträng | b25lLCB0d28sIHRocmVl |
| toStringOutput | Sträng | Ett två tre |
| toJsonOutput | Objekt | {"One": "a", "två": "b"} |

## <a name="base64tostring"></a>base64ToString

`base64ToString(base64Value)`

Konverterar en Base64-representation till en sträng.

### <a name="parameters"></a>Parametrar

| Parameter | Krävs | Typ | Beskrivning |
|:--- |:--- |:--- |:--- |
| base64Value |Ja |sträng |Den base64-representation som ska konverteras till en sträng. |

### <a name="return-value"></a>Returvärde

En sträng med det konverterade base64-värdet.

### <a name="examples"></a>Exempel

Följande [exempel mall](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/base64.json) använder funktionen base64ToString för att konvertera ett base64-värde:

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "stringData": {
      "type": "string",
      "defaultValue": "one, two, three"
    },
    "jsonFormattedData": {
      "type": "string",
      "defaultValue": "{'one': 'a', 'two': 'b'}"
    }
  },
  "variables": {
    "base64String": "[base64(parameters('stringData'))]",
    "base64Object": "[base64(parameters('jsonFormattedData'))]"
  },
  "resources": [
  ],
  "outputs": {
    "base64Output": {
      "type": "string",
      "value": "[variables('base64String')]"
    },
    "toStringOutput": {
      "type": "string",
      "value": "[base64ToString(variables('base64String'))]"
    },
    "toJsonOutput": {
      "type": "object",
      "value": "[base64ToJson(variables('base64Object'))]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param stringData string = 'one, two, three'
param jsonFormattedData string = '{\'one\': \'a\', \'two\': \'b\'}'

var base64String = base64(stringData)
var base64Object = base64(jsonFormattedData)

output base64Output string = base64String
output toStringOutput string = base64ToString(base64String)
output toJsonOutput object = base64ToJson(base64Object)
```

---

Utdata från föregående exempel med standardvärdena är:

| Namn | Typ | Värde |
| ---- | ---- | ----- |
| base64Output | Sträng | b25lLCB0d28sIHRocmVl |
| toStringOutput | Sträng | Ett två tre |
| toJsonOutput | Objekt | {"One": "a", "två": "b"} |

## <a name="concat"></a>concat

`concat (arg1, arg2, arg3, ...)`

Kombinerar flera sträng värden och returnerar den sammanfogade strängen, eller kombinerar flera matriser och returnerar den sammanfogade matrisen.

För att förenkla sträng sammanfogningen stöder bicep en syntax för [String-interpolation](https://en.wikipedia.org/wiki/String_interpolation#) .

### <a name="parameters"></a>Parametrar

| Parameter | Krävs | Typ | Beskrivning |
|:--- |:--- |:--- |:--- |
| arg1 |Ja |sträng eller matris |Den första strängen eller matrisen för sammanfogning. |
| ytterligare argument |Inga |sträng eller matris |Ytterligare strängar eller matriser i sekventiell ordning för sammanfogning. |

Den här funktionen kan ta valfritt antal argument och kan acceptera antingen strängar eller matriser för parametrarna. Du kan dock inte ange både matriser och strängar för parametrar. Strängar sammanfogas bara med andra strängar.

### <a name="return-value"></a>Returvärde

En sträng eller matris med sammanfogade värden.

### <a name="examples"></a>Exempel

I följande [exempel mall](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/concat-string.json) visas hur du kombinerar två sträng värden och returnerar en sammanfogad sträng.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "prefix": {
      "type": "string",
      "defaultValue": "prefix"
    }
  },
  "resources": [],
  "outputs": {
    "concatOutput": {
      "type": "string",
      "value": "[concat(parameters('prefix'), '-', uniqueString(resourceGroup().id))]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param prefix string = 'prefix'

output concatOutput string = concat(prefix, '-', uniqueString(resourceGroup().id))
```

eller

```bicep
param prefix string = 'prefix'

output concatOutput string = '${prefix}-${uniqueString(resourceGroup().id)}'
```

---

Utdata från föregående exempel med standardvärdena är:

| Namn | Typ | Värde |
| ---- | ---- | ----- |
| concatOutput | Sträng | prefix – 5yj4yjf5mbg72 |

I följande [exempel mall](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/concat-array.json) visas hur du kombinerar två matriser.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "firstArray": {
      "type": "array",
      "defaultValue": [
        "1-1",
        "1-2",
        "1-3"
      ]
    },
    "secondArray": {
      "type": "array",
      "defaultValue": [
        "2-1",
        "2-2",
        "2-3"
      ]
    }
  },
  "resources": [
  ],
  "outputs": {
    "return": {
      "type": "array",
      "value": "[concat(parameters('firstArray'), parameters('secondArray'))]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param firstArray array = [
  '1-1'
  '1-2'
  '1-3'
]
param secondArray array = [
  '2-1'
  '2-2'
  '2-3'
]

output return array = concat(firstArray, secondArray)
```

---

Utdata från föregående exempel med standardvärdena är:

| Namn | Typ | Värde |
| ---- | ---- | ----- |
| återgå | Matris | ["1-1", "1-2", "1-3", "2-1", "2-2", "2-3"] |

## <a name="contains"></a>innehåller

`contains (container, itemToFind)`

Kontrollerar om en matris innehåller ett värde, ett objekt innehåller en nyckel eller en sträng innehåller en under sträng. Sträng jämförelsen är Skift läges känslig. Men när du testar om ett objekt innehåller en nyckel är jämförelsen Skift läges okänslig.

### <a name="parameters"></a>Parametrar

| Parameter | Krävs | Typ | Beskrivning |
|:--- |:--- |:--- |:--- |
| container |Ja |matris, objekt eller sträng |Värdet som innehåller värdet som ska hittas. |
| itemToFind |Ja |sträng eller heltal |Det värde som ska hittas. |

### <a name="return-value"></a>Returvärde

**Sant** om objektet hittas; annars **false**.

### <a name="examples"></a>Exempel

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

## <a name="datauri"></a>dataUri

`dataUri(stringToConvert)`

Konverterar ett värde till en data-URI.

### <a name="parameters"></a>Parametrar

| Parameter | Krävs | Typ | Beskrivning |
|:--- |:--- |:--- |:--- |
| stringToConvert |Ja |sträng |Det värde som ska konverteras till en data-URI. |

### <a name="return-value"></a>Returvärde

En sträng formaterad som en data-URI.

### <a name="examples"></a>Exempel

I följande [exempel mall](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/datauri.json) konverteras ett värde till en data-URI och en data-URI konverteras till en sträng:

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "stringToTest": {
      "type": "string",
      "defaultValue": "Hello"
    },
    "dataFormattedString": {
      "type": "string",
      "defaultValue": "data:;base64,SGVsbG8sIFdvcmxkIQ=="
    }
  },
  "resources": [],
  "outputs": {
    "dataUriOutput": {
      "value": "[dataUri(parameters('stringToTest'))]",
      "type": "string"
    },
    "toStringOutput": {
      "type": "string",
      "value": "[dataUriToString(parameters('dataFormattedString'))]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param stringToTest string = 'Hello'
param dataFormattedString string = 'data:;base64,SGVsbG8sIFdvcmxkIQ=='

output dataUriOutput string = dataUri(stringToTest)
output toStringOutput string = dataUriToString(dataFormattedString)
```

---

Utdata från föregående exempel med standardvärdena är:

| Namn | Typ | Värde |
| ---- | ---- | ----- |
| dataUriOutput | Sträng | data: text/plain, charset = utf8; base64, SGVsbG8 = |
| toStringOutput | Sträng | Hello World! |

## <a name="datauritostring"></a>dataUriToString

`dataUriToString(dataUriToConvert)`

Konverterar ett data-URI-formaterat värde till en sträng.

### <a name="parameters"></a>Parametrar

| Parameter | Krävs | Typ | Beskrivning |
|:--- |:--- |:--- |:--- |
| dataUriToConvert |Ja |sträng |Data-URI-värdet som ska konverteras. |

### <a name="return-value"></a>Returvärde

En sträng som innehåller det konverterade värdet.

### <a name="examples"></a>Exempel

I följande [exempel mall](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/datauri.json) konverteras ett värde till en data-URI och en data-URI konverteras till en sträng:

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "stringToTest": {
      "type": "string",
      "defaultValue": "Hello"
    },
    "dataFormattedString": {
      "type": "string",
      "defaultValue": "data:;base64,SGVsbG8sIFdvcmxkIQ=="
    }
  },
  "resources": [],
  "outputs": {
    "dataUriOutput": {
      "value": "[dataUri(parameters('stringToTest'))]",
      "type": "string"
    },
    "toStringOutput": {
      "type": "string",
      "value": "[dataUriToString(parameters('dataFormattedString'))]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param stringToTest string = 'Hello'
param dataFormattedString string = 'data:;base64,SGVsbG8sIFdvcmxkIQ=='

output dataUriOutput string = dataUri(stringToTest)
output toStringOutput string = dataUriToString(dataFormattedString)
```

---

Utdata från föregående exempel med standardvärdena är:

| Namn | Typ | Värde |
| ---- | ---- | ----- |
| dataUriOutput | Sträng | data: text/plain, charset = utf8; base64, SGVsbG8 = |
| toStringOutput | Sträng | Hello World! |

## <a name="empty"></a>tomt

`empty(itemToTest)`

Anger om en matris, ett objekt eller en sträng är tom.

### <a name="parameters"></a>Parametrar

| Parameter | Krävs | Typ | Beskrivning |
|:--- |:--- |:--- |:--- |
| itemToTest |Ja |matris, objekt eller sträng |Värdet för att kontrol lera om det är tomt. |

### <a name="return-value"></a>Returvärde

Returnerar **Sant** om värdet är tomt. annars **false**.

### <a name="examples"></a>Exempel

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

## <a name="endswith"></a>endsWith

`endsWith(stringToSearch, stringToFind)`

Anger om en sträng slutar med ett värde. Jämförelsen är inte Skift läges känslig.

### <a name="parameters"></a>Parametrar

| Parameter | Krävs | Typ | Beskrivning |
|:--- |:--- |:--- |:--- |
| stringToSearch |Ja |sträng |Det värde som innehåller objektet som ska hittas. |
| stringToFind |Ja |sträng |Det värde som ska hittas. |

### <a name="return-value"></a>Returvärde

**Sant** om det sista tecknet eller tecknen i strängen matchar värdet; annars **false**.

### <a name="examples"></a>Exempel

I följande [exempel mall](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/startsendswith.json) visas hur du använder funktionerna StartsWith och endsWith:

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "resources": [],
  "outputs": {
    "startsTrue": {
      "value": "[startsWith('abcdef', 'ab')]",
      "type": "bool"
    },
    "startsCapTrue": {
      "value": "[startsWith('abcdef', 'A')]",
      "type": "bool"
    },
    "startsFalse": {
      "value": "[startsWith('abcdef', 'e')]",
      "type": "bool"
    },
    "endsTrue": {
      "value": "[endsWith('abcdef', 'ef')]",
      "type": "bool"
    },
    "endsCapTrue": {
      "value": "[endsWith('abcdef', 'F')]",
      "type": "bool"
    },
    "endsFalse": {
      "value": "[endsWith('abcdef', 'e')]",
      "type": "bool"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
output startsTrue bool = startsWith('abcdef', 'ab')
output startsCapTrue bool = startsWith('abcdef', 'A')
output startsFalse bool = startsWith('abcdef', 'e')
output endsTrue bool = endsWith('abcdef', 'ef')
output endsCapTrue bool = endsWith('abcdef', 'F')
output endsFalse bool = endsWith('abcdef', 'e')
```

---

Utdata från föregående exempel med standardvärdena är:

| Namn | Typ | Värde |
| ---- | ---- | ----- |
| startsTrue | Bool | Sant |
| startsCapTrue | Bool | Sant |
| startsFalse | Bool | Falskt |
| endsTrue | Bool | Sant |
| endsCapTrue | Bool | Sant |
| endsFalse | Bool | Falskt |

## <a name="first"></a>förstagångskörningen

`first(arg1)`

Returnerar det första tecken strängen eller det första elementet i matrisen.

### <a name="parameters"></a>Parametrar

| Parameter | Krävs | Typ | Beskrivning |
|:--- |:--- |:--- |:--- |
| arg1 |Ja |matris eller sträng |Värdet för att hämta det första elementet eller specialtecknet. |

### <a name="return-value"></a>Returvärde

En sträng med det första tecken eller typen (sträng, heltal, matris eller objekt) för det första elementet i en matris.

### <a name="examples"></a>Exempel

I följande [exempel mall](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/first.json) visas hur du använder den första funktionen med en matris och sträng.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "arrayToTest": {
      "type": "array",
      "defaultValue": [ "one", "two", "three" ]
    }
  },
  "resources": [
  ],
  "outputs": {
    "arrayOutput": {
      "type": "string",
      "value": "[first(parameters('arrayToTest'))]"
    },
    "stringOutput": {
      "type": "string",
      "value": "[first('One Two Three')]"
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

output arrayOutput string = first(arrayToTest)
output stringOutput string = first('One Two Three')
```

---

Utdata från föregående exempel med standardvärdena är:

| Namn | Typ | Värde |
| ---- | ---- | ----- |
| arrayOutput | Sträng | en |
| stringOutput | Sträng | O |

## <a name="format"></a>format

`format(formatString, arg1, arg2, ...)`

Skapar en formaterad sträng från angivna värden.

### <a name="parameters"></a>Parametrar

| Parameter | Krävs | Typ | Beskrivning |
|:--- |:--- |:--- |:--- |
| formatString | Ja | sträng | Strängen för sammansatt format. |
| arg1 | Ja | sträng, heltal eller boolesk | Värdet som ska tas med i den formaterade strängen. |
| ytterligare argument | Inga | sträng, heltal eller boolesk | Ytterligare värden som ska tas med i den formaterade strängen. |

### <a name="remarks"></a>Kommentarer

Använd den här funktionen för att formatera en sträng i mallen. Den använder samma formateringsalternativ som metoden [system. String. format](/dotnet/api/system.string.format) i .net.

### <a name="examples"></a>Exempel

I följande exempel mall visas hur du använder funktionen format.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "greeting": {
      "type": "string",
      "defaultValue": "Hello"
    },
    "name": {
      "type": "string",
      "defaultValue": "User"
    },
    "numberToFormat": {
      "type": "int",
      "defaultValue": 8175133
    }
  },
  "resources": [
  ],
  "outputs": {
    "formatTest": {
      "type": "string",
      "value": "[format('{0}, {1}. Formatted number: {2:N0}', parameters('greeting'), parameters('name'), parameters('numberToFormat'))]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param greeting string = 'Hello'
param name string = 'User'
param numberToFormat int = 8175133

output formatTest string = format('{0}, {1}. Formatted number: {2:N0}', greeting, name, numberToFormat)
```

---

Utdata från föregående exempel med standardvärdena är:

| Namn | Typ | Värde |
| ---- | ---- | ----- |
| formatTest | Sträng | Hej, användare. Formaterat tal: 8 175 133 |

## <a name="guid"></a>guid

`guid(baseString, ...)`

Skapar ett värde i formatet för en globalt unik identifierare baserat på de värden som anges som parametrar.

### <a name="parameters"></a>Parametrar

| Parameter | Krävs | Typ | Beskrivning |
|:--- |:--- |:--- |:--- |
| baseString |Ja |sträng |Värdet som används i hash-funktionen för att skapa GUID. |
| ytterligare parametrar efter behov |Inga |sträng |Du kan lägga till så många strängar som behövs för att skapa värdet som anger nivån av unikhet. |

### <a name="remarks"></a>Kommentarer

Den här funktionen är användbar när du behöver skapa ett värde i formatet för en globalt unik identifierare. Du anger parameter värden som begränsar omfattningen av unikhet för resultatet. Du kan ange om namnet är unikt för prenumeration, resurs grupp eller distribution.

Det returnerade värdet är inte en slumpmässig sträng, utan i stället resultatet av en hash-funktion på parametrarna. Det returnerade värdet är 36 tecken långt. Den är inte globalt unik. Om du vill skapa ett nytt GUID som inte baseras på det hash-värdet för parametrarna använder du funktionen [newGuid](#newguid) .

I följande exempel visas hur du använder GUID för att skapa ett unikt värde för ofta använda nivåer.

Unikt omfång för prenumeration

# <a name="json"></a>[JSON](#tab/json)

```json
"[guid(subscription().subscriptionId)]"
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
guid(subscription().subscriptionId)
```

---

Unikt scope till resurs grupp

# <a name="json"></a>[JSON](#tab/json)

```json
"[guid(resourceGroup().id)]"
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
guid(resourceGroup().id)
```

---

Unikt scope till distribution för en resurs grupp

# <a name="json"></a>[JSON](#tab/json)

```json
"[guid(resourceGroup().id, deployment().name)]"
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
guid(resourceGroup().id, deployment().name)
```

---

### <a name="return-value"></a>Returvärde

En sträng som innehåller 36 tecken i formatet för en globalt unik identifierare.

### <a name="examples"></a>Exempel

Följande [exempel-mall](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/guid.json) returnerar resultat från GUID:

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {},
  "variables": {},
  "resources": [],
  "outputs": {
    "guidPerSubscription": {
      "value": "[guid(subscription().subscriptionId)]",
      "type": "string"
    },
    "guidPerResourceGroup": {
      "value": "[guid(resourceGroup().id)]",
      "type": "string"
    },
    "guidPerDeployment": {
      "value": "[guid(resourceGroup().id, deployment().name)]",
      "type": "string"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
output guidPerSubscription string = guid(subscription().subscriptionId)
output guidPerResourceGroup string = guid(resourceGroup().id)
output guidPerDeployment string = guid(resourceGroup().id, deployment().name)
```

---

## <a name="indexof"></a>indexOf

`indexOf(stringToSearch, stringToFind)`

Returnerar den första positionen för ett värde i en sträng. Jämförelsen är inte Skift läges känslig.

### <a name="parameters"></a>Parametrar

| Parameter | Krävs | Typ | Beskrivning |
|:--- |:--- |:--- |:--- |
| stringToSearch |Ja |sträng |Det värde som innehåller objektet som ska hittas. |
| stringToFind |Ja |sträng |Det värde som ska hittas. |

### <a name="return-value"></a>Returvärde

Ett heltal som representerar positionen för det objekt som ska hittas. Värdet är noll-baserat. Om objektet inte hittas, returneras-1.

### <a name="examples"></a>Exempel

I följande [exempel mall](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/indexof.json) visas hur du använder funktionerna indexOf och lastIndexOf:

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "resources": [],
  "outputs": {
    "firstT": {
      "value": "[indexOf('test', 't')]",
      "type": "int"
    },
    "lastT": {
      "value": "[lastIndexOf('test', 't')]",
      "type": "int"
    },
    "firstString": {
      "value": "[indexOf('abcdef', 'CD')]",
      "type": "int"
    },
    "lastString": {
      "value": "[lastIndexOf('abcdef', 'AB')]",
      "type": "int"
    },
    "notFound": {
      "value": "[indexOf('abcdef', 'z')]",
      "type": "int"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
output firstT int = indexOf('test', 't')
output lastT int = lastIndexOf('test', 't')
output firstString int = indexOf('abcdef', 'CD')
output lastString int = lastIndexOf('abcdef', 'AB')
output notFound int = indexOf('abcdef', 'z')
```

---

Utdata från föregående exempel med standardvärdena är:

| Namn | Typ | Värde |
| ---- | ---- | ----- |
| första | Int | 0 |
| Senaste | Int | 3 |
| firstString | Int | 2 |
| lastString | Int | 0 |
| notFound | Int | -1 |

<a id="json"></a>

## <a name="json"></a>json

`json(arg1)`

Konverterar en giltig JSON-sträng till en JSON-datatyp. Mer information finns i [JSON-funktion](template-functions-object.md#json).

## <a name="last"></a>pågå

`last (arg1)`

Returnerar det sista tecken strängen eller det sista elementet i matrisen.

### <a name="parameters"></a>Parametrar

| Parameter | Krävs | Typ | Beskrivning |
|:--- |:--- |:--- |:--- |
| arg1 |Ja |matris eller sträng |Värdet för att hämta det sista elementet eller specialtecknet. |

### <a name="return-value"></a>Returvärde

En sträng med det sista tecken eller typen (sträng, heltal, matris eller objekt) för det sista elementet i en matris.

### <a name="examples"></a>Exempel

I följande [exempel mall](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/last.json) visas hur du använder den sista funktionen med en matris och sträng.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "arrayToTest": {
      "type": "array",
      "defaultValue": [ "one", "two", "three" ]
    }
  },
  "resources": [
  ],
  "outputs": {
    "arrayOutput": {
      "type": "string",
      "value": "[last(parameters('arrayToTest'))]"
    },
    "stringOutput": {
      "type": "string",
      "value": "[last('One Two Three')]"
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

output arrayOutput string = last(arrayToTest)
output stringOutput string = last('One Two Three')
```

---

Utdata från föregående exempel med standardvärdena är:

| Namn | Typ | Värde |
| ---- | ---- | ----- |
| arrayOutput | Sträng | tre |
| stringOutput | Sträng | e |

## <a name="lastindexof"></a>lastIndexOf

`lastIndexOf(stringToSearch, stringToFind)`

Returnerar den sista positionen för ett värde i en sträng. Jämförelsen är inte Skift läges känslig.

### <a name="parameters"></a>Parametrar

| Parameter | Krävs | Typ | Beskrivning |
|:--- |:--- |:--- |:--- |
| stringToSearch |Ja |sträng |Det värde som innehåller objektet som ska hittas. |
| stringToFind |Ja |sträng |Det värde som ska hittas. |

### <a name="return-value"></a>Returvärde

Ett heltal som representerar den sista positionen för objektet som ska hittas. Värdet är noll-baserat. Om objektet inte hittas, returneras-1.

### <a name="examples"></a>Exempel

I följande [exempel mall](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/indexof.json) visas hur du använder funktionerna indexOf och lastIndexOf:

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "resources": [],
  "outputs": {
    "firstT": {
      "value": "[indexOf('test', 't')]",
      "type": "int"
    },
    "lastT": {
      "value": "[lastIndexOf('test', 't')]",
      "type": "int"
    },
    "firstString": {
      "value": "[indexOf('abcdef', 'CD')]",
      "type": "int"
    },
    "lastString": {
      "value": "[lastIndexOf('abcdef', 'AB')]",
      "type": "int"
    },
    "notFound": {
      "value": "[indexOf('abcdef', 'z')]",
      "type": "int"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
output firstT int = indexOf('test', 't')
output lastT int = lastIndexOf('test', 't')
output firstString int = indexOf('abcdef', 'CD')
output lastString int = lastIndexOf('abcdef', 'AB')
output notFound int = indexOf('abcdef', 'z')
```

---

Utdata från föregående exempel med standardvärdena är:

| Namn | Typ | Värde |
| ---- | ---- | ----- |
| första | Int | 0 |
| Senaste | Int | 3 |
| firstString | Int | 2 |
| lastString | Int | 0 |
| notFound | Int | -1 |

## <a name="length"></a>length

`length(string)`

Returnerar antalet tecken i en sträng, element i en matris eller på rot nivå egenskaper i ett objekt.

### <a name="parameters"></a>Parametrar

| Parameter | Krävs | Typ | Beskrivning |
|:--- |:--- |:--- |:--- |
| arg1 |Ja |matris, sträng eller objekt |Den matris som ska användas för att hämta antalet element, strängen som ska användas för att hämta antalet tecken, eller objektet som ska användas för att hämta antalet rot nivå egenskaper. |

### <a name="return-value"></a>Returvärde

En int.

### <a name="examples"></a>Exempel

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

## <a name="newguid"></a>newGuid

`newGuid()`

Returnerar ett värde i formatet för en globalt unik identifierare. **Den här funktionen kan endast användas i standardvärdet för en parameter.**

### <a name="remarks"></a>Kommentarer

Du kan bara använda den här funktionen i ett uttryck för standardvärdet för en parameter. Om du använder den här funktionen någon annan stans i en mall returneras ett fel. Funktionen tillåts inte i andra delar av mallen eftersom den returnerar ett annat värde varje gång den anropas. Att distribuera samma mall med samma parametrar skulle inte tillförlitligt producera samma resultat.

Funktionen newGuid skiljer sig från [GUID](#guid) -funktionen eftersom den inte tar några parametrar. När du anropar GUID med samma parameter returneras samma identifierare varje gång. Använd GUID när du behöver generera samma GUID tillförlitligt för en speciell miljö. Använd newGuid när du behöver en annan identifierare varje gång, till exempel att distribuera resurser till en test miljö.

Funktionen newGuid använder GUID- [strukturen](/dotnet/api/system.guid) i .NET Framework för att generera den globalt unika identifieraren.

Om du använder [alternativet för att distribuera om en tidigare lyckad distribution](rollback-on-error.md), och den tidigare distributionen innehåller en parameter som använder newGuid, utvärderas inte parametern. I stället återanvänds parametervärdet från den tidigare distributionen automatiskt i återställnings distributionen.

I en test miljö kan du behöva distribuera resurser flera gånger under en kort tid. I stället för att skapa unika namn kan du använda newGuid med [uniqueString](#uniquestring) för att skapa unika namn.

Var noga med att distribuera om en mall som förlitar sig på newGuid-funktionen för ett standardvärde. När du distribuerar om och inte anger något värde för parametern utvärderas funktionen om. Om du vill uppdatera en befintlig resurs i stället för att skapa en ny, måste du skicka värdet i parametervärdet från den tidigare distributionen.

### <a name="return-value"></a>Returvärde

En sträng som innehåller 36 tecken i formatet för en globalt unik identifierare.

### <a name="examples"></a>Exempel

I följande exempel mall visas en parameter med en ny identifierare.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "guidValue": {
      "type": "string",
      "defaultValue": "[newGuid()]"
    }
  },
  "resources": [
  ],
  "outputs": {
    "guidOutput": {
      "type": "string",
      "value": "[parameters('guidValue')]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param guidValue string = newGuid()

output guidOutput string = guidValue
```

---

Resultatet från föregående exempel varierar för varje distribution, men ser ut ungefär så här:

| Namn | Typ | Värde |
| ---- | ---- | ----- |
| guidOutput | sträng | b76a51fc-bd72-4a77-b9a2-3c29e7d2e551 |

I följande exempel används funktionen newGuid för att skapa ett unikt namn för ett lagrings konto. Den här mallen kan fungera för test miljöer där lagrings kontot finns under en kort tid och inte omdistribueras.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "guidValue": {
      "type": "string",
      "defaultValue": "[newGuid()]"
    }
  },
  "variables": {
    "storageName": "[concat('storage', uniqueString(parameters('guidValue')))]"
  },
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "name": "[variables('storageName')]",
      "location": "West US",
      "apiVersion": "2018-07-01",
      "sku": {
        "name": "Standard_LRS"
      },
      "kind": "StorageV2",
      "properties": {}
    }
  ],
  "outputs": {
    "nameOutput": {
      "type": "string",
      "value": "[variables('storageName')]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param guidValue string = newGuid()

var storageName = 'storage${uniqueString(guidValue)}'

resource myStorage 'Microsoft.Storage/storageAccounts@2018-07-01' = {
  name: storageName
  location: 'West US'
  sku: {
    name: 'Standard_LRS'
  }
  kind: 'StorageV2'
  properties: {}
}

output nameOutput string = storageName
```

---

Resultatet från föregående exempel varierar för varje distribution, men ser ut ungefär så här:

| Namn | Typ | Värde |
| ---- | ---- | ----- |
| nameOutput | sträng | storagenziwvyru7uxie |

## <a name="padleft"></a>padLeft

`padLeft(valueToPad, totalLength, paddingCharacter)`

Returnerar en högerjusterad sträng genom att lägga till tecken till vänster tills den totala angivna längden uppnås.

### <a name="parameters"></a>Parametrar

| Parameter | Krävs | Typ | Beskrivning |
|:--- |:--- |:--- |:--- |
| valueToPad |Ja |sträng eller heltal |Värdet till Högerjustera. |
| totalLength |Ja |int |Det totala antalet tecken i den returnerade strängen. |
| paddingCharacter |Inga |enstaka Character |Det tecken som ska användas för vänster utfyllnad tills den totala längden har uppnåtts. Standardvärdet är ett blank steg. |

Om den ursprungliga strängen är längre än antalet tecken som ska padas läggs inga tecken till.

### <a name="return-value"></a>Returvärde

En sträng med minst antalet angivna tecken.

### <a name="examples"></a>Exempel

I följande [exempel mall](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/padleft.json) visas hur du fyller i det användardefinierade parametervärdet genom att lägga till noll tecknet tills det når det totala antalet tecken.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "testString": {
      "type": "string",
      "defaultValue": "123"
    }
  },
  "resources": [],
  "outputs": {
    "stringOutput": {
      "type": "string",
      "value": "[padLeft(parameters('testString'),10,'0')]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param testString string = '123'

output stringOutput string = padLeft(testString, 10, '0')
```

---

Utdata från föregående exempel med standardvärdena är:

| Namn | Typ | Värde |
| ---- | ---- | ----- |
| stringOutput | Sträng | 0000000123 |

## <a name="replace"></a>bytt

`replace(originalString, oldString, newString)`

Returnerar en ny sträng med alla instanser av en sträng ersatt av en annan sträng.

### <a name="parameters"></a>Parametrar

| Parameter | Krävs | Typ | Beskrivning |
|:--- |:--- |:--- |:--- |
| originalString |Ja |sträng |Värdet som innehåller alla instanser av en sträng ersatt av en annan sträng. |
| oldString |Ja |sträng |Strängen som ska tas bort från den ursprungliga strängen. |
| newString |Ja |sträng |Strängen som ska läggas till i stället för den borttagna strängen. |

### <a name="return-value"></a>Returvärde

En sträng med de ersatta tecknen.

### <a name="examples"></a>Exempel

I följande [exempel mall](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/replace.json) visas hur du tar bort alla streck från den användardefinierade strängen och hur du ersätter delar av strängen med en annan sträng.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "testString": {
      "type": "string",
      "defaultValue": "123-123-1234"
    }
  },
  "resources": [],
  "outputs": {
    "firstOutput": {
      "type": "string",
      "value": "[replace(parameters('testString'),'-', '')]"
    },
    "secondOutput": {
      "type": "string",
      "value": "[replace(parameters('testString'),'1234', 'xxxx')]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param testString string = '123-123-1234'

output firstOutput string = replace(testString, '-', '')
output secondOutput string = replace(testString, '1234', 'xxxx')
```

---

Utdata från föregående exempel med standardvärdena är:

| Namn | Typ | Värde |
| ---- | ---- | ----- |
| firstOutput | Sträng | 1231231234 |
| secondOutput | Sträng | 123-123-xxxx |

## <a name="skip"></a>hoppa över

`skip(originalValue, numberToSkip)`

Returnerar en sträng med alla tecken efter det angivna antalet tecken, eller en matris med alla element efter det angivna antalet element.

### <a name="parameters"></a>Parametrar

| Parameter | Krävs | Typ | Beskrivning |
|:--- |:--- |:--- |:--- |
| Ursprungligt värde |Ja |matris eller sträng |Matrisen eller strängen som ska användas för att hoppa över. |
| numberToSkip |Ja |int |Det antal element eller tecken som ska hoppas över. Om värdet är 0 eller mindre returneras alla element eller tecken i värdet. Om den är större än längden på matrisen eller strängen returneras en tom matris eller sträng. |

### <a name="return-value"></a>Returvärde

En matris eller sträng.

### <a name="examples"></a>Exempel

Följande [exempel-mall](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/skip.json) hoppar över det angivna antalet element i matrisen och det angivna antalet tecken i en sträng.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "testArray": {
      "type": "array",
      "defaultValue": [
        "one",
        "two",
        "three"
      ]
    },
    "elementsToSkip": {
      "type": "int",
      "defaultValue": 2
    },
    "testString": {
      "type": "string",
      "defaultValue": "one two three"
    },
    "charactersToSkip": {
      "type": "int",
      "defaultValue": 4
    }
  },
  "resources": [],
  "outputs": {
    "arrayOutput": {
      "type": "array",
      "value": "[skip(parameters('testArray'),parameters('elementsToSkip'))]"
    },
    "stringOutput": {
      "type": "string",
      "value": "[skip(parameters('testString'),parameters('charactersToSkip'))]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param testArray array = [
  'one'
  'two'
  'three'
]
param elementsToSkip int = 2
param testString string = 'one two three'
param charactersToSkip int = 4

output arrayOutput array = skip(testArray, elementsToSkip)
output stringOutput string = skip(testString, charactersToSkip)
```

---

Utdata från föregående exempel med standardvärdena är:

| Namn | Typ | Värde |
| ---- | ---- | ----- |
| arrayOutput | Matris | ["tre"] |
| stringOutput | Sträng | 2 3 |

## <a name="split"></a>split

`split(inputString, delimiter)`

Returnerar en matris med strängar som innehåller under strängarna i Indatasträngen som avgränsas av angivna avgränsare.

### <a name="parameters"></a>Parametrar

| Parameter | Krävs | Typ | Beskrivning |
|:--- |:--- |:--- |:--- |
| inputString |Ja |sträng |Strängen som ska delas. |
| avgränsare |Ja |sträng eller matris med strängar |Avgränsaren som ska användas för att dela strängen. |

### <a name="return-value"></a>Returvärde

En sträng mat ris.

### <a name="examples"></a>Exempel

I följande [exempel mall](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/split.json) delas Indatasträngen med kommatecken, med antingen ett kommatecken eller ett semikolon.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "firstString": {
      "type": "string",
      "defaultValue": "one,two,three"
    },
    "secondString": {
      "type": "string",
      "defaultValue": "one;two,three"
    }
  },
  "variables": {
    "delimiters": [ ",", ";" ]
  },
  "resources": [],
  "outputs": {
    "firstOutput": {
      "type": "array",
      "value": "[split(parameters('firstString'),',')]"
    },
    "secondOutput": {
      "type": "array",
      "value": "[split(parameters('secondString'),variables('delimiters'))]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param firstString string = 'one,two,three'
param secondString string = 'one;two,three'

var delimiters = [
  ','
  ';'
]

output firstOutput array = split(firstString, ',')
output secondOutput array = split(secondString, delimiters)
```

---

Utdata från föregående exempel med standardvärdena är:

| Namn | Typ | Värde |
| ---- | ---- | ----- |
| firstOutput | Matris | ["One", "två", "tre"] |
| secondOutput | Matris | ["One", "två", "tre"] |

## <a name="startswith"></a>startsWith

`startsWith(stringToSearch, stringToFind)`

Anger om en sträng börjar med ett värde. Jämförelsen är inte Skift läges känslig.

### <a name="parameters"></a>Parametrar

| Parameter | Krävs | Typ | Beskrivning |
|:--- |:--- |:--- |:--- |
| stringToSearch |Ja |sträng |Det värde som innehåller objektet som ska hittas. |
| stringToFind |Ja |sträng |Det värde som ska hittas. |

### <a name="return-value"></a>Returvärde

**Sant** om det första tecknet eller tecknen i strängen matchar värdet; annars **false**.

### <a name="examples"></a>Exempel

I följande [exempel mall](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/startsendswith.json) visas hur du använder funktionerna StartsWith och endsWith:

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "resources": [],
  "outputs": {
    "startsTrue": {
      "value": "[startsWith('abcdef', 'ab')]",
      "type": "bool"
    },
    "startsCapTrue": {
      "value": "[startsWith('abcdef', 'A')]",
      "type": "bool"
    },
    "startsFalse": {
      "value": "[startsWith('abcdef', 'e')]",
      "type": "bool"
    },
    "endsTrue": {
      "value": "[endsWith('abcdef', 'ef')]",
      "type": "bool"
    },
    "endsCapTrue": {
      "value": "[endsWith('abcdef', 'F')]",
      "type": "bool"
    },
    "endsFalse": {
      "value": "[endsWith('abcdef', 'e')]",
      "type": "bool"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
output startsTrue bool = startsWith('abcdef', 'ab')
output startsCapTrue bool = startsWith('abcdef', 'A')
output startsFalse bool = startsWith('abcdef', 'e')
output endsTrue bool = endsWith('abcdef', 'ef')
output endsCapTrue bool = endsWith('abcdef', 'F')
output endsFalse bool = endsWith('abcdef', 'e')
```

---

Utdata från föregående exempel med standardvärdena är:

| Namn | Typ | Värde |
| ---- | ---- | ----- |
| startsTrue | Bool | Sant |
| startsCapTrue | Bool | Sant |
| startsFalse | Bool | Falskt |
| endsTrue | Bool | Sant |
| endsCapTrue | Bool | Sant |
| endsFalse | Bool | False |

## <a name="string"></a>sträng

`string(valueToConvert)`

Konverterar det angivna värdet till en sträng.

### <a name="parameters"></a>Parametrar

| Parameter | Krävs | Typ | Beskrivning |
|:--- |:--- |:--- |:--- |
| valueToConvert |Ja | Valfri |Värdet som ska konverteras till sträng. Alla typer av värden kan konverteras, inklusive objekt och matriser. |

### <a name="return-value"></a>Returvärde

En sträng med det konverterade värdet.

### <a name="examples"></a>Exempel

I följande [exempel mall](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/string.json) visas hur du konverterar olika typer av värden till strängar:

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "testObject": {
      "type": "object",
      "defaultValue": {
        "valueA": 10,
        "valueB": "Example Text"
      }
    },
    "testArray": {
      "type": "array",
      "defaultValue": [
        "a",
        "b",
        "c"
      ]
    },
    "testInt": {
      "type": "int",
      "defaultValue": 5
    }
  },
  "resources": [],
  "outputs": {
    "objectOutput": {
      "type": "string",
      "value": "[string(parameters('testObject'))]"
    },
    "arrayOutput": {
      "type": "string",
      "value": "[string(parameters('testArray'))]"
    },
    "intOutput": {
      "type": "string",
      "value": "[string(parameters('testInt'))]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param testObject object = {
  'valueA': 10
  'valueB': 'Example Text'
}
param testArray array = [
  'a'
  'b'
  'c'
]
param testInt int = 5

output objectOutput string = string(testObject)
output arrayOutput string = string(testArray)
output intOutput string = string(testInt)
```

---

Utdata från föregående exempel med standardvärdena är:

| Namn | Typ | Värde |
| ---- | ---- | ----- |
| objectOutput | Sträng | {"värdea": 10, "Värdeb": "exempel text"} |
| arrayOutput | Sträng | ["a", "b", "c"] |
| intOutput | Sträng | 5 |

## <a name="substring"></a>under sträng

`substring(stringToParse, startIndex, length)`

Returnerar en under sträng som börjar vid den angivna tecken positionen och som innehåller det angivna antalet tecken.

### <a name="parameters"></a>Parametrar

| Parameter | Krävs | Typ | Beskrivning |
|:--- |:--- |:--- |:--- |
| stringToParse |Ja |sträng |Den ursprungliga sträng som under strängen extraheras från. |
| Start |Inga |int |Den nollbaserade start tecken positionen för under strängen. |
| length |Inga |int |Antalet tecken för under strängen. Måste referera till en plats inom strängen. Måste vara noll eller större. |

### <a name="return-value"></a>Returvärde

Under strängen. Eller en tom sträng om längden är noll.

### <a name="remarks"></a>Kommentarer

Funktionen Miss lyckas när del strängen sträcker sig utanför slutet av strängen eller när längden är mindre än noll. Följande exempel Miss lyckas med felet "index-och längd parametrarna måste referera till en plats inom strängen. Index parametern: 0, längd parameter: ' 11 ', längden på sträng parametern: ' 10 '.

# <a name="json"></a>[JSON](#tab/json)

```json
"parameters": {
  "inputString": {
    "type": "string",
    "value": "1234567890"
  }
}, "variables": {
  "prefix": "[substring(parameters('inputString'), 0, 11)]"
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param inputString string = '1234567890'

var prefix = substring(inputString, 0, 11)
```

---

### <a name="examples"></a>Exempel

Följande [exempel mal len](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/substring.json) extraherar en under sträng från en parameter.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "testString": {
      "type": "string",
      "defaultValue": "one two three"
    }
  },
  "resources": [],
  "outputs": {
    "substringOutput": {
      "value": "[substring(parameters('testString'), 4, 3)]",
      "type": "string"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param testString string = 'one two three'
output substringOutput string = substring(testString, 4, 3)
```

---

Utdata från föregående exempel med standardvärdena är:

| Namn | Typ | Värde |
| ---- | ---- | ----- |
| substringOutput | Sträng | två |

## <a name="take"></a>take

`take(originalValue, numberToTake)`

Returnerar en sträng med det angivna antalet tecken från början av strängen, eller en matris med det angivna antalet element från matrisens början.

### <a name="parameters"></a>Parametrar

| Parameter | Krävs | Typ | Beskrivning |
|:--- |:--- |:--- |:--- |
| Ursprungligt värde |Ja |matris eller sträng |Matrisen eller strängen som elementen ska tas från. |
| numberToTake |Ja |int |Det antal element eller tecken som ska vidtas. Om värdet är 0 eller mindre returneras en tom matris eller sträng. Om det är större än längden på matrisen eller strängen returneras alla element i matrisen eller strängen. |

### <a name="return-value"></a>Returvärde

En matris eller sträng.

### <a name="examples"></a>Exempel

Följande [exempel-mall](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/take.json) använder det angivna antalet element från matrisen och tecken från en sträng.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "testArray": {
      "type": "array",
      "defaultValue": [
        "one",
        "two",
        "three"
      ]
    },
    "elementsToTake": {
      "type": "int",
      "defaultValue": 2
    },
    "testString": {
      "type": "string",
      "defaultValue": "one two three"
    },
    "charactersToTake": {
      "type": "int",
      "defaultValue": 2
    }
  },
  "resources": [],
  "outputs": {
    "arrayOutput": {
      "type": "array",
      "value": "[take(parameters('testArray'),parameters('elementsToTake'))]"
    },
    "stringOutput": {
      "type": "string",
      "value": "[take(parameters('testString'),parameters('charactersToTake'))]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param testArray array = [
  'one'
  'two'
  'three'
]
param elementsToTake int = 2
param testString string = 'one two three'
param charactersToTake int = 2

output arrayOutput array = take(testArray, elementsToTake)
output stringOutput string = take(testString, charactersToTake)
```

---

Utdata från föregående exempel med standardvärdena är:

| Namn | Typ | Värde |
| ---- | ---- | ----- |
| arrayOutput | Matris | ["One", "två"] |
| stringOutput | Sträng | på |

## <a name="tolower"></a>toLower

`toLower(stringToChange)`

Konverterar den angivna strängen till gemener.

### <a name="parameters"></a>Parametrar

| Parameter | Krävs | Typ | Beskrivning |
|:--- |:--- |:--- |:--- |
| stringToChange |Ja |sträng |Värdet som ska konverteras till gemener. |

### <a name="return-value"></a>Returvärde

Strängen konverterad till gemener.

### <a name="examples"></a>Exempel

Följande [exempel-mall](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/tolower.json) konverterar ett parameter värde till gemener och versaler.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "testString": {
      "type": "string",
      "defaultValue": "One Two Three"
    }
  },
  "resources": [],
  "outputs": {
    "toLowerOutput": {
      "value": "[toLower(parameters('testString'))]",
      "type": "string"
    },
    "toUpperOutput": {
      "type": "string",
      "value": "[toUpper(parameters('testString'))]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param testString string = 'One Two Three'

output toLowerOutput string = toLower(testString)
output toUpperOutput string = toUpper(testString)
```

---

Utdata från föregående exempel med standardvärdena är:

| Namn | Typ | Värde |
| ---- | ---- | ----- |
| toLowerOutput | Sträng | Ett två tre |
| toUpperOutput | Sträng | Ett två tre |

## <a name="toupper"></a>toUpper

`toUpper(stringToChange)`

Konverterar den angivna strängen till versaler.

### <a name="parameters"></a>Parametrar

| Parameter | Krävs | Typ | Beskrivning |
|:--- |:--- |:--- |:--- |
| stringToChange |Ja |sträng |Värdet som ska konverteras till versaler. |

### <a name="return-value"></a>Returvärde

Strängen konverterad till versaler.

### <a name="examples"></a>Exempel

Följande [exempel-mall](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/tolower.json) konverterar ett parameter värde till gemener och versaler.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "testString": {
      "type": "string",
      "defaultValue": "One Two Three"
    }
  },
  "resources": [],
  "outputs": {
    "toLowerOutput": {
      "value": "[toLower(parameters('testString'))]",
      "type": "string"
    },
    "toUpperOutput": {
      "type": "string",
      "value": "[toUpper(parameters('testString'))]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param testString string = 'One Two Three'

output toLowerOutput string = toLower(testString)
output toUpperOutput string = toUpper(testString)
```

---

Utdata från föregående exempel med standardvärdena är:

| Namn | Typ | Värde |
| ---- | ---- | ----- |
| toLowerOutput | Sträng | Ett två tre |
| toUpperOutput | Sträng | Ett två tre |

## <a name="trim"></a>reducera

`trim (stringToTrim)`

Tar bort alla inledande och avslutande blank stegs tecken från den angivna strängen.

### <a name="parameters"></a>Parametrar

| Parameter | Krävs | Typ | Beskrivning |
|:--- |:--- |:--- |:--- |
| stringToTrim |Ja |sträng |Det värde som ska trimmas. |

### <a name="return-value"></a>Returvärde

Strängen utan inledande och avslutande blank stegs tecken.

### <a name="examples"></a>Exempel

I följande [exempel mall](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/trim.json) trimmas blank stegs tecken från parametern.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "testString": {
      "type": "string",
      "defaultValue": "    one two three   "
    }
  },
  "resources": [],
  "outputs": {
    "return": {
      "type": "string",
      "value": "[trim(parameters('testString'))]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param testString string = '    one two three   '

output return string = trim(testString)
```

---

Utdata från föregående exempel med standardvärdena är:

| Namn | Typ | Värde |
| ---- | ---- | ----- |
| återgå | Sträng | Ett två tre |

## <a name="uniquestring"></a>uniqueString

`uniqueString (baseString, ...)`

Skapar en deterministisk hash-sträng baserat på de värden som anges som parametrar.

### <a name="parameters"></a>Parametrar

| Parameter | Krävs | Typ | Beskrivning |
|:--- |:--- |:--- |:--- |
| baseString |Ja |sträng |Värdet som används i hash-funktionen för att skapa en unik sträng. |
| ytterligare parametrar efter behov |Inga |sträng |Du kan lägga till så många strängar som behövs för att skapa värdet som anger nivån av unikhet. |

### <a name="remarks"></a>Kommentarer

Den här funktionen är användbar när du behöver skapa ett unikt namn för en resurs. Du anger parameter värden som begränsar omfattningen av unikhet för resultatet. Du kan ange om namnet är unikt för prenumeration, resurs grupp eller distribution.

Det returnerade värdet är inte en slumpmässig sträng, utan i stället resultatet av en hash-funktion. Det returnerade värdet är 13 tecken långt. Den är inte globalt unik. Du kanske vill kombinera värdet med ett prefix från namngivnings konventionen för att skapa ett namn som är meningsfullt. I följande exempel visas formatet för det returnerade värdet. Det faktiska värdet varierar beroende på de angivna parametrarna.

`tcvhiyu5h2o5o`

I följande exempel visas hur du använder uniqueString för att skapa ett unikt värde för ofta använda nivåer.

Unikt omfång för prenumeration

# <a name="json"></a>[JSON](#tab/json)

```json
"[uniqueString(subscription().subscriptionId)]"
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
uniqueString(subscription().subscriptionId)
```

---

Unikt scope till resurs grupp

# <a name="json"></a>[JSON](#tab/json)

```json
"[uniqueString(resourceGroup().id)]"
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
uniqueString(resourceGroup().id)
```

---

Unikt scope till distribution för en resurs grupp

# <a name="json"></a>[JSON](#tab/json)

```json
"[uniqueString(resourceGroup().id, deployment().name)]"
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
uniqueString(resourceGroup().id, deployment().name)
```

---

I följande exempel visas hur du skapar ett unikt namn för ett lagrings konto baserat på din resurs grupp. Inuti resurs gruppen är namnet inte unikt om det är konstruerat på samma sätt.

# <a name="json"></a>[JSON](#tab/json)

```json
"resources": [{
  "name": "[concat('storage', uniqueString(resourceGroup().id))]",
  "type": "Microsoft.Storage/storageAccounts",
  ...
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
resource mystorage 'Microsoft.Storage/storageAccounts@@2018-07-01' = {
  name: 'storage${uniqueString(resourceGroup().id)}'
  ...
}
```

---

Om du behöver skapa ett nytt unikt namn varje gången du distribuerar en mall och inte vill uppdatera resursen, kan du använda funktionen [utcNow](template-functions-date.md#utcnow) med uniqueString. Du kan använda den här metoden i en test miljö. Ett exempel finns i [utcNow](template-functions-date.md#utcnow).

### <a name="return-value"></a>Returvärde

En sträng som innehåller 13 tecken.

### <a name="examples"></a>Exempel

Följande [exempel-mall](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/uniquestring.json) returnerar resultat från uniquestring:

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "resources": [],
  "outputs": {
    "uniqueRG": {
      "value": "[uniqueString(resourceGroup().id)]",
      "type": "string"
    },
    "uniqueDeploy": {
      "value": "[uniqueString(resourceGroup().id, deployment().name)]",
      "type": "string"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
output uniqueRG string = uniqueString(resourceGroup().id)
output uniqueDeploy string = uniqueString(resourceGroup().id, deployment().name)
```

---

## <a name="uri"></a>URI

`uri (baseUri, relativeUri)`

Skapar en absolut URI genom att kombinera baseUri-och relativeUri-strängen.

### <a name="parameters"></a>Parametrar

| Parameter | Krävs | Typ | Beskrivning |
|:--- |:--- |:--- |:--- |
| baseUri |Ja |sträng |Bas-URI-sträng. Ta hand om det beteende som rör hanteringen av det avslutande snedstrecket ("/"), enligt beskrivningen i den här tabellen.  |
| relativeUri |Ja |sträng |Den relativa URI-sträng som ska läggas till i bas-URI-strängen. |

* Om **baseUri** slutar på ett avslutande snedstreck är resultatet bara **baseUri** , följt av **relativeUri**.

* Om **baseUri** inte avslutas med ett avslutande snedstreck inträffar ett av två saker.

   * Om **baseUri** inte har några snedstreck alls (förutom från "//" nära fram) är resultatet bara **baseUri** , följt av **relativeUri**.

   * Om **baseUri** har vissa snedstreck, men inte slutar med ett snedstreck, tas allt från det sista snedstrecket bort från **BaseUri** och resultatet är **BaseUri** följt av **relativeUri**.

Här är några exempel:

```
uri('http://contoso.org/firstpath', 'myscript.sh') -> http://contoso.org/myscript.sh
uri('http://contoso.org/firstpath/', 'myscript.sh') -> http://contoso.org/firstpath/myscript.sh
uri('http://contoso.org/firstpath/azuredeploy.json', 'myscript.sh') -> http://contoso.org/firstpath/myscript.sh
uri('http://contoso.org/firstpath/azuredeploy.json/', 'myscript.sh') -> http://contoso.org/firstpath/azuredeploy.json/myscript.sh
```
För fullständig information löses de **baseUri** -och **relativeUri** -parametrarna som anges i [RFC 3986, avsnitt 5](https://tools.ietf.org/html/rfc3986#section-5).

### <a name="return-value"></a>Returvärde

En sträng som representerar den absoluta URI: n för bas-och relativa värden.

### <a name="examples"></a>Exempel

I följande exempel visas hur du skapar en länk till en kapslad mall baserat på värdet för den överordnade mallen.

# <a name="json"></a>[JSON](#tab/json)

```json
"templateLink": "[uri(deployment().properties.templateLink.uri, 'nested/azuredeploy.json')]"
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
templateLink: uri(deployment().properties.templateLink.uri, 'nested/azuredeploy.json')
```

---

I följande [exempel mall](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/uri.json) visas hur du använder URI, UriComponent och uriComponentToString:

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "variables": {
    "uriFormat": "[uri('http://contoso.com/resources/', 'nested/azuredeploy.json')]",
    "uriEncoded": "[uriComponent(variables('uriFormat'))]"
  },
  "resources": [
  ],
  "outputs": {
    "uriOutput": {
      "type": "string",
      "value": "[variables('uriFormat')]"
    },
    "componentOutput": {
      "type": "string",
      "value": "[variables('uriEncoded')]"
    },
    "toStringOutput": {
      "type": "string",
      "value": "[uriComponentToString(variables('uriEncoded'))]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
var uriFormat = uri('http://contoso.com/resources/', 'nested/azuredeploy.json')
var uriEncoded = uriComponent(uriFormat)

output uriOutput string = uriFormat
output componentOutput string = uriEncoded
output toStringOutput string = uriComponentToString(uriEncoded)
```

---

Utdata från föregående exempel med standardvärdena är:

| Namn | Typ | Värde |
| ---- | ---- | ----- |
| uriOutput | Sträng | `http://contoso.com/resources/nested/azuredeploy.json` |
| componentOutput | Sträng | `http%3A%2F%2Fcontoso.com%2Fresources%2Fnested%2Fazuredeploy.json` |
| toStringOutput | Sträng | `http://contoso.com/resources/nested/azuredeploy.json` |

## <a name="uricomponent"></a>uriComponent

`uricomponent(stringToEncode)`

Kodar en URI.

### <a name="parameters"></a>Parametrar

| Parameter | Krävs | Typ | Beskrivning |
|:--- |:--- |:--- |:--- |
| stringToEncode |Ja |sträng |Det värde som ska kodas. |

### <a name="return-value"></a>Returvärde

En sträng med det URI-kodade värdet.

### <a name="examples"></a>Exempel

I följande [exempel mall](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/uri.json) visas hur du använder URI, UriComponent och uriComponentToString:

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "variables": {
    "uriFormat": "[uri('http://contoso.com/resources/', 'nested/azuredeploy.json')]",
    "uriEncoded": "[uriComponent(variables('uriFormat'))]"
  },
  "resources": [
  ],
  "outputs": {
    "uriOutput": {
      "type": "string",
      "value": "[variables('uriFormat')]"
    },
    "componentOutput": {
      "type": "string",
      "value": "[variables('uriEncoded')]"
    },
    "toStringOutput": {
      "type": "string",
      "value": "[uriComponentToString(variables('uriEncoded'))]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
var uriFormat = uri('http://contoso.com/resources/', 'nested/azuredeploy.json')
var uriEncoded = uriComponent(uriFormat)

output uriOutput string = uriFormat
output componentOutput string = uriEncoded
output toStringOutput string = uriComponentToString(uriEncoded)
```

---

Utdata från föregående exempel med standardvärdena är:

| Namn | Typ | Värde |
| ---- | ---- | ----- |
| uriOutput | Sträng | `http://contoso.com/resources/nested/azuredeploy.json` |
| componentOutput | Sträng | `http%3A%2F%2Fcontoso.com%2Fresources%2Fnested%2Fazuredeploy.json` |
| toStringOutput | Sträng | `http://contoso.com/resources/nested/azuredeploy.json` |

## <a name="uricomponenttostring"></a>uriComponentToString

`uriComponentToString(uriEncodedString)`

Returnerar en sträng med ett URI-kodat värde.

### <a name="parameters"></a>Parametrar

| Parameter | Krävs | Typ | Beskrivning |
|:--- |:--- |:--- |:--- |
| uriEncodedString |Ja |sträng |Det URI-kodade värdet som ska konverteras till en sträng. |

### <a name="return-value"></a>Returvärde

En avkodad sträng med URI-kodat värde.

### <a name="examples"></a>Exempel

I följande [exempel mall](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/uri.json) visas hur du använder URI, UriComponent och uriComponentToString:

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "variables": {
    "uriFormat": "[uri('http://contoso.com/resources/', 'nested/azuredeploy.json')]",
    "uriEncoded": "[uriComponent(variables('uriFormat'))]"
  },
  "resources": [
  ],
  "outputs": {
    "uriOutput": {
      "type": "string",
      "value": "[variables('uriFormat')]"
    },
    "componentOutput": {
      "type": "string",
      "value": "[variables('uriEncoded')]"
    },
    "toStringOutput": {
      "type": "string",
      "value": "[uriComponentToString(variables('uriEncoded'))]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
var uriFormat = uri('http://contoso.com/resources/', 'nested/azuredeploy.json')
var uriEncoded = uriComponent(uriFormat)

output uriOutput string = uriFormat
output componentOutput string = uriEncoded
output toStringOutput string = uriComponentToString(uriEncoded)
```

---

Utdata från föregående exempel med standardvärdena är:

| Namn | Typ | Värde |
| ---- | ---- | ----- |
| uriOutput | Sträng | `http://contoso.com/resources/nested/azuredeploy.json` |
| componentOutput | Sträng | `http%3A%2F%2Fcontoso.com%2Fresources%2Fnested%2Fazuredeploy.json` |
| toStringOutput | Sträng | `http://contoso.com/resources/nested/azuredeploy.json` |

## <a name="next-steps"></a>Nästa steg

* En beskrivning av avsnitten i en ARM-mall finns i [förstå strukturen och syntaxen för ARM-mallar](template-syntax.md).
* Information om hur du sammanfogar flera mallar finns i [använda länkade och kapslade mallar när du distribuerar Azure-resurser](linked-templates.md).
* Om du vill iterera ett visst antal gånger när du skapar en typ av resurs, se [resurs upprepning i arm-mallar](copy-resources.md).
* Information om hur du distribuerar mallen som du har skapat finns i [distribuera resurser med ARM-mallar och Azure PowerShell](deploy-powershell.md).
