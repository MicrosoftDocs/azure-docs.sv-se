---
title: Installera & distribuera Linux C-agenten
description: Lär dig hur du installerar och distribuerar säkerhets agenten Defender för IoT C-baserad på Linux
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
ms.date: 07/23/2019
ms.author: mlottner
ms.openlocfilehash: 6d3f96ed60ca784402b6d24eea7234f37c4fb959
ms.sourcegitcommit: f6193c2c6ce3b4db379c3f474fdbb40c6585553b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/08/2021
ms.locfileid: "102449789"
---
# <a name="deploy-defender-for-iot-c-based-security-agent-for-linux"></a>Distribuera Defender för IoT C-baserad säkerhets agent för Linux

I den här guiden beskrivs hur du installerar och distribuerar Defender för IoT C-baserad säkerhets agent på Linux.

- Installera
- Verifiera distributionen
- Avinstallera agenten
- Felsöka

## <a name="prerequisites"></a>Förutsättningar

För andra plattformar och agent-varianter, se [Välj rätt säkerhets agent](how-to-deploy-agent.md).

1. Om du vill distribuera säkerhets agenten krävs lokala administratörs rättigheter på den dator som du vill installera på (sudo).

1. [Skapa en säkerhetsmodul](quickstart-create-security-twin.md) för enheten.

## <a name="installation"></a>Installation

Använd följande arbets flöde om du vill installera och distribuera säkerhets agenten:

1. Hämta den senaste versionen till datorn från [GitHub](https://aka.ms/iot-security-github-c).

1. Extrahera innehållet i paketet och navigera till mappen _/src/installation_ .

1. Lägg till behörigheter som körs i **InstallSecurityAgent-skriptet** genom att köra följande kommando:

   ```
   chmod +x InstallSecurityAgent.sh
   ```

1. Kör sedan:

   ```
   ./InstallSecurityAgent.sh -aui <authentication identity> -aum <authentication method> -f <file path> -hn <host name> -di <device id> -i
   ```

   Mer information om autentiseringsmetoder finns i [Konfigurera autentisering](concept-security-agent-authentication-methods.md) .

Det här skriptet utför följande funktion:

1. Installerar nödvändiga komponenter.

1. Lägger till en tjänst användare (med interaktiv inloggning inaktive rad).

1. Installerar agenten som en **daemon** – förutsätter att enheten använder **system** för tjänst hantering.

1. Konfigurerar agenten med de autentiseringsmetoder som anges.

Om du behöver ytterligare hjälp kör du skriptet med parametern – Help:

```./InstallSecurityAgent.sh --help```

### <a name="uninstall-the-agent"></a>Avinstallera agenten

Avinstallera agenten genom att köra skriptet med parametern –-Uninstall:

```./InstallSecurityAgent.sh -–uninstall```

## <a name="troubleshooting"></a>Felsökning

Kontrol lera distributions statusen genom att köra:

```systemctl status ASCIoTAgent.service```

## <a name="next-steps"></a>Nästa steg

- Läs [översikten över](overview.md) Defender for IoT-tjänsten
- Läs mer om Defender för IoT- [arkitektur](architecture.md)
- Aktivera [tjänsten](quickstart-onboard-iot-hub.md)
- Läs [vanliga frågor och svar](resources-frequently-asked-questions.md)
- Förstå [säkerhets aviseringar](concept-security-alerts.md)
