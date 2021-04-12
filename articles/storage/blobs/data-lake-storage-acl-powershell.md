---
title: 'Använd PowerShell för att hantera ACL: er i Azure Data Lake Storage Gen2'
description: Använd PowerShell-cmdletar för att hantera åtkomst kontrol listor (ACL) i lagrings konton som har hierarkiskt namn område (HNS) aktiverat.
services: storage
author: normesta
ms.service: storage
ms.subservice: data-lake-storage-gen2
ms.topic: how-to
ms.date: 02/17/2021
ms.author: normesta
ms.reviewer: prishet
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: dd522355d30564d84fec15bdc57c7397c1e6cfe4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "104702549"
---
# <a name="use-powershell-to-manage-acls-in-azure-data-lake-storage-gen2"></a>Använd PowerShell för att hantera ACL: er i Azure Data Lake Storage Gen2

Den här artikeln visar hur du använder PowerShell för att hämta, ange och uppdatera åtkomst kontrol listorna för kataloger och filer. 

ACL-arv är redan tillgängligt för nya underordnade objekt som skapas under en överordnad katalog. Men du kan också lägga till, uppdatera och ta bort ACL rekursivt på befintliga underordnade objekt i en överordnad katalog utan att behöva göra dessa ändringar individuellt för varje underordnat objekt. 

[Referens](/powershell/module/Az.Storage/)  |  [Rekursiva ACL-exempel](https://recursiveaclpr.blob.core.windows.net/privatedrop/samplePS.ps1?sv=2019-02-02&st=2020-08-24T17%3A04%3A44Z&se=2021-08-25T17%3A04%3A00Z&sr=b&sp=r&sig=dNNKS%2BZcp%2F1gl6yOx6QLZ6OpmXkN88ZjBeBtym1Mejo%3D)  |  [Ge feedback](https://github.com/Azure/azure-powershell/issues)

## <a name="prerequisites"></a>Förutsättningar

- En Azure-prenumeration. Se [Hämta en kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/pricing/free-trial/).

- Ett lagrings konto med hierarkiskt namn område (HNS) aktiverat. Följ [de här](create-data-lake-storage-account.md) anvisningarna för att skapa en.

- Azure CLI-version `2.6.0` eller högre.

- En av följande säkerhets behörigheter:

  - Ett etablerat Azure Active Directory (AD) [säkerhets objekt](../../role-based-access-control/overview.md#security-principal) som har tilldelats rollen som ägare av [lagrings-BLOB-data](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner) i omfånget för antingen mål behållaren, den överordnade resurs gruppen eller prenumerationen.  

  - Ägande användare av mål behållaren eller katalogen som du planerar att tillämpa ACL-inställningar på. Om du vill ange ACL: er rekursivt inkluderar detta alla underordnade objekt i mål behållaren eller katalogen.
  
  - Lagrings konto nyckel.

## <a name="install-the-powershell-module"></a>Installera PowerShell-modulen

1. Kontrol lera att PowerShell-versionen som har installerats är `5.1` eller högre genom att använda följande kommando.    

   ```powershell
   echo $PSVersionTable.PSVersion.ToString() 
   ```

   Information om hur du uppgraderar din version av PowerShell finns i [uppgradera befintliga Windows PowerShell](/powershell/scripting/install/installing-windows-powershell#upgrading-existing-windows-powershell)

2. Installera **AZ. Storage** -modulen.

   ```powershell
   Install-Module Az.Storage -Repository PSGallery -Force  
   ```

   Mer information om hur du installerar PowerShell-moduler finns i [installera modulen Azure PowerShell](/powershell/azure/install-az-ps)

## <a name="connect-to-the-account"></a>Anslut till kontot

Välj hur du vill att dina kommandon ska få behörighet till lagrings kontot. 

### <a name="option-1-obtain-authorization-by-using-azure-active-directory-ad"></a>Alternativ 1: få behörighet genom att använda Azure Active Directory (AD)

> [!NOTE]
> Om du använder Azure Active Directory (Azure AD) för att auktorisera åtkomst kontrollerar du att ditt säkerhets objekt har tilldelats rollen som ägare av [lagrings-BLOB-data](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner). Mer information om hur ACL-behörigheter tillämpas och effekterna av att ändra dem finns i  [åtkomst kontroll modell i Azure Data Lake Storage Gen2](./data-lake-storage-access-control-model.md).

Med den här metoden säkerställer systemet att ditt användar konto har rätt Azure RBAC-tilldelning (Azure-rollbaserad åtkomst kontroll) och ACL-behörigheter.

1. Öppna ett Windows PowerShell-kommando fönster och logga sedan in på Azure-prenumerationen med `Connect-AzAccount` kommandot och följ anvisningarna på skärmen.

   ```powershell
   Connect-AzAccount
   ```

2. Om din identitet är associerad med fler än en prenumeration ställer du in din aktiva prenumeration på prenumerationen på det lagrings konto som du vill skapa och hantera kataloger i. I det här exemplet ersätter du `<subscription-id>` plats hållarens värde med ID: t för din prenumeration.

   ```powershell
   Select-AzSubscription -SubscriptionId <subscription-id>
   ``` 

3. Hämta lagrings konto kontexten.

   ```powershell
   $ctx = New-AzStorageContext -StorageAccountName '<storage-account-name>' -UseConnectedAccount
   ```

### <a name="option-2-obtain-authorization-by-using-the-storage-account-key"></a>Alternativ 2: få behörighet genom att använda lagrings konto nyckeln

Med den här metoden kontrollerar systemet inte Azure RBAC-eller ACL-behörigheter. Hämta lagrings kontots kontext med hjälp av en konto nyckel.

```powershell
$ctx = New-AzStorageContext -StorageAccountName '<storage-account-name>' -StorageAccountKey '<storage-account-key>'
```

## <a name="get-acls"></a>Hämta ACL: er

Hämta ACL för en katalog eller fil med hjälp av `Get-AzDataLakeGen2Item` cmdleten.

Det här exemplet hämtar ACL: en för rot katalogen för en **behållare** och skriver sedan ut ACL: en till-konsolen.

```powershell
$filesystemName = "my-file-system"
$filesystem = Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName
$filesystem.ACL
```

Det här exemplet hämtar ACL: en för en **katalog** och skriver sedan ut ACL: en till-konsolen.

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
$dir = Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname
$dir.ACL
```

Det här exemplet hämtar ACL: en för en **fil** och skriver sedan ut ACL: en till-konsolen.

```powershell
$filePath = "my-directory/upload.txt"
$file = Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $filePath
$file.ACL
```

Följande bild visar utdata när du har hämtat ACL för en katalog.

![Hämta ACL-utdata för katalog](./media/data-lake-storage-directory-file-acl-powershell/get-acl.png)

I det här exemplet har ägande användaren Läs-, skriv-och körnings behörighet. Den ägande gruppen har bara Läs-och körnings behörighet. Mer information om åtkomst kontrol listor finns i [åtkomst kontroll i Azure Data Lake Storage Gen2](data-lake-storage-access-control.md).

## <a name="set-acls"></a>Ange ACL: er

När du *anger* en ACL **ersätter** du hela ACL: en med alla poster. Om du vill ändra behörighets nivån för ett säkerhets objekt eller lägga till ett nytt säkerhets objekt i ACL utan att påverka andra befintliga poster, bör du *Uppdatera* ACL i stället. Information om hur du uppdaterar en ACL i stället för att ersätta den finns i avsnittet [Uppdatera ACL: er](#update-acls) i den här artikeln.  

Om du väljer att *Ange* ACL måste du lägga till en post för den ägande användaren, en post för den ägande gruppen och en post för alla andra användare. Mer information om ägande användare, ägande grupp och andra användare finns i [användare och identiteter](data-lake-storage-access-control.md#users-and-identities).

I det här avsnittet visas hur du:

- Ange en ACL
- Ange ACL:er rekursivt

### <a name="set-an-acl"></a>Ange en ACL

Använd `set-AzDataLakeGen2ItemAclObject` cmdleten för att skapa en ACL för ägande användare, ägande grupp eller andra användare. Använd sedan `Update-AzDataLakeGen2Item` cmdleten för att genomföra ACL: en.

I det här exemplet anges ACL: en för rot katalogen för en **behållare** för ägande användare, ägande grupp eller andra användare, och sedan skrivs ACL: en ut till-konsolen.

```powershell
$filesystemName = "my-file-system"
$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType user -Permission rw- 
$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType group -Permission rw- -InputObject $acl 
$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType other -Permission -wx -InputObject $acl
Update-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Acl $acl
$filesystem = Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName
$filesystem.ACL
```

I det här exemplet anges ACL: en för en **katalog** för ägande användare, ägande grupp eller andra användare, och sedan skrivs ACL: en ut till-konsolen.

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType user -Permission rw- 
$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType group -Permission rw- -InputObject $acl 
$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType other -Permission -wx -InputObject $acl
Update-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname -Acl $acl
$dir = Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname
$dir.ACL
```

> [!NOTE]
> Om du vill ange en **standard** -ACL-post använder du parametern **-DefaultScope** när du kör kommandot **set-AzDataLakeGen2ItemAclObject** . Exempel: `$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType user -Permission rwx -DefaultScope`. 

I det här exemplet anges ACL: en för en **fil** för ägande användare, ägande grupp eller andra användare, och sedan skrivs ACL: en ut till-konsolen.

```powershell
$filesystemName = "my-file-system"
$filePath = "my-directory/upload.txt"
$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType user -Permission rw- 
$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType group -Permission rw- -InputObject $acl 
$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType other -Permission "-wx" -InputObject $acl
Update-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $filePath -Acl $acl
$file = Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $filePath
$file.ACL
```

> [!NOTE]
> Om du vill ange ACL för en speciell grupp eller användare använder du deras respektive objekt-ID. Exempel: `group:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx` eller `user:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx`.

Följande bild visar utdata när du har angett ACL för en fil.

![Hämta ACL-utdata för filen](./media/data-lake-storage-directory-file-acl-powershell/set-acl.png)

I det här exemplet har ägande användare och ägande grupp bara Läs-och Skriv behörighet. Alla andra användare har Skriv-och körnings behörighet. Mer information om åtkomst kontrol listor finns i [åtkomst kontroll i Azure Data Lake Storage Gen2](data-lake-storage-access-control.md).

### <a name="set-acls-recursively"></a>Ange ACL:er rekursivt

Ange ACL: er rekursivt med hjälp av cmdleten **set-AzDataLakeGen2AclRecursive** .

I det här exemplet anges ACL: en för en katalog med namnet `my-parent-directory` . Dessa poster ger den ägande användaren Läs-, skriv-och körnings behörighet, ger den ägande gruppen endast Läs-och kör behörigheter och ger alla andra ingen åtkomst. Den sista ACL-posten i det här exemplet ger en speciell användare med objekt-ID: t "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX" Läs-och körnings behörighet.

```powershell
$filesystemName = "my-container"
$dirname = "my-parent-directory/"
$userID = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx";

$acl = Set-AzDataLakeGen2ItemAclObject -AccessControlType user -Permission rwx 
$acl = Set-AzDataLakeGen2ItemAclObject -AccessControlType group -Permission r-x -InputObject $acl 
$acl = Set-AzDataLakeGen2ItemAclObject -AccessControlType other -Permission "---" -InputObject $acl
$acl = Set-AzDataLakeGen2ItemAclObject -AccessControlType user -EntityId $userID -Permission r-x -InputObject $acl 

Set-AzDataLakeGen2AclRecursive -Context $ctx -FileSystem $filesystemName -Path $dirname -Acl $acl

```

> [!NOTE]
> Om du vill ange en **standard** -ACL-post använder du parametern **-DefaultScope** när du kör kommandot **set-AzDataLakeGen2ItemAclObject** . Exempel: `$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType user -Permission rwx -DefaultScope`.

Om du vill se ett exempel som anger ACL rekursivt i batchar genom att ange en batchstorlek, se artikeln [set-AzDataLakeGen2AclRecursive](/powershell/module/az.storage/set-azdatalakegen2aclrecursive) referens.

## <a name="update-acls"></a>Uppdatera ACL: er

När du *uppdaterar* en ACL ändrar du ACL i stället för att ersätta ACL: en. Du kan till exempel lägga till ett nytt säkerhets objekt till ACL utan att påverka andra säkerhets objekt som listas i åtkomst kontrol listan.  Om du vill ersätta ACL: en i stället för att uppdatera den, se avsnittet [ange ACL: er](#set-acls) i den här artikeln.

Om du vill uppdatera en ACL skapar du ett nytt ACL-objekt med ACL-posten som du vill uppdatera och använder sedan objektet i Update ACL-åtgärden. Hämta inte den befintliga ACL: en och ange bara ACL-poster som ska uppdateras.

I det här avsnittet visas hur du:

- Uppdatera en ACL
- Uppdatera ACL: er rekursivt

### <a name="update-an-acl"></a>Uppdatera en ACL

Hämta först ACL: en. Använd sedan `set-AzDataLakeGen2ItemAclObject` cmdleten för att lägga till eller uppdatera en ACL-post. Använd `Update-AzDataLakeGen2Item` cmdleten för att genomföra ACL: en.

I det här exemplet skapas eller uppdateras ACL: en för en användares **katalog** .

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
$acl = (Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname).ACL
$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType user -EntityID xxxxxxxx-xxxx-xxxxxxxxxxx -Permission r-x -InputObject $acl 
Update-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname -Acl $acl
```

> [!NOTE]
> Om du vill uppdatera en **standard** -ACL-post använder du parametern **-DefaultScope** när du kör kommandot **set-AzDataLakeGen2ItemAclObject** . Exempel: `$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType user -EntityID xxxxxxxx-xxxx-xxxxxxxxxxx -Permission r-x -DefaultScope`.

### <a name="update-acls-recursively"></a>Uppdatera ACL: er rekursivt

Uppdatera ACL: er rekursivt med hjälp av cmdleten  **Update-AzDataLakeGen2AclRecursive** .

Det här exemplet uppdaterar en ACL-post med Skriv behörighet.

```powershell
$filesystemName = "my-container"
$dirname = "my-parent-directory/"
$userID = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx";

$acl = Set-AzDataLakeGen2ItemAclObject -AccessControlType user -EntityId $userID -Permission rwx

Update-AzDataLakeGen2AclRecursive -Context $ctx -FileSystem $filesystemName -Path $dirname -Acl $acl

```

> [!NOTE]
> Om du vill ange ACL för en speciell grupp eller användare använder du deras respektive objekt-ID. Exempel: `group:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx` eller `user:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx`.

Om du vill se ett exempel som uppdaterar ACL rekursivt i batchar genom att ange en batchstorlek, se artikeln [Update-AzDataLakeGen2AclRecursive](/powershell/module/az.storage/update-azdatalakegen2aclrecursive) referens.

## <a name="remove-acl-entries"></a>Ta bort ACL-poster

I det här avsnittet visas hur du:

- Ta bort en ACL-post
- Ta bort ACL-poster rekursivt

### <a name="remove-an-acl-entry"></a>Ta bort en ACL-post

Det här exemplet tar bort en post från en befintlig ACL.

```powershell
$id = "xxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"

# Create the new ACL object.
[Collections.Generic.List[System.Object]]$aclnew =$acl

foreach ($a in $aclnew)
{
    if ($a.AccessControlType -eq "User"-and $a.DefaultScope -eq $false -and $a.EntityId -eq $id)
    {
        $aclnew.Remove($a);
        break;
    }
}
Update-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname -Acl $aclnew
```

### <a name="remove-acl-entries-recursively"></a>Ta bort ACL-poster rekursivt

Du kan ta bort en eller flera ACL-poster rekursivt. Om du vill ta bort en ACL-post skapar du ett nytt ACL-objekt för ACL-posten som ska tas bort och använder sedan objektet i ta bort ACL-åtgärd. Hämta inte den befintliga ACL: en och ange bara ACL-poster som ska tas bort.

Ta bort ACL-poster med hjälp av cmdleten **Remove-AzDataLakeGen2AclRecursive** .

Det här exemplet tar bort en ACL-post från rot katalogen för behållaren.  

```powershell
$filesystemName = "my-container"
$userID = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"

$acl = Set-AzDataLakeGen2ItemAclObject -AccessControlType user -EntityId $userID -Permission "---" 

Remove-AzDataLakeGen2AclRecursive -Context $ctx -FileSystem $filesystemName  -Acl $acl
```

> [!NOTE]
> Om du vill ta bort en **standard** -ACL-post använder du parametern **-DefaultScope** när du kör kommandot **set-AzDataLakeGen2ItemAclObject** . Exempel: `$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType user -EntityId $userID -Permission "---" -DefaultScope`.

Om du vill se ett exempel som tar bort ACL rekursivt i batchar genom att ange en batchstorlek, se artikeln [Remove-AzDataLakeGen2AclRecursive](/powershell/module/az.storage/remove-azdatalakegen2aclrecursive) referens.

## <a name="recover-from-failures"></a>Återställa från fel

Du kan stöta på körnings-eller behörighets fel när du ändrar ACL rekursivt. 

Starta om processen från början för körnings fel. Behörighets fel kan uppstå om säkerhetsobjektet inte har tillräcklig behörighet för att ändra ACL för en katalog eller fil som finns i den katalogpartition som ändras. Åtgärda behörighets problemet och välj sedan att antingen återuppta processen från fel punkten genom att använda en fortsättnings-token eller starta om processen från början. Du behöver inte använda den fortsättnings-token om du vill starta om från början. Du kan tillämpa ACL-poster igen utan negativ påverkan.

Det här exemplet returnerar resultat till variabeln och rör sedan felaktiga poster till en formaterad tabell.

```powershell
$result = Set-AzDataLakeGen2AclRecursive -Context $ctx -FileSystem $filesystemName -Path $dirname -Acl $acl
$result
$result.FailedEntries | ft 
```

Baserat på utdata från tabellen kan du åtgärda eventuella behörighets fel och sedan återuppta körningen med hjälp av en fortsättnings-token.

```powershell
$result = Set-AzDataLakeGen2AclRecursive -Context $ctx -FileSystem $filesystemName -Path $dirname -Acl $acl -ContinuationToken $result.ContinuationToken
$result

```

Om du vill se ett exempel som anger ACL rekursivt i batchar genom att ange en batchstorlek, se artikeln [set-AzDataLakeGen2AclRecursive](/powershell/module/az.storage/set-azdatalakegen2aclrecursive) referens.

Om du vill att processen ska slutföras oavbrutet genom behörighets fel kan du ange den.

I det här exemplet används `ContinueOnFailure` parametern så att körningen fortsätter även om åtgärden påträffar ett behörighets fel. 

```powershell
$result = Set-AzDataLakeGen2AclRecursive -Context $ctx -FileSystem $filesystemName -Path $dirname -Acl $acl -ContinueOnFailure

echo "[Result Summary]"
echo "TotalDirectoriesSuccessfulCount: `t$($result.TotalFilesSuccessfulCount)"
echo "TotalFilesSuccessfulCount: `t`t`t$($result.TotalDirectoriesSuccessfulCount)"
echo "TotalFailureCount: `t`t`t`t`t$($result.TotalFailureCount)"
echo "FailedEntries:"$($result.FailedEntries | ft) 
```

Om du vill se ett exempel som anger ACL rekursivt i batchar genom att ange en batchstorlek, se artikeln [set-AzDataLakeGen2AclRecursive](/powershell/module/az.storage/set-azdatalakegen2aclrecursive) referens.

[!INCLUDE [updated-for-az](../../../includes/recursive-acl-best-practices.md)]

## <a name="see-also"></a>Se även

- [Kända problem](data-lake-storage-known-issues.md#api-scope-data-lake-client-library)
- [Storage PowerShell cmdletar](/powershell/module/az.storage)
- [Åtkomst kontroll modell i Azure Data Lake Storage Gen2](data-lake-storage-access-control.md)
- [Åtkomst kontrol listor (ACL: er) i Azure Data Lake Storage Gen2](data-lake-storage-access-control.md)
