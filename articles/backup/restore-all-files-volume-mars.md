---
title: Återställa alla filer i en volym med MARS
description: Lär dig hur du återställer alla filer i en volym med MARS-agenten.
ms.topic: conceptual
ms.date: 01/17/2021
ms.openlocfilehash: 44c12809fc94f78721ab1788cb352076dfebabe4
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98614423"
---
# <a name="restore-all-the-files-in-a-volume-using-the-mars-agent"></a>Återställa alla filer i en volym med MARS-agenten

Den här artikeln förklarar hur du återställer alla säkerhetskopierade filer på en hel volym med hjälp av guiden Återställ data i Microsoft Azure Recovery Services (MARS) Agent. Du kan:

- Återställ alla säkerhetskopierade filer på en volym till samma dator som säkerhets kopiorna gjordes från.
- Återställ alla säkerhetskopierade filer i en volym till en annan dator.

>[!TIP]
>Alternativet **volym** återställer alla säkerhetskopierade data på en angiven volym. Det här alternativet ger snabbare överföringshastighet (upp till 40 Mbit/s) och rekommenderas för att återskapa stora data eller hela volymer.
>
>**Alternativet enskilda filer och mappar** ger snabb åtkomst till återställnings punkt data. Det är lämpligt för att kunna återskapa enskilda filer och rekommenderas för en total storlek på mindre än 80 GB. Den erbjuder överförings-eller kopierings hastigheter upp till 6 MBps under återställningen.

## <a name="volume-level-restore-to-the-same-machine"></a>Återställ volym nivå till samma dator

Följande steg hjälper dig att återställa alla säkerhetskopierade filer på en volym:

1. Öppna snapin-modulen **Microsoft Azure Backup**. Om du inte vet var snapin-modulen har installerats söker du efter **Microsoft Azure Backup** i datorn eller servern. Skriv bords appen bör visas i Sök resultaten.

1. Välj **Återställ data** för att starta guiden.

    ![Menyn Återställ data](./media/restore-all-files-volume-mars/recover.png)

1. På sidan **komma igång** , för att återställa data till samma server eller dator, väljer du **den här servern (Server namn)**  >  **Nästa**.

    ![Sida för att komma igång](./media/restore-all-files-volume-mars/same-machine-instant-restore.png)

1. På sidan **Välj återställnings läge** väljer du **volym**  >  **Nästa**.

    ![Välj återställnings läge](./media/restore-all-files-volume-mars/select-recovery-mode.png)

1. På sidan **Välj volym och datum** väljer du den volym som du vill återställa.

    Välj en återställnings punkt i kalendern. Datum i **fetstil** anger tillgänglighet för minst en återställnings punkt. Om flera återställnings punkter är tillgängliga inom ett enda datum väljer du den aktuella återställnings punkten i list rutan **tid** .

     ![Välj volym och datum](./media/restore-all-files-volume-mars/select-volume-and-date.png)

1. Konfigurera återställnings beteendet på sidan **Ange återställnings alternativ** .
    1. Välj återställnings mål:
        - **Ursprunglig plats**: Återställ data till den ursprungliga sökvägen.
        - **En annan plats**: Ange en alternativ plats för att återställa data till.
    1. Välj beteende för **när objekt i säkerhets kopian redan finns på återställnings målet**:
        - **Skapa kopior så att du har båda versionerna**: om det redan finns en fil med samma namn kommer data i återställnings punkten att återställas som en kopia. Kopian får ett lokaliserat fil namns prefix med hjälp av den lokala återställnings jobb tiden i något av följande format:
            - `YYYY-MM-DD HH-mm Copy of <original file name>`
            - `YYYY-MM-DD HH-mm Copy (n) of <original file name>`
        - **Skriv över befintliga versioner med återställda versioner**: om det redan finns en fil med samma namn ersätts innehållet med data i återställnings punkten.
        - **Återställ inte de objekt som redan finns på återställnings målet**: om det redan finns en fil med samma namn hoppas den över.
    1. **Aktivera behörighet för återställning av åtkomst kontrol lista (ACL) till den fil eller mapp som återställs** om filen ska återställas med de ursprungliga behörigheterna i återställnings punkten.
        ![Ange återställnings alternativ](./media/restore-all-files-volume-mars/specify-recovery-options.png)

1. Kontrol lera informationen om återställningen i **bekräftelse** fönstret och välj **Återställ**.

    ![Bekräftelse information](./media/restore-all-files-volume-mars/confirmation-details.png)

1. På sidan **återställnings förlopp** övervakar du förloppet för återställnings jobbet. Guiden kan också stängas på ett säkert sätt, och återställnings åtgärden fortsätter i bakgrunden. Du kan visa förloppet igen genom att dubbelklicka på återställnings jobbet på instrument panelen.

## <a name="volume-level-restore-to-an-alternate-machine"></a>Återställa volym nivå till en annan dator

Följande steg hjälper dig att återställa alla säkerhetskopierade filer i en volym till en annan dator. Du kan använda de här stegen för att återställa data från Azure Backup om hela servern går förlorad.

Dessa steg omfattar följande terminologi:

- *Käll dator* – den ursprungliga datorn från vilken säkerhets kopian gjordes och som för tillfället inte är tillgänglig.
- *Måldator* – den dator som data återställs till.
- *Exempel valv* – det Recovery Services valv som käll datorn och mål datorn är registrerade på.

> [!NOTE]
> Säkerhets kopieringar kan inte återställas till en måldator som kör en tidigare version av operativ systemet. Till exempel kan en säkerhets kopia som tas från en dator med Windows 7 återställas på en dator med Windows 7 (eller senare). En säkerhets kopia som tagits från en Windows 10-dator kan inte återställas till en dator med Windows 7.

1. Öppna snapin-modulen **Microsoft Azure Backup** på mål datorn.

1. Se till att mål datorn och käll datorn är registrerade på samma Recovery Services-valv.

1. Välj **Återställ data** för att öppna **guiden Återställ data**.

    ![Skärm bild av Azure Backup, med återställda data markerade (Återställ till alternativ dator)](./media/backup-azure-restore-windows-server/recover.png)

1. På sidan **komma igång** väljer du **en annan server**.

    ![Skärm bild av guiden Återställ data Komma igång sidan (Återställ till en annan dator)](./media/backup-azure-restore-windows-server/alternatemachine_gettingstarted_instantrestore.png)

1. Ange den valv fil för valvet som motsvarar exempel valvet och välj **Nästa**.

    Om valvet för valvet är ogiltigt (eller har gått ut) [laddar du ned en ny fil för valvet från exempel valvet](backup-azure-file-folder-backup-faq.md#where-can-i-download-the-vault-credentials-file) i Azure Portal. När du har angett giltiga autentiseringsuppgifter för valvet visas namnet på motsvarande säkerhets kopierings valv.

1. På sidan **Välj säkerhets kopierings Server** väljer du käll datorn i listan med datorer som visas och anger lösen frasen. Välj sedan **Nästa**.

    ![Skärm bild av guiden Återställ data på sidan Välj säkerhets kopierings Server (Återställ till alternativ dator)](./media/backup-azure-restore-windows-server/alternatemachine_selectmachine_instantrestore.png)

1. På sidan **Välj återställnings läge** väljer du **volym**  >  **Nästa**.

    ![Välj återställnings läge](./media/restore-all-files-volume-mars/select-recovery-mode.png)

1. På sidan **Välj volym och datum** väljer du den volym som du vill återställa.

    Välj en återställnings punkt i kalendern. Datum i **fetstil** anger tillgänglighet för minst en återställnings punkt. Om flera återställnings punkter är tillgängliga inom ett enda datum väljer du den aktuella återställnings punkten i list rutan **tid** .

     ![Välj volym och datum](./media/restore-all-files-volume-mars/select-volume-and-date.png)

1. Konfigurera återställnings beteendet på sidan **Ange återställnings alternativ** .
    1. Välj återställnings mål:
        - **Ursprunglig plats**: Återställ data till den ursprungliga sökvägen.
        - **En annan plats**: Ange en alternativ plats för att återställa data till.
    1. Välj beteende för **när objekt i säkerhets kopian redan finns på återställnings målet**:
        - **Skapa kopior så att du har båda versionerna**: om det redan finns en fil med samma namn kommer data i återställnings punkten att återställas som en kopia. Kopian får ett lokaliserat fil namns prefix med hjälp av den lokala återställnings jobb tiden i något av följande format:
            - `YYYY-MM-DD HH-mm Copy of <original file name>`
            - `YYYY-MM-DD HH-mm Copy (n) of <original file name>`
        - **Skriv över befintliga versioner med återställda versioner**: om det redan finns en fil med samma namn ersätts innehållet med data i återställnings punkten.
        - **Återställ inte de objekt som redan finns på återställnings målet**: om det redan finns en fil med samma namn hoppas den över.
    1. **Aktivera behörighet för återställning av åtkomst kontrol lista (ACL) till den fil eller mapp som återställs** om filen ska återställas med de ursprungliga behörigheterna i återställnings punkten.
        ![Ange återställnings alternativ](./media/restore-all-files-volume-mars/specify-recovery-options.png)

1. Kontrol lera informationen om återställningen i **bekräftelse** fönstret och välj **Återställ**.

    ![Bekräftelse information](./media/restore-all-files-volume-mars/confirmation-details.png)

1. På sidan **återställnings förlopp** övervakar du förloppet för återställnings jobbet. Guiden kan också stängas på ett säkert sätt, och återställnings åtgärden fortsätter i bakgrunden. Du kan visa förloppet igen genom att dubbelklicka på återställnings jobbet på instrument panelen.

## <a name="next-steps"></a>Nästa steg

- Nu när du har återställt dina filer och mappar kan du [Hantera dina säkerhets kopior](backup-azure-manage-windows-server.md).
- Hitta [vanliga frågor om hur du säkerhetskopierar filer och mappar](backup-azure-file-folder-backup-faq.md).
