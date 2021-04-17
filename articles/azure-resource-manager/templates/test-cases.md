---
title: Testfall för testverktyg
description: Beskriver de tester som körs av ARM-mallens testverktyg.
ms.topic: conceptual
ms.date: 04/12/2021
ms.author: tomfitz
author: tfitzmac
ms.openlocfilehash: 7805d6dbdb8b93968a2792ed6dfaf2ac8fea9ae5
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2021
ms.locfileid: "107363401"
---
# <a name="default-test-cases-for-arm-template-test-toolkit"></a>Standardtestfall för ARM-malltestverktyg

I den här artikeln beskrivs de standardtester som körs med [malltestverktyget](test-toolkit.md) för att Azure Resource Manager mallar (ARM-mallar). Den innehåller exempel som klarar eller misslyckas med testet. Den innehåller namnet på varje test. Information om hur du kör ett specifikt test finns [i Testparametrar](test-toolkit.md#test-parameters).

## <a name="use-correct-schema"></a>Använda rätt schema

Testnamn: **DeploymentTemplate-schemat är korrekt**

I mallen måste du ange ett giltigt schemavärde.

Följande exempel klarar **det** här testet.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {},
  "resources": []
}
```

Schemaegenskapen i mallen måste vara inställd på något av följande scheman:

* `https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#`
* `https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#`
* `https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#`
* `https://schema.management.azure.com/schemas/2019-08-01/tenantDeploymentTemplate.json#`
* `https://schema.management.azure.com/schemas/2019-08-01/managementGroupDeploymentTemplate.json`

## <a name="parameters-must-exist"></a>Parametrar måste finnas

Testnamn: **Parameteregenskapen måste finnas**

Mallen ska ha ett parameterelement. Parametrar är viktiga för att göra så att dina mallar kan återanvändas i olika miljöer. Lägg till parametrar i mallen för värden som ändras när du distribuerar till olika miljöer.

Följande exempel klarar **det** här testet:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
      "vmName": {
          "type": "string",
          "defaultValue": "linux-vm",
          "metadata": {
            "description": "Name for the Virtual Machine."
          }
      }
  },
  ...
```

## <a name="declared-parameters-must-be-used"></a>Deklarerade parametrar måste användas

Testnamn: **Parametrar måste refereras**

Du kan minska förvirringen i mallen genom att ta bort alla parametrar som har definierats men inte används. Det här testet hittar alla parametrar som inte används någonstans i mallen. Om du tar bort oanvända parametrar blir det också enklare att distribuera mallen eftersom du inte behöver ange onödiga värden.

## <a name="secure-parameters-cant-have-hardcoded-default"></a>Säkra parametrar kan inte ha hårdkodad standard

Testnamn: **Säkra strängparametrar får inte ha standardvärden**

Ange inte ett hårdkodat standardvärde för en säker parameter i mallen. En tom sträng fungerar bra för standardvärdet.

Du använder typerna **SecureString eller** **SecureObject** på parametrar som innehåller känsliga värden, till exempel lösenord. När en parameter använder en säker typ loggas eller lagras inte värdet för parametern i distributionshistoriken. Den här åtgärden förhindrar att en obehörig användare identifierar det känsliga värdet.

Men när du anger ett standardvärde kan det värdet upptäckas av alla som har åtkomst till mallen eller distributionshistoriken.

I följande exempel **misslyckas det** här testet:

```json
"parameters": {
    "adminPassword": {
        "defaultValue": "HardcodedPassword",
        "type": "SecureString"
    }
}
```

Nästa exempel klarar **det** här testet:

```json
"parameters": {
    "adminPassword": {
        "type": "SecureString"
    }
}
```

## <a name="environment-urls-cant-be-hardcoded"></a>Miljö-URL:er kan inte hårdkodas

Testnamn: **DeploymentTemplate får inte innehålla hårdkodad URI**

Hårdkoda inte miljö-URL:er i mallen. Använd i stället [miljöfunktionen för](template-functions-deployment.md#environment) att dynamiskt hämta dessa URL:er under distributionen. En lista över de URL-värdar som blockeras finns i [testfallet](https://github.com/Azure/arm-ttk/blob/master/arm-ttk/testcases/deploymentTemplate/DeploymentTemplate-Must-Not-Contain-Hardcoded-Uri.test.ps1).

I följande exempel **misslyckas det** här testet eftersom URL:en är hårdkodad.

```json
"variables":{
    "AzureURL":"https://management.azure.com"
}
```

Testet misslyckas också **när det** används med [concat](template-functions-string.md#concat) eller [uri](template-functions-string.md#uri).

```json
"variables":{
    "AzureSchemaURL1": "[concat('https://','gallery.azure.com')]",
    "AzureSchemaURL2": "[uri('gallery.azure.com','test')]"
}
```

Följande exempel klarar **det** här testet.

```json
"variables": {
    "AzureSchemaURL": "[environment().gallery]"
},
```

## <a name="location-uses-parameter"></a>Platsen använder parametern

Testnamn: **Platsen får inte vara hårdkodad**

Mallarna bör ha en parameter med namnet location. Använd den här parametern för att ange platsen för resurser i mallen. I huvudmallen (med _azuredeploy.jspå_ eller _mainTemplate.js)_ kan den här parametern som standard ha resursgruppens plats. I länkade eller kapslade mallar bör platsparametern inte ha en standardplats.

Användare av mallen kan ha begränsade regioner tillgängliga. När du hårdkodar resursplatsen kan användarna blockeras från att skapa en resurs i den regionen. Användare kan blockeras även om du anger resursplatsen till `"[resourceGroup().location]"` . Resursgruppen kan ha skapats i en region som andra användare inte kan komma åt. Dessa användare blockeras från att använda mallen.

Genom att ange en platsparameter som standard är resursgruppens plats kan användarna använda standardvärdet när det passar men även ange en annan plats.

I följande exempel **misslyckas** det här testet eftersom platsen på resursen är inställd på `resourceGroup().location` .

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {},
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Storage/storageAccounts",
            "apiVersion": "2019-06-01",
            "name": "storageaccount1",
            "location": "[resourceGroup().location]",
            "kind": "StorageV2",
            "sku": {
                "name": "Premium_LRS",
                "tier": "Premium"
            }
        }
    ]
}
```

I nästa exempel används en platsparameter men **det** här testet misslyckas eftersom platsparametern som standard har en hårdkodad plats.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "location": {
            "type": "string",
            "defaultValue": "westus"
        }
    },
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Storage/storageAccounts",
            "apiVersion": "2019-06-01",
            "name": "storageaccount1",
            "location": "[parameters('location')]",
            "kind": "StorageV2",
            "sku": {
                "name": "Premium_LRS",
                "tier": "Premium"
            }
        }
    ],
    "outputs": {}
}
```

Skapa i stället en parameter som använder resursgruppens plats som standard, men som gör att användarna kan ange ett annat värde. Följande exempel **klarar det** här testet när mallen används som huvudmall.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "location": {
            "type": "string",
            "defaultValue": "[resourceGroup().location]",
            "metadata": {
                "description": "Location for the resources."
            }
        }
     },
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Storage/storageAccounts",
            "apiVersion": "2019-06-01",
            "name": "storageaccount1",
            "location": "[parameters('location')]",
            "kind": "StorageV2",
            "sku": {
                "name": "Premium_LRS",
                "tier": "Premium"
            }
        }
    ],
    "outputs": {}
}
```

Men om föregående exempel används som en länkad mall misslyckas **testet**. Ta bort standardvärdet när det används som en länkad mall.

## <a name="resources-should-have-location"></a>Resurser ska ha en plats

Testnamn: **Resurserna ska ha en plats**

Platsen för en resurs ska anges till ett [malluttryck](template-expressions.md) eller `global` . Malluttrycket använder vanligtvis platsparametern som beskrivs i föregående test.

I följande exempel **misslyckas** det här testet eftersom platsen inte är ett uttryck eller `global` .

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {},
    "functions": [],
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Storage/storageAccounts",
            "apiVersion": "2019-06-01",
            "name": "storageaccount1",
            "location": "westus",
            "kind": "StorageV2",
            "sku": {
                "name": "Premium_LRS",
                "tier": "Premium"
            }
        }
    ],
    "outputs": {}
}
```

Följande exempel klarar **det** här testet.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {},
    "functions": [],
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Maps/accounts",
            "apiVersion": "2020-02-01-preview",
            "name": "demoMap",
            "location": "global",
            "sku": {
                "name": "S0"
            }
        }
    ],
    "outputs": {
    }
}
```

Nästa exempel klarar **även det** här testet.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "location": {
            "type": "string",
            "defaultValue": "[resourceGroup().location]",
            "metadata": {
                "description": "Location for the resources."
            }
        }
     },
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Storage/storageAccounts",
            "apiVersion": "2019-06-01",
            "name": "storageaccount1",
            "location": "[parameters('location')]",
            "kind": "StorageV2",
            "sku": {
                "name": "Premium_LRS",
                "tier": "Premium"
            }
        }
    ],
    "outputs": {}
}
```

## <a name="vm-size-uses-parameter"></a>VM-storleken använder parametern

Testnamn: **VM-storleken ska vara en parameter**

Hårdkoda inte storleken på den virtuella datorn. Ange en parameter så att användare av mallen kan ändra storleken på den distribuerade virtuella datorn.

Det här testet misslyckas **i följande** exempel.

```json
"hardwareProfile": {
    "vmSize": "Standard_D2_v3"
}
```

Ange i stället en parameter.

```json
"vmSize": {
    "type": "string",
    "defaultValue": "Standard_A2_v2",
    "metadata": {
        "description": "Size for the Virtual Machine."
    }
},
```

Ange sedan VM-storleken till den parametern.

```json
"hardwareProfile": {
    "vmSize": "[parameters('vmSize')]"
},
```

## <a name="min-and-max-values-are-numbers"></a>Minsta och högsta värden är tal

Testnamn: **Minsta och högsta värde är tal**

Om du definierar min- och maxvärden för en parameter anger du dem som siffror.

I följande exempel **misslyckas det** här testet:

```json
"exampleParameter": {
    "type": "int",
    "minValue": "0",
    "maxValue": "10"
},
```

Ange i stället värdena som tal. Följande exempel klarar **det** här testet:

```json
"exampleParameter": {
    "type": "int",
    "minValue": 0,
    "maxValue": 10
},
```

Du får även den här varningen om du anger ett min- eller maxvärde, men inte det andra.

## <a name="artifacts-parameter-defined-correctly"></a>Artefaktparametern har definierats korrekt

Testnamn: **artefaktparameter**

När du inkluderar parametrar `_artifactsLocation` för och använder du rätt `_artifactsLocationSasToken` standardvärden och typer. Följande villkor måste uppfyllas för att klara det här testet:

* Om du anger en parameter måste du ange den andra
* `_artifactsLocation` måste vara en **sträng**
* `_artifactsLocation` måste ha ett standardvärde i huvudmallen
* `_artifactsLocation` kan inte ha ett standardvärde i en kapslad mall
* `_artifactsLocation` måste ha antingen `"[deployment().properties.templateLink.uri]"` eller råa lagringsplatsens URL för dess standardvärde
* `_artifactsLocationSasToken` måste vara en **secureString**
* `_artifactsLocationSasToken` kan bara ha en tom sträng för dess standardvärde
* `_artifactsLocationSasToken` kan inte ha ett standardvärde i en kapslad mall

## <a name="declared-variables-must-be-used"></a>Deklarerade variabler måste användas

Testnamn: **Variabler måste refereras till**

Du kan minska förvirringen i mallen genom att ta bort alla variabler som har definierats men inte används. Det här testet hittar alla variabler som inte används någonstans i mallen.

## <a name="dynamic-variable-should-not-use-concat"></a>Dynamisk variabel bör inte använda concat

Testnamn: **Dynamiska variabelreferenser bör inte använda Concat**

Ibland kan du behöva skapa en variabel dynamiskt baserat på värdet för en annan variabel eller parameter. Använd inte funktionen [concat när](template-functions-string.md#concat) du anger värdet. Använd i stället ett -objekt som innehåller de tillgängliga alternativen och dynamiskt hämta en av egenskaperna från objektet under distributionen.

Följande exempel klarar **det** här testet. Variabeln **currentImage** anges dynamiskt under distributionen.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
      "osType": {
          "type": "string",
          "allowedValues": [
              "Windows",
              "Linux"
          ]
      }
  },
  "variables": {
    "imageOS": {
        "Windows": {
            "image": "Windows Image"
        },
        "Linux": {
            "image": "Linux Image"
        }
    },
    "currentImage": "[variables('imageOS')[parameters('osType')].image]"
  },
  "resources": [],
  "outputs": {
      "result": {
          "type": "string",
          "value": "[variables('currentImage')]"
      }
  }
}
```

## <a name="use-recent-api-version"></a>Använda den senaste API-versionen

Testnamn: **apiVersions bör vara senaste**

API-versionen för varje resurs bör använda en ny version. Testet utvärderar den version som du använder mot de versioner som är tillgängliga för den resurstypen.

## <a name="use-hardcoded-api-version"></a>Använda hårdkodad API-version

Testnamn: **Providers apiVersions tillåts inte**

API-versionen för en resurstyp avgör vilka egenskaper som är tillgängliga. Ange en hårdkodad API-version i mallen. Hämta inte en API-version som fastställs under distributionen. Du vet inte vilka egenskaper som är tillgängliga.

Det här testet misslyckas **i följande** exempel.

```json
"resources": [
    {
        "type": "Microsoft.Compute/virtualMachines",
        "apiVersion": "[providers('Microsoft.Compute', 'virtualMachines').apiVersions[0]]",
        ...
    }
]
```

Följande exempel klarar **det** här testet.

```json
"resources": [
    {
       "type": "Microsoft.Compute/virtualMachines",
       "apiVersion": "2019-12-01",
       ...
    }
]
```

## <a name="properties-cant-be-empty"></a>Egenskaper får inte vara tomma

Testnamn: **Mallen får inte innehålla tomma**

Hårdkoda inte egenskaper till ett tomt värde. Tomma värden är null och tomma strängar, objekt eller matriser. Om du har angett en egenskap till ett tomt värde tar du bort egenskapen från mallen. Det är dock okej att ange en egenskap till ett tomt värde under distributionen, till exempel via en parameter.

## <a name="use-resource-id-functions"></a>Använda resurs-ID-funktioner

Testnamn: **ID:er ska härleds från ResourceIDs**

När du anger ett resurs-ID använder du någon av resurs-ID-funktionerna. Tillåtna funktioner är:

* [resourceId](template-functions-resource.md#resourceid)
* [subscriptionResourceId](template-functions-resource.md#subscriptionresourceid)
* [tenantResourceId](template-functions-resource.md#tenantresourceid)
* [extensionResourceId](template-functions-resource.md#extensionresourceid)

Använd inte funktionen concat för att skapa ett resurs-ID. Det här testet misslyckas **i följande** exempel.

```json
"networkSecurityGroup": {
    "id": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/networkSecurityGroups/', variables('networkSecurityGroupName'))]"
}
```

Nästa exempel klarar **det** här testet.

```json
"networkSecurityGroup": {
    "id": "[resourceId('Microsoft.Network/networkSecurityGroups', variables('networkSecurityGroupName'))]"
}
```

## <a name="resourceid-function-has-correct-parameters"></a>Funktionen ResourceId har rätt parametrar

Testnamn: **ResourceIds får inte innehålla**

Använd inte onödiga funktioner för valfria parametrar när du genererar resurs-ID:er. Som standard använder [funktionen resourceId](template-functions-resource.md#resourceid) den aktuella prenumerationen och resursgruppen. Du behöver inte ange dessa värden.

I följande exempel **misslyckas** det här testet eftersom du inte behöver ange det aktuella prenumerations-ID:t och resursgruppens namn.

```json
"networkSecurityGroup": {
    "id": "[resourceId(subscription().subscriptionId, resourceGroup().name, 'Microsoft.Network/networkSecurityGroups', variables('networkSecurityGroupName'))]"
}
```

Nästa exempel klarar **det** här testet.

```json
"networkSecurityGroup": {
    "id": "[resourceId('Microsoft.Network/networkSecurityGroups', variables('networkSecurityGroupName'))]"
}
```

Det här testet gäller för:

* [resourceId](template-functions-resource.md#resourceid)
* [subscriptionResourceId](template-functions-resource.md#subscriptionresourceid)
* [tenantResourceId](template-functions-resource.md#tenantresourceid)
* [extensionResourceId](template-functions-resource.md#extensionresourceid)
* [Referens](template-functions-resource.md#reference)
* [lista*](template-functions-resource.md#list)

För `reference` och misslyckas testet när `list*` **du** använder för att `concat` skapa resurs-ID:t.

## <a name="dependson-best-practices"></a>dependsOn metodtips

Testnamn: **DependsOn –metodtips**

När du anger distributionsberoenden ska du inte använda [if-funktionen](template-functions-logical.md#if) för att testa ett villkor. Om en resurs är beroende av en resurs som [är villkorligt distribuerad](conditional-resource-deployment.md)anger du beroendet på samma sätt som med alla resurser. När en villkorlig resurs inte distribueras tar Azure Resource Manager bort den från de nödvändiga beroendena.

Det här testet misslyckas **i följande** exempel.

```json
"dependsOn": [
    "[if(equals(parameters('newOrExisting'),'new'), variables('storageAccountName'), '')]"
]
```

Nästa exempel klarar **det** här testet.

```json
"dependsOn": [
    "[variables('storageAccountName')]"
]
```

## <a name="nested-or-linked-deployments-cant-use-debug"></a>Kapslade eller länkade distributioner kan inte använda felsökning

Testnamn: **Distributionsresurser får inte felsökas**

När du definierar [en kapslad](linked-templates.md) eller länkad mall med resurstypen **Microsoft.Resources/deployments** kan du aktivera felsökning för mallen. Felsökning fungerar bra när du behöver testa mallen, men bör aktiveras när du är redo att använda mallen i produktion.

## <a name="admin-user-names-cant-be-literal-value"></a>Administratörsanvändarnamn får inte vara literalvärde

Testnamn: **adminUsername bör inte vara en literal**

Använd inte ett literalvärde när du anger ett administratörsanvändarnamn.

I följande exempel **misslyckas det** här testet:

```json
"osProfile":  {
    "adminUserName":  "myAdmin"
},
```

Använd i stället en parameter. Följande exempel klarar **det** här testet:

```json
"osProfile": {
    "adminUsername": "[parameters('adminUsername')]"
}
```

## <a name="use-latest-vm-image"></a>Använda den senaste vm-avbildningen

Testnamn: **VM-avbildningar bör använda den senaste versionen**

Om mallen innehåller en virtuell dator med en avbildning kontrollerar du att den använder den senaste versionen av avbildningen.

## <a name="use-stable-vm-images"></a>Använda stabila VM-avbildningar

Testnamn: **Virtual Machines ska inte vara förhandsversion**

Virtuella datorer bör inte använda förhandsgranskningsavbildningar.

Det här testet misslyckas **i följande** exempel.

```json
"imageReference": {
    "publisher": "Canonical",
    "offer": "UbuntuServer",
    "sku": "16.04-LTS",
    "version": "latest-preview"
}
```

Följande exempel klarar **det** här testet.

```json
"imageReference": {
    "publisher": "Canonical",
    "offer": "UbuntuServer",
    "sku": "16.04-LTS",
    "version": "latest"
},
```

## <a name="dont-use-managedidentity-extension"></a>Använd inte tillägget ManagedIdentity

Testnamn: **ManagedIdentityExtension får inte användas**

Tillämpa inte managedIdentity-tillägget på en virtuell dator. Tillägget blev inaktuellt 2019 och bör inte längre användas.

## <a name="outputs-cant-include-secrets"></a>Utdata kan inte innehålla hemligheter

Testnamn: **Utdata får inte innehålla hemligheter**

Ta inte med några värden i avsnittet utdata som potentiellt exponerar hemligheter. Utdata från en mall lagras i distributionshistoriken, så en obehörig användare kan hitta den informationen.

I följande exempel **misslyckas** testet eftersom det innehåller en säker parameter i ett utdatavärde.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "secureParam": {
            "type": "securestring"
        }
    },
    "functions": [],
    "variables": {},
    "resources": [],
    "outputs": {
        "badResult": {
            "type": "string",
            "value": "[concat('this is the value ', parameters('secureParam'))]"
        }
    }
}
```

Följande exempel misslyckas **eftersom** den använder en [list*-funktion](template-functions-resource.md#list) i utdata.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "storageName": {
            "type": "string"
        }
    },
    "functions": [],
    "variables": {},
    "resources": [],
    "outputs": {
        "badResult": {
            "type": "object",
            "value": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', parameters('storageName')), '2019-06-01')]"
        }
    }
}
```

## <a name="use-protectedsettings-for-commandtoexecute-secrets"></a>Använda protectedSettings för commandToExecute-hemligheter

Testnamn: **CommandToExecute måste använda ProtectedSettings för hemligheter**

I ett tillägg för anpassat skript använder du den krypterade egenskapen `protectedSettings` när `commandToExecute` innehåller hemliga data, till exempel ett lösenord. Exempel på hemliga datatyper `secureString` är , , funktioner eller `secureObject` `list()` skript.

Mer information om tillägg för anpassade skript för virtuella datorer finns [i Windows](
/azure/virtual-machines/extensions/custom-script-windows), [Linux](/azure/virtual-machines/extensions/custom-script-linux)och schemat [Microsoft.Compute virtualMachines/extensions](/azure/templates/microsoft.compute/virtualmachines/extensions).

I det här exemplet klarar en mall med en parameter med `adminPassword` namnet och typen testet eftersom den `secureString`  krypterade egenskapen `protectedSettings` innehåller `commandToExecute` .

```json
"properties": [
  {
    "protectedSettings": {
      "commandToExecute": "[parameters('adminPassword')]"
    }
  }
]
```

Testet misslyckas **om** den okrypterade egenskapen `settings` innehåller `commandToExecute` .

```json
"properties": [
  {
    "settings": {
      "commandToExecute": "[parameters('adminPassword')]"
    }
  }
]
```

## <a name="next-steps"></a>Nästa steg

* Mer information om hur du kör testverktygen finns i [Använda ARM-malltestverktyg.](test-toolkit.md)
* En Microsoft Learn som beskriver hur du använder testverktygen finns i Förhandsgranska ändringar och validera Azure-resurser med hjälp av vad om och [ARM-mallens testverktyg.](/learn/modules/arm-template-test/)
