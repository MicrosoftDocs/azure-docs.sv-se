---
title: Så här flyttar du en enhet Azure IoT Central från IoT Hub
description: Så här flyttar du en enhet Azure IoT Central från IoT Hub
author: philmea
ms.author: philmea
ms.date: 02/20/2021
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.openlocfilehash: 79aead5b374714e7856897a9b85349198341cb3d
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2021
ms.locfileid: "107872739"
---
# <a name="how-to-transfer-a-device-to-azure-iot-central-from-iot-hub"></a>Överföra en enhet till Azure IoT Central från IoT Hub

*Den här artikeln gäller för operatörer och enhetsutvecklare.*  

Den här artikeln beskriver hur du överför en enhet till ett Azure IoT Central-program från en IoT Hub. 

En enhet ansluter först till en DPS-slutpunkt för att hämta den information som behövs för att ansluta till ditt program. Internt använder ditt IoT Central en IoT-hubb för att hantera enhetsanslutningar.  

En enhet kan anslutas till en IoT-hubb direkt med hjälp av en anslutningssträng eller dps. [Azure IoT Hub Device Provisioning Service (DPS)](../../iot-dps/about-iot-dps.md) är vägen för IoT Central.

## <a name="to-move-the-device-to-azure-iot-central"></a>Flytta enheten till Azure IoT Central

Om du vill ansluta en IoT Central från IoT Hub måste en enhet uppdateras med:

* [Omfångs-ID](../../iot-dps/concepts-service.md) för IoT Central program.
* En nyckel som härletts [antingen från gruppens SAS-nyckel](concepts-get-connected.md) [eller X.509-certifikatet](../../iot-hub/iot-hub-x509ca-overview.md)

Om du vill IoT Central måste det finnas en enhetsmall som modellerar de egenskaper/telemetri/kommandon som enheten implementerar. Mer information finns i [Anslut till IoT Central och](concepts-get-connected.md) Vad är [enhetsmallar?](concepts-device-templates.md)

## <a name="next-steps"></a>Nästa steg

Om du är enhetsutvecklare föreslår vi följande steg:

- Granska lite exempelkod som visar hur du använder SAS-token i [Självstudie: Skapa och ansluta ett klientprogram till ditt Azure IoT Central program](tutorial-connect-device.md)
- Lär dig hur [du ansluter enheter med X.509-certifikat med Node.js-enhets-SDK för IoT Central Application](how-to-connect-devices-x509.md)
- Lär dig hur du [övervakar enhetsanslutningar med Hjälp av Azure CLI](./howto-monitor-devices-azure-cli.md)
- Lär dig hur [du definierar en ny IoT-enhetstyp i ditt Azure IoT Central program](./howto-set-up-template.md)
- Läs om [Azure IoT Edge enheter och Azure IoT Central](./concepts-iot-edge.md)