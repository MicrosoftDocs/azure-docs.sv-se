---
title: Säkerhetsrekommendationer i Azure Security Center
description: Det här dokumentet vägleder dig genom hur rekommendationer i Azure Security Center hjälper dig att skydda dina Azure-resurser och hålla dem kompatibla med säkerhets principer.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 86c50c9f-eb6b-4d97-acb3-6d599c06133e
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/24/2021
ms.author: memildin
ms.openlocfilehash: dbda0aecdadd81da0f7681a5fc9b140157d5e8f3
ms.sourcegitcommit: 5cdd0b378d6377b98af71ec8e886098a504f7c33
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/25/2021
ms.locfileid: "98756817"
---
# <a name="security-recommendations-in-azure-security-center"></a>Säkerhetsrekommendationer i Azure Security Center 

I det här avsnittet beskrivs hur du visar och förstår rekommendationerna i Azure Security Center som hjälper dig att skydda dina Azure-resurser.


## <a name="what-are-security-recommendations"></a>Vad är säkerhets rekommendationer?

Security Center analyserar regelbundet säkerhets status för dina Azure-resurser för att identifiera potentiella säkerhets risker. Därefter får du rekommendationer om hur du åtgärdar problemen.

Rekommendationer är åtgärder som du kan vidta för att skydda och skärp dina resurser. 

Varje rekommendation ger dig följande:

- En kort beskrivning av problemet
- De åtgärder som vidtas för att genomföra rekommendationen
- De berörda resurserna

## <a name="how-does-microsoft-decide-what-needs-securing-and-hardening"></a>Hur avgör Microsoft vad som behöver säkras och skärpas?

Security Centers rekommendationer baseras på Azures säkerhets benchmark. 

Azures säkerhets prestanda är Microsofts-skapade, Azure-/regionsspecifika uppsättning rikt linjer för säkerhets-och efterlevnads metod tips baserade på vanliga ramverk för efterlevnad. Detta respekterade riktmärken bygger på kontrollerna från [Center for Internet Security (CIS)](https://www.cisecurity.org/benchmark/azure/) och [National Institute of Standards and Technology (NIST)](https://www.nist.gov/) med fokus på Cloud-inriktad säkerhet.

Läs mer om [Azure Security Benchmark](../security/benchmarks/introduction.md).

## <a name="monitor-recommendations"></a>Övervaka rekommendationer <a name="monitor-recommendations"></a>

Security Center analyserar dina resursers säkerhets tillstånd för att identifiera potentiella sårbarheter. 

1. Från Security Center menyn öppnar du sidan **rekommendationer** för att se de rekommendationer som gäller för din miljö. Rekommendationerna är grupperade i säkerhets kontroller.

    :::image type="content" source="./media/security-center-recommendations/view-recommendations.png" alt-text="Rekommendationer grupperade efter säkerhets kontroll" lightbox="./media/security-center-recommendations/view-recommendations.png":::

1. Om du vill hitta rekommendationer för resurs typ, allvarlighets grad, miljö eller andra kriterier som är viktiga för dig använder du de valfria filtren ovanför listan med rekommendationer.

    :::image type="content" source="media/security-center-recommendations/recommendation-list-filters.png" alt-text="Filter för att förfina listan med Azure Security Center rekommendationer":::

1. Expandera en kontroll och välj en rekommendation för att Visa rekommendations informations sidan.

    :::image type="content" source="./media/security-center-recommendations/recommendation-details-page.png" alt-text="Sidan rekommendations information." lightbox="./media/security-center-recommendations/recommendation-details-page.png":::

    Sidan innehåller:

    1. **Tillämpa** och **neka** knappar på rekommendationer som stöds (se [förhindra felaktig konfiguration med tvinga/neka-rekommendationer](prevent-misconfigurations.md))
    1. **Allvarlighets grad**
    1. **Aktualitets intervall**  (i förekommande fall) 
    1. **Beskrivning** – en kort beskrivning av problemet
    1. **Reparations steg** – en beskrivning av de manuella steg som krävs för att åtgärda säkerhets problemet på de berörda resurserna. För rekommendationer med snabb korrigering kan du välja **Visa reparations logik** innan du använder den föreslagna korrigeringen för dina resurser. 
    1. **Resurser som påverkas** – dina resurser grupperas i flikar:
        - **Felfria resurser** – relevanta resurser som antingen inte påverkas eller som du redan har åtgärdat problemet på.
        - Resurser som inte är **felfria** – resurser som fortfarande påverkas av det identifierade problemet.
        - **Ej tillämpliga resurser** – resurser för vilka rekommendationen inte kan ge ett definitivt svar. Fliken ej tillämpligt innehåller även orsaker för varje resurs. 

            :::image type="content" source="./media/security-center-recommendations/recommendations-not-applicable-reasons.png" alt-text="Inte tillämpliga resurser på grund av orsaker.":::
    1. Åtgärds knappar för att reparera rekommendationen eller utlösa en Logic app.

## <a name="preview-recommendations"></a>För hands versions rekommendationer

Rekommendationer som har flaggats som **förhands granskning** ingår inte i beräkningarna av dina säkra poäng.

De bör fortfarande åtgärdas när så är möjligt, så att när förhands gransknings perioden är slut bidrar de till dina poäng.

Ett exempel på en förhands gransknings rekommendation:

:::image type="content" source="./media/secure-score-security-controls/example-of-preview-recommendation.png" alt-text="Rekommendation med förhands gransknings flaggan":::
 
## <a name="next-steps"></a>Nästa steg

I det här dokumentet har du lanserat säkerhets rekommendationer i Security Center. För relaterad information:

- [Åtgärda rekommendationer](security-center-remediate-recommendations.md)– lär dig hur du konfigurerar säkerhets principer för dina Azure-prenumerationer och resurs grupper.
- [Förhindra felaktig konfiguration med tvinga/neka-rekommendationer](prevent-misconfigurations.md).
- [Automatisera svar på Security Center-utlösare](workflow-automation.md)– automatisera svar på rekommendationer
- [Undanta en resurs från en rekommendation](exempt-resource.md)
- [Säkerhetsrekommendationer – en referensguide](recommendations-reference.md)