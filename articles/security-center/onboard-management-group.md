---
title: Publicera en hanterings grupp till Azure Security Center
description: Lär dig hur du använder en angiven Azure Policy definition för att aktivera Azure Security Center för alla prenumerationer i en hanterings grupp.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.service: security-center
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/10/2020
ms.author: memildin
ms.openlocfilehash: 31472b0a0eedee03d5cfcb6ff47a97033a8c78e0
ms.sourcegitcommit: 95c2cbdd2582fa81d0bfe55edd32778ed31e0fe8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/26/2021
ms.locfileid: "98796072"
---
# <a name="enable-security-center-on-all-subscriptions-in-a-management-group"></a>Aktivera Security Center för alla prenumerationer i en hanterings grupp

Du kan använda Azure Policy för att aktivera Azure Security Center på alla Azure-prenumerationer i samma hanterings grupp (MG). Detta är mer praktiskt än att komma åt dem individuellt från portalen och fungerar även om prenumerationerna tillhör olika ägare. 

För att publicera en hanterings grupp och alla dess prenumerationer:

1. Som en användare med behörigheten **säkerhets administratör** öppnar du Azure policy och söker efter definitionen **Aktivera Azure Security Center i din prenumeration**.

    :::image type="content" source="./media/security-center-get-started/enable-security-center-policy.png" alt-text="Azure Policy definition aktivera Azure Security Center på din prenumeration":::

1. Välj **tilldela** och se till att du ställer in omfång på nivån mg.

    :::image type="content" source="./media/security-center-get-started/assign-policy.png" alt-text="Tilldela definitions Azure Security Center för din prenumeration":::

    > [!TIP]
    > Förutom omfånget finns det inga parametrar som krävs.

1. Välj **skapa en reparations uppgift** för att se till att alla befintliga prenumerationer som inte har Security Center aktive rad registreras.

    :::image type="content" source="./media/security-center-get-started/remediation-task.png" alt-text="Skapa en reparations uppgift för Azure Policy definition aktivera Azure Security Center på din prenumeration":::

1. När definitionen är tilldelad kommer den att:

    1. Identifiera alla prenumerationer i MG som ännu inte har registrerats med Security Center.
    1. Markera dessa prenumerationer som "ej kompatibla".
    1. Markera som "kompatibla" alla registrerade prenumerationer (oavsett om de har Azure Defender på eller av).

    Reparations uppgiften aktiverar sedan Security Center, kostnads fritt, för de icke-kompatibla prenumerationerna.

> [!IMPORTANT]
> Princip definitionen aktiverar endast Security Center på **befintliga** prenumerationer. Om du vill registrera nya prenumerationer öppnar du fliken efterlevnad, väljer relevanta icke-kompatibla prenumerationer och skapar en reparations uppgift. Upprepa det här steget när du har en eller flera nya prenumerationer som du vill övervaka med Security Center.

## <a name="optional-modifications"></a>Valfria ändringar

Det finns flera olika sätt du kan välja att ändra Azure Policy definition: 

- **Definiera kompatibilitet annorlunda** – den angivna principen klassificerar alla prenumerationer i mg som ännu inte har registrerats med Security Center som "icke-kompatibel". Du kan välja att ställa in den på alla prenumerationer utan Azure Defender.

    Den angivna definitionen definierar *någon* av pris inställningarna nedan som kompatibel. Innebär att en prenumeration som är "standard" eller "Free" är kompatibel.

    > [!TIP]
    > När en Azure Defender-plan är aktive rad beskrivs den som i "standard"-inställningen. När den är inaktive rad är den "ledig". [Läs mer om Azure Defender-planer](security-center-pricing.md).

    ```
    "existenceCondition": {
        "anyof": [
            {
                "field": "microsoft.security/pricings/pricingTier",
                "equals": "standard"
            },
            {
                "field": "microsoft.security/pricings/pricingTier",
                "equals": "free"
            }
        ]
    },
    ```

    Om du ändrar det till följande klassificeras bara de prenumerationer som är inställda på standard som kompatibla:

    ```
    "existenceCondition": {
          {
            "field": "microsoft.security/pricings/pricingTier",
            "equals": "standard"
          },
    },
    ```

- **Definiera vissa Azure Defender-planer som ska gälla när du aktiverar Security Center** – den angivna principen gör det möjligt Security Center utan någon av de valfria Azure Defender-planerna. Du kan välja att aktivera en eller flera av dem.

    Den angivna definitionens `deployment` avsnitt har en-parameter `pricingTier` . Som standard är detta inställt på `free` , men du kan ändra det. 


## <a name="next-steps"></a>Nästa steg:

Nu när du har publicerat en hel hanterings grupp aktiverar du avancerade skydd för Azure Defender. 

> [!div class="nextstepaction"]
> [Aktivera Azure Defender](security-center-pricing.md#enable-azure-defender)
