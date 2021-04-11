---
title: Referera till ett befintligt virtuellt nätverk i en Azure Scale set-mall
description: Lär dig hur du lägger till ett virtuellt nätverk i en befintlig mall för skalnings uppsättningar för virtuella Azure-datorer
author: ju-shim
ms.author: jushiman
ms.topic: how-to
ms.service: virtual-machine-scale-sets
ms.subservice: networking
ms.date: 03/30/2021
ms.reviewer: mimckitt
ms.custom: mimckitt
ms.openlocfilehash: f15fddc54f4b7c5a03843da1bcc11d1991b70d02
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/31/2021
ms.locfileid: "106076674"
---
# <a name="reference-an-existing-virtual-network-in-an-azure-scale-set-template"></a>Referera till ett befintligt virtuellt nätverk i en Azure Scale set-mall

Den här artikeln visar hur du ändrar den [grundläggande skalnings uppsättnings mal len](virtual-machine-scale-sets-mvss-start.md) så att den distribueras till ett befintligt virtuellt nätverk i stället för att skapa en ny.

## <a name="prerequisites"></a>Förutsättningar

I en föregående artikel har vi skapat en [grundläggande mall för skalnings uppsättningar](virtual-machine-scale-sets-mvss-start.md). Du kommer att behöva den tidigare mallen så att du kan ändra den för att skapa en mall som distribuerar en skalnings uppsättning i ett befintligt virtuellt nätverk.

## <a name="identify-subnet"></a>Identifiera undernät

Lägg först till en `subnetId` parameter. Den här strängen skickas till konfigurationen för skalnings uppsättningar, så att skalnings uppsättningen kan identifiera det förskapade under nätet för att distribuera virtuella datorer till. Strängen måste ha formatet:

`/subscriptions/<subscription-id>resourceGroups/<resource-group-name>/providers/Microsoft.Network/virtualNetworks/<virtual-network-name>/subnets/<subnet-name>`


Om du till exempel vill distribuera skalnings uppsättningen till ett befintligt virtuellt nätverk med namn `myvnet` , undernät `mysubnet` , resurs grupp `myrg` och prenumeration `00000000-0000-0000-0000-000000000000` skulle subnetId vara: 

`/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myrg/providers/Microsoft.Network/virtualNetworks/myvnet/subnets/mysubnet`.

```diff
      },
      "adminPassword": {
        "type": "securestring"
+    },
+    "subnetId": {
+      "type": "string"
      }
    },
```

## <a name="delete-extra-virtual-network-resource"></a>Ta bort extra virtuell nätverks resurs

Ta sedan bort den virtuella nätverks resursen från `resources` matrisen, när du använder ett befintligt virtuellt nätverk och inte behöver distribuera ett nytt.

```diff
    "variables": {},
    "resources": [
-    {
-      "type": "Microsoft.Network/virtualNetworks",
-      "name": "myVnet",
-      "location": "[resourceGroup().location]",
-      "apiVersion": "2018-11-01",
-      "properties": {
-        "addressSpace": {
-          "addressPrefixes": [
-            "10.0.0.0/16"
-          ]
-        },
-        "subnets": [
-          {
-            "name": "mySubnet",
-            "properties": {
-              "addressPrefix": "10.0.0.0/16"
-            }
-          }
-        ]
-      }
-    },
```
## <a name="remove-dependency-clause"></a>Ta bort beroende sats

Det virtuella nätverket finns redan innan mallen distribueras, så det finns inget behov av att ange en `dependsOn` sats från skalnings uppsättningen till det virtuella nätverket. Ta bort följande rader:

```diff
      {
        "type": "Microsoft.Compute/virtualMachineScaleSets",
        "name": "myScaleSet",
        "location": "[resourceGroup().location]",
        "apiVersion": "2019-03-01",
-      "dependsOn": [
-        "Microsoft.Network/virtualNetworks/myVnet"
-      ],
        "sku": {
          "name": "Standard_A1",
          "capacity": 2
```

## <a name="pass-subnet-parameter"></a>Skicka under näts parameter

Skicka slutligen i den `subnetId` parameter som angetts av användaren (i stället för `resourceId` att hämta ID: t för ett VNet i samma distribution, vilket är den grundläggande mallen för den grundläggande skalnings uppsättningen.).

```diff
                        "name": "myIpConfig",
                        "properties": {
                          "subnet": {
-                          "id": "[concat(resourceId('Microsoft.Network/virtualNetworks', 'myVnet'), '/subnets/mySubnet')]"
+                          "id": "[parameters('subnetId')]"
                          }
                        }
                      }
```


## <a name="next-steps"></a>Nästa steg

[!INCLUDE [mvss-next-steps-include](../../includes/mvss-next-steps.md)]
