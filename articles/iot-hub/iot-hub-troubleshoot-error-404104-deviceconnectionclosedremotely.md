---
title: Fel sökning av Azure IoT Hub-fel 404104 DeviceConnectionClosedRemotely
description: Förstå hur du åtgärdar fel 404104 DeviceConnectionClosedRemotely
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.custom: mqtt
ms.openlocfilehash: 673a76417739fa59a91979cca7c6807a584868f0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "92538263"
---
# <a name="404104-deviceconnectionclosedremotely"></a>404104 DeviceConnectionClosedRemotely

I den här artikeln beskrivs orsaker och lösningar för **404104 DeviceConnectionClosedRemotely** -fel.

## <a name="symptoms"></a>Symtom

### <a name="symptom-1"></a>Symptom 1

Enheterna kopplar från med jämna mellanrum (till exempel en gång i 65 minuter) och du ser **404104 DeviceConnectionClosedRemotely** i IoT Hub resurs loggar. Ibland kan du också se **401003 IoTHubUnauthorized** och en lyckad enhets anslutnings händelse som är mindre än en minut senare.

### <a name="symptom-2"></a>Symptom 2

Enheterna kopplar bort slumpmässigt och du ser **404104 DeviceConnectionClosedRemotely** i IoT Hub resurs loggar.

### <a name="symptom-3"></a>Symptom 3

Många enheter kopplas från samtidigt visas en DIP i [connectedDeviceCount-måttet (Connected Devices)](monitor-iot-hub-reference.md)och det finns fler **404104-DeviceConnectionClosedRemotely** och [500Xxx internt fel](iot-hub-troubleshoot-error-500xxx-internal-errors.md) i Azure Monitor loggar än vanligt.

## <a name="causes"></a>Orsaker

### <a name="cause-1"></a>Orsak 1

[SAS-token som används för att ansluta till IoT Hub upphör att](iot-hub-devguide-security.md#security-tokens) gälla, vilket gör att IoT Hub kopplar från enheten. Anslutningen etableras igen när token uppdateras av enheten. [SAS-token går till exempel ut varje timme som standard för C SDK](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/connection_and_messaging_reliability.md#connection-authentication), vilket kan leda till vanliga från kopplingar.

Mer information finns i [401003 IoTHubUnauthorized-orsak](iot-hub-troubleshoot-error-401003-iothubunauthorized.md#cause-1).

### <a name="cause-2"></a>Orsak 2

Några möjligheter är:

- Enheten förlorade en underliggande nätverks anslutning längre än [MQTT Keep-Alive](iot-hub-mqtt-support.md#default-keep-alive-timeout), vilket resulterade i en tids gräns för fjärraktivitet. Inställningen MQTT Keep-Alive kan vara olika per enhet.

- Enheten skickade en TCP/IP-nivå återställd, men skickade inte en program nivå `MQTT DISCONNECT` . I själva verket stängde enheten den underliggande socket-anslutningen. Ibland orsakas problemet av buggar i äldre versioner av Azure IoT SDK.

- Enhets sidans program kraschade.

### <a name="cause-3"></a>Orsak 3

IoT Hub kan drabbas av ett tillfälligt problem. Se [IoT Hub interna Server fel orsaken](iot-hub-troubleshoot-error-500xxx-internal-errors.md#cause).

## <a name="solutions"></a>Lösningar

### <a name="solution-1"></a>Lösning 1

Se [401003 IoTHubUnauthorized lösning 1](iot-hub-troubleshoot-error-401003-iothubunauthorized.md#solution-1)

### <a name="solution-2"></a>Lösning 2

- Kontrol lera att enheten har en lämplig anslutning till IoT Hub genom att [testa anslutningen](tutorial-connectivity.md). Om nätverket är otillförlitligt eller tillfälligt, rekommenderar vi inte att du ökar Keep-Alive-värdet eftersom det kan resultera i en identifiering (via Azure Monitor aviseringar, till exempel) som tar längre tid. 

- Använd de senaste versionerna av [IoT SDK](iot-hub-devguide-sdks.md): er.

### <a name="solution-3"></a>Lösning 3

Se [lösningar för att IoT Hub interna Server fel](iot-hub-troubleshoot-error-500xxx-internal-errors.md#solution).

## <a name="next-steps"></a>Nästa steg

Vi rekommenderar att du använder Azure IoT-drivrutiner för att hantera anslutningar på ett tillförlitligt sätt. Mer information finns i [hantera anslutningar och Reliable Messaging med hjälp av Azure IoT Hub-enhets-SDK](iot-hub-reliability-features-in-sdks.md) : er