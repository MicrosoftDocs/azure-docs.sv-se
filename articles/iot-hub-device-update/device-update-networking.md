---
title: Enhets uppdatering för IoT Hub nätverks krav | Microsoft Docs
description: Enhets uppdatering för IoT Hub använder flera olika nätverks portar för olika ändamål.
author: philmea
ms.author: philmea
ms.date: 1/11/2021
ms.topic: conceptual
ms.service: iot-hub-device-update
ms.openlocfilehash: e72ff144a56f44ccaa695b7dab328e42052fce39
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "101680090"
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

[Läs mer](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-protocols#:~:text=Table%202%20%20%20,%201%20more%20rows) om den aktuella listan över protokoll som stöds.
