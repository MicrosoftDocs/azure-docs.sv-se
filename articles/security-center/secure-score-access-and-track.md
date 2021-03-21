---
title: Spåra dina säkra poäng i Azure Security Center
description: Lär dig mer om flera sätt att komma åt och spåra dina säkra poäng i Azure Security Center.
author: memildin
ms.author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: how-to
ms.date: 02/25/2021
ms.openlocfilehash: 5efc48d348e9cfceab590bcfba8c621e7721376f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102107963"
---
# <a name="access-and-track-your-secure-score"></a>Få åtkomst till och spåra dina säkra Poäng

Du hittar den övergripande säkra poängen, samt poängen per prenumeration, via Azure Portal eller program mässigt enligt beskrivningen i följande avsnitt:

> [!TIP]
> En detaljerad förklaring av hur dina resultat beräknas finns i [beräkningar – förstå poängen](secure-score-security-controls.md#calculations---understanding-your-score).

## <a name="get-your-secure-score-from-the-portal"></a>Få dina säkra Poäng från portalen

Security Center visar ditt resultat på en framträdande plats i portalen: det är den första huvud panelen på översikts sidan för Security Center. När du väljer den här panelen går du till sidan dedikerade säker poäng där du ser poängen uppdelad efter prenumeration. Välj en enskild prenumeration om du vill se en detaljerad lista över prioriterade rekommendationer och den potentiella påverkan som åtgärdas i prenumerationens resultat. 

I Sammanfattning visas dina säkra poäng på följande platser i Security Center Portal sidor.

- I en panel på Security Center **Översikt** (huvud instrument panel):

    :::image type="content" source="./media/secure-score-security-controls/score-on-main-dashboard.png" alt-text="Den säkra poängen på Security Centers instrument panel":::

- På sidan dedikerad **säker Poäng** kan du se säkerhets poängen för din prenumeration och dina hanterings grupper:

    :::image type="content" source="./media/secure-score-security-controls/score-on-dedicated-dashboard.png" alt-text="Säkerhets poängen för prenumerationer på Security Center säkra resultat Sidan":::

    :::image type="content" source="./media/secure-score-security-controls/secure-score-management-groups.png" alt-text="Säkerhets poängen för hanterings grupper på Security Center sidan säker Poäng":::

    > [!NOTE]
    > Alla hanterings grupper som du inte har tillräckliga behörigheter för visar deras Poäng som "begränsad". 

- Överst på sidan **rekommendationer** :

    :::image type="content" source="./media/secure-score-security-controls/score-on-recommendations-page.png" alt-text="Sidan säker poäng på Security Centerens rekommendationer":::

## <a name="get-your-secure-score-from-the-rest-api"></a>Få dina säkra Poäng från REST API

Du kan komma åt dina poäng via Secure score-API: et. API-metoderna ger flexibiliteten att fråga data och skapa en egen rapporterings mekanism för dina säkra poäng över tid. Du kan till exempel använda [Secure Scores-API: et](/rest/api/securitycenter/securescores) för att hämta poängen för en speciell prenumeration. Dessutom kan du använda [API: et för säker Poäng](/rest/api/securitycenter/securescorecontrols) för att visa en lista över säkerhets kontrollerna och de aktuella poängen för dina prenumerationer.

![Hämta en enskild säker Poäng via API: et](media/secure-score-security-controls/single-secure-score-via-api.png)

Exempel på verktyg som byggts ovanpå Secure score-API: et finns i avsnittet [Secure Scores i vår GitHub-community](https://github.com/Azure/Azure-Security-Center/tree/master/Secure%20Score). 

## <a name="get-your-secure-score-from-azure-resource-graph"></a>Få dina säkra Poäng från Azure Resource Graph

Azure Resource Graph ger direkt åtkomst till resursinformation i dina moln miljöer med robusta funktioner för filtrering, gruppering och sortering. Det är ett snabbt och effektivt sätt att fråga information i Azure-prenumerationer program mässigt eller inifrån Azure Portal. [Lär dig mer om Azure Resource Graph](../governance/resource-graph/index.yml).

För att komma åt säkra Poäng för flera prenumerationer med Azure Resource Graph:

1. Öppna **Azure Resource Graph Explorer** från Azure Portal.

    :::image type="content" source="./media/security-center-identity-access/opening-resource-graph-explorer.png" alt-text="Startar Azure Resource Graph Explorer * * rekommendations sida" :::

1. Ange din Kusto-fråga (med hjälp av exemplen nedan för vägledning).

    - Den här frågan returnerar prenumerations-ID, den aktuella poängen i punkter och i procent och det högsta antalet poäng för prenumerationen. 

        ```kusto
        SecurityResources 
        | where type == 'microsoft.security/securescores' 
        | extend current = properties.score.current, max = todouble(properties.score.max)
        | project subscriptionId, current, max, percentage = ((current / max)*100)
        ```

    - Den här frågan returnerar status för alla säkerhets kontroller. För varje kontroll får du antalet resurser som inte är felfria, den aktuella poängen och den högsta poängen. 

        ```kusto
        SecurityResources 
        | where type == 'microsoft.security/securescores/securescorecontrols'
        | extend SecureControl = properties.displayName, unhealthy = properties.unhealthyResourceCount, currentscore = properties.score.current, maxscore = properties.score.max
        | project SecureControl , unhealthy, currentscore, maxscore
        ```

1. Välj **Kör fråga**.


## <a name="tracking-your-secure-score-over-time"></a>Spåra dina säkra poäng över tid

### <a name="secure-score-over-time-report-in-workbooks-page"></a>Rapporten säker poäng över tid i arbets boks sidor

Security Center sidan arbets böcker innehåller en färdig rapport som visuellt spårar resultaten av dina prenumerationer, säkerhets kontroller och mycket annat. Läs mer i [skapa omfattande, interaktiva rapporter för Security Center data](custom-dashboards-azure-workbooks.md).

:::image type="content" source="media/custom-dashboards-azure-workbooks/secure-score-over-time-snip.png" alt-text="En del av rapporten säkra poäng över tid från Azure Security Center s galleri för arbets böcker":::

### <a name="power-bi-pro-dashboards"></a>Power BI Pro instrument paneler

Om du är en Power BI användare med ett Pro-konto kan du använda instrument panelen **säker poäng över tid** Power BI för att spåra dina säkra poäng över tid och undersöka eventuella ändringar.

> [!TIP]
> Du hittar den här instrument panelen, samt andra verktyg för att arbeta med program mässigt med säkra poäng, i det dedikerade området i Azure Security Center Community på GitHub: https://github.com/Azure/Azure-Security-Center/tree/master/Secure%20Score

Instrument panelen innehåller följande två rapporter som hjälper dig att analysera din säkerhets status:

- **Resurs Sammanfattning** – tillhandahåller sammanfattande data om dina resursers hälsa.
- **Översikt över säkra Poäng** – tillhandahåller sammanfattade data om Poäng förloppet. Använd diagrammet "säkra poäng över tid per prenumeration" för att visa ändringar i poängen. Om du ser en dramatisk förändring i resultatet, kontrollerar du tabellen "identifierade ändringar som kan påverka din säkra poäng" för möjliga ändringar som kan ha orsakat ändringen. Den här tabellen visar borttagna resurser, nyligen distribuerade resurser eller resurser som deras säkerhets status ändrats för någon av rekommendationerna.

:::image type="content" source="./media/secure-score-security-controls/power-bi-secure-score-dashboard.png" alt-text="De valfria säkra poängen över tid Power BI instrument panelen för att spåra dina säkra poäng över tid och undersöka ändringar":::


## <a name="next-steps"></a>Nästa steg

Den här artikeln beskriver hur du kommer åt och spårar dina säkra poäng. Information om relaterade material finns i följande artiklar:

- [Lär dig mer om de olika elementen i en rekommendation](security-center-recommendations.md)
- [Lär dig hur du åtgärdar rekommendationer](security-center-remediate-recommendations.md)
- [Visa GitHub-baserade verktyg för att arbeta program mässigt med säkra Poäng](https://github.com/Azure/Azure-Security-Center/tree/master/Secure%20Score)