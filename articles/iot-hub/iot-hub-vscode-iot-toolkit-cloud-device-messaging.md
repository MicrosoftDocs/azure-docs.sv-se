---
title: Använda Azure IoT-verktyg för VSCode till chef för IT-hubb
description: Lär dig hur du använder Azure IoT-verktyg för Visual Studio Code för att övervaka enheten till moln meddelanden och skicka molnet till enhets meddelanden i Azure IoT Hub.
author: formulahendry
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 01/18/2019
ms.author: junhan
ms.custom:
- 'Role: Cloud Development'
ms.openlocfilehash: b9d8f0c2a573ba1749b414a389cd7181ddd5f95a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "87321691"
---
# <a name="use-azure-iot-tools-for-visual-studio-code-to-send-and-receive-messages-between-your-device-and-iot-hub"></a>Använd Azure IoT-verktyg för Visual Studio Code för att skicka och ta emot meddelanden mellan enheten och IoT Hub

![Diagram från slut punkt till slut punkt](./media/iot-hub-vscode-iot-toolkit-cloud-device-messaging/e-to-e-diagram.png)

[Azure IoT-verktyg](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit) är ett användbart kod tillägg för Visual Studio som gör det enklare att utveckla IoT Hub-hantering och IoT-program. Den här artikeln fokuserar på hur du använder Azure IoT-verktyg för Visual Studio Code för att skicka och ta emot meddelanden mellan din enhet och IoT Hub.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

## <a name="what-you-will-learn"></a>Det här får du lära dig

Du lär dig hur du använder Azure IoT-verktyg för Visual Studio Code för att övervaka meddelanden från enheten till molnet och skicka meddelanden från molnet till enheten. Meddelanden från enheten till molnet kan vara sensor data som samlas in av enheten och sedan skickas till IoT-hubben. Meddelanden från moln till enhet kan vara kommandon som din IoT Hub skickar till enheten för att blinka med en indikator som är ansluten till din enhet.

## <a name="what-you-will-do"></a>Vad du ska göra

* Använd Azure IoT-verktyg för Visual Studio Code för att övervaka meddelanden från enheten till molnet.

* Använd Azure IoT-verktyg för Visual Studio Code för att skicka meddelanden från molnet till enheten.

## <a name="what-you-need"></a>Detta behöver du

* En aktiv Azure-prenumeration.

* En Azure IoT-hubb under din prenumeration.

* [Visual Studio Code](https://code.visualstudio.com/)

* [Azure IoT-verktyg för vs Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) eller kopiera och klistra in denna URL i ett webbläsarfönster: `vscode:extension/vsciot-vscode.azure-iot-tools`

## <a name="sign-in-to-access-your-iot-hub"></a>Logga in för att få åtkomst till din IoT Hub

1. I **trädvyn med** vs Code expanderar du avsnittet **Azure IoT Hub-enheter** i det nedre vänstra hörnet.

2. Klicka på **välj IoT Hub** i snabb menyn.

3. Ett popup-fönster visas i det nedre högra hörnet så att du kan logga in på Azure för första gången.

4. När du har loggat in visas din Azure-prenumeration och välj sedan Azure-prenumeration och IoT Hub.

5. Enhets listan visas på fliken **Azure IoT Hub Devices** på några sekunder.

   > [!Note]
   > Du kan också slutföra konfigurationen genom att välja **Set IoT Hub Connection String** (Ange IoT Hub-anslutningssträng). Ange anslutnings strängen för **iothubowner** -principen för den IoT-hubb som din IoT-enhet ansluter till i popup-fönstret.

## <a name="monitor-device-to-cloud-messages"></a>Övervaka meddelanden från enheten till molnet

Följ dessa steg om du vill övervaka meddelanden som skickas från enheten till din IoT Hub:

1. Högerklicka på enheten och välj **starta övervakning inbyggd händelse slut punkt**.

2. De övervakade meddelandena visas i   >  vyn **Azure IoT Hub** utdata.

3. Om du vill stoppa övervakningen högerklickar du på vyn **utdata** och väljer **stoppa övervakning inbyggd händelse slut punkt**.

## <a name="send-cloud-to-device-messages"></a>Skicka meddelanden från moln till enhet

Följ dessa steg om du vill skicka ett meddelande från din IoT-hubb till din enhet:

1. Högerklicka på enheten och välj **Skicka C2D-meddelande till enhet**.

2. Ange meddelandet i rutan indatamängd.

3. Resultaten visas i vyn **utdata** i  >  **Azure IoT Hub** .

## <a name="next-steps"></a>Nästa steg

Du har lärt dig hur du övervakar meddelanden från enheten till molnet och skickar meddelanden från molnet till enheten mellan din IoT-enhet och Azure IoT Hub.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]