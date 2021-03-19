---
title: ta med fil
description: ta med fil
services: iot-hub
author: robinsh
ms.service: iot-hub
ms.topic: include
ms.date: 11/06/2018
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: 7b022f71e197c5695876f2049ee376c3616afc6d
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "70048986"
---
<!-- put the ## header in the file that includes this file -->

I det här avsnittet skapar du en enhets identitet i identitets registret i IoT-hubben. En enhet kan inte ansluta till en hubb om den inte har en post i identitets registret. Mer information finns i [utvecklarhandboken för IoT Hub](../articles/iot-hub/iot-hub-devguide-identity-registry.md#identity-registry-operations).

1. I navigerings menyn i IoT Hub öppnar du **IoT-enheter** och väljer sedan **ny** för att lägga till en enhet i IoT Hub.

    ![Skapa enhets identitet i portalen](./media/iot-hub-include-create-device/create-identity-portal-vs2019.png)

1. I **skapa en enhet** anger du ett namn för den nya enheten, till exempel **myDeviceId**, och väljer **Spara**. Den här åtgärden skapar en enhets identitet för din IoT-hubb.

   ![Lägg till en ny enhet](./media/iot-hub-include-create-device/create-a-device-vs2019.png)

   [!INCLUDE [iot-hub-pii-note-naming-device](iot-hub-pii-note-naming-device.md)]

1. När enheten har skapats öppnar du enheten från listan i fönsterrutan **IoT-enheter**. Kopiera den **primära anslutnings strängen** som ska användas senare.

    ![Enhets anslutnings sträng](./media/iot-hub-include-create-device/device-details-vs2019.png)

> [!NOTE]
> IoT Hub-identitetsregistret lagrar bara enhetsidentiteter för att skydda åtkomsten till IoT Hub. Registret lagrar enhets-ID:n och enhetsnycklar som ska användas som säkerhetsreferenser och en aktiverad/inaktiverad-flagga som du kan använda för att inaktivera åtkomst för en enskild enhet. Om ditt program behöver lagra andra enhetsspecifika metadata bör det använda ett programspecifikt datalager. Mer information finns i [utvecklarhandboken för IoT Hub](../articles/iot-hub/iot-hub-devguide-identity-registry.md).
