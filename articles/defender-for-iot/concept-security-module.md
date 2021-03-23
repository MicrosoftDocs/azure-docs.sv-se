---
title: Defender-IoT-Micro-agent och enhets dubbla
description: Lär dig mer om konceptet Defender-IoT-Micro-agent och hur de används i Defender för IoT.
ms.topic: conceptual
ms.date: 07/24/2019
ms.openlocfilehash: 1ed6faf03d168ed7a2a2f07733cb7e238d234915
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/22/2021
ms.locfileid: "104779179"
---
# <a name="defender-iot-micro-agent"></a>Defender-IoT-Micro-agent

I den här artikeln förklaras hur Defender för IoT använder enhets dubbla och moduler.

## <a name="device-twins"></a>Enhets dubbla

För IoT-lösningar som är inbyggda i Azure spelar enheten upp en viktig roll i både enhets hantering och process automatisering.

Defender för IoT erbjuder fullständig integrering med din befintliga plattform för IoT-enhets hantering, så att du kan hantera din enhets säkerhets status och använda befintliga funktioner för enhets kontroll. Integreringen uppnås genom att använda den IoT Hub dubbla mekanismen.

Lär dig mer om begreppet [enhets enheter](../iot-hub/iot-hub-devguide-device-twins.md) i Azure IoT Hub.

## <a name="defender-iot-micro-agent-twins"></a>Defender-IoT-Micro-agent är dubbla

Defender för IoT upprätthåller en Defender-IoT-mikroagent som är dubbel för varje enhet i tjänsten.
Defender-IoT-Micro-agenten innehåller all information som är relevant för enhets säkerhet för varje enskild enhet i lösningen.
Egenskaperna för enhets säkerhet underhålls i en dedikerad Defender-IoT-mikroagent-enhet för säkrare kommunikation och för att aktivera uppdateringar och underhåll som kräver färre resurser.

Se [skapa Defender-IoT-Micro-agents dubbla](quickstart-create-security-twin.md) och [Konfigurera säkerhets agenter](how-to-agent-configuration.md) för att lära dig hur du skapar, anpassar och konfigurerar den dubbla. Mer information om hur modulen finns i IoT Hub finns i [förstå modulernas dubblare](../iot-hub/iot-hub-devguide-module-twins.md) .

## <a name="see-also"></a>Se även

- [Översikt över Defender för IoT](overview.md)
- [Distribuera säkerhetsagenter](how-to-deploy-agent.md)
- [Autentiseringsmetoder för säkerhets agent](concept-security-agent-authentication-methods.md)