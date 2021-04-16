---
title: Back up an Exchange server via System Center DPM
description: Lär dig hur du servrarar en Exchange-server för Azure Backup med System Center 2012 R2 DPM
ms.reviewer: kasinh
ms.topic: conceptual
ms.date: 01/31/2019
ms.openlocfilehash: a89c37b8447b318c44faf0d4e0b1921d305e7f59
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/15/2021
ms.locfileid: "107519399"
---
# <a name="back-up-an-exchange-server-to-azure-backup-with-system-center-2012-r2-dpm"></a>Säkerhetskopiera en Exchange-server till Azure Backup med System Center 2012 R2 DPM

Den här artikeln beskriver hur du konfigurerar en System Center 2012 R2 Data Protection Manager(DPM)-server för att konfigurerar en Microsoft Exchange-server till Azure Backup.  

## <a name="updates"></a>Uppdateringar

Om du vill registrera DPM-servern med Azure Backup måste du installera den senaste samlade uppdateringen för System Center 2012 R2 DPM och den senaste versionen av Azure Backup Agent. Hämta den senaste samlade uppdateringen från [Microsoft Catalog](https://catalog.update.microsoft.com/v7/site/Search.aspx?q=System%20Center%202012%20R2%20Data%20protection%20manager).

> [!NOTE]
> I exemplen i den här artikeln installeras version 2.0.8719.0 av Azure Backup Agent och Samlad uppdatering 6 installeras på System Center 2012 R2 DPM.
>
>

## <a name="prerequisites"></a>Förutsättningar

Innan du fortsätter kontrollerar du att alla krav [för](backup-azure-dpm-introduction.md#prerequisites-and-limitations) att använda Microsoft Azure Backup för att skydda arbetsbelastningar har uppfyllts. Dessa krav omfattar följande:

* Ett säkerhetskopieringsvalv på Azure-webbplatsen har skapats.
* Agent- och valvautentiseringsuppgifter har laddats ned till DPM-servern.
* Agenten installeras på DPM-servern.
* Autentiseringsuppgifterna för valvet användes för att registrera DPM-servern.
* Om du skyddar Exchange 2016 uppgraderar du till DPM 2012 R2 UR9 eller senare.

## <a name="dpm-protection-agent"></a>DPM-skyddsagent

Följ dessa steg om du vill installera DPM-skyddsagenten på Exchange-servern:

1. Kontrollera att brandväggarna är korrekt konfigurerade. Se [Konfigurera brandväggundantag för agenten](/system-center/dpm/configure-firewall-settings-for-dpm).
2. Installera agenten på Exchange-servern genom att välja **Hantering >-agenter > Installera** i DPM-administratörskonsol. Detaljerade [anvisningar finns i Installera DPM-skyddsagenten.](/system-center/dpm/deploy-dpm-protection-agent)

## <a name="create-a-protection-group-for-the-exchange-server"></a>Skapa en skyddsgrupp för Exchange-servern

1. I DPM-administratörskonsol väljer du **Skydd** och  sedan Nytt i verktygsfliken för att öppna **guiden Skapa ny skyddsgrupp.**
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

    Namnge skyddsgruppen och välj sedan båda följande alternativ:

   * Jag vill ha kortvarigt skydd med disk.
   * Jag vill ha onlineskydd.
6. Välj **Nästa**.
7. Välj alternativet **Kör Eseutil för att kontrollera** dataintegritet om du vill kontrollera integriteten för Exchange Server-databaserna.

    När du har valt det här alternativet körs konsekvenskontrollen för säkerhetskopiering på DPM-servern för att undvika den I/O-trafik som genereras genom att **köra kommandot eseutil** på Exchange-servern.

   > [!NOTE]
   > Om du vill använda det här alternativet måste du kopiera Ese.dll- och Eseutil.exe-filerna till katalogen C:\Program Files\Microsoft System Center 2012 R2\DPM\DPM\bin på DPM-servern. Annars utlöses följande fel:  
   > ![eseutil-fel](./media/backup-azure-backup-exchange-server/eseutil-error.png)
   >
   >
8. Välj **Nästa**.
9. Välj databasen för **Kopiera säkerhetskopia** och välj sedan **Nästa.**

   > [!NOTE]
   > Om du inte väljer Fullständig säkerhetskopiering för minst en DAG-kopia av en databas trunkeras inte loggarna.
   >
   >
10. Konfigurera målen för **kortsiktig säkerhetskopiering** och välj sedan **Nästa.**
11. Granska det tillgängliga diskutrymmet och välj sedan **Nästa.**
12. Välj den tid då DPM-servern ska skapa den första replikeringen och välj sedan **Nästa.**
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

1. Om du vill återställa en Exchange-databas **väljer du** Återställning i DPM-administratörskonsol.
2. Leta upp den Exchange-databas som du vill återställa.
3. Välj en återställningspunkt online från *listrutan* återställningstid.
4. Välj **Återställ** för att starta **återställningsguiden.**

Det finns fem återställningstyper för onlineåterställningspunkter:

* **Återställ till den ursprungliga Exchange Server-platsen:** Data återställs till den ursprungliga Exchange-servern.
* **Återställ till en annan databas på en Exchange Server:** Data återställs till en annan databas på en annan Exchange-server.
* **Återställa till en återställningsdatabas:** Data återställs till en Exchange Recovery Database (RDB).
* **Kopiera till en nätverksmapp:** Data återställs till en nätverksmapp.
* **Kopiera till band:** Om du har ett bandbibliotek eller en fristående bandenhet ansluten och konfigurerad på DPM-servern, kopieras återställningspunkten till ett ledigt band.

    ![Välj onlinereplikering](./media/backup-azure-backup-exchange-server/choose-online-replication.png)

## <a name="next-steps"></a>Nästa steg

* [Vanliga frågor och svar Azure Backup Azure Backup om Azure Backup](backup-azure-backup-faq.yml)
