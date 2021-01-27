---
title: Skapa och konfigurera Recovery Services-valv
description: I den här artikeln får du lära dig hur du skapar och konfigurerar Recovery Services valv som lagrar säkerhets kopior och återställnings punkter. Lär dig hur du använder återställning mellan regioner för att återställa i en sekundär region.
ms.topic: conceptual
ms.date: 05/30/2019
ms.custom: references_regions
ms.openlocfilehash: 51d095ab68bc9277cb610bd226039ffbde8d7ddf
ms.sourcegitcommit: 436518116963bd7e81e0217e246c80a9808dc88c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/27/2021
ms.locfileid: "98915734"
---
# <a name="create-and-configure-a-recovery-services-vault"></a>Skapa och konfigurera ett Recovery Services valv

[!INCLUDE [How to create a Recovery Services vault](../../includes/backup-create-rs-vault.md)]

## <a name="set-storage-redundancy"></a>Ange redundans för lagring

Azure Backup hanterar automatiskt lagring för valvet. Du måste ange hur lagringen ska replikeras.

> [!NOTE]
> Att ändra **typ av lagrings replikering** (lokalt redundant/Geo-redundant) för ett Recovery Services valv måste göras innan du konfigurerar säkerhets kopieringar i valvet. När du har konfigurerat säkerhets kopiering inaktive ras alternativet för att ändra.
>
>- Om du ännu inte har konfigurerat säkerhets kopian [följer du de här stegen](#set-storage-redundancy) för att granska och ändra inställningarna.
>- Om du redan har konfigurerat säkerhets kopian och måste gå från GRS till LRS går du [igenom dessa lösningar](#how-to-change-from-grs-to-lrs-after-configuring-backup).

1. Välj det nya valvet i fönstret **Recovery Services valv** . Under avsnittet **Inställningar** väljer du  **Egenskaper**.
1. I **Egenskaper**, under **säkerhets kopierings konfiguration**, väljer du **Uppdatera**.

1. Välj typ av lagrings replikering och välj **Spara**.

     ![Ange lagringskonfigurationen för det nya valvet](./media/backup-create-rs-vault/recovery-services-vault-backup-configuration.png)

   - Vi rekommenderar att om du använder Azure som primär slut punkt för lagring av säkerhets kopior fortsätter du att använda standardvärdet **Geo-redundant** .
   - Om du inte använder Azure som primär slutpunkt för lagring av säkerhetskopior väljer du **Lokalt redundant**, vilket minskar kostnaderna för Azure-lagring.
   - Lär dig mer om [geo](../storage/common/storage-redundancy.md#geo-redundant-storage) och [lokal](../storage/common/storage-redundancy.md#locally-redundant-storage) redundans.
   - Om du behöver data tillgänglighet utan stillestånds tid i en region, garantera data placering, väljer du [zon-redundant lagring](../storage/common/storage-redundancy.md#zone-redundant-storage).

>[!NOTE]
>Inställningarna för lagrings replikering för valvet är inte relevanta för säkerhets kopiering av Azure-filresurs eftersom den aktuella lösningen är ögonblicks bild och det finns inga data som överförs till valvet. Ögonblicks bilder lagras i samma lagrings konto som den säkerhetskopierade fil resursen.

## <a name="set-cross-region-restore"></a>Ange återställning av kors region

Med alternativet återställnings alternativ **över regions återställning (CRR)** kan du återställa data i en sekundär, [kopplad Azure-kopplad region](../best-practices-availability-paired-regions.md).

Den har stöd för följande data Källor:

- Virtuella Azure-datorer (allmän tillgänglighet)
- SQL-databaser som finns på virtuella Azure-datorer (för hands version)
- SAP HANA databaser som finns på virtuella Azure-datorer (för hands version)

Genom att använda återställning mellan regioner kan du:

- genomför övningar när det finns ett krav på granskning eller efterlevnad
- återställa data om det finns en katastrof i den primära regionen

När du återställer en virtuell dator kan du återställa den virtuella datorn eller disken. Om du återställer från SQL/SAP HANA-databaser som finns på virtuella Azure-datorer kan du återställa databaser eller deras filer.

Om du vill välja den här funktionen väljer du **Aktivera återställning av kors region** från **konfigurations fönstret för säkerhets kopiering** .

Eftersom den här processen är på lagrings nivå finns det [pris effekter](https://azure.microsoft.com/pricing/details/backup/).

>[!NOTE]
>Innan du börjar:
>
>- Granska [support matrisen](backup-support-matrix.md#cross-region-restore) för en lista över hanterade typer och regioner som stöds.
>- Funktionen för återställning av kors region (CRR) för virtuella Azure-datorer är nu allmänt tillgänglig i alla offentliga Azure-regioner.
>- Återställning mellan regioner för SQL-och SAP HANA-databaser är i för hands version i alla offentliga Azure-regioner.
>- CRR är ett alternativ för att välja en valv nivå för alla GRS-valv (inaktiverat som standard).
>- Efter väljer kan det ta upp till 48 timmar innan säkerhets kopierings objekten är tillgängliga i sekundära regioner.
>- För närvarande stöds endast CRR för virtuella Azure-datorer i Azure Resource Manager virtuella Azure-datorer. Klassiska virtuella Azure-datorer stöds inte.  När ytterligare hanterings typer stöder CRR registreras de **automatiskt** .
>- Återställning mellan regioner **kan för närvarande inte återställas** till GRS eller LRS när skyddet initieras för första gången.

### <a name="configure-cross-region-restore"></a>Konfigurera återställning mellan regioner

Ett valv som skapats med GRS-redundans omfattar alternativet att konfigurera funktionen för återställning av kors region. Varje GRS-valv kommer att ha en banderoll som länkar till dokumentationen. Om du vill konfigurera CRR för valvet går du till konfigurations fönstret för säkerhets kopiering, som innehåller alternativet att aktivera den här funktionen.

 ![Banderoll för säkerhets kopierings konfiguration](./media/backup-azure-arm-restore-vms/banner.png)

1. Från portalen går du till Recovery Services valv > **Egenskaper** (under **Inställningar**).
1. Under **säkerhets kopierings konfiguration** väljer du **Uppdatera**.
1. Välj **Aktivera återställning mellan regioner i det här valvet** för att aktivera funktionen.

   ![Aktivera återställning mellan regioner](./media/backup-azure-arm-restore-vms/backup-configuration.png)

I de här artiklarna finns mer information om säkerhets kopiering och återställning med CRR:

- [Återställning mellan regioner för virtuella Azure-datorer](backup-azure-arm-restore-vms.md#cross-region-restore)
- [Återställning mellan regioner för SQL-databaser](restore-sql-database-azure-vm.md#cross-region-restore)
- [Återställning mellan regioner för SAP HANA databaser](sap-hana-db-restore.md#cross-region-restore)

## <a name="set-encryption-settings"></a>Ange krypterings inställningar

Som standard krypteras data i Recovery Services-valvet med hjälp av plattforms hanterade nycklar. Inga uttryckliga åtgärder krävs från slutpunkten för att aktivera den här krypteringen, och den gäller för alla arbets belastningar som säkerhets kopie ras till Recovery Services-valvet.  Du kan välja att ta med din egen nyckel för att kryptera säkerhetskopierade data i det här valvet. Detta kallas för Kundhanterade nycklar. Om du vill kryptera säkerhetskopierade data med din egen nyckel måste krypterings nyckeln anges innan något objekt skyddas för det här valvet. När du har aktiverat kryptering med din nyckel går det inte att ångra.

### <a name="configuring-a-vault-to-encrypt-using-customer-managed-keys"></a>Konfigurera ett valv för kryptering med Kundhanterade nycklar

Om du vill konfigurera valvet att kryptera med Kundhanterade nycklar, måste de här stegen följas i följande ordning:

1. Aktivera hanterad identitet för Recovery Services valvet

1. Tilldela behörighet till valvet för att få åtkomst till krypterings nyckeln i Azure Key Vault

1. Aktivera mjuk borttagning och tömning av skydd på Azure Key Vault

1. Tilldela krypterings nyckeln till Recovery Services-valvet

Instruktioner för var och en av de här stegen finns [i den här artikeln](encryption-at-rest-with-cmk.md#configuring-a-vault-to-encrypt-using-customer-managed-keys).

## <a name="modifying-default-settings"></a>Ändra standardinställningar

Vi rekommenderar starkt att du granskar standardinställningarna i **Lagringsreplikeringstyp** och **Säkerhetsinställningar** innan du konfigurerar säkerhetskopior i valvet.

- **Typen av lagringsprovider** är som standard inställd på **Geo-redundant** (GRS). När du har konfigurerat säkerhets kopieringen inaktive ras alternativet att ändra.
  - Om du ännu inte har konfigurerat säkerhets kopian [följer du de här stegen](#set-storage-redundancy) för att granska och ändra inställningarna.
  - Om du redan har konfigurerat säkerhets kopian och måste gå från GRS till LRS går du [igenom dessa lösningar](#how-to-change-from-grs-to-lrs-after-configuring-backup).

- **Mjuk borttagning** är **aktiverat** som standard på nyligen skapade valv för att skydda säkerhets kopierings data från oavsiktliga eller skadliga borttagningar. [Följ dessa steg](./backup-azure-security-feature-cloud.md#enabling-and-disabling-soft-delete) om du vill granska och ändra inställningarna.

### <a name="how-to-change-from-grs-to-lrs-after-configuring-backup"></a>Ändra från GRS till LRS efter att du har konfigurerat säkerhets kopiering

Innan du bestämmer dig för att flytta från GRS till lokalt redundant lagring (LRS) granskar du kompromisserna mellan lägre kostnader och högre data hållbarhet som passar ditt scenario. Om du måste gå från GRS till LRS kan du välja mellan två alternativ. De är beroende av dina verksamhets krav för att spara säkerhets kopierings data:

- [Behöver inte bevara tidigare säkerhetskopierade data](#dont-need-to-preserve-previous-backed-up-data)
- [Måste bevara tidigare säkerhetskopierade data](#must-preserve-previous-backed-up-data)

#### <a name="dont-need-to-preserve-previous-backed-up-data"></a>Behöver inte bevara tidigare säkerhetskopierade data

För att skydda arbets belastningar i ett nytt LRS-valv måste det aktuella skyddet och data tas bort i GRS-valvet och säkerhets kopior som kon figurer ATS igen.

>[!WARNING]
>Följande åtgärd är förstörande och kan inte återställas. Alla säkerhets kopierings data och säkerhets kopierings objekt som är associerade med den skyddade servern tas bort permanent. Tänk dig för innan du fortsätter.

Stoppa och ta bort aktuellt skydd i GRS-valvet:

1. Inaktivera mjuk borttagning i egenskaperna för GRS-valvet. Följ [dessa steg](backup-azure-security-feature-cloud.md#disabling-soft-delete-using-azure-portal) om du vill inaktivera mjuk borttagning.

1. Stoppa skyddet och ta bort säkerhets kopior från det befintliga GRS-valvet. Välj **säkerhets kopierings objekt** på instrument panelen för valv-menyn. Objekt som anges här och som måste flyttas till LRS-valvet måste tas bort tillsammans med sina säkerhets kopierings data. Se [ta bort skyddade objekt i molnet](backup-azure-delete-vault.md#delete-protected-items-in-the-cloud) och [ta bort skyddade objekt lokalt](backup-azure-delete-vault.md#delete-protected-items-on-premises).

1. Om du planerar att flytta AFS (Azure-filresurser), SQL-servrar eller SAP HANA-servrar måste du också avregistrera dem. På instrument panelen för valv väljer du **säkerhets kopierings infrastruktur**. Se hur du [avregistrerar SQL-servern](manage-monitor-sql-database-backup.md#unregister-a-sql-server-instance), [avregistrerar ett lagrings konto som är associerat med Azure-filresurser](manage-afs-backup.md#unregister-a-storage-account)och [avregistrerar en SAP HANA instans](sap-hana-db-manage.md#unregister-an-sap-hana-instance).

1. När de har tagits bort från GRS-valvet fortsätter du att konfigurera säkerhets kopieringarna för din arbets belastning i det nya LRS-valvet.

#### <a name="must-preserve-previous-backed-up-data"></a>Måste bevara tidigare säkerhetskopierade data

Om du behöver behålla nuvarande skyddade data i GRS-valvet och fortsätta skydda i ett nytt LRS-valv, finns det begränsade alternativ för vissa arbets belastningar:

- För MARS kan du [stoppa skyddet med Behåll data](backup-azure-manage-mars.md#stop-protecting-files-and-folder-backup) och registrera agenten i det nya LRS-valvet.

  - Azure Backup tjänsten fortsätter att behålla alla befintliga återställnings punkter i GRS-valvet.
  - Du måste betala för att behålla återställnings punkterna i GRS-valvet.
  - Du kommer bara att kunna återställa säkerhetskopierade data för återställnings punkter som inte har gått ut i GRS-valvet.
  - En ny inledande replik av data måste skapas i LRS-valvet.

- För en virtuell Azure-dator kan du [stoppa skyddet med Behåll data](backup-azure-manage-vms.md#stop-protecting-a-vm) för den virtuella datorn i GRS-valvet, flytta den virtuella datorn till en annan resurs grupp och sedan skydda den virtuella datorn i LRS-valvet. Se [vägledning och begränsningar](../azure-resource-manager/management/move-limitations/virtual-machines-move-limitations.md) för att flytta en virtuell dator till en annan resurs grupp.

  En virtuell dator kan endast skyddas i ett valv i taget. Den virtuella datorn i den nya resurs gruppen kan dock skyddas i LRS-valvet eftersom den betraktas som en annan virtuell dator.

  - Azure Backup tjänsten behåller de återställnings punkter som har säkerhetskopierats i GRS-valvet.
  - Du måste betala för att behålla återställnings punkterna i GRS-valvet (se [Azure Backup priser](azure-backup-pricing.md) för mer information).
  - Du kommer att kunna återställa den virtuella datorn, om det behövs, från GRS-valvet.
  - Den första säkerhets kopieringen i LRS-valvet för den virtuella datorn i den nya resursen är en inledande replik.

## <a name="next-steps"></a>Nästa steg

[Läs mer om](backup-azure-recovery-services-vault-overview.md) Recovery Services valv.
[Läs mer om](backup-azure-delete-vault.md) Ta bort Recovery Services valv.
