---
title: Konfigurera arbets flödet för administratörs medgivande – Azure Active Directory | Microsoft Docs
description: Lär dig hur du konfigurerar ett sätt för slutanvändare att begära åtkomst till program som kräver administratörs medgivande.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 10/29/2019
ms.author: kenwith
ms.reviewer: luleon
ms.collection: M365-identity-device-management
ms.openlocfilehash: 95d89ea0cbc7d1e0379a9cbfce40f11d4f8ac93f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "101643770"
---
# <a name="configure-the-admin-consent-workflow-preview"></a>Konfigurera arbets flödet för administratörs medgivande (för hands version)

Den här artikeln beskriver hur du aktiverar funktionen admin medgivande Workflow (för hands version), vilket ger slutanvändare möjlighet att begära åtkomst till program som kräver administratörs medgivande.

Utan ett administrativt medgivande arbets flöde kommer en användare i en klient där användar medgivande är inaktive rad att blockeras när de försöker få åtkomst till en app som kräver åtkomst till organisationens data. Användaren ser ett allmänt fel meddelande som säger att de inte har behörighet att komma åt appen och de bör be administratören om hjälp. Men ofta vet inte användaren vem som ska kontaktas, så de kan antingen ge upp eller skapa ett nytt lokalt konto i programmet. Även om en administratör meddelas är det inte alltid en strömlinjeformad process som hjälper administratören bevilja åtkomst och meddela sina användare.
 
Arbets flödet för administratörs medgivande ger administratörer ett säkert sätt att bevilja åtkomst till program som kräver administratörs godkännande. När en användare försöker få åtkomst till ett program men inte kan ge samtycke, kan de skicka en begäran om administratörs godkännande. Begäran skickas via e-post till administratörer som har angetts som granskare. En granskare vidtar åtgärder på begäran och användaren meddelas om åtgärden.

För att godkänna begär Anden måste en granskare vara global administratör, moln program administratör eller program administratör. Granskaren måste redan ha en av dessa administratörs roller tilldelade. att helt enkelt ange dem som granskare höjer inte sina privilegier.

## <a name="enable-the-admin-consent-workflow"></a>Aktivera arbets flödet för administratörs medgivande

Aktivera arbets flödet för administratörs medgivande och välj granskare:

1. Logga in på [Azure Portal](https://portal.azure.com) som global administratör.
2. Klicka på **alla tjänster** överst i den vänstra navigerings menyn. **Azure Active Directory-tillägget** öppnas.
3. Skriv "**Azure Active Directory**" i rutan Filter sökning och välj **Azure Active Directory** objektet.
4. I navigerings menyn klickar du på **företags program**. 
5. Under **Hantera** väljer du **användar inställningar**.
6. Under **administrativa medgivande begär Anden (för hands version)** **kan du ställa in användare så att de kan begära administrativt medgivande till appar som de inte** kan godkänna till **Ja**.

   ![Konfigurera arbets flödes inställningar för administratörs medgivande](media/configure-admin-consent-workflow/admin-consent-requests-settings.png)
 
6. Konfigurera följande inställningar:

   * **Välj användare om du vill granska förfrågningar om administratörs medgivande**. Välj granskare för det här arbets flödet från en uppsättning användare som har rollen global administratör, moln program administratör och program administratör.
   * **Valda användare får e-postmeddelanden för förfrågningar**. Aktivera eller inaktivera e-postaviseringar till granskarna när en begäran görs.  
   * **Valda användare får påminnelser om förfallo datum för begäran**. Aktivera eller inaktivera påminnelser om e-postaviseringar till granskarna när en begäran håller på att gå ut.  
   * **Medgivande förfrågan upphör att gälla efter (dagar)**. Ange hur lång tid förfrågningar ska vara giltiga.

7. Välj **Spara**. Det kan ta upp till en timme innan funktionen aktive ras.

> [!NOTE]
> Du kan lägga till eller ta bort granskare för det här arbets flödet genom att ändra listan **Välj granskare för begäran om godkännande** . Observera att en aktuell begränsning av den här funktionen är att granskarna kan behålla möjligheten att granska begär Anden som har gjorts när de betecknas som en granskare.

## <a name="how-users-request-admin-consent"></a>Hur användare begär administrativt medgivande

När arbets flödet för administratörs godkännande har Aktiver ATS kan användarna begära administratörs godkännande för ett program som de inte har behörighet att godkänna. I följande steg beskrivs användarens upplevelse när du begär godkännande. 

1. Användaren försöker logga in i programmet.

2. Meddelandet **godkännande krävs** visas. Användaren anger en motivering för att behöva åtkomst till appen och väljer sedan **begär godkännande**.

   ![Skärm bild som visar dialog rutan godkännande krävs där du kan begära godkännande.](media/configure-admin-consent-workflow/end-user-justification.png)

3. Ett meddelande om att en **begäran har skickats** bekräftar att begäran skickats till administratören. Om användaren skickar flera begär Anden skickas bara den första begäran till administratören.

   ![Skärm bild som visar bekräftelsen på begäran.](media/configure-admin-consent-workflow/end-user-sent-request.png)

 4. Användaren får ett e-postmeddelande när deras begäran godkänns, nekas eller blockeras. 

## <a name="review-and-take-action-on-admin-consent-requests"></a>Granska och vidta åtgärder för begäran om administrativt medgivande

Granska de administrativa medgivande förfrågningarna och vidta åtgärder:

1. Logga in på [Azure Portal](https://portal.azure.com) som en av de registrerade granskarna i arbets flödet för administratörs medgivande.
2. Välj **alla tjänster** överst i den vänstra navigerings menyn. **Azure Active Directory-tillägget** öppnas.
3. Skriv "**Azure Active Directory**" i rutan Filter sökning och välj **Azure Active Directory** objektet.
4. I navigerings menyn klickar du på **företags program**.
5. Under **aktivitet** väljer du **administrativa medgivande förfrågningar (för hands version)**.

   > [!NOTE]
   > Granskare ser bara admin-begäranden som skapats efter att de beskrivits som en granskare.

1. Välj det program som begärs.
2. Granska information om begäran:  

   * Om du vill se vem som begär åtkomst och varför väljer du fliken **begär ande av** .
   * Om du vill se vilka behörigheter som begärs av programmet väljer du **Granska behörigheter och medgivande**.

8. Utvärdera begäran och vidta lämplig åtgärd:

   * **Godkänn begäran**. Om du vill godkänna en begäran ger du administratörs medgivande till programmet. När en begäran har godkänts, meddelas alla begär Anden om att de har beviljats åtkomst.  
   * **Neka begäran**. Om du vill neka en begäran måste du ange en motivering som kommer att tillhandahållas för alla begär Ande. När en begäran nekas, meddelas alla begär Anden om att de har nekats åtkomst till programmet. Att neka en begäran hindrar inte användare från att begära att administratören godkänner appen igen i framtiden.  
   * **Blockera begäran**. Om du vill blockera en begäran måste du ange en motivering som kommer att tillhandahållas för alla begär Ande. När en begäran har blockerats meddelas alla begär Ande om att de har nekats åtkomst till programmet. Om en begäran blockeras skapas ett huvud objekt för tjänsten för programmet i din klient i inaktiverat tillstånd. Användarna kan inte begära administratörs medgivande till programmet i framtiden.
 
## <a name="email-notifications"></a>E-postmeddelanden
 
Om den har kon figurer ATS får alla granskare e-postmeddelanden när:

* En ny begäran har skapats
* En begäran har upphört att gälla
* En begäran närmar sig förfallo datumet  
 
Begär Anden får e-postmeddelanden när:

* De skickar en ny begäran om åtkomst
* Begäran har upphört att gälla
* Begäran har nekats eller blockerats
* Begäran har godkänts
 
## <a name="audit-logs"></a>Granskningsloggar 
 
I tabellen nedan beskrivs de scenarier och gransknings värden som är tillgängliga för arbets flödet för administratörs medgivande. 

> [!NOTE]
> Gransknings aktörens användar kontext saknas i alla scenarier. Detta är en känd begränsning i för hands versionen.


|Scenario  |Granska tjänst  |Granskningskategori  |Gransknings aktivitet  |Granska aktör  |Begränsningar i gransknings loggen  |
|---------|---------|---------|---------|---------|---------|
|Administratör som aktiverar arbets flödet för medgivande förfrågan        |Åtkomstgranskningar           |UserManagement           |Skapa mall för styrnings principer          |App-kontext            |För närvarande går det inte att hitta användar kontexten            |
|Administratör som inaktiverar arbets flödet för medgivande förfrågan       |Åtkomstgranskningar           |UserManagement           |Ta bort mall för styrnings principer          |App-kontext            |För närvarande går det inte att hitta användar kontexten           |
|Administratör som uppdaterar konfigurationen av medgivande arbets flöden        |Åtkomstgranskningar           |UserManagement           |Uppdatera mall för styrnings principer          |App-kontext            |För närvarande går det inte att hitta användar kontexten           |
|Slutanvändare som skapar en begäran om administrativt medgivande för en app       |Åtkomstgranskningar           |Policy         |Skapa begäran           |App-kontext            |För närvarande går det inte att hitta användar kontexten           |
|Granskare som godkänner en begäran om administrativt medgivande       |Åtkomstgranskningar           |UserManagement           |Godkänn alla förfrågningar i affärs flödet          |App-kontext            |För närvarande går det inte att hitta användar kontexten eller app-ID: t som har beviljats administrativt medgivande.           |
|Granskare som nekar en begäran om administrativt medgivande       |Åtkomstgranskningar           |UserManagement           |Godkänn alla förfrågningar i affärs flödet          |App-kontext            | För närvarande går det inte att hitta användar kontexten för den aktör som nekade en administrativ begäran om godkännande          |

## <a name="faq"></a>Vanliga frågor 

**Jag aktiverade det här arbets flödet, men när du testar funktionen kan du inte se meddelandet "godkännande krävs", så att jag kan begära åtkomst?**

När funktionen har Aktiver ATS kan det ta upp till 60 minuter för slutanvändarna att se uppdateringen. Du kan kontrol lera att konfigurationen har börjat fungera genom att visa **EnableAdminConsentRequests** -värdet i `https://graph.microsoft.com/beta/settings` API: et.

**Varför kan jag inte se alla väntande begär Anden som en granskare?**

Granskare kan bara se administrations begär Anden som skapats efter att de utsetts som granskare. Så om du nyligen har lagt till som en granskare ser du inte några förfrågningar som har skapats före tilldelningen.

**Varför visas flera begär Anden för samma program som granskare?**
  
Om en programutvecklare har konfigurerat sin app att använda statiskt och dynamiskt medgivande för att begära åtkomst till slutanvändarens data, visas två administrativa medgivande förfrågningar. En begäran representerar de statiska behörigheterna och den andra representerar de dynamiska behörigheterna.

**Kan jag kontrol lera statusen för min begäran som en beställare?**  

Nej, för nu kan beställare bara hämta uppdateringar via e-postaviseringar.

**Som granskare är det möjligt att godkänna programmet, men inte för alla?**
 
Om du är bekymrad med att bevilja administratörs medgivande och tillåta alla användare i klienten att använda programmet, rekommenderar vi att du nekar begäran. Bevilja sedan administrativt godkännande manuellt genom att begränsa åtkomsten till programmet genom att kräva användar tilldelning och tilldela användare eller grupper till programmet. Mer information finns i [metoder för att tilldela användare och grupper](./assign-user-or-group-access-portal.md).

## <a name="next-steps"></a>Nästa steg

Mer information om samtycker till program finns i [Azure Active Directory medgivande Framework](../develop/consent-framework.md).

[Konfigurera hur slutanvändare godkänner program](configure-user-consent.md)

[Bevilja administratörsmedgivande för hela klientorganisationen till ett program](grant-admin-consent.md)

[Behörigheter och medgivande i Microsoft Identity Platform](../develop/v2-permissions-and-consent.md)

[Azure AD på Microsoft Q&A](/answers/topics/azure-active-directory.html)