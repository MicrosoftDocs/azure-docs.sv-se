---
title: Hantera säkerhetskopierade SAP HANA databaser på virtuella Azure-datorer
description: I den här artikeln lär du dig vanliga uppgifter för att hantera och övervaka SAP HANA databaser som körs på virtuella Azure-datorer.
ms.topic: conceptual
ms.date: 11/12/2019
ms.openlocfilehash: 54d3341a83873ad3cc50815f04a0b252bb44438e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "101703774"
---
# <a name="manage-and-monitor-backed-up-sap-hana-databases"></a>Hantera och övervaka säkerhetskopierade SAP HANA-databaser

I den här artikeln beskrivs vanliga uppgifter för att hantera och övervaka SAP HANA databaser som körs på en virtuell Azure-dator (VM) och som säkerhets kopie ras till en Azure Backup Recovery Services valvet av [Azure backups](./backup-overview.md) tjänsten. Du lär dig hur du övervakar jobb och aviseringar, utlöser en säkerhets kopiering på begäran, redigerar principer, stoppar och återupptar databas skyddet och avregistrerar en virtuell dator från säkerhets kopior.

Om du inte har konfigurerat säkerhets kopieringar ännu för dina SAP HANA-databaser, se [säkerhetskopiera SAP HANA-databaser på virtuella Azure-datorer](./backup-azure-sap-hana-database.md).

## <a name="monitor-manual-backup-jobs-in-the-portal"></a>Övervaka manuella säkerhets kopierings jobb i portalen

Azure Backup visar alla manuellt utlösta jobb i avsnittet **säkerhets kopierings jobb** på Azure Portal.

![Avsnittet säkerhets kopierings jobb](./media/sap-hana-db-manage/backup-jobs.png)

De jobb som visas i den här portalen är identifiering och registrering av databaser samt säkerhets kopierings-och återställnings åtgärder. Schemalagda jobb, inklusive logg säkerhets kopior visas inte i det här avsnittet. Manuellt utlösta säkerhets kopieringar från SAP HANA inbyggda klienter (Studio/cockpit/DBA cockpit) visas inte här.

![Lista över säkerhets kopierings jobb](./media/sap-hana-db-manage/backup-jobs-list.png)

Om du vill veta mer om övervakning går du till [övervakning i Azure Portal](./backup-azure-monitoring-built-in-monitor.md) och [övervakning med hjälp av Azure Monitor](./backup-azure-monitoring-use-azuremonitor.md).

## <a name="view-backup-alerts"></a>Visa säkerhetskopieringsaviseringar

Aviseringar är ett enkelt sätt att övervaka säkerhets kopieringar av SAP HANA databaser. Aviseringar hjälper dig att fokusera på de händelser du bryr dig om utan att förlora antalet händelser som en säkerhets kopia genererar. Med Azure Backup kan du ange aviseringar och de kan övervakas på följande sätt:

* Logga in på [Azure-portalen](https://portal.azure.com/).
* På instrument panelen för valv väljer du **säkerhets kopierings aviseringar**.

  ![Säkerhets kopierings aviseringar på valv instrument panel](./media/sap-hana-db-manage/backup-alerts-dashboard.png)

* Du kommer att kunna se aviseringarna:

  ![Lista över säkerhets kopierings aviseringar](./media/sap-hana-db-manage/backup-alerts-list.png)

* Välj aviseringarna om du vill se mer information:

  ![Aviseringsinformation](./media/sap-hana-db-manage/alert-details.png)

I dag kan Azure Backup skicka aviseringar via e-post. Dessa aviseringar är:

* Utlöses för alla säkerhets kopierings problem.
* Konsol IDE rad på databas nivå efter felkod.
* Skickas bara för första säkerhets kopierings försöket i databasen.

Om du vill veta mer om övervakning går du till [övervakning i Azure Portal](./backup-azure-monitoring-built-in-monitor.md) och [övervakning med hjälp av Azure Monitor](./backup-azure-monitoring-use-azuremonitor.md).

## <a name="management-operations"></a>Hanteringsåtgärder

Azure Backup gör hanteringen av en säkerhetskopierad SAP HANA-databas lätt med en mängd hanterings åtgärder som stöds. Dessa åtgärder beskrivs i detalj i följande avsnitt.

### <a name="run-an-on-demand-backup"></a>Köra en säkerhetskopiering på begäran

Säkerhets kopieringar körs enligt princip schemat. Du kan köra en säkerhets kopiering på begäran på följande sätt:

1. I menyn valv väljer du **säkerhets kopierings objekt**.
2. I **säkerhets kopierings objekt** väljer du den virtuella dator som kör SAP HANA databasen och väljer sedan **Säkerhetskopiera nu**.
3. I **Säkerhetskopiera nu** väljer du vilken typ av säkerhets kopiering du vill utföra. Välj sedan **OK**. Den här säkerhets kopian kommer att behållas enligt principen som är kopplad till det här säkerhets kopierings objektet.
4. Övervaka Portal meddelanden. Du kan övervaka jobb förloppet i valv instrument panelen > **säkerhets kopierings jobb**  >  **pågår**. Det kan ta en stund att skapa den första säkerhets kopieringen, beroende på databasens storlek.

Som standard är kvarhållning av säkerhets kopior på begäran 45 dagar.

### <a name="hana-native-client-integration"></a>HANA inbyggd klient integrering

#### <a name="backup"></a>Backup

Säkerhets kopiering på begäran som utlöses från någon av de HANA-ursprungliga klienterna (till **Backint**) visas i listan säkerhets kopia på sidan **säkerhets kopierings objekt** .

![Senaste säkerhets kopierings körning](./media/sap-hana-db-manage/last-backups.png)

Du kan också [övervaka dessa säkerhets kopior](#monitor-manual-backup-jobs-in-the-portal) från sidan **säkerhets kopierings jobb** .

Dessa säkerhets kopior på begäran visas också i listan över återställnings punkter för återställning.

![Lista över återställnings punkter](./media/sap-hana-db-manage/list-restore-points.png)

#### <a name="restore"></a>Återställ

Återställningar som utlöses från HANA-ursprungliga klienter (med **Backint**) för att återställa till samma dator kan [övervakas](#monitor-manual-backup-jobs-in-the-portal) från sidan **säkerhets kopierings jobb** .

### <a name="run-sap-hana-native-client-backup-to-local-disk-on-a-database-with-azure-backup-enabled"></a>Kör SAP HANA intern klient säkerhets kopiering till lokal disk på en databas med Azure Backup aktiverat

Om du vill ta en lokal säkerhets kopia (med HANA Studio/cockpit) för en databas som säkerhets kopie ras med Azure Backup gör du följande:

1. Vänta tills alla säkerhets kopieringar eller loggar för databasen har slutförts. Kontrol lera statusen i SAP HANA Studio/cockpit.
2. för relevanta databaser
    1. Unset parametrar för backint. Det gör du genom att dubbelklicka på **systemdb**  >  **konfiguration**  >  **Välj databas**  >  **filter (logg)**.
        * enable_auto_log_backup: Nej
        * log_backup_using_backint: falskt
        * catalog_backup_using_backint: falskt
3. Ta en fullständig säkerhets kopiering på begäran av databasen
4. Ångra sedan stegen. För samma relevanta databas som nämns ovan,
    1. återaktivera parametrarna för backint
        1. catalog_backup_using_backint: sant
        1. log_backup_using_backint: sant
        1. enable_auto_log_backup: Ja

### <a name="manage-or-clean-up-the-hana-catalog-for-a-database-with-azure-backup-enabled"></a>Hantera eller rensa HANA-katalogen för en databas med Azure Backup aktive rad

Gör så här om du vill redigera eller rensa säkerhets kopie katalogen:

1. Vänta tills alla säkerhets kopieringar eller loggar för databasen har slutförts. Kontrol lera statusen i SAP HANA Studio/cockpit.
2. för relevanta databaser
    1. Unset parametrar för backint. Det gör du genom att dubbelklicka på **systemdb**  >  **konfiguration**  >  **Välj databas**  >  **filter (logg)**.
        * enable_auto_log_backup: Nej
        * log_backup_using_backint: falskt
        * catalog_backup_using_backint: falskt
3. Redigera katalogen och ta bort äldre poster
4. Ångra sedan stegen. För samma relevanta databas som nämns ovan,
    1. återaktivera parametrarna för backint
        1. catalog_backup_using_backint: sant
        1. log_backup_using_backint: sant
        1. enable_auto_log_backup: Ja

### <a name="change-policy"></a>Ändra princip

Du kan ändra den underliggande principen för ett SAP HANA säkerhets kopierings objekt.

* På instrument panelen för valv går du till **säkerhets kopierings objekt**:

  ![Välj säkerhets kopierings objekt](./media/sap-hana-db-manage/backup-items.png)

* Välj **SAP HANA i virtuell Azure-dator**

  ![Välj SAP HANA i virtuell Azure-dator](./media/sap-hana-db-manage/sap-hana-in-azure-vm.png)

* Välj det säkerhets kopierings objekt vars underliggande princip du vill ändra
* Välj den befintliga säkerhets kopierings policyn.

  ![Välj befintlig säkerhets kopierings princip](./media/sap-hana-db-manage/existing-backup-policy.png)

* Ändra principen genom att välja i listan. [Skapa en ny säkerhets kopierings policy om det](./backup-azure-sap-hana-database.md#create-a-backup-policy) behövs.

  ![Välj princip i listruta](./media/sap-hana-db-manage/choose-backup-policy.png)

* Spara ändringarna

  ![Spara ändringarna](./media/sap-hana-db-manage/save-changes.png)

* Princip ändringen påverkar alla tillhör ande säkerhets kopierings objekt och utlöser motsvarande **Konfigurera skydds** jobb.

>[!NOTE]
> Eventuella ändringar i kvarhållningsperioden tillämpas retroaktivt för alla äldre återställnings punkter förutom de nya.

### <a name="modify-policy"></a>Ändra princip

Ändra princip för att ändra säkerhets kopierings typer, frekvenser och kvarhållningsintervall.

>[!NOTE]
>Alla ändringar i kvarhållningsperioden tillämpas retroaktivt på alla äldre återställnings punkter, förutom de nya.

1. I instrument panelen för valv går du till **Hantera principer för > säkerhets kopiering** och väljer den princip som du vill redigera.

   ![Välj den princip som du vill redigera](./media/sap-hana-db-manage/manage-backup-policies.png)

1. Välj **ändra**.

   ![Välj Ändra](./media/sap-hana-db-manage/modify-policy.png)

1. Välj frekvensen för säkerhets kopierings typerna.

   ![Välj säkerhets kopierings frekvens](./media/sap-hana-db-manage/choose-frequency.png)

Princip ändringen påverkar alla tillhör ande säkerhets kopierings objekt och utlöser motsvarande **Konfigurera skydds** jobb.

### <a name="inconsistent-policy"></a>Inkonsekvent princip

Ibland kan en åtgärd för att ändra en princip leda till en **inkonsekvent** princip version för vissa säkerhets kopierings objekt. Detta inträffar när motsvarande **konfigurations skydds** jobb Miss lyckas för säkerhets kopierings objektet när en åtgärd för att ändra princip har utlösts. Den visas på följande sätt i vyn säkerhets kopierings objekt:

![Inkonsekvent princip](./media/sap-hana-db-manage/inconsistent-policy.png)

Du kan åtgärda princip versionen för alla påverkade objekt i ett klick:

![Korrigera princip version](./media/sap-hana-db-manage/fix-policy-version.png)

### <a name="stop-protection-for-an-sap-hana-database"></a>Stoppa skyddet för en SAP HANA databas

Du kan sluta skydda en SAP HANA-databas på ett par olika sätt:

* Stoppa alla framtida säkerhetskopieringsjobb och ta bort alla återställningspunkter.
* Stoppa alla framtida säkerhets kopierings jobb och lämna återställnings punkterna intakta.

Om du väljer att lämna återställnings punkter bör du tänka på följande:

* Alla återställnings punkter förblir intakta för alltid och all rensning stoppas vid stopp skydd med kvarhållning av data.
* Du debiteras för den skyddade instansen och den förbrukade lagringen. Mer information finns i [Azure Backup prissättning](https://azure.microsoft.com/pricing/details/backup/).
* Om du tar bort en data källa utan att stoppa säkerhets kopieringen kommer nya säkerhets kopieringar att Miss lyckas.

Så här stoppar du skydd för en databas:

* På instrument panelen för valvet väljer du **säkerhets kopierings objekt**.
* Under **säkerhets kopierings hanterings typ** väljer du **SAP HANA i virtuell Azure-dator**

  ![Välj SAP HANA i virtuell Azure-dator](./media/sap-hana-db-manage/sap-hana-azure-vm.png)

* Välj den databas som du vill sluta skydda:

  ![Välj databas för att sluta skydda](./media/sap-hana-db-manage/select-database.png)

* På menyn databas väljer du **stoppa säkerhets kopiering**.

  ![Välj Avbryt säkerhets kopiering](./media/sap-hana-db-manage/stop-backup.png)

* På menyn **stoppa säkerhets kopiering** väljer du om du vill behålla eller ta bort data. Ange en orsak och kommentar om du vill.

  ![Välj Behåll eller ta bort data](./media/sap-hana-db-manage/retain-backup-data.png)

* Välj **stoppa säkerhets kopiering**.

### <a name="resume-protection-for-an-sap-hana-database"></a>Återuppta skyddet för en SAP HANA databas

Om du slutar skydda SAP HANA databasen kan du senare återuppta skyddet om du väljer alternativet **Behåll säkerhets kopierings data** . Om du inte behåller säkerhetskopierade data kan du inte återuppta skyddet.

Återuppta skyddet för en SAP HANA databas:

* Öppna säkerhets kopierings objekt och välj **återuppta säkerhets kopiering**.

   ![Välj återuppta säkerhets kopiering](./media/sap-hana-db-manage/resume-backup.png)

* På menyn **Säkerhetskopieringspolicy** väljer du en policy och sedan **Spara**.

### <a name="upgrading-from-sdc-to-mdc"></a>Uppgradera från SDC till MDC

Lär dig hur du fortsätter säkerhetskopiera för en SAP HANA-databas [efter att ha uppgraderat från SDC till MDC](backup-azure-sap-hana-database-troubleshoot.md#sdc-to-mdc-upgrade-with-a-change-in-sid).

### <a name="upgrading-from-sdc-to-mdc-without-a-sid-change"></a>Uppgradera från SDC till MDC utan en SID-ändring

Lär dig hur du fortsätter säkerhetskopiera en SAP HANA databas vars [sid inte har ändrats efter uppgraderingen från SDC till MDC](backup-azure-sap-hana-database-troubleshoot.md#sdc-to-mdc-upgrade-with-no-change-in-sid).

### <a name="upgrading-to-a-new-version-in-either-sdc-or-mdc"></a>Uppgradera till en ny version i antingen SDC eller MDC

Lär dig hur du fortsätter säkerhetskopiera en SAP HANA databas [vars version uppgraderas](backup-azure-sap-hana-database-troubleshoot.md#sdc-version-upgrade-or-mdc-version-upgrade-on-the-same-vm).

### <a name="unregister-an-sap-hana-instance"></a>Avregistrera en SAP HANA instans

Avregistrera en SAP HANA instans när du har inaktiverat skyddet, men innan du tar bort valvet:

* På instrument panelen för valv under **Hantera** väljer du **infrastruktur för säkerhets kopiering**.

   ![Välja infrastruktur för säkerhetskopiering](./media/sap-hana-db-manage/backup-infrastructure.png)

* Välj **hanterings typen säkerhets kopiering** som **arbets belastning i virtuell Azure-dator**

   ![Välj hanterings typen säkerhets kopiering som arbets belastning i virtuell Azure-dator](./media/sap-hana-db-manage/backup-management-type.png)

* I **skyddade servrar** väljer du den instans som ska avregistreras. Om du vill ta bort valvet måste du avregistrera alla servrar/instanser.

* Högerklicka på den skyddade instansen och välj **avregistrera**.

   ![Välj avregistrera](./media/sap-hana-db-manage/unregister.png)

### <a name="re-register-extension-on-the-sap-hana-server-vm"></a>Omregistrera tillägget på den virtuella SAP HANA-servern

Ibland kan arbets belastnings tillägget på den virtuella datorn påverkas av en orsak eller en annan. I sådana fall påbörjas alla åtgärder som utlöses på den virtuella datorn. Du kan sedan behöva registrera tillägget på den virtuella datorn på nytt. Omregistrering av åtgärden installerar om tillägget för säkerhets kopiering av arbets belastning på den virtuella datorn för att fortsätta.

Använd det här alternativet med försiktighet: när utlöses på en virtuell dator med ett redan felfritt tillägg kommer den här åtgärden att starta tillägget för att starta om. Detta kan leda till att alla pågående jobb Miss lyckas. Sök efter ett eller flera av [symptomen](backup-azure-sap-hana-database-troubleshoot.md#re-registration-failures) innan du utlöser omregistrerings åtgärden.

## <a name="next-steps"></a>Nästa steg

* Lär dig hur du [felsöker vanliga problem när du säkerhetskopierar SAP HANA databaser.](./backup-azure-sap-hana-database-troubleshoot.md)
