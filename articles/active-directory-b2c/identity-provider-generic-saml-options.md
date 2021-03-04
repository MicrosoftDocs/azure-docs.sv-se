---
title: Ange inloggning med alternativ för SAML-identitetsprovider
titleSuffix: Azure Active Directory B2C
description: Konfigurera inloggnings alternativ för SAML Identity Provider (IdP) i Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/03/2021
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 44a084266a083b2cc7c0609e4bfedb10a018585d
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/04/2021
ms.locfileid: "102107811"
---
# <a name="configure-saml-identity-provider-options-with-azure-active-directory-b2c"></a>Konfigurera alternativ för SAML-identitetsprovider med Azure Active Directory B2C

Azure Active Directory B2C (Azure AD B2C) stöder Federation med SAML 2,0 Identity providers. I den här artikeln beskrivs de konfigurations alternativ som är tillgängliga när du aktiverar inloggning med en SAML-identitetsprovider.

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-user-flow"

[!INCLUDE [active-directory-b2c-limited-to-custom-policy](../../includes/active-directory-b2c-limited-to-custom-policy.md)]

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="claims-mapping"></a>Anspråks mappning

**OutputClaims** -elementet innehåller en lista över anspråk som returnerats av SAML Identity Provider. Du måste mappa namnet på det anspråk som definierats i principen till det namn som definierats i identitets leverantören. Kontrol lera identitets leverantören för listan över anspråk (intyg). Du kan också kontrol lera innehållet i SAML-svaret som din identitetsprovider returnerar. Mer information finns i [FELSÖKA SAML-meddelanden](#debug-saml-protocol). Om du vill lägga till ett anspråk måste du först [definiera ett anspråk](claimsschema.md)och sedan lägga till anspråket i samlingen utgående anspråk.

Du kan också inkludera anspråk som inte returneras av identitets leverantören, så länge du ställer in `DefaultValue` attributet. Standardvärdet kan vara statiskt eller dynamiskt, med hjälp av [kontext anspråk](#enable-use-of-context-claims).

Elementet utdata-anspråk innehåller följande attribut:

- **ClaimTypeReferenceId** är referensen till en anspråks typ. 
- **PartnerClaimType** är namnet på den egenskap som visas i Azure Insights. Använd syntaxen `{property:NAME}` där `NAME` är egenskapen som läggs till i händelsen.
- **DefaultValue** är ett fördefinierat värde som ska registreras, t. ex. händelse namn. Ett anspråk som används i användar resan, till exempel namnet på identitets leverantören. Om anspråket är tomt används standardvärdet. `identityProvider`Anspråket anges till exempel av de tekniska profilerna för federationen, t. ex. Facebook. Om anspråket är tomt, anger det att användaren loggar in med ett lokalt konto. Standardvärdet är alltså inställt på *Local*. Du kan också registrera en [anspråks lösare](claim-resolver-overview.md) med ett sammanhangsbaserad värde, till exempel program-ID eller ANVÄNDAREns IP-adress.

### <a name="subject-name"></a>Mottagarnamn

Om du vill läsa SAML Assertion **NameId** i **ämnet** som ett normaliserat anspråk ställer du in anspråks **PartnerClaimType** till värdet för `SPNameQualifier` attributet. Om `SPNameQualifier` attributet inte visas ställer du in anspråkets **PartnerClaimType** värde för `NameQualifier` attributet.

SAML-kontroll:

```xml
<saml:Subject>
  <saml:NameID SPNameQualifier="http://your-idp.com/unique-identifier" Format="urn:oasis:names:tc:SAML:2.0:nameid-format:transient">david@contoso.com</saml:NameID>
  <SubjectConfirmation Method="urn:oasis:names:tc:SAML:2.0:cm:bearer">
    <SubjectConfirmationData InResponseTo="_cd37c3f2-6875-4308-a9db-ce2cf187f4d1" NotOnOrAfter="2020-02-15T16:23:23.137Z" Recipient="https://<your-tenant>.b2clogin.com/<your-tenant>.onmicrosoft.com/B2C_1A_TrustFrameworkBase/samlp/sso/assertionconsumer" />
    </SubjectConfirmation>
  </saml:SubjectConfirmation>
</saml:Subject>
```

Utgående anspråk:

```xml
<OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="http://your-idp.com/unique-identifier" />
```

Om båda `SPNameQualifier` eller `NameQualifier` attributen inte visas i SAML-kontrollen anger du anspråks **PartnerClaimType** till `assertionSubjectName` . Kontrol lera att **NameId** är det första värdet i Assertion XML. När du definierar fler än en kontroll, Azure AD B2C väljer subject-värdet från den senaste försäkran.


## <a name="configure-saml-protocol-bindings"></a>Konfigurera bindningar för SAML-protokoll

SAML-begärandena skickas till identitets leverantören enligt vad som anges i identitets leverantörens metadata- `SingleSignOnService` element. De flesta av identitets leverantörernas autentiseringsbegäranden utförs direkt i URL-frågesträngen för en HTTP GET-begäran (eftersom meddelandena är relativt korta). Information om hur du konfigurerar bindningar för båda SAML-begärandena hittar du i dokumentationen för identitets leverantören.

Följande är ett exempel på en Azure AD-tjänst för metadata för enkel inloggning med två bindningar. `HTTP-Redirect`Prioriteras över `HTTP-POST` eftersom det visas först i SAML Identity Provider metadata.

```xml
<IDPSSODescriptor protocolSupportEnumeration="urn:oasis:names:tc:SAML:2.0:protocol">
  ...
  <SingleSignOnService Binding="urn:oasis:names:tc:SAML:2.0:bindings:HTTP-Redirect" Location="https://login.microsoftonline.com/00000000-0000-0000-0000-000000000000/saml2"/>
  <SingleSignOnService Binding="urn:oasis:names:tc:SAML:2.0:bindings:HTTP-POST" Location="https://login.microsoftonline.com/00000000-0000-0000-0000-000000000000/saml2"/>
</IDPSSODescriptor>
```

SAML-svar överförs till Azure AD B2C via HTTP POST-bindning. Azure AD B2C-principens metadata anger `AssertionConsumerService` bindningen till `urn:oasis:names:tc:SAML:2.0:bindings:HTTP-POST` .

Följande är ett exempel på ett försäkrat konsument tjänst element i en Azure AD B2C princip.

```xml
<SPSSODescriptor AuthnRequestsSigned="true" protocolSupportEnumeration="urn:oasis:names:tc:SAML:2.0:protocol">
  ...
  <AssertionConsumerService Binding="urn:oasis:names:tc:SAML:2.0:bindings:HTTP-POST" Location="https://your-tenant.b2clogin.com/your-tenant/B2C_1A_TrustFrameworkBase/samlp/sso/assertionconsumer" index="0" isDefault="true"/>
</SPSSODescriptor>
```

## <a name="configure-the-saml-request-signature"></a>Konfigurera signaturen för SAML-begäran

Azure AD B2C signerar alla utgående autentiseringsbegäranden med den kryptografiska nyckeln **SamlMessageSigning** . Om du vill inaktivera signaturen för SAML-begäran anger du **WantsSignedRequests** till `false` . Om metadata har angetts till `false` utelämnas parametrarna **SigAlg** och **Signature** (frågesträng eller post parameter) från begäran.

Dessa metadata styr också attributet **AuthnRequestsSigned** , som ingår i metadata för den Azure AD B2C tekniska profil som delas med identitets leverantören. Azure AD B2C signerar inte begäran om värdet för **WantsSignedRequests** i metadata för teknisk profil är inställt på `false` och **WantAuthnRequestsSigned** för identitetsprovider har angetts till `false` eller har inte angetts.

I följande exempel tas signaturen bort från SAML-begäran.

```xml
<Metadata>
  ...
  <Item Key="WantsSignedRequests">false</Item>
</Metadata>
```

### <a name="signature-algorithm"></a>Signaturalgoritm

Azure AD B2C använder `Sha1` för att signera SAML-begäran. Använd **XmlSignatureAlgorithm** metadata för att konfigurera algoritmen som ska användas. Möjliga värden är `Sha256` , `Sha384` , `Sha512` eller `Sha1` (standard). Dessa metadata styr värdet för parametern  **SigAlg** (frågesträng eller post parameter) i SAML-begäran. Se till att du konfigurerar signeringsalgoritmen på båda sidor med samma värde. Använd bara den algoritm som ditt certifikat stöder.

### <a name="include-key-info"></a>Inkludera viktig information

Om identitets leverantören anger att Azure AD B2C bindning är inställt på `HTTP-POST` , Azure AD B2C innehåller signaturen och algoritmen i bröd texten i SAML-begäran. Du kan också konfigurera Azure AD till att inkludera den offentliga nyckeln för certifikatet när bindningen är inställd på `HTTP-POST` . Använd **IncludeKeyInfo** metadata till `true` eller `false` . I följande exempel kommer Azure AD inte att innehålla certifikatets offentliga nyckel.

```xml
<Metadata>
  ...
  <Item Key="IncludeKeyInfo">false</Item>
</Metadata>
```

## <a name="configure-saml-request-name-id"></a>Konfigurera namn-ID för SAML-begäran

SAML Authorization Request- `<NameID>` elementet anger formatet för SAML-namn identifierare. I det här avsnittet beskrivs standard konfigurationen och hur du anpassar namn-ID-elementet.

## <a name="preferred-username"></a>Önskat användar namn

Under en inloggnings användar resa kan ett förlitande part program riktas mot en specifik användare. Med Azure AD B2C kan du skicka ett önskat användar namn till SAML Identity Provider. **InputClaims** -elementet används för att skicka en **NameId** inom **ämnet** för SAML-auktoriseringsbegäran.

Om du vill inkludera ämnes namnets ID i auktoriseringsbegäran lägger du till följande- `<InputClaims>` element omedelbart efter `<CryptographicKeys>` . **PartnerClaimType** måste vara inställt på `subject` .

```xml
<InputClaims>
  <InputClaim ClaimTypeReferenceId="signInName" PartnerClaimType="subject" />
</InputClaims>
```

I det här exemplet skickar Azure AD B2C värdet för **signInName** -anspråket med **NameId** inom **ämnet** för SAML-auktoriseringsbegäran.

```xml
<samlp:AuthnRequest ... >
  ...
  <saml:Subject>
    <saml:NameID>sam@contoso.com</saml:NameID>
  </saml:Subject>
</samlp:AuthnRequest>
```

Du kan använda [kontext anspråk](claim-resolver-overview.md), till exempel `{OIDC:LoginHint}` för att fylla i anspråks värde.

```xml
<Metadata>
  ...
  <Item Key="IncludeClaimResolvingInClaimsHandling">true</Item>
</Metadata>
  ...
<InputClaims>
  <InputClaim ClaimTypeReferenceId="signInName" PartnerClaimType="subject" DefaultValue="{OIDC:LoginHint}" AlwaysUseDefaultValue="true" />
</InputClaims>
```

### <a name="name-id-policy-format"></a>Princip format för namn-ID

Som standard anger SAML Authorization-begäran `urn:oasis:names:tc:SAML:1.1:nameid-format:unspecified` principen. Detta anger att alla typer av identifierare som stöds av identitets leverantören för det begärda ämnet kan användas.

Du kan ändra det här beteendet i dokumentationen för identitets leverantören om vilka namn-ID-principer som stöds. Lägg sedan till `NameIdPolicyFormat` metadata i motsvarande princip format. Exempel:

```xml
<Metadata>
  ...
  <Item Key="NameIdPolicyFormat">urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress</Item>
</Metadata>
```

Följande SAML-auktoriseringsbegäran innehåller namn-ID-principen.

```xml
<samlp:AuthnRequest ... >
  ...
  <samlp:NameIDPolicy Format="urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress" />
</samlp:AuthnRequest>
```

### <a name="allow-creating-new-accounts"></a>Tillåt att nya konton skapas

Om du anger [princip formatet namn-ID](#name-id-policy-format)kan du också ange `AllowCreate` egenskapen för **NameIDPolicy** för att ange om identitets leverantören tillåts att skapa ett nytt konto under inloggnings flödet. Mer information finns i identitets leverantörens dokumentation.

Azure AD B2C utelämnar `AllowCreate` egenskapen som standard. Du kan ändra det här beteendet med hjälp av `NameIdPolicyAllowCreate` metadata. Värdet för dessa metadata är `true` eller `false` .

Följande exempel visar hur du ställer in `AllowCreate` egenskapen för `NameIDPolicy` till `true` .

```xml
<Metadata>
  ...
  <Item Key="NameIdPolicyFormat">urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress</Item>
  <Item Key="NameIdPolicyAllowCreate">true</Item>
</Metadata>
```


Följande exempel visar en auktoriseringsbegäran med **AllowCreate** i **NameIDPolicy** -elementet i auktoriseringsbegäran.


```xml
<samlp:AuthnRequest ... >
  ...
  <samlp:NameIDPolicy 
      Format="urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress" 
      AllowCreate="true" />
</samlp:AuthnRequest>
```

### <a name="include-authentication-context-class-references"></a>Ta med klass referenser för autentiserings kontext

En SAML-auktoriseringsbegäran kan innehålla ett **AuthnContext** -element som anger kontexten för en auktoriseringsbegäran. Elementet kan innehålla en klass referens för en kontext, som talar om för SAML-identitetsprovider vilken autentiseringsmekanism som ska användas för användaren.

Om du vill konfigurera klass referenser för autentiserings kontext lägger du till **IncludeAuthnContextClassReferences** metadata. I värdet anger du en eller flera URI-referenser som identifierar autentiserings kontext klasser. Ange flera URI: er som en kommaavgränsad lista. Information om de **AuthnContextClassRef** -URI: er som stöds finns i dokumentationen för identitets leverantören.

I följande exempel kan användarna logga in med både användar namn och lösen ord och logga in med användar namn och lösen ord via en skyddad session (SSL/TLS).

```xml
<Metadata>
  ...
  <Item Key="IncludeAuthnContextClassReferences">urn:oasis:names:tc:SAML:2.0:ac:classes:Password,urn:oasis:names:tc:SAML:2.0:ac:classes:PasswordProtectedTransport</Item>
</Metadata>
```

Följande SAML-auktoriseringsbegäran innehåller referenser till autentiserings kontext klassen.

```xml
<samlp:AuthnRequest ... >
  ...
  <samlp:RequestedAuthnContext>
    <saml:AuthnContextClassRef>urn:oasis:names:tc:SAML:2.0:ac:classes:Password</saml:AuthnContextClassRef>
    <saml:AuthnContextClassRef>urn:oasis:names:tc:SAML:2.0:ac:classes:PasswordProtectedTransport</saml:AuthnContextClassRef>
  </samlp:RequestedAuthnContext>
</samlp:AuthnRequest>
```

## <a name="include-custom-data-in-the-authorization-request"></a>Inkludera anpassade data i auktoriseringsbegäran

Du kan också ta med protokoll meddelande tilläggs element som överenskommits av både Azure AD BC och din identitets leverantör. Tillägget visas i XML-format. Du inkluderar tilläggs element genom att lägga till XML-data i CDATA-elementet `<![CDATA[Your IDP metadata]]>` . Se efter i identitets leverantörens dokumentation om tillägg elementet stöds.

I följande exempel visas hur du använder tilläggs data:

```xml
<Metadata>
  ...
  <Item Key="AuthenticationRequestExtensions"><![CDATA[
            <ext:MyCustom xmlns:ext="urn:ext:custom">
              <ext:AssuranceLevel>1</ext:AssuranceLevel>
              <ext:AssuranceDescription>Identity verified to level 1.</ext:AssuranceDescription>
            </ext:MyCustom>]]></Item>
</Metadata>
```

När du använder meddelande tillägget SAML-protokoll, ser SAML-svaret ut som i följande exempel:

```xml
<samlp:AuthnRequest ... >
  ...
  <samlp:Extensions>
    <ext:MyCustom xmlns:ext="urn:ext:custom">
      <ext:AssuranceLevel>1</ext:AssuranceLevel>
      <ext:AssuranceDescription>Identity verified to level 1.</ext:AssuranceDescription>
    </ext:MyCustom>
  </samlp:Extensions>
</samlp:AuthnRequest>
```

## <a name="require-signed-saml-responses"></a>Kräv signerade SAML-svar

Azure AD B2C kräver att alla inkommande intyg signeras. Du kan ta bort det här kravet genom att ange **WantsSignedAssertions** till `false` . Identitets leverantören får inte signera intygen i det här fallet, men även om den gör det kan Azure AD B2C inte validera signaturen.

Metadata för **WantsSignedAssertions** styr SAML-metadata flaggan **WantAssertionsSigned**, som ingår i metadata för den Azure AD B2C tekniska profil som delas med identitets leverantören.

```xml
<SPSSODescriptor AuthnRequestsSigned="true" WantAssertionsSigned="true" protocolSupportEnumeration="urn:oasis:names:tc:SAML:2.0:protocol">
```

Om du inaktiverar validerings verifieringen kanske du också vill inaktivera verifiering av svars meddelandets signatur. Ange **ResponsesSigned** metadata till `false` . Identitets leverantören får inte signera SAML-svarsmeddelandet i detta fall, men även om det gör det kan Azure AD B2C inte validera signaturen.

I följande exempel tas både meddelandet och kontroll signaturen bort:

```xml
<Metadata>
  ...
  <Item Key="WantsSignedAssertions">false</Item>
  <Item Key="ResponsesSigned">false</Item>
</Metadata>
```

## <a name="require-encrypted-saml-responses"></a>Kräv krypterade SAML-svar

Om du vill kräva att alla inkommande intyg ska vara krypterade anger du **WantsEncryptedAssertions** metadata. När kryptering krävs använder identitets leverantören en offentlig nyckel för ett krypterings certifikat i en Azure AD B2C teknisk profil. Azure AD B2C dekrypterar svars försäkran med hjälp av den privata delen av krypterings certifikatet.

Om du aktiverar bekräftelse kryptering kan du också behöva inaktivera verifiering av svars signatur (mer information finns i [Kräv signerade SAML-svar](#require-signed-saml-responses).

När **WantsEncryptedAssertions** metadata är inställt på `true` , innehåller metadata för den Azure AD B2C tekniska profilen **krypterings** avsnittet. Identitets leverantören läser metadata och krypterar SAML-svars kontrollen med den offentliga nyckel som anges i metadata för den Azure AD B2C tekniska profilen.

I följande exempel visas avsnittet Key Descriptor i SAML-metadata som används för kryptering:

```xml
<SPSSODescriptor AuthnRequestsSigned="true"  protocolSupportEnumeration="urn:oasis:names:tc:SAML:2.0:protocol">
  <KeyDescriptor use="encryption">
    <KeyInfo xmlns="https://www.w3.org/2000/09/xmldsig#">
      <X509Data>
        <X509Certificate>valid certificate</X509Certificate>
      </X509Data>
    </KeyInfo>
  </KeyDescriptor>
  ...
</SPSSODescriptor>
```

Kryptera SAML-svars kontroll:

1. [Skapa en princip nyckel](identity-provider-generic-saml.md#create-a-policy-key) med en unik identifierare. Till exempel `B2C_1A_SAMLEncryptionCert`.
2. I din SAML Technical Profile **CryptographicKeys** -samling. Ange **StorageReferenceId** till namnet på den princip nyckel som du skapade i det första steget. `SamlAssertionDecryption`ID: t anger användningen av den kryptografiska nyckeln för att kryptera och dekryptera uppslaget av SAML-svaret.

    ```xml
    <CryptographicKeys>
            ...
      <Key Id="SamlAssertionDecryption" StorageReferenceId="B2C_1A_SAMLEncryptionCert"/>
    </CryptographicKeys>
    ```

3. Ange **WantsEncryptedAssertions** för den tekniska profilen till `true` .
    
    ```xml
    <Metadata>
      ...
      <Item Key="WantsEncryptedAssertions">true</Item>
    </Metadata>
    ```

4. Uppdatera din identitets leverantör med de nya metadata för Azure AD B2C teknisk profil. Du bör se nyckel **beskrivningen** med egenskapen **use** som `encryption` innehåller den offentliga nyckeln för ditt certifikat.

## <a name="enable-use-of-context-claims"></a>Aktivera användning av kontext anspråk

I insamlingen av indata och utgående anspråk kan du inkludera anspråk som inte returneras av identitets leverantören så länge du ställer in `DefaultValue` attributet. Du kan också använda [kontext anspråk](claim-resolver-overview.md) som ska ingå i den tekniska profilen. Så här använder du ett kontext anspråk:

1. Lägg till en anspråks typ i [ClaimsSchema](claimsschema.md) -elementet i [BuildingBlocks](buildingblocks.md).
2. Lägg till ett utgående anspråk i indata-eller utdata-samlingen. I följande exempel anger det första anspråket värdet för identitets leverantören. Det andra anspråket använder anspråk för användar-IP- [kontext](claim-resolver-overview.md).
    
    ```xml
    <OutputClaims>
      <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="contoso.com" AlwaysUseDefaultValue="true" />
      <OutputClaim ClaimTypeReferenceId="IpAddress" DefaultValue="{Context:IPAddress}" AlwaysUseDefaultValue="true" />
    </OutputClaims>
    ```

## <a name="disable-single-logout"></a>Inaktivera enkel utloggning

Vid en programbegäran om utloggning Azure AD B2C försök att logga ut från SAML Identity Provider. Mer information finns i [Azure AD B2C-sessionen logga ut](session-behavior.md#sign-out).  Om du vill inaktivera beteendet för enkel utloggning ställer du in **SingleLogoutEnabled** metadata på `false` .

```xml
<Metadata>
  ...
  <Item Key="SingleLogoutEnabled">false</Item>
</Metadata>
```

## <a name="debug-saml-protocol"></a>Felsöka SAML-protokoll

Om du vill konfigurera och felsöka Federation med en SAML-identitetsprovider kan du använda ett webb läsar tillägg för SAML-protokollet, till exempel [SAML DevTools-tillägg](https://chrome.google.com/webstore/detail/saml-devtools-extension/jndllhgbinhiiddokbeoeepbppdnhhio) för Chrome, [SAML-spårningspil](https://addons.mozilla.org/es/firefox/addon/saml-tracer/) för Firefox eller [Edge eller IE Developer Tools](https://techcommunity.microsoft.com/t5/microsoft-sharepoint-blog/gathering-a-saml-token-using-edge-or-ie-developer-tools/ba-p/320957).

Med hjälp av dessa verktyg kan du kontrol lera integrationen mellan Azure AD B2C och din SAML Identity Provider. Exempel:

* Kontrol lera om SAML-begäran innehåller en signatur och avgör vilken algoritm som används för att logga in auktoriseringsbegäran.
* Hämta anspråk (intyg) under `AttributeStatement` avsnittet.
* Kontrol lera om identitets leverantören returnerar ett fel meddelande.
* Kontrol lera om kontroll avsnittet är krypterat.

## <a name="next-steps"></a>Nästa steg

- Lär dig hur du diagnostiserar problem med anpassade principer med hjälp av [Application Insights](troubleshoot-with-application-insights.md). 

::: zone-end
