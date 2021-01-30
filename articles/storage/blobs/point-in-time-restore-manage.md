---
title: Utföra en tidpunkts återställning på block BLOB-data
titleSuffix: Azure Storage
description: Lär dig hur du använder punkt-i-Time-återställning för att återställa en uppsättning block blobbar till sitt tidigare tillstånd vid en viss tidpunkt.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 01/29/2021
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: b62e341d35a4ff7fd5a7ddd6d9f19b138aaf0aa9
ms.sourcegitcommit: dd24c3f35e286c5b7f6c3467a256ff85343826ad
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2021
ms.locfileid: "99071655"
---
# <a name="perform-a-point-in-time-restore-on-block-blob-data"></a>Utföra en tidpunkts återställning på block BLOB-data

Du kan använda tidpunkts återställning för att återställa en eller flera uppsättningar block blobbar till ett tidigare tillstånd. I den här artikeln beskrivs hur du aktiverar återställning av tidpunkter för ett lagrings konto och hur du utför en återställnings åtgärd.

Om du vill veta mer om återställning av punkt-i-tid kan du läsa mer om att återställa en tidpunkt [för block-blobar](point-in-time-restore-overview.md).

> [!CAUTION]
> Återställning vid tidpunkt stöder bara återställnings åtgärder på block-blobbar. Det går inte att återställa åtgärder på behållare. Om du tar bort en behållare från lagrings kontot genom att anropa åtgärden [ta bort behållare](/rest/api/storageservices/delete-container) , kan den behållaren inte återställas med en återställnings åtgärd. Ta bort enskilda blobbar i stället för att ta bort en hel behållare om du vill återställa dem senare. Microsoft rekommenderar också att du aktiverar mjuk borttagning för behållare och blobbar för att skydda mot oavsiktlig borttagning. Mer information finns i [mjuk borttagning för behållare (för hands version)](soft-delete-container-overview.md) och [mjuk borttagning för blobbar](soft-delete-blob-overview.md).

## <a name="enable-and-configure-point-in-time-restore"></a>Aktivera och konfigurera återställning av tidpunkter

Innan du aktiverar och konfigurerar återställning av tidpunkt, aktiverar du dess krav för lagrings kontot: mjuk borttagning, ändrings flöde och blob-versioner. Mer information om hur du aktiverar var och en av dessa funktioner finns i följande artiklar:

- [Aktivera mjuk borttagning för blobar](./soft-delete-blob-enable.md)
- [Aktivera och inaktivera ändrings flödet](storage-blob-change-feed.md#enable-and-disable-the-change-feed)
- [Aktivera och hantera BLOB-versioner](versioning-enable.md)

> [!IMPORTANT]
> Om du aktiverar mjuk borttagning, ändrings flöde och blob-versioner kan ytterligare kostnader uppstå. Mer information finns i [mjuk borttagning för blobbar](soft-delete-blob-overview.md), [ändra feed-stöd i Azure Blob Storage](storage-blob-change-feed.md)och blob- [versioner](versioning-overview.md).

# <a name="azure-portal"></a>[Azure-portalen](#tab/portal)

Följ dessa steg om du vill konfigurera tidpunkts återställning med Azure Portal:

1. Navigera till ditt lagringskonto i Azure-portalen.
1. Under **Inställningar** väljer du **data skydd**.
1. Välj **Aktivera återställning av tidpunkt** . När du väljer det här alternativet aktive ras även mjuk borttagning för blobbar, versions hantering och ändrings flöden.
1. Ange den maximala återställnings punkten för återställning vid tidpunkt, i dagar. Antalet måste vara minst en dag under den angivna kvarhållningsperioden för BLOB-mjuk borttagning.
1. Spara ändringarna.

Följande bild visar ett lagrings konto som har kon figurer ATS för återställning vid olika tidpunkter med en återställnings punkt på sju dagar sedan, och en kvarhållningsperiod för BLOB-mjuk borttagning av 14 dagar.

:::image type="content" source="media/point-in-time-restore-manage/configure-point-in-time-restore-portal.png" alt-text="Skärm bild som visar hur du konfigurerar återställning av tidpunkt i Azure Portal":::

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Om du vill konfigurera tidpunkts återställning med PowerShell installerar du först [AZ. Storage](https://www.powershellgallery.com/packages/Az.Storage) module version 2.6.0 eller senare. Anropa sedan kommandot [Enable-AzStorageBlobRestorePolicy](/powershell/module/az.storage/enable-azstorageblobrestorepolicy) för att aktivera återställning av tidpunkter för lagrings kontot.

I följande exempel aktive ras mjuk borttagning och ställer in lagrings perioden för mjuk borttagning, aktiverar ändrings flöde och versions hantering och aktiverar sedan återställning vid tidpunkter. Kom ihåg att ersätta värdena i vinkelparenteser med dina egna värden när du kör exemplet:

```powershell
# Set resource group and account variables.
$rgName = "<resource-group>"
$accountName = "<storage-account>"

# Enable blob soft delete with a retention of 14 days.
Enable-AzStorageBlobDeleteRetentionPolicy -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -RetentionDays 14

# Enable change feed and versioning.
Update-AzStorageBlobServiceProperty -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -EnableChangeFeed $true `
    -IsVersioningEnabled $true

# Enable point-in-time restore with a retention period of 7 days.
# The retention period for point-in-time restore must be at least
# one day less than that set for soft delete.
Enable-AzStorageBlobRestorePolicy -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -RestoreDays 7

# View the service settings.
Get-AzStorageBlobServiceProperty -ResourceGroupName $rgName `
    -StorageAccountName $accountName
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Om du vill konfigurera tidpunkts återställning med Azure CLI måste du först installera Azure CLI version 2.2.0 eller senare. Anropa sedan kommandot [AZ Storage Account BLOB-service-Properties Update](/cli/azure/ext/storage-blob-preview/storage/account/blob-service-properties#ext_storage_blob_preview_az_storage_account_blob_service_properties_update) för att aktivera återställning av tidpunkter och andra nödvändiga data skydds inställningar för lagrings kontot.

I följande exempel aktive ras mjuk borttagning och anger perioden för mjuk borttagning till 14 dagar, aktiverar ändrings flöde och versions hantering och aktiverar återställning av tidpunkter med en återställnings period på sju dagar. Kom ihåg att ersätta värdena i vinkelparenteser med dina egna värden när du kör exemplet:

```azurecli
az storage account blob-service-properties update \
    --resource-group <resource_group> \
    --account-name <storage-account> \
    --enable-delete-retention true \
    --delete-retention-days 14 \
    --enable-versioning true \
    --enable-change-feed true \
    --enable-restore-policy true \
    --restore-days 7
```

---

## <a name="choose-a-restore-point"></a>Välj en återställnings punkt

Återställnings punkten är datum och tid då data återställs. Azure Storage använder alltid ett UTC-datum/tid-värde som återställnings punkt. Med Azure Portal kan du dock ange återställnings punkten i lokal tid och sedan konvertera datum/tid-värdet till ett UTC-datum/tid-värde för att utföra återställnings åtgärden.

När du utför en återställnings åtgärd med PowerShell eller Azure CLI bör du ange återställnings punkten som ett UTC-datum/tid-värde. Om återställnings punkten anges med ett lokalt tids värde i stället för ett UTC-tidsvärde kan återställningen fortfarande fungera som förväntat i vissa fall. Om din lokala tid till exempel är UTC minus fem timmar kan du ange ett lokalt tids värde i en återställnings punkt som är fem timmar tidigare än det värde som du har angett. Om inga ändringar har gjorts i data i intervallet som ska återställas under den fem Tim perioden, genererar återställnings åtgärden samma resultat oavsett vilket tids värde som tillhandahölls. Att ange en UTC-tid för återställnings punkten rekommenderas för att undvika oväntade resultat.

## <a name="perform-a-restore-operation"></a>Utföra en återställnings åtgärd

Du kan återställa alla behållare i lagrings kontot, eller så kan du återställa ett intervall av blobbar i en eller flera behållare. Ett intervall med blobbar definieras lexicographically, vilket betyder i ord listornas ordning. Upp till tio lexicographical-intervall stöds per återställnings åtgärd. Början av intervallet är inkluderat och slutet av intervallet är exklusivt.

Behållar mönstret som har angetts för start intervallet och slut intervallet måste innehålla minst tre tecken. Det snedstreck (/) som används för att avgränsa ett behållar namn från ett BLOB-namn räknas inte mot det här minimivärdet.

Jokertecken stöds inte i ett lexicographical-intervall. Jokertecken behandlas som standard tecken.

Du kan återställa blobbar i `$root` `$web` behållaren och genom att uttryckligen ange dem i ett intervall som skickas till en återställnings åtgärd. - `$root` Och- `$web` behållare återställs endast om de uttryckligen anges. Andra system behållare kan inte återställas.

Endast block blobbar återställs. Page blobbar och bifogade blobbar ingår inte i en återställnings åtgärd. Mer information om begränsningar som rör tillägg av bifogade blobbar finns i [punkt-i-tids återställning för block-blobar](point-in-time-restore-overview.md).

> [!IMPORTANT]
> När du utför en återställnings åtgärd, Azure Storage blockera data åtgärder på Blobbarna i de intervall som återställs under drift tiden. Läs-, skriv-och borttagnings åtgärder blockeras på den primära platsen. Därför kan åtgärder som att Visa behållare i Azure Portal inte utföras som förväntat medan återställnings åtgärden pågår.
>
> Läs åtgärder från den sekundära platsen kan fortsätta under återställnings åtgärden om lagrings kontot är geo-replikerat.
>
> Den tid det tar att återställa en data uppsättning baseras på antalet skriv-och borttagnings åtgärder som gjorts under återställnings perioden. Till exempel kräver ett konto med 1 000 000 objekt med 3 000 objekt som lagts till per dag och 1 000 objekt som tas bort per dag cirka två timmar för att återställa till en punkt 30 dagar tidigare. En kvarhållningsperiod och återställningen över 90 dagar tidigare skulle inte rekommenderas för ett konto med den här ändrings takten.

### <a name="restore-all-containers-in-the-account"></a>Återställ alla behållare i kontot

Du kan återställa alla behållare i lagrings kontot för att returnera dem till sitt tidigare tillstånd vid en viss tidpunkt.

# <a name="azure-portal"></a>[Azure-portalen](#tab/portal)

Följ dessa steg om du vill återställa alla behållare och blobbar i lagrings kontot med Azure Portal:

1. Navigera till listan över behållare för ditt lagrings konto.
1. I verktygsfältet väljer du **återställnings behållare** och sedan **Återställ alla**.
1. I fönstret **Återställ alla behållare** anger du återställnings punkten genom att ange ett datum och en tid.
1. Bekräfta att du vill fortsätta genom att markera rutan.
1. Välj **Återställ** för att påbörja återställnings åtgärden.

    :::image type="content" source="media/point-in-time-restore-manage/restore-all-containers-portal.png" alt-text="Skärm bild som visar hur du återställer alla behållare till en angiven återställnings punkt":::

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Om du vill återställa alla behållare och blobbar i lagrings kontot med PowerShell anropar du kommandot **restore-AzStorageBlobRange** och anger återställnings punkten som ett UTC-datum/tid-värde. Som standard körs kommandot **restore-AzStorageBlobRange** asynkront och returnerar ett objekt av typen **PSBlobRestoreStatus** som du kan använda för att kontrol lera status för återställnings åtgärden.

I följande exempel återställs behållare i lagrings kontot till sitt tillstånd 12 timmar före den aktuella tidpunkten, och några av egenskaperna för återställnings åtgärden kontrol leras:

```powershell
# Specify -TimeToRestore as a UTC value
$restoreOperation = Restore-AzStorageBlobRange -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -TimeToRestore (Get-Date).ToUniversalTime().AddHours(-12)

# Get the status of the restore operation.
$restoreOperation.Status
# Get the ID for the restore operation.
$restoreOperation.RestoreId
# Get the restore point in UTC time.
$restoreOperation.Parameters.TimeToRestore
```

Om du vill köra återställnings åtgärden synkront tar du med parametern **-WaitForComplete** i kommandot. När parametern **-WaitForComplete** finns, visar PowerShell ett meddelande som innehåller återställnings-ID för åtgärden och blockerar sedan körningen tills återställnings åtgärden har slutförts. Tänk på att den tid som krävs för en återställnings åtgärd beror på mängden data som ska återställas och att det kan ta upp till en timme att slutföra en stor återställnings åtgärd.

```powershell
Restore-AzStorageBlobRange -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -TimeToRestore (Get-Date).AddHours(-12) -WaitForComplete
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Om du vill återställa alla behållare och blobbar i lagrings kontot med Azure CLI anropar du kommandot [AZ Storage BLOB Restore](/cli/azure/storage/blob#az_storage_blob_restore) och anger återställnings punkten som ett UTC-datum/tid-värde.

I följande exempel återställer asynkront alla behållare i lagrings kontot till deras tillstånd 12 timmar innan ett angivet datum och en angiven tid. Du kan kontrol lera status för återställnings åtgärden genom att anropa [AZ Storage Account show](/cli/azure/storage/account#az_storage_account_show):

```azurecli
az storage blob restore \
    --resource-group <resource_group> \
    --account-name <storage-account> \
    --time-to-restore 2021-01-14T06:31:22Z \
    --no-wait
```

Om du vill kontrol lera egenskaperna för en återställnings åtgärd, anropa [AZ Storage Account (Visa](/cli/azure/storage/account#az_storage_account_show) och expandera egenskapen **blobRestoreStatus** ). I följande exempel visas hur du kontrollerar egenskapen **status** .

```azurecli
az storage account show \
    --name <storage-account> \
    --resource-group <resource_group> \ 
    --expand blobRestoreStatus \
    --query blobRestoreStatus.status \
    --output tsv
```

Om du vill köra kommandot **AZ Storage BLOB Restore** och blockera vid körning tills återställningen är klar utelämnar du `--no-wait` parametern.

---

### <a name="restore-ranges-of-block-blobs"></a>Återställa intervall för block-blobbar

Du kan återställa en eller flera lexicographical-intervall med blobbar i en enda behållare eller över flera behållare för att returnera dessa blobbar till sitt tidigare tillstånd vid en viss tidpunkt.

# <a name="azure-portal"></a>[Azure-portalen](#tab/portal)

Följ dessa steg om du vill återställa ett intervall av blobbar i en eller flera behållare med Azure Portal:

1. Navigera till listan över behållare för ditt lagrings konto.
1. Välj den behållare eller de behållare som ska återställas.
1. I verktygsfältet väljer du **återställnings behållare** och sedan **Återställ markerade**.
1. I rutan **Återställ valda behållare** anger du återställnings punkten genom att ange ett datum och en tid.
1. Ange de intervall som ska återställas. Använd ett snedstreck (/) för att avgränsa behållar namnet från BLOB-prefixet.
1. Som standard anger rutan **Återställ valda behållare** ett intervall som innehåller alla blobbar i behållaren. Ta bort det här intervallet om du inte vill återställa hela behållaren. Standard intervallet visas i följande bild.

    :::image type="content" source="media/point-in-time-restore-manage/delete-default-blob-range.png" alt-text="Skärm bild som visar det standard-BLOB-intervall som ska tas bort innan det anpassade intervallet anges":::

1. Bekräfta att du vill fortsätta genom att markera rutan.
1. Välj **Återställ** för att påbörja återställnings åtgärden.

Följande bild visar en återställnings åtgärd för en uppsättning intervall.

:::image type="content" source="media/point-in-time-restore-manage/restore-multiple-container-ranges-portal.png" alt-text="Skärm bild som visar hur du återställer intervall av blobbar i en eller flera behållare":::

Återställnings åtgärden som visas i avbildningen utför följande åtgärder:

- Återställer det fullständiga innehållet i *container1*.
- Återställer blobbar i lexicographical-intervallet *blob1* via *blob5* i *container2*. Det här intervallet återställer blobbar med namn som *blob1*, *blob11*, *blob100*, *blob2* och så vidare. Eftersom slutet på intervallet är exklusivt återställs blobbar vars namn börjar med *blob4*, men återställer inte blobbar vars namn börjar med *blob5*.
- Återställer alla blobbar i *container3* och *container4*. Eftersom slutet på intervallet är exklusivt återställs inte *container5*.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Om du vill återställa en enda uppsättning blobbar anropar du kommandot **restore-AzStorageBlobRange** och anger ett lexicographical-intervall med behållare och blob-namn för `-BlobRestoreRange` parametern. Om du till exempel vill återställa Blobbarna i en enda behållare med namnet *container1* kan du ange ett intervall som börjar med *container1* och slutar med *container2*. Det finns inga krav på att behållarna med namnet i Start-och slut intervall ska finnas. Eftersom slutet på intervallet är exklusivt, även om lagrings kontot innehåller en behållare med namnet *container2*, kommer endast behållaren med namnet *container1* att återställas:

```powershell
$range = New-AzStorageBlobRangeToRestore -StartRange container1 `
    -EndRange container2
```

Om du vill ange en delmängd av blobbar i en behållare som ska återställas använder du ett snedstreck (/) för att avgränsa behållar namnet från mönstret för BLOB-prefixet. Följande intervall väljer till exempel blobbar i en enda behållare vars namn börjar med bokstäverna *d* till *f*:

```powershell
$range = New-AzStorageBlobRangeToRestore -StartRange container1/d `
    -EndRange container1/g
```

Ange sedan intervallet till kommandot **restore-AzStorageBlobRange** . Ange återställnings punkten genom att ange ett UTC- **datetime** -värde för `-TimeToRestore` parametern. I följande exempel återställs blobbar i det angivna intervallet till status 3 dagar före den aktuella tidpunkten:

```powershell
# Specify -TimeToRestore as a UTC value
Restore-AzStorageBlobRange -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -BlobRestoreRange $range `
    -TimeToRestore (Get-Date).AddDays(-3)
```

Som standard körs kommandot **restore-AzStorageBlobRange** asynkront. När du startar en återställnings åtgärd asynkront, visar PowerShell omedelbart en tabell med egenskaper för åtgärden:  

```powershell
Status     RestoreId                            FailureReason Parameters.TimeToRestore     Parameters.BlobRanges
------     ---------                            ------------- ------------------------     ---------------------
InProgress 459c2305-d14a-4394-b02c-48300b368c63               2020-09-15T23:23:07.1490859Z ["container1/d" -> "container1/g"]
```

Om du vill återställa flera intervall block blobbar anger du en matris med intervall för `-BlobRestoreRange` parametern. I följande exempel anges två intervall för att återställa det fullständiga innehållet i *container1* och *container4* till sitt tillstånd för 24 timmar sedan och sparar resultatet i en variabel:

```powershell
# Specify a range that includes the complete contents of container1.
$range1 = New-AzStorageBlobRangeToRestore -StartRange container1 `
    -EndRange container2
# Specify a range that includes the complete contents of container4.
$range2 = New-AzStorageBlobRangeToRestore -StartRange container4 `
    -EndRange container5

$restoreOperation = Restore-AzStorageBlobRange -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -TimeToRestore (Get-Date).AddHours(-24) `
    -BlobRestoreRange @($range1, $range2)

# Get the status of the restore operation.
$restoreOperation.Status
# Get the ID for the restore operation.
$restoreOperation.RestoreId
# Get the blob ranges specified for the operation.
$restoreOperation.Parameters.BlobRanges
```

Om du vill köra återställnings åtgärden synkront och blockera vid körning tills den är slutförd, inkluderar du parametern **-WaitForComplete** i kommandot.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Om du vill återställa ett intervall med blobbar anropar du kommandot [AZ Storage BLOB Restore](/cli/azure/storage/blob#az_storage_blob_restore) och anger ett lexicographical-intervall för parameterns behållare och blob-namn `--blob-range` . Om du vill ange flera intervall anger du `--blob-range` parametern för varje distinkt intervall.

Om du till exempel vill återställa Blobbarna i en enda behållare med namnet *container1* kan du ange ett intervall som börjar med *container1* och slutar med *container2*. Det finns inga krav på att behållarna med namnet i Start-och slut intervall ska finnas. Eftersom slutet på intervallet är exklusivt, även om lagrings kontot innehåller en behållare med namnet *container2*, kommer endast behållaren som heter *container1* att återställas.

Om du vill ange en delmängd av blobbar i en behållare som ska återställas använder du ett snedstreck (/) för att avgränsa behållar namnet från mönstret för BLOB-prefixet. Exemplet som visas nedan återställer ett intervall med blobbar i en behållare vars namn börjar med bokstäverna `d` genom `f` .

```azurecli
az storage blob restore \
    --account-name <storage-account> \
    --time-to-restore 2021-01-14T06:31:22Z \
    --blob-range container1 container2
    --blob-range container3/d container3/g
    --no-wait
```

Om du vill köra kommandot **AZ Storage BLOB Restore** och blockera vid körning tills återställningen är klar utelämnar du `--no-wait` parametern.

---

## <a name="next-steps"></a>Nästa steg

- [Återställning av tidpunkter för block-blobar](point-in-time-restore-overview.md)
- [Mjuk borttagning](./soft-delete-blob-overview.md)
- [Ändra feed](storage-blob-change-feed.md)
- [BLOB-versioner](versioning-overview.md)