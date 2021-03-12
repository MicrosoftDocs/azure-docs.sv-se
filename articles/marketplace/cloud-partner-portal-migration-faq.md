---
title: Vanliga frågor om över gång från Cloud Partner Portal till Partner Center – Microsoft Commercial Marketplace
description: Svar på vanliga frågor om över gångs erbjudanden från Cloud Partner Portal till Partner Center.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: mingshen-ms
ms.author: mingshen
ms.date: 07/14/2020
ms.openlocfilehash: e071692a1c6fe423b048dab884164d9b3c98f1fd
ms.sourcegitcommit: d135e9a267fe26fbb5be98d2b5fd4327d355fe97
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/10/2021
ms.locfileid: "102613620"
---
# <a name="frequently-asked-questions-about-transitioning-from-the-cloud-partner-portal-to-partner-center"></a>Vanliga frågor om över gång från Cloud Partner Portal till Partner Center

Cloud Partner Portal har övergått till Partner Center. Partner Center erbjuder en strömlinjeformad, integrerad upplevelse för att publicera nya erbjudanden på [Microsoft AppSource](https://appsource.microsoft.com/) eller [Azure Marketplace](https://azuremarketplace.microsoft.com/) och för att hantera befintliga erbjudanden som har migrerats från Cloud Partner Portal. Alla funktioner i Cloud Partner Portal är tillgängliga i Partner Center. Den här artikeln handlar om vanliga frågor om detta.

## <a name="what-does-the-transition-to-partner-center-mean-for-me"></a>Vad betyder över gången till Partner Center åt mig?

Du kan fortsätta att göra affärer i Partner Center:

| Område | Ändringar |
| --- | --- |
| Konto | Du behöver inte skapa ett nytt Partner Center-konto. Du kan använda dina befintliga Cloud Partner Portal autentiseringsuppgifter för att logga in på Partner Center där du nu kommer att hantera ditt konto, användare, behörigheter och fakturering. Publicerings avtalet och företags profil informationen migreras till ditt nya partner Center-konto, tillsammans med eventuell utbetalnings profil information, användar konton och behörigheter samt aktiva erbjudanden. Läs mer i [Hantera ditt kommersiella Marketplace-konto i Partner Center](partner-center-portal/manage-account.md). |
| Erbjud hanterings upplevelse för publicering och erbjudande | Vi har flyttat dina erbjudande data från Cloud Partner Portal till Partner Center. Du kommer nu att få åtkomst till dina erbjudanden i Partner Center, vilket ger en bättre användar upplevelse och intuitivt gränssnitt. Lär dig hur du [uppdaterar ett befintligt erbjudande i den kommersiella marknads platsen](partner-center-portal/update-existing-offer.md). |
| Tillgänglighet för dina erbjudanden på den kommersiella marknaden | Inga ändringar. Om ditt erbjudande är aktivt i den kommersiella marknads platsen fortsätter det att vara Live. |
| Nya inköp och distributioner | Inga ändringar. Dina kunder kan fortsätta att köpa och distribuera erbjudanden utan avbrott. |
| Utbetalningar | Alla inköp och distributioner fortsätter att betalas ut till dig som vanligt. Lär dig mer om [att betala på den kommersiella Marketplace](/partner-center/marketplace-get-paid?context=/azure/marketplace/context/context). |
| API-integration med befintliga [Cloud Partner Portal-API: er](cloud-partner-portal-api-overview.md) | Befintliga Cloud Partner Portal-API: er stöds fortfarande och befintliga integreringar fungerar fortfarande. Vill du veta mer på, [kommer Cloud Partner Portal REST-API: er att stödjas?](#are-the-cloud-partner-portal-rest-apis-still-supported) |
| Analys | Du kan fortsätta att övervaka försäljning, utvärdera prestanda och optimera dina erbjudanden på den kommersiella Marketplace genom att visa analyser i Partner Center. Det finns skillnader mellan hur analys rapporter visas i CPP och partner Center. Till exempel har **säljare insikter** i cpp en **order & fliken användning** som visar data för användnings erbjudanden och icke-användnings erbjudanden, men på sidan beställningar i Partner Center har sidan **beställningar** en separat flik för SaaS-erbjudanden. Lär dig mer [om åtkomst analys rapporter för den kommersiella marknads platsen i Partner Center](partner-center-portal/analytics.md). |
|||

## <a name="do-i-need-to-create-a-new-account-to-manage-my-offers-in-partner-center"></a>Måste jag skapa ett nytt konto för att hantera mina erbjudanden i Partner Center?

Nej, ditt konto kommer att bevaras. Det innebär att om du är en befintlig partner kan du använda dina befintliga Cloud Partner Portal kontoautentiseringsuppgifter för att logga in på Partner Center. Skapa inte ett nytt konto eller några erbjudanden som skapats i Cloud Partner Portal och som inte har flyttats till Partner Center.

## <a name="what-pages-in-partner-center-correspond-to-pages-i-used-in-the-cloud-partner-portal"></a>Vilka sidor i Partner Center motsvarar sidor som jag använde i Cloud Partner Portal?

Nedan visas länkar till Partner Center för sidor som används ofta i Cloud Partner Portal. Om du sparade Cloud Partner Portal länkar som bok märken, vill du uppdatera dem.

| Cloud Partner Portal sida | Cloud Partner Portal sid länk | Länk till Partner Center-sidan |
| --- | --- | --- |
| Sidan med alla erbjudanden | [https://cloudpartner.azure.com/#alloffers](https://cloudpartner.azure.com/#alloffers) | [https://partner.microsoft.com/dashboard/commercial-marketplace/overview](https://partner.microsoft.com/dashboard/commercial-marketplace/overview) |
| Sidan med alla utgivare | [https://cloudpartner.azure.com/#publishers](https://cloudpartner.azure.com/#publishers) | [https://partner.microsoft.com/dashboard/account/v3/publishers/list](https://partner.microsoft.com/dashboard/account/v3/publishers/list) |
| Utgivar profil | [https://cloudpartner.azure.com/#profile](https://cloudpartner.azure.com/#profile) | [https://partner.microsoft.com/dashboard/account/management](https://partner.microsoft.com/dashboard/account/management) |
| Användarsidan | [https://cloudpartner.azure.com/#users](https://cloudpartner.azure.com/#users) | [https://partner.microsoft.com/dashboard/account/usermanagement](https://partner.microsoft.com/dashboard/account/usermanagement) |
| Sidan historik | [https://cloudpartner.azure.com/#history](https://cloudpartner.azure.com/#history) | Historik funktionen stöds inte ännu i Partner Center. |
| Instrument panel för insikter | [https://cloudpartner.azure.com/#insights](https://cloudpartner.azure.com/#insights) | [https://partner.microsoft.com/dashboard/commercial-marketplace/analytics/summary](https://partner.microsoft.com/dashboard/commercial-marketplace/analytics/summary) |
| Utbetalnings rapport | [https://cloudpartner.azure.com/#insights/payout](https://cloudpartner.azure.com/#insights/payout) | [https://partner.microsoft.com/dashboard/payouts/reports/incentivepayments](https://partner.microsoft.com/dashboard/payouts/reports/incentivepayments) |
|||

## <a name="payout-report-differences"></a>Skillnader i utbetalnings rapport

Detta är skillnaderna i utbetalnings rapporten mellan den tillbakadragna Cloud Partner Portal och det aktuella partner centret:

| Partnerportalen i molnet | Partnercenter |
| --- | --- |
| **Länk**: https://cloudpartner.azure.com/ | **Länk**: https://partner.microsoft.com/dashboard/payouts/reports/transactionhistory och https://partner.microsoft.com/dashboard/payouts/reports/incentivepayments |
| **Navigering**: utbetalnings rapportering som tillhandahålls i insikts utbetalning | **Navigering**: utbetalnings rapport som tillhandahålls i Partner Center – utbetalnings ikon |
| **Omfattning**:<ul><li>Transaktion per rad objekt är synligt för insamling i arbete, samlas in och betalas.</li><li>Rapportering – visar alla rad artiklar när inköps ordern har skapats, inklusive pågående insamling och fakturering, samt insamlings status och rad artiklar som ännu inte är berättigade att betala.</li></ul> | **Omfattning**:<ul><li>Visar rad artiklarna efter att de bedömts som berättigade intäkter.</li><li>Kunderna betalar till Microsoft först och sedan kan ISV: erna se utbetalnings rapporten som startar.</li><li>Utbetalnings rapporten visar inte insamlingen pågår och faktureringen pågår.</li></ul> |
| **Transaktionen är inte klar för utbetalning**: fakturering pågår | **Transaktionen är inte klar för utbetalning**: nästa beräknade betalning: utbetalnings statusen är i tillståndet Unprocessed. |
| **Utbetalnings status**: ej tillämpligt | **Utbetalnings status**:<ul><li>Obearbetad: amorteringen är berättigad till betalning.</li><li>Kommande: du kommer att skickas till utgivaren i nästa månads utbetalning.</li><li>Skickat: betalningen har skickats till din bank.</li></ul> |
|||

## <a name="what-about-offers-i-published-in-the-cloud-partner-portal"></a>Vad händer om jag har publicerat i Cloud Partner Portal?

Erbjudandena har flyttats till Partner Center och kommer att vara tillgängliga när du har loggat in på Partner Center, med undantag för tjänsten Dynamics NAV Managed och Cortana Intelligence erbjudanden. Om ditt erbjudande var aktivt på den kommersiella marknads platsen fortsätter det att vara Live och kunderna kan fortsätta att köpa och distribuera dem utan avbrott. Se nästa fråga, **vilka erbjudanden som flyttats till Partner Center?**, för mer information.

## <a name="what-offers-were-moved-to-partner-center"></a>Vilka erbjudanden har flyttats till Partner Center?

Alla erbjudande typer som tidigare stöds i Cloud Partner Portal stöds i Partner Center, med undantag för Dynamics NAV Managed Service och Cortana Intelligence erbjudanden.

För de erbjudande typer som stöds i Partner Center har alla erbjudanden flyttats oavsett deras status. utkast, de visas och för hands versions erbjudanden flyttas också.

| Erbjudandetyp | Har du flyttat till Partner Center? | Nästa steg |
| --- | --- | --- |
| SaaS | Ja | Logga in på Partner Center för att skapa nya erbjudanden och hantera erbjudanden som skapats i Cloud Partner Portal. Läs mer i [planera ett SaaS-erbjudande för den kommersiella marknads platsen](plan-saas-offer.md). |
| Virtuell dator | Ja | Logga in på Partner Center för att skapa nya erbjudanden och hantera erbjudanden som skapats i Cloud Partner Portal. Läs mer i [planera ett erbjudande för virtuella datorer](marketplace-virtual-machines.md). |
| Azure-program | Ja | Logga in på Partner Center för att skapa nya erbjudanden och hantera erbjudanden som skapats i Cloud Partner Portal. Läs mer i [skapa ett erbjudande för Azure-program](create-new-azure-apps-offer.md). |
| Dynamics 365 Business Central | Ja | Logga in på Partner Center för att skapa nya erbjudanden och hantera erbjudanden som skapats i Cloud Partner Portal. Läs mer i [skapa ett Dynamics 365 Business Central-erbjudande](partner-center-portal/create-new-business-central-offer.md). |
| Dynamics 365 för kund engagemang & PowerApps | Ja | Logga in på Partner Center för att skapa nya erbjudanden och hantera erbjudanden som skapats i Cloud Partner Portal. Läs mer i [skapa en Dynamics 365 för kund engagemang & PowerApps-erbjudande](partner-center-portal/create-new-customer-engagement-offer.md). |
| Dynamics 365 for Operations | Ja | Logga in på Partner Center för att skapa nya erbjudanden och hantera erbjudanden som skapats i Cloud Partner Portal. Läs mer i [skapa en Dynamics 365 för drift erbjudande](partner-center-portal/create-new-operations-offer.md). |
| Power BI-app | Ja | Logga in på Partner Center för att skapa nya erbjudanden och hantera erbjudanden som skapats i Cloud Partner Portal. Läs mer i [skapa en Power BI-app för AppSource](partner-center-portal/create-power-bi-app-offer.md). |
| IoT Edge modul | Ja | Logga in på Partner Center för att skapa nya erbjudanden och hantera erbjudanden som skapats i Cloud Partner Portal. Läs mer i [skapa, konfigurera och publicera ett IoT Edge modul-erbjudande på Azure Marketplace](partner-center-portal/azure-iot-edge-module-creation.md). |
| Container | Ja | Logga in på Partner Center för att skapa nya erbjudanden och hantera erbjudanden som skapats i Cloud Partner Portal. Läs mer i [skapa ett erbjudande för Azure Container](./create-azure-container-offer.md). |
| Konsulttjänst | Ja | Logga in på Partner Center för att skapa nya erbjudanden och hantera erbjudanden som skapats i Cloud Partner Portal. Läs mer i [skapa ett konsult tjänst erbjudande](./create-consulting-service-offer.md). |
| Hanterad tjänst | Ja | Logga in på Partner Center för att skapa nya erbjudanden och hantera erbjudanden som skapats i Cloud Partner Portal. Läs mer i [skapa ett hanterat tjänst erbjudande](./plan-managed-service-offer.md). |
| Hanterad tjänst för Dynamics NAV | Inga | Microsoft har utvecklats för att hantera Dynamics NAV Managed Service till [Dynamics 365 Business Central](/dynamics365/business-central/), så vi debiteras de Live-erbjudanden som hanteras av Dynamics NAV Managed Service från AppSource. Dessa erbjudanden kan inte längre identifieras av kunder och har inte flyttats till Partner Center. Om du vill göra dina erbjudanden tillgängliga i AppSource kan du anpassa dem till Dynamics 365 Business Central-erbjudanden och skicka dem till [partner Center](https://partner.microsoft.com/). Läs mer i [skapa ett Dynamics 365 Business Central-erbjudande](partner-center-portal/create-new-business-central-offer.md). |
| Cortana Intelligence | Inga | Microsoft har utvecklats produktens väg karta för Cortana Intelligence, så vi har tagit bort Cortana Intelligence Live-erbjudanden från AppSource. Dessa erbjudanden kan inte längre identifieras av kunder och har inte flyttats till Partner Center. Om du vill göra dina erbjudanden tillgängliga på den kommersiella Marketplace kan du anpassa dina erbjudanden till SaaS (Software as a Service) och skicka dem i [partner Center](https://partner.microsoft.com/). Läs mer i [Check lista för att skapa SaaS i Partner Center](./plan-saas-offer.md). |

## <a name="i-cant-find-my-cloud-partner-portal-offers-in-partner-center"></a>Jag kan inte hitta mina Cloud Partner Portal erbjudanden i Partner Center

Vad du ser i partner centret beror på vilka program som du har registrerat i, de konton som du tillhör och de användar roller och behörigheter som du har tilldelats. Det finns många tillgängliga Partner Center-program och du kan vara registrerad i flera program. Du kan också ha åtkomst till flera konton med samma användarautentiseringsuppgifter.

De erbjudanden som du har skapat i Cloud Partner Portal är tillgängliga i Partner Center under det **kommersiella Marketplace** -programmet och under kontot som används för att skapa erbjudandena. Följ stegen nedan för att se till att du visar rätt program och rätt konto. Andra fel söknings tips finns i [Hantera ditt partner Center-konto](/partner-center/partner-center-account-setup).

### <a name="access-the-right-program-in-partner-center"></a>Åtkomst till rätt program i Partner Center

1. Logga in på [partner Center](https://partner.microsoft.com/dashboard/commercial-marketplace/overview) med samma autentiseringsuppgifter som används för att logga in på Cloud Partner Portal. I navigerings fönstret till vänster visas alternativ som är associerade med de program som du har registrerat i.

    Exempel: Antag att du har åtkomst till tre program: MPN-programmet, programmet för hänvisning och programmet för kommersiella marknads platser. När du loggar in på Partner Center visas de här tre programmen i navigerings fönstret.

2. Välj **kommersiell Marketplace**-  >  **Översikt** för att få åtkomst till dina erbjudanden.

    Om du inte ser programmet för kommersiella marknads platser i navigerings fönstret till vänster kan du ha fel konto. Följ stegen i nästa avsnitt för att få åtkomst till rätt konto.

    [![Skärm bild som visar översikts menyn för partner Center](media/cpp-pc-faq/overview-menu.png "Visar översikts menyn för partner Center")](media/cpp-pc-faq/overview-menu.png#lightbox)

### <a name="access-the-right-account-in-partner-center"></a>Få åtkomst till rätt konto i Partner Center

Om du är en del av flera konton visas en knapp för konto väljaren som marker ATS med två pilar i navigerings menyn till vänster i Partner Center. Välj knappen konto väljaren för att visa en lista över alla konton som du tillhör. Välj ett konto i listan för att växla till det och se alla program och information som hör till det kontot. Om du inte ser knappen konto väljare i navigerings menyn är du medlem i ett enda konto.

[![Skärm bild som visar knappen för konto väljaren för partner Center.](media/cpp-pc-faq/picker-button.png "Visar knappen för konto väljaren för partner Center")](media/cpp-pc-faq/picker-button.png#lightbox)

## <a name="how-do-i-create-new-offers"></a>Hur gör jag för att skapa nya erbjudanden?

Få åtkomst till programmet för kommersiella marknads platser i [partner Center](https://partner.microsoft.com/dashboard/commercial-marketplace/overview) för att skapa nya erbjudanden. På sidan Översikt väljer du **+ nytt erbjudande**.

[![Skärm bild som visar översikts menyn för partner Center.](media/cpp-pc-faq/new-offer.png "Visar översikts menyn för partner Center")](media/cpp-pc-faq/new-offer.png#lightbox)

## <a name="i-cant-sign-in-and-need-to-open-a-support-ticket"></a>Jag kan inte logga in och behöver öppna ett support ärende

Om du inte kan logga in på ditt konto kan du öppna ett [support ärende](https://partner.microsoft.com/support/v2/?stage=1) här.

## <a name="where-are-instructions-for-using-partner-center"></a>Var finns instruktioner för att använda Partner Center?

Gå till den [kommersiella Marketplace-dokumentationen](index.yml)och expandera sedan den **kommersiella Marketplace-portalen i Partner Center**. Om du vill se hjälp artiklar om hur du skapar erbjudanden i Partner Center expanderar du **skapa ett nytt erbjudande**.

## <a name="what-are-the-publishing-and-offer-management-differences"></a>Vad är skillnaderna mellan publicering och erbjudande?

Här följer några skillnader mellan Cloud Partner Portal-och partner Center.

### <a name="modular-publishing-capabilities"></a>Funktioner för modulär publicering

Partner Center tillhandahåller ett modulärt publicerings alternativ som låter dig välja de ändringar som du vill publicera i stället för att alltid publicera alla uppdateringar samtidigt. Följande skärm visar till exempel att de enda ändringar som är markerade för publicering är ändringar i **Egenskaper** och  **erbjudande lista**. De ändringar du gör på sidan för förhands granskning kommer inte att publiceras.

[![Skärm bilden visar gransknings-och publicerings sidan för partner Center.](media/cpp-pc-faq/review-page.png "Visar gransknings-och publicerings sidan för partner Center")](media/cpp-pc-faq/review-page.png#lightbox)

Uppdateringarna som du inte publicerar sparas som utkast. Fortsätt att använda din för hands version av erbjudandet för att verifiera ditt erbjudande innan du gör det tillgängligt för allmänheten.

### <a name="enhanced-preview-options"></a>Förbättrade alternativ för förhands granskning

Partner Center innehåller en [jämförelse funktion](partner-center-portal/update-existing-offer.md#compare-changes-to-your-offer) med förbättrade filtrerings alternativ. Det ger dig möjlighet att jämföra mot för hands versionen och live-versionen av erbjudandet.

[![Skärm bild som visar jämförelse funktionen i Partner Center.](media/cpp-pc-faq/compare.png "Visar jämförelse funktionen i Partner Center")](media/cpp-pc-faq/compare.png#lightbox)

### <a name="branding-and-navigation-changes"></a>Anpassnings-och navigerings ändringar

Du märker några anpassnings ändringar. *SKU: er* är till exempel märkta som *planer* i Partner Center:

[![Skärm bild som visar sidan Partner Center-planer.](media/cpp-pc-faq/plans.png "Visar sidan Partner Center-planer")](media/cpp-pc-faq/plans.png#lightbox)

Informationen som du tidigare angav i **Marketplace** -eller butik- **information**  (konsult tjänst, Power BI app) i Cloud Partner Portal har nu samlats in på sidan för **erbjudande listan** i Partner Center:

[! [Skärm bild som visar List sidan för erbjudande för partner Center.] (Media/cpp-PC – FAQ/offer-listing.png](media/cpp-pc-faq/offer-listing.png#lightbox)

Den information som du tidigare angav för SKU: er på en enda sida i Cloud Partner Portal kan nu samlas in på flera sidor i Partner Center:

- Installations sida för plan
- Plan List sida
- Plan tillgänglighets sida
- Sidan planera teknisk konfiguration, som du ser här:

[![Visar sidan teknisk konfiguration för partner Center.](media/cpp-pc-faq/technical-configuration.png)](media/cpp-pc-faq/technical-configuration.png#lightbox)

Ditt erbjudande-ID visas nu i det vänstra navigerings fältet i erbjudandet:

![Visar plats för erbjudande-ID för partner Center](media/cpp-pc-faq/offer-id.png)

### <a name="stop-selling-an-offer"></a>Sluta sälja ett erbjudande

Du kan begära att [sluta sälja ett erbjudande](partner-center-portal/update-existing-offer.md#stop-selling-an-offer-or-plan) på Marketplace direkt från Partner Center-portalen. Alternativet finns på sidan **erbjudande översikt** för ditt erbjudande.

[![Skärm bild som visar sidan Partner Center för att sluta sälja ett erbjudande.](media/cpp-pc-faq/stop-sell.png "Visar sidan Partner Center för att sluta sälja ett erbjudande")](media/cpp-pc-faq/stop-sell.png#lightbox)
<br><br>

## <a name="are-the-cloud-partner-portal-rest-apis-still-supported"></a>Stöds Cloud Partner Portal REST-API: er fortfarande?

Cloud Partner Portal API: er är integrerade med partner Center och kommer att fortsätta att fungera. Över gången till Partner Center introducerar små ändringar. Granska tabellen nedan för att se till att koden fortsätter att fungera i Partner Center.

| API | Ändra beskrivning | Påverkan |
| --- | --- | --- |
| Publicera, GoLive, Avbryt | För migrerade erbjudanden har svars huvudet ett annat format men fortsätter att fungera på samma sätt, vilket anger en relativ sökväg för att hämta åtgärds statusen. | När du skickar någon av motsvarande POST-förfrågningar för ett erbjudande får plats huvudet ett av två format beroende på status för migreringen: <ul><li>Icke-migrerade erbjudanden: `/api/operations/{PublisherId}${offerId}$2$preview?api-version=2017-10-31`</li><li>Migrerade erbjudanden: `/api/publishers/{PublisherId}/offers/{offereId}/operations/408a4835-0000-1000-0000-000000000000?api-version=2017-10-31`</li></ul>|
| Hämta åtgärd | För erbjudanden som tidigare har stöd för fältet meddelande-e-post i svaret kommer det här fältet att föråldras och inte längre returneras för migrerade erbjudanden. | För migrerade erbjudanden skickar vi inte längre meddelanden till listan över e-postmeddelanden som anges i förfrågningarna. I stället justeras API-tjänsten med e-postprocessen för meddelanden i Partner Center för att skicka e-postmeddelanden. Mer specifikt skickas aviseringar om åtgärds förlopp till den e-postadress som anges i avsnittet säljar kontakt information i konto inställningarna i Partner Center.<br><br>Se till att den e-postadress som anges i avsnittet kontakt information för säljaren i [konto inställningarna](https://partner.microsoft.com/dashboard/account/management) i Partner Center är korrekt för att ta emot meddelanden. |
|||