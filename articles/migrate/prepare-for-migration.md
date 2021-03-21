---
title: Förbered datorer för migrering med Azure Migrate
description: Lär dig hur du förbereder lokala datorer för migrering med Azure Migrate.
author: anvar-ms
ms.author: anvar
ms.manager: bsiva
ms.topic: how-to
ms.date: 06/08/2020
ms.openlocfilehash: 8083b9edd49f65f29fe9c9b2cfa30edfacf89507
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102614895"
---
# <a name="prepare-on-premises-machines-for-migration-to-azure"></a>Förbered lokala datorer för migrering till Azure

Den här artikeln beskriver hur du förbereder lokala datorer innan du migrerar dem till Azure med hjälp av [Azure Migrate: Migreringsverktyg för Server](migrate-services-overview.md#azure-migrate-server-migration-tool) .

Den här artikeln innehåller följande avsnitt:
> [!div class="checklist"]
> * Granska begränsningar för migrering.
> * Välj en metod för migrering av virtuella VMware-datorer
> * Kontrol lera hypervisor-och operativ system krav för datorer som du vill migrera.
> * Granska URL och port åtkomst för datorer som du vill migrera.
> * Granska ändringar som du kan behöva göra innan du påbörjar migreringen.
> * Kontrol lera kraven för virtuella Azure-datorer för migrerade datorer
> * Förbered datorer så att du kan ansluta till de virtuella Azure-datorerna efter migreringen.



## <a name="verify-migration-limitations"></a>Verifiera begränsningar för migrering

Tabellen sammanfattar identifierings-, utvärderings-och migrations gränser för Azure Migrate. Vi rekommenderar att du bedömer datorer innan migrering, men du behöver inte.

**Scenario** | **Project** | **Identifiering/bedömning** | **Migrering**
--- | --- | --- | ---
**VMwares virtuella datorer** | Identifiera och utvärdera upp till 35 000 virtuella datorer i ett enda Azure Migrate projekt. | Identifiera upp till 10 000 virtuella VMware-datorer med en enda [Azure Migrate-apparat](common-questions-appliance.md) för VMware. | **Migrering utan agent**: du kan samtidigt replikera högst 500 virtuella datorer från varje vCenter Server. **Agent-baserad migrering**: du kan [skala ut](./agent-based-migration-architecture.md#performance-and-scaling) [replikeringen](migrate-replication-appliance.md) för att replikera ett stort antal virtuella datorer.<br/><br/> I portalen kan du välja upp till 10 datorer på en gång för replikering. Om du vill replikera fler datorer lägger du till i batchar med 10.
**Hyper-V:s virtuella datorer** | Identifiera och utvärdera upp till 35 000 virtuella datorer i ett enda Azure Migrate projekt. | Identifiera upp till 5 000 virtuella Hyper-V-datorer med en enda Azure Migrate-apparat | En apparat används inte för Hyper-V-migrering. I stället körs Hyper-V-Replikeringsprovidern på varje Hyper-V-värd.<br/><br/> Replikeringens kapacitet påverkas av prestanda faktorer som VM-omsättning och uppladdning av bandbredd för replikeringsdata.<br/><br/> I portalen kan du välja upp till 10 datorer på en gång för replikering. Om du vill replikera fler datorer lägger du till i batchar med 10.
**Fysiska datorer** | Identifiera och utvärdera upp till 35 000 datorer i ett enda Azure Migrate projekt. | Identifiera upp till 250 fysiska servrar med en enda Azure Migrate-apparat för fysiska servrar. | Du kan [skala ut](./agent-based-migration-architecture.md#performance-and-scaling) [replikeringen](migrate-replication-appliance.md) för att replikera ett stort antal servrar.<br/><br/> I portalen kan du välja upp till 10 datorer på en gång för replikering. Om du vill replikera fler datorer lägger du till i batchar med 10.

## <a name="select-a-vmware-migration-method"></a>Välj en VMware-migrations metod

Om du migrerar virtuella VMware-datorer till Azure bör du [jämföra](server-migrate-overview.md#compare-migration-methods) de metoder som används för att avgöra vad som passar dig bäst om du använder de metoder som är baserade på agent lös

## <a name="verify-hypervisor-requirements"></a>Verifiera hypervisor-krav

- Verifiera [VMware agent](migrate-support-matrix-vmware-migration.md#vmware-requirements-agentless)-undantagna eller [VMware agent-baserade](migrate-support-matrix-vmware-migration.md#vmware-requirements-agent-based) krav.
- Kontrol lera kraven för [Hyper-V-värden](migrate-support-matrix-hyper-v-migration.md#hyper-v-host-requirements) .


## <a name="verify-operating-system-requirements"></a>Kontrol lera krav för operativ system

Kontrol lera att operativ system som stöds för migrering:

- Om du migrerar virtuella VMware-datorer eller virtuella Hyper-V-datorer bör du kontrol lera de virtuella VMware-datorerna för [agent](migrate-support-matrix-vmware-migration.md#vm-requirements-agentless)utan agent [-baserad](migrate-support-matrix-vmware-migration.md#vm-requirements-agent-based) migrering och krav för [virtuella Hyper-v-datorer](migrate-support-matrix-hyper-v-migration.md#hyper-v-vms).
- Kontrol lera att [Windows-operativsystem](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines) stöds i Azure.
- Verifiera [Linux-distributioner](../virtual-machines/linux/endorsed-distros.md) som stöds i Azure.

## <a name="review-url-and-port-access"></a>Granska URL-och port åtkomst

Granska vilka URL: er och portar som nås under migreringen.

**Scenario** | **Information** |  **Webbadresser** | **Portar**
--- | --- | --- | ---
**VMware-agent utan migrering** | Använder [Azure Migrate-enheten](migrate-appliance-architecture.md) för migrering. Ingenting installeras på virtuella VMware-datorer. | Granska de offentliga moln-och myndighets- [URL: erna](migrate-appliance.md#url-access) som krävs för identifiering, utvärdering och migrering med produkten. | [Granska](migrate-support-matrix-vmware-migration.md#port-requirements-agentless) port kraven för migrering utan agent.
**VMware-agent-baserad migrering** | Använder [replikeringstjänsten](migrate-replication-appliance.md) för migrering. Mobilitets tjänst agenten är installerad på virtuella datorer. | Granska det [offentliga molnet](migrate-replication-appliance.md#url-access) och [Azure Government](migrate-replication-appliance.md#azure-government-url-access) de URL: er som krävs för att få åtkomst till replikerings enheten. | [Granska](migrate-replication-appliance.md#port-access) portarna som används vid agent-baserad migrering.
**Hyper-V-migrering** | Använder en provider som är installerad på Hyper-V-värdar för migrering. Ingenting installeras på virtuella Hyper-V-datorer. | Granska det [offentliga molnet](migrate-support-matrix-hyper-v-migration.md#url-access-public-cloud) och [Azure Government](migrate-support-matrix-hyper-v-migration.md#url-access-azure-government) webb adresser som replikeringsprovidern som körs på värdarna måste ha åtkomst till. | Replikeringsprovidern på Hyper-V-värden använder utgående anslutningar på HTTPS-port 443 för att skicka data för VM-replikering.
**Fysiska datorer** | Använder [replikeringstjänsten](migrate-replication-appliance.md) för migrering. Mobilitets tjänst agenten är installerad på de fysiska datorerna. | Granska det [offentliga molnet](migrate-replication-appliance.md#url-access) och [Azure Government](migrate-replication-appliance.md#azure-government-url-access) de URL: er som krävs för att få åtkomst till replikerings enheten. | [Granska](migrate-replication-appliance.md#port-access) portarna som används vid fysisk migrering.

## <a name="verify-required-changes-before-migrating"></a>Verifiera nödvändiga ändringar innan du migrerar

Vissa ändringar krävs på virtuella datorer innan du migrerar dem till Azure.

- För vissa operativ system gör Azure Migrate ändringar automatiskt under processen för replikering/migrering.
- För andra operativ system måste du konfigurera inställningarna manuellt.
- Det är viktigt att konfigurera inställningar manuellt innan du påbörjar migreringen. Om du migrerar den virtuella datorn innan du gör ändringen kanske den virtuella datorn inte startar i Azure.

Granska tabellerna för att identifiera de ändringar du behöver göra.

### <a name="windows-machines"></a>Windows-datorer

Nödvändiga ändringar sammanfattas i tabellen.

**Åtgärd** | **VMware (utan agent migrering)** | **VMware (agent-based)/Physical-datorer** | **Windows på Hyper-V** 
--- | --- | --- | ---
**Konfigurera SAN-principen som online alla**<br/><br/> Detta säkerställer att Windows-volymer i Azure VM använder samma enhets beteckningar som den lokala virtuella datorn. | Ställ in automatiskt för datorer som kör Windows Server 2008 R2 eller senare.<br/><br/> Konfigurera manuellt för tidigare operativ system. | Ställ in automatiskt i de flesta fall. | Konfigurera manuellt.
**Installera Hyper-V-gäst integrering** | [Installera manuellt](prepare-windows-server-2003-migration.md#install-on-vmware-vms) på datorer som kör Windows Server 2003. | [Installera manuellt](prepare-windows-server-2003-migration.md#install-on-vmware-vms) på datorer som kör Windows Server 2003. | [Installera manuellt](prepare-windows-server-2003-migration.md#install-on-hyper-v-vms) på datorer som kör Windows Server 2003.
**Aktivera Azures serie konsol**.<br/><br/>[Aktivera-konsolen](../virtual-machines/troubleshooting/serial-console-windows.md) på virtuella Azure-datorer så att du kan felsöka. Du behöver inte starta om den virtuella datorn. Den virtuella Azure-datorn startas med disk avbildningen. Disk avbildningens start motsvarar en omstart för den nya virtuella datorn. | Aktivera manuellt | Aktivera manuellt | Aktivera manuellt
**Anslut efter migrering**<br/><br/> För att ansluta efter migreringen finns det ett antal steg som du bör vidta innan du migrerar. | [Konfigurera](#prepare-to-connect-to-azure-windows-vms) manuellt. | [Konfigurera](#prepare-to-connect-to-azure-windows-vms) manuellt. | [Konfigurera](#prepare-to-connect-to-azure-windows-vms) manuellt.


#### <a name="configure-san-policy"></a>Konfigurera SAN-princip

Som standard tilldelas virtuella Azure-datorer enhet D som används som tillfällig lagring.

- Den här enhets tilldelningen gör att alla andra anslutna lagrings enhets tilldelningar ökar med en bokstav.
- Om din lokala installation t. ex. använder en datadisk som är tilldelad enhet D för programinstallationer, ökar tilldelningen för den här enheten till E efter det att du har migrerat den virtuella datorn till Azure. 
- För att förhindra denna automatiska tilldelning och för att säkerställa att Azure tilldelar nästa lediga enhets beteckning till dess tillfälliga volym, ställer du in storage area network (SAN)-principen på **OnlineAll**:

Konfigurera den här inställningen manuellt på följande sätt:

1. På den lokala datorn (inte värd servern) öppnar du en upphöjd kommando tolk.
2. Ange **DiskPart**.
3. Ange **San**. Om enhets beteckningen för gäst operativ systemet inte underhålls, returneras **offline all** eller **offline delad** .
4. Ange **San-princip = OnlineAll** i **DiskPart** -prompten. Den här inställningen säkerställer att diskarna är online och att du kan läsa och skriva till båda diskarna.
5. Under testmigreringen kan du kontrol lera att enhets beteckningarna bevaras.


### <a name="linux-machines"></a>Linux-datorer

Azure Migrate slutför de här åtgärderna automatiskt för dessa versioner

- Red Hat Enterprise Linux 7,8, 7,7, 7,6, 7,5, 7,4, 7,0, 6. x (Azure Linux VM-agenten installeras också automatiskt under migreringen)
- % OS 7,7, 7,6, 7,5, 7,4, 6. x (Azure Linux VM-agenten installeras också automatiskt under migreringen)
- SUSE Linux Enterprise Server 12 SP1 +
- SUSE Linux Enterprise Server 15 SP1
- Ubuntu 19,04, 19,10, 18.04 LTS, 16.04 LTS, 14.04 LTS (Azure Linux VM-agenten installeras också automatiskt under migreringen)
- Ubuntu 18.04 LTS, 16.04 LTS
- Debian 9, 8, 7
- Oracle Linux 7,7, 7,7 – CI

För andra versioner förbereder du datorer så att de sammanfattas i tabellen.  


**Åtgärd** | **Information** | **Linux-version**
--- | --- | ---
**Installera Hyper-V Linux Integration Services** | Återskapa Linux init-avbildningen så att den innehåller de nödvändiga Hyper-V-drivrutinerna. Om du återskapar init-avbildningen ser du till att den virtuella datorn startar i Azure. | De flesta nya versioner av Linux-distributioner har detta inkluderat som standard.<br/><br/> Om den inte ingår installerar du manuellt för alla versioner utom de som anropas ovan.
**Aktivera loggning av Azures serie konsol** | Genom att aktivera konsol loggning kan du felsöka. Du behöver inte starta om den virtuella datorn. Den virtuella Azure-datorn startas med disk avbildningen. Disk avbildningens start motsvarar en omstart för den nya virtuella datorn.<br/><br/> Följ [dessa instruktioner](../virtual-machines/troubleshooting/serial-console-linux.md) för att aktivera.
**Uppdatera enhets mappnings fil** | Uppdatera enhets mappnings filen med enhets namn-till-volym-associationerna, så att du kan använda beständiga enhets identifierare. | Installera manuellt för alla versioner utom de som anropas ovan. (Endast tillämpligt i agent-baserade VMware-scenario)
**Uppdatera fstab-poster** |  Uppdatera poster för att använda beständiga volym identifierare.    | Uppdatera manuellt för alla versioner utom de som anropas ovan.
**Ta bort udev-regel** | Ta bort alla udev-regler som reserverar gränssnitts namn baserat på Mac-adress osv. | Ta bort manuellt för alla versioner utom de som anropas ovan.
**Uppdatera nätverks gränssnitt** | Uppdatera nätverks gränssnitt för att ta emot IP-adresser baserat på DHCP. nst | Uppdatera manuellt för alla versioner utom de som anropas ovan.
**Aktivera SSH** | Se till att SSH är aktiverat och att sshd-tjänsten är inställd på att starta automatiskt vid omstart.<br/><br/> Se till att inkommande anslutningar för ssh-anslutning inte blockeras av OS-brandväggen eller skript bara regler.| Aktivera manuellt för alla versioner utom de som anropas ovan.

I följande tabell sammanfattas de steg som utförs automatiskt för de operativ system som anges ovan.


| Action                                      | Agent \- baserad VMware-migrering | Migrering utan agent | Hyper\-V   |
|---------------------------------------------|-------------------------------|----------------------------|------------|
| Installera Hyper- \- V Linux Integration Services | Ja                           | Ja                        | Krävs inte |
| Aktivera loggning av Azures serie konsol         | Ja                           | Ja                        | Inga         |
| Uppdatera enhets mappnings fil                      | Ja                           | Inga                         | Inga         |
| Uppdatera fstab-poster                        | Ja                           | Ja                        | Inga         |
| Ta bort udev-regel                            | Ja                           | Ja                        | Inga         |
| Uppdatera nätverks gränssnitt                   | Ja                           | Ja                        | Inga         |
| Aktivera SSH                                  | Inga                            | Inga                         | Inga         |

Lär dig mer om hur du [kör en virtuell Linux-dator på Azure](../virtual-machines/linux/create-upload-generic.md)och få instruktioner för några av de populära Linux-distributionerna.

Granska listan med [nödvändiga paket](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-linux#requirements) för att installera Linux VM-agenten. Azure Migrate installerar Linux VM-agenten automatiskt för RHEL6, RHEL7, CentOS7 (6 bör stödjas på liknande sätt som RHEL), Ubuntu 14,04, Ubuntu 16,04, Ubuntu 18.04 när du använder en agent lös metod för VMware-migrering.

## <a name="check-azure-vm-requirements"></a>Kontrol lera kraven för virtuella Azure-datorer

Lokala datorer som du replikerar till Azure måste uppfylla kraven på virtuella Azure-datorer för operativ system och arkitektur, diskar, nätverks inställningar och namngivning av virtuella datorer.

Innan du migrerar bör du gå igenom kraven för virtuella Azure-datorer för [VMware](migrate-support-matrix-vmware-migration.md#azure-vm-requirements), [Hyper-V](migrate-support-matrix-hyper-v-migration.md#azure-vm-requirements)och migrering av [fysiska servrar](migrate-support-matrix-physical-migration.md#azure-vm-requirements) .



## <a name="prepare-to-connect-after-migration"></a>Förbered för att ansluta efter migreringen

Virtuella Azure-datorer skapas under migreringen till Azure. Efter migreringen måste du kunna ansluta till de nya virtuella Azure-datorerna. Flera steg krävs för att kunna ansluta.

### <a name="prepare-to-connect-to-azure-windows-vms"></a>Förbereda för att ansluta till virtuella Azure Windows-datorer

På lokala Windows-datorer:

1. Konfigurera Windows-inställningar. Inställningarna inkluderar borttagning av alla statiska beständiga vägar eller WinHTTP-proxy.
2. Kontrol lera att de [nödvändiga tjänsterna](../virtual-machines/windows/prepare-for-upload-vhd-image.md#check-the-windows-services) körs.
3. Aktivera fjärr skrivbord (RDP) om du vill tillåta fjärr anslutningar till den lokala datorn. Lär dig hur du [använder PowerShell för att aktivera RDP](../virtual-machines/windows/prepare-for-upload-vhd-image.md#update-remote-desktop-registry-settings).
4. För att få åtkomst till en virtuell Azure-dator via Internet efter migreringen, i Windows-brandväggen på den lokala datorn, Tillåt TCP och UDP i den offentliga profilen och ange RDP som en tillåten app för alla profiler.
5. Om du vill få åtkomst till en virtuell Azure-dator via en plats-till-plats-VPN efter migreringen, kan du i Windows-brandväggen på den lokala datorn tillåta RDP för domänen och privata profiler. Lär dig hur du [tillåter RDP-trafik](../virtual-machines/windows/prepare-for-upload-vhd-image.md#configure-windows-firewall-rules). 
6. Se till att inga Windows-uppdateringar väntar på den lokala virtuella datorn när du migrerar. Om så är fallet kan uppdateringar börja installeras på den virtuella Azure-datorn efter migreringen och du kan inte logga in på den virtuella datorn förrän uppdateringarna har slutförts.


### <a name="prepare-to-connect-with-linux-azure-vms"></a>Förbered för att ansluta till virtuella Linux Azure-datorer

På lokala Linux-datorer:

1. Kontrol lera att Secure Shell-tjänsten är inställd på att starta automatiskt vid system start.
2. Kontrollera att brandväggsreglerna tillåter en SSH-anslutning.

### <a name="configure-azure-vms-after-migration"></a>Konfigurera virtuella Azure-datorer efter migrering

Efter migreringen slutför du de här stegen på de virtuella Azure-datorer som skapas:

1. Om du vill ansluta till den virtuella datorn via Internet tilldelar du en offentlig IP-adress till den virtuella datorn. Du måste använda en annan offentlig IP-adress för den virtuella Azure-datorn än du använde för din lokala dator. [Läs mer](../virtual-network/virtual-network-public-ip-address.md).
2. Kontrol lera att reglerna för nätverks säkerhets gruppen (NSG) på den virtuella datorn tillåter inkommande anslutningar till RDP-eller SSH-porten.
3. Kontrol lera [startdiagnostik](../virtual-machines/troubleshooting/boot-diagnostics.md#enable-boot-diagnostics-on-existing-virtual-machine) för att visa den virtuella datorn.


## <a name="next-steps"></a>Nästa steg

Bestäm vilken metod du vill använda för att [migrera virtuella VMware-datorer](server-migrate-overview.md) till Azure, eller börja migrera virtuella [Hyper-V-datorer](tutorial-migrate-hyper-v.md) eller [fysiska servrar eller virtuella datorer eller virtuella datorer i molnet](tutorial-migrate-physical-virtual-machines.md).

## <a name="see-whats-supported"></a>Se vad som stöds

För virtuella VMware-datorer stöder Server migreringen utan [agent eller med agent-baserad migrering](server-migrate-overview.md).

- **Virtuella VMware-datorer**: kontrol lera [kraven för migrering och stöd](migrate-support-matrix-vmware-migration.md) för virtuella VMware-datorer.
- **Virtuella Hyper-v-datorer**: kontrol lera [kraven för migrering och stöd](migrate-support-matrix-hyper-v-migration.md) för virtuella Hyper-v-datorer.
- **Fysiska datorer**: kontrol lera [kraven för migrering och stöd](migrate-support-matrix-physical-migration.md) för lokala fysiska datorer och andra virtualiserade servrar. 
