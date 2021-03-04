---
title: Galleri för arbets böcker i Azure Security Center
description: Lär dig hur du skapar avancerade, interaktiva rapporter för dina Azure Security Center data med hjälp av det integrerade galleriet för Azure Monitor-arbetsböcker
author: memildin
ms.author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 03/04/2021
ms.openlocfilehash: 198702f619e490e8000e4430aab23a7f6bfb6d85
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/04/2021
ms.locfileid: "102107980"
---
# <a name="create-rich-interactive-reports-of-security-center-data"></a>Skapa avancerade interaktiva rapporter för Security Center data

[Azure Monitor-arbetsböcker](../azure-monitor/visualize/workbooks-overview.md) ger en flexibel arbets yta för data analys och skapandet av rika visuella rapporter i Azure Portal. De gör att du kan trycka på flera data källor i Azure och kombinera dem till enhetliga interaktiva upplevelser.

Arbets böcker ger en omfattande uppsättning funktioner för visualisering av dina Azure-data. Detaljerade exempel på varje visualiserings typ finns i [exemplen och dokumentationen för visualiseringar](../azure-monitor/visualize/workbooks-text-visualizations.md). 

I Azure Security Center kan du komma åt inbyggda rapporter för att spåra din organisations säkerhets position. Du kan också bygga anpassade rapporter för att visa en mängd olika data från Security Center eller andra data källor som stöds.

:::image type="content" source="media/custom-dashboards-azure-workbooks/secure-score-over-time-snip.png" alt-text="Rapport om säkra poäng över tid":::

## <a name="availability"></a>Tillgänglighet

| Aspekt                          | Information                                                                                                                                      |
|---------------------------------|:---------------------------------------------------------------------------------------------------------------------------------------------|
| Versions tillstånd:                  | Förhandsgranskning<br>[!INCLUDE [Legalese](../../includes/security-center-preview-legal-text.md)]                                                       |
| Priset                        | Kostnadsfri                                                                                                                                         |
| Nödvändiga roller och behörigheter: | Om du vill spara arbets böcker måste du ha minst en arbets boks deltagar behörighet för mål resurs gruppen                                      |
| Moln                         | ![Ja](./media/icons/yes-icon.png) Kommersiella moln<br>![Ja](./media/icons/yes-icon.png) National/suverän (US Gov, Kina gov, andra gov) |
|                                 |                                                                                                                                              |

## <a name="workbooks-gallery-in-azure-security-center"></a>Galleri för arbets böcker i Azure Security Center

Med den integrerade funktionen för Azure-arbetsböcker gör Azure Security Center det enkelt att bygga egna anpassade interaktiva rapporter. Security Center innehåller även ett arbets boks galleri med följande rapporter som är klara för din anpassning:

- **Säkra poäng över tid** – spåra dina prenumerations Poäng och ändringar av rekommendationer för dina resurser
- **System uppdateringar** – Visa saknade system uppdateringar av resurser, operativ system, allvarlighets grad med mera
- **Resultat från sårbarhets bedömning** – Visa resultaten av sårbarhets genomsökningar av dina Azure-resurser

:::image type="content" source="media/custom-dashboards-azure-workbooks/workbooks-gallery-security-center.png" alt-text="Galleri med inbyggda arbets böcker i Azure Security Center":::

Välj en av de angivna rapporterna eller skapa en egen.

> [!TIP]
> Använd knappen **Redigera** om du vill anpassa någon av de angivna rapporterna till din belåtenhet. När du är klar med redigeringen väljer du **Spara** och ändringarna sparas i en ny arbets bok.
> 
> :::image type="content" source="media/custom-dashboards-azure-workbooks/editing-supplied-workbooks.png" alt-text="Redigera de angivna arbets böckerna för att anpassa dem efter dina specifika behov":::

### <a name="use-the-secure-score-over-time-report"></a>Använd rapporten "säkra poäng över tid"

I den här rapporten används säkra Poäng data från din Log Analytics-arbetsyta. Dessa data måste exporteras från det kontinuerliga export verktyget enligt beskrivningen i [Konfigurera kontinuerlig export från Security Center sidorna i Azure Portal](continuous-export.md?tabs=azure-portal).

När du ställer in den kontinuerliga exporten anger du export frekvensen till både **strömmande uppdateringar** och **ögonblicks bilder**.

:::image type="content" source="media/custom-dashboards-azure-workbooks/export-frequency-both.png" alt-text="För arbets boken säker poäng över tid måste du välja båda alternativen från export frekvenss inställningarna i konfigurationen för den kontinuerliga exporten":::

> [!NOTE]
> Ögonblicks bilder exporteras varje vecka, så du måste vänta minst en vecka innan den första ögonblicks bilden exporteras innan du kan visa data i den här rapporten.

> [!TIP]
> Om du vill konfigurera kontinuerlig export i organisationen använder du de tillhandahållna Azure Policy ' DeployIfNotExist '-principerna som beskrivs i [Konfigurera kontinuerlig export i skala](continuous-export.md?tabs=azure-policy).

Rapporten över säkra poäng över tid har fem grafer för prenumerationer som rapporteras till de valda arbets ytorna:


|Graph  |Exempel  |
|---------|---------|
|**Poäng trender för den senaste veckan och månaden**<br>Använd det här avsnittet för att övervaka den aktuella poängen och de allmänna trenderna i poängen för dina prenumerationer.|:::image type="content" source="media/custom-dashboards-azure-workbooks/secure-score-over-time-table-1.png" alt-text="Trender för säkra poäng i den inbyggda rapporten":::|
|**Sammanställd Poäng för alla valda prenumerationer**<br>Hovra musen över vilken punkt som helst i trend linjen för att se de sammanställda poängen i valfritt datum i det valda tidsintervallet.|:::image type="content" source="media/custom-dashboards-azure-workbooks/secure-score-over-time-table-2.png" alt-text="Sammanställd Poäng för alla valda prenumerationer":::|
|**Rekommendationer med de mest felfria resurserna**<br>I den här tabellen får du hjälp att prioritering rekommendationerna som har haft de flesta resurser ändrade till fel tillstånd under den valda perioden.|:::image type="content" source="media/custom-dashboards-azure-workbooks/secure-score-over-time-table-3.png" alt-text="Rekommendationer med de mest felfria resurserna":::|
|**Poäng för vissa säkerhets kontroller**<br>Security Centers säkerhets kontroller är logiska grupperingar av rekommendationer. Det här diagrammet visar dig en överblick över alla dina kontroller i veckan.|:::image type="content" source="media/custom-dashboards-azure-workbooks/secure-score-over-time-table-4.png" alt-text="Poäng för dina säkerhets kontroller under den valda tids perioden":::|
|**Resurser ändras**<br>Rekommendationer med de flesta resurser som har ändrat tillstånd (felfri, ej felfri eller inte tillämpligt) under den valda perioden visas här. Välj en rekommendation i listan för att öppna en ny tabell med en lista över de aktuella resurserna.|:::image type="content" source="media/custom-dashboards-azure-workbooks/secure-score-over-time-table-5.png" alt-text="Rekommendationer med de flesta resurser som har ändrat hälso tillstånd":::|

### <a name="use-the-system-updates-report"></a>Använd rapporten system uppdateringar

Den här rapporten baseras på säkerhets rekommendationen. system uppdateringar bör installeras på datorerna.

Rapporten hjälper dig att identifiera datorer med utestående uppdateringar.

Du kan visa situationen för de valda prenumerationerna enligt:

- Listan över resurser med utestående uppdateringar
- Listan över uppdateringar som saknas i dina resurser

:::image type="content" source="media/custom-dashboards-azure-workbooks/system-updates-report.png" alt-text="Security Center rapport om system uppdateringar baserat på säkerhets rekommendationen för saknade uppdateringar":::

### <a name="use-the-vulnerability-assessment-findings-report"></a>Använd rapporten med resultat från sårbarhets bedömning

Security Center inkluderar sårbarhets skannrar för dina datorer, behållare i behållar register och SQL-servrar.

Lär dig mer om att använda dessa skannrar:

- [Sök igenom datorerna med den integrerade VA-skannern](deploy-vulnerability-assessment-vm.md)
- [Sök igenom dina registeravbildningar efter sårbarheter](defender-for-container-registries-usage.md)
- [Sök igenom dina SQL-resurser efter sårbarheter](defender-for-sql-on-machines-vulnerability-assessment.md)

Avgöranden för var och en av dessa skannrar rapporteras i separata rekommendationer:

- Säkerhets risker på dina virtuella datorer bör åtgärdas
- Säkerhets risker i Azure Container Registry avbildningar bör åtgärdas (drivs av Qualys)
- Avgöranden för sårbarhets bedömning på SQL-databaser bör åtgärdas
- Avgöranden för sårbarhets bedömning på dina SQL-servrar på datorer bör åtgärdas

Den här rapporten samlar in dessa resultat och ordnar dem efter allvarlighets grad, resurs typ och kategori.

:::image type="content" source="media/custom-dashboards-azure-workbooks/vulnerability-assessment-findings-report.png" alt-text="Security Center rapport om resultat från sårbarhets bedömning":::


## <a name="import-workbooks-from-other-workbook-galleries"></a>Importera arbets böcker från andra arbets boks gallerier

Om du har skapat arbets böcker i andra Azure-tjänster och vill flytta dem till din Azure Security Center-arbets bok Galleri:

1. Öppna mål arbets boken.

1. Välj **Redigera** i verktygsfältet.

    :::image type="content" source="media/custom-dashboards-azure-workbooks/editing-workbooks.png" alt-text="Redigera en Azure Monitor arbets bok":::

1. I verktygsfältet väljer **</>** du att ange avancerad redigerare.

    :::image type="content" source="media/custom-dashboards-azure-workbooks/editing-workbooks-advanced-editor.png" alt-text="Starta avancerad redigerare för att hämta JSON-koden för Galleri mal len":::

1. Kopiera arbets bokens Galleri mal len JSON.

1. Öppna kalkyl blads galleriet i Security Center och välj **nytt** från meny raden.
1. Välj **</>** för att ange avancerad redigerare.
1. Klistra in i hela Galleri mal len JSON.
1. Välj **Använd**.
1. Välj **Spara som** i verktygsfältet.

    :::image type="content" source="media/custom-dashboards-azure-workbooks/editing-workbooks-save-as.png" alt-text="Spara arbets boken i galleriet i Security Center":::

1. Ange den information som krävs för att spara arbets boken:
   1. Ett namn för arbets boken
   2. Önskad region
   3. Prenumeration, resurs grupp och delning efter behov.

Du hittar din sparade arbets bok i kategorin **senast ändrade arbets böcker** .


## <a name="next-steps"></a>Nästa steg

I den här artikeln beskrivs Security Center sidan integrerade Azure Monitor arbets böcker med inbyggda rapporter och möjligheten att skapa egna interaktiva rapporter.

- Läs mer om [Azure Monitor arbets böcker](../azure-monitor/visualize/workbooks-overview.md)
- De inbyggda rapporterna hämtar data från Security Centers rekommendationer. Lär dig mer om de många säkerhets rekommendationerna i [säkerhets rekommendationer – en referens guide](recommendations-reference.md)