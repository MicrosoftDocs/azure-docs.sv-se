---
title: Översikt över säkerhets agenter
description: Förstå säkerhets agent arkitekturen för de agenter som används i Azure Defender for IoT-tjänsten.
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
ms.date: 01/24/2021
ms.author: shhazam
ms.openlocfilehash: ff837fe88f878c522366b2b6bc19a1ef3954b667
ms.sourcegitcommit: 2501fe97400e16f4008449abd1dd6e000973a174
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/08/2021
ms.locfileid: "99820661"
---
# <a name="security-agent-reference-architecture"></a>Referens arkitektur för säkerhets agent

Azure Defender for IoT tillhandahåller referens arkitektur för säkerhets agenter som loggar, bearbetar, sammanställer och skickar säkerhets data via IoT Hub.

Säkerhets agenter är utformade för att fungera i en begränsad IoT-miljö och är mycket anpassningsbara utifrån de värden som de ger jämfört med de resurser de använder.

Säkerhets agenter har stöd för följande funktioner:

- Autentisera med befintlig enhets identitet eller en särskild modul identitet. Mer information finns i [autentiseringsmetoder för säkerhets agenter](concept-security-agent-authentication-methods.md).

- Samla in rå säkerhets händelser från det underliggande operativ systemet (Linux, Windows). Mer information om tillgängliga säkerhets data insamlare finns i [Defender för IoT agent-konfiguration](how-to-agent-configuration.md).

- Samla in rå säkerhets händelser i meddelanden som skickas via IoT Hub.

- Fjärrkonfigurera genom att använda **azureiotsecurity** -modulen, dubbla. Mer information finns i [Konfigurera en Defender för IoT-agent](how-to-agent-configuration.md).

Defender för IoT-säkerhetsagenter utvecklas som projekt med öppen källkod och är tillgängliga från GitHub:

- [Defender för IoT C-baserad agent](https://github.com/Azure/Azure-IoT-Security-Agent-C)
- [Defender för IoT C#-baserad agent](https://github.com/Azure/Azure-IoT-Security-Agent-CS)

## <a name="agent-supported-platforms"></a>Plattformar som stöds

Defender for IoT erbjuder olika installations agenter för 32-bitars och 64-bitars Windows, och samma för 32-bitars och 64-bitars Linux. Kontrol lera att du har rätt agent installations program för var och en av dina enheter enligt följande tabell:

| Arkitektur | Linux | Windows | Information |
|--|--|--|--|
| 32 bitar | C | C# |  |
| 64-bitars | C# eller C | C# | Vi rekommenderar att du använder C-agenten för enheter med mer begränsade eller minimala enhets resurser. |


## <a name="next-steps"></a>Nästa steg

I den här artikeln fick du en översikt över Defender för arkitektur för IoT-säkerhetsmodulen och tillgängliga installations program.

Använd följande artiklar om du vill fortsätta komma igång med Defender för IoT-distribution:

- Förstå [autentiseringsmetoder för säkerhets agenter](concept-security-agent-authentication-methods.md)
- Välj och distribuera en [säkerhets agent](how-to-deploy-agent.md)
- Granska kraven för Defender för IoT- [system](quickstart-system-prerequisites.md)
- Lär dig hur du [aktiverar Defender för IoT-tjänsten i din IoT Hub](quickstart-onboard-iot-hub.md)
- Läs mer om tjänsten från [vanliga frågor och svar om Defender för IoT](resources-frequently-asked-questions.md)
