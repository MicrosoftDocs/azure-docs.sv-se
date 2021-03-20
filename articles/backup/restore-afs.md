---
title: Återställa Azure-filresurser
description: Lär dig hur du använder Azure Portal för att återställa en hel fil resurs eller vissa filer från en återställnings punkt som skapats av Azure Backup.
ms.topic: conceptual
ms.date: 01/12/2020
ms.openlocfilehash: 35ca0936ae6efba716adb51f43326cdd5bfa2d98
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "89376782"
---
# <a name="restore-azure-file-shares"></a>Återställa Azure-filresurser

Den här artikeln förklarar hur du använder Azure Portal för att återställa en hel fil resurs eller vissa filer från en återställnings punkt som skapats av [Azure Backup](./backup-overview.md).

I den här artikeln får du lära dig att:

* Återställa en fullständig Azure-filresurs.
* Återställa enskilda filer eller mappar.
* Spåra återställnings åtgärdens status.

## <a name="steps-to-perform-a-restore-operation"></a>Steg för att utföra en återställnings åtgärd

Följ dessa steg om du vill utföra en återställnings åtgärd.

### <a name="select-the-file-share-to-restore"></a>Välj den fil resurs som ska återställas

1. I [Azure Portal](https://portal.azure.com/)öppnar du Recovery Services-valvet som du använde för att konfigurera säkerhets kopieringen för fil resursen.

1. I fönstret Översikt väljer du **säkerhets kopierings objekt** under avsnittet **skyddade objekt** .

    ![Välj säkerhets kopierings objekt](./media/restore-afs/backup-items.png)

1. När du har valt **säkerhets kopierings objekt** öppnas ett nytt fönster som visar alla typer av säkerhets kopierings hantering bredvid översikts fönstret.

    ![Typer av säkerhets kopierings hantering](./media/restore-afs/backup-management.png)

1. I rutan **säkerhets kopierings objekt** under **säkerhets kopierings hanterings typ** väljer du **Azure Storage (Azure Files)**. Du ser en lista över alla fil resurser och deras motsvarande lagrings konton som har säkerhetskopierats med hjälp av det här valvet.

    ![Lista över alla fil resurser](./media/restore-afs/file-shares.png)

1. I listan över Azure-filresurser väljer du den fil resurs som du vill utföra återställnings åtgärden för.

### <a name="full-share-recovery"></a>Fullständig delnings återställning

Du kan använda det här alternativet för återställning för att återställa hela fil resursen på den ursprungliga platsen eller en annan plats.

1. Välj alternativet för att **återställa delning** i rutan **säkerhets kopierings objekt** som visas när du har valt fil resursen att återställa i steg 5 i avsnittet [Välj den fil resurs som ska återställas](#select-the-file-share-to-restore) .

   ![Välj återställnings resurs](./media/restore-afs/restore-share.png)

1. När du har valt **återställnings resurs** öppnas fönstret **Återställ** . Välj den återställnings punkt som du vill använda för att utföra återställnings åtgärden genom att välja text rutan **Välj** länk under text rutan **återställnings punkt** .

    ![Välj återställnings punkt genom att välja Välj](./media/restore-afs/select-restore-point.png)

1. Rutan **Välj återställnings punkt** kontext öppnas till höger och visar de återställnings punkter som är tillgängliga för den valda fil resursen. Välj den återställnings punkt som du vill använda för att utföra återställnings åtgärden och välj **OK**.

    ![Välj återställnings punkt](./media/restore-afs/restore-point.png)

    >[!NOTE]
    >Som standard listar fönstret **Välj återställnings punkt** återställnings punkter från de senaste 30 dagarna. Om du vill titta på de återställnings punkter som skapas under en angiven varaktighet anger du intervallet genom att välja lämplig **Start tid** och **slut tid** och välja knappen **Uppdatera** .

1. Nästa steg är att välja **återställnings plats**. I avsnittet **återställnings mål** anger du var eller hur du vill återställa data. Välj något av följande två alternativ med hjälp av växlings knappen:

    * **Ursprunglig plats**: Återställ hela fil resursen till samma plats som den ursprungliga källan.
    * **Alternativ plats**: Återställ hela fil resursen till en annan plats och behåll den ursprungliga fil resursen som den är.

#### <a name="restore-to-the-original-location-full-share-recovery"></a>Återställa till den ursprungliga platsen (fullständig delning)

1. Välj den **ursprungliga platsen** som **återställnings mål** och välj om du vill hoppa över eller skriva över om det finns konflikter, genom att välja lämpligt alternativ i list rutan **i händelse av konflikter** .

1. Starta återställnings åtgärden genom att välja **Återställ** .

    ![Välj Återställ för att starta](./media/restore-afs/click-restore.png)

#### <a name="restore-to-an-alternate-location-full-share-recovery"></a>Återställa till en alternativ plats (fullständig delnings återställning)

1. Välj en **annan plats** som **återställnings mål**.
1. Välj det mål lagrings konto där du vill återställa det säkerhetskopierade innehållet i list rutan **lagrings konto** .
1. List rutan **Välj fil resurs** visar de fil resurser som finns i det lagrings konto som du valde i steg 2. Välj den fil resurs där du vill återställa det säkerhetskopierade innehållet.
1. I rutan **mappnamn** anger du namnet på mappen som du vill skapa i mål fil resursen med det återställda innehållet.
1. Välj om du vill hoppa över eller skriva över om det finns konflikter.
1. När du har angett lämpliga värden i alla rutor väljer du **Återställ** för att starta återställnings åtgärden.

    ![Välj alternativ plats](./media/restore-afs/alternate-location.png)

### <a name="item-level-recovery"></a>Återställning på objektnivå

Du kan använda det här alternativet för återställning om du vill återställa enskilda filer eller mappar på den ursprungliga platsen eller på en annan plats.

1. Välj alternativet **fil återställning** i rutan **säkerhets kopierings objekt** som visas när du har valt fil resursen att återställa i steg 5 i avsnittet [Välj den fil resurs som ska återställas](#select-the-file-share-to-restore) .

    ![Välj fil återställning](./media/restore-afs/file-recovery.png)

1. När du har valt **fil återställning** öppnas fönstret **Återställ** . Välj den återställnings punkt som du vill använda för att utföra återställnings åtgärden genom att markera text rutan **Välj** länk under text rutan **återställnings punkt** .

    ![Välj återställnings punkt genom att välja länken Välj](./media/restore-afs/select-restore-point.png)

1. Rutan **Välj återställnings punkt** kontext öppnas till höger och visar de återställnings punkter som är tillgängliga för den valda fil resursen. Välj den återställnings punkt som du vill använda för att utföra återställnings åtgärden och välj **OK**.

    ![Välj återställnings punkt](./media/restore-afs/restore-point.png)

1. Nästa steg är att välja **återställnings plats**. I avsnittet **återställnings mål** anger du var eller hur du vill återställa data. Välj något av följande två alternativ med hjälp av växlings knappen:

    * **Ursprunglig plats**: Återställ valda filer eller mappar till samma fil resurs som den ursprungliga källan.
    * **Alternativ plats**: Återställ valda filer eller mappar till en annan plats och behåll det ursprungliga fil resurs innehållet.

#### <a name="restore-to-the-original-location-item-level-recovery"></a>Återställ till den ursprungliga platsen (återställning på objekt nivå)

1. Välj den **ursprungliga platsen** som **återställnings mål** och välj om du vill hoppa över eller skriva över om det finns konflikter genom att välja lämpligt alternativ i list rutan **i händelse av konflikter** .

    ![Ursprunglig plats för återställning på objekt nivå](./media/restore-afs/original-location-item-level.png)

1. Välj de filer eller mappar som du vill återställa genom att välja knappen **Lägg till fil** . Då öppnas ett kontext fönster till höger som visar innehållet i fil resursens återställnings punkt som du har valt för återställning.

    ![Välj Lägg till fil](./media/restore-afs/add-file.png)

1. Markera kryss rutan som motsvarar den fil eller mapp som du vill återställa och välj **Välj**.

    ![Välj fil eller mapp](./media/restore-afs/select-file-folder.png)

1. Upprepa steg 2 till 4 för att välja flera filer eller mappar som ska återställas.
1. När du har valt alla objekt som du vill återställa, väljer du **Återställ** för att starta återställningen.

    ![Välj Återställ för att starta](./media/restore-afs/click-restore.png)

#### <a name="restore-to-an-alternate-location-item-level-recovery"></a>Återställa till en alternativ plats (återställning på objekt nivå)

1. Välj en **annan plats** som **återställnings mål**.
1. Välj det mål lagrings konto där du vill återställa det säkerhetskopierade innehållet i list rutan **lagrings konto** .
1. List rutan **Välj fil resurs** visar de fil resurser som finns i det lagrings konto som du valde i steg 2. Välj den fil resurs där du vill återställa det säkerhetskopierade innehållet.
1. I rutan **mappnamn** anger du namnet på mappen som du vill skapa i mål fil resursen med det återställda innehållet.
1. Välj om du vill hoppa över eller skriva över om det finns konflikter.
1. Välj de filer eller mappar som du vill återställa genom att välja knappen **Lägg till fil** . Då öppnas ett kontext fönster till höger som visar innehållet i fil resursens återställnings punkt som du har valt för återställning.

    ![Välj objekt som ska återställas till en annan plats](./media/restore-afs/restore-to-alternate-location.png)

1. Markera kryss rutan som motsvarar den fil eller mapp som du vill återställa och välj **Välj**.

    ![Välj återställnings mål](./media/restore-afs/recovery-destination.png)

1. Upprepa steg 6 till 8 för att välja flera filer eller mappar som ska återställas.
1. När du har valt alla objekt som du vill återställa, väljer du **Återställ** för att starta återställningen.

    ![Välj OK när du har valt alla filer](./media/restore-afs/after-selecting-all-items.png)

## <a name="track-a-restore-operation"></a>Spåra en återställnings åtgärd

När du har utlöst återställnings åtgärden skapar säkerhets kopierings tjänsten ett jobb för spårning. Azure Backup visar meddelanden om jobbet i portalen. Om du vill visa åtgärder för jobbet väljer du hyperlänken meddelanden.

![Välj hyperlänk för meddelanden](./media/restore-afs/notifications-link.png)

Du kan också övervaka återställnings förloppet från Recovery Services-valvet:

1. Öppna det Recovery Services valv från vilket du utlöste återställnings åtgärden.
1. I fönstret Översikt väljer du **säkerhets kopierings jobb** under avsnittet **övervakning** för att se statusen för åtgärder som körs mot olika arbets belastningar.

    ![Välj säkerhets kopierings jobb](./media/restore-afs/backup-jobs.png)

1. Välj det arbets belastnings namn som motsvarar din fil resurs om du vill visa mer information om återställnings åtgärden, t. ex. **överförda data** och **antalet återställda filer**.

    ![Se återställd information](./media/restore-afs/restore-details.png)

## <a name="next-steps"></a>Nästa steg

* Lär dig hur du [hanterar säkerhets kopior av Azure-filresurser](manage-afs-backup.md).
