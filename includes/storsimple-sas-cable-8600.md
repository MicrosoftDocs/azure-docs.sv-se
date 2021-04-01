---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: f08a6b3f7abfc79bff6baff2a339053905612535
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96027634"
---
#### <a name="to-attach-the-sas-cables"></a>Så här kopplar du SAS-kablar
1. Identifiera de primära och EBOD-höljen. De två höljen kan identifieras genom att titta på deras respektive back plan. Se följande bild för vägledning. 
   
    ![Bak plan för primär-och EBOD-höljen](./media/storsimple-sas-cable-8600/HCSBackplaneofprimaryandEBODenclosure.png)
   
    **Tillbaka vy över primär-och EBOD-höljen**
   
   | Etikett | Beskrivning |
   |:--- |:--- |
   | 1 |Primärt kabinett |
   | 2 |EBOD-hölje |
2. Leta upp serie numren på den primära och EBOD-höljet. Serie numrets klister märke fästs på bak sidan av varje hölje. Serie numren måste vara identiska på båda höljen. [Kontakta Microsoft Support](../articles/storsimple/storsimple-8000-contact-microsoft-support.md) omedelbart om serie numren inte stämmer överens. Se följande bild för att hitta serie numren.
   
    ![Bakre vy över höljet som visar serie nummer](./media/storsimple-sas-cable-8600/HCSRearviewofenclosureindicatinglocationofserialnumbersticker.png)
   
    **Placering av serie nummer etikett**
   
   | Etikett | Beskrivning |
   |:--- |:--- |
   | 1 |Öron av höljet |
3. Använd de angivna SAS-kablarna för att ansluta EBOD-kabinettet till den primära inne slutningen enligt följande:
   
   1. Identifiera de fyra SAS-portarna på den primära inne slutningen och EBOD-höljet. SAS-portarna är märkta som EBOD på den primära inne slutningen och motsvarar port A på EBOD-höljet, som visas i bilden SAS-kablage nedan.
   2. Använd de angivna SAS-kablarna för att ansluta EBOD-porten till port A.
   3. EBOD-porten på styrenhet 0 bör anslutas till porten A på EBOD Controller 0. EBOD-porten på styrenhet 1 ska vara ansluten till port A på EBOD Controller 1. Se följande illustration för vägledning. 
      
      ![SAS-kablar för din enhet](./media/storsimple-sas-cable-8600/HCSSAScablingforyourdevice.png)
      
      **SAS-kablar**
      
      | Etikett | Beskrivning |
      |:--- |:--- |
      | A |Primärt kabinett |
      | B |EBOD-hölje |
      | 1 |Kontrollant 0 |
      | 2 |Kontrollant 1 |
      | 3 |EBOD Controller 0 |
      | 4 |EBOD Controller 1 |
      | 5, 6 |SAS-portar på primär kabinett (märkta EBOD) |
      | 7, 8 |SAS-portar på EBOD-kabinettet (port A) |