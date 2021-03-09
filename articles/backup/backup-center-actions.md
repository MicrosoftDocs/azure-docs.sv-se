---
title: Utföra åtgärder med Backup Center
description: Den här artikeln förklarar hur du utför åtgärder med Backup Center
ms.topic: conceptual
ms.date: 09/07/2020
ms.openlocfilehash: 8c21475e5a52cdce7e38bbeb9d00df3c3ac3a752
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/09/2021
ms.locfileid: "102506498"
---
# <a name="perform-actions-using-backup-center"></a>Utföra åtgärder med Backup Center

Med Backup Center kan du utföra säkerhets kopiering av säkerhetsrelaterade åtgärder från ett centralt gränssnitt utan att behöva navigera till ett enskilt valv. Vissa åtgärder som du kan utföra från säkerhets kopierings Center är:

* Konfigurera säkerhets kopiering för dina data källor
* Återställa en säkerhets kopierings instans
* Skapa ett nytt valv
* Skapa en ny säkerhets kopierings princip
* Utlös en säkerhets kopiering på begäran för en säkerhets kopierings instans
* Stoppa säkerhets kopiering för en säkerhets kopierings instans

## <a name="supported-scenarios"></a>Scenarier som stöds

* Backup Center stöds för närvarande för säkerhets kopiering av virtuella Azure-datorer, SQL i Azure VM backup, SAP HANA i Azure VM backup, Azure Files säkerhets kopiering och Azure Database for PostgreSQL säkerhets kopiering av Server.
* Se [support mat ris](backup-center-support-matrix.md) för en detaljerad lista över scenarier som stöds och som inte stöds.

## <a name="configure-backup"></a>Konfigurera säkerhetskopiering

Om du säkerhetskopierar virtuella Azure-datorer, SQL på virtuella Azure-datorer SAP HANA i virtuella Azure-datorer eller Azure Files, bör du använda ett Recovery Services-valv. Om du säkerhetskopierar Azure-databaser för PostgreSQL-servern bör du använda ett säkerhets kopierings valv. 

Följ de anvisningar som beskrivs nedan, beroende på vilken typ av data källa du vill säkerhetskopiera.

### <a name="configure-backup-to-a-recovery-services-vault"></a>Konfigurera säkerhets kopiering till ett Recovery Services-valv

1. Gå till säkerhets kopierings Center och välj **+ säkerhetskopiera** överst på fliken **Översikt** .

    ![Översikt över backup Center](./media/backup-center-actions/backup-center-overview-configure-backup.png)

2. Välj den typ av data källa som du vill säkerhetskopiera.

    ![Välj DataSource för att konfigurera VM-säkerhetskopiering](./media/backup-center-actions/backup-select-datasource-vm.png)

3. Välj ett Recovery Services valv och välj **Fortsätt**. Detta leder dig till den konfigurations upplevelse för säkerhets kopiering som är identisk med den som kan nås från ett Recovery Services-valv. [Läs mer om hur du konfigurerar säkerhets kopiering för virtuella Azure-datorer med ett Recovery Services-valv](tutorial-backup-vm-at-scale.md).

### <a name="configure-backup-to-a-backup-vault"></a>Konfigurera säkerhets kopiering till ett säkerhets kopierings valv

1. Gå till säkerhets kopierings Center och välj **+ säkerhetskopiera** överst på fliken **Översikt** .
2. Välj den typ av data källa som du vill säkerhetskopiera (Azure Database for PostgreSQL server i det här fallet).

    ![Välj DataSource för att konfigurera Azure Database for PostgreSQL Server säkerhets kopiering](./media/backup-center-actions/backup-select-datasource-type-postgresql.png)

3. Välj **Fortsätt**. Detta leder dig till den konfigurations upplevelse för säkerhets kopiering som är identisk med den som kan nås från ett säkerhets kopierings valv. [Läs mer om hur du konfigurerar säkerhets kopiering för Azure Database for postgresql server med ett säkerhets kopierings valv](backup-azure-database-postgresql.md#configure-backup-on-azure-postgresql-databases).

## <a name="restore-a-backup-instance"></a>Återställa en säkerhets kopierings instans

Följ de anvisningar som beskrivs nedan, beroende på vilken typ av data källa du vill återställa.

### <a name="if-youre-restoring-from-a-recovery-services-vault"></a>Om du återställer från ett Recovery Services-valv

1. Gå till säkerhets kopierings Center och välj **Återställ** överst på fliken **Översikt** .

    ![Översikt över backup Center för återställning av virtuell dator](./media/backup-center-actions/backup-center-overview-restore.png)

2. Välj vilken typ av data källa du vill återställa.

    ![Välj DataSource för återställning av virtuell dator](./media/backup-center-actions/restore-select-datasource-vm.png)

3. Välj en säkerhets kopierings instans och välj **Fortsätt**. Detta leder dig till den återställnings inställnings upplevelse som är identisk med den som kan nås från ett Recovery Services-valv. [Lär dig mer om hur du återställer en virtuell Azure-dator med ett Recovery Services-valv](backup-azure-arm-restore-vms.md#before-you-start).

### <a name="if-youre-restoring-from-a-backup-vault"></a>Om du återställer från ett säkerhets kopierings valv

1. Gå till säkerhets kopierings Center och välj **Återställ** överst på fliken **Översikt** .
2. Välj den typ av data källa som du vill återställa (Azure Database for PostgreSQL server i det här fallet).

    ![Välj data källa för Azure Database for PostgreSQL återställning av Server](./media/backup-center-actions/restore-select-datasource-postgresql.png)

3. Välj en säkerhets kopierings instans och välj **Fortsätt**. Detta leder dig till den återställnings inställnings upplevelse som är identisk med den som kan nås från ett Recovery Services-valv. [Läs mer om hur du återställer en Azure Database for postgresql-server med ett säkerhets kopierings valv](backup-azure-database-postgresql.md#restore).

## <a name="create-a-new-vault"></a>Skapa ett nytt valv

Du kan skapa ett nytt valv genom att gå till säkerhets kopierings Center och välja **+ valv** överst på fliken **Översikt** .

![Skapa valv](./media/backup-center-actions/backup-center-create-vault.png)

* [Läs mer om hur du skapar ett Recovery Services-valv](backup-create-rs-vault.md)
* [Lär dig mer om att skapa ett säkerhets kopierings valv](backup-vault-overview.md)

## <a name="create-a-new-backup-policy"></a>Skapa en ny säkerhets kopierings princip

Följ de anvisningar som beskrivs nedan, beroende på vilken typ av data källa du vill säkerhetskopiera.

### <a name="if-youre-backing-up-to-a-recovery-services-vault"></a>Om du säkerhetskopierar till ett Recovery Services-valv

1. Gå till säkerhets kopierings Center och välj **+ princip** överst på fliken **Översikt** .

    ![Översikt över backup Center för säkerhets kopierings policy](./media/backup-center-actions/backup-center-overview-policy.png)

2. Välj den typ av data källa som du vill säkerhetskopiera.

    ![Välj data källa för princip för VM-säkerhetskopiering](./media/backup-center-actions/policy-select-datasource-vm.png)

3. Välj ett Recovery Services-valv och välj **Fortsätt**. Detta leder dig till den princip för skapande som är identisk med den som kan nås från ett Recovery Services-valv. [Lär dig mer om hur du skapar en ny säkerhets kopierings princip för en virtuell Azure-dator med ett Recovery Services-valv](backup-azure-arm-vms-prepare.md#create-a-custom-policy).

### <a name="if-youre-backing-up-to-a-backup-vault"></a>Om du säkerhetskopierar till ett säkerhets kopierings valv

1. Gå till säkerhets kopierings Center och välj **+ princip** överst på fliken **Översikt** .
2. Välj den typ av data källa som du vill säkerhetskopiera (Azure Database for PostgreSQL server i det här fallet).

    ![Välj data källa för princip för Azure Database for PostgreSQL Server säkerhets kopiering](./media/backup-center-actions/policy-select-datasource-postgresql.png)

3. Välj **Fortsätt**. Detta leder dig till den princip för skapande av principer som är identisk med den som kan nås från ett säkerhets kopierings valv. [Lär dig mer om hur du skapar en ny säkerhets kopierings princip med ett säkerhets kopierings valv](backup-azure-database-postgresql.md#create-backup-policy).

## <a name="execute-an-on-demand-backup-for-a-backup-instance"></a>Köra en säkerhets kopiering på begäran för en säkerhets kopierings instans

Med Backup Center kan du söka efter säkerhets kopierings instanser i reserven och köra säkerhets kopierings åtgärder på begäran.

Om du vill utlösa en säkerhets kopiering på begäran går du till backup Center och väljer meny alternativet **säkerhets kopierings instanser** . Om du väljer det här alternativet kan du Visa information om alla säkerhets kopierings instanser som du har åtkomst till. Du kan söka efter den säkerhets kopierings instans du vill säkerhetskopiera. Högerklicka på ett objekt i rutnätet för att öppna en lista över tillgängliga åtgärder. Välj alternativet **Säkerhetskopiera nu** för att köra en säkerhets kopiering på begäran.

![Säkerhets kopiering på begäran](./media/backup-center-actions/backup-center-on-demand-backup.png)

[Lär dig mer om att utföra säkerhets kopiering på begäran för Azure Virtual Machines](backup-azure-manage-vms.md#run-an-on-demand-backup).

[Lär dig mer om att utföra säkerhets kopiering på begäran för Azure Database for postgresql server](backup-azure-database-postgresql.md#on-demand-backup).

## <a name="stop-backup-for-a-backup-instance"></a>Stoppa säkerhets kopiering för en säkerhets kopierings instans

Det finns scenarier när du kanske vill stoppa säkerhets kopieringen för en säkerhets kopierings instans, till exempel när den underliggande resurs som säkerhets kopie ras inte finns längre.

Om du vill utlösa en säkerhets kopiering på begäran går du till backup Center och väljer meny alternativet **säkerhets kopierings instanser** . Välj det här alternativet om du vill visa information om alla säkerhets kopierings instanser som du har åtkomst till. Du kan söka efter den säkerhets kopierings instans du vill säkerhetskopiera. Högerklicka på ett objekt i rutnätet för att öppna en lista över tillgängliga åtgärder. Välj alternativet **Avbryt säkerhets kopiering** för att stoppa säkerhets kopieringen av säkerhets kopierings instansen.

![Sluta skydda](./media/backup-center-actions/backup-center-stop-protection.png)

[Läs mer om att stoppa säkerhets kopiering för Azure Virtual Machines](backup-azure-manage-vms.md#stop-protecting-a-vm).

[Läs mer om att stoppa säkerhets kopiering för Azure Database for PostgreSQL Server](backup-azure-database-postgresql.md#stop-protection)

## <a name="next-steps"></a>Nästa steg

* [Övervaka och hantera säkerhets kopior](backup-center-monitor-operate.md)
* [Styr din reserv egendom](backup-center-govern-environment.md)
* [Få insikter om dina säkerhets kopior](backup-center-obtain-insights.md)
