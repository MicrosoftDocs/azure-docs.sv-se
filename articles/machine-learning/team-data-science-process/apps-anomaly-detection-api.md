---
title: Azure Machine Learning avvikelse identifierings-API – team data science process
description: API för avvikelse identifiering är ett exempel som skapats med Microsoft Azure Machine Learning som identifierar avvikelser i Time Series-data med numeriska värden som är jämnt fördelade i tid.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=alokkirpal, previous-ms.author=alok
ms.openlocfilehash: e210c1683d5f14181bc0549e73a892eb91d2e746
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "93305705"
---
# <a name="machine-learning-anomaly-detection-api"></a>API för Machine Learning avvikelse identifiering

> [!NOTE]
> Det här objektet är under underhåll. Vi rekommenderar att du använder [API-tjänsten för avvikelse detektor](https://azure.microsoft.com/services/cognitive-services/anomaly-detector/) som drivs av ett galleri med Machine Learning algoritmer under Azure Cognitive Services för att identifiera avvikelser från affärs-, drift-och IoT-mått.

## <a name="overview"></a>Översikt
[API för avvikelse identifiering](https://gallery.azure.ai/MachineLearningAPI/Anomaly-Detection-2) är ett exempel som skapats med Azure Machine Learning som identifierar avvikelser i Time Series-data med numeriska värden som är jämnt fördelade i tid.

Detta API kan identifiera följande typer av avvikande mönster i tids serie data:

* **Positiva och negativa trender**: om du till exempel övervakar minnes användningen i data behandling kan en uppåtgående trend vara intressant eftersom det kan vara en indikation på en minnes läcka.
* **Ändringar i det dynamiska värde intervallet**: om du till exempel övervakar undantagen som har utlösts av en moln tjänst kan alla ändringar i det dynamiska värde intervallet vara instabilitet i tjänstens hälso tillstånd.
* **Toppar och DIP**: om du till exempel övervakar antalet inloggnings försök i en tjänst eller antalet utcheckningar på en e-handelsplats kan toppar eller dip tyda på onormalt beteende.

Dessa maskin inlärnings detektorer spårar sådana förändringar i värden över tid och rapporterar pågående ändringar i sina värden som avvikande poäng. De kräver inte justering av adhoc-tröskel och deras resultat kan användas för att styra falsk positiv hastighet. API: t för avvikelse identifiering är användbart i flera scenarier som tjänst övervakning genom att spåra KPI: er över tid, användnings övervakning via mått, till exempel antal sökningar, antal klick, prestanda övervakning via räknare som minne, CPU, fil läsningar osv. över tid.

Identifierings erbjudandet för avvikelse identifiering innehåller användbara verktyg för att komma igång.

* [Webb programmet](https://anomalydetection-aml.azurewebsites.net/) hjälper dig att utvärdera och visualisera resultaten av API: er för avvikelse identifiering på dina data.

> [!NOTE]
> Testa **den avvikande Insights-lösning** som drivs av [detta API](https://gallery.azure.ai/MachineLearningAPI/Anomaly-Detection-2)
>
<!-- This Solution is no longer available
> To get this end to end solution deployed to your Azure subscription <a href="https://gallery.cortanaintelligence.com/Solution/Anomaly-Detection-Pre-Configured-Solution-1" target="_blank">**Start here >**</a>
-->

## <a name="api-deployment"></a>API-distribution
Om du vill använda API: et måste du distribuera det till din Azure-prenumeration där den kommer att vara värd för en Azure Machine Learning-webbtjänst.  Du kan göra detta från [Azure AI Gallery](https://gallery.azure.ai/MachineLearningAPI/Anomaly-Detection-2).  Detta distribuerar två Azure Machine Learning Studio (klassiska) webb tjänster (och deras relaterade resurser) till din Azure-prenumeration – en för avvikelse identifiering med säsongs beroende-identifiering och en utan säsongs beroende identifiering.  När distributionen har slutförts kommer du att kunna hantera dina API: er från sidan [Azure Machine Learning Studio (klassiska) webb tjänster](https://services.azureml.net/webservices/) .  Från den här sidan kan du hitta dina slut punkts platser, API-nycklar och exempel kod för att anropa API: et.  Mer detaljerade instruktioner finns [här](../classic/manage-new-webservice.md).

## <a name="scaling-the-api"></a>Skalar API: et
Som standard har din distribution en kostnads fri fakturerings plan för utveckling/testning som innehåller 1 000 transaktioner/månad och 2 beräknings timmar/månad.  Du kan uppgradera till en annan plan efter behov.  Information om prissättningen för olika planer finns [här](https://azure.microsoft.com/pricing/details/machine-learning/) under "Production Web API-prissättning".

## <a name="managing-aml-plans"></a>Hantera AML-planer
Du kan hantera din fakturerings plan [här](https://services.azureml.net/plans/).  Plan namnet kommer att baseras på det resurs grupps namn som du valde när du distribuerade API: et, plus en sträng som är unik för din prenumeration.  Anvisningar om hur du uppgraderar planen finns [här](../classic/manage-new-webservice.md) under avsnittet "hantera fakturerings planer".

## <a name="api-definition"></a>API-definition
Webb tjänsten tillhandahåller ett REST-baserat API över HTTPS som kan användas på olika sätt, till exempel ett webb-eller mobil program, R, python, Excel osv.  Du skickar dina Time Series-data till den här tjänsten via ett REST API samtal och kör en kombination av de tre avvikelse typerna som beskrivs nedan.

## <a name="calling-the-api"></a>Anropar API: et
För att anropa API: et måste du känna till slut punkts platsen och API-nyckeln.  Dessa två krav, tillsammans med exempel kod för att anropa API: n, är tillgängliga på sidan [Azure Machine Learning Studio (klassiska) webb tjänster](https://services.azureml.net/webservices/) .  Navigera till önskat API och klicka sedan på fliken "konsumera" för att hitta dem.  Du kan anropa API: et som ett Swagger-API (det vill säga med URL `format=swagger` -parametern) eller som ett icke-Swagger-API (det vill säga utan `format` URL-parametern).  I exempel koden används Swagger-formatet.  Nedan visas en exempel förfrågan och ett svar i icke-Swagger format.  De här exemplen är till säsongs beroende-slutpunkten.  Den icke-säsongs beroende slut punkten liknar.

### <a name="sample-request-body"></a>Exempel på begär ande text
Begäran innehåller två objekt: `Inputs` och `GlobalParameters` .  I exempel förfrågan nedan skickas vissa parametrar explicit medan andra inte (Rulla nedåt för en fullständig lista över parametrar för varje slut punkt).  Parametrar som inte skickas uttryckligen i begäran kommer att använda standardvärdena som anges nedan.

```json
{
            "Inputs": {
                    "input1": {
                            "ColumnNames": ["Time", "Data"],
                            "Values": [
                                    ["5/30/2010 18:07:00", "1"],
                                    ["5/30/2010 18:08:00", "1.4"],
                                    ["5/30/2010 18:09:00", "1.1"]
                            ]
                    }
            },
    "GlobalParameters": {
        "tspikedetector.sensitivity": "3",
        "zspikedetector.sensitivity": "3",
        "bileveldetector.sensitivity": "3.25",
        "detectors.spikesdips": "Both"
    }
}
```

### <a name="sample-response"></a>Exempelsvar
För att kunna se `ColumnNames` fältet måste du ta med `details=true` som en URL-parameter i din begäran.  I tabellerna nedan visas innebörden bakom vart och ett av dessa fält.

```json
{
    "Results": {
        "output1": {
            "type": "table",
            "value": {
                "Values": [
                    ["5/30/2010 6:07:00 PM", "1", "1", "0", "0", "-0.687952590518378", "0", "-0.687952590518378", "0", "-0.687952590518378", "0"],
                    ["5/30/2010 6:08:00 PM", "1.4", "1.4", "0", "0", "-1.07030497733224", "0", "-0.884548154298423", "0", "-1.07030497733224", "0"],
                    ["5/30/2010 6:09:00 PM", "1.1", "1.1", "0", "0", "-1.30229513613974", "0", "-1.173800281031", "0", "-1.30229513613974", "0"]
                ],
                "ColumnNames": ["Time", "OriginalData", "ProcessedData", "TSpike", "ZSpike", "BiLevelChangeScore", "BiLevelChangeAlert", "PosTrendScore", "PosTrendAlert", "NegTrendScore", "NegTrendAlert"],
                "ColumnTypes": ["DateTime", "Double", "Double", "Double", "Double", "Double", "Int32", "Double", "Int32", "Double", "Int32"]
            }
        }
    }
}
```


## <a name="score-api"></a>Poängsättnings-API
Poäng-API: et används för att köra avvikelse identifiering för tids serie data som inte är säsongs. API: et kör ett antal avvikelse identifieringar för data och returnerar deras avvikelse resultat.
Figuren nedan visar ett exempel på avvikelser som Poäng-API: et kan identifiera. Den här tids serien har två distinkta nivå ändringar och tre toppar. De röda punkterna visar tiden då nivå ändringen identifieras, medan de svarta punkterna visar de identifierade toppar.
![Poängsättnings-API][1]

### <a name="detectors"></a>Detektorer
API: t för avvikelse identifiering stöder identifierare i tre breda kategorier. Information om speciella indataparametrar och utdata för varje detektor finns i följande tabell.

| Detektor kategori | Igenkänning | Beskrivning | Indataparametrar | Utdata |
| --- | --- | --- | --- | --- |
| Insamling av identifieringar |TSpike detektor |Identifiera toppar och DIP utifrån värdena från första och tredje kvartilen |*tspikedetector. känslighet:* tar heltals värde i intervallet 1-10, standard: 3; Högre värden kommer att fånga mer extrema värden, vilket gör det mindre känsligt |TSpike: binära värden – 1 om en insamling/DIP identifieras, 0 |
| Insamling av identifieringar | ZSpike detektor |Identifiera toppar och DIP utifrån hur långt Datapoints är från deras medelvärde |*zspikedetector. känslighet:* ta heltals värde i intervallet 1-10, standard: 3; Högre värden kommer att fånga fler extrema värden vilket gör det mindre känsligt |ZSpike: binära värden – 1 om en insamling/DIP identifieras, 0 |
| Långsam trend detektor |Långsam trend detektor |Identifiera långsam positiv trend enligt inställningen känslighet |*trenddetector. känslighet:* tröskelvärde för detektorernas Poäng (standard: 3,25, 3,25 – 5 är ett rimligt intervall att välja bland. Ju högre mindre känsligt) |tscore: flytande tal som representerar avvikande Poäng i trenden |
| Nivå för ändrings identifiering | Ändrings detektor i dubbelriktad nivå |Identifiera både uppåt och nedåt-nivå ändringar enligt inställningen känslighet |*bileveldetector. känslighet:* tröskelvärde för detektorernas Poäng (standard: 3,25, 3,25 – 5 är ett rimligt intervall att välja bland. Ju högre mindre känsligt) |rpscore: flytande tal som visar avvikande Poäng vid ändring av nivån uppåt och nedåt |

### <a name="parameters"></a>Parametrar
Mer detaljerad information om dessa indataparametrar finns i tabellen nedan:

| Indataparametrar | Beskrivning | Standardinställning | Typ | Giltigt intervall | Föreslaget intervall |
| --- | --- | --- | --- | --- | --- |
| detektorer. historywindow |Historik (i antal data punkter) som används för avvikelse beräkning |500 |heltal |10-2000 |Tids serie beroende |
| detektorer. spikesdips | Om endast toppar, endast DIP eller båda ska identifieras |Båda |räknas upp |Både, toppar, DIP |Båda |
| bileveldetector. känslighet |Känslighet för ändrings detektor i dubbelriktad nivå. |3.25 |double |Inget |3,25-5 (lägre värden betyder mer känslig) |
| trenddetector. känslighet |Känslighet för positiv trend detektor. |3.25 |double |Inget |3,25-5 (lägre värden betyder mer känslig) |
| tspikedetector. känslighet |Känslighet för TSpike detektor |3 |heltal |1-10 |3-5 (lägre värden betyder mer känsligt) |
| zspikedetector. känslighet |Känslighet för ZSpike detektor |3 |heltal |1-10 |3-5 (lägre värden betyder mer känsligt) |
| postprocess.tailRows |Antal senaste data punkter som ska behållas i resultatet av utdata |0 |heltal |0 (Behåll alla data punkter) eller ange antalet punkter som ska behållas i resultatet |Ej tillämpligt |

### <a name="output"></a>Utdata
API: et kör alla identifieringar i dina tids serie data och returnerar avvikelser och binära inöknings indikatorer för varje tidpunkt. I tabellen nedan visas utdata från API: et.

| Utdata | Beskrivning |
| --- | --- |
| Tid |Tidsstämplar från rå data, eller sammanställda (och/eller) imputerade data om agg regeringen (och/eller) data som saknas Imputation används |
| Data |Värden från rå data, eller sammanställda (och/eller) imputerade data om agg regeringen (och/eller) data som saknas Imputation används |
| TSpike |Binär indikator som visar om en insamling upptäcks av TSpike detektor |
| ZSpike |Binär indikator som visar om en insamling upptäcks av ZSpike detektor |
| rpscore |Ett flytande tal som visar avvikande poäng på dubbelriktad ändring av riktnings nivå |
| rpalert |1/0-värde som anger att det finns en ändrings avvikelse för dubbelriktad nivå baserat på indataportens känslighet |
| tscore |Ett flytande tal som visar avvikande Poäng i positiv trend |
| talert |1/0 värde som anger att det finns en positiv trend avvikelse baserat på indataportens känslighet |

## <a name="scorewithseasonality-api"></a>ScoreWithSeasonality-API
ScoreWithSeasonality-API: et används för att köra avvikelse identifiering i tids serier som har säsongs mönster. Detta API är användbart för att identifiera avvikelser i säsongs mönster.
Följande bild visar ett exempel på avvikelser som upptäckts i en säsongs tids serie. Tids serien har en topp (den första svarta punkten), två DIP (den andra svarta punkten och en i slutet) och en förändring på nivån (röd punkt). Både DIP i mitten av tids serien och nivå ändringen är bara går när säsongs komponenterna har tagits bort från serien.
![Säsongs beroende-API][2]

### <a name="detectors"></a>Detektorer
Identifierarna i säsongs beroende-slutpunkten liknar dem i den icke-säsongs beroende slut punkten, men med något annorlunda parameter namn (visas nedan).

### <a name="parameters"></a>Parametrar

Mer detaljerad information om dessa indataparametrar finns i tabellen nedan:

| Indataparametrar | Beskrivning | Standardinställning | Typ | Giltigt intervall | Föreslaget intervall |
| --- | --- | --- | --- | --- | --- |
| preprocess. aggregationInterval |Agg regerings intervall i sekunder för agg regering av indatamängds tids serier |0 (ingen agg regering utförs) |heltal |0: hoppa över agg regering, > 0 annars |5 minuter till 1 dag, tids serie beroende |
| preprocess. aggregationFunc |Funktion som används för att aggregera data till angiven AggregationInterval |medelvärde |räknas upp |medelvärde, Summa, längd |Ej tillämpligt |
| preprocess. replaceMissing |Värden som används för att tillräkna data som saknas |LKV (senast kända värde) |räknas upp |noll, LKV, medel |Ej tillämpligt |
| detektorer. historywindow |Historik (i antal data punkter) som används för avvikelse beräkning |500 |heltal |10-2000 |Tids serie beroende |
| detektorer. spikesdips | Om endast toppar, endast DIP eller båda ska identifieras |Båda |räknas upp |Både, toppar, DIP |Båda |
| bileveldetector. känslighet |Känslighet för ändrings detektor i dubbelriktad nivå. |3.25 |double |Inget |3,25-5 (lägre värden betyder mer känslig) |
| postrenddetector. känslighet |Känslighet för positiv trend detektor. |3.25 |double |Inget |3,25-5 (lägre värden betyder mer känslig) |
| negtrenddetector. känslighet |Känslighet för negativ trend detektor. |3.25 |double |Inget |3,25-5 (lägre värden betyder mer känslig) |
| tspikedetector. känslighet |Känslighet för TSpike detektor |3 |heltal |1-10 |3-5 (lägre värden betyder mer känsligt) |
| zspikedetector. känslighet |Känslighet för ZSpike detektor |3 |heltal |1-10 |3-5 (lägre värden betyder mer känsligt) |
| säsongs beroende. enable |Om säsongs beroende-analys ska utföras |true |boolean |SANT, FALSKT |Tids serie beroende |
| säsongs beroende. numSeasonality |Maximalt antal periodiska cykler som ska identifieras |1 |heltal |1, 2 |1-2 |
| säsongs beroende. Transform |Om trender (och) trend komponenter ska tas bort innan avvikelse identifiering tillämpas |tids säsong |räknas upp |ingen, avsäsong, deseasontrend |Ej tillämpligt |
| postprocess.tailRows |Antal senaste data punkter som ska behållas i resultatet av utdata |0 |heltal |0 (Behåll alla data punkter) eller ange antalet punkter som ska behållas i resultatet |Ej tillämpligt |

### <a name="output"></a>Utdata
API: et kör alla identifieringar i dina tids serie data och returnerar avvikelser och binära inöknings indikatorer för varje tidpunkt. I tabellen nedan visas utdata från API: et.

| Utdata | Beskrivning |
| --- | --- |
| Tid |Tidsstämplar från rå data, eller sammanställda (och/eller) imputerade data om agg regeringen (och/eller) data som saknas Imputation används |
| OriginalData |Värden från rå data, eller sammanställda (och/eller) imputerade data om agg regeringen (och/eller) data som saknas Imputation används |
| ProcessedData |Något av följande alternativ: <ul><li>Tids serier har justerats fortare om betydande säsongs beroende har upptäckts och alternativet avsäsong valts.</li><li>tids serien har justerats och avtrend ATS om betydande säsongs beroende har upptäckts och alternativet deseasontrend valts</li><li>i annat fall är det här alternativet detsamma som OriginalData</li> |
| TSpike |Binär indikator som visar om en insamling upptäcks av TSpike detektor |
| ZSpike |Binär indikator som visar om en insamling upptäcks av ZSpike detektor |
| BiLevelChangeScore |Ett flytande tal som visar avvikande poäng på nivå ändring |
| BiLevelChangeAlert |1/0-värde som anger att det finns en nivå ändrings avvikelse baserat på indataports känslighet |
| PosTrendScore |Ett flytande tal som visar avvikande Poäng i positiv trend |
| PosTrendAlert |1/0 värde som anger att det finns en positiv trend avvikelse baserat på indataportens känslighet |
| NegTrendScore |Ett flytande tal som visar avvikande Poäng i negativ trend |
| NegTrendAlert |1/0 värde som anger att det finns en negativ trend avvikelse baserat på indataportens känslighet |

[1]: ./media/apps-anomaly-detection-api/anomaly-detection-score.png
[2]: ./media/apps-anomaly-detection-api/anomaly-detection-seasonal.png