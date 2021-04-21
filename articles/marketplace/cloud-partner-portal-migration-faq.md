---
title: Vanliga frågor och svar om övergången från Cloud Partner Portal till Partnercenter – Microsofts kommersiella marknadsplats
description: Svar på vanliga frågor om övergång av erbjudanden från Cloud Partner Portal till Partnercenter.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: mingshen-ms
ms.author: mingshen
ms.date: 07/14/2020
ms.openlocfilehash: 0a45e7c0479cf490ad8688230897ae89ce4d1020
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2021
ms.locfileid: "107819132"
---
# <a name="frequently-asked-questions-about-transitioning-from-the-cloud-partner-portal-to-partner-center"></a>Vanliga frågor och svar om övergången från Cloud Partner Portal till Partnercenter

Den Cloud Partner Portal har gått över till Partnercenter. PartnerCenter erbjuder en smidig, integrerad upplevelse för publicering av nya [erbjudanden på Microsoft AppSource](https://appsource.microsoft.com/) eller [Azure Marketplace](https://azuremarketplace.microsoft.com/) och för att hantera befintliga erbjudanden som har migrerats från Cloud Partner Portal. Alla funktioner i Cloud Partner Portal är tillgängliga i Partnercenter. Den här artikeln tar upp vanliga frågor om detta.

## <a name="what-does-the-transition-to-partner-center-mean-for-me"></a>Vad innebär övergången till Partnercenter för mig?

Du kan fortsätta att göra affärer i Partnercenter:

| Område | Ändringar |
| --- | --- |
| Konto | Du behöver inte skapa ett nytt Partnercenter-konto. du kan använda dina Cloud Partner Portal autentiseringsuppgifter för att logga in på Partnercenter där du nu hanterar ditt konto, dina användare, dina behörigheter och din fakturering. Publiceringsavtalet och informationen om företagsprofilen migreras till ditt nya Partnercenter-konto, tillsammans med eventuell utbetalningsprofilinformation, användarkonton och behörigheter samt aktiva erbjudanden. Läs mer i [Hantera ditt commercial marketplace-konto i Partnercenter.](manage-account.md) |
| Erbjudande för publicering och erbjudandehantering | Vi har flyttat dina erbjudandedata från Cloud Partner Portal till Partnercenter. Nu kommer du åt dina erbjudanden i Partnercenter, som ger en förbättrad användarupplevelse och ett intuitivt gränssnitt. Lär dig hur [du uppdaterar ett befintligt erbjudande på den kommersiella marknadsplatsen.](partner-center-portal/update-existing-offer.md) |
| Tillgänglighet för dina erbjudanden på den kommersiella marknadsplatsen | Inga ändringar. Om ditt erbjudande är live på den kommersiella marknadsplatsen fortsätter det att vara live. |
| Nya inköp och distributioner | Inga ändringar. Dina kunder kan fortsätta att köpa och distribuera dina erbjudanden utan avbrott. |
| Utbetalningar | Alla köp och distributioner fortsätter att betalas ut till dig som vanligt. Läs mer om [att få betalt på den kommersiella marknadsplatsen.](/partner-center/marketplace-get-paid?context=/azure/marketplace/context/context) |
| API-integreringar med befintliga [Cloud Partner Portal API:er](cloud-partner-portal-api-overview.md) | Befintliga Cloud Partner Portal-API:er stöds fortfarande och dina befintliga integreringar fungerar fortfarande. Läs mer i [Kommer Cloud Partner Portal REST-API:er att stödjas?](#are-the-cloud-partner-portal-rest-apis-still-supported) |
| Analys | Du kan fortsätta att övervaka försäljning, utvärdera prestanda och optimera dina erbjudanden på den kommersiella marknadsplatsen genom att visa analyser i Partnercenter. Det finns skillnader mellan hur analysrapporter visas i CPP och Partnercenter. Till exempel har **Säljarinsikter** I CPP fliken Beställningar **&-användning** som visar data för användningsbaserade erbjudanden och icke-användningsbaserade erbjudanden, medan sidan Beställningar i Partnercenter har en separat flik för SaaS-erbjudanden.  Läs mer i [Access analytic reports for the commercial marketplace in Partner Center (Få åtkomst till analysrapporter för den kommersiella marknadsplatsen i Partnercenter).](partner-center-portal/analytics.md) |
|||

## <a name="do-i-need-to-create-a-new-account-to-manage-my-offers-in-partner-center"></a>Måste jag skapa ett nytt konto för att hantera mina erbjudanden i Partnercenter?

Nej, ditt konto bevaras. Det innebär att om du är en befintlig partner kan du använda dina befintliga Cloud Partner Portal för att logga in på Partnercenter. Skapa inte ett nytt konto eller några erbjudanden som skapats i Cloud Partner Portal och som flyttas till Partnercenter associeras inte med det.

## <a name="what-pages-in-partner-center-correspond-to-pages-i-used-in-the-cloud-partner-portal"></a>Vilka sidor i Partnercenter motsvarar sidor som jag använde i Cloud Partner Portal?

Följande är Partnercenter-länkar för sidor som ofta används i Cloud Partner Portal. Om du har sparat Cloud Partner Portal länkar som bokmärken bör du uppdatera dem.

| Cloud Partner Portal sidan | Cloud Partner Portal sidlänk | Partnercenter, sidlänk |
| --- | --- | --- |
| Sidan med alla erbjudanden | [https://cloudpartner.azure.com/#alloffers](https://cloudpartner.azure.com/#alloffers) | [https://partner.microsoft.com/dashboard/commercial-marketplace/overview](https://partner.microsoft.com/dashboard/commercial-marketplace/overview) |
| Sidan med alla utgivare | [https://cloudpartner.azure.com/#publishers](https://cloudpartner.azure.com/#publishers) | [https://partner.microsoft.com/dashboard/account/v3/publishers/list](https://partner.microsoft.com/dashboard/account/v3/publishers/list) |
| Utgivarprofil | [https://cloudpartner.azure.com/#profile](https://cloudpartner.azure.com/#profile) | [https://partner.microsoft.com/dashboard/account/management](https://partner.microsoft.com/dashboard/account/management) |
| Användarsidan | [https://cloudpartner.azure.com/#users](https://cloudpartner.azure.com/#users) | [https://partner.microsoft.com/dashboard/account/usermanagement](https://partner.microsoft.com/dashboard/account/usermanagement) |
| Historiksidan | [https://cloudpartner.azure.com/#history](https://cloudpartner.azure.com/#history) | Historikfunktionen stöds inte ännu i Partnercenter. |
| Instrumentpanel för insikter | [https://cloudpartner.azure.com/#insights](https://cloudpartner.azure.com/#insights) | [https://partner.microsoft.com/dashboard/commercial-marketplace/analytics/summary](https://partner.microsoft.com/dashboard/commercial-marketplace/analytics/summary) |
| Utbetalningsrapport | [https://cloudpartner.azure.com/#insights/payout](https://cloudpartner.azure.com/#insights/payout) | [https://partner.microsoft.com/dashboard/payouts/reports/incentivepayments](https://partner.microsoft.com/dashboard/payouts/reports/incentivepayments) |
|||

## <a name="payout-report-differences"></a>Skillnader i utbetalningsrapport

Det här är skillnaderna i utbetalningsrapporten mellan den Cloud Partner Portal och det aktuella Partnercenter:

| Partnerportalen i molnet | Partnercenter |
| --- | --- |
| **Länk**: https://cloudpartner.azure.com/ | **Länk**: https://partner.microsoft.com/dashboard/payouts/reports/transactionhistory och https://partner.microsoft.com/dashboard/payouts/reports/incentivepayments |
| **Navigering:** Utbetalningsrapportering som tillhandahålls i Insights-utbetalning | **Navigering:** Utbetalningsrapportering i Partnercenter – utbetalningsikon |
| **Omfång:**<ul><li>Transaktionen per radobjekt visas för insamling pågår, samlas in och betalas.</li><li>Rapportering – visar alla radobjekt när inköpsordern har skapats, inklusive pågående insamling och fakturering samt samlingsstatus och radobjekt som ännu inte är berättigade att betalas.</li></ul> | **Omfång:**<ul><li>Visar radobjekten efter att de anses vara berättigade intäkter.</li><li>Kunderna betalar till Microsoft först och sedan kan ISV:er se utbetalningsrapporten börja.</li><li>Utbetalningsrapporten visar inte pågående insamling och fakturering pågår.</li></ul> |
| **Transaktionen är inte redo för utbetalning:** Fakturering pågår | **Transaktionen är inte redo för utbetalning:** Nästa beräknade betalning: Utbetalningsstatusen är i obearbetat tillstånd. |
| **Utbetalningsstatus:** n/a | **Utbetalningsstatus:**<ul><li>Obearbetad: Intjäningen är berättigad till betalning.</li><li>Kommande: Intjäningen skickas till utgivaren vid nästa månadsutbetalning.</li><li>Skickat: Betalningen har skickats till din bank.</li></ul> |
|||

## <a name="what-about-offers-i-published-in-the-cloud-partner-portal"></a>Vad gäller för erbjudanden som jag har publicerat i Cloud Partner Portal?

Erbjudandena har flyttats till Partnercenter och kommer att vara tillgängliga för dig när du har loggat in på Partnercenter, med undantag för erbjudandena Dynamics Nav Managed Service och Cortana Intelligence. Om ditt erbjudande fanns live på den kommersiella marknadsplatsen fortsätter det att vara live och dina kunder fortsätter att kunna köpa och distribuera det utan avbrott. Mer information finns i **nästa fråga, Vilka erbjudanden har flyttats till Partnercenter?**.

## <a name="what-offers-were-moved-to-partner-center"></a>Vilka erbjudanden har flyttats till Partnercenter?

Alla typer av erbjudanden som tidigare stöds i Cloud Partner Portal stöds i Partnercenter, med undantag för erbjudandena Dynamics Nav Managed Service och Cortana Intelligence.

För de erbjudandetyper som stöds i Partnercenter har alla erbjudanden flyttats oavsett deras status. draft-, de-listed- och preview-only-erbjudanden flyttas också.

| Erbjudandetyp | Har du flyttat till Partnercenter? | Nästa steg |
| --- | --- | --- |
| SaaS | Yes | Logga in på Partnercenter för att skapa nya erbjudanden och hantera erbjudanden som har skapats i Cloud Partner Portal. Läs mer i [Planera ett SaaS-erbjudande för den kommersiella marknadsplatsen.](plan-saas-offer.md) |
| Virtuell dator | Yes | Logga in på Partnercenter för att skapa nya erbjudanden och hantera erbjudanden som har skapats i Cloud Partner Portal. Läs mer i [Plan a virtual machine offer (Planera ett erbjudande för en virtuell dator).](marketplace-virtual-machines.md) |
| Azure-program | Yes | Logga in på Partnercenter för att skapa nya erbjudanden och hantera erbjudanden som har skapats i Cloud Partner Portal. Läs mer i [Skapa ett erbjudande för Azure-program.](create-new-azure-apps-offer.md) |
| Dynamics 365 Business Central | Ja | Logga in på Partnercenter för att skapa nya erbjudanden och hantera erbjudanden som har skapats i Cloud Partner Portal. Läs mer i [Skapa ett erbjudande för Dynamics 365 Business Central.](partner-center-portal/create-new-business-central-offer.md) |
| Dynamics 365 for Customer Engagement & PowerApps | Yes | Logga in på Partnercenter för att skapa nya erbjudanden och hantera erbjudanden som har skapats i Cloud Partner Portal. Läs mer i [Create a Dynamics 365 for Customer Engagement (Skapa ett Dynamics 365 for Customer Engagement-& PowerApps-erbjudande).](dynamics-365-customer-engage-offer-setup.md) |
| Dynamics 365 for Operations | Yes | Logga in på Partnercenter för att skapa nya erbjudanden och hantera erbjudanden som har skapats i Cloud Partner Portal. Läs mer i Create a Dynamics 365 for Operations offer (Skapa ett erbjudande för [Dynamics 365 for Operations).](partner-center-portal/create-new-operations-offer.md) |
| Power BI-app | Yes | Logga in på Partnercenter för att skapa nya erbjudanden och hantera erbjudanden som har skapats i Cloud Partner Portal. Läs mer i Create a Power BI app for AppSource (Skapa [en Power BI app för AppSource).](partner-center-portal/create-power-bi-app-offer.md) |
| IoT Edge modul | Yes | Logga in på Partnercenter för att skapa nya erbjudanden och hantera erbjudanden som har skapats i Cloud Partner Portal. Läs mer i [Skapa, konfigurera och publicera ett IoT Edge-modulerbjudande i Azure Marketplace](partner-center-portal/azure-iot-edge-module-creation.md). |
| Container | Yes | Logga in på Partnercenter för att skapa nya erbjudanden och hantera erbjudanden som har skapats i Cloud Partner Portal. Läs mer i [Create an Azure container offer (Skapa ett Azure-containererbjudande).](./create-azure-container-offer.md) |
| Konsulttjänst | Yes | Logga in på Partnercenter för att skapa nya erbjudanden och hantera erbjudanden som har skapats i Cloud Partner Portal. Läs mer i [Skapa ett konsulttjänstererbjudande.](./create-consulting-service-offer.md) |
| Hanterad tjänst | Yes | Logga in på Partnercenter för att skapa nya erbjudanden och hantera erbjudanden som har skapats i Cloud Partner Portal. Läs mer i [Create a Managed Service offer (Skapa ett erbjudande för hanterad tjänst).](./plan-managed-service-offer.md) |
| Hanterad Dynamics Nav-tjänst | No | Microsoft har utvecklat Dynamics NAV Managed Service till [Dynamics 365 Business Central,](/dynamics365/business-central/)så vi avmarkerar live-erbjudanden för Dynamics NAV Managed Service från AppSource. Dessa erbjudanden kan inte längre upptäckas av kunder och har inte flyttats till Partnercenter. Om du vill göra dina erbjudanden tillgängliga i AppSource kan du anpassa dem till Dynamics 365 Business Central-erbjudanden och skicka dem i [Partnercenter.](https://partner.microsoft.com/) Läs mer i [Create a Dynamics 365 Business Central offer (Skapa ett Erbjudande för Dynamics 365 Business Central).](partner-center-portal/create-new-business-central-offer.md) |
| Cortana Intelligence | No | Microsoft har utvecklat färdplanen för Cortana Intelligence, så vi av listade bort Cortana Intelligence-liveerbjudanden från AppSource. Dessa erbjudanden kan inte längre upptäckas av kunder och har inte flyttats till Partnercenter. Om du vill göra dina erbjudanden tillgängliga på den kommersiella marknadsplatsen anpassar du dina erbjudanden till SaaS-erbjudanden (Programvara som en tjänst) och skickar dem i [Partnercenter.](https://partner.microsoft.com/) Läs mer på [checklistan för skapande av SaaS-erbjudande i Partnercenter.](./plan-saas-offer.md) |

## <a name="i-cant-find-my-cloud-partner-portal-offers-in-partner-center"></a>Jag hittar inte mina Cloud Partner Portal i Partnercenter

Vad som visas i Partnercenter beror på vilka program du har registrerats i, vilka konton du tillhör samt de användarroller och behörigheter som du har tilldelats. Det finns många Partner Center-program och du kan vara registrerad i flera program. Du kan också ha åtkomst till flera konton med samma användarautentiseringsuppgifter.

De erbjudanden som du skapade i Cloud Partner Portal är tillgängliga i Partnercenter under programmet **Commercial Marketplace** och under det konto som används för att skapa erbjudandena. Följ stegen nedan för att kontrollera att du visar rätt program och rätt konto. Andra felsökningstips finns i [Hantera ditt Partnercenter-konto.](/partner-center/partner-center-account-setup)

### <a name="access-the-right-program-in-partner-center"></a>Få åtkomst till rätt program i Partnercenter

1. Logga in på [Partner Center med](https://partner.microsoft.com/dashboard/commercial-marketplace/overview) samma autentiseringsuppgifter som användes för att logga in på Cloud Partner Portal. Navigeringsfönstret till vänster visar alternativ som är associerade med de program som du är registrerad i.

    Exempel: Anta att du har åtkomst till tre program: MPN-programmet, hänvisningsprogrammet och programmet På den kommersiella marknadsplatsen. När du loggar in på Partnercenter visas dessa tre program i navigeringsfönstret.

2. Välj **Översikt över kommersiell marknadsplats**  >  **för** att få åtkomst till dina erbjudanden.

    Om du inte ser programmet för Den kommersiella marknadsplatsen i navigeringsfönstret till vänster kan du ha fel konto. Följ stegen i nästa avsnitt för att få åtkomst till rätt konto.

    [![Skärmbild som visar översiktsmenyn i Partnercenter](media/cpp-pc-faq/overview-menu.png "Visar partnercentrets översiktsmeny")](media/cpp-pc-faq/overview-menu.png#lightbox)

### <a name="access-the-right-account-in-partner-center"></a>Få åtkomst till rätt konto i Partnercenter

Om du är en del av flera konton visas en kontoväljarknapp i Partnercenter som markerats med två pilar i navigeringsmenyn till vänster. Välj knappen kontoväljare för att visa en lista över alla konton som du tillhör. Välj ett konto i listan för att växla till det och se alla program och all information som hör till det kontot. Om du inte ser någon knapp för kontoväljare i navigeringsmenyn är du medlem i ett enda konto.

[![Skärmbild som visar knappen för kontoväljaren i Partnercenter.](media/cpp-pc-faq/picker-button.png "Visar kontoväljaren i Partnercenter")](media/cpp-pc-faq/picker-button.png#lightbox)

## <a name="how-do-i-create-new-offers"></a>Hur gör jag för att skapa nya erbjudanden?

Öppna Programmet för den kommersiella marknadsplatsen [i Partnercenter](https://partner.microsoft.com/dashboard/commercial-marketplace/overview) för att skapa nya erbjudanden. På sidan Översikt väljer du **+ Nytt erbjudande.**

[![Skärmbild som visar översiktsmenyn i Partnercenter.](media/cpp-pc-faq/new-offer.png "Visar partnercentrets översiktsmeny")](media/cpp-pc-faq/new-offer.png#lightbox)

## <a name="i-cant-sign-in-and-need-to-open-a-support-ticket"></a>Jag kan inte logga in och behöver öppna en supportbiljett

Om du inte kan logga in på ditt konto kan du öppna en [supportbiljett](https://partner.microsoft.com/support/v2/?stage=1) här.

## <a name="where-are-instructions-for-using-partner-center"></a>Var finns instruktioner för att använda Partnercenter?

Gå till dokumentationen [för den kommersiella marknadsplatsen.](index.yml)och expandera **sedan Commercial Marketplace Portal i Partnercenter.** Om du vill se hjälpartiklar för att skapa erbjudanden i Partnercenter **expanderar du Skapa ett nytt erbjudande.**

## <a name="what-are-the-publishing-and-offer-management-differences"></a>Vilka skillnader finns det i publicerings- och erbjudandehanteringen?

Här är några skillnader mellan Cloud Partner Portal och Partnercenter.

### <a name="modular-publishing-capabilities"></a>Modulära publiceringsfunktioner

Partnercenter tillhandahåller ett modulärt publiceringsalternativ där du kan välja vilka ändringar du vill publicera i stället för att alltid publicera alla uppdateringar samtidigt. Följande skärm visar till exempel att de enda ändringar som valts för att publiceras är ändringarna i **Egenskaper och** **Erbjudandelista.** De ändringar du gör på förhandsgranskningssidan publiceras inte.

[![Skärmbild som visar sidan Granska och publicera i Partnercenter.](media/cpp-pc-faq/review-page.png "Visar sidan Granska och publicera i Partnercenter")](media/cpp-pc-faq/review-page.png#lightbox)

De uppdateringar som du inte publicerar sparas som utkast. Fortsätt att använda förhandsversionen av ditt erbjudande för att verifiera ditt erbjudande innan du gör det live för allmänheten.

### <a name="enhanced-preview-options"></a>Förbättrade förhandsgranskningsalternativ

Partnercenter innehåller en [jämförelsefunktion med](partner-center-portal/update-existing-offer.md#compare-changes-to-your-offer) förbättrade filtreringsalternativ. Detta ger dig möjlighet att jämföra med förhandsversionen och live-versionerna av erbjudandet.

[![Skärmbild som visar jämförelsefunktionen i Partnercenter.](media/cpp-pc-faq/compare.png "Visar jämförelsefunktionen i Partnercenter")](media/cpp-pc-faq/compare.png#lightbox)

### <a name="branding-and-navigation-changes"></a>Varumärkes- och navigeringsändringar

Du kommer att märka några varumärkesändringar. SKU:er *är till* exempel märkta som *planer* i Partnercenter:

[![Skärmbild som visar sidan Partnercenter-planer.](media/cpp-pc-faq/plans.png "Visar sidan Partnercenter-planer")](media/cpp-pc-faq/plans.png#lightbox)

Dessutom samlas den information som du tidigare angav på **sidorna Marketplace-** eller  **Butiksinformation** (konsulttjänst, Power BI-app) i Cloud Partner Portal nu på sidan med erbjudandelistan i Partnercenter:

[! [Skärmbild som visar sidan med erbjudanden i Partnercenter.] (media/cpp-pc-faq/offer-listing.png](media/cpp-pc-faq/offer-listing.png#lightbox)

Informationen som du tidigare angav för SKU:er på en enda sida i Cloud Partner Portal kan nu samlas in på flera sidor i Partnercenter:

- Sidan Planera installation
- Sidan planlista
- Sidan Planera tillgänglighet
- Sidan Planera teknisk konfiguration, som du ser här:

[![Visar sidan Teknisk konfiguration för Partnercenter.](media/cpp-pc-faq/technical-configuration.png)](media/cpp-pc-faq/technical-configuration.png#lightbox)

Ditt erbjudande-ID visas nu i det vänstra navigeringsfältet i erbjudandet:

![Visar partnercentrets erbjudande-ID](media/cpp-pc-faq/offer-id.png)

### <a name="stop-selling-an-offer"></a>Sluta sälja ett erbjudande

Du kan begära att [sluta sälja ett erbjudande](partner-center-portal/update-existing-offer.md#stop-selling-an-offer-or-plan) på marketplace direkt från Partner Center-portalen. Alternativet finns på sidan Översikt **över erbjudande** för ditt erbjudande.

[![Skärmbild som visar partnercentersidan för att sluta sälja ett erbjudande.](media/cpp-pc-faq/stop-sell.png "Visar partnercentersidan för att sluta sälja ett erbjudande")](media/cpp-pc-faq/stop-sell.png#lightbox)
<br><br>

## <a name="are-the-cloud-partner-portal-rest-apis-still-supported"></a>Stöds Cloud Partner Portal REST-API:er fortfarande?

De Cloud Partner Portal API:erna är integrerade med Partner Center och fortsätter att fungera. Övergången till Partnercenter medför små ändringar. Granska tabellen nedan för att se till att koden fortsätter att fungera i Partnercenter.

| API | Ändra beskrivning | Påverkan |
| --- | --- | --- |
| POST Publish, GoLive, Cancel | För migrerade erbjudanden har svarshuvudet ett annat format, men fortsätter att fungera på samma sätt, vilket anger en relativ sökväg för att hämta åtgärdsstatusen. | När du skickar någon av motsvarande POST-begäranden för ett erbjudande har Location-huvudet något av två format beroende på migreringsstatusen för erbjudandet: <ul><li>Icke-migrerade erbjudanden: `/api/operations/{PublisherId}${offerId}$2$preview?api-version=2017-10-31`</li><li>Migrerade erbjudanden: `/api/publishers/{PublisherId}/offers/{offereId}/operations/408a4835-0000-1000-0000-000000000000?api-version=2017-10-31`</li></ul>|
| GET-åtgärd | För erbjudanden som tidigare hade stöd för fältet "notification-email" i svaret blir det här fältet inaktuellt och returneras inte längre för migrerade erbjudanden. | För migrerade erbjudanden skickar vi inte längre meddelanden till listan över e-postmeddelanden som anges i begärandena. API-tjänsten överensstämmer i stället med processen för e-postavisering i Partnercenter för att skicka e-postmeddelanden. Mer specifikt skickas meddelanden om åtgärdsförloppet till den e-postadress som angetts i avsnittet Säljarkontaktinformation i kontoinställningarna i Partnercenter.<br><br>Kontrollera att e-postadressen som angetts i avsnittet Säljarkontaktinformation [i Kontoinställningar](https://partner.microsoft.com/dashboard/account/management) i Partnercenter är korrekt för att få meddelanden. |
|||