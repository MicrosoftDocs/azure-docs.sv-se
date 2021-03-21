---
title: ta med fil
description: ta med fil
services: storsimple
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 02/09/2021
ms.author: alkohli
ms.custom: include file
ms.openlocfilehash: 386a39d76bf47da45f07054124886e174104849e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "100545411"
---
Om du vill ta bort en volym behållare måste du
 - ta bort volymer i volym containern. Om volym containern har associerade volymer ska du koppla från dessa volymer först. Följ stegen i koppla från [en volym](../articles/storsimple/storsimple-8000-manage-volumes-u2.md#take-a-volume-offline). När volymerna är offline kan du ta bort dem. 
 - ta bort tillhör ande säkerhets kopierings principer och moln ögonblicks bilder. Kontrol lera om volym containern har tillhör ande säkerhets kopierings principer och moln ögonblicks bilder. I så fall [tar du bort säkerhets kopierings principerna](../articles/storsimple/storsimple-8000-manage-backup-policies-u2.md#delete-a-backup-policy). Detta tar även bort moln ögonblicks bilderna. 
 
När volym containern inte har några associerade volymer, säkerhets kopierings principer och moln ögonblicks bilder kan du ta bort den. Utför följande procedur för att ta bort en volym behållare.

#### <a name="to-delete-a-volume-container"></a>Ta bort en volym behållare

1. Gå till StorSimple Device Manager-tjänsten och klicka på **Enheter**. Markera och klicka på enheten och gå sedan till **inställningar > hantera > volym behållare**.

    ![Bladet volym behållare](./media/storsimple-8000-delete-volume-container/create-volume-container.png)

2. Välj den volym behållare som du vill ta bort i tabell listan över volym behållare, högerklicka på **...** och välj sedan **ta bort**.

    ![Ta bort volym behållare](./media/storsimple-8000-delete-volume-container/delete-volume-container-01.png)

3. Om en volym behållare saknar associerade volymer, säkerhets kopierings principer och moln ögonblicks bilder kan den tas bort. När du uppmanas att bekräfta, granskar och markerar du kryss rutan som visar effekten av att ta bort volym containern. Klicka på **ta bort** för att ta bort volym containern.

    ![Bekräfta borttagning](./media/storsimple-8000-delete-volume-container/delete-volume-container-02.png)

Listan över volym behållare uppdateras för att avspegla den borttagna volym containern.

![Skärm bild av sidan med volym behållare. Tabell listan över volym behållare innehåller inte längre den borttagna behållaren.](./media/storsimple-8000-delete-volume-container/delete-volume-container-05.png)
