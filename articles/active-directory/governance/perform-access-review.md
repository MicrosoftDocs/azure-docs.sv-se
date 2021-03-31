---
title: Granska åtkomst till grupper & program i åtkomst granskningar – Azure AD
description: Lär dig hur du granskar åtkomsten till grupp medlemmar eller program åtkomst i Azure Active Directory åtkomst granskningar.
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
ms.openlocfilehash: b5cd7a5737b2d13e63eabbbddd076cfc7aff83ac
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "97746804"
---
# <a name="review-access-to-groups-and-applications-in-azure-ad-access-reviews"></a>Granska åtkomst till grupper och program i åtkomst granskningar för Azure AD

Azure Active Directory (Azure AD) gör det enklare för företag att hantera åtkomst till grupper och program i Azure AD och andra Microsoft Online Services med en funktion som kallas Azure AD-åtkomst granskningar. Den här artikeln går igenom hur en viss granskare utför en åtkomst granskning för medlemmar i en grupp eller användare som har åtkomst till ett program. Om du vill granska, åtkomst till ett paket Läs [gransknings åtkomst för ett Access-paket i hantering av Azure AD-rättigheter](entitlement-management-access-reviews-review-access.md)

## <a name="perform-access-review-using-my-apps"></a>Utför åtkomst granskning med mina appar

Du kan starta åtkomst gransknings processen från e-postmeddelandet eller genom att gå direkt till webbplatsen.

- **E-post**:

>[!IMPORTANT]
> Det kan uppstå fördröjningar i att ta emot e-post och det kan ta upp till 24 timmar. Lägg till azure-noreply@microsoft.com i listan med betrodda mottagare och se till att du får alla e-postmeddelanden.

1. Sök efter ett e-postmeddelande från Microsoft som ber dig att granska åtkomst. Här är ett exempel på ett e-postmeddelande för att granska åtkomsten för en grupp.

    ![Skärm bild som visar ett exempel på ett e-postmeddelande från Microsoft för att kontrol lera åtkomsten till en grupp.](./media/perform-access-review/access-review-email.png)

1. Klicka på länken **Starta granskning** för att öppna åtkomst granskningen.

- **Om du inte har e-postmeddelandet** kan du hitta dina väntande åtkomst granskningar genom att följa dessa steg.

    1. Logga in på mina apps-portalen på [https://myapps.microsoft.com](https://myapps.microsoft.com) .

        ![Min Apps-portal som visar appar som du har behörighet att](./media/perform-access-review/myapps-access-panel.png)

    1. I det övre högra hörnet på sidan klickar du på användaren bredvid ditt namn och standard organisation. Om mer än en organisation visas väljer du den organisation som begärde en åtkomstgranskning.

    1. Klicka på panelen **åtkomst granskningar** om du vill se en lista över väntande åtkomst granskningar.

        > [!NOTE]
        > Om panelen **åtkomst granskningar** inte visas finns det inga åtkomst granskningar att utföra för organisationen och ingen åtgärd krävs för tillfället.

        ![Skärm bild som visar listan över väntande åtkomst granskningar för appar och grupper.](./media/perform-access-review/access-reviews-list.png)

    1. Klicka på länken **Starta granskning** för den åtkomst granskning som du vill utföra.

När du har öppnat åtkomst granskningen visas namnen på de användare som ska ha åtkomst granskning.

Om begäran är att granska din egen åtkomst kommer sidan att se annorlunda ut. Mer information finns i [Granska åtkomst för dig själv till grupper eller program](review-your-access.md).

![Öppna åtkomst granskning som visar de användare som ska granskas](./media/perform-access-review/perform-access-review.png)

Du kan godkänna eller neka åtkomst på två sätt:

- Du kan godkänna eller neka åtkomst för en eller flera användare manuellt genom att välja lämplig åtgärd för varje Användarbegäran.
- Du kan acceptera system rekommendationerna.

### <a name="approve-or-deny-access-for-one-or-more-users"></a>Godkänn eller neka åtkomst för en eller flera användare

1. Granska listan över användare och bestäm om du vill godkänna eller neka fortsatt åtkomst.

    - Om du vill godkänna eller neka åtkomst för en enskild användare klickar du på raden för att öppna ett fönster för att ange vilken åtgärd som ska vidtas. 
    - Om du vill godkänna eller neka åtkomst för flera användare, så Lägg till kryss markeringar bredvid användarna och klicka sedan på knappen **Granska X-användare** för att öppna ett fönster för att ange vilken åtgärd som ska vidtas.

1. Klicka på **Godkänn** eller **neka**. 

    ![Åtgärds fönster som innehåller alternativen Godkänn, neka och vet inte](./media/perform-access-review/approve-deny.png)
    >[!NOTE]
    > Om du är osäker kan du klicka på **vet inte**. användaren kommer att ha åtkomst och ditt val registreras i gransknings loggarna.

1. Administratören för åtkomst granskningen kan kräva att du anger en orsak i **orsaks** rutan för ditt beslut. Även om det inte krävs någon orsak. Du kan fortfarande ange en orsak till ditt beslut och den information som du inkluderar är tillgänglig för andra granskare.

1. När du har angett vilken åtgärd som ska utföras klickar du på **Spara**.

    >[!NOTE]
    > Du kan ändra ditt svar när som helst innan åtkomst granskningen har slutförts. Om du vill ändra ditt svar väljer du raden och uppdaterar svaret. Du kan till exempel godkänna en tidigare nekad användare eller neka en tidigare godkänd användare.

    >[!IMPORTANT]
    > - Om en användare nekas åtkomst tas de inte bort direkt. De tas bort när gransknings perioden är slut eller när en administratör stoppar granskningen om [Auto Apply](complete-access-review.md#apply-the-changes) är aktiverat.
    > - Om det finns flera granskare registreras det senast skickade svaret. Överväg ett exempel där en administratör betecknar två granskare – Alice och Bob. Alice öppnar först åtkomst granskningen och godkänner en användares åtkomstbegäran. Innan gransknings perioden är slut öppnar Bob åtkomst granskningen och nekar åtkomst på samma begäran som tidigare godkänts av Alice. Det sista beslutet som nekar åtkomst är svaret som registreras.

### <a name="approve-or-deny-access-based-on-recommendations"></a>Godkänn eller neka åtkomst baserat på rekommendationer

För att få åtkomst granskningar enklare och snabbare ger vi även rekommendationer som du kan acceptera med ett enda klick. Rekommendationerna genereras baserat på användarens inloggnings aktivitet.

1. Klicka på **Godkänn rekommendationer** i det blå fältet längst ned på sidan.

    ![Skärm bild som visar den öppna åtkomst gransknings listan med knappen "Godkänn rekommendationer" markerad.](./media/perform-access-review/accept-recommendations.png)

    Du ser en sammanfattning av de rekommenderade åtgärderna.

    ![Fönster som visar en sammanfattning av de rekommenderade åtgärderna](./media/perform-access-review/accept-recommendations-summary.png)

1. Klicka på **OK** för att godkänna rekommendationerna.

## <a name="perform-access-review-using-my-access-new"></a>Utför åtkomst granskning med min åtkomst (ny)

Du kan komma åt den nya granskaren med det uppdaterade användar gränssnittet i min åtkomst på ett par olika sätt:

### <a name="my-apps-portal"></a>Min Apps-Portal

1. Logga in till Mina appar på [https://myapps.microsoft.com](https://myapps.microsoft.com) .

    ![Min Apps-portal som visar appar som du har behörighet att](./media/perform-access-review/myapps-access-panel.png)

2. Klicka på panelen **åtkomst granskningar** om du vill se en lista över väntande åtkomst granskningar.

    > [!NOTE]
    > Om panelen **åtkomst granskningar** inte visas finns det inga åtkomst granskningar att utföra för organisationen och ingen åtgärd krävs för tillfället.

![Listan över väntande åtkomst granskningar för appar och grupper med den nya upplevelsen tillgänglig banderoll som visas under för hands versionen](./media/perform-access-review/banner.png)

3. Klicka på **prova!** i banderollen högst upp på sidan. Detta leder till den nya åtkomst upplevelsen.
  
### <a name="email"></a>E-post

  >[!IMPORTANT]
> Det kan uppstå fördröjningar i att ta emot e-post och det kan ta upp till 24 timmar. Lägg till azure-noreply@microsoft.com i listan med betrodda mottagare och se till att du får alla e-postmeddelanden.

   1. Sök efter ett e-postmeddelande från Microsoft som ber dig att granska åtkomst. Du kan se ett exempel på ett e-postmeddelande nedan:

   ![Exempel på e-post från Microsoft för att kontrol lera åtkomsten till en grupp](./media/perform-access-review/access-review-email-preview.png)

   2. Klicka på länken **Starta granskning** för att öppna åtkomst granskningen.

>[!NOTE]
>Om du klickar på Starta granskning går du till **Mina appar** och följer stegen som anges i avsnittet ovan med rubriken **Mina appar**.

### <a name="navigate-to-my-access-directly"></a>Gå till min åtkomst direkt

Du kan också visa dina väntande åtkomst granskningar genom att använda webbläsaren för att öppna min åtkomst.

1. Logga in till min åtkomst på https://myaccess.microsoft.com/

2. Välj **åtkomst granskningar** på menyn i det vänstra sid fältet för att se en lista över väntande åtkomst granskningar som har tilldelats dig.

   ![åtkomst granskningar på menyn](./media/perform-access-review/access-review-menu.png)

### <a name="approve-or-deny-access-for-one-or-more-users"></a>Godkänn eller neka åtkomst för en eller flera användare

När du har öppnat min åtkomst under grupper och appar kan du se:

- **Namn** Namnet på åtkomst granskningen.
- **Förfallo datum** Förfallo datumet för granskningen. Efter det här datumet kan nekade användare tas bort från gruppen eller appen som granskas.
- **Resurs** Namnet på resursen under granskning.
- **Förlopp** Antalet användare som granskats över det totala antalet användare som ingår i den här åtkomst granskningen.

Klicka på namnet på en åtkomst granskning för att komma igång.

![Lista över väntande åtkomst granskningar för appar och grupper](./media/perform-access-review/access-reviews-list-preview.png)

När den öppnas visas en lista över användare i omfånget för åtkomst granskningen. Om begäran är att granska din egen åtkomst kommer sidan att se annorlunda ut. Mer information finns i [Granska åtkomst för dig själv till grupper eller program](review-your-access.md).

Du kan godkänna eller neka åtkomst på två sätt:

- Du kan godkänna eller neka åtkomst manuellt för en eller flera användare.
- Du kan acceptera system rekommendationerna.

#### <a name="manually-approve-or-deny-access-for-one-or-more-users"></a>Godkänn eller neka åtkomst för en eller flera användare manuellt

1. Granska listan över användare och bestäm om du vill godkänna eller neka fortsatt åtkomst.
2. Välj en eller flera användare genom att klicka på cirkeln bredvid deras namn.
3. Välj **Godkänn** eller **neka** i fältet ovan.
    - Om du är osäker kan du klicka på **vet inte**. Användaren kommer att ha åtkomst och ditt val registreras i gransknings loggarna. Det är viktigt att du är medveten om att all information som du tillhandahåller är tillgänglig för andra granskare. De kan läsa dina kommentarer och ta dem i beaktande när de granskar begäran.

    ![Öppna åtkomst granskning visar en lista över användare som behöver granska](./media/perform-access-review/user-list-preview.png)

4. Administratören för åtkomst granskningen kan kräva att du anger en orsak i **orsaks** rutan för ditt beslut. Även om det inte krävs någon orsak. Du kan fortfarande ange en orsak till ditt beslut och den information som du inkluderar är tillgänglig för andra god kännare för granskning.

5. Klicka på **Skicka**.
    - Du kan ändra ditt svar när du vill tills åtkomst granskningen har slutförts. Om du vill ändra ditt svar väljer du raden och uppdaterar svaret. Du kan till exempel godkänna en tidigare nekad användare eller neka en tidigare godkänd användare.

 >[!IMPORTANT]
 > - Om en användare nekas åtkomst tas de inte bort direkt. De tas bort när gransknings perioden är slut eller när en administratör stoppar granskningen. 
 > - Om det finns flera granskare registreras det senast skickade svaret. Överväg ett exempel där en administratör betecknar två granskare – Alice och Bob. Alice öppnar först åtkomst granskningen och godkänner en användares åtkomstbegäran. Innan gransknings perioden är slut öppnar Bob åtkomst granskningen och nekar åtkomst på samma begäran som tidigare godkänts av Alice. Det sista beslutet som nekar åtkomst är svaret som registreras.

#### <a name="approve-or-deny-access-based-on-recommendations"></a>Godkänn eller neka åtkomst baserat på rekommendationer

För att få åtkomst granskningar enklare och snabbare ger vi även rekommendationer som du kan acceptera med ett enda klick. Rekommendationerna genereras baserat på användarens inloggnings aktivitet.

1. Välj en eller flera användare och klicka sedan på **Godkänn rekommendationer**.

    ![Öppna listan med åtkomst granskningar som visar knappen Godkänn rekommendationer](./media/perform-access-review/accept-recommendations-preview.png)

1. Klicka på **Skicka** för att godkänna rekommendationerna.

Om du vill godkänna rekommendationer för alla användare ser du till att ingen är markerad och klickar på knappen **Godkänn rekommendationer** i översta fältet.

>[!NOTE]
>När du godkänner rekommendationer kommer tidigare beslut inte att ändras.

## <a name="next-steps"></a>Nästa steg

- [Slutför en åtkomst granskning av grupper eller program](complete-access-review.md)
