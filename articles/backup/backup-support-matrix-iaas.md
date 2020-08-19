---
title: Supportmatris för säkerhetskopiering av virtuella Azure-datorer
description: Innehåller en översikt över support inställningar och begränsningar vid säkerhets kopiering av virtuella Azure-datorer med tjänsten Azure Backup.
ms.topic: conceptual
ms.date: 09/13/2019
ms.custom: references_regions
ms.openlocfilehash: c7cd3ed863153762e42a0b6420623fec52e779e1
ms.sourcegitcommit: 37afde27ac137ab2e675b2b0492559287822fded
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88566326"
---
# <a name="support-matrix-for-azure-vm-backup"></a>Supportmatris för säkerhetskopiering av virtuella Azure-datorer

Du kan använda [tjänsten Azure Backup](backup-overview.md) för att säkerhetskopiera lokala datorer och arbets belastningar och virtuella datorer i Azure. Den här artikeln sammanfattar support inställningar och begränsningar när du säkerhetskopierar virtuella Azure-datorer med Azure Backup.

Andra support-matriser:

- [Allmän support mat ris](backup-support-matrix.md) för Azure Backup
- [Support mat ris](backup-support-matrix-mabs-dpm.md) för säkerhets kopiering med Azure Backup Server/System Center Data Protection Manager (DPM)
- [Support mat ris](backup-support-matrix-mars-agent.md) för säkerhets kopiering med Microsoft Azure Recovery Services mars-agenten

## <a name="supported-scenarios"></a>Scenarier som stöds

Så här kan du säkerhetskopiera och återställa virtuella Azure-datorer med tjänsten Azure Backup.

**Scenario** | **Säkerhetskopiering** | **Agent** |**Återställa**
--- | --- | --- | ---
Direkt säkerhets kopiering av virtuella Azure-datorer  | Säkerhetskopiera hela den virtuella datorn.  | Ingen ytterligare agent krävs på den virtuella Azure-datorn. Azure Backup installerar och använder ett tillägg till den virtuella [Azure-agenten](../virtual-machines/extensions/agent-windows.md) som körs på den virtuella datorn. | Återställ enligt följande:<br/><br/> - **Skapa en grundläggande virtuell dator**. Detta är användbart om den virtuella datorn inte har någon särskild konfiguration, till exempel flera IP-adresser.<br/><br/> - **Återställa den virtuella dator disken**. Återställa disken. Koppla den sedan till en befintlig virtuell dator eller skapa en ny virtuell dator från disken med hjälp av PowerShell.<br/><br/> - **Ersätt VM-disk**. Om en virtuell dator finns och den använder hanterade diskar (okrypterade), kan du återställa en disk och använda den för att ersätta en befintlig disk på den virtuella datorn.<br/><br/> - **Återställa vissa filer/mappar**. Du kan återställa filer/mappar från en virtuell dator i stället för från hela den virtuella datorn.
Direkt säkerhets kopiering av virtuella Azure-datorer (endast Windows)  | Säkerhetskopiera vissa filer/mappar/volym. | Installera [Azure Recovery Services-agenten](backup-azure-file-folder-backup-faq.md).<br/><br/> Du kan köra MARS-agenten tillsammans med säkerhets kopierings tillägget för den virtuella Azure-agenten för att säkerhetskopiera den virtuella datorn på fil/mapp-nivå. | Återställa vissa mappar/filer.
Säkerhetskopiera virtuell Azure-dator till säkerhets kopierings Server  | Säkerhetskopiera filer/mappar/volymer. system tillstånd/Bare Metal-filer; AppData till System Center DPM eller till Microsoft Azure Backup Server (MABS).<br/><br/> DPM/MABS säkerhetskopierar sedan till säkerhets kopierings valvet. | Installera DPM/MABS-skyddsagenten på den virtuella datorn. MARS-agenten installeras på DPM/MABS.| Återställ filer/mappar/volymer; system tillstånd/Bare Metal-filer; AppData.

Läs mer om säkerhets kopiering [med en säkerhets kopierings Server](backup-architecture.md#architecture-back-up-to-dpmmabs) och om [support krav](backup-support-matrix-mabs-dpm.md).

## <a name="supported-backup-actions"></a>Säkerhets kopierings åtgärder som stöds

**Åtgärd** | **Support**
--- | ---
Säkerhetskopiera en virtuell dator som är avstängd/offline-VM | Stöds.<br/><br/> Ögonblicks bilden är krasch-konsekvent, inte en app-konsekvent.
Säkerhetskopiera diskar efter migrering till Managed disks | Stöds.<br/><br/> Säkerhets kopieringen fortsätter att fungera. Ingen åtgärd krävs.
Säkerhetskopiera hanterade diskar efter aktivering av resurs grupps lås | Stöds inte.<br/><br/> Azure Backup kan inte ta bort de äldre återställnings punkterna och säkerhets kopieringarna kommer att Miss lyckas när den maximala gränsen för återställnings punkter har nåtts.
Ändra säkerhets kopierings princip för en virtuell dator | Stöds.<br/><br/> Den virtuella datorn kommer att säkerhets kopie ras med hjälp av inställningarna för schema och kvarhållning i ny princip. Om inställningarna för kvarhållning utökas, markeras befintliga återställnings punkter. Om de är reducerade rensas befintliga återställnings punkter i nästa rensnings jobb och slutligen tas bort.
Avbryta ett säkerhets kopierings jobb| Stöds under ögonblicks bild processen.<br/><br/> Stöds inte när ögonblicks bilden överförs till valvet.
Säkerhetskopiera den virtuella datorn till en annan region eller prenumeration |Stöds inte.<br><br>För att säkerhets kopieringen ska kunna utföras måste de virtuella datorerna finnas i samma prenumeration som valvet för säkerhets kopiering.
Säkerhets kopior per dag (via Azure VM-tillägget) | En schemalagd säkerhets kopiering per dag.<br/><br/>Azure Backups tjänsten har stöd för upp till nio säkerhets kopieringar på begäran per dag, men Microsoft rekommenderar högst fyra dagliga säkerhets kopieringar på begäran för att säkerställa bästa prestanda.
Säkerhets kopior per dag (via MARS-agenten) | Tre schemalagda säkerhets kopieringar per dag.
Säkerhets kopior per dag (via DPM/MABS) | Två schemalagda säkerhets kopieringar per dag.
Månatlig/årlig säkerhets kopiering| Stöds inte vid säkerhets kopiering med Azure VM-tillägg. Endast daglig och veckovis stöds.<br/><br/> Du kan ställa in principen för att behålla dagliga/veckovis säkerhets kopieringar för månatlig/årlig kvarhållningsperiod.
Automatisk klock justering | Stöds inte.<br/><br/> Azure Backup justeras inte automatiskt för sommar tids ändringar vid säkerhets kopiering av en virtuell dator.<br/><br/>  Ändra principen manuellt efter behov.
[Säkerhetsfunktioner för Hybrid säkerhets kopiering](./backup-azure-security-feature.md) |Det finns inte stöd för att inaktivera säkerhetsfunktioner.
Säkerhetskopiera den virtuella datorn vars dator tid ändras | Stöds inte.<br/><br/> Om dator tiden ändras till en framtida datum tid efter att du aktiverat säkerhets kopiering för den virtuella datorn. men även om tids ändringen har återställts är inte den slutförda säkerhets kopieringen garanterad.
Virtuella Azure-datorer i [skalnings uppsättningar för virtuella datorer](../virtual-machine-scale-sets/overview.md) | Säkerhets kopiering och återställning stöds för virtuella datorer med [Orchestration-läge](../virtual-machine-scale-sets/orchestration-modes.md#orchestration-modes) inställt på 3. <br><br>Tillgänglighets uppsättningar stöds inte.

## <a name="operating-system-support-windows"></a>Stöd för operativ system (Windows)

I följande tabell sammanfattas de operativ system som stöds när du säkerhetskopierar virtuella Windows Azure-datorer.

**Scenario** | **OS-stöd**
--- | ---
Säkerhetskopiera med tillägget Azure VM agent | – Windows 10-klient (endast 64-bitars) <br/><br/>– Windows Server 2019 (Data Center/Data Center Core/standard) <br/><br/> – Windows Server 2016 (Data Center/Data Center Core/standard) <br/><br/> – Windows Server 2012 R2 (Data Center/standard) <br/><br/> – Windows Server 2012 (Data Center/standard) <br/><br/> – Windows Server 2008 R2 (RTM och SP1 standard)  <br/><br/> – Windows Server 2008 (endast 64 bitars)
Säkerhetskopiera med MARS-agenten | Operativ system som [stöds](backup-support-matrix-mars-agent.md#supported-operating-systems) .
Säkerhetskopiera med DPM/MABS | Operativ system som stöds för säkerhets kopiering med [Mabs](backup-mabs-protection-matrix.md) och [DPM](/system-center/dpm/dpm-protection-matrix).

Azure Backup stöder inte 32-bitars operativsystem.

## <a name="support-for-linux-backup"></a>Stöd för Linux-säkerhetskopiering

Det här är vad som stöds om du vill säkerhetskopiera Linux-datorer.

**Åtgärd** | **Support**
--- | ---
Säkerhetskopiera virtuella Linux Azure-datorer med Linux Azure VM-agenten | Fil konsekvent säkerhets kopiering.<br/><br/> Appkonsekvent säkerhetskopiering med hjälp av [anpassade skript](backup-azure-linux-app-consistent.md).<br/><br/> Under återställningen kan du skapa en ny virtuell dator, återställa en disk och använda den för att skapa en virtuell dator, eller återställa en disk och använda den för att ersätta en disk på en befintlig virtuell dator. Du kan också återställa enskilda filer och mappar.
Säkerhetskopiera virtuella Linux Azure-datorer med MARS-agent | Stöds inte.<br/><br/> MARS-agenten kan endast installeras på Windows-datorer.
Säkerhetskopiera virtuella Linux Azure-datorer med DPM/MABS | Stöds inte.

## <a name="operating-system-support-linux"></a>Stöd för operativ system (Linux)

För Azure VM Linux-säkerhetskopieringar stöder Azure Backup listan över Linux- [distributioner som har godkänts av Azure](../virtual-machines/linux/endorsed-distros.md). Observera följande:

- Azure Backup stöder inte Core OS Linux.
- Azure Backup stöder inte 32-bitars operativsystem.
- Andra distributioner av en egen Linux-distribution kan fungera så länge [Azure VM-agenten för Linux](../virtual-machines/extensions/agent-linux.md) är tillgänglig på den virtuella datorn och så länge python stöds.
- Azure Backup stöder inte en proxy-konfigurerad Linux-baserad virtuell dator om den inte har python version 2,7 installerad.

## <a name="backup-frequency-and-retention"></a>Säkerhets kopierings frekvens och kvarhållning

**Inställning** | **Gränser**
--- | ---
Högsta antal återställnings punkter per skyddad instans (dator/arbets belastning) | 9999.
Maximal förfallo tid för en återställnings punkt | Ingen gräns.
Maximal säkerhets kopierings frekvens till valv (Azure VM-tillägg) | En gång om dagen.
Maximal säkerhets kopierings frekvens till valv (MARS-agent) | Tre säkerhets kopieringar per dag.
Högsta säkerhetskopieringsfrekvens till DPM/MABS | Var 15: e minut för SQL Server.<br/><br/> En gång i timmen för andra arbets belastningar.
Kvarhållning av återställningspunkt | Varje dag, varje vecka, varje månad och varje år.
Högsta kvarhållningsperiod | Beror på säkerhetskopieringsfrekvensen.
Återställningspunkter på DPM-/MABS-disk | 64 för fil servrar och 448 för App-servrar.<br/><br/> Bandåterställningspunkter är obegränsade för lokal DPM.

## <a name="supported-restore-methods"></a>Återställnings metoder som stöds

**Återställnings alternativ** | **Information**
--- | ---
**Skapa en ny virtuell dator** | Skapar och hämtar snabbt en grundläggande virtuell dator som körs från en återställnings punkt.<br/><br/> Du kan ange ett namn på den virtuella datorn, välja resurs gruppen och det virtuella nätverk (VNet) där det ska placeras och ange ett lagrings konto för den återställda virtuella datorn. Den nya virtuella datorn måste skapas i samma region som den virtuella käll datorn.
**Återställ disk** | Återställer en virtuell dator disk, som sedan kan användas för att skapa en ny virtuell dator.<br/><br/> Azure Backup innehåller en mall som hjälper dig att anpassa och skapa en virtuell dator. <br/><br> Återställnings jobbet genererar en mall som du kan hämta och använda för att ange anpassade VM-inställningar och skapa en virtuell dator.<br/><br/> Diskarna kopieras till den resurs grupp som du anger.<br/><br/> Alternativt kan du koppla disken till en befintlig virtuell dator eller skapa en ny virtuell dator med hjälp av PowerShell.<br/><br/> Det här alternativet är användbart om du vill anpassa den virtuella datorn, lägga till konfigurations inställningar som inte fanns vid tidpunkten för säkerhets kopieringen eller lägga till inställningar som måste konfigureras med mallen eller PowerShell.
**Ersätt befintlig** | Du kan återställa en disk och använda den för att ersätta en disk på den befintliga virtuella datorn.<br/><br/> Den aktuella virtuella datorn måste finnas. Det här alternativet kan inte användas om det har tagits bort.<br/><br/> Azure Backup tar en ögonblicks bild av den befintliga virtuella datorn innan disken ersätts och lagrar den på den mellanlagringsplats som du anger. Befintliga diskar som är anslutna till den virtuella datorn ersätts med den valda återställnings punkten.<br/><br/> Ögonblicks bilden kopieras till valvet och bevaras i enlighet med bevarande principen. <br/><br/> Efter åtgärden Ersätt disk behålls den ursprungliga disken i resurs gruppen. Du kan välja att manuellt ta bort de ursprungliga diskarna om de inte behövs. <br/><br/>Ersätt befintlig stöds för okrypterade hanterade virtuella datorer. Den stöds inte för ohanterade diskar, [generaliserade virtuella datorer](../virtual-machines/windows/capture-image-resource.md)eller för virtuella datorer som [skapats med anpassade avbildningar](https://azure.microsoft.com/resources/videos/create-a-custom-virtual-machine-image-in-azure-resource-manager-with-powershell/).<br/><br/> Om återställnings punkten har fler eller färre diskar än den aktuella virtuella datorn kommer antalet diskar i återställnings punkten bara att avspegla konfigurationen för den virtuella datorn.<br><br> Ersätt befintlig stöds också för virtuella datorer med länkade resurser, t. ex. [användardefinierad hanterad identitet](../active-directory/managed-identities-azure-resources/overview.md) och [Key Vault](../key-vault/general/overview.md).
**Mellan region (sekundär region)** | Återställning mellan regioner kan användas för att återställa virtuella Azure-datorer i den sekundära regionen, som är en [Azure-kopplad region](../best-practices-availability-paired-regions.md#what-are-paired-regions).<br><br> Du kan återställa alla virtuella Azure-datorer för den valda återställnings punkten om säkerhets kopian görs i den sekundära regionen.<br><br> Den här funktionen är tillgänglig för alternativen nedan:<br> <li> [Skapa en virtuell dator](./backup-azure-arm-restore-vms.md#create-a-vm) <br> <li> [Återställa diskar](./backup-azure-arm-restore-vms.md#restore-disks) <br><br> Vi stöder för närvarande inte alternativet [Ersätt befintliga diskar](./backup-azure-arm-restore-vms.md#replace-existing-disks) .<br><br> Behörigheter<br> Återställnings åtgärden i den sekundära regionen kan utföras av säkerhets kopierings administratörer och app-administratörer.

## <a name="support-for-file-level-restore"></a>Stöd för återställning på filnivå

**Återställa** | **Tillåtna**
--- | ---
Återställa filer över operativ system | Du kan återställa filer på alla datorer som har samma (eller kompatibla) OS som den säkerhetskopierade virtuella datorn. Se den [kompatibla OS-tabellen](backup-azure-restore-files-from-vm.md#system-requirements).
Återställa filer från krypterade virtuella datorer | Stöds inte.
Återställa filer från nätverk-begränsade lagrings konton | Stöds inte.
Återställa filer på virtuella datorer med Windows lagrings utrymmen | Återställning stöds inte på samma virtuella dator.<br/><br/> Återställ i stället filerna på en kompatibel virtuell dator.
Återställa filer på den virtuella Linux-datorn med LVM/RAID-matriser | Återställning stöds inte på samma virtuella dator.<br/><br/> Återställa på en kompatibel virtuell dator.
Återställa filer med särskilda nätverks inställningar | Återställning stöds inte på samma virtuella dator. <br/><br/> Återställa på en kompatibel virtuell dator.

## <a name="support-for-vm-management"></a>Stöd för hantering av virtuella datorer

I följande tabell sammanfattas stödet för säkerhets kopiering under aktiviteter för hantering av virtuella datorer, till exempel att lägga till eller ersätta VM-diskar.

**Återställa** | **Tillåtna**
--- | ---
Återställ över prenumeration/region/zon. | Stöds inte.
Återställa till en befintlig virtuell dator | Använd alternativet Ersätt disk.
Återställa disk med lagrings konto aktiverat för Azure Storage Service Encryption (SSE) | Stöds inte.<br/><br/> Återställ till ett konto som inte har SSE aktiverat.
Återställ till blandade lagrings konton |Stöds inte.<br/><br/> Alla återställda diskar är antingen Premium eller standard och inte blandade, baserat på lagrings konto typen.
Återställa den virtuella datorn direkt till en tillgänglighets uppsättning | För hanterade diskar kan du återställa disken och använda alternativet tillgänglighets uppsättning i mallen.<br/><br/> Stöds inte för ohanterade diskar. För ohanterade diskar återställer du disken och skapar sedan en virtuell dator i tillgänglighets uppsättningen.
Återställa säkerhets kopia av ohanterade virtuella datorer efter uppgradering till hanterad virtuell dator| Stöds.<br/><br/> Du kan återställa diskar och sedan skapa en hanterad virtuell dator.
Återställ den virtuella datorn till återställnings punkten innan den virtuella datorn migrerades till Managed disks | Stöds.<br/><br/> Du återställer till ohanterade diskar (standard), konverterar de återställda diskarna till den hanterade disken och skapar en virtuell dator med de hanterade diskarna.
Återställa en virtuell dator som har tagits bort. | Stöds.<br/><br/> Du kan återställa den virtuella datorn från en återställnings punkt.
Återställa en domänkontrollant (DC) virtuell dator som ingår i en konfiguration med flera domänkontrollanter via portalen | Stöds om du återställer disken och skapar en virtuell dator med hjälp av PowerShell.
Återställ virtuell dator i ett annat virtuellt nätverk |Stöds.<br/><br/> Det virtuella nätverket måste finnas i samma prenumeration och region.

## <a name="vm-compute-support"></a>Stöd för VM Compute

**Beräkning** | **Support**
--- | ---
Storlek på virtuell dator |Valfri storlek på virtuella Azure-datorer med minst 2 processor kärnor och 1 GB RAM.<br/><br/> [Läs mer.](../virtual-machines/sizes.md)
Säkerhetskopiera virtuella datorer i [tillgänglighets uppsättningar](../virtual-machines/availability.md#availability-sets) | Stöds.<br/><br/> Du kan inte återställa en virtuell dator i en tillgänglig uppsättning genom att använda alternativet för att snabbt skapa en virtuell dator. När du i stället återställer den virtuella datorn återställer du disken och använder den för att distribuera en virtuell dator, eller återställer en disk och använder den för att ersätta en befintlig disk.
Säkerhetskopiera virtuella datorer som har distribuerats med [Hybrid Use Benefit (hubb)](../virtual-machines/windows/hybrid-use-benefit-licensing.md) | Stöds.
Säkerhetskopiera virtuella datorer som distribueras i en [skalnings uppsättning](../virtual-machine-scale-sets/overview.md) |Stöds. [Orchestration-läget](../virtual-machine-scale-sets/orchestration-modes.md) ska vara inställt på 2 för fel domän. Tillgänglighets uppsättningen stöds inte.
Säkerhetskopiera virtuella datorer som distribueras från [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps?filters=virtual-machine-images)<br/><br/> (Publicerat av Microsoft, tredje part) |Stöds.<br/><br/> Den virtuella datorn måste köra ett operativ system som stöds.<br/><br/> När du återställer filer på den virtuella datorn kan du bara återställa till ett kompatibelt operativ system (inte ett tidigare eller senare operativ system). Vi återställer inte virtuella Azure Marketplace-datorer som har säkerhetskopierats som virtuella datorer, eftersom dessa behov av inköps information. De återställs bara som diskar.
Säkerhetskopiera virtuella datorer som distribueras från en anpassad avbildning (tredje part) |Stöds.<br/><br/> Den virtuella datorn måste köra ett operativ system som stöds.<br/><br/> När du återställer filer på den virtuella datorn kan du bara återställa till ett kompatibelt operativ system (inte ett tidigare eller senare operativ system).
Säkerhetskopiera virtuella datorer som migreras till Azure| Stöds.<br/><br/> För att säkerhetskopiera den virtuella datorn måste den virtuella dator agenten vara installerad på den migrerade datorn.
Säkerhetskopiera konsekvens för flera virtuella datorer | Azure Backup tillhandahåller inte data-och program konsekvens över flera virtuella datorer.
Säkerhetskopiera med [diagnostikinställningar](../azure-monitor/platform/platform-logs-overview.md)  | Som inte stöds. <br/><br/> Om återställningen av den virtuella Azure-datorn med diagnostiska inställningar utlöses med alternativet [Skapa nytt](backup-azure-arm-restore-vms.md#create-a-vm) , Miss lyckas återställningen.
Återställa zoner-fästa virtuella datorer | Stöds (för VM som säkerhets kopie ras efter 2019 och där [tillgänglighets zon](https://azure.microsoft.com/global-infrastructure/availability-zones/) är tillgängliga).<br/><br/>Vi stöder för närvarande återställning till samma zon som är fäst i virtuella datorer. Men om zonen inte är tillgänglig, Miss lyckas återställningen.
Virtuella Gen2-datorer | Stöds <br> Azure Backup stöder säkerhets kopiering och återställning av [virtuella Gen2-datorer](https://azure.microsoft.com/updates/generation-2-virtual-machines-in-azure-public-preview/). När de här virtuella datorerna återställs från återställnings punkten återställs de som [virtuella Gen2-datorer](https://azure.microsoft.com/updates/generation-2-virtual-machines-in-azure-public-preview/).
Säkerhets kopiering av virtuella Azure-datorer med lås | Stöds inte för ohanterade virtuella datorer. <br><br> Stöds för hanterade virtuella datorer.
[Virtuella Spot-datorer](../virtual-machines/windows/spot-vms.md) | Som inte stöds. Azure Backup återställer virtuella datorer på plats som vanliga virtuella Azure-datorer.

## <a name="vm-storage-support"></a>Stöd för VM-lagring

**Komponent** | **Support**
--- | ---
Datadiskar för virtuella Azure-datorer | Stöd för säkerhets kopiering av virtuella Azure-datorer med upp till 32 diskar finns i offentlig för hands version i alla regioner.<br><br> Stöd för säkerhets kopiering av virtuella Azure-datorer med ohanterade diskar eller klassiska virtuella datorer är bara upp till 16 diskar.
Data disk storlek | Enskild disk storlek kan vara upp till 32 TB och högst 256 TB kombinerat för alla diskar i en virtuell dator.
Lagringstyp | Standard HDD Standard SSD Premium SSD.
Hanterade diskar | Stöds.
Krypterade diskar | Stöds.<br/><br/> Virtuella Azure-datorer med Azure Disk Encryption kan säkerhets kopie ras (med eller utan Azure AD-appen).<br/><br/> Det går inte att återställa krypterade virtuella datorer på nivån fil/mapp. Du måste återställa hela den virtuella datorn.<br/><br/> Du kan aktivera kryptering på virtuella datorer som redan skyddas av Azure Backup.
Diskar med Skrivningsaccelerator aktiverat | Stöds inte.<br/><br/> Azure Backup undantar automatiskt diskarna med Skrivningsaccelerator (WA) som aktive ras under säkerhets kopieringen. Eftersom de inte har säkerhetskopierats kan du inte återställa diskarna från återställnings punkter på den virtuella datorn. <br><br> **Viktigt meddelande**: virtuella datorer med WA-diskar behöver Internet anslutning för att det ska gå att säkerhetskopiera (även om diskarna exkluderas från säkerhets kopian.)
Säkerhetskopiera & återställa deduplicerade virtuella datorer/diskar | Azure Backup har inte stöd för deduplicering. Mer information finns i den här [artikeln](./backup-support-matrix.md#disk-deduplication-support) <br/> <br/>  -Azure Backup deduplicerar inte mellan virtuella datorer i Recovery Services-valvet <br/> <br/>  – Om det finns virtuella datorer i Deduplicerings tillstånd under återställningen kan filerna inte återställas eftersom valvet inte förstår formatet. Du kan dock utföra fullständig återställning av virtuella datorer.
Lägg till disk i skyddad virtuell dator | Stöds.
Ändra storlek på disk på skyddad virtuell dator | Stöds.
Delad lagring| Säkerhets kopiering av virtuella datorer med klusterdelad volym (CSV) eller Skalbar filserver stöds inte. CSV-skrivare fungerar sannolikt inte under säkerhets kopieringen. Vid återställning kanske diskar som innehåller CSV-volymer inte kommer att visas.
[Delade diskar](../virtual-machines/windows/disks-shared-enable.md) | Stöds inte.

## <a name="vm-network-support"></a>Stöd för virtuella dator nätverk

**Komponent** | **Support**
--- | ---
Antal nätverks gränssnitt (NIC) | Upp till det maximala antalet nätverkskort som stöds för en angiven virtuell Azure-dator storlek.<br/><br/> Nätverkskort skapas när den virtuella datorn skapas under återställnings processen.<br/><br/> Antalet nätverkskort på den återställda virtuella datorn speglar antalet nätverkskort på den virtuella datorn när du aktiverade skyddet. Att ta bort nätverkskort när du har aktiverat skyddet påverkar inte antalet.
Extern/intern belastningsutjämnare |Stöds. <br/><br/> [Lär dig mer](backup-azure-arm-restore-vms.md#restore-vms-with-special-configurations) om att återställa virtuella datorer med särskilda nätverks inställningar.
Flera reserverade IP-adresser |Stöds. <br/><br/> [Lär dig mer](backup-azure-arm-restore-vms.md#restore-vms-with-special-configurations) om att återställa virtuella datorer med särskilda nätverks inställningar.
Virtuella datorer med flera nätverkskort| Stöds. <br/><br/> [Lär dig mer](backup-azure-arm-restore-vms.md#restore-vms-with-special-configurations) om att återställa virtuella datorer med särskilda nätverks inställningar.
Virtuella datorer med offentliga IP-adresser| Stöds.<br/><br/> Koppla en befintlig offentlig IP-adress till NÄTVERKSKORTet eller skapa en adress och koppla den till NÄTVERKSKORTet när återställningen är färdig.
Nätverks säkerhets grupp (NSG) på NIC/undernät. |Stöds.
Statisk IP-adress | Stöds inte.<br/><br/> En ny virtuell dator som skapas från en återställnings punkt tilldelas en dynamisk IP-adress.<br/><br/> För klassiska virtuella datorer kan du inte säkerhetskopiera en virtuell dator med en reserverad IP-adress och ingen definierad slut punkt.
Dynamisk IP-adress |Stöds.<br/><br/> Om NÄTVERKSKORTet på den virtuella käll datorn använder dynamisk IP-adressering, kommer NÄTVERKSKORTet på den återställda virtuella datorn också att använda den.
Azure Traffic Manager| Stöds.<br/><br/>Om den säkerhetskopierade virtuella datorn finns i Traffic Manager lägger du manuellt till den återställda virtuella datorn i samma Traffic Manager instans.
Azure DNS |Stöds.
Anpassad DNS |Stöds.
Utgående anslutning via HTTP-proxy | Stöds.<br/><br/> En autentiserad proxy stöds inte.
Tjänstslutpunkter för virtuellt nätverk| Stöds.<br/><br/> Inställningar för lagrings konto för brand vägg och virtuellt nätverk ska tillåta åtkomst från alla nätverk.

## <a name="vm-security-and-encryption-support"></a>VM-säkerhet och krypterings stöd

Azure Backup stöder kryptering för data under överföring och i vila:

Nätverkstrafik till Azure:

- Säkerhets kopierings trafik från servrar till Recovery Services-valvet krypteras med hjälp av Advanced Encryption Standard 256.
- Säkerhetskopierade data skickas via en säker HTTPS-anslutning.
- Säkerhetskopierade data lagras i Recovery Services-valvet i krypterad form.
- Du har bara krypterings nyckeln för att låsa upp dessa data. Microsoft kan aldrig dekryptera säkerhetskopierade data.

  > [!WARNING]
  > När du har konfigurerat valvet har du bara åtkomst till krypterings nyckeln. Microsoft sparar aldrig någon kopia och har inte åtkomst till nyckeln. Om du tappar bort nyckeln kan Microsoft inte återställa dina säkerhetskopierade data.

Datasäkerhet:

- När du säkerhetskopierar virtuella Azure-datorer måste du konfigurera kryptering *i* den virtuella datorn.
- Azure Backup stöder Azure Disk Encryption som använder BitLocker på virtuella Windows-datorer och US **dm-crypt** på virtuella Linux-datorer.
- På serverdelen använder Azure Backup [Azure Storage Service-kryptering](../storage/common/storage-service-encryption.md), som skyddar vilande data.

**Dator** | **Under överföring** | **I vila**
--- | --- | ---
Lokala Windows-datorer utan DPM/MABS | ![Ja][green] | ![Ja][green]
Virtuella Azure-datorer | ![Ja][green] | ![Ja][green]
Lokala/virtuella Azure-datorer med DPM | ![Ja][green] | ![Ja][green]
Lokala/virtuella Azure-datorer med MABS | ![Ja][green] | ![Ja][green]

## <a name="vm-compression-support"></a>Stöd för VM-komprimering

Säkerhets kopiering stöder komprimering av säkerhets kopierings trafik, som sammanfattas i följande tabell. Observera följande:

- För virtuella Azure-datorer läser VM-tillägget data direkt från Azure Storage-kontot över lagrings nätverket. Det är inte nödvändigt att komprimera den här trafiken.
- Om du använder DPM eller MABS kan du spara bandbredd genom att komprimera data innan de säkerhets kopie ras till DPM/MABS.

**Dator** | **Komprimera till MABS/DPM (TCP)** | **Komprimera till valv (HTTPS)**
--- | --- | ---
Lokala Windows-datorer utan DPM/MABS | Ej tillämpligt | ![Ja][green]
Virtuella Azure-datorer | NA | NA
Lokala/virtuella Azure-datorer med DPM | ![Ja][green] | ![Ja][green]
Lokala/virtuella Azure-datorer med MABS | ![Ja][green] | ![Ja][green]

## <a name="next-steps"></a>Nästa steg

- [Säkerhetskopiera virtuella Azure-datorer](backup-azure-arm-vms-prepare.md).
- [Säkerhetskopiera Windows-datorer direkt](tutorial-backup-windows-server-to-azure.md) utan en reservserver.
- [Konfigurera MABS](backup-azure-microsoft-azure-backup.md) för säkerhetskopiering till Azure och säkerhetskopiera sedan arbetsbelastningar till MABS.
- [Konfigurera DPM](backup-azure-dpm-introduction.md) för säkerhetskopiering till Azure och säkerhetskopiera sedan arbetsbelastningar till DPM.

[green]: ./media/backup-support-matrix/green.png
[yellow]: ./media/backup-support-matrix/yellow.png
[red]: ./media/backup-support-matrix/red.png
