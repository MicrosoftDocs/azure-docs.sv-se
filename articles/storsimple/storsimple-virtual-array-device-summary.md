---
title: Sammanfattnings blad för StorSimple virtuell mat ris enhet | Microsoft Docs
description: Beskriver bladet enhets Sammanfattning för StorSimple Enhetshanteraren och förklarar hur du använder det för att övervaka hälso tillståndet för din virtuella StorSimple-matris.
services: storsimple
documentationcenter: ''
author: manuaery
manager: syadav
editor: ''
ms.assetid: a13c1ea7-6428-4234-84a6-0ebf51670a85
ms.service: storsimple
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/29/2016
ms.author: manuaery
ms.openlocfilehash: 1ab72788c768568366f2627055015c74028330b5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96005885"
---
# <a name="use-the-device-summary-blade-for-storsimple-device-manager-connected-to-storsimple-virtual-array"></a>Använd bladet enhets Sammanfattning för StorSimple Enhetshanteraren som är anslutet till StorSimple virtuella matris

## <a name="overview"></a>Översikt

Bladet StorSimple Enhetshanteraren enhet innehåller en sammanfattning av en StorSimple-virtuell matris som har registrerats med en specifik StorSimple Enhetshanteraren och som markerar de enhets problem som behöver en system administratörs uppmärksamhet. Den här självstudien presenterar bladet enhets sammanfattning, förklarar innehållet och funktionen och beskriver de uppgifter som du kan utföra från det här bladet.

Bladet enhets Sammanfattning visar följande information:

![Instrumentpanel för enhet](./media/storsimple-virtual-array-device-summary/device-blade.png)



## <a name="management"></a>Hantering

På bladet StorSimple enhet ser du alternativen för att hantera din StorSimple-enhet. Du ser hanterings kommandona överst på bladet och på den vänstra sidan. Använd de här alternativen om du vill lägga till resurser eller volymer eller uppdatera eller redundansväxla den virtuella matrisen.

Området Essentials fångar upp några av de viktiga egenskaperna, till exempel, status, modell, program varu version samt en länk till **webb gränssnittet** för matrisen. Om du är i ett internt nätverk kan du direkt starta det [lokala webb gränssnittet](storsimple-ova-web-ui-admin.md) för att administrera din virtuella matris.

![Enhets Essentials](./media/storsimple-virtual-array-device-summary/device-essentials.png)

## <a name="storsimple-device-summary"></a>Sammanfattning av StorSimple-enhet

* Panelen **aviseringar** innehåller en ögonblicks bild av alla aktiva aviseringar för den virtuella matrisen, grupperade efter aviserings allvarlighets grad. Klicka på panelen för att öppna bladet **aviseringar** och klicka sedan på en enskild avisering om du vill visa mer information om aviseringen, inklusive eventuella rekommenderade åtgärder. Du kan också ta bort aviseringen om problemet har lösts.

* I panelen **kapacitet** visas den primära lagrings enheten som är etablerad och kvar på den virtuella enheten i förhållande till det totala lagrings utrymmet som är tillgängligt för samma. Med avseende på den mängd lagrings utrymme som är för beredd och tilldelad för användning **, refererar** **återstående** till den återstående kapacitet som kan tillhandahållas på den här enheten. Den **återstående** kapacitets kapaciteten är den tillgängliga kapacitet som kan tillhandahållas inklusive molnet, medan **återstående lokala** kapacitet är den kapacitet som återstår på diskarna som är anslutna till den virtuella matrisen.

* I **användnings** diagrammet kan du Visa det primära lagrings utrymmet som används i den virtuella matrisen, samt moln lagring som förbrukas under de senaste 7 dagarna, standard tids perioden. Använd alternativet **Redigera** i det övre högra hörnet i diagrammet om du vill välja en annan tids skala.

* Panelen **resurser** eller **volymer** innehåller en översikt över antalet resurser eller volymer i enheten grupperade efter status. Klicka på panelen för att öppna bladet **resurser**  eller **volymer** och klicka sedan på en enskild resurs eller volym för att visa eller ändra dess egenskaper. Mer information finns i [Hantera resurser](storsimple-virtual-array-manage-shares.md) eller [Hantera volymer](storsimple-virtual-array-manage-volumes.md).

## <a name="next-steps"></a>Nästa steg
Lär dig att:
- [Hantera resurser på en virtuell StorSimple-matris](storsimple-virtual-array-manage-shares.md)
    
- [Hantera volymer på en virtuell StorSimple-matris](storsimple-virtual-array-manage-volumes.md)

