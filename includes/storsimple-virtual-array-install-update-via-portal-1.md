---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 3129bbe171329ecf37f8712394cedf9b70188220
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96005832"
---
#### <a name="to-install-updates-via-the-azure-portal"></a>Installera uppdateringar via Azure Portal

1. Gå till din StorSimple-enhetshanterare och välj **Enheter**. Välj i listan över enheter som är anslutna till tjänsten och klicka på den enhet som du vill uppdatera.

    ![I senaste är Enhetshanteraren MySSDevManager markerat och markerat, enheterna är markerade och markerade, och enhets MYSSIS1103 markeras och väljs.](../includes/media/storsimple-virtual-array-install-update-via-portal-04/azupdate1m.png) 

2. I bladet **Inställningar** klickar du på **Enhetsuppdateringar**.

    ![Information om MYSSIS1103 visas. I inställningar markeras enhets uppdateringar.](../includes/media/storsimple-virtual-array-install-update-via-portal-04/azupdate2m.png)  

3. Du ser ett meddelande om programuppdateringar är tillgängliga. Du kan också klicka på **Scan** (Sök). Anteckna den program varu version som du kör. 

    ![I fönstret enhets uppdateringar står det "nya uppdateringar är tillgängliga" och "senast skannade/11/01/2017 10:56 AM/program version/10.0.10289.0". Versionen är markerad.](../includes/media/storsimple-virtual-array-install-update-via-portal-1/azupdate3m1.png)

    Du får ett meddelande när genomsökningen startar och slutförs.

    ![Meddelandet "söker efter uppdateringar för enhetens MySVAFS110.... 10:57 är en pågående åtgärd. "](../includes/media/storsimple-virtual-array-install-update-via-portal-1/azupdate5m.png)

4. När uppdateringarna har genomsökts klickar du på alternativet för att **hämta uppdateringar**.

    ![I fönstret enhets uppdateringar finns meddelandet "nya uppdateringar är tillgängliga". Knappen Hämta uppdateringar är markerad.](../includes/media/storsimple-virtual-array-install-update-via-portal-1/azupdate6m.png)

5. På bladet **nya uppdateringar** granskar du viktig information. Observera också att när uppdateringarna har hämtats måste du bekräfta installationen. Klicka på **OK**.

    ![I fönstret nya uppdateringar visas "när uppdateringarna har hämtats måste du bekräfta installationen". Knappen OK är markerad.](../includes/media/storsimple-virtual-array-install-update-via-portal-1/azupdate7m.png)

6. Du meddelas när överföringen startar och slutförs.

     ![Meddelandet "laddar ned uppdateringar för enhet MySVAFS... 11:07 AM ".](../includes/media/storsimple-virtual-array-install-update-via-portal-1/azupdate8m.png)

5. På bladet **Enhetsuppdateringar** klickar du på **Installera**.

     ![Enhets uppdateringar säger "bekräfta installation av uppdateringar". Knappen Installera är markerad.](../includes/media/storsimple-virtual-array-install-update-via-portal-1/azupdate11m1.png)

6. På bladet **Nya uppdateringar** får du en varning om att uppdateringen är störande. En virtuell matris är en enhet med en enda nod. Enheten startas om när den har uppdaterats. Detta avbryter pågående IO. Klicka på **OK** för att installera uppdateringarna.

    ![Meddelandet i fönstret nya uppdateringar är "enheten kommer att ha avbrott när uppdateringarna installeras". Knappen OK är markerad.](../includes/media/storsimple-virtual-array-install-update-via-portal-1/azupdate12m.png)

7. Du får ett meddelande när installationen startar.

    ![Meddelandet "installerar uppdateringar för enhet MySVAFS110.... 11:09 AM ".](../includes/media/storsimple-virtual-array-install-update-via-portal-1/azupdate13m.png)

8.  När installationen är klar klickar du på länken **Visa jobb** på bladet **Enhetsuppdateringar** för att övervaka installationen. 

    ![I fönstret enhets uppdateringar finns en länk med etiketten "installerar uppdateringar". Visa jobb ". Knappen Installera är markerad.](../includes/media/storsimple-virtual-array-install-update-via-portal-1/azupdate15m1.png)

    Den här åtgärden tar dig till bladet **Installera uppdateringar** . Du kan visa detaljerad information om jobbet här.

    ![I fönstret installations uppdateringar finns installations information, inklusive enhet, status, varaktighet, start tid och stopp tid.](../includes/media/storsimple-virtual-array-install-update-via-portal-1/azupdate16m1.png)

9. Om du startade med en virtuell matris som kör program varu versionen uppdatering 0,6 (10.0.10293.0) kör du nu uppdatering 1 och är färdig. Du kan hoppa över de återstående stegen. Om du startade med en virtuell matris som kör en program varu version före uppdateringen 0,6 (10.0.10293.0) har du nu uppdaterat uppdateringen 0,6. Ett annat meddelande visas som anger att uppdateringar är tillgängliga. Upprepa steg 4-8 för att installera uppdatering 1.

    ![I fönstret enhets uppdateringar står det "nya uppdateringar är tillgängliga" och "senast skannade/11/1/2017 10:56 AM/program version/10.0.10293.0".](../includes/media/storsimple-virtual-array-install-update-via-portal-1/azupdate17.png)

