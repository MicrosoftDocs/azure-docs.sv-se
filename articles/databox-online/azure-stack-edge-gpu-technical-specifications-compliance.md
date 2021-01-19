---
title: Microsoft Azure Stack Edge Pro med GPU tekniska specifikationer och efterlevnad | Microsoft Docs
description: Lär dig mer om tekniska specifikationer och efterlevnad för din Azure Stack Edge Pro-enhet med GPU
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: conceptual
ms.date: 01/19/2021
ms.author: alkohli
ms.openlocfilehash: d40d26e8fab0832a37a43c353e11189f8f193f14
ms.sourcegitcommit: 65cef6e5d7c2827cf1194451c8f26a3458bc310a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/19/2021
ms.locfileid: "98573334"
---
# <a name="technical-specifications-and-compliance-for-azure-stack-edge-pro-with-gpu"></a>Tekniska specifikationer och efterlevnad för Azure Stack Edge Pro med GPU 

Maskin varu komponenterna i Azure Stack Edge Pro med en onboard Graphics Processing Unit (GPU) följer de tekniska specifikationer och regler som beskrivs i den här artikeln. De tekniska specifikationerna beskriver maskin vara, strömförsörjnings enheter (PSUs), lagrings kapacitet, höljen och miljö standarder.

## <a name="compute-and-memory-specifications"></a>Beräknings-och minnes specifikationer

Azure Stack Edge Pro-enheten har följande specifikationer för beräkning och minne:

| Specifikation           | Värde                  |
|-------------------------|----------------------------|
| Processor                     | 2 X Intel Xeon silver 4214-processor (kaskad)            |
| Minne                  | 128 GB RAM-minne (8x16 GB) <br> Dell-kompatibel 16 GB PC4 – 23400 DDR4 – 2933Mhz 2Rx8 1.2 v ECC registrerat RDIMM       |


## <a name="compute-acceleration-specifications"></a>Specifikationer för beräknings acceleration

En GPU (Graphics Processing Unit) ingår på varje Azure Stack Edge Pro-enhet som möjliggör Kubernetes, djup inlärning och maskin inlärnings scenarier.

| Specifikation           | Värde                  |
|-------------------------|----------------------------|
| GPU   | En eller två nVidia T4 GPU: er <br> Mer information finns i [NVIDIA-T4](https://www.nvidia.com/en-us/data-center/tesla-t4/).| 


## <a name="power-supply-unit-specifications"></a>Specifikationer för strömförsörjnings enhet

Azure Stack Edge Pro-enheten har två 100-240 V-enheter för strömförsörjning (PSUs) med högpresterande fläktar. De två PSUs ger en redundant energi konfiguration. Om ett PSU Miss lyckas fortsätter enheten att fungera normalt på den andra PSU tills den felaktiga modulen har ersatts. I följande tabell visas de tekniska specifikationerna för PSUs.

| Specifikation           | 750 W PSU                  |
|-------------------------|----------------------------|
| Maximal uteffekt    | 750 W                     |
| Frekvens               | 50/60 Hz                   |
| Val av spännings intervall | Automatisk mellan: 100-240 V AC |
| Hot pluggable           | Ja                        |


## <a name="network-interface-specifications"></a>Specifikationer för nätverks gränssnitt

Din Azure Stack Edge Pro-enhet har sex nätverks gränssnitt, PORT1-PORT6.

| Specifikation           | Beskrivning                 |
|-------------------------|----------------------------|
|  Nätverksgränssnitt    | **2 X 1 GbE-gränssnitt** – 1 hanterings gränssnitt port 1 används för inledande installation och är statisk som standard. När den första installationen är klar kan du använda gränssnittet för data med valfri IP-adress. Men vid återställning återgår gränssnittet till statisk IP. <br>Den andra gränssnitts porten 2 är användaren konfigurerbar, kan användas för data överföring och är DHCP som standard. <br>**4 X 25 GbE-gränssnitt** – dessa data gränssnitt, Port 3 till port 6, kan konfigureras av användaren som DHCP (standard) eller statisk. De kan också arbeta som 10 GbE-gränssnitt.  | 

Din Azure Stack Edge Pro-enhet har följande nätverks maskin vara:

* **Anpassad Microsoft Qlogic Cavium 25G Norwegian Developers Conference adapter** -port 1 till Port 4.
* **Mellanox dual port 25G ConnectX – 4 kanal nätverkskort** -Port 5 och port 6.

Här följer information om Mellanox-kortet:

| Parameter           | Beskrivning                 |
|-------------------------|----------------------------|
| Modell    | ConnectX® – 4 LX ett nätverkskort                      |
| Modell Beskrivning               | 25 GbE dual-port SFP28; PCIe 3.0 x8; ROHS-R6                    |
| Enhets dels nummer (R640) | MCX4121A-ACAT  |
| PSID (R640)           | MT_2420110034                         |

En fullständig lista över kablar, växlar och Sänd tagare som stöds för dessa nätverkskort finns på:

- [Cavium för QLogic 25G Norwegian Developers Conference adapter](https://www.marvell.com/documents/xalflardzafh32cfvi0z/).
- [Mellanox dual port 25G ConnectX – 4 kanals nätverkskort kompatibla produkter](https://docs.mellanox.com/display/ConnectX4LxFirmwarev14271016/Firmware+Compatible+Products).  

## <a name="storage-specifications"></a>Storage-specifikationer

Azure Stack Edge Pro-enheter har fem 2,5 "NVMe DC P4610-SSD, var och en med en kapacitet på 1,6 TB. Startenheten är 240 GB SATA SSD. Den totala användbara kapaciteten för enheten är ungefär 4,19 TB. I följande tabell visas enhetens lagrings kapacitet.

|     Specifikation                          |     Värde             |
|--------------------------------------------|-----------------------|
|    Antal NVMe-SSD                     |    5                  |
|    Enskild NVMe SSD-kapacitet                |    1,6 TB             |
|    SATA-hårddiskar med solid state-hårddiskar (SSD)      |    1                  |
|    Start SSD-kapacitet                       |    240 GB             |
|    Total kapacitet                          |    8,0 TB             |
|    Total användbar kapacitet                   |    ~ 4,19 TB          |
|    RAID-konfiguration                      |    Lagringsdirigering med en kombination av spegling och paritet  |
|    SAS-styrenhet                          |    HBA330 12 Gbit/s     |

<!--Remove based on feedback from Ravi
## Other hardware specifications

Your Azure Stack Edge Pro device also contains the following hardware:

* iDRAC baseboard management
* Performance fans
* Custom ID module-->

## <a name="enclosure-dimensions-and-weight-specifications"></a>Bilagans dimensioner och vikt specifikationer

I följande tabeller visas de olika specifikationerna för inne slutning för dimensioner och vikt.

### <a name="enclosure-dimensions"></a>Omslutnings dimensioner

I följande tabell visas måtten för 1U-enhetens hölje i millimeter och tum.

|     Hölje     |     Millimeter     |     Mm     |
|-------------------|---------------------|----------------|
|    Höjd         |    44,45            |    1,75 "       |
|    Bredd          |    434,1            |    17,09 "      |
|    Längd         |    740,4            |    29,15 "      |

I följande tabell visas måtten för leverans paketet i millimeter och tum.

|     Paket     |     Millimeter     |     Mm     |
|-------------------|---------------------|----------------|
|    Höjd         |    311,2            |    12,25 "          |
|    Bredd          |    642,8            |    25,31 "          |
|    Längd         |    1 051,1          |    41,38 "          |

### <a name="enclosure-weight"></a>Kabinett vikt

Enhets paketet väger 66 kg. och kräver två personer för att hantera det. Enhetens vikt beror på konfigurationen av kabinett filen.

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
| Ingress-skydd (IP)                 |    Den här typen av rack monterings utrustning för inomhus bruk är vanligt vis inte testad för ingress-skydd (skydd mot solider och vätskor för ett elektriskt hölje). Tillverkarens säkerhets bedömning visar IPXO (inga ingångs skydd).  |
|    Maximal höjd, drift        |    3048 meter (10 000 meter) med maximal drift temperatur som bedöms enligt [specifikationerna för drifts temperatur avklassificering](#operating-temperature-de-rating-specifications).                                                                                |
|    Maximal höjd, ej fungerande    |    12 000 meter (39 370 fot)                                                                                                                                                                                         |
|    Stöt, drift                   |    6 G i 11 millisekunder med 6 orienteringar                                                                                                                                                                         |
|    Stöt, ej fungerande               |    71 G i 2 millisekunder med 6 orienteringar                                                                                                                                                                           |
|    Vibrationer, drift               |    0,26 G<sub>RMS</sub> 5 Hz till 350 Hz slumpmässig                                                                                                                                                                                     |
|    Vibrationer, ej drift           |    1,88 G<sub>RMS</sub> 10 hz till 500 Hz i 15 minuter (alla sex sidor har testats)                                                                                                                                                  |
|    Orientering och montering             |    Standard 19-tums rack montering (1U)                                                                                                                                                                                       |
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

[Distribuera Azure Stack Edge Pro](azure-stack-edge-gpu-deploy-prep.md)
