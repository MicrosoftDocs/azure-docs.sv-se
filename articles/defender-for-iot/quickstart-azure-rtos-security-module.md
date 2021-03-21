---
title: 'Snabb start: Konfigurera och aktivera Defender-IoT-Micro-agenten för Azure återställnings tider'
description: Lär dig hur du integrerar och aktiverar tjänsten Defender-IoT-Micro-Micro-agent för Azure återställnings tider-tjänsten i Azure IoT Hub.
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
ms.date: 01/24/2021
ms.author: shhazam
ms.openlocfilehash: 3c1af1128b99cbd3263ddffc834eb27ab9dec564
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "103489853"
---
# <a name="quickstart-defender-iot-micro-agent-for-azure-rtos-preview"></a>Snabb start: Defender-IoT-Micro-agent för Azure återställnings tider (för hands version)

Den här artikeln innehåller en förklaring av kraven innan du börjar och förklarar hur du aktiverar tjänsten Defender-IoT-Micro-agent för Azure återställnings tider-tjänsten på en IoT Hub. Om du för närvarande inte har en IoT Hub kan du läsa [skapa ett IoT Hub med hjälp av Azure Portal](../iot-hub/iot-hub-create-through-portal.md) för att komma igång.

## <a name="prerequisites"></a>Förutsättningar 

### <a name="supported-devices"></a>Enheter som stöds

- ST STM32F746G Discovery Kit
- NXP i.MX RT1060 EVK
- Mikrochip SAM E54 Xplained Pro EVK

Ladda ned, kompilera och kör en av. zip-filerna för den speciella tavlan och verktyget (IAR, semi, IDE eller PC) som du väljer från [GitHub-resursen för Defender-IoT-Micro-agent för Azure återställnings tider](https://github.com/azure-rtos/azure-iot-preview/releases).

### <a name="azure-resources"></a>Azure-resurser

Nästa steg för att komma igång förbereder dina Azure-resurser. Du behöver ett IoT Hub och vi föreslår en Log Analytics arbets yta. För IoT Hub behöver du din IoT Hub anslutnings sträng för att ansluta till enheten. 
  
### <a name="iot-hub-connection"></a>IoT Hub anslutning

Det krävs en IoT Hub anslutning för att komma igång. 

1. Öppna din **IoT Hub** i Azure Portal.

1. Navigera till **IoT-enheter**.

1. Välj **Skapa**.

1. Kopiera IoT-anslutningssträngen till [konfigurations filen](how-to-azure-rtos-security-module.md).

Autentiseringsuppgifterna för anslutningen hämtas från konfiguration av användar program **HOST_NAME**, **DEVICE_ID** och **DEVICE_SYMMETRIC_KEY**.

Defender-IoT-Micro-agenten för Azure återställnings tider använder Azure IoT mellanprogram anslutningar baserat på **MQTT** -protokollet.

## <a name="next-steps"></a>Nästa steg

Fortsätt till nästa artikel och Slutför konfigurationen och anpassningen av lösningen.

> [!div class="nextstepaction"]
> [Konfigurera och anpassa Defender-IoT-Micro-agent för Azure återställnings tider (för hands version)](how-to-azure-rtos-security-module.md)
