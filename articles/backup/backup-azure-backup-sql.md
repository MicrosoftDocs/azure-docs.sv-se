---
title: Arbetsbelastningen SQL Server till Azure som en DPM-arbetsbelastning
description: En introduktion till att SQL Server databaser med hjälp av Azure Backup tjänsten
ms.topic: conceptual
ms.date: 01/30/2019
ms.openlocfilehash: 03763c3bee5ee4ca5239c49c99fdbeedc242b24d
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/15/2021
ms.locfileid: "107515183"
---
# <a name="back-up-sql-server-to-azure-as-a-dpm-workload"></a>Arbetsbelastningen SQL Server till Azure som en DPM-arbetsbelastning

Den här artikeln leder dig genom konfigurationsstegen för att SQL Server databaser med hjälp av Azure Backup.

Om du vill SQL Server databaser till Azure behöver du ett Azure-konto. Om du inte har ett konto kan du skapa ett kostnadsfritt konto på bara några minuter. Mer information finns i Skapa [ditt kostnadsfria Azure-konto.](https://azure.microsoft.com/pricing/free-trial/)

Så här kan du SQL Server en databas till Azure och återställa den från Azure:

1. Skapa en säkerhetskopieringspolicy för att SQL Server databaser i Azure.
1. Skapa säkerhetskopior på begäran i Azure.
1. Återställa databasen från Azure.

>[!NOTE]
>DPM 2019 UR2 stöder SQL Server redundansklusterinstanser (FCI) med hjälp av klusterdelade volymer (CSV).<br><br>
>Skydd av [SQL Server redundansklusterinstanser med Lagringsdirigering azure](../azure-sql/virtual-machines/windows/failover-cluster-instance-storage-spaces-direct-manually-configure.md)  och SQL Server redundansklusterinstanser med delade [Azure-diskar](../azure-sql/virtual-machines/windows/failover-cluster-instance-azure-shared-disks-manually-configure.md) stöds med den här funktionen. DPM-servern måste distribueras på den virtuella Azure-datorn för att skydda SQL FCI-instansen som distribueras på virtuella Azure-datorer. 

## <a name="prerequisites-and-limitations"></a>Krav och begränsningar

* Om du har en databas med filer på en fjärransluten filresurs, kommer skydd att misslyckas med felet ID 104. DPM stöder inte skydd för att SQL Server data på en fjärrfilresurs.
* DPM kan inte skydda databaser som lagras på fjärranslutna SMB-resurser.
* Kontrollera att [tillgänglighetsgruppens repliker är konfigurerade som skrivskyddade](/sql/database-engine/availability-groups/windows/configure-read-only-access-on-an-availability-replica-sql-server).
* Du måste uttryckligen lägga till systemkontot **NTAuthority\System** i sysadmin-gruppen på SQL Server.
* När du utför en alternativ platsåterställning för en delvis innesluten databas måste du se till att SQL-målinstansen har [funktionen Inneslutna](/sql/relational-databases/databases/migrate-to-a-partially-contained-database#enable) databaser aktiverad.
* När du utför en alternativ platsåterställning för en filströmdatabas måste du se till att SQL-målinstansen har [funktionen för filströmdatabasen](/sql/relational-databases/blob/enable-and-configure-filestream) aktiverad.
* Skydd för SQL Server AlwaysOn:
  * DPM identifierar tillgänglighetsgrupper vid körning av förfrågan när en skyddsgrupp skapas.
  * DPM identifierar en redundans och fortsätter skydda databasen.
  * DPM stödjer klusterkonfigurationer för flera platser för en SQL Server-instans.
* När du skyddar databaser som använder AlwaysOn-funktionen har DPM följande begränsningar:
  * DPM följer säkerhetskopieringsprincipen för tillgänglighetsgrupper som anges i SQL Server baserat på inställningarna för säkerhetskopiering, enligt följande:
    * Föredra sekundär – Säkerhetskopieringar sker på en sekundär replik, förutom när den primära repliken är den enda repliken som är online. Om det finns flera tillgängliga sekundära repliker väljs noden med högst prioritet för säkerhetskopiering. Om endast den primära repliken är tillgänglig bör säkerhetskopieringen ske på den primära repliken.
    * Endast sekundär – Säkerhetskopiering görs inte på den primära repliken. Om bara den primära repliken är online görs ingen säkerhetskopiering.
    * Primär – Säkerhetskopieringar görs alltid på den primära repliken.
    * Alla repliker – Säkerhetskopieringar kan göras på vilken tillgänglig replik som helst i tillgänglighetsgruppen. Noden som säkerhetskopieringen görs från baseras på prioriteten för säkerhetskopiering på varje nod.
  * . Tänk på följande:
    * Säkerhetskopieringar kan ske från alla läsbara repliker, det vill säga primära, synkrona sekundära, asynkrona sekundära.
    * Om en replik undantas från  säkerhetskopieringen, till exempel om Exkludera replik är aktiverad eller om den är markerad som läsbar, väljs inte repliken för säkerhetskopiering under något av alternativen.
    * Om flera repliker är tillgängliga och läsbara väljs noden med högst prioritet för säkerhetskopiering.
    * Om säkerhetskopieringen misslyckas på den valda noden misslyckas säkerhetskopieringen.
    * Återställning till den ursprungliga platsen stöds inte.
* SQL Server 2014 eller högre problem med säkerhetskopiering:
  * SQL Server 2014 lade till en ny funktion för att skapa en databas för lokala [SQL Server i Windows Azure Blob Storage](/sql/relational-databases/databases/sql-server-data-files-in-microsoft-azure). DPM kan inte användas för att skydda den här konfigurationen.
  * Det finns några kända problem med inställningen "Prioritera sekundär" säkerhetskopiering för alternativet SQL AlwaysOn. DPM tar alltid en säkerhetskopia från sekundär. Om ingen sekundär kan hittas misslyckas säkerhetskopieringen.

## <a name="before-you-start"></a>Innan du börjar

Innan du börjar måste du kontrollera att du har uppfyllt [kraven för](backup-azure-dpm-introduction.md#prerequisites-and-limitations) att Azure Backup för att skydda arbetsbelastningar. Här är några av de nödvändiga uppgifterna:

* Skapa ett säkerhetskopieringsvalv.
* Ladda ned autentiseringsuppgifter för valvet.
* Installera Azure Backup agenten.
* Registrera servern med valvet.

## <a name="create-a-backup-policy"></a>Skapa en säkerhetskopieringspolicy

Om du vill SQL Server databaser i Azure skapar du först en säkerhetskopieringsprincip:

1. På Data Protection Manager (DPM) väljer du **arbetsytan** Skydd.
1. Välj **Ny** för att skapa en skyddsgrupp.

    ![Skapa en skyddsgrupp](./media/backup-azure-backup-sql/protection-group.png)
1. Gå igenom vägledningen om hur du skapar en skyddsgrupp på startsidan. Välj sedan **Nästa**.
1. Välj **Servrar.**

    ![Välj typ av serverskyddsgrupp](./media/backup-azure-backup-sql/pg-servers.png)
1. Expandera den SQL Server virtuella datorn där de databaser som du vill brygga finns. Du ser de datakällor som kan säkerhetskopieras från den servern. Expandera **Alla SQL-resurser** och välj sedan de databaser som du vill backa upp. I det här exemplet väljer vi ReportServer$MSDPM2012 och ReportServer$MSDPM2012TempDB. Välj sedan **Nästa**.

    ![Välj en SQL Server databas](./media/backup-azure-backup-sql/pg-databases.png)
1. Ge skyddsgruppen ett namn och välj sedan **Jag vill ha onlineskydd.**

    ![Välj en dataskyddsmetod – kortsiktigt diskskydd eller Azure-onlineskydd](./media/backup-azure-backup-sql/pg-name.png)
1. På sidan **Ange Short-Term mål** inkluderar du de indata som krävs för att skapa säkerhetskopieringspunkter på disken.

    I det här exemplet **är Kvarhållningsintervall** inställt *på 5 dagar*. **Synkroniseringsfrekvensen för säkerhetskopiering** anges till en gång var *15:e minut.* **Fullständig snabbsäkerhetskopiering** *är inställt på 20:00.*

    ![Konfigurera kortsiktiga mål för säkerhetskopieringsskydd](./media/backup-azure-backup-sql/pg-shortterm.png)

   > [!NOTE]
   > I det här exemplet skapas en säkerhetskopieringspunkt 20:00 varje dag. Data som har ändrats sedan föregående dags säkerhetskopieringspunkt 20:00 överförs. Den här processen kallas **Snabb fullständig säkerhetskopiering.** Även om transaktionsloggarna synkroniseras var 15:e minut, och om vi behöver återställa databasen kl. 21:00, skapas punkten genom att loggarna spelas upp igen från den senaste fullständiga snabbsäkerhetskopieringspunkten, som är 20:00 i det här exemplet.
   >
   >

1. Välj **Nästa**. DPM visar det totala tillgängliga lagringsutrymmet. Den visar också den potentiella diskutrymmesanvändningen.

    ![Konfigurera diskallokering](./media/backup-azure-backup-sql/pg-storage.png)

    Som standard skapar DPM en volym per datakälla (SQL Server databas). Volymen används för den första säkerhetskopieringskopian. I den här konfigurationen begränsar LDM (Logical Disk Manager) DPM-skyddet till 300 datakällor (SQL Server databaser). Du kan komma runt den här begränsningen **genom att välja Sam hitta data i DPM-lagringspoolen.** Om du använder det här alternativet använder DPM en enda volym för flera datakällor. Med den här konfigurationen kan DPM skydda upp till 2 000 SQL Server databaser.

    Om du väljer **Utöka automatiskt volymerna** kan DPM ta hänsyn till den ökade säkerhetskopieringsvolymen när produktionsdata växer. Om du inte väljer Utöka **volymerna automatiskt** begränsar DPM lagringen av säkerhetskopior till datakällorna i skyddsgruppen.

1. Om du är administratör kan du välja att överföra den här första säkerhetskopian **automatiskt via nätverket** och välja tidpunkten för överföringen. Eller välj att **Överföra säkerhetskopian** manuellt. Välj sedan **Nästa**.

    ![Välj en metod för att skapa repliker](./media/backup-azure-backup-sql/pg-manual.png)

    Den första säkerhetskopieringskopian kräver överföring av hela datakällan (SQL Server databasen). Säkerhetskopierade data flyttas från produktionsservern (SQL Server dator) till DPM-servern. Om säkerhetskopieringen är stor kan överföring av data över nätverket orsaka överbelastning av bandbredden. Därför kan administratörer välja att använda flyttbara medier för att överföra den första säkerhetskopian **manuellt.** Eller så kan de överföra data **automatiskt via nätverket** vid en angiven tidpunkt.

    När den första säkerhetskopieringen är klar fortsätter säkerhetskopieringarna inkrementellt på den första säkerhetskopian. Inkrementella säkerhetskopieringar tenderar att vara små och överförs enkelt i nätverket.

1. Välj när du vill köra en konsekvenskontroll. Välj sedan **Nästa**.

    ![Välj när du vill köra en konsekvenskontroll](./media/backup-azure-backup-sql/pg-consistent.png)

    DPM kan köra en konsekvenskontroll av säkerhetskopieringsplatsens integritet. Den beräknar kontrollsumman för säkerhetskopieringsfilen på produktionsservern (SQL Server i det här exemplet) och säkerhetskopierade data för filen i DPM. Om kontrollen hittar en konflikt antas den säkerhetskopierade filen i DPM vara skadad. DPM åtgärdar säkerhetskopierade data genom att skicka de block som motsvarar matchningsfelet för kontrollsumma. Eftersom konsekvenskontrollen är en prestandaintensiv åtgärd kan administratörer välja att schemalägga konsekvenskontrollen eller köra den automatiskt.

1. Välj de datakällor som ska skyddas i Azure. Välj sedan **Nästa**.

    ![Välja datakällor som ska skyddas i Azure](./media/backup-azure-backup-sql/pg-sqldatabases.png)
1. Om du är administratör kan du välja säkerhetskopieringsscheman och kvarhållningsprinciper som passar organisationens principer.

    ![Välja scheman och kvarhållningsprinciper](./media/backup-azure-backup-sql/pg-schedule.png)

    I det här exemplet tas säkerhetskopior dagligen kl. 12:00 och 20:00.

    > [!TIP]
    > För snabb återställning behåller du några kortsiktiga återställningspunkter på disken. Dessa återställningspunkter används för driftåterställning. Azure fungerar som en bra annan plats, med högre serviceavtal och garanterad tillgänglighet.
    >
    > Använd DPM för att schemalägga Azure-säkerhetskopieringar när de lokala disksäkerhetskopiorna har avslutats. När du följer den här övningen kopieras den senaste säkerhetskopieringen av disken till Azure.
    >

1. Välj schemat för kvarhållningsprincip. Mer information om hur bevarandeprincipen fungerar finns i Använda Azure Backup [för att ersätta bandinfrastrukturen.](backup-azure-backup-cloud-as-tape.md)

    ![Välj en kvarhållningsprincip](./media/backup-azure-backup-sql/pg-retentionschedule.png)

    I det här exemplet:

    * Säkerhetskopiorna tas dagligen kl. 12:00 och 20:00. De sparas i 180 dagar.
    * Säkerhetskopian på lördagar kl. 12:00 sparas i 104 veckor.
    * Säkerhetskopian från den sista lördagen i månaden kl. 12:00 sparas i 60 månader.
    * Säkerhetskopian från den sista lördagen i mars kl. 12:00 sparas i 10 år.

    När du har valt en kvarhållningsprincip väljer du **Nästa.**

1. Välj hur du vill överföra den första säkerhetskopian till Azure.

    * Alternativet **Automatiskt över nätverket följer** säkerhetskopieringsschemat för att överföra data till Azure.
    * Mer information om **offlinesäkerhetskopiering** finns i [Översikt över offlinesäkerhetskopiering.](offline-backup-overview.md)

    När du har valt en överföringsmekanism väljer du **Nästa.**

1. Granska **principinformationen** på sidan Sammanfattning. Välj sedan **Skapa grupp.** Du kan välja **Stäng** och se jobbförloppet på **arbetsytan** Övervakning.

    ![Förloppet för skapandet av skyddsgruppen](./media/backup-azure-backup-sql/pg-summary.png)

## <a name="create-on-demand-backup-copies-of-a-sql-server-database"></a>Skapa säkerhetskopior på begäran av en SQL Server databas

En återställningspunkt skapas när den första säkerhetskopieringen görs. I stället för att vänta på att schemat ska köras kan du utlösa skapandet av en återställningspunkt manuellt:

1. Kontrollera att databasstatusen är OK i **skyddsgruppen.**

    ![En skyddsgrupp som visar databasens status](./media/backup-azure-backup-sql/sqlbackup-recoverypoint.png)
1. Högerklicka på databasen och välj sedan Skapa **återställningspunkt.**

    ![Välj att skapa en onlineåterställningspunkt](./media/backup-azure-backup-sql/sqlbackup-createrp.png)
1. I den nedrullningsna menyn väljer du **Onlineskydd.** Välj sedan **OK** för att börja skapa en återställningspunkt i Azure.

    ![Börja skapa en återställningspunkt i Azure](./media/backup-azure-backup-sql/sqlbackup-azure.png)
1. Du kan visa jobbförloppet på **arbetsytan** Övervakning.

    ![Visa jobbförloppet i övervakningskonsolen](./media/backup-azure-backup-sql/sqlbackup-monitoring.png)

## <a name="recover-a-sql-server-database-from-azure"></a>Återställa en SQL Server databas från Azure

Så här återställer du en skyddad entitet, till SQL Server en databas, från Azure:

1. Öppna DPM-serverhanteringskonsolen. Gå till arbetsytan **Återställning** för att se de servrar som DPM serverar upp. Välj databasen (i det här exemplet ReportServer$MSDPM2012). Välj en **återställningstid** som slutar med **Online**.

    ![Välja en återställningspunkt](./media/backup-azure-backup-sql/sqlbackup-restorepoint.png)
1. Högerklicka på databasnamnet och välj **Återställ**.

    ![Återställa en databas från Azure](./media/backup-azure-backup-sql/sqlbackup-recover.png)
1. DPM visar information om återställningspunkten. Välj **Nästa**. Om du vill skriva över databasen väljer du återställningstypen **Återställ till den ursprungliga instansen av SQL Server**. Välj sedan **Nästa**.

    ![Återställa en databas till dess ursprungliga plats](./media/backup-azure-backup-sql/sqlbackup-recoveroriginal.png)

    I det här exemplet tillåter DPM att databasen återställs till en annan SQL Server instans eller till en fristående nätverksmapp.
1. På sidan **Ange återställningsalternativ** kan du välja återställningsalternativ. Du kan till exempel välja Begränsning **av nätverksbandbredd för** att begränsa den bandbredd som återställningen använder. Välj sedan **Nästa**.
1. På sidan **Sammanfattning** visas den aktuella återställningskonfigurationen. Välj **Återställ.**

    Återställningsstatusen visar databasen som återställs. Du kan välja **Stäng** för att stänga guiden och visa förloppet på **arbetsytan** Övervakning.

    ![Starta återställningsprocessen](./media/backup-azure-backup-sql/sqlbackup-recoverying.png)

    När återställningen är klar är den återställda databasen konsekvent med programmet.

## <a name="next-steps"></a>Nästa steg

Mer information finns i vanliga [frågor Azure Backup .](backup-azure-backup-faq.yml)