---
title: Självstudie – redundansväxla StorSimple fysisk enhet till samma enhet
description: Lär dig de steg som krävs för att redundansväxla en fysisk enhet i StorSimple 8000-serien till sig själv om det är en katastrof.
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
ms.date: 06/23/2017
ms.author: alkohli
ms.openlocfilehash: 17c116194aa52a82246bcee9114824e8a918ebbd
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96017108"
---
# <a name="fail-over-your-storsimple-physical-device-to-same-device"></a>Redundansväxla din fysiska StorSimple-enhet till samma enhet

## <a name="overview"></a>Översikt

I den här självstudien beskrivs de steg som krävs för att redundansväxla en fysisk enhet i StorSimple 8000-serien till sig själv om det är en katastrof. StorSimple använder funktionen enhets växling för att migrera data från en fysisk käll enhet i data centret till en annan fysisk enhet. Vägledningen i den här självstudien gäller för fysiska enheter i StorSimple 8000-serien som kör program varu versioner uppdatering 3 och senare.

Om du vill veta mer om redundansväxlingen och hur den används för att återställa från en katastrof går du till [redundans och haveri beredskap för StorSimple 8000-serie enheter](storsimple-8000-device-failover-disaster-recovery.md).

Om du vill redundansväxla en fysisk enhet till en annan fysisk enhet går du till [redundansväxla till samma StorSimple fysiska enhet](storsimple-8000-device-failover-physical-device.md). Om du vill redundansväxla en StorSimple fysisk enhet till en StorSimple Cloud Appliance går du till [redundansväxla till en StorSimple Cloud Appliance](storsimple-8000-device-failover-cloud-appliance.md).


## <a name="prerequisites"></a>Förutsättningar

- Se till att du har granskat överväganden för redundansväxling av enhet. Mer information finns i [vanliga överväganden för enhets växling](storsimple-8000-device-failover-disaster-recovery.md).


## <a name="steps-to-fail-over-to-the-same-device"></a>Steg för att redundansväxla till samma enhet

Utför följande steg om du behöver redundansväxla till samma enhet.

1. Ta moln ögonblicks bilder av alla volymer i enheten. Mer information finns i [använda StorSimple Enhetshanteraren service för att skapa säkerhets kopior](storsimple-8000-manage-backup-policies-u2.md).
2. Återställ enheten till fabriks inställningarna. Följ de detaljerade anvisningarna i [så här återställer du en StorSimple-enhet till fabriks inställningarna](storsimple-8000-manage-device-controller.md#reset-the-device-to-factory-default-settings).
3. Gå till StorSimple Enhetshanteraren-tjänsten och välj sedan **enheter**. På bladet **enheter** ska den gamla enheten visas som **offline**.

    ![Käll enheten är offline](./media/storsimple-8000-device-failover-disaster-recovery/failover-single-dev2.png)

4. Konfigurera enheten och registrera den igen med din StorSimple Enhetshanteraren-tjänst. Den nyligen registrerade enheten bör visas som **klar att konfigurera**. Enhets namnet för den nya enheten är detsamma som den gamla enheten, men läggs till med en siffra för att indikera att enheten har återställts till fabriks inställningarna och registrerats igen.

    ![Nyligen registrerad enhet redo att konfigureras](./media/storsimple-8000-device-failover-disaster-recovery/failover-single-dev3.png)
5. Slutför enhets konfigurationen för den nya enheten. Mer information finns i [steg 4: Slutför lägsta enhets konfigurationen](storsimple-8000-deployment-walkthrough-u2.md#step-4-complete-minimum-device-setup). På bladet **enheter** ändras enhetens status till **online**.

   > [!IMPORTANT]
   > **Slutför den minimala konfigurationen först, annars kan din DR Miss lyckas.**

    ![Nyligen registrerad enhet online](./media/storsimple-8000-device-failover-disaster-recovery/failover-single-dev7.png)

6. Välj den gamla enheten (status offline) och i kommando fältet klickar du på **redundans**. På bladet **redundansväxla** väljer du gammal enhet som källa och anger mål enheten som den nyligen registrerade enheten.

    ![Sammanfattning av redundans](./media/storsimple-8000-device-failover-disaster-recovery/failover-single-dev11.png)

    Detaljerade anvisningar finns i redundansväxla till en annan fysisk enhet.

7. En enhets återställnings jobb skapas som du kan övervaka från bladet **jobb** .

8. När jobbet har slutförts öppnar du den nya enheten och går till bladet **volym behållare** . Kontrol lera att alla volym behållare från den gamla enheten har migrerats till den nya enheten.

   ![Volym containrar har migrerats](./media/storsimple-8000-device-failover-disaster-recovery/failover-single-dev13.png)

9. När redundansväxlingen är klar kan du inaktivera och ta bort den gamla enheten från portalen. Välj den gamla enheten (offline), högerklicka på och välj sedan **inaktivera**. När enheten har inaktiverats uppdateras enhetens status.

     ![Käll enheten är inaktive rad](./media/storsimple-8000-device-failover-disaster-recovery/failover-single-dev14.png)

10. Välj den inaktiverade enheten, högerklicka på och välj sedan **ta bort**. Enheten tas bort från listan över enheter.

    ![Käll enheten har tagits bort](./media/storsimple-8000-device-failover-disaster-recovery/failover-single-dev15.png)



## <a name="next-steps"></a>Nästa steg

* När du har utfört en redundansväxling kan du behöva [inaktivera eller ta bort din StorSimple-enhet](storsimple-8000-deactivate-and-delete-device.md).
* Information om hur du använder tjänsten StorSimple Enhetshanteraren finns i [använda StorSimple Enhetshanteraren-tjänsten för att administrera StorSimple-enheten](storsimple-8000-manager-service-administration.md).

