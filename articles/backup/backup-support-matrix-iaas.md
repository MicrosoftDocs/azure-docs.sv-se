---
title: Supportmatris för säkerhetskopiering av virtuella Azure-datorer
description: Innehåller en sammanfattning av supportinställningar och begränsningar vid backning av virtuella Azure-datorer med Azure Backup tjänsten.
ms.topic: conceptual
ms.date: 09/13/2019
ms.custom: references_regions
ms.openlocfilehash: 1f63d0c3ad448a8ab9b91764d4c369fefddea25d
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/15/2021
ms.locfileid: "107516730"
---
# <a name="support-matrix-for-azure-vm-backup"></a>Supportmatris för säkerhetskopiering av virtuella Azure-datorer

Du kan använda [Azure Backup-tjänsten](backup-overview.md) för att backa upp lokala datorer och arbetsbelastningar samt virtuella Azure-datorer (VM). I den här artikeln sammanfattas supportinställningar och begränsningar när du kommer tillbaka till virtuella Azure-datorer med Azure Backup.

Andra supportmatriser:

- [Allmän supportmatris](backup-support-matrix.md) för Azure Backup
- [Stödmatris](backup-support-matrix-mabs-dpm.md) för säkerhetskopiering Azure Backup server/System Center Data Protection Manager (DPM)
- [Stödmatris](backup-support-matrix-mars-agent.md) för säkerhetskopiering med MARS Microsoft Azure (Recovery Services)-agenten

## <a name="supported-scenarios"></a>Scenarier som stöds

Så här kan du återställa virtuella Azure-datorer med hjälp av Azure Backup tjänsten.

**Scenario** | **Säkerhetskopiering** | **Agent** |**Återställ**
--- | --- | --- | ---
Direkt säkerhetskopiering av virtuella Azure-datorer  | Backa upp hela den virtuella datorn.  | Ingen ytterligare agent behövs på den virtuella Azure-datorn. Azure Backup installerar och använder ett tillägg till Azure [VM-agenten](../virtual-machines/extensions/agent-windows.md) som körs på den virtuella datorn. | Återställ på följande sätt:<br/><br/> - **Skapa en grundläggande virtuell dator**. Detta är användbart om den virtuella datorn inte har någon särskild konfiguration, till exempel flera IP-adresser.<br/><br/> - **Återställ den virtuella datordisken**. Återställ disken. Anslut den sedan till en befintlig virtuell dator eller skapa en ny virtuell dator från disken med hjälp av PowerShell.<br/><br/> - **Ersätt VM-disken**. Om en virtuell dator finns och den använder hanterade diskar (okrypterade) kan du återställa en disk och använda den för att ersätta en befintlig disk på den virtuella datorn.<br/><br/> - **Återställa specifika filer/mappar.** Du kan återställa filer/mappar från en virtuell dator i stället för från hela den virtuella datorn.
Direkt säkerhetskopiering av virtuella Azure-datorer (endast Windows)  | Back up specific files/folders/volume. | Installera [Azure Recovery Services-agenten](backup-azure-file-folder-backup-faq.yml).<br/><br/> Du kan köra MARS-agenten tillsammans med säkerhetskopieringstillägget för Azure VM-agenten för att säkerhetskopiera den virtuella datorn på fil-/mappnivå. | Återställa specifika mappar/filer.
Säkerhetskopiera virtuella Azure-datorer till säkerhetskopieringsserver  | Back up files/folders/volumes; systemtillstånd/bare metal-filer; appdata till System Center DPM eller till Microsoft Azure Backup Server (MABS).<br/><br/> DPM/MABS säkerhetskopierar sedan till säkerhetskopieringsvalvet. | Installera DPM/MABS-skyddsagenten på den virtuella datorn. MARS-agenten installeras på DPM/MABS.| Återställa filer/mappar/volymer; systemtillstånd/bare metal-filer; appdata.

Läs mer om säkerhetskopiering [med hjälp av en säkerhetskopieringsserver](backup-architecture.md#architecture-back-up-to-dpmmabs) och [supportkrav.](backup-support-matrix-mabs-dpm.md)

## <a name="supported-backup-actions"></a>Säkerhetskopieringsåtgärder som stöds

**Åtgärd** | **Support**
--- | ---
Back up a VM that's shutdown/offline VM | Stöds.<br/><br/> Ögonblicksbilden är endast krasch konsekvent, inte app-konsekvent.
Back up disks after migrationrating to managed disks ( Back up disks efter migrering till hanterade diskar) | Stöds.<br/><br/> Säkerhetskopieringen fortsätter att fungera. Ingen åtgärd krävs.
Back up managed disks after enabling resource group lock | Stöds inte.<br/><br/> Azure Backup kan inte ta bort de äldre återställningspunkterna och säkerhetskopieringar misslyckas när den maximala gränsen för återställningspunkter nås.
Ändra säkerhetskopieringspolicy för en virtuell dator | Stöds.<br/><br/> Den virtuella datorn säkerhetskopieras med hjälp av inställningarna för schema och kvarhållning i den nya principen. Om kvarhållningsinställningarna utökas markeras och behålls befintliga återställningspunkter. Om de minskas rensas befintliga återställningspunkter i nästa rensningsjobb och tas bort så småningom.
Avbryta ett säkerhetskopieringsjobb| Stöds under ögonblicksbildsprocessen.<br/><br/> Stöds inte när ögonblicksbilden överförs till valvet.
Back up the VM to a different region or subscription ( Back up the VM to a different region eller subscription) |Stöds inte.<br><br>För att säkerhetskopieringen ska lyckas måste virtuella datorer finnas i samma prenumeration som valvet för säkerhetskopiering.
Säkerhetskopieringar per dag (via Azure VM-tillägget) | En schemalagd säkerhetskopiering per dag.<br/><br/>Tjänsten Azure Backup stöder upp till tre säkerhetskopieringar på begäran per dag och ytterligare en schemalagd säkerhetskopiering.
Säkerhetskopior per dag (via MARS-agenten) | Tre schemalagda säkerhetskopieringar per dag.
Säkerhetskopieringar per dag (via DPM/MABS) | Två schemalagda säkerhetskopieringar per dag.
Säkerhetskopiering varje månad/år| Stöds inte vid backning med Azure VM-tillägg. Endast varje dag och varje vecka stöds.<br/><br/> Du kan konfigurera principen för att behålla dagliga/veckovisa säkerhetskopior för månatliga/årliga kvarhållningsperioder.
Automatisk klockjustering | Stöds inte.<br/><br/> Azure Backup justeras inte automatiskt för ändringar i sommartid när du backar upp en virtuell dator.<br/><br/>  Ändra principen manuellt efter behov.
[Säkerhetsfunktioner för hybridsäkerhetskopiering](./backup-azure-security-feature.md) |Inaktivering av säkerhetsfunktioner stöds inte.
Back up the VM vars datortid ändras | Stöds inte.<br/><br/> Om datortiden ändras till en framtida datum/tid efter aktivering av säkerhetskopiering för den virtuella datorn, men även om tidsändringen återställs, garanteras inte en lyckad säkerhetskopiering.

## <a name="operating-system-support-windows"></a>Stöd för operativsystem (Windows)

I följande tabell sammanfattas de operativsystem som stöds vid backing up Windows Azure VMs (Virtuella Windows Azure-datorer).

**Scenario** | **Os-stöd**
--- | ---
Back up with Azure VM agent extension ( Back up with Azure VM agent extension ) | – Windows 10 klient (endast 64-bitars) <br/><br/>– Windows Server 2019 (Datacenter/Datacenter Core/Standard) <br/><br/> – Windows Server 2016 (Datacenter/Datacenter Core/Standard) <br/><br/> – Windows Server 2012 R2 (Datacenter/Standard) <br/><br/> – Windows Server 2012 (Datacenter/Standard) <br/><br/> – Windows Server 2008 R2 (RTM och SP1 Standard)  <br/><br/> – Windows Server 2008 (endast 64-bitars)
Tillbaka med MARS-agenten | [Operativsystem](backup-support-matrix-mars-agent.md#supported-operating-systems) som stöds.
Backa upp med DPM/MABS | Operativsystem som stöds för säkerhetskopiering med [MABS](backup-mabs-protection-matrix.md) och [DPM](/system-center/dpm/dpm-protection-matrix).

Azure Backup stöder inte 32-bitars operativsystem.

## <a name="support-for-linux-backup"></a>Stöd för Linux-säkerhetskopiering

Det här är vad som stöds om du vill säkerhetskopiera Linux-datorer.

**Åtgärd** | **Support**
--- | ---
Back up Linux Azure VMs with the Linux Azure VM agent | Fil konsekvent säkerhetskopiering.<br/><br/> Appkonsekvent säkerhetskopiering med hjälp av [anpassade skript](backup-azure-linux-app-consistent.md).<br/><br/> Under återställningen kan du skapa en ny virtuell dator, återställa en disk och använda den för att skapa en virtuell dator eller återställa en disk och använda den för att ersätta en disk på en befintlig virtuell dator. Du kan också återställa enskilda filer och mappar.
Back up Linux Azure VMs with MARS agent ( Back up Linux Azure VMs with MARS agent) Back up Linux Azure V | Stöds inte.<br/><br/> MARS-agenten kan endast installeras på Windows-datorer.
Back up Linux Azure VMs with DPM/MABS | Stöds inte.
Säkerhetskopiera virtuella Linux Azure-datorer med Docker-monteringspunkter | För närvarande Azure Backup stöder inte undantag av Docker-monteringspunkter eftersom de monteras på olika sökvägar varje gång.

## <a name="operating-system-support-linux"></a>Stöd för operativsystem (Linux)

För Azure VM Linux-säkerhetskopieringar stöder Azure Backup listan över Linux-distributioner [som godkänts av Azure](../virtual-machines/linux/endorsed-distros.md). . Tänk på följande:

- Azure Backup stöder inte Core OS Linux.
- Azure Backup stöder inte 32-bitars operativsystem.
- Andra Bring Your Own Linux-distributioner kan fungera så länge [Azure VM-agenten](../virtual-machines/extensions/agent-linux.md) för Linux är tillgänglig på den virtuella datorn och så länge Python stöds.
- Azure Backup stöder inte en proxykonfigurerad virtuell Linux-dator om python version 2.7 inte är installerat.
- Azure Backup har inte stöd för att backa upp NFS-filer som är monterade från lagring, eller från någon annan NFS-server, till Linux- eller Windows-datorer. Den backar bara upp diskar som är lokalt anslutna till den virtuella datorn.

## <a name="backup-frequency-and-retention"></a>Säkerhetskopieringsfrekvens och kvarhållning

**Inställning** | **Gränser**
--- | ---
Maximalt antal återställningspunkter per skyddad instans (dator/arbetsbelastning) | 9999.
Maximal förfallotid för en återställningspunkt | Ingen gräns (99 år).
Maximal säkerhetskopieringsfrekvens till valv (Azure VM-tillägg) | En gång om dagen.
Maximal säkerhetskopieringsfrekvens till valv (MARS-agent) | Tre säkerhetskopieringar per dag.
Högsta säkerhetskopieringsfrekvens till DPM/MABS | Var 15:e minut för SQL Server.<br/><br/> En gång i timmen för andra arbetsbelastningar.
Kvarhållning av återställningspunkt | Varje dag, varje vecka, varje månad och varje år.
Högsta kvarhållningsperiod | Beror på säkerhetskopieringsfrekvensen.
Återställningspunkter på DPM-/MABS-disk | 64 för filservrar och 448 för appservrar.<br/><br/> Bandåterställningspunkter är obegränsade för lokal DPM.

## <a name="supported-restore-methods"></a>Återställningsmetoder som stöds

**Återställningsalternativ** | **Information**
--- | ---
**Skapa en ny virtuell dator** | Skapar och kör snabbt igång en grundläggande virtuell dator från en återställningspunkt.<br/><br/> Du kan ange ett namn för den virtuella datorn, välja den resursgrupp och det virtuella nätverk (VNet) som den ska placeras i och ange ett lagringskonto för den återställda virtuella datorn. Den nya virtuella datorn måste skapas i samma region som den virtuella källdatorn.
**Återställ disk** | Återställer en virtuell datordisk, som sedan kan användas för att skapa en ny virtuell dator.<br/><br/> Azure Backup tillhandahåller en mall som hjälper dig att anpassa och skapa en virtuell dator. <br/><br> Återställningsjobbet genererar en mall som du kan ladda ned och använda för att ange anpassade inställningar för virtuella datorer och skapa en virtuell dator.<br/><br/> Diskarna kopieras till den resursgrupp som du anger.<br/><br/> Du kan också ansluta disken till en befintlig virtuell dator eller skapa en ny virtuell dator med Hjälp av PowerShell.<br/><br/> Det här alternativet är användbart om du vill anpassa den virtuella datorn, lägga till konfigurationsinställningar som inte fanns vid tidpunkten för säkerhetskopieringen eller lägga till inställningar som måste konfigureras med hjälp av mallen eller PowerShell.
**Ersätt befintlig** | Du kan återställa en disk och använda den för att ersätta en disk på den befintliga virtuella datorn.<br/><br/> Den aktuella virtuella datorn måste finnas. Om den har tagits bort kan det här alternativet inte användas.<br/><br/> Azure Backup tar en ögonblicksbild av den befintliga virtuella datorn innan du ersätter disken och lagrar den på den mellanlagringsplats som du anger. Befintliga diskar som är anslutna till den virtuella datorn ersätts med den valda återställningspunkten.<br/><br/> Ögonblicksbilden kopieras till valvet och bevaras i enlighet med bevarandeprincipen. <br/><br/> Efter diskåtgärden behålls den ursprungliga disken i resursgruppen. Du kan välja att manuellt ta bort de ursprungliga diskarna om de inte behövs. <br/><br/>Ersätt befintlig stöds för okrypterade hanterade virtuella datorer och för virtuella datorer som skapats [med anpassade avbildningar.](https://azure.microsoft.com/resources/videos/create-a-custom-virtual-machine-image-in-azure-resource-manager-with-powershell/) Det stöds inte för ohanterade diskar och virtuella datorer, klassiska virtuella datorer och [generaliserade virtuella datorer.](../virtual-machines/windows/capture-image-resource.md)<br/><br/> Om återställningspunkten har fler eller färre diskar än den aktuella virtuella datorn återspeglar antalet diskar i återställningspunkten endast VM-konfigurationen.<br><br> Ersätt befintlig stöds också för virtuella datorer med länkade resurser, till exempel [användar tilldelad hanterad identitet](../active-directory/managed-identities-azure-resources/overview.md) och [Key Vault](../key-vault/general/overview.md).
**Mellan regioner (sekundär region)** | Återställning mellan regioner kan användas för att återställa virtuella Azure-datorer i den sekundära regionen, som är en [länkad Azure-region.](../best-practices-availability-paired-regions.md#what-are-paired-regions)<br><br> Du kan återställa alla virtuella Azure-datorer för den valda återställningspunkten om säkerhetskopieringen görs i den sekundära regionen.<br><br> Den här funktionen är tillgänglig för alternativen nedan:<br> <li> [Skapa en virtuell dator](./backup-azure-arm-restore-vms.md#create-a-vm) <br> <li> [Återställ diskar](./backup-azure-arm-restore-vms.md#restore-disks) <br><br> Vi stöder för närvarande inte [alternativet Ersätt befintliga](./backup-azure-arm-restore-vms.md#replace-existing-disks) diskar.<br><br> Behörigheter<br> Återställningsåtgärden i den sekundära regionen kan utföras av säkerhetskopieringsadministratörer och appadministratörer.

## <a name="support-for-file-level-restore"></a>Stöd för återställning på filnivå

**Återställ** | **Stöds**
--- | ---
Återställa filer mellan operativsystem | Du kan återställa filer på alla datorer som har samma (eller kompatibla) operativsystem som den säkerhetskopierade virtuella datorn. Se tabellen [Compatible OS (Kompatibelt operativsystem).](backup-azure-restore-files-from-vm.md#step-3-os-requirements-to-successfully-run-the-script)
Återställa filer från krypterade virtuella datorer | Stöds inte.
Återställa filer från nätverksbegränsade lagringskonton | Stöds inte.
Återställa filer på virtuella datorer med Hjälp av Windows-lagringsutrymmen | Återställning stöds inte på samma virtuella dator.<br/><br/> Återställ i stället filerna på en kompatibel virtuell dator.
Återställa filer på virtuella Linux-datorer med LVM/raid-matriser | Återställning stöds inte på samma virtuella dator.<br/><br/> Återställa på en kompatibel virtuell dator.
Återställa filer med särskilda nätverksinställningar | Återställning stöds inte på samma virtuella dator. <br/><br/> Återställa på en kompatibel virtuell dator.
Återställa filer från delad disk, temporär enhet, deduplicerad disk, Ultra-disk och disk med skrivningsacceleratorn aktiverad | Återställning stöds inte, <br/><br/>Se [Azure VM-lagringsstöd](#vm-storage-support).

## <a name="support-for-vm-management"></a>Stöd för hantering av virtuella datorer

I följande tabell sammanfattas stöd för säkerhetskopiering under VM-hanteringsuppgifter, till exempel att lägga till eller ersätta VM-diskar.

**Återställ** | **Stöds**
--- | ---
Återställ i prenumeration/region/zon. | Stöds inte.
Återställa till en befintlig virtuell dator | Använd alternativet Ersätt disk.
Återställ disk med lagringskontot aktiverat Azure Storage SSE (Service Encryption) | Stöds inte.<br/><br/> Återställ till ett konto som inte har SSE aktiverat.
Återställa till blandade lagringskonton |Stöds inte.<br/><br/> Baserat på typen av lagringskonto är alla återställda diskar antingen Premium eller Standard och inte blandade.
Återställa en virtuell dator direkt till en tillgänglighetsuppsättning | För hanterade diskar kan du återställa disken och använda tillgänglighetsuppsättningsalternativet i mallen.<br/><br/> Stöds inte för ohanterade diskar. För ohanterade diskar återställer du disken och skapar sedan en virtuell dator i tillgänglighetsuppsättningen.
Återställa en säkerhetskopia av ohanterade virtuella datorer efter uppgradering till hanterad virtuell dator| Stöds.<br/><br/> Du kan återställa diskar och sedan skapa en hanterad virtuell dator.
Återställa en virtuell dator till en återställningspunkt innan den virtuella datorn migrerades till hanterade diskar | Stöds.<br/><br/> Du återställer till ohanterade diskar (standard), konverterar de återställda diskarna till hanterade diskar och skapar en virtuell dator med de hanterade diskarna.
Återställa en virtuell dator som har tagits bort. | Stöds.<br/><br/> Du kan återställa den virtuella datorn från en återställningspunkt.
Återställa en virtuell domänkontrollantsdatorn  | Stöds. Mer information finns i [Återställa virtuella domänkontrollanter.](backup-azure-arm-restore-vms.md#restore-domain-controller-vms)
Återställa en virtuell dator i ett annat virtuellt nätverk |Stöds.<br/><br/> Det virtuella nätverket måste finnas i samma prenumeration och region.

## <a name="vm-compute-support"></a>Stöd för VM-beräkning

**Beräkning** | **Support**
--- | ---
Storlek på virtuell dator |Valfri storlek på virtuella Azure-datorer med minst 2 CPU-kärnor och 1 GB RAM-minne.<br/><br/> [Läs mer.](../virtual-machines/sizes.md)
Tillförlitlighet för virtuella datorer i [tillgänglighetsuppsättningar](../virtual-machines/availability.md#availability-sets) | Stöds.<br/><br/> Du kan inte återställa en virtuell dator i en tillgänglig uppsättning genom att använda alternativet för att snabbt skapa en virtuell dator. När du återställer den virtuella datorn återställer du i stället disken och använder den för att distribuera en virtuell dator, eller återställer en disk och använder den för att ersätta en befintlig disk.
Back up VMs that are deployed with [Hybrid Use Benefit (HUB)](../virtual-machines/windows/hybrid-use-benefit-licensing.md) | Stöds.
Back up VMs that are deployed from [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps?filters=virtual-machine-images)<br/><br/> (Publicerat av Microsoft, tredje part) |Stöds.<br/><br/> Den virtuella datorn måste köra ett operativsystem som stöds.<br/><br/> När du återställer filer på den virtuella datorn kan du bara återställa till ett kompatibelt operativsystem (inte ett tidigare eller senare operativsystem). Vi återställer inte virtuella Azure Marketplace som virtuella datorer eftersom dessa behöver köpinformation. De återställs bara som diskar.
Back up VMs that are deployed from a custom image (third-party) ( Back up VMs that are deployed from a custom image (tredje part) |Stöds.<br/><br/> Den virtuella datorn måste köra ett operativsystem som stöds.<br/><br/> När du återställer filer på den virtuella datorn kan du bara återställa till ett kompatibelt operativsystem (inte ett tidigare eller senare operativsystem).
Back up VMs that are migrated to Azure| Stöds.<br/><br/> Vm-agenten måste vara installerad på den migrerade datorn för att kunna backa upp den virtuella datorn.
Hanterare av konsekvens för flera virtuella datorer | Azure Backup tillhandahåller inte data och programkonsekvens över flera virtuella datorer.
Säkerhetskopiering med [diagnostikinställningar](../azure-monitor/essentials/platform-logs-overview.md)  | Unsupported. <br/><br/> Om återställningen av den virtuella Azure-datorn med diagnostikinställningar utlöses med alternativet [Skapa](backup-azure-arm-restore-vms.md#create-a-vm) ny misslyckas återställningen.
Återställning av zon fästa virtuella datorer | Stöds (för en virtuell dator som säkerhetskopieras efter januari 2019 och där [tillgänglighetszoner](https://azure.microsoft.com/global-infrastructure/availability-zones/) är tillgängliga).<br/><br/>Vi stöder för närvarande återställning till samma zon som är fäst på virtuella datorer. Men om zonen inte är tillgänglig på grund av ett avbrott misslyckas återställningen.
Virtuella Gen2-datorer | Stöds <br> Azure Backup stöder säkerhetskopiering och återställning av [virtuella Gen2-datorer.](https://azure.microsoft.com/updates/generation-2-virtual-machines-in-azure-public-preview/) När de här virtuella datorerna återställs från återställningspunkten återställs de som [virtuella Gen2-datorer.](https://azure.microsoft.com/updates/generation-2-virtual-machines-in-azure-public-preview/)
Säkerhetskopiering av virtuella Azure-datorer med lås | Stöds inte för ohanterade virtuella datorer. <br><br> Stöds för hanterade virtuella datorer.
[Virtuella Spot-datorer](../virtual-machines/spot-vms.md) | Unsupported. Azure Backup virtuella spot-datorer som vanliga virtuella Azure-datorer.
[Azure Dedicated Host](../virtual-machines/dedicated-hosts.md) | Stöds
Windows Storage Spaces-konfiguration av fristående virtuella Azure-datorer | Stöds
[Azure VM Scale Sets](../virtual-machine-scale-sets/virtual-machine-scale-sets-orchestration-modes.md#scale-sets-with-flexible-orchestration) | Stöds för både enhetliga och flexibla orkestreringsmodeller för att backa upp och återställa en enskild virtuell Azure-dator.

## <a name="vm-storage-support"></a>Stöd för VM-lagring

**Komponent** | **Support**
--- | ---
Datadiskar för virtuella Azure-datorer | Stöd för säkerhetskopiering av virtuella Azure-datorer med upp till 32 diskar.<br><br> Stöd för säkerhetskopiering av virtuella Azure-datorer med ohanterade diskar eller klassiska virtuella datorer är endast upp till 16 diskar.
Datadiskstorlek | Den enskilda diskstorleken kan vara upp till 32 TB och högst 256 TB kombinerat för alla diskar på en virtuell dator.
Lagringstyp | Standard HDD, Standard SSD, Premium SSD.
Hanterade diskar | Stöds.
Krypterade diskar | Stöds.<br/><br/> Virtuella Azure-datorer som Azure Disk Encryption med kan säkerhetskopieras (med eller utan Azure AD-appen).<br/><br/> Krypterade virtuella datorer kan inte återställas på fil-/mappnivå. Du måste återställa hela den virtuella datorn.<br/><br/> Du kan aktivera kryptering på virtuella datorer som redan skyddas av Azure Backup.
Diskar med Skrivningsaccelerator aktiverat | Från och med den 23 november 2020 stöds endast i regionerna Sydkorea, centrala (KRC) och Sydafrika, norra (SAN) för ett begränsat antal prenumerationer. För de prenumerationer som stöds säkerhetskopierar Azure Backup de virtuella datorerna med diskar som är skrivnings-accelererade (WA) aktiverade under säkerhetskopieringen.<br><br>För de regioner som inte stöds krävs internetanslutning på den virtuella datorn för att ta ögonblicksbilder av Virtual Machines med WA aktiverat.<br><br> **Viktigt meddelande:** I de regioner som inte stöds behöver virtuella datorer med WA-diskar Internetanslutning för en lyckad säkerhetskopiering (även om dessa diskar är undantagna från säkerhetskopian).
Återställning & deduplicerade virtuella datorer/diskar | Azure Backup stöder inte deduplicering. Mer information finns i den här [artikeln](./backup-support-matrix.md#disk-deduplication-support) <br/> <br/>  – Azure Backup dedupliceras inte mellan virtuella datorer i Recovery Services-valvet <br/> <br/>  – Om det finns virtuella datorer i dedupliceringstillstånd under återställningen kan filerna inte återställas eftersom valvet inte förstår formatet. Du kan dock utföra den fullständiga återställningen av den virtuella datorn.
Lägga till disk till skyddad virtuell dator | Stöds.
Ändra storlek på disken på en skyddad virtuell dator | Stöds.
Delad lagring| Det finns inte stöd för att klusterdelad volym virtuella datorer med hjälp klusterdelad volym (CSV) eller Scale-Out-filserver. CSV-skrivare misslyckas troligen under säkerhetskopieringen. Vid återställning kanske diskar som innehåller CSV-volymer inte visas.
[Delade diskar](../virtual-machines/disks-shared-enable.md) | Stöds inte.
Ultra SSD diskar | Stöds inte. Mer information finns i dessa [begränsningar.](selective-disk-backup-restore.md#limitations)
[Temporära diskar](../virtual-machines/managed-disks-overview.md#temporary-disk) | Temporära diskar säkerhetskopieras inte av Azure Backup.
NVMe-/tillfälliga diskar | Stöds inte.

## <a name="vm-network-support"></a>Stöd för virtuella datornätverk

**Komponent** | **Support**
--- | ---
Antal nätverksgränssnitt (nätverkskort) | Upp till det maximala antalet nätverkskort som stöds för en specifik storlek för virtuella Azure-datorer.<br/><br/> Nätverkskort skapas när den virtuella datorn skapas under återställningsprocessen.<br/><br/> Antalet nätverkskort på den återställda virtuella datorn speglar antalet nätverkskort på den virtuella datorn när du aktiverade skydd. Antalet påverkas inte om du tar bort nätverkskort när du aktiverar skydd.
Extern/intern lastbalanserare |Stöds. <br/><br/> [Läs mer](backup-azure-arm-restore-vms.md#restore-vms-with-special-configurations) om att återställa virtuella datorer med särskilda nätverksinställningar.
Flera reserverade IP-adresser |Stöds. <br/><br/> [Läs mer](backup-azure-arm-restore-vms.md#restore-vms-with-special-configurations) om att återställa virtuella datorer med särskilda nätverksinställningar.
Virtuella datorer med flera nätverkskort| Stöds. <br/><br/> [Läs mer](backup-azure-arm-restore-vms.md#restore-vms-with-special-configurations) om att återställa virtuella datorer med särskilda nätverksinställningar.
Virtuella datorer med offentliga IP-adresser| Stöds.<br/><br/> Associera en befintlig offentlig IP-adress med nätverkskortet eller skapa en adress och associera den med nätverkskortet när återställningen är klar.
Nätverkssäkerhetsgrupp (NSG) på nätverkskort/undernät. |Stöds.
Statisk IP-adress | Stöds inte.<br/><br/> En ny virtuell dator som skapas från en återställningspunkt tilldelas en dynamisk IP-adress.<br/><br/> För klassiska virtuella datorer kan du inte bladet bladet en virtuell dator med en reserverad IP-adress och ingen definierad slutpunkt.
Dynamisk IP-adress |Stöds.<br/><br/> Om nätverkskortet på den virtuella källdatorn använder dynamisk IP-adressering kommer nätverkskortet på den återställda virtuella datorn som standard att använda det också.
Azure Traffic Manager| Stöds.<br/><br/>Om den säkerhetskopierade virtuella datorn finns i Traffic Manager lägger du manuellt till den återställda virtuella datorn till Traffic Manager instansen.
Azure DNS |Stöds.
Anpassad DNS |Stöds.
Utgående anslutning via HTTP-proxy | Stöds.<br/><br/> En autentiserad proxy stöds inte.
Tjänstslutpunkter för virtuella nätverk| Stöds.<br/><br/> Inställningar för lagringskonto för brandvägg och virtuellt nätverk bör tillåta åtkomst från alla nätverk.

## <a name="vm-security-and-encryption-support"></a>Stöd för säkerhet och kryptering för virtuella datorer

Azure Backup stöder kryptering för data under överföring och i vila:

Nätverkstrafik till Azure:

- Säkerhetskopieringstrafik från servrar till Recovery Services-valvet krypteras med hjälp av Advanced Encryption Standard 256.
- Säkerhetskopierade data skickas via en säker HTTPS-anslutning.
- Säkerhetskopierade data lagras i Recovery Services-valvet i krypterad form.
- Endast du har krypteringsnyckeln för att låsa upp dessa data. Microsoft kan aldrig dekryptera säkerhetskopierade data.

  > [!WARNING]
  > När du har ställt in valvet är det bara du som har åtkomst till krypteringsnyckeln. Microsoft sparar aldrig någon kopia och har inte åtkomst till nyckeln. Om du tappar bort nyckeln kan Microsoft inte återställa dina säkerhetskopierade data.

Datasäkerhet:

- När du ska brygga virtuella Azure-datorer måste du konfigurera kryptering *på* den virtuella datorn.
- Azure Backup stöder Azure Disk Encryption, som använder BitLocker på virtuella **Windows-datorer och dm-crypt** på virtuella Linux-datorer.
- På serverdelen använder Azure Backup [Azure Storage Service-kryptering](../storage/common/storage-service-encryption.md), som skyddar vilande data.

**Dator** | **Under överföring** | **I vila**
--- | --- | ---
Lokala Windows-datorer utan DPM/MABS | ![Ja][green] | ![Ja][green]
Virtuella Azure-datorer | ![Ja][green] | ![Ja][green]
Lokala/virtuella Azure-datorer med DPM | ![Ja][green] | ![Ja][green]
Lokala/virtuella Azure-datorer med MABS | ![Ja][green] | ![Ja][green]

## <a name="vm-compression-support"></a>Stöd för VM-komprimering

Säkerhetskopiering stöder komprimering av säkerhetskopieringstrafik, enligt sammanfattningen i följande tabell. . Tänk på följande:

- För virtuella Azure-datorer läser VM-tillägget data direkt från Azure-lagringskontot via lagringsnätverket. Du behöver inte komprimera den här trafiken.
- Om du använder DPM eller MABS kan du spara bandbredd genom att komprimera data innan de säkerhetskopieras till DPM/MABS.

**Dator** | **Komprimera till MABS/DPM (TCP)** | **Komprimera till valv (HTTPS)**
--- | --- | ---
Lokala Windows-datorer utan DPM/MABS | Ej tillämpligt | ![Ja][green]
Virtuella Azure-datorer | NA | NA
Lokala/virtuella Azure-datorer med DPM | ![Ja][green] | ![Ja][green]
Lokala/virtuella Azure-datorer med MABS | ![Ja][green] | ![Ja][green]

## <a name="next-steps"></a>Nästa steg

- [Back up Azure VMs](backup-azure-arm-vms-prepare.md).
- [Säkerhetskopiera Windows-datorer direkt](tutorial-backup-windows-server-to-azure.md) utan en reservserver.
- [Konfigurera MABS](backup-azure-microsoft-azure-backup.md) för säkerhetskopiering till Azure och säkerhetskopiera sedan arbetsbelastningar till MABS.
- [Konfigurera DPM](backup-azure-dpm-introduction.md) för säkerhetskopiering till Azure och säkerhetskopiera sedan arbetsbelastningar till DPM.

[green]: ./media/backup-support-matrix/green.png
[yellow]: ./media/backup-support-matrix/yellow.png
[red]: ./media/backup-support-matrix/red.png