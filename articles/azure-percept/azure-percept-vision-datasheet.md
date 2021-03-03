---
title: Data blad för Azure percept vision
description: Ta en titt på data bladet för Azure percept vision för detaljerade enhets specifikationer
author: elqu20
ms.author: v-elqu
ms.service: azure-percept
ms.topic: reference
ms.date: 02/16/2021
ms.openlocfilehash: 8814192274a81938d53ffc68c02dfaa9ac1da8ad
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/02/2021
ms.locfileid: "101663804"
---
# <a name="azure-percept-vision-datasheet"></a>Data blad för Azure percept vision

Specifikationerna nedan gäller för den Azure percept vision-enhet som ingår i [Azure PERCEPT DK](./azure-percept-dk-datasheet.md).

|Produkt specifikation           |Värde     |
|--------------------------------|---------------------|
|Mål branscher               |Tillverkningsindustrin <br> Smarta byggnader <br> Automatisk <br> Retail |
|Hjälte scenarier                  |Konsument-analys <br> Tillgänglighet på hylla <br> Minska minskning <br> Säkerhet/övervakning <br> Arbets plats säkerhet|
|Dimensioner                      |42mm x 42mm x 40mm (Azure percept vision som en sammansättning med bostad) <br> 42mm x 42mm x 6mm (vision som chip)|
|Hanterings kontroll plan        |Uppdatering av Azure-enhet (ADU)          |
|Program vara och tjänster som stöds |[Azure IoT Hub](https://azure.microsoft.com/services/iot-hub/) <br> [Azure IoT Edge](https://azure.microsoft.com/services/iot-edge/) <br> [Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/) <br> [ONNX Runtime](https://www.onnxruntime.ai/) <br> [Openprodukt](https://docs.openvinotoolkit.org/latest/index.html) <br> Uppdatering av Azure-enhet |
|AI-acceleration                 |Intel Movidius myriaden X (MA2085) vision Processing Unit () med Intel Camera Internet-integrering, 0,7 överordnad |
|Sensorer och visuella indikatorer   |Omni vision 5670-kamera <br> GSO GS8882AA Lens (lösning: 5MP på 30FPS, avstånd: 50cm-oändlighet, FoV: 120 grader, färg: brett dynamiskt intervall, rullande slutarhastighet med fast storlek)          |
|Kamera support                  |RGB-, IR-och djup-kameror <br> 2 kameror kan köras samtidigt |
|Säkerhets Crypto-Controller      |ST-Micro STM32L462CE      |
|Komponenten versions hantering/ID       |64 kB EEPROM |
|Minne                          |LPDDR4 2 GB     |
|Ström                           |3,5 W     |
|Portar                           |1x USB 3,0-typ C <br> 2x MIPI 4 Lane (upp till 1,5 Gbit/s per Lane)     |
|Kontroll gränssnitt              |2x I2C <br> 2x SPI <br> 6x PWM (GPIOs: 2x klockning, 2x Frame Sync, 2x oanvänd) <br> 2x-reserv GPIO |
|Certifiering                   |CE <br> FCC      |
|Drift temperatur           |0 till 27 grader C (Azure percept vision som sammansättning med bostad) <br> – 10 till 70 grader C (vision som chip) |
|Touch-temperatur               |<= 48 grader C |
|Relativ fuktighet               |8% till 90%    |