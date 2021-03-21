---
title: Tjänst-till-tjänst-autentisering med OAuth 2.0 på uppdrag av Flow | Microsoft Docs
description: Den här artikeln beskriver hur du använder HTTP-meddelanden för att implementera tjänst-till-tjänst-autentisering med OAuth 2.0 on-of-Flow.
services: active-directory
documentationcenter: .net
author: navyasric
manager: CelesteDG
ms.service: active-directory
ms.subservice: azuread-dev
ms.workload: identity
ms.topic: conceptual
ms.date: 08/5/2020
ms.author: ryanwi
ms.reviewer: hirsin, nacanuma
ms.custom: aaddev
ROBOTS: NOINDEX
ms.openlocfilehash: f746cc654934464d907c6ad669eb7470e4dcaeeb
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "88117744"
---
# <a name="service-to-service-calls-that-use-delegated-user-identity-in-the-on-behalf-of-flow"></a>Tjänst-till-tjänst-anrop som använder delegerad användar identitet i flödets räkning

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

OAuth 2,0-flödet på OBO-nivå () gör det möjligt för ett program som anropar en tjänst eller ett webb-API för att skicka användarautentisering till en annan tjänst eller webb-API. OBO-flödet sprider den delegerade användar identiteten och behörigheterna via begär ande kedjan. För att mellanskikts tjänsten ska kunna göra autentiserade begär anden till den underordnade tjänsten måste den skydda en åtkomsttoken från Azure Active Directory (Azure AD) för användarens räkning.

> [!IMPORTANT]
> Från och med maj 2018 kan `id_token` inte användas för den här flödes Inräkningen.  Appar med en sida (SPAs) måste skicka en åtkomsttoken till en konfidentiell klient på mellan nivå för att utföra OBO-flöden. Mer information om klienter som kan utföra on-of-on-of-anrop finns i [begränsningar](#client-limitations).

## <a name="on-behalf-of-flow-diagram"></a>Flödes diagram på uppdrag

OBO-flödet startar när användaren har autentiserats för ett program som använder [OAuth 2,0-auktoriseringskod](v1-protocols-oauth-code.md). Vid det här skedet skickar programmet en åtkomsttoken (token A) till den mellanliggande webb-API: n (API A) som innehåller användarens anspråk och medgivande för att komma åt API A. Sedan gör API A en autentiserad begäran till det underordnade webb-API: et (API B).

De här stegen utgörs av flödet på olika sätt: ![ visar stegen i OAuth 2.0-flöde på uppdrag](./media/v1-oauth2-on-behalf-of-flow/active-directory-protocols-oauth-on-behalf-of-flow.png)

1. Klient programmet skickar en begäran till API A med token A.
1. API A autentiserar till slut punkten för utfärdande av Azure AD-token och begär en token för åtkomst till API B.
1. Svars punkten för utfärdande av Azure AD-token verifierar API-autentiseringsuppgifterna med token A och utfärdar åtkomsttoken för API B (token B).
1. Begäran till API B innehåller token B i Authorization-huvudet.
1. API B returnerar data från den skyddade resursen.

>[!NOTE]
>Mål grupps anspråket i en åtkomsttoken som används för att begära en token för en underordnad tjänst måste vara ID: t för tjänsten som gör OBO-begäran. Token måste också vara signerad med Azure Active Directory globala signerings nyckeln (som är standard för program som registrerats via **Appregistreringar** i portalen).

## <a name="register-the-application-and-service-in-azure-ad"></a>Registrera programmet och tjänsten i Azure AD

Registrera både mellan nivå tjänsten och klient programmet i Azure AD.

### <a name="register-the-middle-tier-service"></a>Registrera mellanskikts tjänsten

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. I det översta fältet väljer du ditt konto och tittar under **katalog** listan för att välja en Active Directory klient för ditt program.
1. Välj **fler tjänster** i den vänstra rutan och välj **Azure Active Directory**.
1. Välj **Appregistreringar** och sedan **ny registrering**.
1. Ange ett eget namn för programmet och välj program typ.
1. Under **Kontotyper som stöds** väljer du **Accounts in any organizational directory and personal Microsoft accounts** (Konton i alla organisationskataloger och personliga Microsoft-konton).
1. Ange omdirigerings-URI: n till bas-URL: en.
1. Välj **Registrera** för att skapa programmet.
1. I Azure Portal väljer du ditt program och väljer **certifikat & hemligheter**.
1. Välj **ny klient hemlighet** och Lägg till en hemlighet med en varaktighet på antingen ett år eller två år.
1. När du sparar den här sidan visas det hemliga värdet i Azure Portal. Kopiera och spara det hemliga värdet på en säker plats.
1. Skapa ett omfång i programmet på sidan **exponera en API** för din app och klicka på Lägg till ett omfång.  Portalen kan kräva att du skapar en program-ID-URI också. 

> [!IMPORTANT]
> Du behöver hemligheten för att konfigurera program inställningarna i din implementering. Det här hemliga värdet visas inte igen och kan inte hämtas på annat sätt. Registrera den så snart den visas i Azure Portal.

### <a name="register-the-client-application"></a>Registrera klient programmet

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. I det översta fältet väljer du ditt konto och tittar under **katalog** listan för att välja en Active Directory klient för ditt program.
1. Välj **fler tjänster** i den vänstra rutan och välj **Azure Active Directory**.
1. Välj **Appregistreringar** och sedan **ny registrering**.
1. Ange ett eget namn för programmet och välj program typ.
1. Under **Kontotyper som stöds** väljer du **Accounts in any organizational directory and personal Microsoft accounts** (Konton i alla organisationskataloger och personliga Microsoft-konton).
1. Ange omdirigerings-URI: n till bas-URL: en.
1. Välj **Registrera** för att skapa programmet.
1. Konfigurera behörigheter för ditt program. I **API-behörigheter** väljer du **Lägg till en behörighet** och sedan **Mina API: er**.
1. Skriv namnet på mellan nivå tjänsten i textfältet.
1. Välj **Välj behörigheter** och välj sedan den omfattning som du skapade i det sista steget för att registrera den mittersta nivån.

### <a name="configure-known-client-applications"></a>Konfigurera kända klient program

I det här scenariot måste mellanskikts tjänsten erhålla användarens medgivande för att få åtkomst till underordnad API utan användar interaktion. Alternativet för att bevilja åtkomst till det underordnade API: t måste visas fram som en del av godkännande steget under autentiseringen.

Följ stegen nedan för att explicit binda klient appens registrering i Azure AD med tjänstens registrering på mellan nivå. Den här åtgärden sammanfogar godkännandet som krävs av både klienten och mellan nivån i en enda dialog ruta.

1. Gå till tjänst registrerings tjänsten mellan nivå och välj **manifest** för att öppna manifest redigeraren.
1. Leta upp `knownClientApplications` mat ris egenskapen och Lägg till klient-ID: t för klient programmet som ett-element.
1. Spara manifestet genom att välja **Spara**.

## <a name="service-to-service-access-token-request"></a>Begäran om tjänst-till-tjänst-åtkomsttoken

Om du vill begära en åtkomsttoken gör du ett HTTP-inlägg till den klient-/regionsspecifika Azure AD-slutpunkten med följande parametrar:

```
https://login.microsoftonline.com/<tenant>/oauth2/token
```

Klient programmet skyddas antingen av en delad hemlighet eller av ett certifikat.

### <a name="first-case-access-token-request-with-a-shared-secret"></a>Första fallet: begäran om åtkomsttoken med en delad hemlighet

När du använder en delad hemlighet innehåller en begäran om tjänst-till-tjänst-åtkomsttoken följande parametrar:

| Parameter | Typ | Beskrivning |
| --- | --- | --- |
| grant_type |krävs | Typ av Tokenbegäran. En OBO-begäran använder en JSON Web Token (JWT) så värdet måste vara **urn: IETF: params: OAuth: Granting-Type: JWT-Bearer**. |
| Assertion |krävs | Värdet för den åtkomsttoken som används i begäran. |
| client_id |krävs | App-ID som tilldelats den anropande tjänsten under registreringen med Azure AD. Om du vill hitta app-ID: t i Azure Portal väljer du **Active Directory**, väljer katalogen och väljer sedan program namnet. |
| client_secret |krävs | Nyckeln som registrerats för den anropande tjänsten i Azure AD. Det här värdet bör ha noterats vid tidpunkten för registreringen. |
| resource |krävs | App-ID-URI för den mottagande tjänsten (skyddad resurs). Om du vill hitta app-ID-URI: n i Azure Portal väljer du **Active Directory** och väljer katalogen. Välj program namnet, Välj **alla inställningar** och välj sedan **Egenskaper**. |
| requested_token_use |krävs | Anger hur begäran ska bearbetas. I flödets räkning måste värdet vara **on_behalf_of**. |
| omfång |krävs | En blankstegsavgränsad lista över omfång för Tokenbegäran. För OpenID Connect måste omfångs- **OpenID** anges.|

#### <a name="example"></a>Exempel

Följande HTTP POST begär en åtkomsttoken för https://graph.microsoft.com webb-API: et. `client_id`Identifierar den tjänst som begär åtkomst-token.

```
// line breaks for legibility only

POST /oauth2/token HTTP/1.1
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

grant_type=urn%3Aietf%3Aparams%3Aoauth%3Agrant-type%3Ajwt-bearer
&client_id=625391af-c675-43e5-8e44-edd3e30ceb15
&client_secret=0Y1W%2BY3yYb3d9N8vSjvm8WrGzVZaAaHbHHcGbcgG%2BoI%3D
&resource=https%3A%2F%2Fgraph.microsoft.com
&assertion=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6InowMzl6ZHNGdWl6cEJmQlZLMVRuMjVRSFlPMCIsImtpZCI6InowMzl6ZHNGdWl6cEJmQlZLMVRuMjVRSFlPMCJ9.ewogICJhdWQiOiAiaHR0cHM6Ly9ncmFwaC5taWNyb3NvZnQuY29tIiwKICAiaXNzIjogImh0dHBzOi8vc3RzLndpbmRvd3MubmV0LzI2MDM5Y2NlLTQ4OWQtNDAwMi04MjkzLTViMGM1MTM0ZWFjYi8iLAogICJpYXQiOiAxNDkzNDIzMTY4LAogICJuYmYiOiAxNDkzNDIzMTY4LAogICJleHAiOiAxNDkzNDY2OTUxLAogICJhY3IiOiAiMSIsCiAgImFpbyI6ICJBU1FBMi84REFBQUE1NnZGVmp0WlNjNWdBVWwrY1Z0VFpyM0VvV2NvZEoveWV1S2ZqcTZRdC9NPSIsCiAgImFtciI6IFsKICAgICJwd2QiCiAgXSwKICAiYXBwaWQiOiAiNjI1MzkxYWYtYzY3NS00M2U1LThlNDQtZWRkM2UzMGNlYjE1IiwKICAiYXBwaWRhY3IiOiAiMSIsCiAgImVfZXhwIjogMzAyNjgzLAogICJmYW1pbHlfbmFtZSI6ICJUZXN0IiwKICAiZ2l2ZW5fbmFtZSI6ICJOYXZ5YSIsCiAgImlwYWRkciI6ICIxNjcuMjIwLjEuMTc3IiwKICAibmFtZSI6ICJOYXZ5YSBUZXN0IiwKICAib2lkIjogIjFjZDRiY2FjLWI4MDgtNDIzYS05ZTJmLTgyN2ZiYjFiYjczOSIsCiAgInBsYXRmIjogIjMiLAogICJwdWlkIjogIjEwMDMzRkZGQTEyRUQ3RkUiLAogICJzY3AiOiAiVXNlci5SZWFkIiwKICAic3ViIjogIjNKTUlaSWJlYTc1R2hfWHdDN2ZzX0JDc3kxa1l1ekZKLTUyVm1Zd0JuM3ciLAogICJ0aWQiOiAiMjYwMzljY2UtNDg5ZC00MDAyLTgyOTMtNWIwYzUxMzRlYWNiIiwKICAidW5pcXVlX25hbWUiOiAibmF2eWFAZGRvYmFsaWFub3V0bG9vay5vbm1pY3Jvc29mdC5jb20iLAogICJ1cG4iOiAibmF2eWFAZGRvYmFsaWFub3V0bG9vay5vbm1pY3Jvc29mdC5jb20iLAogICJ1dGkiOiAieEN3ZnpoYS1QMFdKUU9MeENHZ0tBQSIsCiAgInZlciI6ICIxLjAiCn0.cqmUVjfVbqWsxJLUI1Z4FRx1mNQAHP-L0F4EMN09r8FY9bIKeO-0q1eTdP11Nkj_k4BmtaZsTcK_mUygdMqEp9AfyVyA1HYvokcgGCW_Z6DMlVGqlIU4ssEkL9abgl1REHElPhpwBFFBBenOk9iHddD1GddTn6vJbKC3qAaNM5VarjSPu50bVvCrqKNvFixTb5bbdnSz-Qr6n6ACiEimiI1aNOPR2DeKUyWBPaQcU5EAK0ef5IsVJC1yaYDlAcUYIILMDLCD9ebjsy0t9pj_7lvjzUSrbMdSCCdzCqez_MSNxrk1Nu9AecugkBYp3UVUZOIyythVrj6-sVvLZKUutQ
&requested_token_use=on_behalf_of
&scope=openid
```

### <a name="second-case-access-token-request-with-a-certificate"></a>Andra fall: åtkomsttoken för begäran med ett certifikat

En Tokenbegäran för tjänst-till-tjänst-begäran med ett certifikat innehåller följande parametrar:

| Parameter | Typ | Beskrivning |
| --- | --- | --- |
| grant_type |krävs | Typ av Tokenbegäran. En OBO-begäran använder en JWT-åtkomsttoken så att värdet måste vara **urn: IETF: params: OAuth: Granting-Type: JWT-Bearer**. |
| Assertion |krävs | Värdet för den token som används i begäran. |
| client_id |krävs | App-ID som tilldelats den anropande tjänsten under registreringen med Azure AD. Om du vill hitta app-ID: t i Azure Portal väljer du **Active Directory**, väljer katalogen och väljer sedan program namnet. |
| client_assertion_type |krävs |Värdet måste vara `urn:ietf:params:oauth:client-assertion-type:jwt-bearer` |
| client_assertion |krävs | En JSON Web Token som du skapar och signerar med det certifikat som du har registrerat som autentiseringsuppgifter för ditt program. Se  [autentiseringsuppgifter för certifikat](../develop/active-directory-certificate-credentials.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json) för att lära dig om intygs format och om hur du registrerar ditt certifikat.|
| resource |krävs | App-ID-URI för den mottagande tjänsten (skyddad resurs). Om du vill hitta app-ID-URI: n i Azure Portal väljer du **Active Directory** och väljer katalogen. Välj program namnet, Välj **alla inställningar** och välj sedan **Egenskaper**. |
| requested_token_use |krävs | Anger hur begäran ska bearbetas. I flödets räkning måste värdet vara **on_behalf_of**. |
| omfång |krävs | En blankstegsavgränsad lista över omfång för Tokenbegäran. För OpenID Connect måste omfångs- **OpenID** anges.|

Dessa parametrar är nästan desamma som med begäran av delad hemlighet, förutom att `client_secret parameter` har ersatts av två parametrar: `client_assertion_type` och `client_assertion` .

#### <a name="example"></a>Exempel

Följande HTTP POST begär en åtkomsttoken för https://graph.microsoft.com webb-API: et med ett certifikat. `client_id`Identifierar den tjänst som begär åtkomst-token.

```
// line breaks for legibility only

POST /oauth2/token HTTP/1.1
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

grant_type=urn%3Aietf%3Aparams%3Aoauth%3Agrant-type%3Ajwt-bearer
&client_id=625391af-c675-43e5-8e44-edd3e30ceb15
&client_assertion_type=urn%3Aietf%3Aparams%3Aoauth%3Aclient-assertion-type%3Ajwt-bearer
&client_assertion=eyJhbGciOiJSUzI1NiIsIng1dCI6Imd4OHRHeXN5amNScUtqRlBuZDdSRnd2d1pJMCJ9.eyJ{a lot of characters here}M8U3bSUKKJDEg
&resource=https%3A%2F%2Fgraph.microsoft.com
&assertion=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6InowMzl6ZHNGdWl6cEJmQlZLMVRuMjVRSFlPMCIsImtpZCI6InowMzl6ZHNGdWl6cEJmQlZLMVRuMjVRSFlPMCJ9.eyJhdWQiOiJodHRwczovL2Rkb2JhbGlhbm91dGxvb2sub25taWNyb3NvZnQuY29tLzE5MjNmODYyLWU2ZGMtNDFhMy04MWRhLTgwMmJhZTAwYWY2ZCIsImlzcyI6Imh0dHBzOi8vc3RzLndpbmRvd3MubmV0LzI2MDM5Y2NlLTQ4OWQtNDAwMi04MjkzLTViMGM1MTM0ZWFjYi8iLCJpYXQiOjE0OTM0MjMxNTIsIm5iZiI6MTQ5MzQyMzE1MiwiZXhwIjoxNDkzNDY2NjUyLCJhY3IiOiIxIiwiYWlvIjoiWTJaZ1lCRFF2aTlVZEc0LzM0L3dpQndqbjhYeVp4YmR1TFhmVE1QeG8yYlN2elgreHBVQSIsImFtciI6WyJwd2QiXSwiYXBwaWQiOiJiMzE1MDA3OS03YmViLTQxN2YtYTA2YS0zZmRjNzhjMzI1NDUiLCJhcHBpZGFjciI6IjAiLCJlX2V4cCI6MzAyNDAwLCJmYW1pbHlfbmFtZSI6IlRlc3QiLCJnaXZlbl9uYW1lIjoiTmF2eWEiLCJpcGFkZHIiOiIxNjcuMjIwLjEuMTc3IiwibmFtZSI6Ik5hdnlhIFRlc3QiLCJvaWQiOiIxY2Q0YmNhYy1iODA4LTQyM2EtOWUyZi04MjdmYmIxYmI3MzkiLCJwbGF0ZiI6IjMiLCJzY3AiOiJ1c2VyX2ltcGVyc29uYXRpb24iLCJzdWIiOiJEVXpYbkdKMDJIUk0zRW5pbDFxdjZCakxTNUllQy0tQ2ZpbzRxS1MzNEc4IiwidGlkIjoiMjYwMzljY2UtNDg5ZC00MDAyLTgyOTMtNWIwYzUxMzRlYWNiIiwidW5pcXVlX25hbWUiOiJuYXZ5YUBkZG9iYWxpYW5vdXRsb29rLm9ubWljcm9zb2Z0LmNvbSIsInVwbiI6Im5hdnlhQGRkb2JhbGlhbm91dGxvb2sub25taWNyb3NvZnQuY29tIiwidmVyIjoiMS4wIn0.R-Ke-XO7lK0r5uLwxB8g5CrcPAwRln5SccJCfEjU6IUqpqcjWcDzeDdNOySiVPDU_ZU5knJmzRCF8fcjFtPsaA4R7vdIEbDuOur15FXSvE8FvVSjP_49OH6hBYqoSUAslN3FMfbO6Z8YfCIY4tSOB2I6ahQ_x4ZWFWglC3w5mK-_4iX81bqi95eV4RUKefUuHhQDXtWhrSgIEC0YiluMvA4TnaJdLq_tWXIc4_Tq_KfpkvI004ONKgU7EAMEr1wZ4aDcJV2yf22gQ1sCSig6EGSTmmzDuEPsYiyd4NhidRZJP4HiiQh-hePBQsgcSgYGvz9wC6n57ufYKh2wm_Ti3Q
&requested_token_use=on_behalf_of
&scope=openid
```

## <a name="service-to-service-access-token-response"></a>Svar på tjänst-till-tjänst-åtkomsttoken

Ett lyckat svar är ett JSON OAuth 2,0-svar med följande parametrar:

| Parameter | Beskrivning |
| --- | --- |
| token_type |Anger värdet för token-typ. Den enda typ som Azure AD stöder är **Bearer**. Mer information om Bearer-token finns i [OAuth 2,0 Authorization Framework: Bearer token Usage (RFC 6750)](https://www.rfc-editor.org/rfc/rfc6750.txt). |
| omfång |Omfattningen av åtkomst som beviljats i token. |
| expires_in |Hur lång tid som åtkomsttoken är giltig (i sekunder). |
| expires_on |Tiden då åtkomsttoken upphör att gälla. Datumet visas som antalet sekunder från 1970-01-01T0:0: 0Z UTC fram till förfallo tiden. Det här värdet används för att fastställa livs längden för cachelagrade token. |
| resource |App-ID-URI för den mottagande tjänsten (skyddad resurs). |
| access_token |Den begärda åtkomsttoken. Anrops tjänsten kan använda denna token för att autentisera till den mottagande tjänsten. |
| id_token |Begärd ID-token. Anrops tjänsten kan använda denna token för att verifiera användarens identitet och påbörja en session med användaren. |
| refresh_token |Uppdateringstoken för den begärda åtkomsttoken. Anrops tjänsten kan använda denna token för att begära en annan åtkomsttoken när den aktuella åtkomsttoken upphör att gälla. |

### <a name="success-response-example"></a>Exempel på slutfört svar

I följande exempel visas ett lyckat svar på en begäran om en åtkomsttoken för https://graph.microsoft.com webb-API: et.

```json
{
    "token_type":"Bearer",
    "scope":"User.Read",
    "expires_in":"43482",
    "ext_expires_in":"302683",
    "expires_on":"1493466951",
    "not_before":"1493423168",
    "resource":"https://graph.microsoft.com",
    "access_token":"eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6InowMzl6ZHNGdWl6cEJmQlZLMVRuMjVRSFlPMCIsImtpZCI6InowMzl6ZHNGdWl6cEJmQlZLMVRuMjVRSFlPMCJ9.eyJhdWQiOiJodHRwczovL2dyYXBoLndpbmRvd3MubmV0IiwiaXNzIjoiaHR0cHM6Ly9zdHMud2luZG93cy5uZXQvMjYwMzljY2UtNDg5ZC00MDAyLTgyOTMtNWIwYzUxMzRlYWNiLyIsImlhdCI6MTQ5MzQyMzE2OCwibmJmIjoxNDkzNDIzMTY4LCJleHAiOjE0OTM0NjY5NTEsImFjciI6IjEiLCJhaW8iOiJBU1FBMi84REFBQUE1NnZGVmp0WlNjNWdBVWwrY1Z0VFpyM0VvV2NvZEoveWV1S2ZqcTZRdC9NPSIsImFtciI6WyJwd2QiXSwiYXBwaWQiOiI2MjUzOTFhZi1jNjc1LTQzZTUtOGU0NC1lZGQzZTMwY2ViMTUiLCJhcHBpZGFjciI6IjEiLCJlX2V4cCI6MzAyNjgzLCJmYW1pbHlfbmFtZSI6IlRlc3QiLCJnaXZlbl9uYW1lIjoiTmF2eWEiLCJpcGFkZHIiOiIxNjcuMjIwLjEuMTc3IiwibmFtZSI6Ik5hdnlhIFRlc3QiLCJvaWQiOiIxY2Q0YmNhYy1iODA4LTQyM2EtOWUyZi04MjdmYmIxYmI3MzkiLCJwbGF0ZiI6IjMiLCJwdWlkIjoiMTAwMzNGRkZBMTJFRDdGRSIsInNjcCI6IlVzZXIuUmVhZCIsInN1YiI6IjNKTUlaSWJlYTc1R2hfWHdDN2ZzX0JDc3kxa1l1ekZKLTUyVm1Zd0JuM3ciLCJ0aWQiOiIyNjAzOWNjZS00ODlkLTQwMDItODI5My01YjBjNTEzNGVhY2IiLCJ1bmlxdWVfbmFtZSI6Im5hdnlhQGRkb2JhbGlhbm91dGxvb2sub25taWNyb3NvZnQuY29tIiwidXBuIjoibmF2eWFAZGRvYmFsaWFub3V0bG9vay5vbm1pY3Jvc29mdC5jb20iLCJ1dGkiOiJ4Q3dmemhhLVAwV0pRT0x4Q0dnS0FBIiwidmVyIjoiMS4wIn0.cqmUVjfVbqWsxJLUI1Z4FRx1mNQAHP-L0F4EMN09r8FY9bIKeO-0q1eTdP11Nkj_k4BmtaZsTcK_mUygdMqEp9AfyVyA1HYvokcgGCW_Z6DMlVGqlIU4ssEkL9abgl1REHElPhpwBFFBBenOk9iHddD1GddTn6vJbKC3qAaNM5VarjSPu50bVvCrqKNvFixTb5bbdnSz-Qr6n6ACiEimiI1aNOPR2DeKUyWBPaQcU5EAK0ef5IsVJC1yaYDlAcUYIILMDLCD9ebjsy0t9pj_7lvjzUSrbMdSCCdzCqez_MSNxrk1Nu9AecugkBYp3UVUZOIyythVrj6-sVvLZKUutQ",
    "refresh_token":"AQABAAAAAABnfiG-mA6NTae7CdWW7QfdjKGu9-t1scy_TDEmLi4eLQMjJGt_nAoVu6A4oSu1KsRiz8XyQIPKQxSGfbf2FoSK-hm2K8TYzbJuswYusQpJaHUQnSqEvdaCeFuqXHBv84wjFhuanzF9dQZB_Ng5za9xKlUENrNtlq9XuLNVKzxEyeUM7JyxzdY7JiEphWImwgOYf6II316d0Z6-H3oYsFezf4Xsjz-MOBYEov0P64UaB5nJMvDyApV-NWpgklLASfNoSPGb67Bc02aFRZrm4kLk-xTl6eKE6hSo0XU2z2t70stFJDxvNQobnvNHrAmBaHWPAcC3FGwFnBOojpZB2tzG1gLEbmdROVDp8kHEYAwnRK947Py12fJNKExUdN0njmXrKxNZ_fEM33LHW1Tf4kMX_GvNmbWHtBnIyG0w5emb-b54ef5AwV5_tGUeivTCCysgucEc-S7G8Cz0xNJ_BOiM_4bAv9iFmrm9STkltpz0-Tftg8WKmaJiC0xXj6uTf4ZkX79mJJIuuM7XP4ARIcLpkktyg2Iym9jcZqymRkGH2Rm9sxBwC4eeZXM7M5a7TJ-5CqOdfuE3sBPq40RdEWMFLcrAzFvP0VDR8NKHIrPR1AcUruat9DETmTNJukdlJN3O41nWdZOVoJM-uKN3uz2wQ2Ld1z0Mb9_6YfMox9KTJNzRzcL52r4V_y3kB6ekaOZ9wQ3HxGBQ4zFt-2U0mSszIAA",
    "id_token":"eyJ0eXAiOiJKV1QiLCJhbGciOiJub25lIn0.eyJhdWQiOiI2MjUzOTFhZi1jNjc1LTQzZTUtOGU0NC1lZGQzZTMwY2ViMTUiLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC8yNjAzOWNjZS00ODlkLTQwMDItODI5My01YjBjNTEzNGVhY2IvIiwiaWF0IjoxNDkzNDIzMTY4LCJuYmYiOjE0OTM0MjMxNjgsImV4cCI6MTQ5MzQ2Njk1MSwiYW1yIjpbInB3ZCJdLCJmYW1pbHlfbmFtZSI6IlRlc3QiLCJnaXZlbl9uYW1lIjoiTmF2eWEiLCJpcGFkZHIiOiIxNjcuMjIwLjEuMTc3IiwibmFtZSI6Ik5hdnlhIFRlc3QiLCJvaWQiOiIxY2Q0YmNhYy1iODA4LTQyM2EtOWUyZi04MjdmYmIxYmI3MzkiLCJwbGF0ZiI6IjMiLCJzdWIiOiJEVXpYbkdKMDJIUk0zRW5pbDFxdjZCakxTNUllQy0tQ2ZpbzRxS1MzNEc4IiwidGlkIjoiMjYwMzljY2UtNDg5ZC00MDAyLTgyOTMtNWIwYzUxMzRlYWNiIiwidW5pcXVlX25hbWUiOiJuYXZ5YUBkZG9iYWxpYW5vdXRsb29rLm9ubWljcm9zb2Z0LmNvbSIsInVwbiI6Im5hdnlhQGRkb2JhbGlhbm91dGxvb2sub25taWNyb3NvZnQuY29tIiwidXRpIjoieEN3ZnpoYS1QMFdKUU9MeENHZ0tBQSIsInZlciI6IjEuMCJ9."
}
```

### <a name="error-response-example"></a>Exempel på fel svar

Slut punkten för Azure AD-token returnerar ett felsvar vid försök att hämta en åtkomsttoken för ett underordnat API som anges med en princip för villkorlig åtkomst (till exempel Multi-Factor Authentication). Den mellanliggande tjänsten ska visa det här felet för klient programmet så att klient programmet kan ge användaren interaktion för att uppfylla principen för villkorlig åtkomst.

```json
{
    "error":"interaction_required",
    "error_description":"AADSTS50079: Due to a configuration change made by your administrator, or because you moved to a new location, you must enroll in multi-factor authentication to access 'bf8d80f9-9098-4972-b203-500f535113b1'.\r\nTrace ID: b72a68c3-0926-4b8e-bc35-3150069c2800\r\nCorrelation ID: 73d656cf-54b1-4eb2-b429-26d8165a52d7\r\nTimestamp: 2017-05-01 22:43:20Z",
    "error_codes":[50079],
    "timestamp":"2017-05-01 22:43:20Z",
    "trace_id":"b72a68c3-0926-4b8e-bc35-3150069c2800",
    "correlation_id":"73d656cf-54b1-4eb2-b429-26d8165a52d7",
    "claims":"{\"access_token\":{\"polids\":{\"essential\":true,\"values\":[\"9ab03e19-ed42-4168-b6b7-7001fb3e933a\"]}}}"
}
```

## <a name="use-the-access-token-to-access-the-secured-resource"></a>Använd åtkomsttoken för att få åtkomst till den skyddade resursen

Den mellanliggande tjänsten kan använda den hämtade åtkomsttoken för att göra autentiserade begär anden till den underordnade webb-API: n genom att ange token i `Authorization` rubriken.

### <a name="example"></a>Exempel

```
GET /me?api-version=2013-11-08 HTTP/1.1
Host: graph.microsoft.com
Authorization: Bearer eyJ0eXAiO ... 0X2tnSQLEANnSPHY0gKcgw
```

## <a name="saml-assertions-obtained-with-an-oauth20-obo-flow"></a>SAML-kontroller som hämtats med ett OAuth 2.0 OBO-flöde

Vissa OAuth-baserade webb tjänster behöver åtkomst till andra webb tjänst-API: er som accepterar SAML-kontroller i icke-interaktiva flöden. Azure Active Directory kan tillhandahålla en SAML-kontroll som svar på ett ingångs flöde som använder en SAML-baserad webb tjänst som mål resurs.

>[!NOTE]
>Det här är ett tillägg som inte är standard för OAuth 2,0 på ett flöde som gör det möjligt för ett OAuth2 program att få åtkomst till webb tjänstens API-slutpunkter som använder SAML-token.

> [!TIP]
> När du anropar en SAML-skyddad webb tjänst från ett klient webb program kan du bara anropa API: et och initiera ett normalt interaktivt autentiseringsschema med användarens befintliga session. Du behöver bara använda ett OBO-flöde när ett tjänst-till-tjänst-anrop kräver en SAML-token för att tillhandahålla användar kontext.

### <a name="obtain-a-saml-token-by-using-an-obo-request-with-a-shared-secret"></a>Hämta en SAML-token genom att använda en OBO-begäran med en delad hemlighet

En tjänst-till-tjänst-begäran för en SAML-kontroll innehåller följande parametrar:

| Parameter | Typ | Beskrivning |
| --- | --- | --- |
| grant_type |krävs | Typ av Tokenbegäran. För en begäran som använder en JWT måste värdet vara **urn: IETF: params: OAuth: Grant-Type: JWT-Bearer**. |
| Assertion |krävs | Värdet för den åtkomsttoken som används i begäran.|
| client_id |krävs | App-ID som tilldelats den anropande tjänsten under registreringen med Azure AD. Om du vill hitta app-ID: t i Azure Portal väljer du **Active Directory**, väljer katalogen och väljer sedan program namnet. |
| client_secret |krävs | Nyckeln som registrerats för den anropande tjänsten i Azure AD. Det här värdet bör ha noterats vid tidpunkten för registreringen. |
| resource |krävs | App-ID-URI för den mottagande tjänsten (skyddad resurs). Detta är den resurs som ska vara mål gruppen för SAML-token. Om du vill hitta app-ID-URI: n i Azure Portal väljer du **Active Directory** och väljer katalogen. Välj program namnet, Välj **alla inställningar** och välj sedan **Egenskaper**. |
| requested_token_use |krävs | Anger hur begäran ska bearbetas. I flödets räkning måste värdet vara **on_behalf_of**. |
| requested_token_type | krävs | Anger vilken typ av token som begärdes. Värdet kan vara **urn: IETF: params: OAuth: token-Type: SAML2** eller **urn: IETF: params: OAuth: token-Type: saml1** beroende på kraven för den åtkomst resursen. |

Svaret innehåller en SAML-token som är kodad i UTF8 och Base64url.

- **SubjectConfirmationData för en SAML-kontroll från ett OBO-anrop**: om mål programmet kräver ett mottagar värde i **SubjectConfirmationData** måste värdet vara en svars-URL som inte är jokertecken i resurs programmets konfiguration.
- **SubjectConfirmationData-noden**: noden får inte innehålla ett **InResponseTo** -attribut eftersom den inte är en del av ett SAML-svar. Programmet som tar emot SAML-token måste kunna acceptera SAML-kontrollen utan ett **InResponseTo** -attribut.

- **Medgivande**: medgivande måste ha beviljats för att ta emot en SAML-token som innehåller användar data i ett OAuth-flöde. Information om behörigheter och hur du får administratörs tillstånd finns [i behörigheter och medgivande i Azure Active Directory v 1.0-slutpunkten](./v1-permissions-consent.md).

### <a name="response-with-saml-assertion"></a>Svar med SAML-kontroll

| Parameter | Beskrivning |
| --- | --- |
| token_type |Anger värdet för token-typ. Den enda typ som Azure AD stöder är **Bearer**. Mer information om Bearer-token finns i [OAuth 2,0 Authorization Framework: syntax för användning av token (RFC 6750)](https://www.rfc-editor.org/rfc/rfc6750.txt). |
| omfång |Omfattningen av åtkomst som beviljats i token. |
| expires_in |Hur lång tid som åtkomsttoken är giltig (i sekunder). |
| expires_on |Tiden då åtkomsttoken upphör att gälla. Datumet visas som antalet sekunder från 1970-01-01T0:0: 0Z UTC fram till förfallo tiden. Det här värdet används för att fastställa livs längden för cachelagrade token. |
| resource |App-ID-URI för den mottagande tjänsten (skyddad resurs). |
| access_token |Den parameter som returnerar SAML Assertion. |
| refresh_token |Refresh-token. Anrops tjänsten kan använda denna token för att begära en annan åtkomsttoken när den aktuella SAML-kontrollen upphör att gälla. |

- token_type: Bearer
- expires_in: 3296
- ext_expires_in: 0
- expires_on: 1529627844
- klusterresursen `https://api.contoso.com`
- access_token: \<SAML assertion\>
- issued_token_type: urn: IETF: params: OAuth: token-Type: SAML2
- refresh_token: \<Refresh token\>

## <a name="client-limitations"></a>Klient begränsningar

Offentliga klienter med URL: er med jokertecken kan inte använda ett `id_token` for OBO-flöde. En konfidentiell klient kan dock fortfarande lösa in **åtkomsttoken** som erhållits via det implicita bidraget, även om den offentliga klienten har en omdirigerings-URI registrerad.

## <a name="next-steps"></a>Nästa steg

Läs mer om OAuth 2,0-protokollet och ett annat sätt att utföra tjänst-till-tjänst-autentisering som använder klientautentiseringsuppgifterna:

* [Autentisering av tjänst till tjänst med OAuth 2,0-klientautentiseringsuppgifter i Azure AD](v1-oauth2-client-creds-grant-flow.md)
* [OAuth 2,0 i Azure AD](v1-protocols-oauth-code.md)