---
title: Konfigurera kryptering med kundhanterade nycklar som lagras i Azure Key Vault
titleSuffix: Azure Storage
description: Lär dig hur du Azure Storage kryptering med kund hanterade nycklar som lagras i Azure Key Vault med hjälp av Azure Portal, PowerShell eller Azure CLI.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 02/16/2021
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.custom: devx-track-azurepowershell, devx-track-azurecli
ms.openlocfilehash: 77a01a270f47ddacb71962188e7fedd0a0a9f6d0
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107790445"
---
# <a name="configure-encryption-with-customer-managed-keys-stored-in-azure-key-vault"></a>Konfigurera kryptering med kundhanterade nycklar som lagras i Azure Key Vault

Azure Storage krypterar alla data i ett lagringskonto i vila. Som standard krypteras data med Microsoft-hanterade nycklar. Om du vill ha ytterligare kontroll över krypteringsnycklar kan du hantera dina egna nycklar. Kund hanterade nycklar måste lagras i Azure Key Vault eller Key Vault HSM (Managed Hardware Security Model) (förhandsversion).

Den här artikeln visar hur du konfigurerar kryptering med kund hanterade nycklar som lagras i ett nyckelvalv med hjälp av Azure Portal, PowerShell eller Azure CLI. Information om hur du konfigurerar kryptering med kund hanterade nycklar som lagras i en hanterad HSM finns i Konfigurera kryptering med kund hanterade nycklar som lagras [i Azure Key Vault Managed HSM (förhandsversion).](customer-managed-keys-configure-key-vault-hsm.md)

> [!NOTE]
> Azure Key Vault och Azure Key Vault Managed HSM stöder samma API:er och hanteringsgränssnitt för konfiguration.

## <a name="configure-a-key-vault"></a>Konfigurera ett nyckelvalv

Du kan använda ett nytt eller befintligt nyckelvalv för att lagra kund hanterade nycklar. Lagringskontot och nyckelvalvet måste finnas i samma region, men de kan finnas i olika prenumerationer.

Om du använder kund hanterade nycklar Azure Storage kryptering måste både mjuk borttagning och rensning aktiveras för nyckelvalvet. Mjuk borttagning är aktiverat som standard när du skapar ett nytt nyckelvalv och kan inte inaktiveras. Du kan aktivera rensningsskydd när du skapar nyckelvalvet eller när det har skapats.

# <a name="azure-portal"></a>[Azure-portalen](#tab/portal)

Information om hur du skapar ett nyckelvalv med Azure Portal finns i [Snabbstart: Skapa](../../key-vault/general/quick-create-portal.md)ett nyckelvalv med hjälp av Azure Portal . När du skapar nyckelvalvet väljer **du Aktivera rensningsskydd**, som du ser i följande bild.

:::image type="content" source="media/customer-managed-keys-configure-key-vault/configure-key-vault-portal.png" alt-text="Skärmbild som visar hur du aktiverar rensningsskydd när du skapar ett nyckelvalv":::

Följ dessa steg om du vill aktivera rensningsskydd för ett befintligt nyckelvalv:

1. Gå till ditt nyckelvalv i Azure Portal.
1. Under **Inställningar** väljer du **Egenskaper.**
1. I avsnittet **Rensa skydd** väljer du **Aktivera rensningsskydd.**

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Om du vill skapa ett nytt nyckelvalv med PowerShell installerar du version 2.0.0 eller senare av PowerShell-modulen [Az.KeyVault.](https://www.powershellgallery.com/packages/Az.KeyVault/2.0.0) Anropa sedan [New-AzKeyVault för](/powershell/module/az.keyvault/new-azkeyvault) att skapa ett nytt nyckelvalv. Med version 2.0.0 och senare av Az.KeyVault-modulen är mjuk borttagning aktiverat som standard när du skapar ett nytt nyckelvalv.

I följande exempel skapas ett nytt nyckelvalv med både mjuk borttagning och rensning aktiverat. Kom ihåg att ersätta platshållarvärdena inom hakparentes med dina egna värden.

```powershell
$keyVault = New-AzKeyVault -Name <key-vault> `
    -ResourceGroupName <resource_group> `
    -Location <location> `
    -EnablePurgeProtection
```

Information om hur du aktiverar rensningsskydd för ett befintligt nyckelvalv med PowerShell finns i Så här använder du [mjuk borttagning med PowerShell.](../../key-vault/general/key-vault-recovery.md)

Tilldela sedan en system tilldelad hanterad identitet till ditt lagringskonto. Du använder den här hanterade identiteten för att ge lagringskontot behörighet att komma åt nyckelvalvet. Mer information om system tilldelade hanterade identiteter finns i [Vad är hanterade identiteter för Azure-resurser?](../../active-directory/managed-identities-azure-resources/overview.md).

Om du vill tilldela en hanterad identitet med hjälp av PowerShell [anropar du Set-AzStorageAccount:](/powershell/module/az.storage/set-azstorageaccount)

```powershell
$storageAccount = Set-AzStorageAccount -ResourceGroupName <resource_group> `
    -Name <storage-account> `
    -AssignIdentity
```

Slutligen konfigurerar du åtkomstprincipen för nyckelvalvet så att lagringskontot har behörighet att komma åt det. I det här steget använder du den hanterade identitet som du tidigare tilldelade till lagringskontot.

Ange åtkomstprincipen för nyckelvalvet genom att anropa [Set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy):

```powershell
Set-AzKeyVaultAccessPolicy `
    -VaultName $keyVault.VaultName `
    -ObjectId $storageAccount.Identity.PrincipalId `
    -PermissionsToKeys wrapkey,unwrapkey,get
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Om du vill skapa ett nytt nyckelvalv med Azure CLI anropar [du az keyvault create](/cli/azure/keyvault#az_keyvault_create). Kom ihåg att ersätta platshållarvärdena inom hakparentes med dina egna värden:

```azurecli-interactive
az keyvault create \
    --name <key-vault> \
    --resource-group <resource_group> \
    --location <region> \
    --enable-purge-protection
```

Information om hur du aktiverar rensningsskydd för ett befintligt nyckelvalv med Azure CLI finns i Använda [mjuk borttagning med CLI](../../key-vault/general/key-vault-recovery.md).

Tilldela sedan en system tilldelad hanterad identitet till lagringskontot. Du använder den här hanterade identiteten för att ge lagringskontot behörighet att komma åt nyckelvalvet. Mer information om system tilldelade hanterade identiteter finns i [Vad är hanterade identiteter för Azure-resurser?](../../active-directory/managed-identities-azure-resources/overview.md).

Om du vill tilldela en hanterad identitet med hjälp av Azure CLI [anropar du az storage account update](/cli/azure/storage/account#az_storage_account_update):

```azurecli-interactive
az storage account update \
    --name <storage-account> \
    --resource-group <resource_group> \
    --assign-identity
```

Slutligen konfigurerar du åtkomstprincipen för nyckelvalvet så att lagringskontot har behörighet att komma åt det. I det här steget använder du den hanterade identitet som du tidigare tilldelade till lagringskontot.

Ange åtkomstprincipen för nyckelvalvet genom att anropa [az keyvault set-policy](/cli/azure/keyvault#az_keyvault_set_policy):

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

## <a name="add-a-key"></a>Lägga till en nyckel

Lägg sedan till en nyckel i nyckelvalvet.

Azure Storage kryptering stöder RSA- och RSA-HSM-nycklar av storlekarna 2048, 3072 och 4096. Mer information om nycklar finns i [Om nycklar.](../../key-vault/keys/about-keys.md)

# <a name="azure-portal"></a>[Azure-portalen](#tab/portal)

Information om hur du lägger till en nyckel med Azure Portal finns i [Snabbstart: Ange](../../key-vault/keys/quick-create-portal.md)och hämta en nyckel från Azure Key Vault med hjälp av Azure Portal .

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Om du vill lägga till en nyckel med PowerShell [anropar du Add-AzKeyVaultKey](/powershell/module/az.keyvault/add-azkeyvaultkey). Kom ihåg att ersätta platshållarvärdena inom hakparentes med dina egna värden och att använda variablerna som definierats i föregående exempel.

```powershell
$key = Add-AzKeyVaultKey -VaultName $keyVault.VaultName `
    -Name <key> `
    -Destination 'Software'
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Om du vill lägga till en nyckel med Azure CLI [anropar du az keyvault key create](/cli/azure/keyvault/key#az_keyvault_key_create). Kom ihåg att ersätta platshållarvärdena inom hakparentes med dina egna värden.

```azurecli-interactive
az keyvault key create \
    --name <key> \
    --vault-name <key-vault>
```

---

## <a name="configure-encryption-with-customer-managed-keys"></a>Konfigurera kryptering med kundhanterade nycklar

Konfigurera sedan ditt Azure Storage att använda kund hanterade nycklar med Azure Key Vault och ange sedan den nyckel som ska associeras med lagringskontot.

När du konfigurerar kryptering med kund hanterade nycklar kan du välja att automatiskt uppdatera nyckelversionen som används för Azure Storage-kryptering när en ny version är tillgänglig i det associerade nyckelvalvet. Alternativt kan du uttryckligen ange en nyckelversion som ska användas för kryptering tills nyckelversionen uppdateras manuellt.

> [!NOTE]
> Om du vill rotera en nyckel skapar du en ny version av nyckeln i Azure Key Vault. Azure Storage hanterar inte rotationen av nyckeln i Azure Key Vault, så du måste rotera nyckeln manuellt eller skapa en funktion för att rotera den enligt ett schema.

### <a name="configure-encryption-for-automatic-updating-of-key-versions"></a>Konfigurera kryptering för automatisk uppdatering av nyckelversioner

Azure Storage kan automatiskt uppdatera den kund hanterade nyckeln som används för kryptering för att använda den senaste nyckelversionen. När den kund hanterade nyckeln roteras i Azure Key Vault Azure Storage automatiskt med den senaste versionen av nyckeln för kryptering.

# <a name="azure-portal"></a>[Azure-portalen](#tab/portal)

Följ dessa steg om du vill konfigurera kund hanterade nycklar med automatisk uppdatering av Azure Portal nyckelversionen i den här artikeln:

1. Navigera till ditt lagringskonto.
1. På **bladet Inställningar** för lagringskontot klickar du på **Kryptering.** Som standard är nyckelhantering inställt på **Microsoft Managed Keys**, som du ser i följande bild.

    ![Skärmbild av portalen som visar krypteringsalternativ](./media/customer-managed-keys-configure-key-vault/portal-configure-encryption-keys.png)

1. Välj alternativet **Kund hanterade** nycklar.
1. Välj alternativet **Välj Key Vault** alternativ.
1. Välj **Välj ett nyckelvalv och en nyckel.**
1. Välj det nyckelvalv som innehåller den nyckel som du vill använda.
1. Välj nyckeln från nyckelvalvet.

   ![Skärmbild som visar hur du väljer nyckelvalv och nyckel](./media/customer-managed-keys-configure-key-vault/portal-select-key-from-key-vault.png)

1. Spara ändringarna.

När du har angett nyckeln anger Azure Portal att automatisk uppdatering av nyckelversionen är aktiverat och visar den nyckelversion som för närvarande används för kryptering.

:::image type="content" source="media/customer-managed-keys-configure-key-vault/portal-auto-rotation-enabled.png" alt-text="Skärmbild som visar automatisk uppdatering av nyckelversionen aktiverad":::

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Om du vill konfigurera kund hanterade nycklar med automatisk uppdatering av nyckelversionen med PowerShell installerar du [Az.Storage-modulen](https://www.powershellgallery.com/packages/Az.Storage) version 2.0.0 eller senare.

Om du vill uppdatera nyckelversionen för en kund hanterad nyckel automatiskt utelämnar du nyckelversionen när du konfigurerar kryptering med kund hanterade nycklar för lagringskontot. Anropa [Set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount) för att uppdatera lagringskontots krypteringsinställningar, som du ser i följande exempel, och inkludera alternativet **-KeyvaultEncryption** för att aktivera kund hanterade nycklar för lagringskontot.

Kom ihåg att ersätta platshållarvärdena inom hakparentes med dina egna värden och att använda variablerna som definierades i föregående exempel.

```powershell
Set-AzStorageAccount -ResourceGroupName $storageAccount.ResourceGroupName `
    -AccountName $storageAccount.StorageAccountName `
    -KeyvaultEncryption `
    -KeyName $key.Name `
    -KeyVaultUri $keyVault.VaultUri
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Om du vill konfigurera kund hanterade nycklar med automatisk uppdatering av nyckelversionen med Azure CLI installerar du [Azure CLI version 2.4.0](/cli/azure/release-notes-azure-cli#april-21-2020) eller senare. Mer information finns i [Installera Azure CLI.](/cli/azure/install-azure-cli)

Om du vill uppdatera nyckelversionen för en kund hanterad nyckel automatiskt utelämnar du nyckelversionen när du konfigurerar kryptering med kund hanterade nycklar för lagringskontot. Anropa [az storage account update](/cli/azure/storage/account#az_storage_account_update) för att uppdatera lagringskontots krypteringsinställningar, som du ser i följande exempel. Inkludera `--encryption-key-source` parametern och ställ in den `Microsoft.Keyvault` på för att aktivera kund hanterade nycklar för kontot.

Kom ihåg att ersätta platshållarvärdena inom hakparentes med dina egna värden.

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

### <a name="configure-encryption-for-manual-updating-of-key-versions"></a>Konfigurera kryptering för manuell uppdatering av nyckelversioner

Om du föredrar att uppdatera nyckelversionen manuellt anger du uttryckligen versionen vid den tidpunkt då du konfigurerar kryptering med kund hanterade nycklar. I det här Azure Storage inte automatiskt nyckelversionen när en ny version skapas i nyckelvalvet. Om du vill använda en ny nyckelversion måste du manuellt uppdatera den version som används för Azure Storage kryptering.

# <a name="azure-portal"></a>[Azure-portalen](#tab/portal)

Om du vill konfigurera kund hanterade nycklar med manuell uppdatering av nyckelversionen i Azure Portal anger du nyckel-URI, inklusive versionen. Följ dessa steg om du vill ange en nyckel som en URI:

1. För att hitta nyckel-URI i Azure Portal navigerar du till nyckelvalvet och väljer **inställningen** Nycklar. Välj önskad nyckel och klicka sedan på nyckeln för att visa dess versioner. Välj en nyckelversion för att visa inställningarna för den versionen.
1. Kopiera värdet för fältet **Nyckelidentifierare,** som tillhandahåller URI:en.

    ![Skärmbild som visar nyckel-URI för nyckelvalv](media/customer-managed-keys-configure-key-vault/portal-copy-key-identifier.png)

1. I inställningarna **för krypteringsnyckel** för ditt lagringskonto väljer du alternativet **Ange nyckel-URI.**
1. Klistra in den URI som du kopierade i **fältet Nyckel-URI.** Utelämna nyckelversionen från URI:en för att aktivera automatisk uppdatering av nyckelversionen.

   ![Skärmbild som visar hur du anger nyckel-URI](./media/customer-managed-keys-configure-key-vault/portal-specify-key-uri.png)

1. Ange den prenumeration som innehåller nyckelvalvet.
1. Spara ändringarna.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Om du vill konfigurera kund hanterade nycklar med manuell uppdatering av nyckelversionen anger du uttryckligen nyckelversionen när du konfigurerar kryptering för lagringskontot. Anropa [Set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount) för att uppdatera lagringskontots krypteringsinställningar, som du ser i följande exempel, och inkludera alternativet **-KeyvaultEncryption** för att aktivera kund hanterade nycklar för lagringskontot.

Kom ihåg att ersätta platshållarvärdena inom hakparentes med dina egna värden och att använda variablerna som definierades i föregående exempel.

```powershell
Set-AzStorageAccount -ResourceGroupName $storageAccount.ResourceGroupName `
    -AccountName $storageAccount.StorageAccountName `
    -KeyvaultEncryption `
    -KeyName $key.Name `
    -KeyVersion $key.Version `
    -KeyVaultUri $keyVault.VaultUri
```

När du uppdaterar nyckelversionen manuellt måste du uppdatera lagringskontots krypteringsinställningar för att använda den nya versionen. Anropa först [Get-AzKeyVaultKey för](/powershell/module/az.keyvault/get-azkeyvaultkey) att hämta den senaste versionen av nyckeln. Anropa sedan [Set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount) för att uppdatera lagringskontots krypteringsinställningar för att använda den nya versionen av nyckeln, som du ser i föregående exempel.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Om du vill konfigurera kund hanterade nycklar med manuell uppdatering av nyckelversionen anger du uttryckligen nyckelversionen när du konfigurerar kryptering för lagringskontot. Anropa [az storage account update](/cli/azure/storage/account#az_storage_account_update) för att uppdatera lagringskontots krypteringsinställningar, som du ser i följande exempel. Inkludera `--encryption-key-source` parametern och ställ in den `Microsoft.Keyvault` på för att aktivera kund hanterade nycklar för kontot.

Kom ihåg att ersätta platshållarvärdena inom hakparentes med dina egna värden.

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

När du uppdaterar nyckelversionen manuellt måste du uppdatera lagringskontots krypteringsinställningar för att använda den nya versionen. Fråga först efter nyckelvalvs-URI:en genom att anropa [az keyvault show](/cli/azure/keyvault#az_keyvault_show)och för nyckelversionen genom att anropa [az keyvault key list-versions](/cli/azure/keyvault/key#az_keyvault_key_list-versions). Anropa sedan [az storage account update](/cli/azure/storage/account#az_storage_account_update) för att uppdatera lagringskontots krypteringsinställningar för att använda den nya versionen av nyckeln, som du ser i föregående exempel.

---

## <a name="change-the-key"></a>Ändra nyckeln

Du kan ändra den nyckel som du använder för Azure Storage kryptering när som helst.

# <a name="azure-portal"></a>[Azure-portalen](#tab/portal)

Följ dessa steg om du Azure Portal nyckeln med följande steg:

1. Gå till ditt lagringskonto och visa **krypteringsinställningarna.**
1. Välj nyckelvalvet och välj en ny nyckel.
1. Spara ändringarna.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Om du vill ändra nyckeln med PowerShell anropar du [Set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount) enligt konfigurerar kryptering med kund hanterade [nycklar](#configure-encryption-with-customer-managed-keys) och anger det nya nyckelnamnet och versionen. Om den nya nyckeln finns i ett annat nyckelvalv måste du också uppdatera nyckelvalvs-URI:t.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Om du vill ändra nyckeln med Azure CLI [](#configure-encryption-with-customer-managed-keys) anropar du [az storage account update](/cli/azure/storage/account#az_storage_account_update) som du ser i Konfigurera kryptering med kund hanterade nycklar och anger det nya nyckelnamnet och versionen. Om den nya nyckeln finns i ett annat nyckelvalv måste du också uppdatera nyckelvalvs-URI:t.

---

## <a name="revoke-customer-managed-keys"></a>Återkalla kund hanterade nycklar

Om du återkallar en kund hanterad nyckel tas associationen mellan lagringskontot och nyckelvalvet bort.

# <a name="azure-portal"></a>[Azure-portalen](#tab/portal)

Om du vill återkalla kund hanterade nycklar med Azure Portal inaktiverar du nyckeln enligt beskrivningen i [Inaktivera kund hanterade nycklar.](#disable-customer-managed-keys)

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Du kan återkalla kund hanterade nycklar genom att ta bort åtkomstprincipen för nyckelvalvet. Om du vill återkalla en kund hanterad nyckel med PowerShell anropar du kommandot [Remove-AzKeyVaultAccessPolicy,](/powershell/module/az.keyvault/remove-azkeyvaultaccesspolicy) som du ser i följande exempel. Kom ihåg att ersätta platshållarvärdena inom hakparentes med dina egna värden och att använda variablerna som definierats i föregående exempel.

```powershell
Remove-AzKeyVaultAccessPolicy -VaultName $keyVault.VaultName `
    -ObjectId $storageAccount.Identity.PrincipalId `
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Du kan återkalla kund hanterade nycklar genom att ta bort åtkomstprincipen för nyckelvalvet. Om du vill återkalla en kund hanterad nyckel med Azure CLI anropar du [kommandot az keyvault delete-policy,](/cli/azure/keyvault#az_keyvault_delete_policy) som du ser i följande exempel. Kom ihåg att ersätta platshållarvärdena inom hakparentes med dina egna värden och att använda variablerna som definierats i föregående exempel.

```azurecli-interactive
az keyvault delete-policy \
    --name <key-vault> \
    --object-id $storage_account_principal
```

---

## <a name="disable-customer-managed-keys"></a>Inaktivera kund hanterade nycklar

När du inaktiverar kund hanterade nycklar krypteras lagringskontot återigen med Microsoft-hanterade nycklar.

# <a name="azure-portal"></a>[Azure-portalen](#tab/portal)

Om du vill inaktivera kund hanterade nycklar i Azure Portal följer du dessa steg:

1. Gå till ditt lagringskonto och visa **krypteringsinställningarna.**
1. Avmarkera kryssrutan bredvid inställningen **Använd din egen** nyckel.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Om du vill inaktivera kund hanterade nycklar med PowerShell anropar du [Set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount) med alternativet , som `-StorageEncryption` du ser i följande exempel. Kom ihåg att ersätta platshållarvärdena inom hakparentes med dina egna värden och att använda variablerna som definierades i föregående exempel.

```powershell
Set-AzStorageAccount -ResourceGroupName $storageAccount.ResourceGroupName `
    -AccountName $storageAccount.StorageAccountName `
    -StorageEncryption  
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Om du vill inaktivera kund hanterade nycklar med Azure CLI anropar du [az storage account update](/cli/azure/storage/account#az_storage_account_update) och anger till , som du ser i följande `--encryption-key-source parameter` `Microsoft.Storage` exempel. Kom ihåg att ersätta platshållarvärdena inom hakparentes med dina egna värden och att använda variablerna som definierades i föregående exempel.

```azurecli-interactive
az storage account update
    --name <storage-account> \
    --resource-group <resource_group> \
    --encryption-key-source Microsoft.Storage
```

---

## <a name="next-steps"></a>Nästa steg

- [Azure Storage-kryptering av vilande data](storage-service-encryption.md)
- [Kund-hanterade nycklar för Azure Storage kryptering](customer-managed-keys-overview.md)
- [Konfigurera kryptering med kund hanterade nycklar som lagras i Azure Key Vault Managed HSM (förhandsversion)](customer-managed-keys-configure-key-vault-hsm.md)
