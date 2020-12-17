---
title: Konfigurera ändring av lösen ord med anpassade principer
titleSuffix: Azure AD B2C
description: Lär dig hur du gör det möjligt för användare att ändra sina lösen ord med hjälp av anpassade principer i Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 12/16/2020
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 4da0fccf10d387e7496a8b0ecc7623a22df58c93
ms.sourcegitcommit: 86acfdc2020e44d121d498f0b1013c4c3903d3f3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/17/2020
ms.locfileid: "97618814"
---
# <a name="configure-password-change-using-custom-policies-in-azure-active-directory-b2c"></a>Konfigurera ändring av lösen ord med anpassade principer i Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-user-flow"

[!INCLUDE [active-directory-b2c-limited-to-custom-policy](../../includes/active-directory-b2c-limited-to-custom-policy.md)]

::: zone-end

::: zone pivot="b2c-custom-policy"

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

I Azure Active Directory B2C (Azure AD B2C) kan du göra det möjligt för användare som är inloggade med ett lokalt konto att ändra sina lösen ord utan att behöva bevisa sin äkthet via e-postverifiering. Om sessionen upphör att gälla när användaren kommer till lösen ordet för lösen ords ändring uppmanas de att logga in igen. Den här artikeln visar hur du konfigurerar ändring av lösen ord i [anpassade principer](custom-policy-overview.md). Det är också möjligt att konfigurera [lösen ords återställning via självbetjäning](user-flow-self-service-password-reset.md) för användar flöden.

## <a name="prerequisites"></a>Krav

* Slutför stegen i [Kom igång med anpassade principer i Active Directory B2C](custom-policy-get-started.md).
* [Registrera ett webb program i Azure Active Directory B2C](tutorial-register-applications.md)om du inte redan gjort det.

## <a name="add-the-elements"></a>Lägg till elementen

1. Öppna *TrustframeworkExtensions.xml* -filen och Lägg till följande **claimType** -element med en identifierare för `oldPassword` [ClaimsSchema](claimsschema.md) -elementet:

    ```xml
    <BuildingBlocks>
      <ClaimsSchema>
        <ClaimType Id="oldPassword">
          <DisplayName>Old Password</DisplayName>
          <DataType>string</DataType>
          <UserHelpText>Enter your old password</UserHelpText>
          <UserInputType>Password</UserInputType>
        </ClaimType>
      </ClaimsSchema>
    </BuildingBlocks>
    ```

2. Ett [ClaimsProvider](claimsproviders.md) -element innehåller den tekniska profilen som autentiserar användaren. Lägg till följande anspråks leverantörer i **ClaimsProviders** -elementet:

    ```xml
    <ClaimsProviders>
      <ClaimsProvider>
        <DisplayName>Local Account SignIn</DisplayName>
        <TechnicalProfiles>
          <TechnicalProfile Id="login-NonInteractive-PasswordChange">
            <DisplayName>Local Account SignIn</DisplayName>
            <Protocol Name="OpenIdConnect" />
            <Metadata>
              <Item Key="UserMessageIfClaimsPrincipalDoesNotExist">We can't seem to find your account</Item>
              <Item Key="UserMessageIfInvalidPassword">Your password is incorrect</Item>
              <Item Key="UserMessageIfOldPasswordUsed">Looks like you used an old password</Item>
              <Item Key="ProviderName">https://sts.windows.net/</Item>
              <Item Key="METADATA">https://login.microsoftonline.com/{tenant}/.well-known/openid-configuration</Item>
              <Item Key="authorization_endpoint">https://login.microsoftonline.com/{tenant}/oauth2/token</Item>
              <Item Key="response_types">id_token</Item>
              <Item Key="response_mode">query</Item>
              <Item Key="scope">email openid</Item>
              <Item Key="grant_type">password</Item>
              <Item Key="UsePolicyInRedirectUri">false</Item>
              <Item Key="HttpBinding">POST</Item>
              <Item Key="client_id">ProxyIdentityExperienceFrameworkAppId</Item>
              <Item Key="IdTokenAudience">IdentityExperienceFrameworkAppId</Item>
            </Metadata>
            <InputClaims>
              <InputClaim ClaimTypeReferenceId="signInName" PartnerClaimType="username" Required="true" />
              <InputClaim ClaimTypeReferenceId="oldPassword" PartnerClaimType="password" Required="true" />
              <InputClaim ClaimTypeReferenceId="grant_type" DefaultValue="password" />
              <InputClaim ClaimTypeReferenceId="scope" DefaultValue="openid" />
              <InputClaim ClaimTypeReferenceId="nca" PartnerClaimType="nca" DefaultValue="1" />
              <InputClaim ClaimTypeReferenceId="client_id" DefaultValue="ProxyIdentityExperienceFrameworkAppID" />
              <InputClaim ClaimTypeReferenceId="resource_id" PartnerClaimType="resource" DefaultValue="IdentityExperienceFrameworkAppID" />
            </InputClaims>
            <OutputClaims>
              <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="oid" />
              <OutputClaim ClaimTypeReferenceId="tenantId" PartnerClaimType="tid" />
              <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="given_name" />
              <OutputClaim ClaimTypeReferenceId="surName" PartnerClaimType="family_name" />
              <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
              <OutputClaim ClaimTypeReferenceId="userPrincipalName" PartnerClaimType="upn" />
              <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="localAccountAuthentication" />
            </OutputClaims>
          </TechnicalProfile>
        </TechnicalProfiles>
      </ClaimsProvider>
      <ClaimsProvider>
        <DisplayName>Local Account Password Change</DisplayName>
        <TechnicalProfiles>
          <TechnicalProfile Id="LocalAccountWritePasswordChangeUsingObjectId">
            <DisplayName>Change password (username)</DisplayName>
            <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.SelfAssertedAttributeProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
            <Metadata>
              <Item Key="ContentDefinitionReferenceId">api.selfasserted</Item>
            </Metadata>
            <CryptographicKeys>
              <Key Id="issuer_secret" StorageReferenceId="B2C_1A_TokenSigningKeyContainer" />
            </CryptographicKeys>
            <InputClaims>
              <InputClaim ClaimTypeReferenceId="objectId" />
            </InputClaims>
            <OutputClaims>
              <OutputClaim ClaimTypeReferenceId="oldPassword" Required="true" />
              <OutputClaim ClaimTypeReferenceId="newPassword" Required="true" />
              <OutputClaim ClaimTypeReferenceId="reenterPassword" Required="true" />
            </OutputClaims>
            <ValidationTechnicalProfiles>
              <ValidationTechnicalProfile ReferenceId="login-NonInteractive-PasswordChange" />
              <ValidationTechnicalProfile ReferenceId="AAD-UserWritePasswordUsingObjectId" />
            </ValidationTechnicalProfiles>
          </TechnicalProfile>
        </TechnicalProfiles>
      </ClaimsProvider>
    </ClaimsProviders>
    ```

    Ersätt `IdentityExperienceFrameworkAppId` med program-ID: t för det IdentityExperienceFramework-program som du skapade i den nödvändiga självstudien. Ersätt `ProxyIdentityExperienceFrameworkAppId` med program-ID: t för det ProxyIdentityExperienceFramework-program som du redan har skapat.

3. [UserJourney](userjourneys.md) -elementet definierar den sökväg som användaren ska vidta när de interagerar med ditt program. Lägg till **UserJourneys** -elementet om det inte finns med **UserJourney** som identifieras som `PasswordChange` :

    ```xml
    <UserJourneys>
      <UserJourney Id="PasswordChange">
        <OrchestrationSteps>
          <OrchestrationStep Order="1" Type="ClaimsProviderSelection" ContentDefinitionReferenceId="api.idpselections">
            <ClaimsProviderSelections>
              <ClaimsProviderSelection TargetClaimsExchangeId="LocalAccountSigninEmailExchange" />
            </ClaimsProviderSelections>
          </OrchestrationStep>
          <OrchestrationStep Order="2" Type="ClaimsExchange">
            <ClaimsExchanges>
              <ClaimsExchange Id="LocalAccountSigninEmailExchange" TechnicalProfileReferenceId="SelfAsserted-LocalAccountSignin-Email" />
            </ClaimsExchanges>
          </OrchestrationStep>
          <OrchestrationStep Order="3" Type="ClaimsExchange">
            <ClaimsExchanges>
              <ClaimsExchange Id="NewCredentials" TechnicalProfileReferenceId="LocalAccountWritePasswordChangeUsingObjectId" />
            </ClaimsExchanges>
          </OrchestrationStep>
          <OrchestrationStep Order="4" Type="SendClaims" CpimIssuerTechnicalProfileReferenceId="JwtIssuer" />
        </OrchestrationSteps>
        <ClientDefinition ReferenceId="DefaultWeb" />
      </UserJourney>
    </UserJourneys>
    ```

4. Spara *TrustFrameworkExtensions.xml* princip filen.
5. Kopiera *ProfileEdit.xml* -filen som du laddade ned med start paketet och ge den namnet *ProfileEditPasswordChange.xml*.
6. Öppna den nya filen och uppdatera attributet **PolicyId** med ett unikt värde. Det här värdet är namnet på principen. Till exempel *B2C_1A_profile_edit_password_change*.
7. Ändra attributet **ReferenceId** i `<DefaultUserJourney>` så att det matchar ID: t för den nya användar resan som du skapade. Till exempel *PasswordChange*.
8. Spara ändringarna.

Du kan hitta exempel principen [här](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/password-change).

## <a name="test-your-policy"></a>Testa principen

När du testar dina program i Azure AD B2C kan det vara praktiskt att ha Azure AD B2C token som returneras för att `https://jwt.ms` kunna granska anspråk i det.

### <a name="upload-the-files"></a>Ladda upp filerna

1. Logga in på [Azure-portalen](https://portal.azure.com/).
2. Kontrol lera att du använder den katalog som innehåller din Azure AD B2C klient genom att välja filtret **katalog + prenumeration** på den översta menyn och välja den katalog som innehåller din klient.
3. Välj **Alla tjänster** på menyn högst upp till vänster i Azure-portalen och sök efter och välj **Azure AD B2C**.
4. Välj **ramverk för identitets upplevelse**.
5. På sidan anpassade principer klickar du på **Ladda upp princip**.
6. Välj **Skriv över principen om den finns** och Sök sedan efter och välj den *TrustframeworkExtensions.xml* filen.
7. Klicka på **Överför**.
8. Upprepa steg 5 till 7 för den förlitande part filen, till exempel *ProfileEditPasswordChange.xml*.

### <a name="run-the-policy"></a>Kör principen

1. Öppna den princip som du har ändrat. Till exempel *B2C_1A_profile_edit_password_change*.
2. För **program** väljer du ditt program som du har registrerat tidigare. Om du vill se token ska **svars-URL:** en visas `https://jwt.ms` .
3. Klicka på **Kör nu**. Logga in med det konto som du skapade tidigare. Du bör nu ha möjlighet att ändra lösen ordet.

## <a name="next-steps"></a>Nästa steg

- Lär dig mer om hur du kan [Konfigurera lösen ords komplexitet i Azure AD B2C](password-complexity.md).

::: zone-end