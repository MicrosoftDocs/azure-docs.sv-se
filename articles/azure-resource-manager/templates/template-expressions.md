---
title: Mallens syntax och uttryck
description: Beskriver deklarativ JSON-syntax för Azure Resource Manager mallar (ARM-mallar).
ms.topic: conceptual
ms.date: 03/17/2020
ms.openlocfilehash: 44a386ed849771dfba717c8d1414e64422d0c7bd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "97797051"
---
# <a name="syntax-and-expressions-in-arm-templates"></a>Syntax och uttryck i ARM-mallar

Den grundläggande syntaxen för Azure Resource Manager-mallen (ARM-mallen) är JavaScript Object Notation (JSON). Du kan dock använda uttryck för att utöka de JSON-värden som är tillgängliga i mallen.  Uttryck börjar och slutar med hakparenteser: `[` respektive `]`. Uttryckets värde utvärderas när mallen distribueras. Ett uttryck kan returnera en sträng, ett heltal, ett booleskt värde, en matris eller ett objekt.

Ett mall uttryck får inte överstiga 24 576 tecken.

## <a name="use-functions"></a>Använda funktioner

Azure Resource Manager innehåller [funktioner](template-functions.md) som du kan använda i en mall. I följande exempel visas ett uttryck som använder en funktion i standardvärdet för en parameter:

```json
"parameters": {
  "location": {
    "type": "string",
    "defaultValue": "[resourceGroup().location]"
  }
},
```

I uttrycket anropar syntaxen `resourceGroup()` en av de funktioner som Resource Manager tillhandahåller för användning i en mall. I det här fallet är det [resourceGroup](template-functions-resource.md#resourcegroup) -funktionen. Precis som i Java Script är funktions anrop formaterade som `functionName(arg1,arg2,arg3)` . Syntaxen `.location` hämtar en egenskap från det objekt som returnerades av funktionen.

Mallens funktioner och deras parametrar är Skift läges känsliga. Resource Manager löses till exempel `variables('var1')` och `VARIABLES('VAR1')` är detsamma. Vid bedömning, om inte funktionen uttryckligen ändrar Skift läge (t. ex. `toUpper` eller `toLower` ), bevarar funktionen det fallet. Vissa resurs typer kan ha fall krav som är skilda från hur funktionerna utvärderas.

Om du vill skicka ett sträng värde som en parameter till en funktion använder du enkla citat tecken.

```json
"name": "[concat('storage', uniqueString(resourceGroup().id))]"
```

De flesta funktioner fungerar på samma sätt som när de distribueras till en resurs grupp, prenumeration, hanterings grupp eller klient organisation. Följande funktioner har begränsningar baserat på omfånget:

* [resourceGroup](template-functions-resource.md#resourcegroup) -kan bara användas i distributioner till en resurs grupp.
* [resourceId](template-functions-resource.md#resourceid) -kan användas i valfri omfattning, men giltiga parametrar ändras beroende på omfattningen.
* [prenumeration](template-functions-resource.md#subscription) – kan bara användas i distributioner till en resurs grupp eller prenumeration.

## <a name="escape-characters"></a>Escape-tecken

Om du vill att en litteral sträng ska börja med en vänster hak paren tes `[` och sluta med en högerparentes `]` , men inte ha den tolkas som ett uttryck, lägger du till en extra parentes för att starta strängen med `[[` . Till exempel variabeln:

```json
"demoVar1": "[[test value]"
```

Matchar `[test value]` .

Om den litterala strängen inte slutar med en hak paren tes, ska du inte undanta den första parentesen. Till exempel variabeln:

```json
"demoVar2": "[test] value"
```

Matchar `[test] value` .

Om du vill undanta dubbla citat tecken i ett uttryck, till exempel lägga till ett JSON-objekt i mallen, använder du omvänt snedstreck.

```json
"tags": {
    "CostCenter": "{\"Dept\":\"Finance\",\"Environment\":\"Production\"}"
},
```

När du skickar parameter värden beror användningen av escape-tecken på var parametervärdet har angetts. Om du anger ett standardvärde i mallen behöver du den extra vänster hak paren tes.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "demoParam1":{
            "type": "string",
            "defaultValue": "[[test value]"
        }
    },
    "resources": [],
    "outputs": {
        "exampleOutput": {
            "type": "string",
            "value": "[parameters('demoParam1')]"
        }
    }
}
```

Om du använder standardvärdet returneras mallen `[test value]` .

Men om du skickar ett parameter värde via kommando raden tolkas tecknen bokstavligen. Distribuera den tidigare mallen med:

```azurepowershell
New-AzResourceGroupDeployment -ResourceGroupName demoGroup -TemplateFile azuredeploy.json -demoParam1 "[[test value]"
```

Returnerar `[[test value]` . Använd i stället:

```azurepowershell
New-AzResourceGroupDeployment -ResourceGroupName demoGroup -TemplateFile azuredeploy.json -demoParam1 "[test value]"
```

Samma formatering gäller när du skickar värden i från en parameter fil. Tecknen tolkas bokstavligen. När den används med föregående mall returnerar följande parameter fil `[test value]` :

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "demoParam1": {
            "value": "[test value]"
        }
   }
}
```

## <a name="null-values"></a>Null-värden

Om du vill ange en egenskap till null kan du använda `null` eller `[json('null')]`. [JSON-funktionen](template-functions-object.md#json) returnerar ett tomt objekt när du anger `null` som parameter. I båda fallen behandlar Resource Manager-mallar den som om egenskapen inte finns.

```json
"stringValue": null,
"objectValue": "[json('null')]"
```

## <a name="next-steps"></a>Nästa steg

* En fullständig lista över mallar finns i funktioner i [arm-mallar](template-functions.md).
* Mer information om mallfiler finns i [förstå strukturen och syntaxen för ARM-mallar](template-syntax.md).
