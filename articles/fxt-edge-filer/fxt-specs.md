---
title: Specifikationer för Microsoft Azure FXT Edge | Microsoft Docs
description: Lär dig mer om de fysiska och miljömässiga specifikationerna för Microsoft Azure FXT Edge-maskin vara.
author: ekpgh
ms.service: fxt-edge-filer
ms.topic: conceptual
ms.date: 06/20/2019
ms.author: rohogue
ms.openlocfilehash: b1a172adb54593421ef56e09c131e0ddcaee9f6e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "92215832"
---
# <a name="azure-fxt-edge-filer-specifications"></a>Specifikationer för Azure FXT Edge-filer

I den här artikeln beskrivs maskin varu specifikationerna för maskin varu noder i Azure FXT Edge. I praktiken konfigureras tre eller fler noder tillsammans för att tillhandahålla det klustrade cache-systemet.

## <a name="hardware-specifications"></a>Maskinvaruspecifikationer

| Komponent | FXT 6600 | FXT 6400 |
|----------|-----------|-----------|
| Processorkärnor |  16 | 16 |
| DYNAMISKA  | 1536 GB | 768 GB |
| Nätverksportar | 6 x 25/10 GB + 2 x 1 GB | 6 x 25/10 GB + 2 x 1 GB |
| NVMe SSD-kapacitet | 25,6 TB | 12,8 TB |

## <a name="drive-specifications"></a>Enhets specifikationer

Systemet har tio enhets fack som är tillgängliga från fram sidan. Varje ifylld enhet är märkt till höger med kapacitets information.

Enhets nummer skrivs ut på utrymmet mellan enheter. I Azure FXT Edge-filer är enhet 0 den övre vänstra enheten och enhet 1 är direkt under den.

![Foto av ett hård disk fack i FXT-chassit, som visar enhets nummer och kapacitets etiketter](media/fxt-drives-photo.png)

| Enhets nummer    |  Användning   |  Specifikationer |
|------------------|--------|-----------------|
| 0, 1             | Operativsystem     | 480 GB SATA SSD |
| 2, 3, 4, 5, 6, 7, 8, 9 | Data   | FXT 6600:3,2 TB NVMe SSD <br> FXT 6400:1,6 TB NVMe SSD |

## <a name="dimensions-and-weight"></a>Dimensioner och vikt

Azure FXT Edge-filer har utformats för att passa i ett standardiserat "utrustnings rack" och är en hög rack enhet hög (1U).

<!-- 10x2.5 inches version -->

| Filter för filer            | Värde                    |
|-----------------------------|--------------------------|
| Höjd                      | 42,8 mm (1,68 tum)    |
| Bredd (inklusive rack öron) | 482,0 mm (18,97 tum)  |
| Bredd – huvud hölje      | 434,0 mm (17,08 tum) |
| Djup – rack öron över huvud kabinettet                   | 733,82 mm (29,61 tum) |
| Djup – rack öron till längst bak protrusion                 | 772,67 mm (30,42 tum) |
| Djup – rack öron till längst fram protrusion, utan ram | 22,0 mm (0,87 tum)  |
| Djup – rack öron till längst fram-protrusion, med panel    | 35,84 mm (1,41 tum) |

| Vikt | Värde |
|-----------------|----------------------|
| Node vikt (utan emballage, utan tillbehör) | 40 kg (18,1 kg) |
| Netto vikt (utan paketering, inklusive tillbehör) | 51 kg (23,1 kg)|
| Brutto vikt (som levererad, inklusive alla förpackningar) |  64 kg (29,0 kg) |

### <a name="shipping-dimensions"></a>Leverans dimensioner

| Paket dimension | Millimeter | Mm |
|-------------------|-------------|--------|
| Höjd            | 311,2       | 12,25 " |
| Bredd             | 642,8       | 25,31 " |
| Längd            | 1 051,1     | 41,38 " |

## <a name="power-and-thermal-specifications"></a>Energi-och termisk-specifikationer

Det här avsnittet innehåller energi värderingar och mått för Azure FXT Edge-filer.

### <a name="nameplate-ratings"></a>Nameplate-klassificering

| Nameplate-klassificeringar för FXT 6000-serie modeller |
|----------------|
| 100 – 240V ~    |
| 10A-5A (X2)  |
| 50/60 Hz         |

<!-- matches the Dell regulatory label exactly -->

### <a name="power-and-thermal-measurements"></a>Energi-och värme mått

Azure FXT Edge-noderna använder varierande hastighets fläktar, så kraft beror på temperatur och belastning. Högsta antalet fläkt hastigheter kan nås vid vissa kombinationer av hög belastning och förhöjt omgivande temperaturer.

De här diagrammen ger mått för strömförbrukning och termiska utdata för vanliga kombinationer av spännings frekvens.

| FXT 6600-kraft vid rums temperatur <br />(22 ° C, 71,6 ° F) | 100 V, 60 Hz | 120 V, 60 Hz | 208 V, 60 Hz | 230 V, 50 Hz | 240 V, 50 Hz |
|---------|---|---|---|---|---|
| Spänning (V) | 100 | 120 | 208 | 230 | 240 |
| Frekvens (Hz) | 60 | 60 | 60 | 50 | 50 |
| Aktuell (A) | 5,02 | 4,16 |2,40 | 2,20 | 2,16 |
| Synbar ström (VA) | 502 | 499 | 499 | 506 | 518|
| Effekt faktor | 0,99 | 0,99 |0,98 | 0,98 | 0,98 |
| Verklig effekt (W) | 497 |494 | 489 | 496 | 508 |
| Termisk avledning (BTU/timme) |1696 | 1686 | 1669 | 1692 | 1733 |

| FXT 6600-kraft med högsta fläkt hastigheter | 100 V, 60 Hz | 120 V, 60 Hz | 208 V, 60 Hz | 230 V, 50 Hz | 240 V, 50 Hz |
|---------|---|---|---|---|---|
| Spänning (V) | 100 |120 | 208 | 230 | 240|
| Frekvens (Hz) | 60 | 60 | 60 | 50 | 50 |
| Aktuell (A) | 5,98 | 5,01 | 2,81 | 2.55 | 2,48 |
| Synbar ström (VA) | 598 | 601 | 584 | 587 | 595 |
| Effekt faktor | 0,99 | 0,99 | 0,98 | 0,98 | 0,98 |
| Verklig effekt (W) | 592 | 595 | 573 | 575 | 583 |
| Termisk avledning (BTU/timme) | 2020 |2031 | 1954 | 1961 | 1990 |

| FXT 6400-kraft vid rums temperatur <br />(22 ° C, 71,6 ° F) | 100 V, 60 Hz | 120 V, 60 Hz | 208 V, 60 Hz | 230 V, 50 Hz | 240 V, 50 Hz |
|---------|---|---|---|---|---|
| Spänning (V) | 100 | 120 | 208 | 230 | 240 |
| Frekvens (Hz) |60 | 60 | 60 | 50 | 50 |
| Aktuell (A) | 4,63 | 3,86 | 2.24 | 2,04 | 1,94 |
| Synbar ström (VA) | 463 | 463 | 466 | 469 | 466 |
| Effekt faktor | 0,99 | 0,99 | 0,98 | 0,98 | 0,98 |
| Verklig effekt (W) | 458 | 459 | 457 | 460 | 456 |
| Termisk avledning (BTU/timme) | 1564 | 1565 | 1558 | 1569 | 1557 |

| FXT 6400-kraft med högsta fläkt hastigheter | 100 V, 60 Hz | 120 V, 60 Hz | 208 V, 60 Hz | 230 V, 50 Hz | 240 V, 50 Hz |
|---------|---|---|---|---|---|
| Spänning (V) | 100 | 120 | 208 | 230 | 240 |
| Frekvens (Hz) | 60 | 60 | 60 | 50 | 50 |
| Aktuell (A) | 5,15 | 4,28 | 2,48 | 2,28 | 2,13 |
| Synbar ström (VA) | 515 | 514 | 516 | 524 | 511 |
| Effekt faktor | 0,99 | 0,99 | 0,98 | 0,98 | 0,98 |
| Verklig effekt (W) | 510 | 508 | 506 | 514 | 501 |
| Termisk avledning (BTU/timme) | 1740 | 1735 | 1725 | 1753 | 1709 |

## <a name="environmental-requirements"></a>Miljö krav

Det här avsnittet innehåller specifikationer för maskin varans omgivande miljö.

### <a name="temperature-and-humidity"></a>Temperatur och fuktighet

| Miljö-attribut   | Operativ intervall                   | Utanför drifts området         |
|---------------------------|-----------------------------------|-----------------------------|
| Temperatur intervall för omgivning | 10 ° c till 35 ° c (50 – 86 °F)          | – 40 ° c till 65 ° c (-40-149 °F) |
| Omgivande relativ fuktighet | 10% – 80% icke-kondenserande          | 5%-95% icke-kondenserande     |
| Maximal dagg punkt         | 29 ° C (84 °F)                       | 33 ° C (91 °F)                 |
| Höjden                  | upp till 3048 meter (10 000 meter), beroende på vilken temperatur som anges nedan | upp till 12 000 meter (39 370 meter) |

> [!NOTE]
> **Avklassificering av höjd temperatur:** Maximal temperatur minskas med 1 ° c/300 m (1 °F/547 ft) över 950 m (3 117 ft).

### <a name="airflow-shock-and-vibration"></a>Luft flöde, stöt och vibration

| Attribut         | Specifikation |
|-------------------|---------------|
| Luft flöde                    | Systemets luft flöde är framifrån och bak. Systemet måste köras med låg belastnings installation. |
| Stöt, drift         | 6 G i 11 millisekunder (testas i 6 riktningar) |
| Stöt, ej fungerande     | 71 G i 2 millisekunder (testas i 6 riktningar) |
| Vibrationer, drift     | 0,26 G<sub>RMS</sub> 5 Hz till 350 Hz slumpmässig         |
| Vibrationer, ej drift | 1,88 G<sub>RMS</sub> 10 hz till 500 Hz i 15 minuter (alla sex sidor har testats)  |

## <a name="safety-regulation-compliance"></a>Efterlevnad av säkerhets regler

Azure FXT Edge-filer uppfyller de angivna reglerna.

| Kategori       | Regel specifikation |
|----------------|--------------------------|
| Allmän säkerhet | EN 60950-1:2006 + a1:2010 + a2:2013 + A11:2009 + A12:2011/IEC 60950-1:2005 ed2 + a1:2009 + a2:2013 <br>EN 62311:2008 |
| EMC            | FCC A, ICES-003  <br>EN 55032:2012/CISPR 32:2012  <br>EN 55032:2015/CISPR 32:2015  <br>EN 55024:2010 + A1:2015/CISPR 24:2010 + A1:2015  <br>EN 61000-3-2:2014/IEC 61000-3-2:2014 (klass D)   <br>EN 61000-3-3:2013/IEC 61000-3-3:2013 |
| Energi         | Kommissionens förordning (EU) nr 617/2013  |
| RoHS           |    EN 50581:2012   |
