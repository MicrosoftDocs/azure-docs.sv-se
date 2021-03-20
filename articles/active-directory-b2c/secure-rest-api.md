---
title: Skydda en RESTful-tjänst i din Azure AD B2C
titleSuffix: Azure AD B2C
description: Skydda dina anpassade REST API Claims-utbyten i Azure AD B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 10/15/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: f6907db7f6e53247a8f2fc0042e8c8e6b081dbd3
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "97516368"
---
# <a name="secure-your-restful-services"></a>Skydda dina RESTful-tjänster 

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

När du integrerar en REST API inom en Azure AD B2C användar resa måste du skydda din REST API-slutpunkt med autentisering. Detta säkerställer att endast tjänster som har rätt autentiseringsuppgifter, till exempel Azure AD B2C, kan ringa till REST API-slutpunkten.

Lär dig hur du integrerar en REST API inom Azure AD B2C användar resa i artiklarna för att [Verifiera indata från användaren](custom-policy-rest-api-claims-validation.md) och [lägga till REST API anspråk utbyten till anpassade policys](custom-policy-rest-api-claims-exchange.md) -artiklar.

I den här artikeln får du lära dig hur du skyddar REST API med antingen HTTP Basic, klient certifikat eller OAuth2-autentisering. 

## <a name="prerequisites"></a>Förutsättningar

Slutför stegen i någon av följande rikt linjer:

- [Integrera REST API Claims-utbyten i Azure AD B2C användar resa för att verifiera användarindata](custom-policy-rest-api-claims-validation.md).
- [Lägg till REST API åberopade anspråk till anpassade principer](custom-policy-rest-api-claims-exchange.md)

## <a name="http-basic-authentication"></a>Grundläggande HTTP-autentisering

Grundläggande HTTP-autentisering definieras i [RFC 2617](https://tools.ietf.org/html/rfc2617). Grundläggande autentisering fungerar på följande sätt: Azure AD B2C skickar en HTTP-begäran med klientautentiseringsuppgifterna i Authorization-huvudet. Autentiseringsuppgifterna formateras som Base64-kodad sträng "namn: lösen ord".  

### <a name="add-rest-api-username-and-password-policy-keys"></a>Lägg till princip nycklar för REST API användar namn och lösen ord

Om du vill konfigurera en REST API teknisk profil med HTTP Basic-autentisering skapar du följande kryptografiska nycklar för att lagra användar namn och lösen ord:

1. Logga in på [Azure-portalen](https://portal.azure.com/).
1. Kontrol lera att du använder den katalog som innehåller din Azure AD B2C-klient. Välj **katalog + prenumerations** filter på den översta menyn och välj din Azure AD B2C katalog.
1. Välj **Alla tjänster** på menyn högst upp till vänster i Azure-portalen och sök efter och välj **Azure AD B2C**.
1. På sidan Översikt väljer du **ID för identitets miljö**.
1. Välj **princip nycklar** och välj sedan **Lägg till**.
1. För **alternativ** väljer du **manuell**.
1. I **namn** skriver du **RestApiUsername**.
    Prefixet *B2C_1A_* kan läggas till automatiskt.
1. I rutan **hemlighet** anger du REST API användar namn.
1. För **nyckel användning** väljer du **kryptering**.
1. Välj **Skapa**.
1. Välj **princip nycklar** igen.
1. Välj **Lägg till**.
1. För **alternativ** väljer du **manuell**.
1. I **namn** skriver du **RestApiPassword**.
    Prefixet *B2C_1A_* kan läggas till automatiskt.
1. I rutan **hemlighet** anger du REST API lösen ordet.
1. För **nyckel användning** väljer du **kryptering**.
1. Välj **Skapa**.

### <a name="configure-your-rest-api-technical-profile-to-use-http-basic-authentication"></a>Konfigurera din REST API tekniska profil för att använda HTTP Basic-autentisering

När du har skapat de nödvändiga nycklarna konfigurerar du REST API teknisk profils metadata så att de refererar till autentiseringsuppgifterna.

1. Öppna tilläggs princip filen (TrustFrameworkExtensions.xml) i arbets katalogen.
1. Sök efter den REST API tekniska profilen. Till exempel `REST-ValidateProfile` eller `REST-GetProfile` .
1. Leta upp `<Metadata>` elementet.
1. Ändra *AuthenticationType* till `Basic` .
1. Ändra *AllowInsecureAuthInProduction* till `false` .
1. `</Metadata>`Lägg till följande XML-kodfragment omedelbart efter det avslutande elementet:
    ```xml
    <CryptographicKeys>
        <Key Id="BasicAuthenticationUsername" StorageReferenceId="B2C_1A_RestApiUsername" />
        <Key Id="BasicAuthenticationPassword" StorageReferenceId="B2C_1A_RestApiPassword" />
    </CryptographicKeys>
    ```

Följande är ett exempel på en RESTful-teknisk profil som kon figurer ATS med HTTP Basic-autentisering:

```xml
<ClaimsProvider>
  <DisplayName>REST APIs</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="REST-GetProfile">
      <DisplayName>Get user extended profile Azure Function web hook</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <Item Key="ServiceUrl">https://your-account.azurewebsites.net/api/GetProfile?code=your-code</Item>
        <Item Key="SendClaimsIn">Body</Item>
        <Item Key="AuthenticationType">Basic</Item>
        <Item Key="AllowInsecureAuthInProduction">false</Item>
      </Metadata>
      <CryptographicKeys>
        <Key Id="BasicAuthenticationUsername" StorageReferenceId="B2C_1A_RestApiUsername" />
        <Key Id="BasicAuthenticationPassword" StorageReferenceId="B2C_1A_RestApiPassword" />
      </CryptographicKeys>
      ...
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="https-client-certificate-authentication"></a>Autentisering av HTTPS-klientcertifikat

Autentisering av klient certifikat är en ömsesidigt certifikatbaserad autentisering, där klienten Azure AD B2C, ger sitt klient certifikat till servern för att bevisa sin identitet. Detta inträffar som en del av SSL-handskakningen. Endast tjänster som har rätt certifikat, till exempel Azure AD B2C, kan komma åt din REST API-tjänst. Klient certifikatet är ett digitalt X. 509-certifikat. I produktions miljöer måste det signeras av en certifikat utfärdare.

### <a name="prepare-a-self-signed-certificate-optional"></a>Förbereda ett självsignerat certifikat (valfritt)

Om du inte redan har ett certifikat kan du använda ett självsignerat certifikat för icke-produktions miljöer. I Windows kan du använda PowerShell: s [New-SelfSignedCertificate-](/powershell/module/pkiclient/new-selfsignedcertificate) cmdlet för att skapa ett certifikat.

1. Kör PowerShell-kommandot för att generera ett självsignerat certifikat. Ändra `-Subject` argumentet efter behov för ditt program och Azure AD B2C klient namn. Du kan också justera `-NotAfter` datumet för att ange ett annat förfallo datum för certifikatet.
    ```powershell
    New-SelfSignedCertificate `
        -KeyExportPolicy Exportable `
        -Subject "CN=yourappname.yourtenant.onmicrosoft.com" `
        -KeyAlgorithm RSA `
        -KeyLength 2048 `
        -KeyUsage DigitalSignature `
        -NotAfter (Get-Date).AddMonths(12) `
        -CertStoreLocation "Cert:\CurrentUser\My"
    ```    
1. Öppna **hantera användar certifikat**  >  **aktuella användare**  >  **personliga**  >  **certifikat**  >  *yourappname.yourtenant.onmicrosoft.com*.
1. Välj åtgärden certifikat > **åtgärden**  >  **alla aktiviteter**  >  **Exportera**.
1. Välj **Ja**  >  **Nästa**  >  **Ja, exportera den privata nyckeln**  >  **Nästa**.
1. Acceptera standardinställningarna för **export fil format**.
1. Ange ett lösen ord för certifikatet.

### <a name="add-a-client-certificate-policy-key"></a>Lägga till en princip nyckel för klient certifikat

1. Logga in på [Azure-portalen](https://portal.azure.com/).
1. Kontrol lera att du använder den katalog som innehåller din Azure AD B2C-klient. Välj **katalog + prenumerations** filter på den översta menyn och välj din Azure AD B2C katalog.
1. Välj **Alla tjänster** på menyn högst upp till vänster i Azure-portalen och sök efter och välj **Azure AD B2C**.
1. På sidan Översikt väljer du **ID för identitets miljö**.
1. Välj **princip nycklar** och välj sedan **Lägg till**.
1. I rutan **alternativ** väljer du **överför**.
1. Skriv **RestApiClientCertificate** i rutan **namn** .
    Prefixet *B2C_1A_* läggs till automatiskt.
1. I rutan **fil uppladdning** väljer du certifikatets PFX-fil med en privat nyckel.
1. I rutan **lösen ord** anger du certifikatets lösen ord.
1. Välj **Skapa**.

### <a name="configure-your-rest-api-technical-profile-to-use-client-certificate-authentication"></a>Konfigurera din REST API tekniska profil för att använda autentisering med klient certifikat

När du har skapat den nödvändiga nyckeln konfigurerar du REST API teknisk profils metadata så att de refererar till klient certifikatet.

1. Öppna tilläggs princip filen (TrustFrameworkExtensions.xml) i arbets katalogen.
1. Sök efter den REST API tekniska profilen. Till exempel `REST-ValidateProfile` eller `REST-GetProfile` .
1. Leta upp `<Metadata>` elementet.
1. Ändra *AuthenticationType* till `ClientCertificate` .
1. Ändra *AllowInsecureAuthInProduction* till `false` .
1. `</Metadata>`Lägg till följande XML-kodfragment omedelbart efter det avslutande elementet:
    ```xml
    <CryptographicKeys>
       <Key Id="ClientCertificate" StorageReferenceId="B2C_1A_RestApiClientCertificate" />
    </CryptographicKeys>
    ```

Följande är ett exempel på en RESTful-teknisk profil som kon figurer ATS med ett HTTP-klient certifikat:

```xml
<ClaimsProvider>
  <DisplayName>REST APIs</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="REST-GetProfile">
      <DisplayName>Get user extended profile Azure Function web hook</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <Item Key="ServiceUrl">https://your-account.azurewebsites.net/api/GetProfile?code=your-code</Item>
        <Item Key="SendClaimsIn">Body</Item>
        <Item Key="AuthenticationType">ClientCertificate</Item>
        <Item Key="AllowInsecureAuthInProduction">false</Item>
      </Metadata>
      <CryptographicKeys>
        <Key Id="ClientCertificate" StorageReferenceId="B2C_1A_RestApiClientCertificate" />
      </CryptographicKeys>
      ...
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="oauth2-bearer-authentication"></a>OAuth2 Bearer-autentisering 

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

Autentisering med Bearer-token definieras i [OAuth 2.0 Authorization Framework: användningen av token token (RFC 6750)](https://www.rfc-editor.org/rfc/rfc6750.txt). Vid autentisering med Bearer-token skickar Azure AD B2C en HTTP-begäran med en token i Authorization-huvudet.

```http
Authorization: Bearer <token>
```

En Bearer-token är en ogenomskinlig sträng. Det kan vara en JWT-åtkomsttoken eller en sträng som REST API förväntar sig Azure AD B2C att skicka i Authorization-huvudet. Azure AD B2C stöder följande typer:

- **Bearer-token**. För att kunna skicka Bearer-token i den tekniska RESTful-profilen måste din princip först hämta Bearer-token och sedan använda den i den RESTful tekniska profilen.  
- **Statisk Bearer-token**. Använd den här metoden när REST API utfärdar en långsiktig åtkomsttoken. Om du vill använda en statisk Bearer-token skapar du en princip nyckel och skapar en referens från RESTful Technical Profile till din princip nyckel. 


## <a name="using-oauth2-bearer"></a>Använda OAuth2 Bearer  

Följande steg visar hur du använder klientautentiseringsuppgifter för att hämta en Bearer-token och skicka den till Authorization-huvudet i REST API-anropen.  

### <a name="define-a-claim-to-store-the-bearer-token"></a>Definiera ett anspråk för att lagra Bearer-token

Ett anspråk ger tillfällig lagring av data under en Azure AD B2C princip körning. [Anspråks schema](claimsschema.md) är den plats där du deklarerar dina anspråk. Åtkomsttoken måste lagras i ett anspråk som ska användas senare. 

1. Öppna tilläggs filen för principen. Till exempel <em>`SocialAndLocalAccounts/`**`TrustFrameworkExtensions.xml`**</em> .
1. Sök efter [BuildingBlocks](buildingblocks.md) -elementet. Om elementet inte finns lägger du till det.
1. Leta upp [ClaimsSchema](claimsschema.md) -elementet. Om elementet inte finns lägger du till det.
1. Lägg till följande anspråk i **ClaimsSchema** -elementet.  

```xml
<ClaimType Id="bearerToken">
  <DisplayName>Bearer token</DisplayName>
  <DataType>string</DataType>
</ClaimType>
<ClaimType Id="grant_type">
  <DisplayName>Grant type</DisplayName>
  <DataType>string</DataType>
</ClaimType>
<ClaimType Id="scope">
  <DisplayName>scope</DisplayName>
  <DataType>string</DataType>
</ClaimType>
```

### <a name="acquiring-an-access-token"></a>Förvärva en åtkomsttoken 

Du kan få en åtkomsttoken på ett av flera sätt: genom att hämta den [från en federerad identitetsprovider](idp-pass-through-user-flow.md), genom att anropa en REST API som returnerar en åtkomsttoken, genom att använda ett [ROPC-flöde](../active-directory/develop/v2-oauth-ropc.md)eller genom att använda flödet för [klientautentiseringsuppgifter](../active-directory/develop/v2-oauth2-client-creds-grant-flow.md).  

I följande exempel används en REST API teknisk profil för att göra en begäran till Azure AD-token-slutpunkten med de klientautentiseringsuppgifter som skickas som HTTP Basic-autentisering. Om du vill konfigurera detta i Azure AD, se [Microsoft Identity Platform och OAuth 2,0-klientens autentiseringsuppgifter Flow](../active-directory/develop/v2-oauth2-client-creds-grant-flow.md). Du kan behöva ändra detta till ett gränssnitt med din identitets leverantör. 

För ServiceUrl ersätter du namnet på din klient organisation med namnet på din Azure AD-klient. Se [RESTful Technical Profile](restful-technical-profile.md) Reference för alla alternativ som är tillgängliga.

```xml
<TechnicalProfile Id="SecureREST-AccessToken">
  <DisplayName></DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="ServiceUrl">https://login.microsoftonline.com/your-tenant-name.onmicrosoft.com/oauth2/v2.0/token</Item>
    <Item Key="AuthenticationType">Basic</Item>
     <Item Key="SendClaimsIn">Form</Item>
  </Metadata>
  <CryptographicKeys>
    <Key Id="BasicAuthenticationUsername" StorageReferenceId="B2C_1A_SecureRESTClientId" />
    <Key Id="BasicAuthenticationPassword" StorageReferenceId="B2C_1A_SecureRESTClientSecret" />
  </CryptographicKeys>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="grant_type" DefaultValue="client_credentials" />
    <InputClaim ClaimTypeReferenceId="scope" DefaultValue="https://secureb2cfunction.azurewebsites.net/.default" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="bearerToken" PartnerClaimType="access_token" />
  </OutputClaims>
  <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
</TechnicalProfile>
```

### <a name="change-the-rest-technical-profile-to-use-bearer-token-authentication"></a>Ändra den andra tekniska profilen till att använda Bearer-token-autentisering

Om du vill stödja autentisering med Bearer-token i din anpassade princip ändrar du REST API tekniska profilen med följande:

1. Öppna princip filen *TrustFrameworkExtensions.xml* tillägg i arbets katalogen.
1. Sök efter `<TechnicalProfile>` noden som innehåller `Id="REST-API-SignUp"` .
1. Leta upp `<Metadata>` elementet.
1. Ändra *AuthenticationType* till *Bearer* på följande sätt:
    ```xml
    <Item Key="AuthenticationType">Bearer</Item>
    ```
1. Ändra eller Lägg till *UseClaimAsBearerToken* i *bearerToken* enligt följande. *BearerToken* är namnet på det anspråk som Bearer-token kommer att hämtas från (utgående anspråk från `SecureREST-AccessToken` ).

    ```xml
    <Item Key="UseClaimAsBearerToken">bearerToken</Item>
    ```
    
1. Se till att du lägger till det anspråk som används ovan som ett indatamängds anspråk:

    ```xml
    <InputClaim ClaimTypeReferenceId="bearerToken"/>
    ```    

När du har lagt till ovanstående kodfragment bör din tekniska profil se ut som följande XML-kod:

```xml
<ClaimsProvider>
  <DisplayName>REST APIs</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="REST-GetProfile">
      <DisplayName>Get user extended profile Azure Function web hook</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <Item Key="ServiceUrl">https://your-account.azurewebsites.net/api/GetProfile?code=your-code</Item>
        <Item Key="SendClaimsIn">Body</Item>
        <Item Key="AuthenticationType">Bearer</Item>
        <Item Key="UseClaimAsBearerToken">bearerToken</Item>
        <Item Key="AllowInsecureAuthInProduction">false</Item>
      </Metadata>
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="bearerToken"/>
      </InputClaims>
      ...
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="using-a-static-oauth2-bearer"></a>Använda en statisk OAuth2-Bearer 

### <a name="add-the-oauth2-bearer-token-policy-key"></a>Lägg till princip nyckeln OAuth2 Bearer token

Om du vill konfigurera en REST API teknisk profil med en OAuth2 Bearer-token hämtar du en åtkomsttoken från REST API-ägaren. Skapa sedan följande kryptografiska nyckel för att lagra Bearer-token.

1. Logga in på [Azure-portalen](https://portal.azure.com/).
1. Kontrol lera att du använder den katalog som innehåller din Azure AD B2C-klient. Välj **katalog + prenumerations** filter på den översta menyn och välj din Azure AD B2C katalog.
1. Välj **Alla tjänster** på menyn högst upp till vänster i Azure-portalen och sök efter och välj **Azure AD B2C**.
1. På sidan Översikt väljer du **ID för identitets miljö**.
1. Välj **princip nycklar** och välj sedan **Lägg till**.
1. För **alternativ** väljer du `Manual` .
1. Ange ett **namn** för princip nyckeln. Till exempel `RestApiBearerToken`. Prefixet `B2C_1A_` läggs till automatiskt till namnet på din nyckel.
1. I **hemlighet** anger du din klient hemlighet som du tidigare har spelat in.
1. För **nyckel användning** väljer du `Encryption` .
1. Välj **Skapa**.

### <a name="configure-your-rest-api-technical-profile-to-use-the-bearer-token-policy-key"></a>Konfigurera din REST API tekniska profil för att använda princip nyckeln för Bearer-token

När du har skapat den nödvändiga nyckeln konfigurerar du REST API teknisk profils metadata så att de refererar till Bearer-token.

1. Öppna tilläggs princip filen (TrustFrameworkExtensions.xml) i arbets katalogen.
1. Sök efter den REST API tekniska profilen. Till exempel `REST-ValidateProfile` eller `REST-GetProfile` .
1. Leta upp `<Metadata>` elementet.
1. Ändra *AuthenticationType* till `Bearer` .
1. Ändra *AllowInsecureAuthInProduction* till `false` .
1. `</Metadata>`Lägg till följande XML-kodfragment omedelbart efter det avslutande elementet:
    ```xml
    <CryptographicKeys>
       <Key Id="BearerAuthenticationToken" StorageReferenceId="B2C_1A_RestApiBearerToken" />
    </CryptographicKeys>
    ```

Följande är ett exempel på en RESTful-teknisk profil som kon figurer ATS med autentisering med Bearer-token:

```xml
<ClaimsProvider>
  <DisplayName>REST APIs</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="REST-GetProfile">
      <DisplayName>Get user extended profile Azure Function web hook</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <Item Key="ServiceUrl">https://your-account.azurewebsites.net/api/GetProfile?code=your-code</Item>
        <Item Key="SendClaimsIn">Body</Item>
        <Item Key="AuthenticationType">Bearer</Item>
        <Item Key="AllowInsecureAuthInProduction">false</Item>
      </Metadata>
      <CryptographicKeys>
        <Key Id="BearerAuthenticationToken" StorageReferenceId="B2C_1A_RestApiBearerToken" />
      </CryptographicKeys>
      ...
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="api-key-authentication"></a>Autentisering med API-nyckel

API-nyckeln är en unik identifierare som används för att autentisera en användare för att komma åt en REST API-slutpunkt. Nyckeln skickas i en anpassad HTTP-rubrik. Till exempel använder [Azure Functions HTTP-utlösaren](../azure-functions/functions-bindings-http-webhook-trigger.md#authorization-keys) `x-functions-key` http-huvudet för att identifiera beställaren.  

### <a name="add-api-key-policy-keys"></a>Lägg till princip nycklar för API-nyckel

Om du vill konfigurera en REST API teknisk profil med autentisering med API-nycklar skapar du följande kryptografiska nyckel för att lagra API-nyckeln:

1. Logga in på [Azure-portalen](https://portal.azure.com/).
1. Kontrol lera att du använder den katalog som innehåller din Azure AD B2C-klient. Välj **katalog + prenumerations** filter på den översta menyn och välj din Azure AD B2C katalog.
1. Välj **Alla tjänster** på menyn högst upp till vänster i Azure-portalen och sök efter och välj **Azure AD B2C**.
1. På sidan Översikt väljer du **ID för identitets miljö**.
1. Välj **princip nycklar** och välj sedan **Lägg till**.
1. För **alternativ** väljer du **manuell**.
1. I **namn** skriver du **RestApiKey**.
    Prefixet *B2C_1A_* kan läggas till automatiskt.
1. I rutan **hemlighet** anger du REST API nyckeln.
1. För **nyckel användning** väljer du **kryptering**.
1. Välj **Skapa**.


### <a name="configure-your-rest-api-technical-profile-to-use-api-key-authentication"></a>Konfigurera din REST API tekniska profil för att använda API-autentiseringsnyckel

När du har skapat den nödvändiga nyckeln konfigurerar du REST API teknisk profils metadata så att de refererar till autentiseringsuppgifterna.

1. Öppna tilläggs princip filen (TrustFrameworkExtensions.xml) i arbets katalogen.
1. Sök efter den REST API tekniska profilen. Till exempel `REST-ValidateProfile` eller `REST-GetProfile` .
1. Leta upp `<Metadata>` elementet.
1. Ändra *AuthenticationType* till `ApiKeyHeader` .
1. Ändra *AllowInsecureAuthInProduction* till `false` .
1. `</Metadata>`Lägg till följande XML-kodfragment omedelbart efter det avslutande elementet:
    ```xml
    <CryptographicKeys>
        <Key Id="x-functions-key" StorageReferenceId="B2C_1A_RestApiKey" />
    </CryptographicKeys>
    ```

**ID: t** för krypterings nyckeln definierar HTTP-huvudet. I det här exemplet skickas API-nyckeln som **x-Functions-Key**.

Följande är ett exempel på en RESTful-teknisk profil som kon figurer ATS för att anropa en Azure-funktion med API-nyckel autentisering:

```xml
<ClaimsProvider>
  <DisplayName>REST APIs</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="REST-GetProfile">
      <DisplayName>Get user extended profile Azure Function web hook</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <Item Key="ServiceUrl">https://your-account.azurewebsites.net/api/GetProfile?code=your-code</Item>
        <Item Key="SendClaimsIn">Body</Item>
        <Item Key="AuthenticationType">ApiKeyHeader</Item>
        <Item Key="AllowInsecureAuthInProduction">false</Item>
      </Metadata>
      <CryptographicKeys>
        <Key Id="x-functions-key" StorageReferenceId="B2C_1A_RestApiKey" />
      </CryptographicKeys>
      ...
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="next-steps"></a>Nästa steg

- Läs mer om elementet [RESTful Technical Profile](restful-technical-profile.md) i referensen IEF.