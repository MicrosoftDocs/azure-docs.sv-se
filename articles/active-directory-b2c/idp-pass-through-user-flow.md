---
title: Skicka en Identity Provider-åtkomsttoken till din app
titleSuffix: Azure AD B2C
description: Lär dig hur du skickar en åtkomsttoken för OAuth 2,0-identitets leverantörer som ett anspråk i ett användar flöde i Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 12/15/2020
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: a99d41f5f9fc9538aaf563bd3ae56075d269c94a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "97584654"
---
# <a name="pass-an-identity-provider-access-token-to-your-application-in-azure-active-directory-b2c"></a>Skicka en Identity Provider-åtkomsttoken till ditt program i Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

Ett [användar flöde](user-flow-overview.md) i Azure Active Directory B2C (Azure AD B2C) ger användare av programmet en möjlighet att registrera sig eller logga in med en identitets leverantör. När resan startar får Azure AD B2C en [åtkomsttoken från identitets](tokens-overview.md) leverantören. Azure AD B2C använder denna token för att hämta information om användaren. Du aktiverar ett anspråk i ditt användar flöde för att skicka token till de program som du registrerar i Azure AD B2C.

::: zone pivot="b2c-user-flow"

Azure AD B2C stöder överföring av åtkomsttoken för [OAuth 2,0](add-identity-provider.md) -identitets leverantörer, som innehåller [Facebook](identity-provider-facebook.md) och [Google](identity-provider-google.md). För alla andra identitets leverantörer returneras anspråket tomt.

::: zone-end

::: zone pivot="b2c-custom-policy"

Azure AD B2C stöder överföring av åtkomsttoken för [OAuth 2,0](authorization-code-flow.md) och [OpenID Connect](openid-connect.md) Identity providers. För alla andra identitets leverantörer returneras anspråket tomt.

::: zone-end

Följande diagram visar hur en Identity Provider-token återgår till din app: 

![Strömnings flöde för identitetsprovider](./media/idp-pass-through-user-flow/identity-provider-pass-through-flow.png)

## <a name="prerequisites"></a>Förutsättningar

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

::: zone pivot="b2c-user-flow"

## <a name="enable-the-claim"></a>Aktivera anspråket

1. Logga in på [Azure Portal](https://portal.azure.com/) som global administratör för Azure AD B2C-klientorganisationen.
2. Kontrol lera att du använder den katalog som innehåller din Azure AD B2C-klient. Välj **katalog + prenumerations** filter på den översta menyn och välj den katalog som innehåller din klient.
3. Välj **Alla tjänster** på menyn högst upp till vänster i Azure-portalen och sök efter och välj **Azure AD B2C**.
4. Välj **användar flöden (principer)** och välj sedan ditt användar flöde. Till exempel **B2C_1_signupsignin1**.
5. Välj **Programanspråk**.
6. Aktivera **åtkomst till token för identitets leverantör** .

    ![Aktivera åtkomst till token för identitets leverantör](./media/idp-pass-through-user-flow/identity-provider-pass-through-app-claim.png)

7. Spara användar flödet genom att klicka på **Spara** .

## <a name="test-the-user-flow"></a>Testa användar flödet

När du testar dina program i Azure AD B2C kan det vara praktiskt att använda den Azure AD B2C token som returneras för `https://jwt.ms` att granska anspråk i det.

1. På sidan Översikt i användar flödet väljer du **Kör användar flöde**.
2. För **program** väljer du ditt program som du har registrerat tidigare. Om du vill se token i exemplet nedan ska **svars-URL:** en visas `https://jwt.ms` .
3. Klicka på **Kör användar flöde** och logga in med dina autentiseringsuppgifter för kontot. Du bör se åtkomsttoken för identitets leverantören i **idp_access_token** -anspråket.

    Du bör se något som liknar följande exempel:

    ![Avkodad token i jwt.ms med idp_access_token block markerat](./media/idp-pass-through-user-flow/identity-provider-pass-through-token.png)

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="add-the-claim-elements"></a>Lägg till anspråks elementen

1. Öppna *TrustframeworkExtensions.xml* -filen och Lägg till följande **claimType** -element med en identifierare för `identityProviderAccessToken` **ClaimsSchema** -elementet:

    ```xml
    <BuildingBlocks>
      <ClaimsSchema>
        <ClaimType Id="identityProviderAccessToken">
          <DisplayName>Identity Provider Access Token</DisplayName>
          <DataType>string</DataType>
          <AdminHelpText>Stores the access token of the identity provider.</AdminHelpText>
        </ClaimType>
        ...
      </ClaimsSchema>
    </BuildingBlocks>
    ```

2. Lägg till elementet **OutputClaim** i **TechnicalProfile** -elementet för varje OAuth 2,0-identitetsprovider som du vill ha åtkomsttoken för. I följande exempel visas det element som har lagts till i den nya Facebook-tekniska profilen:

    ```xml
    <ClaimsProvider>
      <DisplayName>Facebook</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="Facebook-OAUTH">
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="identityProviderAccessToken" PartnerClaimType="{oauth2:access_token}" />
          </OutputClaims>
          ...
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

3. Spara *TrustframeworkExtensions.xml* -filen.
4. Öppna den förlitande part princip filen, till exempel *SignUpOrSignIn.xml*, och Lägg till **OutputClaim** -elementet till **TechnicalProfile**:

    ```xml
    <RelyingParty>
      <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
      <TechnicalProfile Id="PolicyProfile">
        <OutputClaims>
          <OutputClaim ClaimTypeReferenceId="identityProviderAccessToken" PartnerClaimType="idp_access_token"/>
        </OutputClaims>
        ...
      </TechnicalProfile>
    </RelyingParty>
    ```

5. Spara princip filen.

## <a name="test-your-policy"></a>Testa principen

När du testar dina program i Azure AD B2C kan det vara praktiskt att ha Azure AD B2C token som returneras för att `https://jwt.ms` kunna granska anspråk i det.

### <a name="upload-the-files"></a>Ladda upp filerna

1. Logga in på [Azure-portalen](https://portal.azure.com/).
2. Kontrol lera att du använder den katalog som innehåller din Azure AD B2C klient genom att klicka på filtret **katalog + prenumeration** på den översta menyn och välja den katalog som innehåller din klient.
3. Välj **Alla tjänster** på menyn högst upp till vänster i Azure-portalen och sök efter och välj **Azure AD B2C**.
4. Välj **ramverk för identitets upplevelse**.
5. På sidan anpassade principer klickar du på **Ladda upp princip**.
6. Välj **Skriv över principen om den finns** och Sök sedan efter och välj den *TrustframeworkExtensions.xml* filen.
7. Välj **Överför**.
8. Upprepa steg 5 till 7 för den förlitande part filen, till exempel *SignUpOrSignIn.xml*.

### <a name="run-the-policy"></a>Kör principen

1. Öppna den princip som du har ändrat. Till exempel *B2C_1A_signup_signin*.
2. För **program** väljer du ditt program som du har registrerat tidigare. Om du vill se token i exemplet nedan ska **svars-URL:** en visas `https://jwt.ms` .
3. Välj **Kör nu**.

    Du bör se något som liknar följande exempel:

    ![Avkodad token i jwt.ms med idp_access_token block markerat](./media/idp-pass-through-user-flow/identity-provider-pass-through-token-custom.png)

::: zone-end

## <a name="next-steps"></a>Nästa steg

Läs mer i [Översikt över Azure AD B2C tokens](tokens-overview.md).
