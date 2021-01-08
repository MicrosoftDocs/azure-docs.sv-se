---
title: 'Azure Data Lake Storage Gen2 PowerShell för filer & ACL: er'
description: Använd PowerShell-cmdletar för att hantera kataloger och åtkomst kontrol listor för filer och kataloger (ACL) i lagrings konton med hierarkiskt namn område (HNS) aktiverat.
services: storage
author: normesta
ms.service: storage
ms.subservice: data-lake-storage-gen2
ms.topic: how-to
ms.date: 01/06/2021
ms.author: normesta
ms.reviewer: prishet
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 83a19074eb131b4024c0eaf92631a7b2f3d266d9
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/08/2021
ms.locfileid: "98014475"
---
# <a name="use-powershell-to-manage-directories-files-and-acls-in-azure-data-lake-storage-gen2"></a>Använd PowerShell för att hantera kataloger, filer och ACL: er i Azure Data Lake Storage Gen2

Den här artikeln visar hur du använder PowerShell för att skapa och hantera kataloger, filer och behörigheter i lagrings konton med hierarkiskt namn område (HNS) aktiverat. 

[Referens](/powershell/module/Az.Storage/)  |  [Gen1 till Gen2-mappning](#gen1-gen2-map)  |  [Ge feedback](https://github.com/Azure/azure-powershell/issues)

## <a name="prerequisites"></a>Förutsättningar

> [!div class="checklist"]
> * En Azure-prenumeration. Se [Hämta en kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/pricing/free-trial/).
> * Ett lagrings konto med hierarkiskt namn område (HNS) aktiverat. Följ [de här](../common/storage-account-create.md) anvisningarna för att skapa en.
> * .NET Framework är 4.7.2 eller senare installerad. Se [Ladda ned .NET Framework](https://dotnet.microsoft.com/download/dotnet-framework).
> * PowerShell-version `5.1` eller högre.

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

Öppna ett Windows PowerShell-kommando fönster och logga sedan in på Azure-prenumerationen med `Connect-AzAccount` kommandot och följ anvisningarna på skärmen.

```powershell
Connect-AzAccount
```

Om din identitet är associerad med fler än en prenumeration ställer du in din aktiva prenumeration på prenumerationen på det lagrings konto som du vill skapa och hantera kataloger i. I det här exemplet ersätter du `<subscription-id>` plats hållarens värde med ID: t för din prenumeration.

```powershell
Select-AzSubscription -SubscriptionId <subscription-id>
```

Välj sedan hur du vill att dina kommandon ska få behörighet till lagrings kontot. 

### <a name="option-1-obtain-authorization-by-using-azure-active-directory-ad"></a>Alternativ 1: få behörighet genom att använda Azure Active Directory (AD)

Med den här metoden säkerställer systemet att ditt användar konto har rätt Azure RBAC-tilldelning (Azure-rollbaserad åtkomst kontroll) och ACL-behörigheter. 

```powershell
$ctx = New-AzStorageContext -StorageAccountName '<storage-account-name>' -UseConnectedAccount
```

### <a name="option-2-obtain-authorization-by-using-the-storage-account-key"></a>Alternativ 2: få behörighet genom att använda lagrings konto nyckeln

Med den här metoden kontrollerar systemet inte Azure RBAC-eller ACL-behörigheter.

```powershell
$ctx = New-AzStorageContext -StorageAccountName "<storage-account-name>" -StorageAccountKey "<storage-account-key>"
```

## <a name="create-a-container"></a>Skapa en container

En behållare fungerar som ett fil system för dina filer. Du kan skapa en med hjälp av `New-AzStorageContainer` cmdleten. 

I det här exemplet skapas en behållare med namnet `my-file-system` .

```powershell
$filesystemName = "my-file-system"
New-AzStorageContainer -Context $ctx -Name $filesystemName
```

## <a name="create-a-directory"></a>Skapa en katalog

Skapa en katalog referens med hjälp av `New-AzDataLakeGen2Item` cmdleten. 

Det här exemplet lägger till en katalog med namnet `my-directory` i en behållare.

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
New-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname -Directory
```

Det här exemplet lägger till samma katalog, men anger även behörigheter, umask, egenskaps värden och metadata. 

```powershell
$dir = New-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname -Directory -Permission rwxrwxrwx -Umask ---rwx---  -Property @{"ContentEncoding" = "UDF8"; "CacheControl" = "READ"} -Metadata  @{"tag1" = "value1"; "tag2" = "value2" }
```

## <a name="show-directory-properties"></a>Visa katalog egenskaper

Det här exemplet hämtar en katalog med hjälp av `Get-AzDataLakeGen2Item` cmdleten och skriver sedan ut egenskaps värden till-konsolen.

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
$dir =  Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname
$dir.ACL
$dir.Permissions
$dir.Group
$dir.Owner
$dir.Properties
$dir.Properties.Metadata
```
> [!NOTE]
> Utelämna parametern om du vill hämta behållarens rot Katalog `-Path` .

## <a name="rename-or-move-a-directory"></a>Byta namn på eller flytta en katalog

Byt namn på eller flytta en katalog med hjälp av `Move-AzDataLakeGen2Item` cmdleten.

I det här exemplet byter namn på en katalog från namnet `my-directory` till namnet `my-new-directory` .

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
$dirname2 = "my-new-directory/"
Move-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname -DestFileSystem $filesystemName -DestPath $dirname2
```

> [!NOTE]
> Använd `-Force` parametern om du vill skriva över utan prompter.

I det här exemplet flyttas en katalog med namnet `my-directory` till en under katalog med `my-directory-2` namnet `my-subdirectory` . 

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
$dirname2 = "my-directory-2/my-subdirectory/"
Move-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname1 -DestFileSystem $filesystemName -DestPath $dirname2
```

## <a name="delete-a-directory"></a>Ta bort en katalog

Ta bort en katalog med hjälp av `Remove-AzDataLakeGen2Item` cmdleten.

Det här exemplet tar bort en katalog med namnet `my-directory` . 

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
Remove-AzDataLakeGen2Item  -Context $ctx -FileSystem $filesystemName -Path $dirname 
```

Du kan använda- `-Force` parametern för att ta bort filen utan någon prompt.

## <a name="download-from-a-directory"></a>Ladda ned från en katalog

Hämta en fil från en katalog med hjälp av `Get-AzDataLakeGen2ItemContent` cmdleten.

I det här exemplet hämtas en fil med namnet `upload.txt` från en katalog med namnet `my-directory` . 

```powershell
$filesystemName = "my-file-system"
$filePath = "my-directory/upload.txt"
$downloadFilePath = "download.txt"
Get-AzDataLakeGen2ItemContent -Context $ctx -FileSystem $filesystemName -Path $filePath -Destination $downloadFilePath
```

## <a name="list-directory-contents"></a>Lista kataloginnehåll

Lista innehållet i en katalog med hjälp av `Get-AzDataLakeGen2ChildItem` cmdleten. Du kan använda den valfria parametern `-OutputUserPrincipalName` för att hämta namnet (i stället för objekt-ID) för användare.

I det här exemplet visas innehållet i en katalog med namnet `my-directory` .

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
Get-AzDataLakeGen2ChildItem -Context $ctx -FileSystem $filesystemName -Path $dirname -OutputUserPrincipalName
```

I följande exempel visas `ACL` egenskaperna, `Permissions` , `Group` och `Owner` för varje objekt i katalogen. `-FetchProperty`Parametern krävs för att hämta värden för `ACL` egenskapen. 

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
$properties = Get-AzDataLakeGen2ChildItem -Context $ctx -FileSystem $filesystemName -Path $dirname -Recurse -FetchProperty
$properties.ACL
$properties.Permissions
$properties.Group
$properties.Owner
```

> [!NOTE]
> Om du vill visa innehållet i rot katalogen för behållaren utelämnar du `-Path` parametern.

## <a name="upload-a-file-to-a-directory"></a>Ladda upp en fil till en katalog

Ladda upp en fil till en katalog med hjälp av `New-AzDataLakeGen2Item` cmdleten.

I det här exemplet överförs en fil med namnet `upload.txt` till en katalog med namnet `my-directory` . 

```powershell
$localSrcFile =  "upload.txt"
$filesystemName = "my-file-system"
$dirname = "my-directory/"
$destPath = $dirname + (Get-Item $localSrcFile).Name
New-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $destPath -Source $localSrcFile -Force 
```

I det här exemplet överförs samma fil, men sedan anges behörigheter, umask, egenskaps värden och metadata för målfilen. I det här exemplet skrivs även dessa värden ut till-konsolen.

```powershell
$file = New-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $destPath -Source $localSrcFile -Permission rwxrwxrwx -Umask ---rwx--- -Property @{"ContentEncoding" = "UDF8"; "CacheControl" = "READ"} -Metadata  @{"tag1" = "value1"; "tag2" = "value2" }
$file1
$file1.Properties
$file1.Properties.Metadata

```

> [!NOTE]
> Om du vill överföra en fil till rot katalogen för behållaren utelämnar du `-Path` parametern.

## <a name="show-file-properties"></a>Visa fil egenskaper

Det här exemplet hämtar en fil med hjälp av `Get-AzDataLakeGen2Item` cmdleten och skriver sedan ut egenskaps värden till-konsolen.

```powershell
$filepath =  "my-directory/upload.txt"
$filesystemName = "my-file-system"
$file = Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $filepath
$file
$file.ACL
$file.Permissions
$file.Group
$file.Owner
$file.Properties
$file.Properties.Metadata
```

## <a name="delete-a-file"></a>Ta bort en fil

Ta bort en fil med hjälp av `Remove-AzDataLakeGen2Item` cmdleten.

Det här exemplet tar bort en fil med namnet `upload.txt` . 

```powershell
$filesystemName = "my-file-system"
$filepath = "upload.txt"
Remove-AzDataLakeGen2Item  -Context $ctx -FileSystem $filesystemName -Path $filepath 
```

Du kan använda- `-Force` parametern för att ta bort filen utan någon prompt.

## <a name="manage-access-control-lists-acls"></a>Hantera åtkomst kontrol listor (ACL: er)

Du kan hämta, ange och uppdatera åtkomst behörigheter för kataloger och filer.

> [!NOTE]
> Om du använder Azure Active Directory (Azure AD) för att auktorisera kommandon kontrollerar du att ditt säkerhets objekt har tilldelats rollen som ägare av [lagrings-BLOB-data](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner). Mer information om hur ACL-behörigheter tillämpas och effekterna av att ändra dem finns i  [åtkomst kontroll i Azure Data Lake Storage Gen2](./data-lake-storage-access-control.md).

### <a name="get-an-acl"></a>Hämta en ACL

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
> Om du vill ange en **standard** -ACL-post använder du parametern **-DefaultScope** när du kör kommandot **set-AzDataLakeGen2ItemAclObject** . Till exempel: `$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType user -Permission rwx -DefaultScope`.

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
> Om du vill ange en **standard** -ACL-post använder du parametern **-DefaultScope** när du kör kommandot **set-AzDataLakeGen2ItemAclObject** . Till exempel: `$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType user -Permission rwx -DefaultScope`.

Följande bild visar utdata när du har angett ACL för en fil.

![Hämta ACL-utdata för filen](./media/data-lake-storage-directory-file-acl-powershell/set-acl.png)

I det här exemplet har ägande användare och ägande grupp bara Läs-och Skriv behörighet. Alla andra användare har Skriv-och körnings behörighet. Mer information om åtkomst kontrol listor finns i [åtkomst kontroll i Azure Data Lake Storage Gen2](data-lake-storage-access-control.md).

### <a name="add-or-update-an-acl-entry"></a>Lägga till eller uppdatera en ACL-post

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
> Om du vill uppdatera en **standard** -ACL-post använder du parametern **-DefaultScope** när du kör kommandot **set-AzDataLakeGen2ItemAclObject** . Till exempel: `$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType user -EntityID xxxxxxxx-xxxx-xxxxxxxxxxx -Permission r-x -DefaultScope`.

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

### <a name="set-an-acl-recursively"></a>Ange en ACL rekursivt

Du kan lägga till, uppdatera och ta bort ACL rekursivt på befintliga underordnade objekt i en överordnad katalog utan att behöva göra dessa ändringar individuellt för varje underordnat objekt. Mer information finns i [ange åtkomst kontrol listor (ACL) rekursivt för Azure Data Lake Storage Gen2](recursive-access-control-lists.md).

<a id="gen1-gen2-map"></a>

## <a name="gen1-to-gen2-mapping"></a>Gen1 till Gen2-mappning

I följande tabell visas hur cmdletarna som används för Data Lake Storage Gen1 mappas till-cmdletarna för Data Lake Storage Gen2.

|Data Lake Storage Gen1 cmdlet| Data Lake Storage Gen2 cmdlet| Kommentarer |
|--------|---------|-----|
|Get-AzDataLakeStoreChildItem|Get-AzDataLakeGen2ChildItem|Som standard visar Get-AzDataLakeGen2ChildItem-cmdleten bara de underordnade objekten på den första nivån. Parametern-rekursivt listar underordnade objekt rekursivt. |
|Get-AzDataLakeStoreItem<br>Get-AzDataLakeStoreItemAclEntry<br>Get-AzDataLakeStoreItemOwner<br>Get-AzDataLakeStoreItemPermission|Get-AzDataLakeGen2Item|Utgående objekt i Get-AzDataLakeGen2Item-cmdlet har följande egenskaper: ACL, ägare, grupp, behörighet.|
|Get-AzDataLakeStoreItemContent|Get-AzDataLakeGen2FileContent|Den Get-AzDataLakeGen2FileContent cmdlet: en hämtar fil innehåll till en lokal fil.|
|Move-AzDataLakeStoreItem|Move-AzDataLakeGen2Item||
|New-AzDataLakeStoreItem|New-AzDataLakeGen2Item|Denna cmdlet överför det nya fil innehållet från en lokal fil.|
|Remove-AzDataLakeStoreItem|Remove-AzDataLakeGen2Item||
|Set-AzDataLakeStoreItemOwner<br>Set-AzDataLakeStoreItemPermission<br>Set-AzDataLakeStoreItemAcl|Update-AzDataLakeGen2Item|Update-AzDataLakeGen2Item-cmdlet: en uppdaterar endast ett enskilt objekt och inte rekursivt. Om du vill uppdatera rekursivt, list objekt med hjälp av Get-AzDataLakeStoreChildItem-cmdlet och sedan pipelinen till Update-AzDataLakeGen2Item-cmdleten.|
|Test-AzDataLakeStoreItem|Get-AzDataLakeGen2Item|Get-AzDataLakeGen2Item-cmdleten rapporterar ett fel om objektet inte finns.|

## <a name="see-also"></a>Se även

* [Kända problem](data-lake-storage-known-issues.md#api-scope-data-lake-client-library)
* [Storage PowerShell cmdletar](/powershell/module/az.storage)
