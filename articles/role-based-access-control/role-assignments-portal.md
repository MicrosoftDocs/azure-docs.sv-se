---
title: Lägga till eller ta bort roll tilldelningar i Azure med hjälp av Azure Portal – Azure RBAC
description: Lär dig hur du beviljar åtkomst till Azure-resurser för användare, grupper, tjänstens huvud namn eller hanterade identiteter med hjälp av Azure Portal och rollbaserad åtkomst kontroll i Azure (Azure RBAC).
services: active-directory
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.topic: how-to
ms.workload: identity
ms.date: 09/30/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 7c58641f0039982f05be14d0f24ba89c62273d4b
ms.sourcegitcommit: f6f928180504444470af713c32e7df667c17ac20
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/07/2021
ms.locfileid: "97964311"
---
# <a name="add-or-remove-azure-role-assignments-using-the-azure-portal"></a>Lägga till eller ta bort rolltilldelningar för Azure med hjälp av Azure-portalen

[!INCLUDE [Azure RBAC definition grant access](../../includes/role-based-access-control/definition-grant.md)] I den här artikeln beskrivs hur du tilldelar roller med hjälp av Azure Portal.

Om du behöver tilldela administratörs roller i Azure Active Directory, se [Visa och tilldela administratörs roller i Azure Active Directory](../active-directory/roles/manage-roles-portal.md).

## <a name="prerequisites"></a>Krav

Om du vill lägga till eller ta bort roll tilldelningar måste du ha:

- `Microsoft.Authorization/roleAssignments/write` och `Microsoft.Authorization/roleAssignments/delete` behörigheter, till exempel [administratör för användar åtkomst](built-in-roles.md#user-access-administrator) eller [ägare](built-in-roles.md#owner)

## <a name="access-control-iam"></a>Åtkomstkontroll (IAM)

**Åtkomst kontroll (IAM)** är den sida som du vanligt vis använder för att tilldela roller för att bevilja åtkomst till Azure-resurser. Det kallas även identitets-och åtkomst hantering och visas på flera platser i Azure Portal. Följande visar ett exempel på sidan åtkomst kontroll (IAM) för en prenumeration.

![Sidan åtkomst kontroll (IAM) för en prenumeration](./media/role-assignments-portal/access-control-subscription.png)

För att det ska vara mest effektivt med sidan åtkomst kontroll (IAM), kan du följa dessa steg för att tilldela en roll.

1. Ta reda på vem som behöver åtkomst. Du kan tilldela en roll till en användare, grupp, tjänstens huvud namn eller en hanterad identitet.

1. Hitta rätt roll. Behörigheter grupperas tillsammans i roller. Du kan välja från en lista över flera [inbyggda Azure-roller](built-in-roles.md) eller så kan du använda dina egna anpassade roller.

1. Identifiera den omfattning som krävs. Azure tillhandahåller fyra nivåer av omfång: [hanterings grupp](../governance/management-groups/overview.md), prenumeration, [resurs grupp](../azure-resource-manager/management/overview.md#resource-groups)och resurs. Mer information om omfång finns i [förstå omfattning](scope-overview.md).

1. Utför stegen i något av följande avsnitt för att tilldela en roll.

## <a name="add-a-role-assignment"></a>Lägg till en rolltilldelning

Du lägger till en roll tilldelning i Azure RBAC för att bevilja åtkomst till en Azure-resurs. Följ dessa steg om du vill tilldela en roll.

1. I Azure Portal klickar du på **alla tjänster** och väljer sedan den omfattning som du vill bevilja åtkomst till. Du kan till exempel välja **hanterings grupper**, **prenumerationer**, **resurs grupper** eller en resurs.

1. Klicka på resursen för det aktuella omfånget.

1. Klicka på **Åtkomstkontroll (IAM)**.

1. Klicka på fliken **roll tilldelningar** för att Visa roll tilldelningarna i det här området.

    ![Fliken åtkomst kontroll (IAM) och roll tilldelningar](./media/role-assignments-portal/role-assignments.png)

1. Klicka på **Lägg till**  >  **Lägg till roll tilldelning**.

   Om du inte har behörighet att tilldela roller är alternativet Lägg till rolltilldelning inaktiverat.

   ![Menyn Lägg till roll tilldelning](./media/shared/add-role-assignment-menu.png)

    Fönstret Lägg till rolltilldelning öppnas.

   ![Fönsterrutan Lägg till rolltilldelning](./media/role-assignments-portal/add-role-assignment.png)

1. I listrutan **Roll** väljer du en roll, till exempel **Virtuell datordeltagare**.

1. Välj en användare, grupp, tjänstens huvud namn eller hanterad identitet i listan **Välj** . Om du inte ser säkerhetsobjekt i listan kan du ange visningsnamn, e-postadresser och objektidentifierare i rutan **Välj** om du vill söka i katalogen.

1. Klicka på **Spara** för att tilldela rollen.

   Efter en liten stund tilldelas säkerhets objekt rollen i det valda omfånget.

    ![Lägg till roll tilldelning Sparad](./media/role-assignments-portal/add-role-assignment-save.png)

## <a name="assign-a-user-as-an-administrator-of-a-subscription"></a>Tilldela en användare administratörsbehörighet för en prenumeration

Om du vill göra en användare till en administratör för en Azure-prenumeration tilldelar du den till [ägar](built-in-roles.md#owner) rollen i prenumerations omfånget. Ägar rollen ger användaren fullständig åtkomst till alla resurser i prenumerationen, inklusive behörighet att bevilja åtkomst till andra. De här stegen är desamma som för andra rolltilldelningar.

1. I Azure-portalen klickar du på **Alla tjänster** och sedan **Prenumerationer**.

1. Klicka på prenumerationen du vill ge åtkomst till.

1. Klicka på **Åtkomstkontroll (IAM)** .

1. Klicka på fliken **roll tilldelningar** för att Visa roll tilldelningarna för den här prenumerationen.

    ![Fliken åtkomst kontroll (IAM) och roll tilldelningar](./media/role-assignments-portal/role-assignments.png)

1. Klicka på **Lägg till**  >  **Lägg till roll tilldelning**.

   Om du inte har behörighet att tilldela roller är alternativet Lägg till rolltilldelning inaktiverat.

   ![Lägg till meny för roll tilldelning för en prenumeration](./media/shared/add-role-assignment-menu.png)

    Fönstret Lägg till rolltilldelning öppnas.

   ![Lägg till Roll tilldelnings fönster för en prenumeration](./media/role-assignments-portal/add-role-assignment.png)

1. I listrutan **Roll** väljer du rollen **Ägare**.

1. Välj en användare i listan **Välj**. Om du inte ser användaren i listan kan du ange visningsnamn och e-postadresser i rutan **Välj** om du vill söka i katalogen.

1. Klicka på **Spara** för att tilldela rollen.

   Efter en stund tilldelas rollen Ägare till användaren i prenumerationsomfånget.

## <a name="add-a-role-assignment-for-a-managed-identity-preview"></a>Lägg till en roll tilldelning för en hanterad identitet (förhands granskning)

Du kan lägga till roll tilldelningar för en hanterad identitet med hjälp av sidan **åtkomst kontroll (IAM)** enligt beskrivningen ovan i den här artikeln. När du använder sidan åtkomst kontroll (IAM) börjar du med omfånget och väljer sedan den hanterade identiteten och rollen. I det här avsnittet beskrivs ett alternativt sätt att lägga till roll tilldelningar för en hanterad identitet. Med de här stegen börjar du med den hanterade identiteten och väljer sedan omfattning och roll.

> [!IMPORTANT]
> Att lägga till en roll tilldelning för en hanterad identitet med de här alternativa stegen är för närvarande en för hands version.
> Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade.
> Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

### <a name="system-assigned-managed-identity"></a>Systemtilldelad hanterad identitet

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

   ![Lägg till Roll tilldelnings fönster för systemtilldelad hanterad identitet](./media/role-assignments-portal/add-role-assignment-with-scope.png)

1. Klicka på **Spara** för att tilldela rollen.

   Efter en liten stund tilldelas den hanterade identiteten rollen i det valda omfånget.

### <a name="user-assigned-managed-identity"></a>Användartilldelad hanterad identitet

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

   ![Lägg till Roll tilldelnings fönster för en användardefinierad hanterad identitet](./media/role-assignments-portal/add-role-assignment-with-scope.png)

1. Klicka på **Spara** för att tilldela rollen.

   Efter en liten stund tilldelas den hanterade identiteten rollen i det valda omfånget.

## <a name="remove-a-role-assignment"></a>Ta bort en rolltilldelning

I Azure RBAC tar du bort åtkomsten från en Azure-resurs genom att ta bort en roll tilldelning. Följ dessa steg om du vill ta bort en roll tilldelning.

1. Öppna **åtkomst kontroll (IAM)** i ett omfång, till exempel hanterings grupp, prenumeration, resurs grupp eller resurs, där du vill ta bort åtkomsten.

1. Klicka på fliken **Rolltilldelningar** så att du ser alla rolltilldelningar för prenumerationen.

1. Lägg till en bock intill säkerhetsobjektet med rolltilldelningen som du vil ta bort i listan med rolltilldelningar.

   ![Roll tilldelningen har marker ATS för borttagning](./media/role-assignments-portal/remove-role-assignment-select.png)

1. Klicka på **Ta bort**.

   ![Ta bort rolltilldelningsmeddelande](./media/role-assignments-portal/remove-role-assignment.png)

1. I meddelandet om att ta bort rolltilldelningen klickar du på **Ja**.

    Om du ser ett meddelande om att ärvda roll tilldelningar inte kan tas bort försöker du ta bort en roll tilldelning i ett underordnat omfång. Du bör öppna åtkomst kontroll (IAM) i omfånget där rollen har tilldelats och försöka igen. Ett snabbt sätt att öppna åtkomst kontroll (IAM) i rätt omfång är att titta i kolumnen **omfattning** och klicka på länken bredvid **(ärvd)**.

   ![Ta bort roll tilldelnings meddelande för ärvda roll tilldelningar](./media/role-assignments-portal/remove-role-assignment-inherited.png)

## <a name="next-steps"></a>Nästa steg

- [Visa en lista med Azures roll tilldelningar med hjälp av Azure Portal](role-assignments-list-portal.md)
- [Självstudie: ge en användare åtkomst till Azure-resurser med hjälp av Azure Portal](quickstart-assign-role-user-portal.md)
- [Felsöka Azure RBAC](troubleshooting.md)
- [Ordna resurser med hanteringsgrupper i Azure](../governance/management-groups/overview.md)
