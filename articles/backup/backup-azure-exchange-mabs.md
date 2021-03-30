---
title: Säkerhetskopiera Exchange Server med Azure Backup Server
description: Lär dig hur du säkerhetskopierar en Exchange-Server till Azure Backup med Azure Backup Server
ms.reviewer: kasinh
ms.topic: conceptual
ms.date: 03/24/2017
ms.openlocfilehash: 86389ef89ced74905db0fc8bfeaabcea6140762a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "91263002"
---
# <a name="back-up-an-exchange-server-to-azure-with-azure-backup-server"></a>Säkerhetskopiera en Exchange-Server till Azure med Azure Backup Server

Den här artikeln beskriver hur du konfigurerar Microsoft Azure Backup Server (MABS) för att säkerhetskopiera en Microsoft Exchange-Server till Azure.  

## <a name="prerequisites"></a>Förutsättningar

Innan du fortsätter kontrollerar du att Azure Backup Server är [installerat och förberett](backup-azure-microsoft-azure-backup.md).

## <a name="mabs-protection-agent"></a>MABS skydds agent

Följ dessa steg om du vill installera MABS-skyddsagenten på Exchange-servern:

1. Kontrol lera att brand väggarna är korrekt konfigurerade. Se [Konfigurera brand Väggs undantag för agenten](/system-center/dpm/configure-firewall-settings-for-dpm).
2. Installera agenten på Exchange-servern genom att välja **hanterings > agenter > installera** i Mabs administratörskonsol. Se [Installera Mabs Protection Agent](/system-center/dpm/deploy-dpm-protection-agent) för detaljerade anvisningar.

## <a name="create-a-protection-group-for-the-exchange-server"></a>Skapa en skydds grupp för Exchange-servern

1. I MABS Administratörskonsol väljer du **skydd** och väljer sedan **nytt** i menyfliksområdet verktyg för att öppna guiden **Skapa ny skydds grupp** .
2. På **välkomst** skärmen i guiden väljer du **Nästa**.
3. På skärmen **Välj typ av skydds grupp** väljer du **servrar** och väljer **Nästa**.
4. Välj den Exchange Server-databas som du vill skydda och välj **Nästa**.

   > [!NOTE]
   > Om du skyddar Exchange 2013 kontrollerar du kraven för [exchange 2013](/system-center/dpm/back-up-exchange).
   >
   >

    I följande exempel är Exchange 2010-databasen vald.

    ![Välj grupp medlemmar](./media/backup-azure-backup-exchange-server/select-group-members.png)
5. Välj data skydds metod.

    Ge skydds gruppen ett namn och välj sedan båda följande alternativ:

   * Jag vill ha kortvarigt skydd med disk.
   * Jag vill ha onlineskydd.
6. Välj **Nästa**.
7. Markera alternativet **Kör Eseutil för att kontrol lera data integriteten** om du vill kontrol lera integriteten för Exchange Server-databaserna.

    När du har valt det här alternativet körs konsekvens kontroll av säkerhets kopiering på MABS för att undvika den I/O-trafik som genereras genom att köra **eseutil** -kommandot på Exchange-servern.

   > [!NOTE]
   > Om du vill använda det här alternativet måste du kopiera Ese.dll och Eseutil.exe filer till katalogen C:\Program Files\Microsoft Azure Backup\DPM\DPM\bin på MABS-servern. Annars utlöses följande fel:  
   > ![Eseutil-fel](./media/backup-azure-backup-exchange-server/eseutil-error.png)
   >
   >
8. Välj **Nästa**.
9. Välj databasen för kopiering av **säkerhets kopia** och välj sedan **Nästa**.

   > [!NOTE]
   > Om du inte väljer fullständig säkerhets kopiering för minst en DAG kopia av en databas trunkeras inte loggarna.
   >
   >
10. Konfigurera målen för **kortsiktig säkerhets kopiering** och välj sedan **Nästa**.
11. Granska tillgängligt disk utrymme och välj sedan **Nästa**.
12. Välj den tid då MABS-servern ska skapa den inledande replikeringen och välj sedan **Nästa**.
13. Välj alternativ för konsekvens kontroll och välj sedan **Nästa**.
14. Välj den databas som du vill säkerhetskopiera till Azure och välj sedan **Nästa**. Exempel:

    ![Ange skydds data online](./media/backup-azure-backup-exchange-server/specify-online-protection-data.png)
15. Definiera schemat för **Azure Backup** och välj sedan **Nästa**. Exempel:

    ![Ange schemat för onlinesäkerhetskopiering](./media/backup-azure-backup-exchange-server/specify-online-backup-schedule.png)

    > [!NOTE]
    > Antecknings återställnings punkter baseras på snabba och fullständiga återställnings punkter. Därför måste du schemalägga onlineåterställningspunkt efter den tid som har angetts för den fullständiga snabb återställnings punkten.
    >
    >
16. Konfigurera bevarande principen för **Azure Backup** och välj sedan **Nästa**.
17. Välj ett alternativ för replikering online och välj **Nästa**.

    Om du har en stor databas kan det ta lång tid för den första säkerhets kopieringen att skapas över nätverket. För att undvika det här problemet kan du skapa en säkerhets kopiering offline.  

    ![Ange bevarande princip online](./media/backup-azure-backup-exchange-server/specify-online-retention-policy.png)
18. Bekräfta inställningarna och välj sedan **Skapa grupp**.
19. Välj **Stäng**.

## <a name="recover-the-exchange-database"></a>Återställa Exchange-databasen

1. Om du vill återställa en Exchange-databas väljer du **återställning** i MABS-administratörskonsol.
2. Leta upp Exchange-databasen som du vill återställa.
3. Välj en online återställnings punkt i list rutan *återställnings tid* .
4. Välj **Återställ** för att starta **återställnings guiden**.

För online-återställnings punkter finns fem återställnings typer:

* **Återställ till den ursprungliga Exchange Server-platsen:** Data kommer att återställas till den ursprungliga Exchange-servern.
* **Återställ till en annan databas på en Exchange-Server:** Data kommer att återställas till en annan databas på en annan Exchange-Server.
* **Återställ till en återställnings databas:** Data kommer att återställas till en Exchange Recovery-databas (RDB).
* **Kopiera till en nätverksmapp:** Data kommer att återställas till en nätverksmapp.
* **Kopiera till band:** Om du har ett band bibliotek eller en fristående band enhet som är ansluten och konfigurerad på MABS, kopieras återställnings punkten till ett ledigt band.

    ![Välj online-replikering](./media/backup-azure-backup-exchange-server/choose-online-replication.png)

## <a name="next-steps"></a>Nästa steg

* [Azure Backup vanliga frågor och svar](backup-azure-backup-faq.md)
