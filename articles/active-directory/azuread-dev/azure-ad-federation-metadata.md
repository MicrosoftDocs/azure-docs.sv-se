---
title: Azure AD Federation-Metadata | Microsoft Docs
description: I den här artikeln beskrivs det dokument för federationsmetadata som Azure Active Directory publiceras för tjänster som accepterar Azure Active Directory tokens.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: azuread-dev
ms.workload: identity
ms.topic: conceptual
ms.date: 01/07/2017
ms.author: ryanwi
ms.reviewer: hirsin
ms.custom: aaddev
ROBOTS: NOINDEX
ms.openlocfilehash: 7f3bd8851fe723461c618499e539c987d79c0d68
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/02/2021
ms.locfileid: "101650149"
---
# <a name="federation-metadata"></a>Federationsmetadata

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

Azure Active Directory (Azure AD) publicerar ett dokument för federationsmetadata för tjänster som är konfigurerade för att godkänna de säkerhetstoken som Azure AD utfärdar. Dokument formatet för federationsmetadata beskrivs i [Web Services Federation Language (WS-Federation) Version 1,2](https://docs.oasis-open.org/wsfed/federation/v1.2/os/ws-federation-1.2-spec-os.html), som utökar [metadata för Oasis Security Assertion Markup Language (SAML) v 2.0](https://docs.oasis-open.org/security/saml/v2.0/saml-metadata-2.0-os.pdf).

## <a name="tenant-specific-and-tenant-independent-metadata-endpoints"></a>Klient-och klient oberoende slut punkter för klienter
Azure AD publicerar klient-och klient oberoende slut punkter.

Klient specifika slut punkter är utformade för en viss klient. De klient-/regionsspecifika federationsmetadata innehåller information om klienten, inklusive klient organisations information och slut punkts information. Program som begränsar åtkomsten till en enda klient organisation använder klient-/regionsspecifika slut punkter.

Klient oberoende slut punkter tillhandahåller information som är gemensam för alla Azure AD-klienter. Den här informationen gäller för klienter som finns på *login.microsoftonline.com* och delas mellan klienter. Klient oberoende slut punkter rekommenderas för program med flera klienter, eftersom de inte är associerade med någon viss klient.

## <a name="federation-metadata-endpoints"></a>Slut punkter för federationsmetadata
Azure AD publicerar federationsmetadata i `https://login.microsoftonline.com/<TenantDomainName>/FederationMetadata/2007-06/FederationMetadata.xml` .

För **klient-/regionsspecifika slut punkter** `TenantDomainName` kan vara en av följande typer:

* Ett registrerat domän namn för en Azure AD-klient, t `contoso.onmicrosoft.com` . ex.:.
* Det oåterkalleliga klient organisations-ID: t för domänen, till exempel `72f988bf-86f1-41af-91ab-2d7cd011db45` .

För **klient oberoende slut punkter** `TenantDomainName` är `common` . I det här dokumentet visas endast de federationsmetadata som är gemensamma för alla Azure AD-klienter som finns på login.microsoftonline.com.

Till exempel kan en klient-/regionsspecifika slut punkt vara `https://login.microsoftonline.com/contoso.onmicrosoft.com/FederationMetadata/2007-06/FederationMetadata.xml` . Klient oberoende slut punkt är [https://login.microsoftonline.com/common/FederationMetadata/2007-06/FederationMetadata.xml](https://login.microsoftonline.com/common/FederationMetadata/2007-06/FederationMetadata.xml) . Du kan visa dokumentet för federationsmetadata genom att skriva denna URL i en webbläsare.

## <a name="contents-of-federation-metadata"></a>Innehåll i federationsmetadata
Följande avsnitt innehåller information som behövs för tjänster som använder token som utfärdats av Azure AD.

### <a name="entity-id"></a>Enhets-ID
`EntityDescriptor`Elementet innehåller ett- `EntityID` attribut. Värdet för `EntityID` attributet representerar utfärdaren, det vill säga den säkerhetstokentjänst som utfärdade token. Det är viktigt att verifiera utfärdaren när du får en token.

Följande metadata visar ett exempel på ett klient-/regionsspecifika `EntityDescriptor` element med ett- `EntityID` element.

```
<EntityDescriptor
xmlns="urn:oasis:names:tc:SAML:2.0:metadata"
ID="_b827a749-cfcb-46b3-ab8b-9f6d14a1294b"
entityID="https://sts.windows.net/72f988bf-86f1-41af-91ab-2d7cd011db45/">
```
Du kan ersätta klient-ID: t i den klient oberoende slut punkten med klient-ID för att skapa ett klient-/regionsspecifika `EntityID` värde. Det resulterande värdet är detsamma som för token-utfärdaren. Strategin gör det möjligt för ett program för flera innehavare att verifiera utfärdaren för en specifik klient.

Följande metadata visar ett exempel på ett klient oberoende `EntityID` element. Observera att `{tenant}` är en literal, inte en plats hållare.

```
<EntityDescriptor
xmlns="urn:oasis:names:tc:SAML:2.0:metadata"
ID="="_0e5bd9d0-49ef-4258-bc15-21ce143b61bd"
entityID="https://sts.windows.net/{tenant}/">
```

### <a name="token-signing-certificates"></a>Token signerings certifikat
När en tjänst tar emot en token som utfärdats av en Azure AD-klient måste signaturen för token verifieras med en signerings nyckel som publiceras i federationsmetadata. Federationsmetadata innehåller den offentliga delen av de certifikat som klienterna använder för Token-signering. Certifikatets RAW-byte visas i- `KeyDescriptor` elementet. Token signerings certifikatet är giltigt endast för signering när värdet för `use` attributet är `signing` .

Ett dokument för federationsmetadata som publicerats av Azure AD kan ha flera signerings nycklar, till exempel när Azure AD förbereder uppdatering av signerings certifikatet. När ett dokument för federationsmetadata innehåller mer än ett certifikat, ska en tjänst som verifierar token ha stöd för alla certifikat i dokumentet.

Följande metadata visar ett exempel `KeyDescriptor` element med en signerings nyckel.

```
<KeyDescriptor use="signing">
<KeyInfo xmlns="https://www.w3.org/2000/09/xmldsig#">
<X509Data>
<X509Certificate>
MIIDPjCCAiqgAwIBAgIQVWmXY/+9RqFA/OG9kFulHDAJBgUrDgMCHQUAMC0xKzApBgNVBAMTImFjY291bnRzLmFjY2Vzc2NvbnRyb2wud2luZG93cy5uZXQwHhcNMTIwNjA3MDcwMDAwWhcNMTQwNjA3MDcwMDAwWjAtMSswKQYDVQQDEyJhY2NvdW50cy5hY2Nlc3Njb250cm9sLndpbmRvd3MubmV0MIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEArCz8Sn3GGXmikH2MdTeGY1D711EORX/lVXpr+ecGgqfUWF8MPB07XkYuJ54DAuYT318+2XrzMjOtqkT94VkXmxv6dFGhG8YZ8vNMPd4tdj9c0lpvWQdqXtL1TlFRpD/P6UMEigfN0c9oWDg9U7Ilymgei0UXtf1gtcQbc5sSQU0S4vr9YJp2gLFIGK11Iqg4XSGdcI0QWLLkkC6cBukhVnd6BCYbLjTYy3fNs4DzNdemJlxGl8sLexFytBF6YApvSdus3nFXaMCtBGx16HzkK9ne3lobAwL2o79bP4imEGqg+ibvyNmbrwFGnQrBc1jTF9LyQX9q+louxVfHs6ZiVwIDAQABo2IwYDBeBgNVHQEEVzBVgBCxDDsLd8xkfOLKm4Q/SzjtoS8wLTErMCkGA1UEAxMiYWNjb3VudHMuYWNjZXNzY29udHJvbC53aW5kb3dzLm5ldIIQVWmXY/+9RqFA/OG9kFulHDAJBgUrDgMCHQUAA4IBAQAkJtxxm/ErgySlNk69+1odTMP8Oy6L0H17z7XGG3w4TqvTUSWaxD4hSFJ0e7mHLQLQD7oV/erACXwSZn2pMoZ89MBDjOMQA+e6QzGB7jmSzPTNmQgMLA8fWCfqPrz6zgH+1F1gNp8hJY57kfeVPBiyjuBmlTEBsBlzolY9dd/55qqfQk6cgSeCbHCy/RU/iep0+UsRMlSgPNNmqhj5gmN2AFVCN96zF694LwuPae5CeR2ZcVknexOWHYjFM0MgUSw0ubnGl0h9AJgGyhvNGcjQqu9vd1xkupFgaN+f7P3p3EVN5csBg5H94jEcQZT7EKeTiZ6bTrpDAnrr8tDCy8ng
</X509Certificate>
</X509Data>
</KeyInfo>
</KeyDescriptor>
  ```

`KeyDescriptor`Elementet visas på två platser i dokumentet för federationsmetadata; i avsnittet WS-Federation-Specific och avsnittet SAML-Specific. De certifikat som publiceras i båda avsnitten blir desamma.

I avsnittet WS-Federation-Specific skulle en WS-Federation metadata-läsare läsa certifikaten från ett- `RoleDescriptor` element med `SecurityTokenServiceType` typen.

Följande metadata visar ett exempel `RoleDescriptor` element.

```
<RoleDescriptor xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns:fed="https://docs.oasis-open.org/wsfed/federation/200706" xsi:type="fed:SecurityTokenServiceType" protocolSupportEnumeration="https://docs.oasis-open.org/wsfed/federation/200706">
```

I det SAML-specificerade avsnittet skulle en WS-Federation metadata-läsare läsa certifikaten från ett- `IDPSSODescriptor` element.

Följande metadata visar ett exempel `IDPSSODescriptor` element.

```
<IDPSSODescriptor protocolSupportEnumeration="urn:oasis:names:tc:SAML:2.0:protocol">
```
Det finns inga skillnader i formatet för klient-och klient oberoende certifikat.

### <a name="ws-federation-endpoint-url"></a>WS-Federation slut punkts-URL
Federationsmetadata omfattar den URL som används i Azure AD för enkel inloggning och enkel utloggning i WS-Federation protokollet. Den här slut punkten visas i- `PassiveRequestorEndpoint` elementet.

Följande metadata visar ett exempel `PassiveRequestorEndpoint` element för en klient-Specific-slutpunkt.

```
<fed:PassiveRequestorEndpoint>
<EndpointReference xmlns="https://www.w3.org/2005/08/addressing">
<Address>
https://login.microsoftonline.com/72f988bf-86f1-41af-91ab-2d7cd011db45/wsfed
</Address>
</EndpointReference>
</fed:PassiveRequestorEndpoint>
```
För den klient oberoende slut punkten visas WS-Federation URL: en i WS-Federation slut punkten, som du ser i följande exempel.

```
<fed:PassiveRequestorEndpoint>
<EndpointReference xmlns="https://www.w3.org/2005/08/addressing">
<Address>
https://login.microsoftonline.com/common/wsfed
</Address>
</EndpointReference>
</fed:PassiveRequestorEndpoint>
```

### <a name="saml-protocol-endpoint-url"></a>Slut punkts-URL för SAML-protokoll
Federationsmetadata innehåller den URL som Azure AD använder för enkel inloggning och enkel utloggning i SAML 2,0-protokollet. De här slut punkterna visas i- `IDPSSODescriptor` elementet.

URL: erna för inloggning och utloggning visas i `SingleSignOnService` `SingleLogoutService` elementen och.

Följande metadata visar ett exempel `PassiveResistorEndpoint` för en klient-/regionsspecifika slut punkt.

```
<IDPSSODescriptor protocolSupportEnumeration="urn:oasis:names:tc:SAML:2.0:protocol">
…
    <SingleLogoutService Binding="urn:oasis:names:tc:SAML:2.0:bindings:HTTP-Redirect" Location="https://login.microsoftonline.com/contoso.onmicrosoft.com/saml2" />
    <SingleSignOnService Binding="urn:oasis:names:tc:SAML:2.0:bindings:HTTP-Redirect" Location="https://login.microsoftonline.com/contoso.onmicrosoft.com /saml2" />
  </IDPSSODescriptor>
```

Slut punkterna för de gemensamma SAML 2,0-protokollets slut punkter publiceras i de klient oberoende federationsmetadata, som visas i följande exempel.

```
<IDPSSODescriptor protocolSupportEnumeration="urn:oasis:names:tc:SAML:2.0:protocol">
…
    <SingleLogoutService Binding="urn:oasis:names:tc:SAML:2.0:bindings:HTTP-Redirect" Location="https://login.microsoftonline.com/common/saml2" />
    <SingleSignOnService Binding="urn:oasis:names:tc:SAML:2.0:bindings:HTTP-Redirect" Location="https://login.microsoftonline.com/common/saml2" />
  </IDPSSODescriptor>
```
