---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: df572b88cf8a67163b28ec87154dcea01646b9a2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "95563035"
---
#### <a name="to-install-updates-via-the-azure-portal"></a>Installera uppdateringar via Azure Portal

1. Gå till din StorSimple-enhetshanterare och välj **Enheter**. Välj i listan över enheter som är anslutna till tjänsten och klicka på den enhet som du vill uppdatera. 

    ![I senaste är Enhetshanteraren MySSDevManager markerat och markerat, enheterna är markerade och markerade, och enhets MYSSIS1103 markeras och väljs.](../includes/media/storsimple-virtual-array-install-update-via-portal/azupdate1m.png) 

2. I bladet **Inställningar** klickar du på **Enhetsuppdateringar**. 

    ![Information för MYSSIS1103 visas. I inställningar markeras enhets uppdateringar.](../includes/media/storsimple-virtual-array-install-update-via-portal/azupdate2m.png)  

3. Du ser ett meddelande om programuppdateringar är tillgängliga. Du kan också klicka på **Scan** (Sök).

    ![I fönstret enhets uppdateringar är knappen Skanna markerad. Det finns ett meddelande på fyra rader: senast skannad/inte skannad/program varu version/10.0.10280.0.](../includes/media/storsimple-virtual-array-install-update-via-portal/azupdate3m.png)

    Du meddelas när genomsökningen startar och slutförs.

    ![Meddelandet "söker efter uppdateringar för enheten MySSIS 1103.2:12 PM/har slutfört åtgärden".](../includes/media/storsimple-virtual-array-install-update-via-portal/azupdate5m.png)

4. När uppdateringarna har genomsökts klickar du på alternativet för att **hämta uppdateringar**. 

    ![I fönstret enhets uppdateringar står det "nya uppdateringar är tillgängliga" och "senast skannad/11/3/2016 2:12 PM/program varu version/10.0.10280.0". Versionen är markerad.](../includes/media/storsimple-virtual-array-install-update-via-portal/azupdate6m.png)

5. På bladet **Nya uppdateringar** går du igenom informationen som kommer när uppdateringarna har hämtats, och du måste bekräfta installationen. Klicka på **OK**.

    ![I fönstret nya uppdateringar visas "när uppdateringarna har hämtats måste du bekräfta installationen". Knappen OK är markerad.](../includes/media/storsimple-virtual-array-install-update-via-portal/azupdate7m.png)

6. Du meddelas när överföringen startar och slutförs.

     ![Meddelandet "laddar ned uppdateringar för enhet MySSIS... 2:13 PM ".](../includes/media/storsimple-virtual-array-install-update-via-portal/azupdate8m.png)

5. På bladet **Enhetsuppdateringar** klickar du på **Installera**.

     ![I fönstret enhets uppdateringar visas meddelandet "bekräfta installation av uppdateringar". Knappen Installera är markerad.](../includes/media/storsimple-virtual-array-install-update-via-portal/azupdate11m.png)   

6. På bladet **Nya uppdateringar** får du en varning om att uppdateringen är störande. En virtuell matris är en enhet med en enda nod. Enheten startas om när den har uppdaterats. Detta avbryter pågående IO. Klicka på **OK** för att installera uppdateringarna. 

    ![Meddelandet i fönstret nya uppdateringar är "enheten kommer att ha avbrott när uppdateringarna installeras". Knappen OK är markerad.](../includes/media/storsimple-virtual-array-install-update-via-portal/azupdate12m.png) 

7. Du får ett meddelande när installationen startar. 

    ![Meddelandet "installerar uppdateringar för enhet MYSSIS1103". 2:15 PM.](../includes/media/storsimple-virtual-array-install-update-via-portal/azupdate13m.png)

8.  När installationen är klar klickar du på länken **Visa jobb** på bladet **Enhetsuppdateringar** för att övervaka installationen. 

    ![I fönstret enhets uppdateringar är länken "installerar uppdateringar". Visa jobb "är markerat.](../includes/media/storsimple-virtual-array-install-update-via-portal/azupdate15m.png)

    Du kommer då till bladet **Installera uppdateringar**. Du kan visa detaljerad information om jobbet här.

    ![I fönstret installations uppdateringar finns installations information, inklusive enhet, status, varaktighet, start tid och stopp tid.](../includes/media/storsimple-virtual-array-install-update-via-portal/azupdate16m.png)

9. När uppdateringarna har installerats ser du ett meddelande om detta på bladet Enhetsuppdateringar. Program versionen ändras också till **10.0.10288.0**. 

    ![I fönstret enhets uppdateringar visas "enheten är uppdaterad" och innehåller program varu versionen (10.0.10288.0).](../includes/media/storsimple-virtual-array-install-update-via-portal/azupdate17m.png)
