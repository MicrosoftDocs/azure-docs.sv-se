---
title: Stöd för migrering av fysiska servrar i Azure Migrate
description: Läs mer om stöd för migrering av fysiska servrar i Azure Migrate.
author: rahulg1190
ms.author: rahugup
ms.manager: bsiva
ms.topic: conceptual
ms.date: 06/14/2020
ms.openlocfilehash: 4ebc363f29ed8956d10e91f41be1d257cbcc492f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98703894"
---
# <a name="support-matrix-for-physical-server-migration"></a>Support mat ris för fysisk server-migrering

I den här artikeln sammanfattas support inställningar och begränsningar för migrering av fysiska servrar till Azure med [Azure Migrate: Server-migrering](migrate-services-overview.md#azure-migrate-server-migration-tool) . Om du vill ha information om hur du bedömer fysiska servrar för migrering till Azure läser du [matrisen för utvärderings support](migrate-support-matrix-physical.md).

## <a name="migrating-machines-as-physical"></a>Migrera datorer som fysiska

Du kan migrera lokala datorer som fysiska servrar med hjälp av en agent-baserad replikering. Med det här verktyget kan du migrera en mängd olika datorer till Azure:

- Lokala fysiska servrar.
- Virtuella datorer som virtualiserats av plattformar som xen, KVM.
- Virtuella Hyper-V-datorer eller virtuella VMware-datorer om du av någon anledning inte vill använda standard [Hyper-v-](tutorial-migrate-hyper-v.md) eller [VMware](server-migrate-overview.md) -flöden.
- Virtuella datorer som körs i privata moln.
- Virtuella datorer som körs i offentliga moln, inklusive Amazon Web Services (AWS) eller Google Cloud Platform (GCP).


## <a name="migration-limitations"></a>Migreringsbegränsningar

Du kan välja upp till 10 datorer på en gång för replikering. Om du vill migrera fler datorer kan du replikera i grupper om 10.


## <a name="physical-server-requirements"></a>Krav för fysisk server

Tabellen sammanfattar stödet för fysiska servrar som du vill migrera med hjälp av en agent-baserad migrering.

**Support** | **Information**
--- | ---
**Dator arbets belastning** | Azure Migrate stöder migrering av arbets belastningar (t. ex. Active Directory, SQL Server osv.) som körs på en dator som stöds.
**Operativsystem** | Du hittar den senaste informationen i [operativ systemets stöd](../site-recovery/vmware-physical-azure-support-matrix.md#replicated-machines) för Site Recovery. Azure Migrate ger samma stöd för operativ system.
**Linux-filsystem/gäst lagring** | Du hittar den senaste informationen i [Linux-filsystemets stöd](../site-recovery/vmware-physical-azure-support-matrix.md#linux-file-systemsguest-storage) för Site Recovery. Azure Migrate tillhandahåller identiskt stöd för Linux-filsystem.
**Nätverk/lagring** | För den senaste informationen granskar du kraven för [nätverk](../site-recovery/vmware-physical-azure-support-matrix.md#network) och [lagring](../site-recovery/vmware-physical-azure-support-matrix.md#storage) för Site Recovery. Azure Migrate tillhandahåller identiska nätverks-/lagrings krav.
**Krav för Azure** | Du hittar den senaste informationen i [Azure-nätverket](../site-recovery/vmware-physical-azure-support-matrix.md#azure-vm-network-after-failover), [lagrings](../site-recovery/vmware-physical-azure-support-matrix.md#azure-storage)-och [beräknings](../site-recovery/vmware-physical-azure-support-matrix.md#azure-compute) kraven för Site Recovery. Azure Migrate har identiska krav för migrering av fysiska servrar.
**Mobilitetstjänsten** | Mobilitets tjänst agenten måste vara installerad på varje dator som du vill migrera.
**UEFI-start** | Stöds. UEFI-baserade datorer kommer att migreras till virtuella datorer i Azure generation 2.  <br/><br/> OS-disken bör ha upp till fyra partitioner och volymer ska formateras med NTFS.
**UEFI – säker start**         | Stöds inte för migrering.
**Mål disk** | Datorer kan bara migreras till Managed disks (standard-HDD, standard SSD, Premium SSD) i Azure.
**Disk storlek** | 2 TB OS-disk; 32 TB för data diskar.
**Disk gränser** |  Upp till 63 diskar per dator.
**Krypterade diskar/volymer** |  Datorer med krypterade diskar/volymer stöds inte för migrering.
**Delat disk kluster** | Stöds inte.
**Oberoende diskar** | Stöds.
**Genom strömnings diskar** | Stöds.
**NFS** | NFS-volymer monterade som volymer på datorerna replikeras inte.
**iSCSI-mål** | Datorer med iSCSI-mål stöds inte för migrering utan agent.
**Multipath i/o** | Stöds inte.
**Grupperade nätverkskort** | Stöds inte.
**IPv6** | Stöds inte.



## <a name="replication-appliance-requirements"></a>Installationskrav för replikering

Om du konfigurerar replikeringen manuellt på en fysisk server kontrollerar du att den uppfyller kraven som sammanfattas i tabellen. När du konfigurerar Azure Migrate Replication-enheten som en virtuell VMware-dator med hjälp av den ägg-mall som finns i Azure Migrate Hub, konfigureras installationen med Windows Server 2016 och uppfyller support kraven. 

- Lär dig mer om [krav för replikerings utrustning](migrate-replication-appliance.md#appliance-requirements).
- MySQL måste vara installerat på enheten. Lär dig mer om [installations alternativ](migrate-replication-appliance.md#mysql-installation).
- Lär dig mer om [webb adresser](migrate-replication-appliance.md#url-access) som krävs för att få åtkomst till replikerings enheten.

## <a name="azure-vm-requirements"></a>Virtuella Azure VMware-datorer

Alla lokala virtuella datorer som replikeras till Azure måste uppfylla de krav för virtuella Azure-datorer som sammanfattas i den här tabellen. När Site Recovery kör en krav kontroll för replikering kommer kontrollen att Miss klar om några av kraven inte uppfylls.

**Komponent** | **Krav** | **Information**
--- | --- | ---
Gästoperativsystem | Verifierar att operativ system som stöds.<br/> Du kan migrera alla arbets belastningar som körs på ett operativ system som stöds. | Kontrollen Miss lyckas om den inte stöds.
Gäst operativ systemets arkitektur | 64-bitars. | Kontrollen Miss lyckas om den inte stöds.
Storlek på operativsystemdisk | Upp till 2 048 GB. | Kontrollen Miss lyckas om den inte stöds.
Antal operativsystemdiskar | 1 | Kontrollen Miss lyckas om den inte stöds.
Antal datadiskar | 64 eller mindre. | Kontrollen Miss lyckas om den inte stöds.
Data disk storlek | Upp till 32 TB | Kontrollen Miss lyckas om den inte stöds.
Nätverkskort | Flera nätverkskort stöds. |
Delad VHD | Stöds inte. | Kontrollen Miss lyckas om den inte stöds.
FC-disk | Stöds inte. | Kontrollen Miss lyckas om den inte stöds.
BitLocker | Stöds inte. | BitLocker måste inaktive ras innan du aktiverar replikering för en dator.
VM-namn | Mellan 1 och 63 tecken.<br/> Begränsat till bokstäver, siffror och bindestreck.<br/><br/> Dator namnet måste börja och sluta med en bokstav eller en siffra. |  Uppdatera värdet i dator egenskaperna i Site Recovery.
Anslut efter migreringen – Windows | Ansluta till virtuella Azure-datorer som kör Windows efter migrering:<br/> -Innan migreringen aktiverar RDP på den lokala virtuella datorn. Kontrollera att TCP- och UDP-regler har lagts till för den **offentliga** profilen och att RDP tillåts i **Windows-brandväggen** > **Tillåtna appar** för alla profiler.<br/> För plats-till-plats-VPN-åtkomst aktiverar du RDP och tillåter RDP i **Windows-brandväggen**  ->  **tillåtna appar och funktioner** för **domän nätverk och privata** nätverk. Dessutom kontrollerar du att operativ systemets SAN-princip är inställd på **OnlineAll**. [Läs mer](prepare-for-migration.md). |
Anslut efter migreringen – Linux | Ansluta till virtuella Azure-datorer efter migrering med SSH:<br/> Innan migreringen går du till den lokala datorn, kontrollerar att tjänsten Secure Shell är inställt på Start och att brand Väggs reglerna tillåter en SSH-anslutning.<br/> Efter redundansväxlingen på den virtuella Azure-datorn tillåter du inkommande anslutningar till SSH-porten för reglerna för nätverks säkerhets grupper på den misslyckade virtuella datorn och för det Azure-undernät som den är ansluten till. Lägg också till en offentlig IP-adress för den virtuella datorn. |  


## <a name="next-steps"></a>Nästa steg

[Migrera](tutorial-migrate-physical-virtual-machines.md) fysiska servrar.
