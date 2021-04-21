---
title: Konceptuell förklaring av grunderna i Defender-IoT-micro-agent för Azure RTOS
description: Lär dig grunderna om Defender-IoT-micro-agent för att Azure RTOS begrepp och arbetsflöde.
ms.topic: conceptual
ms.date: 09/09/2020
ms.openlocfilehash: 04b86d401bcb9fc919c36b28cf4f80ea3bfd7030
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107750490"
---
# <a name="defender-iot-micro-agent-for-azure-rtos-preview"></a>Defender-IoT-micro-agent för Azure RTOS (förhandsversion)

Använd den här artikeln för att få en bättre förståelse för Defender-IoT-micro-agenten för Azure RTOS, inklusive funktioner och fördelar samt länkar till relevanta konfigurations- och referensresurser. 

## <a name="azure-rtos-iot-defender-iot-micro-agent"></a>Azure RTOS IoT Defender-IoT-micro-agent

Defender-IoT-micro-agent för Azure RTOS en omfattande säkerhetslösning för Azure RTOS enheter som en del av NetX Duo-erbjudandet. I NetX Duo-erbjudandet levereras Azure RTOS med inbyggd Azure IoT Defender-IoT-micro-agent och ger täckning för vanliga hot på dina realtidsoperativsystemetsenheter när de har aktiverats.

Defender-IoT-micro-agenten för Azure RTOS körs i bakgrunden och ger en sömlös användarupplevelse när säkerhetsmeddelanden skickas med hjälp av varje kunds unika anslutningar till sina IoT Hub. Defender-IoT-micro-agent för Azure RTOS är aktiverat som standard.  

## <a name="azure-rtos-netx-duo"></a>Azure RTOS NetX Duo

Azure RTOS NetX Duo är en avancerad TCP/IP-nätverksstack i branschklass som utformats särskilt för djupt inbäddade realtids- och IoT-program. Azure RTOS NetX Duo är en dubbel IPv4- och IPv6-nätverksstack som ger en omfattande uppsättning protokoll, inklusive säkerhet och moln. Läs mer om [Azure RTOS NetX Duo-lösningar.](/azure/rtos/netx-duo/)

Modulen innehåller följande funktioner:

- **Identifiera skadliga nätverksaktiviteter**
- **Baslinjer för enhetsbeteende baserat på anpassade aviseringar**
- **Förbättra enhetens säkerhetshygien**

## <a name="defender-iot-micro-agent-for-azure-rtos-architecture"></a>Defender-IoT-micro-agent för Azure RTOS arkitektur

Defender-IoT-micro-agenten för Azure RTOS initieras av Azure IoT-mellanprogramplattformen och använder IoT Hub klienter för att skicka säkerhetstelemetri till hubben.

:::image type="content" source="media/architecture/security-module-state-diagram.png" alt-text="Tillståndsdiagram och informationsflöde för Azure IoT Defender-IoT-mikroagent":::

Defender-IoT-micro-agenten för Azure RTOS övervakar följande enhetsaktivitet och information med hjälp av tre insamlare:
- Enhetsnätverksaktivitet **TCP,** **UDP** och **ICM**
- Systeminformation som **Threadx-** och **NetX Duo-versioner**
- Pulsslagshändelser

Varje insamlare är länkad till en prioritetsgrupp och varje prioritetsgrupp har ett eget intervall med möjliga värden **för Låg,** **Medel** och **Hög.** Intervallen påverkar tidsintervallet då data samlas in och skickas.

Varje tidsintervall kan konfigureras och IoT-anslutningsapparna kan aktiveras och inaktiveras för att ytterligare [anpassa din lösning.](how-to-azure-rtos-security-module.md) 

## <a name="supported-security-alerts-and-recommendations"></a>Säkerhetsaviseringar och rekommendationer som stöds

Defender-IoT-micro-agenten för Azure RTOS har stöd för specifika säkerhetsaviseringar och rekommendationer. Se till att [granska och anpassa relevanta aviserings- och rekommendationsvärden](concept-rtos-security-alerts-recommendations.md) för din tjänst när du har slutfört den inledande konfigurationen.

## <a name="ready-to-begin"></a>Är du redo att börja?

Defender-IoT-micro-agent för Azure RTOS tillhandahålls som en kostnadsfri nedladdning för dina IoT-enheter. Defender for IoT-molntjänsten är tillgänglig med en 30-dagars utvärderingsversion per Azure-prenumeration. [Ladda ned Defender-IoT-micro-agenten](https://github.com/azure-rtos/azure-iot-preview/releases) nu så ska vi komma igång. 

## <a name="next-steps"></a>Nästa steg

- Kom igång med Defender-IoT-micro-agent för Azure RTOS [och konfigurera](quickstart-azure-rtos-security-module.md).
- Läs mer om Defender-IoT-micro-agent för att Azure RTOS [säkerhetsaviseringar och rekommendationer som stöder](concept-rtos-security-alerts-recommendations.md). 
- Använd Defender-IoT-micro-agent för att Azure RTOS [API.](azure-rtos-security-module-api.md)