---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 720288aff462b0590bb9da509096a9305b9b6cc7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "67187378"
---
#### <a name="to-install-maintenance-mode-updates-via-windows-powershell-for-storsimple"></a>Så här installerar du uppdateringar för underhålls läge via Windows PowerShell för StorSimple
1. Om du inte redan har gjort det går du till enhetens serie konsol och väljer alternativ 1, **loggar in med fullständig åtkomst**. 
2. Ange lösen ordet. Standard lösen ordet är **Password1**.
3. Skriv följande i kommandotolken:
   
     `Get-HcsUpdateAvailability` 
4. Du får ett meddelande om uppdateringar är tillgängliga och om uppdateringarna är störande eller inte störda. Om du vill tillämpa störnings uppdateringar måste du försätta enheten i underhålls läge. Se [steg 2: Ange underhålls läge](../articles/storsimple/storsimple-update-device.md#step2) för instruktioner.
5. När enheten är i underhålls läge skriver du följande i kommando tolken: `Start-HcsUpdate`
6. Du uppmanas att bekräfta. När du har bekräftat uppdateringarna installeras de på den kontroll enhet som du för närvarande använder. Efter att uppdateringarna har installerats startar kontrollanten om. 
7. Övervaka status för uppdateringar. Ange:
   
    `Get-HcsUpdateStatus`
   
    Om `RunInProgress` är `True` uppdateras fortfarande uppdateringen. Om `RunInProgress` är `False` , anger det att uppdateringen har slutförts.  
8. När uppdateringen är installerad på den aktuella styrenheten och den har startats om ansluter du till den andra kontrollanten och utför steg 1 till 6.
9. Avsluta underhålls läget när båda styrenheterna har uppdaterats. Se [steg 4: avsluta underhålls läget](../articles/storsimple/storsimple-update-device.md#step4) för instruktioner.

