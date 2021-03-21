---
title: Översikt över arkitekturen
description: Innehåller en översikt över arkitekturen, komponenterna och processerna som används av Azure Backups tjänsten.
ms.topic: conceptual
ms.date: 02/19/2019
ms.openlocfilehash: 1e5a61bd4e3287c1100ff1f54fda797c1add438b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "103466419"
---
# <a name="azure-backup-architecture-and-components"></a>Azure Backup arkitektur och komponenter

Du kan använda [tjänsten Azure Backup](backup-overview.md) för att säkerhetskopiera data till moln plattformen Microsoft Azure. I den här artikeln sammanfattas Azure Backup arkitektur, komponenter och processer.

## <a name="what-does-azure-backup-do"></a>Vad gör Azure Backup?

Azure Backup säkerhetskopierar data, dator tillstånd och arbets belastningar som körs på lokala datorer och instanser av virtuella Azure-datorer. Det finns ett antal Azure Backup scenarier.

## <a name="how-does-azure-backup-work"></a>Hur fungerar Azure Backup?

Du kan säkerhetskopiera datorer och data genom att använda ett antal metoder:

- **Säkerhetskopiera lokala datorer**:
  - Du kan säkerhetskopiera lokala Windows-datorer direkt till Azure med hjälp av MARS-agenten (Azure Backup Microsoft Azure Recovery Services). Linux-datorer stöds inte.
  - Du kan säkerhetskopiera lokala datorer till en säkerhets kopierings Server, antingen System Center Data Protection Manager (DPM) eller Microsoft Azure Backup Server (MABS). Du kan sedan säkerhetskopiera säkerhets kopierings servern till ett Recovery Services valv i Azure.

- **Säkerhetskopiera virtuella Azure-datorer**:
  - Du kan säkerhetskopiera virtuella Azure-datorer direkt. Azure Backup installerar ett säkerhets kopierings tillägg för Azure VM-agenten som körs på den virtuella datorn. Det här tillägget säkerhetskopierar hela den virtuella datorn.
  - Du kan säkerhetskopiera vissa filer och mappar på den virtuella Azure-datorn genom att köra MARS-agenten.
  - Du kan säkerhetskopiera virtuella Azure-datorer till MABS som körs i Azure, och du kan sedan säkerhetskopiera MABS till ett Recovery Services-valv.

Läs mer om [vad du kan säkerhetskopiera](backup-overview.md) och om vilka [säkerhets kopierings scenarier som stöds](backup-support-matrix.md).

## <a name="where-is-data-backed-up"></a>Var säkerhets kopie ras data?

Azure Backup lagrar säkerhetskopierade data i valv – Recovery Services valv och säkerhets kopierings valv. Ett valv är en online-lagrings enhet i Azure som används för att lagra data, till exempel säkerhets kopior, återställnings punkter och säkerhets kopierings principer.

Valv har följande funktioner:

- Valven gör det enkelt att organisera dina säkerhets kopierings data samtidigt som hanterings kostnaderna minimeras.
- Du kan övervaka säkerhetskopierade objekt i ett valv, inklusive virtuella datorer i Azure och lokala datorer.
- Du kan hantera valv åtkomst med [rollbaserad åtkomst kontroll i Azure (Azure RBAC)](../role-based-access-control/role-assignments-portal.md).
- Du anger hur data i valvet replikeras för redundans:
  - **Lokalt Redundant lagring (LRS)**: för att skydda mot problem i ett Data Center kan du använda LRS. LRS replikerar data till en lagrings skalnings enhet. [Läs mer](../storage/common/storage-redundancy.md#locally-redundant-storage).
  - **Geo-redundant lagring (GRS)**: för att skydda mot hela verksamhets avbrott kan du använda GRS. GRS replikerar dina data till en sekundär region. [Läs mer](../storage/common/storage-redundancy.md#geo-redundant-storage).
  - **Zone-redundant lagring (ZRS)**: replikerar dina data i [tillgänglighets zoner](../availability-zones/az-overview.md#availability-zones), vilket garanterar data placering och återhämtning i samma region. [Läs mer](../storage/common/storage-redundancy.md#zone-redundant-storage)
  - Som standard använder Recovery Services-valv GRS.

Recovery Services-valv har följande ytterligare funktioner:

- I varje Azure-prenumeration kan du skapa upp till 500-valv.

## <a name="backup-agents"></a>Säkerhetskopieringsagenter

Azure Backup tillhandahåller olika säkerhets kopierings agenter, beroende på vilken typ av dator som säkerhets kopie ras:

**Gent** | **Information**
--- | ---
**MARS-agent** | <ul><li>Körs på enskilda lokala Windows Server-datorer för att säkerhetskopiera filer, mappar och system tillstånd.</li> <li>Körs på virtuella Azure-datorer för att säkerhetskopiera filer, mappar och system tillstånd.</li> <li>Körs på DPM/MABS-servrar för att säkerhetskopiera den lokala lagrings disken för DPM/MABS till Azure.</li></ul>
**Azure VM-tillägg** | Körs på virtuella Azure-datorer för att säkerhetskopiera dem till ett valv.

## <a name="backup-types"></a>Säkerhets kopierings typer

I följande tabell förklaras de olika typerna av säkerhets kopieringar och när de används:

**Typ av säkerhets kopiering** | **Information** | **Användning**
--- | --- | ---
**Fullständig** | En fullständig säkerhets kopiering innehåller hela data källan. Tar större nätverks bandbredd än differentiella eller stegvisa säkerhets kopieringar. | Används för den första säkerhets kopieringen.
**Differentiell** |  En differentiell säkerhets kopia lagrar block som har ändrats sedan den första fullständiga säkerhets kopieringen. Använder en mindre mängd nätverks-och lagrings utrymme och skyddar inte redundanta kopior av data som inte har ändrats.<br/><br/> Ineffektiva eftersom data block som inte har ändrats mellan senare säkerhets kopieringar överförs och lagras. | Används inte av Azure Backup.
**Inkrementellt** | En stegvis säkerhets kopiering lagrar bara de data block som har ändrats sedan den tidigare säkerhets kopieringen. Hög lagrings-och nätverks effektivitet. <br/><br/> Med en stegvis säkerhets kopiering behöver du inte komplettera med fullständiga säkerhets kopieringar. | Används av DPM/MABS för disk säkerhets kopieringar och används i alla säkerhets kopior till Azure. Används inte för SQL Server säkerhets kopiering.

## <a name="sql-server-backup-types"></a>SQL Server säkerhets kopierings typer

I följande tabell förklaras de olika typerna av säkerhets kopieringar som används för SQL Server databaser och hur ofta de används:

**Typ av säkerhets kopiering** | **Information** | **Användning**
--- | --- | ---
**Fullständig säkerhetskopia** | En fullständig säkerhetskopia av databas säkerhetskopierar hela databasen. Den innehåller alla data i en speciell databas eller i en uppsättning fil grupper eller filer. En fullständig säkerhets kopiering innehåller också tillräckligt med loggar för att återställa dessa data. | Du kan endast utlösa en fullständig säkerhetskopiering per dag.<br/><br/> Du kan välja att göra en fullständig säkerhets kopia på ett dags-eller vecko intervall.
**Differentiell säkerhetskopia** | En differentiell säkerhets kopia baseras på den senaste, tidigare fullständiga säkerhets kopieringen.<br/><br/> Den fångar bara in de data som har ändrats sedan den fullständiga säkerhets kopieringen. |  Du kan endast utlösa en differentiell säkerhetskopia per dag.<br/><br/> Du kan inte konfigurera en fullständig säkerhetskopia och en differentiell säkerhetskopia samma dag.
**Säkerhets kopiering av transaktions logg** | Med en loggsäkerhetskopia kan en återställning som baseras på tidpunkt utföras upp till en specifik sekund. | Du kan som mest konfigurera loggsäkerhetskopior var 15:e minut.

### <a name="comparison-of-backup-types"></a>Jämförelse av säkerhets kopierings typer

Lagrings förbrukning, återställnings tids mål (RTO) och nätverks förbrukning varierar för varje typ av säkerhets kopiering. Följande bild visar en jämförelse av säkerhets kopierings typerna:

- Data källa A består av 10 lagrings block, a1-A10, som säkerhets kopie ras varje månad.
- Block A2, A3, A4 och A9 ändras under den första månaden, och block A5 ändras nästa månad.
- För differentiella säkerhets kopieringar, i den andra månaden, ändrade block a2, A3, A4 och A9 säkerhets kopie ras. Den tredje månaden säkerhetskopieras samma block igen, tillsammans med ändrade A5-block. De ändrade blocken fortsätter att säkerhetskopieras tills nästa fullständiga säkerhetskopiering sker.
- För stegvisa säkerhets kopieringar är a2, A3, A4 och A9 markerade som ändrade och överförda. I den tredje månaden markeras och överförs endast ändrade A5-block.

![Bild som visar jämförelser av säkerhets kopierings metoder](./media/backup-architecture/backup-method-comparison.png)

## <a name="backup-features"></a>Funktioner för säkerhets kopiering

I följande tabell sammanfattas de funktioner som stöds för de olika typerna av säkerhets kopiering:

**Funktion** | **Direkt säkerhets kopiering av filer och mappar (med MARS-agenten)** | **VIRTUELL Azure-säkerhetskopiering** | **Datorer eller appar med DPM/MABS**
--- | --- | --- | ---
Säkerhetskopiera till valvet | ![Ja][green] | ![Ja][green] | ![Ja][green]
Säkerhetskopiera till DPM/MABS disk, sedan till Azure | | | ![Ja][green]
Komprimera data som skickats för säkerhets kopiering | ![Ja][green] | Ingen komprimering används vid överföring av data. Lagringen är inplattat något men återställningen är snabbare.  | ![Ja][green]
Kör stegvis säkerhets kopiering |![Ja][green] |![Ja][green] |![Ja][green]
Säkerhetskopiera deduplicerade diskar | | | ![Delvis][yellow]<br/><br/> Endast för DPM/MABS-servrar distribuerade lokalt.

![Tabell nyckel](./media/backup-architecture/table-key.png)

## <a name="backup-policy-essentials"></a>Säkerhets kopierings princip Essentials

- En säkerhets kopierings policy skapas per valv.
- En säkerhets kopierings princip kan skapas för säkerhets kopiering av följande arbets belastningar: virtuella Azure-datorer, SQL på virtuella Azure-datorer, SAP HANA i virtuella Azure-datorer och Azure-filresurser. Principen för säkerhets kopiering av filer och mappar med MARS-agenten anges i MARS-konsolen.
  - Azure-filresurs
- En princip kan tilldelas till många resurser. En princip för säkerhets kopiering av virtuella Azure-datorer kan användas för att skydda många virtuella Azure-datorer.
- En princip består av två komponenter
  - Schema: när säkerhets kopieringen ska utföras
  - Kvarhållning: för hur länge varje säkerhets kopia ska behållas.
- Schemat kan definieras som "dagligen" eller "veckovis" med en viss tidpunkt.
- Kvarhållning kan definieras för "dagliga", "veckovis", "årliga" säkerhets kopierings punkter.
  - "veckovis" syftar på en säkerhets kopia på en viss veckodag
  - "Månatlig" syftar på en säkerhets kopia på en viss dag i månaden
  - "årlig" syftar på en säkerhets kopia en viss dag på året
- Kvarhållning för "månatliga", "årliga" säkerhets kopierings platser kallas för långsiktig kvarhållning (brv)
- När ett valv skapas, skapas även en "DefaultPolicy" och kan användas för att säkerhetskopiera resurser.
- Alla ändringar som görs i kvarhållningsperioden för en säkerhets kopierings princip tillämpas retroaktivt för alla äldre återställnings punkter som tas bort från de nya.

### <a name="impact-of-policy-change-on-recovery-points"></a>Effekt av princip ändringar på återställnings punkter

- **Retentions tiden höjs/minskas:** När Retentions tiden ändras, tillämpas den nya Retentions tiden även på befintliga återställnings punkter. Därför rensas vissa av återställnings punkterna. Om kvarhållningsperioden ökas har de befintliga återställnings punkterna även en ökad kvarhållning.
- **Ändrad från varje dag till varje vecka:** När de schemalagda säkerhets kopieringarna ändras från varje dag till varje vecka rensas de befintliga dagliga återställnings punkterna.
- **Ändras från varje vecka till varje dag:** Befintliga vecko Visa säkerhets kopior kommer att behållas baserat på antalet dagar kvar enligt den aktuella bevarande principen.

### <a name="additional-reference"></a>Ytterligare referenser

- Virtuell Azure-dator: så här [skapar](./backup-azure-vms-first-look-arm.md#back-up-from-azure-vm-settings) och [ändrar](./backup-azure-manage-vms.md#manage-backup-policy-for-a-vm) du en princip.
- SQL Server databasen på den virtuella Azure-datorn: så här [skapar](./backup-sql-server-database-azure-vms.md#create-a-backup-policy) och [ändrar](./manage-monitor-sql-database-backup.md#modify-policy) du en princip.
- Azure-fil resurs: så här [skapar](./backup-afs.md) och [ändrar](./manage-afs-backup.md#modify-policy) du en princip.
- SAP HANA: så här [skapar](./backup-azure-sap-hana-database.md#create-a-backup-policy) och [ändrar](./sap-hana-db-manage.md#change-policy) du en princip.
- MARS: så här [skapar](./backup-windows-with-mars-agent.md#create-a-backup-policy) och [ändrar](./backup-azure-manage-mars.md#modify-a-backup-policy) du en princip.
- [Finns det några begränsningar för att schemalägga säkerhets kopiering baserat på typen av arbets belastning?](./backup-azure-backup-faq.md#are-there-limits-on-backup-scheduling)
- [Vad händer med befintliga återställningspunkter om jag ändrar kvarhållningspolicyn?](./backup-azure-backup-faq.md#what-happens-when-i-change-my-backup-policy)

## <a name="architecture-built-in-azure-vm-backup"></a>Arkitektur: inbyggd virtuell Azure-säkerhetskopiering

1. När du aktiverar säkerhets kopiering för en virtuell Azure-dator körs en säkerhets kopiering enligt det schema du anger.
1. Under den första säkerhets kopieringen installeras ett säkerhets kopierings tillägg på den virtuella datorn om den virtuella datorn körs.
    - För virtuella Windows-datorer installeras tillägget VMSnapshot.
    - För virtuella Linux-datorer är VMSnapshot Linux-tillägget installerat.
1. Tillägget tar en ögonblicks bild på lagrings nivå.
    - För virtuella Windows-datorer som kör, koordineras säkerhets kopieringen med Windows-tjänsten Volume Shadow Copy (VSS) för att ta en programkonsekvent ögonblicks bild av den virtuella datorn. Som standard tar säkerhets kopieringen fullständiga VSS-säkerhetskopieringar. Om säkerhets kopieringen inte kan göra en programkonsekvent ögonblicks bild tar det en filkonsekvent ögonblicks bild.
    - För virtuella Linux-datorer tar säkerhets kopieringen en filkonsekvent ögonblicks bild. För programkonsekventa ögonblicks bilder måste du manuellt anpassa för-eller-post-skript.
    - Säkerhets kopieringen är optimerad genom att säkerhetskopiera varje virtuell dator disk parallellt. För varje disk som säkerhets kopie ras läser Azure Backup blocken på disk och lagrar endast ändrade data.
1. När ögonblicks bilden har tagits överförs data till valvet.
    - Endast data block som har ändrats sedan den senaste säkerhets kopieringen kopierades.
    - Data är inte krypterade. Azure Backup kan säkerhetskopiera virtuella Azure-datorer som har krypterats med Azure Disk Encryption.
    - Ögonblicksbilddata kopieras kanske inte direkt till valvet. Vid hög belastnings tider kan säkerhets kopieringen ta några timmar. Den totala säkerhetskopieringstiden för en virtuell dator blir mindre än 24 timmar för dagliga säkerhetskopieringsprinciper.
1. När data har skickats till valvet skapas en återställnings punkt. Som standard behålls ögonblicks bilder i två dagar innan de tas bort. Den här funktionen tillåter återställnings åtgärder från dessa ögonblicks bilder, vilket minskar återställnings tiderna. Det minskar den tid som krävs för att transformera och kopiera data tillbaka från valvet. Se [Azure Backup omedelbar återställnings funktion](./backup-instant-restore-capability.md).

Du behöver inte uttryckligen tillåta Internet anslutning att säkerhetskopiera virtuella Azure-datorer.

![Säkerhets kopiering av virtuella Azure-datorer](./media/backup-architecture/architecture-azure-vm.png)

## <a name="architecture-direct-backup-of-on-premises-windows-server-machines-or-azure-vm-files-or-folders"></a>Arkitektur: direkt säkerhets kopiering av lokala Windows Server-datorer eller Azure VM-filer eller-mappar

1. Om du vill konfigurera scenariot laddar du ned och installerar MARS-agenten på datorn. Sedan väljer du vad som ska säkerhets kopie ras, när säkerhets kopieringarna ska köras och hur länge de ska sparas i Azure.
1. Den första säkerhets kopieringen körs enligt inställningarna för säkerhets kopiering.
1. MARS-agenten använder VSS för att ta en ögonblicks bild av de volymer som valts för säkerhets kopiering.
    - MARS-agenten använder bara Windows system Skriv åtgärd för att avbilda ögonblicks bilden.
    - Eftersom agenten inte använder några VSS-skrivare för programmet fångar den inte in programkonsekventa ögonblicks bilder.
1. När du har tagit ögonblicks bilden med VSS skapar MARS-agenten en virtuell hård disk (VHD) i cache-mappen som du angav när du konfigurerade säkerhets kopian. Agenten lagrar också kontroll summor för varje data block. Dessa används senare för att identifiera ändrade block för efterföljande stegvisa säkerhets kopieringar.
1. Stegvisa säkerhets kopieringar körs enligt det schema du anger, om du inte kör en säkerhets kopiering på begäran.
1. I stegvisa säkerhets kopieringar identifieras ändrade filer och en ny virtuell hård disk skapas. Den virtuella hård disken komprimeras och krypteras och skickas sedan till valvet.
1. När den stegvisa säkerhets kopieringen har slutförts slås den nya virtuella hård disken samman med den virtuella hård disk som skapades efter den inledande replikeringen. Denna sammanlagda virtuella hård disk tillhandahåller det senaste tillstånd som ska användas för att jämföra säkerhets kopior.

![Säkerhets kopiering av lokala Windows Server-datorer med MARS-agent](./media/backup-architecture/architecture-on-premises-mars.png)

## <a name="architecture-back-up-to-dpmmabs"></a>Arkitektur: säkerhetskopiera till DPM/MABS

1. Du installerar DPM-eller MABS-skyddsagenten på datorer som du vill skydda. Sedan lägger du till datorerna i en DPM-skyddsagenten.
    - För att skydda lokala datorer måste DPM-eller MABS-servern finnas lokalt.
    - För att skydda virtuella datorer i Azure måste MABS-servern finnas i Azure, som körs som en virtuell Azure-dator.
    - Med DPM/MABS kan du skydda säkerhets kopierings volymer, resurser, filer och mappar. Du kan också skydda datorns system tillstånd (Bare Metal) och du kan skydda vissa appar med app-medvetna inställningar för säkerhets kopiering.
1. När du konfigurerar skydd för en dator eller app i DPM/MABS väljer du att säkerhetskopiera till den lokala MABS/DPM-disken för kortsiktig lagring och till Azure för onlineskydd. Du anger också när säkerhets kopieringen till den lokala DPM/MABS-lagringen ska köras och när Online-säkerhetskopiering till Azure ska köras.
1. Disken för den skyddade arbets belastningen säkerhets kopie ras till de lokala MABS/DPM-diskarna enligt det schema som du har angett.
1. DPM/MABS-diskarna säkerhets kopie ras till valvet av MARS-agenten som körs på DPM/MABS-servern.

![Säkerhets kopiering av datorer och arbets belastningar som skyddas av DPM eller MABS](./media/backup-architecture/architecture-dpm-mabs.png)

## <a name="azure-vm-storage"></a>Azure VM-lagring

Virtuella Azure-datorer använder diskar för att lagra operativ system, appar och data. Varje virtuell Azure-dator har minst två diskar: en disk för operativ systemet och en tillfällig disk. Virtuella Azure-datorer kan också ha data diskar för AppData. Diskar lagras som virtuella hård diskar.

- Virtuella hård diskar lagras som Page blobbar i standard-eller Premium Storage-konton i Azure:
  - **Standard lagring:** Tillförlitligt stöd för diskar med låg kostnad för virtuella datorer som kör arbets belastningar som inte är känsliga för fördröjning. Standard lagringen kan använda SSD-diskar (Solid-State Drive) eller standard diskar på hård disken (HDD).
  - **Premium-lagring:** Disk support med höga prestanda. Använder Premium SSD-diskar.
- Det finns olika prestanda nivåer för diskar:
  - **Standard HDD disk:** Backas upp av hård diskar och används för kostnads effektiv lagring.
  - **Standard SSD disk:** Kombinerar element i Premium SSD-diskar och standard diskar för hård diskar. Erbjuder mer konsekvent prestanda och tillförlitlighet än HDD, men är fortfarande kostnads effektivt.
  - **Premium SSD disk:** Backas upp av SSD och ger hög prestanda och låg latens för virtuella datorer som kör I/O-intensiva arbets belastningar.
- Diskar kan hanteras eller ohanteras:
  - **Ohanterade diskar:** Traditionell typ av diskar som används av virtuella datorer. För de här diskarna skapar du ett eget lagrings konto och anger det när du skapar disken. Du måste sedan ta reda på hur du kan maximera lagrings resurserna för dina virtuella datorer.
  - **Hanterade diskar:** Azure skapar och hanterar lagrings konton åt dig. Du anger disk storlek och prestanda nivå, och Azure skapar hanterade diskar åt dig. När du lägger till diskar och skalar virtuella datorer, hanterar Azure lagrings kontona.

Mer information om disk lagring och tillgängliga disk typer för virtuella datorer finns i följande artiklar:

- [Azure Managed disks för virtuella Linux-datorer](../virtual-machines/managed-disks-overview.md)
- [Tillgängliga disk typer för virtuella datorer](../virtual-machines/disks-types.md)

### <a name="back-up-and-restore-azure-vms-with-premium-storage"></a>Säkerhetskopiera och återställa virtuella Azure-datorer med Premium Storage

Du kan säkerhetskopiera virtuella Azure-datorer med hjälp av Premium Storage med Azure Backup:

- Under säkerhets kopieringen av virtuella datorer med Premium Storage skapar Backup-tjänsten en tillfällig mellanlagringsplats med namnet *AzureBackup-*, i lagrings kontot. Storleken på mellanlagringsplatsen är lika stor som storleken på ögonblicks bilden av återställnings punkten.
- Se till att Premium Storage-kontot har tillräckligt med ledigt utrymme för att kunna hantera den tillfälliga mellanlagrings platsen. Mer information finns i [skalbarhets mål för Premium Page Blob Storage-konton](../storage/blobs/scalability-targets-premium-page-blobs.md). Ändra inte mellanlagringsplatsen.
- När säkerhets kopierings jobbet har slutförts tas mellanlagringsplatsen bort.
- Priset för det lagrings utrymme som används för mellanlagringsplatsen är konsekvent med [priser för Premium Storage](../virtual-machines/disks-types.md#billing).

När du återställer virtuella Azure-datorer med hjälp av Premium Storage kan du återställa dem till Premium eller standard lagring. Normalt skulle du återställa dem till Premium Storage. Men om du bara behöver en delmängd av filerna från den virtuella datorn kan det vara kostnads effektivt att återställa dem till standard lagring.

### <a name="back-up-and-restore-managed-disks"></a>Säkerhetskopiera och återställa hanterade diskar

Du kan säkerhetskopiera virtuella Azure-datorer med Managed disks:

- Du säkerhetskopierar virtuella datorer med hanterade diskar på samma sätt som du gör med andra virtuella Azure-datorer. Du kan säkerhetskopiera den virtuella datorn direkt från inställningarna för den virtuella datorn, eller så kan du aktivera säkerhets kopiering för virtuella datorer i Recovery Services valvet.
- Du kan säkerhetskopiera virtuella datorer på hanterade diskar via RestorePoint-samlingar som är byggda ovanpå hanterade diskar.
- Azure Backup också stöd för säkerhets kopiering av virtuella datorer med hanterade diskar som har krypterats med hjälp av Azure Disk Encryption.

När du återställer virtuella datorer med hanterade diskar kan du återställa till en fullständig virtuell dator med Managed disks eller till ett lagrings konto:

- Under återställnings processen hanterar Azure de hanterade diskarna. Om du använder alternativet lagrings konto hanterar du det lagrings konto som skapas under återställnings processen.
- Om du återställer en hanterad virtuell dator som är krypterad kontrollerar du att de virtuella datorernas nycklar och hemligheter finns i nyckel valvet innan du påbörjar återställnings processen.

## <a name="next-steps"></a>Nästa steg

- Läs support-matrisen [om du vill veta mer om vilka funktioner som stöds och begränsningar för säkerhets kopierings scenarier](backup-support-matrix.md).
- Konfigurera säkerhets kopiering för något av följande scenarier:
  - [Säkerhetskopiera virtuella Azure-datorer](backup-azure-arm-vms-prepare.md).
  - [Säkerhetskopiera Windows-datorer direkt](tutorial-backup-windows-server-to-azure.md) utan en reservserver.
  - [Konfigurera MABS](backup-azure-microsoft-azure-backup.md) för säkerhetskopiering till Azure och säkerhetskopiera sedan arbetsbelastningar till MABS.
  - [Konfigurera DPM](backup-azure-dpm-introduction.md) för säkerhetskopiering till Azure och säkerhetskopiera sedan arbetsbelastningar till DPM.

[green]: ./media/backup-architecture/green.png
[yellow]: ./media/backup-architecture/yellow.png
[red]: ./media/backup-architecture/red.png
