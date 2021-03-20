---
title: Azure AD Connect djup i Cloud Sync – hur det fungerar
description: Det här avsnittet innehåller djupgående information om hur Cloud Sync fungerar.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 12/05/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0e1079ab4a523fbbc99c1e9190aef960b3f0723f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98614319"
---
# <a name="cloud-sync-deep-dive---how-it-works"></a>Djup invisning i Cloud Sync – hur det fungerar

## <a name="overview-of-components"></a>Översikt över komponenter

![Så här fungerar det](media/concept-how-it-works/how-1.png)

Cloud Sync bygger på Azure AD-tjänsterna och har två viktiga komponenter:

- **Etablerings agent**: Azure AD Connect moln etablerings agenten är samma agent som inkommande arbets dag och bygger på samma teknik för Server sidan som App proxy och passerar genom autentisering. Det kräver endast utgående anslutningar och agenter uppdateras automatiskt. 
- **Etablerings tjänst**: samma etablerings tjänst som utgående etablering och inkommande etablering av arbets dagar som använder en Scheduler-baserad modell. I händelse av molnbaserad synkronisering har ändringarna tillhandahållits var 2: e minut.


## <a name="initial-setup"></a>Första konfigurationen
Under den första installationen görs några saker som gör att Cloud Sync sker.  Dessa är: 

- **Under Agent installationen**: du konfigurerar agenten för de AD-domäner som du vill etablera från.  Den här konfigurationen registrerar domänerna i hybrid identitets tjänsten och upprättar en utgående anslutning till Service Bus-lyssningen för begär Anden.
- **När du aktiverar etablering**: du väljer AD-domänen och aktiverar etablering som körs var 2: e minut. Alternativt kan du avmarkera lösen ordets hash-synkronisering och definiera e-postmeddelandet. Du kan också hantera Attribute-transformering med Microsoft Graph-API: er.


## <a name="agent-installation"></a>Agentinstallation
Följande är en genom gång av vad som händer när moln etablerings agenten installeras.

- Först installerar installations programmet agentens binärfiler och Agent tjänsten som körs under det virtuella tjänst kontot (NETWORK SERVICE\AADProvisioningAgent).  Ett virtuellt tjänst konto är en särskild typ av konto som inte har ett lösen ord och som hanteras av Windows.
- Installations programmet startar sedan guiden.
- Guiden kommer att fråga efter autentiseringsuppgifter för Azure AD, kommer sedan att autentiseras och hämta en token.
- Guiden frågar sedan efter den aktuella datorns domän administratörs autentiseringsuppgifter.
- Med hjälp av dessa autentiseringsuppgifter skapas eller lokaliseras agentens allmänt hanterade tjänst konto (GMSA) för den här domänen eller lokaliseras och återanvänds om det redan finns.
- Agent tjänsten har nu kon figurer ATS om för körning under GMSA.
- Guiden frågar nu om domän konfiguration tillsammans med det DA-konto (Enterprise Admin (EA)/Domain admin) för varje domän som du vill att agenten ska betjäna.
- GMSA-kontot uppdateras sedan med behörigheter som ger åtkomst till varje domän som anges ovan.
- Sedan utlöser guiden agent registrering
- Agenten skapar ett certifikat och använder Azure AD-token, registrerar sig och certifikatet med tjänsten hybrid Identity service (registrering)
- Guiden utlöser ett AgentResourceGrouping-anrop. Det här anropet till sin admin-tjänst är att tilldela agenten till en eller flera AD-domäner i konfigurationen.
- Nu startar guiden om Agent tjänsten.
- Agenten anropar en start tjänst vid omstart (och var 10: e minut efteråt) för att söka efter konfigurations uppdateringar.  Start tjänsten verifierar agent identiteten.  Den uppdaterar också den senaste start tiden.  Detta är viktigt eftersom om agenter inte startar, kommer de inte att få uppdaterade Service Bus slut punkter och kan inte ta emot begär Anden. 


## <a name="what-is-system-for-cross-domain-identity-management-scim"></a>Vad är system för SCIM (Cross-Domain Identity Management)?

[Scim-specifikationen](https://tools.ietf.org/html/draft-scim-core-schema-01) är en standard som används för att automatisera utbyte av användar-eller grupp identitets information mellan identitets domäner som Azure AD. SCIM håller på att bli den facto standard för etablering och, om det används tillsammans med Federations standarder som SAML eller OpenID Connect, ger administratörer en heltäckande lösning för åtkomst hantering från slut punkt till slut punkt.

Azure AD Connect Cloud Provisioning-agenten använder SCIM med Azure AD för att etablera och avetablera användare och grupper.

## <a name="synchronization-flow"></a>Flöde för synkronisering
![](media/concept-how-it-works/provisioning-4.png)när du har installerat agenten och aktiverat etableringen inträffar följande flöde.

1.  När Azure AD Provisioning-tjänsten har kon figurer ATS anropar Azure AD hybrid service för att lägga till en begäran till Service Bus. Agenten upprätthåller kontinuerligt en utgående anslutning till Service Bus som lyssnar efter begär Anden och hämtar systemet för SCIM-begäran (Cross-Domain Identity Management) omedelbart. 
2.  Agenten delar upp begäran i separata frågor baserat på objekt typ. 
3.  AD returnerar resultatet till agenten och agenten filtrerar dessa data innan de skickas till Azure AD.  
4.  Agenten returnerar SCIM-svaret till Azure AD.  Dessa svar baseras på den filtrering som har inträffat i agenten.  Agenten använder omfånget för att filtrera resultaten. 
5.  Etablerings tjänsten skriver ändringarna i Azure AD.
6. Om detta är en Delta-synkronisering i stället för en fullständig synkronisering, används cookie/vattenstämpel. Nya frågor kommer att få ändringar från denna cookie/vattenstämpel.

## <a name="supported-scenarios"></a>Scenarier som stöds:
Följande scenarier stöds för molnbaserad synkronisering.


- **Befintlig hybrid kund med en ny skog**: Azure AD Connect Sync används för primära skogar. Molnbaserad synkronisering används för etablering från en AD-skog (inklusive frånkopplad). Mer information finns i självstudien [här](tutorial-existing-forest.md).

 ![Befintlig hybrid](media/tutorial-existing-forest/existing-forest-new-forest-2.png)
- **Ny hybrid kund**: Azure AD Connect Sync används inte. Molnbaserad synkronisering används för etablering från en AD-skog.  Mer information finns i självstudien [här](tutorial-single-forest.md).
 
 ![Nya kunder](media/tutorial-single-forest/diagram-2.png)

- **Befintlig hybrid kund**: Azure AD Connect Sync används för primära skogar. Cloud Sync är Piloted för en liten uppsättning användare i de primära skogarna [här](tutorial-existing-forest.md).

 ![Befintlig pilot](media/tutorial-migrate-aadc-aadccp/diagram-2.png)

Mer information finns i [topologier som stöds](plan-cloud-sync-topologies.md).



## <a name="next-steps"></a>Nästa steg 

- [Vad är etablering?](what-is-provisioning.md)
- [Vad är Azure AD Connect Cloud Sync?](what-is-cloud-sync.md)
