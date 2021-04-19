---
title: Stöd för Hyper-V-migrering i Azure Migrate
description: Lär dig mer om stöd för Hyper-V-migrering med Azure Migrate.
author: bsiva
ms.author: bsiva
ms.manager: abhemraj
ms.topic: conceptual
ms.date: 04/15/2020
ms.openlocfilehash: 33e34e777a78e1c609d2eacdcb501c0bce1f5c9d
ms.sourcegitcommit: 3ed0f0b1b66a741399dc59df2285546c66d1df38
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/19/2021
ms.locfileid: "107714928"
---
# <a name="support-matrix-for-hyper-v-migration"></a>Stödmatris för Hyper-V-migrering

I den här artikeln sammanfattas stödinställningar och begränsningar för att migrera virtuella Hyper-V-datorer [Azure Migrate: Server Migration](migrate-services-overview.md#azure-migrate-server-migration-tool) . Om du vill ha information om att utvärdera virtuella Hyper-V-datorer för migrering till Azure kan du läsa [utvärderingsstödmatrisen](migrate-support-matrix-hyper-v.md).

## <a name="migration-limitations"></a>Migreringsbegränsningar

Du kan välja upp till 10 virtuella datorer samtidigt för replikering. Om du vill migrera fler datorer replikerar du i grupper om 10.


## <a name="hyper-v-host-requirements"></a>Hyper-V-värdkrav

| **Support**                | **Information**               
| :-------------------       | :------------------- |
| **Distribution**       | Hyper-V-värden kan vara fristående eller distribuerad i ett kluster. <br/>Azure Migrate -replikeringsprogramvara (Hyper-V-replikeringsprovider) är installerad på Hyper-V-värdarna.|
| **Behörigheter**           | Du behöver administratörsbehörighet på Hyper-V-värden. |
| **Värdoperativsystemet** | Windows Server 2019, Windows Server 2016 eller Windows Server 2012 R2 med de senaste uppdateringarna. Observera att Server Core-installation av dessa operativsystem också stöds. |
| **Andra programvarukrav** | .NET Framework 4.7 eller senare |
| **Portåtkomst** |  Utgående anslutningar på HTTPS-port 443 för att skicka VM-replikeringsdata.
| **Ledigt diskutrymme (cache)** |  600 GB |
| **Ledigt diskutrymme (kvarhållningsdisk)** |  600 GB |


## <a name="hyper-v-vms"></a>Hyper-V:s virtuella datorer

| **Support**                  | **Information**               
| :----------------------------- | :------------------- |
| **Operativsystem** | Alla [Windows-](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines) [och Linux-operativsystem](../virtual-machines/linux/endorsed-distros.md) som stöds av Azure. |
**Windows Server 2003** | För virtuella datorer som kör Windows Server 2003 måste du installera [Hyper-V Integration Services före](prepare-windows-server-2003-migration.md) migreringen. | 
**Virtuella Linux-datorer i Azure** | Vissa virtuella datorer kan kräva ändringar så att de kan köras i Azure.<br/><br/> För Linux Azure Migrate gör ändringar automatiskt för dessa operativsystem:<br/> - Red Hat Enterprise Linux 7.8, 7.7, 7.6, 7.5, 7.4, 7.0, 6.x<br/> – Cent OS 7.7, 7.6, 7.5, 7.4, 6.x</br> - SUSE Linux Enterprise Server 12 SP1+<br/> - SUSE Linux Enterprise Server 15 SP1 <br/>– Ubuntu 19.04, 19.10, 14.04LTS, 16.04LTS, 18.04LTS<br/> – Debian 7, 8 <br/> Oracle Linux 7.7, 7.7-CI<br/> För andra operativsystem gör du de [nödvändiga ändringarna](prepare-for-migration.md#verify-required-changes-before-migrating) manuellt.
| **Nödvändiga ändringar för Azure** | Vissa virtuella datorer kan kräva ändringar så att de kan köras i Azure. Gör justeringar manuellt före migreringen. De relevanta artiklarna innehåller instruktioner om hur du gör detta. |
| **Linux-start**                 | Om /boot finns på en dedikerad partition bör den finnas på OS-disken och inte spridas över flera diskar.<br/> Om /boot är en del av rotpartitionen (/) ska partitionen "/" finnas på OS-disken och inte sträcka sig över andra diskar. |
| **UEFI-start**                  | Stöds. UEFI-baserade virtuella datorer migreras till virtuella Azure-datorer i generation 2.  |
| **UEFI – Säker start**         | Stöds inte för migrering.|
| **Diskstorlek**                  | 2 TB för OS-disken (BIOS-start), 4 TB för OS-disken (UEFI-start), 4 TB för datadiskarna.|
| **Disknummer** | Högst 16 diskar per virtuell dator.|
| **Krypterade diskar/volymer**    | Stöds inte för migrering.|
| **RDM/genomströmningsdiskar**      | Stöds inte för migrering.|
| **Delad disk** | Virtuella datorer som använder delade diskar stöds inte för migrering.|
| **NFS**                        | NFS-volymer som monterats som volymer på de virtuella datorerna replikeras inte.|
| **Iscsi**                      | Virtuella datorer med iSCSI-mål stöds inte för migrering.
| **Måldisk**                | Du kan endast migrera till virtuella Azure-datorer med hanterade diskar. |
| **IPv6** | Stöds inte.|
| **NIC-teaming** | Stöds inte.|
| **Azure Site Recovery** | Du kan inte replikera med Azure Migrate Server Migration om den virtuella datorn är aktiverad för replikering med Azure Site Recovery.|
| **Portar** | Utgående anslutningar på HTTPS-port 443 för att skicka VM-replikeringsdata.|

### <a name="url-access-public-cloud"></a>URL-åtkomst (offentligt moln)

Replikeringsproviderns programvara på Hyper-V-värdarna behöver åtkomst till dessa URL:er.

**URL** | **Information**
--- | ---
login.microsoftonline.com | Åtkomstkontroll och identitetshantering med hjälp av Active Directory.
backup.windowsazure.com | Dataöverföring och samordning av replikering.
*.hypervrecoverymanager.windowsazure.com | Används för replikeringshantering.
*.blob.core.windows.net | Ladda upp data till lagringskonton. 
dc.services.visualstudio.com | Ladda upp apploggar som används för intern övervakning.
time.windows.com | Verifierar tidssynkronisering mellan system och global tid.

### <a name="url-access-azure-government"></a>URL-åtkomst (Azure Government)

Replikeringsproviderns programvara på Hyper-V-värdarna behöver åtkomst till dessa URL:er.

**URL** | **Information**
--- | ---
login.microsoftonline.us | Åtkomstkontroll och identitetshantering med hjälp av Active Directory.
backup.windowsazure.us | Dataöverföring och samordning av replikering.
*.hypervrecoverymanager.windowsazure.us | Används för replikeringshantering.
*.blob.core.usgovcloudapi.net | Ladda upp data till lagringskonton.
dc.services.visualstudio.com | Ladda upp apploggar som används för intern övervakning.
time.nist.gov | Verifierar tidssynkronisering mellan system och global tid.   

>[!Note]
>
> Om du migrerar projekt har **en privat slutpunktsanslutning** måste replikeringsproviderprogramvaran på Hyper-V-värdarna ha åtkomst till dessa URL:er för private link-stöd. 
> - *.blob.core.windows.com – För att få åtkomst till lagringskonto som lagrar replikerade data. Detta är valfritt och krävs inte om lagringskontot har en privat slutpunkt ansluten. 
> - login.windows.net åtkomstkontroll och identitetshantering med hjälp av Active Directory.

## <a name="azure-vm-requirements"></a>Virtuella Azure VMware-datorer

Alla lokala virtuella datorer som replikeras till Azure måste uppfylla kraven för virtuella Azure-datorer som sammanfattas i den här tabellen.

**Komponent** | **Krav** | **Information**
--- | --- | ---
Storlek på operativsystemdisk | Upp till 2 048 GB. | Kontrollen misslyckas om det inte stöds.
Antal operativsystemdiskar | 1 | Kontrollen misslyckas om det inte stöds.
Antal datadiskar | 16 eller mindre. | Kontrollen misslyckas om det inte stöds.
Datadiskstorlek | Upp till 4 095 GB | Kontrollen misslyckas om det inte stöds.
Nätverkskort | Flera kort stöds. |
Delad VHD | Stöds inte. | Kontrollen misslyckas om det inte stöds.
FC-disk | Stöds inte. | Kontrollen misslyckas om det inte stöds.
BitLocker | Stöds inte. | BitLocker måste inaktiveras innan du aktiverar replikering för en dator.
VM-namn | Mellan 1 och 63 tecken.<br/> Begränsat till bokstäver, siffror och bindestreck.<br/><br/> Datornamnet måste börja och sluta med en bokstav eller siffra. |  Uppdatera värdet i datoregenskaperna i Site Recovery.
Ansluta efter migrering av Windows | Så här ansluter du till virtuella Azure-datorer som kör Windows efter migreringen:<br/><br/> - Innan du migrerar aktiverar du RDP på den lokala virtuella datorn. Kontrollera att TCP- och UDP-regler har lagts till för den **offentliga** profilen och att RDP tillåts i **Windows-brandväggen** > **Tillåtna appar** för alla profiler.<br/><br/> - För PLATS-till-plats-VPN-åtkomst aktiverar du RDP och tillåter RDP i **tillåtna** appar och funktioner för domäner och privata nätverk  ->   **i Windows-brandväggen.** Kontrollera också att operativsystemets SAN-princip är inställd på **OnlineAll .** [Läs mer](prepare-for-migration.md). |
Ansluta efter migrering till Linux | Så här ansluter du till virtuella Azure-datorer efter migrering med SSH:<br/><br/> - Innan du migrerar kontrollerar du på den lokala datorn att Secure Shell-tjänsten är inställd på Starta och att brandväggsreglerna tillåter en SSH-anslutning.<br/><br/> - Efter migreringen tillåter du inkommande anslutningar till SSH-porten på den virtuella Azure-datorn för reglerna för nätverkssäkerhetsgruppen på den felande virtuella datorn och för Det Azure-undernät som den är ansluten till. Lägg dessutom till en offentlig IP-adress för den virtuella datorn. |  

## <a name="next-steps"></a>Nästa steg

[Migrera virtuella Hyper-V-datorer](tutorial-migrate-hyper-v.md) för migrering.
