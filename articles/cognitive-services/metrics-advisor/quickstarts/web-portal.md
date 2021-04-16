---
title: 'Snabbstart: Metrics Advisor webbportalen'
titleSuffix: Azure Cognitive Services
description: Lär dig hur du börjar använda Metrics Advisor webbportalen.
services: cognitive-services
author: mrbullwinkle
ms.author: mbullwin
manager: nitinme
ms.date: 09/30/2020
ms.topic: quickstart
ms.service: cognitive-services
ms.subservice: metrics-advisor
ms.custom:
- mode-portal
ms.openlocfilehash: aaeaf92df140019db32c326199c9614fd8f5ae37
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2021
ms.locfileid: "107531804"
---
# <a name="quickstart-monitor-your-first-metric-using-the-web-portal"></a>Snabbstart: Övervaka ditt första mått med hjälp av webbportalen

När du etablerar en Metrics Advisor-instans kan du använda API:erna och den webbaserade arbetsytan för att arbeta med tjänsten. Den webbaserade arbetsytan kan användas som ett enkelt sätt att snabbt komma igång med tjänsten. Det ger också ett visuellt sätt att konfigurera inställningar, anpassa din modell och utföra rotorsaksanalys. 

* Publicera dina måttdata
* Visa dina mått och visualiseringar
* Finjustera identifieringskonfigurationer
* Utforska diagnostiska insikter
* Skapa och prenumerera på avvikelseaviseringar

## <a name="prerequisites"></a>Förutsättningar

* Azure-prenumeration [– Skapa en utan kostnad](https://azure.microsoft.com/free/cognitive-services)
* När du har din Azure-prenumeration skapar du en Metrics Advisor-resurs för att Metrics Advisor resurs i Azure Portal för att <a href="https://go.microsoft.com/fwlink/?linkid=2142156"  title=" "  target="_blank"> distribuera din </a> Metrics Advisor instans.  

    
> [!TIP]
> * Det kan ta 10 till 30 minuter Metrics Advisor din resurs ska distribueras. Klicka **på Gå till resurs** när den har distribuerats.
> * Om du vill använda -REST API för att interagera med tjänsten behöver du nyckeln och slutpunkten från den resurs som du skapar. Du hittar dem på fliken  **Nycklar och slutpunkter** i den skapade resursen.

I det här dokumentet används SQL Database som exempel för att skapa din första övervakare.

## <a name="sign-in-to-your-workspace"></a>Logga in på din arbetsyta

När resursen har skapats loggar du in på [Metrics Advisor portalen.](https://go.microsoft.com/fwlink/?linkid=2143774) Välj din arbetsyta för att börja övervaka dina mått. 
 
För närvarande kan du skapa Metrics Advisor resurs i varje tillgänglig region. Du kan växla arbetsytor i Metrics Advisor portal när som helst.


## <a name="onboard-time-series-data"></a>Publicera tidsseriedata

Metrics Advisor tillhandahåller anslutningsappar för olika datakällor, till exempel SQL Database, Azure Data Explorer och Azure Table Storage. Stegen för att ansluta data är liknande för olika anslutningsappar, men vissa konfigurationsparametrar kan variera. Se [ansluta dina data från olika källor för](../data-feeds-from-different-sources.md) de parametrar som krävs för specifika datakällor.

Den här snabbstarten använder SQL Database som exempel. Du kan också mata in dina egna data genom att följa samma steg.

Kom igång genom att logga in Metrics Advisor arbetsytan med ditt Active Directory-konto. På landningssidan väljer du din **katalog,** prenumeration **och arbetsyta** **som** precis har skapats och klickar sedan på **Kom igång.** När arbetsbelastningens huvudsida har laddats väljer du **Lägg till dataflöde** på den vänstra menyn.

### <a name="data-schema-requirements-and-configuration"></a>Krav och konfiguration för datascheman

[!INCLUDE [data schema requirements](../includes/data-schema-requirements.md)]

### <a name="configure-connection-settings"></a>Konfigurera anslutningsinställningar

> [!TIP]
> Se [hur du lägger till datafeeds](../how-tos/onboard-your-data.md) för information om tillgängliga parametrar.

Lägg till dataflödet genom att ansluta till din tidsseriedatakälla. Börja med att välja följande parametrar:

* **Källtyp:** Den typ av datakälla där dina tidsseriedata lagras.
* **Kornighet:** Intervallet mellan efterföljande datapunkter i dina tidsseriedata, till exempel Varje år, Varje månad, Varje dag. Det lägsta anpassningsintervallet som stöds är 60 sekunder.
* **Mata in data sedan (UTC):** Starttiden för den första tidsstämpeln som ska matas in. 


Ange sedan **anslutningssträngen** med autentiseringsuppgifterna för datakällan och en anpassad **fråga.** Frågan används för att ange vilka data som ska matas in och konverteras till det nödvändiga schemat.

[!INCLUDE [query requirements](../includes/query-requirements.md)]

:::image type="content" source="../media/connection-settings.png" alt-text="Inställningar för anslutning" lightbox="../media/connection-settings.png":::


### <a name="verify-the-connection-and-load-the-data-schema"></a>Verifiera anslutningen och läsa in dataschemat

När anslutningssträngen och frågesträngen har skapats väljer du Verifiera och hämta **schema** för att verifiera anslutningen och kör frågan för att hämta dataschemat från datakällan. Normalt tar det några sekunder beroende på anslutningen till datakällan. Om det finns ett fel i det här steget bekräftar du att:

1. Anslutningssträngen och frågan är korrekta.
2. Din Metrics Advisor instans kan ansluta till datakällan om det finns brandväggsinställningar.

### <a name="schema-configuration"></a>Schemakonfiguration

När dataschemat har lästs in och visas som nedan väljer du lämpliga fält.


|Urval  |Beskrivning  |Kommentarer  |
|---------|---------|---------|
|**Timestamp**     | Tidsstämpeln för en datapunkt. Om detta utelämnas Metrics Advisor du tidsstämpeln när datapunkten matas in i stället. För varje dataflöde kan du ange högst en kolumn som tidsstämpel.        | Valfritt. Bör anges med högst en kolumn.       |
|**Mått**     |  De numeriska värdena i dataflödet. För varje dataflöde kan du ange flera mått, men minst en kolumn bör väljas som mått.        | Bör anges med minst en kolumn.        |
|**Dimension**     | Kategoriska värden. En kombination av olika värden identifierar en viss tidsserie med en dimension, till exempel land, språk, klientorganisation. Du kan välja ingen eller godtyckligt antal kolumner som dimensioner. Obs! Om du väljer en kolumn som inte är en sträng som dimension bör du vara försiktig med dimensionsexplosion. | Valfritt.        |
|**Ignorera**     | Ignorera den markerade kolumnen.        |         |


:::image type="content" source="../media/schema-configuration.png" alt-text="Schemakonfiguration" lightbox="../media/schema-configuration.png":::

### <a name="automatic-roll-up-settings"></a>Inställningar för automatisk sammanslagning

> [!IMPORTANT]
> Om du vill aktivera **rotorsaksanalys** och andra diagnostikfunktioner måste "automatisk konfiguration av sammanslagning" konfigureras. När funktionen är aktiverad går det inte att ändra inställningarna för automatisk sammanslagning.

Metrics Advisor kan automatiskt utföra aggregering (SUM/MAX/MIN...) på varje dimension under inmatningen och skapar sedan en hierarki som ska användas i rotfallsanalys och andra diagnostiska funktioner. Mer [information finns i Inställningar för](../how-tos/onboard-your-data.md#automatic-roll-up-settings) automatisk sammanslagning.

Ge datafeeden ett anpassat namn som visas på din arbetsyta. Klicka på **Skicka**. 

## <a name="tune-detection-configuration"></a>Finjustera identifieringskonfigurationen

När dataflödet har lagts till Metrics Advisor att försöka mata in måttdata från det angivna startdatumet. Det tar lite tid för data att matas in helt och du  kan visa inmatningsstatusen genom att klicka på inmatningsförloppet överst på dataflödessidan. Om data matas in Metrics Advisor att tillämpa identifiering och fortsätta att övervaka källan för nya data.

När identifieringen tillämpas klickar du på något av de mått som visas i dataflödet för att hitta **sidan Måttinformation** för att: 
- Visa visualiseringar av alla tidsseriesegment under det här måttet
- Uppdatera identifieringskonfigurationen för att uppfylla förväntade resultat
- Konfigurera meddelande för identifierade avvikelser

:::image type="content" source="../media/metric-details.png" alt-text="Måttinformation" lightbox="../media/metric-details.png":::

## <a name="view-the-diagnostic-insights"></a>Visa diagnostikinsikter

När du har justerat identifieringskonfigurationen bör avvikelser som hittas återspegla faktiska avvikelser i dina data. Metrics Advisor på flerdimensionella mått, till exempel avvikelseklustring, incidentkorrelation och rotorsaksanalys. Använd dessa funktioner för att analysera och diagnostisera incidenter i dina data.

Om du vill visa diagnostikinsikter klickar du på de röda punkterna i tidsserievisualiseringar som representerar identifierade avvikelser. Ett fönster visas med en länk till sidan incidentanalys. 

:::image type="content" source="../media/incident-link.png" alt-text="Incidentlänk" lightbox="../media/incident-link.png":::

När du har klickat på länken pivoteras du till sidan för incidentanalys som analyserar motsvarande avvikelse med en mängd diagnostikinsikter. Längst upp finns det statistik om incidenten, till exempel allvarlighetsgrad,  berörda avvikelser **och** starttid och **sluttid.** 

Därefter ser du avvikelsen i överlagringen för incidenten och automatiserade råd om rotorsaken. Denna automatiserade rotorsaksråd genereras genom analys av incidentträdet för alla relaterade avvikelser, inklusive: avvikelse, distribution och bidrag till de överordnade avvikelserna. 

:::image type="content" source="../media/incident-diagnostic.png" alt-text="Incidentdiagnostik" lightbox="../media/incident-diagnostic.png":::

Utifrån dessa kan du redan få en enkel bild av vad som händer och effekten av incidenten samt den mest potentiella rotorsaken. Så att omedelbara åtgärder kan vidtas för att lösa incidenter så snart som möjligt. 

Men du kan också pivotera över fler diagnostikinsikter med hjälp av ytterligare funktioner för att öka detaljgranska avvikelser efter dimension, visa liknande avvikelser och göra jämförelser mellan mått. Mer information finns i [Så här diagnostiserar du en incident](../how-tos/diagnose-incident.md). 

## <a name="get-notified-when-new-anomalies-are-found"></a>Få ett meddelande när nya avvikelser hittas

Om du vill få en avisering när en avvikelse identifieras i dina data kan du skapa en prenumeration för ett eller flera av dina mått. Metrics Advisor använder hookar för att skicka aviseringar. Tre typer av hookar stöds: e-post hook, web hook och Azure DevOps. Vi använder web hook som exempel. 

### <a name="create-a-web-hook"></a>Skapa en web hook

En web hook är startpunkten för att få en avvikelse upptäckt på ett programmatiskt sätt från Metrics Advisor-tjänsten, som anropar ett användaranvändargränssnitt när en avisering utlöses. Mer information om hur du skapar en hook finns i avsnittet Skapa en **hook** i [How-to: Configure alerts](../how-tos/alerts.md#create-a-hook)and get notifications using a hook (Så här konfigurerar du aviseringar och får meddelanden med en hook). 

### <a name="configure-alert-settings"></a>Konfigurera aviseringsinställningar

När du har skapat en hook avgör en aviseringsinställning hur och vilka aviseringsmeddelanden som ska skickas. Du kan ange flera aviseringsinställningar för varje mått. två viktiga inställningar är **Avisering som** anger vilka avvikelser som ska inkluderas och **Filtrera** avvikelsealternativ som definierar vilka avvikelser som ska ingå i aviseringen. Mer information **finns i avsnittet Lägga till eller** redigera aviseringsinställningar i [How-to: Configure alerts and get notifications using a hook](../how-tos/alerts.md#add-or-edit-alert-settings) (Så här konfigurerar du aviseringar och får meddelanden med en hook).


## <a name="next-steps"></a>Nästa steg

- [Publicera dina datafeeds](../how-tos/onboard-your-data.md)
    - [Hantera datafeeds](../how-tos/manage-data-feeds.md)
    - [Konfigurationer för olika datakällor](../data-feeds-from-different-sources.md)
- [Använda REST API eller klientbibliotek](./rest-api-and-client-library.md)
- [Konfigurera mått och finjustera konfiguration för identifiering](../how-tos/configure-metrics.md)
