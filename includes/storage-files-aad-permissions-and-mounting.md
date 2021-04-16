---
title: ta med fil
description: ta med fil
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 08/26/2020
ms.author: rogara
ms.custom: include file, devx-track-azurecli
ms.openlocfilehash: 200bf290543747cf9abab6113b8013e2eec852a8
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2021
ms.locfileid: "107513245"
---
## <a name="assign-access-permissions-to-an-identity"></a>Tilldela åtkomstbehörigheter till en identitet

För att Azure Files-resurser med identitetsbaserad autentisering måste en identitet (en användare, grupp eller tjänstens huvudnamn) ha nödvändiga behörigheter på resursnivå. Den här processen liknar att ange Behörigheter för Windows-resurs, där du anger vilken typ av åtkomst en viss användare har till en filresurs. Vägledningen i det här avsnittet visar hur du tilldelar läs-, skriv- eller borttagningsbehörigheter för en filresurs till en identitet. 

Vi har introducerat tre inbyggda Azure-roller för att bevilja behörigheter på resursnivå till användare:

- **Storage File Data SMB-resursläsare** ger läsbehörighet i Azure Storage filresurser via SMB.
- **Storage File Data SMB-resursdeltagare** tillåter läs-, skriv- och borttagningsåtkomst i Azure Storage filresurser över SMB.
- **Storage File Data SMB Share Elevated Contributor** tillåter läsning, skrivning, borttagning och ändring av NTFS-behörigheter i Azure Storage över SMB.

> [!IMPORTANT]
> Fullständig administrativ kontroll över en filresurs, inklusive möjligheten att bli ägare till en fil, kräver att lagringskontonyckeln används. Administrativ kontroll stöds inte med Azure AD-autentiseringsuppgifter.

Du kan använda Azure Portal, PowerShell eller Azure CLI för att tilldela de inbyggda rollerna till Azure AD-identiteten för en användare för att bevilja behörigheter på resursnivå. Tänk på att azure-rolltilldelningen på resursnivå kan ta lite tid att gälla. 

> [!NOTE]
> Kom ihåg [att synkronisera dina AD DS-autentiseringsuppgifter till Azure AD](../articles/active-directory/hybrid/how-to-connect-install-roadmap.md) om du planerar att använda din lokala AD DS för autentisering. Synkronisering av lösenordshashar från AD DS till Azure AD är valfritt. Behörighet på resursnivå beviljas till den Azure AD-identitet som synkroniseras från din lokala AD DS.

Den allmänna rekommendationen är att använda behörigheter på resursnivå för åtkomsthantering på hög nivå till en AD-grupp som representerar en grupp användare och identiteter och sedan utnyttja NTFS-behörigheter för detaljerad åtkomstkontroll på katalog-/filnivå. 

### <a name="assign-an-azure-role-to-an-ad-identity"></a>Tilldela en Azure-roll till en AD-identitet

# <a name="portal"></a>[Portal](#tab/azure-portal)
Om du vill tilldela en Azure-roll till en Azure [AD-identitet Azure Portal](https://portal.azure.com)du följande steg:

1. I Azure Portal du till filresursen eller Skapa [en filresurs](../articles/storage/files/storage-how-to-create-file-share.md).
2. Välj **Access Control (IAM).**
3. Välj **Lägg till en rolltilldelning**
4. På **bladet Lägg till** rolltilldelning väljer du lämplig inbyggd roll (Storage File Data SMB-resursläsare, Storage File Data SMB-resursdeltagare) från **listan** Roll. Lämna **Tilldela åtkomst till på** standardinställningen: Azure **AD-användare, grupp eller tjänstens huvudnamn.** Välj Azure AD-målidentiteten efter namn eller e-postadress.
5. Välj **Spara** för att slutföra rolltilldelningsåtgärden.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Följande PowerShell-exempel visar hur du tilldelar en Azure-roll till en Azure AD-identitet, baserat på inloggningsnamn. Mer information om hur du tilldelar Azure-roller med PowerShell finns i Hantera åtkomst med [hjälp av RBAC och Azure PowerShell](../articles/role-based-access-control/role-assignments-powershell.md).

Innan du kör följande exempelskript måste du komma ihåg att ersätta platshållarvärden, inklusive hakparenteser, med dina egna värden.

```powershell
#Get the name of the custom role
$FileShareContributorRole = Get-AzRoleDefinition "<role-name>" #Use one of the built-in roles: Storage File Data SMB Share Reader, Storage File Data SMB Share Contributor, Storage File Data SMB Share Elevated Contributor
#Constrain the scope to the target file share
$scope = "/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/fileServices/default/fileshares/<share-name>"
#Assign the custom role to the target identity with the specified scope.
New-AzRoleAssignment -SignInName <user-principal-name> -RoleDefinitionName $FileShareContributorRole.Name -Scope $scope
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
  
Följande CLI 2.0-kommando visar hur du tilldelar en Azure-roll till en Azure AD-identitet baserat på inloggningsnamnet. Mer information om hur du tilldelar Azure-roller med Azure CLI finns i [Hantera åtkomst med hjälp av RBAC och Azure CLI.](../articles/role-based-access-control/role-assignments-cli.md) 

Innan du kör följande exempelskript måste du komma ihåg att ersätta platshållarvärden, inklusive hakparenteser, med dina egna värden.

```azurecli-interactive
#Assign the built-in role to the target identity: Storage File Data SMB Share Reader, Storage File Data SMB Share Contributor, Storage File Data SMB Share Elevated Contributor
az role assignment create --role "<role-name>" --assignee <user-principal-name> --scope "/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/fileServices/default/fileshares/<share-name>"
```
---

## <a name="configure-ntfs-permissions-over-smb"></a>Konfigurera NTFS-behörigheter via SMB

När du har tilldelar behörigheter på resursnivå med RBAC måste du tilldela rätt NTFS-behörigheter på rot-, katalog- eller filnivå. Tänk på behörigheter på resursnivå som en gatekeeper på hög nivå som avgör om en användare kan komma åt resursen. NTFS-behörigheter fungerar på en mer detaljerad nivå för att avgöra vilka åtgärder användaren kan göra på katalog- eller filnivå.

Azure Files har stöd för en fullständig uppsättning grundläggande och avancerade NTFS-behörigheter. Du kan visa och konfigurera NTFS-behörigheter för kataloger och filer i en Azure-filresurs genom att montera resursen och sedan använda Windows Utforskaren eller köra kommandot Windows [icacls](/windows-server/administration/windows-commands/icacls) eller [Set-ACL.](/powershell/module/microsoft.powershell.security/set-acl) 

Om du vill konfigurera NTFS med superanvändarbehörigheter måste du montera resursen med hjälp av lagringskontonyckeln från den domän-ansluten virtuella datorn. Följ instruktionerna i nästa avsnitt för att montera en Azure-filresurs från kommandotolken och konfigurera NTFS-behörigheter därefter.

Följande uppsättningar behörigheter stöds i rotkatalogen för en filresurs:

- BUILTIN\Administrators:(OI)(CI)(F)
- NT AUTHORITY\SYSTEM:(OI)(CI)(F)
- BUILTIN\Users:(RX)
- BUILTIN\Users:(OI)(CI)(IO)(GR,GE)
- NT AUTHORITY\Autentiserade användare:(OI)(CI)(M)
- NT AUTHORITY\SYSTEM:(F)
- SKAPARES ÄGARE:(OI)(CI)(IO)(F)

### <a name="mount-a-file-share-from-the-command-prompt"></a>Montera en filresurs från kommandotolken

Använd kommandot Windows **net use** för att montera Azure-filresursen. Kom ihåg att ersätta platshållarvärdena i följande exampleÂ med dina egna värden. Mer information om hur du monterar filresurser finns [i Använda en Azure-filresurs med Windows](../articles/storage/files/storage-how-to-use-files-windows.md). 

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

Om du får problem med att ansluta till Azure Files kan du gå till felsökningsverktyget som vi publicerade [för att Azure Files monteringsfel på Windows](https://azure.microsoft.com/blog/new-troubleshooting-diagnostics-for-azure-files-mounting-errors-on-windows/). Vi ger också [vägledning för](../articles/storage/files/storage-files-faq.md#on-premises-access) att komma runt scenarier när port 445 blockeras. 


### <a name="configure-ntfs-permissions-with-windows-file-explorer"></a>Konfigurera NTFS-behörigheter med Windows Utforskaren

Använd Windows Utforskaren att bevilja fullständig behörighet till alla kataloger och filer under filresursen, inklusive rotkatalogen.

1. Öppna Windows Utforskaren och högerklicka på filen/katalogen och välj **Egenskaper.**
2. Välj **fliken** Säkerhet.
3. Välj **Redigera..** för att ändra behörigheter.
4. Du kan ändra behörigheterna för befintliga användare eller välja Lägg **till...** för att bevilja behörigheter till nya användare.
5. I promptfönstret för att lägga till nya användare anger du det målanvändarnamn som du  vill ge behörighet till i rutan Ange de objektnamn som ska **väljas** och väljer Kontrollera namn för att hitta målanvändarens fullständiga UPN-namn.
7.    Välj **OK**.
8.    På fliken **Säkerhet** väljer du alla behörigheter som du vill bevilja den nya användaren.
9.    Välj **Använd**.

### <a name="configure-ntfs-permissions-with-icacls"></a>Konfigurera NTFS-behörigheter med icacls

Använd följande Windows-kommando för att bevilja fullständiga behörigheter till alla kataloger och filer under filresursen, inklusive rotkatalogen. Kom ihåg att ersätta platshållarvärdena i exemplet med dina egna värden.

```
icacls <mounted-drive-letter>: /grant <user-email>:(f)
```

Mer information om hur du använder icacls för att ange NTFS-behörigheter och om de olika typerna av behörigheter som stöds finns i kommandoradsreferensen för [icacls](/windows-server/administration/windows-commands/icacls).

## <a name="mount-a-file-share-from-a-domain-joined-vm"></a>Montera en filresurs från en domän-ansluten virtuell dator

Följande process verifierar att filresursen och åtkomstbehörigheterna har ställts in korrekt och att du kan komma åt en Azure-filresurs från en domän-ansluten virtuell dator. Tänk på att azure-rolltilldelningen på resursnivå kan ta lite tid att gälla. 

Logga in på den virtuella datorn med hjälp av den Azure AD-identitet som du har beviljat behörighet till, som du ser i följande bild. Om du har aktiverat lokal AD DS-autentisering för Azure Files använder du dina AD DS-autentiseringsuppgifter. Logga Azure AD DS med Azure AD-autentiseringsuppgifter för att Azure AD DS autentiseringsuppgifter.

![Skärmbild som visar inloggningsskärmen för Azure AD för användarautentisering](media/storage-files-aad-permissions-and-mounting/azure-active-directory-authentication-dialog.png)

Använd följande kommando för att montera Azure-filresursen. Kom ihåg att ersätta platshållarvärdena med dina egna värden. Eftersom du har autentiserats behöver du inte ange lagringskontonyckeln, de lokala AD DS-autentiseringsuppgifterna eller autentiseringsuppgifterna för Azure AD DS autentiseringsuppgifter. Enkel inloggning stöds för autentisering med antingen lokal AD DS eller Azure AD DS. Om du får problem med montering med AD DS-autentiseringsuppgifter kan du gå [till Felsöka Azure Files i Windows](../articles/storage/files/storage-troubleshoot-windows-file-connection-problems.md) för vägledning.

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
