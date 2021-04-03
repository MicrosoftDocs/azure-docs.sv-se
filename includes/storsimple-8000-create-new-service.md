---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 746198f87e23cd7aca2a3177c23974917cb4b12a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96027562"
---
#### <a name="to-create-a-new-service"></a>Skapa en ny tjänst

1. Använd dina Microsoft-kontouppgifter för att logga in på [Azure Portal](https://portal.azure.com/).

2. I Azure Portal klickar du på **Skapa en resurs** och på Marketplace klickar du sedan på **Visa alla**.

    ![Skapa StorSimple Device Manager](./media/storsimple-8000-create-new-service/createssdevman1.png)

    Sök efter _StorSimple Physical_. Markera och klicka på **StorSimple Physical Device Series** och sedan på **Skapa**. Alternativt klickar du på **+** **StorSimple fysisk enhets serie** under **lagring** på Azure Portal.

    ![Skapa StorSimple Enhetshanteraren 2](./media/storsimple-8000-create-new-service/createssdevman11.png)

3. Utför följande steg på bladet för **StorSimple Device Manager**:

   1. Ange ett unikt **resursnamn** för tjänsten. Det här är ett eget namn som kan användas för att identifiera tjänsten. Namnet kan innehålla mellan 2 och 50 tecken som kan vara bokstäver, siffror och bindestreck. Namnet måste börja och sluta med en bokstav eller en siffra.

   2. Välj en **prenumeration** från listrutan. Prenumerationen är kopplad till ditt faktureringskonto. Det här fältet syns inte om du bara har en prenumeration.

   3. Välj **Använd befintlig grupp** eller **Skapa ny grupp** för **Resursgrupp**. Mer information finns i avsnittet om [Azure-resursgrupper](../articles/azure-resource-manager/management/manage-resource-groups-portal.md).

   4. Ange en **plats** för din tjänst. Oftast bör du välja den plats som är närmast den geografiska region där du vill distribuera enheten. Du bör även ha följande i åtanke:

      * Om du har befintliga arbetsbelastningar i Azure som du också tänker distribuera med din StorSimple-enhet, bör du använda det datacentret.
      * StorSimple Device Manager-tjänsten och Azure-lagring kan finnas på två olika platser. I så fall måste du skapa StorSimple Device Manager-kontot och Azure-lagringskontot separat. Du skapar ett Azure-lagringskonto genom att gå till Azure Storage-tjänsten på Azure Portal och följa stegen i [Skapa ett Azure Storage-konto](../articles/storage/common/storage-account-create.md). När du har skapat kontot lägger du till det till StorSimple Device Manager-tjänsten genom att följa stegen i [Konfigurera ett nytt lagringskonto för tjänsten](../articles/storsimple/storsimple-8000-deployment-walkthrough-u2.md#configure-a-new-storage-account-for-the-service).

   5. Välj **Skapa ett nytt lagringskonto**, för att automatiskt skapa ett lagringskonto med tjänsten. Ange ett namn för lagringskontot. Avmarkera kryssrutan om du behöver ha din data på en annan plats.

   6. Markera **Fäst på instrumentpanelen** om du vill skapa en snabblänk till tjänsten på instrumentpanelen.

   7. Skapa StorSimple Device Manager genom att klicka på **Skapa**.

       ![Skapa StorSimple Enhetshanteraren 3](./media/storsimple-8000-create-new-service/createssdevman2.png)

Det tar några minuter att skapa tjänsten. När tjänsten har skapats visas ett meddelande och bladet för den nya tjänsten öppnas.

![Skapa StorSimple Enhetshanteraren 4](./media/storsimple-8000-create-new-service/createssdevman5.png)