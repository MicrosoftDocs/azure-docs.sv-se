---
title: Jämför syntaxen för Azure Resource Manager mallar i JSON och bicep
description: Jämför Azure Resource Manager mallar som har utvecklats med JSON och bicep och visar hur du konverterar mellan språken.
ms.topic: conceptual
ms.date: 03/12/2021
ms.openlocfilehash: 225e52e9534a77a01502b762f043a4f34df19caa
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "103461802"
---
# <a name="comparing-json-and-bicep-for-templates"></a>Jämför JSON-och bicep för mallar

I den här artikeln jämförs bicep-syntax med JSON-syntax för Azure Resource Manager mallar (ARM-mallar). I de flesta fall ger bicep syntax som är mindre utförlig än motsvarande i JSON.

Om du är van att använda JSON för att utveckla ARM-mallar kan du använda följande exempel för att lära dig om motsvarande syntax för bicep.

## <a name="expressions"></a>Uttryck

Så här skapar du ett uttryck:

```bicep
func()
```

```json
"[func()]"
```

## <a name="parameters"></a>Parametrar

Deklarera en parameter med ett standardvärde:

```bicep
param demoParam string = 'Contoso'
```

```json
"parameters": {
  "demoParam": {
    "type": "string",
    "defaultValue": "Contoso"
  }
}
```

Så här hämtar du ett parameter värde:

```bicep
demoParam
```

```json
[parameters('demoParam'))]
```

## <a name="variables"></a>Variabler

Deklarera en variabel:

```bicep
var demoVar = 'example value'
```

```json
"variables": {
  "demoVar": "example value"
},
```

Så här hämtar du ett variabel värde:

```bicep
demoVar
```

```json
[variables('demoVar'))]
```

## <a name="strings"></a>Strängar

Så här sammanfogar du strängar:

```bicep
'${namePrefix}-vm'
```

```json
[concat(parameters('namePrefix'), '-vm')]
```

## <a name="logical-operators"></a>Logiska operatorer

Så här returnerar du det logiska **och**:

```bicep
isMonday && isNovember
```

```json
[and(parameter('isMonday'), parameter('isNovember'))]
```

Ange ett värde enligt villkor:

```bicep
isMonday ? 'valueIfTrue' : 'valueIfFalse'
```

```json
[if(parameters('isMonday'), 'valueIfTrue', 'valueIfFalse')]
```

## <a name="deployment-scope"></a>Distributions omfång

Ange mål omfånget för distributionen:

```bicep
targetScope = 'subscription'
```

```json
"$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#"
```

## <a name="resources"></a>Resurser

Så här deklarerar du en resurs:

```bicep
resource vm 'Microsoft.Compute/virtualMachines@2020-06-01' = {
  ...
}
```

```json
"resources": [ 
  { 
    "type": "Microsoft.Compute/virtualMachines", 
    "apiVersion": "2020-06-01", 
    ... 
  } 
]
```

Så här distribuerar du en resurs villkorligt:

```bicep
resource vm 'Microsoft.Compute/virtualMachines@2020-06-01' = if(deployVM) {
  ...
}
```

```json
"resources": [ 
  {
    "condition": "[parameters('deployVM')]",
    "type": "Microsoft.Compute/virtualMachines", 
    "apiVersion": "2020-06-01", 
    ... 
  } 
]
```

Ange resurs egenskap:

```bicep
sku: '2016-Datacenter'
```

```json
"sku": "2016-Datacenter",
```

Så här hämtar du resurs-ID för resursen i mallen:

```bicep
nic1.id
```

```json
[resourceId('Microsoft.Network/networkInterfaces', variables('nic1Name'))]
```

## <a name="loops"></a>Loopar

Iterera över objekt i en matris eller antal:

```bicep
[for storageName in storageAccounts: {
  ...
}]
```

```json
"copy": {
  "name": "storagecopy",
  "count": "[length(parameters('storageAccounts'))]"
},
...
```

## <a name="resource-dependencies"></a>Resursberoenden

Så här ställer du in beroende mellan resurser:

För bicep måste du antingen vara beroende av automatisk identifiering av beroenden eller manuellt ange beroende.

```bicep
dependsOn: [ stg ]
```

```json
"dependsOn": ["[resourceId('Microsoft.Storage/storageAccounts', 'parameters('storageAccountName'))]"]
```

## <a name="reference-resources"></a>Referens resurser

Så här hämtar du en egenskap från en resurs i mallen:

```bicep
diagsAccount.properties.primaryEndpoints.blob
```

```json
[reference(resourceId('Microsoft.Storage/storageAccounts', variables('diagStorageAccountName'))).primaryEndpoints.blob]
```

Så här hämtar du en egenskap från en befintlig resurs som inte har distribuerats i mallen:

```bicep
resource stg 'Microsoft.Storage/storageAccounts@2019-06-01' existing = {
  name: storageAccountName
}

// use later in template as often as needed
stg.properties.primaryEndpoints.blob
```

```json
// required every time the property is needed
"[reference(resourceId('Microsoft.Storage/storageAccounts/', parameters('storageAccountName')), '2019-06-01').primaryEndpoints.blob]"
```

## <a name="outputs"></a>Utdata

Så här matar du ut en egenskap från en resurs i mallen:

```bicep
output hostname string = publicIP.properties.dnsSettings.fqdn
```

```json
"outputs": {
  "hostname": {
    "type": "string",
    "value": "[reference(resourceId('Microsoft.Network/publicIPAddresses', variables('publicIPAddressName'))).dnsSettings.fqdn]"
  },
}
```

## <a name="code-reuse"></a>Åter användning av kod

Separera en lösning till flera filer:

* Använd [moduler](bicep-tutorial-add-modules.md)för bicep.
* Använd [länkade mallar](linked-templates.md)för JSON.

## <a name="recommendations"></a>Rekommendationer

* Undvik att använda [referens](template-functions-resource.md#reference) -och [resourceId](template-functions-resource.md#resourceid) -funktionerna i bicep-filen när det är möjligt. När du refererar till en resurs i samma bicep-distribution använder du resurs identifieraren i stället. Om du till exempel har distribuerat en resurs i din bicep-fil med `stg` som resurs-ID, använder du syntax som `stg.id` eller `stg.properties.primaryEndpoints.blob` för att hämta egenskaps värden. Med hjälp av resurs identifieraren kan du skapa ett implicit beroende mellan resurserna. Du behöver inte uttryckligen ange beroendet med egenskapen dependsOn.
* Om resursen inte har distribuerats i bicep-filen kan du fortfarande få en symbolisk referens till resursen med hjälp av det **befintliga** nyckelordet.
* Använd konsekvent Skift läge för identifierare. Om du är osäker på vilken typ av Skift läge som ska användas kan du prova kamel notation Skift läge. Till exempel `param myCamelCasedParameter string`.
* Lägg endast till en beskrivning till en parameter när beskrivningen ger nödvändig information till användarna. Du kan använda `//` kommentarer för viss information.

## <a name="next-steps"></a>Nästa steg

* Information om bicep finns i [själv studie kursen om bicep](./bicep-tutorial-create-first-bicep.md).
* Mer information om hur du konverterar mallar mellan språken finns i [konvertera arm-mallar mellan JSON och bicep](bicep-decompile.md).
