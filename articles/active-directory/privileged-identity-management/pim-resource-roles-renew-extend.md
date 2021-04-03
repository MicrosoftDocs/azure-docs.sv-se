---
title: Förnya Azure-resursens roll tilldelningar i PIM – Azure AD | Microsoft Docs
description: Lär dig hur du utökar eller förnyar roll tilldelningar för Azure-resurser i Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: pim
ms.date: 11/08/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0f0d22fc540aae448a3da731b709a3b4ea13a69d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "84742241"
---
# <a name="extend-or-renew-azure-resource-role-assignments-in-privileged-identity-management"></a>Utöka eller förnya Azures resurs roll tilldelningar i Privileged Identity Management

Azure Active Directory (Azure AD) Privileged Identity Management (PIM) tillhandahåller kontroller för att hantera livs cykeln för åtkomst och tilldelning för Azure-resurser. Administratörer kan tilldela roller med start-och Slutdatum/tid-egenskaper. När tilldelnings sluten närmar sig Privileged Identity Management skickar e-postaviseringar till berörda användare eller grupper. Den skickar också e-postaviseringar till administratörer av resursen för att säkerställa att lämplig åtkomst upprätthålls. Tilldelningar kan förnyas och förbli synliga i ett utgånget tillstånd i upp till 30 dagar, även om åtkomsten inte är utökad.

## <a name="who-can-extend-and-renew"></a>Vem kan utöka och förnya?

Endast administratörer av resursen kan utöka eller förnya roll tilldelningar. Den berörda användaren eller gruppen kan begära att utöka roller som håller på att gå ut och begära att förnya roller som redan har upphört att gälla.

## <a name="when-are-notifications-sent"></a>När skickas meddelanden?

Privileged Identity Management skickar e-postaviseringar till administratörer och berörda användare eller grupper av roller som upphör att gälla inom 14 dagar och en dag före förfallo datum. Den skickar ytterligare ett e-postmeddelande när en tilldelning officiellt upphör att gälla.

Administratörer får meddelanden när en användare eller grupp tilldelas en förfallen eller förfallen roll begär Anden som ska utökas eller förnyas. När en enskild administratör löser begäran meddelas alla andra administratörer om lösnings beslutet (godkänd eller nekad). Sedan meddelas användaren eller gruppen för begäran om beslutet.

## <a name="extend-role-assignments"></a>Utöka roll tilldelningar

Följande steg beskriver processen för att begära, matcha eller administrera ett tillägg eller förnya en roll tilldelning.

### <a name="self-extend-expiring-assignments"></a>Själv utökning tilldelningar som upphör att gälla

Användare eller grupper som har tilldelats en roll kan utöka förfallna roll tilldelningar direkt från fliken **behörig** eller **aktiv** på sidan **Mina roller** i en resurs och från sidan **mina roller** på den översta nivån i Privileged Identity Managements portalen. Användare eller grupper kan begära att utöka kvalificerade och aktiva (tilldelade) roller som upphör att gälla under de kommande 14 dagarna.

![Azure-resurser – sidan mina roller visar kvalificerade roller med en åtgärds kolumn](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-ui.png)

När Tilldelningens slutdatum-tid är inom 14 dagar, blir knappen som **utöka** en aktiv länk i användar gränssnittet. I följande exempel antar vi att det aktuella datumet är 27 mars.

![Åtgärds kolumn med länkar att aktivera eller utöka](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-within-14.png)

Om du vill begära en förlängning av den här roll tilldelningen väljer du **utöka** för att öppna formuläret för begäran.

![Utöka roll tilldelnings fönstret med en orsaks ruta](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-role-assignment-request.png)

Om du vill visa information om den ursprungliga tilldelningen expanderar du **tilldelnings information**. Ange en orsak till tilläggs förfrågan och välj sedan **utöka**.

>[!NOTE]
>Vi rekommenderar att du inkluderar information om varför tillägget är nödvändigt och hur länge tillägget ska beviljas (om du har den här informationen).

![Utöka roll tilldelnings fönstret med tilldelnings Detaljer expanderat](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-form-complete.png)

Vid en stund får resurs administratörer ett e-postmeddelande som begär att de granskar tilläggs förfrågan. Om en begäran om utökning redan har skickats visas ett Azure-meddelande i portalen.

![Meddelande som förklarar att det redan finns en befintlig väntande roll tilldelnings tillägg](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-failed-existing-request.png)

Gå till sidan **väntande begär Anden** om du vill visa statusen för din begäran eller avbryta den.

![Sidan Azure-resurser – väntande förfrågningar visar alla väntande begär Anden och en länk för att avbryta](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-cancel-request.png)

### <a name="admin-approved-extension"></a>Administratörs godkänt tillägg

När en användare eller grupp skickar en begäran om att utöka en roll tilldelning får resurs administratörer ett e-postmeddelande som innehåller information om den ursprungliga tilldelningen och orsaken till begäran. Meddelandet innehåller en direkt länk till förfrågan för administratören om att godkänna eller neka.

Förutom att använda följande länk från e-post kan administratörer godkänna eller neka begär Anden genom att gå till Privileged Identity Management administrations Portal och välja **Godkänn begär Anden** i det vänstra fönstret.

![Azure-resurser – sidan Godkänn begär Anden visar begär Anden och länkar till Godkänn eller neka](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-admin-approve-grid.png)

När en administratör väljer **Godkänn** eller **neka** visas information om begäran, tillsammans med ett fält för att ge en affärs motivering för gransknings loggarna.

![Godkänn begäran om roll tilldelning med begär ande orsak, tilldelnings typ, start tid, slut tid och orsak](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-admin-approve-blade.png)

När en begäran om att utöka roll tilldelningen godkänns kan resurs administratörer välja ett nytt start datum, slutdatum och tilldelnings typ. Det kan vara nödvändigt att ändra tilldelnings typ om administratören vill ge begränsad åtkomst för att slutföra en viss uppgift (till exempel en dag). I det här exemplet kan administratören ändra tilldelningen från **tillgänglig** till **aktiv**. Det innebär att de kan ge åtkomst till begär Ande utan att behöva aktivera dem.

### <a name="admin-initiated-extension"></a>Administratörens initierade tillägg

Om en användare som har tilldelats en roll inte begär ett tillägg för roll tilldelningen kan en administratör utöka en tilldelning för användarens räkning. Administrativa tillägg för roll tilldelning kräver inte godkännande, men meddelanden skickas till alla andra administratörer när rollen har utökats.

Om du vill utöka en roll tilldelning bläddrar du till vyn resurs roll eller tilldelning i Privileged Identity Management. Hitta tilldelningen som kräver ett tillägg. Välj sedan **utöka** i kolumnen åtgärd.

![Sidan Azure-resurser – tilldelningar visar kvalificerade roller med länkar att utöka](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-admin-extend.png)

## <a name="renew-role-assignments"></a>Förnya roll tilldelningar

Medan det konceptuellt liknar processen för att begära ett tillägg är processen att förnya en förfallen roll tilldelning annorlunda. Med hjälp av följande steg kan tilldelningar och administratörer förnya åtkomsten till utgångna roller vid behov.

### <a name="self-renew"></a>Själv förnyelse

Användare som inte längre har åtkomst till resurser kan komma åt upp till 30 dagar från den förfallna tilldelnings historiken. Det gör du genom att bläddra till **Mina roller** i den vänstra rutan och sedan välja fliken **Utgångna roller** i avsnittet Azure-resurs roller.

![Sidan mina roller-fliken roller har förfallit](media/pim-resource-roles-renew-extend/aadpim-rbac-renew-from-myroles.png)

Listan över roller som visas som standard för **berättigade roller**. Använd den nedrullningsbara menyn för att växla mellan kvalificerade och aktiva tilldelade roller.

Om du vill begära förnyelse för någon av roll tilldelningarna i listan väljer du åtgärden **förnya** . Ange sedan en orsak för begäran. Det är praktiskt att ange en varaktighet Förutom eventuell ytterligare kontext eller en affärs motivering som kan hjälpa resurs administratören att besluta att godkänna eller neka.

![Fönstret förnya roll tilldelning visar rutan orsak](media/pim-resource-roles-renew-extend/aadpim-rbac-renew-request-form.png)

När begäran har skickats meddelas resurs administratörer om en väntande begäran om att förnya en roll tilldelning.

### <a name="admin-approves"></a>Admin godkänner

Resurs administratörer kan komma åt begäran om förnyelse från länken i e-postaviseringen eller genom att komma åt Privileged Identity Management från Azure Portal och välja **Godkänn förfrågningar** i den vänstra rutan.

![Azure-resurser – sidan Godkänn begär Anden visar begär Anden och länkar till Godkänn eller neka](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-admin-approve-grid.png)

När en administratör väljer **Godkänn** eller **neka** visas information om begäran tillsammans med ett fält för att ge en affärs motivering för gransknings loggarna.

![Godkänn begäran om roll tilldelning med begär ande orsak, tilldelnings typ, start tid, slut tid och orsak](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-admin-approve-blade.png)

När en begäran om att förnya roll tilldelningen godkänns måste resurs administratörer ange ett nytt start datum, slutdatum och tilldelnings typ.

### <a name="admin-renew"></a>Administratörs förnyelse

Resurs administratörer kan förnya förfallna roll tilldelningar från fliken **medlemmar** i den vänstra navigerings menyn i en resurs. De kan också förnya förfallna roll tilldelningar från fliken **Utgångna** roller i en resurs roll.

Om du vill visa en lista över alla förfallna roll tilldelningar på skärmen **medlemmar** väljer du **förfallna roller**.

![Azure-resurser – sidan medlemmar har förfallit roller med länkar att förnya](media/pim-resource-roles-renew-extend/aadpim-rbac-renew-from-member-blade.png)

## <a name="next-steps"></a>Nästa steg

- [Godkänn eller neka begär Anden för Azures resurs roller i Privileged Identity Management](pim-resource-roles-approval-workflow.md)
- [Konfigurera inställningar för Azure-resurs roll i Privileged Identity Management](pim-resource-roles-configure-role-settings.md)
