---
title: Test väskor för test Toolkit
description: Beskriver de tester som körs av ARM-mallens test verktyg.
ms.topic: conceptual
ms.date: 12/03/2020
ms.author: tomfitz
author: tfitzmac
ms.openlocfilehash: 451323058ad743d6e26fc8bcea27d1b44c76f543
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "97674050"
---
# <a name="default-test-cases-for-arm-template-test-toolkit"></a>Standardtest-fall för test verktyg för ARM-mall

I den här artikeln beskrivs de standardtester som körs med [mallens test verktyg](test-toolkit.md). Det innehåller exempel som klarar eller underlåter testet. Den innehåller namnet på varje test.

## <a name="use-correct-schema"></a>Använd rätt schema

Test namn: **DeploymentTemplate-schemat är korrekt**

I mallen måste du ange ett giltigt schema värde.

I följande exempel **överförs** det här testet.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {},
  "resources": []
}
```

Schema egenskapen i mallen måste anges till något av följande scheman:

* `https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#`
* `https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#`
* `https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#`
* `https://schema.management.azure.com/schemas/2019-08-01/tenantDeploymentTemplate.json#`
* `https://schema.management.azure.com/schemas/2019-08-01/managementGroupDeploymentTemplate.json`

## <a name="parameters-must-exist"></a>Parametrarna måste finnas

Test namn: **Parameters-egenskapen måste finnas**

Din mall bör ha ett parameter element. Parametrar är nödvändiga för att göra dina mallar återanvändbara i olika miljöer. Lägg till parametrar i mallen för värden som ändras vid distribution till olika miljöer.

Följande exempel **skickar** det här testet:

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

Test namn: **parametrar måste refereras**

Om du vill minska förvirring i mallen tar du bort alla parametrar som har definierats men inte används. Det här testet hittar alla parametrar som inte används var som helst i mallen. Att ta bort oanvända parametrar gör det också enklare att distribuera mallen eftersom du inte behöver ange onödiga värden.

## <a name="secure-parameters-cant-have-hardcoded-default"></a>Säkra parametrar kan inte ha hårdkodad-standardvärdet

Test namn: **säkra sträng parametrar kan inte ha standard**

Ange inte ett hårdkodat standardvärde för en säker parameter i mallen. En tom sträng är bra för standardvärdet.

Du använder typerna **SecureString** eller **SecureObject** på parametrar som innehåller känsliga värden, t. ex. lösen ord. När en parameter använder en säker typ, loggas inte värdet för parametern eller lagras i distributions historiken. Den här åtgärden förhindrar att en obehörig användare identifierar det känsliga värdet.

Men när du anger ett standardvärde, kan det här värdet identifieras av alla som har åtkomst till mallen eller distributions historiken.

I följande exempel **Miss lyckas** det här testet:

```json
"parameters": {
    "adminPassword": {
        "defaultValue": "HardcodedPassword",
        "type": "SecureString"
    }
}
```

Nästa exempel **skickar** det här testet:

```json
"parameters": {
    "adminPassword": {
        "type": "SecureString"
    }
}
```

## <a name="environment-urls-cant-be-hardcoded"></a>Miljö-URL: er kan inte vara hårdkodad

Test namn: **DeploymentTemplate får inte innehålla hårdkodad-URI**

Hårdkoda inte miljö-URL: er i din mall. Använd i stället [miljö funktionen](template-functions-deployment.md#environment) för att dynamiskt hämta dessa URL: er under distributionen. En lista över de URL-värdar som blockeras finns i [test fallet](https://github.com/Azure/arm-ttk/blob/master/arm-ttk/testcases/deploymentTemplate/DeploymentTemplate-Must-Not-Contain-Hardcoded-Uri.test.ps1).

I följande exempel **Miss lyckas** det här testet eftersom URL: en är hårdkodad.

```json
"variables":{
    "AzureURL":"https://management.azure.com"
}
```

Testet **Miss lyckas** också när det används med [concat](template-functions-string.md#concat) eller [URI](template-functions-string.md#uri).

```json
"variables":{
    "AzureSchemaURL1": "[concat('https://','gallery.azure.com')]",
    "AzureSchemaURL2": "[uri('gallery.azure.com','test')]"
}
```

I följande exempel **överförs** det här testet.

```json
"variables": {
    "AzureSchemaURL": "[environment().gallery]"
},
```

## <a name="location-uses-parameter"></a>Plats använder parameter

Test namn: **platsen får inte vara hårdkodad**

Mallarna måste ha en parameter med namnet location. Använd den här parametern för att ange placeringen av resurser i mallen. I huvud mal len (med namnet azuredeploy.jspå eller mainTemplate.jspå) kan den här parametern användas som standard för resurs gruppens plats. I länkade eller kapslade mallar får plats parametern inte ha en standard plats.

Användare av din mall kan ha begränsade regioner tillgängliga för dem. När du hårdkodar resurs platsen kan användarna blockeras från att skapa en resurs i den regionen. Användarna kan blockeras även om du anger resurs platsen till `"[resourceGroup().location]"` . Resurs gruppen kan ha skapats i en region som andra användare inte kan komma åt. Dessa användare blockeras från att använda mallen.

Genom att ange en plats parameter som är standard för resurs gruppens plats kan användarna använda standardvärdet när de är praktiska, men även ange en annan plats.

I följande exempel **Miss lyckas** det här testet eftersom platsen på resursen är inställd på `resourceGroup().location` .

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

I nästa exempel används en plats parameter men det här testet **Miss lyckas** eftersom plats parametern som standard är en hårdkodad plats.

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

Skapa i stället en parameter som används som standard för resurs grupps platsen, men gör det möjligt för användarna att ange ett annat värde. I följande exempel **överförs** det här testet när mallen används som huvud mal len.

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

Men om föregående exempel används som en länkad mall, **Miss lyckas** testet. Ta bort standardvärdet när det används som en länkad mall.

## <a name="resources-should-have-location"></a>Resurserna måste ha plats

Testnamn: **resurser måste ha plats**

Platsen för en resurs måste anges till ett [mall uttryck](template-expressions.md) eller `global` . Mallnamnet använder vanligt vis den plats parameter som beskrivs i föregående test.

I följande exempel **Miss lyckas** det här testet eftersom platsen inte är ett uttryck eller `global` .

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

I följande exempel **överförs** det här testet.

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

Nästa exempel **klarar** även det här testet.

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

## <a name="vm-size-uses-parameter"></a>Den virtuella datorns storlek använder parametern

Test namn: **storleken på den virtuella datorn måste vara en parameter**

Hårdkoda inte storleken på den virtuella datorn. Ange en parameter så att användare av din mall kan ändra storleken på den distribuerade virtuella datorn.

I följande exempel **Miss lyckas** det här testet.

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

Ange sedan den virtuella dator storleken till den parametern.

```json
"hardwareProfile": {
    "vmSize": "[parameters('vmSize')]"
},
```

## <a name="min-and-max-values-are-numbers"></a>Min-och max värden är tal

Test namn: **min-och Max värde är siffror**

Om du definierar min-och max värden för en parameter anger du dem som tal.

I följande exempel **Miss lyckas** det här testet:

```json
"exampleParameter": {
    "type": "int",
    "minValue": "0",
    "maxValue": "10"
},
```

Ange i stället värdena som siffror. Följande exempel **skickar** det här testet:

```json
"exampleParameter": {
    "type": "int",
    "minValue": 0,
    "maxValue": 10
},
```

Du får även den här varningen om du anger ett minsta eller högsta värde, men inte det andra.

## <a name="artifacts-parameter-defined-correctly"></a>Artefakt parametern har definierats korrekt

Test namn: **artefakt parameter**

När du inkluderar parametrar för `_artifactsLocation` och `_artifactsLocationSasToken` använder du rätt standardinställningar och typer. Följande villkor måste vara uppfyllda för att det här testet ska kunna skickas:

* Om du anger en parameter måste du ange den andra
* `_artifactsLocation` måste vara en **sträng**
* `_artifactsLocation` måste ha ett standardvärde i huvud mal len
* `_artifactsLocation` Det går inte att ha ett standardvärde i en kapslad mall 
* `_artifactsLocation` måste ha antingen `"[deployment().properties.templateLink.uri]"` en eller RAW lagrings platsen-URL för sitt standardvärde
* `_artifactsLocationSasToken` måste vara en **secureString**
* `_artifactsLocationSasToken` Det får bara finnas en tom sträng för standardvärdet
* `_artifactsLocationSasToken` Det går inte att ha ett standardvärde i en kapslad mall 

## <a name="declared-variables-must-be-used"></a>Deklarerade variabler måste användas

Test namn: **variabler måste refereras**

Om du vill minska förvirring i mallen tar du bort alla variabler som har definierats men inte används. Det här testet hittar alla variabler som inte används var som helst i mallen.

## <a name="dynamic-variable-should-not-use-concat"></a>Dynamisk variabel ska inte använda concat

Test namn: **dynamiska variabel referenser ska inte använda concat**

Ibland måste du dynamiskt konstruera en variabel baserat på värdet för en annan variabel eller parameter. Använd inte funktionen [concat](template-functions-string.md#concat) när du anger värdet. Använd i stället ett objekt som innehåller de tillgängliga alternativen och hämta en av egenskaperna dynamiskt från objektet under distributionen.

I följande exempel **överförs** det här testet. **CurrentImage** -variabeln anges dynamiskt under distributionen.

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

## <a name="use-recent-api-version"></a>Använd senaste API-version

Testnamn: **ApiVersions ska vara senaste**

API-versionen för varje resurs bör använda en ny version. Testet utvärderar den version som du använder mot de versioner som är tillgängliga för den resurs typen.

## <a name="use-hardcoded-api-version"></a>Använd hårdkodad API-version

Test namn: **providers ApiVersions tillåts inte**

API-versionen för en resurs typ avgör vilka egenskaper som är tillgängliga. Ange en hårdkodad API-version i mallen. Hämta inte en API-version som fastställs under distributionen. Du vet inte vilka egenskaper som är tillgängliga.

I följande exempel **Miss lyckas** det här testet.

```json
"resources": [
    {
        "type": "Microsoft.Compute/virtualMachines",
        "apiVersion": "[providers('Microsoft.Compute', 'virtualMachines').apiVersions[0]]",
        ...
    }
]
```

I följande exempel **överförs** det här testet.

```json
"resources": [
    {
       "type": "Microsoft.Compute/virtualMachines",
       "apiVersion": "2019-12-01",
       ...
    }
]
```

## <a name="properties-cant-be-empty"></a>Egenskaperna får inte vara tomma

Testnamn: **mallen får inte innehålla blank steg**

Hårdkoda inte till ett tomt värde. Tomma värden är null och tomma strängar, objekt eller matriser. Om du har angett en egenskap till ett tomt värde tar du bort den egenskapen från din mall. Det går dock att ange en egenskap till ett tomt värde under distributionen, t. ex. via en parameter.

## <a name="use-resource-id-functions"></a>Använda resurs-ID-funktioner

Test namn: **ID: n ska härledas från ResourceIDs**

Använd ett av resurs-ID-funktionerna när du anger ett resurs-ID. De tillåtna funktionerna är:

* [resourceId](template-functions-resource.md#resourceid)
* [subscriptionResourceId](template-functions-resource.md#subscriptionresourceid)
* [tenantResourceId](template-functions-resource.md#tenantresourceid)
* [extensionResourceId](template-functions-resource.md#extensionresourceid)

Använd inte funktionen concat för att skapa ett resurs-ID. I följande exempel **Miss lyckas** det här testet.

```json
"networkSecurityGroup": {
    "id": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/networkSecurityGroups/', variables('networkSecurityGroupName'))]"
}
```

I nästa exempel **skickas** det här testet.

```json
"networkSecurityGroup": {
    "id": "[resourceId('Microsoft.Network/networkSecurityGroups', variables('networkSecurityGroupName'))]"
}
```

## <a name="resourceid-function-has-correct-parameters"></a>ResourceId-funktionen har rätt parametrar

Test namn: **ResourceIds får inte innehålla**

Använd inte onödiga funktioner för valfria parametrar när du genererar resurs-ID: n. Som standard använder funktionen [resourceId](template-functions-resource.md#resourceid) den aktuella prenumerationen och resurs gruppen. Du behöver inte ange dessa värden.  

I följande exempel **Miss lyckas** det här testet eftersom du inte behöver ange det aktuella prenumerations-ID: t och resurs gruppens namn.

```json
"networkSecurityGroup": {
    "id": "[resourceId(subscription().subscriptionId, resourceGroup().name, 'Microsoft.Network/networkSecurityGroups', variables('networkSecurityGroupName'))]"
}
```

I nästa exempel **skickas** det här testet.

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
* [förhållande](template-functions-resource.md#reference)
* [lista](template-functions-resource.md#list)

För `reference` och `list*` kan testet **Miss lyckas** när du använder `concat` för att konstruera resurs-ID: t.

## <a name="dependson-best-practices"></a>metod tips för dependsOn

Test namn: **DependsOn metod tips**

När du anger distributions beroenden ska du inte använda [IF](template-functions-logical.md#if) -funktionen för att testa ett villkor. Om en resurs är beroende av en resurs som är [villkorligt distribuerad](conditional-resource-deployment.md)anger du det beroende som du skulle göra med vilken resurs som helst. När en villkorlig resurs inte distribueras tar Azure Resource Manager automatiskt bort den från de nödvändiga beroendena.

I följande exempel **Miss lyckas** det här testet.

```json
"dependsOn": [
    "[if(equals(parameters('newOrExisting'),'new'), variables('storageAccountName'), '')]"
]
```

I nästa exempel **skickas** det här testet.

```json
"dependsOn": [
    "[variables('storageAccountName')]"
]
```

## <a name="nested-or-linked-deployments-cant-use-debug"></a>Kapslade eller länkade distributioner kan inte använda fel sökning

Testnamn: **distributions resurser får inte vara felsöka**

När du definierar en [kapslad eller länkad mall](linked-templates.md) med resurs typen **Microsoft. Resources/distributioner** kan du aktivera fel sökning för den mallen. Fel sökning är bra när du behöver testa mallen men bör vara aktive rad när du är redo att använda mallen i produktion.

## <a name="admin-user-names-cant-be-literal-value"></a>Administratörs användar namn får inte vara litterala värden

Test namn: **AdminUsername får inte vara en literal**

Använd inte ett litteralt värde när du anger ett administratörs användar namn.

I följande exempel **Miss lyckas** det här testet:

```json
"osProfile":  {
    "adminUserName":  "myAdmin"
},
```

Använd i stället en parameter. Följande exempel **skickar** det här testet:

```json
"osProfile": {
    "adminUsername": "[parameters('adminUsername')]"
}
```

## <a name="use-latest-vm-image"></a>Använd den senaste virtuella dator avbildningen

Test namn: **VM-avbildningar bör använda den senaste versionen**

Om din mall innehåller en virtuell dator med en avbildning kontrollerar du att den använder den senaste versionen av avbildningen.

## <a name="use-stable-vm-images"></a>Använd stabila VM-avbildningar

Testnamn: **Virtual Machines får inte vara för hands version**

För virtuella datorer bör inte förhands gransknings bilder användas.

I följande exempel **Miss lyckas** det här testet.

```json
"imageReference": {
    "publisher": "Canonical",
    "offer": "UbuntuServer",
    "sku": "16.04-LTS",
    "version": "latest-preview"
}
```

I följande exempel **överförs** det här testet.

```json
"imageReference": {
    "publisher": "Canonical",
    "offer": "UbuntuServer",
    "sku": "16.04-LTS",
    "version": "latest"
},
```

## <a name="dont-use-managedidentity-extension"></a>Använd inte ManagedIdentity-tillägg

Test namn: **ManagedIdentityExtension får inte användas**

Använd inte ManagedIdentity-tillägget på en virtuell dator. Mer information finns i [så här slutar du använda tillägget för hanterade identiteter för virtuella datorer och börjar använda Azure-instance metadata service](../../active-directory/managed-identities-azure-resources/howto-migrate-vm-extension.md).

## <a name="outputs-cant-include-secrets"></a>Utdata får inte innehålla hemligheter

Test namn: **utdata får inte innehålla hemligheter**

Ta inte med några värden i avsnittet utdata som potentiellt visar hemligheter. Utdata från en mall lagras i distributions historiken så att en obehörig användare kan hitta informationen.

I följande exempel **Miss lyckas** testet eftersom det innehåller en säker parameter i ett utdata-värde.

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

Följande exempel **Miss lyckas** eftersom den använder en [list *](template-functions-resource.md#list) -funktion i utdata.

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

## <a name="next-steps"></a>Nästa steg

- Läs mer om att köra test Toolkit i [använda arm Template test Toolkit](test-toolkit.md).
- En Microsoft Learn-modul som täcker med hjälp av test Toolkit finns i [Förhandsgranska ändringar och verifiera Azure-resurser med hjälp av vad-IF-och arm-mallens test verktyg](/learn/modules/arm-template-test/).