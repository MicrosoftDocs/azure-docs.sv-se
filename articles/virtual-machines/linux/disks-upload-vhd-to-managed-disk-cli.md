---
title: Ladda upp en virtuell hårddisk till Azure eller kopiera en disk mellan regioner – Azure CLI
description: Lär dig hur du laddar upp en virtuell hårddisk till en hanterad Azure-disk och kopierar en hanterad disk mellan regioner med hjälp av Azure CLI via direktuppladdning.
services: virtual-machines,storage
author: roygara
ms.author: rogarana
ms.date: 06/15/2020
ms.topic: how-to
ms.service: virtual-machines
ms.subservice: disks
ms.openlocfilehash: 285f0acd5097ce68cddee6f732b17944dffb0eba
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107762577"
---
# <a name="upload-a-vhd-to-azure-or-copy-a-managed-disk-to-another-region---azure-cli"></a>Ladda upp en virtuell hårddisk till Azure eller kopiera en hanterad disk till en annan region – Azure CLI

[!INCLUDE [disks-upload-vhd-to-disk-intro](../../../includes/disks-upload-vhd-to-disk-intro.md)]

## <a name="prerequisites"></a>Förutsättningar

- Ladda ned den [senaste versionen av AzCopy v10](../../storage/common/storage-use-azcopy-v10.md#download-and-install-azcopy).
- [Installera Azure CLI.](/cli/azure/install-azure-cli)
- Om du planerar att ladda upp en virtuell hårddisk från en lokal plats: En vhd med fast storlek som har förberetts [för Azure](../windows/prepare-for-upload-vhd-image.md)lagras lokalt.
- Eller en hanterad disk i Azure om du planerar att utföra en kopieringsåtgärd.

## <a name="getting-started"></a>Komma igång

Om du föredrar att ladda upp diskar via ett grafiskt användargränssnitt kan du göra det med Azure Storage Explorer. Mer information finns i: [Använda Azure Storage Explorer för att hantera Azure-hanterade diskar](../disks-use-storage-explorer-managed-disks.md)

Om du vill ladda upp den virtuella hårddisken till Azure måste du skapa en tom hanterad disk som är konfigurerad för den här uppladdningsprocessen. Innan du skapar en finns det ytterligare information som du bör känna till om dessa diskar.

Den här typen av hanterad disk har två unika tillstånd:

- ReadToUpload, vilket innebär att disken är redo att ta emot en uppladdning men att ingen signatur [för](../../storage/common/storage-sas-overview.md) säker åtkomst (SAS) har genererats.
- ActiveUpload, vilket innebär att disken är redo att ta emot en uppladdning och att SAS har genererats.

> [!NOTE]
> I båda dessa tillstånd debiteras den hanterade disken enligt standardpris för [HDD,](https://azure.microsoft.com/pricing/details/managed-disks/)oavsett vilken typ av disk det gäller. Till exempel debiteras en P10 som en S10. Detta gäller tills `revoke-access` anropas på den hanterade disken, vilket krävs för att ansluta disken till en virtuell dator.

## <a name="create-an-empty-managed-disk"></a>Skapa en tom hanterad disk

Innan du kan skapa en tom standard-HDD för uppladdning behöver du filstorleken för den virtuella hårddisk som du vill ladda upp i byte. Du kan hämta det genom att använda antingen `wc -c <yourFileName>.vhd` eller `ls -al <yourFileName>.vhd` . Det här värdet används när du anger **parametern --upload-size-bytes.**

Skapa en tom standard hdd för uppladdning genom att ange både parametern **--for-upload** och parametern **--upload-size-bytes** i en cmdlet [för](/cli/azure/disk#az_disk_create) disk create:

Ersätt `<yourdiskname>` , , med värden som du väljer `<yourresourcegroupname>` `<yourregion>` själv. Parametern `--upload-size-bytes` innehåller exempelvärdet `34359738880` , ersätt det med ett värde som passar dig.

> [!TIP]
> Om du skapar en OS-disk lägger du till --hyper-v-generation <yourGeneration> i `az disk create` .

```azurecli
az disk create -n <yourdiskname> -g <yourresourcegroupname> -l <yourregion> --for-upload --upload-size-bytes 34359738880 --sku standard_lrs
```

Om du vill ladda upp antingen en Premium SSD eller en STANDARD SSD ersätter du **standard_lrs** med **antingen premium_LRS** eller **standardssd_lrs**. Ultradiskar stöds inte för tillfället.

Nu när du har skapat en tom hanterad disk som är konfigurerad för uppladdningsprocessen kan du ladda upp en virtuell hårddisk till den. Om du vill ladda upp en virtuell hårddisk till disken behöver du en skrivbar SAS så att du kan referera till den som mål för uppladdningen.

Om du vill generera en skrivbar SAS för den tomma hanterade disken ersätter du `<yourdiskname>` och och använder sedan följande `<yourresourcegroupname>` kommando:

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

Nu när du har en SAS för den tomma hanterade disken kan du använda den för att ange den hanterade disken som mål för uppladdningskommandot.

Använd AzCopy v10 för att ladda upp din lokala VHD-fil till en hanterad disk genom att ange den SAS-URI som du genererade.

Den här uppladdningen har samma dataflöde som motsvarande [standard HDD.](../disks-types.md#standard-hdd) Om du till exempel har en storlek som motsvarar S4 har du ett dataflöde på upp till 60 MiB/s. Men om du har en storlek som motsvarar S70 har du ett dataflöde på upp till 500 MiB/s.

```bash
AzCopy.exe copy "c:\somewhere\mydisk.vhd" "sas-URI" --blob-type PageBlob
```

När uppladdningen är klar och du inte längre behöver skriva mer data till disken återkallar du SAS. Om du återkallar SAS ändras tillståndet för den hanterade disken och du kan ansluta disken till en virtuell dator.

Ersätt `<yourdiskname>` och och använd sedan följande kommando för att göra disken `<yourresourcegroupname>` användbar:

```azurecli
az disk revoke-access -n <yourdiskname> -g <yourresourcegroupname>
```

## <a name="copy-a-managed-disk"></a>Kopiera en hanterad disk

Direktuppladdning förenklar också kopieringen av en hanterad disk. Du kan antingen kopiera inom samma region eller mellan regioner (till en annan region).

Följande skript gör detta åt dig. Processen liknar stegen som beskrivs ovan, med vissa skillnader eftersom du arbetar med en befintlig disk.

> [!IMPORTANT]
> Du måste lägga till en förskjutning på 512 när du tillhandahåller diskstorleken i byte för en hanterad disk från Azure. Det beror på att Azure utelämnar sidfoten när diskstorleken returneras. Kopieringen misslyckas om du inte gör detta. Följande skript gör redan detta åt dig.

Ersätt , , , och (ett exempel på ett platsvärde är `<sourceResourceGroupHere>` `<sourceDiskNameHere>` `<targetDiskNameHere>` `<targetResourceGroupHere>` uswest2) med dina värden och kör sedan följande skript för att `<yourTargetLocationHere>` kopiera en hanterad disk.

> [!TIP]
> Om du skapar en OS-disk lägger du till --hyper-v-generation <yourGeneration> i `az disk create` .

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

Nu när du har överfört en virtuell hårddisk till en hanterad disk kan du koppla disken som en [datadisk](add-disk.md) till en befintlig virtuell dator eller ansluta disken till en virtuell dator som en [OS-disk](upload-vhd.md#create-the-vm)för att skapa en ny virtuell dator.