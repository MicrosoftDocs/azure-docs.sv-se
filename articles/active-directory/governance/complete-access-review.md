---
title: Slutför en åtkomst granskning av grupper & program – Azure AD
description: Lär dig hur du utför en åtkomst granskning av grupp medlemmar eller program åtkomst i Azure Active Directory åtkomst granskningar.
services: active-directory
documentationcenter: ''
author: ajburnle
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: compliance
ms.date: 02/08/2021
ms.author: ajburnle
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: e31b9ce115ebe0723a9d082aaddd8056e486ed27
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102176985"
---
# <a name="complete-an-access-review-of-groups-and-applications-in-azure-ad-access-reviews"></a>Slutför en åtkomst granskning av grupper och program i åtkomst granskningar för Azure AD

Som administratör kan du [skapa en åtkomst granskning av grupper eller program](create-access-review.md) och granskare som [utför åtkomst granskningen](perform-access-review.md). Den här artikeln beskriver hur du visar resultatet av åtkomst granskningen och tillämpar resultatet.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

## <a name="prerequisites"></a>Förutsättningar

- Azure AD Premium P2
- Global administratör, användar administratör, säkerhets administratör eller säkerhets läsare

Mer information finns i [licens krav](access-reviews-overview.md#license-requirements).

## <a name="view-an-access-review"></a>Visa en åtkomst granskning

Du kan följa förloppet när granskarna har slutfört granskningarna.

1. Logga in på Azure Portal och öppna [sidan identitets styrning](https://portal.azure.com/#blade/Microsoft_AAD_ERM/DashboardBlade/).

1. Klicka på **åtkomst granskningar** på den vänstra menyn.

1. I listan klickar du på en åtkomst granskning.

    Om du vill visa framtida instanser av åtkomst granskningar går du till åtkomst granskningen och väljer schemalagda granskningar.

    På sidan **Översikt** kan du se förloppet för den aktuella instansen. Ingen åtkomst behörighet har ändrats i katalogen förrän granskningen har slutförts.

     ![Granskning av alla företags grupper](./media/complete-access-review/all-company-group.png)

    Alla blad under aktuella visas bara under hela gransknings instansens varaktighet. 

    Sidan resultat innehåller mer information om varje användare under granskning i instansen, inklusive möjligheten att stoppa, återställa och hämta resultat.

    ![Granska gäst åtkomst över Microsoft 365 grupper](./media/complete-access-review/all-company-group-results.png)


    Om du visar en åtkomst granskning som granskar gäst åtkomst över Microsoft 365 grupper, visar översikts bladet varje grupp i granskningen. 
   
    ![granska gäst åtkomst över Microsoft 365 grupper](./media/complete-access-review/review-guest-access-across-365-groups.png)

    Klicka på en grupp för att se förloppet för granskningen av gruppen, samt för att stoppa, återställa, tillämpa och ta bort.

   ![granska gäst åtkomst över Microsoft 365 grupper i detalj](./media/complete-access-review/progress-group-review.png)

1. Om du vill stoppa en åtkomst granskning innan det har nått det schemalagda slutdatumet klickar du på knappen **stoppa** .

    När du stoppar en granskning kommer granskarna inte längre att kunna ge svar. Du kan inte starta om en granskning när den har stoppats.

1. Om du inte längre är intresse rad av åtkomst granskningen kan du ta bort den genom att klicka på knappen **ta bort** .

## <a name="apply-the-changes"></a>Tillämpa ändringarna

Om **automatiskt tillämpa resultat på resursen** har Aktiver ATS baserat på dina val i när inställningarna för slut för **ande har slutförts**, utförs automatisk komplettering efter granskningens slutdatum eller när du stoppar granskningen manuellt.

Om alternativet **tillämpa resultat automatiskt på resursen** inte har Aktiver ATS för granskningen navigerar du till **gransknings historik** under **serie** efter att gransknings varaktigheten har gått ut eller om granskningen stoppades tidigt, och klickar på den instans av granskningen som du vill använda.

![Tillämpa ändringar av åtkomst granskning](./media/complete-access-review/apply-changes.png)

Tillämpa ändringarna manuellt genom att klicka på **tillämpa** . Om en användares åtkomst nekades i granskningen och du klickar på **Verkställ**, tar Azure AD bort sina medlemskap eller program tilldelningar.

![Använd knappen Använd ändringar för åtkomst granskning](./media/complete-access-review/apply-changes-button.png)


Status för granskningen ändras från **slutförd** till mellanliggande tillstånd, till exempel att **tillämpa** och slutligen för tillstånds **resultat som tillämpas**. Du bör förvänta dig att se till att nekade användare, om de finns, tas bort från grupp medlemskapet eller program tilldelningen om några minuter.

Manuellt eller automatiskt tillämpade resultat påverkar inte en grupp som kommer från en lokal katalog eller en dynamisk grupp. Om du vill ändra en grupp som har sitt ursprung lokalt hämtar du resultaten och tillämpar ändringarna i åter givningen av gruppen i den katalogen.

## <a name="retrieve-the-results"></a>Hämta resultaten

Klicka på **resultat** sidan om du vill visa resultatet för en enstaka åtkomst granskning. Om du bara vill visa en användares åtkomst går du till rutan Sök och anger visnings namnet eller User Principal Name för en användare vars åtkomst granskades.

![Hämta resultat för en åtkomst granskning](./media/complete-access-review/retrieve-results.png) 

Om du vill visa förloppet för en aktiv åtkomst granskning som är återkommande klickar du på **resultat** sidan.

Om du vill visa resultatet från en slutförd instans av en åtkomst granskning som är återkommande klickar du på **gransknings historik** och väljer sedan den aktuella instansen i listan över slutförda åtkomst gransknings instanser, baserat på instansens start-och slutdatum. Resultatet från den här instansen kan hämtas från **resultat** sidan.

Om du vill hämta alla resultat från en åtkomst granskning klickar du på knappen **Ladda ned** . Den resulterande CSV-filen kan visas i Excel eller i andra program som öppnar CSV-filer med UTF-8.

## <a name="remove-users-from-an-access-review"></a>Ta bort användare från en åtkomst granskning

 Som standard är en användare som tas bort borttagen i Azure AD i 30 dagar. Under den här perioden kan användaren återställas av en administratör om det behövs.  Efter 30 dagar tas användaren bort permanent.  Med hjälp av Azure Active Directory Portal kan en global administratör dessutom uttryckligen [ta bort en nyligen borttagen användare](../fundamentals/active-directory-users-restore.md) innan tids perioden uppnås.  När en användare har tagits bort permanent tas efterföljande data om den användaren bort från aktiva åtkomstgranskningar.  Granskningsinformation om borttagna användare finns kvar i spårningsloggen.

## <a name="next-steps"></a>Nästa steg

- [Hantera användare med Azure AD-åtkomstgranskningar](manage-user-access-with-access-reviews.md)
- [Hantera gäståtkomst med Azure AD-åtkomstgranskningar](manage-guest-access-with-access-reviews.md)
- [Skapa en åtkomst granskning av grupper eller program](create-access-review.md)
- [Skapa en åtkomstgranskning av användarna i en administrativ roll i Azure AD](../privileged-identity-management/pim-how-to-start-security-review.md)
