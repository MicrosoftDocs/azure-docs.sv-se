---
title: Stöd för VMware/fysisk haveri beredskap till en sekundär plats med Azure Site Recovery
description: Sammanfattar stödet för haveri beredskap för virtuella VMware-datorer och fysiska servrar till en sekundär plats med Azure Site Recovery.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
services: site-recovery
ms.topic: conceptual
ms.date: 11/14/2019
ms.author: raynew
ms.openlocfilehash: ac67e3cf8f057738b76b0de7cbcb821ef290e0cb
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98757584"
---
# <a name="support-matrix-for-disaster-recovery-of-vmware-vms-and-physical-servers-to-a-secondary-site"></a>Stöd mat ris för haveri beredskap för virtuella VMware-datorer och fysiska servrar till en sekundär plats

I den här artikeln sammanfattas vad som stöds när du använder [Azure Site Recovery](site-recovery-overview.md) tjänsten för haveri beredskap för virtuella VMware-datorer eller fysiska Windows/Linux-servrar till en sekundär VMware-plats.

- Om du vill replikera virtuella VMware-datorer eller fysiska servrar till Azure läser du [denna support mat ris](vmware-physical-azure-support-matrix.md).
- Om du vill replikera virtuella Hyper-V-datorer till en sekundär plats läser du [denna support mat ris](hyper-v-azure-support-matrix.md).

> [!NOTE]
> Replikering av lokala virtuella VMware-datorer och fysiska servrar tillhandahålls av InMage Scout. InMage Scout ingår i Azure Site Recovery tjänst prenumerationen.

## <a name="end-of-support-announcement"></a>Meddelande om upphörande support
Site Recovery scenariot för replikering mellan lokala VMware-eller fysiska data Center når slut för and support.

- Från augusti 2018 går det inte att konfigurera scenariot i Recovery Services-valvet och InMage Scout-programvaran kan inte laddas ned från valvet. Befintliga distributioner kommer att stödjas.
- - Från december 31 2020 stöds inte scenariot.
Befintliga partners kan publicera nya kunder till scenariot tills supporten upphör.
- Under 2018 och 2019 publiceras två uppdateringar:

    - Uppdatering 7: åtgärdar problem med nätverks konfiguration och efterlevnad och ger stöd för TLS 1,2.
    - Uppdatering 8: lägger till stöd för Linux-operativsystem RHEL/CentOS 7.3/7.4/7.5 och för SUSE 12
    - Efter uppdatering 8 kommer inga ytterligare uppdateringar att släppas. Det finns ett begränsat stöd för snabb korrigeringar för de operativ system som har lagts till i uppdatering 8 och fel korrigeringar baserade på bästa möjliga ansträngning.

## <a name="host-servers"></a>Värd servrar

**Operativsystem** | **Information**
--- | ---
vCenter Server | vCenter 5,5, 6,0 och 6,5<br/><br/> Observera att endast 5,5-funktioner stöds om du kör 6,0 eller 6,5.


## <a name="replicated-vm-support"></a>Stöd för replikerad virtuell dator

I följande tabell sammanfattas stöd för operativ system för datorer som replikeras med Site Recovery. Alla arbets belastningar kan köras på det operativ system som stöds.

**Operativsystem** | **Information**
--- | ---
Windows Server | 64-bitars Windows Server 2016, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 med minst SP1.
Linux | Red Hat Enterprise Linux 6,7, 6,8, 6,9, 7,1, 7,2 <br/><br/> CentOS 6,5, 6,6, 6,7, 6,8, 6,9, 7,0, 7,1, 7,2 <br/><br/> Oracle Enterprise Linux 6,4, 6,5, 6,8 som kör Red Hat-kompatibel kernel eller Enterprise kernel release 3 (UEK3) <br/><br/> SUSE Linux Enterprise Server 11 SP3, 11 SP4 


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
Värd-NIC-teamning | Ja 
Värd-VLAN | Ja 
Värd-IPv4 | Ja 
Värd-IPv6 | Inga 
Gäst-VM – NIC Teaming | Inga
Gäst-VM – IPv4 | Ja
Gäst-VM-IPv6 | Inga
Gäst-VM – Windows/Linux-statisk IP-adress | Ja
Gäst-VM-Multi-NIC | Ja


## <a name="storage"></a>Storage

### <a name="host-storage"></a>Värd lagring

**Lagring (värd)** | **Stöds** 
--- | --- 
NFS | Ja 
SMB 3.0 | Ej tillämpligt 
SAN (ISCSI) | Ja 
Multipath (MPIO) | Ja 

### <a name="guest-or-physical-server-storage"></a>Gäst eller fysisk server lagring

**Konfiguration** | **Stöds** 
--- | --- 
VMDK | Ja 
VHD/VHDX | Ej tillämpligt 
Generation 2 VM | Ej tillämpligt 
Delad kluster disk | Ja 
Krypterad disk | Inga 
UEFI| Ja 
NFS | Inga 
SMB 3.0 | Inga 
RDM | Ja 
Disk > 1 TB | Ja 
Volym med Striped disk > 1 TB<br/><br/> LVM | Ja 
Lagringsutrymmen | Inga 
Snabb Lägg till/ta bort disk | Ja 
Uteslut disk | Ja 
Multipath (MPIO) | Ej tillämpligt 

## <a name="vaults"></a>Valv

**Åtgärd** | **Stöds** 
--- | --- 
Flytta valv över resurs grupper (inom eller mellan prenumerationer) | Inga 
Flytta lagring, nätverk, virtuella Azure-datorer över resurs grupper (inom eller mellan prenumerationer) | Inga 

## <a name="mobility-service-and-updates"></a>Mobilitets tjänsten och uppdateringar

Mobilitets tjänsten samordnar replikering mellan lokala VMware-servrar eller fysiska servrar och den sekundära platsen. När du konfigurerar replikering bör du kontrol lera att du har den senaste versionen av mobilitets tjänsten och andra komponenter.

| **Uppdatera** | **Information** |
| --- | --- |
|Scout-uppdateringar | Uppdateringar för Scout är kumulativa. <br/><br/> [Lär dig mer om och hämta](vmware-physical-secondary-disaster-recovery.md#updates) de senaste Scout-uppdateringarna |
|Komponent uppdateringar | I Scout-uppdateringarna ingår uppdateringar för alla komponenter, inklusive RX-Server, konfigurations Server, process-och huvud mål servrar, vContinuum-servrar och käll servrar som du vill skydda.<br/><br/> [Läs mer](vmware-physical-secondary-disaster-recovery.md#download-and-install-component-updates).|


## <a name="next-steps"></a>Nästa steg

Hämta [användar handboken för InMage Scout](https://aka.ms/asr-scout-user-guide)

- [Replikera virtuella Hyper-V-datorer i VMM-moln till en sekundär plats](./hyper-v-vmm-disaster-recovery.md)
- [Replikera virtuella VMware-datorer och fysiska servrar till en sekundär plats](./vmware-physical-secondary-disaster-recovery.md)
