---
title: Bevilja behörighet till program för att få åtkomst till ett Azure Key Vault – Azure Key Vault | Microsoft Docs
description: Lär dig hur du registrerar ett huvud namn för tjänsten manuellt och ger åtkomst till Azure Key Vault med hjälp av en princip för åtkomst kontroll som kan vara nödvändig i vissa fall.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 09/27/2019
ms.author: mbaldwin
ms.openlocfilehash: d0607b1adb76fd32ce9f4dc9ebede92c7aa96862
ms.sourcegitcommit: 02ca0f340a44b7e18acca1351c8e81f3cca4a370
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/19/2020
ms.locfileid: "88588831"
---
# <a name="provide-key-vault-authentication-with-an-access-control-policy"></a>Tillhandahålla Key Vault autentisering med en princip för åtkomst kontroll

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Det enklaste sättet att autentisera ett molnbaserad program till Key Vault är med en hanterad identitet. Mer information finns i [använda en app service hanterad identitet för att få åtkomst till Azure Key Vault](managed-identity.md) .  Om du skapar ett lokal program, gör en lokal utveckling eller inte kan använda en hanterad identitet, kan du istället registrera ett huvud namn för tjänsten manuellt och ge åtkomst till ditt nyckel valv med hjälp av en princip för åtkomst kontroll.  

Key Vault har stöd för upp till 1024 åtkomst princip poster, med varje post som beviljar en distinkt uppsättning behörigheter till en "huvud konto": Detta är till exempel hur konsolens app i [Azure Key Vault klient bibliotek för .net snabb start](../secrets/quick-create-net.md) har åtkomst till nyckel valvet.

Fullständig information om Key Vault åtkomst kontroll finns i [Azure Key Vault säkerhet: identitets-och åtkomst hantering](overview-security.md#identity-and-access-management). Fullständig information om åtkomst kontroll finns i: 

- [Nycklar](../keys/index.yml)
- [Åtkomst kontroll för hemligheter](../secrets/index.yml)
- [Åtkomst kontroll för certifikat](../certificates/index.yml)

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>Förutsättningar

- Ett nyckel valv. Du kan använda ett befintligt nyckel valv eller skapa ett nytt genom att följa stegen i någon av följande snabb starter:
   - [Skapa ett nyckel valv med Azure CLI](../secrets/quick-create-cli.md)
   - [Skapa ett nyckel valv med Azure PowerShell](../secrets/quick-create-powershell.md)
   - [Skapa ett nyckel valv med Azure Portal](../secrets/quick-create-portal.md).
- [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest) eller [Azure PowerShell](/powershell/azure/). Du kan också använda [Azure Portal](https://portal.azure.com).

## <a name="grant-access-to-your-key-vault"></a>Bevilja åtkomst till ditt nyckel valv

Varje post för åtkomst principer för nyckel valv ger en distinkt uppsättning behörigheter till ett huvud konto:

- **Ett program** Om programmet är molnbaserad bör du i stället [använda en hanterad identitet för att få åtkomst till Azure Key Vault](managed-identity.md), om möjligt
- **En Azure AD-grupp** Även om Key Vault bara stöder 1024 åtkomst princip poster, kan du lägga till flera program och användare till en enda Azure AD-grupp och sedan lägga till gruppen som en enda post i din princip för åtkomst kontroll.
- **En användare** Att ge användare direkt åtkomst till ett nyckel valv **rekommenderas inte.** Vi rekommenderar att användare läggs till i en Azure AD-grupp, vilket i sin tur har gett åtkomst till nyckel valvet. Se [Azure Key Vault säkerhet: identitets-och åtkomst hantering](overview-security.md#identity-and-access-management).


### <a name="get-the-objectid"></a>Hämta objectID

Om du vill ge ett program, Azure AD-grupp eller användar åtkomst till ditt nyckel valv måste du först hämta dess objectId.

#### <a name="applications"></a>Program

ObjectId för ett program motsvarar dess associerade tjänst huvud namn. Fullständig information om tjänstens huvud namn. Se [program-och tjänst huvud objekt i Azure Active Directory](../../active-directory/develop/app-objects-and-service-principals.md). 

Det finns två sätt att hämta ett objectId för ett program.  Det första är att registrera ditt program med Azure Active Directory. Det gör du genom att följa stegen i snabb starten [Registrera ett program med Microsoft Identity Platform](../../active-directory/develop/quickstart-register-app.md). När registreringen är klar visas objectID som "program-ID" (klient).

Det andra är att skapa ett huvud namn för tjänsten i ett terminalfönster. Med Azure CLI använder du kommandot [AZ AD SP Create-for-RBAC](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac) och anger ett unikt huvud namn för tjänsten för flaggan-n i formatet "http:// &lt; My-Unique-service-huvud namn &gt; ".

```azurecli-interactive
az ad sp create-for-rbac -n "http://<my-unique-service-principal-name"
```

ObjectId visas i utdata som `clientID` .

Med Azure PowerShell använder du cmdleten [New-AzADServicePrincipal](/powershell/module/Az.Resources/New-AzADServicePrincipal?view=azps-2.7.0) .


```azurepowershell-interactive
New-AzADServicePrincipal -DisplayName <my-unique-service-principal-name>
```

ObjectId visas i utdata som `Id` (inte `ApplicationId` ).

#### <a name="azure-ad-groups"></a>Azure AD-grupper

Du kan lägga till flera program och användare i en Azure AD-grupp och ge gruppen åtkomst till ditt nyckel valv.  Mer information finns i avsnittet [skapa och lägga till medlemmar i en Azure AD-grupp](#creating-and-adding-members-to-an-azure-ad-group) nedan.

Använd kommandot [AZ AD Group List](/cli/azure/ad/group?view=azure-cli-latest#az-ad-group-list) för att hitta ObjectID för en Azure AD-grupp med Azure CLI. På grund av det stora antalet grupper som kan finnas i din organisation, bör du även ange en Sök sträng till `--display-name` parametern.

```azurecli-interactive
az ad group list --display-name <search-string>
```
ObjectId kommer att returneras i JSON:

```json
    "objectId": "48b21bfb-74d6-48d2-868f-ff9eeaf38a64",
    "objectType": "Group",
    "odata.type": "Microsoft.DirectoryServices.Group",
```

Använd cmdleten [Get-AzADGroup](/powershell/module/az.resources/get-azadgroup?view=azps-2.7.0) för att hitta ObjectID för en Azure AD-grupp med Azure PowerShell. På grund av det stora antalet grupper som kan finnas i din organisation, vill du förmodligen även ange en Sök sträng till `-SearchString` parametern.

```azurepowershell-interactive
Get-AzADGroup -SearchString <search-string>
```

I utdata visas objectId som `Id` :

```console
...
Id                    : 1cef38c4-388c-45a9-b5ae-3d88375e166a
...
```

#### <a name="users"></a>Användare

Du kan också lägga till en enskild användare i ett nyckel valvs princip för åtkomst kontroll. **Vi rekommenderar inte detta.** I stället rekommenderar vi att du lägger till användare i en Azure AD-grupp och lägger till gruppen på principerna.

Om du ändå vill hitta en användare med Azure CLI använder du kommandot [AZ AD User show](/cli/azure/ad/user?view=azure-cli-latest#az-ad-user-show) och skickar användarens e-postadress till `--id` parametern.


```azurecli-interactive
az ad user show --id <email-address-of-user>
```

Användarens objectId kommer att returneras i utdata:

```console
  ...
  "objectId": "f76a2a6f-3b6d-4735-9abd-14dccbf70fd9",
  "objectType": "User",
  ...
```

Om du vill hitta en användare med Azure PowerShell använder du cmdleten [Get-AzADUser](/powershell/module/az.resources/get-azaduser?view=azps-2.7.0) och skickar användarens e-postadress till `-UserPrincipalName` parametern.

```azurepowershell-interactive
 Get-AzAdUser -UserPrincipalName <email-address-of-user>
```

Användarens objectId kommer att returneras i utdata som `Id` .

```console
...
Id                : f76a2a6f-3b6d-4735-9abd-14dccbf70fd9
Type              :
```

### <a name="give-the-principal-access-to-your-key-vault"></a>Ge huvud kontot åtkomst till ditt nyckel valv

Nu när du har ett objectID för ditt huvud objekt kan du skapa en åtkomst princip för nyckel valvet som ger det get-, list-, set-och Delete-behörigheter för både nycklar och hemligheter, plus eventuella ytterligare behörigheter som du önskar.

Med Azure CLI görs detta genom att skicka objectId till [AZ-kommandot Set-policy](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-set-policy) .

```azurecli-interactive
az keyvault set-policy -n <your-unique-keyvault-name> --spn <ApplicationID-of-your-service-principal> --secret-permissions get list set delete --key-permissions create decrypt delete encrypt get list unwrapKey wrapKey
```

Med Azure PowerShell görs detta genom att skicka objectId till cmdleten [set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy?view=azps-2.7.0) . 

```azurepowershell-interactive
Set-AzKeyVaultAccessPolicy -VaultName <your-key-vault-name> -PermissionsToKeys create,decrypt,delete,encrypt,get,list,unwrapKey,wrapKey -PermissionsToSecrets get,list,set,delete -ObjectId <Id>

```

## <a name="creating-and-adding-members-to-an-azure-ad-group"></a>Skapa och lägga till medlemmar i en Azure AD-grupp

Du kan skapa en Azure AD-grupp, lägga till program och användare i gruppen och ge gruppen åtkomst till ditt nyckel valv.  På så sätt kan du lägga till ett antal program i ett nyckel valv som en post för enskild åtkomst princip och eliminera behovet av att ge användarna direkt åtkomst till ditt nyckel valv (som vi avråder). Mer information finns i [Hantera app-och resurs åtkomst med hjälp av Azure Active Directory grupper](../../active-directory/fundamentals/active-directory-manage-groups.md).

### <a name="additional-prerequisites"></a>Ytterligare krav

Utöver [kraven ovan](#prerequisites)måste du ha behörighet att skapa/redigera grupper i Azure Active Directory-klienten. Om du inte har behörighet kan du behöva kontakta Azure Active Directory administratören.

Om du tänker använda PowerShell behöver du även [Azure AD PowerShell-modulen](https://www.powershellgallery.com/packages/AzureAD/2.0.2.50)

### <a name="create-an-azure-active-directory-group"></a>Skapa en Azure Active Directory grupp

Skapa en ny Azure Active Directory grupp med hjälp av kommandot Azure CLI [AZ AD Group create](/cli/azure/ad/group?view=azure-cli-latest#az-ad-group-create) Azure PowerShell eller cmdleten [New-AzureADGroup](/powershell/module/azuread/new-azureadgroup?view=azureadps-2.0) .


```azurecli-interactive
az ad group create --display-name <your-group-display-name> --mail-nickname <your-group-mail-nickname>
```

```powershell
New-AzADGroup -DisplayName <your-group-display-name> -MailNickName <your-group-mail-nickname>
```

I båda fallen noterar du den nyskapade gruppen för användar-och skriv åtgärder så att du behöver det enligt stegen nedan.

### <a name="find-the-objectids-of-your-applications-and-users"></a>Hitta objectIds för dina program och användare

Du kan hitta objectIds för dina program med hjälp av Azure CLI med kommandot [AZ AD SP List](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-list) med `--show-mine` parametern.

```azurecli-interactive
az ad sp list --show-mine
```

Hitta objectIds för dina program med hjälp av Azure PowerShell med cmdleten [Get-AzADServicePrincipal](/powershell/module/az.resources/get-azadserviceprincipal?view=azps-2.7.0) och skicka en Sök sträng till `-SearchString` parametern.

```azurepowershell-interactive
Get-AzADServicePrincipal -SearchString <search-string>
```

Följ stegen i avsnittet [användare](#users) ovan för att hitta objectIds för dina användare.

### <a name="add-your-applications-and-users-to-the-group"></a>Lägg till dina program och användare i gruppen

Lägg nu till objectIds i din nya Azure AD-grupp.

Med Azure CLI använder du AD- [AZ AD-gruppmedlem Lägg till](/cli/azure/ad/group/member?view=azure-cli-latest#az-ad-group-member-add), och skickar ObjectID till `--member-id` parametern.


```azurecli-interactive
az ad group member add -g <groupId> --member-id <objectId>
```

Med Azure PowerShell använder du cmdleten [Add-AzADGroupMember](/powershell/module/az.resources/add-azadgroupmember?view=azps-2.7.0) och skickar ObjectID till `-MemberObjectId` parametern.

```azurepowershell-interactive
Add-AzADGroupMember -TargetGroupObjectId <groupId> -MemberObjectId <objectId> 
```

### <a name="give-the-ad-group-access-to-your-key-vault"></a>Ge AD-gruppen åtkomst till ditt nyckel valv

Till sist ger du AD-gruppen behörighet till ditt nyckel valv med hjälp av Azure CLI [-AZ för att ange princip](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-set-policy) kommando eller cmdleten Azure PowerShell [set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy?view=azps-2.7.0) . Exempel finns i avsnittet [ge appen, Azure AD-gruppen eller användar åtkomst till ditt nyckel valv](#give-the-principal-access-to-your-key-vault) ovan.

Programmet behöver också minst en roll för identitets-och åtkomst hantering (IAM) som har tilldelats nyckel valvet. Annars kommer den inte att kunna logga in och kommer att Miss lyckas med otillräcklig behörighet för att komma åt prenumerationen.

> [!WARNING]
> Azure AD-grupper med hanterade identiteter kan kräva upp till 8hr för att uppdatera token och bli gällande.

## <a name="next-steps"></a>Nästa steg

- [Azure Key Vault säkerhet: identitets-och åtkomst hantering](overview-security.md#identity-and-access-management)
- [Tillhandahålla Key Vault autentisering med en App Service hanterad identitet](managed-identity.md)
- [Skydda ditt nyckel valv](secure-your-key-vault.md)).
- [Guide för Azure Key Vault utvecklare](developers-guide.md)
- Granska [Azure Key Vault bästa praxis](best-practices.md)
