---
title: Skapa ett konto som stöder Kundhanterade nycklar för tabeller och köer
titleSuffix: Azure Storage
description: Lär dig hur du skapar ett lagrings konto som stöder konfigurering av Kundhanterade nycklar för tabeller och köer. Använd Azure CLI eller en Azure Resource Manager mall för att skapa ett lagrings konto som förlitar sig på konto krypterings nyckeln för Azure Storage kryptering. Du kan sedan konfigurera Kundhanterade nycklar för kontot.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 03/31/2021
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.custom: devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: f2bc71100a92d1811d69af31a7a3085af36f60a8
ms.sourcegitcommit: 9f4510cb67e566d8dad9a7908fd8b58ade9da3b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/01/2021
ms.locfileid: "106121939"
---
# <a name="create-an-account-that-supports-customer-managed-keys-for-tables-and-queues"></a>Skapa ett konto som stöder Kundhanterade nycklar för tabeller och köer

Azure Storage krypterar alla data i ett lagrings konto i vila. Som standard använder Queue Storage och table Storage en nyckel som är begränsad till tjänsten och hanteras av Microsoft. Du kan också välja att använda Kundhanterade nycklar för att kryptera kö-eller tabell data. Om du vill använda Kundhanterade nycklar med köer och tabeller måste du först skapa ett lagrings konto som använder en krypterings nyckel som är begränsad till kontot, i stället för till tjänsten. När du har skapat ett konto som använder konto krypterings nyckeln för kö-och tabell data kan du konfigurera Kundhanterade nycklar för lagrings kontot.

Den här artikeln beskriver hur du skapar ett lagrings konto som förlitar sig på en nyckel som är begränsad till kontot. När kontot först skapas använder Microsoft konto nyckeln för att kryptera data i kontot och Microsoft hanterar nyckeln. Du kan sedan konfigurera Kundhanterade nycklar för kontot för att dra nytta av dessa förmåner, inklusive möjligheten att tillhandahålla egna nycklar, uppdatera nyckel versionen, rotera nycklarna och återkalla åtkomst kontroller.

## <a name="create-an-account-that-uses-the-account-encryption-key"></a>Skapa ett konto som använder krypterings nyckeln för kontot

Du måste konfigurera ett nytt lagrings konto för att kunna använda kontots krypterings nyckel för köer och tabeller vid den tidpunkt då du skapar lagrings kontot. Det går inte att ändra omfånget för krypterings nyckeln när kontot har skapats.

Lagrings kontot måste vara av typen General-Purpose v2. Du kan skapa lagrings kontot och konfigurera det så att det förlitar sig på konto krypterings nyckeln med hjälp av antingen Azure CLI eller en Azure Resource Manager mall.

> [!NOTE]
> Det går bara att konfigurera kö-och tabell lagring för att kryptera data med konto krypterings nyckeln när lagrings kontot skapas. Blob Storage och Azure Files alltid använda kontots krypterings nyckel för att kryptera data.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Om du vill använda PowerShell för att skapa ett lagrings konto som förlitar sig på konto krypterings nyckeln kontrollerar du att du har installerat Azure PowerShell-modulen, version 3.4.0 eller senare. Mer information finns i [installera Azure PowerShell-modulen](/powershell/azure/install-az-ps).

Skapa sedan ett allmänt-syfte v2-lagrings konto genom att anropa kommandot [New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount) med lämpliga parametrar:

- Inkludera `-EncryptionKeyTypeForQueue` alternativet och ange värdet för att `Account` använda konto krypterings nyckeln för att kryptera data i Queue Storage.
- Inkludera `-EncryptionKeyTypeForTable` alternativet och ange värdet för att `Account` använda konto krypterings nyckeln för att kryptera data i Table Storage.

I följande exempel visas hur du skapar ett allmänt-syfte v2-lagrings konto som är konfigurerat för Geo-redundant lagring med Läs behörighet (RA-GRS) och som använder konto krypterings nyckeln för att kryptera data för både kö-och tabell lagring. Kom ihåg att ersätta plats hållarnas värden inom hakparenteser med dina egna värden:

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

Om du vill använda Azure CLI för att skapa ett lagrings konto som förlitar sig på konto krypterings nyckeln kontrollerar du att du har installerat Azure CLI-version 2.0.80 eller senare. Mer information finns i [Installera Azure CLI](/cli/azure/install-azure-cli).

Skapa sedan ett allmänt-syfte v2-lagrings konto genom att anropa kommandot [AZ Storage Account Create](/cli/azure/storage/account#az-storage-account-create) med lämpliga parametrar:

- Inkludera `--encryption-key-type-for-queue` alternativet och ange värdet för att `Account` använda konto krypterings nyckeln för att kryptera data i Queue Storage.
- Inkludera `--encryption-key-type-for-table` alternativet och ange värdet för att `Account` använda konto krypterings nyckeln för att kryptera data i Table Storage.

I följande exempel visas hur du skapar ett allmänt-syfte v2-lagrings konto som är konfigurerat för Geo-redundant lagring med Läs behörighet (RA-GRS) och som använder konto krypterings nyckeln för att kryptera data för både kö-och tabell lagring. Kom ihåg att ersätta plats hållarnas värden inom hakparenteser med dina egna värden:

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

Följande JSON-exempel skapar ett allmänt-syfte v2-lagrings konto som är konfigurerat för Geo-redundant lagring med Läs behörighet (RA-GRS) och som använder konto krypterings nyckeln för att kryptera data för både kö-och tabell lagring. Kom ihåg att ersätta plats hållarnas värden inom vinkelparenteser med dina egna värden:

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

När du har skapat ett konto som förlitar sig på konto krypterings nyckeln kan du konfigurera Kundhanterade nycklar som lagras i Azure Key Vault eller i Key Vault hanterad maskin säkerhets modell (HSM) (för hands version). Information om hur du lagrar Kundhanterade nycklar i ett nyckel valv finns i [Konfigurera kryptering med Kundhanterade nycklar som lagras i Azure Key Vault](customer-managed-keys-configure-key-vault.md). Information om hur du lagrar Kundhanterade nycklar i en hanterad HSM finns i [Konfigurera kryptering med Kundhanterade nycklar som lagras i Azure Key Vault hanterad HSM (för hands version)](customer-managed-keys-configure-key-vault-hsm.md).

## <a name="verify-the-account-encryption-key"></a>Verifiera konto krypterings nyckeln

För att verifiera att en tjänst i ett lagrings konto använder konto krypterings nyckeln anropar du kommandot för Azure CLI- [AZ lagrings konto](/cli/azure/storage/account#az-storage-account-show) . Det här kommandot returnerar en uppsättning egenskaper för lagrings konto och deras värden. Leta efter `keyType` fältet för varje tjänst i egenskapen kryptering och kontrol lera att den är inställd på `Account` .

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Du kan kontrol lera att en tjänst i ett lagrings konto använder konto krypterings nyckeln genom att anropa kommandot [Get-AzStorageAccount](/powershell/module/az.storage/get-azstorageaccount) . Det här kommandot returnerar en uppsättning egenskaper för lagrings konto och deras värden. Leta efter `KeyType` fältet för varje tjänst i `Encryption` egenskapen och kontrol lera att den är inställd på `Account` .

```powershell
$account = Get-AzStorageAccount -ResourceGroupName <resource-group> `
    -StorageAccountName <storage-account>
$account.Encryption.Services.Queue
$account.Encryption.Services.Table
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Om du vill kontrol lera att en tjänst i ett lagrings konto använder konto krypterings nyckeln anropar du kommandot [AZ Storage Account show](/cli/azure/storage/account#az-storage-account-show) . Det här kommandot returnerar en uppsättning egenskaper för lagrings konto och deras värden. Leta efter `keyType` fältet för varje tjänst i egenskapen kryptering och kontrol lera att den är inställd på `Account` .

```azurecli
az storage account show /
    --name <storage-account> /
    --resource-group <resource-group>
```

# <a name="template"></a>[Mall](#tab/template)

Ej tillämpligt

---

## <a name="pricing-and-billing"></a>Priser och fakturering

Ett lagrings konto som har skapats för att använda en krypterings nyckel som är begränsad till kontot debiteras för tabell lagrings kapacitet och transaktioner med en annan taxa än ett konto som använder standard tjänst omfattnings nyckeln. Mer information finns i [priser för Azure Table Storage](https://azure.microsoft.com/pricing/details/storage/tables/).

## <a name="next-steps"></a>Nästa steg

- [Azure Storage-kryptering av vilande data](storage-service-encryption.md)
- [Kundhanterade nycklar för Azure Storage kryptering](customer-managed-keys-overview.md)
- [Vad är Azure Key Vault](../../key-vault/general/overview.md)?