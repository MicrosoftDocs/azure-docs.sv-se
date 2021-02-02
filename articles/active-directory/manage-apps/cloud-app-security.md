---
title: Synlighet och kontroll av appar med Microsoft Cloud App Security
description: 'Lär dig hur du kan identifiera risk nivåer för appar, stoppa överträdelser och läckor i real tid och använda app Connectors för att dra nytta av leverantörs-API: er för synlighet och styrning.'
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: conceptual
ms.workload: identity
ms.date: 02/03/2020
ms.author: kenwith
ms.collection: M365-identity-device-management
ms.openlocfilehash: 682549b5b99169060437c8c91d465121002edc6e
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/02/2021
ms.locfileid: "99259612"
---
# <a name="cloud-app-visibility-and-control"></a>Synlighet och kontroll för molnapp

För att få full nytta av molnappar och tjänster för appar måste ett IT-team hitta rätt balans på stöd för åtkomst samtidigt som du behåller kontrollen för att skydda viktiga data. Microsoft Cloud App Security ger stor insyn, kontroll över data resor och avancerad analys för att identifiera och bekämpa cyberhot-hot i alla dina moln tjänster från Microsoft och tredje part.

## <a name="discover-and-manage-shadow-it-in-your-network"></a>Identifiera och hantera skugg-IT i ditt nätverk

När IT-administratörer tillfrågas om hur många molnappar som de anställda använder, i genomsnitt de antar 30 eller 40, i verkligheten, är genomsnittet över 1 000 separata appar som används av anställda i din organisation. Skugga det hjälper dig att identifiera och identifiera vilka appar som används och vad risk nivån är. 80 procent av anställda använder ej sanktionerade appar som ingen har granskat och kanske inte är kompatibla med dina principer för säkerhet och efterlevnad. Och eftersom dina anställda kan komma åt dina resurser och appar utanför företagets nätverk är det inte längre tillräckligt med regler och principer i brand väggarna.

Använd Microsoft Cloud App Discovery (en Azure Active Directory Premium P1-funktion) för att identifiera vilka appar som används, utforska risken för dessa appar, konfigurera principer för att identifiera nya riskfyllda appar och ta bort sanktioner för de här apparna för att blockera dem med hjälp av proxy-eller brand Väggs enheten.

- Upptäcka och identifiera skugg-IT
- Utvärdera och analysera
- Hantera dina appar
- Rapporter om avancerad skugg IT-identifiering
- Kontrol lera sanktionerade appar
 
### <a name="learn-more"></a>Läs mer

- [Identifiera och hantera skugg-IT i nätverket ](/cloud-app-security/tutorial-shadow-it)
- [Identifierade appar med Cloud App Security ](/cloud-app-security/discovered-apps)
 
## <a name="user-session-visibility-and-control"></a>Synlighet och kontroll av användarens session 

I dagens arbets plats är det ofta inte tillräckligt att känna till vad som händer i din moln miljö efter faktumet. Du vill stoppa överträdelser och läckor i real tid innan anställda avsiktligt eller oavsiktligt försätter dina data och din organisation i risk zonen. Tillsammans med Azure Active Directory (Azure AD) kan Microsoft Cloud App Security leverera dessa funktioner i en holistisk och integrerad upplevelse med Appkontroll för villkorsstyrd åtkomst. 

Session Control använder en arkitektur med omvänd proxy och är unikt integrerat med villkorlig åtkomst i Azure AD. Med villkorlig åtkomst i Azure AD kan du genomdriva åtkomst kontroller i din organisations appar baserat på vissa villkor. Villkoren definierar vem (användare eller grupp av användare) och vad (vilka molnappar) och var (vilka platser och nätverk) en princip för villkorlig åtkomst tillämpas på. När du har fastställt villkoren kan du dirigera användare till Cloud App Security där du kan skydda data i real tid.  

Med den här kontrollen kan du:  
- Styr fil hämtningar
- Övervaka B2B-scenarier  
- Kontrol lera åtkomst till filer  
- Skydda dokument vid nedladdning  
 
### <a name="learn-more"></a>Läs mer

- [Skydda appar med session Control i Cloud App Security ](/cloud-app-security/proxy-intro-aad)
 
## <a name="advanced-app-visibility-and-controls"></a>Avancerad synlighet och kontroller för appen 

App-kopplingar använder API: er för app-leverantörer för att ge bättre synlighet och kontroll genom att Microsoft Cloud App Security över de appar som du ansluter till. Cloud App Security utnyttjar de API: er som tillhandahålls av moln leverantören. Varje tjänst har sina egna Ramverks-och API-begränsningar som begränsning, API-gränser, dynamisk tids växling av API-fönster med mera. Cloud App Security produkt teamet arbetade med dessa tjänster för att optimera användningen av API: er och ge bästa möjliga prestanda. Genom att ta hänsyn till olika begränsningar i tjänsternas API: er använder Cloud App Security-motorernas högsta tillåtna kapacitet. Vissa åtgärder, till exempel genomsökning av alla filer i klienten, kräver flera API-anrop så att de sprids över en längre period. Vi räknar med att vissa principer körs i flera timmar eller dagar. 
 
### <a name="learn-more"></a>Läs mer  

- [Anslut appar i Cloud App Security ](/cloud-app-security/enable-instant-visibility-protection-and-governance-actions-for-your-apps)

## <a name="next-steps"></a>Nästa steg

- [Identifiera och hantera skugg-IT i nätverket ](/cloud-app-security/tutorial-shadow-it)
- [Identifierade appar med Cloud App Security ](/cloud-app-security/discovered-apps)
- [Skydda appar med session Control i Cloud App Security ](/cloud-app-security/proxy-intro-aad)
- [Anslut appar i Cloud App Security ](/cloud-app-security/enable-instant-visibility-protection-and-governance-actions-for-your-apps)