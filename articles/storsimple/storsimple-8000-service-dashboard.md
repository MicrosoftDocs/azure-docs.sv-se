---
title: Använd tjänst sammanfattnings bladet för StorSimple 8000-serien het
description: Beskriver bladet StorSimple service Summary och förklarar hur du använder det för att övervaka hälso tillståndet för din StorSimple-lösning.
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
ms.date: 03/27/2017
ms.author: alkohli
ms.openlocfilehash: 7b4b697b3d27b57212fc59396e1f8111e297d6cf
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "85514942"
---
# <a name="use-the-service-summary-blade-for-storsimple-8000-series-device"></a>Använd bladet tjänst Sammanfattning för StorSimple 8000-serien het

## <a name="overview"></a>Översikt

Bladet StorSimple Enhetshanteraren tjänst sammanfattning innehåller en sammanfattningsvy över alla enheter som är anslutna till StorSimple Enhetshanteraren-tjänsten, vilket markerar de enheter som behöver en system administratörs uppmärksamhet. I den här självstudien presenterar vi bladet för tjänst sammanfattning, förklarar instrument panelens innehåll och funktion och beskriver de uppgifter som du kan utföra från den här sidan.

![Tjänst Sammanfattning](./media/storsimple-8000-service-dashboard/service-summary1.png)


## <a name="management-commands"></a>Kommandon för hantering

På bladet StorSimple service Summary ser du alternativen för att hantera din StorSimple Enhetshanteraren-tjänst och de StorSimple 8000-serie enheter som är registrerade för den här tjänsten. Du ser hanterings kommandona överst på bladet och på den vänstra sidan.

![Kommandofältet](./media/storsimple-8000-service-dashboard/service-summary2.png)

Använd de här alternativen för att utföra olika åtgärder, till exempel lägga till resurser eller volymer, eller övervaka de olika jobben som körs på StorSimple-enheterna.


## <a name="essentials"></a>Essentials

I avsnittet Essentials samlas några av de viktiga egenskaperna, till exempel, resurs gruppen, platsen och prenumerationen där din StorSimple Enhetshanteraren skapades.

![Essentials](./media/storsimple-8000-service-dashboard/service-summary3.png)

## <a name="storsimple-device-manager-service-summary"></a>Sammanfattning av StorSimple Enhetshanteraren-tjänsten

* Panelen **aviseringar** innehåller en ögonblicks bild av alla aktiva aviseringar på alla enheter, grupperade efter aviserings allvarlighets grad.

    ![Panelen Aviseringar](./media/storsimple-8000-service-dashboard/service-summary4.png)

    Om du klickar på panelen öppnas **varnings** bladet där du kan klicka på en enskild avisering om du vill visa mer information om aviseringen, inklusive eventuella rekommenderade åtgärder. Du kan också ta bort aviseringen om problemet har lösts.

    ![Klicka på aviserings panelen](./media/storsimple-8000-service-dashboard/service-summary8.png)

* Panelen **kapacitet** visar den primära lagring som tillhandahålls och är kvar på alla enheter i förhållande till det totala lagrings utrymmet som är tillgängligt på alla enheter. Med avseende på den mängd lagrings utrymme som är för beredd och tilldelad för användning **, refererar** **återstående** till den återstående kapacitet som kan tillhandahållas på alla enheter.

    ![Kapacitets panel](./media/storsimple-8000-service-dashboard/service-summary6.png)

    Den **återstående** kapacitets kapaciteten är den tillgängliga kapacitet som kan tillhandahållas inklusive molnet, medan **återstående lokala** är kapaciteten kvar på diskarna som är anslutna till StorSimple 8000-serie enheterna.


* I **användnings** diagrammet kan du se relevanta mått för dina enheter. Du kan visa det primära lagrings utrymmet som används på alla enheter och moln lagring som förbrukas av enheter under de senaste 7 dagarna, standard tids perioden. 

    ![Användnings panel](./media/storsimple-8000-service-dashboard/service-summary7.png) 

    Om du vill välja en annan tids skala använder du alternativet **Redigera** i det övre högra hörnet i diagrammet.

     ![Klicka på panelen användning](./media/storsimple-8000-service-dashboard/service-summary10.png)

     ![Exportera diagram data](./media/storsimple-8000-service-dashboard/service-summary11.png)

* Panelen **enheter** innehåller en översikt över antalet StorSimple 8000-serie enheter i din StorSimple-Enhetshanteraren grupperade efter enhets status. 

    ![Panelen enheter](./media/storsimple-8000-service-dashboard/service-summary5.png)

    Klicka på den här panelen för att öppna bladet **med enhets listan och** klicka sedan på en enskild enhet för att öka detalj nivån för enhetens Sammanfattning. Du kan också utföra enhetsspecifika åtgärder från ett angivet enhets sammanfattnings blad. Mer information om bladet enhets sammanfattning finns på [bladet enhets Sammanfattning](storsimple-8000-device-dashboard.md).

    ![Klicka på enhets panelen](./media/storsimple-8000-service-dashboard/service-summary9.png)

## <a name="view-the-activity-logs"></a>Visa aktivitets loggarna

Om du vill visa de olika åtgärder som utförs i StorSimple Enhetshanteraren klickar du på länken **aktivitets loggar** till vänster på bladet StorSimple service Summary. Då går du till bladet **aktivitets loggar** där du kan se en sammanfattning av de senaste åtgärderna som utförts.

![Aktivitetsloggar](./media/storsimple-8000-service-dashboard/activity-logs1.png)
## <a name="next-steps"></a>Nästa steg

* Lär dig mer om hur du [använder tjänsten StorSimple Enhetshanteraren för att administrera din StorSimple-enhet](storsimple-8000-manager-service-administration.md).

