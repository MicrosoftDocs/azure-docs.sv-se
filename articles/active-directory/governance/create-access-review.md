---
title: Skapa en åtkomstgranskning av grupper & program – Azure AD
description: Lär dig hur du skapar en åtkomstgranskning av gruppmedlemmar eller programåtkomst i Azure Active Directory åtkomstgranskningar.
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
ms.openlocfilehash: f4d6502ffdd13272d396852b11a11d13f929b11b
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2021
ms.locfileid: "107532268"
---
# <a name="create-an-access-review-of-groups-and-applications-in-azure-ad-access-reviews"></a>Skapa en åtkomstgranskning av grupper och program i Azure AD-åtkomstgranskningar

Åtkomsten till grupper och program för anställda och gäster ändras över tid. För att minska risken för inaktuella åtkomsttilldelningar kan administratörer använda Azure Active Directory (Azure AD) för att skapa åtkomstgranskningar för gruppmedlemmar eller programåtkomst. Om du regelbundet behöver granska åtkomsten kan du även skapa återkommande åtkomstgranskningar. Mer information om dessa scenarier finns i [Hantera användaråtkomst och](manage-user-access-with-access-reviews.md) [Hantera gäståtkomst.](manage-guest-access-with-access-reviews.md)

Du kan titta på en snabbvideo som pratar om att aktivera åtkomstgranskningar:

>[!VIDEO https://www.youtube.com/embed/X1SL2uubx9M]

Den här artikeln beskriver hur du skapar en eller flera åtkomstgranskningar för gruppmedlemmar eller programåtkomst.

## <a name="prerequisites"></a>Förutsättningar

- Azure AD Premium P2
- Global administratör eller användaradministratör

Mer information finns i [Licenskrav.](access-reviews-overview.md#license-requirements)

## <a name="create-one-or-more-access-reviews"></a>Skapa en eller flera åtkomstgranskningar

1. Logga in på Azure Portal och öppna sidan [Identity Governance (Identitetsstyrning).](https://portal.azure.com/#blade/Microsoft_AAD_ERM/DashboardBlade/)

2. I den vänstra menyn klickar du på **Åtkomstgranskningar.**

3. Klicka **på Ny åtkomstgranskning** för att skapa en ny åtkomstgranskning.

    ![Fönstret Åtkomstgranskningar i Identity Governance](./media/create-access-review/access-reviews.png)

4. I **Steg 1: Välj vad du vill granska** och välj vilken resurs du vill granska.

    ![Skapa en åtkomstgranskning – Granska namn och beskrivning](./media/create-access-review/select-what-review.png)

5. Om du valde **Teams + Grupper** i steg 1 har du två alternativ i steg 2
   - **Alla Microsoft 365 grupper med gästanvändare.** Välj det här alternativet om du vill skapa återkommande granskningar för alla gästanvändare i alla dina Microsoft Teams- och M365-grupper i din organisation. Du kan välja att undanta vissa grupper genom att klicka på Välj vilka grupper som ska undantas.
   - **Välj team + grupper.** Välj det här alternativet om du vill ange en begränsad uppsättning team och/eller grupper att granska. När du klickar på det här alternativet visas en lista över grupper till höger att välja bland.

     ![Team och grupper](./media/create-access-review/teams-groups.png)

     ![Team och grupper som valts i användargränssnittet](./media/create-access-review/teams-groups-detailed.png)

6. Om du valde **Program** i steg 1 kan du välja ett eller flera program i steg 2.

    >[!NOTE]
    > Om du väljer flera grupper och/eller program skapas flera åtkomstgranskningar. Om du till exempel väljer 5 grupper att granska resulterar det i 5 separata åtkomstgranskningar

   ![Gränssnittet visas om du väljer program i stället för grupper](./media/create-access-review/select-application-detailed.png)

7. I steg 3 kan du sedan välja ett omfång för granskningen. Alternativen är
   - **Endast gästanvändare.** Om du väljer det här alternativet begränsas åtkomstgranskningen till endast Azure AD B2B-gästanvändare i din katalog.
   - **Alla.** Om du väljer det här alternativet omfattas åtkomstgranskningen av alla användarobjekt som är associerade med resursen.

    >[!NOTE]
    > Om du valde Alla Microsoft 365 med gästanvändare i steg 2 är det enda alternativet att granska gästanvändare i steg 3

8. Klicka på Nästa: Granskningar
9. I avsnittet **Välj granskare** väljer du antingen en eller flera personer för att utföra åtkomstgranskningarna. Du kan välja mellan:
    - **Gruppägare (endast tillgängligt** när du utför en granskning i ett team eller en grupp)
    - **Valda användare eller grupper**
    - **Användare granskar egen åtkomst**
    - **Användares chefer.**
    Om du väljer **antingen Chefer för** användare **eller Gruppägare**  kan du också ange en återställningsg granskare. Fallback-granskare uppmanas att göra en granskning när användaren inte har någon chef angiven i katalogen eller om gruppen inte har någon ägare.

    ![ny åtkomstgranskning](./media/create-access-review/new-access-review.png)

10. I avsnittet **Ange upprepning av granskning** kan du ange en frekvens som Varje **vecka, Varje månad, Varje kvartal, Varje halvår, Varje år, Varje år.** Sedan anger du en **Varaktighet**, som definierar hur länge en granskning ska vara öppen för indata från granskare. Den maximala varaktighet som du kan ange för en månatlig granskning är till exempel 27 dagar för att undvika överlappande granskningar. Du kanske vill förkorta varaktigheten för att säkerställa att granskarens indata tillämpas tidigare. Därefter kan du välja **startdatum och** **slutdatum.**

    ![Välj hur ofta granskningen ska ske](./media/create-access-review/frequency.png)

11. Klicka på **knappen Nästa:** Inställningar längst ned på sidan
12. I inställningarna **för Vid slutförande** kan du ange vad som händer när granskningen har slutförts

    ![Skapa en åtkomstgranskning – när slutförandeinställningarna har slutförts](./media/create-access-review/upon-completion-settings-new.png)

Om du vill ta bort åtkomst automatiskt för nekade användare ställer du in Tillämpa resultat automatiskt på resurs på Aktivera. Om du vill tillämpa resultaten manuellt när granskningen är klar ställer du in växeln på Inaktivera.
Använd listan Om granskare inte svarar för att ange vad som händer för användare som inte granskas av granskaren inom granskningsperioden. Den här inställningen påverkar inte användare som har granskats manuellt av granskarna. Om den slutliga granskarens beslut är Neka tas användarens åtkomst bort.

- **Ingen ändring** – Lämna användarens åtkomst oförändrad
- **Ta bort** åtkomst – Ta bort användarens åtkomst
- **Godkänn åtkomst** – Godkänn användarens åtkomst
- **Ta rekommendationer** – Ta systemets rekommendation om att neka eller godkänna användarens fortsatt åtkomst

    ![Alternativ för inställningar vid slutförande](./media/create-access-review/upon-completion-settings-new.png)

Använd åtgärden för att tillämpa på nekade **gästanvändare** för att ange vad som ska hända för gästanvändare om de nekas.
- Ta bort användarens medlemskap från resursen tar bort nekad användares åtkomst till gruppen eller programmet som granskas, kommer de fortfarande att kunna logga in på klienten.
- Blockera användare från att logga in i 30 dagar och ta sedan bort användare från klientorganisationen blockerar nekade användare från att logga in på klienten, oavsett om de har åtkomst till andra resurser. Om det har skett ett misstag eller om en administratör bestämmer sig för att återaktivera åtkomsten kan administratören göra det inom 30 dagar efter att användaren har inaktiverats. Om inga åtgärder vidtas för de inaktiverade användarna tas de bort från klientorganisationen.

Mer information om metodtips för att ta bort gästanvändare som inte längre har åtkomst till resurser i din organisation finns i artikeln Använda Azure AD Identity Governance för att granska och ta bort externa användare som inte längre har [resursåtkomst.](access-reviews-external-users.md)

   >[!NOTE]
   >Åtgärden som ska tillämpas på nekade gästanvändare kan inte konfigureras för granskningar som är begränsade till mer än gästanvändare. Det kan inte heller konfigureras för granskningar av **alla M365-grupper med gästanvändare.** Om det inte går att konfigurera används standardalternativet att ta bort användarens medlemskap från resursen för nekade användare.

13. I Aktivera **beslutshjälp för granskning** väljer du om du vill att granskaren ska få rekommendationer under granskningsprocessen.

    ![Aktivera alternativ för beslutshjälp](./media/create-access-review/helpers.png)

14. I **avsnittet Avancerade** inställningar kan du välja följande
    - Ställ **in Motivering som** krävs **på** Aktivera för att kräva att granskaren anger en orsak till godkännandet.
    - Ställ in **e-postaviseringar** på Aktivera om du vill att Azure AD ska skicka e-postmeddelanden till granskare när en åtkomstgranskning startar och till administratörer när en granskning har slutförts. 
    - Ställ **in Påminnelser** **på Aktivera** om du vill att Azure AD ska skicka påminnelser om pågående åtkomstgranskningar till granskare som inte har slutfört sin granskning. Dessa påminnelser kommer att vara själv halvvägs genom granskningens varaktighet.
    - Innehållet i e-postmeddelandet som skickas till granskare genereras automatiskt baserat på granskningsinformationen, till exempel granskningsnamn, resursnamn, förfallodatum osv. Om du behöver ett sätt att kommunicera ytterligare information, till exempel ytterligare instruktioner eller kontaktinformation, kan du ange den här informationen i avsnittet Ytterligare **innehåll för granskarens e-post.** Den information som du anger ingår i inbjudan och påminnelser som skickas till tilldelade granskare. Avsnittet som är markerat i bilden nedan visar var den här informationen visas.


      ![ytterligare innehåll för granskaren](./media/create-access-review/additional-content-reviewer.png)

15. Klicka på **Nästa: Granska + skapa** för att gå till nästa sida
16. Ge åtkomstgranskningen ett namn. Du kan också ge granskningen en beskrivning. Namnet och beskrivningen visas för granskarna.
17. Granska informationen och välj **Skapa**

       ![skärmen skapa granskning](./media/create-access-review/create-review.png)

## <a name="start-the-access-review"></a>Starta åtkomstgranskningen

När du har angett inställningarna för en åtkomstgranskning klickar du på **Starta**. Åtkomstgranskningen visas i listan med en indikator på dess status.

![Lista över åtkomstgranskningar och deras status](./media/create-access-review/access-reviews-list.png)

Som standard skickar Azure AD ett e-postmeddelande till granskare strax efter att granskningen har startat. Om du väljer att inte få Azure AD att skicka e-postmeddelandet måste du informera granskarna om att en åtkomstgranskning väntar på att de ska slutföras. Du kan visa dem instruktionerna för hur du [granskar åtkomsten till grupper eller program.](perform-access-review.md) Om din granskning är till för gäster att granska sin egen åtkomst, visar du dem anvisningarna för hur du granskar åtkomst för dig [själv till grupper eller program.](review-your-access.md)

Om du har tilldelat gäster som granskare och de inte har accepterat inbjudan får de inget e-postmeddelande från åtkomstgranskningar eftersom de först måste godkänna inbjudan innan de granskar den.

## <a name="access-review-status-table"></a>Statustabell för åtkomstgranskning

| Status | Definition |
|--------|------------|
|Inte startad | Granskningen har skapats och användaridentifiering väntar på att starta. |
|Initierar   | Användaridentifiering pågår för att identifiera alla användare som ingår i granskningen. |
|Startar | Granskningen börjar. Om e-postaviseringar är aktiverade skickas e-postmeddelanden till granskare. |
|InProgress | Granskningen har startat. Om e-postaviseringar är aktiverade har e-postmeddelanden skickats till granskare. Granskare kan skicka beslut fram till förfallodatumet. |
|Slutföra | Granskningen slutförs och e-postmeddelanden skickas till granskningsägaren. |
|Granska automatiskt | Granskningen är i ett systemgranskningssteg. Systemet registrerar beslut för användare som inte har granskats baserat på rekommendationer eller förkonfigurerade beslut. |
|Granskas automatiskt | Beslut har registrerats av systemet för alla användare som inte har granskats. Granskning är redo att fortsätta **till Tillämpa** om Tillämpa automatiskt är aktiverat. |
|Tillämpa | Åtkomsten för användare som har godkänts ändras inte. |
|Tillämpat | Nekade användare, om några, har tagits bort från resursen eller katalogen. |
|Misslyckad | Granskning kunde inte fortskrida. Det här felet kan vara relaterat till borttagning av klientorganisationen, en ändring av licenser eller andra interna klientändringar. |

## <a name="create-reviews-via-apis"></a>Skapa granskningar via API:er

Du kan också skapa åtkomstgranskningar med hjälp av API:er. Det du gör för att hantera åtkomstgranskningar av grupper och programanvändare i Azure Portal kan också göras med hjälp av Microsoft Graph-API:er. Mer information finns i API-referensen [för Azure AD-åtkomstgranskningar.](/graph/api/resources/accessreviewsv2-root?view=graph-rest-beta&preserve-view=true) Ett kodexempel finns i Exempel [på hämtning av Azure AD-åtkomstgranskningar via Microsoft Graph](https://techcommunity.microsoft.com/t5/Azure-Active-Directory/Example-of-retrieving-Azure-AD-access-reviews-via-Microsoft/m-p/236096).

## <a name="next-steps"></a>Nästa steg

- [Granska åtkomst till grupper eller program](perform-access-review.md)
- [Granska åtkomst för dig själv till grupper eller program](review-your-access.md)
- [Slutför en åtkomstgranskning av grupper eller program](complete-access-review.md)