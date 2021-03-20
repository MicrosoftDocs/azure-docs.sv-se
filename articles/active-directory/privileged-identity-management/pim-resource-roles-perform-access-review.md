---
title: Granska åtkomst till Azure Resource roles i PIM – Azure AD | Microsoft Docs
description: Lär dig hur du granskar åtkomsten till Azure Resource roles i Azure AD Privileged Identity Management (PIM).
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
ms.openlocfilehash: 8091890d174ae68f012a6ec24685a0e705100f8b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "84743685"
---
# <a name="review-access-to-azure-resource-roles-in-privileged-identity-management"></a>Granska åtkomst till Azures resurs roller i Privileged Identity Management

Privileged Identity Management (PIM) åtkomst granskningar kan hjälpa till att skydda åtkomsten till privilegierade roller i Azure Active Directory (Azure AD). Den här artikeln beskriver hur du slutför en granskning av dina privilegierade roll tilldelningar i en Azure AD Access-granskning.

Om du har tilldelats en administrativ roll kan du behöva slutföra en åtkomst granskning av administratören för att bekräfta att du behöver en roll. Bekräftelse förfrågan kan komma från ett e-postmeddelande som innehåller en länk, eller så kan du bekräfta i [Azure Portal](https://portal.azure.com).

Om du är en privilegie rad roll administratör som är intresse rad av åtkomst granskningar kan du få mer information om [hur du startar en åtkomst granskning](pim-resource-roles-start-access-review.md).

## <a name="approve-or-deny-access"></a>Godkänn eller neka åtkomst

Du kan godkänna eller neka åtkomst baserat på om du fortfarande använder den här rollen eller inte. Välj **Godkänn** om du vill stanna kvar i rollen eller **neka** om du inte behöver åtkomst längre. Din status ändras bara när granskaren har tillämpat resultaten.

Följ de här stegen för att hitta och slutföra åtkomst granskningen:

1. Logga in på [Azure-portalen](https://portal.azure.com/).
1. Välj **Azure Active Directory** och öppna **Privileged Identity Management**.
1. Välj **Granska åtkomst**.

   ![Skärm bild av Privileged Identity Management program, med bladet granska åtkomst valt](media/pim-resource-roles-perform-access-review/rbac-access-review-complete.png)

1. Välj den granskning som du vill slutföra.
1. Välj **Godkänn** eller **neka**. I **rutan Ange en orsak** anger du en affärs motivering för ditt beslut om det behövs.

   ![Skärm bild av sidan gransknings information](media/pim-resource-roles-perform-access-review/rbac-access-review-choice.png)

## <a name="next-steps"></a>Nästa steg

- [Utför en åtkomst granskning av mina Azure AD-roller i Privileged Identity Management](pim-how-to-perform-security-review.md)
