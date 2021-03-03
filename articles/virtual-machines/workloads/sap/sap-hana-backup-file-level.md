---
title: SAP HANA Azure Backup på fil nivå | Microsoft Docs
description: Det finns två huvudsakliga säkerhets kopierings möjligheter för SAP HANA på virtuella Azure-datorer. den här artikeln handlar om SAP HANA Azure Backup på filnivå
services: virtual-machines-linux
documentationcenter: ''
author: hermanndms
manager: juergent
editor: ''
ms.service: virtual-machines-sap
ms.topic: article
ums.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 03/01/2020
ms.author: juergent
ms.openlocfilehash: 0ff226a156721382a289af0f2a8a0f898a57bab9
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/02/2021
ms.locfileid: "101667976"
---
# <a name="sap-hana-azure-backup-on-file-level"></a>SAP HANA Azure Backup på filnivå

## <a name="introduction"></a>Introduktion

Den här artikeln är en relaterad artikel till [säkerhets kopierings guiden för SAP HANA på Azure Virtual Machines](./sap-hana-backup-guide.md), som innehåller en översikt och information om att komma igång och mer information om Azure Backup ögonblicks bilder av tjänster och lagring. 

Olika VM-typer i Azure tillåter ett annat antal virtuella hård diskar anslutna. De exakta detaljerna dokumenteras i [storlekar för virtuella Linux-datorer i Azure](../../sizes.md). För de tester som hänvisas till i den här dokumentationen använde vi en GS5 Azure VM, som tillåter 64 anslutna data diskar. För större SAP HANA system kan ett stort antal diskar redan utföras för data-och loggfiler, eventuellt i kombination med program striping för optimalt disk-i/o-dataflöde. Mer information om de föreslagna disk konfigurationerna för SAP HANA distributioner på virtuella Azure-datorer finns i artikeln [SAP HANA konfigurationer för virtuella Azure-datorer](./hana-vm-operations-storage.md). Rekommendationerna är inklusive rekommendationer för disk utrymme för lokala säkerhets kopior.

Standard sättet att hantera säkerhets kopiering/återställning på filnivå är med en filbaserad säkerhets kopia via SAP HANA Studio eller via SAP HANA SQL-uttryck. Mer information finns i artikeln [SAP HANA SQL och system views Reference](https://help.sap.com/viewer/4fe29514fd584807ac9f2a04f6754767/2.0.05/en-US/3859e48180bb4cf8a207e15cf25a7e57.html).

![Den här bilden visar dialog rutan för meny alternativet Säkerhetskopiera i SAP HANA Studio](media/sap-hana-backup-file-level/backup-menue-dialog.png)

Den här bilden visar dialog rutan för meny alternativet Säkerhetskopiera i SAP HANA Studio. När du väljer typ &quot; fil måste &quot; en sökväg anges i fil systemet där SAP HANA skriver säkerhetskopieringsfilerna. Återställ fungerar på samma sätt.

Även om det här alternativet låter enkelt och rakt framåt, finns det några saker att tänka på. En virtuell Azure-dator har en begränsning på antalet data diskar som kan kopplas. Det kanske inte finns kapacitet att lagra SAP HANA säkerhets kopior på den virtuella datorns fil system, beroende på storleken på databas-och disk data flödes kraven, vilket kan innebära program striping över flera data diskar. Olika alternativ för att flytta de här säkerhetskopierade filerna och hantera fil storleks begränsningar och prestanda vid hantering av terabyte data finns längre fram i den här artikeln.

Ett annat alternativ, som ger mer frihet om total kapacitet, är Azure Blob Storage. Även om en enskild BLOB också är begränsad till 1 TB, är den totala kapaciteten för en enskild BLOB-behållare för närvarande 500 TB. Dessutom ger kunderna möjlighet att välja så kallade &quot; cool &quot; Blob Storage, som har en kostnads fördel. Se [Azure Blob Storage: frekvent åtkomst, låg frekvent åtkomst och Arkiv](../../../storage/blobs/storage-blob-storage-tiers.md?tabs=azure-portal) lag rings nivåer för information om cool Blob Storage.

För ytterligare säkerhet använder du ett geo-replikerat lagrings konto för att lagra säkerhets kopiorna för SAP HANA. Se [Azure Storage redundans](../../../storage/common/storage-redundancy.md) för mer information om lagrings redundans och lagrings replikering.

Det gick att placera dedikerade virtuella hård diskar för SAP HANA säkerhets kopieringar i ett dedikerat lagrings konto med geo-replikering. Eller en annan kan kopiera de virtuella hård diskar som behåller SAP HANA säkerhets kopieringar till ett geo-replikerat lagrings konto, eller till ett lagrings konto i en annan region.

## <a name="azure-blobxfer-utility-details"></a>Information om Azure blobxfer-verktyget

Om du vill lagra kataloger och filer i Azure Storage kan du använda CLI eller PowerShell eller utveckla ett verktyg med någon av [Azure SDK: erna](https://azure.microsoft.com/downloads/). Det finns också ett verktyg som är redo att använda, AzCopy, för att kopiera data till Azure Storage. (mer information finns i [överföra data med verktyget AzCopy Command-Line](../../../storage/common/storage-use-azcopy-v10.md)).

Därför användes blobxfer för att kopiera SAP HANA säkerhetskopierade filer. Det är öppen källkod som används av många kunder i produktions miljöer och är tillgängliga på [GitHub](https://github.com/Azure/blobxfer). Med det här verktyget kan du kopiera data direkt till antingen Azure Blob Storage eller Azure-filresursen. Den erbjuder också en mängd användbara funktioner, som MD5-hash eller automatisk parallellitet när du kopierar en katalog med flera filer.

## <a name="sap-hana-backup-performance"></a>Säkerhetskopiering av SAP HANA
I det här kapitlet diskuteras prestanda överväganden. De uppnådda talen kanske inte motsvarar det senaste läget eftersom det finns en stadig utveckling för att uppnå bättre data flöde till Azure Storage. Därför bör du utföra enskilda tester för din konfiguration och Azure-region.

![Den här skärm bilden är av SAP HANA säkerhets kopierings konsolen i SAP HANA Studio](media/sap-hana-backup-file-level/backup-console-hana-studio.png)

Den här skärm bilden visar SAP HANA säkerhets kopierings konsolen för SAP HANA Studio. Det tog cirka 42 minuter att göra en säkerhets kopia av 230 GB på en enskild Azure Standard HDD lagrings disk som är ansluten till den virtuella HANA-datorn med hjälp av fil systemet XFS på den ena disken.

![Den här skärm bilden är av YaST på den virtuella test datorn för SAP HANA](media/sap-hana-backup-file-level/one-backup-disk.png)

Den här skärm bilden är av YaST på den virtuella test datorn för SAP HANA. Du kan se den 1 – TB enkla disken för SAP HANA säkerhets kopiering. Det tog cirka 42 minuter att säkerhetskopiera 230 GB. Dessutom var 5 200 GB-diskar anslutna och programvaru-RAID-md0 som skapats, med randning ovanpå dessa fem Azure-datadiskar.

![Upprepa samma säkerhets kopiering på programvaru-RAID med striping över fem anslutna Azure standard Storage-datadiskar](media/sap-hana-backup-file-level/five-backup-disks.png)

Upprepa samma säkerhets kopiering på programvaru-RAID med striping över fem anslutna Azure standard Storage-datadiskar som tog säkerhets kopierings tiden från 42 minuter till 10 minuter. Diskarna har kopplats utan cachelagring till den virtuella datorn. Den här övningen visar vikten av disk skrivnings data flöde för att uppnå en tillräckligt lång säkerhets kopierings tid. Du kan växla till Azure Standard SSD Storage eller Azure Premium Storage för att ytterligare påskynda processen för optimala prestanda. I allmänhet rekommenderas inte Azure standard-lagring för hård diskar och används endast i demonstrations syfte. Rekommendationen är att använda minst Azure Standard SSD Storage eller Azure Premium Storage för produktions system.

## <a name="copy-sap-hana-backup-files-to-azure-blob-storage"></a>Kopiera SAP HANA säkerhetskopierade filer till Azure Blob Storage
Prestanda numren, varaktighets numren för säkerhets kopieringen och de angivna värdena för kopierings varaktigheten kanske inte motsvarar det senaste läget för Azure-teknik. Microsoft förbättrar ständigt Azure Storage för att leverera mer data flöde och lägre fördröjning. Därför är talen endast i demonstrations syfte. Du måste testa för dina individuella behov i den Azure-region som du väljer att kunna bedöma med-metoden passar dig bäst.

Ett annat alternativ för att snabbt lagra SAP HANA säkerhetskopierade filer är Azure Blob Storage. En enda BLOB-behållare har en gräns på omkring 500 TB, tillräckligt med SAP HANA system, med hjälp av M32ts, M32ls, M64ls och GS5 VM-typer för Azure för att behålla tillräckligt med SAP HANA säkerhets kopieringar. Kunder kan välja mellan frekvent &quot; &quot; och &quot; kall &quot; blob-lagring (se [Azure Blob Storage: frekvent åtkomst, låg frekvent åtkomst och Arkiv](../../../storage/blobs/storage-blob-storage-tiers.md?tabs=azure-portal)lag rings nivåer).

Med blobxfer-verktyget är det enkelt att kopiera SAP HANA säkerhetskopierade filer direkt till Azure Blob Storage.

![Här kan du se filerna för en fullständig SAP HANA fil säkerhets kopiering](media/sap-hana-backup-file-level/list-of-backups.png)

Du kan se filerna för en fullständig SAP HANA fil säkerhets kopiering. Av de fyra filerna har det största värdet en storlek på ungefär 230 GB.

![Det tog ungefär 3000 sekunder att kopiera 230 GB till en BLOB-behållare för Azure standard Storage-konto](media/sap-hana-backup-file-level/copy-duration-blobxfer.png)

Om du inte använder MD5-hash i det första testet tog det ungefär 3000 sekunder att kopiera 230 GB till ett Azure standard Storage-konto BLOB-behållare.

Säkerhets kopierings konsolen för HANA Studio tillåter att en maximal fil storlek på HANA-säkerhetskopieringsfiler begränsas. I exempel miljön förbättrade prestanda genom att ha flera mindre säkerhetskopieringsfiler, i stället för en stor 230 GB-fil.

Att ange storleks gränsen för säkerhets kopiering på HANA-sidan har&#39;t förbättra säkerhets kopierings tiden, eftersom filerna skrivs i tur och ordning. Gränsen för fil storlek har angetts till 60 GB, så säkerhets kopieringen skapade fyra stora datafiler i stället för en enda fil med 230 GB. Det kan bli nödvändigt att använda flera säkerhets kopior för att säkerhetskopiera HANA-databaser om dina säkerhets kopierings mål har begränsningar för fil storlekarna i BLOB-storlekarna.

![För att testa parallellitet i blobxfer-verktyget, ställdes den maximala fil storleken för HANA-säkerhetskopiering sedan till 15 GB](media/sap-hana-backup-file-level/parallel-copy-multiple-backup-files.png)

För att testa parallellitet i blobxfer-verktyget, angavs den maximala fil storleken för HANA-säkerhetskopiering sedan till 15 GB, vilket resulterade i 19 säkerhetskopieringsfiler. Den här konfigurationen tog tid för blobxfer att kopiera 230 GB till Azure Blob Storage från 3000 sekunder till 875 sekunder.

När du utforskar kopieringen av säkerhets kopior som utförs mot lokala diskar till andra platser, till exempel Azure Blob Storage, bör du tänka på att den bandbredd som används av en eventuell parallell kopierings process redovisas mot nätverks-eller lagrings kvoten för din enskilda VM-typ. Därför måste du balansera varaktigheten för kopieringen mot nätverket och lagrings bandbredden som den normala arbets belastningen som körs på den virtuella datorn kräver. 


## <a name="copy-sap-hana-backup-files-to-nfs-share"></a>Kopiera SAP HANA säkerhetskopierade filer till NFS-resursen

Microsoft Azure erbjuder interna NFS-resurser via [Azure NetApp Files](https://azure.microsoft.com/services/netapp/). Du kan skapa olika volymer av dussin tals TBs i kapaciteten för att lagra och hantera säkerhets kopior. Du kan också ta ögonblicks bilder av dessa volymer baserat på NetApp-teknik. Azure NetApp Files (ANF) erbjuds i tre olika service nivåer som ger olika lagrings data flöde. Mer information finns i artikeln [service levels for Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-service-levels.md). Du kan skapa och montera en NFS-volym från ANF enligt beskrivningen i artikeln [snabb start: konfigurera Azure NetApp Files och skapa en NFS-volym](../../../azure-netapp-files/azure-netapp-files-quickstart-set-up-account-create-volumes.md?tabs=azure-portal).

Förutom att använda inbyggda NFS-volymer av Azure via ANF finns det olika möjligheter att skapa egna distributioner som tillhandahåller NFS-resurser i Azure. Alla har nack delarna som du behöver för att distribuera och hantera dessa lösningar själv. Några av dessa möjligheter finns dokumenterade i följande artiklar:

- [Hög tillgänglighet för NFS på virtuella Azure-datorer på SUSE Linux Enterprise Server](./high-availability-guide-suse-nfs.md)
- [GlusterFS på virtuella Azure-datorer på Red Hat Enterprise Linux för SAP NetWeaver](./high-availability-guide-rhel-glusterfs.md)

NFS-resurser som skapas med hjälp av det som beskrivs ovan kan användas för att direkt köra HANA-säkerhetskopieringar mot eller för att kopiera säkerhets kopior som utfördes mot lokala diskar till dessa NFS-resurser.

> [!NOTE]
> SAP HANA stöd för NFS v3 och NFS v4. x. Andra format som SMB med CIFS-filsystem stöds inte för att skriva HANA-säkerhetskopieringar. Se även [SAP support note #1820529](https://launchpad.support.sap.com/#/notes/1820529)

## <a name="copy-sap-hana-backup-files-to-azure-files"></a>Kopiera SAP HANA säkerhets kopierings filer till Azure Files

Det går att montera en Azure Files resurs inuti en virtuell Azure Linux-dator. I artikeln [hur du använder Azure File Storage med Linux](../../../storage/files/storage-how-to-use-files-linux.md) får du information om hur du utför konfigurationen. Begränsningar för Azure Files-eller Azure Premium-filer finns i artikeln [Azure Files skalbarhets-och prestanda mål](../../../storage/files/storage-files-scale-targets.md).

> [!NOTE]
> SMB med CIFS-filsystem stöds inte av SAP HANA att skriva HANA-säkerhetskopieringar mot. Se även [#1820529 för SAP-support](https://launchpad.support.sap.com/#/notes/1820529). Därför kan du bara använda den här lösningen som slutgiltigt mål för en säkerhets kopia av HANA-databasen som har utförts direkt mot lokala anslutna diskar
> 

I ett test som utförs mot Azure Files, inte Azure Premium-filer det tog cirka 929 sekunder att kopiera 19 säkerhetskopieringsfiler med en total volym på 230 GB. Vi förväntar dig att använda Azure Premium-filer på ett bättre sätt. Du måste dock vara medveten om att du behöver balansera andelarna av en snabb kopia med de krav som din arbets belastning har på nätverks bandbredd. Eftersom alla typer av virtuella Azure-datorer upprätthåller bandbredds kvoten för nätverket måste du hålla dig inom intervallet för den kvoten med din arbets belastning plus kopian av de säkerhetskopierade filerna.

![Den här bilden visar att det tog cirka 929 sekunder att kopiera 19 SAP HANA säkerhetskopierade filer](media/sap-hana-backup-file-level/parallel-copy-to-azure-files.png)


Det kan vara ett intressant alternativ att lagra SAP HANA säkerhets kopierings filer på Azure Files. Särskilt med den förbättrade svars tiden och data flödet för Azure Premium-filer.

## <a name="next-steps"></a>Nästa steg
* [Säkerhets kopierings guiden för SAP HANA på Azure Virtual Machines](sap-hana-backup-guide.md) ger en översikt och information om att komma igång.
* Information om hur du upprättar hög tillgänglighet och planerar för haveri beredskap för SAP HANA på Azure (stora instanser) finns i [SAP HANA (stora instanser) hög tillgänglighet och haveri beredskap på Azure](hana-overview-high-availability-disaster-recovery.md).
