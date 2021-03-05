---
title: Så här flyttar du en enhet till Azure IoT Central från IoT Hub
description: Så här flyttar du enheten till Azure IoT Central från IoT Hub
author: TheRealJasonAndrew
ms.author: v-anjaso
ms.date: 02/20/2021
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.openlocfilehash: 543c3f1c72857098540cc2a77e8a0093b907b799
ms.sourcegitcommit: f7eda3db606407f94c6dc6c3316e0651ee5ca37c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/05/2021
ms.locfileid: "102210843"
---
# <a name="how-to-transfer-a-device-to-azure-iot-central-from-iot-hub"></a>Överföra en enhet till Azure IoT Central från IoT Hub

*Den här artikeln gäller operatörer och enhets utvecklare.*  

Den här artikeln beskriver hur du överför en enhet till ett Azure IoT Central-program från en IoT Hub. 

En enhet ansluter först till en DPS-slutpunkt för att hämta den information som krävs för att ansluta till ditt program. Internt använder ditt IoT Central-program en IoT-hubb för att hantera enhets anslutningar.  

En enhet kan anslutas till en IoT-hubb direkt med hjälp av en anslutnings sträng eller med hjälp av DPS. [Azure IoT Hub Device Provisioning service (DPS)](../../iot-dps/about-iot-dps.md) är vägen för IoT Central.

## <a name="to-move-the-device-to-azure-iot-central"></a>Så här flyttar du enheten till Azure IoT Central

För att ansluta en enhet till IoT Central från IoT Hub måste en enhet uppdateras med:

* [Omfångs-ID](../../iot-dps/concepts-service.md) för IoT Central programmet.
* En nyckel härledd från antingen [gruppens SAS](concepts-get-connected.md) -nyckel eller [X. 509-certifikatet](../../iot-hub/iot-hub-x509ca-overview.md)

Om du vill interagera med IoT Central måste det finnas en enhets mall som modellerar de egenskaper/telemetri/kommandon som enheten implementerar. Mer information finns i [Anslut till IoT Central](concepts-get-connected.md) och [Vad är enhets mallar?](concepts-device-templates.md)

## <a name="next-steps"></a>Nästa steg

Om du är enhets utvecklare är några förslag på nästa steg att:

- Granska en exempel kod som visar hur du använder SAS-token i [Självstudier: skapa och ansluta ett klient program till ditt Azure IoT Central-program](tutorial-connect-device.md)
- Lär dig hur du [ansluter enheter med X. 509-certifikat med hjälp av Node.js Device SDK för IoT Central program](how-to-connect-devices-x509.md)
- Lär dig hur du [övervakar enhets anslutningar med hjälp av Azure CLI](./howto-monitor-devices-azure-cli.md)
- Lär dig hur du [definierar en ny IoT-enhets typ i ditt Azure IoT Central-program](./howto-set-up-template.md)
- Läs om [Azure IoT Edge enheter och Azure IoT Central](./concepts-iot-edge.md)