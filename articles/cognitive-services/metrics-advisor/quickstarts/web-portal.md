---
title: 'Snabb start: webb portalen för Metrics Advisor'
titleSuffix: Azure Cognitive Services
description: Lär dig hur du börjar använda Metrics Advisor-webbportalen.
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: metrics-advisor
ms.topic: quickstart
ms.date: 09/30/2020
ms.author: mbullwin
ms.openlocfilehash: f853dca12b6a5855dc0603259ab534fa9933cd5b
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/07/2021
ms.locfileid: "102432306"
---
# <a name="quickstart-monitor-your-first-metric-using-the-web-portal"></a>Snabb start: övervaka ditt första mått med hjälp av webb portalen

När du etablerar en Metric Advisor-instans kan du använda API: erna och den webbaserade arbets ytan för att arbeta med tjänsten. Den webbaserade arbets ytan kan användas som ett enkelt sätt för att snabbt komma igång med tjänsten. Det ger också ett visuellt sätt att konfigurera inställningar, anpassa din modell och utföra rotor Saks analys. 

* Publicera dina mått data
* Visa dina mått och visualiseringar
* Finjustera identifierings konfiguration
* Utforska diagnostiska insikter
* Skapa och prenumerera på avvikelse aviseringar

## <a name="prerequisites"></a>Förutsättningar

* Azure-prenumeration – [skapa en kostnads fritt](https://azure.microsoft.com/free/cognitive-services)
* När du har en Azure-prenumeration kan du <a href="https://go.microsoft.com/fwlink/?linkid=2142156"  title=" skapa en Metrics Advisor-resurs "  target="_blank"> som skapar en Metric Advisor-resurs </a> i Azure Portal för att distribuera din Metric Advisor-instans.  

    
> [!TIP]
> * Det kan vara 10 till 30 minuter för din Metric Advisor-resurs att distribuera. Klicka på **gå till resurs** när den har distribuerats.
> * Om du vill använda REST API för att interagera med tjänsten behöver du nyckeln och slut punkten från den resurs som du skapar. Du hittar dem på fliken  **nycklar och slut punkter** i den skapade resursen.

I det här dokumentet används ett SQL Database som exempel för att skapa din första övervakare.

## <a name="sign-in-to-your-workspace"></a>Logga in på din arbets yta

När du har skapat din resurs loggar du in på [Metrics Advisor-portalen](https://go.microsoft.com/fwlink/?linkid=2143774). Välj din arbets yta för att börja övervaka dina mått. 
 
För närvarande kan du skapa en Metrics Advisor-resurs i varje tillgänglig region. Du kan när som helst byta arbets ytor i mått Advisor-portalen.


## <a name="onboard-time-series-data"></a>Tid serie data för onboarding

Metrics Advisor ger anslutningar för olika data källor, till exempel SQL Database, Azure Datautforskaren och Azure-Table Storage. Stegen för att ansluta data liknar olika anslutningar, även om vissa konfigurations parametrar kan variera. Se [Anslut dina data från olika källor](../data-feeds-from-different-sources.md) för de obligatoriska parametrarna för vissa data källor.

I den här snabb starten används ett SQL Database som exempel. Du kan också mata in egna data genom att följa samma steg.

Kom igång genom att logga in på arbets ytan mått rådgivare med ditt Active Directory-konto. Från landnings sidan väljer du din **katalog**, **prenumeration** och **arbets yta** som du nyss skapade och klickar sedan på **Kom igång**. När huvud sidan för arbets belastnings belastningen har lästs in väljer du **Lägg till datafeed** på den vänstra menyn.

### <a name="data-schema-requirements-and-configuration"></a>Krav och konfiguration för data schema

[!INCLUDE [data schema requirements](../includes/data-schema-requirements.md)]

### <a name="configure-connection-settings"></a>Konfigurera anslutnings inställningar

> [!TIP]
> Se [hur du lägger till datafeeds](../how-tos/onboard-your-data.md) för information om de tillgängliga parametrarna.

Lägg till datafeeden genom att ansluta till data källan för Time-serien. Börja med att välja följande parametrar:

* Typ av **källa**: den typ av data källa där tids serie data lagras.
* **Granularitet**: intervallet mellan efterföljande data punkter i dina tids serie data, till exempel varje månad, varje dag. Det lägsta intervallet för anpassning stöds är 60 sekunder.
* **Inmatnings data sedan (UTC)**: Start tiden för den första tidsstämpeln som ska matas in. 


Ange sedan **anslutnings strängen** med autentiseringsuppgifterna för din data källa och en anpassad **fråga**. Frågan används för att ange vilka data som ska matas in och konverteras till det nödvändiga schemat.

[!INCLUDE [query requirements](../includes/query-requirements.md)]

:::image type="content" source="../media/connection-settings.png" alt-text="Inställningar för anslutning" lightbox="../media/connection-settings.png":::


### <a name="verify-the-connection-and-load-the-data-schema"></a>Kontrol lera anslutningen och Läs in data schema

När anslutnings strängen och frågesträngen har skapats väljer du **Verifiera och hämta schema** för att verifiera anslutningen och köra frågan för att hämta ditt data schema från data källan. Normalt tar det några sekunder beroende på anslutningen till din data källa. Om det finns ett fel i det här steget kontrollerar du att:

1. Anslutnings strängen och frågan är korrekta.
2. Din Metric Advisor-instans kan ansluta till data källan om det finns brand Väggs inställningar.

### <a name="schema-configuration"></a>Schema konfiguration

När data schemat har lästs in och visas som nedan väljer du lämpliga fält.


|Urval  |Beskrivning  |Kommentarer  |
|---------|---------|---------|
|**Tidsstämpel**     | Tidsstämpeln för en data punkt. Om detta utelämnas använder Metric Advisor tidsstämpeln när data punkten matas in i stället. För varje datafeed kan du ange högst en kolumn som tidstämpel.        | Valfritt. Ska anges med högst en kolumn.       |
|**Mått**     |  De numeriska värdena i datafeeden. För varje datafeed kan du ange flera mått, men minst en kolumn ska vara markerad som mått.        | Måste anges med minst en kolumn.        |
|**Dimension**     | Kategoriska-värden. En kombination av olika värden identifierar en viss tids serie för en dimension, till exempel: land, språk, klient organisation. Du kan välja inget eller godtyckligt antal kolumner som dimensioner. Obs: om du väljer en kolumn som inte är en sträng som dimension bör du vara försiktig med dimensions explosion. | Valfritt.        |
|**Ignorera**     | Ignorera den markerade kolumnen.        |         |


:::image type="content" source="../media/schema-configuration.png" alt-text="Schema konfiguration" lightbox="../media/schema-configuration.png":::

### <a name="automatic-roll-up-settings"></a>Automatiska sammanslagnings inställningar

> [!IMPORTANT]
> Om du vill aktivera **rotor Saks analys** och andra diagnostiska funktioner måste du konfigurera automatisk sammanslagning av inställningar. När den är aktive rad går det inte att ändra inställningarna för automatisk sammanslagning.

Mått Advisor kan automatiskt utföra Aggregation (summa/MAX/MIN...) för varje dimension under inmatningen, och bygger sedan en hierarki som kommer att användas i grund ärende analys och andra diagnostiska funktioner. Se [automatiska sammanslagnings inställningar](../how-tos/onboard-your-data.md#automatic-roll-up-settings) för mer information.

Ange ett eget namn för datafeeden, som visas i arbets ytan. Klicka på **Skicka**. 

## <a name="tune-detection-configuration"></a>Finjustera identifierings konfiguration

När datafeeden har lagts till försöker Metric Advisor Hämta mått data från det angivna start datumet. Det tar lite tid för data att matas in och du kan visa inmatnings statusen genom att klicka på inmatnings **förloppet** överst på sidan datafeed. Om data matas in använder Metric Advisor identifieringen och fortsätter att övervaka källan för nya data.

När identifieringen används klickar du på ett av måtten som anges i datafeeden för att hitta **mått informations sidan** för att: 
- Visa visualiseringar av alla Time Series-sektorer under det här måttet
- Uppdatera identifiering av konfiguration för att uppfylla förväntade resultat
- Konfigurera ett meddelande om identifierade avvikelser

:::image type="content" source="../media/metric-details.png" alt-text="Mått information" lightbox="../media/metric-details.png":::

## <a name="view-the-diagnostic-insights"></a>Visa diagnostiska insikter

När du har justerat identifierings konfigurationen bör avvikelser som hittas avspegla faktiska avvikelser i dina data. Metrics Advisor utför analyser på flerdimensionella mått, t. ex. avvikande klustring, incident korrelation och rotor Saks analys. Använd dessa funktioner för att analysera och diagnostisera incidenter i dina data.

Om du vill visa diagnostiska insikter klickar du på de röda punkterna i Time Series-visualiseringar som representerar identifierade avvikelser. Ett fönster visas med en länk till incident analys sidan. 

:::image type="content" source="../media/incident-link.png" alt-text="Incident länk" lightbox="../media/incident-link.png":::

När du har klickat på länken visas sidan incident analys som analyserar vid motsvarande avvikelse, med en massa diagnostiska insikter. Högst upp visas statistik om incidenten, till exempel **allvarlighets grad**, **avvikelser** och påverkad **Start tid** och **slut tid**. 

Härnäst ser du den överordnade avvikelsen för incidenten och automatiserad rotor sakens råd. Den här automatiserade rotor sakens saken genereras genom att analysera incident trädet för alla relaterade avvikelser, inklusive: avvikelse, distribution och bidrag till överordnade avvikelser. 

:::image type="content" source="../media/incident-diagnostic.png" alt-text="Incident diagnostik" lightbox="../media/incident-diagnostic.png":::

Baserat på dessa kan du redan få en enkel översikt över vad som händer och påverkan av incidenten samt den mest potentiella rotor saken. Så att omedelbara åtgärder kan vidtas för att lösa incidenter så snart som möjligt. 

Men du kan också pivotera flera diagnostiska insikter som använder ytterligare funktioner för att öka detalj nivån för avvikelser efter dimension, Visa liknande avvikelser och göra jämförelser över mått. Mer information finns i [så här diagnostiserar du en incident](../how-tos/diagnose-incident.md). 

## <a name="get-notified-when-new-anomalies-are-found"></a>Få ett meddelande när nya avvikelser hittas

Om du vill få aviseringar när en avvikelse identifieras i dina data kan du skapa en prenumeration för ett eller flera av dina mått. Mått Advisor använder hookar för att skicka aviseringar. Tre typer av krokar stöds: e-posthook, Web Hook och Azure DevOps. Vi ska använda Web Hook som exempel. 

### <a name="create-a-web-hook"></a>Skapa en webhook

En webbhook är start punkten för att få en avvikelse som har märkts av ett programmerings sätt från tjänsten Metrics Advisor, som anropar en användardefinierad API när en avisering utlöses. Mer information om hur du skapar en Hook finns i avsnittet **skapa en Hook** i [instruktion: Konfigurera aviseringar och få aviseringar med hjälp av en Hook](../how-tos/alerts.md#create-a-hook). 

### <a name="configure-alert-settings"></a>Konfigurera aviseringsinställningar

När du har skapat en Hook bestämmer en varnings inställning hur och vilka aviserings meddelanden som ska skickas. Du kan ange flera aviserings inställningar för varje mått. två viktiga inställningar är **aviseringar** som anger vilka avvikelser som ska tas med och **filtrera avvikelse alternativ** som definierar vilka avvikelser som ska tas med i aviseringen. Se avsnittet **Lägg till eller redigera aviserings inställningar** i [anvisningar: Konfigurera aviseringar och få aviseringar med hjälp av en Hook](../how-tos/alerts.md#add-or-edit-alert-settings) för mer information.


## <a name="next-steps"></a>Nästa steg

- [Publicera dina data strömmar](../how-tos/onboard-your-data.md)
    - [Hantera datafeeds](../how-tos/manage-data-feeds.md)
    - [Konfigurationer för olika data källor](../data-feeds-from-different-sources.md)
- [Använd REST API-eller klient bibliotek](./rest-api-and-client-library.md)
- [Konfigurera mått och finjustera konfiguration för identifiering](../how-tos/configure-metrics.md)