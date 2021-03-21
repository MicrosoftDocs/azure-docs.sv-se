---
title: Stöd för haveri beredskap för virtuella Hyper-V-datorer till Azure med Azure Site Recovery
description: Sammanfattar de komponenter och krav som stöds för haveri beredskap för virtuella Hyper-V-datorer till Azure med Azure Site Recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 7/14/2020
ms.author: raynew
ms.openlocfilehash: 8d748f93337a770e0d565bab79fdfb3625bda70d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "101735530"
---
# <a name="support-matrix-for-disaster-recovery-of-on-premises-hyper-v-vms-to-azure"></a>Stöd mat ris för haveri beredskap för lokala virtuella Hyper-V-datorer till Azure

Den här artikeln sammanfattar de komponenter och inställningar som stöds för haveri beredskap för lokala virtuella Hyper-V-datorer till Azure med hjälp av [Azure Site Recovery](site-recovery-overview.md).

>[!NOTE]
> Site Recovery flyttar eller lagrar inte kund information från mål regionen, där haveri beredskap har kon figurer ATS för käll datorerna. Kunder kan välja ett Recovery Services valv från en annan region om de så vill. Recovery Services valvet innehåller metadata men inga faktiska kund uppgifter.

## <a name="supported-scenarios"></a>Scenarier som stöds

**Scenario** | **Information**
--- | ---
Hyper-V med Virtual Machine Manager <br> <br>| Du kan utföra haveri beredskap till Azure för virtuella datorer som körs på Hyper-V-värdar som hanteras i System Center Virtual Machine Manager-infrastrukturen.<br/><br/> Du kan distribuera det här scenariot i Azure Portal eller med hjälp av PowerShell.<br/><br/> När Hyper-V-värdar hanteras av Virtual Machine Manager kan du också utföra haveri beredskap på en sekundär lokal plats. Läs mer om det här scenariot i [den här självstudien](hyper-v-vmm-disaster-recovery.md).
Hyper-V utan Virtual Machine Manager | Du kan utföra haveri beredskap till Azure för virtuella datorer som körs på Hyper-V-värdar som inte hanteras av Virtual Machine Manager.<br/><br/> Du kan distribuera det här scenariot i Azure Portal eller med hjälp av PowerShell.

## <a name="on-premises-servers"></a>Lokala servrar

**Server** | **Krav** | **Information**
--- | --- | ---
Hyper-V (körs utan Virtual Machine Manager) |  Windows Server 2019, Windows Server 2016, Windows Server 2012 R2 med senaste uppdateringar <br/><br/> **Obs:** Server Core-installation av dessa operativ system stöds också. | Om du redan har konfigurerat Windows Server 2012 R2 med/eller SCVMM 2012 R2 med Azure Site Recovery och planerar att uppgradera operativ systemet, följer du vägledningen [.](upgrade-2012R2-to-2016.md)
Hyper-V (körs med Virtual Machine Manager) | Virtual Machine Manager 2019, Virtual Machine Manager 2016, Virtual Machine Manager 2012 R2 <br/><br/> **Obs:** Server Core-installation av dessa operativ system stöds också.  | Om Virtual Machine Manager används bör Windows Server 2019-värdar hanteras i Virtual Machine Manager 2019. På samma sätt bör Windows Server 2016-värdar hanteras i Virtual Machine Manager 2016.

> [!NOTE]
> Se till att .NET Framework 4.6.2 eller högre finns på den lokala servern.

## <a name="replicated-vms"></a>Replikerade virtuella datorer


I följande tabell sammanfattas stödet för virtuella datorer. Site Recovery stöder alla arbets belastningar som körs på ett operativ system som stöds.

 **Komponent** | **Information**
--- | ---
Konfiguration av virtuell dator | Virtuella datorer som replikeras till Azure måste uppfylla [kraven för Azure](#azure-vm-requirements).
Gästoperativsystem | Alla gäst operativ system [som stöds för Azure](../cloud-services/cloud-services-guestos-update-matrix.md#family-5-releases)...<br/><br/> Windows Server 2016 Nano Server stöds inte.


## <a name="vmdisk-management"></a>Hantering av virtuell dator/disk

**Åtgärd** | **Information**
--- | ---
Ändra storlek på disk på replikerad virtuell Hyper-V-dator | Stöds inte. Inaktivera replikering, gör ändringen och återaktivera replikering för den virtuella datorn.
Lägg till disk på replikerad virtuell Hyper-V-dator | Stöds inte. Inaktivera replikering, gör ändringen och återaktivera replikering för den virtuella datorn.

## <a name="hyper-v-network-configuration"></a>Nätverks konfiguration för Hyper-V

**Komponent** | **Hyper-V med Virtual Machine Manager** | **Hyper-V utan Virtual Machine Manager**
--- | --- | ---
Värd nätverk: NIC Teaming | Ja | Ja
Värd nätverk: VLAN | Ja | Ja
Värd nätverk: IPv4 | Ja | Ja
Värd nätverk: IPv6 | Inga | Inga
Gäst-VM-nätverk: NIC Teaming | Inga | Inga
Gäst-VM-nätverk: IPv4 | Ja | Ja
Gäst-VM-nätverk: IPv6 | Inga | Ja
Gäst-VM-nätverk: statisk IP (Windows) | Ja | Ja
Gäst-VM-nätverk: statisk IP (Linux) | Inga | Inga
Gäst-VM-nätverk: multi-NIC | Ja | Ja
Https-proxy | Inga | Inga
Åtkomst till Site Recovery-tjänst via privat länk | Ja. [Läs mer](hybrid-how-to-enable-replication-private-endpoints.md). | Ja. [Läs mer](hybrid-how-to-enable-replication-private-endpoints.md).




## <a name="azure-vm-network-configuration-after-failover"></a>Konfiguration av Azure VM-nätverk (efter redundans)

**Komponent** | **Hyper-V med Virtual Machine Manager** | **Hyper-V utan Virtual Machine Manager**
--- | --- | ---
Azure ExpressRoute | Ja | Ja
ILB | Ja | Ja
ELB | Ja | Ja
Azure Traffic Manager | Ja | Ja
Multi-NIC | Ja | Ja
Reserverad IP | Ja | Ja
IPv4 | Ja | Ja
Behåll Källans IP-adress | Ja | Ja
Azure Virtual Network-tjänstens slut punkter<br/> (utan Azure Storage brand väggar) | Ja | Ja
Accelererat nätverk | Inga | Inga


## <a name="hyper-v-host-storage"></a>Lagring av Hyper-V-värd

**Storage** | **Hyper-V med Virtual Machine Manager** | **Hyper-V utan Virtual Machine Manager**
--- | --- | --- 
NFS | NA | NA
SMB 3.0 | Ja | Ja
SAN (ISCSI) | Ja | Ja
Multipath (MPIO). Testat med:<br></br> Microsoft DSM, EMC PowerPath 5,7 SP4, EMC PowerPath DSM för CLARiiON | Ja | Ja

## <a name="hyper-v-vm-guest-storage"></a>Gäst lagring för Hyper-V-VM

**Storage** | **Hyper-V med Virtual Machine Manager** | **Hyper-V utan Virtual Machine Manager**
--- | --- | ---
VMDK | NA | NA
VHD/VHDX | Ja | Ja
Generation 2 VM | Ja | Ja
EFI/UEFI<br></br>Den migrerade virtuella datorn i Azure kommer automatiskt att konverteras till en virtuell dator med BIOS-start. Den virtuella datorn ska endast köra Windows Server 2012 och senare. OS-disken bör ha upp till fem partitioner eller färre och storleken på OS-disken måste vara mindre än 300 GB.| Ja | Ja
Delad kluster disk | Inga | Inga
Krypterad disk | Inga | Inga
NFS | NA | NA
SMB 3.0 | Inga | Inga
RDM | NA | NA
Disk >1 TB | Ja, upp till 4 095 GB | Ja, upp till 4 095 GB
Disk: logisk och fysisk sektor i 4K | Stöds inte: gen 1/Gen 2 | Stöds inte: gen 1/Gen 2
Disk: logisk och 512-byte fysisk sektor | Ja |  Ja
Hantering av logiska volymer (LVM). LVM stöds endast på data diskar. Azure tillhandahåller bara en enda OS-disk. | Ja | Ja
Volym med Striped disk >1 TB | Ja | Ja
Lagringsutrymmen | Inga | Inga
Snabb Lägg till/ta bort disk | Inga | Inga
Uteslut disk | Ja | Ja
Multipath (MPIO) | Ja | Ja

## <a name="azure-storage"></a>Azure Storage

**Komponent** | **Hyper-V med Virtual Machine Manager** | **Hyper-V utan Virtual Machine Manager**
--- | --- | ---
Lokalt redundant lagring | Ja | Ja
Geo-redundant lagring | Ja | Ja
Geo-redundant lagring med läsbehörighet (RA-GRS) | Ja | Ja
Zonredundant lagring | Inga | Inga
Cool Storage | Inga | Inga
Frekvent lagring| Inga | Inga
Blockblobar | Inga | Inga
Kryptering i rest (SSE)| Ja | Ja
Kryptering i vilo läge (CMK) <br></br> (Endast för redundans till Managed Disks)| Ja (via PowerShell AZ 3.3.0-modul och senare) | Ja (via PowerShell AZ 3.3.0-modul och senare)
Dubbel kryptering i vila <br></br> (Endast för redundans till Managed Disks) <br></br> Läs mer om regioner som stöds för [Windows](../virtual-machines/disk-encryption.md) och [Linux](../virtual-machines/disk-encryption.md) | Ja (via PowerShell AZ 3.3.0-modul och senare) | Ja (via PowerShell AZ 3.3.0-modul och senare)
Premium Storage | Ja | Ja
Standard Storage | Ja | Ja
Import/export-tjänst | Inga | Inga
Azure Storage konton med aktive rad brand vägg | Ja. För mål lagring och cache. | Ja. För mål lagring och cache.
Ändra lagrings konto | Nej. Mål Azure Storages kontot kan inte ändras efter att replikeringen har Aktiver ATS. Ändra genom att inaktivera och sedan aktivera haveri beredskap igen. | Inga
Alternativ för säker överföring | Ja | Ja


## <a name="azure-compute-features"></a>Beräknings funktioner i Azure

**Funktion** | **Hyper-V med Virtual Machine Manager** | **Hyper-V utan Virtual Machine Manager**
--- | --- | ---
Tillgänglighetsuppsättningar | Ja | Ja
) | Ja | Ja  
Hanterade diskar | Ja, för redundans.<br/><br/> Det finns inte stöd för återställning efter fel för hanterade diskar. | Ja, för redundans.<br/><br/> Det finns inte stöd för återställning efter fel för hanterade diskar.

## <a name="azure-vm-requirements"></a>Virtuella Azure VMware-datorer

Lokala virtuella datorer som du replikerar till Azure måste uppfylla de krav på Azure VM som sammanfattas i den här tabellen.

**Komponent** | **Krav** | **Information**
--- | --- | ---
Gästoperativsystem | Site Recovery stöder alla operativ system som [stöds av Azure](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc794868(v=ws.10)).  | Krav kontrollen Miss lyckas om den inte stöds.
Gäst operativ systemets arkitektur | 32-bitars (Windows Server 2008)/64-bit | Krav kontrollen Miss lyckas om den inte stöds.
Storlek på operativsystemdisk | Upp till 2 048 GB för virtuella datorer i generation 1.<br/><br/> Upp till 300 GB för virtuella datorer i generation 2.  | Krav kontrollen Miss lyckas om den inte stöds.
Antal operativsystemdiskar | 1 | Krav kontrollen Miss lyckas om den inte stöds.
Antal datadiskar | 16 eller mindre  | Krav kontrollen Miss lyckas om den inte stöds.
VHD-storlek för datadisk | Upp till 4 095 GB | Krav kontrollen Miss lyckas om den inte stöds.
Nätverkskort | Flera nätverkskort stöds |
Delad VHD | Stöds inte | Krav kontrollen Miss lyckas om den inte stöds.
FC-disk | Stöds inte | Krav kontrollen Miss lyckas om den inte stöds.
Format för hårddisk | VHD <br/><br/> VHDX | Site Recovery konverterar automatiskt VHDX till VHD när du växlar över till Azure. När du växlar tillbaka till den lokala datorn fortsätter de virtuella datorerna att använda VHDX-formatet.
BitLocker | Stöds inte | BitLocker måste inaktive ras innan du aktiverar replikering för en virtuell dator.
VM-namn | Mellan 1 och 63 tecken. Begränsat till bokstäver, siffror och bindestreck. VM-namnet måste börja och sluta med en bokstav eller en siffra. | Uppdatera värdet i VM-egenskaperna i Site Recovery.
VM-typ | Generation 1<br/><br/> Generation 2--Windows | Generation 2 virtuella datorer med en OS-disktyp (som innehåller en eller två data volymer formaterade som VHDX) och mindre än 300 GB disk utrymme stöds.<br></br>Virtuella Linux generation 2-datorer stöds inte. [Läs mer](https://azure.microsoft.com/blog/2015/04/28/disaster-recovery-to-azure-enhanced-and-were-listening/).|

## <a name="recovery-services-vault-actions"></a>Recovery Services valv åtgärder

**Åtgärd** |  **Hyper-V med VMM** | **Hyper-V utan VMM**
--- | --- | ---
Flytta valv över resurs grupper<br/><br/> Inom och över prenumerationer | Inga | Inga
Flytta lagring, nätverk, virtuella Azure-datorer över resurs grupper<br/><br/> Inom och över prenumerationer | Inga | Inga

> [!NOTE]
> När du replikerar Hyper-VMs från en lokal plats till Azure kan du bara replikera till en AD-klient från en särskild miljö – Hyper-V-plats eller Hyper-V med VMM som tillämpligt.


## <a name="provider-and-agent"></a>Provider och agent

Se till att du kör de senaste leverantörs-och agent versionerna för att se till att distributionen är kompatibel med inställningarna i den här artikeln.

**Namn** | **Beskrivning** | **Information**
--- | --- | --- 
Azure Site Recovery Provider | Koordinera kommunikation mellan lokala servrar och Azure <br/><br/> Hyper-V med Virtual Machine Manager: installerad på Virtual Machine Manager-servrar<br/><br/> Hyper-V utan Virtual Machine Manager: installerat på Hyper-V-värdar| Senaste version: 5.1.2700.1 (tillgänglig från Azure Portal)<br/><br/> [Senaste funktioner och korrigeringar](https://support.microsoft.com/help/4091311/update-rollup-23-for-azure-site-recovery)
Microsoft Azure Recovery Services-agenten | Samordnar replikering mellan virtuella Hyper-V-datorer och Azure<br/><br/> Installerat på lokala Hyper-V-servrar (med eller utan Virtual Machine Manager) | Senaste agent som är tillgänglig från portalen






## <a name="next-steps"></a>Nästa steg
Lär dig hur du [förbereder Azure](tutorial-prepare-azure.md) för haveri beredskap för lokala virtuella Hyper-V-datorer.