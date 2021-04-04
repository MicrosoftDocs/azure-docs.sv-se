---
title: Vad är etablering med Azure Active Directory? | Microsoft Docs
description: Beskriver översikt över identitets etablering och ILM-scenarier.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: overview
ms.date: 10/30/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 640367d1b833f61e8a83fe9ce6b14d6d799cf9b9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96172460"
---
# <a name="what-is-provisioning"></a>Vad är etablering?

Etablering och avetablering är de processer som garanterar konsekvens för digitala identiteter i flera system.  De här processerna används vanligt vis som en del av [hantering av identitets livs cykeln](what-is-identity-lifecycle-management.md).

**Etablering** är en process för att skapa en identitet i ett mål system baserat på vissa villkor.  **Inetablering** är en process där identiteten tas bort från mål systemet, om villkoren inte längre uppfylls. **Synkronisering** är en process där det etablerade objektet hålls uppdaterat, så att källobjektet och målobjektet liknar varandra.

Till exempel när en ny medarbetare ansluter till din organisation, anges den anställda i HR-systemet.  Vid detta tillfälle kan etableringen **från** HR **till** Azure Active Directory (Azure AD) skapa ett motsvarande användar konto i Azure AD. Program som frågar Azure AD kan se kontot för den nya medarbetaren.  Om det finns program som inte använder Azure AD, kan etableringen **från** Azure AD **till** dessa program-databaser Se till att användaren kommer åt alla program som användaren behöver åtkomst till.  Med den här processen kan användaren starta arbetet och ha åtkomst till de program och system som de behöver på dag ett.  På samma sätt, när deras egenskaper, t. ex. avdelnings-eller anställnings status, ändras i HR-systemet, synkronisering av dessa uppdateringar från HR-systemet till Azure AD och även för andra program och mål databaser, garanterar konsekvens.

Azure AD tillhandahåller för närvarande tre områden med automatisk etablering.  De är:  

- Etablering från ett externt icke-katalogs auktoritärt system för post till Azure AD via **[HR-driven etablering](#hr-driven-provisioning)**  
- Etablering från Azure AD till program via **[app-etablering](#app-provisioning)**  
- Etablering mellan Azure AD och Active Directory Domain Services, via **[etablering mellan kataloger](#inter-directory-provisioning)** 

![hantering av identitets livs cykel](media/what-is-provisioning/provisioning.png)

## <a name="hr-driven-provisioning"></a>HR-driven etablering

![HR-etablering](media/what-is-provisioning/cloud-2a.png)

Etableringen från Azure till Azure AD innebär att objekt skapas, vanligt vis användar identiteter som representerar varje anställd, men i vissa fall andra objekt som representerar avdelningar eller andra strukturer, baserat på den information som finns i ditt HR-system.  

Det vanligaste scenariot är att när en ny medarbetare ansluts till företaget registreras de i HR-systemet.  En gång som inträffar är de automatiskt etablerade som en ny användare i Azure AD, utan administrativt engagemang för varje ny anställning.  I allmänhet kan etableringen från HR avse följande scenarier.

- **Anställning av nya anställda** – när en ny medarbetare läggs till i ett HR-system skapas ett användar konto automatiskt i Active Directory, Azure AD och alternativt i katalogerna för andra program som stöds av Azure AD, med Skriv tillbaka till e-postadressen till HR-systemet.
- **Uppdateringar av anställda och profiler** – när en medarbetar post uppdateras i det HR-systemet (till exempel namn, titel eller chef) uppdateras deras användar konto automatiskt i Active Directory, Azure AD och eventuellt andra program som stöds av Azure AD.
- **Anställdas uppsägningar** – när en medarbetare avslutas i HR blockeras användar kontot automatiskt från att logga in eller tas bort i Active Directory, Azure AD och i andra program.
- **Anställdas återställningar** – när en medarbetare återställs i molnet kan deras gamla konto automatiskt återaktiveras eller etableras på nytt (beroende på din preferens).

Det finns tre distributions alternativ för HR-driven etablering med Azure AD:

1. För organisationer med en enda prenumeration på Workday eller SuccessFactors, och Använd inte Active Directory
1. För organisationer med en enda prenumeration på Workday eller SuccessFactors, och har både Active Directory och Azure AD
1. För organisationer med flera HR-system eller ett lokalt HR-system som SAP, Oracle eBusiness eller den andra

Mer information finns i [Vad är HR driven etablering?](what-is-hr-driven-provisioning.md)

## <a name="app-provisioning"></a>App-etablering

![app-etablering](media/what-is-provisioning/cloud-3b.png)

I Azure AD innebär termen **[app-etablering](../app-provisioning/user-provisioning.md)** att automatiskt skapa kopior av användar identiteter i de program som användarna behöver åtkomst till, för program som har sitt eget data lager, som skiljer sig från Azure AD eller Active Directory. Förutom att skapa användar identiteter innehåller app-etablering underhåll och borttagning av användar identiteter från dessa appar, när användarens status eller roller ändras. Vanliga scenarier innefattar etablering av en Azure AD-användare i program som [Dropbox](../saas-apps/dropboxforbusiness-provisioning-tutorial.md), [Salesforce](../saas-apps/salesforce-provisioning-tutorial.md), [ServiceNow](../saas-apps/servicenow-provisioning-tutorial.md), eftersom var och en av dessa program har sin egen användar databas som skiljer sig från Azure AD.

Mer information finns i [Vad är app-etablering?](what-is-app-provisioning.md)

## <a name="inter-directory-provisioning"></a>Etablering mellan kataloger

![etablering mellan kataloger](media/what-is-provisioning/cloud-4a.png)

Många organisationer förlitar sig på både Active Directory och Azure AD och kan ha program anslutna till Active Directory, till exempel lokala fil servrar.

Eftersom många organisationer tidigare har distribuerat HR-drivna etablering lokalt kan de redan ha användar identiteter för alla anställda i Active Directory.   Det vanligaste scenariot för etablering mellan kataloger är när en användare som redan finns i Active Directory har tillhandahållits till Azure AD.  Den här etableringen utförs vanligt vis genom Azure AD Connect synkronisering eller Azure AD Connect moln etablering. 

Dessutom kan organisationer vilja även etablera till lokala system från Azure AD.  En organisation kan till exempel ha fått gäster till Azure AD-katalogen, men dessa gäster behöver åtkomst till lokala Windows-baserade autentiserings-baserade (WIA) webb program via App proxy.  Detta kräver etablering av lokala AD-konton för dessa användare i Azure AD.

Mer information finns i [Vad är etablering mellan kataloger?](what-is-inter-directory-provisioning.md)

 
## <a name="next-steps"></a>Nästa steg 
- [Vad är livscykelhantering för identiteter?](what-is-identity-lifecycle-management.md)
- [Vad är HR driven etablering?](what-is-hr-driven-provisioning.md)
- [Vad är appetablering?](what-is-app-provisioning.md)
- [Vad är etablering mellan kataloger?](what-is-inter-directory-provisioning.md)