---
title: Konfigurera registrering och inloggning med ett Google-konto
titleSuffix: Azure AD B2C
description: Tillhandahålla registrering och inloggning till kunder med Google-konton i dina program med hjälp av Azure Active Directory B2C.
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
ms.openlocfilehash: c8b942e66a76bcc3a095f9bd3d40b44bf4217e50
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/16/2020
ms.locfileid: "97584892"
---
# <a name="set-up-sign-up-and-sign-in-with-a-google-account-using-azure-active-directory-b2c"></a>Konfigurera registrering och inloggning med ett Google-konto med hjälp av Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-custom-policy"

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

::: zone-end

## <a name="prerequisites"></a>Krav

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]


## <a name="create-a-google-application"></a>Skapa ett Google-program

Om du vill använda ett Google-konto som [identitets leverantör](authorization-code-flow.md) i Azure Active Directory B2C (Azure AD B2C) måste du skapa ett program i din Google Developer-konsol. Om du inte redan har ett Google-konto kan du registrera dig på [https://accounts.google.com/SignUp](https://accounts.google.com/SignUp) .

1. Logga in på [Google Developer-konsolen](https://console.developers.google.com/) med dina Google-kontoautentiseringsuppgifter.
1. I det övre vänstra hörnet på sidan väljer du projekt listan och väljer sedan **nytt projekt**.
1. Ange ett **projekt namn** och välj **skapa**.
1. Se till att du använder det nya projektet genom att markera List rutan projekt i det övre vänstra hörnet av skärmen, markera ditt projekt efter namn och välj **Öppna**.
1. Välj alternativet **OAuth-medgivande** på den vänstra menyn, Välj **externt** och välj sedan **skapa**.
Ange ett **namn** för ditt program. Ange *b2clogin.com* i avsnittet **auktoriserade domäner** och välj **Spara**.
1. Välj **autentiseringsuppgifter** på den vänstra menyn och välj sedan **skapa autentiseringsuppgifter**  >  **OAuth klient-ID**.
1. Under **program typ** väljer du **webb program**.
1. Ange ett **namn** för ditt program, ange `https://your-tenant-name.b2clogin.com` i **auktoriserade JavaScript-ursprung** och `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` i **auktoriserade omdirigerings-URI: er**. Ersätt `your-tenant-name` med namnet på din klient. Du måste använda små bokstäver när du anger ditt klient namn även om klienten har definierats med versaler i Azure AD B2C.
1. Klicka på **Skapa**.
1. Kopiera värdena för **klient-ID** och **klient hemlighet**. Du behöver båda dessa för att kunna konfigurera Google som en identitets leverantör i din klient organisation. **Klient hemlighet** är en viktig säkerhets autentiseringsuppgift.

::: zone pivot="b2c-user-flow"

## <a name="configure-a-google-account-as-an-identity-provider"></a>Konfigurera ett Google-konto som en identitets leverantör

1. Logga in på [Azure Portal](https://portal.azure.com/) som global administratör för Azure AD B2C-klientorganisationen.
1. Kontrol lera att du använder den katalog som innehåller din Azure AD B2C klient genom att välja filtret **katalog + prenumeration** på den översta menyn och välja den katalog som innehåller din klient.
1. Välj **Alla tjänster** på menyn högst upp till vänster i Azure-portalen och sök efter och välj **Azure AD B2C**.
1. Välj **identitets leverantörer** och välj sedan **Google**.
1. Ange ett **namn**. Till exempel *Google*.
1. För **klient-ID** anger du klient-ID för det Google-program som du skapade tidigare.
1. Ange den klient hemlighet som du har spelat in för **klient hemligheten**.
1. Välj **Spara**.

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
7. Ange ett **namn** för princip nyckeln. Ett exempel är `GoogleSecret`. Prefixet `B2C_1A_` läggs till automatiskt till namnet på din nyckel.
8. I **hemlighet** anger du din klient hemlighet som du tidigare har spelat in.
9. För **nyckel användning** väljer du `Signature` .
10. Klicka på **Skapa**.

## <a name="add-a-claims-provider"></a>Lägg till en anspråks leverantör

Om du vill att användarna ska logga in med ett Google-konto måste du definiera kontot som en anspråks leverantör som Azure AD B2C kan kommunicera med via en slut punkt. Slut punkten innehåller en uppsättning anspråk som används av Azure AD B2C för att verifiera att en speciell användare har autentiserats.

Du kan definiera ett Google-konto som en anspråks leverantör genom att lägga till det i **ClaimsProviders** -elementet i principens tilläggs fil.

1. Öppna *TrustFrameworkExtensions.xml*.
2. Hitta **ClaimsProviders** -elementet. Om den inte finns lägger du till den under rot elementet.
3. Lägg till en ny **ClaimsProvider** enligt följande:

    ```xml
    <ClaimsProvider>
      <Domain>google.com</Domain>
      <DisplayName>Google</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="Google-OAUTH">
          <DisplayName>Google</DisplayName>
          <Protocol Name="OAuth2" />
          <Metadata>
            <Item Key="ProviderName">google</Item>
            <Item Key="authorization_endpoint">https://accounts.google.com/o/oauth2/auth</Item>
            <Item Key="AccessTokenEndpoint">https://accounts.google.com/o/oauth2/token</Item>
            <Item Key="ClaimsEndpoint">https://www.googleapis.com/oauth2/v1/userinfo</Item>
            <Item Key="scope">email profile</Item>
            <Item Key="HttpBinding">POST</Item>
            <Item Key="UsePolicyInRedirectUri">false</Item>
            <Item Key="client_id">Your Google application ID</Item>
          </Metadata>
          <CryptographicKeys>
            <Key Id="client_secret" StorageReferenceId="B2C_1A_GoogleSecret" />
          </CryptographicKeys>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="id" />
            <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="email" />
            <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="given_name" />
            <OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="family_name" />
            <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
            <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="google.com" />
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" />
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

4. Ange **client_id** till program-ID: t från program registreringen.
5. Spara filen.

### <a name="upload-the-extension-file-for-verification"></a>Ladda upp tilläggs filen för verifiering

Nu har du konfigurerat principen så att Azure AD B2C vet hur de kan kommunicera med ditt Google-konto. Försök att ladda upp tilläggs filen för principen för att bekräfta att den inte har några problem hittills.

1. På sidan **anpassade principer** i Azure AD B2C klienten väljer du **Ladda upp princip**.
2. Aktivera **Skriv över principen om den finns** och bläddra sedan till och välj *TrustFrameworkExtensions.xml* -filen.
3. Klicka på **Överför**.

## <a name="register-the-claims-provider"></a>Registrera anspråks leverantören

Nu har identitets leverantören kon figurer ATS, men den är inte tillgänglig på någon av inloggnings-och inloggnings skärmarna. Om du vill göra det tillgängligt skapar du en dubblett av en befintlig användar resa och ändrar den så att den även har Google Identity-providern.

1. Öppna *TrustFrameworkBase.xml* -filen från start paketet.
2. Sök efter och kopiera hela innehållet i **UserJourney** -elementet som innehåller `Id="SignUpOrSignIn"` .
3. Öppna *TrustFrameworkExtensions.xml* och hitta **UserJourneys** -elementet. Om elementet inte finns lägger du till ett.
4. Klistra in hela innehållet i **UserJourney** -elementet som du kopierade som ett underordnat objekt till **UserJourneys** -elementet.
5. Byt namn på användar resans ID. Ett exempel är `SignUpSignInGoogle`.

### <a name="display-the-button"></a>Visa knappen

**ClaimsProviderSelection** -elementet är detsamma som en identitetsprovider på en registrerings-och inloggnings skärm. Om du lägger till ett **ClaimsProviderSelection** -element för ett Google-konto visas en ny knapp när en användare hamnar på sidan.

1. Hitta **OrchestrationStep** -elementet som innehåller `Order="1"` i användar resan som du skapade.
2. Lägg till följande-element under **ClaimsProviderSelects**. Ange värdet för **TargetClaimsExchangeId** till ett lämpligt värde, till exempel `GoogleExchange` :

    ```xml
    <ClaimsProviderSelection TargetClaimsExchangeId="GoogleExchange" />
    ```

### <a name="link-the-button-to-an-action"></a>Länka knappen till en åtgärd

Nu när du har en knapp på plats måste du länka den till en åtgärd. Åtgärden, i det här fallet, är att Azure AD B2C kommunicera med ett Google-konto för att ta emot en token.

1. Hitta **OrchestrationStep** som ingår `Order="2"` i användar resan.
2. Lägg till följande **ClaimsExchange** -element och kontrol lera att du använder samma värde för ID som du använde för **TargetClaimsExchangeId**:

    ```xml
    <ClaimsExchange Id="GoogleExchange" TechnicalProfileReferenceId="Google-OAuth" />
    ```

    Uppdatera värdet för **TechnicalProfileReferenceId** till ID: t för den tekniska profil som du skapade tidigare. Ett exempel är `Google-OAuth`.

3. Spara *TrustFrameworkExtensions.xml* -filen och ladda upp den igen för verifiering.

::: zone-end

::: zone pivot="b2c-user-flow"

## <a name="add-google-identity-provider-to-a-user-flow"></a>Lägga till Google Identity Provider i ett användar flöde 

1. Välj **användar flöden** i Azure AD B2C klient.
1. Klicka på det användar flöde som du vill använda för Google Identity-providern.
1. Under **leverantörer av sociala identitet** väljer du **Google**.
1. Välj **Spara**.
1. Om du vill testa principen väljer du **Kör användar flöde**.
1. För **program** väljer du det webb program som heter *testapp1* som du tidigare har registrerat. **Svars-URL: en** ska visas `https://jwt.ms` .
1. Klicka på **Kör användar flöde**

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="update-and-test-the-relying-party-file"></a>Uppdatera och testa den förlitande part filen

Uppdatera den förlitande parten (RP) som initierar användar resan som du har skapat.

1. Gör en kopia av *SignUpOrSignIn.xml* i din arbets katalog och Byt namn på den. Du kan till exempel byta namn på den till *SignUpSignInGoogle.xml*.
1. Öppna den nya filen och uppdatera värdet för attributet **PolicyId** för **TrustFrameworkPolicy** med ett unikt värde. Ett exempel är `SignUpSignInGoogle`.
1. Uppdatera värdet för **PublicPolicyUri** med URI: n för principen. Till exempel`http://contoso.com/B2C_1A_signup_signin_google`
1. Uppdatera värdet för attributet **ReferenceId** i **DefaultUserJourney** för att matcha ID för den nya användar resan som du skapade (SignUpSignGoogle).
1. Spara ändringarna, ladda upp filen.
1. Under **anpassade principer** väljer du **B2C_1A_signup_signin**.
1. För **Välj program** väljer du det webb program som heter *testapp1* som du tidigare har registrerat. **Svars-URL: en** ska visas `https://jwt.ms` .
1. Välj **Kör nu** och välj Google för att logga in med Google och testa den anpassade principen.

::: zone-end

## <a name="next-steps"></a>Nästa steg

Lär dig hur du [skickar Google-token till ditt program](idp-pass-through-user-flow.md).