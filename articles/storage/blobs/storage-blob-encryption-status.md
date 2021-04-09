---
title: Kontrol lera krypterings statusen för en BLOB-Azure Storage
description: Lär dig hur du använder Azure Portal, PowerShell eller Azure CLI för att kontrol lera om en specifik BLOB är krypterad. Om en BLOB inte är krypterad, lär du dig att använda AzCopy för att tvinga kryptering genom att ladda ned och ladda upp bloben igen.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 11/26/2019
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.openlocfilehash: ee54357250e3f31ef9db633d933d897fff362f48
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "98878569"
---
# <a name="check-the-encryption-status-of-a-blob"></a>Kontrol lera krypterings statusen för en BLOB

Varje Block-Blob, Lägg till BLOB eller sid-blob som skrevs till Azure Storage efter den 20 oktober 2017 krypteras med Azure Storage kryptering. Blobbar som skapats före detta datum fortsätter att krypteras med en bakgrunds process.

Den här artikeln visar hur du avgör om en specifik BLOB har krypterats.

## <a name="check-a-blobs-encryption-status"></a>Kontrol lera krypterings status för en BLOB

Använd Azure Portal, PowerShell eller Azure CLI för att avgöra om en BLOB är krypterad utan kod.

### <a name="azure-portal"></a>[Azure-portalen](#tab/portal)

Följ dessa steg om du vill använda Azure Portal för att kontrol lera om en BLOB har krypterats:

1. Navigera till ditt lagringskonto i Azure Portal.
1. Välj **behållare** för att navigera till en lista över behållare i kontot.
1. Leta upp blobben och Visa fliken **Översikt** .
1. Visa **serverns krypterade** egenskap. Om **värdet är true**, som visas i följande bild, krypteras blobben. Observera att blobens egenskaper även innehåller datum och tid då blobben skapades.

    ![Skärm bild som visar hur du kontrollerar krypterad egenskap på servern i Azure Portal](media/storage-blob-encryption-status/blob-encryption-property-portal.png)

### <a name="powershell"></a>[PowerShell](#tab/powershell)

Om du vill använda PowerShell för att kontrol lera om en BLOB har krypterats kontrollerar du blobens **IsServerEncrypted** -egenskap. Kom ihåg att ersätta plats hållarnas värden inom vinkelparenteser med dina egna värden:

```powershell
$account = Get-AzStorageAccount -ResourceGroupName <resource-group> `
    -Name <storage-account>
$blob = Get-AzStorageBlob -Context $account.Context `
    -Container <container> `
    -Blob <blob>
$blob.ICloudBlob.Properties.IsServerEncrypted
```

För att fastställa när blobben skapades, kontrol lera värdet för den **skapade** egenskapen:

```powershell
$blob.ICloudBlob.Properties.IsServerEncrypted
```

### <a name="azure-cli"></a>[Azure CLI](#tab/cli)

Om du vill använda Azure CLI för att kontrol lera om en BLOB har krypterats kontrollerar du blobens **IsServerEncrypted** -egenskap. Kom ihåg att ersätta plats hållarnas värden inom vinkelparenteser med dina egna värden:

```azurecli-interactive
az storage blob show \
    --account-name <storage-account> \
    --container-name <container> \
    --name <blob> \
    --query "properties.serverEncrypted"
```

Om du vill fastställa när blobben skapades kontrollerar du värdet för den **skapade** egenskapen.

---

## <a name="force-encryption-of-a-blob"></a>Framtvinga kryptering av en BLOB

Om en blob som skapades före den 20 oktober 2017 ännu inte har krypterats med bakgrunds processen, kan du tvinga krypteringen att ske direkt genom att ladda ned och ladda upp bloben igen. Ett enkelt sätt att göra detta med AzCopy.

Om du vill ladda ned en blob till ditt lokala fil system med AzCopy, använder du följande syntax:

```
azcopy copy 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<blob-path>' '<local-file-path>'

Example:
azcopy copy 'https://storagesamples.blob.core.windows.net/sample-container/blob1.txt' 'C:\temp\blob1.txt'
```

Använd följande syntax för att överföra blobben igen till Azure Storage med AzCopy:

```
azcopy copy '<local-file-path>' 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<blob-name>'

Example:
azcopy copy 'C:\temp\blob1.txt' 'https://storagesamples.blob.core.windows.net/sample-container/blob1.txt'
```

Mer information om hur du använder AzCopy för att kopiera BLOB-data finns i [överföra data med AzCopy och Blob Storage](../common/storage-use-azcopy-v10.md#transfer-data).

## <a name="next-steps"></a>Nästa steg

[Azure Storage-kryptering av vilande data](../common/storage-service-encryption.md)
