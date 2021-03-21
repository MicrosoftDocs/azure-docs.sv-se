---
title: Redundansväxling, haveri beredskap till en annan StorSimple 8000-enhet
description: Lär dig hur du växlar över din fysiska enhet med StorSimple 8000-serien till en annan fysisk enhet.
services: storsimple
documentationcenter: ''
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/03/2017
ms.author: alkohli
ms.openlocfilehash: 67d6fb1f3cc359288ed942d915e186542a62b0fc
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96017142"
---
# <a name="fail-over-to-a-storsimple-8000-series-physical-device"></a>Redundansväxla till en fysisk enhet i StorSimple 8000-serien

## <a name="overview"></a>Översikt

I den här självstudien beskrivs de steg som krävs för att redundansväxla en fysisk enhet i StorSimple 8000-serien till en annan StorSimple fysisk enhet om det finns en katastrof. StorSimple använder funktionen enhets växling för att migrera data från en fysisk käll enhet i data centret till en annan fysisk enhet. Vägledningen i den här självstudien gäller för fysiska enheter i StorSimple 8000-serien som kör program varu versioner uppdatering 3 och senare.

Om du vill veta mer om redundansväxlingen och hur den används för att återställa från en katastrof går du till [redundans och haveri beredskap för StorSimple 8000-serie enheter](storsimple-8000-device-failover-disaster-recovery.md).

Om du vill redundansväxla en StorSimple fysisk enhet till en StorSimple Cloud Appliance går du till [redundansväxla till en StorSimple Cloud Appliance](storsimple-8000-device-failover-cloud-appliance.md). Om du vill redundansväxla en fysisk enhet till sig själv går du till [redundansväxla till samma fysiska StorSimple-enhet](storsimple-8000-device-failover-same-device.md).


## <a name="prerequisites"></a>Förutsättningar

- Se till att du har granskat överväganden för redundansväxling av enhet. Mer information finns i [vanliga överväganden för enhets växling](storsimple-8000-device-failover-disaster-recovery.md).

- Du måste ha en fysisk enhet i StorSimple 8000-serien distribuerad i data centret. Enheten måste köra uppdatering 3 eller senare program varu version. Mer information finns i [distribuera din lokala StorSimple-enhet](storsimple-8000-deployment-walkthrough-u2.md).


## <a name="steps-to-fail-over-to-a-physical-device"></a>Steg för att redundansväxla till en fysisk enhet

Utför följande steg för att återställa enheten till en fysisk mål enhet.

1. Kontrol lera att den volym behållare som du vill växla över har associerade moln ögonblicks bilder. Mer information finns i [använda StorSimple Enhetshanteraren service för att skapa säkerhets kopior](storsimple-8000-manage-backup-policies-u2.md).
2. Gå till StorSimple-Enhetshanteraren och klicka sedan på **enheter**. På bladet **enheter** går du till listan över enheter som är anslutna till tjänsten.
    ![Välj enhet](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev1.png)
3. Välj och klicka på käll enheten. Käll enheten innehåller de volym behållare som du vill redundansväxla. Gå till **inställningar > volym behållare**.
4. Välj en volym behållare som du vill redundansväxla till en annan enhet. Klicka på volym containern om du vill visa listan över volymer i den här behållaren. Välj en volym, högerklicka och klicka sedan på koppla **från** för att koppla från volymen. Upprepa processen för alla volymer i volym containern.
5. Upprepa föregående steg för alla volym behållare som du vill redundansväxla till en annan enhet.
6. Gå tillbaka till bladet **enheter** . I kommando fältet klickar du på **redundans**.
    ![Klicka på redundans](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev2.png)
    
7. Utför följande steg på bladet **redundansväxla** :
   
   1. Klicka på **källa**. Volym behållarna med volymer kopplade till moln ögonblicks bilder visas. Endast de behållare som visas är kvalificerade för redundans. I listan över volym behållare väljer du de volym behållare som du vill redundansväxla. **Endast volym containrarna med tillhör ande moln ögonblicks bilder och offline-volymer visas.**

       ![Välj källa](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev5.png)
   2. Klicka på **mål**. För de volym behållare som valts i föregående steg väljer du en målenhet i list rutan med tillgängliga enheter. Endast de enheter som har tillräckligt med kapacitet för att hantera käll volym behållare visas i listan.

        ![Välja mål](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev6.png)

   3. Granska slutligen alla inställningar för redundans under **Sammanfattning**. När du har granskat inställningarna markerar du kryss rutan som anger att volymerna i de valda volym containrarna är offline. Klicka på **OK**.

       ![Granska redundans inställningar](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev8.png)
  
8. StorSimple skapar ett jobb för redundans. Klicka på aviseringen jobb för att övervaka redundansväxlingen via **jobb** -bladet.

    Om den volym behållare som du har växlat över har lokala volymer kan du se enskilda återställnings jobb för varje lokal volym (inte för skiktade volymer) i behållaren. Det kan ta lite tid att slutföra återställnings jobben. Det är troligt att Redundansåtgärden-jobbet kan slutföras tidigare. Volymerna kommer endast att ha lokala garantier när återställnings jobben har slutförts.

    ![Övervaka jobb för redundans](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev13.png)

9. När redundansväxlingen är klar går du till bladet **enheter** .
   
   1. Välj den enhet som användes som mål enhet för redundansväxlingen.

       ![Välj enhet 2](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev14.png)

   2. Gå till bladet **volym behållare** . Alla volym behållare, tillsammans med volymerna från den gamla enheten, ska listas.

       ![Visa mål volym behållare](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev16.png)


## <a name="next-steps"></a>Nästa steg

* När du har utfört en redundansväxling kan du behöva [inaktivera eller ta bort din StorSimple-enhet](storsimple-8000-deactivate-and-delete-device.md).
* Information om hur du använder tjänsten StorSimple Enhetshanteraren finns i [använda StorSimple Enhetshanteraren-tjänsten för att administrera StorSimple-enheten](storsimple-8000-manager-service-administration.md).

