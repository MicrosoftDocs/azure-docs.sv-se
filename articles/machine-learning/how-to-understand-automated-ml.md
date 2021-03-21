---
title: Utvärdera AutoML experiment resultat
titleSuffix: Azure Machine Learning
description: Lär dig hur du visar och utvärderar diagram och mått för var och en av dina automatiserade experiment körningar i Machine Learning.
services: machine-learning
author: gregorybchris
ms.author: chgrego
ms.reviewer: nibaccam
ms.service: machine-learning
ms.subservice: core
ms.date: 12/09/2020
ms.topic: conceptual
ms.custom: how-to, contperf-fy21q2, automl
ms.openlocfilehash: 6d8c56bc306a7ab0bf118d04f64d6523fc385cdd
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102520786"
---
# <a name="evaluate-automated-machine-learning-experiment-results"></a>Utvärdera resultat från automatiska maskin inlärnings experiment

I den här artikeln lär du dig hur du utvärderar och jämför modeller som har tränats av ditt automatiserade experiment (med automatisk maskin inlärning). Under ett automatiserat ML experiment skapas många körningar och varje körning skapar en modell. För varje modell genererar automatiserad ML utvärderings statistik och diagram som hjälper dig att mäta modellens prestanda. 

Automatiserad ML genererar till exempel följande diagram baserat på experiment typ.

| Klassificering| Regression/Prognosticering |
| ----------------------------------------------------------- | ---------------------------------------- |
| [Förvirringsmatris](#confusion-matrix)                       | [Residualhistogram](#residuals)        |
| [ROC-kurva (mottagarens operativa egenskaper)](#roc-curve) | [Förutsägs jämfört med sant](#predicted-vs-true) |
| [Precisions åter kallelse (PR) kurva](#precision-recall-curve)      |                                          |
| [Lyft kurva](#lift-curve)                                   |                                          |
| [Kurva för ackumulerade vinster](#cumulative-gains-curve)           |                                          |
| [Kalibrerings kurva](#calibration-curve)                     |                     


## <a name="prerequisites"></a>Förutsättningar

- En Azure-prenumeration. (Om du inte har en Azure-prenumeration kan du [skapa ett kostnads fritt konto](https://aka.ms/AMLFree) innan du börjar)
- Ett Azure Machine Learning experiment har skapats med något av följande:
  - [Azure Machine Learning Studio](how-to-use-automated-ml-for-ml-models.md) (ingen kod krävs)
  - [Azure Machine Learning python SDK](how-to-configure-auto-train.md)

## <a name="view-run-results"></a>Visa körnings resultat

När ditt automatiserade ML-experiment är klart kan du hitta en historik över körningarna via:
  - En webbläsare med [Azure Machine Learning Studio](overview-what-is-machine-learning-studio.md)
  - En Jupyter-anteckningsbok med [RunDetails Jupyter-widgeten](/python/api/azureml-widgets/azureml.widgets.rundetails)

I följande steg och video visas hur du visar körnings historik och modell utvärderings mått och diagram i Studio:

1. [Logga in på Studio](https://ml.azure.com/) och navigera till din arbets yta.
1. Välj **experiment** i menyn till vänster.
1. Välj experimentet i listan över experiment.
1. I tabellen längst ned på sidan väljer du en automatisk ML-körning.
1. På fliken **modeller** väljer du **algoritmens namn** för den modell som du vill utvärdera.
1. På fliken **mått** använder du kryss rutorna till vänster för att visa mått och diagram.

![Steg för att visa mått i Studio](./media/how-to-understand-automated-ml/how-to-studio-metrics.gif)

## <a name="classification-metrics"></a>Klassificerings mått

Med automatisk ML beräknas prestanda mått för varje klassificerings modell som genereras för experimentet. Dessa mått baseras på scikit-implementeringen. 

Många klassificerings mått definieras för binär klassificering i två klasser och kräver en genomsnitts grad över klasser för att skapa en poäng för klassificering med flera klasser. Scikit – lär dig flera genomsnitts metoder, varav tre av vilka automatiserade ML exponeras: **makro**, **Micro** och **viktad**.

- **Makro** – beräkna måttet för varje klass och ta det ej viktade medelvärdet
- **Micro** -beräkna måttet globalt genom att räkna antalet sanna positiva identifieringar, falska negativa negativa och falska positiva identifieringar (oberoende av klasser).
- **Viktat** – beräkna måttet för varje klass och ta viktat medelvärde baserat på antalet sampel per klass.

Varje genomsnitts metod har sina fördelar, men ett vanligt övervägande när du väljer lämplig metod är klass obalans. Om klasser har olika antal exempel kan det vara mer informativt att använda ett makro genomsnitt, där minoritets klasser får samma viktning till majoritets klasserna. Lär dig mer om [binära vs multiklassing-mått i automatiserad ml](#binary-vs-multiclass-classification-metrics). 

I följande tabell sammanfattas de modell prestanda mått som automatiserade ML beräknar för varje klassificerings modell som genereras för experimentet. Mer information finns i dokumentationen för scikit-lär som är länkad i **beräknings** fältet för varje mått. 

|Metric|Beskrivning|Beräkning|
|--|--|---|
|AUC | AUC är den del av den [egenskaps kurva](#roc-curve)som används för mottagaren.<br><br> **Mål:** Närmare 1 desto bättre <br> **Intervall:** [0, 1]<br> <br>Mått namn som stöds är, <li>`AUC_macro`, det aritmetiska medelvärdet av AUC för varje klass.<li> `AUC_micro`, beräknad genom att kombinera de sanna positiva positiva och falska positiva identifieringar från varje klass. <li> `AUC_weighted`, det aritmetiska medelvärdet av poängen för varje klass, viktat med antalet sanna instanser i varje klass.   |[Beräkning](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.roc_auc_score.html) | 
|accuracy| Noggrannhet är förhållandet mellan förutsägelser som exakt matchar de sanna klass etiketterna. <br> <br>**Mål:** Närmare 1 desto bättre <br> **Intervall:** [0, 1]|[Beräkning](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.accuracy_score.html)|
|average_precision|Genomsnittlig precision sammanfattar en precisions återställnings kurva som viktat medelvärde för de precisioner som uppnås vid varje tröskelvärde, med en ökning av återställningen från föregående tröskel som används som vikt. <br><br> **Mål:** Närmare 1 desto bättre <br> **Intervall:** [0, 1]<br> <br>Mått namn som stöds är,<li>`average_precision_score_macro`, det aritmetiska medelvärdet av den genomsnittliga precisions poängen för varje klass.<li> `average_precision_score_micro`, beräknad genom att kombinera de sanna positiva positiva och falska positiva identifieringar vid varje avgränsare.<li>`average_precision_score_weighted`, det aritmetiska medelvärdet av den genomsnittliga precisions poängen för varje klass, viktat med antalet sanna instanser i varje klass.|[Beräkning](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.average_precision_score.html)|
balanced_accuracy|Balanserade noggrannhet är det aritmetiska medelvärdet för återkallning för varje klass.<br> <br>**Mål:** Närmare 1 desto bättre <br> **Intervall:** [0, 1]|[Beräkning](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|
f1_score|F1-Poäng är det harmoniska medelvärdet av precision och återkallande. Det är ett god balanserat mått av både falska positiva identifieringar och falska negativa negativa. Det tar dock inte hänsyn till sant negativ till kontot. <br> <br>**Mål:** Närmare 1 desto bättre <br> **Intervall:** [0, 1]<br> <br>Mått namn som stöds är,<li>  `f1_score_macro`: det aritmetiska medelvärdet av F1-poängen för varje klass. <li> `f1_score_micro`: beräknas genom att räkna antalet sanna positiva identifieringar, falska negativa negativa data och falska positiva identifieringar. <li> `f1_score_weighted`: viktat medelvärde per klass frekvens av F1-Poäng för varje klass.|[Beräkning](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.f1_score.html)|
log_loss|Detta är den förlust funktion som används i (MULTINOMIAL) Logistisk regression och tillägg, till exempel neurala Networks, definierad som den negativa logg sannolikheten för de faktiska etiketterna med en Probabilistic klassificerares förutsägelser. <br><br> **Mål:** Närmare 0 desto bättre <br> **Intervall:** [0, inf)|[Beräkning](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.log_loss.html)|
norm_macro_recall| Normaliserat makro återkallande är återkalla makro – Genomsnittligt och normaliserat, så att slumpmässiga prestanda har poängen 0, och perfekt prestanda har en poäng på 1. <br> <br>**Mål:** Närmare 1 desto bättre <br> **Intervall:** [0, 1] |`(recall_score_macro - R)`&nbsp;/&nbsp;`(1 - R)` <br><br>där, `R` är det förväntade värdet `recall_score_macro` för för slumpmässiga förutsägelser.<br><br>`R = 0.5`&nbsp;för &nbsp; binär &nbsp; klassificering. <br>`R = (1 / C)` för klassificerings problem i C-klass.|
matthews_correlation | Matthews korrelations koefficient är ett balanserat mått på noggrannhet, som kan användas även om en klass har många fler sampel än en annan. En koefficient på 1 indikerar perfekt förutsägelse, 0 slumpmässig förutsägelse och-1 inverterad förutsägelse.<br><br> **Mål:** Närmare 1 desto bättre <br> **Intervall:** [-1, 1]|[Beräkning](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.matthews_corrcoef.html)|
precision|Precision är möjligheten för en modell att undvika att etikettera negativa prover som positiva. <br><br> **Mål:** Närmare 1 desto bättre <br> **Intervall:** [0, 1]<br> <br>Mått namn som stöds är, <li> `precision_score_macro`, det aritmetiska medelvärdet för varje klass. <li> `precision_score_micro`, beräknad globalt genom att räkna antalet sanna positiva positiva och falska positiva identifieringar. <li> `precision_score_weighted`, det aritmetiska medelvärdet för varje klass, viktat med antalet sanna instanser i varje klass.|[Beräkning](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.precision_score.html)|
träffsäkerhet| Återkallande är möjligheten för en modell att identifiera alla positiva exempel. <br><br> **Mål:** Närmare 1 desto bättre <br> **Intervall:** [0, 1]<br> <br>Mått namn som stöds är, <li>`recall_score_macro`: det aritmetiska medelvärdet för återkallning för varje klass. <li> `recall_score_micro`: beräknas globalt genom att räkna antalet sanna positiva identifieringar, falska negativa negativa och falska positiva identifieringar.<li> `recall_score_weighted`: det aritmetiska medelvärdet för varje klass, viktat med antalet sanna instanser i varje klass.|[Beräkning](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|
weighted_accuracy|Viktad noggrannhet är exakthet där varje prov viktas av det totala antalet prover som tillhör samma klass. <br><br>**Mål:** Närmare 1 desto bättre <br>**Intervall:** [0, 1]|[Beräkning](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.accuracy_score.html)|

### <a name="binary-vs-multiclass-classification-metrics"></a>Klass mått för binära vs.

Automatisk ML särskiljer inte mellan binära och multiklassiga mått. Samma verifierings mått rapporteras om en data uppsättning har två klasser eller fler än två klasser. Vissa mått är dock avsedda för klassificering av flera klasser. När dessa mått används i en binär data uppsättning, kommer dessa mått inte att behandla någon klass som `true` klass, vilket kan förväntas. Mått som är tydligt avsedda för multiklassen är suffix till `micro` , `macro` eller `weighted` . Exempel:,,, `average_precision_score` `f1_score` `precision_score` `recall_score` och `AUC` .

Till exempel, i stället för att beräkna återkalla som, Genomsnittligt `tp / (tp + fn)` återställnings genomsnitt för flera `micro` `macro` `weighted` klasser över båda klasserna av en binär klassificerings data uppsättning. Detta motsvarar att beräkna återkallningen för `true` klassen och `false` klassen separat, och sedan ta medelvärdet av de två.

## <a name="confusion-matrix"></a>Förvirringsmatris

Förvirrings-matriser ger ett visuellt objekt för hur en Machine Learning-modell gör systematiska fel i sina förutsägelser för klassificerings modeller. Ordet "förvirring" i namnet kommer från en modell "förvirrande" eller felmärks exempel. En cell i rad `i` och kolumn `j` i en förvirring mat ris innehåller antalet exempel i utvärderings data uppsättningen som tillhör klassen `C_i` och klassificerats av modellen som klass `C_j` .

I Studio indikerar en mörkare cell ett större antal exempel. Val av **normaliserad** vy i list rutan normaliserar över varje mat ris rad för att Visa procent andelen av klassen som `C_i` är förväntad som klass `C_j` . Fördelen med standardinställd **RAW** -vy är att du kan se om obalans i fördelningen av faktiska klasser orsakade att modellen inte klassificerar exempel från minoritets klassen, ett vanligt problem i obalanserade data uppsättningar.

Förväxlings mat ris för en lämplig modell kommer att ha de flesta exempel längs diagonalen.

### <a name="confusion-matrix-for-a-good-model"></a>Förväxlings mat ris för en lämplig modell 
![Förväxlings mat ris för en lämplig modell ](./media/how-to-understand-automated-ml/chart-confusion-matrix-good.png)

### <a name="confusion-matrix-for-a-bad-model"></a>Förvirring mat ris för en dålig modell
![Förvirring mat ris för en dålig modell](./media/how-to-understand-automated-ml/chart-confusion-matrix-bad.png)

## <a name="roc-curve"></a>ROC kurva

ROC-kurvan ritar relationen mellan sant positiv hastighet (TPR) och falskt positivt pris (på) som besluts tröskel. ROC-kurvan kan vara mindre informativ vid inlärnings modeller på data uppsättningar med hög obalans, eftersom majoriteten klass kan Drown ut bidrag från minoritets klasser.

Ytan under kurvan (AUC) kan tolkas som andel av korrekt klassificerade prover. Mer exakt är AUC sannolikheten att klassificeraren rangordnar ett slumpmässigt valt positivt positivt exempel än ett slumpmässigt valt negativt exempel. Kurvans form ger en intuition för relationen mellan TPR och på som en funktion i klassificerings tröskeln eller besluts gränsen.

En kurva som närmar sig det övre vänstra hörnet i diagrammet närmar sig en 100% TPR och 0% på, den bästa möjliga modellen. En slumpmässig modell skapar en ROC kurva längs `y = x` linjen från det nedre vänstra hörnet till det övre högra hörnet. En sämre modell än den slumpmässiga modellen skulle ha en ROC kurva som är DIP under `y = x` linjen.
> [!TIP]
> För klassificerings experiment kan varje linje diagram som skapas för automatiserade ML-modeller användas för att utvärdera modellen per klass eller genomsnitt för alla klasser. Du kan växla mellan dessa olika vyer genom att klicka på klass etiketter i förklaringen till höger om diagrammet.
### <a name="roc-curve-for-a-good-model"></a>ROC kurva för en lämplig modell
![ROC kurva för en lämplig modell](./media/how-to-understand-automated-ml/chart-roc-curve-good.png)

### <a name="roc-curve-for-a-bad-model"></a>ROC kurva för en dålig modell
![ROC kurva för en dålig modell](./media/how-to-understand-automated-ml/chart-roc-curve-bad.png)

## <a name="precision-recall-curve"></a>Precision – återkalla kurva

Kurvan för precisions åter kallelse ritar relationen mellan precision och återkallande som besluts tröskel ändringar. Återkallande är möjligheten för en modell att identifiera alla positiva prover och precision är möjligheten för en modell att undvika att etikettera negativa prover som positivt. Vissa affärs problem kan kräva högre återkallning och viss högre precision beroende på den relativa betydelsen av att undvika falska negativa eller falska positiva identifieringar.
> [!TIP]
> För klassificerings experiment kan varje linje diagram som skapas för automatiserade ML-modeller användas för att utvärdera modellen per klass eller genomsnitt för alla klasser. Du kan växla mellan dessa olika vyer genom att klicka på klass etiketter i förklaringen till höger om diagrammet.
### <a name="precision-recall-curve-for-a-good-model"></a>Precisions åter kallelse kurva för en lämplig modell
![Precisions åter kallelse kurva för en lämplig modell](./media/how-to-understand-automated-ml/chart-precision-recall-curve-good.png)

### <a name="precision-recall-curve-for-a-bad-model"></a>Precisions åter kallelse kurva för en dålig modell
![Precisions åter kallelse kurva för en dålig modell](./media/how-to-understand-automated-ml/chart-precision-recall-curve-bad.png)

## <a name="cumulative-gains-curve"></a>Kurva för ackumulerade vinster

Kurvan för ackumulerade vinster ritar upp procent andelen positiva prover som klassificeras korrekt som en funktion av procent andelen av exempel som beaktas där vi betraktar exempel i den förväntade sannolikhets ordningen.

Om du vill beräkna vinst måste du först sortera alla exempel från högsta till lägsta sannolikhet som förväntas av modellen. Ta sedan `x%` med de högsta förtroende förutsägelserna. Dividera antalet positiva exempel som identifierats `x%` med det totala antalet positiva exempel för att få vinst. Ackumulerad vinst är den procentuella insamlingen av positiva exempel som vi upptäcker när du överväger vissa procent av de data som troligen är mest sannolika för klassen positiv.

En perfekt modell rangordnar alla positiva prover över alla negativa prover som ger en kurva om ackumulerade vinster av två enkla segment. Den första är en rad med lutning `1 / x` från `(0, 0)` till `(x, 1)` där `x` är fraktionen av exempel som tillhör klassen positiv ( `1 / num_classes` om klasser är balanserade). Den andra är en vågrät linje från `(x, 1)` till `(1, 1)` . I det första segmentet klassificeras alla positiva prover korrekt och den ackumulerade vinsten går till i `100%` de första `x%` av exemplen som beaktas.

Den slumpmässiga modellen för bas linjen har en kurva för ackumulerade vinster `y = x` , efter vilka `x%` prover som endast betraktas som `x%` av de totala positiva exemplen har identifierats. En perfekt modell har en mikrogenomsnittlig kurva som vidrör det övre vänstra hörnet och en genomsnittlig linje för makron som har en lutning `1 / num_classes` tills den ackumulerade vinsten är 100% och sedan vågrätt tills data procenten är 100.
> [!TIP]
> För klassificerings experiment kan varje linje diagram som skapas för automatiserade ML-modeller användas för att utvärdera modellen per klass eller genomsnitt för alla klasser. Du kan växla mellan dessa olika vyer genom att klicka på klass etiketter i förklaringen till höger om diagrammet.
### <a name="cumulative-gains-curve-for-a-good-model"></a>Kumulativa vinster-kurva för en lämplig modell
![Kumulativa vinster-kurva för en lämplig modell](./media/how-to-understand-automated-ml/chart-cumulative-gains-curve-good.png)

### <a name="cumulative-gains-curve-for-a-bad-model"></a>Ackumulerade vinster-kurvor för en dålig modell
![Ackumulerade vinster-kurvor för en dålig modell](./media/how-to-understand-automated-ml/chart-cumulative-gains-curve-bad.png)

## <a name="lift-curve"></a>Lyft kurva

Lyft kurvan visar hur många gånger bättre en modell fungerar jämfört med en slumpmässig modell. Lyft definieras som förhållandet mellan ackumulerad vinst och den ackumulerade vinsten hos en slumpmässig modell.

Den här relativa prestandan tar hänsyn till det faktum att klassificeringen blir svårare när du ökar antalet klasser. (En slumpmässig modell förutsäger felaktigt en större del av exempel från en data uppsättning med 10 klasser jämfört med en data uppsättning med två klasser)

Bas linje lyft kurvan är den `y = 1` linje där modellens prestanda är konsekvent med den slumpmässiga modellen. I allmänhet kommer lyft kurvan för en bra modell att vara högre i diagrammet och längre bort från x-axeln, som visar att när modellen är mest säker i dess förutsägelser, utförs många gånger bättre än slumpmässig gissning.

> [!TIP]
> För klassificerings experiment kan varje linje diagram som skapas för automatiserade ML-modeller användas för att utvärdera modellen per klass eller genomsnitt för alla klasser. Du kan växla mellan dessa olika vyer genom att klicka på klass etiketter i förklaringen till höger om diagrammet.
### <a name="lift-curve-for-a-good-model"></a>Lyft kurva för en lämplig modell
![Lyft kurva för en lämplig modell](./media/how-to-understand-automated-ml/chart-lift-curve-good.png)
 
### <a name="lift-curve-for-a-bad-model"></a>Lyft kurva för en dålig modell
![Lyft kurva för en dålig modell](./media/how-to-understand-automated-ml/chart-lift-curve-bad.png)

## <a name="calibration-curve"></a>Kalibrerings kurva

Kalibrerings kurvan ritar en modells förtroende i dess förutsägelser mot proportionerna av positiva prover vid varje konfidensnivå. En väl kalibrerad modell klassificeras korrekt 100% av de förutsägelser som den tilldelar 100% konfidensgrad, 50% av de förutsägelser som den tilldelar till 50% konfidensgrad, 20% av förutsägelserna tilldelar 20% förtroende och så vidare. En perfekt kalibrerad modell har en kalibrerings kurva som följer på `y = x` linjen där modellen perfekt förutsäger sannolikheten för att exempel tillhör varje klass.

En försäkrad modell är över-predict sannolikheten nära noll och en, vilket sällan är osäker på om klassen för varje exempel och kalibrerings kurvan ser ut ungefär så här: "S". En under-säker modell tilldelar en lägre sannolikhet i genomsnitt till den klass den förutsäger och den tillhör ande kalibrerings kurvan ser ut ungefär som en "S". Kalibrerings kurvan visar inte en modells förmåga att klassificera korrekt, utan i stället möjlighet att tilldela förtroende till sina förutsägelser korrekt. En dålig modell kan fortfarande ha en bra kalibrerings kurva om modellen korrekt tilldelar låg exakthet och hög osäkerhet.

> [!NOTE]
> Kalibrerings kurvan är känslig för antalet prover, så en liten verifierings uppsättning kan producera brus resultat som kan vara svårt att tolka. Detta innebär inte nödvändigt vis att modellen inte är väl kalibrerad.

### <a name="calibration-curve-for-a-good-model"></a>Kalibrerings kurva för en lämplig modell
![Kalibrerings kurva för en lämplig modell](./media/how-to-understand-automated-ml/chart-calibration-curve-good.png)

### <a name="calibration-curve-for-a-bad-model"></a>Kalibrerings kurva för en dålig modell
![Kalibrerings kurva för en dålig modell](./media/how-to-understand-automated-ml/chart-calibration-curve-bad.png)

## <a name="regressionforecasting-metrics"></a>Regressions-/prognos mått

Med automatisk ML beräknas samma prestanda mått för varje modell som genereras, oavsett om det är ett Regressions-eller prognos experiment. Dessa mått genomgår också normalisering för att möjliggöra jämförelse mellan modeller som har tränats på data med olika intervall. Mer information finns i [mått normalisering](#metric-normalization).  

I följande tabell sammanfattas de modell prestanda mått som genereras för Regressions-och prognos experiment. Precis som klassificerings mått är de här måtten också baserade på scikit lär dig implementeringar. Lämplig dokumentation om scikit-information länkas enligt detta i **beräknings** fältet.

|Metric|Beskrivning|Beräkning|
--|--|--|
explained_variance|Förklarad varians mäter i vilken utsträckning ett modell konto för variationen i mål variabeln. Det är den procentuella minskningen av var Ian sen för de ursprungliga data som var fel. När medelvärdet för felen är 0 är det lika med koefficienten för bestämning (se r2_score nedan). <br> <br> **Mål:** Närmare 1 desto bättre <br> **Intervall:** (-inf, 1]|[Beräkning](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.explained_variance_score.html)|
mean_absolute_error|Medelvärde för absolut fel är det förväntade värdet av absolut värde för skillnaden mellan målet och förutsägelsen.<br><br> **Mål:** Närmare 0 desto bättre <br> **Intervall:** [0, inf) <br><br> Nodtyper <br>`mean_absolute_error` <br>  `normalized_mean_absolute_error`, mean_absolute_error dividerat med data intervallet. | [Beräkning](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_absolute_error.html)|
mean_absolute_percentage_error|Medelvärde för absolut procent fel (MAPE) är ett mått på den genomsnittliga skillnaden mellan ett förutsägande värde och det faktiska värdet.<br><br> **Mål:** Närmare 0 desto bättre <br> **Intervall:** [0, inf) ||
median_absolute_error|Median det absoluta felet är median värdet för alla absoluta skillnader mellan målet och förutsägelsen. Den här förlusten är robust för att kunna avvika.<br><br> **Mål:** Närmare 0 desto bättre <br> **Intervall:** [0, inf)<br><br>Nodtyper <br> `median_absolute_error`<br> `normalized_median_absolute_error`: median_absolute_error dividerat med data intervallet. |[Beräkning](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.median_absolute_error.html)|
r2_score|R<sup>2</sup> (koefficienten för bestämning) mäter den proportionella minskningen i ett kvadratvärde i genomsnitt (MSE) i förhållande till den totala var Ian sen för observerade data. <br> <br> **Mål:** Närmare 1 desto bättre <br> **Intervall:** [-1, 1]<br><br>Obs: R<sup>2</sup> har ofta intervallet (-inf, 1]. MSE kan vara större än den observerade var Ians, så R<sup>2</sup> kan ha godtyckligt negativa negativa värden, beroende på data och modell förutsägelserna. Automatiska ML-klipp som rapporter ATS R<sup>2</sup> -resultat på-1, så värdet-1 för r<sup>2</sup> innebär förmodligen att den sanna r<sup>2</sup> -poängen är mindre än-1. Överväg de andra måtten och egenskaperna för data när du tolkar ett negativt R<sup>2</sup> -resultat.|[Beräkning](https://scikit-learn.org/0.16/modules/generated/sklearn.metrics.r2_score.html)|
root_mean_squared_error |Rot genomsnitts fel (RMSE) är kvadratroten ur den förväntade skillnaden i kvadraten mellan målet och förutsägelsen. RMSE är lika med standard avvikelsen för en icke-vägd uppskattning.<br> <br> **Mål:** Närmare 0 desto bättre <br> **Intervall:** [0, inf)<br><br>Nodtyper<br> `root_mean_squared_error` <br> `normalized_root_mean_squared_error`: root_mean_squared_error dividerat med data intervallet. |[Beräkning](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_squared_error.html)|
root_mean_squared_log_error|Rot genomsnitts logg fel i kvadrat är kvadratroten ur det förväntade logaritmiska felet.<br><br>**Mål:** Närmare 0 desto bättre <br> **Intervall:** [0, inf) <br> <br>Nodtyper <br>`root_mean_squared_log_error` <br> `normalized_root_mean_squared_log_error`: root_mean_squared_log_error dividerat med data intervallet.  |[Beräkning](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_squared_log_error.html)|
spearman_correlation| Spearman-korrelationen är en parameter som inte är ett mått på monotonicity för relationen mellan två data uppsättningar. Till skillnad från Pearson-korrelationen förutsätter Spearman-korrelationen inte att båda data uppsättningarna vanligt vis distribueras. Precis som andra korrelations koefficienter varierar Spearman mellan-1 och 1 med 0, vilket innebär att ingen korrelation. Korrelationer på-1 eller 1 innebär en exakt enkel färgs relation. <br><br> Spearman är ett korrelations mått i ranknings ordning som ändras till förväntade eller faktiska värden inte ändrar Spearman-resultatet om de inte ändrar rangordnings ordningen för förväntade eller faktiska värden.<br> <br> **Mål:** Närmare 1 desto bättre <br> **Intervall:** [-1, 1]|[Beräkning](https://docs.scipy.org/doc/scipy-0.16.1/reference/generated/scipy.stats.spearmanr.html)|

### <a name="metric-normalization"></a>Mått normalisering

Med automatiserad ML normaliseras regression och prognostisering av mått som möjliggör en jämförelse mellan modeller som har tränats på data med olika intervall. En modell som har tränats på en data med ett större intervall har högre fel än samma modell som har tränats på data med ett mindre intervall, om inte felet är normaliserat.

Även om det inte finns någon standard metod för att normalisera fel mått, tar automatiserade ML med den gemensamma metoden att dividera felet med data intervallet: `normalized_error = error / (y_max - y_min)`

När du utvärderar en prognos modell för tids serie data, tar automatiserade ML ytterligare steg för att säkerställa att normalisering sker per Time Series ID (kornigt), eftersom varje tids serie sannolikt har en annan fördelning av mål värden.
## <a name="residuals"></a>Residualer

Resten av diagrammet är ett histogram av förutsägelse felen (rester) som genereras för Regressions-och prognos experiment. Rester beräknas som `y_predicted - y_true` för alla exempel och visas sedan som ett histogram för att Visa modell kompensation.

I det här exemplet är det viktigt att båda modellerna är något fördelade för att förutsäga lägre än det faktiska värdet. Detta är inte ovanligt för en data uppsättning med en skevad fördelning av faktiska mål, men visar sämre modell prestanda. En lämplig modell kommer att ha en rest-fördelning som är hög vid noll med få rester på extrema nivå. En sämre modell kommer att ha en utspridande rest-distribution med färre prover runt noll.

### <a name="residuals-chart-for-a-good-model"></a>Resten av diagrammet för en lämplig modell
![Resten av diagrammet för en lämplig modell](./media/how-to-understand-automated-ml/chart-residuals-good.png)

### <a name="residuals-chart-for-a-bad-model"></a>Resten av diagrammet för en dålig modell
![Resten av diagrammet för en dålig modell](./media/how-to-understand-automated-ml/chart-residuals-bad.png)

## <a name="predicted-vs-true"></a>Förutsägs jämfört med sant

För Regressions-och Prognosticering experimenterar det förväntade vs. True-diagrammet relationen mellan mål funktionen (sant/faktiska värden) och modellens förutsägelser. De sanna värdena är diskretiseras längs x-axeln och för varje lager plats ritas medelvärdet för det förväntade värdet med felstaplar. På så sätt kan du se om en modell är prioriterad mot förutsägelse av vissa värden. Raden visar den genomsnittliga förutsägelsen och det skuggade arean visar var Ian sen för förutsägelserna runt det betyder.

Ofta har det vanligaste sanna värdet de mest exakta förutsägelserna med lägst varians. Avståndet i trend linjen från den perfekta `y = x` linjen där det finns några sanna värden är ett bra mått på modell prestanda för avvikande värden. Du kan använda histogrammet längst ned i diagrammet om du vill veta mer om den faktiska data distributionen. Inklusive fler data exempel där fördelningen är sparse kan förbättra modell prestanda på osett-data.

I det här exemplet är det viktigt att den bättre modellen har en förväntad eller sann rad som är närmare den perfekta `y = x` linjen.

### <a name="predicted-vs-true-chart-for-a-good-model"></a>Förutsägande vs. True-diagram för en lämplig modell
![Förutsägande vs. True-diagram för en lämplig modell](./media/how-to-understand-automated-ml/chart-predicted-true-good.png)

### <a name="predicted-vs-true-chart-for-a-bad-model"></a>Förutsägande vs. True-diagram för en dålig modell
![Förutsägande vs. True-diagram för en dålig modell](./media/how-to-understand-automated-ml/chart-predicted-true-bad.png)

## <a name="model-explanations-and-feature-importances"></a>Modell förklaringar och funktions betydelse

Även om modell utvärderings mått och diagram är bra för att mäta den allmänna kvaliteten i en modell, är det viktigt att kontrol lera vilken data uppsättning som en modell som används för att göra sina förutsägelser viktiga vid en eventuell bedömning av AI. Det är anledningen till att den automatiserade ML ger en instrument panel för modell tolkning som mäter och rapporterar de relativa bidragen för data uppsättnings funktioner.

För att Visa tolknings instrument panelen i Studio:
1. [Logga in på Studio](https://ml.azure.com/) och navigera till din arbets yta
2. På den vänstra menyn väljer du **experiment**
3. Välj experimentet i listan över experiment
4. I tabellen längst ned på sidan väljer du en AutoML-körning
5. På fliken **modeller** väljer du **algoritmens namn** för den modell som du vill förklara
6. På fliken **förklaringar** kan du se att en förklaring redan har skapats om modellen var den bästa
7. Om du vill skapa en ny förklaring väljer du **förklara modell** och väljer den fjärrberäkning som du vill beräkna förklaringar för

[Lär dig mer om modell förklaringar i automatiserad ml](how-to-machine-learning-interpretability-automl.md).

> [!NOTE]
> ForecastTCN-modellen stöds för närvarande inte av automatiska ML-förklaringar och andra prognos modeller kan ha begränsad åtkomst till tolknings verktyg.

## <a name="next-steps"></a>Nästa steg
* Prova de [automatiska antecknings exemplen för Machine Learning-modeller](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/explain-model).
* För automatiserade ML-frågor, kontakta askautomatedml@microsoft.com .
