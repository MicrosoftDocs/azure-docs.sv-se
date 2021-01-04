---
title: Azure Key Vault att flytta ett valv till en annan prenumeration | Microsoft Docs
description: Vägledning om hur du flyttar ett nyckel valv till en annan prenumeration.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 05/05/2020
ms.author: mbaldwin
Customer intent: As a key vault administrator, I want to move my vault to another subscription.
ms.openlocfilehash: d881394391b7967fe602155eefc9844e013de34e
ms.sourcegitcommit: a4533b9d3d4cd6bb6faf92dd91c2c3e1f98ab86a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/22/2020
ms.locfileid: "97724773"
---
# <a name="moving-an-azure-key-vault-to-another-subscription"></a>Flytta en Azure Key Vault till en annan prenumeration

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="overview"></a>Översikt

> [!IMPORTANT]
> **Om du flyttar ett nyckel valv till en annan prenumeration ändras din miljö.**
> Se till att du förstår effekten av den här ändringen och följ anvisningarna i den här artikeln noggrant innan du bestämmer dig för att flytta nyckel valvet till en ny prenumeration.
> Om du använder hanterade tjänst identiteter (MSI) läser du anvisningarna efter flytten i slutet av dokumentet. 

[Azure Key Vault](overview.md) är automatiskt knutet till standard [Azure Active Directory](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis) klient-ID: t för den prenumeration som den skapas i. Du hittar klient-ID: t som är associerat med din prenumeration genom att följa den här [hand boken](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-how-to-find-tenant). Alla åtkomst princip poster och roll tilldelningar är också kopplade till detta klient-ID.  Om du flyttar din Azure-prenumeration från klient A till klient B, blir dina befintliga nyckel valv otillgängliga för tjänstens huvud namn (användare och program) i klient B. För att åtgärda det här problemet måste du:

* Ändra det klient-ID som är associerat med alla befintliga nyckel valv i prenumerationen till klient B.
* Ta bort alla åtkomstprincipposter.
* Lägg till nya åtkomst princip poster som är associerade med klient B.

Mer information om Azure Key Vault och Azure Active Directory finns i
- [Om Azure Key Vault](overview.md)
- [Vad är Azure Active Directory](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis)
- [Så här hittar du klient-ID:n](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-how-to-find-tenant)

## <a name="limitations"></a>Begränsningar

> [!IMPORTANT]
> **Det går inte att flytta nyckel valv som används för disk kryptering** Om du använder Key Vault med disk kryptering för en virtuell dator kan nyckel valvet inte flyttas till en annan resurs grupp eller en prenumeration när disk kryptering är aktiverat. Du måste inaktivera disk kryptering innan du flyttar nyckel valvet till en ny resurs grupp eller prenumeration. 

Vissa tjänst huvud namn (användare och program) är kopplade till en viss klient. Om du flyttar nyckel valvet till en prenumeration i en annan klient, finns det en risk att du inte kan återställa åtkomsten till ett särskilt tjänst huvud namn. Kontrol lera att alla viktiga tjänst huvud namn finns i klient organisationen där du flyttar nyckel valvet.

## <a name="prerequisites"></a>Förutsättningar

* [Deltagar](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#contributor) nivå åtkomst eller högre till den aktuella prenumerationen där nyckel valvet finns. Du kan tilldela rollen med hjälp av [Azure Portal](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal), [Azure CLI](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli)eller [PowerShell](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-powershell).
* [Deltagar](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#contributor) nivå åtkomst eller högre till den prenumeration där du vill flytta nyckel valvet. Du kan tilldela rollen med hjälp av [Azure Portal](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal), [Azure CLI](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli)eller [PowerShell](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-powershell).
* En resurs grupp i den nya prenumerationen. Du kan skapa en med hjälp av [Azure Portal](https://docs.microsoft.com/azure/azure-resource-manager/management/manage-resource-groups-portal), [POWERSHELL](https://docs.microsoft.com/azure/azure-resource-manager/management/manage-resource-groups-powershell)eller [Azure CLI](https://docs.microsoft.com/azure/azure-resource-manager/management/manage-resource-groups-cli).

Du kan kontrol lera befintliga roller med hjälp av [Azure Portal](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-list-portal), [POWERSHELL](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-list-powershell), [Azure CLI](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-list-cli)eller [REST API](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-list-rest).


## <a name="moving-a-key-vault-to-a-new-subscription"></a>Flytta ett nyckel valv till en ny prenumeration

1. Logga in på Azure Portal på https://portal.azure.com.
2. Navigera till ditt [nyckel valv](overview.md)
3. Klicka på fliken "Översikt"
4. Välj knappen "flytta"
5. Välj "flytta till en annan prenumeration" från List Rute alternativen
6. Välj den resurs grupp där du vill flytta nyckel valvet
7. Bekräfta varningen om att flytta resurser
8. Välj OK

## <a name="additional-steps-when-subscription-is-in-a-new-tenant"></a>Ytterligare steg när prenumerationen är i en ny klient

Om du har flyttat ditt nyckel valv till en prenumeration i en ny klient måste du uppdatera klient-ID: t manuellt och ta bort gamla åtkomst principer och roll tilldelningar. Här är självstudier för de här stegen i PowerShell och Azure CLI. Om du använder PowerShell kan du behöva köra kommandot Clear-AzContext som dokumenteras nedan så att du kan se resurser utanför det valda omfånget. 

### <a name="update-tenant-id-in-a-key-vault"></a>Uppdatera klient-ID i ett nyckel valv

```azurepowershell
Select-AzSubscription -SubscriptionId <your-subscriptionId>                # Select your Azure Subscription
$vaultResourceId = (Get-AzKeyVault -VaultName myvault).ResourceId          # Get your key vault's Resource ID 
$vault = Get-AzResource -ResourceId $vaultResourceId -ExpandProperties     # Get the properties for your key vault
$vault.Properties.TenantId = (Get-AzContext).Tenant.TenantId               # Change the Tenant that your key vault resides in
$vault.Properties.AccessPolicies = @()                                     # Access policies can be updated with real
                                                                           # applications/users/rights so that it does not need to be                             # done after this whole activity. Here we are not setting 
                                                                           # any access policies. 
Set-AzResource -ResourceId $vaultResourceId -Properties $vault.Properties  # Modifies the key vault's properties.

Clear-AzContext                                                            #Clear the context from PowerShell
Connect-AzAccount                                                          #Log in again to confirm you have the correct tenant id
````

```azurecli
az account set -s <your-subscriptionId>                                    # Select your Azure Subscription
tenantId=$(az account show --query tenantId)                               # Get your tenantId
az keyvault update -n myvault --remove Properties.accessPolicies           # Remove the access policies
az keyvault update -n myvault --set Properties.tenantId=$tenantId          # Update the key vault tenantId
```
### <a name="update-access-policies-and-role-assignments"></a>Uppdatera åtkomst principer och roll tilldelningar

> [!NOTE]
> Om Key Vault använder [Azure RBAC](https://docs.microsoft.com/azure/role-based-access-control/overview) -behörighets modell. Du måste också ta bort roll tilldelningar för nyckel valvet. Du kan ta bort roll tilldelningar med hjälp av [Azure Portal](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal), [Azure CLI](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli)eller [PowerShell](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-powershell). 

Nu när valvet är associerat med rätt klient-ID och gamla åtkomst princip poster eller roll tilldelningar tas bort, anger du nya åtkomst princip poster eller roll tilldelningar.

Information om hur du tilldelar principer finns i:
- [Tilldela en åtkomst princip med hjälp av portalen](assign-access-policy-portal.md)
- [Tilldela en åtkomst princip med Azure CLI](assign-access-policy-cli.md)
- [Tilldela en åtkomst princip med hjälp av PowerShell](assign-access-policy-powershell.md)

Information om hur du lägger till roll tilldelningar finns i:
- [Lägg till roll tilldelning med hjälp av portalen](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal)
- [Lägg till roll tilldelning med Azure CLI](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli)
- [Lägg till roll tilldelning med PowerShell](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-powershell)


### <a name="update-managed-identities"></a>Uppdatera hanterade identiteter

Om du överför hela prenumerationen och använder en hanterad identitet för Azure-resurser måste du också uppdatera den till den nya Azure Active Directory-klienten. För ytterligare information om hanterade identiteter, [Översikt över hanterad identitet](../../active-directory/managed-identities-azure-resources/overview.md).

Om du använder hanterad identitet måste du också uppdatera identiteten eftersom den gamla identiteten inte längre kommer att ha rätt Azure Active Directory klient. Se följande dokument för att hjälpa till att lösa det här problemet. 

* [Uppdaterar MSI](../../active-directory/managed-identities-azure-resources/known-issues.md#transferring-a-subscription-between-azure-ad-directories)
* [Överför prenumeration till ny katalog](../../role-based-access-control/transfer-subscription.md)

## <a name="next-steps"></a>Nästa steg

- Läs mer om [nycklar, hemligheter och certifikat](about-keys-secrets-certificates.md)
- För konceptuell information, inklusive hur du tolkar Key Vault loggar, se [Key Vault loggning](logging.md)
- [Utvecklarguide för Key Vault](../general/developers-guide.md)
- [Skydda ditt nyckel valv](secure-your-key-vault.md)
- [Konfigurera Azure Key Vault brand väggar och virtuella nätverk](network-security.md)
