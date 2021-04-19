---
title: Undanta en Azure Security Center från en resurs, prenumeration, hanteringsgrupp och säkerhetspoäng
description: Lär dig hur du skapar regler för att undanta säkerhetsrekommendationer från prenumerationer eller hanteringsgrupper och förhindra att de påverkar dina säkerhetspoäng
author: memildin
ms.author: memildin
ms.date: 03/11/2021
ms.topic: how-to
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: 13abb35d0fa9ad3ee949b6edf5205de601a02956
ms.sourcegitcommit: 79c9c95e8a267abc677c8f3272cb9d7f9673a3d7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/19/2021
ms.locfileid: "107718565"
---
# <a name="exempting-resources-and-recommendations-from-your-secure-score"></a>Undanta resurser och rekommendationer från din säkerhetspoäng 

En viktig prioritet för varje säkerhetsteam är att säkerställa att analytiker kan fokusera på de uppgifter och incidenter som är viktiga för organisationen. Security Center har många funktioner för att anpassa upplevelsen och se till att säkerhetspoängen återspeglar organisationens säkerhetsprioriteringar. **Undantagsalternativet** är en sådan funktion.

När du undersöker dina säkerhetsrekommendationer i Azure Security Center är en av de första uppgifterna som du granskar listan över berörda resurser.

Ibland visas en resurs som du anser inte ska ingå. Eller så visas en rekommendation i ett omfång där du anser att den inte tillhör. Resursen kan ha åtgärdats av en process som inte spårats av Security Center. Rekommendationen kan vara olämplig för en viss prenumeration. Eller så har din organisation helt enkelt valt att acceptera de risker som är relaterade till den specifika resursen eller rekommendationen.

I sådana fall kan du skapa ett undantag för en rekommendation för att:

- **Undanta en resurs** för att se till att den inte visas med de resurser som inte är felfria i framtiden och påverkar inte dina säkerhetspoäng. Resursen visas som ej tillämplig och orsaken visas som "undantagen" med den specifika motivering som du väljer.

- **Undanta en prenumeration eller hanteringsgrupp** för att säkerställa att rekommendationen inte påverkar dina säkerhetspoäng och inte visas för prenumerationen eller hanteringsgruppen i framtiden. Detta gäller för befintliga resurser och eventuella som du skapar i framtiden. Rekommendationen markeras med den specifika motivering som du väljer för det omfång som du har valt.

## <a name="availability"></a>Tillgänglighet

| Aspekt                          | Information                                                                                                                                                                                                                                                                                                                            |
|---------------------------------|:-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Utgivningstillstånd:                  | Förhandsgranskning<br>[!INCLUDE [Legalese](../../includes/security-center-preview-legal-text.md)]                                                                                                                                                                                                                                             |
| Prissättning:                        | Det här är Azure Policy premiumfunktion som erbjuds för Azure Defender utan extra kostnad. För andra användare kan avgifter tillkomma i framtiden.                                                                                                                                                                 |
| Nödvändiga roller och behörigheter: | **Ägare** eller **Resursprincipdeltagare för** att skapa ett undantag<br>Om du vill skapa en regel behöver du behörighet att redigera principer i Azure Policy.<br>Läs mer i [Azure RBAC-behörigheter i Azure Policy](../governance/policy/overview.md#azure-rbac-permissions-in-azure-policy).                                            |
| Begränsningar:                    | Undantag kan bara skapas för rekommendationer som ingår i Security Center standardinitiativ, [Azure Security Benchmark](https://docs.microsoft.com/security/benchmark/azure/introduction)eller någon av de tillhandahållna regelstandardinitiativen. Rekommendationer som genereras från anpassade initiativ kan inte undantas. Läs mer om relationerna mellan [principer, initiativ och rekommendationer.](security-policy-concept.md) |
| Moln:                         | ![Yes](./media/icons/yes-icon.png) Kommersiella moln<br>![No](./media/icons/no-icon.png) National/Sovereign (US Gov, China Gov, Other Gov)                                                                                                                                                                                         |
|                                 |                                                                                                                                                                                                                                                                                                                                    |

## <a name="define-an-exemption"></a>Definiera ett undantag

Om du vill finjustera de säkerhetsrekommendationer Security Center gör för dina prenumerationer, hanteringsgrupp eller resurser kan du skapa en undantagsregel för att:

- Markera en specifik **rekommendation** eller som "minimerad" eller "accepterad risk". Du kan skapa rekommendationsundantag för en prenumeration, flera prenumerationer eller en hel hanteringsgrupp.
- Markera **en eller flera resurser som** "minimerad" eller "accepterad risk" för en specifik rekommendation.

> [!NOTE]
> Undantag kan bara skapas för rekommendationer som ingår i Security Center standardinitiativ, Azure Security Benchmark eller någon av de tillhandahållna regelstandardinitiativen. Rekommendationer som genereras från anpassade initiativ som tilldelats dina prenumerationer kan inte undantas. Läs mer om relationerna mellan [principer, initiativ och rekommendationer.](security-policy-concept.md)

> [!TIP]
> Du kan också skapa undantag med hjälp av API:et. Ett exempel på JSON och en förklaring av de relevanta strukturerna finns [i Azure Policy undantagsstruktur](../governance/policy/concepts/exemption-structure.md).

Så här skapar du en undantagsregel:

1. Öppna sidan med information om rekommendationer för den specifika rekommendationen.

1. Välj Undanta i verktygsfältet längst upp på **sidan.**

    :::image type="content" source="media/exempt-resource/exempting-recommendation.png" alt-text="Skapa en undantagsregel för en rekommendation som ska undantas från en prenumeration eller hanteringsgrupp.":::

1. I fönstret **Undantag:**
    1. Välj omfånget för den här undantagsregeln:
        - Om du väljer en hanteringsgrupp undantas rekommendationen från alla prenumerationer i gruppen
        - Om du skapar den här regeln för att undanta en eller flera resurser från rekommendationen väljer du "Valda resurser" och väljer relevanta resurser i listan

    1. Ange ett namn för den här undantagsregeln.
    1. Du kan också ange ett förfallodatum.
    1. Välj kategorin för undantaget:
        - **Löst via tredje part (åtgärdat)** – om du använder en tjänst från tredje part som Security Center inte har identifierat. 

            > [!NOTE]
            > När du undantar en rekommendation som har åtgärdats får du inga poäng mot dina säkerhetspoäng. Men eftersom punkter inte tas *bort för* de resurser som inte är felande blir resultatet att poängen ökar.

        - **Accepterad risk (friskrivning)** – om du har valt att acceptera risken för att inte minska den här rekommendationen
    1. Du kan också ange en beskrivning.
    1. Välj **Skapa**.

    :::image type="content" source="media/exempt-resource/defining-recommendation-exemption.png" alt-text="Steg för att skapa en undantagsregel för att undanta en rekommendation från din prenumeration eller hanteringsgrupp":::

    När undantaget tillämpas (det kan ta upp till 30 minuter):
    - Rekommendationen eller resurserna påverkar inte dina säkerhetspoäng.
    - Om du har undantagits från specifika resurser visas de på fliken **Ej tillämpligt** på sidan med rekommendationsinformation.
    - Om du har undantagits från en rekommendation döljs den som standard Security Center sidan med rekommendationer. Det beror på att standardalternativen för filtret **Rekommendationsstatus** på den sidan är att undanta **Ej tillämpliga rekommendationer.** Samma sak gäller om du undantar alla rekommendationer i en säkerhetskontroll.

        :::image type="content" source="media/exempt-resource/recommendations-filters-hiding-not-applicable.png" alt-text="Standardfilter på Azure Security Center rekommendationer döljer de rekommendationer och säkerhetskontroller som inte är tillämpliga":::

    - Informationslisten överst på sidan med rekommendationsinformation uppdaterar antalet undantagna resurser:
        
        :::image type="content" source="./media/exempt-resource/info-banner.png" alt-text="Antal undantagna resurser":::

1. Om du vill granska dina undantagna resurser öppnar du **fliken Ej** tillämpligt:

    :::image type="content" source="./media/exempt-resource/modifying-exemption.png" alt-text="Ändra ett undantag":::

    Orsaken till varje undantag tas med i tabellen (1).

    Om du vill ändra eller ta bort ett undantag väljer du ellipsmenyn ("...") enligt bilden (2).

1. Om du vill granska alla undantagsregler för din prenumeration väljer du **Visa undantag från informationslisten:**

    > [!IMPORTANT]
    > Om du vill se specifika undantag som är relevanta för en rekommendation filtrerar du listan enligt relevant omfång och rekommendationsnamn.

    :::image type="content" source="./media/exempt-resource/policy-page-exemption.png" alt-text="Azure Policy på undantagssidan":::

    > [!TIP]
    > Du kan också [använda Azure Resource Graph för att hitta rekommendationer med undantag.](#find-recommendations-with-exemptions-using-azure-resource-graph)

## <a name="monitor-exemptions-created-in-your-subscriptions"></a>Övervaka undantag som skapats i dina prenumerationer

Som vi nämnde tidigare på den här sidan är undantagsregler ett kraftfullt verktyg som ger detaljerad kontroll över rekommendationerna som påverkar resurser i dina prenumerationer och hanteringsgrupper. 

För att hålla reda på hur användarna använder den här funktionen har vi skapat en Azure Resource Manager-mall (ARM) som distribuerar en Logic App Playbook och alla nödvändiga API-anslutningar för att meddela dig när ett undantag har skapats.

- Mer information om spelboken finns i tech community-blogginlägget How [to keep track of Resource Exemptions in Azure Security Center](https://techcommunity.microsoft.com/t5/azure-security-center/how-to-keep-track-of-resource-exemptions-in-azure-security/ba-p/1770580)
- DU hittar ARM-mallen på [GitHub Azure Security Center databasen](https://github.com/Azure/Azure-Security-Center/tree/master/Workflow%20automation/Notify-ResourceExemption)
- Om du vill distribuera alla nödvändiga komponenter använder [du den här automatiserade processen](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2FAzure-Security-Center%2Fmaster%2FWorkflow%2520automation%2FNotify-ResourceExemption%2Fazuredeploy.json)


## <a name="find-recommendations-with-exemptions-using-azure-resource-graph"></a>Hitta rekommendationer med undantag med hjälp av Azure Resource Graph

Azure Resource Graph (ARG) ger omedelbar åtkomst till resursinformation i dina molnmiljöer med robusta filtrerings-, grupperings- och sorteringsfunktioner. Det är ett snabbt och effektivt sätt att fråga efter information i Azure-prenumerationer programmatiskt eller inifrån Azure Portal.

Så här visar du alla rekommendationer som har undantagsregler:

1. Öppna **Azure Resource Graph Explorer.**

    :::image type="content" source="./media/security-center-identity-access/opening-resource-graph-explorer.png" alt-text="Starta Azure Resource Graph Explorer**-rekommendationssidan" :::

1. Ange följande fråga och välj **Kör fråga.**

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
- [Läs mer om att Azure Resource Graph](../governance/resource-graph/index.yml).
- [Skapa frågor med Azure Resource Graph Explorer](../governance/resource-graph/first-query-portal.md)
- [Kusto Query Language (KQL)](/azure/data-explorer/kusto/query/)





## <a name="faq---exemption-rules"></a>Vanliga frågor och svar – Undantagsregler

### <a name="what-happens-when-one-recommendation-is-in-multiple-policy-initiatives"></a>Vad händer när en rekommendation finns i flera principinitiativ?

Ibland visas en säkerhetsrekommendation i mer än ett principinitiativ. Om du har flera instanser av samma rekommendation tilldelad till samma prenumeration och du skapar ett undantag för rekommendationen, påverkar det alla initiativ som du har behörighet att redigera. 

Rekommendationen *** är till exempel en del av standardprincipinitiativ som tilldelas till alla Azure-prenumerationer av Azure Security Center. Det finns även i XXXXX.

Om du försöker skapa ett undantag för den här rekommendationen visas något av följande två meddelanden:

- Om du har de behörigheter som krävs för att redigera båda initiativen visas:

    *Den här rekommendationen ingår i flera principinitiativ: [initiativnamn avgränsade med kommatecken]. Undantag skapas på alla.*  

- Om du inte har tillräcklig behörighet för båda initiativen visas det här meddelandet i stället:

    *Du har begränsad behörighet att tillämpa undantaget på alla principinitiativ. Undantagen skapas endast på initiativ med tillräcklig behörighet.*


## <a name="next-steps"></a>Nästa steg

I den här artikeln har du lärt dig hur du undantar en resurs från en rekommendation så att den inte påverkar dina säkerhetspoäng. Mer information om säkerhetspoäng finns i:

- [Säkerhetspoäng i Azure Security Center](secure-score-security-controls.md)
