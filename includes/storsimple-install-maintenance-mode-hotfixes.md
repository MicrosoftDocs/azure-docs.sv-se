---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 2d0fd904dd704c704662192e1e92fe403f0971c5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "67187373"
---
#### <a name="to-install-maintenance-mode-hotfixes-via-windows-powershell-for-storsimple"></a>Installera snabb korrigeringar för underhålls läge via Windows PowerShell för StorSimple
> [!IMPORTANT]
> I underhålls läge måste du tillämpa snabb korrigeringen först på en kontrollant och sedan på den andra styrenheten.
> 
> 

1. Placera enheten i underhålls läge. Se [steg 2: Ange underhålls läge](../articles/storsimple/storsimple-update-device.md#step2) för instruktioner om hur du anger underhålls läge.
2. Om du vill använda snabb korrigeringen skriver du:
   
     `Start-HcsHotfix` 
3. När du uppmanas till det anger du sökvägen till den delade nätverksmapp som innehåller snabbkorrigeringsfilerna.
4. Du uppmanas att bekräfta. Skriv in **Y** om du vill fortsätta med installationen av snabb korrigeringen.
5. När du har tillämpat snabb korrigeringen på en kontrollant loggar du in på den andra kontrollanten. Använd snabb korrigeringen som du gjorde för föregående kontrollant.
6. Avsluta underhålls läge när snabb korrigeringarna har tillämpats. Se [steg 4: avsluta underhålls läget](../articles/storsimple/storsimple-update-device.md#step4) för instruktioner.

