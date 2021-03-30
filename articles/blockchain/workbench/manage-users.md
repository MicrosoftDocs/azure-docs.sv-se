---
title: Hantera användare i Azure blockchain Workbench
description: Hantera användare i Azure blockchain Workbench.
ms.date: 07/15/2020
ms.topic: how-to
ms.reviewer: ravastra
ms.openlocfilehash: be078e7149ba008ab8b7ad44a016a0d1cf82df0e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "91263019"
---
# <a name="manage-users-in-azure-blockchain-workbench"></a>Hantera användare i Azure blockchain Workbench

Azure blockchain Workbench innehåller användar hantering för personer och organisationer som ingår i ditt konsortium.

## <a name="prerequisites"></a>Förutsättningar

En blockchain Workbench-distribution krävs. Se [Azure blockchain Workbench-distribution](deploy.md) för mer information om distribution.

## <a name="add-azure-ad-users"></a>Lägg till Azure AD-användare

Azure blockchain Workbench använder Azure Active Directory (Azure AD) för autentisering, åtkomst kontroll och roller. Användare i blockchain Workbench Azure AD-klienten kan autentisera och använda blockchain Workbench. Lägg till användare i rollen administratörs program för att interagera och utföra åtgärder.

Blockchain Workbench-användare måste finnas i Azure AD-klienten innan du kan tilldela dem till program och roller. Använd följande steg för att lägga till användare i Azure AD:

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Välj ditt konto i det övre högra hörnet och växla till Azure AD-klienten som är associerad med blockchain Workbench.
1. Välj **Azure Active Directory > användare**. Du ser en lista över användare i din katalog.
1. Om du vill lägga till användare i katalogen väljer du **ny användare**. För externa användare väljer du **ny gäst användare**.
1. Fyll i de obligatoriska fälten för den nya användaren. Välj **Skapa**.

Besök [Azure AD](../../active-directory/fundamentals/add-users-azure-active-directory.md) -dokumentationen om du vill ha mer information om hur du hanterar användare i Azure AD.

## <a name="manage-blockchain-workbench-administrators"></a>Hantera blockchain Workbench-administratörer

När användarna har lagts till i katalogen är nästa steg att välja vilka användare som är blockchain Workbench-administratörer. Användare i gruppen **Administratörer** är kopplade till **rollen administratörs program** i blockchain Workbench. Administratörer kan lägga till eller ta bort användare, tilldela användare till vissa scenarier och skapa nya program.

Lägga till användare i gruppen **Administratörer** i Azure AD-katalogen:

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Kontrol lera att du är i Azure AD-klienten som är associerad med blockchain Workbench genom att välja ditt konto i det övre högra hörnet.
1. Välj **Azure Active Directory > företags program**.
1. Ändra List Rute filter för **program typ** till **alla program** och välj **Använd**.
1. Välj Azure AD-klientprogrammet för Azure blockchain Workbench

    ![Alla företags program registreringar](./media/manage-users/select-blockchain-client-app.png)

1. Välj **användare och grupper > Lägg till användare**.
1. I **Lägg till tilldelning** väljer du **Användare**. Välj eller Sök efter den användare som du vill lägga till som administratör. Klicka på **Välj** när du är färdig med valet.

    ![Lägg till tilldelning](./media/manage-users/add-user-assignment.png)

1. Verifiera att **rollen** har angetts till **administratör**
1. Välj **Tilldela**. De tillagda användarna visas i listan med rollen administratör tilldelad.

    ![Blockchain klient program användare](./media/manage-users/blockchain-admin-list.png)

## <a name="managing-blockchain-workbench-members"></a>Hantera blockchain Workbench-medlemmar

Använd blockchain Workbench-programmet för att hantera användare och organisationer som ingår i ditt konsortium. Du kan lägga till eller ta bort användare i program och roller.

1. [Öppna blockchain Workbench](deploy.md#blockchain-workbench-web-url) i webbläsaren och logga in som administratör.

    ![Blockchain Workbench](./media/manage-users/blockchain-workbench-applications.png)

    Medlemmar läggs till i varje program. Medlemmar kan ha en eller flera program roller för att initiera kontrakt eller vidta åtgärder.

1. Om du vill hantera medlemmar för ett program väljer du en fönster panel i fönstret **program** .

    Antalet medlemmar som är associerade med det valda programmet visas på panelen medlemmar.

    ![Välj program](./media/manage-users/blockchain-workbench-select-application.png)


#### <a name="add-member-to-application"></a>Lägg till medlem i program

1. Välj medlems Panelen för att visa en lista över aktuella medlemmar.
1. Välj **Lägg till medlemmar**.

    ![Skärm bild som visar fönstret program medlemskap med knappen Lägg till en medlem markerad.](./media/manage-users/application-add-members.png)

1. Sök efter användarens namn.  Endast Azure AD-användare som finns i Blockchain Workbench-klientorganisationen visas. Om användaren inte hittas måste du [lägga till Azure AD-användare](#add-azure-ad-users).

    ![Lägg till medlemmar](./media/manage-users/find-user.png)

1. Välj en **roll** i list rutan.

    ![Välj roll medlemmar](./media/manage-users/application-select-role.png)

1. Välj **Lägg till** för att lägga till medlemmen med den tillhörande rollen i programmet.

#### <a name="remove-member-from-application"></a>Ta bort medlem från program

1. Välj medlems Panelen för att visa en lista över aktuella medlemmar.
1. Välj **ta bort** från List rutan roll för den användare som du vill ta bort.

    ![Ta bort medlem](./media/manage-users/application-remove-member.png)

#### <a name="change-or-add-role"></a>Ändra eller Lägg till roll

1. Välj medlems Panelen för att visa en lista över aktuella medlemmar.
1. För den användare som du vill ändra klickar du på list rutan och väljer den nya rollen.

    ![Ändra roll](./media/manage-users/application-change-role.png)

## <a name="next-steps"></a>Nästa steg

I den här instruktions artikeln har du lärt dig hur du hanterar användare för Azure blockchain Workbench. Om du vill lära dig hur du skapar ett blockchain-program fortsätter du till nästa instruktions artikel.

> [!div class="nextstepaction"]
> [Skapa ett blockkedjeprogram i Azure Blockchain Workbench](create-app.md)
