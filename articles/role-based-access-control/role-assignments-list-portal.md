---
title: Visa en lista med Azures roll tilldelningar med Azure Portal – Azure RBAC
description: Lär dig hur du avgör vilka resurser användare, grupper, tjänstens huvud namn eller hanterade identiteter har åtkomst till med hjälp av Azure Portal och rollbaserad åtkomst kontroll i Azure (Azure RBAC).
services: active-directory
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.topic: how-to
ms.workload: identity
ms.date: 12/09/2020
ms.author: rolyon
ms.openlocfilehash: 06a9a955e8a963f94b1885abf5a920cb96c01940
ms.sourcegitcommit: 273c04022b0145aeab68eb6695b99944ac923465
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/10/2020
ms.locfileid: "97007486"
---
# <a name="list-azure-role-assignments-using-the-azure-portal"></a>Visa en lista med Azures roll tilldelningar med hjälp av Azure Portal

[!INCLUDE [Azure RBAC definition list access](../../includes/role-based-access-control-definition-list.md)] Den här artikeln beskriver hur du visar roll tilldelningar med hjälp av Azure Portal.

> [!NOTE]
> Om din organisation har funktioner som har hanterats av en tjänst leverantör som använder [Azure-delegerad resurs hantering](../lighthouse/concepts/azure-delegated-resource-management.md), visas inte roll tilldelningar som har auktoriserats av tjänste leverantören här.

## <a name="list-role-assignments-for-a-user-or-group"></a>Lista roll tilldelningar för en användare eller grupp

Ett snabbt sätt att se de roller som tilldelats en användare eller grupp i en prenumeration är att använda fönstret **roll tilldelningar i Azure** .

1. I Azure Portal väljer du **alla tjänster** på Azure Portal-menyn.

1. Välj **Azure Active Directory** och välj sedan **användare** eller **grupper**.

1. Klicka på den användare eller grupp som du vill visa en lista över roll tilldelningarna för.

1. Klicka på **roll tilldelningar för Azure**.

    Du ser en lista över roller som har tilldelats den valda användaren eller gruppen vid olika omfång, till exempel hanterings grupp, prenumeration, resurs grupp eller resurs. Den här listan innehåller alla roll tilldelningar som du har behörighet att läsa.

    ![Rolltilldelningar för en användare](./media/role-assignments-list-portal/azure-role-assignments-user.png)    

1. Klicka på listan **prenumerationer** om du vill ändra prenumerationen.

## <a name="list-owners-of-a-subscription"></a>Lista ägare för en prenumeration

Användare som har tilldelats [ägar](built-in-roles.md#owner) rollen för en prenumeration kan hantera allt i prenumerationen. Följ dessa steg om du vill visa en lista över ägare till en prenumeration.

1. I Azure-portalen klickar du på **Alla tjänster** och sedan **Prenumerationer**.

1. Klicka på den prenumeration som du vill lista ägarna till.

1. Klicka på **Åtkomstkontroll (IAM)** .

1. Klicka på fliken **Rolltilldelningar** så att du ser alla rolltilldelningar för prenumerationen.

1. Bläddra till avsnittet **ägare** om du vill se alla användare som har tilldelats ägar rollen för den här prenumerationen.

   ![Åtkomst kontroll för prenumeration – fliken roll tilldelningar](./media/role-assignments-list-portal/sub-access-control-role-assignments-owners.png)

## <a name="list-role-assignments-at-a-scope"></a>Lista roll tilldelningar i ett omfång

1. I Azure Portal klickar du på **alla tjänster** och väljer sedan omfånget. Du kan till exempel välja **hanterings grupper**, **prenumerationer**, **resurs grupper** eller en resurs.

1. Klicka på den aktuella resursen.

1. Klicka på **Åtkomstkontroll (IAM)**.

1. Klicka på fliken **roll tilldelningar** för att visa alla roll tilldelningar i det här omfånget.

   ![Åtkomst kontroll – fliken roll tilldelningar](./media/role-assignments-list-portal/rg-access-control-role-assignments.png)

   På fliken roll tilldelningar kan du se vem som har åtkomst till det här omfånget. Observera att vissa roller är begränsade till **den här resursen** medan andra är **(Ärvda)** från ett annat omfång. Åtkomst tilldelas antingen specifikt till den här resursen eller ärvts från en tilldelning till det överordnade omfånget.

## <a name="list-role-assignments-for-a-user-at-a-scope"></a>Lista roll tilldelningar för en användare i ett omfång

Om du vill visa en lista över åtkomsten för en användare, grupp, tjänstens huvud namn eller en hanterad identitet anger du roll tilldelningarna. Följ dessa steg om du vill visa roll tilldelningarna för en enskild användare, grupp, tjänstens huvud namn eller hanterad identitet i ett visst omfång.

1. I Azure Portal klickar du på **alla tjänster** och väljer sedan omfånget. Du kan till exempel välja **hanterings grupper**, **prenumerationer**, **resurs grupper** eller en resurs.

1. Klicka på den aktuella resursen.

1. Klicka på **Åtkomstkontroll (IAM)**.

1. Klicka på fliken **Kontrollera åtkomst**.

    ![Åtkomst kontroll för resurs grupp-kontrol lera fliken åtkomst](./media/role-assignments-list-portal/rg-access-control-check-access.png)

1. I listan **Sök** väljer du den användare, grupp, tjänstens huvud namn eller hanterade identitet som du vill kontrol lera åtkomsten för.

1. I sökrutan anger du en sträng för att söka i katalogen efter visningsnamn, e-postadresser eller objektidentifierare.

    ![Välj lista för Kontrollera åtkomst](./media/shared/rg-check-access-select.png)

1. Klicka på säkerhetsprincipen för att öppna fönsterrutan **tilldelningar**.

    I det här fönstret kan du se åtkomsten för det valda säkerhets objektet i det här omfånget och ärvts till det här omfånget. Tilldelningar i underordnade omfattningar visas inte. Följande tilldelningar visas:

    - Roll tilldelningar som har lagts till med Azure RBAC.
    - Neka tilldelningar som har lagts till med Azure-ritningar eller Azure-hanterade appar.
    - Klassisk tjänst administratör eller Co-Administrator tilldelningar för klassiska distributioner. 

    ![fönsterrutan tilldelningar](./media/shared/rg-check-access-assignments-user.png)

## <a name="list-role-assignments-for-a-managed-identity"></a>Lista roll tilldelningar för en hanterad identitet

Du kan lista roll tilldelningar för systemtilldelade och användarspecifika hanterade identiteter i ett visst omfång genom att använda bladet **åtkomst kontroll (IAM)** enligt beskrivningen ovan. I det här avsnittet beskrivs hur du visar roll tilldelningar för bara den hanterade identiteten.

### <a name="system-assigned-managed-identity"></a>Systemtilldelad hanterad identitet

1. I Azure Portal öppnar du en systemtilldelad hanterad identitet.

1. Klicka på **identitet** i den vänstra menyn.

    ![Systemtilldelad hanterad identitet](./media/shared/identity-system-assigned.png)

1. Under **behörigheter** klickar du på **roll tilldelningar för Azure**.

    Du ser en lista över roller som har tilldelats den valda systemtilldelade hanterade identiteten vid olika omfång, till exempel hanterings grupp, prenumeration, resurs grupp eller resurs. Den här listan innehåller alla roll tilldelningar som du har behörighet att läsa.

    ![Roll tilldelningar för en systemtilldelad hanterad identitet](./media/shared/role-assignments-system-assigned.png)

1. Klicka på **prenumerations** listan om du vill ändra prenumerationen.

### <a name="user-assigned-managed-identity"></a>Användartilldelad hanterad identitet

1. Öppna en användardefinierad hanterad identitet i Azure Portal.

1. Klicka på **roll tilldelningar för Azure**.

    Du ser en lista över roller som har tilldelats den valda användarspecifika hanterade identiteten vid olika omfång, till exempel hanterings grupp, prenumeration, resurs grupp eller resurs. Den här listan innehåller alla roll tilldelningar som du har behörighet att läsa.

    ![Skärm bild som visar roll tilldelningar för en användardefinierad hanterad identitet.](./media/shared/role-assignments-user-assigned.png)

1. Klicka på **prenumerations** listan om du vill ändra prenumerationen.

## <a name="list-number-of-role-assignments"></a>Lista antal roll tilldelningar

Du kan ha upp till **2000** roll tilldelningar i varje prenumeration. Den här gränsen omfattar roll tilldelningar i prenumerations-, resurs grupps-och resurs omfång. För att hjälpa dig att hålla koll på den här gränsen innehåller fliken **roll tilldelningar** ett diagram som visar antalet roll tilldelningar för den aktuella prenumerationen.

![Åtkomst kontroll-antal roll tilldelnings diagram](./media/role-assignments-list-portal/access-control-role-assignments-chart.png)

Om du får nära det högsta antalet och du försöker lägga till fler roll tilldelningar visas en varning i fönstret **Lägg till roll tilldelning** . Information om hur du kan minska antalet roll tilldelningar finns i [Felsöka Azure RBAC](troubleshooting.md#azure-role-assignments-limit).

![Åtkomst kontroll – Lägg till Roll tilldelnings varning](./media/role-assignments-list-portal/add-role-assignment-warning.png)

## <a name="download-role-assignments"></a>Hämta roll tilldelningar

Du kan hämta roll tilldelningar i ett omfång i CSV-eller JSON-format. Detta kan vara användbart om du behöver granska listan i ett kalkyl blad eller göra en inventering när du migrerar en prenumeration.

När du hämtar roll tilldelningar bör du tänka på följande kriterier:

- Om du inte har behörighet att läsa katalogen, till exempel katalog läsar rollen, kommer kolumnerna DisplayName, SignInName och ObjectType att vara tomma.
- Roll tilldelningar vars säkerhets objekt har tagits bort ingår inte.
- Åtkomst beviljas till klassiska administratörer ingår inte.

Följ dessa steg om du vill hämta roll tilldelningar i ett omfång.

1. I Azure Portal klickar du på **alla tjänster** och väljer sedan det omfång där du vill hämta roll tilldelningarna. Du kan till exempel välja **hanterings grupper**, **prenumerationer**, **resurs grupper** eller en resurs.

1. Klicka på den aktuella resursen.

1. Klicka på **Åtkomstkontroll (IAM)**.

1. Klicka på **Hämta roll tilldelningar** för att öppna fönstret Hämta roll tilldelningar.

    ![Åtkomst kontroll – Hämta roll tilldelningar](./media/role-assignments-list-portal/download-role-assignments.png)

1. Använd kryss rutorna för att välja de roll tilldelningar som du vill ta med i den nedladdade filen.

    - **Ärvd** – ta med ärvda roll tilldelningar för det aktuella omfånget.
    - **I aktuellt omfång** – inkludera roll tilldelningar för det aktuella omfånget.
    - **Underordnade** – inkludera roll tilldelningar på nivåer under det aktuella omfånget. Den här kryss rutan är inaktive rad för hanterings grupp omfånget.

1. Välj fil formatet, som kan vara kommaavgränsade värden (CSV) eller JavaScript Object Notation (JSON).

1. Ange fil namnet.

1. Starta nedladdningen genom att klicka på **Starta** .

    Följande visar exempel på utdata för varje fil format.

    ![Hämta roll tilldelningar som CSV](./media/role-assignments-list-portal/download-role-assignments-csv.png)

    ![Skärm bild av de nedladdade roll tilldelningarna som i JSON-format.](./media/role-assignments-list-portal/download-role-assignments-json.png)

## <a name="next-steps"></a>Nästa steg

- [Lägga till eller ta bort rolltilldelningar för Azure med hjälp av Azure-portalen](role-assignments-portal.md)
- [Felsöka Azure RBAC](troubleshooting.md)
