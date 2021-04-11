---
title: Kommunikations exempel för Issuer service (för hands version) – Azure Active Directory verifierbara autentiseringsuppgifter
description: Information om kommunikation mellan identitets leverantör och Issuer-tjänsten
author: barclayn
manager: davba
ms.service: identity
ms.subservice: verifiable-credentials
ms.workload: identity
ms.topic: conceptual
ms.date: 04/01/2021
ms.author: barclayn
ms.openlocfilehash: 8771c61f96b244e0cc0bca1c61ceb8042b4a5b4c
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/02/2021
ms.locfileid: "106220206"
---
# <a name="issuer-service-communication-examples-preview"></a>Kommunikations exempel för Issuer service (för hands version)

Tjänsten för verifierbara autentiseringsuppgifter kan utfärda verifierbara autentiseringsuppgifter genom att hämta anspråk från en ID-token som genererats av din organisations OpenID-kompatibla identitets leverantör. Den här artikeln innehåller anvisningar om hur du konfigurerar din identitetsprovider så att autentiseraren kan kommunicera med den och hämta rätt ID-token för att skicka den till den utfärdande tjänsten. 

> [!IMPORTANT]
> Azure Active Directory verifierbara autentiseringsuppgifter finns för närvarande i en offentlig för hands version.
> Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).


För att utfärda verifierbara autentiseringsuppgifter instrueras autentiseraren genom att ladda ned kontraktet för att samla in indata från användaren och skicka informationen till den utfärdande tjänsten. Om du behöver använda en ID-token måste du konfigurera din identitetsprovider så att autentiseraren kan logga in en användare med hjälp av OpenID Connect-protokollet. Anspråk i den resulterande ID-token används för att fylla i innehållet i dina verifierbara autentiseringsuppgifter. Autentiseraren autentiserar användaren med hjälp av OpenID koppla kod flöde för auktoriseringskod. OpenID-providern måste ha stöd för följande OpenID Connect-funktioner: 

| Funktion | Beskrivning |
| ------- | ----------- |
| Typ av beviljande | Måste ha stöd för beviljande typen för auktoriseringskod. |
| Token-format | Måste skapa okrypterade Compact-JWTs. |
| Signaturalgoritm | Måste skapa JWTs signerade med RSA 256. |
| Konfigurations dokument | Måste ha stöd för OpenID Connect konfigurations dokument och `jwks_uri` . | 
| Klient registrering | Måste stödja offentlig klient registrering med `redirect_uri` värdet `vclient://openid/` . | 
| PKCE | Rekommenderas av säkerhets skäl, men krävs inte. |

Exempel på HTTP-begäranden som skickas till din identitetsprovider finns nedan. Din identitetsprovider måste acceptera och svara på dessa förfrågningar enligt OpenID Connect Authentication standard.

## <a name="client-registration"></a>Klient registrering

För att få en verifierbar autentiseringsuppgift måste användarna logga in på din IDP från Microsoft Authenticator-appen. 

Om du vill aktivera det här utbytet registrerar du ett program hos identitets leverantören. Om du använder Azure AD hittar du instruktionerna [här](../develop/quickstart-register-app.md). Använd följande värden vid registrering.

| Inställning | Värde |
| ------- | ----- |
| Programnamn | `<Issuer Name> Verifiable Credential Service` |
| Omdirigerings-URI | `vcclient://openid/ ` |


När du har registrerat ett program med din identitetsprovider registrerar du klient-ID. Du kommer att använda den i avsnittet som följer. Du måste också skriva ned URL: en till den välkända slut punkten för den OIDC-kompatibla identitets leverantören. Den utfärdande tjänsten använder den här slut punkten för att hämta de offentliga nycklar som behövs för att validera ID-token när den skickas av autentiseraren.

Den konfigurerade omdirigerings-URI: n används av autentiseraren så att den vet när inloggningen är slutförd och kan hämta ID-token. 

## <a name="authorization-request"></a>Auktoriseringsbegäran

Auktoriseringsbegäran som skickas till din identitetsprovider använder följande format.

```HTTP
GET /authorize?client_id=<client-id>&redirect_uri=portableidentity%3A%2F%2Fverify&response_mode=query&response_type=code&scope=openid&state=12345&nonce=12345 HTTP/1.1
Host: www.contoso.com
Connection: Keep-Alive
```

| Parameter | Värde |
| ------- | ----------- |
| `client_id` | Det klient-ID som hämtades under program registrerings processen. |
| `redirect_uri` | Måste använda `vcclient://openid/` . |
| `response_mode` | Måste ha stöd för `query` . |
| `response_type` | Måste ha stöd för `code` . |
| `scope` | Måste ha stöd för `openid` . |
| `state` | Måste returneras till klienten enligt OpenID Connect standard. |
| `nonce` | Måste returneras som ett anspråk i ID-token enligt OpenID Connect standard. |

När den får en auktoriseringsbegäran bör din identitetsprovider autentisera användaren och vidta nödvändiga åtgärder för att slutföra inloggningen, till exempel Multi-Factor Authentication.

Du kan anpassa inloggnings processen så att den passar dina behov. Du kan be användarna att ange ytterligare information, godkänna användnings villkoren, betala för sina autentiseringsuppgifter och mycket annat. När alla steg är klara, svarar du på auktoriseringsbegäran genom att omdirigera till omdirigerings-URI: n enligt nedan. 

```HTTP
vcclient://openid/?code=nbafhjbh1ub1yhbj1h4jr1&state=12345
```

| Parameter | Värde |
| ------- | ----------- |
| `code` |  Den auktoriseringskod som identitets leverantören returnerade. |
| `state` | Måste returneras till klienten enligt OpenID Connect standard. |

## <a name="token-request"></a>Tokenbegäran

Den Tokenbegäran som skickas till din identitetsprovider har följande formulär.

```HTTP
POST /token HTTP/1.1
Host: www.contoso.com
Content-Type: application/x-www-form-urlencoded
Content-Length: 291

client_id=<client-id>&redirect_uri=vcclient%3A%2F%2Fopenid%2F&grant_type=authorization_code&code=nbafhjbh1ub1yhbj1h4jr1&scope=openid
```

| Parameter | Värde |
| ------- | ----------- |
| `client_id` | Det klient-ID som hämtades under program registrerings processen. |
| `redirect_uri` | Måste använda `vcclient://openid/` . |
| `scope` | Måste ha stöd för `openid` . |
| `grant_type` | Måste ha stöd för `authorization_code` . |
| `code` | Den auktoriseringskod som identitets leverantören returnerade. |

När du tar emot Tokenbegäran bör din identitetsprovider svara med en ID-token.

```HTTP
HTTP/1.1 200 OK
Content-Type: application/json
Cache-Control: no-store
Pragma: no-cache

{
"id_token": "eyJhbGciOiJSUzI1NiIsImtpZCI6IjFlOWdkazcifQ.ewogImlzc
    yI6ICJodHRwOi8vc2VydmVyLmV4YW1wbGUuY29tIiwKICJzdWIiOiAiMjQ4Mjg5
    NzYxMDAxIiwKICJhdWQiOiAiczZCaGRSa3F0MyIsCiAibm9uY2UiOiAibi0wUzZ
    fV3pBMk1qIiwKICJleHAiOiAxMzExMjgxOTcwLAogImlhdCI6IDEzMTEyODA5Nz
    AKfQ.ggW8hZ1EuVLuxNuuIJKX_V8a_OMXzR0EHR9R6jgdqrOOF4daGU96Sr_P6q
    Jp6IcmD3HP99Obi1PRs-cwh3LO-p146waJ8IhehcwL7F09JdijmBqkvPeB2T9CJ
    NqeGpe-gccMg4vfKjkM8FcGvnzZUN4_KSP0aAp1tOJ1zZwgjxqGByKHiOtX7Tpd
    QyHE5lcMiKPXfEIQILVq0pc_E2DzL7emopWoaoZTF_m0_N0YzFC6g6EJbOEoRoS
    K5hoDalrcvRYLSrQAZZKflyuVCyixEoV9GfNQC3_osjzw2PAithfubEEBLuVVk4
    XUVrWOLrLl0nx7RkKU8NXNHq-rvKMzqg"
}
```

ID-token måste använda formatet för JWT Compact-serialisering och får inte vara krypterat. ID-token ska innehålla följande anspråk.

| Begär | Värde |
| ------- | ----------- |
| `kid` | Nyckel identifieraren för den nyckel som används för att signera ID-token som motsvarar en post i OpenID-providern `jwks_uri` . |
| `aud` | Det klient-ID som hämtades under program registrerings processen. |
| `iss` | Måste vara `issuer` värdet i OpenID Connect-konfigurationsobjektet. |
| `exp` | Måste innehålla förfallo tiden för ID-token. |
| `iat` | Måste innehålla den tid då ID-token utfärdades. |
| `nonce` | Värdet som ingår i auktoriseringsbegäran. |
| Ytterligare anspråk | ID-token ska innehålla eventuella ytterligare anspråk vars värden ska inkluderas i de verifierbara autentiseringsuppgifter som ska utfärdas. I det här avsnittet ska du ta med alla attribut för användaren, till exempel deras namn. |

## <a name="next-steps"></a>Nästa steg

- [Anpassa dina Azure Active Directory verifierbara autentiseringsuppgifter](credential-design.md)