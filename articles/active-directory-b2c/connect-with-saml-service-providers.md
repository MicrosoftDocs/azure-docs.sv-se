---
title: Konfigurera Azure AD B2C som en SAML-IdP till dina program
title-suffix: Azure AD B2C
description: Så här konfigurerar du Azure AD B2C att tillhandahålla SAML-protokoll kontroller till dina program (tjänst leverantörer). Azure AD B2C fungerar som en IdP (Single Identity Provider) till ditt SAML-program.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 05/18/2020
ms.author: mimart
ms.subservice: B2C
ms.custom: fasttrack-edit
ms.openlocfilehash: 2bf767bd87e0df791b0efff1294f15353234ba2c
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88520217"
---
# <a name="register-a-saml-application-in-azure-ad-b2c"></a>Registrera ett SAML-program i Azure AD B2C

I den här artikeln får du lära dig hur du konfigurerar Azure Active Directory B2C (Azure AD B2C) att fungera som en Security Assertion Markup Language (SAML) identitets leverantör (IdP) till dina program.

## <a name="scenario-overview"></a>Översikt över scenario

Organisationer som använder Azure AD B2C som sin kund identitets-och åtkomst hanterings lösning kan kräva interaktion med identitets leverantörer eller program som är konfigurerade att autentiseras med hjälp av SAML-protokollet.

Azure AD B2C uppnår SAML-samverkan på något av två sätt:

* Genom att agera som *identitets leverantör* (IdP) och tillhandahålla enkel inloggning (SSO) med SAML-baserade tjänst leverantörer (dina program)
* Genom att agera som en *tjänst leverantör* (SP) och INTERAGERA med SAML-baserade identitets leverantörer som Salesforce och ADFS

![Diagram med B2C som identitets leverantör till vänster och B2C som tjänst leverantör till höger](media/saml-identity-provider/saml-idp-diagram-01.jpg)

Sammanfatta de två icke-exklusiva huvud scenarierna med SAML:

| Scenario | Azure AD B2C roll | Så här gör du |
| -------- | ----------------- | ------- |
| Mitt program förväntar sig en SAML-kontroll för att slutföra en autentisering. | **Azure AD B2C fungerar som identitets leverantör (IdP)**<br />Azure AD B2C fungerar som en SAML-IdP i programmen. | Den här artikeln. |
| Mina användare behöver en enkel inloggning med en SAML-kompatibel identitets leverantör som ADFS, Salesforce eller Shibboleth.  | **Azure AD B2C fungerar som tjänst leverantör (SP)**<br />Azure AD B2C fungerar som en tjänst leverantör vid anslutning till SAML Identity Provider. Det är en Federations-proxy mellan ditt program och SAML Identity Provider.  | <ul><li>[Konfigurera inloggning med ADFS som ett SAML-IdP med anpassade principer](identity-provider-adfs2016-custom.md)</li><li>[Konfigurera inloggning med en Salesforce-SAML-Provider med anpassade principer](identity-provider-salesforce-custom.md)</li></ul> |

## <a name="prerequisites"></a>Förutsättningar

* Slutför stegen i [Kom igång med anpassade principer i Azure AD B2C](custom-policy-get-started.md). Du behöver den anpassade principen *SocialAndLocalAccounts* från start paketet för anpassad princip som beskrivs i artikeln.
* Grundläggande förståelse för Security Assertion Markup Language-protokollet (SAML).
* Ett webb program som har kon figurer ATS som en SAML-tjänstleverantör (SP). I den här självstudien kan du använda ett [SAML-testprogram][samltest] som vi tillhandahåller.

## <a name="components-of-the-solution"></a>Komponenter i lösningen

Det finns tre huvud komponenter som krävs för det här scenariot:

* SAML **-** tjänstprovider med möjlighet att skicka SAML-begäranden och ta emot, avkoda och svara på SAML-kontroller från Azure AD B2C. Detta kallas även för den förlitande parten.
* Offentligt tillgänglig **slut punkt** för SAML-metadata för din tjänst leverantör.
* [Azure AD B2C klient](tutorial-create-tenant.md)

Om du ännu inte har en SAML-tjänstleverantör och en associerad slut punkt för metadata kan du använda det här exempel på SAML-program som vi har gjort tillgängliga för testning:

[SAML-testprogram][samltest]

## <a name="1-set-up-certificates"></a>1. Konfigurera certifikat

Om du vill skapa en förtroende relation mellan din tjänst leverantör och Azure AD B2C måste du tillhandahålla X509-certifikaten för webbapp.

* **Service Provider-certifikat**
  * Certifikat med en privat nyckel lagrad i din webbapp. Det här certifikatet används av tjänst leverantören för att signera SAML-begäran som skickats till Azure AD B2C. Azure AD B2C läser den offentliga nyckeln från tjänst leverantörens metadata för att verifiera signaturen.
  * Valfritt Certifikat med en privat nyckel lagrad i din webbapp. Azure AD B2C läser den offentliga nyckeln från tjänst leverantörens metadata för att kryptera SAML-försäkran. Tjänste leverantören använder sedan den privata nyckeln för att dekryptera försäkran.
* **Azure AD B2C certifikat**
  * Certifikat med en privat nyckel i Azure AD B2C. Det här certifikatet används av Azure AD B2C för att signera SAML-svaret som skickas till din tjänst leverantör. Leverantören läser Azure AD B2C offentliga nyckel för metadata för att verifiera signaturen för SAML-svaret.

Du kan använda ett certifikat utfärdat av en offentlig certifikat utfärdare eller, för den här självstudien, ett självsignerat certifikat.

### <a name="11-prepare-a-self-signed-certificate"></a>1,1 förbereda ett självsignerat certifikat

Om du inte redan har ett certifikat kan du använda ett självsignerat certifikat för den här självstudien. I Windows kan du använda PowerShell: s [New-SelfSignedCertificate-](https://docs.microsoft.com/powershell/module/pkiclient/new-selfsignedcertificate) cmdlet för att skapa ett certifikat.

1. Kör PowerShell-kommandot för att generera ett självsignerat certifikat. Ändra `-Subject` argumentet efter behov för ditt program och Azure AD B2C klient namn. Du kan också justera `-NotAfter` datumet för att ange ett annat förfallo datum för certifikatet.

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

1. Öppna **hantera användar certifikat**  >  **aktuella användare**  >  **personliga**  >  **certifikat**  >  *yourappname.yourtenant.onmicrosoft.com*
1. Välj åtgärden för certifikat > **åtgärden**  >  **alla aktiviteter**  >  **Exportera**
1. Välj **Ja**  >  **Nästa**  >  **Ja, exportera den privata nyckeln**  >  **Nästa**
1. Acceptera standardinställningarna för **export fil format**
1. Ange ett lösen ord för certifikatet

### <a name="12-upload-the-certificate"></a>1,2 Ladda upp certifikatet

Sedan laddar du upp certifikatet för SAML Assertion och svars signering till Azure AD B2C.

1. Logga in på [Azure Portal](https://portal.azure.com) och bläddra till Azure AD B2C klienten.
1. Under **principer**väljer du **identitets miljö ramverk** och sedan **princip nycklar**.
1. Välj **Lägg till**och välj sedan **alternativ**  >  **Ladda upp**.
1. Ange ett **namn**, till exempel *SamlIdpCert*. Prefixet *B2C_1A_* läggs automatiskt till i namnet på din nyckel.
1. Ladda upp certifikatet med hjälp av upload File-kontrollen.
1. Ange certifikatets lösen ord.
1. Välj **Skapa**.
1. Kontrol lera att nyckeln visas som förväntat. Till exempel *B2C_1A_SamlIdpCert*.

## <a name="2-prepare-your-policy"></a>2. Förbered principen

### <a name="21-create-the-saml-token-issuer"></a>2,1 Skapa SAML-token utfärdare

Nu kan du lägga till kapaciteten för din klient organisation för att utfärda SAML-token med hjälp av [SAML-token utfärdare](saml-issuer-technical-profile.md) och tekniska profiler för [SAML-session](custom-policy-reference-sso.md#samlssosessionprovider) .

Öppna `SocialAndLocalAccounts\` **`TrustFrameworkExtensions.xml`** i Start paketet för den anpassade principen.

Leta upp `<ClaimsProviders>` avsnittet och Lägg till följande XML-kodfragment.

Du kan ändra värdet för `IssuerUri` metadata. Detta är utfärdar-URI: n som returneras i SAML-svaret från Azure AD B2C. Ditt förlitande parts program ska konfigureras för att godkänna en utfärdare-URI under verifieringen av SAML-kontroll.

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
        <!-- The issuer contains the policy name; it should be the same name as configured in the relying party application. B2C_1A_signup_signin_SAML is used below. -->
        <!--<Item Key="IssuerUri">https://tenant-name.b2clogin.com/tenant-name.onmicrosoft.com/B2C_1A_signup_signin_SAML</Item>-->
      </Metadata>
      <CryptographicKeys>
        <Key Id="MetadataSigning" StorageReferenceId="B2C_1A_SamlIdpCert"/>
        <Key Id="SamlAssertionSigning" StorageReferenceId="B2C_1A_SamlIdpCert"/>
        <Key Id="SamlMessageSigning" StorageReferenceId="B2C_1A_SamlIdpCert"/>
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

## <a name="3-add-the-saml-relying-party-policy"></a>3. Lägg till principen för SAML-förlitande part

Nu när din klient organisation kan utfärda SAML-intyg måste du skapa principen för SAML-förlitande part och ändra användar resan så att den utfärdar en SAML-kontroll i stället för ett JWT.

### <a name="31-create-sign-up-or-sign-in-policy"></a>3,1 Skapa registrerings-eller inloggnings princip

1. Skapa en kopia av *SignUpOrSignin.xml* -filen i arbets katalogen för ditt Start paket och spara den med ett nytt namn. Till exempel *SignUpOrSigninSAML.xml*. Detta är den förlitande partens princip fil.

1. Öppna *SignUpOrSigninSAML.xml* -filen i önskat redigerings program.

1. Ändra `PolicyId` och `PublicPolicyUri` för principen till _B2C_1A_signup_signin_saml_ och `http://tenant-name.onmicrosoft.com/B2C_1A_signup_signin_saml` som visas nedan.

    ```xml
    <TrustFrameworkPolicy
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xmlns:xsd="http://www.w3.org/2001/XMLSchema"
    xmlns="http://schemas.microsoft.com/online/cpim/schemas/2013/06"
    PolicySchemaVersion="0.3.0.0"
    TenantId="tenant-name.onmicrosoft.com"
    PolicyId="B2C_1A_signup_signin_saml"
    PublicPolicyUri="http://tenant-name.onmicrosoft.com/B2C_1A_signup_signin_saml">
    ```

1. Lägg till följande XML-kodfragment precis före `<RelyingParty>` elementet. Denna XML skriver över Orchestration-steg 7 i _SignUpOrSignIn_ -användar resan. Om du startade från en annan mapp i Start paketet, eller anpassat din användar resa genom att lägga till eller ta bort Orchestration-steg, kontrollerar du att numret (i `order` -elementet) är justerat med det som anges i användar resan för steget token Issuer (till exempel i de andra startpaket-mapparna det är steg 4 för `LocalAccounts` , 6 för `SocialAccounts` och 9 för `SocialAndLocalAccountsWithMfa` ).

    ```xml
    <UserJourneys>
      <UserJourney Id="SignUpOrSignIn">
        <OrchestrationSteps>
          <OrchestrationStep Order="7" Type="SendClaims" CpimIssuerTechnicalProfileReferenceId="Saml2AssertionIssuer"/>
        </OrchestrationSteps>
      </UserJourney>
    </UserJourneys>
    ```

1. Ersätt hela `<TechnicalProfile>` elementet i `<RelyingParty>` elementet med följande tekniska profil-XML.

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

1. Uppdatera `tenant-name` med namnet på din Azure AD B2C-klient.

Den slutgiltiga förlitande partens princip fil bör se ut så här:

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

### <a name="32-upload-and-test-your-policy-metadata"></a>3,2 Ladda upp och testa dina princip-metadata

Spara ändringarna och ladda upp den nya princip filen. När du har laddat upp båda principerna (tillägget och förlitande part-filerna) öppnar du en webbläsare och navigerar till principens metadata.

Azure AD B2Cs princip IDP metadata är information som används i SAML-protokollet för att exponera konfigurationen för en SAML-identitetsprovider. Metadata definierar platsen för tjänsterna, till exempel inloggning och utloggning, certifikat, inloggnings metod med mera. Metadata för Azure AD B2Cs principen är tillgängliga på följande URL. Ersätt `tenant-name` med namnet på din Azure AD B2C-klient och `policy-name` med namnet (ID) för principen:

`https://tenant-name.b2clogin.com/tenant-name.onmicrosoft.com/policy-name/Samlp/metadata`

Din anpassade princip och Azure AD B2C klient är nu klara. Skapa sedan en program registrering i Azure AD B2C.

## <a name="4-setup-application-in-the-azure-ad-b2c-directory"></a>4. installations programmet i katalogen Azure AD B2C

### <a name="41-register-your-application-in-azure-ad-b2c"></a>4,1 registrera ditt program i Azure AD B2C

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Välj filtret **katalog + prenumeration** på den översta menyn och välj sedan den katalog som innehåller Azure AD B2C klienten.
1. På den vänstra menyn väljer du **Azure AD B2C**. Eller Välj **alla tjänster** och Sök efter och välj **Azure AD B2C**.
1. Välj **Appregistreringar**och välj sedan **ny registrering**.
1. Ange ett **namn** för programmet. Till exempel *SAMLApp1*.
1. Under **konto typer som stöds**väljer du **konton endast i den här organisations katalogen**
1. Under **omdirigerings-URI**väljer du **webb**och anger sedan `https://localhost` . Du ändrar det här värdet senare i program registreringens manifest.
1. Välj **Registrera**.

### <a name="42-update-the-app-manifest"></a>4,2 uppdatera app-manifestet

För SAML-appar finns det flera egenskaper som du måste konfigurera i program registreringens manifest.

1. I [Azure Portal](https://portal.azure.com)navigerar du till den program registrering som du skapade i föregående avsnitt.
1. Under **Hantera**väljer du **manifest** för att öppna manifest redigeraren. Du ändrar flera egenskaper i följande avsnitt.

#### <a name="identifieruris"></a>identifierUris

`identifierUris`Är en sträng samling som innehåller användardefinierade URI: er som unikt identifierar en webbapp inom den Azure AD B2C klienten. Tjänste leverantören måste ange det här värdet i `Issuer` elementet i en SAML-begäran.

#### <a name="samlmetadataurl"></a>samlMetadataUrl

Den här egenskapen representerar tjänst leverantörens allmänt tillgängliga metadata-URL. URL: en för metadata kan peka på en metadatafil som överförts till en anonymt tillgänglig slut punkt, till exempel Blob Storage.

Metadata är information som används i SAML-protokollet för att exponera konfigurationen av en SAML-part, till exempel en tjänst leverantör. Metadata definierar var tjänsterna finns, t. ex. inloggning och utloggning, certifikat, inloggnings metod med mera. Azure AD B2C läser metadata för tjänste leverantören och fungerar enligt detta. Metadata krävs inte. Du kan också ange vissa attribut, t. ex. svars-URI och utloggnings-URI direkt i app-manifestet.

Om det finns egenskaper som anges *i URL: en för SAML* -metadata och i program registreringens manifest, **slås de samman**. Egenskaperna som anges i URL: en för metadata bearbetas först och prioriteras.

I den här självstudien, som använder SAML-testprogrammet, använder du följande värde för `samlMetadataUrl` :

```json
"samlMetadataUrl":"https://samltestapp2.azurewebsites.net/Metadata",
```

#### <a name="replyurlswithtype-optional"></a>replyUrlsWithType (valfritt)

Om du inte anger en URI för metadata kan du uttryckligen ange svars-URL: en. Denna valfria egenskap representerar `AssertionConsumerServiceUrl` ( `SingleSignOnService` URL: en i tjänste leverantörens metadata) och `BindingType` antas vara `HTTP POST` .

Om du väljer att konfigurera svars-URL: en och en utloggnings-URL i applikations manifestet utan att använda metadata för tjänste leverantören, verifierar Azure AD B2C inte signaturen för SAML-begäran eller kryptera SAML-svaret.

I den här självstudien, där du använder SAML-testprogrammet, anger du `url` egenskapen `replyUrlsWithType` till värdet som visas i följande JSON-kodfragment.

```json
"replyUrlsWithType":[
  {
    "url":"https://samltestapp2.azurewebsites.net/SP/AssertionConsumer",
    "type":"Web"
  }
],
```

#### <a name="logouturl-optional"></a>logoutUrl (valfritt)

Den här valfria egenskapen representerar `Logout` URL: en ( `SingleLogoutService` URL i den förlitande partens metadata) och `BindingType` för detta antas som `Http-Redirect` .

I den här självstudien, som använder SAML-testprogrammet, lämnar `logoutUrl` du in inställt på `https://samltestapp2.azurewebsites.net/logout` :

```json
"logoutUrl": "https://samltestapp2.azurewebsites.net/logout",
```

## <a name="5-update-your-application-code"></a>5. uppdatera din program kod

Det sista steget är att aktivera Azure AD B2C som en SAML-IdP i ditt SAML-förlitande parts program. Varje program är olika och stegen för att göra det varierar. Mer information finns i dokumentationen till appen.

En eller flera av följande är vanligt vis obligatoriska:

* **Metadata**: `https://tenant-name.b2clogin.com/tenant-name.onmicrosoft.com/policy-name/Samlp/metadata`
* **Utfärdare**: Använd entityId i metadatafilen
* **Inloggnings webb adress/SAML-slut punkt/SAML-URL**: kontrol lera värdet i metadatafilen
* **Certifikat**: det här är *B2C_1A_SamlIdpCert*, men utan den privata nyckeln. Så här hämtar du den offentliga nyckeln för certifikatet:

    1. Gå till URL: en för metadata som anges ovan.
    1. Kopiera värdet i `<X509Certificate>` elementet.
    1. Klistra in den i en textfil.
    1. Spara text filen som en *CER* -fil.

### <a name="51-test-with-the-saml-test-app-optional"></a>5,1-test med SAML-testappen (valfritt)

För att slutföra den här självstudien med vårt [SAML-testprogram][samltest]:

* Uppdatera klient organisations namnet
* Uppdaterings princip namn, till exempel *B2C_1A_signup_signin_saml*
* Ange denna utfärdar-URI: `https://contoso.onmicrosoft.com/app-name`

Välj **Logga in** och visa en användar inloggnings skärm. Vid inloggning utfärdas en SAML-kontroll tillbaka till exempel programmet.

## <a name="enable-encypted-assertions"></a>Aktivera krypterat-intyg
Om du vill kryptera SAML-kontroller som skickas tillbaka till tjänst leverantören använder Azure AD B2C tjänst leverantörens offentliga nyckel certifikat. Den offentliga nyckeln måste finnas i SAML-metadata som anges i ovanstående ["samlMetadataUrl"](#samlmetadataurl) som en nyckel beskrivning med användning av "kryptering".

Följande är ett exempel på en nyckel beskrivning för SAML-metadata med en användnings uppsättning som kryptering:

```xml
<KeyDescriptor use="encryption">
  <KeyInfo xmlns="https://www.w3.org/2000/09/xmldsig#">
    <X509Data>
      <X509Certificate>valid certificate</X509Certificate>
    </X509Data>
  </KeyInfo>
</KeyDescriptor>
```

Om du vill aktivera Azure AD B2C för att skicka krypterade intyg anger du **WantsEncryptedAssertion** till sant i den förlitande partens tekniska profil som visas nedan.

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
 ..
 ..
  <RelyingParty>
    <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
    <TechnicalProfile Id="PolicyProfile">
      <DisplayName>PolicyProfile</DisplayName>
      <Protocol Name="SAML2"/>
      <Metadata>
          <Item Key="WantsEncryptedAssertions">true</Item>
      </Metadata>
     ..
     ..
     ..
    </TechnicalProfile>
  </RelyingParty>
</TrustFrameworkPolicy>
```

## <a name="sample-policy"></a>Exempel-princip

Vi tillhandahåller en komplett exempel princip som du kan använda för testning med SAML test-appen.

1. Hämta [principen för SAML-SP-initierad inloggnings exempel](https://github.com/azure-ad-b2c/saml-sp/tree/master/policy/SAML-SP-Initiated)
1. Uppdatera `TenantId` för att matcha ditt klient namn, till exempel *contoso.b2clogin.com*
1. Behåll princip namnet för *B2C_1A_SAML2_signup_signin*

## <a name="supported-and-unsupported-saml-modalities"></a>SAML-regler som stöds och inte stöds

Följande scenarier för SAML-förlitande part (RP) stöds via din egen metadata-slutpunkt:

* Flera URL: er för utloggning eller PUBLICERINGs bindning för utloggnings-URL i program-/tjänst huvud objekt.
* Ange signerings nyckel för att verifiera RP-begäranden i program/tjänstens huvud namns objekt.
* Ange token krypterings nyckel i program/tjänstens huvud namns objekt.
* Identitets leverantören initierade inloggningen, där identitetsprovider är Azure AD B2C.

Följande scenarier för SAML-förlitande part (RP) stöds inte för närvarande:
* Identitetsprovider initierade inloggning, där identitets leverantören är en extern identitetsprovider, till exempel ADFS.

## <a name="next-steps"></a>Nästa steg

- Du hittar mer information om SAML- [protokollet på Oasis-webbplatsen](https://www.oasis-open.org/).
- Hämta webbappen SAML-test från [Azure AD B2C GitHub community lagrings platsen](https://github.com/azure-ad-b2c/saml-sp-tester).

<!-- LINKS - External -->
[samltest]: https://aka.ms/samltestapp
