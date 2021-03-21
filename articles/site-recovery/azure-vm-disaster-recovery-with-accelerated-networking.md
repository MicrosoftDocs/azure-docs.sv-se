---
title: Aktivera accelererat nätverk för haveri beredskap för virtuella Azure-datorer med Azure Site Recovery
description: Beskriver hur du aktiverar accelererat nätverk med Azure Site Recovery för haveri beredskap för virtuella Azure-datorer
services: site-recovery
documentationcenter: ''
author: Harsha-CS
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 04/08/2019
ms.author: harshacs
ms.openlocfilehash: 1d2d3b3aacc00428c96cde0f8230421a98151ae2
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96000257"
---
# <a name="accelerated-networking-with-azure-virtual-machine-disaster-recovery"></a>Accelererat nätverk med haveri beredskap för virtuella Azure-datorer

Accelererat nätverk möjliggör SR-IOV (Single root I/O Virtualization) till en virtuell dator, vilket avsevärt förbättrar nätverkets prestanda. Den här högpresterande sökvägen kringgår värden från Datapath, minskar svars tiden, skakningarna och CPU-användningen, för användning med de mest krävande nätverks belastningarna på VM-typer som stöds. Följande bild visar kommunikationen mellan två virtuella datorer med och utan accelererat nätverk:

![Jämförelse](./media/azure-vm-disaster-recovery-with-accelerated-networking/accelerated-networking-benefit.png)

Azure Site Recovery gör att du kan använda fördelarna med accelererat nätverk för virtuella Azure-datorer som har redundansväxlats till en annan Azure-region. Den här artikeln beskriver hur du kan aktivera accelererat nätverk för virtuella Azure-datorer som replikeras med Azure Site Recovery.

## <a name="prerequisites"></a>Förutsättningar

Innan du börjar ska du se till att du förstår:
-   [Arkitektur för replikering](azure-to-azure-architecture.md) av virtuella Azure-datorer
-   Konfigurera [replikering](azure-to-azure-tutorial-enable-replication.md) för virtuella Azure-datorer
-   [Växlar över](azure-to-azure-tutorial-failover-failback.md) Virtuella Azure-datorer

## <a name="accelerated-networking-with-windows-vms"></a>Accelererat nätverk med virtuella Windows-datorer

Azure Site Recovery stöder aktivering av accelererat nätverk för replikerade virtuella datorer endast om den virtuella käll datorn har accelererat nätverk aktiverat. Om den virtuella käll datorn inte har accelererat nätverk aktiverat, kan du lära dig hur du aktiverar accelererat nätverk för virtuella Windows-datorer [här](../virtual-network/create-vm-accelerated-networking-powershell.md#enable-accelerated-networking-on-existing-vms).

### <a name="supported-operating-systems"></a>Operativsystem som stöds
Följande distributioner stöds i rutan från Azure-galleriet:
* **Windows Server 2016 Datacenter**
* **Windows Server 2012 R2 Datacenter**

### <a name="supported-vm-instances"></a>Virtuella dator instanser som stöds
Accelererat nätverk stöds i de flesta generella syftes-och beräknings optimerade instans storlekar med 2 eller fler virtuella processorer.  Dessa serier som stöds är: D/DSv2 och F/FS

På instanser som stöder hyperthreading stöds accelererat nätverk på VM-instanser med 4 eller fler virtuella processorer. Serien som stöds är: D/DSv3, E/ESv3, Fsv2 och MS/MMS

Mer information om VM-instanser finns i [storlekar för virtuella Windows-datorer](../virtual-machines/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

## <a name="accelerated-networking-with-linux-vms"></a>Accelererat nätverk med virtuella Linux-datorer

Azure Site Recovery stöder aktivering av accelererat nätverk för replikerade virtuella datorer endast om den virtuella käll datorn har accelererat nätverk aktiverat. Om den virtuella käll datorn inte har accelererat nätverk aktiverat, kan du lära dig hur du aktiverar accelererat nätverk för virtuella Linux-datorer [här](../virtual-network/create-vm-accelerated-networking-cli.md#enable-accelerated-networking-on-existing-vms).

### <a name="supported-operating-systems"></a>Operativsystem som stöds
Följande distributioner stöds i rutan från Azure-galleriet:
* **Ubuntu 16.04**
* **SLES 12 SP3**
* **RHEL 7,4**
* **CentOS 7.4**
* **CoreOS Linux**
* **Debian "Sträck ut" med backports-kernel**
* **Oracle Linux 7,4**

### <a name="supported-vm-instances"></a>Virtuella dator instanser som stöds
Accelererat nätverk stöds i de flesta generella syftes-och beräknings optimerade instans storlekar med 2 eller fler virtuella processorer.  Dessa serier som stöds är: D/DSv2 och F/FS

På instanser som stöder hyperthreading stöds accelererat nätverk på VM-instanser med 4 eller fler virtuella processorer. Serien som stöds är: D/DSv3, E/ESv3, Fsv2 och MS/MMS.

Mer information om VM-instanser finns i [storlekar för virtuella Linux-datorer](../virtual-machines/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

## <a name="enabling-accelerated-networking-for-replicated-vms"></a>Aktivera accelererat nätverk för replikerade virtuella datorer

När du [aktiverar replikering](azure-to-azure-tutorial-enable-replication.md) för virtuella Azure-datorer kommer Site Recovery automatiskt att identifiera om de virtuella datorernas nätverks gränssnitt har accelererat nätverk aktiverat. Om accelererat nätverk redan är aktiverat konfigurerar Site Recovery automatiskt accelererat nätverk på nätverks gränssnitten för den replikerade virtuella datorn.

Statusen för accelererat nätverk kan verifieras under avsnittet **nätverks gränssnitt** i **beräknings-och nätverks** inställningarna för den replikerade virtuella datorn.

![Snabb nätverks inställning](./media/azure-vm-disaster-recovery-with-accelerated-networking/compute-network-accelerated-networking.png)

Om du har aktiverat accelererat nätverk på den virtuella käll datorn efter att ha aktiverat replikering kan du aktivera accelererat nätverk för den replikerade virtuella datorns nätverks gränssnitt genom följande process:
1. Öppna **beräknings-och nätverks** inställningar för den replikerade virtuella datorn
2. Klicka på namnet på nätverks gränssnittet under avsnittet **nätverks gränssnitt**
3. Välj **aktive rad** i list rutan för accelererat nätverk under kolumnen **mål**

![Aktivera accelererat nätverk](./media/azure-vm-disaster-recovery-with-accelerated-networking/network-interface-accelerated-networking-enabled.png)

Ovanstående process bör också följas för befintliga replikerade virtuella datorer som inte tidigare har accelererat nätverk aktiverat automatiskt av Site Recovery.

## <a name="next-steps"></a>Nästa steg
- Lär dig mer om [fördelarna med accelererat nätverk](../virtual-network/create-vm-accelerated-networking-powershell.md#benefits).
- Lär dig mer om begränsningar och begränsningar i accelererat nätverk för [virtuella Windows-datorer](../virtual-network/create-vm-accelerated-networking-powershell.md#limitations-and-constraints) och [virtuella Linux-datorer](../virtual-network/create-vm-accelerated-networking-cli.md#limitations-and-constraints).
- Läs mer om [återställnings planer](site-recovery-create-recovery-plans.md) för att automatisera programredundans.