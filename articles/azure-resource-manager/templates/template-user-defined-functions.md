---
title: Användardefinierade funktioner i mallar
description: Beskriver hur du definierar och använder användardefinierade funktioner i en Azure Resource Manager-mall (ARM-mall).
ms.topic: conceptual
ms.date: 03/09/2020
ms.openlocfilehash: f428fa3bc827af3820ad9f928f4f92b881c9c84c
ms.sourcegitcommit: 2aa52d30e7b733616d6d92633436e499fbe8b069
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/06/2021
ms.locfileid: "97934687"
---
# <a name="user-defined-functions-in-arm-template"></a>Användardefinierade funktioner i ARM-mallen

I mallen kan du skapa egna funktioner. Dessa funktioner är tillgängliga för användning i din mall. Användardefinierade funktioner är separata från de [standardmallar](template-functions.md) som är tillgängliga automatiskt i din mall. Skapa egna funktioner när du har komplicerade uttryck som används upprepade gånger i din mall.

Den här artikeln beskriver hur du lägger till användardefinierade funktioner i din Azure Resource Manager-mall (ARM-mall).

## <a name="define-the-function"></a>Definiera funktionen

Dina funktioner kräver ett namn områdes värde för att undvika namngivnings konflikter med Template functions. I följande exempel visas en funktion som returnerar ett unikt namn:

```json
"functions": [
  {
    "namespace": "contoso",
    "members": {
      "uniqueName": {
        "parameters": [
          {
            "name": "namePrefix",
            "type": "string"
          }
        ],
        "output": {
          "type": "string",
          "value": "[concat(toLower(parameters('namePrefix')), uniqueString(resourceGroup().id))]"
        }
      }
    }
  }
],
```

## <a name="use-the-function"></a>Använd funktionen

I följande exempel visas en mall som innehåller en användardefinierad funktion. Funktionen används för att hämta ett unikt namn för ett lagrings konto. Mallen har en parameter som heter `storageNamePrefix` att den skickas som en parameter till funktionen.

```json
{
 "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
 "contentVersion": "1.0.0.0",
 "parameters": {
   "storageNamePrefix": {
     "type": "string",
     "maxLength": 11
   }
 },
 "functions": [
  {
    "namespace": "contoso",
    "members": {
      "uniqueName": {
        "parameters": [
          {
            "name": "namePrefix",
            "type": "string"
          }
        ],
        "output": {
          "type": "string",
          "value": "[concat(toLower(parameters('namePrefix')), uniqueString(resourceGroup().id))]"
        }
      }
    }
  }
],
 "resources": [
   {
     "type": "Microsoft.Storage/storageAccounts",
     "apiVersion": "2019-04-01",
     "name": "[contoso.uniqueName(parameters('storageNamePrefix'))]",
     "location": "South Central US",
     "sku": {
       "name": "Standard_LRS"
     },
     "kind": "StorageV2",
     "properties": {
       "supportsHttpsTrafficOnly": true
     }
   }
 ]
}
```

## <a name="limitations"></a>Begränsningar

När du definierar en användar funktion finns det vissa begränsningar:

* Funktionen kan inte komma åt variabler.
* Funktionen kan bara använda parametrar som har definierats i funktionen. När du använder funktionen [parametrar](template-functions-deployment.md#parameters) i en användardefinierad funktion är du begränsad till parametrarna för den funktionen.
* Funktionen kan inte anropa andra användardefinierade funktioner.
* Funktionen kan inte använda [referens](template-functions-resource.md#reference) funktionen eller någon av [list](template-functions-resource.md#list) funktionerna.
* Parametrar för funktionen kan inte ha standardvärden.

## <a name="next-steps"></a>Nästa steg

* Mer information om tillgängliga egenskaper för användardefinierade funktioner finns i [förstå strukturen och syntaxen för ARM-mallar](template-syntax.md).
* En lista över tillgängliga mallar finns i [funktioner i arm-mallar](template-functions.md).
