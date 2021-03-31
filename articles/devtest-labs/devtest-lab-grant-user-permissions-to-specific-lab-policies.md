---
title: Bevilja användar behörighet till vissa labb principer | Microsoft Docs
description: Lär dig hur du beviljar användar behörigheter till vissa labb principer i DevTest Labs baserat på varje användares behov
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 976862476d25e4e9a4933d8a5319eec9d77ca39b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "92328478"
---
# <a name="grant-user-permissions-to-specific-lab-policies"></a>Bevilja användar behörighet till vissa labb principer
## <a name="overview"></a>Översikt
Den här artikeln beskriver hur du använder PowerShell för att ge användare behörighet till en viss labb princip. På så sätt kan behörigheter tillämpas baserat på varje användares behov. Du kanske till exempel vill ge en viss användare möjlighet att ändra inställningarna för den virtuella dator principen, men inte kostnads principerna.

## <a name="policies-as-resources"></a>Principer som resurser
Som beskrivs i artikeln [Azure-rollbaserad åtkomst kontroll (Azure RBAC)](../role-based-access-control/role-assignments-portal.md) kan du använda Azure RBAC för detaljerad åtkomst hantering av resurser för Azure. Med hjälp av Azure RBAC kan du åtskilja uppgifter i ditt DevOps-team och endast bevilja åtkomst till användare som de behöver för att utföra sina jobb.

I DevTest Labs är en princip en resurs typ som aktiverar Azure RBAC **-åtgärden Microsoft. DevTestLab/Labs/policySets/policies/**. Varje labb princip är en resurs i princip resurs typ och kan tilldelas som ett omfång till en Azure-roll.

För att till exempel ge användare läs-/skriv behörighet till principen för **tillåten VM-storlek** skapar du en anpassad roll som fungerar med **Microsoft. DevTestLab/Labs/policySets/policys/** action och tilldelar sedan lämpliga användare till den här anpassade rollen i omfånget **Microsoft. DevTestLab/Labs/PolicySets/policies/AllowedVmSizesInLab**.

Mer information om anpassade roller i Azure RBAC finns i de [anpassade Azure-rollerna](../role-based-access-control/custom-roles.md).

## <a name="creating-a-lab-custom-role-using-powershell"></a>Skapa en anpassad labb roll med PowerShell
Innan du kan komma igång måste du [installera Azure PowerShell](/powershell/azure/install-az-ps). 

När du har konfigurerat Azure PowerShell-cmdlet: ar kan du utföra följande uppgifter:

* Visa en lista över alla åtgärder/åtgärder för en resurs leverantör
* Lista åtgärder i en viss roll:
* Skapa en anpassad roll

Följande PowerShell-skript visar exempel på hur du utför dessa uppgifter:

```azurepowershell
# List all the operations/actions for a resource provider.
Get-AzProviderOperation -OperationSearchString "Microsoft.DevTestLab/*"

# List actions in a particular role.
(Get-AzRoleDefinition "DevTest Labs User").Actions

# Create custom role.
$policyRoleDef = (Get-AzRoleDefinition "DevTest Labs User")
$policyRoleDef.Id = $null
$policyRoleDef.Name = "Policy Contributor"
$policyRoleDef.IsCustom = $true
$policyRoleDef.AssignableScopes.Clear()
$policyRoleDef.AssignableScopes.Add("/subscriptions/<SubscriptionID> ")
$policyRoleDef.Actions.Add("Microsoft.DevTestLab/labs/policySets/policies/*")
$policyRoleDef = (New-AzRoleDefinition -Role $policyRoleDef)
```

## <a name="assigning-permissions-to-a-user-for-a-specific-policy-using-custom-roles"></a>Tilldela behörigheter till en användare för en speciell princip med hjälp av anpassade roller
När du har definierat dina anpassade roller kan du tilldela dem till användare. För att kunna tilldela en anpassad roll till en användare måste du först hämta **ObjectID** som representerar användaren. Det gör du med hjälp av cmdleten **Get-AzADUser** .

I följande exempel är **ObjectID** för *SomeUser* -användaren 05DEFF7B-0AC3-4ABF-B74D-6A72CD5BF3F3.

```azurepowershell
PS C:\>Get-AzADUser -SearchString "SomeUser"

DisplayName                    Type                           ObjectId
-----------                    ----                           --------
someuser@hotmail.com                                          05DEFF7B-0AC3-4ABF-B74D-6A72CD5BF3F3
```

När du har **ObjectID** för användaren och ett namn på en anpassad roll kan du tilldela rollen till användaren med cmdleten **New-AzRoleAssignment** :

```azurepowershell
PS C:\>New-AzRoleAssignment -ObjectId 05DEFF7B-0AC3-4ABF-B74D-6A72CD5BF3F3 -RoleDefinitionName "Policy Contributor" -Scope /subscriptions/<SubscriptionID>/resourceGroups/<ResourceGroupName>/providers/Microsoft.DevTestLab/labs/<LabName>/policySets/default/policies/AllowedVmSizesInLab
```

I det tidigare exemplet används **AllowedVmSizesInLab** -principen. Du kan använda någon av följande principer:

* MaxVmsAllowedPerUser
* MaxVmsAllowedPerLab
* AllowedVmSizesInLab
* LabVmsShutdown

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>Nästa steg
När du har beviljat användar behörigheter till vissa labb principer kan du använda följande steg för att tänka på:

* [Säker åtkomst till ett labb](devtest-lab-add-devtest-user.md)
* [Ange labb principer](devtest-lab-set-lab-policy.md)
* [Skapa en labb-mall](devtest-lab-create-template.md)
* [Skapa anpassade artefakter för dina virtuella datorer](devtest-lab-artifact-author.md)
* [Lägga till en virtuell dator i ett labb](devtest-lab-add-vm.md)
