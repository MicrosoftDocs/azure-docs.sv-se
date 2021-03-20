---
title: 'Snabb start: undersöka säkerhets aviseringar'
description: Förstå, öka detalj nivån och undersök Defender för IoT-säkerhetsaviseringar på dina IoT-enheter.
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/30/2020
ms.author: mlottner
ms.openlocfilehash: 172ae82288c2cb948839b69955b9491715eb4690
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "90947853"
---
# <a name="quickstart-investigate-security-alerts"></a>Snabb start: undersöka säkerhets aviseringar

En schemalagd undersökning och reparation av de aviseringar som utfärdats av Defender för IoT är det bästa sättet att säkerställa efterlevnad och skydd i din IoT-lösning.

I den här snabb starten ska vi utforska den information som finns tillgänglig i varje IoT-säkerhetsavisering och förklara hur du kan öka detalj nivån och använda informationen om varje avisering och relaterad enhet för att svara och åtgärda problemet. 

Nu sätter vi igång. 


## <a name="investigate-new-security-alerts"></a>Undersök nya säkerhets aviseringar

I listan IoT Hub säkerhets avisering visas alla sammanställda säkerhets aviseringar för din IoT Hub. 

1. I Azure Portal öppnar du **IoT Hub** som du vill undersöka för nya aviseringar.
1. I menyn **säkerhet** väljer du **aviseringar**. Alla säkerhets aviseringar för IoT Hub visas och aviseringarna med en **ny** flagga Markera aviseringarna från de senaste 24 timmarna.
:::image type="content" source="media/quickstart/investigate-new-security-alerts.png" alt-text="Undersök nya IoT-säkerhetsaviseringar med hjälp av den nya aviserings flaggan":::
1. Välj och öppna en avisering i listan för att öppna aviserings informationen och öka detalj nivån för aviseringarna. 

## <a name="security-alert-details"></a>Utförlig information om säkerhetsaviseringar

Om du öppnar varje sammanställd avisering visas den detaljerade aviserings beskrivningen, reparations stegen, enhets-ID för varje enhet som utlöste en avisering, samt aviserings allvarlighets grad och direkt gransknings åtkomst med hjälp av Log Analytics. 

1. Välj och öppna eventuella säkerhets aviseringar i listan **IoT Hub**  >  **säkerhets**  >  **aviseringar** . 
1. Granska aviserings **beskrivningen**, **allvarlighets graden**, **källan för identifieringen**, **enhets information** för alla enheter som har utfärdat aviseringen under samlings perioden.
:::image type="content" source="media/quickstart/drill-down-iot-alert-details.png" alt-text="Öka detalj nivån och granska informationen om varje enhet i en sammanställd avisering "::: 
1. När du har granskat aviserings informationen använder du anvisningarna för **manuella åtgärder** för att åtgärda problemet och/eller lösa problemet som orsakade aviseringen. 
:::image type="content" source="media/quickstart/iot-alert-manual-remediation-steps.png" alt-text="Följ de manuella åtgärds stegen för att lösa eller åtgärda dina enhets säkerhets aviseringar":::

1. Om ytterligare undersökning krävs kan **du undersöka aviseringarna i Log Analytics** hjälp av länken. 
:::image type="content" source="media/quickstart/investigate-iot-alert-log-analytics.png" alt-text="Om du vill undersöka en avisering ytterligare använder du länken Undersök med Log Analytics som finns på skärmen":::

## <a name="next-steps"></a>Nästa steg

Fortsätt till nästa artikel om du vill lära dig mer om Defender-aviseringar och möjliga anpassningar...

> [!div class="nextstepaction"]
> [Förstå säkerhets aviseringar i IoT](concept-security-alerts.md)
