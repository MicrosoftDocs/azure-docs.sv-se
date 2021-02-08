---
title: Publicera till Defender för IoT-agent-baserad lösning
description: Lär dig hur du integrerar och aktiverar tjänsten Defender för IoT-säkerhet i Azure-IoT Hub.
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: shhazam-ms
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 1/20/2021
ms.author: shhazam
ms.openlocfilehash: 127e439a7740cb97cbe126071aaaa5245cd85782
ms.sourcegitcommit: 4784fbba18bab59b203734b6e3a4d62d1dadf031
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/08/2021
ms.locfileid: "99809141"
---
# <a name="onboard-to-defender-for-iot-agent-based-solution"></a>Publicera till Defender för IoT-agent-baserad lösning

I den här artikeln förklaras hur du aktiverar tjänsten Defender för IoT på din befintliga IoT Hub. Om du för närvarande inte har en IoT Hub kan du läsa [skapa ett IoT Hub med hjälp av Azure Portal](../iot-hub/iot-hub-create-through-portal.md) för att komma igång.

Du kan hantera din IoT-säkerhet via IoT Hub i Defender för IoT. Hanterings portalen som finns i IoT Hub gör följande: 

- Hantera IoT Hub säkerhet.

- Grundläggande hantering av en IoT-enhets säkerhet utan att installera en agent som baseras på IoT Hub telemetri. 

- Avancerad hantering av en IoT-enhets säkerhet baserat på Micro agent.

> [!NOTE]
> Defender for IoT stöder för närvarande bara IoT-hubbar på standard nivå.

## <a name="onboard-to-defender-for-iot-in-iot-hub"></a>Publicera till Defender för IoT i IoT Hub

För alla nya IoT-hubbar är Defender för IoT inställt **på** som standard. Du kan kontrol lera att Defender för IoT har växlats **till vid skapande av IoT Hub** .

För att kontrol lera att växlingen är inställd på **på**:

1. Gå till Azure-portalen.

1. Välj **IoT Hub** i listan över Azure-tjänster.

1. Välj **Skapa**.

    :::image type="content" source="media/quickstart-onboard-iot-hub/create-iot-hub.png" alt-text="Välj knappen Skapa i det översta verktygsfältet." lightbox="media/quickstart-onboard-iot-hub/create-iot-hub-expanded.png":::

1. Välj fliken **hantering** och kontrol lera att aktivera **Defender för IoT** -växling är inställt **på på**.

    :::image type="content" source="media/quickstart-onboard-iot-hub/management-tab.png" alt-text="Kontrol lera att aktivera Defender för IoT är inställt på på.":::

## <a name="onboard-defender-for-iot-to-an-existing-iot-hub"></a>Publicera Defender för IoT till en befintlig IoT Hub

Du kan övervaka enhetens identitets hanterings-, enhets-och moln-och enhets kommunikations mönster genom att göra följande för att starta tjänsten: 

1. Navigera till IoT Hub. 

1. Välj menyn **säkerhets översikt**   . 

1. Klicka på skydda din IoT-lösning och fyll i formuläret onboarding. 


## <a name="next-steps"></a>Nästa steg

Fortsätt till nästa artikel för att konfigurera din lösning...

> [!div class="nextstepaction"]
> [Skapa en Defender IoT Micro agent-modul, delad (för hands version)](quickstart-create-micro-agent-module-twin.md)
