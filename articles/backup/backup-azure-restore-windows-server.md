---
title: Återställa filer till Windows Server med MARS-agenten
description: I den här artikeln får du lära dig hur du återställer data som lagras i Azure till en Windows Server-eller Windows-dator med den Microsoft Azure Recovery Services MARS-agenten.
ms.topic: conceptual
ms.date: 09/07/2018
ms.openlocfilehash: d96c6c54431d0160bd58be0c3491ef2b22753e53
ms.sourcegitcommit: 64ad2c8effa70506591b88abaa8836d64621e166
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/17/2020
ms.locfileid: "88263409"
---
# <a name="restore-files-to-windows-server-using-the-mars-agent"></a>Återställa filer till Windows Server med MARS-agenten

Den här artikeln förklarar hur du återställer data från ett säkerhets kopierings valv. För att återställa data använder du guiden Återställ data i Microsoft Azure Recovery Services (MARS) agenten. Du kan:

* Återställ data till samma dator som säkerhets kopiorna gjordes från.
* Återställ data till en annan dator.

Använd funktionen omedelbar återställning för att montera en skrivbar ögonblicks bild av återställnings punkt som en återställnings volym. Du kan sedan utforska återställnings volymen och kopiera filer till en lokal dator, på så sätt selektivt återställa filer.

> [!NOTE]
> [Azure Backup uppdateringen från januari 2017](https://support.microsoft.com/help/3216528/azure-backup-update-for-microsoft-azure-recovery-services-agent-januar) krävs om du vill använda omedelbar återställning för att återställa data. Dessutom måste säkerhets kopierings data skyddas i valv i de språk som anges i Support artikeln. Se [Azure Backup uppdateringen för januari 2017](https://support.microsoft.com/help/3216528/azure-backup-update-for-microsoft-azure-recovery-services-agent-januar) för den senaste listan över språk som stöder omedelbar återställning.
>

Använd omedelbar återställning med Recovery Services valv i Azure Portal. Om du har lagrat data i säkerhets kopierings valv har de konverterats till Recovery Services valv. Om du vill använda omedelbar återställning hämtar du MARS-uppdateringen och följer de procedurer som nämns direkt återställning.

[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]

## <a name="use-instant-restore-to-recover-data-to-the-same-machine"></a>Använd omedelbar återställning för att återställa data till samma dator

Om du av misstag har tagit bort en fil och vill återställa den till samma dator (från vilken säkerhets kopian görs) kan du använda följande steg för att återställa data.

1. Öppna snapin-modulen **Microsoft Azure Backup**. Om du inte vet var snapin-modulen har installerats söker du efter **Microsoft Azure Backup**i datorn eller servern.

    Skriv bords appen bör visas i Sök resultaten.

2. Välj **Återställ data** för att starta guiden.

    ![Skärm bild av Azure Backup, med återställda data markerade (Återställ till samma dator)](./media/backup-azure-restore-windows-server/recover.png)

3. På sidan **komma igång** för att återställa data till samma server eller dator väljer du **den här servern ( `<server name>` )**  >  **Nästa**.

    ![Skärm bild av guiden Återställ data Komma igång sida (Återställ till samma dator)](./media/backup-azure-restore-windows-server/samemachine_gettingstarted_instantrestore.png)

4. På sidan **Välj återställnings läge** väljer du **enskilda filer och mappar** > **Nästa**.

    ![Skärm bild av guiden Återställ data på sidan Välj återställnings läge (Återställ till samma dator)](./media/backup-azure-restore-windows-server/samemachine_selectrecoverymode_instantrestore.png)
   > [!IMPORTANT]
   > Alternativet för att återställa enskilda filer och mappar kräver .NET Framework 4.5.2 eller senare. Om du inte ser alternativet **enskilda filer och mappar** måste du uppgradera .NET Framework till version 4.5.2 eller senare och försöka igen.

   > [!TIP]
   > Alternativet **enskilda filer och mappar** ger snabb åtkomst till återställnings punkt data. Det är lämpligt för att återställa enskilda filer, med storlekar som totalt inte är större än 80 GB, och erbjuder överförings-eller kopierings hastigheter upp till 6 MBps under återställningen. Alternativet **volym** återställer alla säkerhetskopierade data på en angiven volym. Det här alternativet ger snabbare överförings hastigheter (upp till 40 Mbit/s), vilket är idealiskt för att kunna återskapa stora data mängder eller hela volymer.

5. På sidan **Välj volym och datum** väljer du den volym som innehåller de filer och mappar som du vill återställa.

    Välj en återställnings punkt i kalendern. Datum i **fetstil** anger tillgänglighet för minst en återställnings punkt. Om flera återställnings punkter är tillgängliga inom ett enda datum väljer du den aktuella återställnings punkten i list rutan **tid** .

    ![Skärm bild av guiden Återställ data Välj volym och datum sida (Återställ till samma dator)](./media/backup-azure-restore-windows-server/samemachine_selectvolumedate_instantrestore.png)

6. När du har valt återställnings punkten som ska återställas väljer du **montera**.

    Azure Backup monterar den lokala återställnings punkten och använder den som återställnings volym.

7. På sidan **Bläddra och återställa filer** väljer du **Bläddra** för att öppna Utforskaren och letar upp de filer och mappar som du vill använda.

    ![Skärm bild av guiden Återställ data för att bläddra och återställa filer-sidan (Återställ till samma dator)](./media/backup-azure-restore-windows-server/samemachine_browserecover_instantrestore.png)

8. I Utforskaren i Windows kopierar du de filer och mappar som du vill återställa och klistrar in dem på alla platser som är lokala på servern eller datorn. Du kan öppna eller strömma filerna direkt från återställnings volymen och kontrol lera att du återställer rätt versioner.

    ![Skärm bild av Utforskaren i Windows med kopiera markerat (Återställ till samma dator)](./media/backup-azure-restore-windows-server/samemachine_copy_instantrestore.png)

9. När du är klar väljer du **demontera**på sidan **Bläddra och återställa filer** . Välj sedan **Ja** för att bekräfta att du vill demontera volymen.

    ![Skärm bild av guiden Återställ data för att bläddra och återställa filer-sidan (Återställ till samma dator)](./media/backup-azure-restore-windows-server/samemachine_unmount_instantrestore.png)

    > [!Important]
    > Om du inte väljer att **demontera**, förblir återställnings volymen monterad i 6 timmar från den tidpunkt då den monterades. Monterings tiden utökas dock upp till högst 24 timmar om en fort löp ande fil kopiering sker. Inga säkerhets kopierings åtgärder körs medan volymen monteras. Alla säkerhets kopieringar som körs under den tid då volymen monteras kommer att köras efter att återställnings volymen har demonterats.
    >

## <a name="use-instant-restore-to-restore-data-to-an-alternate-machine"></a>Använd omedelbar återställning för att återställa data till en annan dator

Om hela servern tappas bort kan du fortfarande återställa data från Azure Backup till en annan dator. Följande steg illustrerar arbets flödet.

Dessa steg omfattar följande terminologi:

* *Käll dator* – den ursprungliga datorn från vilken säkerhets kopian gjordes och som för tillfället inte är tillgänglig.
* *Måldator* – den dator som data återställs till.
* *Exempel valv* – det Recovery Services valv som käll datorn och mål datorn är registrerade på.

> [!NOTE]
> Säkerhets kopieringar kan inte återställas till en måldator som kör en tidigare version av operativ systemet. Till exempel kan en säkerhets kopia som tas från en dator med Windows 7 återställas på en dator med Windows 7 (eller senare). En säkerhets kopia som tagits från en Windows 8-dator kan inte återställas till en dator med Windows 7.
>
>

1. Öppna snapin-modulen **Microsoft Azure Backup** på mål datorn.

2. Se till att mål datorn och käll datorn är registrerade på samma Recovery Services-valv.

3. Välj **Återställ data** för att öppna **guiden Återställ data**.

    ![Skärm bild av Azure Backup, med återställda data markerade (Återställ till alternativ dator)](./media/backup-azure-restore-windows-server/recover.png)

4. På sidan **komma igång** väljer du **en annan server**.

    ![Skärm bild av guiden Återställ data Komma igång sidan (Återställ till en annan dator)](./media/backup-azure-restore-windows-server/alternatemachine_gettingstarted_instantrestore.png)

5. Ange den valv fil för valvet som motsvarar exempel valvet och välj **Nästa**.

    Om valvet för valvet är ogiltigt (eller har gått ut) laddar du ned en ny fil för valvet från exempel valvet i Azure Portal. När du har angett giltiga autentiseringsuppgifter för valvet visas namnet på motsvarande säkerhets kopierings valv.

6. På sidan **Välj säkerhets kopierings Server** väljer du käll datorn i listan med datorer som visas och anger lösen frasen. Välj sedan **Nästa**.

    ![Skärm bild av guiden Återställ data på sidan Välj säkerhets kopierings Server (Återställ till alternativ dator)](./media/backup-azure-restore-windows-server/alternatemachine_selectmachine_instantrestore.png)

7. På sidan **Välj återställnings läge** väljer du **enskilda filer och mappar**  >  **Nästa**.

    ![Skärm bild av guiden Återställ data på sidan Välj återställnings läge (Återställ till alternativ dator)](./media/backup-azure-restore-windows-server/alternatemachine_selectrecoverymode_instantrestore.png)

8. På sidan **Välj volym och datum** väljer du den volym som innehåller de filer och mappar som du vill återställa.

    Välj en återställnings punkt i kalendern. Datum i **fetstil** anger tillgänglighet för minst en återställnings punkt. Om flera återställnings punkter är tillgängliga inom ett enda datum väljer du den aktuella återställnings punkten i list rutan **tid** .

    ![Skärm bild av guiden Återställ data Välj volym och datum sida (Återställ till alternativ dator)](./media/backup-azure-restore-windows-server/alternatemachine_selectvolumedate_instantrestore.png)

9. Välj **montera** för att montera återställnings punkten lokalt som en återställnings volym på mål datorn.

10. På sidan **Bläddra och återställa filer** väljer du **Bläddra** för att öppna Utforskaren och letar upp de filer och mappar som du vill använda.

    ![Skärm bild av guiden Återställ data för att bläddra och återställa filer-sidan (Återställ till en annan dator)](./media/backup-azure-restore-windows-server/alternatemachine_browserecover_instantrestore.png)

11. I Utforskaren i Windows kopierar du filerna och mapparna från återställnings volymen och klistrar in dem på mål datorns plats. Du kan öppna eller strömma filerna direkt från återställnings volymen och kontrol lera att rätt versioner återställs.

    ![Skärm bild av Utforskaren i Windows med kopiera markerat (Återställ till alternativ dator)](./media/backup-azure-restore-windows-server/alternatemachine_copy_instantrestore.png)

12. När du är klar väljer du **demontera**på sidan **Bläddra och återställa filer** . Välj sedan **Ja** för att bekräfta att du vill demontera volymen.

    ![Demontera volymen (Återställ till en annan dator)](./media/backup-azure-restore-windows-server/alternatemachine_unmount_instantrestore.png)

    > [!Important]
    > Om du inte väljer att **demontera**, förblir återställnings volymen monterad i 6 timmar från den tidpunkt då den monterades. Monterings tiden utökas dock upp till högst 24 timmar om en fort löp ande fil kopiering sker. Inga säkerhets kopierings åtgärder körs medan volymen monteras. Alla säkerhets kopieringar som körs under den tid då volymen monteras kommer att köras efter att återställnings volymen har demonterats.
    >

## <a name="next-steps"></a>Nästa steg

* Nu när du har återställt dina filer och mappar kan du [Hantera dina säkerhets kopior](backup-azure-manage-windows-server.md).

* Hitta [vanliga frågor om hur du säkerhetskopierar filer och mappar](backup-azure-file-folder-backup-faq.md).
