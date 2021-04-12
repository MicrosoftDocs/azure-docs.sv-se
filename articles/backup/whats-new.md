---
title: Nyheter i Azure Backup
description: Lär dig mer om nya funktioner i Azure Backup.
ms.topic: conceptual
ms.date: 11/11/2020
ms.openlocfilehash: c5e6734c6a962fa43d79fc90fdfaa85923b6339f
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "105612491"
---
# <a name="whats-new-in-azure-backup"></a>Nyheter i Azure Backup

Azure Backup kan ständigt förbättra och lansera nya funktioner som förbättrar skyddet av dina data i Azure. De här nya funktionerna utökar ditt data skydd till nya arbets belastnings typer, förbättrar säkerheten och förbättrar tillgängligheten för dina säkerhets kopierings data. De lägger också till nya funktioner för hantering, övervakning och automatisering.

Du kan lära dig mer om de nya versionerna genom att skriva bok märken på den här sidan eller genom [att prenumerera på uppdateringar här](https://azure.microsoft.com/updates/?query=backup).

## <a name="updates-summary"></a>Uppdaterings Sammanfattning

- Mars 2021
  - [Azure disk Backup är nu allmänt tillgängligt](#azure-disk-backup-is-now-generally-available)
  - [Backup Center är nu allmänt tillgängligt](#backup-center-is-now-generally-available)
  - [Arkiv nivå stöd för Azure Backup (för hands version)](#archive-tier-support-for-azure-backup-in-preview)
- Februari 2021
  - [Säkerhets kopiering för Azure-blobar (i för hands version)](#backup-for-azure-blobs-in-preview)
- Januari 2021
  - [Säkerhets kopiering av Azure-disk (i för hands version)](#azure-disk-backup-in-preview)
  - [Kryptering i vila med Kundhanterade nycklar (allmän tillgänglighet)](#encryption-at-rest-using-customer-managed-keys)
- November 2020
  - [Azure Resource Manager mall för säkerhets kopiering av Azure-filresursen (AFS)](#azure-resource-manager-template-for-afs-backup)
  - [Stegvisa säkerhets kopieringar för SAP HANA databaser på virtuella Azure-datorer (för hands version)](#incremental-backups-for-sap-hana-databases-in-preview)
- September 2020
  - [Säkerhets kopierings Center (i förhands granskning)](#backup-center-in-preview)
  - [Säkerhets kopierings Azure Database for PostgreSQL (i förhands granskning)](#backup-azure-database-for-postgresql-in-preview)
  - [Säkerhets kopiering och återställning av selektiv disk](#selective-disk-backup-and-restore)
  - [Återställning mellan regioner för SQL Server och SAP HANA databaser på virtuella Azure-datorer (för hands version)](#cross-region-restore-for-sql-server-and-sap-hana-in-preview)
  - [Stöd för säkerhets kopiering av virtuella datorer med upp till 32 diskar (allmän tillgänglighet)](#support-for-backup-of-vms-with-up-to-32-disks)
  - [Förenklad konfigurations upplevelse för säkerhets kopiering för SQL i virtuella Azure-datorer](#simpler-backup-configuration-for-sql-in-azure-vms)
  - [Säkerhetskopiera SAP HANA i RHEL Azure Virtual Machines (för hands version)](#backup-sap-hana-in-rhel-azure-virtual-machines-in-preview)
  - [ZRS (Zone redundant Storage) för säkerhets kopierings data (i för hands version)](#zone-redundant-storage-zrs-for-backup-data-in-preview)
  - [Mjuk borttagning för SQL Server och SAP HANA arbets belastningar i virtuella Azure-datorer](#soft-delete-for-sql-server-and-sap-hana-workloads)

## <a name="azure-disk-backup-is-now-generally-available"></a>Azure disk Backup är nu allmänt tillgängligt

Azure Backup erbjuder livs cykel hantering av ögonblicks bilder till Azure Managed Disks genom att automatisera periodiska skapande av ögonblicks bilder och behålla dessa för konfigurerade varaktigheter med hjälp av säkerhets kopierings principen.

Mer information finns i [Översikt över säkerhets kopiering av Azure-diskar](disk-backup-overview.md).

## <a name="backup-center-is-now-generally-available"></a>Backup Center är nu allmänt tillgängligt

Backup Center fören klar hanteringen av data skydd i skala genom att hjälpa dig att identifiera, styra, övervaka, hantera och optimera säkerhets kopierings hantering från en enda central konsol.

Mer information finns i [Översikt över säkerhets kopierings Center](backup-center-overview.md).

## <a name="archive-tier-support-for-azure-backup-in-preview"></a>Arkiv nivå stöd för Azure Backup (för hands version)

Med Azure Backup kan du nu minska kostnaden för långsiktiga säkerhets kopieringar med tillgänglighet för Arkiv nivå för virtuella Azure-datorer och SQL Server i Azure Virtual Machines.

Mer information finns i [Support för Arkiv nivå (för hands version)](archive-tier-support.md).

## <a name="backup-for-azure-blobs-in-preview"></a>Säkerhets kopiering för Azure-blobar (i för hands version)

Drift säkerhets kopiering för blobbar är en hanterad, lokal data skydds lösning som gör att du kan skydda dina block-blobar från olika data förlust scenarier som skador, BLOB-borttagningar och radering av lagrings kontot. Data lagras lokalt i själva käll lagrings kontot och kan återställas till en vald tidpunkt när det behövs. Det ger dig ett enkelt, säkert och kostnads effektivt sätt att skydda dina blobbar.

Den operativa säkerhets kopieringen för blobbar integreras med Backup Center, bland annat funktioner för säkerhets kopierings hantering, för att tillhandahålla ett fönster med glas som kan hjälpa dig att styra, övervaka, hantera och analysera säkerhets kopior i stor skala.

Mer information finns i [Översikt över drift säkerhets kopiering för Azure-blobbar (i för hands version)](blob-backup-overview.md).

## <a name="azure-disk-backup-in-preview"></a>Säkerhets kopiering av Azure-disk (i för hands version)

Azure disk Backup erbjuder en nyckel färdig lösning som tillhandahåller hantering av ögonblicks bilder för [azure Managed disks](../virtual-machines/managed-disks-overview.md) genom att automatisera periodiska skapande av ögonblicks bilder och behålla den för en konfigurerad varaktighet med hjälp av säkerhets kopierings principen. Du kan hantera disk ögonblicks bilderna med noll som infrastruktur kostnader och utan behov av anpassade skript eller hanterings kostnader. Detta är en krasch lösning för säkerhets kopiering som tar säkerhets kopiering av en hanterad disk vid en viss tidpunkt med hjälp av [stegvisa ögonblicks bilder](../virtual-machines/disks-incremental-snapshots.md) med stöd för flera säkerhets kopior per dag. Det är också en agent lös lösning som inte påverkar prestanda för produktions program. Den har stöd för säkerhets kopiering och återställning av både OS-och data diskar (inklusive delade diskar), oavsett om de är kopplade till en virtuell Azure-dator som körs eller inte.

Mer information finns i [säkerhets kopiering av Azure-diskar (i för hands version)](disk-backup-overview.md).

## <a name="encryption-at-rest-using-customer-managed-keys"></a>Kryptering i vila med Kundhanterade nycklar

Stöd för kryptering i vila med Kundhanterade nycklar är nu allmänt tillgängligt. Detta ger dig möjlighet att kryptera säkerhetskopierade data i dina Recovery Services-valv med dina egna nycklar som lagras i Azure Key Vaults. Krypterings nyckeln som används för kryptering av säkerhets kopior i Recovery Services valvet kan skilja sig från de som används för att kryptera källan. Data skyddas med en AES 256-baserad data krypterings nyckel (DEK), som i sin tur skyddas med hjälp av nycklar som lagras i Key Vault. Jämfört med kryptering med hjälp av plattforms hanterade nycklar (som är tillgängligt som standard) ger dig mer kontroll över dina nycklar och hjälper dig att bättre uppfylla dina krav på efterlevnad.

Mer information finns i [kryptering av säkerhets kopierings data med Kundhanterade nycklar](encryption-at-rest-with-cmk.md).

## <a name="azure-resource-manager-template-for-afs-backup"></a>Azure Resource Manager mall för AFS-säkerhetskopiering

Azure Backup har nu stöd för att konfigurera säkerhets kopiering för befintliga Azure-filresurser med en Azure Resource Manager ARM-mall (ARM). Mallen konfigurerar skydd för en befintlig Azure-filresurs genom att ange lämplig information för Recovery Services valvet och säkerhets kopierings principen. Du kan också skapa ett nytt Recovery Services valv och en säkerhets kopierings princip och registrera lagrings kontot som innehåller fil resursen i Recovery Services-valvet.

Mer information finns i [Azure Resource Manager mallar för Azure Backup](backup-rm-template-samples.md).

## <a name="incremental-backups-for-sap-hana-databases-in-preview"></a>Stegvisa säkerhets kopieringar för SAP HANA databaser (för hands version)

Azure Backup stöder nu stegvisa säkerhets kopieringar för SAP HANA databaser som finns på virtuella Azure-datorer. På så sätt kan du snabbare och mer kostnads effektivt säkerhetskopiera dina SAP HANA data.

Mer information finns i [olika alternativ som är tillgängliga när du skapar en säkerhets kopierings princip](sap-hana-faq-backup-azure-vm.md#policy) och [hur du skapar en säkerhets kopierings princip för SAP HANA databaser](tutorial-backup-sap-hana-db.md#creating-a-backup-policy).

## <a name="backup-center-in-preview"></a>Säkerhets kopierings Center (i förhands granskning)

Azure Backup har aktiverat en ny inbyggd hanterings funktion för att hantera hela reserv fastigheten från en central konsol. Backup Center ger dig möjlighet att övervaka, hantera och optimera data skydd i stor skala på ett enhetligt sätt som överensstämmer med Azures inbyggda hanterings upplevelser.

Med Backup Center får du en sammanställd vy över dina lager över prenumerationer, platser, resurs grupper, valv och till och med-klienter med Azure Lighthouse. Backup Center är också ett åtgärds Center där du kan utlösa säkerhets kopierings aktiviteter, till exempel konfigurera säkerhets kopiering, återställning, skapande av principer eller valv, allt från en enda plats. Dessutom kan du, med sömlös integrering till Azure Policy, nu styra din miljö och spåra kompatibiliteten från ett säkerhets kopierings perspektiv. Inbyggda Azure-principer som är särskilt utformade för Azure Backup också att du kan konfigurera säkerhets kopior i stor skala.

Mer information finns i [Översikt över säkerhets kopierings Center](backup-center-overview.md).

## <a name="backup-azure-database-for-postgresql-in-preview"></a>Säkerhets kopierings Azure Database for PostgreSQL (i förhands granskning)

Azure Backup och Azure Database-tjänster har tillsammans skapat en lösning för säkerhets kopiering i företags klass för Azure PostgreSQL (nu i för hands version). Nu kan du uppfylla dina krav på data skydd och efterlevnad med en kundkontrollerad säkerhets kopierings princip som möjliggör kvarhållning av säkerhets kopior i upp till 10 år. Med detta har du detaljerad kontroll över hur du hanterar säkerhets kopierings-och återställnings åtgärderna på den enskilda databas nivån. På samma sätt kan du återställa över PostgreSQL-versioner eller till Blob Storage utan problem.

Mer information finns i [Azure Database for PostgreSQL säkerhets kopiering](backup-azure-database-postgresql.md).

## <a name="selective-disk-backup-and-restore"></a>Säkerhets kopiering och återställning av selektiv disk

Azure Backup stöder säkerhets kopiering av alla diskar (operativ system och data) i en virtuell dator tillsammans med säkerhets kopierings lösningen för virtuella datorer. Nu kan du använda säkerhets kopierings-och återställnings funktionen selektiva diskar för att säkerhetskopiera en delmängd av data diskarna i en virtuell dator. Detta ger en effektiv och kostnads effektiv lösning för dina säkerhets kopierings-och återställnings behov. Varje återställnings punkt innehåller bara de diskar som ingår i säkerhets kopierings åtgärden.

Mer information finns i [selektiv säkerhets kopiering av diskar och återställning för virtuella Azure-datorer](selective-disk-backup-restore.md).

## <a name="cross-region-restore-for-sql-server-and-sap-hana-in-preview"></a>Återställning mellan regioner för SQL Server och SAP HANA (i för hands version)

Med introduktionen av återställning mellan regioner kan du nu initiera återställningar i en sekundär region på för att undvika problem med verkliga stillestånd i en primär region för din miljö. Detta gör att den sekundära regionen återställer helt kund kontrollerad. Azure Backup använder säkerhetskopierade data som replikeras till den sekundära regionen för sådana återställningar.

Förutom stöd för återställning mellan regioner för virtuella Azure-datorer har funktionen nu utökats för att återställa SQL och SAP HANA databaser på virtuella Azure-datorer.

Mer information finns i [återställning mellan regioner för SQL-databaser](restore-sql-database-azure-vm.md#cross-region-restore) och [återställning mellan regioner för SAP HANA databaser](sap-hana-db-restore.md#cross-region-restore).

## <a name="support-for-backup-of-vms-with-up-to-32-disks"></a>Stöd för säkerhets kopiering av virtuella datorer med upp till 32 diskar

Fram till nu har Azure Backup stöd för 16 hanterade diskar per virtuell dator. Azure Backup har nu stöd för säkerhets kopiering av upp till 32 hanterade diskar per virtuell dator.

Mer information finns i [support mat ris för VM-lagring](backup-support-matrix-iaas.md#vm-storage-support).

## <a name="simpler-backup-configuration-for-sql-in-azure-vms"></a>Enklare säkerhets kopierings konfiguration för SQL i virtuella Azure-datorer

Det är nu ännu enklare att konfigurera säkerhets kopieringar för dina SQL Server i virtuella Azure-datorer med inbyggd säkerhets kopierings konfiguration i den virtuella dator rutan i Azure Portal. Med bara några få steg kan du aktivera säkerhets kopiering av SQL Server för att skydda alla befintliga databaser samt de som läggs till i framtiden.

Mer information finns i [säkerhetskopiera en SQL Server från fönstret VM](backup-sql-server-vm-from-vm-pane.md).

## <a name="backup-sap-hana-in-rhel-azure-virtual-machines-in-preview"></a>Säkerhetskopiera SAP HANA i RHEL Azure Virtual Machines (för hands version)

Azure Backup är en inbyggd lösning för säkerhets kopiering för Azure och är BackInt certifierad av SAP. Azure Backup har nu lagt till stöd för Red Hat Enterprise Linux (RHEL), ett av de vanligaste Linux-operativsystemen som kör SAP HANA.

Mer information finns i [support mat ris för säkerhets kopiering av SAP HANA databas](sap-hana-backup-support-matrix.md#scenario-support).

## <a name="zone-redundant-storage-zrs-for-backup-data-in-preview"></a>ZRS (Zone redundant Storage) för säkerhets kopierings data (i för hands version)

Azure Storage ger en bra balans mellan hög prestanda, hög tillgänglighet och hög data återhämtning med de varierande alternativen för redundans. Med Azure Backup kan du utöka de här fördelarna med att säkerhetskopiera data även med alternativ för att lagra säkerhets kopior i lokalt redundant lagring (LRS) och Geo-redundant lagring (GRS). Nu finns det ytterligare hållbarhets alternativ med stöd för ZRS (Zone redundant Storage).

Mer information finns i [set Storage redundans for the Recovery Services Vault](backup-create-rs-vault.md#set-storage-redundancy).

## <a name="soft-delete-for-sql-server-and-sap-hana-workloads"></a>Mjuk borttagning för SQL Server och SAP HANA arbets belastningar

Problem med säkerhets problem, t. ex. skadlig kod, utpressnings program vara och intrång, ökar. De här säkerhets problemen kan vara dyra, i termer av både Money och data. För att skydda mot sådana angrepp tillhandahåller Azure Backup säkerhetsfunktioner som hjälper dig att skydda säkerhets kopierings data även efter borttagning.

En sådan funktion är mjuk borttagning. Med mjuk borttagning, även om en obehörig aktör tar bort en säkerhets kopia (eller säkerhetskopierade data tas bort av misstag), bevaras säkerhets kopierings data i ytterligare 14 dagar, vilket gör att det inte går att återställa säkerhets kopierings objekt utan data förlust. De ytterligare 14 dagarna för kvarhållning av säkerhets kopierings data i läget "mjuk borttagning" kostar inga kostnader för dig.

Förutom stöd för mjuk borttagning för virtuella Azure-datorer, är SQL Server och SAP HANA arbets belastningar i virtuella Azure-datorer också skyddade med mjuk borttagning.

Mer information finns i [mjuk borttagning för SQL Server i Azure VM och SAP HANA i Azure VM-arbetsbelastningar](soft-delete-sql-saphana-in-azure-vm.md).

## <a name="next-steps"></a>Nästa steg

- [Azure Backup vägledning och bästa praxis](guidance-best-practices.md)