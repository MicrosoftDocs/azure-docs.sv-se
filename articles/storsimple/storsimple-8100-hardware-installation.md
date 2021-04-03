---
title: Installera Microsoft Azure StorSimple 8100-enhet
description: Beskriver hur du packar upp, rackerar montering och kabelansluter din StorSimple 8100-enhet innan du distribuerar och konfigurerar program varan.
author: alkohli
ms.assetid: 6098a01e-c031-488a-a8d7-0b607ce665e1
ms.service: storsimple
ms.topic: conceptual
ms.date: 01/09/2018
ms.author: alkohli
ms.openlocfilehash: 113b72ddf7e5d508c8a0b577d4004d4fbd83e8e5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "84699132"
---
# <a name="unpack-rack-mount-and-cable-your-storsimple-8100-device"></a>Packa upp, rack montering och kabel din StorSimple 8100-enhet

[!INCLUDE [storsimple-8000-eol-banner](../../includes/storsimple-8000-eol-banner.md)]

## <a name="overview"></a>Översikt
Din Microsoft Azure StorSimple 8100 är en enda enhets-och rackmonterad enhet. I den här självstudien beskrivs hur du packar upp, rackerar montering och kabelansluter StorSimple 8100-enhetens maskin vara innan du konfigurerar och distribuerar StorSimple-enheten.

## <a name="unpack-your-storsimple-8100-device"></a>Packa upp din StorSimple 8100-enhet
Följande steg innehåller tydliga och detaljerade instruktioner om hur du packar upp StorSimple 8100-lagrings enheten. Den här enheten levereras i en enda låda.

### <a name="prepare-to-unpack-your-device"></a>Förbered för att packa upp enheten
Läs följande information innan du packar upp enheten.

![Varnings ikon ](./media/storsimple-safety/IC740879.png)![ Varning vid hög vikts ikon ](./media/storsimple-8100-hardware-installation/HCS_HeavyWeight_Icon.png) **!**

1. Se till att du har två personer som är tillgängliga för att hantera vikten av höljet om du hanterar den manuellt. En fullständigt konfigurerad hölje kan väga upp till 32 kg (70 kg).
2. Placera lådan på en plan, jämn yta.

Slutför sedan följande steg för att packa upp enheten.

#### <a name="to-unpack-your-device"></a>Packa upp enheten
1. Kontrollera lådan och förpackningsskummet för att se om det förekommer krosskador, skärningar, vattenskador eller andra uppenbara skador. Om lådan eller förpackningen är allvarligt skadad ska du inte öppna lådan. [Kontakta Microsoft Support](storsimple-8000-contact-microsoft-support.md) för att få hjälp att utvärdera om enheten är i fungerande skick.
2. Packa upp lådan. Följande bild visar den uppackade vyn för din StorSimple-enhet.
   
     ![Packa upp lagrings enheten](./media/storsimple-8100-hardware-installation/HCSUnpackyour2Udevice.png)
   
    **Uppackad vy av lagrings enheten**
   
   | Etikett | Beskrivning |
   | --- | --- |
   |   1 |Förpacknings Box |
   |   2 |Nedre skum |
   |   3 |Enhet |
   |   4 |Övre skum |
   |   5 |Tillbehörs ruta |
3. När du har packat upp lådan kontrollerar du att det finns:
   
   * 1 Single Enclosure-enhet
   * 2 ström sladdar
   * 1 korskopplad Ethernet-kabel
   * 2 kablar för serie konsol
   * 1 seriell USB-omvandlare för seriell åtkomst
   * 1 manipulering – T10 Screwdriver
   * 4 QSFP-till-SFP +-kort som ska användas med 10 GbE-nätverks gränssnitt
   * 1 rack monterings paket (2 Side-räler med monterings maskin vara)
   * Komma igång dokumentation
     
     Om du inte har fått något av de objekt som anges ovan, [kontaktar du Microsoft Support](storsimple-8000-contact-microsoft-support.md).

Nästa steg är att rackmontera enheten.

## <a name="rack-mount-your-storsimple-8100-device"></a>Rack montering av din StorSimple 8100-enhet
Följ nästa steg för att installera StorSimple 8100-lagrings enheten i ett standardiserat 19-tums rack med främre och bakre inlägg. StorSimple 8100-enheten har en enda primär kabinett enhet.

Installationen består av flera steg som beskrivs i följande procedurer.

> [!IMPORTANT]
> StorSimple-enheter måste vara rack montering för att fungera korrekt.
> 
> 

### <a name="prepare-the-site"></a>Förbered platsen
Enheten måste vara installerad i ett standardiserat 19-tums rack som har både främre och bakre inlägg. Använd följande procedur för att förbereda för rack installation.

#### <a name="to-prepare-the-site-for-rack-installation"></a>Förbereda platsen för rack installation
1. Kontrollera att enheten på ett säkert sätt vilar på en plan, stabil och jämn arbetsyta (eller liknande).
2. Kontrol lera att den plats där du planerar att konfigurera har standard ström från en oberoende källa eller en enhet för rack ström distribution (PDU) med en avbrotts fri ström källa (UPS).
3. Se till att det finns en 2U-kortplats i racket där du tänker montera enheten.

![Varnings ikon ](./media/storsimple-safety/IC740879.png)![ Varning vid hög vikts ikon ](./media/storsimple-8100-hardware-installation/HCS_HeavyWeight_Icon.png) **!**

Se till att du har två personer som är tillgängliga för att hantera vikten om du hanterar enhets installationen manuellt. En fullständigt konfigurerad hölje kan väga upp till 32 kg (70 kg).

### <a name="rack-prerequisites"></a>Krav för rack
8100-kammaren är utformad för installation i ett standard-19-tums rack skåp med:

* Minsta djup 27,84 cm från rack post till inlägg.
* Maximal vikt på 32 kg för enheten
* Högsta mottryck på 5 Pascal (0,5 mm vatten mätare).

### <a name="rack-mounting-rail-kit"></a>Rack montering av järnvägs paket
En uppsättning monterings skenor tillhandahålls för användning med 19-tums rack skåp. Räler har testats för att hantera den maximala bilagans vikt. Dessa räler gör det också möjligt att installera flera höljen utan att det går att förlora utrymme i racket.

#### <a name="to-install-the-device-on-the-rails"></a>Så här installerar du enheten på räler
1. Utför bara det här steget om inre räler inte är installerade på enheten. De inre räler installeras vanligt vis på fabriken. Om räler inte är installerade, installerar du vänster-och HÖGERPIL-bilderna på sidorna i höljets chassi. De ansluter med sex metriska skruvar på varje sida. För att hjälpa med orienteringen är räl-bilderna markerade som **LH – framtill** och **RH – framtill**, och slutet som fästs mot bak sidan av höljet har en avsmalnande slut.<br/>
   
    ![Fästa järnvägs bilder i hölje chassi](./media/storsimple-8100-hardware-installation/HCSAttachingRailSlidestoEnclosureChassis.png)

    **Fästa inre räl i höljets sidor**
   
    Etikett | Beskrivning
    ----- | -----------
    1     | M 3x4 knapp – huvud skruvar
    2     | Chassi bilder

2. Koppla de yttre vänster järnvägarna och de yttre högra järnvägs sammansättningarna till rack skåpets lodräta medlemmar. Hakparenteserna är märkta som **LH**, **RH** och **den här sidan upp** för att vägleda dig genom rätt orientering.
3. Leta upp spårstiften på framsidan och baksidan av spårenheten. Utöka spåret så att det passar mellan rack inläggen och Lägg till stiften i det främre och bakre racket posts vertikala medlems hål. Se till att järn vägs sammansättningen är nivå.
4. Använd två av de tillhandahållna måtten skruvarna för att skydda järnvägs sammansättningen med rackets lodräta medlemmar. Använd en skruv på bak sidan och en på bak sidan.
5. Upprepa de här stegen för den andra järnvägs sammansättningen.<br/>
   
     ![Fäst järnvägs bilder i rack skåpet](./media/storsimple-8100-hardware-installation/HCSAttachingRailSlidestoRackCabinet.png)
   
    **Koppla yttre järnvägs-sammansättningar till racket**
   
   | Etikett | Beskrivning |
   | --- | --- |
   |   1 |Ihopfogning skruv |
   |   2 |Rack-håls front Rack-skruv |
   |   3 |PIN-platser för vänster järnväg |
   |   4 |Ihopfogning skruv |
   |   5 |Nålar vänster bak |

### <a name="mounting-the-device-in-the-rack"></a>Montera enheten i racket
Använd de rackbaserade räler som precis har installerats och utför följande steg för att montera enheten i racket.

#### <a name="to-mount-the-device"></a>Montera enheten
1. Lyft inne slutning med en assistent och justera den med rack skenorna.
2. Infoga noggrant enheten i räler och skicka sedan enheten helt till rack skåpet.<br/>
   
    ![Sätter in enheten i racket](./media/storsimple-8100-hardware-installation/HCSInsertingDeviceintheRack.png)
   
    **Montera enheten i racket**
3. Ta bort de vänstra och högra frontend-versalerna genom att dra in de kostnads fria. De flänsiga CAPS fästs bara på flänsarna.
4. Skydda höljet i racket genom att installera en angiven Phillips-skruv genom varje fläns, vänster och höger.
5. Installera de flänsiga CAPS genom att trycka dem i rätt läge och fästa dem på plats.<br/>
   
     ![Installerar fläns Caps](./media/storsimple-8100-hardware-installation/HCSInstallingFlangeCaps.png)
   
    **Installera de flänsiga Caps**
   
   | Etikett | Beskrivning |
   | --- | --- |
   |   1 |Kabinett fäst skruv |

Nästa steg är att ansluta enheten till strömförsörjning, nätverk och seriell åtkomst.

## <a name="cable-your-storsimple-8100-device"></a>Kabelansluta en StorSimple 8100-enhet
Följande procedurer beskriver hur du kan ansluta din StorSimple 8100-enhet för strömförsörjning, nätverk och seriella anslutningar.

### <a name="prerequisites"></a>Förutsättningar
Innan du börjar kabelansluta enheten behöver du:

* Lagrings enheten, helt uppackat och rack montering.
* 2 ström kablar som medföljde din enhet
* Åtkomst till 2 Power distributions enheter (rekommenderas).
* Nätverks kablar
* Tillhandahållna serie kablar
* Seriell USB-omvandlare med lämplig driv rutin installerad på din dator (om det behövs)
* Tillhandahåll 4 QSFP-till-SFP +-kort som ska användas med 10 GbE-nätverks gränssnitt
* [Maskin vara som stöds för 10 GbE Network-gränssnitt på din StorSimple-enhet](storsimple-supported-hardware-for-10-gbe-network-interfaces.md)

### <a name="power-cabling"></a>Ström kablar
Enheten innehåller redundanta Power-och kylnings-moduler (PCMs). Både PCMs måste installeras och anslutas till olika ström källor för att säkerställa hög tillgänglighet.

Utför följande steg för att kabelansluta enheten för strömförsörjning.

[!INCLUDE [storsimple-cable-8100-for-power](../../includes/storsimple-cable-8100-for-power.md)]

### <a name="network-cabling"></a>Nätverks kablar
Enheten är en aktiv standby-konfiguration: när som helst är en Controller-modul aktiv och bearbetar alla disk-och nätverks åtgärder medan den andra Controller-modulen är i vänte läge. Om en styrenhet kraschar aktive ras vilo läges styrenheten omedelbart och fortsätter alla disk-och nätverks åtgärder.

För att stödja redundans av redundanta styrenheter måste du kontakta enhetens nätverk enligt beskrivningen i följande steg.

#### <a name="to-cable-for-network-connection"></a>För kabel för nätverks anslutning
1. Enheten har sex nätverks gränssnitt på varje styrenhet: 4 1 Gbit/s och 2 10 Gbit/s Ethernet-portar. Identifiera de olika data portarna på enhetens bakplan.
   
    ![Bakplanering av 8100-enhet](./media/storsimple-8100-hardware-installation/HCSBackplaneof2UDevicewithPortsLabeled.jpg)
   
    **Bak sidan av enheten som visar data portar**
   
   | Etikett | Beskrivning |
   | --- | --- |
   |   0, 1, 4, 5 |1 GbE-nätverks gränssnitt |
   |   2, 3 |10 GbE-nätverks gränssnitt |
   |   6 |Serie portar |
2. Se följande diagram för nätverks kablar. (Den minsta nätverks konfigurationen visas med heldragna blå linjer. Ytterligare konfiguration som krävs för hög tillgänglighet och prestanda visas med prickade linjer.)

    ![Kabelanslut din 2U-enhet för nätverk](./media/storsimple-8100-hardware-installation/HCSCableYour2UDeviceforNetwork.png)

    **Nätverks kablar för din enhet**

   |Etikett | Beskrivning |
   |----- | ----------- |
   | A    | LAN med Internet åtkomst |
   | B    | Kontrollant 0 |
   | C    | PCM 0 |
   | D    | Kontrollant 1 |
   | E    | PCM 1 |
   | F, G | Värdar |
   | 0-5  | Nätverksgränssnitt |



Vid en kabel av enheten kräver den minsta konfigurationen:

* Minst två nätverks gränssnitt som är anslutna på varje styrenhet med ett för moln åtkomst och ett för iSCSI. DATA 0-porten aktive ras automatiskt och konfigureras via enhetens serie konsol. Förutom DATA 0 måste även en annan data port konfigureras via den klassiska Azure-portalen. I det här fallet ansluter du DATA 0-port till det primära LAN (nätverk med Internet åtkomst). De andra data portarna kan anslutas till nätverks segmentet SAN/iSCSI LAN (VLAN), beroende på den avsedda rollen.
* Identiska gränssnitt på varje styrenhet som är ansluten till samma nätverk för att säkerställa tillgängligheten om en styrenhets växling sker. Om du till exempel väljer att ansluta DATA 0 och DATA 3 för en av kontroll enheterna måste du ansluta motsvarande DATA 0 och DATA 3 på den andra styrenheten.

Tänk på hög tillgänglighet och prestanda:

* När det är möjligt konfigurerar du ett par nätverks gränssnitt för moln åtkomst (1 GbE) och ett annat par för iSCSI (10 GbE rekommenderas) på varje styrenhet.
* När det är möjligt kan du ansluta nätverks gränssnitt från varje kontrollant till två olika växlar för att säkerställa tillgängligheten mot ett växel haveri. Bilden illustrerar 2 10 GbE-nätverks gränssnitt, DATA 2 och DATA 3, från varje styrenhet som är ansluten till två olika växlar.

Mer information finns i **nätverks gränssnitten** under [krav för hög tillgänglighet för din StorSimple-enhet](storsimple-8000-system-requirements.md#high-availability-requirements-for-storsimple).

> [!NOTE]
> Om du använder SFP +-Sänd tagare med dina 10 GbE-nätverkskort använder du de tillhandahållna QSFP-SFP +-korten. Mer information finns i [maskin vara som stöds för 10 GbE Network-gränssnitt på din StorSimple-enhet](storsimple-supported-hardware-for-10-gbe-network-interfaces.md).
> 
> 

### <a name="serial-port-cabling"></a>Serie ports kablar
Utför följande steg för att kabelansluta din serie port.

#### <a name="to-cable-for-serial-connection"></a>För kabel för seriell anslutning
1. Enheten har en seriell port på varje kontrollant som identifieras av en Skift nyckel ikon. Se bilden i avsnittet [nätverks kablar](#network-cabling) för att hitta de seriella portarna på enhetens bakplan.
2. Identifiera den aktiva styrenheten på enhetens bakplan. En blinkande blå indikator anger att styrenheten är aktiv.
3. Använd de angivna serie kablarna (vid behov USB-seriella konverterare för din bärbara dator) och Anslut konsolen eller datorn (med terminalemulering till enheten) till den seriella porten för den aktiva styrenheten.
4. Installera de seriella USB-drivrutinerna (som levererades med enheten) på datorn.
5. Konfigurera den seriella anslutningen enligt följande: 115 200 baud, 8 data bitar, 1 stoppbit, ingen paritet och flödes kontroll inställd på ingen.
6. Kontrol lera att anslutningen fungerar genom att trycka på RETUR i-konsolen. En serie konsol meny ska visas.

> [!NOTE]
> **Hantering av lampor**: när enheten är installerad i ett fjärranslutet Data Center eller i ett dator rum med begränsad åtkomst måste du kontrol lera att serie anslutningarna till båda styrenheterna alltid är anslutna till en serie konsol växel eller liknande utrustning. Detta tillåter out-of-band-fjärrstyrning och support åtgärder om det uppstår nätverks avbrott eller oväntade fel.
> 
> 

Enheten är nu kabelansluten för strömförsörjning, nätverks åtkomst och seriell anslutning. Nästa steg är att konfigurera program varan och distribuera enheten.

## <a name="next-steps"></a>Nästa steg
Lär dig hur du [distribuerar och konfigurerar din lokala StorSimple-enhet](storsimple-8000-deployment-walkthrough-u2.md).

