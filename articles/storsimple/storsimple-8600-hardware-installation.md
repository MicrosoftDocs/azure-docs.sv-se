---
title: Installera Microsoft Azure StorSimple 8600-enhet
description: Beskriver hur du packar upp, rackerar montering och kabelansluter din StorSimple 8600-enhet innan du distribuerar och konfigurerar program varan.
author: alkohli
ms.assetid: 3d82ba5f-3e34-40dc-9c33-50f952bc6be8
ms.service: storsimple
ms.topic: conceptual
ms.date: 01/09/2018
ms.author: alkohli
ms.openlocfilehash: 8d118e71b29b54b034788c37916091ad8f8b996f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "91871821"
---
# <a name="unpack-rack-mount-and-cable-your-storsimple-8600-device"></a>Packa upp, rack montering och kabel din StorSimple 8600-enhet

[!INCLUDE [storsimple-8000-eol-banner](../../includes/storsimple-8000-eol-banner.md)]

## <a name="overview"></a>Översikt
Din Microsoft Azure StorSimple 8600 är en dubbel enhets enhet och består av en primär och en EBOD-bilaga. I den här självstudien beskrivs hur du packar upp, rackerar montering och kabelansluter StorSimple 8600-enhetens maskin vara innan du konfigurerar StorSimple-programvaran.

## <a name="unpack-your-storsimple-8600-device"></a>Packa upp din StorSimple 8600-enhet
Följande steg innehåller tydliga och detaljerade instruktioner om hur du packar upp StorSimple 8600-lagrings enheten. Den här enheten levereras i två rutor, en för den primära inne slutningen och en annan för EBOD-höljet. Dessa två rutor placeras sedan i en enda ruta.

### <a name="prepare-to-unpack-your-device"></a>Förbered för att packa upp enheten
Läs följande information innan du packar upp enheten.

![Varnings ikon ](./media/storsimple-safety/IC740879.png)![ Varning vid hög vikts ikon ](./media/storsimple-8600-hardware-installation/HCS_HeavyWeight_Icon.png) **!**

1. Se till att du har två personer som är tillgängliga för att hantera vikten av enheten om du hanterar den manuellt. En fullständigt konfigurerad hölje kan väga upp till 32 kg (70 kg).
2. Placera lådan på en plan, jämn yta.

Slutför sedan följande steg för att packa upp enheten.

#### <a name="to-unpack-your-device"></a>Packa upp enheten
1. Kontrollera lådan och förpackningsskummet för att se om det förekommer krosskador, skärningar, vattenskador eller andra uppenbara skador. Om lådan eller förpackningen är allvarligt skadad ska du inte öppna lådan. [Kontakta Microsoft Support](storsimple-8000-contact-microsoft-support.md) för att få hjälp att utvärdera om enheten är i fungerande skick.
2. Öppna den yttre rutan och ta sedan bort de två rutorna som motsvarar primär-och EBOD-höljen. Du kan nu packa upp primär-och EBOD-höljen. Följande bild visar den uppackade vyn av en av-höljen.
   
    ![Packa upp lagrings enheten](./media/storsimple-8600-hardware-installation/HCSUnpackyour4Udevice.png)
   
    **Uppackad vy av lagrings enheten**
   
   | Etikett | Beskrivning |
   | --- | --- |
   |   1 |Förpacknings Box |
   |   2 |SAS-kablar (i tillbehör och kabel kassetter) |
   |   3 |Nedre skum |
   |   4 |Enhet |
   |   5 |Övre skum |
   |   6 |Tillbehörs ruta |
3. När du har packat upp de två rutorna kontrollerar du att du har:
   
   * 1 primär kabinett (den primära inne slutningen och EBOD-kammaren finns i två separata rutor)
   * 1 EBOD-hölje
   * 4 ström sladdar, 2 i varje ruta
   * 2 SAS-kablar (för att ansluta den primära inne slutningen till EBOD-höljet)
   * 1 korskopplad Ethernet-kabel
   * 2 kablar för serie konsol
   * 1 seriell USB-omvandlare för seriell åtkomst
   * 4 QSFP-till-SFP +-kort som ska användas med 10 GbE-nätverks gränssnitt
   * 2 rack monterings paket (4-Side-räler med monterings maskin vara, 2 var för den primära kammaren och EBOD-höljet), 1 i varje ruta
   * Komma igång-dokumentation
     
     Om du inte har fått något av de objekt som anges ovan, [kontaktar du Microsoft Support](storsimple-8000-contact-microsoft-support.md).  

Nästa steg är att rackmontera enheten.

## <a name="rack-mount-your-storsimple-8600-device"></a>Rack montering av din StorSimple 8600-enhet
Följ nästa steg för att installera StorSimple 8600-lagrings enheten i ett standardiserat 19-tums rack med främre och bakre inlägg. Den här enheten levereras med två höljen: en primär inne slutning och en EBOD hölje. Båda dessa måste vara rack montering.

Installationen består av flera steg som beskrivs i följande procedurer.

> [!IMPORTANT]
> StorSimple-enheter måste vara rack montering för att fungera korrekt.
> 
> 

### <a name="site-preparation"></a>Förberedelse av webbplats
Höljen måste installeras i ett standardiserat 19-tums rack som har både fram-och bak poster. Använd följande procedur för att förbereda för rack installation.

#### <a name="to-prepare-the-site-for-rack-installation"></a>Förbereda platsen för rack installation
1. Se till att de primära och EBOD höljen är på ett säkert sätt på en platt, stabil och nivå arbets yta (eller liknande).
2. Kontrol lera att den plats där du planerar att konfigurera har standard ström från en oberoende källa eller en enhet för rack ström distribution (PDU) med en avbrotts fri ström källa (UPS).
3. Se till att en 4U-kortplats (2 X 2U) är tillgänglig i racket där du tänker montera höljen.

![Varnings ikon ](./media/storsimple-safety/IC740879.png)![ Varning vid hög vikts ikon ](./media/storsimple-8600-hardware-installation/HCS_HeavyWeight_Icon.png) **!**

 Se till att du har två personer som är tillgängliga för att hantera vikten om du hanterar enhets installationen manuellt. En fullständigt konfigurerad hölje kan väga upp till 32 kg (70 kg).

### <a name="rack-prerequisites"></a>Krav för rack
Höljen är utformade för installation i ett standardiserat standardiserat rack skåp med:

* Minsta djup 27,84 cm från rack post till inlägg
* Maximal vikt på 32 kg för enheten
* Högsta mottryck på 5 Pascal (0,5 mm vatten mätare)

### <a name="rack-mounting-rail-kit"></a>Rack montering av järnvägs paket
En uppsättning monterings skenor kommer att tillhandahållas för användning med 19-tums rack skåp. Räler har testats för att hantera den maximala bilagans vikt. Dessa räler gör det också möjligt att installera flera höljen utan att utrymmet i racket går förlorade. Installera EBOD-kammaren först.

#### <a name="to-install-the-ebod-enclosure-on-the-rails"></a>Så här installerar du EBOD-kammaren på räler
1. Utför bara det här steget om inre räler inte är installerade på enheten. De inre räler installeras vanligt vis på fabriken. Om räler inte är installerade, installerar du vänster-och HÖGERPIL-bilderna på sidorna i höljets chassi. De ansluter med sex metriska skruvar på varje sida. För att hjälpa med orienteringen är räl-bilderna markerade som **LH – framtill** och **RH – framtill**, och slutet som fästs mot bak sidan av höljet har en avsmalnande slut.
   
    ![Fästa järnvägs bilder i hölje chassi](./media/storsimple-8600-hardware-installation/HCSAttachingRailSlidestoEnclosureChassis.png)
   
    **Fästa järnvägs bilder på sidorna i inne slutningen**
   
   | Etikett | Beskrivning |
   | --- | --- |
   |  1 |M 3x4 knapp – huvud skruvar |
   |  2 |Chassi bilder |
2. Koppla de vänstra och högra järnvägs sammansättningarna till rack skåpets lodräta medlemmar. Hakparenteserna är märkta som **LH**, **RH** och **den här sidan upp** för att vägleda dig genom rätt orientering.
3. Leta upp spårstiften på framsidan och baksidan av spårenheten. Utöka spåret så att det passar mellan rack inläggen och Lägg till stiften i främre och bakre racket posts vertikala medlems hål. Se till att järn vägs sammansättningen är nivå.
4. Skydda järnvägs sammansättningen till rackets vertikala medlemmar genom att använda två av de mät skruvarna som tillhandahålls. Använd en skruv på bak sidan och en på bak sidan.
5. Upprepa de här stegen för den andra järnvägs sammansättningen.
   
     ![Fäst järnvägs bilder i rack skåpet](./media/storsimple-8600-hardware-installation/HCSAttachingRailSlidestoRackCabinet.png)
   
    **Fästa järnvägs sammansättningar i racket**
   
   | Etikett | Beskrivning |
   | --- | --- |
   |   1 |Ihopfogning skruv |
   |   2 |Rack-håls front Rack-skruv |
   |   3 |PIN-platser till vänster spår |
   |   4 |Ihopfogning skruv |
   |   5 |PIN-märken till vänster bakre järnvägs plats |

### <a name="mounting-the-ebod-enclosure-in-the-rack"></a>Montera EBOD-kammaren i racket
Använd de rackbaserade räler som precis har installerats och utför följande steg för att montera EBOD-kammaren i racket.

#### <a name="to-mount-the-ebod-enclosure"></a>Montera EBOD-kabinettet
1. Lyft inne slutning med en assistent och justera den med rack skenorna.
2. Infoga försiktigt höljet i räler och skicka det sedan helt i rack skåpet.
   
    ![Sätter in enheten i racket](./media/storsimple-8600-hardware-installation/HCSInsertingDeviceintheRack.png)
   
    **Montera inne slutningen i racket**
3. Ta bort de vänstra och högra frontend-versalerna genom att dra in de kostnads fria. De flänsiga CAPS fästs bara på flänsarna.
4. Skydda inne slutningen i racket genom att installera en angiven Phillips-skruv genom varje fläns, vänster och höger.
5. Installera de flänsiga CAPS genom att trycka dem på plats och fäst dem på plats.
   
     ![Installerar fläns Caps](./media/storsimple-8600-hardware-installation/HCSInstallingFlangeCaps.png)
   
    **Installera de flänsiga Caps**
   
   | Etikett | Beskrivning |
   | --- | --- |
   |   1 |Kabinett fäst skruv |

### <a name="mounting-the-primary-enclosure-in-the-rack"></a>Montera den primära inne slutningen i racket
När du har monterat EBOD-kabinettet måste du montera den primära inne slutningen enligt samma steg.

> [!NOTE]
> * Det är möjligt att ha några tomma fack i racket mellan den primära inne slutningen och EBOD-höljet.
> * Använd den tillhandahållna SAS-kabeln 2 m för att ansluta den primära inne slutningen till EBOD-höljet.
> * Det finns inga begränsningar i den relativa placeringen av Head-enheten i EBOD-enheten. Därför kan den primära inne slutningen placeras på den översta platsen och EBOD-kabinettet nedan – eller tvärtom.
> 
> 

Nästa steg är att ansluta enheten till strömförsörjning, nätverk och seriell åtkomst.

## <a name="cable-your-storsimple-8600-device"></a>Kabelansluta en StorSimple 8600-enhet
Följande procedurer beskriver hur du kan ansluta din StorSimple 8600-enhet för strömförsörjning, nätverk och seriella anslutningar.

### <a name="prerequisites"></a>Förutsättningar
Innan du börjar kabeln till enheten behöver du:

* Ditt primära hölje och EBOD-höljet, helt packas upp
* 4 ström kablar (2 var för den primära och EBOD-höljet) som medföljde din enhet
* 2 SAS-kablar som medföljer enheten för att ansluta EBOD-höljet till den primära inne slutningen
* Åtkomst till 2-PDU (Power distribution units) (rekommenderas)
* Nätverks kablar
* Tillhandahållna serie kablar
* Seriell USB-omvandlare med lämplig driv rutin installerad på din dator (om det behövs)
* Tillhandahåll 4 QSFP-till-SFP +-kort som ska användas med 10 GbE-nätverks gränssnitt
* [Maskin vara som stöds för 10 GbE Network-gränssnitt på din StorSimple-enhet](storsimple-supported-hardware-for-10-gbe-network-interfaces.md)

### <a name="sas-and-power-cabling"></a>SAS-och ström kablar
Enheten har både en primär inne slutning och en EBOD hölje. Detta kräver att enheterna är kabelanslutna för anslutningar med SAS (Serial Attached SCSI) och kraft.

När du konfigurerar den här enheten för första gången ska du först utföra stegen för SAS-kablage och sedan slutföra stegen för ström kablar.

[!INCLUDE [storsimple-cable-8600-for-SAS](../../includes/storsimple-sas-cable-8600.md)]

[!INCLUDE [storsimple-cable-8600-for-power](../../includes/storsimple-cable-8600-for-power.md)]

### <a name="network-cabling"></a>Nätverks kablar
Enheten är i en aktiv-standby-konfiguration: när som helst är en Controller-modul aktiv och bearbetar alla disk-och nätverks åtgärder medan den andra Controller-modulen är i vänte läge. Om ett kontroll enhets fel inträffar aktiverar vilo läges styrenheten omedelbart och fortsätter alla disk-och nätverks åtgärder.

För att stödja redundans av redundanta styrenheter måste du kontakta enhetens nätverk som du ser i följande steg.

#### <a name="to-cable-for-network-connection"></a>För kabel för nätverks anslutning
1. Enheten har sex nätverks gränssnitt på varje styrenhet: 4 1 Gbit/s och 2 10 Gbit/s Ethernet-portar. Se följande bild för att identifiera data portar på enhetens bakplan.
   
     ![Bakplanering av 8600-enhet](./media/storsimple-8600-hardware-installation/HCSBackplaneof2UDevicewithPortsLabeled.jpg)
   
    **Bak sidan av enheten som visar data portarna**
   
   | Etikett | Beskrivning |
   | --- | --- |
   |   0, 1, 4, 5 |1 GbE-nätverks gränssnitt |
   |   2, 3 |10 GbE-nätverks gränssnitt |
   |   6 |Serie portar |
2. Se följande diagram för nätverks kablar. (Den minsta nätverks konfigurationen visas med heldragna blå linjer. För hög tillgänglighet och prestanda visas ytterligare konfiguration som krävs med prickade linjer.)

![Kabelanslut din 4U-enhet för nätverk](./media/storsimple-8600-hardware-installation/HCSCableYour4UDeviceforNetwork.png)

**Nätverks kablar för din enhet**

| Etikett | Beskrivning |
| --- | --- |
| A |LAN med Internet åtkomst |
| B |Kontrollant 0 |
| C |PCM 0 |
| D |Kontrollant 1 |
| E |PCM 1 |
| F |EBOD Controller 0 |
| G |EBOD Controller 1 |
| H, JAG |Värdar (till exempel fil servrar) |
| 0-5 |Nätverksgränssnitt |
| 6 |Primärt kabinett |
| 7 |EBOD-hölje |

Vid en kabel av enheten kräver den minsta konfigurationen:

* Minst två nätverks gränssnitt som är anslutna på varje styrenhet med ett för moln åtkomst och ett för iSCSI. DATA 0-porten aktive ras automatiskt och konfigureras via enhetens serie konsol. Förutom DATA 0 måste även en annan data port konfigureras via den klassiska Azure-portalen. I det här fallet ansluter du DATA 0-port till det primära LAN (nätverk med Internet åtkomst). De andra data portarna kan anslutas till nätverks segmentet SAN/iSCSI LAN (VLAN), beroende på den avsedda rollen.
* Identiska gränssnitt på varje styrenhet som är ansluten till samma nätverk för att säkerställa tillgängligheten om en styrenhets växling sker. Om du till exempel väljer att ansluta DATA 0 och DATA 3 för en av kontroll enheterna måste du ansluta motsvarande DATA 0 och DATA 3 på den andra styrenheten.

Tänk på hög tillgänglighet och prestanda:

* När det är möjligt konfigurerar du ett par nätverks gränssnitt för moln åtkomst (1 GbE) och ett annat par för iSCSI (10 GbE rekommenderas) på varje styrenhet.
* När det är möjligt kan du ansluta nätverks gränssnitt från varje kontrollant till två olika växlar för att säkerställa tillgängligheten mot ett växel haveri. Bilden illustrerar 2 10 GbE-nätverks gränssnitt, DATA 2 och DATA 3, från varje styrenhet som är ansluten till två olika växlar. Mer information finns i **nätverks gränssnitten** under [krav för hög tillgänglighet för din StorSimple-enhet](storsimple-8000-system-requirements.md#high-availability-requirements-for-storsimple).

> [!NOTE]
> Om du använder SFP +-Sänd tagare med dina 10 GbE-nätverks gränssnitt använder du de angivna QSFP-SFP +-korten. Mer information finns i [maskin vara som stöds för 10 GbE Network-gränssnitt på din StorSimple-enhet](storsimple-supported-hardware-for-10-gbe-network-interfaces.md).
> 
> 

### <a name="serial-port-cabling"></a>Serie ports kablar
Utför följande steg för att kabelansluta din serie port.

#### <a name="to-cable-for-serial-connection"></a>För kabel för seriell anslutning
1. Enheten har en seriell port på varje kontrollant som identifieras av en Skift nyckel ikon. Du hittar serie portarna genom att se bilden som visar data portarna på bak sidan av enheten.
2. Identifiera den aktiva styrenheten på enhetens bakplan. En blinkande blå indikator anger att styrenheten är aktiv.
3. Använd den tillhandahållna serie kabeln (om det behövs, USB-seriella konverterare för din bärbara dator) och Anslut konsolen eller datorn (med terminalemulering till enheten) till den seriella porten för den aktiva styrenheten.
4. Installera de seriella USB-drivrutinerna (som levererades med enheten) på datorn.
5. Konfigurera den seriella anslutningen enligt följande:
   
   * 115 200 baud
   * 8 data bitar
   * 1 stopp bitar
   * Ingen paritet
   * Flödes kontroll har angetts till **ingen**
6. Kontrol lera att anslutningen fungerar genom att trycka på RETUR i-konsolen. En serie konsol meny ska visas.

> [!NOTE]
> **Hantering av lampor:** När enheten är installerad i ett fjärranslutet Data Center eller i ett dator rum med begränsad åtkomst, se till att serie anslutningarna till båda styrenheterna alltid är anslutna till en serie konsol växel eller liknande utrustning. Detta tillåter funktioner för out-of-band-fjärrstyrning och-support om nätverks avbrott eller oväntade fel inträffar.
> 
> 

Du har slutfört kablar av enheten för strömförsörjning, nätverks åtkomst och seriell anslutning. Nästa steg är att konfigurera program varan på enheten.

## <a name="next-steps"></a>Nästa steg
Du är nu redo att [distribuera och konfigurera din lokala StorSimple-enhet](storsimple-8000-deployment-walkthrough-u2.md).

