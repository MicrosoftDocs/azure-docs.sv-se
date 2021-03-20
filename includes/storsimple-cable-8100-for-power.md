---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: db2104020e9478b1fedf68e1c9467f75e16044e2
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "67187380"
---
#### <a name="to-cable-for-power"></a>Till kabel för ström
1. Se till att ström växlarna på alla Power-och kylnings moduler (PCMs) är på plats.
2. Anslut ström sladdarna till var och en av PCMs i den primära inne slutningen.
3. Koppla ström sladdarna till rackets distributions enheter (PDU) som visas i följande bild. Kontrol lera att de två PCMs använder separata ström källor.
   
   > [!IMPORTANT]
   > För att säkerställa hög tillgänglighet för ditt system rekommenderar vi att du följer det energi kabel schema som visas i följande diagram. 
   > 
   > 
   
    ![Kabelanslut din 2U-enhet för strömförsörjning](./media/storsimple-cable-8100-for-power/HCSCableYour2UDeviceforPower.png)
   
    **Ström kablar på en 8100-enhet**
   
   | Etikett | Beskrivning |
   |:--- |:--- |
   | 1 |PCM 0 |
   | 2 |Kontrollant 1 |
   | 3 |Kontrollant 0 |
   | 4 |PCM 1 |
   | 5 |PDU |
4. Om du vill aktivera systemet vänder du tillbaka ström växlarna på båda PCMs.

