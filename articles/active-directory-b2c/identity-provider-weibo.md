---
title: Konfigurera registrering och inloggning med ett Weibo-konto
titleSuffix: Azure AD B2C
description: Tillhandahålla registrering och inloggning till kunder med Weibo-konton i dina program med hjälp av Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/15/2021
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 373c69d0d8e966f1b55f680ef58ff26826a14de5
ms.sourcegitcommit: d40ffda6ef9463bb75835754cabe84e3da24aab5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/07/2021
ms.locfileid: "107027915"
---
# <a name="set-up-sign-up-and-sign-in-with-a-weibo-account-using-azure-active-directory-b2c"></a>Konfigurera registrering och inloggning med ett Weibo-konto med hjälp av Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

## <a name="prerequisites"></a>Förutsättningar

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

## <a name="create-a-weibo-application"></a>Skapa ett Weibo-program

Om du vill aktivera inloggning för användare med ett Weibo-konto i Azure Active Directory B2C (Azure AD B2C) måste du skapa ett program i Weibo Developer-portalen. Om du inte redan har ett Weibo-konto kan du registrera dig på [https://weibo.com](https://weibo.com/signup/signup.php?lang=en-us) .

1. Logga in på Weibo Developer-portalen med dina Weibo-kontoautentiseringsuppgifter.
1. När du har loggat in väljer du ditt visnings namn i det övre högra hörnet.
1. I list rutan väljer du **编辑开发者信息** (redigera information om utvecklare).
1. Ange den information som krävs och välj **提交** (skicka).
1. Slutför verifierings processen för e-post.
1. Gå till sidan med identitets verifiering.
1. Ange den information som krävs och välj **提交** (skicka).

### <a name="register-a-weibo-application"></a>Registrera ett Weibo-program

1. Gå till den nya Weibo app Registration-sidan.
1. Ange nödvändig programinformation.
1. Välj **创建** (skapa).
1. Kopiera värdena för **app Key** och **app Secret**. Du behöver båda dessa för att lägga till identitets leverantören till din klient.
1. Överför de nödvändiga fotona och ange nödvändig information.
1. Välj **保存以上信息** (Spara).
1. Välj **高级信息** (avancerad information).
1. Välj **编辑** (redigera) bredvid fältet för OAuth 2.0- **授权设置** (omdirigerings-URL).
1. För OAuth 2.0- **授权设置** (omdirigerings-URL) anger du `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` . Om du använder en [anpassad domän](custom-domain.md)anger du `https://your-domain-name/your-tenant-name.onmicrosoft.com/oauth2/authresp` . Ersätt `your-tenant-name` med namnet på din klient och `your-domain-name` med din anpassade domän.
1. Välj **提交** (skicka).

::: zone pivot="b2c-user-flow"

## <a name="configure-weibo-as-an-identity-provider"></a>Konfigurera Weibo som identitets leverantör

1. Logga in på [Azure Portal](https://portal.azure.com/) som global administratör för Azure AD B2C-klientorganisationen.
1. Kontrol lera att du använder den katalog som innehåller din Azure AD B2C klient genom att välja filtret **katalog + prenumeration** på den översta menyn och välja den katalog som innehåller din klient.
1. Välj **Alla tjänster** på menyn högst upp till vänster i Azure-portalen och sök efter och välj **Azure AD B2C**.
1. Välj **identitets leverantörer** och välj sedan **Weibo (för hands version)**.
1. Ange ett **namn**. Till exempel *Weibo*.
1. För **klient-ID** anger du appens nyckel för det Weibo-program som du skapade tidigare.
1. För **klient hemligheten** anger du appens hemlighet som du har spelat in.
1. Välj **Spara**.

## <a name="add-weibo-identity-provider-to-a-user-flow"></a>Lägg till Weibo Identity Provider i ett användar flöde 

1. Välj **användar flöden** i Azure AD B2C klient.
1. Klicka på det användar flöde som du vill lägga till Weibo Identity Provider.
1. Under **leverantörer av sociala identitet** väljer du **Weibo**.
1. Välj **Spara**.
1. Om du vill testa principen väljer du **Kör användar flöde**.
1. För **program** väljer du det webb program som heter *testapp1* som du tidigare har registrerat. **Svars-URL: en** ska visas `https://jwt.ms` .
1. Välj knappen **Kör användar flöde** .
1. På sidan registrering eller inloggning väljer du **Weibo** för att logga in med Weibo-kontot.

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
7. Ange ett **namn** för princip nyckeln. Till exempel `WeiboSecret`. Prefixet `B2C_1A_` läggs till automatiskt till namnet på din nyckel.
8. I **hemlighet** anger du din klient hemlighet som du tidigare har spelat in.
9. För **nyckel användning** väljer du `Signature` .
10. Klicka på **Skapa**.

## <a name="configure-weibo-as-an-identity-provider"></a>Konfigurera Weibo som identitets leverantör

Om du vill att användarna ska kunna logga in med ett Weibo-konto måste du definiera kontot som en anspråks leverantör som Azure AD B2C kan kommunicera med via en slut punkt. Slut punkten innehåller en uppsättning anspråk som används av Azure AD B2C för att verifiera att en speciell användare har autentiserats.

Du kan definiera ett Weibo-konto som en anspråks leverantör genom att lägga till det i **ClaimsProviders** -elementet i principens tilläggs fil.

1. Öppna *TrustFrameworkExtensions.xml*.
2. Hitta **ClaimsProviders** -elementet. Om den inte finns lägger du till den under rot elementet.
3. Lägg till en ny **ClaimsProvider** enligt följande:

    ```xml
    <ClaimsProvider>
      <Domain>weibo.com</Domain>
      <DisplayName>Weibo (Preview)</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="Weibo-OAuth2">
          <DisplayName>Weibo</DisplayName>
          <Protocol Name="OAuth2" />
          <Metadata>
            <Item Key="ProviderName">weibo</Item>
            <Item Key="authorization_endpoint">https://api.weibo.com/oauth2/authorize</Item>
            <Item Key="AccessTokenEndpoint">https://api.weibo.com/oauth2/access_token</Item>
            <Item Key="ClaimsEndpoint">https://api.weibo.com/2/account/get_uid.json</Item>
            <Item Key="scope">email</Item>
            <Item Key="HttpBinding">POST</Item>
            <Item Key="external_user_identity_claim_id">uid</Item>
            <Item Key="client_id">Your Weibo application ID</Item>
          </Metadata>
          <CryptographicKeys>
            <Key Id="client_secret" StorageReferenceId="B2C_1A_WeiboSecret" />
          </CryptographicKeys>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="numericUserId" PartnerClaimType="uid" />
            <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="weibo.com" AlwaysUseDefaultValue="true" />
            <OutputClaim ClaimTypeReferenceId="displayName" DefaultValue="Weibo User" />
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" AlwaysUseDefaultValue="true" />
            <OutputClaim ClaimTypeReferenceId="UserId" />
          </OutputClaims>
          <OutputClaimsTransformations>
            <OutputClaimsTransformation ReferenceId="CreateIssuerUserId" />
            <OutputClaimsTransformation ReferenceId="CreateRandomUPNUserName"/>
            <OutputClaimsTransformation ReferenceId="CreateUserPrincipalName"/>
            <OutputClaimsTransformation ReferenceId="CreateAlternativeSecurityId"/>
            <OutputClaimsTransformation ReferenceId="CreateSubjectClaimFromAlternativeSecurityId"/>
          </OutputClaimsTransformations>
          <UseTechnicalProfileForSessionManagement ReferenceId="SM-SocialLogin" />
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    <ClaimsProvider>
    ```

4. Ange **client_id** till program-ID: t från program registreringen.
5. Spara filen.

### <a name="add-the-claims-transformations"></a>Lägg till anspråks omvandlingarna

GitHub tekniska profil kräver att **CreateIssuerUserId** -postomvandlingar läggs till i listan över ClaimsTransformations. Om du inte har ett **ClaimsTransformations** -element definierat i filen lägger du till de överordnade XML-elementen som visas nedan. Anspråks omvandlingarna behöver också en ny anspråks typ som definierats med namnet **numericUserId**.

1. Sök efter [BuildingBlocks](buildingblocks.md) -elementet. Om elementet inte finns lägger du till det.
1. Leta upp [ClaimsSchema](claimsschema.md) -elementet. Om elementet inte finns lägger du till det.
1. Lägg till numericUserId-anspråket i **ClaimsSchema** -elementet.
1. Leta upp [ClaimsTransformations](claimstransformations.md) -elementet. Om elementet inte finns lägger du till det.
1. Lägg till CreateIssuerUserId-postomvandlingar i **ClaimsTransformations** -elementet.

```xml
<BuildingBlocks>
  <ClaimsSchema>
    <ClaimType Id="numericUserId">
      <DisplayName>Numeric user Identifier</DisplayName>
      <DataType>long</DataType>
    </ClaimType>
  </ClaimsSchema>
  <ClaimsTransformations>
    <ClaimsTransformation Id="CreateIssuerUserId" TransformationMethod="ConvertNumberToStringClaim">
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="numericUserId" TransformationClaimType="inputClaim" />
      </InputClaims>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="issuerUserId" TransformationClaimType="outputClaim" />
      </OutputClaims>
    </ClaimsTransformation>
  </ClaimsTransformations>
</BuildingBlocks>
```

[!INCLUDE [active-directory-b2c-add-identity-provider-to-user-journey](../../includes/active-directory-b2c-add-identity-provider-to-user-journey.md)]


```xml
<OrchestrationStep Order="1" Type="CombinedSignInAndSignUp" ContentDefinitionReferenceId="api.signuporsignin">
  <ClaimsProviderSelections>
    ...
    <ClaimsProviderSelection TargetClaimsExchangeId="WeiboExchange" />
  </ClaimsProviderSelections>
  ...
</OrchestrationStep>

<OrchestrationStep Order="2" Type="ClaimsExchange">
  ...
  <ClaimsExchanges>
    <ClaimsExchange Id="WeiboExchange" TechnicalProfileReferenceId="Weibo-OAuth2" />
  </ClaimsExchanges>
</OrchestrationStep>
```

[!INCLUDE [active-directory-b2c-configure-relying-party-policy](../../includes/active-directory-b2c-configure-relying-party-policy-user-journey.md)]

## <a name="test-your-custom-policy"></a>Testa din anpassade princip

1. Välj en princip för förlitande part, till exempel `B2C_1A_signup_signin` .
1. För **program** väljer du ett webb program som du [har registrerat tidigare](tutorial-register-applications.md). **Svars-URL: en** ska visas `https://jwt.ms` .
1. Välj knappen **Kör nu** .
1. På sidan registrering eller inloggning väljer du **Weibo** för att logga in med Weibo-kontot.

Om inloggnings processen lyckas omdirigeras webbläsaren till `https://jwt.ms` , som visar innehållet i den token som returnerades av Azure AD B2C.

::: zone-end
