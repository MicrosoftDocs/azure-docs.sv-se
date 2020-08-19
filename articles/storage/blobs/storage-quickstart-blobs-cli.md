---
title: Snabb start – skapa en blob med Azure CLI
titleSuffix: Azure Storage
description: I den här snabb starten får du lära dig hur du använder Azure CLI för att ladda upp en blob till Azure Storage, laddar ned en blob och listar blobarna i en behållare.
services: storage
author: tamram
ms.service: storage
ms.subservice: blobs
ms.topic: quickstart
ms.date: 08/17/2020
ms.author: tamram
ms.custom: devx-track-azurecli
ms.openlocfilehash: 55cbf0a304bbf13d47fefad0981c0143c101bbb0
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88520778"
---
# <a name="quickstart-create-download-and-list-blobs-with-azure-cli"></a>Snabb start: skapa, ladda ned och lista blobar med Azure CLI

Azure CLI är Azures kommandoradsmiljö för att hantera Azure-resurser. Du kan använda det i din webbläsare med Azure Cloud Shell. Du kan även installera det på macOS, Linux eller Windows och köra det från kommandoraden. I den här snabbstarten lär du dig att använda Azure CLI för att ladda upp och ned data till och från Azure Blob Storage.

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

## <a name="prerequisites"></a>Förutsättningar

[!INCLUDE [storage-quickstart-prereq-include](../../../includes/storage-quickstart-prereq-include.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="install-the-azure-cli-locally"></a>Installera Azure CLI lokalt

Om du väljer att installera och använda Azure CLI lokalt kräver den här snabb starten att du kör Azure CLI-version 2.0.46 eller senare. Kör `az --version` för att fastställa din version. Om du behöver installera eller uppgradera kan du läsa informationen i [Installera Azure CLI](/cli/azure/install-azure-cli).

Om du kör Azure CLI lokalt måste du logga in och autentisera. Det här steget är inte nödvändigt om du använder Azure Cloud Shell. Logga in på Azure CLI genom att köra `az login` och autentisera i webbläsarfönstret:

```azurecli
az login
```

Mer information om autentisering med Azure CLI finns i [Logga in med Azure CLI](/cli/azure/authenticate-azure-cli).

## <a name="authorize-access-to-blob-storage"></a>Auktorisera åtkomst till Blob Storage

Du kan ge åtkomst till Blob Storage från Azure CLI antingen med Azure AD-autentiseringsuppgifter eller med hjälp av åtkomst nyckeln för lagrings kontot. Du rekommenderas att använda Azure AD-autentiseringsuppgifter. Den här artikeln visar hur du auktoriserar Blob Storage-åtgärder med hjälp av Azure AD.

Azure CLI-kommandon för data åtgärder mot Blob Storage har stöd för `--auth-mode` parametern, vilket gör att du kan ange hur en specifik åtgärd ska auktoriseras. Ange att `--auth-mode` parametern ska `login` auktoriseras med Azure AD-autentiseringsuppgifter. Mer information finns i [bevilja åtkomst till BLOB-eller Queue-data med Azure CLI](../common/authorize-data-operations-cli.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

Endast Blob Storage-data åtgärder stöder `--auth-mode` parametern. Hanterings åtgärder, till exempel att skapa en resurs grupp eller ett lagrings konto, använder automatiskt Azure AD-autentiseringsuppgifter för auktorisering.

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

Skapa en Azure-resursgrupp med kommandot [az group create](/cli/azure/group). En resursgrupp är en logisk container där Azure-resurser distribueras och hanteras.

Kom ihåg att ersätta plats hållarnas värden inom vinkelparenteser med dina egna värden:

```azurecli
az group create \
    --name <resource-group> \
    --location <location>
```

## <a name="create-a-storage-account"></a>Skapa ett lagringskonto

Skapa ett allmänt lagringskonto med kommandot [az storage account create](/cli/azure/storage/account). Det allmänna lagringskontot kan användas för alla fyra tjänsterna: blobar, filer, tabeller och köer.

Kom ihåg att ersätta plats hållarnas värden inom vinkelparenteser med dina egna värden:

```azurecli
az storage account create \
    --name <storage-account> \
    --resource-group <resource-group> \
    --location <location> \
    --sku Standard_ZRS \
    --encryption-services blob
```

## <a name="create-a-container"></a>Skapa en container

Blobar laddas alltid upp till en container. Du kan organisera grupper av blobbar i behållare på samma sätt som du ordnar dina filer på din dator i mappar. Skapa en behållare för att lagra blobar med kommandot [az storage container create](/cli/azure/storage/container).

I följande exempel används ditt Azure AD-konto för att auktorisera åtgärden att skapa behållaren. Innan du skapar behållaren ska du tilldela rollen [Storage BLOB data Contributor](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor) till dig själv. Även om du är kontots ägare behöver du explicita behörigheter för att utföra data åtgärder mot lagrings kontot. Mer information om hur du tilldelar Azure-roller finns i [använda Azure CLI för att tilldela en Azure-roll för åtkomst](../common/storage-auth-aad-rbac-cli.md?toc=/azure/storage/blobs/toc.json).  

Kom ihåg att ersätta plats hållarnas värden inom vinkelparenteser med dina egna värden:

```azurecli
az ad signed-in-user show --query objectId -o tsv | az role assignment create \
    --role "Storage Blob Data Contributor" \
    --assignee @- \
    --scope "/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>"

az storage container create \
    --account-name <storage-account> \
    --name <container> \
    --auth-mode login
```

> [!IMPORTANT]
> Det kan ta några minuter att sprida Azures roll tilldelningar.

Du kan också använda lagrings konto nyckeln för att ge åtgärden att skapa behållaren. Mer information om hur du auktoriserar data åtgärder med Azure CLI finns i [bevilja åtkomst till BLOB-eller köa data med Azure CLI](../common/authorize-data-operations-cli.md?toc=/azure/storage/blobs/toc.json).

## <a name="upload-a-blob"></a>Ladda upp en blob

Blob Storage stöder blockblobar, tilläggsblobar och sidblobar. I exemplen i den här snabb starten visas hur du arbetar med block-blobbar.

Börja med att skapa en fil som ska laddas upp till en Block-Blob. Om du använder Azure Cloud Shell använder du följande kommando för att skapa en fil:

```bash
vi helloworld
```

När filen öppnas trycker du på **Infoga**. Skriv *Hej världen*och tryck sedan på **ESC**. Skriv sedan *: x*och tryck på **RETUR**.

I det här exemplet laddar vi upp en blob till den container som vi skapade i det senaste steget med kommandot [az storage blob upload](/cli/azure/storage/blob). Det är inte nödvändigt att ange en fil Sök väg eftersom filen skapades i rot katalogen. Kom ihåg att ersätta plats hållarnas värden inom vinkelparenteser med dina egna värden:

```azurecli
az storage blob upload \
    --account-name <storage-account> \
    --container-name <container> \
    --name helloworld \
    --file helloworld \
    --auth-mode login
```

Den här åtgärden skapar bloben om den inte redan finns, och skriver över den om den finns. Ladda upp så många filer som du vill innan du fortsätter.

Om du vill ladda upp flera filer samtidigt kan du använda kommandot [az storage blob upload-batch](/cli/azure/storage/blob).

## <a name="list-the-blobs-in-a-container"></a>Visa en lista över blobarna i en container

Lista blobarna i containern med kommandot [az storage blob list](/cli/azure/storage/blob). Kom ihåg att ersätta plats hållarnas värden inom vinkelparenteser med dina egna värden:

```azurecli
az storage blob list \
    --account-name <storage-account> \
    --container-name <container> \
    --output table \
    --auth-mode login
```

## <a name="download-a-blob"></a>Ladda ned en blob

Använd kommandot [az storage blob download](/cli/azure/storage/blob) för att ladda ned den blob som du laddade upp tidigare. Kom ihåg att ersätta plats hållarnas värden inom vinkelparenteser med dina egna värden:

```azurecli
az storage blob download \
    --account-name <storage-account> \
    --container-name <container> \
    --name helloworld \
    --file ~/destination/path/for/file \
    --auth-mode login
```

## <a name="data-transfer-with-azcopy"></a>Dataöverföring med AzCopy

Kommando rads verktyget AzCopy erbjuder högpresterande, skript bara data överföring för Azure Storage. Du kan använda AzCopy för att överföra data till och från Blob Storage och Azure Files. För ytterligare information om AzCopy v10, den senaste versionen av AzCopy, se [Kom igång med AZCopy](../common/storage-use-azcopy-v10.md). Mer information om hur du använder AzCopy-v10 med Blob Storage finns i [överföra data med AzCopy och Blob Storage](../common/storage-use-azcopy-blobs.md).

I följande exempel används AzCopy för att ladda upp en lokal fil till en blob. Kom ihåg att ersätta exempel värden med dina egna värden:

```bash
azcopy login
azcopy copy 'C:\myDirectory\myTextFile.txt' 'https://mystorageaccount.blob.core.windows.net/mycontainer/myTextFile.txt'
```

## <a name="clean-up-resources"></a>Rensa resurser

Om du vill ta bort de resurser som du skapade som en del av den här snabb starten, inklusive lagrings kontot, tar du bort resurs gruppen med kommandot [AZ Group Delete](/cli/azure/group) . Kom ihåg att ersätta plats hållarnas värden inom vinkelparenteser med dina egna värden:

```azurecli
az group delete \
    --name <resource-group> \
    --no-wait
```

## <a name="next-steps"></a>Nästa steg

I den här snabb starten har du lärt dig hur du överför filer mellan ett lokalt fil system och en behållare i Azure Blob Storage. Om du vill veta mer om att arbeta med Blob Storage med hjälp av Azure CLI kan du utforska Azure CLI-exempel för Blob Storage.

> [!div class="nextstepaction"]
> [Azure CLI-exempel för Blob Storage](/azure/storage/blobs/storage-samples-blobs-cli?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
