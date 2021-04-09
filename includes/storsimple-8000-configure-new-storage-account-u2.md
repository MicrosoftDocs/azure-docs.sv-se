---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 9f1c30b303bd1fe02e0685c7d848be92073ca2f6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96016885"
---
#### <a name="to-add-a-storage-account-credential-in-the-same-azure-subscription-as-the-storsimple-device-manager-service"></a>Så här lägger du till autentiseringsuppgifter för ett lagringskonto i samma Azure-prenumeration som StorSimple Device Manager-tjänsten

1. Gå till StorSimple Device Manager-tjänsten. Klicka på **Autentiseringsuppgifter för lagringskonto** i avsnittet **Konfiguration**.

    ![Gå till autentiseringsuppgifter för lagrings konto](./media/storsimple-8000-configure-new-storage-account-u2/createnewstorageacct1.png)

2. Klicka på **+ Lägg till** på bladet **Autentiseringsuppgifter för lagringskonto**.

    ![Lägg till autentiseringsuppgift för lagringskonto](./media/storsimple-8000-configure-new-storage-account-u2/createnewstorageacct2.png)

3. Utför följande steg på bladet **Lägg till autentiseringsuppgift för lagringskonto**:

    1. När du lägger till en autentiseringsuppgift för ett lagringskonto i samma Azure-prenumeration som din tjänst kontrollerar du att **Aktuell** är markerat.

    2. Välj ett befintligt lagringskonto i listrutan **lagringskonto**.

    3. **Platsen** visas baserat på vilket lagringskonto du valt (nedtonad och kan inte ändras här).

    4. Välj **Aktivera SSL-läge** om du vill skapa en säker kanal för nätverkskommunikation mellan din enhet och molnet. Inaktivera endast **Aktivera SSL** om du arbetar i ett privat moln.

        ![Bladet Lägg till autentiseringsuppgifter för lagringskonto](./media/storsimple-8000-configure-new-storage-account-u2/createnewstorageacct3.png)

    5. Skapa autentiseringsuppgifterna för lagringskontot genom att klicka på **Lägg till**. Du meddelas när autentiseringsuppgifterna för lagringskontot har skapats.

        ![Meddelande om att autentiseringsuppgifterna för lagringskontot har skapats](./media/storsimple-8000-configure-new-storage-account-u2/createnewstorageacct5.png)

De nya autentiseringsuppgifterna för lagringskontot visas under listan **Autentiseringsuppgifter för lagringskonto**.

![Lista autentiseringsuppgifter för lagrings kontot](./media/storsimple-8000-configure-new-storage-account-u2/createnewstorageacct6.png)

