---
title: Hantera användare som undantas från principer för villkorlig åtkomst – Azure AD
description: Lär dig hur du använder Azure Active Directory åtkomstgranskningar (Azure AD) för att hantera användare som har undantagits från principer för villkorsstyrd åtkomst
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 12/23/2020
ms.author: barclayn
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: d4b2ac36ad1140968fd17db0bed0b60a8aca6a02
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2021
ms.locfileid: "107532673"
---
# <a name="use-azure-ad-access-reviews-to-manage-users-excluded-from-conditional-access-policies"></a>Använda Azure AD-åtkomstgranskningar för att hantera användare som undantagits från principer för villkorlig åtkomst

I en idealisk värld följer alla användare åtkomstprinciperna för att skydda åtkomsten till organisationens resurser. Ibland finns det dock affärsfall som kräver att du gör undantag. Den här artikeln går igenom några exempel på situationer där exkludering kan vara nödvändigt. Som IT-administratör kan du hantera den här uppgiften, undvika att övervaka principundantag och förse granskare med bevis på att dessa undantag granskas regelbundet med hjälp av Azure Active Directory-åtkomstgranskningar (Azure AD).

>[!NOTE]
> En giltig Azure AD Premium P2-, Enterprise Mobility + Security E5-betal- eller utvärderingslicens krävs för att använda Azure AD-åtkomstgranskningar. Mer information finns i [Azure Active Directory-versioner](../fundamentals/active-directory-whatis.md).

## <a name="why-would-you-exclude-users-from-policies"></a>Varför skulle du undanta användare från principer?

Anta att du som administratör bestämmer dig för att använda villkorsstyrd åtkomst i [Azure AD](../conditional-access/concept-conditional-access-policy-common.md) för att kräva multifaktorautentisering (MFA) och begränsa autentiseringsbegäranden till specifika nätverk eller enheter. Under distributionsplaneringen inser du att inte alla användare kan uppfylla dessa krav. Du kan till exempel ha användare som arbetar från fjärrkontor, inte en del av ditt interna nätverk. Du kan också behöva hantera användare som ansluter med enheter som inte stöds medan de väntar på att enheterna ska ersättas. Kort sagt behöver företaget dessa användare för att logga in och göra sitt jobb så att du undantar dem från principer för villkorlig åtkomst.

Som ett annat exempel kan du använda namngivna platser i [villkorsstyrd](../conditional-access/location-condition.md) åtkomst för att ange en uppsättning länder och regioner som du inte vill att användarna ska få åtkomst till sin klientorganisation från.

![Namngivna platser i villkorsstyrd åtkomst](./media/conditional-access-exclusion/named-locations.png)

Tyvärr kan vissa användare fortfarande ha en giltig anledning att logga in från dessa blockerade länder/regioner. Användare kan till exempel resa i arbetet och behöva åtkomst till företagets resurser. I det här fallet kan principen för villkorlig åtkomst för att blockera dessa länder/regioner använda en molnsäkerhetsgrupp för de exkluderade användarna från principen. Användare som behöver åtkomst när de reser kan lägga till sig själva i gruppen med hjälp av [Självbetjäningsgrupphantering i Azure AD.](../enterprise-users/groups-self-service-management.md)

Ett annat exempel kan vara att du har en princip för villkorsstyrd åtkomst som blockerar äldre [autentisering för de allra flesta av dina användare.](https://cloudblogs.microsoft.com/enterprisemobility/2018/06/07/azure-ad-conditional-access-support-for-blocking-legacy-auth-is-in-public-preview/) Men om du har vissa användare som behöver använda äldre autentiseringsmetoder för att komma åt dina resurser via Office 2010- eller IMAP/SMTP/POP-baserade klienter kan du undanta dessa användare från principen som blockerar äldre autentiseringsmetoder.

>[!NOTE]
>Microsoft rekommenderar starkt att du blockerar användningen av äldre protokoll i din klientorganisation för att förbättra din säkerhetsstatus.

## <a name="why-are-exclusions-challenging"></a>Varför är undantag utmanande?

I Azure AD kan du begränsa en princip för villkorlig åtkomst till en uppsättning användare. Du kan också konfigurera undantag genom att välja Azure AD-roller, enskilda användare eller gäster. Tänk på att när undantag har konfigurerats kan inte principinställningen tillämpas på exkluderade användare. Om undantag konfigureras med hjälp av en lista över användare eller använder äldre lokala säkerhetsgrupper får du begränsad insyn i undantagen. Därför:

- Användarna kanske inte vet att de är exkluderade.

- Användare kan ansluta till säkerhetsgruppen för att kringgå principen.

- Exkluderade användare kan ha kvalificerat sig för undantaget tidigare, men de kanske inte längre är kvalificerade för det.

När du först konfigurerar ett undantag finns det ofta en lista över användare som kringgår principen. Med tiden läggs fler och fler användare till i undantaget och listan växer. Du måste någon gång granska listan och bekräfta att var och en av dessa användare fortfarande är berättigade till undantag. Det kan vara relativt enkelt att hantera undantagslistan ur ett tekniskt perspektiv, men vem fattar affärsbesluten och hur ser du till att allt är granskningsbart? Men om du konfigurerar undantaget med hjälp av en Azure AD-grupp kan du använda åtkomstgranskningar som en kompenserande kontroll för att öka synligheten och minska antalet exkluderade användare.

## <a name="how-to-create-an-exclusion-group-in-a-conditional-access-policy"></a>Så här skapar du en undantagsgrupp i en princip för villkorsstyrd åtkomst

Följ dessa steg för att skapa en ny Azure AD-grupp och en princip för villkorlig åtkomst som inte gäller för den gruppen.

### <a name="create-an-exclusion-group"></a>Skapa en exkluderingsgrupp

1. Logga in på Azure-portalen.

2. I det vänstra navigeringsfönstret klickar **du Azure Active Directory** och sedan på **Grupper.**

3. På den översta menyn klickar du **på Ny grupp** för att öppna gruppfönstret.

4. I listan **Grupptyp** väljer du **Säkerhet.** Ange ett namn och en beskrivning.

5. Se till att ange **Medlemskapstyp** till **Tilldelad**.

6. Välj de användare som ska ingå i den här undantagsgruppen och klicka sedan på **Skapa.**

![Fönstret Ny grupp i Azure Active Directory](./media/conditional-access-exclusion/new-group.png)

### <a name="create-a-conditional-access-policy-that-excludes-the-group"></a>Skapa en princip för villkorlig åtkomst som exkluderar gruppen

Nu kan du skapa en princip för villkorlig åtkomst som använder den här undantagsgruppen.

1. I det vänstra navigeringsfönstret klickar **du Azure Active Directory** och sedan på Villkorlig **åtkomst för** att öppna **bladet** Principer.

2. Klicka **på Ny princip** för att öppna **fönstret** Ny.

3. Ange ett namn.

4. Under Tilldelningar klickar du **på Användare och grupper.**

5. På fliken **Inkludera** väljer du **Alla användare.**

6. På fliken **Exkludera** lägger du till en bockmarkering i **Användare och grupper och klickar** sedan på Välj **exkluderade användare.**

7. Välj den exkluderingsgrupp som du skapade.

   > [!NOTE] 
   > Vi rekommenderar att du undantar minst ett administratörskonto från principen vid testning för att se till att du inte är utelåst från din klientorganisation.

8. Fortsätt med att konfigurera principen för villkorlig åtkomst baserat på organisationens krav.

![Fönstret Välj exkluderade användare i Villkorsstyrd åtkomst](./media/conditional-access-exclusion/select-excluded-users.png)
  
Vi går igenom två exempel där du kan använda åtkomstgranskningar för att hantera undantag i principer för villkorsstyrd åtkomst.

## <a name="example-1-access-review-for-users-accessing-from-blocked-countriesregions"></a>Exempel 1: Åtkomstgranskning för användare som kommer åt från blockerade länder/regioner

Anta att du har en princip för villkorsstyrd åtkomst som blockerar åtkomst från vissa länder/regioner. Den innehåller en grupp som undantas från principen. Här är en rekommenderad åtkomstgranskning där medlemmar i gruppen granskas.

> [!NOTE] 
> En Global administratör administratörsroll krävs för att skapa åtkomstgranskningar.

1. Granskningen sker varje vecka.

2. Slutar aldrig för att se till att du håller den här undantagsgruppen mest uppdaterad.

3. Alla medlemmar i den här gruppen omfattas av granskningen.

4. Varje användare måste självbestyra att de fortfarande behöver åtkomst från dessa blockerade länder/regioner, och därför måste de fortfarande vara medlemmar i gruppen.

5. Om användaren inte svarar på granskningsbegäran tas den bort automatiskt från gruppen och användaren har inte längre åtkomst till klientorganisationen när de reser till dessa länder/regioner.

6. Aktivera e-postaviseringar för att meddela användarna om början och slutförandet av åtkomstgranskningen.

    ![Skapa ett åtkomstgranskningsfönster till exempel 1](./media/conditional-access-exclusion/create-access-review-1.png)

## <a name="example-2-access-review-for-users-accessing-with-legacy-authentication"></a>Exempel 2: Åtkomstgranskning för användare som använder äldre autentisering

Anta att du har en princip för villkorsstyrd åtkomst som blockerar åtkomst för användare som använder äldre autentisering och äldre klientversioner och som innehåller en grupp som är undantagen principen. Här är en rekommenderad åtkomstgranskning där medlemmar i gruppen granskas.

1. Den här granskningen måste vara en återkommande granskning.

2. Alla i gruppen måste granskas.

3. Den kan konfigureras för att lista affärsenhetsägare som valda granskare.

4. Tillämpa resultaten automatiskt och ta bort användare som inte har godkänts för att fortsätta använda äldre autentiseringsmetoder.

5. Det kan vara bra att aktivera rekommendationer så att granskare av stora grupper enkelt kan fatta sina beslut.

6. Aktivera e-postaviseringar så att användarna meddelas om början och slutförandet av åtkomstgranskningen.

    ![Skapa ett åtkomstgranskningsfönster till exempel 2](./media/conditional-access-exclusion/create-access-review-2.png)

>[!IMPORTANT] 
>Om du har många exkluderingsgrupper och därför behöver skapa flera åtkomstgranskningar har vi nu ett API i Microsoft Graph-betaslutpunkten som gör att du kan skapa och hantera dem programmatiskt. Kom igång genom att läsa API-referensen för Azure AD-åtkomstgranskningar och Exempel på hämtning [av Azure AD-åtkomstgranskningar via Microsoft Graph](https://techcommunity.microsoft.com/t5/Azure-Active-Directory/Example-of-retrieving-Azure-AD-access-reviews-via-Microsoft/td-p/236096). [](/graph/api/resources/accessreviewsv2-root?view=graph-rest-beta&preserve-view=true)

## <a name="access-review-results-and-audit-logs"></a>Få åtkomst till granskningsresultat och granskningsloggar

Nu när du har allt på plats, grupp, princip för villkorlig åtkomst och åtkomstgranskningar är det dags att övervaka och spåra resultatet av dessa granskningar.

1. I Azure Portal du bladet **Åtkomstgranskningar.**

2. Öppna kontrollen och programmet som du har skapat för att hantera undantagsgruppen.

3. Klicka **på Resultat** för att se vem som har godkänts för att stanna kvar i listan och vem som har tagits bort.

    ![Resultat för åtkomstgranskningar visar vem som godkändes](./media/conditional-access-exclusion/access-reviews-results.png)

4. Klicka sedan **på Granskningsloggar** för att se de åtgärder som vidtogs under den här granskningen.

    ![Åtkomstgranskningar granskningsloggar med åtgärder](./media/conditional-access-exclusion/access-reviews-audit-logs.png)

Som IT-administratör vet du att det ibland är oundvikligt att hantera exkluderingsgrupper till dina principer. Men att underhålla dessa grupper, regelbundet granska dem av företagsägaren eller användarna själva, och granskning av dessa ändringar kan göras enklare med Azure AD-åtkomstgranskningar.

## <a name="next-steps"></a>Nästa steg

- [Skapa en åtkomstgranskning av grupper eller program](create-access-review.md)
- [Vad är villkorlig åtkomst i Azure Active Directory?](../conditional-access/overview.md)