---
title: Konfigurera Azure Active Directory B2C som en SAML-IdP till dina program
title-suffix: Azure Active Directory B2C
description: Så här konfigurerar du Azure Active Directory B2C att tillhandahålla SAML-protokoll kontroller till dina program (tjänst leverantörer). Azure AD B2C fungerar som en IdP (Single Identity Provider) till ditt SAML-program.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/03/2021
ms.author: mimart
ms.subservice: B2C
ms.custom: fasttrack-edit
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 1035f43642f3884e7cc0f6ab47e9c9afd1f29170
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/04/2021
ms.locfileid: "102107771"
---
# <a name="register-a-saml-application-in-azure-ad-b2c"></a>Registrera ett SAML-program i Azure AD B2C

I den här artikeln får du lära dig hur du ansluter dina Security Assertion Markup Language (SAML)-program (tjänst leverantörer) till Azure Active Directory B2C (Azure AD B2C) för autentisering.

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-user-flow"

[!INCLUDE [active-directory-b2c-limited-to-custom-policy](../../includes/active-directory-b2c-limited-to-custom-policy.md)]

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="overview"></a>Översikt

Organisationer som använder Azure AD B2C som sin kund identitets-och åtkomst hanterings lösning kan kräva integrering med program som autentiserar med hjälp av SAML-protokollet. Följande diagram visar hur Azure AD B2C fungerar som en *identitets leverantör* (IdP) för att få enkel inloggning (SSO) med SAML-baserade program.

![Diagram med B2C som identitets leverantör till vänster och B2C som tjänst leverantör till höger.](media/saml-service-provider/saml-service-provider-integration.png)

1. Programmet skapar en SAML authn-begäran som skickas till Azure AD-B2C's SAML-inloggnings slut punkt.
2. Användaren kan använda ett Azure AD B2C lokalt konto eller någon annan federerad identitets leverantör (om det är konfigurerat) för att autentisera.
3. Om användaren loggar in med en federerad identitets leverantör skickas ett token-svar till Azure AD B2C.
4. Azure AD B2C genererar en SAML-kontroll och skickar den till programmet.

## <a name="prerequisites"></a>Förutsättningar

* Slutför stegen i [Kom igång med anpassade principer i Azure AD B2C](custom-policy-get-started.md). Du behöver den anpassade principen *SocialAndLocalAccounts* från start paketet för anpassad princip som beskrivs i artikeln.
* Grundläggande förståelse för SAML-protokollet och förtrogenhet med programmets SAML-implementering.
* Ett webb program som kon figurer ATS som ett SAML-program. I den här självstudien kan du använda ett [SAML-testprogram][samltest] som vi tillhandahåller.

## <a name="components"></a>Komponenter

Det finns tre huvud komponenter som krävs för det här scenariot:

* Ett SAML- **program** med möjlighet att skicka SAML authn-begäranden och ta emot, avkoda och verifiera SAML-svar från Azure AD B2C. SAML-programmet kallas även för den förlitande part applikationen eller tjänst leverantören.
* SAML-programmets offentligt tillgängliga SAML **metadata-slutpunkt** eller XML-dokument.
* En [Azure AD B2C klient](tutorial-create-tenant.md)

Om du ännu inte har ett SAML-program och en associerad metadata-slutpunkt kan du använda det här exempel på SAML-program som vi har gjort tillgängliga för testning:

[SAML-testprogram][samltest]

## <a name="set-up-certificates"></a>Konfigurera certifikat

För att skapa en förtroende relation mellan ditt program och Azure AD B2C måste båda tjänsterna kunna skapa och verifiera var and ras signaturer. Du konfigurerar en konfigurera X509-certifikat i Azure AD B2C och ditt program.

**Program certifikat**

| Användning | Krävs | Beskrivning |
| --------- | -------- | ----------- |
| Signering av SAML-begäran  | Inga | Ett certifikat med en privat nyckel lagrad i din webbapp som används av ditt program för att signera SAML-begäranden som skickas till Azure AD B2C. Webb programmet måste exponera den offentliga nyckeln via slut punkten för SAML-metadata. Azure AD B2C validerar signaturen för SAML-begäran med hjälp av den offentliga nyckeln från programmets metadata.|
| Kryptering för SAML-kontroll  | Inga | Ett certifikat med en privat nyckel lagrad i din webbapp. Webb programmet måste exponera den offentliga nyckeln via slut punkten för SAML-metadata. Azure AD B2C kan kryptera kontroller till ditt program med hjälp av den offentliga nyckeln. Programmet använder den privata nyckeln för att dekryptera försäkran.|

**Azure AD B2C certifikat**

| Användning | Krävs | Beskrivning |
| --------- | -------- | ----------- |
| Signering av SAML-svar | Ja | Ett certifikat med en privat nyckel lagrad i Azure AD B2C. Det här certifikatet används av Azure AD B2C för att signera SAML-svaret som skickas till ditt program. Programmet läser Azure AD B2C offentliga nyckel för metadata för att verifiera signaturen för SAML-svaret. |

I en produktions miljö rekommenderar vi att du använder certifikat som utfärdats av en offentlig certifikat utfärdare. Du kan dock också slutföra den här proceduren med självsignerade certifikat.

### <a name="prepare-a-self-signed-certificate-for-saml-response-signing"></a>Förbereda ett självsignerat certifikat för SAML-svars signering

Du måste skapa ett signerings certifikat för SAML-signering så att ditt program kan lita på att certifikatet är betrott från Azure AD B2C.

[!INCLUDE [active-directory-b2c-create-self-signed-certificate](../../includes/active-directory-b2c-create-self-signed-certificate.md)]

## <a name="enable-your-policy-to-connect-with-a-saml-application"></a>Aktivera principen för att ansluta till ett SAML-program

För att kunna ansluta till ditt SAML-program måste Azure AD B2C kunna skapa SAML-svar.

Öppna `SocialAndLocalAccounts\` **`TrustFrameworkExtensions.xml`** i Start paketet för den anpassade principen.

Leta upp `<ClaimsProviders>` avsnittet och Lägg till följande XML-kodfragment för att implementera din SAML-svars Generator.

```xml
<ClaimsProvider>
  <DisplayName>Token Issuer</DisplayName>
  <TechnicalProfiles>

    <!-- SAML Token Issuer technical profile -->
    <TechnicalProfile Id="Saml2AssertionIssuer">
      <DisplayName>Token Issuer</DisplayName>
      <Protocol Name="SAML2"/>
      <OutputTokenFormat>SAML2</OutputTokenFormat>
      <Metadata>
        <Item Key="IssuerUri">https://issuerUriMyAppExpects</Item>
      </Metadata>
      <CryptographicKeys>
        <Key Id="SamlAssertionSigning" StorageReferenceId="B2C_1A_SamlIdpCert"/>
      </CryptographicKeys>
      <InputClaims/>
      <OutputClaims/>
      <UseTechnicalProfileForSessionManagement ReferenceId="SM-Saml-issuer"/>
    </TechnicalProfile>

    <!-- Session management technical profile for SAML based tokens -->
    <TechnicalProfile Id="SM-Saml-issuer">
      <DisplayName>Session Management Provider</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.SamlSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null"/>
    </TechnicalProfile>

  </TechnicalProfiles>
</ClaimsProvider>
```

#### <a name="configure-the-issueruri-of-the-saml-response"></a>Konfigurera IssuerUri för SAML-svaret

Du kan ändra värdet för `IssuerUri` metadataobjektet i den tekniska profilen för utfärdare av SAML-token. Den här ändringen visas i det `issuerUri` attribut som returneras i SAML-svaret från Azure AD B2C. Ditt program måste konfigureras att acceptera samma `issuerUri` vid verifiering av SAML-svar.

```xml
<ClaimsProvider>
  <DisplayName>Token Issuer</DisplayName>
  <TechnicalProfiles>
    <!-- SAML Token Issuer technical profile -->
    <TechnicalProfile Id="Saml2AssertionIssuer">
      <DisplayName>Token Issuer</DisplayName>
      <Protocol Name="SAML2"/>
      <OutputTokenFormat>SAML2</OutputTokenFormat>
      <Metadata>
        <Item Key="IssuerUri">https://issuerUriMyAppExpects</Item>
      </Metadata>
      ...
    </TechnicalProfile>
```

#### <a name="sign-the-azure-ad-b2c-idp-saml-metadata-optional"></a>Signera Azure AD B2C SAML-metadata för IdP (valfritt)

Du kan instruera Azure AD B2C att signera SAML IdP metadata-dokumentet, om det krävs av programmet. Det gör du genom att generera och ladda upp en SAML-IdP metadata signerings princip nyckel som visas i [förbereda ett självsignerat certifikat för SAML-svars signering](#prepare-a-self-signed-certificate-for-saml-response-signing). Konfigurera sedan `MetadataSigning` metadataobjektet i den tekniska profilen för utfärdare av SAML-token. `StorageReferenceId`Måste referera till princip nyckel namnet.

```xml
<ClaimsProvider>
  <DisplayName>Token Issuer</DisplayName>
  <TechnicalProfiles>
    <!-- SAML Token Issuer technical profile -->
    <TechnicalProfile Id="Saml2AssertionIssuer">
      <DisplayName>Token Issuer</DisplayName>
      <Protocol Name="SAML2"/>
      <OutputTokenFormat>SAML2</OutputTokenFormat>
        ...
      <CryptographicKeys>
        <Key Id="MetadataSigning" StorageReferenceId="B2C_1A_SamlMetadataCert"/>
        ...
      </CryptographicKeys>
    ...
    </TechnicalProfile>
```

#### <a name="sign-the-azure-ad-b2c-idp-saml-response-element-optional"></a>Signera Azure AD B2C IdP SAML Response element (valfritt)

Du kan ange ett certifikat som ska användas för att signera SAML-meddelanden. Meddelandet är det `<samlp:Response>` element i SAML-svaret som skickas till programmet.

Om du vill ange ett certifikat genererar och laddar du upp en princip nyckel som visas i [förbereda ett självsignerat certifikat för signering av SAML-svar](#prepare-a-self-signed-certificate-for-saml-response-signing). Konfigurera sedan `SamlMessageSigning` metadataobjektet i den tekniska profilen för utfärdare av SAML-token. `StorageReferenceId`Måste referera till princip nyckel namnet.

```xml
<ClaimsProvider>
  <DisplayName>Token Issuer</DisplayName>
  <TechnicalProfiles>
    <!-- SAML Token Issuer technical profile -->
    <TechnicalProfile Id="Saml2AssertionIssuer">
      <DisplayName>Token Issuer</DisplayName>
      <Protocol Name="SAML2"/>
      <OutputTokenFormat>SAML2</OutputTokenFormat>
        ...
      <CryptographicKeys>
        <Key Id="SamlMessageSigning" StorageReferenceId="B2C_1A_SamlMessageCert"/>
        ...
      </CryptographicKeys>
    ...
    </TechnicalProfile>
```
## <a name="configure-your-policy-to-issue-a-saml-response"></a>Konfigurera principen för att utfärda ett SAML-svar

Nu när principen kan skapa SAML-svar måste du konfigurera principen för att utfärda ett SAML-svar i stället för standard-JWT-svaret till ditt program.

### <a name="create-a-sign-up-or-sign-in-policy-configured-for-saml"></a>Skapa en registrerings-eller inloggnings princip som kon figurer ATS för SAML

1. Skapa en kopia av *SignUpOrSignin.xml* -filen i arbets katalogen för ditt Start paket och spara den med ett nytt namn. Till exempel *SignUpOrSigninSAML.xml*. Den här filen är den förlitande part princip filen och är konfigurerad att utfärda ett JWT-svar som standard.

1. Öppna *SignUpOrSigninSAML.xml* -filen i önskat redigerings program.

1. Ändra `PolicyId` och `PublicPolicyUri` för principen till _B2C_1A_signup_signin_saml_ och `http://<tenant-name>.onmicrosoft.com/B2C_1A_signup_signin_saml` som visas nedan.

    ```xml
    <TrustFrameworkPolicy
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xmlns:xsd="http://www.w3.org/2001/XMLSchema"
    xmlns="http://schemas.microsoft.com/online/cpim/schemas/2013/06"
    PolicySchemaVersion="0.3.0.0"
    TenantId="tenant-name.onmicrosoft.com"
    PolicyId="B2C_1A_signup_signin_saml"
    PublicPolicyUri="http://<tenant-name>.onmicrosoft.com/B2C_1A_signup_signin_saml">
    ```

1. I slutet av användar resan innehåller Azure AD B2C ett `SendClaims` steg. Det här steget hänvisar till den tekniska profilen för token-utfärdaren. Om du vill utfärda ett SAML-svar i stället för standard-JWT-svaret ändrar du `SendClaims` steget för att referera till den nya tekniska profilen för SAML-token-utfärdaren `Saml2AssertionIssuer` .

Lägg till följande XML-kodfragment precis före `<RelyingParty>` elementet. Denna XML skriver över Orchestration-steg 7 i _SignUpOrSignIn_ -användar resan. Om du startade från en annan mapp i Start paketet eller du anpassade användar resan genom att lägga till eller ta bort Orchestration-steg, kontrollerar du att antalet i `order` elementet motsvarar det nummer som anges i användar resan för steget token Issuer. I de andra startmapparna för startpaket är till exempel motsvarande steg 4 för `LocalAccounts` , 6 för `SocialAccounts` och 9 för `SocialAndLocalAccountsWithMfa` ).

```xml
<UserJourneys>
  <UserJourney Id="SignUpOrSignIn">
    <OrchestrationSteps>
      <OrchestrationStep Order="7" Type="SendClaims" CpimIssuerTechnicalProfileReferenceId="Saml2AssertionIssuer"/>
    </OrchestrationSteps>
  </UserJourney>
</UserJourneys>
```

Elementet för förlitande part avgör vilket protokoll som används i programmet. Standardvärdet är `OpenId`. `Protocol`Elementet måste ändras till `SAML` . De utgående anspråken skapar anspråks mappningen till SAML-kontrollen.

Ersätt hela `<TechnicalProfile>` elementet i `<RelyingParty>` elementet med följande tekniska profil-XML. Uppdatera `tenant-name` med namnet på din Azure AD B2C-klient.

```xml
    <TechnicalProfile Id="PolicyProfile">
      <DisplayName>PolicyProfile</DisplayName>
      <Protocol Name="SAML2"/>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="displayName" />
        <OutputClaim ClaimTypeReferenceId="givenName" />
        <OutputClaim ClaimTypeReferenceId="surname" />
        <OutputClaim ClaimTypeReferenceId="email" DefaultValue="" />
        <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="" />
        <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="objectId"/>
      </OutputClaims>
      <SubjectNamingInfo ClaimType="objectId" ExcludeAsClaim="true"/>
    </TechnicalProfile>
```

Den slutgiltiga förlitande partens princip fil bör se ut som följande XML-kod:

```xml
<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<TrustFrameworkPolicy
  xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xmlns:xsd="http://www.w3.org/2001/XMLSchema"
  xmlns="http://schemas.microsoft.com/online/cpim/schemas/2013/06"
  PolicySchemaVersion="0.3.0.0"
  TenantId="contoso.onmicrosoft.com"
  PolicyId="B2C_1A_signup_signin_saml"
  PublicPolicyUri="http://contoso.onmicrosoft.com/B2C_1A_signup_signin_saml">

  <BasePolicy>
    <TenantId>contoso.onmicrosoft.com</TenantId>
    <PolicyId>B2C_1A_TrustFrameworkExtensions</PolicyId>
  </BasePolicy>

  <UserJourneys>
    <UserJourney Id="SignUpOrSignIn">
      <OrchestrationSteps>
        <OrchestrationStep Order="7" Type="SendClaims" CpimIssuerTechnicalProfileReferenceId="Saml2AssertionIssuer"/>
      </OrchestrationSteps>
    </UserJourney>
  </UserJourneys>

  <RelyingParty>
    <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
    <TechnicalProfile Id="PolicyProfile">
      <DisplayName>PolicyProfile</DisplayName>
      <Protocol Name="SAML2"/>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="displayName" />
        <OutputClaim ClaimTypeReferenceId="givenName" />
        <OutputClaim ClaimTypeReferenceId="surname" />
        <OutputClaim ClaimTypeReferenceId="email" DefaultValue="" />
        <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="" />
        <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="objectId"/>
      </OutputClaims>
      <SubjectNamingInfo ClaimType="objectId" ExcludeAsClaim="true"/>
    </TechnicalProfile>
  </RelyingParty>
</TrustFrameworkPolicy>
```

> [!NOTE]
> Du kan följa samma process för att implementera andra typer av användar flöden (t. ex. inloggning, lösen ords återställning eller profil redigerings flöden).

### <a name="upload-your-policy"></a>Överför principen

Spara ändringarna och överför de nya **TrustFrameworkExtensions.xml** och **SignUpOrSigninSAML.xml** principfiler till Azure Portal.

### <a name="test-the-azure-ad-b2c-idp-saml-metadata"></a>Testa Azure AD B2C IdP SAML-metadata

När principfiler har överförts använder Azure AD B2C konfigurations informationen för att generera identitets leverantörens SAML-dokumentmetadata som ska användas av programmet. SAML-Metadatadokumentet innehåller tjänsternas platser, till exempel inloggnings-och utloggnings metoder, certifikat och så vidare.

Metadata för Azure AD B2Cs principen är tillgängliga på följande URL:

`https://<tenant-name>.b2clogin.com/<tenant-name>.onmicrosoft.com/<policy-name>/samlp/metadata`

Ersätt `<tenant-name>` med namnet på din Azure AD B2C klient och `<policy-name>` med namnet (ID) för principen, till exempel:

`https://contoso.b2clogin.com/contoso.onmicrosoft.com/B2C_1A_signup_signin_saml/samlp/metadata`

## <a name="register-your-saml-application-in-azure-ad-b2c"></a>Registrera ditt SAML-program i Azure AD B2C

För att Azure AD B2C att lita på ditt program, skapar du en Azure AD B2C program registrering som innehåller konfigurations information som programmets slut punkt för metadata.

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Välj filtret **katalog + prenumeration** på den översta menyn och välj sedan den katalog som innehåller Azure AD B2C klienten.
1. På den vänstra menyn väljer du **Azure AD B2C**. Eller Välj **alla tjänster** och Sök efter och välj **Azure AD B2C**.
1. Välj **Appregistreringar** och välj sedan **ny registrering**.
1. Ange ett **namn** för programmet. Till exempel *SAMLApp1*.
1. Under **konto typer som stöds** väljer du **konton endast i den här organisations katalogen**.
1. Under **omdirigerings-URI** väljer du **webb** och anger sedan `https://localhost` . Du kommer att ändra det här värdet senare i program registreringens manifest.
1. Välj **Register** (Registrera).

### <a name="configure-your-application-in-azure-ad-b2c"></a>Konfigurera ditt program i Azure AD B2C

För SAML-appar måste du konfigurera flera egenskaper i program registreringens manifest.

1. I [Azure Portal](https://portal.azure.com)navigerar du till den program registrering som du skapade i föregående avsnitt.
1. Under **Hantera** väljer du **manifest** för att öppna manifest redigeraren och ändrar sedan de egenskaper som beskrivs i följande avsnitt.

#### <a name="add-the-identifier"></a>Lägg till identifieraren

När SAML-programmet gör en begäran till Azure AD B2C, innehåller SAML authn-begäran ett `Issuer` attribut, vilket vanligt vis är samma värde som programmets metadata `entityID` . Azure AD B2C använder det här värdet för att leta upp program registreringen i katalogen och läsa konfigurationen. För att sökningen ska lyckas `identifierUri` måste i program registreringen fyllas i med ett värde som matchar `Issuer` attributet.

I registrerings manifestet letar du upp `identifierURIs` parametern och lägger till lämpligt värde. Det här värdet kommer att vara samma värde som har kon figurer ATS i SAML authn-begärandena för EntityId vid programmet och `entityID` värdet i programmets metadata.

I följande exempel visas `entityID` i SAML-metadata:

```xml
<EntityDescriptor ID="id123456789" entityID="https://samltestapp2.azurewebsites.net" validUntil="2099-12-31T23:59:59Z" xmlns="urn:oasis:names:tc:SAML:2.0:metadata">
```

`identifierUris`Egenskapen accepterar bara URL: er på domänen `tenant-name.onmicrosoft.com` .

```json
"identifierUris":"https://samltestapp2.azurewebsites.net",
```

#### <a name="share-the-applications-metadata-with-azure-ad-b2c"></a>Dela programmets metadata med Azure AD B2C

När program registreringen har lästs in `identifierUri` , använder Azure AD B2C programmets metadata för att verifiera SAML authn-begäran och bestämma hur de ska svara.

Vi rekommenderar att ditt program exponerar en offentligt tillgänglig metadata-slutpunkt.

Om det finns egenskaper som anges *i URL: en för SAML* -metadata och program registreringens manifest, *slås de samman*. Egenskaperna som anges i URL: en för metadata bearbetas först och prioriteras.

Med SAML-testprogrammet som exempel använder du följande värde för `samlMetadataUrl` i applikations manifestet:

```json
"samlMetadataUrl":"https://samltestapp2.azurewebsites.net/Metadata",
```

#### <a name="override-or-set-the-assertion-consumer-url-optional"></a>Åsidosätt eller ange försäkrans konsument-URL (valfritt)

Du kan konfigurera svars-URL: en till vilken Azure AD B2C skickar SAML-svar. Svars-URL: er kan konfigureras i applikations manifestet. Den här konfigurationen är användbar när programmet inte exponerar en offentligt tillgänglig metadata-slutpunkt.

Svars-URL: en för ett SAML-program är den slut punkt som programmet förväntar sig att ta emot SAML-svar. Programmet tillhandahåller vanligt vis denna URL i metadata-dokumentet under `AssertionConsumerServiceUrl` attributet, som du ser nedan:

```xml
<SPSSODescriptor AuthnRequestsSigned="false" WantAssertionsSigned="false" protocolSupportEnumeration="urn:oasis:names:tc:SAML:2.0:protocol">
    ...
    <AssertionConsumerService index="0" isDefault="true" Binding="urn:oasis:names:tc:SAML:2.0:bindings:HTTP-POST" Location="https://samltestapp2.azurewebsites.net/SP/AssertionConsumer" />        
</SPSSODescriptor>
```

Om du vill åsidosätta de metadata som anges i `AssertionConsumerServiceUrl` attributet eller om URL: en inte finns i Metadatadokumentet kan du konfigurera URL: en i manifestet under `replyUrlsWithType` egenskapen. `BindingType`Ställs in på `HTTP POST` .

Med hjälp av SAML-testprogrammet som ett exempel anger du `url` egenskapen `replyUrlsWithType` till värdet som visas i följande JSON-kodfragment.

```json
"replyUrlsWithType":[
  {
    "url":"https://samltestapp2.azurewebsites.net/SP/AssertionConsumer",
    "type":"Web"
  }
],
```

#### <a name="override-or-set-the-logout-url-optional"></a>Åsidosätt eller ange URL för utloggning (valfritt)

Du kan konfigurera den utloggnings-URL som Azure AD B2C ska skicka användaren efter en utloggnings förfrågan. Svars-URL: er kan konfigureras i applikations manifestet.

Om du vill åsidosätta de metadata som anges i `SingleLogoutService` attributet eller om URL: en inte finns i Metadatadokumentet kan du konfigurera det i manifestet under `Logout` egenskapen. `BindingType`Ställs in på `Http-Redirect` .

Programmet tillhandahåller vanligt vis denna URL i metadata-dokumentet under `AssertionConsumerServiceUrl` attributet, som du ser nedan:

```xml
<IDPSSODescriptor WantAuthnRequestsSigned="false" WantAssertionsSigned="false" protocolSupportEnumeration="urn:oasis:names:tc:SAML:2.0:protocol">
    <SingleLogoutService Binding="urn:oasis:names:tc:SAML:2.0:bindings:HTTP-Redirect" Location="https://samltestapp2.azurewebsites.net/logout" ResponseLocation="https://samltestapp2.azurewebsites.net/logout" />

</IDPSSODescriptor>
```

Använd SAML-testprogrammet som ett exempel, lämna `logoutUrl` inställningen till `https://samltestapp2.azurewebsites.net/logout` :

```json
"logoutUrl": "https://samltestapp2.azurewebsites.net/logout",
```

> [!NOTE]
> Om du väljer att konfigurera svars-URL: en och en utloggnings-URL i applikations manifestet utan att fylla i programmets metadata-slutpunkt via `samlMetadataUrl` egenskapen, kommer Azure AD B2C inte att validera signaturen för SAML-begäran, eller kommer inte att kryptera SAML-svaret.

## <a name="configure-azure-ad-b2c-as-a-saml-idp-in-your-saml-application"></a>Konfigurera Azure AD B2C som en SAML-IdP i ditt SAML-program

Det sista steget är att aktivera Azure AD B2C som en SAML-IdP i ditt SAML-program. Varje program är olika och stegen varierar. Mer information finns i dokumentationen till appen.

Metadata kan konfigureras i ditt program som *statiska metadata* eller *dynamiska metadata*. I statiskt läge kopierar du alla eller delar av metadata från Azure AD B2C-principens metadata. I dynamiskt läge anger du URL: en för metadata och för att tillåta att ditt program kan läsa metadata dynamiskt.

En eller flera av följande är vanligt vis obligatoriska:

* **Metadata**: Använd formatet `https://<tenant-name>.b2clogin.com/<tenant-name>.onmicrosoft.com/<policy-name>/Samlp/metadata` .
* **Utfärdare**: SAML-begärans `issuer` värde måste matcha en av de URI: er som kon figurer ATS i `identifierUris` elementet i manifestet för program registrering. Om SAML-frågans `issuer` namn inte finns i `identifierUris` elementet, [lägger du till det i manifestet för program registreringen](#add-the-identifier). Till exempel `https://contoso.onmicrosoft.com/app-name`. 
* **Inloggnings webb adress/SAML-slut punkt/SAML-URL**: kontrol lera värdet i Azure AD B2C SAML-principens metadatafil för `<SingleSignOnService>` XML-elementet.
* **Certifikat**: det här certifikatet är *B2C_1A_SamlIdpCert*, men utan den privata nyckeln. Så här hämtar du den offentliga nyckeln för certifikatet:

    1. Gå till URL: en för metadata som anges ovan.
    1. Kopiera värdet i `<X509Certificate>` elementet.
    1. Klistra in den i en textfil.
    1. Spara text filen som en *CER* -fil.

### <a name="test-with-the-saml-test-app"></a>Testa med SAML test-appen

Du kan använda vårt [SAML-testprogram][samltest] för att testa konfigurationen:

* Uppdatera klient organisationens namn.
* Uppdatera princip namnet, till exempel *B2C_1A_signup_signin_saml*.
* Ange den här utfärdar-URI: n. Använd en av de URI: er som finns i `identifierUris` elementet i program registrerings manifestet, till exempel `https://contoso.onmicrosoft.com/app-name` .

Välj **Logga in** och visa en användar inloggnings skärm. Vid inloggning utfärdas ett SAML-svar tillbaka till exempel programmet.

## <a name="supported-and-unsupported-saml-modalities"></a>SAML-regler som stöds och inte stöds

Följande scenarier för SAML-program stöds via din egen metadata-slutpunkt:

* Flera utloggnings-URL: er eller POST bindning för utloggnings-URL i objektet Application/Service huvud namn.
* Ange en signerings nyckel för att verifiera förlitande part (RP)-begär anden i objektet program/tjänst objekt.
* Ange en krypterings nyckel för token i objektet program/tjänstens huvud namn.
* Identitets leverantör – initierad inloggning, där identitets leverantören är Azure AD B2C.

## <a name="next-steps"></a>Nästa steg

- Hämta webbappen SAML-test från [Azure AD B2C GitHub community lagrings platsen](https://github.com/azure-ad-b2c/saml-sp-tester).
- Se [alternativen för att registrera ett SAML-program i Azure AD B2C](saml-service-provider-options.md)

<!-- LINKS - External -->
[samltest]: https://aka.ms/samltestapp

::: zone-end
