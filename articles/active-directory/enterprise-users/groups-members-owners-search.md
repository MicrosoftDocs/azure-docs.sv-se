---
title: Sök och filtrera grupper medlemmar och ägare (för hands version) – Azure Active Directory | Microsoft Docs
description: Sök och filtrera grupp medlemmar och ägare i Azure Portal.
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
ms.service: active-directory
ms.subservice: enterprise-users
ms.workload: identity
ms.topic: how-to
ms.date: 12/02/2020
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: d78a2a4e0f14d99a7a1ecada915857f59422bb58
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96547380"
---
# <a name="search-groups-and-members-preview-in-azure-active-directory"></a>Sök efter grupper och medlemmar (förhands granskning) i Azure Active Directory

Den här artikeln beskriver hur du söker efter medlemmar och ägare av en grupp och hur du använder Sök filter som en del av för hands versionen av grupper i Azure Active Directory (Azure AD)-portalen. Det finns många förbättringar i gruppen med grupper som hjälper dig att hantera dina grupper, inklusive medlemmar och ägare, snabbt och enkelt. Mer information om för hands versionerna finns i kompletterande användnings [villkor för Microsoft Azure för hands](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)versionerna.

Ändringarna i den här förhands granskningen innehåller:

- Nya grupper Sök funktioner, till exempel under Strängs sökning i grupp namn
- Nya filtrerings-och sorterings alternativ för medlems-och ägar listor
- Nya Sök funktioner för medlems-och ägar listor
- Mer exakta grupp antal för stora grupper

## <a name="enabling-and-managing-the-preview"></a>Aktivera och hantera för hands versionen

Vi har gjort det enkelt att delta i för hands versionen:

  1. Logga in på [Azure AD-portalen](https://portal.azure.com)och välj **grupper**.
  2. Från sidan grupper – alla grupper väljer du banderollen längst upp på sidan för att gå med i förhands granskningen.

Du kan också ta en titt på de senaste funktionerna och förbättringarna genom att välja **förhands gransknings information** på sidan **alla grupper** . När du har gått med i förhands granskningen visas en förhands gransknings tagg på alla grupper som har förbättringar och ingår i förhands granskningen. Sidan alla grupper har inte uppdaterats som en del av den här för hands versionen.

Om du har problem kan du växla tillbaka till den äldre upplevelsen genom att välja banderollen högst upp på sidan **alla grupper** . Vi uppskattar din feedback så att vi kan förbättra vår upplevelse.

## <a name="group-search-and-sorting"></a>Grupps ökning och sortering

Sök efter grupp listan har förbättrats så att när du kan ange en Sök sträng utför sökningen automatiskt en och en `startswith` under Strängs sökning i listan över grupp namn. Under Strängs sökningen utförs endast på hela ord och innehåller inte specialtecken. Under Strängs sökningen är Skift läges känslig.

![ny under Strängs sökning på sidan alla grupper](./media/groups-members-owners-search/groups-search-preview.png)

En sökning efter "princip" kommer nu att returnera både "MDM-princip – väst" och "princip grupp". Det går inte att returnera en grupp med namnet "New_policy".

- Du kan också utföra samma sökning på grupp medlemskaps listor.
- Nu kan du sortera listan grupper efter namn med hjälp av pilarna till höger om kolumn rubriken namn för att sortera listan i stigande eller fallande ordning.

## <a name="group-member-search-and-filtering"></a>Sökning och filtrering av grupp medlemmar

### <a name="search-group-member-and-owner-lists"></a>Sök grupps medlem och ägar listor

Nu kan du söka efter medlemmar i en speciell grupp efter namn och utföra samma sökning i listan över gruppens ägare. I den nya upplevelsen kommer en StartsWith-sökning att utföras om du anger en sträng i sökrutan. Till exempel kommer en sökning efter "Glenn" att returnera Scott Wilkinson.

![ny under Strängs sökning i listorna grupp medlemmar och ägare](./media/groups-members-owners-search/members-list.png)

### <a name="filter-member-and-owners-list"></a>Filtrera medlem och ägare-lista

Förutom sökning kan du nu filtrera medlems-och ägar listor efter användar typ. Detta är den information som finns i kolumnen användar typ i listan. Så du kan filtrera listan efter medlemmar och gäster för att fastställa om det finns några gäster i gruppen.

### <a name="view-and-manage-membership"></a>Visa och hantera medlemskap

Förutom att visa de direkta medlemmarna i en speciell grupp kan du nu visa en lista över alla medlemmar i gruppen på sidan medlemmar. Listan medlemmar innehåller alla unika medlemmar i gruppen inklusive eventuella transitiva medlemmar.

Du kan också söka efter och filtrera listan direkt medlemmar och listan Alla medlemmar individuellt. Filtrering av listan Alla medlemmar påverkar inte de filter som tillämpas på listan direkt medlemmar.

## <a name="improved-group-member-counts"></a>Förbättrade grupp medlems antal

Vi har förbättrat grupp **översikts** sidan för att ge grupp medlemmar antal för grupper av alla storlekar. Du kan se medlems antalet även för grupper med fler än 1 000 medlemmar. Nu kan du se det totala antalet direkta medlemmar för en grupp och det totala antalet medlemskap (alla unika medlemmar i gruppen inklusive transitiva medlemmar) på **översikts** sidan.

![Högre precision i antal grupp medlemskap](./media/groups-members-owners-search/member-numbers.png)

## <a name="next-steps"></a>Nästa steg

De här artiklarna innehåller ytterligare information om hur du arbetar med grupper i Azure AD.

- [Visa dina grupper och medlemmar](../fundamentals/active-directory-groups-view-azure-portal.md)
- [Hantera gruppmedlemskap](../fundamentals/active-directory-groups-membership-azure-portal.md)
- [Hantera dynamiska regler för användare i en grupp](groups-create-rule.md)
- [Redigera dina gruppinställningar](../fundamentals/active-directory-groups-settings-azure-portal.md)
- [Hantera åtkomst till resurser med grupper](../fundamentals/active-directory-manage-groups.md)
- [Hantera åtkomst till SaaS-appar med grupper](groups-saasapps.md)
- [Hantera grupper med PowerShell-kommandon](../enterprise-users/groups-settings-v2-cmdlets.md)
- [Lägg till en Azure-prenumeration i Azure Active Directory](../fundamentals/active-directory-how-subscriptions-associated-directory.md)
