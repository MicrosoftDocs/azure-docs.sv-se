---
title: Konfigurera inloggning för en Azure AD-organisation
titleSuffix: Azure AD B2C
description: Konfigurera inloggning för en speciell Azure Active Directory organisation i Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 01/27/2021
ms.author: mimart
ms.subservice: B2C
ms.custom: fasttrack-edit, project-no-code
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 9e315812f7e281cf281cad9e052ab4546bde05f9
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/02/2021
ms.locfileid: "101645858"
---
# <a name="set-up-sign-in-for-a-specific-azure-active-directory-organization-in-azure-active-directory-b2c"></a>Konfigurera inloggning för en speciell Azure Active Directory organisation i Azure Active Directory B2C

Den här artikeln visar hur du aktiverar inloggning för användare från en särskild Azure AD-organisation med hjälp av ett användar flöde i Azure AD B2C.

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-custom-policy"

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

::: zone-end

## <a name="prerequisites"></a>Förutsättningar

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

## <a name="register-an-azure-ad-app"></a>Registrera en Azure AD-app

Om du vill aktivera inloggning för användare med ett Azure AD-konto från en specifik Azure AD-organisation, i Azure Active Directory B2C (Azure AD B2C), måste du skapa ett program i [Azure Portal](https://portal.azure.com). Mer information finns i [Registrera ett program med Microsoft Identity Platform](../active-directory/develop/quickstart-register-app.md).

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Kontrol lera att du använder den katalog som innehåller din organisations Azure AD-klient (till exempel contoso.com). Välj **filtret katalog + prenumeration** på den översta menyn och välj sedan den katalog som innehåller din Azure AD-klient.
1. Välj **alla tjänster** i det övre vänstra hörnet av Azure Portal och Sök sedan efter och välj **Appregistreringar**.
1. Välj **ny registrering**.
1. Ange ett **namn** för ditt program. Till exempel `Azure AD B2C App`.
1. Godkänn standard valet av **konton endast i den här organisations katalogen** för det här programmet.
1. För **omdirigerings-URI: n**, godkänn värdet för **webb** och ange följande URL i gemener, där `your-B2C-tenant-name` ersätts med namnet på din Azure AD B2C-klient.

    ```
    https://your-B2C-tenant-name.b2clogin.com/your-B2C-tenant-name.onmicrosoft.com/oauth2/authresp
    ```

    Till exempel `https://fabrikam.b2clogin.com/fabrikam.onmicrosoft.com/oauth2/authresp`.

1. Välj **Register** (Registrera). Registrera **program-ID: t (Client)** för användning i ett senare steg.
1. Välj **certifikat & hemligheter** och välj sedan **ny klient hemlighet**.
1. Ange en **Beskrivning** av hemligheten, Välj förfallo datum och välj sedan **Lägg till**. Registrera **värdet** för hemligheten som ska användas i ett senare steg.

### <a name="configuring-optional-claims"></a>Konfigurera valfria anspråk

Om du vill hämta `family_name` och `given_name` anspråk från Azure AD kan du konfigurera valfria anspråk för programmet i Azure Portal användar gränssnitt eller applikations manifest. Mer information finns i [så här ger du valfria anspråk till din Azure AD-App](../active-directory/develop/active-directory-optional-claims.md).

1. Logga in på [Azure-portalen](https://portal.azure.com). Sök efter och välj **Azure Active Directory**.
1. I avsnittet **Hantera** väljer du **Appregistreringar**.
1. Välj det program som du vill konfigurera valfria anspråk för i listan.
1. I avsnittet **Hantera** väljer du **token-konfiguration**.
1. Välj **Lägg till valfritt anspråk**.
1. I **tokentyp** väljer du **ID**.
1. Välj de valfria anspråk som ska läggas till `family_name` och `given_name` .
1. Klicka på **Lägg till**.

::: zone pivot="b2c-user-flow"

## <a name="configure-azure-ad-as-an-identity-provider"></a>Konfigurera Azure AD som en identitets leverantör

1. Kontrol lera att du använder den katalog som innehåller Azure AD B2C klient. Välj **katalog + prenumerations** filter på den översta menyn och välj den katalog som innehåller Azure AD B2C klienten.
1. Välj **Alla tjänster** på menyn högst upp till vänster i Azure-portalen och sök efter och välj **Azure AD B2C**.
1. Välj **identitets leverantörer** och välj sedan **ny OpenID Connect-Provider**.
1. Ange ett **namn**. Ange till exempel *contoso Azure AD*.
1. För **metadata-URL** anger du följande URL `{tenant}` som ersätter med domän namnet för din Azure AD-klient:

    ```
    https://login.microsoftonline.com/{tenant}/v2.0/.well-known/openid-configuration
    ```

    Till exempel `https://login.microsoftonline.com/contoso.onmicrosoft.com/v2.0/.well-known/openid-configuration`.
    Till exempel `https://login.microsoftonline.com/contoso.com/v2.0/.well-known/openid-configuration`.

1. För **klient-ID** anger du det program-ID som du tidigare har registrerat.
1. För **klient hemlighet** anger du den klient hemlighet som du tidigare har registrerat.
1. För **scopet** anger du `openid profile` .
1. Lämna standardvärdena för **svars typ** och **svars läge**.
1. Valfritt I **domän tipset** anger du `contoso.com` . Mer information finns i [Konfigurera direkt inloggning med Azure Active Directory B2C](direct-signin.md#redirect-sign-in-to-a-social-provider).
1. Under **identitets leverantör anspråks mappning** väljer du följande anspråk:

    - **Användar-ID**: *OID*
    - **Visnings namn**: *namn*
    - **Tilldelat namn**: *given_name*
    - Efter **namn**: *family_name*
    - **E-post**: *preferred_username*

1. Välj **Spara**.

## <a name="add-azure-ad-identity-provider-to-a-user-flow"></a>Lägga till Azure AD Identity Provider i ett användar flöde 

1. Välj **användar flöden** i Azure AD B2C klient.
1. Klicka på det användar flöde som du vill lägga till Azure AD Identity-providern.
1. Under **leverantörer av sociala identitet** väljer du **contoso Azure AD**.
1. Välj **Spara**.
1. Om du vill testa principen väljer du **Kör användar flöde**.
1. För **program** väljer du det webb program som heter *testapp1* som du tidigare har registrerat. **Svars-URL: en** ska visas `https://jwt.ms` .
1. Klicka på **Kör användar flöde**

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="create-a-policy-key"></a>Skapa en princip nyckel

Du måste lagra den program nyckel som du skapade i Azure AD B2C klient organisationen.

1. Kontrol lera att du använder den katalog som innehåller din Azure AD B2C-klient. Välj **filtret katalog + prenumeration** på den översta menyn och välj sedan den katalog som innehåller Azure AD B2C klienten.
1. Välj **Alla tjänster** på menyn högst upp till vänster i Azure-portalen och sök efter och välj **Azure AD B2C**.
1. Under **principer** väljer du **Identity Experience Framework**.
1. Välj **princip nycklar** och välj sedan **Lägg till**.
1. För **alternativ** väljer du `Manual` .
1. Ange ett **namn** för princip nyckeln. Till exempel `ContosoAppSecret`.  Prefixet `B2C_1A_` läggs automatiskt till namnet på nyckeln när det skapas, så referensen i XML i följande avsnitt är att *B2C_1A_ContosoAppSecret*.
1. I **hemlighet** anger du din klient hemlighet som du registrerade tidigare.
1. För **nyckel användning** väljer du `Signature` .
1. Välj **Skapa**.

## <a name="configure-azure-ad-as-an-identity-provider"></a>Konfigurera Azure AD som en identitets leverantör

För att användarna ska kunna logga in med ett Azure AD-konto måste du definiera Azure AD som en anspråks leverantör som Azure AD B2C kan kommunicera med via en slut punkt. Slut punkten innehåller en uppsättning anspråk som används av Azure AD B2C för att verifiera att en speciell användare har autentiserats.

Du kan definiera Azure AD som en anspråks leverantör genom att lägga till Azure AD i **ClaimsProvider** -elementet i tilläggs filen för din princip.

1. Öppna *TrustFrameworkExtensions.xml* -filen.
2. Hitta **ClaimsProviders** -elementet. Om den inte finns lägger du till den under rot elementet.
3. Lägg till en ny **ClaimsProvider** enligt följande:
    ```xml
    <ClaimsProvider>
      <Domain>Contoso</Domain>
      <DisplayName>Login using Contoso</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="AADContoso-OpenIdConnect">
          <DisplayName>Contoso Employee</DisplayName>
          <Description>Login with your Contoso account</Description>
          <Protocol Name="OpenIdConnect"/>
          <Metadata>
            <Item Key="METADATA">https://login.microsoftonline.com/tenant-name.onmicrosoft.com/v2.0/.well-known/openid-configuration</Item>
            <Item Key="client_id">00000000-0000-0000-0000-000000000000</Item>
            <Item Key="response_types">code</Item>
            <Item Key="scope">openid profile</Item>
            <Item Key="response_mode">form_post</Item>
            <Item Key="HttpBinding">POST</Item>
            <Item Key="UsePolicyInRedirectUri">false</Item>
          </Metadata>
          <CryptographicKeys>
            <Key Id="client_secret" StorageReferenceId="B2C_1A_ContosoAppSecret"/>
          </CryptographicKeys>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="oid"/>
            <OutputClaim ClaimTypeReferenceId="tenantId" PartnerClaimType="tid"/>
            <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="given_name" />
            <OutputClaim ClaimTypeReferenceId="surName" PartnerClaimType="family_name" />
            <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" AlwaysUseDefaultValue="true" />
            <OutputClaim ClaimTypeReferenceId="identityProvider" PartnerClaimType="iss" />
          </OutputClaims>
          <OutputClaimsTransformations>
            <OutputClaimsTransformation ReferenceId="CreateRandomUPNUserName"/>
            <OutputClaimsTransformation ReferenceId="CreateUserPrincipalName"/>
            <OutputClaimsTransformation ReferenceId="CreateAlternativeSecurityId"/>
            <OutputClaimsTransformation ReferenceId="CreateSubjectClaimFromAlternativeSecurityId"/>
          </OutputClaimsTransformations>
          <UseTechnicalProfileForSessionManagement ReferenceId="SM-SocialLogin"/>
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

4. Under elementet **ClaimsProvider** uppdaterar du värdet för **domän** till ett unikt värde som kan användas för att skilja den från andra identitets leverantörer. Till exempel `Contoso`. Du har inte `.com` angett något i slutet av den här domän inställningen.
5. Under elementet **ClaimsProvider** uppdaterar du värdet för **DisplayName** till ett eget namn för anspråks leverantören. Det här värdet används inte för närvarande.

### <a name="update-the-technical-profile"></a>Uppdatera den tekniska profilen

Om du vill hämta en token från Azure AD-slutpunkten måste du definiera de protokoll som Azure AD B2C ska använda för att kommunicera med Azure AD. Detta görs i **TechnicalProfile** -elementet för  **ClaimsProvider**.

1. Uppdatera ID för **TechnicalProfile** -elementet. Detta ID används för att referera till den här tekniska profilen från andra delar av principen, till exempel `AADContoso-OpenIdConnect` .
1. Uppdatera värdet för **DisplayName**. Det här värdet kommer att visas på inloggnings knappen på inloggnings skärmen.
1. Uppdatera värdet för **Beskrivning**.
1. Azure AD använder OpenID Connect-protokollet, så se till att värdet för **protokollet** är `OpenIdConnect` .
1. Ange värdet för **metadata** till `https://login.microsoftonline.com/tenant-name.onmicrosoft.com/v2.0/.well-known/openid-configuration` , där `tenant-name` är namnet på din Azure AD-klient. Till exempel `https://login.microsoftonline.com/contoso.onmicrosoft.com/v2.0/.well-known/openid-configuration`
1. Ange **client_id** till program-ID: t från program registreringen.
1. Under **CryptographicKeys** uppdaterar du värdet för **StorageReferenceId** till namnet på den princip nyckel som du skapade tidigare. Till exempel `B2C_1A_ContosoAppSecret`.


[!INCLUDE [active-directory-b2c-add-identity-provider-to-user-journey](../../includes/active-directory-b2c-add-identity-provider-to-user-journey.md)]


```xml
<OrchestrationStep Order="1" Type="CombinedSignInAndSignUp" ContentDefinitionReferenceId="api.signuporsignin">
  <ClaimsProviderSelections>
    ...
    <ClaimsProviderSelection TargetClaimsExchangeId="AzureADContosoExchange" />
  </ClaimsProviderSelections>
  ...
</OrchestrationStep>

<OrchestrationStep Order="2" Type="ClaimsExchange">
  ...
  <ClaimsExchanges>
    <ClaimsExchange Id="AzureADContosoExchange" TechnicalProfileReferenceId="AADContoso-OpenIdConnect" />
  </ClaimsExchanges>
</OrchestrationStep>
```

[!INCLUDE [active-directory-b2c-configure-relying-party-policy](../../includes/active-directory-b2c-configure-relying-party-policy-user-journey.md)]

[!INCLUDE [active-directory-b2c-test-relying-party-policy](../../includes/active-directory-b2c-test-relying-party-policy-user-journey.md)]

## <a name="next-steps"></a>Nästa steg

När du arbetar med anpassade principer kan du ibland behöva ytterligare information när du felsöker en princip under dess utveckling.

För att hjälpa till att diagnostisera problem kan du tillfälligt ställa in principen i "utvecklarläge" och samla in loggar med Azure Application insikter. Ta reda på hur i [Azure Active Directory B2C: samlar in loggar](troubleshoot-with-application-insights.md).

::: zone-end