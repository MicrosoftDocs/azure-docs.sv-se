---
title: Skapa eller uppdatera Azure-anpassade roller med Azure PowerShell-Azure RBAC
description: Lär dig att visa, skapa, uppdatera eller ta bort anpassade roller med hjälp av Azure PowerShell och rollbaserad åtkomst kontroll i Azure (Azure RBAC).
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
ms.date: 03/18/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 799475db567c88f067192d027589e9185ee1782b
ms.sourcegitcommit: 1bdcaca5978c3a4929cccbc8dc42fc0c93ca7b30
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/13/2020
ms.locfileid: "97369182"
---
# <a name="create-or-update-azure-custom-roles-using-azure-powershell"></a>Skapa eller uppdatera Azure-anpassade roller med hjälp av Azure PowerShell

> [!IMPORTANT]
> Att lägga till en hanterings grupp i `AssignableScopes` är för närvarande en för hands version.
> Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade.
> Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Om de [inbyggda Azure-rollerna](built-in-roles.md) inte uppfyller organisationens specifika behov kan du skapa dina egna anpassade roller. Den här artikeln beskriver hur du visar, skapar, uppdaterar eller tar bort anpassade roller med hjälp av Azure PowerShell.

En stegvis själv studie kurs om hur du skapar en anpassad roll finns i [Självstudier: skapa en anpassad Azure-roll med hjälp av Azure PowerShell](tutorial-custom-role-powershell.md).

[!INCLUDE [az-powershell-update](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Förutsättningar

Om du vill skapa anpassade roller behöver du:

- Behörigheter att skapa anpassade roller som [Owner](built-in-roles.md#owner) (Ägare) eller [User Access Administrator](built-in-roles.md#user-access-administrator) (Administratör för användaråtkomst)
- [Azure Cloud Shell](../cloud-shell/overview.md) eller [Azure PowerShell](/powershell/azure/install-az-ps)

## <a name="list-custom-roles"></a>Lista anpassade roller

Om du vill visa en lista över roller som är tillgängliga för tilldelning i ett omfång använder du kommandot [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition) . I följande exempel visas alla roller som är tillgängliga för tilldelning i den valda prenumerationen.

```azurepowershell
Get-AzRoleDefinition | FT Name, IsCustom
```

```Example
Name                                              IsCustom
----                                              --------
Virtual Machine Operator                              True
AcrImageSigner                                       False
AcrQuarantineReader                                  False
AcrQuarantineWriter                                  False
API Management Service Contributor                   False
...
```

I följande exempel visas bara de anpassade roller som är tillgängliga för tilldelning i den valda prenumerationen.

```azurepowershell
Get-AzRoleDefinition -Custom | FT Name, IsCustom
```

```Example
Name                     IsCustom
----                     --------
Virtual Machine Operator     True
```

Om den valda prenumerationen inte finns i `AssignableScopes` rollen visas inte den anpassade rollen.

## <a name="list-a-custom-role-definition"></a>Lista en anpassad roll definition

Om du vill visa en anpassad roll definition använder du [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition). Det här är samma kommando som du använder för en inbyggd roll.

```azurepowershell
Get-AzRoleDefinition <role_name> | ConvertTo-Json
```

```Example
PS C:\> Get-AzRoleDefinition "Virtual Machine Operator" | ConvertTo-Json

{
  "Name": "Virtual Machine Operator",
  "Id": "00000000-0000-0000-0000-000000000000",
  "IsCustom": true,
  "Description": "Can monitor and restart virtual machines.",
  "Actions": [
    "Microsoft.Storage/*/read",
    "Microsoft.Network/*/read",
    "Microsoft.Compute/*/read",
    "Microsoft.Compute/virtualMachines/start/action",
    "Microsoft.Compute/virtualMachines/restart/action",
    "Microsoft.Authorization/*/read",
    "Microsoft.ResourceHealth/availabilityStatuses/read",
    "Microsoft.Resources/subscriptions/resourceGroups/read",
    "Microsoft.Insights/alertRules/*",
    "Microsoft.Support/*"
  ],
  "NotActions": [],
  "DataActions": [],
  "NotDataActions": [],
  "AssignableScopes": [
    "/subscriptions/11111111-1111-1111-1111-111111111111"
  ]
}
```

I följande exempel visas bara åtgärder för rollen:

```azurepowershell
(Get-AzRoleDefinition <role_name>).Actions
```

```Example
PS C:\> (Get-AzRoleDefinition "Virtual Machine Operator").Actions

"Microsoft.Storage/*/read",
"Microsoft.Network/*/read",
"Microsoft.Compute/*/read",
"Microsoft.Compute/virtualMachines/start/action",
"Microsoft.Compute/virtualMachines/restart/action",
"Microsoft.Authorization/*/read",
"Microsoft.ResourceHealth/availabilityStatuses/read",
"Microsoft.Resources/subscriptions/resourceGroups/read",
"Microsoft.Insights/alertRules/*",
"Microsoft.Insights/diagnosticSettings/*",
"Microsoft.Support/*"
```

## <a name="create-a-custom-role"></a>Skapa en anpassad roll

Använd kommandot [New-AzRoleDefinition](/powershell/module/az.resources/new-azroledefinition) om du vill skapa en anpassad roll. Det finns två sätt att strukturera rollen med hjälp av `PSRoleDefinition` objekt eller en JSON-mall. 

### <a name="get-operations-for-a-resource-provider"></a>Hämta åtgärder för en resurs leverantör

När du skapar anpassade roller är det viktigt att känna till alla möjliga åtgärder från resurs leverantörerna.
Du kan visa listan över [resurs leverantörs åtgärder](resource-provider-operations.md) eller så kan du använda kommandot [Get-AzProviderOperation](/powershell/module/az.resources/get-azprovideroperation) för att hämta den här informationen.
Om du till exempel vill kontrol lera alla tillgängliga åtgärder för virtuella datorer använder du följande kommando:

```azurepowershell
Get-AzProviderOperation <operation> | FT OperationName, Operation, Description -AutoSize
```

```Example
PS C:\> Get-AzProviderOperation "Microsoft.Compute/virtualMachines/*" | FT OperationName, Operation, Description -AutoSize

OperationName                                  Operation                                                      Description
-------------                                  ---------                                                      -----------
Get Virtual Machine                            Microsoft.Compute/virtualMachines/read                         Get the propertie...
Create or Update Virtual Machine               Microsoft.Compute/virtualMachines/write                        Creates a new vir...
Delete Virtual Machine                         Microsoft.Compute/virtualMachines/delete                       Deletes the virtu...
Start Virtual Machine                          Microsoft.Compute/virtualMachines/start/action                 Starts the virtua...
...
```

### <a name="create-a-custom-role-with-the-psroledefinition-object"></a>Skapa en anpassad roll med PSRoleDefinition-objektet

När du använder PowerShell för att skapa en anpassad roll kan du använda en av de [inbyggda rollerna](built-in-roles.md) som start punkt eller så kan du börja från början. Det första exemplet i det här avsnittet börjar med en inbyggd roll och anpassar sedan den med fler behörigheter. Redigera attributen för att lägga till den `Actions` , `NotActions` eller så `AssignableScopes` du vill, och spara sedan ändringarna som en ny roll.

Följande exempel börjar med den inbyggda rollen [virtuell dator deltagare](built-in-roles.md#virtual-machine-contributor) för att skapa en anpassad roll med namnet *Virtual Machine-operator*. Den nya rollen beviljar åtkomst till alla Läs åtgärder i *Microsoft. Compute*, *Microsoft. Storage* och *Microsoft. Network* Resource providers och ger åtkomst till Start, omstart och övervakning av virtuella datorer. Den anpassade rollen kan användas i två prenumerationer.

```azurepowershell
$role = Get-AzRoleDefinition "Virtual Machine Contributor"
$role.Id = $null
$role.Name = "Virtual Machine Operator"
$role.Description = "Can monitor and restart virtual machines."
$role.Actions.Clear()
$role.Actions.Add("Microsoft.Storage/*/read")
$role.Actions.Add("Microsoft.Network/*/read")
$role.Actions.Add("Microsoft.Compute/*/read")
$role.Actions.Add("Microsoft.Compute/virtualMachines/start/action")
$role.Actions.Add("Microsoft.Compute/virtualMachines/restart/action")
$role.Actions.Add("Microsoft.Authorization/*/read")
$role.Actions.Add("Microsoft.ResourceHealth/availabilityStatuses/read")
$role.Actions.Add("Microsoft.Resources/subscriptions/resourceGroups/read")
$role.Actions.Add("Microsoft.Insights/alertRules/*")
$role.Actions.Add("Microsoft.Support/*")
$role.AssignableScopes.Clear()
$role.AssignableScopes.Add("/subscriptions/00000000-0000-0000-0000-000000000000")
$role.AssignableScopes.Add("/subscriptions/11111111-1111-1111-1111-111111111111")
New-AzRoleDefinition -Role $role
```

I följande exempel visas ett annat sätt att skapa den anpassade rollen för den *virtuella datorn* . Den börjar med att skapa ett nytt `PSRoleDefinition` objekt. Åtgärds åtgärderna anges i `perms` variabeln och anges till `Actions` egenskapen. `NotActions`Egenskapen anges genom `NotActions` att läsa från den inbyggda rollen [virtuell dator deltagare](built-in-roles.md#virtual-machine-contributor) . Eftersom den [virtuella dator deltagaren](built-in-roles.md#virtual-machine-contributor) inte har några `NotActions` , krävs inte den här raden, men den visar hur information kan hämtas från en annan roll.

```azurepowershell
$role = [Microsoft.Azure.Commands.Resources.Models.Authorization.PSRoleDefinition]::new()
$role.Name = 'Virtual Machine Operator 2'
$role.Description = 'Can monitor and restart virtual machines.'
$role.IsCustom = $true
$perms = 'Microsoft.Storage/*/read','Microsoft.Network/*/read','Microsoft.Compute/*/read'
$perms += 'Microsoft.Compute/virtualMachines/start/action','Microsoft.Compute/virtualMachines/restart/action'
$perms += 'Microsoft.Authorization/*/read'
$perms += 'Microsoft.ResourceHealth/availabilityStatuses/read'
$perms += 'Microsoft.Resources/subscriptions/resourceGroups/read'
$perms += 'Microsoft.Insights/alertRules/*','Microsoft.Support/*'
$role.Actions = $perms
$role.NotActions = (Get-AzRoleDefinition -Name 'Virtual Machine Contributor').NotActions
$subs = '/subscriptions/00000000-0000-0000-0000-000000000000','/subscriptions/11111111-1111-1111-1111-111111111111'
$role.AssignableScopes = $subs
New-AzRoleDefinition -Role $role
```

### <a name="create-a-custom-role-with-json-template"></a>Skapa en anpassad roll med JSON-mall

En JSON-mall kan användas som käll definition för den anpassade rollen. I följande exempel skapas en anpassad roll som ger Läs åtkomst till lagrings-och beräknings resurser, åtkomst till support och tillägg av rollen till två prenumerationer. Skapa en ny fil `C:\CustomRoles\customrole1.json` med följande exempel. ID: t måste anges till `null` vid första roll skapande eftersom ett nytt ID genereras automatiskt. 

```json
{
  "Name": "Custom Role 1",
  "Id": null,
  "IsCustom": true,
  "Description": "Allows for read access to Azure storage and compute resources and access to support",
  "Actions": [
    "Microsoft.Compute/*/read",
    "Microsoft.Storage/*/read",
    "Microsoft.Support/*"
  ],
  "NotActions": [],
  "AssignableScopes": [
    "/subscriptions/00000000-0000-0000-0000-000000000000",
    "/subscriptions/11111111-1111-1111-1111-111111111111"
  ]
}
```

Om du vill lägga till rollen i prenumerationerna kör du följande PowerShell-kommando:

```azurepowershell
New-AzRoleDefinition -InputFile "C:\CustomRoles\customrole1.json"
```

## <a name="update-a-custom-role"></a>Uppdatera en anpassad roll

På samma sätt som du skapar en anpassad roll kan du ändra en befintlig anpassad roll med hjälp av antingen `PSRoleDefinition` objektet eller en JSON-mall.

### <a name="update-a-custom-role-with-the-psroledefinition-object"></a>Uppdatera en anpassad roll med PSRoleDefinition-objektet

Om du vill ändra en anpassad roll använder du först kommandot [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition) för att hämta roll definitionen. Sedan gör du önskade ändringar i roll definitionen. Använd slutligen kommandot [set-AzRoleDefinition](/powershell/module/az.resources/set-azroledefinition) för att spara den ändrade roll definitionen.

I följande exempel läggs `Microsoft.Insights/diagnosticSettings/*` åtgärden till i den *virtuella datorns operatörs* anpassade roll.

```azurepowershell
$role = Get-AzRoleDefinition "Virtual Machine Operator"
$role.Actions.Add("Microsoft.Insights/diagnosticSettings/*")
Set-AzRoleDefinition -Role $role
```

```Example
PS C:\> $role = Get-AzRoleDefinition "Virtual Machine Operator"
PS C:\> $role.Actions.Add("Microsoft.Insights/diagnosticSettings/*")
PS C:\> Set-AzRoleDefinition -Role $role

Name             : Virtual Machine Operator
Id               : 88888888-8888-8888-8888-888888888888
IsCustom         : True
Description      : Can monitor and restart virtual machines.
Actions          : {Microsoft.Storage/*/read, Microsoft.Network/*/read, Microsoft.Compute/*/read,
                   Microsoft.Compute/virtualMachines/start/action...}
NotActions       : {}
AssignableScopes : {/subscriptions/00000000-0000-0000-0000-000000000000,
                   /subscriptions/11111111-1111-1111-1111-111111111111}
```

I följande exempel läggs en Azure-prenumeration till i de tilldelnings bara omfattningarna för den *virtuella datorns operatörs* anpassade roll.

```azurepowershell
Get-AzSubscription -SubscriptionName Production3

$role = Get-AzRoleDefinition "Virtual Machine Operator"
$role.AssignableScopes.Add("/subscriptions/22222222-2222-2222-2222-222222222222")
Set-AzRoleDefinition -Role $role
```

```Example
PS C:\> Get-AzSubscription -SubscriptionName Production3

Name     : Production3
Id       : 22222222-2222-2222-2222-222222222222
TenantId : 99999999-9999-9999-9999-999999999999
State    : Enabled

PS C:\> $role = Get-AzRoleDefinition "Virtual Machine Operator"
PS C:\> $role.AssignableScopes.Add("/subscriptions/22222222-2222-2222-2222-222222222222")
PS C:\> Set-AzRoleDefinition -Role $role

Name             : Virtual Machine Operator
Id               : 88888888-8888-8888-8888-888888888888
IsCustom         : True
Description      : Can monitor and restart virtual machines.
Actions          : {Microsoft.Storage/*/read, Microsoft.Network/*/read, Microsoft.Compute/*/read,
                   Microsoft.Compute/virtualMachines/start/action...}
NotActions       : {}
AssignableScopes : {/subscriptions/00000000-0000-0000-0000-000000000000,
                   /subscriptions/11111111-1111-1111-1111-111111111111,
                   /subscriptions/22222222-2222-2222-2222-222222222222}
```

I följande exempel lägger du till en hanterings grupp till `AssignableScopes` i den anpassade rollen för den *virtuella datorn* . Att lägga till en hanterings grupp i `AssignableScopes` är för närvarande en för hands version.

```azurepowershell
Get-AzManagementGroup

$role = Get-AzRoleDefinition "Virtual Machine Operator"
$role.AssignableScopes.Add("/providers/Microsoft.Management/managementGroups/{groupId1}")
Set-AzRoleDefinition -Role $role
```

```Example
PS C:\> Get-AzManagementGroup

Id          : /providers/Microsoft.Management/managementGroups/marketing-group
Type        : /providers/Microsoft.Management/managementGroups
Name        : marketing-group
TenantId    : 99999999-9999-9999-9999-999999999999
DisplayName : Marketing group

PS C:\> $role = Get-AzRoleDefinition "Virtual Machine Operator"
PS C:\> $role.AssignableScopes.Add("/providers/Microsoft.Management/managementGroups/marketing-group")
PS C:\> Set-AzRoleDefinition -Role $role

Name             : Virtual Machine Operator
Id               : 88888888-8888-8888-8888-888888888888
IsCustom         : True
Description      : Can monitor and restart virtual machines.
Actions          : {Microsoft.Storage/*/read, Microsoft.Network/*/read, Microsoft.Compute/*/read,
                   Microsoft.Compute/virtualMachines/start/action...}
NotActions       : {}
AssignableScopes : {/subscriptions/00000000-0000-0000-0000-000000000000,
                   /subscriptions/11111111-1111-1111-1111-111111111111,
                   /subscriptions/22222222-2222-2222-2222-222222222222,
                   /providers/Microsoft.Management/managementGroups/marketing-group}
```

### <a name="update-a-custom-role-with-a-json-template"></a>Uppdatera en anpassad roll med en JSON-mall

Med hjälp av föregående JSON-mall kan du enkelt ändra en befintlig anpassad roll för att lägga till eller ta bort åtgärder. Uppdatera JSON-mallen och Lägg till Läs åtgärden för nätverk som visas i följande exempel. Definitionerna som anges i mallen tillämpas inte sammantaget i en befintlig definition, vilket innebär att rollen visas exakt som du anger i mallen. Du måste också uppdatera ID-fältet med rollens ID. Om du inte är säker på vad det här värdet är kan du använda cmdleten [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition) för att hämta den här informationen.

```json
{
  "Name": "Custom Role 1",
  "Id": "acce7ded-2559-449d-bcd5-e9604e50bad1",
  "IsCustom": true,
  "Description": "Allows for read access to Azure storage and compute resources and access to support",
  "Actions": [
    "Microsoft.Compute/*/read",
    "Microsoft.Storage/*/read",
    "Microsoft.Network/*/read",
    "Microsoft.Support/*"
  ],
  "NotActions": [],
  "AssignableScopes": [
    "/subscriptions/00000000-0000-0000-0000-000000000000",
    "/subscriptions/11111111-1111-1111-1111-111111111111"
  ]
}
```

Kör följande PowerShell-kommando för att uppdatera den befintliga rollen:

```azurepowershell
Set-AzRoleDefinition -InputFile "C:\CustomRoles\customrole1.json"
```

## <a name="delete-a-custom-role"></a>Ta bort en anpassad roll

Om du vill ta bort en anpassad roll använder du kommandot [Remove-AzRoleDefinition](/powershell/module/az.resources/remove-azroledefinition) .

I följande exempel tar vi bort den anpassade rollen för den *virtuella datorn* .

```azurepowershell
Get-AzRoleDefinition "Virtual Machine Operator"
Get-AzRoleDefinition "Virtual Machine Operator" | Remove-AzRoleDefinition
```

```Example
PS C:\> Get-AzRoleDefinition "Virtual Machine Operator"

Name             : Virtual Machine Operator
Id               : 88888888-8888-8888-8888-888888888888
IsCustom         : True
Description      : Can monitor and restart virtual machines.
Actions          : {Microsoft.Storage/*/read, Microsoft.Network/*/read, Microsoft.Compute/*/read,
                   Microsoft.Compute/virtualMachines/start/action...}
NotActions       : {}
AssignableScopes : {/subscriptions/00000000-0000-0000-0000-000000000000,
                   /subscriptions/11111111-1111-1111-1111-111111111111}

PS C:\> Get-AzRoleDefinition "Virtual Machine Operator" | Remove-AzRoleDefinition

Confirm
Are you sure you want to remove role definition with name 'Virtual Machine Operator'.
[Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): Y
```

## <a name="next-steps"></a>Nästa steg

- [Självstudie: skapa en anpassad Azure-roll med hjälp av Azure PowerShell](tutorial-custom-role-powershell.md)
- [Anpassade roller i Azure](custom-roles.md)
- [Azure Resource Provider-åtgärder](resource-provider-operations.md)
