---
title: Tekniska specifikationer och efterlevnad för Microsoft Azure Stack Edge Mini R | Microsoft Docs
description: Lär dig mer om tekniska specifikationer och efterlevnad för din Azure Stack Edge-Mini R-enhet
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: conceptual
ms.date: 03/01/2021
ms.author: alkohli
ms.openlocfilehash: 3a0b87f04e60fd56d543c7c7a752cd788e087c78
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "101727489"
---
# <a name="azure-stack-edge-mini-r-technical-specifications"></a>Tekniska specifikationer för Azure Stack Edge Mini R

Maskin varu komponenterna i din Microsoft Azure Stack Edge Mini R-enhet följer de tekniska specifikationer och regler som beskrivs i den här artikeln. De tekniska specifikationerna beskriver CPU, minne, strömförsörjnings enheter (PSUs), lagrings kapacitet, omslutnings dimensioner och vikt.


## <a name="compute-memory-specifications"></a>Beräkning, minnes krav

Azure Stack Edge Mini R-enheten har följande specifikationer för beräkning och minne:

| Specifikation           | Värde                  |
|-------------------------|------------------------|
| Processor    | 16 kärnor CPU, Intel Xeon-D 1577 |
| Minne              | 48 GB RAM-minne (2400 MT/s)                  |


## <a name="compute-acceleration-specifications"></a>Specifikationer för beräknings acceleration

En VPU (vision Processing Unit) ingår på varje Azure Stack Edge Mini R-enhet som möjliggör Kubernetes, djup neurala nätverk och dator visionbaserade program.

| Specifikation           | Värde                  |
|-------------------------|------------------------|
| Compute accelerations kort         | Intel Movidius myriaden X VPU <br> Mer information finns i [Intel Movidius myriaden X VPU](https://www.movidius.com/MyriadX) |


## <a name="storage-specifications"></a>Storage-specifikationer

Azure Stack Edge Mini R-enheten har 1 data disk och 1 start disk (som fungerar som operativ system lagring). I följande tabell visas information om enhetens lagrings kapacitet.

|     Specifikation                          |     Värde             |
|--------------------------------------------|-----------------------|
|    Antal solid state-hårddiskar (SSD)     |    2 X 1 TB diskar <br> En datadisk och en start disk                  |
|    Kapacitet för enskild SSD                     |    1 TB               |
|    Total kapacitet (endast data)              |    1 TB              |
|    Total användbar kapacitet *                  |    ~ 750 GB        |

**En del utrymme är reserverat för internt bruk.*

## <a name="network-specifications"></a>Nätverks specifikationer

Azure Stack Edge Mini R-enheten har följande specifikationer för nätverk:


|Specifikation  |Värde  |
|---------|---------|
|Nätverksgränssnitt    |2 x 10 GbE SFP + <br> Visas som PORT 3 och PORT 4 i det lokala användar gränssnittet           |
|Nätverksgränssnitt    |2 x 1 GbE RJ45 <br> Visas som PORT 1 och PORT 2 i det lokala användar gränssnittet          |
|Wi-Fi   |802.11ac         |


## <a name="power-supply-unit-specifications"></a>Specifikationer för strömförsörjnings enhet

Azure Stack Edge Mini R-enheten innehåller ett externt 85 W nätadapter för att tillhandahålla strömförsörjning och debitera det inbyggda batteriet.

I följande tabell visas specifikationer för strömförsörjnings enhet:

| Specifikation           | Värde                      |
|-------------------------|----------------------------|
| Maximal uteffekt    | 85 W                       |
| Frekvens               | 50/60 Hz                   |
| Val av spännings intervall | Automatisk mellan: 100-240 V AC |



## <a name="included-battery"></a>Inkluderat batteri

Azure Stack Edge Mini R-enheten innehåller också ett onboard-batteri som debiteras av strömförsörjningen.

Ett ytterligare [typ 2590-batteri](https://www.bren-tronics.com/bt-70791ck.html) kan användas tillsammans med det inbyggda batteriet för att utöka användningen av enheten mellan avgifterna. Batteriet bör vara kompatibelt med alla regler för säkerhet, transport och miljö som är tillämpliga i användnings landet.


| Specifikation           | Värde                      |
|-------------------------|----------------------------|
| Onboard Battery-kapacitet | 73 wattimmar                    |

## <a name="enclosure-dimensions-and-weight-specifications"></a>Bilagans dimensioner och vikt specifikationer

I följande tabeller visas de olika specifikationerna för inne slutning för dimensioner och vikt.

### <a name="enclosure-dimensions"></a>Omslutnings dimensioner

I följande tabell visas enhetens mått och USP med det robusta fallet i millimeter och tum.

|     Hölje     |     Millimeter     |     Mm     |
|-------------------|---------------------|----------------|
|    Höjd         |    68            |    2,68          |
|    Bredd          |    208          |      8,19          |
|    Längd          |   259           |    10,20          |


### <a name="enclosure-weight"></a>Kabinett vikt

I följande tabell visas vikten för enheten, inklusive batteri.

|     Hölje                                 |     Vikt          |
|-----------------------------------------------|---------------------|
|    Enhetens totala vikt     |    7 kg.          |

## <a name="enclosure-environment-specifications"></a>Specifikationer för kabinett miljö


I det här avsnittet visas de specifikationer som är relaterade till inne slutnings miljön, till exempel temperatur, fuktighet och höjd.


|     Specifikationer             |     Beskrivning                                                          |
|--------------------------------|--------------------------------------------------------------------------|
|     Temperatur intervall          |     0 – 43 ° C (drift)                                              |
|     Vibrationer                  |     MIL-STD-810-metod 514,7 *<br> Procedure I CAT 4, 20                  |
|     Skydd                      |     MIL-STD-810-metod 516,7 *<br> Procedur IV, logistik                 |
|     Höjden                   |     Drift: 10 000 fot<br> Ej fungerande: 40 000 fot          |

**Alla referenser är till MIL-STD-810G Change 1 (2014)*


## <a name="next-steps"></a>Nästa steg

- [Distribuera Azure Stack Edge Mini R](azure-stack-edge-placeholder.md)
