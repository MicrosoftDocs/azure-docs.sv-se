---
title: Undanta en Azure Security Center rekommendation från en resurs, prenumeration, hanterings grupp och säkra Poäng
description: Lär dig hur du skapar regler för att undanta säkerhets rekommendationer från prenumerationer eller hanterings grupper och hindra dem från att påverka dina säkra Poäng
author: memildin
ms.author: memildin
ms.date: 03/11/2021
ms.topic: how-to
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: d3627f6bcda7a18204c24fc2a1347c4a512c5369
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "103149740"
---
# <a name="exempting-resources-and-recommendations-from-your-secure-score"></a>Undanta resurser och rekommendationer från dina säkra Poäng 

En kärn prioritet för varje säkerhets grupp är att säkerställa att analytiker kan fokusera på de uppgifter och incidenter som är viktiga för organisationen. Security Center har många funktioner för att anpassa upplevelsen och se till att dina säkra poäng visar din organisations säkerhets prioriteringar. Alternativet **undanta** är en sådan funktion.

När du undersöker dina säkerhets rekommendationer i Azure Security Center är en av de första delarna av informationen som du granskar listan över berörda resurser.

Ibland visas en resurs som du inte bör ta med. Eller så visas en rekommendation i ett omfång där du känner att den inte tillhör. Resursen kan ha åtgärd ATS av en process som inte spåras av Security Center. Rekommendationen kan vara olämplig för en speciell prenumeration. Eller så kanske din organisation bara har valt att godkänna riskerna relaterade till den aktuella resursen eller rekommendationen.

I sådana fall kan du skapa ett undantag för en rekommendation att:

- **Undanta en resurs** för att se till att den inte visas i listan med felaktiga resurser i framtiden och inte påverkar dina säkra poäng. Resursen visas som ej tillämplig och orsaken visas som "undantagen" med den önskade justering som du väljer.

- **Undanta en prenumeration eller hanterings grupp** för att säkerställa att rekommendationen inte påverkar dina säkra poäng och inte visas för prenumerationen eller hanterings gruppen i framtiden. Detta relaterar till befintliga resurser och alla som du skapar i framtiden. Rekommendationen markeras med den speciella motivering som du väljer för det omfång som du har valt.

## <a name="availability"></a>Tillgänglighet

| Aspekt                          | Information                                                                                                                                                                                                                                                                                                                            |
|---------------------------------|:-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Versions tillstånd:                  | Förhandsgranskning<br>[!INCLUDE [Legalese](../../includes/security-center-preview-legal-text.md)]                                                                                                                                                                                                                                             |
| Priset                        | Det här är en förstklassig Azure policy-funktion som erbjuds för Azure Defender-kunder utan extra kostnad. För andra användare kan avgifter tillkomma i framtiden.                                                                                                                                                                 |
| Nödvändiga roller och behörigheter: | **Prenumerations ägare** eller **princip deltagare** för att skapa ett undantag<br>Om du vill skapa en regel måste du ha behörighet att redigera principer i Azure Policy.<br>Läs mer i [Azure RBAC-behörigheter i Azure policy](../governance/policy/overview.md#azure-rbac-permissions-in-azure-policy).                                            |
| Begränsningar:                    | Det går endast att skapa undantag för rekommendationer som ingår i Security Centers standard initiativ, Azure-säkerhetsbenchmark eller någon av de tillhandahållna myndighets standard initiativen. Rekommendationer som genereras från anpassade initiativ kan inte undantas. Lär dig mer om relationerna mellan [principer, initiativ och rekommendationer](security-policy-concept.md). |
| Moln                         | ![Ja](./media/icons/yes-icon.png) Kommersiella moln<br>![Inga](./media/icons/no-icon.png) National/suverän (US Gov, Kina gov, andra gov)                                                                                                                                                                                         |
|                                 |                                                                                                                                                                                                                                                                                                                                    |

## <a name="define-an-exemption"></a>Definiera ett undantag

För att finjustera de säkerhets rekommendationer som Security Center gör för dina prenumerationer, hanterings grupper eller resurser kan du skapa en undantags regel för att:

- Markera en speciell **rekommendation** eller som "begränsad" eller "risk godkänd". Du kan skapa rekommendations undantag för en prenumeration, flera prenumerationer eller en hel hanterings grupp.
- Markera **en eller flera resurser** som "begränsad" eller "risk accepterad" för en speciell rekommendation.

> [!NOTE]
> Det går endast att skapa undantag för rekommendationer som ingår i Security Centers standard initiativ, Azure-säkerhetsbenchmark eller någon av de tillhandahållna myndighets standard initiativen. Rekommendationer som genereras från anpassade initiativ som är tilldelade till dina prenumerationer kan inte undantas. Lär dig mer om relationerna mellan [principer, initiativ och rekommendationer](security-policy-concept.md).

> [!TIP]
> Du kan också skapa undantag med hjälp av API: et. För ett exempel-JSON, och en förklaring av relevanta strukturer, se [Azure policy undantags struktur](../governance/policy/concepts/exemption-structure.md).

Så här skapar du en undantags regel:

1. Öppna rekommendations informations sidan för den aktuella rekommendationen.

1. I verktygsfältet högst upp på sidan väljer du **undanta**.

    :::image type="content" source="media/exempt-resource/exempting-recommendation.png" alt-text="Skapa en undantags regel för en rekommendation som ska undantas från en prenumeration eller hanterings grupp.":::

1. I fönstret **undantag** :
    1. Välj omfattning för den här undantags regeln:
        - Om du väljer en hanterings grupp undantas rekommendationen från alla prenumerationer i gruppen
        - Om du skapar den här regeln för att undanta en eller flera resurser från rekommendationen väljer du "valda resurser" "och väljer de relevanta i listan

    1. Ange ett namn för den här undantags regeln.
    1. Du kan också ange ett förfallo datum.
    1. Välj kategorin för undantaget:
        - **Löses genom tredje part (minimerat)** – om du använder en tjänst från tredje part som Security Center inte har identifierats. 

            > [!NOTE]
            > När du undantar en rekommendation som minimerad får du inga poäng för dina säkra poäng. Men eftersom punkter inte *tas bort* för de felaktiga resurserna är resultatet att poängen ökar.

        - **Risk som godkänns (avstående)** – om du har bestämt dig för att godkänna risken för att inte begränsa den här rekommendationen
    1. Du kan också ange en beskrivning.
    1. Välj **Skapa**.

    :::image type="content" source="media/exempt-resource/defining-recommendation-exemption.png" alt-text="Steg för att skapa en undantags regel för att undanta en rekommendation från din prenumeration eller hanterings grupp":::

    När undantaget börjar gälla (det kan ta upp till 30 minuter):
    - Rekommendationen eller resurserna påverkar inte dina säkra poäng.
    - Om du har beviljat vissa resurser visas de på fliken **ej tillämpligt** på sidan med rekommendations information.
    - Om du undantar en rekommendation döljs den som standard på Security Center sidan rekommendationer. Detta beror på att standard alternativen i **rekommendations status** filtret på sidan är att undanta **inte tillämpliga** rekommendationer. Samma sak gäller om du undantar alla rekommendationer i en säkerhets kontroll.

        :::image type="content" source="media/exempt-resource/recommendations-filters-hiding-not-applicable.png" alt-text="Standard filter på Azure Security Center sidan rekommendationer Dölj inte tillämpliga rekommendationer och säkerhets kontroller":::

    - Informations remsan överst på rekommendations informations sidan uppdaterar antalet undantagna resurser:
        
        :::image type="content" source="./media/exempt-resource/info-banner.png" alt-text="Antal undantagna resurser":::

1. Om du vill granska dina undantagna resurser öppnar du fliken **ej tillämpligt** :

    :::image type="content" source="./media/exempt-resource/modifying-exemption.png" alt-text="Ändra ett undantag":::

    Orsaken till varje undantag ingår i tabellen (1).

    Om du vill ändra eller ta bort ett undantag väljer du ellips-menyn ("...") som visas (2).

1. Om du vill granska alla undantags regler för din prenumeration väljer du **Visa undantag** från informations remsan:

    > [!IMPORTANT]
    > Om du vill se de angivna undantagen som är relevanta för en rekommendation filtrerar du listan enligt relevant omfattning och rekommendations namn.

    :::image type="content" source="./media/exempt-resource/policy-page-exemption.png" alt-text="Azure Policy sidan undantag":::

    > [!TIP]
    > Du kan också [använda Azure Resource Graph för att hitta rekommendationer med undantag](#find-recommendations-with-exemptions-using-azure-resource-graph).

## <a name="monitor-exemptions-created-in-your-subscriptions"></a>Övervaka undantag som skapats i dina prenumerationer

Som tidigare förklarats på den här sidan, är undantags regler ett kraftfullt verktyg som ger detaljerad kontroll över rekommendationerna som påverkar resurser i dina prenumerationer och hanterings grupper. 

För att hålla reda på hur användarna utnyttjar den här funktionen har vi skapat en mall för Azure Resource Manager (ARM) som distribuerar en Logic app-Spelbok och alla nödvändiga API-anslutningar för att meddela dig när ett undantag har skapats.

- Mer information om Spelbok finns i teknisk Community-bloggen [hur du håller reda på resurs undantag i Azure Security Center](https://techcommunity.microsoft.com/t5/azure-security-center/how-to-keep-track-of-resource-exemptions-in-azure-security/ba-p/1770580)
- ARM-mallen finns i [Azure Security Center GitHub-lagringsplatsen](https://github.com/Azure/Azure-Security-Center/tree/master/Workflow%20automation/Notify-ResourceExemption)
- [Använd den här automatiserade processen](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2FAzure-Security-Center%2Fmaster%2FWorkflow%2520automation%2FNotify-ResourceExemption%2Fazuredeploy.json) för att distribuera alla nödvändiga komponenter


## <a name="find-recommendations-with-exemptions-using-azure-resource-graph"></a>Hitta rekommendationer med undantag med hjälp av Azure Resource Graph

Azure Resource Graph (ARG) ger direkt åtkomst till resursinformation i dina moln miljöer med robusta funktioner för filtrering, gruppering och sortering. Det är ett snabbt och effektivt sätt att fråga information i Azure-prenumerationer program mässigt eller inifrån Azure Portal.

Visa alla rekommendationer som har undantags regler:

1. Öppna **Azure Resource Graph Explorer**.

    :::image type="content" source="./media/security-center-identity-access/opening-resource-graph-explorer.png" alt-text="Startar Azure Resource Graph Explorer * * rekommendations sida" :::

1. Ange följande fråga och välj **Kör fråga**.

    ```kusto
    securityresources
    | where type == "microsoft.security/assessments"
    // Get recommendations in useful format
    | project
    ['TenantID'] = tenantId,
    ['SubscriptionID'] = subscriptionId,
    ['AssessmentID'] = name,
    ['DisplayName'] = properties.displayName,
    ['ResourceType'] = tolower(split(properties.resourceDetails.Id,"/").[7]),
    ['ResourceName'] = tolower(split(properties.resourceDetails.Id,"/").[8]),
    ['ResourceGroup'] = resourceGroup,
    ['ContainsNestedRecom'] = tostring(properties.additionalData.subAssessmentsLink),
    ['StatusCode'] = properties.status.code,
    ['StatusDescription'] = properties.status.description,
    ['PolicyDefID'] = properties.metadata.policyDefinitionId,
    ['Description'] = properties.metadata.description,
    ['RecomType'] = properties.metadata.assessmentType,
    ['Remediation'] = properties.metadata.remediationDescription,
    ['Severity'] = properties.metadata.severity,
    ['Link'] = properties.links.azurePortal
    | where StatusDescription contains "Exempt"    
    ```


Läs mer på följande sidor:
- [Lär dig mer om Azure Resource Graph](../governance/resource-graph/index.yml).
- [Skapa frågor med Azure Resource Graph Explorer](../governance/resource-graph/first-query-portal.md)
- [Kusto Query Language (KQL)](/azure/data-explorer/kusto/query/)





## <a name="faq---exemption-rules"></a>FOS – undantags regler

### <a name="what-happens-when-one-recommendation-is-in-multiple-policy-initiatives"></a>Vad händer om en rekommendation finns i flera princip initiativ?

Ibland visas en säkerhets rekommendation i mer än ett princip initiativ. Om du har flera instanser av samma rekommendation som har tilldelats samma prenumeration, och du skapar ett undantag för rekommendationen, kommer den att påverka alla de initiativ som du har behörighet att redigera. 

Rekommendationen * * * * är till exempel en del av standard policy initiativet som tilldelas till alla Azure-prenumerationer med Azure Security Center. Den också i XXXXX.

Om du försöker skapa ett undantag för den här rekommendationen visas ett av följande två meddelanden:

- Om du har de behörigheter som krävs för att redigera båda initiativen visas:

    *Den här rekommendationen ingår i flera princip initiativ: [initiativ namn avgränsade med kommatecken]. Undantag kommer att skapas på alla.*  

- Om du inte har tillräcklig behörighet för båda initiativen visas det här meddelandet i stället:

    *Du har begränsade behörigheter för att tillämpa undantaget på alla princip initiativ. undantagen skapas endast på initiativen med tillräcklig behörighet.*


## <a name="next-steps"></a>Nästa steg

I den här artikeln har du lärt dig hur du undantar en resurs från en rekommendation så att den inte påverkar dina säkra poäng. Mer information om säkra Poäng finns i:

- [Säkerhetspoäng i Azure Security Center](secure-score-security-controls.md)