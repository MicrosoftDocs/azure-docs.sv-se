---
title: Azure IoT-enhetshantering med Azure IoT Tools för VSCode
description: Använd Azure IoT Tools för Visual Studio Code för Azure IoT Hub enhetshantering med Direct-metoderna och alternativen för hantering av önskade egenskaper för tvillingen.
author: formulahendry
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/04/2019
ms.author: junhan
ms.openlocfilehash: b48def283ea27fdd0eaa3230a2eb9a8327461ff1
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2021
ms.locfileid: "107567021"
---
# <a name="use-azure-iot-tools-for-visual-studio-code-for-azure-iot-hub-device-management"></a>Använd Azure IoT Tools för Visual Studio Code för Azure IoT Hub enhetshantering

![Diagram från end-to-end](media/iot-hub-get-started-e2e-diagram/2.png)

I den här artikeln får du lära dig hur du använder Azure IoT Tools för Visual Studio Code med olika hanteringsalternativ på utvecklingsdatorn. [Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) är ett användbart Visual Studio Code-tillägg som gör IoT Hub och IoT-programutveckling enklare. Den levereras med hanteringsalternativ som du kan använda för att utföra olika uppgifter.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

| Hanteringsalternativ          | Uppgift                    |
|----------------------------|--------------------------------|
| Direkta metoder             | Gör så att en enhet till exempel startar eller stoppar sändning av meddelanden eller startar om enheten.                                        |
| Läsa enhetstvilling           | Hämta det rapporterade tillståndet för en enhet. Enheten rapporterar till exempel att lysdioden blinkar nu.                                    |
| Uppdatera enhetstvilling         | Placera en enhet i vissa tillstånd, till exempel att ange en lysdiod till grön eller ställa in telemetrisändintervallet till 30 minuter.         |
| Meddelanden från moln till enhet   | Skicka meddelanden till en enhet. Till exempel " Det är mycket troligt regn idag. Glöm inte att ta med ett umbrella."              |

Mer detaljerad förklaring av skillnaderna och vägledningen [](iot-hub-devguide-d2c-guidance.md) för hur du använder dessa alternativ finns i Vägledning för kommunikation från enhet till moln och Vägledning för kommunikation från moln [till enhet.](iot-hub-devguide-c2d-guidance.md)

Enhetstvillingar är JSON-dokument som lagrar information om enhetstillstånd (metadata, konfigurationer och villkor). IoT Hub beständiga en enhetstvilling för varje enhet som ansluter till den. Mer information om enhetstvillingarna finns i [Kom igång med enhetstvillingarna.](iot-hub-node-node-twin-getstarted.md)

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Förutsättningar

* En aktiv Azure-prenumeration.
* En Azure IoT-hubb under din prenumeration.
* [Visual Studio Code](https://code.visualstudio.com/)
* [Azure IoT Tools vs code eller kopiera](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) url:en och klistra in den i ett webbläsarfönster: `vscode:extension/vsciot-vscode.azure-iot-tools` .

## <a name="sign-in-to-access-your-iot-hub"></a>Logga in för att få åtkomst till din IoT-hubb

1. I **Explorer-vyn** för VS Code **expanderar du Azure IoT Hub Enheter** i det nedre vänstra hörnet.

2. Klicka **på IoT Hub** på snabbmenyn.

3. Ett popup-fönster visas i det nedre högra hörnet så att du kan logga in på Azure för första gången.

4. När du har loggat in visas listan med Azure-prenumerationer. Välj sedan Azure-prenumeration och IoT Hub.

5. Enhetslistan visas på fliken **Azure IoT Hub enheter** inom några sekunder.

   > [!Note]
   > Du kan också slutföra konfigurationen genom att välja **Set IoT Hub Connection String** (Ange IoT Hub-anslutningssträng). Ange **anslutningssträngen för iothubowner-principen** för den IoT-hubb som din IoT-enhet ansluter till i popup-fönstret.

## <a name="direct-methods"></a>Direkta metoder

1. Högerklicka på enheten och välj **Invoke Direct Method (Anropa direktmetod).** 

2. Ange metodnamnet och nyttolasten i inmatningsrutan.

3. Resultaten visas i **utdatavyn**  >  **Azure IoT Hub** vyn.

## <a name="read-device-twin"></a>Läsa enhetstvilling

1. Högerklicka på enheten och välj Redigera **enhetstvilling.** 

2. En **azure-iot-device-twin.jsfil** öppnas med innehållet i enhetstvillingen.

## <a name="update-device-twin"></a>Uppdatera enhetstvilling

1. Gör några ändringar i taggar **eller** **properties.desired-fält.**

2. Högerklicka på denazure-iot-device-twin.js **filen.**

3. Välj **Uppdatera enhetstvilling** för att uppdatera enhetstvillingen.

## <a name="send-cloud-to-device-messages"></a>Skicka meddelanden från moln till enhet

Följ dessa steg om du vill skicka ett meddelande från din IoT-hubb till enheten:
 
1. Högerklicka på enheten och välj Skicka **C2D-meddelande till enhet.** 

2. Ange meddelandet i inmatningsrutan.

3. Resultaten visas i **utdatavyn**  >  **Azure IoT Hub** vyn.

## <a name="next-steps"></a>Nästa steg

Du har lärt dig hur du använder Azure IoT Tools för att Visual Studio Code med olika hanteringsalternativ.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
