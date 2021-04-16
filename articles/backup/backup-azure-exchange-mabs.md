---
title: Back up Exchange server with Azure Backup Server
description: Lär dig hur du servrarar en Exchange-server för Azure Backup med Azure Backup Server
ms.reviewer: kasinh
ms.topic: conceptual
ms.date: 03/24/2017
ms.openlocfilehash: f3a7fae5a1f5ec933c015546ddf2bdb2898e3904
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/15/2021
ms.locfileid: "107515506"
---
# <a name="back-up-an-exchange-server-to-azure-with-azure-backup-server"></a>Back up an Exchange server to Azure with Azure Backup Server

Den här artikeln beskriver hur du konfigurerar Microsoft Azure Backup Server (MABS) för att konfigurerar en Microsoft Exchange-server till Azure.  

## <a name="prerequisites"></a>Förutsättningar

Innan du fortsätter kontrollerar du att Azure Backup Server är [installerat och förberett.](backup-azure-microsoft-azure-backup.md)

## <a name="mabs-protection-agent"></a>MABS-skyddsagent

Följ dessa steg om du vill installera MABS-skyddsagenten på Exchange-servern:

1. Kontrollera att brandväggarna är korrekt konfigurerade. Se [Konfigurera brandväggundantag för agenten](/system-center/dpm/configure-firewall-settings-for-dpm).
2. Installera agenten på Exchange-servern genom att välja **Hanteringsagenter > installera >** MABS-Administratörskonsol. Detaljerade [anvisningar finns i Installera MABS-skyddsagenten.](/system-center/dpm/deploy-dpm-protection-agent)

## <a name="create-a-protection-group-for-the-exchange-server"></a>Skapa en skyddsgrupp för Exchange-servern

1. I MABS-Administratörskonsol väljer **du Skydd** och  sedan Nytt i verktygsfliken för att öppna guiden **Skapa ny skyddsgrupp.**
2. På **välkomstskärmen** i guiden väljer du **Nästa.**
3. På skärmen **Välj typ av skyddsgrupp** väljer **du Servrar** och sedan **Nästa.**
4. Välj den Exchange-serverdatabas som du vill skydda och välj **Nästa.**

   > [!NOTE]
   > Om du skyddar Exchange 2013 kontrollerar du kraven [för Exchange 2013.](/system-center/dpm/back-up-exchange)
   >
   >

    I följande exempel är Exchange 2010-databasen vald.

    ![Välj gruppmedlemmar](./media/backup-azure-backup-exchange-server/select-group-members.png)
5. Välj dataskyddsmetod.

    Ge skyddsgruppen ett namn och välj sedan båda följande alternativ:

   * Jag vill ha kortvarigt skydd med disk.
   * Jag vill ha onlineskydd.
6. Välj **Nästa**.
7. Välj alternativet **Kör Eseutil för att** kontrollera dataintegriteten om du vill kontrollera integriteten för Exchange Server-databaserna.

    När du har valt det här alternativet körs konsekvenskontrollen för säkerhetskopiering på MABS för att undvika den I/O-trafik som genereras genom att **köra eseutil-kommandot** på Exchange-servern.

   > [!NOTE]
   > Om du vill använda det här alternativet måste du kopiera Ese.dll- och Eseutil.exe-filerna till katalogen C:\Program Files\Microsoft Azure Backup\DPM\DPM\bin på MABS-servern. Annars utlöses följande fel:  
   > ![eseutil-fel](./media/backup-azure-backup-exchange-server/eseutil-error.png)
   >
   >
8. Välj **Nästa**.
9. Välj databasen för **Kopiera säkerhetskopiering** och välj sedan **Nästa.**

   > [!NOTE]
   > Om du inte väljer Fullständig säkerhetskopiering för minst en DAG-kopia av en databas trunkeras inte loggarna.
   >
   >
10. Konfigurera målen för **kortsiktig säkerhetskopiering** och välj sedan **Nästa.**
11. Granska det tillgängliga diskutrymmet och välj sedan **Nästa.**
12. Välj den tid då MABS-servern ska skapa den inledande replikeringen och välj sedan **Nästa.**
13. Välj konsekvenskontrollalternativen och välj sedan **Nästa.**
14. Välj den databas som du vill backa upp till Azure och välj sedan **Nästa.** Exempel:

    ![Ange data för onlineskydd](./media/backup-azure-backup-exchange-server/specify-online-protection-data.png)
15. Definiera schemat för **Azure Backup** och välj sedan **Nästa.** Exempel:

    ![Ange schema för onlinesäkerhetskopiering](./media/backup-azure-backup-exchange-server/specify-online-backup-schedule.png)

    > [!NOTE]
    > Obs! Onlineåterställningspunkter baseras på fullständiga expressåterställningspunkter. Därför måste du schemalägga onlineåterställningspunkten efter den tid som anges för den fullständiga expressåterställningspunkten.
    >
    >
16. Konfigurera bevarandeprincipen för **Azure Backup** och välj sedan **Nästa.**
17. Välj ett alternativ för onlinereplikering och välj **Nästa.**

    Om du har en stor databas kan det ta lång tid innan den första säkerhetskopieringen skapas över nätverket. Du kan undvika det här problemet genom att skapa en offlinesäkerhetskopia.  

    ![Ange princip för onlinebevarande](./media/backup-azure-backup-exchange-server/specify-online-retention-policy.png)
18. Bekräfta inställningarna och välj sedan **Skapa grupp.**
19. Välj **Stäng**.

## <a name="recover-the-exchange-database"></a>Återställa Exchange-databasen

1. Om du vill återställa en Exchange-databas **väljer du** Återställning i MABS-Administratörskonsol.
2. Leta upp den Exchange-databas som du vill återställa.
3. Välj en återställningspunkt online från *listrutan* återställningstid.
4. Välj **Återställ** för att starta **återställningsguiden.**

Det finns fem återställningstyper för onlineåterställningspunkter:

* **Återställ till den ursprungliga Exchange Server-platsen:** Data återställs till den ursprungliga Exchange-servern.
* **Återställ till en annan databas på en Exchange Server:** Data återställs till en annan databas på en annan Exchange-server.
* **Återställa till en återställningsdatabas:** Data återställs till en Exchange Recovery Database (RDB).
* **Kopiera till en nätverksmapp:** Data återställs till en nätverksmapp.
* **Kopiera till band:** Om du har ett bandbibliotek eller en fristående bandenhet ansluten och konfigurerad på MABS, kopieras återställningspunkten till ett ledigt band.

    ![Välj onlinereplikering](./media/backup-azure-backup-exchange-server/choose-online-replication.png)

## <a name="next-steps"></a>Nästa steg

* [Vanliga frågor och svar Azure Backup Azure Backup om Azure Backup](backup-azure-backup-faq.yml)
