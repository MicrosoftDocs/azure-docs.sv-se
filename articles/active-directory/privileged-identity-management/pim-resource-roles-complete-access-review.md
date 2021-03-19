---
title: Slutför åtkomst granskning av Azure Resource roles i PIM – Azure AD | Microsoft Docs
description: Lär dig hur du utför en åtkomst granskning av Azure Resource roles Privileged Identity Management i Azure Active Directory.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 11/08/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1601c9fc750df1f8e18bdaa072ede1b5ee164329
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "84743821"
---
# <a name="complete-an-access-review-of-azure-resource-roles-in-privileged-identity-management"></a>Slutför en åtkomst granskning av Azures resurs roller i Privileged Identity Management

Privilegierade roll administratörer kan granska privilegie rad åtkomst när de har [startat en åtkomst granskning](pim-resource-roles-start-access-review.md). Privileged Identity Management (PIM) i Azure Active Directory (Azure AD) skickar automatiskt ett e-postmeddelande som efterfrågar användare att granska deras åtkomst. Om en användare inte får ett e-postmeddelande kan du skicka dem till instruktioner för [hur du utför en åtkomst granskning](pim-resource-roles-perform-access-review.md).

När åtkomst gransknings perioden är över, eller när alla användare har avslutat sin egen granskning, följer du stegen i den här artikeln för att hantera granskningen och se resultatet.

## <a name="manage-access-reviews"></a>Hantera åtkomst granskningar

1. Gå till [Azure-portalen](https://portal.azure.com/). På instrument panelen väljer du tjänsten **Azure-resurser** .

2. Välj din resurs.

3. Välj avsnittet **åtkomst granskningar** på instrument panelen.

    ![Azure-resurser – åtkomst gransknings listan visar roll, ägare, start datum, slutdatum och status](media/pim-resource-roles-complete-access-review/rbac-access-review-home-list.png)

4. Välj den åtkomst granskning som du vill hantera.

På informations sidan för åtkomst granskningen finns ett antal alternativ för att hantera den här granskningen. Alternativen är följande:

![Alternativ för att hantera en granskning-stopp, återställning, tillämpa och ta bort](media/pim-resource-roles-complete-access-review/rbac-access-review-menu.png)

### <a name="stop"></a>Stoppa

Alla åtkomst granskningar har ett slutdatum. Välj **stopp** för att avsluta det tidigt. Användare som inte har slutfört sin granskning vid den här tiden kommer inte att kunna slutföra den när du har stoppat granskningen. Du kan inte starta om en granskning när den har stoppats.

### <a name="reset"></a>Återställ

Du kan återställa en åtkomst granskning för att ta bort alla beslut som fattas på den. När du har återställt en åtkomst granskning markeras alla användare som inte granskas igen.

### <a name="apply"></a>Använd

När en åtkomst granskning har slutförts väljer du **tillämpa** för att implementera resultatet av granskningen. Om en användares åtkomst nekades i granskningen tar det här steget bort roll tilldelningen.  

### <a name="delete"></a>Ta bort

Om du inte är intresse rad av att granska mer tar du bort den. Välj **ta bort** Yo ta bort granskningen från Privileged Identity Managements tjänsten.

## <a name="results"></a>Resultat

På sidan **resultat** kan du Visa och hämta en lista över dina gransknings resultat.

![Resultat sida som visar användare, resultat, orsak, granskning av, tillämpad av och tillämpa resultat](media/pim-resource-roles-complete-access-review/rbac-access-review-results.png)

## <a name="reviewers"></a>Granskare

Visa och Lägg till granskare i din befintliga åtkomst granskning. Påminn granskare för att slutföra granskningarna.

![Sid lista för granskare, namn och User Principal Name](media/pim-resource-roles-complete-access-review/rbac-access-review-reviewers.png)

## <a name="next-steps"></a>Nästa steg

- [Starta en åtkomst granskning för Azures resurs roller i Privileged Identity Management](pim-resource-roles-start-access-review.md)
- [Utför en åtkomst granskning av mina Azure Resource roles i Privileged Identity Management](pim-resource-roles-perform-access-review.md)
