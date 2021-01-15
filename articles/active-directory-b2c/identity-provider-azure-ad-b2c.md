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
ms.date: 01/14/2021
ms.author: mimart
ms.subservice: B2C
ms.custom: fasttrack-edit, project-no-code
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: b8be516051f8eed0649064ae0f7c29a4dde85675
ms.sourcegitcommit: d59abc5bfad604909a107d05c5dc1b9a193214a8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/14/2021
ms.locfileid: "98224500"
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

Om du vill använda ett Azure AD B2C konto som [identitets leverantör](openid-connect.md) i din Azure AD B2C klient (till exempel contoso) i den andra Azure AD B2C (till exempel Fabrikam):

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

    Ett exempel är `https://contoso.b2clogin.com/contoso.onmicrosoft.com/oauth2/authresp`.

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

    Ett exempel är `https://fabrikam.b2clogin.com/fabrikam.onmicrosoft.com/B2C_1_susi/v2.0/.well-known/openid-configuration`.

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

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="create-a-policy-key"></a>Skapa en princip nyckel

Du måste lagra program nyckeln som du skapade tidigare i Azure AD B2C-klienten.

1. Kontrol lera att du använder den katalog som innehåller den Azure AD B2C klienten som du vill konfigurera federationen (till exempel contoso). Välj **katalog + prenumerations** filter på den översta menyn och välj den katalog som innehåller Azure AD B2C klient organisation (till exempel contoso).
1. Välj **Alla tjänster** på menyn högst upp till vänster i Azure-portalen och sök efter och välj **Azure AD B2C**.
1. Under **principer** väljer du **Identity Experience Framework**.
1. Välj **princip nycklar** och välj sedan **Lägg till**.
1. För **alternativ** väljer du `Manual` .
1. Ange ett **namn** för princip nyckeln. Ett exempel är `FabrikamAppSecret`.  Prefixet `B2C_1A_` läggs automatiskt till namnet på nyckeln när det skapas, så referensen i XML i följande avsnitt är att *B2C_1A_FabrikamAppSecret*.
1. I **hemlighet** anger du din klient hemlighet som du registrerade tidigare.
1. För **nyckel användning** väljer du `Signature` .
1. Välj **Skapa**.

## <a name="add-a-claims-provider"></a>Lägg till en anspråks leverantör

Om du vill att användarna ska logga in med hjälp av den andra Azure AD B2C (Fabrikam) måste du definiera de andra Azure AD B2C som en anspråks leverantör som Azure AD B2C kan kommunicera med via en slut punkt. Slut punkten innehåller en uppsättning anspråk som används av Azure AD B2C för att verifiera att en speciell användare har autentiserats.

Du kan definiera Azure AD B2C som anspråks leverantör genom att lägga till Azure AD B2C i **ClaimsProvider** -elementet i principens tilläggs fil.

1. Öppna *TrustFrameworkExtensions.xml* -filen.
1. Hitta **ClaimsProviders** -elementet. Om den inte finns lägger du till den under rot elementet.
1. Lägg till en ny **ClaimsProvider** enligt följande:
    ```xml
    <ClaimsProvider>
      <Domain>fabrikam.com</Domain>
      <DisplayName>Federation with Fabrikam tenant</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="Fabrikam-OpenIdConnect">
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
    |Metadata\METADATA|En URL som pekar på ett konfigurations dokument för OpenID Connect Identity Provider, som även kallas OpenID-känd konfigurations slut punkt. Ange följande URL som ersätter `{tenant}` med domän namnet för den andra Azure AD B2C klienten (Fabrikam). Ersätt `{tenant}` med namnet på principen som du konfigurerar i den andra klienten och `{policy]` med princip namnet: `https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/{policy}/v2.0/.well-known/openid-configuration` . Ett exempel är `https://fabrikam.b2clogin.com/fabrikam.onmicrosoft.com/B2C_1_susi/v2.0/.well-known/openid-configuration`.|
    |CryptographicKeys| Uppdatera värdet för **StorageReferenceId** till namnet på den princip nyckel som du skapade tidigare. Ett exempel är `B2C_1A_FabrikamAppSecret`.| 
    

### <a name="upload-the-extension-file-for-verification"></a>Ladda upp tilläggs filen för verifiering

Nu har du konfigurerat principen så att Azure AD B2C vet hur de kan kommunicera med den andra Azure AD B2C-klienten. Försök att ladda upp tilläggs filen för principen för att bekräfta att den inte har några problem hittills.

1. På sidan **anpassade principer** i Azure AD B2C klienten väljer du **Ladda upp princip**.
1. Aktivera **Skriv över principen om den finns** och bläddra sedan till och välj *TrustFrameworkExtensions.xml* -filen.
1. Klicka på **Överför**.

## <a name="register-the-claims-provider"></a>Registrera anspråks leverantören

I det här läget har identitets leverantören kon figurer ATS, men den är inte tillgänglig ännu på någon av sidorna för registrering/inloggning. Om du vill göra det tillgängligt skapar du en dubblett av en befintlig mall för användar resa och ändrar den så att den även har Azure AD-identitets leverantören:

1. Öppna *TrustFrameworkBase.xml* -filen från start paketet.
1. Sök efter och kopiera hela innehållet i **UserJourney** -elementet som innehåller `Id="SignUpOrSignIn"` .
1. Öppna *TrustFrameworkExtensions.xml* och hitta **UserJourneys** -elementet. Om elementet inte finns lägger du till ett.
1. Klistra in hela innehållet i **UserJourney** -elementet som du kopierade som ett underordnat objekt till **UserJourneys** -elementet.
1. Byt namn på användar resans ID. Ett exempel är `SignUpSignInFabrikam`.

### <a name="display-the-button"></a>Visa knappen

**ClaimsProviderSelection** -elementet är detsamma som en identitetsprovider på en registrerings-/inloggnings sida. Om du lägger till ett **ClaimsProviderSelection** -element för Azure AD B2C visas en ny knapp när en användare hamnar på sidan.

1. Hitta **OrchestrationStep** -elementet som innehåller `Order="1"` i användar resan som du skapade i *TrustFrameworkExtensions.xml*.
1. Lägg till följande-element under **ClaimsProviderSelections**. Ange värdet för **TargetClaimsExchangeId** till ett lämpligt värde, till exempel `FabrikamExchange` :

    ```xml
    <ClaimsProviderSelection TargetClaimsExchangeId="FabrikamExchange" />
    ```

### <a name="link-the-button-to-an-action"></a>Länka knappen till en åtgärd

Nu när du har en knapp på plats måste du länka den till en åtgärd. Åtgärden, i det här fallet, är att Azure AD B2C kommunicera med den andra Azure AD B2C för att ta emot en token. Länka knappen till en åtgärd genom att länka den tekniska profilen för Azure AD B2C anspråks leverantör:

1. Hitta **OrchestrationStep** som ingår `Order="2"` i användar resan.
1. Lägg till följande **ClaimsExchange** -element och kontrol lera att du använder samma värde för **ID** som du använde för **TargetClaimsExchangeId**:

    ```xml
    <ClaimsExchange Id="FabrikamExchange" TechnicalProfileReferenceId="Fabrikam-OpenIdConnect" />
    ```

    Uppdatera värdet för **TechnicalProfileReferenceId** till **ID: t** för den tekniska profil som du skapade tidigare. Ett exempel är `Fabrikam-OpenIdConnect`.

1. Spara *TrustFrameworkExtensions.xml* -filen och ladda upp den igen för verifiering.

::: zone-end

::: zone pivot="b2c-user-flow"

## <a name="add-azure-ad-b2c-identity-provider-to-a-user-flow"></a>Lägga till Azure AD B2C identitets leverantör i ett användar flöde 

1. Välj **användar flöden** i Azure AD B2C klient.
1. Klicka på det användar flöde som du vill lägga till Azure AD B2C Identity-providern.
1. Under **leverantörer av sociala identitet** väljer du **Fabrikam**.
1. Välj **Spara**.
1. Om du vill testa principen väljer du **Kör användar flöde**.
1. För **program** väljer du det webb program som heter *testapp1* som du tidigare har registrerat. **Svars-URL: en** ska visas `https://jwt.ms` .
1. Klicka på **Kör användar flöde**
1. Från registrerings-eller inloggnings sidan väljer du *Fabrikam* för att logga in med den andra Azure AD B2C klienten.

::: zone-end

::: zone pivot="b2c-custom-policy"


## <a name="update-and-test-the-relying-party-file"></a>Uppdatera och testa den förlitande part filen

Uppdatera den förlitande parten (RP) som initierar användar resan som du har skapat.

1. Gör en kopia av *SignUpOrSignIn.xml* i din arbets katalog och Byt namn på den. Du kan till exempel byta namn på den till *SignUpSignInFabrikam.xml*.
1. Öppna den nya filen och uppdatera värdet för attributet **PolicyId** för **TrustFrameworkPolicy** med ett unikt värde. Ett exempel är `SignUpSignInFabrikam`.
1. Uppdatera värdet för **PublicPolicyUri** med URI: n för principen. Ett exempel är `http://contoso.com/B2C_1A_signup_signin_fabrikam`.
1. Uppdatera värdet för attributet **ReferenceId** i **DefaultUserJourney** för att matcha ID: t för användar resan som du skapade tidigare. Till exempel *SignUpSignInFabrikam*.
1. Spara ändringarna och ladda upp filen.
1. Under **anpassade principer** väljer du den nya principen i listan.
1. I list rutan **Välj program** väljer du det Azure AD B2C program som du skapade tidigare. Till exempel *testapp1*.
1. Välj **Kör nu** 
1. Från registrerings-eller inloggnings sidan väljer du *Fabrikam* för att logga in med den andra Azure AD B2C klienten.

::: zone-end

## <a name="next-steps"></a>Nästa steg

Lär dig hur du [skickar den andra Azure AD B2C token till ditt program](idp-pass-through-user-flow.md).
