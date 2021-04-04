---
title: Inaktivera och ta bort en Microsoft Azure StorSimple virtuell matris | Microsoft Docs
description: Beskriver hur du tar bort StorSimple-enheten från tjänsten genom att först inaktivera den och sedan ta bort den.
services: storsimple
documentationcenter: ''
author: alkohli
manager: carmonm
editor: ''
ms.assetid: a929f5bc-03e2-4b01-b925-973db236f19f
ms.service: storsimple
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2016
ms.author: alkohli
ms.openlocfilehash: 84fd1a2d3d0dc4ce6960469ea3212b8ca1d4d07c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96023031"
---
# <a name="deactivate-and-delete-a-storsimple-virtual-array"></a>Inaktivera och ta bort en virtuell StorSimple-matris

## <a name="overview"></a>Översikt

När du inaktiverar en virtuell StorSimple-matris bryter du förbindelsen mellan enheten och motsvarande StorSimple Enhetshanteraren-tjänst. I den här självstudien beskrivs hur du:

* Inaktivera en enhet 
* Ta bort en inaktive rad enhet

Informationen i den här artikeln gäller endast virtuella StorSimple-matriser. Information om 8000-serien finns i så här [inaktiverar eller tar du bort en enhet](./storsimple-8000-deactivate-and-delete-device.md).

## <a name="when-to-deactivate"></a>När ska du inaktivera?

Inaktive ringen är en PERMANENT åtgärd och kan inte ångras. Du kan inte registrera en inaktive rad enhet med StorSimple-Enhetshanteraren tjänsten igen. Du kan behöva inaktivera och ta bort en virtuell StorSimple-matris i följande scenarier:

* **Planerad redundans** : enheten är online och du planerar att redundansväxla enheten. Om du planerar att uppgradera till en större enhet kan du behöva redundansväxla enheten. När data ägarskapet har överförts och redundansväxlingen är klar tas käll enheten bort automatiskt.
* **Oplanerad redundans** : din enhet är offline och du måste redundansväxla enheten. Det här scenariot kan inträffa under en katastrof när det uppstår ett avbrott i data centret och din primära enhet är avstängd. Du planerar att redundansväxla enheten till en sekundär enhet. När data ägarskapet har överförts och redundansväxlingen är klar tas käll enheten bort automatiskt.
* **Avställning** : du vill inaktivera enheten. Du måste först inaktivera enheten och sedan ta bort den. När du inaktiverar en enhet kan du inte längre komma åt data som lagras lokalt. Du kan bara komma åt och återställa data som lagras i molnet. Om du planerar att behålla enhets data efter inaktive ringen bör du ta en moln ögonblicks bild av alla dina data innan du inaktiverar en enhet. Med den här ögonblicks bilden av molnet kan du återställa alla data i ett senare skede.

## <a name="deactivate-a-device"></a>Inaktivera en enhet

Utför följande steg för att inaktivera enheten.

#### <a name="to-deactivate-the-device"></a>Så här inaktiverar du enheten

1. I din tjänst går du till **hantering > enheter**. I bladet **enheter** klickar du på och väljer den enhet som du vill inaktivera.
   
    ![Välj enhet som ska inaktive ras](./media/storsimple-virtual-array-deactivate-and-delete-device/deactivate-delete7.png)
2. I bladet för din **enhets instrument panel** klickar du på **... Mer** och välj **inaktivera** i listan.
   
    ![Klicka på Inaktivera](./media/storsimple-virtual-array-deactivate-and-delete-device/deactivate-delete8.png)
3. Skriv enhets namnet i bladet **inaktive ring** och klicka sedan på **inaktivera**. 
   
    ![Bekräfta inaktive ring](./media/storsimple-virtual-array-deactivate-and-delete-device/deactivate-delete1.png)
   
    Inaktive rings processen startar och tar några minuter att slutföra.
   
    ![Inaktive ring pågår](./media/storsimple-virtual-array-deactivate-and-delete-device/deactivate-delete2.png)
4. Efter inaktive ringen uppdateras listan över enheter.
   
    ![Inaktivera slutförd](./media/storsimple-virtual-array-deactivate-and-delete-device/deactivate-delete3.png)
   
    Nu kan du ta bort den här enheten.

## <a name="delete-the-device"></a>Ta bort enheten

En enhet måste först inaktive ras för att ta bort den. Om du tar bort en enhet tas den bort från listan över enheter som är anslutna till tjänsten. Tjänsten kan sedan inte längre hantera den borttagna enheten. De data som är associerade med enheten finns dock kvar i molnet. Dessa data påförs sedan avgifterna.

Utför följande steg för att ta bort enheten.

#### <a name="to-delete-the-device"></a>Ta bort enheten

1. I StorSimple Enhetshanteraren går du till **hantering > enheter**. På bladet **enheter** väljer du en inaktive rad enhet som du vill ta bort.
2. I bladet för **enhets instrument panelen** klickar du på **... Mer** och klicka sedan på **ta bort**.
   
   ![Välj vilken enhet som ska tas bort](./media/storsimple-virtual-array-deactivate-and-delete-device/deactivate-delete4.png)
3. I bladet **ta bort** skriver du namnet på enheten för att bekräfta borttagningen och klickar sedan på **ta bort**. Om du tar bort enheten tas inte de moln data som är kopplade till enheten bort. 
   
   ![Bekräfta borttagning](./media/storsimple-virtual-array-deactivate-and-delete-device/deactivate-delete5.png) 
4. Borttagningen startar och tar några minuter att slutföra.
   
   ![Borttagning pågår](./media/storsimple-virtual-array-deactivate-and-delete-device/deactivate-delete6.png)
   
    När enheten har tagits bort kan du Visa den uppdaterade listan med enheter.

## <a name="next-steps"></a>Nästa steg

* För information om hur du växlar över, gå till [redundans och haveri beredskap för din virtuella StorSimple-matris](storsimple-virtual-array-failover-dr.md).

* Om du vill veta mer om hur du använder tjänsten StorSimple Enhetshanteraren går du till [använda tjänsten StorSimple Enhetshanteraren för att administrera din StorSimple virtuella matris](storsimple-virtual-array-manager-service-administration.md).