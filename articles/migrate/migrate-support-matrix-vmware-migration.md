---
title: Stöd för VMware-migrering i Azure Migrate
description: Läs mer om stöd för migrering av VMware VM i Azure Migrate.
author: anvar-ms
ms.author: anvar
ms.manager: bsiva
ms.topic: conceptual
ms.date: 06/08/2020
ms.openlocfilehash: 0b671fbdfe16848012ac94671ce68e8a33a8b3e8
ms.sourcegitcommit: 75041f1bce98b1d20cd93945a7b3bd875e6999d0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/22/2021
ms.locfileid: "98703877"
---
# <a name="support-matrix-for-vmware-migration"></a>Stödmatris för VMware-migrering

Den här artikeln sammanfattar support inställningar och begränsningar för migrering av virtuella VMware-datorer med [Azure Migrate: Server-migrering](migrate-services-overview.md#azure-migrate-server-migration-tool) . Om du vill ha information om hur du bedömer virtuella VMware-datorer för migrering till Azure läser du [matrisen för utvärderings support](migrate-support-matrix-vmware.md).


## <a name="migration-options"></a>Migreringsalternativ

Du kan migrera virtuella VMware-datorer på ett par olika sätt:

- **Använda migrering utan agent**: migrera virtuella datorer utan att behöva installera något på dem. Du distribuerar [Azure Migrate](migrate-appliance.md) -installationen för migrering utan agent.
- **Använda agent-baserad migrering**: installera en agent på den virtuella datorn för replikering. För en agent-baserad migrering distribuerar du en [replikeringsfil](migrate-replication-appliance.md).

Läs [den här artikeln](server-migrate-overview.md) för att ta reda på vilken metod du vill använda.

## <a name="migration-limitations"></a>Migreringsbegränsningar

- Du kan välja upp till 10 virtuella datorer på en gång för replikering. Om du vill migrera fler datorer kan du replikera i grupper om 10.
- För VMware-agent lös migrering kan du köra upp till 300 replikeringar samtidigt.

## <a name="agentless-migration"></a>Migrering utan agent 

I det här avsnittet sammanfattas kraven för migrering utan agent.

### <a name="vmware-requirements-agentless"></a>VMware-krav (utan agent)

I tabellen sammanfattas VMware hypervisor-krav.

**VMware** | **Information**
--- | ---
**VMware vCenter Server** | Version 5,5, 6,0, 6,5, 6,7, 7,0.
**VMware vSphere ESXI-värd** | Version 5,5, 6,0, 6,5, 6,7, 7,0.
**vCenter Server behörigheter** | [Vid utan agent används migreringen](migrate-appliance.md). Installationen behöver följande behörigheter i vCenter Server:<br/><br/> - **Data lager. browse** (data lager > bläddra i data lager): Tillåt bläddring av VM-loggfiler för att felsöka skapande och borttagning av ögonblicks bilder.<br/><br/> - **Data lager. FileManagement** (data lager > lågnivå fil åtgärder): Tillåt Läs-/skriv-/ta bort/ta bort/Byt namn på åtgärder i data lagret för att felsöka skapande och borttagning av ögonblicks bilder.<br/><br/> - **VirtualMachine.Config. ChangeTracking** (virtuell dator – > disk ändrings spårning): Tillåt aktivering eller inaktive ring av ändrings spårning av virtuella dator diskar för att hämta ändrade block med data mellan ögonblicks bilder.<br/><br/> - **VirtualMachine.Config. DiskLease** (virtuell dator-> disk Lease): Tillåt disk låne åtgärder för en virtuell dator för att läsa disken med hjälp av VMware vSphere Virtual Disk Development Kit (vddk).<br/><br/> - **VirtualMachine. etablering. DiskRandomRead** (virtuell dator-> etablering-> Tillåt skrivskyddad disk åtkomst): Tillåt att en disk öppnas på en virtuell dator för att läsa disken med hjälp av vddk.<br/><br/> - **VirtualMachine. etablering. DiskRandomAccess** (virtuell dator-> etablering-> Tillåt disk åtkomst): Tillåt att en disk öppnas på en virtuell dator för att läsa disken med hjälp av vddk.<br/><br/> - **VirtualMachine. Provisioning. GetVmFiles** (virtuell dator-> etablering-> Tillåt hämtning av virtuell dator): tillåter Läs åtgärder på filer som är associerade med en virtuell dator, för att ladda ned loggarna och felsöka om det uppstår fel.<br/><br/> - **VirtualMachine. State. \* *_ (Virtuell dator-> ögonblicks bilds hantering): Tillåt skapande och hantering av VM-ögonblicksbilder för replikering. <br/> <br/> -_* VirtualMachine. interagera. avstängnings läge** (virtuell dator-> interaktion-> avstängning): Tillåt att den virtuella datorn stängs av under migreringen till Azure.



### <a name="vm-requirements-agentless"></a>VM-krav (utan agent)

I tabellen sammanfattas kraven för att migrera utan agent för virtuella VMware-datorer.

**Support** | **Information**
--- | ---
**Operativ system som stöds** | Du kan migrera [Windows](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines) -och [Linux](../virtual-machines/linux/endorsed-distros.md) -operativsystem som stöds av Azure.
**Virtuella Windows-datorer i Azure** | Du kan behöva [göra några ändringar](prepare-for-migration.md#verify-required-changes-before-migrating) på virtuella datorer innan migreringen. 
**Virtuella Linux-datorer i Azure** | Vissa virtuella datorer kan kräva ändringar så att de kan köras i Azure.<br/><br/> För Linux gör Azure Migrate ändringarna automatiskt för dessa operativ system:<br/> -Red Hat Enterprise Linux 7,8, 7,7, 7,6, 7,5, 7,4, 7,0, 6. x<br/> -% OS 7,7, 7,6, 7,5, 7,4, 6. x</br> -SUSE Linux Enterprise Server 12 SP1 +<br/> – SUSE Linux Enterprise Server 15 SP1 <br/>-Ubuntu 19,04, 19,10, 14.04 LTS, 16.04 LTS, 18.04 LTS<br/> -Debian 7, 8 <br/> Oracle Linux 7,7, 7,7 – CI<br/> För andra operativ system gör du [nödvändiga ändringar](prepare-for-migration.md#verify-required-changes-before-migrating) manuellt.
**Linux-start** | Om/boot finns på en dedikerad partition bör den finnas på OS-disken och inte spridas över flera diskar.<br/> Om/Boot är en del av rot-partitionen (/) bör partitionen/-partitionen finnas på OS-disken och inte omfatta andra diskar.
**UEFI-start** | Stöds. UEFI-baserade virtuella datorer kommer att migreras till virtuella datorer i Azure generation 2. 
**Disk storlek** | 2 TB OS-disk;  32 TB för data diskar.
**Disk gränser** |  Upp till 60 diskar per virtuell dator.
**Krypterade diskar/volymer** | Virtuella datorer med krypterade diskar/volymer stöds inte för migrering.
**Delat disk kluster** | Stöds inte.
**Oberoende diskar** | Stöds inte.
**RDM/passthrough-diskar** | Om de virtuella datorerna har RDM-eller passthrough-diskar replikeras inte dessa diskar till Azure.
**NFS** | NFS-volymer som monterats som volymer på de virtuella datorerna replikeras inte.
**iSCSI-mål** | Virtuella datorer med iSCSI-mål stöds inte för migrering utan agent.
**Multipath i/o** | Stöds inte.
**Lagrings vMotion** | Stöds inte. Det går inte att använda replikering om en virtuell dator använder Storage vMotion.
**Grupperade nätverkskort** | Stöds inte.
**IPv6** | Stöds inte.
**Mål disk** | Virtuella datorer kan bara migreras till Managed disks (standard-HDD, standard SSD, Premium SSD) i Azure.
**Samtidig replikering** | 300 virtuella datorer per vCenter Server. Om du har fler kan du migrera dem i batchar på 300.
**Automatisk installation av Azure VM-agenten (Windows-agent)** | Stöds för Windows Server 2008 R2 och senare.

### <a name="appliance-requirements-agentless"></a>Installations krav (utan agent)

Vid migrering utan agent används [Azure Migrate-enheten](migrate-appliance.md). Du kan distribuera installationen som en virtuell VMware-dator med hjälp av en ägg-mall, som importeras till vCenter Server eller med ett [PowerShell-skript](deploy-appliance-script.md).

- Lär dig mer om installations [krav](migrate-appliance.md#appliance---vmware) för VMware.
- Lär dig mer om webb adresser som behövs för att få åtkomst till [offentliga](migrate-appliance.md#public-cloud-urls) [och offentliga](migrate-appliance.md#government-cloud-urls) moln.
- I Azure Government måste du distribuera enheten [med hjälp av skriptet](deploy-appliance-script-government.md).

### <a name="port-requirements-agentless"></a>Port krav (utan agent)

**Enhet** | **Anslutning**
--- | ---
Enhet | Utgående anslutningar på port 443 för att överföra replikerade data till Azure och för att kommunicera med Azure Migrate tjänster som dirigerar replikering och migrering.
vCenter Server | Inkommande anslutningar på port 443 för att tillåta att enheten dirigerar replikering – skapa ögonblicks bilder, kopiera data, versions ögonblicks bilder
vSphere/ESXI-värd | Inkommande på TCP-port 902 för att enheten ska replikera data från ögonblicks bilder.

## <a name="agent-based-migration"></a>Agent-baserad migrering 


I det här avsnittet sammanfattas kraven för agent-baserad migrering.


### <a name="vmware-requirements-agent-based"></a>VMware-krav (agent-baserad)

I den här tabellen sammanfattas utvärderings support och begränsningar för VMware virtualization-servrar.

**Krav för VMware** | **Information**
--- | ---
**VMware vCenter Server** | Version 5,5, 6,0, 6,5 eller 6,7.
**VMware vSphere ESXI-värd** | Version 5,5, 6,0, 6,5 eller 6,7.
**vCenter Server behörigheter** | Ett skrivskyddat konto för vCenter Server.

### <a name="vm-requirements-agent-based"></a>VM-krav (agent-baserad)

Tabellen sammanfattar VMware VM-stöd för virtuella VMware-datorer som du vill migrera med hjälp av en agent-baserad migrering.

**Support** | **Information**
--- | ---
**Dator arbets belastning** | Azure Migrate stöder migrering av arbets belastningar (t. ex. Active Directory, SQL Server osv.) som körs på en dator som stöds.
**Operativsystem** | Du hittar den senaste informationen i [operativ systemets stöd](../site-recovery/vmware-physical-azure-support-matrix.md#replicated-machines) för Site Recovery. Azure Migrate tillhandahåller identiskt stöd för virtuella dator operativ system.
**Linux-filsystem/gäst lagring** | Du hittar den senaste informationen i [Linux-filsystemets stöd](../site-recovery/vmware-physical-azure-support-matrix.md#linux-file-systemsguest-storage) för Site Recovery. Azure Migrate har samma stöd för Linux-filsystem.
**Nätverk/lagring** | För den senaste informationen granskar du kraven för [nätverk](../site-recovery/vmware-physical-azure-support-matrix.md#network) och [lagring](../site-recovery/vmware-physical-azure-support-matrix.md#storage) för Site Recovery. Azure Migrate tillhandahåller identiska nätverks-/lagrings krav.
**Krav för Azure** | Du hittar den senaste informationen i [Azure-nätverket](../site-recovery/vmware-physical-azure-support-matrix.md#azure-vm-network-after-failover), [lagrings](../site-recovery/vmware-physical-azure-support-matrix.md#azure-storage)-och [beräknings](../site-recovery/vmware-physical-azure-support-matrix.md#azure-compute) kraven för Site Recovery. Azure Migrate har identiska krav för VMware-migrering.
**Mobilitetstjänsten** | Mobilitets tjänst agenten måste vara installerad på varje virtuell dator som du vill migrera.
**UEFI-start** | Stöds. UEFI-baserade virtuella datorer kommer att migreras till virtuella datorer i Azure generation 2. 
**UEFI – säker start**         | Stöds inte för migrering.
**Mål disk** | Virtuella datorer kan bara migreras till Managed disks (standard-HDD, standard SSD, Premium SSD) i Azure.
**Disk storlek** | 2 TB OS-disk; 32 TB för data diskar.
**Disk gränser** |  Upp till 63 diskar per virtuell dator.
**Krypterade diskar/volymer** | Virtuella datorer med krypterade diskar/volymer stöds inte för migrering.
**Delat disk kluster** | Stöds inte.
**Oberoende diskar** | Stöds.
**Genom strömnings diskar** | Stöds.
**NFS** | NFS-volymer som monterats som volymer på de virtuella datorerna replikeras inte.
**iSCSI-mål** | Stöds.
**Multipath i/o** | Stöds inte.
**Lagrings vMotion** | Stöds
**Grupperade nätverkskort** | Stöds inte.
**IPv6** | Stöds inte.




### <a name="appliance-requirements-agent-based"></a>Krav på utrustning (agent-baserad)

När du ställer in replikeringen med hjälp av den ägg-mall som finns i Azure Migrate Hub, kör enheten Windows Server 2016 och uppfyller support kraven. Om du konfigurerar replikeringen manuellt på en fysisk server kontrollerar du att den uppfyller kraven.

- Lär dig mer om [krav för replikering](migrate-replication-appliance.md#appliance-requirements) av VMware.
- MySQL måste vara installerat på enheten. Lär dig mer om [installations alternativ](migrate-replication-appliance.md#mysql-installation).
- Lär dig mer om webb adresser som krävs för att få åtkomst i [offentliga](migrate-replication-appliance.md#url-access) [och offentliga](migrate-replication-appliance.md#azure-government-url-access) moln.
- Granska de [portar](migrate-replication-appliance.md#port-access) som krävs för att få åtkomst till replikerings enheten.

### <a name="port-requirements-agent-based"></a>Port krav (agent-baserad)

**Enhet** | **Anslutning**
--- | ---
Virtuella datorer | Mobilitets tjänsten som körs på virtuella datorer kommunicerar med den lokala replikerings enheten (konfigurations servern) på port HTTPS 443 inkommande, för hantering av replikering.<br/><br/> Virtuella datorer skickar replikeringsdata till processervern (körs på konfigurations serverdatorn) på port HTTPS 9443 inkommande. Den här porten kan ändras.
Replikeringsfil | Replikeringstjänsten dirigerar replikering med Azure över Port HTTPS 443 utgående.
Processerver | Processervern tar emot replikeringsdata, optimerar och krypterar den och skickar den till Azure Storage via port 443 utgående.<br/> Som standard körs processervern på replikerings enheten.

## <a name="azure-vm-requirements"></a>Virtuella Azure VMware-datorer

Alla lokala virtuella datorer som replikeras till Azure (med agent lös eller agent-baserad migrering) måste uppfylla de krav för virtuella Azure-datorer som sammanfattas i den här tabellen. 

**Komponent** | **Krav** 
--- | --- | ---
Gästoperativsystem | Verifierar att virtuella VMware-operativsystem stöds för migrering.<br/> Du kan migrera alla arbets belastningar som körs på ett operativ system som stöds. 
Gäst operativ systemets arkitektur | 64-bitars. 
Storlek på operativsystemdisk | Upp till 2 048 GB. 
Antal operativsystemdiskar | 1 
Antal datadiskar | 64 eller mindre. 
Data disk storlek | Upp till 32 TB
Nätverkskort | Flera nätverkskort stöds.
Delad VHD | Stöds inte. 
FC-disk | Stöds inte. 
BitLocker | Stöds inte.<br/><br/> BitLocker måste inaktive ras innan du migrerar datorn.
VM-namn | Mellan 1 och 63 tecken.<br/><br/> Begränsat till bokstäver, siffror och bindestreck.<br/><br/> Dator namnet måste börja och sluta med en bokstav eller en siffra. 
Anslut efter migreringen – Windows | Ansluta till virtuella Azure-datorer som kör Windows efter migrering:<br/><br/> -Innan migrering aktiverar du RDP på den lokala virtuella datorn.<br/><br/> Kontrollera att TCP- och UDP-regler har lagts till för den **offentliga** profilen och att RDP tillåts i **Windows-brandväggen** > **Tillåtna appar** för alla profiler.<br/><br/> För plats-till-plats-VPN-åtkomst aktiverar du RDP och tillåter RDP i **Windows-brandväggen**  ->  **tillåtna appar och funktioner** för **domän nätverk och privata** nätverk.<br/><br/> Dessutom kontrollerar du att operativ systemets SAN-princip är inställd på **OnlineAll**. [Läs mer](prepare-for-migration.md).
Anslut efter migreringen – Linux | Ansluta till virtuella Azure-datorer efter migrering med SSH:<br/><br/> Innan migreringen går du till den lokala datorn, kontrollerar att tjänsten Secure Shell är inställt på Start och att brand Väggs reglerna tillåter en SSH-anslutning.<br/><br/> Efter redundansväxlingen på den virtuella Azure-datorn tillåter du inkommande anslutningar till SSH-porten för reglerna för nätverks säkerhets grupper på den misslyckade virtuella datorn och för det Azure-undernät som den är ansluten till.<br/><br/> Lägg också till en offentlig IP-adress för den virtuella datorn.  


## <a name="next-steps"></a>Nästa steg

[Välj](server-migrate-overview.md) ett alternativ för VMware-migrering.
