---
title: Design av hybrid identitet – hanterings uppgifter Azure | Microsoft Docs
description: Azure AD kontrollerar de specifika villkor som du väljer när du autentiserar användaren och innan du tillåter åtkomst till programmet med villkorlig åtkomst kontroll.
documentationcenter: ''
services: active-directory
author: billmath
manager: daveba
editor: ''
ms.assetid: 65f80aea-0426-4072-83e1-faf5b76df034
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/29/2019
ms.subservice: hybrid
ms.author: billmath
ms.custom: seohack1
ms.collection: M365-identity-device-management
ms.openlocfilehash: c416bf19acb1736eeed679c16dbd87de1cc98537
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "90986526"
---
# <a name="plan-for-hybrid-identity-lifecycle"></a>Planera för Hybrid identitets livs cykel
Identiteten är en av grunderna i din Enterprise Mobility-och program åtkomst strategi. Oavsett om du loggar in på din mobila enhet eller SaaS-app, är din identitet nyckeln för att få åtkomst till allt. På sin högsta nivå omfattar en identitets hanterings lösning att förena och synkronisera dina identitets lager, vilket omfattar automatisering och centralisera processen för etablering av resurser. Identitets lösningen bör vara en centraliserad identitet i både lokalt och i molnet och kan också använda någon form av identitets Federation för att upprätthålla centraliserad autentisering och dela och samar beta med externa användare och företag på ett säkert sätt. Resurs intervall från operativ system och program till personer i, eller som är kopplade till, en organisation. Organisations strukturen kan ändras för att hantera etablerings principer och procedurer.

Det är också viktigt att ha en identitets lösning som är avsedd att hjälpa dina användare genom att tillhandahålla självbetjänings upplevelser för att hålla dem produktiva. Din identitets lösning är mer robust om den möjliggör enkel inloggning för användare över alla resurser som de behöver åtkomst till. Administratörer på alla nivåer kan använda standardiserade procedurer för att hantera användarautentiseringsuppgifter. Vissa administrations nivåer kan minskas eller elimineras, beroende på bredden för etablerings hanterings lösningen. Dessutom kan du på ett säkert sätt distribuera administrations funktioner manuellt eller automatiskt mellan olika organisationer. En domän administratör kan till exempel endast hantera personer och resurser i domänen. Den här användaren kan utföra administrativa och etablerings uppgifter, men har inte behörighet att utföra konfigurations åtgärder, till exempel skapa arbets flöden.

## <a name="determine-hybrid-identity-management-tasks"></a>Fastställa uppgifter för Hybrid identitets hantering
Genom att distribuera administrativa uppgifter i din organisation förbättras precisionen och effektiviteten i administrationen och det går snabbare att balansera arbets belastningen i en organisation. Följande är de pivoteror som definierar ett robust identitets hanterings system.

 ![överväganden för identitets hantering](./media/plan-hybrid-identity-design-considerations/Identity_management_considerations.png)

Om du vill definiera uppgifter för Hybrid identitets hantering måste du förstå några viktiga egenskaper hos den organisation som ska anta hybrid identitet. Det är viktigt att förstå de aktuella databaser som används för identitets källor. Genom att känna till de grundläggande elementen får du grundläggande krav och bygger på att du behöver ställa mer detaljerade frågor som leder dig till ett bättre design beslut för din identitets lösning.  

När du definierar dessa krav bör du se till att minst följande frågor besvaras

* Etablerings alternativ: 
  
  * Stöder hybrid identitets lösningen en robust konto åtkomst hantering och ett etablerings system?
  * Hur ska användare, grupper och lösen ord hanteras?
  * Svarar livs cykeln för identitets hantering? 
    * Hur lång tid tar det för kontot att skjuta upp lösen ords uppdateringar?
* Licens hantering: 
  
  * Hanterar hybrid identitets lösningen licens hantering?
    * Om ja, vilka funktioner är tillgängliga?
  * Hanterar lösningen hantering av gruppbaserad licens? 
  
    * Om ja, är det möjligt att tilldela en säkerhets grupp till den? 
    * Om ja, kommer moln katalogen automatiskt tilldela licenser till alla medlemmar i gruppen? 
    * Vad händer om en användare senare läggs till i eller tas bort från gruppen, kommer en licens automatiskt att tilldelas eller tas bort efter behov? 
* Integrering med andra tredjeparts identitets leverantörer:
  * Kan denna hybrid lösning integreras med identitets leverantörer från tredje part för att implementera enkel inloggning?
  * Är det möjligt att förena alla olika identitets leverantörer i ett sammanhängande identitets system?
  * Om ja, hur och vilka funktioner är tillgängliga?

## <a name="synchronization-management"></a>Hantering av synkronisering
Ett av målen för en identitets hanterare, för att kunna ta alla identitets leverantörer och synkronisera dem. Du behåller synkroniserade data baserat på en auktoritativ huvud identitets leverantör. I ett hybrid identitets scenario, med en synkroniserad hanterings modell, hanterar du alla användar-och enhets identiteter på en lokal server och synkroniserar kontona och eventuellt lösen ord till molnet. Användaren anger samma lösen ord lokalt som i molnet, och vid inloggningen verifieras lösen ordet av identitets lösningen. Den här modellen använder ett verktyg för katalog synkronisering.

![Katalog synkronisering ](./media/plan-hybrid-identity-design-considerations/Directory_synchronization.png) för korrekt design synkroniseringen av din hybrid identitets lösning kontrol lera att följande frågor besvaras:
*    Vilka är Sync-lösningarna tillgängliga för Hybrid identitets lösningen?
*    Vilka är funktionerna för enkel inloggning tillgänglig?
*    Vilka är alternativen för identitets federation mellan B2B och B2C?

## <a name="next-steps"></a>Nästa steg
[Fastställ strategi för införande av hybrid identitets hantering](plan-hybrid-identity-design-considerations-lifecycle-adoption-strategy.md)

## <a name="see-also"></a>Se även
[Översikt över design överväganden](plan-hybrid-identity-design-considerations-overview.md)

