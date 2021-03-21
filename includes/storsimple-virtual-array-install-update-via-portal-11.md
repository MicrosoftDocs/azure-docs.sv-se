---
title: ta med fil
description: ta med fil
services: storsimple
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 07/18/2018
ms.author: alkohli
ms.custom: include file
ms.openlocfilehash: 8ef8b9d3350d0599ab00dfdbb018cf8dd900e316
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "95562474"
---
#### <a name="to-install-updates-via-the-azure-portal"></a>Installera uppdateringar via Azure Portal

1. Gå till din StorSimple-enhetshanterare och välj **Enheter**. Välj i listan över enheter som är anslutna till tjänsten och klicka på den enhet som du vill uppdatera.

2. Under **Inställningar** klickar du på **enhets uppdateringar**.  

3. Du ser ett meddelande om programuppdateringar är tillgängliga. Du kan också klicka på **Scan** (Sök). Anteckna den program varu version som du kör. 

    ![I fönstret enhets uppdateringar står det "nya uppdateringar är tillgängliga" (markerat) och "senast skannad/6/22/2018 2:46 PM/program varu version/10.0.10296.0". Versionen är markerad.](../includes/media/storsimple-virtual-array-install-update-via-portal-11/azupdate3m1.png)

    Du får ett meddelande när genomsökningen startar och slutförs.
 
4. När uppdateringarna har genomsökts klickar du på alternativet för att **hämta uppdateringar**. Granska viktig information under **nya uppdateringar**. Observera också att när uppdateringarna har hämtats måste du bekräfta installationen. Klicka på **OK**.

    ![I enhets uppdateringar är knappen Hämta uppdateringar markerad. I nya uppdateringar finns det en länk till viktig information och ett meddelande om att bekräfta installationen när uppdateringarna har hämtats. Det finns en OK-knapp.](../includes/media/storsimple-virtual-array-install-update-via-portal-11/azupdate6m.png)

    Du meddelas när överföringen startar och slutförs.

5. Under **enhets uppdateringar** klickar du på **Installera**.

     ![Enhets uppdateringar säger "bekräfta installation av uppdateringar". Knappen Installera och program varu versionen är markerad.](../includes/media/storsimple-virtual-array-install-update-via-portal-11/azupdate11m1.png)

6. Under **nya uppdateringar** varnas du om att uppdateringen är störande. En virtuell matris är en enhet med en enda nod. Enheten startas om när den har uppdaterats. Detta avbryter pågående IO. Klicka på **OK** för att installera uppdateringarna.

    ![Nya uppdateringar säger "enheten kommer att ha avbrott när dessa uppdateringar installeras". Det finns en länk till viktig information och en OK-knapp.](../includes/media/storsimple-virtual-array-install-update-via-portal-11/azupdate12m.png)

    Du får ett meddelande när installationen startar.

7.  När installations jobbet har slutförts klickar du på **Visa jobb** länk. Den här åtgärden tar dig till bladet **Installera uppdateringar** . Du kan visa detaljerad information om jobbet här. 

    ![Enhets uppdateringar har en länk med etiketten "installerar uppdateringar". Visa jobb ".](../includes/media/storsimple-virtual-array-install-update-via-portal-11/azupdate16m1.png)

8. Om du startade med en virtuell matris som kör program version uppdatering 1 (10.0.10296.0) kör du nu uppdatering 1,1 och är färdig. Du kan hoppa över de återstående stegen. 

    Om du startade med en virtuell matris som kör program varu versionen uppdatering 0,6 (10.0.10293.0) har du nu uppdaterat uppdateringen 1,0. Ett annat meddelande visas som anger att uppdateringar är tillgängliga. Upprepa steg 4-7 för att installera uppdatering 1,1.

    

