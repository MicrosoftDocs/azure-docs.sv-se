---
title: Vanliga frågor och svar – säkerhetskopiera virtuella Azure-datorer
description: I den här artikeln hittar du svar på vanliga frågor om hur du säkerhetskopierar virtuella Azure-datorer med tjänsten Azure Backup.
ms.reviewer: sogup
ms.topic: conceptual
ms.date: 09/17/2019
ms.openlocfilehash: 03e2f004fa54ee235eabc49afd6abd7532a6ee44
ms.sourcegitcommit: 64ad2c8effa70506591b88abaa8836d64621e166
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/17/2020
ms.locfileid: "88262780"
---
# <a name="frequently-asked-questions-back-up-azure-vms"></a>Vanliga frågor och svar – säkerhetskopiera virtuella Azure-datorer

I den här artikeln besvaras vanliga frågor om säkerhets kopiering av virtuella Azure-datorer med tjänsten [Azure Backup](./backup-overview.md) .

## <a name="backup"></a>Backup

### <a name="which-vm-images-can-be-enabled-for-backup-when-i-create-them"></a>Vilka VM-avbildningar kan aktive ras för säkerhets kopiering när jag skapar dem?

När du skapar en virtuell dator kan du aktivera säkerhets kopiering för virtuella datorer som kör [operativ system som stöds](backup-support-matrix-iaas.md#supported-backup-actions).

### <a name="is-the-backup-cost-included-in-the-vm-cost"></a>Ingår säkerhets kopierings kostnaden i kostnaden för den virtuella datorn?

Nej. Kostnaderna för säkerhets kopiering skiljer sig från en VM-kostnad. Läs mer om [Azure Backup prissättning](https://azure.microsoft.com/pricing/details/backup/).

### <a name="which-permissions-are-required-to-enable-backup-for-a-vm"></a>Vilka behörigheter krävs för att aktivera säkerhets kopiering för en virtuell dator?

Om du är en VM-deltagare kan du aktivera säkerhets kopiering på den virtuella datorn. Om du använder en anpassad roll behöver du följande behörigheter för att aktivera säkerhets kopiering på den virtuella datorn:

- Microsoft. RecoveryServices/valv/skriva
- Microsoft. RecoveryServices/valv/läsa
- Microsoft. RecoveryServices/locations/*
- Microsoft. RecoveryServices/valv/backupFabrics/protectionContainers/protectedItems/*/Read
- Microsoft. RecoveryServices/valv/backupFabrics/protectionContainers/protectedItems/Read
- Microsoft. RecoveryServices/valv/backupFabrics/protectionContainers/protectedItems/Write
- Microsoft. RecoveryServices/valv/backupFabrics/backupProtectionIntent/Write
- Microsoft. RecoveryServices/valv/backupPolicies/läsa
- Microsoft. RecoveryServices/valv/backupPolicies/Write

Om Recovery Services valvet och den virtuella datorn har olika resurs grupper kontrollerar du att du har Skriv behörighet i resurs gruppen för Recovery Servicess valvet.  

### <a name="does-an-on-demand-backup-job-use-the-same-retention-schedule-as-scheduled-backups"></a>Använder ett säkerhets kopierings jobb på begäran samma bevarande schema som schemalagda säkerhets kopieringar?

Nej. Ange kvarhållningsintervall för ett säkerhets kopierings jobb på begäran. Som standard sparas den i 30 dagar när den utlöses från portalen.

### <a name="i-recently-enabled-azure-disk-encryption-on-some-vms-will-my-backups-continue-to-work"></a>Jag nyligen har aktiverat Azure Disk Encryption på vissa virtuella datorer. Kommer mina säkerhetskopior att fungera även i fortsättningen?

Ange behörigheter för Azure Backup för att få åtkomst till Key Vault. Ange behörigheter i PowerShell enligt beskrivningen i avsnittet **Aktivera säkerhets kopiering** i [Azure Backup PowerShell](backup-azure-vms-automation.md) -dokumentationen.

### <a name="i-migrated-vm-disks-to-managed-disks-will-my-backups-continue-to-work"></a>Jag migrerade VM-diskar till hanterade diskar. Kommer mina säkerhetskopior att fungera även i fortsättningen?

Ja, säkerhets kopieringarna fungerar sömlöst. Du behöver inte konfigurera om något.

### <a name="why-cant-i-see-my-vm-in-the-configure-backup-wizard"></a>Varför kan jag inte se min virtuella dator i guiden Konfigurera säkerhetskopiering?

Guiden visar endast virtuella datorer i samma region som valvet och som inte redan säkerhets kopie ras.

### <a name="my-vm-is-shut-down-will-an-on-demand-or-a-scheduled-backup-work"></a>Den virtuella datorn stängs av. Kommer en säkerhets kopiering på begäran eller en schemalagd säkerhets kopiering att fungera?

Ja. Säkerhets kopieringar körs när en dator stängs av. Återställnings punkten markeras som krasch-konsekvent.

### <a name="can-i-cancel-an-in-progress-backup-job"></a>Kan jag avbryta ett pågående säkerhets kopierings jobb?

Ja. Du kan avbryta säkerhets kopierings jobbet med status för **ögonblicks bilder** . Du kan inte avbryta ett jobb om data överföringen från ögonblicks bilden pågår.

### <a name="i-enabled-a-lock-on-the-resource-group-created-by-azure-backup-service-for-example-azurebackuprg_geo_number-will-my-backups-continue-to-work"></a>Jag aktiverade ett lås på resurs gruppen som skapades av Azure Backup-tjänsten (till exempel `AzureBackupRG_<geo>_<number>` ). Kommer mina säkerhetskopior att fungera även i fortsättningen?

Om du låser resurs gruppen som skapats av tjänsten Azure Backup, kommer säkerhets kopieringarna att Miss lyckas eftersom det finns en övre gräns på 18 återställnings punkter.

Ta bort låset och rensa återställnings punkt samlingen från resurs gruppen för att göra framtida säkerhets kopieringar genomförda. [Följ de här stegen](backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout.md#clean-up-restore-point-collection-from-azure-portal) för att ta bort återställnings punkts samlingen.

### <a name="does-azure-backup-support-standard-ssd-managed-disks"></a>Har Azure Backup stöd för standard SSD-hanterade diskar?

Ja, Azure Backup stöder [standard-SSD Managed disks](https://azure.microsoft.com/blog/announcing-general-availability-of-standard-ssd-disks-for-azure-virtual-machine-workloads/).

### <a name="can-we-back-up-a-vm-with-a-write-accelerator-wa-enabled-disk"></a>Kan vi säkerhetskopiera en virtuell dator med en Skrivningsaccelerator (WA)-aktiverad disk?

Ögonblicks bilder kan inte utföras på den WA-aktiverade disken. Azure Backups tjänsten kan dock utesluta den WA-aktiverade disken från säkerhets kopian.

### <a name="i-have-a-vm-with-write-accelerator-wa-disks-and-sap-hana-installed-how-do-i-back-up"></a>Jag har en virtuell dator med Skrivningsaccelerator-diskar (WA) och SAP HANA installerade. Hur gör jag för att säkerhetskopiera?

Azure Backup kan inte säkerhetskopiera den WA-aktiverade disken, men kan utesluta den från en säkerhets kopia. Säkerhets kopieringen ger dock inte databas konsekvens eftersom information på den WA-aktiverade disken inte säkerhets kopie ras. Du kan säkerhetskopiera diskar med den här konfigurationen om du vill ha säkerhets kopiering av operativ system diskar och säkerhets kopiering av diskar som inte är WA-aktiverade.

Azure Backup tillhandahåller en strömmande säkerhets kopierings lösning för SAP HANA databaser med en återställning på 15 minuter. Det är Backint certifierat av SAP för att ge en inbyggd säkerhets kopierings support som utnyttjar SAP HANA inbyggda API: er. Läs mer [om hur du säkerhetskopierar SAP HANA databaser på virtuella Azure-datorer](./sap-hana-db-about.md).

### <a name="what-is-the-maximum-delay-i-can-expect-in-backup-start-time-from-the-scheduled-backup-time-i-have-set-in-my-vm-backup-policy"></a>Vad är den största fördröjning som jag förväntar dig i Start tiden för säkerhets kopieringen från den schemalagda säkerhets kopierings tiden jag har angett i min princip för säkerhets kopiering

Den schemalagda säkerhets kopieringen aktive ras inom 2 timmar efter den schemalagda säkerhets kopierings tiden. Om till exempel 100 VM: ar har schemalagt start tid för säkerhets kopieringen till 2:00 AM, sedan efter 4:00 är det senaste för alla virtuella 100-datorer att deras säkerhets kopierings jobb pågår. Om schemalagda säkerhets kopieringar har pausats på grund av ett avbrott och återförsöket, kan säkerhets kopieringen starta utanför det schemalagda två Tim fönstret.

### <a name="what-is-the-minimum-allowed-retention-range-for-a-daily-backup-point"></a>Vad är det lägsta tillåtna kvarhållningsintervallet för en daglig säkerhets kopierings punkt?

Säkerhets kopierings principen för virtuella Azure-datorer stöder ett minsta kvarhållningsintervall från sju dagar upp till 9999 dagar. Ändringar i en befintlig princip för säkerhets kopiering av virtuella datorer med mindre än sju dagar kräver en uppdatering för att uppfylla det lägsta kvarhållningsintervallet på sju dagar.

### <a name="what-happens-if-i-change-the-case-of-the-name-of-my-vm-or-my-vm-resource-group"></a>Vad händer om jag ändrar Skift läget för namnet på den virtuella datorn eller min VM-resurs grupp?

Om du ändrar Skift läget (till övre eller lägre) för din virtuella dator eller resurs grupp för virtuell dator ändras inte namnet på säkerhets kopians objekt namn. Detta är dock förväntat Azure Backup beteende. Ärende ändringen visas inte i säkerhets kopierings objekt, utan uppdateras på Server delen.

### <a name="can-i-back-up-or-restore-selective-disks-attached-to-a-vm"></a>Kan jag säkerhetskopiera eller återställa selektiva diskar som är anslutna till en virtuell dator?

Azure Backup har nu stöd för säkerhets kopiering och återställning av selektiva diskar med den virtuella Azure-datorn säkerhets kopierings lösning. Mer information finns i [selektiv säkerhets kopiering av diskar och återställning för virtuella Azure-datorer](selective-disk-backup-restore.md).

### <a name="are-managed-identities-preserved-if-a-tenant-change-occurs-during-backup"></a>Behålls hanterade identiteter om en klient förändring sker under säkerhets kopieringen?

Om [klient ändringar](https://docs.microsoft.com/azure/devops/organizations/accounts/change-azure-ad-connection) sker måste du inaktivera och återaktivera [hanterade identiteter](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) för att säkerhets kopieringen ska fungera igen.

## <a name="restore"></a>Återställ

### <a name="how-do-i-decide-whether-to-restore-disks-only-or-a-full-vm"></a>Hur gör jag för att bestämma om du bara vill återställa diskar eller en fullständig virtuell dator?

Tänk på en VM-återställning som ett snabb skapande alternativ för en virtuell Azure-dator. Det här alternativet ändrar disk namn, behållare som används av diskarna, offentliga IP-adresser och nätverks gränssnitts namn. Ändringen upprätthåller unika resurser när en virtuell dator skapas. Den virtuella datorn har inte lagts till i en tillgänglighets uppsättning.

Du kan använda alternativet Återställ disk om du vill:

- Anpassa den virtuella datorn som skapas. Ändra till exempel storleken.
- Lägg till konfigurations inställningar som inte fanns vid säkerhets kopierings tillfället.
- Styr namngivnings konventionen för resurser som skapas.
- Lägg till den virtuella datorn i en tillgänglighets uppsättning.
- Lägg till andra inställningar som måste konfigureras med hjälp av PowerShell eller en mall.

### <a name="can-i-restore-backups-of-unmanaged-vm-disks-after-i-upgrade-to-managed-disks"></a>Kan jag återställa säkerhets kopior av ohanterade VM-diskar efter att jag uppgraderat till Managed disks?

Ja, du kan använda säkerhets kopior som tagits innan diskarna migrerades från ohanterade till hanterade.

### <a name="how-do-i-restore-a-vm-to-a-restore-point-before-the-vm-was-migrated-to-managed-disks"></a>Hur återställer jag en virtuell dator till en återställningspunkt innan den virtuella datorn har migrerats till hanterade diskar?

Återställnings processen förblir densamma. Om återställnings punkten är en tidpunkt då den virtuella datorn hade ohanterade diskar kan du [återställa diskarna som ohanterade](tutorial-restore-disk.md#unmanaged-disks-restore). Om den virtuella datorn hade hanterade diskar kan du [återställa diskarna som Managed disks](tutorial-restore-disk.md#managed-disk-restore). Sedan kan du [skapa en virtuell dator från dessa diskar](tutorial-restore-disk.md#create-a-vm-from-the-restored-disk).

[Läs mer](backup-azure-vms-automation.md#restore-an-azure-vm) om hur du gör detta i PowerShell.

### <a name="if-the-restore-fails-to-create-the-vm-what-happens-to-the-disks-included-in-the-restore"></a>Om återställningen Miss lyckas med att skapa den virtuella datorn, vad händer med diskarna som ingår i återställningen?

I händelse av en hanterad VM-återställning, även om det inte går att skapa en virtuell dator, kommer diskarna fortfarande att återställas.

### <a name="can-i-restore-a-vm-thats-been-deleted"></a>Kan jag återställa en virtuell dator som har tagits bort?

Ja. Även om du tar bort den virtuella datorn kan du gå till motsvarande säkerhets kopierings objekt i valvet och återställa från en återställnings punkt.

### <a name="how-do-i-restore-a-vm-to-the-same-availability-sets"></a>Hur gör jag för att återställa en virtuell dator till samma tillgänglighets uppsättningar?

För hanterade virtuella Azure-datorer aktive ras återställning till tillgänglighets uppsättningarna genom att tillhandahålla ett alternativ i mallen när de återställs som hanterade diskar. Den här mallen har Indataparametern inheter **tillgänglighets uppsättningar**.

### <a name="how-do-we-get-faster-restore-performances"></a>Hur får vi snabbare återställning av prestanda?

[Omedelbar återställnings](backup-instant-restore-capability.md) funktion hjälper till med snabbare säkerhets kopieringar och återställer omedelbart från ögonblicks bilderna.

### <a name="what-happens-when-we-change-the-key-vault-settings-for-the-encrypted-vm"></a>Vad händer när vi ändrar nyckel valvs inställningarna för den krypterade virtuella datorn?

När du har ändrat nyckel valvs inställningarna för den krypterade virtuella datorn fortsätter säkerhets kopieringarna att fungera med den nya uppsättningen med information. Men efter återställningen från en återställnings punkt innan ändringen måste du återställa hemligheterna i ett nyckel valv innan du kan skapa den virtuella datorn från den. Mer information finns i den här [artikeln](./backup-azure-restore-key-secret.md).

Åtgärder som Secret/Key Enrollment-on kräver inte det här steget och samma nyckel valv kan användas efter återställning.

### <a name="can-i-access-the-vm-once-restored-due-to-a-vm-having-broken-relationship-with-domain-controller"></a>Kan jag få åtkomst till den virtuella datorn när den återställts på grund av en virtuell dator med en skadad relation med domänkontrollanten?

Ja, du får åtkomst till den virtuella datorn när den återställts på grund av en virtuell dator med en bruten relation med domänkontrollanten. Mer information finns i den här [artikeln](./backup-azure-arm-restore-vms.md#post-restore-steps)

## <a name="manage-vm-backups"></a>Hantera säkerhetskopior av virtuella datorer

### <a name="what-happens-if-i-modify-a-backup-policy"></a>Vad händer om jag ändrar en säkerhets kopierings policy?

Den virtuella datorn säkerhets kopie ras med inställningarna för schemaläggning och kvarhållning i den ändrade eller nya principen.

- Om kvarhållning är utökad, markeras befintliga återställnings punkter i enlighet med den nya principen.
- Om kvarhållning minskas markeras återställnings punkter för rensning i nästa rensnings jobb och tas sedan bort.

### <a name="how-do-i-move-a-vm-backed-up-by-azure-backup-to-a-different-resource-group"></a>Hur gör jag för att flyttar du en virtuell dator som har säkerhetskopierats av Azure Backup till en annan resurs grupp?

1. Stoppa säkerhets kopieringen tillfälligt och behåll säkerhets kopierings data.
2. Gör så här om du vill flytta virtuella datorer som kon figurer ATS med Azure Backup:

   1. Hitta platsen för den virtuella datorn.
   2. Hitta en resurs grupp med följande namn mönster: `AzureBackupRG_<location of your VM>_1` . Till exempel *AzureBackupRG_westus2_1*
   3. I Azure Portal markerar du **Visa dolda typer**.
   4. Hitta resursen med typen **Microsoft. Compute/restorePointCollections** som har namngivnings mönstret `AzureBackup_<name of your VM that you're trying to move>_###########` .
   5. Ta bort den här resursen. Den här åtgärden tar bara bort direkta återställnings punkter, inte säkerhetskopierade data i valvet.
   6. När borttagnings åtgärden har slutförts kan du flytta den virtuella datorn.

3. Flytta den virtuella datorn till mål resurs gruppen.
4. Återuppta säkerhets kopieringen.

Du kan återställa den virtuella datorn från tillgängliga återställnings punkter som skapades före flytt åtgärden.

### <a name="what-happens-after-i-move-a-vm-to-a-different-resource-group"></a>Vad händer när jag flyttar en virtuell dator till en annan resurs grupp?

När en virtuell dator har flyttats till en annan resurs grupp är det en ny virtuell dator i den mån Azure Backup berörs.

När du flyttar den virtuella datorn till en ny resurs grupp kan du skydda den virtuella datorn antingen i samma valv eller i ett annat valv. Eftersom det här är en ny virtuell dator för Azure Backup debiteras du för den separat.

Den gamla återställnings punkten för den gamla datorn kommer att vara tillgänglig för återställning om det behövs. Om du inte behöver dessa säkerhetskopierade data kan du sluta skydda din gamla virtuella dator med ta bort data.

### <a name="is-there-a-limit-on-number-of-vms-that-can-beassociated-with-the-same-backup-policy"></a>Finns det en gräns för antalet virtuella datorer som kan associeras med samma säkerhets kopierings princip?

Ja, det finns en gräns på 100 virtuella datorer som kan kopplas till samma säkerhets kopierings princip från portalen. Vi rekommenderar att du skapar flera säkerhets kopierings principer med samma schema eller ett annat schema för fler än 100 virtuella datorer.

### <a name="how-can-i-view-the-retention-settings-for-my-backups"></a>Hur kan jag se inställningarna för kvarhållning för mina säkerhets kopieringar?

För närvarande kan du Visa inställningar för kvarhållning på en säkerhets kopierings objekt nivå (VM) utifrån den säkerhets kopierings princip som har tilldelats den virtuella datorn. 

Ett sätt att visa inställningarna för kvarhållning av dina säkerhets kopior är att gå till [instrument panelen](https://docs.microsoft.com/azure/backup/backup-azure-manage-vms#view-vms-on-the-dashboard) för säkerhets kopierings objekt för den virtuella datorn i Azure Portal. Genom att klicka på länken till säkerhets kopierings principen kan du se Retentions tiden för alla dagliga, vecko Visa och årliga bevarande punkter som är associerade med den virtuella datorn.

Du kan också använda [Backup Explorer](https://docs.microsoft.com/azure/backup/monitor-azure-backup-with-backup-explorer) för att visa inställningarna för kvarhållning för alla virtuella datorer i samma fönster ruta. Gå till backup Explorer från ett Recovery Services valv, gå till fliken **säkerhets kopierings objekt** och välj Avancerad vy för att se detaljerad kvarhållning av information för varje virtuell dator.  