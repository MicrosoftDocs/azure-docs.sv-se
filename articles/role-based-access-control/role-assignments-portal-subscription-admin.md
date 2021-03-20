---
title: Tilldela en användare som administratör för en Azure-prenumeration – Azure RBAC
description: Lär dig hur du gör en användare till administratör för en Azure-prenumeration med hjälp av Azure Portal och rollbaserad åtkomst kontroll i Azure (Azure RBAC).
services: active-directory
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.topic: how-to
ms.workload: identity
ms.date: 01/11/2021
ms.author: rolyon
ms.openlocfilehash: dec5888127ed1fc291bec244a44cfb71e343e3bb
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "100556832"
---
# <a name="assign-a-user-as-an-administrator-of-an-azure-subscription"></a>Tilldela en användare administratörsbehörighet för en Azure-prenumeration

Om du vill göra en användare till en administratör för en Azure-prenumeration tilldelar du den till [ägar](built-in-roles.md#owner) rollen i prenumerations omfånget. Ägar rollen ger användaren fullständig åtkomst till alla resurser i prenumerationen, inklusive behörighet att bevilja åtkomst till andra. De här stegen är desamma som för andra rolltilldelningar.

## <a name="prerequisites"></a>Förutsättningar

[!INCLUDE [Azure role assignment prerequisites](../../includes/role-based-access-control/prerequisites-role-assignments.md)]

## <a name="step-1-open-the-subscription"></a>Steg 1: öppna prenumerationen

1. Logga in på [Azure-portalen](https://portal.azure.com).

1. Sök efter prenumerationer i rutan Sök högst upp.

    ![Azure Portal Sök efter resurs grupp](./media/shared/sub-portal-search.png)

1. Klicka på den prenumeration som du vill använda.

    Följande visar en exempel prenumeration.

    ![Översikt över resurs grupp](./media/shared/sub-overview.png)

## <a name="step-2-open-the-add-role-assignment-pane"></a>Steg 2: öppna fönstret Lägg till roll tilldelning

**Åtkomst kontroll (IAM)** är den sida som du vanligt vis använder för att tilldela roller för att bevilja åtkomst till Azure-resurser. Det kallas även identitets-och åtkomst hantering (IAM) och visas på flera platser i Azure Portal.

1. Klicka på **Åtkomstkontroll (IAM)**.

    Följande visar ett exempel på sidan åtkomst kontroll (IAM) för en prenumeration.

    ![Åtkomst kontroll (IAM)-sida för en resurs grupp](./media/shared/sub-access-control.png)

1. Klicka på fliken **roll tilldelningar** för att Visa roll tilldelningarna i det här området.

1. Klicka på **Lägg till**  >  **Lägg till roll tilldelning**.
   Om du inte har behörighet att tilldela roller är alternativet Lägg till rolltilldelning inaktiverat.

   ![Menyn Lägg till roll tilldelning](./media/shared/add-role-assignment-menu.png)

    Fönstret Lägg till rolltilldelning öppnas.

   ![Fönsterrutan Lägg till rolltilldelning](./media/shared/add-role-assignment.png)

## <a name="step-3-select-the-owner-role"></a>Steg 3: Välj ägar rollen

[Ägar](built-in-roles.md#owner) rollen beviljar fullständig åtkomst för att hantera alla resurser, inklusive möjligheten att tilldela roller i Azure RBAC. Du bör ha högst tre prenumerations ägare för att minska risken för brott mot en komprometterad ägare.

- Välj **ägar** rollen i listan **roll** .

   ![Välj ägar roll i fönstret Lägg till roll tilldelning](./media/role-assignments-portal-subscription-admin/add-role-assignment-role-owner.png)

## <a name="step-4-select-who-needs-access"></a>Steg 4: Välj vem som behöver åtkomst

1. I listan **tilldela åtkomst till väljer du** **användare, grupp eller tjänstens huvud namn**.

1. I avsnittet **Välj** kan du söka efter användaren genom att ange en sträng eller bläddra i listan.

   ![Välj användare i Lägg till roll tilldelning](./media/role-assignments-portal-subscription-admin/add-role-assignment-user-admin.png)

1. När du har hittat användaren, klickar du för att markera den.

## <a name="step-5-assign-role"></a>Steg 5: tilldela roll

1. Om du vill tilldela rollen klickar du på **Spara**.

   Efter en liten stund tilldelas användaren rollen i det valda omfånget.

1. På fliken **roll tilldelningar** kontrollerar du att roll tilldelningen visas i listan.

    ![Lägg till roll tilldelning Sparad](./media/role-assignments-portal-subscription-admin/sub-role-assignments-owner.png)

## <a name="next-steps"></a>Nästa steg

- [Tilldela Azure-roller med hjälp av Azure Portal](role-assignments-portal.md)
- [Visa en lista med Azures roll tilldelningar med hjälp av Azure Portal](role-assignments-list-portal.md)
- [Ordna resurser med hanteringsgrupper i Azure](../governance/management-groups/overview.md)
