---
title: NP-serien – Azure Virtual Machines
description: Specifikationer för virtuella datorer i NP-serien.
author: vikancha-MSFT
ms.service: virtual-machines
ms.subservice: vm-sizes-gpu
ms.topic: conceptual
ms.date: 02/09/2021
ms.author: vikancha
ms.openlocfilehash: 61488b88b00206cb78beed4fe773bf9377848701
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2021
ms.locfileid: "107861057"
---
# <a name="np-series"></a>NP-serien 
De virtuella datorerna i NP-serien drivs av [Xilinx U250 ](https://www.xilinx.com/products/boards-and-kits/alveo/u250.html) FPGA:er för att påskynda arbetsbelastningar som inferens av maskininlärning, videotranskodning och & analys. Virtuella datorer i NP-serien drivs också av Intel Xeon 8171M-processorer (Skylake) med en hastighet på 3,2 GHz för alla kärnor med turboklocka.

[Premium Storage](premium-storage-performance.md): Stöds<br>
[Premium Storage cachelagring:](premium-storage-performance.md)Stöds<br>
[direktmigrering:](maintenance-and-updates.md)Stöds inte<br>
[Minnesbevarande uppdateringar:](maintenance-and-updates.md)Stöds inte<br>
Stöd för VM-generering: Generation 1<br>
[Accelererat nätverk:](../virtual-network/create-vm-accelerated-networking-cli.md)Stöds<br>
[Tillfälliga OS-diskar:](ephemeral-os-disks.md)Stöds inte <br>
<br>

| Storlek | Virtuell processor | Minne: GiB | Temporär lagring (SSD) GiB | FPGA | FPGA-minne: GiB | Maximalt antal datadiskar | Maximalt antal nätverkskort/förväntad nätverksbandbredd (MBps) | 
|---|---|---|---|---|---|---|---|
| Standard_NP10s | 10 | 168 | 736  | 1 | 64  | 8 | 1 / 7500 | 
| Standard_NP20s | 20 | 336 | 1474 | 2 | 128 | 16 | 2 / 15000 | 
| Standard_NP40s | 40 | 672 | 2948 | 4 | 256 | 32 | 4 / 30000 | 



[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]


##  <a name="frequently-asked-questions"></a>Vanliga frågor och svar

**F:** Vilken version av Vitis ska jag använda? 

**S:** Xilinx rekommenderar [Vitis 2020.2](https://www.xilinx.com/products/design-tools/vitis/vitis-platform.html)


**F:** Måste jag använda virtuella NP-datorer för att utveckla min lösning? 

**S:** Nej, du kan utveckla lokalt och distribuera till molnet! Se till att följa attestationsdokumentationen för distribution på virtuella NP-datorer. 

**F:** Vilken version av XRT ska jag använda?

**S:** xrt_202020.2.8.832 

**F:** Vad är måldistributionsplattformen?

**S:** Använd följande plattformar.
- xilinx-u250-gen3x16-xdma-platform-2.1-3_all
- xilinx-u250-gen3x16-xdma-validate_2.1-3005608.1 

**F:** Vilken plattform ska jag rikta in mig på för utveckling?

**S:** xilinx-u250-gen3x16-xdma-2.1-202010-1-dev_1-2954688_all 

**F:** Vilket operativsystem stöds (operativsystem)? 

**S:** Xilinx och Microsoft har verifierat Ubuntu 18.04 LTS och CentOS 7.8.

 Xilinx har skapat följande Marketplace-avbildningar för att förenkla distributionen av dessa virtuella datorer. 

[Xilinx Alveo U250 Deployment VM – Ubuntu18.04](https://ms.portal.azure.com/#blade/Microsoft_Azure_Marketplace/GalleryItemDetailsBladeNopdl/id/xilinx.xilinx_alveo_u250_deployment_vm_ubuntu1804_032321)

[Xilinx Alveo U250 Deployment VM – CentOS7.8](https://ms.portal.azure.com/#blade/Microsoft_Azure_Marketplace/GalleryItemDetailsBladeNopdl/id/xilinx.xilinx_alveo_u250_deployment_vm_centos78_032321)

**F:** Kan jag distribuera mina egna virtuella Ubuntu/CentOS-datorer och installera XRT/Deployment Target Platform? 

**S:** Ja.

**F:** Vilka paket och steg krävs om jag distribuerar min egen virtuella Ubuntu18.04-dator?

**S:** Använda Kernel 4.1X per [XRT-dokumentation för Xilinx](https://www.xilinx.com/support/documentation/sw_manuals/xilinx2020_2/ug1451-xrt-release-notes.pdf)
       
Installera följande paket.
- xrt_202020.2.8.832_18.04-amd64-xrt.deb
       
- xrt_202020.2.8.832_18.04-amd64-azure.deb
       
- xilinx-u250-gen3x16-xdma-platform-2.1-3_all_18.04.deb.tar.gz
       
- xilinx-u250-gen3x16-xdma-validate_2.1-3005608.1_all.deb  

**F:** När jag har startat om min virtuella dator i Ubuntu kan jag inte hitta mina FPGA:er: 

**S:** Kontrollera att din kernel inte har uppgraderats (uname -a). I så fall nedgraderar du till kernel 4.1X. 

**F:** Vilka paket och steg krävs om jag distribuerar min egen virtuella CentOS7.8-dator?

**S:** Använd Kernel-version: 3.10.0-1160.15.2.el7.x86_64

 Installera följande paket.
   
 - xrt_202020.2.8.832_7.4.1708-x86_64-xrt.rpm 
      
 - xrt_202020.2.8.832_7.4.1708-x86_64-azure.rpm 
     
 - xilinx-u250-gen3x16-xdma-platform-2.1-3.noarch.rpm.tar.gz 
      
 - xilinx-u250-gen3x16-xdma-validate-2.1-3005608.1.noarch.rpm  

**F:** När jag kör xbutil validate på CentOS får jag den här varningen: "VARNING: Kernelversion 3.10.0-1160.15.2.el7.x86_64 stöds inte officiellt. 4.18.0-193 är den senaste versionen som stöds." 

**S:** Detta kan ignoreras. 

**F:** Vilka är skillnaderna mellan virtuella OnPrem- och NP-datorer när det gäller XRT? 

**S:** På Azure stöder XDMA 2.1-plattformen endast funktioner för Host_Mem (SB) och DDR-datalagring. 

Så här aktiverar Host_Mem(SB) (1 GB RAM): sudo xbutil host_mem --enable --size 1g 

Så här inaktiverar Host_Mem(SB): sudo xbutil host_mem --disable 

**F:** Kan jag köra xbmgmt-kommandon? 

**S:** Nej, på virtuella Azure-datorer finns det inget hanteringsstöd direkt från den virtuella Azure-datorn. 

 **F:** Måste jag läsa in en PLP? 

**S:** Nej, PLP läses in automatiskt åt dig, så du behöver inte läsa in via xbmgmt-kommandon. 

 
**F:** Stöder Azure olika PLP:er? 

**S:** Inte just nu. Vi stöder endast PLP som tillhandahålls i distributionens plattformspaket. 

**F:** Hur frågar jag PLP-informationen? 

**S:** Du måste köra xbutil-frågan och titta på den nedre delen. 

**F:** Vilka ytterligare ändringar behöver jag göra om jag skapar min egen virtuella dator och distribuerar XRT manuellt? 

**S:** I /opt/xilinx/xrt/setup.sh lägger du till en post för XRT_INI_PATH som pekar på /opt/xilinx/xrt/xrt.ini

 
Innehållet i /opt/xilinx/xrt/xrt.ini ska innehålla: <br>
[Runtime]<br>
ert=false <br>

## <a name="other-sizes"></a>Andra storlekar

- [Generell användning](sizes-general.md)
- [Minnesoptimerad](sizes-memory.md)
- [Lagringsoptimerad](sizes-storage.md)
- [GPU-optimerad](sizes-gpu.md)
- [Databehandling med höga prestanda](sizes-hpc.md)
- [Tidigare generationer](sizes-previous-gen.md)

## <a name="next-steps"></a>Nästa steg

Läs mer om hur [Azure-beräkningsenheter (ACU) kan](acu.md) hjälpa dig att jämföra beräkningsprestanda mellan Azure-SKU:er.
