---
title: Bestäm din säkerhets position för externt samarbete med Azure Active Directory
description: Innan du kan köra en säkerhets plan för extern åtkomst måste du bestämma vad du försöker uppnå.
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 12/18/2020
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 37c27e84f15a01a2d8832baae137518685de59a8
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/23/2021
ms.locfileid: "98725449"
---
# <a name="determine-your-security-posture-for-external-access"></a>Bestäm din säkerhets position för extern åtkomst 

När du funderar på att styra extern åtkomst måste du bedöma säkerhets-och samarbets behoven för din organisations övergripande och inom varje scenario. På organisations nivå bör du ta hänsyn till hur mycket kontroll du behöver för IT-teamet för att få över det dagliga samarbetet. Organisationer i reglerade branscher kan kräva mer IT-kontroll. En skydds entreprenör kan till exempel krävas för att identifiera och dokumentera varje extern användare, deras åtkomst och borttagning av åtkomst. Detta krav kan vara för all åtkomst eller i vissa scenarier eller arbets belastningar. I andra änden av spektrumet kan ett konsult företag vanligt vis ge slutanvändarna möjlighet att fastställa vilka externa användare de behöver samar beta med, inom vissa IT Guard-spår. 

![IT och slut användar kontroll för samarbete](media/secure-external-access/1-overall-control.png)

> [!NOTE]
> Övertätt kontroll över samarbetet kan leda till högre IT-budgetar, minskad produktivitet och fördröjda verksamhets resultat. När de officiella samarbets kanalerna uppfattas som för betungande kan slutanvändarna gå runt IT-system för att få jobben gjorda, till exempel e-posta oskyddade dokument.

## <a name="think-in-terms-of-scenarios"></a>Tänk på scenarier

I många fall kan den Delegera åtkomst till partnern, minst i vissa scenarier, samtidigt som de tillhandahåller skydds skenor. IT Guard-spåren kan vara till hjälp för att säkerställa att immateriell egendom förblir säker, samtidigt som de kan samar beta med partners för att få jobbet gjort.

När du funderar på scenarier i din organisation bör du bedöma behovet av medarbetare och affärs partner åtkomst till resurser. En bank kan ha behov av efterlevnad som begränsar åtkomsten till vissa resurser, t. ex. användar konto information, till en liten grupp interna anställda. Samma bank kan däremot ge delegerad åtkomst till partner som arbetar på en marknadsförings kampanj.

![Continuum för styrning per scenario](media\secure-external-access\1-scenarios.png)

I varje scenario kan du överväga att 

* känsligheten för informationen i risk zonen

* Om du behöver begränsa vilka partners som kan se om andra användare

* kostnaden för en överträdelse jämfört med den centrala kontrollen och slutanvändarens friktion

 Du kan också börja med centralt hanterade kontroller för att uppfylla kompatibla mål och delegera kontroll till slutanvändare över tid. Alla åtkomst hanterings modeller kan samtidigt finnas i en organisation. 

Användningen av [hanterade autentiseringsuppgifter för partner](../external-identities/what-is-b2b.md) ger din organisation en viktig signal som avslutar åtkomsten till dina resurser när den externa användaren har förlorat åtkomst till resurserna i sitt eget företag.

## <a name="goals-of-securing-external-access"></a>Mål för att skydda extern åtkomst

Målen för IT-styrd och delegerad åtkomst skiljer sig åt.

**De primära målen med IT-styrd åtkomst är att:**

* Uppfylla styrnings-, reglerings-och efterlevnads-(GRC) mål. 

* Noggrant kontrol lera partner åtkomsten och vilka partners som kan se om medlems användare, grupper och andra partner.

**De främsta målen med att delegera åtkomst är att:**

* Ge företags ägare möjlighet att styra vem de samarbetar med, inom IT-begränsningar.

* Gör det möjligt för affärs partner att begära åtkomst baserat på regler som definieras av företags ägare.

Oavsett vilket du använder för din organisation och scenarier behöver du: 

* **Kontrol lera åtkomsten till program, data och innehåll**. Detta kan åstadkommas via en mängd olika metoder, beroende på dina versioner av [Azure AD](https://azure.microsoft.com/pricing/details/active-directory/) och [Microsoft 365](https://www.microsoft.com/microsoft-365/compare-microsoft-365-enterprise-plans). 

* **Minska angrepps ytan**. [Privileged Identity Management](../privileged-identity-management/pim-configure.md), [data förlust skydd (DLP)](/exchange/security-and-compliance/data-loss-prevention/data-loss-prevention) och [krypterings funktioner](/exchange/security-and-compliance/data-loss-prevention/data-loss-prevention) minskar attack ytan.

* **Granska regelbundet aktivitet och gransknings loggen för att bekräfta efterlevnad**. Det kan delegera åtkomst beslut till företags ägare via rättighets hantering medan åtkomst granskningar ger ett sätt att regelbundet bekräfta fortsatt åtkomst. Automatiserad data klassificering med känslighets etiketter hjälper till att automatisera kryptering av känsligt innehåll, vilket gör det enklare för slutanvändare av anställda att följa.

## <a name="next-steps"></a>Nästa steg 

Se följande artiklar om hur du skyddar extern åtkomst till resurser. Vi rekommenderar att du vidtar åtgärder i den angivna ordningen.

1. [Bestäm din säkerhets position för extern åtkomst](1-secure-access-posture.md) (du är här.)

2. [Identifiera ditt nuvarande tillstånd](2-secure-access-current-state.md)

3. [Skapa en styrnings plan](3-secure-access-plan.md)

4. [Använda grupper för säkerhet](4-secure-access-groups.md)

5. [Över gång till Azure AD B2B](5-secure-access-b2b.md)

6. [Säker åtkomst med hantering av rättigheter](6-secure-access-entitlement-managment.md)

7. [Säker åtkomst med principer för villkorlig åtkomst](7-secure-access-conditional-access.md)

8. [Säker åtkomst med känslighets etiketter](8-secure-access-sensitivity-labels.md)

9. [Säker åtkomst till Microsoft Teams, OneDrive och SharePoint](9-secure-access-teams-sharepoint.md)
 

