---
title: Lägga till villkorlig åtkomst till ett användarflöde i Azure AD B2C
description: Lär dig hur du lägger till villkorlig åtkomst till Azure AD B2C användarflöden. Konfigurera multifaktorautentiseringsinställningar (MFA) och principer för villkorsstyrd åtkomst i dina användarflöden för att genomdriva principer och åtgärda riskfyllda inloggningar.
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
ms.openlocfilehash: 0e6a872891f09f60ea963fa783e6f49dc4e94a54
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2021
ms.locfileid: "107861867"
---
# <a name="add-conditional-access-to-user-flows-in-azure-active-directory-b2c"></a>Lägga till villkorlig åtkomst till användarflöden i Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

Villkorlig åtkomst kan läggas till i dina Azure Active Directory B2C(Azure AD B2C) användarflöden eller anpassade principer för att hantera riskfyllda inloggningar till dina program. Azure Active Directory (Azure AD) Villkorsstyrd åtkomst är det verktyg som används av Azure AD B2C för att samla signaler, fatta beslut och genomdriva organisationsprinciper.

![Flöde för villkorsstyrd åtkomst](media/conditional-access-user-flow/conditional-access-flow.png)

Automatisering av riskbedömning med principvillkor innebär att riskfyllda inloggningar identifieras omedelbart och sedan antingen åtgärdas eller blockeras.

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

## <a name="service-overview"></a>Tjänstöversikt

Azure AD B2C utvärderar varje inloggningshändelse och ser till att alla principkrav är uppfyllda innan användaren beviljas åtkomst. Under den **här utvärderingsfasen** utvärderar tjänsten för villkorsstyrd åtkomst de signaler som samlas in av Riskidentifiering för Identity Protection under inloggningshändelser. Resultatet av den här utvärderingsprocessen är en uppsättning anspråk som anger om inloggningen ska beviljas eller blockeras. Principen Azure AD B2C använder dessa anspråk för att vidta en åtgärd i användarflödet, till exempel blockera åtkomst eller utmana användaren med en specifik åtgärd som multifaktorautentisering (MFA). "Blockera åtkomst" åsidosätter alla andra inställningar.

::: zone pivot="b2c-custom-policy"
I följande exempel visas en teknisk profil för villkorsstyrd åtkomst som används för att utvärdera inloggningshotet.

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

I **reparationsfasen** som följer uppmanas användaren att använda MFA. När det är Azure AD B2C Identity Protection meddelas att det identifierade inloggningshotet har åtgärdats och med vilken metod. I det här Azure AD B2C att användaren har slutfört multifaktorautentiseringsutmaningen. 

::: zone pivot="b2c-custom-policy"

I följande exempel visas en teknisk profil för villkorsstyrd åtkomst som används för att åtgärda det identifierade hotet:

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

Det här är de komponenter som aktiverar villkorlig åtkomst i Azure AD B2C:

- **Användarflöde** **eller anpassad** princip som vägleder användaren genom inloggnings- och registreringsprocessen.
- **Princip för villkorsstyrd** åtkomst som samlar signaler för att fatta beslut och genomdriva organisationsprinciper. När en användare loggar in i ditt program via en Azure AD B2C-princip använder principen för villkorsstyrd åtkomst Azure AD Identity Protection-signaler för att identifiera riskfyllda inloggningar och presenterar lämplig åtgärd.
- **Registrerat program** som dirigerar användare till rätt Azure AD B2C användarflöde eller anpassad princip.
- [TOR Browser](https://www.torproject.org/download/) för att simulera en riskabel inloggning.

## <a name="service-limitations-and-considerations"></a>Tjänstbegränsningar och överväganden

Tänk på följande när du använder villkorsstyrd åtkomst i Azure AD:

- Identity Protection är tillgängligt för både lokala och sociala identiteter, till exempel Google eller Facebook. För sociala identiteter måste du aktivera villkorlig åtkomst manuellt. Identifieringen är begränsad eftersom autentiseringsuppgifter för sociala konton hanteras av den externa identitetsprovidern.
- I Azure AD B2C är endast en delmängd av principerna för [villkorsstyrd åtkomst i Azure AD](../active-directory/conditional-access/overview.md) tillgängliga.


## <a name="prerequisites"></a>Förutsättningar

[!INCLUDE [active-directory-b2c-customization-prerequisites-custom-policy](../../includes/active-directory-b2c-customization-prerequisites-custom-policy.md)]

## <a name="pricing-tier"></a>Prisnivå

Azure AD B2C Premium **P2** krävs för att skapa riskfyllda inloggningsprinciper. **Premium P1-klienter** kan skapa en princip som baseras på plats, program, användarbaserade eller gruppbaserade principer. Mer information finns i [Ändra Azure AD B2C prisnivå](billing.md#change-your-azure-ad-pricing-tier)

## <a name="prepare-your-azure-ad-b2c-tenant"></a>Förbereda din Azure AD B2C klient

Om du vill lägga till en princip för villkorlig åtkomst inaktiverar du standardinställningarna för säkerhet:

1. Logga in på [Azure-portalen](https://portal.azure.com/).
2. Välj ikonen **Katalog +** prenumeration i portalens verktygsfält och välj sedan den katalog som innehåller din Azure AD B2C klientorganisation.
3. Under **Azure-tjänster** väljer **du Azure AD B2C**. Eller använd sökrutan för att hitta och välja **Azure AD B2C**.
4. Välj **Egenskaper** och välj sedan **Hantera standardinställningar för säkerhet.**

   ![Inaktivera standardvärdena för säkerhet](media/conditional-access-user-flow/disable-security-defaults.png)

5. Under **Aktivera standardinställningar för säkerhet** väljer du **Nej.**

   ![Ställ in Aktivera standardinställningar för säkerhet på Nej](media/conditional-access-user-flow/enable-security-defaults-toggle.png)

## <a name="add-a-conditional-access-policy"></a>Lägga till en princip för villkorlig åtkomst

En princip för villkorsstyrd åtkomst är en if-then-instruktion med tilldelningar och åtkomstkontroller. En princip för villkorsstyrd åtkomst samlar signaler för att fatta beslut och genomdriva organisationsprinciper. Den logiska operatorn mellan tilldelningarna är *And*. Operatorn i varje tilldelning är *Eller*.

![Tilldelningar för villkorlig åtkomst](media/conditional-access-user-flow/conditional-access-assignments.png)

Så här lägger du till en princip för villkorlig åtkomst:

1. I listan Azure Portal du efter och väljer **Azure AD B2C**.
1. Under **Säkerhet** väljer du **Villkorlig åtkomst (förhandsversion)**. Sidan **Principer för villkorsstyrd** åtkomst öppnas.
1. Välj **+ Ny princip.**
1. Ange ett namn för principen, till exempel *Blockera riskabel inloggning*.
1. Under **Tilldelningar** väljer **du Användare och** grupper och väljer sedan någon av följande konfigurationer som stöds:

    |Inkludera  |Licens | Kommentarer  |
    |---------|---------|---------|
    |**Alla användare** | P1, P2 |Om du väljer att **inkludera Alla användare** påverkar den här principen alla dina användare. Se till att du inte låser dig själv genom att exkludera ditt administratörskonto genom att välja **Exkludera,** välja **Katalogroller** och sedan välja Global administratör i listan. Du kan också välja **Användare och grupper** och sedan välja ditt konto i listan Välj **exkluderade** användare.  | 
 
1. Välj **Molnappar eller åtgärder** och sedan Välj **appar.** Bläddra efter ditt [förlitande partprogram](tutorial-register-applications.md).

1. Välj **Villkor** och välj sedan bland följande villkor. Välj till exempel  **Inloggningsrisk och Hög,** **Medel** och **Låg** risknivå.
    
    |Villkor  |Licens  |Kommentarer  |
    |---------|---------|---------|
    |**Användarrisk**|P2|Användarrisken representerar sannolikheten att en viss identitet eller ett visst konto komprometteras.|
    |**Inloggningsrisk**|P2|Inloggningsrisken representerar sannolikheten att en viss autentiseringsbegäran inte auktoriserats av identitetsägaren.|
    |**Enhetsplattformar**|Stöds inte| Kännetecknas av det operativsystem som körs på en enhet. Mer information finns i [Enhetsplattformar.](../active-directory/conditional-access/concept-conditional-access-conditions.md#device-platforms)|
    |**Platser**|P1, P2|Namngivna platser kan innehålla offentlig IPv4-nätverksinformation, land eller region eller okända områden som inte mappar till specifika länder eller regioner. Mer information finns i [Platser.](../active-directory/conditional-access/concept-conditional-access-conditions.md#locations) |
 
1. Under **Åtkomstkontroller** väljer du **Bevilja**. Välj sedan om du vill blockera eller bevilja åtkomst:
    
    |Alternativ  |Licens |Anteckning  |
    |---------|---------|---------|
    |**Blockera åtkomst**|P1, P2| Förhindrar åtkomst baserat på de villkor som anges i den här principen för villkorlig åtkomst.|
    |**Bevilja åtkomst** med **Kräv multifaktorautentisering**|P1, P2|Baserat på de villkor som anges i den här principen för villkorlig åtkomst måste användaren gå igenom Azure AD B2C multifaktorautentisering.|

1. Under **Aktivera princip** väljer du något av följande:
    
    |Alternativ  |Licens |Anteckning  |
    |---------|---------|---------|
    |**Endast rapport**|P1, P2| Endast rapport gör att administratörer kan utvärdera effekten av principer för villkorlig åtkomst innan de kan aktivera dem i sin miljö. Vi rekommenderar att du kontrollerar principen med det här tillståndet och fastställer effekten för slutanvändarna utan att kräva multifaktorautentisering eller blockerande användare. Mer information finns i [Granska resultat för villkorsstyrd åtkomst i granskningsrapporten](#review-conditional-access-outcomes-in-the-audit-report)|
    | **På**| P1, P2| Åtkomstprincipen utvärderas och framtvingas inte. |
    | **Av** | P1, P2| Åtkomstprincipen är inte aktiverad och påverkar inte användarna. |

1. Aktivera testprincipen för villkorlig åtkomst genom att välja **Skapa.**

## <a name="add-conditional-access-to-a-user-flow"></a>Lägga till villkorlig åtkomst till ett användarflöde

När du har lagt till azure AD-principen för villkorsstyrd åtkomst aktiverar du villkorlig åtkomst i ditt användarflöde eller anpassade princip. När du aktiverar villkorlig åtkomst behöver du inte ange ett principnamn.

Flera principer för villkorlig åtkomst kan tillämpas på en enskild användare när som helst. I det här fallet har den mest strikta principen för åtkomstkontroll företräde. Om en princip till exempel kräver multifaktorautentisering (MFA), medan den andra blockerar åtkomsten, blockeras användaren.

## <a name="conditional-access-template-1-sign-in-risk-based-conditional-access"></a>Mall för villkorsstyrd åtkomst 1: Riskbaserad villkorlig åtkomst för inloggning

De flesta användare har ett normalt beteende som kan spåras. När de hamnar utanför den här normen kan det vara riskabelt att tillåta att de bara loggar in. Du kanske vill blockera den användaren eller kanske bara be dem att utföra multifaktorautentisering för att bevisa att de verkligen är den de utger sig för att vara.

En inloggningsrisk representerar sannolikheten att en viss autentiseringsbegäran inte auktoriserats av identitetsägaren. Organisationer med P2-licenser kan skapa principer för villkorsstyrd [åtkomst som Azure AD Identity Protection implementerar riskidentifiering för inloggning.](https://docs.microsoft.com/azure/active-directory/identity-protection/concept-identity-protection-risks#sign-in-risk) Observera begränsningarna för [Identity Protection-identifieringar för B2C](https://docs.microsoft.com/azure/active-directory-b2c/identity-protection-investigate-risk?pivots=b2c-user-flow#service-limitations-and-considerations).

Om risken upptäcks kan användarna utföra multifaktorautentisering för att själv åtgärda och stänga den riskfyllda inloggningshändelsen för att förhindra onödigt brus för administratörer.

Organisationer bör välja något av följande alternativ för att aktivera en princip för inloggningsriskbaserad villkorlig åtkomst som kräver multifaktorautentisering (MFA) när inloggningsrisken är medelhög ELLER hög.

### <a name="enable-with-conditional-access-policy"></a>Aktivera med princip för villkorsstyrd åtkomst

1. Logga in på **Azure-portalen**.
2. Bläddra till **Azure AD B2C**  >  **Villkorlig åtkomst** för  >  **säkerhet.**
3. Välj **Ny princip.**
4. Ge principen ett namn. Vi rekommenderar att organisationer skapar en meningsfull standard för namnen på sina principer.
5. Under **Tilldelningar** väljer du **Användare och grupper**.
   1. Under **Inkludera** väljer du **Alla användare.**
   2. Under **Exkludera** väljer du **Användare och grupper** och väljer organisationens konton för åtkomst vid akutfall eller nödfall. 
   3. Välj **Klar**.
6. Under **Molnappar eller åtgärder**  >  **Inkludera** väljer du Alla **molnappar.**
7. Under **Villkor**  >  **Inloggningsrisk anger** du **Konfigurera** till **Ja.** Under **Välj risknivå för inloggning gäller den här principen för** 
   1. Välj **Hög** och **Medel.**
   2. Välj **Klar**.
8. Under **Åtkomstkontroller**  >  **Bevilja** väljer du **Bevilja åtkomst,** **Kräv multifaktorautentisering** och **väljer Välj**.
9. Bekräfta inställningarna och ställ **in Aktivera princip** på **På.**
10. Välj **Skapa** för att skapa för att aktivera principen.

### <a name="enable-with-conditional-access-apis"></a>Aktivera med API:er för villkorsstyrd åtkomst

Om du vill skapa en princip för riskbaserad villkorlig åtkomst för inloggning med API:er för villkorsstyrd åtkomst kan du läsa dokumentationen för API:er [för villkorsstyrd åtkomst.](https://docs.microsoft.com/azure/active-directory/conditional-access/howto-conditional-access-apis#graph-api)

Följande mall kan användas för att skapa en princip för villkorsstyrd åtkomst med visningsnamnet "CA002: Kräv MFA för medel+ inloggningsrisk" i rapportläge.

```json
{
    "displayName": "Template 1: Require MFA for medium+ sign-in risk",
    "state": "enabledForReportingButNotEnforced",
    "conditions": {
        "signInRiskLevels": [ "high" ,
            "medium"
        ],
        "applications": {
            "includeApplications": [
                "All"
            ]
        },
        "users": {
            "includeUsers": [
                "All"
            ],
            "excludeUsers": [
                "f753047e-de31-4c74-a6fb-c38589047723"
            ]
        }
    },
    "grantControls": {
        "operator": "OR",
        "builtInControls": [
            "mfa"
        ]
    }
}
```

## <a name="enable-multi-factor-authentication-optional"></a>Aktivera multifaktorautentisering (valfritt)

När du lägger till villkorlig åtkomst till ett användarflöde bör du överväga att använda **multifaktorautentisering (MFA).** Användare kan använda en enda gång-kod via SMS eller röst, eller ett lösenord en gång via e-post för multifaktorautentisering. MFA-inställningarna är oberoende av inställningarna för villkorsstyrd åtkomst. Du kan ställa in MFA **på Always On** (Alltid på) så att MFA alltid krävs oavsett din konfiguration av villkorlig åtkomst. Eller så kan du ställa in MFA på **Villkorsstyrd** så att MFA endast krävs när en aktiv princip för villkorlig åtkomst kräver det.

> [!IMPORTANT]
> Om din princip för villkorlig åtkomst ger åtkomst med MFA men användaren inte har registrerat något telefonnummer kan användaren blockeras.

::: zone pivot="b2c-user-flow"

Om du vill aktivera villkorlig åtkomst för ett användarflöde kontrollerar du att versionen stöder villkorlig åtkomst. Dessa användarflödesversioner har etiketten **Recommended (Rekommenderas).**

1. Logga in på [Azure-portalen](https://portal.azure.com).

1. Välj ikonen **Katalog + prenumeration** i portalens verktygsfält och välj sedan den katalog som innehåller din Azure AD B2C klientorganisation.

1. Under **Azure-tjänster** väljer **du Azure AD B2C**. Eller använd sökrutan för att hitta och välja **Azure AD B2C**.

1. Under **Principer** väljer du **Användarflöden.** Välj sedan användarflödet.

1. Välj **Egenskaper** och kontrollera att användarflödet stöder villkorlig åtkomst genom att leta efter inställningen villkorlig **åtkomst**.
 
   ![Konfigurera MFA och villkorsstyrd åtkomst i egenskaper](media/conditional-access-user-flow/add-conditional-access.png)

1. I avsnittet **Multi-factor authentication (Multifaktorautentisering)** väljer du önskad **MFA-metod** och sedan **Villkorsstyrd (rekommenderas)** under **MFA-tvingande**.
 
1. I avsnittet **Villkorsstyrd** åtkomst markerar du **kryssrutan Framtvinga principer för villkorlig** åtkomst.

1. Välj **Spara**.


::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="add-conditional-access-to-your-policy"></a>Lägga till villkorlig åtkomst till principen

1. Hämta exemplet på en princip för villkorlig åtkomst på [GitHub](https://github.com/azure-ad-b2c/samples/tree/master/policies/conditional-access).
1. Ersätt strängen i varje fil `yourtenant` med namnet på din Azure AD B2C klientorganisation. Om namnet på din B2C-klientorganisation till exempel är *contosob2c* blir alla instanser av `yourtenant.onmicrosoft.com` `contosob2c.onmicrosoft.com` .
1. Ladda upp principfilerna.

## <a name="test-your-custom-policy"></a>Testa din anpassade princip

1. Välj principen `B2C_1A_signup_signin_with_ca` eller för att öppna dess `B2C_1A_signup_signin_with_ca_whatif` översiktssida. Välj sedan **Kör användarflöde**. Under **Program** väljer du *webapp1.* **Svars-URL:en** ska visa `https://jwt.ms` .
1. Kopiera URL:en under **Kör slutpunkt för användarflöde.**

1. Om du vill simulera en riskabel inloggning öppnar du [Tor Browser](https://www.torproject.org/download/) och använder den URL som du kopierade i föregående steg för att logga in på den registrerade appen.

1. Ange den begärda informationen på inloggningssidan och försök sedan logga in. Token returneras till `https://jwt.ms` och bör visas för dig. I jwt.ms avkodad token bör du se att inloggningen har blockerats.

::: zone-end

::: zone pivot="b2c-user-flow"

## <a name="test-your-user-flow"></a>Testa ditt användarflöde

1. Välj det användarflöde som du skapade för att öppna dess översiktssida och välj sedan **Kör användarflöde.** Under **Program** väljer du *webapp1.* **Svars-URL:en** ska visa `https://jwt.ms` .

1. Kopiera URL:en under **Kör slutpunkt för användarflöde.**

1. Om du vill simulera en riskabel inloggning öppnar du [Tor Browser](https://www.torproject.org/download/) och använder den URL som du kopierade i föregående steg för att logga in på den registrerade appen.

1. Ange den begärda informationen på inloggningssidan och försök sedan logga in. Token returneras till `https://jwt.ms` och bör visas för dig. I jwt.ms avkodad token bör du se att inloggningen har blockerats.

::: zone-end

## <a name="review-conditional-access-outcomes-in-the-audit-report"></a>Granska resultat för villkorsstyrd åtkomst i granskningsrapporten

Så här granskar du resultatet av en villkorlig åtkomsthändelse:

1. Logga in på [Azure-portalen](https://portal.azure.com/).

2. Välj ikonen **Katalog + prenumeration** i portalens verktygsfält och välj sedan den katalog som innehåller din Azure AD B2C klientorganisation.

3. Under **Azure-tjänster** väljer **du Azure AD B2C**. Eller använd sökrutan för att hitta och välja **Azure AD B2C**.

4. Under **Aktiviteter** väljer du **Granskningsloggar.**

5. Filtrera granskningsloggen genom att **ange Kategori** **till B2C** och **ange Activity Resource Type** till **IdentityProtection**. Välj sedan **Använd**.

6. Granska granskningsaktiviteten under upp till de senaste sju dagarna. Följande typer av aktivitet ingår:

   - **Utvärdera principer för villkorlig åtkomst:** Den här granskningsloggposten anger att en utvärdering av villkorlig åtkomst utfördes under en autentisering.
   - **Åtgärda** användare: Den här posten anger att beviljandet eller kraven för en princip för villkorsstyrd åtkomst uppfylldes av slutanvändaren, och den här aktiviteten rapporterades till riskmotorn för att minimera (minska risken för) användaren.

7. Välj loggposten **Utvärdera** princip för villkorlig åtkomst i listan för att öppna sidan **Aktivitetsinformation:** Granskningslogg, som visar granskningsloggidentifierarna, tillsammans med den här informationen i **avsnittet Ytterligare** information:

   - **ConditionalAccessResult:** Det beviljande som krävs av den villkorliga principutvärderingen.
   - **AppliedPolicies:** En lista över alla principer för villkorsstyrd åtkomst där villkoren uppfylldes och principerna är PÅ.
   - **ReportingPolicies**: En lista över de principer för villkorsstyrd åtkomst som var inställda på endast rapportläge och där villkoren uppfylldes.

## <a name="next-steps"></a>Nästa steg

[Anpassa användargränssnittet i ett Azure AD B2C användarflöde](customize-ui-with-html.md)
