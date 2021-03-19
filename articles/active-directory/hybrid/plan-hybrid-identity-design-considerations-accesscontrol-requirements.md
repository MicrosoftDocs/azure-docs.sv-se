---
title: Krav för åtkomst kontroll för Hybrid Identitets design Azure | Microsoft Docs
description: Omfattar en identitets pelare och identifiera åtkomst krav för resurser för användare i en hybrid miljö.
documentationcenter: ''
services: active-directory
author: billmath
manager: daveba
editor: ''
ms.assetid: e3b3b984-0d15-4654-93be-a396324b9f5e
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/30/2018
ms.subservice: hybrid
ms.author: billmath
ms.custom: seohack1
ms.collection: M365-identity-device-management
ms.openlocfilehash: 84b786a1701892823554a83fa2015ac88d6eff4d
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "60295151"
---
# <a name="determine-access-control-requirements-for-your-hybrid-identity-solution"></a>Fastställa krav för åtkomst kontroll för din hybrid identitets lösning
När en organisation utformar sin hybrid identitets lösning kan de också använda den här möjligheten för att granska åtkomst kraven för de resurser som de planerar att göra den tillgänglig för användare. Data åtkomsten mellan alla fyra pelare för identiteter, som är:

* Administration
* Autentisering
* Auktorisering
* Granskning

Avsnitten som följer beskriver autentisering och auktorisering i mer information, administration och granskning är en del av livs cykeln för Hybrid identiteter. Läs [se uppgifter om hybrid identitets hantering](plan-hybrid-identity-design-considerations-hybrid-id-management-tasks.md) för mer information om dessa funktioner.

> [!NOTE]
> Mer information om var och en av dessa tre pelare finns [i de fyra pelaren med identitets identitets hantering](https://social.technet.microsoft.com/wiki/contents/articles/15530.the-four-pillars-of-identity-identity-management-in-the-age-of-hybrid-it.aspx) .
> 
> 

## <a name="authentication-and-authorization"></a>Autentisering och auktorisering
Det finns olika scenarier för autentisering och auktorisering. de här scenarierna har särskilda krav som måste uppfyllas av den hybrid identitets lösning som företaget ska anta. Scenarier som involverar kommunikation mellan företag och företag (B2B) kan lägga till en extra utmaning för IT-administratörer eftersom de måste se till att metoden för autentisering och auktorisering som används av organisationen kan kommunicera med sina affärs partner. Under utformnings processen för krav på autentisering och auktorisering kontrollerar du att följande frågor besvaras:

* Kommer din organisation att autentisera och auktorisera endast användare som finns i identitets hanterings systemet?
  * Finns det några planer för B2B-scenarier?
  * Om ja, vet du redan vilka protokoll (SAML, OAuth, Kerberos eller certifikat) som ska användas för att ansluta båda företagen?
* Använder du den hybrid identitets lösning som du ska använda för att införa stöd för dessa protokoll?

En annan viktig punkt att tänka på är var i vilken databas för autentisering som kommer att användas av användare och partners och vilken administrativ modell som ska användas. Överväg följande två Core-alternativ:

* Centraliserad: i den här modellen kan användarens autentiseringsuppgifter, principer och administration vara centraliserade lokalt eller i molnet.
* Hybrid: i den här modellen kommer användarens autentiseringsuppgifter, principer och administration att centraliseras lokalt och replikeras i molnet.

Vilken modell din organisation kommer att använda varierar beroende på deras affärs krav, du vill besvara följande frågor för att identifiera var identitets hanterings systemet kommer att finnas och vilket administrativt läge som ska användas:

* Har din organisation för närvarande en identitets hantering lokalt?
  * Om ja, planerar du att behålla det?
  * Finns det några bestämmelser om reglering eller efterlevnad som din organisation måste följa för att bestämma var identitets hanterings systemet ska finnas?
* Använder din organisation enkel inloggning för appar som finns lokalt eller i molnet?
  * Om ja, påverkar implementeringen av en hybrid identitets modell den här processen?

## <a name="access-control"></a>Åtkomstkontroll
Autentisering och auktorisering är kärn element för att ge åtkomst till företags data via användarens verifiering, men det är också viktigt att kontrol lera den åtkomst nivå som användarna kommer att ha och nivån av åtkomst administratörer kommer att ha över de resurser som de hanterar. Din hybrid identitets lösning måste kunna ge detaljerad åtkomst till resurser, delegering och roll bas åtkomst kontroll. Se till att följande fråga besvaras om åtkomst kontroll:

* Har företaget fler än en användare med utökade privilegier för att hantera ditt identitets system?
  * Om ja, behöver varje användare samma åtkomst nivå?
* Skulle företaget behöva Delegera åtkomst till användare för att kunna hantera vissa resurser?
  * Om ja, hur ofta detta händer?
* Behöver företaget integrera åtkomst kontroll funktioner mellan lokala och molnbaserade resurser?
* Behöver företaget begränsa åtkomsten till resurser i enlighet med vissa villkor?
* Skulle företaget ha något program som behöver anpassad kontroll till vissa resurser?
  * Om ja, var finns apparna (lokalt eller i molnet)?
  * Om ja, var finns de mål resurserna (lokalt eller i molnet)?

> [!NOTE]
> Skriv ner varje svar och försök förstå anledningen till svaret. [Definiera data skydds strategi](plan-hybrid-identity-design-considerations-data-protection-strategy.md) går över tillgängliga alternativ och fördelar/nack delar med varje alternativ.  Genom att besvara frågorna väljer du vilket alternativ som passar dina affärs behov bäst.
> 
> 

## <a name="next-steps"></a>Nästa steg
[Fastställa krav för incidenthantering](plan-hybrid-identity-design-considerations-incident-response-requirements.md)

## <a name="see-also"></a>Se även
[Översikt över design överväganden](plan-hybrid-identity-design-considerations-overview.md)

