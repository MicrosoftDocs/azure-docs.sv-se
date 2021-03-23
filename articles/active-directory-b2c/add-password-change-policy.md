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
ms.date: 03/22/2021
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 21da8f79772d9648836bedec89cb5d7014486dc6
ms.sourcegitcommit: ba3a4d58a17021a922f763095ddc3cf768b11336
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/23/2021
ms.locfileid: "104798367"
---
# <a name="configure-password-change-using-custom-policies-in-azure-active-directory-b2c"></a>Konfigurera ändring av lösen ord med anpassade principer i Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

I Azure Active Directory B2C (Azure AD B2C) kan du aktivera användare som är inloggade med ett lokalt konto för att ändra sina lösen ord utan att behöva bevisa sin identitet via e-postverifiering. Flödet för lösen ords ändring omfattar följande steg:

1. Användaren loggar in på sitt lokala konto. Om sessionen fortfarande är aktiv, Azure AD B2C auktorisera användaren och gå vidare till nästa steg.
1. Användaren verifierar det **gamla lösen ordet** och skapar och bekräftar sedan det **nya lösen ordet**.

![Flöde för lösen ords ändring](./media/add-password-change-policy/password-change-flow.png)  

> [!TIP]
> Med lösen ords ändrings flödet kan användarna bara ändra sitt lösen ord när användaren känner till sitt lösen ord och vill ändra det. Vi rekommenderar att du även aktiverar [lösen ords återställning via självbetjäning](add-password-reset-policy.md) till stöd för de fall där användaren glömmer sitt lösen ord.

::: zone pivot="b2c-user-flow"

[!INCLUDE [active-directory-b2c-limited-to-custom-policy](../../includes/active-directory-b2c-limited-to-custom-policy.md)]

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="prerequisites"></a>Förutsättningar

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
            <InputClaims>
              <InputClaim ClaimTypeReferenceId="oldPassword" PartnerClaimType="password" Required="true" />
              </InputClaims>
            <IncludeTechnicalProfile ReferenceId="login-NonInteractive" />
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

3. [UserJourney](userjourneys.md) -elementet definierar den sökväg som användaren ska vidta när de interagerar med ditt program. Lägg till **UserJourneys** -elementet om det inte finns med **UserJourney** som identifieras som `PasswordChange` :

    ```xml
    <UserJourneys>
      <UserJourney Id="PasswordChange">
        <OrchestrationSteps>
          <OrchestrationStep Order="1" Type="ClaimsProviderSelection" ContentDefinitionReferenceId="api.signuporsignin">
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
          <OrchestrationStep Order="4" Type="ClaimsExchange">
            <ClaimsExchanges>
              <ClaimsExchange Id="AADUserReadWithObjectId" TechnicalProfileReferenceId="AAD-UserReadUsingObjectId" />
            </ClaimsExchanges>
          </OrchestrationStep>
          <OrchestrationStep Order="5" Type="SendClaims" CpimIssuerTechnicalProfileReferenceId="JwtIssuer" />
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

## <a name="upload-and-test-the-policy"></a>Ladda upp och testa principen

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

- Hitta exempel policyn på [GitHub](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/password-change).
- Lär dig mer om hur du kan [Konfigurera lösen ords komplexitet i Azure AD B2C](password-complexity.md).
- Konfigurera ett [flöde för återställning av lösen ord](add-password-reset-policy.md).

::: zone-end
