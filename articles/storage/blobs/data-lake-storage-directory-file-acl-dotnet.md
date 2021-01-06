---
title: 'Azure Data Lake Storage Gen2 .NET SDK för filer & ACL: er'
description: Använd Azure Storage klient bibliotek för att hantera kataloger och åtkomst kontrol listor för filer och kataloger (ACL) i lagrings konton som har hierarkiskt namn område (HNS) aktiverat.
author: normesta
ms.service: storage
ms.date: 08/26/2020
ms.author: normesta
ms.topic: how-to
ms.subservice: data-lake-storage-gen2
ms.reviewer: prishet
ms.custom: devx-track-csharp
ms.openlocfilehash: 5af1c656699a7c60ad4f93beb43b603bdc6e3be7
ms.sourcegitcommit: 2aa52d30e7b733616d6d92633436e499fbe8b069
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/06/2021
ms.locfileid: "97935112"
---
# <a name="use-net-to-manage-directories-files-and-acls-in-azure-data-lake-storage-gen2"></a>Använd .NET för att hantera kataloger, filer och ACL: er i Azure Data Lake Storage Gen2

Den här artikeln visar hur du använder .NET för att skapa och hantera kataloger, filer och behörigheter i lagrings konton med hierarkiskt namn område (HNS) aktiverat. 

[Paket (NuGet)](https://www.nuget.org/packages/Azure.Storage.Files.DataLake)  |  [Exempel](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake)  |  [API-referens](/dotnet/api/azure.storage.files.datalake)  |  [Gen1 till Gen2-mappning](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake/GEN1_GEN2_MAPPING.md)  |  [Ge feedback](https://github.com/Azure/azure-sdk-for-net/issues)

## <a name="prerequisites"></a>Förutsättningar

> [!div class="checklist"]
> * En Azure-prenumeration. Se [Hämta en kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/pricing/free-trial/).
> * Ett lagrings konto med hierarkiskt namn område (HNS) aktiverat. Följ [de här](../common/storage-account-create.md) anvisningarna för att skapa en.

## <a name="set-up-your-project"></a>Konfigurera projektet

Kom igång genom att installera NuGet-paketet för [Azure. Storage. files. DataLake](https://www.nuget.org/packages/Azure.Storage.Files.DataLake/) .

Mer information om hur du installerar NuGet-paket finns i [Installera och hantera paket i Visual Studio med hjälp av NuGet Package Manager](/nuget/consume-packages/install-use-packages-visual-studio).

Lägg sedan till dessa med-instruktioner överst i din kod fil.

```csharp
using Azure;
using Azure.Storage.Files.DataLake;
using Azure.Storage.Files.DataLake.Models;
using Azure.Storage;
using System.IO;

```

## <a name="connect-to-the-account"></a>Anslut till kontot

Om du vill använda kodfragmenten i den här artikeln måste du skapa en [DataLakeServiceClient](/dotnet/api/azure.storage.files.datalake.datalakeserviceclient) -instans som representerar lagrings kontot. 

### <a name="connect-by-using-an-account-key"></a>Anslut med hjälp av en konto nyckel

Detta är det enklaste sättet att ansluta till ett konto. 

I det här exemplet skapas en [DataLakeServiceClient](/dotnet/api/azure.storage.files.datalake.datalakeserviceclient) -instans med hjälp av en konto nyckel.

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Authorize_DataLake.cs" id="Snippet_AuthorizeWithKey":::

### <a name="connect-by-using-azure-active-directory-ad"></a>Anslut med hjälp av Azure Active Directory (AD)

Du kan använda [Azure Identity client-biblioteket för .net för](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/identity/Azure.Identity) att autentisera ditt program med Azure AD.

I det här exemplet skapas en [DataLakeServiceClient](/dotnet/api/azure.storage.files.datalake.datalakeserviceclient) -instans med hjälp av ett klient-ID, en klient hemlighet och ett klient-ID.  Om du vill hämta dessa värden läser du [Hämta en token från Azure AD för att auktorisera begär Anden från ett klient program](../common/storage-auth-aad-app.md).

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Authorize_DataLake.cs" id="Snippet_AuthorizeWithAAD":::

> [!NOTE]
> Fler exempel finns i dokumentationen [för Azure Identity Client library för .net](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/identity/Azure.Identity) .

## <a name="create-a-container"></a>Skapa en container

En behållare fungerar som ett fil system för dina filer. Du kan skapa en genom att anropa metoden [DataLakeServiceClient. CreateFileSystem](/dotnet/api/azure.storage.files.datalake.datalakeserviceclient.createfilesystemasync) .

I det här exemplet skapas en behållare med namnet `my-file-system` . 

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/CRUD_DataLake.cs" id="Snippet_CreateContainer":::

## <a name="create-a-directory"></a>Skapa en katalog

Skapa en katalog referens genom att anropa metoden [DataLakeFileSystemClient. CreateDirectoryAsync](/dotnet/api/azure.storage.files.datalake.datalakefilesystemclient.createdirectoryasync) .

Det här exemplet lägger till en katalog med namnet `my-directory` i en behållare och lägger sedan till en under katalog med namnet `my-subdirectory` . 

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/CRUD_DataLake.cs" id="Snippet_CreateDirectory":::

## <a name="rename-or-move-a-directory"></a>Byta namn på eller flytta en katalog

Byt namn på eller flytta en katalog genom att anropa metoden [DataLakeDirectoryClient. RenameAsync](/dotnet/api/azure.storage.files.datalake.datalakedirectoryclient.renameasync) . Skicka sökvägen till önskad katalog en parameter. 

I det här exemplet byter namn på en under katalog till namnet `my-subdirectory-renamed` .

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/CRUD_DataLake.cs" id="Snippet_RenameDirectory":::

I det här exemplet flyttas en katalog med namnet `my-subdirectory-renamed` till en under katalog till en katalog med namnet `my-directory-2` . 

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/CRUD_DataLake.cs" id="Snippet_MoveDirectory":::

## <a name="delete-a-directory"></a>Ta bort en katalog

Ta bort en katalog genom att anropa metoden [DataLakeDirectoryClient. Delete](/dotnet/api/azure.storage.files.datalake.datalakedirectoryclient.delete) .

Det här exemplet tar bort en katalog med namnet `my-directory` .  

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/CRUD_DataLake.cs" id="Snippet_DeleteDirectory":::

## <a name="upload-a-file-to-a-directory"></a>Ladda upp en fil till en katalog

Börja med att skapa en fil referens i mål katalogen genom att skapa en instans av klassen [DataLakeFileClient](/dotnet/api/azure.storage.files.datalake.datalakefileclient) . Ladda upp en fil genom att anropa metoden [DataLakeFileClient. AppendAsync](/dotnet/api/azure.storage.files.datalake.datalakefileclient.appendasync) . Se till att slutföra överföringen genom att anropa metoden [DataLakeFileClient. FlushAsync](/dotnet/api/azure.storage.files.datalake.datalakefileclient.flushasync) .

I det här exemplet överförs en textfil till en katalog med namnet `my-directory` . 
   
:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/CRUD_DataLake.cs" id="Snippet_UploadFile":::

> [!TIP]
> Om din fil storlek är stor måste koden göra flera anrop till [DataLakeFileClient. AppendAsync](/dotnet/api/azure.storage.files.datalake.datalakefileclient.appendasync). Överväg att använda metoden [DataLakeFileClient. UploadAsync](/dotnet/api/azure.storage.files.datalake.datalakefileclient.uploadasync#Azure_Storage_Files_DataLake_DataLakeFileClient_UploadAsync_System_IO_Stream_) i stället. På så sätt kan du ladda upp hela filen i ett enda anrop. 
>
> Se nästa avsnitt för ett exempel.

## <a name="upload-a-large-file-to-a-directory"></a>Ladda upp en stor fil till en katalog

Använd metoden [DataLakeFileClient. UploadAsync](/dotnet/api/azure.storage.files.datalake.datalakefileclient.uploadasync#Azure_Storage_Files_DataLake_DataLakeFileClient_UploadAsync_System_IO_Stream_) för att överföra stora filer utan att behöva göra flera anrop till metoden [DataLakeFileClient. AppendAsync](/dotnet/api/azure.storage.files.datalake.datalakefileclient.appendasync) .

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/CRUD_DataLake.cs" id="Snippet_UploadFileBulk":::

## <a name="download-from-a-directory"></a>Ladda ned från en katalog 

Börja med att skapa en [DataLakeFileClient](/dotnet/api/azure.storage.files.datalake.datalakefileclient) -instans som representerar den fil som du vill ladda ned. Använd metoden [DataLakeFileClient. ReadAsync](/dotnet/api/azure.storage.files.datalake.datalakefileclient.readasync)  och parsa returvärdet för att hämta ett [Stream](/dotnet/api/system.io.stream) -objekt. Använd alla .NET-fil bearbetnings-API: er för att spara byte från data strömmen till en fil. 

I det här exemplet används en [BinaryReader](/dotnet/api/system.io.binaryreader) och en [FILESTREAM](/dotnet/api/system.io.filestream) för att spara byte till en fil. 

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/CRUD_DataLake.cs" id="Snippet_DownloadBinaryFromDirectory":::

## <a name="list-directory-contents"></a>Lista kataloginnehåll

Lista katalog innehåll genom att anropa metoden [FileSystemClient. GetPathsAsync](/dotnet/api/azure.storage.files.datalake.datalakefilesystemclient.getpathsasync) och sedan räkna upp genom resultaten.

Det här exemplet skriver ut namnen på varje fil som finns i en katalog med namnet `my-directory` .

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/CRUD_DataLake.cs" id="Snippet_ListFilesInDirectory":::

## <a name="manage-access-control-lists-acls"></a>Hantera åtkomst kontrol listor (ACL: er)

Du kan hämta, ange och uppdatera åtkomst behörigheter för kataloger och filer.

> [!NOTE]
> Om du använder Azure Active Directory (Azure AD) för att auktorisera åtkomst kontrollerar du att ditt säkerhets objekt har tilldelats rollen som ägare av [lagrings-BLOB-data](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner). Mer information om hur ACL-behörigheter tillämpas och effekterna av att ändra dem finns i  [åtkomst kontroll i Azure Data Lake Storage Gen2](./data-lake-storage-access-control.md).

### <a name="manage-a-directory-acl"></a>Hantera en katalog-ACL

Hämta ACL (Access Control List) för en katalog genom att anropa metoden [DataLakeDirectoryClient. GetAccessControlAsync](/dotnet/api/azure.storage.files.datalake.datalakedirectoryclient.getaccesscontrolasync) och ange ACL genom att anropa metoden [DataLakeDirectoryClient. SetAccessControlList](/dotnet/api/azure.storage.files.datalake.datalakedirectoryclient.setaccesscontrollist) .

> [!NOTE]
> Om ditt program tillåter åtkomst genom att använda Azure Active Directory (Azure AD) måste du kontrol lera att det säkerhets objekt som programmet använder för att auktorisera åtkomst har tilldelats rollen som [lagrings-BLOB-dataägare](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner). Mer information om hur ACL-behörigheter tillämpas och effekterna av att ändra dem finns i  [åtkomst kontroll i Azure Data Lake Storage Gen2](./data-lake-storage-access-control.md). 

Det här exemplet hämtar och anger ACL: en för en katalog med namnet `my-directory` . Strängen `user::rwx,group::r-x,other::rw-` ger den ägande användaren Läs-, skriv-och körnings behörighet, ger den ägande gruppen endast Läs-och kör behörigheter och ger alla andra Läs-och Skriv behörighet.

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/ACL_DataLake.cs" id="Snippet_ACLDirectory":::

Du kan också hämta och ange ACL: en för rot katalogen för en behållare. Om du vill hämta rot katalogen skickar du en tom sträng ( `""` ) till metoden [DataLakeFileSystemClient. GetDirectoryClient](/dotnet/api/azure.storage.files.datalake.datalakefilesystemclient.getdirectoryclient) .

### <a name="manage-a-file-acl"></a>Hantera en fil-ACL

Hämta ACL (Access Control List) för en fil genom att anropa metoden [DataLakeFileClient. GetAccessControlAsync](/dotnet/api/azure.storage.files.datalake.datalakefileclient.getaccesscontrolasync) och ange ACL genom att anropa metoden [DataLakeFileClient. SetAccessControlList](/dotnet/api/azure.storage.files.datalake.datalakefileclient.setaccesscontrollist) .

> [!NOTE]
> Om ditt program tillåter åtkomst genom att använda Azure Active Directory (Azure AD) måste du kontrol lera att det säkerhets objekt som programmet använder för att auktorisera åtkomst har tilldelats rollen som [lagrings-BLOB-dataägare](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner). Mer information om hur ACL-behörigheter tillämpas och effekterna av att ändra dem finns i  [åtkomst kontroll i Azure Data Lake Storage Gen2](./data-lake-storage-access-control.md). 

Det här exemplet hämtar och anger ACL: en för en fil med namnet `my-file.txt` . Strängen `user::rwx,group::r-x,other::rw-` ger den ägande användaren Läs-, skriv-och körnings behörighet, ger den ägande gruppen endast Läs-och kör behörigheter och ger alla andra Läs-och Skriv behörighet.

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/ACL_DataLake.cs" id="Snippet_FileACL":::

### <a name="set-an-acl-recursively"></a>Ange en ACL rekursivt

Du kan lägga till, uppdatera och ta bort ACL rekursivt på befintliga underordnade objekt i en överordnad katalog utan att behöva göra dessa ändringar individuellt för varje underordnat objekt. Mer information finns i [ange åtkomst kontrol listor (ACL) rekursivt för Azure Data Lake Storage Gen2](recursive-access-control-lists.md).

## <a name="see-also"></a>Se även

* [Referensdokumentation för API](/dotnet/api/azure.storage.files.datalake)
* [Paket (NuGet)](https://www.nuget.org/packages/Azure.Storage.Files.DataLake)
* [Exempel](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake)
* [Gen1 till Gen2-mappning](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake/GEN1_GEN2_MAPPING.md)
* [Kända problem](data-lake-storage-known-issues.md#api-scope-data-lake-client-library)
* [Ge feedback](https://github.com/Azure/azure-sdk-for-net/issues)