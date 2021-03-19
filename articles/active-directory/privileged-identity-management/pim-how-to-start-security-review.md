---
title: Skapa en åtkomst granskning av Azure AD-roller i PIM – Azure AD | Microsoft Docs
description: Lär dig hur du skapar en åtkomst granskning av Azure AD-roller i Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: how-to
ms.workload: identity
ms.subservice: pim
ms.date: 3/16/2021
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 310122177d4bd1603f5f498aa2a51620eeda4a20
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "104592856"
---
# <a name="create-an-access-review-of-azure-ad-roles-in-privileged-identity-management"></a>Skapa en åtkomst granskning av Azure AD-roller i Privileged Identity Management

För att minska risken för inaktuella roll tilldelningar bör du regelbundet granska åtkomst. Du kan använda Azure AD Privileged Identity Management (PIM) för att skapa åtkomst granskningar för privilegierade Azure AD-roller. Du kan också konfigurera återkommande åtkomst granskningar som sker automatiskt.

Den här artikeln beskriver hur du skapar en eller flera åtkomst granskningar för privilegierade Azure AD-roller.

## <a name="prerequisites"></a>Förutsättningar

[Privilegie rad roll administratör](../roles/permissions-reference.md#privileged-role-administrator)

## <a name="open-access-reviews"></a>Öppna åtkomst granskningar

1. Logga in på [Azure Portal](https://portal.azure.com/) med en användare som är medlem i rollen privilegie rad roll administratör.

1. Öppna **Azure AD Privileged Identity Management**.

1. Välj **Azure AD-roller**.

1. Under hantera, Välj **åtkomst granskningar** och välj sedan **ny**.

    ![Azure AD-roller – åtkomst gransknings listan visar status för alla granskningar](./media/pim-how-to-start-security-review/access-reviews.png)

Klicka på **ny** för att skapa en ny åtkomst granskning.

1. Ge åtkomst granskningen namnet. Du kan också ange en beskrivning av granskningen. Namnet och beskrivningen visas för granskarna.

    ![Skapa en åtkomst granskning – granska namn och beskrivning](./media/pim-how-to-start-security-review/name-description.png)

1. Ange **start datum**. Som standard sker en åtkomst granskning en gång, startas samma gång som den skapas och den avslutas om en månad. Du kan ändra start-och slutdatumen så att en åtkomst granskning börjar i framtiden och det senaste antalet dagar som du vill.

    ![Start datum, frekvens, varaktighet, slut, antal gånger och slutdatum](./media/pim-how-to-start-security-review/start-end-dates.png)

1. Om du vill göra åtkomst granskningen återkommande ändrar du **frekvens** inställningen från **en gång** till **varje vecka**, **varje månad**, varje kvartal, **varje** **kvartal** eller varje **halvår**. Använd skjutreglaget **varaktighet** eller text rutan för att definiera hur många dagar varje granskning av den återkommande serien ska vara öppen för inmatade i granskare. Den längsta tid som du kan ange för en månatlig granskning är till exempel 27 dagar för att undvika överlappande granskningar.

1. Använd **End** -inställningen för att ange hur du ska avsluta gransknings serien för återkommande åtkomst. Serien kan sluta på tre sätt: den körs kontinuerligt för att starta recensioner på obestämd tid, fram till ett visst datum eller efter att ett definierat antal förekomster har slutförts. En annan användar administratör eller en annan global administratör kan stoppa serien när den har skapats genom att ändra datumet i **Inställningar**, så att det slutar på det datumet.

1. I avsnittet **användare** väljer du en eller flera roller som du vill granska medlemskap i.

    ![Användarens omfång att granska roll medlemskap för](./media/pim-how-to-start-security-review/users.png)

    > [!NOTE]
    > - Roller som väljs här inkluderar både [permanenta och berättigade roller](../privileged-identity-management/pim-how-to-add-role-to-user.md).
    > - Om du väljer mer än en roll skapas flera åtkomst granskningar. Om du till exempel väljer fem roller skapas fem olika åtkomst granskningar.
    > - För roller med tilldelade grupper kommer åtkomsten till varje grupp som är länkad till rollen under granskning att granskas som en del av åtkomst granskningen.
    Om du skapar en åtkomst granskning av **Azure AD-roller**, visar följande ett exempel på listan granska medlemskap.

    ![Fönstret granska medlemskap visar Azure AD-roller som du kan välja](./media/pim-how-to-start-security-review/review-membership.png)

    Om du skapar en åtkomst granskning av **Azures resurs roller** visar följande bild ett exempel på listan granska medlemskap.

    ![Rutan granska medlemskap visar de Azure-resurstilldelningar du kan välja](./media/pim-how-to-start-security-review/review-membership-azure-resource-roles.png)

1. I avsnittet **granskare väljer du en** eller flera personer för att granska alla användare. Alternativt kan du välja att låta medlemmarna granska sin egen åtkomst.

    ![Listor över granskare av markerade användare eller medlemmar (Self)](./media/pim-how-to-start-security-review/reviewers.png)

    - **Valda användare** – Använd det här alternativet om du inte vet vem som behöver åtkomst. Med det här alternativet kan du tilldela granskningen till en resurs ägare eller grupp hanterare för att slutföra.
    - **Medlemmar (Self)** – Använd det här alternativet om du vill att användarna ska granska sina egna roll tilldelningar. Grupper som tilldelats rollen kommer inte att ingå i granskningen när det här alternativet är markerat.
    - **Manager** – Använd det här alternativet om du vill att användarens chef ska granska roll tilldelningen. När du väljer chef kan du också välja att ange en återställnings granskare. Återställnings granskare uppmanas att granska en användare när användaren inte har angett någon chef i katalogen. Grupper som tilldelats rollen granskas av återställnings granskaren om en har valts. 

### <a name="upon-completion-settings"></a>Vid slut för ande inställningar

1. Om du vill ange vad som händer när en granskning har slutförts expanderar du avsnittet **när inställningarna slutförs** .

    ![Vid slut för ande inställningar som ska tillämpas automatiskt och bör granskning inte svara](./media/pim-how-to-start-security-review/upon-completion-settings.png)

1. Om du vill ta bort åtkomst automatiskt för användare som har nekats, ställer du in **automatiskt tillämpa resultat på resursen** för att **Aktivera**. Om du vill tillämpa resultaten manuellt när granskningen är klar ställer du in växeln på **inaktivera**.

1. Använd listan **ska inte svara** på listan om du vill ange vad som händer för användare som inte granskas av granskaren under gransknings perioden. Den här inställningen påverkar inte användare som har granskats manuellt av granskarna. Om den sista granskaren av beslutet är neka tas användarens åtkomst bort.

    - **Ingen ändring** – lämna användarens åtkomst oförändrad
    - **Ta bort åtkomst** – ta bort användares åtkomst
    - **Godkänn åtkomst** – Godkänn användarens åtkomst
    - **Ta rekommendationer** – beakta systemets rekommendation om att neka eller godkänna användarens fortsatta åtkomst

### <a name="advanced-settings"></a>Avancerade inställningar

1. Om du vill ange ytterligare inställningar expanderar du avsnittet **Avancerade inställningar** .

    ![Avancerade inställningar för Visa rekommendationer, Kräv orsak för godkännande, e-postaviseringar och påminnelser](./media/pim-how-to-start-security-review/advanced-settings.png)

1. Ange **Visa rekommendationer** för att **Aktivera** för att Visa granskarna system rekommendationer baserat på användarens åtkomst information.

1. Ange **Kräv orsak för godkännande** för att **göra det möjligt** för granskaren att ange en orsak till godkännande.

1. Ange **e-postaviseringar** för att **tillåta** att Azure AD skickar e-postmeddelanden till granskare när en åtkomst granskning startar och till administratörer när en granskning har slutförts.

1. Ställ in **påminnelser** för att **tillåta** att Azure AD skickar påminnelser om åtkomst granskningar till granskare som inte har slutfört sin granskning.
1. Innehållet i e-postmeddelandet som skickas till granskare genereras automatiskt baserat på gransknings informationen, till exempel gransknings namn, resurs namn, förfallo datum osv. Om du behöver ett sätt att kommunicera ytterligare information, till exempel ytterligare instruktioner eller kontakt information, kan du ange den här informationen i det **extra innehållet för e-postmeddelandet i e-** postmeddelandet som ska ingå i inbjudan och påminnelse e-postmeddelanden som skickas till tilldelade granskare. Det markerade avsnittet nedan visar var den här informationen kommer att visas.

    ![Innehållet i e-postmeddelandet som skickas till granskare med högdagrar](./media/pim-how-to-start-security-review/email-info.png)

## <a name="start-the-access-review"></a>Starta åtkomst granskningen

När du har angett inställningarna för åtkomst granskning väljer du **Starta**. Åtkomst granskningen visas i listan med en indikator för dess status.

![Listan åtkomst granskningar som visar statusen för startade granskningar](./media/pim-how-to-start-security-review/access-reviews-list.png)

Som standard skickar Azure AD ett e-postmeddelande till granskare strax efter att granskningen startar. Om du väljer att inte låta Azure AD skicka e-postmeddelandet måste du meddela granskarna att en åtkomst granskning väntar på att de ska slutföras. Du kan visa dem i anvisningarna för hur du [granskar åtkomst till Azure AD-roller](pim-how-to-perform-security-review.md).

## <a name="manage-the-access-review"></a>Hantera åtkomst granskningen

Du kan följa förloppet när granskarna har slutfört sina granskningar på **översikts** sidan för åtkomst granskningen. Ingen åtkomst behörighet har ändrats i katalogen förrän [granskningen har slutförts](pim-how-to-complete-review.md).

![Översikts sidan åtkomst granskningar som visar information om granskningen](./media/pim-how-to-start-security-review/access-review-overview.png)

Om det här är en engångs granskning kan du efter att åtkomst gransknings perioden är över eller administratören stoppar åtkomst granskningen genom att följa stegen i [slutföra en åtkomst granskning av Azure AD-roller](pim-how-to-complete-review.md) för att se och tillämpa resultaten.  

Om du vill hantera en serie åtkomst granskningar navigerar du till åtkomst granskningen och du hittar kommande förekomster i schemalagda granskningar och redigerar slutdatumet eller lägger till/tar bort granskare i enlighet med detta.

Baserat på dina val i **när inställningarna för slut för ande slutförs**, utförs automatiskt tillämpande efter slutdatum för granskningen eller när du stoppar granskningen manuellt. Status för granskningen ändras från **slutförd** till mellanliggande tillstånd som att **tillämpa** och slutligen tillämpa **tillstånd.** Du bör förvänta dig att se till att nekade användare, om de finns, tas bort från roller på några minuter.

## <a name="next-steps"></a>Nästa steg

- [Granska åtkomst till Azure AD-roller](pim-how-to-perform-security-review.md)
- [Slutför en åtkomst granskning av Azure AD-roller](pim-how-to-complete-review.md)
- [Skapa en åtkomst granskning av Azures resurs roller](pim-resource-roles-start-access-review.md)
