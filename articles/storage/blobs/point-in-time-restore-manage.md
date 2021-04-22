---
title: Utföra en återställning till en tidpunkt på blockblobdata
titleSuffix: Azure Storage
description: Lär dig hur du använder återställning till tidpunkt för att återställa en uppsättning blockblobar till deras tidigare tillstånd vid en viss tidpunkt.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 01/29/2021
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: e8c926c2fbc5b19f67fb78d321ee3293c73be939
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2021
ms.locfileid: "107869355"
---
# <a name="perform-a-point-in-time-restore-on-block-blob-data"></a>Utföra en återställning till en tidpunkt på blockblobdata

Du kan använda återställning till tidpunkt för att återställa en eller flera uppsättningar blockblobar till ett tidigare tillstånd. Den här artikeln beskriver hur du aktiverar återställning till en tidpunkt för ett lagringskonto och hur du utför en återställningsåtgärd.

Mer information om återställning till tidpunkt finns i Återställning till tidpunkt [för blockblobar.](point-in-time-restore-overview.md)

> [!CAUTION]
> Återställning till tidpunkt stöder endast återställningsåtgärder på blockblobar. Det går inte att återställa åtgärder på containrar. Om du tar bort en container från lagringskontot genom att anropa åtgärden Ta bort [container](/rest/api/storageservices/delete-container) kan inte containern återställas med en återställningsåtgärd. Ta bort enskilda blobar i stället för att ta bort en hel container om du vill återställa dem senare. Microsoft rekommenderar också att du aktiverar mjuk borttagning för containrar och blobar för att skydda mot oavsiktlig borttagning. Mer information finns i Mjuk [borttagning för containrar (förhandsversion)](soft-delete-container-overview.md) och [Mjuk borttagning för blobar.](soft-delete-blob-overview.md)

## <a name="enable-and-configure-point-in-time-restore"></a>Aktivera och konfigurera återställning till en tidpunkt

Innan du aktiverar och konfigurerar återställning till tidpunkt måste du aktivera dess krav för lagringskontot: mjuk borttagning, ändringsflöde och versionshantering av blobar. Mer information om hur du aktiverar var och en av dessa funktioner finns i följande artiklar:

- [Aktivera mjuk borttagning för blobar](./soft-delete-blob-enable.md)
- [Aktivera och inaktivera ändringsflödet](storage-blob-change-feed.md#enable-and-disable-the-change-feed)
- [Aktivera och hantera versionshantering av blobar](versioning-enable.md)

> [!IMPORTANT]
> Om du aktiverar mjuk borttagning, ändringsflöde och versionshantering för blobar kan det leda till ytterligare avgifter. Mer information finns i Mjuk [borttagning för blobar, Ändringsflödesstöd](soft-delete-blob-overview.md) [i Azure Blob Storage](storage-blob-change-feed.md)och [Versionshantering för blobar.](versioning-overview.md)

# <a name="azure-portal"></a>[Azure-portalen](#tab/portal)

Följ dessa steg om du vill konfigurera återställning Azure Portal tidpunkt med hjälp Azure Portal:

1. Navigera till ditt lagringskonto i Azure-portalen.
1. Under **Inställningar** väljer **du Dataskydd.**
1. Välj **Aktivera återställning till tidpunkt.** När du väljer det här alternativet aktiveras även mjuk borttagning för blobar, versionshantering och ändringsflöde.
1. Ange den maximala återställningspunkten för återställning till tidpunkt, i dagar. Det här antalet måste vara minst en dag mindre än kvarhållningsperioden som angetts för mjuk borttagning av blob.
1. Spara ändringarna.

Följande bild visar ett lagringskonto som konfigurerats för återställning till en viss tidpunkt med en återställningspunkt för sju dagar sedan och en kvarhållningsperiod för mjuk blob-borttagning på 14 dagar.

:::image type="content" source="media/point-in-time-restore-manage/configure-point-in-time-restore-portal.png" alt-text="Skärmbild som visar hur du konfigurerar återställning till en tidpunkt i Azure Portal":::

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Om du vill konfigurera återställning till tidpunkt med PowerShell installerar du först [Az.Storage-modul](https://www.powershellgallery.com/packages/Az.Storage) version 2.6.0 eller senare. Anropa sedan [kommandot Enable-AzStorageBlobRestorePolicy](/powershell/module/az.storage/enable-azstorageblobrestorepolicy) för att aktivera återställning till tidpunkt för lagringskontot.

I följande exempel aktiveras mjuk borttagning och kvarhållningsperioden för mjuk borttagning anges, ändringsflöde och versionshantering aktiveras och återställning till tidpunkt aktiveras. Kom ihåg att ersätta värdena inom vinkelparenteser med dina egna värden när du kör exemplet:

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

Om du vill konfigurera återställning till en tidpunkt med Azure CLI installerar du först Azure CLI version 2.2.0 eller senare. Anropa sedan [kommandot az storage account blob-service-properties update](/cli/azure/storage/account/blob-service-properties#az_storage_account_blob_service_properties_update) för att aktivera återställning till tidpunkt och de andra nödvändiga dataskyddsinställningarna för lagringskontot.

Följande exempel aktiverar mjuk borttagning och anger kvarhållningsperioden för mjuk borttagning till 14 dagar, aktiverar ändringsflöde och versionshantering och aktiverar återställning till tidpunkt med en återställningsperiod på 7 dagar. Kom ihåg att ersätta värdena inom vinkelparenteser med dina egna värden när du kör exemplet:

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

## <a name="choose-a-restore-point"></a>Välj en återställningspunkt

Återställningspunkten är det datum och den tid som data återställs till. Azure Storage använder alltid ett datum-/tidsvärde för UTC som återställningspunkt. Men med Azure Portal kan du ange återställningspunkten i lokal tid och sedan konvertera det datum-/tidsvärdet till ett UTC-datum/tid-värde för att utföra återställningen.

När du utför en återställningsåtgärd med PowerShell eller Azure CLI bör du ange återställningspunkten som ett datum-/tidsvärde för UTC. Om återställningspunkten anges med ett lokalt tidsvärde i stället för ett UTC-tidsvärde kan återställningsåtgärden fortfarande fungera som förväntat i vissa fall. Om din lokala tid till exempel är UTC minus fem timmar, resulterar ett lokalt tidsvärde i en återställningspunkt som är fem timmar tidigare än det värde som du angav. Om inga ändringar har gjorts i data i intervallet som ska återställas under den femtimmarsperioden ger återställningsåtgärden samma resultat oavsett vilket tidsvärde som angavs. Vi rekommenderar att du anger en UTC-tid för återställningspunkten för att undvika oväntade resultat.

## <a name="perform-a-restore-operation"></a>Utföra en återställningsåtgärd

Du kan återställa alla containrar i lagringskontot eller återställa ett intervall med blobar i en eller flera containrar. Ett intervall med blobar definieras lexikografiskt, vilket innebär i ordlisteordning. Upp till tio lexicographical intervall stöds per återställningsåtgärd. Början av intervallet är inkluderande och slutet av intervallet är exklusivt.

Containermönstret som anges för startintervallet och slutintervallet måste innehålla minst tre tecken. Det snedstreck (/) som används för att separera ett containernamn från ett blobnamn räknas inte mot detta minimum.

Jokertecken stöds inte i ett lexikografiskt intervall. Jokertecken behandlas som standardtecken.

Du kan återställa blobar i `$root` containrarna `$web` och genom att uttryckligen ange dem i ett intervall som skickas till en återställningsåtgärd. Containrarna `$root` `$web` och återställs bara om de uttryckligen anges. Andra systemcontainrar kan inte återställas.

Endast blockblobar återställs. Sidblobar och tilläggsblobar ingår inte i en återställningsåtgärd. Mer information om begränsningar relaterade till tilläggsblobar finns i [Återställning till tidpunkt för blockblobar.](point-in-time-restore-overview.md)

> [!IMPORTANT]
> När du utför en återställningsåtgärd Azure Storage dataåtgärder på blobarna i intervallen som återställs under åtgärdens varaktighet. Läs-, skriv- och borttagningsåtgärder blockeras på den primära platsen. Därför kanske åtgärder som att visa containrar i Azure Portal inte fungerar som förväntat medan återställningsåtgärden pågår.
>
> Läsåtgärder från den sekundära platsen kan fortsätta under återställningen om lagringskontot geo-replikeras.
>
> Den tid det tar att återställa en uppsättning data baseras på antalet skriv- och borttagningsåtgärder som gjorts under återställningsperioden. Till exempel kräver ett konto med en miljon objekt med 3 000 objekt som lagts till per dag och 1 000 objekt som tas bort per dag cirka två timmar för att återställa till en tidigare tidpunkt på 30 dagar. En kvarhållningsperiod och återställning som är mer än 90 dagar tidigare rekommenderas inte för ett konto med den här ändringsfrekvensen.

### <a name="restore-all-containers-in-the-account"></a>Återställa alla containrar i kontot

Du kan återställa alla containrar i lagringskontot för att återställa dem till deras tidigare tillstånd vid en viss tidpunkt.

# <a name="azure-portal"></a>[Azure-portalen](#tab/portal)

Följ dessa steg om du vill återställa alla containrar och blobar i lagringskontot Azure Portal lagringskontot:

1. Navigera till listan över containrar för ditt lagringskonto.
1. I verktygsfältet väljer du Återställ **containrar** och sedan **Återställ alla**.
1. I fönstret **Återställ alla containrar** anger du återställningspunkten genom att ange datum och tid.
1. Bekräfta att du vill fortsätta genom att markera kryssrutan.
1. Välj **Återställ** för att påbörja återställningen.

    :::image type="content" source="media/point-in-time-restore-manage/restore-all-containers-portal.png" alt-text="Skärmbild som visar hur du återställer alla containrar till en angiven återställningspunkt":::

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Om du vill återställa alla containrar och blobar i lagringskontot med PowerShell anropar du kommandot **Restore-AzStorageBlobRange** och anger återställningspunkten som ett datum-/tidsvärde för UTC. Som standard körs **kommandot Restore-AzStorageBlobRange** asynkront och returnerar ett objekt av typen **PSBlobRestoreStatus** som du kan använda för att kontrollera status för återställningsåtgärden.

I följande exempel återställs containrar i lagringskontot asynkront till sitt tillstånd 12 timmar före det aktuella tillfället och kontrollerar några av egenskaperna för återställningsåtgärden:

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

Om du vill köra återställningsåtgärden synkront inkluderar du **parametern -WaitForComplete** i kommandot . När **parametern -WaitForComplete** finns visar PowerShell ett meddelande som innehåller återställnings-ID:t för åtgärden och blockerar sedan körningen tills återställningsåtgärden har slutförts. Tänk på att den tid som krävs av en återställningsåtgärd beror på mängden data som ska återställas och en stor återställningsåtgärd kan ta upp till en timme att slutföra.

```powershell
Restore-AzStorageBlobRange -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -TimeToRestore (Get-Date).AddHours(-12) -WaitForComplete
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Om du vill återställa alla containrar och blobar i lagringskontot med Azure CLI anropar du [kommandot az storage blob restore](/cli/azure/storage/blob#az_storage_blob_restore) och anger återställningspunkten som ett datum-/tidsvärde för UTC.

I följande exempel återställs alla containrar i lagringskontot asynkront till sitt tillstånd 12 timmar före ett angivet datum och tid. Om du vill kontrollera status för återställningsåtgärden anropar [du az storage account show](/cli/azure/storage/account#az_storage_account_show):

```azurecli
az storage blob restore \
    --resource-group <resource_group> \
    --account-name <storage-account> \
    --time-to-restore 2021-01-14T06:31:22Z \
    --no-wait
```

Om du vill kontrollera egenskaperna för en återställningsåtgärd [anropar du az storage account show](/cli/azure/storage/account#az_storage_account_show) och expanderar egenskapen **blobRestoreStatus.** I följande exempel visas hur du kontrollerar **statusegenskapen.**

```azurecli
az storage account show \
    --name <storage-account> \
    --resource-group <resource_group> \ 
    --expand blobRestoreStatus \
    --query blobRestoreStatus.status \
    --output tsv
```

Om du vill **köra kommandot az storage blob restore** synkront och blockera vid körning tills återställningen är klar utelämnar du `--no-wait` parametern .

---

### <a name="restore-ranges-of-block-blobs"></a>Återställa intervall med blockblobar

Du kan återställa ett eller flera lexikographiciska intervall med blobar i en enda container eller över flera containrar för att returnera dessa blobar till deras tidigare tillstånd vid en viss tidpunkt.

# <a name="azure-portal"></a>[Azure-portalen](#tab/portal)

Om du vill återställa ett antal blobar i en eller flera containrar med Azure Portal gör du följande:

1. Navigera till listan över containrar för ditt lagringskonto.
1. Välj den container eller de containrar som ska återställas.
1. I verktygsfältet väljer du Återställ **containrar** och sedan **Återställ markerat.**
1. I fönstret **Återställ valda containrar** anger du återställningspunkten genom att ange datum och tid.
1. Ange de intervall som ska återställas. Använd ett snedstreck (/) för att avgränsa containernamnet från blobprefixet.
1. Som standard anger **fönstret Återställ valda** containrar ett intervall som innehåller alla blobar i containern. Ta bort det här intervallet om du inte vill återställa hela containern. Standardintervallet visas i följande bild.

    :::image type="content" source="media/point-in-time-restore-manage/delete-default-blob-range.png" alt-text="Skärmbild som visar standardblobintervallet som ska tas bort innan du anger anpassat intervall":::

1. Bekräfta att du vill fortsätta genom att markera kryssrutan.
1. Välj **Återställ** för att påbörja återställningsåtgärden.

Följande bild visar en återställningsåtgärd i en uppsättning intervall.

:::image type="content" source="media/point-in-time-restore-manage/restore-multiple-container-ranges-portal.png" alt-text="Skärmbild som visar hur du återställer intervall med blobar i en eller flera containrar":::

Återställningsåtgärden som visas i bilden utför följande åtgärder:

- Återställer det fullständiga innehållet i *container1*.
- Återställer blobar i det lexicographicala intervallet *blob1 till* *blob5* i *container2*. Det här intervallet återställer blobar med namn som *blob1*, *blob11,* *blob100,* *blob2* och så vidare. Eftersom slutet av intervallet är exklusivt återställs blobar vars namn börjar med *blob4*, men inte blobar vars namn börjar med *blob5*.
- Återställer alla blobar i *container3* och *container4.* Eftersom slutet av intervallet är exklusivt återställs inte container5 i det *här intervallet.*

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Om du vill återställa ett enda blobintervall anropar du kommandot **Restore-AzStorageBlobRange** och anger ett lexicographicalt intervall med container- och blobnamn för `-BlobRestoreRange` parametern . Om du till exempel vill återställa blobarna i en enda container med namnet *container1* kan du ange ett intervall som börjar med *container1* och slutar med *container2*. Det finns inga krav på att containrarna med namnet i start- och slutintervallen ska finnas. Eftersom slutet av intervallet är exklusivt, även om lagringskontot innehåller en container med namnet *container2,* kommer endast containern med namnet *container1* att återställas:

```powershell
$range = New-AzStorageBlobRangeToRestore -StartRange container1 `
    -EndRange container2
```

Om du vill ange en delmängd av blobar i en container som ska återställas använder du ett snedstreck (/) för att separera containernamnet från blobprefixmönstret. Följande intervall väljer till exempel blobar i en enda container vars namn börjar med bokstäverna *d* till *f*:

```powershell
$range = New-AzStorageBlobRangeToRestore -StartRange container1/d `
    -EndRange container1/g
```

Ange sedan intervallet för kommandot **Restore-AzStorageBlobRange.** Ange återställningspunkten genom att ange ett UTC **DateTime-värde** för `-TimeToRestore` parametern. I följande exempel återställs blobar i det angivna intervallet till deras tillstånd 3 dagar före det aktuella tillfället:

```powershell
# Specify -TimeToRestore as a UTC value
Restore-AzStorageBlobRange -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -BlobRestoreRange $range `
    -TimeToRestore (Get-Date).AddDays(-3)
```

Som standard körs **kommandot Restore-AzStorageBlobRange** asynkront. När du initierar en återställningsåtgärd asynkront visar PowerShell omedelbart en tabell med egenskaper för åtgärden:  

```powershell
Status     RestoreId                            FailureReason Parameters.TimeToRestore     Parameters.BlobRanges
------     ---------                            ------------- ------------------------     ---------------------
InProgress 459c2305-d14a-4394-b02c-48300b368c63               2020-09-15T23:23:07.1490859Z ["container1/d" -> "container1/g"]
```

Om du vill återställa flera intervall med blockblobar anger du en matris med intervall för `-BlobRestoreRange` parametern . I följande exempel anges två intervall för att återställa hela innehållet i *container1* och *container4* till deras tillstånd för 24 timmar sedan och sparar resultatet till en variabel:

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

Om du vill köra återställningsåtgärden synkront och blockera vid körning tills den är klar, inkluderar du parametern **-WaitForComplete** i kommandot .

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Om du vill återställa ett intervall med blobar anropar du kommandot [az storage blob restore](/cli/azure/storage/blob#az_storage_blob_restore) och anger ett lexicographical range med container- och blobnamn för `--blob-range` parametern . Om du vill ange flera intervall anger du `--blob-range` parametern för varje distinkt intervall.

Om du till exempel vill återställa blobarna i en enda container med namnet *container1* kan du ange ett intervall som börjar med *container1* och slutar med *container2.* Det finns inga krav på att containrarna med namnet i start- och slutintervallen ska finnas. Eftersom slutet av intervallet är exklusivt, även om lagringskontot innehåller en container med namnet *container2,* återställs bara containern med namnet *container1.*

Om du vill ange en delmängd av blobar i en container som ska återställas använder du ett snedstreck (/) för att separera containernamnet från blobprefixmönstret. Exemplet som visas nedan återställer asynkront ett intervall med blobar i en container vars namn börjar med bokstäverna `d` via `f` .

```azurecli
az storage blob restore \
    --account-name <storage-account> \
    --time-to-restore 2021-01-14T06:31:22Z \
    --blob-range container1 container2
    --blob-range container3/d container3/g
    --no-wait
```

Om du vill **köra kommandot az storage blob restore** synkront och blockera vid körning tills återställningen är klar utelämnar du `--no-wait` parametern .

---

## <a name="next-steps"></a>Nästa steg

- [Återställning till tidpunkt för blockblobar](point-in-time-restore-overview.md)
- [Mjuk borttagning](./soft-delete-blob-overview.md)
- [Ändringsflöde](storage-blob-change-feed.md)
- [Versionshantering av blobar](versioning-overview.md)