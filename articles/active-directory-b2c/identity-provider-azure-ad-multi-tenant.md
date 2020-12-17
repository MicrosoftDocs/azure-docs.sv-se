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
ms.date: 12/07/2020
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 71e3bf429c7b8d3f4f8fe205c05b0701732fdef9
ms.sourcegitcommit: ad677fdb81f1a2a83ce72fa4f8a3a871f712599f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/17/2020
ms.locfileid: "97653817"
---
# <a name="set-up-sign-in-for-multi-tenant-azure-active-directory-using-custom-policies-in-azure-active-directory-b2c"></a>Konfigurera inloggning för Azure Active Directory för flera innehavare med anpassade principer i Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-custom-policy"

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

::: zone-end

## <a name="prerequisites"></a>Krav

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

Den här artikeln visar hur du aktiverar inloggning för användare som använder slut punkten för flera innehavare för Azure Active Directory (Azure AD). Detta gör att användare från flera Azure AD-klienter kan logga in med hjälp av Azure AD B2C, utan att du behöver konfigurera en identitets leverantör för varje klient. Gäst medlemmar i någon av dessa klienter **kommer dock inte** att kunna logga in. För det måste du [Konfigurera varje klient för sig](identity-provider-azure-ad-single-tenant.md).


## <a name="register-an-application"></a>Registrera ett program

Om du vill aktivera inloggning för användare från en specifik Azure AD-organisation måste du registrera ett program inom organisationens Azure AD-klient.

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Kontrol lera att du använder den katalog som innehåller din organisations Azure AD-klient (till exempel contoso.com). Välj **filtret katalog + prenumeration** på den översta menyn och välj sedan den katalog som innehåller din klient.
1. Välj **alla tjänster** i det övre vänstra hörnet av Azure Portal och Sök sedan efter och välj **Appregistreringar**.
1. Välj **ny registrering**.
1. Ange ett **namn** för ditt program. Ett exempel är `Azure AD B2C App`.
1. Välj **konton i valfri organisations katalog (alla Azure AD-kataloger – flera innehavare)** för det här programmet.
1. För **omdirigerings-URI: n**, godkänn värdet för **webb** och ange följande URL i gemener, där `your-B2C-tenant-name` ersätts med namnet på din Azure AD B2C-klient.

    ```
    https://your-B2C-tenant-name.b2clogin.com/your-B2C-tenant-name.onmicrosoft.com/oauth2/authresp
    ```

    Ett exempel är `https://fabrikam.b2clogin.com/fabrikam.onmicrosoft.com/oauth2/authresp`.

1. Välj **Register** (Registrera). Registrera **program-ID: t (Client)** för användning i ett senare steg.
1. Välj **certifikat & hemligheter** och välj sedan **ny klient hemlighet**.
1. Ange en **Beskrivning** av hemligheten, Välj förfallo datum och välj sedan **Lägg till**. Registrera **värdet** för hemligheten som ska användas i ett senare steg.

## <a name="configuring-optional-claims"></a>Konfigurera valfria anspråk

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

    Ett exempel är `https://login.microsoftonline.com/contoso.onmicrosoft.com/v2.0/.well-known/openid-configuration`.
    Ett exempel är `https://login.microsoftonline.com/contoso.com/v2.0/.well-known/openid-configuration`.

1. För **klient-ID** anger du det program-ID som du tidigare har registrerat.
1. För **klient hemlighet** anger du den klient hemlighet som du tidigare har registrerat.
1. För **scopet** anger du `openid profile` .
1. Lämna standardvärdena för **svars typ**, **svars läge** och **domän tips**.
1. Under **identitets leverantör anspråks mappning** väljer du följande anspråk:

    - **Användar-ID**: *OID*
    - **Visnings namn**: *namn*
    - **Tilldelat namn**: *given_name*
    - Efter **namn**: *family_name*
    - **E-post**: *preferred_username*

1. Välj **Spara**.

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="create-a-policy-key"></a>Skapa en princip nyckel

Du måste lagra den program nyckel som du skapade i Azure AD B2C klient organisationen.

1. Kontrol lera att du använder den katalog som innehåller din Azure AD B2C-klient. Välj **filtret katalog + prenumeration** på den översta menyn och välj sedan den katalog som innehåller Azure AD B2C klienten.
1. Välj **Alla tjänster** på menyn högst upp till vänster i Azure-portalen och sök efter och välj **Azure AD B2C**.
1. Under **principer** väljer du **Identity Experience Framework**.
1. Välj **princip nycklar** och välj sedan **Lägg till**.
1. För **alternativ** väljer du `Manual` .
1. Ange ett **namn** för princip nyckeln. Ett exempel är `AADAppSecret`.  Prefixet `B2C_1A_` läggs automatiskt till namnet på nyckeln när det skapas, så referensen i XML i följande avsnitt är att *B2C_1A_AADAppSecret*.
1. I **hemlighet** anger du din klient hemlighet som du registrerade tidigare.
1. För **nyckel användning** väljer du `Signature` .
1. Välj **Skapa**.

## <a name="add-a-claims-provider"></a>Lägg till en anspråks leverantör

Om du vill att användarna ska logga in med Azure AD måste du definiera Azure AD som en anspråks leverantör som Azure AD B2C kan kommunicera med via en slut punkt. Slut punkten innehåller en uppsättning anspråk som används av Azure AD B2C för att verifiera att en speciell användare har autentiserats.

Du kan definiera Azure AD som en anspråks leverantör genom att lägga till Azure AD i **ClaimsProvider** -elementet i tilläggs filen för din princip.

1. Öppna *TrustFrameworkExtensions.xml* -filen.
1. Hitta **ClaimsProviders** -elementet. Om den inte finns lägger du till den under rot elementet.
1. Lägg till en ny **ClaimsProvider** enligt följande:

    ```xml
    <ClaimsProvider>
      <Domain>commonaad</Domain>
      <DisplayName>Common AAD</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="Common-AAD">
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
1. Under **CryptographicKeys** uppdaterar du värdet för **StorageReferenceId** till namnet på den princip nyckel som skapades tidigare. Ett exempel är `B2C_1A_AADAppSecret`.

### <a name="restrict-access"></a>Begränsa åtkomst

> [!NOTE]
> Om du använder `https://login.microsoftonline.com/` som värde för **ValidTokenIssuerPrefixes** kan alla Azure AD-användare logga in i ditt program.

Du måste uppdatera listan över giltiga token-utfärdare och begränsa åtkomsten till en särskild lista över Azure AD-Innehavaradministratörer som kan logga in.

Hämta värdena genom att titta på OpenID Connect Discovery-metadata för var och en av de Azure AD-klienter som du vill att användarna ska logga in från. Formatet på URL: en för metadata liknar `https://login.microsoftonline.com/your-tenant/v2.0/.well-known/openid-configuration` , där `your-tenant` är namnet på din Azure AD-klient. Exempel:

`https://login.microsoftonline.com/fabrikam.onmicrosoft.com/v2.0/.well-known/openid-configuration`

Utför de här stegen för varje Azure AD-klient som ska användas för att logga in:

1. Öppna webbläsaren och gå till URL: en för OpenID Connect-metadata för klienten. Hitta **Issuer** -objektet och registrera dess värde. Det bör se ut ungefär så här `https://login.microsoftonline.com/00000000-0000-0000-0000-000000000000/` .
1. Kopiera och klistra in värdet i **ValidTokenIssuerPrefixes** -nyckeln. Avgränsa flera utfärdare med kommatecken. Ett exempel med två utfärdare visas i föregående `ClaimsProvider` XML-exempel.

### <a name="upload-the-extension-file-for-verification"></a>Ladda upp tilläggs filen för verifiering

Nu har du konfigurerat principen så att Azure AD B2C vet hur de kan kommunicera med dina Azure AD-kataloger. Försök att ladda upp tilläggs filen för principen för att bekräfta att den inte har några problem hittills.

1. På sidan **anpassade principer** i Azure AD B2C klienten väljer du **Ladda upp princip**.
2. Aktivera **Skriv över principen om den finns** och bläddra sedan till och välj *TrustFrameworkExtensions.xml* -filen.
3. Välj **Överför**.

## <a name="register-the-claims-provider"></a>Registrera anspråks leverantören

Nu har identitets leverantören kon figurer ATS, men den är inte tillgänglig på någon av inloggnings-och inloggnings skärmarna. För att göra det tillgängligt, skapar du en dubblett av en befintlig användar resa och ändrar den så att den även har Azure AD-identitetsprovider.

1. Öppna *TrustFrameworkBase.xml* -filen från start paketet.
2. Sök efter och kopiera hela innehållet i **UserJourney** -elementet som innehåller `Id="SignUpOrSignIn"` .
3. Öppna *TrustFrameworkExtensions.xml* och hitta **UserJourneys** -elementet. Om elementet inte finns lägger du till ett.
4. Klistra in hela innehållet i **UserJourney** -elementet som du kopierade som ett underordnat objekt till **UserJourneys** -elementet.
5. Byt namn på användar resans ID. Ett exempel är `SignUpSignInContoso`.

### <a name="display-the-button"></a>Visa knappen

**ClaimsProviderSelection** -elementet är detsamma som en identitetsprovider på en registrerings-och inloggnings skärm. Om du lägger till ett **ClaimsProviderSelection** -element för Azure AD visas en ny knapp när en användare hamnar på sidan.

1. Hitta **OrchestrationStep** -elementet som innehåller `Order="1"` i användar resan som du skapade i *TrustFrameworkExtensions.xml*.
1. Lägg till följande-element under **ClaimsProviderSelects**. Ange värdet för **TargetClaimsExchangeId** till ett lämpligt värde, till exempel `AzureADExchange` :

    ```xml
    <ClaimsProviderSelection TargetClaimsExchangeId="AzureADExchange" />
    ```

### <a name="link-the-button-to-an-action"></a>Länka knappen till en åtgärd

Nu när du har en knapp på plats måste du länka den till en åtgärd. Åtgärden, i det här fallet, är för Azure AD B2C för att kommunicera med Azure AD för att ta emot en token. Länka knappen till en åtgärd genom att länka den tekniska profilen för Azure AD-anspråks leverantören.

1. Hitta **OrchestrationStep** som ingår `Order="2"` i användar resan.
2. Lägg till följande **ClaimsExchange** -element och kontrol lera att du använder samma värde för **ID** som du använde för **TargetClaimsExchangeId**:

    ```xml
    <ClaimsExchange Id="AzureADExchange" TechnicalProfileReferenceId="Common-AAD" />
    ```

    Uppdatera värdet för **TechnicalProfileReferenceId** till **ID: t** för den tekniska profil som du skapade tidigare. Ett exempel är `Common-AAD`.

3. Spara *TrustFrameworkExtensions.xml* -filen och ladda upp den igen för verifiering.

::: zone-end

::: zone pivot="b2c-user-flow"

## <a name="add-azure-ad-identity-provider-to-a-user-flow"></a>Lägga till Azure AD Identity Provider i ett användar flöde 

1. Välj **användar flöden** i Azure AD B2C klient.
1. Klicka på det användar flöde som du vill använda för Azure AD Identity-providern.
1. Under **leverantörer av sociala identitet** väljer du **contoso Azure AD**.
1. Välj **Spara**.
1. Om du vill testa principen väljer du **Kör användar flöde**.
1. För **program** väljer du det webb program som heter *testapp1* som du tidigare har registrerat. **Svars-URL: en** ska visas `https://jwt.ms` .
1. Klicka på **Kör användar flöde**

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="update-and-test-the-relying-party-file"></a>Uppdatera och testa den förlitande part filen

Uppdatera den förlitande part filen (RP) som initierar användar resan som du skapade:

1. Gör en kopia av *SignUpOrSignIn.xml* i din arbets katalog och Byt namn på den. Du kan till exempel byta namn på den till *SignUpSignContoso.xml*.
1. Öppna den nya filen och uppdatera värdet för attributet **PolicyId** för **TrustFrameworkPolicy** med ett unikt värde. Ett exempel är `SignUpSignInContoso`.
1. Uppdatera värdet för **PublicPolicyUri** med URI: n för principen. Ett exempel är `http://contoso.com/B2C_1A_signup_signin_contoso`.
1. Uppdatera värdet för attributet **ReferenceId** i **DefaultUserJourney** för att matcha ID: t för användar resan som du skapade tidigare. Till exempel *SignUpSignInContoso*.
1. Spara ändringarna och ladda upp filen.
1. Välj den nyligen skapade principen från listan i de överförda **anpassade principerna**.
1. I list rutan **Välj program** väljer du det Azure AD B2C program som du skapade tidigare. Till exempel *testapp1*.
1. Kopiera **Kör nu-slutpunkten** och öppna den i ett privat webbläsarfönster, till exempel Incognito läge i Google Chrome eller ett InPrivate-fönster i Microsoft Edge. Genom att öppna ett privat webbläsarfönster kan du testa hela användar resan genom att inte använda några cachelagrade autentiseringsuppgifter för Azure AD.
1. Välj knappen för Azure AD-inloggning, till exempel *contoso Employee*, och ange sedan autentiseringsuppgifterna för en användare i någon av dina Azure AD-organisatoriska klienter. Du uppmanas att auktorisera programmet och sedan ange information om din profil.

Om inloggningen lyckas omdirigeras webbläsaren till `https://jwt.ms` , som visar innehållet i den token som returnerades av Azure AD B2C.

Testa inloggnings funktionen för flera innehavare genom att utföra de senaste två stegen med autentiseringsuppgifterna för en användare som finns i en annan Azure AD-klient.

## <a name="next-steps"></a>Nästa steg

När du arbetar med anpassade principer kan du ibland behöva ytterligare information när du felsöker en princip under dess utveckling.

För att hjälpa till att diagnostisera problem kan du tillfälligt ställa in principen i "utvecklarläge" och samla in loggar med Azure Application insikter. Ta reda på hur i [Azure Active Directory B2C: samlar in loggar](troubleshoot-with-application-insights.md).

::: zone-end