---
title: Använd Java för att hantera data i Azure Data Lake Storage Gen2
description: Använd Azure Storage bibliotek för Java för att hantera kataloger och filer i lagrings konton med hierarkiskt namn område aktiverat.
author: normesta
ms.service: storage
ms.date: 02/17/2021
ms.custom: devx-track-java
ms.author: normesta
ms.topic: how-to
ms.subservice: data-lake-storage-gen2
ms.reviewer: prishet
ms.openlocfilehash: 10debe7bb870ddd9f8711e73ccb4b690d7011b62
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "100650199"
---
# <a name="use-java-to-manage-directories-and-files-in-azure-data-lake-storage-gen2"></a>Använd Java för att hantera kataloger och filer i Azure Data Lake Storage Gen2

Den här artikeln visar hur du använder Java för att skapa och hantera kataloger och filer i lagrings konton som har ett hierarkiskt namn område.

Information om hur du hämtar, anger och uppdaterar åtkomst kontrol listorna (ACL) för kataloger och filer finns i [använda. Java för att hantera ACL: er i Azure Data Lake Storage Gen2](data-lake-storage-acl-java.md).

[Paket (maven)](https://search.maven.org/artifact/com.azure/azure-storage-file-datalake)  |  [Exempel](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage/azure-storage-file-datalake)  |  [API-referens](/java/api/overview/azure/storage-file-datalake-readme)  |  [Gen1 till Gen2-mappning](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage/azure-storage-file-datalake/GEN1_GEN2_MAPPING.md)  |  [Ge feedback](https://github.com/Azure/azure-sdk-for-java/issues)

## <a name="prerequisites"></a>Förutsättningar

- En Azure-prenumeration. Se [Hämta en kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/pricing/free-trial/).

- Ett lagrings konto med hierarkiskt namn område aktiverat. Följ [de här](create-data-lake-storage-account.md) anvisningarna för att skapa en.

## <a name="set-up-your-project"></a>Konfigurera projektet

Kom igång genom att öppna [den här sidan](https://search.maven.org/artifact/com.azure/azure-storage-file-datalake) och hitta den senaste versionen av Java-biblioteket. Öppna sedan *pom.xml* -filen i text redigeraren. Lägg till ett beroende element som refererar till den versionen.

Om du planerar att autentisera ditt klient program med hjälp av Azure Active Directory (Azure AD) lägger du till ett beroende till klient biblioteket för Azure Secret. Se  [lägga till det hemliga klient biblioteks paketet i projektet](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/identity/azure-identity#adding-the-package-to-your-project).

Lägg sedan till dessa import uttryck i din kod fil.

```java
import com.azure.storage.common.StorageSharedKeyCredential;
import com.azure.storage.file.datalake.DataLakeDirectoryClient;
import com.azure.storage.file.datalake.DataLakeFileClient;
import com.azure.storage.file.datalake.DataLakeFileSystemClient;
import com.azure.storage.file.datalake.DataLakeServiceClient;
import com.azure.storage.file.datalake.DataLakeServiceClientBuilder;
import com.azure.storage.file.datalake.models.ListPathsOptions;
import com.azure.storage.file.datalake.models.PathItem;
import com.azure.storage.file.datalake.models.AccessControlChangeCounters;
import com.azure.storage.file.datalake.models.AccessControlChangeResult;
import com.azure.storage.file.datalake.models.AccessControlType;
import com.azure.storage.file.datalake.models.PathAccessControl;
import com.azure.storage.file.datalake.models.PathAccessControlEntry;
import com.azure.storage.file.datalake.models.PathPermissions;
import com.azure.storage.file.datalake.models.PathRemoveAccessControlEntry;
import com.azure.storage.file.datalake.models.RolePermissions;
import com.azure.storage.file.datalake.options.PathSetAccessControlRecursiveOptions;
```

## <a name="connect-to-the-account"></a>Anslut till kontot

Om du vill använda kodfragmenten i den här artikeln måste du skapa en **DataLakeServiceClient** -instans som representerar lagrings kontot. 

### <a name="connect-by-using-an-account-key"></a>Anslut med hjälp av en konto nyckel

Detta är det enklaste sättet att ansluta till ett konto. 

I det här exemplet skapas en **DataLakeServiceClient** -instans med hjälp av en konto nyckel.

:::code language="java" source="~/azure-storage-snippets/blobs/howto/Java/Java-v12/src/main/java/com/datalake/manage/Authorize_DataLake.java" id="Snippet_AuthorizeWithKey":::

### <a name="connect-by-using-azure-active-directory-azure-ad"></a>Anslut med hjälp av Azure Active Directory (Azure AD)

Du kan använda [klient biblioteket för Azure Identity för Java för](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/identity/azure-identity) att autentisera ditt program med Azure AD.

I det här exemplet skapas en **DataLakeServiceClient** -instans med hjälp av ett klient-ID, en klient hemlighet och ett klient-ID.  Om du vill hämta dessa värden läser du [Hämta en token från Azure AD för att auktorisera begär Anden från ett klient program](../common/storage-auth-aad-app.md).

:::code language="java" source="~/azure-storage-snippets/blobs/howto/Java/Java-v12/src/main/java/com/datalake/manage/Authorize_DataLake.java" id="Snippet_AuthorizeWithAzureAD":::

> [!NOTE]
> Fler exempel finns i [klient biblioteket för Azure Identity-klienten för Java](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/identity/azure-identity) -dokumentation.

## <a name="create-a-container"></a>Skapa en container

En behållare fungerar som ett fil system för dina filer. Du kan skapa en genom att anropa metoden **DataLakeServiceClient. createFileSystem** .

I det här exemplet skapas en behållare med namnet `my-file-system` . 

:::code language="java" source="~/azure-storage-snippets/blobs/howto/Java/Java-v12/src/main/java/com/datalake/manage/CRUD_DataLake.java" id="Snippet_CreateFileSystem":::

## <a name="create-a-directory"></a>Skapa en katalog

Skapa en katalog referens genom att anropa metoden **DataLakeFileSystemClient. createDirectory** .

Det här exemplet lägger till en katalog med namnet `my-directory` i en behållare och lägger sedan till en under katalog med namnet `my-subdirectory` . 

:::code language="java" source="~/azure-storage-snippets/blobs/howto/Java/Java-v12/src/main/java/com/datalake/manage/CRUD_DataLake.java" id="Snippet_CreateDirectory":::

## <a name="rename-or-move-a-directory"></a>Byta namn på eller flytta en katalog

Byt namn på eller flytta en katalog genom att anropa metoden **DataLakeDirectoryClient. Rename** . Skicka sökvägen till önskad katalog en parameter. 

I det här exemplet byter namn på en under katalog till namnet `my-subdirectory-renamed` .

:::code language="java" source="~/azure-storage-snippets/blobs/howto/Java/Java-v12/src/main/java/com/datalake/manage/CRUD_DataLake.java" id="Snippet_RenameDirectory":::

I det här exemplet flyttas en katalog med namnet `my-subdirectory-renamed` till en under katalog till en katalog med namnet `my-directory-2` . 

:::code language="java" source="~/azure-storage-snippets/blobs/howto/Java/Java-v12/src/main/java/com/datalake/manage/CRUD_DataLake.java" id="Snippet_MoveDirectory":::

## <a name="delete-a-directory"></a>Ta bort en katalog

Ta bort en katalog genom att anropa metoden **DataLakeDirectoryClient. deleteWithResponse** .

Det här exemplet tar bort en katalog med namnet `my-directory` .   

:::code language="java" source="~/azure-storage-snippets/blobs/howto/Java/Java-v12/src/main/java/com/datalake/manage/CRUD_DataLake.java" id="Snippet_DeleteDirectory":::

## <a name="upload-a-file-to-a-directory"></a>Ladda upp en fil till en katalog

Börja med att skapa en fil referens i mål katalogen genom att skapa en instans av klassen **DataLakeFileClient** . Ladda upp en fil genom att anropa metoden **DataLakeFileClient. append** . Se till att slutföra överföringen genom att anropa metoden **DataLakeFileClient. FlushAsync** .

I det här exemplet överförs en textfil till en katalog med namnet `my-directory` .

:::code language="java" source="~/azure-storage-snippets/blobs/howto/Java/Java-v12/src/main/java/com/datalake/manage/CRUD_DataLake.java" id="Snippet_UploadFile":::

> [!TIP]
> Om din fil storlek är stor måste koden göra flera anrop till metoden **DataLakeFileClient. append** . Överväg att använda metoden **DataLakeFileClient. uploadFromFile** i stället. På så sätt kan du ladda upp hela filen i ett enda anrop. 
>
> Se nästa avsnitt för ett exempel.

## <a name="upload-a-large-file-to-a-directory"></a>Ladda upp en stor fil till en katalog

Använd metoden **DataLakeFileClient. uploadFromFile** för att överföra stora filer utan att behöva göra flera anrop till **DataLakeFileClient. append** -metoden.

:::code language="java" source="~/azure-storage-snippets/blobs/howto/Java/Java-v12/src/main/java/com/datalake/manage/CRUD_DataLake.java" id="Snippet_UploadFileBulk":::

## <a name="download-from-a-directory"></a>Ladda ned från en katalog

Börja med att skapa en **DataLakeFileClient** -instans som representerar den fil som du vill ladda ned. Använd metoden **DataLakeFileClient. Read** för att läsa filen. Använd alla .NET-fil bearbetnings-API: er för att spara byte från data strömmen till en fil. 

:::code language="java" source="~/azure-storage-snippets/blobs/howto/Java/Java-v12/src/main/java/com/datalake/manage/CRUD_DataLake.java" id="Snippet_DownloadFile":::

## <a name="list-directory-contents"></a>Lista kataloginnehåll

Det här exemplet skriver ut namnen på varje fil som finns i en katalog med namnet `my-directory` .

:::code language="java" source="~/azure-storage-snippets/blobs/howto/Java/Java-v12/src/main/java/com/datalake/manage/CRUD_DataLake.java" id="Snippet_ListFilesInDirectory":::

## <a name="see-also"></a>Se även

* [Referensdokumentation för API](/java/api/overview/azure/storage-file-datalake-readme)
* [Paket (maven)](https://search.maven.org/artifact/com.azure/azure-storage-file-datalake)
* [Exempel](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage/azure-storage-file-datalake)
* [Gen1 till Gen2-mappning](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage/azure-storage-file-datalake/GEN1_GEN2_MAPPING.md)
* [Kända problem](data-lake-storage-known-issues.md#api-scope-data-lake-client-library)
* [Ge feedback](https://github.com/Azure/azure-sdk-for-java/issues)