---
title: Azure AD-tjänst till tjänst-autentisering med OAuth 2.0 | Microsoft Docs
description: Den här artikeln beskriver hur du använder HTTP-meddelanden för att implementera tjänst-till-tjänst-autentisering med hjälp av OAuth 2.0-klientens autentiseringsuppgifter för utfärdande.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: azuread-dev
ms.workload: identity
ms.topic: conceptual
ms.date: 02/08/2017
ms.author: ryanwi
ms.reviewer: nacanuma
ms.custom: aaddev
ROBOTS: NOINDEX
ms.openlocfilehash: 977dfea28c5c0dc3f34ada0c138556d70c979e04
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "85551712"
---
# <a name="service-to-service-calls-using-client-credentials-shared-secret-or-certificate"></a>Tjänst-till-tjänst-anrop med klientautentiseringsuppgifterna (delad hemlighet eller certifikat)

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

Med tilldelningen OAuth 2,0-klientautentiseringsuppgifter tillåts en webb tjänst (*konfidentiell klient*) att använda sina egna autentiseringsuppgifter i stället för att personifiera en användare, för att autentisera vid anrop till en annan webb tjänst. I det här scenariot är klienten vanligt vis en webb tjänst på mellan nivå, en daemon-tjänst eller webbplats. För en högre säkerhets nivå tillåter Azure AD också att anrops tjänsten använder ett certifikat (i stället för en delad hemlighet) som autentiseringsuppgift.

## <a name="client-credentials-grant-flow-diagram"></a>Flödes diagram för tilldelning av klientautentiseringsuppgifter
I följande diagram förklaras hur klientens autentiseringsuppgifter som beviljar flödet fungerar i Azure Active Directory (Azure AD).

![Grant-flöde för autentiseringsuppgifter för OAuth 2.0-klient](./media/v1-oauth2-client-creds-grant-flow/active-directory-protocols-oauth-client-credentials-grant-flow.jpg)

1. Klient programmet autentiserar slut punkten för utfärdande av Azure AD-token och begär en åtkomsttoken.
2. Utfärdande slut punkten för Azure AD-token utfärdar åtkomst-token.
3. Åtkomsttoken används för att autentisera till den skyddade resursen.
4. Data från den skyddade resursen returneras till klient programmet.

## <a name="register-the-services-in-azure-ad"></a>Registrera tjänsterna i Azure AD
Registrera både den anropande tjänsten och den mottagande tjänsten i Azure Active Directory (Azure AD). Detaljerade anvisningar finns i [integrera program med Azure Active Directory](../develop/quickstart-register-app.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json).

## <a name="request-an-access-token"></a>Begär en åtkomsttoken
Om du vill begära en åtkomsttoken använder du ett HTTP-POST till den klient-/regionsspecifika Azure AD-slutpunkten.

```
https://login.microsoftonline.com/<tenant id>/oauth2/token
```

## <a name="service-to-service-access-token-request"></a>Begäran om tjänst-till-tjänst-åtkomsttoken
Det finns två fall beroende på om klient programmet väljer att skyddas av en delad hemlighet eller ett certifikat.

### <a name="first-case-access-token-request-with-a-shared-secret"></a>Första fallet: begäran om åtkomsttoken med en delad hemlighet
När du använder en delad hemlighet innehåller en begäran om tjänst-till-tjänst-åtkomsttoken följande parametrar:

| Parameter | Typ | Beskrivning |
| --- | --- | --- |
| grant_type |krävs |Anger den begärda anslags typen. I ett flöde för autentiseringsuppgifter för klientautentiseringsuppgifter måste värdet vara **client_credentials**. |
| client_id |krävs |Anger Azure AD-klient-ID: t för den anropande webb tjänsten. Du hittar det anropande programmets klient-ID genom att klicka på **Azure Active Directory** i [Azure Portal](https://portal.azure.com), klicka på **Appregistreringar**, klicka på programmet. Client_id är *program-ID: t* |
| client_secret |krävs |Ange en nyckel som registrerats för den anropande webb tjänsten eller daemon-appen i Azure AD. Skapa en nyckel genom att klicka på **Azure Active Directory** i Azure Portal, klicka på **Appregistreringar**, klicka på programmet, klicka på **Inställningar**, klicka på **nycklar** och lägga till en nyckel.  URL – koda denna hemlighet när den tillhandahålls. |
| resource |krävs |Ange app-ID-URI för den mottagande webb tjänsten. Du hittar app-ID-URI: n genom att klicka på **Azure Active Directory** i Azure Portal, klicka på **Appregistreringar**, klicka på tjänst programmet och sedan klicka på **Inställningar** och **Egenskaper**. |

#### <a name="example"></a>Exempel
Följande HTTP POST [begär en åtkomsttoken](../develop/access-tokens.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json) för `https://service.contoso.com/` webb tjänsten. `client_id`Identifierar webb tjänsten som begär åtkomsttoken.

```
POST /contoso.com/oauth2/token HTTP/1.1
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

grant_type=client_credentials&client_id=625bc9f6-3bf6-4b6d-94ba-e97cf07a22de&client_secret=qkDwDJlDfig2IpeuUZYKH1Wb8q1V0ju6sILxQQqhJ+s=&resource=https%3A%2F%2Fservice.contoso.com%2F
```

### <a name="second-case-access-token-request-with-a-certificate"></a>Andra fall: åtkomsttoken för begäran med ett certifikat
En Tokenbegäran för tjänst-till-tjänst-begäran med ett certifikat innehåller följande parametrar:

| Parameter | Typ | Beskrivning |
| --- | --- | --- |
| grant_type |krävs |Anger den begärda svars typen. I ett flöde för autentiseringsuppgifter för klientautentiseringsuppgifter måste värdet vara **client_credentials**. |
| client_id |krävs |Anger Azure AD-klient-ID: t för den anropande webb tjänsten. Du hittar det anropande programmets klient-ID genom att klicka på **Azure Active Directory** i [Azure Portal](https://portal.azure.com), klicka på **Appregistreringar**, klicka på programmet. Client_id är *program-ID: t* |
| client_assertion_type |krävs |Värdet måste vara `urn:ietf:params:oauth:client-assertion-type:jwt-bearer` |
| client_assertion |krävs | En försäkran (en JSON Web Token) som du måste skapa och signera med det certifikat som du har registrerat som autentiseringsuppgifter för ditt program. Läs om [autentiseringsuppgifter för certifikat](../develop/active-directory-certificate-credentials.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json) för att lära dig hur du registrerar ditt certifikat och formatet på intyget.|
| resource | krävs |Ange app-ID-URI för den mottagande webb tjänsten. Du hittar app-ID-URI: n genom att klicka på **Azure Active Directory** i Azure Portal, klicka på **Appregistreringar**, klicka på tjänst programmet och sedan klicka på **Inställningar** och **Egenskaper**. |

Observera att parametrarna är nästan desamma som i fallet med delad hemlighet, förutom att parametern client_secret ersätts av två parametrar: en client_assertion_type och client_assertion.

#### <a name="example"></a>Exempel
Följande HTTP POST begär en åtkomsttoken för `https://service.contoso.com/` webb tjänsten med ett certifikat. `client_id`Identifierar webb tjänsten som begär åtkomsttoken.

```
POST /<tenant_id>/oauth2/token HTTP/1.1
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

resource=https%3A%2F%contoso.onmicrosoft.com%2Ffc7664b4-cdd6-43e1-9365-c2e1c4e1b3bf&client_id=97e0a5b7-d745-40b6-94fe-5f77d35c6e05&client_assertion_type=urn%3Aietf%3Aparams%3Aoauth%3Aclient-assertion-type%3Ajwt-bearer&client_assertion=eyJhbGciOiJSUzI1NiIsIng1dCI6Imd4OHRHeXN5amNScUtqRlBuZDdSRnd2d1pJMCJ9.eyJ{a lot of characters here}M8U3bSUKKJDEg&grant_type=client_credentials
```

### <a name="service-to-service-access-token-response"></a>Svar på tjänst-till-tjänst-åtkomsttoken

Ett lyckat svar innehåller ett JSON OAuth 2,0-svar med följande parametrar:

| Parameter | Beskrivning |
| --- | --- |
| access_token |Den begärda åtkomsttoken. Den anropande webb tjänsten kan använda denna token för att autentisera till den mottagande webb tjänsten. |
| token_type |Anger värdet för token-typ. Den enda typ som Azure AD stöder är **Bearer**. Mer information om Bearer-token finns i [OAuth 2,0 Authorization Framework: Bearer token Usage (RFC 6750)](https://www.rfc-editor.org/rfc/rfc6750.txt). |
| expires_in |Hur länge åtkomsttoken är giltig (i sekunder). |
| expires_on |Tiden då åtkomsttoken upphör att gälla. Datumet visas som antalet sekunder från 1970-01-01T0:0: 0Z UTC fram till förfallo tiden. Det här värdet används för att fastställa livs längden för cachelagrade token. |
| not_before |Tiden som åtkomsttoken blir användbar. Datumet visas som antalet sekunder från 1970-01-01T0:0: 0Z UTC tills giltighets tiden för token.|
| resource |App-ID-URI för den mottagande webb tjänsten. |

#### <a name="example-of-response"></a>Exempel på svar
I följande exempel visas ett lyckat svar på en begäran om en åtkomsttoken till en webb tjänst.

```
{
"access_token":"eyJ0eXAiO ... 0X2tnSQLEANnSPHY0gKcgw",
"token_type":"Bearer",
"expires_in":"3599",
"expires_on":"1388452167",
"resource":"https://service.contoso.com/"
}
```
## <a name="use-the-access-token-to-access-the-secured-resource"></a>Använd åtkomsttoken för att få åtkomst till den skyddade resursen

Tjänsten kan använda den hämtade åtkomsttoken för att göra autentiserade begär anden till den underordnade webb-API: n genom att ange token i `Authorization` rubriken.

### <a name="example"></a>Exempel

```
GET /me?api-version=2013-11-08 HTTP/1.1
Host: graph.microsoft.com
Authorization: Bearer eyJ0eXAiO ... 0X2tnSQLEANnSPHY0gKcgw
```

## <a name="see-also"></a>Se även
* [OAuth 2,0 i Azure AD](v1-protocols-oauth-code.md)
* [Exempel i c# för tjänst-till-tjänst-anrop med delad hemlighet](https://github.com/Azure-Samples/active-directory-dotnet-daemon) och [exempel i c# för tjänst-till-tjänst-anrop med ett certifikat](https://github.com/Azure-Samples/active-directory-dotnet-daemon-certificate-credential)
