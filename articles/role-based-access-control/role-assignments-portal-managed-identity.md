---
title: Lägga till en roll tilldelning för en hanterad identitet (för hands version) – Azure RBAC
description: Lär dig hur du lägger till en roll tilldelning genom att börja med den hanterade identiteten och sedan välja omfattning och roll med hjälp av Azure Portal och rollbaserad åtkomst kontroll i Azure (Azure RBAC).
services: active-directory
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.topic: how-to
ms.workload: identity
ms.date: 01/11/2021
ms.author: rolyon
ms.openlocfilehash: a01246c0cf35653f4d13262183cf9df28b056c69
ms.sourcegitcommit: aacbf77e4e40266e497b6073679642d97d110cda
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/12/2021
ms.locfileid: "98122495"
---
# <a name="add-a-role-assignment-for-a-managed-identity-preview"></a>Lägg till en roll tilldelning för en hanterad identitet (förhands granskning)

Du kan lägga till roll tilldelningar för en hanterad identitet med hjälp av sidan **åtkomst kontroll (IAM)** enligt beskrivningen i [Lägg till eller ta bort roll tilldelningar i Azure med hjälp av Azure Portal](role-assignments-portal.md). När du använder sidan åtkomst kontroll (IAM) börjar du med omfånget och väljer sedan den hanterade identiteten och rollen. I den här artikeln beskrivs ett alternativt sätt att lägga till roll tilldelningar för en hanterad identitet. Med de här stegen börjar du med den hanterade identiteten och väljer sedan omfattning och roll.

> [!IMPORTANT]
> Att lägga till en roll tilldelning för en hanterad identitet med de här alternativa stegen är för närvarande en för hands version.
> Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade.
> Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Förutsättningar

[!INCLUDE [Azure role assignment prerequisites](../../includes/role-based-access-control/prerequisites-role-assignments.md)]

## <a name="system-assigned-managed-identity"></a>Systemtilldelad hanterad identitet

Följ dessa steg om du vill tilldela en roll till en systemtilldelad hanterad identitet genom att börja med den hanterade identiteten.

1. I Azure Portal öppnar du en systemtilldelad hanterad identitet.

1. Klicka på **identitet** i den vänstra menyn.

    ![Systemtilldelad hanterad identitet](./media/shared/identity-system-assigned.png)

1. Under **behörigheter** klickar du på **roll tilldelningar för Azure**.

    Om roller redan har tilldelats den valda systemtilldelade hanterade identiteten visas en lista över roll tilldelningar. Den här listan innehåller alla roll tilldelningar som du har behörighet att läsa.

    ![Roll tilldelningar för en systemtilldelad hanterad identitet](./media/shared/role-assignments-system-assigned.png)

1. Klicka på **prenumerations** listan om du vill ändra prenumerationen.

1. Klicka på **Lägg till roll tilldelning (för hands version)**.

1. Använd List rutorna för att välja den uppsättning resurser som roll tilldelningen avser, till exempel **prenumeration**, **resurs grupp** eller resurs.

    Om du inte har Skriv behörighet för roll tilldelning för det valda omfånget visas ett infogat meddelande. 

1. I listrutan **Roll** väljer du en roll, till exempel **Virtuell datordeltagare**.

   ![Lägg till Roll tilldelnings fönster för systemtilldelad hanterad identitet](./media/role-assignments-portal-managed-identity/add-role-assignment-with-scope.png)

1. Klicka på **Spara** för att tilldela rollen.

   Efter en liten stund tilldelas den hanterade identiteten rollen i det valda omfånget.

## <a name="user-assigned-managed-identity"></a>Användartilldelad hanterad identitet

Följ dessa steg om du vill tilldela en roll till en användardefinierad hanterad identitet genom att börja med den hanterade identiteten.

1. Öppna en användardefinierad hanterad identitet i Azure Portal.

1. Klicka på **roll tilldelningar för Azure** i den vänstra menyn.

    Om roller redan har tilldelats den valda användarspecifika hanterade identiteten visas listan över roll tilldelningar. Den här listan innehåller alla roll tilldelningar som du har behörighet att läsa.

    ![Roll tilldelningar för en användardefinierad hanterad identitet](./media/shared/role-assignments-user-assigned.png)

1. Klicka på **prenumerations** listan om du vill ändra prenumerationen.

1. Klicka på **Lägg till roll tilldelning (för hands version)**.

1. Använd List rutorna för att välja den uppsättning resurser som roll tilldelningen avser, till exempel **prenumeration**, **resurs grupp** eller resurs.

    Om du inte har Skriv behörighet för roll tilldelning för det valda omfånget visas ett infogat meddelande. 

1. I listrutan **Roll** väljer du en roll, till exempel **Virtuell datordeltagare**.

   ![Lägg till Roll tilldelnings fönster för en användardefinierad hanterad identitet](./media/role-assignments-portal-managed-identity/add-role-assignment-with-scope.png)

1. Klicka på **Spara** för att tilldela rollen.

   Efter en liten stund tilldelas den hanterade identiteten rollen i det valda omfånget.

## <a name="next-steps"></a>Nästa steg

- [Vad är hanterade identiteter för Azure-resurser?](../active-directory/managed-identities-azure-resources/overview.md)
- [Lägga till eller ta bort rolltilldelningar för Azure med hjälp av Azure-portalen](role-assignments-portal.md)
- [Visa en lista med Azures roll tilldelningar med hjälp av Azure Portal](role-assignments-list-portal.md)
