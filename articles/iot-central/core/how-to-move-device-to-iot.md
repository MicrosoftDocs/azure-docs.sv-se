---
title: Så här flyttar du en enhet till Azure IoT Central från IoT Hub
description: Så här flyttar du enheten till Azure IoT Central från IoT Hub
author: TheRealJasonAndrew
ms.author: v-anjaso
ms.date: 12/20/2020
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.openlocfilehash: 7898f842529b81b80febff444c97b199fbebba3c
ms.sourcegitcommit: 431bf5709b433bb12ab1f2e591f1f61f6d87f66c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/12/2021
ms.locfileid: "98146473"
---
# <a name="how-to-transfer-a-device-to-azure-iot-central-from-iot-hub"></a>Överföra en enhet till Azure IoT Central från IoT Hub

*Den här artikeln gäller operatörer och enhets utvecklare.*  

Den här artikeln beskriver hur du överför en enhet till ett Azure IoT Central-program från en IoT Hub. 

En enhet ansluter först till en DPS-slutpunkt för att hämta den information som krävs för att ansluta till ditt program. Internt använder ditt IoT Central-program en IoT-hubb för att hantera enhets anslutningar.  

En enhet kan anslutas till en IoT-hubb direkt med hjälp av en anslutnings sträng eller med hjälp av DPS. [Azure IoT Hub Device Provisioning service (DPS)](../../iot-dps/about-iot-dps.md) är vägen för IoT Central.

## <a name="to-move-the-device"></a>Flytta enheten

För att ansluta en enhet till IoT Central från IOT Hub måste en enhet uppdateras med:

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