---
title: Lägg till villkorlig åtkomst till ett användar flöde i Azure AD B2C
description: Lär dig hur du lägger till villkorlig åtkomst till dina Azure AD B2C användar flöden. Konfigurera Multi-Factor Authentication-inställningar (MFA) och principer för villkorlig åtkomst i dina användar flöden för att genomdriva principer och åtgärda riskfyllda inloggningar.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: overview
ms.date: 03/03/2021
ms.custom: project-no-code
ms.author: mimart
author: msmimart
manager: celested
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 6325a890ea297a3aa2bdad76a1d95c10448a7b61
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102033956"
---
# <a name="add-conditional-access-to-user-flows-in-azure-active-directory-b2c"></a>Lägg till villkorlig åtkomst till användar flöden i Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

Villkorlig åtkomst kan läggas till i dina Azure Active Directory B2C (Azure AD B2C) användar flöden eller anpassade principer för att hantera riskfyllda inloggningar till dina program. Azure Active Directory (Azure AD) villkorlig åtkomst är det verktyg som används av Azure AD B2C för att samla in signaler, fatta beslut och tillämpa organisations principer.

![Flöde för villkorlig åtkomst](media/conditional-access-user-flow/conditional-access-flow.png)

Att automatisera riskbedömning med villkors villkor innebär att riskfyllda inloggningar identifieras omedelbart och sedan antingen åtgärdas eller blockeras.

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

## <a name="service-overview"></a>Tjänstöversikt

Azure AD B2C utvärderar varje inloggnings händelse och säkerställer att alla princip krav uppfylls innan användaren beviljas åtkomst. Under den här **utvärderings** fasen utvärderar tjänsten för villkorlig åtkomst de signaler som samlas in av identitets skydds risk identifieringar under inloggnings händelser. Resultatet av den här utvärderings processen är en uppsättning anspråk som anger om inloggningen ska beviljas eller blockeras. Den Azure AD B2C principen använder dessa anspråk för att utföra en åtgärd i användar flödet, till exempel blockera åtkomst eller att göra användaren utmanande med en speciell åtgärd, till exempel Multi-Factor Authentication (MFA). "Blockera åtkomst" åsidosätter alla andra inställningar.

::: zone pivot="b2c-custom-policy"
I följande exempel visas en teknisk profil för villkorlig åtkomst som används för att utvärdera inloggnings hotet.

```XML
<TechnicalProfile Id="ConditionalAccessEvaluation">
  <DisplayName>Conditional Access Provider</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.ConditionalAccessProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="OperationType">Evaluation</Item>
  </Metadata>
  ...
</TechnicalProfile>
```

::: zone-end

I **reparations** fasen som följer visas användaren med MFA. När du är klar Azure AD B2C informerar identitets skyddet att det identifierade inloggnings hotet har åtgärd ATS och av vilken metod. I det här exemplet Azure AD B2C signaler att användaren har slutfört Multi-Factor Authentication-utmaningen. 

::: zone pivot="b2c-custom-policy"

I följande exempel visas en teknisk profil för villkorlig åtkomst som används för att reparera identifierat hot:

```XML
<TechnicalProfile Id="ConditionalAccessRemediation">
  <DisplayName>Conditional Access Remediation</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.ConditionalAccessProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null"/>
  <Metadata>
    <Item Key="OperationType">Remediation</Item>
  </Metadata>
  ...
</TechnicalProfile>
```

::: zone-end

## <a name="components-of-the-solution"></a>Komponenter i lösningen

Det här är de komponenter som möjliggör villkorlig åtkomst i Azure AD B2C:

- **Användar flöde** eller **anpassad princip** som guidar användaren genom inloggnings-och registrerings processen.
- **Princip för villkorlig åtkomst** som ger signaler tillsammans för att fatta beslut och tillämpa organisations principer. När en användare loggar in i ditt program via en Azure AD B2C-princip använder principen för villkorlig åtkomst Azure AD Identity Protection signaler för att identifiera riskfyllda inloggningar och visar lämplig åtgärd för reparation.
- **Registrerat program** som dirigerar användarna till rätt Azure AD B2C användar flöde eller anpassad princip.
- [Tor webbläsare](https://www.torproject.org/download/) för att simulera en riskfylld inloggning.

## <a name="service-limitations-and-considerations"></a>Begränsningar och överväganden för tjänsten

Tänk på följande när du använder villkorlig åtkomst för Azure AD:

- Identitets skydd är tillgängligt för både lokala och sociala identiteter, till exempel Google eller Facebook. För sociala identiteter måste du aktivera villkorlig åtkomst manuellt. Identifieringen är begränsad eftersom autentiseringsuppgifter för sociala konton hanteras av den externa identitets leverantören.
- I Azure AD B2C klienter är endast en delmängd av principerna för [villkorlig åtkomst i Azure AD](../active-directory/conditional-access/overview.md) tillgängliga.


## <a name="prerequisites"></a>Förutsättningar

[!INCLUDE [active-directory-b2c-customization-prerequisites-custom-policy](../../includes/active-directory-b2c-customization-prerequisites-custom-policy.md)]

## <a name="pricing-tier"></a>Prisnivå

Azure AD B2C **Premium P2** krävs för att skapa riskfyllda inloggnings principer. **Premium P1** -klienter kan skapa en princip som baseras på plats-, program-, användarbaserade eller gruppbaserade principer. Mer information finns i [ändra din Azure AD B2C pris nivå](billing.md#change-your-azure-ad-pricing-tier)

## <a name="prepare-your-azure-ad-b2c-tenant"></a>Förbered din Azure AD B2C-klient

Om du vill lägga till en princip för villkorlig åtkomst inaktiverar du säkerhets inställningarna:

1. Logga in på [Azure-portalen](https://portal.azure.com/).
2. Välj ikonen **katalog + prenumeration** i portalens verktygsfält och välj sedan den katalog som innehåller Azure AD B2C klienten.
3. Under **Azure-tjänster** väljer du **Azure AD B2C**. Eller Använd sökrutan för att söka efter och välja **Azure AD B2C**.
4. Välj **Egenskaper** och välj sedan **hantera säkerhets inställningar**.

   ![Inaktivera säkerhets inställningarna](media/conditional-access-user-flow/disable-security-defaults.png)

5. Under **Aktivera säkerhets inställningar** väljer du **Nej**.

   ![Ställ in aktivera säkerhets standardvärden växla till Nej](media/conditional-access-user-flow/enable-security-defaults-toggle.png)

## <a name="add-a-conditional-access-policy"></a>Lägg till en princip för villkorlig åtkomst

En princip för villkorlig åtkomst är en if-then-sats för tilldelningar och åtkomst kontroller. En princip för villkorlig åtkomst ger signaler tillsammans för att fatta beslut och tillämpa organisations principer. Den logiska operatorn mellan tilldelningarna är *och*. Operatorn i varje tilldelning är *eller*.

![Tilldelningar för villkorlig åtkomst](media/conditional-access-user-flow/conditional-access-assignments.png)

Så här lägger du till en princip för villkorlig åtkomst:

1. I Azure Portal söker du efter och väljer **Azure AD B2C**.
1. Under **säkerhet** väljer du **villkorlig åtkomst (för hands version)**. Sidan **principer för villkorlig åtkomst** öppnas.
1. Välj **+ ny princip**.
1. Ange ett namn för principen, till exempel *blockera riskfylld inloggning*.
1. Under **tilldelningar** väljer du **användare och grupper** och väljer sedan en av följande konfigurationer som stöds:

    |Inkludera  |Licens | Kommentarer  |
    |---------|---------|---------|
    |**Alla användare** | P1, P2 |Om du väljer att inkludera **alla användare** kommer den här principen att påverka alla dina användare. Om du vill vara säker på att du inte vill låsa dig, Uteslut ditt administrativa konto genom att välja **exkludera**, välja **katalog roller** och sedan välja **Global administratör** i listan. Du kan också välja **användare och grupper** och sedan välja ditt konto i listan **Välj undantagna användare** .  | 
 
1. Välj **molnappar eller åtgärder** och **Välj sedan appar**. Bläddra efter ditt [förlitande parts program](tutorial-register-applications.md).

1. Välj **villkor** och välj sedan något av följande villkor. Välj till exempel **inloggnings risk** , **hög**, **medel** och **låg** risk nivå.
    
    |Villkor  |Licens  |Kommentarer  |
    |---------|---------|---------|
    |**Användarrisk**|P2|Användar risken representerar sannolikheten för att en specifik identitet eller ett konto har komprometterats.|
    |**Inloggningsrisk**|P2|Inloggnings risken representerar sannolikheten att en begäran om autentisering inte har behörighet av identitets ägaren.|
    |**Enhetsplattformar**|Stöds inte| Karakteriserad av det operativ system som körs på en enhet. Mer information finns i [enhets plattformar](../active-directory/conditional-access/concept-conditional-access-conditions.md#device-platforms).|
    |**Platser**|P1, P2|Namngivna platser kan innehålla information om offentliga IPv4-nätverk, land eller region eller okända områden som inte mappas till vissa länder eller regioner. Mer information finns i [platser](../active-directory/conditional-access/concept-conditional-access-conditions.md#locations). |
 
1. Under **Åtkomstkontroller** väljer du **Bevilja**. Välj sedan om du vill blockera eller bevilja åtkomst:
    
    |Alternativ  |Licens |Anteckning  |
    |---------|---------|---------|
    |**Blockera åtkomst**|P1, P2| Förhindrar åtkomst baserat på de villkor som anges i den här principen för villkorlig åtkomst.|
    |**Bevilja åtkomst** med **kräver Multi-Factor Authentication**|P1, P2|Baserat på de villkor som anges i den här principen för villkorlig åtkomst måste användaren gå igenom Azure AD B2C Multi-Factor Authentication.|

1. Under **Aktivera princip** väljer du något av följande:
    
    |Alternativ  |Licens |Anteckning  |
    |---------|---------|---------|
    |**Endast rapport**|P1, P2| Rapport – tillåter administratörer att utvärdera effekten av principer för villkorlig åtkomst innan de aktive ras i deras miljö. Vi rekommenderar att du kontrollerar principen med det här läget och bestämmer konsekvenserna för slutanvändarna utan att kräva multifaktorautentisering eller blockera användare. Mer information finns i [Granska villkorliga åtkomst resultat i gransknings rapporten](#review-conditional-access-outcomes-in-the-audit-report)|
    | **På**| P1, P2| Åtkomst principen utvärderas och verkställs inte. |
    | **Av** | P1, P2| Åtkomst principen är inte aktive rad och har ingen inverkan på användarna. |

1. Aktivera din test princip för villkorlig åtkomst genom att välja **skapa**.

## <a name="add-conditional-access-to-a-user-flow"></a>Lägg till villkorlig åtkomst till ett användar flöde

När du har lagt till principen för villkorlig åtkomst för Azure AD, aktiverar du villkorlig åtkomst i ditt användar flöde eller en anpassad princip. När du aktiverar villkorlig åtkomst behöver du inte ange ett princip namn.

Flera principer för villkorlig åtkomst kan användas för enskilda användare när som helst. I det här fallet har den strikta principen för åtkomst kontroll företräde. Om en princip till exempel kräver Multi-Factor Authentication (MFA), medan de andra blockerar åtkomsten, kommer användaren att blockeras.

## <a name="enable-multi-factor-authentication-optional"></a>Aktivera Multi-Factor Authentication (valfritt)

När du lägger till villkorlig åtkomst till ett användar flöde bör du överväga att använda **Multi-Factor Authentication (MFA)**. Användare kan använda en engångs kod via SMS eller Voice eller ett eng ång slö sen ord via e-post för Multi-Factor Authentication. MFA-inställningar är oberoende av inställningarna för villkorlig åtkomst. Du kan ställa in MFA **så att MFA alltid krävs** , oavsett konfigurationen för villkorlig åtkomst. Du kan också ange MFA till **villkorligt** så att MFA endast krävs när en aktiv princip för villkorlig åtkomst kräver det.

> [!IMPORTANT]
> Om din princip för villkorlig åtkomst beviljar åtkomst med MFA men användaren inte har registrerat ett telefonnummer, kan användaren blockeras.

::: zone pivot="b2c-user-flow"

Om du vill aktivera villkorlig åtkomst för ett användar flöde kontrollerar du att versionen stöder villkorlig åtkomst. Dessa användar flödes versioner är märkta som **rekommenderas**.

1. Logga in på [Azure-portalen](https://portal.azure.com).

1. Välj ikonen **katalog + prenumeration** i portalens verktygsfält och välj sedan den katalog som innehåller Azure AD B2C klienten.

1. Under **Azure-tjänster** väljer du **Azure AD B2C**. Eller Använd sökrutan för att söka efter och välja **Azure AD B2C**.

1. Under **principer** väljer du **användar flöden**. Välj sedan användar flödet.

1. Välj **Egenskaper** och se till att användar flödet stöder villkorlig åtkomst genom att leta efter inställningen **villkorlig åtkomst**.
 
   ![Konfigurera MFA och villkorlig åtkomst i egenskaper](media/conditional-access-user-flow/add-conditional-access.png)

1. I avsnittet **Multi-Factor Authentication** väljer du önskad **MFA-metod** och väljer **villkorlig (rekommenderas)** under **MFA-tvång**.
 
1. I avsnittet **villkorlig åtkomst** markerar du kryss rutan **tillämpa villkorliga åtkomst principer** .

1. Välj **Spara**.


::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="add-conditional-access-to-your-policy"></a>Lägg till villkorlig åtkomst till principen

1. Hämta exemplet på en princip för villkorlig åtkomst på [GitHub](https://github.com/azure-ad-b2c/samples/tree/master/policies/conditional-access).
1. Ersätt strängen `yourtenant` med namnet på din Azure AD B2C-klient i varje fil. Om namnet på din B2C-klient till exempel är *contosob2c*, blir alla instanser av `yourtenant.onmicrosoft.com` `contosob2c.onmicrosoft.com` .
1. Ladda upp principfiler.

## <a name="test-your-custom-policy"></a>Testa din anpassade princip

1. Välj `B2C_1A_signup_signin_with_ca` eller- `B2C_1A_signup_signin_with_ca_whatif` principen för att öppna sidan Översikt. Välj sedan **Kör användar flöde**. Under **program** väljer du *webapp1*. **Svars-URL: en** ska visas `https://jwt.ms` .
1. Kopiera URL-adressen under **Kör användar flödets slut punkt**.

1. För att simulera en riskfylld inloggning öppnar du [webbläsaren Tor](https://www.torproject.org/download/) och använder den URL som du kopierade i föregående steg för att logga in på den registrerade appen.

1. Ange den begärda informationen på inloggnings sidan och försök sedan logga in. Token returneras till `https://jwt.ms` och ska visas för dig. I jwt.ms-kodad token bör du se att inloggningen blockerades.

::: zone-end

::: zone pivot="b2c-user-flow"

## <a name="test-your-user-flow"></a>Testa ditt användar flöde

1. Välj det användar flöde som du skapade för att öppna sidan Översikt och välj sedan **Kör användar flöde**. Under **program** väljer du *webapp1*. **Svars-URL: en** ska visas `https://jwt.ms` .

1. Kopiera URL-adressen under **Kör användar flödets slut punkt**.

1. För att simulera en riskfylld inloggning öppnar du [webbläsaren Tor](https://www.torproject.org/download/) och använder den URL som du kopierade i föregående steg för att logga in på den registrerade appen.

1. Ange den begärda informationen på inloggnings sidan och försök sedan logga in. Token returneras till `https://jwt.ms` och ska visas för dig. I jwt.ms-kodad token bör du se att inloggningen blockerades.

::: zone-end

## <a name="review-conditional-access-outcomes-in-the-audit-report"></a>Granska resultat från villkorlig åtkomst i gransknings rapporten

Granska resultatet av en händelse för villkorlig åtkomst:

1. Logga in på [Azure-portalen](https://portal.azure.com/).

2. Välj ikonen **katalog + prenumeration** i portalens verktygsfält och välj sedan den katalog som innehåller Azure AD B2C klienten.

3. Under **Azure-tjänster** väljer du **Azure AD B2C**. Eller Använd sökrutan för att söka efter och välja **Azure AD B2C**.

4. Under **aktiviteter** väljer du **gransknings loggar**.

5. Filtrera gransknings loggen genom att ställa in **kategorin** till **B2C** och ange **aktivitets resurs typ** till **IdentityProtection**. Välj sedan **Använd**.

6. Granska gransknings aktiviteten under de senaste sju dagarna. Följande typer av aktivitet ingår:

   - **Utvärdera principer för villkorlig åtkomst**: den här Gransknings logg posten anger att en utvärdering av villkorlig åtkomst utfördes under en autentisering.
   - **Reparera användare**: det här värdet anger att bidraget eller kraven för en princip för villkorlig åtkomst uppfylldes av slutanvändaren, och den här aktiviteten rapporterades till riskhanteringen för att minimera (minska risken för) användaren.

7. Välj logg posten **utvärdera villkorlig åtkomst** i listan för att öppna **aktivitets information: Gransknings logg** sidan, som visar Gransknings logg identifierarna, tillsammans med den här informationen i avsnittet **Ytterligare information** :

   - **ConditionalAccessResult**: det bidrag som krävs av utvärderingen av villkorliga principer.
   - **AppliedPolicies**: en lista över alla principer för villkorlig åtkomst där villkoren uppfylldes och principerna är på.
   - **ReportingPolicies**: en lista över principer för villkorlig åtkomst som ställts in på endast rapport läge och där villkoren uppfylldes.

## <a name="next-steps"></a>Nästa steg

[Anpassa användar gränssnittet i ett Azure AD B2C användar flöde](customize-ui-with-html.md)
