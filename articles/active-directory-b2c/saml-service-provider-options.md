---
title: Konfigurera alternativ för SAML Service Provider
title-suffix: Azure Active Directory B2C
description: Så här konfigurerar du Azure Active Directory B2C alternativ för SAML-tjänstprovidern
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/15/2021
ms.author: mimart
ms.subservice: B2C
ms.custom: fasttrack-edit
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 09cfdd026105a34db976118f38b011e2c4578a24
ms.sourcegitcommit: 66ce33826d77416dc2e4ba5447eeb387705a6ae5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/15/2021
ms.locfileid: "103470776"
---
# <a name="options-for-registering-a-saml-application-in-azure-ad-b2c"></a>Alternativ för att registrera ett SAML-program i Azure AD B2C

I den här artikeln beskrivs de konfigurations alternativ som är tillgängliga när du ansluter Azure Active Directory (Azure AD B2C) med ditt SAML-program.

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-user-flow"

[!INCLUDE [active-directory-b2c-limited-to-custom-policy](../../includes/active-directory-b2c-limited-to-custom-policy.md)]

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="encrypted-saml-assertions"></a>Krypterade SAML-kontroller

När ditt program förväntar sig att SAML-intyg ska vara i krypterat format måste du se till att kryptering är aktiverat i principen för Azure AD B2C.

Azure AD B2C använder tjänst leverantörens offentliga nyckel certifikat för att kryptera SAML-kontrollen. Den offentliga nyckeln måste finnas i SAML-programmets metadata-slutpunkt med nyckel beskrivningen "use" inställd på "kryptering", vilket visas i följande exempel:

```xml
<KeyDescriptor use="encryption">
  <KeyInfo xmlns="https://www.w3.org/2000/09/xmldsig#">
    <X509Data>
      <X509Certificate>valid certificate</X509Certificate>
    </X509Data>
  </KeyInfo>
</KeyDescriptor>
```

Om du vill aktivera Azure AD B2C skicka krypterade intyg anger du **WantsEncryptedAssertion** -objektet till `true` i den [förlitande partens tekniska profil](relyingparty.md#technicalprofile). Du kan också konfigurera algoritmen som används för att kryptera SAML-kontrollen.

```xml
<RelyingParty>
  <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
  <TechnicalProfile Id="PolicyProfile">
    <DisplayName>PolicyProfile</DisplayName>
    <Protocol Name="SAML2"/>
    <Metadata>
      <Item Key="WantsEncryptedAssertions">true</Item>
    </Metadata>
   ..
  </TechnicalProfile>
</RelyingParty>
```

### <a name="encryption-method"></a>Krypteringsmetod

Om du vill konfigurera krypterings metoden som används för att kryptera SAML Assertion-data, ställer du in `DataEncryptionMethod` metadata-nyckeln inom den förlitande parten. Möjliga värden är `Aes256` (standard), `Aes192` , `Sha512` eller `Aes128` . Metadata styr värdet för `<EncryptedData>` elementet i SAML-svaret.

Om du vill konfigurera krypterings metoden som används för att kryptera kopian av nyckeln, som användes för att kryptera SAML Assertion-data, ställer du in `KeyEncryptionMethod` metadata-nyckeln inom den förlitande parten. Möjliga värden är `Rsa15` (standard)-RSA-algoritmen (Public Key Cryptography Standard) (PKCS) Version 1,5 och `RsaOaep` -RSA optimal OAEP-krypteringsalgoritm (asymmetrisk kryptering).  Metadata styr värdet för  `<EncryptedKey>` elementet i SAML-svaret.

I följande exempel visas `EncryptedAssertion` avsnittet i en SAML-kontroll. Metoden krypterade data är `Aes128` och den krypterade nyckel metoden `Rsa15` .

```xml
<saml:EncryptedAssertion>
  <xenc:EncryptedData xmlns:xenc="http://www.w3.org/2001/04/xmlenc#"
    xmlns:dsig="http://www.w3.org/2000/09/xmldsig#" Type="http://www.w3.org/2001/04/xmlenc#Element">
    <xenc:EncryptionMethod Algorithm="http://www.w3.org/2001/04/xmlenc#aes128-cbc" />
    <dsig:KeyInfo>
      <xenc:EncryptedKey>
        <xenc:EncryptionMethod Algorithm="http://www.w3.org/2001/04/xmlenc#rsa-1_5" />
        <xenc:CipherData>
          <xenc:CipherValue>...</xenc:CipherValue>
        </xenc:CipherData>
      </xenc:EncryptedKey>
    </dsig:KeyInfo>
    <xenc:CipherData>
      <xenc:CipherValue>...</xenc:CipherValue>
    </xenc:CipherData>
  </xenc:EncryptedData>
</saml:EncryptedAssertion>
```

Du kan ändra formatet på krypterade kontroller. Konfigurera krypterings formatet genom att ange `UseDetachedKeys` metadata-nyckeln inom den förlitande parten. Möjliga värden: `true` , eller `false` (standard). När värdet är inställt på `true` , lägger de frånkopplade nycklarna till den krypterade försäkran som underordnad till i `EncrytedAssertion` stället för `EncryptedData` .

Konfigurera krypterings metoden och-formatet, Använd metadata-nycklarna i den [förlitande partens tekniska profil](relyingparty.md#technicalprofile):

```xml
<RelyingParty>
  <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
  <TechnicalProfile Id="PolicyProfile">
    <DisplayName>PolicyProfile</DisplayName>
    <Protocol Name="SAML2"/>
    <Metadata>
      <Item Key="DataEncryptionMethod">Aes128</Item>
      <Item Key="KeyEncryptionMethod">Rsa15</Item>
      <Item Key="UseDetachedKeys">false</Item>
    </Metadata>
   ..
  </TechnicalProfile>
</RelyingParty>
```

## <a name="identity-provider-initiated-flow"></a>Identity Provider – initierat flöde

När ditt program förväntar sig att ta emot en SAML-kontroll utan att först skicka en SAML authn-begäran till identitets leverantören, måste du konfigurera Azure AD B2C för flödet som initieras av identitets leverantören.

I flödes leverantörens initierade flöde initieras inloggnings processen av identitets leverantören (Azure AD B2C), som skickar ett oönskat SAML-svar till tjänst leverantören (ditt förlitande parts program).

Vi stöder för närvarande inte scenarier där den initierande identitets leverantören är en extern identitetsprovider som är federerad med Azure AD B2C, till exempel [AD-FS](identity-provider-adfs.md)eller [Salesforce](identity-provider-salesforce-saml.md). Det finns endast stöd för autentisering med Azure AD B2C lokala konton.

Om du vill aktivera flöde för initierad identitetsprovider anger du **IdpInitiatedProfileEnabled** -objektet till `true` i den [förlitande partens tekniska profil](relyingparty.md#technicalprofile).

```xml
<RelyingParty>
  <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
  <TechnicalProfile Id="PolicyProfile">
    <DisplayName>PolicyProfile</DisplayName>
    <Protocol Name="SAML2"/>
    <Metadata>
      <Item Key="IdpInitiatedProfileEnabled">true</Item>
    </Metadata>
   ..
  </TechnicalProfile>
</RelyingParty>
```

Om du vill logga in eller registrera en användare via Identity Provider-initierat flöde använder du följande URL:

```
https://<tenant-name>.b2clogin.com/<tenant-name>.onmicrosoft.com/<policy-name>/generic/login?EntityId=app-identifier-uri 
```

Ersätt följande värden:

* **klient organisations** namn med ditt klient namn
* **princip-namn** med ditt SAML-princip namn för förlitande part
* **app-Identifier-URI** med `identifierUris` i metadatafilen, till exempel `https://contoso.onmicrosoft.com/app-name`

### <a name="sample-policy"></a>Exempel-princip

Vi tillhandahåller en komplett exempel princip som du kan använda för testning med SAML test-appen.

1. Hämta [principen för SAML-SP-initierad inloggnings exempel](https://github.com/azure-ad-b2c/saml-sp/tree/master/policy/SAML-SP-Initiated).
1. Uppdatera `TenantId` för att matcha ditt klient namn, till exempel *contoso.b2clogin.com*.
1. Behåll princip namnet *B2C_1A_signup_signin_saml*.

## <a name="saml-response-signature-algorithm"></a>Signeringsalgoritm för SAML-svar

Du kan konfigurera signeringsalgoritmen som används för att signera SAML-försäkran. Möjliga värden är `Sha256` , `Sha384` , `Sha512` eller `Sha1` . Kontrol lera att den tekniska profilen och programmet använder samma signaturalgoritm. Använd bara den algoritm som ditt certifikat stöder.

Konfigurera signeringsalgoritmen med hjälp av `XmlSignatureAlgorithm` metadata-nyckeln inom den förlitande partens metadata-element.

```xml
<RelyingParty>
  <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
  <TechnicalProfile Id="PolicyProfile">
    <DisplayName>PolicyProfile</DisplayName>
    <Protocol Name="SAML2"/>
    <Metadata>
      <Item Key="XmlSignatureAlgorithm">Sha256</Item>
    </Metadata>
   ..
  </TechnicalProfile>
</RelyingParty>
```

## <a name="saml-response-lifetime"></a>Svars tid för SAML-svar

Du kan konfigurera hur lång tid som SAML-svaret ska vara giltigt. Ange livs längden med `TokenLifeTimeInSeconds` metadataobjektet i den tekniska profilen för utfärdare av SAML-token. Det här värdet är antalet sekunder som kan förflyta från `NotBefore` tidsstämpeln som beräknas vid tidpunkten för utfärdande av token. Standard livs längden är 300 sekunder (5 minuter).

```xml
<ClaimsProvider>
  <DisplayName>Token Issuer</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="Saml2AssertionIssuer">
      <DisplayName>Token Issuer</DisplayName>
      <Protocol Name="SAML2"/>
      <OutputTokenFormat>SAML2</OutputTokenFormat>
      <Metadata>
        <Item Key="TokenLifeTimeInSeconds">400</Item>
      </Metadata>
      ...
    </TechnicalProfile>
```

## <a name="saml-response-valid-from-skew"></a>SAML-svar giltigt från skevning

Du kan konfigurera tids skevningen som ska användas för SAML-svarets `NotBefore` tidsstämpel. Den här konfigurationen säkerställer att SAML-kontrollen fortfarande anses vara giltig när tiden mellan två plattformar inte är synkroniserad.

Ange tids skevningen med `TokenNotBeforeSkewInSeconds` metadataobjektet i den tekniska profilen för utfärdare av SAML-token. Värdet skev anges i sekunder, med standardvärdet 0. Det maximala värdet är 3600 (en timme).

Till exempel när `TokenNotBeforeSkewInSeconds` är inställt på `120` sekunder:

- Token utfärdas vid 13:05:10 UTC
- Token är giltig från 13:03:10 UTC

```xml
<ClaimsProvider>
  <DisplayName>Token Issuer</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="Saml2AssertionIssuer">
      <DisplayName>Token Issuer</DisplayName>
      <Protocol Name="SAML2"/>
      <OutputTokenFormat>SAML2</OutputTokenFormat>
      <Metadata>
        <Item Key="TokenNotBeforeSkewInSeconds">120</Item>
      </Metadata>
      ...
    </TechnicalProfile>
```

## <a name="remove-milliseconds-from-date-and-time"></a>Ta bort millisekunder från datum och tid

Du kan ange om millisekunderna ska tas bort från datetime-värden inom SAML-svaret (bland annat IssueInstant, NotBefore, NotOnOrAfter och AuthnInstant). Om du vill ta bort millisekunderna ställer du in `RemoveMillisecondsFromDateTime
` metadata-nyckeln inom den förlitande parten. Möjliga värden: `false` (standard) eller `true` .

```xml
<ClaimsProvider>
  <DisplayName>Token Issuer</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="Saml2AssertionIssuer">
      <DisplayName>Token Issuer</DisplayName>
      <Protocol Name="SAML2"/>
      <OutputTokenFormat>SAML2</OutputTokenFormat>
      <Metadata>
        <Item Key="RemoveMillisecondsFromDateTime">true</Item>
      </Metadata>
      ...
    </TechnicalProfile>
```

## <a name="azure-ad-b2c-issuer-id"></a>Azure AD B2C utfärdar-ID

Om du har flera SAML-program som är beroende av olika `entityID` värden kan du åsidosätta `issueruri` värdet i filen för förlitande part. Om du vill åsidosätta utfärdar-URI: n kopierar du den tekniska profilen med "Saml2AssertionIssuer"-ID: t från bas filen och åsidosätter `issueruri` värdet.

> [!TIP]
> Kopiera `<ClaimsProviders>` avsnittet från basen och behåll dessa element i anspråks leverantören: `<DisplayName>Token Issuer</DisplayName>` , `<TechnicalProfile Id="Saml2AssertionIssuer">` och `<DisplayName>Token Issuer</DisplayName>` .
 
Exempel:

```xml
   <ClaimsProviders>   
    <ClaimsProvider>
      <DisplayName>Token Issuer</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="Saml2AssertionIssuer">
          <DisplayName>Token Issuer</DisplayName>
          <Metadata>
            <Item Key="IssuerUri">customURI</Item>
          </Metadata>
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
  </ClaimsProviders>
  <RelyingParty>
    <DefaultUserJourney ReferenceId="SignUpInSAML" />
    <TechnicalProfile Id="PolicyProfile">
      <DisplayName>PolicyProfile</DisplayName>
      <Protocol Name="SAML2" />
      <Metadata>
     …
```

## <a name="session-management"></a>Sessionshantering

Du kan hantera sessionen mellan Azure AD B2C och SAML-förlitande parts program med hjälp av `UseTechnicalProfileForSessionManagement` elementet och [SamlSSOSessionProvider](custom-policy-reference-sso.md#samlssosessionprovider).

## <a name="force-users-to-re-authenticate"></a>Tvinga användare att autentisera igen 

För att användarna ska kunna autentisera igen kan programmet inkludera `ForceAuthn` attributet i SAML-autentiseringsbegäran. `ForceAuthn`Attributet är ett booleskt värde. När värdet är true, kommer användarsessionen att bli ogiltig vid Azure AD B2C och användaren tvingas att autentisera igen. Följande begäran om SAML-autentisering visar hur du ställer in `ForceAuthn` attributet på True. 


```xml
<samlp:AuthnRequest 
       Destination="https://contoso.b2clogin.com/contoso.onmicrosoft.com/B2C_1A_SAML2_signup_signin/samlp/sso/login"
       ForceAuthn="true" ...>
    ...
</samlp:AuthnRequest>
```

## <a name="debug-the-saml-protocol"></a>Felsöka SAML-protokollet

För att hjälpa till att konfigurera och felsöka integrationen med din tjänst leverantör kan du använda ett webb läsar tillägg för SAML-protokollet, till exempel [SAML DevTools-tillägg](https://chrome.google.com/webstore/detail/saml-devtools-extension/jndllhgbinhiiddokbeoeepbppdnhhio) för Chrome, [SAML-spårningspil](https://addons.mozilla.org/es/firefox/addon/saml-tracer/) för Firefox eller [Edge eller IE Developer Tools](https://techcommunity.microsoft.com/t5/microsoft-sharepoint-blog/gathering-a-saml-token-using-edge-or-ie-developer-tools/ba-p/320957).

Med dessa verktyg kan du kontrol lera integrationen mellan ditt program och Azure AD B2C. Exempel:

* Kontrol lera om SAML-begäran innehåller en signatur och avgör vilken algoritm som används för att logga in auktoriseringsbegäran.
* Kontrol lera om Azure AD B2C returnerar ett fel meddelande.
* Kontrol lera att avsnittet är krypterat.

## <a name="next-steps"></a>Nästa steg

- Hitta mer information om [SAML-protokollet på Oasis-webbplatsen](https://www.oasis-open.org/).
- Hämta webbappen SAML-test från [Azure AD B2C GitHub community lagrings platsen](https://github.com/azure-ad-b2c/saml-sp-tester).

<!-- LINKS - External -->
[samltest]: https://aka.ms/samltestapp

::: zone-end
