---
title: NP-serien – Azure Virtual Machines
description: Specifikationer för de virtuella datorerna i NP-serien.
author: vikancha-MSFT
ms.service: virtual-machines
ms.subservice: vm-sizes-gpu
ms.topic: conceptual
ms.date: 02/09/2021
ms.author: vikancha
ms.openlocfilehash: aa67a858d0396badc25a625b23dc2f2fdf1bdff9
ms.sourcegitcommit: b0557848d0ad9b74bf293217862525d08fe0fc1d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/07/2021
ms.locfileid: "106551381"
---
# <a name="np-series"></a>NP-serien 
De virtuella datorerna i NP-serien drivs av [Xilinx U250 ](https://www.xilinx.com/products/boards-and-kits/alveo/u250.html) -FPGAs för att påskynda arbets belastningar, inklusive maskin inlärnings störningar, video omkodning och databas sökning & analys. De virtuella datorerna i NP-serien drivs med Intel Xeon 8171M-processorer (Skylake) med all Core Turbo klock hastighet på 3,2 GHz.

[Premium Storage](premium-storage-performance.md): stöds<br>
[Premium Storage caching](premium-storage-performance.md): stöds<br>
[Direktmigrering](maintenance-and-updates.md): stöds inte<br>
[Minnes bebetjänings uppdateringar](maintenance-and-updates.md): stöds inte<br>
Stöd för VM-generering: generation 1<br>
[Accelererat nätverk](../virtual-network/create-vm-accelerated-networking-cli.md): stöds<br>
[Tillfälliga OS-diskar](ephemeral-os-disks.md): stöds inte <br>
<br>

| Storlek | Virtuell processor | Minne: GiB | Temporär lagring (SSD) GiB | FPGA | FPGA-minne: GiB | Maximalt antal datadiskar | Högsta antal nätverkskort/förväntad nätverks bandbredd (Mbit/s) | 
|---|---|---|---|---|---|---|---|
| Standard_NP10s | 10 | 168 | 736  | 1 | 64  | 8 | 1 / 7500 | 
| Standard_NP20s | 20 | 336 | 1474 | 2 | 128 | 16 | 2 / 15000 | 
| Standard_NP40s | 40 | 672 | 2948 | 4 | 256 | 32 | 4 / 30000 | 



[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]


##  <a name="frequently-asked-questions"></a>Vanliga frågor och svar

**F:** Vilken version av Vitis ska jag använda? 

**A:** Xilinx rekommenderar [Vitis 2020,2](https://www.xilinx.com/products/design-tools/vitis/vitis-platform.html)


**F:** Behöver jag använda NP-datorer för att utveckla min lösning? 

**A:** Nej, du kan utveckla lokalt och distribuera det till molnet! Kontrol lera att du följer attesterings dokumentationen för att distribuera på de virtuella datorerna i NP. 

**F:** Vilken version av XRT ska jag använda?

**A:** xrt_202020.2.8.832 

**F:** Vad är mål distributions plattformen?

**A:** Använd följande plattformar.
- Xilinx-u250-gen3x16-xdma-Platform-2.1-3_all
- Xilinx-u250-gen3x16-xdma-validate_2.1-3005608.1 

**F:** Vilken plattform ska jag ha för utveckling?

**A:** Xilinx-u250-gen3x16-xdma-2.1-202010-1-dev_1-2954688_all 

**F:** Vilka operativ system stöds? 

**A:** Xilinx och Microsoft har verifierat Ubuntu 18,04 LTS och CentOS 7,8.

 Xilinx har skapat följande Marketplace-avbildningar för att förenkla distributionen av de här virtuella datorerna. 

[Xilinx ALVEO U250 Deployment VM – Ubuntu 18.04](https://ms.portal.azure.com/#blade/Microsoft_Azure_Marketplace/GalleryItemDetailsBladeNopdl/id/xilinx.xilinx_alveo_u250_deployment_vm_ubuntu1804_032321)

[Xilinx ALVEO U250 Deployment VM – CentOS 7,8](https://ms.portal.azure.com/#blade/Microsoft_Azure_Marketplace/GalleryItemDetailsBladeNopdl/id/xilinx.xilinx_alveo_u250_deployment_vm_centos78_032321)

**F:** Kan jag distribuera mina egna Ubuntu/CentOS-datorer och installera XRT/distributions mål plattform? 

**S:** Ja.

**F:** Om jag distribuerar min egen Ubuntu 18.04 VM, vilka är de nödvändiga paketen och stegen?

**A:** Använda kernel 4.1 X per [Xilinx XRT-dokumentation](https://www.xilinx.com/support/documentation/sw_manuals/xilinx2020_2/ug1451-xrt-release-notes.pdf)
       
Installera följande paket.
- xrt_202020 xrt_202020.2.8.832_18.04-amd64-XRT. deb
       
- xrt_202020 xrt_202020.2.8.832_18.04-amd64-Azure. deb
       
- Xilinx-u250-gen3x16-xdma-Platform-2.1-3_all_18.04. deb. tar. gz
       
- Xilinx-u250-gen3x16-xdma-validate_2.1-3005608.1_all. deb  

**F:** Efter omstart av den virtuella datorn på Ubuntu kan jag inte hitta mina FPGA: 

**A:** Kontrol lera att din kernel inte har uppgraderats (uname-a). I så fall kan du nedgradera till kernel 4.1 X. 

**F:** Om jag distribuerar min egen CentOS 7,8 virtuell dator, vilka är de nödvändiga paketen och stegen?

**A:** Använd kernel-version: 3.10.0-1160.15.2.el7.x86_64

 Installera följande paket.
   
 - xrt_202020 xrt_202020.2.8.832_7.4.1708-x86_64-XRT. rpm 
      
 - xrt_202020 xrt_202020.2.8.832_7.4.1708-x86_64-Azure. rpm 
     
 - Xilinx-u250-gen3x16-xdma-Platform-2.1 -3. nobåge. rpm. tar. gz 
      
 - Xilinx-u250-gen3x16-xdma-validate-2.1 – 3005608.1. nobåge. rpm  

**F:** När du kör xbutil validate på CentOS får du den här varningen: "Varning: kernel-version 3.10.0-1160.15.2.el7.x86_64 stöds inte officiellt. 4.18.0-193 är den senaste versionen som stöds. " 

**A:** Detta kan ignoreras. 

**F:** Vad är skillnaden mellan OnPrem och NP-datorer angående XRT? 

**A:** På Azure stöder XDMA 2,1-plattformen endast Host_Mem (SB) och DDR-datakvarhållning. 

Aktivera Host_Mem (SB) (1 GB RAM): sudo xbutil host_mem--Enable--size 1G 

Så här inaktiverar du Host_Mem (SB): sudo xbutil host_mem--Disable 

**F:** Kan jag köra xbmgmt-kommandon? 

**A:** Nej, på virtuella Azure-datorer finns det inget hanterings stöd direkt från den virtuella Azure-datorn. 

 **F:** Måste jag läsa in en PLP? 

**A:** Nej, PLP läses in automatiskt åt dig, så det är inte nödvändigt att läsa in via xbmgmt-kommandon. 

 
**F:** Stöder Azure olika PLPs? 

**A:** Inte just nu. Vi stöder bara de PLP som tillhandahålls i distributions plattforms paketen. 

**F:** Hur kan jag fråga PLP-informationen? 

**A:** Du måste köra xbutil-frågan och titta på den nedre delen. 

## <a name="other-sizes"></a>Andra storlekar

- [Generell användning](sizes-general.md)
- [Minnesoptimerad](sizes-memory.md)
- [Lagringsoptimerad](sizes-storage.md)
- [GPU-optimerad](sizes-gpu.md)
- [Databehandling med höga prestanda](sizes-hpc.md)
- [Tidigare generationer](sizes-previous-gen.md)

## <a name="next-steps"></a>Nästa steg

Lär dig mer om hur [Azure Compute Units (ACU)](acu.md) kan hjälpa dig att jämföra beräknings prestanda i Azure SKU: er.
