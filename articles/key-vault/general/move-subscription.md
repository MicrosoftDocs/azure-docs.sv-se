---
title: Azure Key Vault ett valv till en annan prenumeration | Microsoft Docs
description: Vägledning om hur du flyttar ett nyckelvalv till en annan prenumeration.
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 05/05/2020
ms.author: mbaldwin
ms.openlocfilehash: 1a1cd8c051f9e04c09ef2986805873d8e7fea54e
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2021
ms.locfileid: "107817638"
---
# <a name="moving-an-azure-key-vault-to-another-subscription"></a>Flytta en Azure Key Vault-instans till en annan prenumeration

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="overview"></a>Översikt

> [!IMPORTANT]
> **Om du flyttar ett nyckelvalv till en annan prenumeration kommer det att leda till en stor ändring i din miljö.**
> Se till att du förstår effekten av den här ändringen och följ riktlinjerna i den här artikeln noggrant innan du bestämmer dig för att flytta nyckelvalvet till en ny prenumeration.
> Om du använder hanterade tjänstidentiteter (MSI) läser du anvisningarna efter flytten i slutet av dokumentet. 

[Azure Key Vault](overview.md) är automatiskt kopplat till [Azure Active Directory](../../active-directory/fundamentals/active-directory-whatis.md) klientorganisations-ID för prenumerationen som den skapas i. Du hittar det klient-ID som är associerat med din prenumeration genom att följa den här [guiden.](../../active-directory/fundamentals/active-directory-how-to-find-tenant.md) Alla åtkomstprincipposter och rolltilldelningar är också knutna till detta klientorganisations-ID.  Om du flyttar din Azure-prenumeration från klient A till klient B kommer dina befintliga nyckelvalv inte att vara tillgängliga för tjänstens huvudnamn (användare och program) i klient B. För att åtgärda det här problemet måste du:

* Ändra det klient-ID som är associerat med alla befintliga nyckelvalv i prenumerationen till klient B.
* Ta bort alla åtkomstprincipposter.
* Lägg till nya åtkomstprincipposter som är associerade med klient B.

Mer information om Azure Key Vault och Azure Active Directory finns i
- [Om Azure Key Vault](overview.md)
- [Vad är Azure Active Directory](../../active-directory/fundamentals/active-directory-whatis.md)
- [Så här hittar du klient-ID:n](../../active-directory/fundamentals/active-directory-how-to-find-tenant.md)

## <a name="limitations"></a>Begränsningar

> [!IMPORTANT]
> **Nyckelvalv som används för diskkryptering kan inte flyttas** Om du använder nyckelvalv med diskkryptering för en virtuell dator kan inte nyckelvalvet flyttas till en annan resursgrupp eller en prenumeration när diskkryptering är aktiverat. Du måste inaktivera diskkryptering innan du flyttar nyckelvalvet till en ny resursgrupp eller prenumeration. 

Vissa tjänsthuvudnamn (användare och program) är bundna till en specifik klientorganisation. Om du flyttar nyckelvalvet till en prenumeration i en annan klientorganisation finns det en risk att du inte kan återställa åtkomsten till ett specifikt huvudnamn för tjänsten. Kontrollera att alla viktiga tjänsthuvudnamn finns i den klientorganisation där du flyttar nyckelvalvet.

## <a name="prerequisites"></a>Förutsättningar

* [Åtkomst](../../role-based-access-control/built-in-roles.md#contributor) på deltagarnivå eller högre till den aktuella prenumerationen där nyckelvalvet finns. Du kan tilldela en roll [Azure Portal,](../../role-based-access-control/role-assignments-portal.md) [Azure CLI](../../role-based-access-control/role-assignments-cli.md)eller [PowerShell.](../../role-based-access-control/role-assignments-powershell.md)
* [Åtkomst](../../role-based-access-control/built-in-roles.md#contributor) på deltagarnivå eller högre till prenumerationen där du vill flytta nyckelvalvet. Du kan tilldela en roll [med Azure Portal,](../../role-based-access-control/role-assignments-portal.md) [Azure CLI](../../role-based-access-control/role-assignments-cli.md)eller [PowerShell.](../../role-based-access-control/role-assignments-powershell.md)
* En resursgrupp i den nya prenumerationen. Du kan skapa en med [Azure Portal,](../../azure-resource-manager/management/manage-resource-groups-portal.md) [PowerShell](../../azure-resource-manager/management/manage-resource-groups-powershell.md)eller [Azure CLI.](../../azure-resource-manager/management/manage-resource-groups-cli.md)

Du kan kontrollera befintliga roller med [Azure Portal,](../../role-based-access-control/role-assignments-list-portal.md) [PowerShell,](../../role-based-access-control/role-assignments-list-powershell.md) [Azure CLI](../../role-based-access-control/role-assignments-list-cli.md)eller [REST API](../../role-based-access-control/role-assignments-list-rest.md).


## <a name="moving-a-key-vault-to-a-new-subscription"></a>Flytta ett nyckelvalv till en ny prenumeration

1. Logga in på Azure Portal på https://portal.azure.com.
2. Gå till ditt [nyckelvalv](overview.md)
3. Klicka på fliken Översikt
4. Välj knappen Flytta
5. Välj Flytta till en annan prenumeration i listrutan
6. Välj den resursgrupp där du vill flytta nyckelvalvet
7. Bekräfta varningen om att flytta resurser
8. Välj OK

## <a name="additional-steps-when-subscription-is-in-a-new-tenant"></a>Ytterligare steg när prenumerationen finns i en ny klientorganisation

Om du har flyttat nyckelvalvet till en prenumeration i en ny klientorganisation måste du uppdatera klient-ID:t manuellt och ta bort gamla åtkomstprinciper och rolltilldelningar. Här är självstudier för de här stegen i PowerShell och Azure CLI. Om du använder PowerShell kan du behöva köra kommandot Clear-AzContext nedan så att du kan se resurser utanför det aktuella valda omfånget. 

### <a name="update-tenant-id-in-a-key-vault"></a>Uppdatera klient-ID i ett nyckelvalv

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
### <a name="update-access-policies-and-role-assignments"></a>Uppdatera åtkomstprinciper och rolltilldelningar

> [!NOTE]
> Om Key Vault använder Azure [RBAC-behörighetsmodellen.](../../role-based-access-control/overview.md) Du måste också ta bort rolltilldelningar för nyckelvalvet. Du kan ta bort rolltilldelningar [med hjälp av Azure-portalen,](../../role-based-access-control/role-assignments-portal.md) [Azure CLI](../../role-based-access-control/role-assignments-cli.md)eller [PowerShell.](../../role-based-access-control/role-assignments-powershell.md) 

Nu när valvet är associerat med rätt klientorganisations-ID och gamla åtkomstprincipposter eller rolltilldelningar har tagits bort, anger du nya åtkomstprincipposter eller rolltilldelningar.

Information om tilldelning av principer finns i:
- [Tilldela en åtkomstprincip med hjälp av portalen](assign-access-policy-portal.md)
- [Tilldela en åtkomstprincip med hjälp av Azure CLI](assign-access-policy-cli.md)
- [Tilldela en åtkomstprincip med PowerShell](assign-access-policy-powershell.md)

Information om hur du lägger till rolltilldelningar finns i:
- [Tilldela Azure-roller med hjälp av Azure Portal](../../role-based-access-control/role-assignments-portal.md)
- [Tilldela Azure-roller med Hjälp av Azure CLI](../../role-based-access-control/role-assignments-cli.md)
- [Tilldela Azure-roller med Hjälp av PowerShell](../../role-based-access-control/role-assignments-powershell.md)


### <a name="update-managed-identities"></a>Uppdatera hanterade identiteter

Om du överför hela prenumerationen och använder en hanterad identitet för Azure-resurser måste du även uppdatera den till den Azure Active Directory klientorganisationen. Mer information om hanterade identiteter finns i [Översikt över hanterade identiteter.](../../active-directory/managed-identities-azure-resources/overview.md)

Om du använder en hanterad identitet måste du också uppdatera identiteten eftersom den gamla identiteten inte längre finns i rätt Azure Active Directory klientorganisation. Se följande dokument för att lösa problemet. 

* [Uppdatera MSI](../../active-directory/managed-identities-azure-resources/known-issues.md#transferring-a-subscription-between-azure-ad-directories)
* [Överföra prenumerationen till ny katalog](../../role-based-access-control/transfer-subscription.md)

## <a name="next-steps"></a>Nästa steg

- Läs mer om [nycklar, hemligheter och certifikat](about-keys-secrets-certificates.md)
- Konceptuell information, inklusive hur du tolkar Key Vault loggar, finns i [Key Vault loggning](logging.md)
- [Utvecklarguide för Key Vault](../general/developers-guide.md)
- [Azure Key Vault säkerhetsfunktioner](security-features.md)
- [Konfigurera Azure Key Vault brandväggar och virtuella nätverk](network-security.md)