---
title: Aktivera eller inaktivera din StorSimple 8000-serien het
description: Förklarar hur du aktiverar en ny StorSimple-enhet, aktiverar en enhet som stängdes av eller förlorat ström och stänger av en enhet som körs.
author: alkohli
ms.assetid: 8e9c6e6c-965c-4a81-81bd-e1c523a14c82
ms.service: storsimple
ms.topic: how-to
ms.date: 01/09/2018
ms.author: alkohli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: c8f4269235c494ff9dd8d1bf8e0ef940562f8927
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "85515277"
---
# <a name="turn-on-or-turn-off-your-storsimple-8000-series-device"></a>Aktivera eller inaktivera din enhet med StorSimple 8000-serien

## <a name="overview"></a>Översikt
Att stänga av en Microsoft Azure StorSimple enhet krävs inte som en del av den normala system åtgärden. Du kan dock behöva aktivera en ny enhet eller en enhet som måste stängas av. I allmänhet krävs en avstängning i fall där du måste ersätta felaktig maskin vara, fysiskt flytta en enhet eller ta en enhet ur drift. I den här självstudien beskrivs den metod som krävs för att aktivera och stänga av StorSimple-enheten i olika scenarier.

## <a name="turn-on-a-new-device"></a>Aktivera en ny enhet
Stegen för att aktivera en StorSimple-enhet för första gången varierar beroende på om enheten är en 8100-eller 8600-modell. 8100 har en enda primär inne slutning, medan 8600 är en enhet med dubbla höljen med en primär inne slutning och en EBOD hölje. De detaljerade stegen för båda modellerna beskrivs i följande avsnitt.

* [Ny enhet med endast primärt kabinett](#new-device-with-primary-enclosure-only)
* [Ny enhet med EBOD-kabinett](#new-device-with-ebod-enclosure)

### <a name="new-device-with-primary-enclosure-only"></a>Ny enhet med endast primärt kabinett
StorSimple 8100-modellen är en enda kabinetten het. Enheten innehåller redundanta Power-och kylnings-moduler (PCMs). Både PCMs måste installeras och anslutas till olika ström källor för att säkerställa hög tillgänglighet.

Utför följande steg för att kabelansluta enheten för strömförsörjning.

[!INCLUDE [storsimple-cable-8100-for-power](../../includes/storsimple-cable-8100-for-power.md)]

> [!NOTE]
> För fullständig enhets konfiguration och kabel instruktioner går du till [installera din StorSimple 8100-enhet](storsimple-8100-hardware-installation.md). Se till att du följer anvisningarna exakt.
> 
> 

### <a name="new-device-with-ebod-enclosure"></a>Ny enhet med EBOD-kabinett
StorSimple 8600-modellen har både en primär inne slutning och ett EBOD-hölje. Detta kräver att enheterna är kabelanslutna för anslutningar med SAS (Serial Attached SCSI) och kraft.

När du konfigurerar den här enheten för första gången ska du först utföra stegen för SAS-kablage och sedan slutföra stegen för ström kablar.

[!INCLUDE [storsimple-sas-cable-8600](../../includes/storsimple-sas-cable-8600.md)]

[!INCLUDE [storsimple-cable-8600-for-power](../../includes/storsimple-cable-8600-for-power.md)]

> [!NOTE]
> För fullständig enhets konfiguration och kabel instruktioner går du till [installera din StorSimple 8600-enhet](storsimple-8600-hardware-installation.md). Se till att du följer anvisningarna exakt.

## <a name="turn-on-a-device-after-shutdown"></a>Aktivera en enhet efter avstängning
Stegen för att aktivera en StorSimple-enhet när den har stängts är olika beroende på om enheten är en 8100-eller 8600-modell. 8100 har en enda primär inne slutning, medan 8600 är en enhet med dubbla höljen med en primär inne slutning och en EBOD hölje.

* [Enhet med endast primärt kabinett](#device-with-primary-enclosure-only)
* [Enhet med EBOD-kabinett](#device-with-ebod-enclosure)

### <a name="device-with-primary-enclosure-only"></a>Enhet med endast primärt kabinett
Efter en avstängning använder du följande procedur för att aktivera en StorSimple-enhet med ett primärt hölje och inget EBOD hölje.

#### <a name="to-turn-on-a-device-with-a-primary-enclosure-only"></a>Så här aktiverar du en enhet med endast primär kabinett
1. Se till att Power switcharna i både Power-och kylnings moduler (PCMs) är på plats av. Om växlarna inte är på-av-platsen, vänder du dem till STÄNGNINGs platsen och väntar på att indikeringarna ska gå ut.
2. Sätt på enheten genom att vända ström växlarna på båda PCMs till den här platsen. Enheten ska aktive ras.
3. Kontrol lera följande för att kontrol lera att enheten är helt på:
   
   1. Indikatorerna OK i båda PCM-modulerna är gröna.
   2. Status lamporna på båda styrenheterna är heldragna gröna.
   3. Den blå INDIKATORn på en av kontroll enheterna blinkar, vilket indikerar att kontrollanten är aktiv.
      
      Om något av dessa villkor inte är uppfyllt, är enheten inte felfri. [Kontakta Microsoft Support](storsimple-8000-contact-microsoft-support.md).

### <a name="device-with-ebod-enclosure"></a>Enhet med EBOD-kabinett
Efter en avstängning använder du följande procedur för att aktivera en StorSimple-enhet med en primär inne slutning och ett EBOD-hölje. Utför varje steg i sekvens exakt enligt beskrivningen. Om det inte gör det kan data gå förlorade.

#### <a name="to-turn-on-a-device-with-a-primary-and-an-ebod-enclosure"></a>Så här aktiverar du en enhet med en primär och en EBOD-kabinett
1. Kontrol lera att EBOD-kabinettet är anslutet till den primära inne slutningen. Mer information finns i [installera din StorSimple 8600-enhet](storsimple-8600-hardware-installation.md).
2. Se till att Power and kylning-modulerna (PCMs) på både EBOD och primär höljen är på plats. Om växlarna inte är på-av-platsen, vänder du dem till STÄNGNINGs platsen och väntar på att indikeringarna ska gå ut.
3. Aktivera EBOD-kammaren först genom att vända ström växlarna på båda PCMs till på plats. PCM-lamporna måste vara gröna. En grön EBOD Controller-lampa på den här enheten anger att EBOD-höljet är aktiverat.
4. Aktivera den primära inne slutningen genom att vända ström växlarna på båda PCMs till på plats. Hela systemet bör nu vara på.
5. Kontrol lera att SAS-lamporna är gröna, vilket säkerställer att anslutningen mellan EBOD-höljet och den primära inne slutningen är korrekt.

## <a name="turn-on-a-device-after-a-power-loss"></a>Aktivera en enhet efter en ström förlust
Strömavbrott eller avbrott kan stänga av en StorSimple-enhet. Strömavbrottet kan ske på ett av strömförsörjnings aggregaten eller i båda energi källor. Återställnings stegen skiljer sig åt beroende på om enheten är en 8100-eller 8600-modell. 8100 har en enda primär inne slutning, medan 8600 är en enhet med dubbla höljen med en primär inne slutning och en EBOD hölje. I det här avsnittet beskrivs återställnings proceduren för varje scenario.

* [Enhet med endast primärt kabinett](#8100)
* [Enhet med EBOD-kabinett](#8600)

### <a name="device-with-primary-enclosure-only"></a>Enhet med endast primärt kabinett <a name="8100"></a>
Systemet kan fortsätta med sin normala drift om det uppstår strömavbrott till något av dess strömförsörjnings enheter. För att säkerställa hög tillgänglighet för enheten kan du dock återställa strömförsörjningen så snart som möjligt.

Om det uppstår strömavbrott eller strömavbrott i båda strömförsörjningarna, stängs systemet av i ett ordnings-och kontrollerat sätt. När strömförsörjningen återställs aktive ras systemet automatiskt.

### <a name="device-with-ebod-enclosure"></a>Enhet med EBOD-kabinett <a name="8600"></a>
#### <a name="power-loss-on-one-power-supply"></a>Ström förlust för en strömförsörjning
Systemet kan fortsätta sin normala drift om det uppstår strömavbrott till ett av dess strömförsörjnings material på den primära inne slutningen eller EBOD-höljet. För att säkerställa hög tillgänglighet för enheten kan du dock återställa strömförsörjningen så snart som möjligt.

#### <a name="power-loss-on-both-power-supplies-on-primary-and-ebod-enclosures"></a>Strömavbrott i både strömförsörjning på primära och EBOD-höljen
Om det uppstår strömavbrott eller strömavbrott i båda strömförsörjningarna, stängs EBOD-höljet omedelbart och den primära inne slutningen stängs av på ett besvarat och kontrollerat sätt. När strömförsörjningen återställs startar installations programmet automatiskt.

Om strömförsörjningen stängs av manuellt utför du följande steg för att återställa systemets strömförsörjning.

1. Aktivera EBOD-höljet.
2. När EBOD-kammaren är på aktiverar du den primära inne slutningen.

### <a name="power-loss-on-both-power-supplies-on-ebod-enclosure"></a>Strömavbrott på både strömförsörjnings enheter på EBOD-höljen
När du konfigurerar dina kablar måste du se till att EBOD aldrig är anslutet till en separat PDU. Om EBOD och primär kabinettet inte kan köras samtidigt kommer systemet att återställas.

Om endast EBOD-kabinettet Miss lyckas på båda ström källor återställs inte systemet automatiskt. Utför följande steg för att aktivera systemet och återställa det till ett felfritt tillstånd:

1. Om den primära inne slutningen är aktive rad, Stäng av både Power-och kylnings moduler (PCMs).
2. Vänta några minuter tills systemet stängs av.
3. Aktivera EBOD-höljet.
4. När EBOD-kammaren är på aktiverar du den primära inne slutningen.

## <a name="turn-on-a-device-after-the-primary-and-ebod-enclosure-connection-is-lost"></a>Aktivera en enhet efter att den primära och EBOD-kabinett anslutningen förlorats
Om anslutningen förloras mellan vänte läges styrenheten och motsvarande EBOD-kontrollant fortsätter enheten att fungera. Om anslutningen mellan systemets aktiva styrenhet och motsvarande EBOD-styrenhet förloras, ska redundansväxlingen ske och enheten bör fortsätta att fungera som normalt.

När båda SAS-kablarna (Serial Attached SCSI) tas bort eller anslutningen mellan EBOD-kammaren och den primära inne slutningen är svår, slutar enheten att fungera. I det här läget utför du följande steg.

### <a name="to-turn-on-the-device-after-connection-is-lost"></a>Så här aktiverar du enheten efter att anslutningen förlorats
1. Få åtkomst till enhetens bak enhet.
2. Om SAS-kabeln mellan EBOD-kammaren och den primära inne slutningen bryts, kommer alla SAS-lampor i EBOD-höljet att vara inaktiverade.
3. Stäng av både Power-och kylnings moduler (PCMs) i EBOD-höljet och den primära inne slutningen.
4. Vänta tills alla lampor på bak sidan av båda höljen stängs av.
5. Sätt i SAS-kablarna igen och se till att det finns en lämplig anslutning mellan EBOD-höljet och den primära inne slutningen.
6. Aktivera EBOD-kammaren först genom att vända båda PCM-växlarna till på plats.
7. Se till att EBOD-höljet är aktiverat genom att kontrol lera att den gröna INDIKATORn är på.
8. Aktivera den primära inne slutningen.
9. Se till att den primära inne slutningen är på genom att kontrol lera att kontroll panelens gröna lampa är på.
10. Kontrol lera att EBOD hölje-anslutningen med den primära inne slutningen är felfri genom att kontrol lera att lysdioderna för SAS-Lane (fyra per EBOD Controller) är alla.

> [!IMPORTANT]
> Om SAS-kablarna är felaktiga eller om anslutningen mellan EBOD-kabinettet och den primära kabinett filen inte är lämplig, kommer den att gå i återställnings läge när du aktiverar systemet. [Kontakta Microsoft Support](storsimple-8000-contact-microsoft-support.md) om det händer.


## <a name="turn-off-a-running-device"></a>Stänga av en enhet som körs
En StorSimple-enhet som körs kan behöva stängas av om den flyttas, tas ur drift eller har en funktion som inte behöver bytas ut. Stegen varierar beroende på om StorSimple-enheten är en 8100-eller 8600-modell. 8100 har en enda primär inne slutning, medan 8600 är en enhet med dubbla höljen med en primär inne slutning och en EBOD hölje. I det här avsnittet beskrivs stegen för att stänga av en enhet som körs.

* [Enhet med primär kabinett](#8100a)
* [Enhet med EBOD-kabinett](#8600a)

### <a name="device-with-primary-enclosure"></a>Enhet med primär kabinett <a name="8100a"></a>
Om du vill stänga av enheten på ett besorterat och kontrollerat sätt kan du göra det via Azure Portal eller via Windows PowerShell för StorSimple. 

> [!IMPORTANT]
> Stäng inte av en enhet som körs genom att använda strömbrytaren på bak sidan av enheten.
> 
> Kontrol lera att alla enhets komponenter är felfria innan du stänger av enheten. I Azure Portal går du till **enheter**  >  **övervaka**  >  **maskin varu hälsa** och kontrollerar att status för alla komponenter är grön. Detta gäller endast för felfria system. Om systemet stängs av för att ersätta en komponent som inte fungerar visas en misslyckad (röd) eller degraderad (gul) status för respektive komponent i **maskin varu statusen**.
> 
> 

När du har åtkomst till Windows PowerShell för StorSimple eller Azure Portal följer du stegen i [stänga av en StorSimple-enhet](storsimple-8000-manage-device-controller.md#shut-down-a-storsimple-device). 

### <a name="device-with-ebod-enclosure"></a>Enhet med EBOD-kabinett <a name="8600a"></a>
> [!IMPORTANT]
> Innan du stänger av den primära inne slutningen och EBOD-höljet kontrollerar du att alla enhets komponenter är felfria. I Azure Portal går du till **enheter**  >  **övervaka**  >  **maskin varu hälsa** och kontrollerar att alla komponenter är felfria.


#### <a name="to-shut-down-a-running-device-with-ebod-enclosure"></a>Stänga en enhet som körs med EBOD-kabinettet
1. Följ alla steg som anges i [stänga av en StorSimple-enhet](storsimple-8000-manage-device-controller.md#shut-down-a-storsimple-device) för den primära inne slutningen.
2. När den primära inne slutningen har stängts stänger du av EBOD genom att stänga av både Power-och kylnings modulens (PCM) växlar.
3. Kontrol lera att alla lampor på bak sidan av EBOD-höljet är inaktiverade för att kontrol lera att EBOD har stängts av.

> [!NOTE]
> SAS-kablarna som används för att ansluta EBOD-kabinettet till den primära kabinett filen bör inte tas bort förrän datorn stängts av.

## <a name="next-steps"></a>Nästa steg
[Kontakta Microsoft Support](storsimple-8000-contact-microsoft-support.md) om det uppstår problem när du aktiverar eller stänger av en StorSimple-enhet.

