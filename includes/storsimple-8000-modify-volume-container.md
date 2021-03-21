---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 02/09/2021
ms.author: alkohli
ms.openlocfilehash: 71f18cf8448060385ea38be9b2719b1ed545c5d2
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "100545426"
---
> [!NOTE] 
> Du kan inte ändra krypterings inställningarna och autentiseringsuppgifterna för lagrings kontot som är associerat med en volym behållare när den har skapats.

#### <a name="to-modify-a-volume-container"></a>Ändra en volym behållare

1. Gå till StorSimple Enhetshanteraren-tjänsten och gå sedan till **hanterings > volym behållare**.

2. Välj den volym behållare som du vill ändra i tabell listan över volym behållare. På sidan **enheter** väljer du enheten, dubbelklickar på den och klickar sedan på fliken **volym behållare** .

3. I Tabellista för volym containrar väljer du den volym behållare som du vill ändra. På bladet som öppnas klickar du på **ändra** i kommando fältet.

    ![Ändra volym container](./media/storsimple-8000-modify-volume-container/modify-volume-container-01.png)

4. Utför följande steg på bladet **ändra volym behållare** :
   
   1. Namn, krypterings nyckel och lagrings konto som är kopplat till volym containern kan inte ändras efter att de har angetts. Ändra den associerade bandbredds inställningen.<!--STEPS NEED WORK. Updated screen doesn't show alternative to Unlimited or subsequent steps if they customize bandwidth. Can we talk them through this (briefly)?-->
      
       ![Ändra bandbredds inställning](./media/storsimple-8000-modify-volume-container/modify-volume-container-02.png)<!--New graphic based on: modify-volume-container-bw-setting.png-->

   1.  Klicka på **OK**.<!--If they choose Custom, do they still click OK, or are there more steps?-->

5. På nästa sida i dialog rutan **ändra volym behållare** :<!--This step happens only if they choose Custom bandwidth? Are the steps similar to those in "Add volume container," step 3f, above?"-->
   
   1. Välj en befintlig bandbredds mal len i list rutan.
   1. Granska schema inställningarna för den angivna bandbredds mal len.
   1. Klicka på **Spara** och bekräfta ändringarna.
      
       ![Bekräfta ändringar](./media/storsimple-8000-modify-volume-container/modify-volume-container-03.png)

      Bladet **volym behållare** uppdateras för att avspegla ändringarna.
