---
title: Autentiseringsprotokoll i Azure Active Directory B2C | Microsoft Docs
description: Så här skapar du appar direkt med hjälp av de protokoll som stöds av Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/30/2018
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: ed393f721d4461ebadea41f8dad707d4881865cd
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "78183913"
---
# <a name="azure-ad-b2c-authentication-protocols"></a>Azure AD B2C: autentiseringsprotokoll
Azure Active Directory B2C (Azure AD B2C) tillhandahåller identitet som en tjänst för dina appar genom att stödja två bransch standard protokoll: OpenID Connect och OAuth 2,0. Tjänsten är standard-kompatibel, men alla två implementeringar av dessa protokoll kan ha diskreta skillnader.

Informationen i den här guiden är användbar om du skriver koden genom att direkt skicka och hantera HTTP-begäranden i stället för att använda ett bibliotek med öppen källkod. Vi rekommenderar att du läser den här sidan innan du får information om varje enskilt protokoll. Men om du redan är bekant med Azure AD B2C kan du gå direkt till [protokoll referens guider](#protocols).

<!-- TODO: Need link to libraries above -->

## <a name="the-basics"></a>Grunderna
Alla appar som använder Azure AD B2C måste registreras i din B2C-katalog i [Azure Portal](https://portal.azure.com). Registreringsprocessen samlar in och tilldelar några värden till din app:

* Ett **program-ID** som identifierar din app unikt.
* En **omdirigerings-URI** eller **paket identifierare** som kan användas för att dirigera svar tillbaka till din app.
* Några andra scenario-/regionsspecifika värden. Mer information finns i [så här registrerar du ditt program](tutorial-register-applications.md).

När du har registrerat appen kommunicerar den med Azure Active Directory (Azure AD) genom att skicka begär anden till slut punkten:

```
https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/oauth2/v2.0/authorize
https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/oauth2/v2.0/token
```

I nästan alla OAuth-och OpenID Connect-flöden ingår fyra parter i Exchange:

![Diagram över de fyra OAuth 2,0-rollerna](./media/protocols-overview/protocols_roles.png)

* **Auktoriseringsservern** är Azure AD-slutpunkten. Det hanterar säkert allt som är relaterat till användar information och åtkomst. Den hanterar också förtroende relationer mellan parterna i ett flöde. Det ansvarar för att verifiera användarens identitet, bevilja och återkalla åtkomst till resurser och utfärdande av tokens. Den kallas även för identitets leverantören.

* **Resurs ägaren** är vanligt vis slutanvändaren. Det är den part som äger data och har behörighet att tillåta tredje parter att komma åt dessa data eller resurser.

* **OAuth-klienten** är din app. Den identifieras av dess program-ID. Det är vanligt vis den part som slutanvändarna interagerar med. Den begär också token från auktoriseringsservern. Resurs ägaren måste ge klienten behörighet att komma åt resursen.

* **Resurs servern** är den plats där resursen eller data finns. Den litar på auktoriseringsservern för att autentisera och auktorisera OAuth-klienten på ett säkert sätt. Den använder också token med Bearer-åtkomst för att säkerställa att åtkomst till en resurs kan beviljas.

## <a name="policies-and-user-flows"></a>Principer och användar flöden
Utan tvekan är Azure AD B2C policys som är de viktigaste funktionerna i tjänsten. Azure AD B2C utökar standard OAuth 2,0-och OpenID Connect-protokollen genom att introduktion till principer. Dessa gör det möjligt för Azure AD B2C att utföra mycket mer än enkel autentisering och auktorisering.

För att hjälpa dig att skapa de vanligaste identitets uppgifterna innehåller Azure AD B2C portalen fördefinierade, konfigurerbara principer som kallas **användar flöden**. Användar flöden beskriver fullständigt konsument identitets upplevelser, inklusive registrering, inloggning och profil redigering. Användar flöden kan definieras i ett administrativt användar gränssnitt. De kan utföras med hjälp av en särskild frågeparameter i HTTP-autentiseringsbegäranden.

Principer och användar flöden är inte standard funktioner i OAuth 2,0 och OpenID Connect, så du bör ta dig tid att förstå dem. Mer information finns i [referens hand boken för Azure AD B2C användar flöde](user-flow-overview.md).

## <a name="tokens"></a>Token
Azure AD B2C implementeringen av OAuth 2,0 och OpenID Connect gör en omfattande användning av Bearer-token, inklusive Bearer-token som representeras som JSON-webbtoken (JWTs). En Bearer-token är en förenklad säkerhetstoken som ger "Bearer"-åtkomst till en skyddad resurs.

Innehavaren är en part som kan presentera token. Azure AD måste först autentisera en part innan den kan ta emot en Bearer-token. Men om de nödvändiga stegen inte vidtas för att skydda token i överföring och lagring, kan den fångas upp och användas av en ej avsedd part.

Vissa säkerhetstoken har inbyggda metoder som förhindrar att obehöriga parter använder dem, men som saknar token för den här mekanismen. De måste transporteras i en säker kanal, till exempel via HTTPS (Transport Layer Security).

Om en Bearer-token överförs utanför en säker kanal kan en angripare använda en person-in-the-Middle-attack för att hämta token och använda den för att få obehörig åtkomst till en skyddad resurs. Samma säkerhets principer gäller när Bearer-token lagras eller cachelagras för senare användning. Se alltid till att appen skickar och lagrar Bearer-token på ett säkert sätt.

Mer information finns i [RFC 6750 avsnitt 5](https://tools.ietf.org/html/rfc6750).

Mer information om de olika typerna av tokens som används i Azure AD B2C finns i [referens för Azure AD-token](tokens-overview.md).

## <a name="protocols"></a>Protokoll
När du är redo att granska några exempel förfrågningar kan du börja med någon av följande självstudier. Vart och ett motsvarar ett visst scenario för autentisering. Om du behöver hjälp med att fastställa vilket flöde som passar dig bäst kan du titta närmare [på vilka typer av appar du kan bygga med hjälp av Azure AD B2C](application-types.md).

* [Bygga mobila och inbyggda program med hjälp av OAuth 2,0](authorization-code-flow.md)
* [Bygg webb program med OpenID Connect](openid-connect.md)
* [Bygg appar med en sida med det implicita flödet för OAuth 2,0](implicit-flow-single-page-application.md)

