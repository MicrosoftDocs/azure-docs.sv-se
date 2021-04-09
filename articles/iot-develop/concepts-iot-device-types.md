---
title: Översikt över Azure IoT-enhets typer
description: Läs om de olika enhets typer som stöds av Azure IoT och verktygen som är tillgängliga.
author: ryanwinter
ms.author: rywinter
ms.service: iot-develop
ms.topic: conceptual
ms.date: 01/11/2021
ms.openlocfilehash: aa99594fe3de98635e37d15beebf015f15dc4f64
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "100654565"
---
# <a name="overview-of-azure-iot-device-types"></a>Översikt över Azure IoT-enhets typer
IoT-enheter finns i ett brett urval av maskinvaruplattformar. Det finns små 8-bitars MCU hela vägen upp till de senaste x86-processorerna som finns på en stationär dator. Många variabler är en faktor i beslutet för vilken maskin vara du kan välja för en IoT-enhet och den här artikeln beskriver några av de viktigaste skillnaderna.

## <a name="key-hardware-differentiators"></a>Viktiga maskin varu differentieringar
Vissa viktiga faktorer när du väljer maskin vara är kostnad, energi förbrukning, nätverk och tillgängliga indata och utdata.

* **Kostnad:** Mindre billigare enheter används vanligt vis vid Mass framställning av den slutliga produkten. Men kompromisser är att utvecklingen av enheten kan vara dyrare än den mycket begränsade enheten. Utvecklings kostnaden kan spridas över alla producerade enheter så att kostnad per enhet är låg.

* **Effekt:** Hur mycket strömförsörjning en enhet förbrukar är viktig om enheten använder batterier och inte är ansluten till Power Grid. MCU är ofta utformade för lägre energi scenarier och kan vara ett bättre alternativ för att förlänga batteri tiden.

* **Nätverks åtkomst:** Det finns många sätt att ansluta en enhet till en moln tjänst. Ethernet, Wi-Fi och mobil nät och några av de tillgängliga alternativen. Vilken Anslutnings typ du väljer beror på var enheten distribueras och hur den används. Mobil nät kan till exempel vara ett attraktivt alternativ med hög täckning, men för hög trafik enheter kan det vara dyrt. Hardwired Ethernet ger billigare data kostnader men med nack delen mindre portabel.

* **Indata och utdata:** De indata och utdata som är tillgängliga på enheten påverkar direkt enheternas funktions kapacitet. En mikrostyrenhet har normalt många I/O-funktioner som byggts direkt till kretsen och erbjuder ett brett urval sensorer för att ansluta direkt.

## <a name="microcontrollers-vs-microprocessors"></a>Mikrokontrollanter vs mikroprocessorer
IoT-enheter kan delas upp i två breda kategorier, mikrokontrollanter (MCU) och mikroprocessorer (MPUs).

**MCU** är billigare och enklare att hantera än MPUs. En MCU innehåller många av funktionerna, till exempel minne, gränssnitt och I/O i själva kretsen. En MPU ritar den här funktionen från komponenter i stödjande chips. En MCU använder ofta ett återställnings tider (real tids operativ system) eller kör Bare Metal (inget operativ system) och tillhandahåller svar i real tid och mycket deterministiska reaktioner på externa händelser.

**MPUs** kommer vanligt vis att köra ett allmänt operativ system, till exempel Windows, Linux eller MacOSX, som tillhandahåller en icke-deterministisk real tids svar. Det finns vanligt vis ingen garanti för när en uppgift ska slutföras. 

:::image type="content" border="false" source="media/concepts-iot-device-types/mcu-vs-mpu.png" alt-text="MCU vs MPU":::

Nedan visas en tabell med några av de definierade skillnaderna mellan en MCU och ett MPU-baserat system:

||Mikrostyrenhet (MCU)|Mikroprocessorer (MPU)|
|-|-|-|
|**Processor**| Less | Mer |
|**Mycket**| Less | Mer |
|**Utvecklingsverktyget**| Less | Mer |
|**Operativsystem**| Nej eller återställnings tider | Generell användning |
|**Utvecklings problem**| Svårare |  Lätt |
|**Energi förbrukning**| Lägre | Högre |
|**Kostnad**| Lägre | Högre |
|**Deterministisk**| Ja | Inga – med undantag|
|**Enhets storlek**| Små | Större |
