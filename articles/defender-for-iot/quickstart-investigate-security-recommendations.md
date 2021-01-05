---
title: 'Snabb start: Undersök säkerhets rekommendationer'
description: Undersök säkerhets rekommendationer med tjänsten Defender for IoT.
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
ms.date: 09/09/2020
ms.author: mlottner
ms.openlocfilehash: 66b5694823096f0747ea099b631a53198a819816
ms.sourcegitcommit: 8be279f92d5c07a37adfe766dc40648c673d8aa8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/31/2020
ms.locfileid: "97835048"
---
# <a name="quickstart-investigate-security-recommendations"></a>Snabb start: Undersök säkerhets rekommendationer


Analys av analyser och förbättringar av rekommendationer från Defender för IoT är det bästa sättet att förbättra säkerheten position och minska angrepps ytan i IoT-lösningen.

I den här snabb starten ska vi utforska den information som är tillgänglig i varje IoT-säkerhetsrekommendation och förklara hur du kan öka detalj nivån och använda informationen för varje rekommendation och relaterade enheter för att minska risken.

Nu sätter vi igång.

## <a name="investigate-new-recommendations"></a>Undersök nya rekommendationer

I listan med IoT Hub rekommendationer visas alla sammanställda säkerhets rekommendationer för din IoT Hub.

1.  I Azure Portal öppnar du **IoT Hub** som du vill undersöka för nya rekommendationer.

1.  Från menyn **säkerhet** väljer du **rekommendationer**. Alla säkerhets rekommendationer för IoT Hub visas och rekommendationerna med en **ny** flagga Markera rekommendationerna från de senaste 24 timmarna. 

    :::image type="content" source="media/quickstart/investigate-security-recommendations-expanded.png#lightbox" alt-text="Undersök säkerhets rekommendationer med ASC för IoT] (Media/snabb start/investigate-security-recommendations-inline.png)":::


1.  Välj och öppna alla rekommendationer i listan för att öppna rekommendations informationen och gå nedåt till de olika uppgifterna.

## <a name="security-recommendation-details"></a>Information om säkerhets rekommendationer

Öppna varje sammanställd rekommendation för att visa detaljerad rekommendations beskrivning, reparations steg, enhets-ID för varje enhet som utlöste en rekommendation. Den visar också rekommendations allvarlighets grad och direkt gransknings åtkomst med hjälp av Log Analytics.

1.  Välj och öppna säkerhets rekommendationer från listan **IoT Hub** \> **säkerhets** \> **rekommendationer** .

1.  Läs rekommendations **beskrivningen**, **allvarlighets graden**, **enhets informationen** för alla enheter som utfärdade den här rekommendationen under samlings perioden. 

1.  När du har granskat rekommendations information kan du använda **stegen för manuella åtgärder** för att åtgärda problemet och lösa problemet som orsakade rekommendationen. 

    [ :::image type="content" source="media/quickstart/remediate-security-recommendations-expanded.png#lightbox" alt-text="Åtgärda säkerhets rekommendationer med ASC för IoT] (Media/snabb start/remediate-security-recommendations-inline.png)":::


1.  Utforska rekommendations informationen för en speciell enhet genom att välja önskad enhet på sidan öka detalj nivån.

    [ :::image type="content" source="media/quickstart/explore-security-recommendation-detail-expanded.png#lightbox" alt-text="Undersök vissa säkerhets rekommendationer för en enhet med ASC för IoT] (Media/snabb start/explore-security-recommendation-detail-inline.png)":::


1.  Om ytterligare undersökning krävs bör **du undersöka rekommendationen i Log Analytics** med hjälp av länken. 


## <a name="next-steps"></a>Nästa steg

Gå vidare till nästa artikel om du vill lära dig hur du skapar anpassade aviseringar...

> [!div class="nextstepaction"]
> [Skapa anpassade aviseringar](quickstart-create-custom-alerts.md)
