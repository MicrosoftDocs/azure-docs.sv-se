---
title: Tilldela en Azure Key Vault åtkomstprincip (CLI)
description: Så här använder du Azure CLI för att tilldela en Key Vault åtkomstprincip till ett säkerhetsobjekt eller en programidentitet.
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 08/27/2020
ms.author: mbaldwin
ms.openlocfilehash: 96b4daa027871201a201b253721114372e58f377
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107751444"
---
# <a name="assign-a-key-vault-access-policy"></a>Tilldela en Key Vault åtkomstprincip

En Key Vault-åtkomstprincip avgör om ett visst säkerhetsobjekt, nämligen en användare, ett program eller en användargrupp, kan utföra olika åtgärder på Key Vault [hemligheter,](../secrets/index.yml) [nycklar](../keys/index.yml)och [certifikat](../certificates/index.yml). Du kan tilldela åtkomstprinciper med [hjälp Azure Portal,](assign-access-policy-portal.md)Azure CLI (den här artikeln) eller [Azure PowerShell](assign-access-policy-powershell.md).

[!INCLUDE [key-vault-access-policy-limits.md](../../../includes/key-vault-access-policy-limits.md)]

Mer information om hur du skapar grupper i Azure Active Directory med Hjälp av Azure CLI finns i [az ad group create](/cli/azure/ad/group#az-ad-group-create) och az ad group member [add](/cli/azure/ad/group/member#az-ad-group-member-add).

## <a name="configure-the-azure-cli-and-sign-in"></a>Konfigurera Azure CLI och logga in

1. Om du vill köra Azure CLI-kommandon lokalt installerar du [Azure CLI](/cli/azure/install-azure-cli).
 
    Om du vill köra kommandon direkt i molnet använder du [Azure Cloud Shell](../../cloud-shell/overview.md).

1. Endast lokalt CLI: logga in på Azure med `az login` :

    ```bash
    az login
    ```

    Kommandot `az login` öppnar ett webbläsarfönster för att samla in autentiseringsuppgifter om det behövs.

## <a name="acquire-the-object-id"></a>Hämta objekt-ID:t

Fastställ objekt-ID:t för programmet, gruppen eller användaren som du vill tilldela åtkomstprincipen till:

- Program och andra tjänsthuvudnamn: Använd kommandot [az ad sp list](/cli/azure/ad/sp#az-ad-sp-list) för att hämta tjänstens huvudnamn. Granska kommandots utdata för att fastställa objekt-ID:t för det säkerhetsobjekt som du vill tilldela åtkomstprincipen till.

    ```azurecli-interactive
    az ad sp list --show-mine
    ```

- Grupper: Använd [kommandot az ad group list](/cli/azure/ad/group#az-ad-group-list) och filtrera resultatet med `--display-name` parametern :

     ```azurecli-interactive
    az ad group list --display-name <search-string>
    ```

- Användare: Använd [kommandot az ad user show](/cli/azure/ad/user#az-ad-user-show) och skicka användarens e-postadress i `--id` parametern :

    ```azurecli-interactive
    az ad user show --id <email-address-of-user>
    ```

## <a name="assign-the-access-policy"></a>Tilldela åtkomstprincipen
    
Använd kommandot [az keyvault set-policy](/cli/azure/keyvault#az-keyvault-set-policy) för att tilldela önskade behörigheter:

```azurecli-interactive
az keyvault set-policy --name myKeyVault --object-id <object-id> --secret-permissions <secret-permissions> --key-permissions <key-permissions> --certificate-permissions <certificate-permissions>
```

Ersätt `<object-id>` med objekt-ID:t för ditt säkerhetsobjekt.

Du behöver bara inkludera `--secret-permissions` , och när du `--key-permissions` `--certificate-permissions` tilldelar behörigheter till dessa specifika typer. De tillåtna värdena för `<secret-permissions>` , och anges i dokumentationen az `<key-permissions>` `<certificate-permissions>` [keyvault set-policy.](/cli/azure/keyvault#az-keyvault-set-policy)

## <a name="next-steps"></a>Nästa steg

- [Azure Key Vault: Identitets- och åtkomsthantering](security-overview.md#identity-management)
- [Skydda ditt nyckelvalv.](security-overview.md)
- [Azure Key Vault utvecklarguide](developers-guide.md)