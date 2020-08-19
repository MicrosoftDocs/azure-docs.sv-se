---
title: Skapa en Azure-filresurs
titleSuffix: Azure Files
description: Hur du skapar en Azure-filresurs med hjälp av Azure Portal, PowerShell eller Azure CLI.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 2/22/2020
ms.author: rogarana
ms.subservice: files
ms.custom: devx-track-azurecli, references_regions
ms.openlocfilehash: aaba608ba80a751c40cd300dee80f673897c22a8
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88525657"
---
# <a name="create-an-azure-file-share"></a>Skapa en Azure-filresurs
Om du vill skapa en Azure-filresurs måste du svara på tre frågor om hur du ska använda den:

- **Vilka prestanda krav gäller för din Azure-filresurs?**  
    Azure Files erbjuder standard fil resurser som finns på hård diskbaserade (HDD-baserade) maskin vara och Premium-filresurser som finns på diskbaserade (SSD-) maskin vara med solid-tillstånd.

- **Vilken storlek fil resurs behöver du?**  
    Standard fil resurser kan omfatta upp till 100 TiB, men den här funktionen är inte aktive rad som standard. Om du behöver en fil resurs som är större än 5 TiB måste du aktivera funktionen stor fil resurs för ditt lagrings konto. Premium-filresurser kan sträcka sig upp till 100 TiB utan någon särskild inställning, men Premium-filresurser etablerades, i stället för att betala per användning som standard fil resurser. Det innebär att etablering av en fil resurs som är mycket större än det du behöver ökar den totala lagrings kostnaden.

- **Vilka är dina redundans krav för din Azure-filresurs?**  
    Standard fil resurser erbjuder lokalt redundant (LRS), zon redundant (ZRS), Geo-redundant (GRS) eller geo-Zone-redundant lagring (GZRS), men den stora fil resurs funktionen stöds bara på lokalt redundanta och zoner-redundanta fil resurser. Premium File-resurser stöder inte någon form av GEO-redundans.

    Premium-filresurser är tillgängliga med lokalt redundans i de flesta regioner som erbjuder lagrings konton och zon redundans i en mindre delmängd av regioner. För att ta reda på om Premium-filresurser är tillgängliga i din region, se sidan [produkter som är tillgängliga per region](https://azure.microsoft.com/global-infrastructure/services/?products=storage) för Azure. Information om regioner som stöder ZRS finns [Azure Storage redundans](../common/storage-redundancy.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

Mer information om dessa tre alternativ finns i [Planera för en Azure Files distribution](storage-files-planning.md).

## <a name="prerequisites"></a>Förutsättningar
- Den här artikeln förutsätter att du redan har skapat en Azure-prenumeration. Om du inte redan har en prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.
- Om du tänker använda Azure PowerShell [installerar du den senaste versionen](https://docs.microsoft.com/powershell/azure/install-az-ps).
- Om du tänker använda Azure CLI [installerar du den senaste versionen](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

## <a name="create-a-storage-account"></a>Skapa ett lagringskonto
Azure-filresurser distribueras till *lagrings konton*som är toppnivå objekt som representerar en delad lagringspool. Den här lagringspoolen kan användas för att distribuera flera fil resurser. 

Azure har stöd för flera typer av lagrings konton för olika lagrings scenarier. kunder kan ha, men det finns två huvud typer av lagrings konton för Azure Files. Vilken typ av lagrings konto du behöver skapa beror på om du vill skapa en standard fil resurs eller en Premium fil resurs: 

- **GPv2-lagrings konton (General Purpose version 2)**: med GPv2 Storage-konton kan du Distribuera Azure-filresurser på standard-/hård diskbaserade (HDD-baserade) maskin vara. Förutom att lagra Azure-filresurser kan GPv2 lagrings konton lagra andra lagrings resurser, till exempel BLOB-behållare, köer och tabeller. 

- **FileStorage lagrings konton**: med FileStorage Storage-konton kan du Distribuera Azure-filresurser i Premium/Solid-State-baserad (SSD) maskin vara. FileStorage-konton kan bara användas för att lagra Azure-filresurser. inga andra lagrings resurser (BLOB-behållare, köer, tabeller osv.) kan distribueras i ett FileStorage-konto.

# <a name="portal"></a>[Portal](#tab/azure-portal)
Om du vill skapa ett lagrings konto via Azure Portal väljer du **+ skapa en resurs** från instrument panelen. I fönstret Azure Marketplace search kan du söka efter **lagrings konto** och välja det resulterande Sök resultatet. Detta kommer att leda till en översikts sida för lagrings konton. Klicka på **skapa** för att fortsätta med guiden skapa lagrings konto.

![En skärm bild av alternativet för snabb skapande av lagrings konto i en webbläsare](media/storage-how-to-create-file-share/create-storage-account-0.png)

#### <a name="the-basics-section"></a>Avsnittet grundläggande
Det första avsnittet att slutföra för att skapa ett lagrings konto är märkta med **grunderna**. Innehåller alla obligatoriska fält för att skapa ett lagrings konto. Om du vill skapa ett GPv2 lagrings konto ser du till att alternativet **prestanda** är inställt på *standard* och list rutan **Kontotyp** är markerad till *StorageV2 (generell användning v2)*.

![En skärm bild av alternativ knappen prestanda med standard vald och konto typ med StorageV2 valt](media/storage-how-to-create-file-share/create-storage-account-1.png)

Om du vill skapa ett FileStorage lagrings konto ser du till att alternativ knappen för **prestanda** är inställd på *Premium* och att List rutan **Kontotyp** är markerad för *FileStorage*.

![En skärm bild av prestanda alternativ knappen med Premium valt och konto typ med FileStorage valt](media/storage-how-to-create-file-share/create-storage-account-2.png)

De andra grunderna fälten är oberoende av valet av lagrings konto:
- **Prenumeration**: prenumerationen på det lagrings konto som ska distribueras till. 
- **Resurs grupp**: resurs gruppen för det lagrings konto som ska distribueras till. Du kan antingen skapa en ny resurs grupp eller använda en befintlig resurs grupp. En resursgrupp är en logisk container där Azure-resurserna grupperas. När du skapar ett lagringskonto kan du antingen skapa en ny resursgrupp eller använda en befintlig resursgrupp.
- **Lagrings konto namn**: namnet på lagrings konto resursen som ska skapas. Det här namnet måste vara globalt unikt, men annars kan alla namn du vill ha. Namnet på lagrings kontot kommer att användas som server namn när du monterar en Azure-filresurs via SMB.
- **Plats**: regionen för det lagrings konto som ska distribueras till. Detta kan vara den region som är kopplad till resurs gruppen eller någon annan tillgänglig region.
- **Replikering**: även om det här är en etikettad replikering innebär det här fältet faktiskt **redundans**. Detta är önskad redundans nivå: lokalt redundans (LRS), zon redundans (ZRS), GEO-redundans (GRS) och geo-Zone-redundans. Den här List rutan innehåller också Read-Access GEO-redundans (RA-GRS) och-Access geo-Zone redundans (ra-GZRS), som inte gäller för Azure-filresurser. alla fil resurser som har skapats i ett lagrings konto med dessa är markerade är antingen geo-redundanta eller geo-zon-redundanta. Beroende på din region eller vald lagrings konto typ kan vissa alternativ för redundans inte tillåtas.
- **Åtkomst nivå**: det här fältet gäller inte för Azure Files, så du kan välja någon av alternativ knapparna.

#### <a name="the-networking-blade"></a>Bladet nätverk
I avsnittet nätverk kan du konfigurera nätverks alternativ. De här inställningarna är valfria för att skapa lagrings kontot och kan konfigureras senare om det behövs. Mer information om dessa alternativ finns i [Azure Files nätverks överväganden](storage-files-networking-overview.md).

#### <a name="the-advanced-blade"></a>Det avancerade bladet
Avsnittet Avancerat innehåller flera viktiga inställningar för Azure-fil resurser:

- **Säker överföring krävs**: det här fältet visar om lagrings kontot kräver kryptering i överföring för kommunikation till lagrings kontot. Vi rekommenderar att detta är kvar, men om du behöver stöd för SMB 2,1 måste du inaktivera detta. Vi rekommenderar att du inaktiverar kryptering som begränsar ditt lagrings konto till ett virtuellt nätverk med tjänst slut punkter och/eller privata slut punkter.
- **Stora fil resurser**: det här fältet aktiverar lagrings kontot för fil resurser som sträcker sig upp till 100 TIB. Om du aktiverar den här funktionen begränsas ditt lagrings konto till endast lokalt redundant och zon alternativ för redundant lagring. När ett GPv2 lagrings konto har Aktiver ATS för stora fil resurser kan du inte inaktivera funktionen för stor fil resurs. FileStorage lagrings konton (lagrings konton för Premium-filresurser) har inte det här alternativet eftersom alla Premium fil resurser kan skala upp till 100 TiB. 

![En skärm bild av viktiga avancerade inställningar som gäller för Azure Files](media/storage-how-to-create-file-share/create-storage-account-3.png)

De andra inställningarna som är tillgängliga på fliken Avancerat (BLOB Soft-Delete, hierarkisk namnrymd för Azure Data Lake lagring gen 2 och NFSv3 för Blob Storage) gäller inte för Azure Files.

#### <a name="tags"></a>Taggar
Taggar är namn/värde-par som låter dig kategorisera resurser och Visa konsol IDE rad fakturering genom att tillämpa samma tagg på flera resurser och resurs grupper. Dessa är valfria och kan användas när lagrings kontot har skapats.

#### <a name="review--create"></a>Granska + skapa
Det sista steget för att skapa lagrings kontot är att välja knappen **skapa** på fliken **Granska + skapa** . Den här knappen är inte tillgänglig om alla obligatoriska fält för ett lagrings konto inte har fyllts i.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
Vi använder cmdleten för att skapa ett lagrings konto med hjälp av PowerShell `New-AzStorageAccount` . Denna cmdlet har många alternativ; endast de alternativ som krävs visas. Mer information om avancerade alternativ finns i cmdlet- [ `New-AzStorageAccount` dokumentationen](/powershell/module/az.storage/new-azstorageaccount).

För att förenkla skapandet av lagrings kontot och efterföljande fil resurser kommer vi att lagra flera parametrar i variabler. Du kan ersätta variabel innehållet med de värden du önskar, men Observera att lagrings konto namnet måste vara globalt unikt.

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$storageAccountName = "mystorageacct$(Get-Random)"
$region = "westus2"
```

Vi använder följande kommando för att skapa ett lagrings konto som kan lagra standard Azure-filresurser. `-SkuName`Parametern relaterar till den typ av redundans som önskas. om du vill ha ett Geo-redundant eller geo-zon-redundant lagrings konto måste du också ta bort `-EnableLargeFileShare` parametern.

```azurepowershell-interactive
$storAcct = New-AzStorageAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $storageAccountName `
    -SkuName Standard_LRS `
    -Location $region `
    -Kind StorageV2 `
    -EnableLargeFileShare
```

För att kunna skapa ett lagrings konto som kan lagra Premium Azure-filresurser, kommer vi att använda följande kommando. Observera att `-SkuName` parametern har ändrats till att inkludera både `Premium` och önskad redundans nivå för lokalt redundant ( `LRS` ). - `-Kind` Parametern är `FileStorage` istället för `StorageV2` att Premium-filresurser måste skapas i ett FileStorage lagrings konto i stället för ett GPv2-lagrings konto.

```azurepowershell-interactive
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

```azurecli-interactive
resourceGroupName="myResourceGroup"
storageAccountName="mystorageacct$RANDOM"
region="westus2"
```

Vi använder följande kommando för att skapa ett lagrings konto som kan lagra standard Azure-filresurser. `--sku`Parametern relaterar till den typ av redundans som önskas. om du vill ha ett Geo-redundant eller geo-zon-redundant lagrings konto måste du också ta bort `--enable-large-file-share` parametern.

```azurecli-interactive
az storage account create \
    --resource-group $resourceGroupName \
    --name $storageAccountName \
    --kind StorageV2 \
    --sku Standard_ZRS \
    --enable-large-file-share \
    --output none
```

För att kunna skapa ett lagrings konto som kan lagra Premium Azure-filresurser, kommer vi att använda följande kommando. Observera att `--sku` parametern har ändrats till att inkludera både `Premium` och önskad redundans nivå för lokalt redundant ( `LRS` ). - `--kind` Parametern är `FileStorage` istället för `StorageV2` att Premium-filresurser måste skapas i ett FileStorage lagrings konto i stället för ett GPv2-lagrings konto.

```azurecli-interactive
az storage account create \
    --resource-group $resourceGroupName \
    --name $storageAccountName \
    --kind FileStorage \
    --sku Premium_LRS \
    --output none
```

---

## <a name="create-file-share"></a>Skapa en filresurs
När du har skapat ditt lagrings konto är allt det som är kvar att skapa din fil resurs. Den här processen är i huvudsak detsamma oavsett om du använder en Premium-filresurs eller en standard fil resurs. Den främsta skillnaden är **kvoten** och vad den representerar.

För standard fil resurser är det en övre gränser för Azure-filresursen, utöver vilka slutanvändare inte kan gå. Det primära syftet med kvoten för en standard fil resurs är budget: "Jag vill inte att den här fil resursen ska växa bortom den här punkten". Om ingen kvot anges kan standard fil resursen omfatta upp till 100 TiB (eller 5 TiB om den stora fil resurs egenskapen inte har angetts för ett lagrings konto).

För Premium-filresurser är kvoten överbelastad till en medels **Tor storlek**. Den etablerade storleken är den mängd som du debiteras för, oavsett faktisk användning. När du etablerar en Premium-filresurs vill du överväga två faktorer: 1) den framtida tillväxten av resursen från ett utrymmes användnings perspektiv och 2) IOPS som krävs för din arbets belastning. Varje etablerad GiB ger dig ytterligare reserverade och burst-IOPS. Mer information om hur du planerar för en Premium-filresurs finns i avsnittet om att [allokera Premium-filresurser](storage-files-planning.md#understanding-provisioning-for-premium-file-shares).

# <a name="portal"></a>[Portal](#tab/azure-portal)
Om du precis har skapat ditt lagrings konto kan du navigera till det från distributions skärmen genom att välja **gå till resurs**. Om du tidigare har skapat lagrings kontot kan du navigera till det via den resurs grupp som innehåller det. När du är i lagrings kontot väljer du panelen med namnet **fil resurser** (du kan också navigera till **fil resurser** via innehålls förteckningen för lagrings kontot).

![En skärm bild av panelen fil resurser](media/storage-how-to-create-file-share/create-file-share-1.png)

I listan fil resurs bör du se eventuella fil resurser som du tidigare har skapat i det här lagrings kontot. en tom tabell om inga fil resurser har skapats ännu. Välj **+ fil resurs** för att skapa en ny fil resurs.

Bladet ny fil resurs bör visas på skärmen. Fyll i fälten på bladet ny fil resurs för att skapa en fil resurs:

- **Namn**: namnet på den fil resurs som ska skapas.
- **Kvot**: kvoten för fil resursen för standard fil resurser; den allokerade storleken på fil resursen för Premium-filresurser.

Välj **skapa** för att slutföra skapandet av den nya resursen. Observera att om ditt lagrings konto finns i ett virtuellt nätverk kan du inte skapa en Azure-filresurs om inte din klient också finns i det virtuella nätverket. Du kan också kringgå den här begränsningen genom att använda Azure PowerShell `New-AzRmStorageShare` cmdlet.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
Du kan skapa Azure-filresursen med [`New-AzRmStorageShare`](/powershell/module/az.storage/New-AzRmStorageShare) cmdleten. Följande PowerShell-kommandon förutsätter att du har angett variablerna `$resourceGroupName` och `$storageAccountName` enligt definitionen ovan i avsnittet Skapa ett lagrings konto med Azure PowerShell. 

> [!Important]  
> För Premium-filresurser `-QuotaGiB` refererar parametern till den allokerade storleken på fil resursen. Den allokerade storleken på fil resursen är den mängd som du debiteras för, oavsett användning. Standard fil resurser faktureras baserat på användning i stället för en etablerad storlek.

```azurepowershell-interactive
$shareName = "myshare"

New-AzRmStorageShare `
    -ResourceGroupName $resourceGroupName `
    -StorageAccountName $storageAccountName `
    -Name $shareName `
    -QuotaGiB 1024 | Out-Null
```

> [!Note]  
> Namnet på filresursen får bara innehålla gemener. Fullständig information om namngivning av fil resurser och filer finns i [namnge och referera till resurser, kataloger, filer och metadata](https://msdn.microsoft.com/library/azure/dn167011.aspx).

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
Innan vi kan skapa en Azure-filresurs med Azure CLI måste du skaffa en lagrings konto nyckel för att auktorisera fil resursens skapa-åtgärd med. Detta kan göras med [`az storage account keys list`](/cli/azure/storage/account/keys) kommandot:

```azurecli-interactive
storageAccountKey=$(az storage account keys list \
    --resource-group $resourceGroupName \
    --account-name $storageAccountName \
    --query "[0].value" | tr -d '"')
```

När du har lagrings konto nyckeln kan du skapa Azure-filresursen med [`az storage share create`](/cli/azure/storage/share) kommandot. 

> [!Important]  
> För Premium-filresurser `--quota` refererar parametern till den allokerade storleken på fil resursen. Den allokerade storleken på fil resursen är den mängd som du debiteras för, oavsett användning. Standard fil resurser faktureras baserat på användning i stället för en etablerad storlek.

```azurecli-interactive
shareName="myshare"

az storage share create \
    --account-name $storageAccountName \
    --account-key $storageAccountKey \
    --name $shareName \
    --quota 1024 \
    --output none
```

Det här kommandot kommer inte att fungera om lagrings kontot finns i ett virtuellt nätverk och datorn som du anropar kommandot från inte ingår i det virtuella nätverket. Du kan undvika den här begränsningen genom att använda Azure PowerShell `New-AzRmStorageShare` cmdlet enligt beskrivningen ovan, eller genom att köra Azure CLI från en dator som är en del av det virtuella nätverket, inklusive via en VPN-anslutning.

---

> [!Note]  
> Namnet på filresursen får bara innehålla gemener. Fullständig information om namngivning av fil resurser och filer finns i [namnge och referera till resurser, kataloger, filer och metadata](https://msdn.microsoft.com/library/azure/dn167011.aspx).

### <a name="create-a-hot-or-cool-file-share"></a>Skapa en frekvent eller svag fil resurs
En fil resurs i ett **GPv2-lagrings konto (General Purpose v2)** kan innehålla transaktions optimerade, frekventa eller häftiga fil resurser (eller en blandning av dessa). Transaktions optimerade resurser är tillgängliga i alla Azure-regioner, men frekventa och häftiga fil resurser är bara tillgängliga [i en delmängd av regioner](storage-files-planning.md#storage-tiers). Du kan skapa en frekvent eller låg fil resurs med hjälp av Azure PowerShell Preview-modulen eller Azure CLI. 

# <a name="portal"></a>[Portal](#tab/azure-portal)
Azure Portal har ännu inte stöd för att skapa frekventa och häftiga fil resurser eller flytta befintliga transaktioner optimerade fil resurser till frekvent eller låg frekvent. Läs anvisningarna för att skapa en fil resurs med PowerShell eller Azure CLI.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
```PowerShell
# Update the Azure storage module to use the preview version. You may need to close and 
# reopen PowerShell before running this command. If you are running PowerShell 5.1, ensure 
# the following:
# - Run the below cmdlets as an administrator.
# - Have PowerShellGet 2.2.3 or later. Uncomment the following line to check.
# Get-Module -ListAvailable -Name PowerShellGet
Remove-Module -Name Az.Storage -ErrorAction SilentlyContinue
Uninstall-Module -Name Az.Storage
Install-Module -Name Az.Storage -RequiredVersion "2.1.1-preview" -AllowClobber -AllowPrerelease 

# Assuming $resourceGroupName and $storageAccountName from earlier in this document have already
# been populated. The access tier parameter may be TransactionOptimized, Hot, or Cool for GPv2 
# storage accounts. Standard tiers are only available in standard storage accounts. 
$shareName = "myhotshare"

New-AzRmStorageShare `
    -ResourceGroupName $resourceGroupName `
    -StorageAccountName $storageAccountName `
    -Name $shareName `
    -AccessTier Hot

# You can also change an existing share's tier.
Update-AzRmStorageShare `
    -ResourceGroupName $resourceGroupName `
    -StorageAccountName $storageAccountName `
    -Name $shareName `
    -AccessTier Cool
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
Funktionen för att skapa eller flytta en fil resurs till en speciell nivå är tillgänglig i den senaste uppdateringen av Azure CLI. Uppdatering av Azure CLI är bara för operativ system/Linux-distributioner som används. Anvisningar om hur du uppdaterar Azure CLI i systemet finns i [Installera Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli).

```bash
# Assuming $resourceGroupName and $storageAccountName from earlier in this document have already
# been populated. The access tier parameter may be TransactionOptimized, Hot, or Cool for GPv2
# storage accounts. Standard tiers are only available in standard storage accounts.
shareName="myhotshare"

az storage share-rm create \
    --resource-group $resourceGroupName \
    --storage-account $storageAccountName \
    --name $shareName \
    --access-tier "Hot"
```
---

## <a name="next-steps"></a>Nästa steg
- [Planera för distribution av Azure Files](storage-files-planning.md) eller [Planera för en distribution av Azure File Sync](storage-sync-files-planning.md). 
- [Översikt över nätverk](storage-files-networking-overview.md).
- Anslut och montera en fil resurs på [Windows](storage-how-to-use-files-windows.md), [MacOS](storage-how-to-use-files-mac.md)och [Linux](storage-how-to-use-files-linux.md).