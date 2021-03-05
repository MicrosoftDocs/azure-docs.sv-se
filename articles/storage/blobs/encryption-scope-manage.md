---
title: Skapa och hantera krypterings omfång (förhands granskning)
description: Lär dig hur du skapar en krypterings omfattning för att isolera BLOB-data på container-eller BLOB-nivå.
services: storage
author: tamram
ms.service: storage
ms.date: 03/05/2021
ms.topic: conceptual
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.openlocfilehash: d5590ff275ce821c81f5751f4d92972c49adaafc
ms.sourcegitcommit: f7eda3db606407f94c6dc6c3316e0651ee5ca37c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/05/2021
ms.locfileid: "102209599"
---
# <a name="create-and-manage-encryption-scopes-preview"></a>Skapa och hantera krypterings omfång (förhands granskning)

Krypterings omfång (för hands version) gör att du kan hantera kryptering på nivån för en enskild BLOB eller behållare. En krypteringsomfattning isolerar blob-data i en säker enklav i ett lagringskonto. Du kan använda krypterings omfång för att skapa säkra gränser mellan data som finns i samma lagrings konto men som tillhör olika kunder. Mer information om krypterings omfång finns i [krypterings omfång för Blob Storage (för hands version)](encryption-scope-overview.md).

Den här artikeln visar hur du skapar en krypterings omfattning. Det visar också hur du anger ett krypterings omfång när du skapar en BLOB eller behållare.

> [!IMPORTANT]
> Krypterings omfång är för närvarande en för **hands version**. Se [kompletterande användnings villkor för Microsoft Azure för hands](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) versioner av juridiska villkor som gäller för Azure-funktioner som är i beta, för hands version eller på annat sätt ännu inte släpps till allmän tillgänglighet.
>
> Undvik oväntade kostnader genom att inaktivera eventuella krypterings omfattningar som du inte behöver.

[!INCLUDE [storage-data-lake-gen2-support](../../../includes/storage-data-lake-gen2-support.md)]

## <a name="create-an-encryption-scope"></a>Skapa ett krypterings omfång

Du kan skapa en krypterings omfattning med en Microsoft-hanterad nyckel eller med en kundhanterad nyckel som lagras i Azure Key Vault eller Azure Key Vault-hanterad maskinvarubaserad säkerhets modell (HSM) (för hands version). Om du vill skapa en krypterings omfattning med en kundhanterad nyckel måste du först skapa ett nyckel valv eller en hanterad HSM och lägga till den nyckel som du vill använda för omfånget. Nyckel valvet eller hanterad HSM måste ha rensnings skyddet aktiverat och måste finnas i samma region som lagrings kontot.

En krypterings omfattning aktive ras automatiskt när du skapar den. När du har skapat krypterings omfånget kan du ange det när du skapar en blob. Du kan också ange ett standard krypterings omfång när du skapar en behållare, som automatiskt tillämpas på alla blobar i behållaren.

# <a name="portal"></a>[Portal](#tab/portal)

Följ dessa steg om du vill skapa en krypterings omfattning i Azure Portal:

1. Navigera till ditt lagringskonto i Azure-portalen.
1. Välj **krypterings** inställningen.
1. Välj fliken **krypterings omfång** .
1. Klicka på knappen **Lägg** till för att lägga till en ny krypterings omfattning.
1. I fönstret Skapa **krypterings omfång** anger du ett namn för det nya omfånget.
1. Välj typ av kryptering, antingen **Microsoft-hanterade nycklar** eller **Kundhanterade nycklar**.
    - Om du har valt **Microsoft-hanterade nycklar**, klickar du på **skapa** för att skapa krypterings omfånget.
    - Om du har valt **Kundhanterade nycklar** anger du ett nyckel valv eller en hanterad HSM, nyckel och nyckel version som ska användas för krypterings omfånget, som visas i följande bild.

    :::image type="content" source="media/encryption-scope-manage/create-encryption-scope-customer-managed-key-portal.png" alt-text="Skärm bild som visar hur du skapar ett krypterings omfång i Azure Portal":::

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Om du vill skapa en krypterings omfattning med PowerShell installerar du först versionen av AZ. Storage Preview module. Det rekommenderas att du använder den senaste för hands versionen, men krypterings omfång stöds i version 1.13.4-Preview och senare. Ta bort alla andra versioner av modulen AZ. Storage.

Följande kommando installerar AZ. Storage [2.1.1-Preview-](https://www.powershellgallery.com/packages/Az.Storage/2.1.1-preview) modulen:

```powershell
Install-Module -Name Az.Storage -RequiredVersion 2.1.1-preview -AllowPrerelease
```

### <a name="create-an-encryption-scope-protected-by-microsoft-managed-keys"></a>Skapa ett krypterings område som skyddas av Microsoft-hanterade nycklar

Om du vill skapa en ny krypterings omfattning som skyddas av Microsoft-hanterade nycklar anropar du kommandot **New-AzStorageEncryptionScope** med `-StorageEncryption` parametern.

Kom ihåg att ersätta plats hållarnas värden i exemplet med dina egna värden:

```powershell
$rgName = "<resource-group>"
$accountName = "<storage-account>"
$scopeName1 = "customer1scope"

New-AzStorageEncryptionScope -ResourceGroupName $rgName `
    -AccountName $accountName `
    -EncryptionScopeName $scopeName1 `
    -StorageEncryption
```

### <a name="create-an-encryption-scope-protected-by-customer-managed-keys"></a>Skapa ett krypterings område som skyddas av Kundhanterade nycklar

Om du vill skapa en ny krypterings omfattning som skyddas av Kundhanterade nycklar som lagras i ett nyckel valv eller en hanterad HSM måste du först konfigurera Kundhanterade nycklar för lagrings kontot. Du måste tilldela en hanterad identitet till lagrings kontot och sedan använda den hanterade identiteten för att konfigurera åtkomst principen för Key Vault eller hanterad HSM så att lagrings kontot har behörighet att komma åt det.

Om du vill konfigurera Kundhanterade nycklar för användning med en krypterings omfattning måste rensnings skyddet vara aktiverat på nyckel valvet eller hanterad HSM. Nyckel valvet eller hanterad HSM måste finnas i samma region som lagrings kontot.

Kom ihåg att ersätta plats hållarnas värden i exemplet med dina egna värden:

```powershell
$rgName = "<resource-group>"
$accountName = "<storage-account>"
$keyVaultName = "<key-vault>"
$keyUri = "<key-uri-with-version>"
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

Anropa sedan kommandot **New-AzStorageEncryptionScope** med `-KeyvaultEncryption` parametern och ange nyckel-URI: n. Se till att inkludera nyckel versionen på nyckel-URI: n. Kom ihåg att ersätta plats hållarnas värden i exemplet med dina egna värden:

```powershell
New-AzStorageEncryptionScope -ResourceGroupName $rgName `
    -AccountName $accountName `
    -EncryptionScopeName $scopeName2 `
    -KeyUri $keyUri `
    -KeyvaultEncryption
```

# <a name="azure-cli"></a>[Azure CLI](#tab/cli)

Om du vill skapa en krypterings omfattning med Azure CLI måste du först installera Azure CLI version 2.4.0 eller senare.

### <a name="create-an-encryption-scope-protected-by-microsoft-managed-keys"></a>Skapa ett krypterings område som skyddas av Microsoft-hanterade nycklar

Om du vill skapa en ny krypterings omfattning som skyddas av Microsoft-hanterade nycklar anropar du kommandot [AZ Storage Account Encryption-scope Create](/cli/azure/storage/account/encryption-scope#az-storage-account-encryption-scope-create) och anger `--key-source` parametern som `Microsoft.Storage` . Kom ihåg att ersätta plats hållarnas värden med dina egna värden:

```azurecli-interactive
az storage account encryption-scope create \
    --resource-group <resource-group> \
    --account-name <storage-account> \
    --name <scope> \
    --key-source Microsoft.Storage
```

### <a name="create-an-encryption-scope-protected-by-customer-managed-keys"></a>Skapa ett krypterings område som skyddas av Kundhanterade nycklar

Om du vill skapa en ny krypterings omfattning som skyddas av Microsoft-hanterade nycklar anropar du kommandot [AZ Storage Account Encryption-scope Create](/cli/azure/storage/account/encryption-scope#az-storage-account-encryption-scope-create) och anger `--key-source` parametern som `Microsoft.Storage` . Kom ihåg att ersätta plats hållarnas värden med dina egna värden:

Om du vill skapa en ny krypterings omfattning som skyddas av Kundhanterade nycklar i ett nyckel valv eller en hanterad HSM måste du först konfigurera Kundhanterade nycklar för lagrings kontot. Du måste tilldela en hanterad identitet till lagrings kontot och sedan använda den hanterade identiteten för att konfigurera åtkomst principen för nyckel valvet så att lagrings kontot har behörighet att komma åt det. Mer information finns i [kund hanterade nycklar för Azure Storage kryptering](../common/customer-managed-keys-overview.md).

Om du vill konfigurera Kundhanterade nycklar för användning med en krypterings omfattning måste rensnings skyddet vara aktiverat på nyckel valvet eller hanterad HSM. Nyckel valvet eller hanterad HSM måste finnas i samma region som lagrings kontot.

Kom ihåg att ersätta plats hållarnas värden i exemplet med dina egna värden:

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

Sedan anropar du kommandot **AZ Storage Account Encryption-scope Create** med `--key-uri` parametern och anger nyckel-URI: n. Se till att inkludera nyckel versionen på nyckel-URI: n. Kom ihåg att ersätta plats hållarnas värden i exemplet med dina egna värden:

```azurecli-interactive
az storage account encryption-scope create \
    --resource-group <resource-group> \
    --account-name <storage-account> \
    --name <scope> \
    --key-source Microsoft.KeyVault \
    --key-uri <key-uri>
```

---

Information om hur du konfigurerar Azure Storage kryptering med Kundhanterade nycklar i ett nyckel valv finns i [Konfigurera kryptering med Kundhanterade nycklar som lagras i Azure Key Vault](../common/customer-managed-keys-configure-key-vault.md). Om du vill konfigurera Kundhanterade nycklar i en hanterad HSM, se [Konfigurera kryptering med Kundhanterade nycklar som lagras i Azure Key Vault hanterad HSM (för hands version)](../common/customer-managed-keys-configure-key-vault-hsm.md).

## <a name="list-encryption-scopes-for-storage-account"></a>Lista krypterings omfång för lagrings konto

# <a name="portal"></a>[Portal](#tab/portal)

Om du vill visa krypterings omfång för ett lagrings konto i Azure Portal navigerar du till inställningen **krypterings omfång** för lagrings kontot. I det här fönstret kan du aktivera eller inaktivera ett krypterings omfång eller ändra nyckeln för en krypterings omfattning.

:::image type="content" source="media/encryption-scope-manage/list-encryption-scopes-portal.png" alt-text="Skärm bild som visar en lista över krypterings områden i Azure Portal":::

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Om du vill visa en lista över krypterings omfång som är tillgängliga för ett lagrings konto med PowerShell anropar du kommandot **Get-AzStorageEncryptionScope** . Kom ihåg att ersätta plats hållarnas värden i exemplet med dina egna värden:

```powershell
Get-AzStorageEncryptionScope -ResourceGroupName $rgName `
    -StorageAccountName $accountName
```

Om du vill visa en lista över alla krypterings omfattningar i en resurs grupp efter lagrings konto använder du pipeline-syntaxen:

```powershell
Get-AzStorageAccount -ResourceGroupName $rgName | Get-AzStorageEncryptionScope
```

# <a name="azure-cli"></a>[Azure CLI](#tab/cli)

Om du vill visa en lista över krypterings omfång som är tillgängliga för ett lagrings konto med Azure CLI anropar du kommandot [AZ Storage Account Encryption-scope List](/cli/azure/storage/account/encryption-scope#az-storage-account-encryption-scope-list) . Kom ihåg att ersätta plats hållarnas värden i exemplet med dina egna värden:

```azurecli-interactive
az storage account encryption-scope list \
    --account-name <storage-account> \
    --resource-group <resource-group>
```

---

## <a name="create-a-container-with-a-default-encryption-scope"></a>Skapa en behållare med ett standard krypterings omfång

När du skapar en behållare kan du ange ett standard krypterings omfång. Blobbar i behållaren kommer att använda det omfånget som standard.

En enskild BLOB kan skapas med sin egen krypterings omfattning, om inte behållaren är konfigurerad att kräva att alla blobbar använder sitt standard omfång.

# <a name="portal"></a>[Portal](#tab/portal)

Om du vill skapa en behållare med en standard krypterings omfattning i Azure Portal skapar du först krypterings omfånget enligt beskrivningen i [skapa en krypterings omfattning](#create-an-encryption-scope). Följ sedan de här stegen för att skapa behållaren:

1. Navigera till listan över behållare i ditt lagrings konto och välj knappen **Lägg till** för att skapa en ny behållare.
1. Expandera de **avancerade** inställningarna i fönstret **ny behållare** .
1. I list rutan **krypterings omfång** väljer du standard krypterings omfång för behållaren.
1. Om du vill kräva att alla blobar i behållaren använder standard krypterings omfånget markerar du kryss rutan för att **använda krypterings omfånget för alla blobbar i behållaren**. Om den här kryss rutan är markerad kan en enskild BLOB i behållaren inte åsidosätta standard krypterings omfånget.

    :::image type="content" source="media/encryption-scope-manage/create-container-default-encryption-scope.png" alt-text="Skärm bild som visar behållare med standard krypterings omfång":::

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Om du vill skapa en behållare med en standard krypterings omfattning med PowerShell anropar du kommandot [New-AzRmStorageContainer](/powershell/module/az.storage/new-azrmstoragecontainer) och anger omfånget för `-DefaultEncryptionScope` parametern. Kommandot **New-AzRmStorageContainer** skapar en behållare med hjälp av Azure Storage Resource Provider, som möjliggör konfiguration av krypterings omfattningar och andra resurs hanterings åtgärder.

Om du vill tvinga alla blobbar i en behållare att använda behållarens standard omfång anger du `-PreventEncryptionScopeOverride` parametern till `true` .

```powershell
$containerName1 = "container1"
$containerName2 = "container2"

# Create a container with a default encryption scope that cannot be overridden.
New-AzRmStorageContainer -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -Name $containerName1 `
    -DefaultEncryptionScope $scopeName1 `
    -PreventEncryptionScopeOverride $true
```

# <a name="azure-cli"></a>[Azure CLI](#tab/cli)

Om du vill skapa en behållare med ett standard krypterings omfång med Azure CLI anropar du kommandot [AZ Storage container Create](/cli/azure/storage/container#az-storage-container-create) och anger omfånget för `--default-encryption-scope` parametern. Om du vill tvinga alla blobbar i en behållare att använda behållarens standard omfång anger du `--prevent-encryption-scope-override` parametern till `true` .

I följande exempel används ditt Azure AD-konto för att auktorisera åtgärden att skapa behållaren. Du kan också använda kontots åtkomst nyckel. Mer information finns i [bevilja åtkomst till BLOB-eller Queue-data med Azure CLI](./authorize-data-operations-cli.md).

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

Om en klient försöker ange ett omfång när en BLOB laddas upp till en behållare som har ett standard krypterings omfång och behållaren har kon figurer ATS för att förhindra att blobbar åsidosätter standard omfånget, Miss lyckas åtgärden med ett meddelande som anger att begäran är förbjuden av behållarens krypterings princip.

## <a name="upload-a-blob-with-an-encryption-scope"></a>Ladda upp en blob med ett krypterings omfång

När du laddar upp en BLOB kan du ange ett krypterings omfång för denna BLOB, eller använda standard krypterings omfånget för behållaren, om ett sådant har angetts.

# <a name="portal"></a>[Portal](#tab/portal)

Om du vill ladda upp en blob med ett krypterings omfång som anges i Azure Portal skapar du först krypterings omfånget enligt beskrivningen i [skapa en krypterings omfattning](#create-an-encryption-scope). Följ sedan de här stegen för att skapa blobben:

1. Navigera till den behållare som du vill ladda upp bloben till.
1. Välj knappen **överför** och leta upp bloben som ska laddas upp.
1. Expandera de **avancerade** inställningarna i fönstret **Ladda upp BLOB** .
1. Leta upp List rutan **krypterings omfång** . Som standard skapas blobben med standard krypterings omfånget för behållaren, om en sådan har angetts. Om behållaren kräver att blobbar använder standard krypterings omfattningen är det här avsnittet inaktiverat.
1. Om du vill ange ett annat omfång för den blob som du överför, väljer du **Välj en befintlig omfattning** och väljer sedan önskat omfång i list rutan.

    :::image type="content" source="media/encryption-scope-manage/upload-blob-encryption-scope.png" alt-text="Skärm bild som visar hur du laddar upp en blob med ett krypterings omfång":::

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Om du vill ladda upp en blob med ett krypterings omfång som anges med hjälp av PowerShell anropar du kommandot [set-AzStorageBlobContent](/powershell/module/az.storage/set-azstorageblobcontent) och anger krypterings omfånget för blobben.

```powershell
$containerName2 = "container2"
$localSrcFile = "C:\temp\helloworld.txt"
$ctx = (Get-AzStorageAccount -ResourceGroupName $rgName -StorageAccountName $accountName).Context

# Create a new container with no default scope defined.
New-AzStorageContainer -Name $containerName2 -Context $ctx
# Upload a block upload with an encryption scope specified.
Set-AzStorageBlobContent -Context $ctx -Container $containerName2 -File $localSrcFile -Blob "helloworld.txt" -BlobType Block -EncryptionScope $scopeName2
```

# <a name="azure-cli"></a>[Azure CLI](#tab/cli)

Om du vill ladda upp en blob med ett krypterings omfång som anges med Azure CLI anropar du kommandot [AZ Storage BLOB upload](/cli/azure/storage/blob#az-storage-blob-upload) och anger krypterings omfånget för blobben.

Om du använder Azure Cloud Shell följer du stegen som beskrivs i [Ladda upp en BLOB](storage-quickstart-blobs-cli.md#upload-a-blob) för att skapa en fil i rot katalogen. Du kan sedan överföra filen till en blob med hjälp av följande exempel.

```azurecli-interactive
az storage blob upload \
    --account-name <storage-account> \
    --container-name <container> \
    --file <file> \
    --name <file> \
    --encryption-scope <scope>
```

---

## <a name="change-the-encryption-key-for-a-scope"></a>Ändra krypterings nyckeln för ett omfång

Om du vill ändra nyckeln som skyddar ett krypterings omfång från en Microsoft-hanterad nyckel till en kundhanterad nyckel måste du först kontrol lera att du har aktiverat Kundhanterade nycklar med Azure Key Vault eller Key Vault HSM för lagrings kontot. Mer information finns i [Konfigurera kryptering med Kundhanterade nycklar som lagras i Azure Key Vault](../common/customer-managed-keys-configure-key-vault.md) eller [Konfigurera kryptering med Kundhanterade nycklar som lagras i Azure Key Vault](../common/customer-managed-keys-configure-key-vault.md).

# <a name="portal"></a>[Portal](#tab/portal)

Följ dessa steg om du vill ändra nyckeln som skyddar en omfattning i Azure Portal:

1. Gå till fliken **krypterings omfattningar** om du vill visa en lista över krypterings omfång för lagrings kontot.
1. Välj knappen **mer** bredvid det omfång som du vill ändra.
1. I fönstret **Redigera krypterings omfång** kan du ändra krypterings typen från Microsoft-hanterad nyckel till kundhanterad nyckel eller vice versa.
1. Om du vill välja en ny kundhanterad nyckel väljer du **Använd en ny nyckel** och anger nyckel valvet, nyckeln och nyckel versionen.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Om du vill ändra nyckeln som skyddar ett krypterings omfång från en kundhanterad nyckel till en Microsoft-hanterad nyckel med PowerShell, anropar du kommandot **Update-AzStorageEncryptionScope** och skickar `-StorageEncryption` följande parameter:

```powershell
Update-AzStorageEncryptionScope -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -EncryptionScopeName $scopeName2 `
    -StorageEncryption
```

Anropa sedan kommandot **Update-AzStorageEncryptionScope** och skicka in `-KeyUri` `-KeyvaultEncryption` parametrarna och:

```powershell
Update-AzStorageEncryptionScope -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -EncryptionScopeName $scopeName1 `
    -KeyUri $keyUri `
    -KeyvaultEncryption
```

# <a name="azure-cli"></a>[Azure CLI](#tab/cli)

Om du vill ändra nyckeln som skyddar ett krypterings omfång från en kundhanterad nyckel till en Microsoft-hanterad nyckel med Azure CLI, anropar du kommandot [AZ Storage Account Encryption-scope Update](/cli/azure/storage/account/encryption-scope#az-storage-account-encryption-scope-update) och överför i `--key-source` parametern med värdet `Microsoft.Storage` :

```azurecli-interactive
az storage account encryption-scope update \
    --account-name <storage-account> \
    --resource-group <resource-group>
    --name <scope> \
    --key-source Microsoft.Storage
```

Sedan anropar du kommandot **AZ Storage Account Encryption-scope Update** , skickar in `--key-uri` parametern och skickar `--key-source` parametern med värdet `Microsoft.KeyVault` :

```powershell
az storage account encryption-scope update \
    --resource-group <resource-group> \
    --account-name <storage-account> \
    --name <scope> \
    --key-source Microsoft.KeyVault \
    --key-uri <key-uri>
```

---

## <a name="disable-an-encryption-scope"></a>Inaktivera ett krypterings omfång

När en krypterings omfattning är inaktive rad debiteras du inte längre. Inaktivera eventuella krypterings områden som inte behövs för att undvika onödiga kostnader. Mer information finns i [Azure Storage kryptering för vilande data](../common/storage-service-encryption.md).

# <a name="portal"></a>[Portal](#tab/portal)

Om du vill inaktivera ett krypterings omfång i Azure Portal navigerar du till inställningen **krypterings omfång** för lagrings kontot, väljer önskad krypterings omfattning och väljer **inaktivera**.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Om du vill inaktivera ett krypterings omfång med PowerShell anropar du kommandot Update-AzStorageEncryptionScope och inkluderar `-State` parametern med värdet `disabled` , som du ser i följande exempel. Om du vill återaktivera en krypterings omfattning anropar du samma kommando med `-State` parametern inställd på `enabled` . Kom ihåg att ersätta plats hållarnas värden i exemplet med dina egna värden:

```powershell
Update-AzStorageEncryptionScope -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -EncryptionScopeName $scopeName1 `
    -State disabled
```

# <a name="azure-cli"></a>[Azure CLI](#tab/cli)

Om du vill inaktivera ett krypterings omfång med Azure CLI anropar du kommandot [AZ Storage Account Encryption-scope Update](/cli/azure/storage/account/encryption-scope#az-storage-account-encryption-scope-update) och inkluderar `--state` parametern med värdet `Disabled` , som visas i följande exempel. Om du vill återaktivera en krypterings omfattning anropar du samma kommando med `--state` parametern inställd på `Enabled` . Kom ihåg att ersätta plats hållarnas värden i exemplet med dina egna värden:

```azurecli-interactive
az storage account encryption-scope update \
    --account-name <storage-account> \
    --resource-group <resource-group> \
    --name <scope> \
    --state Disabled
```

---

## <a name="next-steps"></a>Nästa steg

- [Azure Storage-kryptering av vilande data](../common/storage-service-encryption.md)
- [Krypterings omfång för Blob Storage (för hands version)](encryption-scope-overview.md)
- [Kundhanterade nycklar för Azure Storage kryptering](../common/customer-managed-keys-overview.md)