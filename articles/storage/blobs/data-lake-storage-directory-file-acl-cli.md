---
title: Använd Azure CLI för att hantera data (Azure Data Lake Storage Gen2)
description: Använd Azure CLI för att hantera kataloger och filer i lagrings konton som har ett hierarkiskt namn område.
services: storage
author: normesta
ms.service: storage
ms.subservice: data-lake-storage-gen2
ms.topic: how-to
ms.date: 02/17/2021
ms.author: normesta
ms.reviewer: prishet
ms.custom: devx-track-azurecli
ms.openlocfilehash: 3e9afd4617eb7445ba83948d46eef0890832e2be
ms.sourcegitcommit: 227b9a1c120cd01f7a39479f20f883e75d86f062
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/18/2021
ms.locfileid: "100650362"
---
# <a name="use-azure-cli-to-manage-directories-and-files-in-azure-data-lake-storage-gen2"></a>Använd Azure CLI för att hantera kataloger och filer i Azure Data Lake Storage Gen2

Den här artikeln visar hur du använder [CLI (Azure Command-Line Interface)](/cli/azure/) för att skapa och hantera kataloger och filer i lagrings konton som har ett hierarkiskt namn område.

Information om hur du hämtar, anger och uppdaterar åtkomst kontrol listorna (ACL) för kataloger och filer finns i [använda Azure CLI för att hantera ACL: er i Azure Data Lake Storage Gen2](data-lake-storage-acl-cli.md).

[Exempel](https://github.com/Azure/azure-cli/blob/dev/src/azure-cli/azure/cli/command_modules/storage/docs/ADLS%20Gen2.md)  |  [Ge feedback](https://github.com/Azure/azure-cli-extensions/issues)

## <a name="prerequisites"></a>Förutsättningar

- En Azure-prenumeration. Se [Hämta en kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/pricing/free-trial/).

- Ett lagrings konto med hierarkiskt namn område aktiverat. Följ [de här](create-data-lake-storage-account.md) anvisningarna för att skapa en.

- Azure CLI-version `2.6.0` eller högre.

## <a name="ensure-that-you-have-the-correct-version-of-azure-cli-installed"></a>Kontrol lera att du har rätt version av Azure CLI installerad

1. Öppna [Azure Cloud Shell](../../cloud-shell/overview.md), eller om du har [installerat](/cli/azure/install-azure-cli) Azure CLI lokalt öppnar du ett kommando konsol program, till exempel Windows PowerShell.

2. Kontrol lera att den version av Azure CLI som har installerats är `2.6.0` eller högre genom att använda följande kommando.

   ```azurecli
    az --version
   ```

   Om din version av Azure CLI är lägre än `2.6.0` kan du installera en senare version. Se [Installera Azure CLI](/cli/azure/install-azure-cli).

## <a name="connect-to-the-account"></a>Anslut till kontot

1. Om du använder Azure CLI lokalt kan du köra inloggnings kommandot.

   ```azurecli
   az login
   ```

   Om CLI kan öppna din standard webbläsare så gör den det och läser in en Azure-inloggnings sida.

   Annars öppnar du en webb sida på [https://aka.ms/devicelogin](https://aka.ms/devicelogin) och anger den auktoriseringskod som visas i din terminal. Logga sedan in med dina konto uppgifter i webbläsaren.

   Mer information om olika autentiseringsmetoder finns i [bevilja åtkomst till BLOB-eller Queue-data med Azure CLI](./authorize-data-operations-cli.md).

2. Om din identitet är associerad med fler än en prenumeration ställer du in din aktiva prenumeration på prenumerationen på det lagrings konto som ska vara värd för din statiska webbplats.

   ```azurecli
   az account set --subscription <subscription-id>
   ```

   Ersätt `<subscription-id>` placeholder-värdet med ID: t för din prenumeration.

> [!NOTE]
> Exemplet som presenteras i den här artikeln visar Azure Active Directory (Azure AD)-auktorisering. Mer information om autentiseringsmetoder finns i [bevilja åtkomst till BLOB-eller Queue-data med Azure CLI](./authorize-data-operations-cli.md).

## <a name="create-a-container"></a>Skapa en container

En behållare fungerar som ett fil system för dina filer. Du kan skapa en med hjälp av `az storage fs create` kommandot. 

I det här exemplet skapas en behållare med namnet `my-file-system` .

```azurecli
az storage fs create -n my-file-system --account-name mystorageaccount --auth-mode login
```

## <a name="show-container-properties"></a>Visa behållar egenskaper

Du kan skriva ut egenskaperna för en behållare till-konsolen med hjälp av `az storage fs show` kommandot.

```azurecli
az storage fs show -n my-file-system --account-name mystorageaccount --auth-mode login
```

## <a name="list-container-contents"></a>Lista behållarens innehåll

Lista innehållet i en katalog med hjälp av `az storage fs file list` kommandot.

I det här exemplet visas innehållet i en behållare med namnet `my-file-system` .

```azurecli
az storage fs file list -f my-file-system --account-name mystorageaccount --auth-mode login
```

## <a name="delete-a-container"></a>Ta bort en container

Ta bort en behållare med hjälp av `az storage fs delete` kommandot.

I det här exemplet tas en behållare med namnet `my-file-system` . 

```azurecli
az storage fs delete -n my-file-system --account-name mystorageaccount --auth-mode login
```

## <a name="create-a-directory"></a>Skapa en katalog

Skapa en katalog referens med hjälp av `az storage fs directory create` kommandot. 

I det här exemplet läggs en katalog `my-directory` till som heter i en behållare med namnet `my-file-system` som finns i ett konto med namnet `mystorageaccount` .

```azurecli
az storage fs directory create -n my-directory -f my-file-system --account-name mystorageaccount --auth-mode login
```

## <a name="show-directory-properties"></a>Visa katalog egenskaper

Du kan skriva ut egenskaperna för en katalog till-konsolen med hjälp av `az storage fs directory show` kommandot.

```azurecli
az storage fs directory show -n my-directory -f my-file-system --account-name mystorageaccount --auth-mode login
```

## <a name="rename-or-move-a-directory"></a>Byta namn på eller flytta en katalog

Byt namn på eller flytta en katalog med hjälp av `az storage fs directory move` kommandot.

I det här exemplet byter namn på en katalog från namnet `my-directory` till namnet `my-new-directory` i samma behållare.

```azurecli
az storage fs directory move -n my-directory -f my-file-system --new-directory "my-file-system/my-new-directory" --account-name mystorageaccount --auth-mode login
```

I det här exemplet flyttas en katalog till en behållare med namnet `my-second-file-system` .

```azurecli
az storage fs directory move -n my-directory -f my-file-system --new-directory "my-second-file-system/my-new-directory" --account-name mystorageaccount --auth-mode login
```

## <a name="delete-a-directory"></a>Ta bort en katalog

Ta bort en katalog med hjälp av `az storage fs directory delete` kommandot.

Det här exemplet tar bort en katalog med namnet `my-directory` . 

```azurecli
az storage fs directory delete -n my-directory -f my-file-system  --account-name mystorageaccount --auth-mode login 
```

## <a name="check-if-a-directory-exists"></a>Kontrol lera om en katalog finns

Ta reda på om en angiven katalog finns i behållaren med hjälp av `az storage fs directory exists` kommandot.

Det här exemplet visar om en katalog med namnet `my-directory` finns i `my-file-system` behållaren. 

```azurecli
az storage fs directory exists -n my-directory -f my-file-system --account-name mystorageaccount --auth-mode login 
```

## <a name="download-from-a-directory"></a>Ladda ned från en katalog

Hämta en fil från en katalog med hjälp av `az storage fs file download` kommandot.

I det här exemplet hämtas en fil med namnet `upload.txt` från en katalog med namnet `my-directory` . 

```azurecli
az storage fs file download -p my-directory/upload.txt -f my-file-system -d "C:\myFolder\download.txt" --account-name mystorageaccount --auth-mode login
```

## <a name="list-directory-contents"></a>Lista kataloginnehåll

Lista innehållet i en katalog med hjälp av `az storage fs file list` kommandot.

I det här exemplet visas innehållet i en katalog med namnet `my-directory` som finns i `my-file-system` behållaren för ett lagrings konto med namnet `mystorageaccount` . 

```azurecli
az storage fs file list -f my-file-system --path my-directory --account-name mystorageaccount --auth-mode login
```

## <a name="upload-a-file-to-a-directory"></a>Ladda upp en fil till en katalog

Ladda upp en fil till en katalog med hjälp av `az storage fs directory upload` kommandot.

I det här exemplet överförs en fil med namnet `upload.txt` till en katalog med namnet `my-directory` . 

```azurecli
az storage fs file upload -s "C:\myFolder\upload.txt" -p my-directory/upload.txt  -f my-file-system --account-name mystorageaccount --auth-mode login
```

## <a name="show-file-properties"></a>Visa fil egenskaper

Du kan skriva ut egenskaperna för en fil till-konsolen med hjälp av `az storage fs file show` kommandot.

```azurecli
az storage fs file show -p my-file.txt -f my-file-system --account-name mystorageaccount --auth-mode login
```

## <a name="rename-or-move-a-file"></a>Byta namn på eller flytta en fil

Byt namn på eller flytta en fil med hjälp av `az storage fs file move` kommandot.

I det här exemplet byter namn på en fil från namnet `my-file.txt` till namnet `my-file-renamed.txt` .

```azurecli
az storage fs file move -p my-file.txt -f my-file-system --new-path my-file-system/my-file-renamed.txt --account-name mystorageaccount --auth-mode login
```

## <a name="delete-a-file"></a>Ta bort en fil

Ta bort en fil med hjälp av `az storage fs file delete` kommandot.

Det här exemplet tar bort en fil med namnet `my-file.txt`

```azurecli
az storage fs file delete -p my-directory/my-file.txt -f my-file-system  --account-name mystorageaccount --auth-mode login 
```

## <a name="see-also"></a>Se även

- [Exempel](https://github.com/Azure/azure-cli/blob/dev/src/azure-cli/azure/cli/command_modules/storage/docs/ADLS%20Gen2.md)
- [Lämna feedback](https://github.com/Azure/azure-cli-extensions/issues)
- [Kända problem](data-lake-storage-known-issues.md#api-scope-data-lake-client-library)
- [Använd Azure CLI för att hantera ACL: er i Azure Data Lake Storage Gen2](data-lake-storage-acl-cli.md)