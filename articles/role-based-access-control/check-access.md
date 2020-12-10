---
title: Snabb start – kontrol lera åtkomsten för en användare till Azure-resurser – Azure RBAC
description: I den här snabb starten får du lära dig att kontrol lera åtkomsten för dig själv eller en annan användare till Azure-resurser med hjälp av Azure Portal och rollbaserad åtkomst kontroll i Azure (Azure RBAC).
services: role-based-access-control
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.topic: quickstart
ms.workload: identity
ms.date: 12/09/2020
ms.author: rolyon
ms.custom: contperfq2
ms.openlocfilehash: 8036bd300522000902789db59f8bebae14fedf10
ms.sourcegitcommit: 273c04022b0145aeab68eb6695b99944ac923465
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/10/2020
ms.locfileid: "97007331"
---
# <a name="quickstart-check-access-for-a-user-to-azure-resources"></a>Snabb start: kontrol lera åtkomsten för en användare till Azure-resurser

Ibland behöver du kontrol lera vilken åtkomst en användare har till en uppsättning Azure-resurser. Du kontrollerar deras åtkomst genom att ange deras tilldelningar. Ett snabbt sätt att kontrol lera åtkomsten för en enskild användare är att använda funktionen **kontrol lera åtkomst** på sidan **åtkomst kontroll (IAM)** .

## <a name="step-1-open-the-azure-resources"></a>Steg 1: öppna Azure-resurserna

Om du vill kontrol lera åtkomsten för en användare måste du först öppna de Azure-resurser som du vill kontrol lera åtkomsten för. Azure-resurser är ordnade i nivåer som vanligt vis kallas för *omfånget*. I Azure kan du ange ett omfång på fyra nivåer från breda till smala: hanterings grupp, prenumeration, resurs grupp och resurs.

![Omfattnings nivåer för Azure RBAC](../../includes/role-based-access-control/media/scope-levels.png)

Följ de här stegen för att öppna uppsättningen med Azure-resurser som du vill kontrol lera åtkomsten för.

1. Öppna [Azure-portalen](https://portal.azure.com).

1. Öppna uppsättningen Azure-resurser, till exempel **hanterings grupper**, **prenumerationer**, **resurs grupper** eller en viss resurs.

1. Klicka på den aktuella resursen i det omfånget.

    I följande exempel visas en resurs grupp.

    ![Översikt över resurs grupp](./media/check-access/rg-overview.png)

## <a name="step-2-check-access-for-a-user"></a>Steg 2: kontrol lera åtkomsten för en användare

Följ dessa steg om du vill kontrol lera åtkomsten för en enskild användare, grupp, tjänstens huvud namn eller hanterad identitet till de tidigare valda Azure-resurserna.

1. Klicka på **Åtkomstkontroll (IAM)**.

    Följande visar ett exempel på sidan åtkomst kontroll (IAM) för en resurs grupp.

    ![Åtkomst kontroll för resurs grupp-kontrol lera fliken åtkomst](./media/check-access/rg-access-control.png)

1. På fliken **kontrol lera åtkomst** går du till listan **Sök** och väljer den användare, grupp, tjänstens huvud namn eller hanterade identitet som du vill kontrol lera åtkomsten för.

1. I sökrutan anger du en sträng för att söka i katalogen efter visningsnamn, e-postadresser eller objektidentifierare.

    ![Välj lista för Kontrollera åtkomst](./media/shared/rg-check-access-select.png)

1. Klicka på säkerhetsprincipen för att öppna fönsterrutan **tilldelningar**.

    I det här fönstret kan du se åtkomsten för det valda säkerhets objektet i det här omfånget och ärvts till det här omfånget. Tilldelningar i underordnade omfattningar visas inte. Följande tilldelningar visas:

    - Roll tilldelningar som har lagts till med Azure RBAC.
    - Neka tilldelningar som har lagts till med Azure-ritningar eller Azure-hanterade appar.
    - Klassisk tjänst administratör eller Co-Administrator tilldelningar för klassiska distributioner. 

    ![Fönstret roller och neka tilldelningar för en användare](./media/shared/rg-check-access-assignments-user.png)

## <a name="step-3-check-your-access"></a>Steg 3: kontrol lera åtkomsten

Följ de här stegen för att kontrol lera åtkomsten till de tidigare valda Azure-resurserna.

1. Klicka på **Åtkomstkontroll (IAM)**.

1. På fliken **kontrol lera åtkomst** klickar du på knappen **Visa min åtkomst** .

    Fönstret tilldelningar visas med en lista över din åtkomst i det här omfånget och ärvts till det här omfånget. Tilldelningar i underordnade omfattningar visas inte.

    ![Fönstret roll och neka tilldelningar](./media/check-access/rg-check-access-assignments.png)

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Visa en lista med Azures roll tilldelningar med hjälp av Azure Portal](role-assignments-list-portal.md)
