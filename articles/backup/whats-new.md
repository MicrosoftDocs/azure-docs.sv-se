---
title: Nyheter i Azure Backup
description: Lär dig mer om nya funktioner i Azure Backup.
ms.topic: conceptual
ms.date: 11/11/2020
ms.openlocfilehash: 68e0e5cc0876840c30ab9e428a2b96bd7d667756
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/15/2021
ms.locfileid: "107516339"
---
# <a name="whats-new-in-azure-backup"></a>Nyheter i Azure Backup

Azure Backup hela tiden på att förbättra och lansera nya funktioner som förbättrar skyddet av dina data i Azure. De här nya funktionerna utökar ditt dataskydd till nya arbetsbelastningstyper, förbättrar säkerheten och förbättrar tillgängligheten för dina säkerhetskopierade data. De lägger också till nya hanterings-, övervaknings- och automatiseringsfunktioner.

Du kan lära dig mer om de nya versionerna genom att bokmärka den här sidan [eller prenumerera på uppdateringar här.](https://azure.microsoft.com/updates/?query=backup)

## <a name="updates-summary"></a>Sammanfattning av uppdateringar

- Mars 2021
  - [Azure Disk Backup är nu allmänt tillgängligt](#azure-disk-backup-is-now-generally-available)
  - [Backup Center är nu allmänt tillgängligt](#backup-center-is-now-generally-available)
  - [Stöd för arkivnivå för Azure Backup (i förhandsversion)](#archive-tier-support-for-azure-backup-in-preview)
- Februari 2021
  - [Säkerhetskopiering för Azure-blobar (i förhandsversion)](#backup-for-azure-blobs-in-preview)
- Januari 2021
  - [Azure Disk Backup (förhandsversion)](#azure-disk-backup-in-preview)
  - [Kryptering i vila med kund hanterade nycklar (allmän tillgänglighet)](#encryption-at-rest-using-customer-managed-keys)
- November 2020
  - [Azure Resource Manager för säkerhetskopiering av Azure-filresurs (AFS)](#azure-resource-manager-template-for-afs-backup)
  - [Inkrementella säkerhetskopieringar SAP HANA databaser på virtuella Azure-datorer (i förhandsversion)](#incremental-backups-for-sap-hana-databases-in-preview)
- September 2020
  - [Backup Center (i förhandsversion)](#backup-center-in-preview)
  - [Säkerhetskopiering Azure Database for PostgreSQL (i förhandsversion)](#backup-azure-database-for-postgresql-in-preview)
  - [Selektiv säkerhetskopiering och återställning av disk](#selective-disk-backup-and-restore)
  - [Återställning mellan regioner för SQL Server och SAP HANA på virtuella Azure-datorer (i förhandsversion)](#cross-region-restore-for-sql-server-and-sap-hana-in-preview)
  - [Stöd för säkerhetskopiering av virtuella datorer med upp till 32 diskar (allmän tillgänglighet)](#support-for-backup-of-vms-with-up-to-32-disks)
  - [Förenklad konfiguration av säkerhetskopiering för SQL på virtuella Azure-datorer](#simpler-backup-configuration-for-sql-in-azure-vms)
  - [Säkerhetskopiering SAP HANA RHEL Azure Virtual Machines (i förhandsversion)](#backup-sap-hana-in-rhel-azure-virtual-machines-in-preview)
  - [Zonredundant lagring (ZRS) för säkerhetskopierade data (i förhandsversion)](#zone-redundant-storage-zrs-for-backup-data-in-preview)
  - [Mjuk borttagning för SQL Server och SAP HANA arbetsbelastningar på virtuella Azure-datorer](#soft-delete-for-sql-server-and-sap-hana-workloads)

## <a name="azure-disk-backup-is-now-generally-available"></a>Azure Disk Backup är nu allmänt tillgängligt

Azure Backup erbjuder livscykelhantering för ögonblicksbilder till Azure Managed Disks genom att automatisera periodiskt skapande av ögonblicksbilder och behålla dessa under konfigurerade varaktigheter med hjälp av säkerhetskopieringspolicyn.

Mer information finns i [Översikt över Azure Disk Backup.](disk-backup-overview.md)

## <a name="backup-center-is-now-generally-available"></a>Backup Center är nu allmänt tillgängligt

Backup Center förenklar hanteringen av dataskydd i stor skala genom att du kan identifiera, styra, övervaka, använda och optimera hanteringen av säkerhetskopiering från en enda central konsol.

Mer information finns i [Översikt över Backup Center.](backup-center-overview.md)

## <a name="archive-tier-support-for-azure-backup-in-preview"></a>Stöd för arkivnivå för Azure Backup (i förhandsversion)

Azure Backup kan du nu minska kostnaden för säkerhetskopior med långsiktig kvarhållning med tillgängligheten för arkivnivån för virtuella Azure-datorer och SQL Server på virtuella Azure-datorer.

Mer information finns i Stöd [för arkivnivå (förhandsversion).](archive-tier-support.md)

## <a name="backup-for-azure-blobs-in-preview"></a>Säkerhetskopiering för Azure-blobar (i förhandsversion)

Driftsäkerhetskopiering för blobar är en hanterad, lokal dataskyddslösning som gör att du kan skydda blockblobar från olika dataförlustscenarier, till exempel skador, blobborttagningar och oavsiktlig borttagning av lagringskonto. Data lagras lokalt i själva källlagringskontot och kan återställas till en vald tidpunkt när det behövs. Det ger ett enkelt, säkert och kostnadseffektivt sätt att skydda dina blobar.

Driftsäkerhetskopiering för blobar integreras med Backup Center, bland andra funktioner för säkerhetskopieringshantering, för att tillhandahålla ett enda fönster som kan hjälpa dig att styra, övervaka, använda och analysera säkerhetskopior i stor skala.

Mer information finns i Översikt [över driftsäkerhetskopiering för Azure Blobs (i förhandsversion).](blob-backup-overview.md)

## <a name="azure-disk-backup-in-preview"></a>Azure Disk Backup (förhandsversion)

Azure Disk Backup erbjuder en nyckelfärdig lösning som tillhandahåller livscykelhantering för ögonblicksbilder för [Azure Managed Disks](../virtual-machines/managed-disks-overview.md) genom att automatisera periodiskt skapande av ögonblicksbilder och behålla dem under en konfigurerad varaktighet med hjälp av säkerhetskopieringspolicyn. Du kan hantera ögonblicksbilder av diskar utan någon infrastrukturkostnad och utan behov av anpassade skript eller hanteringskostnader. Det här är en krasch-konsekvent [säkerhetskopieringslösning](../virtual-machines/disks-incremental-snapshots.md) som tar säkerhetskopiering till tidpunkt av en hanterad disk med hjälp av inkrementella ögonblicksbilder med stöd för flera säkerhetskopior per dag. Det är också en lösning utan agent och påverkar inte prestandan för produktionsprogram. Den stöder säkerhetskopiering och återställning av både operativsystem- och datadiskar (inklusive delade diskar), oavsett om de för närvarande är anslutna till en virtuell Azure-dator som körs.

Mer information finns i [Azure Disk Backup (i förhandsversion).](disk-backup-overview.md)

## <a name="encryption-at-rest-using-customer-managed-keys"></a>Kryptering i vila med kund hanterade nycklar

Stöd för kryptering i vila med kund hanterade nycklar är nu allmänt tillgängligt. Detta ger dig möjlighet att kryptera säkerhetskopierade data i dina Recovery Services-valv med dina egna nycklar som lagras i Azure Key Vaults. Krypteringsnyckeln som används för att kryptera säkerhetskopior i Recovery Services-valvet kan vara annorlunda än den som används för att kryptera källan. Data skyddas med en AES 256-baserad datakrypteringsnyckel (DEK), som i sin tur skyddas med hjälp av dina nycklar som lagras i Key Vault. Jämfört med kryptering med plattformsbaserade nycklar (som är tillgängliga som standard) ger detta dig större kontroll över dina nycklar och kan hjälpa dig att bättre uppfylla dina efterlevnadsbehov.

Mer information finns i Kryptering [av säkerhetskopierade data med kund hanterade nycklar.](encryption-at-rest-with-cmk.md)

## <a name="azure-resource-manager-template-for-afs-backup"></a>Azure Resource Manager för AFS-säkerhetskopiering

Azure Backup nu stöd för att konfigurera säkerhetskopiering för befintliga Azure-filresurser med hjälp av Azure Resource Manager (ARM)-mall. Mallen konfigurerar skydd för en befintlig Azure-filresurs genom att ange lämplig information för Recovery Services-valvet och säkerhetskopieringspolicyn. Du kan också skapa ett nytt Recovery Services-valv och en säkerhetskopieringsprincip och registrera lagringskontot som innehåller filresursen till Recovery Services-valvet.

Mer information finns i [Azure Resource Manager för Azure Backup](backup-rm-template-samples.md).

## <a name="incremental-backups-for-sap-hana-databases-in-preview"></a>Inkrementella säkerhetskopieringar SAP HANA databaser (i förhandsversion)

Azure Backup har nu stöd för inkrementella säkerhetskopieringar SAP HANA databaser som finns på virtuella Azure-datorer. Detta möjliggör snabbare och mer kostnadseffektiva säkerhetskopieringar av dina SAP HANA data.

Mer information finns i olika [alternativ som är tillgängliga när](/sap-hana-faq-backup-azure-vm.yml#policy) du skapar en säkerhetskopieringspolicy och hur du skapar en säkerhetskopieringspolicy för SAP HANA [databaser.](tutorial-backup-sap-hana-db.md#creating-a-backup-policy)

## <a name="backup-center-in-preview"></a>Backup Center (i förhandsversion)

Azure Backup har aktiverat en ny inbyggd hanteringsfunktion för att hantera hela din säkerhetskopia från en central konsol. Backup Center ger dig möjlighet att övervaka, hantera, styra och optimera dataskydd i stor skala på ett enhetligt sätt som är konsekvent med Azures inbyggda hanteringsupplevelser.

Med Backup Center får du en sammanställd vy över inventeringen över prenumerationer, platser, resursgrupper, valv och till och med klienter med hjälp av Azure Lighthouse. Backup Center är också en åtgärdscenter där du kan utlösa dina säkerhetskopieringsrelaterade aktiviteter, till exempel konfigurera säkerhetskopiering, återställning, skapande av principer eller valv, allt från en enda plats. Med sömlös integrering till Azure Policy kan du nu styra din miljö och spåra efterlevnad ur ett säkerhetskopieringsperspektiv. Med inbyggda Azure-principer som är Azure Backup kan du även konfigurera säkerhetskopieringar i stor skala.

Mer information finns i [Översikt över Backup Center.](backup-center-overview.md)

## <a name="backup-azure-database-for-postgresql-in-preview"></a>Säkerhetskopiering Azure Database for PostgreSQL (i förhandsversion)

Azure Backup och Azure Database Services har gått samman för att skapa en säkerhetskopieringslösning i företagsklass för Azure PostgreSQL (nu i förhandsversion). Nu kan du uppfylla dina behov av dataskydd och efterlevnad med en kundkontrollerad säkerhetskopieringspolicy som möjliggör kvarhållning av säkerhetskopior i upp till 10 år. Med detta får du detaljerad kontroll för att hantera säkerhetskopierings- och återställningsåtgärder på enskild databasnivå. På samma sätt kan du enkelt återställa mellan PostgreSQL-versioner eller bloblagring.

Mer information finns i [Azure Database for PostgreSQL backup](backup-azure-database-postgresql.md).

## <a name="selective-disk-backup-and-restore"></a>Selektiv säkerhetskopiering och återställning av disk

Azure Backup stöd för säkerhetskopiering av alla diskar (operativsystem och data) på en virtuell dator tillsammans med hjälp av säkerhetskopieringslösningen för virtuella datorer. Med hjälp av funktionen för selektiv säkerhetskopiering och återställning av diskar kan du nu säkerhetskopiera en delmängd av datadiskarna på en virtuell dator. Detta ger en effektiv och kostnadseffektiv lösning för dina säkerhetskopierings- och återställningsbehov. Varje återställningspunkt innehåller bara de diskar som ingår i säkerhetskopieringen.

Mer information finns i Selektiv [säkerhetskopiering och återställning av diskar för virtuella Azure-datorer.](selective-disk-backup-restore.md)

## <a name="cross-region-restore-for-sql-server-and-sap-hana-in-preview"></a>Återställning mellan regioner för SQL Server och SAP HANA (i förhandsversion)

Med introduktionen av återställning mellan regioner kan du nu initiera återställningar i en sekundär region för att minimera problem med verklig stilleståndstid i en primär region för din miljö. Detta gör att den sekundära regionen återställs helt kundkontrollerat. Azure Backup använder säkerhetskopierade data som replikeras till den sekundära regionen för sådana återställningar.

Förutom stöd för återställning mellan regioner för virtuella Azure-datorer har funktionen utökats till att även återställa SQL SAP HANA databaser på virtuella Azure-datorer.

Mer information finns i Återställning [mellan regioner för SQL-databaser och](restore-sql-database-azure-vm.md#cross-region-restore) Återställning mellan regioner för SAP HANA [databaser.](sap-hana-db-restore.md#cross-region-restore)

## <a name="support-for-backup-of-vms-with-up-to-32-disks"></a>Stöd för säkerhetskopiering av virtuella datorer med upp till 32 diskar

Fram till nu Azure Backup stöd för 16 hanterade diskar per virtuell dator. Nu har Azure Backup stöd för säkerhetskopiering av upp till 32 hanterade diskar per virtuell dator.

Mer information finns i stödmatrisen [för VM-lagring.](backup-support-matrix-iaas.md#vm-storage-support)

## <a name="simpler-backup-configuration-for-sql-in-azure-vms"></a>Enklare konfiguration av säkerhetskopiering för SQL på virtuella Azure-datorer

Att konfigurera säkerhetskopior för din SQL Server virtuella Azure-datorer är nu ännu enklare med inbyggd säkerhetskopieringskonfiguration integrerad i vm-fönstret i Azure Portal. Med bara några få steg kan du aktivera säkerhetskopiering av SQL Server för att skydda alla befintliga databaser samt de som läggs till i framtiden.

Mer information finns i [Back up a SQL Server from the VM pane](backup-sql-server-vm-from-vm-pane.md).

## <a name="backup-sap-hana-in-rhel-azure-virtual-machines-in-preview"></a>Säkerhetskopiering SAP HANA virtuella RHEL Azure-datorer (i förhandsversion)

Azure Backup är den inbyggda säkerhetskopieringslösningen för Azure och backint-certifierad av SAP. Azure Backup har nu lagt till stöd för Red Hat Enterprise Linux (RHEL), ett av de mest använda Linux-operativsystemen som kör SAP HANA.

Mer information finns i supportmatrisen [SAP HANA för säkerhetskopiering av databaser.](sap-hana-backup-support-matrix.md#scenario-support)

## <a name="zone-redundant-storage-zrs-for-backup-data-in-preview"></a>Zonredundant lagring (ZRS) för säkerhetskopierade data (i förhandsversion)

Azure Storage ger en bra balans mellan hög prestanda, hög tillgänglighet och hög dataåter återhämtning med dess olika redundansalternativ. Azure Backup kan du utöka dessa fördelar till att även säkerhetskopiera data, med alternativ för att lagra dina säkerhetskopior i lokalt redundant lagring (LRS) och geo-redundant lagring (GRS). Nu finns det ytterligare hållbarhetsalternativ med extra stöd för zonredundant lagring (ZRS).

Mer information finns i Ange [lagringsredundans för Recovery Services-valvet](backup-create-rs-vault.md#set-storage-redundancy).

## <a name="soft-delete-for-sql-server-and-sap-hana-workloads"></a>Mjuk borttagning för SQL Server och SAP HANA arbetsbelastningar

Oron för säkerhetsproblem som skadlig kod, utpressningstrojaner och intrång ökar. Dessa säkerhetsproblem kan vara kostsamma både vad gäller pengar och data. För att skydda dig mot sådana attacker Azure Backup säkerhetsfunktioner som skyddar säkerhetskopierade data även efter borttagning.

En sådan funktion är mjuk borttagning. Med mjuk borttagning, även om en illvillig aktör tar bort en säkerhetskopia (eller om säkerhetskopierade data tas bort av misstag), bevaras säkerhetskopierade data i ytterligare 14 dagar, vilket gör att säkerhetskopieringsobjektet kan återställas utan dataförlust. De ytterligare 14 dagarnas kvarhållning för säkerhetskopierade data i läget "mjuk borttagning" medför inte någon kostnad för dig.

Förutom stöd för mjuk borttagning för virtuella Azure-datorer skyddas nu även SQL Server och SAP HANA på virtuella Azure-datorer med mjuk borttagning.

Mer information finns i Mjuk borttagning [för SQL Server på virtuella Azure-datorer och SAP HANA i Azure VM-arbetsbelastningar.](soft-delete-sql-saphana-in-azure-vm.md)

## <a name="next-steps"></a>Nästa steg

- [Azure Backup vägledning och metodtips](guidance-best-practices.md)