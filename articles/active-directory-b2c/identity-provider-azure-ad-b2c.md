---
title: Konfigurera registrering och inloggning med ett Azure AD B2C konto från en annan Azure AD B2C klient
titleSuffix: Azure AD B2C
description: Tillhandahålla registrering och inloggning till kunder med Azure AD B2C konton från en annan klient i dina program med hjälp av Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/15/2021
ms.author: mimart
ms.subservice: B2C
ms.custom: fasttrack-edit, project-no-code
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 4b357213f4e552fd791fb575d8b7a287b924c7f9
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "103489078"
---
# <a name="set-up-sign-up-and-sign-in-with-an-azure-ad-b2c-account-from-another-azure-ad-b2c-tenant"></a>Konfigurera registrering och inloggning med ett Azure AD B2C konto från en annan Azure AD B2C klient

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-custom-policy"

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

::: zone-end

## <a name="overview"></a>Översikt

I den här artikeln beskrivs hur du konfigurerar en federation med en annan Azure AD B2C klient. När dina program skyddas med Azure AD B2C kan användare från andra Azure AD-B2C's logga in med sina befintliga konton. I följande diagram kan användarna logga in i ett program som skyddas av *contosos* Azure AD B2C, med ett konto som hanteras av *Fabrikams* Azure AD B2C-klient 

![Azure AD B2C Federation med en annan Azure AD B2C-klient](./media/identity-provider-azure-ad-b2c/azure-ad-b2c-federation.png)


## <a name="prerequisites"></a>Förutsättningar

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

## <a name="create-an-azure-ad-b2c-application"></a>Skapa ett Azure AD B2C program

Så här aktiverar du inloggning för användare med ett konto från en annan Azure AD B2C klient organisation (till exempel Fabrikam) i din Azure AD B2C (till exempel contoso):

1. Skapa ett [användar flöde](tutorial-create-user-flows.md)eller en [anpassad princip](custom-policy-get-started.md).
1. Skapa sedan ett program i Azure AD B2C enligt beskrivningen i det här avsnittet. 

För att skapa ett program.

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Kontrol lera att du använder den katalog som innehåller din andra Azure AD B2C-klient (till exempel Fabrikam.com).
1. I Azure Portal söker du efter och väljer **Azure AD B2C**.
1. Välj **Appregistreringar** och välj sedan **ny registrering**.
1. Ange ett **namn** för programmet. Till exempel *ContosoApp*.
1. Under **konto typer som stöds** väljer du **konton i valfri identitets leverantör eller organisations katalog (för autentisering av användare med användar flöden)**.
1. Under **omdirigerings-URI** väljer du **webb** och anger sedan följande URL i gemener, där `your-B2C-tenant-name` ersätts med namnet på din Azure AD B2C-klient (till exempel contoso).

    ```
    https://your-B2C-tenant-name.b2clogin.com/your-B2C-tenant-name.onmicrosoft.com/oauth2/authresp
    ```

    Till exempel `https://contoso.b2clogin.com/contoso.onmicrosoft.com/oauth2/authresp`.

    Om du använder en [anpassad domän](custom-domain.md)anger du `https://your-domain-name/your-tenant-name.onmicrosoft.com/oauth2/authresp` . Ersätt `your-domain-name` med din anpassade domän och `your-tenant-name` med namnet på din klient.

1. Under Behörigheter markerar du kryss rutan **bevilja administratörs medgivande till OpenID och offline_access behörighet** .
1. Välj **Register** (Registrera).
1. På sidan **Azure AD B2C-Appregistreringar** väljer du det program som du skapade, till exempel *ContosoApp*.
1. Registrera **program-ID (klient)** som visas på sidan program översikt. Du behöver detta när du konfigurerar identitets leverantören i nästa avsnitt.
1. På den vänstra menyn, under **Hantera**, väljer du **certifikat & hemligheter**.
1. Välj **Ny klienthemlighet**.
1. Ange en beskrivning av klient hemligheten i rutan **Beskrivning** . Till exempel *clientsecret1*.
1. Under **upphör ande** väljer du en varaktighet för vilken hemligheten är giltig och väljer sedan **Lägg till**.
1. Registrera hemlighetens **värde**. Du behöver detta när du konfigurerar identitets leverantören i nästa avsnitt.


::: zone pivot="b2c-user-flow"

## <a name="configure-azure-ad-b2c-as-an-identity-provider"></a>Konfigurera Azure AD B2C som identitets leverantör

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Kontrol lera att du använder den katalog som innehåller den Azure AD B2C klienten som du vill konfigurera federationen (till exempel contoso). Välj **katalog + prenumerations** filter på den översta menyn och välj den katalog som innehåller Azure AD B2C klient organisation (till exempel contoso).
1. Välj **Alla tjänster** på menyn högst upp till vänster i Azure-portalen och sök efter och välj **Azure AD B2C**.
1. Välj **identitets leverantörer** och välj sedan **ny OpenID Connect-Provider**.
1. Ange ett **namn**. Ange till exempel *Fabrikam*.
1. För **metadata-URL** anger du följande URL som ersätter `{tenant}` med domän namnet för din Azure AD B2C-klient (till exempel Fabrikam). Ersätt `{policy}` med namnet på principen som du konfigurerar i den andra klienten:

    ```
    https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/{policy}/v2.0/.well-known/openid-configuration
    ```

    Till exempel `https://fabrikam.b2clogin.com/fabrikam.onmicrosoft.com/B2C_1_susi/v2.0/.well-known/openid-configuration`.

1. För **klient-ID** anger du det program-ID som du tidigare har registrerat.
1. För **klient hemlighet** anger du den klient hemlighet som du tidigare har registrerat.
1. För **scopet** anger du `openid` .
1. Lämna standardvärdena för **svars typ** och **svars läge**.
1. Valfritt I **domän tipset** anger du det domän namn som du vill använda för [direkt inloggning](direct-signin.md#redirect-sign-in-to-a-social-provider). Till exempel *fabrikam.com*.
1. Under **identitets leverantör anspråks mappning** väljer du följande anspråk:

    - **Användar-ID**: *Sub*
    - **Visnings namn**: *namn*
    - **Tilldelat namn**: *given_name*
    - Efter **namn**: *family_name*
    - **E-post**: *e-post*

1. Välj **Spara**.

## <a name="add-azure-ad-b2c-identity-provider-to-a-user-flow"></a>Lägga till Azure AD B2C identitets leverantör i ett användar flöde 

1. Välj **användar flöden** i Azure AD B2C klient.
1. Klicka på det användar flöde som du vill lägga till Azure AD B2C Identity-providern.
1. Under **leverantörer av sociala identitet** väljer du **Fabrikam**.
1. Välj **Spara**.
1. Om du vill testa principen väljer du **Kör användar flöde**.
1. För **program** väljer du det webb program som heter *testapp1* som du tidigare har registrerat. **Svars-URL: en** ska visas `https://jwt.ms` .
1. Välj knappen **Kör användar flöde** .
1. Från registrerings-eller inloggnings sidan väljer du **Fabrikam** för att logga in med den andra Azure AD B2C klienten.

Om inloggnings processen lyckas omdirigeras webbläsaren till `https://jwt.ms` , som visar innehållet i den token som returnerades av Azure AD B2C.

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="create-a-policy-key"></a>Skapa en princip nyckel

Du måste lagra program nyckeln som du skapade tidigare i Azure AD B2C-klienten.

1. Kontrol lera att du använder den katalog som innehåller den Azure AD B2C klienten som du vill konfigurera federationen (till exempel contoso). Välj **katalog + prenumerations** filter på den översta menyn och välj den katalog som innehåller Azure AD B2C klient organisation (till exempel contoso).
1. Välj **Alla tjänster** på menyn högst upp till vänster i Azure-portalen och sök efter och välj **Azure AD B2C**.
1. Under **principer** väljer du **Identity Experience Framework**.
1. Välj **princip nycklar** och välj sedan **Lägg till**.
1. För **alternativ** väljer du `Manual` .
1. Ange ett **namn** för princip nyckeln. Till exempel `FabrikamAppSecret`.  Prefixet `B2C_1A_` läggs automatiskt till namnet på nyckeln när det skapas, så referensen i XML i följande avsnitt är att *B2C_1A_FabrikamAppSecret*.
1. I **hemlighet** anger du din klient hemlighet som du registrerade tidigare.
1. För **nyckel användning** väljer du `Signature` .
1. Välj **Skapa**.

## <a name="configure-azure-ad-b2c-as-an-identity-provider"></a>Konfigurera Azure AD B2C som identitets leverantör

Om du vill att användarna ska kunna logga in med ett konto från en annan Azure AD B2C-klient (Fabrikam) måste du definiera andra Azure AD B2C som en anspråks leverantör som Azure AD B2C kan kommunicera med via en slut punkt. Slut punkten innehåller en uppsättning anspråk som används av Azure AD B2C för att verifiera att en speciell användare har autentiserats.

Du kan definiera Azure AD B2C som anspråks leverantör genom att lägga till Azure AD B2C i **ClaimsProvider** -elementet i principens tilläggs fil.

1. Öppna *TrustFrameworkExtensions.xml* -filen.
1. Hitta **ClaimsProviders** -elementet. Om den inte finns lägger du till den under rot elementet.
1. Lägg till en ny **ClaimsProvider** enligt följande:
    ```xml
    <ClaimsProvider>
      <Domain>fabrikam.com</Domain>
      <DisplayName>Federation with Fabrikam tenant</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="AzureADB2CFabrikam-OpenIdConnect">
        <DisplayName>Fabrikam</DisplayName>
        <Protocol Name="OpenIdConnect"/>
        <Metadata>
          <!-- Update the Client ID below to the Application ID -->
          <Item Key="client_id">00000000-0000-0000-0000-000000000000</Item>
          <!-- Update the metadata URL with the other Azure AD B2C tenant name and policy name -->
          <Item Key="METADATA">https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/{policy}/v2.0/.well-known/openid-configuration</Item>
          <Item Key="UsePolicyInRedirectUri">false</Item>
          <Item Key="response_types">code</Item>
          <Item Key="scope">openid</Item>
          <Item Key="response_mode">form_post</Item>
          <Item Key="HttpBinding">POST</Item>
        </Metadata>
        <CryptographicKeys>
          <Key Id="client_secret" StorageReferenceId="B2C_1A_FabrikamAppSecret"/>
        </CryptographicKeys>
        <OutputClaims>
          <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="sub" />
          <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="given_name" />
          <OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="family_name" />
          <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
          <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="email" />
          <OutputClaim ClaimTypeReferenceId="identityProvider" PartnerClaimType="iss"  />
          <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" AlwaysUseDefaultValue="true" />
          <OutputClaim ClaimTypeReferenceId="otherMails" PartnerClaimType="emails"/>    
        </OutputClaims>
        <OutputClaimsTransformations>
          <OutputClaimsTransformation ReferenceId="CreateRandomUPNUserName" />
          <OutputClaimsTransformation ReferenceId="CreateUserPrincipalName" />
          <OutputClaimsTransformation ReferenceId="CreateAlternativeSecurityId" />
        </OutputClaimsTransformations>
        <UseTechnicalProfileForSessionManagement ReferenceId="SM-SocialLogin"/>
      </TechnicalProfile>
     </TechnicalProfiles>
    </ClaimsProvider>
    ```

1. Uppdatera följande XML-element med det relevanta värdet:

    |XML-element  |Värde  |
    |---------|---------|
    |ClaimsProvider\Domain  | Domän namnet som används för [direkt inloggning](direct-signin.md?pivots=b2c-custom-policy#redirect-sign-in-to-a-social-provider). Ange det domän namn som du vill använda i direkt inloggningen. Till exempel *fabrikam.com*. |
    |TechnicalProfile\DisplayName|Det här värdet kommer att visas på inloggnings knappen på inloggnings skärmen. Till exempel *Fabrikam*. |
    |Metadata \ client_id|Program identifieraren för identitets leverantören. Uppdatera klient-ID: t med det program-ID som du skapade tidigare i den andra Azure AD B2C klienten.|
    |Metadata\METADATA|En URL som pekar på ett konfigurations dokument för OpenID Connect Identity Provider, som även kallas OpenID-känd konfigurations slut punkt. Ange följande URL som ersätter `{tenant}` med domän namnet för den andra Azure AD B2C klienten (Fabrikam). Ersätt `{tenant}` med namnet på principen som du konfigurerar i den andra klienten och `{policy]` med princip namnet: `https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/{policy}/v2.0/.well-known/openid-configuration` . Till exempel `https://fabrikam.b2clogin.com/fabrikam.onmicrosoft.com/B2C_1_susi/v2.0/.well-known/openid-configuration`.|
    |CryptographicKeys| Uppdatera värdet för **StorageReferenceId** till namnet på den princip nyckel som du skapade tidigare. Till exempel `B2C_1A_FabrikamAppSecret`.| 
    

[!INCLUDE [active-directory-b2c-add-identity-provider-to-user-journey](../../includes/active-directory-b2c-add-identity-provider-to-user-journey.md)]


```xml
<OrchestrationStep Order="1" Type="CombinedSignInAndSignUp" ContentDefinitionReferenceId="api.signuporsignin">
  <ClaimsProviderSelections>
    ...
    <ClaimsProviderSelection TargetClaimsExchangeId="AzureADB2CFabrikamExchange" />
  </ClaimsProviderSelections>
  ...
</OrchestrationStep>

<OrchestrationStep Order="2" Type="ClaimsExchange">
  ...
  <ClaimsExchanges>
    <ClaimsExchange Id="AzureADB2CFabrikamExchange" TechnicalProfileReferenceId="AzureADB2CFabrikam-OpenIdConnect" />
  </ClaimsExchanges>
</OrchestrationStep>
```

[!INCLUDE [active-directory-b2c-configure-relying-party-policy](../../includes/active-directory-b2c-configure-relying-party-policy-user-journey.md)]


## <a name="test-your-custom-policy"></a>Testa din anpassade princip

1. Välj en princip för förlitande part, till exempel `B2C_1A_signup_signin` .
1. För **program** väljer du ett webb program som du [har registrerat tidigare](troubleshoot-custom-policies.md#troubleshoot-the-runtime). **Svars-URL: en** ska visas `https://jwt.ms` .
1. Välj knappen **Kör nu** .
1. Från registrerings-eller inloggnings sidan väljer du **Fabrikam** för att logga in med den andra Azure AD B2C klienten.

Om inloggnings processen lyckas omdirigeras webbläsaren till `https://jwt.ms` , som visar innehållet i den token som returnerades av Azure AD B2C.

::: zone-end

## <a name="next-steps"></a>Nästa steg

Lär dig hur du [skickar den andra Azure AD B2C token till ditt program](idp-pass-through-user-flow.md).
