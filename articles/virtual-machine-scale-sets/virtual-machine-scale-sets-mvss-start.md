---
title: Lär dig mer om mallar för skalnings uppsättningar för virtuella datorer
description: Lär dig hur du skapar en grundläggande skalnings uppsättnings mall för skalnings uppsättningar för virtuella Azure-datorer via flera enkla steg.
author: mimckitt
ms.author: mimckitt
ms.topic: conceptual
ms.service: virtual-machine-scale-sets
ms.subservice: template
ms.date: 04/26/2019
ms.reviewer: jushiman
ms.custom: mimckitt
ms.openlocfilehash: 55c826b4baf38732684aaa0465aeaab6a45564db
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "87831506"
---
# <a name="learn-about-virtual-machine-scale-set-templates"></a>Lär dig mer om mallar för skalnings uppsättningar för virtuella datorer
[Azure Resource Manager-mallar](../azure-resource-manager/templates/overview.md#template-deployment-process) är ett bra sätt att distribuera grupper av relaterade resurser. Den här själv studie serien visar hur du skapar en grundläggande mall för skalnings uppsättningar och hur du ändrar den här mallen så att den passar olika scenarier. Alla exempel kommer från den här [GitHub-lagringsplatsen](https://github.com/gatneil/mvss).

Den här mallen är avsedd att vara enkel. Fler kompletta exempel på mallar för skalnings uppsättningar finns i [GitHub-lagringsplatsen för Azure snabb starts mallar](https://github.com/Azure/azure-quickstart-templates) och söka efter mappar som innehåller strängen `vmss` .

Om du redan är bekant med att skapa mallar kan du gå vidare till avsnittet "nästa steg" för att se hur du ändrar den här mallen.

## <a name="define-schema-and-contentversion"></a>Definiera $schema och contentVersion
Börja med `$schema` att definiera och `contentVersion` i mallen. `$schema`Elementet definierar versionen för mallens språk och används för Visual Studio-syntax och liknande verifierings funktioner. `contentVersion`Elementet används inte av Azure. I stället hjälper det dig att hålla koll på mallens version.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
  "contentVersion": "1.0.0.0",
```

## <a name="define-parameters"></a>Definiera parametrar
Definiera sedan två parametrar `adminUsername` och `adminPassword` . Parametrar är värden som du anger vid tidpunkten för distributionen. `adminUsername`Parametern är helt enkelt en `string` typ, men eftersom `adminPassword` är en hemlighet, så ge den typen `securestring` . Senare överförs dessa parametrar till konfigurationen för skalnings uppsättningar.

```json
  "parameters": {
    "adminUsername": {
      "type": "string"
    },
    "adminPassword": {
      "type": "securestring"
    }
  },
```
## <a name="define-variables"></a>Definiera variabler
Med Resource Manager-mallar kan du också definiera variabler som ska användas senare i mallen. Exemplet använder inte några variabler, så JSON-objektet är tomt.

```json
  "variables": {},
```

## <a name="define-resources"></a>Definiera resurser
Härnäst är avsnittet resurser i mallen. Här definierar du vad du faktiskt vill distribuera. Till skillnad från `parameters` och `variables` (som är JSON-objekt) `resources` är en JSON-lista med JSON-objekt.

```json
   "resources": [
```

Alla resurser kräver `type` , `name` , `apiVersion` och `location` . Det här exemplets första resurs har typen [Microsoft. Network/virtualNetwork](/azure/templates/microsoft.network/virtualnetworks), Name och `myVnet` API version `2018-11-01` . (Du hittar den senaste API-versionen för en resurs typ finns i [referensen för Azure Resource Manager mal len](/azure/templates/).)

```json
     {
       "type": "Microsoft.Network/virtualNetworks",
       "name": "myVnet",
       "apiVersion": "2018-11-01",
```

## <a name="specify-location"></a>Ange plats
Använd en [Resource Manager-mall](../azure-resource-manager/templates/template-functions.md)för att ange platsen för det virtuella nätverket. Funktionen måste stå inom citat tecken och hakparenteser som denna: `"[<template-function>]"` . I det här fallet använder du `resourceGroup` funktionen. Det tar inga argument och returnerar ett JSON-objekt med metadata om den resurs grupp som distributionen distribueras till. Resurs gruppen ställs in av användaren vid tidpunkten för distributionen. Värdet indexeras sedan till detta JSON-objekt med `.location` för att hämta platsen från JSON-objektet.

```json
       "location": "[resourceGroup().location]",
```

## <a name="specify-virtual-network-properties"></a>Ange egenskaper för virtuellt nätverk
Varje Resource Manager-resurs har ett eget `properties` avsnitt för konfigurationer som är specifika för resursen. I det här fallet anger du att det virtuella nätverket ska ha ett undernät som använder det privata IP-adressintervallet `10.0.0.0/16` . En skalnings uppsättning ingår alltid i ett undernät. Den kan inte omfatta undernät.

```json
       "properties": {
         "addressSpace": {
           "addressPrefixes": [
             "10.0.0.0/16"
           ]
         },
         "subnets": [
           {
             "name": "mySubnet",
             "properties": {
               "addressPrefix": "10.0.0.0/16"
             }
           }
         ]
       }
     },
```

## <a name="add-dependson-list"></a>Lägg till dependsOn-lista
Förutom obligatoriska `type` ,, `name` `apiVersion` och `location` egenskaper kan varje resurs ha en valfri `dependsOn` lista med strängar. Den här listan anger vilka andra resurser från distributionen som måste slutföras innan den här resursen distribueras.

I det här fallet finns det bara ett-element i listan, det virtuella nätverket från föregående exempel. Du anger detta beroende eftersom skalnings uppsättningen kräver att nätverket finns innan du skapar några virtuella datorer. På så sätt kan skalnings uppsättningen ge de virtuella datorerna privata IP-adresser från det IP-adressintervall som tidigare angavs i nätverks egenskaperna. Formatet för varje sträng i dependsOn-listan är `<type>/<name>` . Använd samma `type` och `name` som tidigare i den virtuella nätverks resurs definitionen.

```json
     {
       "type": "Microsoft.Compute/virtualMachineScaleSets",
       "name": "myScaleSet",
       "apiVersion": "2019-03-01",
       "location": "[resourceGroup().location]",
       "dependsOn": [
         "Microsoft.Network/virtualNetworks/myVnet"
       ],
```
## <a name="specify-scale-set-properties"></a>Ange egenskaper för skalnings uppsättning
Skalnings uppsättningar har många egenskaper för att anpassa de virtuella datorerna i skalnings uppsättningen. En fullständig lista över dessa egenskaper finns i [mallen referens](/azure/templates/microsoft.compute/virtualmachinescalesets). I den här självstudien anges bara några vanliga egenskaper.
### <a name="supply-vm-size-and-capacity"></a>Ange storlek och kapacitet för virtuell dator
Skalnings uppsättningen måste veta vad storleken på den virtuella datorn som ska skapas ("SKU-namn") och hur många sådana virtuella datorer som ska skapas ("SKU-kapacitet"). Information om vilka VM-storlekar som är tillgängliga finns i [dokumentationen för VM-storlekar](../virtual-machines/sizes.md).

```json
       "sku": {
         "name": "Standard_A1",
         "capacity": 2
       },
```

### <a name="choose-type-of-updates"></a>Välj typ av uppdateringar
Skalnings uppsättningen måste också veta hur du hanterar uppdateringar på skalnings uppsättningen. Det finns för närvarande tre alternativ, `Manual` `Rolling` och `Automatic` . Mer information om skillnaderna mellan de två finns i dokumentationen om [hur du uppgraderar en skalnings uppsättning](./virtual-machine-scale-sets-upgrade-scale-set.md#how-to-bring-vms-up-to-date-with-the-latest-scale-set-model).

```json
       "properties": {
         "upgradePolicy": {
           "mode": "Manual"
         },
```

### <a name="choose-vm-operating-system"></a>Välj operativ system för virtuell dator
Skalnings uppsättningen måste veta vilket operativ system som ska finnas på de virtuella datorerna. Här skapar du de virtuella datorerna med en fullständigt korrigerad Ubuntu 16,04-LTS-avbildning.

```json
         "virtualMachineProfile": {
           "storageProfile": {
             "imageReference": {
               "publisher": "Canonical",
               "offer": "UbuntuServer",
               "sku": "16.04-LTS",
               "version": "latest"
             }
           },
```

### <a name="specify-computernameprefix"></a>Ange computerNamePrefix
Skalnings uppsättningen distribuerar flera virtuella datorer. I stället för att ange varje VM-namn anger du `computerNamePrefix` . Skalnings uppsättningen lägger till ett index i prefixet för varje virtuell dator, så att VM-namn har formuläret `<computerNamePrefix>_<auto-generated-index>` .

I följande kodfragment använder du parametrarna från innan för att ange administratörens användar namn och lösen ord för alla virtuella datorer i skalnings uppsättningen. Den här processen använder `parameters` funktionen mall. Den här funktionen tar i en sträng som anger vilken parameter som ska referera till och returnerar värdet för den parametern.

```json
           "osProfile": {
             "computerNamePrefix": "vm",
             "adminUsername": "[parameters('adminUsername')]",
             "adminPassword": "[parameters('adminPassword')]"
           },
```

### <a name="specify-vm-network-configuration"></a>Ange konfiguration av virtuellt dator nätverk
Slutligen anger du nätverks konfigurationen för de virtuella datorerna i skalnings uppsättningen. I så fall behöver du bara ange ID för under nätet som skapades tidigare. Detta anger att skalnings uppsättningen ska sätta nätverks gränssnitten i det här under nätet.

Du kan hämta ID för det virtuella nätverket som innehåller under nätet med hjälp av `resourceId` funktionen mall. Den här funktionen tar i typ och namn för en resurs och returnerar den fullständigt kvalificerade identifieraren för den resursen. Detta ID har formatet: `/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/<resourceProviderNamespace>/<resourceType>/<resourceName>`

Identifieraren för det virtuella nätverket är dock inte tillräckligt. Ange det angivna under nätet som de virtuella datorerna för skalnings uppsättningen ska vara i. Det gör du genom att sammanfoga `/subnets/mySubnet` till ID: t för det virtuella nätverket. Resultatet är det fullständigt kvalificerade ID: t för under nätet. Gör den här sammanfogningen med `concat` funktionen, som tar i en serie med strängar och returnerar deras sammanfogning.

```json
           "networkProfile": {
             "networkInterfaceConfigurations": [
               {
                 "name": "myNic",
                 "properties": {
                   "primary": "true",
                   "ipConfigurations": [
                     {
                       "name": "myIpConfig",
                       "properties": {
                         "subnet": {
                           "id": "[concat(resourceId('Microsoft.Network/virtualNetworks', 'myVnet'), '/subnets/mySubnet')]"
                         }
                       }
                     }
                   ]
                 }
               }
             ]
           }
         }
       }
     }
   ]
}

```

## <a name="next-steps"></a>Nästa steg

[!INCLUDE [mvss-next-steps-include](../../includes/mvss-next-steps.md)]