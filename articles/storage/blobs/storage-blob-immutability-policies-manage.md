---
title: Ange och hantera oföränderlighets-principer för Blob Storage – Azure Storage
description: Lär dig hur du använder WORM (Skriv en gång, Läs många) stöd för blob-lagring (objekt) för att lagra data i ett icke-redigerbart tillstånd för ett angivet intervall.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 11/26/2019
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: a09dbd7d778a4f7ea2a9aac9ca07b9e6d06bc1ae
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "84463645"
---
# <a name="set-and-manage-immutability-policies-for-blob-storage"></a>Ange och hantera oföränderlighets-principer för Blob Storage

Oföränderlig lagring för Azure Blob Storage gör det möjligt för användare att lagra affärs kritiska data objekt i en mask (Skriv en gång, läsa många). Det här läget gör att data inte kan raderas och inte kan ändras för ett användardefinierat intervall. För varaktigheten för kvarhållningsintervallet kan blobbar skapas och läsas, men kan inte ändras eller tas bort. Oföränderlig lagring är tillgängligt för generella v2-och Blob Storage-konton i alla Azure-regioner.

Den här artikeln visar hur du ställer in och hanterar oföränderlighets-principer och juridiska undantag för data i Blob Storage med hjälp av Azure Portal, PowerShell eller Azure CLI. Mer information om oföränderlig lagring finns i [lagra affärs kritiska BLOB-data med oföränderlig lagring](storage-blob-immutable-storage.md).

## <a name="set-retention-policies-and-legal-holds"></a>Ange bevarande principer och juridiska undantag

### <a name="portal"></a>[Portal](#tab/azure-portal)

1. Skapa en ny container eller välj en befintlig container för lagring av de blobbar som ska behållas i det oföränderliga tillståndet. Behållaren måste vara i ett General-Purpose v2-eller Blob Storage-konto.

2. Välj **åtkomst princip** i behållar inställningarna. Välj sedan **Lägg till princip** under **oåterkalleligt blob-lagring**.

    ![Behållar inställningar i portalen](media/storage-blob-immutability-policies-manage/portal-image-1.png)

3. Om du vill aktivera tidsbaserad kvarhållning väljer du **tidsbaserad kvarhållning** på den nedrullningsbara menyn.

    !["Tidsbaserad kvarhållning" som valts under "princip typ"](media/storage-blob-immutability-policies-manage/portal-image-2.png)

4. Ange kvarhållningsintervall i dagar (giltiga värden är 1 till 146000 dagar).

    ![Rutan uppdatera kvarhållningsperiod till](media/storage-blob-immutability-policies-manage/portal-image-5-retention-interval.png)

    Principens ursprungliga tillstånd är olåst så att du kan testa funktionen och göra ändringar i principen innan du låser den. Att låsa principen är viktigt för att följa regler som SEC 17a-4.

5. Lås principen. Högerklicka på ellipsen (**...**) och följande meny visas med ytterligare åtgärder:

    !["Lås princip" på menyn](media/storage-blob-immutability-policies-manage/portal-image-4-lock-policy.png)

6. Välj **Lås princip** och bekräfta låset. Principen är nu låst och kan inte tas bort, endast tillägg för kvarhållningsintervallet kommer att tillåtas. BLOB-borttagningar och åsidosättningar är inte tillåtna. 

    ![Bekräfta "Lås princip" på menyn](media/storage-blob-immutability-policies-manage/portal-image-5-lock-policy.png)

7. Om du vill aktivera juridiska undantag väljer du **Lägg till princip**. Välj **juridiskt undantag** i den nedrullningsbara menyn.

    !["Juridiskt bevarande" på menyn under "princip typ"](media/storage-blob-immutability-policies-manage/portal-image-legal-hold-selection-7.png)

8. Skapa ett juridiskt undantag med en eller flera taggar.

    ![Rutan taggnamn under princip typen](media/storage-blob-immutability-policies-manage/portal-image-set-legal-hold-tags.png)

9. Ta bort en juridisk spärr genom att ta bort den använda taggen för juridiskt bevarande.

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Funktionen ingår i följande kommando grupper: `az storage container immutability-policy`  och `az storage container legal-hold` . Kör `-h` på dem för att se kommandona.

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Modulen AZ. Storage stöder oföränderligt lagrings utrymme.  Aktivera funktionen genom att följa dessa steg:

1. Kontrol lera att du har den senaste versionen av PowerShellGet installerad: `Install-Module PowerShellGet –Repository PSGallery –Force` .
2. Ta bort alla tidigare installationer av Azure PowerShell.
3. Installera Azure PowerShell: `Install-Module Az –Repository PSGallery –AllowClobber` .

Följande exempel på PowerShell-skript är för referens. Det här skriptet skapar ett nytt lagrings konto och en behållare. Det visar sedan hur du ställer in och rensar juridiska undantag, skapar och låser en tidsbaserad bevarande princip (kallas även för en oföränderlighets-princip) och utökar kvarhållningsintervallet.

Skapa först ett Azure Storage-konto:

```powershell
$resourceGroup = "<Enter your resource group>"
$storageAccount = "<Enter your storage account name>"
$container = "<Enter your container name>"
$location = "<Enter the storage account location>"

# Log in to Azure
Connect-AzAccount
Register-AzResourceProvider -ProviderNamespace "Microsoft.Storage"

# Create your Azure resource group
New-AzResourceGroup -Name $resourceGroup -Location $location

# Create your Azure storage account
$account = New-AzStorageAccount -ResourceGroupName $resourceGroup -StorageAccountName `
    $storageAccount -SkuName Standard_ZRS -Location $location -Kind StorageV2

# Create a new container using the context
$container = New-AzStorageContainer -Name $container -Context $account.Context

# List the containers in the account
Get-AzStorageContainer -Context $account.Context

# Remove a container
Remove-AzStorageContainer -Name $container -Context $account.Context
```

Ange och rensa juridiska undantag:

```powershell
# Set a legal hold
Add-AzRmStorageContainerLegalHold -ResourceGroupName $resourceGroup `
    -StorageAccountName $storageAccount -Name $container -Tag <tag1>,<tag2>,...

# Clear a legal hold
Remove-AzRmStorageContainerLegalHold -ResourceGroupName $resourceGroup `
    -StorageAccountName $storageAccount -Name $container -Tag <tag3>
```

Skapa eller uppdatera tidsbaserade oföränderlighets-principer:

```powershell
# Create a time-based immutability policy
Set-AzRmStorageContainerImmutabilityPolicy -ResourceGroupName $resourceGroup `
    -StorageAccountName $storageAccount -ContainerName $container -ImmutabilityPeriod 10
```

Hämta oföränderlighets-principer:

```powershell
# Get an immutability policy
Get-AzRmStorageContainerImmutabilityPolicy -ResourceGroupName $resourceGroup `
    -StorageAccountName $storageAccount -ContainerName $container
```

Lås oföränderlighets-principer (Lägg till `-Force` för att stänga prompten):

```powershell
# Lock immutability policies
$policy = Get-AzRmStorageContainerImmutabilityPolicy -ResourceGroupName `
    $resourceGroup -StorageAccountName $storageAccount -ContainerName $container
Lock-AzRmStorageContainerImmutabilityPolicy -ResourceGroupName `
    $resourceGroup -StorageAccountName $storageAccount -ContainerName $container `
    -Etag $policy.Etag
```

Utöka oföränderlighets-principer:

```powershell
# Extend immutability policies
$policy = Get-AzRmStorageContainerImmutabilityPolicy -ResourceGroupName `
    $resourceGroup -StorageAccountName $storageAccount -ContainerName $container

Set-AzRmStorageContainerImmutabilityPolicy -ImmutabilityPolicy `
    $policy -ImmutabilityPeriod 11 -ExtendPolicy
```

Ta bort en olåst oföränderlighets-princip (Lägg till `-Force` för att stänga meddelandet):

```powershell
# Remove an unlocked immutability policy
$policy = Get-AzRmStorageContainerImmutabilityPolicy -ResourceGroupName `
    $resourceGroup -StorageAccountName $storageAccount -ContainerName $container

Remove-AzRmStorageContainerImmutabilityPolicy -ImmutabilityPolicy $policy
```

---

## <a name="enabling-allow-protected-append-blobs-writes"></a>Aktivera Tillåt att skyddade bifogade blobbar skrivs

### <a name="portal"></a>[Portal](#tab/azure-portal)

![Tillåt ytterligare append-skrivningar](media/storage-blob-immutability-policies-manage/immutable-allow-additional-append-writes.png)

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Funktionen ingår i följande kommando grupper: `az storage container immutability-policy`  och `az storage container legal-hold` . Kör `-h` på dem för att se kommandona.

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```powershell
# Create an immutability policy with appends allowed
Set-AzRmStorageContainerImmutabilityPolicy -ResourceGroupName $resourceGroup `
    -StorageAccountName $storageAccount -ContainerName $container -ImmutabilityPeriod 10 -AllowProtectedAppendWrite $true
```

---

## <a name="next-steps"></a>Nästa steg

[Lagra affärskritiska blobdata med oföränderlig lagring](storage-blob-immutable-storage.md)
