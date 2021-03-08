---
title: Konfigurera registrering och inloggning med ett Twitter-konto
titleSuffix: Azure AD B2C
description: Tillhandahålla registrering och inloggning till kunder med Twitter-konton i dina program med hjälp av Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/08/2021
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 8cb31f57e5403e99e2ef9bfcc5d1042e33516d1d
ms.sourcegitcommit: f6193c2c6ce3b4db379c3f474fdbb40c6585553b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/08/2021
ms.locfileid: "102448157"
---
# <a name="set-up-sign-up-and-sign-in-with-a-twitter-account-using-azure-active-directory-b2c"></a>Konfigurera registrering och inloggning med ett Twitter-konto med hjälp av Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]
::: zone pivot="b2c-custom-policy"

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

::: zone-end

## <a name="prerequisites"></a>Förutsättningar

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

## <a name="create-an-application"></a>Skapa ett program

Om du vill aktivera inloggning för användare med ett Twitter-konto i Azure AD B2C måste du skapa ett Twitter-program. Om du inte redan har ett Twitter-konto kan du registrera dig på [https://twitter.com/signup](https://twitter.com/signup) . Du måste också [ansöka om ett Developer-konto](https://developer.twitter.com/en/apply/user.html). Mer information finns i [tillämpa för åtkomst](https://developer.twitter.com/en/apply-for-access).

1. Logga in på [Twitter Developer-portalen](https://developer.twitter.com/portal/projects-and-apps) med dina Twitter-kontoautentiseringsuppgifter.
1. Under **fristående appar** väljer du **+ skapa app**.
1. Ange ett **namn på appen** och välj sedan **Slutför**.
1. Kopiera värdet för **appens nyckel** och **API Key Secret**.  Du använder båda alternativen för att konfigurera Twitter som en identitets leverantör i din klient organisation. 
1. Under **Konfigurera din app** väljer du **app-inställningar**.
1. Under **autentiseringsinställningar** väljer du **Redigera**
    1. Markera kryss rutan **Aktivera 3-ledade OAuth** .
    1. Kryss rutan Markera **begär e-postadress från användare** .
    1. För **återanrops-URL: er** anger du `https://your-tenant.b2clogin.com/your-tenant.onmicrosoft.com/your-user-flow-Id/oauth1/authresp` . Ersätt `your-tenant` med namnet på ditt klient namn och `your-user-flow-Id` med identifieraren för ditt användar flöde. Till exempel `b2c_1a_signup_signin_twitter`. Använd gemener när du anger ditt klient namn och ditt användar flödes-ID även om de definieras med versaler i Azure AD B2C.
    1. För **webbplats-URL: en** anger du `https://your-tenant.b2clogin.com` . Ersätt `your-tenant` med namnet på din klient. Till exempel `https://contosob2c.b2clogin.com`.
    1. Ange en URL för **tjänst villkoren**, till exempel `http://www.contoso.com/tos` . Princip-URL: en är en sida som du upprätthåller för att tillhandahålla villkor för ditt program.
    1. Ange en URL för **Sekretess policyn**, till exempel `http://www.contoso.com/privacy` . Princip-URL: en är en sida som du upprätthåller för att tillhandahålla sekretess information för ditt program.
    1. Välj **Spara**.

::: zone pivot="b2c-user-flow"

## <a name="configure-twitter-as-an-identity-provider"></a>Konfigurera Twitter som identitets leverantör

1. Logga in på [Azure Portal](https://portal.azure.com/) som global administratör för Azure AD B2C-klientorganisationen.
1. Kontrol lera att du använder den katalog som innehåller din Azure AD B2C klient genom att välja filtret **katalog + prenumeration** på den översta menyn och välja den katalog som innehåller din klient.
1. Välj **Alla tjänster** på menyn högst upp till vänster i Azure-portalen och sök efter och välj **Azure AD B2C**.
1. Välj **identitets leverantörer** och välj sedan **Twitter**.
1. Ange ett **namn**. Till exempel *Twitter*.
1. För **klient-ID** anger du *API-nyckeln* för det Twitter-program som du skapade tidigare.
1. Ange den *API-nyckel hemlighet* som du har spelat in för **klient hemligheten**.
1. Välj **Spara**.

## <a name="add-twitter-identity-provider-to-a-user-flow"></a>Lägg till Twitter Identity Provider i ett användar flöde 

1. Välj **användar flöden** i Azure AD B2C klient.
1. Välj det användar flöde som du vill lägga till Twitter-identitetsprovider.
1. Under **leverantörer av sociala identitet** väljer du **Twitter**.
1. Välj **Spara**.
1. Om du vill testa principen väljer du **Kör användar flöde**.
1. För **program** väljer du det webb program som heter *testapp1* som du tidigare har registrerat. **Svars-URL: en** ska visas `https://jwt.ms` .
1. Välj knappen **Kör användar flöde** .
1. På sidan registrering eller inloggning väljer du **Twitter** för att logga in med Twitter-konto.

Om inloggnings processen lyckas omdirigeras webbläsaren till `https://jwt.ms` , som visar innehållet i den token som returnerades av Azure AD B2C.

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="create-a-policy-key"></a>Skapa en princip nyckel

Du måste lagra den hemliga nyckeln som du tidigare har registrerat i Azure AD B2C-klienten.

1. Logga in på [Azure-portalen](https://portal.azure.com/).
2. Kontrol lera att du använder den katalog som innehåller din Azure AD B2C-klient. Välj **katalog + prenumerations** filter på den översta menyn och välj den katalog som innehåller din klient.
3. Välj **Alla tjänster** på menyn högst upp till vänster i Azure-portalen och sök efter och välj **Azure AD B2C**.
4. På sidan Översikt väljer du **ID för identitets miljö**.
5. Välj **princip nycklar** och välj sedan **Lägg till**.
6. För **alternativ** väljer du `Manual` .
7. Ange ett **namn** för princip nyckeln. Till exempel `TwitterSecret`. Prefixet `B2C_1A_` läggs till automatiskt till namnet på din nyckel.
8. I **hemlighet** anger du din klient hemlighet som du tidigare har spelat in.
9. För **nyckel användning** väljer du `Encryption` .
10. Klicka på **Skapa**.

## <a name="configure-twitter-as-an-identity-provider"></a>Konfigurera Twitter som identitets leverantör

Om du vill att användarna ska kunna logga in med ett Twitter-konto måste du definiera kontot som en anspråks leverantör som Azure AD B2C kan kommunicera med via en slut punkt. Slut punkten innehåller en uppsättning anspråk som används av Azure AD B2C för att verifiera att en speciell användare har autentiserats.

Du kan definiera ett Twitter-konto som en anspråks leverantör genom att lägga till det i **ClaimsProviders** -elementet i principens tilläggs fil.

1. Öppna *TrustFrameworkExtensions.xml*.
2. Hitta **ClaimsProviders** -elementet. Om den inte finns lägger du till den under rot elementet.
3. Lägg till en ny **ClaimsProvider** enligt följande:

    ```xml
    <ClaimsProvider>
      <Domain>twitter.com</Domain>
      <DisplayName>Twitter</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="Twitter-OAuth1">
          <DisplayName>Twitter</DisplayName>
          <Protocol Name="OAuth1" />
          <Metadata>
            <Item Key="ProviderName">Twitter</Item>
            <Item Key="authorization_endpoint">https://api.twitter.com/oauth/authenticate</Item>
            <Item Key="access_token_endpoint">https://api.twitter.com/oauth/access_token</Item>
            <Item Key="request_token_endpoint">https://api.twitter.com/oauth/request_token</Item>
            <Item Key="ClaimsEndpoint">https://api.twitter.com/1.1/account/verify_credentials.json?include_email=true</Item>
            <Item Key="ClaimsResponseFormat">json</Item>
            <Item Key="client_id">Your Twitter application API key</Item>
          </Metadata>
          <CryptographicKeys>
            <Key Id="client_secret" StorageReferenceId="B2C_1A_TwitterSecret" />
          </CryptographicKeys>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="user_id" />
            <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="screen_name" />
            <OutputClaim ClaimTypeReferenceId="email" />
            <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="twitter.com" />
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" />
          </OutputClaims>
          <OutputClaimsTransformations>
            <OutputClaimsTransformation ReferenceId="CreateRandomUPNUserName" />
            <OutputClaimsTransformation ReferenceId="CreateUserPrincipalName" />
            <OutputClaimsTransformation ReferenceId="CreateAlternativeSecurityId" />
            <OutputClaimsTransformation ReferenceId="CreateSubjectClaimFromAlternativeSecurityId" />
          </OutputClaimsTransformations>
          <UseTechnicalProfileForSessionManagement ReferenceId="SM-SocialLogin" />
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

4. Ersätt värdet för **client_id** med den *API-nyckel hemlighet* som du tidigare har spelat in.
5. Spara filen.

[!INCLUDE [active-directory-b2c-add-identity-provider-to-user-journey](../../includes/active-directory-b2c-add-identity-provider-to-user-journey.md)]


```xml
<OrchestrationStep Order="1" Type="CombinedSignInAndSignUp" ContentDefinitionReferenceId="api.signuporsignin">
  <ClaimsProviderSelections>
    ...
    <ClaimsProviderSelection TargetClaimsExchangeId="TwitterExchange" />
  </ClaimsProviderSelections>
  ...
</OrchestrationStep>

<OrchestrationStep Order="2" Type="ClaimsExchange">
  ...
  <ClaimsExchanges>
    <ClaimsExchange Id="TwitterExchange" TechnicalProfileReferenceId="Twitter-OAuth1" />
  </ClaimsExchanges>
</OrchestrationStep>
```

[!INCLUDE [active-directory-b2c-configure-relying-party-policy](../../includes/active-directory-b2c-configure-relying-party-policy-user-journey.md)]

## <a name="test-your-custom-policy"></a>Testa din anpassade princip

1. Välj en princip för förlitande part, till exempel `B2C_1A_signup_signin` .
1. För **program** väljer du ett webb program som du [har registrerat tidigare](troubleshoot-custom-policies.md#troubleshoot-the-runtime). **Svars-URL: en** ska visas `https://jwt.ms` .
1. Välj knappen **Kör nu** .
1. På sidan registrering eller inloggning väljer du **Twitter** för att logga in med Twitter-konto.

Om inloggnings processen lyckas omdirigeras webbläsaren till `https://jwt.ms` , som visar innehållet i den token som returnerades av Azure AD B2C.

::: zone-end
