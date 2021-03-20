---
title: SAML-protokoll för enkel inloggning i Azure
titleSuffix: Microsoft identity platform
description: I den här artikeln beskrivs SAML-protokollet Single Sign-On (SSO) i Azure Active Directory
services: active-directory
documentationcenter: .net
author: kenwith
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 05/18/2020
ms.author: kenwith
ms.custom: aaddev
ms.reviewer: paulgarn
ms.openlocfilehash: 40bf202e0f14f18d817e4e918f8372ba3c0a4ad8
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "91950677"
---
# <a name="single-sign-on-saml-protocol"></a>SAML-protokoll för enkel inloggning

Den här artikeln beskriver SAML 2,0-autentiseringsbegäranden och svar som Azure Active Directory (Azure AD) stöder för enskilda Sign-On (SSO).

I protokoll diagrammet nedan beskrivs en sekvens med enkel inloggning. Moln tjänsten (tjänst leverantören) använder en HTTP-omdirigerings-bindning för att skicka ett `AuthnRequest` (Authentication Request)-element till Azure AD (identitets leverantören). Azure AD använder sedan en HTTP post-bindning för att publicera ett `Response` element i moln tjänsten.

![Single Sign-On-arbetsflöde (SSO)](./media/single-sign-on-saml-protocol/active-directory-saml-single-sign-on-workflow.png)

> [!NOTE]
> I den här artikeln beskrivs hur du använder SAML för enkel inloggning. Mer information om andra sätt att hantera enkel inloggning (till exempel genom att använda OpenID Connect eller integrerad Windows-autentisering) finns i [enkel inloggning till program i Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="authnrequest"></a>AuthnRequest

Om du vill begära en användarautentisering skickar Cloud Services ett- `AuthnRequest` element till Azure AD. Ett exempel på SAML 2,0 `AuthnRequest` kan se ut som i följande exempel:

```
<samlp:AuthnRequest
xmlns="urn:oasis:names:tc:SAML:2.0:metadata"
ID="id6c1c178c166d486687be4aaf5e482730"
Version="2.0" IssueInstant="2013-03-18T03:28:54.1839884Z"
xmlns:samlp="urn:oasis:names:tc:SAML:2.0:protocol">
<Issuer xmlns="urn:oasis:names:tc:SAML:2.0:assertion">https://www.contoso.com</Issuer>
</samlp:AuthnRequest>
```

| Parameter | Typ | Description |
| --- | --- | --- |
| ID | Obligatorisk | Azure AD använder det här attributet för att fylla `InResponseTo` attributet för det returnerade svaret. ID får inte börja med en siffra, så en gemensam strategi är att lägga en sträng som "ID" till sträng representationen av ett GUID. Till exempel `id6c1c178c166d486687be4aaf5e482730` är ett giltigt ID. |
| Version | Obligatorisk | Den här parametern ska vara inställd på **2,0**. |
| IssueInstant | Obligatorisk | Detta är en DateTime-sträng med ett UTC-värde och [tur och retur-format ("o")](/dotnet/standard/base-types/standard-date-and-time-format-strings). Azure AD förväntar sig ett DateTime-värde av den här typen, men utvärderar eller använder inte värdet. |
| AssertionConsumerServiceURL | Valfritt | Om den här parametern anges måste den matcha `RedirectUri` moln tjänsten i Azure AD. |
| ForceAuthn | Valfritt | Detta är ett booleskt värde. Om värdet är true innebär det att användaren tvingas att autentiseras på nytt, även om de har en giltig session med Azure AD. |
| IsPassive | Valfritt | Detta är ett booleskt värde som anger om Azure AD ska autentisera användaren tyst, utan användar interaktion, med hjälp av sessionens cookie om en sådan finns. Om detta är sant försöker Azure AD autentisera användaren med hjälp av sessions-cookien. |

Alla andra `AuthnRequest` attribut, till exempel medgivande, destination, AssertionConsumerServiceIndex, AttributeConsumerServiceIndex och ProviderName **ignoreras**.

Azure AD ignorerar också `Conditions` elementet i `AuthnRequest` .

### <a name="issuer"></a>Utfärdare

`Issuer`Elementet i en `AuthnRequest` måste exakt matcha ett av **ServicePrincipalNames** i moln tjänsten i Azure AD. Detta är vanligt vis inställt på **app-ID-URI: n** som anges vid program registrering.

Ett SAML-utdrag som innehåller `Issuer` elementet ser ut som i följande exempel:

```
<Issuer xmlns="urn:oasis:names:tc:SAML:2.0:assertion">https://www.contoso.com</Issuer>
```

### <a name="nameidpolicy"></a>NameIDPolicy

Det här elementet kräver ett visst namn-ID-format i svaret och är valfritt i `AuthnRequest` element som skickas till Azure AD.

Ett `NameIdPolicy` element ser ut som i följande exempel:

```
<NameIDPolicy Format="urn:oasis:names:tc:SAML:2.0:nameid-format:persistent"/>
```

Om `NameIDPolicy` har angetts kan du inkludera dess valfria `Format` attribut. `Format`Attributet får bara ha ett av följande värden: andra värden resulterar i ett fel.

* `urn:oasis:names:tc:SAML:2.0:nameid-format:persistent`: Azure Active Directory utfärdar NameID-anspråket som en-identifierare.
* `urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress`: Azure Active Directory utfärdar NameID-anspråket i e-postadressen.
* `urn:oasis:names:tc:SAML:1.1:nameid-format:unspecified`: Det här värdet tillåter att Azure Active Directory väljer anspråks formatet. Azure Active Directory utfärdar NameID som en identifierare.
* `urn:oasis:names:tc:SAML:2.0:nameid-format:transient`: Azure Active Directory utfärdar NameID-anspråket som ett slumpmässigt genererat värde som är unikt för den aktuella SSO-åtgärden. Det innebär att värdet är tillfälligt och inte kan användas för att identifiera den autentiserande användaren.

Om `SPNameQualifier` har angetts kommer Azure AD att inkludera samma `SPNameQualifier` i svaret.

Azure AD ignorerar `AllowCreate` attributet.

### <a name="requestauthncontext"></a>RequestAuthnContext
`RequestedAuthnContext`Elementet anger önskade autentiseringsmetoder. Det är valfritt i `AuthnRequest` element som skickas till Azure AD. Azure AD stöder `AuthnContextClassRef` värden som `urn:oasis:names:tc:SAML:2.0:ac:classes:Password` .

### <a name="scoping"></a>Omfångsdefinierande
`Scoping`Elementet, som innehåller en lista över identitets leverantörer, är valfritt i `AuthnRequest` element som skickas till Azure AD.

Inkludera inte `ProxyCount` attributet `IDPListOption` eller `RequesterID` elementet eftersom de inte stöds.

### <a name="signature"></a>Signatur
Ett `Signature` element i `AuthnRequest` element är valfritt. Azure AD validerar inte signerade autentiseringsbegäranden om det finns en signatur. Verifiering av begär Ande är avsedd för genom att bara besvara registrerade konsument tjänst webb adresser.

### <a name="subject"></a>Ämne
Ta inte med ett- `Subject` element. Azure AD har inte stöd för att ange ett ämne för en begäran och returnerar ett fel om ett sådant anges.

## <a name="response"></a>Svarsåtgärder
När en begärd inloggning har slutförts skickar Azure AD ett svar till moln tjänsten. Ett svar på ett lyckat inloggnings försök ser ut som i följande exempel:

```
<samlp:Response ID="_a4958bfd-e107-4e67-b06d-0d85ade2e76a" Version="2.0" IssueInstant="2013-03-18T07:38:15.144Z" Destination="https://contoso.com/identity/inboundsso.aspx" InResponseTo="id758d0ef385634593a77bdf7e632984b6" xmlns:samlp="urn:oasis:names:tc:SAML:2.0:protocol">
  <Issuer xmlns="urn:oasis:names:tc:SAML:2.0:assertion"> https://login.microsoftonline.com/82869000-6ad1-48f0-8171-272ed18796e9/</Issuer>
  <ds:Signature xmlns:ds="https://www.w3.org/2000/09/xmldsig#">
    ...
  </ds:Signature>
  <samlp:Status>
    <samlp:StatusCode Value="urn:oasis:names:tc:SAML:2.0:status:Success" />
  </samlp:Status>
  <Assertion ID="_bf9c623d-cc20-407a-9a59-c2d0aee84d12" IssueInstant="2013-03-18T07:38:15.144Z" Version="2.0" xmlns="urn:oasis:names:tc:SAML:2.0:assertion">
    <Issuer>https://login.microsoftonline.com/82869000-6ad1-48f0-8171-272ed18796e9/</Issuer>
    <ds:Signature xmlns:ds="https://www.w3.org/2000/09/xmldsig#">
      ...
    </ds:Signature>
    <Subject>
      <NameID>Uz2Pqz1X7pxe4XLWxV9KJQ+n59d573SepSAkuYKSde8=</NameID>
      <SubjectConfirmation Method="urn:oasis:names:tc:SAML:2.0:cm:bearer">
        <SubjectConfirmationData InResponseTo="id758d0ef385634593a77bdf7e632984b6" NotOnOrAfter="2013-03-18T07:43:15.144Z" Recipient="https://contoso.com/identity/inboundsso.aspx" />
      </SubjectConfirmation>
    </Subject>
    <Conditions NotBefore="2013-03-18T07:38:15.128Z" NotOnOrAfter="2013-03-18T08:48:15.128Z">
      <AudienceRestriction>
        <Audience>https://www.contoso.com</Audience>
      </AudienceRestriction>
    </Conditions>
    <AttributeStatement>
      <Attribute Name="http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name">
        <AttributeValue>testuser@contoso.com</AttributeValue>
      </Attribute>
      <Attribute Name="http://schemas.microsoft.com/identity/claims/objectidentifier">
        <AttributeValue>3F2504E0-4F89-11D3-9A0C-0305E82C3301</AttributeValue>
      </Attribute>
      ...
    </AttributeStatement>
    <AuthnStatement AuthnInstant="2013-03-18T07:33:56.000Z" SessionIndex="_bf9c623d-cc20-407a-9a59-c2d0aee84d12">
      <AuthnContext>
        <AuthnContextClassRef> urn:oasis:names:tc:SAML:2.0:ac:classes:Password</AuthnContextClassRef>
      </AuthnContext>
    </AuthnStatement>
  </Assertion>
</samlp:Response>
```

### <a name="response"></a>Svarsåtgärder

`Response`Elementet innehåller resultatet av auktoriseringsbegäran. Azure AD anger `ID` - `Version` och- `IssueInstant` värden i- `Response` elementet. Dessutom anges följande attribut:

* `Destination`: När inloggningen är klar har detta angetts till `RedirectUri` för tjänst leverantören (moln tjänsten).
* `InResponseTo`: Detta anges till `ID` attributet för det `AuthnRequest` element som initierade svaret.

### <a name="issuer"></a>Utfärdare

Azure AD ställer in `Issuer` elementet till `https://sts.windows.net/<TenantIDGUID>/` där \<TenantIDGUID> är klient-ID: t för Azure AD-klienten.

Till exempel kan ett svar med Issuer-elementet se ut som i följande exempel:

```
<Issuer xmlns="urn:oasis:names:tc:SAML:2.0:assertion"> https://sts.windows.net/82869000-6ad1-48f0-8171-272ed18796e9/</Issuer>
```

### <a name="status"></a>Status

- `Status` Elementet förmedlar lyckad eller misslyckad inloggning. Det innehåller `StatusCode` elementet, som innehåller en kod eller en uppsättning kapslade koder som representerar status för begäran. Den innehåller också `StatusMessage` elementet som innehåller anpassade fel meddelanden som genereras under inloggnings processen.

<!-- TODO: Add an authentication protocol error reference -->

Följande exempel är ett SAML-svar på ett misslyckat inloggnings försök.

```
<samlp:Response ID="_f0961a83-d071-4be5-a18c-9ae7b22987a4" Version="2.0" IssueInstant="2013-03-18T08:49:24.405Z" InResponseTo="iddce91f96e56747b5ace6d2e2aa9d4f8c" xmlns:samlp="urn:oasis:names:tc:SAML:2.0:protocol">
  <Issuer xmlns="urn:oasis:names:tc:SAML:2.0:assertion">https://sts.windows.net/82869000-6ad1-48f0-8171-272ed18796e9/</Issuer>
  <samlp:Status>
    <samlp:StatusCode Value="urn:oasis:names:tc:SAML:2.0:status:Requester">
      <samlp:StatusCode Value="urn:oasis:names:tc:SAML:2.0:status:RequestUnsupported" />
    </samlp:StatusCode>
    <samlp:StatusMessage>AADSTS75006: An error occurred while processing a SAML2 Authentication request. AADSTS90011: The SAML authentication request property 'NameIdentifierPolicy/SPNameQualifier' is not supported.
Trace ID: 66febed4-e737-49ff-ac23-464ba090d57c
Timestamp: 2013-03-18 08:49:24Z</samlp:StatusMessage>
  </samlp:Status>
```

### <a name="assertion"></a>Assertion

Förutom `ID` , `IssueInstant` och `Version` ställer Azure AD in följande element i `Assertion` svarets element.

#### <a name="issuer"></a>Utfärdare

Detta är inställt på `https://sts.windows.net/<TenantIDGUID>/` där \<TenantIDGUID> är klient-ID: t för Azure AD-klienten.

```
<Issuer>https://sts.windows.net/82869000-6ad1-48f0-8171-272ed18796e9/</Issuer>
```

#### <a name="signature"></a>Signatur

Azure AD signerar kontrollen som svar på en lyckad inloggning. - `Signature` Elementet innehåller en digital signatur som moln tjänsten kan använda för att autentisera källan för att verifiera kontrollens integritet.

För att generera den här digitala signaturen använder Azure AD signerings nyckeln i `IDPSSODescriptor` elementet i metadata-dokumentet.

```
<ds:Signature xmlns:ds="https://www.w3.org/2000/09/xmldsig#">
      digital_signature_here
    </ds:Signature>
```

#### <a name="subject"></a>Ämne

Detta anger huvud kontot som omfattas av uttrycken i försäkran. Den innehåller ett `NameID` -element som representerar den autentiserade användaren. `NameID`Värdet är en mål-ID som endast dirigeras till tjänst leverantören som är mål gruppen för token. Det är beständigt – det kan återkallas, men omtilldelas aldrig. Det är också ogenomskinligt, eftersom det inte avslöjar något om användaren och inte kan användas som en identifierare för attribut frågor.

`Method`Attribut för `SubjectConfirmation` elementet är alltid inställt på `urn:oasis:names:tc:SAML:2.0:cm:bearer` .

```
<Subject>
      <NameID>Uz2Pqz1X7pxe4XLWxV9KJQ+n59d573SepSAkuYKSde8=</NameID>
      <SubjectConfirmation Method="urn:oasis:names:tc:SAML:2.0:cm:bearer">
        <SubjectConfirmationData InResponseTo="id758d0ef385634593a77bdf7e632984b6" NotOnOrAfter="2013-03-18T07:43:15.144Z" Recipient="https://contoso.com/identity/inboundsso.aspx" />
      </SubjectConfirmation>
</Subject>
```

#### <a name="conditions"></a>Villkor

Det här elementet anger villkor som definierar acceptabel användning av SAML-kontroller.

```
<Conditions NotBefore="2013-03-18T07:38:15.128Z" NotOnOrAfter="2013-03-18T08:48:15.128Z">
      <AudienceRestriction>
        <Audience>https://www.contoso.com</Audience>
      </AudienceRestriction>
</Conditions>
```

`NotBefore` `NotOnOrAfter` Attributen och anger det intervall under vilket kontrollen är giltig.

* `NotBefore`Attributets värde är lika med eller något (mindre än en sekund) senare än värdet för `IssueInstant` `Assertion` elementet. Azure AD tar inte hänsyn till någon tids skillnad mellan sig själv och moln tjänsten (tjänst leverantören) och lägger inte till någon buffert i den här tiden.
* Värdet för `NotOnOrAfter` attributet är 70 minuter senare än värdet för `NotBefore` attributet.

#### <a name="audience"></a>Målgrupp

Innehåller en URI som identifierar en avsedd mål grupp. Azure AD anger värdet för det här elementet till värdet för `Issuer` elementet i `AuthnRequest` som initierade inloggningen. Om du vill utvärdera `Audience` värdet använder du värdet för det `App ID URI` som angavs under program registreringen.

```
<AudienceRestriction>
        <Audience>https://www.contoso.com</Audience>
</AudienceRestriction>
```

Precis som `Issuer` värdet `Audience` måste värdet exakt matcha ett av tjänstens huvud namn som representerar moln tjänsten i Azure AD. Men om värdet för `Issuer` elementet inte är ett URI-värde, `Audience` är värdet i svaret det `Issuer` värde som föregås av `spn:` .

#### <a name="attributestatement"></a>AttributeStatement

Detta innehåller anspråk om ämnet eller användaren. Följande utdrag innehåller ett exempel `AttributeStatement` element. Ellipsen visar att elementet kan innehålla flera attribut och attributvärden.

```
<AttributeStatement>
      <Attribute Name="http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name">
        <AttributeValue>testuser@contoso.com</AttributeValue>
      </Attribute>
      <Attribute Name="http://schemas.microsoft.com/identity/claims/objectidentifier">
        <AttributeValue>3F2504E0-4F89-11D3-9A0C-0305E82C3301</AttributeValue>
      </Attribute>
      ...
</AttributeStatement>
```        

* **Namn anspråk** – värdet för `Name` attributet ( `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name` ) är User Principal Name av den autentiserade användaren, t `testuser@managedtenant.com` . ex..
* **ObjectIdentifier-anspråk** – värdet för `ObjectIdentifier` attributet ( `http://schemas.microsoft.com/identity/claims/objectidentifier` ) är det `ObjectId` katalog objekt som representerar den autentiserade användaren i Azure AD. `ObjectId` är en oföränderlig, globalt unik och åter användnings säker identifierare för den autentiserade användaren.

#### <a name="authnstatement"></a>AuthnStatement

Det här elementet förutsätter att kontroll ämnet autentiserades av ett visst sätt vid en viss tidpunkt.

* `AuthnInstant`Attributet anger tiden då användaren autentiseras med Azure AD.
* `AuthnContext`Elementet anger den autentiserings kontext som används för att autentisera användaren.

```
<AuthnStatement AuthnInstant="2013-03-18T07:33:56.000Z" SessionIndex="_bf9c623d-cc20-407a-9a59-c2d0aee84d12">
      <AuthnContext>
        <AuthnContextClassRef> urn:oasis:names:tc:SAML:2.0:ac:classes:Password</AuthnContextClassRef>
      </AuthnContext>
</AuthnStatement>
```
