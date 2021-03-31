---
title: Identity Protection och B2B-användare – Azure Active Directory
description: Använda identitets skydd med B2B-användare hur det fungerar och kända begränsningar
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: conceptual
ms.date: 10/18/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: c9ec9e110c3a476c9096ae3e216c9780da0e0f49
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "88949204"
---
# <a name="identity-protection-and-b2b-users"></a>Identitetsskydd och B2B-användare

Med Azure AD B2B-samarbete kan organisationer tillämpa riskfyllda principer för B2B-användare som använder identitets skydd. Dessa principer konfigureras på två sätt:

- Administratörer kan konfigurera de inbyggda riskhanterings principerna för identiteter som gäller för alla appar, inklusive gäst användare.
- Administratörer kan konfigurera sina principer för villkorlig åtkomst med hjälp av inloggnings risker som ett villkor, inklusive gäst användare.

## <a name="how-is-risk-evaluated-for-b2b-collaboration-users"></a>Hur utvärderas risken för B2B-samarbets användare

Användar risken för B2B-samarbets användare utvärderas i sin Hem Katalog. Inloggnings risken i real tid för dessa användare utvärderas i resurs katalogen när de försöker få åtkomst till resursen.

## <a name="limitations-of-identity-protection-for-b2b-collaboration-users"></a>Begränsningar för identitets skydd för B2B-samarbets användare

Det finns begränsningar i implementeringen av Identity Protection för B2B-samarbets användare i en resurs katalog på grund av deras identitet i sin arbets katalog. De största begränsningarna är följande:

- Om en gäst användare utlöser identitets skydds principens användar risk princip för att tvinga lösen ords återställning, **kommer de att blockeras**. Det här blocket beror på att det inte går att återställa lösen ord i resurs katalogen.
- **Gäst användare visas inte i rapporten riskfyllda användare**. Den här förlusten av synlighet beror på riskbedömningen i B2B-användarens Hem Katalog.
- Administratörer **kan inte stänga eller reparera en riskfylld B2B-samarbets användare** i sina resurs kataloger. Detta beror på att administratörer i resurs katalogen inte har åtkomst till B2B-användarens Hem Katalog.

### <a name="why-cant-i-remediate-risky-b2b-collaboration-users-in-my-directory"></a>Varför kan jag inte reparera riskfyllda B2B-samarbets användare i min katalog?

Riskbedömningen och reparationen för B2B-användare sker i deras hem katalog. På grund av detta faktum visas inte gäst användarna i rapporten riskfyllda användare i resurs katalogen och administratörer i resurs katalogen kan inte tvinga en säker lösen ords återställning för dessa användare.

### <a name="what-do-i-do-if-a-b2b-collaboration-user-was-blocked-due-to-a-risk-based-policy-in-my-organization"></a>Vad gör jag om en B2B-samarbets användare blockerades på grund av en riskfylld princip i min organisation?

Om en riskfylld B2B-användare i din katalog blockeras av en riskfylld princip, måste användaren åtgärda risken i sin Hem Katalog. Användare kan reparera sin risk genom att göra en säker lösen ords återställning i sin Hem Katalog. Om de inte har självbetjänings återställning av lösen ord aktiverade i sin hem katalog måste de kontakta sin egen organisations IT-personal för att låta en administratör manuellt stänga av sin risk eller återställa sitt lösen ord.

### <a name="how-do-i-prevent-b2b-collaboration-users-from-being-impacted-by-risk-based-policies"></a>Hur gör jag för att förhindrar du att B2B-samarbets användare påverkas av riskfyllda principer?

Om du undantar B2B-användare från din organisations riskfyllda principer för villkorlig åtkomst hindras B2B-användare från att påverkas eller blockeras av riskbedömningen. Om du vill utesluta dessa B2B-användare skapar du en grupp i Azure AD som innehåller alla användare i din organisations gäst. Lägg sedan till den här gruppen som ett undantag för din inbyggda identitets skydds användar risk och inloggnings risk principer, samt alla principer för villkorlig åtkomst som använder inloggnings risker som ett villkor.

## <a name="next-steps"></a>Nästa steg

Se följande artiklar om Azure AD B2B-samarbete:

- [Vad är Azure AD B2B-samarbete?](../external-identities/what-is-b2b.md)