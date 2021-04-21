---
title: Vad är Azure Machine Learning
description: Azure Machine Learning är en integrerad datavetenskapslösning som dataforskare och MLops kan använda för att modellera och distribuera ML-program i molnskala.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: overview
ms.author: larryfr
author: BlackMist
ms.date: 04/08/2021
ms.custom: devx-track-python
adobe-target: true
ms.openlocfilehash: cd395f0ff29eed1e09f5bdac882482b201052c90
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2021
ms.locfileid: "107814844"
---
# <a name="what-is-azure-machine-learning"></a>Vad är Azure Machine Learning?

I den här artikeln får du lära Azure Machine Learning, en molnbaserad miljö som du kan använda för att träna, distribuera, automatisera, hantera och spåra ML-modeller. 

Azure Machine Learning kan användas för alla typer av maskininlärning, från klassisk ml till djupinlärning, övervakad och oövervakad inlärning. Oavsett om du föredrar att skriva Python- eller R-kod med SDK eller arbeta med alternativ utan kod/lite kod i [studio](#build-ml-models-in-the-studio)kan du skapa, träna och spåra maskininlärnings- och djupinlärningsmodeller i en Azure Machine Learning-arbetsyta. 

Börja träna på den lokala datorn och skala sedan ut till molnet. 

Tjänsten fungerar också med populära verktyg för djupinlärning och förstärkt öppen källkod som PyTorch, TensorFlow, scikit-learn och Ray RLlib. 

> [!Tip]
> **Kostnadsfri utvärderingsversion!**  Om du inte har en Azure-prenumeration kan du skapa ett kostnadsfritt konto innan du börjar. Prova den [kostnadsfria eller betalda versionen Azure Machine Learning](https://aka.ms/AMLFree) idag. Du får krediter som du kan använda för att köpa Azure-tjänster. När de är slut kan du behålla kontot och använda [kostnadsfria Azure-tjänster](https://azure.microsoft.com/free/). Ditt kreditkort debiteras aldrig om du inte specifikt ändrar dina inställningar och ber om debitering.


## <a name="what-is-machine-learning"></a>Vad är maskininlärning?

Maskininlärning är en datavetenskapsteknik som gör att datorer kan använda befintliga data för att göra prognoser om framtida beteenden, resultat och trender. Genom att använda maskininlärning kan datorer lära sig utan att vara explicit programmerade.

Prognoser eller förutsägelser från maskininlärning kan göra appar och enheter smartare. När du handlar på nätet kan maskininlärning till exempel användas för att rekommendera andra produkter som du kanske gillar baserat på vad du har köpt. När ditt kreditkort används så kontrollerar maskininlärningen transaktionen mot en databas med transaktioner vilket hjälper till att upptäcka bedrägerier. När din robotdammsugare dammsuger ett rum tar den hjälp av maskininlärning för att avgöra om jobbet är klart.

## <a name="machine-learning-tools-to-fit-each-task"></a>Maskininlärningsverktyg som passar varje uppgift 

Azure Machine Learning innehåller alla verktyg som utvecklare och dataexperter behöver för sina arbetsflöden för maskininlärning, inklusive:
+ Den [Azure Machine Learning designern:](tutorial-designer-automobile-price-train-score.md)dra och släpp moduler för att skapa dina experiment och sedan distribuera pipelines i en miljö med lite kod.

+ Jupyter Notebooks: Använd våra [exempelanteckningsböcker](https://github.com/Azure/MachineLearningNotebooks) eller skapa egna notebook-filer för att använda vår SDK för <a href="/python/api/overview/azure/ml/intro" target="_blank">Python-exempel</a> för din maskininlärning. 

+ R-skript eller notebook-filer där du använder SDK för <a href="https://azure.github.io/azureml-sdk-for-r/reference/index.html" target="_blank">R</a> för att skriva din egen kod eller använda R-modulerna i designern.

+ [Lösningsacceleratorn](https://aka.ms/many-models) många modeller (förhandsversion) bygger Azure Machine Learning och gör att du kan träna, använda och hantera hundratals eller till och med tusentals maskininlärningsmodeller.

+ [Maskininlärningstillägget för Visual Studio Code (förhandsversion)](how-to-set-up-vs-code-remote.md) ger dig en komplett utvecklingsmiljö för att skapa och hantera dina maskininlärningsprojekt.

+ [Machine Learning CLI](reference-azure-machine-learning-cli.md) är ett Azure CLI-tillägg som innehåller kommandon för att Azure Machine Learning med resurser från kommandoraden.

+ [Integrering med ramverk med](concept-open-source.md) öppen källkod, till exempel PyTorch, TensorFlow och scikit-learn och många fler för träning, distribution och hantering av maskininlärningsprocessen från slutet till slut.

+ [Förstärkt inlärning](how-to-use-reinforcement-learning.md) med Ray RLlib

Du kan till och med [använda MLflow](how-to-use-mlflow.md) för att spåra mått och distribuera modeller eller Kubeflow för att skapa [arbetsflödespipelines från](https://www.kubeflow.org/docs/azure/)steg till slut.

## <a name="build-ml-models-in-python-or-r"></a>Skapa ML-modeller i Python eller R

Börja träna på den lokala datorn med hjälp Azure Machine Learning <a href="/python/api/overview/azure/ml/intro" target="_blank">Python SDK</a> eller <a href="https://azure.github.io/azureml-sdk-for-r/reference/index.html" target="_blank">R SDK.</a> Sedan kan du skala ut till molnet. 

Tack vare de många tillgängliga [beräkningsmålen](how-to-create-attach-compute-studio.md), däribland Azure Machine Learning Compute och [Azure Databricks](/azure/databricks/scenarios/what-is-azure-databricks), och med [tjänster för finjustering av avancerade hyperparametrar](how-to-tune-hyperparameters.md) kan du skapa bättre modeller snabbare genom att använda hela styrkan från molnet.

Du kan också [automatisera modellträning och justering med](tutorial-auto-train-models.md) hjälp av SDK:n.

## <a name="build-ml-models-in-the-studio"></a>Skapa ML-modeller i studio

[Azure Machine Learning-studio](https://studio.azureml.net) är en webbportal i Azure Machine Learning för alternativ med lite kod och utan kod för modellträning, distribution och tillgångshantering. Studio integreras med Azure Machine Learning SDK för en sömlös upplevelse. Mer information finns i [Vad är Azure Machine Learning-studio](overview-what-is-machine-learning-studio.md).

+ **Azure Machine Learning Designer**

  Använd [designern för](concept-designer.md) att träna och distribuera maskininlärningsmodeller utan att skriva någon kod. Prova [självstudien om designern](tutorial-designer-automobile-price-train-score.md) för att komma igång. 

  ![Animerad gif av dra och släpp-gränssnittet i Azure Machine Learning designer](media/concept-designer/designer-drag-and-drop.gif)

+ **Spåra experiment**

  Lär dig hur [du spårar och visualiserar dataexperiment](how-to-track-monitor-analyze-runs.md) i studio. 

    :::image type="content" source="media/how-to-track-monitor-analyze-runs/run-history.png" alt-text="Körningsinformation i Azure Machine Learning-studio":::


+ **Och mycket mer...**

  Besök Azure Machine Learning-studio på [ml.azure.com](https://studio.azureml.net).


## <a name="mlops-deploy--lifecycle-management"></a>MLOps: Distribuera & livscykelhantering
När du har rätt modell kan du enkelt använda den i en webbtjänst, på en IoT-enhet eller från Power BI. Mer information finns i artikeln om [hur och var man distribuerar](how-to-deploy-and-where.md).

Sedan kan du hantera dina distribuerade modeller med hjälp Azure Machine Learning SDK för [Python,](/python/api/overview/azure/ml/) [Azure Machine Learning-studio](https://ml.azure.com)eller Machine [Learning CLI](reference-azure-machine-learning-cli.md).

Dessa modeller kan användas och returnera förutsägelser [i realtid](how-to-consume-web-service.md) [eller asynkront](./tutorial-pipeline-batch-scoring-classification.md) på stora mängder data.

Och med avancerade [pipelines för maskininlärning](concept-ml-pipelines.md)kan du samarbeta kring varje steg från förberedelse av data, modellträning och utvärdering till distribution. Med pipelines kan du:

* Automatisera maskininlärningsprocessen från end-to-end till molnet
* Återanvända komponenter och kör bara om stegen när det behövs
* Använda olika beräkningsresurser i varje steg
* Köra batchbedömningsuppgifter

Om du vill använda skript för att automatisera arbetsflödet för maskininlärning tillhandahåller [Machine Learning CLI](reference-azure-machine-learning-cli.md) kommandoradsverktyg som utför vanliga uppgifter, till exempel att skicka en träningskörning eller distribuera en modell.

Kom igång med Azure Machine Learning i [Nästa steg.](#next-steps)

## <a name="integration-with-other-services"></a>Integrering med andra tjänster

Azure Machine Learning fungerar med andra tjänster på Azure-plattformen och integreras även med verktyg med öppen källkod som Git och MLFlow.

+ Beräkningsmål som __Azure Kubernetes Service,__ __Azure Container Instances__, __Azure Databricks__, __Azure Data Lake Analytics__ och __Azure HDInsight__. Mer information om beräkningsmål finns i [Vad är beräkningsmål?](concept-compute-target.md).
+ __Azure Event Grid__. Mer information finns i [Använda Azure Machine Learning händelser.](./how-to-use-event-grid.md)
+ __Azure Monitor__. Mer information finns i [Monitoring Azure Machine Learning](monitor-azure-machine-learning.md).
+ Datalager som __Azure Storage,__ __Azure Data Lake Storage__, __Azure SQL Database__, __Azure Database for PostgreSQL__ och __Azure Open Datasets__. Mer information finns i Åtkomst [till data i Azure Storage-tjänster](how-to-access-data.md) och Skapa [datauppsättningar med Azure Open Datasets](how-to-create-register-datasets.md).
+ __Azure Virtual Networks__. Mer information finns i Översikt [över isolering och sekretess för virtuella nätverk.](how-to-network-security-overview.md)
+ __Azure Pipelines__. Mer information finns i Träna [och distribuera maskininlärningsmodeller.](/azure/devops/pipelines/targets/azure-machine-learning)
+ __Git-lagringsplatsloggar__. Mer information finns i [Git-integrering.](concept-train-model-git-integration.md)
+ __MLFlow__. Mer information finns i [MLflow för att spåra mått och](how-to-use-mlflow.md)  Distribuera [Mlflow-modeller som en webbtjänst](how-to-deploy-mlflow-models.md) 
+ __Kubeflow__. Mer information finns i [Skapa arbetsflödespipelines från end-to-end.](https://www.kubeflow.org/docs/azure/)

### <a name="secure-communications"></a>Säker kommunikation

Ditt Azure Storage konto, beräkningsmål och andra resurser kan användas på ett säkert sätt i ett virtuellt nätverk för att träna modeller och utföra slutsatsledning. Mer information finns i Översikt [över isolering och sekretess för virtuella nätverk.](how-to-network-security-overview.md)

## <a name="next-steps"></a>Nästa steg

- Skapa ditt första experiment med den metod du föredrar:
- + [Kom igång i din egen utvecklingsmiljö](tutorial-1st-experiment-sdk-setup-local.md)
  + [Använda Jupyter Notebooks på en beräkningsinstans för att träna & distribuera ML-modeller](tutorial-1st-experiment-sdk-setup.md)
  + [Använda automatiserad maskininlärning för att & distribuera ML-modeller](tutorial-first-experiment-automated-ml.md) 
  + [Hantera resurser i Visual Studio Code](how-to-manage-resources-vscode.md)
  + [Använda Visual Studio Code för att träna och distribuera en bildklassificeringsmodell](tutorial-train-deploy-image-classification-model-vscode.md)
  + [Använd designerns dra och släpp-funktioner & att träna & distribuera](tutorial-designer-automobile-price-train-score.md) 
  + [Använda Machine Learning CLI för att träna och distribuera en modell](tutorial-train-deploy-model-cli.md)

- Lär dig mer om [pipelines för maskininlärning](concept-ml-pipelines.md) för att skapa, optimera och hantera dina maskininlärningsscenarier.

- Läs den djupgående artikeln [Azure Machine Learning om arkitektur och](concept-azure-machine-learning-architecture.md) begrepp.
