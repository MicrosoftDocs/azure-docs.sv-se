---
title: Lägg till din organisations sekretess information – Azure Active Directory | Microsoft Docs
description: Anvisningar om hur du lägger till din organisations sekretess information i Azure Active Directory egenskaps områden.
services: active-directory
author: ajburnle
manager: daveba
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: how-to
ms.date: 04/17/2018
ms.author: ajburnle
ms.reviewer: bpham
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: f47ae9b087615a77f6bd4d3f14f1b0052037cba6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "95996770"
---
# <a name="add-your-organizations-privacy-info-using-azure-active-directory"></a>Lägg till din organisations sekretess information med Azure Active Directory
Den här artikeln förklarar hur en innehavaradministratör kan lägga till sekretess information till en organisations Azure Active Directory (Azure AD)-klient, via Azure Portal.

Vi rekommenderar starkt att du lägger till både din globala sekretess kontakt och din organisations sekretess policy, så att dina interna medarbetare och externa gäster kan granska dina principer. Eftersom sekretess policys är unikt skapade och skräddarsydda för varje företag, rekommenderar vi starkt att du kontaktar en advokat för att få hjälp.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="add-your-privacy-info-on-azure-ad"></a>Lägg till din sekretess information på Azure AD
Du lägger till din organisations sekretess information i avsnittet **Egenskaper** i Azure AD.

### <a name="to-access-the-properties-area-and-add-your-privacy-information"></a>Så här öppnar du egenskaps avsnittet och lägger till din sekretess information

1. Logga in på Azure Portal som klient organisations administratör.

2. Välj **Azure Active Directory** i det vänstra navigerings fältet och välj sedan **Egenskaper**.

    **Egenskaps** områden visas.

    ![Avsnittet för Azure AD-egenskaper som markerar fältet sekretess information](media/active-directory-properties-area/properties-area.png)

3. Lägg till din sekretess information för dina anställda:

    - **Teknisk kontakt.** Ange e-postadressen som personen ska kontakta för teknisk support inom organisationen.
    
    - **Global sekretess kontakt.** Ange e-postadressen som personen ska kontakta för att få frågor om personlig data sekretess. Den här personen är även Microsoft-kontakter om det finns data intrång. Om ingen person visas här kontaktar Microsoft dina globala administratörer.

    - **URL för sekretess policy.** Skriv länken till din organisations dokument som beskriver hur din organisation hanterar både intern och extern gäst data sekretess.

        >[!Important]
        >Om du inte inkluderar antingen din egen sekretess policy eller din sekretess kontakt kommer dina externa gäster att se text i rutan **Granska behörigheter** som säger att **< _ditt organisations namn_> inte har tillhandahållit några länkar till sina villkor för att granska**. En gäst användare ser exempelvis det här meddelandet när de får en inbjudan att få åtkomst till en organisation via B2B-samarbete.

        ![B2B-samarbete för gransknings behörighet med meddelande](media/active-directory-properties-area/active-directory-no-privacy-statement-or-contact.png)

4. Välj **Spara**.

## <a name="next-steps"></a>Nästa steg
- [Inlösen av inbjudan till Azure Active Directory B2B-samarbete](../external-identities/redemption-experience.md)
- [Lägga till eller ändra profil information för en användare i Azure Active Directory](active-directory-users-profile-azure-portal.md)