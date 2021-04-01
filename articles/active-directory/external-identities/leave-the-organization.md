---
title: Lämna en organisation som gäst användare – Azure Active Directory
description: Visar hur en Azure AD B2B-gäst användare kan lämna en organisation med hjälp av åtkomst panelen.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 06/13/2019
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.collection: M365-identity-device-management
ms.openlocfilehash: fc3ef7d168c17ec10fe64925adbda7044a2a4c82
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "87910078"
---
# <a name="leave-an-organization-as-a-guest-user"></a>Lämna en organisation som en gästanvändare

En Azure Active Directory (Azure AD) B2B gäst användare kan välja att lämna en organisation när som helst om de inte längre behöver använda appar från organisationen eller upprätthålla någon koppling. En användare kan lämna en organisation på egen hand, utan att behöva kontakta en administratör.

> [!NOTE]
> En gäst användare kan inte lämna en organisation om deras konto har inaktiverats i hem klienten eller resurs klienten. Om kontot har inaktiverats måste gäst användaren kontakta klient administratören, som antingen kan ta bort gäst kontot eller aktivera gäst kontot så att användaren kan lämna organisationen.

## <a name="leave-an-organization"></a>Lämna en organisation

Följ dessa steg om du vill lämna en organisation.

1. Gå till profil sidan för åtkomst panelen genom att utföra något av följande steg:
   
   - Klicka på ditt namn längst upp till höger i [Azure Portal](https://portal.azure.com)och välj **Visa konto**.
   - Öppna [åtkomst panelen](https://myapps.microsoft.com), klicka på ditt namn längst upp till höger och bredvid **organisationer** väljer du inställnings ikonen (kugg hjul).
 
   ![Skärm bild som visar användar inställningar i åtkomst panelen](media/leave-the-organization/UserSettings.png) 

   > [!NOTE]
   > Om du inte redan har loggat in på den organisation som du vill lämna klickar du på Logga in **för att lämna organisations** länken bredvid organisationens namn under **organisationer**. När du har loggat in klickar du på ditt namn i det övre högra hörnet och bredvid **organisationer** väljer du inställnings ikonen (kugg hjul).

3. Under **organisationer**, leta upp den organisation som du vill lämna och välj **lämna organisation**.

   ![Skärm bild som visar alternativet låt organisationen vara kvar i användar gränssnittet](media/leave-the-organization/LeaveOrg.png)

4. När du uppmanas att bekräfta väljer du **lämna**. 

## <a name="account-removal"></a>Ta bort konto

När en användare lämnar en organisation, är användar kontot "Soft Deleted" i katalogen. Objektet användare flyttas som standard till avsnittet **borttagna användare** i Azure AD, men tas inte bort permanent i 30 dagar. Med den här mjuka borttagningen kan administratören återställa användar kontot (inklusive grupper och behörigheter), om användaren gör en begäran om att återställa kontot under den 30-dagars perioden.

Om du vill kan en innehavaradministratör permanent ta bort kontot när som helst under 30-dagars perioden. Gör så här:

1. Välj **Azure Active Directory** i [Azure-portalen](https://portal.azure.com).
2. Under **Hantera** väljer du **Användare**.
3. Välj **borttagna användare**.
4. Markera kryss rutan bredvid en borttagen användare och välj sedan **ta bort permanent**.

Den här åtgärden är oåterkallelig om du tar bort en användare permanent.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="next-steps"></a>Nästa steg

- En översikt över Azure AD B2B finns i [Vad är Azure AD B2B-samarbete?](what-is-b2b.md)



