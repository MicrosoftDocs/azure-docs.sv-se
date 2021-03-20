---
title: Tilldela licenser till en grupp-Azure Active Directory | Microsoft Docs
description: Tilldela licenser till användare med hjälp av Azure Active Directory Group Licensing
services: active-directory
keywords: Azure AD-licensiering
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.subservice: enterprise-users
ms.topic: how-to
ms.workload: identity
ms.date: 12/02/2020
ms.author: curtand
ms.reviewer: sumitp
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1c1a6957d347681f1c7dd9979429fffb153ccb39
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96546615"
---
# <a name="assign-licenses-to-users-by-group-membership-in-azure-active-directory"></a>Tilldela licenser till användare efter grupp medlemskap i Azure Active Directory

Den här artikeln beskriver hur du tilldelar produkt licenser till en grupp användare och kontrollerar att de är korrekt licensierade i Azure Active Directory (Azure AD).

I det här exemplet innehåller Azure AD-organisationen en säkerhets grupp med namnet **HR Department**. Den här gruppen omfattar alla medlemmar av personalavdelningen (cirka 1 000 användare). Du vill tilldela Office 365 Enterprise E3-licenser till hela avdelningen. Yammer Enterprise-tjänsten som ingår i produkten måste vara tillfälligt inaktive rad tills avdelningen är redo att börja använda den. Du vill också distribuera Enterprise Mobility + Security licenser till samma grupp med användare.

> [!NOTE]
> Vissa Microsoft-tjänster är inte tillgängliga på alla platser. Innan en licens kan tilldelas en användare måste administratören ange egenskapen för användnings plats för användaren.
>
> För grupp licens tilldelningen ärver alla användare som saknar en användnings plats platsen för katalogen. Om du har användare på flera platser rekommenderar vi att du alltid anger användnings plats som en del av ditt flöde för att skapa användare i Azure AD (t. ex. via AAD Connect-konfiguration) – som garanterar att licens tilldelningen alltid är korrekt och användarna inte får tjänster på platser som inte är tillåtna.

## <a name="step-1-assign-the-required-licenses"></a>Steg 1: tilldela de licenser som krävs

1. Logga in på [**Azure AD Admin Center**](https://aad.portal.azure.com) med ett licens administratörs konto. För att hantera licenser måste kontot vara en licens administratör, användar administratör eller global administratör.

1. Välj **licenser** för att öppna en sida där du kan se och hantera alla licensierbart-produkter i organisationen.

1. Under **alla produkter** väljer du både Office 365 Enterprise E5 och Enterprise Mobility + Security E3 genom att välja produkt namnen. Om du vill starta tilldelningen väljer du **tilldela** högst upp på sidan.

   ![Välj produkter för att tilldela licenser](./media/licensing-groups-assign/licenses-all-products-assign.png)
  
1. På sidan **tilldela licens** väljer du **användare och grupper** för att öppna en lista över användare och grupper.

1. Välj en användare eller grupp och bekräfta valet genom att använda knappen **Välj** längst ned på sidan.

1. På sidan **tilldela licens** klickar du på **tilldelnings alternativ**, som visar alla Service planer som ingår i de två produkter som vi valde tidigare. Hitta **Yammer Enterprise** **och inaktivera tjänsten** från produkt licensen. Bekräfta genom att klicka på **OK** längst ned i **licens alternativen**.

   ![Välj tjänst planer för licenser](./media/licensing-groups-assign/assignment-options.png)
  
1. Slutför tilldelningen på sidan **tilldela licens** genom att klicka på **tilldela** längst ned på sidan.

1. Ett meddelande visas i det övre högra hörnet som visar status och resultatet av processen. Om tilldelningen till gruppen inte kunde slutföras (till exempel på grund av befintliga licenser i gruppen) klickar du på meddelandet för att visa information om felen.

När du tilldelar licenser till en grupp bearbetar Azure AD alla befintliga medlemmar i gruppen. Den här processen kan ta lite tid, variera med gruppens storlek. Nästa steg beskriver hur du kontrollerar att processen har slutförts och fastställer om ytterligare uppmärksamhet krävs för att lösa problem.

## <a name="step-2-verify-that-the-initial-assignment-has-finished"></a>Steg 2: kontrol lera att den inledande tilldelningen har avslut ATS

1. Gå till **Azure Active Directory**  >  **grupper**. Välj den grupp som licenserna tilldelades.

1. Välj **licenser** på sidan grupp. På så sätt kan du snabbt bekräfta om licenserna har tilldelats fullständigt till användare och om det finns fel som du behöver titta på. Följande information är tillgänglig:

   - Tjänst licenser som är tilldelade till gruppen. Välj en post för att visa de enskilda tjänster som har Aktiver ATS och att göra ändringar.

   - Status uppdateringar av de senaste licens ändringarna, som är tillgängliga om ändringarna bearbetas eller om bearbetningen har avslut ATS för alla användar medlemmar.

   - Information om användar licens tilldelningar som är i fel tillstånd.

   ![licens fel och licens status](./media/licensing-groups-assign/assignment-errors.png)

1. Se mer detaljerad information om licens bearbetning under **Azure Active Directory**  >  **användare och grupper**  >  *grupp namn*  >  **gransknings loggar**. Kontrol lera följande aktiviteter:

   - Aktivitet: `Start applying group based license to users` . Detta loggas när systemet hämtar ändringen av licens tilldelningen i gruppen och börjar tillämpa den på alla användar medlemmar. Den innehåller information om den ändring som har gjorts.

   - Aktivitet: `Finish applying group based license to users` . Detta loggas när systemet har slutfört bearbetningen av alla användare i gruppen. Den innehåller en översikt över hur många användare som har bearbetats och hur många användare som inte kunde tilldelas grupp licenser.

   [Läs det här avsnittet](licensing-group-advanced.md#use-audit-logs-to-monitor-group-based-licensing-activity) om du vill veta mer om hur gransknings loggar kan användas för att analysera ändringar som görs med gruppbaserad licensiering.

## <a name="step-3-check-for-license-problems-and-resolve-them"></a>Steg 3: Sök efter licens problem och lös dem

1. Gå till **Azure Active Directory**  >  **grupper** och Sök efter den grupp som licenser har tilldelats.
1. Välj **licenser** på sidan grupp. Meddelandet överst på sidan visar att det finns 10 användare som det inte gick att tilldela licenser till. Öppna den för att se en lista över alla användare i ett licensierings fel tillstånd för den här gruppen.
1. Kolumnen **misslyckade tilldelningar** visar att det inte gick att tilldela båda produkt licenserna till användarna. Den **vanligaste orsaken till fel** -kolumnen innehåller orsaken till felet. I det här fallet är **tjänst planer i konflikt**.

   ![licenser som inte kunde tilldelas](./media/licensing-groups-assign/failed-assignments.png)

1. Välj en användare för att öppna sidan med användarens **licenser** . På den här sidan visas alla licenser som är tilldelade till användaren. I det här exemplet har användaren Office 365 Enterprise E1-licensen som ärvts från gruppen **kiosk användare** . Detta står i konflikt med E3-licensen som systemet försökte använda från **personal avdelnings** gruppen. Därför har ingen av licenserna från gruppen tilldelats användaren.

   ![Visa alla licens konflikter för en användare](./media/licensing-groups-assign/user-licence-conflicting-service-plans.png)

1. Lös konflikten genom att ta bort användaren från gruppen **kiosk användare** . När Azure AD har behandlat ändringen tilldelas **HR-avdelningens** licenser rätt.

## <a name="next-steps"></a>Nästa steg

Mer information om funktions uppsättningen för licens tilldelning med hjälp av grupper finns i följande artiklar:

- [Vad är gruppbaserad licensiering i Azure Active Directory?](../fundamentals/active-directory-licensing-whatis-azure-portal.md?context=azure%2factive-directory%2fusers-groups-roles%2fcontext%2fugr-context)
- [Identifiera och lösa licensproblem för en grupp i Azure Active Directory](licensing-groups-resolve-problems.md)
- [Migrera enskilda licensierade användare till gruppbaserad licensiering i Azure Active Directory](licensing-groups-migrate-users.md)
- [Så här migrerar du användare mellan produkt licenser med gruppbaserad licensiering i Azure Active Directory](licensing-groups-change-licenses.md)
- [Fler scenarier med gruppbaserad licensiering i Azure Active Directory](licensing-group-advanced.md)
- [PowerShell-exempel för gruppbaserad licensiering i Azure Active Directory](licensing-ps-examples.md)