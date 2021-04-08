---
title: ta med fil
description: ta med fil
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 08/26/2020
ms.author: rogara
ms.custom: include file
ms.openlocfilehash: 4773446ec0007ffbed99bc01939d1f92f5823d99
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "95559660"
---
## <a name="assign-access-permissions-to-an-identity"></a>Tilldela åtkomst behörigheter till en identitet

För att få åtkomst till Azure Files resurser med identitets baserad autentisering måste en identitet (användare, grupp eller tjänstens huvud namn) ha nödvändig behörighet på delnings nivå. Den här processen liknar att ange behörigheter för Windows-resurs, där du anger vilken typ av åtkomst som en viss användare har till en fil resurs. Rikt linjerna i det här avsnittet visar hur du tilldelar behörigheterna läsa, skriva eller ta bort för en fil resurs till en identitet. 

Vi har introducerat tre inbyggda Azure-roller för att bevilja behörigheter på resurs nivå till användare:

- **Lagrings fil data SMB Share Reader** tillåter Läs åtkomst i Azure Storage fil resurser över SMB.
- **Storage File data SMB Share Contributor** tillåter Läs-, skriv-och borttagnings åtkomst i Azure Storage fil resurser över SMB.
- **Lagrings fil data SMB dela utökad deltagare** tillåter Läs-, Skriv-, borttagnings-och ändrings-NTFS-behörigheter i Azure Storage fil resurser över SMB.

> [!IMPORTANT]
> Fullständig administrativ kontroll av en fil resurs, inklusive möjligheten att bli ägare till en fil, kräver att du använder lagrings konto nyckeln. Administrativ kontroll stöds inte med autentiseringsuppgifter för Azure AD.

Du kan använda Azure Portal, PowerShell eller Azure CLI för att tilldela de inbyggda rollerna till Azure AD-identiteten för en användare för att bevilja behörigheter på resurs nivå. Tänk på att tilldelningen av Azure-rollen på resurs nivå kan ta lite tid. 

> [!NOTE]
> Kom ihåg att [Synkronisera dina AD DS-autentiseringsuppgifter till Azure AD](../articles/active-directory/hybrid/how-to-connect-install-roadmap.md) om du planerar att använda din lokala AD DS för autentisering. Password hash Sync från AD DS till Azure AD är valfritt. Behörigheten på resurs nivå beviljas den Azure AD-identitet som synkroniseras från din lokala AD DS.

Den allmänna rekommendationen är att använda behörighet på resurs nivå för åtkomst hantering på hög nivå till en AD-grupp som representerar en grupp användare och identiteter, och sedan utnyttja NTFS-behörigheter för detaljerad åtkomst kontroll på katalog-/filnivå. 

### <a name="assign-an-azure-role-to-an-ad-identity"></a>Tilldela en Azure-roll till en AD-identitet

# <a name="portal"></a>[Portal](#tab/azure-portal)
Följ dessa steg om du vill tilldela en Azure-roll till en Azure AD-identitet med hjälp av [Azure Portal](https://portal.azure.com):

1. I Azure Portal går du till fil resursen eller [skapar en fil resurs](../articles/storage/files/storage-how-to-create-file-share.md).
2. Välj **Access Control (IAM)**.
3. Välj **Lägg till en roll tilldelning**
4. På bladet **Lägg till roll tilldelning** väljer du lämplig inbyggd roll (lagrings fil data SMB Share Reader, Storage File data SMB Share Contributor) från **roll** listan. Lämna inställningen **tilldela till gång till** standardinställningen: **Azure AD-användare, grupp eller tjänstens huvud namn**. Välj målets Azure AD-identitet efter namn eller e-postadress.
5. Klicka på **Spara** för att slutföra roll tilldelnings åtgärden.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Följande PowerShell-exempel visar hur du tilldelar en Azure-roll till en Azure AD-identitet baserat på inloggnings namn. Mer information om hur du tilldelar Azure-roller med PowerShell finns i [Hantera åtkomst med RBAC och Azure PowerShell](../articles/role-based-access-control/role-assignments-powershell.md).

Innan du kör följande exempel skript måste du komma ihåg att ersätta plats hållarnas värden, inklusive hakparenteser, med dina egna värden.

```powershell
#Get the name of the custom role
$FileShareContributorRole = Get-AzRoleDefinition "<role-name>" #Use one of the built-in roles: Storage File Data SMB Share Reader, Storage File Data SMB Share Contributor, Storage File Data SMB Share Elevated Contributor
#Constrain the scope to the target file share
$scope = "/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/fileServices/default/fileshares/<share-name>"
#Assign the custom role to the target identity with the specified scope.
New-AzRoleAssignment -SignInName <user-principal-name> -RoleDefinitionName $FileShareContributorRole.Name -Scope $scope
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
  
Följande CLI 2,0-kommando visar hur du tilldelar en Azure-roll till en Azure AD-identitet baserat på inloggnings namn. Mer information om hur du tilldelar Azure-roller med Azure CLI finns i [Hantera åtkomst med RBAC och Azure CLI](../articles/role-based-access-control/role-assignments-cli.md). 

Innan du kör följande exempel skript måste du komma ihåg att ersätta plats hållarnas värden, inklusive hakparenteser, med dina egna värden.

```azurecli-interactive
#Assign the built-in role to the target identity: Storage File Data SMB Share Reader, Storage File Data SMB Share Contributor, Storage File Data SMB Share Elevated Contributor
az role assignment create --role "<role-name>" --assignee <user-principal-name> --scope "/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/fileServices/default/fileshares/<share-name>"
```
---

## <a name="configure-ntfs-permissions-over-smb"></a>Konfigurera NTFS-behörigheter över SMB

När du har tilldelat behörigheter på resurs nivå med RBAC måste du tilldela rätt NTFS-behörighet på rot-, katalog-eller filnivå. Tänk på behörigheter på resurs nivå som den övergripande gatekeepern som avgör om en användare har åtkomst till resursen. NTFS-behörigheter fungerar på en mer detaljerad nivå för att avgöra vilka åtgärder användaren kan göra på katalog-eller filnivå.

Azure Files stöder en fullständig uppsättning NTFS Basic-och Advanced-behörigheter. Du kan visa och konfigurera NTFS-behörigheter för kataloger och filer i en Azure-filresurs genom att montera resursen och sedan använda Utforskaren i Windows eller köra kommandot Windows [icacls](/windows-server/administration/windows-commands/icacls) eller [set-ACL](/powershell/module/microsoft.powershell.security/set-acl) . 

Om du vill konfigurera NTFS med behörigheter för superanvändare måste du montera resursen med hjälp av lagrings konto nyckeln från din domänanslutna VM. Följ anvisningarna i nästa avsnitt för att montera en Azure-filresurs från kommando tolken och konfigurera NTFS-behörigheter enligt detta.

Följande behörighets uppsättningar stöds i rot katalogen för en fil resurs:

- BUILTIN\Administrators: (OI) (CI) (F)
- NT INSTANS\SYSTEM: (OI) (CI) (F)
- BUILTIN\Users: (RX)
- BUILTIN\Users: (OI) (CI) (IO) (GR, GE)
- NT Instans\autentiserade-användare: (OI) (CI) (M)
- NT INSTANS\SYSTEM: (F)
- SKAPARE ÄGARE: (OI) (CI) (IO) (F)

### <a name="mount-a-file-share-from-the-command-prompt"></a>Montera en fil resurs från kommando tolken

Använd kommandot Windows **net use** för att montera Azure-filresursen. Kom ihåg att ersätta plats hållarnas värden i följande exempel med dina egna värden. Mer information om hur du monterar fil resurser finns i [använda en Azure-filresurs med Windows](../articles/storage/files/storage-how-to-use-files-windows.md). 

```
$connectTestResult = Test-NetConnection -ComputerName <storage-account-name>.file.core.windows.net -Port 445
if ($connectTestResult.TcpTestSucceeded)
{
 net use <desired-drive letter>: \\<storage-account-name>.file.core.windows.net\<fileshare-name>
} 
else 
{
 Write-Error -Message "Unable to reach the Azure storage account via port 445. Check to make sure your organization or ISP is not blocking port 445, or use Azure P2S VPN, Azure S2S VPN, or Express Route to tunnel SMB traffic over a different port."
}

```

Om du får problem med att ansluta till Azure Files kan du läsa [fel söknings verktyget som vi publicerade för Azure Files monterings fel i Windows](https://azure.microsoft.com/blog/new-troubleshooting-diagnostics-for-azure-files-mounting-errors-on-windows/). Vi ger också [vägledning](../articles/storage/files/storage-files-faq.md#on-premises-access) för att lösa scenarier när port 445 är blockerad. 


### <a name="configure-ntfs-permissions-with-windows-file-explorer"></a>Konfigurera NTFS-behörigheter med Windows Utforskaren

Använd Utforskaren i Windows för att ge fullständig behörighet till alla kataloger och filer under fil resursen, inklusive rot katalogen.

1. Öppna Utforskaren i Windows och högerklicka på filen/katalogen och välj **Egenskaper**.
2. Välj fliken **säkerhet** .
3. Välj **Redigera...** ändra behörigheter.
4. Du kan ändra behörigheter för befintliga användare eller välja **Lägg till...** om du vill ge nya användare behörigheter.
5. I fönstret prompt för att lägga till nya användare anger du det mål användar namn som du vill ge behörighet i rutan **Ange de objekt namn som ska väljas** och väljer **kontrol lera namn** för att hitta det fullständiga UPN-namnet för mål användaren.
7.    Välj **OK**.
8.    På fliken **säkerhet** väljer du alla behörigheter som du vill ge den nya användaren.
9.    Välj **Använd**.

### <a name="configure-ntfs-permissions-with-icacls"></a>Konfigurera NTFS-behörigheter med icacls

Använd följande Windows-kommando för att ge fullständig behörighet till alla kataloger och filer under fil resursen, inklusive rot katalogen. Kom ihåg att ersätta plats hållarnas värden i exemplet med dina egna värden.

```
icacls <mounted-drive-letter>: /grant <user-email>:(f)
```

Mer information om hur du använder icacls för att ange NTFS-behörigheter och de olika typerna av behörigheter som stöds finns i [kommando rads referens för icacls](/windows-server/administration/windows-commands/icacls).

## <a name="mount-a-file-share-from-a-domain-joined-vm"></a>Montera en fil resurs från en domänansluten virtuell dator

Följande process kontrollerar att fil resursen och åtkomst behörigheterna har ställts in korrekt och att du kan komma åt en Azure-filresurs från en domänansluten virtuell dator. Tänk på att tilldelningen av Azure-rollen på resurs nivå kan ta lite tid. 

Logga in på den virtuella datorn med hjälp av den Azure AD-identitet som du har beviljat behörigheter för, som visas i följande bild. Om du har aktiverat AD DS-autentisering på plats för Azure Files använder du dina AD DS-autentiseringsuppgifter. Logga in med Azure AD-autentiseringsuppgifter för Azure AD DS-autentisering.

![Skärm bild som visar inloggnings skärmen för Azure AD för användarautentisering](media/storage-files-aad-permissions-and-mounting/azure-active-directory-authentication-dialog.png)

Använd följande kommando för att montera Azure-filresursen. Kom ihåg att ersätta plats hållarnas värden med dina egna värden. Eftersom du har autentiserats behöver du inte ange lagrings konto nyckeln, lokala AD DS-autentiseringsuppgifter eller autentiseringsuppgifter för Azure AD DS. Enkel inloggning stöds för autentisering med antingen lokal AD DS eller Azure AD DS. Om du stöter på problem med att montera med AD DS-autentiseringsuppgifter läser du [felsöka Azure Files problem i Windows](../articles/storage/files/storage-troubleshoot-windows-file-connection-problems.md) för vägledning.

```
$connectTestResult = Test-NetConnection -ComputerName <storage-account-name>.file.core.windows.net -Port 445
if ($connectTestResult.TcpTestSucceeded)
{
 net use <desired-drive letter>: \\<storage-account-name>.file.core.windows.net\<fileshare-name>
} 
else 
{
 Write-Error -Message "Unable to reach the Azure storage account via port 445. Check to make sure your organization or ISP is not blocking port 445, or use Azure P2S VPN, Azure S2S VPN, or Express Route to tunnel SMB traffic over a different port."
}
```