---
title: Agent-baserad lösnings arkitektur
description: Lär dig mer om Azure Defender för IoT agent-baserad arkitektur och informations flöde.
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: shhazam-ms
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 1/25/2021
ms.author: shhazam
ms.openlocfilehash: 51b1f639ada01eda06bdf6c938eef7dce2a379fc
ms.sourcegitcommit: f6193c2c6ce3b4db379c3f474fdbb40c6585553b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/08/2021
ms.locfileid: "102448616"
---
# <a name="agent-based-solution-for-device-builders"></a>Agentbaserad lösning för enhetsskapare

I den här artikeln beskrivs den funktionella system arkitekturen hos Defender for IoT agent-baserad lösning. Azure Defender for IoT erbjuder två uppsättningar funktioner som passar din miljös behov, agent lös lösning för organisationer och en agent-baserad lösning för enhets byggare.

## <a name="iot-hub-built-in-security"></a>Inbyggd säkerhet för IoT Hub

Defender for IoT är aktiverat som standard i alla nya IoT Hub som skapas. Defender för IoT tillhandahåller övervakning i real tid, rekommendationer och aviseringar utan att kräva agent installation på några enheter och använder avancerade analyser på loggade IoT Hub-metadata för att analysera och skydda dina fält enheter och IoT-hubbar. 

## <a name="defender-for-iot-micro-agent"></a>Defender för IoT Micro agent 

Defender för IoT Micro agent ger djup säkerhet och insyn i enhetens beteende. samlar in, samlar in och analyserar rå säkerhets händelser från dina enheter. Rå säkerhets händelser kan omfatta IP-anslutningar, process skapande, användar inloggningar och annan säkerhetsrelaterad information. Defender för IoT-enhets agenter hanterar också händelse agg regering för att undvika hög nätverks data flöde. Agenterna är mycket anpassningsbara, så att du kan använda dem för olika uppgifter, t. ex. genom att bara skicka viktig information till det snabbaste service avtalet, eller för att samla in omfattande säkerhets information och kontext i större segment, vilket kan undvika högre tjänste kostnader.

Enhets agenter och andra program använder Azure-funktionen för att **Skicka Security meddelande-SDK** för att skicka säkerhets information till Azure IoT Hub. IoT Hub hämtar den här informationen och vidarebefordrar den till tjänsten Defender för IoT.

När tjänsten Defender for IoT är aktive rad skickar IoT Hub förutom vidarebefordrade data även alla interna data för analys av Defender för IoT. Dessa data omfattar enhets moln åtgärds loggar, enhets identiteter och nav konfiguration. All den här informationen hjälper dig att skapa en pipeline för Defender för IoT Analytics.

Defender för IoT Analytics-pipeline tar också emot andra hot informations strömmar från olika källor i Microsoft-och Microsoft-partner. Pipelinen Defender for IoT hela Analytics fungerar med varje kundkonfiguration som gjorts på tjänsten (till exempel anpassade aviseringar och användning av SDK: n för att skicka Security-meddelanden).

Med Analytics-pipeline kombinerar Defender för IoT alla data strömmar för att generera åtgärds bara rekommendationer och aviseringar. Pipelinen innehåller både anpassade regler som skapats av säkerhets forskare och experter, samt Machine Learning-modeller som söker efter avvikelser från standardenhets beteende och risk analys.

Defender för IoT-rekommendationer och-varningar (analys av pipeline-utdata) skrivs till Log Analytics arbets ytan för varje kund. Inklusive rå händelser på arbets ytan och aviseringar och rekommendationer möjliggör djupgående undersökning och frågor med hjälp av exakt information om de misstänkta aktiviteter som upptäckts.

:::image type="content" source="media/architecture/micro-agent-architecture.png" alt-text="Mikroagentens arkitektur.":::

## <a name="see-also"></a>Se även

[Vanliga frågor och svar om Defender för IoT](resources-frequently-asked-questions.md)

[Systemkrav](quickstart-system-prerequisites.md)
