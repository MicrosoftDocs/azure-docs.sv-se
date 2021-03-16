---
title: Defender-IoT-Micro-agent för Azure återställnings tider-översikt
description: Läs mer om Defender-IoT-Micro-agenten för Azure återställnings tider support och implementering som en del av Azure Defender for IoT.
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
ms.date: 01/14/2021
ms.author: shhazam
ms.openlocfilehash: ae1ae941dcb1af73286a4865089b1be227c484fc
ms.sourcegitcommit: 4bda786435578ec7d6d94c72ca8642ce47ac628a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/16/2021
ms.locfileid: "103496056"
---
# <a name="overview-defender-for-iot-defender-iot-micro-agent-for-azure-rtos-preview"></a>Översikt: Defender för IoT Defender-IoT-Micro-agent för Azure återställnings tider (för hands version)

Azure Defender for IoT Micro-modulen innehåller en omfattande säkerhetslösning för enheter som använder Azure återställnings tider. Den ger täckning för vanliga hot och potentiella skadliga aktiviteter på återställnings tider-enheter (Real Time Opera ting system). Azure återställnings tider levereras nu med Azure IoT Defender-IoT-Micro-agent inbyggt.

:::image type="content" source="./media/architecture/azure-rtos-security-monitoring.png" alt-text="Visualisering av Defender för IoT Azure-återställnings tider.":::


Micro-modulen för Azure återställnings tider erbjuder följande funktioner:

- Identifiering av skadlig nätverks aktivitet
- Anpassad avisering-baserad enhets beteende bas linje
- Förbättrad enhets säkerhets hygien

## <a name="detect-malicious-network-activities"></a>Identifiera skadliga nätverks aktiviteter

Inkommande och utgående nätverks aktivitet för varje enhet övervakas. Protokoll som stöds är TCP, UDP och ICMP på IPv4 och IPv6. Defender för IoT kontrollerar var och en av dessa nätverks aktiviteter mot Microsoft Threat Intelligence-flödet. Flödet uppdateras i real tid med miljon tals unika hot indikatorer som samlas in över hela världen.

## <a name="device-behavior-baselining-based-on-custom-alerts"></a>Enhets beteende bas linje baserat på anpassade aviseringar

Bas linje tillåter klustring av enheter till säkerhets grupper och definierar förväntat beteende för varje grupp. Eftersom IoT-enheter vanligt vis är utformade för att fungera i väldefinierade och begränsade scenarier, är det enkelt att skapa en bas linje som definierar deras förväntade beteende genom att använda en uppsättning parametrar. Eventuella avvikelser från bas linjen utlöser en avisering.

## <a name="improve-your-device-security-hygiene"></a>Förbättra din enhets säkerhets hygien

Genom att använda den rekommenderade Infrastructure Defender för IoT får du kunskap och insikter om problem i miljön som påverkar och skadar position på dina enheter. En svag IoT-enhets säkerhets position kan ge potentiella attacker att lyckas om den lämnas oförändrad. Säkerhet mäts alltid av den svagaste länken i en organisation.

## <a name="get-started-protecting-azure-rtos-devices"></a>Kom igång med att skydda Azure återställnings tider-enheter

Defender-IoT-Micro-agent för Azure återställnings tider tillhandahålls som kostnads fri nedladdning för dina enheter. Tjänsten Defender för IoT Cloud är tillgänglig med en 30-dagars utvärderings version per Azure-prenumeration. Kom igång genom att hämta [Defender-IoT-Micro-agenten för Azure återställnings tider](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/defender-for-iot/iot-security-azure-rtos.md). 

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du lärt dig mer om Defender-IoT-Micro-agenten för Azure återställnings tider. Mer information om Defender-IoT-Micro-agenten och kom igång finns i följande artiklar:

- [Azure återställnings tider IoT Defender-IoT-Micro-agent-koncept](concept-rtos-security-module.md)
- [Snabb start: Azure återställnings tider IoT Defender-IoT-Micro-agent](quickstart-azure-rtos-security-module.md)
