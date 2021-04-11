---
title: Viktiga begrepp för arkitektur &
titleSuffix: Azure Machine Learning
description: Den här artikeln ger dig en övergripande förståelse för arkitekturen, termer och koncept som utgör Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sgilley
author: sdgilley
ms.date: 08/20/2020
ms.custom: seoapril2019, seodec18
ms.openlocfilehash: 09ebbaa6bb7cd5a2b4dcdee4e18fe905c5b2fbd3
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/31/2021
ms.locfileid: "106062530"
---
# <a name="how-azure-machine-learning-works-architecture-and-concepts"></a>Hur Azure Machine Learning fungerar: arkitektur och koncept

Lär dig mer om arkitekturen och begreppen för [Azure Machine Learning](overview-what-is-azure-ml.md).  Den här artikeln ger dig en övergripande förståelse för komponenterna och hur de fungerar tillsammans för att skapa, distribuera och underhålla maskin inlärnings modeller.

## <a name="workspace"></a><a name="workspace"></a> Platsen

En [Machine Learning-arbetsyta](concept-workspace.md) är en resurs på den översta nivån för Azure Machine Learning.

:::image type="content" source="media/concept-azure-machine-learning-architecture/architecture.svg" alt-text="Diagram: Azure Machine Learning arkitektur för en arbets yta och dess komponenter":::

Arbets ytan är den centrala platsen för att:

* Hantera resurser som du använder för utbildning och distribution av modeller, till exempel [beräkningar](#compute-instance)
* Lagra till gångar som du skapar när du använder Azure Machine Learning, inklusive:
  * [Miljöer](#environments)
  * [Experiment](#experiments)
  * [Pipelines](#ml-pipelines)
  * [Datauppsättningar](#datasets-and-datastores)
  * [Modeller](#models)
  * [Slutpunkter](#endpoints)

En arbets yta innehåller andra Azure-resurser som används av arbets ytan:

+ [Azure Container Registry (ACR)](https://azure.microsoft.com/services/container-registry/): registrerar Docker-behållare som du använder under utbildningen och när du distribuerar en modell. För att minimera kostnaderna skapas ACR endast när distributions avbildningar skapas.
+ [Azure Storage konto](https://azure.microsoft.com/services/storage/): används som standard data lager för arbets ytan.  Jupyter-anteckningsböcker som används med dina Azure Machine Learning beräknings instanser lagras också här.
+ [Azure Application Insights](https://azure.microsoft.com/services/application-insights/): lagrar övervaknings information om dina modeller.
+ [Azure Key Vault](https://azure.microsoft.com/services/key-vault/): lagrar hemligheter som används av beräknings mål och annan känslig information som krävs av arbets ytan.

Du kan dela en arbets yta med andra.

## <a name="computes"></a>Beräknar

<a name="compute-targets"></a> Ett [beräknings mål](concept-compute-target.md) är en dator eller en uppsättning datorer som du använder för att köra ditt utbildnings skript eller vara värd för tjänst distributionen. Du kan använda din lokala dator eller en fjärran sluten beräknings resurs som ett beräknings mål.  Med beräknings mål kan du börja träna på din lokala dator och sedan skala ut till molnet utan att ändra ditt utbildnings skript.

Azure Machine Learning introducerar två fullständigt hanterade molnbaserade virtuella datorer (VM) som har kon figurer ATS för Machine Learning-aktiviteter:

* <a name="compute-instance"></a>**Beräknings instans**: en beräknings instans är en virtuell dator som innehåller flera verktyg och miljöer installerade för Machine Learning. Den primära användningen av en beräknings instans är för din utvecklings arbets Station.  Du kan börja köra exempel antecknings böcker utan att behöva konfigurera några inställningar. En beräknings instans kan också användas som beräknings mål för utbildnings-och inferencing-jobb.

* **Beräknings kluster**: beräknings kluster är ett kluster med virtuella datorer med skalnings funktioner för flera noder. Compute-kluster passar bättre för beräknings mål för stora jobb och produktion.  Klustret skalas upp automatiskt när ett jobb skickas.  Använd som inlärnings mål eller för utveckling och testning av distribution.

Mer information om inlärnings mål finns i [träna beräknings mål](concept-compute-target.md#train).  Mer information om beräknings mål för distribution finns i [distributions mål](concept-compute-target.md#deploy).

## <a name="datasets-and-datastores"></a>Data uppsättningar och data lager

[**Azure Machine Learning data uppsättningar**](concept-data.md#datasets)  gör det enklare att komma åt och arbeta med dina data. Genom att skapa en data uppsättning skapar du en referens till data käll platsen tillsammans med en kopia av dess metadata. Eftersom data behålls på den befintliga platsen debiteras du ingen extra lagrings kostnad och riskerar inte att skydda dina data källor.

Mer information finns i [skapa och registrera Azure Machine Learning data uppsättningar](how-to-create-register-datasets.md).  Fler exempel på hur du använder data uppsättningar finns i [exempel antecknings böckerna](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/work-with-data/datasets-tutorial).

Data uppsättningar använder [data lager](concept-data.md#datastores) för att på ett säkert sätt ansluta till dina Azure Storage-tjänster. Data lager lagrar anslutnings information utan att ange autentiseringsuppgifter för autentisering och integritet för den ursprungliga data källan i risk zonen. De lagrar anslutnings information, t. ex. prenumerations-ID och token-auktorisering i Key Vault som är kopplade till arbets ytan, så att du kan komma åt lagringen på ett säkert sätt utan att behöva hårdkoda dem i skriptet.

## <a name="environments"></a>Miljöer

[Arbets yta](#workspace)  >  **Miljöer**

En [miljö](concept-environments.md) är inkapslingen av miljön där inlärningen eller poängen för din Machine Learning-modell sker. Miljön anger python-paket, miljövariabler och program varu inställningar kring dina utbildnings-och bedömnings skript.  

Kod exempel finns i avsnittet "hantera miljöer" i [använda miljöer](how-to-use-environments.md#manage-environments).

## <a name="experiments"></a>Experiment

[Arbets yta](#workspace)  >  **Experiment**

Ett experiment är en gruppering av många körningar från ett angivet skript. Den tillhör alltid en arbets yta. När du skickar en körning anger du ett experiment namn. Information för körningen lagras under det experimentet. Om namnet inte finns när du skickar ett experiment skapas ett nytt experiment automatiskt.
  
Ett exempel på hur du använder ett experiment finns i [Självstudier: träna din första modell](tutorial-1st-experiment-sdk-train.md).

### <a name="runs"></a>Körningar

[Arbets yta](#workspace)  >  [Experiment](#experiments)  >  **Kör**

En körning är en enskild körning av ett utbildnings skript. Ett experiment innehåller vanligt vis flera körningar.

Azure Machine Learning registrerar alla körningar och lagrar följande information i experimentet:

* Metadata om körning (tidstämpel, varaktighet och så vidare)
* Mått som loggas av ditt skript
* Utdatafiler som autosamlas in av experimentet eller som uttryckligen laddas upp av dig
* En ögonblicks bild av katalogen som innehåller dina skript, innan körningen

Du skapar en körning när du skickar ett skript för att träna en modell. En körning kan ha noll eller flera underordnade körningar. Körningen på den översta nivån kan till exempel ha två underordnade körningar, som var och en kan ha sin egen underordnade körning.

### <a name="run-configurations"></a>Köra konfigurationer

[Arbets yta](#workspace)  >  [Experiment](#experiments)  >  [Kör](#runs)  >  **Kör konfiguration**

En körnings konfiguration definierar hur ett skript ska köras i ett angivet beräknings mål. Du kan använda konfigurationen för att ange skript, beräknings mål och Azure ML-miljö som ska köras på, alla distribuerade projektspecifika konfigurationer och vissa ytterligare egenskaper. Mer information om en fullständig uppsättning konfigurerbara alternativ för körningar finns i [ScriptRunConfig](/python/api/azureml-core/azureml.core.scriptrunconfig).

En körnings konfiguration kan sparas i en fil i den katalog som innehåller ditt utbildnings skript.   Eller så kan det skapas som ett minnes intern objekt och användas för att skicka in en körning.

Du kan till exempel köra konfigurationer i [Konfigurera en tränings körning](how-to-set-up-training-targets.md).

### <a name="snapshots"></a>Ögonblicksbilder

[Arbets yta](#workspace)  >  [Experiment](#experiments)  >  [Kör](#runs)  >  **Ögonblicks bild**

När du skickar en körning komprimerar Azure Machine Learning den katalog som innehåller skriptet som en zip-fil och skickar den till beräknings målet. Zip-filen extraheras sedan och skriptet körs där. Azure Machine Learning lagrar också zip-filen som en ögonblicks bild som en del av körnings posten. Alla som har åtkomst till arbets ytan kan bläddra i en körnings post och ladda ned ögonblicks bilden.

### <a name="logging"></a>Loggning

Azure Machine Learning loggar standard körnings mått automatiskt åt dig. Du kan dock också [använda python SDK för att logga godtyckliga mått](how-to-track-experiments.md).

Det finns flera sätt att visa dina loggar: övervakningens körnings status i real tid eller Visa resultaten efter slut för ande. Mer information finns i [övervaka och Visa ml-körnings loggar](how-to-monitor-view-training-logs.md).


> [!NOTE]
> [!INCLUDE [amlinclude-info](../../includes/machine-learning-amlignore-gitignore.md)]

### <a name="git-tracking-and-integration"></a>Git-spårning och integrering

När du startar en utbildning som kör där käll katalogen är en lokal git-lagringsplats, lagras information om lagrings platsen i körnings historiken. Detta fungerar med körningar som skickas med hjälp av en konfiguration för skript körning eller en ML-pipeline. Den fungerar även för körningar som skickats från SDK eller Machine Learning CLI.

Mer information finns i [git-integrering för Azure Machine Learning](concept-train-model-git-integration.md).

### <a name="training-workflow"></a>Arbets flöde för utbildning

När du kör ett experiment för att träna en modell sker följande steg. Dessa illustreras i arbets flödes diagrammet för utbildning nedan:

* Azure Machine Learning anropas med ögonblicks bild-ID: t för kod ögonblicks bilden som sparades i föregående avsnitt.
* Azure Machine Learning skapar ett körnings-ID (valfritt) och en Machine Learning-nyckeltoken som senare används av beräknings mål som Machine Learning-beräkning/VM: ar för att kommunicera med Machine Learning-tjänsten.
* Du kan välja antingen ett hanterat beräknings mål (till exempel Machine Learning-beräkning) eller ett ohanterat beräknings mål (t. ex. virtuella datorer) för att köra utbildnings jobb. Här är data flöden för båda scenarierna:
   * VM/HDInsight, som används av SSH-autentiseringsuppgifter i ett nyckel valv i Microsoft-prenumerationen. Azure Machine Learning kör hanterings kod på Compute-målet som:

   1. Förbereder miljön. (Docker är ett alternativ för virtuella datorer och lokala datorer. Se följande steg för att Machine Learning-beräkning förstå hur du kan köra experiment i Docker-behållare fungerar.)
   1. Laddar ned koden.
   1. Ställer in miljövariabler och konfigurationer.
   1. Kör användar skript (kod ögonblicks bilden som nämns i föregående avsnitt).

   * Machine Learning-beräkning, som nås via en arbets yta-hanterad identitet.
Eftersom Machine Learning-beräkning är ett hanterat beräknings mål (dvs. det hanteras av Microsoft) körs det under din Microsoft-prenumeration.

   1. Fjärrdockans konstruktion har inaktiverats, om det behövs.
   1. Hanterings koden skrivs till användarens Azure Files-resurs.
   1. Behållaren startas med ett inledande kommando. Det vill säga hanterings koden enligt beskrivningen i föregående steg.

* När körningen är klar kan du fråga körningar och mått. I flödes diagrammet nedan inträffar det här steget när träning Compute Target skriver körnings måtten tillbaka till Azure Machine Learning från lagringen i Cosmos DB-databasen. Klienter kan anropa Azure Machine Learning. Machine Learning kommer i tur och retur-mått från Cosmos DB-databasen och återställa dem tillbaka till klienten.

[![Arbets flöde för utbildning](media/concept-azure-machine-learning-architecture/training-and-metrics.png)](media/concept-azure-machine-learning-architecture/training-and-metrics.png#lightbox)

## <a name="models"></a>Modeller

En modell är i sin enklaste del en kod som tar indata och producerar utdata. Genom att skapa en maskin inlärnings modell kan du välja en algoritm, tillhandahålla data och [Justera disponeringsparametrarna](how-to-tune-hyperparameters.md). Träning är en iterativ process som skapar en utbildad modell som kapslar in vad modellen lärt sig under inlärnings processen.

Du kan ta en modell som har tränats utanför Azure Machine Learning. Eller så kan du träna en modell genom att skicka [ett](#runs) [experiment](#experiments) till ett [beräknings mål](#compute-targets) i Azure Machine Learning. När du har en modell registrerar du [modellen](#register-model) i arbets ytan.

Azure Machine Learning är en Framework-oberoende. När du skapar en modell kan du använda alla populära ramverk för maskin inlärning, till exempel Scikit – lära, XGBoost, PyTorch, TensorFlow och kedjor.

Ett exempel på hur du tränar en modell med Scikit-lära finns i [Självstudier: träna en bild klassificerings modell med Azure Machine Learning](tutorial-train-models-with-aml.md).


### <a name="model-registry"></a><a name="register-model"></a> Modell register

[Arbets yta](#workspace)  >  **Modeller**

Med **modell registret** kan du hålla reda på alla modeller i Azure Machine Learning arbets ytan.

Modeller identifieras efter namn och version. Varje gången du registrerar en modell med samma namn som en befintlig, förutsätter registret att det är en ny version. Versionen ökar och den nya modellen registreras under samma namn.

När du registrerar modellen kan du ange ytterligare metadata-Taggar och sedan använda taggarna när du söker efter modeller.

> [!TIP]
> En registrerad modell är en logisk behållare för en eller flera filer som utgör din modell. Om du till exempel har en modell som lagras i flera filer kan du registrera dem som en enskild modell i Azure Machine Learning arbets ytan. Efter registreringen kan du ladda ned eller distribuera den registrerade modellen och ta emot alla filer som har registrerats.

Du kan inte ta bort en registrerad modell som används av en aktiv distribution.

Ett exempel på hur du registrerar en modell finns i [träna en bild klassificerings modell med Azure Machine Learning](tutorial-train-models-with-aml.md).

## <a name="deployment"></a>Distribution

Du distribuerar en [registrerad modell](#register-model) som en tjänst slut punkt. Du behöver följande komponenter:

* **Miljö**. Den här miljön kapslar in de beroenden som krävs för att köra din modell för härledning.
* **Bedömnings kod**. Det här skriptet accepterar begär Anden, visar förfrågningarna med hjälp av modellen och returnerar resultatet.
* **Konfiguration av härledning**. Konfigurationen av konfigurationen anger miljön, start skriptet och andra komponenter som behövs för att köra modellen som en tjänst.

Mer information om dessa komponenter finns i [Distribuera modeller med Azure Machine Learning](how-to-deploy-and-where.md).

### <a name="endpoints"></a>Slutpunkter

[Arbets yta](#workspace)  >  **Slut punkter**

En slut punkt är en instansiering av din modell i antingen en webb tjänst som kan finnas i molnet eller en IoT-modul för integrerade enhets distributioner.

#### <a name="web-service-endpoint"></a>Webb tjänst slut punkt

När du distribuerar en modell som en webb tjänst kan slut punkten distribueras på Azure Container Instances, Azure Kubernetes-tjänsten eller FPGAs. Du skapar tjänsten från din modell, ditt skript och tillhör ande filer. De placeras i en bas behållar avbildning som innehåller körnings miljön för modellen. Avbildningen har en belastningsutjämnad HTTP-slutpunkt som tar emot Poäng begär Anden som skickas till webb tjänsten.

Du kan aktivera Application Insights telemetri eller modellera telemetri för att övervaka din webb tjänst. Telemetri-data är bara tillgängliga för dig.  Den lagras i Application Insights-och lagrings konto instanser. Om du har aktiverat automatisk skalning skalar Azure automatiskt distributionen.

I följande diagram visas ett flödes schema för en modell som distribueras som en webb tjänst slut punkt:

Här är information:

* Användaren registrerar en modell med hjälp av en klient som Azure Machine Learning SDK.
* Användaren skapar en avbildning med hjälp av en modell, en resultat fil och andra modell beroenden.
* Docker-avbildningen skapas och lagras i Azure Container Registry.
* Webb tjänsten distribueras till Compute-målet (Container Instances/AKS) med hjälp av avbildningen som skapades i föregående steg.
* Information om bedömnings förfrågningar lagras i Application Insights, som finns i användarens prenumeration.
* Telemetri skickas också till Microsoft/Azure-prenumerationen.

[![Arbets flöde för störningar](media/concept-azure-machine-learning-architecture/inferencing.png)](media/concept-azure-machine-learning-architecture/inferencing.png#lightbox)


Ett exempel på hur du distribuerar en modell som en webb tjänst finns [i Distribuera en bild klassificerings modell i Azure Container instances](tutorial-deploy-models-with-aml.md).

#### <a name="real-time-endpoints"></a>Real tids slut punkter

När du distribuerar en tränad modell i designern kan du [distribuera modellen som en slut punkt i real tid](tutorial-designer-automobile-price-deploy.md). En slut punkt i real tid tar ofta emot en enskild begäran via REST-slutpunkten och returnerar en förutsägelse i real tid. Detta är i motsats till batchbearbetning, som bearbetar flera värden samtidigt och sparar resultaten när de har slutförts till ett data lager.

#### <a name="pipeline-endpoints"></a>Pipeline-slutpunkter

Med pipeline-slutpunkter kan du ringa dina [ml-pipeliner](#ml-pipelines) program mässigt via en REST-slutpunkt. Med pipelinens slut punkter kan du automatisera dina pipeline-arbetsflöden.

En pipeline-slutpunkt är en samling publicerade pipeliner. Med den här logiska organisationen kan du hantera och anropa flera pipeliner med samma slut punkt. Varje publicerad pipeline i en pipeline-slutpunkt har versions hantering. Du kan välja en förvald pipeline för slut punkten eller ange en version i REST-anropet.
 

#### <a name="iot-module-endpoints"></a>IoT-modulens slut punkter

En distribuerad IoT module-slutpunkt är en Docker-behållare som innehåller din modell och tillhör ande skript eller program och eventuella ytterligare beroenden. Du distribuerar dessa moduler genom att använda Azure IoT Edge på gräns enheter.

Om du har aktiverat övervakning samlar Azure in telemetridata från modellen i Azure IoT Edge-modulen. Telemetridata är bara tillgängliga för dig och lagras i din lagrings konto instans.

Azure IoT Edge säkerställer att modulen körs och övervakar den enhet som är värd för den. 
## <a name="automation"></a>Automation

### <a name="azure-machine-learning-cli"></a>Azure Machine Learning CLI 

[Azure Machine Learning CLI](reference-azure-machine-learning-cli.md) är ett tillägg till Azure CLI, ett plattforms oberoende kommando rads gränssnitt för Azure-plattformen. Det här tillägget innehåller kommandon för att automatisera dina Machine Learning-aktiviteter.

### <a name="ml-pipelines"></a>ML pipelines

Du använder [maskin inlärnings pipeliner](concept-ml-pipelines.md) för att skapa och hantera arbets flöden som häftar ihop Machine Learning-faser. En pipeline kan till exempel omfatta förberedelse av data, modell utbildning, modell distribution och steg för härledning/poängsättning. Varje fas kan omfatta flera steg, som var och en kan köras obevakade i olika beräknings mål. 

Pipeline-steg kan återanvändas och kan köras utan att köra föregående steg om utdata från dessa steg inte har ändrats. Du kan till exempel omträna en modell utan att köra kostsamma data förberedelse steg om data inte har ändrats. Pipelines gör det också möjligt för data experter att samar beta medan de arbetar med olika delar av ett Machine Learning-arbetsflöde.

## <a name="monitoring-and-logging"></a>Övervakning och loggning

Azure Machine Learning tillhandahåller följande funktioner för övervakning och loggning:

* För __data__ experter kan du övervaka dina experiment och logg information från dina utbildnings körningar. Mer information finns i följande artiklar:
   * [Starta, övervaka och avbryta inlärnings körningar](how-to-manage-runs.md)
   * [Loggmått för träningskörningar](how-to-track-experiments.md)
   * [Spåra experiment med MLflow](how-to-use-mlflow.md)
   * [Visualisera körningar med TensorBoard](how-to-monitor-tensorboard.md)
* För __Administratörer__ kan du övervaka information om arbets ytan, relaterade Azure-resurser och händelser som att skapa och ta bort resurser med hjälp av Azure Monitor. Mer information finns i [så här övervakar du Azure Machine Learning](monitor-azure-machine-learning.md).
* För __DevOps__ eller __MLOps__ kan du övervaka information som genererats av modeller som har distribuerats som webb tjänster eller IoT Edge moduler för att identifiera problem med distributionerna och samla in data som skickats till tjänsten. Mer information finns i [samla in modell data](how-to-enable-data-collection.md) och [övervaka med Application Insights](how-to-enable-app-insights.md).

## <a name="interacting-with-your-workspace"></a>Interagera med din arbets yta

### <a name="studio"></a>Studio

[Azure Machine Learning Studio](overview-what-is-machine-learning-studio.md) innehåller en webbvy över alla artefakter i din arbets yta.  Du kan visa resultat och information om dina data uppsättningar, experiment, pipelines, modeller och slut punkter.  Du kan också hantera beräknings resurser och data lager i Studio.

Studio är också den plats där du kommer åt de interaktiva verktyg som ingår i Azure Machine Learning:

+ [Azure Machine Learning designer](concept-designer.md) för att utföra arbets flödes steg utan att skriva kod
+ Webb upplevelse för [Automatisk maskin inlärning](concept-automated-ml.md)
+ [Azure Machine Learning antecknings böcker](how-to-run-jupyter-notebooks.md) för att skriva och köra din egen kod i integrerade Jupyter Notebook-servrar.
+ [Data](how-to-create-labeling-projects.md) som används för att skapa, hantera och övervaka projekt för att förse dina data med etiketter

### <a name="programming-tools"></a>Programmerings verktyg

> [!IMPORTANT]
> Verktyg som marker ATS (för hands version) nedan finns för närvarande i offentlig för hands version.
> För hands versionen tillhandahålls utan service nivå avtal och rekommenderas inte för produktions arbets belastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

+  Interagera med tjänsten i valfri python-miljö med [Azure Machine Learning SDK för python](/python/api/overview/azure/ml/intro).
+ Interagera med tjänsten i valfri R-miljö med [Azure Machine Learning SDK för R (för](https://azure.github.io/azureml-sdk-for-r/reference/index.html) hands version).
+ Använd [Azure Machine Learning designer](concept-designer.md) för att utföra arbets flödes stegen utan att skriva kod. 
+ Använd [Azure Machine Learning CLI](./reference-azure-machine-learning-cli.md) för Automation.
+ [Många modeller Solution Accelerator](https://aka.ms/many-models) (för hands version) bygger på Azure Machine Learning och gör det möjligt att träna, hantera och hantera hundratals eller till och med tusentals maskin inlärnings modeller.

## <a name="next-steps"></a>Nästa steg

För att komma igång med Azure Machine Learning, se:

* [Vad är Azure Machine Learning?](overview-what-is-azure-ml.md)
* [Skapa en Azure Machine Learning-arbetsyta](how-to-manage-workspace.md)
* [Självstudie (del 1): träna en modell](tutorial-train-models-with-aml.md)