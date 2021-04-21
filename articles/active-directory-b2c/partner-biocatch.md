---
title: Självstudie för att konfigurera BioCatch med Azure Active Directory B2C
titleSuffix: Azure AD B2C
description: Självstudie för att konfigurera Azure Active Directory B2C med BioCatch för att identifiera riskfyllda och bedrägliga användare
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 04/20/2021
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: f15cc294a0d3d930548d7d9bdf1d05b552b60fa5
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2021
ms.locfileid: "107819442"
---
# <a name="tutorial-configure-biocatch-with-azure-active-directory-b2c"></a>Självstudie: Konfigurera BioCatch med Azure Active Directory B2C

I den här självstudien lär du dig att integrera Azure Active Directory (AD) B2C-autentisering med [BioCatch](https://www.biocatch.com/) för att ytterligare utöka säkerhetspositionen för customer Identity and Access Management (CIAM). BioCatch analyserar en användares fysiska och kognitiva digitala beteenden för att generera insikter som skiljer mellan legitima kunder och cyberbrottsligheter.

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du:

- En Azure-prenumeration. Om du inte har en prenumeration kan du skaffa ett [kostnadsfritt konto.](https://azure.microsoft.com/free/)

- [En Azure AD B2C-klientorganisation](tutorial-create-tenant.md) som är länkad till din Azure-prenumeration.

- Ett [BioCatch-konto.](https://www.biocatch.com/contact-us)

## <a name="scenario-description"></a>Scenariobeskrivning

BioCatch-integreringen innehåller följande komponenter:

- **En webbapp eller webbtjänst** – Användaren bläddrar först till den här webbtjänsten. Den här webbtjänsten instansierar ett unikt klientsessions-ID som skickas till BioCatch. Klientsessions-ID:t börjar sedan omedelbart överföra egenskaper för användarbeteende till BioCatch.

- **En metod** – Skickar det unika klientsessions-ID:t till Azure AD B2C. I det angivna exemplet används JavaScript för att mata in värdet i ett dolt HTML-fält.

- **Ett Azure AD B2C anpassat användargränssnitt** – Döljer ett HTML-fält för indata för klientsessions-ID från JavaScript, om du använder metoden ovan

- **Azure AD B2C anpassad princip**

  - Tar det anpassade klientsessions-ID:t från användargränssnittet i form av ett anspråk. Detta uppnås via en egen infogad teknisk profil

  - Integrerar med BioCatch via en REST API-anspråksprovider och skickar klientsessions-ID:t till BioCatch-plattformen

  - Flera anpassade anspråk returneras från BioCatch för att den anpassade principlogiken sedan ska fungera

  - En userjourney, som utvärderar ett returnerat anspråk, till exempel sessionsrisk, och villkorsstyrd kör en åtgärd, till exempel anropa multifaktorautentisering (MFA).

![Diagram över arkitekturen för biofångst.](media/partner-biocatch/biocatch-architecture-diagram.png)

| Steg  | Description |
|:---|:-----------------------|
|1a     | Användaren bläddrar i webbtjänsten. Webbtjänsten returnerar sedan HTML-, CSS- eller JavaScript-värden och konfigurerar för att läsa in BioCatch JavaScript SDK. JavaScript på klientsidan konfigurerar/anger klientsessions-ID för BioCatch SDK. Webbtjänsten kan också förkonfigurera klientsessions-ID och skicka till klienten.        |
|1b    |  Konfigurera den instansierade JavaScript SDK:n för BioCatch mot BioCatch-plattformen. Börja omedelbart att skicka egenskaper för användarbeteende till BioCatch från klientenhet med hjälp av klientsessions-ID:t från steg 1a.    |
|2     |  Användaren försöker registrera sig/logga in och omdirigeras till Azure AD B2C.      |
|3a    | En del av userjourney är en självbetjänad anspråksprovider som tar klientsessions-ID:t som indata. Det här fältet är dolt på skärmen. Du kan använda JavaScript för att ange sessions-ID:t i fältet. Välj *nästa* knapp för att fortsätta registrering/inloggningsprocessen.|
|3b     | Klientsessions-ID:t skickas till BioCatch-plattformen för att fastställa en riskpoäng. |
|3c     | BioCatch returnerar information om sessionen, till exempel riskpoäng, och en rekommendation om vad som ska göra – tillåt eller blockera |
|3d    |Userjourney har ett steg för villkorsstyrd kontroll som agerar på de anspråk som returneras|
| 4   | Baserat på resultatet av den villkorliga kontrollen anropas en åtgärd, till exempel *stegvis MFA*|
|5    | När som helst från det att användaren först kommer till webbtjänstsidan kan webbtjänsten använda klientsessions-ID:t för att fråga BioCatch-API:et för att fastställa riskpoäng och sessionsinformation i realtid. |

## <a name="onboard-with-biocatch"></a>Publicera med BioCatch

Kontakta [BioCatch](https://www.biocatch.com/contact-us) och skapa ett konto.

## <a name="configure-the-custom-ui"></a>Konfigurera det anpassade användargränssnittet

Vi rekommenderar att du döljer fältet klientsessions-ID. Använd CSS, JavaScript eller någon annan metod för att dölja fältet. I testsyfte kan du visa fältet. JavaScript används till exempel för att dölja indatafältet som:

```
document.getElementById("clientSessionId").style.display = 'none';
```

## <a name="configure--azure-ad-b2c-identity-experience-framework-policies"></a>Konfigurera Azure AD B2C Identity Experience Framework principer

1. Konfigurera den första [anpassade principkonfigurationen.](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-get-started)

2. Skapa en ny fil som ärver från filtillägget.

    ```
    <BasePolicy> 

        <TenantId>tenant.onmicrosoft.com</TenantId> 

        <PolicyId>B2C_1A_TrustFrameworkExtensions</PolicyId> 

      </BasePolicy> 
    ```

3. Skapa en referens till det anpassade användargränssnittet för att dölja inmatningsrutan under resursen BuildingBlocks.

    ```
    <ContentDefinitions> 

        <ContentDefinition Id="api.selfasserted"> 

            <LoadUri>https://domain.com/path/to/selfAsserted.cshtml</LoadUri> 

            <DataUri>urn:com:microsoft:aad:b2c:elements:contract:selfasserted:2.1.0</DataUri> 

          </ContentDefinition> 

        </ContentDefinitions>
    ```

4. Lägg till följande anspråk under resursen BuildingBlocks.

    ```
    <ClaimsSchema> 

          <ClaimType Id="riskLevel"> 

            <DisplayName>Session risk level</DisplayName> 

            <DataType>string</DataType>       

          </ClaimType> 

          <ClaimType Id="score"> 

            <DisplayName>Session risk score</DisplayName> 

            <DataType>int</DataType>       

          </ClaimType> 

          <ClaimType Id="clientSessionId"> 

            <DisplayName>The ID of the client session</DisplayName> 

            <DataType>string</DataType> 

            <UserInputType>TextBox</UserInputType> 

          </ClaimType> 

    <ClaimsSchema> 
    ```

5. Konfigurera självinfogade anspråksprovider för fältet klientsessions-ID.

    ```
    <ClaimsProvider> 

          <DisplayName>Client Session ID Claims Provider</DisplayName> 

          <TechnicalProfiles> 

            <TechnicalProfile Id="login-NonInteractive-clientSessionId"> 

              <DisplayName>Client Session ID TP</DisplayName> 

              <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.SelfAssertedAttributeProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" /> 

              <Metadata> 

                <Item Key="ContentDefinitionReferenceId">api.selfasserted</Item> 

              </Metadata> 

              <CryptographicKeys> 

                <Key Id="issuer_secret" StorageReferenceId="B2C_1A_TokenSigningKeyContainer" /> 

              </CryptographicKeys> 

            <!—Claim we created earlier --> 

              <OutputClaims> 

                <OutputClaim ClaimTypeReferenceId="clientSessionId" Required="false" DefaultValue="100"/> 

              </OutputClaims> 

            <UseTechnicalProfileForSessionManagement ReferenceId="SM-AAD" /> 

            </TechnicalProfile> 

          </TechnicalProfiles> 

        </ClaimsProvider> 
    ```

6. Konfigurera REST API anspråksprovider för BioCatch. 

    ```
    <TechnicalProfile Id="BioCatch-API-GETSCORE"> 

          <DisplayName>Technical profile for BioCatch API to return session information</DisplayName> 

          <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" /> 

          <Metadata> 

            <Item Key="ServiceUrl">https://biocatch-url.com/api/v6/score?customerID=<customerid>&amp;action=getScore&amp;uuid=<uuid>&amp;customerSessionID={clientSessionId}&amp;solution=ATO&amp;activtyType=<activity_type>&amp;brand=<brand></Item> 

            <Item Key="SendClaimsIn">Url</Item> 

            <Item Key="IncludeClaimResolvingInClaimsHandling">true</Item> 

            <!-- Set AuthenticationType to Basic or ClientCertificate in production environments --> 

            <Item Key="AuthenticationType">None</Item> 

            <!-- REMOVE the following line in production environments --> 

            <Item Key="AllowInsecureAuthInProduction">true</Item> 

          </Metadata> 

          <InputClaims> 

            <InputClaim ClaimTypeReferenceId="clientsessionId" /> 

          </InputClaims> 

          <OutputClaims> 

            <OutputClaim ClaimTypeReferenceId="riskLevel" /> 

            <OutputClaim ClaimTypeReferenceId="score" /> 

          </OutputClaims> 

          <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" /> 

        </TechnicalProfile> 

      </TechnicalProfiles>
    ```

    > [!Note]
    > BioCatch tillhandahåller url, kund-ID och unikt användar-ID (uuID) som ska konfigureras. Kundens SessionsID-anspråk skickas som en frågesträngsparameter till BioCatch. Du kan välja aktivitetstyp, till exempel *MAKE_PAYMENT*.

7. Konfigurera userjourney; följ exemplet

   1. Hämta clientSessionID som ett anspråk

   1. Anropa BioCatch-API:et för att hämta sessionsinformationen

   1. Om den returnerade *anspråksrisken* är *lika med låg* hoppar du över steget för MFA, annars tvingar du användarens MFA

    ```
    <OrchestrationStep Order="8" Type="ClaimsExchange"> 

              <ClaimsExchanges> 

                <ClaimsExchange Id="clientSessionIdInput" TechnicalProfileReferenceId="login-NonInteractive-clientSessionId" /> 

              </ClaimsExchanges> 

            </OrchestrationStep> 

            <OrchestrationStep Order="9" Type="ClaimsExchange"> 

              <ClaimsExchanges> 

                <ClaimsExchange Id="BcGetScore" TechnicalProfileReferenceId=" BioCatch-API-GETSCORE" /> 

              </ClaimsExchanges> 

            </OrchestrationStep> 

            <OrchestrationStep Order="10" Type="ClaimsExchange"> 

              <Preconditions> 

                <Precondition Type="ClaimEquals" ExecuteActionsIf="true"> 

                  <Value>riskLevel</Value> 

                  <Value>LOW</Value> 

                  <Action>SkipThisOrchestrationStep</Action> 

                </Precondition> 

              </Preconditions> 

              <ClaimsExchanges> 

                <ClaimsExchange Id="PhoneFactor-Verify" TechnicalProfileReferenceId="PhoneFactor-InputOrVerify" /> 

              </ClaimsExchanges>  

    ```

8. Konfigurera konfiguration av förlitande part (valfritt)

    Det är användbart att skicka den BioCatch-returnerade informationen till ditt program som anspråk i token, särskilt *risknivå* och *poäng*.

    ```
    <RelyingParty> 

        <DefaultUserJourney ReferenceId="SignUpOrSignInMfa" /> 

        <UserJourneyBehaviors> 

          <SingleSignOn Scope="Tenant" KeepAliveInDays="30" /> 

          <SessionExpiryType>Absolute</SessionExpiryType> 

          <SessionExpiryInSeconds>1200</SessionExpiryInSeconds> 

          <ScriptExecution>Allow</ScriptExecution> 

        </UserJourneyBehaviors> 

        <TechnicalProfile Id="PolicyProfile"> 

          <DisplayName>PolicyProfile</DisplayName> 

          <Protocol Name="OpenIdConnect" /> 

          <OutputClaims> 

            <OutputClaim ClaimTypeReferenceId="displayName" /> 

            <OutputClaim ClaimTypeReferenceId="givenName" /> 

            <OutputClaim ClaimTypeReferenceId="surname" /> 

            <OutputClaim ClaimTypeReferenceId="email" /> 

            <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub" /> 

            <OutputClaim ClaimTypeReferenceId="identityProvider" />                 

            <OutputClaim ClaimTypeReferenceId="riskLevel" /> 

            <OutputClaim ClaimTypeReferenceId="score" /> 

            <OutputClaim ClaimTypeReferenceId="tenantId" AlwaysUseDefaultValue="true" DefaultValue="{Policy:TenantObjectId}" /> 

          </OutputClaims> 

          <SubjectNamingInfo ClaimType="sub" /> 

        </TechnicalProfile> 

      </RelyingParty> 

    ```

## <a name="integrate-with-azure-ad-b2c"></a>Integrera med Azure AD B2C

Följ dessa steg för att lägga till principfilerna i Azure AD B2C

1. Logga in på  [**Azure Portal**](https://portal.azure.com/) som global administratör för din Azure AD B2C klient.

2. Kontrollera att du använder den katalog som innehåller din Azure AD B2C klientorganisation. Välj **Katalog +**   prenumerationsfilter på den översta menyn och välj den katalog som innehåller din klientorganisation.

3. Välj **Alla tjänster** i det övre vänstra hörnet av   Azure Portal, sök efter och välj Azure AD B2C.

4. Gå till **Azure AD B2C**   >  **Identity Experience Framework**

3. Ladda upp alla principfiler till din klientorganisation.

## <a name="test-the-solution"></a>Testa lösningen

1. [Registrera ett dummy-program som omdirigerar till JWT.MS](https://docs.microsoft.com/azure/active-directory-b2c/tutorial-register-applications?tabs=app-reg-ga)  

2. Under **Identity Experience Framework** väljer du den princip som du skapade

3. I principfönstret väljer du dummy-JWT.MS appen och väljer **kör nu**

4. Gå igenom inloggningsflödet och skapa ett konto. Token som returneras JWT.MS bör ha 2 x anspråk för riskLevel och poäng. Följ exemplet.  

    ```
    { 

      "typ": "JWT", 

      "alg": "RS256", 

      "kid": "_keyid" 

    }.{ 

      "exp": 1615872580, 

      "nbf": 1615868980, 

      "ver": "1.0", 

      "iss": "https://tenant.b2clogin.com/12345678-1234-1234-1234-123456789012/v2.0/", 

      "sub": "12345678-1234-1234-1234-123456789012", 

      "aud": "12345678-1234-1234-1234-123456789012", 

      "acr": "b2c_1a_signup_signin_biocatch_policy", 

      "nonce": "defaultNonce", 

      "iat": 1615868980, 

      "auth_time": 1615868980, 

      "name": "John Smith", 

      "email": "john.smith@contoso.com", 

      "given_name": "John", 

      "family_name": "Smith", 

      "riskLevel": "LOW", 

      "score": 275, 

      "tid": "12345678-1234-1234-1234-123456789012" 

    }.[Signature]  

    ```

## <a name="additional-resources"></a>Ytterligare resurser

- [Anpassade principer i Azure AD B2C](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-overview)

- [Kom igång med anpassade principer i Azure AD B2C](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-get-started?tabs=applications)
