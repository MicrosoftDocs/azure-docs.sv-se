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
ms.date: 12/07/2020
ms.author: mimart
ms.subservice: B2C
ms.custom: fasttrack-edit, project-no-code
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 05c4d36f266fb526a1d0232cc32f0408e4322c80
ms.sourcegitcommit: ad677fdb81f1a2a83ce72fa4f8a3a871f712599f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/17/2020
ms.locfileid: "97654395"
---
# <a name="set-up-sign-in-for-a-specific-azure-active-directory-organization-in-azure-active-directory-b2c"></a>Konfigurera inloggning för en speciell Azure Active Directory organisation i Azure Active Directory B2C

Den här artikeln visar hur du aktiverar inloggning för användare från en särskild Azure AD-organisation med hjälp av ett användar flöde i Azure AD B2C.

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-custom-policy"

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

::: zone-end

## <a name="prerequisites"></a>Krav

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

## <a name="register-an-azure-ad-app"></a>Registrera en Azure AD-app

Om du vill aktivera inloggning för användare från en specifik Azure AD-organisation måste du registrera ett program inom organisationens Azure AD-klient.

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Kontrol lera att du använder den katalog som innehåller din organisations Azure AD-klient (till exempel contoso.com). Välj **filtret katalog + prenumeration** på den översta menyn och välj sedan den katalog som innehåller din Azure AD-klient.
1. Välj **alla tjänster** i det övre vänstra hörnet av Azure Portal och Sök sedan efter och välj **Appregistreringar**.
1. Välj **ny registrering**.
1. Ange ett **namn** för ditt program. Ett exempel är `Azure AD B2C App`.
1. Godkänn standard valet av **konton endast i den här organisations katalogen** för det här programmet.
1. För **omdirigerings-URI: n**, godkänn värdet för **webb** och ange följande URL i gemener, där `your-B2C-tenant-name` ersätts med namnet på din Azure AD B2C-klient.

    ```
    https://your-B2C-tenant-name.b2clogin.com/your-B2C-tenant-name.onmicrosoft.com/oauth2/authresp
    ```

    Ett exempel är `https://fabrikam.b2clogin.com/fabrikam.onmicrosoft.com/oauth2/authresp`.

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

    Ett exempel är `https://login.microsoftonline.com/contoso.onmicrosoft.com/v2.0/.well-known/openid-configuration`.
    Ett exempel är `https://login.microsoftonline.com/contoso.com/v2.0/.well-known/openid-configuration`.

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

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="create-a-policy-key"></a>Skapa en princip nyckel

Du måste lagra den program nyckel som du skapade i Azure AD B2C klient organisationen.

1. Kontrol lera att du använder den katalog som innehåller din Azure AD B2C-klient. Välj **filtret katalog + prenumeration** på den översta menyn och välj sedan den katalog som innehåller Azure AD B2C klienten.
1. Välj **Alla tjänster** på menyn högst upp till vänster i Azure-portalen och sök efter och välj **Azure AD B2C**.
1. Under **principer** väljer du **Identity Experience Framework**.
1. Välj **princip nycklar** och välj sedan **Lägg till**.
1. För **alternativ** väljer du `Manual` .
1. Ange ett **namn** för princip nyckeln. Ett exempel är `ContosoAppSecret`.  Prefixet `B2C_1A_` läggs automatiskt till namnet på nyckeln när det skapas, så referensen i XML i följande avsnitt är att *B2C_1A_ContosoAppSecret*.
1. I **hemlighet** anger du din klient hemlighet som du registrerade tidigare.
1. För **nyckel användning** väljer du `Signature` .
1. Välj **Skapa**.

## <a name="add-a-claims-provider"></a>Lägg till en anspråks leverantör

Om du vill att användarna ska logga in med Azure AD måste du definiera Azure AD som en anspråks leverantör som Azure AD B2C kan kommunicera med via en slut punkt. Slut punkten innehåller en uppsättning anspråk som används av Azure AD B2C för att verifiera att en speciell användare har autentiserats.

Du kan definiera Azure AD som en anspråks leverantör genom att lägga till Azure AD i **ClaimsProvider** -elementet i tilläggs filen för din princip.

1. Öppna *TrustFrameworkExtensions.xml* -filen.
2. Hitta **ClaimsProviders** -elementet. Om den inte finns lägger du till den under rot elementet.
3. Lägg till en ny **ClaimsProvider** enligt följande:
    ```xml
    <ClaimsProvider>
      <Domain>Contoso</Domain>
      <DisplayName>Login using Contoso</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="OIDC-Contoso">
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

1. Uppdatera ID för **TechnicalProfile** -elementet. Detta ID används för att referera till den här tekniska profilen från andra delar av principen, till exempel `OIDC-Contoso` .
1. Uppdatera värdet för **DisplayName**. Det här värdet kommer att visas på inloggnings knappen på inloggnings skärmen.
1. Uppdatera värdet för **Beskrivning**.
1. Azure AD använder OpenID Connect-protokollet, så se till att värdet för **protokollet** är `OpenIdConnect` .
1. Ange värdet för **metadata** till `https://login.microsoftonline.com/tenant-name.onmicrosoft.com/v2.0/.well-known/openid-configuration` , där `tenant-name` är namnet på din Azure AD-klient. Till exempel `https://login.microsoftonline.com/contoso.onmicrosoft.com/v2.0/.well-known/openid-configuration`
1. Ange **client_id** till program-ID: t från program registreringen.
1. Under **CryptographicKeys** uppdaterar du värdet för **StorageReferenceId** till namnet på den princip nyckel som du skapade tidigare. Ett exempel är `B2C_1A_ContosoAppSecret`.

### <a name="upload-the-extension-file-for-verification"></a>Ladda upp tilläggs filen för verifiering

Nu har du konfigurerat principen så att Azure AD B2C vet hur de kan kommunicera med Azure AD-katalogen. Försök att ladda upp tilläggs filen för principen för att bekräfta att den inte har några problem hittills.

1. På sidan **anpassade principer** i Azure AD B2C klienten väljer du **Ladda upp princip**.
1. Aktivera **Skriv över principen om den finns** och bläddra sedan till och välj *TrustFrameworkExtensions.xml* -filen.
1. Klicka på **Överför**.

## <a name="register-the-claims-provider"></a>Registrera anspråks leverantören

I det här läget har identitets leverantören kon figurer ATS, men den är inte tillgänglig ännu på någon av sidorna för registrering/inloggning. Om du vill göra det tillgängligt skapar du en dubblett av en befintlig mall för användar resa och ändrar den så att den även har Azure AD-identitets leverantören:

1. Öppna *TrustFrameworkBase.xml* -filen från start paketet.
1. Sök efter och kopiera hela innehållet i **UserJourney** -elementet som innehåller `Id="SignUpOrSignIn"` .
1. Öppna *TrustFrameworkExtensions.xml* och hitta **UserJourneys** -elementet. Om elementet inte finns lägger du till ett.
1. Klistra in hela innehållet i **UserJourney** -elementet som du kopierade som ett underordnat objekt till **UserJourneys** -elementet.
1. Byt namn på användar resans ID. Ett exempel är `SignUpSignInContoso`.

### <a name="display-the-button"></a>Visa knappen

**ClaimsProviderSelection** -elementet är detsamma som en identitetsprovider på en registrerings-/inloggnings sida. Om du lägger till ett **ClaimsProviderSelection** -element för Azure AD visas en ny knapp när en användare hamnar på sidan.

1. Hitta **OrchestrationStep** -elementet som innehåller `Order="1"` i användar resan som du skapade i *TrustFrameworkExtensions.xml*.
1. Lägg till följande-element under **ClaimsProviderSelections**. Ange värdet för **TargetClaimsExchangeId** till ett lämpligt värde, till exempel `ContosoExchange` :

    ```xml
    <ClaimsProviderSelection TargetClaimsExchangeId="ContosoExchange" />
    ```

### <a name="link-the-button-to-an-action"></a>Länka knappen till en åtgärd

Nu när du har en knapp på plats måste du länka den till en åtgärd. Åtgärden, i det här fallet, är för Azure AD B2C för att kommunicera med Azure AD för att ta emot en token. Länka knappen till en åtgärd genom att länka den tekniska profilen för Azure AD-anspråks leverantören:

1. Hitta **OrchestrationStep** som ingår `Order="2"` i användar resan.
1. Lägg till följande **ClaimsExchange** -element och kontrol lera att du använder samma värde för **ID** som du använde för **TargetClaimsExchangeId**:

    ```xml
    <ClaimsExchange Id="ContosoExchange" TechnicalProfileReferenceId="OIDC-Contoso" />
    ```

    Uppdatera värdet för **TechnicalProfileReferenceId** till **ID: t** för den tekniska profil som du skapade tidigare. Ett exempel är `OIDC-Contoso`.

1. Spara *TrustFrameworkExtensions.xml* -filen och ladda upp den igen för verifiering.

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

Uppdatera den förlitande parten (RP) som initierar användar resan som du har skapat.

1. Gör en kopia av *SignUpOrSignIn.xml* i din arbets katalog och Byt namn på den. Du kan till exempel byta namn på den till *SignUpSignInContoso.xml*.
1. Öppna den nya filen och uppdatera värdet för attributet **PolicyId** för **TrustFrameworkPolicy** med ett unikt värde. Ett exempel är `SignUpSignInContoso`.
1. Uppdatera värdet för **PublicPolicyUri** med URI: n för principen. Ett exempel är `http://contoso.com/B2C_1A_signup_signin_contoso`.
1. Uppdatera värdet för attributet **ReferenceId** i **DefaultUserJourney** för att matcha ID: t för användar resan som du skapade tidigare. Till exempel *SignUpSignInContoso*.
1. Spara ändringarna och ladda upp filen.
1. Under **anpassade principer** väljer du den nya principen i listan.
1. I list rutan **Välj program** väljer du det Azure AD B2C program som du skapade tidigare. Till exempel *testapp1*.
1. Kopiera **Kör nu-slutpunkten** och öppna den i ett privat webbläsarfönster, till exempel Incognito läge i Google Chrome eller ett InPrivate-fönster i Microsoft Edge. Genom att öppna ett privat webbläsarfönster kan du testa hela användar resan genom att inte använda några cachelagrade autentiseringsuppgifter för Azure AD.
1. Välj knappen för Azure AD-inloggning, till exempel *contoso Employee*, och ange sedan autentiseringsuppgifterna för en användare i din Azure AD-organisatoriska klient. Du uppmanas att auktorisera programmet och sedan ange information om din profil.

Om inloggningen lyckas omdirigeras webbläsaren till `https://jwt.ms` , som visar innehållet i den token som returnerades av Azure AD B2C.

## <a name="next-steps"></a>Nästa steg

När du arbetar med anpassade principer kan du ibland behöva ytterligare information när du felsöker en princip under dess utveckling.

För att hjälpa till att diagnostisera problem kan du tillfälligt ställa in principen i "utvecklarläge" och samla in loggar med Azure Application insikter. Ta reda på hur i [Azure Active Directory B2C: samlar in loggar](troubleshoot-with-application-insights.md).

::: zone-end