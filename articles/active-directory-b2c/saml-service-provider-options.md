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
ms.date: 03/03/2021
ms.author: mimart
ms.subservice: B2C
ms.custom: fasttrack-edit
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: b9a491b639cd1b960ffe3b7164a0940770792148
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/04/2021
ms.locfileid: "102107779"
---
# <a name="options-for-registering-a-saml-application-in-azure-ad-b2c"></a>Alternativ för att registrera ett SAML-program i Azure AD B2C

I den här artikeln beskrivs de konfigurations alternativ som är tillgängliga när du ansluter Azure Active Directory (Azure AD B2C) med ditt SAML-program.

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-user-flow"

[!INCLUDE [active-directory-b2c-limited-to-custom-policy](../../includes/active-directory-b2c-limited-to-custom-policy.md)]

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="encrypted-saml-assertions"></a>Krypterade SAML-kontroller

När ditt program förväntar sig att SAML-intyg ska vara i krypterat format måste du kontrol lera att kryptering är aktiverat i Azure AD B2C principen.

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

Konfigurera signeringsalgoritmen med hjälp av `XmlSignatureAlgorithm` nyckeln metadata i noden RelyingParty metadata.

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

Du kan konfigurera hur lång tid som SAML-svaret ska vara giltigt. Ange livs längden med `TokenLifeTimeInSeconds` metadataobjektet i den tekniska profilen för utfärdare av SAML-token. Det här värdet är antalet sekunder som kan förflyta från `NotBefore` tidsstämpeln som beräknas vid tidpunkten för utfärdande av token. Den tid som har valts för detta är den aktuella tiden. Standard livs längden är 300 sekunder (5 minuter).

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
