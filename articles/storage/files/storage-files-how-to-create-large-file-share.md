---
title: Aktivera och skapa stora filresurser – Azure Files
description: I den här artikeln får du lära dig hur du aktiverar och skapar stora filresurser.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 04/20/2021
ms.author: rogarana
ms.subservice: files
ms.custom: devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: a53f964020583c41e2400d97ad244bacd33813bc
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2021
ms.locfileid: "107818268"
---
# <a name="enable-and-create-large-file-shares"></a>Aktivera och skapa stora filresurser
Dina Azure-filresurser kan skalas upp till 100 TiB när du har aktivera stora filresurser på ditt lagringskonto. När du aktiverar stora filresurser kan det också öka filresursens IOPS- och dataflödesgränser. Du kan också aktivera den här skalningen på dina befintliga lagringskonton för befintliga och nya filresurser. Mer information om prestandaskillnader finns i [filresurs- och filskalningsmål.](storage-files-scale-targets.md#azure-files-scale-targets)

## <a name="prerequisites"></a>Krav
- Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.
- Om du tänker använda Azure CLI installerar [du den senaste versionen](/cli/azure/install-azure-cli).
- Om du tänker använda modulen Azure PowerShell installerar [du den senaste versionen](/powershell/azure/install-az-ps).

## <a name="restrictions"></a>Begränsningar
För tillfället kan du bara använda lokalt redundant lagring (LRS) eller zonredundant lagring (ZRS) på lagringskonton med stora filresurser aktiverade. Du kan inte använda geo-zonredundant lagring (GZRS), geo-redundant lagring (GRS), read-access geo-redundant storage (RA-GRS) eller read-access geo-zone-redundant storage (RA-GZRS).

Det går inte att ångra aktiveringen av stora filresurser på ett konto. När du har aktiverar det kan du inte konvertera ditt konto till GZRS, GRS, RA-GRS eller RA-GZRS.

## <a name="create-a-new-storage-account"></a>Skapa ett nytt lagringskonto

# <a name="portal"></a>[Portal](#tab/azure-portal)
1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Välj **Alla tjänster** i Azure-portalen. 
1. I listan över resurser anger du **Lagringskonton**. När du skriver filtreras listan baserat på dina indata. Välj **Lagringskonton**.
1. På **bladet Lagringskonton** som visas väljer du **+ Nytt.**
1. På bladet Grundprinciper fyller du i de val du vill ha.
1. Kontrollera att **Prestanda är** inställt på **Standard**.
1. Ange **Redundans** till **Lokalt redundant lagring eller** **Zonredundant lagring.**
1. Välj **bladet** Avancerat och välj sedan **alternativet Aktiverad** till höger om **Stora filresurser.**
1. Välj **Granska + skapa** för att granska inställningarna för ditt lagringskonto och skapa kontot.
1. Välj **Skapa**.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
Om du vill skapa ett lagringskonto med stora filresurser aktiverade använder du följande kommando. Ersätt `<yourStorageAccountName>` , och med din `<yourResourceGroup>` `<yourDesiredRegion>` information.

```powershell
# This command creates a large file share–enabled account. It will not support changing the 
# redundancy to GRS, GZRS, RA-GRS, or RA-GZRS.
New-AzStorageAccount `
    -ResourceGroupName <yourResourceGroup> `
    -Name <yourStorageAccountName> `
    -Location <yourDesiredRegion> `
    -SkuName Standard_LRS `
    -EnableLargeFileShare
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
Om du vill skapa ett lagringskonto med stora filresurser aktiverade använder du följande kommando. Ersätt `<yourStorageAccountName>` , och med din `<yourResourceGroup>` `<yourDesiredRegion>` information.

```azurecli-interactive
# This command creates a large file share–enabled account. It will not support changing the 
# redundancy to GRS, GZRS, RA-GRS, or RA-GZRS.
az storage account create \
    --name <yourStorageAccountName> \
    -g <yourResourceGroup> \
    -l <yourDesiredRegion> \
    --sku Standard_LRS \
    --kind StorageV2 \
    --enable-large-file-share
```

---

När du har skapat ett lagringskonto med stor filresurs aktiverat kan du skapa en filresurs som utnyttjar den stora kapaciteten och skalningsgränserna. Mer information om hur du skapar lagringskonton och Azure-filresurser finns i [Skapa en Azure-filresurs.](storage-how-to-create-file-share.md)

## <a name="enable-large-files-shares-on-an-existing-account"></a>Aktivera stora filresurser på ett befintligt konto
Du kan också aktivera stora filresurser på dina befintliga LRS- och ZRS-lagringskonton. Om du har ett GRS-, GZRS-, RA-GRS- eller RA-GZRS-konto måste du konvertera det till ett LRS-konto innan du fortsätter.

# <a name="portal"></a>[Portal](#tab/azure-portal)
1. Öppna [Azure Portal](https://portal.azure.com)och gå till det lagringskonto där du vill aktivera stora filresurser.
1. Öppna lagringskontot och välj **Filresurser**.
1. Välj **Aktiverad** på **stora filresurser** och välj sedan **Spara.**
1. Välj **Översikt** och sedan **Uppdatera.**
1. Välj **Dela kapacitet** och välj sedan **100 TiB** och **Spara**.

    :::image type="content" source="media/storage-files-how-to-create-large-file-share/files-enable-large-file-share-existing-account.png" alt-text="Skärmbild av Azure Storage-kontot, bladet filresurser med 100 tib-resurser markerade.":::

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
Om du vill aktivera stora filresurser på ditt befintliga konto använder du följande kommando. Ersätt `<yourStorageAccountName>` och med din `<yourResourceGroup>` information.

```powershell
Set-AzStorageAccount `
    -ResourceGroupName <yourResourceGroup> `
    -Name <yourStorageAccountName> `
    -EnableLargeFileShare
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
Om du vill aktivera stora filresurser på ditt befintliga konto använder du följande kommando. Ersätt `<yourStorageAccountName>` och med din `<yourResourceGroup>` information.

```azurecli-interactive
az storage account update --name <yourStorageAccountName> -g <yourResourceGroup> --enable-large-file-share
```

---

Nu har du aktiverat stora filresurser på ditt lagringskonto. Därefter måste du uppdatera [den befintliga resursens kvot för att](#expand-existing-file-shares) dra nytta av ökad kapacitet och skalning. 

> [!Important]  
> Befintliga filresurser skalas inte upp till de annonserade gränserna för stora filresurser såvida inte kvoten har ändrats.

## <a name="expand-existing-file-shares"></a>Expandera befintliga filresurser
När du har aktiverat stora filresurser på ditt lagringskonto måste du expandera befintliga filresurser i det lagringskontot för att dra nytta av den ökade kapaciteten och skalan. 

# <a name="portal"></a>[Portal](#tab/azure-portal)
1. Välj Filresurser från ditt **lagringskonto.**
1. Högerklicka på filresursen och välj sedan **Kvot.**
1. Ange den nya storleken som du vill ha och välj sedan **OK.**

![Användargränssnittet Azure Portal med kvoten för befintliga filresurser](media/storage-files-how-to-create-large-file-share/update-large-file-share-quota.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
Om du vill ange kvoten till den maximala storleken använder du följande kommando. Ersätt `<YourResourceGroupName>` , och med din `<YourStorageAccountName>` `<YourStorageAccountFileShareName>` information.

```powershell
$resourceGroupName = "<YourResourceGroupName>"
$storageAccountName = "<YourStorageAccountName>"
$shareName="<YourStorageAccountFileShareName>"

# update quota
Set-AzRmStorageShare `
    -ResourceGroupName $resourceGroupName `
    -StorageAccountName $storageAccountName `
    -Name $shareName `
    -QuotaGiB 102400
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
Om du vill ange kvoten till den maximala storleken använder du följande kommando. Ersätt `<yourResourceGroupName>` , och med din `<yourStorageAccountName>` `<yourFileShareName>` information.

```azurecli-interactive
az storage share-rm update \
    --resource-group <yourResourceGroupName> \
    --storage-account <yourStorageAccountName> \
    --name <yourFileShareName> \
    --quota 102400
```

---

## <a name="next-steps"></a>Nästa steg
* [Ansluta och montera en filresurs i Windows](storage-how-to-use-files-windows.md)
* [Ansluta och montera en filresurs i Linux](storage-how-to-use-files-linux.md)
* [Ansluta och montera en filresurs på macOS](storage-how-to-use-files-mac.md)