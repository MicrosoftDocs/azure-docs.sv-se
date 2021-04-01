---
title: Ändra licens planer för användare och grupper – Azure AD | Microsoft Docs
description: Så här migrerar du användare i en grupp till olika tjänst planer med hjälp av grupp licensiering i Azure Active Directory
services: active-directory
keywords: Azure AD-licensiering
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: enterprise-users
ms.topic: how-to
ms.workload: identity
ms.date: 12/02/2020
ms.author: curtand
ms.reviewer: sumitp
ms.custom: it-pro;seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 050ae95c79e7ecb98f8508c2fdb41b90fc1b1da0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96546547"
---
# <a name="change-license-assignments-for-a-user-or-group-in-azure-active-directory"></a>Ändra licens tilldelningar för en användare eller grupp i Azure Active Directory

Den här artikeln beskriver hur du flyttar användare och grupper mellan tjänste licens planer i Azure Active Directory (Azure AD). Målet för Azure AD är att se till att det inte går att förlora tjänster eller data under licens ändringen. Användarna bör växla mellan tjänsterna sömlöst. I tilldelnings stegen för licens planen i den här artikeln beskrivs hur du ändrar en användare eller grupp i Office 365 E1 till Office 365 E3, men stegen gäller alla licens planer. När du uppdaterar licens tilldelningar för en användare eller grupp görs licens tilldelningen och nya tilldelningar samtidigt så att användarna inte förlorar åtkomsten till deras tjänster vid licens ändringar eller se licens konflikter mellan planer.

## <a name="before-you-begin"></a>Innan du börjar

Innan du uppdaterar licens tilldelningarna är det viktigt att kontrol lera att vissa antaganden är uppfyllda för alla användare eller grupper som ska uppdateras. Om antagandena inte är sanna för alla användare i en grupp, kan migreringen Miss par. Därför kan vissa användare förlora åtkomst till tjänster eller data. Se till att:

- Användare har den aktuella licens planen (i det här fallet Office 365 E1) som har tilldelats en grupp och ärvts av användaren och inte tilldelas direkt.

- Du har tillräckligt många tillgängliga licenser för den licens plan som du tilldelar. Om du inte har tillräckligt med licenser kan vissa användare inte tilldelas den nya licens planen. Du kan kontrol lera antalet tillgängliga licenser.

- Användare har inte andra tilldelade tjänst licenser som kan vara i konflikt med önskad licens eller förhindra borttagning av den aktuella licensen. Till exempel en licens från en tjänst som till exempel arbets plats analys eller Project Online som har ett beroende av andra tjänster.

- Om du hanterar grupper lokalt och synkroniserar dem till Azure AD via Azure AD Connect, lägger du till eller tar bort användare med hjälp av ditt lokala system. Det kan ta lite tid innan ändringarna har synkroniserats med Azure AD som ska hämtas av grupp licensiering.

- Om du använder Azure ADs dynamiska grupp medlemskap lägger du till eller tar bort användare genom att ändra deras attribut, men uppdaterings processen för licens tilldelningar förblir densamma.

## <a name="change-user-license-assignments"></a>Ändra användar licens tilldelningar

Om du ser att vissa kryss rutor inte är tillgängliga på sidan **Uppdatera licens tilldelningar** anger du tjänster som inte kan ändras eftersom de ärvs från en grupp licens.

1. Logga in på [Azure Portal](https://portal.azure.com/) med ett licens administratörs konto i din Azure AD-organisation.
1. Välj **Azure Active Directory**  >  **användare** och öppna sedan **profil** sidan för en användare.
1. Välj **licenser**.
1. Välj **tilldelningar** för att redigera licens tilldelning för användaren eller gruppen. På sidan **tilldelningar** kan du lösa licens tilldelnings konflikter.
1. Markera kryss rutan för Office 365 E3 och se till att åtminstone alla de E1-tjänster som är tilldelade till användaren är markerade.
1. Avmarkera kryss rutan för Office 365 E1.

    ![Sidan licens tilldelningar för en användare som visar Office 365 E1 rensad och Office 365 E3 valt](./media/licensing-groups-change-licenses/update-user-license-assignments.png)

1. Välj **Spara**.

Azure AD tillämpar de nya licenserna och tar bort de gamla licenserna samtidigt för att tillhandahålla tjänste kontinuitet.

## <a name="change-group-license-assignments"></a>Ändra grupp licens tilldelningar

1. Logga in på [Azure Portal](https://portal.azure.com/) med ett licens administratörs konto i din Azure AD-organisation.
1. Välj **Azure Active Directory**  >  **grupper** och öppna sedan **översikts** sidan för en grupp.
1. Välj **licenser**.
1. Välj kommandot **tilldelningar** för att redigera licens tilldelningen för användaren eller gruppen.
1. Markera kryss rutan för Office 365 E3. Se till att du väljer alla E1-tjänster som redan har tilldelats användaren för att upprätthålla kontinuitet för tjänsten.
1. Avmarkera kryss rutan för Office 365 E1.

    ![Välj kommandot tilldelningar på sidan licenser för användare eller grupp](./media/licensing-groups-change-licenses/update-group-license-assignments.png)

1. Välj **Spara**.

För att tillhandahålla tjänst kontinuitet tillämpar Azure AD de nya licenserna och tar bort de gamla licenserna samtidigt för alla användare i gruppen.

## <a name="next-steps"></a>Nästa steg

Lär dig mer om andra scenarier för licens hantering genom grupper i följande artiklar:

- [Tilldela licenser till en grupp i Azure Active Directory](licensing-groups-assign.md)
- [Identifiera och lösa licensproblem för en grupp i Azure Active Directory](licensing-groups-resolve-problems.md)
- [Så här migrerar du enskilda licensierade användare till grupp licensiering i Azure Active Directory](licensing-groups-migrate-users.md)
- [Ytterligare scenarier för Azure Active Directory grupp licensiering](licensing-group-advanced.md)
- [PowerShell-exempel för grupp licensiering i Azure Active Directory](licensing-ps-examples.md)
