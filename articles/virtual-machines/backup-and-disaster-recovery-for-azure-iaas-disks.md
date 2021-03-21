---
title: Säkerhets kopiering och haveri beredskap för IaaS-diskar på virtuella Azure-datorer
description: Den här artikeln beskriver hur du planerar för säkerhets kopiering och haveri beredskap för virtuella IaaS-datorer och diskar i Azure. Det här dokumentet omfattar både hanterade och ohanterade diskar.
author: roygara
ms.service: virtual-machines
ms.topic: conceptual
ms.date: 07/19/2017
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: e5ae08c23748e55a8c3b75eb8fb9c112684f022e
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102507914"
---
# <a name="backup-and-disaster-recovery-for-azure-iaas-disks"></a>Säkerhets kopiering och haveri beredskap för Azure IaaS-diskar

Den här artikeln beskriver hur du planerar för säkerhets kopiering och haveri beredskap (DR) för virtuella IaaS-datorer (VM) och diskar i Azure. Det här dokumentet omfattar både hanterade och ohanterade diskar.

Först beskriver vi de inbyggda fel tolerans funktionerna i Azure-plattformen som hjälper till att skydda mot lokala fel. Vi diskuterar sedan de katastrof scenarier som inte helt omfattas av de inbyggda funktionerna. Vi visar också flera exempel på arbets belastnings scenarier där olika säkerhets kopierings-och katastrof överväganden kan användas. Därefter granskar vi eventuella lösningar för DR IaaS-diskar.

## <a name="introduction"></a>Introduktion

Azure-plattformen använder olika metoder för redundans och fel tolerans för att hjälpa till att skydda kunder från lokaliserade maskin varu fel. Lokala fel kan innehålla problem med en Azure Storage Server-dator som lagrar en del av data för en virtuell disk eller fel i en SSD eller hård disk på den servern. Sådana isolerade maskin varu komponents haverier kan inträffa under normal drift.

Azure-plattformen är utformad för att vara flexibel för dessa problem. Större katastrofer kan leda till att flera lagrings servrar eller till och med ett helt data Center inte är åtkomliga. Även om de virtuella datorerna och diskarna normalt skyddas från lokaliserade haverier är det nödvändigt med ytterligare åtgärder för att skydda arbets belastningen från verksamhets omfattande oåterkalleliga haverier, till exempel en större katastrof, som kan påverka din virtuella dator och diskar.

Förutom möjligheten för plattforms fel kan problem med ett kund program eller data uppstå. Till exempel kan en ny version av programmet oavsiktligt göra en ändring i data som gör att den bryts. I så fall kanske du vill återställa programmet och data till en tidigare version som innehåller det senast fungerande tillstånd. Detta kräver att vanliga säkerhets kopieringar upprätthålls.

För regional haveri beredskap måste du säkerhetskopiera dina virtuella IaaS-diskar till en annan region.

Innan vi tittar på säkerhets kopierings-och DR-alternativ kan vi Sammanfattning några metoder som är tillgängliga för att hantera lokaliserade haverier.

### <a name="azure-iaas-resiliency"></a>Azure IaaS-återhämtning

*Återhämtning* syftar på toleransen för normala fel som inträffar i maskin varu komponenter. Återhämtning är möjligheten att återställa från fel och fortsätta att fungera. Det handlar inte om att undvika fel, utan om att reagera på fel på ett sätt som förhindrar avbrott eller dataförluster. Målet med elasticiteten är att återställa programmet till ett fullt fungerande tillstånd efter fel. Azure Virtual Machines och disks har utformats för att vara elastiska för vanliga maskin varu fel. Nu ska vi titta på hur Azure IaaS-plattformen ger den här återhämtnings förmågan.

En virtuell dator består huvudsakligen av två delar: en beräknings Server och de beständiga diskarna. Båda påverkar fel toleransen för en virtuell dator.

Om Azure Compute Host-servern som delar din virtuella dator upplever ett maskin varu haveri, som är sällsynt, är Azure utformad för att automatiskt återställa den virtuella datorn på en annan server. Om det här scenariot startar om datorn och den virtuella datorn kommer att säkerhets kopie ras efter en stund. Azure identifierar automatiskt sådana maskin varu fel och utför återställningar för att se till att den virtuella kund datorn är tillgänglig så snart som möjligt.

För IaaS-diskar är data hållbarheten kritisk för en beständig lagrings plattform. Azure-kunder har viktiga affärs program som körs på IaaS och de är beroende av data beständigheten. Azure Forms-skydd för dessa IaaS-diskar, med tre redundanta kopior av de data som lagras lokalt. Dessa kopior ger hög hållbarhet mot lokala problem. Om en av de maskin varu komponenter som innehåller disken Miss lyckas, påverkas inte din virtuella dator, eftersom det finns två ytterligare kopior som stöder disk förfrågningar. Det fungerar bra, även om två olika maskin varu komponenter som har stöd för en disk kraschar på samma tidpunkt (som är sällsynt). 

För att säkerställa att du alltid behåller tre repliker skapar Azure Storage automatiskt en ny kopia av data i bakgrunden om någon av de tre kopiorna blir otillgänglig. Därför bör det inte vara nödvändigt att använda RAID med Azure-diskar för fel tolerans. En enkel RAID 0-konfiguration bör vara tillräckligt för att Stripa diskarna, om det behövs, för att skapa större volymer.

På grund av den här arkitekturen har Azure ständigt levererat tålighet i företags klass för IaaS-diskar, med en branschledande noll procents [TAXE frekvens](https://en.wikipedia.org/wiki/Annualized_failure_rate).

Lokaliserade maskin varu fel på beräknings värden eller i lagrings plattformen kan ibland leda till att den virtuella datorn inte är tillgänglig som omfattas av [Azure SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/) för VM-tillgänglighet. Azure tillhandahåller också ett branschledande service avtal för enskilda VM-instanser som använder Azure Premium-SSD.

Kunder kan använda [tillgänglighets uppsättningar](./availability.md)för att skydda program arbets belastningar från stillestånds tid på grund av att en disk eller virtuell dator är tillfälligt otillgänglig. Minst två virtuella datorer i en tillgänglighets uppsättning ger dig redundans för programmet. Azure skapar sedan de här virtuella datorerna och diskarna i separata fel domäner med olika strömförsörjnings-, nätverks-och Server komponenter.

På grund av dessa separata fel domäner påverkar inte lokaliserade maskin varu fel flera virtuella datorer i uppsättningen samtidigt. Att ha separata fel domäner ger hög tillgänglighet för ditt program. Det anses vara en bra idé att använda tillgänglighets uppsättningar när hög tillgänglighet krävs. I nästa avsnitt beskrivs Disaster Recovery-aspekten.

### <a name="backup-and-disaster-recovery"></a>Säkerhets kopiering och haveri beredskap

Haveri beredskap är möjligheten att återställa från sällsynta, men större incidenter. Dessa incidenter omfattar icke-tillfälliga, storskaliga haverier, till exempel avbrott i tjänsten som påverkar en hel region. Haveri beredskap omfattar säkerhets kopiering och arkivering av data, och kan innehålla manuella åtgärder, till exempel att återställa en databas från en säkerhets kopia.

Azure-plattformens inbyggda skydd mot lokaliserade fel kanske inte helt skyddar de virtuella datorerna/diskarna om en stor olycka orsakar storskaliga avbrott. Dessa storskaliga avbrott är oåterkalleliga händelser, t. ex. om ett Data Center träffas av en orkan, jord bävning, eld eller om det finns ett storskaligt fel i maskin varu enheten. Dessutom kan du stöta på fel på grund av problem med programmet eller data.

För att skydda dina IaaS-arbetsbelastningar från avbrott bör du planera för redundans och ha säkerhets kopieringar för att aktivera återställning. Vid haveri beredskap bör du säkerhetskopiera på en annan geografisk plats bort från den primära platsen. Den här metoden hjälper till att säkerställa att säkerhets kopieringen inte påverkas av samma händelse som ursprungligen påverkade den virtuella datorn eller diskarna. Mer information finns i [haveri beredskap för Azure-program](/azure/architecture/resiliency/disaster-recovery-azure-applications).

Dina DR-överväganden kan innehålla följande aspekter:

- Hög tillgänglighet: appens möjlighet att fortsätta att köras i felfritt tillstånd, utan betydande stillestånds tid. I *felfritt tillstånd* innebär det här tillståndet att programmet svarar och att användarna kan ansluta till programmet och interagera med det. Vissa verksamhets kritiska program och databaser kan krävas för att alltid vara tillgängliga, även om det finns problem på plattformen. För dessa arbets belastningar kan du behöva planera redundans för programmet, samt data.

- Data hållbarhet: i vissa fall säkerställer den främsta övervägandeheten att data bevaras om en katastrof inträffar. Därför kan du behöva en säkerhets kopia av dina data på en annan plats. För sådana arbets belastningar kanske du inte behöver fullständig redundans för programmet, men endast en regelbunden säkerhets kopiering av diskarna.

## <a name="backup-and-dr-scenarios"></a>Säkerhets kopiering och DR-scenarier

Nu ska vi titta på några typiska exempel på program arbets belastnings scenarier och överväganden för att planera för haveri beredskap.

### <a name="scenario-1-major-database-solutions"></a>Scenario 1: huvud lösningar för databaser

Överväg en produktions databas server, t. ex. SQL Server eller Oracle, som har stöd för hög tillgänglighet. Kritiska produktions program och användare är beroende av den här databasen. Katastrof återställnings planen för det här systemet kan behöva stödja följande krav:

- Data måste vara skyddade och återställnings bara.
- Servern måste vara tillgänglig för användning.

Disaster Recovery-planen kan kräva att en replik av databasen befinner sig i en annan region som en säkerhets kopia. Beroende på kraven för Server tillgänglighet och data återställning kan lösningen variera från en aktiv-aktiv eller aktiv-passiv replik plats till periodisk offline-säkerhetskopiering av data. Relations databaser, till exempel SQL Server och Oracle, innehåller olika alternativ för replikering. För SQL Server använder du [SQL Server AlwaysOn-tillgänglighetsgrupper](/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server) för hög tillgänglighet.

NoSQL-databaser, som MongoDB, stöder även [repliker](https://docs.mongodb.com/manual/replication/) för redundans. Replikerna för hög tillgänglighet används.

### <a name="scenario-2-a-cluster-of-redundant-vms"></a>Scenario 2: ett kluster med redundanta virtuella datorer

Överväg en arbets belastning som hanteras av ett kluster med virtuella datorer som ger redundans och belastnings utjämning. Ett exempel är ett Cassandra-kluster som distribueras i en region. Den här typen av arkitektur ger redan en hög nivå av redundans inom den regionen. För att skydda arbets belastningen från ett fel på regional nivå bör du dock överväga att sprida klustret över två regioner eller att göra regelbundna säkerhets kopior till en annan region.

### <a name="scenario-3-iaas-application-workload"></a>Scenario 3: IaaS program arbets belastning

Nu ska vi titta på IaaS-programmets arbets belastning. Det här programmet kan till exempel vara en typisk produktions arbets belastning som körs på en virtuell Azure-dator. Det kan vara en webb server eller fil server som innehåller innehållet och andra resurser på en plats. Det kan också vara ett anpassat, byggt affärs program som körs på en virtuell dator som har lagrat data, resurser och program tillstånd på de virtuella dator diskarna. I det här fallet är det viktigt att se till att du regelbundet tar säkerhets kopior. Säkerhets kopierings frekvensen bör baseras på den virtuella dator Belastningens natur. Om programmet till exempel körs varje dag och ändrar data, ska säkerhets kopieringen göras varje timme.

Ett annat exempel är en rapport server som hämtar data från andra källor och genererar sammanställda rapporter. Förlusten av den här virtuella datorn eller diskarna kan leda till att rapporterna går förlorade. Det kan dock vara möjligt att köra rapporterings processen igen och återskapa utdata. I så fall har du faktiskt ingen förlust av data, även om rapport servern har nått en katastrof. Därför kan du ha en högre tolerans nivå för att förlora delar av data på rapport servern. I så fall är mindre frekventa säkerhets kopieringar ett alternativ för att minska kostnaderna.

### <a name="scenario-4-iaas-application-data-issues"></a>Scenario 4: problem med IaaS program data

IaaS program data problem är en annan möjlighet. Överväg ett program som beräknar, underhåller och hanterar viktiga affärs data, till exempel pris information. En ny version av programmet hade en program varu bugg som felaktigt beräknade prissättningen och skadat de befintliga handels data som betjänas av plattformen. Den bästa åtgärden är att återgå till den tidigare versionen av programmet och data. Du kan aktivera detta genom att göra regelbundna säkerhets kopior av systemet.

## <a name="disaster-recovery-solution-azure-backup"></a>Lösning för katastrof återställning: Azure Backup 

[Azure Backup](https://azure.microsoft.com/services/backup/) används för säkerhets kopiering och Dr och fungerar med [hanterade diskar](managed-disks-overview.md) samt ohanterade diskar. Du kan skapa ett säkerhets kopierings jobb med tidsbaserade säkerhets kopieringar, enkel VM-återställning och bevarande principer för säkerhets kopiering.

Om du använder [Premium-SSD](disks-types.md), [hanterade diskar](managed-disks-overview.md)eller andra disk typer med alternativet [Lokalt Redundant lagring](../storage/common/storage-redundancy.md#locally-redundant-storage) , är det särskilt viktigt att göra regelbundna Dr-säkerhetskopieringar. Azure Backup lagrar data i Recovery Services-valvet för långsiktig kvarhållning. Välj alternativet [Geo-redundant lagring](../storage/common/storage-redundancy.md#geo-redundant-storage) för backup Recovery Services-valvet. Alternativet säkerställer att säkerhets kopieringar replikeras till en annan Azure-region för att skydda sig från regionala haverier.

För ohanterade diskar kan du använda den lokalt redundanta lagrings typen för IaaS diskar, men se till att Azure Backup är aktiverat med alternativet Geo-redundant lagring för Recovery Services-valvet.

> [!NOTE]
> Om du använder geo [-redundant lagring](../storage/common/storage-redundancy.md#geo-redundant-storage) eller alternativet [Geo-redundant lagring med Läs åtkomst](../storage/common/storage-redundancy.md#read-access-to-data-in-the-secondary-region)  för dina ohanterade diskar, behöver du fortfarande konsekventa ögonblicks bilder för säkerhets kopiering och Dr. Använd antingen [Azure Backup](https://azure.microsoft.com/services/backup/) eller [konsekventa ögonblicks bilder](#alternative-solution-consistent-snapshots).

 Följande tabell är en sammanfattning av de lösningar som är tillgängliga för DR.

| Scenario | Automatisk replikering | DR-lösning |
| --- | --- | --- |
| Premium SSD-diskar | Lokalt ([Lokalt Redundant lagring](../storage/common/storage-redundancy.md#locally-redundant-storage)) | [Azure Backup](https://azure.microsoft.com/services/backup/) |
| Hanterade diskar | Lokalt ([Lokalt Redundant lagring](../storage/common/storage-redundancy.md#locally-redundant-storage)) | [Azure Backup](https://azure.microsoft.com/services/backup/) |
| Ohanterade lokalt redundanta lagrings diskar | Lokalt ([Lokalt Redundant lagring](../storage/common/storage-redundancy.md#locally-redundant-storage)) | [Azure Backup](https://azure.microsoft.com/services/backup/) |
| Ohanterade geo-redundanta lagrings diskar | Mellan region ([Geo-redundant lagring](../storage/common/storage-redundancy.md#geo-redundant-storage)) | [Azure Backup](https://azure.microsoft.com/services/backup/)<br/>[Konsekventa ögonblicks bilder](#alternative-solution-consistent-snapshots) |
| Ohanterade geo-redundanta lagrings diskar med Läs behörighet | Mellan region ([Read-Access Geo-redundant lagring](../storage/common/storage-redundancy.md#read-access-to-data-in-the-secondary-region)) | [Azure Backup](https://azure.microsoft.com/services/backup/)<br/>[Konsekventa ögonblicks bilder](#alternative-solution-consistent-snapshots) |

Hög tillgänglighet uppnås bäst genom att använda hanterade diskar i en tillgänglighets uppsättning tillsammans med Azure Backup. Om du använder ohanterade diskar kan du fortfarande använda Azure Backup för DR. Om du inte kan använda Azure Backup och använder [konsekventa ögonblicks bilder](#alternative-solution-consistent-snapshots), som beskrivs i ett senare avsnitt, är en alternativ lösning för säkerhets kopiering och Dr.

Dina val för hög tillgänglighet, säkerhets kopiering och DR på program-eller infrastruktur nivåer kan representeras på följande sätt:

| Nivå |   Hög tillgänglighet   | Säkerhets kopiering eller DR |
| --- | --- | --- |
| Program | SQL Server AlwaysOn | Azure Backup |
| Infrastruktur    | Tillgänglighetsuppsättning  | Geo-redundant lagring med konsekventa ögonblicks bilder |

### <a name="using-azure-backup"></a>Använda Azure Backup 

[Azure Backup](../backup/backup-azure-vms-introduction.md) kan säkerhetskopiera dina virtuella datorer som kör Windows eller Linux till Azure Recovery Services-valvet. Att säkerhetskopiera och återställa affärs kritiska data är krångligt eftersom affärs kritiska data måste säkerhets kopie ras medan programmen som producerar data körs. 

För att lösa det här problemet ger Azure Backup programkonsekventa säkerhets kopieringar för Microsoft-arbetsbelastningar. Tjänsten Volume Shadow service används för att säkerställa att data skrivs korrekt till lagringen. För virtuella Linux-datorer är standard konsekvens läget filkonsekventa säkerhets kopieringar, eftersom Linux inte har funktioner som motsvarar tjänsten Volume Shadow som i fallet med Windows. För Linux-datorer, se [programkonsekvent säkerhets kopiering av virtuella Azure Linux-datorer](../backup/backup-azure-linux-app-consistent.md).

![Azure Backup flöde][1]

När Azure Backup initierar ett säkerhets kopierings jobb vid den schemalagda tiden utlöser det säkerhets kopierings tillägget som är installerat i den virtuella datorn för att ta en ögonblicks bild av tidpunkten. En ögonblicks bild utförs i samordning med tjänsten Volume Shadow för att få en enhetlig ögonblicks bild av diskarna i den virtuella datorn utan att behöva stänga av den. Säkerhets kopierings tillägget i den virtuella datorn tömmer alla skrivningar innan du tar en enhetlig ögonblicks bild av alla diskar. När du har tagit ögonblicks bilden överförs data av Azure Backup till säkerhets kopierings valvet. För att säkerhets kopierings processen ska bli mer effektiv, identifierar och överför tjänsten bara de data block som har ändrats efter den senaste säkerhets kopieringen.

För att återställa kan du Visa tillgängliga säkerhets kopior via Azure Backup och sedan starta en återställning. Du kan skapa och återställa Azure-säkerhetskopieringar via [Azure Portal](https://portal.azure.com/), med [hjälp av PowerShell](../backup/backup-azure-vms-automation.md)eller med hjälp av [Azure CLI](/cli/azure/).

### <a name="steps-to-enable-a-backup"></a>Steg för att aktivera en säkerhets kopia

Använd följande steg för att aktivera säkerhets kopiering av virtuella datorer med hjälp av [Azure Portal](https://portal.azure.com/). Det finns viss variation beroende på ditt exakta scenario. Mer information finns i [Azure Backup](../backup/backup-azure-vms-introduction.md) -dokumentationen. Azure Backup också [stöd för virtuella datorer med hanterade diskar](https://azure.microsoft.com/blog/azure-managed-disk-backup/).

1.  Skapa ett Recovery Services-valv för en virtuell dator:

    a. I [Azure Portal](https://portal.azure.com/)bläddrar du till **alla resurser** och söker **Recovery Services valv**.

    b. På menyn **Recovery Services valv** klickar du på **Lägg till** och följer stegen för att skapa ett nytt valv i samma region som den virtuella datorn. Om din virtuella dator till exempel finns i regionen Västra USA väljer du västra USA för valvet.

1.  Verifiera Storage-replikeringen för det nyligen skapade valvet. Öppna valvet under **Recovery Services valv** och gå till **Egenskaper**  >  **säkerhetskopiera konfigurations**  >  **uppdatering**. Se till att alternativet **Geo-redundant lagring** är markerat som standard. Det här alternativet säkerställer att valvet replikeras automatiskt till ett sekundärt Data Center. Ditt valv i västra USA replikeras till exempel automatiskt till USA, östra.

1.  Konfigurera säkerhets kopierings principen och välj den virtuella datorn från samma användar gränssnitt.

1.  Kontrol lera att säkerhets kopierings agenten är installerad på den virtuella datorn. Om den virtuella datorn har skapats med hjälp av en Azure Gallery-avbildning är säkerhets kopierings agenten redan installerad. Annars (det vill säga om du använder en anpassad avbildning) kan du använda anvisningarna för att [installera VM-agenten på en virtuell dator](../backup/backup-azure-arm-vms-prepare.md#install-the-vm-agent).

1.  När föregående steg har slutförts körs säkerhets kopieringen med jämna mellanrum som anges i säkerhets kopierings principen. Vid behov kan du utlösa den första säkerhets kopieringen manuellt från valv instrument panelen på Azure Portal.

För att automatisera Azure Backup med hjälp av skript, se [PowerShell-cmdletar för säkerhets kopiering av virtuella datorer](../backup/backup-azure-vms-automation.md).

### <a name="steps-for-recovery"></a>Steg för återställning

Om du behöver reparera eller återskapa en virtuell dator kan du återställa den virtuella datorn från någon av återställnings punkterna för säkerhets kopiering i valvet. Det finns ett par olika alternativ för att utföra återställningen:

-   Du kan skapa en ny virtuell dator som en tidpunkts representation av den säkerhetskopierade virtuella datorn.

-   Du kan återställa diskarna och sedan använda mallen för den virtuella datorn för att anpassa och återskapa den återställda virtuella datorn.

Mer information finns i anvisningarna för att [använda Azure Portal för att återställa virtuella datorer](../backup/backup-azure-arm-restore-vms.md). Det här dokumentet beskriver också de olika stegen för att återställa säkerhetskopierade virtuella datorer till ett parat Data Center med hjälp av ditt Geo-redundant säkerhets kopierings valv om det finns en katastrof i det primära data centret. I så fall använder Azure Backup beräknings tjänsten från den sekundära regionen för att skapa den återställda virtuella datorn.

Du kan också använda PowerShell för att [skapa en ny virtuell dator från återställda diskar](../backup/backup-azure-vms-automation.md#create-a-vm-from-restored-disks).

## <a name="alternative-solution-consistent-snapshots"></a>Alternativ lösning: konsekventa ögonblicks bilder

Om du inte kan använda Azure Backup kan du implementera en egen mekanism för säkerhets kopiering med hjälp av ögonblicks bilder. Det är komplicerat att skapa konsekventa ögonblicks bilder för alla diskar som används av en virtuell dator och sedan replikera dessa ögonblicks bilder till en annan region. Av den anledningen tar Azure hänsyn till säkerhets kopierings tjänsten som ett bättre alternativ än att skapa en anpassad lösning.

Om du använder Geo-redundant lagring/Geo-redundant lagring med Läs åtkomst för diskar replikeras ögonblicks bilder automatiskt till ett sekundärt Data Center. Om du använder lokalt redundant lagring för diskar måste du själva replikera data. Mer information finns i [säkerhetskopiera Azure-ohanterade virtuella dator diskar med stegvisa ögonblicks bilder](windows/incremental-snapshots.md).

En ögonblicks bild är en representation av ett objekt vid en viss tidpunkt. En ögonblicks bild debiteras för den stegvisa storleken på de data som den innehåller. Mer information finns i [skapa en BLOB-ögonblicksbild](../storage/blobs/snapshots-overview.md).

### <a name="create-snapshots-while-the-vm-is-running"></a>Skapa ögonblicks bilder medan den virtuella datorn körs

Även om du kan ta en ögonblicks bild när som helst, om den virtuella datorn körs, finns det fortfarande data som strömmas till diskarna. Ögonblicks bilderna kan innehålla delvis utförda åtgärder i flygningen. Det kan också hända att ögonblicks bilder av olika diskar har inträffat vid olika tidpunkter, om det finns flera diskar inblandade. Dessa scenarier kan leda till att ögonblicks bilderna blir uncoordinated. Detta brist på samordning är särskilt problematiskt för stripe-volymer vars filer kan vara skadade om ändringar gjordes under säkerhets kopieringen.

För att undvika den här situationen måste säkerhets kopierings processen implementera följande steg:

1.  Lås alla diskar.

1.  Rensa alla väntande skrivningar.

1.  [Skapa en BLOB-ögonblicksbild](../storage/blobs/snapshots-manage-dotnet.md) för alla diskar.

Vissa Windows-program, till exempel SQL Server, tillhandahåller en koordinerad säkerhets kopierings funktion via en volym skugg tjänst för att skapa programkonsekventa säkerhets kopior. I Linux kan du använda ett verktyg som *fsfreeze* för att koordinera diskarna. Det här verktyget ger filkonsekventa säkerhets kopieringar, men inte programkonsekventa ögonblicks bilder. Den här processen är komplicerad, så du bör överväga att använda [Azure Backup](../backup/backup-azure-vms-introduction.md) eller en säkerhets kopierings lösning från tredje part som redan har implementerat den här proceduren.

Föregående process resulterar i en samling koordinerade ögonblicks bilder för alla VM-diskar som representerar en viss tidpunkt för den virtuella datorn. Det här är en återställnings punkt för säkerhets kopiering för den virtuella datorn. Du kan upprepa processen med schemalagda intervall för att skapa regelbundna säkerhets kopieringar. Information om hur du kopierar ögonblicks bilderna till en annan region för DR finns i [Kopiera säkerhets kopior till en annan region](#copy-the-snapshots-to-another-region) .

### <a name="create-snapshots-while-the-vm-is-offline"></a>Skapa ögonblicks bilder när den virtuella datorn är offline

Ett annat alternativ för att skapa konsekventa säkerhets kopieringar är att stänga av den virtuella datorn och ta BLOB-ögonblicksbilder för varje disk. Att ta ögonblicks bilder av BLOB är enklare än att koordinera ögonblicks bilder av en virtuell dator som körs, men det krävs några minuter av drift stopp.

1. Stäng av den virtuella datorn.

1. Skapa en ögonblicks bild av varje virtuell hård disk-BLOB, vilket bara tar några sekunder.

    Om du vill skapa en ögonblicks bild kan du använda [PowerShell](/powershell/module/az.storage), [Azure Storage REST API](/rest/api/storageservices/Snapshot-Blob), [Azure CLI](/cli/azure/)eller något av Azure Storage-klient biblioteken, till exempel [lagrings klient biblioteket för .net](/rest/api/storageservices/Creating-a-Snapshot-of-a-Blob).

1. Starta den virtuella datorn, vilket avslutar stillestånds tiden. Normalt slutförs hela processen inom några minuter.

Den här processen ger en samling konsekventa ögonblicks bilder för alla diskar, vilket ger en säkerhets kopierings punkt för återställnings punkten för den virtuella datorn.

### <a name="copy-the-snapshots-to-another-region"></a>Kopiera ögonblicks bilderna till en annan region

Att skapa ögonblicks bilder kan vara otillräckligt för DR. Du måste också replikera ögonblicks säkerhets kopiorna till en annan region.

Om du använder Geo-redundant lagring eller Geo-redundant lagring med Läs åtkomst för dina diskar replikeras ögonblicks bilderna till den sekundära regionen automatiskt. Det kan finnas några minuters fördröjning innan replikeringen. Om det primära data centret kraschar innan ögonblicks bilderna har slutförts, kan du inte komma åt ögonblicks bilderna från det sekundära data centret. Sannolikheten för detta är liten.

> [!NOTE]
> Endast diskar i en Geo-redundant lagring eller Geo-redundant lagrings konto med Läs åtkomst skyddar inte den virtuella datorn från haverier. Du måste också skapa koordinerade ögonblicks bilder eller använda Azure Backup. Detta krävs för att återställa en virtuell dator till ett konsekvent tillstånd.

Om du använder lokalt redundant lagring måste du kopiera ögonblicks bilderna till ett annat lagrings konto omedelbart efter att du har skapat ögonblicks bilden. Kopierings målet kan vara ett lokalt redundant lagrings konto i en annan region, vilket resulterar i att kopian befinner sig i en annan region. Du kan också kopiera ögonblicks bilden till ett Geo-redundant lagrings konto med Läs åtkomst i samma region. I det här fallet replikeras ögonblicks bilden Lazy till den sekundära fjärrregionen. Säkerhets kopieringen skyddas från haverier på den primära platsen när kopieringen och replikeringen har slutförts.

Om du vill kopiera dina stegvisa ögonblicks bilder så att de fungerar effektivt kan du läsa anvisningarna i [säkerhetskopiera Azure-ohanterade virtuella dator diskar med stegvisa ögonblicks bilder](windows/incremental-snapshots.md).

![Säkerhetskopiera Azure ohanterade virtuella dator diskar med stegvisa ögonblicks bilder][2]

### <a name="recovery-from-snapshots"></a>Återställning från ögonblicks bilder

Om du vill hämta en ögonblicks bild kopierar du den för att skapa en ny blob. Om du kopierar ögonblicks bilden från det primära kontot kan du kopiera ögonblicks bilden till den grundläggande bloben för ögonblicks bilden. Den här processen återställer disken till ögonblicks bilden. Den här processen kallas för att befordra ögonblicks bilden. Om du kopierar ögonblicks bilds säkerhets kopian från ett sekundärt konto måste du kopiera det till ett primärt konto om du vill ha ett Geo-redundant lagrings konto med Läs behörighet. Du kan kopiera en ögonblicks bild med [hjälp av PowerShell](/powershell/module/az.storage) eller med hjälp av AzCopy-verktyget. Mer information finns i [överföra data med kommando rads verktyget AzCopy](../storage/common/storage-use-azcopy-v10.md).

För virtuella datorer med flera diskar måste du kopiera alla ögonblicks bilder som är en del av samma koordinerade återställnings punkt. När du har kopierat ögonblicks bilderna till skrivbara VHD-blobar kan du använda Blobbarna för att återskapa den virtuella datorn med hjälp av mallen för den virtuella datorn.

## <a name="other-options"></a>Andra alternativ

### <a name="sql-server"></a>SQL Server

SQL Server som körs i en virtuell dator har sina egna inbyggda funktioner för att säkerhetskopiera SQL Server-databasen till Azure Blob Storage eller en fil resurs. Om lagrings kontot är Geo-redundant lagring eller Geo-redundant lagring med Läs behörighet kan du komma åt de säkerhets kopiorna i lagrings kontots sekundära Data Center i händelse av en katastrof, med samma begränsningar som tidigare diskuterats. Mer information finns i [säkerhetskopiera och återställa för SQL Server i Azure Virtual Machines](../azure-sql/virtual-machines/windows/azure-storage-sql-server-backup-restore-use.md). Förutom att säkerhetskopiera och återställa kan [SQL Server AlwaysOn-tillgänglighetsgrupper](../azure-sql/virtual-machines/windows/business-continuity-high-availability-disaster-recovery-hadr-overview.md) upprätthålla sekundära repliker av databaser. Den här möjligheten minskar haveri återställnings tiden avsevärt.

## <a name="other-considerations"></a>Ytterligare överväganden

Den här artikeln innehåller information om hur du säkerhetskopierar eller tar ögonblicks bilder av dina virtuella datorer och deras diskar för att stödja haveri beredskap och hur du använder dessa säkerhets kopior eller ögonblicks bilder för att återställa dina data. Med Azure Resource Manager-modellen använder många personer mallar för att skapa sina virtuella datorer och andra infrastrukturer i Azure. Du kan använda en mall för att skapa en virtuell dator som har samma konfiguration varje gång. Om du använder anpassade avbildningar för att skapa dina virtuella datorer, måste du också se till att dina avbildningar skyddas med hjälp av ett Geo-redundant lagrings konto med Läs åtkomst för att lagra dem.

Säkerhets kopierings processen kan därför vara en kombination av två saker:

- Säkerhetskopiera data (diskar).
- Säkerhetskopiera konfigurationen (mallar och anpassade avbildningar).

Beroende på vilket säkerhets kopierings alternativ du väljer kan du behöva hantera säkerhets kopian av både data och konfigurationen, eller så kan säkerhets kopierings tjänsten hantera allt åt dig.

## <a name="appendix-understanding-the-impact-of-data-redundancy"></a>Bilaga: förstå effekten av dataredundans

För lagrings konton i Azure finns det tre typer av dataredundans som du bör överväga om haveri beredskap: lokalt redundant, Geo-redundant eller Geo-redundant med Läs behörighet. 

Lokalt redundant lagring behåller tre kopior av data i samma data Center. När den virtuella datorn skriver data uppdateras alla tre kopior innan det går att returnera dem till anroparen, så att du vet att de är identiska. Disken skyddas från lokala haverier, eftersom det är osannolikt att alla tre kopior påverkas på samma tid. När det gäller lokalt redundant lagring finns det ingen GEO-redundans, så disken skyddas inte från oåterkalleliga haverier som kan påverka ett helt data Center eller en lagrings enhet.

Med Geo-redundant lagring och Geo-redundant lagring med Läs behörighet behålls tre kopior av dina data i den primära region som väljs av dig. Tre kopior av dina data bevaras i en motsvarande sekundär region som anges av Azure. Om du till exempel lagrar data i västra USA replikeras data till USA, östra. Kvarhållning av kopior görs asynkront och det finns en liten fördröjning mellan uppdateringar av primära och sekundära platser. Repliker av diskarna på den sekundära platsen är konsekventa per disk (med fördröjningen), men repliker av flera aktiva diskar kanske inte är synkroniserade med varandra. Om du vill ha konsekventa repliker över flera diskar behövs konsekventa ögonblicks bilder.

Den största skillnaden mellan Geo-redundant lagring och Geo-redundant lagring med Läs behörighet är att med Read-Access Geo-redundant lagring, kan du läsa den sekundära kopian när du vill. Om det uppstår ett problem som gör att det inte går att komma åt data i den primära regionen gör Azure-teamet varje gång för att återställa åtkomsten. Om du har aktiverat Geo-redundant lagring med Läs behörighet aktive rad, medan den primära är nere, kan du komma åt data i det sekundära data centret. Om du planerar att läsa från repliken medan den primära inte är tillgänglig, bör du överväga att använda Geo-redundant lagring med Läs behörighet.

Om det blir ett betydande avbrott kan Azure-teamet utlösa en GEO-redundans och ändra de primära DNS-posterna så att de pekar på sekundär lagring. Om du i det här läget har antingen Geo-redundant lagring eller Geo-redundant lagring med Läs behörighet aktiverat, kan du komma åt data i den region som används som sekundär. Om ditt lagrings konto är Geo-redundant lagring och det uppstår problem kan du bara komma åt den sekundära lagringen om det finns en GEO-redundans.

Mer information finns i [Vad du gör om ett avbrott i Azure Storage inträffar?](../storage/common/storage-disaster-recovery-guidance.md).

## <a name="next-steps"></a>Nästa steg

Se [säkerhetskopiera Azure-ohanterade virtuella dator diskar med stegvisa ögonblicks bilder](linux/incremental-snapshots.md).

[1]: ./media/virtual-machines-common-backup-and-disaster-recovery-for-azure-iaas-disks/backup-and-disaster-recovery-for-azure-iaas-disks-1.png
[2]: ./media/virtual-machines-common-backup-and-disaster-recovery-for-azure-iaas-disks/backup-and-disaster-recovery-for-azure-iaas-disks-2.png