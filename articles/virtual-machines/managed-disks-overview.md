---
title: Översikt över Azure-disklagring
description: Översikt över Azure Managed disks, som hanterar lagrings konton åt dig när du använder virtuella datorer.
author: roygara
ms.service: virtual-machines
ms.topic: conceptual
ms.date: 04/24/2020
ms.author: rogarana
ms.subservice: disks
ms.custom: contperf-fy21q1
ms.openlocfilehash: 4a3f272ab6e4a1788368442d7d060233391442fd
ms.sourcegitcommit: 59cfed657839f41c36ccdf7dc2bee4535c920dd4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/06/2021
ms.locfileid: "99627823"
---
# <a name="introduction-to-azure-managed-disks"></a>Introduktion till Azure Managed Disks

Azure Managed disks är lagrings volymer på Block nivå som hanteras av Azure och används med Azure Virtual Machines. Hanterade diskar liknar en fysisk disk på en lokal server, men virtualiseras. Med hanterade diskar behöver du bara ange disk storlek, disk typ och etablera disken. När du har etablerat disken hanterar Azure resten.

De tillgängliga disk typerna är Ultra disks, Premium-hårddiskar (solid-state-hårddiskar), standard-SSD och standard hård diskar (HDD). Information om varje enskild disk typ finns i [Välj en disktyp för virtuella IaaS-datorer](disks-types.md).

## <a name="benefits-of-managed-disks"></a>Fördelar med hanterade diskar

Vi går igenom några av fördelarna med att använda hanterade diskar.

### <a name="highly-durable-and-available"></a>Mycket tåliga och tillgängliga

Managed disks har utformats för 99,999% tillgänglighet. För att uppnå detta tillhandahåller hanterade diskar tre repliker av dina data, vilket ger mycket hög hållbarhet. Om en eller till och med två repliker får problem upprätthåller de återstående replikerna beständigheten i dina data och säkerställer en hög feltolerans. Den här arkitekturen har hjälpt Azure att ständigt leverera tålighet i företags klass för IaaS-diskar (Infrastructure as a Service) med en branschledande noll% årlig procents ATS.

### <a name="simple-and-scalable-vm-deployment"></a>Enkel och skalbar VM-distribution

Med hanterade diskar kan du skapa upp till 50 000 VM- **diskar** av en typ i en prenumeration per region, så att du kan skapa tusentals **virtuella datorer** i en enda prenumeration. Den här funktionen ökar också skalbarheten för [virtuella dator skalnings uppsättningar](../virtual-machine-scale-sets/overview.md) genom att du kan skapa upp till 1 000 virtuella datorer i en skalnings uppsättning för virtuella datorer med en Marketplace-avbildning.

### <a name="integration-with-availability-sets"></a>Integrering med tillgänglighets uppsättningar

Hanterade diskar är integrerade med tillgänglighets uppsättningar för att säkerställa att diskarna för [virtuella datorer i en tillgänglighets uppsättning](./manage-availability.md#use-managed-disks-for-vms-in-an-availability-set) är tillräckligt isolerade från varandra för att undvika en enskild felpunkt. Diskar placeras automatiskt i olika enheter för lagrings skalning (stämplar). Om en stämpel Miss lyckas på grund av maskin-eller program varu fel, Miss lyckas bara de virtuella dator instanserna med diskar på dessa stämplar. Anta till exempel att du har ett program som körs på fem virtuella datorer och att de virtuella datorerna finns i en tillgänglighets uppsättning. Diskarna för de virtuella datorerna lagras inte i samma stämpel, så om en stämpel slutar fungera fortsätter de andra instanserna av programmet att köras.

### <a name="integration-with-availability-zones"></a>Integrering med Tillgänglighetszoner

Hanterade diskar har stöd för [Tillgänglighetszoner](../availability-zones/az-overview.md), vilket är ett erbjudande med hög tillgänglighet som skyddar dina program från data centers problem. Tillgänglighetszoner är unika fysiska platser inom en Azure-region. Varje zon utgörs av ett eller flera datacenter som är utrustade med oberoende kraft, kylning och nätverk. För att säkerställa återhämtning finns det minst tre separata zoner i alla aktiverade regioner. Med tillgänglighetszonerna kan Azure erbjuda branschens bästa serviceavtal med en drifttid på 99,99 % för virtuella datorer.

### <a name="azure-backup-support"></a>Azure Backup support

För att skydda mot regionala haverier kan [Azure Backup](../backup/backup-overview.md) användas för att skapa ett säkerhets kopierings jobb med tidsbaserade säkerhets kopieringar och säkerhets kopierings principer. På så sätt kan du utföra återställning av virtuella datorer eller hanterade diskar på. För närvarande Azure Backup stöd för disk storlekar på upp till 32 tebibyte-diskar (TiB). [Läs mer](../backup/backup-support-matrix-iaas.md) om stöd för säkerhets kopiering av virtuella Azure-datorer.

#### <a name="azure-disk-backup"></a>Säkerhetskopiering av Azure-disk

Azure Backup erbjuder Azure disk Backup (för hands version) som en inbyggd, molnbaserad säkerhets kopierings lösning som skyddar dina data i Managed disks. Det är en enkel, säker och kostnads effektiv lösning som gör att du kan konfigurera skydd för hanterade diskar med några få steg. Azure disk Backup erbjuder en nyckel färdig lösning som tillhandahåller hantering av ögonblicks bilder för hanterade diskar genom att automatisera regelbunden generering av ögonblicks bilder och behålla den för konfigurerad varaktighet med hjälp av säkerhets kopierings principen. Mer information om säkerhets kopiering av Azure-diskar finns [i Översikt över Azure disk Backup (i för hands version)](../backup/disk-backup-overview.md).

### <a name="granular-access-control"></a>Detaljerad åtkomst kontroll

Du kan använda [rollbaserad åtkomst kontroll i Azure (Azure RBAC)](../role-based-access-control/overview.md) för att tilldela vissa behörigheter för en hanterad disk till en eller flera användare. Hanterade diskar visar en rad olika åtgärder, inklusive läsa, skriva (Skapa/uppdatera), ta bort och hämta en [URL för signatur för delad åtkomst (SAS)](../storage/common/storage-sas-overview.md) för disken. Du kan bevilja åtkomst till endast de åtgärder som en person behöver för att utföra sitt jobb. Om du till exempel inte vill att en person ska kopiera en hanterad disk till ett lagrings konto kan du välja att inte bevilja åtkomst till export åtgärden för den hanterade disken. Om du inte vill att en person ska använda en SAS-URI för att kopiera en hanterad disk kan du välja att inte bevilja den här behörigheten till den hanterade disken.

### <a name="upload-your-vhd"></a>Ladda upp din virtuella hård disk

Med direkt uppladdning är det enkelt att överföra din virtuella hård disk till en Azure-hanterad disk. Tidigare var du tvungen att följa en mer engagerad process som inkluderade mellanlagring av dina data i ett lagrings konto. Nu finns det färre steg. Det är enklare att ladda upp lokala virtuella datorer till Azure, ladda upp till stora hanterade diskar och säkerhets kopierings-och återställnings processen för enklas. Det minskar också kostnaden genom att tillåta att du överför data till hanterade diskar direkt utan att koppla dem till virtuella datorer. Du kan använda direkt uppladdning för att ladda upp virtuella hård diskar upp till 32 TiB i storlek.

Information om hur du överför din virtuella hård disk till Azure finns i [CLI](linux/disks-upload-vhd-to-managed-disk-cli.md) -eller [PowerShell](windows/disks-upload-vhd-to-managed-disk-powershell.md) -artiklarna.

## <a name="security"></a>Säkerhet

### <a name="private-links"></a>Privata länkar

Stöd för privata Länkar för hanterade diskar kan användas för att importera eller exportera en hanterad disk internt i nätverket. Med privata länkar kan du generera en tidsbunden SAS-URI (signatur för delad åtkomst) för icke anslutna hanterade diskar och ögonblicks bilder som du kan använda för att exportera data till andra regioner för regional expansion, haveri beredskap och kriminal tekniska analys. Du kan också använda SAS-URI: n för att direkt ladda upp en virtuell hård disk till en tom disk lokalt. Nu kan du använda [privata länkar](../private-link/private-link-overview.md) för att begränsa exporten och importen av Managed disks så att den bara kan ske i det virtuella Azure-nätverket. Med privata länkar kan du se till att dina data bara skickas inom det säkra Microsoft stamnät nätverket.

Information om hur du aktiverar privata Länkar för att importera eller exportera en hanterad disk finns i [CLI](linux/disks-export-import-private-links-cli.md) -eller [Portal](disks-enable-private-links-for-import-export-portal.md) artiklarna.

### <a name="encryption"></a>Kryptering

Hanterade diskar erbjuder två olika typer av kryptering. Det första är Server Side Encryption (SSE) som utförs av lagrings tjänsten. Den andra är Azure Disk Encryption (ADE) som du kan aktivera på operativ systemets och data diskarna för dina virtuella datorer.

#### <a name="server-side-encryption"></a>Kryptering på serversidan

Kryptering på Server sidan ger kryptering vid vila och skyddar dina data så att de uppfyller organisationens säkerhets-och efterlevnads åtaganden. Kryptering på Server sidan är aktiverat som standard för alla hanterade diskar, ögonblicks bilder och avbildningar i alla regioner där Managed disks finns tillgängliga. (Temporära diskar krypteras å andra sidan inte av kryptering på Server sidan om du inte aktiverar kryptering på värden. se [disk roller: temporära diskar](#temporary-disk)).

Du kan antingen tillåta Azure att hantera dina nycklar åt dig, dessa är plattforms hanterade nycklar, eller så kan du hantera nycklarna själv, som är Kundhanterade nycklar. Mer information finns på [Server sidans kryptering av Azure-disklagring](./disk-encryption.md) artikel.


#### <a name="azure-disk-encryption"></a>Azure Disk Encryption

Med Azure Disk Encryption kan du kryptera operativ system och data diskar som används av en virtuell IaaS-dator. Krypteringen omfattar Managed disks. För Windows krypteras enheterna med hjälp av teknik för BitLocker-kryptering enligt bransch standard. För Linux krypteras diskarna med DM-Crypt-teknik. Krypteringsprocessen är integrerad med Azure Key Vault så att du kan styra och hantera diskkrypteringsnycklarna. Mer information finns i [Azure Disk Encryption för virtuella Linux-datorer](linux/disk-encryption-overview.md) eller [Azure Disk Encryption för virtuella Windows-datorer](windows/disk-encryption-overview.md).

## <a name="disk-roles"></a>Diskroller

Det finns tre huvudsakliga disk roller i Azure: data disken, OS-disken och den temporära disken. Dessa roller mappar till diskar som är kopplade till den virtuella datorn.

![Disk roller i praktiken](media/virtual-machines-managed-disks-overview/disk-types.png)

### <a name="data-disk"></a>Datadisk

En datadisk är en hanterad disk som är ansluten till en virtuell dator för att lagra program data eller andra data som du behöver behålla. Data diskar registreras som SCSI-enheter och märks med en bokstav som du väljer. Varje data disk har en maximal kapacitet på 32 767 gibibyte (GiB). Storleken på den virtuella datorn avgör hur många data diskar du kan koppla till den och vilken typ av lagring som du kan använda som värd för diskarna.

### <a name="os-disk"></a>OS-disk

Varje virtuell dator har en ansluten operativ system disk. Den OS-disken har ett förinstallerat operativ system som valdes när den virtuella datorn skapades. Den här disken innehåller start volymen.

Den här disken har en maximal kapacitet på 4 095 GiB.

### <a name="temporary-disk"></a>Tillfällig disk

De flesta virtuella datorer innehåller en temporär disk, som inte är en hanterad disk. Den temporära disken ger kortsiktig lagring för program och processer och är avsedd att endast lagra data, till exempel sid-eller växlingsfiler. Data på den tillfälliga disken kan gå förlorade under en [underhålls händelse](./manage-availability.md#understand-vm-reboots---maintenance-vs-downtime) eller när du [distribuerar om en virtuell dator](troubleshooting/redeploy-to-new-node-windows.md?toc=/azure/virtual-machines/windows/toc.json). Vid en lyckad standard omstart av den virtuella datorn kommer data på den temporära disken att kvarstå. Mer information om virtuella datorer utan temporära diskar finns i [storlekar för virtuella Azure-datorer utan lokal temporär disk](azure-vms-no-temp-disk.md).

På virtuella Azure Linux-datorer är den temporära disken vanligt vis/dev/SDB och på virtuella Windows-datorer är den temporära disken D: som standard. Den tillfälliga disken krypteras inte med kryptering på Server sidan om du inte aktiverar kryptering på värden.

## <a name="managed-disk-snapshots"></a>Ögonblicks bilder av hanterade diskar

En ögonblicks bild av en hanterad disk är en skrivskyddad, enhetlig, fullständig kopia av en hanterad disk som är lagrad som en standard-hanterad disk som standard. Med ögonblicks bilder kan du säkerhetskopiera de hanterade diskarna vid alla tidpunkter. Dessa ögonblicks bilder finns oberoende av käll disken och kan användas för att skapa nya hanterade diskar. 

Ögonblicks bilder faktureras baserat på den använda storleken. Om du till exempel skapar en ögonblicks bild av en hanterad disk med en etablerad kapacitet på 64 GiB och den faktiska använda data storleken på 10 GiB, faktureras ögonblicks bilden endast för den använda data storleken på 10 GiB. Du kan se den använda storleken på dina ögonblicks bilder genom att titta på [Azures användnings rapport](../cost-management-billing/understand/review-individual-bill.md). Om den använda data storleken för en ögonblicks bild till exempel är 10 GiB, visar rapporten **daglig** användning 10 GIB/(31 dagar) = 0,3226 som förbrukad kvantitet.

Mer information om hur du skapar ögonblicks bilder för Managed disks finns i följande resurser:

- [Skapa en ögonblicks bild av en hanterad disk i Windows](windows/snapshot-copy-managed-disk.md)
- [Skapa en ögonblicks bild av en hanterad disk i Linux](linux/snapshot-copy-managed-disk.md)

### <a name="images"></a>Avbildningar

Managed disks stöder också skapande av en hanterad anpassad avbildning. Du kan skapa en avbildning från din anpassade virtuella hård disk i ett lagrings konto eller direkt från en generaliserad (Sysprep) virtuell dator. Den här processen fångar upp en enda avbildning. Den här avbildningen innehåller alla hanterade diskar som är associerade med en virtuell dator, inklusive både operativ system och data diskar. Den här hanterade anpassade avbildningen gör det möjligt att skapa hundratals virtuella datorer med din anpassade avbildning utan att behöva kopiera eller hantera lagrings konton.

Information om hur du skapar avbildningar finns i följande artiklar:

- [Så här avbildar du en hanterad avbildning av en generaliserad virtuell dator i Azure](windows/capture-image-resource.md)
- [Så här generaliserar och avbildar du en Linux-dator med hjälp av Azure CLI](linux/capture-image.md)

#### <a name="images-versus-snapshots"></a>Bilder jämfört med ögonblicks bilder

Det är viktigt att förstå skillnaden mellan bilder och ögonblicks bilder. Med hanterade diskar kan du ta en bild av en generaliserad virtuell dator som har frigjorts. Den här avbildningen innehåller alla diskar som är anslutna till den virtuella datorn. Du kan använda den här avbildningen för att skapa en virtuell dator och den innehåller alla diskar.

En ögonblicks bild är en kopia av en disk vid tidpunkten då ögonblicks bilden tas. Den gäller endast för en disk. Om du har en virtuell dator som har en disk (OS-disken) kan du ta en ögonblicks bild eller en avbildning av den och skapa en virtuell dator från antingen ögonblicks bilden eller avbildningen.

En ögonblicks bild har ingen medvetenhet om vilken disk som helst, förutom den som den innehåller. Detta gör det problematiskt att använda i scenarier som kräver samordning av flera diskar, till exempel striping. Ögonblicks bilder måste kunna koordineras med varandra och detta stöds för närvarande inte.

## <a name="disk-allocation-and-performance"></a>Diskallokering och prestanda

Följande diagram illustrerar allokering av bandbredd och IOPS i real tid med hjälp av ett etablerings system på tre nivåer:

![Etablerings system på tre nivåer visar bandbredd och IOPS-allokering](media/virtual-machines-managed-disks-overview/real-time-disk-allocation.png)

Etablering på första nivån anger bandbredds tilldelning per disk och bandbredd.  På den andra nivån implementerar Compute Server-värden SSD-etablering, som endast använder den på data som lagras på serverns SSD, som innehåller diskar med cachelagring (ReadWrite och ReadOnly) samt lokala och temporära diskar. Slutligen sker etablering av virtuella dator nätverk på den tredje nivån för alla I/O som beräknings värden skickar till Azure Storage Server delen. Med det här schemat är prestandan för en virtuell dator beroende av en mängd olika faktorer, från hur den virtuella datorn använder den lokala SSD-enheten, till antalet diskar som är anslutna, samt typ av prestanda och cachelagring för de diskar som är anslutna.

Som ett exempel på dessa begränsningar hindras en Standard_DS1v1 VM från att uppnå 5 000 IOPS-potentialen på en P30 disk, oavsett om den är cachelagrad eller inte, på grund av gränser på SSD-och nätverks nivåerna:

![Standard_DS1v1 exempel tilldelning](media/virtual-machines-managed-disks-overview/example-vm-allocation.png)

Azure använder prioriterad nätverks kanal för disk trafik, som får företräde framför annan låg prioritet för nätverks trafik. Detta hjälper diskar att underhålla sina förväntade prestanda i händelse av nätverks innehåll. På samma sätt hanterar Azure Storage resurs innehåll och andra problem i bakgrunden med automatisk belastnings utjämning. Azure Storage allokerar nödvändiga resurser när du skapar en disk och tillämpar proaktiv och återaktiv utjämning av resurser för att hantera trafik nivån. Detta säkerställer att diskar kan hantera sina förväntade IOPS-och data flödes mål. Du kan använda mått på VM-nivå och disk nivå för att spåra prestanda-och installations aviseringar efter behov.

Se vår [design för artikeln med hög prestanda](premium-storage-performance.md) för att lära dig om bästa praxis för att optimera VM + diskkonfigurationer så att du kan uppnå önskade prestanda

## <a name="next-steps"></a>Nästa steg

Om du vill att en video ska bli mer detaljerad på hanterade diskar kan du kolla in: [bättre återhämtning av Azure VM med Managed disks](https://channel9.msdn.com/Blogs/Azure/Managed-Disks-for-Azure-Resiliency).

Lär dig mer om de enskilda disk typerna Azure-erbjudanden, vilken typ som passar bäst för dina behov och lär dig mer om deras prestanda mål i vår artikel om disk typer.

> [!div class="nextstepaction"]
> [Välja en disktyp för virtuella IaaS-datorer](disks-types.md)