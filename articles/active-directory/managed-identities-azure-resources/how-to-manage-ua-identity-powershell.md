---
title: Skapa, lista & bort användar tilldelad hanterad identitet med hjälp Azure PowerShell – Azure AD
description: Stegvisa instruktioner om hur du skapar, listar och tar bort användar tilldelad hanterad identitet med hjälp av Azure PowerShell.
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
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: af76affd9f4034401225e82de4e25e8b0a51125a
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107784847"
---
# <a name="create-list-or-delete-a-user-assigned-managed-identity-using-azure-powershell"></a>Skapa, visa eller ta bort en användar tilldelad hanterad identitet med Azure PowerShell

Hanterade identiteter för Azure-resurser ger Azure-tjänster en hanterad identitet i Azure Active Directory. Du kan använda den här identiteten för att autentisera mot tjänster som stöder Azure AD-autentisering, utan att behöva autentiseringsuppgifter i din kod. 

I den här artikeln får du lära dig hur du skapar, listar och tar bort en användar tilldelad hanterad identitet med Azure PowerShell.

[!INCLUDE [az-powershell-update](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Förutsättningar

- Om du inte är bekant med hanterade identiteter för Azure-resurser kan du läsa [översiktsavsnittet](overview.md). **Se till att granska skillnaden [mellan en system-tilldelad och användar tilldelad hanterad identitet](overview.md#managed-identity-types)**.
- Om du inte redan har ett Azure-konto [registrerar du dig för ett kostnadsfritt konto](https://azure.microsoft.com/free/) innan du fortsätter.
- Om du vill köra exempelskripten har du två alternativ:
    - Använd den [Azure Cloud Shell](../../cloud-shell/overview.md), som du kan öppna med hjälp av **knappen Prova** i det övre högra hörnet av kodblock.
    - Kör skript lokalt med Azure PowerShell, enligt beskrivningen i nästa avsnitt.

### <a name="configure-azure-powershell-locally"></a>Konfigurera Azure PowerShell lokalt

Om du Azure PowerShell lokalt för den här artikeln (i stället för att använda Cloud Shell) gör du följande:

1. Installera [den senaste versionen Azure PowerShell](/powershell/azure/install-az-ps) om du inte redan har gjort det.

1. Logga in på Azure:

    ```azurepowershell
    Connect-AzAccount
    ```

1. Installera [den senaste versionen av PowerShellGet](/powershell/scripting/gallery/installing-psget#for-systems-with-powershell-50-or-newer-you-can-install-the-latest-powershellget).

    ```azurepowershell
    Install-Module -Name PowerShellGet -AllowPrerelease
    ```

    Du kan behöva ta `Exit` bort den aktuella PowerShell-sessionen när du har kört det här kommandot för nästa steg.

1. Installera förhandsversionen av modulen för att `Az.ManagedServiceIdentity` utföra åtgärderna för användar tilldelad hanterad identitet i den här artikeln:

    ```azurepowershell
    Install-Module -Name Az.ManagedServiceIdentity -AllowPrerelease
    ```

## <a name="create-a-user-assigned-managed-identity"></a>Skapa en användartilldelad hanterad identitet

Om du vill skapa en användar tilldelad hanterad identitet måste ditt konto ha [rolltilldelningen Hanterad](../../role-based-access-control/built-in-roles.md#managed-identity-contributor) identitetsdeltagare.

Om du vill skapa en användar tilldelad hanterad identitet använder du `New-AzUserAssignedIdentity` kommandot . Parametern anger resursgruppen där den användar tilldelade hanterade identiteten ska `ResourceGroupName` skapas, och `-Name` parametern anger dess namn. Ersätt `<RESOURCE GROUP>` `<USER ASSIGNED IDENTITY NAME>` parametervärdena och med dina egna värden:

[!INCLUDE [ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]

 ```azurepowershell-interactive
New-AzUserAssignedIdentity -ResourceGroupName <RESOURCEGROUP> -Name <USER ASSIGNED IDENTITY NAME>
```
## <a name="list-user-assigned-managed-identities"></a>Visa en lista över användar tilldelade hanterade identiteter

Om du vill visa/läsa en användar tilldelad hanterad identitet måste ditt konto ha [rolltilldelningen](../../role-based-access-control/built-in-roles.md#managed-identity-operator) Hanterad identitetsoperatör [eller Hanterad](../../role-based-access-control/built-in-roles.md#managed-identity-contributor) identitetsdeltagare.

Om du vill visa en lista över användartilldelade hanterade identiteter använder du kommandot [Get-AzUserAssigned].  Parametern `-ResourceGroupName` anger den resursgrupp där den användar tilldelade hanterade identiteten skapades. Ersätt `<RESOURCE GROUP>` med ditt eget värde:

```azurepowershell-interactive
Get-AzUserAssignedIdentity -ResourceGroupName <RESOURCE GROUP>
```
I svaret har användar tilldelade hanterade identiteter ett värde `"Microsoft.ManagedIdentity/userAssignedIdentities"` som returneras för nyckeln, `Type` .

`Type :Microsoft.ManagedIdentity/userAssignedIdentities`

## <a name="delete-a-user-assigned-managed-identity"></a>Ta bort en användar tilldelad hanterad identitet

Om du vill ta bort en användar tilldelad hanterad identitet måste ditt konto ha [rolltilldelningen Hanterad](../../role-based-access-control/built-in-roles.md#managed-identity-contributor) identitetsdeltagare.

Om du vill ta bort en användar tilldelad hanterad identitet använder du `Remove-AzUserAssignedIdentity` kommandot .  Parametern `-ResourceGroupName` anger den resursgrupp där den användar tilldelade identiteten skapades och `-Name` parametern anger dess namn. Ersätt `<RESOURCE GROUP>` `<USER ASSIGNED IDENTITY NAME>` parametervärdena och med dina egna värden:

 ```azurepowershell-interactive
Remove-AzUserAssignedIdentity -ResourceGroupName <RESOURCE GROUP> -Name <USER ASSIGNED IDENTITY NAME>
```
> [!NOTE]
> Om du tar bort en användar tilldelad hanterad identitet tas inte referensen bort från alla resurser som den har tilldelats till. Identitetstilldelningar måste tas bort separat.

## <a name="next-steps"></a>Nästa steg

En fullständig lista med mer information om de Azure PowerShell identiteter för Azure-resurskommandon finns i [Az.ManagedServiceIdentity](/powershell/module/az.managedserviceidentity#managed_service_identity).
