---
title: Kontrol lera åtkomst till Azure-filresurser – lokal AD DS-autentisering
description: Lär dig hur du tilldelar behörigheter till en Active Directory Domain Services identitet som representerar ditt lagrings konto. På så sätt kan du kontrol lera åtkomst med identitets-baserad autentisering.
author: roygara
ms.service: storage
ms.subservice: files
ms.topic: how-to
ms.date: 06/22/2020
ms.author: rogarana
ms.openlocfilehash: af88f0b3403fb80acbb7dacebe293ac583e35799
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "91716035"
---
# <a name="part-two-assign-share-level-permissions-to-an-identity"></a>Del två: tilldela behörigheter på resurs nivå till en identitet

Innan du börjar den här artikeln kontrollerar du att du har slutfört den föregående artikeln och [aktiverar AD DS-autentisering för ditt konto](storage-files-identity-ad-ds-enable.md).

När du har aktiverat autentisering med Active Directory Domain Services (AD DS) på ditt lagrings konto måste du konfigurera behörigheter på resurs nivå för att få åtkomst till dina fil resurser. Den identitet som du vill få åtkomst till Azure-filresurser med måste vara en hybrid identitet som finns i både AD DS och Azure AD. Anta till exempel att du har en användare i din AD DS som är user1@onprem.contoso.com och du har synkroniserat till Azure AD som att user1@contoso.com använda Azure AD Connect Sync. Om du vill tillåta att den här användaren får åtkomst till Azure Files måste du tilldela behörigheter på resurs nivå till user1@contoso.com . Samma koncept gäller för grupper eller tjänstens huvud namn. Därför måste du synkronisera användare och grupper från AD DS till Azure AD med hjälp av Azure AD Connect Sync. 

Behörigheter på resurs nivå måste tilldelas till Azure AD-identiteten som representerar samma användare eller grupp i AD DS för att ge stöd för AD DS-autentisering till Azure-filresursen. Autentisering och auktorisering mot identiteter som bara finns i Azure AD, till exempel Azure Managed Identities (MSIs), stöds inte med AD DS-autentisering. Den här artikeln visar hur du tilldelar behörigheter på resurs nivå för en fil resurs till en identitet.


## <a name="share-level-permissions"></a>Behörigheter på resurs nivå

I allmänhet rekommenderar vi att du använder behörigheter på resurs nivå för åtkomst hantering på hög nivå till en Azure AD-grupp som representerar en grupp användare och identiteter, och sedan använder Windows ACL: er för detaljerad åtkomst kontroll till katalog-/filnivå. 

Det finns tre inbyggda Azure-roller för att bevilja behörigheter på resurs nivå till användare:

- **Lagrings fil data SMB Share Reader** tillåter Läs åtkomst i Azure Storage fil resurser över SMB.
- **Storage File data SMB Share Contributor** tillåter Läs-, skriv-och borttagnings åtkomst i Azure Storage fil resurser över SMB.
- **Lagrings fil data SMB dela utökad deltagare** tillåter Läs-, Skriv-, borttagnings-och ändring av Windows ACL: er i Azure Storage fil resurser över SMB.

> [!IMPORTANT]
> Fullständig administrativ kontroll av en fil resurs, inklusive möjligheten att bli ägare till en fil, kräver att du använder lagrings konto nyckeln. Administrativ kontroll stöds inte med autentiseringsuppgifter för Azure AD.

Du kan använda Azure Portal, Azure PowerShell eller Azure CLI för att tilldela de inbyggda rollerna till Azure AD-identiteten för en användare för att bevilja behörigheter på resurs nivå.

## <a name="assign-an-azure-role"></a>Tilldela en Azure-roll

### <a name="azure-portal"></a>Azure Portal

Följ dessa steg om du vill tilldela en Azure-roll till en Azure AD-identitet med hjälp av [Azure Portal](https://portal.azure.com):

1. I Azure Portal går du till fil resursen eller [skapar en fil resurs](storage-how-to-create-file-share.md).
1. Välj **Access Control (IAM)**.
1. Välj **Lägg till en roll tilldelning**
1. På bladet **Lägg till roll tilldelning** väljer du lämplig inbyggd roll (lagrings fil data SMB Share Reader, Storage File data SMB Share Contributor) från **roll** listan. Lämna inställningen **tilldela till gång till** standardinställningen: **Azure AD-användare, grupp eller tjänstens huvud namn**. Välj målets Azure AD-identitet efter namn eller e-postadress. **Den valda Azure AD-identiteten måste vara en hybrid identitet och får inte vara en moln identitet.** Det innebär att samma identitet också representeras i AD DS.
1. Klicka på **Spara** för att slutföra roll tilldelnings åtgärden.

### <a name="powershell"></a>PowerShell

Följande PowerShell-exempel visar hur du tilldelar en Azure-roll till en Azure AD-identitet baserat på inloggnings namn. Mer information om hur du tilldelar Azure-roller med PowerShell finns i [lägga till eller ta bort Azure Role-tilldelningar med hjälp av Azure PowerShell-modulen](../../role-based-access-control/role-assignments-powershell.md).

Innan du kör följande exempel skript ska du ersätta plats hållarnas värden, inklusive hakparenteser, med dina värden.

```powershell
#Get the name of the custom role
$FileShareContributorRole = Get-AzRoleDefinition "<role-name>" #Use one of the built-in roles: Storage File Data SMB Share Reader, Storage File Data SMB Share Contributor, Storage File Data SMB Share Elevated Contributor
#Constrain the scope to the target file share
$scope = "/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/fileServices/default/fileshares/<share-name>"
#Assign the custom role to the target identity with the specified scope.
New-AzRoleAssignment -SignInName <user-principal-name> -RoleDefinitionName $FileShareContributorRole.Name -Scope $scope
```

### <a name="cli"></a>CLI
  
Följande CLI 2,0-kommando tilldelar en Azure-roll till en Azure AD-identitet baserat på inloggnings namn. Mer information om hur du tilldelar Azure-roller med Azure CLI finns i [lägga till eller ta bort roll tilldelningar i Azure med hjälp av Azure CLI](../../role-based-access-control/role-assignments-cli.md). 

Innan du kör följande exempel skript måste du komma ihåg att ersätta plats hållarnas värden, inklusive hakparenteser, med dina egna värden.

```azurecli-interactive
#Assign the built-in role to the target identity: Storage File Data SMB Share Reader, Storage File Data SMB Share Contributor, Storage File Data SMB Share Elevated Contributor
az role assignment create --role "<role-name>" --assignee <user-principal-name> --scope "/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/fileServices/default/fileshares/<share-name>"
```

## <a name="next-steps"></a>Nästa steg

Nu när du har tilldelat behörigheter på resurs nivå måste du konfigurera katalog-och fil nivå behörigheter. Fortsätt till nästa artikel.

[Del tre: Konfigurera behörigheter för kataloger och filnivå över SMB](storage-files-identity-ad-ds-configure-permissions.md)
