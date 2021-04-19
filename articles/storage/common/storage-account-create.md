---
title: Skapa ett lagringskonto
titleSuffix: Azure Storage
description: Lär dig hur du skapar ett lagringskonto för att lagra blobar, filer, köer och tabeller. Ett Azure Storage-konto tillhandahåller ett unikt namnområde i Microsoft Azure för att läsa och skriva data.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 04/19/2021
ms.author: tamram
ms.subservice: common
ms.custom: devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: cb5caeb7f75834a317b222392c6e827185cfac00
ms.sourcegitcommit: 3ed0f0b1b66a741399dc59df2285546c66d1df38
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/19/2021
ms.locfileid: "107714368"
---
# <a name="create-a-storage-account"></a>Skapa ett lagringskonto

Ett Azure Storage-konto innehåller alla Azure Storage dataobjekt: blobar, filer, köer och tabeller. Lagringskontot tillhandahåller ett unikt namnområde för dina Azure Storage data som kan nås var som helst i världen via HTTP eller HTTPS. Mer information om Azure Storage-konton finns i Översikt [över lagringskonton.](storage-account-overview.md)

I den här artikeln lär du dig att skapa ett lagringskonto med [hjälp av Azure Portal](https://portal.azure.com/), [Azure PowerShell,](/powershell/azure/) [Azure CLI](/cli/azure)eller en [Azure Resource Manager mall](../../azure-resource-manager/management/overview.md).

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Krav

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.

# <a name="portal"></a>[Portal](#tab/azure-portal)

Inga.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Om du vill skapa ett Azure Storage-konto med PowerShell kontrollerar du att du har installerat [Az PowerShell-modulen](https://www.powershellgallery.com/packages/Az), version 0.7 eller senare. Mer information finns i Introduktion [till Azure PowerShell Az-modulen](/powershell/azure/new-azureps-module-az).

Kör följande kommando för att hitta din aktuella version:

```powershell
Get-InstalledModule -Name "Az"
```

Information om hur du installerar Azure PowerShell uppgraderar finns [i Installera Azure PowerShell modulen](/powershell/azure/install-az-ps).

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Du kan logga in på Azure och köra Azure CLI-kommandon på något av två sätt:

- Du kan köra CLI-kommandon från Azure Portal i Azure Cloud Shell.
- Du kan installera CLI och köra CLI-kommandon lokalt.

### <a name="use-azure-cloud-shell"></a>Använda Azure Cloud Shell

Azure Cloud Shell är ett kostnadsfritt Bash-gränssnitt som du kan köra direkt i Azure Portal. Azure CLI är förinstallerat och konfigurerat för användning med ditt konto. Klicka på **Cloud Shell** på menyn i det övre högra avsnittet i Azure Portal:

[![Cloud Shell](./media/storage-quickstart-create-account/cloud-shell-menu.png)](https://portal.azure.com)

Knappen startar ett interaktivt gränssnitt som du kan använda för att köra stegen som beskrivs i den här artikeln:

[![Skärmbild som visar Cloud Shell i portalen](./media/storage-quickstart-create-account/cloud-shell.png)](https://portal.azure.com)

### <a name="install-the-cli-locally"></a>Installera CLI lokalt

Du kan även installera och använda Azure CLI lokalt. Exemplen i den här artikeln kräver Azure CLI version 2.0.4 eller senare. Kör `az --version` för att hitta den installerade versionen. Om du behöver installera eller uppgradera kan du läsa informationen i [Installera Azure CLI](/cli/azure/install-azure-cli).

# <a name="template"></a>[Mall](#tab/template)

Inga.

---

Därefter loggar du in på Azure.

# <a name="portal"></a>[Portal](#tab/azure-portal)

Logga in på [Azure-portalen](https://portal.azure.com).

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Logga in på din Azure-prenumeration med `Connect-AzAccount` kommandot och följ anvisningarna på skärmen för att autentisera.

```powershell
Connect-AzAccount
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Starta Azure Cloud Shell genom att logga in på [Azure Portal](https://portal.azure.com).

Logga in på den lokala installationen av CLI genom att köra [kommandot az login:](/cli/azure/reference-index#az-login)

```azurecli-interactive
az login
```

# <a name="template"></a>[Mall](#tab/template)

Ej tillämpligt

---

## <a name="create-a-storage-account"></a>Skapa ett lagringskonto

Ett lagringskonto är en Azure Resource Manager resurs. Resource Manager är distributions- och hanteringstjänsten för Azure. Mer information finns i [Översikt över Azure Resource Manager](../../azure-resource-manager/management/overview.md).

Varje Resource Manager resurs, inklusive ett Azure Storage-konto, måste tillhöra en Azure-resursgrupp. En resursgrupp är en logisk container där Azure-resurserna grupperas. När du skapar ett lagringskonto kan du antingen skapa en ny resursgrupp eller använda en befintlig resursgrupp. Den här i så här artikeln visar hur du skapar en ny resursgrupp.

# <a name="portal"></a>[Portal](#tab/azure-portal)

Följ dessa steg om du vill skapa Azure Portal Azure Storage-konto:

1. I menyn till vänster i portalen väljer du **Lagringskonton för** att visa en lista över dina lagringskonton.
1. På sidan **Lagringskonton** väljer du **Nytt.**

Alternativen för ditt nya lagringskonto är ordnade i flikar på **sidan Skapa ett lagringskonto.** I följande avsnitt beskrivs var och en av flikarna och deras alternativ.

### <a name="basics-tab"></a>Fliken Grundläggande

På fliken Grundläggande anger du viktig information för ditt **lagringskonto.** När du har slutfört fliken Grundläggande inställningar kan du välja att anpassa ditt nya **lagringskonto** ytterligare genom att ange alternativ på de andra flikarna, eller så kan du välja Granska **+** skapa för att acceptera standardalternativen och fortsätta med att validera och skapa kontot.

I följande tabell beskrivs fälten på **fliken Grundläggande** inställningar.

| Avsnitt | Fält | Obligatorisk eller valfri | Description |
|--|--|--|--|
| Projektinformation | Prenumeration | Obligatorisk | Välj prenumerationen för det nya lagringskontot. |
| Projektinformation | Resursgrupp | Obligatorisk | Skapa en ny resursgrupp för det här lagringskontot eller välj en befintlig. Mer information finns i [Resursgrupper.](../../azure-resource-manager/management/overview.md#resource-groups) |
| Instansinformation | Lagringskontonamn | Obligatorisk | Välj ett unikt namn för ditt lagringskonto. Namnet på ett lagringskonto måste vara mellan 3 och 24 tecken långt och får endast innehålla siffror och gemener. |
| Instansinformation | Region | Obligatorisk | Välj lämplig region för ditt lagringskonto. Mer information finns i [Regioner och Tillgänglighetszoner i Azure](../../availability-zones/az-overview.md).<br /><br />Alla regioner stöds inte för alla typer av lagringskonton eller redundanskonfigurationer. Läs mer i [Redundansalternativ för Azure Storage](storage-redundancy.md).<br /><br />Valet av region kan ha en faktureringspåverkan. Mer information finns i Fakturering för [lagringskonto.](storage-account-overview.md#storage-account-billing) |
| Instansinformation | Prestanda | Obligatorisk | Välj **Standardprestanda** för allmänna v2-lagringskonton (standard). Den här typen av konto rekommenderas av Microsoft för de flesta scenarier. Mer information finns i Typer [av lagringskonton.](storage-account-overview.md#types-of-storage-accounts)<br /><br />Välj **Premium för** scenarier som kräver kort svarstid. När du **har valt Premium** väljer du den typ av premiumlagringskonto som ska skapas. Följande typer av Premium Storage-konton är tillgängliga: <ul><li>[Blockblobar](../blobs/storage-blob-performance-tiers.md)</li><li>[Filresurser](../files/storage-files-planning.md#management-concepts)</li><li>[Sidblobar](../blobs/storage-blob-pageblob-overview.md)</li></ul> |
| Instansinformation | Redundans | Obligatorisk | Välj önskad redundanskonfiguration. Alla redundansalternativ är inte tillgängliga för alla typer av lagringskonton i alla regioner. Mer information om redundanskonfigurationer finns i [Azure Storage redundans](storage-redundancy.md).<br /><br />Om du väljer en geo-redundant konfiguration (GRS eller GZRS) replikeras dina data till ett datacenter i en annan region. Om du vill ha läsåtkomst till data i den sekundära regionen väljer du Gör läsåtkomst till data tillgängliga **om det skulle bli otillgängligt i en region.** |

Följande bild visar en standardkonfiguration för ett nytt lagringskonto.

:::image type="content" source="media/storage-account-create/create-account-basics-tab.png" alt-text="Skärmbild som visar en standardkonfiguration för ett nytt lagringskonto – fliken Grundläggande inställningar":::

### <a name="advanced-tab"></a>Fliken Avancerat

På fliken **Avancerat** kan du konfigurera ytterligare alternativ och ändra standardinställningarna för det nya lagringskontot. Vissa av dessa alternativ kan också konfigureras när lagringskontot har skapats, medan andra måste konfigureras när det skapas.

I följande tabell beskrivs fälten på **fliken** Avancerat.

| Avsnitt | Fält | Obligatorisk eller valfri | Description |
|--|--|--|--|
| Säkerhet | Aktivera säker överföring | Valfritt | Aktivera säker överföring för att kräva att inkommande begäranden till det här lagringskontot endast görs via HTTPS (standard). Rekommenderas för optimal säkerhet. Mer information finns i [Kräv säker överföring för att säkerställa säkra anslutningar.](storage-require-secure-transfer.md) |
| Säkerhet | Aktivera infrastrukturkryptering | Valfritt | Som standard är infrastrukturkryptering inte aktiverat. Aktivera infrastrukturkryptering för att kryptera dina data på både tjänstnivå och infrastrukturnivå. Mer information finns i Skapa [ett lagringskonto med infrastrukturkryptering aktiverat för dubbel kryptering av data](infrastructure-encryption-enable.md). |
| Säkerhet | Aktivera offentlig blobåtkomst | Valfritt | När den här inställningen är aktiverad kan en användare med rätt behörighet aktivera anonym offentlig åtkomst till en container i lagringskontot (standard). Om du inaktiverar den här inställningen förhindras all anonym offentlig åtkomst till lagringskontot. Mer information finns i Förhindra [anonym offentlig läsåtkomst till containrar och blobar.](../blobs/anonymous-read-access-prevent.md)<br> <br> När du aktiverar offentlig blobåtkomst blir inte blobdata tillgängliga för offentlig åtkomst såvida inte användaren vidtar ytterligare steg för att uttryckligen konfigurera containerns inställning för offentlig åtkomst. |
| Säkerhet | Aktivera åtkomst till lagringskontonyckel (förhandsversion) | Valfritt | När den här inställningen är aktiverad kan klienter auktorisera begäranden till lagringskontot med antingen kontoåtkomstnycklarna eller ett Azure Active Directory-konto (Azure AD) (standard). Om du inaktiverar den här inställningen förhindras auktorisering med åtkomstnycklarna för kontot. Mer information finns i Prevent [Shared Key authorization for an Azure Storage account (preview) (Förhindra auktorisering med delad nyckel för ett Azure Storage konto (förhandsversion)](shared-key-authorization-prevent.md). |
| Säkerhet | Lägsta TLS-version | Obligatorisk | Välj den lägsta versionen av Transport Layer Security (TLS) för inkommande begäranden till lagringskontot. Standardvärdet är TLS version 1.2. När värdet är inställt på standardvärdet avvisas inkommande begäranden som görs med TLS 1.0 eller TLS 1.1. Mer information finns i [Framtvinga en lägsta version av Transport Layer Security (TLS) för begäranden till ett lagringskonto](transport-layer-security-configure-minimum-version.md). |
| Data Lake Storage Gen2 | Aktivera hierarkisk namnrymd | Valfritt | Om du vill använda det här Azure Data Lake Storage Gen2 för arbetsbelastningar konfigurerar du ett hierarkiskt namnområde. Mer information finns i [Introduktion till Azure Data Lake Storage Gen2](../blobs/data-lake-storage-introduction.md). |
| Blob Storage | Aktivera nätverksfilresurs (NFS) v3 (förhandsversion) | Valfritt | NFS v3 ger Kompatibilitet för Linux-filsystem i objektlagringsskala gör det möjligt för Linux-klienter att montera en container i Blob Storage från en virtuell Azure-dator (VM) eller en lokal dator. Mer information finns i [Protokollstöd för Network File System (NFS) 3.0 i Azure Blob Storage (förhandsversion).](../blobs/network-file-system-protocol-support.md) |
| Blob Storage | Åtkomstnivå | Obligatorisk | Med åtkomstnivåer för blobar kan du lagra blobdata på det mest kostnadseffektiva sättet, baserat på användning. Välj frekvent nivå (standard) för data som används ofta. Välj den coola nivån för data som inte används ofta. Mer information finns i [Åtkomstnivåer för Azure Blob Storage - hot, cool, and archive](../blobs/storage-blob-storage-tiers.md). |
| Azure Files | Aktivera stora filresurser | Valfritt | Endast tillgängligt för Premium Storage-konton för filresurser. Mer information finns i Aktivera [standardfilresurser för att sträcka sig över upp till 100 TiB.](../files/storage-files-planning.md#enable-standard-file-shares-to-span-up-to-100-tib) |
| Tabeller och köer | Aktivera stöd för kund hanterade nycklar | Valfritt | Om du vill aktivera stöd för kund hanterade nycklar för tabeller och köer måste du välja den här inställningen när du skapar lagringskontot. Mer information finns i Skapa [ett konto som stöder kundhanteringsnycklar för tabeller och köer.](account-encryption-key-create.md) |

### <a name="networking-tab"></a>Fliken Nätverk

På fliken **Nätverk** kan du konfigurera inställningar för nätverksanslutning och routning för ditt nya lagringskonto. Dessa alternativ kan också konfigureras när lagringskontot har skapats.

I följande tabell beskrivs fälten på **fliken** Nätverk.

| Avsnitt | Fält | Obligatorisk eller valfri | Description |
|--|--|--|--|
| Nätverksanslutningar | Anslutningsmetod | Obligatorisk | Som standard dirigeras inkommande nätverkstrafik till den offentliga slutpunkten för ditt lagringskonto. Du kan ange att trafiken måste dirigeras till den offentliga slutpunkten via ett virtuellt Azure-nätverk. Du kan också konfigurera privata slutpunkter för ditt lagringskonto. Mer information finns i [Använda privata slutpunkter för Azure Storage](storage-private-endpoints.md). |
| Nätverksroutning | Routningsprioritet | Obligatorisk | Inställningen för nätverksroutning anger hur nätverkstrafik dirigeras till den offentliga slutpunkten för ditt lagringskonto från klienter via Internet. Som standard använder ett nytt lagringskonto Microsofts nätverksroutning. Du kan också välja att dirigera nätverkstrafik via den POP som är närmast lagringskontot, vilket kan sänka nätverkskostnaderna. Mer information finns i Inställningar [för nätverksroutning för Azure Storage](network-routing-preference.md). |

### <a name="data-protection-tab"></a>Fliken Dataskydd

På fliken **Dataskydd kan** du konfigurera dataskyddsalternativ för blobdata i ditt nya lagringskonto.  Dessa alternativ kan också konfigureras när lagringskontot har skapats. En översikt över dataskyddsalternativen i Azure Storage finns i [Dataskyddsöversikt.](../blobs/data-protection-overview.md)

I följande tabell beskrivs fälten på **fliken** Dataskydd.

| Avsnitt | Fält | Obligatorisk eller valfri | Description |
|--|--|--|--|
| Återställning | Aktivera återställning till tidpunkt för containrar | Valfritt | Återställning till tidpunkt ger skydd mot oavsiktlig borttagning eller skada genom att du kan återställa blockblobdata till ett tidigare tillstånd. Mer information finns i [Återställning till tidpunkt för blockblobar.](../blobs/point-in-time-restore-overview.md)<br /><br />Aktivering av återställning till tidpunkt möjliggör även versionshantering av blobar, mjuk blob-borttagning och blobändringsflöde. Dessa nödvändiga funktioner kan ha en kostnadspåverkan. Mer information finns i [Priser och fakturering](../blobs/point-in-time-restore-overview.md#pricing-and-billing) för återställning till tidpunkt. |
| Återställning | Aktivera mjuk borttagning för blobar | Valfritt | Mjuk borttagning av blob skyddar en enskild blob, ögonblicksbild eller version från oavsiktliga borttagningar eller överlagringar genom att bevara borttagna data i systemet under en angiven kvarhållningsperiod. Under kvarhållningsperioden kan du återställa ett mjukt borttagna objekt till dess tillstånd när det togs bort. Mer information finns i [Mjuk borttagning för blobar.](../blobs/soft-delete-blob-overview.md)<br /><br />Microsoft rekommenderar att du aktiverar mjuk borttagning av blobar för dina lagringskonton och anger en minsta kvarhållningsperiod på sju dagar. |
| Återställning | Aktivera mjuk borttagning för containrar (förhandsversion) | Valfritt | Mjuk borttagning av containrar skyddar en container och dess innehåll från oavsiktliga borttagningar genom att bevara borttagna data i systemet under en angiven kvarhållningsperiod. Under kvarhållningsperioden kan du återställa en mjukt borttagna container till dess tillstånd när den togs bort. Mer information finns i Mjuk [borttagning för containrar (förhandsversion).](../blobs/soft-delete-container-overview.md)<br /><br />Microsoft rekommenderar att du aktiverar mjuk borttagning av containrar för dina lagringskonton och anger en minsta kvarhållningsperiod på sju dagar. |
| Återställning | Aktivera mjuk borttagning för filresurser | Valfritt | Mjuk borttagning för filresurser skyddar en filresurs och dess innehåll från oavsiktliga borttagningar genom att bevara borttagna data i systemet under en angiven kvarhållningsperiod. Under kvarhållningsperioden kan du återställa en mjukt borttagna filresurs till dess tillstånd när den togs bort. Mer information finns i Förhindra [oavsiktlig borttagning av Azure-filresurser.](../files/storage-files-prevent-file-share-deletion.md)<br /><br />Microsoft rekommenderar att du aktiverar mjuk borttagning för filresurser för Azure Files arbetsbelastningar och anger en minsta kvarhållningsperiod på sju dagar. |
| Spårning | Aktivera versionshantering för blobar | Valfritt | Blobversionshantering sparar automatiskt tillståndet för en blob i en tidigare version när bloben skrivs över. Mer information finns i [Blobversionshantering.](../blobs/versioning-overview.md)<br /><br />Microsoft rekommenderar att du aktiverar blobversionshantering för optimalt dataskydd för lagringskontot. |
| Spårning | Aktivera blobändringsflöde | Valfritt | Blobändringsflödet innehåller transaktionsloggar för alla ändringar av alla blobar i ditt lagringskonto samt deras metadata. Mer information finns i [Ändringsflödesstöd i Azure Blob Storage](../blobs/storage-blob-change-feed.md). |

### <a name="tags-tab"></a>Fliken Taggar

På fliken **Taggar** kan du ange taggar Resource Manager att organisera dina Azure-resurser. Mer information finns i [Tagga resurser, resursgrupper och prenumerationer för logisk organisation.](../../azure-resource-manager/management/tag-resources.md)

### <a name="review--create-tab"></a>Fliken Granska och skapa

När du går till fliken **Granska + skapa** kör Azure valideringen av de lagringskontoinställningar som du har valt. Om valideringen har passerat kan du fortsätta att skapa lagringskontot.

Om verifieringen misslyckas anger portalen vilka inställningar som behöver ändras.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Om du vill skapa ett v2-lagringskonto för generell användning med PowerShell skapar du först en ny resursgrupp genom att anropa kommandot [New-AzResourceGroup:](/powershell/module/az.resources/new-azresourcegroup)

```azurepowershell-interactive
$resourceGroup = "<resource-group>"
$location = "<location>"
New-AzResourceGroup -Name $resourceGroup -Location $location
```

Om du inte vet vilken region du ska ange för parametern `-Location` så kan du hämta en lista över regioner som stöds för din prenumeration med kommandot [Get-AzLocation](/powershell/module/az.resources/get-azlocation):

```azurepowershell-interactive
Get-AzLocation | select Location
```

Skapa sedan ett standardlagringskonto för generell användning v2 med RA-GRS (Read-Access Geo Redundant Storage) med kommandot [New-AzStorageAccount.](/powershell/module/az.storage/new-azstorageaccount) Kom ihåg att namnet på ditt lagringskonto måste vara unikt i Azure, så ersätt platshållarvärdet inom hakparentes med ditt eget unika värde:

```azurepowershell-interactive
New-AzStorageAccount -ResourceGroupName $resourceGroup `
  -Name <account-name> `
  -Location $location `
  -SkuName Standard_RAGRS `
  -Kind StorageV2
```

Om du vill aktivera en hierarkisk namnrymd för lagringskontot att [använda Azure Data Lake Storage](https://azure.microsoft.com/services/storage/data-lake-storage/), inkluderar du parametern i anropet till kommandot `-EnableHierarchicalNamespace $True` **New-AzStorageAccount.**

I följande tabell visas vilka värden som ska användas för parametrarna och för att skapa en viss typ av `-SkuName` lagringskonto `-Kind` med önskad redundanskonfiguration.

| Typ av lagringskonto | Redundanskonfigurationer som stöds | Värde för parametern -Kind | Möjliga värden för parametern -SkuName | Stöder hierarkisk namnrymd |
|--|--|--|--|--|
| Standard generell användning v2 | LRS/GRS/RA-GRS/ZRS/GZRS/RA-GZRS | StorageV2 | Standard_LRS/ Standard_GRS / Standard_RAGRS/ Standard_ZRS / Standard_GZRS / Standard_RAGZRS | Yes |
| Premium-blockblobar | LRS/ZRS | BlockBlobStorage | Premium_LRS/Premium_ZRS | Yes |
| Premium-filresurser | LRS/ZRS | FileStorage | Premium_LRS/Premium_ZRS | No |
| Premium-sidblobar | LRS | StorageV2 | Premium_LRS | No |
| Äldre standard för generell användning v1 | LRS/GRS/RA-GRS | Storage | Standard_LRS/Standard_GRS/Standard_RAGRS | No |
| Äldre bloblagring | LRS/GRS/RA-GRS | BlobStorage | Standard_LRS/Standard_GRS/Standard_RAGRS | No |

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Om du vill skapa ett v2-lagringskonto för generell användning med Azure CLI skapar du först en ny resursgrupp genom att anropa [kommandot az group create.](/cli/azure/group#az_group_create)

```azurecli-interactive
az group create \
  --name storage-resource-group \
  --location westus
```

Om du inte vet vilken region du ska ange för parametern `--location` kan du hämta en lista över regioner som stöds för din prenumeration med kommandot [az account list-locations](/cli/azure/account#az_account_list).

```azurecli-interactive
az account list-locations \
  --query "[].{Region:name}" \
  --out table
```

Skapa sedan ett allmänt v2-standardlagringskonto med geo-redundant lagring med läsbehörighet med hjälp av [kommandot az storage account create.](/cli/azure/storage/account#az_storage_account_create) Kom ihåg att namnet på ditt lagringskonto måste vara unikt i Azure, så ersätt platshållarvärdet inom hakparentes med ditt eget unika värde:

```azurecli-interactive
az storage account create \
  --name <account-name> \
  --resource-group storage-resource-group \
  --location westus \
  --sku Standard_RAGRS \
  --kind StorageV2
```

Om du vill aktivera en hierarkisk namnrymd för lagringskontot att [använda Azure Data Lake Storage](https://azure.microsoft.com/services/storage/data-lake-storage/), inkluderar du parametern för anropet till kommandot az storage `--enable-hierarchical-namespace true` account **create.** För att skapa en hierarkisk namnrymd krävs Azure CLI version 2.0.79 eller senare.

I följande tabell visas vilka värden som ska användas för parametrarna och för att skapa en viss typ av `-sku` lagringskonto `-kind` med önskad redundanskonfiguration.

| Typ av lagringskonto | Redundanskonfigurationer som stöds | Värde för parametern -kind | Möjliga värden för parametern -sku | Stöder hierarkisk namnrymd |
|--|--|--|--|--|
| Standard generell användning v2 | LRS/GRS/RA-GRS/ZRS/GZRS/RA-GZRS | StorageV2 | Standard_LRS/ Standard_GRS / Standard_RAGRS/ Standard_ZRS / Standard_GZRS / Standard_RAGZRS | Yes |
| Premium-blockblobar | LRS/ZRS | BlockBlobStorage | Premium_LRS/Premium_ZRS | Yes |
| Premium-filresurser | LRS/ZRS | FileStorage | Premium_LRS/Premium_ZRS | No |
| Premium-sidblobar | LRS | StorageV2 | Premium_LRS | No |
| Äldre standard för generell användning v1 | LRS/GRS/RA-GRS | Storage | Standard_LRS/Standard_GRS/Standard_RAGRS | No |
| Äldre bloblagring | LRS/GRS/RA-GRS | BlobStorage | Standard_LRS/Standard_GRS/Standard_RAGRS | No |

# <a name="template"></a>[Mall](#tab/template)

Du kan använda antingen Azure PowerShell eller Azure CLI för att distribuera en Resource Manager för att skapa ett lagringskonto. Mallen som används i den här artikeln kommer från Azure Resource Manager [snabbstartsmallar](https://azure.microsoft.com/resources/templates/101-storage-account-create/). Om du vill köra skripten väljer **du Prova för** att öppna Azure Cloud Shell. Klistra in skriptet genom att högerklicka på gränssnittet och sedan välja **Klistra in.**

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"

New-AzResourceGroup -Name $resourceGroupName -Location "$location"
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json"
```

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
echo "Enter the location (i.e. centralus):" &&
read location &&
az group create --name $resourceGroupName --location "$location" &&
az deployment group create --resource-group $resourceGroupName --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json"
```

> [!NOTE]
> Den här mallen fungerar bara som ett exempel. Det finns många inställningar för lagringskonto som inte har konfigurerats som en del av den här mallen. Om du till exempel vill använda [Data Lake Storage](https://azure.microsoft.com/services/storage/data-lake-storage/)ändrar du den här mallen genom att ange `isHnsEnabledad` -egenskapen för `StorageAccountPropertiesCreateParameters` -objektet till `true` .

Information om hur du ändrar den här mallen eller skapar nya finns i:

- [Azure Resource Manager dokumentation](../../azure-resource-manager/index.yml).
- [Mallreferens för lagringskonto.](/azure/templates/microsoft.storage/allversions)
- [Ytterligare exempel på lagringskontomallar.](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Storage)

---

## <a name="delete-a-storage-account"></a>Ta bort ett lagringskonto

Om du tar bort ett lagringskonto tas hela kontot bort, inklusive alla data i kontot, och kan inte ångras.

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. Gå till lagringskontot i [Azure Portal](https://portal.azure.com).
1. Klicka på **Ta bort**.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Om du vill ta bort lagringskontot använder du kommandot [Remove-AzStorageAccount:](/powershell/module/az.storage/remove-azstorageaccount)

```powershell
Remove-AzStorageAccount -Name <storage-account> -ResourceGroupName <resource-group>
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Om du vill ta bort lagringskontot använder du [kommandot az storage account delete:](/cli/azure/storage/account#az-storage-account-delete)

```azurecli-interactive
az storage account delete --name <storage-account> --resource-group <resource-group>
```

# <a name="template"></a>[Mall](#tab/template)

Om du vill ta bort lagringskontot använder du antingen Azure PowerShell eller Azure CLI.

```azurepowershell-interactive
$storageResourceGroupName = Read-Host -Prompt "Enter the resource group name"
$storageAccountName = Read-Host -Prompt "Enter the storage account name"
Remove-AzStorageAccount -Name $storageAccountName -ResourceGroupName $storageResourceGroupName
```

```azurecli-interactive
echo "Enter the resource group name:" &&
read resourceGroupName &&
echo "Enter the storage account name:" &&
read storageAccountName &&
az storage account delete --name storageAccountName --resource-group resourceGroupName
```

---

Alternativt kan du ta bort resursgruppen, vilket tar bort lagringskontot och andra resurser i resursgruppen. Mer information om hur du tar bort en resursgrupp finns i [Ta bort resursgrupp och resurser.](../../azure-resource-manager/management/delete-resource-group.md)

> [!WARNING]
> Det går inte att återställa ett borttaget lagringskonto eller att hämta innehåll som det innehöll före borttagningen. Var noga med att säkerhetskopiera allt som du vill spara innan du tar bort kontot. Detta gäller även alla resurser i kontot. När du tar bort en blobb, tabell, kö eller fil tas den bort permanent.
>
> Om du försöker ta bort ett lagringskonto som är kopplat till en virtuell Azure-dator kan du få ett felmeddelande om att lagringskontot fortfarande används. Information om hur du felsöker det här [felet finns i Felsöka fel när du tar bort lagringskonton.](/troubleshoot/azure/virtual-machines/welcome-virtual-machines)

## <a name="next-steps"></a>Nästa steg

- [Översikt över lagringskonto](storage-account-overview.md)
- [Uppgradera till ett v2-lagringskonto för generell användning](storage-account-upgrade.md)
- [Flytta ett lagringskonto till en annan region](storage-account-move.md)
- [Återställa ett borttaget lagringskonto](storage-account-recover.md)