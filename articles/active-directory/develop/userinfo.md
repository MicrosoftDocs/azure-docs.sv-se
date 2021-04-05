---
title: Microsoft Identity Platform UserInfo-slutpunkt | Azure
titleSuffix: Microsoft identity platform
description: Läs mer om UserInfo-slutpunkten på Microsoft Identity Platform.
services: active-directory
author: hpsin
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 09/21/2020
ms.author: hirsin
ms.reviewer: hirsin
ms.custom: aaddev
ms.openlocfilehash: 3bd0621d607b121f19bc47c717343b8e2e39b04f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "98752725"
---
# <a name="microsoft-identity-platform-userinfo-endpoint"></a>Microsoft Identity Platform UserInfo-slutpunkt

UserInfo-slutpunkten är en del av [OpenID Connect standard](https://openid.net/specs/openid-connect-core-1_0.html#UserInfo) (OIDC) som har utformats för att returnera anspråk för den användare som autentiserats.  För Microsoft Identity Platform är UserInfo-slutpunkten värd för Microsoft Graph ( https://graph.microsoft.com/oidc/userinfo) . 

## <a name="find-the-well-known-configuration-endpoint"></a>Hitta den. välkända konfigurations slut punkten

Du kan program mässigt identifiera UserInfo-slutpunkten med hjälp av OpenID Connect Discovery-dokumentet på `https://login.microsoftonline.com/common/v2.0/.well-known/openid-configuration` . Den visas i `userinfo_endpoint` fältet och det här mönstret kan användas över molnet för att peka till den högra slut punkten.  Vi rekommenderar inte att du hårdkodar UserInfo-slutpunkten i din app – Använd OIDC identifierings dokument för att hitta slut punkten vid körning i stället.

Som en del av OpenID Connect-specifikationen anropas UserInfo-slutpunkten ofta automatiskt av [OIDC-kompatibla bibliotek](https://openid.net/developers/certified/)  för att hämta information om användaren.  Utan att vara värd för en sådan slut punkt skulle Microsoft Identity Platform inte vara standard standarder och vissa bibliotek skulle därför inte fungera.  I [listan över anspråk som identifierats i OIDC-standarden](https://openid.net/specs/openid-connect-core-1_0.html#StandardClaims) producerar vi namn anspråk, ämnes anspråk och e-post när de är tillgängliga och medgivande för.  

## <a name="consider-use-an-id-token-instead"></a>Överväg: Använd en ID-token i stället

Den information som är tillgänglig i ID-token som din app kan ta emot är en supermängd av den information som kan hämtas från UserInfo-slutpunkten.  Eftersom du kan hämta en ID-token samtidigt som du får en token för att anropa UserInfo-slutpunkten, rekommenderar vi att du använder denna ID-token för att hämta information om användaren i stället för att anropa UserInfo-slutpunkten.  Genom att använda ID-token elimineras en till två nätverks begär Anden från din program Start, vilket minskar svars tiden i ditt program.

Om du behöver mer information om användaren ska du anropa [Microsoft Graph- `/user` API: et](/graph/api/user-get) för att få information som Office-nummer eller befattning.   Du kan också använda [valfria anspråk](active-directory-optional-claims.md) för att inkludera ytterligare användar information i dina ID-och åtkomsttoken.

## <a name="calling-the-userinfo-endpoint"></a>Anropar UserInfo-slutpunkten

UserInfo är ett standard-API för OAuth Bearer som anropats som andra Microsoft Graph-API med hjälp av den åtkomsttoken som togs emot när en token hämtades för Microsoft Graph. Den returnerar ett JSON-svar som innehåller anspråk om användaren.

### <a name="permissions"></a>Behörigheter

Använd följande [OIDC-behörigheter](v2-permissions-and-consent.md#openid-connect-scopes) för att anropa UserInfo-API: et. `openid` är obligatoriskt och `profile` `email` omfattningarna och säkerställer att ytterligare information tillhandahålls i svaret.

|Behörighets typ      | Behörigheter    |
|:--------------------|:---------------------------------------------------------|
|Delegerat (arbets-eller skol konto) | OpenID (krävs), profil, e-post |
|Delegerad (personlig Microsoft-konto) | OpenID (krävs), profil, e-post |
|Program | Inte tillämpligt |

> [!TIP]
> Kopiera denna URL i webbläsaren för att hämta en token för UserInfo-slutpunkten samt en [ID-token](id-tokens.md) och ersätt klient-ID och omdirigerings-URI med din egen. Observera att den endast begär omfattningar för OpenID-eller Graph-omfattningar, och inget annat.  Detta är obligatoriskt eftersom du inte kan begära behörigheter för två olika resurser i samma token-begäran.
>
> `https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id=<yourClientID>&response_type=token+id_token&redirect_uri=<YourRedirectUri>&scope=user.read+openid+profile+email&response_mode=fragment&state=12345&nonce=678910`
>
> Du kan använda den här åtkomsttoken i nästa avsnitt.

Precis som med andra Microsoft Graph token kan den token som du får här inte vara en JWT. Om du har loggat in Microsoft-konto användare är det ett krypterat token-format. Detta beror på att Microsoft Graph har ett särskilt utfärdande mönster för token. Detta påverkar inte möjligheten att använda åtkomsttoken för att anropa UserInfo-slutpunkten.

### <a name="calling-the-api"></a>Anropar API: et

UserInfo-API: et stöder både GET och POST, enligt OIDC-specifikationen.

```http
GET or POST /oidc/userinfo HTTP/1.1
Host: graph.microsoft.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJub25jZSI6Il…
```

### <a name="userinfo-response"></a>UserInfo-svar

```jsonc
{
    "sub": "OLu859SGc2Sr9ZsqbkG-QbeLgJlb41KcdiPoLYNpSFA",
    "name": "Mikah Ollenburg", // names all require the “profile” scope.
    "family_name": " Ollenburg",
    "given_name": "Mikah",
    "email": "mikoll@contoso.com" //requires the “email” scope.
}
```

Anspråken som anges här är alla anspråk som UserInfo-slutpunkten kan returnera.  Detta är samma värden som appen ser i [ID-token](id-tokens.md) som utfärdats till appen.  

## <a name="notes-and-caveats-on-the-userinfo-endpoint"></a>Anteckningar och varningar om UserInfo-slutpunkten

* Om du vill anropa den här UserInfo-slutpunkten måste du använda v 2.0-slutpunkten.  Om du använder v 1.0-slutpunkten får du en token för den v 1.0 UserInfo-slutpunkt som finns på login.microsoftonline.com.  Vi rekommenderar att alla OIDC-kompatibla appar och bibliotek använder v 2.0-slutpunkten för att säkerställa kompatibiliteten.
* Det går inte att anpassa svaret från UserInfo-slutpunkten.  Om du vill anpassa anspråk använder du [anspråks mappning]( active-directory-claims-mapping.md) för att redigera informationen som returneras i tokens.
* Det går inte att lägga till svaret från UserInfo-slutpunkten i.  Om du vill få ytterligare anspråk om användaren, Använd [valfria anspråk]( active-directory-optional-claims.md) för att lägga till nya anspråk till tokens.

## <a name="next-steps"></a>Nästa steg

* [Granska innehållet i ID-token](id-tokens.md)
* [Anpassa innehållet i en ID-token med valfria anspråk](active-directory-optional-claims.md)
* [Begär en åtkomsttoken och ID-token med OAuth2-protokollet](v2-protocols-oidc.md)
