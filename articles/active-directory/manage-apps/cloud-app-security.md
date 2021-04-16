---
title: Appsynlighet och kontroll med Microsoft Cloud App Security
description: Lär dig hur du identifierar apprisknivåer, stoppar överträdelser och läckor i realtid och använder appanslutningsappar för att dra nytta av provider-API:er för synlighet och styrning.
services: active-directory
author: iantheninja
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: conceptual
ms.workload: identity
ms.date: 02/03/2020
ms.author: iangithinji
ms.collection: M365-identity-device-management
ms.openlocfilehash: d9a9f56d70e049200cee0c3655a9998feccfa301
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2021
ms.locfileid: "107377844"
---
# <a name="cloud-app-visibility-and-control"></a>Synlighet och kontroll för molnapp

För att få full nytta av molnappar och molntjänster måste ett IT-team hitta rätt balans mellan att stödja åtkomst och samtidigt behålla kontrollen för att skydda viktiga data. Microsoft Cloud App Security ger omfattande synlighet, kontroll över dataresor och avancerad analys för att identifiera och bekämpa cyberhot i alla dina Molntjänster från Microsoft och tredje part.

## <a name="discover-and-manage-shadow-it-in-your-network"></a>Identifiera och hantera skugg-IT i ditt nätverk

När IT-administratörer tillfrågas hur många molnappar de tror att deras anställda använder säger de i genomsnitt 30 eller 40. I verkligheten är genomsnittet över 1 000 separata appar som används av anställda i din organisation. Shadow IT hjälper dig att känna till och identifiera vilka appar som används och vilken risknivå du har. 80 procent av de anställda använder osanktionerade appar som ingen har granskat och kanske inte är kompatibla med dina säkerhets- och efterlevnadsprinciper. Och eftersom dina anställda kan komma åt dina resurser och appar utanför företagets nätverk, räcker det inte längre att ha regler och principer i brandväggarna.

Använd Microsoft Cloud App Discovery (en Azure Active Directory Premium P1-funktion) för att identifiera vilka appar som används, utforska risken för dessa appar, konfigurera principer för att identifiera nya riskfyllda appar och ta bort sanktionering av dessa appar för att blockera dem inbyggt med hjälp av proxy- eller brandväggsinstallationen.

- Upptäcka och identifiera skugg-IT
- Utvärdera och analysera
- Hantera dina appar
- Rapportering om avancerad skugg-IT-identifiering
- Kontrollera sanktionerade appar
 
### <a name="learn-more"></a>Läs mer

- [Identifiera och hantera skugg-IT i nätverket ](/cloud-app-security/tutorial-shadow-it)
- [Identifierade appar med Cloud App Security ](/cloud-app-security/discovered-apps)
 
## <a name="user-session-visibility-and-control"></a>Synlighet och kontroll för användarsessioner 

På dagens arbetsplats räcker det ofta inte att veta vad som händer i molnmiljön i efterhand. Du vill stoppa överträdelser och läckor i realtid innan anställda avsiktligt eller oavsiktligt riskerar dina data och din organisation. Tillsammans med Azure Active Directory (Azure AD) Microsoft Cloud App Security dessa funktioner i en holistisk och integrerad upplevelse med Appkontroll för villkorsstyrd åtkomst. 

Sessionskontrollen använder en omvänd proxyarkitektur och är unikt integrerad med villkorsstyrd åtkomst i Azure AD. Med villkorsstyrd åtkomst i Azure AD kan du tillämpa åtkomstkontroller på din organisations appar baserat på vissa villkor. Villkoren definierar vem (användare eller grupp av användare) och vilka (vilka molnappar) och var (vilka platser och nätverk) en princip för villkorlig åtkomst tillämpas på. När du har fastställt villkoren kan du dirigera användare till Cloud App Security där du kan skydda data i realtid.  

Med den här kontrollen kan du:  
- Kontrollera filnedladdningar
- Övervaka B2B-scenarier  
- Kontrollera åtkomsten till filer  
- Skydda dokument vid nedladdning  
 
### <a name="learn-more"></a>Läs mer

- [Skydda appar med sessionskontroll i Cloud App Security ](/cloud-app-security/proxy-intro-aad)
 
## <a name="advanced-app-visibility-and-controls"></a>Avancerad appsynlighet och kontroller 

Appanslutningsappar använder API:er från appleverantörer för att ge bättre synlighet och kontroll Microsoft Cloud App Security över de appar som du ansluter till. Cloud App Security använder de API:er som tillhandahålls av molnleverantören. Varje tjänst har ett eget ramverk och API-begränsningar som begränsning, API-gränser, API-fönster för dynamisk tidsväxling med mera. Produktteamet Cloud App Security arbetat med dessa tjänster för att optimera användningen av API:er och ge bästa möjliga prestanda. Med hänsyn till de olika begränsningar som tjänster har på sina API:er använder Cloud App Security sin högsta tillåtna kapacitet. Vissa åtgärder, till exempel genomsökning av alla filer i klientorganisationen, kräver flera API-anrop så att de sprids över en längre period. Du kan förvänta dig att vissa principer körs i flera timmar eller dagar. 
 
### <a name="learn-more"></a>Läs mer  

- [Ansluta appar i Cloud App Security ](/cloud-app-security/enable-instant-visibility-protection-and-governance-actions-for-your-apps)

## <a name="next-steps"></a>Nästa steg

- [Identifiera och hantera skugg-IT i nätverket ](/cloud-app-security/tutorial-shadow-it)
- [Identifierade appar med Cloud App Security ](/cloud-app-security/discovered-apps)
- [Skydda appar med sessionskontroll i Cloud App Security ](/cloud-app-security/proxy-intro-aad)
- [Ansluta appar i Cloud App Security ](/cloud-app-security/enable-instant-visibility-protection-and-governance-actions-for-your-apps)