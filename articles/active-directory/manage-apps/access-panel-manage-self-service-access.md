---
title: Använda självbetjäning för program åtkomst i Azure AD
description: Aktivera självbetjäning så att användarna kan hitta appar i Azure AD
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 07/11/2017
ms.author: kenwith
ms.reviewer: japere,asteen
ms.openlocfilehash: 13f91fdd9e2d9501fba426bd6facbf9824a39285
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "99257025"
---
# <a name="how-to-use-self-service-application-access"></a>Använda självbetjäning för program åtkomst

Innan användarna kan identifiera program från sina Mina appar, måste du aktivera självbetjänings **program åtkomst** till alla program som du vill att användarna ska kunna identifiera och begära åtkomst till.

Den här funktionen är ett bra sätt att spara tid och pengar som en IT-grupp och är starkt rekommenderat som en del av en modern program distribution med Azure Active Directory.

Mer information om hur du använder Mina appar från en slut användar perspektiv finns i [hjälpen för min Apps-portalen](../user-help/my-apps-portal-end-user-access.md).

Med den här funktionen kan du:

-   Låt användare själv identifiera program från [Mina appar](https://myapps.microsoft.com/) utan att bry sig om IT-gruppen.
-   Lägg till dessa användare i en förkonfigurerad grupp så att du kan se vem som har begärt åtkomst, ta bort åtkomst och hantera de roller som har tilldelats dem.
-   Du kan också låta någon godkänna förfrågningar om app-åtkomst så att IT-gruppen inte behöver.
-   Du kan också konfigurera upp till 10 personer som kan godkänna åtkomst till det här programmet.
-   Du kan också låta någon ställa in lösen ord som användarna kan använda för att logga in i programmet.
-   Du kan också automatiskt tilldela självbetjänings användare till en program roll direkt.

## <a name="enable-self-service-application-access-to-allow-users-to-find-their-own-applications"></a>Aktivera självbetjäning för program åtkomst så att användarna kan hitta sina egna program

Åtkomst till självbetjänings program är ett bra sätt att ge användare möjlighet att identifiera program, vilket gör det möjligt för företags gruppen att godkänna åtkomst till dessa program. Du kan tillåta att affärs gruppen hanterar de autentiseringsuppgifter som tilldelats dessa användare för lösen ord Single-Sign på program direkt från sidan Mina appar.

Följ stegen nedan om du vill aktivera självbetjänings program åtkomst till ett program:
1. Öppna [**Azure Portal**](https://portal.azure.com/) och logga in som **Global administratör.**
2. Öppna **tillägget Azure Active Directory** genom att välja **alla tjänster** överst i huvud menyn till vänster.
3. Skriv **"Azure Active Directory**" i rutan filtrera sökning och välj **Azure Active Directory** objektet.
4. Välj **Företagsprogram** på den vänstra navigeringsmenyn i Azure Active Directory.
5. Välj **Alla program** för att visa en lista över alla dina program.
   * Om du inte ser det program som du vill visa här använder du **filter** kontrollen längst upp i **listan Alla program** och anger alternativet **Visa** för **alla program.**
6. Välj det program som du vill aktivera självbetjänings åtkomst till i listan.
7. När programmet har lästs in väljer du **självbetjäning** från programmets vänstra navigerings meny.
8. Aktivera självbetjäning för program åtkomst för det här programmet genom att aktivera alternativet **Tillåt användare att begära åtkomst till det här programmet?** växla till **Ja.**
9. För att välja den grupp som användare som begär åtkomst till det här programmet ska läggas till väljer du väljaren bredvid etiketten som **gruppen ska tilldelas användare till?** och väljer sedan en grupp.
10. **Valfritt:** Om du vill kräva ett affärs godkännande innan användarna tillåts måste du ställa in **Kräv godkännande innan jag beviljar åtkomst till det här programmet?** växla till **Ja**.
11. **Valfritt: endast för program som använder enkel inloggning med lösen ord,** om du vill tillåta att affärs god kännare anger lösen ord som skickas till det här programmet för godkända användare ställer du in alternativet **Tillåt god kännare att ange användarens lösen ord för det här programmet?** växla till **Ja**.
12. **Valfritt:** Ange de affärs god kännare som får godkänna åtkomst till den här appen. Välj **vem får godkänna åtkomst till det här programmet?** Välj sedan upp till 10 enskilda affärs god kännare.
    * Grupper stöds inte.
13. **Valfritt:** **för program som visar roller**, om du vill tilldela självbetjänings godkända användare till en roll, väljer du väljaren bredvid **rollen till vilken roll ska användare tilldelas i det här programmet?** Välj den roll som användarna ska tilldelas till.
14. Välj knappen **Spara** längst upp för att avsluta.

När du har slutfört självbetjänings program konfigurationen kan användarna navigera till [Mina appar](https://myapps.microsoft.com/) och välja knappen **+ Lägg** till för att hitta appar som du har aktiverat självbetjänings åtkomst till. Affärs god kännare ser också ett meddelande på sidan [Mina appar](https://myapps.microsoft.com/) . Du kan aktivera ett e-postmeddelande som meddelar dem när en användare har begärt åtkomst till ett program som kräver godkännande. 

Dessa godkännanden stöder endast enskilda godkännande arbets flöden, vilket innebär att om du anger flera god kännare kan en enskild god kännare godkänna åtkomst till programmet.

## <a name="things-to-check-if-self-service-isnt-working"></a>Saker att kontrol lera om självbetjäningen inte fungerar
-   Se till att användaren eller gruppen har Aktiver ATS för att begära åtkomst till självbetjänings program.
-   Se till att användaren besöker rätt plats för självbetjänings program åtkomst. användarna kan gå till sidan [Mina appar](https://myapps.microsoft.com/) och välja knappen **+ Lägg** till för att hitta appar som du har aktiverat självbetjänings åtkomst till.
-   Om självbetjänings program åtkomsten nyligen har kon figurer ATS kan du försöka logga in och ut igen i användarens Mina appar efter några minuter för att se om självbetjänings åtkomsten har ändrats.

## <a name="next-steps"></a>Nästa steg
[Konfigurera Azure Active Directory för grupphantering via självbetjäning](../enterprise-users/groups-self-service-management.md)
