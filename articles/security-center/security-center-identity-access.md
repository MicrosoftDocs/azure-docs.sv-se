---
title: Azure Security Center säkerhets rekommendationer för MFA
description: Lär dig hur du upprätthåller Multi-Factor Authentication för dina Azure-prenumerationer med hjälp av Azure Security Center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 03/10/2021
ms.author: memildin
ms.openlocfilehash: 9af4f225b1b9ca5e8023a8d5b4bb7607762e4447
ms.sourcegitcommit: b572ce40f979ebfb75e1039b95cea7fce1a83452
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/11/2021
ms.locfileid: "102631905"
---
# <a name="manage-multi-factor-authentication-mfa-enforcement-on-your-subscriptions"></a>Hantera Multi-Factor Authentication (MFA)-tvång i dina prenumerationer

Om du bara använder lösen ord för att autentisera dina användare lämnar du en angrepps vektor öppen. Användarna använder ofta svaga lösen ord eller återanvänder dem för flera tjänster. När [MFA](https://www.microsoft.com/security/business/identity/mfa) är aktiverat är dina konton säkrare och användare kan fortfarande autentisera till nästan alla program med enkel inloggning (SSO).

Det finns flera sätt att aktivera MFA för dina Azure Active Directory-användare (AD) baserat på de licenser som organisationen äger. Den här sidan innehåller information om var och en i kontexten för Azure Security Center.


## <a name="mfa-and-security-center"></a>MFA och Security Center 

Security Center sätter ett högt värde på MFA. Säkerhets kontrollen som bidrar mest till dina säkra poäng är **att aktivera MFA**. 

Rekommendationerna i Aktivera MFA-kontrollen garanterar att du uppfyller rekommenderade metoder för användare av dina prenumerationer:

- MFA ska vara aktiverat på konton med ägar behörigheter för din prenumeration
- MFA ska vara aktiverat på konton med skrivbehörigheter för din prenumeration

Det finns tre sätt att aktivera MFA och vara kompatibelt med de två rekommendationerna i Security Center: säkerhets inställningar, tilldelning per användare, princip för villkorlig åtkomst (CA). Vart och ett av dessa alternativ beskrivs nedan.

### <a name="free-option---security-defaults"></a>Kostnads fritt alternativ-säkerhets inställningar
Om du använder den kostnads fria versionen av Azure AD ska du använda [säkerhets inställningar](../active-directory/fundamentals/concept-fundamentals-security-defaults.md) för att aktivera Multi-Factor Authentication på din klient.

### <a name="mfa-for-microsoft-365-business-e3-or-e5-customers"></a>MFA för Microsoft 365 Business-, E3-eller E5-kunder
Kunder med Microsoft 365 kan använda **tilldelning per användare**. I det här scenariot är Azure AD MFA antingen aktive rad eller inaktive rad för alla användare, för alla inloggnings händelser. Det finns ingen möjlighet att aktivera Multi-Factor Authentication för en delmängd av användare, eller under vissa scenarier, och hantering sker via Office 365-portalen.

### <a name="mfa-for-azure-ad-premium-customers"></a>MFA för Azure AD Premium kunder
Uppgradera till Azure AD Premium P1 eller P2 för princip alternativ för **villkorlig åtkomst (ca)** för en bättre användar upplevelse. Om du vill konfigurera en CA-princip behöver du [Azure Active Directory (AD) klient behörighet](../active-directory/roles/permissions-reference.md).

Din CA-princip måste:
- tillämpa MFA
- ta med ID för Microsoft Azure Management app (797f4846-BA00-4fd7-ba43-dac1f8f63013) eller alla appar
- undanta inte ID för appen för Microsoft Azure hantering

**Azure AD Premium P1** -kunder kan använda Azure AD ca för att uppmana användarna att använda Multi-Factor Authentication under vissa scenarier eller händelser så att de passar dina affärs behov. Andra licenser som innehåller den här funktionen: Enterprise Mobility + Security E3, Microsoft 365 F1 och Microsoft 365 E3.

**Azure AD Premium P2** ger de starkaste säkerhetsfunktionerna och en bättre användar upplevelse. Den här licensen lägger till [riskfylld villkorlig åtkomst](../active-directory/conditional-access/howto-conditional-access-policy-risk.md) till Azure AD Premium P1-funktioner. Riskfylld CA anpassar sig till användarnas mönster och minimerar Multi-Factor Authentication-prompter. Andra licenser som innehåller den här funktionen: Enterprise Mobility + Security E5 eller Microsoft 365 E5.

Läs mer i [dokumentationen för villkorlig åtkomst för Azure](../active-directory/conditional-access/overview.md).

## <a name="identify-accounts-without-multi-factor-authentication-mfa-enabled"></a>Identifiera konton utan Multi-Factor Authentication (MFA) aktiverat

Du kan visa listan över användar konton utan MFA aktiverat från någon av sidan med information om Security Center rekommendationer eller med Azure Resource Graph.

### <a name="view-the-accounts-without-mfa-enabled-in-the-azure-portal"></a>Visa kontona utan MFA aktiverat i Azure Portal
På sidan rekommendations information väljer du en prenumeration från listan över **felaktiga resurser** eller väljer **vidta åtgärd** så visas listan.

### <a name="view-the-accounts-without-mfa-enabled-using-azure-resource-graph"></a>Visa konton utan MFA aktiverat med Azure Resource Graph
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


## <a name="faq---mfa-in-security-center"></a>Vanliga frågor och svar – MFA i Security Center

- [Vi använder redan CA-principen för att genomdriva MFA. Varför får vi fortfarande Security Center rekommendationer?](#were-already-using-ca-policy-to-enforce-mfa-why-do-we-still-get-the-security-center-recommendations)
- [Vi använder ett MFA-verktyg från tredje part för att genomdriva MFA. Varför får vi fortfarande Security Center rekommendationer?](#were-using-a-third-party-mfa-tool-to-enforce-mfa-why-do-we-still-get-the-security-center-recommendations)
- [Varför visar Security Center användar konton utan behörigheter för prenumerationen som "kräver MFA"?](#why-does-security-center-show-user-accounts-without-permissions-on-the-subscription-as-requiring-mfa)
- [Vi tillämpar MFA med PIM. Varför visas PIM-konton som inkompatibla?](#were-enforcing-mfa-with-pim-why-are-pim-accounts-shown-as-noncompliant)
- [Kan jag undanta eller ignorera några av kontona?](#can-i-exempt-or-dismiss-some-of-the-accounts)
- [Finns det några begränsningar för Security Center identitets-och åtkomst skydd?](#are-there-any-limitations-to-security-centers-identity-and-access-protections)

### <a name="were-already-using-ca-policy-to-enforce-mfa-why-do-we-still-get-the-security-center-recommendations"></a>Vi använder redan CA-principen för att genomdriva MFA. Varför får vi fortfarande Security Center rekommendationer?
Du kan undersöka varför rekommendationerna fortfarande genereras genom att kontrol lera följande konfigurations alternativ i din princip för MFA-certifikat utfärdare:

- Du har inkluderat kontona i avsnittet **användare** i din MFA ca-princip (eller någon av grupperna i avsnittet **grupper** )
- Azure Management app-ID: t (797f4846-BA00-4fd7-ba43-dac1f8f63013) eller alla appar ingår i avsnittet **appar** i din MFA ca-princip
- ID: t för Azure Management app är inte uteslutet i **Apps** -avsnittet i din MFA ca-princip

### <a name="were-using-a-third-party-mfa-tool-to-enforce-mfa-why-do-we-still-get-the-security-center-recommendations"></a>Vi använder ett MFA-verktyg från tredje part för att genomdriva MFA. Varför får vi fortfarande Security Center rekommendationer?
Security Centers MFA-rekommendationer stöder inte MFA-verktyg från tredje part (till exempel DUO).

Om rekommendationerna inte är irrelevanta för din organisation bör du överväga att markera dem som "minimerade" enligt beskrivningen i [undanta resurser och rekommendationer från dina säkra Poäng](exempt-resource.md). Du kan också [inaktivera en rekommendation](tutorial-security-policy.md#disable-security-policies-and-disable-recommendations).

### <a name="why-does-security-center-show-user-accounts-without-permissions-on-the-subscription-as-requiring-mfa"></a>Varför visar Security Center användar konton utan behörigheter för prenumerationen som "kräver MFA"?
Security Center är MFA-rekommendationerna finns i [Azure RBAC](../role-based-access-control/role-definitions-list.md) -roller och rollen administratörer i den [klassiska Azure-prenumerationen](../role-based-access-control/classic-administrators.md) . Kontrollera att inga av kontona har sådana roller.

### <a name="were-enforcing-mfa-with-pim-why-are-pim-accounts-shown-as-noncompliant"></a>Vi tillämpar MFA med PIM. Varför visas PIM-konton som inkompatibla?
Security Centers MFA-rekommendationer för närvarande inte stöder PIM-konton. Du kan lägga till dessa konton i en CA-princip i avsnittet Användare/Grupper.

### <a name="can-i-exempt-or-dismiss-some-of-the-accounts"></a>Kan jag undanta eller ignorera några av kontona?
Möjligheten att undanta vissa konton som inte använder MFA stöds inte för närvarande.  

### <a name="are-there-any-limitations-to-security-centers-identity-and-access-protections"></a>Finns det några begränsningar för Security Center identitets-och åtkomst skydd?
Det finns vissa begränsningar i Security Center identitets-och åtkomst skydd:

- Identitets rekommendationer är inte tillgängliga för prenumerationer med fler än 600 konton. I sådana fall visas dessa rekommendationer under "ej tillgängliga utvärderingar".
- Identitets rekommendationer är inte tillgängliga för CSP-partnerns administratörs agenter.
- Identitets rekommendationer identifierar inte konton som hanteras med ett system med Privileged Identity Management (PIM). Om du använder ett PIM-verktyg kan du se felaktiga resultat i kontrollen **Hantera åtkomst och behörigheter** .


## <a name="next-steps"></a>Nästa steg
Mer information om rekommendationer som gäller för andra typer av Azure-resurser finns i följande artikel:

- [Skydda nätverket i Azure Security Center](security-center-network-recommendations.md)