---
title: Vad är hantering av identitets livs cykel med Azure Active Directory? | Microsoft Docs
description: Beskriver översikt över hantering av identitets livs cykeln.
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
ms.openlocfilehash: 65e1217041d85b66664792d9475cdfcb517559b9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96172443"
---
# <a name="what-is-identity-lifecycle-management"></a>Vad är livscykelhantering för identiteter?

Identitets styrning hjälper organisationer att få en balans mellan produktiviteten – hur snabbt kan en person ha åtkomst till de resurser som de behöver, till exempel när de ansluter till min organisation? Och säkerhet – hur ska deras åtkomst ändras över tid, till exempel på grund av ändringar i personens anställnings status?

**Hantering av identitets livs cykeln** är grunden för identitets styrning, och effektiva styrningar i skalan kräver att infrastrukturen för identitets livs cykels hantering används för program. Hantering av identitets livs cykel syftar till att automatisera och hantera hela livs cykeln för digitala identiteter. 

![moln etablering](media/what-is-provisioning/cloud-1.png)

## <a name="what-is-a-digital-identity"></a>Vad är en digital identitet?

En digital identitet är information om en entitet som används av en eller flera data behandlings resurser, t. ex. operativ system eller program. Dessa entiteter kan representera personer, organisationer, program eller enheter.  Identiteten beskrivs vanligt vis av de attribut som är associerade med den, till exempel namn, identifierare, och egenskaper som roller som används för åtkomst hantering.  De här attributen hjälper system att avgöra vilka som har åtkomst till vad och vem som får använda det här systemet eller det systemet.  

## <a name="managing-the-lifecycle-of-digital-identities"></a>Hantera livs cykeln för digitala identiteter

Hantering av digitala identiteter är en komplex uppgift, särskilt när det gäller att korrelera verkliga objekt, till exempel en person och deras relation med en organisation som anställd i organisationen, med en digital representation.    I små organisationer kan en IT-specialist skapa ett konto för dem i en katalog och tilldela dem den åtkomst som krävs för att hålla den digitala representationen av personer som kräver en identitet en manuell process.  I medel stora och stora organisationer kan dock automatisering göra det möjligt för organisationen att skala mer effektivt och hålla identiteterna korrekt.

Den typiska processen för att skapa identitets livs cykel hantering i en organisation följer dessa steg:

1. Ta reda på om det redan finns system med post: data källor som organisationen hanterar som auktoritativa.  Organisationen kan till exempel ha en arbets dag i HR-systemet och det systemet är auktoritativt för att tillhandahålla den aktuella listan över anställda och vissa av deras egenskaper, till exempel medarbetarens namn eller avdelning.  Eller ett e-postsystem, till exempel Exchange Online, kan vara auktoritativt för en medarbetares e-postadress.

2. Anslut de system som är registrerade med en eller flera kataloger och databaser som används av program, och Lös eventuella inkonsekvenser mellan kataloger och post system. En katalog kan till exempel ha föråldrade data, till exempel ett konto för en tidigare anställd, som inte längre behövs. 

3. Bestäm vilka processer som kan användas för att tillhandahålla auktoritativ information i frånvaro av ett post system.  Om det till exempel finns digitala identiteter, men om organisationen inte har någon databas för besökare, kan det vara nödvändigt att hitta ett alternativt sätt att fastställa när en digital identitet för en besökare inte längre behövs.

4. Konfigurera att ändringar från systemet för poster eller andra processer replikeras till var och en av de kataloger eller databaser som kräver en uppdatering.

## <a name="identity-lifecycle-management-for-representing-employees-and-other-individuals-with-an-organizational-relationship"></a>Hantering av identitets livs cykel för att representera anställda och andra personer med en organisations relation

När du planerar livs cykel hantering av identitet för anställda eller andra personer med en organisations relation, till exempel en entreprenör eller student, kan många organisationer ta processen "delta, flytta och lämna".  Dessa är:
    
   - Delta – när en individ kommer till omfånget för att få åtkomst krävs en identitet av dessa program, så en ny digital identitet kan behöva skapas om en sådan inte redan är tillgänglig
   - Flytta – när en enskild person flyttas mellan gränser, som kräver att ytterligare åtkomst-auktoriseringar läggs till eller tas bort i sin digitala identitet
   - Lämna – när en enskild person lämnar omfånget som behöver åtkomst kan du behöva ta bort åtkomsten och därefter kan identiteten inte längre krävas av andra program än för gransknings-eller data utredning

Om till exempel en ny medarbetare ansluter till din organisation, som aldrig har varit kopplad till din organisation tidigare, kräver den anställda en ny digital identitet som visas som ett användar konto i Azure AD.  Skapandet av det här kontot skulle hamna i en "anslutning"-process som kan automatiseras om det fanns ett post system, till exempel en arbets dag som kan tyda på att den nya medarbetaren börjar fungera.  Senare, om din organisation har en anställd som flyttar från t. ex. försäljning till marknadsföring, skulle de kunna bli en process som "flytta".  Detta skulle kräva att du tar bort åtkomst rättigheterna de hade i den försäljnings organisation som de inte längre kräver, och beviljar dem rättigheter i marknadsförings organisationen som de nya kräver.

## <a name="identity-lifecycle-management-for-guests"></a>Hantering av identitets livs cykel för gäster

Liknande processer behövs också för gäster och andra användare.  Hantering av Azure AD-rättigheterna använder Azure AD Business-to-Business (B2B) för att tillhandahålla de livs cykel kontroller som krävs för att samar beta med personer utanför organisationen som behöver åtkomst till organisationens resurser. Med Azure AD B2B autentiserar externa användare till sin arbets katalog, men har en representation i din katalog. Representationen i din katalog gör det möjligt för användaren att tilldelas åtkomst till dina resurser.  Hantering av rättighets hantering gör det möjligt för personer utanför organisationen att begära åtkomst, skapa en digital identitet för dem efter behov. Dessa digitala identiteter tas bort automatiskt när användaren förlorar åtkomst.  

## <a name="how-does-azure-ad-automate-identity-lifecycle-management"></a>Hur automatiserar Azure AD identitets livs cykel hantering?

Azure AD tillhandahåller för närvarande följande funktioner:

* Användare som representerar medarbetare kan automatiskt skapas och uppdateras i Azure AD och Active Directory med hjälp av [HR-driven etablering](what-is-hr-driven-provisioning.md)
* Användare som redan finns i Active Directory kan skapas och hanteras automatiskt i Azure AD med hjälp av etablering i olika [kataloger](what-is-inter-directory-provisioning.md)
* Användare kan tilldelas automatiskt till grupper baserat på deras egenskaper, med hjälp av [dynamiska grupper](../external-identities/use-dynamic-groups.md#what-are-dynamic-groups) och kan på begäran tilldelas grupper, team, Azure AD-roller, Azure-resurs roller och SharePoint Online-webbplatser med hjälp av [hantering av rättigheter](entitlement-management-scenarios.md) och [Privileged Identity Management](../privileged-identity-management/pim-configure.md)
* Uppdateringar till användare kan skickas automatiskt till fler program med hjälp av [app-etablering](what-is-app-provisioning.md)

## <a name="next-steps"></a>Nästa steg 

- [Vad är etablering?](what-is-provisioning.md)
- [Styra åtkomsten för externa användare i hantering av Azure AD-rättigheter](./entitlement-management-external-users.md)
- [Vad är HR driven etablering?](what-is-hr-driven-provisioning.md)
- [Vad är appetablering?](what-is-app-provisioning.md)
- [Vad är etablering mellan kataloger?](what-is-inter-directory-provisioning.md)