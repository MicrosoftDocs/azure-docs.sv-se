---
title: Översikt över tokens – Azure Active Directory B2C
description: Lär dig mer om de tokens som används i Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/31/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: b4e268d35a2e31db0ce92ff61e66fd23bce68e38
ms.sourcegitcommit: 66479d7e55449b78ee587df14babb6321f7d1757
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/15/2020
ms.locfileid: "97516352"
---
# <a name="overview-of-tokens-in-azure-active-directory-b2c"></a>Översikt över tokens i Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory B2C (Azure AD B2C) genererar flera typer av säkerhetstoken när de bearbetar varje [autentiseringspaket](application-types.md). I det här dokumentet beskrivs format, säkerhets egenskaper och innehåll för varje typ av token.

## <a name="token-types"></a>Token-typer

Azure AD B2C stöder [protokollen OAuth 2,0 och OpenID Connect](protocols-overview.md), som använder token för autentisering och säker åtkomst till resurser. Alla tokens som används i Azure AD B2C är [JSON Web tokens (JWTs)](https://self-issued.info/docs/draft-ietf-oauth-json-web-token.html) som innehåller information om innehavaren och ämnet för token.

Följande tokens används i kommunikationen med Azure AD B2C:

- *ID-token* – ett JWT som innehåller anspråk som du kan använda för att identifiera användare i ditt program. Den här token skickas säkert i HTTP-begäranden för kommunikation mellan två komponenter i samma program eller tjänst. Du kan använda anspråken i en ID-token när du ser anpassa. De används ofta för att Visa konto information eller för att fatta beslut om åtkomst kontroll i ett program. ID-token är signerade, men de krypteras inte. När ditt program eller API tar emot en ID-token måste den verifiera signaturen för att bevisa att token är autentisk. Ditt program eller API måste också verifiera ett fåtal anspråk i token för att bevisa att det är giltigt. Beroende på scenario kraven kan anspråk som verifierats av ett program variera, men programmet måste utföra några vanliga anspråks valideringar i varje scenario.
- *Åtkomsttoken* – ett JWT som innehåller anspråk som du kan använda för att identifiera de beviljade behörigheterna för dina API: er. Åtkomsttoken signeras, men de krypteras inte. Åtkomsttoken används för att ge åtkomst till API: er och resurs servrar.  När ditt API tar emot en åtkomsttoken måste den verifiera signaturen för att bevisa att token är autentisk. Ditt API måste också verifiera ett fåtal anspråk i token för att bevisa att det är giltigt. Beroende på scenario kraven kan anspråk som verifierats av ett program variera, men programmet måste utföra några vanliga anspråks valideringar i varje scenario.
- *Refresh token* -Refresh tokens används för att hämta nya ID-token och åtkomsttoken i ett OAuth 2,0-flöde. De tillhandahåller ditt program med långsiktig åtkomst till resurser på uppdrag av användare utan att det krävs någon interaktion med dessa användare. Uppdatering av tokens är ogenomskinlig för ditt program. De utfärdas av Azure AD B2C och kan bara granskas och tolkas av Azure AD B2C. De är långa, men programmet bör inte skrivas med förväntat att en uppdateringstoken kommer att vara sist under en viss tids period. Du kan när som helst välja att inte validera token för en mängd olika orsaker. Det enda sättet för ditt program att veta om en uppdateringstoken är giltig för att försöka lösa in den genom att göra en Tokenbegäran att Azure AD B2C. När du löser in en uppdateringstoken för en ny token får du en ny uppdateringstoken i svaret från token. Spara den nya uppdateringstoken. Den ersätter den uppdateringstoken som du tidigare använde i begäran. Den här åtgärden hjälper till att garantera att uppdateringstoken är giltiga så länge som möjligt. Observera att program med en enda sida som använder auktoriseringskod med PKCE alltid har en livs längd på en uppdateringstoken på 24 timmar. [Lär dig mer om säkerhets konsekvenserna av uppdateringstoken i webbläsaren](../active-directory/develop/reference-third-party-cookies-spas.md#security-implications-of-refresh-tokens-in-the-browser).

## <a name="endpoints"></a>Slutpunkter

Ett [registrerat program](tutorial-register-applications.md) tar emot tokens och kommunicerar med Azure AD B2C genom att skicka begär anden till dessa slut punkter:

- `https://<tenant-name>.b2clogin.com/<tenant-name>.onmicrosoft.com/<policy-name>/oauth2/v2.0/authorize`
- `https://<tenant-name>.b2clogin.com/<tenant-name>.onmicrosoft.com/<policy-name>/oauth2/v2.0/token`

Säkerhetstoken som programmet tar emot från Azure AD B2C kan komma från-eller- `/authorize` `/token` slut punkterna. När ID-token har hämtats från `/authorize` slut punkten görs det med det [implicita flödet](implicit-flow-single-page-application.md), som ofta används för användare som loggar in på JavaScript-baserade webb program. När ID-token har hämtats från `/token` slut punkten görs det med hjälp av [flödet för auktoriseringskod](openid-connect.md#get-a-token), som gör att token är dold i webbläsaren.

## <a name="claims"></a>Anspråk

När du använder Azure AD B2C har du detaljerad kontroll över innehållet i dina tokens. Du kan konfigurera [användar flöden](user-flow-overview.md) och [anpassade principer](custom-policy-overview.md) för att skicka vissa uppsättningar användar data i anspråk som krävs för ditt program. Dessa anspråk kan innehålla standard egenskaper som **DisplayName** och **EmailAddress**. Dina program kan använda dessa anspråk för att autentisera användare och förfrågningar på ett säkert sätt.

Anspråk i ID-token returneras inte i någon särskild ordning. Nya anspråk kan introduceras i ID-token när som helst. Programmet bör inte brytas när nya anspråk införs. Du kan också inkludera [anpassade användarattribut](user-flow-custom-attributes.md) i dina anspråk.

I följande tabell visas de anspråk som du kan förväntar dig i ID-token och åtkomsttoken som utfärdats av Azure AD B2C.

| Name | Begär | Exempelvärde | Description |
| ---- | ----- | ------------- | ----------- |
| Målgrupp | `aud` | `90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6` | Identifierar den avsedda mottagaren för token. För Azure AD B2C är mål gruppen program-ID: t. Programmet bör validera det här värdet och avvisa token om det inte matchar. Mål gruppen är synonym med resursen. |
| Utfärdare | `iss` |`https://<tenant-name>.b2clogin.com/775527ff-9a37-4307-8b3d-cc311f58d925/v2.0/` | Identifierar säkerhetstokentjänst som konstruerar och returnerar token. Den identifierar även den katalog där användaren autentiserades. Ditt program bör verifiera utfärdarens anspråk för att se till att token kommer från lämplig slut punkt. |
| Utfärdat | `iat` | `1438535543` | Tiden då token utfärdades, representeras i epok tid. |
| Förfallo tid | `exp` | `1438539443` | Tiden då token blir ogiltig, representeras i epok tid. Programmet bör använda detta anspråk för att kontrol lera giltigheten för token för token. |
| Inte före | `nbf` | `1438535543` | Tiden då token börjar gälla, representeras i epok tiden. Den här tiden är vanligt vis samma som den tidpunkt då token utfärdades. Programmet bör använda detta anspråk för att kontrol lera giltigheten för token för token. |
| Version | `ver` | `1.0` | Versionen av ID-token, som definieras av Azure AD B2C. |
| Kod-hash | `c_hash` | `SGCPtt01wxwfgnYZy2VJtQ` | En kod-hash som ingår i en ID-token endast när token utfärdas tillsammans med en OAuth 2,0-auktoriseringskod. En kod-hash kan användas för att verifiera äktheten för en auktoriseringskod. Mer information om hur du utför den här verifieringen finns i [OpenID Connect-specifikationen](https://openid.net/specs/openid-connect-core-1_0.html).  |
| Hash för åtkomsttoken | `at_hash` | `SGCPtt01wxwfgnYZy2VJtQ` | En hash för åtkomsttoken ingår bara i en ID-token när token utfärdas tillsammans med en OAuth 2,0-åtkomsttoken. En hash för åtkomsttoken kan användas för att verifiera äktheten för en åtkomsttoken. Mer information om hur du utför den här verifieringen finns i [OpenID Connect-specifikationen](https://openid.net/specs/openid-connect-core-1_0.html)  |
| Nnär | `nonce` | `12345` | En nonce är en strategi som används för att minimera repetitions attacker. Ditt program kan ange en nonce i en auktoriseringsbegäran med hjälp av `nonce` Frågeparametern. Värdet som du anger i begäran genereras oförändrat i `nonce` anspråk för en ID-token. Detta påstående gör att ditt program kan verifiera värdet mot det värde som anges i begäran. Programmet bör utföra den här verifieringen under validerings processen för ID-token. |
| Ämne | `sub` | `884408e1-2918-4cz0-b12d-3aa027d7563b` | Den huvudprincip som token förutsätter information för, t. ex. användaren av ett program. Värdet är oföränderligt och kan inte tilldelas om eller återanvändas. Den kan användas för att utföra verifierings kontroller på ett säkert sätt, till exempel när token används för att få åtkomst till en resurs. Som standard fylls ämnes anspråket med objekt-ID: t för användaren i katalogen. |
| Klass referens för autentiserings kontext | `acr` | Inte tillämpligt | Används endast med äldre principer. |
| Princip för förtroende ramverk | `tfp` | `b2c_1_signupsignin1` | Namnet på den princip som användes för att hämta ID-token. |
| Autentiserings tid | `auth_time` | `1438535543` | Den tid då användaren senast angav autentiseringsuppgifter, som representeras i epok tid. Det finns ingen diskriminering mellan den autentiseringen som en ny inloggning, en enkel inloggnings-eller SSO-session eller en annan typ av inloggning. `auth_time`Är den senaste gången som programmet (eller användaren) initierade ett autentiseringsförsök mot Azure AD B2C. Den metod som används för autentisering skiljer sig inte åt. |
| Omfång | `scp` | `Read`| De behörigheter som tilldelats resursen för en åtkomsttoken. Flera beviljade behörigheter avgränsas med ett blank steg. |
| Auktoriserad part | `azp` | `975251ed-e4f5-4efd-abcb-5f1a8f566ab7` | **Program-ID** för det klient program som initierade begäran. |

## <a name="configuration"></a>Konfiguration

Följande egenskaper används för att [Hantera livs längder för säkerhetstoken](configure-tokens.md) som genereras av Azure AD B2C:

- **Åtkomst &-ID-token livs längd (minuter)** – livstiden för OAuth 2,0 Bearer-token som används för att få åtkomst till en skyddad resurs. Standardvärdet är 60 minuter. Minimivärdet (inklusive) är 5 minuter. Det maximala antalet (inklusive) är 1440 minuter.

- **Giltighets tid för token (dagar)** – den maximala tids period som en uppdateringstoken kan användas för att hämta en ny åtkomst-eller ID-token. Tids perioden omfattar också hämtning av en ny uppdateringstoken om ditt program har beviljats `offline_access` omfånget. Standardvärdet är 14 dagar. Minimivärdet (inklusive) är en dag. Det största (inklusive) är 90 dagar.

- **Uppdatera token glidande fönster livs längd (dagar)** – när den här tids perioden går ut tvingas användaren att autentisera igen, oberoende av giltighets perioden för den senaste uppdateringstoken som hämtats av programmet. Den kan bara anges om växeln är inställd på **Bounded**. Det måste vara större än eller lika med värdet för **uppdateringstoken för uppdateringstoken (dagar)** . Om växeln är **obunden** kan du inte ange ett angivet värde. Standardvärdet är 90 dagar. Minimivärdet (inklusive) är en dag. Det största (inklusive) är 365 dagar.

Följande användnings fall är aktiverade med följande egenskaper:

- Tillåt att en användare förblir inloggad i ett mobilt program under obegränsad tid, så länge användaren alltid är aktiv i programmet. Du kan ställa in **livs längden för skjutreglaget för att uppdatera token (dagar)** till **avgränsade** i ditt inloggnings användar flöde.
- Uppfylla din branschs krav på säkerhet och efterlevnad genom att ange lämpliga livs längder för åtkomst-token.

De här inställningarna är inte tillgängliga för användar flöden för lösen ords återställning.

## <a name="compatibility"></a>Kompatibilitet

Följande egenskaper används för att [Hantera token-kompatibilitet](configure-tokens.md):

- Certifikat **utfärdare (ISS)-anspråk** – den här egenskapen identifierar den Azure AD B2C klient som utfärdade token. Standardvärdet är `https://<domain>/{B2C tenant GUID}/v2.0/`. Värdet för `https://<domain>/tfp/{B2C tenant GUID}/{Policy ID}/v2.0/` innehåller ID: n för både den Azure AD B2C klienten och det användar flöde som användes i Tokenbegäran. Om ditt program eller bibliotek behöver Azure AD B2C vara kompatibelt med [OpenID Connect Discovery Discovery 1,0-specifikationen](https://openid.net/specs/openid-connect-discovery-1_0.html)använder du det här värdet.

- **Subject (sub)-anspråk** – den här egenskapen identifierar den entitet som token kontrollerar information om. Standardvärdet är **ObjectID**, som fyller på `sub` anspråket i token med objekt-ID: t för användaren. Värdet som **inte stöds** anges bara för bakåtkompatibilitet. Vi rekommenderar att du växlar till **ObjectID** så snart du kan.

- **Anspråk som representerar princip-ID** – den här egenskapen identifierar anspråks typen där princip namnet som används i Tokenbegäran fylls i. Standardvärdet är `tfp`. Värdet för `acr` anges bara för bakåtkompatibilitet.

## <a name="pass-through"></a>Direkt

När en användar resa startar, Azure AD B2C ta emot en åtkomsttoken från en identitets leverantör. Azure AD B2C använder denna token för att hämta information om användaren. Du aktiverar ett anspråk i ditt användar flöde för att [Skicka token](idp-pass-through-user-flow.md) till de program som du registrerar i Azure AD B2C. Ditt program måste använda ett [rekommenderat användar flöde](user-flow-versions.md) för att kunna dra nytta av att skicka token som ett anspråk.

Azure AD B2C stöder för närvarande bara att skicka åtkomsttoken för OAuth 2,0-identitets leverantörer, som innehåller Facebook och Google. För alla andra identitets leverantörer returneras anspråket tomt.

## <a name="validation"></a>Validering

För att validera en token bör programmet kontrol lera både signaturen och anspråk för token. Många bibliotek med öppen källkod är tillgängliga för att verifiera JWTs, beroende på vilket språk du föredrar. Vi rekommenderar att du utforskar dessa alternativ i stället för att implementera din egen verifierings logik.

### <a name="validate-signature"></a>Verifiera signatur

En JWT innehåller tre segment, ett *sidhuvud*, en *brödtext* och en *signatur*. Du kan använda signatur segmentet för att verifiera tokens äkthet så att den kan vara betrodd av ditt program. Azure AD B2C tokens signeras med hjälp av algoritmer för asymmetrisk kryptering i bransch standard, till exempel RSA 256.

Rubriken för token innehåller information om nyckeln och krypterings metoden som används för att signera token:

```
{
        "typ": "JWT",
        "alg": "RS256",
        "kid": "GvnPApfWMdLRi8PDmisFn7bprKg"
}
```

Värdet för **alg** -anspråket är algoritmen som användes för att signera token. Värdet för **barn** -anspråket är den offentliga nyckel som användes för att signera token. Vid en angiven tidpunkt kan Azure AD B2C signera en token med hjälp av någon av en uppsättning offentliga privata nyckel par. Azure AD B2C roterar den möjliga uppsättningen nycklar med jämna mellanrum. Programmet ska skrivas för att hantera dessa nycklar automatiskt. En rimlig frekvens för att söka efter uppdateringar av de offentliga nycklar som används av Azure AD B2C är var 24: e timme. Om du vill hantera oväntade nyckel ändringar ska programmet skrivas för att hämta de offentliga nycklarna igen om det får ett oväntat **barn** värde.

Azure AD B2C har en slut punkt för OpenID Connect-metadata. Med den här slut punkten kan program begära information om Azure AD B2C vid körning. Den här informationen omfattar slut punkter, token innehåll och signerings nycklar för token. Din Azure AD B2C klient innehåller ett JSON-Metadatadokumentet för varje princip. Metadatadokumentet är ett JSON-objekt som innehåller flera användbara informations delar. Metadata innehåller **jwks_uri**, vilket ger platsen för den uppsättning offentliga nycklar som används för att signera token. Platsen finns här, men det är bäst att hämta platsen dynamiskt med hjälp av Metadatadokumentet och parsa **jwks_uri**:

```
https://contoso.b2clogin.com/contoso.onmicrosoft.com/b2c_1_signupsignin1/discovery/v2.0/keys
```
JSON-dokumentet som finns på den här URL: en innehåller all information om den offentliga nyckeln som används vid en viss tidpunkt. Din app kan använda `kid` anspråket i JWT-huvudet för att välja den offentliga nyckeln i JSON-dokumentet som används för att signera en viss token. Den kan sedan utföra verifiering av signaturen med hjälp av rätt offentlig nyckel och angiven algoritm.

Metadatadokumentet för `B2C_1_signupsignin1` principen i `contoso.onmicrosoft.com` klienten finns på:

```
https://contoso.b2clogin.com/contoso.onmicrosoft.com/b2c_1_signupsignin1/v2.0/.well-known/openid-configuration
```

För att avgöra vilken princip som användes för att signera en token (och var du ska begära metadata) har du två alternativ. Först ingår princip namnet i `tfp` (standard) eller `acr` anspråk (enligt konfigurationen) i token. Du kan parsa anspråk från bröd texten i JWT genom Base-64-avkodning av texten och Avserialiserar den JSON-sträng som resulterar. `tfp`Eller `acr` anspråket är namnet på den princip som användes för att utfärda token. Det andra alternativet är att koda principen i värdet för `state` parametern när du utfärdar begäran och sedan avkoda den för att avgöra vilken princip som användes. Antingen är metoden giltig.

En beskrivning av hur du utför signaturverifiering är utanför det här dokumentets omfattning. Många bibliotek med öppen källkod är tillgängliga som hjälper dig att validera en token.

### <a name="validate-claims"></a>Validera anspråk

När dina program eller API: er får en ID-token bör det också utföra flera kontroller mot anspråk i ID-token. Följande anspråk bör kontrol leras:

- **mål grupp** – kontrollerar att ID-token är avsedd att ges till ditt program.
- **inte före** och **utgångs tid** – verifierar att ID-token inte har gått ut.
- **utfärdare** – kontrollerar att token har utfärdats till ditt program av Azure AD B2C.
- **nonce** – en strategi för repetition av attack attacker för token.

En fullständig lista över vilka valideringar programmet ska utföra finns i [OpenID Connect-specifikationen](https://openid.net).

## <a name="next-steps"></a>Nästa steg

Läs mer om hur du [använder åtkomsttoken](access-tokens.md).

