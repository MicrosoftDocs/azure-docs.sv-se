---
title: Arbeta med säkerhets principer | Microsoft Docs
description: Den här artikeln beskriver hur du arbetar med säkerhets principer i Azure Security Center.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 2d248817-ae97-4c10-8f5d-5c207a8019ea
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/24/2021
ms.author: memildin
ms.openlocfilehash: 7ea871428fffe685cc9d43dd698bfefb8d8a511a
ms.sourcegitcommit: 5cdd0b378d6377b98af71ec8e886098a504f7c33
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/25/2021
ms.locfileid: "98756686"
---
# <a name="manage-security-policies"></a>Hantera säkerhetsprinciper

Den här artikeln förklarar hur du konfigurerar säkerhets principer och hur du visar dem i Security Center. 

## <a name="introduction-to-security-policies"></a>Introduktion till säkerhets principer

En säkerhets princip definierar den önskade konfigurationen av dina arbets belastningar och hjälper dig att se till att du följer säkerhets kraven för ditt företag eller din regulator.

Azure Security Center gör säkerhets rekommendationerna baserade på dina valda principer. Security Center principer baseras på princip initiativ som skapats i Azure Policy. Du kan använda [Azure policy](../governance/policy/overview.md) för att hantera principer och ange principer över hanterings grupper och över flera prenumerationer.

Security Center erbjuder följande alternativ för att arbeta med säkerhets principer:

* **Visa och redigera den inbyggda standard principen** – när du aktiverar Security Center tilldelas initiativet med namnet "Azure Security benchmark" automatiskt till alla Security Center registrerade prenumerationer. Du kan anpassa det här initiativet genom att aktivera eller inaktivera enskilda principer i den. Se listan över [inbyggda säkerhets principer](./policy-reference.md) för att förstå alternativen som är tillgängliga direkt.

* **Lägg till egna anpassade principer** – om du vill anpassa de säkerhets initiativ som tillämpas på din prenumeration kan du göra det i Security Center. Du får sedan rekommendationer om datorerna inte följer de principer som du skapar. Anvisningar om hur du skapar och tilldelar anpassade principer finns i [använda anpassade säkerhets principer](custom-security-policies.md).

* **Lägg till regler för efterlevnadsprinciper** -Security Centers instrument panelen för kontroll av efterlevnad visar status för alla utvärderingar i din miljö i samband med en viss standard eller regel (till exempel Azure CIS, NIST SP 800-53 R4, Swift CSP CSCF-v2020). Mer information finns i [förbättra din regelefterlevnad](security-center-compliance-dashboard.md).


## <a name="manage-your-security-policies"></a>Hantera dina säkerhets principer

Visa dina säkerhetsprinciper i Security Center:

1. I instrument panelen **Security Center** väljer du **säkerhets princip**.

    :::image type="content" source="./media/security-center-policies/security-center-policy-mgt.png" alt-text="Sidan princip hantering":::

   På skärmen **princip hantering** kan du se antalet hanterings grupper, prenumerationer och arbets ytor samt hanterings gruppens struktur.

1. Välj den prenumeration eller hanterings grupp vars principer du vill visa.

1. Sidan säkerhets princip för den prenumerationen eller hanterings gruppen visas. Den visar de tillgängliga och tilldelade principerna.

   ![princip sida](./media/tutorial-security-policy/security-policy-page.png)

    > [!NOTE]
    > Om det finns en etikett "MG ärvd" tillsammans med standard principen, innebär det att principen har tilldelats till en hanterings grupp och ärvts av den prenumeration som du visar.


1. Välj bland de tillgängliga alternativen på den här sidan:

    1. Om du vill arbeta med bransch principer väljer du **Lägg till fler standarder**. Mer information finns i [Uppdatera till dynamiska Compliance-paket](update-regulatory-compliance-packages.md).

    1. Om du vill tilldela och hantera anpassade initiativ väljer du **Lägg till anpassade initiativ**. Mer information finns i [använda anpassade säkerhets principer](custom-security-policies.md).

    1. Om du vill visa och redigera standard principen väljer du **Visa gällande princip** och fortsätter enligt beskrivningen nedan. 

        :::image type="content" source="./media/security-center-policies/policy-screen.png" alt-text="Skärmen gällande princip":::

       Den här **säkerhets princip** skärmen visar den åtgärd som vidtas av de principer som har tilldelats den prenumeration eller hanterings grupp du valt.
       
       * Använd länkarna överst för att öppna en princip **tilldelning** som gäller för prenumerationen eller hanterings gruppen. Med dessa länkar kan du komma åt tilldelningen och redigera eller inaktivera principen. Om du till exempel ser att en viss princip tilldelning på ett effektivt sätt nekar Endpoint Protection använder du länken för att redigera eller inaktivera principen.
       
       * I listan över principer kan du se det effektiva tillämpnings programmet för principen i din prenumeration eller hanterings grupp. Inställningarna för varje princip som gäller för omfånget beaktas och det ackumulerade resultatet av de åtgärder som vidtagits av principen visas. Om till exempel en tilldelning av principen är inaktive rad, men i en annan som är inställd på AuditIfNotExist, gäller den kumulativa påverkan AuditIfNotExist. Den mer aktiva effekter har alltid företräde.
       
       * Principens inverkan kan vara: tillägg, granskning, AuditIfNotExists, Deny, DeployIfNotExists, disabled. Mer information om hur effekter tillämpas finns i [Förstå princip effekter](../governance/policy/concepts/effects.md).

       > [!NOTE]
       > När du visar tilldelade principer kan du se flera tilldelningar och du kan se hur varje tilldelning har kon figurer ATS på egen hand.


## <a name="who-can-edit-security-policies"></a>Vem kan redigera säkerhets principer?

Du kan redigera säkerhets principer via Azure Policy-portalen via REST API eller med hjälp av Windows PowerShell.

Security Center använder rollbaserad åtkomst kontroll i Azure (Azure RBAC) som innehåller inbyggda roller som du kan tilldela till Azure-användare, grupper och tjänster. När användare öppnar Security Center ser de bara information om de resurser som de har åtkomst till. Det innebär att användare tilldelas rollen som *ägare*, *deltagare* eller *läsare* till resursens prenumeration. Det finns också två olika Security Centers roller:

- **Säkerhets läsare**: har behörighet att Visa Security Center objekt, till exempel rekommendationer, aviseringar, principer och hälsa. Det går inte att göra ändringar.
- **Säkerhets administratör**: har samma visnings rättigheter som *säkerhets läsaren*. Kan också uppdatera säkerhets principen och ignorera aviseringar.


## <a name="disable-security-policies-and-disable-recommendations"></a>Inaktivera säkerhets principer och inaktivera rekommendationer

När säkerhets initiativet utlöser en rekommendation som är irrelevant för din miljö kan du förhindra att rekommendationen visas igen. Inaktivera en rekommendation genom att inaktivera den princip som genererar rekommendationen.

Rekommendationen du vill inaktivera visas fortfarande om det krävs för en regel standard som du har använt med Security Center reglerna för regelefterlevnad. Även om du har inaktiverat en princip i det inbyggda initiativet, kommer en princip i initiativ Standards initiativet fortfarande att utlösa rekommendationen om det är nödvändigt för efterlevnad. Du kan inte inaktivera principer från myndighets standard initiativ.

Mer information om rekommendationer finns i [hantera säkerhets rekommendationer](security-center-recommendations.md).

1. I Security Center går du till avsnittet **princip & efterlevnad** och väljer **säkerhets princip**.

   ![princip hantering](./media/tutorial-security-policy/policy-management.png)

2. Välj den prenumeration eller hanterings grupp som du vill inaktivera rekommendationen för.

   > [!NOTE]
   > Kom ihåg att en hanteringsgrupp tillämpar sina policyer på prenumerationerna i den. Om du inaktiverar en policy för en prenumeration och prenumerationen tillhör en hanteringsgrupp som fortfarande använder samma policy så fortsätter du att få tillhörande rekommendationer. Policyn tillämpas fortfarande från hanteringsnivån och rekommendationerna fortsätter att genereras.

1. Välj **Visa gällande princip**.

   ![Visa princip](./media/tutorial-security-policy/view-effective-policy.png)

1. Välj den tilldelade principen.

   ![Välj princip](./media/tutorial-security-policy/security-policy.png)

1. I avsnittet **parametrar** söker du efter principen som anropar rekommendationen som du vill inaktivera. Välj **inaktive rad** i list rutan.

   ![Inaktivera princip](./media/tutorial-security-policy/disable-policy.png)

1. Välj **Spara**.

   > [!NOTE]
   > Det kan ta upp till 12 timmar innan ändringar av principen inaktive ras.



## <a name="next-steps"></a>Nästa steg
I den här artikeln förklaras säkerhets principer. Relaterad information finns i följande artiklar:

- [Lär dig hur du ställer in principer med PowerShell](../governance/policy/assign-policy-powershell.md) - 
- [Lär dig hur du redigerar en säkerhets princip i Azure Policy](../governance/policy/tutorials/create-and-manage.md) - 
- [Lär dig hur du ställer in en princip över prenumerationer eller på hanterings grupper med Azure policy](../governance/policy/overview.md).
- [Lär dig hur du aktiverar Security Center för alla prenumerationer i en hanterings grupp](onboard-management-group.md)