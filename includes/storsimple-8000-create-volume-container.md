---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 02/09/2021
ms.author: alkohli
ms.openlocfilehash: fd9b3b501d6efbe6a74d350a678494e8254dbb32
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "100545408"
---
#### <a name="to-create-a-volume-container"></a>Skapa en volymcontainer

1. Gå till StorSimple Device Manager-tjänsten och klicka på **Enheter**. Markera och klicka på en enhet i tabellistan med enheter. 

    ![Blad för volymcontainer](./media/storsimple-8000-create-volume-container/create-volume-container-01.png)

2. Klicka på **+ Lägg till volymcontainer** på instrumentpanelen för enheten

    ![Bladet volym behållare 2](./media/storsimple-8000-create-volume-container/create-volume-container-02.png)

3. På bladet **Lägg till volymcontainer**:
   
   1. Enheten markeras automatiskt.
   2. Ange ett **namn** för din volymcontainer. Namnet måste vara mellan 3 och 32 tecken. Du kan byta namn på en volymcontainer när den har skapats.
   3. Välj **Aktivera kryptering av molnlagring** för att aktivera kryptering av data som skickas från enheten till molnet.
   4. Ange och bekräfta en **krypteringsnyckel för molnlagring** som är 8 till 32 tecken. Nyckeln används av enheten för att få åtkomst till krypterad data.
   5. Ange ett **lagringskonto** att associera med volymcontainern. Du kan välja ett befintligt lagringskonto eller standardkontot som genereras när tjänsten skapas. Du kan också använda alternativet **Lägg till ny** för att ange ett lagringskonto som inte är länkad till den här tjänstprenumerationen.
   6. Välj **Obegränsad** i listrutan **Ange bandbredd** om du vill använda all tillgänglig bandbredd. Du kan också välja alternativet **Anpassat** om du vill använda bandbreddskontroller, och sedan ange ett värde mellan 1 Mbit/s och 1 000 Mbit/s.
   
      Om du har informationen om din bandbreddsanvändning tillgänglig, kan du allokera bandbredd enligt ett schema genom att ange **Välj en bandbreddsmall**. Stegvisa instruktioner för proceduren finns i [Lägg till en bandbreddsmall](../articles/storsimple/storsimple-8000-manage-bandwidth-templates.md#add-a-bandwidth-template).

      ![Volym container blad 3](./media/storsimple-8000-create-volume-container/create-volume-container-06-b.png)<!--New graphic. Source: add-volume-container-bw-setting.-->

   7. Klicka på **Skapa**.

        <!--![Volume container blade 4](./media/storsimple-8000-create-volume-container/create-volume-container-06.png)-->
   
       Du får ett meddelande när volymcontainern har skapats.

       ![Meddelande om att volymcontainern har skapats](./media/storsimple-8000-create-volume-container/create-volume-container-08.png)

   Den nya volymcontainern visas i listan med volymcontainrar för enheten.

   ![Bladet Lägg till volymcontainer](./media/storsimple-8000-create-volume-container/create-volume-container-09.png)
