---
title: Felsöka LocationNotFoundForRoleSize när du distribuerar en moln tjänst (klassisk) till Azure | Microsoft Docs
description: Den här artikeln visar hur du löser ett LocationNotFoundForRoleSize-undantag när du distribuerar en moln tjänst (klassisk) till Azure.
services: cloud-services
author: mibufo
ms.author: v-mibufo
ms.service: cloud-services
ms.topic: troubleshooting
ms.date: 02/22/2021
ms.openlocfilehash: 3d1a1135db0421d89d4c6c9f278c86e02cb5cb32
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2021
ms.locfileid: "105558966"
---
# <a name="troubleshoot-locationnotfoundforrolesize-when-deploying-a-cloud-service-classic-to-azure"></a>Felsöka LocationNotFoundForRoleSize när du distribuerar en moln tjänst (klassisk) till Azure

I den här artikeln får du felsöka allokeringsfel där en storlek på en virtuell dator (VM) inte är tillgänglig när du distribuerar en Azure Cloud Service (klassisk).

När du distribuerar instanser till en moln tjänst (klassisk) eller lägger till nya webb-eller arbets Rolls instanser, allokerar Microsoft Azure beräknings resurser.

Du kan ibland få fel under dessa åtgärder även innan du når gränsen för Azure-prenumerationen.

> [!TIP]
> Informationen kan också vara användbar när du planerar distributionen av dina tjänster.

## <a name="symptom"></a>Symptom

I Azure Portal navigerar du till din moln tjänst (klassisk) och i panelen Välj *Åtgärds logg (klassisk)* för att visa loggarna.

![Bild visar bladet åtgärds logg (klassisk).](./media/cloud-services-troubleshoot-location-not-found-for-role-size/cloud-services-troubleshoot-allocation-logs.png)

När du har granskat loggarna för din moln tjänst (klassisk) visas följande undantag:

|Undantags typ  |Felmeddelande  |
|---------|---------|
|LocationNotFoundForRoleSize     |Åtgärden `{Operation ID}` misslyckades: den begärda VM-nivån är för närvarande inte tillgänglig i region ( `{Region ID}` ) för den här prenumerationen. Försök med en annan nivå eller distribuera till en annan plats.|

## <a name="cause"></a>Orsak

Det finns ett kapacitets problem med den region eller det kluster som du distribuerar till. *LocationNotFoundForRoleSize* -undantaget inträffar när resurs-SKU: n du har valt (VM-storlek) inte är tillgänglig för den angivna regionen.

## <a name="solution"></a>Lösning

I det här scenariot bör du välja en annan region eller SKU för att distribuera din moln tjänst (klassisk) till. Innan du distribuerar eller uppgraderar din moln tjänst (klassisk) kan du bestämma vilka SKU: er som är tillgängliga i en region eller tillgänglighets zon. Följ [Azure CLI](#list-skus-in-region-using-azure-cli)-, [PowerShell](#list-skus-in-region-using-powershell)-eller [rest Apis](#list-skus-in-region-using-rest-api) processerna nedan.

### <a name="list-skus-in-region-using-azure-cli"></a>Lista SKU: er i region med Azure CLI

Du kan använda kommandot [AZ VM List-SKU: er](/cli/azure/vm.html#az_vm_list_skus) .

- Använd `--location` parametern för att filtrera utdata till den plats som du använder.
- Använd `--size` parametern för att söka efter ett namn för en partiell storlek.
- Mer information finns i guiden [lösa fel för SKU: n som inte är tillgänglig](../azure-resource-manager/templates/error-sku-not-available.md#solution-2---azure-cli) .

    **Exempel:**

    ```azurecli
    az vm list-skus --location southcentralus --size Standard_F --output table
    ```

    **Exempel resultat:** ![ Azure CLI-utdata för att köra kommandot "AZ VM List-SKU--location usasödracentrala--size Standard_F--output Table", som visar tillgängliga SKU: er.](./media/cloud-services-troubleshoot-constrained-allocation-failed/cloud-services-troubleshoot-constrained-allocation-failed-1.png)

#### <a name="list-skus-in-region-using-powershell"></a>Lista SKU: er i region med PowerShell

Du kan använda kommandot [Get-AzComputeResourceSku](/powershell/module/az.compute/get-azcomputeresourcesku) .

- Filtrera resultaten efter plats.
- Du måste ha den senaste versionen av PowerShell för det här kommandot.
- Mer information finns i guiden [lösa fel för SKU: n som inte är tillgänglig](../azure-resource-manager/templates/error-sku-not-available.md#solution-1---powershell) .

**Exempel:**

```azurepowershell
Get-AzComputeResourceSku | where {$_.Locations -icontains "centralus"}
```

**Några andra användbara kommandon:**

Filtrera bort de platser som innehåller storlek (Standard_DS14_v2):

```azurepowershell
Get-AzComputeResourceSku | where {$_.Locations.Contains("centralus") -and $_.ResourceType.Contains("virtualMachines") -and $_.Name.Contains("Standard_DS14_v2")}
```

Filtrera bort alla platser som innehåller storlek (v3):

```azurepowershell
Get-AzComputeResourceSku | where {$_.Locations.Contains("centralus") -and $_.ResourceType.Contains("virtualMachines") -and $_.Name.Contains("v3")} | fc
```

#### <a name="list-skus-in-region-using-rest-api"></a>Lista SKU: er i region med REST API

Du kan använda [Resource SKU-List](/rest/api/compute/resourceskus/list) åtgärden. Den returnerar tillgängliga SKU: er och regioner i följande format:

```json
{
  "value": [
    {
      "resourceType": "virtualMachines",
      "name": "Standard_A0",
      "tier": "Standard",
      "size": "A0",
      "locations": [
        "eastus"
      ],
      "restrictions": []
    },
    {
      "resourceType": "virtualMachines",
      "name": "Standard_A1",
      "tier": "Standard",
      "size": "A1",
      "locations": [
        "eastus"
      ],
      "restrictions": []
    },
      <<The rest of your file is located here>>
  ]
}
    
```

## <a name="next-steps"></a>Nästa steg

För fler lösningar för tilldelnings fel och för att bättre förstå hur de genereras:

> [!div class="nextstepaction"]
> [Allokeringsfel – moln tjänst (klassisk)](cloud-services-allocation-failures.md)

Om ditt Azure-problem inte åtgärdas i den här artikeln kan du gå till Azure-forumen på [MSDN och Stack Overflow](https://azure.microsoft.com/support/forums/). Du kan publicera ditt problem i dessa forum eller publicera på [ @AzureSupport Twitter](https://twitter.com/AzureSupport). Du kan också skicka en support förfrågan för Azure. Om du vill skicka en supportbegäran väljer du *Hämta support* på sidan [Support för Azure](https://azure.microsoft.com/support/options/) .