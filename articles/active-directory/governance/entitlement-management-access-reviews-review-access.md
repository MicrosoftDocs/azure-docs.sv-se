---
title: Granska åtkomst till ett Access-paket i hantering av Azure AD-rättigheter
description: Lär dig hur du utför en åtkomst granskning av rättighets hanterings paket i Azure Active Directory åtkomst granskningar (för hands version).
services: active-directory
documentationCenter: ''
author: ajburnle
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: compliance
ms.date: 06/18/2020
ms.author: ajburnle
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: d1115c08214db19227b4b8d7be671ce4da1cf2b1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "87798605"
---
# <a name="review-access-of-an-access-package-in-azure-ad-entitlement-management"></a>Granska åtkomst till ett Access-paket i hantering av Azure AD-rättigheter

Hantering av Azure AD-rättigheten fören klar hur företag hanterar åtkomst till grupper, program och SharePoint-webbplatser. Den här artikeln beskriver hur du utför åtkomst granskningar för andra användare som är tilldelade till ett Access-paket som en angiven granskare.

## <a name="prerequisites"></a>Förutsättningar

Om du vill granska användarnas aktiva åtkomst paket tilldelningar måste du uppfylla kraven för att få åtkomst granskning:
- Azure AD Premium P2
- Global administratör
- Utsedd användar administratör, katalog ägare eller åtkomst till paket hanteraren

Mer information finns i [licens krav](entitlement-management-overview.md#license-requirements).


## <a name="open-the-access-review"></a>Öppna åtkomst granskningen

Använd följande steg för att hitta och öppna åtkomst granskningen:

1. Du kan få ett e-postmeddelande från Microsoft som ber dig att granska åtkomst. Leta upp e-postmeddelandet för att öppna åtkomst granskningen. Här är ett exempel på ett e-postmeddelande för att granska åtkomst:
    
    ![Åtkomst granskning granskare e-post](./media/entitlement-management-access-reviews-review-access/review-access-reviewer-email.png)

1. Klicka på länken **Granska användar åtkomst** för att öppna åtkomst granskningen. 

1. Om du inte har e-postmeddelandet kan du hitta dina väntande åtkomst granskningar genom att navigera direkt till https://myaccess.microsoft.com .  (För amerikanska myndigheter använder du `https://myaccess.microsoft.us` i stället.)

1. Klicka på **åtkomst granskningar** i det vänstra navigerings fältet för att se en lista över väntande åtkomst granskningar som har tilldelats dig.
    
    ![Välj åtkomst granskningar för min åtkomst](./media/entitlement-management-access-reviews-review-access/review-access-myaccess-select-access-review.png)

1. Klicka på den granskning som du vill börja.
    
    ![Välj åtkomst granskning](./media/entitlement-management-access-reviews-review-access/review-access-select-access-review.png)

## <a name="perform-the-access-review"></a>Utför åtkomst granskningen

När du har öppnat åtkomst granskningen visas namnen på de användare som du vill granska. Du kan godkänna eller neka åtkomst på två sätt:
- Du kan godkänna eller neka åtkomst manuellt för en eller flera användare
- Du kan acceptera system rekommendationerna

### <a name="manually-approve-or-deny-access-for-one-or-more-users"></a>Godkänn eller neka åtkomst för en eller flera användare manuellt
1. Granska listan med användare och bestäm vilka användare som behöver fortsätta att ha åtkomst.

    ![Lista över användare som ska granskas](./media/entitlement-management-access-reviews-review-access/review-access-list-of-users.png)

1. Om du vill godkänna eller neka åtkomst väljer du alternativ knappen till vänster om användarens namn.

1. Välj **Godkänn** eller **neka** i fältet ovanför användar namnen.

    ![Välj användaren](./media/entitlement-management-access-reviews-review-access/review-access-select-users.png)

1. Om du inte är säker kan du klicka på knappen **Känn inte** till.

    Om du väljer det här alternativet, behåller användaren åtkomst och det här alternativet går till gransknings loggarna. Loggen visar andra granskare som du fortfarande har slutfört granskningen.

1. Du kan behöva ange en orsak till ditt beslut. Ange ett skäl och klicka på **Skicka**.

    ![Godkänn eller neka åtkomst](./media/entitlement-management-access-reviews-review-access/review-access-decision-approve.png)

1. Du kan ändra ditt beslut när som helst innan granskningen är klar. Det gör du genom att markera användaren i listan och ändra beslutet. Du kan till exempel godkänna åtkomst för en användare som du tidigare nekade.

Om det finns flera granskare registreras det senast skickade svaret. Överväg ett exempel där en administratör betecknar två granskare – Alice och Bob. Alice öppnar granska först och godkänner åtkomst. Innan granskningen avslutas öppnar Bob granskningen och nekar åtkomst. I det här fallet registreras det senaste beslutet om nekad åtkomst.

>[!NOTE]
>Om en användare nekas åtkomst tas de inte bort från Access-paketet omedelbart. Användaren kommer att tas bort från åtkomst paketet när granskningen upphör, eller en administratör avslutar granskningen.

### <a name="approve-or-deny-access-using-the-system-generated-recommendations"></a>Godkänn eller neka åtkomst med hjälp av de rekommendationer som genereras av systemet

Om du vill granska åtkomsten för flera användare snabbare kan du använda de rekommendationer som genereras av systemet och godkänna rekommendationerna med ett enda klick. Rekommendationerna genereras baserat på användarens inloggnings aktivitet.

1.  Klicka på **Godkänn rekommendationer** i fältet högst upp på sidan.
    
    ![Välj Godkänn rekommendationer](./media/entitlement-management-access-reviews-review-access/review-access-use-recommendations.png)
    
    Du ser en sammanfattning av de rekommenderade åtgärderna.

1.  Klicka på **Skicka** för att godkänna rekommendationerna.

## <a name="next-steps"></a>Nästa steg

- [Själv granskning av åtkomst paket](entitlement-management-access-reviews-self-review.md)