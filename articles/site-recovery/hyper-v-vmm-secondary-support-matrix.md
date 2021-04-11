---
title: Stöd för matris – återställning av Hyper-V till en sekundär VMM-plats med Azure Site Recovery
description: Sammanfattar stöd för replikering av virtuella Hyper-V-datorer i VMM-moln till en sekundär plats med Azure Site Recovery.
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/06/2019
ms.openlocfilehash: 82f34d660c4012e192a157dc836574bc1ab25233
ms.sourcegitcommit: d63f15674f74d908f4017176f8eddf0283f3fac8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/07/2021
ms.locfileid: "106579947"
---
# <a name="support-matrix-for-disaster-recovery-of-hyper-v-vms-to-a-secondary-site"></a>Supportmatris för haveriberedskap av virtuella Hyper-V-datorer till en sekundär webbplats

I den här artikeln sammanfattas vad som stöds när du använder tjänsten [Azure Site Recovery](site-recovery-overview.md) för att replikera virtuella Hyper-V-datorer som hanteras i System Center Virtual Machine Manager (VMM)-moln till en sekundär plats. Om du vill replikera virtuella Hyper-V-datorer till Azure läser du [denna support mat ris](hyper-v-azure-support-matrix.md).

> [!NOTE]
> Du kan bara replikera till en sekundär plats när dina Hyper-V-värdar hanteras i VMM-moln.


## <a name="host-servers"></a>Värd servrar

**Operativsystem** | **Information**
--- | ---
Windows Server 2012 R2 | Servrarna måste köra de senaste uppdateringarna.
Windows Server 2016 |  VMM 2016-moln med en blandning av Windows Server 2016-och 2012 R2-värdar stöds inte för närvarande.<br/><br/> Distributioner som uppgraderas från System Center 2012 R2 VMM 2012 R2 till System Center 2016 stöds inte för närvarande.


## <a name="replicated-vm-support"></a>Stöd för replikerad virtuell dator

I följande tabell sammanfattas stöd för operativ system för datorer som replikeras med Site Recovery. Alla arbets belastningar kan köras på det operativ system som stöds.

**Windows-version** | **Hyper-V (med VMM)**
--- | ---
Windows Server 2016 | Alla gäst operativ system [som stöds av Hyper-V](/windows-server/virtualization/hyper-v/Supported-Windows-guest-operating-systems-for-Hyper-V-on-Windows) på Windows Server 2016 
Windows Server 2012 R2 | Alla gäst operativ system [som stöds av Hyper-V](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn792027%28v%3dws.11%29) på Windows Server 2012 R2

## <a name="linux-machine-storage"></a>Linux-dator lagring

Endast Linux-datorer med följande lagring kan replikeras:

- Fil system (EXT3, ETX4, ReiserFS, XFS).
- Multipath program vara-enhets mappning.
- Volym hanteraren (LVM2).
- Fysiska servrar med HP CCISS Controller-lagring stöds inte.
- Fil systemet ReiserFS stöds endast på SUSE Linux Enterprise Server 11 SP3.

## <a name="network-configuration---hostguest-vm"></a>Nätverks konfiguration – värd/gäst-VM

**Konfiguration** | **Stöds**  
--- | --- 
Värd-NIC-teamning | Yes 
Värd-VLAN | Yes 
Värd-IPv4 | Yes 
Värd-IPv6 | No 
Gäst-VM – NIC Teaming | No
Gäst-VM – IPv4 | Yes
Gäst-VM-IPv6 | No
Gäst-VM – Windows/Linux-statisk IP-adress | Yes
Gäst-VM-Multi-NIC | Ja


## <a name="storage"></a>Storage

### <a name="host-storage"></a>Värd lagring

**Lagring (värd)** | **Stöds**
--- | --- 
NFS | Ej tillämpligt
SMB 3.0 |  Yes
SAN (ISCSI) | Yes
Multipath (MPIO) | Yes

### <a name="guest-or-physical-server-storage"></a>Gäst eller fysisk server lagring

**Konfiguration** | **Stöds**
--- | --- | 
VMDK |  Ej tillämpligt
VHD/VHDX | Ja (upp till 16 diskar)
Generation 2 VM | Yes
Delad kluster disk | No
Krypterad disk | No
UEFI| Ej tillämpligt
NFS | No
SMB 3.0 | No
RDM | Ej tillämpligt
Disk > 1 TB | Yes
Volym med Striped disk > 1 TB<br/><br/> LVM | Yes
Lagringsutrymmen | Yes
Snabb Lägg till/ta bort disk | No
Uteslut disk | Yes
Multipath (MPIO) | Yes

## <a name="vaults"></a>Valv

**Åtgärd** | **Stöds**
--- | --- 
Flytta valv över resurs grupper (inom eller mellan prenumerationer) |  No
Flytta lagring, nätverk, virtuella Azure-datorer över resurs grupper (inom eller mellan prenumerationer) | No

## <a name="azure-site-recovery-provider"></a>Azure Site Recovery Provider

Providern samordnar kommunikationen mellan VMM-servrar. 

**Nya** | **Uppdateringar**
--- | --- 
5.1.19 ([tillgängligt från portalen](https://aka.ms/downloaddra) | [Senaste funktioner och korrigeringar](https://support.microsoft.com/kb/3155002)



## <a name="next-steps"></a>Nästa steg

[Replikera virtuella Hyper-V-datorer i VMM-moln till en sekundär plats](./hyper-v-vmm-disaster-recovery.md)
