---
title: Azure Active Directory identitets konfiguration för Azure API för FHIR
description: Lär dig principerna för identitet, autentisering och auktorisering för Azure FHIR-servrar.
services: healthcare-apis
author: caitlinv39
ms.reviewer: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: conceptual
ms.date: 02/19/2019
ms.author: cavoeg
ms.openlocfilehash: 277838e3ce870b528f986292f88ad2b2b20f42b1
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/11/2021
ms.locfileid: "103020264"
---
# <a name="azure-active-directory-identity-configuration-for-azure-api-for-fhir"></a>Azure Active Directory identitets konfiguration för Azure API för FHIR

En viktig del när du arbetar med hälso vårds data är att säkerställa att data är säkra och inte kan nås av obehöriga användare eller program. FHIR-servrar använder [OAuth 2,0](https://oauth.net/2/) för att säkerställa denna data säkerhet. [Azure API för FHIR](https://azure.microsoft.com/services/azure-api-for-fhir/) skyddas med [Azure Active Directory](../../active-directory/index.yml), vilket är ett exempel på en identitets leverantör för OAuth 2,0. Den här artikeln innehåller en översikt över FHIR Server-auktorisering och de steg som krävs för att hämta en token för att få åtkomst till en FHIR-Server. De här stegen gäller för alla FHIR-servrar och identitets leverantörer, och vi går igenom Azure API för FHIR som FHIR-Server och Azure AD som vår identitets leverantör i den här artikeln.

## <a name="access-control-overview"></a>Översikt över åtkomstkontroll

För att ett klient program ska kunna komma åt Azure API för FHIR måste det presentera en åtkomsttoken. Åtkomsttoken är en signerad [base64](https://en.wikipedia.org/wiki/Base64) -kodad samling egenskaper (anspråk) som förmedlar information om klientens identitet och roller och behörigheter som beviljas till klienten.

Det finns ett antal sätt att hämta en token, men Azure API för FHIR inte bryr sig om att token erhålls så länge det är en korrekt signerad token med rätt anspråk. 

Genom att använda [Flow-flöde](../../active-directory/azuread-dev/v1-protocols-oauth-code.md) som exempel går det att komma åt en FHIR-Server genom de fyra stegen nedan:

![FHIR-auktorisering](media/azure-ad-hcapi/fhir-authorization.png)

1. Klienten skickar en begäran till `/authorize` slut punkten för Azure AD. Azure AD kommer att omdirigera klienten till en inloggnings sida där användaren autentiseras med hjälp av lämpliga autentiseringsuppgifter (till exempel användar namn och lösen ord eller tvåfaktorautentisering). Se information om hur du [skaffar en auktoriseringskod](../../active-directory/azuread-dev/v1-protocols-oauth-code.md#request-an-authorization-code). Vid lyckad autentisering returneras en *auktoriseringskod* till klienten. Azure AD tillåter bara att den här auktoriseringskod returneras till en registrerad svars-URL som kon figurer ATS i klient program registreringen (se nedan).
1. Klient programmet utbyter auktoriseringskod för en *åtkomsttoken* i `/token` slut punkten för Azure AD. När en token begärs kan klient programmet behöva ange en klient hemlighet (program lösen ordet). Se information om hur du [skaffar en åtkomsttoken](../../active-directory/azuread-dev/v1-protocols-oauth-code.md#use-the-authorization-code-to-request-an-access-token).
1. Klienten skickar en begäran till Azure-API: t för FHIR, till exempel för `GET /Patient` att söka i alla patienter. När du gör en begäran, innehåller den åtkomsttoken i en HTTP-begärans huvud, till exempel `Authorization: Bearer eyJ0e...` där `eyJ0e...` representerar Base64-kodad åtkomsttoken.
1. Azure API för FHIR verifierar att token innehåller lämpliga anspråk (egenskaper i token). Om allting checkar ut, slutförs begäran och returnerar ett FHIR-paket med resultat till klienten.

Det är viktigt att Observera att Azure API för FHIR inte är inblandad vid verifiering av användarautentiseringsuppgifter och att det inte utfärdar token. Autentiseringen och token skapas av Azure AD. Azure API för FHIR verifierar bara att token har signerats korrekt (den är autentisk) och har lämpliga anspråk.

## <a name="structure-of-an-access-token"></a>Struktur för en åtkomsttoken

Utveckling av FHIR-program innebär ofta fel sökning av åtkomst problem. Om en klient nekas åtkomst till Azure-API: et för FHIR är det bra att förstå strukturen i åtkomsttoken och hur den kan avkodas för att kontrol lera innehållet (anspråken) för token. 

FHIR-servrar förväntar sig vanligt vis en [JSON Web token](https://en.wikipedia.org/wiki/JSON_Web_Token) (JWT, ibland uttalar "skriva"). Det består av tre delar:

1. En rubrik som kan se ut så här:
    ```json
    {
      "alg": "HS256",
      "typ": "JWT"
    }
    ```
1. Nytto lasten (anspråken), till exempel:
    ```json
    {
     "oid": "123",
     "iss": "https://issuerurl",
     "iat": 1422779638,
     "roles": [
        "admin"
      ]
    }
    ```
1. En signatur, som beräknas genom att kombinera det Base64-kodade innehållet i huvudet och nytto lasten och beräkna en kryptografisk hash för dem baserat på algoritmen ( `alg` ) som anges i huvudet. En server kan hämta offentliga nycklar från identitets leverantören och kontrol lera att denna token har utfärdats av en identitets leverantör och att den inte har manipulerats.

Den fullständiga token består av base64-kodade (faktiskt base64-kodade) versioner av dessa tre segment. De tre segmenten sammanfogas och åtskiljs med en `.` (punkt).

En exempel-token visas nedan:

```
eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJvaWQiOiIxMjMiLCAiaXNzIjoiaHR0cHM6Ly9pc3N1ZXJ1cmwiLCJpYXQiOjE0MjI3Nzk2MzgsInJvbGVzIjpbImFkbWluIl19.gzSraSYS8EXBxLN_oWnFSRgCzcmJmMjLiuyu5CSpyHI
```

Token kan avkodas och inspekteras med verktyg som [https://jwt.ms](https://jwt.ms) . Resultatet av att avkoda token är:

```json
{
  "alg": "HS256",
  "typ": "JWT"
}.{
  "oid": "123",
  "iss": "https://issuerurl",
  "iat": 1422779638,
  "roles": [
    "admin"
  ]
}.[Signature]
```

## <a name="obtaining-an-access-token"></a>Hämta en åtkomsttoken

Som nämnts ovan finns det flera sätt att hämta en token från Azure AD. De beskrivs i detalj i dokumentationen för [Azure AD-utvecklare](../../active-directory/develop/index.yml).

Azure AD har två olika versioner av OAuth 2,0-slutpunkter, som kallas `v1.0` och `v2.0` . Båda dessa versioner är OAuth 2,0-slutpunkter och- `v1.0` och- `v2.0` benämningarna avser skillnader i hur Azure AD implementerar standarden. 

När du använder en FHIR-Server kan du använda antingen- `v1.0` eller- `v2.0` slut punkterna. Valet kan bero på vilka autentiseringsinställningar du använder i klient programmet.

De relevanta avsnitten i Azure AD-dokumentationen är:

* `v1.0` Endpoint
    * [Flöde för auktoriseringskod](../../active-directory/azuread-dev/v1-protocols-oauth-code.md).
    * [Flöde](../../active-directory/azuread-dev/v1-oauth2-client-creds-grant-flow.md)för klientautentiseringsuppgifter.
* `v2.0` Endpoint
    * [Flöde för auktoriseringskod](../../active-directory/develop/v2-oauth2-auth-code-flow.md).
    * [Flöde](../../active-directory/develop/v2-oauth2-client-creds-grant-flow.md)för klientautentiseringsuppgifter.

Det finns andra varianter (till exempel för flödets räkning) för att hämta en token. Mer information finns i Azure AD-dokumentationen. När du använder Azure API för FHIR finns det också vissa genvägar för att hämta en åtkomsttoken (för fel söknings syfte) [med hjälp av Azure CLI](get-healthcare-apis-access-token-cli.md).

## <a name="next-steps"></a>Nästa steg

I det här dokumentet har du lärt dig några av de grundläggande begreppen som krävs för att skydda åtkomsten till Azure API för FHIR med hjälp av Azure AD. Om du vill lära dig hur du distribuerar en instans av Azure API för FHIR fortsätter du till snabb starten för distribution.

>[!div class="nextstepaction"]
>[Distribuera Azure API för FHIR](fhir-paas-portal-quickstart.md)