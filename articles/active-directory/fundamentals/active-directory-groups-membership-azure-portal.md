---
title: Lägga till eller ta bort en grupp från en annan grupp – Azure AD
description: Anvisningar om hur du lägger till eller tar bort en grupp från en annan grupp med hjälp av Azure Active Directory.
services: active-directory
author: ajburnle
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: how-to
ms.date: 10/19/2018
ms.author: ajburnle
ms.custom: it-pro, seodec18
ms.reviewer: krbain
ms.collection: M365-identity-device-management
ms.openlocfilehash: cbd7eecd80b528b1d20d016d51c985bc24d1301d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "92371808"
---
# <a name="add-or-remove-a-group-from-another-group-using-azure-active-directory"></a>Lägg till eller ta bort en grupp från en annan grupp med hjälp av Azure Active Directory
Den här artikeln hjälper dig att lägga till och ta bort en grupp från en annan grupp med hjälp av Azure Active Directory.

>[!Note]
>Om du försöker ta bort den överordnade gruppen, se [så här uppdaterar eller tar du bort en grupp och dess medlemmar](active-directory-groups-delete-group.md).

## <a name="add-a-group-to-another-group"></a>Lägg till en grupp i en annan grupp
Du kan lägga till en befintlig säkerhets grupp till en annan befintlig säkerhets grupp (kallas även kapslade grupper) och skapa en medlems grupp (under grupp) och en överordnad grupp. Medlems gruppen ärver attribut och egenskaper för den överordnade gruppen, vilket sparar konfigurations tiden.

>[!Important]
>Vi stöder för närvarande inte:<ul><li>Lägga till grupper i en grupp som har synkroniserats med lokala Active Directory.</li><li>Lägga till säkerhets grupper i Microsoft 365 grupper.</li><li>Lägga till Microsoft 365 grupper i säkerhets grupper eller andra Microsoft 365 grupper.</li><li>Tilldela appar till kapslade grupper.</li><li>Tillämpa licenser på kapslade grupper.</li><li>Lägga till distributions grupper i kapslings scenarier.</li></ul>

### <a name="to-add-a-group-as-a-member-of-another-group"></a>Lägga till en grupp som medlem i en annan grupp

1. Logga in på [Azure-portalen](https://portal.azure.com) med ett Globalt administratörskonto för katalogen.

2. Välj **Azure Active Directory** och välj sedan **grupper**.

3. På sidan **grupper – alla grupper** söker du efter och väljer den grupp som ska bli medlem i en annan grupp. I den här övningen använder vi gruppen **MDM policy – västra** .

    >[!Note]
    >Du kan lägga till gruppen som en medlem i en enda grupp i taget. Dessutom filtrerar rutan **Välj grupp** visningen baserat på matchning av posten till någon del av ett användar-eller enhets namn. Jokertecken stöds dock inte.

    ![Grupper – sidan alla grupper med MDM-princip – västra gruppen vald](media/active-directory-groups-membership-azure-portal/group-all-groups-screen.png)

4. På sidan **MDM-princip – väst-grupp medlemskap** väljer du **grupp medlemskap**, väljer **Lägg till**, letar upp den grupp som du vill att din grupp ska vara medlem i och väljer sedan **Välj**. I den här övningen använder vi **MDM-principen – alla organisations** grupper.

    **MDM-principen – västra** gruppen är nu medlem i **MDM-policyn – alla org-** grupper, och ärver alla egenskaper och all konfiguration för MDM-principen – alla org-grupper.

    ![Skapa ett grupp medlemskap genom att lägga till en grupp i en annan grupp](media/active-directory-groups-membership-azure-portal/group-add-group-membership.png)

5. Granska sidan **MDM-princip – väst-grupp medlemskap** för att se relationen mellan gruppen och medlemmen.

6. Om du vill ha en mer detaljerad vy av gruppen och medlems relationen väljer du grupp namnet (**MDM-princip – alla org**) och ta en titt på sidan **MDM-princip – väst-** information.

## <a name="remove-a-group-from-another-group"></a>Ta bort en grupp från en annan grupp
Du kan ta bort en befintlig säkerhets grupp från en annan säkerhets grupp. Men om du tar bort gruppen tas även ärvda attribut och egenskaper bort för dess medlemmar.

### <a name="to-remove-a-member-group-from-another-group"></a>Ta bort en medlems grupp från en annan grupp
1. På sidan **grupper – alla grupper** söker du efter och väljer den grupp som ska tas bort som medlem i en annan grupp. I den här övningen använder vi gruppen **MDM policy-väst** .

2. På **översikts sidan för MDM-policyn** väljer du **grupp medlemskap**.

3. Välj **MDM-principen – alla org** grupper från sidan **MDM-princip – västra-grupp medlemskap** och välj sedan **ta bort** från **MDM-principens** sid information.

    ![Sidan grupp medlemskap visar både medlemmen och grupp informationen](media/active-directory-groups-membership-azure-portal/group-membership-remove.png)

## <a name="additional-information"></a>Ytterligare information
Dessa artiklar innehåller ytterligare information om Azure Active Directory.

- [Visa dina grupper och medlemmar](active-directory-groups-view-azure-portal.md)

- [Skapa en basgrupp och lägg till medlemmar](active-directory-groups-create-azure-portal.md)

- [Lägga till eller ta bort medlemmar från en grupp](active-directory-groups-members-azure-portal.md)

- [Redigera dina gruppinställningar](active-directory-groups-settings-azure-portal.md)

- [Använd en grupp för att hantera åtkomst till SaaS-program](../enterprise-users/groups-saasapps.md)

- [Scenarier, begränsningar och kända problem med grupper för att hantera licensiering i Azure Active Directory](../enterprise-users/licensing-group-advanced.md#limitations-and-known-issues)
