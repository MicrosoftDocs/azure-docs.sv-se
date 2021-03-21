---
title: Fel sökning och vanliga frågor och svar om administrativa enheter – Azure Active Directory | Microsoft Docs
description: Undersök administrativa enheter för att bevilja behörigheter med begränsad omfattning i Azure Active Directory.
services: active-directory
documentationcenter: ''
author: rolyon
manager: daveba
ms.service: active-directory
ms.topic: how-to
ms.subservice: roles
ms.workload: identity
ms.date: 11/04/2020
ms.author: rolyon
ms.reviewer: anandy
ms.custom: oldportal;it-pro;
ms.collection: M365-identity-device-management
ms.openlocfilehash: c1f41dca3b52ce75ba2342506f621cca0618a3bf
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102565894"
---
# <a name="azure-ad-administrative-units-troubleshooting-and-faq"></a>Azure AD-administrativa enheter: fel sökning och vanliga frågor och svar

För mer detaljerad administrativ kontroll i Azure Active Directory (Azure AD) kan du tilldela användare till en Azure AD-roll med en omfattning som är begränsad till en eller flera administrativa enheter. Exempel på PowerShell-skript för vanliga aktiviteter finns i [arbeta med administrativa enheter](/powershell/azure/active-directory/working-with-administrative-units).

## <a name="frequently-asked-questions"></a>Vanliga frågor och svar

**F: Varför kan jag inte skapa en administrativ enhet?**

**A:** Endast en *Global administratör* eller en *privilegie rad roll administratör* kan skapa en administrativ enhet i Azure AD. Kontrol lera att den användare som försöker skapa den administrativa enheten tilldelas antingen rollen *Global administratör* eller *administratör för privilegie rad roll* .

**F: Jag har lagt till en grupp i en administrativ enhet. Varför visas inte grupp medlemmarna?**

**A:** När du lägger till en grupp i en administrativ enhet innebär det inte att alla grupp medlemmar läggs till i den. Användarna måste tilldelas direkt till den administrativa enheten.

**F: Jag har precis lagt till (eller tagit bort) en medlem i den administrativa enheten. Varför visas inte medlemmen (eller fortfarande visas) i användar gränssnittet?**

**A:** Ibland kan tillägg eller borttagning av en eller flera medlemmar i en administrativ enhet ta några minuter innan de visas i fönstret **administrativa enheter** . Alternativt kan du gå direkt till den associerade resursens egenskaper och se om åtgärden har slutförts. Mer information om användare och grupper i administrativa enheter finns i [Visa en lista över administrativa enheter för en användare](admin-units-add-manage-users.md) och [Visa en lista över administrativa enheter för en grupp](admin-units-add-manage-groups.md).

**F: Jag är en delegerad lösen ords administratör för en administrativ enhet. Varför kan jag inte återställa en speciell användares lösen ord?**

**A:** Som administratör för en administrativ enhet kan du bara återställa lösen ord för användare som har tilldelats din administrativa enhet. Se till att användaren vars lösen ords återställning inte fungerar tillhör den administrativa enhet som du har tilldelats. Om användaren tillhör samma administrativa enhet men du fortfarande inte kan återställa användarens lösen ord, kontrollerar du de roller som är tilldelade till användaren. 

En administrativ enhets begränsad administratör kan inte återställa lösen ordet för en användare som har tilldelats en roll med en företagsomfattande omfattning för att förhindra en behörighets höjning.

**F: Varför krävs administrativa enheter? Gick det inte att använda säkerhets grupper som ett sätt att definiera ett omfång?**

**A:** Säkerhets grupper har ett befintligt syfte och en befintlig auktoriserings modell. En *användar administratör* kan till exempel hantera medlemskap i alla säkerhets grupper i Azure AD-organisationen. Rollen kan använda grupper för att hantera åtkomst till program som Salesforce. En *användar administratör* bör inte kunna hantera Delegerings modellen själva, vilket skulle innebära att säkerhets grupper har utökats till stöd för "resurs gruppering"-scenarier. 

Administrativa enheter, t. ex. organisationsenheter i Windows Server Active Directory, är avsedda att ge ett sätt att administrera en mängd olika katalog objekt. Själva säkerhets grupperna kan vara medlemmar i resurs omfång. Att använda säkerhets grupper för att definiera en uppsättning säkerhets grupper som en administratör kan hantera kan bli förvirrande.

**F: Vad betyder det att lägga till en grupp i en administrativ enhet?**

**A:** Om du lägger till en grupp i en administrativ enhet får du själva gruppen i hanterings omfånget för alla *användar administratörer* som också har begränsad till den administrativa enheten. Användar administratörer för den administrativa enheten kan hantera namnet och medlemskapet i själva gruppen. Den ger inte *användaren administratörs* behörighet att hantera användare av gruppen (till exempel för att återställa sina lösen ord). För att ge *användar administratören* möjlighet att hantera användare måste användarna vara direkt medlemmar i den administrativa enheten.

**F: kan en resurs (användare eller grupp) vara medlem i fler än en administrativ enhet?**

**A:** Ja, en resurs kan vara medlem i mer än en administrativ enhet. Resursen kan hanteras av alla organisations omfattande och administrativa enhets administratörer som har behörigheter över resursen.

**F: är administrativa enheter tillgängliga i B2C-organisationer?**

**A:** Nej, administrativa enheter är inte tillgängliga för B2C-organisationer.

**F: är kapslade administrativa enheter som stöds?**

**A:** Nej, kapslade administrativa enheter stöds inte.

**F: är administrativa enheter som stöds i PowerShell och Graph API?**

**S:** Ja. Du hittar stöd för administrativa enheter i [PowerShell-cmdlet-dokumentation](/powershell/module/Azuread/) och [exempel skript](/powershell/azure/active-directory/working-with-administrative-units).

Hitta stöd för [resurs typen administrativeUnit](/graph/api/resources/administrativeunit) i Microsoft Graph.

## <a name="next-steps"></a>Nästa steg

- [Begränsa omfång för roller med hjälp av administrativa enheter](administrative-units.md)
- [Hantera administrativa enheter](admin-units-manage.md)
