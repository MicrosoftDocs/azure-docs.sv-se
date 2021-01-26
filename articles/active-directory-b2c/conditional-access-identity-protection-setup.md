---
title: Konfigurera identitetsskydd och villkorsstyrd åtkomst i Azure AD B2C
description: Lär dig hur du konfigurerar identitets skydd och villkorlig åtkomst för dig Azure AD B2C klient organisationen för att Visa riskfyllda inloggningar och andra risk händelser och skapa principer baserat på risk identifieringar.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: overview
ms.date: 09/01/2020
ms.author: mimart
author: msmimart
manager: celested
ms.collection: M365-identity-device-management
ms.openlocfilehash: 654206bccd25bf09fcdc5c3e7ee72ba97c75af2a
ms.sourcegitcommit: a055089dd6195fde2555b27a84ae052b668a18c7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/26/2021
ms.locfileid: "98785489"
---
# <a name="set-up-identity-protection-and-conditional-access-in-azure-ad-b2c"></a>Konfigurera identitetsskydd och villkorsstyrd åtkomst i Azure AD B2C

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

Identitets skydd ger kontinuerlig identifiering av risker för din Azure AD B2C klient. Om din Azure AD B2C klient pris nivå är Premium P2 kan du Visa detaljerade risk händelser för identitets skydd i Azure Portal. Du kan också använda principer för [villkorlig åtkomst](../active-directory/conditional-access/overview.md) baserat på dessa risk identifieringar för att fastställa åtgärder och tillämpa organisations principer.

## <a name="prerequisites"></a>Krav

- Din Azure AD B2C klient måste vara [länkad till en Azure AD-prenumeration](billing.md#link-an-azure-ad-b2c-tenant-to-a-subscription).
- Azure AD B2C Premium P2 krävs för att använda inloggnings-och användar riskbaserade villkorlig åtkomst. Om det behövs [ändrar du Azure AD B2C pris nivå till Premium P2](./billing.md). 
- Du behöver ett konto som har tilldelats rollen som global administratör eller rollen säkerhets administratör för att kunna hantera identitets skydd och villkorlig åtkomst i B2C-klienten.
- Om du vill använda dessa funktioner i din klient organisation måste du först växla till pris nivån Azure AD B2C Premium P2.

## <a name="set-up-identity-protection"></a>Konfigurera identitets skydd

Identity Protection är aktiverat som standard. För att kunna visa risk händelser för identitets skydd i din Azure AD B2C-klient, länka du bara din Azure AD B2C-klient till en Azure AD-prenumeration och väljer pris nivån Azure AD B2C Premium P2. Du kan visa detaljerade risk händelse rapporter i Azure Portal.

### <a name="supported-identity-protection-risk-detections"></a>Identifieringar av identifierade identitets skydds risker

Följande identifieringar för risker stöds för närvarande för Azure AD B2C:  

|Typ av risk identifiering  |Beskrivning  |
|---------|---------|
| Ovanlig resa     | Logga in från en ovanlig-plats baserat på användarens senaste inloggningar.        |
|Anonym IP-adress     | Logga in från en anonym IP-adress (t. ex. Tor webbläsare, Anonymizer VPN).        |
|Länkad IP-adress för skadlig kod     | Logga in från en länkad IP-adress med skadlig kod.         |
|Obekanta inloggningsegenskaper     | Logga in med egenskaper som vi inte har sett nyligen för den angivna användaren.        |
|Administratören bekräftade att användaren har komprometterats    | En administratör har angett att en användare har komprometterats.             |
|Lösen ords sprayning     | Logga in via ett angrepp vid lösen ords spridning.      |
|Azure AD Threat Intelligence     | Microsofts interna och externa hot informations källor har identifierat ett känt angrepps mönster.        |

## <a name="view-risk-events-for-your-azure-ad-b2c-tenant"></a>Visa risk händelser för din Azure AD B2C-klient

1. Logga in på [Azure-portalen](https://portal.azure.com/).

1. Välj ikonen **katalog + prenumeration** i portalens verktygsfält och välj sedan den katalog som innehåller Azure AD B2C klienten.

1. I Azure Portal söker du efter och väljer **Azure AD B2C**.

1. Under **säkerhet** väljer du **riskfyllda användare (förhands granskning)**.

   ![Riskfyllda användare](media/conditional-access-identity-protection-setup/risky-users.png)

1. Under **säkerhet** väljer du **risk identifieringar (för hands version)**.

   ![Riskidentifieringar](media/conditional-access-identity-protection-setup/risk-detections.png)

## <a name="add-a-conditional-access-policy"></a>Lägg till en princip för villkorlig åtkomst 

Om du vill lägga till en princip för villkorlig åtkomst baserat på identifiering av identitets skydds risker kontrollerar du att säkerhets inställningarna är inaktiverade för din Azure AD B2C-klient och skapar sedan principer för villkorlig åtkomst.

### <a name="to-disable-security-defaults"></a>Så här inaktiverar du säkerhets inställningar

1. Logga in på [Azure-portalen](https://portal.azure.com/).

2. Välj ikonen **katalog + prenumeration** i portalens verktygsfält och välj sedan den katalog som innehåller Azure AD B2C klienten.

3. I Azure Portal söker du efter och väljer **Azure Active Directory**.

4. Välj **Egenskaper** och välj sedan **hantera säkerhets inställningar**.

   ![Inaktivera säkerhets inställningarna](media/conditional-access-identity-protection-setup/disable-security-defaults.png)

5. Under aktivera säkerhets inställningar väljer du Nej. 

   ![Ställ in aktivera säkerhets standardvärden växla till Nej](media/conditional-access-identity-protection-setup/enable-security-defaults-toggle.png)

### <a name="to-create-a-conditional-access-policy"></a>Så här skapar du en princip för villkorlig åtkomst

1. Logga in på [Azure-portalen](https://portal.azure.com/).

1. Välj ikonen **katalog + prenumeration** i portalens verktygsfält och välj sedan den katalog som innehåller Azure AD B2C klienten.

1. I Azure Portal söker du efter och väljer **Azure AD B2C**.

1. Under **säkerhet** väljer du **villkorlig åtkomst (för hands version)**. Sidan **principer för villkorlig åtkomst** öppnas. 

1. Välj **ny princip** och följ dokumentationen för villkorlig åtkomst för Azure AD för att skapa en ny princip. För riskfyllda principer måste du konfigurera separata principer baserat på [användar risk](../active-directory/conditional-access/howto-conditional-access-policy-risk-user.md#enable-with-conditional-access-policy) eller [inloggnings risker](../active-directory/conditional-access/howto-conditional-access-policy-risk.md#enable-with-conditional-access-policy) beroende på vilken typ av risk du vill använda som ett villkor. Vi rekommenderar inte att du använder båda risk typerna i en enda princip.

   > [!IMPORTANT]
   > När du väljer de användare som du vill tillämpa principen på, ska du inte välja enbart **alla användare** eller så kan du förhindra att du loggar in.

## <a name="test-the-conditional-access-policy"></a>Testa principen för villkorlig åtkomst

1. Skapa en princip för villkorlig åtkomst som anges ovan med följande inställningar:
   
   - För **användare och grupper** väljer du test användaren. Markera inte **alla användare** eller blockera dig själv från att logga in.
   - För **molnappar eller åtgärder** väljer du **Välj appar** och väljer sedan ditt förlitande parts program.
   - För villkor väljer du **inloggnings risk** , **hög**, **medel** och **låg** risk nivå.
   - För **beviljande** väljer du **blockera åtkomst**.

      ![Välj blockera åtkomst](media/conditional-access-identity-protection-setup/test-conditional-access-policy.png)

1. Aktivera din test princip för villkorlig åtkomst genom att välja **skapa**.

1. Simulera en riskfylld inloggning genom att använda [Tor-webbläsaren](https://www.torproject.org/download/). 

1. I jwt.ms-kodad token för inloggning med försök bör du se att inloggningen blockerades:

   ![Testa en blockerad inloggning](media/conditional-access-identity-protection-setup/test-blocked-sign-in.png)

## <a name="review-conditional-access-outcomes-in-the-audit-report"></a>Granska resultat från villkorlig åtkomst i gransknings rapporten

Granska resultatet av en händelse för villkorlig åtkomst:

1. Logga in på [Azure-portalen](https://portal.azure.com/).

2. Välj ikonen **katalog + prenumeration** i portalens verktygsfält och välj sedan den katalog som innehåller Azure AD B2C klienten.

3. I Azure Portal söker du efter och väljer **Azure AD B2C**.

4. Under **aktiviteter** väljer du **gransknings loggar**.

5. Filtrera gransknings loggen genom att ställa in **kategorin** till **B2C** och ange **aktivitets resurs typ** till **IdentityProtection**. Välj sedan **Använd**.

6. Granska gransknings aktiviteten under de senaste 7 dagarna. Följande typer av aktivitet ingår:

   - **Utvärdera principer för villkorlig åtkomst**: den här Gransknings logg posten anger att en utvärdering av villkorlig åtkomst utfördes under en autentisering.
   - **Reparera användare**: det här värdet anger att bidraget eller kraven för en princip för villkorlig åtkomst uppfylldes av slutanvändaren, och den här aktiviteten rapporterades till riskhanteringen för att minska risken för (att minska) användaren.

7. Välj logg posten **utvärdera villkorlig åtkomst** i listan för att öppna **aktivitets information: Gransknings logg** sidan, som visar Gransknings logg identifierarna, tillsammans med den här informationen i avsnittet **Ytterligare information** :

   - ConditionalAccessResult: det bidrag som krävs av utvärderingen av villkorliga principer.
   - AppliedPolicies: en lista över alla principer för villkorlig åtkomst där villkoren uppfylldes och principerna är på.
   - ReportingPolicies: en lista över principer för villkorlig åtkomst som ställts in på endast rapport läge och där villkoren uppfylldes.

## <a name="next-steps"></a>Nästa steg

[Lägg till villkorlig åtkomst till ett användar flöde](conditional-access-user-flow.md).
