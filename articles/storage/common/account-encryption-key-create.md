---
title: Skapa ett konto som stöder Kundhanterade nycklar för tabeller och köer
titleSuffix: Azure Storage
description: Lär dig hur du skapar ett lagrings konto som stöder konfigurering av Kundhanterade nycklar för tabeller och köer. Använd Azure CLI eller en Azure Resource Manager mall för att skapa ett lagrings konto som förlitar sig på konto krypterings nyckeln för Azure Storage kryptering. Du kan sedan konfigurera Kundhanterade nycklar för kontot.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 02/05/2020
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.custom: devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: 8150375eff98374e21d200d98c04158b07f1c243
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "92789700"
---
# <a name="create-an-account-that-supports-customer-managed-keys-for-tables-and-queues"></a>Skapa ett konto som stöder Kundhanterade nycklar för tabeller och köer

Azure Storage krypterar alla data i ett lagrings konto i vila. Som standard använder Queue Storage och table Storage en nyckel som är begränsad till tjänsten och hanteras av Microsoft. Du kan också välja att använda Kundhanterade nycklar för att kryptera kö-eller tabell data. Om du vill använda Kundhanterade nycklar med köer och tabeller måste du först skapa ett lagrings konto som använder en krypterings nyckel som är begränsad till kontot, i stället för till tjänsten. När du har skapat ett konto som använder konto krypterings nyckeln för kö-och tabell data kan du konfigurera Kundhanterade nycklar för lagrings kontot.

Den här artikeln beskriver hur du skapar ett lagrings konto som förlitar sig på en nyckel som är begränsad till kontot. När kontot först skapas använder Microsoft konto nyckeln för att kryptera data i kontot och Microsoft hanterar nyckeln. Du kan sedan konfigurera Kundhanterade nycklar för kontot för att dra nytta av dessa förmåner, inklusive möjligheten att tillhandahålla egna nycklar, uppdatera nyckel versionen, rotera nycklarna och återkalla åtkomst kontroller.

## <a name="about-the-feature"></a>Om funktionen

Om du vill skapa ett lagrings konto som förlitar sig på konto krypterings nyckeln för kö-och tabell lagring måste du först registrera dig för att använda den här funktionen med Azure. På grund av begränsad kapacitet bör du vara medveten om att det kan ta flera månader innan förfrågningar om åtkomst godkänns.

Du kan skapa ett lagrings konto som förlitar sig på konto krypterings nyckeln för kö-och tabell lagring i följande regioner:

- East US
- USA, södra centrala
- USA, västra 2  

### <a name="register-to-use-the-account-encryption-key"></a>Registrera dig för att använda konto krypterings nyckeln

Om du vill registrera dig för att använda kontots krypterings nyckel med kö-eller tabell lagring, använder du PowerShell eller Azure CLI.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Registrera med PowerShell genom att anropa kommandot [register-AzProviderFeature](/powershell/module/az.resources/register-azproviderfeature) .

```powershell
Register-AzProviderFeature -ProviderNamespace Microsoft.Storage `
    -FeatureName AllowAccountEncryptionKeyForQueues
Register-AzProviderFeature -ProviderNamespace Microsoft.Storage `
    -FeatureName AllowAccountEncryptionKeyForTables
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Om du vill registrera dig med Azure CLI anropar du kommandot [AZ funktions register](/cli/azure/feature#az-feature-register) .

```azurecli
az feature register --namespace Microsoft.Storage \
    --name AllowAccountEncryptionKeyForQueues
az feature register --namespace Microsoft.Storage \
    --name AllowAccountEncryptionKeyForTables
```

# <a name="template"></a>[Mall](#tab/template)

Ej tillämpligt

---

### <a name="check-the-status-of-your-registration"></a>Kontrol lera status för registreringen

Använd PowerShell eller Azure CLI för att kontrol lera status för registreringen för kö-eller tabell lagring.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Om du vill kontrol lera status för registreringen med PowerShell anropar du kommandot [Get-AzProviderFeature](/powershell/module/az.resources/get-azproviderfeature) .

```powershell
Get-AzProviderFeature -ProviderNamespace Microsoft.Storage `
    -FeatureName AllowAccountEncryptionKeyForQueues
Get-AzProviderFeature -ProviderNamespace Microsoft.Storage `
    -FeatureName AllowAccountEncryptionKeyForTables
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Om du vill kontrol lera statusen för registreringen med Azure CLI anropar du kommandot [AZ Feature](/cli/azure/feature#az-feature-show) .

```azurecli
az feature show --namespace Microsoft.Storage \
    --name AllowAccountEncryptionKeyForQueues
az feature show --namespace Microsoft.Storage \
    --name AllowAccountEncryptionKeyForTables
```

# <a name="template"></a>[Mall](#tab/template)

Ej tillämpligt

---

### <a name="re-register-the-azure-storage-resource-provider"></a>Registrera Azure Storage Resource providern igen

När registreringen har godkänts måste du registrera Azure Storage resurs leverantören igen. Använd PowerShell eller Azure CLI för att omregistrera resurs leverantören.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Anropa kommandot [register-AzResourceProvider](/powershell/module/az.resources/register-azresourceprovider) för att omregistrera resurs leverantören med PowerShell.

```powershell
Register-AzResourceProvider -ProviderNamespace 'Microsoft.Storage'
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Om du vill registrera om resurs leverantören med Azure CLI anropar du kommandot [AZ Provider register](/cli/azure/provider#az-provider-register) .

```azurecli
az provider register --namespace 'Microsoft.Storage'
```

# <a name="template"></a>[Mall](#tab/template)

Ej tillämpligt

---

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

## <a name="next-steps"></a>Nästa steg

- [Azure Storage-kryptering av vilande data](storage-service-encryption.md)
- [Kundhanterade nycklar för Azure Storage kryptering](customer-managed-keys-overview.md)
- [Vad är Azure Key Vault](../../key-vault/general/overview.md)?