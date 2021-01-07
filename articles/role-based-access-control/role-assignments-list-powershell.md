---
title: Visa en lista med Azures roll tilldelningar med Azure PowerShell – Azure RBAC
description: Lär dig hur du avgör vilka resurser användare, grupper, tjänstens huvud namn eller hanterade identiteter har åtkomst till med hjälp av Azure PowerShell och rollbaserad åtkomst kontroll i Azure (Azure RBAC).
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: 9e225dba-9044-4b13-b573-2f30d77925a9
ms.service: role-based-access-control
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/28/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 980e9f1ffd453a070dde72f8c7ca34c7ef08f948
ms.sourcegitcommit: f6f928180504444470af713c32e7df667c17ac20
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/07/2021
ms.locfileid: "97964362"
---
# <a name="list-azure-role-assignments-using-azure-powershell"></a>Visa en lista med Azure Role-tilldelningar med Azure PowerShell

[!INCLUDE [Azure RBAC definition list access](../../includes/role-based-access-control/definition-list.md)] Den här artikeln beskriver hur du visar roll tilldelningar med hjälp av Azure PowerShell.

[!INCLUDE [az-powershell-update](../../includes/updated-for-az.md)]

> [!NOTE]
> Om din organisation har funktioner som har hanterats av en tjänst leverantör som använder [Azure-delegerad resurs hantering](../lighthouse/concepts/azure-delegated-resource-management.md), visas inte roll tilldelningar som har auktoriserats av tjänste leverantören här.

## <a name="prerequisites"></a>Krav

- [PowerShell i Azure Cloud Shell](../cloud-shell/overview.md) eller [Azure PowerShell](/powershell/azure/install-az-ps)

## <a name="list-role-assignments-for-the-current-subscription"></a>Lista roll tilldelningar för den aktuella prenumerationen

Det enklaste sättet att hämta en lista över alla roll tilldelningar i den aktuella prenumerationen (inklusive ärvda roll tilldelningar från rot-och hanterings grupper) är att använda [Get-AzRoleAssignment](/powershell/module/az.resources/get-azroleassignment) utan parametrar.

```azurepowershell
Get-AzRoleAssignment
```

```Example
PS C:\> Get-AzRoleAssignment

RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Authorization/roleAssignments/11111111-1111-1111-1111-111111111111
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000
DisplayName        : Alain
SignInName         : alain@example.com
RoleDefinitionName : Storage Blob Data Reader
RoleDefinitionId   : 2a2b9908-6ea1-4ae2-8e65-a410df84e7d1
ObjectId           : 44444444-4444-4444-4444-444444444444
ObjectType         : User
CanDelegate        : False

RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales/providers/Microsoft.Authorization/roleAssignments/33333333-3333-3333-3333-333333333333
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales
DisplayName        : Marketing
SignInName         :
RoleDefinitionName : Contributor
RoleDefinitionId   : b24988ac-6180-42a0-ab88-20f7382dd24c
ObjectId           : 22222222-2222-2222-2222-222222222222
ObjectType         : Group
CanDelegate        : False

...
```

## <a name="list-role-assignments-for-a-subscription"></a>Lista med rolltilldelningar för en prenumeration

Om du vill visa en lista över alla roll tilldelningar i en prenumerations omfattning använder du [Get-AzRoleAssignment](/powershell/module/az.resources/get-azroleassignment). För att hämta prenumerations-ID kan du hitta det på bladet **prenumerationer** i Azure Portal eller så kan du använda [Get-AzSubscription](/powershell/module/Az.Accounts/Get-AzSubscription).

```azurepowershell
Get-AzRoleAssignment -Scope /subscriptions/<subscription_id>
```

```Example
PS C:\> Get-AzRoleAssignment -Scope /subscriptions/00000000-0000-0000-0000-000000000000
```

## <a name="list-role-assignments-for-a-user"></a>Visa rolltilldelningar för en användare

Om du vill visa en lista över alla roller som har tilldelats en angiven användare använder du [Get-AzRoleAssignment](/powershell/module/az.resources/get-azroleassignment).

```azurepowershell
Get-AzRoleAssignment -SignInName <email_or_userprincipalname>
```

```Example
PS C:\> Get-AzRoleAssignment -SignInName isabella@example.com | FL DisplayName, RoleDefinitionName, Scope

DisplayName        : Isabella Simonsen
RoleDefinitionName : BizTalk Contributor
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales
```

Om du vill visa en lista över alla roller som har tilldelats en angiven användare och de roller som är tilldelade de grupper som användaren tillhör använder du [Get-AzRoleAssignment](/powershell/module/az.resources/get-azroleassignment).

```azurepowershell
Get-AzRoleAssignment -SignInName <email_or_userprincipalname> -ExpandPrincipalGroups
```

```Example
Get-AzRoleAssignment -SignInName isabella@example.com -ExpandPrincipalGroups | FL DisplayName, RoleDefinitionName, Scope
```

## <a name="list-role-assignments-for-a-resource-group"></a>Visa rolltilldelningar för en resursgrupp

Om du vill visa en lista över alla roll tilldelningar i ett resurs grupps omfång använder du [Get-AzRoleAssignment](/powershell/module/az.resources/get-azroleassignment).

```azurepowershell
Get-AzRoleAssignment -ResourceGroupName <resource_group_name>
```

```Example
PS C:\> Get-AzRoleAssignment -ResourceGroupName pharma-sales | FL DisplayName, RoleDefinitionName, Scope

DisplayName        : Alain Charon
RoleDefinitionName : Backup Operator
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales

DisplayName        : Isabella Simonsen
RoleDefinitionName : BizTalk Contributor
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales

DisplayName        : Alain Charon
RoleDefinitionName : Virtual Machine Contributor
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales
```

## <a name="list-role-assignments-for-a-management-group"></a>Lista roll tilldelningar för en hanterings grupp

Om du vill visa en lista över alla roll tilldelningar i ett hanterings grupps omfång använder du [Get-AzRoleAssignment](/powershell/module/az.resources/get-azroleassignment). Om du vill hämta ID för hanterings grupp kan du hitta det på bladet **hanterings grupper** i Azure Portal eller så kan du använda [Get-AzManagementGroup](/powershell/module/az.resources/get-azmanagementgroup).

```azurepowershell
Get-AzRoleAssignment -Scope /providers/Microsoft.Management/managementGroups/<group_id>
```

```Example
PS C:\> Get-AzRoleAssignment -Scope /providers/Microsoft.Management/managementGroups/marketing-group
```

## <a name="list-role-assignments-for-a-resource"></a>Lista roll tilldelningar för en resurs

Om du vill visa roll tilldelningar för en speciell resurs använder du [Get-AzRoleAssignment](/powershell/module/az.resources/get-azroleassignment) och `-Scope` parametern. Omfattningen varierar beroende på resurs. Om du vill hämta omfånget kan du köra `Get-AzRoleAssignment` utan parametrar för att lista alla roll tilldelningar och sedan hitta den omfattning som du vill visa.

```azurepowershell
Get-AzRoleAssignment -Scope "/subscriptions/<subscription_id>/resourcegroups/<resource_group_name>/providers/<provider_name>/<resource_type>/<resource>
```

I följande exempel visas hur du listar roll tilldelningarna för ett lagrings konto. Observera att det här kommandot även visar roll tilldelningar med högre omfång, till exempel resurs grupper och prenumerationer som gäller för det här lagrings kontot.

```Example
PS C:\> Get-AzRoleAssignment -Scope "/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/storage-test-rg/providers/Microsoft.Storage/storageAccounts/storagetest0122"
```

Om du bara vill lista roll tilldelningar som tilldelas direkt på en resurs kan du använda kommandot [Where-Object](/powershell/module/microsoft.powershell.core/where-object) för att filtrera listan.

```Example
PS C:\> Get-AzRoleAssignment | Where-Object {$_.Scope -eq "/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/storage-test-rg/providers/Microsoft.Storage/storageAccounts/storagetest0122"}
```

## <a name="list-role-assignments-for-classic-service-administrator-and-co-administrators"></a>Lista roll tilldelningar för klassisk tjänst administratör och medadministratörer

Använd [Get-AzRoleAssignment](/powershell/module/az.resources/get-azroleassignment)för att visa en lista över roll tilldelningar för den klassiska prenumerations administratören och medadministratörerna.

```azurepowershell
Get-AzRoleAssignment -IncludeClassicAdministrators
```

## <a name="list-role-assignments-for-a-managed-identity"></a>Lista roll tilldelningar för en hanterad identitet

1. Hämta objekt-ID: t för den systemtilldelade eller användarspecifika hanterade identiteten. 

    Du kan använda [Get-AzADServicePrincipal](/powershell/module/az.resources/get-azadserviceprincipal)för att hämta objekt-ID: t för en användardefinierad hanterad identitet.

    ```azurepowershell
    Get-AzADServicePrincipal -DisplayNameBeginsWith "<name> or <vmname>"
    ```

1. Om du vill visa roll tilldelningarna använder du [Get-AzRoleAssignment](/powershell/module/az.resources/get-azroleassignment).

    ```azurepowershell
    Get-AzRoleAssignment -ObjectId <objectid>
    ```

## <a name="next-steps"></a>Nästa steg

- [Lägga till eller ta bort Azure-rolltilldelningar med hjälp av Azure PowerShell](role-assignments-powershell.md)