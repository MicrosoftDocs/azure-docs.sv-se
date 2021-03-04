---
title: Data blad för Azure percept vision
description: Ta en titt på data bladet för Azure percept vision för detaljerade enhets specifikationer
author: elqu20
ms.author: v-elqu
ms.service: azure-percept
ms.topic: reference
ms.date: 02/16/2021
ms.openlocfilehash: 54dae3341910bf863de7e2a2cef832ae670ca09a
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/04/2021
ms.locfileid: "102097575"
---
# <a name="azure-percept-vision-datasheet"></a>Data blad för Azure percept vision

Specifikationerna nedan gäller för den Azure percept vision-enhet som ingår i [Azure PERCEPT DK](./azure-percept-dk-datasheet.md).

|Produkt specifikation           |Värde     |
|--------------------------------|---------------------|
|Mål branscher               |Tillverkningsindustrin <br> Smarta byggnader <br> Automatisk <br> Retail |
|Hjälte scenarier                  |Konsument-analys <br> Tillgänglighet på hylla <br> Minska minskning <br> Arbets plats övervakning|
|Dimensioner                      |42mm x 42mm x 40mm (Azure percept vision som en sammansättning med bostad) <br> 42mm x 42mm x 6mm (vision som chip)|
|Hanterings kontroll plan        |Uppdatering av Azure-enhet (ADU)          |
|Program vara och tjänster som stöds |[Azure IoT Hub](https://azure.microsoft.com/services/iot-hub/) <br> [Azure IoT Edge](https://azure.microsoft.com/services/iot-edge/) <br> [Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/) <br> [ONNX Runtime](https://www.onnxruntime.ai/) <br> [Openprodukt](https://docs.openvinotoolkit.org/latest/index.html) <br> Uppdatering av Azure-enhet |
|AI-acceleration                 |Intel Movidius myriaden X (MA2085) vision Processing Unit () med Intel Camera Internet-integrering, 0,7 överordnad |
|Sensorer och visuella indikatorer   |Sony IMX219-kamera sensor med 6P-lins<br>Lösning: 8MP vid 30FPS, avstånd: 50cm-oändlighet<br>FoV: 120 grader diagonal, färg: brett dynamiskt intervall, fast fokus rullande slutarhastighet|
|Kamera support                  |RGB (för närvarande) och IR (i framtiden) <br> 2 kameror kan köras samtidigt |
|Säkerhets Crypto-Controller      |ST-Micro STM32L462CE      |
|Komponenten versions hantering/ID       |64 kB EEPROM |
|Minne                          |LPDDR4 2 GB     |
|Ström                           |3,5 W     |
|Portar                           |1x USB 3,0-typ C <br> 2x MIPI 4 Lane (upp till 1,5 Gbit/s per Lane)     |
|Kontroll gränssnitt              |2x I2C <br> 2x SPI <br> 6x PWM (GPIOs: 2x klockning, 2x Frame Sync, 2x oanvänd) <br> 2x-reserv GPIO |
|Certifiering                   |FCC <br> KONCERNINTERN <br> RoHS <br> ÅT <br> UL   |
|Drift temperatur           |0 till 27 grader C (Azure percept vision som sammansättning med bostad) <br> – 10 till 70 grader C (vision som chip) |
|Touch-temperatur               |<= 48 grader C |
|Relativ fuktighet               |8% till 90%    |
