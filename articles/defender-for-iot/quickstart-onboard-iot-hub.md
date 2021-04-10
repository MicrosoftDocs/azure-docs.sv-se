---
title: 'Snabb start: publicera Defender för IoT till en agent-baserad lösning'
description: I den här snabb starten får du lära dig hur du integrerar och aktiverar tjänsten Defender för IoT-säkerhet i Azure-IoT Hub.
ms.topic: quickstart
ms.date: 1/20/2021
ms.openlocfilehash: b795773ae0eb667cf7f53f4209b6476ba937b17f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "104780964"
---
# <a name="quickstart-onboard-defender-for-iot-to-an-agent-based-solution"></a>Snabb start: publicera Defender för IoT till en agent-baserad lösning

I den här artikeln förklaras hur du aktiverar tjänsten Defender för IoT på din befintliga IoT Hub. Om du för närvarande inte har en IoT Hub kan du läsa [skapa ett IoT Hub med hjälp av Azure Portal](../iot-hub/iot-hub-create-through-portal.md) för att komma igång.

Du kan hantera din IoT-säkerhet via IoT Hub i Defender för IoT. Hanterings portalen som finns i IoT Hub gör följande: 

- Hantera IoT Hub säkerhet.

- Grundläggande hantering av en IoT-enhets säkerhet utan att installera en agent som baseras på IoT Hub telemetri. 

- Avancerad hantering av en IoT-enhets säkerhet baserat på Micro agent.

> [!NOTE]
> Defender for IoT stöder för närvarande bara IoT-hubbar på standard nivå.

## <a name="prerequisites"></a>Förutsättningar

Inget

## <a name="onboard-defender-for-iot-to-an-iot-hub"></a>Publicera Defender för IoT till en IoT Hub

För alla nya IoT-hubbar är Defender för IoT inställt **på** som standard. Du kan kontrol lera att Defender för IoT har växlats **till vid skapande av IoT Hub** .

För att kontrol lera att växlingen är inställd på **på**:

1. Gå till Azure-portalen.

1. Välj **IoT Hub** i listan över Azure-tjänster.

1. Välj **Skapa**.

    :::image type="content" source="media/quickstart-onboard-iot-hub/create-iot-hub.png" alt-text="Välj knappen Skapa i det översta verktygsfältet." lightbox="media/quickstart-onboard-iot-hub/create-iot-hub-expanded.png":::

1. Välj fliken **hantering** och kontrol lera att aktivera **Defender för IoT** -växling är inställt **på på**.

    :::image type="content" source="media/quickstart-onboard-iot-hub/management-tab.png" alt-text="Kontrol lera att aktivera Defender för IoT är inställt på på.":::

## <a name="onboard-defender-for-iot-to-an-existing-iot-hub"></a>Publicera Defender för IoT till en befintlig IoT Hub

Du kan publicera Defender för IoT till en befintlig IoT Hub, där du sedan kan övervaka enhetens identitets hantering, enhet till molnet och kommunikations mönster för molnet till enheten.

För att publicera Defender för IoT till en befintlig IoT Hub:

1. Navigera till IoT Hub. 

1. Välj IoT Hub som ska publiceras.

1. Välj ett alternativ under avsnittet **säkerhet** .

1. Klicka på **skydda din IoT-lösning**   och fyll i formuläret onboarding. 

    :::image type="content" source="media/quickstart-onboard-iot-hub/secure-your-iot-solution.png" alt-text="Välj knappen skydda din IoT-lösning för att skydda din lösning.":::

Knappen **skydda din IoT-lösning** visas bara om IoT Hub inte redan har registrerats, eller om du har lämnat tjänsten Defender för IoT **avstängd**.

:::image type="content" source="media/quickstart-onboard-iot-hub/toggle-is-off.png" alt-text="Om växlingen var inaktive rad under onboarding.":::

## <a name="next-steps"></a>Nästa steg

Fortsätt till nästa artikel för att konfigurera din lösning...

> [!div class="nextstepaction"]
> [Skapa en Defender IoT Micro agent-modul, delad (för hands version)](quickstart-create-micro-agent-module-twin.md)
