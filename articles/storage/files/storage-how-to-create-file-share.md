---
title: Skapa en Azure-filresurs
titleSuffix: Azure Files
description: Skapa en Azure-filresurs med hjälp av Azure Portal, PowerShell eller Azure CLI.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 04/05/2021
ms.author: rogarana
ms.subservice: files
ms.custom: devx-track-azurecli, references_regions
ms.openlocfilehash: 9caabb8dc7f09e4ef3852d9269d178c086744779
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107789815"
---
# <a name="create-an-azure-file-share"></a>Skapa en Azure-filresurs
Om du vill skapa en Azure-filresurs måste du besvara tre frågor om hur du ska använda den:

- **Vilka är prestandakraven för din Azure-filresurs?**  
    Azure Files erbjuder standardfilresurser som finns på hårddiskbaserad (HDD-baserad) maskinvara och premiumfilresurser som finns på SOLID-State Disk-baserad (SSD-baserad) maskinvara.

- **Vilka är dina redundanskrav för din Azure-filresurs?**  
    Standardfilresurser erbjuder lokalt redundant (LRS), zonredundant (ZRS), geo-redundant (GRS) eller geo-zonredundant lagring (GZRS), men funktionen för stora filresurser stöds endast på lokalt redundanta filresurser och zonredundant filresurser. Premium-filresurser stöder inte någon form av geo-redundans.

    Premium-filresurser är tillgängliga med lokalt redundans och zonredundans i en delmängd av regionerna. Om du vill ta reda på om Premium-filresurser för närvarande är tillgängliga i din region kan du gå till [sidan produkter som är tillgängliga per region](https://azure.microsoft.com/global-infrastructure/services/?products=storage) för Azure. Information om regioner som stöder ZRS finns i [Azure Storage redundans](../common/storage-redundancy.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

- **Hur stor filresurs behöver du?**  
    I lokala och zonredundant lagringskonton kan Azure-filresurser sträcka sig över upp till 100 TiB, men i geo- och geo-zonredundant lagringskonton kan Azure-filresurser endast omfatta upp till 5 TiB. 

Mer information om dessa tre alternativ finns i [Planera för en Azure Files distribution](storage-files-planning.md).

## <a name="prerequisites"></a>Förutsättningar
- Den här artikeln förutsätter att du redan har skapat en Azure-prenumeration. Om du inte redan har en prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.
- Om du tänker använda Azure PowerShell installerar [du den senaste versionen](/powershell/azure/install-az-ps).
- Om du tänker använda Azure CLI installerar [du den senaste versionen](/cli/azure/install-azure-cli).

## <a name="create-a-storage-account"></a>Skapa ett lagringskonto
Azure-filresurser distribueras till *lagringskonton*, som är objekt på den översta nivån som representerar en delad lagringspool. Den här lagringspoolen kan användas för att distribuera flera filresurser. 

Azure stöder flera typer av lagringskonton för olika lagringsscenarier som kunder kan ha, men det finns två huvudsakliga typer av lagringskonton för Azure Files. Vilken typ av lagringskonto du behöver skapa beror på om du vill skapa en standardfilresurs eller en premiumfilresurs: 

- **GPv2-lagringskonton (GPv2)** för generell användning: Med GPv2-lagringskonton kan du distribuera Azure-filresurser på standard-/hårddiskbaserad (HDD-baserad) maskinvara. Förutom att lagra Azure-filresurser kan GPv2-lagringskonton lagra andra lagringsresurser, till exempel blobcontainrar, köer eller tabeller. Filresurser kan distribueras till transaktionsoptimerade (standard), heta eller coola nivåer.

- **FileStorage-lagringskonton:** Med FileStorage-lagringskonton kan du distribuera Azure-filresurser på premium-/solid state-diskbaserad (SSD-baserad) maskinvara. FileStorage-konton kan bara användas för att lagra Azure-filresurser. inga andra lagringsresurser (blobcontainrar, köer, tabeller osv.) kan distribueras i ett FileStorage-konto.

# <a name="portal"></a>[Portal](#tab/azure-portal)
Om du vill skapa ett lagringskonto via Azure Portal väljer **du + Skapa en resurs** på instrumentpanelen. I det Azure Marketplace sökfönstret söker du efter **lagringskonto** och väljer det resulterande sökresultatet. Detta leder till en översiktssida för lagringskonton. Välj **Skapa för** att fortsätta med guiden skapa lagringskonto.

![En skärmbild av alternativet för snabbstart för lagringskonto i en webbläsare](media/storage-how-to-create-file-share/create-storage-account-0.png)

#### <a name="basics"></a>Grundläggande inställningar
Det första avsnittet som ska slutföras för att skapa ett lagringskonto kallas **Grundläggande.** Detta innehåller alla obligatoriska fält för att skapa ett lagringskonto. Om du vill skapa ett GPv2-lagringskonto ser  du till att alternativknappen Prestanda är inställd på *Standard* och att listrutan Typ av konto har valts till  *StorageV2 (generell användning v2).*

:::image type="content" source="media/storage-how-to-create-file-share/files-create-smb-share-performance-standard.png" alt-text="En skärmbild av alternativknappen för prestanda med standard valt och typ av konto med storagev2 valt.":::

Om du vill skapa ett FileStorage-lagringskonto ser du till att **alternativknappen** Prestanda är inställd på *Premium* och att **Filresurs** har valts i listrutan Premium-kontotyp. 

:::image type="content" source="media/storage-how-to-create-file-share/files-create-smb-share-performance-premium.png" alt-text="En skärmbild av alternativknappen prestanda med Premium valt och konto typ med filtorage valt.":::

De andra grundläggande fälten är oberoende av valet av lagringskonto:
- **Lagringskontonamn:** Namnet på lagringskontoresursen som ska skapas. Det här namnet måste vara globalt unikt, men annars kan du välja vilket namn du vill. Lagringskontots namn används som servernamn när du monterar en Azure-filresurs via SMB.
- **Plats:** Den region för lagringskontot som ska distribueras till. Detta kan vara den region som är associerad med resursgruppen eller någon annan tillgänglig region.
- **Replikering:** Även om det här är märkt replikering innebär det här fältet faktiskt **redundans**; Det här är önskad redundansnivå: lokalt redundans (LRS), zonredundans (ZRS), georedundans (GRS) och geo-zonredundans (GZRS). Den här listrutan innehåller också RA-GRS (Read-Access Geo-Redundancy) och ra-GZRS (read-access geo-zone redundancy), som inte gäller för Azure-filresurser. alla filresurs som skapas i ett lagringskonto med dessa valda blir faktiskt antingen geo-redundant eller geo-zonredundant. 

#### <a name="networking"></a>Nätverk
I avsnittet nätverk kan du konfigurera nätverksalternativ. De här inställningarna är valfria för att skapa lagringskontot och kan konfigureras senare om du vill. Mer information om dessa alternativ finns i Azure Files [nätverksöverväganden](storage-files-networking-overview.md).

#### <a name="data-protection"></a>Dataskydd
I avsnittet dataskydd kan du konfigurera principen för mjuk borttagning för Azure-filresurser i ditt lagringskonto. Andra inställningar som rör mjuk borttagning för blobar, containrar, återställning till tidpunkt för containrar, versionshantering och ändringsflöde gäller endast för Azure Blob Storage.

#### <a name="advanced"></a>Avancerat
Det avancerade avsnittet innehåller flera viktiga inställningar för Azure-filresurser:

- **Säker överföring krävs:** Det här fältet anger om lagringskontot kräver kryptering under överföring för kommunikation till lagringskontot. Om du behöver stöd för SMB 2.1 måste du inaktivera detta.

    :::image type="content" source="media/storage-how-to-create-file-share/files-create-smb-share-secure-transfer.png" alt-text="En skärmbild av säker överföring aktiverad i de avancerade inställningarna för lagringskontot.":::

- **Stora filresurser:** Det här fältet aktiverar lagringskontot för filresurser som sträcker sig över upp till 100 TiB. Om du aktiverar den här funktionen begränsar du ditt lagringskonto till endast lokalt redundanta lagringsalternativ och zonredundant lagringsalternativ. När ett GPv2-lagringskonto har aktiverats för stora filresurser kan du inte inaktivera funktionen för stora filresurser. FileStorage-lagringskonton (lagringskonton för premiumfilresurser) har inte det här alternativet eftersom alla premiumfilresurser kan skalas upp till 100 TiB. 

    :::image type="content" source="media/storage-how-to-create-file-share/files-create-smb-share-large-file-shares.png" alt-text="En skärmbild av inställningen för den stora filresursen på lagringskontots avancerade blad.":::

De andra inställningarna som är tillgängliga på fliken Avancerat (hierarkisk namnrymd för Azure Data Lake Storage Gen2, standardblobnivå, NFSv3 för bloblagring osv.) gäller inte för Azure Files.

> [!Important]  
> Om du väljer blobåtkomstnivå påverkas inte filresursens nivå.

#### <a name="tags"></a>Taggar
Taggar är namn/värde-par som gör att du kan kategorisera resurser och visa konsoliderad fakturering genom att tillämpa samma tagg på flera resurser och resursgrupper. Dessa är valfria och kan tillämpas när lagringskontot har skapats.

#### <a name="review--create"></a>Granska + skapa
Det sista steget för att skapa lagringskontot är att **välja knappen** Skapa på fliken **Granska +** skapa. Den här knappen är inte tillgänglig om alla obligatoriska fält för ett lagringskonto inte har fyllts i.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
För att skapa ett lagringskonto med PowerShell använder vi `New-AzStorageAccount` cmdleten . Den här cmdleten har många alternativ. endast de obligatoriska alternativen visas. Mer information om avancerade alternativ finns i [ `New-AzStorageAccount` cmdlet-dokumentationen](/powershell/module/az.storage/new-azstorageaccount).

För att förenkla skapandet av lagringskontot och efterföljande filresurs lagrar vi flera parametrar i variabler. Du kan ersätta variabelinnehållet med de värden du vill, men observera att namnet på lagringskontot måste vara globalt unikt.

```powershell
$resourceGroupName = "myResourceGroup"
$storageAccountName = "mystorageacct$(Get-Random)"
$region = "westus2"
```

För att skapa ett lagringskonto som kan lagra Azure-standardfilresurser använder vi följande kommando. Parametern relaterar till den typ av redundans du vill ha. Om du vill ha ett `-SkuName` geo-redundant eller geo-zonredundant lagringskonto måste du också ta bort `-EnableLargeFileShare` parametern.

```powershell
$storAcct = New-AzStorageAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $storageAccountName `
    -SkuName Standard_LRS `
    -Location $region `
    -Kind StorageV2 `
    -EnableLargeFileShare
```

För att skapa ett lagringskonto som kan lagra Azure-premiumfilresurser använder vi följande kommando. Observera att `-SkuName` parametern har ändrats för att inkludera `Premium` både och den önskade redundansnivån lokalt redundant ( `LRS` ). Parametern är i stället för eftersom premiumfilresurser måste skapas i ett `-Kind` `FileStorage` `StorageV2` FileStorage-lagringskonto i stället för ett GPv2-lagringskonto.

```powershell
$storAcct = New-AzStorageAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $storageAccountName `
    -SkuName Premium_LRS `
    -Location $region `
    -Kind FileStorage 
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
För att skapa ett lagringskonto med hjälp av Azure CLI använder vi kommandot az storage account create. Det här kommandot har många alternativ. endast de obligatoriska alternativen visas. Mer information om avancerade alternativ finns i [ `az storage account create` kommandodokumentationen](/cli/azure/storage/account).

För att förenkla skapandet av lagringskontot och efterföljande filresurs lagrar vi flera parametrar i variabler. Du kan ersätta variabelinnehållet med de värden du vill, men observera att namnet på lagringskontot måste vara globalt unikt.

```azurecli
resourceGroupName="myResourceGroup"
storageAccountName="mystorageacct$RANDOM"
region="westus2"
```

För att skapa ett lagringskonto som kan lagra Azure-standardfilresurser använder vi följande kommando. Parametern relaterar till den typ av redundans du vill ha. Om du vill ha ett `--sku` geo-redundant eller geo-zonredundant lagringskonto måste du också ta bort `--enable-large-file-share` parametern.

```azurecli
az storage account create \
    --resource-group $resourceGroupName \
    --name $storageAccountName \
    --kind StorageV2 \
    --sku Standard_ZRS \
    --enable-large-file-share \
    --output none
```

För att skapa ett lagringskonto som kan lagra Azure-premiumfilresurser använder vi följande kommando. Observera att `--sku` parametern har ändrats för att inkludera `Premium` både och den önskade redundansnivån lokalt redundant ( `LRS` ). Parametern är i stället för att premiumfilresurser måste skapas i ett `--kind` `FileStorage` `StorageV2` FileStorage-lagringskonto i stället för ett GPv2-lagringskonto.

```azurecli
az storage account create \
    --resource-group $resourceGroupName \
    --name $storageAccountName \
    --kind FileStorage \
    --sku Premium_LRS \
    --output none
```

---

## <a name="create-a-file-share"></a>Skapa en filresurs
När du har skapat ditt lagringskonto är allt som återstår att skapa filresursen. Den här processen är i stort sett densamma oavsett om du använder en premiumfilresurs eller en standardfilresurs. Du bör tänka på följande skillnader.

Standardfilresurser kan distribueras till en standardnivå: transaktionsoptimerad (standard), varm eller kall. Det här är en nivå per filresurs som inte påverkas av **blobåtkomstnivån** för lagringskontot (den här egenskapen gäller endast Azure Blob Storage – den relaterar inte till Azure Files alls). Du kan ändra nivå för resursen när som helst efter att den har distribuerats. Premium-filresurser kan inte konverteras direkt till någon standardnivå.

> [!Important]  
> Du kan flytta filresurser mellan nivåer inom GPv2-lagringskontotyper (transaktionsoptimerade, heta och coola). Delningsflyttningar mellan nivåer medför transaktioner: om du flyttar från en lägre nivå till en mer kallare nivå debiteras den mer coola nivåns transaktionskostnad för varje fil i resursen, medan en flytt från en mer kallare nivå till en lägre nivå medför den coola nivåns transaktionsavgift för läsning för varje fil som resursen innehåller.

Kvotegenskapen innebär något något annorlunda mellan premium- och standardfilresurser: 

- För standardfilresurser är det en övre gräns för Azure-filresursen, utanför vilken slutanvändarna inte kan gå. Om en kvot inte anges kan standardfilresursen sträcka sig över upp till 100 TiB (eller 5 TiB om egenskapen för stora filresurser inte har angetts för ett lagringskonto).

- För premiumfilresurser innebär kvoten **den etablerade storleken**. Den etablerade storleken är det belopp som du debiteras för, oavsett faktisk användning. Mer information om hur du planerar för en premiumfilresurs finns i [Etablering av premiumfilresurser](understanding-billing.md#provisioned-model).

# <a name="portal"></a>[Portal](#tab/azure-portal)
Om du precis har skapat ditt lagringskonto kan du navigera till det från distributionsskärmen genom att välja **Gå till resurs**. När du är i lagringskontot väljer **du Filresurser** i innehållsförteckningen för lagringskontot.

I listan över filresurser bör du se alla filresurser som du tidigare har skapat i det här lagringskontot. en tom tabell om inga filresurser har skapats ännu. Välj **+ Filresurs för** att skapa en ny filresurs.

Det nya filresursbladet bör visas på skärmen. Fyll i fälten på bladet för den nya filresursen för att skapa en filresurs:

- **Namn:** namnet på den filresurs som ska skapas.
- **Kvot:** kvoten för filresursen för standardfilresurser; den etablerade storleken på filresursen för premiumfilresurser.
- **Nivåer:** den valda nivån för en filresurs. Det här fältet är endast tillgängligt i **ett GPv2-lagringskonto (generell användning).** Du kan välja transaktionsoptimerad, het eller cool. Resursens nivå kan ändras när som helst. Vi rekommenderar att du väljer den hetaste nivån som är möjlig under en migrering, minimerar transaktionskostnaderna och sedan växlar till en lägre nivå om du vill när migreringen är klar.

Välj **Skapa för** att slutföra skapandet av den nya resursen.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
Du kan skapa en Azure-filresurs med [`New-AzRmStorageShare`](/powershell/module/az.storage/New-AzRmStorageShare) cmdleten . Följande PowerShell-kommandon förutsätter att du har angett variablerna och enligt definitionen ovan i avsnittet skapa `$resourceGroupName` `$storageAccountName` ett lagringskonto med Azure PowerShell. 

I följande exempel visas hur du skapar en filresurs med en explicit nivå med hjälp av `-AccessTier` parametern . Detta kräver att du använder az.storage-modulen (förhandsversion) som anges i exemplet. Om en nivå inte anges, antingen eftersom du använder GA Az.Storage-modulen eller eftersom du inte tog med det här kommandot, är standardnivån för standardfilresurser transaktionsoptimerad.

> [!Important]  
> För premiumfilresurser `-QuotaGiB` refererar parametern till den etablerade storleken på filresursen. Den etablerade storleken på filresursen är det belopp som du debiteras för, oavsett användning. Standardfilresurser debiteras baserat på användning i stället för den etablerade storleken.

```powershell
# Assuming $resourceGroupName and $storageAccountName from earlier in this document have already
# been populated. The access tier parameter may be TransactionOptimized, Hot, or Cool for GPv2 
# storage accounts. Standard tiers are only available in standard storage accounts. 
$shareName = "myshare"

New-AzRmStorageShare `
        -ResourceGroupName $resourceGroupName `
        -StorageAccountName $storageAccountName `
        -Name $shareName `
        -AccessTier TransactionOptimized `
        -QuotaGiB 1024 | `
    Out-Null
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
Du kan skapa en Azure-filresurs med [`az storage share-rm create`](/cli/azure/storage/share-rm#az_storage_share_rm_create) kommandot . Följande Azure CLI-kommandon förutsätter att du har angett variablerna och enligt definitionen ovan i `$resourceGroupName` `$storageAccountName` avsnittet Skapa ett lagringskonto med Azure CLI.

> [!Important]  
> För premiumfilresurser `--quota` refererar parametern till den etablerade storleken på filresursen. Den etablerade storleken på filresursen är det belopp som du debiteras för, oavsett användning. Standardfilresurser debiteras baserat på användning i stället för den etablerade storleken.

```azurecli
shareName="myshare"

az storage share-rm create \
    --resource-group $resourceGroupName \
    --storage-account $storageAccountName \
    --name $shareName \
    --access-tier "TransactionOptimized" \
    --quota 1024 \
    --output none
```

---

> [!Note]  
> Namnet på filresursen får bara innehålla gemener. Fullständig information om namngivning av filresurser och filer finns i Namnge och [referera till resurser, kataloger, filer och metadata.](/rest/api/storageservices/Naming-and-Referencing-Shares--Directories--Files--and-Metadata)

### <a name="change-the-tier-of-an-azure-file-share"></a>Ändra nivå för en Azure-filresurs
Filresurser som distribueras i GPv2-lagringskontot (generell användning **v2)** kan vara på transaktionsoptimerade, heta eller coola nivåer. Du kan när som helst ändra nivå för Azure-filresursen, beroende på transaktionskostnader enligt beskrivningen ovan.

# <a name="portal"></a>[Portal](#tab/azure-portal)
På huvudlagringskontots  sida väljer du Filresurser och väljer den panel som är märkt **Filresurser** (du kan också navigera till Filresurser **via** innehållsförteckningen för lagringskontot).

:::image type="content" source="media/storage-files-quick-create-use-windows/click-files.png" alt-text="Skärmbild av bladet lagringskonto, valda filresurser.":::

I tabelllistan över filresurser väljer du den filresurs som du vill ändra nivån för. På översiktssidan för filresursen väljer **du Ändra nivå** på menyn.

![En skärmbild av översiktssidan för filresursen med knappen ändra nivå markerad](media/storage-how-to-create-file-share/change-tier-0.png)

I dialogrutan som visas väljer du önskad nivå: transaktionsoptimerad, het eller kall.

![En skärmbild av dialogrutan Ändra nivå](media/storage-how-to-create-file-share/change-tier-1.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
Följande PowerShell-cmdlet förutsätter att du har angett variablerna , , enligt beskrivningen `$resourceGroupName` i tidigare avsnitt i det här `$storageAccountName` `$shareName` dokumentet.

```PowerShell
Update-AzRmStorageShare `
    -ResourceGroupName $resourceGroupName `
    -StorageAccountName $storageAccountName `
    -Name $shareName `
    -AccessTier Cool
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
Följande Azure CLI-kommando förutsätter att du har angett variablerna , och enligt beskrivningen `$resourceGroupName` `$storageAccountName` i de tidigare `$shareName` avsnitten i det här dokumentet.

```azurecli
az storage share-rm update \
    --resource-group $resourceGroupName \
    --storage-account $storageAccountName \
    --name $shareName \
    --access-tier "Cool"
```

---

## <a name="next-steps"></a>Nästa steg
- [Planera för en distribution av Azure Files](storage-files-planning.md) eller Planera för en distribution av [Azure File Sync](../file-sync/file-sync-planning.md). 
- [Nätverksöversikt](storage-files-networking-overview.md).
- Anslut och montera en filresurs på [Windows,](storage-how-to-use-files-windows.md) [macOS](storage-how-to-use-files-mac.md)och [Linux.](storage-how-to-use-files-linux.md)