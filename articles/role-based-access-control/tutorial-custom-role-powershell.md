---
title: 'Självstudie: skapa en anpassad Azure-roll med Azure PowerShell – Azure RBAC'
description: Kom igång med att skapa en anpassad Azure-roll med hjälp av Azure PowerShell och rollbaserad åtkomst kontroll i Azure (Azure RBAC) i den här självstudien.
services: active-directory
documentationCenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: role-based-access-control
ms.devlang: ''
ms.topic: tutorial
ms.tgt_pltfrm: ''
ms.workload: identity
ms.date: 02/20/2019
ms.author: rolyon
ms.openlocfilehash: 8b1815e7598410cd709572d93082d5dee5e0b0fb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "97369250"
---
# <a name="tutorial-create-an-azure-custom-role-using-azure-powershell"></a>Självstudie: skapa en anpassad Azure-roll med hjälp av Azure PowerShell

Om de [inbyggda Azure-rollerna](built-in-roles.md) inte uppfyller organisationens specifika behov kan du skapa dina egna anpassade roller. För den här självstudien skapar du en anpassad roll med namnet Reader Support Tickets (Läsare av supportbegäranden) med hjälp av Azure PowerShell. Med den här anpassade rollen kan användare visa allt i en prenumerations hanteringsplan samt öppna supportbegäranden.

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Skapa en anpassad roll
> * Lista anpassade roller
> * Uppdatera en anpassad roll
> * Ta bort en anpassad roll

Om du inte har någon Azure-prenumeration kan du [skapa ett kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

[!INCLUDE [az-powershell-update](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Förutsättningar

För att kunna genomföra den här kursen behöver du följande:

- Behörigheter att skapa anpassade roller som [Owner](built-in-roles.md#owner) (Ägare) eller [User Access Administrator](built-in-roles.md#user-access-administrator) (Administratör för användaråtkomst)
- [Azure Cloud Shell](../cloud-shell/overview.md) eller [Azure PowerShell](/powershell/azure/install-az-ps)

## <a name="sign-in-to-azure-powershell"></a>Logga in till Azure PowerShell

Logga in på [Azure PowerShell](/powershell/azure/authenticate-azureps).

## <a name="create-a-custom-role"></a>Skapa en anpassad roll

Det enklaste sättet att skapa en anpassad roll är att utgå från en inbyggd roll, redigera den och sedan skapa en ny roll.

1. I PowerShell använder du kommandot [Get-AzProviderOperation](/powershell/module/az.resources/get-azprovideroperation) för att hämta listan över åtgärder för resursprovidern Microsoft.Support. Det är bra att känna till de åtgärder som är tillgängliga för att skapa dina behörigheter. Du kan också se en lista över alla åtgärder vid [Azure Resource Provider-åtgärder](resource-provider-operations.md#microsoftsupport).

    ```azurepowershell
    Get-AzProviderOperation "Microsoft.Support/*" | FT Operation, Description -AutoSize
    ```
    
    ```Output
    Operation                              Description
    ---------                              -----------
    Microsoft.Support/register/action      Registers to Support Resource Provider
    Microsoft.Support/supportTickets/read  Gets Support Ticket details (including status, severity, contact ...
    Microsoft.Support/supportTickets/write Creates or Updates a Support Ticket. You can create a Support Tic...
    ```

1. Använd kommandot [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition) för att mata ut rollen [Reader](built-in-roles.md#reader) (Läsare) i JSON-format.

    ```azurepowershell
    Get-AzRoleDefinition -Name "Reader" | ConvertTo-Json | Out-File C:\CustomRoles\ReaderSupportRole.json
    ```

1. Öppna filen **ReaderSupportRole.json** i en textredigerare.

    Följande visar JSON-utdata. Information om de olika egenskaperna finns i [Azure-anpassade roller](custom-roles.md).

    ```json
    {
      "Name": "Reader",
      "Id": "acdd72a7-3385-48ef-bd42-f606fba81ae7",
      "IsCustom": false,
      "Description": "Lets you view everything, but not make any changes.",
      "Actions": [
        "*/read"
      ],
      "NotActions": [],
      "DataActions": [],
      "NotDataActions": [],
      "AssignableScopes": [
        "/"
      ]
    }
    ```
    
1. Redigera JSON-filen för att lägga till `"Microsoft.Support/*"`-åtgärden till `Actions`-egenskapen. Se till att inkludera ett kommatecken efter läsåtgärden. Den här åtgärden tillåter att användare skapar supportbegäranden.

1. Hämta ID för din prenumeration med hjälp av kommandot [Get-AzSubscription](/powershell/module/Az.Accounts/Get-AzSubscription).

    ```azurepowershell
    Get-AzSubscription
    ```

1. I `AssignableScopes` lägger du till ditt prenumerations-ID i följande format: `"/subscriptions/00000000-0000-0000-0000-000000000000"`

    Du måste lägga till explicita prenumerations-ID:n; annars tillåts du inte importera rollen i din prenumeration.

1. Ta bort egenskapsraden `Id` och ändra egenskapen `IsCustom` till `true`.

1. Ändra egenskaperna `Name` och `Description` till ”Reader Support Tickets” (Läsare av supportbegäranden) och ”View everything in the subscription and also open support tickets” (Visa allt i prenumerationen och även öppna supportbegäranden).

    Din JSON-fil ska se ut så här:

    ```json
    {
      "Name": "Reader Support Tickets",
      "IsCustom": true,
      "Description": "View everything in the subscription and also open support tickets.",
      "Actions": [
        "*/read",
        "Microsoft.Support/*"
      ],
      "NotActions": [],
      "DataActions": [],
      "NotDataActions": [],
      "AssignableScopes": [
        "/subscriptions/00000000-0000-0000-0000-000000000000"
      ]
    }
    ```
    
1. För att skapa en ny anpassad roll använder du kommandot [New-AzRoleDefinition](/powershell/module/az.resources/new-azroledefinition) och anger JSON-rolldefinitionsfilen.

    ```azurepowershell
    New-AzRoleDefinition -InputFile "C:\CustomRoles\ReaderSupportRole.json"
    ```

    ```Output
    Name             : Reader Support Tickets
    Id               : 22222222-2222-2222-2222-222222222222
    IsCustom         : True
    Description      : View everything in the subscription and also open support tickets.
    Actions          : {*/read, Microsoft.Support/*}
    NotActions       : {}
    DataActions      : {}
    NotDataActions   : {}
    AssignableScopes : {/subscriptions/00000000-0000-0000-0000-000000000000}
    ```
        
    Den nya anpassade rollen är nu tillgänglig i Azure-portalen och kan tilldelas till användare, grupper eller tjänstens huvudnamn precis som inbyggda roller.

## <a name="list-custom-roles"></a>Lista anpassade roller

- Om du vill lista alla dina anpassade roller använder du kommandot [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition).

    ```azurepowershell
    Get-AzRoleDefinition | ? {$_.IsCustom -eq $true} | FT Name, IsCustom
    ```

    ```Output
    Name                   IsCustom
    ----                   --------
    Reader Support Tickets     True
    ```
    
    Du kan även visa den anpassade rollen i Azure-portalen.

    ![skärmbild av anpassad roll som importerats i Azure-portalen](./media/tutorial-custom-role-powershell/custom-role-reader-support-tickets.png)

## <a name="update-a-custom-role"></a>Uppdatera en anpassad roll

Om du vill uppdatera den anpassade rollen kan du uppdatera JSON-filen eller använda objektet `PSRoleDefinition`.

1. För att uppdatera JSON-filen använder du kommandot [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition) för att mata ut den anpassade rollen i JSON-format.

    ```azurepowershell
    Get-AzRoleDefinition -Name "Reader Support Tickets" | ConvertTo-Json | Out-File C:\CustomRoles\ReaderSupportRole2.json
    ```

1. Öppna filen i en textredigerare.

1. I `Actions` lägger du till åtgärden för att skapa och hantera resursgruppsdistributioner `"Microsoft.Resources/deployments/*"`.

    Din uppdaterade JSON-fil ska se ut så här:

    ```json
    {
      "Name": "Reader Support Tickets",
      "Id": "22222222-2222-2222-2222-222222222222",
      "IsCustom": true,
      "Description": "View everything in the subscription and also open support tickets.",
      "Actions": [
        "*/read",
        "Microsoft.Support/*",
        "Microsoft.Resources/deployments/*"
      ],
      "NotActions": [],
      "DataActions": [],
      "NotDataActions": [],
      "AssignableScopes": [
        "/subscriptions/00000000-0000-0000-0000-000000000000"
      ]
    }
    ```
        
1. För att uppdatera den anpassade rollen använder du kommandot [Set-AzRoleDefinition](/powershell/module/az.resources/set-azroledefinition) och anger den uppdaterade JSON-filen.

    ```azurepowershell
    Set-AzRoleDefinition -InputFile "C:\CustomRoles\ReaderSupportRole2.json"
    ```

    ```Output
    Name             : Reader Support Tickets
    Id               : 22222222-2222-2222-2222-222222222222
    IsCustom         : True
    Description      : View everything in the subscription and also open support tickets.
    Actions          : {*/read, Microsoft.Support/*, Microsoft.Resources/deployments/*}
    NotActions       : {}
    DataActions      : {}
    NotDataActions   : {}
    AssignableScopes : {/subscriptions/00000000-0000-0000-0000-000000000000}
    ```

1. Om du vill använda objektet `PSRoleDefintion` för att uppdatera din anpassade roll använder du först kommandot [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition) för att hämta rollen.

    ```azurepowershell
    $role = Get-AzRoleDefinition "Reader Support Tickets"
    ```
    
1. Anropa metoden `Add` för att lägga till åtgärden för att läsa diagnostikinställningar.

    ```azurepowershell
    $role.Actions.Add("Microsoft.Insights/diagnosticSettings/*/read")
    ```

1. Använd kommandot [Set-AzRoleDefinition](/powershell/module/az.resources/set-azroledefinition) för att uppdatera rollen.

    ```azurepowershell
    Set-AzRoleDefinition -Role $role
    ```
    
    ```Output
    Name             : Reader Support Tickets
    Id               : 22222222-2222-2222-2222-222222222222
    IsCustom         : True
    Description      : View everything in the subscription and also open support tickets.
    Actions          : {*/read, Microsoft.Support/*, Microsoft.Resources/deployments/*,
                       Microsoft.Insights/diagnosticSettings/*/read}
    NotActions       : {}
    DataActions      : {}
    NotDataActions   : {}
    AssignableScopes : {/subscriptions/00000000-0000-0000-0000-000000000000}
    ```
    
## <a name="delete-a-custom-role"></a>Ta bort en anpassad roll

1. Använd kommandot [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition) för att hämta ID för den anpassade rollen.

    ```azurepowershell
    Get-AzRoleDefinition "Reader Support Tickets"
    ```

1. Använd kommandot [Remove-AzRoleDefinition](/powershell/module/az.resources/remove-azroledefinition) och ange roll-ID för att ta bort den anpassade rollen.

    ```azurepowershell
    Remove-AzRoleDefinition -Id "22222222-2222-2222-2222-222222222222"
    ```

    ```Output
    Confirm
    Are you sure you want to remove role definition with id '22222222-2222-2222-2222-222222222222'.
    [Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"):
    ```

1. När du ombeds bekräfta skriver du **Y**.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Skapa eller uppdatera Azure-anpassade roller med hjälp av Azure PowerShell](custom-roles-powershell.md)
