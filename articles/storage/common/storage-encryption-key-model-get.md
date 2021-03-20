---
title: Avgöra vilken krypterings nyckel modell som används för lagrings kontot
titleSuffix: Azure Storage
description: Använd Azure Portal, PowerShell eller Azure CLI för att kontrol lera hur krypterings nycklar hanteras för lagrings kontot. Nycklar kan hanteras av Microsoft (standard) eller av kunden. Kundhanterade nycklar måste lagras i Azure Key Vault.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 03/13/2020
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: f59e4238c983cdb336500a68c52730ae5346b1c7
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "91612429"
---
# <a name="determine-which-azure-storage-encryption-key-model-is-in-use-for-the-storage-account"></a>Avgöra vilken Azure Storage krypterings nyckel modell som används för lagrings kontot

Data i ditt lagrings konto krypteras automatiskt av Azure Storage. Azure Storage kryptering erbjuder två alternativ för att hantera krypterings nycklar på lagrings kontots nivå:

- **Microsoft-hanterade nycklar.** Som standard hanterar Microsoft nycklarna som används för att kryptera ditt lagrings konto.
- **Kundhanterade nycklar.** Du kan också välja att hantera krypterings nycklar för ditt lagrings konto. Kundhanterade nycklar måste lagras i Azure Key Vault.

Dessutom kan du ange en krypterings nyckel på nivån för en enskild begäran för vissa Blob Storage-åtgärder. När en krypterings nyckel anges i begäran åsidosätter den krypterings nyckeln som är aktiv på lagrings kontot. Mer information finns i [Ange en kund-baserad nyckel på en begäran till Blob Storage](../blobs/storage-blob-customer-provided-key.md).

Mer information om krypterings nycklar finns [Azure Storage kryptering av data i vila](storage-service-encryption.md).

## <a name="check-the-encryption-key-model-for-the-storage-account"></a>Kontrol lera krypterings nyckel modellen för lagrings kontot

Använd någon av följande metoder för att avgöra om ett lagrings konto använder Microsoft-hanterade nycklar eller kund hanterade nycklar för kryptering.

# <a name="azure-portal"></a>[Azure-portalen](#tab/portal)

Följ dessa steg om du vill kontrol lera krypterings modellen för lagrings kontot med hjälp av Azure Portal:

1. Navigera till ditt lagringskonto i Azure Portal.
1. Välj **krypterings** inställningen och anteckna inställningen.

Följande bild visar ett lagrings konto som är krypterat med Microsoft-hanterade nycklar:

![Visa kontot krypterat med Microsoft-hanterade nycklar](media/storage-encryption-key-model-get/microsoft-managed-encryption-key-setting-portal.png)

Och följande bild visar ett lagrings konto som är krypterat med Kundhanterade nycklar:

![Skärm bild som visar krypterings nyckel inställningen i Azure Portal](media/storage-encryption-key-model-get/customer-managed-encryption-key-setting-portal.png)

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Om du vill kontrol lera krypterings modellen för lagrings kontot med hjälp av PowerShell anropar du kommandot [Get-AzStorageAccount](/powershell/module/az.storage/get-azstorageaccount) och kontrollerar sedan egenskapen nyckel **källa** för kontot.

```powershell
$account = Get-AzStorageAccount -ResourceGroupName <resource-group> `
    -Name <storage-account>
$account.Encryption.KeySource
```

Om värdet för egenskapen nyckel **källa** är `Microsoft.Storage` , krypteras kontot med Microsoft-hanterade nycklar. Om värdet för egenskapen nyckel **källa** är `Microsoft.Keyvault` , krypteras kontot med Kundhanterade nycklar.

# <a name="azure-cli"></a>[Azure CLI](#tab/cli)

Om du vill kontrol lera krypterings modellen för lagrings kontot med hjälp av Azure CLI anropar du kommandot [AZ Storage Account show](/cli/azure/storage/account#az-storage-account-show) och kontrollerar sedan egenskapen nyckel **källa** för kontot.

```azurecli-interactive
key_source=$(az storage account show \
    --name <storage-account> \
    --resource-group <resource-group> \
    --query encryption.keySource \
    --output tsv)
```

Om värdet för egenskapen nyckel **källa** är `Microsoft.Storage` , krypteras kontot med Microsoft-hanterade nycklar. Om värdet för egenskapen nyckel **källa** är `Microsoft.Keyvault` , krypteras kontot med Kundhanterade nycklar.

---

## <a name="next-steps"></a>Nästa steg

- [Azure Storage-kryptering av vilande data](storage-service-encryption.md)
- [Kundhanterade nycklar för Azure Storage kryptering](customer-managed-keys-overview.md)
