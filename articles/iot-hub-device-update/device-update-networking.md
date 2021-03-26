---
title: Enhets uppdatering för IoT Hub nätverks krav | Microsoft Docs
description: Enhets uppdatering för IoT Hub använder flera olika nätverks portar för olika ändamål.
author: philmea
ms.author: philmea
ms.date: 1/11/2021
ms.topic: conceptual
ms.service: iot-hub-device-update
ms.openlocfilehash: 0512308fbaa0a725c6ecca573c70c90d8c04e247
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2021
ms.locfileid: "105558388"
---
# <a name="ports-used-with-device-update-for-iot-hub"></a>Portar som används med enhets uppdatering för IoT Hub
ADU använder olika nätverks portar för olika ändamål.

## <a name="default-ports"></a>Standard portar

Syfte|Portnummer |
---|---
Ladda ned från nätverk/CDN  | 80 (HTTP-protokoll)
Ladda ned från MCC/CDN | 80 (HTTP-protokoll)
ADU-agentens anslutning till Azure IoT Hub  | 8883 (MQTT-protokoll)

## <a name="use-azure-iot-hub-supported-protocols"></a>Använd Azure IoT Hub-protokoll som stöds
ADU-agenten kan ändras för att använda något av de Azure IoT Hub-protokoll som stöds.

[Läs mer](../iot-hub/iot-hub-devguide-protocols.md) om den aktuella listan över protokoll som stöds.
