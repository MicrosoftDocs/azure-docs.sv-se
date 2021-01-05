---
title: Översikt över säkerhets agent
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
ms.date: 12/27/2019
ms.author: shhazam
ms.openlocfilehash: d3b50d909fb167ee123b548603628bae7d844c5b
ms.sourcegitcommit: 8be279f92d5c07a37adfe766dc40648c673d8aa8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/31/2020
ms.locfileid: "97843693"
---
# <a name="get-started-with-azure-defender-for-iot-device-security-agents"></a>Kom igång med Azure Defender för IoT-enhetens säkerhets agenter

Defender för IoT-säkerhetsagenter ger förbättrade säkerhetsfunktioner, till exempel övervakning av fjärr anslutningar, aktiva program, inloggnings händelser och metod tips för operativ system konfiguration. Ta kontroll över ditt enhets fält hot skydd och säkerhets position med en enda tjänst.

Referens arkitektur för Linux-och Windows-säkerhetsagenter, både i C# och C.

Säkerhets agenter för Defender för IoT hanterar rå händelse insamling från enhetens operativ system, händelse agg regering för att minska kostnaderna och konfigurationen via en enhets modul, dubbel. Säkerhets meddelanden skickas via IoT Hub, till Defender för IoT Analytics-tjänster.

Använd följande arbets flöde för att distribuera och testa dina Defender for IoT-säkerhetsagenter:

1. [Aktivera Defender för IoT-tjänsten till din IoT Hub](quickstart-onboard-iot-hub.md)
1. Om din IoT Hub inte har några registrerade enheter [registrerar du en ny enhet](../iot-accelerators/quickstart-device-simulation-deploy.md).
1. [Skapa en azureiotsecurity säkerhetsmodul](quickstart-create-security-twin.md) för dina enheter.
1. Om du vill installera agenten på en simulerad Azure-enhet i stället för att installera på en faktisk enhet, kan du [skapa en ny virtuell Azure-dator (VM)](../virtual-machines/linux/quick-create-portal.md) i en tillgänglig zon.
1. [Distribuera en Defender for IoT-säkerhetsagent](how-to-deploy-linux-cs.md) på din IoT-enhet eller till en ny virtuell dator.
1. Följ anvisningarna för [trigger_events](https://aka.ms/iot-security-github-trigger-events) för att köra en oskadlig simulering av ett angrepp.
1. Verifiera Defender för IoT-aviseringar som svar på det simulerade angreppet i föregående steg. Börja verifiera fem minuter efter att du kört skriptet.
1. Utforska [aviseringar](concept-security-alerts.md), [rekommendationer](concept-recommendations.md)och [djupgående att använda Log Analytics](how-to-security-data-access.md) med hjälp av IoT Hub.

## <a name="next-steps"></a>Nästa steg

- Konfigurera din [lösning](quickstart-configure-your-solution.md)
- [Skapa säkerhetsmoduler](quickstart-create-security-twin.md)
- Konfigurera [anpassade aviseringar](quickstart-create-custom-alerts.md)
- [Distribuera en säkerhets agent](how-to-deploy-agent.md)