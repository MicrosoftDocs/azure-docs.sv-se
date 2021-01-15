---
title: Modell tolkning i Azure Machine Learning (för hands version)
titleSuffix: Azure Machine Learning
description: Lär dig hur du förstår & förklara hur din Machine Learning-modell gör förutsägelser under utbildningen & inferencing med Azure Machine Learning python SDK.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to, responsible-ml
ms.author: mithigpe
author: minthigpen
ms.reviewer: Luis.Quintanilla
ms.date: 11/16/2020
ms.openlocfilehash: 6784361dde67d7dcc1423d9edbcc92ec513ff6d4
ms.sourcegitcommit: d59abc5bfad604909a107d05c5dc1b9a193214a8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/14/2021
ms.locfileid: "98222640"
---
# <a name="model-interpretability-in-azure-machine-learning-preview"></a>Modell tolkning i Azure Machine Learning (för hands version)


## <a name="overview-of-model-interpretability"></a>Översikt över modell tolkning

Tolkning är avgörande för data forskare, revisorer och affärs besluts fattare som är likadana för att säkerställa efterlevnaden av företagets principer, bransch standarder och myndighets bestämmelser:

+ Data forskare behöver kunna förklara sina modeller för chefer och intressenter, så att de kan förstå värdet och noggrannheten i sina resultat. De behöver också kunna tolkas för att felsöka sina modeller och fatta välgrundade beslut om hur de kan förbättras. 

+ Juridiska granskare kräver verktyg för att validera modeller med avseende på regelefterlevnad och övervaka hur modellernas beslut påverkar människa. 

+ Affärs besluts fattare behöver tryggare genom att kunna tillhandahålla insyn för slutanvändare. Detta gör att de kan tjäna och upprätthålla förtroende.


Att göra det möjligt att förklara en maskin inlärnings modell är viktigt under två huvud faser av modell utveckling:
+ Under övnings fasen kan modell designers och utvärderare använda tolknings resultat för en modell för att verifiera Hypotheses och bygga förtroende med intressenter. De kan också använda insikter i modellen för fel sökning, verifiera modell beteendet överensstämmer med deras mål och för att kontrol lera om det finns någon modell som är rättvis eller obetydlig.

+ I inferencing-fasen, som har genomskinlighet kring distribuerade modeller, ger chefer möjlighet att förstå "när de distribueras", hur modellen fungerar och hur dess beslut behandlas och påverkar personer i real tid. 

## <a name="interpretability-with-azure-machine-learning"></a>Tolkning med Azure Machine Learning

Tolknings klasser görs tillgängliga via följande SDK-paket: (Lär dig hur du [installerar SDK-paket för Azure Machine Learning](/python/api/overview/azure/ml/install?preserve-view=true&view=azure-ml-py))

* `azureml.interpret`, innehåller funktioner som stöds av Microsoft.

Används `pip install azureml-interpret` för allmän användning.

## <a name="how-to-interpret-your-model"></a>Så här tolkar du din modell

Med hjälp av klasserna och metoderna i SDK kan du:
+ Förklara modell förutsägelsen genom att generera funktions prioritets värden för hela modellen och/eller enskilda datapoints. 
+ Uppnå modell tolkning på verkliga data uppsättningar i stor skala, under utbildning och härledning.
+ Använd en interaktiv instrument panel för visualisering för att identifiera mönster i data och förklaringar i utbildnings tid


I Machine Learning är **funktioner** de data fält som används för att förutsäga en mål data punkt. För att förutsäga kredit risken kan exempelvis data fält för ålder, konto storlek och konto ålder användas. I det här fallet är ålder, konto storlek och konto ålder **funktioner**. Funktions prioriteten visar hur varje data fält påverkar modellens förutsägelser. Till exempel kan ålder användas kraftigt i förutsägelsen när kontots storlek och ålder inte påverkar förutsägelserna avsevärt. Den här processen gör det möjligt för data experter att förklara de resulterande förutsägelserna, så att intressenterna har insyn i vilka funktioner som är viktigast i modellen.

Lär dig mer om tolknings tekniker som stöds, maskin inlärnings modeller som stöds och vilka körnings miljöer som stöds här.


## <a name="supported-interpretability-techniques"></a>Tolknings tekniker som stöds

 `azureml-interpret` använder tolknings tekniker som utvecklats i [tolkning – community](https://github.com/interpretml/interpret-community/), ett python-paket med öppen källkod för inlärnings bara modeller och hjälper till att förklara blackbox AI-system. [Tolkning – community](https://github.com/interpretml/interpret-community/) fungerar som värd för den här SDK: s support förklaringar och stöder för närvarande följande tolknings tekniker:

|Tolknings teknik|Beskrivning|Typ|
|--|--|--------------------|
|SHAP Tree-förklaring| [SHAP](https://github.com/slundberg/shap): s träd förklaring, som fokuserar på polynomed Time fast SHAP för värde uppskattning som är speciell för träd **och ensembler för träd**.|Modell-/regionsspecifika|
|SHAP djup förklaring| Baserat på förklaringen från SHAP är djupgående förklaring en algoritm för hög hastighet för SHAP värden i djup inlärnings modeller som bygger på en anslutning med DeepLIFT som beskrivs i [SHAP Nips-papper](https://papers.nips.cc/paper/7062-a-unified-approach-to-interpreting-model-predictions). **TensorFlow** -modeller och **keras** -modeller med TensorFlow-backend stöds (det finns även stöd för PyTorch) ".|Modell-/regionsspecifika|
|SHAP linjär förklara| SHAP är en linjär uppskattning som beräknar SHAP-värden för en **linjär modell**, vilket kan vara att redovisa mellan funktions korrelationer.|Modell-/regionsspecifika|
|SHAP kernel-förklaring| SHAPs kernel-förklaring använder en särskilt viktad lokal linjär regression för att beräkna SHAP-värden för **alla modeller**.|Modell – oberoende|
|Imitera förklaring (globalt surrogat)| Härma förklarar vad som är baserat på idén med [globala surrogat modeller](https://christophm.github.io/interpretable-ml-book/global.html) för att efterlikna blackbox-modeller. En global surrogat modell är en modell med en inbyggd tolkning som är utbildad för att approximera förutsägelserna för **en svart Box-modell** så exakt som möjligt. Data forskare kan tolka surrogat modellen för att rita slut satser om den svarta Box-modellen. Du kan använda någon av följande tolknings bara modeller som surrogat modell: LightGBM (LGBMExplainableModel), linjär regression (LinearExplainableModel), Stochastic gradient brantaste-förklarande modell (SGDExplainableModel) och besluts träd (DecisionTreeExplainableModel).|Modell – oberoende|
|Förklaring av permutations-funktions prioritet (PFI)| Permutations funktionens betydelse är en teknik som används för att förklara klassificerings-och Regressions modeller som inspireras av [Breiman-bladet för slumpmässiga skogar](https://www.stat.berkeley.edu/~breiman/randomforest2001.pdf) (se avsnitt 10). På en hög nivå är det sättet som det fungerar genom att slumpmässigt blandning data en funktion i taget för hela data uppsättningen och att beräkna hur mycket prestanda måtten för räntan förändras. Ju större ändringen är, desto viktigare är funktionen. PFI kan förklara det övergripande beteendet för **en underliggande modell** men förklarar inte enskilda förutsägelser. |Modell – oberoende|




Förutom de tolknings tekniker som beskrivs ovan har vi stöd för en annan SHAP-baserad förklaring, som kallas `TabularExplainer` . Beroende på modellen `TabularExplainer` använder en av de SHAP-förklaringar som stöds:

* TreeExplainer för alla trädbaserade modeller
* DeepExplainer för DNN-modeller
* LinearExplainer för linjära modeller
* KernelExplainer för alla andra modeller

`TabularExplainer` har också gjort betydande förbättringar av funktioner och prestanda i de direkta SHAP-förklaringarna:

* **Sammanfattning av initierings data uppsättningen**. I de fall då förklaringen är viktigast sammanfattar vi initierings data uppsättningen och genererar en liten uppsättning representativa exempel, vilket påskyndar skapandet av övergripande och enskilda funktions prioritets värden.
* **Sampling av utvärderings data uppsättningen**. Om användaren går igenom en stor uppsättning utvärderings exempel men inte behöver alla dem för att utvärderas, kan exempel parametern anges till sant för att påskynda beräkningen av övergripande modell förklaringar.

Följande diagram visar den aktuella strukturen för förklaringar som stöds.

[![Machine Learning tolknings arkitektur](./media/how-to-machine-learning-interpretability/interpretability-architecture.png)](./media/how-to-machine-learning-interpretability/interpretability-architecture.png#lightbox)


## <a name="supported-machine-learning-models"></a>Maskin inlärnings modeller som stöds

`azureml.interpret`Paketet för SDK har stöd för modeller som har tränats med följande data uppsättnings format:
- `numpy.array`
- `pandas.DataFrame`
- `iml.datatypes.DenseData`
- `scipy.sparse.csr_matrix`

Förklarings funktionerna accepterar både modeller och pipeliner som inmatade. Om en modell anges måste modellen implementera förutsägelse funktionen `predict` eller som följer Scikit- `predict_proba` konventionen. Om din modell inte stöder detta kan du omsluta din modell i en funktion som genererar samma resultat som `predict` eller `predict_proba` i Scikit och använder den omslutnings funktionen med den valda förklaringen. Om en pipeline anges förutsätter förklarings funktionen att det pågående pipeline-skriptet returnerar en förutsägelse. Med den här metoden `azureml.interpret` kan du använda modeller som har tränats via PyTorch, TensorFlow och keras djup inlärnings modeller samt klassiska maskin inlärnings modeller.

## <a name="local-and-remote-compute-target"></a>Lokalt och fjärrstyrt beräknings mål

`azureml.interpret`Paketet är utformat för att fungera med både lokala och fjärranslutna beräknings mål. Om kör lokalt kontaktar inte SDK-funktionerna några Azure-tjänster. 

Du kan köra förklaringen via fjärr anslutning på Azure Machine Learning beräkna och logga förklarings informationen i Azure Machine Learning körnings historik tjänsten. När den här informationen har loggats finns rapporter och visualiseringar från förklaringen enkelt i Azure Machine Learning Studio för användar analys.


## <a name="next-steps"></a>Nästa steg

- Se [anvisningar för att](how-to-machine-learning-interpretability-aml.md) aktivera tolkning för modell utbildning både lokalt och på Azure Machine Learning fjärrstyrda beräknings resurser. 
- Se [exempel antecknings böcker](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/explain-model) för ytterligare scenarier. 
- Om du är intresse rad av att tolka text scenarier, se [tolka text](https://github.com/interpretml/interpret-text), en relaterad öppen källkod lagrings platsen till [tolkning – community](https://github.com/interpretml/interpret-community/), för tolknings tekniker för NLP. `azureml.interpret` paketet stöder för närvarande inte dessa tekniker, men du kan komma igång med en [exempel antecknings bok i text klassificeringen](https://github.com/interpretml/interpret-text/blob/master/notebooks/text_classification/text_classification_classical_text_explainer.ipynb).