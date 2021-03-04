---
title: Arbeta med säkerhets principer | Microsoft Docs
description: Den här artikeln beskriver hur du arbetar med säkerhets principer i Azure Security Center.
author: memildin
manager: rkarlin
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.date: 01/24/2021
ms.author: memildin
ms.openlocfilehash: 6ecedc20cf6924a82b6b4640d3caa75bc5958de0
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/04/2021
ms.locfileid: "102101332"
---
# <a name="manage-security-policies"></a>Hantera säkerhetsprinciper

Den här artikeln förklarar hur du konfigurerar säkerhets principer och hur du visar dem i Security Center. 

## <a name="who-can-edit-security-policies"></a>Vem kan redigera säkerhets principer?

Du kan redigera säkerhets principer via Azure Policy-portalen via REST API eller med hjälp av Windows PowerShell.

Security Center använder rollbaserad åtkomst kontroll i Azure (Azure RBAC) som innehåller inbyggda roller som du kan tilldela till Azure-användare, grupper och tjänster. När användare öppnar Security Center ser de bara information om de resurser som de har åtkomst till. Det innebär att användare tilldelas rollen som *ägare*, *deltagare* eller *läsare* till resursens prenumeration. Det finns också två olika Security Centers roller:

- **Säkerhets läsare**: har behörighet att Visa Security Center objekt, till exempel rekommendationer, aviseringar, principer och hälsa. Det går inte att göra ändringar.
- **Säkerhets administratör**: har samma visnings rättigheter som *säkerhets läsaren*. Kan också uppdatera säkerhets principen och ignorera aviseringar.

## <a name="manage-your-security-policies"></a>Hantera dina säkerhets principer

Visa dina säkerhetsprinciper i Security Center:

1. I instrument panelen **Security Center** väljer du **säkerhets princip**.

    :::image type="content" source="./media/security-center-policies/security-center-policy-mgt.png" alt-text="Sidan princip hantering":::

   På skärmen **princip hantering** kan du se antalet hanterings grupper, prenumerationer och arbets ytor samt hanterings gruppens struktur.

1. Välj den prenumeration eller hanterings grupp vars principer du vill visa.

1. Sidan säkerhets princip för den prenumerationen eller hanterings gruppen visas. Den visar de tillgängliga och tilldelade principerna.

    :::image type="content" source="./media/tutorial-security-policy/security-policy-page.png" alt-text="Sidan säkerhets princip för Security Center" lightbox="./media/tutorial-security-policy/security-policy-page.png":::

    > [!NOTE]
    > Om det finns en etikett "MG ärvd" tillsammans med standard principen, innebär det att principen har tilldelats till en hanterings grupp och ärvts av den prenumeration som du visar.

1. Välj bland de tillgängliga alternativen på den här sidan:

    1. Om du vill arbeta med bransch standarder väljer du **Lägg till fler standarder**. Mer information finns i [Anpassa uppsättningen med standarder på instrument panelen för kontroll av efterlevnad](update-regulatory-compliance-packages.md).

    1. Om du vill tilldela och hantera anpassade initiativ väljer du **Lägg till anpassade initiativ**. Mer information finns i [använda anpassade säkerhets initiativ och principer](custom-security-policies.md).

    1. Om du vill visa och redigera standard initiativet väljer du **Visa en effektiv princip** och fortsätter enligt beskrivningen nedan. 

        :::image type="content" source="./media/security-center-policies/policy-screen.png" alt-text="Skärmen gällande princip":::

       Den här **säkerhets princip** skärmen visar den åtgärd som vidtas av de principer som har tilldelats den prenumeration eller hanterings grupp du valt.
       
       * Använd länkarna överst för att öppna en princip **tilldelning** som gäller för prenumerationen eller hanterings gruppen. Med dessa länkar kan du komma åt tilldelningen och redigera eller inaktivera principen. Om du till exempel ser att en viss princip tilldelning på ett effektivt sätt nekar Endpoint Protection använder du länken för att redigera eller inaktivera principen.
       
       * I listan över principer kan du se det effektiva tillämpnings programmet för principen i din prenumeration eller hanterings grupp. Inställningarna för varje princip som gäller för omfånget beaktas och det ackumulerade resultatet av de åtgärder som vidtagits av principen visas. Om till exempel en tilldelning av principen är inaktive rad, men i en annan som är inställd på AuditIfNotExist, gäller den kumulativa påverkan AuditIfNotExist. Den mer aktiva effekter har alltid företräde.
       
       * Principens inverkan kan vara: tillägg, granskning, AuditIfNotExists, Deny, DeployIfNotExists, disabled. Mer information om hur effekter tillämpas finns i [Förstå princip effekter](../governance/policy/concepts/effects.md).

       > [!NOTE]
       > När du visar tilldelade principer kan du se flera tilldelningar och du kan se hur varje tilldelning har kon figurer ATS på egen hand.


## <a name="disable-security-policies-and-disable-recommendations"></a>Inaktivera säkerhets principer och inaktivera rekommendationer

När säkerhets initiativet utlöser en rekommendation som är irrelevant för din miljö kan du förhindra att rekommendationen visas igen. Inaktivera en rekommendation genom att inaktivera den princip som genererar rekommendationen.

Rekommendationen du vill inaktivera visas fortfarande om det krävs för en regel standard som du har använt med Security Center reglerna för regelefterlevnad. Även om du har inaktiverat en princip i det inbyggda initiativet, kommer en princip i initiativ Standards initiativet fortfarande att utlösa rekommendationen om det är nödvändigt för efterlevnad. Du kan inte inaktivera principer från myndighets standard initiativ.

Mer information om rekommendationer finns i [hantera säkerhets rekommendationer](security-center-recommendations.md).

1. I Security Center går du till avsnittet **princip & efterlevnad** och väljer **säkerhets princip**.

    :::image type="content" source="./media/tutorial-security-policy/policy-management.png" alt-text="Starta princip hanterings processen i Azure Security Center":::

2. Välj den prenumeration eller hanterings grupp som du vill inaktivera rekommendationen för.

   > [!NOTE]
   > Kom ihåg att en hanteringsgrupp tillämpar sina policyer på prenumerationerna i den. Om du inaktiverar en policy för en prenumeration och prenumerationen tillhör en hanteringsgrupp som fortfarande använder samma policy så fortsätter du att få tillhörande rekommendationer. Policyn tillämpas fortfarande från hanteringsnivån och rekommendationerna fortsätter att genereras.

1. Välj **Visa gällande princip**.

    :::image type="content" source="./media/tutorial-security-policy/view-effective-policy.png" alt-text="Så här öppnar du den gällande principen som har tilldelats din prenumeration":::

1. Välj den tilldelade principen.

   ![Välj princip](./media/tutorial-security-policy/security-policy.png)

1. I avsnittet **parametrar** söker du efter principen som anropar rekommendationen som du vill inaktivera. Välj **inaktive rad** i list rutan.

   ![Inaktivera princip](./media/tutorial-security-policy/disable-policy.png)

1. Välj **Spara**.

   > [!NOTE]
   > Det kan ta upp till 12 timmar innan ändringar av principen inaktive ras.

## <a name="next-steps"></a>Nästa steg
Den här sidan förklaras säkerhets principer. Relaterad information finns på följande sidor:

- [Lär dig hur du ställer in principer med PowerShell](../governance/policy/assign-policy-powershell.md)
- [Lär dig hur du redigerar en säkerhets princip i Azure Policy](../governance/policy/tutorials/create-and-manage.md)
- [Lär dig hur du ställer in en princip över prenumerationer eller på hanterings grupper med hjälp av Azure Policy](../governance/policy/overview.md)
- [Lär dig hur du aktiverar Security Center för alla prenumerationer i en hanterings grupp](onboard-management-group.md)