---
title: Bygg mer elastisk hybrid autentisering i Azure Active Directory
description: En guide för arkitekter och IT-administratörer om att skapa en elastisk hybrid infrastruktur.
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
ms.openlocfilehash: de01788c01d4d6dedd9563faaaac07bff30bbd97
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98724767"
---
# <a name="build-resilience-in-your-hybrid-architecture"></a>Bygg återhämtning i din hybrid arkitektur

Med hybrid autentisering kan användarna få åtkomst till molnbaserade resurser med sina identiteter som har hanterats lokalt. En hybrid infrastruktur omfattar både molnbaserade och lokala komponenter.

* Moln komponenter omfattar Azure AD, Azure-resurser och-tjänster, din organisations molnbaserade appar och SaaS-program.

* Lokala komponenter omfattar lokala program, resurser som SQL-databaser och en identitets leverantör som Windows Server Active Directory. 

> [!IMPORTANT]
> När du planerar för återhämtning i din hybrid infrastruktur är det viktigt att minimera beroenden och enskilda felpunkter. 

Microsoft erbjuder tre mekanismer för Hybrid autentisering. Alternativen visas i återhämtnings ordning. Vi rekommenderar att du implementerar Password hash-synkronisering om det är möjligt.

* PHS ( [Password hash Synchronization](../hybrid/whatis-phs.md) ) använder Azure AD Connect för att synkronisera identiteten och hashen för lösen ordets hash-värde till Azure AD, så att användarna kan logga in på molnbaserade resurser med sitt lösen ord lokalt. PHS har endast lokala beroenden för synkronisering, inte för autentisering.

* [Direkt autentisering](../hybrid/how-to-connect-pta.md) (PTA) omdirigerar användare till Azure AD för inloggning. Sedan verifieras användar namnet och lösen ordet mot Active Directory lokalt, via en agent som distribueras i företags nätverket. PTA har en lokal plats för sina Azure AD PTA-agenter som finns på lokala servrar.

* [Federations](../hybrid/whatis-fed.md) kunder distribuerar en Federations tjänst, till exempel AD FS, och sedan validerar Azure AD SAML-kontrollen som genereras av Federations tjänsten. Federationen har det högsta beroendet för den lokala infrastrukturen och därmed fler felpoäng. 

   
Du kan använda en eller flera av dessa metoder i din organisation. Mer information finns i [Välj rätt autentiseringsmetod för din Azure AD hybrid Identity-lösning](../hybrid/choose-ad-authn.md). Den här artikeln innehåller ett besluts träd som kan hjälpa dig att avgöra din metod.

## <a name="password-hash-synchronization"></a>Synkronisering av lösenordshash

Det enklaste och mest elastiska hybrid verifierings alternativet för Azure AD är ett [lösen ord för hash-synkronisering](../hybrid/whatis-phs.md) som inte har något lokalt identitets infrastrukturs beroende vid bearbetning av autentiseringsbegäranden. När identiteter med hash-värden för lösen ord synkroniseras till Azure AD kan användarna autentisera till moln resurser utan att det är beroende av de lokala identitets komponenterna. 

![Arkitektur diagram för PHS](./media/resilience-in-hybrid/admin-resilience-password-hash-sync.png)

Om du väljer det här autentiseringsalternativet får du inget avbrott när lokala identitets komponenter blir otillgängliga. Ett lokalt avbrott kan inträffa av många orsaker, inklusive maskin varu fel, strömavbrott, natur katastrofer och attacker mot skadlig kod. 

### <a name="how-do-i-implement-phs"></a>Hur gör jag för att implementera PHS?

Information om hur du implementerar PHS finns i följande resurser:

* [Implementera hash-synkronisering av lösen ord med Azure AD Connect](../hybrid/how-to-connect-password-hash-synchronization.md)

* [Aktivera hashsynkronisering för lösenord](../hybrid/how-to-connect-password-hash-synchronization.md)

Om dina krav är sådana att du inte kan använda PHS använder du direktautentisering.

## <a name="pass-through-authentication"></a>Direktautentisering

Direktautentisering är beroende av autentiserings agenter som finns lokalt på servrar. En permanent anslutning, eller Service Bus, finns mellan Azure AD och de lokala PTA-agenterna. Brand väggen, servrar som är värd för Autentiseringstjänsten och den lokala Windows Server-Active Directory (eller någon annan identitets leverantör) är alla potentiella felpunkter. 

![Arkitektur diagram för PTA](./media/resilience-in-hybrid/admin-resilience-pass-through-authentication.png)

### <a name="how-do-i-implement-pta"></a>Hur gör jag för att implementera PTA?

Information om hur du implementerar direktautentisering finns i följande resurser.

* [Så här fungerar direkt autentisering](../hybrid/how-to-connect-pta-how-it-works.md)

* [Ingående information om säkerhet vid direktautentisering](../hybrid/how-to-connect-pta-security-deep-dive.md)

* [Installera direkt autentisering i Azure AD](../hybrid/how-to-connect-pta-quick-start.md)

* Om du använder PTA definierar du en [topologi med hög](../hybrid/how-to-connect-pta-quick-start.md)tillgänglighet.

 ## <a name="federation"></a>Federation

Federationen innebär att en förtroende relation mellan Azure AD och Federations tjänsten skapas, vilket omfattar bytet av slut punkter, certifikat för tokensignering och andra metadata. När en begäran kommer till Azure AD läser den konfigurationen och omdirigerar användaren till de slut punkter som har kon figurer ATS. Vid det här tillfället agerar användaren med Federations tjänsten, som utfärdar en SAML-kontroll som verifieras av Azure AD. 

Följande diagram visar en topologi för företags Active Directory Federation Services (AD FS) (AD FS), distribution som innehåller redundanta Federations-och Webbprogramproxy i flera lokala data Center. Den här konfigurationen är beroende av komponenter för företags nätverks infrastruktur som DNS, utjämning av nätverks belastning med funktioner för geo-tillhörighet, brand väggar osv. Alla lokala komponenter och anslutningar är utsatta för att Miss lyckas. Mer information finns i [dokumentationen för AD FS kapacitets planering](/windows-server/identity/ad-fs/design/planning-for-ad-fs-server-capacity) .

> [!NOTE]
>  Federation har det högsta antalet lokala beroenden och därför de mest potentiella felen. Även om det här diagrammet visar AD FS, är andra lokala identitets leverantörer underkastade liknande design överväganden för att uppnå hög tillgänglighet, skalbarhet och redundans.

![Arkitektur diagram för Federation](./media/resilience-in-hybrid/admin-resilience-federation.png)

 ### <a name="how-do-i-implement-federation"></a>Hur gör jag för att implementera federationen?

Om du implementerar en strategi för federerade autentisering eller vill göra den mer elastisk kan du läsa följande resurser.

* [Vad är federerad autentisering](../hybrid/whatis-fed.md)

* [Så här fungerar federation](../hybrid/how-to-connect-fed-whatis.md)

* [Kompatibilitetslista för Azure AD-federation](../hybrid/how-to-connect-fed-compatibility.md)

* Följ [dokumentationen för AD FS kapacitets planering](/windows-server/identity/ad-fs/design/planning-for-ad-fs-server-capacity)

* [Distribuera AD FS i Azure IaaS](/windows-server/identity/ad-fs/deployment/how-to-connect-fed-azure-adfs)

* [Aktivera PHS](../hybrid/tutorial-phs-backup.md) tillsammans med din Federation

## <a name="next-steps"></a>Nästa steg
Återhämtnings resurser för administratörer och arkitekter
 
* [Bygg återhämtning med hantering av autentiseringsuppgifter](resilience-in-credentials.md)

* [Bygg återhämtning med enhets tillstånd](resilience-with-device-states.md)

* [Bygg återhämtning med hjälp av den kontinuerliga åtkomst utvärderingen (CAE)](resilience-with-continuous-access-evaluation.md)

* [Bygg återhämtning vid extern användarautentisering](resilience-b2b-authentication.md)

* [Bygg återhämtning i program åtkomst med Application Proxy](resilience-on-premises-access.md)

Återhämtnings resurser för utvecklare

* [Bygg IAM-återhämtning i dina program](resilience-app-development-overview.md)

* [Bygg återhämtning i dina CIAM-system](resilience-b2c.md)