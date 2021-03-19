---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: a5a286753e438b7d65f3d33a82669c4f7e79a282
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "86544515"
---
#### <a name="to-create-public-endpoints-on-the-cloud-appliance"></a>Så här skapar du offentliga slutpunkter för molninstallationen

1. Logga in på Azure-portalen.
2. Gå till **Virtual Machines** och markera och klicka på den virtuella dator som används som molninstallation.
    
3. Du måste skapa en NSG-regel (nätverkssäkerhetsgrupp) för att styra flödet av trafik in till och ut från den virtuella datorn. Skapa en NSG-regel genom att utföra stegen nedan.
    1. Välj **Nätverkssäkerhetsgrupp**.
        ![Skärm bild av sidan för den virtuella datorn. I avsnittet Inställningar markeras nätverks säkerhets gruppen.](./media/storsimple-8000-create-public-endpoints-cloud-appliance/sca-create-public-endpt1.png)

    2. Klicka på standardnätverkssäkerhetsgruppen.
        ![Skärm bild av sidan Nätverks säkerhets grupp. Standard nätverks säkerhets gruppen är markerad.](./media/storsimple-8000-create-public-endpoints-cloud-appliance/sca-create-public-endpt2.png)

    3. Välj **Ingående säkerhetsregler**.
        ![Skärm bild av en sida som visar egenskaperna för standard nätverks säkerhets gruppen. I navigerings fönstret markeras inkommande säkerhets regler.](./media/storsimple-8000-create-public-endpoints-cloud-appliance/sca-create-public-endpt3.png)

    4. Skapa en säkerhetsregel för inkommande trafik genom att klicka på **+ Lägg till**.
        ![Skärm bild av sidan inkommande säkerhets regler. Plus tecknet och ordet Lägg till visas bredvid varandra och är markerade.](./media/storsimple-8000-create-public-endpoints-cloud-appliance/sca-create-public-endpt4.png)

        På bladet Lägg till inkommande säkerhetsregel:

        1. I fältet **Namn** anger du följande namn för slutpunkten: WinRMHttps.
        
        2. För **Prioritet** väljer du ett tal som är mindre än 1 000 (som är prioriteten för standardregeln). Desto högre värde, ju lägre prioritet.

        3. Ange **Källa** till **Alla**.

        4. För **Tjänst** väljer du **WinRM**. **Protokoll** ställs automatiskt in till **TCP**, och **Portintervall** till **5986**.

        5. Skapa regeln genom att klicka på **OK**.

            ![Skärm bild av bladet Lägg till inkommande säkerhets regel. Värdena fylls i enligt beskrivningen i proceduren och knappen OK markeras.](./media/storsimple-8000-create-public-endpoints-cloud-appliance/sca-create-public-endpt5.png)

4. Det sista steget är att associera nätverkssäkerhetsgruppen med ett undernät eller ett visst nätverksgränssnitt. Du associerar nätverkssäkerhetsgruppen med ett undernät genom att utföra stegen nedan.
    1. Gå till **Undernät**.
    2. Klicka på **+ Associera**.
        ![Skärm bild av sidan under nät. Plus tecknet och ordet associeras bredvid varandra och är markerade.](./media/storsimple-8000-create-public-endpoints-cloud-appliance/sca-create-public-endpt7.png)

    3. Välj ditt virtuella nätverk och välj sedan lämpligt undernät.
    4. Skapa regeln genom att klicka på **OK**.

        ![Skärm bild av sidan associera undernät. Det virtuella nätverket är valt och knappen OK är markerad.](./media/storsimple-8000-create-public-endpoints-cloud-appliance/sca-create-public-endpt11.png)

När regeln har skapats kan du visa information om regeln för att ta reda på den offentliga virtuella IP-adressen (VIP). Anteckna den adressen.


