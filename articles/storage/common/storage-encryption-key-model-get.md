---
title: Avgöra vilken krypteringsnyckelmodell som används för lagringskontot
titleSuffix: Azure Storage
description: Använd Azure Portal, PowerShell eller Azure CLI för att kontrollera hur krypteringsnycklar hanteras för lagringskontot. Nycklar kan hanteras av Microsoft (standard) eller av kunden. Kund hanterade nycklar måste lagras i Azure Key Vault.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 03/13/2020
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.custom: devx-track-azurepowershell, devx-track-azurecli
ms.openlocfilehash: ef0f32ecc59bea6ee7a0f7ff12083fd2358c223c
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2021
ms.locfileid: "107478921"
---
# <a name="determine-which-azure-storage-encryption-key-model-is-in-use-for-the-storage-account"></a>Avgöra vilken Azure Storage krypteringsnyckelmodell som används för lagringskontot

Data i ditt lagringskonto krypteras automatiskt av Azure Storage. Azure Storage kryptering erbjuder två alternativ för att hantera krypteringsnycklar på lagringskontots nivå:

- **Microsoft-hanterade nycklar.** Som standard hanterar Microsoft de nycklar som används för att kryptera ditt lagringskonto.
- **Kund-hanterade nycklar.** Du kan också välja att hantera krypteringsnycklar för ditt lagringskonto. Kund hanterade nycklar måste lagras i Azure Key Vault.

Dessutom kan du ange en krypteringsnyckel på nivån för en enskild begäran för vissa Blob Storage-åtgärder. När en krypteringsnyckel anges på begäran åsidosätter den nyckeln krypteringsnyckeln som är aktiv på lagringskontot. Mer information finns i Ange [en kundtilldelade nyckel för en begäran till Blob Storage.](../blobs/storage-blob-customer-provided-key.md)

Mer information om krypteringsnycklar finns i [Azure Storage för vilodata.](storage-service-encryption.md)

## <a name="check-the-encryption-key-model-for-the-storage-account"></a>Kontrollera krypteringsnyckelmodellen för lagringskontot

Använd någon av följande metoder för att avgöra om ett lagringskonto använder Microsoft-hanterade nycklar eller kund hanterade nycklar för kryptering.

# <a name="azure-portal"></a>[Azure-portalen](#tab/portal)

Följ dessa steg om du vill kontrollera krypteringsmodellen för lagringskontot Azure Portal med hjälp av Azure Portal:

1. Navigera till ditt lagringskonto i Azure Portal.
1. Välj **krypteringsinställningen** och notera inställningen.

Följande bild visar ett lagringskonto som är krypterat med Microsoft-hanterade nycklar:

![Visa ett konto som är krypterat med Microsoft-hanterade nycklar](media/storage-encryption-key-model-get/microsoft-managed-encryption-key-setting-portal.png)

Och följande bild visar ett lagringskonto som är krypterat med kund hanterade nycklar:

![Skärmbild som visar inställningen för krypteringsnyckel i Azure Portal](media/storage-encryption-key-model-get/customer-managed-encryption-key-setting-portal.png)

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Om du vill kontrollera krypteringsmodellen för lagringskontot med hjälp av PowerShell anropar du kommandot [Get-AzStorageAccount](/powershell/module/az.storage/get-azstorageaccount) och kontrollerar **sedan keysource-egenskapen** för kontot.

```powershell
$account = Get-AzStorageAccount -ResourceGroupName <resource-group> `
    -Name <storage-account>
$account.Encryption.KeySource
```

Om värdet för egenskapen **KeySource** är `Microsoft.Storage` krypteras kontot med Microsoft-hanterade nycklar. Om värdet för **egenskapen KeySource** är krypteras kontot med kund `Microsoft.Keyvault` hanterade nycklar.

# <a name="azure-cli"></a>[Azure CLI](#tab/cli)

Om du vill kontrollera krypteringsmodellen för lagringskontot med hjälp av Azure CLI anropar du kommandot [az storage account show](/cli/azure/storage/account#az-storage-account-show) och kontrollerar sedan egenskapen **keySource** för kontot.

```azurecli-interactive
key_source=$(az storage account show \
    --name <storage-account> \
    --resource-group <resource-group> \
    --query encryption.keySource \
    --output tsv)
```

Om värdet för egenskapen **keySource** är `Microsoft.Storage` krypteras kontot med Microsoft-hanterade nycklar. Om värdet för **egenskapen keySource** är krypteras kontot med kund `Microsoft.Keyvault` hanterade nycklar.

---

## <a name="next-steps"></a>Nästa steg

- [Azure Storage-kryptering av vilande data](storage-service-encryption.md)
- [Kund-hanterade nycklar för Azure Storage kryptering](customer-managed-keys-overview.md)
