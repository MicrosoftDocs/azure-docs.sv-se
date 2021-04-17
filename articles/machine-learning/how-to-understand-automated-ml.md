---
title: Utvärdera AutoML-experimentresultat
titleSuffix: Azure Machine Learning
description: Lär dig hur du visar och utvärderar diagram och mått för var och en av dina automatiserade maskininlärningsexperimentkörningar.
services: machine-learning
author: gregorybchris
ms.author: chgrego
ms.reviewer: nibaccam
ms.service: machine-learning
ms.subservice: core
ms.date: 12/09/2020
ms.topic: conceptual
ms.custom: how-to, contperf-fy21q2, automl
ms.openlocfilehash: 71d8d577bdfd8b359ce872f3489b60dca0b462b2
ms.sourcegitcommit: d3bcd46f71f578ca2fd8ed94c3cdabe1c1e0302d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2021
ms.locfileid: "107575640"
---
# <a name="evaluate-automated-machine-learning-experiment-results"></a>Utvärdera resultat från automatiserade maskininlärningsexperiment

I den här artikeln får du lära dig hur du utvärderar och jämför modeller som tränats av ditt automatiserade maskininlärningsexperiment (automatiserad ML). Under ett automatiserat ML-experiment skapas många körningar och varje körning skapar en modell. För varje modell genererar automatiserad ML utvärderingsmått och diagram som hjälper dig att mäta modellens prestanda. 

Till exempel genererar automatiserad ML följande diagram baserat på experimenttyp.

| Klassificering| Regression/prognostiserade |
| ----------------------------------------------------------- | ---------------------------------------- |
| [Förvirringsmatris](#confusion-matrix)                       | [Residualhistogram](#residuals)        |
| [ROC-kurva (receiver operating characteristic)](#roc-curve) | [Förutsagt kontra sant](#predicted-vs-true) |
| [Kurva för precisions träffsäkerhet (PR)](#precision-recall-curve)      |                                          |
| [Lyftkurva](#lift-curve)                                   |                                          |
| [Kumulativ ökningskurva](#cumulative-gains-curve)           |                                          |
| [Kalibreringskurvan](#calibration-curve)                     |                     


## <a name="prerequisites"></a>Förutsättningar

- En Azure-prenumeration. (Om du inte har en Azure-prenumeration kan [du skapa ett kostnadsfritt konto](https://aka.ms/AMLFree) innan du börjar)
- Ett Azure Machine Learning experiment som skapats med antingen:
  - Den [Azure Machine Learning-studio](how-to-use-automated-ml-for-ml-models.md) (ingen kod krävs)
  - Den [Azure Machine Learning Python SDK](how-to-configure-auto-train.md)

## <a name="view-run-results"></a>Visa körningsresultat

När det automatiserade ML-experimentet har slutförts kan du hitta en historik över körningarna via:
  - En webbläsare med [Azure Machine Learning-studio](overview-what-is-machine-learning-studio.md)
  - En Jupyter Notebook med [RunDetails Jupyter-widgeten](/python/api/azureml-widgets/azureml.widgets.rundetails)

Följande steg och video visar hur du visar mått och diagram för körningshistorik och modellutvärdering i studio:

1. [Logga in på studio och](https://ml.azure.com/) navigera till din arbetsyta.
1. I den vänstra menyn väljer du **Experiment.**
1. Välj experimentet i listan över experiment.
1. Välj en automatiserad ML-körning i tabellen längst ned på sidan.
1. På fliken **Modeller** väljer du **Algoritmnamn för** den modell som du vill utvärdera.
1. På **fliken Mått** använder du kryssrutorna till vänster för att visa mått och diagram.

![Steg för att visa mått i Studio](./media/how-to-understand-automated-ml/how-to-studio-metrics.gif)

## <a name="classification-metrics"></a>Klassificeringsmått

Automatiserad ML beräknar prestandamått för varje klassificeringsmodell som genereras för experimentet. De här måtten baseras på scikit learn-implementeringen. 

Många klassificeringsmått definieras för binär klassificering i två klasser och kräver medelvärde över klasser för att producera en poäng för klassificering med flera klasser. Scikit-learn innehåller flera medelvärdesmetoder, varav tre automatiserade ML exponerar: **makro,** **mikro** och **viktad**.

- **Makro** – Beräkna måttet för varje klass och ta det ovikta medelvärdet
- **Micro** – Beräkna måttet globalt genom att räkna det totala antalet sanna positiva, falska negativa och falska positiva resultat (oberoende av klasser).
- **Viktat** – Beräkna måttet för varje klass och ta det viktade medelvärdet baserat på antalet urval per klass.

Även om varje medelvärdesmetod har sina fördelar är en vanlig faktor när du väljer lämplig metod obalans i klassen. Om klasserna har olika antal urval kan det vara mer informativt att använda ett makrogenomsnitt där klasser som är klassificerade ges samma viktning som majoritetsklasser. Läs mer om [binära mått jämfört med flerklassmått i automatiserad ML.](#binary-vs-multiclass-classification-metrics) 

I följande tabell sammanfattas de modellprestandamått som automatiserad ML beräknar för varje klassificeringsmodell som genereras för experimentet. Mer information finns i scikit-learn-dokumentationen som är länkad i **fältet Beräkning** för varje mått. 

|Metric|Beskrivning|Beräkning|
|--|--|---|
|AUC | AUC är området under kurva för [mottagaroperativsystemet.](#roc-curve)<br><br> **Mål:** Närmare 1 desto bättre <br> **Intervall:** [0, 1]<br> <br>Måttnamn som stöds är: <li>`AUC_macro`, det aritmetiska medelvärdet för AUC för varje klass.<li> `AUC_micro`, beräknat genom att kombinera sanna positiva och falska positiva resultat från varje klass. <li> `AUC_weighted`, aritmetiskt medelvärde för poängen för varje klass, viktat med antalet sanna instanser i varje klass.   |[Beräkning](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.roc_auc_score.html) | 
|accuracy| Noggrannhet är förhållandet mellan förutsägelser som exakt matchar de sanna klassetiketterna. <br> <br>**Mål:** Närmare 1 desto bättre <br> **Intervall:** [0, 1]|[Beräkning](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.accuracy_score.html)|
|average_precision|Genomsnittlig precision sammanfattar en kurva för precisions träffsäkerhet som det viktade medelvärdet av precisioner som uppnås vid varje tröskelvärde, med ökningen av träffsäkerhet från det tidigare tröskelvärdet som används som vikt. <br><br> **Mål:** Närmare 1 desto bättre <br> **Intervall:** [0, 1]<br> <br>Måttnamn som stöds är:<li>`average_precision_score_macro`, det aritmetiska medelvärdet av den genomsnittliga precisionspoängen för varje klass.<li> `average_precision_score_micro`, beräknat genom att kombinera sanna positiva och falska positiva vid varje cutoff.<li>`average_precision_score_weighted`, det aritmetiska medelvärdet av den genomsnittliga precisionspoängen för varje klass, viktat med antalet sanna instanser i varje klass.|[Beräkning](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.average_precision_score.html)|
balanced_accuracy|Balanserad noggrannhet är det aritmetiska medelvärdet för träffsäkerhet för varje klass.<br> <br>**Mål:** Närmare 1 desto bättre <br> **Intervall:** [0, 1]|[Beräkning](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|
f1_score|F1-poäng är det första medelvärdet av precision och träffsäkerhet. Det är ett bra balanserat mått på både falska positiva och falska negativa resultat. Men det tar inte hänsyn till sanna negativa. <br> <br>**Mål:** Närmare 1 desto bättre <br> **Intervall:** [0, 1]<br> <br>Måttnamn som stöds är:<li>  `f1_score_macro`: det aritmetiska medelvärdet för F1-poäng för varje klass. <li> `f1_score_micro`: beräknas genom att räkna det totala antalet sanna positiva, falska negativa och falska positiva. <li> `f1_score_weighted`: viktat medelvärde efter klassfrekvens för F1-poäng för varje klass.|[Beräkning](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.f1_score.html)|
log_loss|Det här är den förlustfunktion som används i (multinomial) logistisk regression och tillägg av den, till exempel neurala nätverk, som definieras som den negativa loggsanno sannolikheten för de sanna etiketterna givet en probabilistisk klassificerare förutsägelser. <br><br> **Mål:** Närmare 0 desto bättre <br> **Intervall:** [0, inf)|[Beräkning](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.log_loss.html)|
norm_macro_recall| Normaliserat makroinkallelse återkallas makrogenomsnitt och normaliseras, så att slumpmässiga prestanda har ett resultat på 0 och perfekt prestanda har ett resultat på 1. <br> <br>**Mål:** Närmare 1 desto bättre <br> **Intervall:** [0, 1] |`(recall_score_macro - R)`&nbsp;/&nbsp;`(1 - R)` <br><br>där är `R` det förväntade värdet för för `recall_score_macro` slumpmässiga förutsägelser.<br><br>`R = 0.5`&nbsp;för &nbsp; binär &nbsp; klassificering. <br>`R = (1 / C)` för C-klassklassificeringsproblem.|
matthews_correlation | Matthews korrelationskoefficient är ett balanserat mått på noggrannhet, som kan användas även om en klass har många fler urval än en annan. En koefficient på 1 indikerar perfekt förutsägelse, 0 slumpmässiga förutsägelser och -1 inverterad förutsägelse.<br><br> **Mål:** Närmare 1 desto bättre <br> **Intervall:** [-1, 1]|[Beräkning](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.matthews_corrcoef.html)|
precision|Precision är möjligheten för en modell att undvika att märka negativa urval som positiva. <br><br> **Mål:** Närmare 1 desto bättre <br> **Intervall:** [0, 1]<br> <br>Måttnamn som stöds är: <li> `precision_score_macro`, det aritmetiska medelvärdet av precision för varje klass. <li> `precision_score_micro`, beräknat globalt genom att räkna det totala antalet sanna positiva och falska positiva resultat. <li> `precision_score_weighted`, det aritmetiska medelvärdet av precision för varje klass, viktat med antalet sanna instanser i varje klass.|[Beräkning](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.precision_score.html)|
träffsäkerhet| Kom ihåg att en modell kan identifiera alla positiva urval. <br><br> **Mål:** Närmare 1 desto bättre <br> **Intervall:** [0, 1]<br> <br>Måttnamn som stöds är: <li>`recall_score_macro`: det aritmetiska medelvärdet för återkallande för varje klass. <li> `recall_score_micro`: beräknas globalt genom att räkna det totala antalet sanna positiva, falska negativa och falska positiva resultat.<li> `recall_score_weighted`: det aritmetiska medelvärdet för återkallande för varje klass, viktat med antalet sanna instanser i varje klass.|[Beräkning](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|
weighted_accuracy|Viktad noggrannhet är noggrannheten där varje urval viktas med det totala antalet urval som tillhör samma klass. <br><br>**Mål:** Närmare 1 desto bättre <br>**Intervall:** [0, 1]|[Beräkning](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.accuracy_score.html)|

### <a name="binary-vs-multiclass-classification-metrics"></a>Klassificeringsmått för binär eller multiklass

Automatiserad ML skiljer inte mellan binära mått och flerklassmått. Samma valideringsmått rapporteras om en datauppsättning har två klasser eller fler än två klasser. Vissa mått är dock avsedda för klassificering med flera klasser. När de tillämpas på en binär datauppsättning behandlar de här måtten inte någon klass som `true` klass, som du kanske förväntar dig. Mått som är tydligt avsedda för flera klasser har suffixet `micro` , `macro` eller `weighted` . Exempel är `average_precision_score` , , , och `f1_score` `precision_score` `recall_score` `AUC` .

I stället för att beräkna återkallande som beräknas till exempel medelvärdet för genomsnittligt antal återkallade klasser ( , , eller ) för båda klasserna i `tp / (tp + fn)` `micro` en `macro` `weighted` binär klassificeringsdatamängd. Detta motsvarar beräkning av återkallning `true` för klassen och klassen separat och tar sedan `false` medelvärdet av de två.

Automatiserad ML beräknar inte binära mått, det vill säga mått för binära klassificeringsdatamängder. Dessa mått kan dock beräknas manuellt med hjälp av [felmatrisen som](#confusion-matrix) automatisk ML genererade för den specifika körningen. Du kan till exempel beräkna precision, , med de sanna positiva och falska positiva värdena som `tp / (tp + fp)` visas i ett 2x2-felmatrisdiagram.

## <a name="confusion-matrix"></a>Förvirringsmatris

Förvirringsmatriser ger ett visuellt objekt för hur en maskininlärningsmodell gör systematisk fel i sina förutsägelser för klassificeringsmodeller. Ordet "förvirring" i namnet kommer från en modell som är "förvirrande" eller felmärkta exempel. En cell på rad och kolumn i en felmatris innehåller antalet exempel i utvärderingsdatamängden som tillhör klassen och klassificerades av `i` `j` modellen som klass `C_i` `C_j` .

I studio indikerar en mörkare cell ett högre antal urval. Om **du väljer Normaliserad** vy i listrutan normaliseras varje matrisrad för att visa den procentandel av klassen som `C_i` förväntas vara klassen `C_j` . Fördelen med Raw-standardvyn är att du kan se om obalansen i fördelningen av faktiska klasser orsakade att modellen felklassificerade exempel från klass 1, vilket är ett vanligt problem i obalanserade datamängder. 

Förvirringsmatrisen för en bra modell har de flesta urval längs diagonalen.

### <a name="confusion-matrix-for-a-good-model"></a>Felmatris för en bra modell 
![Felmatris för en bra modell ](./media/how-to-understand-automated-ml/chart-confusion-matrix-good.png)

### <a name="confusion-matrix-for-a-bad-model"></a>Felmatris för en felaktig modell
![Felmatris för en felaktig modell](./media/how-to-understand-automated-ml/chart-confusion-matrix-bad.png)

## <a name="roc-curve"></a>ROC-kurva

ROC-kurvan (receiver operating characteristic) ritar relationen mellan true positive rate (TPR) och false positive rate (FPR) när beslutströskeln ändras. ROC-kurvan kan vara mindre informativ när du tränar modeller på datauppsättningar med obalans i hög klass, eftersom majoritetsklassen kan undvika bidrag från klasser med beroenden.

Området under kurvan (AUC) kan tolkas som andelen korrekt klassificerade urval. Mer exakt är AUC sannolikheten att klassificeraren rangordnar ett slumpmässigt valt positivt urval högre än ett slumpmässigt valt negativt urval. Kurvans form ger en intuition för relationen mellan TPR och FPR som en funktion av klassificeringströskeln eller beslutsgränsen.

En kurva som närmar sig det övre vänstra hörnet i diagrammet närmar sig en 100 % TPR och 0 % FPR, den bästa möjliga modellen. En slumpmässig modell skulle generera en `y = x` ROC-kurva längs linjen från det nedre vänstra hörnet till det övre högra hörnet. En sämre än slumpmässig modell skulle ha en ROC-kurva som sjunker under `y = x` linjen.
> [!TIP]
> För klassificeringsexperiment kan vart och ett av linjediagrammen som skapas för automatiserade ML-modeller användas för att utvärdera modellen per klass eller medelvärdet för alla klasser. Du kan växla mellan dessa olika vyer genom att klicka på klassetiketter i förklaringen till höger om diagrammet.
### <a name="roc-curve-for-a-good-model"></a>ROC-kurva för en bra modell
![ROC-kurva för en bra modell](./media/how-to-understand-automated-ml/chart-roc-curve-good.png)

### <a name="roc-curve-for-a-bad-model"></a>ROC-kurva för en felaktig modell
![ROC-kurva för en felaktig modell](./media/how-to-understand-automated-ml/chart-roc-curve-bad.png)

## <a name="precision-recall-curve"></a>Kurva för precisions träffsäkerhet

Kurvan för precisions träffsäkerhet ritar relationen mellan precision och träffsäkerhet när beslutströskeln ändras. Träffsäkerhet är möjligheten för en modell att identifiera alla positiva urval och precision är möjligheten för en modell att undvika att märka negativa urval som positiva. Vissa affärsproblem kan kräva högre träffsäkerhet och viss högre precision beroende på den relativa vikten av att undvika falska negativa eller falska positiva resultat.
> [!TIP]
> För klassificeringsexperiment kan vart och ett av linjediagrammen som skapas för automatiserade ML-modeller användas för att utvärdera modellen per klass eller medelvärdet för alla klasser. Du kan växla mellan dessa olika vyer genom att klicka på klassetiketter i förklaringen till höger om diagrammet.
### <a name="precision-recall-curve-for-a-good-model"></a>Precisionskurva för en bra modell
![Precisionskurva för en bra modell](./media/how-to-understand-automated-ml/chart-precision-recall-curve-good.png)

### <a name="precision-recall-curve-for-a-bad-model"></a>Kurva för precisions träffsäkerhet för en felaktig modell
![Kurva för precisions träffsäkerhet för en felaktig modell](./media/how-to-understand-automated-ml/chart-precision-recall-curve-bad.png)

## <a name="cumulative-gains-curve"></a>Kumulativ ökningskurva

Den kumulativa ökningskurvan ritar ut procentandelen positiva urval korrekt klassificerade som en funktion av procentandelen urval som beaktas, där vi överväger urval i ordningen för den förväntade sannolikheten.

Om du vill beräkna vinst sorterar du först alla urval från högsta till lägsta sannolikhet som förutsägs av modellen. Ta sedan `x%` de högsta konfidensförutsägelserna. Dividera antalet positiva urval som identifierats i det `x%` med det totala antalet positiva urval för att få vinsten. Kumulativ vinst är den procentandel positiva urval som vi identifierar när vi överväger en procentandel av de data som mest sannolikt tillhör den positiva klassen.

En perfekt modell rangordnar alla positiva urval framför alla negativa urval, vilket ger en kumulativ ökningskurva som består av två räta segment. Den första är en linje med lutning från till där är den `1 / x` `(0, 0)` `(x, 1)` `x` fraktion av urval som tillhör den positiva klassen ( `1 / num_classes` om klasserna är balanserade). Den andra är en vågrät linje från `(x, 1)` till `(1, 1)` . I det första segmentet klassificeras alla positiva urval korrekt och kumulativ vinst går till inom `100%` det första urval som `x%` övervägs.

Den slumpmässiga baslinjemodellen har en kumulativ ökningskurva efter där för urval som endast anses vara ungefär `y = x` av de totala positiva `x%` `x%` urvalen har identifierats. En perfekt modell har en mikrogenomsnittskurva som berör det övre vänstra hörnet och en makrogenomsnittslinje som har lutning tills den kumulativa vinsten är 100 % och sedan vågrät tills dataprocenten är `1 / num_classes` 100.
> [!TIP]
> För klassificeringsexperiment kan vart och ett av linjediagrammen som skapas för automatiserade ML-modeller användas för att utvärdera modellen per klass eller medelvärdet för alla klasser. Du kan växla mellan dessa olika vyer genom att klicka på klassetiketter i förklaringen till höger om diagrammet.
### <a name="cumulative-gains-curve-for-a-good-model"></a>Kumulativ ökningskurva för en bra modell
![Kumulativ ökningskurva för en bra modell](./media/how-to-understand-automated-ml/chart-cumulative-gains-curve-good.png)

### <a name="cumulative-gains-curve-for-a-bad-model"></a>Kumulativ ökningskurva för en felaktig modell
![Kumulativ ökningskurva för en felaktig modell](./media/how-to-understand-automated-ml/chart-cumulative-gains-curve-bad.png)

## <a name="lift-curve"></a>Lyftkurva

Liftkurvan visar hur många gånger bättre en modell presterar jämfört med en slumpmässig modell. Lift definieras som förhållandet mellan kumulativ vinst och kumulativ vinst för en slumpmässig modell.

Den här relativa prestandan tar hänsyn till att klassificeringen blir svårare när du ökar antalet klasser. (En slumpmässig modell förutsäger felaktigt en högre fraktion av urval från en datauppsättning med 10 klasser jämfört med en datauppsättning med två klasser)

Baslinjens lyftkurva är `y = 1` den linje där modellens prestanda är konsekvent med modellens prestanda för en slumpmässig modell. I allmänhet är liftkurvan för en bra modell högre upp i diagrammet och längre från x-axeln, vilket visar att när modellen är mest säker på sina förutsägelser presterar den många gånger bättre än slumpmässig gissning.

> [!TIP]
> För klassificeringsexperiment kan vart och ett av linjediagrammen som skapas för automatiserade ML-modeller användas för att utvärdera modellen per klass eller medelvärdet för alla klasser. Du kan växla mellan dessa olika vyer genom att klicka på klassetiketter i förklaringen till höger om diagrammet.
### <a name="lift-curve-for-a-good-model"></a>Lift curve for a good model (Liftkurva för en bra modell)
![Lift curve for a good model (Liftkurva för en bra modell)](./media/how-to-understand-automated-ml/chart-lift-curve-good.png)
 
### <a name="lift-curve-for-a-bad-model"></a>Lift curve for a bad model (Liftkurva för en felaktig modell)
![Lift curve for a bad model (Liftkurva för en felaktig modell)](./media/how-to-understand-automated-ml/chart-lift-curve-bad.png)

## <a name="calibration-curve"></a>Kalibreringskurvan

Kurvan ritar en modells konfidens för dess förutsägelser mot andelen positiva urval på varje konfidensnivå. En väl kalibrerad modell klassificerar korrekt 100 % av förutsägelserna som den tilldelar 100 % konfidens, 50 % av förutsägelserna den tilldelar 50 % konfidens, 20 % av förutsägelserna den tilldelar 20 % konfidens och så vidare. En perfekt kalibrerad modell har en kurva efter linjen där modellen perfekt förutsäger `y = x` sannolikheten att urvalen tillhör varje klass.

En översäker modell överutsäger sannolikheter nära noll och en, och är sällan osäker på klassen för varje urval och kurvakurvan ser ut ungefär som bakåt "S". En undersäker modell tilldelar i genomsnitt en lägre sannolikhet till den klass som den förutsäger och den associerade kurva för kurva ser ut ungefär som ett "S". Kurvan visar inte en modells förmåga att klassificera korrekt, utan i stället dess förmåga att korrekt tilldela konfidens till sina förutsägelser. En dålig modell kan fortfarande ha en bra kurva om modellen korrekt tilldelar låg konfidens och hög osäkerhet.

> [!NOTE]
> Kalibreringskurvan är känslig för antalet urval, så en liten valideringsuppsättning kan ge brusresultat som kan vara svåra att tolka. Detta innebär inte nödvändigtvis att modellen inte kalibreras väl.

### <a name="calibration-curve-for-a-good-model"></a>Kalibreringskurva för en bra modell
![Kalibreringskurva för en bra modell](./media/how-to-understand-automated-ml/chart-calibration-curve-good.png)

### <a name="calibration-curve-for-a-bad-model"></a>Kalibreringskurva för en felaktig modell
![Kalibreringskurva för en felaktig modell](./media/how-to-understand-automated-ml/chart-calibration-curve-bad.png)

## <a name="regressionforecasting-metrics"></a>Regressions-/prognosmått

Automatiserad ML beräknar samma prestandamått för varje modell som genereras, oavsett om det är ett regressions- eller prognosexperiment. Dessa mått genomgår också normalisering för att möjliggöra jämförelse mellan modeller som tränats med data med olika intervall. Mer information finns i [normalisering av mått.](#metric-normalization)  

I följande tabell sammanfattas de modellprestandamått som genererats för regressions- och prognostiserade experiment. Precis som klassificeringsmått baseras de här måtten även på scikit learn-implementeringar. Lämplig scikit learn-dokumentation länkas i enlighet med detta i **fältet** Beräkning.

|Metric|Beskrivning|Beräkning|
--|--|--|
explained_variance|Förklarad varians mäter i vilken utsträckning en modell står för variationen i målvariabeln. Det är den procentuella minskningen i variansen för ursprungliga data jämfört med variansen för felen. När medelvärdet för felen är 0 är det lika med bestämningskoefficienten (se r2_score nedan). <br> <br> **Mål:** Närmare 1 desto bättre <br> **Intervall:** (-inf, 1]|[Beräkning](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.explained_variance_score.html)|
mean_absolute_error|Medel absoluta fel är det förväntade värdet av absolut värde för skillnaden mellan målet och förutsägelsen.<br><br> **Mål:** Närmare 0 desto bättre <br> **Intervall:** [0, inf) <br><br> Typer: <br>`mean_absolute_error` <br>  `normalized_mean_absolute_error`, mean_absolute_error dividerat med dataintervallet. | [Beräkning](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_absolute_error.html)|
mean_absolute_percentage_error|MEAN Absolute Percentage Error (MAPE) är ett mått på den genomsnittliga skillnaden mellan ett förutsagt värde och det faktiska värdet.<br><br> **Mål:** Närmare 0 desto bättre <br> **Intervall:** [0, inf) ||
median_absolute_error|Medianen för absoluta fel är medianen för alla absoluta skillnader mellan målet och förutsägelsen. Den här förlusten är robust för extremvärden.<br><br> **Mål:** Närmare 0 desto bättre <br> **Intervall:** [0, inf)<br><br>Typer: <br> `median_absolute_error`<br> `normalized_median_absolute_error`: median_absolute_error dividerat med dataintervallet. |[Beräkning](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.median_absolute_error.html)|
r2_score|R<sup>2</sup> (bestämningskoefficienten) mäter den proportionella minskningen av MSE (mean squared error) i förhållande till den totala variansen för observerade data. <br> <br> **Mål:** Närmare 1 desto bättre <br> **Intervall:** [-1, 1]<br><br>Obs! R<sup>2</sup> har ofta intervallet (-inf, 1]. MSE kan vara större än den observerade variansen, så R<sup>2</sup> kan ha godtyckligt stora negativa värden, beroende på data och modellförutsägelser. Automatiserade ML-klipp rapporterade R<sup>2-poäng</sup> vid -1, så värdet -1 för R<sup>2</sup> innebär sannolikt att den sanna R<sup>2-poängen</sup> är mindre än -1. Överväg de andra måttvärdena och egenskaperna för data när du tolkar en negativ R<sup>2-poäng.</sup>|[Beräkning](https://scikit-learn.org/0.16/modules/generated/sklearn.metrics.r2_score.html)|
root_mean_squared_error |RMSE (Root Mean Square Error) är kvadratroten av den förväntade kvadratskillnaden mellan målet och förutsägelsen. RMSE är lika med standardavvikelsen för ett objektivt beräkningsvärde.<br> <br> **Mål:** Närmare 0 desto bättre <br> **Intervall:** [0, inf)<br><br>Typer:<br> `root_mean_squared_error` <br> `normalized_root_mean_squared_error`: root_mean_squared_error dividerat med dataintervallet. |[Beräkning](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_squared_error.html)|
root_mean_squared_log_error|Rotens genomsnittliga kvadratloggfel är kvadratroten av det förväntade logaritmiska felet i kvadrat.<br><br>**Mål:** Närmare 0 desto bättre <br> **Intervall:** [0, inf) <br> <br>Typer: <br>`root_mean_squared_log_error` <br> `normalized_root_mean_squared_log_error`: root_mean_squared_log_error dividerat med dataintervallet.  |[Beräkning](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_squared_log_error.html)|
spearman_correlation| Spearman-korrelation är ett icke-parametriskt mått på monotoniteten i relationen mellan två datauppsättningar. Till skillnad från Pearson-korrelationen förutsätter Spearman-korrelationen inte att båda datauppsättningarna normalt distribueras. Precis som andra korrelationskoefficienter varierar Spearman mellan -1 och 1 och 0 vilket innebär att det inte finns någon korrelation. Korrelationer av -1 eller 1 innebär en exakt monoton relation. <br><br> Spearman är ett korrelationsmått i rangordningen, vilket innebär att ändringar av förutsagda eller faktiska värden inte ändrar Spearman-resultatet om de inte ändrar rangordningen för förutsagda eller faktiska värden.<br> <br> **Mål:** Närmare 1 desto bättre <br> **Intervall:** [-1, 1]|[Beräkning](https://docs.scipy.org/doc/scipy-0.16.1/reference/generated/scipy.stats.spearmanr.html)|

### <a name="metric-normalization"></a>Mått normalisering

Automatiserad ML normaliserar regressions- och prognosmått som möjliggör jämförelse mellan modeller som tränas på data med olika intervall. En modell som tränas på data med ett större intervall har högre fel än samma modell som tränats med data med ett mindre intervall, om inte felet normaliseras.

Det finns ingen standardmetod för att normalisera felmått, men automatiserad ML använder den vanliga metoden för att dela upp felet med dataintervallet: `normalized_error = error / (y_max - y_min)`

Vid utvärdering av en prognosmodell för tidsseriedata vidtar automatiserad ML extra åtgärder för att säkerställa att normaliseringen sker per tidsserie-ID (kornighet), eftersom varje tidsserie sannolikt har en annan distribution av målvärden.
## <a name="residuals"></a>Residualer

Residualdiagrammet är ett histogram över förutsägelsefelen (residualer) som genereras för regressions- och prognosexperiment. Residualer beräknas som `y_predicted - y_true` för alla urval och visas sedan som ett histogram för att visa modellens bias.

Observera att båda modellerna i det här exemplet är något partiska för att förutsäga lägre än det faktiska värdet. Detta är inte ovanligt för en datauppsättning med en skev fördelning av faktiska mål, men indikerar sämre modellprestanda. En bra modell har en residualfördelning som toppar på noll med få residualer i extremfall. En sämre modell har en spridning av residualfördelningen med färre urval runt noll.

### <a name="residuals-chart-for-a-good-model"></a>Residualdiagram för en bra modell
![Residualdiagram för en bra modell](./media/how-to-understand-automated-ml/chart-residuals-good.png)

### <a name="residuals-chart-for-a-bad-model"></a>Residualdiagram för en felaktig modell
![Residualdiagram för en felaktig modell](./media/how-to-understand-automated-ml/chart-residuals-bad.png)

## <a name="predicted-vs-true"></a>Förutsagt kontra sant

För regressions- och prognosexperiment ritar det förutsagda kontra sanna diagrammet relationen mellan målfunktionen (sanna/faktiska värden) och modellens förutsägelser. De sanna värdena är indelade längs x-axeln och för varje lagerplats ritas det förväntade medelvärdet med felstaplar. På så sätt kan du se om en modell är partisk för att förutsäga vissa värden. Linjen visar den genomsnittliga förutsägelsen och det skuggade området anger variansen för förutsägelser kring medelvärdet.

Ofta har det vanligaste sanna värdet de mest exakta förutsägelserna med den lägsta variansen. Avståndet för trendlinjen från den ideala linjen där det finns få sanna värden är ett bra mått `y = x` på modellens prestanda på extremvärden. Du kan använda histogrammet längst ned i diagrammet för att få information om den faktiska datafördelningen. Om du tar med fler dataexempel där fördelningen är gles kan du förbättra modellens prestanda för data som inte är inlästa.

Observera i det här exemplet att den bättre modellen har en förutsagd kontra sann linje som är närmare den perfekta `y = x` linjen.

### <a name="predicted-vs-true-chart-for-a-good-model"></a>Förutsagt kontra sant-diagram för en bra modell
![Förutsagt kontra sant-diagram för en bra modell](./media/how-to-understand-automated-ml/chart-predicted-true-good.png)

### <a name="predicted-vs-true-chart-for-a-bad-model"></a>Förutsagt kontra sant-diagram för en felaktig modell
![Förutsagt kontra sant-diagram för en felaktig modell](./media/how-to-understand-automated-ml/chart-predicted-true-bad.png)

## <a name="model-explanations-and-feature-importances"></a>Modellförklaringar och funktions prioriteter

Även om mått och diagram för modellutvärdering är bra för att mäta den allmänna kvaliteten på en modell, är det viktigt att kontrollera vilka datauppsättningar som har en modell som används för att göra förutsägelser när du använder ansvarsfull AI. Det är därför som automatiserad ML tillhandahåller en instrumentpanel med modellförklaringar för att mäta och rapportera relativa bidrag från datamängdsfunktioner. Se hur du [visar instrumentpanelen med förklaringar i Azure Machine Learning-studio](how-to-use-automated-ml-for-ml-models.md#model-explanations-preview).

En första kodupplevelse finns i konfigurera modellförklaringar för [automatiserade ML-experiment med Azure Machine Learning Python SDK](how-to-machine-learning-interpretability-automl.md).

> [!NOTE]
> ForecastTCN-modellen stöds för närvarande inte av automatiserade ML-förklaringar och andra prognosmodeller kan ha begränsad åtkomst till tolkningsverktyg.

## <a name="next-steps"></a>Nästa steg
* Prova [notebook-exempelanteckningsböckerna med förklaring av automatiserad maskininlärningsmodell.](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/explain-model)
* För automatiserade ML-specifika frågor kan du kontakta askautomatedml@microsoft.com .
