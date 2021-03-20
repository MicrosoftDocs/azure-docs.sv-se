---
title: Verifiera regler för dynamiskt grupp medlemskap (för hands version) – Azure AD | Microsoft Docs
description: Så här testar du medlemmar mot en medlemskaps regel för dynamiska grupper i Azure Active Directory.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.subservice: enterprise-users
ms.workload: identity
ms.topic: how-to
ms.date: 12/02/2020
ms.author: curtand
ms.reviewer: yukarppa
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: f1048284e8e7a492bf0810a16e29409546ed414b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96547567"
---
# <a name="validate-a-dynamic-group-membership-rule-preview-in-azure-active-directory"></a>Verifiera en regel för dynamisk grupp medlemskap (förhands granskning) i Azure Active Directory

Azure Active Directory (Azure AD) ger nu möjlighet att verifiera dynamiska grupp regler (i offentlig för hands version). På fliken **validera regler** kan du verifiera din dynamiska regel mot exempel grupp medlemmar för att bekräfta att regeln fungerar som förväntat. När du skapar eller uppdaterar dynamiska grupp regler vill administratörer veta om en användare eller enhet kommer att vara medlem i gruppen. Detta hjälper till att utvärdera om användaren eller enheten uppfyller regel villkoren och hjälp vid fel sökning när medlemskap inte förväntas.

## <a name="step-by-step-walk-through"></a>Steg för steg-anvisningar

Kom igång genom att gå till **Azure Active Directory**  >  **grupper**. Välj en befintlig dynamisk grupp eller skapa en ny dynamisk grupp och klicka på regler för dynamiskt medlemskap. Sedan kan du se fliken **validera regler** .

![Leta upp fliken validera regler och börja med en befintlig regel](./media/groups-dynamic-rule-validation/validate-tab.png)

På fliken **validera regler** kan du välja användare för att verifiera deras medlemskap. Det går att välja 20 användare eller enheter samtidigt.

![Lägg till användare för att validera den befintliga regeln mot](./media/groups-dynamic-rule-validation/validate-tab-add-users.png)

När du har valt användare eller enheter från väljaren och **väljer**, startar valideringen automatiskt och verifierings resultaten visas.

![Visa resultaten av regel valideringen](./media/groups-dynamic-rule-validation/validate-tab-results.png)

Resultatet anger om en användare är medlem i gruppen eller inte. Om regeln inte är giltig eller om det uppstår ett nätverks problem visas resultatet som **Okänt**. I händelse av **Okänt**, beskriver det detaljerade fel meddelandet problemet och åtgärderna som behövs.

![Visa information om resultatet av regel valideringen](./media/groups-dynamic-rule-validation/validate-tab-view-details.png)

Du kan ändra regeln och valideringen av medlemskap kommer att utlösas. Om du vill se varför användaren inte är medlem i gruppen klickar du på "Visa information" och verifierings information visar resultatet av varje uttryck som skriver regeln. Klicka på **OK** för att avsluta.

## <a name="next-steps"></a>Nästa steg

- [Regler för dynamiskt medlemskap för grupper](groups-dynamic-membership.md)
