---
title: Planera distribution av en Azure Active Directory åtkomst granskningar
description: Planerings guide för lyckad åtkomst gransknings distribution
services: active-directory
documentationCenter: ''
author: BarbaraSelden
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: compliance
ms.date: 12/23/2020
ms.author: barclayn
ms.reviewer: markwahl-msft
ms.collection: M365-identity-device-management
ms.openlocfilehash: a2e670525840b801df874e3f8dcbdbadc0955a12
ms.sourcegitcommit: 6e2d37afd50ec5ee148f98f2325943bafb2f4993
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/23/2020
ms.locfileid: "97746311"
---
# <a name="planning-azure-active-directory-access-reviews-deployment"></a>Planera distribution av Azure Active Directory åtkomst granskningar

[Azure Active Directory åtkomst granskningar (Azure AD)](access-reviews-overview.md) hjälper din organisation att skydda nätverket genom att hantera dess [livs cykel för resurs åtkomst](identity-governance-overview.md). Med åtkomst granskningar kan du:

* Schemalägg regelbunden granskning eller utföra ad hoc-granskningar för att se vem som har åtkomst till särskilda resurser, till exempel program och grupper

* Spåra granskningar för insikter, efterlevnad eller princip orsaker

* Delegera granskningar till specifika administratörer, företags ägare eller slutanvändare som själv kan bekräfta behovet av fortsatt åtkomst

* Använd insikterna för att effektivt bestämma om användarna ska fortsätta att ha åtkomst

* Automatisera gransknings resultat, till exempel att ta bort användares åtkomst till resurser

  ![Diagram som visar flödet för åtkomst granskningar.](./media/deploy-access-review/1-planning-review.png)

Åtkomst granskningar är en [Azure AD Identity Governance](identity-governance-overview.md) -funktion. De andra funktionerna är [rättighets hantering](entitlement-management-overview.md), [Privileged Identity Management](../privileged-identity-management/pim-configure.md) och [användnings villkor](../conditional-access/terms-of-use.md). Tillsammans hjälper de organisationer att hantera följande fyra frågor:

* Vilka användare ska ha åtkomst till vilka resurser?

* Vilka användare gör med den här åtkomsten?

* Är det effektiv organisations kontroll för att hantera åtkomst?

* Kan granskare verifiera att kontrollerna fungerar?

Det är viktigt att planera distributionen av åtkomst granskningar för att se till att du uppnår den önskade styrnings strategin för användare i din organisation.

### <a name="key-benefits"></a>Viktiga fördelar

De främsta fördelarna med att aktivera åtkomst granskningar är:

* **Styr samarbete**. Med åtkomst granskningar kan organisationer hantera åtkomst till alla resurser som användarna behöver. När deras användare delar och samarbetar, kan organisationer vara säkra på att informationen endast är av auktoriserade användare.

* **Hantera risker**. Åtkomst granskningar ger organisationer ett sätt att granska åtkomst till data och program, vilket minskar risken för data läckage och data spill. Detta inkluderar funktioner för att regelbundet granska extern partners åtkomst till företagets resurser. 

* **Hantera efterlevnad och styrning**. Med åtkomst granskningar kan du styra och certifiera åtkomst livs cykeln till grupper, appar och platser. Du kan kontrol lera spårnings granskningar för efterlevnad eller risk känsliga program som är speciella för din organisation. 

* **Minska kostnaderna**. Åtkomst granskningar är inbyggda i molnet och fungerar internt med moln resurser, till exempel grupper, program och åtkomst paket. Att använda åtkomst granskningar är billigare än att skapa egna verktyg eller på annat sätt uppgradera dina lokala verktyg.

### <a name="training-resources"></a>Utbildnings resurser

Följande videor kan vara användbara när du lär dig mer om åtkomst granskningar:

* [Vad är åtkomst granskningar i Azure AD?](https://youtu.be/kDRjQQ22Wkk)

* [Så här skapar du åtkomst granskningar i Azure AD](https://youtu.be/6KB3TZ8Wi40)

* [Så här aktiverar du åtkomst granskningar i Azure AD](https://youtu.be/X1SL2uubx9M)

* [Granska åtkomst med min åtkomst](https://youtu.be/tIKdQhdHLXU)

### <a name="licenses"></a>Licenser

Du behöver en giltig Azure AD Premium-licens (P2) för varje person, förutom globala administratörer eller användar administratörer, som kommer att skapa eller utföra åtkomst granskningar. Mer information finns i [åtkomst granskningar licens krav](access-reviews-overview.md).

Du kan också behöva andra funktioner för identitets styrning, t. ex. [livs cykel hantering av rättigheter](entitlement-management-overview.md) eller Privileged Identity Management. I så fall kan du också behöva relaterade licenser. Mer information finns i [Azure Active Directory prissättning](https://azure.microsoft.com/pricing/details/active-directory/).

## <a name="plan-the-access-reviews-deployment-project"></a>Planera distributions projekt för åtkomst granskningar

Överväg organisationens behov av att fastställa strategin för att distribuera åtkomst granskningar i din miljö.

### <a name="engage-the-right-stakeholders"></a>Engagera rätt intressenter

När teknik projekt inte fungerar, gör de vanligt vis på grund av felaktiga förväntningar på påverkan, resultat och ansvars områden. För att undvika dessa fall GRO par bör [du se till att du är engagerande rätt intressenter](../fundamentals/active-directory-deployment-plans.md) och att projekt rollerna är tydliga.

För åtkomst granskningar kommer du troligen att inkludera representanter från följande team i din organisation:

* **IT** -administratören hanterar din IT-infrastruktur och administrerar dina moln investeringar och SaaS-appar (program vara som en tjänst). Det här teamet kommer att:

   * Granska privilegie rad åtkomst till infrastruktur och appar, inklusive Microsoft 365 och Azure AD.

   * Schemalägg och kör åtkomst granskningar för grupper som används för att underhålla undantags listor eller IT Pilot-projekt för att upprätthålla aktuella åtkomst listor.

   * Se till att program mässig (skriptad) åtkomst till resurser via tjänstens huvud namn styrs och granskas.

* **Utvecklings team** skapar och underhåller program för din organisation. Det här teamet kommer att:

   * Kontrol lera vem som kan komma åt och hantera komponenter i SaaS-, PaaS-och IaaS-resurser som utgör de utvecklade lösningarna.

   * Hantera grupper som har åtkomst till program och verktyg för intern program utveckling.

   * Kräv privilegierade identiteter som har åtkomst till produktions program vara eller lösningar som är värd för dina kunder

* **Affär senheter** hanterar projekt och egna program. Det här teamet kommer att: 

   * Granska och Godkänn eller neka åtkomst till grupper och program för interna och externa användare.

   * Schemalägg och genomför granskningar för att få fortsatt åtkomst för anställda och externa identiteter som affärs partner.

* **Företags styrningen** säkerställer att organisationen följer interna principer och följer reglerna. Det här teamet kommer att:

   * Begär eller Schemalägg nya åtkomst granskningar.

   * Utvärdera processer och procedurer för att granska åtkomst, inklusive dokumentation och registrering av efterlevnad.

   * Granska resultaten av tidigare granskningar för de mest kritiska resurserna.

> [!NOTE]
> För granskningar som kräver manuella utvärderingar, se till att planera för lämpliga granskare och granska cykler som uppfyller dina krav på principer och efterlevnad. Om gransknings cyklerna är för frekventa eller om det finns för få granskare, kan kvaliteten gå förlorad och för många eller för få personer kan ha åtkomst. 

### <a name="plan-communications"></a>Planera kommunikation

Kommunikationen är nödvändig för att alla nya affärs processer ska bli framgångs rik. Kommunicera proaktivt till användare hur och när deras upplevelse kommer att ändras och hur du får support om de drabbas av problem. 

#### <a name="communicate-changes-in-accountability"></a>Kommunicera ändringar i ansvar

Åtkomst granskningar har stöd för flyttning av ansvar för att granska och agera på fortsatt åtkomst till företags ägare. Att koppla från åtkomst beslut från IT-enheter till mer exakta åtkomst beslut. Detta är en kulturell förändring i resurs ägarens ansvar och ansvar. Kommunicera proaktivt den här ändringen och se till att resurs ägarna är utbildade och kan använda insikter för att fatta bättre beslut.

Det är tydligt att ha kontroll över alla infrastruktur-relaterade åtkomst beslut och privilegierade roll tilldelningar. 

#### <a name="customize-email-communication"></a>Anpassa e-postkommunikation

När du schemalägger en granskning utses användare som ska utföra den här granskningen. Dessa granskare får sedan ett e-postmeddelande om nya granskningar som har tilldelats dem, samt påminnelser innan en granskning som tilldelats dem upphör att gälla.

Administratörer kan välja att skicka den här aviseringen antingen halvvägs innan granskningen går ut eller en dag innan den upphör att gälla. 

E-postmeddelandet som skickas till granskare kan anpassas för att inkludera ett anpassat kort meddelande som uppmanar dem att agera på granskningen. Vi rekommenderar att du använder den ytterligare texten för att:

* Ta med ett personligt meddelande till granskare, så att de förstår att de skickas av din efterlevnad eller IT-avdelning.

* Inkludera en hyperlänk eller referens till intern information om vad gransknings förväntningarna är och ytterligare referens-eller utbildnings material.

* Innehåller en länk till instruktioner om [hur du utför en själv granskning av åtkomst.](review-your-access.md) 

  ![E-postgranskare](./media/deploy-access-review/2-plan-reviewer-email.png)

När du väljer Starta granskning dirigeras granskare till åtkomst [portalen](https://myapplications.microsoft.com/) för åtkomst granskningar för grupper och program. Portalen ger dig en översikt över alla användare som har åtkomst till den resurs som de granskar och system rekommendationer baserat på senaste inloggnings-och åtkomst information.

### <a name="plan-a-pilot"></a>Planera en pilot

Vi uppmuntrar våra kunder att inlednings vis pilot åtkomst granskningar med en liten grupp och riktade till icke-kritiska resurser. Pilotering kan hjälpa dig att justera processer och kommunikation vid behov och öka användarnas och granskars möjlighet att uppfylla kraven på säkerhet och efterlevnad.

I piloten rekommenderar vi följande:

* Börja med granskningar där resultaten inte tillämpas automatiskt och du kan kontrol lera konsekvenserna.

* Se till att alla användare har giltiga e-postadresser som visas i Azure AD och att de får e-postkommunikation för att vidta lämpliga åtgärder. 

* Dokumentera all åtkomst som tas bort som en del av piloten om du behöver återställa det snabbt.

* Övervaka gransknings loggar för att se till att allt är alla händelser som granskas korrekt.

Mer information finns i [metod tips för en pilot](../fundamentals/active-directory-deployment-plans.md).

## <a name="introduction-to-access-reviews"></a>Introduktion till åtkomst granskningar

Det här avsnittet introducerar åtkomst gransknings begrepp som du bör känna till innan du planerar granskningarna.

### <a name="what-resource-types-can-be-reviewed"></a>Vilka resurs typer kan granskas?

När du integrerar din organisations resurser med Azure AD (till exempel användare, program och grupper) kan de hanteras och granskas. 

Typiska mål för granskning är:

* [Program som är integrerade med Azure AD för enkel inloggning](../manage-apps/what-is-application-management.md) (till exempel SaaS, verksamhets nivå).

* Grupp [medlemskap](../fundamentals/active-directory-manage-groups.md?context=azure%2factive-directory%2fusers-groups-roles%2fcontext%2fugr-context) (synkroniserat till Azure AD eller skapats i Azure ad eller Microsoft 365, inklusive Microsoft Teams).

* [Åtkomst paket](./entitlement-management-overview.md) som grupperar resurser (grupper, appar och platser) till ett enda paket för att hantera åtkomst.

* [Azure AD-roller och Azure-resurs roller](../privileged-identity-management/pim-resource-roles-assign-roles.md) som definieras i Privileged Identity Management.

### <a name="who-will-create-and-manage-access-reviews"></a>Vem kommer att skapa och hantera åtkomst granskningar?

Den administrativa roll som krävs för att skapa, hantera eller läsa en åtkomst granskning beror på vilken typ av resurs som granskas. Följande tabell anger de roller som krävs för varje resurs typ:

| Resurstyp| Skapa och hantera åtkomst granskningar (skapare)| Läs åtkomst gransknings resultat |
| - | - | -|
| Grupp eller program| Global administratör <p>Användaradministratör| Skapare och säkerhets administratör |
| Privilegierade roller i Azure AD| Global administratör <p>Privilegie rad roll administratör| Creators <p>Säkerhetsläsare<p>Säkerhetsadministratör |
| Privilegierade roller i Azure (resurser)| Global administratör<p>Användaradministratör<p>Resurs ägare| Creators |
| Åtkomst paket| Global administratör<p>Skapare av Access-paket| Endast global administratör |


Mer information finns i [Administratörs roll behörigheter i Azure Active Directory](../roles/permissions-reference.md).

### <a name="who-will-review-the-access-to-the-resource"></a>Vem kommer att granska åtkomsten till resursen?

Skaparen av åtkomst granskningen bestämmer vid tidpunkten för skapandet som ska utföra granskningen. Den här inställningen kan inte ändras när granskningen har startats. Granskare representeras av tre personer:

* Resurs ägare, som är företags ägare till resursen.

* En uppsättning individuellt valda ombud som valts av administratören för åtkomst granskningar.

* Slutanvändare som varje egen fråga kommer att behöva för att få åtkomst till dem.

När du skapar en åtkomst granskning kan administratörer välja en eller flera granskare. Alla granskare kan starta och utföra en granskning, välja användare för fortsatt åtkomst till en resurs eller ta bort dem. 

### <a name="components-of-an-access-review"></a>Komponenter i en åtkomst granskning

Innan du implementerar åtkomst granskningarna bör du planera vilka typer av granskningar som är relevanta för din organisation. För att göra det måste du fatta affärs beslut om vad du vill granska och vilka åtgärder som ska vidtas utifrån granskningarna.

Om du vill skapa en princip för åtkomst granskning måste du ha följande information.

* Vad är resurs (er) att granska?

* Vars åtkomst granskas.

* Hur ofta ska granskningen ske?

* Vem ska utföra granskningen?

   * Hur kommer de att meddelas om granskning?

   * Vilka tids linjer ska framtvingas för granskning?

* Vilka automatiska åtgärder ska verkställas baserat på granskningen?

   * Vad händer om granskaren inte svarar i tid?

* Vilka manuella åtgärder kommer att utföras baserat på granskningen?

* Vilken kommunikation ska skickas baserat på vidtagna åtgärder?


**Exempel på åtkomst gransknings plan**

| Komponent| Värde |
| - | - |
| **Resurser att granska**| Åtkomst till Microsoft Dynamics |
| **Gransknings frekvens**| Varje månad |
| **Som utför granskning**| Program hanterare för Dynamics Business Group |
| **Indikering**| E-post 24 timmar innan granskning till alias Dynamics-Pms<p>Ta med att uppmuntra anpassade meddelanden till granskare för att skydda sina köp |
| **Tidslinje**| 48 timmar från meddelande |
|**Automatiska åtgärder**| Ta bort åtkomst från alla konton som inte har någon interaktiv inloggning inom 90 dagar genom att ta bort användaren från säkerhets gruppen Dynamics-Access. <p>*Utför åtgärder om de inte granskas inom tids linjen.* |
| **Manuella åtgärder**| Granskare kan utföra borttagnings godkännande innan den automatiserade åtgärden om det behövs. |
| **Kommunikation**| Skicka interna (medlemmar) användare som har tagit bort ett e-postmeddelande som förklarar att de tas bort och hur de får åtkomst. |


 

### <a name="automate-actions-based-on-access-reviews"></a>Automatisera åtgärder baserat på åtkomst granskningar

Du kan välja att ta bort åtkomsten automatiskt genom att ställa in alternativet Använd resultat automatiskt till resurs för att aktivera.

  ![Planera åtkomst granskningar](./media/deploy-access-review/3-automate-actions-settings.png)

När granskningen är klar och har avslut ATS tas användare som inte har godkänts av granskaren bort automatiskt från resursen, eller sparas med fortsatt åtkomst. Detta kan innebära att du tar bort grupp medlemskap, deras program tilldelning eller återkallar sin behörighet att öka till en privilegie rad roll.

Ta rekommendationer

Rekommendationerna visas för granskare som en del av granskaren och indikerar en persons senaste inloggning till klienten eller senaste åtkomst till ett program. Den här informationen hjälper granskare att fatta rätt åtkomst beslut. Om du väljer Följ rekommendationer följer du rekommendationerna för åtkomst granskning. I slutet av en åtkomst granskning kommer systemet att tillämpa dessa rekommendationer automatiskt för användare som granskarna inte har svarat på.

Rekommendationerna baseras på kriterierna i åtkomst granskningen. Om du till exempel konfigurerar granskningen för att ta bort åtkomst utan interaktiv inloggning i 90 dagar, rekommenderar vi att alla användare som uppfyller villkoren tas bort. Microsoft arbetar ständigt med att förbättra rekommendationerna. 

### <a name="review-guest-user-access"></a>Granska gäst användar åtkomst

Använd åtkomst granskningar för att granska och rensa samarbets partners identiteter från externa organisationer. Konfiguration av en granskning per partner kan uppfylla kraven för efterlevnad.

Externa identiteter kan beviljas åtkomst till företags resurser via någon av följande åtgärder:

* Tillagt i en grupp 

* Inbjuden till Teams 

* Tilldelad till ett företags program eller åtkomst paket

* Tilldelad en privilegie rad roll i Azure AD eller i en Azure-prenumeration

Se [exempel skript](https://github.com/microsoft/access-reviews-samples/tree/master/ExternalIdentityUse). Skriptet visar var externa identiteter som har bjudits in till klienten ska användas. Du kan se externa användares grupp medlemskap, roll tilldelningar och program tilldelningar i Azure AD. Skriptet visar inga tilldelningar utanför Azure AD, till exempel direkt rättighets tilldelning till SharePoint-resurser, utan användning av grupper.

När du skapar en åtkomst granskning för grupper eller program kan du välja att låta granskaren fokusera på alla med åtkomst eller gäst användare. Genom att välja gäst användare får granskare en fokuserad lista över externa identiteter från Azure AD B2B som har åtkomst till resursen.

 ![Granska gäst användare](./media/deploy-access-review/4-review-guest-users-admin-ui.png)

> [!IMPORTANT]
> Detta inkluderar inte externa medlemmar som har en userType medlem. Detta inkluderar inte heller användare som bjudits in utanför Azure AD B2B-samarbete, till exempel de som har åtkomst till delat innehåll direkt via SharePoint.

## <a name="plan-access-reviews-for-access-packages"></a>Planera åtkomst granskningar för åtkomst paket

[Åtkomst paket](entitlement-management-overview.md) kan avsevärt förenkla din strategi för styrning och åtkomst granskning. Ett Access-paket är ett paket med alla resurser som har åtkomst till en användare som behöver arbeta med ett projekt eller utföra sina uppgifter. Du kanske till exempel vill skapa ett Access-paket som innehåller alla program som utvecklare i din organisation behöver, eller alla program som externa användare ska ha åtkomst till. En administratör eller delegerad Access Package Manager grupperar sedan resurserna (grupper eller appar) och de roller som användarna behöver för dessa resurser.

När du [skapar ett Access-paket](entitlement-management-access-package-create.md)kan du skapa en eller flera åtkomst principer som anger villkor för vilka användare kan begära ett åtkomst paket, vad godkännande processen ser ut och hur ofta en person skulle behöva begära åtkomst igen. Åtkomst granskningar konfigureras när du skapar eller redigerar en princip för åtkomst paket.

Öppna fliken livs cykel om du vill bläddra nedåt för att få åtkomst till recensioner.

 ![Skärm bild som visar "Redigera princip" på fliken "livs cykel".](./media/deploy-access-review/5-plan-access-packages-admin-ui.png)

## <a name="plan-access-reviews-for-groups"></a>Planera åtkomst granskningar för grupper

Förutom åtkomst paket är det mest effektiva sättet att styra åtkomsten att granska grupp medlemskap. Vi rekommenderar att åtkomst till resurser tilldelas via [säkerhets grupper eller Microsoft 365 grupper](../fundamentals/active-directory-manage-groups.md)och att användare läggs till i grupperna för att få åtkomst.

En enskild grupp kan beviljas åtkomst till alla lämpliga resurser. Du kan tilldela gruppen åtkomst till enskilda resurser eller till ett Access-paket som grupperar program och andra resurser. Med den här metoden kan du granska åtkomst till gruppen i stället för en enskild persons åtkomst till varje program. 

Grupp medlemskap kan granskas av: 

* Administratörer

* Grupp ägare

* Valda användare, delegerad gransknings funktion när granskningen skapas

* Medlemmar i gruppen, attestera för sig själva

### <a name="group-ownership"></a>Grupp ägarskap

Vi rekommenderar att grupp ägare granskar medlemskap, eftersom de är mest placerade för att veta vem som behöver åtkomst. Gruppens ägarskap skiljer sig åt från grupp typen:

Grupper som skapas i Microsoft 365 och Azure AD har en eller flera väldefinierade ägare. I de flesta fall gör dessa ägare perfekta granskare för sina egna grupper eftersom de vet vem som ska ha åtkomst. 

Till exempel använder Microsoft team Microsoft 365 grupper som den underliggande auktoriserings modellen för att ge användarna åtkomst till resurser som finns i SharePoint, Exchange, OneNote eller andra Microsoft 365-tjänster. Skaparen av gruppen blir automatiskt ägare och ansvarar för att intyga medlemskapet i gruppen. 

Grupper som skapats manuellt i Azure AD-portalen eller via skriptning via Microsoft Graph kanske inte nödvändigt vis har definierade ägare. Vi rekommenderar att du definierar dem antingen via Azure AD-portalen i gruppens "ägare"-eller via Graph.

Grupper som synkroniseras från lokala Active Directory kan inte ha en ägare i Azure AD. När du skapar en åtkomst granskning för dem bör du välja personer som passar bäst för att fatta beslut om medlemskap i dem.

> [!NOTE]
> Vi rekommenderar att du definierar affärs principer som definierar hur grupper skapas för att se till att grupp ägande och ansvar är klart för regelbunden granskning av medlemskap. 

### <a name="review-membership-of-exclusion-groups-in-ca-policies"></a>Granska medlemskap i undantags grupper i CA-principer 

Det finns tillfällen när principer för villkorlig åtkomst (CA) har utformats för att skydda nätverket bör inte gälla för alla användare. Till exempel kan en CA-princip som bara tillåter användare att logga in på företags nätverket inte gälla för försäljnings teamet, som skickas i stor utsträckning. I så fall skulle sälj grupps medlemmar placeras i en grupp och gruppen skulle uteslutas från CA-principen. 

Granska ett sådant grupp medlemskap regelbundet som undantaget utgör en potentiell risk om fel medlemmar undantas från kravet.

Du kan [använda åtkomst granskningar i Azure AD för att hantera användare som uteslutits från principer för villkorlig åtkomst](conditional-access-exclusion.md).

### <a name="review-external-users-group-memberships"></a>Granska externa användares grupp medlemskap

Överväg att använda [dynamiska grupper](../enterprise-users/groups-create-rule.md) för att tilldela grupp medlemskap baserat på en användares attribut för att minimera manuellt arbete och relaterade möjliga fel. Du kanske vill skapa en eller flera dynamiska grupper för externa användare. Den interna sponsorn kan fungera som en granskare för medlemskap i gruppen. 

Obs! externa användare som tas bort från en grupp som resultat av en åtkomst granskning tas inte bort från klienten. 

De kan tas bort från en klient som antingen tas bort manuellt eller via ett skript.

### <a name="review-access-to-on-premises-groups"></a>Granska åtkomst till lokala grupper

Åtkomst granskningar kan inte ändra grupp medlemskap för grupper som du synkroniserar lokalt med [Azure AD Connect](../hybrid/whatis-azure-ad-connect.md). Detta beror på att källan till auktoriteten är lokal.

Du kan fortfarande använda åtkomst granskningar för att schemalägga och upprätthålla regelbunden granskning av lokala grupper. Granskare gör sedan en åtgärd i den lokala gruppen. Den här strategin ger åtkomst till granskningar som verktyg för alla granskningar.

Du kan använda resultatet från en åtkomst granskning på lokala grupper och bearbeta dem ytterligare, antingen genom att:

* Laddar ned CSV-rapporten från åtkomst granskningen och vidtar åtgärden manuellt.

* Använda Microsoft Graph för att programmatiskt komma åt resultat och beslut i slutförda åtkomst granskningar.

Om du till exempel vill få åtkomst till resultat för en Windows AD-hanterad grupp använder du det här [PowerShell-exempel skriptet](https://github.com/microsoft/access-reviews-samples/tree/master/AzureADAccessReviewsOnPremises). Skriptet beskriver de nödvändiga diagram anropen och exporterar Windows AD-PowerShell-kommandon för att genomföra ändringarna.

## <a name="plan-access-reviews-for-applications"></a>Planera åtkomst granskningar för program 

När du granskar åtkomst till ett program granskar du åtkomsten för anställda och externa identiteter till informationen och data i programmet. Välj att granska ett program när du behöver veta vem som har åtkomst till ett speciellt program, i stället för ett åtkomst paket eller en grupp. 

Vi rekommenderar att du planerar granskningar för program i följande scenarier:

* Användare beviljas direkt åtkomst till programmet (utanför en grupp eller ett Access-paket).

* Programmet visar kritisk eller känslig information.

* Programmet har specifika krav på efterlevnad som du måste attestera.

* Du misstänker olämplig åtkomst.

Om du vill skapa åtkomst granskningar för ett program måste du ange användar tilldelning krävs? egenskap till Ja. Om värdet är nej kan alla användare i din katalog, inklusive externa identiteter, komma åt programmet och du kan inte granska åtkomst till programmet. 

 ![planera program tilldelningar](./media/deploy-access-review/6-plan-applications-assignment-required.png)

Du måste sedan [tilldela de användare och grupper](../manage-apps/assign-user-or-group-access-portal.md) som du vill ha åtkomst till. 

### <a name="reviewers-for-an-application"></a>Granskare för ett program

Åtkomst granskningar kan göras för medlemmar i en grupp eller för användare som har tilldelats till ett program. Program i Azure AD har inte nödvändigt vis någon ägare, vilket är anledningen till att det inte går att välja program ägaren som en granskare. Du kan ytterligare omfånget för en granskning om du bara vill granska gäst användare som är tilldelade till programmet, i stället för att granska all åtkomst.

## <a name="plan-review-of-azure-ad-and-azure-resource-roles"></a>Planera granskning av Azure AD och Azures resurs roller

[Privileged Identity Management (PIM)](../privileged-identity-management/pim-configure.md) fören klar hur företag hanterar privilegie rad åtkomst till resurser i Azure AD. Detta bevarar listan över privilegierade roller, både i [Azure AD](../roles/permissions-reference.md) och [Azure-resurser](../../role-based-access-control/built-in-roles.md) mycket mindre och ökar den övergripande säkerheten för katalogen.

Med åtkomst granskningar kan granskare intyga om användarna fortfarande måste vara i en roll. Precis som åtkomst granskningar för åtkomst paket integreras granskningar för Azure AD-roller och Azure-resurser i användar upplevelsen för PIM-administratören. Vi rekommenderar att du granskar följande roll tilldelningar regelbundet:

* Global administratör

* Användaradministratör

* Administratör för privilegie rad autentisering

* Administratör för villkorsstyrd åtkomst

* Säkerhetsadministratör

* Alla roller för Microsoft 365-och Dynamics-tjänsteadministration

Roller som väljs här inkluderar permanent och berättigad roll. 

I avsnittet granskare väljer du en eller flera personer för att granska alla användare. Alternativt kan du välja att låta medlemmarna granska sin egen åtkomst.

 ![Redigera princip](./media/deploy-access-review/7-plan-azure-resources-reviewers-selection.png)

## <a name="deploy-access-reviews"></a>Distribuera åtkomst granskningar

När du har för berett en strategi och en plan för att granska åtkomsten för resurser som är integrerade med Azure AD, distribuerar och hanterar du granskningar genom att använda resurserna nedan.

### <a name="review-access-packages"></a>Granska åtkomst paket

För att minska risken för föråldrad åtkomst kan administratörer aktivera regelbunden granskning av användare som har aktiva tilldelningar till ett Access-paket. Följ anvisningarna i länken nedan:

| Instruktionsartiklar| Beskrivning |
| - | - |
| [Skapa åtkomst granskningar](entitlement-management-access-reviews-create.md)| Aktivera granskningar av Access-paket. |
| [Utför åtkomst granskningar](entitlement-management-access-reviews-review-access.md)| Utför åtkomst granskningar för andra användare som är tilldelade till ett Access-paket. |
| [Själv granska tilldelade åtkomst paket](entitlement-management-access-reviews-self-review.md)| Själv granskning av tilldelade åtkomst paket |


> [!NOTE]
> Slutanvändare som själv granskar och säger att de inte längre behöver åtkomst tas inte bort från åtkomst paketet omedelbart. De tas bort från åtkomst paketet när granskningen upphör eller om en administratör stoppar granskningen.

### <a name="review-groups-and-apps"></a>Granska grupper och appar

Åtkomst behov till grupper och program för medarbetare och gäster kan troligt vis förändras över tid. För att minska risken för inaktuella åtkomst tilldelningar kan administratörer skapa åtkomst granskningar för grupp medlemmar eller program åtkomst. Följ anvisningarna i länken nedan:

| Instruktionsartiklar| Beskrivning |
| - | - |
| [Skapa åtkomst granskningar](create-access-review.md)| Skapa en eller flera åtkomst granskningar för grupp medlemmar eller program åtkomst. |
| [Utför åtkomst granskningar](perform-access-review.md)| Utför åtkomst granskning för medlemmar i en grupp eller användare med åtkomst till ett program. |
| [Själv granskning av din åtkomst](review-your-access.md)| Medlemmar granskar egen åtkomst till en grupp eller ett program |
| [Fullständig åtkomst granskning](complete-access-review.md)| Visa en åtkomst granskning och tillämpa resultaten |
| [Vidta åtgärder för lokala grupper](https://github.com/microsoft/access-reviews-samples/tree/master/AzureADAccessReviewsOnPremises)| Exempel på PowerShell-skript för att agera på åtkomst granskningar för lokala grupper. |


### <a name="review-azure-ad-roles"></a>Granska Azure AD-roller

För att minska risken för inaktuella roll tilldelningar bör du regelbundet granska åtkomsten till behöriga Azure AD-roller.

![Skärm bild som visar listan "granska medlemskap" för Azure A D-roller.](./media/deploy-access-review/8-review-azure-ad-roles-picker.png)

Följ anvisningarna i länkarna nedan:

| Instruktionsartiklar | Beskrivning |
| - | - |
 [Skapa åtkomst granskningar](../privileged-identity-management/pim-how-to-start-security-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json)| Skapa åtkomst granskningar för privilegierade Azure AD-roller i PIM |
| [Själv granskning av din åtkomst](../privileged-identity-management/pim-how-to-perform-security-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json)| Om du har tilldelats en administrativ roll, Godkänn eller neka åtkomst till din roll |
| [Slutför en åtkomst granskning](../privileged-identity-management/pim-how-to-complete-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json)| Visa en åtkomst granskning och tillämpa resultaten |


### <a name="review-azure-resource-roles"></a>Granska Azures resurs roller

För att minska risken som är kopplad till föråldrade roll tilldelningar bör du regelbundet granska åtkomst till privilegierade Azure-resurs roller. 

![granska Azure AD-roller](./media/deploy-access-review/9-review-azure-roles-picker.png)

Följ anvisningarna i länkarna nedan:

| Instruktionsartiklar| Beskrivning |
| - | -|
| [Skapa åtkomst granskningar](../privileged-identity-management/pim-resource-roles-start-access-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json)| Skapa åtkomst granskningar för privilegierade Azure-resurs roller i PIM |
| [Själv granskning av din åtkomst](../privileged-identity-management/pim-resource-roles-perform-access-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json)| Om du har tilldelats en administrativ roll, Godkänn eller neka åtkomst till din roll |
| [Slutför en åtkomst granskning](../privileged-identity-management/pim-resource-roles-complete-access-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json)| Visa en åtkomst granskning och tillämpa resultaten |


## <a name="use-the-access-reviews-api"></a>Använd API: et för åtkomst granskning

Se [Graph API-metoder](/graph/api/resources/accessreviews-root?view=graph-rest-beta) och [kontroller för roll-och program behörighet](/graph/api/resources/accessreviews-root?view=graph-rest-beta) för att interagera med och hantera resurser som kan granskas. Åtkomst gransknings metoderna i Microsoft Graph API är tillgängliga för både program-och användar kontexter. När skript körs i program kontexten måste det konto som används för att köra API: et (tjänst principen) beviljas behörigheten "AccessReview. Read. all" för att få åtkomst till gransknings information.

Populära åtkomst gransknings aktiviteter för att automatisera med Graph API för åtkomst granskningar är:

* Skapa och starta en åtkomst granskning

* Avsluta en åtkomst granskning manuellt innan dess schemalagda slut

* Visa en lista med alla tillgängliga granskningar och deras status

* Se historiken för en gransknings serie och de beslut och åtgärder som vidtagits i varje granskning.

* Samla in beslut från en åtkomst granskning

* Samla in beslut från slutförda granskningar där granskaren tog ett annat beslut än vad systemet rekommenderar.

När du skapar nya Graph API-frågor för Automation rekommenderar vi att du använder [Graph Explorer](https://developer.microsoft.com/en-us/graph/graph-explorer). Du kan bygga och utforska dina diagram frågor innan du skickar dem till skript och kod. Detta kan hjälpa dig att snabbt iterera din fråga så att du får exakt de resultat som du letar efter, utan att ändra koden för skriptet.

## <a name="monitor-access-reviews"></a>Övervaka åtkomst granskningar

Åtkomst gransknings aktiviteter registreras och är tillgängliga från [Azure Ads gransknings loggar](../reports-monitoring/concept-audit-logs.md). Du kan filtrera gransknings data för kategorin, aktivitets typen och datum intervallet. Här är en exempel fråga:

| Kategori| Princip |
| - | - |
| Aktivitetstyp| Skapa åtkomstgranskning |
| | Uppdatera åtkomst granskning |
| | Åtkomst granskning avslutad |
| | Ta bort åtkomstgranskning |
| | Godkänn beslut |
| | Neka beslut |
| | Återställ beslut |
| | Tillämpa beslut |
| Datumintervall| sju dagar |


För mer avancerade frågor och analys av åtkomst granskningar, och för att spåra ändringar och slutföra granskningar, rekommenderar vi att du exporterar dina Azure AD audit-loggar till [azure Log Analytics](../reports-monitoring/quickstart-azure-monitor-route-logs-to-storage-account.md) eller Azure Event Hub. När du har lagrat i Azure Log Analytics kan du använda det [kraftfulla Analytics-språket](../reports-monitoring/howto-analyze-activity-logs-log-analytics.md) – och bygga dina egna instrument paneler.

## <a name="next-steps"></a>Nästa steg

Lär dig mer om de nedan relaterade teknikerna.

* [Vad är hantering av Azure AD-rättigheter?](entitlement-management-overview.md)

* [Vad är Azure AD Privileged Identity Management?](../privileged-identity-management/pim-configure.md)