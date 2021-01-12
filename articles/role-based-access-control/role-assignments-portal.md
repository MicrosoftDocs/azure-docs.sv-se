---
title: Lägga till eller ta bort roll tilldelningar i Azure med hjälp av Azure Portal – Azure RBAC
description: Lär dig hur du beviljar åtkomst till Azure-resurser för användare, grupper, tjänstens huvud namn eller hanterade identiteter med hjälp av Azure Portal och rollbaserad åtkomst kontroll i Azure (Azure RBAC).
services: active-directory
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.topic: how-to
ms.workload: identity
ms.date: 01/11/2021
ms.author: rolyon
ms.openlocfilehash: f1753e7bc50fa9ff2c5512696a37dae7578f23b4
ms.sourcegitcommit: aacbf77e4e40266e497b6073679642d97d110cda
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/12/2021
ms.locfileid: "98117452"
---
# <a name="add-or-remove-azure-role-assignments-using-the-azure-portal"></a>Lägga till eller ta bort rolltilldelningar för Azure med hjälp av Azure-portalen

[!INCLUDE [Azure RBAC definition grant access](../../includes/role-based-access-control/definition-grant.md)] I den här artikeln beskrivs hur du tilldelar roller med hjälp av Azure Portal.

Om du behöver tilldela administratörs roller i Azure Active Directory, se [Visa och tilldela administratörs roller i Azure Active Directory](../active-directory/roles/manage-roles-portal.md).

## <a name="prerequisites"></a>Förutsättningar

[!INCLUDE [Azure role assignment prerequisites](../../includes/role-based-access-control/prerequisites-role-assignments.md)]

## <a name="add-a-role-assignment"></a>Lägg till en rolltilldelning

Du lägger till en roll tilldelning i Azure RBAC för att bevilja åtkomst till en Azure-resurs. Följ dessa steg om du vill tilldela en roll. En övergripande översikt över steg finns i [steg för att lägga till en roll tilldelning](role-assignments-steps.md).

### <a name="step-1-identify-the-needed-scope"></a>Steg 1: identifiera omfattningen som krävs

[!INCLUDE [Scope for Azure RBAC introduction](../../includes/role-based-access-control/scope-intro.md)]

[!INCLUDE [Scope for Azure RBAC least privilege](../../includes/role-based-access-control/scope-least.md)] Mer information om omfång finns i [förstå omfattning](scope-overview.md).

![Omfattnings nivåer för Azure RBAC](../../includes/role-based-access-control/media/scope-levels.png)

1. Logga in på [Azure-portalen](https://portal.azure.com).

1. I rutan Sök högst upp söker du efter det omfång som du vill bevilja åtkomst till. Du kan till exempel söka efter **hanterings grupper**, **prenumerationer**, **resurs grupper** eller en speciell resurs.

    ![Azure Portal Sök efter resurs grupp](./media/shared/rg-portal-search.png)

1. Klicka på resursen för det aktuella omfånget.

    I följande exempel visas en resurs grupp.

    ![Översikt över resurs grupp](./media/shared/rg-overview.png)

### <a name="step-2-open-the-add-role-assignment-pane"></a>Steg 2: öppna fönstret Lägg till roll tilldelning

**Åtkomst kontroll (IAM)** är den sida som du vanligt vis använder för att tilldela roller för att bevilja åtkomst till Azure-resurser. Det kallas även identitets-och åtkomst hantering (IAM) och visas på flera platser i Azure Portal.

1. Klicka på **Åtkomstkontroll (IAM)**.

    Följande visar ett exempel på sidan åtkomst kontroll (IAM) för en resurs grupp.

    ![Åtkomst kontroll (IAM)-sida för en resurs grupp](./media/shared/rg-access-control.png)

1. Klicka på fliken **roll tilldelningar** för att Visa roll tilldelningarna i det här området.

1. Klicka på **Lägg till**  >  **Lägg till roll tilldelning**.
   Om du inte har behörighet att tilldela roller är alternativet Lägg till rolltilldelning inaktiverat.

   ![Menyn Lägg till roll tilldelning](./media/shared/add-role-assignment-menu.png)

    Fönstret Lägg till rolltilldelning öppnas.

   ![Fönsterrutan Lägg till rolltilldelning](./media/shared/add-role-assignment.png)

### <a name="step-3-select-the-appropriate-role"></a>Steg 3: Välj lämplig roll

1. I listan **roll** söker du efter eller bläddrar för att hitta den roll som du vill tilldela.

    För att hjälpa dig att fastställa lämplig roll kan du hovra över informations ikonen för att visa en beskrivning av rollen. Om du vill ha mer information kan du visa artikeln [inbyggda roller i Azure](built-in-roles.md) .

   ![Välj roll i Lägg till roll tilldelning](./media/role-assignments-portal/add-role-assignment-role.png)

1. Klicka för att välja rollen.

### <a name="step-4-select-who-needs-access"></a>Steg 4: Välj vem som behöver åtkomst

1. I listan **tilldela åtkomst till** väljer du vilken typ av säkerhets objekt som ska tilldelas åtkomst.

    | Typ | Description |
    | --- | --- |
    | **Användare, grupp eller tjänstens huvud namn** | Om du vill tilldela rollen till en användare, grupp eller tjänstens huvud namn (program) väljer du den här typen. |
    | **Användare tilldelad hanterad identitet** | Om du vill tilldela rollen till en [användardefinierad hanterad identitet](../active-directory/managed-identities-azure-resources/overview.md)väljer du den här typen. |
    | *Systemtilldelad hanterad identitet* | Om du vill tilldela rollen till en [systemtilldelad hanterad identitet](../active-directory/managed-identities-azure-resources/overview.md)väljer du den Azure-tjänstinstans där den hanterade identiteten finns. |

   ![Välj säkerhets objekt typ i Lägg till roll tilldelning](./media/role-assignments-portal/add-role-assignment-type.png)

1. Om du har valt en användardefinierad hanterad identitet eller en systemtilldelad hanterad identitet väljer du den **prenumeration** där den hanterade identiteten finns.

1. I avsnittet **Välj** söker du efter säkerhets objekt genom att ange en sträng eller bläddra i listan.

   ![Välj användare i Lägg till roll tilldelning](./media/role-assignments-portal/add-role-assignment-user.png)

1. När du har hittat säkerhets objektets säkerhets objekt klickar du på den.

### <a name="step-5-assign-role"></a>Steg 5: tilldela roll

1. Om du vill tilldela rollen klickar du på **Spara**.

   Efter en liten stund tilldelas säkerhets objekt rollen i det valda omfånget.

1. På fliken **roll tilldelningar** kontrollerar du att roll tilldelningen visas i listan.

    ![Lägg till roll tilldelning Sparad](./media/role-assignments-portal/rg-role-assignments.png)

## <a name="remove-a-role-assignment"></a>Ta bort en rolltilldelning

I Azure RBAC tar du bort åtkomsten från en Azure-resurs genom att ta bort en roll tilldelning. Följ dessa steg om du vill ta bort en roll tilldelning.

1. Öppna **åtkomst kontroll (IAM)** i ett omfång, till exempel hanterings grupp, prenumeration, resurs grupp eller resurs, där du vill ta bort åtkomsten.

1. Klicka på fliken **roll tilldelningar** för att visa alla roll tilldelningar i det här omfånget.

1. Lägg till en bock intill säkerhetsobjektet med rolltilldelningen som du vil ta bort i listan med rolltilldelningar.

   ![Roll tilldelningen har marker ATS för borttagning](./media/role-assignments-portal/rg-role-assignments-select.png)

1. Klicka på **Ta bort**.

   ![Ta bort rolltilldelningsmeddelande](./media/role-assignments-portal/remove-role-assignment.png)

1. I meddelandet om att ta bort rolltilldelningen klickar du på **Ja**.

    Om du ser ett meddelande om att ärvda roll tilldelningar inte kan tas bort försöker du ta bort en roll tilldelning i ett underordnat omfång. Du bör öppna åtkomst kontroll (IAM) i omfånget där rollen har tilldelats och försöka igen. Ett snabbt sätt att öppna åtkomst kontroll (IAM) i rätt omfång är att titta i kolumnen **omfattning** och klicka på länken bredvid **(ärvd)**.

   ![Ta bort roll tilldelnings meddelande för ärvda roll tilldelningar](./media/role-assignments-portal/remove-role-assignment-inherited.png)

## <a name="next-steps"></a>Nästa steg

- [Tilldela en användare administratörsbehörighet för en Azure-prenumeration](role-assignments-portal-subscription-admin.md)
- [Lägg till en roll tilldelning för en hanterad identitet](role-assignments-portal-managed-identity.md)
- [Felsöka Azure RBAC](troubleshooting.md)
