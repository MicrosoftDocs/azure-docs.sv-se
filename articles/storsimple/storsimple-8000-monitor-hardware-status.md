---
title: StorSimple 8000-seriens maskin varu komponenter och status | Microsoft Docs
description: Lär dig hur du övervakar StorSimple-enhetens maskin varu komponenter via StorSimple Enhetshanteraren-tjänsten.
services: storsimple
documentationcenter: ''
author: alkohli
manager: twooley
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/15/2018
ms.author: alkohli
ms.openlocfilehash: 6b750659bf22e856ee8ad7368e3fea64dd7245df
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "94960367"
---
# <a name="use-the-storsimple-device-manager-service-to-monitor-hardware-components-and-status"></a>Använd tjänsten StorSimple Enhetshanteraren för att övervaka maskin varu komponenter och status

## <a name="overview"></a>Översikt
I den här artikeln beskrivs de olika fysiska och logiska komponenterna i din lokala StorSimple 8000-serie enhet. Du lär dig också hur du övervakar enhets komponentens status genom att använda bladet **status och maskin varu hälsa** i StorSimple Enhetshanteraren-tjänsten.

Bladet **status och maskin varu hälsa** visar maskin varu status för alla StorSimple enhets komponenter.

I listan över komponenter för 8100 finns tre avsnitt som beskriver:

* **Delade komponenter** – dessa är inte en del av styrenheterna, till exempel disk enheter, hölje, Power and kylning module-komponenter (PCM) och PCM-temperatur, linje spänning och linje Ströms sensorer.
* **Enhet 0-komponenter** – de komponenter som finns på styrenhet 0, till exempel kontrollant, SAS-expanderare och koppling, styrenhetens temperatur sensorer och de olika nätverks gränssnitten.
* **Komponenter för styrenhet 1** – de komponenter som utgör styrenhet 1, ungefär som de som beskrivs för Controller 0.

En 8600-enhet har ytterligare komponenter som motsvarar EBOD-kabinettet (Extended Bunted Disks). Det finns fem avsnitt under listan med komponenter. Av dessa finns det tre avsnitt som innehåller komponenterna i den primära inne slutningen och som är identiska med de som beskrivs i 8100. Det finns två ytterligare avsnitt för EBOD-kabinettet som beskriver:

* **EBOD Controller 0-komponenter** – de komponenter som finns på EBOD-kabinettet 0, t. ex. EBOD Controller, SAS-Expander och anslutning samt styrenhetens temperatur sensorer.
* **EBOD Controller 1-komponenter** – de komponenter som utgör EBOD-kabinett 1, ungefär som de som beskrivs i EBOD-kammaren 0.
* **Delade komponenter i EBOD-kabinett** – komponenterna som finns i EBOD-höljet och PCM som inte tillhör EBOD-kontrollanten.

> [!NOTE]
> **Maskin varu statusen är inte tillgänglig för en StorSimple Cloud Appliance (8010/8020).**


## <a name="monitor-the-hardware-status"></a>Övervaka maskin varu status
Utför följande steg för att Visa maskin varu status för en enhets komponent:

1. Navigera till **enheter**, Välj en speciell StorSimple-enhet. Gå till **övervaka > maskin varu hälsa**.

    ![Skärm bild som visar bladet inställningar i Enhetshanterarens tjänsten. Under övervakaren väljs maskin varu hälsa.](./media/storsimple-8000-monitor-hardware-status/hw-health1.png)

2. Leta upp avsnittet **maskin varu komponenter** och välj bland de tillgängliga komponenterna. Klicka bara på komponent etiketten för att expandera listan och visa statusen för de olika enhets komponenterna. Se listan [detaljerad komponent för den primära inne slutningen](#component-list-for-primary-enclosure-of-storsimple-device) och [listan med detaljerade komponenter för EBOD-höljet](#component-list-for-ebod-enclosure-of-storsimple-device).

    ![Skärm bild som visar en komponent som marker ATS i avsnittet maskin varu komponenter. Ett intilliggande blad visar namn och status för komponentens delar.](./media/storsimple-8000-monitor-hardware-status/hw-health2.png)

3. Använd följande färg kodnings schema för att tolka komponent status:
   
   * **Grön kontroll** – anger en felfri komponent med statusen **OK** .
   * **Gul** – anger en försämrad komponent i **varnings** tillstånd.
   * **Rött utrops tecken** – anger en misslyckad komponent som har statusen **fel** .
   * **Vit med svart text** – anger en komponent som inte finns.
   
   Följande skärm bild visar en enhet som har komponenter i läget **OK**, **Varning** och **fel** .
       
   ![Skärm bild som visar tre komponenter i avsnittet maskin varu komponenter: en i läget OK, ett i fel tillstånd och en i ett varnings tillstånd.](./media/storsimple-8000-monitor-hardware-status/hw-health3.png)

   Om du expanderar **listan delade komponenter** kan vi se att NVRAM och klustret är degraderat.

   ![Skärm bild som visar objektet delade komponenter markerade. På det intilliggande bladet är N V R A M och klustret i varnings tillstånd.](./media/storsimple-8000-monitor-hardware-status/hw-health5.png)

   Om du expanderar listan med **styrenhets 1-komponenter** kan vi se att klusternoden har misslyckats.  

   ![Skärm bild som visar objektet kontroll enhet 1-komponenter markerat. Klusternoden är i ett felaktigt tillstånd i det intilliggande bladet.](./media/storsimple-8000-monitor-hardware-status/hw-health4.png)  

4. Kontakta Microsoft Support om du stöter på en komponent som inte är i **felfritt** tillstånd. Om aviseringar är aktiverade på din enhet får du en e-postavisering. Om du behöver ersätta en felaktig maskin varu komponent kan du läsa mer i [StorSimple-komponent utbyte av maskin vara](./storsimple-8000-hardware-component-replacement.md).

## <a name="component-list-for-primary-enclosure-of-storsimple-device"></a>Komponent lista för primär inne slutning av StorSimple-enhet
I följande tabell visas de fysiska och logiska komponenterna som finns i den primära inne slutningen (finns både i 8100 och 8600) på din lokala StorSimple-enhet.

| Komponent | Modul | Typ | Location | Är fältet replacementable Unit (FRU)? | Beskrivning |
| --- | --- | --- | --- | --- | --- |
| Enhet i fack [0-11] |Disk enheter |Fysisk |Delad |Ja |En rad visas för var och en av SSD-enheterna eller hård diskarna i den primära inne slutningen. |
| Omgivnings temperatur sensor |Hölje |Fysisk |Delad |Inga |Mäter temperaturen i chassit. |
| Temperatur sensor för Mid-plan |Hölje |Fysisk |Delad |Inga |Mäter temperaturen i mitten av planet. |
| Akustiskt larm |Hölje |Fysisk |Delad |Inga |Anger om ljud signal under systemet på chassit fungerar. |
| Hölje |Hölje |Fysisk |Delad |Ja |Indikerar förekomsten av ett chassi. |
| Kabinett inställningar |Hölje |Fysisk |Delad |Inga |Hänvisar till chassits front panel. |
| Linje spännings sensorer |PCM |Fysisk |Delad |Inga |Många linje spännings sensorer har sitt tillstånd visas, vilket indikerar om den uppmätta spänningen är inom toleransen. |
| Linje aktuella sensorer |PCM |Fysisk |Delad |Inga |Många linje ström sensorer har sitt tillstånd visas, vilket anger om den uppmätta strömmen är inom toleransen. |
| Temperatur sensorer i PCM |PCM |Fysisk |Delad |Inga |Många temperatur sensorer, till exempel insugnings sensorer och hotspots sensorer har sitt tillstånd visas, vilket anger om den uppmätta temperaturen är inom toleransen. |
| Strömförsörjning [0-1] |PCM |Fysisk |Delad |Ja |En linje presenteras för var och en av strömförsörjningarna i de två PCMs som finns på bak sidan av enheten. |
| Kylning [0-1] |PCM |Fysisk |Delad |Ja |En linje presenteras för var och en av de fyra kyl fläktar som finns i de två PCMs. |
| Batteri [0-1] |PCM |Fysisk |Delad |Ja |En rad visas för var och en av de moduler för säkerhets kopierings batterier som finns i PCM. |
| Metis |Ej tillämpligt |Logiskt |Delad |Ej tillämpligt |Visar batteriets status: om de behöver debiteras och närmar sig livs längd. |
| Kluster |Ej tillämpligt |Logiskt |Delad |Ej tillämpligt |Visar status för det kluster som skapas mellan de två integrerade Controller-modulerna. |
| Klusternod |Ej tillämpligt |Logiskt |Delad |Ej tillämpligt |Anger status för kontrollanten som en del av klustret. |
| Klusterkvorum |Ej tillämpligt |Logiskt | |Ej tillämpligt |Anger förekomsten av majoriteten av disk medlemskapet i HDD-lagringspoolen. |
| Data utrymme på hård disk |Ej tillämpligt |Logiskt |Delad |Ej tillämpligt |Det lagrings utrymme som används för data i lagringspoolen för hård diskar (HDD). |
| Hanterings utrymme för HDD |Ej tillämpligt |Logiskt |Delad |Ej tillämpligt |Utrymmet som reserver ATS i lagringspoolen för HDD-lagring för hanterings uppgifter. |
| Kvorumdisk för HDD |Ej tillämpligt |Logiskt |Delad |Ej tillämpligt |Utrymmet som reserver ATS i HDD-lagringspoolen för klusterkvorum. |
| Utrymme för hård disk ersättning |Ej tillämpligt |Logiskt |Delad |Ej tillämpligt |Utrymmet som reserver ATS i lagringspoolen för HDD-lagring för ersättning av kontrollant. |
| Data utrymme för SSD |Ej tillämpligt |Logiskt |Delad |Ej tillämpligt |Det lagrings utrymme som används för data i SSD-lagringspoolen (Solid State Drive). |
| Utrymme för SSD NVRAM |Ej tillämpligt |Logiskt |Delad |Ej tillämpligt |Lagrings utrymmet i lagringspoolen SSD som är dedikerat för NVRAM-logik. |
| HDD-lagringspool |Ej tillämpligt |Logiskt |Delad |Ej tillämpligt |Visar status för den logiska lagringspoolen som har skapats från enhetens hård diskar. |
| Lagringspool för SSD |Ej tillämpligt |Logiskt |Delad |Ej tillämpligt |Visar status för den logiska lagringspoolen som har skapats från enhetens SSD. |
| Kontrollant [0-1] [State] |I/O |Fysisk |Kontrollant |Ja |Visar status för kontrollanten och om den är i aktivt eller vänte läge i chassit. |
| Temperatur sensorer i styrenheten |I/O |Fysisk |Kontrollant |Inga |Många temperatur sensorer, till exempel I/O-modul, CPU-temperatur, DIMM-och PCIe-sensorer har tillstånd att visas, vilket anger om temperaturen som påträffats är inom toleransen eller inte. |
| SAS-expandera |I/O |Fysisk |Kontrollant |Inga |Anger status för SAS-expanderaen (Serial Attached SCSI) som används för att ansluta den integrerade lagringen till kontrollanten. |
| SAS-anslutning [0-1] |I/O |Fysisk |Kontrollant |Inga |Anger status för varje SAS-anslutning som används för att ansluta integrerad lagring till SAS-expanderingen. |
| MITTPLANSSAMMANKOPPLING Mid-plan-sammanlänkning |I/O |Fysisk |Kontrollant |Inga |Anger läget för centrum-plan-kopplingen, som används för att ansluta varje kontrollant till mitten-planet. |
| Processor kärna |I/O |Fysisk |Kontrollant |Inga |Anger tillstånd för processor kärnor inom varje kontrollant. |
| Hölje Electronics Power |I/O |Fysisk |Kontrollant |Inga |Anger tillstånd för det energi system som används av kabinett filen. |
| Hölje Electronics-diagnostik |I/O |Fysisk |Kontrollant |Inga |Anger status för de under system för diagnostik som tillhandahålls av kontrollanten. |
| Styrenhet för huvudkortshantering (BMC) |I/O |Fysisk |Kontrollant |Inga |Anger status för BMC (Baseboard Management Controller), som är en specialiserad tjänst processor som övervakar maskin varu enheten via sensorer och kommunicerar med system administratören via en oberoende anslutning. |
| Ethernet |I/O |Fysisk |Kontrollant |Inga |Anger tillstånd för varje nätverks gränssnitt, det vill säga hanterings-och data portarna som finns på styrenheten. |
| Rensa |I/O |Fysisk |Kontrollant |Inga |Anger status för NVRAM, ett icke-flyktigt RAM-minne som säkerhets kopie ras av batteriet och som hanterar program kritisk information i händelse av strömavbrott. |

## <a name="component-list-for-ebod-enclosure-of-storsimple-device"></a>Komponent lista för EBOD-inne slutning av StorSimple-enhet
I följande tabell beskrivs de fysiska och logiska komponenterna i EBOD-kabinettet (endast i 8600-modellen) för den lokala StorSimple-enheten.

| Komponent | Modul | Typ | Location | Fru? | Beskrivning |
| --- | --- | --- | --- | --- | --- |
| Enhet i fack [0-11] |Disk enheter |Fysisk |Delad |Ja |En rad visas för var och en av de HDD-enheterna framför EBOD-höljet. |
| Omgivnings temperatur sensor |Hölje |Fysisk |Delad |Inga |Mäter temperaturen i chassit. |
| Temperatur sensor för Mid-plan |Hölje |Fysisk |Delad |Inga |Mäter temperaturen i mitten av planet. |
| Akustiskt larm |Hölje |Fysisk |Delad |Inga |Anger om ljud signal under systemet på chassit fungerar. |
| Hölje |Hölje |Fysisk |Delad |Ja |Indikerar förekomsten av ett chassi. |
| Kabinett inställningar |Hölje |Fysisk |Delad |Inga |Avser OPS eller front panel på chassit. |
| Linje spännings sensorer |PCM |Fysisk |Delad |Inga |Många linje spännings sensorer har sitt tillstånd visas, vilket indikerar om den uppmätta spänningen är inom toleransen. |
| Linje aktuella sensorer |PCM |Fysisk |Delad |Inga |Många linje ström sensorer har sitt tillstånd visas, vilket anger om den uppmätta strömmen är inom toleransen. |
| Temperatur sensorer i PCM |PCM |Fysisk |Delad |Inga |Många temperatur sensorer, till exempel insugnings sensorer och hotspots sensorer har sitt tillstånd visas, vilket anger om den uppmätta temperaturen är inom toleransen. |
| Strömförsörjning [0-1] |PCM |Fysisk |Delad |Ja |En linje presenteras för var och en av strömförsörjningarna i de två PCMs som finns på bak sidan av enheten. |
| Kylning [0-1] |PCM |Fysisk |Delad |Ja |En linje presenteras för var och en av de fyra kyl fläktar som finns i de två PCMs. |
| Lokal lagring [HDD] |Ej tillämpligt |Logiskt |Delad |Ej tillämpligt |Visar status för den logiska lagringspoolen som har skapats från enhetens hård diskar. |
| Kontrollant [0-1] [State] |I/O |Fysisk |Kontrollant |Ja |Visar status för kontrollanter i EBOD-modulen. |
| Temperatur sensorer i EBOD |I/O |Fysisk |Kontrollant |Inga |Ett flertal temperatur sensorer från varje kontrollant har sitt tillstånd visat, vilket anger om temperaturen påträffas inom toleransen. |
| SAS-expandera |I/O |Fysisk |Kontrollant |Inga |Anger status för SAS-expanderingen, som används för att ansluta den integrerade lagringen till kontrollanten. |
| SAS-anslutning [0-2] |I/O |Fysisk |Kontrollant |Inga |Anger status för varje SAS-anslutning som används för att ansluta integrerad lagring till SAS-expanderingen. |
| MITTPLANSSAMMANKOPPLING Mid-plan-sammanlänkning |I/O |Fysisk |Kontrollant |Inga |Anger läget för centrum-plan-kopplingen, som används för att ansluta varje kontrollant till mitten-planet. |
| Hölje Electronics Power |I/O |Fysisk |Kontrollant |Inga |Anger tillstånd för det energi system som används av kabinett filen. |
| Hölje Electronics-diagnostik |I/O |Fysisk |Kontrollant |Inga |Anger status för de under system för diagnostik som tillhandahålls av kontrollanten. |
| Anslutning till enhets styrenhet |I/O |Fysisk |Kontrollant |Inga |Anger status för anslutningen mellan EBOD I/O-modulen och enhets styrenheten. |

## <a name="next-steps"></a>Nästa steg
* Om du vill använda tjänsten StorSimple Enhetshanteraren för att administrera enheten går du till [Använd tjänsten StorSimple Enhetshanteraren för att administrera StorSimple-enheten](storsimple-8000-manager-service-administration.md).
* Om du behöver felsöka en enhets komponent som har statusen degraderad eller misslyckad, se [StorSimple övervaknings indikatorer](storsimple-monitoring-indicators.md).
* Information om hur du ersätter en felaktig maskin varu komponent finns i [StorSimple-utbyte av maskin varu komponenter](./storsimple-8000-hardware-component-replacement.md).
* [Kontakta Microsoft Support](storsimple-8000-contact-microsoft-support.md)om du fortsätter att uppleva enhets problem.