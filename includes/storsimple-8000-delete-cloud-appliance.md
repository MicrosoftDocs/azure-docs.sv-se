---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: ac708eb2ac79a74b8f4e09a7306a42665b3aca94
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "67187400"
---
#### <a name="to-delete-a-cloud-appliance"></a>Så här tar du bort en molninstallation

1. Logga in på Azure-portalen.
2. Du kan bara ta bort en inaktiverad enhet som inte innehåller data. Ta bort alla data på enheten först. Du kan även [redundansväxla data](../articles/storsimple/storsimple-8000-device-failover-cloud-appliance.md) i volymcontainrar till en annan enhet. När alla data tagits bort är du färdig och kan inaktivera enheten.
3. Klicka på **Enheter** på tjänstsidan för StorSimple Device Manager och markera sedan enheten. Högerklicka och välj **Inaktivera**.
4. När enheten är inaktiverad högerklickar du på enheten och väljer **Ta bort**.

    ![Välj den inaktiverade enheten och klicka på ta bort](./media/storsimple-8000-delete-cloud-appliance/delete-cloud-appliance1.png)

5. Ange enhetsnamnet för att bekräfta borttagningen. När enheten har tagits bort uppdateras listan över enheter.

    ![Bekräfta borttagning](./media/storsimple-8000-delete-cloud-appliance/delete-cloud-appliance2.png)

6. Du får ett meddelande när enheten har tagits bort.

    ![Meddelande för lyckad borttagning av enhet](./media/storsimple-8000-delete-cloud-appliance/delete-cloud-appliance4.png)

7. Listan över enheter uppdateras för att visa att enheten tagits bort.

    ![Uppdaterad enhetslista](./media/storsimple-8000-delete-cloud-appliance/delete-cloud-appliance5.png)
