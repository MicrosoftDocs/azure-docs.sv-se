---
title: Bygg återhämtning med hantering av autentiseringsuppgifter i Azure Active Directory
description: En guide för arkitekter och IT-administratörer om att skapa en strategi för elastiska autentiseringsuppgifter.
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 11/30/2020
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 399d2f71fa20d63dce89cf3be5c12ffd63264895
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "98724718"
---
# <a name="build-resilience-with-credential-management"></a>Bygg återhämtning med hantering av autentiseringsuppgifter

När en autentiseringsuppgift presenteras för Azure AD i en Tokenbegäran, finns det flera beroenden som måste vara tillgängliga för verifiering. Den första autentiseringsmetoden förlitar sig på Azure AD-autentisering och i vissa fall på en lokal infrastruktur. Mer information om hybrid autentiserings arkitekturer finns i [bygga återhämtning i din hybrid infrastruktur](resilience-in-hybrid.md). 

Om du implementerar en andra faktor läggs beroenden för den andra faktorn till i beroendena för den första. Om din första faktor exempelvis är via PTA och den andra faktorn är SMS, är dina beroenden:

* Azure AD Authentication Services

* Azure MFA-tjänst

* Lokal infrastruktur

* Telefon operatör

* Användarens enhet (inte avbildad)

 
![Bild av autentiseringsmetoder och beroenden](./media/resilience-in-credentials/admin-resilience-credentials.png)

Din strategi för autentiseringsuppgifter bör överväga beroenden för varje autentiseringstyp och tillhandahålla metoder som undviker en enskild felpunkt. 

Eftersom autentiseringsmetoder har olika beroenden, är det en bra idé att göra det möjligt för användare att registrera sig för så många alternativ för andra faktorer som möjligt. Se till att inkludera andra faktorer med olika beroenden om möjligt. Till exempel, röst samtal och SMS som andra faktorer delar samma beroenden, så du får dem som de enda alternativen som inte minskar risken.

Den mest elastiska Credential-strategin är att använda lösenordsbaserad autentisering. Säkerhets nycklarna för Windows Hello för företag och FIDO 2,0 har färre beroenden än stark autentisering med två olika faktorer. Säkerhets nycklarna för Microsoft Authenticator app, Windows Hello för företag och FIDO 2,0 är de säkraste. 

För andra faktorer har Microsoft Authenticator-appen eller andra Authenticator-appar som använder tidsbaserad eng ång slö sen ord (TOTP mobilapp) eller OATH-token, och är därför mer elastiska.

## <a name="how-do-multiple-credentials-help-resilience"></a>Hur bidrar flera autentiseringsuppgifter till återhämtning?

Etablering av flera typer av autentiseringsuppgifter ger användarna alternativ som hanterar sina inställningar och miljö begränsningar. Det innebär att interaktiv autentisering där användare uppmanas att använda Multi-Factor Authentication är mer elastiska för vissa beroenden som inte är tillgängliga vid tidpunkten för begäran. Du kan [optimera återautentiserings-prompter för Multi-Factor Authentication](../authentication/concepts-azure-multi-factor-authentication-prompts-session-lifetime.md).

Förutom individuell användar återhämtning som beskrivs ovan bör företag planera för oförutsedda störningar, till exempel drifts fel som ger en felaktig konfiguration, en natur katastrof eller ett resurs avbrott i hela företaget till en lokal Federations tjänst (särskilt när den används för Multi-Factor Authentication). 

## <a name="how-do-i-implement-resilient-credentials"></a>Hur gör jag för att implementera elastiska autentiseringsuppgifter?

* Distribuera [lösen ords lös autentiseringsuppgifter](../authentication/howto-authentication-passwordless-deployment.md) som Windows Hello för företag, telefonen och FIDO2 säkerhets nycklar för att minska beroenden.

* Distribuera [Microsoft Authenticator-appen](../user-help/user-help-auth-app-overview.md) som en andra faktor.

* Aktivera [synkronisering av lösen ords-hash](../hybrid/whatis-phs.md) för Hybrid konton som synkroniseras från Windows Server Active Directory. Det här alternativet kan aktive ras tillsammans med Federations tjänster som AD FS och det finns en gräns för om Federations tjänsten Miss lyckas.

* [Analysera användningen av Multi-Factor Authentication-metoder](/samples/azure-samples/azure-mfa-authentication-method-analysis/azure-mfa-authentication-method-analysis/) för att förbättra användarnas upplevelse.

* [Implementera en strategi för elastisk åtkomst kontroll](../authentication/concept-resilient-controls.md)

## <a name="next-steps"></a>Nästa steg
Återhämtnings resurser för administratörer och arkitekter
 
* [Bygg återhämtning med enhets tillstånd](resilience-with-device-states.md)

* [Bygg återhämtning med hjälp av den kontinuerliga åtkomst utvärderingen (CAE)](resilience-with-continuous-access-evaluation.md)

* [Bygg återhämtning vid extern användarautentisering](resilience-b2b-authentication.md)

* [Bygg återhämtning i din hybrid autentisering](resilience-in-hybrid.md)

* [Bygg återhämtning i program åtkomst med Application Proxy](resilience-on-premises-access.md)

Återhämtnings resurser för utvecklare

* [Bygg IAM-återhämtning i dina program](resilience-app-development-overview.md)

* [Bygg återhämtning i dina CIAM-system](resilience-b2c.md)