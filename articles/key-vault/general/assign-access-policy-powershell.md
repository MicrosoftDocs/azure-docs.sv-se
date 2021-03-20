---
title: Tilldela en princip för Azure Key Vault åtkomst
description: Använda Azure Portal, Azure CLI eller Azure PowerShell för att tilldela en Key Vault åtkomst princip till ett huvud namn för tjänsten eller en program identitet.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 08/27/2020
ms.author: mbaldwin
ms.openlocfilehash: 4cf0393cdb0aae1b352b81df4893f874f34970cc
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "97934545"
---
# <a name="assign-a-key-vault-access-policy-using-azure-powershell"></a>Tilldela en Key Vault åtkomst princip med Azure PowerShell

En Key Vault åtkomst princip bestämmer om ett specifikt tjänstens huvud namn, nämligen ett program eller en användar grupp, kan utföra olika åtgärder på Key Vault [hemligheter](../secrets/index.yml), [nycklar](../keys/index.yml)och [certifikat](../certificates/index.yml). Du kan tilldela åtkomst principer med hjälp av [Azure Portal](assign-access-policy-portal.md), [Azure CLI](assign-access-policy-cli.md)eller Azure PowerShell (den här artikeln).

[!INCLUDE [key-vault-access-policy-limits.md](../../../includes/key-vault-access-policy-limits.md)]

Mer information om hur du skapar grupper i Azure Active Directory med hjälp av Azure PowerShell finns i [New-AzureADGroup](/powershell/module/azuread/new-azureadgroup) och [Add-AzADGroupMember](/powershell/module/az.resources/add-azadgroupmember).

## <a name="configure-powershell-and-sign-in"></a>Konfigurera PowerShell och inloggning

1. Om du vill köra kommandon lokalt installerar du [Azure PowerShell](/powershell/azure/) om du inte redan gjort det.

    Använd [Azure Cloud Shell](../../cloud-shell/overview.md)för att köra kommandon direkt i molnet.

1. Endast lokal PowerShell:

    1. Installera [Azure Active Directory PowerShell-modulen](https://www.powershellgallery.com/packages/AzureAD).

    1. Logga in på Azure:

        ```azurepowershell-interactive
        Login-AzAccount
        ```
    
## <a name="acquire-the-object-id"></a>Hämta objekt-ID

Bestäm objekt-ID för det program, den grupp eller användare som du vill tilldela åtkomst principen till:

- Program och andra tjänst huvud namn: Använd cmdleten [Get-AzADServicePrincipal](/powershell/module/az.resources/get-azadserviceprincipal) med `-SearchString` parametern för att filtrera resultat till namnet på önskat tjänst huvud namn:

    ```azurepowershell-interactive
    Get-AzADServicePrincipal -SearchString <search-string>
    ```

- Grupper: Använd cmdleten [Get-AzADGroup](/powershell/module/az.resources/get-azadgroup) med `-SearchString` parametern för att filtrera resultat till namnet på önskad grupp:

    ```azurepowershell-interactive
    Get-AzADGroup -SearchString <search-string>
    ```
    
    I utdata visas objekt-ID: t som `Id` .

- Användare: Använd cmdleten [Get-AzADUser](/powershell/module/az.resources/get-azaduser) för att skicka användarens e-postadress till `-UserPrincipalName` parametern.

    ```azurepowershell-interactive
     Get-AzAdUser -UserPrincipalName <email-address-of-user>
    ```

    I utdata visas objekt-ID: t som `Id` .

## <a name="assign-the-access-policy"></a>Tilldela åtkomst principen

Använd cmdleten [set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy) för att tilldela åtkomst principen:

```azurepowershell-interactive
Set-AzKeyVaultAccessPolicy -VaultName <key-vault-name> -ObjectId <Id> -PermissionsToSecrets <secrets-permissions> -PermissionsToKeys <keys-permissions> -PermissionsToCertificates <certificate-permissions    
```

Du behöver bara inkludera `-PermissionsToSecrets` , `-PermissionsToKeys` och `-PermissionsToCertificates` när du tilldelar behörigheter till dessa specifika typer. De tillåtna värdena för `<secret-permissions>` , `<key-permissions>` och `<certificate-permissions>` anges i dokumentationen för [set-AzKeyVaultAccessPolicy-Parameters](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy#parameters) .

## <a name="next-steps"></a>Nästa steg

- [Azure Key Vault säkerhet: identitets-och åtkomst hantering](security-overview.md#identity-management)
- [Skydda nyckel valvet](secure-your-key-vault.md).
- [Guide för Azure Key Vault utvecklare](developers-guide.md)