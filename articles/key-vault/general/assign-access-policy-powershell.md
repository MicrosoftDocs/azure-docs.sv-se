---
title: Tilldela en Azure Key Vault åtkomstprincip
description: Så här använder du Azure Portal, Azure CLI eller Azure PowerShell för att tilldela en Key Vault-åtkomstprincip till ett säkerhetsobjekt eller en programidentitet.
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 08/27/2020
ms.author: mbaldwin
ms.openlocfilehash: 1c7c31f38d6a59f4ded17e1e1fd7e985ce59922a
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107751426"
---
# <a name="assign-a-key-vault-access-policy-using-azure-powershell"></a>Tilldela en Key Vault åtkomstprincip med hjälp av Azure PowerShell

En Key Vault-åtkomstprincip avgör om ett visst säkerhetsobjekt, nämligen en användare, ett program eller en användargrupp, kan utföra olika åtgärder på Key Vault [hemligheter,](../secrets/index.yml) [nycklar](../keys/index.yml)och [certifikat.](../certificates/index.yml) Du kan tilldela åtkomstprinciper med [hjälp Azure Portal,](assign-access-policy-portal.md) [Azure CLI](assign-access-policy-cli.md)eller Azure PowerShell (den här artikeln).

[!INCLUDE [key-vault-access-policy-limits.md](../../../includes/key-vault-access-policy-limits.md)]

Mer information om hur du skapar grupper i Azure Active Directory med Azure PowerShell finns [i New-AzureADGroup](/powershell/module/azuread/new-azureadgroup) och [Add-AzADGroupMember](/powershell/module/az.resources/add-azadgroupmember).

## <a name="configure-powershell-and-sign-in"></a>Konfigurera PowerShell och logga in

1. Om du vill köra kommandon lokalt [installerar Azure PowerShell](/powershell/azure/) om du inte redan har gjort det.

    Om du vill köra kommandon direkt i molnet använder du [Azure Cloud Shell](../../cloud-shell/overview.md).

1. Endast lokalt PowerShell:

    1. Installera Azure Active Directory [PowerShell-modulen](https://www.powershellgallery.com/packages/AzureAD).

    1. Logga in på Azure:

        ```azurepowershell-interactive
        Login-AzAccount
        ```
    
## <a name="acquire-the-object-id"></a>Hämta objekt-ID:t

Fastställ objekt-ID:t för programmet, gruppen eller användaren som du vill tilldela åtkomstprincipen till:

- Program och andra tjänsthuvudnamn: Använd cmdleten [Get-AzADServicePrincipal](/powershell/module/az.resources/get-azadserviceprincipal) med parametern för att filtrera resultaten efter namnet på det `-SearchString` önskade tjänsthuvudnamnet:

    ```azurepowershell-interactive
    Get-AzADServicePrincipal -SearchString <search-string>
    ```

- Grupper: Använd [cmdleten Get-AzADGroup](/powershell/module/az.resources/get-azadgroup) med parametern för `-SearchString` att filtrera resultaten efter namnet på den önskade gruppen:

    ```azurepowershell-interactive
    Get-AzADGroup -SearchString <search-string>
    ```
    
    I utdata visas objekt-ID:t som `Id` .

- Användare: Använd [cmdleten Get-AzADUser](/powershell/module/az.resources/get-azaduser) och skicka användarens e-postadress till `-UserPrincipalName` parametern .

    ```azurepowershell-interactive
     Get-AzAdUser -UserPrincipalName <email-address-of-user>
    ```

    I utdata visas objekt-ID:t som `Id` .

## <a name="assign-the-access-policy"></a>Tilldela åtkomstprincipen

Använd [cmdleten Set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy) för att tilldela åtkomstprincipen:

```azurepowershell-interactive
Set-AzKeyVaultAccessPolicy -VaultName <key-vault-name> -ObjectId <Id> -PermissionsToSecrets <secrets-permissions> -PermissionsToKeys <keys-permissions> -PermissionsToCertificates <certificate-permissions    
```

Du behöver bara inkludera `-PermissionsToSecrets` , och när du `-PermissionsToKeys` `-PermissionsToCertificates` tilldelar behörigheter till dessa specifika typer. Tillåtna värden för `<secret-permissions>` , och anges i dokumentationen `<key-permissions>` `<certificate-permissions>` [Set-AzKeyVaultAccessPolicy –](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy#parameters) Parametrar.

## <a name="next-steps"></a>Nästa steg

- [Azure Key Vault: Identitets- och åtkomsthantering](security-overview.md#identity-management)
- [Skydda nyckelvalvet](security-overview.md).
- [Azure Key Vault utvecklarguide](developers-guide.md)