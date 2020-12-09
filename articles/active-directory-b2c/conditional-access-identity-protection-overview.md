---
title: Identitets skydd och villkorlig åtkomst i Azure AD B2C
description: Lär dig hur identitets skydd ger dig insyn i riskfyllda inloggningar och risk identifieringar. Ta reda på hur och villkorlig åtkomst gör att du kan genomdriva organisations principer baserat på risk händelser i Azure AD B2C klienter.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: overview
ms.date: 09/01/2020
ms.author: mimart
author: msmimart
manager: celested
ms.collection: M365-identity-device-management
ms.openlocfilehash: b17706f908613336714bb7daf08fdf3bfd5eaf64
ms.sourcegitcommit: 1756a8a1485c290c46cc40bc869702b8c8454016
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/09/2020
ms.locfileid: "96928400"
---
# <a name="identity-protection-and-conditional-access-for-azure-ad-b2c"></a>Identitets skydd och villkorlig åtkomst för Azure AD B2C

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

Förbättra säkerheten för Azure Active Directory B2C (Azure AD B2C) med Azure AD Identity Protection och villkorlig åtkomst. Identifierings funktionerna för identitets skydd, inklusive riskfyllda användare och riskfyllda inloggningar, identifieras automatiskt och visas i Azure AD B2C klient organisationen. Du kan skapa principer för villkorlig åtkomst som använder dessa risk identifieringar för att fastställa åtgärder och tillämpa organisations principer. Tillsammans ger de här funktionerna Azure AD B2C program ägare bättre kontroll över riskfyllda autentiseringar och åtkomst principer.
  
Om du redan är bekant med [identitets skydd](../active-directory/identity-protection/overview-identity-protection.md) och [villkorlig åtkomst](../active-directory/conditional-access/overview.md) i Azure AD, är användningen av dessa funktioner med Azure AD B2C en välbekant upplevelse, med mindre skillnader som beskrivs i den här artikeln.

![Villkorlig åtkomst i en B2C-klient](media/conditional-access-identity-protection-overview/conditional-access-b2c.png)

> [!NOTE]
> Om du vill använda villkorlig åtkomst krävs Azure AD B2C Premium P2.

## <a name="benefits-of-identity-protection-and-conditional-access-for-azure-ad-b2c"></a>Fördelar med identitets skydd och villkorlig åtkomst för Azure AD B2C  

Genom att para ihop principer för villkorlig åtkomst med identifiering av identitets skydd kan du svara på riskfyllda autentiseringar med lämplig princip åtgärd.

- **Få en ny Synlighets nivå för autentiserings risker för dina appar och din kund bas**. Med signaler från miljarder månatliga autentiseringar i Azure AD och Microsoft-kontot flaggar algoritmerna för riskbedömning nu autentiseringar som låg, medel eller hög risk för dina lokala konsument-eller medborgarna.
- **Adressera risker automatiskt genom att konfigurera egen anpassad autentisering**. För angivna program kan du kräva att en specifik uppsättning användare tillhandahåller en andra autentiseringsmetod, som i Multi-Factor Authentication (MFA). Eller så kan du blockera åtkomst baserat på den risk nivå som identifierats. Precis som med andra Azure AD B2C upplevelser kan du anpassa den resulterande slut användar upplevelsen med din organisations röst, stil och varumärke. Du kan också visa alternativ för minskning om användaren inte kan få åtkomst.
- **Kontrol lera åtkomst baserat på plats, grupper och appar**.Villkorlig åtkomst kan också användas för att kontrol lera icke-riskfyllda situationer. Du kan till exempel kräva MFA för kunder som har åtkomst till en viss app eller blockera åtkomst från angivna geografiska områden.
- **Integrera med anpassade principer för Azure AD B2C användar flöden och identitets miljö**. Använd dina befintliga anpassade upplevelser och Lägg till de kontroller som du behöver i gränssnittet med villkorlig åtkomst. Du kan också implementera avancerade scenarier för att bevilja åtkomst, till exempel kunskapsbaserade åtkomst eller din egen önskade MFA-Provider.

## <a name="feature-differences-and-limitations"></a>Funktions skillnader och begränsningar

Identitets skydd och villkorlig åtkomst i Azure AD B2C fungerar vanligt vis på samma sätt som i Azure AD, med följande undantag:

- Security Center är inte tillgänglig i Azure AD B2C.

- Identitets skydd och villkorlig åtkomst stöds inte för ROPC server-till-Server-flöden i Azure AD B2C klienter.

- I Azure AD B2C klienter är identitets skydds identifieringar endast tillgängliga för lokala B2C-konton och inte för sociala identiteter som Google eller Facebook.

- I Azure AD B2C klient organisationer är en del av identifieringen av identitets skydds risker tillgängliga. Se [Konfigurera identitets skydd](conditional-access-identity-protection-setup.md#set-up-identity-protection).

- Funktionen för kompatibilitet för villkorlig åtkomst är inte tillgänglig i Azure AD B2C klienter.


## <a name="integrate-conditional-access-with-user-flows-and-custom-policies"></a>Integrera villkorlig åtkomst med användar flöden och anpassade principer

I Azure AD B2C kan du utlösa villkor för villkorlig åtkomst från inbyggda användar flöden. Du kan också inkludera villkorlig åtkomst i anpassade principer. Precis som med andra aspekter av B2C-användarkontot kan meddelanden om slutanvändare anpassas enligt organisationens alternativ för röst, varumärke och minskning. Se [definiera en teknisk profil för villkorlig åtkomst](conditional-access-technical-profile.md).

## <a name="microsoft-graph-api"></a>Microsoft Graph API

Du kan också hantera principer för villkorlig åtkomst i Azure AD B2C med Microsoft Graph API. Mer information finns i [dokumentationen för villkorlig åtkomst](../active-directory/conditional-access/overview.md) och [Microsoft Graph referensen](/graph/api/resources/conditionalaccesspolicy?view=graph-rest-beta).

## <a name="next-steps"></a>Nästa steg

- [Konfigurera identitets skydd och villkorlig åtkomst för Azure AD B2C](conditional-access-identity-protection-setup.md)
- [Lär dig mer om identitets skydd i Azure AD](../active-directory/identity-protection/overview-identity-protection.md)
- [Läs mer om villkorlig åtkomst](../active-directory/conditional-access/overview.md)