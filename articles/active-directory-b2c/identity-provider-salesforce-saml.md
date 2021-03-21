---
title: Konfigurera inloggning med en Salesforce-SAML-Provider med hjälp av SAML-protokollet
titleSuffix: Azure AD B2C
description: Konfigurera inloggning med en Salesforce-SAML-Provider med hjälp av SAML-protokollet i Azure Active Directory B2C.
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
ms.openlocfilehash: e740fdb9cd232892dadfe98c4d739759be66bf55
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "103488728"
---
# <a name="set-up-sign-in-with-a-salesforce-saml-provider-by-using-saml-protocol-in-azure-active-directory-b2c"></a>Konfigurera inloggning med en Salesforce-SAML-Provider med hjälp av SAML-protokollet i Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-user-flow"
[!INCLUDE [active-directory-b2c-limited-to-custom-policy](../../includes/active-directory-b2c-limited-to-custom-policy.md)]

::: zone-end

::: zone pivot="b2c-custom-policy"

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Den här artikeln visar hur du aktiverar inloggning för användare från en Salesforce-organisation med [anpassade principer](custom-policy-overview.md) i Azure Active Directory B2C (Azure AD B2C). Du aktiverar inloggning genom att lägga till en [SAML-identitetsprovider](identity-provider-generic-saml.md) i en anpassad princip.

## <a name="prerequisites"></a>Förutsättningar

[!INCLUDE [active-directory-b2c-customization-prerequisites-custom-policy](../../includes/active-directory-b2c-customization-prerequisites-custom-policy.md)]
- Registrera dig för ett [kostnads fritt Developer Edition-konto](https://developer.salesforce.com/signup)om du inte redan gjort det. I den här artikeln används [Salesforce-upplevelsen](https://developer.salesforce.com/page/Lightning_Experience_FAQ).
- [Konfigurera en min domän](https://help.salesforce.com/articleView?id=domain_name_setup.htm&language=en_US&type=0) för din Salesforce-organisation.

## <a name="set-up-salesforce-as-an-identity-provider"></a>Konfigurera Salesforce som identitets leverantör

1. [Logga in på Salesforce](https://login.salesforce.com/).
2. På den vänstra menyn, under **Inställningar**, expandera **identitet** och välj sedan **identitetsprovider**.
3. Välj **Aktivera identitets leverantör**.
4. Under **Välj certifikat** väljer du det certifikat som du vill att Salesforce ska använda för att kommunicera med Azure AD B2C. Du kan använda standard certifikatet.
5. Klicka på **Spara**.

### <a name="create-a-connected-app-in-salesforce"></a>Skapa en ansluten app i Salesforce

1. På sidan **identitetsprovider** väljer du **tjänst leverantörer som nu skapas via anslutna appar. Klicka här.**
2. Under **grundläggande information** anger du de värden som krävs för den anslutna appen.
3. Markera kryss rutan **Aktivera SAML** under **Inställningar för webbapp**.
4. I fältet **entitets-ID** anger du följande URL. Se till att du ersätter värdet för `your-tenant` med namnet på din Azure AD B2C-klient.

      ```
      https://your-tenant.b2clogin.com/your-tenant.onmicrosoft.com/B2C_1A_TrustFrameworkBase
      ```

      Använd följande format när du använder en [anpassad domän](custom-domain.md):

      ```
      https://your-domain-name/your-tenant.onmicrosoft.com/B2C_1A_TrustFrameworkBase
      ```

6. I fältet **ACS URL** anger du följande URL. Se till att du ersätter värdet för `your-tenant` med namnet på din Azure AD B2C-klient.

      ```
      https://your-tenant.b2clogin.com/your-tenant.onmicrosoft.com/B2C_1A_TrustFrameworkBase/samlp/sso/assertionconsumer
      ```

      Använd följande format när du använder en [anpassad domän](custom-domain.md):

      ```
      https://your-domain-name/your-tenant.onmicrosoft.com/B2C_1A_TrustFrameworkBase/samlp/sso/assertionconsumer
      ```

7. Rulla längst ned i listan och klicka sedan på **Spara**.

### <a name="get-the-metadata-url"></a>Hämta metadata-URL

1. På översikts sidan för din anslutna app klickar du på **Hantera**.
2. Kopiera värdet för **slut punkten för identifiering av metadata** och spara det sedan. Du kommer att använda den senare i den här artikeln.

### <a name="set-up-salesforce-users-to-federate"></a>Konfigurera Salesforce-användare att federera

1. På sidan **Hantera** i din anslutna app klickar du på **Hantera profiler**.
2. Välj de profiler (eller grupper av användare) som du vill federera med Azure AD B2C. Som system administratör markerar du kryss rutan **system administratör** så att du kan federera med ditt Salesforce-konto.

## <a name="create-a-self-signed-certificate"></a>Skapa ett självsignerat certifikat

[!INCLUDE [active-directory-b2c-create-self-signed-certificate](../../includes/active-directory-b2c-create-self-signed-certificate.md)]

## <a name="create-a-policy-key"></a>Skapa en princip nyckel

Du måste lagra det certifikat som du skapade i Azure AD B2C-klienten.

1. Logga in på [Azure-portalen](https://portal.azure.com/).
2. Kontrol lera att du använder den katalog som innehåller din Azure AD B2C klient genom att välja filtret **katalog + prenumeration** på den översta menyn och välja den katalog som innehåller din klient.
3. Välj **Alla tjänster** på menyn högst upp till vänster i Azure-portalen och sök efter och välj **Azure AD B2C**.
4. På sidan Översikt väljer du **ID för identitets miljö**.
5. Välj **princip nycklar** och välj sedan **Lägg till**.
6. För **alternativ** väljer du `Upload` .
7. Ange ett **Namn** för principen. Till exempel SAMLSigningCert. Prefixet `B2C_1A_` läggs automatiskt till i namnet på din nyckel.
8. Bläddra till och välj det B2CSigningCert. PFX-certifikat som du skapade.
9. Ange **lösen ordet** för certifikatet.
3. Klicka på **Skapa**.

## <a name="add-a-claims-provider"></a>Lägg till en anspråks leverantör

Om du vill att användarna ska logga in med ett Salesforce-konto måste du definiera kontot som en anspråks leverantör som Azure AD B2C kan kommunicera med via en slut punkt. Slut punkten innehåller en uppsättning anspråk som används av Azure AD B2C för att verifiera att en speciell användare har autentiserats.

Du kan definiera ett Salesforce-konto som en anspråks leverantör genom att lägga till det i **ClaimsProviders** -elementet i principens tilläggs fil. Mer information finns i [definiera en SAML Identity Provider](identity-provider-generic-saml.md).

1. Öppna *TrustFrameworkExtensions.xml*.
1. Hitta **ClaimsProviders** -elementet. Om den inte finns lägger du till den under rot elementet.
1. Lägg till en ny **ClaimsProvider** enligt följande:

    ```xml
    <ClaimsProvider>
      <Domain>salesforce.com</Domain>
      <DisplayName>Salesforce</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="Salesforce-SAML2">
          <DisplayName>Salesforce</DisplayName>
          <Description>Login with your Salesforce account</Description>
          <Protocol Name="SAML2"/>
          <Metadata>
            <Item Key="WantsEncryptedAssertions">false</Item>
            <Item Key="WantsSignedAssertions">false</Item>
            <Item Key="PartnerEntity">https://contoso-dev-ed.my.salesforce.com/.well-known/samlidp.xml</Item>
          </Metadata>
          <CryptographicKeys>
            <Key Id="SamlMessageSigning" StorageReferenceId="B2C_1A_SAMLSigningCert"/>
          </CryptographicKeys>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="userId"/>
            <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="given_name"/>
            <OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="family_name"/>
            <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="email"/>
            <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="username"/>
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication"/>
            <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="salesforce.com" />
          </OutputClaims>
          <OutputClaimsTransformations>
            <OutputClaimsTransformation ReferenceId="CreateRandomUPNUserName"/>
            <OutputClaimsTransformation ReferenceId="CreateUserPrincipalName"/>
            <OutputClaimsTransformation ReferenceId="CreateAlternativeSecurityId"/>
            <OutputClaimsTransformation ReferenceId="CreateSubjectClaimFromAlternativeSecurityId"/>
          </OutputClaimsTransformations>
          <UseTechnicalProfileForSessionManagement ReferenceId="SM-Saml-idp"/>
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

1. Uppdatera värdet för **PartnerEntity** med URL: en för Salesforce-metadata som du kopierade tidigare.
1. Uppdatera värdet för båda instanserna av **StorageReferenceId** till namnet på nyckeln för ditt signerings certifikat. Till exempel B2C_1A_SAMLSigningCert.
1. Leta upp `<ClaimsProviders>` avsnittet och Lägg till följande XML-kodfragment. Om principen redan innehåller den `SM-Saml-idp` tekniska profilen går du vidare till nästa steg. Mer information finns i [hantering av enkel inloggnings session](custom-policy-reference-sso.md).

    ```xml
    <ClaimsProvider>
      <DisplayName>Session Management</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="SM-Saml-idp">
          <DisplayName>Session Management Provider</DisplayName>
          <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.SamlSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
          <Metadata>
            <Item Key="IncludeSessionIndex">false</Item>
            <Item Key="RegisterServiceProviders">false</Item>
          </Metadata>
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```
1. Spara filen.

[!INCLUDE [active-directory-b2c-add-identity-provider-to-user-journey](../../includes/active-directory-b2c-add-identity-provider-to-user-journey.md)]

```xml
<OrchestrationStep Order="1" Type="CombinedSignInAndSignUp" ContentDefinitionReferenceId="api.signuporsignin">
  <ClaimsProviderSelections>
    ...
    <ClaimsProviderSelection TargetClaimsExchangeId="SalesforceExchange" />
  </ClaimsProviderSelections>
  ...
</OrchestrationStep>

<OrchestrationStep Order="2" Type="ClaimsExchange">
  ...
  <ClaimsExchanges>
    <ClaimsExchange Id="SalesforceExchange" TechnicalProfileReferenceId="Salesforce-SAML2" />
  </ClaimsExchanges>
</OrchestrationStep>
```

[!INCLUDE [active-directory-b2c-configure-relying-party-policy](../../includes/active-directory-b2c-configure-relying-party-policy-user-journey.md)]

## <a name="test-your-custom-policy"></a>Testa din anpassade princip

1. Välj en princip för förlitande part, till exempel `B2C_1A_signup_signin` .
1. För **program** väljer du ett webb program som du [har registrerat tidigare](troubleshoot-custom-policies.md#troubleshoot-the-runtime). **Svars-URL: en** ska visas `https://jwt.ms` .
1. Välj knappen **Kör nu** .
1. På sidan registrering eller inloggning väljer du **Salesforce** för att logga in med Salesforce-konto.

Om inloggnings processen lyckas omdirigeras webbläsaren till `https://jwt.ms` , som visar innehållet i den token som returnerades av Azure AD B2C.

::: zone-end