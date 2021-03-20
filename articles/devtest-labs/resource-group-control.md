---
title: Ange resurs grupp för virtuella datorer i Azure DevTest Labs | Microsoft Docs
description: Lär dig hur du anger en resurs grupp för virtuella datorer i ett labb i Azure DevTest Labs.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 7b72048405d3025ca21b324b6ad3168dd0c9ac95
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "85483371"
---
# <a name="specify-a-resource-group-for-lab-virtual-machines-in-azure-devtest-labs"></a>Ange en resurs grupp för virtuella labb datorer i Azure DevTest Labs

Som labb ägare kan du konfigurera dina virtuella labb datorer så att de skapas i en speciell resurs grupp. Den här funktionen hjälper dig i följande scenarier:

- Har färre resurs grupper som skapats av labb i din prenumeration.
- Låt dina labb köras i en fast uppsättning resurs grupper som du konfigurerar.
- Undvik begränsningar och godkännanden som krävs för att skapa resurs grupper i din Azure-prenumeration.
- Konsolidera alla dina labb resurser i en enda resurs grupp för att förenkla spårning av dessa resurser och tillämpa [principer](../governance/policy/overview.md) för att hantera resurser på resurs grupps nivå.

Med den här funktionen kan du använda ett skript för att ange en ny eller befintlig resurs grupp i din Azure-prenumeration för alla dina virtuella labb datorer. För närvarande har Azure DevTest Labs stöd för den här funktionen via ett API.

> [!NOTE]
> Alla prenumerations begränsningar gäller när du skapar labb i DevTest Labs. Tänk på ett labb som andra resurser i din prenumeration. I händelse av resurs grupper är gränsen [980 resurs grupper per prenumeration](../azure-resource-manager/management/azure-subscription-service-limits.md#subscription-limits). 

## <a name="use-azure-portal"></a>Använda Azure-portalen
Följ dessa steg om du vill ange en resurs grupp för alla virtuella datorer som skapats i labbet. 

1. Logga in på [Azure-portalen](https://portal.azure.com).
2. Välj **alla tjänster** i den vänstra navigerings menyn. 
3. Välj **DevTest Labs** från listan.
4. I listan med labb väljer du ditt **labb**.  
5. Välj **konfiguration och principer** i avsnittet **Inställningar** på den vänstra menyn. 
6. Välj **Lab-inställningar** på den vänstra menyn. 
7. Välj **alla virtuella datorer i en resurs grupp**. 
8. Välj en befintlig resurs grupp i list rutan (eller) Välj **Skapa ny**, ange ett **namn** för resurs gruppen och välj **OK**. 

    ![Välj resurs grupp för alla virtuella labb datorer](./media/resource-group-control/select-resource-group.png)

## <a name="use-powershell"></a>Använd PowerShell 
I följande exempel visas hur du använder ett PowerShell-skript för att skapa alla virtuella labb datorer i en ny resurs grupp.

```powershell
[CmdletBinding()]
Param(
    $subId,
    $labRg,
    $labName,
    $vmRg
)

az login | out-null

az account set --subscription $subId | out-null

$rgId = "/subscriptions/"+$subId+"/resourceGroups/"+$vmRg

"Updating lab '$labName' with vm rg '$rgId'..."

az resource update -g $labRg -n $labName --resource-type "Microsoft.DevTestLab/labs" --api-version 2018-10-15-preview --set properties.vmCreationResourceGroupId=$rgId

"Done. New virtual machines will now be created in the resource group '$vmRg'."
```

Anropa skriptet med hjälp av följande kommando. ResourceGroup.ps1 är den fil som innehåller föregående skript:

```powershell
.\ResourceGroup.ps1 -subId <subscriptionID> -labRg <labRGNAme> -labName <LanName> -vmRg <RGName> 
```

## <a name="use-an-azure-resource-manager-template"></a>Använda en Azure Resource Manager mall
Om du använder en Azure Resource Manager mall för att skapa ett labb använder du egenskapen **vmCreationResourceGroupId** i avsnittet labb egenskaper i din mall, som du ser i följande exempel:

```json
        {
            "type": "microsoft.devtestlab/labs",
            "name": "[parameters('lab_name')]",
            "apiVersion": "2018_10_15_preview",
            "location": "eastus",
            "tags": {},
            "scale": null,
            "properties": {
                "vmCreationResourceGroupId": "/subscriptions/<SubscriptionID>/resourcegroups/<ResourceGroupName>",
                "labStorageType": "Premium",
                "premiumDataDisks": "Disabled",
                "provisioningState": "Succeeded",
                "uniqueIdentifier": "000000000f-0000-0000-0000-00000000000000"
            },
            "dependsOn": []
        },
```


## <a name="api-to-configure-a-resource-group-for-lab-vms"></a>API för att konfigurera en resurs grupp för virtuella labb datorer
Du har följande alternativ som labb ägare när du använder det här API: et:

- Välj **Labbets resurs grupp** för alla virtuella datorer.
- Välj en annan **resurs grupp** än Labbets resurs grupp för alla virtuella datorer.
- Ange ett **nytt resurs grupps** namn för alla virtuella datorer.
- Fortsätt att använda det befintliga beteendet, där en resurs grupp skapas för varje virtuell dator i labbet.
 
Den här inställningen gäller för nya virtuella datorer som skapats i labbet. De äldre virtuella datorerna i labbet som skapades i deras egna resurs grupper förblir opåverkade. Miljöer som skapas i ditt labb fortsätter att finnas kvar i sina egna resurs grupper.

Så här använder du detta API:
- Använd API-version **2018_10_15_preview**.
- Om du anger en ny resurs grupp måste du kontrol lera att du har **Skriv behörighet för resurs grupper** i din prenumeration. Om du saknar Skriv behörighet kommer du inte att kunna skapa nya virtuella datorer i den angivna resurs gruppen.
- Skicka i det **fullständiga resurs grupps-ID: t** när du använder API: et. Exempel: `/subscriptions/<SubscriptionID>/resourceGroups/<ResourceGroupName>`. Se till att resurs gruppen finns i samma prenumeration som labbet. 


## <a name="next-steps"></a>Nästa steg
Se följande artiklar: 

- [Ange principer för ett labb](devtest-lab-set-lab-policy.md)
- [Vanliga frågor och svar](devtest-lab-faq.md)
