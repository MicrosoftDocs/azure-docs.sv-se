---
title: Bladet StorSimple Virtual Array service Summary | Microsoft Docs
description: Beskriver bladet för tjänst Sammanfattning för StorSimple Enhetshanteraren och förklarar hur du använder det för att övervaka hälso tillståndet för din virtuella StorSimple-matris.
services: storsimple
documentationcenter: ''
author: manuaery
manager: syadav
editor: ''
ms.assetid: 8a2b9a84-b0e6-48b9-b366-d16f004241a5
ms.service: storsimple
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2016
ms.author: manuaery
ms.openlocfilehash: 31220a8f8b012e08d46195b60a26be9cfc261be5
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "85514523"
---
# <a name="use-the-service-summary-blade-for-storsimple-device-manager-connected-to-storsimple-virtual-array"></a>Använd bladet för tjänst Sammanfattning för StorSimple Enhetshanteraren som är anslutet till StorSimple virtuella matris
## <a name="overview"></a>Översikt
Bladet för tjänst Sammanfattning för StorSimple-Enhetshanteraren innehåller en sammanfattningsvy för virtuella StorSimple-matriser (även kallade StorSimple lokala virtuella enheter eller virtuella enheter) som är anslutna till din tjänst och som markerar de som behöver en system administratörs uppmärksamhet. I den här självstudien presenterar vi bladet för tjänst sammanfattning, förklarar innehållet och funktionen och beskriver de uppgifter som du kan utföra från det här bladet.

![Instrument panel för tjänsten](./media/storsimple-virtual-array-service-summary/service-blade.png)

## <a name="management-commands-and-essentials"></a>Hanterings kommandon och Essentials
På bladet StorSimple Summary ser du alternativen för att hantera din StorSimple Enhetshanteraren-tjänst samt de virtuella matriser som har registrerats för den här tjänsten. Du ser hanterings kommandona överst på bladet och på den vänstra sidan.

Använd de här alternativen för att utföra olika åtgärder, till exempel lägga till resurser eller volymer, eller övervaka de olika jobb som körs på de virtuella matriserna.

I avsnittet Essentials samlas några av de viktiga egenskaperna, till exempel, resurs gruppen, platsen och prenumerationen där din StorSimple Enhetshanteraren skapades.

## <a name="storsimple-device-manager-service-summary"></a>Sammanfattning av StorSimple Enhetshanteraren-tjänsten
* Panelen **aviseringar** innehåller en ögonblicks bild av alla aktiva aviseringar på alla virtuella enheter, grupperade efter aviserings allvarlighets grad. Om du klickar på panelen öppnas **varnings** bladet där du kan klicka på en enskild avisering om du vill visa mer information om aviseringen, inklusive eventuella rekommenderade åtgärder. Du kan också ta bort aviseringen om problemet har lösts.
* Panelen **kapacitet** visar den primära lagring som tillhandahålls och är kvar på alla virtuella enheter i förhållande till det totala lagrings utrymmet som är tillgängligt för alla virtuella enheter. Med den mängd lagrings utrymme som är för beredd och tilldelad för användning **, refererar** **återstående** till den återstående kapacitet som kan tillhandahållas på alla virtuella enheter. Den **återstående** kapacitets kapaciteten är den tillgängliga kapacitet som kan tillhandahållas inklusive molnet, medan **återstående lokala** kapacitet är den kapacitet som återstår på diskarna som är anslutna till de virtuella matriserna.
* I **användnings** diagrammet kan du se relevanta mått för dina virtuella enheter. Du kan visa den primära lagringen som används för alla virtuella enheter, samt den moln lagring som förbrukas av virtuella enheter under de senaste 7 dagarna, standard tids perioden. Använd alternativet **Redigera** i det övre högra hörnet i diagrammet om du vill välja en annan tids skala.
* Panelen **enheter** innehåller en översikt över antalet virtuella matriser i din StorSimple-Enhetshanteraren grupperade efter enhets status. Klicka på den här panelen för att öppna bladet **med enhets listan och** klicka sedan på en enskild enhet för att öka detalj nivån för enhetens Sammanfattning. Du kan också utföra enhetsspecifika åtgärder från ett angivet enhets sammanfattnings blad. Mer information om bladet enhets sammanfattning finns på [bladet enhets Sammanfattning](storsimple-virtual-array-device-summary.md).

## <a name="view-the-activity-logs"></a>Visa aktivitets loggarna
Om du vill visa de olika åtgärder som utförs i StorSimple Enhetshanteraren klickar du på länken **aktivitets loggar** till vänster på bladet StorSimple service Summary. Då går du till bladet **aktivitets loggar** där du kan se en sammanfattning av de senaste åtgärderna som utförts.

![Aktivitetsloggar](./media/storsimple-virtual-array-service-summary/activity-log.png)

## <a name="next-steps"></a>Nästa steg
Lär dig hur du [använder det lokala webb gränssnittet för att administrera din virtuella StorSimple-matris](storsimple-ova-web-ui-admin.md).

