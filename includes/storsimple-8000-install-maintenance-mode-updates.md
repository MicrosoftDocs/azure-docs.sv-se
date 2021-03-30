---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 560c9c177bfa693580979101e5b9343fcff7fe40
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "67187539"
---
### <a name="install-maintenance-mode-updates-via-windows-powershell-for-storsimple"></a>Installera uppdateringar av underhålls läge via Windows PowerShell för StorSimple

När du tillämpar uppdateringar av underhålls läge på StorSimple-enheten pausas alla I/O-begäranden. Tjänster som icke-flyktigt RAM-minne (Random Access Memory) eller kluster tjänsten stoppas. Båda styrenheterna startar om när du anger eller avslutar det här läget. När du avslutar det här läget fortsätter alla tjänster att vara igång och är felfria. (Det kan ta några minuter.)

> [!IMPORTANT]
> * Innan du startar underhålls läget måste du kontrol lera att båda enhets styrenheterna är felfria i Azure Portal. Om kontrollanten inte är felfri [kontaktar du Microsoft Support](../articles/storsimple/storsimple-8000-contact-microsoft-support.md) för nästa steg.
> * När du är i underhålls läge måste du först uppdatera en kontrollant och sedan den andra kontrollanten.

1. Använd SparaTillFil för att ansluta till serie konsolen. Följ de detaljerade instruktionerna i [Använd PuTTY för att ansluta till enhetens seriekonsol](../articles/storsimple/storsimple-8000-deployment-walkthrough-u2.md#use-putty-to-connect-to-the-device-serial-console). Tryck på **Retur** i kommandotolken. Välj alternativ 1, **Logga in med fullständig åtkomst**.

2. Om du vill placera kontrollanten i underhålls läge, skriver du:
    
    `Enter-HcsMaintenanceMode`

    Båda styrenheterna startas om i underhålls läge.

3. Installera uppdateringar av underhålls läge. Ange:

    `Start-HcsUpdate`

    Du uppmanas att bekräfta. När du har bekräftat uppdateringarna installeras de på den kontroll enhet som du för närvarande använder. När uppdateringarna har installerats startar kontrollanten om.

4. Övervaka status för uppdateringar. Logga in på peer-styrenheten när den aktuella styrenheten uppdateras och kan inte bearbeta några andra kommandon. Ange:

    `Get-HcsUpdateStatus`

    Om `RunInProgress` är `True` uppdateras fortfarande uppdateringen. Om `RunInProgress` är `False` , anger det att uppdateringen har slutförts.

5. När diskens inbyggda program uppdateringar har tillämpats och den uppdaterade styrenheten har startats om kontrollerar du diskens inbyggda version. På den uppdaterade kontrollanten skriver du:

    `Get-HcsFirmwareVersion`
   
    De förväntade versionerna av inbyggd program vara:  `XMGJ, XGEG, KZ50, F6C2, VR08, N003, 0107`

6. Avsluta underhålls läget. Skriv följande kommando för varje enhets styrenhet:

    `Exit-HcsMaintenanceMode`

    Styrenheterna startas om när du avslutar underhållsläget.

7. Gå tillbaka till Azure-portalen. Portalen kanske inte visar att du har installerat uppdatering av underhålls läge i 24 timmar.