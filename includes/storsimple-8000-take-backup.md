---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 5e923fdf560692c645c8a69e7e26d13f69d6920c
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "93375945"
---
### <a name="to-take-a-backup"></a>Gör en säkerhetskopia

1. Gå till StorSimple Device Manager-tjänsten. Markera och klicka på din enhet i tabellistan med enheter och klicka sedan på **Alla inställningar**. Gå till **Inställningar > Hantera > Säkerhetskopieringspolicy** på bladet **Inställningar**.

    ![Lägg till princip för säkerhetskopiering](./media/storsimple-8000-take-backup/step8takebu1.png)

2. Klicka på **+ Lägg till princip** på bladet **Säkerhetskopieringspolicy**.

    ![Lägg till säkerhets kopiering – princip 2](./media/storsimple-8000-take-backup/step8takebu2.png)

3. På bladet **Skapa säkerhetskopieringspolicy** anger du ett namn som innehåller mellan 3 och 150 tecken för säkerhetskopieringspolicyn.

4. Välj de volymer som ska säkerhetskopieras. Om du väljer mer än en volym grupperas volymerna för att skapa en kraschkonsekvent säkerhetskopia.

    ![Lägg till säkerhets kopiering – princip 3](./media/storsimple-8000-take-backup/step8takebu4.png)

5. På bladet **Lägg till första schema**:

    1. Välj typen av säkerhetskopiering. Välj **lokal** ögonblicks bild för snabbare återställningar. För data återhämtning väljer du **moln** ögonblicks bilder.
    2. Ange frekvensen för säkerhetskopiering i minuter, timmar, dagar eller veckor.
    3. Välj en kvarhållningstid. Kvarhållningsalternativen beror på frekvensen för säkerhetskopiering. För en daglig princip, kan kvarhållning anges i veckor, medan kvarhållningen för en månatlig princip är i månader.
    4. Välj starttiden och datum för principen för säkerhetskopiering.
    5. Skapa säkerhetskopieringspolicyn genom att klicka på **OK**.

        ![Lägg till säkerhets kopiering – princip 4](./media/storsimple-8000-take-backup/step8takebu5.png) 

6. Starta genereringen av säkerhetskopieringspolicyn genom att klicka på **Skapa**. Du får ett meddelande när säkerhetskopieringspolicyn har skapats. Listan över säkerhetskopieringspolicyer uppdateras också.
      
      ![Lägg till säkerhets kopiering – princip 5](./media/storsimple-8000-take-backup/step8takebu9.png)
      
      Nu har du en säkerhetskopieringspolicy som skapar schemalagda säkerhetskopieringar av din volymdata.




