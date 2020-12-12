---
title: Skapa ett lagringskonto
titleSuffix: Azure Storage
description: Lär dig att skapa ett lagrings konto för att lagra blobar, filer, köer och tabeller. Ett Azure Storage-konto tillhandahåller ett unikt namn område i Microsoft Azure för att läsa och skriva data.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 12/11/2020
ms.author: tamram
ms.subservice: common
ms.custom: devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: c8d07e53e259fff28e662f0c1ca3934eabf015e8
ms.sourcegitcommit: dfc4e6b57b2cb87dbcce5562945678e76d3ac7b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/12/2020
ms.locfileid: "97357457"
---
# <a name="create-a-storage-account"></a>Skapa ett lagringskonto

Ett Azure Storage-konto innehåller alla dina Azure Storage data objekt: blobbar, filer, köer, tabeller och diskar. Lagrings kontot tillhandahåller ett unikt namn område för dina Azure Storage data som är tillgängliga från var som helst i världen via HTTP eller HTTPS. Data i ditt Azure Storage-konto är tåliga och mycket tillgängliga, säkra och enorma skalbara.

I den här instruktions artikeln lär du dig att skapa ett lagrings konto med hjälp av [Azure Portal](https://portal.azure.com/), [Azure POWERSHELL](/powershell/azure/), [Azure CLI](/cli/azure)eller en [Azure Resource Manager-mall](../../azure-resource-manager/management/overview.md).  

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Krav

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.

# <a name="portal"></a>[Portal](#tab/azure-portal)

Inga.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Om du vill skapa ett Azure Storage-konto med PowerShell kontrollerar du att du har installerat Azure PowerShell modul AZ version 0,7 eller senare. Mer information finns i [Introduktion till modulen för Azure PowerShell AZ](/powershell/azure/new-azureps-module-az).

Kör följande kommando för att hitta din aktuella version:

```powershell
Get-InstalledModule -Name "Az"
```

Information om hur du installerar eller uppgraderar Azure PowerShell finns i [installera Azure PowerShell-modulen](/powershell/azure/install-Az-ps).

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Du kan logga in på Azure och köra Azure CLI-kommandon på ett av två sätt:

- Du kan köra CLI-kommandon inifrån Azure Portal i Azure Cloud Shell.
- Du kan installera CLI-och kör CLI-kommandona lokalt.

### <a name="use-azure-cloud-shell"></a>Använda Azure Cloud Shell

Azure Cloud Shell är ett kostnadsfritt Bash-gränssnitt som du kan köra direkt i Azure Portal. Azure CLI är förinstallerat och konfigurerat för användning med ditt konto. Klicka på knappen **Cloud Shell** på menyn i det övre högra avsnittet av Azure Portal:

[![Cloud Shell](./media/storage-quickstart-create-account/cloud-shell-menu.png)](https://portal.azure.com)

Knappen startar ett interaktivt gränssnitt som du kan använda för att köra stegen som beskrivs i den här instruktions artikeln:

[![Skärm bild som visar Cloud Shell-fönstret i portalen](./media/storage-quickstart-create-account/cloud-shell.png)](https://portal.azure.com)

### <a name="install-the-cli-locally"></a>Installera CLI lokalt

Du kan även installera och använda Azure CLI lokalt. Exemplen i den här artikeln kräver Azure CLI version 2.0.4 eller senare. Kör `az --version` för att hitta den installerade versionen. Om du behöver installera eller uppgradera kan du läsa informationen i [Installera Azure CLI](/cli/azure/install-azure-cli).

# <a name="template"></a>[Mall](#tab/template)

Inga.

---

## <a name="sign-in-to-azure"></a>Logga in på Azure

# <a name="portal"></a>[Portal](#tab/azure-portal)

Logga in på [Azure-portalen](https://portal.azure.com).

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Logga in på din Azure-prenumeration med `Connect-AzAccount` kommandot och följ anvisningarna på skärmen för att autentisera.

```powershell
Connect-AzAccount
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Logga in på [Azure Portal](https://portal.azure.com)för att starta Azure Cloud Shell.

Logga in på den lokala installationen av CLI genom att köra kommandot [AZ login](/cli/azure/reference-index#az-login) :

```azurecli-interactive
az login
```

# <a name="template"></a>[Mall](#tab/template)

Ej tillämpligt

---

## <a name="create-a-storage-account"></a>Skapa ett lagringskonto

Varje lagringskonto måste tillhöra en Azure-resursgrupp. En resursgrupp är en logisk container där Azure-resurserna grupperas. När du skapar ett lagringskonto kan du antingen skapa en ny resursgrupp eller använda en befintlig resursgrupp. Den här artikeln visar hur du skapar en ny resurs grupp.

Ett **v2-lagringskonto för generell användning** ger åtkomst till alla Azure Storage-tjänster: blobar, filer, köer, tabeller och diskar. De steg som beskrivs här skapar ett allmänt-syfte v2-lagrings konto, men stegen för att skapa en typ av lagrings konto liknar varandra. Mer information om typer av lagringskonton och andra inställningar för lagringskonto finns i [översikten över Azure-lagringskonton](storage-account-overview.md).

# <a name="portal"></a>[Portal](#tab/azure-portal)

[!INCLUDE [storage-create-account-portal-include](../../../includes/storage-create-account-portal-include.md)]

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Skapa först en ny resursgrupp med PowerShell med kommandot [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup):

```powershell
# put resource group in a variable so you can use the same group name going forward,
# without hard-coding it repeatedly
$resourceGroup = "storage-resource-group"
$location = "westus"
New-AzResourceGroup -Name $resourceGroup -Location $location
```

Om du inte vet vilken region du ska ange för parametern `-Location` så kan du hämta en lista över regioner som stöds för din prenumeration med kommandot [Get-AzLocation](/powershell/module/az.resources/get-azlocation):

```powershell
Get-AzLocation | select Location
```

Därefter skapar du ett allmänt-syfte v2-lagrings konto med Geo-redundant lagring med Läs behörighet (RA-GRS) med hjälp av kommandot [New-AzStorageAccount](/powershell/module/az.storage/New-azStorageAccount) . Kom ihåg att namnet på ditt lagrings konto måste vara unikt i Azure, så Ersätt plats hållarens värde inom hakparenteser med ditt eget unika värde:

```powershell
New-AzStorageAccount -ResourceGroupName $resourceGroup `
  -Name <account-name> `
  -Location $location `
  -SkuName Standard_RAGRS `
  -Kind StorageV2
```

> [!IMPORTANT]
> Om du planerar att använda [Azure Data Lake Storage](https://azure.microsoft.com/services/storage/data-lake-storage/)ska du ta med `-EnableHierarchicalNamespace $True` i den här listan över parametrar.

Om du vill skapa ett allmänt-syfte v2-lagrings konto med ett annat replikeringsalternativ, ersätter du det önskade värdet i tabellen nedan för parametern **SkuName** .

|Replikeringsalternativ  |SkuName-parameter  |
|---------|---------|
|Lokalt redundant lagring (LRS)     |Standard_LRS         |
|Zonredundant lagring (ZRS)     |Standard_ZRS         |
|Geo-redundant lagring (GRS)     |Standard_GRS         |
|Read-Access Geo-Redundant Storage (GRS)     |Standard_RAGRS         |
|Geografiskt zonredundant lagring (GZRS)    |Standard_GZRS         |
|Skrivskyddad geozonredundant lagring (RA-GZRS)    |Standard_RAGZRS         |

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Skapa först en ny resursgrupp med Azure CLI med kommandot [az group create](/cli/azure/group#az_group_create).

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

Skapa sedan ett allmänt-syfte v2-lagrings konto med Geo-redundant lagring med Läs behörighet med hjälp av kommandot [AZ Storage Account Create](/cli/azure/storage/account#az_storage_account_create) . Kom ihåg att namnet på ditt lagrings konto måste vara unikt i Azure, så Ersätt plats hållarens värde inom hakparenteser med ditt eget unika värde:

```azurecli-interactive
az storage account create \
    --name <account-name> \
    --resource-group storage-resource-group \
    --location westus \
    --sku Standard_RAGRS \
    --kind StorageV2
```

> [!IMPORTANT]
> Om du planerar att använda [Azure Data Lake Storage](https://azure.microsoft.com/services/storage/data-lake-storage/)ska du ta med `--enable-hierarchical-namespace true` i den här listan över parametrar.

Om du vill skapa ett allmänt-syfte v2-lagrings konto med ett annat replikeringsalternativ, ersätter du det önskade värdet i tabellen nedan för **SKU** -parametern.

|Replikeringsalternativ  |sku-parameter  |
|---------|---------|
|Lokalt redundant lagring (LRS)     |Standard_LRS         |
|Zonredundant lagring (ZRS)     |Standard_ZRS         |
|Geo-redundant lagring (GRS)     |Standard_GRS         |
|Read-Access Geo-Redundant Storage (GRS)     |Standard_RAGRS         |
|Geografiskt zonredundant lagring (GZRS)    |Standard_GZRS         |
|Skrivskyddad geozonredundant lagring (RA-GZRS)    |Standard_RAGZRS         |

# <a name="template"></a>[Mall](#tab/template)

Du kan använda antingen Azure PowerShell eller Azure CLI för att distribuera en Resource Manager-mall för att skapa ett lagrings konto. Mallen som används i den här instruktions artikeln är från [Azure Resource Manager snabb starts mallar](https://azure.microsoft.com/resources/templates/101-storage-account-create/). Om du vill köra skripten väljer du **prova** att öppna Azure Cloud Shell. Om du vill klistra in skriptet högerklickar du på gränssnittet och väljer **Klistra in**.

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
az group deployment create --resource-group $resourceGroupName --template-file "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json"
```

> [!NOTE]
> Den här mallen fungerar bara som ett exempel. Det finns många inställningar för lagrings konton som inte har kon figurer ATS som en del av den här mallen. Om du till exempel vill använda [Azure Data Lake Storage](https://azure.microsoft.com/services/storage/data-lake-storage/)ändrar du mallen genom `isHnsEnabledad` att ange egenskapen för `StorageAccountPropertiesCreateParameters` objektet till `true` . 

Information om hur du ändrar den här mallen eller skapar nya finns i:

- [Azure Resource Manager-dokumentation](../../azure-resource-manager/index.yml).
- [Referens för lagrings konto mal len](/azure/templates/microsoft.storage/allversions).
- [Fler exempel på mallar för lagrings konton](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Storage).

---

## <a name="delete-a-storage-account"></a>Ta bort ett lagringskonto

Om du tar bort ett lagrings konto raderas hela kontot, inklusive alla data i kontot, och det går inte att ångra.

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. Navigera till lagrings kontot i [Azure Portal](https://portal.azure.com).
1. Klicka på **Ta bort**.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Om du vill ta bort lagrings kontot använder du kommandot [Remove-AzStorageAccount](/powershell/module/az.storage/remove-azstorageaccount) :

```powershell
Remove-AzStorageAccount -Name <storage-account> -ResourceGroupName <resource-group>
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Om du vill ta bort lagrings kontot använder du kommandot [AZ Storage Account Delete](/cli/azure/storage/account#az-storage-account-delete) :

```azurecli-interactive
az storage account delete --name <storage-account> --resource-group <resource-group>
```

# <a name="template"></a>[Mall](#tab/template)

Om du vill ta bort lagrings kontot använder du antingen Azure PowerShell eller Azure CLI.

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

Alternativt kan du ta bort resurs gruppen, vilket innebär att lagrings kontot och andra resurser i resurs gruppen tas bort. Mer information om hur du tar bort en resurs grupp finns i [ta bort resurs grupp och resurser](../../azure-resource-manager/management/delete-resource-group.md).

> [!WARNING]
> Det går inte att återställa ett borttaget lagringskonto eller att hämta innehåll som det innehöll före borttagningen. Var noga med att säkerhetskopiera allt som du vill spara innan du tar bort kontot. Detta gäller även alla resurser i kontot. När du tar bort en blobb, tabell, kö eller fil tas den bort permanent.
>
> Om du försöker ta bort ett lagringskonto som är kopplat till en virtuell Azure-dator kan du få ett felmeddelande om att lagringskontot fortfarande används. Information om hur du felsöker det här felet finns i [Felsöka fel när du tar bort lagrings konton](../../virtual-machines/troubleshooting/index.yml).

## <a name="next-steps"></a>Nästa steg

- [Översikt över lagringskonto](storage-account-overview.md)
- [Uppgradera till ett V2-lagringskonto för generell användning](storage-account-upgrade.md)
- [Flytta ett Azure Storage-konto till en annan region](storage-account-move.md)
- [Återställa ett borttaget lagringskonto](storage-account-recover.md)