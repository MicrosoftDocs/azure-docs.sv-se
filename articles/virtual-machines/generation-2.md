---
title: Azure-stöd för virtuella datorer i generation 2
description: Översikt över Azure-stöd för virtuella datorer i generation 2
author: ju-shim
ms.service: virtual-machines
ms.subservice: sizes
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 08/28/2020
ms.author: jushiman
ms.openlocfilehash: 61897a790da8a5f52b1b8f8e208629e7755690fa
ms.sourcegitcommit: 6cca6698e98e61c1eea2afea681442bd306487a4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/24/2020
ms.locfileid: "97759746"
---
# <a name="support-for-generation-2-vms-on-azure"></a>Stöd för virtuella datorer ur generation 2 i Azure

Stöd för virtuella datorer i generation 2 (VM) är nu tillgängligt på Azure. Du kan inte ändra en virtuell dators generation när du har skapat den, så Läs igenom övervägandena på den här sidan innan du väljer en generation.

Virtuella datorer i generation 2 stöder viktiga funktioner som inte stöds i virtuella datorer i generation 1. Dessa funktioner omfattar ökat minne, Intel Software Guard-tillägg (Intel SGX) och virtualiserat beständigt minne (vPMEM). Generation 2 virtuella datorer som körs lokalt, har vissa funktioner som inte stöds i Azure än. Mer information finns i avsnittet [funktioner och funktioner](#features-and-capabilities) .

Virtuella datorer i generation 2 använder den nya UEFI-baserade start arkitekturen i stället för den BIOS-baserade arkitekturen som används av virtuella datorer i generation 1. Jämfört med virtuella datorer i generation 1 kan virtuella datorer i generation 2 ha förbättrade start-och installations tider. En översikt över virtuella datorer i generation 2 och några av skillnaderna mellan generation 1 och generation 2 finns i [ska jag skapa en virtuell dator i generation 1 eller 2 i Hyper-V?](/windows-server/virtualization/hyper-v/plan/should-i-create-a-generation-1-or-2-virtual-machine-in-hyper-v).

## <a name="generation-2-vm-sizes"></a>Generation 2 VM-storlekar

Virtuella datorer i generation 1 stöds av alla VM-storlekar i Azure (förutom för virtuella datorer i Mv2-serien). Azure erbjuder nu generation 2-stöd för följande valda VM-serien:


* [B-serien](sizes-b-series-burstable.md)
* [DCsv2-serien](dcv2-series.md)
* [DSv2-serien](dv2-dsv2-series.md)
* [Dsv3-serien](dv3-dsv3-series.md)
* [Dsv4-serien](dv4-dsv4-series.md)
* [Dasv4-serien](dav4-dasv4-series.md)
* [Ddsv4-serien](ddv4-ddsv4-series.md)
* [Esv3-serien](ev3-esv3-series.md)
* [Esv4-serien](ev4-esv4-series.md)
* [Easv4-serien](eav4-easv4-series.md)
* [Edsv4-serien](edv4-edsv4-series.md)
* [Fsv2-serien](fsv2-series.md)
* [GS-serien](sizes-previous-gen.md#gs-series)
* [HB-serien](hb-series.md)
* [HC-serien](hc-series.md)
* [LS-serien](sizes-previous-gen.md#ls-series) 
* [Lsv2-serien](lsv2-series.md)
* [M-serien](m-series.md)
* [Mv2-serie](mv2-series.md)<sup>1</sup>
* [NCv2-serien](ncv2-series.md) 
* [NCv3-serien](ncv3-series.md)
* [ND-serien](nd-series.md)
* [NVv3-serien](nvv3-series.md)
* [NVv4-serien](nvv4-series.md)
* [NCasT4_v3-serien](nct4-v3-series.md)

<sup>1</sup> Mv2-serien stöder inte VM-avbildningar av generation 1 och stöder endast en delmängd av generation 2-avbildningar. Mer information finns i [dokumentationen för Mv2-serien](mv2-series.md) .


## <a name="generation-2-vm-images-in-azure-marketplace"></a>Generation 2 VM-avbildningar på Azure Marketplace

Virtuella datorer i generation 2 stöder följande Marketplace-avbildningar:

* Windows Server 2019, 2016, 2012 R2, 2012
* Windows 10 Pro, Windows 10 Enterprise
* SUSE Linux Enterprise Server 15 SP1
* SUSE Linux Enterprise Server 12 SP4
* Ubuntu Server 16,04, 18,04, 19,04, 19,10 
* RHEL 8,1, 8,0, 7,7, 7,6, 7,5, 7,4, 7,0
* % OS 8,1, 8,0, 7,7, 7,6, 7,5, 7,4
* Oracle Linux 7,7, 7,7 – CI

> [!NOTE]
> Vissa storlekar på virtuella datorer som Mv2-Series kanske bara har stöd för en delmängd av dessa avbildningar. mer information finns i dokumentationen för den aktuella virtuella datorns storlek.

## <a name="on-premises-vs-azure-generation-2-vms"></a>Lokala eller Azure generation 2-datorer

Azure stöder för närvarande inte några av de funktioner som lokala Hyper-V stöder för virtuella datorer i generation 2.

| Generation 2-funktion                | Lokal Hyper-V | Azure |
|-------------------------------------|---------------------|-------|
| Säker start                         | :heavy_check_mark:  | röntgen   |
| Avskärmad virtuell dator                         | :heavy_check_mark:  | röntgen   |
| vTPM                                | :heavy_check_mark:  | röntgen   |
| Virtualiseringsbaserad säkerhet (VBS) | :heavy_check_mark:  | röntgen   |
| VHDX-format                         | :heavy_check_mark:  | röntgen   |

## <a name="features-and-capabilities"></a>Funktioner

### <a name="generation-1-vs-generation-2-features"></a>Generation 1 vs. generation 2-funktioner

| Funktion | Generation 1 | Generation 2 |
|---------|--------------|--------------|
| Start             | PCAT                      | UEFI                               |
| Disk styrenheter | IDE                       | SCSI                               |
| VM-storlekar         | Alla VM-storlekar | [Se tillgängliga storlekar](#generation-2-vm-sizes) |

### <a name="generation-1-vs-generation-2-capabilities"></a>Funktioner för generation 1 vs. generation 2

| Funktion | Generation 1 | Generation 2 |
|------------|--------------|--------------|
| OS-disk > 2 TB                    | röntgen                | :heavy_check_mark: |
| Anpassad disk/avbildning/växla OS         | :heavy_check_mark: | :heavy_check_mark: |
| Stöd för skalnings uppsättning för virtuell dator | :heavy_check_mark: | :heavy_check_mark: |
| Azure Site Recovery               | :heavy_check_mark: | :heavy_check_mark: |
| Säkerhetskopiera och återställa                    | :heavy_check_mark: | :heavy_check_mark: |
| Galleri för delad avbildning              | :heavy_check_mark: | :heavy_check_mark: |
| [Azure Disk Encryption](../security/fundamentals/azure-disk-encryption-vms-vmss.md)             | :heavy_check_mark: | röntgen                |
| [Kryptering på serversidan](disk-encryption.md)            | :heavy_check_mark: | :heavy_check_mark: |

## <a name="creating-a-generation-2-vm"></a>Skapar en virtuell dator i generation 2

### <a name="marketplace-image"></a>Marketplace-avbildning

I Azure Portal eller Azure CLI kan du skapa virtuella datorer i generation 2 från en Marketplace-avbildning som stöder UEFI-start.

#### <a name="azure-portal"></a>Azure Portal

Nedan visas stegen för att skapa en virtuell dator i generation 2 (Gen2) i Azure Portal.

1. Logga in på Azure Portal på https://portal.azure.com.
1. Välj **Skapa en resurs**.
1. Klicka på **Visa alla** från Azure Marketplace till vänster.
1. Välj en avbildning som stöder Gen2.
1. Klicka på **Skapa**.
1. På fliken **Avancerat** under avsnittet VM- **generering** väljer du alternativet **gen 2** .
1. På fliken **grundläggande** , under **information om instans**, går du till **storlek** och öppnar bladet **Välj en VM-storlek** .
1. Välj en [virtuell dator i generation 2 som stöds](#generation-2-vm-sizes).
1. Gå igenom resten av sidorna för att slutföra skapandet av den virtuella datorn.

![Välj gen 1 eller gen 2 VM](./media/generation-2/gen1-gen2-select.png)

#### <a name="powershell"></a>PowerShell

Du kan också använda PowerShell för att skapa en virtuell dator genom att referera till SKU för generation 1 eller generation 2.

Använd till exempel följande PowerShell-cmdlet för att hämta en lista över SKU: er i `WindowsServer` erbjudandet.

```powershell
Get-AzVMImageSku -Location westus2 -PublisherName MicrosoftWindowsServer -Offer WindowsServer
```

Om du skapar en virtuell dator med Windows Server 2012 som operativ system väljer du antingen generation 1 (BIOS) eller generation 2 (UEFI) VM SKU, som ser ut så här:

```powershell
2012-Datacenter
2012-datacenter-gensecond
```

I avsnittet [funktioner och funktioner](#features-and-capabilities) finns en aktuell lista över Marketplace-avbildningar som stöds.

#### <a name="azure-cli"></a>Azure CLI

Du kan också använda Azure CLI för att se alla tillgängliga generation 2-avbildningar som listas av **Publisher**.

```azurecli
az vm image list --publisher Canonical --sku gen2 --output table --all
```

### <a name="managed-image-or-managed-disk"></a>Hanterad avbildning eller hanterad disk

Du kan skapa en virtuell dator i generation 2 från en hanterad avbildning eller en hanterad disk på samma sätt som du skapar en virtuell dator i generation 1.

### <a name="virtual-machine-scale-sets"></a>Skalningsuppsättningar för virtuella datorer

Du kan också skapa virtuella datorer i generation 2 med hjälp av skalnings uppsättningar för virtuella datorer. I Azure CLI använder du Azure Scale set för att skapa virtuella datorer i generation 2.

## <a name="frequently-asked-questions"></a>Vanliga frågor och svar

* **Är generation 2 virtuella datorer tillgängliga i alla Azure-regioner?**  
    Ja. Men alla [VM-storlekar i generation 2](#generation-2-vm-sizes) är inte tillgängliga i varje region. Tillgängligheten för den virtuella datorn i generation 2 beror på tillgängligheten för VM-storleken.

* **Finns det en pris skillnad mellan virtuella datorer i generation 1 och generation 2?**  
   Nej.

* **Jag har en. VHD-fil från min lokala generation 2 VM. Kan jag använda den. VHD-filen för att skapa en generation 2 VM i Azure?**
  Ja, du kan ta din generations 2. VHD-fil till Azure och använda den för att skapa en virtuell dator i generation 2. Använd följande steg för att göra det:
    1. Överför. VHD till ett lagrings konto i samma region där du vill skapa den virtuella datorn.
    1. Skapa en hanterad disk från VHD-filen. Ställ in Hyper-V generations egenskapen på v2. Följande PowerShell-kommandon ställer in Hyper-V generations-egenskapen när du skapar en hanterad disk.

        ```powershell
        $sourceUri = 'https://xyzstorage.blob.core.windows.net/vhd/abcd.vhd'. #<Provide location to your uploaded .vhd file>
        $osDiskName = 'gen2Diskfrmgenvhd'  #<Provide a name for your disk>
        $diskconfig = New-AzDiskConfig -Location '<location>' -DiskSizeGB 127 -AccountType Standard_LRS -OsType Windows -HyperVGeneration "V2" -SourceUri $sourceUri -CreateOption 'Import'
        New-AzDisk -DiskName $osDiskName -ResourceGroupName '<Your Resource Group>' -Disk $diskconfig
        ```

    1. När disken är tillgänglig skapar du en virtuell dator genom att koppla den här disken. Den virtuella datorn som skapas är en virtuell dator i generation 2.
    När generation 2 VM skapas kan du välja att generalisera avbildningen av den virtuella datorn. Genom att generalisera avbildningen kan du använda den för att skapa flera virtuella datorer.

* **Hur gör jag för att du öka storleken på operativ system disken?**  

  OS-diskar som är större än 2 TiB är nya för virtuella datorer i generation 2. Som standard är OS-diskar mindre än 2 TiB för virtuella datorer i generation 2. Du kan öka disk storleken upp till ett rekommenderat max 4-TiB. Använd Azure CLI eller Azure Portal för att öka storleken på operativ system disken. Information om hur du expanderar diskar program mässigt finns i **ändra storlek på en disk** för [Windows](./windows/expand-os-disk.md) eller [Linux](./linux/resize-os-disk-gpt-partition.md).

  För att öka storleken på operativ system disken från Azure Portal:

  1. Gå till sidan Egenskaper för virtuell dator i Azure Portal.
  1. Välj **stopp** -knappen om du vill stänga av och frigöra den virtuella datorn.
  1. I avsnittet **diskar** väljer du den OS-disk som du vill öka.
  1. I avsnittet **diskar** väljer du **konfiguration** och uppdaterar **storleken** till det värde som du vill använda.
  1. Gå tillbaka till sidan Egenskaper för virtuell dator och **Starta** den virtuella datorn.
  
  Du kan se en varning för OS-diskar som är större än 2 TiB. Varningen gäller inte för virtuella datorer i generation 2. OS-disk storlekar som är större än 4 TiB stöds dock inte.

* **Stöder virtuella datorer i generation 2 stöd för accelererat nätverk?**  
    Ja. Mer information finns i [skapa en virtuell dator med accelererat nätverk](../virtual-network/create-vm-accelerated-networking-cli.md).

* **Har generation 2-VM-datorer stöd för säker start eller vTPM i Azure?**
    Både virtuella datorer i generation 1 och generation 2 i Azure stöder inte säker start eller vTPM. 
    
* **Stöds VHDX i generation 2?**  
    Nej, generation 2-VM: ar stöder bara VHD.

* **Har virtuella datorer i generation 2 stöd för Azure Ultra Disklagring?**  
    Ja.

* **Kan jag migrera en virtuell dator från generation 1 till generation 2?**  
    Nej, du kan inte ändra genereringen av en virtuell dator när du har skapat den. Om du behöver växla mellan VM-generationer skapar du en ny virtuell dator med en annan generation.

* **Varför är min VM-storlek inte aktive rad i storleks väljaren när jag försöker skapa en virtuell Gen2-dator?**

    Detta kan lösas genom att göra följande:

    1. Kontrol lera att egenskapen **VM generation** har angetts till **gen 2** på fliken **Avancerat** .
    1. Kontrol lera att du söker efter en [VM-storlek som har stöd för virtuella Gen2-datorer](#generation-2-vm-sizes).

## <a name="next-steps"></a>Nästa steg

Lär dig mer om [virtuella datorer i generation 2 i Hyper-V](/windows-server/virtualization/hyper-v/plan/should-i-create-a-generation-1-or-2-virtual-machine-in-hyper-v).
