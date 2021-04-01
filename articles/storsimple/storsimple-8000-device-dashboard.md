---
title: Använda enhets Sammanfattning i StorSimple Enhetshanteraren-tjänsten
description: Beskriver StorSimple Enhetshanteraren tjänst enhets Sammanfattning och hur du använder den för att Visa lagrings mått och anslutna initierare och hitta serie numret och det kvalificerade iSCSI-namnet.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 07/03/2017
ms.author: alkohli
ms.openlocfilehash: 34bbf4d723e46663efe77560245db74d9dea1fc4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96017159"
---
# <a name="use-the-device-summary-in-storsimple-device-manager-service"></a>Använda enhets översikten i StorSimple Enhetshanteraren-tjänsten

## <a name="overview"></a>Översikt
Bladet StorSimple Device Summary ger dig en översikt över information för en speciell StorSimple-enhet, i motsats till bladet för tjänst sammanfattning, som ger dig information om alla enheter som ingår i din Microsoft Azure StorSimple lösning.

Bladet enhets sammanfattning innehåller en sammanfattning av en StorSimple 8000-serie som har registrerats med ett angivet StorSimple-Enhetshanteraren och som markerar de enhets problem som behöver en system administratörs uppmärksamhet. Den här självstudien presenterar bladet enhets sammanfattning, förklarar innehållet och funktionen och beskriver de uppgifter som du kan utföra från det här bladet.

Bladet enhets Sammanfattning visar följande information:

![Bladet enhets Sammanfattning](./media/storsimple-8000-device-dashboard/device-summary1.png)

## <a name="management-command-bar"></a>Hanterings kommando fält

På bladet StorSimple enhet ser du alternativen för att hantera din StorSimple-enhet. Du ser hanterings kommandona överst på bladet och på den vänstra sidan. Använd de här alternativen om du vill lägga till resurser eller volymer, eller uppdatera eller redundansväxla enheten.

![Hanterings kommando fält](./media/storsimple-8000-device-dashboard/device-summary2.png)

## <a name="essentials"></a>Essentials

I avsnittet Essentials samlas några av de viktiga egenskaperna, till exempel, status, modell, mål-IQN och program varu version. 

![Enhets Essentials](./media/storsimple-8000-device-dashboard/device-summary3.png)

## <a name="monitoring"></a>Övervakning

* Panelen **aviseringar** innehåller en ögonblicks bild av alla aktiva aviseringar för enheten, grupperat efter allvarlighets grad för avisering.

    ![Varnings panel](./media/storsimple-8000-device-dashboard/device-summary4.png)

    Klicka på panelen för att öppna bladet **aviseringar** och klicka sedan på en enskild avisering om du vill visa mer information om aviseringen, inklusive eventuella rekommenderade åtgärder. Du kan också ta bort aviseringen om problemet har lösts.

    ![Klicka på aviserings panel](./media/storsimple-8000-device-dashboard/device-summary10.png)

* Panelen **status och hälsa** ger insikter om hälso tillståndet för maskin varu komponenten för en enhet, inklusive enhets status. Enhetens status kan vara offline, online, inaktive rad eller redo att konfigureras.

    ![Panel för status och hälsa](./media/storsimple-8000-device-dashboard/device-summary5.png)

* Panelen **volymer** innehåller en översikt över antalet volymer i enheten grupperat efter status.

    ![Panelen volymer](./media/storsimple-8000-device-dashboard/device-summary6.png)

    Klicka på panelen för att öppna bladet **volym** listor och klicka sedan på en enskild volym för att visa eller ändra dess egenskaper.
    
    ![Klicka på panelen volymer](./media/storsimple-8000-device-dashboard/device-summary9.png)
    
    Mer information finns i [Hantera volymer](storsimple-8000-manage-volumes-u2.md).

* I **användnings** diagrammet kan du Visa det primära lagrings utrymmet som används på enheten och moln lagring som förbrukas under de senaste 7 dagarna, standard tids perioden.

     ![Användnings panel](./media/storsimple-8000-device-dashboard/device-summary7.png)
    
     Om du vill välja en annan tids skala använder du alternativet **Redigera** i det övre högra hörnet i diagrammet.

     ![Redigera användnings diagram](./media/storsimple-8000-device-dashboard/device-summary12.png)

     I det här diagrammet kan du visa mått för den totala primära lagringen (mängden data som skrivits av värdar till din enhet) och den totala moln lagring som förbrukas av enheten under en viss tids period.
  
     I det här sammanhanget refererar *primär lagring* till den totala mängden data som skrivits av värden och kan delas upp av volymtyp: *primärt lagrings utrymme* omfattar både lokalt lagrade data och data på nivå av molnet. *Primär lokalt fast lagring* innehåller bara data som lagras lokalt. *Moln lagring*, å andra sidan, är en mätning av den totala mängden data som lagras i molnet. Det här lagrings utrymmet innehåller data och säkerhets kopior på nivå. Data som lagras i molnet dedupliceras och komprimeras, medan primär lagring anger mängden lagrings utrymme som används innan data dedupliceras och komprimeras. (Du kan jämföra de här två talen för att få en uppfattning om komprimerings hastigheten.) För både primär-och moln lagring baseras de angivna beloppen på den spårnings frekvens som du konfigurerar. Om du till exempel väljer en frekvens på en vecka visar diagrammet data för varje dag i föregående vecka.

     Om du vill se mängden moln lagring som förbrukas över tid väljer du alternativet **moln lagring som används** . Om du vill se den totala lagrings utrymme som har skrivits av värden väljer du alternativet **primärt lagrings utrymme som används** och **primärt lokalt fäst lagrings utrymme som används** . 
     Mer information finns i [använda tjänsten StorSimple Enhetshanteraren för att övervaka StorSimple-enheten](./storsimple-8000-monitor-device.md).


* I panelen **kapacitet** visas den primära lagringen som är etablerad och kvar på enheten i förhållande till det totala lagrings utrymmet som är tillgängligt för samma. Med avseende på den mängd lagrings utrymme som är för beredd och tilldelad för användning **, refererar** **återstående** till den återstående kapacitet som kan tillhandahållas på den här enheten. 

    ![Användnings panel 2](./media/storsimple-8000-device-dashboard/device-summary8.png)

    Klicka på den här panelen om du vill se hur kapaciteten tillhandahålls mellan olika nivåer och lokalt fästa volymer. Den **återstående** kapacitets kapaciteten är den tillgängliga kapacitet som kan tillhandahållas inklusive molnet, medan **återstående lokala** kapacitet är den kapacitet som återstår på diskarna som är anslutna till den här enheten.

    ![Klicka på användnings diagram](./media/storsimple-8000-device-dashboard/device-summary13.png)


## <a name="next-steps"></a>Nästa steg
* Läs mer om [StorSimple service Summary-bladet](storsimple-8000-service-dashboard.md).
* Lär dig mer om [att använda tjänsten StorSimple Enhetshanteraren för att administrera din StorSimple-enhet](storsimple-8000-manager-service-administration.md).