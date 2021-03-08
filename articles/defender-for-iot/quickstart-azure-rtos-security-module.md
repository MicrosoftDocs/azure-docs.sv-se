---
title: 'Snabb start: Konfigurera och aktivera säkerhetsmodulen för Azure återställnings tider'
description: I den här snabb starten får du lära dig hur du integrerar och aktiverar säkerhetsmodulen för Azure återställnings tider service i Azure IoT Hub.
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
ms.openlocfilehash: 19a439ec48d4a8705ffb46db7ca037b51449083d
ms.sourcegitcommit: f6193c2c6ce3b4db379c3f474fdbb40c6585553b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/08/2021
ms.locfileid: "102447307"
---
# <a name="quickstart-security-module-for-azure-rtos"></a>Snabb start: säkerhetsmodul för Azure-återställnings tider 

Den här artikeln innehåller en förklaring av kraven innan du börjar och förklarar hur du aktiverar säkerhetsmodulen för Azure återställnings tider service på en IoT Hub. Om du för närvarande inte har en IoT Hub kan du läsa [skapa ett IoT Hub med hjälp av Azure Portal](../iot-hub/iot-hub-create-through-portal.md) för att komma igång.

## <a name="prerequisites"></a>Förutsättningar 

### <a name="supported-devices"></a>Enheter som stöds

- ST STM32F746G Discovery Kit
- NXP i.MX RT1060 EVK
- Mikrochip SAM E54 Xplained Pro EVK

Ladda ned, kompilera och kör en av. zip-filerna för den speciella tavlan och verktyget (IAR, semi, IDE eller PC) som du väljer från [säkerhetsmodulen för Azure återställnings tider GitHub-resursen](https://github.com/azure-rtos/azure-iot-preview/releases).

### <a name="azure-resources"></a>Azure-resurser

Nästa steg för att komma igång förbereder dina Azure-resurser. Du behöver ett IoT Hub och vi föreslår en Log Analytics arbets yta. För IoT Hub behöver du din IoT Hub anslutnings sträng för att ansluta till enheten. 
  
### <a name="iot-hub-connection"></a>IoT Hub anslutning

Det krävs en IoT Hub anslutning för att komma igång. 

1. Öppna din **IoT Hub** i Azure Portal.

1. Navigera till **IoT-enheter**.

1. Välj **Skapa**.

1. Kopiera IoT-anslutningssträngen till [konfigurations filen](how-to-azure-rtos-security-module.md).

Autentiseringsuppgifterna för anslutningen hämtas från konfiguration av användar program **HOST_NAME**, **DEVICE_ID** och **DEVICE_SYMMETRIC_KEY**.

Säkerhetsmodulen för Azure återställnings tider använder Azure IoT mellanprogram anslutningar baserat på **MQTT** -protokollet.

## <a name="next-steps"></a>Nästa steg

Fortsätt till nästa artikel och Slutför konfigurationen och anpassningen av lösningen.

> [!div class="nextstepaction"]
> [Konfigurera säkerhetsmodul för Azure RTOS](how-to-azure-rtos-security-module.md)
