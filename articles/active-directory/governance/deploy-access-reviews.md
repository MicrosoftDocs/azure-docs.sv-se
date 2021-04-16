---
title: Planera en distribution Azure Active Directory åtkomstgranskningar
description: Planeringsguide för en lyckad distribution av åtkomstgranskningar
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
ms.openlocfilehash: 3af783d7ff8be36c63af871ab4f2d214ca9f9405
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2021
ms.locfileid: "107532582"
---
# <a name="planning-azure-active-directory-access-reviews-deployment"></a>Planera Azure Active Directory distribution av åtkomstgranskningar

[Azure Active Directory åtkomstgranskningar (Azure AD)](access-reviews-overview.md) hjälper din organisation att skydda nätverket genom att hantera dess [resursåtkomstlivscykel.](identity-governance-overview.md) Med åtkomstgranskningar kan du:

* Schemalägga regelbundna granskningar eller utföra ad hoc-granskningar för att se vem som har åtkomst till specifika resurser, till exempel program och grupper

* Spåra granskningar för insikter, efterlevnad eller principorsaker

* Delegera granskningar till specifika administratörer, företagsägare eller slutanvändare som själv kan intyga behovet av fortsatt åtkomst

* Använd insikterna för att effektivt avgöra om användarna ska fortsätta att ha åtkomst

* Automatisera granskningsresultat, till exempel att ta bort användarnas åtkomst till resurser

  ![Diagram som visar flödet för åtkomstgranskningar.](./media/deploy-access-review/1-planning-review.png)

Åtkomstgranskningar är [en Azure AD Identity Governance](identity-governance-overview.md) funktion. De andra funktionerna är [Berättigandehantering,](entitlement-management-overview.md) [Privileged Identity Management](../privileged-identity-management/pim-configure.md) [och Användningsvillkor.](../conditional-access/terms-of-use.md) Tillsammans hjälper de organisationer att hantera dessa fyra frågor:

* Vilka användare ska ha åtkomst till vilka resurser?

* Vad gör dessa användare med den åtkomsten?

* Finns det effektiv organisationskontroll för att hantera åtkomst?

* Kan granskare kontrollera att kontrollerna fungerar?

Det är viktigt att planera distributionen av åtkomstgranskningar för att säkerställa att du uppnår önskad styrningsstrategi för användare i din organisation.

### <a name="key-benefits"></a>Viktiga fördelar

De främsta fördelarna med att aktivera åtkomstgranskningar är:

* **Kontrollera samarbete**. Med åtkomstgranskningar kan organisationer hantera åtkomst till alla resurser som användarna behöver. När deras användare delar och samarbetar kan organisationer vara säkra på att informationen endast finns bland behöriga användare.

* **Hantera risk**. Åtkomstgranskningar ger organisationer ett sätt att granska åtkomsten till data och program, vilket minskar risken för dataläckage och dataläckage. Detta omfattar funktioner för att regelbundet granska externa partners åtkomst till företagsresurser. 

* **Hantera efterlevnad och styrning.** Med åtkomstgranskningar kan du styra och omertifiera åtkomstlivscykeln för grupper, appar och webbplatser. Du kan styra spåra granskningar för efterlevnad eller riskkänsliga program som är specifika för din organisation. 

* **Minska kostnaden.** Åtkomstgranskningar är inbyggda i molnet och fungerar inbyggt med molnresurser som grupper, program och åtkomstpaket. Att använda åtkomstgranskningar är mindre kostsamt än att skapa egna verktyg eller på annat sätt uppgradera din lokala verktygsuppsättning.

### <a name="training-resources"></a>Utbildningsresurser

Följande videor kan vara användbara när du lär dig mer om åtkomstgranskningar:

* [Vad är åtkomstgranskningar i Azure AD?](https://youtu.be/kDRjQQ22Wkk)

* [Så här skapar du åtkomstgranskningar i Azure AD](https://youtu.be/6KB3TZ8Wi40)

* [Så här aktiverar du åtkomstgranskningar i Azure AD](https://youtu.be/X1SL2uubx9M)

* [Granska åtkomst med hjälp av Min åtkomst](https://youtu.be/tIKdQhdHLXU)

### <a name="licenses"></a>Licenser

Du behöver en giltig Azure AD Premium (P2)-licens för varje person, förutom globala administratörer eller användaradministratörer, som ska skapa eller utföra åtkomstgranskningar. Mer information finns i [Licenskrav för åtkomstgranskningar.](access-reviews-overview.md)

Du kan också behöva andra funktioner för identitetsstyrning, till exempel [Livscykelhantering för berättigande](entitlement-management-overview.md) eller Privileged Identity Managements. I så fall kan du också behöva relaterade licenser. Mer information finns i [Azure Active Directory prissättning.](https://azure.microsoft.com/pricing/details/active-directory/)

## <a name="plan-the-access-reviews-deployment-project"></a>Planera distributionsprojekt för åtkomstgranskningar

Överväg organisationens behov av att fastställa strategin för att distribuera åtkomstgranskningar i din miljö.

### <a name="engage-the-right-stakeholders"></a>Engagera rätt intressenter

När teknikprojekt misslyckas gör de det vanligtvis på grund av felmatchade förväntningar på påverkan, resultat och ansvarsområden. För att undvika dessa fallgropar [bör du se till att du engagerar rätt intressenter och](../fundamentals/active-directory-deployment-plans.md) att projektrollerna är tydliga.

För åtkomstgranskningar kommer du troligen att inkludera representanter från följande team i din organisation:

* **IT-administrationen** hanterar din IT-infrastruktur och administrerar dina molninvesteringar och SaaS-appar (Programvara som en tjänst). Det här teamet kommer att:

   * Granska privilegierad åtkomst till infrastruktur och appar, inklusive Microsoft 365 och Azure AD.

   * Schemalägg och kör åtkomstgranskningar på grupper som används för att underhålla undantagslistor eller IT-pilotprojekt för att upprätthålla uppdaterade åtkomstlistor.

   * Se till att programmatisk (skriptad) åtkomst till resurser via tjänstens huvudnamn styrs och granskas.

* **Utvecklingsteamen** skapar och underhåller program för din organisation. Det här teamet kommer att:

   * Kontrollera vem som kan komma åt och hantera komponenter i SaaS-, PaaS- och IaaS-resurser som utgör de utvecklade lösningarna.

   * Hantera grupper som har åtkomst till program och verktyg för intern programutveckling.

   * Kräv privilegierade identiteter som har åtkomst till produktionsprogram eller lösningar som finns för dina kunder

* **Affärsenheter** hanterar projekt och egna program. Det här teamet kommer att: 

   * Granska och godkänn eller neka åtkomst till grupper och program för interna och externa användare.

   * Schemalägg och utför granskningar för att intyga fortsatt åtkomst för anställda och externa identiteter som affärspartner.

* **Företagsstyrning** säkerställer att organisationen följer den interna policyn och följer reglerna. Det här teamet kommer att:

   * Begär eller schemalägg nya åtkomstgranskningar.

   * Utvärdera processer och procedurer för att granska åtkomst, inklusive dokumentation och registerhållning för efterlevnad.

   * Granska resultatet av tidigare granskningar för de mest kritiska resurserna.

> [!NOTE]
> För granskningar som kräver manuell utvärdering bör du planera för lämpliga granskare och granska cykler som uppfyller dina princip- och efterlevnadsbehov. Om granskningscyklerna är för frekventa, eller om det finns för få granskare, kan kvaliteten gå förlorad och för många eller för få personer kan ha åtkomst. 

### <a name="plan-communications"></a>Planera kommunikation

Kommunikation är avgörande för att en ny affärsprocess ska lyckas. Kommunicera proaktivt med användarna hur och när deras upplevelse kommer att ändras och hur de kan få support om de får problem. 

#### <a name="communicate-changes-in-accountability"></a>Kommunicera ändringar i ansvar

Åtkomstgranskningar har stöd för att flytta ansvaret för att granska och agera på fortsatt åtkomst till företagsägare. Frikoppling av åtkomstbeslut från IT-enheter ger bättre åtkomstbeslut. Det här är en kulturell förändring i resursägarens ansvar och ansvar. Kommunicera den här ändringen proaktivt och se till att resursägare tränas och kan använda insikterna för att fatta bra beslut.

IT-it-användare vill ha kontroll över alla infrastrukturrelaterade åtkomstbeslut och privilegierade rolltilldelningar. 

#### <a name="customize-email-communication"></a>Anpassa e-postkommunikation

När du schemalägger en granskning nominerar du användare som ska utföra den här granskningen. Dessa granskare får sedan ett e-postmeddelande om nya granskningar som tilldelats dem, samt påminnelser innan en granskning som tilldelats dem upphör att gälla.

Administratörer kan välja att skicka det här meddelandet i halvtid innan granskningen upphör att gälla eller en dag innan den upphör att gälla. 

E-postmeddelandet som skickas till granskare kan anpassas så att det innehåller ett anpassat kort meddelande som uppmuntrar dem att agera på granskningen. Vi rekommenderar att du använder den ytterligare texten för att:

* Inkludera ett personligt meddelande till granskare så att de förstår att det skickas av din efterlevnads- eller IT-avdelning.

* Inkludera en hyperlänk eller referens till intern information om vad förväntningarna för granskningen är och ytterligare referens- eller träningsmaterial.

* Inkludera en länk till instruktioner [om hur du utför en självgranskning av åtkomst.](review-your-access.md) 

  ![E-post för granskare](./media/deploy-access-review/2-plan-reviewer-email.png)

När du väljer Starta granskning dirigeras granskarna till [myAccess-portalen för grupp-](https://myapplications.microsoft.com/) och programåtkomstgranskningar. Portalen ger dem en översikt över alla användare som har åtkomst till resursen som de granskar och systemrekommendationer baserat på senaste inloggning och åtkomstinformation.

### <a name="plan-a-pilot"></a>Planera ett pilotprojekt

Vi uppmuntrar kunderna att först pilottesta åtkomstgranskningar med en liten grupp och rikta in sig på icke-kritiska resurser. Pilottestning kan hjälpa dig att justera processer och kommunikation efter behov och öka användarnas och granskarens möjlighet att uppfylla säkerhets- och efterlevnadskrav.

I pilottestet rekommenderar vi:

* Börja med granskningar där resultaten inte tillämpas automatiskt och du kan kontrollera konsekvenserna.

* Se till att alla användare har giltiga e-postadresser listade i Azure AD och att de får e-postkommunikation för att vidta lämpliga åtgärder. 

* Dokumentera alla åtkomst som tagits bort som en del av piloten om du snabbt behöver återställa den.

* Övervaka granskningsloggar för att se till att allt är alla händelser granskas korrekt.

Mer information finns i [metodtips för ett pilottest.](../fundamentals/active-directory-deployment-plans.md)

## <a name="introduction-to-access-reviews"></a>Introduktion till åtkomstgranskningar

I det här avsnittet beskrivs de accessgranskningsbegrepp som du bör känna till innan du planerar granskningarna.

### <a name="what-resource-types-can-be-reviewed"></a>Vilka resurstyper kan granskas?

När du integrerar organisationens resurser med Azure AD (till exempel användare, program och grupper) kan de hanteras och granskas. 

Vanliga mål för granskning är:

* [Program som är integrerade med Azure AD för enkel](../manage-apps/what-is-application-management.md) inloggning (till exempel SaaS, verksamhetsrad).

* [Gruppmedlemskap](../fundamentals/active-directory-manage-groups.md?context=azure%2factive-directory%2fusers-groups-roles%2fcontext%2fugr-context) (synkroniserat till Azure AD eller skapat i Azure AD eller Microsoft 365, inklusive Microsoft Teams).

* [Åtkomstpaket](./entitlement-management-overview.md) som grupperar resurser (grupper, appar och platser) till ett enda paket för att hantera åtkomst.

* [Azure AD-roller och Azure-resursroller](../privileged-identity-management/pim-resource-roles-assign-roles.md) enligt definitionen i Privileged Identity Management.

### <a name="who-will-create-and-manage-access-reviews"></a>Vem skapar och hanterar åtkomstgranskningar?

Vilken administrativ roll som krävs för att skapa, hantera eller läsa en åtkomstgranskning beror på vilken typ av resurs som granskas. Följande tabell anger de roller som krävs för varje resurstyp:

| Resurstyp| Skapa och hantera åtkomstgranskningar (skapare)| Resultat för läsåtkomstgranskning |
| - | - | -|
| Grupp eller program| Global administratör <p>Användaradministratör| Skapare och säkerhetsadministratör |
| Privilegierade roller i Azure AD| Global administratör <p>Privilegierad rolladministratör| Creators <p>Säkerhetsläsare<p>Säkerhetsadministratör |
| Privilegierade roller i Azure (resurser)| Global administratör<p>Användaradministratör<p>Resursägare| Creators |
| Åtkomstpaket| Global administratör<p>Skapare av åtkomstpaket| Endast global administratör |


Mer information finns i Behörigheter [för administratörsroll i Azure Active Directory](../roles/permissions-reference.md).

### <a name="who-will-review-the-access-to-the-resource"></a>Vem granskar åtkomsten till resursen?

Den som skapar åtkomstgranskningen avgör vem som ska utföra granskningen när den skapas. Den här inställningen kan inte ändras när granskningen har startats. Granskare representeras av tre personer:

* Resursägare, som är företagsägare av resursen.

* En uppsättning individuellt valda ombud, som valts av administratören för åtkomstgranskningar.

* Slutanvändare som var och en själv kan intyga att de behöver fortsatt åtkomst.

När administratörer skapar en åtkomstgranskning kan de välja en eller flera granskare. Alla granskare kan starta och utföra en granskning och välja användare för fortsatt åtkomst till en resurs eller ta bort dem. 

### <a name="components-of-an-access-review"></a>Komponenter i en åtkomstgranskning

Innan du implementerar dina åtkomstgranskningar bör du planera vilka typer av granskningar som är relevanta för din organisation. För att göra det måste du fatta affärsbeslut om vad du vill granska och vilka åtgärder som ska vidtas baserat på dessa granskningar.

Om du vill skapa en princip för åtkomstgranskning måste du ha följande information.

* Vilka resurser ska granskas?

* Vars åtkomst granskas.

* Hur ofta ska granskningen ske?

* Vem ska utföra granskningen?

   * Hur kommer de att meddelas att granska?

   * Vilka tidslinjer ska tillämpas för granskning?

* Vilka automatiska åtgärder ska tillämpas baserat på granskningen?

   * Vad händer om granskaren inte svarar i tid?

* Vilka manuella åtgärder kommer att vidtas som ett resultat baserat på granskningen?

* Vilken kommunikation ska skickas baserat på vidtagna åtgärder?


**Exempel på åtkomstgranskningsplan**

| Komponent| Värde |
| - | - |
| **Resurser att granska**| Åtkomst till Microsoft Dynamics |
| **Granskningsfrekvens**| Månadsvis |
| **Who performs review (Vem utför granskning)**| Programhanterare för Dynamics-affärsgrupp |
| **Meddelande**| Skicka ett e-postmeddelande 24 timmar före granskning av Dynamics-Pms<p>Inkludera uppmuntrande anpassade meddelanden till granskare för att skydda sina inköp |
| **Tidslinje**| 48 timmar från meddelande |
|**Automatiska åtgärder**| Ta bort åtkomst från alla konton som inte har någon interaktiv inloggning inom 90 dagar genom att ta bort användaren från säkerhetsgruppen dynamics-access. <p>*Utför åtgärder om de inte granskas inom tidslinjen.* |
| **Manuella åtgärder**| Granskare kan utföra godkännande av borttagningar före automatisk åtgärd om så önskas. |
| **Kommunikation**| Skicka interna (medlemmar) användare som tas bort ett e-postmeddelande som förklarar att de har tagits bort och hur de kan återfå åtkomst. |


 

### <a name="automate-actions-based-on-access-reviews"></a>Automatisera åtgärder baserat på åtkomstgranskningar

Du kan välja att få åtkomstborttagning automatiskt genom att ställa in alternativet Tillämpa resultat automatiskt på resurs på Aktivera.

  ![Planera åtkomstgranskningar](./media/deploy-access-review/3-automate-actions-settings.png)

När granskningen är klar och har avslutats tas användare som inte godkändes av granskaren automatiskt bort från resursen , eller fortsätter att ha åtkomst. Detta kan innebära att deras gruppmedlemskap, programtilldelning eller återkallar deras behörighet att upphöja till en privilegierad roll.

Ta rekommendationer

Rekommendationerna visas för granskare som en del av granskarens upplevelse och anger en persons senaste inloggning till klientorganisationen eller senaste åtkomst till ett program. Den här informationen hjälper granskare att fatta rätt åtkomstbeslut. Om du väljer Ta rekommendationer följer du rekommendationerna från Access Review. I slutet av en åtkomstgranskning tillämpar systemet dessa rekommendationer automatiskt för användare som granskare inte har svarat på.

Rekommendationerna baseras på kriterierna i åtkomstgranskningen. Om du till exempel konfigurerar granskningen för att ta bort åtkomst utan interaktiv inloggning under 90 dagar rekommenderar vi att alla användare som uppfyller kriterierna tas bort. Microsoft arbetar kontinuerligt med att förbättra rekommendationerna. 

### <a name="review-guest-user-access"></a>Granska gästanvändaråtkomst

Använd åtkomstgranskningar för att granska och rensa samarbetspartners identiteter från externa organisationer. Konfigurationen av en granskning per partner kan uppfylla efterlevnadskraven.

Externa identiteter kan beviljas åtkomst till företagsresurser via någon av följande åtgärder:

* Har lagts till i en grupp 

* Inbjuden till Teams 

* Tilldelas till ett företagsprogram eller åtkomstpaket

* Tilldelat en privilegierad roll i Azure AD eller i en Azure-prenumeration

Se [exempelskriptet](https://github.com/microsoft/access-reviews-samples/tree/master/ExternalIdentityUse). Skriptet visar var externa identiteter som bjudits in till klientorganisationen används. Du kan se externa användares gruppmedlemskap, rolltilldelningar och programtilldelningar i Azure AD. Skriptet visar inga tilldelningar utanför Azure AD, till exempel direkt rättighetstilldelning till Sharepoint-resurser, utan användning av grupper.

När du skapar en åtkomstgranskning för grupper eller program kan du välja att låta granskaren fokusera på Alla med åtkomst eller Endast gästanvändare. Genom att välja Endast gästanvändare får granskarna en fokuserad lista över externa identiteter från Azure AD B2B som har åtkomst till resursen.

 ![Granska gästanvändare](./media/deploy-access-review/4-review-guest-users-admin-ui.png)

> [!IMPORTANT]
> Detta inkluderar INTE externa medlemmar som har en userType av medlem. Detta omfattar inte heller användare som bjudits in utanför Azure AD B2B-samarbete, till exempel de som har åtkomst till delat innehåll direkt via SharePoint.

## <a name="plan-access-reviews-for-access-packages"></a>Planera åtkomstgranskningar för åtkomstpaket

[Åtkomstpaket](entitlement-management-overview.md) kan avsevärt förenkla din strategi för styrning och åtkomstgranskning. Ett åtkomstpaket är ett paket med alla resurser med den åtkomst som en användare behöver för att arbeta med ett projekt eller utföra sin uppgift. Du kanske till exempel vill skapa ett åtkomstpaket som innehåller alla program som utvecklare i din organisation behöver, eller alla program som externa användare ska ha åtkomst till. En administratör eller delegerad åtkomstpakethanterare grupperar sedan resurserna (grupper eller appar) och de roller som användarna behöver för dessa resurser.

När [du](entitlement-management-access-package-create.md)skapar ett åtkomstpaket kan du skapa en eller flera åtkomstprinciper som anger villkor för vilka användare kan begära ett åtkomstpaket, hur godkännandeprocessen ser ut och hur ofta en person måste begära åtkomst på nytt. Åtkomstgranskningar konfigureras när du skapar eller redigerar en princip för åtkomstpaket.

Öppna fliken Livscykel för att rulla ned till Åtkomstgranskningar.

 ![Skärmbild som visar "Redigera princip" på fliken "Livscykel".](./media/deploy-access-review/5-plan-access-packages-admin-ui.png)

## <a name="plan-access-reviews-for-groups"></a>Planera åtkomstgranskningar för grupper

Förutom åtkomstpaket är granskning av gruppmedlemskap det effektivaste sättet att styra åtkomst. Vi rekommenderar att åtkomst till resurser tilldelas via säkerhetsgrupper [eller Microsoft 365 och](../fundamentals/active-directory-manage-groups.md)att användare läggs till i dessa grupper för att få åtkomst.

En enda grupp kan beviljas åtkomst till alla lämpliga resurser. Du kan tilldela gruppåtkomst till enskilda resurser eller till ett åtkomstpaket som grupperar program och andra resurser. Med den här metoden kan du granska åtkomsten till gruppen i stället för en individs åtkomst till varje program. 

Gruppmedlemskap kan granskas av: 

* Administratörer

* Gruppägare

* Valda användare, delegerad granskningsfunktion när granskningen skapas

* Medlemmar i gruppen, attestering för sig själva

### <a name="group-ownership"></a>Gruppägarskap

Vi rekommenderar att gruppägare granskar medlemskapet, eftersom de är bäst placerade för att veta vem som behöver åtkomst. Ägarskapet för grupper skiljer sig åt beroende på grupptyp:

Grupper som skapas i Microsoft 365 och Azure AD har en eller flera väldefinierade ägare. I de flesta fall gör dessa ägare perfekta granskare för sina egna grupper eftersom de vet vem som ska ha åtkomst. 

Microsoft Teams använder till exempel Microsoft 365-grupper som underliggande auktoriseringsmodell för att ge användare åtkomst till resurser som finns i SharePoint, Exchange, OneNote eller andra Microsoft 365 tjänster. Teamets skapare blir automatiskt ägare och bör ansvara för attestera gruppens medlemskap. 

Grupper som skapats manuellt i Azure AD-portalen eller via skript via Microsoft Graph kanske inte nödvändigtvis har definierade ägare. Vi rekommenderar att du definierar dem antingen via Azure AD-portalen i gruppens avsnitt "Ägare" eller via Graph.

Grupper som synkroniseras från lokal Active Directory kan inte ha en ägare i Azure AD. När du skapar en åtkomstgranskning för dem bör du välja vilka personer som passar bäst för att välja medlemskap i dem.

> [!NOTE]
> Vi rekommenderar att du definierar affärsprinciper som definierar hur grupper skapas för att säkerställa ett tydligt gruppägarskap och ansvar för regelbunden granskning av medlemskap. 

### <a name="review-membership-of-exclusion-groups-in-conditional-access-policies"></a>Granska medlemskap i undantagsgrupper i principer för villkorsstyrd åtkomst 

Det finns tillfällen när principer för villkorsstyrd åtkomst som har utformats för att skydda nätverket inte ska gälla för alla användare. En princip för villkorlig åtkomst som endast tillåter användare att logga in i företagsnätverket kanske till exempel inte gäller för säljteamet, som reser i stor utsträckning. I så fall skulle säljteamets medlemmar läggas till i en grupp och den gruppen skulle undantas från principen för villkorlig åtkomst. 

Granska ett sådant gruppmedlemskap regelbundet eftersom undantaget representerar en potentiell risk om fel medlemmar undantas från kravet.

Du kan använda [Azure AD-åtkomstgranskningar för att hantera användare som undantagits från principer för villkorlig åtkomst.](conditional-access-exclusion.md)

### <a name="review-external-users-group-memberships"></a>Granska externa användares gruppmedlemskap

Överväg att använda dynamiska grupper för [](../enterprise-users/groups-create-rule.md) att tilldela gruppmedlemskap baserat på en användares attribut för att minimera manuellt arbete och associerade potentiella fel. Du kanske vill skapa en eller flera dynamiska grupper för externa användare. Den interna sponsringen kan fungera som granskare för medlemskap i gruppen. 

Obs! Externa användare som tas bort från en grupp som ett resultat av en åtkomstgranskning tas inte bort från klientorganisationen. 

De kan tas bort från en klientorganisation antingen manuellt eller via ett skript.

### <a name="review-access-to-on-premises-groups"></a>Granska åtkomsten till lokala grupper

Åtkomstgranskningar kan inte ändra gruppmedlemskapet för grupper som du synkroniserar lokalt med [Azure AD Connect](../hybrid/whatis-azure-ad-connect.md). Det beror på att auktoritetskällan finns lokalt.

Du kan fortfarande använda åtkomstgranskningar för att schemalägga och underhålla regelbundna granskningar av lokala grupper. Granskare kommer sedan att vidta åtgärder i den lokala gruppen. Den här strategin behåller åtkomstgranskningar som verktyg för alla granskningar.

Du kan använda resultaten från en åtkomstgranskning av lokala grupper och bearbeta dem ytterligare, antingen genom att:

* Ladda ned CSV-rapporten från Åtkomstgranskning och vidta åtgärder manuellt.

* Använda Microsoft Graph för att programmatiskt komma åt resultat och beslut i slutförda åtkomstgranskningar.

Om du till exempel vill komma åt resultat för en Windows AD-hanterad grupp använder du det här [PowerShell-exempelskriptet](https://github.com/microsoft/access-reviews-samples/tree/master/AzureADAccessReviewsOnPremises). Skriptet beskriver de Graph-anrop som krävs och exporterar Windows AD-PowerShell kommandon för att utföra ändringarna.

## <a name="plan-access-reviews-for-applications"></a>Planera åtkomstgranskningar för program 

När du granskar åtkomsten till ett program granskar du åtkomsten för anställda och externa identiteter till information och data i programmet. Välj att granska ett program när du behöver veta vem som har åtkomst till ett visst program, i stället för ett åtkomstpaket eller en grupp. 

Vi rekommenderar att du planerar granskningar för program i följande scenarier:

* Användare beviljas direkt åtkomst till programmet (utanför en grupp eller ett åtkomstpaket).

* Programmet exponerar kritisk eller känslig information.

* Programmet har specifika efterlevnadskrav som du måste intyga.

* Du misstänker olämplig åtkomst.

Om du vill skapa åtkomstgranskningar för ett program anger du Användartilldelning krävs? egenskapen till Ja. Om det är inställt på Nej kan alla användare i din katalog, inklusive externa identiteter, komma åt programmet och du kan inte granska åtkomsten till programmet. 

 ![planera apptilldelningar](./media/deploy-access-review/6-plan-applications-assignment-required.png)

Sedan måste du [tilldela de användare och grupper](../manage-apps/assign-user-or-group-access-portal.md) som du vill ha åtkomst till. 

### <a name="reviewers-for-an-application"></a>Granskare för ett program

Åtkomstgranskningar kan vara för medlemmar i en grupp eller för användare som har tilldelats till ett program. Program i Azure AD har inte nödvändigtvis en ägare, vilket är anledningen till att det inte går att välja programägaren som granskare. Du kan ytterligare begränsa en granskning för att endast granska gästanvändare som är tilldelade till programmet i stället för att granska all åtkomst.

## <a name="plan-review-of-azure-ad-and-azure-resource-roles"></a>Planera granskning av Azure AD- och Azure-resursroller

[Privileged Identity Management (PIM)](../privileged-identity-management/pim-configure.md) förenklar hur företag hanterar privilegierad åtkomst till resurser i Azure AD. Detta gör att listan över privilegierade roller, både i [Azure AD- och](../roles/permissions-reference.md) [Azure-resurser,](../../role-based-access-control/built-in-roles.md) är mycket mindre och ökar den övergripande säkerheten för katalogen.

Åtkomstgranskningar gör det möjligt för granskare att bedöma om användarna fortfarande behöver ha en roll. Precis som åtkomstgranskningar för åtkomstpaket är granskningar för Azure AD-roller och Azure-resurser integrerade i PIM-administratörsanvändarupplevelsen. Vi rekommenderar att du regelbundet granskar följande rolltilldelningar:

* Global administratör

* Användaradministratör

* Privilegierad autentiseringsadministratör

* Administratör för villkorsstyrd åtkomst

* Säkerhetsadministratör

* Alla Microsoft 365- och Dynamics-tjänstadministrationsroller

Roller som väljs här omfattar permanent och berättigad roll. 

I avsnittet Granskare väljer du en eller flera personer för att granska alla användare. Eller så kan du välja att medlemmarna ska granska sin egen åtkomst.

 ![Redigera princip](./media/deploy-access-review/7-plan-azure-resources-reviewers-selection.png)

## <a name="deploy-access-reviews"></a>Distribuera åtkomstgranskningar

När du har förberett en strategi och en plan för att granska åtkomsten för resurser som är integrerade med Azure AD kan du distribuera och hantera granskningar med hjälp av resurserna nedan.

### <a name="review-access-packages"></a>Granska åtkomstpaket

För att minska risken för inaktuell åtkomst kan administratörer aktivera periodiska granskningar av användare som har aktiva tilldelningar till ett åtkomstpaket. Följ anvisningarna i länken nedan:

| Instruktionsartiklar| Description |
| - | - |
| [Skapa åtkomstgranskningar](entitlement-management-access-reviews-create.md)| Aktivera granskningar av Åtkomstpaket. |
| [Utföra åtkomstgranskningar](entitlement-management-access-reviews-review-access.md)| Utför åtkomstgranskningar för andra användare som har tilldelats till ett åtkomstpaket. |
| [Självgranskning av tilldelade åtkomstpaket](entitlement-management-access-reviews-self-review.md)| Självgranskning av tilldelade åtkomstpaket |


> [!NOTE]
> Slutanvändare som själv granskar och säger att de inte längre behöver åtkomst tas inte bort från åtkomstpaketet omedelbart. De tas bort från åtkomstpaketet när granskningen upphör eller om en administratör stoppar granskningen.

### <a name="review-groups-and-apps"></a>Granska grupper och appar

Åtkomsten måste till grupper och program för anställda och gäster ändras troligen med tiden. Administratörer kan skapa åtkomstgranskningar för gruppmedlemmar eller programåtkomst för att minska risken för inaktuella åtkomsttilldelningar. Följ anvisningarna i länken nedan:

| Instruktionsartiklar| Description |
| - | - |
| [Skapa åtkomstgranskningar](create-access-review.md)| Skapa en eller flera åtkomstgranskningar för gruppmedlemmar eller programåtkomst. |
| [Utföra åtkomstgranskningar](perform-access-review.md)| Utför åtkomstgranskning för medlemmar i en grupp eller användare med åtkomst till ett program. |
| [Själv granska din åtkomst](review-your-access.md)| Medlemmar granskar egen åtkomst till en grupp eller ett program |
| [Fullständig åtkomstgranskning](complete-access-review.md)| Visa en åtkomstgranskning och tillämpa resultatet |
| [Vidta åtgärder för lokala grupper](https://github.com/microsoft/access-reviews-samples/tree/master/AzureADAccessReviewsOnPremises)| Exempel på PowerShell-skript för att agera på åtkomstgranskningar för lokala grupper. |


### <a name="review-azure-ad-roles"></a>Granska Azure AD-roller

För att minska risken för inaktuella rolltilldelningar bör du regelbundet granska åtkomsten till privilegierade Azure AD-roller.

![Skärmbild som visar listan "Granska medlemskap" för Azure A D-roller.](./media/deploy-access-review/8-review-azure-ad-roles-picker.png)

Följ anvisningarna i länkarna nedan:

| Instruktionsartiklar | Description |
| - | - |
 [Skapa åtkomstgranskningar](../privileged-identity-management/pim-how-to-start-security-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json)| Skapa åtkomstgranskningar för privilegierade Azure AD-roller i PIM |
| [Själv granska din åtkomst](../privileged-identity-management/pim-how-to-perform-security-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json)| Om du har tilldelats en administrativ roll ska du godkänna eller neka åtkomst till din roll |
| [Slutföra en åtkomstgranskning](../privileged-identity-management/pim-how-to-complete-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json)| Visa en åtkomstgranskning och tillämpa resultatet |


### <a name="review-azure-resource-roles"></a>Granska Azure-resursroller

För att minska risken för inaktuella rolltilldelningar bör du regelbundet granska åtkomsten till privilegierade Azure-resursroller. 

![granska azure ad-roller](./media/deploy-access-review/9-review-azure-roles-picker.png)

Följ anvisningarna i länkarna nedan:

| Instruktionsartiklar| Description |
| - | -|
| [Skapa åtkomstgranskningar](../privileged-identity-management/pim-resource-roles-start-access-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json)| Skapa åtkomstgranskningar för privilegierade Azure-resursroller i PIM |
| [Själv granska din åtkomst](../privileged-identity-management/pim-resource-roles-perform-access-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json)| Om du har tilldelats en administrativ roll ska du godkänna eller neka åtkomst till din roll |
| [Slutföra en åtkomstgranskning](../privileged-identity-management/pim-resource-roles-complete-access-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json)| Visa en åtkomstgranskning och tillämpa resultatet |


## <a name="use-the-access-reviews-api"></a>Använda API:et för åtkomstgranskning

Mer information finns i Graph API methods and [role and application permission authorization checks](/graph/api/resources/accessreviewsv2-root?view=graph-rest-beta&preserve-view=true) to interact with and manage reviewable resources (Kontroll av [roll-](/graph/api/resources/accessreviewsv2-root?view=graph-rest-beta&preserve-view=true) och programbehörighet för att interagera med och hantera granskningsbara resurser). Metoderna för åtkomstgranskningar i Microsoft Graph-API:et är tillgängliga för både program- och användarkontext. När skript körs i programkontexten måste det konto som används för att köra API:et (tjänstprincipen) beviljas behörigheten "AccessReview.Read.All" för att kunna köra frågor mot åtkomstgranskningsinformation.

Populära uppgifter för åtkomstgranskningar för att automatisera användningen Graph API för åtkomstgranskningar är:

* Skapa och starta en åtkomstgranskning

* Avsluta en åtkomstgranskning manuellt innan dess schemalagda slut

* Lista alla åtkomstgranskningar som körs och deras status

* Se historiken för en granskningsserie och de beslut och åtgärder som vidtas i varje granskning.

* Samla in beslut från en åtkomstgranskning

* Samla in beslut från slutförda granskningar där granskaren tog ett annat beslut än vad systemet rekommenderade.

När du skapar Graph API frågor för automatisering rekommenderar vi att du använder [Graph Explorer.](https://developer.microsoft.com/en-us/graph/graph-explorer) Du kan skapa och utforska dina Graph-frågor innan du lägger till dem i skript och kod. Detta kan hjälpa dig att snabbt iterera frågan så att du får exakt de resultat du letar efter, utan att ändra koden i skriptet.

## <a name="monitor-access-reviews"></a>Övervaka åtkomstgranskningar

Aktiviteter för åtkomstgranskningar registreras och är tillgängliga från [Azure AD:s granskningsloggar.](../reports-monitoring/concept-audit-logs.md) Du kan filtrera granskningsdata efter kategori, aktivitetstyp och datumintervall. Här är en exempelfråga:

| Kategori| Policy |
| - | - |
| Aktivitetstyp| Skapa åtkomstgranskning |
| | Uppdatera åtkomstgranskning |
| | Åtkomstgranskningen har avslutats |
| | Ta bort åtkomstgranskning |
| | Godkänn beslut |
| | Neka beslut |
| | Beslut om återställning |
| | Tillämpa beslut |
| Datumintervall| sju dagar |


För mer avancerade frågor och analys av åtkomstgranskningar och för att spåra ändringar och slutförande av granskningar rekommenderar vi att du exporterar dina Azure AD-granskningsloggar till [Azure Log Analytics](../reports-monitoring/quickstart-azure-monitor-route-logs-to-storage-account.md) eller Azure Event Hub. När det lagras i Azure Log Analytics kan du använda det [kraftfulla analysspråket](../reports-monitoring/howto-analyze-activity-logs-log-analytics.md) och skapa egna instrumentpaneler.

## <a name="next-steps"></a>Nästa steg

Lär dig mer om nedanstående tekniker.

* [Vad är Azure AD-berättigandehantering?](entitlement-management-overview.md)

* [Vad är Azure AD Privileged Identity Management?](../privileged-identity-management/pim-configure.md)