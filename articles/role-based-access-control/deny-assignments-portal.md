---
title: Visa en lista över Azure Deny-tilldelningar med hjälp av Azure Portal – Azure RBAC
description: Lär dig hur du visar en lista över användare, grupper, tjänstens huvud namn och hanterade identiteter som har nekats åtkomst till specifika Azure-resurs åtgärder i vissa områden med hjälp av Azure Portal och rollbaserad åtkomst kontroll i Azure (Azure RBAC).
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: 8078f366-a2c4-4fbb-a44b-fc39fd89df81
ms.service: role-based-access-control
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/10/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 92046b3a944a747ce76d2426855eec7b6bc2cd70
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "84790254"
---
# <a name="list-azure-deny-assignments-using-the-azure-portal"></a>Visa en lista över Azure Deny-tilldelningar med hjälp av Azure Portal

[Azure Deny-tilldelningar](deny-assignments.md) blockerar användare från att utföra vissa åtgärder för Azure-resurser även om en roll tilldelning ger åtkomst till dem. Den här artikeln beskriver hur du visar en lista över nekade tilldelningar med hjälp av Azure Portal.

> [!NOTE]
> Du kan inte skapa egna neka-tilldelningar direkt. För information om hur neka-tilldelningar skapas, se [Azure Deny-tilldelningar](deny-assignments.md).

## <a name="prerequisites"></a>Förutsättningar

För att få information om en neka-tilldelning måste du ha:

- `Microsoft.Authorization/denyAssignments/read` behörighet, som ingår i de flesta [inbyggda Azure-roller](built-in-roles.md).

## <a name="list-deny-assignments"></a>Visa lista över nekade tilldelningar

Följ dessa steg om du vill visa en lista över neka tilldelningar i prenumerations-eller hanterings gruppens omfång

1. Klicka på **alla tjänster** i Azure Portal och sedan på **hanterings grupper** eller **prenumerationer**.

1. Klicka på den hanterings grupp eller prenumeration som du vill visa.

1. Klicka på **Åtkomstkontroll (IAM)**.

1. Klicka på fliken **neka tilldelningar** (eller klicka på knappen **Visa** på panelen Visa neka-tilldelningar).

    Om det finns några nekande tilldelningar i det här omfånget eller som ärvts till det här omfånget visas de.

    ![Åtkomst kontroll – fliken neka tilldelningar](./media/deny-assignments-portal/access-control-deny-assignments.png)

1. Klicka på **Redigera kolumner** om du vill visa fler kolumner.

    ![Neka tilldelningar – kolumner](./media/deny-assignments-portal/deny-assignments-columns.png)

    |  |  |
    | --- | --- |
    | **Namn** | Namnet på neka-tilldelningen. |
    | **Typ av huvudkonto** | Användare, grupp, systemdefinierad grupp eller tjänstens huvud namn. |
    | **Nekad**  | Namnet på det säkerhets objekt som ingår i neka-tilldelningen. |
    | **Identitet** | Unik identifierare för neka-tilldelning. |
    | **Exkluderade objekt** | Om det finns säkerhets objekt som är undantagna från neka-tilldelningen. |
    | **Gäller inte för underordnade** | Om neka-tilldelningen ärvs till under områden. |
    | **Systemskyddat** | Om neka-tilldelningen hanteras av Azure. Är för närvarande alltid ja. |
    | **Omfång** | Hanterings grupp, prenumeration, resurs grupp eller resurs. |

1. Lägg till en bock till något av de aktiverade objekten och klicka sedan på **OK** för att visa de markerade kolumnerna.

## <a name="list-details-about-a-deny-assignment"></a>Visa information om en neka-tilldelning

Följ dessa steg om du vill visa mer information om en neka-tilldelning.

1. Öppna fönstret **neka tilldelningar** enligt beskrivningen i föregående avsnitt.

1. Klicka på namnet på neka tilldelning för att öppna bladet **användare** .

    ![Neka tilldelning – användare](./media/deny-assignments-portal/deny-assignment-users.png)

    Bladet **användare** innehåller följande två avsnitt.

    |  |  |
    | --- | --- |
    | **Neka tilldelning gäller**  | Säkerhets objekt som neka-tilldelningen gäller för. |
    | **Neka tilldelning exkluderar** | Säkerhets objekt som undantas från neka-tilldelning. |

    **Systemdefinierat huvud konto** representerar alla användare, grupper, tjänstens huvud namn och hanterade identiteter i en Azure AD-katalog.

1. Om du vill se en lista över de behörigheter som nekas klickar du på **nekade behörigheter**.

    ![Neka tilldelning-nekade behörigheter](./media/deny-assignments-portal/deny-assignment-denied-permissions.png)

    | Åtgärdstyp | Beskrivning |
    | --- | --- |
    | **Åtgärder**  | Nekade hanterings åtgärder. |
    | **NotActions** | Hanterings åtgärder som undantas från nekad hanterings åtgärd. |
    | **DataActions**  | Nekade data åtgärder. |
    | **NotDataActions** | Data åtgärder som uteslutits från nekad data åtgärd. |

    I exemplet som visas i föregående skärm bild är följande de gällande behörigheterna:

    - Alla lagrings åtgärder på data planet nekas, förutom för beräknings åtgärder.

1. Klicka på **Egenskaper** för att se egenskaperna för en neka-tilldelning.

    ![Neka tilldelning – egenskaper](./media/deny-assignments-portal/deny-assignment-properties.png)

    På bladet **Egenskaper** kan du se namn, ID, beskrivning och omfattning för neka-tilldelning. Växeln **gäller inte för underordnade** växlar anger om neka-tilldelningen ärvs till under områden. Växeln **system Protected** anger om den här nekande tilldelningen hanteras av Azure. För närvarande är detta **Ja** i samtliga fall.

## <a name="next-steps"></a>Nästa steg

* [Förstå Azure Deny-tilldelningar](deny-assignments.md)
* [Visa en lista över Azure neka-tilldelningar med Azure PowerShell](deny-assignments-powershell.md)
