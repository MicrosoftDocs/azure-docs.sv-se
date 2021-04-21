---
title: Viktiga & arkitektur
titleSuffix: Azure Machine Learning
description: Den här artikeln ger dig en god förståelse för arkitektur, termer och begrepp som utgör Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sgilley
author: sdgilley
ms.date: 08/20/2020
ms.custom: seoapril2019, seodec18
ms.openlocfilehash: f1eb7a5b4697801775d23091c610ab594b0b27ec
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2021
ms.locfileid: "107813388"
---
# <a name="how-azure-machine-learning-works-architecture-and-concepts"></a>Så Azure Machine Learning fungerar: Arkitektur och begrepp

Lär dig mer om arkitekturen och begreppen [för Azure Machine Learning](overview-what-is-azure-ml.md).  Den här artikeln ger dig en högnivåförståelse av komponenterna och hur de fungerar tillsammans för att hjälpa dig att skapa, distribuera och underhålla maskininlärningsmodeller.

## <a name="workspace"></a><a name="workspace"></a> Arbetsytan

En [arbetsyta för](concept-workspace.md) maskininlärning är den översta resursen för Azure Machine Learning.

:::image type="content" source="media/concept-azure-machine-learning-architecture/architecture.svg" alt-text="Diagram: Azure Machine Learning arkitekturen för en arbetsyta och dess komponenter":::

Arbetsytan är den centraliserade platsen för att:

* Hantera resurser som du använder för träning och distribution av modeller, [till exempel beräkningar](#compute-instance)
* Lagra tillgångar som du skapar när du använder Azure Machine Learning, inklusive:
  * [Miljöer](#environments)
  * [Experiment](#experiments)
  * [Pipelines](#ml-pipelines)
  * [Datauppsättningar](#datasets-and-datastores)
  * [Modeller](#models)
  * [Slutpunkter](#endpoints)

En arbetsyta innehåller andra Azure-resurser som används av arbetsytan:

+ [Azure Container Registry (ACR):](https://azure.microsoft.com/services/container-registry/)Registrerar dockercontainrar som du använder under träningen och när du distribuerar en modell. För att minimera kostnaderna skapas ACR endast när distributionsavbildningar skapas.
+ [Azure Storage:](https://azure.microsoft.com/services/storage/)Används som standarddatalager för arbetsytan.  Jupyter-notebook-filer som används Azure Machine Learning dina beräkningsinstanser lagras även här.
+ [Azure Application Insights:](https://azure.microsoft.com/services/application-insights/)Lagrar övervakningsinformation om dina modeller.
+ [Azure Key Vault:](https://azure.microsoft.com/services/key-vault/)Lagrar hemligheter som används av beräkningsmål och annan känslig information som krävs av arbetsytan.

Du kan dela en arbetsyta med andra.

## <a name="computes"></a>Beräknar

<a name="compute-targets"></a> Ett [beräkningsmål är](concept-compute-target.md) en dator eller uppsättning datorer som du använder för att köra ditt träningsskript eller vara värd för tjänstdistributionen. Du kan använda den lokala datorn eller en fjärrbearbetningsresurs som beräkningsmål.  Med beräkningsmål kan du börja träna på den lokala datorn och sedan skala ut till molnet utan att ändra ditt träningsskript.

Azure Machine Learning två fullständigt hanterade molnbaserade virtuella datorer (VM) som är konfigurerade för maskininlärningsuppgifter:

* <a name="compute-instance"></a>**Beräkningsinstans:** En beräkningsinstans är en virtuell dator som innehåller flera verktyg och miljöer som är installerade för maskininlärning. Den primära användningen av en beräkningsinstans är för utvecklingsarbetsstationen.  Du kan börja köra exempelanteckningsböcker utan att någon konfiguration krävs. En beräkningsinstans kan också användas som beräkningsmål för tränings- och härkomstjobb.

* **Beräkningskluster:** Beräkningskluster är ett kluster med virtuella datorer med skalningsfunktioner för flera noder. Beräkningskluster passar bättre för beräkningsmål för stora jobb och produktion.  Klustret skalas upp automatiskt när ett jobb skickas.  Använd som ett träningsbearbetningsmål eller för dev/test-distribution.

Mer information om att träna beräkningsmål finns i [Training compute targets (Träna beräkningsmål).](concept-compute-target.md#train)  Mer information om beräkningsmål för distribution finns i [Distributionsmål.](concept-compute-target.md#deploy)

## <a name="datasets-and-datastores"></a>Datauppsättningar och datalager

[**Azure Machine Learning Datauppsättningar**](concept-data.md#datasets)  gör det enklare att komma åt och arbeta med dina data. Genom att skapa en datauppsättning skapar du en referens till datakällans plats tillsammans med en kopia av dess metadata. Eftersom data finns kvar på den befintliga platsen medför du ingen extra lagringskostnad och riskerar inte integriteten hos dina datakällor.

Mer information finns i [Skapa och registrera Azure Machine Learning datauppsättningar](how-to-create-register-datasets.md).  Fler exempel med datauppsättningar finns i [exempelanteckningsböckerna](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/work-with-data/datasets-tutorial).

Datauppsättningar använder [datalager för att](concept-data.md#datastores) på ett säkert sätt ansluta till dina Azure-lagringstjänster. Datalager lagrar anslutningsinformation utan att dina autentiseringsuppgifter och integriteten för den ursprungliga datakällan riskeras. De lagrar anslutningsinformation, till exempel ditt prenumerations-ID och tokenauktorisering i din Key Vault som är associerad med arbetsytan, så att du på ett säkert sätt kan komma åt din lagring utan att behöva hårdkoda dem i skriptet.

## <a name="environments"></a>Miljöer

[Arbetsyta](#workspace)  >  **Miljöer**

En [miljö](concept-environments.md) är inkapsling av miljön där träning eller bedömning av maskininlärningsmodellen sker. Miljön anger Python-paket, miljövariabler och programvaruinställningar runt dina tränings- och bedömningsskript.  

Kodexempel finns i avsnittet "Hantera miljöer" i [Så här använder du miljöer.](how-to-use-environments.md#manage-environments)

## <a name="experiments"></a>Experiment

[Arbetsyta](#workspace)  >  **Experiment**

Ett experiment är en gruppering av många körningar från ett angivet skript. Den tillhör alltid en arbetsyta. När du skickar en körning anger du ett experimentnamn. Information för körningen lagras under experimentet. Om namnet inte finns när du skickar ett experiment skapas ett nytt experiment automatiskt.
  
Ett exempel på hur du använder ett experiment finns [i Självstudie: Träna din första modell.](tutorial-1st-experiment-sdk-train.md)

### <a name="runs"></a>Körningar

[Arbetsyta](#workspace)  >  [Experiment](#experiments)  >  **Kör**

En körning är en enskild körning av ett träningsskript. Ett experiment innehåller vanligtvis flera körningar.

Azure Machine Learning registrerar alla körningar och lagrar följande information i experimentet:

* Metadata om körningen (tidsstämpel, varaktighet och så vidare)
* Mått som loggas av skriptet
* Utdatafiler som automatiskt insamlas av experimentet eller som uttryckligen laddats upp av dig
* En ögonblicksbild av katalogen som innehåller dina skript före körningen

Du skapar en körning när du skickar ett skript för att träna en modell. En körning kan ha noll eller flera underordnade körningar. Körningen på den översta nivån kan till exempel ha två underordnade körningar, där var och en kan ha en egen underordnad körning.

### <a name="run-configurations"></a>Köra konfigurationer

[Arbetsyta](#workspace)  >  [Experiment](#experiments)  >  [Kör](#runs)  >  **Köra konfiguration**

En körningskonfiguration definierar hur ett skript ska köras i ett angivet beräkningsmål. Du använder konfigurationen för att ange skriptet, beräkningsmålet och Azure ML-miljön som ska köras på, eventuella distribuerade jobbspecifika konfigurationer och vissa ytterligare egenskaper. Mer information om den fullständiga uppsättningen konfigurerbara alternativ för körningar finns [i ScriptRunConfig](/python/api/azureml-core/azureml.core.scriptrunconfig).

En körningskonfiguration kan bevaras i en fil i katalogen som innehåller ditt träningsskript.   Eller så kan den konstrueras som ett minnes in memory-objekt och användas för att skicka en körning.

Exempel på körningskonfigurationer finns i [Konfigurera en träningskörning.](how-to-set-up-training-targets.md)

### <a name="snapshots"></a>Ögonblicksbilder

[Arbetsyta](#workspace)  >  [Experiment](#experiments)  >  [Kör](#runs)  >  **Ögonblicksbild**

När du skickar en Azure Machine Learning den katalog som innehåller skriptet som en zip-fil och skickar den till beräkningsmålet. Zip-filen extraheras sedan och skriptet körs där. Azure Machine Learning även zip-filen som en ögonblicksbild som en del av körningsposten. Alla med åtkomst till arbetsytan kan bläddra i en körningspost och ladda ned ögonblicksbilden.

### <a name="logging"></a>Loggning

Azure Machine Learning loggar automatiskt standardkörningsmått åt dig. Du kan dock också använda [Python SDK för att logga godtyckliga mått.](how-to-log-view-metrics.md)

Det finns flera sätt att visa dina loggar: övervaka körningsstatus i realtid eller visa resultat efter slutförande. Mer information finns i Övervaka [och visa ML-körningsloggar.](how-to-log-view-metrics.md)


> [!NOTE]
> [!INCLUDE [amlinclude-info](../../includes/machine-learning-amlignore-gitignore.md)]

### <a name="git-tracking-and-integration"></a>Git-spårning och -integrering

När du startar en träningskörning där källkatalogen är en lokal Git-lagringsplats lagras information om lagringsplatsen i körningshistoriken. Detta fungerar med körningar som skickas med en skriptkörningskonfiguration eller ML-pipeline. Det fungerar även för körningar som skickas från SDK eller Machine Learning CLI.

Mer information finns i [Git-integrering för Azure Machine Learning](concept-train-model-git-integration.md).

### <a name="training-workflow"></a>Arbetsflöde för träning

När du kör ett experiment för att träna en modell sker följande steg. Dessa illustreras i arbetsflödesdiagrammet för träning nedan:

* Azure Machine Learning anropas med ögonblicksbild-ID:t för den kodögonblicksbild som sparades i föregående avsnitt.
* Azure Machine Learning skapar ett körnings-ID (valfritt) och en Machine Learning-tjänsttoken, som senare används av beräkningsmål som Machine Learning-beräkning/VM för att kommunicera med Machine Learning-tjänsten.
* Du kan välja antingen ett hanterat beräkningsmål (till exempel Machine Learning-beräkning) eller ett ohanterat beräkningsmål (t.ex. virtuella datorer) för att köra träningsjobb. Här är dataflödena för båda scenarierna:
   * Virtuella datorer/HDInsight som nås av SSH-autentiseringsuppgifter i ett nyckelvalv i Microsoft-prenumerationen. Azure Machine Learning kör hanteringskod på beräkningsmålet som:

   1. Förbereder miljön. (Docker är ett alternativ för virtuella datorer och lokala datorer. Se följande steg för att Machine Learning-beräkning förstå hur körning av experiment på Docker-containrar fungerar.)
   1. Laddar ned koden.
   1. Uppsättningar miljövariabler och konfigurationer.
   1. Kör användarskript (kodögonblicksbild som nämns i föregående avsnitt).

   * Machine Learning-beräkning åtkomst via en arbetsyte hanterad identitet.
Eftersom Machine Learning-beräkning är ett hanterat beräkningsmål (det vill säga att det hanteras av Microsoft) körs det under din Microsoft-prenumeration.

   1. Docker-fjärrkonstruktionen har startades om det behövs.
   1. Hanteringskoden skrivs till användarens Azure Files resurs.
   1. Containern startas med ett inledande kommando. Det vill säga hanteringskod enligt beskrivningen i föregående steg.

* När körningen är klar kan du köra frågor mot körningar och mått. I flödesdiagrammet nedan sker det här steget när träningsbearbetningsmålet skriver tillbaka körningsmåtten till Azure Machine Learning från lagringen i Cosmos DB databasen. Klienter kan anropa Azure Machine Learning. Machine Learning hämtar i sin tur mått från Cosmos DB databasen och returnerar dem tillbaka till klienten.

[![Arbetsflöde för träning](media/concept-azure-machine-learning-architecture/training-and-metrics.png)](media/concept-azure-machine-learning-architecture/training-and-metrics.png#lightbox)

## <a name="models"></a>Modeller

I sin enklaste är en modell ett kodstycke som tar indata och producerar utdata. Att skapa en maskininlärningsmodell innebär att välja en algoritm, ge den data [och justera hyperparametrar.](how-to-tune-hyperparameters.md) Träning är en iterativ process som skapar en tränad modell som kapslar in det modellen lärt sig under träningsprocessen.

Du kan ta med en modell som har tränats utanför Azure Machine Learning. Eller så kan du träna en modell genom att [skicka en körning](#runs) av ett [experiment](#experiments) till ett [beräkningsmål](#compute-targets) i Azure Machine Learning. När du har en modell registrerar [du modellen](#register-model) på arbetsytan.

Azure Machine Learning är ramverksoberoende. När du skapar en modell kan du använda alla populära maskininlärningsramverk som Scikit-learn, XGBoost, PyTorch, TensorFlow och Chainer.

Ett exempel på hur du tränar en modell med Scikit-learn finns [i Självstudie: Träna en bildklassificeringsmodell med Azure Machine Learning](tutorial-train-models-with-aml.md).


### <a name="model-registry"></a><a name="register-model"></a> Modellregister

[Arbetsyta](#workspace)  >  **Modeller**

Med **modellregistret** kan du hålla reda på alla modeller i Azure Machine Learning arbetsyta.

Modeller identifieras med namn och version. Varje gång du registrerar en modell med samma namn som en befintlig förutsätter registret att det är en ny version. Versionen ökas och den nya modellen registreras med samma namn.

När du registrerar modellen kan du ange ytterligare metadatataggar och sedan använda taggarna när du söker efter modeller.

> [!TIP]
> En registrerad modell är en logisk container för en eller flera filer som utgör din modell. Om du till exempel har en modell som lagras i flera filer kan du registrera dem som en enda modell i din Azure Machine Learning arbetsyta. Efter registreringen kan du sedan ladda ned eller distribuera den registrerade modellen och ta emot alla filer som har registrerats.

Du kan inte ta bort en registrerad modell som används av en aktiv distribution.

Ett exempel på hur du registrerar en modell finns i [Träna en bildklassificeringsmodell med Azure Machine Learning](tutorial-train-models-with-aml.md).

## <a name="deployment"></a>Distribution

Du distribuerar en [registrerad modell](#register-model) som en tjänstslutpunkt. Du behöver följande komponenter:

* **Miljö**. Den här miljön kapslar in de beroenden som krävs för att köra modellen för slutsatsledning.
* **Bedömningskod**. Det här skriptet accepterar begäranden, poängar begäranden med hjälp av modellen och returnerar resultatet.
* **Slutsatsledningskonfiguration**. Inferenskonfigurationen anger miljön, inmatningsskriptet och andra komponenter som behövs för att köra modellen som en tjänst.

Mer information om dessa komponenter finns i [Distribuera modeller med Azure Machine Learning](how-to-deploy-and-where.md).

### <a name="endpoints"></a>Slutpunkter

[Arbetsyta](#workspace)  >  **Slutpunkter**

En slutpunkt är en instans av din modell i en webbtjänst som kan finnas i molnet eller en IoT-modul för integrerade enhetsdistributioner.

#### <a name="web-service-endpoint"></a>Webbtjänstslutpunkt

När du distribuerar en modell som en webbtjänst kan slutpunkten distribueras på Azure Container Instances, Azure Kubernetes Service eller FPGA. Du skapar tjänsten från din modell, ditt skript och associerade filer. Dessa placeras i en bascontaineravbildning som innehåller modellens körningsmiljö. Avbildningen har en belastningsutjämnad HTTP-slutpunkt som tar emot bedömningsbegäranden som skickas till webbtjänsten.

Du kan aktivera Application Insights telemetri eller modelltelemetri för att övervaka din webbtjänst. Telemetridata är endast tillgängliga för dig.  Den lagras i dina instanser Application Insights lagringskonto. Om du har aktiverat automatisk skalning skalar Azure automatiskt distributionen.

Följande diagram visar härledningsarbetsflödet för en modell som distribuerats som en webbtjänstslutpunkt:

Här är information:

* Användaren registrerar en modell med hjälp av en klient som Azure Machine Learning SDK.
* Användaren skapar en avbildning med hjälp av en modell, en poängfil och andra modellberoenden.
* Docker-avbildningen skapas och lagras i Azure Container Registry.
* Webbtjänsten distribueras till beräkningsmålet (Container Instances/AKS) med hjälp av avbildningen som skapades i föregående steg.
* Information om bedömningsbegäran lagras Application Insights som finns i användarens prenumeration.
* Telemetri push-skickas också till Microsoft/Azure-prenumerationen.

[![Slutsatsledningsarbetsflöde](media/concept-azure-machine-learning-architecture/inferencing.png)](media/concept-azure-machine-learning-architecture/inferencing.png#lightbox)


Ett exempel på hur du distribuerar en modell som en webbtjänst finns i [Distribuera en bildklassificeringsmodell i Azure Container Instances](tutorial-deploy-models-with-aml.md).

#### <a name="real-time-endpoints"></a>Realtidsslutpunkter

När du distribuerar en tränad modell i designern [kan du distribuera modellen som en realtidsslutpunkt.](tutorial-designer-automobile-price-deploy.md) En realtidsslutpunkt tar vanligtvis emot en enskild begäran via REST-slutpunkten och returnerar en förutsägelse i realtid. Detta skiljer sig från batchbearbetning, som bearbetar flera värden samtidigt och sparar resultatet efter slutförande till ett datalager.

#### <a name="pipeline-endpoints"></a>Pipelineslutpunkter

Med pipelineslutpunkter kan du anropa dina [ML-pipelines](#ml-pipelines) programmatiskt via en REST-slutpunkt. Med pipelineslutpunkter kan du automatisera dina pipelinearbetsflöden.

En pipelineslutpunkt är en samling publicerade pipelines. Med den här logiska organisationen kan du hantera och anropa flera pipelines med samma slutpunkt. Varje publicerad pipeline i en pipelineslutpunkt versionsdelade. Du kan välja en standardpipeline för slutpunkten eller ange en version i REST-anropet.
 

#### <a name="iot-module-endpoints"></a>IoT-modulslutpunkter

En distribuerad IoT-modulslutpunkt är en Docker-container som innehåller din modell och tillhörande skript eller program och eventuella ytterligare beroenden. Du distribuerar dessa moduler med hjälp Azure IoT Edge på gränsenheter.

Om du har aktiverat övervakning samlar Azure in telemetridata från modellen i Azure IoT Edge modulen. Telemetridata är endast tillgängliga för dig och lagras i din lagringskontoinstans.

Azure IoT Edge ser till att modulen körs och övervakar den enhet som är värd för den. 
## <a name="automation"></a>Automation

### <a name="azure-machine-learning-cli"></a>Azure Machine Learning CLI 

Den [Azure Machine Learning CLI](reference-azure-machine-learning-cli.md) är ett tillägg till Azure CLI, ett plattformsoberoende kommandoradsgränssnitt för Azure-plattformen. Det här tillägget innehåller kommandon för att automatisera dina maskininlärningsaktiviteter.

### <a name="ml-pipelines"></a>ML-pipelines

Du använder [pipelines för maskininlärning](concept-ml-pipelines.md) för att skapa och hantera arbetsflöden som sammanfogar maskininlärningsfaser. En pipeline kan till exempel innehålla förberedelse av data, modellträning, modelldistribution och slutsatslednings-/bedömningsfaser. Varje fas kan omfatta flera steg som vart och ett kan köras obevakat i olika beräkningsmål. 

Pipelinesteg kan återanvändas och kan köras utan att köra de föregående stegen igen om resultatet av dessa steg inte har ändrats. Du kan till exempel träna om en modell utan att köra kostsamma steg för förberedelse av data igen om data inte har ändrats. Pipelines gör det också möjligt för dataexperter att samarbeta medan de arbetar med separata områden i ett arbetsflöde för maskininlärning.

## <a name="monitoring-and-logging"></a>Övervakning och loggning

Azure Machine Learning innehåller följande funktioner för övervakning och loggning:

* För __dataforskare__ kan du övervaka dina experiment och logga information från dina träningskörningar. Mer information finns i följande artiklar:
   * [Starta, övervaka och avbryta träningskörningar](how-to-track-monitor-analyze-runs.md)
   * [Loggmått för träningskörningar](how-to-log-view-metrics.md)
   * [Spåra experiment med MLflow](how-to-use-mlflow.md)
   * [Visualisera körningar med TensorBoard](how-to-monitor-tensorboard.md)
* För __administratörer__ kan du övervaka information om arbetsytan, relaterade Azure-resurser och händelser som att skapa och ta bort resurser med hjälp av Azure Monitor. Mer information finns i [Så här övervakar du Azure Machine Learning](monitor-azure-machine-learning.md).
* För __DevOps__ eller __MLOps__ kan du övervaka information som genereras av modeller som distribuerats som webbtjänster eller IoT Edge-moduler för att identifiera problem med distributionerna och samla in data som skickas till tjänsten. Mer information finns i Samla [in modelldata](how-to-enable-data-collection.md) och [Övervaka med Application Insights](how-to-enable-app-insights.md).

## <a name="interacting-with-your-workspace"></a>Interagera med din arbetsyta

### <a name="studio"></a>Studio

[Azure Machine Learning-studio](overview-what-is-machine-learning-studio.md) en webbvy över alla artefakter på din arbetsyta.  Du kan visa resultat och information om dina datauppsättningar, experiment, pipelines, modeller och slutpunkter.  Du kan också hantera beräkningsresurser och datalager i studio.

I Studio får du även åtkomst till de interaktiva verktyg som ingår i Azure Machine Learning:

+ [Azure Machine Learning designer för](concept-designer.md) att utföra arbetsflödessteg utan att skriva kod
+ Webbupplevelse för [automatiserad maskininlärning](concept-automated-ml.md)
+ [Azure Machine Learning notebook-datorer](how-to-run-jupyter-notebooks.md) för att skriva och köra din egen kod i integrerade Jupyter Notebook-servrar.
+ [Dataetiketteringsprojekt för](how-to-create-labeling-projects.md) att skapa, hantera och övervaka projekt för att märka dina data

### <a name="programming-tools"></a>Programmeringsverktyg

> [!IMPORTANT]
> Verktyg som är markerade (förhandsversion) nedan är för närvarande i offentlig förhandsversion.
> Förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

+  Interagera med tjänsten i valfri Python-miljö med [Azure Machine Learning SDK för Python.](/python/api/overview/azure/ml/intro)
+ Interagera med tjänsten i valfri R-miljö med [Azure Machine Learning SDK för R](https://azure.github.io/azureml-sdk-for-r/reference/index.html) (förhandsversion).
+ Använd [Azure Machine Learning designer för](concept-designer.md) att utföra arbetsflödesstegen utan att skriva kod. 
+ Använd [Azure Machine Learning CLI](./reference-azure-machine-learning-cli.md) för automatisering.
+ [Lösningsacceleratorn](https://aka.ms/many-models) många modeller (förhandsversion) bygger på Azure Machine Learning och gör att du kan träna, använda och hantera hundratals eller till och med tusentals maskininlärningsmodeller.

## <a name="next-steps"></a>Nästa steg

Kom igång med Azure Machine Learning här:

* [Vad är Azure Machine Learning?](overview-what-is-azure-ml.md)
* [Skapa en Azure Machine Learning-arbetsyta](how-to-manage-workspace.md)
* [Självstudie (del 1): Träna en modell](tutorial-train-models-with-aml.md)