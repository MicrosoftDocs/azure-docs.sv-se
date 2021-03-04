---
title: Jämför syntaxen för Azure Resource Manager mallar i JSON och bicep
description: Jämför Azure Resource Manager mallar som har utvecklats med JSON och bicep och visar hur du konverterar mellan språken.
ms.topic: conceptual
ms.date: 03/03/2021
ms.openlocfilehash: 29c2b9948957ebc10a26f22f0fe3daf383dfe5ba
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/04/2021
ms.locfileid: "102036222"
---
# <a name="comparing-json-and-bicep-for-templates"></a>Jämför JSON-och bicep för mallar

I den här artikeln jämförs bicep-syntax med JSON-syntax för Azure Resource Manager mallar (ARM-mallar). I de flesta fall ger bicep syntax som är mindre utförlig än motsvarande i JSON.

## <a name="syntax-equivalents"></a>Motsvarigheter i syntax

Om du är van att använda JSON för att utveckla ARM-mallar kan du använda följande tabell för att lära dig om motsvarande syntax för bicep.

| Scenario | Bicep | JSON |
| -------- | ------------ | ----- |
| Redigera ett uttryck | `func()` | `"[func()]"` |
| Hämta parameter värde | `exampleParameter` | `[parameters('exampleParameter'))]` |
| Hämta variabel värde | `exampleVar` | `[variables('exampleVar'))]` |
| Sammanfoga strängar | `'${namePrefix}-vm'` | `[concat(parameters('namePrefix'), '-vm')]` |
| Ange resurs egenskap | `sku: '2016-Datacenter'` | `"sku": "2016-Datacenter",` |
| Returnera det logiska och | `isMonday && isNovember` | `[and(parameter('isMonday'), parameter('isNovember'))]` |
| Hämta resurs-ID för resursen i mallen | `nic1.id` | `[resourceId('Microsoft.Network/networkInterfaces', variables('nic1Name'))]` |
| Hämta egenskap från en resurs i mallen | `diagsAccount.properties.primaryEndpoints.blob` | `[reference(resourceId('Microsoft.Storage/storageAccounts', variables('diagStorageAccountName'))).primaryEndpoints.blob]` |
| Ange ett värde enligt villkor | `isMonday ? 'valueIfTrue' : 'valueIfFalse'` | `[if(parameters('isMonday'), 'valueIfTrue', 'valueIfFalse')]` |
| Separera en lösning i flera filer | Använda moduler | Använd länkade mallar |
| Ange mål omfånget för distributionen | `targetScope = 'subscription'` | `"$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#"` |
| Ange beroende | Förlita dig antingen på automatisk identifiering av beroenden eller Ställ in beroende manuellt med `dependsOn: [ stg ]` | `"dependsOn": ["[resourceId('Microsoft.Storage/storageAccounts', 'parameters('storageAccountName'))]"]` |
| Resurs deklaration | `resource vm 'Microsoft.Compute/virtualMachines@2020-06-01' = {...}` | `"resources": [ { "type": "Microsoft.Compute/virtualMachines", "apiVersion": "2020-06-01", ... } ]` |

## <a name="recommendations"></a>Rekommendationer

* Undvik att använda [referens](template-functions-resource.md#reference) -och [resourceId](template-functions-resource.md#resourceid) -funktionerna i bicep-filen när det är möjligt. När du refererar till en resurs i samma bicep-distribution använder du resurs identifieraren i stället. Om du till exempel har distribuerat en resurs i din bicep-fil med `stg` som resurs-ID, använder du syntax som `stg.id` eller `stg.properties.primaryEndpoints.blob` för att hämta egenskaps värden. Med hjälp av resurs identifieraren kan du skapa ett implicit beroende mellan resurserna. Du behöver inte uttryckligen ange beroendet med egenskapen dependsOn.
* Använd konsekvent Skift läge för identifierare. Om du är osäker på vilken typ av Skift läge som ska användas kan du prova kamel notation Skift läge. Till exempel `param myCamelCasedParameter string`.
* Lägg endast till en beskrivning till en parameter när beskrivningen ger nödvändig information till användarna. Du kan använda `//` kommentarer för viss information.

## <a name="decompile-json-to-bicep"></a>Dekompilera JSON till bicep

Bicep CLI innehåller ett kommando för att dekompilera alla befintliga ARM-mallar till en bicep-fil. Om du vill dekompilera en JSON-fil använder du: `bicep decompile "path/to/file.json"`

Det här kommandot ger en start punkt för bicep redigering, men kommandot fungerar inte för alla mallar. Kommandot kan Miss lyckas eller så kan du behöva åtgärda problem efter dekompileringen. För närvarande kan kapslade mallar bara dekompileras om de använder utvärderings omfattningen "internt".

Du kan exportera mallen för en resurs grupp och sedan skicka den direkt till bicep Decompile-kommandot. I följande exempel visas hur du dekompilerar en exporterad mall.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az group export --name "your_resource_group_name" > main.json
bicep decompile main.json
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
Export-AzResourceGroup -ResourceGroupName "your_resource_group_name" -Path ./main.json
bicep decompile main.json
```

# <a name="portal"></a>[Portal](#tab/azure-portal)

[Exportera mallen](export-template-portal.md) via portalen. Används `bicep decompile <filename>` i den nedladdade filen.

---

## <a name="build-json-from-bicep"></a>Bygga JSON från bicep

Bicep CLI innehåller också ett kommando för att konvertera bicep till JSON. Använd följande för att bygga en JSON-fil: `bicep build "path/to/file.json"`

## <a name="side-by-side-view"></a>Sida-vid-sida-vy

Med [bicep-Playground](https://aka.ms/bicepdemo) kan du Visa motsvarande JSON-och bicep-filer sida vid sida. Du kan välja en exempel mall för att se båda versionerna. `Decompile`Du kan också välja att ladda upp din egen JSON-mall och Visa motsvarande bicep-fil.

## <a name="next-steps"></a>Nästa steg

Information om bicep finns i [själv studie kursen om bicep](./bicep-tutorial-create-first-bicep.md).
