---
title: Vad är automatisk ML? AutoML
titleSuffix: Azure Machine Learning
description: Lär dig hur Azure Machine Learning automatiskt kan generera en modell med hjälp av de parametrar och kriterier som du anger.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: cartacioS
ms.author: sacartac
ms.date: 10/27/2020
ms.custom: automl
ms.openlocfilehash: e7038a4e06a96ac2df1a7f0918877db9c97cb71d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102502543"
---
# <a name="what-is-automated-machine-learning-automl"></a>Vad är automatisk maskin inlärning (AutoML)?

Automatisk maskin inlärning, som också kallas automatisk ML eller AutoML, är en process för att automatisera tids krävande, repetitiva uppgifter om utveckling av maskin inlärnings modeller. Det gör det möjligt för data experter, analytiker och utvecklare att bygga ML-modeller med hög skalbarhet, effektivitet och produktivitet samtidigt som modell kvaliteten försämras. Automatiserad ML i Azure Machine Learning baseras på en Ban brytande från vår [Microsoft Research-avdelning](https://www.microsoft.com/research/project/automl/).

Traditionell miljö utveckling för maskin inlärning är resurs intensiv, vilket kräver betydande domän kunskap och tid för att producera och jämföra dussin tals modeller. Med automatisk maskin inlärning kan du påskynda den tid det tar att få produktions klara ML-modeller med mycket enkelt och effektivt.

## <a name="when-to-use-automl-classify-regression--forecast"></a>När du ska använda AutoML: klassificera, regression, & prognos

Använd automatisk ML när du vill Azure Machine Learning för att träna och finjustera en modell för att använda det målmått du anger. Automatiserad ML demokratiserar sakernas utvecklings processen för maskin inlärning och ger användarna möjlighet att, oavsett data vetenskaps expert, identifiera en pipeline för maskin inlärning från slut punkt till slut punkt för alla problem.

Data experter, analytiker och utvecklare i olika branscher kan använda automatisk ML för att:
+ Implementera ML-lösningar utan omfattande programmerings kunskap
+ Spara tid och resurser
+ Utnyttja metod tips för data vetenskap
+ Ge smidig problemlösning

### <a name="classification"></a>Klassificering

Klassificering är en vanlig maskin inlärnings uppgift. Klassificering är en typ av övervakad inlärning där modeller lär sig använda tränings data och tillämpa dem på nya data. Azure Machine Learning erbjuder featurizations specifikt för dessa uppgifter, till exempel djup neurala Network text featurizers för klassificering. Läs mer om [funktionalisering-alternativ](how-to-configure-auto-features.md#featurization). 

Det huvudsakliga målet med klassificerings modeller är att förutsäga vilka kategorier nya data kommer att ingå i baserat på inlärnings data. Vanliga klassificerings exempel är bedrägeri identifiering, hand SKRIFTS igenkänning och objekt identifiering. Läs mer och se ett exempel på hur du [skapar en klassificerings modell med automatiserad ml](tutorial-first-experiment-automated-ml.md).

Se exempel på klassificering och automatisk maskin inlärning i de här python-anteckningarna: [bedrägeri identifiering](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-credit-card-fraud/auto-ml-classification-credit-card-fraud.ipynb), [marknads förutsägelse](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-bank-marketing-all-features/auto-ml-classification-bank-marketing-all-features.ipynb)och [diskussions grupps data klassificering](https://towardsdatascience.com/automated-text-classification-using-machine-learning-3df4f4f9570b)

### <a name="regression"></a>Regression

Regressions aktiviteter liknar klassificeringen och är också en gemensam övervakad utbildnings uppgift. Azure Machine Learning erbjuder [featurizations specifikt för dessa uppgifter](how-to-configure-auto-features.md#featurization).

Det skiljer sig från klassificeringen där förutsägande utdatakolumner är kategoriska. Regressions modeller förutsäger numeriska utmatnings värden baserat på oberoende förförutsägelser. Syftet med regressionen är att hjälpa till att upprätta relationen mellan de oberoende förutsägande variablerna genom att uppskatta hur en variabel påverkar de andra. Till exempel, bil pris baserat på funktioner som gas mil, säkerhets klassificering osv. Läs mer och se ett exempel på [regression med automatiserad maskin inlärning](tutorial-auto-train-models.md).

Se exempel på regression och automatiserad maskin inlärning för förutsägelser i dessa python-anteckningsböcker: [CPU-prestanda förutsägelse](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/regression-explanation-featurization/auto-ml-regression-explanation-featurization.ipynb), 

### <a name="time-series-forecasting"></a>Prognoser för tids serier

Att skapa prognoser är en viktig del av alla verksamheter, vare sig det gäller intäkter, inventering, försäljning eller kund efter frågan. Du kan använda automatiserad ML för att kombinera teknik och metoder och få en rekommenderad prognos för tids serier med hög kvalitet. Lär dig mer med den här instruktionen: [Automatisk maskin inlärning för tids serie prognoser](how-to-auto-train-forecast.md). 

Ett automatiserat experiment med tids serier behandlas som ett multivarierad Regressions problem. Tidigare tids serie värden är "pivoterade" för att bli ytterligare dimensioner för modellerings regressor tillsammans med andra förutsägelser. Den här metoden, till skillnad från klassiska Time Series-metoder, har en fördel med att använda flera sammanhangsbaserade variabler och deras relation till varandra under utbildningen. Med automatisk ML får du en enda, men ofta ingrenad modell för alla objekt i data uppsättningen och förutsägelserna. Mer data är därför tillgängliga för att uppskatta modell parametrar och generalisering till osett-serien blir möjlig.

Konfiguration av avancerad prognos omfattar:
* jul avkänning och funktionalisering
* Time-Series och DNN-elever (Auto-ARIMA, Prophet, ForecastTCN)
* många modeller stöder gruppering
* kors validering av rullande ursprung
* konfigurerbar lags
* mängd funktioner för rullande fönster


Se exempel på regression och automatiserad maskin inlärning för förutsägelser i de här python-antecknings böckerna: [försäljnings prognoser](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-orange-juice-sales/auto-ml-forecasting-orange-juice-sales.ipynb), [prognoser för efter frågan](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-energy-demand/auto-ml-forecasting-energy-demand.ipynb)och [dryckens produktions prognos](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-beer-remote/auto-ml-forecasting-beer-remote.ipynb).

## <a name="how-automated-ml-works"></a>Så här fungerar automatisk ML

Under utbildningen skapar Azure Machine Learning ett antal pipelines parallellt som testar olika algoritmer och parametrar. Tjänsten itererar genom ML-algoritmer med funktions val där varje iteration genererar en modell med en utbildnings poäng. Ju högre poäng, desto bättre blir modellen att "passa" dina data.  Det stoppas när det träffar de slut kriterier som definierats i experimentet. 

Med hjälp av **Azure Machine Learning** kan du utforma och köra dina AUTOMATISERAde ml-experiment med följande steg:

1. **Identifiera det ml-problem** som ska lösas: klassificering, Prognosticering eller regression

1. **Välj om du vill använda python SDK eller Studio Web Experience**: Lär dig mer om pariteten mellan [webb upplevelsen python SDK och Studio](#parity).

   * För begränsad eller ingen kod upplevelse kan du prova Azure Machine Learning Studio-webbupplevelsen på [https://ml.azure.com](https://ml.azure.com/)  
   * För python-utvecklare kan du ta en titt på [Azure Machine Learning python SDK](how-to-configure-auto-train.md) 
    
1. **Ange källa och format för etiketten utbildnings data**: numpy-matriser eller Pandas-dataframe

1. **Konfigurera beräknings målet för modell träning**, till exempel din [lokala dator, Azure Machine Learning beräkningar, fjärranslutna virtuella datorer eller Azure Databricks](how-to-set-up-training-targets.md).  Lär dig mer om automatisk utbildning [på en fjär resurs](how-to-auto-train-remote.md).

1. **Konfigurera de automatiserade Machine Learning-parametrarna** som avgör hur många iterationer över olika modeller, inställningar för funktionalisering, avancerade förbearbetnings-/och vilka mått som ska visas när du bestämmer den bästa modellen.  
1. **Skicka in utbildnings körningen.**

1. **Granska resultaten** 

Följande diagram illustrerar den här processen. 
![Automatisk maskin inlärning](./media/concept-automated-ml/automl-concept-diagram2.png)


Du kan också kontrol lera den loggade körnings informationen, som [innehåller mått](how-to-understand-automated-ml.md) som samlats in under körningen. Inlärnings körningen genererar ett serialiserat objekt ( `.pkl` fil) som innehåller modellen och data förbearbetningen.

När modell byggnaden automatiseras, kan du också [lära dig hur viktiga eller relevanta funktioner är](how-to-configure-auto-train.md#explain) i de genererade modellerna.

Lär dig hur du använder ett [fjärrberäknings mål](how-to-auto-train-remote.md).



> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE2Xc9t]


## <a name="feature-engineering"></a>Funktionsframställning

Funktions teknik är en process där du använder domän information om data för att skapa funktioner som hjälper ML-algoritmer att lära sig bättre. I Azure Machine Learning används skalnings-och normaliserings tekniker för att under lätta funktions teknikerna. De här teknikerna och funktions teknikerna kallas gemensamt för funktionalisering.

För automatiserade maskin inlärnings experiment används funktionalisering automatiskt, men kan också anpassas baserat på dina data. [Läs mer om vad funktionalisering ingår](how-to-configure-auto-features.md#featurization).  

> [!NOTE]
> Automatiserade funktionalisering-steg för Machine Learning (funktions normalisering, hantering av data som saknas, konvertering av text till tal osv.) blir en del av den underliggande modellen. När du använder modellen för förutsägelser tillämpas samma funktionalisering-steg som tillämpades under träningen på dina indata automatiskt.

### <a name="automatic-featurization-standard"></a>Automatisk funktionalisering (standard)

I varje automatiserad maskin inlärnings experiment skalas dina data automatiskt eller normaliseras för att hjälpa algoritmerna att fungera bra. I modell utbildningen används en av följande skalnings-eller normaliserings tekniker för varje modell. Lär dig hur AutoML bidrar till [att förhindra överanpassning och obalanserade data](concept-manage-ml-pitfalls.md) i dina modeller.

|Skalnings &nbsp; & &nbsp; bearbetning| Beskrivning |
| ------------- | ------------- |
| [StandardScaleWrapper](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.StandardScaler.html)  | Standardisera funktioner genom att ta bort medelvärdet och skalan till enhets avvikelse  |
| [MinMaxScalar](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.MinMaxScaler.html)  | Transformerar funktioner genom att skala varje funktion efter minsta och högsta värde för kolumnen  |
| [MaxAbsScaler](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.MaxAbsScaler.html#sklearn.preprocessing.MaxAbsScaler) |Skala varje funktion efter dess maximala absoluta värde |
| [RobustScalar](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.RobustScaler.html) |De här skalnings funktionerna efter deras quantile-intervall |
| [PCA](https://scikit-learn.org/stable/modules/generated/sklearn.decomposition.PCA.html) |Linjär linjär reducering med enkel värdes nedbrytning av data för att projicera den till ett lägre dimensionellt utrymme |
| [TruncatedSVDWrapper](https://scikit-learn.org/stable/modules/generated/sklearn.decomposition.TruncatedSVD.html) |Den här transformeringen utför linjär minskning genom trunkering av sammansatta värde diskompositioner (SVD). I motsats till PCA centrerar denna uppskattning inte data innan du beräknar ett uppdelnings värde för en enkel värde, vilket innebär att det fungerar med scipy. glesa matriser effektivt |
| [SparseNormalizer](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.Normalizer.html) | Varje exempel (det vill säga varje rad i data matrisen) med minst en komponent som inte är noll skalas om oberoende av andra prover så att dess norm (L1 eller L2) är lika med ett |

### <a name="customize-featurization"></a>Anpassa funktionalisering

Ytterligare funktioner teknik tekniker som, kodning och transformeringar är också tillgängliga. 

Aktivera den här inställningen med:

+ Azure Machine Learning Studio: aktivera **Automatisk funktionalisering** i avsnittet **Visa ytterligare konfiguration** [med de här stegen](how-to-use-automated-ml-for-ml-models.md#customize-featurization).

+ Python SDK: ange `"feauturization": 'auto' / 'off' / 'FeaturizationConfig'` i [AutoMLConfig](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig) -objektet. Läs mer om hur du [aktiverar funktionalisering](how-to-configure-auto-features.md). 

## <a name="ensemble-models"></a><a name="ensemble"></a> Ensemble-modeller

Automatisk Machine Learning stöder Ensemble-modeller som är aktiverade som standard. Ensemble-inlärningen förbättrar maskin inlärnings resultatet och förutsäger prestanda genom att kombinera flera modeller i stället för att använda enskilda modeller. Ensemble-iterationerna visas som de slutliga iterationerna av din körning. Automatiserad Machine Learning använder både röstnings-och stack-Ensemble-metoder för att kombinera modeller:

* **Röstning**: förutsäger baserat på viktat medelvärde för förutsebara klass sannolikheter (för klassificerings aktiviteter) eller förutsägande Regressions mål (för Regressions aktiviteter).
* **Stackning**: stackning kombinerar heterogena modeller och tågen en meta-modell som baseras på utdata från de enskilda modellerna. De aktuella standard-meta-modellerna är LogisticRegression för klassificerings uppgifter och ElasticNet för Regressions-/prognos uppgifter.

[Caruana Ensemble-algoritmen](http://www.niculescu-mizil.org/papers/shotgun.icml04.revised.rev2.pdf) med sorterad Ensemble-initiering används för att bestämma vilka modeller som ska användas i ensemblen. På hög nivå initierar den här algoritmen ensemblen med upp till fem modeller med de bästa enskilda poängen, och kontrollerar att dessa modeller är inom tröskelvärdet 5% för att undvika en dålig inledande ensemble. För varje Ensemble-iteration läggs en ny modell till i den befintliga ensemblen och den resulterande poängen beräknas. Om en ny modell förbättrar den befintliga Ensemble-poängen uppdateras ensemblen för att inkludera den nya modellen.

Se [hur du](how-to-configure-auto-train.md#ensemble) ändrar standardinställningar för ensemble i automatiserad maskin inlärning.

## <a name="guidance-on-local-vs-remote-managed-ml-compute-targets"></a><a name="local-remote"></a>Vägledning om lokala kontra fjärrstyrda ML-Compute-mål

Webb gränssnittet för automatisk ML använder alltid ett [fjärrberäknings mål](concept-compute-target.md).  Men när du använder python SDK väljer du antingen en lokal beräkning eller ett fjärrberäknings mål för automatisk ML-utbildning.

* **Lokal beräkning**: träning sker på din lokala bärbara eller VM-beräkning. 
* **Fjärrberäkning**: utbildning sker på Machine Learning beräknings kluster.  

### <a name="choose-compute-target"></a>Välj Compute Target
Tänk på följande faktorer när du väljer Compute-målet:

 * **Välj en lokal beräkning**: om ditt scenario är om inledande utforskningar eller demonstrationer som använder små data och korta tåg (t. ex. sekunder eller ett par minuter per underordnad körning) kan utbildning på din lokala dator vara ett bättre alternativ.  Det finns ingen konfigurations tid, infrastruktur resurserna (din dator eller VM) är direkt tillgängliga.
 * **Välj ett fjärran slutet ml-beräknings kluster**: om du tränar med större data uppsättningar som i produktions träning skapar modeller som behöver längre tåg, ger fjärrberäkningen mycket bättre prestanda för slut punkt till slut punkt eftersom `AutoML` kommer att parallellisera tågen över klustrets noder. Vid en fjärrberäkning kommer start tiden för den interna infrastrukturen att läggas till cirka 1,5 minuter per underordnad körning, plus ytterligare minuter för kluster infrastrukturen om de virtuella datorerna inte är igång ännu.

### <a name="pros-and-cons"></a>-Och nack delar
Överväg dessa tekniker och nack delar när du väljer att använda lokala kontra fjärranslutna.

|  | -Proffs (fördelar)  |Nack delar (handikapp)  |
|---------|---------|---------|---------|
|**Lokalt beräknings mål** |  <li> Ingen miljö start tid   | <li>  Delmängd av funktioner<li>  Det går inte att parallellisera körningar <li> Sämre för stora data. <li>Ingen data strömning vid utbildning <li>  Ingen DNN-baserad funktionalisering <li> Endast python SDK |
|**Fjärran slutet beräknings kluster**|  <li> Fullständig uppsättning funktioner <li> Parallellisera underordnade körningar <li>   Stöd för stora data<li>  DNN-baserad funktionalisering <li>  Dynamisk skalbarhet för beräknings kluster på begäran <li> Ingen kod upplevelse (webb gränssnitt) är också tillgänglig  |  <li> Start tid för klusternoder <li> Start tid för varje underordnad körning    |

### <a name="feature-availability"></a>Funktionstillgänglighet 

 Fler funktioner är tillgängliga när du använder fjärrberäkningen, som du ser i tabellen nedan. 

| Funktion                                                    | Fjärransluten | Lokal | 
|------------------------------------------------------------|--------|-------|
| Data strömning (stöd för stora data, upp till 100 GB)          | ✓      |       | 
| DNN – BERT text funktionalisering and Training             | ✓      |       |
| Föråldrad GPU-support (utbildning och härledning)        | ✓      |       |
| Stöd för bild klassificering och etikettering                  | ✓      |       |
| ARIMA, Prophet och ForecastTCN modeller för Prognosticering | ✓      |       | 
| Flera körningar/iterationer parallellt                       | ✓      |       |
| Skapa modeller med tolknings möjligheter i AutoML Studio Web Experience UI      | ✓      |       |
| Anpassning av funktions teknik i gränssnittet för Studio Web Experience| ✓      |       |
| Justering av Azure ML-min parameter                             | ✓      |       |
| Support för Azure ML pipeline-arbetsflöde                         | ✓      |       |
| Fortsätt en körning                                             | ✓      |       |
| Prognosticering                                                | ✓      | ✓     |
| Skapa och köra experiment i antecknings böcker                    | ✓      | ✓     |
| Registrera och visualisera experimentets information och mått i användar gränssnittet | ✓      | ✓     |
| Data guardrails                                            | ✓      | ✓     |

## <a name="many-models"></a>Många modeller 

[Många modeller Solution Accelerator](https://aka.ms/many-models) (för hands version) bygger på Azure Machine Learning och gör att du kan använda automatisk ml för att träna, hantera och hantera hundratals eller till och med tusentals maskin inlärnings modeller.

Att till exempel skapa en modell __för varje instans eller individ__ i följande scenarier kan leda till förbättrade resultat:

* Förutsäga försäljning för varje enskilt lager
* Förutsägelse underhåll för hundratals olje brunnar
* Skräddarsy en upplevelse för enskilda användare.

## <a name="automl-in-azure-machine-learning"></a>AutoML i Azure Machine Learning

Azure Machine Learning erbjuder två upplevelser för att arbeta med automatiserade ML:

* För kod erfarna kunder [Azure Machine Learning python SDK](/python/api/overview/azure/ml/intro) 

* För kunder med begränsade/inga kod upplevelser Azure Machine Learning Studio på [https://ml.azure.com](https://ml.azure.com/)  

<a name="parity"></a>

### <a name="experiment-settings"></a>Experiment inställningar 

Med följande inställningar kan du konfigurera ditt automatiserade ML-experiment. 

| |Python SDK|Studio-webbmiljön|
----|:----:|:----:
|**Dela upp data i tåg/verifierings uppsättningar**| ✓|✓
|**Har stöd för ML-aktiviteter: klassificering, regression och Prognosticering**| ✓| ✓
|**Optimerar baserat på primärt mått**| ✓| ✓
|**Stöder Azure ML Compute as Compute Target** | ✓|✓
|**Konfigurera en prognos horisont, mål lags & rullande fönster**|✓|✓
|**Ange avslutnings villkor** |✓|✓ 
|**Ange samtidiga iterationer**| ✓|✓
|**Ta bort kolumner**| ✓|✓
|**Blockera algoritmer**|✓|✓
|**Kors validering** |✓|✓
|**Stöder utbildning i Azure Databricks kluster**| ✓|
|**Visa namn på förtillverkade funktioner**|✓|
|**Sammanfattning av funktionalisering**| ✓|
|**Funktionalisering för helgdagar**|✓|
|**Utförliga logg fils nivåer**| ✓|

### <a name="model-settings"></a>Modell inställningar

De här inställningarna kan tillämpas på den bästa modellen som ett resultat av ditt automatiserade ML-experiment.

| |Python SDK|Studio-webbmiljön|
|----|:----:|:----:|
|**Bästa modell registrering, distribution, förklaring**| ✓|✓|
|**Aktivera röstnings Ensemble & stack Ensemble-modeller**| ✓|✓|
|**Visa bästa modellen baserat på icke-primära mått**|✓||
|**Aktivera/inaktivera kompatibilitet för ONNX-modell**|✓||
|**Testa modellen** | ✓| |

### <a name="run-control-settings"></a>Kör kontroll inställningar

Med de här inställningarna kan du granska och kontrol lera experiment körningarna och dess underordnade körningar. 

| |Python SDK|Studio-webbmiljön|
|----|:----:|:----:|
|**Kör sammanfattnings tabell**| ✓|✓|
|**Avbryt körning & underordnade körningar**| ✓|✓|
|**Hämta guardrails**| ✓|✓|
|**Pausa & återuppta körningar**| ✓| |

<a name="use-with-onnx"></a>

## <a name="automl--onnx"></a>AutoML & ONNX

Med Azure Machine Learning kan du använda automatisk ML för att bygga en python-modell och konvertera den till ONNX-format. När modellerna är i ONNX-formatet kan de köras på olika plattformar och enheter. Lär dig mer om att [accelerera ml-modeller med ONNX](concept-onnx.md).

Se så här konverterar du till ONNX-format [i det här Jupyter Notebook-exemplet](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-bank-marketing-all-features/auto-ml-classification-bank-marketing-all-features.ipynb). Lär dig vilka [algoritmer som stöds i ONNX](how-to-configure-auto-train.md#select-your-experiment-type).

ONNX runtime stöder också C#, så du kan använda modellen som skapats automatiskt i dina C#-appar utan att behöva koda om eller någon av nätverks fördröjningarna som REST-slutpunkter introducerar. Lär dig mer om att [använda en AUTOML ONNX-modell i ett .NET-program med ml.net](./how-to-use-automl-onnx-model-dotnet.md) -och [inferencing ONNX-modeller med API: et för ONNX runtime C#](https://github.com/Microsoft/onnxruntime/blob/master/docs/CSharp_API.md). 

## <a name="next-steps"></a>Nästa steg

Det finns flera resurser som du kan använda för att komma igång med AutoML. 

### <a name="tutorials-how-tos"></a>Självstudier/anvisningar – TOS
Självstudier är från början till slut inledande exempel på AutoML-scenarier.
+ **För en kod första** gången följer du [självstudien: träna en Regressions modell automatiskt med Azure Machine Learning python SDK](tutorial-auto-train-models.md).

 + Information **om låg eller ingen kod** får du i [självstudien: skapa automatiska ml-klassificerings modeller med Azure Machine Learning Studio](tutorial-first-experiment-automated-ml.md).

Artiklar ger ytterligare information om vilka funktioner AutoML erbjuder. Exempel: 

+ Konfigurera inställningarna för automatiska utbildnings experiment
    + [Använd de här stegen](how-to-use-automated-ml-for-ml-models.md)i Azure Machine Learning Studio. 
    + [Använd de här stegen](how-to-configure-auto-train.md)med python SDK.

+  Lär dig hur du tränar att använda Time Series-data automatiskt [med de här stegen](how-to-auto-train-forecast.md).

### <a name="jupyter-notebook-samples"></a>Jupyter Notebook-exempel 

Granska detaljerade kod exempel och användnings fall i [GitHub Notebook-lagringsplatsen för automatiserade maskin inlärnings](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/)exempel.

### <a name="python-sdk-reference"></a>Python SDK-referens

Fördjupa dina kunskaper om SDK design mönster och klass specifikationer med [AutoML klass Reference documentation](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig). 

> [!Note]
> Automatiska maskin inlärnings funktioner är också tillgängliga i andra Microsoft-lösningar som, [ml.net](/dotnet/machine-learning/automl-overview), [HDInsight](../hdinsight/spark/apache-spark-run-machine-learning-automl.md), [Power BI](/power-bi/service-machine-learning-automated) och [SQL Server](https://cloudblogs.microsoft.com/sqlserver/2019/01/09/how-to-automate-machine-learning-on-sql-server-2019-big-data-clusters/)
