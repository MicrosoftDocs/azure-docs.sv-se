---
title: Skapa en Azure-IoT Hub med hjälp av Azure IoT Tools för VS Code | Microsoft Docs
description: Lär dig hur du använder Azure IoT-verktyg för Visual Studio Code för att skapa en Azure IoT-hubb i en resurs grupp.
author: formulahendry
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/04/2019
ms.author: junhan
ms.openlocfilehash: 668087ae596688e86b7b84a16bc5c0fd0f9fcef6
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "75912258"
---
# <a name="create-an-iot-hub-using-the-azure-iot-tools-for-visual-studio-code"></a>Skapa en IoT-hubb med Azure IoT-verktyg för Visual Studio Code

[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

Den här artikeln visar hur du använder [Azure IoT-verktyg för Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) för att skapa en Azure IoT-hubb. 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

För att slutföra den här artikeln behöver du följande:

- En Azure-prenumeration. Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

- [Visual Studio Code](https://code.visualstudio.com/)

- [Azure IoT-verktyg](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) för Visual Studio Code.

## <a name="create-an-iot-hub"></a>Skapa en IoT Hub

1. Öppna vyn **Utforskare** i Visual Studio Code.

2. Längst ned i Utforskaren expanderar du avsnittet **Azure IoT Hub-enheter** . 

   ![Expandera Azure IoT Hub-enheter](./media/iot-hub-create-use-iot-toolkit/azure-iot-hub-devices.png)

3. Klicka på avsnittet **...** i avsnittet om **Azure IoT Hub-enheter** . Om du inte ser ellipsen hovrar du över sidhuvudet. 

4. Välj **skapa IoT Hub**.

5. Ett popup-fönster visas i det nedre högra hörnet så att du kan logga in på Azure för första gången.

6. Välj Azure-prenumeration. 

7. Välj resurs grupp.

8. Välj plats.

9. Välj pris nivå.

10. Ange ett globalt unikt namn för din IoT Hub.

11. Vänta några minuter tills IoT Hub har skapats.

## <a name="next-steps"></a>Nästa steg

Nu har du distribuerat en IoT-hubb med hjälp av Azure IoT-verktyg för Visual Studio Code. Om du vill utforska ytterligare kan du läsa följande artiklar:

* [Använd Azure IoT-verktyg för Visual Studio Code för att skicka och ta emot meddelanden mellan enheten och en IoT Hub](iot-hub-vscode-iot-toolkit-cloud-device-messaging.md).

* [Använd Azure IoT-verktyg för Visual Studio Code för Azure IoT Hub Device Management](iot-hub-device-management-iot-toolkit.md)

* [Se sidan Azure IoT Hub for vs Code wiki](https://github.com/microsoft/vscode-azure-iot-toolkit/wiki).
