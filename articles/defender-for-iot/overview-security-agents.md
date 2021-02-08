---
title: Säkerhetsagenter
description: Kom igång med att förstå, konfigurera, distribuera och använda Azure Defender för IoT Security Service-agenter på dina IoT-enheter.
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: shhazam-ms
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 1/24/2021
ms.author: shhazam
ms.openlocfilehash: 61c7f1bddd40151aff2b1ca556045d34c4a1cc0d
ms.sourcegitcommit: 2501fe97400e16f4008449abd1dd6e000973a174
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/08/2021
ms.locfileid: "99820831"
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
- [Skapa säkerhetsmoduler](quickstart-create-security-twin.md)
- Konfigurera [anpassade aviseringar](quickstart-create-custom-alerts.md)
- [Distribuera en säkerhets agent](how-to-deploy-agent.md)