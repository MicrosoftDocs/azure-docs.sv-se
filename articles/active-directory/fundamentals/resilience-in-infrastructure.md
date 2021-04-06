---
title: Bygg återhämtning i din IAM-infrastruktur med Azure Active Directory
description: En guide för arkitekter och IT-administratörer som inte skapar återhämtning till avbrott i sin IAM-infrastruktur.
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
ms.openlocfilehash: 64fe4b8c217ec46cbb6dd046339c3ac65eebb121
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "98724685"
---
# <a name="build-resilience-in-your-identity-and-access-management-infrastructure"></a>Bygg återhämtning i din infrastruktur för identitets-och åtkomst hantering

Azure Active Directory är ett globalt moln identitets-och åtkomst hanterings system som tillhandahåller kritiska tjänster som autentisering och auktorisering till din organisations resurser. I det här dokumentet får du vägledning om hur du förstår, innehåller och minimerar risken för avbrott i autentiserings-eller auktoriserings tjänster för resurser som förlitar sig på Azure Active Directory (Azure AD). 

Dokument uppsättningen är utformad för

* Identitets arkitekter

* Identitet tjänst ägare

* Identitets drifts team

Läs även dokumentationen för [programutvecklare](./resilience-app-development-overview.md) och för [Azure AD B2C system](resilience-b2c.md).

## <a name="what-is-resilience"></a>Vad är återhämtning?

I kontexten för din identitets infrastruktur kan du använda återhämtning för att kunna utnyttja avbrott i tjänster som autentisering och auktorisering, eller för att Miss lyckas med andra komponenter, med minimal eller ingen påverkan på din verksamhet, användare och verksamhet. Effekten av avbrott kan vara svår och återhämtning kräver noggrann planering.

## <a name="why-worry-about-disruption"></a>Varför bekymra dig om avbrott?

Varje anrop till Authentication system är beroende av avbrott om någon del av anropet Miss lyckas. När autentiseringen avbryts på grund av de underliggande komponent felen kommer användarna inte att få åtkomst till sina program. Därför är det viktigt att minska antalet autentiserings anrop och antalet beroenden i dessa anrop till din återhämtning. Programutvecklare kan kontrol lera viss kontroll över hur ofta token begärs. Du kan till exempel samar beta med dina utvecklare för att se till att de använder Azure AD Managed-identiteter för sina program närhelst det är möjligt. 

I ett tokenbaserat autentiseringsprotokoll som Azure AD, måste en användares program (klient) Hämta en säkerhetstoken från identitets systemet innan den kan komma åt ett program eller en annan resurs. Under giltighets perioden kan en klient presentera samma token flera gånger för att få åtkomst till programmet.

När den token som visas för programmet upphör att gälla avvisar programmet token och klienten måste skaffa en ny token från Azure AD. Att förvärva en ny token kan kräva användar interaktion, t. ex. autentiseringsuppgifter för autentiseringsuppgifter eller för att uppfylla andra krav för autentiserings systemet. Att minska frekvensen för autentiserings anrop med längre livs längds tokens minskar onödig interaktion. Du måste dock balansera token Life med den risk som skapats av färre princip utvärderingar. Mer information om hur du hanterar livs längd för token finns i den här artikeln om hur du [optimerar omautentiserings-prompter](../authentication/concepts-azure-multi-factor-authentication-prompts-session-lifetime.md).

## <a name="ways-to-increase-resilience"></a>Sätt att öka återhämtningen
I följande diagram visas sex konkreta sätt som du kan öka återhämtningen på. Varje metod beskrivs i detalj i artiklarna som är länkade i nästa steg i den här artikeln.
  
![Diagram som visar översikt över administratörs återhämtning](./media/resilience-in-infrastructure/admin-resilience-overview.png)

## <a name="next-steps"></a>Nästa steg
Återhämtnings resurser för administratörer och arkitekter
 
* [Bygg återhämtning med hantering av autentiseringsuppgifter](resilience-in-credentials.md)

* [Bygg återhämtning med enhets tillstånd](resilience-with-device-states.md)

* [Bygg återhämtning med hjälp av den kontinuerliga åtkomst utvärderingen (CAE)](resilience-with-continuous-access-evaluation.md)

* [Bygg återhämtning vid extern användarautentisering](resilience-b2b-authentication.md)

* [Bygg återhämtning i din hybrid autentisering](resilience-in-hybrid.md)

* [Bygg återhämtning i program åtkomst med Application Proxy](resilience-on-premises-access.md)

Återhämtnings resurser för utvecklare

* [Bygg IAM-återhämtning i dina program](resilience-app-development-overview.md)

* [Bygg återhämtning i dina CIAM-system](resilience-b2c.md)