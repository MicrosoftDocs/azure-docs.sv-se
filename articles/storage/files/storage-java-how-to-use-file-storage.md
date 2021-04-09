---
title: Utveckla för Azure Files med Java | Microsoft Docs
description: Lär dig hur du utvecklar Java-program och tjänster som använder Azure Files för att lagra fildata.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 11/18/2020
ms.custom: devx-track-java
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 25baa278961b93b04e60f2e997b98753cb6cf3ab
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "95024117"
---
# <a name="develop-for-azure-files-with-java"></a>Utveckla för Azure Files med Java

[!INCLUDE [storage-selector-file-include](../../../includes/storage-selector-file-include.md)]

Lär dig grunderna för att utveckla Java-program som använder Azure Files för att lagra data. Skapa ett konsol program och lär dig grundläggande åtgärder med hjälp av Azure Files API: er:

- Skapa och ta bort Azure-filresurser
- Skapa och ta bort kataloger
- Räkna upp filer och kataloger i en Azure-filresurs
- Ladda upp, ladda ned och ta bort en fil

[!INCLUDE [storage-check-out-samples-java](../../../includes/storage-check-out-samples-java.md)]

## <a name="create-a-java-application"></a>Skapa ett Java-program

För att bygga exemplen behöver du Java Development Kit (JDK) och [Azure Storage SDK för Java](https://github.com/azure/azure-sdk-for-java). Du bör också ha skapat ett Azure Storage-konto.

## <a name="set-up-your-application-to-use-azure-files"></a>Konfigurera ditt program så att det använder Azure Files

Om du vill använda Azure Files-API: er lägger du till följande kod överst i Java-filen från den plats där du vill komma åt Azure Files.

# <a name="java-v12"></a>[Java-V12](#tab/java)

:::code language="java" source="~/azure-storage-snippets/files/howto/java/java-v12/files-howto-v12/src/main/java/com/files/howto/App.java" id="Snippet_ImportStatements":::

# <a name="java-v11"></a>[Java-v11](#tab/java11)

```java
// Include the following imports to use Azure Files APIs v11
import com.microsoft.azure.storage.*;
import com.microsoft.azure.storage.file.*;
```

---

## <a name="set-up-an-azure-storage-connection-string"></a>Konfigurera en anslutnings sträng för Azure Storage

Om du vill använda Azure Files måste du ansluta till ditt Azure Storage-konto. Konfigurera en anslutnings sträng och Använd den för att ansluta till ditt lagrings konto. Definiera en statisk variabel som ska innehålla anslutnings strängen.

# <a name="java-v12"></a>[Java-V12](#tab/java)

Ersätt *\<storage_account_name\>* och *\<storage_account_key\>* med de faktiska värdena för ditt lagrings konto.

:::code language="java" source="~/azure-storage-snippets/files/howto/java/java-v12/files-howto-v12/src/main/java/com/files/howto/App.java" id="Snippet_ConnectionString":::

# <a name="java-v11"></a>[Java-v11](#tab/java11)

Ersätt *your_storage_account_name* och *your_storage_account_key* med de faktiska värdena för ditt lagrings konto.

```java
// Configure the connection-string with your values
public static final String storageConnectionString =
    "DefaultEndpointsProtocol=http;" +
    "AccountName=your_storage_account_name;" +
    "AccountKey=your_storage_account_key";
```

---

## <a name="access-azure-files-storage"></a>Åtkomst Azure Files lagring

# <a name="java-v12"></a>[Java-V12](#tab/java)

Skapa ett [ShareClient](/java/api/com.azure.storage.file.share.shareclient) -objekt för att få åtkomst till Azure Files. Använd [ShareClientBuilder](/java/api/com.azure.storage.file.share.shareclientbuilder) -klassen för att bygga ett nytt **ShareClient** -objekt.

:::code language="java" source="~/azure-storage-snippets/files/howto/java/java-v12/files-howto-v12/src/main/java/com/files/howto/App.java" id="Snippet_createClient":::

# <a name="java-v11"></a>[Java-v11](#tab/java11)

Om du vill komma åt ditt lagrings konto använder du **CloudStorageAccount** -objektet och skickar anslutnings strängen till dess **parse** -metod.

```java
// Use the CloudStorageAccount object to connect to your storage account
try {
    CloudStorageAccount storageAccount = CloudStorageAccount.parse(storageConnectionString);
} catch (InvalidKeyException invalidKey) {
    // Handle the exception
}
```

**CloudStorageAccount. parse** genererar en InvalidKeyException så du måste lägga den i ett try/catch-block.

---

## <a name="create-a-file-share"></a>Skapa en filresurs

Alla filer och kataloger i Azure Files lagras i en behållare som kallas för en resurs.

# <a name="java-v12"></a>[Java-V12](#tab/java)

Metoden [ShareClient. Create](/java/api/com.azure.storage.file.share.shareclient.create) genererar ett undantag om resursen redan finns. Parkera anropet för att **skapa** i ett `try/catch` block och hantera undantaget.

:::code language="java" source="~/azure-storage-snippets/files/howto/java/java-v12/files-howto-v12/src/main/java/com/files/howto/App.java" id="Snippet_createFileShare":::

# <a name="java-v11"></a>[Java-v11](#tab/java11)

Skapa en Azure Files-klient för att få åtkomst till en resurs och dess innehåll.

```java
// Create the Azure Files client.
CloudFileClient fileClient = storageAccount.createCloudFileClient();
```

Med hjälp av Azure Files-klienten kan du hämta en referens till en resurs.

```java
// Get a reference to the file share
CloudFileShare share = fileClient.getShareReference("sampleshare");
```

Skapa resursen genom att använda **createIfNotExists** -metoden för **CloudFileShare** -objektet.

```java
if (share.createIfNotExists()) {
    System.out.println("New share created");
}
```

I det här läget innehåller **resursen** en referens till en resurs med namnet **exempel resurs**.

---

## <a name="delete-a-file-share"></a>Ta bort en filresurs

Följande exempel kod tar bort en fil resurs.

# <a name="java-v12"></a>[Java-V12](#tab/java)

Ta bort en resurs genom att anropa metoden [ShareClient. Delete](/java/api/com.azure.storage.file.share.shareclient.delete) .

:::code language="java" source="~/azure-storage-snippets/files/howto/java/java-v12/files-howto-v12/src/main/java/com/files/howto/App.java" id="Snippet_deleteFileShare":::

# <a name="java-v11"></a>[Java-v11](#tab/java11)

Ta bort en resurs genom att anropa metoden **deleteIfExists** i ett **CloudFileShare** -objekt.

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount = CloudStorageAccount.parse(storageConnectionString);

    // Create the file client.
   CloudFileClient fileClient = storageAccount.createCloudFileClient();

   // Get a reference to the file share
   CloudFileShare share = fileClient.getShareReference("sampleshare");

   if (share.deleteIfExists()) {
       System.out.println("sampleshare deleted");
   }
} catch (Exception e) {
    e.printStackTrace();
}
```

---

## <a name="create-a-directory"></a>Skapa en katalog

Organisera lagring genom att lägga till filer i under kataloger i stället för att använda dem i rot katalogen.

# <a name="java-v12"></a>[Java-V12](#tab/java)

Följande kod skapar en katalog genom att anropa [ShareDirectoryClient. Create](/java/api/com.azure.storage.file.share.sharedirectoryclient.create). Metoden example returnerar ett `Boolean` värde som anger om katalogen har skapats.

:::code language="java" source="~/azure-storage-snippets/files/howto/java/java-v12/files-howto-v12/src/main/java/com/files/howto/App.java" id="Snippet_createDirectory":::

# <a name="java-v11"></a>[Java-v11](#tab/java11)

Följande kod skapar en under katalog med namnet **sampledir** under rot katalogen.

```java
//Get a reference to the root directory for the share.
CloudFileDirectory rootDir = share.getRootDirectoryReference();

//Get a reference to the sampledir directory
CloudFileDirectory sampleDir = rootDir.getDirectoryReference("sampledir");

if (sampleDir.createIfNotExists()) {
    System.out.println("sampledir created");
} else {
    System.out.println("sampledir already exists");
}
```

---

## <a name="delete-a-directory"></a>Ta bort en katalog

Att ta bort en katalog är en enkel uppgift. Du kan inte ta bort en katalog som fortfarande innehåller filer eller under kataloger.

# <a name="java-v12"></a>[Java-V12](#tab/java)

Metoden [ShareDirectoryClient. Delete](/java/api/com.azure.storage.file.share.sharedirectoryclient.delete) genererar ett undantag om katalogen inte finns eller inte är tom. Parkera samtalet och **ta bort** det i ett `try/catch` block och hantera undantaget.

:::code language="java" source="~/azure-storage-snippets/files/howto/java/java-v12/files-howto-v12/src/main/java/com/files/howto/App.java" id="Snippet_deleteDirectory":::

# <a name="java-v11"></a>[Java-v11](#tab/java11)

```java
// Get a reference to the root directory for the share.
CloudFileDirectory rootDir = share.getRootDirectoryReference();

// Get a reference to the directory you want to delete
CloudFileDirectory containerDir = rootDir.getDirectoryReference("sampledir");

// Delete the directory
if ( containerDir.deleteIfExists() ) {
    System.out.println("Directory deleted");
}
```

---

## <a name="enumerate-files-and-directories-in-an-azure-file-share"></a>Räkna upp filer och kataloger i en Azure-filresurs

# <a name="java-v12"></a>[Java-V12](#tab/java)

Hämta en lista över filer och kataloger genom att anropa [ShareDirectoryClient. listFilesAndDirectories](/java/api/com.azure.storage.file.share.sharedirectoryclient.listfilesanddirectories). Metoden returnerar en lista med [ShareFileItem](/java/api/com.azure.storage.file.share.models.sharefileitem) -objekt som du kan iterera. Följande kod listar filer och kataloger i katalogen som anges av parametern *logsdirectory* .

:::code language="java" source="~/azure-storage-snippets/files/howto/java/java-v12/files-howto-v12/src/main/java/com/files/howto/App.java" id="Snippet_enumerateFilesAndDirs":::

# <a name="java-v11"></a>[Java-v11](#tab/java11)

Hämta en lista över filer och kataloger genom att anropa **listFilesAndDirectories** på en **CloudFileDirectory** -referens. Metoden returnerar en lista med **ListFileItem** -objekt som du kan iterera. Följande kod listar filer och kataloger i rot katalogen.

```java
//Get a reference to the root directory for the share.
CloudFileDirectory rootDir = share.getRootDirectoryReference();

for ( ListFileItem fileItem : rootDir.listFilesAndDirectories() ) {
    System.out.println(fileItem.getUri());
}
```

---

## <a name="upload-a-file"></a>Ladda upp en fil

Lär dig hur du laddar upp en fil från lokal lagring.

# <a name="java-v12"></a>[Java-V12](#tab/java)

Följande kod laddar upp en lokal fil till Azure File Storage genom att anropa metoden [ShareFileClient. uploadFromFile](/java/api/com.azure.storage.file.share.sharefileclient.uploadfromfile) . Följande exempel metod returnerar ett `Boolean` värde som anger om den angivna filen har överförts.

:::code language="java" source="~/azure-storage-snippets/files/howto/java/java-v12/files-howto-v12/src/main/java/com/files/howto/App.java" id="Snippet_uploadFile":::

# <a name="java-v11"></a>[Java-v11](#tab/java11)

Hämta en referens till den katalog där filen laddas upp genom att anropa **getRootDirectoryReference** -metoden på Share-objektet.

```java
//Get a reference to the root directory for the share.
CloudFileDirectory rootDir = share.getRootDirectoryReference();
```

Nu när du har en referens till resursens rot Katalog kan du ladda upp en fil till den med hjälp av följande kod.

```java
// Define the path to a local file.
final String filePath = "C:\\temp\\Readme.txt";

CloudFile cloudFile = rootDir.getFileReference("Readme.txt");
cloudFile.uploadFromFile(filePath);
```

---

## <a name="download-a-file"></a>Ladda ned en fil

En av de vanligaste åtgärderna är att ladda ned filer från Azure Files lagring.

# <a name="java-v12"></a>[Java-V12](#tab/java)

I följande exempel hämtas den angivna filen till den lokala katalog som anges i parametern *destDir* . Exempel metoden gör det nedladdade fil namnet unikt genom att vänta på datum och tid.

:::code language="java" source="~/azure-storage-snippets/files/howto/java/java-v12/files-howto-v12/src/main/java/com/files/howto/App.java" id="Snippet_downloadFile":::

# <a name="java-v11"></a>[Java-v11](#tab/java11)

I följande exempel hämtas SampleFile.txt och innehållet visas.

```java
//Get a reference to the root directory for the share.
CloudFileDirectory rootDir = share.getRootDirectoryReference();

//Get a reference to the directory that contains the file
CloudFileDirectory sampleDir = rootDir.getDirectoryReference("sampledir");

//Get a reference to the file you want to download
CloudFile file = sampleDir.getFileReference("SampleFile.txt");

//Write the contents of the file to the console.
System.out.println(file.downloadText());
```

---

## <a name="delete-a-file"></a>Ta bort en fil

En annan vanlig Azure Files åtgärd är fil borttagning.

# <a name="java-v12"></a>[Java-V12](#tab/java)

Följande kod tar bort den angivna filen. Först skapar exemplet en [ShareDirectoryClient](/java/api/com.azure.storage.file.share.sharedirectoryclient) baserat på *logsdirectory* -parametern. Sedan hämtar koden en [ShareFileClient](/java/api/com.azure.storage.file.share.sharefileclient) från katalog klienten baserat på *fil namns* parametern. Slutligen anropar exempel metoden [ShareFileClient. Delete](/java/api/com.azure.storage.file.share.sharefileclient.delete) för att ta bort filen.

:::code language="java" source="~/azure-storage-snippets/files/howto/java/java-v12/files-howto-v12/src/main/java/com/files/howto/App.java" id="Snippet_deleteFile":::

# <a name="java-v11"></a>[Java-v11](#tab/java11)

Följande kod tar bort en fil med namnet SampleFile.txt som lagras i en katalog med namnet **sampledir**.

```java
// Get a reference to the root directory for the share.
CloudFileDirectory rootDir = share.getRootDirectoryReference();

// Get a reference to the directory where the file to be deleted is in
CloudFileDirectory containerDir = rootDir.getDirectoryReference("sampledir");

String filename = "SampleFile.txt"
CloudFile file;

file = containerDir.getFileReference(filename)
if ( file.deleteIfExists() ) {
    System.out.println(filename + " was deleted");
}
```

---

## <a name="next-steps"></a>Nästa steg

Om du vill veta mer om andra Azure Storage-API: er, följer du dessa länkar.

- [Azure för Java-utvecklare](/azure/developer/java)
- [Azure SDK för Java](https://github.com/azure/azure-sdk-for-java)
- [Azure SDK för Android](https://github.com/azure/azure-sdk-for-android)
- [Klient bibliotek för Azure-filresurs för Java SDK-referens](/java/api/overview/azure/storage-file-share-readme)
- [REST-API för Azure Storage Services](/rest/api/storageservices/)
- [Azure Storage teamets blogg](https://azure.microsoft.com/blog/topics/storage-backup-and-recovery/)
- [Överföra data med kommandoradsverktyget AzCopy](../common/storage-use-azcopy-v10.md)
- [Felsökning av problem i Azure Files – Windows](storage-troubleshoot-windows-file-connection-problems.md)
