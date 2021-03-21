---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 8b827a80059cecadd7b76263f13dd4a0d141fc7a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "95555917"
---
#### <a name="to-create-a-cloud-appliance"></a>Så här skapar du en molninstallation

1. Gå till **StorSimple Device Manager**-tjänsten på Azure Portal.
2. Gå till bladet **Enheter**. Klicka på **Skapa molninstallation** från kommandofältet på sammanfattningsbladet för tjänsten.
    ![Skapa StorSimple-molninstallation](./media/storsimple-8000-create-cloud-appliance-u2/sca-create1.png)
3. Ange följande information på bladet **Skapa molninstallation**.
   
    ![StorSimple skapa moln installation 2](./media/storsimple-8000-create-cloud-appliance-u2/sca-create2m.png)
   
   1. **Namn** – Ett unikt namn för molninstallationen.
   2. **Modell** – Välj modell för molninstallationen. En 8010-enhet erbjuder 30 TB standardlagring, medan 8020 har 64 TB Premium Storage. Välj 8010 om du vill distribuera scenarier för hämtning på objektnivå från säkerhetskopior. Välj 8020 om du vill distribuera högpresterande arbetsbelastningar med korta svarstider, eller för användning som en sekundär enhet för haveriberedskap.
   3. **Version** – Välj versionen för molninstallationen. Versionen motsvarar versionen av den virtuella diskens avbildning som används för att skapa molninstallationen. Eftersom molninstallationens version bestämmer vilken fysisk enhet du växlar över eller klonar från är det viktigt att du skapar rätt version för molninstallationen.
   4. **Virtuellt nätverk** – Ange ett virtuellt nätverk som du vill använda med den här virtuella enheten. Om du använder Premium Storage så måste du välja ett virtuellt nätverk som stöds av Premium Storage-kontot. Virtuella nätverk som inte stöds är nedtonade i listrutan. Du får en varning om du väljer ett virtuellt nätverk som inte stöds.
   5. **Undernät** – Associerade undernät visas i listrutan baserat på det virtuella nätverk du valt. Tilldela ett undernät till molninstallationen.
   6. **Lagringskonto** – Välj det lagringskonto där avbildningen av molninstallationen ska lagras under etableringen. Lagringskontot måste finnas i samma region som molninstallationen och det virtuella nätverket. Det bör inte användas för datalagring av vare sig den fysiska enheten eller molninstallationen. Som standard skapas ett nytt lagringskonto för detta ändamål. Men om du vet att du redan har ett lagringskonto som är lämpligt, kan du välja det från listan. Om du skapar en premiummolninstallation visas endast Premium Storage-konton i listan.
      
      > [!NOTE]
      > Molninstallationen fungerar bara med Azure Storage-konton.
    
   7. Markera kryssrutan för att bekräfta att du förstår att data som lagras i molninstallationen finns på ett Microsoft-datacenter.
       * När du bara använder en fysisk enhet så sparas krypteringsnyckeln med din enhet. Microsoft kan därmed inte dekryptera den.

       * När du använder en molninstallation lagras både krypteringsnyckeln och dekrypteringsnyckeln i Microsoft Azure. Mer information finns i [Säkerhetsöverväganden vid användning av en molninstallation](../articles/storsimple/storsimple-8000-security.md).
   8. Etablera molninstallationen genom att klicka på **Skapa**. Enheten kan ta runt 30 minuter för att etableras. Du får ett meddelande när molninstallationen har skapats. Gå till bladet Enheter så uppdateras enhetslistan med molninstallationen. Installationens status är **Redo för installation**.
      
      ![StorSimple-molninstallation som är redo att installeras](./media/storsimple-8000-create-cloud-appliance-u2/sca-create3.png)