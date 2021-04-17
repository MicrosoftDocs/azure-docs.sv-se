---
title: Modelltolkning i Azure Machine Learning (förhandsversion)
titleSuffix: Azure Machine Learning
description: Lär dig hur du & förklarar hur din maskininlärningsmodell gör förutsägelser under träning & inferencing med hjälp Azure Machine Learning Python SDK.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to, responsible-ml
ms.author: mithigpe
author: minthigpen
ms.reviewer: Luis.Quintanilla
ms.date: 02/25/2021
ms.openlocfilehash: c517cf2fc8491d62cf2379c87acd2eaadde8fe15
ms.sourcegitcommit: d3bcd46f71f578ca2fd8ed94c3cdabe1c1e0302d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2021
ms.locfileid: "107576439"
---
# <a name="model-interpretability-in-azure-machine-learning-preview"></a>Modelltolkning i Azure Machine Learning (förhandsversion)


## <a name="model-interpretability-overview"></a>Översikt över modelltolkning

Modelltolkning är avgörande för både dataforskare, granskare och beslutsfattare för att säkerställa efterlevnad av företagets principer, branschstandarder och myndighetsregler:

+ Dataexperter behöver kunna förklara sina modeller för chefer och intressenter så att de kan förstå resultatets värde och noggrannhet. De kräver också tolkning för att felsöka sina modeller och fatta välgrundade beslut om hur de ska förbättras. 

+ Juridiska granskare kräver verktyg för att verifiera modeller med avseende på regelefterlevnad och övervaka hur modellers beslut påverkar människor. 

+ Företagsbeslutsfattarna behöver ha sinnesro genom att kunna tillhandahålla transparens för slutanvändarna. Detta gör att de kan tjäna och upprätthålla förtroende.

Det är viktigt att du kan förklara en maskininlärningsmodell i två huvudfaser i modellutvecklingen:

+ Under träningsfasen kan modelldesigners och utvärderare använda tolkningsutdata från en modell för att verifiera hypoteser och skapa förtroende hos intressenter. De använder också insikterna i modellen för felsökning, validering av modellbeteenden som matchar deras mål och för att kontrollera om modellen är försent eller om modellens egenskaper är funktionslösa.

+ Under inferensfasen kan chefer förstå hur modellen fungerar och hur dess beslut behandlar och påverkar människor i verkligheten eftersom transparensen kring distribuerade modeller gör det möjligt för chefer att förstå hur modellen fungerar när den distribueras. 

## <a name="interpretability-with-azure-machine-learning"></a>Tolkning med Azure Machine Learning

Modelltolkningsklasserna görs tillgängliga via följande SDK-paket: (Lär dig hur du installerar [SDK-paket för Azure Machine Learning](/python/api/overview/azure/ml/install))

* `azureml.interpret`, innehåller funktioner som stöds av Microsoft.

Använd `pip install azureml-interpret` för allmän användning.

## <a name="how-to-interpret-your-model"></a>Så här tolkar du din modell

Med hjälp av klasser och metoder i SDK kan du:
+ Förklara modellförutsägelse genom att generera egenskapsvärden för hela modellen och/eller enskilda datapunkter. 
+ Uppnå modelltolkning på verkliga datamängder i stor skala under träning och slutsatsledning.
+ Använda en instrumentpanel för interaktiv visualisering för att identifiera mönster i data och förklaringar vid träning

I maskininlärning **är funktioner** de datafält som används för att förutsäga en måldatapunkt. För att till exempel förutsäga kreditrisk kan datafält för ålder, kontostorlek och kontoålder användas. I det här fallet är ålder, kontostorlek och kontoålder **funktioner**. Funktionens prioritet visar hur varje datafält påverkade modellens förutsägelser. Ålder kan till exempel användas mycket i förutsägelsen, medan kontostorlek och ålder inte påverkar förutsägelsevärdena avsevärt. Den här processen gör det möjligt för dataexperter att förklara resulterande förutsägelser, så att intressenterna får insyn i vilka funktioner som är viktigast i modellen.

## <a name="supported-interpretability-techniques"></a>Tolkningstekniker som stöds

 `azureml-interpret` använder tolkningstekniker som utvecklats i Interpret-Community , ett [Python-paket](https://github.com/interpretml/interpret-community/)med öppen källkod för att träna tolkningsbara modeller och hjälpa till att förklara svarta AI-system. [Interpret-Community](https://github.com/interpretml/interpret-community/) fungerar som värd för den här SDK:ns förklarare som stöds och stöder för närvarande följande tolkningstekniker:

|Tolkningsteknik|Beskrivning|Typ|
|--|--|--------------------|
|SHAP-trädförklarare| [SHAP:](https://github.com/slundberg/shap)s trädförklarare, som fokuserar på polynomtid snabb SHAP-värdeuppskattningsalgoritm som är specifik för träd och **ensembleer av träd**.|Modellspecifik|
|SHAP Deep Explainer| Baserat på förklaringen från SHAP är Deep Explainer "en algoritm för snabb approximering för SHAP-värden i djupinlärningsmodeller som bygger på en anslutning med Deep BUILD SOM beskrivs i [SHAP NIPS-dokumentet](https://papers.nips.cc/paper/7062-a-unified-approach-to-interpreting-model-predictions). **TensorFlow-modeller** och **Keras-modeller** som använder TensorFlow-backend stöds (det finns även preliminärt stöd för PyTorch)".|Modellspecifik|
|SHAP Linear Explainer| SHAP:s linjära förklaring beräknar SHAP-värden för en **linjär** modell, som eventuellt redovisar korrelationer mellan funktioner.|Modellspecifik|
|SHAP Kernel Explainer| SHAP:s Kernel-förklaring använder en särskilt viktad lokal linjär regression för att skatta SHAP-värden för **alla modeller.**|Modelloberoende|
|Mimic Explainer (globalt surrogat)| Imiterarförklararen baseras på idén att träna globala [surrogatmodeller för att](https://christophm.github.io/interpretable-ml-book/global.html) efterlikna svartbox-modeller. En global surrogatmodell är en inbyggda tolkningsbar modell som tränas att approximera förutsägelserna för en **black box-modell** så korrekt som möjligt. Dataexperter kan tolka surrogatmodellen för att dra slutsatser om black box-modellen. Du kan använda någon av följande tolkningsbara modeller som din surrogatmodell: LightGBM (LGBMExplainableModel), Linear Regression (LinearExplainableModel), Förklarad modell med stochastic Gradient Descent (SGDExplainableModel) och beslutsträd (DecisionTreeExplainableModel).|Modelloberoende|
|PFI (Permutation Feature Importance Explainer)| Permutationsfunktions prioritet är en teknik som används för att förklara klassificerings- och regressionsmodeller som är inspirerade av [Breiman-dokumentet Random Forests](https://www.stat.berkeley.edu/~breiman/randomforest2001.pdf) (se avsnitt 10). På en hög nivå fungerar det genom att slumpmässigt blanda data en funktion i taget för hela datauppsättningen och beräkna hur mycket prestandamåttet av intresse ändras. Ju större ändring, desto viktigare är funktionen. PFI kan förklara det övergripande beteendet **för en underliggande modell,** men förklarar inte enskilda förutsägelser. |Modelloberoende|

Förutom tolkningstekniker som beskrivs ovan stöder vi en annan SHAP-baserad förklaring, som kallas `TabularExplainer` . Beroende på modellen använder `TabularExplainer` en av de SHAP-förklarare som stöds:

* TreeExplainer för alla trädbaserade modeller
* DeepExplainer för DNN-modeller
* LinearExplainer för linjära modeller
* KernelExplainer för alla andra modeller

`TabularExplainer` har också gjort betydande funktions- och prestandaförbättringar över direkta SHAP-förklarare:

* **Sammanfattning av initieringsdatamängden**. I de fall där förklaringshastigheten är viktigast sammanfattar vi initieringsdatamängden och genererar en liten uppsättning representativa urval, vilket påskyndar genereringen av övergripande och enskilda egenskapsvärden.
* **Sampling av utvärderingsdatauppsättningen**. Om användaren skickar en stor uppsättning utvärderingsexempel men faktiskt inte behöver utvärdera alla kan samplingsparametern anges till true för att påskynda beräkningen av övergripande modellförklaringar.

Följande diagram visar den aktuella strukturen för förklarare som stöds.

[![Machine Learning för tolkning](./media/how-to-machine-learning-interpretability/interpretability-architecture.png)](./media/how-to-machine-learning-interpretability/interpretability-architecture.png#lightbox)


## <a name="supported-machine-learning-models"></a>Maskininlärningsmodeller som stöds

`azureml.interpret`SDK-paketet stöder modeller som tränats med följande datamängdsformat:
- `numpy.array`
- `pandas.DataFrame`
- `iml.datatypes.DenseData`
- `scipy.sparse.csr_matrix`

Förklaringsfunktionerna accepterar både modeller och pipelines som indata. Om en modell tillhandahålls måste modellen implementera förutsägelsefunktionen `predict` eller som överensstämmer med `predict_proba` Scikit-konventionen. Om din modell inte stöder detta kan du omsluta modellen i en funktion som genererar samma resultat som eller i Scikit och använda den omslutningsfunktionen med den `predict` `predict_proba` valda förklararen. Om en pipeline anges förutsätter förklaringsfunktionen att det pipelineskript som körs returnerar en förutsägelse. Med den här omslutningstekniken kan du använda modeller som tränas via djupinlärningsramverken `azureml.interpret` PyTorch, TensorFlow och Keras samt klassiska maskininlärningsmodeller.

## <a name="local-and-remote-compute-target"></a>Lokalt och fjärranslutet beräkningsmål

Paketet `azureml.interpret` är utformat för att fungera med både lokala och fjärranslutna beräkningsmål. Om de körs lokalt kontaktar SDK-funktionerna inte några Azure-tjänster. 

Du kan fjärrstyra en förklaring Azure Machine Learning Compute och logga förklaringsinformationen i Azure Machine Learning Run History Service. När den här informationen har loggats är rapporter och visualiseringar från förklaringen tillgängliga på Azure Machine Learning-studio för användaranalys.


## <a name="next-steps"></a>Nästa steg

- Se [how-to for](how-to-machine-learning-interpretability-aml.md) enabling interpretability for models training both locally and on Azure Machine Learning remote compute resources (Aktivera tolkningsbarhet för modeller som tränar både lokalt och Azure Machine Learning fjärrbearbetningsresurser. 
- Lär dig hur du aktiverar [tolkning för automatiserade maskininlärningsmodeller.](how-to-machine-learning-interpretability-automl.md)
- Se [notebook-exempelanteckningsböcker](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/explain-model) för ytterligare scenarier. 
- Om du är intresserad av tolkning för textscenarier kan du läsa [Interpret-text](https://github.com/interpretml/interpret-text), en relaterad lagringsplatsen med öppen källkod till [Interpret-Community](https://github.com/interpretml/interpret-community/)för tolkningstekniker för NLP. `azureml.interpret` -paketet stöder för närvarande inte dessa tekniker, men du kan komma igång med en [exempel-notebook-exempelfil om textklassificering](https://github.com/interpretml/interpret-text/blob/master/notebooks/text_classification/text_classification_classical_text_explainer.ipynb).