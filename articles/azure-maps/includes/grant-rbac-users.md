---
title: Bevilja rollbaserad åtkomst för användare
titleSuffix: Azure Maps
description: Använd rollbaserad åtkomst kontroll för att bevilja användare tillstånd att Azure Maps
author: anastasia-ms
ms.author: v-stharr
ms.date: 06/17/2020
ms.topic: include
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: 234c3358b98b7af677793fba58c1602a8bd976f0
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/18/2021
ms.locfileid: "101102826"
---
## <a name="grant-role-based-access-for-users-to-azure-maps"></a>Bevilja rollbaserad åtkomst för användare att Azure Maps

Du beviljar *Azure rollbaserad åtkomst kontroll (Azure RBAC)* genom att tilldela antingen en Azure AD-grupp eller säkerhets objekt till en eller flera Azure Maps roll definitioner. Om du vill visa de roll definitioner för Azure som är tillgängliga för Azure Maps går du till **åtkomst kontroll (IAM)**. Välj **roller** och Sök sedan efter roller som börjar med *Azure Maps*.

* För att effektivt hantera en stor mängd användares åtkomst till Azure Maps, se [Azure AD-grupper](../../active-directory/fundamentals/active-directory-manage-groups.md).
* För att användarna ska kunna autentisera till programmet måste användarna skapas i Azure AD. Se [Lägg till eller ta bort användare med Azure AD](../../active-directory/fundamentals/add-users-azure-active-directory.md).

Läs mer i [Azure AD](../../active-directory/fundamentals/index.yml) för att effektivt hantera en katalog för användare.

1. Gå till ditt **Azure Maps-konto**. Välj roll tilldelningen **åtkomst kontroll (IAM)**  >  .

    ![Bevilja åtkomst med Azure RBAC](../media/how-to-manage-authentication/how-to-grant-rbac.png)

2. På fliken **roll tilldelningar** under **roll** väljer du en inbyggd Azure Maps roll definition som **Azure Maps data läsare** eller **Azure Maps data deltagare**. Under **tilldela åtkomst till** väljer du **Azure AD-användare, grupp eller tjänstens huvud namn**. Välj huvudobjektet efter namn. Välj sedan **Spara**.

   * Se information om att [tilldela Azure-roller](../../role-based-access-control/role-assignments-portal.md).

> [!WARNING]
> Azure Maps inbyggda roll definitioner ger en mycket stor behörighet till många Azure Maps REST-API: er. Om du vill begränsa API: erna för användare till ett minimum, se [skapa en anpassad roll definition och tilldela användare](../../role-based-access-control/custom-roles.md) till den anpassade roll definitionen. Detta gör det möjligt för användarna att ha den minsta behörighet som krävs för programmet.