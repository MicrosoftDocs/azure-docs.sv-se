---
title: Vad är automatiserad ML? AutoML
titleSuffix: Azure Machine Learning
description: Lär dig Azure Machine Learning kan generera en modell automatiskt med hjälp av de parametrar och kriterier som du anger.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: cartacioS
ms.author: sacartac
ms.date: 10/27/2020
ms.custom: automl
ms.openlocfilehash: 6f8f775e474b47cbfd5f3b4aca8987a009a7f6e1
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2021
ms.locfileid: "107874143"
---
# <a name="what-is-automated-machine-learning-automl"></a>Vad är automatiserad maskininlärning (AutoML)?

Automatiserad maskininlärning, som även kallas automatiserad ML eller AutoML, är en process för att automatisera de tidskrävande, iterativa uppgifterna vid utveckling av maskininlärningsmodeller. Det gör att dataforskare, analytiker och utvecklare kan skapa ML-modeller med hög skala, effektivitet och produktivitet samtidigt som modellkvaliteten bibehålls. Automatiserad ML Azure Machine Learning baseras på ett banbrytande arbete från [vår Microsoft Research-avdelning.](https://www.microsoft.com/research/project/automl/)

Utveckling av traditionella maskininlärningsmodeller är resurskrävande, vilket kräver betydande domänkunskaper och tid för att producera och jämföra dussintals modeller. Med automatiserad maskininlärning påskyndar du den tid det tar att få produktionsklara ML-modeller med stor enkelhet och effektivitet.

## <a name="automl-in-azure-machine-learning"></a>AutoML i Azure Machine Learning

Azure Machine Learning två funktioner för att arbeta med automatiserad ML:

* För kod erfarna kunder, [Azure Machine Learning Python SDK](/python/api/overview/azure/ml/intro).  Kom igång med [Självstudie: Använda automatiserad maskininlärning för att förutsäga taxiresor.](tutorial-auto-train-models.md)

* För kunder med begränsad/ingen kodupplevelse Azure Machine Learning-studio på [https://ml.azure.com](https://ml.azure.com/) .  Kom igång med de här självstudierna:
    * [Självstudie: Skapa en klassificeringsmodell med automatiserad ML i Azure Machine Learning](tutorial-first-experiment-automated-ml.md).
    *  [Självstudie: Skapa prognoser för efterfrågan med automatiserad maskininlärning](tutorial-automated-ml-forecast.md)


## <a name="when-to-use-automl-classify-regression--forecast"></a>När du ska använda AutoML: klassificera, regression & prognos

Tillämpa automatiserad ML när du Azure Machine Learning att träna och finjustera en modell åt dig med hjälp av det målmått som du anger. Automatiserad ML demokratiserar utvecklingsprocessen för maskininlärningsmodeller och ger användarna, oavsett dataexpertis, möjlighet att identifiera en pipeline för maskininlärning från slutet till slut för alla problem.

Dataforskare, analytiker och utvecklare i olika branscher kan använda automatiserad ML för att:
+ Implementera ML-lösningar utan omfattande programmeringskunskaper
+ Spara tid och resurser
+ Använda metodtips för datavetenskap
+ Tillhandahålla flexibel problemlösning

### <a name="classification"></a>Klassificering

Klassificering är en vanlig maskininlärningsuppgift. Klassificering är en typ av övervakad inlärning där modeller lär sig använda träningsdata och tillämpar dessa lärdomar på nya data. Azure Machine Learning erbjuder funktioner som är specifika för dessa uppgifter, till exempel djupgående neurala nätverkstext-funktioner för klassificering. Läs mer om [alternativ för featurisering.](how-to-configure-auto-features.md#featurization) 

Det huvudsakliga målet med klassificeringsmodeller är att förutsäga vilka kategorier nya data kommer att hamna i baserat på lärdomar från dess träningsdata. Vanliga klassificeringsexempel är bedrägeriidentifiering, handskriftsigenkänning och objektidentifiering. Läs mer och se ett exempel på [Skapa en klassificeringsmodell med automatiserad ML.](tutorial-first-experiment-automated-ml.md)

Se exempel på klassificering och automatiserad maskininlärning i dessa Python-anteckningsböcker: [Bedrägeriidentifiering,](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-credit-card-fraud/auto-ml-classification-credit-card-fraud.ipynb) [marknadsföringsförutsägelse](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-bank-marketing-all-features/auto-ml-classification-bank-marketing-all-features.ipynb)och [dataklassificering för nyhetsgrupper](https://towardsdatascience.com/automated-text-classification-using-machine-learning-3df4f4f9570b)

### <a name="regression"></a>Regression

Precis som med klassificering är regressionsuppgifter också en vanlig övervakad inlärningsuppgift. Azure Machine Learning erbjuder [funktioner som är specifika för dessa uppgifter.](how-to-configure-auto-features.md#featurization)

Regressionsmodeller skiljer sig från klassificering där förutsagda utdatavärden är kategoriska, och regressionsmodeller förutsäger numeriska utdatavärden baserat på oberoende prediktorer. I regression är målet att hjälpa till att upprätta relationen mellan dessa oberoende förutsägelsevariabler genom att uppskatta hur en variabel påverkar de andra. Till exempel bilpriser baserade på funktioner som bränslekostnad, säkerhetsklassificering osv. Läs mer och se ett exempel på [regression med automatiserad maskininlärning.](tutorial-auto-train-models.md)

Se exempel på regression och automatiserad maskininlärning för förutsägelser i dessa Python-anteckningsböcker: [CPU Performance Prediction](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/regression-explanation-featurization/auto-ml-regression-explanation-featurization.ipynb), 

### <a name="time-series-forecasting"></a>Prognostiserade tidsserier

Att skapa prognoser är en viktig del av alla företag, oavsett om det är intäkter, lager, försäljning eller kundbehov. Du kan använda automatiserad ML för att kombinera tekniker och metoder och få en rekommenderad prognos för tidsserier av hög kvalitet. Läs mer i den här i så här: [automatiserad maskininlärning för prognostiserade tidsserier.](how-to-auto-train-forecast.md) 

Ett automatiserat tidsserieexperiment behandlas som ett multivarierat regressionsproblem. Tidigare tidsserievärden "pivoteras" för att bli ytterligare dimensioner för regrediktorn tillsammans med andra prediktorer. Den här metoden, till skillnad från klassiska tidsseriemetoder, har en fördel med att integrera flera sammanhangsberoende variabler och deras relation till varandra under träningen. Automatiserad ML lär sig en enda, men ofta internt förgrenad modell för alla objekt i datamängden och förutsägelser. Mer data är därför tillgängliga för att uppskatta modellparametrar och generalisering för ouppspelade serier blir möjligt.

Konfiguration av avancerad prognostiserade data inkluderar:
* helgdagsidentifiering och spelning
* time-series och DNN-elever (Auto-ARIMA, ForecastTCN)
* stöd för många modeller via gruppering
* korsvalidering för rullande ursprung
* konfigurerbara fördröjningar
* funktioner för rullande fönsteraggregat


Se exempel på regression och automatiserad maskininlärning för förutsägelser i dessa Python-anteckningsböcker: [Sales Forecasting](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-orange-juice-sales/auto-ml-forecasting-orange-juice-sales.ipynb), [Demand Forecasting](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-energy-demand/auto-ml-forecasting-energy-demand.ipynb)och [Beverage Production Forecast](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-beer-remote/auto-ml-forecasting-beer-remote.ipynb).

## <a name="how-automated-ml-works"></a>Så här fungerar automatiserad ML

Under träningen Azure Machine Learning ett antal pipelines parallellt som provar olika algoritmer och parametrar åt dig. Tjänsten itererar genom ML-algoritmer i par med funktionsval, där varje iteration skapar en modell med en träningspoäng. Ju högre poäng, desto bättre anses modellen "passa" dina data.  Den stoppas när den når de avslutningskriterier som definierats i experimentet. 

Med **Azure Machine Learning** kan du utforma och köra dina automatiserade ML-träningsexperiment med följande steg:

1. **Identifiera ml-problemet** som ska lösas: klassificering, prognostiserade data eller regression

1. **Välj om du vill använda Python SDK** eller [studiowebbupplevelsen:](#parity)Lär dig mer om pariteten mellan Python SDK och Studio-webbupplevelsen .

   * För begränsad eller ingen kodupplevelse kan du prova Azure Machine Learning-studio webbupplevelse på [https://ml.azure.com](https://ml.azure.com/)  
   * För Python-utvecklare kan du ta en [Azure Machine Learning Python SDK](how-to-configure-auto-train.md) 
    
1. **Ange källa och format för märkta träningsdata:** Numpy-matriser eller Pandas-dataram

1. **Konfigurera beräkningsmålet för modellträning,** till exempel din lokala [dator, Azure Machine Learning Computes,](how-to-set-up-training-targets.md)virtuella fjärrdatorer eller Azure Databricks .

1. **Konfigurera de** automatiserade maskininlärningsparametrarna som avgör hur många iterationer över olika modeller, inställningar för hyperparametrar, avancerad förbearbetning/funktionering och vilka mått du bör titta på när du fastställer den bästa modellen.  
1. **Skicka träningskörningen.**

1. **Granska resultaten** 

Följande diagram illustrerar den här processen. 
![Automatiserad maskininlärning](./media/concept-automated-ml/automl-concept-diagram2.png)


Du kan också granska den loggade körningsinformationen, [som innehåller mått som](how-to-understand-automated-ml.md) samlats in under körningen. Träningskörningen skapar ett Python-serialiserat objekt `.pkl` (-fil) som innehåller modellen och förbearbetningen av data.

Medan modellskapande är automatiserat kan du också [lära dig hur viktiga eller relevanta funktioner är](how-to-configure-auto-train.md#explain) för de genererade modellerna.



> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE2Xc9t]


## <a name="feature-engineering"></a>Funktionsframställning

Funktionsteknik är en process där domänkunskaper om data används för att skapa funktioner som hjälper ML-algoritmer att lära sig bättre. I Azure Machine Learning används skalnings- och normaliseringstekniker för att underlätta funktionskonstruktion. Tillsammans kallas dessa tekniker och funktionstekniker för funktionalisering.

För automatiserade maskininlärningsexperiment tillämpas funktioner automatiskt, men kan även anpassas baserat på dina data. [Läs mer om vilken featuralisering som ingår.](how-to-configure-auto-features.md#featurization)  

> [!NOTE]
> Automatiserade maskininlärningsfunktionaliseringssteg (funktions normalisering, hantering av saknade data, konvertering av text till numerisk osv.) blir en del av den underliggande modellen. När du använder modellen för förutsägelser tillämpas samma featuriseringssteg som tillämpades under träningen på dina indata automatiskt.

### <a name="automatic-featurization-standard"></a>Automatisk featurisering (standard)

I varje automatiserat maskininlärningsexperiment skalas eller normaliseras dina data automatiskt för att hjälpa algoritmerna att prestera bra. Under modellträningen används någon av följande skalnings- eller normaliseringstekniker för varje modell. Lär dig hur AutoML [hjälper till att förhindra överpassning och obalanserade data](concept-manage-ml-pitfalls.md) i dina modeller.

|&nbsp; & &nbsp; Skalningsbearbetning| Description |
| ------------- | ------------- |
| [StandardScaleWrapper](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.StandardScaler.html)  | Standardisera funktioner genom att ta bort medelvärdet och skalningen till enhetsvariansen  |
| [MinMaxScalar](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.MinMaxScaler.html)  | Transformerar funktioner genom att skala varje funktion efter kolumnens lägsta och högsta  |
| [MaxAbsScaler](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.MaxAbsScaler.html#sklearn.preprocessing.MaxAbsScaler) |Skala varje funktion efter det högsta absoluta värdet |
| [RobustScalar](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.RobustScaler.html) |Skalningsfunktioner efter kvantilintervall |
| [Pca](https://scikit-learn.org/stable/modules/generated/sklearn.decomposition.PCA.html) |Linjär minskning av antalet dimensioner med hjälp av singularvärdesdepositionering av data för att projicera dem till ett lägre dimensionsutrymme |
| [TruncatedSVDWrapper](https://scikit-learn.org/stable/modules/generated/sklearn.decomposition.TruncatedSVD.html) |Den här transformeren utför linjär dimensionalitetsminskning med hjälp av trunkerad singularvärdesdekomposition (SVD). Till skillnad från PCA centreras inte data i den här beräknaren innan singularvärdesdeposition beräknas, vilket innebär att den kan arbeta effektivt med scipy.sparse-matriser |
| [SparseNormalizer](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.Normalizer.html) | Varje urval (det vill säga varje rad i datamatrisen) med minst en komponent som inte är noll skalas om oberoende av andra urval så att dess norm (l1 eller l2) är lika med ett |

### <a name="customize-featurization"></a>Anpassa featurisering

Ytterligare tekniker för funktionstekniker som kodning och transformeringar är också tillgängliga. 

Aktivera den här inställningen med:

+ Azure Machine Learning-studio: Aktivera **automatisk funktioner i** avsnittet **Visa ytterligare konfiguration** med de här [stegen.](how-to-use-automated-ml-for-ml-models.md#customize-featurization)

+ Python SDK: Ange `"feauturization": 'auto' / 'off' / 'FeaturizationConfig'` i [AutoMLConfig-objektet.](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig) Läs mer om [att aktivera featurisering.](how-to-configure-auto-features.md) 

## <a name="ensemble-models"></a><a name="ensemble"></a> Ensemblemodeller

Automatiserad maskininlärning stöder ensemblemodeller, som är aktiverade som standard. Ensembleinlärning förbättrar maskininlärningsresultat och förutsägelseprestanda genom att kombinera flera modeller i stället för att använda enskilda modeller. Ensemble-iterationerna visas som de slutliga iterationerna för körningen. Automatiserad maskininlärning använder både röstnings- och stackningsmetoder för att kombinera modeller:

* **Röstning:** förutsäger baserat på det viktade medelvärdet av förväntade klassan probabilities (för klassificeringsuppgifter) eller förutsagda regressionsmål (för regressionsuppgifter).
* **Stapling:** vid stapling kombineras heterogena modeller och en metamodell tränas baserat på utdata från de enskilda modellerna. De aktuella standardmetamodellerna är LogisticRegression för klassificeringsuppgifter och ElasticNet för regressions-/prognostiserade uppgifter.

[Algoritmen för urval i Caruana ensemble](http://www.niculescu-mizil.org/papers/shotgun.icml04.revised.rev2.pdf) med sorterad ensemble-initiering används för att bestämma vilka modeller som ska användas i ensemblen. På en hög nivå initierar den här algoritmen ensemblen med upp till fem modeller med de bästa enskilda poängen och verifierar att dessa modeller ligger inom 5 % tröskelvärdet för bästa poäng för att undvika en dålig inledande ensemble. För varje ensemble-iteration läggs sedan en ny modell till i den befintliga ensemblen och den resulterande poängen beräknas. Om en ny modell förbättrade den befintliga ensemblepoängen uppdateras ensemblen med den nya modellen.

Se [how-to för att](how-to-configure-auto-train.md#ensemble) ändra standardinställningar för ensemble i automatiserad maskininlärning.

## <a name="guidance-on-local-vs-remote-managed-ml-compute-targets"></a><a name="local-remote"></a>Vägledning om lokala kontra fjärrstyrda ML-beräkningsmål

Webbgränssnittet för automatiserad ML använder alltid ett [fjärrbearbetningsmål](concept-compute-target.md).  Men när du använder Python SDK väljer du antingen en lokal beräkning eller ett fjärrbearbetningsmål för automatisk ML-träning.

* **Lokal beräkning:** Träning sker på din lokala bärbara dator eller vm-beräkning. 
* **Fjärrbearbetning:** Träning sker i Machine Learning beräkningskluster.  

### <a name="choose-compute-target"></a>Välj beräkningsmål
Tänk på följande när du väljer beräkningsmål:

 * Välj en lokal **beräkning:** Om ditt scenario handlar om inledande utforskningar eller demonstrationer med små data och korta träningar (d.v.s. sekunder eller några minuter per underordnad körning), kan träning på din lokala dator vara ett bättre alternativ.  Det finns ingen installationstid, infrastrukturresurserna (din dator eller virtuella dator) är direkt tillgängliga.
 * **Välj ett fjärranslutet** ML-beräkningskluster: Om du tränar med större datamängder som i produktionsträning och skapar modeller som behöver längre träning, ger fjärrbearbetning mycket bättre prestanda från end-to-end eftersom parallelliserar träning över klustrets `AutoML` noder. Vid en fjärrbearbetning lägger starttiden för den interna infrastrukturen till cirka 1,5 minuter per underordnad körning, plus ytterligare minuter för klusterinfrastrukturen om de virtuella datorerna ännu inte är igång.

### <a name="pros-and-cons"></a>För- och nackdelar
Tänk på dessa för- och nackdelar när du väljer att använda lokal kontra fjärrlagring.

|  | Fördelar (fördelar)  |Cons (Conss)  |
|---------|---------|---------|---------|
|**Lokalt beräkningsmål** |  <li> Ingen starttid för miljön   | <li>  Delmängd av funktioner<li>  Det går inte att parallellisera körningar <li> Ännu värre för stora data. <li>Ingen dataströmning under träning <li>  Ingen DNN-baserad featurisering <li> Endast Python SDK |
|**Fjärranslutna ML-beräkningskluster**|  <li> Fullständig uppsättning funktioner <li> Parallellisera underordnade körningar <li>   Stöd för stora data<li>  DNN-baserad featurization <li>  Dynamisk skalbarhet för beräkningskluster på begäran <li> Ingen kodupplevelse (webbgränssnitt) är också tillgängligt  |  <li> Starttid för klusternoder <li> Starttid för varje underordnad körning    |

### <a name="feature-availability"></a>Funktionstillgänglighet 

 Fler funktioner är tillgängliga när du använder fjärrbearbetningen, som du ser i tabellen nedan. 

| Funktion                                                    | Fjärransluten | Lokal | 
|------------------------------------------------------------|--------|-------|
| Dataströmning (stöd för stora data, upp till 100 GB)          | ✓      |       | 
| DNN-MESS-baserad textalisering och utbildning             | ✓      |       |
| Out-of-the-box GPU-stöd (träning och slutsatsledning)        | ✓      |       |
| Stöd för bildklassificering och etikettering                  | ✓      |       |
| Auto-ARIMA- och ForecastTCN-modeller för prognostisering | ✓      |       | 
| Flera körningar/iterationer parallellt                       | ✓      |       |
| Skapa modeller med tolkning i AutoML Studio-webbgränssnittet      | ✓      |       |
| Funktionsanpassning i studiowebbgränssnittet| ✓      |       |
| Finjustering av Hyperparameter i Azure ML                             | ✓      |       |
| Arbetsflödesstöd för Azure ML-pipeline                         | ✓      |       |
| Fortsätt en körning                                             | ✓      |       |
| Prognosticering                                                | ✓      | ✓     |
| Skapa och köra experiment i notebook-datorer                    | ✓      | ✓     |
| Registrera och visualisera experimentets information och mått i användargränssnittet | ✓      | ✓     |
| Dataskyddsräcken                                            | ✓      | ✓     |

## <a name="many-models"></a>Många modeller 

[Lösningsacceleratorn](https://aka.ms/many-models) många modeller (förhandsversion) bygger på Azure Machine Learning och gör att du kan använda automatiserad ML för att träna, använda och hantera hundratals eller till och med tusentals maskininlärningsmodeller.

Att till exempel skapa en __modell för varje instans eller individ__ i följande scenarier kan leda till bättre resultat:

* Förutsäga försäljningen för varje enskild butik
* Förutsägande underhåll för hundratals olje brunnar
* Skräddarsy en upplevelse för enskilda användare.

<a name="parity"></a>

### <a name="experiment-settings"></a>Experimentinställningar 

Med följande inställningar kan du konfigurera ditt automatiserade ML-experiment. 

| |The Python SDK|Studio-webbupplevelsen|
----|:----:|:----:
|**Dela upp data i uppsättningar för träna/validering**| ✓|✓
|**Stöder ML-uppgifter: klassificering, regression och prognostiserade uppgifter**| ✓| ✓
|**Optimerar baserat på primärt mått**| ✓| ✓
|**Stöder Azure ML-beräkning som beräkningsmål** | ✓|✓
|**Konfigurera prognostiserade prognoser, målfördröjningar & rullande fönster**|✓|✓
|**Ange avslutningsvillkor** |✓|✓ 
|**Ange samtidiga iterationer**| ✓|✓
|**Ta bort kolumner**| ✓|✓
|**Blockalgoritmer**|✓|✓
|**Korsvalidering** |✓|✓
|**Stöder träning Azure Databricks kluster**| ✓|
|**Visa funktionsnamn**|✓|
|**Sammanfattning av featuralisering**| ✓|
|**Featuralisering för helgdagar**|✓|
|**Utförliga nivåer för loggfiler**| ✓|

### <a name="model-settings"></a>Modellinställningar

De här inställningarna kan tillämpas på den bästa modellen som ett resultat av ditt automatiserade ML-experiment.

| |The Python SDK|Studio-webbupplevelsen|
|----|:----:|:----:|
|**Bästa modellregistrering, distribution, förklarbarhet**| ✓|✓|
|**Aktivera ensemblemodeller för & i stacken**| ✓|✓|
|**Visa bästa modell baserat på icke-primärt mått**|✓||
|**Aktivera/inaktivera ONNX-modellkompatibilitet**|✓||
|**Testa modellen** | ✓| |

### <a name="run-control-settings"></a>Inställningar för körningskontroll

Med de här inställningarna kan du granska och styra experimentkörningarna och dess underordnade körningar. 

| |The Python SDK|Studio-webbupplevelsen|
|----|:----:|:----:|
|**Körningssammanfattningstabell**| ✓|✓|
|**Avbryt körningar & underordnade körningar**| ✓|✓|
|**Hämta skyddräcken**| ✓|✓|
|**Pausa & återuppta körningar**| ✓| |

<a name="use-with-onnx"></a>

## <a name="automl--onnx"></a>AutoML & ONNX

Med Azure Machine Learning kan du använda automatiserad ML för att skapa en Python-modell och konvertera den till ONNX-formatet. När modellerna har ONNX-format kan de köras på en mängd olika plattformar och enheter. Läs mer om [att accelerera ML-modeller med ONNX](concept-onnx.md).

Se hur du konverterar till ONNX-format [i det här Jupyter Notebook-exemplet](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-bank-marketing-all-features/auto-ml-classification-bank-marketing-all-features.ipynb). Lär dig [vilka algoritmer som stöds i ONNX](how-to-configure-auto-train.md#select-your-experiment-type).

ONNX-körningen stöder också C#, så du kan använda modellen som skapas automatiskt i dina C#-appar utan att behöva omkoda eller någon av de nätverksfördröjningar som REST-slutpunkter introducerar. Läs mer om hur du använder en [AutoML ONNX-modell](./how-to-use-automl-onnx-model-dotnet.md) i ett .NET-program med ML.NET och [inferens av ONNX-modeller med C#-API:et ONNX Runtime.](https://github.com/plaidml/onnxruntime/blob/plaidml/docs/CSharp_API.md) 

## <a name="next-steps"></a>Nästa steg

Det finns flera resurser för att komma igång med AutoML. 

### <a name="tutorials-how-tos"></a>Självstudier/instruktioner
Självstudier är introduktionsexempel på AutoML-scenarier från start till slut.
+ **För en första kodupplevelse följer** du [Självstudie: Träna automatiskt en regressionsmodell med Azure Machine Learning Python SDK.](tutorial-auto-train-models.md)

 + **Om du vill ha lite eller ingen kod kan du** gå till [Självstudie: Skapa automatiserade ML-klassificeringsmodeller med Azure Machine Learning-studio](tutorial-first-experiment-automated-ml.md).

I artiklarna finns mer information om vilka funktioner AutoML erbjuder. Exempel: 

+ Konfigurera inställningarna för automatiska träningsexperiment
    + I Azure Machine Learning-studio du [dessa steg.](how-to-use-automated-ml-for-ml-models.md) 
    + Använd de här stegen [med](how-to-configure-auto-train.md)Python SDK.

+  Lär dig hur du tränar automatiskt med hjälp av tidsseriedata [med de här stegen.](how-to-auto-train-forecast.md)

### <a name="jupyter-notebook-samples"></a>Jupyter Notebook-exempel 

Granska detaljerade kodexempel och användningsfall i GitHub Notebook-lagringsplatsen [för automatiserade maskininlärningsexempel.](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/)

### <a name="python-sdk-reference"></a>Python SDK-referens

Fördjupa dina kunskaper om SDK-designmönster och klassspecifikationer med [AutoML-klassreferensdokumentationen](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig). 

> [!Note]
> Automatiserade maskininlärningsfunktioner är också tillgängliga i andra Microsoft-lösningar [som, ML.NET,](/dotnet/machine-learning/automl-overview) [HDInsight,](../hdinsight/spark/apache-spark-run-machine-learning-automl.md) [Power BI](/power-bi/service-machine-learning-automated) och [SQL Server](https://cloudblogs.microsoft.com/sqlserver/2019/01/09/how-to-automate-machine-learning-on-sql-server-2019-big-data-clusters/)
