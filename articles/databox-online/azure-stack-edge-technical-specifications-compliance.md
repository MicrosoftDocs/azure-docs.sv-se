---
title: Tekniska specifikationer och efterlevnad för Microsoft Azure Stack Edge Pro | Microsoft Docs
description: Lär dig mer om tekniska specifikationer och efterlevnad för din Azure Stack Edge Pro
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 03/02/2020
ms.author: alkohli
ms.openlocfilehash: 8b36bb34f4c9081d807998cb8287797443625a6b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96460295"
---
# <a name="azure-stack-edge-pro-technical-specifications"></a>Tekniska specifikationer för Azure Stack Edge Pro

Maskin varu komponenterna i din Microsoft Azure Stack Edge Pro-enhet följer de tekniska specifikationer och reglerings standarder som beskrivs i den här artikeln. De tekniska specifikationerna beskriver strömförsörjnings enheter (PSUs), lagrings kapacitet, höljen och miljö standarder.

## <a name="compute-memory-specifications"></a>Beräkning, minnes krav

Azure Stack Edge Pro-enheten har följande specifikationer för beräkning och minne:

| Specifikation           | Värde                  |
|-------------------------|----------------------------|
| Processor    | 2 X 10 kärnor CPU Intel Xeon silver 4114 2,2 G                    |
| Minne              | 128 GB RAM-minne (8x 16 GB RDIMM)                 |

## <a name="fpga-specifications"></a>FPGA-specifikationer

En fält programmerbar grind mat ris (FPGA) ingår på varje Azure Stack Edge Pro-enhet som möjliggör Machine Learning-scenarier (ML).

| Specifikation           | Värde                  |
|-------------------------|----------------------------|
| FPGA   | Intel Arria 10 <br> Tillgängliga DNN-modeller (djup neurala Network) är desamma som de som [stöds av moln FPGA instanser](../machine-learning/how-to-deploy-fpga-web-service.md#fpga-support-in-azure).|

## <a name="power-supply-unit-specifications"></a>Specifikationer för strömförsörjnings enhet

Azure Stack Edge Pro-enheten har två 100-240 V-enheter för strömförsörjning (PSUs) med högpresterande fläktar. De två PSUs ger en redundant energi konfiguration. Om ett PSU Miss lyckas fortsätter enheten att fungera normalt på den andra PSU tills den felaktiga modulen har ersatts. I följande tabell visas de tekniska specifikationerna för PSUs.

| Specifikation           | 750 W PSU                  |
|-------------------------|----------------------------|
| Maximal uteffekt    | 750 W                     |
| Frekvens               | 50/60 Hz                   |
| Val av spännings intervall | Automatisk mellan: 100-240 V AC |
| Hot pluggable           | Ja                        |

### <a name="azure-stack-edge-pro-power-cord-specifications-by-region"></a>Specifikationer för Azure Stack Edge Pro-ström sladd per region

Din Azure Stack Edge Pro-enhet behöver en ström sladd som varierar beroende på din Azure-region.
Tekniska specifikationer för alla de ström sladdar som stöds finns i [Azure Stack Edge Pro Power kabel-specifikationer per region](azure-stack-edge-technical-specifications-power-cords-regional.md).

<!--## Power consumption statistics

The following table lists the typical power consumption data (actual values may vary from the published) for the Azure Stack Edge Pro device.-->

## <a name="network-interface-specifications"></a>Specifikationer för nätverks gränssnitt

Din Azure Stack Edge Pro-enhet har 6 nätverks gränssnitt, PORT1-PORT6.

| Specifikation           | Beskrivning                 |
|-------------------------|----------------------------|
|  Nätverksgränssnitt    | 2 x 1 GbE-gränssnitt – 1 för hantering som inte kan konfigureras av användaren används för installationen. Det andra gränssnittet kan konfigureras av användaren, kan användas för dataöverföring och är DHCP som standard. <br>2 x 25 GbE-gränssnitt – dessa kan även fungera som 10 GbE-gränssnitt. Dessa datagränssnitt kan konfigureras av användaren som DHCP (standard) eller statiska. <br> 2 x 25 GbE-gränssnitt – dessa datagränssnitt kan konfigureras av användaren som DHCP (standard) eller statiska.                  |

De nätverkskort som används är: 

| Specifikation           | Beskrivning                 |
|-------------------------|----------------------------|
|Network dotter-kort (rNDC) |QLogic FastLinQ 41264 med dubbel port 25GbE SFP +, dubbel port 1GbE, rNDC|
|PCI-nätverkskort |QLogic FastLinQ 41262 Zwei ports 25Gbit/s SFP28 adapter|

Se listan över maskinvarukompatibilitet från Intel QLogic för kompatibel Gigabit Interface Converter (GBIC). GBIC (Gigabit Interface Converter) ingår inte i leveransen av Azure Stack Edge. 

## <a name="storage-specifications"></a>Storage-specifikationer

Azure Stack Edge Pro-enheter har 9 X 2,5 "NVMe-SSD, med en kapacitet på 1,6 TB. Av dessa SSD, 1 är en operativ system disk och de andra 8 är data diskar. Den totala användbara kapaciteten för enheten är ungefär 12,5 TB. I följande tabell finns information om enhetens lagrings kapacitet.

|     Specifikation                          |     Värde             |
|--------------------------------------------|-----------------------|
|    Antal solid state-hårddiskar (SSD)     |    8                  |
|    Kapacitet för enskild SSD                     |    1,6 TB             |
|    Total kapacitet                          |    12,8 TB            |
|    Total användbar kapacitet *                  |    ~ 12,5 TB            |

**En del utrymme är reserverat för internt bruk.*

## <a name="enclosure-dimensions-and-weight-specifications"></a>Bilagans dimensioner och vikt specifikationer

I följande tabeller visas de olika specifikationerna för inne slutning för dimensioner och vikt.

### <a name="enclosure-dimensions"></a>Omslutnings dimensioner

I följande tabell visas storleken på höljet i millimeter och tum.

|     Hölje     |     Millimeter     |     Mm     |
|-------------------|---------------------|----------------|
|    Höjd         |    44,45            |    1,75 "          |
|    Bredd          |    434,1           |    17,09 "          |
|    Längd          |    740,4           |    29,15 "          |

I följande tabell visas måtten för leverans paketet i millimeter och tum.

|     Paket     |     Millimeter     |     Mm     |
|-------------------|---------------------|----------------|
|    Höjd         |    311,2            |    12,25 "          |
|    Bredd          |    642,8          |    25,31 "          |
|    Längd          |   1 051,1          |    41,38 "          |

### <a name="enclosure-weight"></a>Kabinett vikt

Enhets paketet väger 61 kg. och kräver två personer för att hantera det. Enhetens vikt beror på konfigurationen av kabinett filen.

|     Hölje                                 |     Vikt          |
|-----------------------------------------------|---------------------|
|    Total vikt inklusive förpackningen       |    61 kg.          |
|    Enhetens vikt                       |    35 kg.          |

## <a name="enclosure-environment-specifications"></a>Specifikationer för kabinett miljö

I det här avsnittet visas de specifikationer som är relaterade till inne slutnings miljön, till exempel temperatur, fuktighet och höjd.

### <a name="temperature-and-humidity"></a>Temperatur och fuktighet

|     Hölje         |     Temperatur intervall för omgivning     |     Omgivande relativ fuktighet     |     Maximal dagg punkt     |
|-----------------------|--------------------------------------|--------------------------------------|---------------------------|
|    Operativ        |    10 ° C – 35 ° C (50 °F-86 °F)         |    10% – 80% icke-kondenserande.         |    29 ° C (84 °F)            |
|    Fungerar inte    |    – 40 ° c till 65 ° c (-40 °F-149 °F)     |    5%-95% icke-kondenserande.          |    33 ° C (91 °F)            |

### <a name="airflow-altitude-shock-vibration-orientation-safety-and-emc"></a>Luft flöde, höjd, stöt, vibration, orientering, säkerhet och EMC

|     Hölje                           |     Operativa specifikationer                                                                                                                                                                                         |
|-----------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
|    Luft flöde                              |    Systemets luft flöde är framifrån och bak. Systemet måste köras med en installation med låg belastning. <!--Back pressure created by rack doors and obstacles should not exceed 5 pascals (0.5 mm water gauge).-->    |
|    Maximal höjd, drift        |    3048 meter (10 000 meter) med maximal drift temperatur som bedöms enligt [specifikationerna för drifts temperatur avklassificering](#operating-temperature-de-rating-specifications).                                                                                |
|    Maximal höjd, ej fungerande    |    12 000 meter (39 370 fot)                                                                                                                                                                                         |
|    Stöt, drift                   |    6 G i 11 millisekunder med 6 orienteringar                                                                                                                                                                         |
|    Stöt, ej fungerande               |    71 G i 2 millisekunder med 6 orienteringar                                                                                                                                                                           |
|    Vibrationer, drift               |    0,26 G<sub>RMS</sub> 5 Hz till 350 Hz slumpmässig                                                                                                                                                                                     |
|    Vibrationer, ej drift           |    1,88 G<sub>RMS</sub> 10 hz till 500 Hz i 15 minuter (alla sex sidor har testats)                                                                                                                                                  |
|    Orientering och montering             |    19 "rack montering                                                                                                                                                                                        |
|    Säkerhet och godkännanden                 |    EN 60950-1:2006 + a1:2010 + a2:2013 + A11:2009 + A12:2011/IEC 60950-1:2005 ed2 + a1:2009 + a2:2013 EN 62311:2008                                                                                                                                                                       |
|    EMC                                  |    FCC A, ICES-003 <br>EN 55032:2012/CISPR 32:2012  <br>EN 55032:2015/CISPR 32:2015  <br>EN 55024:2010 + A1:2015/CISPR 24:2010 + A1:2015  <br>EN 61000-3-2:2014/IEC 61000-3-2:2014 (klass D)   <br>EN 61000-3-3:2013/IEC 61000-3-3:2013                                                                                                                                                                                         |
|    Energi             |    Kommissionens förordning (EU) nr 617/2013                                                                                                                                                                                        |
|    RoHS           |    EN 50581:2012                                                                                                                                                                                        |

### <a name="operating-temperature-de-rating-specifications"></a>Specifikationer för drifts temperatur avklassificering

|     Värdering av drifts temperatur     |     Temperatur intervall för omgivning                                                         |
|--------------------------------------------|------------------------------------------------------------------------------------------|
|    Upp till 35 ° c (95 °F)                       |    Maximal temperatur minskas med 1 ° c/300 m (1 °F/547 ft) över 950 m (3 117 ft).    |
|    35 ° c till 40 ° c (95 °F till 104 °F)            |    Maximal temperatur minskas med 1 ° c/175 m (1 °F/319 ft) över 950 m (3 117 ft).    |
|    40 ° c till 45 ° c (104 °F till 113 °F)           |    Maximal temperatur minskas med 1 ° c/125 m (1 °F/228 ft) över 950 m (3 117 ft).    |

## <a name="next-steps"></a>Nästa steg

- [Distribuera Azure Stack Edge Pro](azure-stack-edge-deploy-prep.md)