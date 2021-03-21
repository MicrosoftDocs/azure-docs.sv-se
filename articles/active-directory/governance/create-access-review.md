---
title: Skapa en åtkomst granskning av grupper & program – Azure AD
description: Lär dig hur du skapar en åtkomst granskning av grupp medlemmar eller program åtkomst i Azure Active Directory åtkomst granskningar.
services: active-directory
author: ajburnle
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: compliance
ms.date: 3/3/2021
ms.author: ajburnle
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7143c3f9786d41c32ae954ab219197a9cfaa1050
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102176883"
---
# <a name="create-an-access-review-of-groups-and-applications-in-azure-ad-access-reviews"></a>Skapa en åtkomst granskning av grupper och program i åtkomst granskningar för Azure AD

Åtkomst till grupper och program för medarbetare och gäster förändras över tid. För att minska risken som är kopplad till inaktuella åtkomst tilldelningar kan administratörer använda Azure Active Directory (Azure AD) för att skapa åtkomst granskningar för grupp medlemmar eller program åtkomst. Om du behöver granska åtkomst rutinmässigt kan du också skapa återkommande åtkomst granskningar. Mer information om dessa scenarier finns i [hantera användar åtkomst](manage-user-access-with-access-reviews.md) och [Hantera gäst åtkomst](manage-guest-access-with-access-reviews.md).

Du kan se en snabb video som talar om hur du aktiverar åtkomst granskningar:

>[!VIDEO https://www.youtube.com/embed/X1SL2uubx9M]

Den här artikeln beskriver hur du skapar en eller flera åtkomst granskningar för grupp medlemmar eller program åtkomst.

## <a name="prerequisites"></a>Förutsättningar

- Azure AD Premium P2
- Global administratör eller användar administratör

Mer information finns i [licens krav](access-reviews-overview.md#license-requirements).

## <a name="create-one-or-more-access-reviews"></a>Skapa en eller flera åtkomst granskningar

1. Logga in på Azure Portal och öppna [sidan identitets styrning](https://portal.azure.com/#blade/Microsoft_AAD_ERM/DashboardBlade/).

2. Klicka på **åtkomst granskningar** på den vänstra menyn.

3. Klicka på **ny åtkomst granskning** för att skapa en ny åtkomst granskning.

    ![Fönstret åtkomst granskningar i identitets styrning](./media/create-access-review/access-reviews.png)

4. I **steg 1: Välj vad du vill granska och** Välj vilken resurs som du vill granska.

    ![Skapa en åtkomst granskning – granska namn och beskrivning](./media/create-access-review/select-what-review.png)

5. Om du har valt **Teams + grupper** i steg 1 har du två alternativ i steg 2
   - **Alla Microsoft 365 grupper med gäst användare.** Välj det här alternativet om du vill skapa återkommande granskningar för alla gäst användare i alla dina Microsoft-team och M365-grupper i din organisation. Du kan välja att undanta vissa grupper genom att klicka på Välj grupp (er) att undanta.
   - **Välj Teams + grupper.** Välj det här alternativet om du vill ange en begränsad uppsättning team och/eller grupper att granska. När du har klickat på det här alternativet visas en lista över grupper till höger att välja från.

     ![Team och grupper](./media/create-access-review/teams-groups.png)

     ![Team och grupper som valts i användar gränssnittet](./media/create-access-review/teams-groups-detailed.png)

6. Om du har valt **program** i steg 1 kan du sedan välja ett eller flera program i steg 2.

    >[!NOTE]
    > Om du väljer flera grupper och/eller program leder det till att flera åtkomst granskningar har skapats. Om du till exempel väljer 5 grupper som ska granskas resulterar det i 5 separata åtkomst granskningar

   ![Gränssnittet som visas om du väljer program i stället för grupper](./media/create-access-review/select-application-detailed.png)

7. I steg 3 kan du sedan välja ett omfång för granskningen. Dina alternativ är
   - **Endast gäst användare.** Om du väljer det här alternativet begränsas åtkomst granskningen enbart till Azure AD B2B-gäst användare i din katalog.
   - **Grupperna.** Om du väljer det här alternativet omfångs åtkomst granskningen till alla användar objekt som är associerade med resursen.

    >[!NOTE]
    > Om du har valt alla Microsoft 365 grupper med gäst användare i steg 2, är ditt enda alternativ att granska gäst användare i steg 3

8. Klicka på Nästa: recensioner
9. I avsnittet **Välj granskare** väljer du antingen en eller flera personer för att utföra åtkomst granskningarna. Du kan välja mellan:
    - **Grupp ägare (er)** (endast tillgängligt när du utför en granskning på ett team eller en grupp)
    - **Valda användare eller grupper**
    - **Användare granskar egen åtkomst**
    - **Användares chefer.**
    Om du väljer antingen **Administratörer av användare** eller **grupp ägare**  kan du också välja att ange en återställnings granskare. Återställnings granskare uppmanas att göra en granskning när användaren inte har angett någon ansvarig i katalogen eller om gruppen inte har någon ägare.

    ![ny åtkomst granskning](./media/create-access-review/new-access-review.png)

10. I avsnittet **Ange upprepning av granskning** kan du ange en frekvens, till exempel **varje vecka, varje månad, varje kvartal, varje halvår, varje år**. Sedan anger du en **varaktighet** som definierar hur länge en granskning ska vara öppen för inmatade granskare. Den längsta tid som du kan ange för en månatlig granskning är till exempel 27 dagar för att undvika överlappande granskningar. Du kanske vill förkorta varaktigheten för att se till att dina inmatade granskare används tidigare. Sedan kan du välja ett **start datum** och **slutdatum**.

    ![Välj hur ofta granskningen ska ske](./media/create-access-review/frequency.png)

11. Klicka på **Nästa: inställningar** -knappen längst ned på sidan
12. När du har slutfört **inställningarna** kan du ange vad som händer när granskningen är klar

    ![Skapa en åtkomst granskning-när inställningarna slutförs](./media/create-access-review/upon-completion-settings-new.png)

Om du vill ta bort åtkomsten för nekade användare automatiskt anger du Använd automatiskt tillämpa resultat till resurs för att aktivera. Om du vill tillämpa resultaten manuellt när granskningen är klar ställer du in växeln på Inaktivera.
Använd listan om granskare inte svarar för att ange vad som händer för användare som inte granskas av granskaren under gransknings perioden. Den här inställningen påverkar inte användare som har granskats manuellt av granskarna. Om den sista granskaren av beslutet är neka tas användarens åtkomst bort.

- **Ingen ändring** – lämna användarens åtkomst oförändrad
- **Ta bort åtkomst** – ta bort användares åtkomst
- **Godkänn åtkomst** – Godkänn användarens åtkomst
- **Ta rekommendationer** – beakta systemets rekommendation om att neka eller godkänna användarens fortsatta åtkomst

    ![Vid inställnings alternativ för slut för ande](./media/create-access-review/upon-completion-settings-new.png)

Använd åtgärden som ska tillämpas på nekade **gäst** användare för att ange vad som händer för gäst användare om de nekas.
- Ta bort användarens medlemskap från resursen tar bort nekad användares åtkomst till gruppen eller programmet som granskas, men de kommer fortfarande att kunna logga in till klienten.
- Blockera användare från att logga in i 30 dagar och ta sedan bort användare från klienten som nekade användare från att logga in till klienten, oavsett om de har åtkomst till andra resurser. Om det var fel eller om en administratör bestämmer sig för att återaktivera en åtkomst, kan de göra det inom 30 dagar efter att användaren har inaktiverats. Om ingen åtgärd vidtas för de inaktiverade användarna tas de bort från klienten.

Om du vill veta mer om metod tips för att ta bort gäst användare som inte längre har åtkomst till resurser i din organisation läser du artikeln [med rubriken använd Azure AD Identity Governance för att granska och ta bort externa användare som inte längre har åtkomst till resursen.](access-reviews-external-users.md)

   >[!NOTE]
   >Åtgärder som ska tillämpas på nekade gäst användare kan inte konfigureras för recensioner som är begränsade till fler än gäst användare. Den kan inte heller konfigureras för granskningar av **alla M365-grupper med gäst användare.** När det inte går att konfigurera används standard alternativet för att ta bort användarens medlemskap från resursen för nekade användare.

13. I avsnittet **Aktivera gransknings hjälp** väljer du om du vill att granskaren ska få rekommendationer under gransknings processen.

    ![Aktivera alternativ för besluts hjälp](./media/create-access-review/helpers.png)

14. I avsnittet **Avancerade inställningar** kan du välja följande
    - Ange en **motivering som krävs** för att göra det **möjligt** för granskaren att ange en orsak till godkännande.
    - Ange **e-postaviseringar** för att **tillåta** att Azure AD skickar e-postmeddelanden till granskare när en åtkomst granskning startar och till administratörer när en granskning har slutförts.
    - Ställ in **påminnelser** för att **tillåta** att Azure AD skickar påminnelser om åtkomst granskningar till granskare som inte har slutfört sin granskning. Dessa påminnelser är själv hälften av tiden för granskningen.
    - Innehållet i e-postmeddelandet som skickas till granskare genereras automatiskt baserat på gransknings informationen, till exempel gransknings namn, resurs namn, förfallo datum osv. Om du behöver ett sätt att kommunicera ytterligare information, till exempel ytterligare instruktioner eller kontakt information, kan du ange den här informationen i avsnittet **ytterligare innehåll för e-post i granskare** . Den information som du anger inkluderas i e-postinbjudan och påminnelse-e-postmeddelanden som skickas till tilldelade granskare. Avsnittet som är markerat i bilden nedan visar var den här informationen visas.


      ![ytterligare innehåll för granskare](./media/create-access-review/additional-content-reviewer.png)

15. Klicka på **Nästa: granska + skapa** för att gå till nästa sida
16. Ge åtkomst granskningen namnet. Du kan också ange en beskrivning av granskningen. Namnet och beskrivningen visas för granskarna.
17. Granska informationen och välj **skapa**

       ![Skapa gransknings skärmen](./media/create-access-review/create-review.png)

## <a name="start-the-access-review"></a>Starta åtkomst granskningen

När du har angett inställningarna för åtkomst granskning klickar du på **Start**. Åtkomst granskningen visas i listan med en indikator för dess status.

![Lista över åtkomst granskningar och deras status](./media/create-access-review/access-reviews-list.png)

Som standard skickar Azure AD ett e-postmeddelande till granskare strax efter att granskningen startar. Om du väljer att inte låta Azure AD skicka e-postmeddelandet måste du meddela granskarna att en åtkomst granskning väntar på att de ska slutföras. Du kan visa dem i anvisningarna för hur du [granskar åtkomst till grupper eller program](perform-access-review.md). Om granskningen är till för gäster för att granska sin egen åtkomst, visar du dem i anvisningarna för att [Granska åtkomsten för dig själv till grupper eller program](review-your-access.md).

Om du har tilldelat gäster som granskare och de inte har accepterat inbjudan får han eller hon ingen e-post från åtkomst granskningar eftersom de först måste godkänna inbjudan innan de kan granskas.

## <a name="access-review-status-table"></a>Status tabell för åtkomst granskning

| Status | Definition |
|--------|------------|
|NotStarted | Granskningen skapades, användar identifieringen väntar på att starta. |
|Initierar   | Användar identifiering pågår för att identifiera alla användare som ingår i granskningen. |
|Startar | Granskningen startas. Om e-postaviseringar har Aktiver ATS skickas e-postmeddelanden till granskare. |
|Pågår | Granskning har startat. Om e-postaviseringar har Aktiver ATS har e-postmeddelanden skickats till granskarna. Granskare kan skicka beslut fram till förfallo datumet. |
|Slutföra | Granskningen slutförs och e-postmeddelanden skickas till gransknings ägaren. |
|Automatisk visning | Granskning är i ett system gransknings steg. Systemet registrerar beslut för användare som inte har granskats utifrån rekommendationer eller förkonfigurerade beslut. |
|Automatiskt granskad | Beslut har registrerats av systemet för alla användare som inte har granskats. Granskning är redo att fortsätta att **tillämpa** om Auto-Apply har Aktiver ATS. |
|Lägger | Det kommer inte att gå att komma åt användare som har godkänts. |
|Tillämpat | Nekade användare, om sådana finns, har tagits bort från resursen eller katalogen. |
|Misslyckad | Granskningen kunde inte fortsätta. Det här felet kan bero på borttagning av klienten, en ändring i licenser eller andra interna klient ändringar. |

## <a name="create-reviews-via-apis"></a>Skapa recensioner via API: er

Du kan också skapa åtkomst granskningar med hjälp av API: er. Vad du gör för att hantera åtkomst granskningar av grupper och program användare i Azure Portal kan också göras med hjälp av Microsoft Graph-API: er. Mer information finns i API- [referens för Azure AD Access-granskning](/graph/api/resources/accessreviews-root?view=graph-rest-beta). Ett kod exempel finns i [exempel på hämtning av åtkomst granskningar för Azure AD via Microsoft Graph](https://techcommunity.microsoft.com/t5/Azure-Active-Directory/Example-of-retrieving-Azure-AD-access-reviews-via-Microsoft/m-p/236096).

## <a name="next-steps"></a>Nästa steg

- [Granska åtkomst till grupper eller program](perform-access-review.md)
- [Granska åtkomst för dig själv till grupper eller program](review-your-access.md)
- [Slutför en åtkomst granskning av grupper eller program](complete-access-review.md)