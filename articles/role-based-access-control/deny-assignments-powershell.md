---
title: Visa en lista över Azure neka-tilldelningar med Azure PowerShell-Azure RBAC
description: Lär dig hur du visar en lista över användare, grupper, tjänstens huvud namn och hanterade identiteter som har nekats åtkomst till specifika Azure-resurs åtgärder i vissa områden med hjälp av Azure PowerShell och rollbaserad åtkomst kontroll i Azure (Azure RBAC).
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/12/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 6cf379b051ba42be2d7df7d288f07cdc2a0002a1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "94657643"
---
# <a name="list-azure-deny-assignments-using-azure-powershell"></a>Visa en lista över Azure neka-tilldelningar med Azure PowerShell

[Azure Deny-tilldelningar](deny-assignments.md) blockerar användare från att utföra vissa åtgärder för Azure-resurser även om en roll tilldelning ger åtkomst till dem. I den här artikeln beskrivs hur du visar en lista över nekade tilldelningar med Azure PowerShell.

> [!NOTE]
> Du kan inte skapa egna neka-tilldelningar direkt. För information om hur neka-tilldelningar skapas, se [Azure Deny-tilldelningar](deny-assignments.md).

## <a name="prerequisites"></a>Förutsättningar

För att få information om en neka-tilldelning måste du ha:

- `Microsoft.Authorization/denyAssignments/read` behörighet, som ingår i de flesta [inbyggda Azure-roller](built-in-roles.md)
- [PowerShell i Azure Cloud Shell](../cloud-shell/overview.md) eller [Azure PowerShell](/powershell/azure/install-az-ps)

## <a name="list-deny-assignments"></a>Visa lista över nekade tilldelningar

### <a name="list-all-deny-assignments"></a>Lista alla neka-tilldelningar

Om du vill visa alla neka-tilldelningar för den aktuella prenumerationen använder du [Get-AzDenyAssignment](/powershell/module/az.resources/get-azdenyassignment).

```azurepowershell
Get-AzDenyAssignment
```

```Example
PS C:\> Get-AzDenyAssignment

Id                      : 22222222-2222-2222-2222-222222222222
DenyAssignmentName      : Deny assignment '22222222-2222-2222-2222-222222222222' created by Blueprint Assignment
                          '/subscriptions/11111111-1111-1111-1111-111111111111/providers/Microsoft.Blueprint/blueprintAssignments/assignment-locked-storageaccount-TestingBPLocks'.
Description             : Created by Blueprint Assignment '/subscriptions/11111111-1111-1111-1111-111111111111/providers/Microsoft.Blueprint/blueprintAssignments/assignment-locked-storageaccount-TestingBPLocks'.
Actions                 : {*}
NotActions              : {*/read}
DataActions             : {}
NotDataActions          : {}
Scope                   : /subscriptions/11111111-1111-1111-1111-111111111111/resourceGroups/TestingBPLocks
DoNotApplyToChildScopes : True
Principals              : {
                          DisplayName:  All Principals
                          ObjectType:   SystemDefined
                          ObjectId:     00000000-0000-0000-0000-000000000000
                          }
ExcludePrincipals       : {
                          ObjectType:   ServicePrincipal
                          }
IsSystemProtected       : True

Id                      : 33333333-3333-3333-3333-333333333333
DenyAssignmentName      : Deny assignment '33333333-3333-3333-3333-333333333333' created by Blueprint Assignment
                          '/subscriptions/11111111-1111-1111-1111-111111111111/providers/Microsoft.Blueprint/blueprintAssignments/assignment-locked-storageaccount-TestingBPLocks'.
Description             : Created by Blueprint Assignment '/subscriptions/11111111-1111-1111-1111-111111111111/providers/Microsoft.Blueprint/blueprintAssignments/assignment-locked-storageaccount-TestingBPLocks'.
Actions                 : {*}
NotActions              : {*/read}
DataActions             : {}
NotDataActions          : {}
Scope                   : /subscriptions/11111111-1111-1111-1111-111111111111/resourceGroups/TestingBPLocks/providers/Microsoft.Storage/storageAccounts/storep6vkuxmu4m4pq
DoNotApplyToChildScopes : True
Principals              : {
                          DisplayName:  All Principals
                          ObjectType:   SystemDefined
                          ObjectId:     00000000-0000-0000-0000-000000000000
                          }
ExcludePrincipals       : {
                          DisplayName:  assignment-locked-storageaccount-TestingBPLocks
                          ObjectType:   ServicePrincipal
                          ObjectId:     2311a0b7-657a-4ca2-af6f-d1c33f6d2fff
                          }
IsSystemProtected       : True
```

### <a name="list-deny-assignments-at-a-resource-group-scope"></a>Visa lista över neka tilldelningar i en resurs grupp omfånget

Använd [Get-AzDenyAssignment](/powershell/module/az.resources/get-azdenyassignment)om du vill visa alla neka-tilldelningar vid en resurs grupps definitions område.

```azurepowershell
Get-AzDenyAssignment -ResourceGroupName <resource_group_name>
```

```Example
PS C:\> Get-AzDenyAssignment -ResourceGroupName TestingBPLocks | FL DenyAssignmentName, Scope

DenyAssignmentName : Deny assignment '22222222-2222-2222-2222-222222222222' created by Blueprint Assignment
                     '/subscriptions/11111111-1111-1111-1111-111111111111/providers/Microsoft.Blueprint/blueprintAssignments/assignment-locked-storageaccount-TestingBPLocks'.
Scope              : /subscriptions/11111111-1111-1111-1111-111111111111/resourceGroups/TestingBPLocks
Principals         : {
                     DisplayName:       All Principals
                     ObjectType:        SystemDefined
                     ObjectId:  00000000-0000-0000-0000-000000000000
                     }
```

### <a name="list-deny-assignments-at-a-subscription-scope"></a>Visa lista över neka tilldelningar i ett prenumerations omfång

Om du vill visa alla neka-tilldelningar i en prenumerations omfattning använder du [Get-AzDenyAssignment](/powershell/module/az.resources/get-azdenyassignment). För att hämta prenumerations-ID kan du hitta det på bladet **prenumerationer** i Azure Portal eller så kan du använda [Get-AzSubscription](/powershell/module/Az.Accounts/Get-AzSubscription).

```azurepowershell
Get-AzDenyAssignment -Scope /subscriptions/<subscription_id>
```

```Example
PS C:\> Get-AzDenyAssignment -Scope /subscriptions/11111111-1111-1111-1111-111111111111
```

## <a name="next-steps"></a>Nästa steg

- [Förstå Azure Deny-tilldelningar](deny-assignments.md)
- [Visa en lista över Azure Deny-tilldelningar med hjälp av Azure Portal](deny-assignments-portal.md)
- [Visa en lista över Azure Deny-tilldelningar med hjälp av REST API](deny-assignments-rest.md)