---
title: 'Använd Java Script för att ange ACL: er i Azure Data Lake Storage Gen2'
description: Använd Azure Storage Data Lake klient bibliotek för Java Script för att hantera åtkomst kontrol listor (ACL) i lagrings konton med hierarkiskt namn område (HNS) aktiverat.
author: normesta
ms.service: storage
ms.date: 02/17/2021
ms.author: normesta
ms.topic: how-to
ms.subservice: data-lake-storage-gen2
ms.reviewer: prishet
ms.custom: devx-track-js
ms.openlocfilehash: 4d3e13c6593c0e11df84131a9a07eb2868277d2f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "100654484"
---
# <a name="use-javascript-to-manage-acls-in-azure-data-lake-storage-gen2"></a>Använd Java Script för att hantera ACL: er i Azure Data Lake Storage Gen2

Den här artikeln visar hur du använder Java Script för att hämta, ange och uppdatera åtkomst kontrol listorna för kataloger och filer. 

[Paket (Node Package Manager)](https://www.npmjs.com/package/@azure/storage-file-datalake)  |  [Exempel](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage/storage-file-datalake/samples)  |  [Ge feedback](https://github.com/Azure/azure-sdk-for-java/issues)

## <a name="prerequisites"></a>Förutsättningar

- En Azure-prenumeration. Se [Hämta en kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/pricing/free-trial/).

- Ett lagrings konto med hierarkiskt namn område (HNS) aktiverat. Följ [de här](create-data-lake-storage-account.md) anvisningarna för att skapa en.

- Azure CLI-version `2.6.0` eller högre.

- En av följande säkerhets behörigheter:

  - Ett etablerat Azure Active Directory (AD) [säkerhets objekt](../../role-based-access-control/overview.md#security-principal) som har tilldelats rollen som ägare av [lagrings-BLOB-data](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner) i omfånget för antingen mål behållaren, den överordnade resurs gruppen eller prenumerationen.  

  - Ägande användare av mål behållaren eller katalogen som du planerar att tillämpa ACL-inställningar på. Om du vill ange ACL: er rekursivt inkluderar detta alla underordnade objekt i mål behållaren eller katalogen.
  
  - Lagrings konto nyckel..

## <a name="set-up-your-project"></a>Konfigurera projektet

Installera Data Lake klient bibliotek för Java Script genom att öppna ett terminalfönster och sedan skriva in följande kommando.

```javascript
npm install @azure/storage-file-datalake
```

Importera `storage-file-datalake` paketet genom att placera den här instruktionen överst i din kod fil. 

```javascript
const AzureStorageDataLake = require("@azure/storage-file-datalake");
```

## <a name="connect-to-the-account"></a>Anslut till kontot

Om du vill använda kodfragmenten i den här artikeln måste du skapa en **DataLakeServiceClient** -instans som representerar lagrings kontot. 

### <a name="connect-by-using-azure-active-directory-ad"></a>Anslut med hjälp av Azure Active Directory (AD)

> [!NOTE]
> Om du använder Azure Active Directory (Azure AD) för att auktorisera åtkomst kontrollerar du att ditt säkerhets objekt har tilldelats rollen som ägare av [lagrings-BLOB-data](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner). Mer information om hur ACL-behörigheter tillämpas och effekterna av att ändra dem finns i  [åtkomst kontroll modell i Azure Data Lake Storage Gen2](./data-lake-storage-access-control-model.md).

Du kan använda [Azure Identity client-biblioteket för js för](https://www.npmjs.com/package/@azure/identity) att autentisera ditt program med Azure AD.

Hämta ett klient-ID, en klient hemlighet och ett klient-ID. Information om hur du gör detta finns i [Hämta en token från Azure AD för att auktorisera begär Anden från ett klient program](../common/storage-auth-aad-app.md). Som en del av den processen måste du tilldela en av följande roller för [Azure-rollbaserad åtkomst kontroll (Azure RBAC)](../../role-based-access-control/overview.md) till ditt säkerhets objekt. 

|Roll|Inställnings funktion för ACL|
|--|--|
|[Storage Blob Data-ägare](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner)|Alla kataloger och filer i kontot.|
|[Storage Blob Data-deltagare](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor)|Endast kataloger och filer som ägs av säkerhets objekt.|

I det här exemplet skapas en **DataLakeServiceClient** -instans med hjälp av ett klient-ID, en klient hemlighet och ett klient-ID.  

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
> Den här metoden för auktorisering fungerar bara för Node.js program. Om du planerar att köra din kod i en webbläsare kan du godkänna med hjälp av Azure Active Directory (AD).

## <a name="get-and-set-a-directory-acl"></a>Hämta och ange en katalog-ACL

Det här exemplet hämtar och anger sedan ACL för en katalog med namnet `my-directory` . Det här exemplet ger den ägande användaren Läs-, skriv-och körnings behörighet, ger den ägande gruppen endast Läs-och kör behörigheter och ger alla andra Läs behörighet.

> [!NOTE]
> Om ditt program tillåter åtkomst genom att använda Azure Active Directory (Azure AD) måste du kontrol lera att det säkerhets objekt som programmet använder för att auktorisera åtkomst har tilldelats rollen som [lagrings-BLOB-dataägare](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner). Mer information om hur ACL-behörigheter tillämpas och effekterna av att ändra dem finns i  [åtkomst kontroll i Azure Data Lake Storage Gen2](./data-lake-storage-access-control.md).

```javascript
async function ManageDirectoryACLs(fileSystemClient) {

    const directoryClient = fileSystemClient.getDirectoryClient("my-directory"); 
    const permissions = await directoryClient.getAccessControl();

    console.log(permissions.acl);

    const acl = [
    {
      accessControlType: "user",
      entityId: "",
      defaultScope: false,
      permissions: {
        read: true,
        write: true,
        execute: true
      }
    },
    {
      accessControlType: "group",
      entityId: "",
      defaultScope: false,
      permissions: {
        read: true,
        write: false,
        execute: true
      }
    },
    {
      accessControlType: "other",
      entityId: "",
      defaultScope: false,
      permissions: {
        read: true,
        write: true,
        execute: false
      }

    }

  ];

  await directoryClient.setAccessControl(acl);
}
```

Du kan också hämta och ange ACL: en för rot katalogen för en behållare. Om du vill hämta rot katalogen skickar du en tom sträng ( `/` ) till metoden **DataLakeFileSystemClient. getDirectoryClient** .

### <a name="get-and-set-a-file-acl"></a>Hämta och ange en fil-ACL

Det här exemplet hämtar och anger sedan ACL för en fil med namnet `upload-file.txt` . Det här exemplet ger den ägande användaren Läs-, skriv-och körnings behörighet, ger den ägande gruppen endast Läs-och kör behörigheter och ger alla andra Läs behörighet.

> [!NOTE]
> Om ditt program tillåter åtkomst genom att använda Azure Active Directory (Azure AD) måste du kontrol lera att det säkerhets objekt som programmet använder för att auktorisera åtkomst har tilldelats rollen som [lagrings-BLOB-dataägare](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner). Mer information om hur ACL-behörigheter tillämpas och effekterna av att ändra dem finns i  [åtkomst kontroll i Azure Data Lake Storage Gen2](./data-lake-storage-access-control.md).

```javascript
async function ManageFileACLs(fileSystemClient) {

  const fileClient = fileSystemClient.getFileClient("my-directory/uploaded-file.txt"); 
  const permissions = await fileClient.getAccessControl();

  console.log(permissions.acl);

  const acl = [
  {
    accessControlType: "user",
    entityId: "",
    defaultScope: false,
    permissions: {
      read: true,
      write: true,
      execute: true
    }
  },
  {
    accessControlType: "group",
    entityId: "",
    defaultScope: false,
    permissions: {
      read: true,
      write: false,
      execute: true
    }
  },
  {
    accessControlType: "other",
    entityId: "",
    defaultScope: false,
    permissions: {
      read: true,
      write: true,
      execute: false
    }

  }

];

await fileClient.setAccessControl(acl);        
}
```

## <a name="see-also"></a>Se även

- [Paket (Node Package Manager)](https://www.npmjs.com/package/@azure/storage-file-datalake)
- [Exempel](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage/storage-file-datalake/samples)
- [Lämna feedback](https://github.com/Azure/azure-sdk-for-java/issues)
- [Åtkomst kontroll modell i Azure Data Lake Storage Gen2](data-lake-storage-access-control.md)
- [Åtkomst kontrol listor (ACL: er) i Azure Data Lake Storage Gen2](data-lake-storage-access-control.md)