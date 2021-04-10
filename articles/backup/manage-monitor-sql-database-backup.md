---
title: Hantera och övervaka SQL Server databaser på en virtuell Azure-dator
description: Den här artikeln beskriver hur du hanterar och övervakar SQL Server databaser som körs på en virtuell Azure-dator.
ms.topic: conceptual
ms.date: 09/11/2019
ms.openlocfilehash: e37e6fc211b34b7e427b66db374a705faafd25f9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "97858746"
---
# <a name="manage-and-monitor-backed-up-sql-server-databases"></a>Hantera och övervaka säkerhetskopierade SQL Server-databaser

I den här artikeln beskrivs vanliga uppgifter för att hantera och övervaka SQL Server databaser som körs på en virtuell Azure-dator (VM) och som säkerhets kopie ras till en Azure Backup Recovery Services valvet av [Azure backups](backup-overview.md) tjänsten. Du får lära dig att övervaka jobb och aviseringar, stoppa och återuppta databas skydd, köra säkerhets kopierings jobb och avregistrera en virtuell dator från säkerhets kopior.

Om du ännu inte har konfigurerat säkerhets kopior för dina SQL Server-databaser, se [säkerhetskopiera SQL Server databaser på virtuella Azure-datorer](backup-azure-sql-database.md)

## <a name="monitor-backup-jobs-in-the-portal"></a>Övervaka säkerhets kopierings jobb i portalen

Azure Backup visar alla schemalagda och begärda åtgärder under **säkerhets kopierings jobb** i portalen, förutom de schemalagda logg säkerhets kopiorna eftersom de kan vara mycket frekventa. De jobb som visas i den här portalen är identifiering och registrering av databaser, konfiguration av säkerhets kopiering och säkerhets kopiering och återställning.

![Säkerhets kopierings jobb portalen](./media/backup-azure-sql-database/sql-backup-jobs-list.png)

Information om övervaknings scenarier finns [i övervakning i Azure Portal](backup-azure-monitoring-built-in-monitor.md) och [övervakning med hjälp av Azure Monitor](backup-azure-monitoring-use-azuremonitor.md).  

## <a name="view-backup-alerts"></a>Visa säkerhetskopieringsaviseringar

Eftersom säkerhets kopiering av loggar sker var 15: e minut, kan det vara omständligt att övervaka säkerhets kopierings jobb. Azure Backup underlättar övervakningen genom att skicka e-postaviseringar. E-postaviseringar är:

- Utlöses för alla säkerhets kopierings problem.
- Konsol IDE rad på databas nivå efter felkod.
- Skickas bara för första säkerhets kopierings försöket i databasen.

Övervaka aviseringar om säkerhets kopiering av databas:

1. Logga in på [Azure-portalen](https://portal.azure.com).

2. På instrument panelen för valv väljer du **säkerhets kopierings aviseringar**.

   ![Välja säkerhetskopieringsaviseringar](./media/backup-azure-sql-database/sql-backup-alerts-list.png)

## <a name="stop-protection-for-a-sql-server-database"></a>Stoppa skydd för en SQL-serverdatabas

Du kan sluta säkerhetskopiera en SQL Server databas på ett par olika sätt:

- Stoppa alla framtida säkerhets kopierings jobb och ta bort alla återställnings punkter.
- Stoppa alla framtida säkerhets kopierings jobb och lämna återställnings punkterna intakta.

Om du väljer att lämna återställnings punkter bör du tänka på följande:

- Alla återställnings punkter förblir intakta för alltid och all rensning stoppas vid stopp skydd med kvarhållning av data.
- Du debiteras för den skyddade instansen och den förbrukade lagringen. Mer information finns i [Azure Backup prissättning](https://azure.microsoft.com/pricing/details/backup/).
- Om du tar bort en data källa utan att stoppa säkerhets kopieringen kommer nya säkerhets kopieringar att Miss lyckas. De gamla återställnings punkterna upphör att gälla enligt principen, men den senaste återställnings punkten kommer alltid att behållas tills du stoppar säkerhets kopieringarna och tar bort data.

Så här stoppar du skydd för en databas:

1. På instrument panelen för valvet väljer du **säkerhets kopierings objekt**.

2. Under **säkerhets kopierings hanterings typ** väljer du **SQL i Azure VM**.

    ![Välja SQL på Azure VM](./media/backup-azure-sql-database/sql-restore-backup-items.png)

3. Välj den databas som du vill stoppa skyddet för.

    ![Välja den databas som skydd ska stoppas för](./media/backup-azure-sql-database/sql-restore-sql-in-vm.png)

4. På menyn databas väljer du **stoppa säkerhets kopiering**.

    ![Välja Avbryt säkerhetskopiering](./media/backup-azure-sql-database/stop-db-button.png)

5. På menyn **stoppa säkerhets kopiering** väljer du om du vill behålla eller ta bort data. Ange en orsak och kommentar om du vill.

    ![Behåll eller ta bort data på menyn stoppa säkerhets kopiering](./media/backup-azure-sql-database/stop-backup-button.png)

6. Välj **stoppa säkerhets kopiering**.

> [!NOTE]
>
>Mer information om alternativet ta bort data finns i vanliga frågor och svar nedan:
>
>- [Vad händer med säkerhetskopiorna om jag tar bort en databas från en automatiskt skyddad instans?](faq-backup-sql-server.md#if-i-delete-a-database-from-an-autoprotected-instance-what-will-happen-to-the-backups)
>- [Vad händer om jag avbryter säkerhets kopieringen av en automatiskt skyddad databas?](faq-backup-sql-server.md#if-i-change-the-name-of-the-database-after-it-has-been-protected-what-will-be-the-behavior)
>
>

## <a name="resume-protection-for-a-sql-database"></a>Återuppta skyddet för en SQL-databas

Om du slutar skydda SQL-databasen kan du senare återuppta skyddet om du väljer alternativet **Behåll säkerhets kopierings data** . Om du inte behåller säkerhetskopierade data kan du inte återuppta skyddet.

Återuppta skyddet för en SQL-databas:

1. Öppna säkerhets kopierings objekt och välj **återuppta säkerhets kopiering**.

    ![Välj Resume backup (Återuppta säkerhetskopiering) för att återuppta databasskyddet](./media/backup-azure-sql-database/resume-backup-button.png)

2. På menyn **Säkerhetskopieringspolicy** väljer du en policy och sedan **Spara**.

## <a name="run-an-on-demand-backup"></a>Köra en säkerhetskopiering på begäran

Du kan köra olika typer av säkerhets kopieringar på begäran:

- Fullständig säkerhetskopia
- Fullständig säkerhetskopia med endast kopiering
- Differentiell säkerhetskopia
- Loggsäkerhetskopia

Du måste ange kvarhållningsperioden för fullständig säkerhets kopiering, men kvarhållningsintervall för fullständig säkerhets kopiering på begäran kommer automatiskt att ställas in på 45 dagar från aktuell tid.

Mer information finns i [SQL Server säkerhets kopierings typer](backup-architecture.md#sql-server-backup-types).

## <a name="modify-policy"></a>Ändra princip

Ändra princip för att ändra säkerhets kopierings frekvens eller kvarhållningsintervall.

> [!NOTE]
> Eventuella ändringar i kvarhållningsperioden tillämpas retroaktivt för alla äldre återställnings punkter förutom de nya.

I valv instrument panelen, gå till **Hantera**  >  **säkerhets kopierings principer** och välj den princip som du vill redigera.

  ![Hantera säkerhets kopierings princip](./media/backup-azure-sql-database/modify-backup-policy.png)

  ![Ändra säkerhets kopierings princip](./media/backup-azure-sql-database/modify-backup-policy-impact.png)

Princip ändringen påverkar alla tillhör ande säkerhets kopierings objekt och utlöser motsvarande **Konfigurera skydds** jobb.

### <a name="inconsistent-policy"></a>Inkonsekvent princip

Ibland kan en åtgärd för att ändra en princip leda till en **inkonsekvent** princip version för vissa säkerhets kopierings objekt. Detta inträffar när motsvarande **konfigurations skydds** jobb Miss lyckas för säkerhets kopierings objektet när en åtgärd för att ändra princip har utlösts. Den visas på följande sätt i vyn säkerhets kopierings objekt:

  ![Inkonsekvent princip](./media/backup-azure-sql-database/inconsistent-policy.png)

Du kan åtgärda princip versionen för alla påverkade objekt i ett klick:

  ![Åtgärda inkonsekvent princip](./media/backup-azure-sql-database/fix-inconsistent-policy.png)

## <a name="unregister-a-sql-server-instance"></a>Avregistrera en SQL-serverinstans

Avregistrera en SQL Server instans när du har inaktiverat skyddet, men innan du tar bort valvet:

1. På instrument panelen för valv under **Hantera** väljer du **infrastruktur för säkerhets kopiering**.  

   ![Välja infrastruktur för säkerhetskopiering](./media/backup-azure-sql-database/backup-infrastructure-button.png)

2. Under **Hanteringsservrar** väljer du **Skyddade servrar**.

   ![Välja skyddade servrar](./media/backup-azure-sql-database/protected-servers.png)

3. I **skyddade servrar** väljer du den server som ska avregistreras. Om du vill ta bort valvet måste du avregistrera alla servrar.

4. Högerklicka på den skyddade servern och välj **avregistrera**.

   ![Välja Ta bort](./media/backup-azure-sql-database/delete-protected-server.jpg)

## <a name="re-register-extension-on-the-sql-server-vm"></a>Registrera tillägget på nytt på SQL Server VM

Ibland kan arbets belastnings tillägget på den virtuella datorn bli påverkat av en orsak eller en annan. I sådana fall påbörjas alla åtgärder som utlöses på den virtuella datorn. Du kan sedan behöva registrera tillägget på den virtuella datorn på nytt. **Omregistrerings** åtgärden installerar om säkerhets kopieringen av arbets belastningen på den virtuella datorn för att fortsätta. Du kan hitta det här alternativet under **säkerhets kopierings infrastruktur** i Recovery Services valvet.

![Skyddade servrar under säkerhets kopierings infrastruktur](./media/backup-azure-sql-database/protected-servers-backup-infrastructure.png)

Använd det här alternativet med försiktighet. När den utlöses på en virtuell dator med ett redan felfritt tillägg kommer den här åtgärden att leda till att tillägget startas om. Detta kan leda till att alla pågående jobb Miss lyckas. Sök efter ett eller flera av [symptomen](backup-sql-server-azure-troubleshoot.md#re-registration-failures) innan du utlöser omregistrerings åtgärden.

## <a name="next-steps"></a>Nästa steg

Mer information finns i [Felsöka säkerhets kopieringar på en SQL Server databas](backup-sql-server-azure-troubleshoot.md).
