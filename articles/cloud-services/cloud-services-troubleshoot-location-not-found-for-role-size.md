---
title: Felsöka LocationNotFoundForRoleSize när du distribuerar en molntjänst (klassisk) till Azure | Microsoft Docs
description: Den här artikeln visar hur du löser ett LocationNotFoundForRoleSize-undantag när du distribuerar en molntjänst (klassisk) till Azure.
services: cloud-services
author: mamccrea
ms.author: mamccrea
ms.service: cloud-services
ms.topic: troubleshooting
ms.date: 02/22/2021
ms.openlocfilehash: 54af2387ec0ff6c8f86f96821baad17736e8d85b
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2021
ms.locfileid: "107877974"
---
# <a name="troubleshoot-locationnotfoundforrolesize-when-deploying-a-cloud-service-classic-to-azure"></a>Felsöka LocationNotFoundForRoleSize när du distribuerar en molntjänst (klassisk) till Azure

I den här artikeln felsöker du allokeringsfel där storleken på en virtuell dator (VM) inte är tillgänglig när du distribuerar en Azure-molntjänst (klassisk).

När du distribuerar instanser till en molntjänst (klassisk) eller lägger till nya webb- eller arbetsrollinstanser Microsoft Azure till beräkningsresurser.

Du kan ibland få fel under dessa åtgärder även innan du når gränsen för Azure-prenumerationen.

> [!TIP]
> Informationen kan också vara användbar när du planerar distributionen av dina tjänster.

## <a name="symptom"></a>Symptom

I Azure Portal du till molntjänsten (klassisk) och i sidopanelen väljer du *Åtgärdslogg (klassisk) för* att visa loggarna.

![Bild som visar bladet Åtgärdslogg (klassisk).](./media/cloud-services-troubleshoot-location-not-found-for-role-size/cloud-services-troubleshoot-allocation-logs.png)

När du granskar loggarna för din molntjänst (klassisk) visas följande undantag:

|Undantagstyp  |Felmeddelande  |
|---------|---------|
|LocationNotFoundForRoleSize     |Åtgärden `{Operation ID}` misslyckades: Den begärda VM-nivån är för närvarande inte tillgänglig i Region ( `{Region ID}` ) för den här prenumerationen. Försök med en annan nivå eller distribuera till en annan plats.".|

## <a name="cause"></a>Orsak

Det finns ett kapacitetsproblem med den region eller det kluster som du distribuerar till. *LocationNotFoundForRoleSize-undantaget* inträffar när den resurs-SKU som du har valt (VM-storlek) inte är tillgänglig för den angivna regionen.

## <a name="solution"></a>Lösning

I det här scenariot bör du välja en annan region eller SKU att distribuera molntjänsten (klassisk) till. Innan du distribuerar eller uppgraderar molntjänsten (klassisk) kan du avgöra vilka SKU:er som är tillgängliga i en region eller tillgänglighetszon. Följ [processerna Azure CLI,](#list-skus-in-region-using-azure-cli) [PowerShell](#list-skus-in-region-using-powershell) [REST API](#list-skus-in-region-using-rest-api) nedan.

### <a name="list-skus-in-region-using-azure-cli"></a>Visa en lista över SKU:er i en region med Hjälp av Azure CLI

Du kan använda [az vm list-skus](/cli/azure/vm?view=azure-cli-latest
#<a name="az_vm_list_skus-command"></a>az_vm_list_skus) kommandot .

- Använd `--location` parametern för att filtrera utdata till den plats som du använder.
- Använd `--size` parametern för att söka efter ett namn med partiell storlek.
- Mer information finns i guiden [Resolve error for SKU not available](../azure-resource-manager/templates/error-sku-not-available.md#solution-2---azure-cli) (Lösa fel för SKU som inte är tillgänglig).

    **Ett exempel:**

    ```azurecli
    az vm list-skus --location southcentralus --size Standard_F --output table
    ```

    **Exempelresultat:** ![ Azure CLI-utdata från körning av kommandot "az vm list-skus --location southcentralus --size Standard_F --output table" som visar tillgängliga SKU:er.](./media/cloud-services-troubleshoot-constrained-allocation-failed/cloud-services-troubleshoot-constrained-allocation-failed-1.png)

#### <a name="list-skus-in-region-using-powershell"></a>Visa en lista över SKU:er i en region med Hjälp av PowerShell

Du kan använda kommandot [Get-AzComputeResourceSku.](/powershell/module/az.compute/get-azcomputeresourcesku)

- Filtrera resultaten efter plats.
- Du måste ha den senaste versionen av PowerShell för det här kommandot.
- Mer information finns i guiden [Resolve error for SKU not available](../azure-resource-manager/templates/error-sku-not-available.md#solution-1---powershell) (Lösa fel för SKU som inte är tillgänglig).

**Ett exempel:**

```azurepowershell
Get-AzComputeResourceSku | where {$_.Locations -icontains "centralus"}
```

**Några andra användbara kommandon:**

Filtrera bort de platser som innehåller storlek (Standard_DS14_v2):

```azurepowershell
Get-AzComputeResourceSku | where {$_.Locations.Contains("centralus") -and $_.ResourceType.Contains("virtualMachines") -and $_.Name.Contains("Standard_DS14_v2")}
```

Filtrera bort alla platser som innehåller storlek (V3):

```azurepowershell
Get-AzComputeResourceSku | where {$_.Locations.Contains("centralus") -and $_.ResourceType.Contains("virtualMachines") -and $_.Name.Contains("v3")} | fc
```

#### <a name="list-skus-in-region-using-rest-api"></a>Visa en lista över SKU:er i en region med REST API

Du kan använda åtgärden [Resurs-SKU:er –](/rest/api/compute/resourceskus/list) lista. Den returnerar tillgängliga SKU:er och regioner i följande format:

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

För fler lösningar för allokeringsfel och för att bättre förstå hur de genereras:

> [!div class="nextstepaction"]
> [Allokeringsfel – molntjänst (klassisk)](cloud-services-allocation-failures.md)

Om ditt Azure-problem inte åtgärdas i den här artikeln går du till Azure-forumen på [MSDN och Stack Overflow](https://azure.microsoft.com/support/forums/). Du kan publicera ditt problem i dessa forum eller publicera det [ @AzureSupport på Twitter.](https://twitter.com/AzureSupport) Du kan också skicka en Azure-supportbegäran. Om du vill skicka en supportbegäran går du [till Azure-supportsidan](https://azure.microsoft.com/support/options/) och väljer *Få support.*
