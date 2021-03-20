---
title: REST API anspråks utbyten i den anpassade principen för B2C
titleSuffix: Azure AD B2C
description: En introduktion till att skapa en Azure AD B2C användar resa som samverkar med RESTful-tjänster.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 05/18/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: bc2b72779460c2b7e3999204ace50ca57388b9a2
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "89594194"
---
# <a name="integrate-rest-api-claims-exchanges-in-your-azure-ad-b2c-custom-policy"></a>Integrera REST API Claims-utbyten i Azure AD B2C anpassad princip

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Den identitets miljö som ligger under Azure Active Directory B2C (Azure AD B2C) kan integreras med RESTful-API: er i en användar resa. Den här artikeln visar hur du skapar en användar resa som interagerar med en RESTful-tjänst med en [RESTful-teknisk profil](restful-technical-profile.md).

Med hjälp av Azure AD B2C kan du lägga till din egen affärs logik till en användar resa genom att anropa din egen RESTful-tjänst. Med identitets miljön kan du skicka och ta emot data från din RESTful-tjänst till Exchange-anspråk. Du kan till exempel:

- **Verifiera indata från användaren**. Du kan till exempel kontrol lera att den e-postadress som användaren har angett finns i kundens databas och om den inte finns ett fel.
- **Bearbeta anspråk**. Om en användare anger sitt förnamn i gemener eller versaler kan REST API formatera namnet med enbart den första bokstaven och returnera det till Azure AD B2C.
- **Utöka användar data genom att ytterligare integrera med affärs program i företags nivå**. Din RESTful-tjänst kan ta emot användarens e-postadress, fråga kundens databas och returnera användarens lojalitets nummer till Azure AD B2C. Sedan kan returnera anspråk lagras i användarens Azure AD-konto, utvärderas i nästa steg för att dirigera eller inkluderas i åtkomsttoken.
- **Kör anpassad affärs logik**. Du kan skicka push-meddelanden, uppdatera företags databaser, köra en användarmigrering, hantera behörigheter, granska databaser och utföra andra arbets flöden.

![Diagram över ett RESTful service anspråk utbyte](media/custom-policy-rest-api-intro/restful-service-claims-exchange.png)

> [!NOTE]
> Om det finns långsam eller inget svar från RESTful-tjänsten till Azure AD B2C, är tids gränsen 30 sekunder och antalet nya försök är 2 gånger (vilket innebär att det finns tre försök totalt). Inställningarna för timeout och antal nya försök är för närvarande inte att konfigurera.

## <a name="calling-a-restful-service"></a>Anropa en RESTful-tjänst

Interaktionen innehåller ett anspråk utbyte av information mellan REST API anspråk och Azure AD B2C. Du kan utforma integrationen med RESTful-tjänsterna på följande sätt:

- **Verifiering av teknisk profil**. Anropet till RESTful-tjänsten sker i en [verifierings teknisk profil](validation-technical-profile.md) av den angivna [självkontrollerade tekniska profilen](self-asserted-technical-profile.md)eller en [verifierings kontroll](display-control-verification.md) för en [visnings kontroll](display-controls.md). Den tekniska verifierings profilen verifierar de data som anges av användaren innan användar resan flyttas framåt. Med den tekniska verifierings profilen kan du:

  - Skicka anspråk till REST API.
  - Validera anspråk och Utlös de anpassade fel meddelanden som visas för användaren.
  - Skicka tillbaka anspråk från REST API till efterföljande Orchestration-steg.

- **Anspråk utbyte**. Ett direkt anspråk utbyte kan konfigureras genom att anropa en REST API teknisk profil direkt från ett Dirigerings steg i en [användar resa](userjourneys.md). Den här definitionen är begränsad till:

  - Skicka anspråk till REST API.
  - Validera anspråk och Utlös anpassade fel meddelanden som returneras till programmet.
  - Skicka tillbaka anspråk från REST API till efterföljande Orchestration-steg.

Du kan lägga till ett REST API-anrop i alla steg i användar resan som definieras av en anpassad princip. Du kan till exempel anropa en REST API:

- Under inloggningen, precis innan Azure AD B2C validerar autentiseringsuppgifterna.
- Omedelbart efter inloggning.
- Innan Azure AD B2C skapar ett nytt konto i katalogen.
- När Azure AD B2C skapar ett nytt konto i katalogen.
- Innan Azure AD B2C utfärdar en åtkomsttoken.

![Validering av teknisk profil samling](media/custom-policy-rest-api-intro/validation-technical-profile.png)

## <a name="sending-data"></a>Skickar data

I [RESTful tekniska profil](restful-technical-profile.md) `InputClaims` innehåller elementet en lista över anspråk att skicka till din RESTful-tjänst. Du kan mappa namnet på ditt anspråk till namnet som definierats i RESTful-tjänsten, ange ett standardvärde och använda [anspråk matchare](claim-resolver-overview.md).

Du kan konfigurera hur inloggade anspråk skickas till RESTful-anspråks leverantören med hjälp av SendClaimsIn-attributet. Möjliga värden är:

- **Brödtext** som skickas i meddelandet HTTP POST-begäran i JSON-format.
- **Formulär** som skickas i meddelandet HTTP post-begär Ande i ett et-tecken ("&").
- **Sidhuvud** som skickas i HTTP Get-begärans huvud.
- **QueryString**, som skickats i HTTP GET-förfrågan frågesträng.

När du har konfigurerat **Body** -alternativet kan du med REST API teknisk profil skicka en komplex JSON-nyttolast till en slut punkt. Mer information finns i [skicka en JSON-nyttolast](restful-technical-profile.md#send-a-json-payload).

## <a name="receiving-data"></a>Tar emot data

`OutputClaims`Elementet i den [tekniska RESTful-profilen](restful-technical-profile.md) innehåller en lista över anspråk som returneras av REST API. Du kan behöva mappa namnet på det anspråk som definierats i principen till det namn som definierats i REST API. Du kan också ta med anspråk som inte returneras av REST API identitets leverantören, så länge som du ställer in attributet DefaultValue.

De utgående anspråk som parsas av RESTful-anspråks leverantören förväntar sig alltid att parsa ett fast JSON-Body-svar, till exempel:

```json
{
  "name": "Emily Smith",
  "email": "emily@outlook.com",
  "loyaltyNumber":  1234
}
```

De utgående anspråken bör se ut så här:

```xml
<OutputClaims>
  <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
  <OutputClaim ClaimTypeReferenceId="email" />
  <OutputClaim ClaimTypeReferenceId="loyaltyNumber" />
</OutputClaims>
```

Om du vill parsa ett kapslat JSON-Body-svar anger du ResolveJsonPathsInJsonTokens-metadata till true. I utgående anspråk anger du PartnerClaimType till det JSON-sökobjekt som du vill mata ut.

```json
"contacts": [
  {
    "id": "MAINCONTACT_1",
    "person": {
      "name": "Emily Smith",
      "loyaltyNumber":  1234,
      "emails": [
        {
          "id": "EMAIL_1",
          "type": "WORK",
          "email": "email@domain.com"
        }
      ]
    }
  }
],
```


De utgående anspråken bör se ut så här:

```xml
<OutputClaims>
  <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="contacts[0].person.name" />
  <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="contacts[0].person.emails[0].email" />
  <OutputClaim ClaimTypeReferenceId="loyaltyNumber" PartnerClaimType="contacts[0].person.loyaltyNumber" />
</OutputClaims>
```

## <a name="security-considerations"></a>Säkerhetsöverväganden

Du måste skydda REST API-slutpunkten så att endast autentiserade klienter kan kommunicera med den. REST API måste använda en HTTPS-slutpunkt. Ange AuthenticationType metadata till någon av följande autentiseringsmetoder:

- **Klient certifikatet** begränsar åtkomsten med hjälp av autentisering av klient certifikat. Endast tjänster som har rätt certifikat kan komma åt ditt API. Du lagrar klient certifikatet i en Azure AD B2C princip nyckel. Lär dig mer om hur du [skyddar din RESTful-tjänst genom att använda klient certifikat](secure-rest-api.md#https-client-certificate-authentication).
- **Basic** säkrar REST API med HTTP Basic-autentisering. Endast verifierade användare, inklusive Azure AD B2C, har åtkomst till ditt API. Användar namnet och lösen ordet lagras i Azure AD B2C princip nycklar. Lär dig hur du [skyddar dina RESTful-tjänster med hjälp av http Basic-autentisering](secure-rest-api.md#http-basic-authentication).
- **Bearer** begränsar åtkomsten med hjälp av en klient OAuth2-åtkomsttoken. Åtkomsttoken lagras i en Azure AD B2C princip nyckel. Lär dig mer om hur du [skyddar din RESTful-tjänst genom att använda Bearer-token](secure-rest-api.md#oauth2-bearer-authentication).

## <a name="rest-api-platform"></a>REST API plattform
Din REST API kan baseras på valfri plattform och skrivas på valfritt språk, så länge det är säkert och kan skicka och ta emot anspråk som anges i [RESTful Technical Profile](restful-technical-profile.md).

## <a name="localize-the-rest-api"></a>Lokalisera REST API
I en RESTful teknisk profil kanske du vill skicka den aktuella sessionens språk/nationella inställningar och vid behov generera ett lokaliserat fel meddelande. Med hjälp av [anspråks lösa ren](claim-resolver-overview.md)kan du skicka ett sammanhangsbaseradt anspråk, till exempel användar språket. I följande exempel visas en RESTful teknisk profil som demonstrerar det här scenariot.

```xml
<TechnicalProfile Id="REST-ValidateUserData">
  <DisplayName>Validate user input data</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="ServiceUrl">https://your-app.azurewebsites.net/api/identity</Item>
    <Item Key="AuthenticationType">None</Item>
    <Item Key="SendClaimsIn">Body</Item>
    <Item Key="IncludeClaimResolvingInClaimsHandling">true</Item>
  </Metadata>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="userLanguage" DefaultValue="{Culture:LCID}" AlwaysUseDefaultValue="true" />
    <InputClaim ClaimTypeReferenceId="email" PartnerClaimType="emailAddress" />
  </InputClaims>
  <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
</TechnicalProfile>
```

## <a name="handling-error-messages"></a>Hantera fel meddelanden

Din REST API kan behöva returnera ett fel meddelande, till exempel "användaren kunde inte hittas i CRM-systemet". Om ett fel inträffar ska REST API returnera ett HTTP 409-fel meddelande (konflikt svars status kod). Mer information finns i [teknisk profil för RESTful](restful-technical-profile.md#returning-validation-error-message).

Detta kan endast uppnås genom att anropa en REST API teknisk profil från en teknisk verifierings profil. Detta gör att användaren kan korrigera data på sidan och köra verifieringen igen när sidan skickas.

Ett HTTP 409-svar krävs för att förhindra bearbetning av efterföljande validerings tekniska profiler i det här Orchestration-steget.

Om du refererar till en REST API teknisk profil direkt från en användar resa omdirigeras användaren tillbaka till den förlitande part appen med det relevanta fel meddelandet.

## <a name="publishing-your-rest-api"></a>Publicera REST API

Begäran till din REST API-tjänst kommer från Azure AD B2C-servrar. Den REST API tjänsten måste publiceras till en offentligt tillgänglig HTTPS-slutpunkt. REST API-anropen kommer från en Azure Data Center-IP-adress.

Designa din REST API-tjänst och dess underliggande komponenter (till exempel databas och fil system) till hög tillgänglighet.

## <a name="next-steps"></a>Nästa steg

I följande artiklar finns exempel på hur du använder en RESTful teknisk profil:

- [Genom gång: integrera REST API Claims-utbyten i Azure AD B2C användar resa som validering av användarindata](custom-policy-rest-api-claims-validation.md)
- [Genom gång: Lägg till REST API Claims-utbyten till anpassade principer i Azure Active Directory B2C](custom-policy-rest-api-claims-validation.md)
- [Skydda dina REST API-tjänster](secure-rest-api.md)
- [Referens: RESTful teknisk profil](restful-technical-profile.md)
