---
title: Förstå enhets uppdatering för Azure IoT Hub-resurser | Microsoft Docs
description: Förstå enhets uppdatering för Azure IoT Hub-resurser
author: vimeht
ms.author: vimeht
ms.date: 2/11/2021
ms.topic: conceptual
ms.service: iot-hub-device-update
ms.openlocfilehash: ba43889b885252f68bb3b4b158b5626411aac3d5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "101664079"
---
# <a name="device-update-resources"></a>Enhets uppdaterings resurser

Om du vill använda enhets uppdatering för IoT Hub måste du skapa ett konto och en instans resurs för enhets uppdatering. 

## <a name="device-update-account"></a>Enhets uppdaterings konto

Ett enhets uppdaterings konto är en resurs som skapas i din Azure-prenumeration. På konto nivå för enhets uppdatering kan du välja den region där ditt enhets uppdaterings konto kommer att skapas. Du kan också ange behörigheter för att auktorisera användare som ska ha åtkomst till enhets uppdatering.


## <a name="device-update-instance"></a>Enhets uppdaterings instans
När ett konto har skapats måste du skapa en enhets uppdaterings instans. En instans är en logisk behållare som innehåller uppdateringar och distributioner som är associerade med en angiven IoT-hubb. Enhets uppdatering använder IoT Hub som en enhets katalog och en kommunikations kanal med enheter. 

Under den offentliga för hands versionen kan två enhets uppdaterings konton skapas per prenumeration. Dessutom kan två enhets uppdaterings instanser skapas per konto.

## <a name="configuring-device-update-linked-iot-hub"></a>Konfigurerar länkade IoT Hub enhets uppdateringar 

För att enhets uppdateringen ska kunna ta emot ändrings meddelanden från IoT Hub integreras enhets uppdateringen med den inbyggda Händelsehubben. Om du klickar på knappen "Konfigurera IoT Hub" i din instans konfigureras de nödvändiga meddelande vägarna och den åtkomst princip som krävs för att kommunicera med IoT-enheter. 

Följande meddelande vägar har kon figurer ATS för enhets uppdatering:

|   Flödes namn    | Routnings fråga  | Beskrivning  |
| :--------- | :---- |:---- |
|  DeviceUpdate.DigitalTwinChanges | true |Lyssnar efter digitala dubbla ändringar-händelser  |
|  DeviceUpdate.DeviceLifeCycle | opType = ' deleteDeviceIdentity '  | Lyssnar efter enheter som har tagits bort |
|  DeviceUpdate.TelemetryModelInformation | iothub-Interface-ID = "urn: azureiot: ModelDiscovery: ModelInformation: 1 | Lyssnar efter nya enhets typer |
|  DeviceUpdate.DeviceTwinEvents| (opType = ' updateTwin' eller opType = ' replaceTwin') OCH IS_DEFINED ($body. Tagss. ADUGroup) | Lyssnar efter nya uppdaterings grupper för enheter |

## <a name="next-steps"></a>Nästa steg

[Skapa resurser för enhets uppdatering](./create-device-update-account.md)
