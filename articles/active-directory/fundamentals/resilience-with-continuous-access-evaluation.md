---
title: Bygg återhämtning med hjälp av utvärdering av kontinuerlig åtkomst i Azure Active Directory
description: En guide för arkitekter och IT-administratörer om att använda CAE
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
ms.openlocfilehash: 2d792c1eb0a85f2a898d1dc5c63047a4553f13b0
ms.sourcegitcommit: 5fd1f72a96f4f343543072eadd7cdec52e86511e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/01/2021
ms.locfileid: "106106780"
---
# <a name="build-resilience-by-using-continuous-access-evaluation"></a>Bygg återhämtning med hjälp av utvärdering av kontinuerlig åtkomst

Med hjälp av den [kontinuerliga åtkomst utvärderingen](../conditional-access/concept-continuous-access-evaluation.md) (CAE) kan Azure AD-program prenumerera på kritiska händelser som sedan kan utvärderas och tillämpas. Detta omfattar utvärdering av följande händelser:

* Det användar konto som ska tas bort eller inaktive ras

* Lösen ordet för en användare har ändrats

* MFA har Aktiver ATS för användaren.

* Administratören återkallar uttryckligen en token.

* Utökad användar risk har identifierats.

Därför kan program avvisa token som inte har gått ut baserat på händelser som har signaler ATS av Azure AD, som visas i följande diagram.

![conceptualiagram för CAE](./media/resilience-with-cae/admin-resilience-continuous-access-evaluation.png)

## <a name="how-does-cae-help"></a>Hur fungerar CAE-hjälpen?

Med den här metoden kan Azure AD utfärda längre token för längre livs längd, samtidigt som program kan återkalla åtkomsten och framtvinga omautentisering när det behövs. Netto resultatet av det här mönstret är färre anrop för att hämta token, vilket innebär att slut punkt till slut punkt-flödet är mer elastiskt. 

För att kunna använda CAE måste både tjänsten och klienten vara CAE-kompatibla. Microsoft 365 tjänster som Exchange Online, teams och SharePoint Online Support CAE. På klient sidan är de webbläsarbaserade upplevelser som använder dessa Office 365-tjänster (t. ex. Outlook Web App) och specifika versioner av Office 365 Native-klienterna CAE-kompatibla. Fler Microsoft Cloud Services blir CAE-kompatibla.

Microsoft arbetar med branschen för att bygga [standarder](https://openid.net/wg/sse/) som gör det möjligt för program från tredje part att använda den här funktionen. Du kan också utveckla program som är CAE-kompatibla. Mer information finns i så här skapar du återhämtning i ditt program.

## <a name="how-do-i-implement-cae"></a>Hur gör jag för att implementera CAE?

* [Uppdatera din kod för att använda CAE-aktiverade API: er](../develop/app-resilience-continuous-access-evaluation.md).

* [Aktivera CAE](../conditional-access/concept-continuous-access-evaluation.md) i säkerhets konfigurationen för Azure AD.

* Se till att din organisation använder [kompatibla versioner](../conditional-access/concept-continuous-access-evaluation.md) av Microsoft Office inbyggda program.

* [Optimera dina omautentiserings-prompter](../authentication/concepts-azure-multi-factor-authentication-prompts-session-lifetime.md).

 
## <a name="next-steps"></a>Nästa steg
Återhämtnings resurser för administratörer och arkitekter
 
* [Bygg återhämtning med hantering av autentiseringsuppgifter](resilience-in-credentials.md)

* [Bygg återhämtning med enhets tillstånd](resilience-with-device-states.md)

* [Bygg återhämtning vid extern användarautentisering](resilience-b2b-authentication.md)

* [Bygg återhämtning i din hybrid autentisering](resilience-in-hybrid.md)

* [Bygg återhämtning i program åtkomst med Application Proxy](resilience-on-premises-access.md)

Återhämtnings resurser för utvecklare

* [Bygg IAM-återhämtning i dina program](resilience-app-development-overview.md)

* [Bygg återhämtning i dina CIAM-system](resilience-b2c.md)
