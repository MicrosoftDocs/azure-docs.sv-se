---
title: Konfigurera inloggning med en Salesforce-SAML-provider genom att använda anpassade principer
titleSuffix: Azure AD B2C
description: Konfigurera inloggning med en Salesforce-SAML-provider genom att använda anpassade principer i Azure Active Directory B2C.
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
ms.openlocfilehash: 8214cbd7bb517f0bdcfde14e93790238bd1e354b
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/16/2020
ms.locfileid: "97584722"
---
# <a name="set-up-sign-in-with-a-salesforce-saml-provider-by-using-custom-policies-in-azure-active-directory-b2c"></a>Konfigurera inloggning med en Salesforce-SAML-provider genom att använda anpassade principer i Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-user-flow"
[!INCLUDE [active-directory-b2c-limited-to-custom-policy](../../includes/active-directory-b2c-limited-to-custom-policy.md)]

::: zone-end

::: zone pivot="b2c-custom-policy"

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Den här artikeln visar hur du aktiverar inloggning för användare från en Salesforce-organisation med [anpassade principer](custom-policy-overview.md) i Azure Active Directory B2C (Azure AD B2C). Du aktiverar inloggning genom att lägga till en [teknisk profil för SAML Identity Provider](saml-identity-provider-technical-profile.md) i en anpassad princip.

## <a name="prerequisites"></a>Krav

- Slutför stegen i [Kom igång med anpassade principer i Azure Active Directory B2C](custom-policy-get-started.md).
- Registrera dig för ett [kostnads fritt Developer Edition-konto](https://developer.salesforce.com/signup)om du inte redan gjort det. I den här artikeln används [Salesforce-upplevelsen](https://developer.salesforce.com/page/Lightning_Experience_FAQ).
- [Konfigurera en min domän](https://help.salesforce.com/articleView?id=domain_name_setup.htm&language=en_US&type=0) för din Salesforce-organisation.

### <a name="set-up-salesforce-as-an-identity-provider"></a>Konfigurera Salesforce som identitets leverantör

1. [Logga in på Salesforce](https://login.salesforce.com/).
2. På den vänstra menyn, under **Inställningar**, expandera **identitet** och välj sedan **identitetsprovider**.
3. Välj **Aktivera identitets leverantör**.
4. Under **Välj certifikat** väljer du det certifikat som du vill att Salesforce ska använda för att kommunicera med Azure AD B2C. Du kan använda standard certifikatet.
5. Klicka på **Spara**.

### <a name="create-a-connected-app-in-salesforce"></a>Skapa en ansluten app i Salesforce

1. På sidan **identitetsprovider** väljer du **tjänst leverantörer som nu skapas via anslutna appar. Klicka här.**
2. Under **grundläggande information** anger du de värden som krävs för den anslutna appen.
3. Markera kryss rutan **Aktivera SAML** under **Inställningar för webbapp**.
4. I fältet **entitets-ID** anger du följande URL. Se till att du ersätter värdet för `your-tenant` med namnet på din Azure AD B2C-klient.

      ```
      https://your-tenant.b2clogin.com/your-tenant.onmicrosoft.com/B2C_1A_TrustFrameworkBase
      ```

6. I fältet **ACS URL** anger du följande URL. Se till att du ersätter värdet för `your-tenant` med namnet på din Azure AD B2C-klient.

      ```
      https://your-tenant.b2clogin.com/your-tenant.onmicrosoft.com/B2C_1A_TrustFrameworkBase/samlp/sso/assertionconsumer
      ```
7. Rulla längst ned i listan och klicka sedan på **Spara**.

### <a name="get-the-metadata-url"></a>Hämta metadata-URL

1. På översikts sidan för din anslutna app klickar du på **Hantera**.
2. Kopiera värdet för **slut punkten för identifiering av metadata** och spara det sedan. Du kommer att använda den senare i den här artikeln.

### <a name="set-up-salesforce-users-to-federate"></a>Konfigurera Salesforce-användare att federera

1. På sidan **Hantera** i din anslutna app klickar du på **Hantera profiler**.
2. Välj de profiler (eller grupper av användare) som du vill federera med Azure AD B2C. Som system administratör markerar du kryss rutan **system administratör** så att du kan federera med ditt Salesforce-konto.

## <a name="generate-a-signing-certificate"></a>Generera ett signerings certifikat

Begär Anden som skickats till Salesforce måste signeras av Azure AD B2C. Om du vill generera ett signerings certifikat öppnar du Azure PowerShell och kör sedan följande kommandon.

> [!NOTE]
> Se till att du uppdaterar klientens namn och lösen ord på de två översta raderna.

```powershell
$tenantName = "<YOUR TENANT NAME>.onmicrosoft.com"
$pwdText = "<YOUR PASSWORD HERE>"

$Cert = New-SelfSignedCertificate -CertStoreLocation Cert:\CurrentUser\My -DnsName "SamlIdp.$tenantName" -Subject "B2C SAML Signing Cert" -HashAlgorithm SHA256 -KeySpec Signature -KeyLength 2048

$pwd = ConvertTo-SecureString -String $pwdText -Force -AsPlainText

Export-PfxCertificate -Cert $Cert -FilePath .\B2CSigningCert.pfx -Password $pwd
```

## <a name="create-a-policy-key"></a>Skapa en princip nyckel

Du måste lagra det certifikat som du skapade i Azure AD B2C-klienten.

1. Logga in på [Azure-portalen](https://portal.azure.com/).
2. Kontrol lera att du använder den katalog som innehåller din Azure AD B2C klient genom att välja filtret **katalog + prenumeration** på den översta menyn och välja den katalog som innehåller din klient.
3. Välj **Alla tjänster** på menyn högst upp till vänster i Azure-portalen och sök efter och välj **Azure AD B2C**.
4. På sidan Översikt väljer du **ID för identitets miljö**.
5. Välj **princip nycklar** och välj sedan **Lägg till**.
6. För **alternativ** väljer du `Upload` .
7. Ange ett **Namn** för principen. Till exempel SAMLSigningCert. Prefixet `B2C_1A_` läggs automatiskt till i namnet på din nyckel.
8. Bläddra till och välj det B2CSigningCert. PFX-certifikat som du skapade.
9. Ange **lösen ordet** för certifikatet.
3. Klicka på **Skapa**.

## <a name="add-a-claims-provider"></a>Lägg till en anspråks leverantör

Om du vill att användarna ska logga in med ett Salesforce-konto måste du definiera kontot som en anspråks leverantör som Azure AD B2C kan kommunicera med via en slut punkt. Slut punkten innehåller en uppsättning anspråk som används av Azure AD B2C för att verifiera att en speciell användare har autentiserats.

Du kan definiera ett Salesforce-konto som en anspråks leverantör genom att lägga till det i **ClaimsProviders** -elementet i principens tilläggs fil. Mer information finns i [definiera en teknisk profil för SAML Identity Provider](saml-identity-provider-technical-profile.md).

1. Öppna *TrustFrameworkExtensions.xml*.
1. Hitta **ClaimsProviders** -elementet. Om den inte finns lägger du till den under rot elementet.
1. Lägg till en ny **ClaimsProvider** enligt följande:

    ```xml
    <ClaimsProvider>
      <Domain>salesforce</Domain>
      <DisplayName>Salesforce</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="salesforce">
          <DisplayName>Salesforce</DisplayName>
          <Description>Login with your Salesforce account</Description>
          <Protocol Name="SAML2"/>
          <Metadata>
            <Item Key="WantsEncryptedAssertions">false</Item>
            <Item Key="WantsSignedAssertions">false</Item>
            <Item Key="PartnerEntity">https://contoso-dev-ed.my.salesforce.com/.well-known/samlidp.xml</Item>
          </Metadata>
          <CryptographicKeys>
            <Key Id="SamlMessageSigning" StorageReferenceId="B2C_1A_SAMLSigningCert"/>
          </CryptographicKeys>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="userId"/>
            <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="given_name"/>
            <OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="family_name"/>
            <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="email"/>
            <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="username"/>
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication"/>
            <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="SAMLIdp" />
          </OutputClaims>
          <OutputClaimsTransformations>
            <OutputClaimsTransformation ReferenceId="CreateRandomUPNUserName"/>
            <OutputClaimsTransformation ReferenceId="CreateUserPrincipalName"/>
            <OutputClaimsTransformation ReferenceId="CreateAlternativeSecurityId"/>
            <OutputClaimsTransformation ReferenceId="CreateSubjectClaimFromAlternativeSecurityId"/>
          </OutputClaimsTransformations>
          <UseTechnicalProfileForSessionManagement ReferenceId="SM-Saml-idp"/>
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

1. Uppdatera värdet för **PartnerEntity** med URL: en för Salesforce-metadata som du kopierade tidigare.
1. Uppdatera värdet för båda instanserna av **StorageReferenceId** till namnet på nyckeln för ditt signerings certifikat. Till exempel B2C_1A_SAMLSigningCert.
1. Leta upp `<ClaimsProviders>` avsnittet och Lägg till följande XML-kodfragment. Om principen redan innehåller den `SM-Saml-idp` tekniska profilen går du vidare till nästa steg. Mer information finns i [hantering av enkel inloggnings session](custom-policy-reference-sso.md).

    ```xml
    <ClaimsProvider>
      <DisplayName>Session Management</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="SM-Saml-idp">
          <DisplayName>Session Management Provider</DisplayName>
          <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.SamlSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
          <Metadata>
            <Item Key="IncludeSessionIndex">false</Item>
            <Item Key="RegisterServiceProviders">false</Item>
          </Metadata>
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```
1. Spara filen.

### <a name="upload-the-extension-file-for-verification"></a>Ladda upp tilläggs filen för verifiering

Nu har du konfigurerat principen så att Azure AD B2C vet hur de kan kommunicera med ditt Salesforce-konto. Försök att ladda upp tilläggs filen för principen för att bekräfta att den inte har några problem hittills.

1. På sidan **anpassade principer** i Azure AD B2C klienten väljer du **Ladda upp princip**.
2. Aktivera **Skriv över principen om den finns** och bläddra sedan till och välj *TrustFrameworkExtensions.xml* -filen.
3. Klicka på **Överför**.

## <a name="register-the-claims-provider"></a>Registrera anspråks leverantören

Nu har identitets leverantören kon figurer ATS, men den är inte tillgänglig på någon av registrerings-eller inloggnings skärmarna. För att göra det tillgängligt, skapar du en dubblett av en befintlig användar resa och ändrar den så att den även har Salesforce-identitetsprovider.

1. Öppna *TrustFrameworkBase.xml* -filen från start paketet.
2. Sök efter och kopiera hela innehållet i **UserJourney** -elementet som innehåller `Id="SignUpOrSignIn"` .
3. Öppna *TrustFrameworkExtensions.xml* och hitta **UserJourneys** -elementet. Om elementet inte finns lägger du till ett.
4. Klistra in hela innehållet i **UserJourney** -elementet som du kopierade som ett underordnat objekt till **UserJourneys** -elementet.
5. Byt namn på användar resans ID. Ett exempel är `SignUpSignInSalesforce`.

### <a name="display-the-button"></a>Visa knappen

**ClaimsProviderSelection** -elementet är detsamma som en identitetsprovider på en registrerings-eller inloggnings skärm. Om du lägger till ett **ClaimsProviderSelection** -element för ett LinkedIn-konto visas en ny knapp när en användare hamnar på sidan.

1. Hitta **OrchestrationStep** -elementet som innehåller `Order="1"` i användar resan som du nyss skapade.
2. Lägg till följande-element under **ClaimsProviderSelects**. Ange värdet för **TargetClaimsExchangeId** till ett lämpligt värde, till exempel `SalesforceExchange` :

    ```xml
    <ClaimsProviderSelection TargetClaimsExchangeId="SalesforceExchange" />
    ```

### <a name="link-the-button-to-an-action"></a>Länka knappen till en åtgärd

Nu när du har en knapp på plats måste du länka den till en åtgärd. Åtgärden, i det här fallet, är att Azure AD B2C kommunicera med ett Salesforce-konto för att ta emot en token.

1. Hitta **OrchestrationStep** som ingår `Order="2"` i användar resan.
2. Lägg till följande **ClaimsExchange** -element och kontrol lera att du använder samma värde för **ID** som du använde för **TargetClaimsExchangeId**:

    ```xml
    <ClaimsExchange Id="SalesforceExchange" TechnicalProfileReferenceId="salesforce" />
    ```

    Uppdatera värdet för **TechnicalProfileReferenceId** till **ID: t** för den tekniska profil som du skapade tidigare. Exempel: `salesforce` eller `LinkedIn-OAUTH`.

3. Spara *TrustFrameworkExtensions.xml* -filen och ladda upp den igen för verifiering.

## <a name="update-and-test-the-relying-party-file"></a>Uppdatera och testa den förlitande part filen

Uppdatera den förlitande part filen (RP) som initierar användar resan som du nyss skapade:

1. Gör en kopia av *SignUpOrSignIn.xml* i din arbets katalog och Byt namn på den. Du kan till exempel byta namn på den till *SignUpSignInSalesforce.xml*.
2. Öppna den nya filen och uppdatera värdet för attributet **PolicyId** för **TrustFrameworkPolicy** med ett unikt värde. Ett exempel är `SignUpSignInSalesforce`.
3. Uppdatera värdet för **PublicPolicyUri** med URI: n för principen. Till exempel`http://contoso.com/B2C_1A_signup_signin_salesforce`
4. Uppdatera värdet för attributet **ReferenceId** i **DefaultUserJourney** för att matcha ID för den nya användar resan som du skapade (SignUpSignInSalesforce).
5. Spara ändringarna, ladda upp filen och välj sedan den nya principen i listan.
6. Kontrol lera att Azure AD B2C programmet som du har skapat är markerat i fältet **Välj program** och testa det genom att klicka på **Kör nu**.

::: zone-end