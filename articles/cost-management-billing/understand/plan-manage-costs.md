---
title: Planera för hantering av Azure-kostnader
description: Lär dig hur du planerar för hantering av Azure-kostnader samt använder funktioner för kostnadsspårning och -hantering för ditt Azure-konto.
author: bandersmsft
ms.reviewer: amberb
tags: billing
ms.service: cost-management-billing
ms.subservice: common
ms.topic: conceptual
ms.date: 09/22/2020
ms.author: banders
ms.custom: contperf-fy21q1
ms.openlocfilehash: 210f7daa9305f2fb343dfae050354264e2fd823e
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/27/2021
ms.locfileid: "98898570"
---
# <a name="plan-to-manage-azure-costs"></a>Planera för hantering av Azure-kostnader

Den här artikeln hjälper dig att komma igång med hur du planerar att hantera dina Azure-kostnader. När du registrerar dig för Azure finns det flera saker du kan göra för att få en bättre uppfattning om dina utgifter:

- Få uppskattningar av kostnader innan du lägger till tjänster med [priskalkylatorn](https://azure.microsoft.com/pricing/calculator/), Azure-prisdokumentet eller när du lägger till tjänster i Azure-portalen.
- Övervaka dina kostnader med [budgetar](../costs/tutorial-acm-create-budgets.md), [aviseringar](../costs/cost-mgt-alerts-monitor-usage-spending.md) och [kostnadsanalys](../costs/quick-acm-cost-analysis.md).
- Granska kostnaderna på fakturan genom att jämföra dem med [de detaljerade användningsfilerna](../manage/download-azure-invoice-daily-usage-date.md).
- Integrera fakturerings- och kostnadsdata i ditt eget rapportsystem med hjälp av API:erna för [fakturering](/rest/api/billing/) och [förbrukning](/rest/api/consumption/).
- Använd ytterligare resurser och verktyg för Enterprise-avtal (EA), Cloud Solution Provider (CSP) och Azure Sponsorship-kunder.
- Använd [några av de populäraste Azure-tjänsterna utan kostnad i 12 månader](../manage/create-free-services.md) med ett [kostnadsfritt Azure-konto](https://azure.microsoft.com/free/). Tillsammans med rekommendationerna nedan kan du läsa [Undvika att debiteras för ett kostnadsfritt konto](../manage/avoid-charges-free-account.md).

Om du behöver avbryta din Azure-prenumeration läser du [Avbryta din Azure-prenumeration](../manage/cancel-azure-subscription.md).

## <a name="get-estimated-costs-before-adding-azure-services"></a>Få en kostnadsuppskattning innan du lägger till Azure-tjänster

Använd något av följande verktyg för att beräkna kostnaden för att använda en Azure-tjänst:
- Priskalkylator för Azure
- Azure-prisdokument
- Azure Portal

Bilderna i följande avsnitt innehåller exempel på priser i amerikanska dollar. De angivna priserna är endast exempel. De är inte avsedda att representera faktiska kostnader. 

### <a name="estimate-cost-online-using-the-pricing-calculator"></a>Beräkna kostnaden online med priskalkylatorn

Ta en titt på [priskalkylatorn](https://azure.microsoft.com/pricing/calculator/) för att beräkna månadskostnaden för den tjänst du vill lägga till. Du kan ändra valutan för att få uppskattningen i din lokala valuta.

![Skärmbild av menyn för priskalkylatorn](./media/plan-manage-costs/pricing-calc.png)

Du kan visa uppskattade kostnader för alla Azure-tjänster från första part. I skärmbilden nedan uppskattas till exempel beräkningstimmarna för en virtuell A1-dator med Windows kosta 66.96 USD i månaden om den körs utan avbrott:

![Skärmbild av priskalkylatorn som visar den beräknade månadskostnaden för en virtuell A1-dator med Windows](./media/plan-manage-costs/pricing-calc-vm.png)

De angivna priserna är endast exempel. De är inte avsedda att representera faktiska kostnader.

Mer information om priser finns i [vanliga frågor och svar om prissättning](https://azure.microsoft.com/pricing/faq/). Om du vill prata med en Azure-säljare ringer du telefonnumret som visas överst på sidan med vanliga frågor och svar.

### <a name="review-prices"></a>Granska priser

Om du har åtkomst till Azure via ett Enterprise-avtal (EA) eller ett Microsoft-kundavtal (MCA) kan du visa och ladda ned prisdokumentet för ditt Azure-konto. Prisdokumentet är en Excel-fil som innehåller priser för alla Azure-tjänster. Mer information finns i [Visa och ladda ned dina Azure-priser](../manage/ea-pricing.md).

För andra prenumerationstyper kan du få standardpriser för återförsäljare med [Azure-API:et för återförsäljarpriser](/rest/api/cost-management/retail-prices/azure-retail-prices).

### <a name="review-estimated-costs-in-the-azure-portal"></a>Gå igenom kostnadsuppskattningarna på Azure-portalen

Du kan visa den uppskattade kostnaden per månad när du lägger till en tjänst i Azure-portalen. När du till exempel väljer storleken på din virtuella Windows-dator kan du se den uppskattade månadskostnaden för beräkningstimmarna:

![Exempel: en virtuell A1-dator med Windows som visar den uppskattade månadskostnaden](./media/plan-manage-costs/vm-size-cost.png)

De angivna priserna är endast exempel. De är inte avsedda att representera faktiska kostnader.

## <a name="monitor-costs-when-using-azure-services"></a>Övervaka kostnader när du använder Azure-tjänster
Du kan övervaka kostnader med följande verktyg:

- Budget-och kostnadsaviseringar
- Kostnadsanalys

### <a name="track-costs-with-budgets-and-cost-alerts"></a>Spåra kostnader med budgetar och kostnadsaviseringar

Skapa [budgetar](../costs/tutorial-acm-create-budgets.md) för att hantera kostnader och skapa [aviseringar](../costs/cost-mgt-alerts-monitor-usage-spending.md) som automatiskt meddelar mottagarna om kostnadsavvikelser och överförbrukning.

### <a name="explore-and-analyze-costs-with-cost-analysis"></a><a name="costs"></a> Utforska och analysera kostnader med kostnadsanalys

När dina Azure-tjänster är igång bör du regelbundet kontrollera kostnaderna så att du sparar dina utgifter för Azure. Du kan använda kostnadsanalys till att ta reda på var kostnaderna för din Azure-användning kommer ifrån.

Öppna [sidan Kostnadshantering och fakturering i Azure-portalen](https://portal.azure.com/#blade/Microsoft_Azure_Billing/ModernBillingMenuBlade).

Klicka på **Kostnadsanalys** till vänster på skärmen så ser du aktuella kostnader uppdelade i olika kategorier som tjänst, plats och prenumeration. När du har lagt till en tjänst eller gör ett köp kan det ta upp till 24 timmar innan data visas. Som standard visas kostnaderna för den aktuella omfattningen i kostnadsanalysen. I skärmbilden nedan ser du till exempel kostnader för Contosos faktureringskonto. Använd reglaget Omfång om du vill växla till ett annat omfång i kostnadsanalysen. Mer information om omfång finns i [Förstå och arbeta med omfång](../costs/understand-work-scopes.md#scopes)

![Skärmbild av kostnadsanalysvyn på Azure-portalen](./media/plan-manage-costs/cost-analysis.png)

Du kan filtrera efter olika egenskaper som etiketter, resurstyper och tidsintervall. Klicka på **Lägg till filter** för att lägga till filtret för en egenskap och välja vilka värden du vill filtrera. Välj **Exportera** för att exportera vyn till en fil med kommaavgränsade värden (.csv).

Du kan också klicka på etiketterna i diagrammet om du vill se historiken för dagliga utgifter för etiketten. I exemplet i skärmbilden nedan visas dagliga kostnader för att köra virtuella datorer när en virtuell dator väljs.

:::image type="content" source="./media/plan-manage-costs/cost-history.png" alt-text="Skärmbild av utgiftshistorikvyn på Azure-portalen" lightbox="./media/plan-manage-costs/cost-history.png" :::

## <a name="optimize-and-reduce-costs"></a>Optimera och minska kostnaderna

Mer information om kostnadshanteringsprinciperna finns i [Optimera molninvesteringen med Azure Cost Management](../costs/cost-mgt-best-practices.md).

På Azure-portalen kan du även optimera och minska kostnaderna för Azure med automatisk avstängning av virtuella datorer och Advisor-rekommendationer.

### <a name="consider-cost-cutting-features-like-auto-shutdown-for-vms"></a>Överväg att använda kostnadsbesparingsfunktioner, t.ex. automatisk avstängning för virtuella datorer

Beroende på ditt scenario kan du konfigurera automatisk avstängning för dina virtuella datorerna på Azure-portalen. Mer information finns i [Automatisk avstängning för virtuella datorer med Azure Resource Manager](https://azure.microsoft.com/blog/announcing-auto-shutdown-for-vms-using-azure-resource-manager/).

![Skärmbild av alternativet för automatisk avstängning på portalen](./media/plan-manage-costs/auto-shutdown.png)

Automatisk avstängning är inte detsamma som när du stänger av den virtuella datorn med energialternativ. Automatisk avstängning stoppar och frigör de virtuella datorerna så att ytterligare användningskostnader stoppas. Mer information finns i avsnittet med vanliga frågor och svar om priser för [virtuella Linux-datorer](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) och [virtuella Windows-datorer](https://azure.microsoft.com/pricing/details/virtual-machines/windows/) i olika VM-tillstånd.

Information om kostnadsbesparingsfunktioner för utvecklings- och testmiljöer finns i avsnittet om [Azure DevTest Labs](https://azure.microsoft.com/services/devtest-lab/).

### <a name="turn-on-and-review-azure-advisor-recommendations"></a>Aktivera och granska Azure Advisor-rekommendationer

[Azure Advisor](../../advisor/advisor-overview.md) hjälper dig att minska kostnaderna genom att identifiera resurser med låg användning. Sök efter **Advisor** i Azure-portalen:

![Skärmbild av knappen Azure Advisor på Azure-portalen](./media/plan-manage-costs/advisor-button.png)

Välj **Kostnad** till vänster. Du ser rekommendationer som kan åtgärdas på fliken **Kostnad**:

![Skärmbild av exempel på Advisor-kostnadsrekommendation](./media/plan-manage-costs/advisor-action.png)

De angivna priserna är endast exempel. De är inte avsedda att representera faktiska kostnader.

Gå igenom självstudien [Optimera kostnader från rekommendationer](../costs/tutorial-acm-opt-recommendations.md) för en guidad självstudie om Advisor-rekommendationer för kostnadsbesparing.

## <a name="integrate-with-billing-and-consumption-apis"></a>Integrera med API:er för fakturering och förbrukning

Du kan använda Azure-API:erna för [fakturering](/rest/api/billing/) och [förbrukning](/rest/api/consumption/) till att hämta fakturerings- och kostnadsdata programmatiskt. Använd RateCard-API:et och användnings-API:et tillsammans för att visa din fakturerade användning. Mer information finns i [Få insikter om din resursförbrukning i Microsoft Azure](../manage/consumption-api-overview.md#usage-details-api).

## <a name="additional-resources-and-special-cases"></a><a name="other-offers"></a> Ytterligare resurser och särskilda fall

### <a name="ea-csp-and-sponsorship-customers"></a>EA- och CSP-kunder och kunder med sponsring
Prata med din kontoansvarige eller Azure-partner för att komma igång.

| Erbjudande | Resurser |
|-------------------------------|-----------------------------------------------------------------------------------|
| Enterprise-avtal (EA) | [EA-portal](https://ea.azure.com/), [hjälpdokument](https://ea.azure.com/helpdocs) och [Power BI-rapport](https://powerbi.microsoft.com/documentation/powerbi-content-pack-azure-enterprise/) |
| Leverantör av molnlösningar (CSP) | Prata med din leverantör |
| Azure-sponsring | [Sponsringsportalen](https://www.microsoftazuresponsorships.com/) |

Om du hanterar IT för en stor organisation rekommenderar vi att du läser [Azure Enterprise-kodskelett](/azure/architecture/cloud-adoption-guide/subscription-governance) och [faktabladet om företags-IT](https://download.microsoft.com/download/F/F/F/FFF60E6C-DBA1-4214-BEFD-3130C340B138/Azure_Onboarding_Guide_for_IT_Organizations_EN_US.pdf) (nedladdning av PDF-fil, endast på engelska).

### <a name="enterprise-agreement-cost-views-in-the-azure-portal"></a><a name="EA"></a> Kostnadsvyer för Enterprise-avtal på Azure-portalen

Enterprise-kostnadsvyer finns för närvarande som en offentlig förhandsversion. Saker att tänka på:

- Prenumerationskostnader baseras på användning och inkluderar inte förbetalda belopp, överförbrukning, inkluderade antal, justeringar eller moms. Faktiska avgifter beräknas på registreringsnivån.
- Beloppen som visas på Azure-portalen kan skilja sig från de som visas på Enterprise-portalen. Det kan ta några minuter innan ändringarna på Enterprise-portalen visas på Azure-portalen.
- Om du inte ser några kostnader kan det bero på något av följande:
    - Du har inte behörighet på prenumerationsnivå. Om du vill visa vyer för företagskostnader måste du ha rollen Faktureringsläsare, Läsare, Deltagare eller Ägare på prenumerationsnivå.
    - Du är kontoägare och registreringsadministratören har inaktiverat inställningen Visa debiteringar för kontoägare.  Kontakta registreringsadministratören för att få åtkomst till kostnaderna.
    - Du är avdelningsadministratör och registreringsadministratören har inaktiverat inställningen **Visa debiteringar** för avdelningsadministratörer.  Kontakta registreringsadministratören för att få åtkomst.
    - Du har köpt Azure via en kanalpartner och partnern publicerade ingen prisinformation.  
- Om du uppdaterar inställningar för kostnader och åtkomst i Enterprise-portalen kan det ta några minuter innan du ser ändringarna i Azure-portalen.
- Utgiftsgräns och fakturahjälp gäller inte för EA-prenumerationer.

### <a name="check-your-subscription-and-access"></a>Kontrollera din prenumeration och åtkomst

För att visa kostnader behöver du åtkomst på konto- eller prenumerationsnivå för kostnads- eller faktureringsinformationen. Åtkomsten varierar beroende på typen av faktureringskonto. Du kan läsa mer om faktureringskonton och se vilken typ av faktureringskonto du har i [Visa faktureringskonton i Azure-portalen](../manage/view-all-accounts.md).

Om du har åtkomst till Azure via ett MOSP-faktureringskonto (Microsoft Online Service Program) kan du läsa [Hantera åtkomst till faktureringsinformation för Azure ](../manage/manage-billing-access.md).

Om du har åtkomst till Azure via ett EA-faktureringskonto (EA) kan du läsa [Förstå administrativa roller för Azure Enterprise-avtal i Azure](../manage/understand-ea-roles.md).

Om du har åtkomst till Azure via ett MCA-faktureringskonto (Microsoft-kundavtal) kan du läsa [Förstå administrativa roller för Microsoft-kundavtal i Azure](../manage/understand-mca-roles.md).

### <a name="request-a-service-level-agreement-credit-for-a-service-incident"></a>Begära en SLA-kredit för en tjänstincident

Serviceavtalet beskriver Microsofts åtaganden vad gäller drifttid och anslutning. En tjänstincident, eller *avbrott*, rapporteras när det har uppstått problem med Azure-tjänster som påverkar driften eller anslutningen till tjänsten i fråga. Om vi inte lyckas upprätthålla tjänstnivåerna för respektive tjänst enligt relevant SLA kan du ha rätt till en kredit för en del av dina månatliga tjänstavgifter.

Så här begär du en kredit:

Logga in på [Azure-portalen](https://portal.azure.com/). Om du har flera konton kontrollerar du att du använder det som påverkades av avbrottet i Azure. Skapa sedan en ny supportbegäran.

Under **Typ av problem** väljer du **Fakturering**. Under **Typ av problem** väljer du därefter **Begäran om återbetalning**.

Förklara att du ber om en SLA-kredit, ange datum/tid/tidszon samt de berörda tjänsterna (virtuella datorer, webbplatser osv.)

Verifiera slutligen dina kontaktuppgifter och välj **Skapa** för att skicka in begäran.

För vissa tjänster måste särskilda krav vara uppfyllda för att serviceavtalet ska gälla. Till exempel måste virtuella datorer ha två eller fler distribuerade instanser i samma tillgänglighetsuppsättning.

Mer information finns i avsnittet [Servicenivåavtal](https://azure.microsoft.com/support/legal/sla/) och [Sammanfattning av serviceavtal för Azure-tjänster](https://azure.microsoft.com/support/legal/sla/summary/).

## <a name="next-steps"></a>Nästa steg
- Lär dig mer om hur du använder [utgiftsgränser](../manage/spending-limit.md) för att förhindra överförbrukning.
- Börja [analysera dina Azure-kostnader](../costs/quick-acm-cost-analysis.md).