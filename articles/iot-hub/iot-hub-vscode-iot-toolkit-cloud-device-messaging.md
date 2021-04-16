---
title: Använda Azure IoT Tools vscode för att chefa IT Hub-meddelanden
description: Lär dig hur du använder Azure IoT Tools för Visual Studio Code för att övervaka meddelanden från enheten till molnet och skicka meddelanden från molnet till Azure IoT Hub.
author: formulahendry
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 01/18/2019
ms.author: junhan
ms.custom:
- 'Role: Cloud Development'
ms.openlocfilehash: 1c4a840233e576c528e9c58d57eca0b3d524bf4d
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2021
ms.locfileid: "107566936"
---
# <a name="use-azure-iot-tools-for-visual-studio-code-to-send-and-receive-messages-between-your-device-and-iot-hub"></a>Använd Azure IoT Tools för Visual Studio Code för att skicka och ta emot meddelanden mellan enheten och IoT Hub

![Diagram från end-to-end](./media/iot-hub-vscode-iot-toolkit-cloud-device-messaging/e-to-e-diagram.png)

I den här artikeln får du lära dig hur du använder Azure IoT Tools för Visual Studio Code för att övervaka "enhet till molnet"-meddelanden och för att skicka meddelanden från molnet till enheten. Meddelanden från enhet till moln kan vara sensordata som enheten samlar in och sedan skickar till din IoT-hubb. Meddelanden från moln till enhet kan vara kommandon som din IoT-hubb skickar till enheten för att blinka en led som är ansluten till enheten.

[Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit) är ett användbart Visual Studio Code-tillägg som gör IoT Hub och IoT-programutveckling enklare. Den här artikeln fokuserar på hur du använder Azure IoT Tools för Visual Studio Code för att skicka och ta emot meddelanden mellan din enhet och din IoT-hubb.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

## <a name="prerequisites"></a>Förutsättningar

* En aktiv Azure-prenumeration.

* En Azure IoT-hubb under din prenumeration.

* [Visual Studio Code](https://code.visualstudio.com/)

* [Azure IoT Tools för VS Code eller](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) kopiera och klistra in den här URL:en i ett webbläsarfönster: `vscode:extension/vsciot-vscode.azure-iot-tools`

## <a name="sign-in-to-access-your-iot-hub"></a>Logga in för att få åtkomst till din IoT-hubb

1. I **Utforskaren** i VS Code expanderar **du Azure IoT Hub Enheter** i det nedre vänstra hörnet.

2. Klicka **på Välj IoT Hub** på snabbmenyn.

3. Ett popup-fönster visas i det nedre högra hörnet så att du kan logga in på Azure för första gången.

4. När du har loggat in visas listan med Azure-prenumerationer. Välj sedan Azure-prenumeration och IoT Hub.

5. Enhetslistan visas på fliken **Azure IoT Hub enheter** inom några sekunder.

   > [!Note]
   > Du kan också slutföra konfigurationen genom att välja **Set IoT Hub Connection String** (Ange IoT Hub-anslutningssträng). Ange **anslutningssträngen för iothubowner-principen** för den IoT-hubb som din IoT-enhet ansluter till i popup-fönstret.

## <a name="monitor-device-to-cloud-messages"></a>Övervaka meddelanden från enhet till moln

Följ dessa steg om du vill övervaka meddelanden som skickas från enheten till din IoT-hubb:

1. Högerklicka på enheten och välj **Starta övervakning av inbyggd händelseslutpunkt.**

2. De övervakade meddelandena visas i **utdatavyn**  >  **Azure IoT Hub** vy.

3. Om du vill stoppa övervakningen högerklickar du på **vyn UTDATA** och väljer **Stop Monitoring Built-in Event Endpoint (Stoppa övervakning av inbyggd händelseslutpunkt).**

## <a name="send-cloud-to-device-messages"></a>Skicka meddelanden från moln till enhet

Följ dessa steg om du vill skicka ett meddelande från din IoT Hub till enheten:

1. Högerklicka på enheten och välj Skicka **C2D-meddelande till enhet.**

2. Ange meddelandet i inmatningsrutan.

3. Resultaten visas i **utdatavyn**  >  **Azure IoT Hub** vyn.

## <a name="next-steps"></a>Nästa steg

Du har lärt dig hur du övervakar "enhet till molnet"-meddelanden och skickar meddelanden från moln till enhet mellan din IoT-enhet och Azure IoT Hub.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]