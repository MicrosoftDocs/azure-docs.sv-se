---
title: Konceptuell förklaring av grunderna i Defender-IoT-Micro-agenten för Azure återställnings tider
description: Lär dig grunderna om Defender-IoT-Micro-agent för Azure återställnings tider-koncept och arbets flöde.
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
ms.openlocfilehash: 04a499f1feae630d3436c75ae2081413789c0ca3
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "103494242"
---
# <a name="defender-iot-micro-agent-for-azure-rtos-preview"></a>Defender-IoT-Micro-agent för Azure återställnings tider (för hands version)

Använd den här artikeln för att få en bättre förståelse för Defender-IoT-Micro-agenten för Azure återställnings tider, inklusive funktioner och fördelar samt länkar till relevanta konfigurations-och referens resurser. 

## <a name="azure-rtos-iot-defender-iot-micro-agent"></a>Azure återställnings tider IoT Defender-IoT-Micro-agent

Defender-IoT-Micro-agent för Azure återställnings tider tillhandahåller en omfattande säkerhetslösning för Azure återställnings tider-enheter som en del av NetX Duo-erbjudandet. I NetX Duo-erbjudandet levereras Azure återställnings tider med Azure IoT Defender-IoT-Micro-agent inbyggt och ger täckning för vanliga hot i real tids operativ system enheter när de har Aktiver ATS. 

Defender-IoT-Micro-agenten för Azure återställnings tider körs i bakgrunden och ger en smidig användar upplevelse, samtidigt som du skickar säkerhets meddelanden med hjälp av varje kunds unika anslutningar till deras IoT Hub. Defender-IoT-Micro-agenten för Azure återställnings tider är aktive rad som standard.  

## <a name="azure-rtos-netx-duo"></a>Azure RTOS NetX Duo

Azure återställnings tider NetX Duo är en avancerad, inbyggd TCP/IP-nätverks stack som utformats specifikt för djupt inbyggda real tids-och IoT-program. Azure återställnings tider NetX Duo är en dubbel IPv4-och IPv6-nätverks stack som ger en omfattande uppsättning protokoll, inklusive säkerhet och moln. Läs mer om [Azure återställnings tider netx Duo](/azure/rtos/netx-duo/) -lösningar.

Modulen erbjuder följande funktioner:

- **Identifiera skadliga nätverks aktiviteter**
- **Enhets beteende bas linjer baserade på anpassade aviseringar**
- **Förbättra enhetens säkerhets hygien**

## <a name="defender-iot-micro-agent-for-azure-rtos-architecture"></a>Defender-IoT-Micro-agent för Azure återställnings tider-arkitektur

Defender-IoT-Micro-agenten för Azure återställnings tider initieras av Azure IoT mellan plattforms plattform och använder IoT Hub klienter för att skicka säkerhetstelemetri till hubben.

:::image type="content" source="media/architecture/security-module-state-diagram.png" alt-text="Azure IoT Defender – IoT-Micro-agent state diagram och informations flöde":::

Defender-IoT-Micro-agenten för Azure återställnings tider övervakar följande enhets aktivitet och information med hjälp av tre insamlare:
- Enhets nätverks aktivitet **TCP**, **UDP** och **ICM**
- System information som **ThreadX** -och **netx Duo** -versioner
- Pulsslagshändelser

Varje insamlare är länkad till en prioritets grupp och varje prioritets grupp har ett eget intervall med möjliga värden **låg**, **medel** och **hög**. Intervallen påverkar det tidsintervall då data samlas in och skickas.

Varje tidsintervall kan konfigureras och IoT-anslutningarna kan aktive ras och inaktive ras för att ytterligare [Anpassa din lösning](how-to-azure-rtos-security-module.md). 

## <a name="supported-security-alerts-and-recommendations"></a>Säkerhets aviseringar och rekommendationer som stöds

Defender-IoT-Micro-agenten för Azure återställnings tider har stöd för vissa säkerhets aviseringar och rekommendationer. Se till att [Granska och anpassa relevanta aviserings-och rekommendations värden](concept-rtos-security-alerts-recommendations.md) för tjänsten när du har slutfört den inledande konfigurationen.

## <a name="ready-to-begin"></a>Är du redo att börja?

Defender-IoT-Micro-agent för Azure återställnings tider tillhandahålls som kostnads fri nedladdning för dina IoT-enheter. Tjänsten Defender för IoT Cloud är tillgänglig med en 30-dagars utvärderings version per Azure-prenumeration. [Ladda ned Defender-IoT-Micro-agenten nu](https://github.com/azure-rtos/azure-iot-preview/releases) och kom igång. 

## <a name="next-steps"></a>Nästa steg

- Kom igång med Defender-IoT-Micro-agent för Azure återställnings tider- [krav och installations program](quickstart-azure-rtos-security-module.md).
- Läs mer om Defender-IoT-Micro-agent för Azure återställnings tider- [säkerhetsaviseringar och rekommendations support](concept-rtos-security-alerts-recommendations.md). 
- Använd [referens-API: et](azure-rtos-security-module-api.md)Defender-IoT-Micro-agent för Azure återställnings tider.