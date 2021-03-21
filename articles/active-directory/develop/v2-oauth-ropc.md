---
title: Logga in med tilldela autentiseringsuppgifter för resurs ägare | Azure
titleSuffix: Microsoft identity platform
description: Stöd för webbläsare-mindre autentiseringar med hjälp av ROPC-tilldelningen för resurs ägarens lösen ord.
services: active-directory
author: hpsin
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 05/18/2020
ms.author: hirsin
ms.reviewer: hirsin
ms.custom: aaddev
ms.openlocfilehash: bf469b79fa532978e904a54f32c80280706ee7cb
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102174588"
---
# <a name="microsoft-identity-platform-and-oauth-20-resource-owner-password-credentials"></a>Autentiseringsuppgifter för Microsoft Identity Platform och OAuth 2,0-resurs ägar lösen ord

Microsoft Identity Platform stöder [OAuth 2,0-ROPC (Resource Owner Password Credential)](https://tools.ietf.org/html/rfc6749#section-4.3)som gör att ett program kan logga in användaren genom att direkt hantera lösen ordet.  Den här artikeln beskriver hur du programmerar direkt mot protokollet i ditt program.  När det är möjligt rekommenderar vi att du använder MSAL (Microsoft Authentication Libraries) i stället för att [Hämta tokens och anropa säkra webb-API: er](authentication-flows-app-scenarios.md#scenarios-and-supported-authentication-flows).  Ta också en titt på de [exempel appar som använder MSAL](sample-v2-code.md).

> [!WARNING]
> Microsoft rekommenderar att du _inte_ använder ROPC-flödet. I de flesta fall är fler säkra alternativ tillgängliga och rekommenderade. Det här flödet kräver en mycket hög förtroende nivå i programmet och medför risker som inte finns i andra flöden. Du bör endast använda det här flödet när det inte går att använda andra säkrare flöden.

> [!IMPORTANT]
>
> * Microsoft Identity Platform stöder endast ROPC för Azure AD-klienter, inte personliga konton. Det innebär att du måste använda en klient-/regionsspecifika slut punkt ( `https://login.microsoftonline.com/{TenantId_or_Name}` ) eller `organizations` slut punkten.
> * Personliga konton som bjuds in till en Azure AD-klient kan inte använda ROPC.
> * Konton som inte har lösen ord kan inte logga in via ROPC. I det här scenariot rekommenderar vi att du använder ett annat flöde för appen i stället.
> * Om användarna behöver använda [Multi-Factor Authentication (MFA)](../authentication/concept-mfa-howitworks.md) för att logga in i programmet kommer de att blockeras i stället.
> * ROPC stöds inte i scenarier med [hybrid identitets Federation](../hybrid/whatis-fed.md) (till exempel Azure AD och ADFS som används för att autentisera lokala konton). Om användarna är fulla omdirigerade till en lokal identitets leverantör kan inte Azure AD testa användar namn och lösen ord mot identitets leverantören. [Direktautentisering](../hybrid/how-to-connect-pta.md) stöds med ROPC, men.
> * Ett undantag till ett hybrid identitets Federations scenario skulle vara följande: identifierings principen för hemsfär med AllowCloudPasswordValidation inställt på TRUE kommer att aktivera ROPC-flödet för federerade användare när det lokala lösen ordet synkroniseras till molnet. Mer information finns i [Aktivera direkt ROPC-autentisering av federerade användare för äldre program](../manage-apps/configure-authentication-for-federated-users-portal.md#enable-direct-ropc-authentication-of-federated-users-for-legacy-applications).

## <a name="protocol-diagram"></a>Protokoll diagram

I följande diagram visas ROPC-flödet.

![Diagram som visar autentiseringsuppgifter för resurs ägarens lösen ord](./media/v2-oauth2-ropc/v2-oauth-ropc.svg)

## <a name="authorization-request"></a>Auktoriseringsbegäran

ROPC-flödet är en enskild begäran: den skickar klient identifieringen och användarens autentiseringsuppgifter till IDP och tar sedan emot tokens i retur. Klienten måste begära användarens e-postadress (UPN) och lösen ord innan du gör detta. Omedelbart efter en lyckad begäran bör klienten på ett säkert sätt släppa användarens autentiseringsuppgifter från minnet. Det får aldrig spara dem.

> [!TIP]
> Försök att köra denna begäran i Postman!
> [![Försök att köra denna begäran i Postman](./media/v2-oauth2-auth-code-flow/runInPostman.png)](https://app.getpostman.com/run-collection/f77994d794bab767596d)


```HTTP
// Line breaks and spaces are for legibility only.  This is a public client, so no secret is required.

POST {tenant}/oauth2/v2.0/token
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&scope=user.read%20openid%20profile%20offline_access
&username=MyUsername@myTenant.com
&password=SuperS3cret
&grant_type=password
```

| Parameter | Villkor | Beskrivning |
| --- | --- | --- |
| `tenant` | Krävs | Den katalog klient som du vill logga in användaren i. Detta kan vara i ett GUID eller eget namn format. Den här parametern kan inte anges till `common` eller `consumers` , men den kan vara inställd på `organizations` . |
| `client_id` | Obligatorisk | Det program (klient)-ID som den [Azure Portal-Appregistreringar](https://go.microsoft.com/fwlink/?linkid=2083908) sidan har tilldelats till din app. |
| `grant_type` | Obligatorisk | Måste anges till `password` . |
| `username` | Obligatorisk | Användarens e-postadress. |
| `password` | Obligatorisk | Användarens lösen ord. |
| `scope` | Rekommenderas | En blankstegsavgränsad lista med [omfattningar](v2-permissions-and-consent.md)eller behörigheter som appen kräver. I ett interaktivt flöde måste administratören eller användaren samtycka till dessa omfattningar i förväg. |
| `client_secret`| Krävs ibland | Om din app är en offentlig klient kan `client_secret` eller inte tas `client_assertion` med.  Om appen är en konfidentiell klient måste den tas med. |
| `client_assertion` | Krävs ibland | En annan form av `client_secret` , som genereras med hjälp av ett certifikat.  Se [autentiseringsuppgifter för certifikat](active-directory-certificate-credentials.md) för mer information. |

### <a name="successful-authentication-response"></a>Godkänt autentiserings svar

I följande exempel visas ett lyckat svar på token:

```json
{
    "token_type": "Bearer",
    "scope": "User.Read profile openid email",
    "expires_in": 3599,
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...",
    "refresh_token": "AwABAAAAvPM1KaPlrEqdFSBzjqfTGAMxZGUTdM0t4B4...",
    "id_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJub25lIn0.eyJhdWQiOiIyZDRkMTFhMi1mODE0LTQ2YTctOD..."
}
```

| Parameter | Format | Beskrivning |
| --------- | ------ | ----------- |
| `token_type` | Sträng | Ställ alltid in på `Bearer` . |
| `scope` | Separerade blankstegsavgränsad strängar | Om en åtkomsttoken returnerades listar den här parametern de omfattningar som åtkomsttoken är giltig för. |
| `expires_in`| int | Antal sekunder som den inkluderade åtkomsttoken är giltig för. |
| `access_token`| Ogenomskinlig sträng | Utfärdat för de [omfattningar](v2-permissions-and-consent.md) som begärdes. |
| `id_token` | JWT | Utfärdas om den ursprungliga `scope` parametern omfattade `openid` omfånget. |
| `refresh_token` | Ogenomskinlig sträng | Utfärdas om den ursprungliga `scope` parametern ingår `offline_access` . |

Du kan använda uppdateringstoken för att hämta nya åtkomsttoken och uppdatera tokens med samma flöde som beskrivs i [dokumentationen för OAuth-kod flödet](v2-oauth2-auth-code-flow.md#refresh-the-access-token).

### <a name="error-response"></a>Fel svar

Om användaren inte har angett rätt användar namn eller lösen ord, eller om klienten inte har fått det begärda godkännandet, kommer autentiseringen att Miss sen.

| Fel | Beskrivning | Klient åtgärd |
|------ | ----------- | -------------|
| `invalid_grant` | Autentiseringen misslyckades | Autentiseringsuppgifterna var felaktiga eller så har klienten inte tillstånd för de begärda omfattningarna. Om omfången inte beviljas returneras ett `consent_required` fel. Om detta inträffar ska klienten skicka användaren till en interaktiv prompt med en webbvy eller webbläsare. |
| `invalid_request` | Begäran har inte konstruerats korrekt | Anslags typen stöds inte i `/common` `/consumers` kontexterna eller.  Använd `/organizations` eller ett klient-ID i stället. |

## <a name="learn-more"></a>Läs mer

Ett exempel på hur du använder ROPC finns i program kod exemplet för [.net Core-konsolen](https://github.com/azure-samples/active-directory-dotnetcore-console-up-v2) på GitHub.
