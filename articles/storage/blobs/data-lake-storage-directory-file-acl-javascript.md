---
title: Använd Java Script (Node.js) för att hantera data i Azure Data Lake Storage Gen2
description: Använd Azure Storage Data Lake klient bibliotek för Java Script för att hantera kataloger och filer i lagrings konton med hierarkiskt namn område aktiverat.
author: normesta
ms.service: storage
ms.date: 03/19/2021
ms.author: normesta
ms.topic: how-to
ms.subservice: data-lake-storage-gen2
ms.reviewer: prishet
ms.custom: devx-track-js
ms.openlocfilehash: 678af3e2fb4111593ece0cc2cdf3811cf0e793a8
ms.sourcegitcommit: 2c1b93301174fccea00798df08e08872f53f669c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/22/2021
ms.locfileid: "104774770"
---
# <a name="use-javascript-sdk-in-nodejs-to-manage-directories-and-files-in-azure-data-lake-storage-gen2"></a>Använd JavaScript SDK i Node.js för att hantera kataloger och filer i Azure Data Lake Storage Gen2

Den här artikeln visar hur du använder Node.js för att skapa och hantera kataloger och filer i lagrings konton som har ett hierarkiskt namn område.

Information om hur du hämtar, anger och uppdaterar åtkomst kontrol listorna (ACL) för kataloger och filer finns i [använda JavaScript SDK i Node.js för att hantera ACL: er i Azure Data Lake Storage Gen2](data-lake-storage-acl-javascript.md).

[Paket (Node Package Manager)](https://www.npmjs.com/package/@azure/storage-file-datalake)  |  [Exempel](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage/storage-file-datalake/samples)  |  [Ge feedback](https://github.com/Azure/azure-sdk-for-java/issues)

## <a name="prerequisites"></a>Förutsättningar

- En Azure-prenumeration. Se [Hämta en kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/pricing/free-trial/).

- Ett lagrings konto med hierarkiskt namn område aktiverat. Följ [de här](create-data-lake-storage-account.md) anvisningarna för att skapa en.

- Om du använder det här paketet i ett Node.js-program behöver du Node.js 8.0.0 eller senare.

## <a name="set-up-your-project"></a>Konfigurera projektet

Installera Data Lake klient bibliotek för Java Script genom att öppna ett terminalfönster och sedan skriva in följande kommando.

```javascript
npm install @azure/storage-file-datalake
```

Importera `storage-file-datalake` paketet genom att placera den här instruktionen överst i din kod fil. 

```javascript
const {
AzureStorageDataLake,
DataLakeServiceClient,
StorageSharedKeyCredential
} = require("@azure/storage-file-datalake");
```

## <a name="connect-to-the-account"></a>Anslut till kontot 

Om du vill använda kodfragmenten i den här artikeln måste du skapa en **DataLakeServiceClient** -instans som representerar lagrings kontot. 

### <a name="connect-by-using-an-account-key"></a>Anslut med hjälp av en konto nyckel

Detta är det enklaste sättet att ansluta till ett konto. 

I det här exemplet skapas en **DataLakeServiceClient** -instans med hjälp av en konto nyckel.

```javascript

function GetDataLakeServiceClient(accountName, accountKey) {

  const sharedKeyCredential = 
     new StorageSharedKeyCredential(accountName, accountKey);
  
  const datalakeServiceClient = new DataLakeServiceClient(
      `https://${accountName}.dfs.core.windows.net`, sharedKeyCredential);

  return datalakeServiceClient;             
}      

```

> [!NOTE]
> Den här metoden för auktorisering fungerar bara för Node.js program. Om du planerar att köra din kod i en webbläsare kan du auktorisera genom att använda Azure Active Directory (Azure AD).

### <a name="connect-by-using-azure-active-directory-azure-ad"></a>Anslut med hjälp av Azure Active Directory (Azure AD)

Du kan använda [Azure Identity client-biblioteket för js för](https://www.npmjs.com/package/@azure/identity) att autentisera ditt program med Azure AD.

I det här exemplet skapas en **DataLakeServiceClient** -instans med hjälp av ett klient-ID, en klient hemlighet och ett klient-ID.  Om du vill hämta dessa värden läser du [Hämta en token från Azure AD för att auktorisera begär Anden från ett klient program](../common/storage-auth-aad-app.md).

```javascript
function GetDataLakeServiceClientAD(accountName, clientID, clientSecret, tenantID) {

  const credential = new ClientSecretCredential(tenantID, clientID, clientSecret);
  
  const datalakeServiceClient = new DataLakeServiceClient(
      `https://${accountName}.dfs.core.windows.net`, credential);

  return datalakeServiceClient;             
}
```

> [!NOTE]
> Fler exempel finns i dokumentationen [för Azure Identity Client library för JS](https://www.npmjs.com/package/@azure/identity) .

## <a name="create-a-container"></a>Skapa en container

En behållare fungerar som ett fil system för dina filer. Du kan skapa en genom att hämta en **FileSystemClient** -instans och sedan anropa metoden **FileSystemClient. Create** .

I det här exemplet skapas en behållare med namnet `my-file-system` . 

```javascript
async function CreateFileSystem(datalakeServiceClient) {

  const fileSystemName = "my-file-system";
  
  const fileSystemClient = datalakeServiceClient.getFileSystemClient(fileSystemName);

  const createResponse = await fileSystemClient.create();
        
}
```

## <a name="create-a-directory"></a>Skapa en katalog

Skapa en katalog referens genom att hämta en **DirectoryClient** -instans och anropa sedan metoden **DirectoryClient. Create** .

Det här exemplet lägger till en katalog med namnet `my-directory` i en behållare. 

```javascript
async function CreateDirectory(fileSystemClient) {
   
  const directoryClient = fileSystemClient.getDirectoryClient("my-directory");
  
  await directoryClient.create();

}
```

## <a name="rename-or-move-a-directory"></a>Byta namn på eller flytta en katalog

Byt namn på eller flytta en katalog genom att anropa metoden **DirectoryClient. Rename** . Skicka sökvägen till önskad katalog en parameter. 

I det här exemplet byter namn på en under katalog till namnet `my-directory-renamed` .

```javascript
async function RenameDirectory(fileSystemClient) {

  const directoryClient = fileSystemClient.getDirectoryClient("my-directory"); 
  await directoryClient.move("my-directory-renamed");

}
```

I det här exemplet flyttas en katalog med namnet `my-directory-renamed` till en under katalog till en katalog med namnet `my-directory-2` . 

```javascript
async function MoveDirectory(fileSystemClient) {

  const directoryClient = fileSystemClient.getDirectoryClient("my-directory-renamed"); 
  await directoryClient.move("my-directory-2/my-directory-renamed");      

}
```

## <a name="delete-a-directory"></a>Ta bort en katalog

Ta bort en katalog genom att anropa metoden **DirectoryClient. Delete** .

Det här exemplet tar bort en katalog med namnet `my-directory` .   

```javascript
async function DeleteDirectory(fileSystemClient) {

  const directoryClient = fileSystemClient.getDirectoryClient("my-directory"); 
  await directoryClient.delete();

}
```

## <a name="upload-a-file-to-a-directory"></a>Ladda upp en fil till en katalog

Börja med att läsa en fil. I det här exemplet används `fs` modulen Node.js. Skapa sedan en fil referens i mål katalogen genom att skapa en **FileClient** -instans och sedan anropa metoden **FileClient. Create** . Ladda upp en fil genom att anropa metoden **FileClient. append** . Se till att slutföra överföringen genom att anropa metoden **FileClient. flush** .

I det här exemplet överförs en textfil till en katalog med namnet `my-directory` .

```javascript
async function UploadFile(fileSystemClient) {

  const fs = require('fs') 

  var content = "";
  
  fs.readFile('mytestfile.txt', (err, data) => { 
      if (err) throw err; 

      content = data.toString();

  }) 
  
  const fileClient = fileSystemClient.getFileClient("my-directory/uploaded-file.txt");
  await fileClient.create();
  await fileClient.append(content, 0, content.length);
  await fileClient.flush(content.length);

}
```

## <a name="download-from-a-directory"></a>Ladda ned från en katalog

Börja med att skapa en **FileSystemClient** -instans som representerar den fil som du vill ladda ned. Använd metoden **FileSystemClient. Read** för att läsa filen. Skriv sedan filen. I det här exemplet används Node.js- `fs` modulen för att göra det. 

> [!NOTE]
> Den här metoden för att hämta en fil fungerar bara för Node.js program. Om du planerar att köra din kod i en webbläsare, se filen [Azure Storage fil data Lake klient bibliotek för Java Script](https://www.npmjs.com/package/@azure/storage-file-datalake) för ett exempel på hur du gör detta i en webbläsare.

```javascript
async function DownloadFile(fileSystemClient) {

  const fileClient = fileSystemClient.getFileClient("my-directory/uploaded-file.txt");

  const downloadResponse = await fileClient.read();

  const downloaded = await streamToString(downloadResponse.readableStreamBody);
 
  async function streamToString(readableStream) {
    return new Promise((resolve, reject) => {
      const chunks = [];
      readableStream.on("data", (data) => {
        chunks.push(data.toString());
      });
      readableStream.on("end", () => {
        resolve(chunks.join(""));
      });
      readableStream.on("error", reject);
    });
  }   
  
  const fs = require('fs');

  fs.writeFile('mytestfiledownloaded.txt', downloaded, (err) => {
    if (err) throw err;
  });
}

```

## <a name="list-directory-contents"></a>Lista kataloginnehåll

Det här exemplet skriver ut namnen på varje katalog och fil som finns i en katalog med namnet `my-directory` .

```javascript
async function ListFilesInDirectory(fileSystemClient) {
  
  let i = 1;

  let iter = await fileSystemClient.listPaths({path: "my-directory", recursive: true});

  for await (const path of iter) {
    
    console.log(`Path ${i++}: ${path.name}, is directory: ${path.isDirectory}`);
  }

}
```

## <a name="see-also"></a>Se även

- [Paket (Node Package Manager)](https://www.npmjs.com/package/@azure/storage-file-datalake)
- [Exempel](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage/storage-file-datalake/samples)
- [Ge feedback](https://github.com/Azure/azure-sdk-for-java/issues)