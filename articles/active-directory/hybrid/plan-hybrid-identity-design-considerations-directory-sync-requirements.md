---
title: Hybrid Identity design – krav för katalog synkronisering Azure | Microsoft Docs
description: Identifiera vilka krav som krävs för att synkronisera alla användare mellan på = lokalt och i molnet för företaget.
documentationcenter: ''
services: active-directory
author: billmath
manager: daveba
editor: ''
ms.assetid: 593eaa71-17eb-4c16-8c98-43cc62987e65
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/18/2017
ms.subservice: hybrid
ms.author: billmath
ms.custom: seohack1
ms.collection: M365-identity-device-management
ms.openlocfilehash: 500d226fcb60646becc49144f206dcb0dee49bd8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "89278402"
---
# <a name="determine-directory-synchronization-requirements"></a>Fastställa krav för katalog synkronisering
Synkronisering innebär att du kan ge användarna en identitet i molnet baserat på deras lokala identitet. Oavsett om de kommer att använda ett synkroniserat konto för autentisering eller federerad autentisering måste användarna fortfarande ha en identitet i molnet.  Den här identiteten måste upprätthållas och uppdateras regelbundet.  Uppdateringarna kan ta många formulär, från rubriken ändringar till lösen ords ändringar.  

Börja med att utvärdera organisationens lokala identitets lösning och användar krav. Den här utvärderingen är viktig för att definiera de tekniska kraven för hur användar identiteter ska skapas och hanteras i molnet.  För en majoritet av organisationer är Active Directory lokalt och är den lokala katalog som användarna kommer att synkronisera från, men i vissa fall är detta inte fallet.  

Se till att besvara följande frågor:

* Har du en AD-skog, flera eller inga?
  
  * Hur många Azure AD-kataloger kommer du att synkronisera till?
    
    1. Använder du filtrering?
    2. Har flera Azure AD Connects servrar planer ATS?
* Har du för närvarande ett synkroniseringsschema lokalt?
  
  * Om ja, kan användarna om de har en virtuell katalog/integrering av identiteter?
* Har du andra lokala kataloger som du vill synkronisera (t. ex. LDAP-katalog, HR-databas osv.)?
  * Kommer du att göra alla GALSync?
  * Vad är det aktuella läget för UPN i din organisation? 
  * Har du en annan katalog som användare autentiserar mot?
  * Använder företaget Microsoft Exchange?
    * Planerar de att ha en hybrid Exchange-distribution?

Nu när du har en uppfattning om dina krav för synkronisering måste du bestämma vilket verktyg som är rätt för att uppfylla dessa krav.  Microsoft tillhandahåller flera verktyg för att utföra katalog integrering och synkronisering.  Mer information finns i [jämförelse tabellen för katalog integrerings verktyg för Hybrid identitet](plan-hybrid-identity-design-considerations-tools-comparison.md) . 

Nu när du har dina krav för synkronisering och verktyget som ska utföra detta för ditt företag, måste du utvärdera de program som använder dessa katalog tjänster. Den här utvärderingen är viktig för att definiera de tekniska kraven för att integrera dessa program i molnet. Se till att besvara följande frågor:

* Kommer dessa program att flyttas till molnet och använda katalogen?
* Finns det särskilda attribut som behöver synkroniseras till molnet så att dessa program kan använda dem korrekt?
* Kommer dessa program att behöva skrivas om för att dra nytta av molnbaserad autentisering?
* Kommer de här programmen fortsätta att vara aktiva lokalt medan användare kommer åt dem med hjälp av moln identiteten?

Du måste också bestämma katalog synkroniseringen säkerhets krav och begränsningar. Den här utvärderingen är viktig för att få en lista över de krav som krävs för att skapa och underhålla användarens identiteter i molnet. Se till att besvara följande frågor:

* Var finns synkroniseringstjänsten?
* Kommer den att vara domänansluten?
* Kommer servern att finnas i ett begränsat nätverk bakom en brand vägg, till exempel en DMZ?
  * Kommer du att kunna öppna de brand Väggs portar som krävs för att stödja synkronisering?
* Har du en katastrof återställnings plan för synkroniseringstjänsten?
* Har du ett konto med rätt behörigheter för alla skogar som du vill synkronisera med?
  * Om ditt företag inte vet svaret på den här frågan kan du läsa avsnittet "behörigheter för Lösenordssynkronisering" i artikeln installera tjänsten [Azure Active Directory Sync](/previous-versions/azure/azure-services/dn757602(v=azure.100)#BKMK_CreateAnADAccountForTheSyncService) och ta reda på om du redan har ett konto med dessa behörigheter eller om du behöver skapa ett.
* Om du har multifaktorautentisering kan Sync-servern hämta till varje skog?

> [!NOTE]
> Skriv ner varje svar och försök förstå anledningen till svaret. [Fastställ kraven på incident svar](plan-hybrid-identity-design-considerations-incident-response-requirements.md) över tillgängliga alternativ. Genom att ha besvarat dessa frågor väljer du det alternativ som passar dina affärs behov bäst.
> 
> 

## <a name="next-steps"></a>Nästa steg
[Fastställa krav för Multi-Factor Authentication](plan-hybrid-identity-design-considerations-multifactor-auth-requirements.md)

## <a name="see-also"></a>Se även
[Översikt över design överväganden](plan-hybrid-identity-design-considerations-overview.md)