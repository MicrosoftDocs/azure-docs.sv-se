---
title: Exempel på utfärdare av tjänstkommunikation (förhandsversion) – Azure Active Directory verifierbara autentiseringsuppgifter
description: Information om kommunikationen mellan identitetsprovidern och utfärdartjänsten
author: barclayn
manager: davba
ms.service: identity
ms.subservice: verifiable-credentials
ms.workload: identity
ms.topic: conceptual
ms.date: 04/01/2021
ms.author: barclayn
ms.openlocfilehash: 942b77f8338636f9dda5dcf6cd4262dad57b4b0a
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/19/2021
ms.locfileid: "107726276"
---
# <a name="issuer-service-communication-examples-preview"></a>Exempel på utfärdare av tjänstkommunikation (förhandsversion)

Tjänsten Azure AD Verifiable Credential kan utfärda verifierbara autentiseringsuppgifter genom att hämta anspråk från en ID-token som genererats av din organisations OpenID-kompatibla identitetsprovider. Den här artikeln beskriver hur du ställer in din identitetsprovider så att Authenticator kan kommunicera med den och hämta rätt ID-token för att skicka till den utfärdande tjänsten. 

> [!IMPORTANT]
> Azure Active Directory verifierbara autentiseringsuppgifter är för närvarande i offentlig förhandsversion.
> Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).


För att utfärda en verifierbar autentiseringsidentifiering instrueras Authenticator genom att ladda ned kontraktet för att samla in indata från användaren och skicka informationen till den utfärdande tjänsten. Om du behöver använda en ID-token måste du konfigurera din identitetsprovider så att Authenticator kan logga in en användare med hjälp OpenID Connect protokollet. Anspråken i den resulterande ID-token används för att fylla i innehållet i dina verifierbara autentiseringsuppgifter. Authenticator autentiserar användaren med hjälp OpenID Connect auktoriseringskodflödet. OpenID-providern måste ha stöd för OpenID Connect funktioner: 

| Funktion | Beskrivning |
| ------- | ----------- |
| Typ av beviljande | Måste ha stöd för typ av beviljande av auktoriseringskod. |
| Tokenformat | Måste skapa okrypterade kompakta JWT:er. |
| Signaturalgoritm | Måste producera JWT-signerat med RSA 256. |
| Konfigurationsdokument | Måste ha stöd OpenID Connect konfigurationsdokument och `jwks_uri` . | 
| Klientregistrering | Måste ha stöd för offentlig klientregistrering med `redirect_uri` värdet `vclient://openid/` . | 
| PKCE | Rekommenderas av säkerhetsskäl, men krävs inte. |

Exempel på HTTP-begäranden som skickas till din identitetsprovider finns nedan. Din identitetsprovider måste acceptera och svara på dessa begäranden i enlighet OpenID Connect standard för autentisering.

## <a name="client-registration"></a>Klientregistrering

För att få en verifierbar autentiseringsidentifiering måste användarna logga in på din IDP från Microsoft Authenticator appen. 

Om du vill aktivera det här bytet registrerar du ett program hos din identitetsprovider. Om du använder Azure AD hittar du anvisningarna [här.](../develop/quickstart-register-app.md) Använd följande värden vid registrering.

| Inställning | Värde |
| ------- | ----- |
| Programnamn | `<Issuer Name> Verifiable Credential Service` |
| Omdirigerings-URI | `vcclient://openid/ ` |


När du har registrerat ett program med din identitetsprovider registrerar du dess klient-ID. Du kommer att använda den i avsnittet nedan. Du måste också skriva ned URL:en till den välkända slutpunkten för den OIDC-kompatibla identitetsprovidern. Den utfärdande tjänsten använder den här slutpunkten för att ladda ned de offentliga nycklar som behövs för att verifiera ID-token när den skickas av Authenticator.

Den konfigurerade omdirigerings-URI:n används av Authenticator så att den vet när inloggningen är klar och den kan hämta ID-token. 

## <a name="authorization-request"></a>Auktoriseringsbegäran

Auktoriseringsbegäran som skickas till din identitetsprovider använder följande format.

```HTTP
GET /authorize?client_id=<client-id>&redirect_uri=portableidentity%3A%2F%2Fverify&response_mode=query&response_type=code&scope=openid&state=12345&nonce=12345 HTTP/1.1
Host: www.contoso.com
Connection: Keep-Alive
```

| Parameter | Värde |
| ------- | ----------- |
| `client_id` | Det klient-ID som erhölls under programregistreringsprocessen. |
| `redirect_uri` | Måste använda `vcclient://openid/` . |
| `response_mode` | Måste ha stöd för `query` . |
| `response_type` | Måste ha stöd för `code` . |
| `scope` | Måste ha stöd för `openid` . |
| `state` | Måste returneras till klienten enligt OpenID Connect standard. |
| `nonce` | Måste returneras som ett anspråk i ID-token enligt OpenID Connect standard. |

När identitetsprovidern tar emot en auktoriseringsbegäran bör den autentisera användaren och vidta nödvändiga åtgärder för att slutföra inloggningen, till exempel multifaktorautentisering.

Du kan anpassa inloggningsprocessen efter dina behov. Du kan be användarna att ange ytterligare information, godkänna användningsvillkor, betala för sina autentiseringsuppgifter med mera. När alla steg har slutförts svarar du på auktoriseringsbegäran genom att omdirigera till omdirigerings-URI:en enligt nedan. 

```HTTP
vcclient://openid/?code=nbafhjbh1ub1yhbj1h4jr1&state=12345
```

| Parameter | Värde |
| ------- | ----------- |
| `code` |  Auktoriseringskoden som returneras av din identitetsprovider. |
| `state` | Måste returneras till klienten enligt OpenID Connect standard. |

## <a name="token-request"></a>Tokenbegäran

Tokenbegäran som skickas till din identitetsprovider har följande formulär.

```HTTP
POST /token HTTP/1.1
Host: www.contoso.com
Content-Type: application/x-www-form-urlencoded
Content-Length: 291

client_id=<client-id>&redirect_uri=vcclient%3A%2F%2Fopenid%2F&grant_type=authorization_code&code=nbafhjbh1ub1yhbj1h4jr1&scope=openid
```

| Parameter | Värde |
| ------- | ----------- |
| `client_id` | Klient-ID:t som erhölls under programregistreringsprocessen. |
| `redirect_uri` | Måste använda `vcclient://openid/` . |
| `scope` | Måste ha stöd för `openid` . |
| `grant_type` | Måste ha stöd för `authorization_code` . |
| `code` | Auktoriseringskoden som returneras av din identitetsprovider. |

När du tar emot tokenbegäran bör din identitetsprovider svara med en ID-token.

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

ID-token måste använda JWT kompakt serialiseringsformat och får inte krypteras. ID-token ska innehålla följande anspråk.

| Begär | Värde |
| ------- | ----------- |
| `kid` | Nyckelidentifieraren för den nyckel som används för att signera ID-token, som motsvarar en post i OpenID-providerns `jwks_uri` . |
| `aud` | Det klient-ID som erhölls under programregistreringsprocessen. |
| `iss` | Måste vara värdet `issuer` i ditt OpenID Connect konfigurationsdokument. |
| `exp` | Måste innehålla förfallotiden för ID-token. |
| `iat` | Måste innehålla den tid då ID-token utfärdades. |
| `nonce` | Värdet som ingår i auktoriseringsbegäran. |
| Ytterligare anspråk | ID-token ska innehålla eventuella ytterligare anspråk vars värden kommer att inkluderas i de verifierbara autentiseringsuppgifter som kommer att utfärdas. I det här avsnittet bör du inkludera attribut om användaren, till exempel deras namn. |

## <a name="next-steps"></a>Nästa steg

- [Så här anpassar du dina Azure Active Directory verifierbara autentiseringsuppgifter](credential-design.md)
