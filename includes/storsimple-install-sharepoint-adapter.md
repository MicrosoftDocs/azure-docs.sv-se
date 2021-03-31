---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: e2d258f327ade2149adc92bdb7fb4bcc87cdd58c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "67187384"
---
#### <a name="to-install-the-storsimple-adapter-for-sharepoint"></a>Så här installerar du StorSimple Adapter för SharePoint
1. Kopiera installations programmet till webb server delen (WFE) som också är konfigurerad för att köra SharePoint-webbprogrammet för Central administration. 
2. Använd ett konto med administratörs behörighet för att logga in på WFE-servern.
3. Dubbelklicka på installations programmet. Installations guiden för StorSimple Adapter för SharePoint startas. Klicka på **Nästa** för att påbörja installationen.
   
    ![Start sida för StorSimple Adapter setup](./media/storsimple-install-sharepoint-adapter/HCS_SSASP_Setup1-include.png)
4. På sidan konfiguration av installations program för StorSimple Adapter för SharePoint väljer du en installations plats, anger IP-adressen för nätverks gränssnittet för DATA 0 på din StorSimple-enhet och klickar sedan på **Nästa**. 
   
    ![Konfigurations sida för StorSimple Adapter setup](./media/storsimple-install-sharepoint-adapter/HCS_SSASP_Setup2-include.png) 
5. På sidan installations bekräftelse klickar du på **Installera**.
   
    ![Bekräftelse sida för StorSimple Adapter](./media/storsimple-install-sharepoint-adapter/HCS_SSASP_Confirm_Setup-include.png) 
6. Klicka på **Slutför** för att stänga installations guiden.
   
    ![Sidan installation av StorSimple Adapter har slutförts](./media/storsimple-install-sharepoint-adapter/HCS_SSASP_Setup_finish-include.png) 
7. Öppna sidan Central administration av SharePoint. Du bör se en konfigurations grupp för StorSimple som innehåller StorSimple Adapter för SharePoint-länkar.
8. Gå till nästa steg: [Konfigurera RBS](#configure-rbs).

