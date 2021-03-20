---
title: Skapa, Visa & ta bort användardefinierad hanterad identitet med hjälp av Azure PowerShell-Azure AD
description: Steg för steg-anvisningar om hur du skapar, visar och tar bort användarspecifika hanterade identiteter med hjälp av Azure PowerShell.
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/02/2020
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.openlocfilehash: 636f40f0c425c25dfe7f41f1f404afc90ed5ba56
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96548230"
---
# <a name="create-list-or-delete-a-user-assigned-managed-identity-using-azure-powershell"></a>Skapa, Visa eller ta bort en användardefinierad hanterad identitet med hjälp av Azure PowerShell

Hanterade identiteter för Azure-resurser ger Azure-tjänster med en hanterad identitet i Azure Active Directory. Du kan använda den här identiteten för att autentisera till tjänster som stöder Azure AD-autentisering, utan att behöva ange autentiseringsuppgifter i koden. 

I den här artikeln får du lära dig hur du skapar, visar och tar bort en användardefinierad hanterad identitet med hjälp av Azure PowerShell.

[!INCLUDE [az-powershell-update](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Förutsättningar

- Om du inte känner till hanterade identiteter för Azure-resurser kan du läsa [avsnittet Översikt](overview.md). **Se till att granska [skillnaden mellan en tilldelad och användardefinierad hanterad identitet](overview.md#managed-identity-types)**.
- Om du inte redan har ett Azure-konto [registrerar du dig för ett kostnadsfritt konto](https://azure.microsoft.com/free/) innan du fortsätter.
- Om du vill köra exempel skripten har du två alternativ:
    - Använd [Azure Cloud Shell](../../cloud-shell/overview.md)som du kan öppna med knappen **prova** på det övre högra hörnet av kodblock.
    - Kör skript lokalt med Azure PowerShell, enligt beskrivningen i nästa avsnitt.

### <a name="configure-azure-powershell-locally"></a>Konfigurera Azure PowerShell lokalt

Utför följande steg för att använda Azure PowerShell lokalt för den här artikeln (i stället för att använda Cloud Shell):

1. Installera [den senaste versionen av Azure PowerShell](/powershell/azure/install-az-ps) om du inte redan gjort det.

1. Logga in på Azure:

    ```azurepowershell
    Connect-AzAccount
    ```

1. Installera [den senaste versionen av PowerShellGet](/powershell/scripting/gallery/installing-psget#for-systems-with-powershell-50-or-newer-you-can-install-the-latest-powershellget).

    ```azurepowershell
    Install-Module -Name PowerShellGet -AllowPrerelease
    ```

    Du kan behöva använda `Exit` den aktuella PowerShell-sessionen när du har kört det här kommandot för nästa steg.

1. Installera för hands versionen av `Az.ManagedServiceIdentity` modulen för att utföra de användare som tilldelats hanterade identitets åtgärder i den här artikeln:

    ```azurepowershell
    Install-Module -Name Az.ManagedServiceIdentity -AllowPrerelease
    ```

## <a name="create-a-user-assigned-managed-identity"></a>Skapa en användartilldelad hanterad identitet

För att skapa en användardefinierad hanterad identitet måste ditt konto ha roll tilldelningen [hanterad identitets deltagare](../../role-based-access-control/built-in-roles.md#managed-identity-contributor) .

Använd kommandot för att skapa en användardefinierad hanterad identitet `New-AzUserAssignedIdentity` . `ResourceGroupName`Parametern anger den resurs grupp där den tilldelade hanterade identiteten ska skapas och `-Name` parametern anger dess namn. Ersätt `<RESOURCE GROUP>` `<USER ASSIGNED IDENTITY NAME>` parameter värden och med dina egna värden:

[!INCLUDE [ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]

 ```azurepowershell-interactive
New-AzUserAssignedIdentity -ResourceGroupName <RESOURCEGROUP> -Name <USER ASSIGNED IDENTITY NAME>
```
## <a name="list-user-assigned-managed-identities"></a>Lista användare-tilldelade hanterade identiteter

Om du vill visa en lista över/läsa en användardefinierad hanterad identitet måste ditt konto ha roll tilldelningen [hanterad identitet](../../role-based-access-control/built-in-roles.md#managed-identity-operator) eller [hanterad identitets deltagare](../../role-based-access-control/built-in-roles.md#managed-identity-contributor) .

Använd kommandot [Get-AzUserAssigned] för att visa en lista över användare som tilldelats hanterade identiteter.  `-ResourceGroupName`Parametern anger resurs gruppen där den användare som tilldelats den hanterade identiteten skapades. Ersätt `<RESOURCE GROUP>` med ditt eget värde:

```azurepowershell-interactive
Get-AzUserAssignedIdentity -ResourceGroupName <RESOURCE GROUP>
```
I svaret har användare tilldelade hanterade identiteter `"Microsoft.ManagedIdentity/userAssignedIdentities"` värdet returnerat för nyckeln `Type` .

`Type :Microsoft.ManagedIdentity/userAssignedIdentities`

## <a name="delete-a-user-assigned-managed-identity"></a>Ta bort en användare som tilldelats en hanterad identitet

För att ta bort en användardefinierad hanterad identitet måste ditt konto ha roll tilldelningen [hanterad identitets deltagare](../../role-based-access-control/built-in-roles.md#managed-identity-contributor) .

Om du vill ta bort en användardefinierad hanterad identitet använder du `Remove-AzUserAssignedIdentity` kommandot.  `-ResourceGroupName`Parametern anger resurs gruppen där den användardefinierade identiteten skapades och `-Name` parametern anger dess namn. Ersätt `<RESOURCE GROUP>` och `<USER ASSIGNED IDENTITY NAME>` parametrarna värden med dina egna värden:

 ```azurepowershell-interactive
Remove-AzUserAssignedIdentity -ResourceGroupName <RESOURCE GROUP> -Name <USER ASSIGNED IDENTITY NAME>
```
> [!NOTE]
> Om du tar bort en tilldelad hanterad identitet tas inte referensen bort från alla resurser som den har tilldelats. Identitets tilldelningar måste tas bort separat.

## <a name="next-steps"></a>Nästa steg

En fullständig lista och mer information om Azure PowerShell hanterade identiteter för Azure-resurser finns i [AZ. ManagedServiceIdentity](/powershell/module/az.managedserviceidentity#managed_service_identity).
