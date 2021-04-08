---
title: Uppdatera hanterade resurser
description: Beskriver hur du arbetar med resurser i den hanterade resurs gruppen för ett Azure-hanterat program.
author: tfitzmac
ms.topic: conceptual
ms.date: 10/26/2017
ms.author: tomfitz
ms.openlocfilehash: 3dbb88d53a961e277e102d7eec580309951c73b6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "75651220"
---
# <a name="work-with-resources-in-the-managed-resource-group-for-azure-managed-application"></a>Arbeta med resurser i den hanterade resurs gruppen för Azure-hanterat program

Den här artikeln beskriver hur du uppdaterar resurser som distribueras som en del av ett hanterat program. Som utgivare av ett hanterat program har du åtkomst till resurserna i den hanterade resurs gruppen. Om du vill uppdatera resurserna måste du hitta den hanterade resurs grupp som är associerad med ett hanterat program och få åtkomst till resursen i den resurs gruppen.

Den här artikeln förutsätter att du har distribuerat det hanterade programmet i det [hanterade webb programmet (IaaS) med exempel projekt för Azure Management Services](https://github.com/Azure/azure-managedapp-samples/tree/master/Managed%20Application%20Sample%20Packages/201-managed-web-app) . Det hanterade programmet innehåller en **Standard_D1_v2** virtuell dator. Om du inte har distribuerat det hanterade programmet kan du fortfarande använda den här artikeln för att bekanta dig med stegen för att uppdatera en hanterad resurs grupp.

Följande bild visar det distribuerade hanterade programmet.

![Distribuerat hanterat program](./media/update-managed-resources/deployed.png)

I den här artikeln använder du Azure CLI för att:

* Identifiera det hanterade programmet
* Identifiera den hanterade resurs gruppen
* Identifiera den eller de virtuella dator resurserna i den hanterade resurs gruppen
* Ändra storleken på den virtuella datorn (antingen till en mindre storlek om den inte används eller större för att stödja mer belastning)
* Tilldela en princip till den hanterade resurs gruppen som anger tillåtna platser

## <a name="get-managed-application-and-managed-resource-group"></a>Hämta hanterat program och hanterad resurs grupp

Använd följande för att hämta de hanterade programmen i en resurs grupp:

```azurecli-interactive
az managedapp list --query "[?contains(resourceGroup,'DemoApp')]"
```

Använd följande för att hämta ID: t för den hanterade resurs gruppen:

```azurecli-interactive
az managedapp list --query "[?contains(resourceGroup,'DemoApp')].{ managedResourceGroup:managedResourceGroupId }"
```

## <a name="resize-vms-in-managed-resource-group"></a>Ändra storlek på virtuella datorer i hanterad resurs grupp

Ange namnet på den hanterade resurs gruppen om du vill se de virtuella datorerna i den hanterade resurs gruppen.

```azurecli-interactive
az vm list -g DemoApp6zkevchqk7sfq --query "[].{VMName:name,OSType:storageProfile.osDisk.osType,VMSize:hardwareProfile.vmSize}"
```

Om du vill uppdatera storleken på de virtuella datorerna använder du:

```azurecli-interactive
az vm resize --size Standard_D2_v2 --ids $(az vm list -g DemoApp6zkevchqk7sfq --query "[].id" -o tsv)
```

När åtgärden har slutförts kontrollerar du att programmet körs på standard D2 v2.

![Hanterat program med standard D2 v2](./media/update-managed-resources/upgraded.png)

## <a name="apply-policy-to-managed-resource-group"></a>Använd princip för hanterad resurs grupp

Hämta den hanterade resurs gruppen och tilldela en princip till denna omfattning. Principen **e56962a6-4747-49cd-b67b-bf8b01975c4c** är en inbyggd princip för att ange tillåtna platser.

```azurecli-interactive
managedGroup=$(az managedapp show --name <app-name> --resource-group DemoApp --query managedResourceGroupId --output tsv)

az policy assignment create --name locationAssignment --policy e56962a6-4747-49cd-b67b-bf8b01975c4c --scope $managedGroup --params '{
                            "listofallowedLocations": {
                                "value": [
                                    "northeurope",
                                    "westeurope"
                                ]
                            }
                        }'
```

Använd följande för att se de tillåtna platserna:

```azurecli-interactive
az policy assignment show --name locationAssignment --scope $managedGroup --query parameters.listofallowedLocations.value
```

Princip tilldelningen visas i portalen.

![Visa princip tilldelning](./media/update-managed-resources/assignment.png)

## <a name="next-steps"></a>Nästa steg

* En introduktion till hanterade program finns i [Managed application overview](overview.md) (Översikt över hanterade program).
* Exempel projekt finns i [exempel projekt för Azure-hanterade program](sample-projects.md).
