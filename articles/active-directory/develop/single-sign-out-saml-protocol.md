---
title: SAML-protokoll för enkel inloggning i Azure
description: I den här artikeln beskrivs den enskilda Sign-Out SAML-protokollet i Azure Active Directory
services: active-directory
author: kenwith
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 03/22/2021
ms.author: kenwith
ms.custom: aaddev
ms.reviewer: paulgarn
ms.openlocfilehash: 040e49ee870704107e1d4118f1484119d98a9ebf
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/22/2021
ms.locfileid: "104781219"
---
# <a name="single-sign-out-saml-protocol"></a>Single Sign-Out SAML-protokoll

Azure Active Directory (Azure AD) stöder en enkel inloggnings profil för SAML 2,0-webbläsare. För att enkel utloggning ska fungera korrekt måste **LogoutURL** för programmet uttryckligen registreras med Azure AD under program registreringen. Om appen [läggs till i Azure App galleriet](v2-howto-app-gallery-listing.md) , kan det här värdet anges som standard. Annars måste värdet bestämmas och anges av den person som lägger till appen i sin Azure AD-klient. Azure AD använder LogoutURL för att omdirigera användare när de har loggat ut. 

Azure AD stöder omdirigerings bindning (HTTP GET) och inte HTTP POST-bindning.

Följande diagram visar arbets flödet för processen för enkel inloggning i Azure AD.

![Arbets flöde för enkel inloggning i Azure AD](./media/single-sign-out-saml-protocol/active-directory-saml-single-sign-out-workflow.png)

## <a name="logoutrequest"></a>LogoutRequest
Moln tjänsten skickar ett `LogoutRequest` meddelande till Azure AD för att indikera att en session har avslut ATS. Följande utdrag visar ett exempel `LogoutRequest` element.

```
<samlp:LogoutRequest xmlns="urn:oasis:names:tc:SAML:2.0:metadata" ID="idaa6ebe6839094fe4abc4ebd5281ec780" Version="2.0" IssueInstant="2013-03-28T07:10:49.6004822Z" xmlns:samlp="urn:oasis:names:tc:SAML:2.0:protocol">
  <Issuer xmlns="urn:oasis:names:tc:SAML:2.0:assertion">https://www.workaad.com</Issuer>
  <NameID xmlns="urn:oasis:names:tc:SAML:2.0:assertion"> Uz2Pqz1X7pxe4XLWxV9KJQ+n59d573SepSAkuYKSde8=</NameID>
</samlp:LogoutRequest>
```

### <a name="logoutrequest"></a>LogoutRequest
Det `LogoutRequest` element som skickas till Azure AD kräver följande attribut:

* `ID` -Detta identifierar utloggnings förfrågan. Värdet för `ID` får inte börja med en siffra. Den typiska metoden är att lägga till **ID** i sträng representationen av ett GUID.
* `Version` – Ange värdet för det här elementet som **2,0**. Det här värdet är obligatoriskt.
* `IssueInstant` – Det här är en `DateTime` sträng med ett UTC-värde (Coordination Universal Time) och tur och [RETUR-format ("o")](/dotnet/standard/base-types/standard-date-and-time-format-strings). Azure AD förväntar sig ett värde av den här typen, men tvingar inte det.

### <a name="issuer"></a>Utfärdare
`Issuer`Elementet i en `LogoutRequest` måste exakt matcha ett av **ServicePrincipalNames** i moln tjänsten i Azure AD. Detta är vanligt vis inställt på **app-ID-URI: n** som anges vid program registrering.

### <a name="nameid"></a>NameID
Värdet för `NameID` elementet måste exakt matcha `NameID` användarens som loggas ut.

## <a name="logoutresponse"></a>LogoutResponse
Azure AD skickar ett `LogoutResponse` som svar på ett- `LogoutRequest` element. Följande utdrag visar ett exempel `LogoutResponse` .

```
<samlp:LogoutResponse ID="_f0961a83-d071-4be5-a18c-9ae7b22987a4" Version="2.0" IssueInstant="2013-03-18T08:49:24.405Z" InResponseTo="iddce91f96e56747b5ace6d2e2aa9d4f8c" xmlns:samlp="urn:oasis:names:tc:SAML:2.0:protocol">
  <Issuer xmlns="urn:oasis:names:tc:SAML:2.0:assertion">https://sts.windows.net/82869000-6ad1-48f0-8171-272ed18796e9/</Issuer>
  <samlp:Status>
    <samlp:StatusCode Value="urn:oasis:names:tc:SAML:2.0:status:Success" />
  </samlp:Status>
</samlp:LogoutResponse>
```

### <a name="logoutresponse"></a>LogoutResponse
Azure AD anger `ID` - `Version` och- `IssueInstant` värden i- `LogoutResponse` elementet. Den anger också `InResponseTo` elementet till värdet för `ID` attributet för det som har bevarat `LogoutRequest` svaret.

### <a name="issuer"></a>Utfärdare
Azure AD anger det här värdet till `https://login.microsoftonline.com/<TenantIdGUID>/` där \<TenantIdGUID> är klient-ID: t för Azure AD-klienten.

Om du vill utvärdera värdet för `Issuer` elementet använder du värdet för **app-ID-URI: n** som angavs under program registreringen.

### <a name="status"></a>Status
Azure AD använder `StatusCode` elementet i- `Status` elementet för att ange att utloggningen lyckades eller misslyckades. När utloggnings försöket Miss lyckas `StatusCode` kan elementet också innehålla anpassade fel meddelanden.
