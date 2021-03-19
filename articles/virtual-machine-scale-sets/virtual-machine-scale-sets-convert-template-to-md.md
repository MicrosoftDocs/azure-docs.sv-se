---
title: Konvertera en skalningsuppsättningsmall för användning av hanterad disk
description: Konvertera en Azure Resource Manager mall för skalnings uppsättning för virtuell dator till en mall för hanterad disk skalnings uppsättning.
keywords: skalningsuppsättningar för virtuella datorer
author: ju-shim
ms.author: jushiman
ms.topic: how-to
ms.service: virtual-machine-scale-sets
ms.subservice: disks
ms.date: 6/25/2020
ms.reviewer: mimckitt
ms.custom: mimckitt
ms.openlocfilehash: 03cbe4eb56f3b3b99f87048b699f76b30b7937c8
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "85373972"
---
# <a name="convert-a-scale-set-template-to-a-managed-disk-scale-set-template"></a>Konvertera en mall för skalnings uppsättningar till en mall för hanterad disk skalnings uppsättning

Kunder med en Resource Manager-mall för att skapa en skalnings uppsättning som inte använder hanterade diskar kanske vill ändra den för att använda hanterad disk. Den här artikeln visar hur du använder hanterade diskar, som exempel på en pull-begäran från [Azure snabb starts mallar](https://github.com/Azure/azure-quickstart-templates), en community-driven lagrings platsen för exempel på Resource Manager-mallar. Den fullständiga pull-begäran kan visas här: [https://github.com/Azure/azure-quickstart-templates/pull/2998](https://github.com/Azure/azure-quickstart-templates/pull/2998) och relevanta delar av diffen nedan, tillsammans med förklaringar:

## <a name="making-the-os-disks-managed"></a>Göra operativ system diskar hanterade

I följande skillnad tas flera variabler som rör lagrings konto och disk egenskaper bort. Lagrings konto typen behövs inte längre (Standard_LRS är standard), men du kan ange den om du vill. Endast Standard_LRS och Premium_LRS stöds med hanterad disk. Nytt suffix för lagrings konto, unik sträng mat ris och antal sa användes i den gamla mallen för att generera lagrings konto namn. Dessa variabler behövs inte längre i den nya mallen eftersom hanterade diskar automatiskt skapar lagrings konton för kundens räkning. På samma sätt behövs inte längre VHD-behållare och namn på operativ system disken eftersom den hanterade disken automatiskt namnger de underliggande lagrings-BLOB-behållarna och diskarna.

```diff
   "variables": {
-    "storageAccountType": "Standard_LRS",
     "namingInfix": "[toLower(substring(concat(parameters('vmssName'), uniqueString(resourceGroup().id)), 0, 9))]",
     "longNamingInfix": "[toLower(parameters('vmssName'))]",
-    "newStorageAccountSuffix": "[concat(variables('namingInfix'), 'sa')]",
-    "uniqueStringArray": [
-      "[concat(uniqueString(concat(resourceGroup().id, variables('newStorageAccountSuffix'), '0')))]",
-      "[concat(uniqueString(concat(resourceGroup().id, variables('newStorageAccountSuffix'), '1')))]",
-      "[concat(uniqueString(concat(resourceGroup().id, variables('newStorageAccountSuffix'), '2')))]",
-      "[concat(uniqueString(concat(resourceGroup().id, variables('newStorageAccountSuffix'), '3')))]",
-      "[concat(uniqueString(concat(resourceGroup().id, variables('newStorageAccountSuffix'), '4')))]"
-    ],
-    "saCount": "[length(variables('uniqueStringArray'))]",
-    "vhdContainerName": "[concat(variables('namingInfix'), 'vhd')]",
-    "osDiskName": "[concat(variables('namingInfix'), 'osdisk')]",
     "addressPrefix": "10.0.0.0/16",
     "subnetPrefix": "10.0.0.0/24",
     "virtualNetworkName": "[concat(variables('namingInfix'), 'vnet')]",
```


I följande skillnad är beräknings-API: et uppdaterat till version 2016-04-30 – för hands version, vilket är den tidigaste version som krävs för stöd för hanterade diskar med skalnings uppsättningar. Du kan använda ohanterade diskar i den nya API-versionen med den gamla syntaxen om du vill. Om du bara uppdaterar Compute API-versionen och inte ändrar något annat bör mallen fortsätta att fungera som tidigare.

```diff
@@ -86,7 +74,7 @@
       "version": "latest"
     },
     "imageReference": "[variables('osType')]",
-    "computeApiVersion": "2016-03-30",
+    "computeApiVersion": "2016-04-30-preview",
     "networkApiVersion": "2016-03-30",
     "storageApiVersion": "2015-06-15"
   },
```

I följande skillnad tas lagrings konto resursen bort från resurs-matrisen helt och hållet. Resursen behövs inte längre eftersom den hanterade disken skapar dem automatiskt.

```diff
@@ -113,19 +101,6 @@
       }
     },
-    {
-      "type": "Microsoft.Storage/storageAccounts",
-      "name": "[concat(variables('uniqueStringArray')[copyIndex()], variables('newStorageAccountSuffix'))]",
-      "location": "[resourceGroup().location]",
-      "apiVersion": "[variables('storageApiVersion')]",
-      "copy": {
-        "name": "storageLoop",
-        "count": "[variables('saCount')]"
-      },
-      "properties": {
-        "accountType": "[variables('storageAccountType')]"
-      }
-    },
     {
       "type": "Microsoft.Network/publicIPAddresses",
       "name": "[variables('publicIPAddressName')]",
       "location": "[resourceGroup().location]",
```

I följande diff kan vi se att vi tar bort den beroende on-satsen som refererar från skalnings uppsättningen till den loop som skapade lagrings konton. I den gamla mallen säkerställer detta att lagrings kontona skapades innan skalnings uppsättningen började skapas, men den här satsen behövs inte längre med den hanterade disken. Egenskapen för VHD-behållare tas också bort, tillsammans med egenskapen för namn på operativ systemets disk när dessa egenskaper hanteras automatiskt under hanteringen av den hanterade disken. Du kan lägga till `"managedDisk": { "storageAccountType": "Premium_LRS" }` i "osDisk"-konfigurationen om du vill ha Premium OS-diskar. Endast virtuella datorer med versaler eller gemener i VM-SKU: n kan använda Premium-diskar.

```diff
@@ -183,7 +158,6 @@
       "location": "[resourceGroup().location]",
       "apiVersion": "[variables('computeApiVersion')]",
       "dependsOn": [
-        "storageLoop",
         "[concat('Microsoft.Network/loadBalancers/', variables('loadBalancerName'))]",
         "[concat('Microsoft.Network/virtualNetworks/', variables('virtualNetworkName'))]"
       ],
@@ -200,16 +174,8 @@
         "virtualMachineProfile": {
           "storageProfile": {
             "osDisk": {
-              "vhdContainers": [
-                "[concat('https://', variables('uniqueStringArray')[0], variables('newStorageAccountSuffix'), '.blob.core.windows.net/', variables('vhdContainerName'))]",
-                "[concat('https://', variables('uniqueStringArray')[1], variables('newStorageAccountSuffix'), '.blob.core.windows.net/', variables('vhdContainerName'))]",
-                "[concat('https://', variables('uniqueStringArray')[2], variables('newStorageAccountSuffix'), '.blob.core.windows.net/', variables('vhdContainerName'))]",
-                "[concat('https://', variables('uniqueStringArray')[3], variables('newStorageAccountSuffix'), '.blob.core.windows.net/', variables('vhdContainerName'))]",
-                "[concat('https://', variables('uniqueStringArray')[4], variables('newStorageAccountSuffix'), '.blob.core.windows.net/', variables('vhdContainerName'))]"
-              ],
-              "name": "[variables('osDiskName')]",
             },
             "imageReference": "[variables('imageReference')]"
           },

```

Det finns ingen uttrycklig egenskap i skalnings uppsättnings konfigurationen för om du vill använda en hanterad eller ohanterad disk. Skalnings uppsättningen vet vilken som ska användas baserat på de egenskaper som finns i lagrings profilen. Därför är det viktigt när du ändrar mallen för att se till att rätt egenskaper finns i lagrings profilen för skalnings uppsättningen.


## <a name="data-disks"></a>Datadiskar

Med ändringarna ovan använder skalnings uppsättningen Managed disks för OS-disken, men vad finns det för data diskar? Lägg till data diskar genom att lägga till egenskapen "data disks" under "storageProfile" på samma nivå som "osDisk". Egenskapens värde är en JSON-lista med objekt som var och en har egenskaper "LUN" (som måste vara unika per datadisk på en virtuell dator), "createOption" ("Empty" är för närvarande det enda alternativ som stöds) och "diskSizeGB" (storleken på disken i gigabyte; måste vara större än 0 och mindre än 1024) som i följande exempel:

```
"dataDisks": [
  {
    "lun": "1",
    "createOption": "empty",
    "diskSizeGB": "1023"
  }
]
```

Om du anger `n` diskar i den här matrisen hämtar varje virtuell dator i skalnings uppsättningen `n` data diskar. Observera dock att dessa data diskar är RAW-enheter. De formateras inte. Det är upp till kunden att ansluta, partitionera och Formatera diskarna innan de används. Alternativt kan du också ange `"managedDisk": { "storageAccountType": "Premium_LRS" }` i varje datadisk-objekt för att ange att det ska vara en Premium data-disk. Endast virtuella datorer med versaler eller gemener i VM-SKU: n kan använda Premium-diskar.

Mer information om hur du använder data diskar med skalnings uppsättningar finns i [den här artikeln](./virtual-machine-scale-sets-attached-disks.md).


## <a name="next-steps"></a>Nästa steg
Till exempel med Resource Manager-mallar med hjälp av skalnings uppsättningar söker du efter "VMSS" i [Azures snabb starts mallar GitHub lagrings platsen](https://github.com/Azure/azure-quickstart-templates).

För allmän information, se [huvud landnings sidan för skalnings uppsättningar](https://azure.microsoft.com/services/virtual-machine-scale-sets/).

