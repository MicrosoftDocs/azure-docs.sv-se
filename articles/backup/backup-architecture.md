---
title: Översikt över arkitekturen
description: Ger en översikt över arkitektur, komponenter och processer som används av Azure Backup tjänsten.
ms.topic: conceptual
ms.date: 02/19/2019
ms.openlocfilehash: 8fca05f8718fc5e44da33b19447895f5daafc905
ms.sourcegitcommit: 79c9c95e8a267abc677c8f3272cb9d7f9673a3d7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/19/2021
ms.locfileid: "107716761"
---
# <a name="azure-backup-architecture-and-components"></a>Azure Backup arkitektur och komponenter

Du kan använda [tjänsten Azure Backup för](backup-overview.md) att backa upp data till Microsoft Azure molnplattformen. Den här artikeln sammanfattar Azure Backup arkitektur, komponenter och processer.

## <a name="what-does-azure-backup-do"></a>Vad gör Azure Backup?

Azure Backup data, datortillstånd och arbetsbelastningar som körs på lokala datorer och virtuella Azure-datorinstanser. Det finns ett antal Azure Backup scenarier.

## <a name="how-does-azure-backup-work"></a>Hur fungerar Azure Backup?

Du kan backa upp datorer och data med hjälp av ett antal metoder:

- **Back up on-premises machines ( Back up on-premises machines):**
  - Du kan backa upp lokala Windows-datorer direkt till Azure med hjälp Azure Backup Microsoft Azure Recovery Services-agenten (MARS). Linux-datorer stöds inte.
  - Du kan säkerhetskopiera lokala datorer till en säkerhetskopieringsserver – antingen System Center Data Protection Manager (DPM) eller Microsoft Azure Backup Server (MABS). Du kan sedan säkerhetskopiera säkerhetskopieringsservern till ett Recovery Services-valv i Azure.

- **Back up Azure VMs ( Back up Azure VMs:**
  - Du kan backa upp virtuella Azure-datorer direkt. Azure Backup installerar ett säkerhetskopieringstillägg till azure VM-agenten som körs på den virtuella datorn. Det här tillägget backar upp hela den virtuella datorn.
  - Du kan backa upp specifika filer och mappar på den virtuella Azure-datorn genom att köra MARS-agenten.
  - Du kan backa upp virtuella Azure-datorer till MABS som körs i Azure och sedan valv backa upp MABS till ett Recovery Services-valv.

Läs mer om [vad du kan säkerhetskopiera och om](backup-overview.md) säkerhetskopieringsscenarier som [stöds.](backup-support-matrix.md)

## <a name="where-is-data-backed-up"></a>Var säkerhetskopieras data?

Azure Backup säkerhetskopierade data i valv – Recovery Services-valv och säkerhetskopieringsvalv. Ett valv är en entitet för onlinelagring i Azure som används för att lagra data, till exempel säkerhetskopior, återställningspunkter och säkerhetskopieringsprinciper.

Valv har följande funktioner:

- Valv gör det enkelt att organisera dina säkerhetskopierade data, samtidigt som hanteringskostnader minimeras.
- Du kan övervaka säkerhetskopierade objekt i ett valv, inklusive virtuella Azure-datorer och lokala datorer.
- Du kan hantera valvåtkomst [med rollbaserad åtkomstkontroll i Azure (Azure RBAC).](../role-based-access-control/role-assignments-portal.md)
- Du anger hur data i valvet replikeras för redundans:
  - **Lokalt redundant lagring (LRS):** Om du vill skydda mot fel i ett datacenter kan du använda LRS. LRS replikerar data till en lagringsskalningsenhet. [Läs mer](../storage/common/storage-redundancy.md#locally-redundant-storage).
  - **Geo-redundant lagring (GRS):** Om du vill skydda mot regionomfattande avbrott kan du använda GRS. GRS replikerar dina data till en sekundär region. [Läs mer](../storage/common/storage-redundancy.md#geo-redundant-storage).
  - **Zonredundant lagring (ZRS):** replikerar dina data i tillgänglighetszoner [,](../availability-zones/az-overview.md#availability-zones)garanterar datahemlighet och återhämtning i samma region. [Läs mer](../storage/common/storage-redundancy.md#zone-redundant-storage)
  - Som standard använder Recovery Services-valv GRS.

Recovery Services-valv har följande ytterligare funktioner:

- I varje Azure-prenumeration kan du skapa upp till 500 valv.

## <a name="backup-agents"></a>Säkerhetskopieringsagenter

Azure Backup tillhandahåller olika säkerhetskopieringsagenter, beroende på vilken typ av dator som säkerhetskopieras:

**Agent** | **Information**
--- | ---
**MARS-agent** | <ul><li>Körs på enskilda lokala Windows Server-datorer för att servera filer, mappar och systemtillstånd.</li> <li>Körs på virtuella Azure-datorer för att backa upp filer, mappar och systemtillstånd.</li> <li>Körs på DPM/MABS-servrar för att servera den lokala DPM/MABS-lagringsdisken till Azure.</li></ul>
**Azure VM-tillägg** | Körs på virtuella Azure-datorer för att backa upp dem till ett valv.

## <a name="backup-types"></a>Säkerhetskopieringstyper

I följande tabell beskrivs de olika typerna av säkerhetskopieringar och när de används:

**Typ av säkerhetskopiering** | **Information** | **Användning**
--- | --- | ---
**Full** | En fullständig säkerhetskopia innehåller hela datakällan. Tar mer nätverksbandbredd än differentiella eller inkrementella säkerhetskopieringar. | Används för inledande säkerhetskopiering.
**Differentiell** |  En differentiell säkerhetskopia lagrar de block som har ändrats sedan den första fullständiga säkerhetskopieringen. Använder en mindre mängd nätverk och lagring och behåller inte redundanta kopior av oförändrade data.<br/><br/> Ineffektivt eftersom datablock som är oförändrade mellan senare säkerhetskopior överförs och lagras. | Används inte av Azure Backup.
**Inkrementellt** | En inkrementell säkerhetskopiering lagrar endast de datablock som har ändrats sedan den föregående säkerhetskopieringen. Hög lagrings- och nätverkseffektivitet. <br/><br/> Med inkrementell säkerhetskopiering behöver du inte komplettera med fullständiga säkerhetskopior. | Används av DPM/MABS för disksäkerhetskopior och används i alla säkerhetskopieringar till Azure. Används inte för SQL Server säkerhetskopiering.

## <a name="sql-server-backup-types"></a>SQL Server säkerhetskopieringstyper

I följande tabell beskrivs de olika typerna av säkerhetskopieringar som används SQL Server databaserna och hur ofta de används:

**Typ av säkerhetskopiering** | **Information** | **Användning**
--- | --- | ---
**Fullständig säkerhetskopia** | En fullständig säkerhetskopia av databas säkerhetskopierar hela databasen. Den innehåller alla data i en specifik databas eller i en uppsättning filgrupper eller filer. En fullständig säkerhetskopia innehåller också tillräckligt med loggar för att återställa dessa data. | Du kan endast utlösa en fullständig säkerhetskopiering per dag.<br/><br/> Du kan välja att göra en fullständig säkerhetskopiering varje dag eller varje vecka.
**Differentiell säkerhetskopia** | En differentiell säkerhetskopia baseras på den senaste, tidigare fullständiga säkerhetskopieringen.<br/><br/> Den samlar bara in de data som har ändrats sedan den fullständiga säkerhetskopieringen. |  Du kan endast utlösa en differentiell säkerhetskopia per dag.<br/><br/> Du kan inte konfigurera en fullständig säkerhetskopia och en differentiell säkerhetskopia samma dag.
**Säkerhetskopiering av transaktionslogg** | Med en loggsäkerhetskopia kan en återställning som baseras på tidpunkt utföras upp till en specifik sekund. | Du kan som mest konfigurera loggsäkerhetskopior var 15:e minut.

### <a name="comparison-of-backup-types"></a>Jämförelse av säkerhetskopieringstyper

Lagringsförbrukning, mål för återställningstid (RTO) och nätverksförbrukning varierar för varje typ av säkerhetskopiering. Följande bild visar en jämförelse av säkerhetskopieringstyperna:

- Datakälla A består av 10 lagringsblock, A1–A10, som säkerhetskopieras varje månad.
- Block A2, A3, A4 och A9 ändras under den första månaden, och block A5 ändras nästa månad.
- För differentiella säkerhetskopieringar säkerhetskopieras ändrade block A2, A3, A4 och A9 i den andra månaden. Den tredje månaden säkerhetskopieras samma block igen, tillsammans med ändrade A5-block. De ändrade blocken fortsätter att säkerhetskopieras tills nästa fullständiga säkerhetskopiering sker.
- För inkrementella säkerhetskopieringar markeras block A2, A3, A4 och A9 som ändrade och överförda i den andra månaden. I den tredje månaden markeras och överförs endast ändrade A5-block.

![Bild som visar jämförelser mellan säkerhetskopieringsmetoder](./media/backup-architecture/backup-method-comparison.png)

## <a name="backup-features"></a>Säkerhetskopieringsfunktioner

I följande tabell sammanfattas de funktioner som stöds för de olika typerna av säkerhetskopiering:

**Funktion** | **Direkt säkerhetskopiering av filer och mappar (med MARS-agenten)** | **Säkerhetskopiering av virtuella Azure-datorer** | **Datorer eller appar med DPM/MABS**
--- | --- | --- | ---
Back up to vault ( Valv Valv) | ![Ja][green] | ![Ja][green] | ![Ja][green]
Backa upp till DPM/MABS-disken och sedan till Azure | | | ![Yes][green]
Komprimera data som skickas för säkerhetskopiering | ![Yes][green] | Ingen komprimering används vid överföring av data. Lagringen ökar något, men återställningen går snabbare.  | ![Yes][green]
Köra inkrementell säkerhetskopiering |![Ja][green] |![Ja][green] |![Ja][green]
Komplicera deduplicerade diskar | | | ![Delvis][yellow]<br/><br/> Endast för DPM/MABS-servrar som distribuerats lokalt.

![Tabellnyckel](./media/backup-architecture/table-key.png)

## <a name="backup-policy-essentials"></a>Information om säkerhetskopieringspolicy

- En säkerhetskopieringspolicy skapas per valv.
- En säkerhetskopieringspolicy kan skapas för säkerhetskopiering av följande arbetsbelastningar: virtuella Azure-datorer, SQL på virtuella Azure-datorer, SAP HANA i virtuella Azure-datorer och Azure-filresurser. Principen för säkerhetskopiering av filer och mappar med MARS-agenten anges i MARS-konsolen.
  - Azure-filresurs
- En princip kan tilldelas till många resurser. En säkerhetskopieringsprincip för virtuella Azure-datorer kan användas för att skydda många virtuella Azure-datorer.
- En princip består av två komponenter
  - Schema: När säkerhetskopieringen ska ske
  - Kvarhållning: Hur länge varje säkerhetskopia ska behållas.
- Schemat kan definieras som "varje dag" eller "varje vecka" med en viss tidpunkt.
- Kvarhållning kan definieras för "dagliga", "veckovisa", "månatliga", "årliga" säkerhetskopieringspunkter.
  - "varje vecka" syftar på en säkerhetskopia en viss dag i veckan
  - "monthly" refererar till en säkerhetskopia en viss dag i månaden
  - "år" syftar på en säkerhetskopia på en viss dag på året
- Kvarhållning för "månatliga", "årliga" säkerhetskopieringspunkter kallas långsiktig kvarhållning (LTR)
- När ett valv skapas skapas även en "DefaultPolicy" och kan användas för att backa upp resurser.
- Ändringar som görs i kvarhållningsperioden för en säkerhetskopieringspolicy tillämpas retroaktivt på alla äldre återställningspunkter förutom de nya.

### <a name="impact-of-policy-change-on-recovery-points"></a>Effekt av principändring på återställningspunkter

- **Kvarhållningstiden ökas/minskas:** När kvarhållningens varaktighet ändras tillämpas även den nya kvarhållningstiden på de befintliga återställningspunkterna. Därför rensas vissa återställningspunkter. Om kvarhållningsperioden ökar får de befintliga återställningspunkterna även en ökad kvarhållning.
- **Har ändrats från varje dag till varje vecka:** När de schemalagda säkerhetskopieringarna ändras från varje dag till varje vecka rensas de befintliga dagliga återställningspunkterna.
- **Har ändrats från varje vecka till varje dag:** De befintliga veckovisa säkerhetskopiorna bevaras baserat på det antal dagar som återstår enligt den aktuella bevarandeprincipen.

### <a name="additional-reference"></a>Ytterligare referenser

- Virtuell Azure-dator: Skapa [och](./backup-azure-vms-first-look-arm.md#back-up-from-azure-vm-settings) [ändra](./backup-azure-manage-vms.md#manage-backup-policy-for-a-vm) princip.
- SQL Server en databas på en virtuell Azure-dator: Så här [skapar](./backup-sql-server-database-azure-vms.md#create-a-backup-policy) och [ändrar du](./manage-monitor-sql-database-backup.md#modify-policy) en princip.
- Azure-filresurs: Skapa [och](./backup-afs.md) [ändra](./manage-afs-backup.md#modify-policy) princip.
- SAP HANA: Skapa [och](./backup-azure-sap-hana-database.md#create-a-backup-policy) [ändra](./sap-hana-db-manage.md#change-policy) princip.
- MARS: Skapa [och](./backup-windows-with-mars-agent.md#create-a-backup-policy) [ändra](./backup-azure-manage-mars.md#modify-a-backup-policy) princip.
- [Finns det några begränsningar för schemaläggning av säkerhetskopiering baserat på typen av arbetsbelastning?](./backup-azure-backup-faq.yml#are-there-limits-on-backup-scheduling-)
- [Vad händer med befintliga återställningspunkter om jag ändrar kvarhållningspolicyn?](./backup-azure-backup-faq.yml#what-happens-when-i-change-my-backup-policy-)

## <a name="architecture-built-in-azure-vm-backup"></a>Arkitektur: Inbyggd säkerhetskopiering av virtuella Azure-datorer

[!INCLUDE [azure-vm-backup-process.md](../../includes/azure-vm-backup-process.md)]

## <a name="architecture-direct-backup-of-on-premises-windows-server-machines-or-azure-vm-files-or-folders"></a>Arkitektur: Direkt säkerhetskopiering av lokala Windows Server-datorer eller Azure VM-filer eller mappar

1. Om du vill konfigurera scenariot laddar du ned och installerar MARS-agenten på datorn. Sedan väljer du vad som ska säkerhetskopieras, när säkerhetskopieringar ska köras och hur länge de ska sparas i Azure.
1. Den första säkerhetskopieringen körs enligt dina inställningar för säkerhetskopiering.
1. MARS-agenten använder VSS för att ta en ögonblicksbild av de volymer som valts för säkerhetskopiering.
    - MARS-agenten använder bara Windows-systemets skrivåtgärd för att avbilda ögonblicksbilden.
    - Eftersom agenten inte använder några VSS-skrivare för program avbildas inte app-konsekventa ögonblicksbilder.
1. När DU har tagit ögonblicksbilden med VSS skapar MARS-agenten en virtuell hårddisk (VHD) i den cachemapp som du angav när du konfigurerade säkerhetskopieringen. Agenten lagrar också kontrollsumor för varje datablock. Dessa används senare för att identifiera ändrade block för efterföljande inkrementella säkerhetskopieringar.
1. Inkrementella säkerhetskopieringar körs enligt det schema som du anger, såvida du inte kör en säkerhetskopiering på begäran.
1. I inkrementella säkerhetskopior identifieras ändrade filer och en ny virtuell hårddisk skapas. Den virtuella hårddisken komprimeras och krypteras och skickas sedan till valvet.
1. När den inkrementella säkerhetskopieringen är klar sammanfogas den nya virtuella hårddisken med den virtuella hårddisken som skapades efter den inledande replikeringen. Den här sammanslagna virtuella hårddisken ger det senaste tillståndet som ska användas för jämförelse av pågående säkerhetskopiering.

![Säkerhetskopiering av lokala Windows Server-datorer med MARS-agent](./media/backup-architecture/architecture-on-premises-mars.png)

## <a name="architecture-back-up-to-dpmmabs"></a>Arkitektur: Backa upp till DPM/MABS

1. Du installerar DPM- eller MABS-skyddsagenten på datorer som du vill skydda. Sedan lägger du till datorerna i en DPM-skyddsgrupp.
    - För att skydda lokala datorer måste DPM- eller MABS-servern finnas lokalt.
    - För att skydda virtuella Azure-datorer måste MABS-servern finnas i Azure och köras som en virtuell Azure-dator.
    - Med DPM/MABS kan du skydda säkerhetskopieringsvolymer, resurser, filer och mappar. Du kan också skydda en dators systemtillstånd (utan operativsystem) och du kan skydda specifika appar med appmedvetna inställningar för säkerhetskopiering.
1. När du ställer in skydd för en dator eller app i DPM/MABS väljer du att backa upp till den lokala MABS/DPM-disken för kortsiktig lagring och till Azure för onlineskydd. Du anger också när säkerhetskopieringen till lokal DPM/MABS-lagring ska köras och när onlinesäkerhetskopiering till Azure ska köras.
1. Disken för den skyddade arbetsbelastningen säkerhetskopieras till de lokala MABS/DPM-diskarna enligt det schema som du har angett.
1. DPM/MABS-diskarna säkerhetskopieras till valvet av MARS-agenten som körs på DPM/MABS-servern.

![Säkerhetskopiering av datorer och arbetsbelastningar som skyddas av DPM eller MABS](./media/backup-architecture/architecture-dpm-mabs.png)

## <a name="azure-vm-storage"></a>Lagring av virtuella Azure-datorer

Virtuella Azure-datorer använder diskar för att lagra operativsystem, appar och data. Varje virtuell Azure-dator har minst två diskar: en disk för operativsystemet och en tillfällig disk. Virtuella Azure-datorer kan också ha datadiskar för appdata. Diskar lagras som virtuella hårddiskar.

- Virtuella hårddiskar lagras som sidblobar i standard- eller premiumlagringskonton i Azure:
  - **Standardlagring:** Tillförlitligt diskstöd till låg kostnad för virtuella datorer som kör arbetsbelastningar som inte är känsliga för svarstider. Standard Storage kan använda SSD-standarddiskar (Solid State Drive) eller standardhårddiskar (HDD).
  - **Premium Storage:** Högpresterande diskstöd. Använder Premium SSD-diskar.
- Det finns olika prestandanivåer för diskar:
  - **Standard HDD disk:** Backas upp av hårddiskar och används för kostnadseffektiv lagring.
  - **Standard SSD disk:** Kombinerar element med Premium SSD-diskar och standard HDD-diskar. Ger mer konsekvent prestanda och tillförlitlighet än HDD, men fortfarande kostnadseffektivt.
  - **Premium SSD disk:** Backas upp av SSD:er och ger högpresterande och låg latens för virtuella datorer som kör I/O-intensiva arbetsbelastningar.
- Diskar kan hanteras eller vara ohanterade:
  - **Ohanterade diskar:** Traditionell typ av diskar som används av virtuella datorer. För dessa diskar skapar du ett eget lagringskonto och anger det när du skapar disken. Sedan måste du ta reda på hur du maximerar lagringsresurserna för dina virtuella datorer.
  - **Hanterade diskar:** Azure skapar och hanterar lagringskontona åt dig. Du anger diskstorlek och prestandanivå så skapar Azure hanterade diskar åt dig. När du lägger till diskar och skalar virtuella datorer hanterar Azure lagringskontona.

Mer information om disklagring och tillgängliga disktyper för virtuella datorer finns i följande artiklar:

- [Azure-hanterade diskar för virtuella Linux-datorer](../virtual-machines/managed-disks-overview.md)
- [Tillgängliga disktyper för virtuella datorer](../virtual-machines/disks-types.md)

### <a name="back-up-and-restore-azure-vms-with-premium-storage"></a>Återställning och återställning av virtuella Azure-datorer med Premium Storage

Du kan backa upp virtuella Azure-datorer med premiumlagring med Azure Backup:

- Under säkerhetskopieringen av virtuella datorer med Premium Storage skapar Backup-tjänsten en tillfällig mellanlagringsplats med namnet *AzureBackup-* i lagringskontot. Storleken på mellanlagringsplatsen är lika med storleken på ögonblicksbilden av återställningspunkten.
- Kontrollera att Premium Storage-kontot har tillräckligt med ledigt utrymme för den tillfälliga mellanlagringsplatsen. Mer information finns i [Skalbarhetsmål för bloblagringskonton på premiumsidan.](../storage/blobs/scalability-targets-premium-page-blobs.md) Ändra inte mellanlagringsplatsen.
- När säkerhetskopieringsjobbet är klart tas mellanlagringsplatsen bort.
- Priset för lagring som används för mellanlagringsplatsen stämmer överens med [prissättningen för Premium Storage.](../virtual-machines/disks-types.md#billing)

När du återställer virtuella Azure-datorer med premiumlagring kan du återställa dem till premium- eller standardlagring. Normalt återställer du dem till Premium Storage. Men om du bara behöver en delmängd filer från den virtuella datorn kan det vara kostnadseffektivt att återställa dem till standardlagring.

### <a name="back-up-and-restore-managed-disks"></a>Återställning och återställning av hanterade diskar

Du kan backa upp virtuella Azure-datorer med hanterade diskar:

- Du hittar virtuella datorer med hanterade diskar på samma sätt som du gör med andra virtuella Azure-datorer. Du kan säkerhetskopiera den virtuella datorn direkt från inställningarna för den virtuella datorn eller aktivera säkerhetskopiering för virtuella datorer i Recovery Services-valvet.
- Du kan säkerhetskopiera virtuella datorer på hanterade diskar via RestorePoint-samlingar som är byggda ovanpå hanterade diskar.
- Azure Backup också stöd för att backa upp virtuella datorer med hanterade diskar som har krypterats med hjälp av Azure Disk Encryption.

När du återställer virtuella datorer med hanterade diskar kan du återställa till en fullständig virtuell dator med hanterade diskar eller till ett lagringskonto:

- Under återställningsprocessen hanterar Azure de hanterade diskarna. Om du använder alternativet för lagringskonto hanterar du det lagringskonto som skapas under återställningsprocessen.
- Om du återställer en hanterad virtuell dator som är krypterad kontrollerar du att den virtuella datorns nycklar och hemligheter finns i nyckelvalvet innan du startar återställningsprocessen.

## <a name="next-steps"></a>Nästa steg

- Granska supportmatrisen om du [vill veta mer om vilka funktioner och begränsningar som stöds för säkerhetskopieringsscenarier.](backup-support-matrix.md)
- Konfigurera säkerhetskopiering för något av följande scenarier:
  - [Back up Azure VMs](backup-azure-arm-vms-prepare.md).
  - [Säkerhetskopiera Windows-datorer direkt](tutorial-backup-windows-server-to-azure.md) utan en reservserver.
  - [Konfigurera MABS](backup-azure-microsoft-azure-backup.md) för säkerhetskopiering till Azure och säkerhetskopiera sedan arbetsbelastningar till MABS.
  - [Konfigurera DPM](backup-azure-dpm-introduction.md) för säkerhetskopiering till Azure och säkerhetskopiera sedan arbetsbelastningar till DPM.

[green]: ./media/backup-architecture/green.png
[yellow]: ./media/backup-architecture/yellow.png
[red]: ./media/backup-architecture/red.png
