---
title: Konfigurera registrering och inloggning med ett Apple-ID
titleSuffix: Azure AD B2C
description: Tillhandahålla registrering och inloggning till kunder med Apple-ID i dina program med hjälp av Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/22/2021
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 4c3308ff9b1ffeb0f14c5808e0d4cdd71dabf335
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/22/2021
ms.locfileid: "104780131"
---
# <a name="set-up-sign-up-and-sign-in-with-an-apple-id--using-azure-active-directory-b2c-preview"></a>Konfigurera registrering och inloggning med ett Apple-ID med hjälp av Azure Active Directory B2C (för hands version)

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-custom-policy"

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

::: zone-end

## <a name="prerequisites"></a>Förutsättningar

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

## <a name="create-an-apple-id-application"></a>Skapa ett Apple-ID-program

Om du vill aktivera inloggning för användare med ett Apple-ID i Azure Active Directory B2C (Azure AD B2C) måste du skapa ett program i [https://developer.apple.com](https://developer.apple.com) . Mer information finns i [Logga in med Apple](https://developer.apple.com/sign-in-with-apple/get-started/). Om du inte redan har ett Apple Developer-konto kan du registrera dig hos [Apple Developer-programmet](https://developer.apple.com/programs/enroll/).

1. Logga in på [Apple Developer-portalen](https://developer.apple.com/account) med dina autentiseringsuppgifter för kontot.
1. Från menyn väljer du **certifikat, ID: n & profiler** och väljer sedan **(+)**.
1. Om du vill **Registrera en ny identifierare** väljer du **app-ID: n** och väljer sedan **Fortsätt**.
1. Välj **app** i **Välj en typ** och välj sedan **Fortsätt**.
1. **Registrera ett app-ID**:
    1. Ange en **Beskrivning** 
    1. Ange **paket-ID**: t, till exempel `com.contoso.azure-ad-b2c` . 
    1. För **funktioner** väljer du **Logga in med Apple** från listan funktioner. 
    1. Anteckna ditt app-ID-prefix (Team-ID) från det här steget. Du behöver det senare.
    1. Välj **Fortsätt** och **Registrera** sedan.
1. Från menyn väljer du **certifikat, ID: n & profiler** och väljer sedan **(+)**.
1. Om du vill **Registrera en ny identifierare** väljer du **Services ID** och väljer sedan **Fortsätt**.
1. **Registrera ett tjänste-ID**:
    1. Ange en **Beskrivning**. Beskrivningen visas för användaren på skärmen medgivande.
    1. Ange **identifieraren**, till exempel `com.consoto.azure-ad-b2c-service` . Identifieraren är ditt klient-ID för OpenID Connect-flödet.
    1. Välj **Fortsätt** och välj sedan **Registrera**.
1. Från **identifierare** väljer du den identifierare som du skapade.
1. Välj **Logga in med Apple** och välj sedan **Konfigurera**.
    1. Välj det **primära app-ID** som du vill konfigurera logga in med Apple med.
    1. I **domäner och under domäner** anger du `your-tenant-name.b2clogin.com` . Ersätt ditt-Tenant-namn med namnet på din klient. Om du använder en [anpassad domän](custom-domain.md)anger du `https://your-domain-name` .
    1. I **Retur-URL: er** anger du `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` . Om du använder en [anpassad domän](custom-domain.md)anger du `https://your-domain-name/your-tenant-name.onmicrosoft.com/oauth2/authresp` . Ersätt `your-tenant-name` med namnet på din klient och `your-domain-name` med din anpassade domän.
    1. Välj **Nästa** och välj sedan **färdig**.
    1. När popup-fönstret stängs väljer du **Fortsätt** och väljer sedan **Spara**.

## <a name="creating-an-apple-client-secret"></a>Skapa en Apple-klient hemlighet

1. Från menyn Apple Developer Portal väljer du **nycklar** och sedan **(+)**.
1. **Registrera en ny nyckel**:
    1. Ange ett **nyckel namn**.
    1. Välj **Logga in med Apple** och välj sedan **Konfigurera**.
    1. För det **primära app-ID: t** väljer du den app som du skapade tidigare och väljer **Spara**.
    1. Välj **Konfigurera**, och välj sedan **Registrera** för att slutföra nyckel registrerings processen.
1. För att **Hämta din nyckel** väljer du **Ladda ned** för att ladda ned en. P8-fil som innehåller din nyckel.


::: zone pivot="b2c-user-flow"

## <a name="configure-apple-as-an-identity-provider"></a>Konfigurera Apple som identitets leverantör

1. Logga in på [Azure Portal](https://portal.azure.com/) som global administratör för din Azure AD B2C-klient.
1. Välj **katalog + prenumerations** filter på den översta menyn och välj den katalog som innehåller Azure AD B2C klienten.
1. Under **Azure-tjänster** väljer du **Azure AD B2C**. Eller Använd sökrutan för att söka efter och välja **Azure AD B2C**.
1. Välj **identitets leverantörer** och välj sedan **Apple (för hands version)**.
1. Ange ett **namn**. Till exempel *Apple*.
1. Ange **Apple Developer-ID: t (Team-ID)**.
1. Ange **Apple-tjänst-ID (klient-ID)**.
1. Ange **Apple-nyckel-ID**.
1. Välj och ladda upp **Apple-certifikatets data**.
1. Välj **Spara**.


> [!IMPORTANT] 
> - Logga in med Apple kräver att administratören förnyar sin klient hemlighet var 6: e månad. 
> - Under den offentliga för hands versionen av den här funktionen måste du förnya Apples klient hemlighet manuellt om den upphör att gälla. En varning visas i förväg på sidan Konfigurera sociala IDP, men vi rekommenderar att du anger din egen påminnelse. 
> - Om du behöver förnya hemligheten öppnar du Azure AD B2C i Azure Portal, går till **Identity providers**  >  **Apple** och väljer **förnya hemlighet**.

## <a name="add-the-apple-identity-provider-to-a-user-flow"></a>Lägg till Apple Identity-providern i ett användar flöde

Om du vill att användarna ska kunna logga in med ett Apple-ID måste du lägga till Apple Identity Provider i ett användar flöde. Logga in med Apple kan bara konfigureras för den **rekommenderade** versionen av användar flöden. Så här lägger du till Apple Identity-providern i ett användar flöde:

1. Välj **användar flöden** i Azure AD B2C klient.
1. Välj ett användar flöde som du vill lägga till Apple Identity-providern för. 
1. Under **sociala identitets leverantörer** väljer du **Apple (för hands version)**.
1. Välj **Spara**.
1. Om du vill testa principen väljer du **Kör användar flöde**.
1. För **program** väljer du det webb program som heter *testapp1* som du tidigare har registrerat. **Svars-URL: en** ska visas `https://jwt.ms` .
1. Välj knappen **Kör användar flöde** .
1. På sidan registrering eller inloggning väljer du **Apple** för att logga in med Apple-ID.

Om inloggnings processen lyckas omdirigeras webbläsaren till `https://jwt.ms` , som visar innehållet i den token som returnerades av Azure AD B2C.

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="signing-the-client-secret"></a>Signera klient hemligheten

Använd den. P8-fil som du laddade ned tidigare för att signera klient hemligheten i en JWT-token. Det finns många bibliotek som kan skapa och signera JWT åt dig. Använd [Azure-funktionen som skapar en token](https://github.com/azure-ad-b2c/samples/tree/master/policies/sign-in-with-apple/azure-function) åt dig. 

1. Skapa en [Azure-funktion](../azure-functions/functions-create-function-app-portal.md).
1. Välj **kod + test** under **utvecklare**. 
1. Kopiera innehållet i filen [Run. CSX](https://github.com/azure-ad-b2c/samples/blob/master/policies/sign-in-with-apple/azure-function/run.csx) och klistra in det i redigeraren.
1. Välj **Spara**.
1. Gör en HTTP- `POST` begäran och ange följande information:

    - **appleTeamId**: ditt Apple Developer-Team-ID
    - **appleServiceId**: Apple-tjänst-ID (även klient-ID)
    - **p8key**: PEM format nyckel. Du kan hämta detta genom att öppna. P8-filen i en text redigerare och kopiera allt mellan `-----BEGIN PRIVATE KEY-----` och `-----END PRIVATE KEY-----` utan rad brytningar.
 
Följande JSON är ett exempel på ett anrop till Azure-funktionen:

```json
{
    "appleTeamId": "ABC123DEFG",
    "appleKeyId": "URKEYID001",
    "appleServiceId": "com.yourcompany.app1",
    "p8key": "MIGTAgEAMBMGByqGSM49AgEGCCqGSM49AwEHBHkwdwIBAQQg+s07NiAcuGEu8rxsJBG7ttupF6FRe3bXdHxEipuyK82gCgYIKoZIzj0DAQehRANCAAQnR1W/KbbaihTQayXH3tuAXA8Aei7u7Ij5OdRy6clOgBeRBPy1miObKYVx3ki1msjjG2uGqRbrc1LvjLHINWRD"
}
```

Azure-funktionen svarar med en korrekt formaterad och signerad klients hemliga JWT i ett svar, till exempel:

```json
{
    "token": "eyJhbGciOiJFUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiJjb20ueW91cmNvbXBhbnkuYXBwMSIsIm5iZiI6MTU2MDI2OTY3NSwiZXhwIjoxNTYwMzU2MDc1LCJpc3MiOiJBQkMxMjNERUZHIiwiYXVkIjoiaHR0cHM6Ly9hcHBsZWlkLmFwcGxlLmNvbSJ9.Dt9qA9NmJ_mk6tOqbsuTmfBrQLFqc9BnSVKR6A-bf9TcTft2XmhWaVODr7Q9w1PP3QOYShFXAnNql5OdNebB4g"
}
```

## <a name="create-a-policy-key"></a>Skapa en princip nyckel

Du måste lagra klient hemligheten som du tidigare registrerade i Azure AD B2C-klienten.

1. Logga in på [Azure-portalen](https://portal.azure.com/).
1. Välj **katalog + prenumerations** filter på den översta menyn och välj den katalog som innehåller Azure AD B2C klienten.
2. Under **Azure-tjänster** väljer du **Azure AD B2C**. Eller Använd sökrutan för att söka efter och välja **Azure AD B2C**.
1. På sidan **Översikt** väljer du **ID för identitets miljö**.
1. Välj **princip nycklar** och välj sedan **Lägg till**.
1. För **alternativ** väljer du **manuell**.
1. Ange ett **namn** för princip nyckeln. Till exempel "AppleSecret". Prefixet "B2C_1A_" läggs till automatiskt till namnet på din nyckel.
1. I **hemlighet** anger du värdet för en token som returneras av Azure-funktionen (en JWT-token).
1. För **nyckel användning** väljer du **signatur**.
1. Välj **Skapa**.

> [!IMPORTANT] 
> - Logga in med Apple kräver att administratören förnyar sin klient hemlighet var 6: e månad.
> - Du måste förnya Apples klient hemlighet manuellt om den upphör att gälla och lagra det nya värdet i princip nyckeln.
> - Vi rekommenderar att du anger din egen påminnelse inom 6 månader för att skapa en ny klient hemlighet. 

## <a name="configure-apple-as-an-identity-provider"></a>Konfigurera Apple som identitets leverantör

För att användarna ska kunna logga in med ett Apple-ID måste du definiera kontot som en anspråks leverantör som Azure AD B2C kan kommunicera med via en slut punkt. Slut punkten innehåller en uppsättning anspråk som används av Azure AD B2C för att verifiera att en speciell användare är autentiserad.

Du kan definiera ett Apple-ID som en anspråks leverantör genom att lägga till det i **ClaimsProviders** -elementet i principens tilläggs fil.

1. Öppna *TrustFrameworkExtensions.xml*.
2. Hitta **ClaimsProviders** -elementet. Om den inte finns lägger du till den under rot elementet.
3. Lägg till en ny **ClaimsProvider** enligt följande:

    ```xml
    <ClaimsProvider>
      <Domain>apple.com</Domain>
      <DisplayName>Apple</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="Apple-OIDC">
          <DisplayName>Apple</DisplayName>
          <Protocol Name="OpenIdConnect" />
          <Metadata>
            <Item Key="ProviderName">apple</Item>
            <Item Key="authorization_endpoint">https://appleid.apple.com/auth/authorize</Item>
            <Item Key="AccessTokenEndpoint">https://appleid.apple.com/auth/token</Item>
            <Item Key="JWKS">https://appleid.apple.com/auth/keys</Item>
            <Item Key="issuer">https://appleid.apple.com</Item>
            <Item Key="scope">name email openid</Item>
            <Item Key="HttpBinding">POST</Item>
            <Item Key="response_types">code</Item>
            <Item Key="external_user_identity_claim_id">sub</Item>
            <Item Key="response_mode">form_post</Item>
            <Item Key="ReadBodyClaimsOnIdpRedirect">user.firstName user.lastName user.email</Item>
            <Item Key="client_id">You Apple ID</Item>
            <Item Key="UsePolicyInRedirectUri">false</Item>
          </Metadata>
          <CryptographicKeys>
            <Key Id="client_secret" StorageReferenceId="B2C_1A_AppleSecret"/>
          </CryptographicKeys>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="sub" />
            <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="https://appleid.apple.com" AlwaysUseDefaultValue="true" />
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" AlwaysUseDefaultValue="true" />
            <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="user.name.firstName"/>
            <OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="user.name.lastName"/>
            <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="user.email"/>
          </OutputClaims>
          <OutputClaimsTransformations>
            <OutputClaimsTransformation ReferenceId="CreateRandomUPNUserName"/>
            <OutputClaimsTransformation ReferenceId="CreateUserPrincipalName"/>
            <OutputClaimsTransformation ReferenceId="CreateAlternativeSecurityId"/>
            <OutputClaimsTransformation ReferenceId="CreateSubjectClaimFromAlternativeSecurityId"/>
          </OutputClaimsTransformations>
          <UseTechnicalProfileForSessionManagement ReferenceId="SM-SocialLogin" />
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

4. Ange **client_id** till tjänst identifieraren. Till exempel `com.consoto.azure-ad-b2c-service`.
5. Spara filen.

[!INCLUDE [active-directory-b2c-add-identity-provider-to-user-journey](../../includes/active-directory-b2c-add-identity-provider-to-user-journey.md)]


```xml
<OrchestrationStep Order="1" Type="CombinedSignInAndSignUp" ContentDefinitionReferenceId="api.signuporsignin">
  <ClaimsProviderSelections>
    ...
    <ClaimsProviderSelection TargetClaimsExchangeId="AppleExchange" />
  </ClaimsProviderSelections>
  ...
</OrchestrationStep>

<OrchestrationStep Order="2" Type="ClaimsExchange">
  ...
  <ClaimsExchanges>
    <ClaimsExchange Id="AppleExchange" TechnicalProfileReferenceId="Apple-OIDC" />
  </ClaimsExchanges>
</OrchestrationStep>
```

[!INCLUDE [active-directory-b2c-configure-relying-party-policy](../../includes/active-directory-b2c-configure-relying-party-policy-user-journey.md)]

## <a name="test-your-custom-policy"></a>Testa din anpassade princip

1. Välj en princip för förlitande part, till exempel `B2C_1A_signup_signin` .
1. För **program** väljer du ett webb program som du [har registrerat tidigare](troubleshoot-custom-policies.md#troubleshoot-the-runtime). **Svars-URL: en** ska visas `https://jwt.ms` .
1. Välj knappen **Kör nu** .
1. På sidan registrering eller inloggning väljer du **Apple** för att logga in med Apple-ID.

Om inloggnings processen lyckas omdirigeras webbläsaren till `https://jwt.ms` , som visar innehållet i den token som returnerades av Azure AD B2C.

::: zone-end
