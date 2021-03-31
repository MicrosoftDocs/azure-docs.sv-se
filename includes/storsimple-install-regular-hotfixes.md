---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 8cc5dbb907c342b766cebe6da36cf580ddac5e2c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "67187375"
---
#### <a name="to-install-regular-hotfixes-via-windows-powershell-for-storsimple"></a>Installera vanliga snabb korrigeringar via Windows PowerShell för StorSimple
1. Anslut till enhetens serie konsol. Mer information finns i [steg 1: Anslut till serie konsolen](../articles/storsimple/storsimple-update-device.md#step1).
2. I menyn serie konsol väljer du alternativ 1, **loggar in med fullständig åtkomst**. Ange lösen ordet. Standard lösen ordet är **Password1**.
3. Skriv följande i kommandotolken:
   
    ```
    Start-HcsHotfix
    ```
   
    > [!IMPORTANT]
    >
    > Detta kommando gäller endast för vanliga snabb korrigeringar. Du kör bara det här kommandot på en kontroll enhet, men båda styrenheterna kommer att uppdateras.
    > Du kanske upptäcker att en styrenhets fel uppstår under uppdaterings processen. redundansväxlingen kommer dock inte att påverka systemets tillgänglighet eller åtgärd.

4. När du uppmanas till det anger du sökvägen till den delade nätverksmapp som innehåller snabbkorrigeringsfilerna.
5. Du uppmanas att bekräfta. Skriv in **Y** om du vill fortsätta med installationen av snabb korrigeringen.

