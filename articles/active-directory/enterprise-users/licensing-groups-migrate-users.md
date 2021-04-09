---
title: Lägg till användare med direkt licenser till grupp licensiering – Azure AD | Microsoft Docs
description: Så här migrerar du från enskilda användar licenser till gruppbaserad licensiering med Azure Active Directory
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
ms.custom: seohack1;it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: adc80cf579ce3086abd4171b065f859acd0b9294
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96546513"
---
# <a name="how-to-migrate-users-with-individual-licenses-to-groups-for-licensing"></a>Så här migrerar du användare med enskilda licenser till grupper för licensiering

Du kan ha befintliga licenser distribuerade till användare i organisationerna via direkt tilldelning. Det innebär att du kan använda PowerShell-skript eller andra verktyg för att tilldela enskilda användar licenser. Innan du börjar använda gruppbaserad licensiering för att hantera licenser i din organisation kan du använda den här migrations planen för att på ett smidigt sätt ersätta befintliga lösningar med gruppbaserad licensiering.

Det viktigaste att tänka på är att du bör undvika en situation där migreringen till gruppbaserad licensiering leder till att användarna tillfälligt förlorar sina tilldelade licenser. Alla processer som kan leda till borttagning av licenser bör undvikas för att ta bort risken för att användare förlorar åtkomst till tjänster och deras data.

## <a name="recommended-migration-process"></a>Rekommenderad migreringsprocessen

1. Du har befintlig automatisering (till exempel PowerShell) som hanterar licens tilldelning och borttagning av användare. Låt det köras i befintligt skick.

1. Skapa en ny licens grupp (eller Bestäm vilka befintliga grupper som ska användas) och se till att alla nödvändiga användare läggs till som medlemmar.

1. Tilldela de licenser som krävs till dessa grupper. målet bör vara att avspegla samma licensierings tillstånd som din befintliga automatisering (till exempel PowerShell) gäller för dessa användare.

1. Kontrol lera att licenserna har tillämpats på alla användare i dessa grupper. Du kan göra det här programmet genom att kontrol lera bearbetnings statusen för varje grupp och genom att kontrol lera gransknings loggarna.

   - Du kan kontrol lera enskilda användare genom att titta på deras licens information. Du ser att de har samma licenser som har tilldelats "direkt" och "ärvda" från grupper.

   - Du kan köra ett PowerShell-skript för att [kontrol lera hur licenser tilldelas till användare](licensing-group-advanced.md#use-powershell-to-see-who-has-inherited-and-direct-licenses).

   - När samma produkt licens tilldelas användaren både direkt och via en grupp, används bara en licens för användaren. Det krävs därför inga ytterligare licenser för att utföra migrering.

1. Kontrol lera att inga licens tilldelningar misslyckades genom att kontrol lera varje grupp för användare med fel tillstånd. Mer information finns i [identifiera och lösa licens problem för en grupp](licensing-groups-resolve-problems.md).

Överväg att ta bort de ursprungliga direkta tilldelningarna. Vi rekommenderar att du gör det gradvis och övervakar resultatet på en delmängd av användarna först. Om du kan lämna de ursprungliga direkta tilldelningarna för användarna, men när användarna lämnar sina licensierade grupper behåller de de direkt tilldelade licenserna, vilket kanske inte är vad du vill.

## <a name="an-example"></a>Ett exempel

En organisation har 1 000 användare. Alla användare kräver Office 365 Enterprise E3-licenser. För närvarande har organisationen ett PowerShell-skript som körs lokalt, lägger till och tar bort licenser från användare när de kommer och går. Organisationen vill dock ersätta skriptet med gruppbaserad licensiering så att licenser kan hanteras automatiskt av Azure AD.

I så fall kan migreringsprocessen se ut så här:

1. Med hjälp av Azure Portal tilldelar du Office 365 E3-licensen till gruppen **alla användare** i Azure AD.

1. Bekräfta att licens tilldelningen har slutförts för alla användare. Gå till sidan Översikt för gruppen, Välj **licenser** och kontrol lera bearbetnings status överst på bladet **licenser** .

   - Sök efter "de senaste licens ändringarna har tillämpats för alla användare" för att bekräfta bearbetningen har slutförts.

   - Sök efter ett meddelande ovanpå alla användare för vilka licenser kanske inte har tilldelats. Har vi slut på licenser för vissa användare? Har vissa användare motstridiga licens planer som hindrar dem från att ärva grupp licenser?

1. Kontrol lera vissa användare för att kontrol lera att de båda licenserna för direkt och grupp har tillämpats. Gå till profil sidan för en användare, Välj **licenser** och undersök licensernas tillstånd.

   - Detta är förväntat användar tillstånd under migreringen:

      ![förväntat användar tillstånd under migreringen](./media/licensing-groups-migrate-users/expected-user-state.png)

     Detta bekräftar att användaren har både direkta och ärvda licenser. Vi ser att Office 365 E3 har tilldelats.

   - Välj varje licens för att se vilka tjänster som är aktiverade. För att kontrol lera att de direkta och grupp licenserna tillåter exakt samma tjänster för användaren, Välj **tilldelningar**.

1. När du har bekräftat att både direkta och grupp licenser är likvärdiga, kan du börja ta bort direkt licenser från användare. Du kan testa detta genom att ta bort dem för enskilda användare i portalen och sedan köra automatiserings skript så att de tas bort i grupp. Här är ett exempel på samma användare med direkta licenser som tas bort via portalen. Observera att licens statusen är oförändrad, men vi kan inte längre se direkta tilldelningar.

   ![bekräfta att direkta licenser tas bort](./media/licensing-groups-migrate-users/direct-licenses-removed.png)

## <a name="next-steps"></a>Nästa steg

Lär dig mer om andra scenarier för grupp licens hantering:

- [Vad är gruppbaserad licensiering i Azure Active Directory?](../fundamentals/active-directory-licensing-whatis-azure-portal.md)
- [Tilldela licenser till en grupp i Azure Active Directory](licensing-groups-assign.md)
- [Identifiera och lösa licensproblem för en grupp i Azure Active Directory](licensing-groups-resolve-problems.md)
- [Så här migrerar du användare mellan produkt licenser med gruppbaserad licensiering i Azure Active Directory](licensing-groups-change-licenses.md)
- [Fler scenarier med gruppbaserad licensiering i Azure Active Directory](licensing-group-advanced.md)
- [PowerShell-exempel för gruppbaserad licensiering i Azure Active Directory](licensing-ps-examples.md)
