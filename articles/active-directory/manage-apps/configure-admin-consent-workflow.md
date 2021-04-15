---
title: Konfigurera arbetsflödet för administratörsmedgivande – Azure Active Directory | Microsoft Docs
description: Lär dig hur du konfigurerar ett sätt för slutanvändare att begära åtkomst till program som kräver administratörsmedgivande.
services: active-directory
author: iantheninja
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 10/29/2019
ms.author: iangithinji
ms.reviewer: luleon
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9811c3d1833a02ad3cbaf22b9f0b31fd2da5bb6d
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2021
ms.locfileid: "107375193"
---
# <a name="configure-the-admin-consent-workflow"></a>Konfigurera arbetsflödet för administratörsmedgivande

Den här artikeln beskriver hur du aktiverar arbetsflödesfunktionen för administratörsmedgivande, vilket ger slutanvändarna ett sätt att begära åtkomst till program som kräver administratörsmedgivande.

Utan ett arbetsflöde för administratörsmedgivande blockeras en användare i en klientorganisation där användarmedgivande är inaktiverat när de försöker komma åt appar som kräver behörighet att komma åt organisationsdata. Användaren ser ett allmänt felmeddelande som säger att de inte har behörighet att komma åt appen och de bör be administratören om hjälp. Men ofta vet inte användaren vem som ska kontaktas, så de ger antingen upp eller skapar ett nytt lokalt konto i programmet. Även när en administratör meddelas finns det inte alltid en effektiv process som hjälper administratören att bevilja åtkomst och meddela sina användare.
 
Arbetsflödet för administratörsmedgivande ger administratörer ett säkert sätt att bevilja åtkomst till program som kräver administratörsgodkännande. När en användare försöker komma åt ett program men inte kan ge sitt medgivande kan de skicka en begäran om administratörsgodkännande. Begäran skickas via e-post till administratörer som har angetts som granskare. En granskare vidtar åtgärder på begäran och användaren meddelas om åtgärden.

För att godkänna begäranden måste granskaren vara global administratör, molnprogramadministratör eller programadministratör. Granskaren måste redan ha en av dessa administratörsroller tilldelade. att bara utse dem som granskare höjer inte sina privilegier.

## <a name="enable-the-admin-consent-workflow"></a>Aktivera arbetsflödet för administratörsmedgivande

Så här aktiverar du arbetsflödet för administratörsmedgivande och väljer granskare:

1. Logga in på [Azure Portal](https://portal.azure.com) som global administratör.
2. Klicka **på Alla** tjänster längst upp i den vänstra navigeringsmenyn. Tillägget **Azure Active Directory öppnas.**
3. I filtersökrutan skriver du "**Azure Active Directory**" och väljer **Azure Active Directory** objekt.
4. Klicka på Företagsprogram på **navigeringsmenyn.** 
5. Under **Hantera** väljer du **Användarinställningar.**
6. Under **Begäranden om administratörsmedgivande** **anger du Användare kan begära administratörsmedgivande för appar som de inte kan godkänna till** **Ja.**

   ![Konfigurera arbetsflödesinställningar för administratörsmedgivande](media/configure-admin-consent-workflow/admin-consent-requests-settings.png)
 
6. Konfigurera följande inställningar:

   * **Välj användare för att granska begäranden om administratörsmedgivande.** Välj granskare för det här arbetsflödet från en uppsättning användare som har rollen global administratör, molnprogramadministratör och programadministratör.
   * **Valda användare får e-postaviseringar för begäranden.** Aktivera eller inaktivera e-postaviseringar till granskarna när en begäran görs.  
   * **Valda användare får påminnelser om förfallotid för begäran.** Aktivera eller inaktivera e-postaviseringar om påminnelser till granskarna när en begäran snart upphör att gälla.  
   * **Begäran om medgivande upphör att gälla efter (dagar)**. Ange hur länge begäranden ska vara giltiga.

7. Välj **Spara**. Det kan ta upp till en timme innan funktionen aktiveras.

> [!NOTE]
> Du kan lägga till eller ta bort granskare för det här arbetsflödet genom att ändra listan Välj granskare **av begäranden om administratörsmedgivande.** Observera att en aktuell begränsning i den här funktionen är att granskare kan behålla möjligheten att granska begäranden som gjorts medan de har angetts som granskare.

## <a name="how-users-request-admin-consent"></a>Hur användare begär administratörsmedgivande

När arbetsflödet för administratörsmedgivande har aktiverats kan användarna begära administratörsgodkännande för ett program som de inte har tillåtelse att godkänna. Följande steg beskriver användarupplevelsen när du begär godkännande. 

1. Användaren försöker logga in i programmet.

2. Meddelandet **Godkännande krävs** visas. Användaren skriver en motivering för att behöva åtkomst till appen och väljer sedan **Begär godkännande.**

   ![Skärmbild som visar dialogrutan Godkännande krävs där du kan begära godkännande.](media/configure-admin-consent-workflow/end-user-justification.png)

3. Ett **meddelande om** att begäran skickades bekräftar att begäran skickades till administratören. Om användaren skickar flera begäranden skickas bara den första begäran till administratören.

   ![Skärmbild som visar bekräftelsen av att begäran skickades.](media/configure-admin-consent-workflow/end-user-sent-request.png)

 4. Användaren får ett e-postmeddelande när begäran godkänns, nekas eller blockeras. 

## <a name="review-and-take-action-on-admin-consent-requests"></a>Granska och vidta åtgärder vid begäranden om administratörsmedgivande

Granska begäranden om administratörsmedgivande och vidta åtgärder:

1. Logga in på Azure Portal [som](https://portal.azure.com) en av de registrerade granskarna av arbetsflödet för administratörsmedgivande.
2. Välj **Alla tjänster** överst i den vänstra navigeringsmenyn. Tillägget **Azure Active Directory öppnas.**
3. I filtersökrutan skriver du "**Azure Active Directory**" och väljer **Azure Active Directory** objektet.
4. Klicka på Företagsprogram på **navigeringsmenyn.**
5. Under **Aktivitet** väljer du **Begäranden om administratörsmedgivande.**

   > [!NOTE]
   > Granskare ser bara administrativa begäranden som har skapats efter att de har angetts som granskare.

1. Välj det program som begärs.
2. Granska informationen om begäran:  

   * Om du vill se vem som begär åtkomst och varför väljer du **fliken Begärd av.**
   * Om du vill se vilka behörigheter som begärs av programmet väljer du **Granska behörigheter och godkänn .**

8. Utvärdera begäran och vidta lämpliga åtgärder:

   * **Godkänn begäran**. Om du vill godkänna en begäran beviljar du administratörsmedgivande för programmet. När en begäran har godkänts meddelas alla beställare om att de har beviljats åtkomst.  
   * **Neka begäran**. Om du vill neka en begäran måste du ange en motivering som ska tillhandahållas till alla beställare. När en begäran nekas meddelas alla beställare om att de har nekats åtkomst till programmet. Om du nekar en begäran hindras inte användare från att begära administratörsmedgivande till appen igen i framtiden.  
   * **Blockera begäran**. Om du vill blockera en begäran måste du ange en motivering som ska tillhandahållas till alla beställare. När en begäran har blockerats meddelas alla beställare om att de har nekats åtkomst till programmet. Om du blockerar en begäran skapas ett objekt för tjänstens huvudnamn för programmet i din klientorganisation i ett inaktiverat tillstånd. Användarna kommer inte att kunna begära administratörsmedgivande för programmet i framtiden.
 
## <a name="email-notifications"></a>E-postmeddelanden
 
Om den är konfigurerad får alla granskare e-postmeddelanden när:

* En ny begäran har skapats
* En begäran har upphört att gälla
* En begäran närmar sig förfallodatumet  
 
Beställare får e-postaviseringar när:

* De skickar en ny begäran om åtkomst
* Begäran har upphört att gälla
* Begäran har nekats eller blockerats
* Deras begäran har godkänts
 
## <a name="audit-logs"></a>Granskningsloggar 
 
I tabellen nedan beskrivs de scenarier och granskningsvärden som är tillgängliga för arbetsflödet för administratörsmedgivande.

|Scenario  |Granskningstjänst  |Granskningskategori  |Granskningsaktivitet  |Granska aktör  |Begränsningar i granskningsloggen  |
|---------|---------|---------|---------|---------|---------|
|Administratör som aktiverar arbetsflödet för begäran om medgivande        |Åtkomstgranskningar           |UserManagement           |Skapa en principmall för styrning          |Appkontext            |För närvarande kan du inte hitta användarkontexten            |
|Administratören inaktiverar arbetsflödet för begäran om medgivande       |Åtkomstgranskningar           |UserManagement           |Ta bort principmall för styrning          |Appkontext            |För närvarande kan du inte hitta användarkontexten           |
|Administratören uppdaterar arbetsflödeskonfigurationerna för medgivande        |Åtkomstgranskningar           |UserManagement           |Uppdatera principmall för styrning          |Appkontext            |För närvarande kan du inte hitta användarkontexten           |
|Slutanvändare som skapar en begäran om administratörsmedgivande för en app       |Åtkomstgranskningar           |Policy         |Skapa begäran           |Appkontext            |För närvarande kan du inte hitta användarkontexten           |
|Granskare som godkänner en begäran om administratörsmedgivande       |Åtkomstgranskningar           |UserManagement           |Godkänna alla begäranden i affärsflödet          |Appkontext            |För närvarande kan du inte hitta den användarkontext eller det app-ID som har beviljats administratörsmedgivande.           |
|Granskare nekar en begäran om administratörsmedgivande       |Åtkomstgranskningar           |UserManagement           |Godkänna alla begäranden i affärsflödet          |Appkontext            | För närvarande kan du inte hitta användarkontexten för aktören som nekade en begäran om administratörsmedgivande          |

## <a name="faq"></a>Vanliga frågor 

**Jag har aktiverat det här arbetsflödet, men varför kan jag inte se den nya uppmaningen "Godkännande krävs" när jag testar funktionerna, så att jag kan begära åtkomst?**

När du har aktivera funktionen kan det ta upp till 60 minuter för slutanvändarna att se uppdateringen. Du kan kontrollera att konfigurationen har verkställts korrekt genom att visa värdet **EnableAdminConsentRequests** i `https://graph.microsoft.com/beta/settings` API:et.

**Varför kan jag inte se alla väntande begäranden som granskare?**

Granskare kan bara se administrativa begäranden som har skapats efter att de har angetts som granskare. Så om du nyligen har lagts till som granskare ser du inga begäranden som har skapats före tilldelningen.

**Varför ser jag flera begäranden för samma program som granskare?**
  
Om en programutvecklare har konfigurerat sin app för att använda statiskt och dynamiskt medgivande för att begära åtkomst till slutanvändarens data visas två begäranden om administratörsmedgivande. En begäran representerar de statiska behörigheterna och den andra representerar de dynamiska behörigheterna.

**Kan jag som begärare kontrollera status för min begäran?**  

Nej, för tillfället kan beställare bara få uppdateringar via e-postaviseringar.

**Går det att godkänna programmet som granskare, men inte för alla?**
 
Om du är orolig över att bevilja administratörsmedgivande och tillåta alla användare i klientorganisationen att använda programmet rekommenderar vi att du nekar begäran. Bevilja sedan administratörsmedgivande manuellt genom att begränsa åtkomsten till programmet genom att kräva användartilldelning och tilldela användare eller grupper till programmet. Mer information finns i [Metoder för att tilldela användare och grupper.](./assign-user-or-group-access-portal.md)

## <a name="next-steps"></a>Nästa steg

Mer information om medgivande till program finns i Azure Active Directory [ramverket för medgivande.](../develop/consent-framework.md)

[Konfigurera hur slutanvändare godkänner program](configure-user-consent.md)

[Bevilja administratörsmedgivande för hela klientorganisationen till ett program](grant-admin-consent.md)

[Behörigheter och medgivande i Microsofts identitetsplattform](../develop/v2-permissions-and-consent.md)

[Azure AD på Microsoft Q&A](/answers/topics/azure-active-directory.html)
