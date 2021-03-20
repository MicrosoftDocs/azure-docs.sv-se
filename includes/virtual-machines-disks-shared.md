---
title: ta med fil
description: ta med fil
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 07/14/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 74c77356df4f35461a8b9f1459712cdcf7f77cbf
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "95558893"
---
Azure delade diskar är en ny funktion för Azure Managed disks som gör att du kan ansluta en hanterad disk till flera virtuella datorer samtidigt. Genom att ansluta en hanterad disk till flera virtuella datorer kan du antingen distribuera nya eller migrera befintliga klustrade program till Azure.

## <a name="how-it-works"></a>Så här fungerar det

Virtuella datorer i klustret kan läsa eller skriva till sin anslutna disk baserat på den reservation som valts av det klustrade programmet med [SCSI-beständiga reservationer](https://www.t10.org/members/w_spc3.htm) (SCSI-PR). SCSI PR är en bransch standard som utnyttjas av program som körs på lokala SAN-nätverk (Storage Area Network). Genom att aktivera SCSI PR på en hanterad disk kan du migrera dessa program till Azure som de är.

Delade hanterade diskar erbjuder delad block lagring som kan nås från flera virtuella datorer, och dessa visas som logiska enhets nummer (LUN). LUN visas sedan för en initierare (VM) från ett mål (disk). Dessa LUN ser ut som direktansluten lagring (DAS) eller en lokal enhet till den virtuella datorn.

Delade hanterade diskar erbjuder inget fullständigt hanterat fil system som kan nås via SMB/NFS. Du måste använda en kluster hanterare, t. ex. Windows Server failover Cluster (WSFC) eller pacemaker, som hanterar kommunikation mellan kluster noder och skriv låsning.

## <a name="limitations"></a>Begränsningar

[!INCLUDE [virtual-machines-disks-shared-limitations](virtual-machines-disks-shared-limitations.md)]

### <a name="operating-system-requirements"></a>Operativsystemskrav

Delade diskar har stöd för flera operativ system. Se avsnittet [Windows](#windows) eller [Linux](#linux) för de operativ system som stöds.

## <a name="disk-sizes"></a>Disk storlekar

[!INCLUDE [virtual-machines-disks-shared-sizes](virtual-machines-disks-shared-sizes.md)]

## <a name="sample-workloads"></a>Exempel arbets belastningar

### <a name="windows"></a>Windows

Azure delade diskar stöds på Windows Server 2008 och senare. De flesta Windows-baserade klustring bygger på WSFC, som hanterar alla kärn infrastrukturer för kommunikation i kluster noder, vilket gör att dina program kan dra nytta av parallella åtkomst mönster. WSFC möjliggör både CSV- och icke-CSV-baserade alternativ, beroende på vilken version av Windows Server du använder. Mer information finns i [Create a failover cluster](/windows-server/failover-clustering/create-failover-cluster) (Skapa ett redundanskluster).

Några populära program som körs på WSFC är:

- [Skapa en FCI med Azure Shared disks (SQL Server på virtuella Azure-datorer)](../articles/azure-sql/virtual-machines/windows/failover-cluster-instance-azure-shared-disks-manually-configure.md)
- Skalbar fil server (SoFS) [mall] (https://aka.ms/azure-shared-disk-sofs-template)
- SAP ASCS/SCS [Template] (https://aka.ms/azure-shared-disk-sapacs-template)
- Filserver för allmän användning (IW-arbetsbelastning)
- Användarprofildisk för fjärrskrivbordsserver (Remote Desktop Server User Profile Disk (RDS UPD))

### <a name="linux"></a>Linux

Azure delade diskar stöds på:
- [SUSE SLE för SAP och SUSE SLE HA 15 SP1 och senare](https://www.suse.com/c/azure-shared-disks-excercise-w-sles-for-sap-or-sle-ha/)
- [Ubuntu 18,04 och senare](https://discourse.ubuntu.com/t/ubuntu-high-availability-corosync-pacemaker-shared-disk-environments/14874)
- [RHEL Developer Preview på valfri RHEL 8-version](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/8/html-single/deploying_red_hat_enterprise_linux_8_on_public_cloud_platforms/index?lb_target=production#azure-configuring-shared-block-storage_configuring-rhel-high-availability-on-azure)
- [Oracle Enterprise Linux](https://docs.oracle.com/en/operating-systems/oracle-linux/8/availability/hacluster-1.html)

Linux-kluster kan utnyttja kluster hanterare som [pacemaker](https://wiki.clusterlabs.org/wiki/Pacemaker). Pacemaker bygger på [corosync](http://corosync.github.io/corosync/), vilket möjliggör kluster kommunikation för program som distribueras i miljöer med hög tillgänglighet. Några vanliga klustrade fil system är [ocfs2](https://oss.oracle.com/projects/ocfs2/) och [gfs2](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/global_file_system_2/ch-overview-gfs2). Du kan använda SCSI-PR (SCSI persistent reservation) och/eller SBD-baserade (STONITH block Device) kluster modeller för arbitrating åtkomst till disken. När du använder SCSI-PR kan du ändra reservationer och registreringar med hjälp av verktyg som [fence_scsi](http://manpages.ubuntu.com/manpages/eoan/man8/fence_scsi.8.html) och [sg_persist](https://linux.die.net/man/8/sg_persist).

## <a name="persistent-reservation-flow"></a>Persistent reservation Flow

Följande diagram illustrerar ett exempel på klustrade databas program i två noder som utnyttjar SCSI PR för att aktivera redundans från en nod till en annan.

![Kluster med två noder. Ett program som körs i klustret hanterar åtkomst till disken](media/virtual-machines-disks-shared-disks/shared-disk-updated-two-node-cluster-diagram.png)

Flödet är följande:

1. Det klustrade programmet som körs på både Azure VM1 och VM2 registrerar sin avsikt att läsa eller skriva till disken.
1. Program instansen på VM1 tar sedan exklusiv reservation att skriva till disken.
1. Den här reservationen tillämpas på din Azure-disk och databasen kan nu endast skriva till disken. Det går inte att utföra alla skrivningar från program instansen på VM2.
1. Om program instansen på VM1 kraschar kan instansen på VM2 nu initiera en databas växling vid fel och ta över disken.
1. Den här reservationen tillämpas nu på Azure-disken och disken tar inte längre emot skrivningar från VM1. Det tar bara emot skrivningar från VM2.
1. Det klustrade programmet kan slutföra redundansväxlingen av databasen och betjäna förfrågningar från VM2.

Följande diagram illustrerar en annan vanlig klustrad arbets belastning som består av flera noder som läser data från disken för att köra parallella processer, till exempel utbildning av Machine Learning-modeller.

![Fyra noder för virtuella datorer, varje nod registrerar sig för att skriva, programmet tar exklusiv reservation för att hantera Skriv resultaten korrekt](media/virtual-machines-disks-shared-disks/shared-disk-updated-machine-learning-trainer-model.png)

Flödet är följande:

1. Det klustrade programmet som körs på alla virtuella datorer registrerar avsikten att läsa eller skriva till disken.
1. Program instansen på VM1 tar en exklusiv reservation att skriva till disken medan läsning av läsningar till disken från andra virtuella datorer öppnas.
1. Den här reservationen tillämpas på Azure-disken.
1. Alla noder i klustret kan nu läsa från disken. Endast en nod skriver tillbaka resultat till disken på uppdrag av alla noder i klustret.

### <a name="ultra-disks-reservation-flow"></a>Ultra disks reservations flöde

Ultra disks ger ytterligare en begränsning, för totalt två begränsningar. På grund av detta kan Ultra disks reservations flöde fungera på det sätt som beskrivs i det tidigare avsnittet, eller så kan det begränsa och distribuera prestanda mer detaljerat.

:::image type="content" source="media/virtual-machines-disks-shared-disks/ultra-reservation-table.png" alt-text="En bild av en tabell som visar åtkomsten &quot;ReadOnly&quot; eller &quot;Läs/skriv&quot; för reservations innehavare, registrerad och andra.":::

## <a name="performance-throttles"></a>Prestanda begränsningar

### <a name="premium-ssd-performance-throttles"></a>Premium SSD prestanda begränsningar

Med Premium SSD åtgärdas disken IOPS och data flödet, till exempel IOPS för en P30 är 5000. Det här värdet är kvar om disken delas mellan två virtuella datorer eller 5 virtuella datorer. Disk gränserna kan nås från en enskild virtuell dator eller delas upp på två eller flera virtuella datorer. 

### <a name="ultra-disk-performance-throttles"></a>Prestanda begränsningar för Ultra disk

Ultra disks har en unik funktion som gör att du kan ställa in prestanda genom att exponera ändrings bara attribut och göra det möjligt att ändra dem. Som standard finns det bara två ändrings bara attribut, men delade Ultra disks har två ytterligare attribut.


|Attribut  |Beskrivning  |
|---------|---------|
|DiskIOPSReadWrite     |Det totala antalet IOPS som tillåts över alla virtuella datorer som monterar delnings disken med skriv åtkomst.         |
|DiskMBpsReadWrite     |Det totala data flödet (MB/s) som tillåts på alla virtuella datorer som monterar den delade disken med skriv åtkomst.         |
|DiskIOPSReadOnly*     |Det totala antalet IOPS som tillåts över alla virtuella datorer som monterar den delade disken som `ReadOnly` .         |
|DiskMBpsReadOnly*     |Det totala data flödet (MB/s) som tillåts på alla virtuella datorer som monterar den delade disken som `ReadOnly` .         |

\* Gäller enbart delade Ultra disks

Följande formler förklarar hur prestanda-attribut kan anges, eftersom de är ändrings bara av användaren:

- DiskIOPSReadWrite/DiskIOPSReadOnly: 
    - IOPS-gränser på 300 IOPS/GiB, upp till högst 160K IOPS per disk
    - Minst 100 IOPS
    - DiskIOPSReadWrite + DiskIOPSReadOnly är minst 2 IOPS/GiB
- DiskMBpsRead-skrivning/DiskMBpsReadOnly:
    - Data flödes gränsen på en enskild disk är 256 KiB/s för varje etablerad IOPS, upp till högst 2000 Mbit/s per disk
    - Lägsta garanterade data flöde per disk är 4KiB/s för varje etablerad IOPS med en total bas linje i minst 1 Mbit/s

#### <a name="examples"></a>Exempel

I följande exempel beskrivs några scenarier som visar hur begränsningen kan fungera med delade Ultra disks, särskilt.

##### <a name="two-nodes-cluster-using-cluster-shared-volumes"></a>Två noder kluster med klusterdelade volymer

Följande är ett exempel på en WSFC med två noder som använder klusterdelade volymer. Med den här konfigurationen har båda de virtuella datorerna samtidig skriv åtkomst till disken, vilket leder till att `ReadWrite` begränsningen delas mellan de två virtuella datorerna och att `ReadOnly` begränsningen inte används.

:::image type="content" source="media/virtual-machines-disks-shared-disks/ultra-two-node-example.png" alt-text="CSV två-noder, Ultra-exempel":::

##### <a name="two-node-cluster-without-cluster-share-volumes"></a>Två nods kluster utan kluster resurs volymer

Följande är ett exempel på en WSFC-kluster med två noder som inte använder klusterdelade volymer. Med den här konfigurationen är det bara en virtuell dator som har skriv åtkomst till disken. Detta resulterar i att den `ReadWrite` begränsning som används uteslutande för den primära virtuella datorn och `ReadOnly` begränsningen endast används av den sekundära.

:::image type="content" source="media/virtual-machines-disks-shared-disks/ultra-two-node-no-csv.png" alt-text="Två noder för CSV: inga CSV Ultra disk-exempel":::

##### <a name="four-node-linux-cluster"></a>Linux-kluster med fyra noder

Följande är ett exempel på en 4-nods Linux-kluster med en enda skrivare och tre skalbara läsare. Med den här konfigurationen är det bara en virtuell dator som har skriv åtkomst till disken. Detta resulterar i att `ReadWrite` begränsningen används exklusivt för den primära virtuella datorn och den `ReadOnly` begränsning som delas av de sekundära virtuella datorerna.

:::image type="content" source="media/virtual-machines-disks-shared-disks/ultra-four-node-example.png" alt-text="Exempel på fyra noder med ultralåg begränsning":::

#### <a name="ultra-pricing"></a>Ultra prissättning

Ultra Shared disks priss ätts baserat på etablerad kapacitet, totalt antal etablerade IOPS (diskIOPSReadWrite + diskIOPSReadOnly) och totalt tillhandahållet data flöde Mbit/s (diskMBpsReadWrite + diskMBpsReadOnly). Det finns ingen extra kostnad för varje ytterligare VM-montering. Till exempel är en ultra Shared disk med följande konfiguration (diskSizeGB: 1024, DiskIOPSReadWrite: 10000, DiskMBpsReadWrite: 600, DiskIOPSReadOnly: 100, DiskMBpsReadOnly: 1) debiteras med 1024 GiB, 10100 IOPS och 601 Mbit/s oavsett om den är monterad på två virtuella datorer eller fem virtuella datorer.