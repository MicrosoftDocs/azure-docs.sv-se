---
title: Övervaka identitet och åtkomst i Azure Security Center | Microsoft Docs
description: Lär dig hur du använder identitets- och åtkomstfunktionerna i Azure Security Center för att övervaka användarnas åtkomstaktiviteter och identitetsrelaterade problem.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 9f04e730-4cfa-4078-8eec-905a443133da
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/08/2020
ms.author: memildin
ms.openlocfilehash: 3c0dd2b4e7e48eeb76d82c26eb52b89b61e9f668
ms.sourcegitcommit: 431bf5709b433bb12ab1f2e591f1f61f6d87f66c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/12/2021
ms.locfileid: "98134117"
---
# <a name="monitor-identity-and-access"></a>Övervaka identitet och åtkomst

Säkerhetsperimetern har utvecklats från en nätverksgräns till en identitetsgräns. Med den här utvecklingen är säkerheten mindre säker på ditt nätverk och mer om att hantera säkerheten för dina appar, data och användare.

Genom att övervaka aktiviteternas och konfigurations inställningarna som är relaterade till identiteten, kan du vidta proaktiva åtgärder innan en incident inträffar, eller återaktiverade åtgärder för att stoppa försöks angrepp.

## <a name="what-identity-and-access-safeguards-does-security-center-provide"></a>Vilka identitets-och åtkomst skydd ger Security Center? 

Azure Security Center har två dedikerade säkerhets kontroller för att se till att du uppfyller organisationens identitets-och säkerhets krav: 

 - **Hantera åtkomst och behörigheter** – vi rekommenderar att du använder den [lägsta behörighets modellen för åtkomst](/windows-server/identity/ad-ds/plan/security-best-practices/implementing-least-privilege-administrative-models) och ser till att du endast beviljar användare den åtkomst som krävs för att utföra sina jobb. Den här kontrollen innehåller även rekommendationer för att implementera [rollbaserad åtkomst kontroll i Azure (Azure RBAC)](../role-based-access-control/overview.md) för att kontrol lera åtkomsten till dina resurser.
 
 - **Aktivera MFA** – med [MFA](https://www.microsoft.com/security/business/identity/mfa) aktiverat är dina konton säkrare och användare kan fortfarande autentisera till nästan alla program med enkel inloggning.

### <a name="example-recommendations-for-identity-and-access"></a>Exempel rekommendationer för identitet och åtkomst

Exempel på rekommendationer som du kan se i de här två kontrollerna på sidan med **rekommendationer** för Security Center:

- MFA ska vara aktiverat på konton med ägar behörigheter för din prenumeration
- Högst 3 ägare bör anges för din prenumeration
- Externa konton med Läs behörighet bör tas bort från din prenumeration
- Föråldrade konton bör tas bort från din prenumeration (inaktuella konton är konton som inte längre behövs och som blockeras från att logga in med Azure Active Directory)

> [!TIP]
> Mer information om dessa rekommendationer och de andra som du kan se i dessa kontroller finns i [identitets-och åtkomst rekommendationer](recommendations-reference.md#recs-identityandaccess).

### <a name="limitations"></a>Begränsningar

Det finns vissa begränsningar i Security Center identitets-och åtkomst skydd:

- Identitets rekommendationer är inte tillgängliga för prenumerationer med fler än 600 konton. I sådana fall visas dessa rekommendationer under "ej tillgängliga utvärderingar".
- Identitets rekommendationer är inte tillgängliga för CSP-partnerns administratörs agenter.
- Identitets rekommendationer identifierar inte konton som hanteras med ett system med Privileged Identity Management (PIM). Om du använder ett PIM-verktyg kan du se felaktiga resultat i kontrollen **Hantera åtkomst och behörigheter** .

## <a name="multi-factor-authentication-mfa-and-azure-active-directory"></a>Multi-Factor Authentication (MFA) och Azure Active Directory 

För att aktivera MFA krävs [Azure Active Directory (AD) klient behörighet](../active-directory/roles/permissions-reference.md).

- Om du har en Premium-version av AD aktiverar du MFA med [villkorlig åtkomst](../active-directory/conditional-access/concept-conditional-access-policy-common.md).
- Om du använder en kostnads fri version av AD, aktiverar du **säkerhets inställningarna** enligt beskrivningen i [Azure Active Directory-dokumentationen](../active-directory/fundamentals/concept-fundamentals-security-defaults.md).

## <a name="identify-accounts-without-multi-factor-authentication-mfa-enabled"></a>Identifiera konton utan Multi-Factor Authentication (MFA) aktiverat

Använd följande Azure Resource Graph-fråga om du vill se vilka konton som inte har MFA aktiverat. Frågan returnerar alla resurser som inte är felfria – konton – i rekommendationen måste MFA vara aktiverat på konton med ägar behörigheter för din prenumeration. 

1. Öppna **Azure Resource Graph Explorer**.

    :::image type="content" source="./media/security-center-identity-access/opening-resource-graph-explorer.png" alt-text="Startar Azure Resource Graph Explorer * * rekommendations sida" :::

1. Ange följande fråga och välj **Kör fråga**.

    ```kusto
    securityresources
     | where type == "microsoft.security/assessments"
     | where properties.displayName == "MFA should be enabled on accounts with owner permissions on your subscription"
     | where properties.status.code == "Unhealthy"
    ```

1. `additionalData`Egenskapen visar en lista över konto objekt-ID: n för konton som inte har MFA-tvång. 

    > [!NOTE]
    > Kontona visas som objekt-ID i stället för konto namn för att skydda konto innehavarens sekretess.

> [!TIP]
> Du kan också använda Security Center REST API metod [bedömningar – Hämta](/rest/api/securitycenter/assessments/get).


## <a name="next-steps"></a>Nästa steg
Mer information om rekommendationer som gäller för andra typer av Azure-resurser finns i följande artikel:

- [Skydda nätverket i Azure Security Center](security-center-network-recommendations.md)