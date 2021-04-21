---
title: Skydda en Restful-tjänst i din Azure AD B2C
titleSuffix: Azure AD B2C
description: Skydda dina REST API anspråksutbyten i din Azure AD B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 04/21/2021
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: a1c161c28a589e4250fded13cd3d94ccdda97b55
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2021
ms.locfileid: "107829826"
---
# <a name="secure-your-restful-services"></a>Skydda dina RESTful-tjänster 

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

När du integrerar REST API i en Azure AD B2C användarresa måste du skydda REST API slutpunkten med autentisering. Detta säkerställer att endast tjänster som har rätt autentiseringsuppgifter, till exempel Azure AD B2C, kan göra anrop till REST API slutpunkten.

Lär dig hur du integrerar REST API i Azure AD B2C användarresa i artiklarna Verifiera användarindata och Lägga [till REST API anspråksutbyten](custom-policy-rest-api-claims-exchange.md) i anpassade principer. [](custom-policy-rest-api-claims-validation.md)

I den här artikeln lär du dig hur du skyddar REST API med antingen GRUNDLÄGGANDE HTTP-autentisering, klientcertifikat eller OAuth2-autentisering. 

## <a name="prerequisites"></a>Förutsättningar

Slutför stegen i någon av följande "Instruktioner"-guider:

- [Integrera REST API anspråksutbyten i din Azure AD B2C för att verifiera användarindata.](custom-policy-rest-api-claims-validation.md)
- [Lägga REST API anspråksutbyten i anpassade principer](custom-policy-rest-api-claims-exchange.md)

## <a name="http-basic-authentication"></a>Grundläggande HTTP-autentisering

Grundläggande HTTP-autentisering definieras [i RFC 2617](https://tools.ietf.org/html/rfc2617). Grundläggande autentisering fungerar på följande sätt: Azure AD B2C skickar en HTTP-begäran med klientautentiseringsuppgifterna i auktoriseringshuvudet. Autentiseringsuppgifterna formateras som den base64-kodade strängen "name:password".  

### <a name="add-rest-api-username-and-password-policy-keys"></a>Lägga REST API användarnamn och lösenord principnycklar

Om du vill REST API en teknisk profil med grundläggande HTTP-autentisering skapar du följande kryptografiska nycklar för att lagra användarnamnet och lösenordet:

1. Logga in på [Azure-portalen](https://portal.azure.com/).
1. Kontrollera att du använder den katalog som innehåller din Azure AD B2C klientorganisation. Välj filtret **Katalog +** prenumeration på den översta menyn och välj Azure AD B2C katalog.
1. Välj **Alla tjänster** på menyn högst upp till vänster i Azure-portalen och sök efter och välj **Azure AD B2C**.
1. På sidan Översikt väljer du **Identity Experience Framework**.
1. Välj **Principnycklar** och välj sedan Lägg **till**.
1. För **Alternativ** väljer du **Manuell**.
1. I **namn** skriver du **RestApiUsername**.
    Prefixet *B2C_1A_* läggas till automatiskt.
1. I rutan **Hemlighet** anger du REST API användarnamn.
1. För **Nyckelanvändning** väljer du **Kryptering.**
1. Välj **Skapa**.
1. Välj **Principnycklar** igen.
1. Välj **Lägg till**.
1. För **Alternativ** väljer du **Manuell**.
1. I **namn** skriver du **RestApiPassword.**
    Prefixet *B2C_1A_* läggas till automatiskt.
1. I rutan **Hemlighet** anger du REST API lösenord.
1. För **Nyckelanvändning** väljer du **Kryptering.**
1. Välj **Skapa**.

### <a name="configure-your-rest-api-technical-profile-to-use-http-basic-authentication"></a>Konfigurera din REST API profil för att använda grundläggande HTTP-autentisering

När du har skapat de nödvändiga nycklarna konfigurerar du REST API metadata för den tekniska profilen så att de refererar till autentiseringsuppgifterna.

1. I arbetskatalogen öppnar du tilläggsprincipfilen (TrustFrameworkExtensions.xml).
1. Sök efter REST API tekniska profilen. Till exempel `REST-ValidateProfile` , eller `REST-GetProfile` .
1. Leta upp `<Metadata>` elementet .
1. Ändra *AuthenticationType* till `Basic` .
1. Ändra *AllowInsecureAuthInProduction* till `false` .
1. Omedelbart efter det avslutande `</Metadata>` elementet lägger du till följande XML-kodfragment:
    ```xml
    <CryptographicKeys>
        <Key Id="BasicAuthenticationUsername" StorageReferenceId="B2C_1A_RestApiUsername" />
        <Key Id="BasicAuthenticationPassword" StorageReferenceId="B2C_1A_RestApiPassword" />
    </CryptographicKeys>
    ```

Följande är ett exempel på en restful-teknisk profil som konfigurerats med grundläggande HTTP-autentisering:

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

## <a name="https-client-certificate-authentication"></a>HTTPS-klientcertifikatautentisering

Klientcertifikatautentisering är en ömsesidig certifikatbaserad autentisering, där klienten, Azure AD B2C, tillhandahåller sitt klientcertifikat till servern för att bevisa sin identitet. Detta sker som en del av SSL-handskakningen. Endast tjänster som har rätt certifikat, till exempel Azure AD B2C, har åtkomst till din REST API tjänst. Klientcertifikatet är ett digitalt X.509-certifikat. I produktionsmiljöer måste den signeras av en certifikatutfärdare.

### <a name="prepare-a-self-signed-certificate-optional"></a>Förbereda ett själv signerat certifikat (valfritt)

Om du inte redan har ett certifikat kan du använda ett själv signerat certifikat för icke-produktionsmiljöer. I Windows kan du använda PowerShells cmdlet [New-SelfSignedCertificate](/powershell/module/pkiclient/new-selfsignedcertificate) för att generera ett certifikat.

1. Kör det här PowerShell-kommandot för att generera ett själv signerat certifikat. Ändra argumentet `-Subject` efter behov för ditt program och Azure AD B2C klientorganisationens namn. Du kan också justera datumet `-NotAfter` om du vill ange en annan giltighetstid för certifikatet.
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
1. Öppna **Hantera användarcertifikat Aktuella** personliga  >    >    >  **användarcertifikat**  >  *yourappname.yourtenant.onmicrosoft.com*.
1. Välj certifikatet för > **Exportera**  >  **alla**  >  **aktiviteter.**
1. Välj **Ja**  >  **Nästa**  >  **Ja, exportera den privata nyckeln**  >  **Nästa.**
1. Acceptera standardinställningarna för **Filformat för export.**
1. Ange ett lösenord för certifikatet.

### <a name="add-a-client-certificate-policy-key"></a>Lägga till en principnyckel för klientcertifikat

1. Logga in på [Azure-portalen](https://portal.azure.com/).
1. Kontrollera att du använder den katalog som innehåller din Azure AD B2C klientorganisation. Välj filtret **Katalog + prenumeration** på den översta menyn och välj Azure AD B2C katalog.
1. Välj **Alla tjänster** på menyn högst upp till vänster i Azure-portalen och sök efter och välj **Azure AD B2C**.
1. På sidan Översikt väljer du **Identity Experience Framework**.
1. Välj **Principnycklar** och välj sedan Lägg **till**.
1. I rutan **Alternativ** väljer du **Ladda upp**.
1. I rutan **Namn** skriver du **RestApiClientCertificate**.
    Prefixet *B2C_1A_* läggs till automatiskt.
1. I rutan **Filuppladdning** väljer du certifikatets PFX-fil med en privat nyckel.
1. Skriv **certifikatets** lösenord i rutan Lösenord.
1. Välj **Skapa**.

### <a name="configure-your-rest-api-technical-profile-to-use-client-certificate-authentication"></a>Konfigurera din REST API profil för att använda autentisering med klientcertifikat

När du har skapat den nödvändiga nyckeln konfigurerar REST API metadata för den tekniska profilen så att de refererar till klientcertifikatet.

1. I arbetskatalogen öppnar du tilläggsprincipfilen (TrustFrameworkExtensions.xml).
1. Sök efter REST API tekniska profilen. Till exempel `REST-ValidateProfile` , eller `REST-GetProfile` .
1. Leta upp `<Metadata>` elementet .
1. Ändra *AuthenticationType* till `ClientCertificate` .
1. Ändra *AllowInsecureAuthInProduction* till `false` .
1. Omedelbart efter det avslutande `</Metadata>` elementet lägger du till följande XML-kodfragment:
    ```xml
    <CryptographicKeys>
       <Key Id="ClientCertificate" StorageReferenceId="B2C_1A_RestApiClientCertificate" />
    </CryptographicKeys>
    ```

Följande är ett exempel på en teknisk RESTful-profil som konfigurerats med ett HTTP-klientcertifikat:

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

## <a name="oauth2-bearer-authentication"></a>OAuth2-autentisering av bearer 

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

Autentisering med bearer-token definieras [i OAuth2.0 Authorization Framework: Bearer Token Usage (RFC 6750)](https://www.rfc-editor.org/rfc/rfc6750.txt). Vid autentisering med bearer-token Azure AD B2C en HTTP-begäran med en token i auktoriseringsrubriken.

```http
Authorization: Bearer <token>
```

En bearer-token är en täckande sträng. Det kan vara en JWT-åtkomsttoken eller en sträng som REST API förväntar Azure AD B2C skicka i auktoriseringshuvudet. Azure AD B2C stöder följande typer:

- **Bearer-token**. För att kunna skicka en bearer-token i den tekniska Restful-profilen måste principen först hämta bearer-token och sedan använda den i den tekniska RESTful-profilen.  
- **Statisk bearer-token**. Använd den här metoden när REST API utfärdar en långsiktig åtkomsttoken. Om du vill använda en statisk bearer-token skapar du en principnyckel och gör en referens från den tekniska RESTful-profilen till din principnyckel. 


## <a name="using-oauth2-bearer"></a>Använda OAuth2-bearer  

Följande steg visar hur du använder klientautentiseringsuppgifter för att hämta en bearer-token och skickar den till auktoriseringsrubriken för REST API-anrop.  

### <a name="define-a-claim-to-store-the-bearer-token"></a>Definiera ett anspråk för att lagra bearer-token

Ett anspråk ger tillfällig lagring av data under en Azure AD B2C principkörning. [Anspråksschemat](claimsschema.md) är den plats där du deklarerar dina anspråk. Åtkomsttoken måste lagras i ett anspråk som ska användas senare. 

1. Öppna tilläggsfilen för principen. Till exempel <em>`SocialAndLocalAccounts/`**`TrustFrameworkExtensions.xml`**</em> .
1. Sök efter [elementet BuildingBlocks.](buildingblocks.md) Om elementet inte finns lägger du till det.
1. Leta upp [elementet ClaimsSchema.](claimsschema.md) Om elementet inte finns lägger du till det.
1. Lägg till följande anspråk i **elementet ClaimsSchema.**  

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

### <a name="acquiring-an-access-token"></a>Hämta en åtkomsttoken 

Du kan hämta en åtkomsttoken på något av följande sätt: genom att hämta den från en [federerad](idp-pass-through-user-flow.md)identitetsprovider, genom att anropa en REST API som returnerar en åtkomsttoken, genom att använda ett [ROPC-flöde](../active-directory/develop/v2-oauth-ropc.md)eller genom att använda flödet för klientautentiseringsuppgifter. [](../active-directory/develop/v2-oauth2-client-creds-grant-flow.md) Flödet för klientautentiseringsuppgifter används ofta för server-till-server-interaktioner som måste köras i bakgrunden, utan omedelbar interaktion med en användare.

#### <a name="acquiring-an-azure-ad-access-token"></a>Hämta en Azure AD-åtkomsttoken 

I följande exempel används en REST API teknisk profil för att göra en begäran till Azure AD-tokenslutpunkten med de klientautentiseringsuppgifter som skickas som grundläggande HTTP-autentisering. Mer information finns i [Microsoft Identity Platform och flödet för OAuth 2.0-klientautentiseringsuppgifter.](../active-directory/develop/v2-oauth2-client-creds-grant-flow.md) 

Innan den tekniska profilen kan interagera med Azure AD för att hämta en åtkomsttoken måste du registrera ett program. Azure AD B2C förlitar sig på Azure AD-plattformen. Du kan skapa appen i din Azure AD B2C eller i valfri Azure AD-klientorganisation som du hanterar. Så här registrerar du ett program:

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Välj filtret **Katalog +** prenumeration på den översta menyn och välj sedan den katalog som innehåller din Azure AD eller välj Azure AD B2C klientorganisation.
1. I den vänstra menyn väljer du **Azure Active Directory**. Du kan också **välja Alla tjänster** och söka efter och välja **Azure Active Directory**.
1. Välj **Appregistreringar** och välj sedan **Ny registrering.**
1. Ange **ett Namn** för programmet. Du kan till *exempel Client_Credentials_Auth_app*.
1. Under **Kontotyper som stöds** väljer du Endast konton i den här **organisationskatalogen.**
1. Välj **Register** (Registrera).
2. Registrera **program-ID :t (klienten).** 


För ett flöde för klientautentiseringsuppgifter måste du skapa en programhemlighet. Klienthemligheten kallas även för ett programlösenord. Hemligheten används av ditt program för att hämta en åtkomsttoken.

1. På sidan **Azure AD - Appregistreringar** väljer du det program som du skapade, till exempel *Client_Credentials_Auth_app*.
1. I den vänstra menyn, under **Hantera,** väljer **du Certifikat & hemligheter**.
1. Välj **Ny klienthemlighet**.
1. Ange en beskrivning av klienthemligheten i **rutan** Beskrivning. Till exempel *clientsecret1*.
1. Under **Förfaller** väljer du en varaktighet för vilken hemligheten är giltig och väljer sedan Lägg **till**.
1. Registrera hemlighetens **Värde för** användning i klientprogramkoden. Det här hemliga värdet visas aldrig igen när du lämnar den här sidan. Du använder det här värdet som programhemlighet i programmets kod.

#### <a name="create-azure-ad-b2c-policy-keys"></a>Skapa Azure AD B2C principnycklar

Du måste lagra klient-ID:t och klienthemligheten som du tidigare registrerade i Azure AD B2C klientorganisationen.

1. Logga in på [Azure-portalen](https://portal.azure.com/).
2. Kontrollera att du använder den katalog som innehåller din Azure AD B2C klientorganisation. Välj filtret **Katalog +** prenumeration på den översta menyn och välj den katalog som innehåller din klientorganisation.
3. Välj **Alla tjänster** på menyn högst upp till vänster i Azure-portalen och sök efter och välj **Azure AD B2C**.
4. På sidan Översikt väljer du **Identity Experience Framework**.
5. Välj **Principnycklar och** välj sedan Lägg **till**.
6. För **Alternativ** väljer du `Manual` .
7. Ange ett **namn** för principnyckeln, `SecureRESTClientId` . Prefixet `B2C_1A_` läggs automatiskt till i namnet på din nyckel.
8. I **Hemlighet** anger du det klient-ID som du tidigare registrerade.
9. För **Nyckelanvändning** väljer du `Signature` .
10. Välj **Skapa**.
11. Skapa en annan principnyckel med följande inställningar:
    -   **Namn:** `SecureRESTClientSecret` .
    -   **Hemlighet:** Ange din klienthemlighet som du tidigare har spelat in

För ServiceUrl ersätter du your-tenant-name med namnet på din Azure AD-klientorganisation. Se [referensen för RESTful-teknisk profil](restful-technical-profile.md) för alla tillgängliga alternativ.

```xml
<TechnicalProfile Id="REST-AcquireAccessToken">
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
    <InputClaim ClaimTypeReferenceId="scope" DefaultValue="https://graph.microsoft.com/.default" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="bearerToken" PartnerClaimType="access_token" />
  </OutputClaims>
  <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
</TechnicalProfile>
```

### <a name="change-the-rest-technical-profile-to-use-bearer-token-authentication"></a>Ändra den tekniska PROFILEN för REST för att använda autentisering med bearer-token

För att stödja autentisering med bearer-token i din anpassade princip ändrar REST API tekniska profilen med följande:

1. I arbetskatalogen öppnar du *TrustFrameworkExtensions.xml* fil för tilläggsprincipen.
1. Sök efter `<TechnicalProfile>` noden som innehåller `Id="REST-API-SignUp"` .
1. Leta upp `<Metadata>` elementet .
1. Ändra *AuthenticationType till* *Bearer* enligt följande:
    ```xml
    <Item Key="AuthenticationType">Bearer</Item>
    ```
1. Ändra eller lägg till *UseClaimAsBearerToken* *till bearerToken* enligt följande. *BearerToken* är namnet på anspråket som bearer-token hämtas från (utgående anspråk från `REST-AcquireAccessToken` ).

    ```xml
    <Item Key="UseClaimAsBearerToken">bearerToken</Item>
    ```
    
1. Se till att du lägger till anspråket som används ovan som inkommande anspråk:

    ```xml
    <InputClaim ClaimTypeReferenceId="bearerToken"/>
    ```    

När du har lagt till kodfragmenten ovan bör den tekniska profilen se ut som följande XML-kod:

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

## <a name="using-a-static-oauth2-bearer"></a>Använda en statisk OAuth2-bearer 

### <a name="add-the-oauth2-bearer-token-policy-key"></a>Lägga till principnyckeln för OAuth2-bearer-token

Om du vill REST API en teknisk profil med en OAuth2-ägartoken hämtar du en åtkomsttoken från REST API ägaren. Skapa sedan följande kryptografiska nyckel för att lagra bearer-token.

1. Logga in på [Azure-portalen](https://portal.azure.com/).
1. Kontrollera att du använder den katalog som innehåller din Azure AD B2C klientorganisation. Välj filtret **Katalog +** prenumeration på den översta menyn och välj Azure AD B2C katalog.
1. Välj **Alla tjänster** på menyn högst upp till vänster i Azure-portalen och sök efter och välj **Azure AD B2C**.
1. På sidan Översikt väljer du **Identity Experience Framework**.
1. Välj **Principnycklar** och välj sedan Lägg **till**.
1. För **Alternativ** väljer du `Manual` .
1. Ange ett **namn** för principnyckeln. Till exempel `RestApiBearerToken`. Prefixet `B2C_1A_` läggs automatiskt till i namnet på din nyckel.
1. I **Hemlighet** anger du din klienthemlighet som du tidigare antecknade.
1. För **Nyckelanvändning** väljer du `Encryption` .
1. Välj **Skapa**.

### <a name="configure-your-rest-api-technical-profile-to-use-the-bearer-token-policy-key"></a>Konfigurera din REST API tekniska profil för att använda principnyckeln för bearer-token

När du har skapat den nödvändiga nyckeln konfigurerar REST API metadata för den tekniska profilen så att de refererar till bearer-token.

1. I arbetskatalogen öppnar du tilläggsprincipfilen (TrustFrameworkExtensions.xml).
1. Sök efter den REST API tekniska profilen. Till exempel `REST-ValidateProfile` , eller `REST-GetProfile` .
1. Leta upp `<Metadata>` elementet .
1. Ändra *AuthenticationType* till `Bearer` .
1. Ändra *AllowInsecureAuthInProduction* till `false` .
1. Lägg till följande `</Metadata>` XML-kodfragment direkt efter det avslutande elementet:
    ```xml
    <CryptographicKeys>
       <Key Id="BearerAuthenticationToken" StorageReferenceId="B2C_1A_RestApiBearerToken" />
    </CryptographicKeys>
    ```

Följande är ett exempel på en teknisk RESTful-profil som konfigurerats med autentisering med bearer-token:

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

## <a name="api-key-authentication"></a>API-nyckelautentisering

API-nyckeln är en unik identifierare som används för att autentisera en användare för att få åtkomst REST API slutpunkt. Nyckeln skickas i ett anpassat HTTP-huvud. Till exempel använder [Azure Functions HTTP-utlösaren](../azure-functions/functions-bindings-http-webhook-trigger.md#authorization-keys) `x-functions-key` HTTP-huvudet för att identifiera beställaren.  

### <a name="add-api-key-policy-keys"></a>Lägga till API-nyckelprincipnycklar

Om du vill REST API en teknisk profil med API-nyckelautentisering skapar du följande kryptografiska nyckel för att lagra API-nyckeln:

1. Logga in på [Azure-portalen](https://portal.azure.com/).
1. Kontrollera att du använder den katalog som innehåller din Azure AD B2C klientorganisation. Välj filtret **Katalog +** prenumeration på den översta menyn och välj Azure AD B2C katalog.
1. Välj **Alla tjänster** på menyn högst upp till vänster i Azure-portalen och sök efter och välj **Azure AD B2C**.
1. På sidan Översikt väljer du **Identity Experience Framework**.
1. Välj **Principnycklar** och välj sedan Lägg **till.**
1. För **Alternativ** väljer du **Manuell.**
1. I **Namn** skriver du **RestApiKey.**
    Prefixet *B2C_1A_* läggas till automatiskt.
1. I rutan **Hemlighet** anger du REST API nyckel.
1. För **Nyckelanvändning** väljer du **Kryptering.**
1. Välj **Skapa**.


### <a name="configure-your-rest-api-technical-profile-to-use-api-key-authentication"></a>Konfigurera din tekniska REST API att använda API-nyckelautentisering

När du har skapat den nödvändiga nyckeln konfigurerar du REST API metadata för den tekniska profilen så att de refererar till autentiseringsuppgifterna.

1. I arbetskatalogen öppnar du tilläggsprincipfilen (TrustFrameworkExtensions.xml).
1. Sök efter REST API tekniska profilen. Till exempel `REST-ValidateProfile` , eller `REST-GetProfile` .
1. Leta upp `<Metadata>` elementet .
1. Ändra *AuthenticationType* till `ApiKeyHeader` .
1. Ändra *AllowInsecureAuthInProduction* till `false` .
1. Lägg till följande `</Metadata>` XML-kodfragment direkt efter det avslutande elementet:
    ```xml
    <CryptographicKeys>
        <Key Id="x-functions-key" StorageReferenceId="B2C_1A_RestApiKey" />
    </CryptographicKeys>
    ```

**ID:t** för den kryptografiska nyckeln definierar HTTP-huvudet. I det här exemplet skickas API-nyckeln som **x-functions-key**.

Följande är ett exempel på en restful-teknisk profil som konfigurerats för att anropa en Azure-funktion med API-nyckelautentisering:

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

- Läs mer om [restful-elementet för teknisk](restful-technical-profile.md) profil i IEF-referensen.
