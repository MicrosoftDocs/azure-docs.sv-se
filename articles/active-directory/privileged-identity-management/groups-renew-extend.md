---
title: Förnya utgångna grupp ägare för medlems tilldelningar i Privileged Identity Management-Azure AD | Microsoft Docs
description: Lär dig hur du utökar eller förnyar grupp tilldelningar som kan tilldelas av roller i Azure AD Privileged Identity Management (PIM).
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
ms.date: 06/30/2020
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0cfbb0be22dee4550050d6af10314f3a3bb1f583
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "87505996"
---
# <a name="extend-or-renew-privileged-access-group-assignments-preview-in-privileged-identity-management"></a>Utöka eller förnya privilegierade åtkomst grupp tilldelningar (för hands version) i Privileged Identity Management

Azure Active Directory (Azure AD) Privileged Identity Management (PIM) tillhandahåller kontroller för att hantera åtkomst-och tilldelnings livs cykeln för privilegierade åtkomst grupper. Administratörer kan tilldela roller med start-och Slutdatum/tid-egenskaper. När tilldelnings sluten närmar sig Privileged Identity Management skickar e-postaviseringar till berörda användare eller grupper. Den skickar också e-postaviseringar till administratörer av resursen för att säkerställa att lämplig åtkomst upprätthålls. Tilldelningar kan förnyas och förbli synliga i ett utgånget tillstånd i upp till 30 dagar, även om åtkomsten inte är utökad.

## <a name="who-can-extend-and-renew"></a>Vem kan utöka och förnya

Endast administratörer av resursen kan utöka eller förnya tilldelningar av privilegierade åtkomst grupper. Den berörda användaren eller gruppen kan begära att utöka tilldelningar som håller på att gå ut och begära att förnya tilldelningar som redan har upphört att gälla.

## <a name="when-notifications-are-sent"></a>När meddelanden skickas

Privileged Identity Management skickar e-postaviseringar till administratörer och berörda användare av tilldelningar av privilegierade åtkomst grupper som upphör att gälla:

- Inom 14 dagar före förfallo datum
- En dag innan förfallo datum
- När en tilldelning förfaller

Administratörer får aviseringar när en användare eller grupp begär att utöka eller förnya en förfallen tilldelning eller förfaller. När en administratör löser begäran meddelas alla administratörer och den begär ande användaren om godkännandet eller avslag.

## <a name="extend-group-assignments"></a>Utöka grupp tilldelningar

Följande steg beskriver processen för att begära, lösa eller administrera ett tillägg eller en förnyelse av en grupp tilldelning.

### <a name="self-extend-expiring-assignments"></a>Själv utökning tilldelningar som upphör att gälla

Användare som är tilldelade till en privilegie rad åtkomst grupp kan utöka grupp tilldelningar som upphör att gälla direkt från fliken **berättigade** eller **aktiva** på sidan **tilldelning** för gruppen. Användare eller grupper kan begära att utöka kvalificerade och aktiva tilldelningar som upphör att gälla under de kommande 14 dagarna.

![Sidan mina roller visar berättigade assgnments med en åtgärds kolumn](media/groups-renew-extend/self-extend-group-assignment.png)

När Tilldelningens slutdatum-tid är inom 14 dagar är kommandot **utöka** tillgängligt. Om du vill begära en förlängning av en grupp tilldelning väljer du **utöka** för att öppna formuläret för begäran.

![Utöka grupp tilldelnings fönstret med en orsaks ruta och information](media/groups-renew-extend/extend-request-details-group-assignment.png)

>[!NOTE]
>Vi rekommenderar att du inkluderar information om varför tillägget är nödvändigt och hur länge tillägget ska beviljas (om du har den här informationen).

Vid en stund får administratörer ett e-postmeddelande som begär att de granskar tilläggs förfrågan. Om en begäran om utökning redan har skickats visas ett Azure-meddelande i portalen.

Om du vill visa status för eller avbryta din begäran öppnar du sidan **väntande begär Anden** för grupp tilldelningen.

![Privilegie rad åtkomst grupp tilldelningar sidan för väntande förfrågningar som visar länken för att avbryta](media/groups-renew-extend/group-assignment-extend-cancel-request.png)

### <a name="admin-approved-extension"></a>Administratörs godkänt tillägg

När en användare eller grupp skickar en begäran om att utöka en grupp tilldelning får administratörer ett e-postmeddelande som innehåller information om den ursprungliga tilldelningen och orsaken till begäran. Meddelandet innehåller en direkt länk till förfrågan för administratören om att godkänna eller neka.

Förutom att använda följande länk från e-post kan administratörer godkänna eller neka begär Anden genom att gå till Privileged Identity Management administrations Portal och välja **Godkänn begär Anden** i det vänstra fönstret.

![Grupp tilldelningar för privilegie rad åtkomst – sidan Godkänn begär Anden visar begär Anden och länkar till Godkänn eller neka](media/groups-renew-extend/group-assignment-extend-admin-approve.png)

När en administratör väljer **Godkänn** eller **neka** visas information om begäran, tillsammans med ett fält för att ge en affärs motivering för gransknings loggarna.

![Godkänn grupp tilldelnings förfrågan med en begär ande orsak, tilldelnings typ, start tid, slut tid och orsak](media/groups-renew-extend/group-assignment-extend-admin-approve-reason.png)

När du godkänner en begäran om att utöka en grupp tilldelning kan resurs administratörer välja ett nytt start datum, slutdatum och tilldelnings typ. Det kan vara nödvändigt att ändra tilldelnings typ om administratören vill ge begränsad åtkomst för att slutföra en viss uppgift (till exempel en dag). I det här exemplet kan administratören ändra tilldelningen från **tillgänglig** till **aktiv**. Det innebär att de kan ge åtkomst till begär Ande utan att behöva aktivera dem.

### <a name="admin-initiated-extension"></a>Administratörens initierade tillägg

Om en användare som har tilldelats en grupp inte begär ett tillägg för grupp tilldelningen kan en administratör utöka en tilldelning för användarens räkning. Administrativa tillägg för grupp tilldelning kräver inte godkännande, men meddelanden skickas till alla andra administratörer när tilldelningen har utökats.

Om du vill utöka en grupp tilldelning bläddrar du till vyn tilldelning i Privileged Identity Management. Hitta tilldelningen som kräver ett tillägg. Välj sedan **utöka** i kolumnen åtgärd.

![Sidan tilldelningar visar kvalificerade grupp tilldelningar med länkar som ska utökas](media/groups-renew-extend/group-assignment-extend-admin-approve.png)

## <a name="renew-group-assignments"></a>Förnya grupp tilldelningar

Medan det konceptuellt liknar processen för att begära ett tillägg skiljer sig processen för att förnya en utgånget grupp tilldelning. Med hjälp av följande steg kan tilldelningar och administratörer förnya åtkomst till utgångna tilldelningar när det behövs.

### <a name="self-renew"></a>Själv förnyelse

Användare som inte längre har åtkomst till resurser kan komma åt upp till 30 dagar från den förfallna tilldelnings historiken. Det gör du genom att bläddra till **Mina roller** i den vänstra rutan och sedan välja fliken **förfallna tilldelningar** .

![Sidan mina roller-fliken tilldelningar som har förfallit](media/groups-renew-extend/groups-renew-from-my-roles.png)

Listan över tilldelningar som visas som standard för **kvalificerade tilldelningar**. Använd den nedrullningsbara menyn för att växla mellan kvalificerade och aktiva tilldelningar.

Om du vill begära förnyelse för någon av grupp tilldelningarna i listan väljer du åtgärden **förnya** . Ange sedan en orsak för begäran. Det är praktiskt att ange en varaktighet Förutom eventuell ytterligare kontext eller en affärs motivering som kan hjälpa resurs administratören att besluta att godkänna eller neka.

![Fönstret förnya grupp tilldelning visar rutan orsak](media/groups-renew-extend/groups-renew-request-form.png)

När begäran har skickats meddelas resurs administratörer om en väntande begäran om att förnya en grupp tilldelning.

### <a name="admin-approves"></a>Admin godkänner

Resurs administratörer kan komma åt begäran om förnyelse från länken i e-postaviseringen eller genom att komma åt Privileged Identity Management från Azure Portal och välja **Godkänn förfrågningar** i den vänstra rutan.

När en administratör väljer **Godkänn** eller **neka** visas information om begäran tillsammans med ett fält för att ge en affärs motivering för gransknings loggarna.

När en begäran om att förnya en grupp tilldelning godkänns måste resurs administratörer ange ett nytt start datum, slutdatum och tilldelnings typ.

## <a name="next-steps"></a>Nästa steg

- [Godkänn eller neka begär Anden om tilldelningar av privilegierade åtkomst grupper i Privileged Identity Management](groups-approval-workflow.md)
- [Konfigurera inställningar för privilegierad åtkomst grupp i Privileged Identity Management](groups-role-settings.md)
