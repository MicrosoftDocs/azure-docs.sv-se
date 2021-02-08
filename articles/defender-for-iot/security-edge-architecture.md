---
title: Defender för IoT-säkerhetsmodulen för IoT Edge
description: Lär dig mer om arkitekturen och funktionerna i Azure Defender för IoT-säkerhetsmodulen för IoT Edge.
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
ms.date: 09/09/2020
ms.author: mlottner
ms.openlocfilehash: 3e653983b39e8eb4ac13ad840ca53e4ab6d1cfc7
ms.sourcegitcommit: 2501fe97400e16f4008449abd1dd6e000973a174
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/08/2021
ms.locfileid: "99820627"
---
# <a name="azure-defender-for-iot-edge-security-module"></a>Azure Defender för IoT Edge säkerhetsmodul

[Azure IoT Edge](../iot-edge/index.yml) tillhandahåller kraftfulla funktioner för att hantera och utföra affärs arbets flöden i gränsen.
Den nyckel del som IoT Edge spelar i IoT-miljöer gör det särskilt attraktivt för skadliga aktörer.

Defender för IoT-säkerhetsmodulen innehåller en omfattande säkerhetslösning för dina IoT Edge enheter.
Defender för IoT-modulen samlar in, samlar in och analyserar rå säkerhets data från operativ systemet och behållar systemet till rekommenderade säkerhets rekommendationer och aviseringar.

Precis som med Defender för IoT-säkerhetsagenter för IoT-enheter är Defender för IoT Edge-modulen mycket anpassningsbar genom sin modul.
Mer information finns i [Konfigurera agenten](how-to-agent-configuration.md) .

Defender för IoT-säkerhetsmodulen för IoT Edge erbjuder följande funktioner:

- Samlar in rå säkerhets händelser från det underliggande operativ systemet (Linux) och IoT Edge container system.

  Se [Defender för IoT agent-konfiguration](how-to-agent-configuration.md) för att lära dig mer om tillgängliga säkerhets data insamlare.

- Analys av IoT Edge distributions manifest.

- Sammanställer rå säkerhets händelser till meddelanden som skickas via [IoT Edge Hub](../iot-edge/iot-edge-runtime.md#iot-edge-hub).

- Ta bort konfigurationen genom att använda säkerhetsmodulen dubbla.

  Mer information finns i [Konfigurera en Defender för IoT-agent](how-to-agent-configuration.md) .

Defender för IoT-säkerhetsmodulen för IoT Edge körs i privilegierat läge under IoT Edge.
Privilegierat läge krävs för att modulen ska kunna övervaka operativ systemet och andra IoT Edge moduler.

## <a name="module-supported-platforms"></a>Plattformar som stöds av moduler

Defender för IoT-säkerhetsmodulen för IoT Edge är för närvarande bara tillgänglig för Linux.

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du lärt dig om arkitekturen och funktionerna i Defender för IoT-säkerhetsmodulen för IoT Edge.

Använd följande artiklar om du vill fortsätta komma igång med Defender för IoT-distribution:

- Distribuera [säkerhetsmodulen för IoT Edge](how-to-deploy-edge.md)
- Lär dig hur du [konfigurerar din säkerhetsmodul](how-to-agent-configuration.md)
- Granska horisont för Defender for IoT [Defender för IoT](resources-manage-proprietary-protocols.md)
- Lär dig hur du [aktiverar Defender för IoT-tjänsten i din IoT Hub](quickstart-onboard-iot-hub.md)
- Läs mer om tjänsten från [vanliga frågor och svar om Defender för IoT](resources-frequently-asked-questions.md)