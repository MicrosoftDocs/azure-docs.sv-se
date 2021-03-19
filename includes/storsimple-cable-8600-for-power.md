---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 9b9922602218280d58331a755ed0dfed7df96f40
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "67187379"
---
#### <a name="to-cable-your-device-for-power"></a>Så här kabelansluter du enheten för strömförsörjning
> [!NOTE]
> Båda höljen på din StorSimple-enhet innehåller redundant PCMs. För varje inne slutning måste PCMs installeras och anslutas till olika ström källor för att säkerställa hög tillgänglighet.
> 
> 

1. Se till att ström växlarna på alla PCMs är på plats.
2. På den primära inne slutningen ansluter du ström sladdarna till båda PCMs. Ström sladdarna identifieras i rött i diagrammet med ström kablar, nedan.
3. Se till att de två PCMs på den primära inne slutningen använder separata ström källor.
4. Koppla ström sladdarna till kraften på rack distributions enheterna som visas i diagram över ström kablar.
5. Upprepa steg 2 till 4 för EBOD-höljet.
6. Aktivera EBOD-kammaren genom att vända ström växeln på varje PCM till den här platsen.
7. Kontrol lera att EBOD-kammaren är aktive rad genom att kontrol lera att de gröna lysdioderna på bak sidan av EBOD-styrenheten är aktiverade.
8. Aktivera den primära inne slutningen genom att vända varje PCM-växel till den här platsen.
9. Kontrol lera att systemet är på genom att se till att enhets kontrollernas indikatorer har Aktiver ATS.
10. Kontrol lera att anslutningen mellan EBOD-styrenheten och enhets styrenheten är aktiv genom att kontrol lera att de fyra lysdioderna bredvid SAS-porten på EBOD-styrenheten är gröna.
    
    > [!IMPORTANT]
    > För att säkerställa hög tillgänglighet för ditt system rekommenderar vi att du följer det energi kabel schema som visas i följande diagram.
    > 
    > 
    
    ![Kabelanslut din 4U-enhet för strömförsörjning](./media/storsimple-cable-8600-for-power/HCSCableYour4UDeviceforPower.png)
    
    **Ström kablar**
    
    | Etikett | Beskrivning |
    |:--- |:--- |
    | 1 |Primärt kabinett |
    | 2 |PCM 0 |
    | 3 |PCM 1 |
    | 4 |Kontrollant 0 |
    | 5 |Kontrollant 1 |
    | 6 |EBOD Controller 0 |
    | 7 |EBOD Controller 1 |
    | 8 |EBOD-hölje |
    | 9 |PDU |

