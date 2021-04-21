---
title: Skapa ett konto som stöder kund hanterade nycklar för tabeller och köer
titleSuffix: Azure Storage
description: Lär dig hur du skapar ett lagringskonto som stöder konfiguration av kund hanterade nycklar för tabeller och köer. Använd Azure CLI eller en Azure Resource Manager mall för att skapa ett lagringskonto som förlitar sig på kontots krypteringsnyckel för Azure Storage kryptering. Du kan sedan konfigurera kund hanterade nycklar för kontot.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 03/31/2021
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.custom: devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: 4c86811ee72d2713fced6320a17d1ccde1866d99
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107769957"
---
# <a name="create-an-account-that-supports-customer-managed-keys-for-tables-and-queues"></a>Skapa ett konto som stöder kund hanterade nycklar för tabeller och köer

Azure Storage krypterar alla data i ett vilokonto för lagring. Som standard använder Queue Storage och Table Storage en nyckel som är begränsad till tjänsten och hanteras av Microsoft. Du kan också välja att använda kund hanterade nycklar för att kryptera kö- eller tabelldata. Om du vill använda kund hanterade nycklar med köer och tabeller måste du först skapa ett lagringskonto som använder en krypteringsnyckel som är begränsad till kontot i stället för tjänsten. När du har skapat ett konto som använder kontokrypteringsnyckeln för kö- och tabelldata kan du konfigurera kund hanterade nycklar för det lagringskontot.

Den här artikeln beskriver hur du skapar ett lagringskonto som förlitar sig på en nyckel som är begränsad till kontot. När kontot först skapas använder Microsoft kontonyckeln för att kryptera data i kontot, och Microsoft hanterar nyckeln. Därefter kan du konfigurera kund hanterade nycklar för kontot för att dra nytta av dessa fördelar, inklusive möjligheten att tillhandahålla egna nycklar, uppdatera nyckelversionen, rotera nycklarna och återkalla åtkomstkontroller.

## <a name="create-an-account-that-uses-the-account-encryption-key"></a>Skapa ett konto som använder kontokrypteringsnyckeln

Du måste konfigurera ett nytt lagringskonto för att använda kontokrypteringsnyckeln för köer och tabeller när du skapar lagringskontot. Krypteringsnyckelns omfattning kan inte ändras när kontot har skapats.

Lagringskontot måste vara av typen generell användning v2. Du kan skapa lagringskontot och konfigurera det så att det förlitar sig på kontokrypteringsnyckeln med hjälp av antingen Azure CLI eller en Azure Resource Manager mall.

> [!NOTE]
> Endast Queue- och Table Storage kan konfigureras för att kryptera data med kontots krypteringsnyckel när lagringskontot skapas. Blob Storage och Azure Files alltid använda kontots krypteringsnyckel för att kryptera data.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Om du vill använda PowerShell för att skapa ett lagringskonto som förlitar sig på kontokrypteringsnyckeln kontrollerar du att du har installerat Azure PowerShell-modulen, version 3.4.0 eller senare. Mer information finns i [Installera Azure PowerShell modulen](/powershell/azure/install-az-ps).

Skapa sedan ett v2-lagringskonto för generell användning genom att anropa kommandot [New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount) med lämpliga parametrar:

- Inkludera alternativet `-EncryptionKeyTypeForQueue` och ange värdet till för att använda `Account` kontokrypteringsnyckeln för att kryptera data i Queue Storage.
- Inkludera alternativet `-EncryptionKeyTypeForTable` och ange värdet till för att använda `Account` kontokrypteringsnyckeln för att kryptera data i Table Storage.

I följande exempel visas hur du skapar ett v2-lagringskonto för generell användning som är konfigurerat för read-access geo-redundant storage (RA-GRS) och som använder kontokrypteringsnyckeln för att kryptera data för både Queue- och Table Storage. Kom ihåg att ersätta platshållarvärdena inom hakparentes med dina egna värden:

```powershell
New-AzStorageAccount -ResourceGroupName <resource_group> `
    -AccountName <storage-account> `
    -Location <location> `
    -SkuName "Standard_RAGRS" `
    -Kind StorageV2 `
    -EncryptionKeyTypeForTable Account `
    -EncryptionKeyTypeForQueue Account
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Om du vill använda Azure CLI för att skapa ett lagringskonto som förlitar sig på kontokrypteringsnyckeln kontrollerar du att du har installerat Azure CLI version 2.0.80 eller senare. Mer information finns i [Installera Azure CLI.](/cli/azure/install-azure-cli)

Skapa sedan ett v2-lagringskonto för generell användning genom att anropa [kommandot az storage account create](/cli/azure/storage/account#az_storage_account_create) med lämpliga parametrar:

- Inkludera alternativet `--encryption-key-type-for-queue` och ange värdet till för att använda `Account` kontokrypteringsnyckeln för att kryptera data i Queue Storage.
- Inkludera alternativet `--encryption-key-type-for-table` och ange värdet till för att använda `Account` kontots krypteringsnyckel för att kryptera data i Table Storage.

I följande exempel visas hur du skapar ett v2-lagringskonto för generell användning som är konfigurerat för read-access geo-redundant storage (RA-GRS) och som använder kontokrypteringsnyckeln för att kryptera data för både Queue- och Table Storage. Kom ihåg att ersätta platshållarvärdena inom hakparentes med dina egna värden:

```azurecli
az storage account create \
    --name <storage-account> \
    --resource-group <resource-group> \
    --location <location> \
    --sku Standard_RAGRS \
    --kind StorageV2 \
    --encryption-key-type-for-table Account \
    --encryption-key-type-for-queue Account
```

# <a name="template"></a>[Mall](#tab/template)

I följande JSON-exempel skapas ett v2-lagringskonto för generell användning som är konfigurerat för read-access geo-redundant storage (RA-GRS) och som använder kontokrypteringsnyckeln för att kryptera data för både Queue- och Table Storage. Kom ihåg att ersätta platshållarvärdena inom vinkelparenteser med dina egna värden:

```json
"resources": [
    {
        "type": "Microsoft.Storage/storageAccounts",
        "apiVersion": "2019-06-01",
        "name": "[parameters('<storage-account>')]",
        "location": "[parameters('<location>')]",
        "dependsOn": [],
        "tags": {},
        "sku": {
            "name": "[parameters('Standard_RAGRS')]"
        },
        "kind": "[parameters('StorageV2')]",
        "properties": {
            "accessTier": "[parameters('<accessTier>')]",
            "supportsHttpsTrafficOnly": "[parameters('supportsHttpsTrafficOnly')]",
            "largeFileSharesState": "[parameters('<largeFileSharesState>')]",
            "encryption": {
                "services": {
                    "queue": {
                        "keyType": "Account"
                    },
                    "table": {
                        "keyType": "Account"
                    }
                },
                "keySource": "Microsoft.Storage"
            }
        }
    }
],
```

---

När du har skapat ett konto som förlitar sig på kontokrypteringsnyckeln kan du konfigurera kund hanterade nycklar som lagras i Azure Key Vault eller i Key Vault Managed Hardware Security Model (HSM) (förhandsversion). Information om hur du lagrar kund hanterade nycklar i ett nyckelvalv finns i Konfigurera kryptering med kund hanterade nycklar som [lagras i Azure Key Vault](customer-managed-keys-configure-key-vault.md). Information om hur du lagrar kund hanterade nycklar i en hanterad HSM finns i Konfigurera kryptering med kund hanterade nycklar som lagras [i Azure Key Vault Managed HSM (förhandsversion).](customer-managed-keys-configure-key-vault-hsm.md)

## <a name="verify-the-account-encryption-key"></a>Verifiera kontokrypteringsnyckeln

Om du vill kontrollera att en tjänst i ett lagringskonto använder kontokrypteringsnyckeln anropar du kommandot Azure CLI [az storage account.](/cli/azure/storage/account#az_storage_account_show) Det här kommandot returnerar en uppsättning egenskaper för lagringskontot och deras värden. Leta efter fältet `keyType` för varje tjänst i krypteringsegenskapen och kontrollera att den är inställd på `Account` .

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Om du vill kontrollera att en tjänst i ett lagringskonto använder kontokrypteringsnyckeln anropar du kommandot [Get-AzStorageAccount.](/powershell/module/az.storage/get-azstorageaccount) Det här kommandot returnerar en uppsättning egenskaper för lagringskontot och deras värden. Leta efter `KeyType` fältet för varje tjänst i egenskapen och kontrollera att den är inställd på `Encryption` `Account` .

```powershell
$account = Get-AzStorageAccount -ResourceGroupName <resource-group> `
    -StorageAccountName <storage-account>
$account.Encryption.Services.Queue
$account.Encryption.Services.Table
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Om du vill kontrollera att en tjänst i ett lagringskonto använder kontokrypteringsnyckeln anropar du [kommandot az storage account show.](/cli/azure/storage/account#az_storage_account_show) Det här kommandot returnerar en uppsättning lagringskontoegenskaper och deras värden. Leta efter fältet `keyType` för varje tjänst i krypteringsegenskapen och kontrollera att den är inställd på `Account` .

```azurecli
az storage account show /
    --name <storage-account> /
    --resource-group <resource-group>
```

# <a name="template"></a>[Mall](#tab/template)

Ej tillämpligt

---

## <a name="pricing-and-billing"></a>Priser och fakturering

Ett lagringskonto som skapas för att använda en krypteringsnyckel som är begränsad till kontot debiteras för tabellagringskapacitet och transaktioner med en annan hastighet än ett konto som använder standardnyckeln för tjänstomfång. Mer information finns i [Priser för Azure Table Storage .](https://azure.microsoft.com/pricing/details/storage/tables/)

## <a name="next-steps"></a>Nästa steg

- [Azure Storage-kryptering av vilande data](storage-service-encryption.md)
- [Kund hanterade nycklar för Azure Storage kryptering](customer-managed-keys-overview.md)
- [Vad är Azure Key Vault?](../../key-vault/general/overview.md)