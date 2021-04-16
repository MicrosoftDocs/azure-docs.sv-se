---
title: Så här använder du programåtkomst via självbetjäning i Azure AD
description: Aktivera självbetjäning så att användarna kan hitta appar i Azure AD
services: active-directory
author: iantheninja
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 07/11/2017
ms.author: iangithinji
ms.reviewer: japere,asteen
ms.openlocfilehash: 8e3851a702da46d07634a4141c774275845fa44d
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2021
ms.locfileid: "107377623"
---
# <a name="how-to-use-self-service-application-access"></a>Så här använder du programåtkomst via självbetjäning

Innan användarna kan själva identifiera program från sin Mina appar-sida  måste du aktivera programåtkomst via självbetjäning till alla program som du vill tillåta användare att själva identifiera och begära åtkomst till.

Den här funktionen är ett bra sätt för dig att spara tid och pengar som IT-grupp och rekommenderas starkt som en del av en modern programdistribution med Azure Active Directory.

Mer information om hur Mina appar kan ur ett slutanvändarperspektiv finns i [Mina appar hjälpportalen.](../user-help/my-apps-portal-end-user-access.md)

Med den här funktionen kan du:

-   Låt användarna själv identifiera program [från](https://myapps.microsoft.com/) Mina appar utan att störa IT-gruppen.
-   Lägg till dessa användare i en förkonfigurerad grupp så att du kan se vem som har begärt åtkomst, ta bort åtkomst och hantera de roller som tilldelats dem.
-   Du kan också tillåta att någon godkänner begäranden om appåtkomst så att IT-gruppen inte behöver göra det.
-   Du kan också konfigurera upp till 10 personer som kan godkänna åtkomst till det här programmet.
-   Du kan också tillåta att någon anger de lösenord som användarna kan använda för att logga in i programmet.
-   Du kan också automatiskt tilldela självbetjäning tilldelade användare till en programroll direkt.

## <a name="enable-self-service-application-access-to-allow-users-to-find-their-own-applications"></a>Aktivera programåtkomst via självbetjäning så att användarna kan hitta sina egna program

Programåtkomst via självbetjäning är ett bra sätt att tillåta användare att själva identifiera program, om du vill tillåta att affärsgruppen godkänner åtkomst till dessa program. Du kan låta affärsgruppen hantera de autentiseringsuppgifter som tilldelats till dessa användare för lösenord Single-Sign på program direkt från Mina appar sidan.

Följ stegen nedan om du vill aktivera programåtkomst via självbetjäning till ett program:
1. Öppna [**Azure Portal**](https://portal.azure.com/) och logga in som **global administratör.**
2. Öppna Azure Active Directory **genom** att **välja Alla** tjänster längst upp på den vänstra navigeringsmenyn.
3. Skriv **"Azure Active Directory"** i filtersökrutan och välj **Azure Active Directory** objektet.
4. Välj **Företagsprogram** på den vänstra navigeringsmenyn i Azure Active Directory.
5. Välj **Alla program** för att visa en lista över alla dina program.
   * Om du inte ser det program som du vill ska visas  här använder du **filterkontrollen** överst i listan med alla program och ställer in **alternativet** Visa **på Alla program.**
6. Välj det program som du vill aktivera självbetjäning för från listan.
7. När programmet har laddats **väljer du Självbetjäning** på programmets vänstra navigeringsmeny.
8. Om du vill aktivera programåtkomst via självbetjäning för det här programmet aktiverar du alternativet Tillåt användare att begära åtkomst till det här **programmet?** till **Ja.**
9. Välj sedan den grupp som användare som begär åtkomst till det här programmet ska läggas till i genom att välja väljaren bredvid etiketten Till vilken grupp ska tilldelade användare läggas **till?** och välja en grupp.
10. **Valfritt:** Om du vill kräva ett affärsgodkännande innan användarna får åtkomst ställer du in Kräv godkännande innan du beviljar åtkomst till det här **programmet?** på **Ja.**
11. **Valfritt:** För program som endast använder enkel inloggning med lösenord, om du vill tillåta att dessa företagsgodkännare anger de lösenord som skickas till det här programmet för godkända användare, ställer du in Tillåt godkännare för att ställa in användarens lösenord för det här **programmet?** på **Ja.**
12. **Valfritt:** Ange de företags godkännare som har behörighet att godkänna åtkomst till den här appen. Välj **Vem får godkänna åtkomst till det här programmet?** Välj sedan upp till 10 enskilda företags godkännare.
    * Grupper stöds inte.
13. **Valfritt:** Om du vill tilldela självbetjäning godkända användare till en roll för program som exponerar roller väljer du väljaren bredvid till vilken roll ska användare tilldelas i det här **programmet?** för att välja den roll som dessa användare ska tilldelas.
14. Välj knappen **Spara** längst upp för att slutföra.

När du har slutfört konfigurationen av självbetjäningsprogrammet kan användarna [gå till Mina appar](https://myapps.microsoft.com/) och välja knappen **+Lägg** till för att hitta de appar som du har aktiverat självbetjäning för åtkomst till. Företags godkännare ser också ett meddelande på [Mina appar](https://myapps.microsoft.com/) sidan. Du kan aktivera ett e-postmeddelande som meddelar dem när en användare har begärt åtkomst till ett program som kräver deras godkännande. 

Dessa godkännanden stöder endast arbetsflöden för enskilt godkännande, vilket innebär att om du anger flera godkännare kan en enskild godkännare godkänna åtkomst till programmet.

## <a name="things-to-check-if-self-service-isnt-working"></a>Saker att kontrollera om självbetjäning inte fungerar
-   Kontrollera att användaren eller gruppen har aktiverats för att begära åtkomst till självbetjäningsprogrammet.
-   Kontrollera att användaren besöker rätt plats för programåtkomst via självbetjäning. användarna kan navigera till [Mina appar](https://myapps.microsoft.com/) och välja **knappen +Lägg** till för att hitta de appar som du har aktiverat självbetjäning för.
-   Om programåtkomst via självbetjäning nyligen har konfigurerats kan du försöka logga in och ut igen i användarens Mina appar efter några minuter för att se om åtkomständringarna för självbetjäning har visats.

## <a name="next-steps"></a>Nästa steg
[Konfigurera Azure Active Directory för grupphantering via självbetjäning](../enterprise-users/groups-self-service-management.md)
