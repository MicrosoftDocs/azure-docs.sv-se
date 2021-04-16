---
title: Tilldela Azure AD-roller till användare – Azure Active Directory | Microsoft Docs
description: Anvisningar om hur du tilldelar administratörs- och icke-administratörsroller till användare med Azure Active Directory.
services: active-directory
author: ajburnle
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: how-to
ms.date: 08/31/2020
ms.author: ajburnle
ms.reviewer: jeffsta
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: f49e15984b7a673de1e7d1607f4802c17ebef4e2
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2021
ms.locfileid: "107531846"
---
# <a name="assign-administrator-and-non-administrator-roles-to-users-with-azure-active-directory"></a>Tilldela administratörs- och icke-administratörsroller till användare med Azure Active Directory

Om Azure Active Directory (Azure AD) behöver en av användarna behörighet att hantera Azure AD-resurser måste du tilldela dem en roll som ger de behörigheter som de behöver. Information om vilka roller som hanterar Azure-resurser och vilka roller som hanterar Azure AD-resurser finns i Administratörsroller för klassiska [prenumerationer, Azure-roller och Azure AD-roller.](../../role-based-access-control/rbac-and-directory-admin-roles.md)

Mer information om tillgängliga Azure AD-roller finns i [Tilldela administratörsroller i Azure Active Directory](../roles/permissions-reference.md). Information om hur du lägger till [användare finns i Lägga till nya användare i Azure Active Directory](add-users-azure-active-directory.md).

## <a name="assign-roles"></a>Tilldela roller

Ett vanligt sätt att tilldela Azure AD-roller till en användare är på **sidan Tilldelade roller** för en användare. Du kan också konfigurera användarberättigandet att utökas just-in-time till en roll med hjälp av Privileged Identity Management (PIM). Mer information om hur du använder PIM finns i [Privileged Identity Management](../privileged-identity-management/index.yml).

Om en katalogroll tilldelas till en gästanvändare beviljas gästanvändaren ytterligare behörigheter som ingår i rollen, inklusive grundläggande läsbehörigheter. Se [Inbyggda roller i Azure AD.](https://docs.microsoft.com/azure/active-directory/roles/permissions-reference)

> [!Note]
> Om du har en Azure AD Premium P2-licensplan och redan använder PIM utförs alla [rollhanteringsuppgifter i Privileged Identity Management miljö.](../roles/manage-roles-portal.md) Den här funktionen är för närvarande begränsad till att endast tilldela en roll i taget. Du kan för närvarande inte välja flera roller och tilldela dem till en användare på samma gång.
>
> ![Azure AD-roller som hanteras i PIM för användare som redan använder PIM och har en Premium P2-licens](./media/active-directory-users-assign-role-azure-portal/pim-manages-roles-for-p2.png)

## <a name="assign-a-role-to-a-user"></a>Tilldela en användare en roll

1. Gå till [Azure Portal](https://portal.azure.com/) och logga in med ett Global administratör-konto för katalogen.

2. Sök efter och välj **Azure Active Directory**.

      ![Azure Portal söka efter Azure Active Directory](media/active-directory-users-assign-role-azure-portal/search-azure-active-directory.png)

3. Välj **Användare**.

4. Sök efter och välj den användare som får rolltilldelningen. Till exempel _Alain Charon_.

      ![Sidan Alla användare – välj användaren](media/active-directory-users-assign-role-azure-portal/directory-role-select-user.png)

5. På sidan **Alain Charon – Profil** väljer du **Tilldelade roller**.

    Sidan **Alain Charon – Administrativa roller** visas.

6. Välj **Lägg till tilldelningar,** välj den roll som du vill tilldela till Alain _(till exempel Programadministratör_) och välj sedan **Välj**.

    ![Sidan Tilldelade roller – visar den valda rollen](media/active-directory-users-assign-role-azure-portal/directory-role-select-role.png)

    Rollen Programadministratör tilldelas till Alain Charon och visas på sidan Administrativa roller för **Alain Charon.**

## <a name="remove-a-role-assignment"></a>Ta bort en rolltilldelning

Om du behöver ta bort rolltilldelningen från en användare kan du också göra det från sidan Administrativa roller för **Alain Charon** .

### <a name="to-remove-a-role-assignment-from-a-user"></a>Ta bort en rolltilldelning från en användare

1. Välj **Azure Active Directory**, välj **Användare** och sök sedan efter och välj den användare som får rolltilldelningen borttagen. Till exempel _Alain Charon_.

2. Välj **Tilldelade roller,** välj **Programadministratör** och välj sedan Ta **bort tilldelning.**

    ![Sidan Tilldelade roller, som visar den valda rollen och alternativet Ta bort](media/active-directory-users-assign-role-azure-portal/directory-role-remove-role.png)

    Rollen Programadministratör bort från Alain Charon och visas inte längre på sidan Administrativa roller för **Alain Charon.**

## <a name="next-steps"></a>Nästa steg

- [Lägga till eller ta bort användare](add-users-azure-active-directory.md)

- [Lägga till eller ändra profilinformation](active-directory-users-profile-azure-portal.md)

- [Lägga till gästanvändare från annan katalog](../external-identities/what-is-b2b.md)

Andra användarhanteringsuppgifter som du kan läsa finns i Azure Active Directory [användarhanteringsdokumentationen](../enterprise-users/index.yml).
