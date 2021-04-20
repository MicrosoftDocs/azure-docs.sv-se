---
title: Granska din Azure Enterprise-avtalsfaktura
description: Lär dig att läsa och förstå din användning och fakturering för Azure Enterprise-avtal.
author: bandersmsft
ms.reviewer: amberb
tags: billing
ms.service: cost-management-billing
ms.subservice: enterprise
ms.topic: tutorial
ms.date: 08/20/2020
ms.author: banders
ms.openlocfilehash: 5d945c421dc0a4abed17b3a355339c131dd922df
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/19/2021
ms.locfileid: "107728058"
---
# <a name="understand-your-azure-enterprise-agreement-bill"></a>Förstå fakturan för ditt Azure Enterprise-avtal

Azure-kunder med ett Enterprise-avtal får en faktura när de överskrider organisationens kredit eller använder tjänster som inte omfattas av krediten.

Organisationens kredit omfattar din Azure-förskottsbetalning (kallades tidigare ekonomiskt åtagande). Azure-förskottsbetalning är det belopp som din organisation förskottsbetalade för användningen av Azure-tjänster. Du kan lägga till belopp för Azure-förskottsbetalning till ditt Enterprise-avtal genom att kontakta din Microsoft-kontoansvarige eller återförsäljare.

Den här självstudien gäller endast för Azure-kunder som har ett Azure Enterprise-avtal.

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Granska fakturerade avgifter
> * Granska avgifter för överförbrukning av tjänst
> * Granska Marketplace-faktura

## <a name="prerequisites"></a>Krav

För att kunna granska och verifiera avgifterna på din faktura måste du vara företagsadministratör. Mer information finns i [Förstå administrativa roller för Azure Enterprise-avtal i Azure](../manage/understand-ea-roles.md). Om du inte vet vem som är företagsadministratör för din organisation bör du [kontakta supporten](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

## <a name="review-invoiced-charges-for-most-customers"></a>Granska fakturerade avgifter för de flesta kunder

Det här avsnittet gäller inte för Azure-kunder i Australien, Japan eller Singapore.

Du får en Azure-faktura när något av följande inträffar under din faktureringsperiod:

- **Överförbrukning av tjänst**: Din organisations användningsavgifter överstiger ditt kreditsaldo.
- **Avgifter som debiteras separat**: De tjänster som din organisation använder omfattas inte av krediten. Du faktureras för följande tjänster oavsett ditt kreditsaldo:
    - Canonical
    - Citrix XenApp Essentials
    - Citrix XenDesktop
    - Registrerad användare
    - Openlogic
    - Registrerad användare för Remote Access Rights XenApp Essentials
    - Ubuntu Advantage
    - Visual Studio Enterprise (månatligen)
    - Visual Studio Enterprise (årsvis)
    - Visual Studio Professional (månatligen)
    - Visual Studio Professional (årsvis)
- **Marketplace-avgifter**: Azure Marketplace-köp och -användning omfattas inte av din organisations kredit. Du faktureras därmed för Marketplace-avgifter oavsett ditt kreditsaldo. I Enterprise Portal kan en företagsadministratör aktivera och inaktivera Marketplace-köp.

Din faktura visar användningsavgifterna för Azure och associerade kostnader först, följt av eventuella Marketplace-kostnader. Om du har ett kreditsaldo används det för Azure-användningen och din faktura visar Azure-användningen och Marketplace-användningen utan någon kostnad sist.

Jämför det sammanlagda belopp som visas i Enterprise Portal i **Rapporter** > **Användningssammanfattning** med din Azure-faktura. De belopp som anges i **Användningssammanfattning** inkluderar inte skatt.

Logga in på [Azure EA-portalen](https://ea.azure.com). Välj sedan **Rapporter**. I flikens övre högra hörn växlar du vyn från **M** till **C** och matchar perioden på fakturan.  

![Skärmbild som visar alternativet M + C i Användningssammanfattning.](./media/review-enterprise-agreement-bill/ea-portal-usage-sumary-cm-option.png)

Den sammanlagda summan för **Total användning** och **Azure Marketplace** bör överensstämma med **Totalt utökat belopp** på din faktura. Om du vill ha mer information om dina avgifter kan du gå till **Ladda ned användning**.  

![Skärmbild som visar fliken Ladda ned användning](./media/review-enterprise-agreement-bill/ea-portal-download-usage.png)

## <a name="review-invoiced-charges-for-other-customers"></a>Granska fakturerade avgifter för andra kunder

Det här avsnittet gäller endast för Azure-kunder i Australien, Japan eller Singapore.

Du får en eller flera Azure-fakturor när något av följande inträffar:

- **Överförbrukning av tjänst**: Din organisations användningsavgifter överstiger ditt kreditsaldo.
- **Avgifter som debiteras separat**: De tjänster som din organisation använder omfattas inte av krediten. Du faktureras för följande tjänster:
    - Canonical
    - Citrix XenApp Essentials
    - Citrix XenDesktop
    - Registrerad användare
    - Openlogic
    - Registrerad användare för Remote Access Rights XenApp Essentials
    - Ubuntu Advantage
    - Visual Studio Enterprise (månatligen)
    - Visual Studio Enterprise (årsvis)
    - Visual Studio Professional (månatligen)
    - Visual Studio Professional (årsvis)
- **Marketplace-avgifter**: Azure Marketplace-köp och -användning omfattas inte av din organisations kredit, och faktureras separat. I Enterprise Portal kan en företagsadministratör aktivera och inaktivera Marketplace-köp.

När du har avgifter som har förfallit för överförbrukning av tjänst och avgifter som fakturas separat under faktureringsperioden får du en faktura. Den innehåller båda typerna av avgifter. Marketplace-avgifter faktureras alltid separat.

## <a name="review-service-overage-charges-for-other-customers"></a>Granska avgifter överförbrukning av tjänst för andra kunder

Det här avsnittet gäller endast om du är i Australien, Japan eller Singapore.

Jämför ditt totala användningsbelopp i Enterprise Portal i **Rapporter** > **Användningssammanfattning** med din faktura för överförbrukning av tjänst. Fakturan för överförbrukning av tjänst omfattar användning som överskrider organisationens kredit och/eller tjänster som inte omfattas av krediten. Belopp i **Användningssammanfattning** inkluderar inte skatt.

Logga in på [Azure EA-portalen](https://ea.azure.com) och välj sedan **Rapporter**. I flikens övre högra hörn växlar du vyn från **M** till **C** och matchar perioden på fakturan.  

![Skärmbild som visar alternativet M + C i Användningssammanfattning.](./media/review-enterprise-agreement-bill/ea-portal-usage-sumary-cm-option.png)

Beloppet för **Total användning** ska överensstämma med **Totalt utökat belopp** på fakturan för överförbrukning av tjänst. Du kan få mer information om dina avgifter genom att gå till **Ladda ned användning** > **Nedladdning av avancerad rapport**. Rapporten innehåller inte skatter eller avgifter för reservationer eller Marketplace-debiteringar.  

![Skärmbild som visar Nedladdning av avancerad rapport på fliken Ladda ned användning.](./media/review-enterprise-agreement-bill/ea-portal-download-usage-advanced.png)

I följande tabell visas de termer och beskrivningar som finns på fakturan och i **Användningssammanfattning** i Enterprise Portal:

|Fakturaterm|Termen Användningssammanfattning|Beskrivning|
|---|---|---|
|Totalt utökat belopp|Total användning|Den totala användningsavgiften före skatt för den specifika perioden innan krediten tillämpas.|
|Åtagandeanvändning|Åtagandeanvändning|Den kredit som tillämpas under den specifika perioden.|
|Total försäljning|Total överförbrukning|Den totala överförbrukningsavgiften som överstiger ditt kreditbelopp. Detta belopp inkluderar inte skatt.|
|Skattebelopp|Inte tillämpligt|Skatt som gäller för det totala försäljningsbeloppet för den specifika perioden.|
|Totalt belopp|Inte tillämpligt|Det belopp som ska betalas för fakturan efter det att krediten har tillämpats och skatten lagts till.|

### <a name="review-marketplace-invoice"></a>Granska Marketplace-faktura

Det här avsnittet gäller endast om du är i Australien, Japan eller Singapore.

Jämför din totala Azure Marketplace-summa i **Rapporter** > **Användningssammanfattning** i Enterprise-portalen med din Marketplace-faktura. Marketplace-fakturan avser enbart Azure Marketplace-köp och -användning. Beloppen på **förbrukningsöversikten** innehåller redan en skatt som fastställs av utgivaren.

Logga in på [Enterprise-portalen](https://ea.azure.com) och välj sedan **Rapporter**. I flikens övre högra hörn växlar du vyn från **M** till **C** och matchar perioden på fakturan.  

![Skärmbild som visar alternativet M + C i Användningssammanfattning.](./media/review-enterprise-agreement-bill/ea-portal-usage-sumary-cm-option.png)  

Det totala **Azure Marketplace**-beloppet ska överensstämma med **Total försäljning** på din Marketplace-faktura. Du kan få mer information om dina användningsbaserade avgifter genom att gå till **Ladda ned användning**. Under **Marketplace-avgifter** väljer du **Ladda ned**. Marketplace-priset innehåller en skatt som fastställs av utgivaren. Kunder får ingen separat faktura från utgivaren för att samla in skatt på transaktionen.

![Skärmbild som visar nedladdningsalternativet under Marketplace-avgifter.](./media/review-enterprise-agreement-bill/ea-portal-download-usage-marketplace.png)

## <a name="view-price-sheet-information"></a>Visa information om prisdokument

Företagsadministratörer kan visa den prislista som är associerad med deras registrering för Azure-tjänster.

Så här visar du aktuellt prisdokument:

1. I Azure Enterprise-portalen väljer du **Rapporter** och sedan **Prisdokument**.
2. Visa prisdokumentet eller välj **Ladda ned**.

![Exempel som visar information om prisdokument](./media/review-enterprise-agreement-bill/ea-create-view-price-sheet-information.png)

Så här laddar du ned en historisk prislista:

1. I Azure Enterprise-portalen väljer du **Rapporter** och sedan **Ladda ned användning**.
2. Ladda ned prisdokumentet.

![Exempel som visar var du laddar ned ett äldre pris dokument](./media/review-enterprise-agreement-bill/create-ea-view-price-sheet-download-historical-price-list.png)

Några orsaker till prisskillnader:

- Prissättningen kan ha ändrats mellan den tidigare registreringen och den nya registreringen. Det kan uppstå prisändringar eftersom priser är avtalsenliga för en specifik registrering från startdatumet till slutdatumet för ett avtal.
- När du byter till en ny registrering ändras priset till det nya avtalet. Prissättningen definieras av prisdokumentet, som kan vara högre i den nya registreringen.
- Om en registrering övergår till en längre period ändras även prissättningen. Priserna ändras till Betala per användning-priser.

## <a name="request-detailed-usage-information"></a>Begära detaljerad användningsinformation

Företagsadministratörer kan visa en sammanfattning av användningsdata, förbrukad Azure-förskottsbetalning samt avgifter som är kopplade till ytterligare användning i Azure Enterprise-portalen. Avgifterna presenteras på sammanfattningsnivå för alla konton och prenumerationer.

Om du vill visa detaljerad användning i specifika konton laddar du ned rapporten med användningsinformation genom att gå till **Rapporter** > **Ladda ned användning**.

> [!NOTE]
> Rapporten med användningsinformation inkluderar inga tillämpliga skatter. Det kan ta upp till åtta timmar från den tidpunkt då användningen började gälla till dess att den återspeglas i rapporten.

För indirekta registreringar behöver din partner aktivera markeringsfunktionen innan du kan se kostnadsrelaterad information.

## <a name="reports"></a>Rapporter

Företagsadministratörer kan visa en sammanfattning av användningsdata, förbrukad Azure-förskottsbetalning samt avgifter som är kopplade till ytterligare användning i Azure Enterprise-portalen. Avgifterna presenteras på sammanfattningsnivå för alla konton och prenumerationer.

### <a name="azure-enterprise-reports"></a>Azure Enterprise-rapporter

- Användningssammanfattning och diagram
- Rapport för tjänstanvändning
- Rapport för saldo och avgifter
- Rapport för användningsinformation
- Rapport för Azure Marketplace-debiteringar
- Prisdokument
- Advanced report download (Nedladdning av avancerad rapport)
- Formatering av CSV-rapport

### <a name="to-view-the-usage-summary-reports-and-graphs"></a>Så här visar du rapporter och diagram för användningssammanfattning:

1. Gå till Azure Enterprise-portalen.
1. Välj **Rapporter** i det vänstra fönstret.
1. Välj fliken **Användningssammanfattning**.
1. Välj åtagandeperiod på menyn för datumintervall längst upp till vänster.
1. Välj en period eller månad i diagrammet om du vill visa mer information.
1. På den här fliken kan du:
   - Visa diagram över användning månad för månad med en analys av utnyttjad användning, tjänstöverförbrukning, separat debiterade avgifter samt Azure Marketplace-debiteringar.
   - Filtrera efter avdelningar, konton och prenumerationer nedanför diagrammet.
   - Växla uppdelning mellan **Avgifter efter tjänster** och **Avgifter efter hierarki**.
   - Visa detaljerad information om Azure-tjänster, separat debiterade avgifter och Azure Marketplace-debiteringar.

## <a name="service-usage-report"></a>Rapport för tjänstanvändning

På sidan med rapporten för tjänstanvändning kan företagsadministratörer visa en sammanfattning av tjänstanvändningsdata. Användningen presenteras på sammanfattningsnivå för alla konton och prenumerationer. Om du vill visa användningen mer i detalj kan du filtrera rapporten efter konton eller prenumerationer.

> [!NOTE]
> Det kan vara en fördröjning på upp till fem dagar från användningsdatumet tills användningen visas i rapporten.

### <a name="to-view-the-report"></a>Så här visar du rapporten:

1. Logga in på Azure Enterprise-portalen.
1. Välj **Rapporter** i det vänstra navigeringsfältet.
1. Välj fliken **Användningssammanfattning**.
1. Välj datumintervall.
1. Välj vilka konton eller prenumerationer du vill visa.
1. Du kan också:
   - Ändra vy mellan **Avgifter efter tjänster** och **Avgifter efter hierarki** för att visa olika uppdelningar.
   - Visa information om tjänstnamn, måttenhet, förbrukade enheter, pris och utökad kostnad.

## <a name="download-csv-reports"></a>Ladda ned CSV-rapporter

På sidan för nedladdning av månadsrapport kan företagsadministratörer ladda ned flera rapporter som CSV-filer. Nedladdningsbara rapporter:

- Rapport för saldo och avgifter
- Rapport för användningsinformation
- Rapport för Azure Marketplace-debiteringar
- Prisdokument

### <a name="to-download-reports"></a>Så här laddar du ned rapporter:

1. Välj **Rapport** i Azure Enterprise-portalen.
1. Välj **Usage Download** (Ladda ned användning) från det översta menyfliksområdet.
1. Välj **Hämta** intill månadens rapport.

### <a name="csv-report-formatting-issues"></a>Formateringsproblem för CSV-rapporter

Kunder som visar CSV-rapporter i Azure Enterprise-portalen i euro kan få formateringsproblem med kommatecken och punkter.

Du kan till exempel visa:

| **ServiceResource** | **ResourceQtyConsumed** | **ResourceRate** | **ExtendedCost** |
| --- | --- | --- | --- |
| Timmar | 24 | 0,0535960591133005 | 12,863,054,187,192,100,000,000 |

Du bör se:

| ServiceResource | ResourceQtyConsumed | ResourceRate | ExtendedCost |
| --- | --- | --- | --- |
| Timmar | 24 | 0,0535960591133005 | 1,2863054187192120000000 |

Det här formateringsproblemet inträffar på grund av standardinställningarna i importfunktionen i Excel. Excel importerar alla fält som ”vanlig” text och antar att tal avgränsas med matematisk standard. Ett exempel: "1,000.00".

Om en europeisk valuta använder en punkt (.) som tusentalsavgränsare och ett kommatecken som decimaltecken (,), till exempel ”1.000,00”, blir formateringen felaktig. Ett exempel: ”1.000,00”. Importresultatet kan variera beroende på dina nationella inställningar.

### <a name="to-import-the-csv-file-without-formatting-issues"></a>Så här importerar du CSV-filen utan formateringsproblem:

1. Gå till **Arkiv** > **Öppna** i Microsoft Excel.
   Guiden för att importera text visas.
1. Välj **Avgränsade** under **Ursprunglig datatyp**.  Standardvärdet är **Fast bredd**.
1. Välj **Nästa**.
1. Under Avgränsare markerar du kryssrutan för **kommatecken**. Avmarkera **Flik** om det är markerat.
1. Välj **Nästa**.
1. Bläddra till kolumnerna **ResourceRate** och **ExtendedCost**.
1. Välj kolumnen **ResourceRate**. Den visas markerad i svart.
1. Under avsnittet **Kolumndataformat** väljer du **Text** i stället för **Allmänt**. Kolumnrubriken ändras från **Allmänt** till **Text.**
1. Upprepa steg 8 och 9 för kolumnen **Extended Cost** och välj sedan **Slutför**.

> [!TIP]
> Om du har kopplat Excel så att CSV-filer öppnas automatiskt måste du använda funktionen **Öppna** i Excel i stället. Gå till **Arkiv** > **Öppna** i Excel.

## <a name="balance-and-charge-report"></a>Rapport för saldo och avgifter

Rapporten för saldo och avgifter ger en månatlig sammanfattning av saldon, nya inköp, Azure Marketplace-tjänstavgifter, justeringar och överförbrukningsavgifter.

Alla rader i översiktstabellen för Azures tjänståtagande är fortfarande statiska och visar månad för månad. Information om alla köp och justeringar finns i avsnitten för **ny information om köp** och **justeringsinformation**.

### <a name="download-the-balance-and-charge-report"></a>Ladda ned rapporten för saldo och avgifter

1. Logga in på Azure Enterprise-portalen som företagsadministratör.
1. Välj **Rapporter** i det vänstra fönstret.
1. Välj fliken **Report Download** (Ladda ned rapport).
1. Välj lämplig månad under kolumnen **Balance and Charge** (Saldo och avgifter) och klicka för att ladda ned rapporten.

## <a name="usage-detail-report"></a>Rapport för användningsinformation

Rapporten för användningsinformation innehåller en månatlig sammanfattning av förbrukningen av tjänster och kvantiteter för en registrering. Den innehåller information om mätarnamn, mätartyper och förbrukade kvantiteter.

### <a name="download-the-usage-detail-report"></a>Ladda ned rapporten med användningsinformation

1. Logga in på Azure Enterprise-portalen som företagsadministratör.
1. Välj **Rapporter** i det vänstra navigeringsfältet.
1. Välj fliken **Ladda ned användning**.
1. Välj lämplig månad under kolumnen **Usage Detail** (Användningsinformation) och välj nedladdning av rapporten.

## <a name="azure-marketplace-charges-in-azure-enterprise-portal-reports"></a>Azure Marketplace-debiteringar i rapporter på Azure Enterprise-portalen

Det finns två typer av Azure Marketplace-debiteringar:

- **Användningsbaserad:** Produkter mäts i transaktionsenheter.  Till exempel debiteras virtuella datorer per timme och Bing API-sökningar debiteras per antal sökningar.
- **Månadsavgift:** Faktureras månadsvis baserat på användning eller åtkomst.

Mer information om Azure Marketplace-debiteringar finns i [vanliga frågor och svar om Azure Marketplace](https://azure.microsoft.com/marketplace/faq/).

Så här visar du olika debiteringar i Azure Enterprise-portalen:

- **Rapport för användningssammanfattning**: Visar **både** användningsbaserad debitering och månadsavgifter för Azure Marketplace.
- **Rapport för Marketplace-debiteringar**: Visar **endast** användningsbaserade Azure Marketplace-debiteringar.  Engångsavgifter visas inte.

> [!NOTE]
> Azure Marketplace inte är tillgängligt för MPSA-registreringar.

## <a name="advanced-report-download"></a>Nedladdning av avancerad rapport

Om du vill skapa rapporter för specifika datumintervall eller konton kan du välja nedladdning av avancerad rapport. Från och med 30 augusti 2016 är formatet för utdatafilen CSV så att det går att hantera större uppsättningar av poster.

1. I Azure Enterprise-portalen väljer du **Advanced Report Download** (Nedladdning av avancerad rapport).
1. Välj **Appropriate Date Range** (Lämpligt datumintervall).
1. Välj **Appropriate Accounts** (Lämpliga konton).
1. Välj **Request Usage Data** (Begär användningsdata).
1. Välj **uppdateringsknappen** tills rapportens status uppdateras till **Ladda ned**.
1. Ladda ned rapporten.

## <a name="reporting-for-non-enterprise-administrators"></a>Rapportering för icke-företagsadministratörer

Företagsadministratörer kan ge avdelningsadministratörer (DA) och kontoägare (AO) behörighet att visa kostnader för en registrering. Kontoägare med åtkomst kan ladda ned CSV-rapporter som är specifika för deras konto och prenumerationer. De kan också visa informationen visuellt under rapporteringsavsnittet i Azure Enterprise-portalen.

### <a name="to-enable-access"></a>Så här aktiverar du åtkomst:

 1. Logga in på Azure Enterprise-portalen som företagsadministratör.
 1. Välj **Hantera** i det vänstra navigeringsfältet.
 1. Välj fliken **Registrering**.
 1. Under avsnittet med **registreringsinformation** väljer du pennikonen bredvid **Visa avgifter för DA** eller **Visa avgifter för AO**.
 1. Välj **Aktiverad**.
 1. Välj **Spara**.

### <a name="to-view-reports"></a>Så här visar du rapporter:

1. Logga in på Azure Enterprise-portalen som avdelningsadministratör eller kontoägare.
1. Välj **Rapporter** i det vänstra navigeringsfältet.
1. Välj fliken **Användningssammanfattning** för att visa information om kontot och prenumerationer.
1. Välj **Usage Download** (Ladda ned användning) för att visa CSV-rapporter.

Avdelningsadministratörer och kontoägare kan inte visa avgifter när du använder funktionen **Advanced Report Download** (Nedladdning av avancerad rapport). Kostnaderna visas som 0 USD.

Kontoägarnas behörighet att visa avgifter gäller alla kontoägare och alla användare som har behörighet på associerade prenumerationer. Om du är en indirekt kund måste din kanalpartner aktivera kostnadsfunktioner.

## <a name="power-bi-reporting"></a>Power BI-rapportering

Power BI-rapportering är tillgängligt för EA-direktkunder, partner och indirekta kunder som har åtkomst till att via faktureringsinformation.

### <a name="power-bi-pro"></a>Power BI Pro

Power BI Pro är tillgängligt för EA-kunder. Med Power BI Pro kan du skapa och dela rapporter för att effektivt hantera dina kostnadsdata. Och det finns ytterligare funktioner för samarbete och uppdatering av data. Power BI Pro erbjuder högre gränser för datakapacitet och dataströmning.

<!--We plan to add new cost management features for Azure Enterprise customers.

Current Power BI (free) users who use the Microsoft Azure Consumption Insights content pack can get a 60-day free trial of Power BI Pro. After the trial is over, you can continue using Power BI Pro by adding a license.

To sign up for the free Power BI Pro trial:

1. From the gear icon in Power BI, select **Manage personal storage**.
1. Select **Try Pro for free** on the right.

See [Power BI self-service sign up](https://powerbi.microsoft.com/documentation/powerbi-service-self-service-signup-for-power-bi/#power-bi-pro-60-day-trial) for more information on the Power BI Pro free trial.

### Azure EA Power BI Pro trial terms

- **General purpose**: The extended Power BI Pro for the "Microsoft Azure Enterprise" content pack trial offer (the "Offer") is available to existing qualified users during the term of the Offer, to allow them to access certain insights related to their Azure consumption through the use of a specific Power BI content pack.
- **Eligibility**: Users under an Enterprise Agreement (EA) can participate in the Offer if they have a function related to their organization's Azure billing, service, or cost management.
- **Exclusions**:
  - Users already participating in the Extended Power BI Pro trial will continue to qualify under the pre-existing offer and can't enter into the Azure EA Power BI Pro trial offer.
  - Users participating in the Offer can only use Power BI Pro with the Microsoft Azure Enterprise content pack. Any other use of Power BI Pro is prohibited.
  - Term: The Offer began on June 1, 2017 and ended on May 31, 2018.  Acceptance can occur at any time during the 12-month period, though the offer will terminate on May 31, 2018 for all users regardless of when they accepted the Offer.
  -->

### <a name="to-access-microsoft-azure-consumption-insights"></a>Så här kommer du åt Microsoft Azure Consumption Insights:

1. Gå till [Microsoft Azure Consumption Insights](https://app.powerbi.com/getdata/services/azureconsumption?cpcode=MicrosoftAzureConsumptionInsights&amp;getDataForceConnect=true&amp;WT.mc_id=azurebg_email_Trans_33675_1378_Service_Notice_EA_Customer_Power_BI_EA_Content_Pack_Apr26).
1. Välj **Get It Now** (Hämta nu).
1. Ange ett registreringsnummer och antal månader och välj sedan **Nästa**.
1. Ange din API-åtkomstnyckel för att ansluta. Nyckeln för registreringen hittar du i [Enterprise-portalen](https://ea.azure.com/?WT.mc_id=azurebg_email_Trans_33675_1378_Service_Notice_EA_Customer_Power_BI_EA_Content_Pack_Apr26).
1. Välj **Logga in**. Därmed startas importen automatiskt.
1. När den är klar visas en ny instrumentpanel, rapport och modell i navigeringsfönstret. Välj instrumentpanelen för att visa dina importerade data.

> [!TIP]
>
> - Information om hur du skapar API-nyckeln för din registrering finns i hjälpfilen för API-rapporter i [Enterprise-portalen](https://ea.azure.com/?WT.mc_id=azurebg_email_Trans_33675_1378_Service_Notice_EA_Customer_Power_BI_EA_Content_Pack_Apr26).
> - Mer information om hur du ansluter Power BI till din Azure-förbrukning finns i [Microsoft Azure Consumption Insights](/power-bi/desktop-connect-azure-cost-management).

### <a name="to-access-the-legacy-power-bi-ea-content-pack"></a>Så här kommer du åt det tidigare Power BI EA-innehållspaketet:

1. Gå till [Power BI-webbplatsen](https://app.powerbi.com/getdata/services/azure-enterprise).
1. Logga in med ett giltigt arbets- eller skolkonto.

   Arbets- eller skolkontot kan vara detsamma eller ett annat än det som du använder för att nå registreringen via Azure Enterprise-portalen.
1. Välj **Microsoft Azure Enterprise** på instrumentpanelen för tjänster och välj **Anslut**.
1. På sidan **Connect to Azure Enterprise** (Anslut till Azure Enterprise) fyller du i fälten:
    - URL till Azure-miljön: [https://ea.azure.com](https://ea.azure.com/)
    - Antal månader: mellan 1 och 36
    - Registreringsnummer: ditt registreringsnummer
1. Välj **Nästa**.
1. I rutan **Autentiseringsnyckel** anger du API-nyckeln.

    Du kan hämta API-nyckeln i Azure EA-portalen under fliken **Download Usage** (Ladda ned användning). Välj **API Access Key** (API-åtkomstnyckel) och klistra sedan in nyckeln i rutan **Account Key** (Kontonyckel).
1. Det tar cirka 5 till 30 minuter att läsa in data i Power BI beroende på datamängdernas storlek.

### <a name="glossary-of-processes-for-calculating-the-resource-rate"></a>Ordlista för processer för beräkning av resurspris

- **Totalt antal RAW-enheter:** Använd kvantitet i den detaljerade användningsrapporten.
- **MOCP-resurs per enhet:** Upstream Usage System anger användning för varje tjänst i olika enheter. (Förinställning)
- **Förbrukning per enhet:** Azure Enterprise-måttenhet. (Förinställning)
- **Pris:** Enhetspris från Azure Enterprise-portalen.
- **Total kostnad:** Utökad kostnad från den detaljerade användningsrapporten eller utnyttjat åtagande plus överförbrukning från Azure Enterprise-portalen.

### <a name="charges-calculations"></a>Avgiftsberäkningar

- **Konvertering till förbrukade MOCP-resurser** = `ROUND(Total RAW Units * MOCP Resource Per Unit,4)`
- **Konvertering till förbrukade enheter** = `Consumed MOCP Resources / Consumption per Unit`
- **Beräkning av total kostnad** = `Consumed Units * Price`

### <a name="logic-in-the-usage-calculation-logic"></a>Logiken för beräkning av användning

**Resurspris** = `Total Cost /(Total RAW Units / MOCP Resource Per Unit)`

Resurspriset beräknas utifrån dina avgifter. Det kanske inte stämmer överens med det faktiska enhetspriset i prisdokumentet.

I den nedladdningsbara rapporten med användningsdata kan du se rådata för resursanvändning med upp till sex decimaler. Dessa data används för beräkning av överförbrukningsavgifter. Men användningsdata som visas i Azure Enterprise-portalen avrundas till fyra decimaler för åtagandeenheter och trunkeras till noll decimaler för överförbrukningsenheter. I Azure Enterprise-portalen debiterar vi endast för fullständiga enheter för all användning som debiteras som överförbrukning. Skillnaden kan vara stor mellan enhetspriset och resurspriset för den användning som debiteras som överförbrukning eller vid blandade månader.

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
> * Granska fakturerade avgifter
> * Granska avgifter för överförbrukning av tjänst
> * Granska Marketplace-faktura

Fortsätt till nästa artikel för att lära dig mer om hur du använder Azure EA-portalen.

> [!div class="nextstepaction"]
> [Komma igång med Azure EA-portalen](../manage/ea-portal-get-started.md)