---
title: Säkerhets modul för Azure återställnings tider-översikt
description: Lär dig mer om säkerhetsmodulen för Azure återställnings tider support och implementering som en del av Azure Defender for IoT.
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
ms.date: 09/07/2020
ms.author: mlottner
ms.openlocfilehash: 9950f3727aac365205e979d9590edacebd32f1fc
ms.sourcegitcommit: 8be279f92d5c07a37adfe766dc40648c673d8aa8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/31/2020
ms.locfileid: "97832751"
---
# <a name="overview-defender-for-iot-security-module-for-azure-rtos-preview"></a>Översikt: Defender för IoT Security Module för Azure återställnings tider (för hands version)

Azure Defender för IoT-säkerhetsmodulen innehåller en omfattande säkerhetslösning för enheter som använder Azure-återställnings tider. Den ger täckning för vanliga hot och potentiella skadliga aktiviteter på återställnings tider-enheter (Real Time Opera ting system). Azure återställnings tider levereras nu med Azure IoT Security-modulen inbyggd.

:::image type="content" source="./media/architecture/azure-rtos-security-monitoring.png" alt-text="Visualisering av Defender för IoT Azure-återställnings tider.":::


Säkerhetsmodulen för Azure återställnings tider erbjuder följande funktioner:

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

Säkerhetsmodulen för Azure återställnings tider tillhandahålls som kostnads fri nedladdning för dina enheter. Tjänsten Defender för IoT Cloud är tillgänglig med en 30-dagars utvärderings version per Azure-prenumeration. Kom igång genom att ladda ned [Security-modulen för Azure återställnings tider](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/defender-for-iot/iot-security-azure-rtos.md). 

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du lärt dig om säkerhetsmodulen för Azure återställnings tider. Mer information om säkerhetsmodulen och hur du kommer igång finns i följande artiklar:

- [Koncept i Azure återställnings tider IoT Security Module](concept-rtos-security-module.md)
- [Snabb start: Azure återställnings tider IoT-säkerhetsmodul](quickstart-azure-rtos-security-module.md)
