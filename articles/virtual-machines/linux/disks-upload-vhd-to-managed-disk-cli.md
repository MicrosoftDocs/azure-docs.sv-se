---
title: Ladda upp en virtuell hård disk till Azure eller kopiera en disk i flera regioner – Azure CLI
description: Lär dig hur du laddar upp en virtuell hård disk till en Azure-hanterad disk och kopierar en hanterad disk mellan regioner med hjälp av Azure CLI via direkt uppladdning.
services: virtual-machines,storage
author: roygara
ms.author: rogarana
ms.date: 06/15/2020
ms.topic: how-to
ms.service: virtual-machines
ms.subservice: disks
ms.openlocfilehash: 473e87904742395eca6b7eeba0875cd93789104d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "91978993"
---
# <a name="upload-a-vhd-to-azure-or-copy-a-managed-disk-to-another-region---azure-cli"></a>Ladda upp en virtuell hård disk till Azure eller kopiera en hanterad disk till en annan region – Azure CLI

[!INCLUDE [disks-upload-vhd-to-disk-intro](../../../includes/disks-upload-vhd-to-disk-intro.md)]

## <a name="prerequisites"></a>Förutsättningar

- Ladda ned den senaste [versionen av AzCopy v10](../../storage/common/storage-use-azcopy-v10.md#download-and-install-azcopy).
- [Installera Azure CLI](/cli/azure/install-azure-cli).
- Om du tänker Ladda upp en virtuell hård disk från en lokal plats: en fast storleks-VHD som [har förberetts för Azure](../windows/prepare-for-upload-vhd-image.md), lagrad lokalt.
- Eller en hanterad disk i Azure om du vill utföra en kopierings åtgärd.

## <a name="getting-started"></a>Komma igång

Om du föredrar att ladda upp diskar via ett grafiskt användar gränssnitt kan du göra det med hjälp av Azure Storage Explorer. Mer information finns i: [använda Azure Storage Explorer för att hantera Azure Managed disks](../disks-use-storage-explorer-managed-disks.md)

Om du vill överföra din virtuella hård disk till Azure måste du skapa en tom hanterad disk som har kon figurer ATS för den här överförings processen. Innan du skapar en sådan finns det ytterligare information som du bör känna till om de här diskarna.

Den här typen av hanterade diskar har två unika tillstånd:

- ReadToUpload, vilket innebär att disken är redo att ta emot en uppladdning, men [att ingen säker åtkomst-signatur](../../storage/common/storage-sas-overview.md) (SAS) har genererats.
- ActiveUpload, vilket innebär att disken är redo att ta emot en uppladdning och att SAS har genererats.

> [!NOTE]
> I något av dessa tillstånd debiteras den hanterade disken med [standard priset för hård](https://azure.microsoft.com/pricing/details/managed-disks/)diskar, oavsett vilken typ av disk som används. Till exempel kommer en P10 att faktureras som en S10. Detta är sant tills `revoke-access` anropas på den hanterade disken, vilket krävs för att ansluta disken till en virtuell dator.

## <a name="create-an-empty-managed-disk"></a>Skapa en tom hanterad disk

Innan du kan skapa en tom standard hård disk för uppladdning behöver du fil storleken på den virtuella hård disk som du vill ladda upp, i byte. För att få det kan du använda antingen `wc -c <yourFileName>.vhd` eller `ls -al <yourFileName>.vhd` . Det här värdet används när du anger parametern **--upload-size-bytes** .

Skapa en tom standard hård disk för uppladdning genom att ange både parametrarna **-– för-upload** och parametern **--upload-size-byte** i en [disk Create](/cli/azure/disk#az-disk-create) -cmdlet:

Ersätt `<yourdiskname>` , `<yourresourcegroupname>` , `<yourregion>` med värden som du väljer. `--upload-size-bytes`Parametern innehåller ett exempel värde för `34359738880` och ersätter det med ett lämpligt värde.

> [!TIP]
> Om du skapar en operativ system disk lägger du till--Hyper-v-generation <yourGeneration> till `az disk create` .

```azurecli
az disk create -n <yourdiskname> -g <yourresourcegroupname> -l <yourregion> --for-upload --upload-size-bytes 34359738880 --sku standard_lrs
```

Om du vill ladda upp antingen en Premium SSD eller en standard SSD ersätter du **standard_lrs** med antingen **premium_LRS** eller **standardssd_lrs**. Ultra disks stöds inte för tillfället.

Nu när du har skapat en tom hanterad disk som är konfigurerad för överförings processen kan du ladda upp en virtuell hård disk till den. Om du vill ladda upp en virtuell hård disk till disken behöver du en skrivbar SAS, så att du kan referera till den som mål för överföringen.

Om du vill generera en skrivbar SAS av din tomma hanterade disk ersätter du `<yourdiskname>` och och `<yourresourcegroupname>` använder sedan följande kommando:

```azurecli
az disk grant-access -n <yourdiskname> -g <yourresourcegroupname> --access-level Write --duration-in-seconds 86400
```

Exempel på returnerat värde:

```output
{
  "accessSas": "https://md-impexp-t0rdsfgsdfg4.blob.core.windows.net/w2c3mj0ksfgl/abcd?sv=2017-04-17&sr=b&si=600a9281-d39e-4cc3-91d2-923c4a696537&sig=xXaT6mFgf139ycT87CADyFxb%2BnPXBElYirYRlbnJZbs%3D"
}
```

## <a name="upload-a-vhd"></a>Ladda upp en virtuell hårddisk

Nu när du har en SAS för din tomma hanterade disk kan du använda den för att ange den hanterade disken som mål för ditt upload-kommando.

Använd AzCopy v10 för att ladda upp din lokala VHD-fil till en hanterad disk genom att ange den SAS-URI som du skapade.

Den här uppladdningen har samma data flöde som motsvarande [standard-hårddisk](../disks-types.md#standard-hdd). Om du till exempel har en storlek som motsvarar S4, kommer du att ha ett data flöde på upp till 60 MiB/s. Men om du har en storlek som motsvarar S70 har du ett data flöde på upp till 500 MiB/s.

```bash
AzCopy.exe copy "c:\somewhere\mydisk.vhd" "sas-URI" --blob-type PageBlob
```

När uppladdningen är klar och du inte längre behöver skriva mer data till disken ska du återkalla SAS. Att återkalla SAS ändrar statusen för den hanterade disken och låter dig ansluta disken till en virtuell dator.

Ersätt `<yourdiskname>` och och `<yourresourcegroupname>` Använd sedan följande kommando för att göra disken användbar:

```azurecli
az disk revoke-access -n <yourdiskname> -g <yourresourcegroupname>
```

## <a name="copy-a-managed-disk"></a>Kopiera en hanterad disk

Direkt överföring fören klar också processen att kopiera en hanterad disk. Du kan antingen kopiera inom samma region eller mellan regioner (till en annan region).

Följ skriptet gör detta åt dig, processen liknar de steg som beskrivs ovan, med vissa skillnader eftersom du arbetar med en befintlig disk.

> [!IMPORTANT]
> Du måste lägga till en förskjutning på 512 när du tillhandahåller disk storleken i byte för en hanterad disk från Azure. Detta beror på att Azure utelämnar sidfoten när den returnerar disk storleken. Kopieringen Miss kommer om du inte gör det. Följande skript använder redan det här.

Ersätt `<sourceResourceGroupHere>` , `<sourceDiskNameHere>` ,, `<targetDiskNameHere>` `<targetResourceGroupHere>` och `<yourTargetLocationHere>` (ett exempel på ett plats värde är uswest2) med dina värden och kör sedan följande skript för att kopiera en hanterad disk.

> [!TIP]
> Om du skapar en operativ system disk lägger du till--Hyper-v-generation <yourGeneration> till `az disk create` .

```azurecli
sourceDiskName=<sourceDiskNameHere>
sourceRG=<sourceResourceGroupHere>
targetDiskName=<targetDiskNameHere>
targetRG=<targetResourceGroupHere>
targetLocation=<yourTargetLocationHere>

sourceDiskSizeBytes=$(az disk show -g $sourceRG -n $sourceDiskName --query '[diskSizeBytes]' -o tsv)

az disk create -g $targetRG -n $targetDiskName -l $targetLocation --for-upload --upload-size-bytes $(($sourceDiskSizeBytes+512)) --sku standard_lrs

targetSASURI=$(az disk grant-access -n $targetDiskName -g $targetRG  --access-level Write --duration-in-seconds 86400 -o tsv)

sourceSASURI=$(az disk grant-access -n $sourceDiskName -g $sourceRG --duration-in-seconds 86400 --query [accessSas] -o tsv)

.\azcopy copy $sourceSASURI $targetSASURI --blob-type PageBlob

az disk revoke-access -n $sourceDiskName -g $sourceRG

az disk revoke-access -n $targetDiskName -g $targetRG
```

## <a name="next-steps"></a>Nästa steg

Nu när du har laddat upp en virtuell hård disk till en hanterad disk kan du koppla disken som en [datadisk till en befintlig virtuell dator](add-disk.md) eller [ansluta disken till en virtuell dator som en operativ system disk](upload-vhd.md#create-the-vm)för att skapa en ny virtuell dator.