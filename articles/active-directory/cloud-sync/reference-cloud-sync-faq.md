---
title: Vanliga frågor och svar om Azure AD Connect Cloud Sync
description: Det här dokumentet beskriver vanliga frågor och svar om moln synkronisering.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 06/25/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 39d1554fd1b6cac1a90a794cfd93def97e494bfe
ms.sourcegitcommit: 8a74ab1beba4522367aef8cb39c92c1147d5ec13
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/20/2021
ms.locfileid: "98614021"
---
# <a name="azure-active-directory-connect-cloud-sync-faq"></a>Vanliga frågor och svar om Azure Active Directory Connect Cloud Sync

Läs om vanliga frågor och svar för Azure Active Directory (Azure AD) Connect-molnbaserad synkronisering.

## <a name="general-installation"></a>Allmän installation

**F: hur ofta körs Cloud Sync?**

Moln etablering är schemalagt att köras var 2: e minut. Var 2: e minut, kommer alla användare, grupp-och lösen ords-hash-ändringar att tillhandahållas till Azure AD.

**F: se synkroniseringsfel vid synkronisering av lösen ord vid första körningen. Varför?**

Detta är förväntat. Felen beror på att användarobjektet inte finns i Azure AD. När användaren har allokerats till Azure AD bör hash-värden för lösen ord tillhandahållas i den efterföljande körningen. Vänta på några körningar och bekräfta att det inte längre finns några fel i lösen ordets hash-synkronisering.

**F: Vad händer om Active Directory-instansen har attribut som inte stöds av molnbaserad synkronisering (till exempel katalog tillägg)?**

Moln etablering kommer att köras och etablera de attribut som stöds. Attribut som inte stöds är inte etablerade i Azure AD. Granska katalog tilläggen i Active Directory och se till att du inte behöver de attributen för att flöda till Azure AD. Om det krävs ett eller flera attribut kan du överväga att använda Azure AD Connect synkronisera eller flytta nödvändig information till ett av de attribut som stöds (t. ex. tilläggets attribut 1-15).

**F: Vad är skillnaden mellan Azure AD Connect synkronisering och moln synkronisering?**

Med Azure AD Connect Sync körs etableringen på den lokala Sync-servern. Konfigurationen lagras på den lokala Sync-servern. Med Azure AD Connect Cloud Sync lagras etablerings konfigurationen i molnet och körs i molnet som en del av Azure AD Provisioning-tjänsten. 

**F: kan jag använda molnbaserad synkronisering för att synkronisera från flera Active Directory skogar?**

Ja. Moln etablering kan användas för att synkronisera från flera Active Directory skogar. I miljöer med flera skogar måste alla referenser (t. ex. hanteraren) finnas i domänen.  

**F: Hur uppdateras agenten?**

Agenterna uppgraderas automatiskt av Microsoft. För IT-teamet minskar detta belastningen på att testa och validera nya agent versioner. 

**F: kan jag inaktivera automatisk uppgradering?**

Det finns inget stöd för att inaktivera automatisk uppgradering.

**F: kan jag ändra käll fäst punkten för molnbaserad synkronisering?**

Som standard använder Cloud Sync ms-DS-konsekvens-GUID med en återställning till ObjectGUID som käll ankare. Det finns inget stöd för att ändra käll ankare.

**F: Jag ser nya tjänst huvud namn med AD-domännamnen när de använder Cloud Sync. Förväntas det?**

Ja, Cloud Sync skapar ett tjänst huvud namn för etablerings konfigurationen med domän namnet som tjänstens huvud namn. Gör inga ändringar i tjänstens huvud konfiguration.

**F: Vad händer när en synkroniserad användare krävs för att ändra lösen ord vid nästa inloggning?**

Om lösen ordets hash-synkronisering är aktiverat i Cloud Sync och den synkroniserade användaren krävs för att ändra lösen ord vid nästa inloggning i den lokala AD-miljön, etablerar inte Cloud Sync "to-changed"-lösen ordet hash till Azure AD. När användaren har ändrat lösen ordet, tillhandahålls hashen för användarens lösen ord från AD till Azure AD.

**F: har Cloud Sync stöd för tillbakaskrivning av ms-DS-consistencyGUID för alla objekt?**

Nej, Cloud Sync stöder inte tillbakaskrivning av ms-DS-consistencyGUID för alla objekt (inklusive användar objekt). 

**F: Jag konfigurerar användare med hjälp av molnbaserad synkronisering. Jag har tagit bort konfigurationen. Varför ser jag fortfarande de gamla synkroniserade objekten i Azure AD?** 

När du tar bort konfigurationen tar Cloud Sync inte bort de synkroniserade objekten automatiskt i Azure AD. Se till att du inte har de gamla objekten genom att ändra omfånget för konfigurationen till en tom grupp eller organisationsenheter. När etableringen körs och rensar objekten inaktiverar du och tar bort konfigurationen. 

**F: Vad betyder det att Exchange hybrid inte stöds?**

Funktionen Exchange hybrid distribution möjliggör samtidig användning av Exchange-postlådor både lokalt och i Microsoft 365. Azure AD Connect synkroniserar en specifik uppsättning attribut från Azure AD tillbaka till din lokala katalog.  Moln Provisioning-agenten synkroniserar för närvarande inte de här attributen i din lokala katalog och stöds därför inte som en ersättning för Azure AD Connect.

**F: kan jag installera moln etablerings agenten på Windows Server Core?**

Nej, det finns inte stöd för att installera agenten på Server Core.

**F: kan jag använda en uppsamlings server med moln etablerings agenten?**

Nej, det finns inte stöd för mellanlagrings servrar.

**F: kan jag synkronisera gäst användar konton?**

Nej, det finns inte stöd för synkronisering av gäst användar konton.

**F: om jag flyttar en användare från en ORGANISATIONSENHET som är begränsad till en ORGANISATIONSENHET som är begränsad till Azure AD Connect, vad händer?**

Användaren kommer att tas bort och skapas på nytt.  Att flytta en användare från en ORGANISATIONSENHET som är begränsad till moln synkronisering visas som en borttagnings åtgärd.  Om användaren flyttas till en ORGANISATIONSENHET som hanteras av Azure AD Connect, etableras den om till Azure AD och en ny användare som skapats.

**F: om jag byter namn på eller flyttar ORGANISATIONSENHETen som ligger inom omfånget för Cloud Sync-filtret, vad händer med användaren som skapades i Azure AD?**

Ingenstans.  Användarna tas inte bort om ORGANISATIONSENHETen har bytt namn eller flyttats.

**F: stöder Azure AD Connect Cloud Sync stora grupper?**

Ja. Idag stöder vi upp till 50 000 grupp medlemmar som synkroniseras med hjälp av omfångs filtrering av ORGANISATIONSENHETer. När du använder grupp omfångs filtrering rekommenderar vi att du behåller grupp storleken till färre än 1500 medlemmar samtidigt. Orsaken till detta är att även om du kan synkronisera en stor grupp som en del av grupp omfångs filtret, kommer delta-synkroniseringen att Miss förväntas när du lägger till medlemmar i gruppen efter batchar som är större än 1500. 

## <a name="next-steps"></a>Nästa steg 

- [Vad är etablering?](what-is-provisioning.md)
- [Vad är Azure AD Connect Cloud Sync?](what-is-cloud-sync.md)
