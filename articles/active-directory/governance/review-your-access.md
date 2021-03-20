---
title: Granska åtkomst till grupper & appar i åtkomst granskningar – Azure AD
description: Lär dig hur du granskar din egen åtkomst till grupper eller program i Azure Active Directory åtkomst granskningar.
services: active-directory
author: barclayn
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: compliance
ms.date: 12/22/2020
ms.author: barclayn
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: b3fef2f85ca7e7b4034c8582477796d49446ea44
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "97746787"
---
# <a name="review-access-for-yourself-to-groups-or-applications-in-azure-ad-access-reviews"></a>Granska åtkomst för dig själv till grupper eller program i åtkomst granskningar för Azure AD

Azure Active Directory (Azure AD) gör det enklare för företag att hantera åtkomst till grupper eller program i Azure AD och andra Microsoft Online Services med en funktion som kallas Azure AD-åtkomst granskningar.

Den här artikeln beskriver hur du granskar din egen åtkomst till en grupp eller ett program.

## <a name="review-your-access-using-my-apps"></a>Granska din åtkomst med mina appar

Det första steget för att utföra en åtkomst granskning är att hitta och öppna åtkomst granskningen.

>[!IMPORTANT]
> Det kan uppstå fördröjningar i att ta emot e-post och det kan ta upp till 24 timmar. Lägg till azure-noreply@microsoft.com i listan med betrodda mottagare och se till att du får alla e-postmeddelanden.

1. Sök efter ett e-postmeddelande från Microsoft som ber dig att granska åtkomst. Här är ett exempel på ett e-postmeddelande för att granska din åtkomst till en grupp.

    ![Exempel på e-post från Microsoft för att granska din åtkomst till en grupp](./media/review-your-access/access-review-email.png)

1. Klicka på länken **Granska åtkomst** för att öppna åtkomst granskningen.

Om du inte har e-postmeddelandet kan du hitta dina väntande åtkomst granskningar genom att följa dessa steg.

1. Logga in på mina apps-portalen på [https://myapps.microsoft.com](https://myapps.microsoft.com) .

    ![Min Apps-portal som visar appar som du har behörighet att](./media/review-your-access/myapps-access-panel.png)

1. I det övre högra hörnet på sidan klickar du på användarsymbolen, som visar ditt namn och standardorganisationen. Om mer än en organisation visas väljer du den organisation som begärde en åtkomstgranskning.

1. Klicka på panelen **åtkomst granskningar** på höger sida av sidan för att se en lista över väntande åtkomst granskningar.

    Om panelen inte är synlig finns det inga åtkomstgranskningar att utföra för den organisationen, och ingen åtgärd krävs just nu.

    ![Lista över väntande åtkomst granskningar för dina appar och grupper](./media/review-your-access/access-reviews-list.png)

1. Klicka på länken **Starta granskning** för den åtkomst granskning som du vill utföra.

### <a name="perform-the-access-review"></a>Utför åtkomst granskningen

När du har öppnat åtkomst granskningen kan du se din åtkomst.

1. Granska din åtkomst och bestäm om du fortfarande behöver åtkomst.

    Om begäran är att granska åtkomsten för andra, ser sidan annorlunda ut. Mer information finns i [Granska åtkomst till grupper eller program](perform-access-review.md).

    ![Skärm bild som visar en öppen åtkomst granskning som frågar om du fortfarande behöver åtkomst till en grupp.](./media/review-your-access/perform-access-review.png)

1. Klicka på **Ja** om du vill behålla åtkomsten eller på **Nej** om du vill ta bort åtkomsten.

1. Om du klickar på **Ja** kan du behöva ange en motivering i rutan **orsak** .

    ![Skärm bild som visar en slutförd åtkomst granskning som frågar om du fortfarande behöver åtkomst till en grupp, med alternativet Ja valt.](./media/review-your-access/perform-access-review-submit.png)

1. Klicka på **Skicka**.

    Ditt val skickas och du returnerade till portalen Mina appar.

    Om du vill ändra ditt svar öppnar du sidan åtkomst granskningar igen och uppdaterar ditt svar. Du kan ändra ditt svar när du vill tills åtkomst granskningen har slutförts.

    > [!NOTE]
    > Om du har angett att du inte längre behöver åtkomst, tas du inte bort direkt. Du tas bort när granskningen har slutförts eller när en administratör stoppar granskningen.

## <a name="review-your-own-access-using-my-access-new"></a>Granska din egen åtkomst med min åtkomst (ny)

Du kan prova den nya upplevelsen med det uppdaterade användar gränssnittet i min åtkomst ett par olika sätt:

### <a name="my-apps-portal"></a>Min Apps-Portal

1. Logga in på mina apps-portalen på [https://myapps.microsoft.com](https://myapps.microsoft.com) .

    ![Min Apps-portal som visar appar som du har behörighet att](./media/review-your-access/myapps-access-panel.png)

2. Klicka på panelen **åtkomst granskningar** om du vill se en lista över väntande åtkomst granskningar.

    > [!NOTE]
    > Om panelen **åtkomst granskningar** inte visas finns det inga åtkomst granskningar att utföra för organisationen och ingen åtgärd krävs för tillfället.

3. Klicka på **prova!** i banderollen högst upp på sidan för att gå till den nya åtkomst miljön.

    ![Listan över väntande åtkomst granskningar för appar och grupper med den nya upplevelsen tillgänglig banderoll som visas under för hands versionen](./media/review-your-access/banner-your-access.png)

4. Fortsätt i avsnittet **utför åtkomst granskningen**

### <a name="email"></a>E-post

>[!IMPORTANT]
> Det kan uppstå fördröjningar i att ta emot e-post och det kan ta upp till 24 timmar. Lägg till azure-noreply@microsoft.com i listan med betrodda mottagare och se till att du får alla e-postmeddelanden.

1. Sök efter ett e-postmeddelande från Microsoft som ber dig att granska åtkomst. Du kan se ett exempel på ett e-postmeddelande nedan:

 ![Exempel på e-post från Microsoft för att kontrol lera åtkomsten till en grupp](./media/review-your-access/access-review-email-preview.png)

2. Klicka på länken **Granska åtkomst** för att öppna åtkomst granskningen.

3. Fortsätt i avsnittet **utför åtkomst granskningen**

>[!NOTE]
>Om du klickar på Starta granskning går du till **Mina appar** och följer stegen som anges i avsnittet ovan med rubriken **Mina appar**.

### <a name="directly-at-my-access"></a>Direkt i min åtkomst

Du kan också visa dina väntande åtkomst granskningar genom att använda webbläsaren för att öppna min åtkomst.

1. Logga in till min åtkomst på https://myaccess.microsoft.com/

2. Välj **åtkomst granskningar** på menyn i det vänstra sid fältet för att se en lista över väntande åtkomst granskningar som har tilldelats dig.

   ![åtkomst granskningar på menyn](./media/review-your-access/access-review-menu.png)

### <a name="perform-the-access-review"></a>Utför åtkomst granskningen

1. Under grupper och appar kan du se:
    
    - **Namn** Namnet på åtkomst granskningen.
    - **Förfallo datum** Förfallo datumet för granskningen. Efter det här datumet kan nekade användare tas bort från gruppen eller appen som granskas.
    - **Resurs** Namnet på resursen under granskning.
    - **Förlopp** Antalet användare som granskats över det totala antalet användare som ingår i den här åtkomst granskningen.
    
2. Klicka på namnet på en åtkomst granskning för att komma igång.

   ![Lista över väntande åtkomst granskningar för appar och grupper](./media/review-your-access/access-reviews-list-preview.png)

3. Granska din åtkomst och bestäm om du fortfarande behöver åtkomst.

    Om begäran är att granska åtkomsten för andra, ser sidan annorlunda ut. Mer information finns i [Granska åtkomst till grupper eller program](perform-access-review.md).

    ![Öppna åtkomst granskning och fråga om du fortfarande behöver åtkomst till en grupp](./media/review-your-access/review-access-preview.png)

1. Välj **Ja** om du vill behålla åtkomsten eller Välj **Nej** om du vill ta bort åtkomsten.

1. Om du klickar på **Ja** kan du behöva ange en motivering i rutan **orsak** .

    ![Slutförd åtkomst granskning som frågar om du fortfarande behöver åtkomst till en grupp](./media/review-your-access/review-access-yes-preview.png)

1. Klicka på **Skicka**.

    Ditt val skickas och du kommer tillbaka till sidan min åtkomst.

    Om du vill ändra ditt svar öppnar du sidan åtkomst granskningar igen och uppdaterar ditt svar. Du kan ändra ditt svar när du vill tills åtkomst granskningen har slutförts.

    > [!NOTE]
    > Om du har angett att du inte längre behöver åtkomst, tas du inte bort direkt. Du tas bort när granskningen har slutförts eller när en administratör stoppar granskningen.

## <a name="next-steps"></a>Nästa steg

- [Slutför en åtkomst granskning av grupper eller program](complete-access-review.md)
