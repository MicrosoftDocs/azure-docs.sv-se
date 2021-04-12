---
title: 'Snabb start: översikt över säkerhets agenter'
description: I den här snabb starten lär du dig att förstå säkerhets agent arkitekturen för de agenter som används i Azure Defender for IoT-tjänsten.
ms.topic: quickstart
ms.date: 4/4/2021
ms.openlocfilehash: 0937cccb0335f4eee16ca3590babe9574320b89f
ms.sourcegitcommit: 77d7639e83c6d8eb6c2ce805b6130ff9c73e5d29
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/05/2021
ms.locfileid: "106384994"
---
# <a name="quickstart-security-agent-reference-architecture"></a>Snabb start: referens arkitektur för säkerhets agent

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

## <a name="prerequisites"></a>Krav

- Inget

## <a name="agent-supported-platforms"></a>Plattformar som stöds

Defender for IoT erbjuder olika installations agenter för 32-bitars och 64-bitars Windows, och samma för 32-bitars och 64-bitars Linux. Kontrol lera att du har rätt agent installations program för var och en av dina enheter enligt följande tabell:

| Arkitektur | Linux | Windows | Information |
|--|--|--|--|
| 32 bitar | C | C# |  |
| 64-bitars | C# eller C | C# | Vi rekommenderar att du använder C-agenten för enheter med mer begränsade eller minimala enhets resurser. |


## <a name="next-steps"></a>Nästa steg

I den här artikeln fick du en översikt på hög nivå om Defender för IoT Defender-IoT-Micro-agent-arkitektur och tillgängliga installations program.
För att fortsätta komma igång med Defender för IoT-distribution, 

> [!div class="nextstepaction"]
> [autentiseringsmetoder för säkerhets agent](concept-security-agent-authentication-methods.md)
