---
title: Tilldela Azure-roller med hjälp av Azure Portal – Azure RBAC
description: Lär dig hur du beviljar åtkomst till Azure-resurser för användare, grupper, tjänstens huvud namn eller hanterade identiteter med hjälp av Azure Portal och rollbaserad åtkomst kontroll i Azure (Azure RBAC).
services: active-directory
author: rolyon
manager: daveba
ms.service: role-based-access-control
ms.topic: how-to
ms.workload: identity
ms.date: 02/15/2021
ms.author: rolyon
ms.custom: contperf-fy21q3-portal
ms.openlocfilehash: 6b159d9ca7d8d7739d623bb3a48752b4d03e24bc
ms.sourcegitcommit: 77d7639e83c6d8eb6c2ce805b6130ff9c73e5d29
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/05/2021
ms.locfileid: "106385979"
---
# <a name="assign-azure-roles-using-the-azure-portal"></a>Tilldela Azure-roller med hjälp av Azure Portal

[!INCLUDE [Azure RBAC definition grant access](../../includes/role-based-access-control/definition-grant.md)] I den här artikeln beskrivs hur du tilldelar roller med hjälp av Azure Portal.

Om du behöver tilldela administratörs roller i Azure Active Directory, se [Visa och tilldela administratörs roller i Azure Active Directory](../active-directory/roles/manage-roles-portal.md).

## <a name="prerequisites"></a>Krav

[!INCLUDE [Azure role assignment prerequisites](../../includes/role-based-access-control/prerequisites-role-assignments.md)]

## <a name="step-1-identify-the-needed-scope"></a>Steg 1: identifiera omfattningen som krävs

[!INCLUDE [Scope for Azure RBAC introduction](../../includes/role-based-access-control/scope-intro.md)]

[!INCLUDE [Scope for Azure RBAC least privilege](../../includes/role-based-access-control/scope-least.md)] Mer information om omfång finns i [förstå omfattning](scope-overview.md).

![Omfattnings nivåer för Azure RBAC](../../includes/role-based-access-control/media/scope-levels.png)

1. Logga in på [Azure-portalen](https://portal.azure.com).

1. I rutan Sök högst upp söker du efter det omfång som du vill bevilja åtkomst till. Du kan till exempel söka efter **hanterings grupper**, **prenumerationer**, **resurs grupper** eller en speciell resurs.

    ![Azure Portal Sök efter resurs grupp](./media/shared/rg-portal-search.png)

1. Klicka på resursen för det aktuella omfånget.

    I följande exempel visas en resurs grupp.

    ![Översikt över resurs grupp](./media/shared/rg-overview.png)

## <a name="step-2-open-the-add-role-assignment-pane"></a>Steg 2: öppna fönstret Lägg till roll tilldelning

**Åtkomst kontroll (IAM)** är den sida som du vanligt vis använder för att tilldela roller för att bevilja åtkomst till Azure-resurser. Det kallas även identitets-och åtkomst hantering (IAM) och visas på flera platser i Azure Portal.

1. Klicka på **Åtkomstkontroll (IAM)**.

    Följande visar ett exempel på sidan åtkomst kontroll (IAM) för en resurs grupp.

    ![Åtkomst kontroll (IAM)-sida för en resurs grupp](./media/shared/rg-access-control.png)

1. Klicka på fliken **roll tilldelningar** för att Visa roll tilldelningarna i det här området.

1. Klicka på **Lägg till**  >  **Lägg till roll tilldelning**.
   Om du inte har behörighet att tilldela roller är alternativet Lägg till rolltilldelning inaktiverat.

   ![Menyn Lägg till roll tilldelning](./media/shared/add-role-assignment-menu.png)

    Fönstret Lägg till rolltilldelning öppnas.

   ![Sidan Lägg till roll tilldelning](../../includes/role-based-access-control/media/add-role-assignment-page.png)

## <a name="step-3-select-the-appropriate-role"></a>Steg 3: Välj lämplig roll

1. I listan **roll** söker du efter eller bläddrar för att hitta den roll som du vill tilldela.

    För att hjälpa dig att fastställa lämplig roll kan du hovra över informations ikonen för att visa en beskrivning av rollen. Om du vill ha mer information kan du visa artikeln [inbyggda roller i Azure](built-in-roles.md) .

   ![Välj roll i Lägg till roll tilldelning](./media/role-assignments-portal/add-role-assignment-role.png)

1. Klicka för att välja rollen.

## <a name="step-4-select-who-needs-access"></a>Steg 4: Välj vem som behöver åtkomst

1. I listan **tilldela åtkomst till** väljer du vilken typ av säkerhets objekt som ska tilldelas åtkomst.

    | Typ | Beskrivning |
    | --- | --- |
    | **Användare, grupp eller tjänstens huvud namn** | Om du vill tilldela rollen till en användare, grupp eller tjänstens huvud namn (program) väljer du den här typen. |
    | **Användare tilldelad hanterad identitet** | Om du vill tilldela rollen till en [användardefinierad hanterad identitet](../active-directory/managed-identities-azure-resources/overview.md)väljer du den här typen. |
    | *Systemtilldelad hanterad identitet* | Om du vill tilldela rollen till en [systemtilldelad hanterad identitet](../active-directory/managed-identities-azure-resources/overview.md)väljer du den Azure-tjänstinstans där den hanterade identiteten finns. |

   ![Välj säkerhets objekt typ i Lägg till roll tilldelning](./media/role-assignments-portal/add-role-assignment-type.png)

1. Om du har valt en användardefinierad hanterad identitet eller en systemtilldelad hanterad identitet väljer du den **prenumeration** där den hanterade identiteten finns.

1. I avsnittet **Välj** söker du efter säkerhets objekt genom att ange en sträng eller bläddra i listan.

   ![Välj användare i Lägg till roll tilldelning](./media/role-assignments-portal/add-role-assignment-user.png)

1. När du har hittat säkerhets objektets säkerhets objekt klickar du på den.

## <a name="step-5-assign-role"></a>Steg 5: tilldela roll

1. Om du vill tilldela rollen klickar du på **Spara**.

   Efter en liten stund tilldelas säkerhets objekt rollen i det valda omfånget.

1. På fliken **roll tilldelningar** kontrollerar du att roll tilldelningen visas i listan.

    ![Lägg till roll tilldelning Sparad](./media/role-assignments-portal/rg-role-assignments.png)

## <a name="next-steps"></a>Nästa steg

- [Tilldela en användare administratörsbehörighet för en Azure-prenumeration](role-assignments-portal-subscription-admin.md)
- [Ta bort roll tilldelningar i Azure](role-assignments-remove.md)
- [Felsöka Azure RBAC](troubleshooting.md)
