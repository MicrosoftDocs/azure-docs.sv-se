---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 48ce594fa5f4b736e69b5b4ef7a10011fe8031fa
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96027819"
---
#### <a name="to-install-updates-via-the-azure-portal"></a>Installera uppdateringar via Azure Portal

1. Gå till din StorSimple-enhetshanterare och välj **Enheter**. Välj i listan över enheter som är anslutna till tjänsten och klicka på den enhet som du vill uppdatera. 

    ![I senaste är Enhetshanteraren MySSDevManager markerat och markerat, enheterna är markerade och markerade, och enhets MYSSIS1103 markeras och väljs.](../includes/media/storsimple-virtual-array-install-update-via-portal-04/azupdate1m.png) 

2. I bladet **Inställningar** klickar du på **Enhetsuppdateringar**. 

    ![Information om MYSSIS1103 visas. I inställningar markeras enhets uppdateringar.](../includes/media/storsimple-virtual-array-install-update-via-portal-04/azupdate2m.png)  

3. Du ser ett meddelande om programuppdateringar är tillgängliga. Du kan också klicka på **Scan** (Sök).

    ![I fönstret enhets uppdateringar står det "nya uppdateringar är tillgängliga" och "senast skannad/18/01/2017 2:42 PM/program varu version/10.0.10288.0". Versionen är markerad.](../includes/media/storsimple-virtual-array-install-update-via-portal-04/azupdate3m1.png)

    Du meddelas när genomsökningen startar och slutförs.

    ![Meddelandet "söker efter uppdateringar för enhets MySSIS1103. 2:12 PM/har slutfört åtgärden. "](../includes/media/storsimple-virtual-array-install-update-via-portal-04/azupdate5m.png)

4. När uppdateringarna har genomsökts klickar du på alternativet för att **hämta uppdateringar**. 

    ![I fönstret enhets uppdateringar finns meddelandet "nya uppdateringar är tillgängliga". Knappen Hämta uppdateringar är markerad.](../includes/media/storsimple-virtual-array-install-update-via-portal-04/azupdate6m.png)

5. På bladet **Nya uppdateringar** går du igenom informationen som kommer när uppdateringarna har hämtats, och du måste bekräfta installationen. Klicka på **OK**.

    ![I fönstret nya uppdateringar visas "när uppdateringarna har hämtats måste du bekräfta installationen". Knappen OK är markerad.](../includes/media/storsimple-virtual-array-install-update-via-portal-04/azupdate7m.png)

6. Du meddelas när överföringen startar och slutförs.

     ![Meddelandet "laddar ned uppdateringar för enhet MySSIS1... 2:13 PM ".](../includes/media/storsimple-virtual-array-install-update-via-portal-04/azupdate8m.png)

5. På bladet **Enhetsuppdateringar** klickar du på **Installera**.

     ![Enhets uppdateringar säger "bekräfta installation av uppdateringar". Det finns en installations knapp.](../includes/media/storsimple-virtual-array-install-update-via-portal-04/azupdate11m1.png)   

6. På bladet **Nya uppdateringar** får du en varning om att uppdateringen är störande. En virtuell matris är en enhet med en enda nod. Enheten startas om när den har uppdaterats. Detta avbryter pågående IO. Klicka på **OK** för att installera uppdateringarna. 

    ![Meddelandet i fönstret nya uppdateringar är "enheten kommer att ha avbrott när uppdateringarna installeras". Knappen OK är markerad.](../includes/media/storsimple-virtual-array-install-update-via-portal-04/azupdate12m.png) 

7. Du får ett meddelande när installationen startar. 

    ![Meddelandet "installerar uppdateringar för enhet MySSIS1103. 2:15 PM ".](../includes/media/storsimple-virtual-array-install-update-via-portal-04/azupdate13m.png)

8.  När installationen är klar klickar du på länken **Visa jobb** på bladet **Enhetsuppdateringar** för att övervaka installationen. 

    ![I fönstret enhets uppdateringar finns en länk med etiketten "installerar uppdateringar". Visa jobb ".](../includes/media/storsimple-virtual-array-install-update-via-portal-04/azupdate15m1.png)

    Du kommer då till bladet **Installera uppdateringar**. Du kan visa detaljerad information om jobbet här.

    ![I fönstret installations uppdateringar finns installations information, inklusive enhet, status, varaktighet, start tid och stopp tid.](../includes/media/storsimple-virtual-array-install-update-via-portal-04/azupdate16m1.png)

9. När uppdateringarna har installerats ser du ett meddelande om detta på bladet **Enhetsuppdateringar**. 
