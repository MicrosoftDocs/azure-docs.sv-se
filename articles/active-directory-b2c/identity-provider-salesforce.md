---
title: Konfigurera registrering och inloggning med ett Salesforce-konto
titleSuffix: Azure AD B2C
description: Tillhandahålla registrering och inloggning till kunder med Salesforce-konton i dina program med hjälp av Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 01/27/2021
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 0981687b03344daf7a447cc4d9e50f0923341340
ms.sourcegitcommit: 436518116963bd7e81e0217e246c80a9808dc88c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/27/2021
ms.locfileid: "98952299"
---
# <a name="set-up-sign-up-and-sign-in-with-a-salesforce-account-using-azure-active-directory-b2c"></a>Konfigurera registrering och inloggning med ett Salesforce-konto med hjälp av Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-custom-policy"

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

::: zone-end

## <a name="prerequisites"></a>Förutsättningar

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]


## <a name="create-a-salesforce-application"></a>Skapa ett Salesforce-program

Om du vill aktivera inloggning för användare med ett Salesforce-konto i Azure Active Directory B2C (Azure AD B2C) måste du skapa ett program i Salesforce [App Manager](https://login.salesforce.com/). Mer information finns i [Konfigurera inställningar för grundläggande anslutna appar](https://help.salesforce.com/articleView?id=connected_app_create_basics.htm)och [Aktivera OAuth-inställningar för API-integrering](https://help.salesforce.com/articleView?id=connected_app_create_api_integration.htm)

1. [Logga in på Salesforce](https://login.salesforce.com/).
1. I menyn väljer du **Inställningar**.
1.  Expandera **appar** och välj sedan **App Manager**.
1. Välj **ny ansluten app**.
1. Under **grundläggande information** anger du:
    1. **Namnet på den anslutna appen** – namnet på den anslutna appen visas i App Manager och i panelen App Launcher. Namnet måste vara unikt i din organisation. 
    1. **API-namn** 
    1. **Kontakta e** -postadress – e-postkontakten för Salesforce
1. Under **API (Aktivera OAuth-inställningar)** väljer du **Aktivera OAuth-inställningar**
    1. I **återanrops-URL** anger du `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` . Ersätt `your-tenant-name` med namnet på din klient. Du måste använda små bokstäver när du anger ditt klient namn även om klienten har definierats med versaler i Azure AD B2C.
    1. I de **valda OAuth-omfattningarna** väljer **du åtkomst till grundläggande information (ID, profil, e-post, adress, telefon)** och **tillåter åtkomst till din unika identifierare (OpenID)**.
    1. Välj **Kräv hemlighet för webb server flöde**.
1. Välj **Konfigurera ID-token** 
    1. Ange **token som är giltig i** 5 minuter.
    1. Välj **inkludera standard anspråk**.
1. Klicka på **Spara**.
1. Kopiera värdena för **konsument nyckel** och **konsument hemlighet**. Du behöver båda dessa för att konfigurera Salesforce som en identitets leverantör i din klient. **Klient hemlighet** är en viktig säkerhets autentiseringsuppgift.

::: zone pivot="b2c-user-flow"

## <a name="configure-salesforce-as-an-identity-provider"></a>Konfigurera Salesforce som identitets leverantör

1. Kontrol lera att du använder den katalog som innehåller Azure AD B2C klient. Välj **katalog + prenumerations** filter på den översta menyn och välj den katalog som innehåller Azure AD B2C klienten.
1. Välj **Alla tjänster** på menyn högst upp till vänster i Azure-portalen och sök efter och välj **Azure AD B2C**.
1. Välj **identitets leverantörer** och välj sedan **ny OpenID Connect-Provider**.
1. Ange ett **namn**. Ange till exempel *Salesforce*.
1. För **metadata-URL** anger du URL: en för [Salesforce-OpenID Connect-konfigurationsfilen](https://help.salesforce.com/articleView?id=remoteaccess_using_openid_discovery_endpoint.htm). För en sandbox ersätts login.salesforce.com med test.salesforce.com. För en community ersätts login.salesforce.com med grupp-URL: en, till exempel username.force.com/.well-known/openid-configuration. URL: en måste vara HTTPS.

    ```
    https://login.salesforce.com/.well-known/openid-configuration
    ```

1. För **klient-ID** anger du det program-ID som du tidigare har registrerat.
1. För **klient hemlighet** anger du den klient hemlighet som du tidigare har registrerat.
1. För **scopet** anger du `openid id profile email` .
1. Lämna standardvärdena för **svars typ** och **svars läge**.
1. Valfritt I **domän tipset** anger du `contoso.com` . Mer information finns i [Konfigurera direkt inloggning med Azure Active Directory B2C](direct-signin.md#redirect-sign-in-to-a-social-provider).
1. Under **identitets leverantör anspråks mappning** väljer du följande anspråk:

    - **Användar-ID**: *Sub*
    - **Visnings namn**: *namn*
    - **Tilldelat namn**: *given_name*
    - Efter **namn**: *family_name*
    - **E-post**: *e-post*

1. Välj **Spara**.

## <a name="add-salesforce-identity-provider-to-a-user-flow"></a>Lägg till Salesforce-identitetsprovider i ett användar flöde 

1. Välj **användar flöden** i Azure AD B2C klient.
1. Klicka på det användar flöde som du vill lägga till Salesforce Identity Provider.
1. Under **leverantörer av sociala identitet** väljer du **Salesforce**.
1. Välj **Spara**.
1. Om du vill testa principen väljer du **Kör användar flöde**.
1. För **program** väljer du det webb program som heter *testapp1* som du tidigare har registrerat. **Svars-URL: en** ska visas `https://jwt.ms` .
1. Klicka på **Kör användar flöde**

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
7. Ange ett **namn** för princip nyckeln. Ett exempel är `SalesforceSecret`. Prefixet `B2C_1A_` läggs till automatiskt till namnet på din nyckel.
8. I **hemlighet** anger du din klient hemlighet som du tidigare har spelat in.
9. För **nyckel användning** väljer du `Signature` .
10. Klicka på **Skapa**.

## <a name="configure-salesforce-as-an-identity-provider"></a>Konfigurera Salesforce som identitets leverantör

Om du vill att användarna ska kunna logga in med ett Salesforce-konto måste du definiera kontot som en anspråks leverantör som Azure AD B2C kan kommunicera med via en slut punkt. Slut punkten innehåller en uppsättning anspråk som används av Azure AD B2C för att verifiera att en speciell användare har autentiserats.

Du kan definiera ett Salesforce-konto som en anspråks leverantör genom att lägga till det i **ClaimsProviders** -elementet i principens tilläggs fil.

1. Öppna *TrustFrameworkExtensions.xml*.
2. Hitta **ClaimsProviders** -elementet. Om den inte finns lägger du till den under rot elementet.
3. Lägg till en ny **ClaimsProvider** enligt följande:

    ```xml
    <ClaimsProvider>
      <Domain>salesforce.com</Domain>
      <DisplayName>Salesforce</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="Salesforce-OpenIdConnect">
          <DisplayName>Salesforce</DisplayName>
          <Protocol Name="OpenIdConnect" />
          <Metadata>
            <Item Key="METADATA">https://login.salesforce.com/.well-known/openid-configuration</Item>
            <Item Key="response_types">code</Item>
            <Item Key="response_mode">form_post</Item>
            <Item Key="scope">openid id profile email</Item>
            <Item Key="HttpBinding">POST</Item>
            <Item Key="UsePolicyInRedirectUri">0</Item>
            <!-- Update the Client ID below to the Application ID -->
            <Item Key="client_id">Your Salesforce application ID</Item>
          </Metadata>
          <CryptographicKeys>
            <Key Id="client_secret" StorageReferenceId="B2C_1A_SalesforceSecret"/>
          </CryptographicKeys>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="sub" />
            <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="given_name" />
            <OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="family_name" />
            <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
            <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="email" />
            <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="salesforce.com" AlwaysUseDefaultValue="true" />
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" AlwaysUseDefaultValue="true" />
          </OutputClaims>
          <OutputClaimsTransformations>
            <OutputClaimsTransformation ReferenceId="CreateRandomUPNUserName" />
            <OutputClaimsTransformation ReferenceId="CreateUserPrincipalName" />
            <OutputClaimsTransformation ReferenceId="CreateAlternativeSecurityId" />
          </OutputClaimsTransformations>
          <UseTechnicalProfileForSessionManagement ReferenceId="SM-SocialLogin" />
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

4. **Metadata** anges till URL: en för Salesforce- [OpenID Connect-konfigurationsfilen](https://help.salesforce.com/articleView?id=remoteaccess_using_openid_discovery_endpoint.htm). För en sandbox ersätts login.salesforce.com med test.salesforce.com. För en community ersätts login.salesforce.com med grupp-URL: en, till exempel username.force.com/.well-known/openid-configuration. URL: en måste vara HTTPS.
5. Ange **client_id** till program-ID: t från program registreringen.
6. Spara filen.

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
    <ClaimsExchange Id="SalesforceExchange" TechnicalProfileReferenceId="Salesforce-OpenIdConnect" />
  </ClaimsExchanges>
</OrchestrationStep>
```

[!INCLUDE [active-directory-b2c-configure-relying-party-policy](../../includes/active-directory-b2c-configure-relying-party-policy-user-journey.md)]

[!INCLUDE [active-directory-b2c-test-relying-party-policy](../../includes/active-directory-b2c-test-relying-party-policy-user-journey.md)]

::: zone-end

## <a name="next-steps"></a>Nästa steg

Lär dig hur du [skickar Salesforce-token till ditt program](idp-pass-through-user-flow.md).
