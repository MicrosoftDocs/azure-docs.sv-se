---
title: Identifiering och insikter för Azure AD-roller (för hands version) i Privileged Identity Management tidigare säkerhets guide – Azure Active Directory
description: Identifiering och insikter (tidigare säkerhets guide) hjälper dig att konvertera permanenta Azure AD-roll tilldelningar till just-in-Time-tilldelningar med Privileged Identity Management.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: how-to
ms.workload: identity
ms.subservice: pim
ms.date: 09/01/2020
ms.author: curtand
ms.custom: pim ; H1Hack27Feb2017
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4662e9fb537a93cb87c554e319256e2eca40d2e7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "92372437"
---
# <a name="discovery-and-insights-preview-for-azure-ad-roles-formerly-security-wizard"></a>Identifiering och insikter (för hands version) för Azure AD-roller (tidigare säkerhets guide)

Om du börjar med Privileged Identity Management (PIM) i din Azure Active Directory (Azure AD)-organisation kan du komma igång med hjälp av sidan **identifiering och insikter (för hands version)** . Den här funktionen visar vem som har tilldelats privilegierade roller i din organisation och hur du använder PIM för att snabbt ändra permanenta roll tilldelningar till just-in-Time-tilldelningar. Du kan visa eller göra ändringar i dina permanenta privilegierade roll tilldelningar i **identifiering och insikter (för hands version)**. Det är ett analys verktyg och ett åtgärds verktyg.

## <a name="discovery-and-insights-preview"></a>Identifiering och insikter (för hands version)

Innan organisationen börjar använda Privileged Identity Management, är alla roll tilldelningar permanenta. Användarna är alltid i tilldelade roller även om de inte behöver sina privilegier. Identifiering och insikter (för hands version), som ersätter den tidigare säkerhets guiden, visar en lista över privilegierade roller och hur många användare som för närvarande finns i dessa roller. Du kan lista ut tilldelningar för en roll för att lära dig mer om de tilldelade användarna om en eller flera av dem är okända.

: heavy_check_mark: **Microsoft rekommenderar** att du behåller två rast glas konton som är permanent tilldelade till den globala administratörs rollen. Se till att dessa konton inte kräver samma Multi-Factor Authentication-mekanism som dina vanliga administrativa konton för att logga in, enligt beskrivningen i [Hantera åtkomst konton för nöd situationer i Azure AD](../roles/security-emergency-access.md).

Håll också roll tilldelningar permanenta om en användare har en Microsoft-konto (med andra ord, ett konto som de använder för att logga in på Microsoft-tjänster som Skype eller Outlook.com). Om du behöver Multi-Factor Authentication för en användare med en Microsoft-konto för att aktivera en roll tilldelning kommer användaren att låsas upp.

## <a name="open-discovery-and-insights-preview"></a>Öppna identifiering och insikter (för hands version)

1. Logga in på [Azure-portalen](https://portal.azure.com/).

1. Öppna **Azure AD Privileged Identity Management**.

1. Välj **Azure AD-roller** och välj sedan **identifiering och insikter (för hands version)**. Om du öppnar sidan påbörjas identifierings processen för att hitta relevanta roll tilldelningar.

    ![Sidan Azure AD-roller – identifiering och insikter som visar de tre alternativen](./media/pim-security-wizard/new-preview-link.png)

1. Välj **minska globala administratörer**.

    ![Skärm bild som visar "identifiering och insikter (för hands version)" med åtgärden "minska globala administratörer" valt.](./media/pim-security-wizard/new-preview-page.png)

1. Granska listan med roll tilldelningar för global administratör.

    ![Minimera globala administratörer – roll fönstret som visar alla globala administratörer](./media/pim-security-wizard/new-global-administrator-list.png)

1. Välj **Nästa** för att välja de användare eller grupper som du vill göra berättigade och välj sedan **gör giltig** eller **ta bort tilldelning**.

    ![Konvertera medlemmar till berättigade sidor med alternativ för att välja medlemmar som du vill ska vara berättigade till roller](./media/pim-security-wizard/new-global-administrator-buttons.png)

1. Du kan också kräva att alla globala administratörer ska granska sin egen åtkomst.

    ![Sidan globala administratörer med avsnittet åtkomst granskningar](./media/pim-security-wizard/new-global-administrator-access-review.png)

1. När du har valt någon av dessa ändringar visas ett Azure-meddelande.

1. Du kan sedan välja **eliminera ständig åtkomst** eller **Granska tjänstens huvud namn** för att upprepa stegen ovan i andra privilegierade roller och roll tilldelningar för tjänstens huvud namn. För roll tilldelningar för tjänstens huvud namn kan du bara ta bort roll tilldelningar.

    ![Ytterligare insikts alternativ för att eliminera ständig åtkomst och granskning av tjänstens huvud namn ](./media/pim-security-wizard/new-preview-page-service-principals.png)

## <a name="next-steps"></a>Nästa steg

- [Tilldela Azure AD-roller i Privileged Identity Management](pim-how-to-add-role-to-user.md)
- [Ge åtkomst till andra administratörer för att hantera Privileged Identity Management](pim-how-to-give-access-to-pim.md)
