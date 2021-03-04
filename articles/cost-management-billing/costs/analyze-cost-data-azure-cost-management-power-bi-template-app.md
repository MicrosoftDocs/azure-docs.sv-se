---
title: Analysera Azure-kostnader med Power BI-appen
description: Den här artikeln beskriver hur du installerar och använder Azure Cost Management Power BI-appen.
author: bandersmsft
ms.author: banders
ms.date: 02/19/2021
ms.topic: how-to
ms.service: cost-management-billing
ms.subservice: cost-management
ms.reviewer: benshy
ms.openlocfilehash: b08ff57f964ef7bc3712c930c222a10ed0f89ef4
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/04/2021
ms.locfileid: "102045106"
---
# <a name="analyze-cost-with-the-azure-cost-management-power-bi-app-for-enterprise-agreements-ea"></a>Analysera kostnader med Azure Cost Management Power BI-appen för Enterprise-avtal (EA)

Den här artikeln beskriver hur du installerar och använder Azure Cost Management Power BI-appen. Appen hjälper dig att analysera och hantera dina Azure-kostnader i Power BI. Du kan använda appen till att övervaka kostnader, användningstrender och identifiera alternativ för kostnadsoptimering för att minska dina utgifter.

Azure Cost Management Power BI-appen har för närvarande endast stöd för kunder med en [Enterprise-avtal](https://azure.microsoft.com/pricing/enterprise-agreement/).

Appen begränsar anpassningsbarhet. Om du vill ändra och utöka de standard filter, vyer och visualiseringar som ska anpassas efter dina behov använder du [Azure Cost Management Connector i Power BI Desktop](/power-bi/connect-data/desktop-connect-azure-cost-management) i stället. Med Azure Cost Management Connector kan du koppla ytterligare data från andra källor för att skapa anpassade rapporter för att få en helhets vy över din totala företags kostnad. Anslutningen stöder även Microsofts kund avtal.

> [!NOTE]
> Power BI-mallappar stöder inte hämtning av PBIX-filen.

## <a name="prerequisites"></a>Krav

- En [Power BI Pro-licens](/power-bi/service-self-service-signup-for-power-bi) krävs för installation och användning av appen.
- För att ansluta till data måste du använda ett [företagsadministratörskonto](../manage/understand-ea-roles.md). Rollen företagsadministratör (skrivskyddad) stöds.

## <a name="installation-steps"></a>Installationssteg

Så här installerar du appen:

1. Öppna [Azure Cost Management Power BI-appen](https://aka.ms/costmgmt/ACMApp).
1. På sidan Power BI AppSource väljer du **Hämta nu**.
1. Välj **Fortsätt** för att godkänna användningsvillkoren och sekretesspolicyn.
1. I rutan **Installera den här Power BI-appen** väljer du **Installera**.
1. Om det behövs skapar du en arbetsyta och väljer **Fortsätt**.
1. När installationen är klar visas ett meddelande om att din nya app är klar.
1. Välj den app som du har installerat.
1. På sidan komma igång väljer du **Anslut dina data**.
    :::image type="content" source="./media/analyze-cost-data-azure-cost-management-power-bi-template-app/connect-your-data.png" alt-text="Skärm bild som markerar länken Anslut din data." lightbox="./media/analyze-cost-data-azure-cost-management-power-bi-template-app/connect-your-data.png" :::
1. I dialogrutan som visas anger du ditt EA-registreringsnummer för **BillingProfileIdOrEnrollmentNumber**. Ange hur många månaders data som ska hämtas. Lämna **Omfång**-standardvärdet som **Registreringsnummer** och välj **Nästa**.  
    :::image type="content" source="./media/analyze-cost-data-azure-cost-management-power-bi-template-app/ea-number.png" alt-text="Skärm bild som visar var du anger din E-postregistrerings information." lightbox="./media/analyze-cost-data-azure-cost-management-power-bi-template-app/ea-number.png" :::
1. Nästa dialog ansluter till Azure och hämtar data. *Låt standardvärdena vara konfigurerade* och välj **Logga in och fortsätt**.  
    :::image type="content" source="./media/analyze-cost-data-azure-cost-management-power-bi-template-app/autofit.png" alt-text="Skärm bild som visar dialog rutan Anslut till Azure Cost Management app med standardvärden." lightbox="./media/analyze-cost-data-azure-cost-management-power-bi-template-app/autofit.png" :::
1. I det sista installationssteget görs en anslutning till din EA-registrering och ett [företagsadministratörskonto](../manage/understand-ea-roles.md) krävs. Lämna alla standardvärden. Välj **Logga in och Anslut**.  
    :::image type="content" source="./media/analyze-cost-data-azure-cost-management-power-bi-template-app/ea-auth.png" alt-text="Skärm bild som visar dialog rutan Anslut till Azure Cost Management app med standardvärden för att ansluta till." lightbox="./media/analyze-cost-data-azure-cost-management-power-bi-template-app/ea-auth.png" :::
1. Du uppmanas att autentisera med din EA-registrering. Autentisera med Power BI. När du har autentiserats startar en Power BI data uppdatering.
    > [!NOTE]
    > Det kan ta en stund att slutföra datauppdateringen. Hur lång tid det tar beror på hur många månader som har angetts och hur mycket data som behöver synkroniseras.

När datauppdateringen är klar väljer du appen Azure Cost Management för att visa de rapporter som skapats i förväg.

## <a name="reports-available-with-the-app"></a>Rapporter som är tillgängliga med appen

Följande rapporter är tillgängliga i appen.

**Komma igång** – Innehåller användbara länkar till dokumentation och länkar för att ge feedback.

**Kontoöversikt** – Rapporten visar en månatlig sammanfattning av informationen, inklusive:

- Debiteringar mot kredit
- Nya inköp
- Azure Marketplace-avgifter
- Överförbrukning och totala avgifter

**Användning efter prenumerationer och resursgrupper** – Ger en vy över kostnader över tid och visar diagram över kostnader per prenumeration och resursgrupp.

**Användning efter tjänster** – Ger en vy över tiden för användning efter MeterCategory. Du kan spåra dina användningsdata och öka detaljnivån så att du kan se eventuella avvikelser och få information om topp- och bottenvärden för användningen.

**De 5 främsta användningsdrivkrafterna** – Rapporten visar en filtrerad kostnadssammanfattning med de 5 främsta MeterCategory och motsvarande MeterName.

**Windows Server AHB Usage** (AHB-användning för Windows Server) – Rapporten visar antal virtuella datorer som har Azure Hybrid-förmånen aktiverad. Den visar också antalet kärnor/virtuella processorer som används av de virtuella datorerna.

:::image type="content" source="./media/analyze-cost-data-azure-cost-management-power-bi-template-app/ahb-report-full.png" alt-text="Skärm bild som visar den fullständiga Azure Hybrid Benefits-rapporten." lightbox="./media/analyze-cost-data-azure-cost-management-power-bi-template-app/ahb-report-full.png" :::

Rapporten visar om det finns virtuella Windows-datorer som har Hybrid-förmånen **aktiverad** och _mindre än_ 8 virtuella processorer. Den visar också om Hybrid-förmånen **inte är aktiverad** och det finns 8 _eller fler_ virtuella processorer. Den här informationen hjälper dig att få ut mesta möjliga av Hybrid-förmånen. Du gör störst besparingar om du använder förmånen med dina dyraste virtuella datorer.

:::image type="content" source="./media/analyze-cost-data-azure-cost-management-power-bi-template-app/ahb-report.png" alt-text="Skärm bild som visar mindre än 8 virtuella processorer och virtuella processorer som inte är aktiverade i Azure Hybrid Benefits Report." lightbox="./media/analyze-cost-data-azure-cost-management-power-bi-template-app/ahb-report.png" :::

**RI Chargeback** (Återbetalning för reserverad instans) – rapporten beskriver var och hur mycket av en reserverad instans (RI) som används per region, prenumeration, resursgrupp eller resurs. I rapporten används amorterade användningsdata för att visa vyn.

Du kan tillämpa ett filter på _chargetype_ om du vill visa underanvändningsdata för reserverad instans.

Mer information om amorterade data finns i [Hämta kostnader för och användning av EA-reservationer](../reservations/understand-reserved-instance-usage-ea.md).

**RI Savings** (RI-besparingar) – rapporten visar besparingar som uppkommit genom reservationer för prenumeration, resursgrupp och resursnivå. Den visar:

- Kostnad med reservation
- Uppskattad On Demand-kostnad om reservationen inte tillämpades för användningen
- Kostnadsbesparingar som uppkommit genom reservationen

 I rapporten dras eventuella kostnader för slöseri på grund av underanvända reservationer bort från de totala besparingarna. Slöseriet skulle inte uppstå utan en reservation.

Du kan använda amorterade användningsdata för att bygga på data.

<a name="shared-recommendation"></a>
**VM RI Coverage (shared recommendation)** (VM RI-täckning (delad rekommendation)) – Rapporten delas upp mellan VM-användning på begäran och VM-användning för reserverad instans under den period som valts. Rapporten ger rekommendationer för VM RI-köp i ett delat omfång.

Om du vill använda rapporten väljer du filtret för att öka detaljnivån.

:::image type="content" source="./media/analyze-cost-data-azure-cost-management-power-bi-template-app/ri-drill-down2.png" alt-text="Skärm bild som visar alternativet Välj detalj nivå ned i rapporten VM RI-disponering." lightbox="./media/analyze-cost-data-azure-cost-management-power-bi-template-app/ri-drill-down2.png" :::

Välj den region som du vill analysera. Välj sedan grupp för flexibel instansstorlek och så vidare.

För varje detaljnivå tillämpas följande filter i rapporten:

- Täckningsdata till höger är ett filter som visar hur mycket användning som debiteras med priset på begäran jämfört med hur mycket som täcks av reservationen.
- Rekommendationerna filtreras också.

Rekommendationstabellen innehåller rekommendationer för reservationsköp, utifrån de VM-storlekar som används.

Värdena för _normaliserad storlek_ och _rekommenderat normaliserat antal_ hjälper dig att normalisera köpet till den minsta storleken för en grupp för flexibel instansstorlek. Den här informationen är användbar om du planerar att köpa bara en reservation för alla storlekar i gruppen för flexibel instansstorlek.

:::image type="content" source="./media/analyze-cost-data-azure-cost-management-power-bi-template-app/ri-recommendations.png" alt-text="Skärm bild som visar rapporten för RI-rekommendationer." lightbox="./media/analyze-cost-data-azure-cost-management-power-bi-template-app/ri-recommendations.png" :::

**VM RI Coverage (single recommendation)** (VM RI-täckning (enskild rekommendation)) – Rapporten är uppdelad på VM-användning på begäran och VM-användning för reserverad instans under den tidsperiod som valts. Rapporten ger rekommendationer för VM RI-köp i ett prenumerationsomfång.

Mer information om hur du använder rapporten finns i avsnittet [VM RI-täckning (delad rekommendation)](#shared-recommendation).

**RI purchases** (RI-inköp) – rapporten visar RI-inköp under den angivna perioden.

**Prisdokument** – rapporten innehåller en detaljerad prislista som är specifik för ett faktureringskonto eller en EA-registrering.

## <a name="troubleshoot-problems"></a>Felsöka problem

Följande felsökningsinformation kan hjälpa dig om du har problem med Power BI-appen.

### <a name="error-processing-the-data-in-the-dataset"></a>Fel vid bearbetningen av informationen i datauppsättningen

Du kan få ett felmeddelande som säger:

```
There was an error when processing the data in the dataset.
Data source error: {"error":{"code":"ModelRefresh_ShortMessage_ProcessingError","pbi.error":{"code":"ModelRefresh_ShortMessage_ProcessingError","parameters":{},"details":[{"code":"Message","detail":{"type":1,"value":"We cannot convert the value \"Required Field: 'Enr...\" to type List."}}],"exceptionCulprit":1}}} Table: <TableName>.
```

Ett tabellnamn visas i stället för `<TableName>`.

#### <a name="cause"></a>Orsak

Standardvärdet för **omfång** för `Enrollment Number` ändrades i anslutningen till Cost Management.

#### <a name="solution"></a>Lösning

Återanslut till Cost Management och ange värdet för **omfång** till `Enrollment Number`. Ange inte organisationens registreringsnummer. Skriv i stället `Enrollment Number` exakt som det visas i följande bild.

:::image type="content" source="./media/analyze-cost-data-azure-cost-management-power-bi-template-app/ea-number-troubleshoot.png" alt-text="Skärm bild som visar att standard texten för registrerings nummer inte får ändras." lightbox="./media/analyze-cost-data-azure-cost-management-power-bi-template-app/ea-number-troubleshoot.png" :::

### <a name="budgetamount-error"></a>BudgetAmount-fel

Du kan få ett felmeddelande som säger:

```
Something went wrong
There was an error when processing the data in the dataset.
Please try again later or contact support. If you contact support, please provide these details.
Data source error: The 'budgetAmount' column does not exist in the rowset. Table: Budgets.
```

#### <a name="cause"></a>Orsak

Det här felet orsakas av ett fel i underliggande metadata. Problemet uppstår eftersom det inte finns någon tillgänglig budget i **Cost Management > Budget** i Microsoft Azure-portalen. En felkorrigering håller på att distribueras till Power BI Desktop och Power BI-tjänsten.

#### <a name="solution"></a>Lösning

- Tills felet har åtgärdats kan du kringgå problemet genom att lägga till en testbudget i Microsoft Azure-portalen på faktureringskonto-/EA-registreringsnivån. Testbudgeten avblockerar anslutningen till Power BI. Mer information om hur du skapar en budget finns i [Självstudie: Skapa och hantera Azure-budgetar](tutorial-acm-create-budgets.md).

### <a name="invalid-credentials-for-azureblob-error"></a>Ogiltiga autentiseringsuppgifter för AzureBlob-fel

Du kan få ett felmeddelande som säger:

```
Failed to update data source credentials: The credentials provided for the AzureBlobs source are invalid.
```

#### <a name="cause"></a>Orsak

Det här felet uppstår om du ändrar autentiseringsmetoden för anslutningen till data källan.

#### <a name="solution"></a>Lösning

1. Ansluta till data.
1. När du har angett din EA-registrering och antalet månader bör du se till att du inte ändrar standardvärdet **Anonym** som autentiseringsmetod och **Ingen** som inställning för sekretessnivå.  
  :::image type="content" source="./media/analyze-cost-data-azure-cost-management-power-bi-template-app/autofit-troubleshoot.png" alt-text="Skärmbild som visar dialogrutan Anslut till Azure Cost Management-appen med de angivna värdena Anonym och Inget." lightbox="./media/analyze-cost-data-azure-cost-management-power-bi-template-app/autofit-troubleshoot.png" :::
1. På nästa sida anger du **OAuth2** som autentiseringsmetod och **Ingen** som sekretessnivå. Sedan loggar du in för att autentisera med din registrering. Det här steget startar också en datauppdatering i Power BI.

## <a name="data-reference"></a>Datareferens

Följande information sammanfattar den information som är tillgänglig via appen. Det finns också länkar till API:er som ger detaljerad information om datafält och värden.

| **Tabellreferens** | **Beskrivning** |
| --- | --- |
| **AutoFitComboMeter** | Data som ingår i appen normaliserar RI-rekommendationen och användningen till den minsta storleken i instansfamiljegruppen. |
| [**Saldosammanfattning**](/rest/api/billing/enterprise/billing-enterprise-api-balance-summary#response) | Översikt över saldot för Enterprise-avtal. |
| [**Budgetar**](/rest/api/consumption/budgets/get#definitions) | Budgetinformation som visar de faktiska kostnaderna eller den faktiska användningen jämfört med befintliga budgetmål. |
| [**Prisdokument**](/rest/api/billing/enterprise/billing-enterprise-api-pricesheet#see-also) | Tillämpliga mätarpriser för den angivna faktureringsprofilen eller EA-registreringen. |
| [**RI-avgifter**](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-charges#response) | Avgifter för reserverade instanser under de senaste 24 månaderna. |
| [**RI-rekommendationer (delade)**](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation#response) | Rekommendationerna för köp av reserverade instanser bygger på användningstrenderna för alla dina prenumerationer under de senaste 7 dagarna. |
| [**RI-rekommendationer (enskild)**](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation#response-1) | Rekommendationerna för köp av reserverade instanser bygger på användningstrenderna för din enskilda prenumeration under de senaste 7 dagarna. |
| [**RI-användningsinformation**](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-usage#response) | Förbrukningsinformation för dina befintliga reserverade instanser under den senaste månaden. |
| [**RI-användningssammanfattning**](/rest/api/consumption/reservationssummaries/list) | Daglig Azure-reservationsanvändning i procent. |
| [**Användningsinformation**](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#usage-details-field-definitions) | En uppdelning av förbrukade kvantiteter och beräknade avgifter för den aktuella faktureringsprofilen i EA-registreringen. |
| [**Användningsinformation amorterat**](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#usage-details-field-definitions) | En uppdelning av förbrukade kvantiteter och beräknade amorterade avgifter för den aktuella faktureringsprofilen i EA-registreringen. |

## <a name="next-steps"></a>Nästa steg

Mer information om hur du konfigurerar data, uppdaterar, delar rapporter och gör ytterligare anpassningar i rapporter finns i följande artiklar:

- [Konfigurera schemalagd uppdatering](/power-bi/refresh-scheduled-refresh)
- [Dela Power BI-instrumentpaneler och -rapporter med kollegor och andra](/power-bi/service-share-dashboards)
- [Prenumerera på rapporter och instrumentpaneler i Power BI-tjänsten (lägg till dig själv och andra)](/power-bi/service-report-subscribe)
- [Ladda ned en rapport från Power BI-tjänsten till Power BI Desktop](/power-bi/service-export-to-pbix)
- [Spara en rapport i Power BI-tjänsten och Power BI Desktop](/power-bi/service-report-save)
- [Skapa en rapport i Power BI-tjänsten genom att importera en datamängd](/power-bi/service-report-create-new)