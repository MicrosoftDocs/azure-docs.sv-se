---
title: TypingDNA med Azure Active Directory B2C
titleSuffix: Azure AD B2C
description: Lär dig hur du integrerar Azure AD B2C-autentisering med TypingDNA för att få hjälp med identitets verifiering och bevisning baserat på användarens Skriv mönster, tillhandahåller ID-verifierings lösningar som tvingar Multi-Factor Authentication och hjälper till att uppfylla SCA-kraven för Payment Services-direktiv 2 (PSD2).
author: gargi-sinha
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 06/25/2020
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: 68617d86fda940c5d3752f2389088a8c729aebec
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "97108356"
---
# <a name="tutorial-for-configuring-typingdna-with-azure-active-directory-b2c"></a>Självstudie för att konfigurera TypingDNA med Azure Active Directory B2C

I den här genom gången lär du dig att integrera en exempel-app för online-betalning i Azure Active Directory B2C med TypingDNA-appen. Med hjälp av TypingDNA-appen kan Azure AD B2C kunder följa PSD2 ( [Payment Services direktiv 2](https://www.typingdna.com/use-cases/sca-strong-customer-authentication) ) transaktions krav med hjälp av tangenttryckning och stark kundautentisering. Mer information om TypingDNA finns [här](https://www.typingdna.com/).

 Azure AD B2C använder TypingDNA-teknikerna för att avbilda användarnas Skriv egenskaper och de registreras och analyseras för att känna sig på varje autentisering. Detta lägger till ett skydds lager som är relaterat till riskiness för en autentisering och utvärderar risk nivåerna. Azure AD B2C kan anropa andra metoder för att ge ytterligare säkerhet som användaren är som han eller hon utger sig för att aktivera Azure AD MFA, framtvinga e-postverifiering eller någon annan anpassad logik för ditt scenario.

>[!NOTE]
> Den här exempel principen baseras på [SocialAndLocalAccountsWithMfa](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/SocialAndLocalAccountsWithMfa) start paket.

## <a name="scenario-description"></a>Scenariobeskrivning

![Skärm bild av TypingDNA Architecture-diagram](./media/partner-typingdna/typingdna-architecture-diagram.png)

### <a name="sign-up"></a>Registrera dig

1. Azure AD B2C Pages använder TypingDNAs JavaScript-bibliotek för att registrera användarens Skriv mönster. Användar namnet och lösen ordet registreras till exempel vid registrering för den första registreringen och sedan vid varje inloggning för verifiering.

2. När användaren skickar sidan, kommer TypingDNA-biblioteket att beräkna användarens Skriv egenskaper. Sedan infogar du informationen i ett dolt textfält som Azure AD B2C har Render ATS. Det här fältet är dolt med CSS.  

    [Exemplet innehåller HTML-filer](https://github.com/azure-ad-b2c/partner-integrations/blob/master/samples/TypingDNA/source-code/selfAssertedSignUp.cshtml) med Java Script-och CSS-ändringar och refereras till av `api.selfasserted.tdnasignin` `api.selfasserted.tdnasignup` innehålls definitionerna och. Läs om [hur du använder sid innehållet](./customize-ui-with-html.md#hosting-the-page-content) som värd för dina HTML-filer.

3. Azure AD B2C har nu Skriv mönstret inom anspråks säcken när användaren skickar sina autentiseringsuppgifter. Det måste anropa ett API (ditt) för att skicka dessa data till TypingDNA REST API-slutpunkten. Detta API ingår i [exemplet (typingDNA-API-Interface)](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/TypingDNA/source-code/TypingDNA-API-Interface).
4. API för mellanlager skickar sedan inmatnings mönster data till TypingDNA REST API. Vid registreringen uppmanas du att [kontrol lera användar slut punkten](https://api.typingdna.com/index.html#api-API_Services-GetUser) för att bekräfta att användaren inte fanns och sedan anropas slut punkten [Spara mönster](https://api.typingdna.com/index.html#api-API_Services-saveUserPattern) för att spara användarens första Skriv mönster.

> [!NOTE]
> Alla anrop till TypingDNA REST API-slutpunkten skickar ett UserId. Detta måste vara ett hashat värde. Azure AD B2C använder `HashObjectIdWithEmail` anspråks omvandlingen för att Hasha e-postmeddelandet med slumpmässig salt och hemlighet.  

REST API-anropen modelleras med `validationTechnicalProfiles` i `LocalAccountSignUpWithLogonEmail-TDNA` :

```xml

<ValidationTechnicalProfiles>

    <ValidationTechnicalProfile ReferenceId="AAD-UserWriteUsingLogonEmail-TDNA" />
    <ValidationTechnicalProfile ReferenceId="REST-TDNA-CheckUser" ContinueOnError="true"/>

    <ValidationTechnicalProfile ReferenceId="REST-TDNA-SaveUser"/>

</ValidationTechnicalProfiles>

```

### <a name="sign-in"></a>Logga in

Vid efterföljande inloggningar beräknas användarens Skriv mönster på samma sätt som vid registreringen med hjälp av den [anpassade HTML-koden](https://github.com/azure-ad-b2c/partner-integrations/blob/master/samples/TypingDNA/source-code/selfAssertedSignIn.cshtml). När Skriv profilen är inom Azure AD B2C-anspråks säcken anropar Azure AD B2C ditt API för att anropa TypingDNA REST API-slutpunkt. [Kontroll användarens](https://api.typingdna.com/index.html#api-API_Services-GetUser) slut punkt anropas för att bekräfta att användaren finns. [Kontrol lera](https://api.typingdna.com/index.html#api-API_Services-verifyTypingPattern) sedan att mönster slut punkten anropas för att returnera `net_score` . Detta `net_score` är en indikation på hur nära att Skriv mönstret var till originalet vid registreringen.

Det här Skriv mönstret modelleras med `validationTechnicalProfiles` i `SelfAsserted-LocalAccountSignin-Email-TDNA` :

```xml

<ValidationTechnicalProfiles>

    <ValidationTechnicalProfile ReferenceId="login-NonInteractive"/>

    <ValidationTechnicalProfile ReferenceId="REST-TDNA-CheckUser" ContinueOnError="false"/>

    <ValidationTechnicalProfile ReferenceId="REST-TDNA-VerifyUser"/>

    <ValidationTechnicalProfile ReferenceId="REST-TDNA-SaveUser">

        <Preconditions>

            <Precondition Type="ClaimEquals" ExecuteActionsIf="true">

            <Value>saveTypingPattern</Value>

            <Value>False</Value>

            <Action>SkipThisValidationTechnicalProfile</Action>

            </Precondition>

        </Preconditions>

    </ValidationTechnicalProfile>

</ValidationTechnicalProfiles>

```

 Om användaren får ett mönster som är högt `net_score` kan du spara det med hjälp av TypingDNA Spara slut punkt för [Skriv mönster](https://api.typingdna.com/index.html#api-API_Services-saveUserPattern) .  

Ditt API måste returnera ett anspråk  `saveTypingPattern` om du vill att TypingDNA ska kunna anropas av Azure AD B2C (via ditt API).

Exemplet i lagrings platsen innehåller ett API (TypingDNA-API-gränssnitt) som har kon figurer ATS med följande egenskaper.

- Tränings läge – om användaren har färre än två mönster sparade, ska du alltid uppmanas att ange MFA.

- Om användaren har 2-5 mönster sparade och `net_score` är lägre än 50, uppmanas du att ange MFA.

- Om användaren har 5 + mönster sparade och `net_score` är mindre än 65, uppmanas du att ange MFA.

Dessa tröskelvärden bör justeras i användnings fallet.

- När ditt API har utvärderat `net_score` ska det returnera ett booleskt anspråk till B2C- `promptMFA` .

- `promptMFA`Anspråket används inom ett för villkor för att villkorligt köra Azure AD MFA.

```xml

<OrchestrationStep Order="3" Type="ClaimsExchange">

    <Preconditions>

        <Precondition Type="ClaimsExist" ExecuteActionsIf="true">

            <Value>isActiveMFASession</Value>

            <Action>SkipThisOrchestrationStep</Action>

        </Precondition>

        <Precondition Type="ClaimEquals" ExecuteActionsIf="true">

            <Value>promptMFA</Value>

            <Value>False</Value>

            <Action>SkipThisOrchestrationStep</Action>

        </Precondition>

    </Preconditions>

    <ClaimsExchanges>

        <ClaimsExchange Id="PhoneFactor-Verify" TechnicalProfileReferenceId="PhoneFactor-InputOrVerify" />

    </ClaimsExchanges>

</OrchestrationStep>

```

## <a name="onboard-with-typingdna"></a>Publicera med TypingDNA

1. Registrera dig för TypingDNA [här](https://www.typingdna.com/)
2. Logga in på TypingDNA-instrumentpanelen och hämta **API-nyckel** och **API-hemlighet**. Detta kommer att behövas i installationen av API-gränssnittet senare

## <a name="integrate-typingdna-with-azure-ad-b2c"></a>Integrera TypingDNA med Azure AD B2C

1. Vara värd för det [TypingDNA-API-gränssnitt](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/TypingDNA/source-code/TypingDNA-API-Interface) som är värd leverantören som du väljer
2. Ersätt alla instanser av `apiKey` och `apiSecret` i [TypingDNA-API-gränssnitts](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/TypingDNA/source-code/TypingDNA-API-Interface) lösning med autentiseringsuppgifterna från din TypingDNA-instrumentpanel
3. Var värd för HTML-filerna i din leverantör som du väljer efter CORS-kraven [här](./customize-ui-with-html.md#3-configure-cors)
4. Ersätt LoadURI-elementen för `api.selfasserted.tdnasignup` `api.selfasserted.tdnasignin` -och-innehålls definitionerna i `TrustFrameworkExtensions.xml` filen till URI: n för dina värdbaserade HTML-filer.
5. Skapa en B2C-princip under ramverk för identitets upplevelse i Azure AD-bladet i **Azure Portal**. Använd `Generate` alternativet och namnge den här nyckeln `tdnaHashedId` .
6. Ersätt TenantId i principfiler
7. Ersätt ServiceURLs i alla TypingDNA-REST API tekniska profiler (REST-TDNA-VerifyUser, REST-TDNA-SaveUser, REST-TDNA-CheckUser) med slut punkten för API: et för [API-gränssnittet i TypingDNA](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/TypingDNA/source-code/TypingDNA-API-Interface).
8. Ladda upp [principfiler](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/TypingDNA/policy) till din klient organisation.

## <a name="test-the-user-flow"></a>Testa användar flödet

1. Öppna B2C-klienten och välj ett ramverk för identitets upplevelse.
2. Välj ditt tidigare skapade **användar flöde**.
3. Välj **Kör** användar flöde

    a. **Program** – Välj den registrerade appen (EXEMPLET är JWT)

    b. **Svars-URL** – Välj omdirigerings-URL

    c. Välj **Kör användar flöde**.
  
4. Gå igenom registrerings flödet och skapa ett konto
5. Logga ut
6. Gå igenom inloggnings flödet
7. Det resulterande JWT-resultatet visar TypingDNA-resultaten

## <a name="live-version"></a>Live-version

• MFA har inaktiverats i den här test versionen, men du kan se resultatet av huruvida MFA har tillfrågats av anspråket `promptMFA` efter autentisering.

• Registrera dig [här](https://b2cprod.b2clogin.com/b2cprod.onmicrosoft.com/oauth2/v2.0/authorize?p=B2C_1A_SU_TDNA&client_id=51d907f8-db14-4460-a1fd-27eaeb2a74da&nonce=defaultNonce&redirect_uri=https://jwt.ms/&scope=openid&response_type=id_token&prompt=login) och logga in [här](https://b2cprod.b2clogin.com/b2cprod.onmicrosoft.com/oauth2/v2.0/authorize?p=B2C_1A_SI_TDNA&client_id=51d907f8-db14-4460-a1fd-27eaeb2a74da&nonce=defaultNonce&redirect_uri=https://jwt.ms/&scope=openid&response_type=id_token&prompt=login)

## <a name="next-steps"></a>Nästa steg

Mer information finns i följande artiklar:

- [Anpassade principer i AAD B2C](./custom-policy-overview.md)

- [Kom igång med anpassade principer i AAD B2C](./custom-policy-get-started.md?tabs=applications)