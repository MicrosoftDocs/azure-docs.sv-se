---
title: Skapa och hantera krypteringsomfång
description: Lär dig hur du skapar ett krypteringsomfång för att isolera blobdata på container- eller blobnivå.
services: storage
author: tamram
ms.service: storage
ms.date: 03/26/2021
ms.topic: conceptual
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.openlocfilehash: 656443b0bc9d0e45f43634b1b4c21145de7a5bb5
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107792551"
---
# <a name="create-and-manage-encryption-scopes"></a>Skapa och hantera krypteringsomfång

Med krypteringsomfång kan du hantera kryptering på enskild blob-nivå eller behållarnivå. Du kan använda krypteringsomfång för att skapa säkra gränser mellan data som finns i samma lagringskonto men som tillhör olika kunder. Mer information om krypteringsomfång finns i [Krypteringsomfång för Blob Storage.](encryption-scope-overview.md)

Den här artikeln visar hur du skapar ett krypteringsomfång. Den visar också hur du anger ett krypteringsomfång när du skapar en blob eller container.

[!INCLUDE [storage-data-lake-gen2-support](../../../includes/storage-data-lake-gen2-support.md)]

## <a name="create-an-encryption-scope"></a>Skapa ett krypteringsomfång

Du kan skapa ett krypteringsomfång som skyddas med en Microsoft-hanterad nyckel eller med en kund hanterad nyckel som lagras i en Azure Key Vault eller i en Azure Key Vault Managed Hardware Security Model (HSM) (förhandsversion). Om du vill skapa ett krypteringsomfång med en kund hanterad nyckel måste du först skapa ett nyckelvalv eller en hanterad HSM och lägga till den nyckel som du tänker använda för omfånget. Nyckelvalvet eller hanterad HSM måste ha rensningsskydd aktiverat och måste finnas i samma region som lagringskontot.

Ett krypteringsomfång aktiveras automatiskt när du skapar det. När du har skapat krypteringsomfånget kan du ange det när du skapar en blob. Du kan också ange ett standardkrypteringsomfång när du skapar en container, som automatiskt gäller för alla blobar i containern.

# <a name="portal"></a>[Portal](#tab/portal)

Följ dessa steg om du vill skapa Azure Portal krypteringsomfång i den här Azure Portal:

1. Navigera till ditt lagringskonto i Azure-portalen.
1. Välj **krypteringsinställningen.**
1. Välj **fliken Krypteringsomfång.**
1. Klicka på knappen **Lägg** till för att lägga till ett nytt krypteringsomfång.
1. I fönstret **Skapa krypteringsomfång** anger du ett namn för det nya omfånget.
1. Välj önskad typ av stöd för krypteringsnyckeln, **antingen Microsoft-hanterade nycklar** **eller Kund-hanterade nycklar.**
    - Om du har valt **Microsoft-hanterade nycklar** klickar du på **Skapa för** att skapa krypteringsomfånget.
    - Om du har **valt Kund hanterade** nycklar väljer du en prenumeration och anger ett nyckelvalv eller en hanterad HSM och en nyckel som ska användas för det här krypteringsomfånget, enligt följande bild.

    :::image type="content" source="media/encryption-scope-manage/create-encryption-scope-customer-managed-key-portal.png" alt-text="Skärmbild som visar hur du skapar krypteringsomfång i Azure Portal":::

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Om du vill skapa ett krypteringsomfång med PowerShell installerar du [Az.Storage](https://www.powershellgallery.com/packages/Az.Storage) PowerShell-modulen version 3.4.0 eller senare.

### <a name="create-an-encryption-scope-protected-by-microsoft-managed-keys"></a>Skapa ett krypteringsomfång som skyddas av Microsoft-hanterade nycklar

Om du vill skapa ett nytt krypteringsomfång som skyddas av Microsoft-hanterade nycklar anropar du kommandot **New-AzStorageEncryptionScope** med `-StorageEncryption` parametern .

Kom ihåg att ersätta platshållarvärdena i exemplet med dina egna värden:

```powershell
$rgName = "<resource-group>"
$accountName = "<storage-account>"
$scopeName1 = "customer1scope"

New-AzStorageEncryptionScope -ResourceGroupName $rgName `
    -AccountName $accountName `
    -EncryptionScopeName $scopeName1 `
    -StorageEncryption
```

### <a name="create-an-encryption-scope-protected-by-customer-managed-keys"></a>Skapa ett krypteringsomfång som skyddas av kund hanterade nycklar

Om du vill skapa ett nytt krypteringsomfång som skyddas av kund hanterade nycklar som lagras i ett nyckelvalv eller en hanterad HSM konfigurerar du först kund hanterade nycklar för lagringskontot. Du måste tilldela en hanterad identitet till lagringskontot och sedan använda den hanterade identiteten för att konfigurera åtkomstprincipen för nyckelvalvet eller hanterad HSM så att lagringskontot har behörighet att komma åt det.

Om du vill konfigurera kund hanterade nycklar för användning med ett krypteringsomfång måste rensningsskydd vara aktiverat på nyckelvalvet eller hanterad HSM. Nyckelvalvet eller den hanterade HSM:en måste finnas i samma region som lagringskontot.

Kom ihåg att ersätta platshållarvärdena i exemplet med dina egna värden:

```powershell
$rgName = "<resource-group>"
$accountName = "<storage-account>"
$keyVaultName = "<key-vault>"
$keyUri = "<key-uri>"
$scopeName2 = "customer2scope"

# Assign a system managed identity to the storage account.
$storageAccount = Set-AzStorageAccount -ResourceGroupName $rgName `
    -Name $accountName `
    -AssignIdentity

# Configure the access policy for the key vault.
Set-AzKeyVaultAccessPolicy `
    -VaultName $keyVaultName `
    -ObjectId $storageAccount.Identity.PrincipalId `
    -PermissionsToKeys wrapkey,unwrapkey,get
```

Anropa sedan kommandot **New-AzStorageEncryptionScope** med `-KeyvaultEncryption` parametern och ange nyckel-URI:en. Det är valfritt att inkludera nyckelversionen på nyckel-URI:en. Om du utelämnar nyckelversionen använder krypteringsomfånget automatiskt den senaste nyckelversionen. Om du inkluderar nyckelversionen måste du uppdatera nyckelversionen manuellt för att använda en annan version.

Kom ihåg att ersätta platshållarvärdena i exemplet med dina egna värden:

```powershell
New-AzStorageEncryptionScope -ResourceGroupName $rgName `
    -AccountName $accountName `
    -EncryptionScopeName $scopeName2 `
    -KeyUri $keyUri `
    -KeyvaultEncryption
```

# <a name="azure-cli"></a>[Azure CLI](#tab/cli)

Om du vill skapa ett krypteringsomfång med Azure CLI installerar du först Azure CLI version 2.20.0 eller senare.

### <a name="create-an-encryption-scope-protected-by-microsoft-managed-keys"></a>Skapa ett krypteringsomfång som skyddas av Microsoft-hanterade nycklar

Om du vill skapa ett nytt krypteringsomfång som skyddas av Microsoft-hanterade nycklar anropar du kommandot [az storage account encryption-scope create](/cli/azure/storage/account/encryption-scope#az_storage_account_encryption_scope_create) och anger `--key-source` parametern som `Microsoft.Storage` . Kom ihåg att ersätta platshållarvärdena med dina egna värden:

```azurecli-interactive
az storage account encryption-scope create \
    --resource-group <resource-group> \
    --account-name <storage-account> \
    --name <scope> \
    --key-source Microsoft.Storage
```

### <a name="create-an-encryption-scope-protected-by-customer-managed-keys"></a>Skapa ett krypteringsomfång som skyddas av kund hanterade nycklar

Om du vill skapa ett nytt krypteringsomfång som skyddas av Microsoft-hanterade nycklar anropar du kommandot [az storage account encryption-scope create](/cli/azure/storage/account/encryption-scope#az_storage_account_encryption_scope_create) och anger `--key-source` parametern som `Microsoft.Storage` . Kom ihåg att ersätta platshållarvärdena med dina egna värden:

Om du vill skapa ett nytt krypteringsomfång som skyddas av kund hanterade nycklar i ett nyckelvalv eller en hanterad HSM konfigurerar du först kund hanterade nycklar för lagringskontot. Du måste tilldela en hanterad identitet till lagringskontot och sedan använda den hanterade identiteten för att konfigurera åtkomstprincipen för nyckelvalvet så att lagringskontot har behörighet att komma åt det. Mer information finns i [Kund-hanterade nycklar för Azure Storage kryptering.](../common/customer-managed-keys-overview.md)

Om du vill konfigurera kund hanterade nycklar för användning med ett krypteringsomfång måste rensningsskydd vara aktiverat på nyckelvalvet eller hanterad HSM. Nyckelvalvet eller den hanterade HSM:en måste finnas i samma region som lagringskontot.

Kom ihåg att ersätta platshållarvärdena i exemplet med dina egna värden:

```azurecli-interactive
az login
az account set --subscription <subscription-id>

az storage account update \
    --name <storage-account> \
    --resource-group <resource_group> \
    --assign-identity

storage_account_principal=$(az storage account show \
    --name <storage-account> \
    --resource-group <resource-group> \
    --query identity.principalId \
    --output tsv)

az keyvault set-policy \
    --name <key-vault> \
    --resource-group <resource_group> \
    --object-id $storage_account_principal \
    --key-permissions get unwrapKey wrapKey
```

Anropa sedan kommandot **az storage account encryption-scope create** med `--key-uri` parametern och ange nyckelns URI. Det är valfritt att inkludera nyckelversionen på nyckel-URI:en. Om du utelämnar nyckelversionen använder krypteringsomfånget automatiskt den senaste nyckelversionen. Om du inkluderar nyckelversionen måste du uppdatera nyckelversionen manuellt för att använda en annan version.

Kom ihåg att ersätta platshållarvärdena i exemplet med dina egna värden:

```azurecli-interactive
az storage account encryption-scope create \
    --resource-group <resource-group> \
    --account-name <storage-account> \
    --name <scope> \
    --key-source Microsoft.KeyVault \
    --key-uri <key-uri>
```

---

Information om hur du konfigurerar Azure Storage med kund hanterade nycklar i ett nyckelvalv eller en hanterad HSM finns i följande artiklar:

- [Konfigurera kryptering med kundhanterade nycklar som lagras i Azure Key Vault](../common/customer-managed-keys-configure-key-vault.md)
- [Konfigurera kryptering med kund hanterade nycklar som lagras i Azure Key Vault Managed HSM (förhandsversion).](../common/customer-managed-keys-configure-key-vault-hsm.md)

## <a name="list-encryption-scopes-for-storage-account"></a>Lista krypteringsomfång för lagringskonto

# <a name="portal"></a>[Portal](#tab/portal)

Om du vill visa krypteringsomfången för ett lagringskonto i Azure Portal navigerar du till inställningen **Krypteringsomfång** för lagringskontot. I det här fönstret kan du aktivera eller inaktivera ett krypteringsomfång eller ändra nyckeln för ett krypteringsomfång.

:::image type="content" source="media/encryption-scope-manage/list-encryption-scopes-portal.png" alt-text="Skärmbild som visar en lista över krypteringsomfång i Azure Portal":::

Om du vill visa information om en kund hanterad nyckel, inklusive nyckel-URI och version och om nyckelversionen uppdateras automatiskt, följer du länken i **kolumnen** Nyckel.

:::image type="content" source="media/encryption-scope-manage/customer-managed-key-details-portal.png" alt-text="Skärmbild som visar information om en nyckel som används med ett krypteringsomfång":::

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Om du vill visa en lista över de krypteringsomfång som är tillgängliga för ett lagringskonto med PowerShell anropar du kommandot **Get-AzStorageEncryptionScope.** Kom ihåg att ersätta platshållarvärdena i exemplet med dina egna värden:

```powershell
Get-AzStorageEncryptionScope -ResourceGroupName $rgName `
    -StorageAccountName $accountName
```

Om du vill visa en lista över alla krypteringsomfång i en resursgrupp efter lagringskonto använder du pipelinesyntaxen:

```powershell
Get-AzStorageAccount -ResourceGroupName $rgName | Get-AzStorageEncryptionScope
```

# <a name="azure-cli"></a>[Azure CLI](#tab/cli)

Om du vill visa en lista över de krypteringsomfång som är tillgängliga för ett lagringskonto med Azure CLI anropar du kommandot [az storage account encryption-scope list.](/cli/azure/storage/account/encryption-scope#az_storage_account_encryption_scope_list) Kom ihåg att ersätta platshållarvärdena i exemplet med dina egna värden:

```azurecli-interactive
az storage account encryption-scope list \
    --account-name <storage-account> \
    --resource-group <resource-group>
```

---

## <a name="create-a-container-with-a-default-encryption-scope"></a>Skapa en container med ett standardkrypteringsomfång

När du skapar en container kan du ange ett standardkrypteringsomfång. Blobar i containern använder det omfånget som standard.

En enskild blob kan skapas med ett eget krypteringsomfång, såvida inte containern är konfigurerad för att kräva att alla blobar använder standardomfånget. Mer information finns i [Krypteringsomfång för containrar och blobar.](encryption-scope-overview.md#encryption-scopes-for-containers-and-blobs)

# <a name="portal"></a>[Portal](#tab/portal)

Om du vill skapa en container med ett standardkrypteringsomfång i Azure Portal skapar du först krypteringsomfånget enligt beskrivningen [i Skapa ett krypteringsomfång](#create-an-encryption-scope). Följ sedan de här stegen för att skapa containern:

1. Navigera till listan över containrar i ditt lagringskonto och välj knappen **Lägg till** för att skapa en ny container.
1. Expandera Avancerade **inställningar** i fönstret **Ny** container.
1. I **listrutan Krypteringsomfång** väljer du standardkrypteringsomfånget för containern.
1. Om du vill kräva att alla blobar i containern använder standardkrypteringsomfånget markerar du kryssrutan för att använda det här krypteringsomfånget för alla **blobar i containern**. Om den här kryssrutan är markerad kan en enskild blob i containern inte åsidosätta standardkrypteringsomfånget.

    :::image type="content" source="media/encryption-scope-manage/create-container-default-encryption-scope.png" alt-text="Skärmbild som visar container med standardkrypteringsomfång":::

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Om du vill skapa en container med ett standardkrypteringsomfång med PowerShell anropar du kommandot [New-AzStorageContainer](/powershell/module/az.storage/new-azstoragecontainer) och anger omfånget för `-DefaultEncryptionScope` parametern . Om du vill tvinga alla blobar i en container att använda containerns standardomfång anger du `-PreventEncryptionScopeOverride` parametern till `true` .

```powershell
$containerName1 = "container1"
$ctx = New-AzStorageContext -StorageAccountName $accountName -UseConnectedAccount

# Create a container with a default encryption scope that cannot be overridden.
New-AzStorageContainer -Name $containerName1 `
    -Context $ctx `
    -DefaultEncryptionScope $scopeName1 `
    -PreventEncryptionScopeOverride $true
```

# <a name="azure-cli"></a>[Azure CLI](#tab/cli)

Om du vill skapa en container med ett standardkrypteringsomfång med Azure CLI anropar du kommandot [az storage container create](/cli/azure/storage/container#az_storage_container_create) och anger omfånget för `--default-encryption-scope` parametern . Om du vill tvinga alla blobar i en container att använda containerns standardomfång anger du `--prevent-encryption-scope-override` parametern till `true` .

I följande exempel används ditt Azure AD-konto för att auktorisera åtgärden för att skapa containern. Du kan också använda åtkomstnyckeln för kontot. Mer information finns i [Auktorisera åtkomst till blob- eller ködata med Azure CLI.](./authorize-data-operations-cli.md)

```azurecli-interactive
az storage container create \
    --account-name <storage-account> \
    --resource-group <resource-group> \
    --name <container> \
    --default-encryption-scope <scope> \
    --prevent-encryption-scope-override true \
    --auth-mode login
```

---

Om en klient försöker ange ett omfång när en blob laddas upp till en container som har ett standardkrypteringsomfång och containern är konfigurerad för att förhindra att blobar åsidosätter standardomfånget, misslyckas åtgärden med ett meddelande som anger att begäran är förbjuden av containerkrypteringsprincipen.

## <a name="upload-a-blob-with-an-encryption-scope"></a>Ladda upp en blob med ett krypteringsomfång

När du laddar upp en blob kan du ange ett krypteringsomfång för bloben eller använda standardkrypteringsomfånget för containern, om ett sådant har angetts.

# <a name="portal"></a>[Portal](#tab/portal)

Om du vill ladda upp en blob med ett krypteringsomfång via Azure Portal skapar du först krypteringsomfånget enligt beskrivningen [i Skapa ett krypteringsomfång](#create-an-encryption-scope). Följ sedan de här stegen för att skapa bloben:

1. Navigera till den container som du vill ladda upp bloben till.
1. Välj knappen **Ladda** upp och leta upp bloben som ska laddas upp.
1. Expandera Avancerade **inställningar** i fönstret **Ladda upp blob.**
1. Leta upp **listrutan** Krypteringsomfång. Som standard skapas bloben med standardkrypteringsomfånget för containern, om en sådan har angetts. Om containern kräver att blobar använder standardkrypteringsomfånget inaktiveras det här avsnittet.
1. Om du vill ange ett annat omfång för bloben som du laddar upp väljer du Välj ett befintligt omfång och sedan önskat omfång i listrutan.

    :::image type="content" source="media/encryption-scope-manage/upload-blob-encryption-scope.png" alt-text="Skärmbild som visar hur du laddar upp en blob med ett krypteringsomfång":::

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Om du vill ladda upp en blob med ett krypteringsomfång via PowerShell anropar du kommandot [Set-AzStorageBlobContent](/powershell/module/az.storage/set-azstorageblobcontent) och anger blobens krypteringsomfång.

```powershell
$containerName2 = "container2"
$localSrcFile = "C:\temp\helloworld.txt"
$ctx = New-AzStorageContext -StorageAccountName $accountName -UseConnectedAccount

# Create a new container with no default scope defined.
New-AzStorageContainer -Name $containerName2 -Context $ctx

# Upload a block upload with an encryption scope specified.
Set-AzStorageBlobContent -Context $ctx `
    -Container $containerName2 `
    -File $localSrcFile `
    -Blob "helloworld.txt" `
    -BlobType Block `
    -EncryptionScope $scopeName2
```

# <a name="azure-cli"></a>[Azure CLI](#tab/cli)

Om du vill ladda upp en blob med ett krypteringsomfång via Azure CLI anropar du kommandot [az storage blob upload](/cli/azure/storage/blob#az_storage_blob_upload) och anger krypteringsomfånget för bloben.

Om du använder Azure Cloud Shell följer du stegen som beskrivs i Ladda upp [en blob](storage-quickstart-blobs-cli.md#upload-a-blob) för att skapa en fil i rotkatalogen. Du kan sedan ladda upp den här filen till en blob med hjälp av följande exempel.

```azurecli-interactive
az storage blob upload \
    --account-name <storage-account> \
    --container-name <container> \
    --file <file> \
    --name <file> \
    --encryption-scope <scope>
```

---

## <a name="change-the-encryption-key-for-a-scope"></a>Ändra krypteringsnyckeln för ett omfång

Om du vill ändra nyckeln som skyddar ett krypteringsomfång från en Microsoft-hanterad nyckel till en kund hanterad nyckel kontrollerar du först att du har aktiverat kund hanterade nycklar med Azure Key Vault eller Key Vault HSM för lagringskontot. Mer information finns i Konfigurera kryptering med kund hanterade nycklar som [lagras i Azure Key Vault](../common/customer-managed-keys-configure-key-vault.md) eller Konfigurera kryptering med kund hanterade nycklar som [lagras i Azure Key Vault](../common/customer-managed-keys-configure-key-vault.md).

# <a name="portal"></a>[Portal](#tab/portal)

Följ dessa steg om du vill ändra nyckeln som skyddar ett Azure Portal omfånget:

1. Gå till fliken **Krypteringsomfång** för att visa listan över krypteringsomfång för lagringskontot.
1. Välj knappen **Mer** bredvid det omfång som du vill ändra.
1. I fönstret **Redigera krypteringsomfång** kan du ändra krypteringstyp från Microsoft-hanterad nyckel till kund hanterad nyckel eller tvärtom.
1. Om du vill välja en ny kund hanterad nyckel **väljer du Använd en** ny nyckel och anger nyckelvalvet, nyckeln och nyckelversionen.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Om du vill ändra nyckeln som skyddar ett krypteringsomfång från en kund hanterad nyckel till en Microsoft-hanterad nyckel med PowerShell anropar du **kommandot Update-AzStorageEncryptionScope** och skickar `-StorageEncryption` parametern :

```powershell
Update-AzStorageEncryptionScope -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -EncryptionScopeName $scopeName2 `
    -StorageEncryption
```

Anropa sedan kommandot **Update-AzStorageEncryptionScope** och skicka `-KeyUri` `-KeyvaultEncryption` parametrarna och :

```powershell
Update-AzStorageEncryptionScope -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -EncryptionScopeName $scopeName1 `
    -KeyUri $keyUri `
    -KeyvaultEncryption
```

# <a name="azure-cli"></a>[Azure CLI](#tab/cli)

Om du vill ändra nyckeln som skyddar ett krypteringsomfång från en kund hanterad nyckel till en Microsoft-hanterad nyckel med Azure CLI anropar du kommandot [az storage account encryption-scope update](/cli/azure/storage/account/encryption-scope#az_storage_account_encryption_scope_update) och skickar `--key-source` parametern med värdet `Microsoft.Storage` :

```azurecli-interactive
az storage account encryption-scope update \
    --account-name <storage-account> \
    --resource-group <resource-group>
    --name <scope> \
    --key-source Microsoft.Storage
```

Anropa sedan kommandot **az storage account encryption-scope update,** skicka in parametern och `--key-uri` skicka `--key-source` parametern med värdet `Microsoft.KeyVault` :

```powershell
az storage account encryption-scope update \
    --resource-group <resource-group> \
    --account-name <storage-account> \
    --name <scope> \
    --key-source Microsoft.KeyVault \
    --key-uri <key-uri>
```

---

## <a name="disable-an-encryption-scope"></a>Inaktivera ett krypteringsomfång

När ett krypteringsomfång har inaktiverats debiteras du inte längre för det. Inaktivera alla krypteringsomfång som inte behövs för att undvika onödiga kostnader. Mer information finns i [Azure Storage kryptering för vilodata.](../common/storage-service-encryption.md)

# <a name="portal"></a>[Portal](#tab/portal)

Om du vill inaktivera ett krypteringsomfång i Azure Portal navigerar du till inställningen Krypteringsomfång för lagringskontot, väljer önskat **krypteringsomfång** och väljer **Inaktivera.**

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Om du vill inaktivera ett krypteringsomfång med PowerShell anropar du kommandot Update-AzStorageEncryptionScope och inkluderar parametern med värdet , som `-State` du ser i följande `disabled` exempel. Om du vill återaktivera ett krypteringsomfång anropar du samma kommando med `-State` parametern inställd på `enabled` . Kom ihåg att ersätta platshållarvärdena i exemplet med dina egna värden:

```powershell
Update-AzStorageEncryptionScope -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -EncryptionScopeName $scopeName1 `
    -State disabled
```

# <a name="azure-cli"></a>[Azure CLI](#tab/cli)

Om du vill inaktivera ett krypteringsomfång med Azure CLI anropar du kommandot [az storage account encryption-scope update](/cli/azure/storage/account/encryption-scope#az_storage_account_encryption_scope_update) och inkluderar parametern med värdet , som du ser i följande `--state` `Disabled` exempel. Om du vill återaktivera ett krypteringsomfång anropar du samma kommando med `--state` parametern inställd på `Enabled` . Kom ihåg att ersätta platshållarvärdena i exemplet med dina egna värden:

```azurecli-interactive
az storage account encryption-scope update \
    --account-name <storage-account> \
    --resource-group <resource-group> \
    --name <scope> \
    --state Disabled
```

> [!IMPORTANT]
> Det går inte att ta bort ett krypteringsomfång. För att undvika oväntade kostnader bör du inaktivera alla krypteringsomfång som du inte behöver för närvarande.

---

## <a name="next-steps"></a>Nästa steg

- [Azure Storage-kryptering av vilande data](../common/storage-service-encryption.md)
- [Krypteringsomfång för Blob Storage](encryption-scope-overview.md)
- [Kund hanterade nycklar för Azure Storage kryptering](../common/customer-managed-keys-overview.md)