---
title: Så här tar du bort en användares åtkomst till ett program i Azure Active Directory
description: Förstå hur du tar bort en användares åtkomst till ett program i Azure Active Directory
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 11/02/2020
ms.author: kenwith
ms.openlocfilehash: e6a6c00811a7b87156802897db62a4a10130f130
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/02/2021
ms.locfileid: "99257363"
---
# <a name="how-to-remove-a-users-access-to-an-application"></a>Så här tar du bort en användares åtkomst till ett program

Den här artikeln hjälper dig att förstå hur du tar bort en användares åtkomst till ett program.

## <a name="i-want-to-remove-a-specific-users-or-groups-assignment-to-an-application"></a>Jag vill ta bort tilldelningen för en specifik användare eller grupp till ett program

Om du vill ta bort en användare eller grupp tilldelning till ett program följer du stegen i artikeln [ta bort en användare eller grupp tilldelning från en företags app i Azure Active Directory](./assign-user-or-group-access-portal.md) artikeln.

## <a name="i-want-to-disable-all-access-to-an-application-for-every-user"></a>Jag vill inaktivera all åtkomst till ett program för varje användare

Om du vill inaktivera alla användar inloggningar till ett program följer du stegen i avsnittet [Inaktivera användar inloggningar för en Enterprise-App i Azure Active Directory](./disable-user-sign-in-portal.md) artikeln.

## <a name="i-want-to-delete-an-application-entirely"></a>Jag vill ta bort ett program helt

[Snabb starts serien i program hantering](delete-application-portal.md) innehåller vägledning om hur du tar bort ett program från Azure Active Directory-klienten.

## <a name="i-want-to-disable-all-future-user-consent-operations-to-any-application"></a>Jag vill inaktivera alla framtida åtgärder för användar medgivande till alla program

Om du inaktiverar användar medgivande för hela katalogen kan slutanvändarna inte godkänna något program. Administratörer kan fortfarande godkänna användarens räkning. Om du vill ha mer information om program medgivande och varför du kanske inte vill göra detta, Läs [förstå användar-och administratörs medgivande](../develop/howto-convert-app-to-be-multi-tenant.md#understand-user-and-admin-consent). Se även [behörigheter och medgivande](../develop/v2-permissions-and-consent.md).

Följ dessa instruktioner om du vill **inaktivera alla framtida användar medgivande åtgärder i hela katalogen**:

1.  Öppna [**Azure Portal**](https://portal.azure.com/) och logga in som **Global administratör.**

2.  Öppna **tillägget Azure Active Directory** 

3.  Klicka på **företags program** på navigerings menyn.

5.  Klicka på **användar inställningar**.

6.  Ange att **användarna ska kunna tillåta att appar får åtkomst till företags data för deras räkning** , växla till **Nej** och klicka på knappen Spara.


## <a name="next-steps"></a>Nästa steg

[Hantera åtkomst till appar](what-is-access-management.md)