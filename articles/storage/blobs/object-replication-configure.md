---
title: Konfigurera objekt replikering (för hands version)
titleSuffix: Azure Storage
description: Lär dig hur du konfigurerar objekt replikering till asynkron kopiering av block-blobbar från en behållare i ett lagrings konto till ett annat.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 07/16/2020
ms.author: tamram
ms.subservice: blobs
ms.custom: devx-track-azurecli
ms.openlocfilehash: dd85504ac2321310288efe5d0a1ef7dfcde60f21
ms.sourcegitcommit: 37afde27ac137ab2e675b2b0492559287822fded
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88566071"
---
# <a name="configure-object-replication-for-block-blobs-preview"></a>Konfigurera objekt replikering för block-blobar (för hands version)

Objekt replikering (för hands version) kopierar asynkront block blobbar mellan ett käll lagrings konto och ett mål konto. Mer information om objekt replikering finns i [Object Replication (för hands version)](object-replication-overview.md).

När du konfigurerar objekt replikering skapar du en replikeringsprincip som anger käll lagrings kontot och mål kontot. En replikeringsprincip innehåller en eller flera regler som anger en käll behållare och en mål behållare och anger vilka block-blobar som ska replikeras i käll behållaren.

Den här artikeln beskriver hur du konfigurerar objekt replikering för ditt lagrings konto med hjälp av Azure Portal, PowerShell eller Azure CLI. Du kan också använda en av klient biblioteken för Azure Storage Resource Provider för att konfigurera objekt replikering.

## <a name="create-a-replication-policy-and-rules"></a>Skapa en replikeringsprincip och regler

Innan du konfigurerar objekt replikering skapar du käll-och mål lagrings konton om de inte redan finns. Båda kontona måste vara generella-syfte v2-lagrings konton. Mer information finns i [skapa ett Azure Storage-konto](../common/storage-account-create.md).

Ett lagrings konto kan fungera som käll konto för upp till två mål konton. Och ett mål konto får inte ha fler än två käll konton. Käll- och målkontona kan finnas i olika regioner. Du kan konfigurera separata replikeringsprinciper för att replikera data till varje mål konto.

Innan du börjar ska du kontrol lera att du har registrerat dig för följande funktions förändringar:

- [Objekt replikering (för hands version)](object-replication-overview.md)
- [Blob-version (för hands version)](versioning-overview.md)
- [Ändra stöd för feed i Azure Blob Storage (för hands version)](storage-blob-change-feed.md)

# <a name="azure-portal"></a>[Azure-portalen](#tab/portal)

Innan du konfigurerar objekt replikering i Azure Portal skapar du käll-och mål behållare i deras respektive lagrings konton, om de inte redan finns. Du kan också aktivera BLOB-versioner och ändra feed på käll kontot och aktivera BLOB-versioner på mål kontot.

Följ dessa steg om du vill skapa en replikeringsprincip i Azure Portal:

1. Navigera till käll lagrings kontot i Azure Portal.
1. Under **BLOB service**väljer du **objekt replikering**.
1. Välj **Konfigurera regler för replikering**.
1. Välj mål prenumeration och lagrings konto.
1. I avsnittet **behållar par** väljer du en käll behållare från käll kontot och en mål behållare från mål kontot. Du kan skapa upp till 10 container par per replikeringsprincip.

    Följande bild visar en uppsättning regler för replikering.

    :::image type="content" source="media/object-replication-configure/configure-replication-policy.png" alt-text="Skärm bild som visar regler för replikering i Azure Portal":::

1. Om du vill kan du ange ett eller flera filter för att endast kopiera blobbar som matchar ett prefix. Om du till exempel anger ett prefix `b` replikeras endast blobbar vars namn börjar med den bokstaven. Du kan ange en virtuell katalog som en del av prefixet. Prefixlängden stöder inte jokertecken.

    Följande bild visar filter som begränsar vilka blobbar som kopieras som en del av en replikeringsprincip.

    :::image type="content" source="media/object-replication-configure/configure-replication-copy-prefix.png" alt-text="Skärm bild som visar filter för en replikeringsprincip":::

1. Som standard är kopierings omfånget inställt på att bara kopiera nya objekt. Om du vill kopiera alla objekt i behållaren eller kopiera objekt från ett anpassat datum och en anpassad tid, väljer du länken **ändra** och konfigurerar kopierings omfånget för behållar paret.

    Följande bild visar en anpassad kopierings omfattning.

    :::image type="content" source="media/object-replication-configure/configure-replication-copy-scope.png" alt-text="Skärm bild som visar en anpassad kopierings omfattning för objekt replikering":::

1. Välj **Spara och tillämpa** för att skapa replikeringsprincipen och påbörja replikering av data.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Om du vill skapa en replikeringsprincip med PowerShell måste du först installera [2.0.1-för hands](https://www.powershellgallery.com/packages/Az.Storage/2.0.1-preview) version eller senare av AZ. Storage PowerShell-modulen. Följ de här stegen för att installera Preview-modulen:

1. Avinstallera tidigare installationer av Azure PowerShell från Windows med hjälp av inställningen **appar & funktioner** under **Inställningar**.

1. Kontrol lera att du har den senaste versionen av PowerShellGet installerad. Öppna ett Windows PowerShell-fönster och kör följande kommando för att installera den senaste versionen:

    ```powershell
    Install-Module PowerShellGet –Repository PSGallery –Force
    ```

    Stäng PowerShell-fönstret och öppna det igen när du har installerat PowerShellGet.

1. Installera den senaste versionen av Azure PowerShell:

    ```powershell
    Install-Module Az –Repository PSGallery –AllowClobber
    ```

1. Installera AZ. Storage Preview-modulen:

    ```powershell
    Install-Module Az.Storage -Repository PSGallery -RequiredVersion 2.0.1-preview -AllowPrerelease -AllowClobber -Force
    ```

Mer information om hur du installerar Azure PowerShell finns i [installera Azure PowerShell med PowerShellGet](/powershell/azure/install-az-ps).

I följande exempel visas hur du skapar en replikeringsprincip på käll-och mål kontona. Kom ihåg att ersätta värden inom vinkelparenteser med dina egna värden:

```powershell
# Sign in to your Azure account.
Connect-AzAccount

# Set variables.
$rgName = "<resource-group>"
$srcAccountName = "<source-storage-account>"
$destAccountName = "<destination-storage-account>"
$srcContainerName1 = "source-container1"
$destContainerName1 = "dest-container1"
$srcContainerName2 = "source-container2"
$destContainerName2 = "dest-container2"

# Enable blob versioning and change feed on the source account.
Update-AzStorageBlobServiceProperty -ResourceGroupName $rgname `
    -StorageAccountName $srcAccountName `
    -EnableChangeFeed $true `
    -IsVersioningEnabled $true

# Enable blob versioning on the destination account.
Update-AzStorageBlobServiceProperty -ResourceGroupName $rgname `
    -StorageAccountName $destAccountName `
    -IsVersioningEnabled $true

# List the service properties for both accounts.
Get-AzStorageBlobServiceProperty -ResourceGroupName $rgname `
    -StorageAccountName $srcAccountName
Get-AzStorageBlobServiceProperty -ResourceGroupName $rgname `
    -StorageAccountName $destAccountName

# Create containers in the source and destination accounts.
Get-AzStorageAccount -ResourceGroupName $rgname -StorageAccountName $srcAccountName |
    New-AzStorageContainer $srcContainerName1
Get-AzStorageAccount -ResourceGroupName $rgname -StorageAccountName $destAccountName |
    New-AzStorageContainer $destContainerName1
Get-AzStorageAccount -ResourceGroupName $rgname -StorageAccountName $srcAccountName |
    New-AzStorageContainer $srcContainerName2
Get-AzStorageAccount -ResourceGroupName $rgname -StorageAccountName $destAccountName |
    New-AzStorageContainer $destContainerName2

# Define replication rules for each container.
$rule1 = New-AzStorageObjectReplicationPolicyRule -SourceContainer $srcContainerName1 `
    -DestinationContainer $destContainerName1 `
    -PrefixMatch b
$rule2 = New-AzStorageObjectReplicationPolicyRule -SourceContainer $srcContainerName2 `
    -DestinationContainer $destContainerName2  `
    -MinCreationTime 2020-05-10T00:00:00Z

# Create the replication policy on the destination account.
$destPolicy = Set-AzStorageObjectReplicationPolicy -ResourceGroupName $rgname `
    -StorageAccountName $destAccountName `
    -PolicyId default `
    -SourceAccount $srcAccountName `
    -Rule $rule1,$rule2

# Create the same policy on the source account.
Set-AzStorageObjectReplicationPolicy -ResourceGroupName $rgname `
    -StorageAccountName $srcAccountName `
    -InputObject $destPolicy
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Om du vill skapa en replikeringsprincip med Azure CLI måste du först installera förhands gransknings tillägget för Azure Storage.:

```azurecli
az extension add -n storage-or-preview
```

Logga sedan in med dina Azure-autentiseringsuppgifter:

```azurecli
az login
```

Aktivera BLOB-versioner på käll-och mål lagrings kontona och aktivera ändra feed på käll kontot. Kom ihåg att ersätta värden inom vinkelparenteser med dina egna värden:

```azurecli
az storage blob service-properties update \
    --resource-group <resource-group> \
    --account-name <source-storage-account> \
    --enable-versioning

az storage blob service-properties update \
    --resource-group <resource-group> \
    --account-name <source-storage-account> \
    --enable-change-feed

az storage blob service-properties update \
    --resource-group <resource-group> \
    --account-name <dest-storage-account> \
    --enable-versioning
```

Skapa käll-och mål behållare i deras respektive lagrings konton.

```azurecli
az storage container create \
    --account-name <source-storage-account> \
    --name source-container3 \
    --auth-mode login
az storage container create \
    --account-name <source-storage-account> \
    --name source-container4 \
    --auth-mode login

az storage container create \
    --account-name <dest-storage-account> \
    --name source-container3 \
    --auth-mode login
az storage container create \
    --account-name <dest-storage-account> \
    --name source-container4 \
    --auth-mode login
```

Skapa en ny replikeringsprincip och tillhör ande regler på mål kontot.

```azurecli
az storage account or-policy create \
    --account-name <dest-storage-account> \
    --resource-group <resource-group> \
    --source-account <source-storage-account> \
    --destination-account <dest-storage-account> \
    --source-container source-container3 \
    --destination-container dest-container3 \
    --min-creation-time '2020-05-10T00:00:00Z' \
    --prefix-match a

az storage account or-policy rule add \
    --account-name <dest-storage-account> \
    --destination-container dest-container4 \
    --policy-id <policy-id> \
    --resource-group <resource-group> \
    --source-container source-container4 \
    --prefix-match b
```

Skapa principen på käll kontot med hjälp av princip-ID: t.

```azurecli
az storage account or-policy show \
    --resource-group <resource-group> \
    --name <dest-storage-account> \
    --policy-id <policy-id> |
    --az storage account or-policy create --resource-group <resource-group> \
    --name <source-storage-account> \
    --policy "@-"
```

---

## <a name="remove-a-replication-policy"></a>Ta bort en replikeringsprincip

Om du vill ta bort en replikeringsprincip och dess associerade regler använder du Azure Portal, PowerShell eller CLI.

# <a name="azure-portal"></a>[Azure-portalen](#tab/portal)

Följ dessa steg om du vill ta bort en replikeringsprincip i Azure Portal:

1. Navigera till käll lagrings kontot i Azure Portal.
1. Under **Inställningar**väljer du **objekt replikering**.
1. Klicka på knappen **mer** bredvid princip namnet.
1. Välj **ta bort regler**.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Ta bort en replikeringsprincip genom att ta bort principen från både käll kontot och mål kontot. Om du tar bort principen raderas även eventuella regler som är kopplade till den.

```powershell
# Remove the policy from the destination account.
Remove-AzStorageObjectReplicationPolicy -ResourceGroupName $rgname `
    -StorageAccountName $destAccountName `
    -PolicyId $destPolicy.PolicyId

# Remove the policy from the source account.
Remove-AzStorageObjectReplicationPolicy -ResourceGroupName $rgname `
    -StorageAccountName $srcAccountName `
    -PolicyId $destPolicy.PolicyId
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Ta bort en replikeringsprincip genom att ta bort principen från både käll kontot och mål kontot. Om du tar bort principen raderas även eventuella regler som är kopplade till den.

```azurecli
az storage account or-policy delete \
    --policy-id $policyid \
    --account-name <source-storage-account> \
    --resource-group <resource-group>

az storage account or-policy delete \
    --policy-id $policyid \
    --account-name <dest-storage-account> \
    --resource-group <resource-group>
```

---

## <a name="next-steps"></a>Nästa steg

- [Översikt över objekt replikering (för hands version)](object-replication-overview.md)
