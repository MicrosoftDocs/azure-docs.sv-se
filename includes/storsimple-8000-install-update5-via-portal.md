---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 9832a961562930e61e94b28ed78976fd9f8c6f78
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "93376020"
---
#### <a name="to-install-an-update-from-the-azure-portal"></a>Installera en uppdatering från Azure Portal

1. Välj enheten på tjänstesidan StorSimple.

    ![Välj enhet](./media/storsimple-8000-install-update5-via-portal/update1.png)

2. Gå till **enhets inställningar**  >  **enhets uppdateringar**.

    ![Klicka på enhets uppdateringar](./media/storsimple-8000-install-update5-via-portal/update2.png)

2. Ett meddelande visas om nya uppdateringar är tillgängliga. Alternativt klickar du på **Sök efter uppdateringar** på bladet **enhets uppdateringar** . Det skapas ett jobb för att söka efter tillgängliga uppdateringar. Du meddelas när jobbet har slutförts.

    ![Klicka på enhets uppdateringar 2](./media/storsimple-8000-install-update5-via-portal/update3.png)

3. Vi rekommenderar att du läser den viktiga informationen innan du installerar en uppdatering på enheten. Klicka på **Installera uppdateringar** om du vill tillämpa uppdateringar. På bladet **Bekräfta vanliga uppdateringar** kontrollerar du att kraven är uppfyllda innan du installerar uppdateringarna. Markera kryss rutan för att ange att du är redo att uppdatera enheten och klicka sedan på **Installera**.

    ![Klicka på enhets uppdateringar 3](./media/storsimple-8000-install-update5-via-portal/update4.png)

6. En uppsättning nödvändiga kontrollerar startar. Dessa kontroller omfattar följande:
   
   * **Hälsokontroller av styrenheten** för att verifiera att båda styrenheterna är felfria och online.
   * **Hälsokontroller för maskinvarukomponenter** för att verifiera att alla maskinvarukomponenter på StorSimple-enheten är felfria.
   * **DATA 0-kontroller** för att verifiera att DATA 0 är aktiverat på enheten. Om inte det här gränssnittet är aktiverat måste du aktivera det och sedan försöka igen.

     Uppdateringen laddas ned och installeras endast om alla kontroller har slutförts. Du får ett meddelande när kontrollerna pågår. Om för kontrollen Miss lyckas får du orsaken till felet. Åtgärda problemen och försök sedan igen. Du kan behöva kontakta Microsofts support om du inte kan lösa dessa problem själv.

7. När dina för-kontroller har slutförts skapas ett uppdaterings jobb. Du får ett meddelande när uppdateringsjobbet har skapats.
   
    ![Uppdatera skapande av jobb](./media/storsimple-8000-install-update5-via-portal/update6.png)
   
    Uppdateringen tillämpas sedan på enheten.

9. Uppdateringen tar några timmar att slutföra. Markera uppdateringsjobbet och klicka på **Information** så kan du visa information om jobbet när som helst.

    ![Skapa jobb för att uppdatera jobb 2](./media/storsimple-8000-install-update5-via-portal/update8.png)

     Du kan också övervaka förloppet för uppdaterings jobbet från **enhets inställningar > jobb**. På bladet **jobb** kan du se uppdaterings förloppet.

     ![Uppdatera jobb, skapa 3](./media/storsimple-8000-install-update5-via-portal/update7.png)

10. När jobbet är klart navigerar du till **enhets inställningarna > enhets uppdateringar**. Program versionen bör nu uppdateras.

