---
title: Konfigurera inloggning för Azure AD med flera innehavare med anpassade principer
titleSuffix: Azure AD B2C
description: Lägg till en Azure AD-identitetsprovider med flera innehavare med hjälp av anpassade principer i Azure Active Directory B2C.
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
ms.openlocfilehash: 9d9ba7e3e898da8d5b7d1bb4fbc69554ee205147
ms.sourcegitcommit: d40ffda6ef9463bb75835754cabe84e3da24aab5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/07/2021
ms.locfileid: "107028344"
---
# <a name="set-up-sign-in-for-multi-tenant-azure-active-directory-using-custom-policies-in-azure-active-directory-b2c"></a>Konfigurera inloggning för Azure Active Directory för flera innehavare med anpassade principer i Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-user-flow"

[!INCLUDE [active-directory-b2c-limited-to-custom-policy](../../includes/active-directory-b2c-limited-to-custom-policy.md)]

::: zone-end

::: zone pivot="b2c-custom-policy"

Den här artikeln visar hur du aktiverar inloggning för användare som använder slut punkten för flera innehavare för Azure Active Directory (Azure AD). Tillåta användare från flera Azure AD-klienter att logga in med hjälp av Azure AD B2C, utan att du behöver konfigurera en identitets leverantör för varje klient. Gäst medlemmar i någon av dessa klienter **kommer dock inte** att kunna logga in. För det måste du [Konfigurera varje klient för sig](identity-provider-azure-ad-single-tenant.md).

## <a name="prerequisites"></a>Förutsättningar

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

## <a name="register-an-application"></a>Registrera ett program

Om du vill aktivera inloggning för användare med ett Azure AD-konto i Azure Active Directory B2C (Azure AD B2C) måste du skapa ett program i [Azure Portal](https://portal.azure.com). Mer information finns i [Registrera ett program med Microsoft Identity Platform](../active-directory/develop/quickstart-register-app.md).


1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Kontrol lera att du använder den katalog som innehåller din organisations Azure AD-klient (till exempel contoso.com). Välj **filtret katalog + prenumeration** på den översta menyn och välj sedan den katalog som innehåller din klient.
1. Välj **alla tjänster** i det övre vänstra hörnet av Azure Portal och Sök sedan efter och välj **Appregistreringar**.
1. Välj **ny registrering**.
1. Ange ett **namn** för ditt program. Till exempel `Azure AD B2C App`.
1. Välj **konton i valfri organisations katalog (alla Azure AD-kataloger – flera innehavare)** för det här programmet.
1. För **omdirigerings-URI: n**, godkänn värdet för **webb** och ange följande URL i gemener, där `your-B2C-tenant-name` ersätts med namnet på din Azure AD B2C-klient.

    ```
    https://your-B2C-tenant-name.b2clogin.com/your-B2C-tenant-name.onmicrosoft.com/oauth2/authresp
    ```

    Till exempel `https://fabrikam.b2clogin.com/fabrikam.onmicrosoft.com/oauth2/authresp`.

    Om du använder en [anpassad domän](custom-domain.md)anger du `https://your-domain-name/your-tenant-name.onmicrosoft.com/oauth2/authresp` . Ersätt `your-domain-name` med din anpassade domän och `your-tenant-name` med namnet på din klient.

1. Välj **Register** (Registrera). Registrera **program-ID: t (Client)** för användning i ett senare steg.
1. Välj **certifikat & hemligheter** och välj sedan **ny klient hemlighet**.
1. Ange en **Beskrivning** av hemligheten, Välj förfallo datum och välj sedan **Lägg till**. Registrera **värdet** för hemligheten som ska användas i ett senare steg.

## <a name="configuring-optional-claims"></a>Konfigurera valfria anspråk

Om du vill hämta `family_name` , och `given_name` anspråk från Azure AD, kan du konfigurera valfria anspråk för programmet i Azure Portal användar gränssnitt eller program manifest. Mer information finns i [så här ger du valfria anspråk till din Azure AD-App](../active-directory/develop/active-directory-optional-claims.md).

1. Logga in på [Azure-portalen](https://portal.azure.com). Sök efter och välj **Azure Active Directory**.
1. I avsnittet **Hantera** väljer du **Appregistreringar**.
1. Välj det program som du vill konfigurera valfria anspråk för i listan.
1. I avsnittet **Hantera** väljer du **token-konfiguration**.
1. Välj **Lägg till valfritt anspråk**.
1. I **tokentyp** väljer du **ID**.
1. Välj de valfria anspråk som du vill lägga till, `family_name` och `given_name` .
1. Klicka på **Lägg till**.

## <a name="create-a-policy-key"></a>Skapa en princip nyckel

Du måste lagra den program nyckel som du skapade i Azure AD B2C klient organisationen.

1. Kontrol lera att du använder den katalog som innehåller din Azure AD B2C-klient. Välj **filtret katalog + prenumeration** på den översta menyn och välj sedan den katalog som innehåller Azure AD B2C klienten.
1. Välj **Alla tjänster** på menyn högst upp till vänster i Azure-portalen och sök efter och välj **Azure AD B2C**.
1. Under **principer** väljer du **Identity Experience Framework**.
1. Välj **princip nycklar** och välj sedan **Lägg till**.
1. För **alternativ** väljer du `Manual` .
1. Ange ett **namn** för princip nyckeln. Till exempel `AADAppSecret`.  Prefixet `B2C_1A_` läggs automatiskt till namnet på nyckeln när det skapas, så referensen i XML i följande avsnitt är att *B2C_1A_AADAppSecret*.
1. I **hemlighet** anger du din klient hemlighet som du registrerade tidigare.
1. För **nyckel användning** väljer du `Signature` .
1. Välj **Skapa**.

## <a name="configure-azure-ad-as-an-identity-provider"></a>Konfigurera Azure AD som en identitets leverantör

För att användarna ska kunna logga in med ett Azure AD-konto måste du definiera Azure AD som en anspråks leverantör som Azure AD B2C kan kommunicera med via en slut punkt. Slut punkten innehåller en uppsättning anspråk som används av Azure AD B2C för att verifiera att en speciell användare har autentiserats.

Du kan definiera Azure AD som en anspråks leverantör genom att lägga till Azure AD i **ClaimsProvider** -elementet i tilläggs filen för din princip.

1. Öppna *TrustFrameworkExtensions.xml* -filen.
1. Hitta **ClaimsProviders** -elementet. Om den inte finns lägger du till den under rot elementet.
1. Lägg till en ny **ClaimsProvider** enligt följande:

    ```xml
    <ClaimsProvider>
      <Domain>commonaad</Domain>
      <DisplayName>Common AAD</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="AADCommon-OpenIdConnect">
          <DisplayName>Multi-Tenant AAD</DisplayName>
          <Description>Login with your Contoso account</Description>
          <Protocol Name="OpenIdConnect"/>
          <Metadata>
            <Item Key="METADATA">https://login.microsoftonline.com/common/v2.0/.well-known/openid-configuration</Item>
            <!-- Update the Client ID below to the Application ID -->
            <Item Key="client_id">00000000-0000-0000-0000-000000000000</Item>
            <Item Key="response_types">code</Item>
            <Item Key="scope">openid profile</Item>
            <Item Key="response_mode">form_post</Item>
            <Item Key="HttpBinding">POST</Item>
            <Item Key="UsePolicyInRedirectUri">false</Item>
            <Item Key="DiscoverMetadataByTokenIssuer">true</Item>
            <!-- The key below allows you to specify each of the Azure AD tenants that can be used to sign in. Update the GUIDs below for each tenant. -->
            <Item Key="ValidTokenIssuerPrefixes">https://login.microsoftonline.com/00000000-0000-0000-0000-000000000000,https://login.microsoftonline.com/11111111-1111-1111-1111-111111111111</Item>
            <!-- The commented key below specifies that users from any tenant can sign-in. Uncomment if you would like anyone with an Azure AD account to be able to sign in. -->
            <!-- <Item Key="ValidTokenIssuerPrefixes">https://login.microsoftonline.com/</Item> -->
          </Metadata>
          <CryptographicKeys>
            <Key Id="client_secret" StorageReferenceId="B2C_1A_AADAppSecret"/>
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

1. Under elementet **ClaimsProvider** uppdaterar du värdet för **domän** till ett unikt värde som kan användas för att skilja den från andra identitets leverantörer.
1. Uppdatera värdet för **DisplayName** under **TechnicalProfile** -elementet, till exempel `Contoso Employee` . Det här värdet visas på inloggnings knappen på inloggnings sidan.
1. Ange **client_id** till program-ID: t för Azure AD-programmet för flera klient organisationer som du registrerade tidigare.
1. Under **CryptographicKeys** uppdaterar du värdet för **StorageReferenceId** till namnet på den princip nyckel som skapades tidigare. Till exempel `B2C_1A_AADAppSecret`.

### <a name="restrict-access"></a>Begränsa åtkomst

Om du använder `https://login.microsoftonline.com/` som värde för **ValidTokenIssuerPrefixes** kan alla Azure AD-användare logga in i ditt program. Uppdatera listan över giltiga token-utfärdare och begränsa åtkomsten till en särskild lista över Azure AD-Innehavaradministratörer som kan logga in.

Hämta värdena genom att titta på OpenID Connect Discovery-metadata för var och en av de Azure AD-klienter som du vill att användarna ska logga in från. Formatet på URL: en för metadata liknar `https://login.microsoftonline.com/your-tenant/v2.0/.well-known/openid-configuration` , där `your-tenant` är namnet på din Azure AD-klient. Exempel:

`https://login.microsoftonline.com/fabrikam.onmicrosoft.com/v2.0/.well-known/openid-configuration`

Utför de här stegen för varje Azure AD-klient som ska användas för att logga in:

1. Öppna webbläsaren och gå till URL: en för OpenID Connect-metadata för klienten. Hitta **Issuer** -objektet och registrera dess värde. Det bör se ut ungefär så här `https://login.microsoftonline.com/00000000-0000-0000-0000-000000000000/` .
1. Kopiera och klistra in värdet i **ValidTokenIssuerPrefixes** -nyckeln. Avgränsa flera utfärdare med kommatecken. Ett exempel med två utfärdare visas i föregående `ClaimsProvider` XML-exempel.

[!INCLUDE [active-directory-b2c-add-identity-provider-to-user-journey](../../includes/active-directory-b2c-add-identity-provider-to-user-journey.md)]


```xml
<OrchestrationStep Order="1" Type="CombinedSignInAndSignUp" ContentDefinitionReferenceId="api.signuporsignin">
  <ClaimsProviderSelections>
    ...
    <ClaimsProviderSelection TargetClaimsExchangeId="AzureADCommonExchange" />
  </ClaimsProviderSelections>
  ...
</OrchestrationStep>

<OrchestrationStep Order="2" Type="ClaimsExchange">
  ...
  <ClaimsExchanges>
    <ClaimsExchange Id="AzureADCommonExchange" TechnicalProfileReferenceId="AADCommon-OpenIdConnect" />
  </ClaimsExchanges>
</OrchestrationStep>
```

[!INCLUDE [active-directory-b2c-configure-relying-party-policy](../../includes/active-directory-b2c-configure-relying-party-policy-user-journey.md)]

## <a name="test-your-custom-policy"></a>Testa din anpassade princip

1. Välj en princip för förlitande part, till exempel `B2C_1A_signup_signin` .
1. För **program** väljer du ett webb program som du [har registrerat tidigare](tutorial-register-applications.md). **Svars-URL: en** ska visas `https://jwt.ms` .
1. Välj knappen **Kör nu** .
1. På sidan registrering eller inloggning väljer du **gemensamt AAD** för att logga in med Azure AD-konto.

Testa inloggnings funktionen för flera innehavare genom att utföra de senaste två stegen med autentiseringsuppgifterna för en användare som finns i en annan Azure AD-klient. Kopiera **Kör nu-slutpunkten** och öppna den i ett privat webbläsarfönster, till exempel Incognito läge i Google Chrome eller ett InPrivate-fönster i Microsoft Edge. Genom att öppna ett privat webbläsarfönster kan du testa hela användar resan genom att inte använda några cachelagrade autentiseringsuppgifter för Azure AD.

Om inloggnings processen lyckas omdirigeras webbläsaren till `https://jwt.ms` , som visar innehållet i den token som returnerades av Azure AD B2C.

## <a name="next-steps"></a>Nästa steg

När du arbetar med anpassade principer kan du ibland behöva ytterligare information när du felsöker en princip under dess utveckling.

För att hjälpa till att diagnostisera problem kan du tillfälligt ställa in principen i "utvecklarläge" och samla in loggar med Azure Application insikter. Ta reda på hur i [Azure Active Directory B2C: samlar in loggar](troubleshoot-with-application-insights.md).

::: zone-end
