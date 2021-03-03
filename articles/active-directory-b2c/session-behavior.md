---
title: Konfigurera sessionens beteende – Azure Active Directory B2C | Microsoft Docs
description: Lär dig hur du konfigurerar sessionens beteende i Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 02/23/2021
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 85d00b393ad169764a2f26e324295308ef49d3ba
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/02/2021
ms.locfileid: "101646589"
---
# <a name="configure-session-behavior-in-azure-active-directory-b2c"></a>Konfigurera sessionsbeteende i Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

Enkel inloggning (SSO) lägger till säkerhet och bekvämlighet när användare loggar in mellan program i Azure Active Directory B2C (Azure AD B2C). Den här artikeln beskriver de enkla inloggnings metoderna som används i Azure AD B2C och hjälper dig att välja den lämpligaste SSO-metoden när du konfigurerar principen.

Med enkel inloggning loggar användarna in en gång med ett enda konto och får åtkomst till flera program. Programmet kan vara ett webb-, mobil-eller enda webb program, oavsett plattform eller domän namn.

När användaren loggar in till ett program från början behåller Azure AD B2C en cookie-baserad session. Vid efterföljande autentiseringsbegäranden läser Azure AD B2C och verifierar den cookie-baserade sessionen och utfärdar en åtkomsttoken utan att användaren uppmanas att logga in igen. Om den cookie-baserade sessionen upphör att gälla eller blir ogiltig, uppmanas användaren att logga in igen.  

## <a name="prerequisites"></a>Förutsättningar

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

## <a name="azure-ad-b2c-session-overview"></a>Översikt över Azure AD B2C session

Integrering med Azure AD B2C omfattar tre typer av SSO-sessioner:

- **Azure AD B2C** -session hanteras av Azure AD B2C
- **Federerad identitetsprovider** – session som hanteras av identitets leverantören, till exempel Facebook, Salesforce eller Microsoft-konto
- Programsession som hanteras av webb-, mobil-eller enskild sida **-program**

![Session för enkel inloggning](media/session-behavior/sso-session-types.png)

### <a name="azure-ad-b2c-session"></a>Azure AD B2C session 

När en användare autentiseras med ett lokalt eller socialt konto lagrar Azure AD B2C en cookie-baserad session i användarens webbläsare. Cookien lagras under Azure AD B2C klient domän namnet, till exempel `https://contoso.b2clogin.com` .

Om en användare ursprungligen loggar in med ett federerat konto och sedan loggar in på samma app eller i en annan app, Azure AD B2C försöker hämta en ny åtkomsttoken från den federerade identitets leverantören under tids perioden för sessionen (Time to Live eller TTL). Om den federerade identitets leverantören har upphört att gälla eller är ogiltig, uppmanas användaren att ange sina autentiseringsuppgifter för den federerade identitets leverantören. Om sessionen fortfarande är aktiv (eller om användaren har loggat in med ett lokalt konto i stället för ett federerat konto), auktoriserar Azure AD B2C användaren och eliminerar ytterligare prompter.

Du kan konfigurera sessionens beteende, inklusive sessions-TTL och hur Azure AD B2C delar sessionen över principer och program.

### <a name="federated-identity-provider-session"></a>Provider för federerade identitetsprovider

En social-eller företags identitets leverantör hanterar sin egen session. Cookien lagras under identitets leverantörens domän namn, till exempel `https://login.salesforce.com` . Azure AD B2C styr inte den federerade identitets leverantören. I stället bestäms sessionens beteende av den federerade identitets leverantören. 

Föreställ dig följande scenario:

1. En användare loggar in på Facebook för att kontrol lera deras flöde.
2. Senare öppnar användaren ditt program och påbörjar inloggnings processen. Programmet omdirigerar användaren till Azure AD B2C för att slutföra inloggnings processen.
3. På sidan Azure AD B2C registrering eller inloggning väljer användaren att logga in med sitt Facebook-konto. Användaren omdirigeras till Facebook. Om det finns en aktiv session på Facebook uppmanas användaren inte att ange sina autentiseringsuppgifter och dirigeras omedelbart om till Azure AD B2C med en Facebook-token.

### <a name="application-session"></a>Programsession

En webb-, mobil-eller enskild sida-applikation kan skyddas av OAuth-åtkomst, ID-token eller SAML-token. När en användare försöker komma åt en skyddad resurs i appen kontrollerar appen om det finns en aktiv session på program sidan. Om det inte finns någon app-session eller om sessionen har gått ut, tar appen användaren Azure AD B2C till inloggnings sidan.

Programsessionen kan vara en cookie-baserad session som lagras under programmets domän namn, till exempel `https://contoso.com` . Mobila program kan lagra sessionen på ett annat sätt, men med hjälp av en liknande metod.

## <a name="configure-azure-ad-b2c-session-behavior"></a>Konfigurera beteende för Azure AD B2C session

Du kan konfigurera beteendet för Azure AD B2C session, inklusive:

- **Web App-sessionens livs längd (minuter)** – hur lång tid som Azure AD B2C sessions-cookien lagras i användarens webbläsare efter autentiseringen. Du kan ställa in sessionens livs längd till ett värde mellan 15 och 720 minuter.

- **Timeout för webbapp** – visar hur en session utökas av inställningen för sessionens livs längd eller inställningen Behåll mig inloggad (KMSI avgör).
  - **Rullande** – anger att sessionen är utökad varje gång användaren utför en cookie-baserad autentisering (standard).
  - **Absolut** -visar att användaren tvingas att autentisera igen efter den angivna tids perioden.

- **Konfiguration av enkel inloggning** – Azure AD B2C-sessionen kan konfigureras med följande omfång:
  - **Klient** – den här inställningen är standard. Med den här inställningen kan flera program och användare flöda i B2C-klienten för att dela med sig av samma användarsession. När en användare till exempel loggar in på ett program, kan användaren också sömlöst logga in på en annan vid åtkomst till den.
  - **Program** – med den här inställningen kan du underhålla en användarsession exklusivt för ett program, oberoende av andra program. Du kan till exempel använda den här inställningen om du vill att användaren ska logga in på Contoso apotek oavsett om användaren redan har loggat in på Contosos inköp.
  - **Princip** – med den här inställningen kan du underhålla en användarsession exklusivt för ett användar flöde, oberoende av de program som använder den. Om användaren till exempel redan har loggat in och slutfört ett Multi-Factor Authentication (MFA)-steg, kan användaren få åtkomst till högre säkerhets delar av flera program, förutsatt att sessionen som är kopplad till användar flödet inte upphör att gälla.
  - **Disabled** – inställningen tvingar användaren att köra genom hela användar flödet vid varje körning av principen.
- **Håll mig inloggad (KMSI avgör)** – utökar sessionens livs längd genom att använda en beständig cookie. Om den här funktionen är aktive rad och användaren väljer den, förblir sessionen aktiv även när användaren har stängt och öppnat webbläsaren igen. Sessionen återkallas bara när användaren loggar ut. Funktionen KMSI avgör gäller bara för inloggning med lokala konton. KMSI avgör-funktionen har högre prioritet än sessionens livs längd.

::: zone pivot="b2c-user-flow"

Konfigurera sessionens beteende:

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Kontrol lera att du använder den katalog som innehåller din Azure AD B2C klient genom att välja filtret **katalog + prenumeration** på den översta menyn och välja den katalog som innehåller din Azure AD B2C-klient.
1. Välj **Alla tjänster** på menyn högst upp till vänster i Azure-portalen och sök efter och välj **Azure AD B2C**.
1. Välj **användar flöden**.
1. Öppna det användar flöde som du skapade tidigare.
1. Välj **Egenskaper**.
1. Konfigurera **livs längd för webbappens session (minuter)**, **tids gräns för webbapp**, **konfiguration av enkel inloggning** och **Kräv ID-token i utloggnings begär Anden** efter behov.
1. Klicka på **Spara**.

::: zone-end

::: zone pivot="b2c-custom-policy"

Om du vill ändra sessionens beteende och SSO-konfigurationer lägger du till ett **UserJourneyBehaviors** -element i [RelyingParty](relyingparty.md) -elementet.  **UserJourneyBehaviors** -elementet måste omedelbart följa **DefaultUserJourney**. Ditt **UserJourneyBehavors** -element bör se ut som i det här exemplet:

```xml
<UserJourneyBehaviors>
   <SingleSignOn Scope="Application" />
   <SessionExpiryType>Absolute</SessionExpiryType>
   <SessionExpiryInSeconds>86400</SessionExpiryInSeconds>
</UserJourneyBehaviors>
```
::: zone-end

## <a name="enable-keep-me-signed-in-kmsi"></a>Aktivera Håll mig inloggad (KMSI avgör)

Du kan aktivera funktionen KMSI avgör för användare av dina webb program och interna program som har lokala konton i din Azure AD B2C katalog. När du aktiverar funktionen kan användarna välja att förbli inloggade så att sessionen förblir aktiv när de har stängt webbläsaren. Sedan kan de öppna webbläsaren igen utan att uppmanas att ange sitt användar namn och lösen ord igen. Den här åtkomsten återkallas när en användare loggar ut.

![Exempel på registrerings inloggnings sida med kryss rutan Håll mig inloggad](./media/session-behavior/keep-me-signed-in.png)


::: zone pivot="b2c-user-flow"

KMSI avgör kan konfigureras på den enskilda användar flödes nivån. Innan du aktiverar KMSI avgör för dina användar flöden bör du tänka på följande:

- KMSI avgör stöds endast för de **rekommenderade** versionerna av registrering och inloggning (SUSI), inloggning och profil redigering av användar flöden. Om du för närvarande  har standard **-eller äldre** versioner av dessa användar flöden och vill aktivera KMSI avgör måste du skapa nya, **rekommenderade** versioner av dessa användar flöden.
- KMSI avgör stöds inte med lösen ords återställning eller registrerings användar flöden.
- Om du vill aktivera KMSI avgör för alla program i din klient organisation rekommenderar vi att du aktiverar KMSI avgör för alla användar flöden i din klient organisation. Eftersom en användare kan visas med flera principer under en session, är det möjligt att de kan upptäcka en som inte har KMSI avgör aktiverat, vilket skulle ta bort KMSI avgör-cookien från sessionen.
- KMSI avgör bör inte aktive ras på offentliga datorer.

### <a name="configure-kmsi-for-a-user-flow"></a>Konfigurera KMSI avgör för ett användar flöde

Så här aktiverar du KMSI avgör för ditt användar flöde:

1. Logga in på [Azure-portalen](https://portal.azure.com).
2. Kontrol lera att du använder den katalog som innehåller din Azure AD B2C-klient. Välj **katalog + prenumerations**   filter på den översta menyn och välj den katalog som innehåller Azure AD B2C klienten.
3. Välj **alla tjänster**   i det övre vänstra hörnet av Azure Portal och Sök sedan efter och välj **Azure AD B2C**.
4. Välj **användar flöden (principer)**.
5. Öppna det användar flöde som du skapade tidigare.
6. Välj **Egenskaper**.

7. Under  **sessions beteende** väljer du **Aktivera Behåll mig inloggad session**. Bredvid **Håll mig inloggad session (dagar)** anger du ett värde mellan 1 och 90 för att ange antalet dagar som en session kan vara öppen.


   ![Aktivera Behåll mig inloggad session](media/session-behavior/enable-keep-me-signed-in.png)

::: zone-end

::: zone pivot="b2c-custom-policy"

Användarna bör inte aktivera det här alternativet på offentliga datorer.

### <a name="configure-the-page-identifier"></a>Konfigurera sid identifieraren

Om du vill aktivera KMSI avgör anger du innehålls definitions `DataUri` elementet till [sid-ID](contentdefinitions.md#datauri) `unifiedssp` och [sid version](page-layout.md) *1.1.0* eller senare.

1. Öppna tilläggs filen för principen. Till exempel <em>`SocialAndLocalAccounts/`**`TrustFrameworkExtensions.xml`**</em> . Den här tilläggs filen är en av de principfiler som ingår i den anpassade principens start paket, som du borde ha skaffat i förutsättningen, [Kom igång med anpassade principer](custom-policy-get-started.md).
1. Sök efter **BuildingBlocks** -elementet. Om elementet inte finns lägger du till det.
1. Lägg till elementet **ContentDefinitions** i **BuildingBlocks** -elementet för principen.

    Den anpassade principen bör se ut som följande kodfragment:

    ```xml
    <BuildingBlocks>
      <ContentDefinitions>
        <ContentDefinition Id="api.signuporsignin">
          <DataUri>urn:com:microsoft:aad:b2c:elements:unifiedssp:1.1.0</DataUri>
        </ContentDefinition>
      </ContentDefinitions>
    </BuildingBlocks>
    ```

### <a name="add-the-metadata-to-the-self-asserted-technical-profile"></a>Lägg till metadata till den självkontrollerade tekniska profilen

För att lägga till KMSI avgör-rutan på sidan för registrering och inloggning ställer du in `setting.enableRememberMe` metadata på True. Åsidosätt de tekniska profilerna för SelfAsserted-LocalAccountSignin-email i tilläggs filen.

1. Hitta ClaimsProviders-elementet. Om elementet inte finns lägger du till det.
1. Lägg till följande anspråks leverantör i ClaimsProviders-elementet:

```xml
<ClaimsProvider>
  <DisplayName>Local Account</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="SelfAsserted-LocalAccountSignin-Email">
      <Metadata>
        <Item Key="setting.enableRememberMe">True</Item>
      </Metadata>
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

1. Spara tilläggs filen.

### <a name="configure-a-relying-party-file"></a>Konfigurera en förlitande parts fil

Uppdatera den förlitande parten (RP) som initierar användar resan som du har skapat.

1. Öppna din anpassade princip fil. Till exempel *SignUpOrSignin.xml*.
1. Lägg till en `<UserJourneyBehaviors>` underordnad nod till noden om den inte redan finns `<RelyingParty>` . Den måste finnas omedelbart efter `<DefaultUserJourney ReferenceId="User journey Id" />` , till exempel: `<DefaultUserJourney ReferenceId="SignUpOrSignIn" />` .
1. Lägg till följande nod som underordnad till `<UserJourneyBehaviors>` elementet.

    ```xml
    <UserJourneyBehaviors>
      <SingleSignOn Scope="Tenant" KeepAliveInDays="30" />
      <SessionExpiryType>Absolute</SessionExpiryType>
      <SessionExpiryInSeconds>1200</SessionExpiryInSeconds>
    </UserJourneyBehaviors>
    ```

Vi rekommenderar att du anger värdet för SessionExpiryInSeconds till en kort period (1200 sekunder), medan värdet för KeepAliveInDays kan anges till en relativt lång period (30 dagar), vilket visas i följande exempel:

```xml
<RelyingParty>
  <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
  <UserJourneyBehaviors>
    <SingleSignOn Scope="Tenant" KeepAliveInDays="30" />
    <SessionExpiryType>Absolute</SessionExpiryType>
    <SessionExpiryInSeconds>1200</SessionExpiryInSeconds>
  </UserJourneyBehaviors>
  <TechnicalProfile Id="PolicyProfile">
    <DisplayName>PolicyProfile</DisplayName>
    <Protocol Name="OpenIdConnect" />
    <OutputClaims>
      <OutputClaim ClaimTypeReferenceId="displayName" />
      <OutputClaim ClaimTypeReferenceId="givenName" />
      <OutputClaim ClaimTypeReferenceId="surname" />
      <OutputClaim ClaimTypeReferenceId="email" />
      <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub"/>
      <OutputClaim ClaimTypeReferenceId="identityProvider" />
      <OutputClaim ClaimTypeReferenceId="tenantId" AlwaysUseDefaultValue="true" DefaultValue="{Policy:TenantObjectId}" />
    </OutputClaims>
    <SubjectNamingInfo ClaimType="sub" />
  </TechnicalProfile>
</RelyingParty>
```

::: zone-end

## <a name="sign-out"></a>Logga ut

När du vill signera användaren från programmet räcker det inte att ta bort programmets cookies eller på annat sätt avsluta sessionen med användaren. Du måste omdirigera användaren till Azure AD B2C för att kunna logga ut. Annars kan användaren kunna autentisera till dina program igen utan att ange sina autentiseringsuppgifter igen.

Vid en inloggningsbegäran Azure AD B2C:

1. Invaliderar den Azure AD B2C cookie-baserade sessionen.
::: zone pivot="b2c-user-flow"
2. Försök att logga ut från federerade identitets leverantörer
::: zone-end
::: zone pivot="b2c-custom-policy"
3. Försök att logga ut från federerade identitets leverantörer:
   - OpenId Connect – om den välkända konfigurations slut punkten för identitets leverantör anger en `end_session_endpoint` plats.
   - OAuth2 – om [identitets leverantörens metadata](oauth2-technical-profile.md#metadata) innehåller `end_session_endpoint` platsen.
   - SAML – om [identitets leverantörens metadata](saml-identity-provider-technical-profile.md#metadata) innehåller `SingleLogoutService` platsen.
4. Du kan också logga ut från andra program. Mer information finns i avsnittet om [enkel inloggning](#single-sign-out) .

> [!NOTE]
> Du kan inaktivera utloggningen från federerade identitets leverantörer genom att ange metadata för teknisk profil för identitetsprovider `SingleLogoutEnabled` till `false` .
::: zone-end

Utloggningen rensar användarens läge för enkel inloggning med Azure AD B2C, men det är inte säkert att användaren kan logga ut från sin session med social identitetsprovider. Om användaren väljer samma identitetsprovider vid en efterföljande inloggning, kan de autentiseras igen utan att ange sina autentiseringsuppgifter. Om en användare vill logga ut från programmet betyder det inte nödvändigt vis att de vill logga ut från sitt Facebook-konto. Men om lokala konton används avslutas användarens session korrekt.

::: zone pivot="b2c-custom-policy"

### <a name="single-sign-out"></a>Enkel utloggning 

När du omdirigerar användaren till Azure AD B2C utloggnings slut punkt (för både OAuth2-och SAML-protokoll) rensar Azure AD B2C användarens session från webbläsaren. Användaren kan dock fortfarande vara inloggad i andra program som använder Azure AD B2C för autentisering. Om du vill att dessa program ska kunna signera användaren samtidigt, skickar Azure AD B2C en HTTP GET-begäran till registrerade `LogoutUrl` för alla program som användaren för närvarande är inloggad på.

Program måste svara på den här begäran genom att rensa alla sessioner som identifierar användaren och returnera ett `200` svar. Om du vill stödja enkel utloggning i ditt program måste du implementera en `LogoutUrl` i program koden. 

För att stödja enkel utloggning måste token Issuer tekniska profiler för både JWT och SAML ange:

- Protokoll namnet, t. ex. `<Protocol Name="OpenIdConnect" />`
- Referens till teknisk profil för sessionen, till exempel `UseTechnicalProfileForSessionManagement ReferenceId="SM-OAuth-issuer" />` .

I följande exempel illustrerar JWT-och SAML-token-utfärdare med enkel utloggning:

```xml
<ClaimsProvider>
  <DisplayName>Local Account SignIn</DisplayName>
  <TechnicalProfiles>
    <!-- JWT Token Issuer -->
    <TechnicalProfile Id="JwtIssuer">
      <DisplayName>JWT token Issuer</DisplayName>
      <Protocol Name="OpenIdConnect" />
      <OutputTokenFormat>JWT</OutputTokenFormat>
      ...    
      <UseTechnicalProfileForSessionManagement ReferenceId="SM-OAuth-issuer" />
    </TechnicalProfile>

    <!-- Session management technical profile for OIDC based tokens -->
    <TechnicalProfile Id="SM-OAuth-issuer">
      <DisplayName>Session Management Provider</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.OAuthSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
    </TechnicalProfile>

    <!--SAML token issuer-->
    <TechnicalProfile Id="Saml2AssertionIssuer">
      <DisplayName>SAML token issuer</DisplayName>
      <Protocol Name="SAML2" />
      <OutputTokenFormat>SAML2</OutputTokenFormat>
      ...
      <UseTechnicalProfileForSessionManagement ReferenceId="SM-Saml-issuer" />
    </TechnicalProfile>

    <!-- Session management technical profile for SAML based tokens -->
    <TechnicalProfile Id="SM-Saml-issuer">
      <DisplayName>Session Management Provider</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.SamlSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

::: zone-end

### <a name="secure-your-logout-redirect"></a>Skydda din utloggnings omdirigering

Efter utloggning omdirigeras användaren till den URI som anges i `post_logout_redirect_uri` parametern, oavsett vilka svars-URL: er som har angetts för programmet. Men om ett giltigt `id_token_hint` värde skickas och **Kräv ID-token i utloggnings begär Anden** aktive ras, verifierar Azure AD B2C att värdet för `post_logout_redirect_uri` matchar ett av programmets konfigurerade omdirigerings-URI: er innan omdirigeringen utförs. Om ingen matchande svars-URL har kon figurer ATS för programmet visas ett fel meddelande och användaren omdirigeras inte. 

::: zone pivot="b2c-user-flow"

Så här kräver du en ID-token i utloggnings begär Anden:

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Kontrol lera att du använder den katalog som innehåller din Azure AD B2C klient genom att välja filtret **katalog + prenumeration** på den översta menyn och välja den katalog som innehåller din Azure AD B2C-klient.
1. Välj **Alla tjänster** på menyn högst upp till vänster i Azure-portalen och sök efter och välj **Azure AD B2C**.
1. Välj **användar flöden**.
1. Öppna det användar flöde som du skapade tidigare.
1. Välj **Egenskaper**.
1. Aktivera **Kräv ID-token i utloggnings begär Anden**.
1. Gå tillbaka till  **Azure AD B2C**.
1. Välj **Appregistreringar** och välj sedan ditt program.
1. Välj **Autentisering**.
1. I text rutan **utloggnings-URL** skriver du in återutloggning omdirigerings-URI och väljer sedan **Spara**.

::: zone-end

::: zone pivot="b2c-custom-policy"

Om du vill kräva en ID-token i utloggnings begär Anden lägger du till ett **UserJourneyBehaviors** -element i [RelyingParty](relyingparty.md) -elementet. Ange sedan **EnforceIdTokenHintOnLogout** för **SingleSignOn** -elementet till `true` . Ditt **UserJourneyBehaviors** -element bör se ut som i det här exemplet:

```xml
<UserJourneyBehaviors>
  <SingleSignOn Scope="Tenant" EnforceIdTokenHintOnLogout="true"/>
</UserJourneyBehaviors>
```

::: zone-end

Så här konfigurerar du webb adressen för din program utloggning:

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Kontrol lera att du använder den katalog som innehåller din Azure AD B2C klient genom att välja filtret **katalog + prenumeration** på den översta menyn och välja den katalog som innehåller din Azure AD B2C-klient.
1. Välj **Alla tjänster** på menyn högst upp till vänster i Azure-portalen och sök efter och välj **Azure AD B2C**.
1. Välj **Appregistreringar** och välj sedan ditt program.
1. Välj **Autentisering**.
1. I text rutan **utloggnings-URL** skriver du in återutloggning omdirigerings-URI och väljer sedan **Spara**.

## <a name="next-steps"></a>Nästa steg

- Lär dig hur du [konfigurerar tokens i Azure AD B2C](configure-tokens.md).
