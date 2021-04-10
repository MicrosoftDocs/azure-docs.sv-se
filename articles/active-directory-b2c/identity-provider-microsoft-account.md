---
title: Konfigurera registrering och inloggning med ett Microsoft-konto
titleSuffix: Azure AD B2C
description: Tillhandahålla registrering och inloggning till kunder med Microsoft-konton i dina program med hjälp av Azure Active Directory B2C.
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
ms.openlocfilehash: 60a846d72c1760c7f9dddac891f36e834b8364f3
ms.sourcegitcommit: d40ffda6ef9463bb75835754cabe84e3da24aab5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/07/2021
ms.locfileid: "107028170"
---
# <a name="set-up-sign-up-and-sign-in-with-a-microsoft-account-using-azure-active-directory-b2c"></a>Konfigurera registrering och inloggning med en Microsoft-konto med hjälp av Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-custom-policy"

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

::: zone-end

## <a name="prerequisites"></a>Förutsättningar

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

## <a name="create-a-microsoft-account-application"></a>Skapa ett Microsoft-konto program

Om du vill aktivera inloggning för användare med en Microsoft-konto i Azure Active Directory B2C (Azure AD B2C) måste du skapa ett program i [Azure Portal](https://portal.azure.com). Mer information finns i [Registrera ett program med Microsoft Identity Platform](../active-directory/develop/quickstart-register-app.md). Om du inte redan har en Microsoft-konto kan du hämta en på [https://www.live.com/](https://www.live.com/) .

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Kontrol lera att du använder den katalog som innehåller din Azure AD-klient genom att välja filtret **katalog + prenumeration** på den översta menyn och välja den katalog som innehåller din Azure AD-klient.
1. Välj **alla tjänster** i det övre vänstra hörnet av Azure Portal och Sök sedan efter och välj **Appregistreringar**.
1. Välj **ny registrering**.
1. Ange ett **namn** för ditt program. Till exempel *MSAapp1*.
1. Under **konto typer som stöds** väljer du **konton i valfri organisations katalog (alla Azure AD-klienter-flera klienter) och personliga Microsoft-konton (t. ex. Skype, Xbox)**.

   Mer information om de olika alternativen för konto typer finns i [snabb start: registrera ett program med Microsoft Identity Platform](../active-directory/develop/quickstart-register-app.md).
1. Under **omdirigerings-URI (valfritt)** väljer du **webb** och retur `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` . Om du använder en [anpassad domän](custom-domain.md)anger du `https://your-domain-name/your-tenant-name.onmicrosoft.com/oauth2/authresp` . Ersätt `your-tenant-name` med namnet på din klient och `your-domain-name` med din anpassade domän.
1. Välj **register**
1. Registrera **program-ID (klient)** som visas på sidan program översikt. Du behöver klient-ID när du konfigurerar identitets leverantören i nästa avsnitt.
1. Välj **certifikat & hemligheter**
1. Klicka på **Ny klienthemlighet**
1. Ange en **Beskrivning** av hemligheten, till exempel *program lösen ord 1*, och klicka sedan på **Lägg till**.
1. Registrera program lösen ordet som visas i kolumnen **värde** . Du behöver klient hemligheten när du konfigurerar identitets leverantören i nästa avsnitt.

::: zone pivot="b2c-user-flow"

## <a name="configure-microsoft-as-an-identity-provider"></a>Konfigurera Microsoft som en identitets leverantör

1. Logga in på [Azure Portal](https://portal.azure.com/) som global administratör för Azure AD B2C-klientorganisationen.
1. Kontrol lera att du använder den katalog som innehåller din Azure AD B2C klient genom att välja filtret **katalog + prenumeration** på den översta menyn och välja den katalog som innehåller din klient.
1. Välj **Alla tjänster** på menyn högst upp till vänster i Azure-portalen och sök efter och välj **Azure AD B2C**.
1. Välj **identitets leverantörer** och välj sedan **Microsoft-konto**.
1. Ange ett **namn**. Till exempel *MSA*.
1. För **klient-ID** anger du program-ID: t för det Azure AD-program som du skapade tidigare.
1. Ange den klient hemlighet som du har spelat in för **klient hemligheten**.
1. Välj **Spara**.

## <a name="add-microsoft-identity-provider-to-a-user-flow"></a>Lägg till Microsoft Identity Provider i ett användar flöde 

Microsoft Identity Provider har nu kon figurer ATS, men den är inte tillgänglig ännu på någon av inloggnings sidorna. Så här lägger du till Microsoft Identity-providern i ett användar flöde:

1. Välj **användar flöden** i Azure AD B2C klient.
1. Klicka på det användar flöde som du vill lägga till Microsoft Identity Provider.
1. Under **leverantörer av sociala identitet** väljer du **Microsoft-konto**.
1. Välj **Spara**.
1. Om du vill testa principen väljer du **Kör användar flöde**.
1. För **program** väljer du det webb program som heter *testapp1* som du tidigare har registrerat. **Svars-URL: en** ska visas `https://jwt.ms` .
1. Välj knappen **Kör användar flöde** .
1. Från registrerings-eller inloggnings sidan väljer du **Microsoft** för att logga in med Microsoft-konto.

Om inloggnings processen lyckas omdirigeras webbläsaren till `https://jwt.ms` , som visar innehållet i den token som returnerades av Azure AD B2C.

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="configuring-optional-claims"></a>Konfigurera valfria anspråk

Om du vill hämta `family_name` och `given_name` anspråk från Azure AD kan du konfigurera valfria anspråk för programmet i Azure Portal användar gränssnitt eller applikations manifest. Mer information finns i [så här ger du valfria anspråk till din Azure AD-App](../active-directory/develop/active-directory-optional-claims.md).

1. Logga in på [Azure-portalen](https://portal.azure.com). Sök efter och välj **Azure Active Directory**.
1. I avsnittet **Hantera** väljer du **Appregistreringar**.
1. Välj det program som du vill konfigurera valfria anspråk för i listan.
1. I avsnittet **Hantera** väljer du **konfiguration av token (för hands version)**.
1. Välj **Lägg till valfritt anspråk**.
1. Välj den tokentyp som du vill konfigurera.
1. Välj de valfria anspråk som ska läggas till.
1. Klicka på **Lägg till**.

## <a name="create-a-policy-key"></a>Skapa en princip nyckel

Nu när du har skapat programmet i din Azure AD-klient måste du lagra programmets klient hemlighet i din Azure AD B2C klient.

1. Logga in på [Azure-portalen](https://portal.azure.com/).
1. Kontrol lera att du använder den katalog som innehåller din Azure AD B2C-klient. Välj **katalog + prenumerations** filter på den översta menyn och välj den katalog som innehåller din klient.
1. Välj **Alla tjänster** på menyn högst upp till vänster i Azure-portalen och sök efter och välj **Azure AD B2C**.
1. På sidan Översikt väljer du **ID för identitets miljö**.
1. Välj **princip nycklar** och välj sedan **Lägg till**.
1. För **alternativ** väljer du `Manual` .
1. Ange ett **namn** för princip nyckeln. Till exempel `MSASecret`. Prefixet `B2C_1A_` läggs till automatiskt till namnet på din nyckel.
1. I **hemlighet** anger du den klient hemlighet som du registrerade i föregående avsnitt.
1. För **nyckel användning** väljer du `Signature` .
1. Klicka på **Skapa**.

## <a name="configure-microsoft-as-an-identity-provider"></a>Konfigurera Microsoft som en identitets leverantör

Om du vill att användarna ska kunna logga in med en Microsoft-konto måste du definiera kontot som en anspråks leverantör som Azure AD B2C kan kommunicera med via en slut punkt. Slut punkten innehåller en uppsättning anspråk som används av Azure AD B2C för att verifiera att en speciell användare har autentiserats.

Du kan definiera Azure AD som en anspråks leverantör genom att lägga till **ClaimsProvider** -elementet i tilläggs filen för din princip.

1. Öppna den *TrustFrameworkExtensions.xml* princip filen.
1. Hitta **ClaimsProviders** -elementet. Om den inte finns lägger du till den under rot elementet.
1. Lägg till en ny **ClaimsProvider** enligt följande:

    ```xml
    <ClaimsProvider>
      <Domain>live.com</Domain>
      <DisplayName>Microsoft Account</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="MSA-MicrosoftAccount-OpenIdConnect">
          <DisplayName>Microsoft Account</DisplayName>
          <Protocol Name="OpenIdConnect" />
          <Metadata>
            <Item Key="ProviderName">https://login.live.com</Item>
            <Item Key="METADATA">https://login.live.com/.well-known/openid-configuration</Item>
            <Item Key="response_types">code</Item>
            <Item Key="response_mode">form_post</Item>
            <Item Key="scope">openid profile email</Item>
            <Item Key="HttpBinding">POST</Item>
            <Item Key="UsePolicyInRedirectUri">false</Item>
            <Item Key="client_id">Your Microsoft application client ID</Item>
          </Metadata>
          <CryptographicKeys>
            <Key Id="client_secret" StorageReferenceId="B2C_1A_MSASecret" />
          </CryptographicKeys>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="oid" />
            <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="given_name" />
            <OutputClaim ClaimTypeReferenceId="surName" PartnerClaimType="family_name" />
            <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" />
            <OutputClaim ClaimTypeReferenceId="identityProvider" PartnerClaimType="iss" />
            <OutputClaim ClaimTypeReferenceId="email" />
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

1. Ersätt värdet för **client_id** med Azure AD *-programmets program-ID (klient)* som du registrerade tidigare.
1. Spara filen.

Nu har du konfigurerat principen så att Azure AD B2C vet hur de kan kommunicera med ditt Microsoft-konto-program i Azure AD.

[!INCLUDE [active-directory-b2c-add-identity-provider-to-user-journey](../../includes/active-directory-b2c-add-identity-provider-to-user-journey.md)]


```xml
<OrchestrationStep Order="1" Type="CombinedSignInAndSignUp" ContentDefinitionReferenceId="api.signuporsignin">
  <ClaimsProviderSelections>
    ...
    <ClaimsProviderSelection TargetClaimsExchangeId="MicrosoftAccountExchange" />
  </ClaimsProviderSelections>
  ...
</OrchestrationStep>

<OrchestrationStep Order="2" Type="ClaimsExchange">
  ...
  <ClaimsExchanges>
    <ClaimsExchange Id="MicrosoftAccountExchange" TechnicalProfileReferenceId="MSA-MicrosoftAccount-OpenIdConnect" />
  </ClaimsExchanges>
</OrchestrationStep>
```

[!INCLUDE [active-directory-b2c-configure-relying-party-policy](../../includes/active-directory-b2c-configure-relying-party-policy-user-journey.md)]

## <a name="test-your-custom-policy"></a>Testa din anpassade princip

1. Välj en princip för förlitande part, till exempel `B2C_1A_signup_signin` .
1. För **program** väljer du ett webb program som du [har registrerat tidigare](tutorial-register-applications.md). **Svars-URL: en** ska visas `https://jwt.ms` .
1. Välj knappen **Kör nu** .
1. Från registrerings-eller inloggnings sidan väljer du **Microsoft** för att logga in med Microsoft-konto.

Om inloggnings processen lyckas omdirigeras webbläsaren till `https://jwt.ms` , som visar innehållet i den token som returnerades av Azure AD B2C.

::: zone-end
