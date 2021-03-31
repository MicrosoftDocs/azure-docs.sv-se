---
title: Hantera användare som uteslutits från principer för villkorlig åtkomst – Azure AD
description: Lär dig hur du använder Azure Active Directory (Azure AD) åtkomst granskningar för att hantera användare som har uteslutits från principer för villkorlig åtkomst
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
ms.openlocfilehash: 426e28048ae370919529ea710717a3a3867d999d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "97746260"
---
# <a name="use-azure-ad-access-reviews-to-manage-users-excluded-from-conditional-access-policies"></a>Använd åtkomst granskningar i Azure AD för att hantera användare som uteslutits från principer för villkorlig åtkomst

I en idealisk värld följer alla användare åtkomst principerna för att skydda åtkomsten till organisationens resurser. Ibland finns det dock affärs fall där du behöver göra undantag. Den här artikeln går igenom några exempel på situationer där det kan vara nödvändigt med undantag. Som IT-administratör kan hantera den här uppgiften, undvika överblick i princip undantag och tillhandahålla granskare med bevis på att dessa undantag granskas regelbundet med hjälp av Azure Active Directory (Azure AD) åtkomst granskningar.

>[!NOTE]
> En giltig Azure AD Premium P2, Enterprise Mobility + Security E5 betald eller utvärderings licens krävs för att använda åtkomst granskningar för Azure AD. Mer information finns i [Azure Active Directory-versioner](../fundamentals/active-directory-whatis.md).

## <a name="why-would-you-exclude-users-from-policies"></a>Varför skulle du undanta användare från principer?

Anta att du som administratör bestämmer dig för att använda [villkorlig åtkomst för Azure AD](../conditional-access/concept-conditional-access-policy-common.md) för att kräva Multi-Factor Authentication (MFA) och begränsa autentiseringsbegäranden till vissa nätverk eller enheter. Under distributions planeringen inser du att inte alla användare kan uppfylla dessa krav. Du kan till exempel ha användare som arbetar från fjärranslutna kontor, inte en del av det interna nätverket. Du kan också behöva hantera användare som ansluter med hjälp av enheter som inte stöds under väntan på att enheterna ska ersättas. I korthet kräver företaget att dessa användare loggar in och utför sitt jobb så att du exkluderar dem från principer för villkorlig åtkomst.

Ett annat exempel är om du använder [namngivna platser](../conditional-access/location-condition.md) i villkorlig åtkomst för att ange en uppsättning länder och regioner som du inte vill att användarna ska ha åtkomst till av klienten.

![Namngivna platser i villkorlig åtkomst](./media/conditional-access-exclusion/named-locations.png)

Vissa användare kan tyvärr fortfarande ha en giltig anledning att logga in från dessa blockerade länder/regioner. Användare kan till exempel vara resande om arbete och behöver åtkomst till företags resurser. I det här fallet kan principen för villkorlig åtkomst för att blockera dessa länder/regioner använda en moln säkerhets grupp för de exkluderade användarna från principen. Användare som behöver åtkomst under resor kan lägga till sig själva i gruppen med hjälp av [Azure AD-](../enterprise-users/groups-self-service-management.md)självbetjänings grupp hantering.

Ett annat exempel är att du har en princip för villkorlig åtkomst som [blockerar äldre autentisering för de flesta användare](https://cloudblogs.microsoft.com/enterprisemobility/2018/06/07/azure-ad-conditional-access-support-for-blocking-legacy-auth-is-in-public-preview/). Men om du har vissa användare som behöver använda äldre autentiseringsmetoder för att komma åt dina resurser via Office 2010 eller IMAP/SMTP/POP-baserade klienter kan du undanta dessa användare från principen som blockerar äldre autentiseringsmetoder.

>[!NOTE]
>Microsoft rekommenderar starkt att du blockerar användningen av äldre protokoll i din klient organisation för att förbättra din säkerhets position.

## <a name="why-are-exclusions-challenging"></a>Varför är undantagen utmanande?

I Azure AD kan du begränsa en princip för villkorlig åtkomst till en uppsättning användare. Du kan också konfigurera undantag genom att välja Azure AD-roller, enskilda användare eller gäster. Tänk på att när undantag har kon figurer ATS kan inte princip avsikten tillämpas på undantagna användare. Om undantag konfigureras med hjälp av en lista över användare eller om du använder äldre lokala säkerhets grupper får du begränsad insyn i undantagen. Därför:

- Användare kanske inte vet att de är uteslutna.

- Användare kan ansluta till säkerhets gruppen för att kringgå principen.

- Exkluderade användare kan ha kvalificerat sig för undantag före, men de kan inte längre kvalificera sig för det.

När du först konfigurerar ett undantag är det ofta en Shortlist av användare som kringgår principen. Över tid kommer fler och fler användare att läggas till i uteslutningen och listan växer. Du måste gå igenom listan och kontrol lera att var och en av dessa användare fortfarande är kvalificerade för undantag. Att hantera undantags listan, från en teknisk sikt, kan vara relativt enkelt, men som fattar affärs beslut och hur ser du till att det är alla gransknings bara? Men om du konfigurerar undantaget med en Azure AD-grupp kan du använda åtkomst granskningar som en kompenserande kontroll, för att öka synligheten och minska antalet undantagna användare.

## <a name="how-to-create-an-exclusion-group-in-a-conditional-access-policy"></a>Så här skapar du en undantags grupp i en princip för villkorlig åtkomst

Följ dessa steg om du vill skapa en ny Azure AD-grupp och en princip för villkorlig åtkomst som inte gäller för den gruppen.

### <a name="create-an-exclusion-group"></a>Skapa en undantags grupp

1. Logga in på Azure-portalen.

2. Klicka på **Azure Active Directory** i det vänstra navigerings fönstret och klicka sedan på **grupper**.

3. Öppna grupp fönstret genom att klicka på **ny grupp** på den översta menyn.

4. Välj **säkerhet** i listan **typ av grupp** . Ange ett namn och en beskrivning.

5. Se till att ange **medlemskaps** typen som **tilldelad**.

6. Välj de användare som ska ingå i den här undantags gruppen och klicka sedan på **skapa**.

![Fönstret ny grupp i Azure Active Directory](./media/conditional-access-exclusion/new-group.png)

### <a name="create-a-conditional-access-policy-that-excludes-the-group"></a>Skapa en princip för villkorlig åtkomst som utesluter gruppen

Nu kan du skapa en princip för villkorlig åtkomst som använder den här undantags gruppen.

1. Klicka på **Azure Active Directory** i det vänstra navigerings fönstret och klicka sedan på **villkorlig åtkomst** för att öppna bladet **principer** .

2. Klicka på **ny princip** för att öppna det **nya** fönstret.

3. Ange ett namn.

4. Under tilldelningar klickar **du på användare och grupper**.

5. På fliken **Inkludera** väljer du **alla användare**.

6. På fliken **exkludera** lägger du till en bock till **användare och grupper** och klickar sedan på **Välj exkluderade användare**.

7. Välj den undantags grupp som du skapade.

   > [!NOTE] 
   > Som bästa praxis rekommenderar vi att du undantar minst ett administratörs konto från principen när du testar för att se till att du inte är utelåst från din klient.

8. Fortsätt med att konfigurera principen för villkorlig åtkomst baserat på organisationens krav.

![Välj rutan undantagna användare i villkorlig åtkomst](./media/conditional-access-exclusion/select-excluded-users.png)
  
Vi ska gå igenom två exempel där du kan använda åtkomst granskningar för att hantera undantag i principer för villkorlig åtkomst.

## <a name="example-1-access-review-for-users-accessing-from-blocked-countriesregions"></a>Exempel 1: åtkomst granskning för användare som kommer åt från blockerade länder/regioner

Anta att du har en princip för villkorlig åtkomst som blockerar åtkomst från vissa länder/regioner. Den innehåller en grupp som undantas från principen. Här är en rekommenderad åtkomst granskning där medlemmarna i gruppen granskas.

> [!NOTE] 
> En global administratör eller användar administratörs roll krävs för att skapa åtkomst granskningar.

1. Granskningen sker varje vecka.

2. Upphör aldrig att gälla för att se till att du håller den här undantags gruppen mest uppdaterad.

3. Alla medlemmar i den här gruppen kommer att omfattas av granskningen.

4. Varje användare måste själv bekräfta att de fortfarande behöver åtkomst från dessa blockerade länder/regioner, och de behöver därför fortfarande vara medlemmar i gruppen.

5. Om användaren inte svarar på gransknings förfrågan tas de bort automatiskt från gruppen och de kommer inte längre att ha åtkomst till klienten när de reser till dessa länder/regioner.

6. Aktivera e-postaviseringar om du vill att användarna ska kunna se hur åtkomst granskningen startar och slutförs.

    ![Skapa ett åtkomst gransknings fönster till exempel 1](./media/conditional-access-exclusion/create-access-review-1.png)

## <a name="example-2-access-review-for-users-accessing-with-legacy-authentication"></a>Exempel 2: åtkomst granskning för användare som har åtkomst till med äldre autentisering

Anta att du har en princip för villkorlig åtkomst som blockerar åtkomst för användare med äldre autentisering och äldre klient versioner och att den innehåller en grupp som är exkluderad från principen. Här är en rekommenderad åtkomst granskning där medlemmarna i gruppen granskas.

1. Den här granskningen måste vara en återkommande granskning.

2. Alla i gruppen måste granskas.

3. Det kan konfigureras för att visa en lista över affär senhets ägare som de valda granskarna.

4. Tillämpa resultaten automatiskt och ta bort användare som inte har godkänts för att fortsätta använda äldre autentiseringsmetoder.

5. Det kan vara bra att aktivera rekommendationer så att granskare av stora grupper enkelt kan fatta sina beslut.

6. Aktivera e-postaviseringar så att användarna meddelas om hur åtkomst granskningen startar och slutförs.

    ![Skapa ett åtkomst gransknings fönster till exempel 2](./media/conditional-access-exclusion/create-access-review-2.png)

>[!IMPORTANT] 
>Om du har många undantags grupper och därför behöver skapa flera åtkomst granskningar, har vi nu ett API i Microsoft Graph beta-slutpunkten där du kan skapa och hantera dem program mässigt. Information om hur du kommer igång finns i [API-referens för Azure AD Access Reviews](/graph/api/resources/accessreviews-root?view=graph-rest-beta) och [exempel på hur du hämtar åtkomst granskningar för azure AD via Microsoft Graph](https://techcommunity.microsoft.com/t5/Azure-Active-Directory/Example-of-retrieving-Azure-AD-access-reviews-via-Microsoft/td-p/236096).

## <a name="access-review-results-and-audit-logs"></a>Åtkomst gransknings resultat och gransknings loggar

Nu när du har allt på plats, grupp, princip för villkorlig åtkomst och åtkomst granskningar, är det dags att övervaka och spåra resultatet av dessa granskningar.

1. Öppna bladet **åtkomst granskningar** i Azure Portal.

2. Öppna den kontroll och det program som du har skapat för att hantera undantags gruppen.

3. Klicka på **resultat** för att se vem som har godkänts för att stanna kvar i listan och vem som har tagits bort.

    ![Åtkomst gransknings resultat Visa vem som godkänts](./media/conditional-access-exclusion/access-reviews-results.png)

4. Klicka sedan på **gransknings loggar** för att se de åtgärder som vidtogs under granskningen.

    ![Åtkomst granskningar gransknings loggar List åtgärder](./media/conditional-access-exclusion/access-reviews-audit-logs.png)

Som IT-administratör vet du att hanteringen av undantags grupper i principerna ibland är oundviklig. Men om du underhåller dessa grupper, granskar dem regelbundet av affärs ägaren eller själva användarna, och granskning av dessa ändringar kan göras enklare med åtkomst granskningar för Azure AD.

## <a name="next-steps"></a>Nästa steg

- [Skapa en åtkomst granskning av grupper eller program](create-access-review.md)
- [Vad är villkorlig åtkomst i Azure Active Directory?](../conditional-access/overview.md)