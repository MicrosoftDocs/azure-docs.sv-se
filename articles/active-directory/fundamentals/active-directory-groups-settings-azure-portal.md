---
title: Redigera din grupp information – Azure Active Directory | Microsoft Docs
description: Anvisningar om hur du redigerar din grupps information med hjälp av Azure Active Directory.
services: active-directory
author: ajburnle
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: how-to
ms.date: 08/27/2018
ms.author: ajburnle
ms.reviewer: krbain
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: c346877d29c5fef03b45c895cbf8d462f90d0979
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "92369139"
---
# <a name="edit-your-group-information-using-azure-active-directory"></a>Redigera din grupp information med Azure Active Directory

Med hjälp av Azure Active Directory (Azure AD) kan du redigera en grupps inställningar, inklusive uppdatera dess namn, beskrivning eller medlemskaps typ.

## <a name="to-edit-your-group-settings"></a>Redigera dina grupp inställningar
1. Logga in på [Azure-portalen](https://portal.azure.com) med ett Globalt administratörskonto för katalogen.

2. Välj **Azure Active Directory** och välj sedan **grupper**.

    Sidan **grupper – alla grupper** visas med alla dina aktiva grupper.

3. På sidan **grupper – alla grupper** skriver du så mycket av grupp namnet som du **kan i sökrutan.** I den här artikeln söker vi efter gruppen **MDM policy-väst** .

    Sök resultaten visas under **sökrutan och** uppdateras när du skriver fler tecken.

    ![Sidan alla grupper, med söktext i sökrutan](media/active-directory-groups-settings-azure-portal/search-for-specific-group.png)

4. Välj grup **princip för MDM-väst** och välj sedan **Egenskaper** från **hanterings** ytan.

    ![Sidan grupp översikt med medlems alternativ och information markerad](media/active-directory-groups-settings-azure-portal/group-overview-blade.png)

5. Uppdatera **allmän inställnings** information efter behov, inklusive:

    ![Egenskaps inställningar för en grupp](media/active-directory-groups-settings-azure-portal/group-properties-settings.png)

    - **Grupp namn.** Redigera det befintliga grupp namnet.
    
    - **Grupp beskrivning.** Redigera den befintliga grupp beskrivningen.

    - **Grupptyp.** Du kan inte ändra typ av grupp när den har skapats. Om du vill ändra **grupp typen** måste du ta bort gruppen och skapa en ny.
    
    - **Medlemskaps typ.** Ändra medlemskaps typ. Mer information om de olika tillgängliga medlemskaps typerna finns i [så här gör du: skapa en grundläggande grupp och lägga till medlemmar med hjälp av Azure Active Directory portalen](active-directory-groups-create-azure-portal.md).
    
    - **Objekt-ID.** Du kan inte ändra objekt-ID: t, men du kan kopiera det så att det används i PowerShell-kommandon för gruppen. Mer information om hur du använder PowerShell-cmdlets finns i [Azure Active Directory-cmdletar för att konfigurera grupp inställningar](../enterprise-users/groups-settings-v2-cmdlets.md).

## <a name="next-steps"></a>Nästa steg
Dessa artiklar innehåller ytterligare information om Azure Active Directory.

- [Visa dina grupper och medlemmar](active-directory-groups-view-azure-portal.md)

- [Skapa en basgrupp och lägg till medlemmar](active-directory-groups-create-azure-portal.md)

- [Lägga till eller ta bort medlemmar från en grupp](active-directory-groups-members-azure-portal.md)

- [Hantera dynamiska regler för användare i en grupp](../enterprise-users/groups-create-rule.md)

- [Hantera medlemskap i en grupp](active-directory-groups-membership-azure-portal.md)

- [Hantera åtkomst till resurser med grupper](active-directory-manage-groups.md)

- [Associera eller lägg till en Azure-prenumeration till Azure Active Directory](active-directory-how-subscriptions-associated-directory.md)
