---
title: Utveckla för Azure Files med Java | Microsoft Docs
description: Lär dig hur du utvecklar Java-program och -tjänster som använder Azure Files för att lagra fildata.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 11/18/2020
ms.custom: devx-track-java
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 115c55a5833906aa0dcc616a5b1b659468647282
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2021
ms.locfileid: "107814558"
---
# <a name="develop-for-azure-files-with-java"></a>Utveckla för Azure Files med Java

[!INCLUDE [storage-selector-file-include](../../../includes/storage-selector-file-include.md)]

Lär dig grunderna i att utveckla Java-program som använder Azure Files för att lagra data. Skapa ett konsolprogram och lär dig grundläggande åtgärder med hjälp Azure Files API:er:

- Skapa och ta bort Azure-filresurser
- Skapa och ta bort kataloger
- Räkna upp filer och kataloger i en Azure-filresurs
- Ladda upp, ladda ned och ta bort en fil

[!INCLUDE [storage-check-out-samples-java](../../../includes/storage-check-out-samples-java.md)]

## <a name="create-a-java-application"></a>Skapa ett Java-program

För att skapa exemplen behöver du Java Development Kit (JDK) och [Azure Storage SDK för Java](https://github.com/azure/azure-sdk-for-java). Du bör också ha skapat ett Azure Storage-konto.

## <a name="set-up-your-application-to-use-azure-files"></a>Konfigurera ditt program att använda Azure Files

Om du vill Azure Files API:er lägger du till följande kod överst i Java-filen som du vill komma åt Azure Files.

# <a name="azure-java-sdk-v12"></a>[Azure Java SDK v12](#tab/java)

:::code language="java" source="~/azure-storage-snippets/files/howto/java/java-v12/files-howto-v12/src/main/java/com/files/howto/App.java" id="Snippet_ImportStatements":::

# <a name="azure-java-sdk-v11"></a>[Azure Java SDK v11](#tab/java11)

```java
// Include the following imports to use Azure Files APIs v11
import com.microsoft.azure.storage.*;
import com.microsoft.azure.storage.file.*;
```

---

## <a name="set-up-an-azure-storage-connection-string"></a>Konfigurera en Anslutningssträng för Azure Storage

Om du Azure Files måste du ansluta till ditt Azure Storage-konto. Konfigurera en anslutningssträng och använd den för att ansluta till ditt lagringskonto. Definiera en statisk variabel som ska innehålla anslutningssträngen.

# <a name="azure-java-sdk-v12"></a>[Azure Java SDK v12](#tab/java)

Ersätt *\<storage_account_name\>* och med de faktiska värdena för ditt *\<storage_account_key\>* lagringskonto.

:::code language="java" source="~/azure-storage-snippets/files/howto/java/java-v12/files-howto-v12/src/main/java/com/files/howto/App.java" id="Snippet_ConnectionString":::

# <a name="azure-java-sdk-v11"></a>[Azure Java SDK v11](#tab/java11)

Ersätt *your_storage_account_name* och *your_storage_account_key* med de faktiska värdena för ditt lagringskonto.

```java
// Configure the connection-string with your values
public static final String storageConnectionString =
    "DefaultEndpointsProtocol=http;" +
    "AccountName=your_storage_account_name;" +
    "AccountKey=your_storage_account_key";
```

---

## <a name="access-an-azure-file-share"></a>Få åtkomst till en Azure-filresurs

# <a name="azure-java-sdk-v12"></a>[Azure Java SDK v12](#tab/java)

Skapa ett [ShareClient-Azure Files](/java/api/com.azure.storage.file.share.shareclient) åtkomst till objektet. Använd klassen [ShareClientBuilder för](/java/api/com.azure.storage.file.share.shareclientbuilder) att skapa ett nytt **ShareClient-objekt.**

:::code language="java" source="~/azure-storage-snippets/files/howto/java/java-v12/files-howto-v12/src/main/java/com/files/howto/App.java" id="Snippet_createClient":::

# <a name="azure-java-sdk-v11"></a>[Azure Java SDK v11](#tab/java11)

Du kommer åt ditt lagringskonto genom att använda **objektet CloudStorageAccount** och skicka anslutningssträngen till **dess parsningsmetod.**

```java
// Use the CloudStorageAccount object to connect to your storage account
try {
    CloudStorageAccount storageAccount = CloudStorageAccount.parse(storageConnectionString);
} catch (InvalidKeyException invalidKey) {
    // Handle the exception
}
```

**CloudStorageAccount.parse** kastar en InvalidKeyException så du måste placera den i ett try/catch-block.

---

## <a name="create-a-file-share"></a>Skapa en filresurs

Alla filer och kataloger i Azure Files lagras i en container som kallas en resurs.

# <a name="azure-java-sdk-v12"></a>[Azure Java SDK v12](#tab/java)

Metoden [ShareClient.create](/java/api/com.azure.storage.file.share.shareclient.create) kastar ett undantag om resursen redan finns. Placera anropet **för att** skapa `try/catch` i ett block och hantera undantaget.

:::code language="java" source="~/azure-storage-snippets/files/howto/java/java-v12/files-howto-v12/src/main/java/com/files/howto/App.java" id="Snippet_createFileShare":::

# <a name="azure-java-sdk-v11"></a>[Azure Java SDK v11](#tab/java11)

Om du vill få åtkomst till en resurs och dess innehåll skapar du Azure Files klient.

```java
// Create the Azure Files client.
CloudFileClient fileClient = storageAccount.createCloudFileClient();
```

Med hjälp Azure Files-klienten kan du hämta en referens till en resurs.

```java
// Get a reference to the file share
CloudFileShare share = fileClient.getShareReference("sampleshare");
```

Om du vill skapa resursen använder du **metoden createIfNotExists** för **objektet CloudFileShare.**

```java
if (share.createIfNotExists()) {
    System.out.println("New share created");
}
```

I det här läget innehåller **resursen** en referens till en resurs med namnet **sample share**.

---

## <a name="delete-a-file-share"></a>Ta bort en filresurs

Följande exempelkod tar bort en filresurs.

# <a name="azure-java-sdk-v12"></a>[Azure Java SDK v12](#tab/java)

Ta bort en resurs genom att anropa [metoden ShareClient.delete.](/java/api/com.azure.storage.file.share.shareclient.delete)

:::code language="java" source="~/azure-storage-snippets/files/howto/java/java-v12/files-howto-v12/src/main/java/com/files/howto/App.java" id="Snippet_deleteFileShare":::

# <a name="azure-java-sdk-v11"></a>[Azure Java SDK v11](#tab/java11)

Ta bort en resurs genom att anropa **metoden deleteIfExists** på ett **CloudFileShare-objekt.**

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

Organisera lagring genom att placera filer i underkataloger i stället för att ha dem i rotkatalogen.

# <a name="azure-java-sdk-v12"></a>[Azure Java SDK v12](#tab/java)

Följande kod skapar en katalog genom att anropa [ShareDirectoryClient.create](/java/api/com.azure.storage.file.share.sharedirectoryclient.create). Exempelmetoden returnerar ett `Boolean` värde som anger om katalogen har skapats.

:::code language="java" source="~/azure-storage-snippets/files/howto/java/java-v12/files-howto-v12/src/main/java/com/files/howto/App.java" id="Snippet_createDirectory":::

# <a name="azure-java-sdk-v11"></a>[Azure Java SDK v11](#tab/java11)

Följande kod skapar en underkatalog med namnet **sampledir** under rotkatalogen.

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

Det är enkelt att ta bort en katalog. Du kan inte ta bort en katalog som fortfarande innehåller filer eller underkataloger.

# <a name="azure-java-sdk-v12"></a>[Azure Java SDK v12](#tab/java)

Metoden [ShareDirectoryClient.delete](/java/api/com.azure.storage.file.share.sharedirectoryclient.delete) kastar ett undantag om katalogen inte finns eller inte är tom. Placera anropet **för att** ta bort `try/catch` i ett block och hantera undantaget.

:::code language="java" source="~/azure-storage-snippets/files/howto/java/java-v12/files-howto-v12/src/main/java/com/files/howto/App.java" id="Snippet_deleteDirectory":::

# <a name="azure-java-sdk-v11"></a>[Azure Java SDK v11](#tab/java11)

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

# <a name="azure-java-sdk-v12"></a>[Azure Java SDK v12](#tab/java)

Hämta en lista över filer och kataloger genom att anropa [ShareDirectoryClient.listFilesAndDirectories](/java/api/com.azure.storage.file.share.sharedirectoryclient.listfilesanddirectories). Metoden returnerar en lista över [ShareFileItem-objekt](/java/api/com.azure.storage.file.share.models.sharefileitem) som du kan iterera på. Följande kod listar filer och kataloger i katalogen som anges av *parametern dirName.*

:::code language="java" source="~/azure-storage-snippets/files/howto/java/java-v12/files-howto-v12/src/main/java/com/files/howto/App.java" id="Snippet_enumerateFilesAndDirs":::

# <a name="azure-java-sdk-v11"></a>[Azure Java SDK v11](#tab/java11)

Hämta en lista över filer och kataloger genom att anropa **listFilesAndDirectories** på en **CloudFileDirectory-referens.** Metoden returnerar en lista med **ListFileItem-objekt** som du kan iterera på. Följande kod listar filer och kataloger i rotkatalogen.

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

# <a name="azure-java-sdk-v12"></a>[Azure Java SDK v12](#tab/java)

Följande kod laddar upp en lokal fil till Azure File Storage genom att anropa metoden [ShareFileClient.uploadFromFile.](/java/api/com.azure.storage.file.share.sharefileclient.uploadfromfile) Följande exempelmetod returnerar ett `Boolean` värde som anger om den angivna filen har laddats upp.

:::code language="java" source="~/azure-storage-snippets/files/howto/java/java-v12/files-howto-v12/src/main/java/com/files/howto/App.java" id="Snippet_uploadFile":::

# <a name="azure-java-sdk-v11"></a>[Azure Java SDK v11](#tab/java11)

Hämta en referens till katalogen där filen ska laddas upp genom att anropa **metoden getRootDirectoryReference** för resursobjektet.

```java
//Get a reference to the root directory for the share.
CloudFileDirectory rootDir = share.getRootDirectoryReference();
```

Nu när du har en referens till resursens rotkatalog kan du ladda upp en fil till den med hjälp av följande kod.

```java
// Define the path to a local file.
final String filePath = "C:\\temp\\Readme.txt";

CloudFile cloudFile = rootDir.getFileReference("Readme.txt");
cloudFile.uploadFromFile(filePath);
```

---

## <a name="download-a-file"></a>Ladda ned en fil

En av de vanligaste åtgärderna är att ladda ned filer från en Azure-filresurs.

# <a name="azure-java-sdk-v12"></a>[Azure Java SDK v12](#tab/java)

I följande exempel laddas den angivna filen ned till den lokala katalogen som anges i *parametern destDir.* Exempelmetoden gör det nedladdade filnamnet unikt genom att förestående datum och tid.

:::code language="java" source="~/azure-storage-snippets/files/howto/java/java-v12/files-howto-v12/src/main/java/com/files/howto/App.java" id="Snippet_downloadFile":::

# <a name="azure-java-sdk-v11"></a>[Azure Java SDK v11](#tab/java11)

I följande exempel laddas SampleFile.txt ned och dess innehåll visas.

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

En annan vanlig Azure Files är filborttagning.

# <a name="azure-java-sdk-v12"></a>[Azure Java SDK v12](#tab/java)

Följande kod tar bort den angivna filen. Först skapar exemplet en [ShareDirectoryClient baserat](/java/api/com.azure.storage.file.share.sharedirectoryclient) på *dirName-parametern.* Sedan hämtar koden en [ShareFileClient från](/java/api/com.azure.storage.file.share.sharefileclient) katalogklienten, baserat på *parametern fileName.* Slutligen anropar exempelmetoden [ShareFileClient.delete för](/java/api/com.azure.storage.file.share.sharefileclient.delete) att ta bort filen.

:::code language="java" source="~/azure-storage-snippets/files/howto/java/java-v12/files-howto-v12/src/main/java/com/files/howto/App.java" id="Snippet_deleteFile":::

# <a name="azure-java-sdk-v11"></a>[Azure Java SDK v11](#tab/java11)

Följande kod tar bort en fil med namnet SampleFile.txt lagras i en katalog med namnet **sampledir**.

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

Om du vill veta mer om andra Azure Storage-API:er följer du dessa länkar.

- [Azure för Java-utvecklare](/azure/developer/java)
- [Azure SDK för Java](https://github.com/azure/azure-sdk-for-java)
- [Azure SDK för Android](https://github.com/azure/azure-sdk-for-android)
- [Azure-filresursklientbibliotek för Java SDK-referens](/java/api/overview/azure/storage-file-share-readme)
- [REST-API för Azure Storage Services](/rest/api/storageservices/)
- [Azure Storage teamblogg](https://azure.microsoft.com/blog/topics/storage-backup-and-recovery/)
- [Överföra data med kommandoradsverktyget AzCopy](../common/storage-use-azcopy-v10.md)
- [Felsökning av problem i Azure Files – Windows](storage-troubleshoot-windows-file-connection-problems.md)
