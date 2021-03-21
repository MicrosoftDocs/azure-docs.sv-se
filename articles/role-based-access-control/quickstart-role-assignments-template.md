---
title: 'Snabb start: tilldela en Azure-roll med hjälp av en Azure Resource Manager-mall – Azure RBAC'
description: Lär dig hur du beviljar åtkomst till Azure-resurser för en användare i resurs grupp omfånget med hjälp av Azure Resource Manager mallar och rollbaserad åtkomst kontroll i Azure (Azure RBAC).
services: role-based-access-control,azure-resource-manager
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.topic: quickstart
ms.custom: subject-armqs
ms.workload: identity
ms.date: 02/15/2021
ms.author: rolyon
ms.openlocfilehash: 6e8f194cd85a3c381bdabf206777a99dce3c29b6
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "100559268"
---
# <a name="quickstart-assign-an-azure-role-using-an-arm-template"></a>Snabb start: tilldela en Azure-roll med en ARM-mall

[Rollbaserad åtkomst kontroll i Azure (Azure RBAC)](overview.md) är hur du hanterar åtkomst till Azure-resurser. I den här snabb starten skapar du en resurs grupp och ger användaren åtkomst till att skapa och hantera virtuella datorer i resurs gruppen. I den här snabb starten används en Azure Resource Manager-mall (ARM-mall) för att ge åtkomst.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Om din miljö uppfyller förhandskraven och du är van att använda ARM-mallar väljer du knappen **Distribuera till Azure**. Mallen öppnas på Azure-portalen.

[![Distribuera till Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-rbac-builtinrole-resourcegroup%2Fazuredeploy.json)

## <a name="prerequisites"></a>Krav

Om du vill tilldela Azure-roller och ta bort roll tilldelningar måste du ha:

- Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.
- `Microsoft.Authorization/roleAssignments/write` och `Microsoft.Authorization/roleAssignments/delete` behörigheter, till exempel [administratör för användar åtkomst](built-in-roles.md#user-access-administrator) eller [ägare](built-in-roles.md#owner)
- Om du vill tilldela en roll måste du ange tre element: säkerhets objekt, roll definition och omfång. I den här snabb starten är säkerhets objekt en annan användare i din katalog, roll definitionen är [virtuell dator deltagare](built-in-roles.md#virtual-machine-contributor)och omfånget är en resurs grupp som du anger.

## <a name="review-the-template"></a>Granska mallen

Mallen som används i den här snabbstarten kommer från [Azure-snabbstartsmallar](https://azure.microsoft.com/resources/templates/101-rbac-builtinrole-resourcegroup/). Mallen har tre parametrar och avsnittet Resources. I avsnittet resurser ser du att det har tre element i en roll tilldelning: säkerhets objekt, roll definition och omfattning.

:::code language="json" source="~/quickstart-templates/101-rbac-builtinrole-resourcegroup/azuredeploy.json":::

Den resurs som definierats i mallen är:

- [Microsoft. Authorization/roleAssignments](/azure/templates/Microsoft.Authorization/roleAssignments)

## <a name="deploy-the-template"></a>Distribuera mallen

1. Logga in på [Azure-portalen](https://portal.azure.com).

1. Ta reda på vilken e-postadress som är kopplad till din Azure-prenumeration. Eller bestäm e-postadressen till en annan användare i din katalog.

1. Öppna Azure Cloud Shell för PowerShell.

1. Kopiera och klistra in följande skript i Cloud Shell.

    ```azurepowershell
    $resourceGroupName = Read-Host -Prompt "Enter a resource group name (i.e. ExampleGrouprg)"
    $emailAddress = Read-Host -Prompt "Enter an email address for a user in your directory"
    $location = Read-Host -Prompt "Enter a location (i.e. centralus)"
    
    $roleAssignmentName = New-Guid
    $principalId = (Get-AzAdUser -Mail $emailAddress).id
    $roleDefinitionId = (Get-AzRoleDefinition -name "Virtual Machine Contributor").id
    $templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-rbac-builtinrole-resourcegroup/azuredeploy.json"
    
    New-AzResourceGroup -Name $resourceGroupName -Location $location
    New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri -roleAssignmentName $roleAssignmentName -roleDefinitionID $roleDefinitionId -principalId $principalId
    ```

1. Ange ett resurs grupp namn, till exempel ExampleGrouprg.

1. Ange en e-postadress för dig själv eller en annan användare i din katalog.

1. Ange en plats för resurs gruppen, till exempel ett centralt objekt.

1. Om det behövs trycker du på RETUR för att köra kommandot New-AzResourceGroupDeployment.

    Kommandot [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) skapar en ny resurs grupp och kommandot [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment) distribuerar mallen för att lägga till roll tilldelningen.

    Du bör se utdata som liknar följande:

    ```azurepowershell
    PS> New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri -roleAssignmentName $roleAssignmentName -roleDefinitionID $roleDefinitionId -principalId $principalId
    
    DeploymentName          : azuredeploy
    ResourceGroupName       : ExampleGrouprg
    ProvisioningState       : Succeeded
    Timestamp               : 5/22/2020 9:01:30 PM
    Mode                    : Incremental
    TemplateLink            :
                              Uri            : https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-rbac-builtinrole-resourcegroup/azuredeploy.json
                              ContentVersion : 1.0.0.0
    
    Parameters              :
                              Name                  Type                       Value
                              ====================  =========================  ==========
                              roleAssignmentName    String                     {roleAssignmentName}
                              roleDefinitionID      String                     9980e02c-c2be-4d73-94e8-173b1dc7cf3c
                              principalId           String                     {principalId}
    
    Outputs                 :
    DeploymentDebugLogLevel :
    ```

## <a name="review-deployed-resources"></a>Granska distribuerade resurser

1. Öppna den resurs grupp som du skapade i Azure Portal.

1. Klicka på **åtkomst kontroll (IAM)** på den vänstra menyn.

1. Klicka på fliken **Rolltilldelningar**.

1. Verifiera att rollen **virtuell dator deltagare** har tilldelats den användare som du har angett.

   ![Ny rolltilldelning](./media/quickstart-role-assignments-template/role-assignment-portal.png)

## <a name="clean-up-resources"></a>Rensa resurser

Följ dessa steg om du vill ta bort roll tilldelningen och resurs gruppen du skapade.

1. Kopiera och klistra in följande skript i Cloud Shell.

    ```azurepowershell
    $emailAddress = Read-Host -Prompt "Enter the email address of the user with the role assignment to remove"
    $resourceGroupName = Read-Host -Prompt "Enter the resource group name to remove (i.e. ExampleGrouprg)"
    
    $principalId = (Get-AzAdUser -Mail $emailAddress).id
    
    Remove-AzRoleAssignment -ObjectId $principalId -RoleDefinitionName "Virtual Machine Contributor" -ResourceGroupName $resourceGroupName
    Remove-AzResourceGroup -Name $resourceGroupName
    ```
    
1. Ange e-postadressen för användaren med den roll tilldelning som ska tas bort.

1. Ange namnet på den resurs grupp som ska tas bort, till exempel ExampleGrouprg.

1. Om det behövs trycker du på RETUR för att köra kommandot Remove-AzResourceGroup.

1. Ange **Y** för att bekräfta att du vill ta bort resurs gruppen.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Självstudie: ge en användare åtkomst till Azure-resurser med hjälp av Azure PowerShell](tutorial-role-assignments-user-powershell.md)