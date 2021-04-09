---
title: Välj och distribuera säkerhets agenter
description: Lär dig mer om hur du väljer och distribuerar Defender för IoT-säkerhetsagenter på IoT-enheter.
ms.topic: conceptual
ms.date: 07/23/2019
ms.openlocfilehash: c71c92ffa79c844f3529265320b46eadd0c158cf
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "104778856"
---
# <a name="select-and-deploy-a-security-agent-on-your-iot-device"></a>Välj och distribuera en säkerhets agent på din IoT-enhet

Defender for IoT innehåller referens arkitekturer för säkerhets agenter som övervakar och samlar in data från IoT-enheter.
Mer information finns i [referens arkitektur för säkerhets agenter](security-agent-architecture.md).

Agenter utvecklas som projekt med öppen källkod och är tillgängliga i två varianter: <br> [C](https://aka.ms/iot-security-github-c)och [C#](https://aka.ms/iot-security-github-cs).

I den här artikeln kan du se hur du:
- Jämför säkerhets agentens varianter
- Identifiera agent plattformar som stöds
- Välj rätt agent-smak för din lösning

## <a name="understand-security-agent-options"></a>Förstå alternativ för säkerhets agent

Varje Defender för IoT Security Agent-smak erbjuder samma uppsättning funktioner och har stöd för liknande konfigurations alternativ.

Den C-baserade säkerhets agenten har ett mindre minnes utrymme och är det idealiska valet för enheter med färre tillgängliga resurser.

|     | C-baserad säkerhets agent | C#-baserad säkerhets agent |
| --- | ----------- | --------- |
| **Öppen källkod** | Tillgängligt under [MIT-licens](https://en.wikipedia.org/wiki/MIT_License) i [GitHub](https://aka.ms/iot-security-github-c) | Tillgängligt under [MIT-licens](https://en.wikipedia.org/wiki/MIT_License) i [GitHub](https://aka.ms/iot-security-github-cs) |
| **Utvecklingsspråk**    | C | C# |
| **Windows-plattformar som stöds?** | Inga | Ja |
| **Windows-krav** | --- | [WMI](/windows/desktop/wmisdk/) |
| **Linux-plattformar som stöds?** | Ja, x64 och x86 | Ja, endast x64 |
| **Förutsättningar för Linux** | libunwind8, libcurl3, UUID-körning, granskad, audispd-plugin-program | libunwind8, libcurl3, UUID-körning, granskad, audispd-plugin-program, sudo, netstat, program varan iptables |
| **Disk utrymme** | 10,5 MB | 90 MB |
| **Minnes storlek (i genomsnitt)** | 5,5 MB | 33 MB |
| **[Autentisering](concept-security-agent-authentication-methods.md) till IoT Hub** | Ja | Ja |
| **[Insamling](how-to-agent-configuration.md#supported-security-events) av säkerhets data** | Ja | Ja |
| **Händelsesammansättning** | Ja | Ja |
| **Fjärrkonfiguration via [Defender-IoT-Micro-agent, dubbla](concept-security-module.md)** | Ja | Ja |

## <a name="security-agent-installation-guidelines"></a>Installations rikt linjer för säkerhets agent

För **Windows**: installations SecurityAgent.ps1s skriptet måste köras från ett administratörs PowerShell-fönster.

För **Linux**: InstallSecurityAgent.sh måste köras som superanvändare. Vi rekommenderar att du förkorrigerar installations kommandot med "sudo".

## <a name="choose-an-agent-flavor"></a>Välj en agent smak

Besvara följande frågor om dina IoT-enheter för att välja rätt agent:

- Använder du _Windows Server_ eller _Windows IoT Core_?

    [Distribuera en C#-baserad säkerhets agent för Windows](how-to-deploy-windows-cs.md).

- Använder du en Linux-distribution med x86-arkitekturen?

    [Distribuera en C-baserad säkerhets agent för Linux](how-to-deploy-linux-c.md).

- Använder du en Linux-distribution med x64-arkitekturen?

    Båda agent varianter kan användas. <br>
    [Distribuera en C-baserad säkerhets agent för Linux](how-to-deploy-linux-c.md) och/eller [distribuera en C#-baserad säkerhets agent för Linux](how-to-deploy-linux-cs.md).

Båda agent varianter erbjuder samma uppsättning funktioner och stöder liknande konfigurations alternativ.
Mer information finns i [jämförelse av säkerhets agenter](how-to-deploy-agent.md#understand-security-agent-options) .

## <a name="supported-platforms"></a>Plattformar som stöds

Följande lista innehåller alla plattformar som stöds för närvarande.

|Defender för IoT-agent |Operativsystem |Arkitektur |
|--------------|------------|--------------|
|C|Ubuntu 16.04 |    x64|
|C|Ubuntu 18.04 |    x64, ARMv7|
|C|Debian 9 |    x64, x86|
|C#|Ubuntu 16.04     |x64|
|C#|Ubuntu 18.04    |x64, ARMv7|
|C#|Debian 9    |x64|
|C#|Windows Server 2016|    X64|
|C#|Windows 10 IoT Core, build 17763    |x64|
|

## <a name="next-steps"></a>Nästa steg

Om du vill veta mer om konfigurations alternativ fortsätter du till instruktions guiden för agent konfiguration.
> [!div class="nextstepaction"]
> [Konfigurations guide för agent](./how-to-agent-configuration.md)