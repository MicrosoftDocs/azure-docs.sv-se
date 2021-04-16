---
title: Återställa filer till Windows Server med MARS-agenten
description: I den här artikeln får du lära dig hur du återställer data som lagras i Azure till en Windows-server eller Windows-dator med Microsoft Azure Recovery Services-agenten (MARS).
ms.topic: conceptual
ms.date: 09/07/2018
ms.openlocfilehash: 7ca0787ec38e1bc22b62e756c7ee56c5c9e93493
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/15/2021
ms.locfileid: "107517342"
---
# <a name="restore-files-to-windows-server-using-the-mars-agent"></a>Återställa filer till Windows Server med MARS-agenten

Den här artikeln beskriver hur du återställer data från ett säkerhetskopieringsvalv. Om du vill återställa data använder du guiden Återställ data i Microsoft Azure Recovery Services (MARS)-agenten. Du kan:

* Återställ data till samma dator som säkerhetskopiorna togs från.
* Återställ data till en annan dator.

Använd funktionen Omedelbar återställning för att montera en skrivbar ögonblicksbild av återställningspunkten som en återställningsvolym. Du kan sedan utforska återställningsvolymen och kopiera filer till en lokal dator, på så sätt selektivt återställa filer.

> [!NOTE]
> Uppdateringen [för januari 2017 Azure Backup krävs](https://support.microsoft.com/help/3216528/azure-backup-update-for-microsoft-azure-recovery-services-agent-januar) om du vill använda Omedelbar återställning för att återställa data. Dessutom måste säkerhetskopierade data skyddas i valv på de språk som anges i supportartikeln. I uppdateringen [för januari 2017 Azure Backup](https://support.microsoft.com/help/3216528/azure-backup-update-for-microsoft-azure-recovery-services-agent-januar) den senaste listan över språk som stöder omedelbar återställning.
>

Använd omedelbar återställning med Recovery Services-valv i Azure Portal. Om du har lagrat data i Backup-valv har de konverterats till Recovery Services-valv. Om du vill använda omedelbar återställning laddar du ned MARS-uppdateringen och följer de procedurer som nämner Omedelbar återställning.

[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]

## <a name="use-instant-restore-to-recover-data-to-the-same-machine"></a>Använda omedelbar återställning för att återställa data till samma dator

Om du av misstag har tagit bort en fil och vill återställa den till samma dator (från vilken säkerhetskopian har tagits), hjälper följande steg dig att återställa data.

1. Öppna snapin-modulen **Microsoft Azure Backup**. Om du inte vet var snapin-modulen installerades söker du i datorn eller servern efter **Microsoft Azure Backup**.

    Skrivbordsappen bör visas i sökresultaten.

2. Välj **Återställ data** för att starta guiden.

    ![Skärmbild av Azure Backup, med Återställ data markerat (återställ till samma dator)](./media/backup-azure-restore-windows-server/recover.png)

3. På sidan **Komma igång** du återställa data till samma server eller dator genom att välja **Den här servern ( `<server name>` )**  >  **Nästa**.

    ![Skärmbild av guiden Återställ data Komma igång sida (återställ till samma dator)](./media/backup-azure-restore-windows-server/samemachine_gettingstarted_instantrestore.png)

4. På sidan **Välj återställningsläge** väljer du **Enskilda filer och mappar** > **Nästa.**

    ![Skärmbild av sidan Återställ data i guiden Välj återställningsläge (återställ till samma dator)](./media/backup-azure-restore-windows-server/samemachine_selectrecoverymode_instantrestore.png)
   > [!IMPORTANT]
   > Alternativet för att återställa enskilda filer och mappar kräver .NET Framework 4.5.2 eller senare. Om du inte ser  alternativet Enskilda filer och mappar måste du uppgradera .NET Framework till version 4.5.2 eller senare och försöka igen.

   > [!TIP]
   > Alternativet **Enskilda filer och mappar** ger snabb åtkomst till återställningspunktdata. Det är lämpligt för att återställa enskilda filer och rekommenderas för en total storlek på mindre än 80 GB. Den erbjuder överförings- eller kopieringshastigheter på upp till 6 MBps under återställningen. Alternativet **Volym** återställer alla säkerhetskopierade data på en angiven volym. Det här alternativet ger snabbare överföring (upp till 40 MBps) och rekommenderas för återställning av stora data eller hela volymer.

5. På sidan **Välj volym och datum** väljer du den volym som innehåller de filer och mappar som du vill återställa.

    Välj en återställningspunkt i kalendern. Datum i **fetstil** anger tillgängligheten för minst en återställningspunkt. Om flera återställningspunkter är tillgängliga inom ett enda datum väljer du den specifika återställningspunkten från **listrutan** Tid.

    ![Skärmbild av sidan Välj volym och datum i guiden Återställ data (återställ till samma dator)](./media/backup-azure-restore-windows-server/samemachine_selectvolumedate_instantrestore.png)

6. När du har valt återställningspunkt väljer du **Montera**.

    Azure Backup monterar den lokala återställningspunkten och använder den som en återställningsvolym.

7. På sidan **Bläddra och återställ filer** väljer du Bläddra **för** att öppna Utforskaren i Windows och söker efter de filer och mappar som du vill använda.

    ![Skärmbild av sidan Bläddra i guiden Återställ data och återställ filer (återställ till samma dator)](./media/backup-azure-restore-windows-server/samemachine_browserecover_instantrestore.png)

8. I Utforskaren kopierar du de filer och mappar som du vill återställa och klistrar in dem på valfri plats som är lokal på servern eller datorn. Du kan öppna eller strömma filerna direkt från återställningsvolymen och kontrollera att du återställer rätt versioner.

    ![Skärmbild av Utforskaren i Windows med Kopiera markerat (återställ till samma dator)](./media/backup-azure-restore-windows-server/samemachine_copy_instantrestore.png)

9. När du är klar går du till sidan **Bläddra och återställ** filer och väljer **Demontera**. Välj sedan **Ja** för att bekräfta att du vill demontera volymen.

    ![Skärmbild av sidan Återställ data i guiden Bläddra och återställ filer (återställ till samma dator) – Bekräfta återställningsvolym demontera](./media/backup-azure-restore-windows-server/samemachine_unmount_instantrestore.png)

    > [!Important]
    > Om du inte väljer **Demontera förblir** återställningsvolymen monterad i 6 timmar från den tidpunkt då den monterades. Monteringstiden utökas dock upp till högst 24 timmar i händelse av en pågående filkopia. Inga säkerhetskopieringsåtgärder körs när volymen är monterad. Säkerhetskopieringsåtgärd som schemalagts att köras under den tid då volymen monteras körs när återställningsvolymen har demonterats.
    >

## <a name="use-instant-restore-to-restore-data-to-an-alternate-machine"></a>Använda omedelbar återställning för att återställa data till en alternativ dator

Om hela servern går förlorad kan du fortfarande återställa data från Azure Backup till en annan dator. Följande steg illustrerar arbetsflödet.

De här stegen innehåller följande terminologi:

* *Källdator* – Den ursprungliga datorn som säkerhetskopian togs från och som för närvarande inte är tillgänglig.
* *Måldator* – Den dator som data återställs till.
* *Exempelvalv* – Recovery Services-valvet som källdatorn och måldatorn är registrerade på.

> [!NOTE]
> Säkerhetskopior kan inte återställas till en måldator som kör en tidigare version av operativsystemet. En säkerhetskopia som tas från en Windows 7-dator kan till exempel återställas på en Dator med Windows 7 (eller senare). En säkerhetskopia som tagits Windows 10 en dator kan inte återställas till en Windows 7-dator.
>
>

1. Öppna **Microsoft Azure Backup** snapin-modulen på måldatorn.

2. Kontrollera att måldatorn och källdatorn är registrerade i samma Recovery Services-valv.

3. Välj **Återställ data** för att öppna guiden Återställ **data.**

    ![Skärmbild av Azure Backup, med Återställ data markerat (återställ till en annan dator)](./media/backup-azure-restore-windows-server/recover.png)

4. På sidan **Komma igång** väljer du En **annan server**.

    ![Skärmbild av guiden Återställ data Komma igång (återställ till en annan dator)](./media/backup-azure-restore-windows-server/alternatemachine_gettingstarted_instantrestore.png)

5. Ange valvaudentialfilen som motsvarar exempelvalvet och välj **Nästa.**

    Om valvaudentialfilen är ogiltig (eller har upphört att gälla) laddar du ned en ny [valvaudentialfil](backup-azure-file-folder-backup-faq.yml#where-can-i-download-the-vault-credentials-file-) från exempelvalvet i Azure Portal. När du har anger en giltig valvaudential visas namnet på motsvarande säkerhetskopieringsvalv.

6. På sidan **Välj säkerhetskopieringsserver** väljer du källdatorn i listan över datorer som visas och anger lösenfrasen. Välj sedan **Nästa**.

    ![Skärmbild av guiden Återställ data Välj säkerhetskopieringsserver (återställ till en annan dator)](./media/backup-azure-restore-windows-server/alternatemachine_selectmachine_instantrestore.png)

7. På sidan **Välj återställningsläge** väljer du **Enskilda filer och mappar**  >  **Nästa.**

    ![Skärmbild av sidan Återställ data i guiden Välj återställningsläge (återställ till en annan dator)](./media/backup-azure-restore-windows-server/alternatemachine_selectrecoverymode_instantrestore.png)

8. På sidan **Välj volym och datum** väljer du den volym som innehåller de filer och mappar som du vill återställa.

    Välj en återställningspunkt i kalendern. Datum i **fetstil** anger tillgängligheten för minst en återställningspunkt. Om flera återställningspunkter är tillgängliga inom ett enda datum väljer du den specifika återställningspunkten från **listrutan** Tid.

    ![Skärmbild av sidan Välj volym och datum i guiden Återställ data (återställ till en annan dator)](./media/backup-azure-restore-windows-server/alternatemachine_selectvolumedate_instantrestore.png)

9. Välj **Montera** för att lokalt montera återställningspunkten som en återställningsvolym på måldatorn.

10. På sidan **Bläddra och återställ filer** väljer du Bläddra **för** att öppna Utforskaren i Windows och hittar de filer och mappar som du vill använda.

    ![Skärmbild av sidan Återställ data i guiden Bläddra och återställa filer (återställa till en annan dator)](./media/backup-azure-restore-windows-server/alternatemachine_browserecover_instantrestore.png)

11. I Utforskaren kopierar du filerna och mapparna från återställningsvolymen och klistrar in dem på måldatorns plats. Du kan öppna eller strömma filerna direkt från återställningsvolymen och kontrollera att rätt versioner har återställts.

    ![Skärmbild av Utforskaren i Windows med Kopiera markerat (återställ till en annan dator)](./media/backup-azure-restore-windows-server/alternatemachine_copy_instantrestore.png)

12. När du är klar går du till sidan **Bläddra och återställ** filer och väljer **Demontera**. Välj sedan **Ja** för att bekräfta att du vill demontera volymen.

    ![Demontera volymen (återställ till en annan dator)](./media/backup-azure-restore-windows-server/alternatemachine_unmount_instantrestore.png)

    > [!Important]
    > Om du inte väljer **Demontera förblir** återställningsvolymen monterad i 6 timmar från den tidpunkt då den monterades. Monteringstiden utökas dock upp till högst 24 timmar i händelse av en pågående filkopia. Inga säkerhetskopieringsåtgärder körs när volymen är monterad. Säkerhetskopieringsåtgärd som schemalagts att köras under den tid då volymen monteras körs när återställningsvolymen har demonterats.
    >

## <a name="next-steps"></a>Nästa steg

* Nu när du har återställt dina filer och mappar kan du [hantera dina säkerhetskopior.](backup-azure-manage-windows-server.md)

* Hitta [vanliga frågor om att eftersom filer och mappar ska orsakerna till att de inte är det.](backup-azure-file-folder-backup-faq.yml)
