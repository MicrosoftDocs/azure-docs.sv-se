---
title: Alternativ för Azure IoT Hub från enhet till moln | Microsoft Docs
description: Guide för utvecklare – vägledning om när du ska använda enhets-till-moln-meddelanden, rapporterade egenskaper eller fil uppladdning för kommunikation från moln till enhet.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/29/2018
ms.custom:
- amqp
- mqtt
- 'Role: Cloud Development'
- 'Role: IoT Device'
ms.openlocfilehash: dd4f4ad7f84ebc1f5e254843e7afa4aa0f3f224f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "87319141"
---
# <a name="device-to-cloud-communications-guidance"></a>Vägledning för kommunikation från enhet till moln

När du skickar information från Device-appen till lösningens Server del, visar IoT Hub tre alternativ:

* [Enhets-till-moln-meddelanden](iot-hub-devguide-messages-d2c.md) för Time Series-telemetri och-aviseringar.

* [Enhetens dubbla rapporter egenskaper](iot-hub-devguide-device-twins.md) för rapportering av enhets tillståndsinformation, till exempel tillgängliga funktioner, villkor eller tillstånd för långvariga arbets flöden. Till exempel konfigurations-och program uppdateringar.

* [Fil överföringar](iot-hub-devguide-file-upload.md) för mediefiler och stora telemetri-batchar laddas upp av tillfälligt anslutna enheter eller komprimerade för att spara bandbredd.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

Här är en detaljerad jämförelse av de olika alternativen för kommunikation mellan enheter och moln.

| Faktor | Meddelanden från enheten till molnet | Enhetens dubbla egenskaper för rapporter | Fil överföringar |
| ---- | ------- | ---------- | ---- |
| Scenario | Tids serier och aviseringar för telemetri. Till exempel är 256 KB-sensor data-batchar skickade var 5: e minut. | Tillgängliga funktioner och villkor. Till exempel det aktuella anslutnings läget för enhet, till exempel mobil nät eller WiFi. Synkronisering av tids krävande arbets flöden, till exempel konfigurations-och program uppdateringar. | Mediafiler. Stora (vanligt vis komprimerade) telemetri-batchar. |
| Lagring och hämtning | Lagras tillfälligt med IoT Hub, upp till 7 dagar. Endast sekventiell läsning. | Lagras av IoT Hub i enheten. Hämtnings bara med [IoT Hub frågespråk](iot-hub-devguide-query-language.md). | Lagrat i Azure Storage konto för användare. |
| Storlek | Upp till 256 KB-meddelanden. | Maximalt antal rapporterade egenskaper är 32 KB. | Maximal fil storlek som stöds av Azure Blob Storage. |
| Frekvens | Hög. Mer information finns i [IoT Hub gränser](iot-hub-devguide-quotas-throttling.md). | Medium. Mer information finns i [IoT Hub gränser](iot-hub-devguide-quotas-throttling.md). | Låg. Mer information finns i [IoT Hub gränser](iot-hub-devguide-quotas-throttling.md). |
| Protokoll | Tillgängligt på alla protokoll. | Tillgängligt med MQTT eller AMQP. | Tillgängligt när du använder valfritt protokoll, men kräver HTTPS på enheten. |

Ett program kan behöva skicka information både som en tids serie för telemetri eller en avisering och göra den tillgänglig på enheten. I det här scenariot kan du välja något av följande alternativ:

* Enhets appen skickar ett meddelande från enhet till molnet och rapporterar en egenskaps ändring.
* Server delen i lösningen kan lagra informationen i enhetens dubbla Taggar när den tar emot meddelandet.

Eftersom meddelanden från enhet till molnet möjliggör mycket högre data flöde än enhets dubbla uppdateringar, är det ibland önskvärt att undvika att uppdatera enheten för varje meddelande från enhet till moln.
