---
title: Konfigurera kryptering med kundhanterade nycklar som lagras i Azure Key Vault
titleSuffix: Azure Storage
description: Lär dig hur du konfigurerar Azure Storage kryptering med Kundhanterade nycklar som lagras i Azure Key Vault med hjälp av Azure Portal, PowerShell eller Azure CLI.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 02/16/2021
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.custom: devx-track-azurepowershell, devx-track-azurecli
ms.openlocfilehash: 24fbe843986b732a04c9e356c54f3d768d6739be
ms.sourcegitcommit: de98cb7b98eaab1b92aa6a378436d9d513494404
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/17/2021
ms.locfileid: "100558179"
---
# <a name="configure-encryption-with-customer-managed-keys-stored-in-azure-key-vault"></a>Konfigurera kryptering med kundhanterade nycklar som lagras i Azure Key Vault

Azure Storage krypterar alla data i ett lagrings konto i vila. Som standard krypteras data med Microsoft-hanterade nycklar. Om du vill ha ytterligare kontroll över krypterings nycklarna kan du hantera dina egna nycklar. Kundhanterade nycklar måste lagras i Azure Key Vault eller Key Vault-hanterad maskin varu säkerhets modell (HSM) (för hands version).

Den här artikeln visar hur du konfigurerar kryptering med Kundhanterade nycklar som lagras i ett nyckel valv med hjälp av Azure Portal, PowerShell eller Azure CLI. Information om hur du konfigurerar kryptering med Kundhanterade nycklar som lagras i en hanterad HSM finns i [Konfigurera kryptering med Kundhanterade nycklar som lagras i Azure Key Vault hanterad HSM (för hands version)](customer-managed-keys-configure-key-vault-hsm.md).

> [!NOTE]
> Azure Key Vault och Azure Key Vault Managed HSM stöder samma API: er och hanterings gränssnitt för konfiguration.

## <a name="configure-a-key-vault"></a>Konfigurera ett nyckel valv

Du kan använda ett nytt eller befintligt nyckel valv för att lagra Kundhanterade nycklar. Lagrings kontot och nyckel valvet måste finnas i samma region, men de kan finnas i olika prenumerationer.

Om du använder Kundhanterade nycklar med Azure Storage kryptering måste både mjuk borttagnings-och rensnings skydd aktive ras för nyckel valvet. Mjuk borttagning är aktiverat som standard när du skapar ett nytt nyckel valv och inte kan inaktive ras. Du kan aktivera rensnings skyddet antingen när du skapar nyckel valvet eller när det har skapats.

# <a name="azure-portal"></a>[Azure-portalen](#tab/portal)

Information om hur du skapar ett nyckel valv med Azure Portal finns i [snabb start: skapa ett nyckel valv med hjälp av Azure Portal](../../key-vault/general/quick-create-portal.md). När du skapar nyckel valvet väljer du **Aktivera rensnings skydd**, som du ser i följande bild.

:::image type="content" source="media/customer-managed-keys-configure-key-vault/configure-key-vault-portal.png" alt-text="Skärm bild som visar hur du aktiverar rensnings skyddet när du skapar ett nyckel valv":::

Följ dessa steg om du vill aktivera rensnings skyddet för ett befintligt nyckel valv:

1. Navigera till ditt nyckel valv i Azure Portal.
1. Under **Inställningar** väljer du **Egenskaper**.
1. I avsnittet **Rensa skydd** väljer du **Aktivera rensnings skydd**.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Om du vill skapa ett nytt nyckel valv med PowerShell installerar du version 2.0.0 eller senare av PowerShell-modulen [AZ. Key Vault](https://www.powershellgallery.com/packages/Az.KeyVault/2.0.0) . Anropa sedan [New-AzKeyVault](/powershell/module/az.keyvault/new-azkeyvault) för att skapa ett nytt nyckel valv. Med version 2.0.0 och senare av modulen AZ. Key Vault är mjuk borttagning aktiverat som standard när du skapar ett nytt nyckel valv.

I följande exempel skapas ett nytt nyckel valv med både mjuk borttagnings-och rensnings skydd aktiverat. Kom ihåg att ersätta plats hållarnas värden inom hakparenteser med dina egna värden.

```powershell
$keyVault = New-AzKeyVault -Name <key-vault> `
    -ResourceGroupName <resource_group> `
    -Location <location> `
    -EnablePurgeProtection
```

Information om hur du aktiverar rensnings skydd på ett befintligt nyckel valv med PowerShell finns i [använda mjuk borttagning med PowerShell](../../key-vault/general/key-vault-recovery.md).

Tilldela sedan en systemtilldelad hanterad identitet till ditt lagrings konto. Du använder den här hanterade identiteten för att ge lagrings kontots behörigheter åtkomst till nyckel valvet. Mer information om systemtilldelade hanterade identiteter finns i [Vad är hanterade identiteter för Azure-resurser?](../../active-directory/managed-identities-azure-resources/overview.md).

Om du vill tilldela en hanterad identitet med PowerShell, anropa [set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount):

```powershell
$storageAccount = Set-AzStorageAccount -ResourceGroupName <resource_group> `
    -Name <storage-account> `
    -AssignIdentity
```

Konfigurera slutligen åtkomst principen för nyckel valvet så att lagrings kontot har behörighet att komma åt det. I det här steget ska du använda den hanterade identitet som du tidigare tilldelade till lagrings kontot.

Ange åtkomst principen för nyckel valvet genom att anropa [set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy):

```powershell
Set-AzKeyVaultAccessPolicy `
    -VaultName $keyVault.VaultName `
    -ObjectId $storageAccount.Identity.PrincipalId `
    -PermissionsToKeys wrapkey,unwrapkey,get
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Skapa ett nytt nyckel valv med Azure CLI genom att anropa [AZ](/cli/azure/keyvault#az-keyvault-create)-nyckelpar Create. Kom ihåg att ersätta plats hållarnas värden inom hakparenteser med dina egna värden:

```azurecli-interactive
az keyvault create \
    --name <key-vault> \
    --resource-group <resource_group> \
    --location <region> \
    --enable-purge-protection
```

Information om hur du aktiverar rensnings skydd på ett befintligt nyckel valv med Azure CLI finns i [använda mjuk borttagning med CLI](../../key-vault/general/key-vault-recovery.md).

Tilldela sedan en systemtilldelad hanterad identitet till lagrings kontot. Du använder den här hanterade identiteten för att ge lagrings kontots behörigheter åtkomst till nyckel valvet. Mer information om systemtilldelade hanterade identiteter finns i [Vad är hanterade identiteter för Azure-resurser?](../../active-directory/managed-identities-azure-resources/overview.md).

Om du vill tilldela en hanterad identitet med Azure CLI kan du anropa [AZ Storage Account Update](/cli/azure/storage/account#az-storage-account-update):

```azurecli-interactive
az storage account update \
    --name <storage-account> \
    --resource-group <resource_group> \
    --assign-identity
```

Konfigurera slutligen åtkomst principen för nyckel valvet så att lagrings kontot har behörighet att komma åt det. I det här steget ska du använda den hanterade identitet som du tidigare tilldelade till lagrings kontot.

Om du vill ange åtkomst principen för nyckel valvet anropar du [AZ-nyckel valv set-princip](/cli/azure/keyvault#az-keyvault-set-policy):

```azurecli-interactive
storage_account_principal=$(az storage account show \
    --name <storage-account> \
    --resource-group <resource-group> \
    --query identity.principalId \
    --output tsv)
az keyvault set-policy \
    --name <key-vault> \
    --resource-group <resource_group>
    --object-id $storage_account_principal \
    --key-permissions get unwrapKey wrapKey
```

---

## <a name="add-a-key"></a>Lägg till en nyckel

Lägg sedan till en nyckel i nyckel valvet.

Azure Storage kryptering stöder RSA-och RSA-HSM-nycklar av storlekarna 2048, 3072 och 4096. Mer information om nycklar finns i [om nycklar](../../key-vault/keys/about-keys.md).

# <a name="azure-portal"></a>[Azure-portalen](#tab/portal)

Information om hur du lägger till en nyckel med Azure Portal finns i [snabb start: Ange och hämta en nyckel från Azure Key Vault med hjälp av Azure Portal](../../key-vault/keys/quick-create-portal.md).

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Om du vill lägga till en nyckel med PowerShell anropar du [Add-AzKeyVaultKey](/powershell/module/az.keyvault/add-azkeyvaultkey). Kom ihåg att ersätta plats hållarnas värden inom hakparenteser med dina egna värden och använda variablerna som definierats i föregående exempel.

```powershell
$key = Add-AzKeyVaultKey -VaultName $keyVault.VaultName `
    -Name <key> `
    -Destination 'Software'
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Om du vill lägga till en nyckel med Azure CLI anropar du [AZ Key Vault Key Create](/cli/azure/keyvault/key#az-keyvault-key-create). Kom ihåg att ersätta plats hållarnas värden inom hakparenteser med dina egna värden.

```azurecli-interactive
az keyvault key create \
    --name <key> \
    --vault-name <key-vault>
```

---

## <a name="configure-encryption-with-customer-managed-keys"></a>Konfigurera kryptering med kundhanterade nycklar

Konfigurera sedan Azure Storage-kontot så att det använder Kundhanterade nycklar med Azure Key Vault och ange sedan nyckeln som ska associeras med lagrings kontot.

När du konfigurerar kryptering med Kundhanterade nycklar kan du välja att automatiskt uppdatera den nyckel version som används för Azure Storage kryptering när en ny version är tillgänglig i det associerade nyckel valvet. Alternativt kan du uttryckligen ange en nyckel version som ska användas för kryptering tills nyckel versionen uppdateras manuellt.

> [!NOTE]
> Om du vill rotera en nyckel skapar du en ny version av nyckeln i Azure Key Vault. Azure Storage hanterar inte rotationen av nyckeln i Azure Key Vault, så du måste rotera nyckeln manuellt eller skapa en funktion för att rotera den enligt ett schema.

### <a name="configure-encryption-for-automatic-updating-of-key-versions"></a>Konfigurera kryptering för automatisk uppdatering av nyckel versioner

Azure Storage kan automatiskt uppdatera den Kundhanterade nyckel som används för kryptering för att använda den senaste nyckel versionen. När den Kundhanterade nyckeln roteras i Azure Key Vault börjar Azure Storage automatiskt att använda den senaste versionen av nyckeln för kryptering.

# <a name="azure-portal"></a>[Azure-portalen](#tab/portal)

Följ dessa steg om du vill konfigurera Kundhanterade nycklar med automatisk uppdatering av nyckel versionen i Azure Portal:

1. Navigera till ditt lagringskonto.
1. På bladet **Inställningar** för lagrings kontot klickar du på **kryptering**. Som standard är nyckel hantering inställd på **Microsoft-hanterade nycklar**, som du ser i följande bild.

    ![Portal skärm bild som visar krypterings alternativ](./media/customer-managed-keys-configure-key-vault/portal-configure-encryption-keys.png)

1. Välj alternativet **kundens hanterade nycklar** .
1. Välj alternativet **Välj från Key Vault** .
1. Välj **Välj ett nyckel valv och nyckel**.
1. Välj det nyckel valv som innehåller den nyckel som du vill använda.
1. Välj nyckeln från nyckel valvet.

   ![Skärm bild som visar hur du väljer Key Vault och Key](./media/customer-managed-keys-configure-key-vault/portal-select-key-from-key-vault.png)

1. Spara ändringarna.

När du har angett nyckeln anger Azure Portal att automatisk uppdatering av nyckel versionen är aktive rad och visar den nyckel version som används för kryptering.

:::image type="content" source="media/customer-managed-keys-configure-key-vault/portal-auto-rotation-enabled.png" alt-text="Skärm bild som visar automatisk uppdatering av den nyckel version som är aktive rad":::

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Om du vill konfigurera Kundhanterade nycklar med automatisk uppdatering av nyckel versionen med PowerShell installerar du modulen [AZ. Storage](https://www.powershellgallery.com/packages/Az.Storage) , version 2.0.0 eller senare.

Om du vill uppdatera nyckel versionen för en kundhanterad nyckel automatiskt, utelämnar du nyckel versionen när du konfigurerar kryptering med Kundhanterade nycklar för lagrings kontot. Anropa [set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount) för att uppdatera lagrings kontots krypterings inställningar, som visas i följande exempel, och inkludera alternativet **-KeyvaultEncryption** för att aktivera Kundhanterade nycklar för lagrings kontot.

Kom ihåg att ersätta plats hållarnas värden inom hakparenteser med dina egna värden och använda variablerna som definierats i föregående exempel.

```powershell
Set-AzStorageAccount -ResourceGroupName $storageAccount.ResourceGroupName `
    -AccountName $storageAccount.StorageAccountName `
    -KeyvaultEncryption `
    -KeyName $key.Name `
    -KeyVaultUri $keyVault.VaultUri
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Om du vill konfigurera Kundhanterade nycklar med automatisk uppdatering av nyckel versionen med Azure CLI installerar du [Azure CLI version 2.4.0](/cli/azure/release-notes-azure-cli#april-21-2020) eller senare. Mer information finns i [Installera Azure CLI](/cli/azure/install-azure-cli).

Om du vill uppdatera nyckel versionen för en kundhanterad nyckel automatiskt, utelämnar du nyckel versionen när du konfigurerar kryptering med Kundhanterade nycklar för lagrings kontot. Anropa [AZ Storage Account Update](/cli/azure/storage/account#az-storage-account-update) för att uppdatera lagrings kontots krypterings inställningar, som du ser i följande exempel. Inkludera `--encryption-key-source` parametern och Ställ in den på `Microsoft.Keyvault` för att aktivera Kundhanterade nycklar för kontot.

Kom ihåg att ersätta plats hållarnas värden inom hakparenteser med dina egna värden.

```azurecli-interactive
key_vault_uri=$(az keyvault show \
    --name <key-vault> \
    --resource-group <resource_group> \
    --query properties.vaultUri \
    --output tsv)
az storage account update
    --name <storage-account> \
    --resource-group <resource_group> \
    --encryption-key-name <key> \
    --encryption-key-source Microsoft.Keyvault \
    --encryption-key-vault $key_vault_uri
```

---

### <a name="configure-encryption-for-manual-updating-of-key-versions"></a>Konfigurera kryptering för manuell uppdatering av nyckel versioner

Om du vill uppdatera nyckel versionen manuellt måste du uttryckligen ange versionen vid den tidpunkt då du konfigurerar kryptering med Kundhanterade nycklar. I det här fallet uppdaterar Azure Storage inte nyckel versionen automatiskt när en ny version skapas i nyckel valvet. Om du vill använda en ny nyckel version måste du uppdatera den version som används för Azure Storage kryptering manuellt.

# <a name="azure-portal"></a>[Azure-portalen](#tab/portal)

Om du vill konfigurera Kundhanterade nycklar med manuell uppdatering av nyckel versionen i Azure Portal anger du nyckel-URI, inklusive versionen. Följ dessa steg om du vill ange en nyckel som en URI:

1. Om du vill hitta nyckel-URI: n i Azure Portal navigerar du till nyckel valvet och väljer inställningen **nycklar** . Välj önskad nyckel och klicka sedan på nyckeln för att visa dess versioner. Välj en nyckel version för att visa inställningarna för den versionen.
1. Kopiera värdet för fältet **nyckel identifierare** , som innehåller URI.

    ![Skärm bild som visar Key Vault Key-URI](media/customer-managed-keys-configure-key-vault/portal-copy-key-identifier.png)

1. I **krypterings nyckel** inställningarna för ditt lagrings konto väljer du alternativet för att **Ange nyckel-URI** .
1. Klistra in den URI som du kopierade i fältet **nyckel-URI** . Utelämna nyckel versionen från URI: n för att aktivera automatisk uppdatering av nyckel versionen.

   ![Skärm bild som visar hur du anger nyckel-URI](./media/customer-managed-keys-configure-key-vault/portal-specify-key-uri.png)

1. Ange den prenumeration som innehåller nyckel valvet.
1. Spara ändringarna.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Konfigurera Kundhanterade nycklar med manuell uppdatering av nyckel versionen genom att uttryckligen tillhandahålla nyckel versionen när du konfigurerar kryptering för lagrings kontot. Anropa [set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount) för att uppdatera lagrings kontots krypterings inställningar, som visas i följande exempel, och inkludera alternativet **-KeyvaultEncryption** för att aktivera Kundhanterade nycklar för lagrings kontot.

Kom ihåg att ersätta plats hållarnas värden inom hakparenteser med dina egna värden och använda variablerna som definierats i föregående exempel.

```powershell
Set-AzStorageAccount -ResourceGroupName $storageAccount.ResourceGroupName `
    -AccountName $storageAccount.StorageAccountName `
    -KeyvaultEncryption `
    -KeyName $key.Name `
    -KeyVersion $key.Version `
    -KeyVaultUri $keyVault.VaultUri
```

När du uppdaterar nyckel versionen manuellt måste du uppdatera lagrings kontots krypterings inställningar för att använda den nya versionen. Börja med att anropa [Get-AzKeyVaultKey](/powershell/module/az.keyvault/get-azkeyvaultkey) för att hämta den senaste versionen av nyckeln. Anropa sedan [set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount) för att uppdatera lagrings kontots krypterings inställningar för att använda den nya versionen av nyckeln, som du ser i föregående exempel.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Konfigurera Kundhanterade nycklar med manuell uppdatering av nyckel versionen genom att uttryckligen tillhandahålla nyckel versionen när du konfigurerar kryptering för lagrings kontot. Anropa [AZ Storage Account Update](/cli/azure/storage/account#az-storage-account-update) för att uppdatera lagrings kontots krypterings inställningar, som du ser i följande exempel. Inkludera `--encryption-key-source` parametern och Ställ in den på `Microsoft.Keyvault` för att aktivera Kundhanterade nycklar för kontot.

Kom ihåg att ersätta plats hållarnas värden inom hakparenteser med dina egna värden.

```azurecli-interactive
key_vault_uri=$(az keyvault show \
    --name <key-vault> \
    --resource-group <resource_group> \
    --query properties.vaultUri \
    --output tsv)
key_version=$(az keyvault key list-versions \
    --name <key> \
    --vault-name <key-vault> \
    --query [-1].kid \
    --output tsv | cut -d '/' -f 6)
az storage account update
    --name <storage-account> \
    --resource-group <resource_group> \
    --encryption-key-name <key> \
    --encryption-key-version $key_version \
    --encryption-key-source Microsoft.Keyvault \
    --encryption-key-vault $key_vault_uri
```

När du uppdaterar nyckel versionen manuellt måste du uppdatera lagrings kontots krypterings inställningar för att använda den nya versionen. Börja med att fråga efter Key Vault-URI: n genom att anropa AZ-nyckelpar [show](/cli/azure/keyvault#az-keyvault-show)och för nyckel versionen genom att anropa [AZ Key Vault Key List-versions](/cli/azure/keyvault/key#az-keyvault-key-list-versions). Anropa sedan [AZ Storage Account Update](/cli/azure/storage/account#az-storage-account-update) för att uppdatera lagrings kontots krypterings inställningar för att använda den nya versionen av nyckeln, som du ser i föregående exempel.

---

## <a name="change-the-key"></a>Ändra nyckeln

Du kan ändra den nyckel som du använder för Azure Storage kryptering när som helst.

# <a name="azure-portal"></a>[Azure-portalen](#tab/portal)

Följ dessa steg om du vill ändra nyckeln till Azure Portal:

1. Navigera till ditt lagrings konto och visa **krypterings** inställningarna.
1. Välj nyckel valvet och välj en ny nyckel.
1. Spara ändringarna.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Om du vill ändra nyckeln med PowerShell, anropa [set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount) som visas i [Konfigurera kryptering med Kundhanterade nycklar](#configure-encryption-with-customer-managed-keys) och ange det nya nyckel namnet och den nya versionen. Om den nya nyckeln finns i ett annat nyckel valv måste du även uppdatera Key Vault-URI: n.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Om du vill ändra nyckeln med Azure CLI kan du anropa [AZ Storage Account Update](/cli/azure/storage/account#az-storage-account-update) på det sätt som visas i [Konfigurera kryptering med Kundhanterade nycklar](#configure-encryption-with-customer-managed-keys) och ange det nya nyckel namnet och den nya versionen. Om den nya nyckeln finns i ett annat nyckel valv måste du även uppdatera Key Vault-URI: n.

---

## <a name="revoke-customer-managed-keys"></a>Återkalla Kundhanterade nycklar

Om du återkallar en kundhanterad nyckel tas associationen bort mellan lagrings kontot och nyckel valvet.

# <a name="azure-portal"></a>[Azure-portalen](#tab/portal)

Om du vill återkalla Kundhanterade nycklar med Azure Portal inaktiverar du nyckeln enligt beskrivningen i [inaktivera Kundhanterade nycklar](#disable-customer-managed-keys).

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Du kan återkalla Kundhanterade nycklar genom att ta bort åtkomst principen för nyckel valvet. Om du vill återkalla en kundhanterad nyckel med PowerShell anropar du kommandot [Remove-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/remove-azkeyvaultaccesspolicy) , som du ser i följande exempel. Kom ihåg att ersätta plats hållarnas värden inom hakparenteser med dina egna värden och använda variablerna som definierats i föregående exempel.

```powershell
Remove-AzKeyVaultAccessPolicy -VaultName $keyVault.VaultName `
    -ObjectId $storageAccount.Identity.PrincipalId `
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Du kan återkalla Kundhanterade nycklar genom att ta bort åtkomst principen för nyckel valvet. Om du vill återkalla en kundhanterad nyckel med Azure CLI anropar du kommandot [AZ Key Vault Delete-policy](/cli/azure/keyvault#az-keyvault-delete-policy) , som visas i följande exempel. Kom ihåg att ersätta plats hållarnas värden inom hakparenteser med dina egna värden och använda variablerna som definierats i föregående exempel.

```azurecli-interactive
az keyvault delete-policy \
    --name <key-vault> \
    --object-id $storage_account_principal
```

---

## <a name="disable-customer-managed-keys"></a>Inaktivera Kundhanterade nycklar

När du inaktiverar Kundhanterade nycklar är ditt lagrings konto återigen krypterat med Microsoft-hanterade nycklar.

# <a name="azure-portal"></a>[Azure-portalen](#tab/portal)

Följ dessa steg om du vill inaktivera Kundhanterade nycklar i Azure Portal:

1. Navigera till ditt lagrings konto och visa **krypterings** inställningarna.
1. Avmarkera kryss rutan bredvid inställningen **Använd din egen nyckel** .

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Om du vill inaktivera Kundhanterade nycklar med PowerShell, anropa [set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount) med `-StorageEncryption` alternativet, som du ser i följande exempel. Kom ihåg att ersätta plats hållarnas värden inom hakparenteser med dina egna värden och använda variablerna som definierats i föregående exempel.

```powershell
Set-AzStorageAccount -ResourceGroupName $storageAccount.ResourceGroupName `
    -AccountName $storageAccount.StorageAccountName `
    -StorageEncryption  
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Om du vill inaktivera Kundhanterade nycklar med Azure CLI anropar du [AZ lagrings konto uppdatering](/cli/azure/storage/account#az-storage-account-update) och anger `--encryption-key-source parameter` till `Microsoft.Storage` , som visas i följande exempel. Kom ihåg att ersätta plats hållarnas värden inom hakparenteser med dina egna värden och använda variablerna som definierats i föregående exempel.

```azurecli-interactive
az storage account update
    --name <storage-account> \
    --resource-group <resource_group> \
    --encryption-key-source Microsoft.Storage
```

---

## <a name="next-steps"></a>Nästa steg

- [Azure Storage-kryptering av vilande data](storage-service-encryption.md)
- [Kundhanterade nycklar för Azure Storage kryptering](customer-managed-keys-overview.md)
- [Konfigurera kryptering med Kundhanterade nycklar som lagras i Azure Key Vault hanterad HSM (förhands granskning)](customer-managed-keys-configure-key-vault-hsm.md)
