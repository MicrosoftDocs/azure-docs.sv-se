---
title: Kom igång med ditt uppdaterade Azure-faktureringskonto
description: Kom igång med ditt uppdaterade Azure-faktureringskonto och lär dig hur den nya fakturerings- och kostnadshanteringsmiljön fungerar
author: bandersmsft
ms.reviewer: amberbhargava
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: conceptual
ms.date: 03/31/2021
ms.author: banders
ms.openlocfilehash: 4f7179a5ad35b4d3ca9a92119fb7b492e2aff779
ms.sourcegitcommit: 9f4510cb67e566d8dad9a7908fd8b58ade9da3b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/01/2021
ms.locfileid: "106122534"
---
# <a name="get-started-with-your-updated-azure-billing-account"></a>Kom igång med ditt uppdaterade Azure-faktureringskonto

Hanteringen av kostnader och fakturor är en av de viktigaste delarna i din molnmiljö, och hjälper dig att hålla koll på dina utgifter i molnet. För att göra det enklare för dig att hantera dina kostnader och fakturor uppdaterar Microsoft ditt Azure-faktureringskonto med förbättrade kostnadshanterings- och faktureringsfunktioner. Den här artikeln beskriver uppdateringarna av ditt faktureringskonto och går igenom de nya funktionerna.

> [!IMPORTANT]
> Innan ditt konto uppdateras får du ett e-postmeddelande från Microsoft som informerar dig om kontouppdateringarna. Meddelandet skickas 60 dagar innan ditt konto uppdateras.

## <a name="more-flexibility-with-your-new-billing-account"></a>Mer flexibilitet med ditt nya faktureringskonto

I följande diagram jämförs ditt gamla och nya faktureringskonto:

:::image type="content" source="./media/mosp-new-customer-experience/comparison-old-new-account.png" alt-text="Diagram som visar jämförelsen mellan fakturerings-hierarkin i det gamla och det nya kontot." border="false" lightbox="./media/mosp-new-customer-experience/comparison-old-new-account.png":::

Ditt nya faktureringskonto innehåller en eller flera faktureringsprofiler som du använder för att hantera dina fakturor och betalningsmetoder. Varje faktureringsprofil har ett eller flera fakturaavsnitt där du kan organisera kostnaderna på faktureringsprofilens faktura.

:::image type="content" source="./media/mosp-new-customer-experience/new-billing-account-hierarchy.png" alt-text="Diagram som visar den nya fakturerings-hierarkin." border="false" lightbox="./media/mosp-new-customer-experience/new-billing-account-hierarchy.png":::

Roller för faktureringskontot har den högsta behörighetsnivån. Dessa roller bör tilldelas till användare som behöver visa fakturor och spåra kostnader för hela ditt konto, t.ex. ekonomi- eller IT-chefer i en organisation eller den person som har registrerat sig för ett konto. Mer information finns i [Roller och uppgifter för faktureringskonton](../manage/understand-mca-roles.md#billing-account-roles-and-tasks). När ditt konto har uppdaterats tilldelas användaren med rollen som kontoadministratör i det gamla faktureringskontot en ägarroll i det nya kontot.

## <a name="billing-profiles"></a>Faktureringsprofiler

En faktureringsprofil används för att hantera dina fakturerings- och betalningsmetoder. I början av månaden skapas en månadsfaktura för varje faktureringsprofil på ditt konto. Fakturan innehåller respektive avgifter från den föregående månaden för alla prenumerationer som är kopplade till faktureringsprofilen.

När ditt konto uppdateras skapas en faktureringsprofil automatiskt för varje prenumeration. Prenumerationsavgifterna faktureras till respektive faktureringsprofil och visas på fakturan.

Roller för faktureringsprofiler har behörighet att visa och hantera fakturor och betalningsmetoder. De här rollerna bör tilldelas till användare som betalar fakturor, t.ex. medlemmar i redovisningsteamet i en organisation. Mer information finns i [Roller och uppgifter för faktureringsprofiler](../manage/understand-mca-roles.md#billing-profile-roles-and-tasks).

För varje prenumeration som du har gett andra behörighet att [visa fakturor](download-azure-invoice.md#allow-others-to-download-your-subscription-invoice) för, tilldelas användare som har en Azure-roll som ägare, deltagare, läsare eller faktureringsläsare, rollen som läsare i den aktuella faktureringsprofilen när ditt konto uppdateras.

## <a name="invoice-sections"></a>Fakturaavsnitt

Ett fakturaavsnitt används för att organisera kostnaderna på din faktura. Du kanske till exempel behöver en enda faktura, men vill sortera kostnaderna efter avdelning, grupp eller projekt. I det här scenariot har du en enda faktureringsprofil där du skapar ett fakturaavsnitt för varje avdelning, grupp eller projekt.

När ditt konto uppdateras skapas ett fakturaavsnitt för varje faktureringsprofil och den relaterade prenumerationen kopplas till fakturaavsnittet. När du lägger till fler prenumerationer kan du skapa ytterligare avsnitt och koppla prenumerationerna till fakturaavsnitten. Dessa avsnitt visas på faktureringsprofilens faktura och anger användningen för respektive prenumeration som du har kopplat till dem.

Roller för fakturaavsnitt har behörighet att styra vem som skapar Azure-prenumerationer. Rollerna bör tilldelas till användare som konfigurerar Azure-miljön för team i en organisation, t.ex. teknikchefer och tekniska arkitekter. Mer information finns i [Roller och uppgifter för fakturaavsnitt](../manage/understand-mca-roles.md#invoice-section-roles-and-tasks).

## <a name="enhanced-features-in-your-new-experience"></a>Förbättrade funktioner i den nya miljön

Den nya miljön innehåller följande kostnadshanterings- och faktureringsfunktioner som gör det enkelt att hantera kostnader och fakturor:

#### <a name="invoice-management"></a>Fakturahantering

**Mer förutsägbar månatlig faktureringsperiod** – Med det nya kontot börjar faktureringsperioden den första dagen i månaden och slutar den sista dagen i månaden, oavsett när du registrerar dig för Azure. En faktura skapas i början av varje månad och innehåller alla avgifter från föregående månad.

**Hämta en enda månads faktura för flera prenumerationer** – i ditt befintliga konto får du en faktura för varje Azure-prenumeration. När ditt konto har uppdaterats underhålls det befintliga beteendet, men du har flexibiliteten att konsolidera prenumerationernas kostnader på en enda faktura. När du har uppdaterat kontot följer du stegen nedan för att konsolidera dina avgifter på en enda faktura:

1. Logga in på [Azure-portalen](https://portal.azure.com).
2. Sök efter **Kostnadshantering + fakturering**.  
   ![Skärmbild som visar en sökning i Azure-portalen efter Kostnadshantering + fakturering.](./media/mosp-new-customer-experience/billing-search-cost-management-billing.png)
3. Välj **Azure-prenumerationer** på vänster sida av skärmen. 
4. Tabellen innehåller Azure-prenumerationer som du betalar för. I kolumnen fakturerings profil hittar du den fakturerings profil som debiteras för prenumerationen. Prenumerations avgifterna visas på fakturan för fakturerings profilen. Om du vill konsolidera avgifterna för alla prenumerationer på en enda faktura måste du koppla alla dina prenumerationer till en enda fakturerings profil.  
    :::image type="content" source="./media/mosp-new-customer-experience/list-azure-subscriptions.png" alt-text="Skärm bild som visar listan med Azure-prenumerationer." lightbox="./media/mosp-new-customer-experience/list-azure-subscriptions.png" :::
5. Välj en fakturerings profil som du vill använda. 
6. Välj en prenumeration som inte är länkad till den fakturerings profil som du valde i steg 5. Klicka på tre punkter (tre punkter) för prenumerationen. Välj **Ändra fakturaavsnitt**.  
    :::image type="content" source="./media/mosp-new-customer-experience/select-change-invoice-section.png" alt-text="Skärm bild som visar var du hittar alternativet att ändra faktura avsnittet." lightbox="./media/mosp-new-customer-experience/select-change-invoice-section-zoomed-in.png" :::
7. Välj den fakturerings profil som du valde i steg #5.  
    :::image type="content" source="./media/mosp-new-customer-experience/change-invoice-section.png" alt-text="Skärm bild som visar hur du ändrar faktura avsnittet." lightbox="./media/mosp-new-customer-experience/change-invoice-section-zoomed-in.png" :::
8. Välj **ändra**.
9. Upprepa steg 6-8 för alla andra prenumerationer. 

**Få en enda månadsfaktura för Azure-prenumerationer, supportavtal och Azure Marketplace-produkter** – Du får en enda månadsfaktura för alla avgifter, inklusive användningskostnader för Azure-prenumerationer och kostnader för supportavtal och Azure Marketplace-köp.

**Gruppera kostnader på fakturan utifrån dina behov** – Du kan gruppera kostnaderna på din faktura baserat på dina behov – efter avdelning, projekt eller team.

**Ange ett valfritt inköpsordernummer på fakturan** – Ange ett inköpsordernummer om du vill koppla fakturan till dina interna ekonomisystem. Du kan hantera och uppdatera den när du vill på Azure-portalen.

#### <a name="payment-management"></a>Betalningshantering

**Betala fakturor direkt med ett kreditkort** – Du behöver inte vänta på att den registrerade betalningen ska debiteras på ditt kreditkort. Du kan använda valfritt kreditkort för att betala en obetald eller förfallen faktura på Azure-portalen.

**Håll koll på alla betalningar som är kopplade till kontot** – Visa alla betalningar som är kopplade till ditt konto, inklusive betalningsmetod, betalat belopp och betalningsdatum, på Azure-portalen.

#### <a name="cost-management"></a>Kostnadshantering

**Schemalägg månatliga exporter av användningsdata till ett lagringskonto** – Publicera dina kostnads- och användningsdata automatiskt till ett lagringskonto varje dag, vecka eller månad.

#### <a name="account-and-subscription-management"></a>Konto- och prenumerationshantering

**Ge faktureringsbehörighet till flera administratörer** – Bevilja faktureringsbehörighet till flera användare så att de kan hantera faktureringen för ditt konto. Öka flexibiliteten genom att ge andra läsbehörighet, skrivbehörighet eller både läs- och skrivbehörighet.

**Skapa fler prenumerationer direkt på Azure-portalen** – Skapa alla dina prenumerationer med en enkel knapptryckning på Azure-portalen.

#### <a name="api-support"></a>API-stöd

**Hantera fakturering och kostnader via API:er, SDK och PowerShell** – Använd API:er för kostnadshantering, fakturering och förbrukning för att hämta fakturerings- och kostnadsdata till dina dataanalysverktyg.

**Hantera alla prenumerationsåtgärder via API:er, SDK och PowerShell** – Använd API:er för Azure-prenumerationer för att automatisera hanteringen av dina Azure-prenumerationer, t.ex. för att skapa, byta namn på eller avsluta en prenumeration.

## <a name="get-prepared-for-your-new-experience"></a>Förbered dig för den nya miljön

Vi rekommenderar att du gör följande för att förbereda dig för den nya miljön:

**Månatlig faktureringsperiod och annat fakturadatum**

I den nya miljön genereras din faktura runt den nionde dagen i varje månad, och innehåller alla avgifter från föregående månad. Datumet kan skilja sig från det datum då din faktura genereras i det gamla kontot. Om du delar dina fakturor med andra meddelar du dem om datumändringen.


**Fakturor under den första månaden efter migrering**

Dagen då ditt konto uppdateras slutförs dina befintliga ej fakturerade kostnader och du får fakturorna för dessa avgifter på dagen när du normalt får dina fakturor. John har till exempel två Azure-prenumerationer – Azure SUB 01 med fakturerings perioden från den femte dagen i månaden till den fjärde dagen i nästa månad och Azure sub 02 med fakturerings perioden från den tionde dagen i månaden till den nionde dagen i nästa månad. John får fakturor för båda Azure-prenumerationer vanligt vis den femte i månaden. Nu om Johns konto uppdateras den 4 april, kommer avgifterna för Azure SUB 01 från och med 5 mars till 4 april och avgifter för Azure sub 02 från den 10 mars till 4 april att slutföras. John får två fakturor, en för varje under 5 april. När kontot har uppdaterats kommer John fakturerings perioden att baseras på kalender månad och kommer att täcka alla kostnader som uppstår från början av en kalender månad till slutet av kalender månaden.  Fakturan för föregående kalender månads avgifter är tillgängliga den 9: e i månaden. I exemplet ovan får John en annan faktura den 5 maj för fakturerings perioden 5 april till den 30 april. 


**Nya API:er för fakturerings- och kostnadshantering**

Om du använder API:er för kostnadshantering och fakturering för att köra frågor mot och uppdatera fakturerings- eller kostnadsdata måste du använda nya API:er. Tabellen nedan innehåller de API:er som inte fungerar med det nya faktureringskontot och de ändringar som du måste göra i ditt nya faktureringskonto.

|API | Ändringar  |
|---------|---------|
|[API:et Billing Accounts – List](/rest/api/billing/2019-10-01-preview/billingaccounts/list) | I API:et Billing Accounts – List har ditt gamla faktureringskonto agreementType **MicrosoftOnlineServiceProgram**, och ditt nya faktureringskonto har agreementType **MicrosoftCustomerAgreement**. Om du har ett agreementType-beroende uppdaterar du det. |
|[Invoices – List By Billing Subscription](/rest/api/billing/2019-10-01-preview/invoices/listbybillingsubscription)     | Detta API returnerar bara fakturor som genererades innan ditt konto uppdaterades. Du måste använda API:et [Invoices – List By Billing Account](/rest/api/billing/2019-10-01-preview/invoices/listbybillingaccount) för att få fakturor som genereras i det nya faktureringskontot. |


## <a name="cost-management-updates-after-account-update"></a>Cost Management-uppdateringar efter kontouppdatering

Ditt uppdaterade Azure-faktureringskonto för ditt Microsoft-kundavtal ger dig tillgång till nya och utökade Cost Managements-upplevelser på Azure-portalen som inte var tillgängliga med ditt ”betala per användning”-konto.

### <a name="new-capabilities"></a>Nya funktioner

Följande nya funktioner är tillgängliga med ditt Azure-faktureringskonto.

#### <a name="new-billing-scopes"></a>Nya faktureringsomfång

Som en del av ditt uppdaterade konto har du tillgång till nya omfång i Cost Management + Billing. Förutom att underlätta den hierarkiska organiseringen och faktureringen är de också ett sätt att visa kombinerade avgifter från flera underliggande prenumerationer. Mer information om faktureringsomfång finns i [Omfång för Microsoft-kundavtal](../costs/understand-work-scopes.md#microsoft-customer-agreement-scopes).

Du kan också använda Cost Management-API:er för att visa kombinerade kostnadsvyer i högre omfång. Alla Cost Management-API:er som använder prenumerationsomfånget är fortfarande tillgängliga med några mindre ändringar i schemat. Mer information om API:erna finns i avsnitten om [Azure Cost Management-API:er](/rest/api/cost-management/) och [Azure Consumption-API:er](/rest/api/consumption/).

#### <a name="cost-allocation"></a>Kostnadsallokering

Med ditt uppdaterade konto kan du använda kostnadsallokeringsfunktioner för att fördela kostnader från delade tjänster i din organisation. Mer information om hur du allokerar kostnader finns i avsnittet [Skapa och hantera regler för kostnadsallokering i Azure](../costs/allocate-costs.md).

#### <a name="power-bi"></a>Power BI

Med Azure Cost Management-anslutningsappen för Power BI Desktop kan du skapa anpassade visualiseringar och rapporter över din Azure-användning och dina Azure-utgifter. Du kommer åt dina kostnader och användningsdata när du har anslutit till ditt uppdaterade konto. Mer information om Azure Cost Management-anslutningsappen för Power BI Desktop finns i [Skapa visuella objekt och rapporter med Azure Cost Management-kopplingen i Power BI Desktop](/power-bi/connect-data/desktop-connect-azure-cost-management).

### <a name="updated-capabilities"></a>Uppdaterade funktioner

Följande uppdaterade funktioner är tillgängliga med ditt Azure-faktureringskonto.

#### <a name="cost-analysis"></a>Kostnadsanalys

Precis som tidigare kan du visa och spåra din månatliga förbrukningskostnad, och nu kan du även visa kostnader för reservationer och Marketplace-köp i kostnadsanalyser.

Med ditt uppdaterade konto får du en enda faktura för alla Azure-avgifter. Nu har du även tillgång till en enda förenklad månadskalendervy som ersätter den tidigare faktureringsperiodvyn.

Om faktureringsperioden exempelvis var den 24 november till den 23 december med ditt gamla konto, blir perioden efter uppgraderingen den 1 november till den 30 november, den 1 december till den 31 december och så vidare.

:::image type="content" source="./media/mosp-new-customer-experience/billing-periods.png" alt-text="Skärm bild som visar en jämförelse mellan gamla och nya fakturerings perioder." lightbox="./media/mosp-new-customer-experience/billing-periods.png" :::

#### <a name="budgets"></a>Budgetar

Nu kan du skapa budgetar för faktureringskontot, så att du kan spåra kostnader mellan prenumerationer. Du kan också hålla koll på dina inköpskostnader med hjälp av budgetar. Mer information om budgetar finns i [Skapa och hantera Azure-budgetar](../costs/tutorial-acm-create-budgets.md).

#### <a name="exports"></a>Exporter

Det nya faktureringskontot har förbättrade exportfunktioner. Du kan till exempel skapa exporter för faktiska kostnader som inkluderar inköp eller periodiserade kostnader (kostnader för reservationsköp fördelade över inköpsperioden). Du kan också skapa en export för faktureringskontot för att hämta information om användning och avgifter för alla prenumerationer i faktureringskontot. Mer information om exporter finns i [Skapa och hantera exporterade data](../costs/tutorial-export-acm-data.md).

> [!NOTE]
> Exporter som skapades innan kontouppdateringen med typen **Månatlig export av den senaste månadens kostnader** exporterar data för den senaste kalendermånaden, inte den senaste faktureringsperioden.

Exempelvis skulle den exporterade CSV-filen för faktureringsperioden från den 23 december till den 22 januari innehålla kostnads- och användningsdata för den perioden. Efter uppdateringen kommer exporten att innehålla data för kalendermånaden. Till exempel från den 1 januari till den 31 januari och så vidare.

:::image type="content" source="./media/mosp-new-customer-experience/export-amortized-costs.png" alt-text="Skärm dum par som visar en jämförelse mellan Gammal och ny export information." lightbox="./media/mosp-new-customer-experience/export-amortized-costs.png" :::

## <a name="additional-information"></a>Ytterligare information

Följande avsnitt innehåller ytterligare information om den nya miljön.

**Inga tjänstavbrott** Azure-tjänster i din prenumeration fortsätter att köras utan avbrott. Det enda som uppdateras är faktureringsmiljön. Befintliga resurser, resursgrupper och hanteringsgrupper påverkas inte.

**Inga ändringar av Azure-resurser** Åtkomst till Azure-resurser som angavs med hjälp av rollbaserad åtkomst i Azure (Azure RBAC) påverkas inte av uppdateringen.

**Tidigare fakturor är tillgängliga i den nya miljön** Fakturor som skapades innan ditt konto uppdaterades är fortfarande tillgängliga på Azure-portalen.

**Fakturor för konto som uppdateras i mitten av månaden** Om ditt konto uppdateras i mitten av månaden får du en faktura för avgifter som ackumulerats fram till den dag då ditt konto uppdateras. Du får en till faktura för resten av månaden. Exempel: Ditt konto har en prenumeration och uppdateras den 15 september. Du får en faktura för avgifter som ackumulerats fram till den 15 september. Du får en ny faktura för perioden mellan den 15 september till och med den 30 september. Efter september får du en faktura per månad.

## <a name="need-help-contact-support"></a>Behöver du hjälp? Kontakta supporten.

Om du behöver hjälp kan du [kontakta supporten](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) så får du hjälp att lösa problemet snabbt.

## <a name="next-steps"></a>Nästa steg

I följande artiklar kan du läsa mer om ditt faktureringskonto.

- [Förstå administrativa roller för ditt nya faktureringskonto](../manage/understand-mca-roles.md)
- [Skapa en till Azure-prenumeration för ditt nya faktureringskonto](../manage/create-subscription.md)
- [Skapa avsnitt på din faktura och organisera dina kostnader](../manage/mca-section-invoice.md)