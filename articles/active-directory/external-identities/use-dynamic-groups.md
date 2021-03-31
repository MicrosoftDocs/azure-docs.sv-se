---
title: Dynamiska grupper och B2B-samarbete – Azure Active Directory | Microsoft Docs
description: Visar hur du använder dynamiska Azure AD-grupper med Azure Active Directory B2B-samarbete
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 02/28/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5b820b8b9606795709d03414fa14ec29a1b5c519
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "92441563"
---
# <a name="dynamic-groups-and-azure-active-directory-b2b-collaboration"></a>Dynamiska grupper och Azure Active Directory B2B-samarbete

## <a name="what-are-dynamic-groups"></a>Vad är dynamiska grupper?
Dynamisk konfiguration av säkerhets grupp medlemskap för Azure Active Directory (Azure AD) finns i [Azure Portal](https://portal.azure.com). Administratörer kan ange regler för att fylla i grupper som skapas i Azure AD baserat på användarattribut (till exempel userType, avdelning eller land/region). Medlemmar kan läggas till eller tas bort automatiskt från en säkerhets grupp baserat på deras attribut. Dessa grupper kan ge åtkomst till program eller moln resurser (SharePoint-webbplatser, dokument) och tilldela licenser till medlemmar. Läs mer om dynamiska grupper i [dedikerade grupper i Azure Active Directory](../fundamentals/active-directory-groups-create-azure-portal.md).

Lämplig [Azure AD Premium P1-eller P2-licens](https://azure.microsoft.com/pricing/details/active-directory/) krävs för att skapa och använda dynamiska grupper. Läs mer i artikeln [skapa attribut-baserade regler för dynamiska grupp medlemskap i Azure Active Directory](../enterprise-users/groups-dynamic-membership.md).

## <a name="creating-an-all-users-dynamic-group"></a>Skapa en dynamisk grupp för alla användare
Du kan skapa en grupp som innehåller alla användare i en klient som använder en medlemskaps regel. När användare läggs till eller tas bort från klienten i framtiden, justeras gruppens medlemskap automatiskt.

1. Logga in på [Azure Portal](https://portal.azure.com) med ett konto som är tilldelat rollen global administratör eller användar administratör i klienten.
1. Välj **Azure Active Directory**.
2. Under **Hantera**, Välj **grupper** och välj sedan **ny grupp**.
1. Välj **säkerhet** under **grupptyp** på sidan **ny grupp** . Ange ett **gruppnamn** och en **gruppbeskrivning** för den nya gruppen. 
2. Under **medlemskaps typ** väljer du **dynamisk användare** och väljer sedan **Lägg till dynamisk fråga**. 
4. I text rutan **syntax för regel** väljer du **Redigera**. På sidan **Redigera regeltyp** skriver du följande uttryck i text rutan:

   ```
   user.objectId -ne null
   ```
1. Välj **OK**. Regeln visas i rutan syntax för regel:

   ![Regel-syntax för alla användares dynamiska grupp](media/use-dynamic-groups/all-user-rule-syntax.png)

1.  Välj **Spara**. Den nya dynamiska gruppen kommer nu att innehålla B2B-gäst användare samt medlems användare.


1. Skapa gruppen genom att välja **skapa** på sidan **ny grupp** .

## <a name="creating-a-group-of-members-only"></a>Endast skapa en grupp med medlemmar

Om du vill att din grupp ska undanta gäst användare och bara inkludera medlemmar i din klient organisation, skapar du en dynamisk grupp enligt beskrivningen ovan, men i rutan **syntax för regel** anger du följande uttryck:

```
(user.objectId -ne null) and (user.userType -eq "Member")
```

Följande bild visar regel syntaxen för en dynamisk grupp som har ändrats för att inkludera endast medlemmar och exkludera gäster.

![Visar regel där användar typen är lika med medlem](media/use-dynamic-groups/all-member-user-rule-syntax.png)

## <a name="creating-a-group-of-guests-only"></a>Skapa en grupp med endast gäster

Du kanske också är användbar att skapa en ny dynamisk grupp som bara innehåller gäst användare, så att du kan tillämpa principer (till exempel principer för villkorlig åtkomst i Azure AD). Skapa en dynamisk grupp enligt beskrivningen ovan, men ange följande uttryck i rutan **regeltyp** :

```
(user.objectId -ne null) and (user.userType -eq "Guest")
```

Följande bild visar regel syntaxen för en dynamisk grupp som har ändrats för att endast omfatta gäster och utesluta medlems användare.

![Visar regel där användar typen är lika med gäst](media/use-dynamic-groups/all-guest-user-rule-syntax.png)

## <a name="next-steps"></a>Nästa steg

- [Användaregenskaper för B2B-samarbete](user-properties.md)
- [Lägga till en B2B-samarbets användare till en roll](add-guest-to-role.md)
- [Villkorlig åtkomst för B2B-samarbets användare](conditional-access.md)