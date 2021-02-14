---
title: Felsöka ConstrainedAllocationFailed när du distribuerar en moln tjänst till Azure | Microsoft Docs
description: Den här artikeln visar hur du löser ett ConstrainedAllocationFailed-undantag när du distribuerar en moln tjänst till Azure.
services: cloud-services
author: mibufo
ms.author: v-mibufo
ms.service: cloud-services
ms.topic: troubleshooting
ms.date: 02/04/2020
ms.openlocfilehash: de344bbcd89158676bacf2a8aa1743d282700b9d
ms.sourcegitcommit: e972837797dbad9dbaa01df93abd745cb357cde1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100521174"
---
# <a name="troubleshoot-constrainedallocationfailed-when-deploying-a-cloud-service-to-azure"></a>Felsöka ConstrainedAllocationFailed när du distribuerar en moln tjänst till Azure

I den här artikeln får du felsöka allokeringsfel där Azure Cloud Services inte kan distribuera på grund av begränsningar.

Microsoft Azure allokeras när du är:

- Uppgradera Cloud Services-instanser

- Lägga till nya webb-eller arbets roll instanser

- Distribuera instanser till en moln tjänst

Du kan ibland få fel under dessa åtgärder även innan du når gränsen för Azure-prenumerationen.

> [!TIP]
> Informationen kan också vara användbar när du planerar distributionen av dina tjänster.

## <a name="symptom"></a>Symptom

I Azure Portal går du till moln tjänsten och väljer *Åtgärds loggar (klassisk)* i marginal listen för att visa loggarna.

När du har granskat loggarna för din moln tjänst visas följande undantag:

|Undantags typ  |Felmeddelande  |
|---------|---------|
|ConstrainedAllocationFailed     |Azure-åtgärden `{Operation ID}` misslyckades med kod Compute. ConstrainedAllocationFailed. Information: allokeringen misslyckades; Det går inte att uppfylla begränsningarna i begäran. Den begärda nya tjänstdistributionen är kopplad till en tillhörighetsgrupp eller är riktad mot ett virtuellt nätverk, eller så finns det en befintlig distribution under den här värdbaserade tjänsten. Något av dessa villkor begränsar den nya distributionen till vissa Azure-resurser. Försök igen senare eller försök att minska storleken på den virtuella datorn eller antalet roll instanser. Alternativt kan du, om möjligt, ta bort de ovannämnda begränsningarna eller prova att distribuera till en annan region.|

## <a name="cause"></a>Orsak

Det finns ett kapacitets problem med den region eller det kluster som du distribuerar till. Det inträffar när resurs-SKU: n som du har valt inte är tillgänglig för den angivna platsen.

> [!NOTE]
> När den första noden i en moln tjänst distribueras *fästs* den på en resurspool. En resurspool kan vara ett enskilt kluster eller en grupp kluster.
>
> Med tiden kan resurserna i den här resurspoolen bli fullt utnyttjade. Om en moln tjänst gör en tilldelnings förfrågan för ytterligare resurser när det inte finns tillräckligt med resurser i den fasta resurspoolen, leder begäran till ett [allokeringsfel](cloud-services-allocation-failures.md).

## <a name="solution"></a>Lösning

I det här scenariot bör du välja en annan region eller SKU för att distribuera din moln tjänst till. Innan du distribuerar eller uppgraderar din moln tjänst kan du bestämma vilka SKU: er som är tillgängliga i en region eller tillgänglighets zon. Följ [Azure CLI](#list-skus-in-region-using-azure-cli)-, [PowerShell](#list-skus-in-region-using-powershell)-eller [rest Apis](#list-skus-in-region-using-rest-api) processerna nedan.

### <a name="list-skus-in-region-using-azure-cli"></a>Lista SKU: er i region med Azure CLI

Du kan använda kommandot [AZ VM List-SKU: er](https://docs.microsoft.com/cli/azure/vm.html#az_vm_list_skus) .

- Använd `--location` parametern för att filtrera utdata till den plats som du använder.
- Använd `--size` parametern för att söka efter ett namn för en partiell storlek.
- Mer information finns i guiden [lösa fel för SKU: n som inte är tillgänglig](../azure-resource-manager/templates/error-sku-not-available.md#solution-2---azure-cli) .

    **Exempel:**

    ```azurecli
    az vm list-skus --location southcentralus --size Standard_F --output table
    ```

    **Exempel resultat:** ![ Azure CLI-utdata för att köra kommandot "AZ VM List-SKU--location usasödracentrala--size Standard_F--output Table", som visar tillgängliga SKU: er.](./media/cloud-services-troubleshoot-constrained-allocation-failed/cloud-services-troubleshoot-constrained-allocation-failed-1.png)

#### <a name="list-skus-in-region-using-powershell"></a>Lista SKU: er i region med PowerShell

Du kan använda kommandot [Get-AzComputeResourceSku](https://docs.microsoft.com/powershell/module/az.compute/get-azcomputeresourcesku) .

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

Du kan använda [Resource SKU-List](https://docs.microsoft.com/rest/api/compute/resourceskus/list) åtgärden. Den returnerar tillgängliga SKU: er och regioner i följande format:

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
    <Rest_of_your_file_is_located_here...>
  ]
}
    
```

## <a name="next-steps"></a>Nästa steg

För fler lösningar för tilldelnings fel och för att bättre förstå hur de genereras:

> [!div class="nextstepaction"]
> [Allokeringsfel (moln tjänster)](cloud-services-allocation-failures.md)

Om ditt Azure-problem inte åtgärdas i den här artikeln kan du gå till Azure-forumen på [MSDN och Stack Overflow](https://azure.microsoft.com/support/forums/). Du kan publicera ditt problem i dessa forum eller publicera på [ @AzureSupport Twitter](https://twitter.com/AzureSupport). Du kan också skicka en support förfrågan för Azure. Om du vill skicka en supportbegäran väljer du *Hämta support* på sidan [Support för Azure](https://azure.microsoft.com/support/options/) .
