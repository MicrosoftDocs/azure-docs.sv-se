---
title: Redundansväxling och haveri beredskap till en StorSimple Cloud Appliance
description: Lär dig hur du växlar över din fysiska enhet i StorSimple 8000-serien till en moln installation.
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
ms.date: 07/03/2017
ms.author: alkohli
ms.openlocfilehash: 18b34ff466f3935cb5cd18d46e6d26e36e756a9b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "91774468"
---
# <a name="fail-over-to-your-storsimple-cloud-appliance"></a>Redundansväxla till din StorSimple Cloud Appliance

## <a name="overview"></a>Översikt

I den här självstudien beskrivs de steg som krävs för att redundansväxla en fysisk enhet i StorSimple 8000-serien till en StorSimple Cloud Appliance om det finns en katastrof. StorSimple använder funktionen enhets växling för att migrera data från en fysisk käll enhet i data centret till en moln installation som körs i Azure. Vägledningen i den här självstudien gäller för fysiska enheter i StorSimple 8000-serien och moln apparater som kör program versioner uppdatering 3 och senare.

Om du vill veta mer om redundansväxlingen och hur den används för att återställa från en katastrof går du till [redundans och haveri beredskap för StorSimple 8000-serie enheter](storsimple-8000-device-failover-disaster-recovery.md).

Om du vill redundansväxla en fysisk StorSimple-enhet till en annan fysisk enhet går du till [redundansväxla till en StorSimple fysisk enhet](storsimple-8000-device-failover-physical-device.md). Om du vill redundansväxla en enhet till sig själv går du till [redundansväxla till samma fysiska StorSimple-enhet](storsimple-8000-device-failover-same-device.md).

## <a name="prerequisites"></a>Förutsättningar

- Se till att du har granskat överväganden för redundansväxling av enhet. Mer information finns i [vanliga överväganden för enhets växling](storsimple-8000-device-failover-disaster-recovery.md).

- Du måste ha ett StorSimple Cloud Appliance skapat och konfigurerat innan du kör den här proceduren. Om du kör uppdatering 3 eller senare kan du använda en 8020-moln installation för DR. 8020-modellen har 64 TB och använder Premium Storage. Mer information finns i [distribuera och hantera en StorSimple Cloud Appliance](storsimple-8000-cloud-appliance-u2.md).

## <a name="steps-to-fail-over-to-a-cloud-appliance"></a>Steg för att redundansväxla till en moln installation

Utför följande steg för att återställa enheten till en mål StorSimple Cloud Appliance.

1.  Kontrol lera att den volym behållare som du vill växla över har associerade moln ögonblicks bilder. Mer information finns i [använda StorSimple Enhetshanteraren service för att skapa säkerhets kopior](storsimple-8000-manage-backup-policies-u2.md).
2. Gå till StorSimple Device Manager-tjänsten och klicka på **Enheter**. På bladet **enheter** går du till listan över enheter som är anslutna till tjänsten.
    ![Välj enhet](./media/storsimple-8000-device-failover-disaster-recovery/failover-cloud-dev1.png)
3. Välj och klicka på käll enheten. Käll enheten innehåller de volym behållare som du vill redundansväxla. Gå till **inställningar > volym behållare**.

    ![Välj enhet 2](./media/storsimple-8000-device-failover-disaster-recovery/failover-cloud-dev2.png)
    
4. Välj en volym behållare som du vill redundansväxla till en annan enhet. Klicka på volym containern om du vill visa listan över volymer i den här behållaren. Välj en volym, högerklicka och klicka sedan på koppla **från** för att koppla från volymen.

    ![Välj enhet 3](./media/storsimple-8000-device-failover-disaster-recovery/failover-cloud-dev5.png)

5. Upprepa processen för alla volymer i volym containern.

     ![Välj enhet 4](./media/storsimple-8000-device-failover-disaster-recovery/failover-cloud-dev7.png)

6. Upprepa föregående steg för alla volym behållare som du vill redundansväxla till en annan enhet.

7. Gå tillbaka till bladet **enheter** . I kommando fältet klickar du på **redundans**.

    ![Klicka på redundans](./media/storsimple-8000-device-failover-disaster-recovery/failover-cloud-dev8.png)
8. Utför följande steg på bladet **redundansväxla** :
   
    1. Klicka på **källa**. Välj de volym behållare som ska redundansväxlas. **Endast volym containrarna med tillhör ande moln ögonblicks bilder och offline-volymer visas.**
        ![Välj källa](./media/storsimple-8000-device-failover-disaster-recovery/failover-cloud-dev11.png)
    2. Klicka på **mål**. Välj en mål moln installation i list rutan med tillgängliga enheter. **Endast de enheter som har tillräckligt med kapacitet för att hantera käll volym behållare visas i listan.**

        ![Välja mål](./media/storsimple-8000-device-failover-disaster-recovery/failover-cloud-dev12.png)

    3. Granska inställningarna för redundans under **Sammanfattning** och markera kryss rutan som anger att volymerna i de valda volym containrarna är offline. 

        ![Granska redundans inställningar](./media/storsimple-8000-device-failover-disaster-recovery/failover-cloud-dev13.png)

9. Ett jobb för redundans har skapats. Klicka på aviseringen om du vill övervaka redundansväxlingen.

    ![Övervaka jobb för redundans](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev13.png)

10. När redundansväxlingen är klar går du tillbaka till bladet **enheter** .

    1. Välj den enhet som användes som mål för redundansväxlingen.

       ![Välj enhet 5](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev14.png)

    2. Klicka på **volym behållare**. Alla volym behållare, tillsammans med volymerna från den gamla enheten, ska listas.

       Om den volym behållare som du har växlat över har lokalt fästa volymer, växlas dessa volymer över som nivåer av volymer. Lokalt fästa volymer stöds inte på en StorSimple Cloud Appliance.

       ![Visa mål volym behållare](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev17.png)


## <a name="next-steps"></a>Nästa steg

* När du har utfört en redundansväxling kan du behöva [inaktivera eller ta bort din StorSimple-enhet](storsimple-8000-deactivate-and-delete-device.md).

* Information om hur du använder tjänsten StorSimple Enhetshanteraren finns i [använda StorSimple Enhetshanteraren-tjänsten för att administrera StorSimple-enheten](storsimple-8000-manager-service-administration.md).

