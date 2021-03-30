---
title: Referera till en anpassad avbildning i en mall för Azure-skalnings uppsättning
description: Lär dig hur du lägger till en anpassad avbildning i en befintlig mall för skalnings uppsättningar för virtuella Azure-datorer
author: cynthn
ms.author: cynthn
ms.topic: how-to
ms.service: virtual-machine-scale-sets
ms.subservice: imaging
ms.date: 04/26/2018
ms.reviewer: akjosh
ms.custom: akjosh
ms.openlocfilehash: 5ed9ee79dde73e738417031b928a675ea913179c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "83124915"
---
# <a name="add-a-custom-image-to-an-azure-scale-set-template"></a>Lägga till en anpassad avbildning till en skalningsuppsättningsmall i Azure

Den här artikeln visar hur du ändrar den [grundläggande skalnings uppsättnings mal len](virtual-machine-scale-sets-mvss-start.md) så att den distribueras från en anpassad avbildning.

## <a name="change-the-template-definition"></a>Ändra mal len definition
I en [föregående artikel](virtual-machine-scale-sets-mvss-start.md) har vi skapat en grundläggande mall för skalnings uppsättningar. Vi kommer nu att använda den tidigare mallen och ändra den för att skapa en mall som distribuerar en skalnings uppsättning från en anpassad avbildning.  

### <a name="creating-a-managed-disk-image"></a>Skapa en hanterad disk avbildning

Om du redan har en anpassad hanterad disk avbildning (en resurs av typen `Microsoft.Compute/images` ) kan du hoppa över det här avsnittet.

Lägg först till en `sourceImageVhdUri` parameter, som är URI: n till den generaliserade blobben i Azure Storage som innehåller den anpassade avbildningen som ska distribueras från.


```diff
     },
     "adminPassword": {
       "type": "securestring"
+    },
+    "sourceImageVhdUri": {
+      "type": "string",
+      "metadata": {
+        "description": "The source of the generalized blob containing the custom image"
+      }
     }
   },
   "variables": {},
```

Lägg sedan till en resurs av typen `Microsoft.Compute/images` , som är den hanterade disk avbildningen baserat på den generaliserade blobben som finns på URI `sourceImageVhdUri` . Den här bilden måste vara i samma region som den skalnings uppsättning som använder den. I egenskaperna för avbildningen anger du OS-typen, platsen för blobben (från `sourceImageVhdUri` parametern) och lagrings konto typen:

```diff
   "resources": [
     {
+      "type": "Microsoft.Compute/images",
+      "apiVersion": "2019-03-01",
+      "name": "myCustomImage",
+      "location": "[resourceGroup().location]",
+      "properties": {
+        "storageProfile": {
+          "osDisk": {
+            "osType": "Linux",
+            "osState": "Generalized",
+            "blobUri": "[parameters('sourceImageVhdUri')]",
+            "storageAccountType": "Standard_LRS"
+          }
+        }
+      }
+    },
+    {
       "type": "Microsoft.Network/virtualNetworks",
       "name": "myVnet",
       "location": "[resourceGroup().location]",

```

I skalnings uppsättnings resursen lägger du till en `dependsOn` sats som refererar till den anpassade avbildningen för att se till att avbildningen skapas innan skalnings uppsättningen försöker distribuera från avbildningen:

```diff
       "location": "[resourceGroup().location]",
       "apiVersion": "2019-03-01-preview",
       "dependsOn": [
-        "Microsoft.Network/virtualNetworks/myVnet"
+        "Microsoft.Network/virtualNetworks/myVnet",
+        "Microsoft.Compute/images/myCustomImage"
       ],
       "sku": {
         "name": "Standard_A1",

```

### <a name="changing-scale-set-properties-to-use-the-managed-disk-image"></a>Ändra skalnings uppsättnings egenskaper till att använda den hanterade disk avbildningen

I i `imageReference` skalnings uppsättningen `storageProfile` , i stället för att ange utgivare, erbjudande, SKU och version för en plattforms avbildning, anger du `id` för `Microsoft.Compute/images` resursen:

```json
         "virtualMachineProfile": {
           "storageProfile": {
             "imageReference": {
              "id": "[resourceId('Microsoft.Compute/images', 'myCustomImage')]"
             }
           },
           "osProfile": {
```

I det här exemplet använder du `resourceId` funktionen för att hämta resurs-ID för avbildningen som skapats i samma mall. Om du har skapat den hanterade disk avbildningen i förväg bör du ange ID: t för avbildningen i stället. Detta ID måste ha formatet: `/subscriptions/<subscription-id>resourceGroups/<resource-group-name>/providers/Microsoft.Compute/images/<image-name>` .


## <a name="next-steps"></a>Nästa steg

[!INCLUDE [mvss-next-steps-include](../../includes/mvss-next-steps.md)]
