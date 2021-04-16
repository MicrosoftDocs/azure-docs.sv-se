---
title: Återställa alla filer på en volym med MARS
description: Lär dig hur du återställer alla filer på en volym med hjälp av MARS-agenten.
ms.topic: conceptual
ms.date: 01/17/2021
ms.openlocfilehash: 1d04e9f77b9f92594def9381f973c999e96b2cb2
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/15/2021
ms.locfileid: "107516509"
---
# <a name="restore-all-the-files-in-a-volume-using-the-mars-agent"></a>Återställa alla filer på en volym med HJÄLP av MARS-agenten

Den här artikeln beskriver hur du återställer alla säkerhetskopierade filer i en hel volym med hjälp av guiden Återställ data i MARS-agenten (Microsoft Azure Recovery Services). Du kan:

- Återställ alla säkerhetskopierade filer på en volym till samma dator som säkerhetskopiorna gjordes från.
- Återställ alla säkerhetskopierade filer i en volym till en annan dator.

>[!TIP]
>Alternativet **Volym** återställer alla säkerhetskopierade data på en angiven volym. Det här alternativet ger snabbare överföringshastigheter (upp till 40 MBps) och rekommenderas för återställning av stora data eller hela volymer.
>
>Alternativet **Enskilda filer och mappar ger** snabb åtkomst till återställningspunktdata. Det är lämpligt för att återställa enskilda filer och rekommenderas för en total storlek på mindre än 80 GB. Den erbjuder överförings- eller kopieringshastigheter på upp till 6 MBps under återställningen.

## <a name="volume-level-restore-to-the-same-machine"></a>Återställning på volymnivå till samma dator

Följande steg hjälper dig att återställa alla säkerhetskopierade filer på en volym:

1. Öppna snapin-modulen **Microsoft Azure Backup**. Om du inte vet var snapin-modulen installerades söker du i datorn eller servern efter **Microsoft Azure Backup**. Skrivbordsappen bör visas i sökresultaten.

1. Välj **Återställ data** för att starta guiden.

    ![Menyn Återställ data](./media/restore-all-files-volume-mars/recover.png)

1. På sidan **Komma igång** du återställa data till samma server eller dator genom att välja **Den här servern (servernamnet)**  >  **Nästa**.

    ![Sida för att komma igång](./media/restore-all-files-volume-mars/same-machine-instant-restore.png)

1. På sidan **Välj återställningsläge** väljer du **Volym**  >  **Nästa.**

    ![Välj återställningsläge](./media/restore-all-files-volume-mars/select-recovery-mode.png)

1. På sidan **Välj volym och** datum väljer du den volym som du vill återställa.

    Välj en återställningspunkt i kalendern. Datum i **fetstil** anger tillgängligheten för minst en återställningspunkt. Om flera återställningspunkter är tillgängliga inom ett enda datum väljer du den specifika återställningspunkten **i** listrutan Tid.

     ![Välj volym och datum](./media/restore-all-files-volume-mars/select-volume-and-date.png)

1. Konfigurera **återställningsbeteendet** på sidan Ange återställningsalternativ.
    1. Välj återställningsmål:
        - **Ursprunglig plats:** Återställ data till den ursprungliga sökvägen.
        - **En annan** plats: Ange en alternativ plats att återställa data till.
    1. Välj beteendet för **När objekt i säkerhetskopian redan finns i återställningsmålet:**
        - **Skapa kopior så att du** har båda versionerna: Om det redan finns en fil med samma namn återställs data i återställningspunkten som en kopia. Kopian har ett lokaliserat filnamnsprefix som använder jobbtiden för lokal återställning i något av följande format:
            - `YYYY-MM-DD HH-mm Copy of <original file name>`
            - `YYYY-MM-DD HH-mm Copy (n) of <original file name>`
        - **Skriva över befintliga versioner med återställda** versioner: Om det redan finns en fil med samma namn ersätts innehållet med data på återställningspunkten.
        - **Återställ inte de objekt som redan** finns på återställningsmålet: Om det redan finns en fil med samma namn hoppas den över.
    1. **Aktivera åtkomstkontrollista för återställning (ACL)** till filen eller mappen som återställs om filen ska återställas med de ursprungliga behörigheterna på återställningspunkten.
        ![Ange återställningsalternativ](./media/restore-all-files-volume-mars/specify-recovery-options.png)

1. Kontrollera informationen om återställningen i **bekräftelsefönstret** och välj **Återställ.**

    ![Bekräftelseinformation](./media/restore-all-files-volume-mars/confirmation-details.png)

1. På sidan **Återställningsförloppet** övervakar du förloppet för återställningsjobbet. Guiden kan också stängas på ett säkert sätt och återställningsåtgärden fortsätter i bakgrunden. Du kan visa förloppet igen genom att dubbelklicka på återställningsjobbet på instrumentpanelen.

## <a name="volume-level-restore-to-an-alternate-machine"></a>Återställning på volymnivå till en annan dator

Följande steg hjälper dig att återställa alla säkerhetskopierade filer i en volym till en annan dator. Du kan använda de här stegen för att återställa data Azure Backup om hela servern går förlorad.

De här stegen innehåller följande terminologi:

- *Källdator* – Den ursprungliga datorn som säkerhetskopian togs från och som för närvarande inte är tillgänglig.
- *Måldator* – Den dator som data återställs till.
- *Exempelvalv* – Recovery Services-valvet som källdatorn och måldatorn är registrerade på.

> [!NOTE]
> Säkerhetskopior kan inte återställas till en måldator som kör en tidigare version av operativsystemet. Till exempel kan en säkerhetskopia som tagits från en Windows 7-dator återställas på en Dator med Windows 7 (eller senare). En säkerhetskopia som tagits Windows 10 en dator kan inte återställas till en Windows 7-dator.

1. Öppna **Microsoft Azure Backup** snapin-modulen på måldatorn.

1. Kontrollera att måldatorn och källdatorn är registrerade i samma Recovery Services-valv.

1. Välj **Återställ data** för att öppna guiden Återställ **data.**

    ![Skärmbild av Azure Backup, med Återställ data markerat (återställ till en annan dator)](./media/backup-azure-restore-windows-server/recover.png)

1. På sidan **Komma igång** väljer du En **annan server**.

    ![Skärmbild av guiden Återställ data Komma igång (återställ till en annan dator)](./media/backup-azure-restore-windows-server/alternatemachine_gettingstarted_instantrestore.png)

1. Ange valvaudentialfilen som motsvarar exempelvalvet och välj **Nästa.**

    Om valvaudentialfilen är ogiltig (eller har upphört att gälla) laddar du ned en ny [valvaudentialfil](backup-azure-file-folder-backup-faq.yml#where-can-i-download-the-vault-credentials-file-) från exempelvalvet i Azure Portal. När du har anger en giltig valvaudential visas namnet på motsvarande säkerhetskopieringsvalv.

1. På sidan **Välj säkerhetskopieringsserver** väljer du källdatorn i listan över datorer som visas och anger lösenfrasen. Välj sedan **Nästa**.

    ![Skärmbild av sidan Återställ data i guiden Välj säkerhetskopieringsserver (återställ till en annan dator)](./media/backup-azure-restore-windows-server/alternatemachine_selectmachine_instantrestore.png)

1. På sidan **Välj återställningsläge** väljer du **Volym**  >  **nästa.**

    ![Välj återställningsläge](./media/restore-all-files-volume-mars/select-recovery-mode.png)

1. På sidan **Välj volym och** datum väljer du den volym som du vill återställa.

    Välj en återställningspunkt i kalendern. Datum i **fetstil** anger tillgängligheten för minst en återställningspunkt. Om flera återställningspunkter är tillgängliga inom ett enda datum väljer du den specifika återställningspunkten **i** listrutan Tid.

     ![Välj volym och datum](./media/restore-all-files-volume-mars/select-volume-and-date.png)

1. Konfigurera **återställningsbeteendet** på sidan Ange återställningsalternativ.
    1. Välj återställningsmål:
        - **Ursprunglig plats:** Återställ data till den ursprungliga sökvägen.
        - **En annan** plats: Ange en alternativ plats att återställa data till.
    1. Välj beteendet för **När objekt i säkerhetskopian redan finns i återställningsmålet:**
        - **Skapa kopior så att du** har båda versionerna: Om det redan finns en fil med samma namn återställs data i återställningspunkten som en kopia. Kopian har ett lokaliserat filnamnsprefix som använder jobbtiden för lokal återställning i något av följande format:
            - `YYYY-MM-DD HH-mm Copy of <original file name>`
            - `YYYY-MM-DD HH-mm Copy (n) of <original file name>`
        - **Skriva över befintliga versioner med återställda** versioner: Om det redan finns en fil med samma namn ersätts innehållet med data på återställningspunkten.
        - **Återställ inte de objekt som redan** finns på återställningsmålet: Om det redan finns en fil med samma namn hoppas den över.
    1. **Aktivera Behörigheter för åtkomstkontrollista för återställning (ACL)** till den fil eller mapp som återställs om filen ska återställas med de ursprungliga behörigheterna på återställningspunkten.
        ![Ange återställningsalternativ](./media/restore-all-files-volume-mars/specify-recovery-options.png)

1. Kontrollera informationen om återställningen i **bekräftelsefönstret** och välj **Återställ.**

    ![Bekräftelseinformation](./media/restore-all-files-volume-mars/confirmation-details.png)

1. På sidan **Återställningsförloppet** övervakar du förloppet för återställningsjobbet. Guiden kan också stängas på ett säkert sätt och återställningen fortsätter i bakgrunden. Du kan visa förloppet igen genom att dubbelklicka på återställningsjobbet på instrumentpanelen.

## <a name="next-steps"></a>Nästa steg

- Nu när du har återställt dina filer och mappar kan du [hantera dina säkerhetskopior.](backup-azure-manage-windows-server.md)
- Hitta [vanliga frågor om att orsaker till att filer och mappar ska orsakerna till att de inte är det.](backup-azure-file-folder-backup-faq.yml)
