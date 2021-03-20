---
title: Stöd för Hyper-V-migrering i Azure Migrate
description: Läs mer om stöd för Hyper-V-migrering med Azure Migrate.
author: bsiva
ms.author: bsiva
ms.manager: abhemraj
ms.topic: conceptual
ms.date: 04/15/2020
ms.openlocfilehash: 90da16789344754c02d46022160db71ee261a056
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96754070"
---
# <a name="support-matrix-for-hyper-v-migration"></a>Support mat ris för Hyper-V-migrering

I den här artikeln sammanfattas support inställningar och begränsningar för migrering av virtuella Hyper-V-datorer med [Azure Migrate: Server-migrering](migrate-services-overview.md#azure-migrate-server-migration-tool) . Om du vill ha information om hur du bedömer virtuella Hyper-V-datorer för migrering till Azure läser du avsnittet om [utvärderings support](migrate-support-matrix-hyper-v.md).

## <a name="migration-limitations"></a>Migreringsbegränsningar

Du kan välja upp till 10 virtuella datorer på en gång för replikering. Om du vill migrera fler datorer replikerar du i grupper om 10.


## <a name="hyper-v-host-requirements"></a>Krav för Hyper-V-värd

| **Support**                | **Information**               
| :-------------------       | :------------------- |
| **Distribution**       | Hyper-V-värden kan vara fristående eller distribuerad i ett kluster. <br/>Azure Migrate Replication-programvara (Hyper-V-Replikeringsprovider) är installerad på Hyper-V-värdarna.|
| **Behörigheter**           | Du behöver administratörs behörighet på Hyper-V-värden. |
| **Värd operativ system** | Windows Server 2019, Windows Server 2016 eller Windows Server 2012 R2 med de senaste uppdateringarna. Observera att Server Core-installation av dessa operativ system också stöds. |
| **Andra program varu krav** | .NET Framework 4,7 eller senare |
| **Port åtkomst** |  Utgående anslutningar på HTTPS-port 443 för att skicka data för VM-replikering.
| **Ledigt disk utrymme (cache)** |  600 GB |
| **Ledigt disk utrymme (kvarhållning av disk)** |  600 GB |


## <a name="hyper-v-vms"></a>Hyper-V:s virtuella datorer

| **Support**                  | **Information**               
| :----------------------------- | :------------------- |
| **Operativsystem** | Alla [Windows](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines) -och [Linux](../virtual-machines/linux/endorsed-distros.md) -operativsystem som stöds av Azure. |
**Windows Server 2003** | För virtuella datorer som kör Windows Server 2003 måste du [Installera Hyper-V Integration Services](prepare-windows-server-2003-migration.md) innan du migrerar. | 
**Virtuella Linux-datorer i Azure** | Vissa virtuella datorer kan kräva ändringar så att de kan köras i Azure.<br/><br/> För Linux gör Azure Migrate ändringarna automatiskt för dessa operativ system:<br/> -Red Hat Enterprise Linux 7,8, 7,7, 7,6, 7,5, 7,4, 7,0, 6. x<br/> -% OS 7,7, 7,6, 7,5, 7,4, 6. x</br> -SUSE Linux Enterprise Server 12 SP1 +<br/> – SUSE Linux Enterprise Server 15 SP1 <br/>-Ubuntu 19,04, 19,10, 14.04 LTS, 16.04 LTS, 18.04 LTS<br/> -Debian 7, 8 <br/> Oracle Linux 7,7, 7,7 – CI<br/> För andra operativ system gör du [nödvändiga ändringar](prepare-for-migration.md#verify-required-changes-before-migrating) manuellt.
| **Nödvändiga ändringar för Azure** | Vissa virtuella datorer kan kräva ändringar så att de kan köras i Azure. Gör justeringar manuellt innan migreringen. Relevanta artiklar innehåller instruktioner om hur du gör detta. |
| **Linux-start**                 | Om/boot finns på en dedikerad partition bör den finnas på OS-disken och inte spridas över flera diskar.<br/> Om/Boot är en del av rot-partitionen (/) bör partitionen/-partitionen finnas på OS-disken och inte omfatta andra diskar. |
| **UEFI-start**                  | Stöds. UEFI-baserade virtuella datorer kommer att migreras till virtuella datorer i Azure generation 2.  |
| **UEFI – säker start**         | Stöds inte för migrering.|
| **Disk storlek**                  | 2 TB för OS-disken (BIOS-start), 4 TB för OS-disken (UEFI-start), 4 TB för data diskarna.|
| **Disk nummer** | Högst 16 diskar per virtuell dator.|
| **Krypterade diskar/volymer**    | Stöds inte för migrering.|
| **RDM/passthrough-diskar**      | Stöds inte för migrering.|
| **Delad disk** | Virtuella datorer som använder delade diskar stöds inte för migrering.|
| **NFS**                        | NFS-volymer som monterats som volymer på de virtuella datorerna replikeras inte.|
| **-**                      | Virtuella datorer med iSCSI-mål stöds inte för migrering.
| **Mål disk**                | Du kan bara migrera till virtuella Azure-datorer med Managed disks. |
| **IPv6** | Stöds inte.|
| **NIC-teamning** | Stöds inte.|
| **Azure Site Recovery** | Du kan inte replikera med Azure Migrate Server-migrering om den virtuella datorn är aktive rad för replikering med Azure Site Recovery.|
| **Portar** | Utgående anslutningar på HTTPS-port 443 för att skicka data för VM-replikering.|

### <a name="url-access-public-cloud"></a>URL-åtkomst (offentligt moln)

Providerns program vara på Hyper-V-värdarna måste ha åtkomst till dessa URL: er.

**URL** | **Information**
--- | ---
login.microsoftonline.com | Åtkomst kontroll och identitets hantering med hjälp av Active Directory.
backup.windowsazure.com | Överföring och samordning av replikeringsdata.
*.hypervrecoverymanager.windowsazure.com | Används för hantering av replikering.
*.blob.core.windows.net | Ladda upp data till lagrings konton. 
dc.services.visualstudio.com | Ladda upp program loggar som används för intern övervakning.
time.windows.com | Verifierar tidssynkronisering mellan system och global tid.

### <a name="url-access-azure-government"></a>URL-åtkomst (Azure Government)

Providerns program vara på Hyper-V-värdarna måste ha åtkomst till dessa URL: er.

**URL** | **Information**
--- | ---
login.microsoftonline.us | Åtkomst kontroll och identitets hantering med hjälp av Active Directory.
backup.windowsazure.us | Överföring och samordning av replikeringsdata.
*. hypervrecoverymanager.windowsazure.us | Används för hantering av replikering.
*. blob.core.usgovcloudapi.net | Ladda upp data till lagrings konton.
dc.services.visualstudio.com | Ladda upp program loggar som används för intern övervakning.
time.nist.gov | Verifierar tidssynkronisering mellan system och global tid.

## <a name="azure-vm-requirements"></a>Virtuella Azure VMware-datorer

Alla lokala virtuella datorer som replikeras till Azure måste uppfylla de krav för virtuella Azure-datorer som sammanfattas i den här tabellen.

**Komponent** | **Krav** | **Information**
--- | --- | ---
Storlek på operativsystemdisk | Upp till 2 048 GB. | Kontrollen Miss lyckas om den inte stöds.
Antal operativsystemdiskar | 1 | Kontrollen Miss lyckas om den inte stöds.
Antal datadiskar | 16 eller mindre. | Kontrollen Miss lyckas om den inte stöds.
Data disk storlek | Upp till 4 095 GB | Kontrollen Miss lyckas om den inte stöds.
Nätverkskort | Flera nätverkskort stöds. |
Delad VHD | Stöds inte. | Kontrollen Miss lyckas om den inte stöds.
FC-disk | Stöds inte. | Kontrollen Miss lyckas om den inte stöds.
BitLocker | Stöds inte. | BitLocker måste inaktive ras innan du aktiverar replikering för en dator.
VM-namn | Mellan 1 och 63 tecken.<br/> Begränsat till bokstäver, siffror och bindestreck.<br/><br/> Dator namnet måste börja och sluta med en bokstav eller en siffra. |  Uppdatera värdet i dator egenskaperna i Site Recovery.
Anslut efter migreringen – Windows | Ansluta till virtuella Azure-datorer som kör Windows efter migrering:<br/><br/> -Innan migrering aktiverar du RDP på den lokala virtuella datorn. Kontrollera att TCP- och UDP-regler har lagts till för den **offentliga** profilen och att RDP tillåts i **Windows-brandväggen** > **Tillåtna appar** för alla profiler.<br/><br/> För plats-till-plats-VPN-åtkomst, aktivera RDP och Tillåt RDP i **Windows-brandväggen**  ->  **tillåtna appar och funktioner** för **domän nätverk och privata** nätverk. Dessutom kontrollerar du att operativ systemets SAN-princip är inställd på **OnlineAll**. [Läs mer](prepare-for-migration.md). |
Anslut efter migreringen – Linux | Ansluta till virtuella Azure-datorer efter migrering med SSH:<br/><br/> – Innan migreringen, på den lokala datorn, kontrollerar du att tjänsten Secure Shell är inställd på Start och att brand Väggs reglerna tillåter en SSH-anslutning.<br/><br/> – Efter migreringen kan du på den virtuella Azure-datorn tillåta inkommande anslutningar till SSH-porten för reglerna för nätverks säkerhets grupper på den misslyckade virtuella datorn och för det Azure-undernät som den är ansluten till. Lägg också till en offentlig IP-adress för den virtuella datorn. |  

## <a name="next-steps"></a>Nästa steg

[Migrera virtuella Hyper-V-datorer](tutorial-migrate-hyper-v.md) för migrering.
