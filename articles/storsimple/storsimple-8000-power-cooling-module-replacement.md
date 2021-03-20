---
title: Ersätt en PCM på din StorSimple 8000-serie enhet | Microsoft Docs
description: Förklarar hur du tar bort och byter ut energi-och kylnings modulen (PCM) på din StorSimple-enhet
services: storsimple
documentationcenter: ''
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: troubleshooting
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/02/2017
ms.author: alkohli
ms.openlocfilehash: 045cec85174a88d1d608a4adc679461008852768
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "85514597"
---
# <a name="replace-a-power-and-cooling-module-on-your-storsimple-device"></a>Ersätta en energi- och kylningsmodul på StorSimple-enheten
## <a name="overview"></a>Översikt
Power and kylning module (PCM) i Microsoft Azure StorSimple-enheten består av en strömförsörjnings-och kyl fläktar som styrs av de primära och EBOD-höljen. Det finns bara en modell av PCM som är certifierad för varje inne slutning. Den primära inne slutningen är certifierad för en 764 W PCM och EBOD-höljet är certifierat för en 580 W PCM. Även om PCMs för den primära inne slutningen och EBOD-kammaren är olika är ersättnings proceduren identisk.

I den här självstudien beskrivs hur du:

* Ta bort en PCM
* Installera en ny PCM

> [!IMPORTANT]
> Innan du tar bort och ersätter en PCM bör du gå igenom säkerhets informationen i [StorSimple för maskin varu komponenter](storsimple-8000-hardware-component-replacement.md).


## <a name="before-you-replace-a-pcm"></a>Innan du ersätter en PCM
Tänk på följande viktiga problem innan du ersätter PCM:

* Om strömförsörjningen av PCM Miss lyckas lämnar du modulen för fel installerad, men tar bort ström sladden. Fläkten fortsätter att ta emot ström från inne slutningen och fortsätter att tillhandahålla korrekt kylning. Om fläkten Miss lyckas måste PCM ersättas omedelbart.
* Innan du tar bort PCM tar du bort kraften från PCM genom att stänga av huvud växeln (där det finns) eller genom att fysiskt ta bort ström sladden. Detta ger en varning om systemet att en strömavbrott är överhäng Ande.
* Se till att den andra PCM fungerar för fortsatt system drift innan du ersätter den felaktiga PCM. En felaktig PCM måste ersättas av en fullständigt fungerande PCM så snart som möjligt.
* Ersättning av PCM-modulen tar bara några minuter att slutföra, men den måste slutföras inom 10 minuter från det att det inte gick att ta bort den misslyckade PCM för att förhindra överhettning.
* Observera att de byte 764 W PCM-moduler som levereras från fabriken inte innehåller modulen för säkerhets kopierings batteri. Du måste ta bort batteriet från den felaktiga PCM och sedan infoga det i den nya modulen innan du utför ersättnings åtgärden. Mer information finns i så här [tar du bort och infogar en modul för säkerhets kopierings batteri](storsimple-8000-battery-replacement.md).

## <a name="remove-a-pcm"></a>Ta bort en PCM
Följ dessa anvisningar när du är redo att ta bort en Power-och kylnings modul (PCM) från din Microsoft Azure StorSimple-enhet.

> [!NOTE]
> Innan du tar bort din PCM måste du kontrol lera att du har rätt ersättnings (764 W för den primära inne slutningen eller 580 W för EBOD-kammaren).

#### <a name="to-remove-a-pcm"></a>Ta bort en PCM
1. I den klassiska Azure-portalen klickar du på **inställningar > övervaka > maskin varu hälsa**. Kontrol lera status för PCM-komponenterna under **delade komponenter** för att identifiera vilken PCM som misslyckades:
   
   * Om en strömförsörjning i PCM 0 har misslyckats blir statusen för **strömförsörjningen i PCM 0** röd.
   * Om en strömförsörjning i PCM 1 har misslyckats blir statusen för **strömförsörjningen i PCM 1** röd.
   * Om fläkten i PCM 1 har misslyckats blir statusen för antingen **kylning 0 för PCM 0** eller **kylning 1 för PCM 0** röd.
2. Leta upp det misslyckade PCM på bak sidan av den primära inne slutningen. Om du kör en 8600-modell identifierar du den primära inne slutningen genom att titta på system enhetens identifierings nummer som visas på front panelens indikator visas. Standardenhets-ID som visas i den primära inne slutningen är **00**, medan standardenhets-ID: t som visas i EBOD-kammaren är **01**. I följande diagram och tabell förklaras INDIKATORns front panel.
   
    ![System-ID på front OPS panel](./media/storsimple-power-cooling-module-replacement/IC740991.png)
   
     **Bild 1** Enhetens Front Panel  
   
   | Etikett | Beskrivning |
   |:--- |:--- |
   | 1 |Knappen ljud av |
   | 2 |Systemets strömförsörjning |
   | 3 |Modul-fel |
   | 4 |Logiskt fel |
   | 5 |Visning av enhets-ID |
3. Övervaknings indikator lamporna på bak sidan av den primära inne slutningen kan också användas för att identifiera felet PCM. Se följande diagram och tabell för att lära dig hur du använder lysdioderna för att hitta den felande PCM. Till exempel, om INDIKATORn som motsvarar **fläkten Miss lyckas** , är fläkten klar. På samma sätt har strömförsörjningen misslyckats om LAMPAn som motsvarar **AC** misslyckades är tänd. 
   
    ![Bakplanering av enhetens PCM för att övervaka indikatorer](./media/storsimple-power-cooling-module-replacement/IC740992.png)
   
     **Bild 2** Bak från PCM med indikator lampor
   
   | Etikett | Beskrivning |
   |:--- |:--- |
   | 1 |AC-strömavbrott |
   | 2 |Fläkt haveri |
   | 3 |Batteri fel |
   | 4 |PCM OK |
   | 5 |LIKSTRÖMs strömavbrott |
   | 6 |Batteriet är felfritt |
4. Se följande diagram över bak sidan av StorSimple-enheten för att hitta den misslyckade PCM-modulen. PCM 0 är till vänster och PCM 1 är till höger. I tabellen nedan beskrivs moduler.
   
     ![Bakplanering av enhetens primära inne slutnings moduler](./media/storsimple-power-cooling-module-replacement/IC740994.png)
   
     **Bild 3** Tillbaka till enheten med plugin-moduler 
   
   | Etikett | Beskrivning |
   |:--- |:--- |
   | 1 |PCM 0 |
   | 2 |PCM 1 |
   | 3 |Kontrollant 0 |
   | 4 |Kontrollant 1 |
5. Stäng av den felaktiga PCM och koppla från ström sladden. Nu kan du ta bort PCM.
6. Grepp lås och sidan om PCM-handtaget mellan ditt tumme och pekfingret och sammanför dem för att öppna handtaget.
   
    ![Öppnar PCM-handtag](./media/storsimple-power-cooling-module-replacement/IC740995.png)
   
    **Bild 4** Öppna PCM-handtaget
7. Grepp i handtaget och ta bort PCM.
   
    ![Tar bort enhet PCM](./media/storsimple-power-cooling-module-replacement/IC740996.png)
   
    **Figur 5** Ta bort PCM

## <a name="install-a-replacement-pcm"></a>Installera en ny PCM
Följ de här anvisningarna för att installera en PCM på din StorSimple-enhet. Kontrol lera att du har infogat modulen för säkerhets kopierings batteri innan du installerar den nya PCM (gäller 764 W endast PCMs). Mer information finns i så här [tar du bort och infogar en modul för säkerhets kopierings batteri](storsimple-8000-battery-replacement.md).

#### <a name="to-install-a-pcm"></a>Så här installerar du en PCM
1. Kontrol lera att du har rätt ersättnings-PCM för denna inne slutning. Den primära inne slutningen behöver en 764 W PCM och EBOD-kabinettet måste ha 580 W PCM. Du bör inte försöka använda 580 W PCM i den primära inne slutningen eller 764 W PCM i EBOD-höljet. Följande bild visar var du kan identifiera den här informationen på etiketten som är fäst på PCM.
   
    ![Enhets-PCM-etikett](./media/storsimple-power-cooling-module-replacement/IC740973.png)
   
    **Bild 6** PCM-etikett
2. Kontrol lera om det finns skada på höljet och betala särskilt uppmärksamheten för anslutningarna. 
   
   > [!NOTE]
   > **Installera inte modulen om några kopplings stift är böjda.**
   > 
   > 
3. Med PCM-handtaget i den öppna positionen drar du modulen till inne slutningen.
   
    ![Installerar enhets-PCM](./media/storsimple-power-cooling-module-replacement/IC740975.png)
   
    **Figur 7** Installera PCM
4. Stäng PCM-handtaget manuellt. Du bör höra ett klick när handtaget spärras.
   
   > [!NOTE]
   > För att säkerställa att kopplings stiften har Aktiver ATS kan du försiktigt Tug i handtaget utan att släppa låset. Om PCM-bilderna ut innebär det att låsen stängdes innan kopplingarna var aktiverade.
   
5. Anslut ström sladdarna till ström källan och till PCM.
6. Skydda Bales för begränsning av stammar.
7. Aktivera PCM.
8. Kontrol lera att ersättningen lyckades: i Azure Portal av din StorSimple Enhetshanteraren-tjänst navigerar du till enheten och sedan till **inställningar > övervaka > maskin varu hälsa**. Under **delade komponenter** måste status för PCM vara grönt.
   
   > [!NOTE]
   > Det kan ta några minuter innan den nya PCM är helt initierad.

## <a name="next-steps"></a>Nästa steg
Läs mer om [StorSimple av maskin varu komponenter](storsimple-8000-hardware-component-replacement.md).

