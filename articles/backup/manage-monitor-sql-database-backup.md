---
title: Hantera och övervaka SQL Server på en virtuell Azure-dator
description: Den här artikeln beskriver hur du hanterar och övervakar SQL Server databaser som körs på en virtuell Azure-dator.
ms.topic: conceptual
ms.date: 09/11/2019
ms.openlocfilehash: 3938e26e134f7d823d8a6f6fac631ebf4442e6ab
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/15/2021
ms.locfileid: "107519144"
---
# <a name="manage-and-monitor-backed-up-sql-server-databases"></a>Hantera och övervaka säkerhetskopierade SQL Server-databaser

Den här artikeln beskriver vanliga uppgifter för att hantera och övervaka SQL Server-databaser som körs på en virtuell Azure-dator (VM) och som säkerhetskopieras till ett Azure Backup Recovery Services-valv [av Azure Backup-tjänsten.](backup-overview.md) Du får lära dig hur du övervakar jobb och aviseringar, stoppar och återupptar databasskydd, kör säkerhetskopieringsjobb och avregistrerar en virtuell dator från säkerhetskopior.

Om du ännu inte har konfigurerat säkerhetskopior för dina SQL Server databaser kan du gå till [Säkerhetskopiera SQL Server databaser på virtuella Azure-datorer](backup-azure-sql-database.md)

## <a name="monitor-backup-jobs-in-the-portal"></a>Övervaka säkerhetskopieringsjobb i portalen

Azure Backup visar alla schemalagda och på begäran-åtgärder **under** Säkerhetskopieringsjobb i portalen, förutom schemalagda loggsäkerhetskopior eftersom de kan vara mycket frekventa. Jobben som visas i den här portalen omfattar databasidentifiering och -registrering, konfiguration av säkerhetskopiering samt säkerhetskopierings- och återställningsåtgärder.

![Portalen för säkerhetskopieringsjobb](./media/backup-azure-sql-database/sql-backup-jobs-list.png)

Mer information om övervakningsscenarier finns [i Övervakning i Azure Portal](backup-azure-monitoring-built-in-monitor.md) övervakning med hjälp av [Azure Monitor](backup-azure-monitoring-use-azuremonitor.md).  

## <a name="view-backup-alerts"></a>Visa säkerhetskopieringsaviseringar

Eftersom loggsäkerhetskopior sker var 15:e minut kan det vara omstösande att övervaka säkerhetskopieringsjobb. Azure Backup enklare övervakning genom att skicka e-postaviseringar. E-postaviseringar är:

- Utlöst för alla säkerhetskopieringsfel.
- Konsolideras på databasnivå efter felkod.
- Skickas endast för databasens första säkerhetskopieringsfel.

Så här övervakar du aviseringar om säkerhetskopiering av databaser:

1. Logga in på [Azure-portalen](https://portal.azure.com).

2. På instrumentpanelen för valvet väljer du **Säkerhetskopieringsaviseringar.**

   ![Välja säkerhetskopieringsaviseringar](./media/backup-azure-sql-database/sql-backup-alerts-list.png)

## <a name="stop-protection-for-a-sql-server-database"></a>Stoppa skydd för en SQL-serverdatabas

Du kan sluta eftersom SQL Server en databas på ett par olika sätt:

- Stoppa alla framtida säkerhetskopieringsjobb och ta bort alla återställningspunkter.
- Stoppa alla framtida säkerhetskopieringsjobb och lämna återställningspunkterna intakta.

Om du väljer att lämna återställningspunkter bör du tänka på följande:

- Alla återställningspunkter förblir intakta för alltid och all rensning stoppas vid stoppskydd med kvarhållna data.
- Du debiteras för den skyddade instansen och den förbrukade lagringen. Mer information finns i [Azure Backup priser.](https://azure.microsoft.com/pricing/details/backup/)
- Om du tar bort en datakälla utan att stoppa säkerhetskopieringar misslyckas nya säkerhetskopieringar. Gamla återställningspunkter upphör att gälla enligt principen, men den senaste återställningspunkten behålls alltid tills du stoppar säkerhetskopieringarna och tar bort data.

Så här stoppar du skydd för en databas:

1. På instrumentpanelen för valvet väljer du **Säkerhetskopieringsobjekt.**

2. Under **Typ av säkerhetskopieringshantering** väljer du **SQL i Azure VM**.

    ![Välja SQL på Azure VM](./media/backup-azure-sql-database/sql-restore-backup-items.png)

3. Välj den databas som du vill stoppa skyddet för.

    ![Välja den databas som skydd ska stoppas för](./media/backup-azure-sql-database/sql-restore-sql-in-vm.png)

4. På databasmenyn väljer du **Stoppa säkerhetskopiering.**

    ![Välja Avbryt säkerhetskopiering](./media/backup-azure-sql-database/stop-db-button.png)

5. På menyn **Stoppa säkerhetskopiering** väljer du om du vill behålla eller ta bort data. Ange en orsak och en kommentar om du vill.

    ![Behåll eller ta bort data på menyn Avbryt säkerhetskopiering](./media/backup-azure-sql-database/stop-backup-button.png)

6. Välj **Stoppa säkerhetskopiering.**

> [!NOTE]
>
>Mer information om alternativet för att ta bort data finns i Vanliga frågor och svar nedan:
>
>- [Vad händer med säkerhetskopiorna om jag tar bort en databas från en automatiskt skyddad instans?](faq-backup-sql-server.yml#if-i-delete-a-database-from-an-autoprotected-instance--what-will-happen-to-the-backups-)
>- [Vad är dess beteende om jag stoppar säkerhetskopieringen av en automatisktskyddad databas?](faq-backup-sql-server.yml#if-i-change-the-name-of-the-database-after-it-has-been-protected--what-will-be-the-behavior-)
>
>

## <a name="resume-protection-for-a-sql-database"></a>Återuppta skyddet för en SQL-databas

Om du väljer alternativet Behåll säkerhetskopieringsdata när du stoppar skyddet **för SQL-databasen** kan du återuppta skyddet senare. Om du inte behåller säkerhetskopierade data kan du inte återuppta skyddet.

Så här återupptar du skyddet av en SQL-databas:

1. Öppna säkerhetskopieringsobjektet och välj **Återuppta säkerhetskopiering.**

    ![Välj Resume backup (Återuppta säkerhetskopiering) för att återuppta databasskyddet](./media/backup-azure-sql-database/resume-backup-button.png)

2. På menyn **Säkerhetskopieringspolicy** väljer du en policy och sedan **Spara**.

## <a name="run-an-on-demand-backup"></a>Köra en säkerhetskopiering på begäran

Du kan köra olika typer av säkerhetskopieringar på begäran:

- Fullständig säkerhetskopia
- Fullständig säkerhetskopia med endast kopiering
- Differentiell säkerhetskopia
- Loggsäkerhetskopia

Du måste ange kvarhållningstiden för fullständig kopieringssäkerhetskopia, men kvarhållningsintervallet för fullständig säkerhetskopiering på begäran anges automatiskt till 45 dagar från den aktuella tiden.

Mer information finns i SQL Server [säkerhetskopieringstyper.](backup-architecture.md#sql-server-backup-types)

## <a name="modify-policy"></a>Ändra princip

Ändra principen för att ändra säkerhetskopieringsfrekvens eller kvarhållningsintervall.

> [!NOTE]
> Ändringar i kvarhållningsperioden tillämpas i efterhand på alla äldre återställningspunkter utöver de nya.

På instrumentpanelen för valvet går du **till**  >  **Hantera säkerhetskopieringsprinciper** och väljer den princip som du vill redigera.

  ![Hantera säkerhetskopieringspolicy](./media/backup-azure-sql-database/modify-backup-policy.png)

  ![Ändra säkerhetskopieringspolicy](./media/backup-azure-sql-database/modify-backup-policy-impact.png)

Principändring påverkar alla associerade säkerhetskopieringsobjekt och utlöser motsvarande **jobb för konfigurationsskydd.**

### <a name="inconsistent-policy"></a>Inkonsekvent princip

Ibland kan en ändringsprincipåtgärd leda till en **inkonsekvent** principversion för vissa säkerhetskopieringsobjekt. Detta inträffar när motsvarande **jobb för konfigurationsskydd** misslyckas för säkerhetskopieringsobjektet efter att en ändringsprincipåtgärd har utlösts. Det visas på följande sätt i vyn för säkerhetskopieringsobjekt:

  ![Inkonsekvent princip](./media/backup-azure-sql-database/inconsistent-policy.png)

Du kan åtgärda principversionen för alla påverkade objekt med ett enda klick:

  ![Åtgärda inkonsekvent princip](./media/backup-azure-sql-database/fix-inconsistent-policy.png)

## <a name="unregister-a-sql-server-instance"></a>Avregistrera en SQL-serverinstans

Avregistrera en SQL Server instans när du har inaktiverat skyddet men innan du tar bort valvet:

1. På instrumentpanelen för valvet går du **till Hantera** och väljer Infrastruktur **för säkerhetskopiering.**  

   ![Välja infrastruktur för säkerhetskopiering](./media/backup-azure-sql-database/backup-infrastructure-button.png)

2. Under **Hanteringsservrar** väljer du **Skyddade servrar**.

   ![Välja skyddade servrar](./media/backup-azure-sql-database/protected-servers.png)

3. I **Skyddade servrar** väljer du den server som du vill avregistrera. Om du vill ta bort valvet måste du avregistrera alla servrar.

4. Högerklicka på den skyddade servern och välj **Avregistrera**.

   ![Välja Ta bort](./media/backup-azure-sql-database/delete-protected-server.jpg)

## <a name="re-register-extension-on-the-sql-server-vm"></a>Registrera tillägget på SQL Server VM

Ibland kan arbetsbelastningstillägget på den virtuella datorn påverkas av en eller annan orsak. I sådana fall börjar alla åtgärder som utlöses på den virtuella datorn att misslyckas. Du kan sedan behöva registrera om tillägget på den virtuella datorn. Åtgärden **Registrera om installerar om** tillägget för säkerhetskopiering av arbetsbelastningar på den virtuella datorn för att åtgärder ska fortsätta. Du hittar det här alternativet under **Infrastruktur för säkerhetskopiering** i Recovery Services-valvet.

![Skyddade servrar under Infrastruktur för säkerhetskopiering](./media/backup-azure-sql-database/protected-servers-backup-infrastructure.png)

Använd det här alternativet med försiktighet. När det utlöses på en virtuell dator med ett redan felfritt tillägg, kommer den här åtgärden att göra att tillägget startas om. Detta kan leda till att alla pågående jobb misslyckas. Sök efter ett eller flera av [symtomen](backup-sql-server-azure-troubleshoot.md#re-registration-failures) innan du utlöser omregisteringsåtgärden.

## <a name="next-steps"></a>Nästa steg

Mer information finns i Felsöka [säkerhetskopieringar på en SQL Server databas.](backup-sql-server-azure-troubleshoot.md)
