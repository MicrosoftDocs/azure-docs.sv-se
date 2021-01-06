---
title: Lägga till eller ta bort Azure Role-tilldelningar med Azure PowerShell-Azure RBAC
description: Lär dig hur du beviljar åtkomst till Azure-resurser för användare, grupper, tjänstens huvud namn eller hanterade identiteter med hjälp av Azure PowerShell och rollbaserad åtkomst kontroll i Azure (Azure RBAC).
services: active-directory
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.topic: how-to
ms.workload: identity
ms.date: 11/25/2020
ms.author: rolyon
ms.openlocfilehash: 6eadba7d778a632896529f3c7c53886619d96444
ms.sourcegitcommit: 5e762a9d26e179d14eb19a28872fb673bf306fa7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/05/2021
ms.locfileid: "97901478"
---
# <a name="add-or-remove-azure-role-assignments-using-azure-powershell"></a>Lägga till eller ta bort Azure-rolltilldelningar med hjälp av Azure PowerShell

[!INCLUDE [Azure RBAC definition grant access](../../includes/role-based-access-control-definition-grant.md)] I den här artikeln beskrivs hur du tilldelar roller med hjälp av Azure PowerShell.

[!INCLUDE [az-powershell-update](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Förutsättningar

Om du vill lägga till eller ta bort roll tilldelningar måste du ha:

- `Microsoft.Authorization/roleAssignments/write` och `Microsoft.Authorization/roleAssignments/delete` behörigheter, till exempel [administratör för användar åtkomst](built-in-roles.md#user-access-administrator) eller [ägare](built-in-roles.md#owner)
- [PowerShell i Azure Cloud Shell](../cloud-shell/overview.md) eller [Azure PowerShell](/powershell/azure/install-az-ps)
- Det konto som du använder för att köra PowerShell-kommandot måste ha Microsoft Graph- `Directory.Read.All` behörighet.

## <a name="steps-to-add-a-role-assignment"></a>Steg för tillägg av en rolltilldelning

I Azure RBAC för att bevilja åtkomst lägger du till en roll tilldelning. En rolltilldelning består av tre delar: säkerhetsobjekt, rolldefinition och omfång. Följ dessa steg om du vill lägga till en roll tilldelning.

### <a name="step-1-determine-who-needs-access"></a>Steg 1: Bestäm vem som behöver åtkomst

Du kan tilldela en roll till en användare, grupp, tjänstens huvud namn eller en hanterad identitet. Om du vill lägga till en roll tilldelning kan du behöva ange det unika ID: t för objektet. ID: t har formatet: `11111111-1111-1111-1111-111111111111` . Du kan hämta ID: t med hjälp av Azure Portal eller Azure PowerShell.

**Användare**

För en Azure AD-användare hämtar du User Principal Name, till exempel *patlong \@ contoso.com* eller User Object ID. Du kan hämta objekt-ID genom att använda [Get-AzADUser](/powershell/module/az.resources/get-azaduser).

```azurepowershell
Get-AzADUser -StartsWith <userName>
(Get-AzADUser -DisplayName <userName>).id
```

**Grupper**

För en Azure AD-grupp behöver du grupp objekt-ID: t. Du kan hämta objekt-ID genom att använda [Get-AzADGroup](/powershell/module/az.resources/get-azadgroup).

```azurepowershell
Get-AzADGroup -SearchString <groupName>
(Get-AzADGroup -DisplayName <groupName>).id
```

**Tjänstens huvudnamn**

För ett Azure AD-tjänstens huvud namn (identitet som används av ett program) behöver du ett objekt-ID för tjänstens huvud namn. Du kan hämta objekt-ID genom att använda [Get-AzADServicePrincipal](/powershell/module/az.resources/get-azadserviceprincipal). För ett huvud namn för tjänsten använder du objekt-ID: t och **inte** program-ID: t.

```azurepowershell
Get-AzADServicePrincipal -SearchString <principalName>
(Get-AzADServicePrincipal -DisplayName <principalName>).id
```

**Hanterade identiteter**

För en systemtilldelad eller användardefinierad hanterad identitet behöver du objekt-ID: t. Du kan hämta objekt-ID genom att använda [Get-AzADServicePrincipal](/powershell/module/az.resources/get-azadserviceprincipal).

```azurepowershell
Get-AzADServicePrincipal -SearchString <principalName>
(Get-AzADServicePrincipal -DisplayName <principalName>).id
```
    
### <a name="step-2-find-the-appropriate-role"></a>Steg 2: hitta rätt roll

Behörigheter grupperas tillsammans i roller. Du kan välja från en lista över flera [inbyggda Azure-roller](built-in-roles.md) eller så kan du använda dina egna anpassade roller. Vi rekommenderar att du beviljar åtkomst med den minsta behörighet som krävs, så undvik att tilldela en bredare roll.

Om du vill visa en lista över roller och hämta det unika roll-ID: t kan du använda [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition).

```azurepowershell
Get-AzRoleDefinition | FT Name, IsCustom, Id
```

Så här visar du information om en viss roll.

```azurepowershell
Get-AzRoleDefinition <roleName>
```

Mer information finns i [lista över Azure-roll definitioner](role-definitions-list.md#azure-powershell).
 
### <a name="step-3-identify-the-needed-scope"></a>Steg 3: identifiera omfattningen som krävs

Azure tillhandahåller fyra nivåer av omfång: resurs, [resurs grupp](../azure-resource-manager/management/overview.md#resource-groups), prenumeration och [hanterings grupp](../governance/management-groups/overview.md). Vi rekommenderar att du beviljar åtkomst med den minsta behörighet som krävs, så undvik att tilldela en roll i ett större omfattning. Mer information om omfång finns i [förstå omfattning](scope-overview.md).

**Resursomfång**

För resurs omfång behöver du resurs-ID för resursen. Du hittar resurs-ID genom att titta på resursens egenskaper i Azure Portal. Ett resurs-ID har följande format.

```
/subscriptions/<subscriptionId>/resourcegroups/<resourceGroupName>/providers/<providerName>/<resourceType>/<resourceSubType>/<resourceName>
```

**Omfånget för resursgrupp**

För resurs grupp omfånget behöver du namnet på resurs gruppen. Du hittar namnet på sidan **resurs grupper** i Azure Portal eller så kan du använda [Get-AzResourceGroup](/powershell/module/az.resources/get-azresourcegroup).

```azurepowershell
Get-AzResourceGroup
```

**Prenumerations omfång** 

Du behöver prenumerations-ID för prenumerations omfång. Du hittar ID på sidan **prenumerationer** i Azure Portal eller så kan du använda [Get-AzSubscription](/powershell/module/az.accounts/get-azsubscription).

```azurepowershell
Get-AzSubscription
```

**Hanterings gruppens omfattning** 

För hanterings gruppens omfattning behöver du hanterings gruppens namn. Du hittar namnet på sidan **hanterings grupper** i Azure Portal eller så kan du använda [Get-AzManagementGroup](/powershell/module/az.resources/get-azmanagementgroup).

```azurepowershell
Get-AzManagementGroup
```
    
### <a name="step-4-add-role-assignment"></a>Steg 4: Lägg till roll tilldelning

Om du vill lägga till en roll tilldelning använder du kommandot [New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment) . Beroende på omfattningen har kommandot vanligt vis något av följande format.

**Resursomfång**

```azurepowershell
New-AzRoleAssignment -ObjectId <objectId> `
-RoleDefinitionName <roleName> `
-Scope /subscriptions/<subscriptionId>/resourcegroups/<resourceGroupName>/providers/<providerName>/<resourceType>/<resourceSubType>/<resourceName>
```

```azurepowershell
New-AzRoleAssignment -ObjectId <objectId> `
-RoleDefinitionId <roleId> `
-ResourceName <resourceName> `
-ResourceType <resourceType> `
-ResourceGroupName <resourceGroupName>
```

**Omfånget för resursgrupp**

```azurepowershell
New-AzRoleAssignment -SignInName <emailOrUserprincipalname> `
-RoleDefinitionName <roleName> `
-ResourceGroupName <resourceGroupName>
```

```azurepowershell
New-AzRoleAssignment -ObjectId <objectId> `
-RoleDefinitionName <roleName> `
-ResourceGroupName <resourceGroupName>
```

**Prenumerations omfång** 

```azurepowershell
New-AzRoleAssignment -SignInName <emailOrUserprincipalname> `
-RoleDefinitionName <roleName> `
-Scope /subscriptions/<subscriptionId>
```

```azurepowershell
New-AzRoleAssignment -ObjectId <objectId> `
-RoleDefinitionName <roleName> `
-Scope /subscriptions/<subscriptionId>
```

**Hanterings gruppens omfattning** 

```azurepowershell
New-AzRoleAssignment -SignInName <emailOrUserprincipalname> `
-RoleDefinitionName <roleName> `
-Scope /providers/Microsoft.Management/managementGroups/<groupName>
``` 

```azurepowershell
New-AzRoleAssignment -ObjectId <objectId> `
-RoleDefinitionName <roleName> `
-Scope /providers/Microsoft.Management/managementGroups/<groupName>
``` 
    
## <a name="add-role-assignment-examples"></a>Lägg till Roll tilldelnings exempel

#### <a name="add-role-assignment-for-all-blob-containers-in-a-storage-account-resource-scope"></a>Lägg till roll tilldelning för alla BLOB-behållare i ett lagrings konto resurs omfång

Tilldelar rollen [Storage BLOB data Contributor](built-in-roles.md#storage-blob-data-contributor) till ett huvud namn för tjänsten med objekt-ID *55555555-5555-5555-5555-555555555555* i en resurs omfattning för ett lagrings konto med namnet *storage12345*.

```azurepowershell
PS C:\> New-AzRoleAssignment -ObjectId 55555555-5555-5555-5555-555555555555 `
-RoleDefinitionName "Storage Blob Data Contributor" `
-Scope "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Example-Storage-rg/providers/Microsoft.Storage/storageAccounts/storage12345"

RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Example-Storage-rg/providers/Microsoft.Storage/storageAccounts/storage12345/providers/Microsoft.Authorization/roleAssignments/cccccccc-cccc-cccc-cccc-cccccccccccc
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Example-Storage-rg/providers/Microsoft.Storage/storageAccounts/storage12345
DisplayName        : example-identity
SignInName         :
RoleDefinitionName : Storage Blob Data Contributor
RoleDefinitionId   : ba92f5b4-2d11-453d-a403-e96b0029c9fe
ObjectId           : 55555555-5555-5555-5555-555555555555
ObjectType         : ServicePrincipal
CanDelegate        : False
```

#### <a name="add-role-assignment-for-a-specific-blob-container-resource-scope"></a>Lägg till roll tilldelning för en angiven resurs omfattning för BLOB container

Tilldelar rollen [Storage BLOB data Contributor](built-in-roles.md#storage-blob-data-contributor) till ett huvud namn för tjänsten med objekt-ID *55555555-5555-5555-5555-555555555555* i en resurs omfattning för en BLOB-behållare med namnet *BLOB-container-01*.

```azurepowershell
PS C:\> New-AzRoleAssignment -ObjectId 55555555-5555-5555-5555-555555555555 `
-RoleDefinitionName "Storage Blob Data Contributor" `
-Scope "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Example-Storage-rg/providers/Microsoft.Storage/storageAccounts/storage12345/blobServices/default/containers/blob-container-01"

RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Example-Storage-rg/providers/Microsoft.Storage/storageAccounts/storage12345/blobServices/default/containers/blob-container-01/providers/Microsoft.Authorization/roleAssignm
                     ents/dddddddd-dddd-dddd-dddd-dddddddddddd
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Example-Storage-rg/providers/Microsoft.Storage/storageAccounts/storage12345/blobServices/default/containers/blob-container-01
DisplayName        : example-identity
SignInName         :
RoleDefinitionName : Storage Blob Data Contributor
RoleDefinitionId   : ba92f5b4-2d11-453d-a403-e96b0029c9fe
ObjectId           : 55555555-5555-5555-5555-555555555555
ObjectType         : ServicePrincipal
CanDelegate        : False
```

#### <a name="add-role-assignment-for-a-group-in-a-specific-virtual-network-resource-scope"></a>Lägg till roll tilldelning för en grupp i ett angivet virtuellt nätverks resurs omfång

Tilldelar rollen [virtuell dator deltagare](built-in-roles.md#virtual-machine-contributor) till gruppen *Pharma Sales admins* med ID aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa vid en resurs omfattning för ett virtuellt nätverk med namnet *Pharma-Sales-Project-Network*.

```azurepowershell
PS C:\> New-AzRoleAssignment -ObjectId aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa `
-RoleDefinitionName "Virtual Machine Contributor" `
-ResourceName pharma-sales-project-network `
-ResourceType Microsoft.Network/virtualNetworks `
-ResourceGroupName MyVirtualNetworkResourceGroup

RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/MyVirtualNetworkResourceGroup
                     /providers/Microsoft.Network/virtualNetworks/pharma-sales-project-network/providers/Microsoft.Authorizat
                     ion/roleAssignments/bbbbbbbb-bbbb-bbbb-bbbb-bbbbbbbbbbbb
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/MyVirtualNetworkResourceGroup
                     /providers/Microsoft.Network/virtualNetworks/pharma-sales-project-network
DisplayName        : Pharma Sales Admins
SignInName         :
RoleDefinitionName : Virtual Machine Contributor
RoleDefinitionId   : 9980e02c-c2be-4d73-94e8-173b1dc7cf3c
ObjectId           : aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa
ObjectType         : Group
CanDelegate        : False
```

#### <a name="add-a-role-assignment-for-a-user-at-a-resource-group-scope"></a>Lägg till en roll tilldelning för en användare i ett resurs grupps omfång

Tilldelar rollen [virtuell dator deltagare](built-in-roles.md#virtual-machine-contributor) till *patlong \@ contoso.com* -användare i resurs grupps omfånget *Pharma-Sales* .

```azurepowershell
PS C:\> New-AzRoleAssignment -SignInName patlong@contoso.com `
-RoleDefinitionName "Virtual Machine Contributor" `
-ResourceGroupName pharma-sales

RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales/pr
                     oviders/Microsoft.Authorization/roleAssignments/55555555-5555-5555-5555-555555555555
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales
DisplayName        : Pat Long
SignInName         : patlong@contoso.com
RoleDefinitionName : Virtual Machine Contributor
RoleDefinitionId   : 9980e02c-c2be-4d73-94e8-173b1dc7cf3c
ObjectId           : 44444444-4444-4444-4444-444444444444
ObjectType         : User
CanDelegate        : False
```

Alternativt kan du ange den fullständigt kvalificerade resurs gruppen med `-Scope` parametern:

```azurepowershell
PS C:\> New-AzRoleAssignment -SignInName patlong@contoso.com `
-RoleDefinitionName "Virtual Machine Contributor" `
-Scope "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales"

RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales/providers/Microsoft.Authorization/roleAssignments/55555555-5555-5555-5555-555555555555
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales
DisplayName        : Pat Long
SignInName         : patlong@contoso.com
RoleDefinitionName : Virtual Machine Contributor
RoleDefinitionId   : 9980e02c-c2be-4d73-94e8-173b1dc7cf3c
ObjectId           : 44444444-4444-4444-4444-444444444444
ObjectType         : User
CanDelegate        : False
```

#### <a name="add-role-assignment-for-a-user-using-the-unique-role-id-at-a-resource-group-scope"></a>Lägg till roll tilldelning för en användare med hjälp av det unika roll-ID: t i en resurs grupp omfånget

Det finns ett par gånger när ett roll namn kan ändras, till exempel:

- Du använder din egen anpassade roll och du bestämmer dig för att ändra namnet.
- Du använder en förhands gransknings roll som har **(förhands granskning)** i namnet. När rollen släpps får rollen ett nytt namn.

Även om en roll får ett nytt namn ändras inte roll-ID: t. Om du använder skript eller automatisering för att skapa roll tilldelningar, är det en bra idé att använda det unika roll-ID: t i stället för roll namnet. Därför är skripten mer sannolika om du byter namn på en roll.

I följande exempel tilldelas rollen [virtuell dator deltagare](built-in-roles.md#virtual-machine-contributor) till *patlong \@ contoso.com* -användaren i resurs grupps omfånget *Pharma-Sales* .

```azurepowershell
PS C:\> New-AzRoleAssignment -ObjectId 44444444-4444-4444-4444-444444444444 `
-RoleDefinitionId 9980e02c-c2be-4d73-94e8-173b1dc7cf3c `
-Scope "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales"

RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales/providers/Microsoft.Authorization/roleAssignments/55555555-5555-5555-5555-555555555555
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales
DisplayName        : Pat Long
SignInName         : patlong@contoso.com
RoleDefinitionName : Virtual Machine Contributor
RoleDefinitionId   : 9980e02c-c2be-4d73-94e8-173b1dc7cf3c
ObjectId           : 44444444-4444-4444-4444-444444444444
ObjectType         : User
CanDelegate        : False
```

#### <a name="add-role-assignment-for-an-application-at-a-resource-group-scope"></a>Lägg till roll tilldelning för ett program i en resurs grupp omfånget

Tilldelar rollen [virtuell dator deltagare](built-in-roles.md#virtual-machine-contributor) till ett program med tjänstens huvud OBJEKTS-ID 77777777-7777-7777-7777-777777777777 i resurs grupps omfånget *Pharma-Sales* .

```azurepowershell
PS C:\> New-AzRoleAssignment -ObjectId 77777777-7777-7777-7777-777777777777 `
-RoleDefinitionName "Virtual Machine Contributor" `
-ResourceGroupName pharma-sales

RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Authorization/roleAssignments/66666666-6666-6666-6666-666666666666
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales
DisplayName        : MyApp1
SignInName         :
RoleDefinitionName : Virtual Machine Contributor
RoleDefinitionId   : 9980e02c-c2be-4d73-94e8-173b1dc7cf3c
ObjectId           : 77777777-7777-7777-7777-777777777777
ObjectType         : ServicePrincipal
CanDelegate        : False
```

#### <a name="add-role-assignment-for-a-user-at-a-subscription-scope"></a>Lägg till roll tilldelning för en användare med ett prenumerations omfång

Tilldelar [läsaren](built-in-roles.md#reader) rollen till *annm \@ example.com* -användaren i ett prenumerations omfång.

```azurepowershell
PS C:\> New-AzRoleAssignment -SignInName annm@example.com `
-RoleDefinitionName "Reader" `
-Scope "/subscriptions/00000000-0000-0000-0000-000000000000"

RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Authorization/roleAssignments/66666666-6666-6666-6666-666666666666
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000
DisplayName        : Ann M
SignInName         : annm@example.com
RoleDefinitionName : Reader
RoleDefinitionId   : acdd72a7-3385-48ef-bd42-f606fba81ae7
ObjectId           : 77777777-7777-7777-7777-777777777777
ObjectType         : ServicePrincipal
CanDelegate        : False
```

#### <a name="add-role-assignment-for-a-user-at-a-management-group-scope"></a>Lägg till roll tilldelning för en användare i en hanterings grupps omfattning

Tilldelar rollen för [fakturerings läsare](built-in-roles.md#billing-reader) till *Alain \@ example.com* -användaren i ett hanterings grupps omfång.

```azurepowershell
PS C:\> New-AzRoleAssignment -SignInName alain@example.com `
-RoleDefinitionName "Billing Reader" `
-Scope "/providers/Microsoft.Management/managementGroups/marketing-group"

RoleAssignmentId   : /providers/Microsoft.Management/managementGroups/marketing-group/providers/Microsoft.Authorization/roleAssignments/22222222-2222-2222-2222-222222222222
Scope              : /providers/Microsoft.Management/managementGroups/marketing-group
DisplayName        : Alain Charon
SignInName         : alain@example.com
RoleDefinitionName : Billing Reader
RoleDefinitionId   : fa23ad8b-c56e-40d8-ac0c-ce449e1d2c64
ObjectId           : 44444444-4444-4444-4444-444444444444
ObjectType         : User
CanDelegate        : False
```

## <a name="remove-a-role-assignment"></a>Ta bort en rolltilldelning

I Azure RBAC tar du bort en roll tilldelning genom att använda [Remove-AzRoleAssignment](/powershell/module/az.resources/remove-azroleassignment)för att ta bort åtkomst.

I följande exempel tas roll tilldelningen för [virtuell dator deltagare](built-in-roles.md#virtual-machine-contributor) bort från *patlong \@ contoso.com* -användaren på resurs gruppen *Pharma-Sales* :

```azurepowershell
PS C:\> Remove-AzRoleAssignment -SignInName patlong@contoso.com `
-RoleDefinitionName "Virtual Machine Contributor" `
-ResourceGroupName pharma-sales
```

Tar bort rollen [läsare](built-in-roles.md#reader) från *Ann Mack team* Group med ID 22222222-2222-2222-2222-222222222222 i ett prenumerations omfång.

```azurepowershell
PS C:\> Remove-AzRoleAssignment -ObjectId 22222222-2222-2222-2222-222222222222 `
-RoleDefinitionName "Reader" `
-Scope "/subscriptions/00000000-0000-0000-0000-000000000000"
```

Tar bort rollen för [fakturerings läsare](built-in-roles.md#billing-reader) från *Alain \@ example.com* -användaren i hanterings gruppens omfång.

```azurepowershell
PS C:\> Remove-AzRoleAssignment -SignInName alain@example.com `
-RoleDefinitionName "Billing Reader" `
-Scope "/providers/Microsoft.Management/managementGroups/marketing-group"
```

Om du får fel meddelandet "den tillhandahållna informationen inte mappas till en roll tilldelning", se till att du även anger `-Scope` `-ResourceGroupName` parametrarna eller. Mer information finns i [Felsöka Azure RBAC](troubleshooting.md#role-assignments-with-identity-not-found).

## <a name="next-steps"></a>Nästa steg

- [Visa en lista med Azure Role-tilldelningar med Azure PowerShell](role-assignments-list-powershell.md)
- [Självstudie: bevilja en grupp åtkomst till Azure-resurser med hjälp av Azure PowerShell](tutorial-role-assignments-group-powershell.md)
- [Hantera resurser med Azure PowerShell](../azure-resource-manager/management/manage-resources-powershell.md)
