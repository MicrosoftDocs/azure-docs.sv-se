---
title: Skapa en Azure-filresurs
titleSuffix: Azure Files
description: Hur du skapar en Azure-filresurs med hjälp av Azure Portal, PowerShell eller Azure CLI.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 1/20/2021
ms.author: rogarana
ms.subservice: files
ms.custom: devx-track-azurecli, references_regions
ms.openlocfilehash: 24bee926d84c7a5be3f19c39d39285c2cd486824
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102211030"
---
# <a name="create-an-azure-file-share"></a>Skapa en Azure-filresurs
Om du vill skapa en Azure-filresurs måste du svara på tre frågor om hur du ska använda den:

- **Vilka prestanda krav gäller för din Azure-filresurs?**  
    Azure Files erbjuder standard fil resurser som finns på hård diskbaserade (HDD-baserade) maskin vara och Premium-filresurser som finns på diskbaserade (SSD-) maskin vara med solid-tillstånd.

- **Vilka är dina redundans krav för din Azure-filresurs?**  
    Standard fil resurser erbjuder lokalt redundant (LRS), zon redundant (ZRS), Geo-redundant (GRS) eller geo-Zone-redundant lagring (GZRS), men den stora fil resurs funktionen stöds bara på lokalt redundanta och zoner-redundanta fil resurser. Premium File-resurser stöder inte någon form av GEO-redundans.

    Premium-filresurser är tillgängliga med lokalt redundans och zon redundans i en delmängd regioner. För att ta reda på om Premium-filresurser är tillgängliga i din region, se sidan [produkter som är tillgängliga per region](https://azure.microsoft.com/global-infrastructure/services/?products=storage) för Azure. Information om regioner som stöder ZRS finns [Azure Storage redundans](../common/storage-redundancy.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

- **Vilken storlek fil resurs behöver du?**  
    I lokala och zon redundanta lagrings konton kan Azure-filresurser sträcka sig upp till 100 TiB, men i lagrings konton med Geo-och geo Zone kan Azure-filresurser bara omfatta upp till 5 TiB. 

Mer information om dessa tre alternativ finns i [Planera för en Azure Files distribution](storage-files-planning.md).

## <a name="prerequisites"></a>Förutsättningar
- Den här artikeln förutsätter att du redan har skapat en Azure-prenumeration. Om du inte redan har en prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.
- Om du tänker använda Azure PowerShell [installerar du den senaste versionen](/powershell/azure/install-az-ps).
- Om du tänker använda Azure CLI [installerar du den senaste versionen](/cli/azure/install-azure-cli).

## <a name="create-a-storage-account"></a>Skapa ett lagringskonto
Azure-filresurser distribueras till *lagrings konton* som är toppnivå objekt som representerar en delad lagringspool. Den här lagringspoolen kan användas för att distribuera flera fil resurser. 

Azure har stöd för flera typer av lagrings konton för olika lagrings scenarier. kunder kan ha, men det finns två huvud typer av lagrings konton för Azure Files. Vilken typ av lagrings konto du behöver skapa beror på om du vill skapa en standard fil resurs eller en Premium fil resurs: 

- **GPv2-lagrings konton (General Purpose version 2)**: med GPv2 Storage-konton kan du Distribuera Azure-filresurser på standard-/hård diskbaserade (HDD-baserade) maskin vara. Förutom att lagra Azure-filresurser kan GPv2 lagrings konton lagra andra lagrings resurser, till exempel BLOB-behållare, köer och tabeller. Fil resurser kan distribueras i transaktionens optimerade (standard), frekventa eller låg frekventa nivåer.

- **FileStorage lagrings konton**: med FileStorage Storage-konton kan du Distribuera Azure-filresurser i Premium/Solid-State-baserad (SSD) maskin vara. FileStorage-konton kan bara användas för att lagra Azure-filresurser. inga andra lagrings resurser (BLOB-behållare, köer, tabeller osv.) kan distribueras i ett FileStorage-konto.

# <a name="portal"></a>[Portal](#tab/azure-portal)
Om du vill skapa ett lagrings konto via Azure Portal väljer du **+ skapa en resurs** från instrument panelen. I fönstret Azure Marketplace search kan du söka efter **lagrings konto** och välja det resulterande Sök resultatet. Detta kommer att leda till en översikts sida för lagrings konton. Klicka på **skapa** för att fortsätta med guiden skapa lagrings konto.

![En skärm bild av alternativet för snabb skapande av lagrings konto i en webbläsare](media/storage-how-to-create-file-share/create-storage-account-0.png)

#### <a name="basics"></a>Grundläggande inställningar
Det första avsnittet att slutföra för att skapa ett lagrings konto är märkta med **grunderna**. Innehåller alla obligatoriska fält för att skapa ett lagrings konto. Om du vill skapa ett GPv2 lagrings konto ser du till att alternativet **prestanda** är inställt på *standard* och list rutan **Kontotyp** är markerad till *StorageV2 (generell användning v2)*.

![En skärm bild av alternativ knappen prestanda med standard vald och konto typ med StorageV2 valt](media/storage-how-to-create-file-share/create-storage-account-1.png)

Om du vill skapa ett FileStorage lagrings konto ser du till att alternativ knappen för **prestanda** är inställd på *Premium* och att List rutan **Kontotyp** är markerad för *FileStorage*.

![En skärm bild av prestanda alternativ knappen med Premium valt och konto typ med FileStorage valt](media/storage-how-to-create-file-share/create-storage-account-2.png)

De andra grunderna fälten är oberoende av valet av lagrings konto:
- **Lagrings konto namn**: namnet på lagrings konto resursen som ska skapas. Det här namnet måste vara globalt unikt, men annars kan alla namn du vill ha. Namnet på lagrings kontot kommer att användas som server namn när du monterar en Azure-filresurs via SMB.
- **Plats**: regionen för det lagrings konto som ska distribueras till. Detta kan vara den region som är kopplad till resurs gruppen eller någon annan tillgänglig region.
- **Replikering**: även om det här är en etikettad replikering innebär det här fältet faktiskt **redundans**. Detta är önskad redundans nivå: lokalt redundans (LRS), zon redundans (ZRS), GEO-redundans (GRS) och geo-Zone-redundans (GZRS). Den här List rutan innehåller också Read-Access GEO-redundans (RA-GRS) och-Access geo-Zone redundans (ra-GZRS), som inte gäller för Azure-filresurser. alla fil resurser som har skapats i ett lagrings konto med dessa är markerade är antingen geo-redundanta eller geo-zon-redundanta. 

#### <a name="networking"></a>Nätverk
I avsnittet nätverk kan du konfigurera nätverks alternativ. De här inställningarna är valfria för att skapa lagrings kontot och kan konfigureras senare om det behövs. Mer information om dessa alternativ finns i [Azure Files nätverks överväganden](storage-files-networking-overview.md).

#### <a name="data-protection"></a>Dataskydd
I avsnittet data skydd kan du konfigurera principen för mjuk borttagning för Azure-filresurser i ditt lagrings konto. Andra inställningar som rör mjuk borttagning för blobbar, behållare, tidpunkts återställning för behållare, versions hantering och ändrings flöden gäller endast för Azure Blob Storage.

#### <a name="advanced"></a>Avancerat
Avsnittet Avancerat innehåller flera viktiga inställningar för Azure-fil resurser:

- **Säker överföring krävs**: det här fältet visar om lagrings kontot kräver kryptering i överföring för kommunikation till lagrings kontot. Om du behöver stöd för SMB 2,1 måste du inaktivera detta.
- **Stora fil resurser**: det här fältet aktiverar lagrings kontot för fil resurser som sträcker sig upp till 100 TIB. Om du aktiverar den här funktionen begränsas ditt lagrings konto till endast lokalt redundant och zon alternativ för redundant lagring. När ett GPv2 lagrings konto har Aktiver ATS för stora fil resurser kan du inte inaktivera funktionen för stor fil resurs. FileStorage lagrings konton (lagrings konton för Premium-filresurser) har inte det här alternativet eftersom alla Premium fil resurser kan skala upp till 100 TiB. 

![En skärm bild av viktiga avancerade inställningar som gäller för Azure Files](media/storage-how-to-create-file-share/create-storage-account-3.png)

De andra inställningarna som är tillgängliga på fliken Avancerat (hierarkiskt namn område för Azure Data Lake Storage gen 2, standard BLOB-nivå, NFSv3 för Blob Storage osv.) gäller inte för Azure Files.

> [!Important]  
> Att välja BLOB-åtkomstnivå påverkar inte fil resursens nivå.

#### <a name="tags"></a>Taggar
Taggar är namn/värde-par som låter dig kategorisera resurser och Visa konsol IDE rad fakturering genom att tillämpa samma tagg på flera resurser och resurs grupper. Dessa är valfria och kan användas när lagrings kontot har skapats.

#### <a name="review--create"></a>Granska + skapa
Det sista steget för att skapa lagrings kontot är att välja knappen **skapa** på fliken **Granska + skapa** . Den här knappen är inte tillgänglig om alla obligatoriska fält för ett lagrings konto inte har fyllts i.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
Vi använder cmdleten för att skapa ett lagrings konto med hjälp av PowerShell `New-AzStorageAccount` . Denna cmdlet har många alternativ; endast de alternativ som krävs visas. Mer information om avancerade alternativ finns i cmdlet- [ `New-AzStorageAccount` dokumentationen](/powershell/module/az.storage/new-azstorageaccount).

För att förenkla skapandet av lagrings kontot och efterföljande fil resurser kommer vi att lagra flera parametrar i variabler. Du kan ersätta variabel innehållet med de värden du önskar, men Observera att lagrings konto namnet måste vara globalt unikt.

```powershell
$resourceGroupName = "myResourceGroup"
$storageAccountName = "mystorageacct$(Get-Random)"
$region = "westus2"
```

Vi använder följande kommando för att skapa ett lagrings konto som kan lagra standard Azure-filresurser. `-SkuName`Parametern relaterar till den typ av redundans som önskas. om du vill ha ett Geo-redundant eller geo-zon-redundant lagrings konto måste du också ta bort `-EnableLargeFileShare` parametern.

```powershell
$storAcct = New-AzStorageAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $storageAccountName `
    -SkuName Standard_LRS `
    -Location $region `
    -Kind StorageV2 `
    -EnableLargeFileShare
```

För att kunna skapa ett lagrings konto som kan lagra Premium Azure-filresurser, kommer vi att använda följande kommando. Observera att `-SkuName` parametern har ändrats till att inkludera både `Premium` och önskad redundans nivå för lokalt redundant ( `LRS` ). - `-Kind` Parametern är `FileStorage` istället för `StorageV2` att Premium-filresurser måste skapas i ett FileStorage lagrings konto i stället för ett GPv2-lagrings konto.

```powershell
$storAcct = New-AzStorageAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $storageAccountName `
    -SkuName Premium_LRS `
    -Location $region `
    -Kind FileStorage 
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
Om du vill skapa ett lagrings konto med hjälp av Azure CLI använder du kommandot AZ Storage Account Create. Det här kommandot har många alternativ; endast de alternativ som krävs visas. Mer information om de avancerade alternativen finns i [ `az storage account create` kommando dokumentationen](/cli/azure/storage/account).

För att förenkla skapandet av lagrings kontot och efterföljande fil resurser kommer vi att lagra flera parametrar i variabler. Du kan ersätta variabel innehållet med de värden du önskar, men Observera att lagrings konto namnet måste vara globalt unikt.

```azurecli
resourceGroupName="myResourceGroup"
storageAccountName="mystorageacct$RANDOM"
region="westus2"
```

Vi använder följande kommando för att skapa ett lagrings konto som kan lagra standard Azure-filresurser. `--sku`Parametern relaterar till den typ av redundans som önskas. om du vill ha ett Geo-redundant eller geo-zon-redundant lagrings konto måste du också ta bort `--enable-large-file-share` parametern.

```azurecli
az storage account create \
    --resource-group $resourceGroupName \
    --name $storageAccountName \
    --kind StorageV2 \
    --sku Standard_ZRS \
    --enable-large-file-share \
    --output none
```

För att kunna skapa ett lagrings konto som kan lagra Premium Azure-filresurser, kommer vi att använda följande kommando. Observera att `--sku` parametern har ändrats till att inkludera både `Premium` och önskad redundans nivå för lokalt redundant ( `LRS` ). - `--kind` Parametern är `FileStorage` istället för `StorageV2` att Premium-filresurser måste skapas i ett FileStorage lagrings konto i stället för ett GPv2-lagrings konto.

```azurecli
az storage account create \
    --resource-group $resourceGroupName \
    --name $storageAccountName \
    --kind FileStorage \
    --sku Premium_LRS \
    --output none
```

---

## <a name="create-file-share"></a>Skapa en filresurs
När du har skapat ditt lagrings konto är allt det som är kvar att skapa din fil resurs. Den här processen är i huvudsak detsamma oavsett om du använder en Premium-filresurs eller en standard fil resurs. Du bör tänka på följande skillnader.

Standard fil resurser kan distribueras till en av standard-nivåerna: transaktions optimering (standard), frekvent eller låg frekvent. Det här är en fildelnings nivå per fil som inte påverkas av lagrings kontots **BLOB-åtkomst** (den här egenskapen relaterar bara till Azure Blob Storage – den är inte relaterad till Azure Files alls). Du kan ändra delnings nivån när som helst efter att den har distribuerats. Premium-filresurser kan inte konverteras direkt till standard nivån.

> [!Important]  
> Du kan flytta fil resurser mellan nivåer inom GPv2 lagrings konto typer (transaktion optimerad, frekvent och låg frekvent). Delnings åtgärder mellan nivåer medför transaktioner: om du flyttar från en Hotter-nivå till en låg frekvent nivå kommer den avbilds nivå som är låg för varje fil i resursen, medan en flytt från en låg frekvent nivå till en Hotter-nivå kommer att medföra den låg frekventa transaktions avgiften för varje fil resursen.

**Kvot** egenskapen innebär något annorlunda mellan Premium-och standard fil resurser:

- För standard fil resurser är det en övre gränser för Azure-filresursen, utöver vilka slutanvändare inte kan gå. Om ingen kvot anges kan standard fil resursen omfatta upp till 100 TiB (eller 5 TiB om den stora fil resurs egenskapen inte har angetts för ett lagrings konto).

- För Premium-filresurser innebär kvoten en **etablerad storlek**. Den etablerade storleken är den mängd som du debiteras för, oavsett faktisk användning. Mer information om hur du planerar för en Premium-filresurs finns i avsnittet om att [allokera Premium-filresurser](understanding-billing.md#provisioned-model).

# <a name="portal"></a>[Portal](#tab/azure-portal)
Om du precis har skapat ditt lagrings konto kan du navigera till det från distributions skärmen genom att välja **gå till resurs**. När du är i lagrings kontot väljer du panelen med namnet **fil resurser** (du kan också navigera till **fil resurser** via innehålls förteckningen för lagrings kontot).

![En skärm bild av panelen fil resurser](media/storage-how-to-create-file-share/create-file-share-1.png)

I listan fil resurs bör du se eventuella fil resurser som du tidigare har skapat i det här lagrings kontot. en tom tabell om inga fil resurser har skapats ännu. Välj **+ fil resurs** för att skapa en ny fil resurs.

Bladet ny fil resurs bör visas på skärmen. Fyll i fälten på bladet ny fil resurs för att skapa en fil resurs:

- **Namn**: namnet på den fil resurs som ska skapas.
- **Kvot**: kvoten för fil resursen för standard fil resurser; den allokerade storleken på fil resursen för Premium-filresurser.
- **Nivåer**: den valda nivån för en fil resurs. Det här fältet är endast tillgängligt i ett **GPv2-lagrings konto (General Purpose)**. Du kan välja transaktion optimerad, frekvent eller låg frekvent. Resurs nivån kan ändras när som helst. Vi rekommenderar att du plockar den hetaste nivån under en migrering, för att minimera transaktions kostnaderna och sedan växlar till en lägre nivå om du vill när migreringen är klar.

Välj **skapa** för att slutföra skapandet av den nya resursen.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
Du kan skapa en Azure-filresurs med [`New-AzRmStorageShare`](/powershell/module/az.storage/New-AzRmStorageShare) cmdleten. Följande PowerShell-kommandon förutsätter att du har angett variablerna `$resourceGroupName` och `$storageAccountName` enligt definitionen ovan i avsnittet Skapa ett lagrings konto med Azure PowerShell. 

I följande exempel visas hur du skapar en fil resurs med en explicit nivå med hjälp av `-AccessTier` parametern. Detta kräver att du använder modulen för förhands granskning AZ. Storage som anges i exemplet. Om en nivå inte anges, antingen på grund av att du använder GA AZ. Storage eller eftersom du inte har inkluderat det här kommandot, är standard nivån för standard fil resurser optimerad.

> [!Important]  
> För Premium-filresurser `-QuotaGiB` refererar parametern till den allokerade storleken på fil resursen. Den allokerade storleken på fil resursen är den mängd som du debiteras för, oavsett användning. Standard fil resurser faktureras baserat på användning i stället för en etablerad storlek.

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
Du kan skapa en Azure-filresurs med [`az storage share-rm create`](/cli/azure/storage/share-rm#az_storage_share_rm_create) kommandot. Följande Azure CLI-kommandon förutsätter att du har angett variablerna `$resourceGroupName` och `$storageAccountName` enligt definitionen ovan i avsnittet Skapa ett lagrings konto med Azure CLI.

> [!Important]  
> För Premium-filresurser `--quota` refererar parametern till den allokerade storleken på fil resursen. Den allokerade storleken på fil resursen är den mängd som du debiteras för, oavsett användning. Standard fil resurser faktureras baserat på användning i stället för en etablerad storlek.

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
> Namnet på filresursen får bara innehålla gemener. Fullständig information om namngivning av fil resurser och filer finns i [namnge och referera till resurser, kataloger, filer och metadata](/rest/api/storageservices/Naming-and-Referencing-Shares--Directories--Files--and-Metadata).

### <a name="changing-the-tier-of-an-azure-file-share"></a>Ändra nivån för en Azure-filresurs
Fil resurser som har distribuerats i **GPv2-lagrings kontot (General Purpose v2)** kan finnas i transaktionen optimerade, frekventa eller låg frekventa nivåer. Du kan ändra nivån på Azure-filresursen när som helst, beroende på transaktionskostnader enligt beskrivningen ovan.

# <a name="portal"></a>[Portal](#tab/azure-portal)
På sidan huvud lagrings konto väljer du **fil resurser**  Välj panelen **fil resurser** (du kan också navigera till **fil resurser** via innehålls förteckningen för lagrings kontot).

![En skärm bild av panelen fil resurser](media/storage-how-to-create-file-share/create-file-share-1.png)

I tabell listan över fil resurser väljer du den fil resurs som du vill ändra nivån för. På sidan Översikt över fil resurs väljer du **ändra nivå** på menyn.

![En skärm bild av översikts sidan för fil resurser med knappen ändra nivå markerad](media/storage-how-to-create-file-share/change-tier-0.png)

I dialog rutan som visas väljer du önskad nivå: transaktion optimerad, frekvent eller låg frekvent.

![En skärm bild av dialog rutan ändra nivå](media/storage-how-to-create-file-share/change-tier-1.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
Följande PowerShell-cmdlet förutsätter att du har ställt `$resourceGroupName` in `$storageAccountName` , `$shareName` variabler enligt beskrivningen i de tidigare avsnitten i det här dokumentet.

```PowerShell
Update-AzRmStorageShare `
    -ResourceGroupName $resourceGroupName `
    -StorageAccountName $storageAccountName `
    -Name $shareName `
    -AccessTier Cool
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
Följande Azure CLI-kommando förutsätter att du har ställt in `$resourceGroupName` , `$storageAccountName` , och `$shareName` variabler enligt beskrivningen i de tidigare avsnitten i det här dokumentet.

```azurecli
az storage share-rm update \
    --resource-group $resourceGroupName \
    --storage-account $storageAccountName \
    --name $shareName \
    --access-tier "Cool"
```

---

## <a name="next-steps"></a>Nästa steg
- [Planera för distribution av Azure Files](storage-files-planning.md) eller [Planera för en distribution av Azure File Sync](storage-sync-files-planning.md). 
- [Översikt över nätverk](storage-files-networking-overview.md).
- Anslut och montera en fil resurs på [Windows](storage-how-to-use-files-windows.md), [MacOS](storage-how-to-use-files-mac.md)och [Linux](storage-how-to-use-files-linux.md).