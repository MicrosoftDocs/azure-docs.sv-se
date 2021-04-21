---
title: Skapa ett lagringskonto med infrastrukturkryptering aktiverat för dubbel kryptering av data
titleSuffix: Azure Storage
description: Kunder som behöver högre säkerhetsnivåer för att deras data är säkra kan också aktivera 256-bitars AES-kryptering på Azure Storage infrastrukturnivå. När infrastrukturkryptering är aktiverat krypteras data i ett lagringskonto två gånger med två olika krypteringsalgoritmer och två olika nycklar.
services: storage
author: tamram
ms.service: storage
ms.date: 09/17/2020
ms.topic: conceptual
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.custom: devx-track-azurecli
ms.openlocfilehash: 23b3ca919be030490cca06f31dac623d7f80be44
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107790391"
---
# <a name="create-a-storage-account-with-infrastructure-encryption-enabled-for-double-encryption-of-data"></a>Skapa ett lagringskonto med infrastrukturkryptering aktiverat för dubbel kryptering av data

Azure Storage krypterar automatiskt alla data i ett lagringskonto på tjänstnivå med hjälp av 256-bitars AES-kryptering, ett av de starkaste blockchiffreringarna som är tillgängliga och är FIPS 140-2-kompatibel. Kunder som behöver högre säkerhetsnivåer för att deras data är säkra kan också aktivera 256-bitars AES-kryptering på Azure Storage infrastrukturnivå. När infrastrukturkryptering är aktiverat krypteras data i ett lagringskonto två gånger på tjänstnivå och en gång på infrastrukturnivå med två olika krypteringsalgoritmer och &mdash; &mdash; två olika nycklar. Dubbel kryptering Azure Storage data skyddar mot ett scenario där en av krypteringsalgoritmerna eller nycklarna kan komprometteras. I det här scenariot fortsätter det extra krypteringslagret att skydda dina data.

Kryptering på tjänstnivå stöder användning av antingen Microsoft-hanterade nycklar eller kund hanterade nycklar med Azure Key Vault eller Key Vault Managed Hardware Security Model (HSM) (förhandsversion). Kryptering på infrastrukturnivå är beroende av Microsoft-hanterade nycklar och använder alltid en separat nyckel. Mer information om nyckelhantering med hjälp Azure Storage kryptering finns i [Om hantering av krypteringsnycklar.](storage-service-encryption.md#about-encryption-key-management)

Om du vill kryptera dina data dubbelt måste du först skapa ett lagringskonto som är konfigurerat för infrastrukturkryptering. I den här artikeln beskrivs hur du skapar ett lagringskonto som möjliggör infrastrukturkryptering.

## <a name="register-to-use-infrastructure-encryption"></a>Registrera dig för att använda infrastrukturkryptering

Om du vill skapa ett lagringskonto som har infrastrukturkryptering aktiverat måste du först registrera dig för att använda den här funktionen med Azure med hjälp av PowerShell eller Azure CLI.

# <a name="azure-portal"></a>[Azure-portalen](#tab/portal)

Ej tillämpligt

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Om du vill registrera dig med PowerShell anropar du kommandot [Register-AzProviderFeature.](/powershell/module/az.resources/register-azproviderfeature)

```powershell
Register-AzProviderFeature -ProviderNamespace Microsoft.Storage `
    -FeatureName AllowRequireInfraStructureEncryption
```

Om du vill kontrollera statusen för din registrering med PowerShell anropar du kommandot [Get-AzProviderFeature.](/powershell/module/az.resources/get-azproviderfeature)

```powershell
Get-AzProviderFeature -ProviderNamespace Microsoft.Storage `
    -FeatureName AllowRequireInfraStructureEncryption
```

När registreringen har godkänts måste du registrera om Azure Storage resursprovidern. Om du vill registrera resursprovidern igen med PowerShell anropar du kommandot [Register-AzResourceProvider.](/powershell/module/az.resources/register-azresourceprovider)

```powershell
Register-AzResourceProvider -ProviderNamespace 'Microsoft.Storage'
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Om du vill registrera dig med Azure CLI anropar [du kommandot az feature register.](/cli/azure/feature#az_feature_register)

```azurecli
az feature register --namespace Microsoft.Storage \
    --name AllowRequireInfraStructureEncryption
```

Om du vill kontrollera statusen för din registrering med Azure CLI anropar du [kommandot az feature.](/cli/azure/feature#az_feature_show)

```azurecli
az feature show --namespace Microsoft.Storage \
    --name AllowRequireInfraStructureEncryption
```

När registreringen har godkänts måste du registrera om Azure Storage resursprovidern. Om du vill omregistrera resursprovidern med Azure CLI anropar du [kommandot az provider register.](/cli/azure/provider#az_provider_register)

```azurecli
az provider register --namespace 'Microsoft.Storage'
```

# <a name="template"></a>[Mall](#tab/template)

Ej tillämpligt

---

## <a name="create-an-account-with-infrastructure-encryption-enabled"></a>Skapa ett konto med infrastrukturkryptering aktiverat

Du måste konfigurera ett lagringskonto att använda infrastrukturkryptering när du skapar kontot. Lagringskontot måste vara av typen generell användning v2.

Infrastrukturkryptering kan inte aktiveras eller inaktiveras när kontot har skapats.

# <a name="azure-portal"></a>[Azure-portalen](#tab/portal)

Följ dessa steg om du vill använda PowerShell för att skapa ett lagringskonto med infrastrukturkryptering aktiverat:

1. I Azure Portal du till sidan **Lagringskonton.**
1. Välj knappen **Lägg till** för att lägga till ett nytt v2-lagringskonto för generell användning.
1. På fliken **Avancerat** letar du **upp Infrastrukturkryptering** och väljer **Aktiverad.**
1. Välj **Granska + skapa för** att slutföra skapandet av lagringskontot.

    :::image type="content" source="media/infrastructure-encryption-enable/create-account-infrastructure-encryption-portal.png" alt-text="Skärmbild som visar hur du aktiverar infrastrukturkryptering när du skapar ett konto":::

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Om du vill använda PowerShell för att skapa ett lagringskonto med infrastrukturkryptering aktiverat kontrollerar du att du har installerat [Az.Storage PowerShell-modulen](https://www.powershellgallery.com/packages/Az.Storage), version 2.2.0 eller senare. Mer information finns i [Installera Azure PowerShell](/powershell/azure/install-az-ps).

Skapa sedan ett v2-lagringskonto för generell användning genom att anropa kommandot [New-AzStorageAccount.](/powershell/module/az.storage/new-azstorageaccount) Inkludera alternativet `-RequireInfrastructureEncryption` för att aktivera infrastrukturkryptering.

I följande exempel visas hur du skapar ett v2-lagringskonto för generell användning som är konfigurerat för read-access geo-redundant storage (RA-GRS) och har infrastrukturkryptering aktiverat för dubbel kryptering av data. Kom ihåg att ersätta platshållarvärdena inom hakparentes med dina egna värden:

```powershell
New-AzStorageAccount -ResourceGroupName <resource_group> `
    -AccountName <storage-account> `
    -Location <location> `
    -SkuName "Standard_RAGRS" `
    -Kind StorageV2 `
    -RequireInfrastructureEncryption
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Om du vill använda Azure CLI för att skapa ett lagringskonto som har infrastrukturkryptering aktiverat kontrollerar du att du har installerat Azure CLI version 2.8.0 eller senare. Mer information finns i [Installera Azure CLI.](/cli/azure/install-azure-cli)

Skapa sedan ett v2-lagringskonto för generell användning genom att anropa [kommandot az storage account create](/cli/azure/storage/account#az_storage_account_create) och inkludera för att aktivera `--require-infrastructure-encryption option` infrastrukturkryptering.

I följande exempel visas hur du skapar ett v2-lagringskonto för generell användning som är konfigurerat för read-access geo-redundant storage (RA-GRS) och har infrastrukturkryptering aktiverat för dubbel kryptering av data. Kom ihåg att ersätta platshållarvärdena inom hakparentes med dina egna värden:

```azurecli-interactive
az storage account create \
    --name <storage-account> \
    --resource-group <resource-group> \
    --location <location> \
    --sku Standard_RAGRS \
    --kind StorageV2 \
    --require-infrastructure-encryption
```

# <a name="template"></a>[Mall](#tab/template)

I följande JSON-exempel skapas ett v2-lagringskonto för generell användning som är konfigurerat för read-access geo-redundant storage (RA-GRS) och har infrastrukturkryptering aktiverat för dubbel kryptering av data. Kom ihåg att ersätta platshållarvärdena inom hakparentes med dina egna värden:

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
                "keySource": "Microsoft.Storage",
                "requireInfrastructureEncryption": true,
                "services": {
                    "blob": { "enabled": true },
                    "file": { "enabled": true }
              }
            }
        }
    }
],
```

---

## <a name="verify-that-infrastructure-encryption-is-enabled"></a>Kontrollera att infrastrukturkryptering är aktiverat

# <a name="azure-portal"></a>[Azure-portalen](#tab/portal)

Så här kontrollerar du att infrastrukturkryptering är aktiverat för ett lagringskonto Azure Portal lagringskontot:

1. Navigera till ditt lagringskonto i Azure-portalen.
1. Under **Inställningar** väljer du **Kryptering.**

    :::image type="content" source="media/infrastructure-encryption-enable/verify-infrastructure-encryption-portal.png" alt-text="Skärmbild som visar hur du verifierar att infrastrukturkryptering är aktiverat för kontot":::

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Om du vill kontrollera att infrastrukturkryptering är aktiverat för ett lagringskonto med PowerShell anropar du kommandot [Get-AzStorageAccount.](/powershell/module/az.storage/get-azstorageaccount) Det här kommandot returnerar en uppsättning egenskaper för lagringskontot och deras värden. Hämta fältet `RequireInfrastructureEncryption` i egenskapen och kontrollera att det är `Encryption` inställt på `True` .

I följande exempel hämtas värdet för `RequireInfrastructureEncryption` egenskapen . Kom ihåg att ersätta platshållarvärdena inom vinkelparenteser med dina egna värden:

```powershell
$account = Get-AzStorageAccount -ResourceGroupName <resource-group> `
    -StorageAccountName <storage-account>
$account.Encryption.RequireInfrastructureEncryption
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Om du vill kontrollera att infrastrukturkryptering har aktiverats för ett lagringskonto med Azure CLI anropar du [kommandot az storage account show.](/cli/azure/storage/account#az_storage_account_show) Det här kommandot returnerar en uppsättning egenskaper för lagringskontot och deras värden. Leta efter `requireInfrastructureEncryption` fältet i egenskapen och kontrollera att det är `encryption` inställt på `true` .

I följande exempel hämtas värdet för `requireInfrastructureEncryption` egenskapen . Kom ihåg att ersätta platshållarvärdena inom vinkelparenteser med dina egna värden:

```azurecli-interactive
az storage account show /
    --name <storage-account> /
    --resource-group <resource-group>
```

# <a name="template"></a>[Mall](#tab/template)

Ej tillämpligt

---

## <a name="next-steps"></a>Nästa steg

- [Azure Storage-kryptering av vilande data](storage-service-encryption.md)
- [Kund-hanterade nycklar för Azure Storage kryptering](customer-managed-keys-overview.md)
