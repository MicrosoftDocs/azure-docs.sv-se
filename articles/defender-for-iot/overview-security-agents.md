---
title: Säkerhetsagenter
description: Kom igång med att förstå, konfigurera, distribuera och använda Azure Defender för IoT Security Service-agenter på dina IoT-enheter.
ms.topic: conceptual
ms.date: 1/24/2021
ms.openlocfilehash: a1d74de9824b9e825f87754da21070d4e7f1ee33
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "104783497"
---
# <a name="get-started-with-azure-defender-for-iot-device-micro-agents"></a>Kom igång med Azure Defender för IoT Device Micro-agenter

Defender för IoT-säkerhetsagenter ger förbättrade säkerhetsfunktioner, till exempel övervakning av operativ Systems konfiguration metod tips. Ta kontroll över ditt enhets fält hot skydd och säkerhets position med en enda tjänst.

Skydds ansvariga för IoT-säkerhetsagenter hanterar rå händelse insamling från enhetens operativ system, händelse agg regering för att minska kostnaderna och konfigurationen genom en enhets modul med dubbla. Säkerhets meddelanden skickas via IoT Hub, till Defender för IoT Analytics-tjänster.

Använd följande arbets flöde för att distribuera och testa dina Defender for IoT-säkerhetsagenter:

1. [Aktivera Defender för IoT-tjänsten till din IoT Hub](quickstart-onboard-iot-hub.md).

1. Om din IoT Hub inte har några registrerade enheter [registrerar du en ny enhet](../iot-accelerators/iot-accelerators-device-simulation-overview.md).

1. [Skapa en DefenderIotMicroAgent-modul](quickstart-create-micro-agent-module-twin.md) för dina enheter.

1. Om du vill installera agenten på en simulerad Azure-enhet i stället för att installera på en faktisk enhet, kan du [skapa en ny virtuell Azure-dator (VM)](../virtual-machines/linux/quick-create-portal.md) i en tillgänglig zon.

1. [Distribuera en Defender for IoT-säkerhetsagent](how-to-deploy-linux-cs.md) på din IoT-enhet eller till en ny virtuell dator.

1. Följ anvisningarna för [trigger_events](https://aka.ms/iot-security-github-trigger-events) för att köra ett bas linje händelse för operativ system.

1. Verifiera Defender för IoT-rekommendationer som svar på den simulerade operativ systemets bas linje kontroll har misslyckats i föregående steg. Börja verifiera 30 minuter efter att du kört skriptet.

## <a name="next-steps"></a>Nästa steg

- Konfigurera din [lösning](quickstart-configure-your-solution.md)
- [Skapa Defender-IoT-Micro-agenter](quickstart-create-security-twin.md)
- Konfigurera [anpassade aviseringar](quickstart-create-custom-alerts.md)
- [Distribuera en säkerhets agent](how-to-deploy-agent.md)