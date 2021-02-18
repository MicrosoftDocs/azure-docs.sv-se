---
title: Använda .NET för att hantera data i Azure Data Lake Storage Gen2
description: Använd Azure Storage klient bibliotek för .NET för att hantera kataloger och filer i lagrings konton med hierarkiskt namn område aktiverat.
author: normesta
ms.service: storage
ms.date: 02/17/2021
ms.author: normesta
ms.topic: how-to
ms.subservice: data-lake-storage-gen2
ms.reviewer: prishet
ms.custom: devx-track-csharp
ms.openlocfilehash: 52e993a22a512a94c8b5b8b050205db0c4ce0b1b
ms.sourcegitcommit: 227b9a1c120cd01f7a39479f20f883e75d86f062
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/18/2021
ms.locfileid: "100650328"
---
# <a name="use-net-to-manage-directories-and-files-in-azure-data-lake-storage-gen2"></a>Använd .NET för att hantera kataloger och filer i Azure Data Lake Storage Gen2

Den här artikeln visar hur du använder .NET för att skapa och hantera kataloger och filer i lagrings konton som har ett hierarkiskt namn område.

Information om hur du hämtar, anger och uppdaterar åtkomst kontrol listorna (ACL) för kataloger och filer finns i [använda .net för att hantera ACL: er i Azure Data Lake Storage Gen2](data-lake-storage-acl-dotnet.md).

[Paket (NuGet)](https://www.nuget.org/packages/Azure.Storage.Files.DataLake)  |  [Exempel](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake)  |  [API-referens](/dotnet/api/azure.storage.files.datalake)  |  [Gen1 till Gen2-mappning](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake/GEN1_GEN2_MAPPING.md)  |  [Ge feedback](https://github.com/Azure/azure-sdk-for-net/issues)

## <a name="prerequisites"></a>Förutsättningar

- En Azure-prenumeration. Se [Hämta en kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/pricing/free-trial/).

- Ett lagrings konto med hierarkiskt namn område aktiverat. Följ [de här](create-data-lake-storage-account.md) anvisningarna för att skapa en.

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

### <a name="connect-by-using-azure-active-directory-azure-ad"></a>Anslut med hjälp av Azure Active Directory (Azure AD)

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

## <a name="see-also"></a>Se även

- [Referensdokumentation för API](/dotnet/api/azure.storage.files.datalake)
- [Paket (NuGet)](https://www.nuget.org/packages/Azure.Storage.Files.DataLake)
- [Exempel](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake)
- [Gen1 till Gen2-mappning](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake/GEN1_GEN2_MAPPING.md)
- [Kända problem](data-lake-storage-known-issues.md#api-scope-data-lake-client-library)
- [Ge feedback](https://github.com/Azure/azure-sdk-for-net/issues)