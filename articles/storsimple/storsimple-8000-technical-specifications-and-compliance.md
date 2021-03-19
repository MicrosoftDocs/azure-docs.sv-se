---
title: Tekniska specifikationer för StorSimple | Microsoft Docs
description: Beskriver de tekniska specifikationerna och specifikationernas kompatibilitetsinformation för StorSimple-maskin varu komponenter.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/02/2017
ms.author: alkohli
ms.openlocfilehash: 061194422a8c1bc449dbef0c4f04bb8e1db10dea
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "68965295"
---
# <a name="technical-specifications-and-compliance-for-the-storsimple-device"></a>Tekniska specifikationer och efterlevnad för StorSimple-enheten

## <a name="overview"></a>Översikt

[!INCLUDE [storsimple-8000-eol-banner](../../includes/storsimple-8000-eol-banner.md)]

Maskin varu komponenterna i Microsoft Azure StorSimples enheten följer de tekniska specifikationer och regler som beskrivs i den här artikeln. De tekniska specifikationerna beskriver Power-och kylnings-modulerna (PCMs), disk enheter, lagrings kapacitet och höljen. Kompatibilitetsinformation täcker sådana saker som internationella standarder, säkerhet och utsläpp och kablar.

## <a name="power-and-cooling-module-specifications"></a>Specifikationer för energi-och kylnings modul

StorSimple-enheten har två 100-240 V-enheter med dubbel fläkt, MITTPLANSSAMMANKOPPLING-kompatibla Power kylning-moduler (PCMs). Detta ger en redundant energi konfiguration. Om en PCM Miss lyckas fortsätter enheten att fungera normalt på den andra PCM tills den felande modulen har ersatts.

EBOD-kabinettet använder en 580 W PCM och primär inne slutning använder en 764 W PCM. I följande tabeller visas de tekniska specifikationer som är associerade med PCMs.

| Specifikation | 580 W PCM (EBOD) | 764 W PCM (primär) |
| --- | --- | --- |
| Maximal uteffekt |580 W |764 |
| Frekvens |50/60 Hz |50/60 Hz |
| Val av spännings intervall |Automatisk mellan: 90 – 264 V AC, 47/63 Hz |Automatisk mellanliggande: 90 – 264 V AC, 47/63 Hz |
| Högsta inaktiva ström |20 A |20 A |
| Energi faktor korrigering |>95% nominell ingångs spänning |>95% nominell ingångs spänning |
| Övertoner |Uppfyller EN61000-3-2 |Uppfyller EN61000-3-2 |
| Utdata |5V standby-spänning \@ 2,0 A |5V standby-spänning \@ 2,7 A |
| + 5 v \@ 42 A |+ 5 v \@ 40 A | |
| + 12 V Nätspännings \@ 38 A |+ 12 V Nätspännings \@ 38 A | |
| Hot pluggable |Ja |Ja |
| Växlar och lysdioder |Växel ström på/av-växel och fyra status indikator lampor |Växel ström på/av-växel och sex status indikator lampor |
| Kabinett kylning |Koaxiala kyl fläktar med variabel fläkt hastighets kontroll |Koaxiala kyl fläktar med variabel fläkt hastighets kontroll |

## <a name="power-consumption-statistics"></a>Statistik för energi förbrukning

I följande tabell visas vanliga energi förbruknings data (de faktiska värdena kan variera beroende på publiceringen) för olika modeller av StorSimple-enheten.

| Villkor | 240 V AC | 240 V AC | 240 V AC | 110 V AC | 110 V AC | 110 V AC |
| --- | --- | --- | --- | --- | --- | --- |
|  Fläktar är långsamma, enheterna är inaktiva |1,45 A |0,31 kW |1057,76 BTU/timme |3,19 A |0,34 kW |1160,13 BTU/timme |
|  Fläktar är långsamma, åtkomst till enheter |1,54 A |0,33 kW |1126,01 BTU/timme |3,27 A |0,36 kW |1228,37 BTU/timme |
|  Fläktar snabbt, enheter inaktiva, två PSUs som drivs |2,14 A |0,49 kW |1671,95 BTU/timme |4,99 A |0,54 kW |1842,56 BTU/timme |
|  Fläktar snabbt, enheter inaktiva, en PSU med ett inaktivt |2,05 A |0,48 kW |1637,83 BTU/timme |4,58 A |0,50 kW |1706,07 BTU/timme |
|  Fläktar snabbt, enheters åtkomst, två PSUs som drivs |2,26 A |0,51 kW |1740,19 BTU/timme |4,95 A |0,54 kW |1842,56 BTU/timme |
|  Snabba fläktar, enheter som har åtkomst till, en PSU med ett inaktivt |2,14 A |0,49 kW |1671,95 BTU/timme |4,81 A |0,53 kW |1808,44 BTU/timme |

## <a name="disk-drive-specifications"></a>Specifikationer för disk enhet

Din StorSimple-enhet har stöd för upp till 12 3,5-tums form faktor seriellt anslutna SCSI-diskenheter (SAS). De faktiska enheterna kan vara en blandning av solid-state-hårddiskar (SSD) eller hård diskar, beroende på produkt konfigurationen. De 12 disk enhets platserna finns i 3 av 4-konfigurationen framför höljet. EBOD-kabinettet ger ytterligare lagrings utrymme för ytterligare 12 disk enheter. De är alltid hård diskar.

## <a name="storage-specifications"></a>Storage-specifikationer

StorSimple-enheter har en blandning av hård diskar och solid state-hårddiskar för både 8100 och 8600. Den totala användbara kapaciteten för 8100 och 8600 är ungefär 15 TB respektive 38 TB. I följande tabell dokumenteras information om SSD, hård disk och moln kapacitet i samband med StorSimple-lösningens kapacitet.

| Enhets modell/kapacitet | 8100 | 8600 |
| --- | --- | --- |
| Antal hård diskar (HDD) |8 |19 |
| Antal solid state-hårddiskar (SSD) |4 |5 |
| Enskild hård disk kapacitet |4 TB |4 TB |
| Kapacitet för enskild SSD |400 GB |800 GB |
| Reserv kapacitet |4 TB |4 TB |
| Användbar hård disk kapacitet |14 TB |36 TB |
| Användbar SSD-kapacitet |800 GB |2 TB |
| Total användbar kapacitet * |~ 15 TB |~ 38 TB |
| Maximal lösnings kapacitet (inklusive moln) |200 TB |500 TB |

<sup>* </sup>- *Den totala användbara kapaciteten inkluderar tillgänglig kapacitet för data, metadata och buffertar. Du kan etablera lokalt fästa volymer upp till 8,5 TB på 8100-enheten eller upp till 22,5 TB på den större 8600-enheten. Mer information finns på [StorSimple lokalt fästa volymer](storsimple-8000-local-volume-faq.md).*

## <a name="enclosure-dimensions-and-weight-specifications"></a>Bilagans dimensioner och vikt specifikationer

I följande tabeller visas de olika specifikationerna för inne slutning för dimensioner och vikt.

### <a name="enclosure-dimensions"></a>Omslutnings dimensioner

I följande tabell visas storleken på höljet i millimeter och tum.

| Hölje | Millimeter | Mm |
| --- | --- | --- |
| Höjd |87,9 |3,46 |
| Bredd över monterings fläns |483 |19,02 |
| Bredd på bröd texten i kammaren |443 |17,44 |
| Djup från Front montering till ytterlighets utrymme för kabinett text |577 |22,72 |
| Djup från Åtgärds panelen till den sista ytterlighets mängden för kabinett |630,5 |24,82 |
| Djup från montering av fläns till den sista ytterligheten i kammaren |603 |23,74 |

### <a name="enclosure-weight"></a>Kabinett vikt

Beroende på konfigurationen kan en fullständigt inläst primär enhet väga från 21 till 33 KGS och det krävs två personer för att hantera den.

| Hölje | Vikt |
| --- | --- |
| Maximal vikt (beror på konfigurationen) |30 kg – 33 kg |
| Tom (inga enheter är monterade) |21 – 23 kg |

## <a name="enclosure-environment-specifications"></a>Specifikationer för kabinett miljö

I det här avsnittet visas de specifikationer som är relaterade till inne slutnings miljön. Temperatur, fuktighet, höjd, stöt, vibration, orientering, säkerhet och elektro magnetisk kompatibilitet (EMC) ingår i den här kategorin.

### <a name="temperature-and-humidity"></a>Temperatur och fuktighet

| Hölje | Temperatur intervall för omgivning | Omgivande relativ fuktighet | Maximal våt lampa |
| --- | --- | --- | --- |
| Operativ |5 ° C – 35 ° C (41 °F-95 °F) |20%-80% icke-kondenserande – |28 ° C (82 °F) |
| Fungerar inte |-40 ° C – 70 ° C (40 °F-158 °F) |5%-100% icke-kondenserande |29 ° C (84 °F) |

### <a name="airflow-altitude-shock-vibration-orientation-safety-and-emc"></a>Luft flöde, höjd, stöt, vibration, orientering, säkerhet och EMC

| Hölje | Operativa specifikationer |
| --- | --- |
| Luft flöde |Systemets luft flöde är framifrån och bak. Systemet måste köras med en installation med låg belastning. Mottryck som skapats av rack dörrar och hinder får inte överstiga 5 Pascal (0,5 mm vatten mätare). |
| Höjd, drift |– 30 meter till 3045 meter (-100 meter till 10 000 fot) med maximal drift temperatur som avklassificeras med 5 ° c över 7000 m. |
| Höjd, ej fungerande |– 305 meter till 12 192 meter (-1 000 fot till 40 000 m) |
| Stöt, drift |5G 10 MS 1/2-sinus |
| Stöt, ej fungerande |30g 10 MS 1/2-sinus |
| Vibrationer, drift |0.21 g RMS 5-500 Hz slumpmässig |
| Vibrationer, ej drift |1.04 g RMS 2-200 Hz slumpmässig |
| Vibration, flyttning |3G 2-200 Hz sinus |
| Orientering och montering |19 "rack montering (2 EIA-enheter) |
| Rack skenor |För att passa minsta 700 mm-djupet (31,50 tum) och är kompatibelt med IEC 297 |
| Säkerhet och godkännanden |CE och UL EN 61000-3, IEC 61000-3, UL 61000-3 |
| EMC |EN55022 (CISPR-A), FCC A |

## <a name="international-standards-compliance"></a>Efterlevnad av internationella standarder

Din Microsoft Azure StorSimple-enhet uppfyller följande internationella standarder:  

* CE-EN 60950-1
* CB-rapport till IEC 60950-1
* UL och cUL till UL 60950-1

## <a name="safety-compliance"></a>Säkerhetskompatibilitet

Din Microsoft Azure StorSimple enhet uppfyller följande säkerhets klassificeringar:

* Godkännande av system produkt typ: UL, cUL, CE
* Säkerhetskompatibilitet: UL 60950, IEC 60950, EN 60950

## <a name="emc-compliance"></a>EMC-kompatibilitet

Din Microsoft Azure StorSimple-enhet uppfyller följande EMC-klassificeringar.

### <a name="emissions"></a>Avgas

Enheten är EMC-kompatibel för genomförda och utstrålade utsläpps nivåer.

* Utsläppta utsläpps nivåer: CFR 47 del 15B klass A EN55022 klass a CISPR klass A
* Utsläpps gräns nivåer: CFR 47 del 15B klass A EN55022 klass a CISPR klass A

### <a name="harmonics-and-flicker"></a>Övertoner och flimmer

Enheten överensstämmer med EN61000-3-2/3.

### <a name="immunity-limit-levels"></a>Gräns nivåer för immunitet

Enheten överensstämmer med EN55024.

## <a name="ac-power-cord-compliance"></a>Kompatibilitet för ström sladd

Plug and Play-sammansättningen måste uppfylla de standarder som är lämpliga för det land/den region där enheten används, och de måste ha säkerhets godkännanden som är acceptabla i landet/regionen. I följande tabeller listas standarder för USA och Europa.

### <a name="ac-power-cords---usa-must-be-nrtl-listed"></a>AC-nätkablar – USA (måste vara NRTL listat)

| Komponent | Specifikation |
| --- | --- |
| Typ av sladd |SV eller SVT, 18 AWG minimum, 3 ledare, 2,0 meter maximal längd |
| Tillägg |NEMA 5-15P-typ av bilaga som är tilldelad 120 V, 10 A; eller IEC 320 C14, 250 V, 10 A |
| Socketanslutning |IEC 320 C-13, 250 V, 10 A |

### <a name="ac-power-cords---europe"></a>AC-nätsladd – Europa

| Komponent | Specifikation |
| --- | --- |
| Typ av sladd |Harmoniserad, H05-VVF – 3G 1.0 |
| Socketanslutning |IEC 320 C-13, 250 V, 10 A |

## <a name="supported-network-cables"></a>Nätverks kablar som stöds

För 10 GbE-nätverks gränssnitt, DATA 2 och DATA 3, se [listan över nätverks kablar och moduler som stöds](storsimple-supported-hardware-for-10-gbe-network-interfaces.md).

## <a name="next-steps"></a>Nästa steg

Du är nu redo att distribuera en StorSimple-enhet i ditt data Center. Mer information finns i [distribuera din lokala enhet](storsimple-8000-deployment-walkthrough-u2.md).

