---
title: Ta bort en användares åtkomst till ett program i Azure Active Directory
description: Förstå hur du tar bort en användares åtkomst till ett program i Azure Active Directory
services: active-directory
author: iantheninja
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 11/02/2020
ms.author: iangithinji
ms.openlocfilehash: 958abc5f9be443d66037a6d9fe8d8779e6e37e0e
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2021
ms.locfileid: "107379595"
---
# <a name="how-to-remove-a-users-access-to-an-application"></a>Ta bort en användares åtkomst till ett program

Den här artikeln hjälper dig att förstå hur du tar bort en användares åtkomst till ett program.

## <a name="i-want-to-remove-a-specific-users-or-groups-assignment-to-an-application"></a>Jag vill ta bort en viss användares eller grupps tilldelning till ett program

Om du vill ta bort en användar- eller grupptilldelning till ett program följer du stegen i artikeln Ta bort en användar- eller [grupptilldelning från en företagsapp i Azure Active Directory.](./assign-user-or-group-access-portal.md)

## <a name="i-want-to-disable-all-access-to-an-application-for-every-user"></a>Jag vill inaktivera all åtkomst till ett program för varje användare

Om du vill inaktivera alla användar inloggningar till ett program följer du stegen i artikeln Inaktivera användar inloggningar för en [företagsapp i Azure Active Directory.](./disable-user-sign-in-portal.md)

## <a name="i-want-to-delete-an-application-entirely"></a>Jag vill ta bort ett program helt

[Snabbstartsserien om programhantering innehåller vägledning](delete-application-portal.md) om hur du tar bort ett program från din Azure Active Directory klientorganisation.

## <a name="i-want-to-disable-all-future-user-consent-operations-to-any-application"></a>Jag vill inaktivera alla framtida åtgärder för användarmedgivande för alla program

Om du inaktiverar användarmedgivande för hela katalogen kan slutanvändare inte godkänna program. Administratörer kan fortfarande godkänna för användarens räkning. Mer information om programmedgivande och varför du kanske vill göra detta finns i Förstå [användar- och administratörsmedgivande.](../develop/howto-convert-app-to-be-multi-tenant.md#understand-user-and-admin-consent) Se även Behörigheter [och medgivande.](../develop/v2-permissions-and-consent.md)

Om **du vill inaktivera alla framtida åtgärder för användarmedgivande i hela katalogen** följer du dessa anvisningar:

1.  Öppna [**Azure Portal**](https://portal.azure.com/) och logga in som **global administratör.**

2.  Öppna **Azure Active Directory tillägget** 

3.  Klicka **på Företagsprogram** på navigeringsmenyn.

5.  Klicka **på Användarinställningar.**

6.  Ställ in **Användare kan tillåta att appar får åtkomst till företagsdata för** deras räkning på **Nej** och klicka på knappen Spara.


## <a name="next-steps"></a>Nästa steg

[Hantera åtkomst till appar](what-is-access-management.md)