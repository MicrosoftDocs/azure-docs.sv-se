---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: dc50f94ae9b207961a71480c2fc172e88db79cf4
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "67187376"
---
#### <a name="to-install-regular-updates-via-windows-powershell-for-storsimple"></a>Så här installerar du regelbundna uppdateringar via Windows PowerShell för StorSimple
1. Öppna enhetens serie konsol och välj alternativ 1, **Logga in med fullständig åtkomst**. Ange lösen ordet. Standard lösen ordet är *Password1*. 
2. Skriv följande i kommandotolken:
   
     `Get-HcsUpdateAvailability`
   
    Du får ett meddelande om uppdateringar är tillgängliga och om uppdateringarna är störande eller inte störda.
3. Skriv följande i kommandotolken:
   
     `Start-HcsUpdate`
   
    Uppdaterings processen kommer att starta.

> [!IMPORTANT]
> * Detta kommando gäller endast för vanliga uppdateringar. Du kör bara det här kommandot på en kontroll enhet, men båda styrenheterna kommer att uppdateras. 
> * Du kanske upptäcker att en styrenhets fel uppstår under uppdaterings processen. redundansväxlingen kommer dock inte att påverka systemets tillgänglighet eller åtgärd.
> 
> 

