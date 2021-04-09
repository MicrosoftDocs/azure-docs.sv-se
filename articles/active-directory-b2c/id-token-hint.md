---
title: Definiera en teknisk profil för ID-token i en anpassad princip
titleSuffix: Azure AD B2C
description: Definiera en teknisk profil för ID-token i en anpassad princip i Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 10/16/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: d77e145cabcef2931d5fe6e76599da7931e576e8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "97669167"
---
# <a name="define-an-id-token-hint-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Definiera en teknisk profil för ID-token i en Azure Active Directory B2C anpassad princip

Azure AD B2C tillåter att förlitande parters program skickar en inkommande JWT som en del av OAuth2. JWT-token kan utfärdas av ett program för förlitande part eller en identitetsprovider, och det kan skicka ett tips om användaren eller begäran om auktorisering. Azure AD B2C verifierar signaturen, utfärdarens namn och token-målgruppen och extraherar anspråket från den inkommande token.

## <a name="use-cases"></a>Användningsfall

Du kan använda den här lösningen för att skicka data till Azure AD B2C inkapslade i en enda JWT-token. [Lösningen för registrering med inbjudan till e-post](https://github.com/azure-ad-b2c/samples/blob/master/policies/invite/README.md), där system administratören kan skicka en signerad inbjudan till användare, baseras på id_token_hint. Endast användare med åtkomst till e-postinbjudan kan skapa kontot i katalogen.

## <a name="token-signing-approach"></a>Metod för Token-signering

Med id_token_hint skapar token utfärdaren (en förlitande part eller en identitets leverantör) token och signerar den sedan med hjälp av en signerings nyckel för att bevisa att token kommer från en betrodd källa. Signerings nyckeln kan vara symmetrisk eller asymmetrisk. Symmetrisk kryptering eller kryptering med privat nyckel använder en delad hemlighet för att både signera och validera signaturen. Asymmetrisk kryptering eller kryptering med offentliga nycklar är ett kryptografiskt system som använder både en privat nyckel och en offentlig nyckel. Den privata nyckeln är endast känd för token-utfärdaren och används för att signera token. Den offentliga nyckeln delas med Azure AD B2C-principen för att verifiera signaturen för token.

## <a name="token-format"></a>Token-format

Id_token_hint måste vara en giltig JWT-token. I följande tabell visas de anspråk som är obligatoriska. Ytterligare anspråk är valfria.

| Name | Begär | Exempelvärde | Beskrivning |
| ---- | ----- | ------------- | ----------- |
| Målgrupp | `aud` | `a489fc44-3cc0-4a78-92f6-e413cd853eae` | Identifierar den avsedda mottagaren för token. Mål gruppen är en godtycklig sträng som definieras av token Issuer. Azure AD B2C validerar det här värdet och avvisar token om det inte matchar.  |
| Utfärdare | `iss` |`https://localhost` | Identifierar Security Token Service (token Issuer). Utfärdaren är en godtycklig URI som definieras av token Issuer. Azure AD B2C validerar det här värdet och avvisar token om det inte matchar.  |
| Förfallo tid | `exp` | `1600087315` | Tiden då token blir ogiltig, representeras i epok tid. Azure AD B2C validerar det här värdet och avvisar token om token har upphört att gälla.|
| Inte före | `nbf` | `1599482515` | Tiden då token börjar gälla, representeras i epok tiden. Den här tiden är vanligt vis samma som den tidpunkt då token utfärdades. Azure AD B2C validerar det här värdet och avvisar token om token för token inte är giltig. |

 Följande token är ett exempel på en giltig ID-token:

```json
{
  "alg": "HS256",
  "typ": "JWT"
}.{
  "displayName": " John Smith",
  "userId": "john.s@contoso.com",
  "nbf": 1599482515,
  "exp": 1600087315,
  "iss": "https://localhost",
  "aud": "a489fc44-3cc0-4a78-92f6-e413cd853eae"
}
```

## <a name="protocol"></a>Protokoll

Namnattributet **för** **protokoll** elementet måste anges till `None` . Till exempel är protokollet för den **IdTokenHint_ExtractClaims** tekniska profilen `None` :

```xml
<TechnicalProfile Id="IdTokenHint_ExtractClaims">
  <DisplayName> My ID Token Hint TechnicalProfile</DisplayName>
  <Protocol Name="None" />
  ...
```

Den tekniska profilen anropas från ett Dirigerings steg med typen `GetClaims` .

```xml
<OrchestrationStep Order="1" Type="GetClaims" CpimIssuerTechnicalProfileReferenceId="IdTokenHint_ExtractClaims" />
``` 

## <a name="output-claims"></a>Utgående anspråk

**OutputClaims** -elementet innehåller en lista över anspråk som ska extraheras från JWT-token. Du kan behöva mappa namnet på det anspråk som definierats i principen till det namn som definierats i JWT-token. Du kan också ta med anspråk som inte returneras av JWT-token, förutsatt att du ställer in `DefaultValue` attributet.

## <a name="metadata"></a>Metadata

Följande metadata är relevanta när du använder symmetrisk nyckel. 

| Attribut | Krävs | Beskrivning |
| --------- | -------- | ----------- |
| utfärdare | Ja | Identifierar Security Token Service (token Issuer). Värdet måste vara identiskt med `iss` anspråket i JWT-token-anspråket. | 
| IdTokenAudience | Ja | Identifierar den avsedda mottagaren för token. Måste vara identiskt med `aud` anspråket inom JWT-token-anspråket. | 

Följande metadata är relevanta när du använder en asymmetrisk nyckel. 

| Attribut | Krävs | Beskrivning |
| --------- | -------- | ----------- |
| METADATATJÄNST| Ja | En URL som pekar på ett konfigurations dokument för token, som även kallas en OpenID-känd konfigurations slut punkt.   |
| utfärdare | Inga | Identifierar Security Token Service (token Issuer). Det här värdet kan användas för att skriva över värdet som kon figurer ATS i metadata och måste vara identiskt med `iss` anspråket i JWT-token-anspråket. |  
| IdTokenAudience | Inga | Identifierar den avsedda mottagaren för token. Måste vara identiskt med `aud` anspråket inom JWT-token-anspråket. |  

## <a name="cryptographic-keys"></a>Kryptografiska nycklar

När du använder en symmetrisk nyckel innehåller **CryptographicKeys** -elementet följande attribut:

| Attribut | Krävs | Beskrivning |
| --------- | -------- | ----------- |
| client_secret | Ja | Den kryptografiska nyckel som används för att validera JWT-tokens signatur.|


## <a name="how-to-guide"></a>Instruktionsguide

### <a name="issue-a-token-with-symmetric-keys"></a>Utfärda en token med symmetriska nycklar

#### <a name="step-1-create-a-shared-key"></a>Steg 1. Skapa en delad nyckel 

Skapa en nyckel som kan användas för att signera token. Använd till exempel följande PowerShell-kod för att generera en nyckel.

```powershell
$bytes = New-Object Byte[] 32
$rand = [System.Security.Cryptography.RandomNumberGenerator]::Create()
$rand.GetBytes($bytes)
$rand.Dispose()
$newClientSecret = [System.Convert]::ToBase64String($bytes)
$newClientSecret
```

Den här koden skapar en hemlig sträng som `VK62QTn0m1hMcn0DQ3RPYDAr6yIiSvYgdRwjZtU5QhI=` .

#### <a name="step-2-add-the-signing-key-to-azure-ad-b2c"></a>Steg 2. Lägg till signerings nyckeln i Azure AD B2C

Samma nyckel som används av token Issuer måste skapas i Azure AD B2C princip nycklar.  

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Välj ikonen **katalog + prenumeration** i portalens verktygsfält och välj sedan den katalog som innehåller Azure AD B2C klienten.
1. I Azure Portal söker du efter och väljer **Azure AD B2C**.
1. På sidan Översikt, under **principer**, väljer du **Identity Experience Framework**.
1. Välj **princip nycklar** 
1. Välj **manuell**.
1. Som **namn** använder du `IdTokenHintKey` .  
   Prefixet `B2C_1A_` kan läggas till automatiskt.
1. I rutan **hemlighet** anger du den inloggnings nyckel som du skapade tidigare.
1. Använd **kryptering** för **nyckel användning**.
1. Välj **Skapa**.
1. Bekräfta att du har skapat nyckeln `B2C_1A_IdTokenHintKey` .


#### <a name="step-3-add-the-id-token-hint-technical-profile"></a>Steg 3. Lägg till den tekniska profilen för ID-token-tips

Följande tekniska profil verifierar token och extraherar anspråken. 

```xml
<ClaimsProvider>
  <DisplayName>My ID Token Hint ClaimsProvider</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="IdTokenHint_ExtractClaims">
      <DisplayName> My ID Token Hint TechnicalProfile</DisplayName>
      <Protocol Name="None" />
      <Metadata>
        <Item Key="IdTokenAudience">a489fc44-3cc0-4a78-92f6-e413cd853eae</Item>
        <Item Key="issuer">https://localhost</Item>
      </Metadata>
      <CryptographicKeys>
        <Key Id="client_secret" StorageReferenceId="B2C_1A_IdTokenHintKey" />
      </CryptographicKeys>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="userId" />
      </OutputClaims>
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

#### <a name="step-4-prepare-your-policy"></a>Steg 4. Förbered principen

Slutför steget [Konfigurera ditt policy](#configure-your-policy) .

#### <a name="step-5-prepare-the-code"></a>Steg 5. Förbered koden  

[GitHub-exemplet](https://github.com/azure-ad-b2c/id_token_hint/tree/master/dotnet_core_symmetric_key) är ett ASP.NET-webbprogram och en konsolsession som genererar en ID-token som är signerad med en symmetrisk nyckel. 


### <a name="issue-a-token-with-asymmetric-keys"></a>Utfärda en token med asymmetriska nycklar

Med en asymmetrisk nyckel signeras token med hjälp av RSA-certifikat. Det här programmet är värd för en öppen ID Connect-slutpunkt för metadata och JSON Web Keys (JWKs) som används av Azure AD B2C för att verifiera signaturen för ID-token.

Token Issuer måste ange följande slut punkter:

* `/.well-known/openid-configuration` – En välkänd konfigurations slut punkt med relevant information om token, t. ex. token utfärdarens namn och länken till JWK-slutpunkten. 
* `/.well-known/keys` – slut punkten för JSON-webbnyckeln (JWK) med den offentliga nyckel som används för att signera nyckeln (med den privata nyckel delen av certifikatet).

Se exemplet [TokenMetadataController. cs](https://github.com/azure-ad-b2c/id-token-builder/blob/master/source-code/B2CIdTokenBuilder/Controllers/TokenMetadataController.cs) .NET MVC Controller.

#### <a name="step-1-prepare-a-self-signed-certificate"></a>Steg 1. Förbereda ett självsignerat certifikat

Om du inte redan har ett certifikat kan du använda ett självsignerat certifikat för den här instruktions guiden. I Windows kan du använda PowerShell: s [New-SelfSignedCertificate-](/powershell/module/pkiclient/new-selfsignedcertificate) cmdlet för att skapa ett certifikat.

Kör PowerShell-kommandot för att generera ett självsignerat certifikat. Ändra `-Subject` argumentet efter behov för ditt program och Azure AD B2C klient namn. Du kan också justera `-NotAfter` datumet för att ange ett annat förfallo datum för certifikatet.

```PowerShell
New-SelfSignedCertificate `
    -KeyExportPolicy Exportable `
    -Subject "CN=yourappname.yourtenant.onmicrosoft.com" `
    -KeyAlgorithm RSA `
    -KeyLength 2048 `
    -KeyUsage DigitalSignature `
    -NotAfter (Get-Date).AddMonths(12) `
    -CertStoreLocation "Cert:\CurrentUser\My"
```


#### <a name="step-2-add-the-id-token-hint-technical-profile"></a>Steg 2. Lägg till den tekniska profilen för ID-token-tips 

Följande tekniska profil verifierar token och extraherar anspråken. Ändra URI för metadata till den välkända konfigurations slut punkten för din token-utfärdare.  

```xml
<ClaimsProvider>
  <DisplayName>My ID Token Hint ClaimsProvider</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="IdTokenHint_ExtractClaims">
      <DisplayName> My ID Token Hint TechnicalProfile</DisplayName>
      <Protocol Name="None" />
      <Metadata>
        <!-- Replace with your endpoint location -->
        <Item Key="METADATA">https://your-app.azurewebsites.net/.well-known/openid-configuration</Item>
        <Item Key="IdTokenAudience">your_optional_audience</Item> -->
        <!-- <Item Key="issuer">your_optional_token_issuer_override</Item> -->
      </Metadata>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="userId" />
      </OutputClaims>
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

#### <a name="step-3-prepare-your-policy"></a>Steg 3. Förbered principen

Slutför steget [Konfigurera ditt policy](#configure-your-policy) .

#### <a name="step-4-prepare-the-code"></a>Steg 4. Förbered koden 

Detta [GitHub-exempel](https://github.com/azure-ad-b2c/id-token-builder) ASP.NET-webbprogram genererar ID-token och är värdar för de metadata-slutpunkter som krävs för att använda parametern "id_token_hint" i Azure AD B2C.


### <a name="configure-your-policy"></a>Konfigurera principen

För både symmetriska och asymmetriska metoder `id_token_hint` anropas den tekniska profilen från ett Dirigerings steg med typ av `GetClaims` och måste ange indatamängden för principen för förlitande part.

1. Lägg till IdTokenHint_ExtractClaims tekniska profilen i tilläggs principen.
1. Lägg till följande Orchestration-steg i din användar resa som det första objektet.  

    ```xml
    <OrchestrationStep Order="1" Type="GetClaims" CpimIssuerTechnicalProfileReferenceId="IdTokenHint_ExtractClaims" />
    ``` 
1. I principen för förlitande part upprepar du samma inloggade anspråk som du konfigurerade i IdTokenHint_ExtractClaims tekniska profilen. Exempel:
    ```xml
   <RelyingParty>
     <DefaultUserJourney ReferenceId="SignUp" />
     <TechnicalProfile Id="PolicyProfile">
       <DisplayName>PolicyProfile</DisplayName>
       <Protocol Name="OpenIdConnect" />
       <InputClaims>
         <InputClaim ClaimTypeReferenceId="email" PartnerClaimType="userId" />
        </InputClaims>
       <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="displayName" />
        <OutputClaim ClaimTypeReferenceId="givenName" />
        <OutputClaim ClaimTypeReferenceId="surname" />
        <OutputClaim ClaimTypeReferenceId="email" />
        <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub"/>
        <OutputClaim ClaimTypeReferenceId="identityProvider" />
       </OutputClaims>
       <SubjectNamingInfo ClaimType="sub" />
      </TechnicalProfile>
    </RelyingParty>
    ```

Beroende på dina affärs behov kan du behöva lägga till token-valideringar, till exempel kontrol lera formatet på e-postadressen. Det gör du genom att lägga till Dirigerings steg som anropar en [teknisk profil för anspråks omvandling](claims-transformation-technical-profile.md). Lägg också till en [självkontrollerad teknisk profil](self-asserted-technical-profile.md) för att presentera ett fel meddelande. 

### <a name="create-and-sign-a-token"></a>Skapa och signera en token

I GitHub-exemplen visas hur du skapar en sådan token som utfärdar en JWT som senare skickades som en frågesträngparametern `id_token_hint` . Följande är ett exempel på en auktoriseringsbegäran med id_token_hint parameter
 
```html
https://tenant-name.b2clogin.com/tenant-name.onmicrosoft.com/B2C_1A_signup_signin/oauth2/v2.0/authorize?client_id=63ba0d17-c4ba-47fd-89e9-31b3c2734339&nonce=defaultNonce&redirect_uri=https%3A%2F%2Fjwt.ms&scope=openid&response_type=id_token&prompt=login&id_token_hint=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJkaXNwbGF5TmFtZSI6IiBKb2huIFNtaXRoIiwidXNlcklkIjoiam9obi5zQGNvbnRvc28uY29tIiwibmJmIjoxNTk5NDgyNTE1LCJleHAiOjE2MDAwODczMTUsImlzcyI6Imh0dHBzOi8vbG9jYWxob3N0IiwiYXVkIjoiYTQ4OWZjNDQtM2NjMC00YTc4LTkyZjYtZTQxM2NkODUzZWFlIn0.nPmLXydI83PQCk5lRBYUZRu_aX58pL1khahHyQuupig
```

## <a name="next-steps"></a>Nästa steg

- Kontrol lera [registreringen med inbjudan via e-post](https://github.com/azure-ad-b2c/samples/blob/master/policies/invite/README.md) på Azure AD B2C community GitHub lagrings platsen.
