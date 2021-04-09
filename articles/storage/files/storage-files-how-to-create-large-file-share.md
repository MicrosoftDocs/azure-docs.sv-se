---
title: Aktivera och skapa stora fil resurser – Azure Files
description: I den här artikeln får du lära dig hur du aktiverar och skapar stora fil resurser.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 05/29/2020
ms.author: rogarana
ms.subservice: files
ms.custom: devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: c22b3f3164cbb7c1a7ed150d093f77777c7b1023
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "102501302"
---
# <a name="enable-and-create-large-file-shares"></a>Aktivera och skapa stora fil resurser

När du aktiverar stora fil resurser på ditt lagrings konto kan fil resurserna skala upp till 100 TiB, samtidigt som du ökar IOPS och data flödes gränserna för standard resurserna. Du kan också aktivera den här skalningen på dina befintliga lagrings konton för dina befintliga fil resurser. Se [fil resurs-och fil skalnings mål](storage-files-scale-targets.md#azure-files-scale-targets) för mer information. 

## <a name="prerequisites"></a>Krav

- Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.
- Om du tänker använda Azure CLI [installerar du den senaste versionen](/cli/azure/install-azure-cli).
- Om du tänker använda Azure PowerShell-modulen [installerar du den senaste versionen](/powershell/azure/install-az-ps).

## <a name="restrictions"></a>Begränsningar

För närvarande kan du bara använda lokalt redundant lagring (LRS) eller zon redundant lagring (ZRS) på konton med stor fil resurs – aktiverade konton. Du kan inte använda geo-Zone-redundant lagring (GZRS), Geo-redundant lagring (GRS), Geo-redundant lagring med Läs behörighet (RA-GRS) eller Read-Access geo-Zone-redundant lagring (RA-GZRS).

Att aktivera stora fil resurser på ett konto är en oåterkallelig process. När du har aktiverat det kan du inte konvertera ditt konto till GZRS, GRS, RA-GRS eller RA-GZRS.

## <a name="create-a-new-storage-account"></a>Skapa ett nytt lagringskonto

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Välj **Alla tjänster** i Azure-portalen. 
1. Ange **lagrings konton** i listan över resurser. När du skriver filtreras listan baserat på dina inaktuella inaktuella inaktuella inaktuella. Välj **lagrings konton**.
1. I fönstret **lagrings konton** som visas väljer du **Lägg till**.
1. Välj den prenumeration som du ska använda för att skapa lagrings kontot.
1. Under fältet **Resursgrupp** väljer du **Skapa ny**. Ange ett namn för din nya resurs grupp.

    ![Skärmbild som visar hur du skapar en resursgrupp i portalen](media/storage-files-how-to-create-large-file-share/create-large-file-share.png)

1. Ange sedan ett namn för lagringskontot. Namnet måste vara unikt i Azure. Namnet måste också vara mellan 3 och 24 tecken långt och får bara innehålla siffror och gemener.
1. Välj en plats för ditt lagrings konto.
1. Ange replikeringen till antingen **Lokalt Redundant lagring** eller **zon-redundant lagring**.
1. Lämna dessa fält till standardvärdena:

   |Fält  |Värde  |
   |---------|---------|
   |Distributionsmodell     |Resource Manager         |
   |Prestanda     |Standard         |
   |Typ av konto     |StorageV2 (generell användning v2)         |
   |Åtkomstnivå     |Frekvent         |

1. Välj **Avancerat** och välj sedan knappen **aktive rad** alternativ till höger om **stora fil resurser**.
1. Välj **Granska + skapa** för att granska inställningarna för ditt lagringskonto och skapa kontot.

    ![Skärm bild med alternativ knappen "aktive rad" i ett nytt lagrings konto i Azure Portal](media/storage-files-how-to-create-large-file-share/large-file-shares-advanced-enable.png)

1. Välj **Skapa**.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Installera först [den senaste versionen av Azure CLI](/cli/azure/install-azure-cli) så att du kan aktivera stora fil resurser.

Om du vill skapa ett lagrings konto med stora fil resurser aktiverade använder du följande kommando. Ersätt `<yourStorageAccountName>` , `<yourResourceGroup>` och `<yourDesiredRegion>` med din information.

```azurecli-interactive
## This command creates a large file share–enabled account. It will not support GZRS, GRS, RA-GRS, or RA-GZRS.
az storage account create --name <yourStorageAccountName> -g <yourResourceGroup> -l <yourDesiredRegion> --sku Standard_LRS --kind StorageV2 --enable-large-file-share
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Installera först [den senaste versionen av PowerShell](/powershell/azure/install-az-ps) så att du kan aktivera stora fil resurser.

Om du vill skapa ett lagrings konto med stora fil resurser aktiverade använder du följande kommando. Ersätt `<yourStorageAccountName>` , `<yourResourceGroup>` och `<yourDesiredRegion>` med din information.

```powershell
## This command creates a large file share–enabled account. It will not support GZRS, GRS, RA-GRS, or RA-GZRS.
New-AzStorageAccount -ResourceGroupName <yourResourceGroup> -Name <yourStorageAccountName> -Location <yourDesiredRegion> -SkuName Standard_LRS -EnableLargeFileShare;
```
---

## <a name="enable-large-files-shares-on-an-existing-account"></a>Aktivera stora fil resurser på ett befintligt konto

Du kan också aktivera stora fil resurser på dina befintliga konton. Om du aktiverar stora fil resurser kan du inte konvertera till GZRS, GRS, RA-GRS eller RA-GZRS. Att aktivera stora fil resurser går inte att ångra på det här lagrings kontot.

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. Öppna [Azure Portal](https://portal.azure.com)och gå till det lagrings konto där du vill aktivera stora fil resurser.
1. Öppna lagrings kontot och välj **konfiguration**.
1. Välj **aktive rad** på **stora fil resurser** och välj sedan **Spara**.
1. Välj **Översikt** och välj **Uppdatera**.

![Att välja alternativ knappen aktive rad i ett befintligt lagrings konto i Azure Portal](media/storage-files-how-to-create-large-file-share/enable-large-file-shares-on-existing.png)

Nu har du aktiverat stora fil resurser på ditt lagrings konto. Sedan måste du [Uppdatera den befintliga resursens kvot](#expand-existing-file-shares) för att dra nytta av ökad kapacitet och skalning.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Använd följande kommando för att aktivera stora fil resurser på ditt befintliga konto. Ersätt `<yourStorageAccountName>` och `<yourResourceGroup>` med din information.

```azurecli-interactive
az storage account update --name <yourStorageAccountName> -g <yourResourceGroup> --enable-large-file-share
```

Nu har du aktiverat stora fil resurser på ditt lagrings konto. Sedan måste du [Uppdatera den befintliga resursens kvot](#expand-existing-file-shares) för att dra nytta av ökad kapacitet och skalning.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Använd följande kommando för att aktivera stora fil resurser på ditt befintliga konto. Ersätt `<yourStorageAccountName>` och `<yourResourceGroup>` med din information.

```powershell
Set-AzStorageAccount -ResourceGroupName <yourResourceGroup> -Name <yourStorageAccountName> -EnableLargeFileShare
```

Nu har du aktiverat stora fil resurser på ditt lagrings konto. Sedan måste du [Uppdatera den befintliga resursens kvot](#expand-existing-file-shares) för att dra nytta av ökad kapacitet och skalning.

---

## <a name="create-a-large-file-share"></a>Skapa en stor fil resurs

När du har aktiverat stora fil resurser på ditt lagrings konto kan du skapa fil resurser i det kontot med högre kvoter. 

# <a name="portal"></a>[Portal](#tab/azure-portal)

Att skapa en stor fil resurs är nästan identiskt med att skapa en standard fil resurs. Den största skillnaden är att du kan ange en kvot på upp till 100 TiB.

1. Välj **fil resurser** från ditt lagrings konto.
1. Välj **+ Filresurs**.
1. Ange ett namn på fil resursen. Du kan också ange den kvot storlek som du vill, upp till 100 TiB. Välj sedan **Skapa**. 

![Azure Portal gränssnittet visar rutorna namn och kvot](media/storage-files-how-to-create-large-file-share/large-file-shares-create-share.png)

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Använd följande kommando för att skapa en stor fil resurs. Ersätt `<yourStorageAccountName>` , `<yourStorageAccountKey>` och `<yourFileShareName>` med din information.

```azurecli-interactive
az storage share create --account-name <yourStorageAccountName> --account-key <yourStorageAccountKey> --name <yourFileShareName>
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Använd följande kommando för att skapa en stor fil resurs. Ersätt `<YourStorageAccountName>` , `<YourStorageAccountKey>` och `<YourStorageAccountFileShareName>` med din information.

```powershell
##Config
$storageAccountName = "<YourStorageAccountName>"
$storageAccountKey = "<YourStorageAccountKey>"
$shareName="<YourStorageAccountFileShareName>"
$ctx = New-AzStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey
New-AzStorageShare -Name $shareName -Context $ctx
```
---

## <a name="expand-existing-file-shares"></a>Expandera befintliga fil resurser

När du har aktiverat stora fil resurser på ditt lagrings konto kan du också expandera befintliga fil resurser i det kontot till den högre kvoten. 

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. Välj **fil resurser** från ditt lagrings konto.
1. Högerklicka på fil resursen och välj sedan **kvot**.
1. Ange den nya storlek som du vill ha och välj sedan **OK**.

![Azure Portal gränssnittet med kvoten för befintliga fil resurser](media/storage-files-how-to-create-large-file-share/update-large-file-share-quota.png)

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Använd följande kommando för att ange kvoten till maximal storlek. Ersätt `<yourStorageAccountName>` , `<yourStorageAccountKey>` och `<yourFileShareName>` med din information.

```azurecli-interactive
az storage share update --account-name <yourStorageAccountName> --account-key <yourStorageAccountKey> --name <yourFileShareName> --quota 102400
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Använd följande kommando för att ange kvoten till maximal storlek. Ersätt `<YourStorageAccountName>` , `<YourStorageAccountKey>` och `<YourStorageAccountFileShareName>` med din information.

```powershell
##Config
$storageAccountName = "<YourStorageAccountName>"
$storageAccountKey = "<YourStorageAccountKey>"
$shareName="<YourStorageAccountFileShareName>"
$ctx = New-AzStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey
# update quota
Set-AzStorageShareQuota -ShareName $shareName -Context $ctx -Quota 102400
```
---

## <a name="next-steps"></a>Nästa steg

* [Ansluta och montera en fil resurs i Windows](storage-how-to-use-files-windows.md)
* [Ansluta och montera en fil resurs på Linux](storage-how-to-use-files-linux.md)
* [Ansluta och montera en fil resurs på macOS](storage-how-to-use-files-mac.md)