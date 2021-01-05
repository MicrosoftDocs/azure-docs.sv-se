---
title: Arkitektur för säkerhets agent
description: Förstå säkerhets agent arkitekturen för de agenter som används i Azure Defender for IoT-tjänsten.
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/26/2019
ms.author: mlottner
ms.openlocfilehash: 5773f7af3bb065976e8f05d7b54c58b90da2d3d2
ms.sourcegitcommit: 8be279f92d5c07a37adfe766dc40648c673d8aa8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/31/2020
ms.locfileid: "97835133"
---
# <a name="security-agent-reference-architecture"></a>Referens arkitektur för säkerhets agent

Azure Defender for IoT tillhandahåller referens arkitektur för säkerhets agenter som loggar, bearbetar, sammanställer och skickar säkerhets data via IoT Hub.

Säkerhets agenter är utformade för att fungera i en begränsad IoT-miljö och är mycket anpassningsbara utifrån de värden som de ger jämfört med de resurser de använder.

Säkerhets agenter har stöd för följande funktioner:

- Samla in rå säkerhets händelser från det underliggande operativ systemet (Linux, Windows). Mer information om tillgängliga säkerhets data insamlare finns i [Defender för IoT agent-konfiguration](how-to-agent-configuration.md).

- Samla in rå säkerhets händelser i meddelanden som skickas via IoT Hub.

- Autentisera med befintlig enhets identitet eller en särskild modul identitet. Mer information finns i [autentiseringsmetoder för säkerhets agenter](concept-security-agent-authentication-methods.md) .

- Fjärrkonfigurera genom att använda **azureiotsecurity** -modulen, dubbla. Mer information finns i [Konfigurera en Defender för IoT-agent](how-to-agent-configuration.md).

Defender för IoT-säkerhetsagenter utvecklas som projekt med öppen källkod och är tillgängliga från GitHub:

- [Defender för IoT C-baserad agent](https://github.com/Azure/Azure-IoT-Security-Agent-C)
- [Defender för IoT C#-baserad agent](https://github.com/Azure/Azure-IoT-Security-Agent-CS)

## <a name="agent-supported-platforms"></a>Plattformar som stöds

Defender for IoT erbjuder olika installations agenter för 32-och 64-bitars fönster, och samma för 32-och 64-bitars Linux. Kontrol lera att du har rätt agent installations program för var och en av dina enheter enligt följande tabell:

| Arkitektur | Linux | Windows | Information |
|--|--|--|--|
| 32 | C | C# |  |
| 64 | C# eller C | C# | Vi rekommenderar att du använder C-agenten för enheter med mer begränsade eller minimala enhets resurser. |


## <a name="next-steps"></a>Nästa steg

I den här artikeln har du lärt dig om Defender för IoT Security Agent-arkitekturen och de tillgängliga installations program.

Använd följande artiklar om du vill fortsätta komma igång med Defender för IoT-distribution:

- Förstå [autentiseringsmetoder för säkerhets agenter](concept-security-agent-authentication-methods.md)
- Välj och distribuera en [säkerhets agent](how-to-deploy-agent.md)
- Granska kraven för Defender för IoT- [system](quickstart-system-prerequisites.md)
- Lär dig hur du [aktiverar Defender för IoT-tjänsten i din IoT Hub](quickstart-onboard-iot-hub.md)
- Läs mer om tjänsten från [vanliga frågor och svar om Defender för IoT](resources-frequently-asked-questions.md)
