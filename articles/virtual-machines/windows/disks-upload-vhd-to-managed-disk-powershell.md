---
title: Ladda upp en virtuell hård disk till Azure eller kopiera en disk i flera regioner – Azure PowerShell
description: Lär dig hur du laddar upp en virtuell hård disk till en Azure-hanterad disk och kopierar en hanterad disk över flera regioner med hjälp av Azure PowerShell via direkt uppladdning.
author: roygara
ms.author: rogarana
ms.date: 06/15/2020
ms.topic: how-to
ms.service: virtual-machines
ms.tgt_pltfrm: linux
ms.subservice: disks
ms.openlocfilehash: 1655c48eeb9227bf934c7fd9bb37610327b2b98c
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/23/2021
ms.locfileid: "98736279"
---
# <a name="upload-a-vhd-to-azure-or-copy-a-managed-disk-to-another-region---azure-powershell"></a>Ladda upp en virtuell hård disk till Azure eller kopiera en hanterad disk till en annan region – Azure PowerShell

[!INCLUDE [disks-upload-vhd-to-disk-intro](../../../includes/disks-upload-vhd-to-disk-intro.md)]

## <a name="prerequisites"></a>Krav

- Ladda ned den senaste [versionen av AzCopy v10](../../storage/common/storage-use-azcopy-v10.md#download-and-install-azcopy).
- [Installera Azure PowerShell-modul](/powershell/azure/install-Az-ps).
- Om du tänker Ladda upp en virtuell hård disk från en lokal plats: en fast storleks-VHD som [har förberetts för Azure](prepare-for-upload-vhd-image.md), lagrad lokalt.
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

Innan du kan skapa en tom standard hård disk för uppladdning behöver du fil storleken på den virtuella hård disk som du vill ladda upp, i byte. Exempel koden kommer att ge dig, men för att göra det själv kan du använda: `$vhdSizeBytes = (Get-Item "<fullFilePathHere>").length` . Det här värdet används när du anger parametern **-UploadSizeInBytes** .

I det lokala gränssnittet skapar du en tom standard hård disk för uppladdning genom att ange **uppladdnings** inställningen i parametern **-CreateOption** och parametern **-UploadSizeInBytes** i cmdleten [New-AzDiskConfig](/powershell/module/az.compute/new-azdiskconfig) . Anropa sedan [New-AzDisk](/powershell/module/az.compute/new-azdisk) för att skapa disken.

Ersätt `<yourdiskname>` , `<yourresourcegroupname>` och `<yourregion>` kör sedan följande kommandon:

> [!TIP]
> Om du skapar en OS-disk lägger du till-HyperVGeneration <yourGeneration> i `New-AzDiskConfig` .

```powershell
$vhdSizeBytes = (Get-Item "<fullFilePathHere>").length

$diskconfig = New-AzDiskConfig -SkuName 'Standard_LRS' -OsType 'Windows' -UploadSizeInBytes $vhdSizeBytes -Location '<yourregion>' -CreateOption 'Upload'

New-AzDisk -ResourceGroupName '<yourresourcegroupname' -DiskName '<yourdiskname>' -Disk $diskconfig
```

Om du vill ladda upp antingen en Premium SSD eller en standard SSD ersätter du **Standard_LRS** med antingen **Premium_LRS** eller **StandardSSD_LRS**. Ultra disks stöds inte ännu.

Nu när du har skapat en tom hanterad disk som är konfigurerad för överförings processen kan du ladda upp en virtuell hård disk till den. Om du vill ladda upp en virtuell hård disk till disken behöver du en skrivbar SAS, så att du kan referera till den som mål för överföringen.

Om du vill generera en skrivbar SAS av din tomma hanterade disk ersätter du `<yourdiskname>` och och `<yourresourcegroupname>` använder sedan följande kommandon:

```powershell
$diskSas = Grant-AzDiskAccess -ResourceGroupName '<yourresourcegroupname>' -DiskName '<yourdiskname>' -DurationInSecond 86400 -Access 'Write'

$disk = Get-AzDisk -ResourceGroupName '<yourresourcegroupname>' -DiskName '<yourdiskname>'
```

## <a name="upload-a-vhd"></a>Ladda upp en virtuell hårddisk

Nu när du har en SAS för din tomma hanterade disk kan du använda den för att ange den hanterade disken som mål för ditt upload-kommando.

Använd AzCopy v10 för att ladda upp din lokala VHD-fil till en hanterad disk genom att ange den SAS-URI som du skapade.

Den här uppladdningen har samma data flöde som motsvarande [standard-hårddisk](../disks-types.md#standard-hdd). Om du till exempel har en storlek som motsvarar S4, kommer du att ha ett data flöde på upp till 60 MiB/s. Men om du har en storlek som motsvarar S70 har du ett data flöde på upp till 500 MiB/s.

```
AzCopy.exe copy "c:\somewhere\mydisk.vhd" $diskSas.AccessSAS --blob-type PageBlob
```

När uppladdningen är klar och du inte längre behöver skriva mer data till disken ska du återkalla SAS. Att återkalla SAS ändrar statusen för den hanterade disken och låter dig ansluta disken till en virtuell dator.

Ersätt `<yourdiskname>` och och `<yourresourcegroupname>` kör sedan följande kommando:

```powershell
Revoke-AzDiskAccess -ResourceGroupName '<yourresourcegroupname>' -DiskName '<yourdiskname>'
```

## <a name="copy-a-managed-disk"></a>Kopiera en hanterad disk

Direkt överföring fören klar också processen att kopiera en hanterad disk. Du kan antingen kopiera inom samma region eller kopiera din hanterade disk till en annan region.

Följ skriptet gör detta åt dig, processen liknar de steg som beskrivs ovan, med vissa skillnader, eftersom du arbetar med en befintlig disk.

> [!IMPORTANT]
> Du måste lägga till en förskjutning på 512 när du tillhandahåller disk storleken i byte för en hanterad disk från Azure. Detta beror på att Azure utelämnar sidfoten när den returnerar disk storleken. Kopieringen Miss kommer om du inte gör det. Följande skript använder redan det här.

Ersätt `<sourceResourceGroupHere>` , `<sourceDiskNameHere>` ,, `<targetDiskNameHere>` `<targetResourceGroupHere>` `<yourOSTypeHere>` och `<yourTargetLocationHere>` (ett exempel på ett plats värde är uswest2) med dina värden och kör sedan följande skript för att kopiera en hanterad disk.

> [!TIP]
> Om du skapar en OS-disk lägger du till-HyperVGeneration <yourGeneration> i `New-AzDiskConfig` .

```powershell

$sourceRG = <sourceResourceGroupHere>
$sourceDiskName = <sourceDiskNameHere>
$targetDiskName = <targetDiskNameHere>
$targetRG = <targetResourceGroupHere>
$targetLocate = <yourTargetLocationHere>
#Expected value for OS is either "Windows" or "Linux"
$targetOS = <yourOSTypeHere>

$sourceDisk = Get-AzDisk -ResourceGroupName $sourceRG -DiskName $sourceDiskName

# Adding the sizeInBytes with the 512 offset, and the -Upload flag
$targetDiskconfig = New-AzDiskConfig -SkuName 'Standard_LRS' -osType $targetOS -UploadSizeInBytes $($sourceDisk.DiskSizeBytes+512) -Location $targetLocate -CreateOption 'Upload'

$targetDisk = New-AzDisk -ResourceGroupName $targetRG -DiskName $targetDiskName -Disk $targetDiskconfig

$sourceDiskSas = Grant-AzDiskAccess -ResourceGroupName $sourceRG -DiskName $sourceDiskName -DurationInSecond 86400 -Access 'Read'

$targetDiskSas = Grant-AzDiskAccess -ResourceGroupName $targetRG -DiskName $targetDiskName -DurationInSecond 86400 -Access 'Write'

azcopy copy $sourceDiskSas.AccessSAS $targetDiskSas.AccessSAS --blob-type PageBlob

Revoke-AzDiskAccess -ResourceGroupName $sourceRG -DiskName $sourceDiskName

Revoke-AzDiskAccess -ResourceGroupName $targetRG -DiskName $targetDiskName 
```

## <a name="next-steps"></a>Nästa steg

Nu när du har laddat upp en virtuell hård disk till en hanterad disk kan du ansluta disken till en virtuell dator och börja använda den.

Information om hur du ansluter en datadisk till en virtuell dator finns i vår artikel om ämnet: [koppla en datadisk till en virtuell Windows-dator med PowerShell](attach-disk-ps.md). Om du vill använda disken som operativ system disk, se [skapa en virtuell Windows-dator från en specialiserad disk](create-vm-specialized.md#create-the-new-vm).
