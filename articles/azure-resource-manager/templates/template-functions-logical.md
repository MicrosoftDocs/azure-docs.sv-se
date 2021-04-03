---
title: Template Functions – Logical
description: Beskriver de funktioner som används i en Azure Resource Manager mall (ARM-mall) för att fastställa logiska värden.
ms.topic: conceptual
ms.date: 11/18/2020
ms.openlocfilehash: 27d94f10374daf0b9a351469579a5eb659cf5445
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96920486"
---
# <a name="logical-functions-for-arm-templates"></a>Logiska funktioner för ARM-mallar

Resource Manager innehåller flera funktioner för att göra jämförelser i din Azure Resource Manager-mall (ARM-mall):

* [and](#and)
* [boolesk](#bool)
* [!](#false)
* [eventuella](#if)
* [Ogiltigt](#not)
* [eller](#or)
* [värdet](#true)

[!INCLUDE [Bicep preview](../../../includes/resource-manager-bicep-preview.md)]

## <a name="and"></a>och

`and(arg1, arg2, ...)`

Kontrollerar om alla parameter värden är sanna. `and`Funktionen stöds inte i bicep. Använd `&&` operatorn i stället.

### <a name="parameters"></a>Parametrar

| Parameter | Krävs | Typ | Beskrivning |
|:--- |:--- |:--- |:--- |
| arg1 |Ja |boolean |Det första värdet för att kontrol lera om är sant. |
| arg2 |Ja |boolean |Det andra värdet för att kontrol lera om är sant. |
| ytterligare argument |Inga |boolean |Ytterligare argument för att kontrol lera om är true. |

### <a name="return-value"></a>Returvärde

Returnerar **True** om alla värden är true; annars **false**.

### <a name="examples"></a>Exempel

I följande [exempel mall](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/andornot.json) visas hur du använder logiska funktioner.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "resources": [],
  "outputs": {
    "andExampleOutput": {
      "type": "bool",
      "value": "[and(bool('true'), bool('false'))]"
    },
    "orExampleOutput": {
      "type": "bool",
      "value": "[or(bool('true'), bool('false'))]"
    },
    "notExampleOutput": {
      "type": "bool",
      "value": "[not(bool('true'))]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
output andExampleOutput bool = bool('true') && bool('false')
output orExampleOutput bool = bool('true') || bool('false')
output notExampleOutput bool = !(bool('true'))
```

---

Utdata från föregående exempel är:

| Namn | Typ | Värde |
| ---- | ---- | ----- |
| andExampleOutput | Bool | Falskt |
| orExampleOutput | Bool | Sant |
| notExampleOutput | Bool | Falskt |

## <a name="bool"></a>boolesk

`bool(arg1)`

Konverterar parametern till ett booleskt värde.

### <a name="parameters"></a>Parametrar

| Parameter | Krävs | Typ | Beskrivning |
|:--- |:--- |:--- |:--- |
| arg1 |Ja |sträng eller heltal |Värdet som ska konverteras till ett booleskt värde. |

### <a name="return-value"></a>Returvärde

Ett booleskt värde för det konverterade värdet.

### <a name="remarks"></a>Kommentarer

Du kan också använda [sant ()](#true) och [falskt ()](#false) för att hämta booleska värden.

### <a name="examples"></a>Exempel

I följande [exempel mall](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/bool.json) visas hur du använder bool med en sträng eller ett heltal.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "resources": [],
  "outputs": {
    "trueString": {
      "type": "bool",
      "value": "[bool('true')]",
    },
    "falseString": {
      "type": "bool",
      "value": "[bool('false')]"
    },
    "trueInt": {
      "type": "bool",
      "value": "[bool(1)]"
    },
    "falseInt": {
      "type": "bool",
      "value": "[bool(0)]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
output trueString bool = bool('true')
output falseString bool = bool('false')
output trueInt bool = bool(1)
output falseInt bool = bool(0)
```

---
Utdata från föregående exempel med standardvärdena är:

| Namn | Typ | Värde |
| ---- | ---- | ----- |
| trueString | Bool | Sant |
| falseString | Bool | Falskt |
| trueInt | Bool | Sant |
| falseInt | Bool | Falskt |

## <a name="false"></a>falskt

`false()`

Returnerar false. `false`Funktionen är inte tillgänglig i bicep.  Använd `false` nyckelordet i stället.

### <a name="parameters"></a>Parametrar

Funktionen false accepterar inte några parametrar.

### <a name="return-value"></a>Returvärde

Ett booleskt värde som alltid är falskt.

### <a name="example"></a>Exempel

I följande exempel returneras ett falskt utdata-värde.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "resources": [],
  "outputs": {
    "falseOutput": {
      "type": "bool",
      "value": "[false()]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
output falseOutput bool = false
```

---

Utdata från föregående exempel är:

| Namn | Typ | Värde |
| ---- | ---- | ----- |
| falseOutput | Bool | Falskt |

## <a name="if"></a>om

`if(condition, trueValue, falseValue)`

Returnerar ett värde baserat på om ett villkor är sant eller falskt. `if`Funktionen stöds inte i bicep. Använd `?:` operatorn i stället.

### <a name="parameters"></a>Parametrar

| Parameter | Krävs | Typ | Beskrivning |
|:--- |:--- |:--- |:--- |
| moduletype |Ja |boolean |Värdet för att kontrol lera om det är sant eller falskt. |
| trueValue |Ja | sträng, heltal, objekt eller matris |Värdet som ska returneras när villkoret är sant. |
| falseValue |Ja | sträng, heltal, objekt eller matris |Värdet som ska returneras när villkoret är falskt. |

### <a name="return-value"></a>Returvärde

Returnerar den andra parametern när den första parametern är **True**; annars returnerar den tredje parametern.

### <a name="remarks"></a>Kommentarer

När villkoret är **Sant** utvärderas bara det sanna värdet. Om villkoret är **falskt** utvärderas bara det falska värdet. Med `if` -funktionen kan du inkludera uttryck som endast är villkorligt giltiga. Du kan till exempel referera till en resurs som finns under ett villkor, men inte med det andra villkoret. Ett exempel på villkorligt utvärdering av uttryck visas i följande avsnitt.

### <a name="examples"></a>Exempel

I följande [exempel mall](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/if.json) visas hur du använder `if` funktionen.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "resources": [
  ],
  "outputs": {
    "yesOutput": {
      "type": "string",
      "value": "[if(equals('a', 'a'), 'yes', 'no')]"
    },
    "noOutput": {
      "type": "string",
      "value": "[if(equals('a', 'b'), 'yes', 'no')]"
    },
    "objectOutput": {
      "type": "object",
      "value": "[if(equals('a', 'a'), json('{\"test\": \"value1\"}'), json('null'))]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
output yesOutput string = 'a' == 'a' ? 'yes' : 'no'
output noOutput string = 'a' == 'b' ? 'yes' : 'no'
output objectOutput object = 'a' == 'a' ? json('{"test": "value1"}') : json('null')
```

---

Utdata från föregående exempel är:

| Namn | Typ | Värde |
| ---- | ---- | ----- |
| yesOutput | Sträng | ja |
| nooutput | Sträng | nej |
| objectOutput | Objekt | {"test": "värde1"} |

I följande [exempel mall](https://github.com/krnese/AzureDeploy/blob/master/ARM/deployments/conditionWithReference.json) visas hur du använder den här funktionen med uttryck som endast är villkorligt giltiga.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "vmName": {
      "type": "string"
    },
    "location": {
      "type": "string"
    },
    "logAnalytics": {
      "type": "string",
      "defaultValue": ""
    }
  },
  "resources": [
    {
      "condition": "[not(empty(parameters('logAnalytics')))]",
      "type": "Microsoft.Compute/virtualMachines/extensions",
      "apiVersion": "2017-03-30",
      "name": "[concat(parameters('vmName'),'/omsOnboarding')]",
      "location": "[parameters('location')]",
      "properties": {
        "publisher": "Microsoft.EnterpriseCloud.Monitoring",
        "type": "MicrosoftMonitoringAgent",
        "typeHandlerVersion": "1.0",
        "autoUpgradeMinorVersion": true,
        "settings": {
          "workspaceId": "[if(not(empty(parameters('logAnalytics'))), reference(parameters('logAnalytics'), '2015-11-01-preview').customerId, json('null'))]"
        },
        "protectedSettings": {
          "workspaceKey": "[if(not(empty(parameters('logAnalytics'))), listKeys(parameters('logAnalytics'), '2015-11-01-preview').primarySharedKey, json('null'))]"
        }
      }
    }
  ],
  "outputs": {
    "mgmtStatus": {
      "type": "string",
      "value": "[if(not(empty(parameters('logAnalytics'))), 'Enabled monitoring for VM!', 'Nothing to enable')]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

> [!NOTE]
> `Conditions` har ännu inte implementerats i bicep. Se [villkor](https://github.com/Azure/bicep/issues/186).

---

## <a name="not"></a>inte

`not(arg1)`

Konverterar booleskt värde till motsatt värde. `not`Funktionen stöds inte i bicep. Använd `!` operatorn i stället.

### <a name="parameters"></a>Parametrar

| Parameter | Krävs | Typ | Beskrivning |
|:--- |:--- |:--- |:--- |
| arg1 |Ja |boolean |Det värde som ska konverteras. |

### <a name="return-value"></a>Returvärde

Returnerar **True** när parametern är **false**. Returnerar **false** när parametern är **True**.

### <a name="examples"></a>Exempel

I följande [exempel mall](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/andornot.json) visas hur du använder logiska funktioner.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "resources": [],
  "outputs": {
    "andExampleOutput": {
      "type": "bool",
      "value": "[and(bool('true'), bool('false'))]",
    },
    "orExampleOutput": {
      "type": "bool",
      "value": "[or(bool('true'), bool('false'))]"
    },
    "notExampleOutput": {
      "type": "bool",
      "value": "[not(bool('true'))]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
output andExampleOutput bool = bool('true') && bool('false')
output orExampleOutput bool = bool('true') || bool('false')
output notExampleOutput bool = !(bool('true'))
```

---

Utdata från föregående exempel är:

| Namn | Typ | Värde |
| ---- | ---- | ----- |
| andExampleOutput | Bool | Falskt |
| orExampleOutput | Bool | Sant |
| notExampleOutput | Bool | Falskt |

Följande [exempel-mall](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/not-equals.json) använder **inte** [lika](template-functions-comparison.md#equals)med.

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
output checkNotEquals bool = !(1 == 2)
```

---

Utdata från föregående exempel är:

| Namn | Typ | Värde |
| ---- | ---- | ----- |
| checkNotEquals | Bool | Sant |

## <a name="or"></a>eller

`or(arg1, arg2, ...)`

Kontrollerar om ett parameter värde är sant. `or`Funktionen stöds inte i bicep. Använd `||` operatorn i stället.

### <a name="parameters"></a>Parametrar

| Parameter | Krävs | Typ | Beskrivning |
|:--- |:--- |:--- |:--- |
| arg1 |Ja |boolean |Det första värdet för att kontrol lera om är sant. |
| arg2 |Ja |boolean |Det andra värdet för att kontrol lera om är sant. |
| ytterligare argument |Inga |boolean |Ytterligare argument för att kontrol lera om är true. |

### <a name="return-value"></a>Returvärde

Returnerar **Sant** om något värde är sant; annars **false**.

### <a name="examples"></a>Exempel

I följande [exempel mall](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/andornot.json) visas hur du använder logiska funktioner.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "resources": [],
  "outputs": {
    "andExampleOutput": {
      "value": "[and(bool('true'), bool('false'))]",
      "type": "bool"
    },
    "orExampleOutput": {
      "value": "[or(bool('true'), bool('false'))]",
      "type": "bool"
    },
    "notExampleOutput": {
      "value": "[not(bool('true'))]",
      "type": "bool"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
output andExampleOutput bool = bool('true') && bool('false')
output orExampleOutput bool = bool('true') || bool('false')
output notExampleOutput bool = !(bool('true'))
```

---

Utdata från föregående exempel är:

| Namn | Typ | Värde |
| ---- | ---- | ----- |
| andExampleOutput | Bool | Falskt |
| orExampleOutput | Bool | Sant |
| notExampleOutput | Bool | Falskt |

## <a name="true"></a>true

`true()`

Returnerar true. `true`Funktionen är inte tillgänglig i bicep.  Använd `true` nyckelordet i stället.

### <a name="parameters"></a>Parametrar

Funktionen True accepterar inte några parametrar. `true`Funktionen är inte tillgänglig i bicep.  Använd `true` nyckelordet i stället.

### <a name="return-value"></a>Returvärde

Ett booleskt värde som alltid är sant.

### <a name="example"></a>Exempel

I följande exempel returneras ett sant utdata-värde.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "resources": [],
  "outputs": {
    "trueOutput": {
      "type": "bool",
      "value": "[true()]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
output trueOutput bool = true
```

---

Utdata från föregående exempel är:

| Namn | Typ | Värde |
| ---- | ---- | ----- |
| trueOutput | Bool | Sant |

## <a name="next-steps"></a>Nästa steg

* En beskrivning av avsnitten i en ARM-mall finns i [förstå strukturen och syntaxen för ARM-mallar](template-syntax.md).
