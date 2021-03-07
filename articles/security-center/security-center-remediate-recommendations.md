---
title: Åtgärda rekommendationer i Azure Security Center | Microsoft Docs
description: Den här artikeln förklarar hur du svarar på rekommendationer i Azure Security Center för att skydda dina resurser och tillgodose säkerhets principer.
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 03/04/2021
ms.author: memildin
ms.openlocfilehash: f382646c889d004738064cae2d09fd66d897b110
ms.sourcegitcommit: 5bbc00673bd5b86b1ab2b7a31a4b4b066087e8ed
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/07/2021
ms.locfileid: "102438275"
---
# <a name="remediate-recommendations-in-azure-security-center"></a>Reparationsrekommendationer i Azure Security Center

Rekommendationer ger förslag på hur du bättre skyddar resurserna. Du implementerar en rekommendation genom att följa de åtgärds steg som beskrivs i rekommendationen.

## <a name="remediation-steps"></a>Reparations steg <a name="remediation-steps"></a>

När du har granskat alla rekommendationer, Bestäm vilken av dem som ska åtgärdas först. Vi rekommenderar att du prioriterar säkerhets kontrollerna med högsta möjliga möjlighet att öka dina säkra poäng.

1. Välj en rekommendation i listan.

1. Följ anvisningarna i avsnittet **reparations steg** . Varje rekommendation har en egen uppsättning instruktioner. Följande skärm bild visar åtgärds steg för att konfigurera program att bara tillåta trafik över HTTPS.

    :::image type="content" source="./media/security-center-remediate-recommendations/security-center-remediate-recommendation.png" alt-text="Manuella reparations steg för en rekommendation" lightbox="./media/security-center-remediate-recommendations/security-center-remediate-recommendation.png":::

1. När du är klar visas ett meddelande om problemet har lösts.

## <a name="quick-fix-remediation"></a>Snabb korrigerings reparation

Många rekommendationer innehåller ett snabb korrigerings alternativ för att förenkla reparation och förbättra din miljös säkerhet (och öka dina säkra poäng).

Med snabb korrigering kan du snabbt åtgärda en rekommendation om flera resurser.

> [!TIP]
> Snabb korrigerings lösningar är bara tillgängliga för vissa rekommendationer. Du hittar rekommendationerna som har en tillgänglig snabb korrigering med hjälp av filtret **svars åtgärder** i listan över rekommendationer:
> 
> :::image type="content" source="media/security-center-remediate-recommendations/quick-fix-filter.png" alt-text="Använd filtren ovanför listan rekommendationer för att hitta rekommendationer som har alternativet snabb korrigering":::

Så här implementerar du en snabb lösning:

1. I listan över rekommendationer som har **snabb korrigering!** etikett väljer du en rekommendation.

    [![Välj snabb korrigering!](media/security-center-remediate-recommendations/security-center-quick-fix-select.png)](media/security-center-remediate-recommendations/security-center-quick-fix-select.png#lightbox)

1. På fliken **felaktiga resurser** väljer du de resurser som du vill implementera rekommendationen för och väljer **åtgärda**.

    > [!NOTE]
    > Några av de listade resurserna kan vara inaktiverade eftersom du inte har rätt behörighet för att ändra dem.

1. Läs korrigerings informationen och konsekvenserna i bekräftelse rutan.

    ![Snabb korrigering](./media/security-center-remediate-recommendations/security-center-quick-fix-view.png)

    > [!NOTE]
    > Konsekvenserna visas i den grå rutan i fönstret **Åtgärds resurser** som öppnas när du klickar på **åtgärda**. De listar vilka ändringar som sker när du fortsätter med snabb korrigerings reparationen.

1. Infoga relevanta parametrar vid behov och godkänn reparationen.

    > [!NOTE]
    > Det kan ta flera minuter efter att reparationen har slutförts för att se resurserna på fliken **felfria resurser** . Om du vill visa reparations åtgärderna kontrollerar du [aktivitets loggen](#activity-log).

1. När åtgärden har slutförts visas ett meddelande om att reparationen lyckades.

## <a name="quick-fix-remediation-logging-in-the-activity-log"></a>Snabb korrigering av reparations loggning i aktivitets loggen <a name="activity-log"></a>

Reparations åtgärden använder en mall för distribution av mallar eller REST-PATCH-API-anrop för att tillämpa konfigurationen på resursen. De här åtgärderna loggas i [aktivitets loggen i Azure](../azure-resource-manager/management/view-activity-logs.md).


## <a name="next-steps"></a>Nästa steg

I det här dokumentet visade du hur du kan åtgärda rekommendationerna i Security Center. Mer information om Security Center finns på följande sidor:

* [Ange säkerhets principer i Azure Security Center](tutorial-security-policy.md) – lär dig hur du konfigurerar säkerhets principer för dina Azure-prenumerationer och resurs grupper
* [Vad är säkerhets principer, initiativ och rekommendationer?](security-policy-concept.md)