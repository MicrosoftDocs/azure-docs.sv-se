---
title: Jämför syntaxen för Azure Resource Manager mallar i JSON och bicep
description: Jämför Azure Resource Manager mallar som har utvecklats med JSON och bicep och visar hur du konverterar mellan språken.
ms.topic: conceptual
ms.date: 03/12/2021
ms.openlocfilehash: 85f85e66e69eede68bab847e4bc68514e65115eb
ms.sourcegitcommit: df1930c9fa3d8f6592f812c42ec611043e817b3b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/13/2021
ms.locfileid: "103418053"
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

## <a name="next-steps"></a>Nästa steg

* Information om bicep finns i [själv studie kursen om bicep](./bicep-tutorial-create-first-bicep.md).
* Mer information om hur du konverterar mallar mellan språken finns i [konvertera arm-mallar mellan JSON och bicep](bicep-decompile.md).
