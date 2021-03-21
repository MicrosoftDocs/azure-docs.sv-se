---
title: Konfigurera registrering och inloggning med ett Google-konto
titleSuffix: Azure AD B2C
description: Tillhandahålla registrering och inloggning till kunder med Google-konton i dina program med hjälp av Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/17/2021
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 7d5786c4188db63efc3012e565071f8fd410b92f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "104579968"
---
# <a name="set-up-sign-up-and-sign-in-with-a-google-account-using-azure-active-directory-b2c"></a>Konfigurera registrering och inloggning med ett Google-konto med hjälp av Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-custom-policy"

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

::: zone-end

## <a name="prerequisites"></a>Förutsättningar

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]


## <a name="create-a-google-application"></a>Skapa ett Google-program

Om du vill aktivera inloggning för användare med ett Google-konto i Azure Active Directory B2C (Azure AD B2C) måste du skapa ett program i [Google Developer-konsolen](https://console.developers.google.com/). Mer information finns i [Konfigurera OAuth 2,0](https://support.google.com/googleapi/answer/6158849). Om du inte redan har ett Google-konto kan du registrera dig på [https://accounts.google.com/SignUp](https://accounts.google.com/SignUp) .

1. Logga in på [Google Developer-konsolen](https://console.developers.google.com/) med dina Google-kontoautentiseringsuppgifter.
1. I det övre vänstra hörnet på sidan väljer du projekt listan och väljer sedan **nytt projekt**.
1. Ange ett **projekt namn** och välj **skapa**.
1. Se till att du använder det nya projektet genom att markera List rutan projekt i det övre vänstra hörnet av skärmen. Välj ditt projekt efter namn och välj sedan **Öppna**.
1. Välj alternativet **OAuth-medgivande** på den vänstra menyn, Välj **externt** och välj sedan **skapa**.
Ange ett **namn** för ditt program. Ange *b2clogin.com* i avsnittet **auktoriserade domäner** och välj **Spara**.
1. Välj **autentiseringsuppgifter** på den vänstra menyn och välj sedan **skapa autentiseringsuppgifter**  >  **OAuth klient-ID**.
1. Under **program typ** väljer du **webb program**.
    1. Ange ett **namn** för ditt program.
    1. För de **auktoriserade JavaScript-ursprungen** anger du `https://your-tenant-name.b2clogin.com` . Om du använder en [anpassad domän](custom-domain.md)anger du `https://your-domain-name` .
    1. Ange för de **auktoriserade omdirigerings-URI: erna** `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` . Om du använder en [anpassad domän](custom-domain.md)anger du `https://your-domain-name/your-tenant-name.onmicrosoft.com/oauth2/authresp` . Ersätt `your-domain-name` med din anpassade domän och `your-tenant-name` med namnet på din klient. Använd små bokstäver när du anger ditt klient namn även om klienten har definierats med versaler i Azure AD B2C.
1. Klicka på **Skapa**.
1. Kopiera värdena för **klient-ID** och **klient hemlighet**. Du behöver båda dessa för att kunna konfigurera Google som en identitets leverantör i din klient organisation. **Klient hemlighet** är en viktig säkerhets autentiseringsuppgift.

::: zone pivot="b2c-user-flow"

## <a name="configure-google-as-an-identity-provider"></a>Konfigurera Google som en identitets leverantör

1. Logga in på [Azure Portal](https://portal.azure.com/) som global administratör för Azure AD B2C-klientorganisationen.
1. Kontrol lera att du använder den katalog som innehåller din Azure AD B2C klient genom att välja filtret **katalog + prenumeration** på den översta menyn och välja den katalog som innehåller din klient.
1. Välj **Alla tjänster** på menyn högst upp till vänster i Azure-portalen och sök efter och välj **Azure AD B2C**.
1. Välj **identitets leverantörer** och välj sedan **Google**.
1. Ange ett **namn**. Till exempel *Google*.
1. För **klient-ID** anger du klient-ID för det Google-program som du skapade tidigare.
1. Ange den klient hemlighet som du har spelat in för **klient hemligheten**.
1. Välj **Spara**.

## <a name="add-google-identity-provider-to-a-user-flow"></a>Lägga till Google Identity Provider i ett användar flöde 

Google Identity-providern har nu kon figurer ATS, men är ännu inte tillgänglig på någon av inloggnings sidorna. Så här lägger du till Google Identity-providern i ett användar flöde:


1. Välj **användar flöden** i Azure AD B2C klient.
1. Klicka på det användar flöde som du vill lägga till Google Identity-providern.
1. Under **leverantörer av sociala identitet** väljer du **Google**.
1. Välj **Spara**.
1. Om du vill testa principen väljer du **Kör användar flöde**.
1. För **program** väljer du det webb program som heter *testapp1* som du tidigare har registrerat. **Svars-URL: en** ska visas `https://jwt.ms` .
1. Välj knappen **Kör användar flöde** .
1. Från registrerings-eller inloggnings sidan väljer du **Google** för att logga in med Google-konto.

Om inloggnings processen lyckas omdirigeras webbläsaren till `https://jwt.ms` , som visar innehållet i den token som returnerades av Azure AD B2C.

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="create-a-policy-key"></a>Skapa en princip nyckel

Du måste lagra klient hemligheten som du tidigare registrerade i Azure AD B2C-klienten.

1. Logga in på [Azure-portalen](https://portal.azure.com/).
2. Kontrol lera att du använder den katalog som innehåller din Azure AD B2C-klient. Välj **katalog + prenumerations** filter på den översta menyn och välj den katalog som innehåller din klient.
3. Välj **Alla tjänster** på menyn högst upp till vänster i Azure-portalen och sök efter och välj **Azure AD B2C**.
4. På sidan Översikt väljer du **ID för identitets miljö**.
5. Välj **princip nycklar** och välj sedan **Lägg till**.
6. För **alternativ** väljer du `Manual` .
7. Ange ett **namn** för princip nyckeln. Till exempel `GoogleSecret`. Prefixet `B2C_1A_` läggs till automatiskt till namnet på din nyckel.
8. I **hemlighet** anger du din klient hemlighet som du tidigare har spelat in.
9. För **nyckel användning** väljer du `Signature` .
10. Klicka på **Skapa**.

## <a name="configure-google-as-an-identity-provider"></a>Konfigurera Google som en identitets leverantör

För att användarna ska kunna logga in med ett Google-konto måste du definiera kontot som en anspråks leverantör som Azure AD B2C kan kommunicera med via en slut punkt. Slut punkten innehåller en uppsättning anspråk som används av Azure AD B2C för att verifiera att en speciell användare har autentiserats.

Du kan definiera ett Google-konto som en anspråks leverantör genom att lägga till det i **ClaimsProviders** -elementet i principens tilläggs fil.

1. Öppna *TrustFrameworkExtensions.xml*.
2. Hitta **ClaimsProviders** -elementet. Om den inte finns lägger du till den under rot elementet.
3. Lägg till en ny **ClaimsProvider** enligt följande:

    ```xml
    <ClaimsProvider>
      <Domain>google.com</Domain>
      <DisplayName>Google</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="Google-OAuth2">
          <DisplayName>Google</DisplayName>
          <Protocol Name="OAuth2" />
          <Metadata>
            <Item Key="ProviderName">google</Item>
            <Item Key="authorization_endpoint">https://accounts.google.com/o/oauth2/auth</Item>
            <Item Key="AccessTokenEndpoint">https://accounts.google.com/o/oauth2/token</Item>
            <Item Key="ClaimsEndpoint">https://www.googleapis.com/oauth2/v1/userinfo</Item>
            <Item Key="scope">email profile</Item>
            <Item Key="HttpBinding">POST</Item>
            <Item Key="UsePolicyInRedirectUri">false</Item>
            <Item Key="client_id">Your Google application ID</Item>
          </Metadata>
          <CryptographicKeys>
            <Key Id="client_secret" StorageReferenceId="B2C_1A_GoogleSecret" />
          </CryptographicKeys>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="id" />
            <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="email" />
            <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="given_name" />
            <OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="family_name" />
            <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
            <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="google.com" />
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

4. Ange **client_id** till program-ID: t från program registreringen.
5. Spara filen.

[!INCLUDE [active-directory-b2c-add-identity-provider-to-user-journey](../../includes/active-directory-b2c-add-identity-provider-to-user-journey.md)]


```xml
<OrchestrationStep Order="1" Type="CombinedSignInAndSignUp" ContentDefinitionReferenceId="api.signuporsignin">
  <ClaimsProviderSelections>
    ...
    <ClaimsProviderSelection TargetClaimsExchangeId="GoogleExchange" />
  </ClaimsProviderSelections>
  ...
</OrchestrationStep>

<OrchestrationStep Order="2" Type="ClaimsExchange">
  ...
  <ClaimsExchanges>
    <ClaimsExchange Id="GoogleExchange" TechnicalProfileReferenceId="Google-OAuth2" />
  </ClaimsExchanges>
</OrchestrationStep>
```

[!INCLUDE [active-directory-b2c-configure-relying-party-policy](../../includes/active-directory-b2c-configure-relying-party-policy-user-journey.md)]

## <a name="test-your-custom-policy"></a>Testa din anpassade princip

1. Välj en princip för förlitande part, till exempel `B2C_1A_signup_signin` .
1. För **program** väljer du ett webb program som du [har registrerat tidigare](troubleshoot-custom-policies.md#troubleshoot-the-runtime). **Svars-URL: en** ska visas `https://jwt.ms` .
1. Välj knappen **Kör nu** .
1. Från registrerings-eller inloggnings sidan väljer du **Google** för att logga in med Google-konto.

Om inloggnings processen lyckas omdirigeras webbläsaren till `https://jwt.ms` , som visar innehållet i den token som returnerades av Azure AD B2C.

::: zone-end

## <a name="next-steps"></a>Nästa steg

Lär dig hur du [skickar Google-token till ditt program](idp-pass-through-user-flow.md).