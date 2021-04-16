---
title: Ange resursgrupp för virtuella datorer i Azure DevTest Labs | Microsoft Docs
description: Lär dig hur du anger en resursgrupp för virtuella datorer i ett labb i Azure DevTest Labs.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: c6f576a20fc8fada9dd515e8ba2a266761a3e586
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2021
ms.locfileid: "107377496"
---
# <a name="specify-a-resource-group-for-lab-virtual-machines-in-azure-devtest-labs"></a>Ange en resursgrupp för virtuella labbdatorer i Azure DevTest Labs

Som labbägare kan du konfigurera dina virtuella labbdatorer så att de skapas i en specifik resursgrupp. Den här funktionen hjälper dig i följande scenarier:

- Ha färre resursgrupper som skapats av labb i din prenumeration.
- Få dina labb att fungera inom en fast uppsättning resursgrupper som du konfigurerar.
- Gå runt begränsningar och godkännanden som krävs för att skapa resursgrupper i din Azure-prenumeration.
- Konsolidera alla labbresurser i en enda resursgrupp för att förenkla spårningen av resurserna och tillämpa [principer](../governance/policy/overview.md) för att hantera resurser på resursgruppsnivå.

Med den här funktionen kan du använda ett skript för att ange en ny eller befintlig resursgrupp i din Azure-prenumeration för alla dina virtuella labb datorer. För närvarande Azure DevTest Labs den här funktionen via ett API.

> [!NOTE]
> Alla prenumerationsgränser gäller när du skapar labb i DevTest Labs. Tänk på ett labb som en annan resurs i din prenumeration. När det gäller resursgrupper är gränsen [980 resursgrupper per prenumeration](../azure-resource-manager/management/azure-subscription-service-limits.md#subscription-limits). 

## <a name="use-azure-portal"></a>Använda Azure-portalen
Följ de här stegen för att ange en resursgrupp för alla virtuella datorer som skapats i labbet. 

1. Logga in på [Azure-portalen](https://portal.azure.com).
2. Välj **Alla tjänster** på den vänstra navigeringsmenyn. 
3. Välj **DevTest Labs** från listan.
4. I listan över labb väljer du ditt **labb.**  
5. Välj **Konfiguration och principer** i **avsnittet** Inställningar på den vänstra menyn. 
6. Välj **Labbinställningar** på den vänstra menyn. 
7. Välj **Alla virtuella datorer i en resursgrupp.** 
8. Välj en befintlig resursgrupp i listrutan (eller) välj  **Skapa** ny, ange ett namn för resursgruppen och välj **OK.** 

    ![Välj resursgrupp för alla virtuella labb datorer](./media/resource-group-control/select-resource-group.png)

## <a name="use-powershell"></a>Använd PowerShell 
I följande exempel visas hur du använder ett PowerShell-skript för att skapa alla virtuella labbdatorer i en ny resursgrupp.

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

Anropa skriptet med hjälp av följande kommando. ResourceGroup.ps1 är filen som innehåller föregående skript:

```powershell
.\ResourceGroup.ps1 -subId <subscriptionID> -labRg <labRGNAme> -labName <LanName> -vmRg <RGName> 
```

## <a name="use-an-azure-resource-manager-template"></a>Använda en Azure Resource Manager mall
Om du använder en Azure Resource Manager-mall för att skapa ett labb använder du egenskapen **vmCreationResourceGroupId** i avsnittet labbegenskaper i mallen, enligt följande exempel:

```json
        {
            "type": "microsoft.devtestlab/labs",
            "name": "[parameters('lab_name')]",
            "apiVersion": "2018-10-15-preview",
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


## <a name="api-to-configure-a-resource-group-for-lab-vms"></a>API för att konfigurera en resursgrupp för virtuella labb datorer
Du har följande alternativ som labbägare när du använder det här API:et:

- Välj **labbets resursgrupp för** alla virtuella datorer.
- Välj en **befintlig resursgrupp** förutom labbets resursgrupp för alla virtuella datorer.
- Ange ett **nytt resursgruppnamn** för alla virtuella datorer.
- Fortsätt att använda det befintliga beteendet, där en resursgrupp skapas för varje virtuell dator i labbet.
 
Den här inställningen gäller för nya virtuella datorer som skapats i labbet. De äldre virtuella datorerna i labbet som har skapats i egna resursgrupper påverkas inte. Miljöer som skapas i ditt labb fortsätter att finnas kvar i sina egna resursgrupper.

Så här använder du det här API:et:
- Använd API-version **2018-10-15-preview**.
- Om du anger en ny resursgrupp måste du kontrollera att du har **skrivbehörighet för resursgrupper** i din prenumeration. Om du saknar skrivbehörighet kommer det inte att gå att skapa nya virtuella datorer i den angivna resursgruppen.
- När du använder API:et skickar du det **fullständiga resursgrupps-ID:t**. Exempel: `/subscriptions/<SubscriptionID>/resourceGroups/<ResourceGroupName>`. Kontrollera att resursgruppen finns i samma prenumeration som labbet. 


## <a name="next-steps"></a>Nästa steg
Se följande artiklar: 

- [Ange principer för ett labb](devtest-lab-set-lab-policy.md)
- [Vanliga frågor och svar](devtest-lab-faq.md)
