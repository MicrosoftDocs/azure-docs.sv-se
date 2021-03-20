---
title: Etablera appar med omfångs filter | Microsoft Docs
description: Lär dig hur du använder omfångs filter för att förhindra att objekt i appar som har stöd för automatisk användar etablering är etablerade om ett objekt inte uppfyller dina affärs krav.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.topic: how-to
ms.date: 06/08/2020
ms.author: kenwith
ms.openlocfilehash: 7fff456b7ad6e980fc3c9bda36cfcab02e2ed863
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "99255839"
---
# <a name="attribute-based-application-provisioning-with-scoping-filters"></a>Attributbaserade program etablering med omfångs filter
Syftet med den här artikeln är att förklara hur du använder omfångs filter för att definiera attributbaserade regler som avgör vilka användare som ska tillhandahållas till ett program.

## <a name="scoping-filter-use-cases"></a>Användnings fall för omfattnings filter

Ett scope-filter gör att Azure Active Directory (Azure AD) etablerings tjänsten kan ta med eller undanta användare som har ett attribut som matchar ett angivet värde. När du till exempel konfigurerar användare från Azure AD till ett SaaS-program som används av ett försäljnings team kan du ange att endast användare med ett "avdelning"-attribut för "försäljning" ska omfattas av etableringen.

Omfångs filter kan användas på olika sätt beroende på typ av etablerings koppling:

* **Utgående etablering från Azure AD till SaaS-program**. När Azure AD är käll systemet är [användar-och grupp tilldelningar](../manage-apps/assign-user-or-group-access-portal.md) den vanligaste metoden för att avgöra vilka användare som omfattas av etableringen. Dessa tilldelningar används också för att aktivera enkel inloggning och tillhandahålla en enda metod för att hantera åtkomst och etablering. Omfångs filter kan användas valfritt, förutom tilldelningar eller i stället för dem, för att filtrera användare baserat på attributvärden.

    >[!TIP]
    > Du kan inaktivera etablering baserat på tilldelningar för ett företags program genom att ändra inställningarna i menyn [omfång](../app-provisioning/user-provisioning.md#how-do-i-set-up-automatic-provisioning-to-an-application) under etablerings inställningarna för att **synkronisera alla användare och grupper**. 

* **Inkommande etablering från HCM-program till Azure AD och Active Directory**. När ett [HCM-program som Workday](../saas-apps/workday-tutorial.md) är käll systemet är definitions områdes filter den primära metoden för att avgöra vilka användare som ska tillhandahållas från HCM-programmet till Active Directory eller Azure AD.

Som standard har inte Azure AD Provisioning-kopplingar några attribut-baserade omfångs filter konfigurerade. 

## <a name="scoping-filter-construction"></a>Uppförande av omfattnings filter

Ett omfångs filter består av en eller flera *satser*. -Satser avgör vilka användare som tillåts passera genom omfångs filtret genom att utvärdera varje användares attribut. Du kan till exempel ha en-sats som kräver att användarens "State"-attribut är lika med "New York", så att endast nya användare i Göteborg är etablerade i programmet. 

En enskild sats definierar ett enskilt villkor för ett enda attributvärde. Om flera satser skapas i ett enda omfångs filter utvärderas de tillsammans med hjälp av "och"-logik. Det innebär att alla satser måste utvärderas till "true" för att en användare ska kunna tillhandahållas.

Slutligen kan flera omfångs filter skapas för ett enda program. Om det finns flera omfångs filter utvärderas de tillsammans med hjälp av "OR"-logik. Det innebär att om alla satser i något av de konfigurerade omfångs filtren utvärderas till "true", är användaren etablerade.

Varje användare eller grupp som bearbetas av Azure AD Provisioning-tjänsten utvärderas alltid individuellt mot varje omfångs filter.

Anta till exempel följande scope-filter:

![Omfångs filter](./media/define-conditional-rules-for-provisioning-user-accounts/scoping-filter.PNG) 

Enligt det här omfångs filtret måste användarna uppfylla följande kriterier som ska tillhandahållas:

* De måste vara i New York.
* De måste arbeta på teknik avdelningen.
* Företagets anställnings-ID måste vara mellan 1 000 000 och 2 000 000.
* Deras befattning får inte vara null eller tom.

## <a name="create-scoping-filters"></a>Skapa omfångs filter
Definitions områdes filter konfigureras som en del av mappningar av mappar för varje Azure AD-anslutning för användar etablering. Följande procedur förutsätter att du redan har konfigurerat automatisk etablering för [ett av de program som stöds](../saas-apps/tutorial-list.md) och lägger till ett omfångs filter till det.

### <a name="create-a-scoping-filter"></a>Skapa ett omfångs filter
1. I [Azure Portal](https://portal.azure.com)går du till avsnittet **Azure Active Directory**  >  **program för företags program**  >   .

2. Välj det program som du har konfigurerat automatisk etablering för: till exempel "ServiceNow".

3. Välj fliken **Etablering**.

4. I avsnittet **mappningar** väljer du den mappning som du vill konfigurera ett omfångs filter för: till exempel "synkronisera Azure Active Directory användare till ServiceNow".

5. Välj menyn **käll objekt omfånget** .

6. Välj **Lägg till omfångs filter**.

7. Definiera en sats genom att välja ett källattribut **namn**, en **operator** och ett **Attributvärde** som ska matchas mot. Följande operatorer stöds:

   a. **Lika med**. Sats returnerar true om det utvärderade attributet matchar värdet för Indatasträngen exakt (Skift läges känsligt).

   b. **inte lika med**. Sats returnerar true om det utvärderade attributet inte matchar värdet för Indatasträngen (Skift läges känsligt).

   c. **är sant**. Sats returnerar true om det utvärderade attributet innehåller det booleska värdet true.

   d. **är falskt**. Sats returnerar true om det utvärderade attributet innehåller ett booleskt värde falskt.

   e. **är null**. Sats returnerar true om det utvärderade attributet är tomt.

   f. **är inte null**. Sats returnerar true om det utvärderade attributet inte är tomt.

   ex. **regex-matchning**. Sats returnerar true om det utvärderade attributet matchar ett mönster för reguljära uttryck. Exempel: ([1-9] [0-9]) matchar alla siffror mellan 10 och 99.

   h. **ingen regex-matchning**. Sats returnerar true om det utvärderade attributet inte matchar ett mönster för reguljära uttryck.
   
   i. **Greater_Than.** Sats returnerar true om det utvärderade attributet är större än värdet. Värdet som anges i omfångs filtret måste vara ett heltal och attributet för användaren måste vara ett heltal [0, 1, 2,...]. 
   
   j. **Greater_Than_OR_EQUALS.** Sats returnerar true om det utvärderade attributet är större än eller lika med värdet. Värdet som anges i omfångs filtret måste vara ett heltal och attributet för användaren måste vara ett heltal [0, 1, 2,...]. 
   
   k. **Åtgärd.** Sats returnerar true om det utvärderade attributet innehåller strängens värde (Skift läges känsligt) enligt beskrivningen [här](/dotnet/api/system.string.contains). 


>[!IMPORTANT] 
> - IsMemberOf-filtret stöds inte för närvarande.
> - LIKA med och inte lika med stöds inte för attribut med flera värden

9. Du kan också upprepa steg 7-8 om du vill lägga till fler omfångs satser.

10. I **omfångs filter rubrik**, Lägg till ett namn för ditt omfångs filter.

11. Välj **OK**.

12. Välj **OK** igen på skärmen **omfångs filter** . Du kan också upprepa steg 6-11 om du vill lägga till ett annat omfångs filter.

13. Välj **Spara** på skärmen **Mappning av attribut** . 

>[!IMPORTANT] 
> Om du sparar ett nytt omfångs filter utlöses en ny fullständig synkronisering för programmet, där alla användare i käll systemet utvärderas igen mot det nya omfångs filtret. Om en användare i programmet tidigare fanns inom omfånget för etablering, men faller utanför omfattningen, så inaktive ras eller avetableras kontot i programmet. Om du vill åsidosätta det här standard beteendet går du till [hoppa över borttagning för användar konton som omfattas av omfånget](../app-provisioning/skip-out-of-scope-deletions.md).


## <a name="common-scoping-filters"></a>Vanliga omfångs filter
| Target-attribut| Operator | Värde | Beskrivning|
|----|----|----|----|
|userPrincipalName|REGEX-MATCHNING|.\*@domain.com |Alla användare med userPrincipal som har domänen är @domain.com inom omfånget för etablering|
|userPrincipalName|INGEN REGEX-MATCHNING|.\*@domain.com|Alla användare med userPrincipal som har domänen @domain.com kommer utanför omfånget för etablering|
|avdelning|ÄR lika med|försäljning|Alla användare från försäljnings avdelningen är inom omfånget för etablering|
|workerID|REGEX-MATCHNING|(1 [0-9] [0-9] [0-9] [0-9] [0-9] [0-9])| Alla anställda med workerIDs mellan 1000000 och 2000000 finns inom omfånget för etablering.|

## <a name="related-articles"></a>Relaterade artiklar
* [Automatisera användar etablering och avetablering för SaaS-program](../app-provisioning/user-provisioning.md)
* [Anpassa mappningar av attribut för användar etablering](../app-provisioning/customize-application-attributes.md)
* [Skriva uttryck för attributmappningar](functions-for-customizing-application-data.md)
* [Meddelanden om konto etablering](../app-provisioning/user-provisioning.md)
* [Använd SCIM för att aktivera automatisk etablering av användare och grupper från Azure Active Directory till program](../app-provisioning/use-scim-to-provision-users-and-groups.md)
* [Lista över självstudier om hur du integrerar SaaS-appar](../saas-apps/tutorial-list.md)
