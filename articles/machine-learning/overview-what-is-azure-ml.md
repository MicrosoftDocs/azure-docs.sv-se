---
title: Vad är Azure Machine Learning
description: Azure Machine Learning är en integrerad data vetenskaps lösning för data forskare och MLops för att modellera och distribuera ML-program i moln skala.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: overview
ms.author: larryfr
author: BlackMist
ms.date: 11/04/2020
ms.custom: devx-track-python
adobe-target: true
ms.openlocfilehash: 09be6a73b092f6298099e4a11bcd606759515c07
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100374260"
---
# <a name="what-is-azure-machine-learning"></a>Vad är Azure Machine Learning?

I den här artikeln får du lära dig mer om Azure Machine Learning, en molnbaserad miljö som du kan använda för att träna, distribuera, automatisera, hantera och spåra ML-modeller. 

Azure Machine Learning kan användas för alla typer av Machine Learning, från klassisk ml till djup inlärning, övervakad och oövervakad inlärning. Oavsett om du föredrar att skriva python eller R-kod med SDK eller arbeta med alternativ utan kod/låg kod i [Studio](#build-ml-models-in-the-studio), kan du skapa, träna och spåra maskin inlärnings-och djup inlärnings modeller i en Azure Machine Learning-arbetsyta. 

Starta utbildningen på den lokala datorn och skala sedan ut till molnet. 

Tjänsten samverkar också med populära djup inlärnings-och förstärknings verktyg med öppen källkod, till exempel PyTorch, TensorFlow, scikit – lära och Ray RLlib. 

> [!Tip]
> **Kostnads fri utvärderings version!**  Om du inte har en Azure-prenumeration kan du skapa ett kostnadsfritt konto innan du börjar. Prova den [kostnads fria eller betalda versionen av Azure Machine Learning](https://aka.ms/AMLFree) idag. Du får krediter som du kan använda för att köpa Azure-tjänster. När de är slut kan du behålla kontot och använda [kostnadsfria Azure-tjänster](https://azure.microsoft.com/free/). Ditt kreditkort debiteras aldrig om du inte specifikt ändrar dina inställningar och ber om debitering.


## <a name="what-is-machine-learning"></a>Vad är maskininlärning?

Maskininlärning är en datavetenskapsteknik som gör att datorer kan använda befintliga data för att göra prognoser om framtida beteenden, resultat och trender. Genom att använda maskininlärning kan datorer lära sig utan att vara explicit programmerade.

Prognoser eller förutsägelser från maskininlärning kan göra appar och enheter smartare. När du handlar på nätet kan maskininlärning till exempel användas för att rekommendera andra produkter som du kanske gillar baserat på vad du har köpt. När ditt kreditkort används så kontrollerar maskininlärningen transaktionen mot en databas med transaktioner vilket hjälper till att upptäcka bedrägerier. När din robotdammsugare dammsuger ett rum tar den hjälp av maskininlärning för att avgöra om jobbet är klart.

## <a name="machine-learning-tools-to-fit-each-task"></a>Maskin inlärnings verktyg för att passa varje uppgift 

Azure Machine Learning innehåller alla verktyg och data forskare som behöver för sina Machine Learning-arbetsflöden, inklusive:
+ [Azure Machine Learning designer](tutorial-designer-automobile-price-train-score.md): dra-n-släpp-moduler för att bygga experimenten och sedan distribuera pipeliner.

+ Jupyter Notebook: Använd våra [exempel Notebook-anteckningsböcker](https://github.com/Azure/MachineLearningNotebooks) eller skapa egna antecknings böcker för att utnyttja våra SDK: er <a href="/python/api/overview/azure/ml/intro?view=azure-ml-py" target="_blank">för python</a> -exempel för din maskin inlärning. 

+ R-skript eller antecknings böcker där du använder <a href="https://azure.github.io/azureml-sdk-for-r/reference/index.html" target="_blank">SDK för R</a> för att skriva din egen kod eller använda R-modulerna i designern.

+ [Många modeller Solution Accelerator](https://aka.ms/many-models) (för hands version) bygger på Azure Machine Learning och gör det möjligt att träna, hantera och hantera hundratals eller till och med tusentals maskin inlärnings modeller.

+ [Machine Learning-tillägg för Visual Studio Code-användare](tutorial-setup-vscode-extension.md)

+ [Maskininlärning CLI](reference-azure-machine-learning-cli.md)

+ Ramverk med öppen källkod, till exempel PyTorch, TensorFlow och scikit – lära och många fler

+ [Förstärka inlärning](how-to-use-reinforcement-learning.md) med Ray RLlib

Du kan även använda [MLflow för att spåra mått och distribuera modeller](how-to-use-mlflow.md) eller Kubeflow för att [bygga arbets flöden från slut punkt till slut punkt](https://www.kubeflow.org/docs/azure/).

## <a name="build-ml-models-in-python-or-r"></a>Bygg ML-modeller i python eller R

Starta utbildningen på den lokala datorn med hjälp av Azure Machine Learning <a href="/python/api/overview/azure/ml/intro?view=azure-ml-py" target="_blank">python SDK</a> eller <a href="https://azure.github.io/azureml-sdk-for-r/reference/index.html" target="_blank">R SDK</a>. Sedan kan du skala ut till molnet. 

Tack vare de många tillgängliga [beräkningsmålen](how-to-create-attach-compute-studio.md), däribland Azure Machine Learning Compute och [Azure Databricks](/azure/databricks/scenarios/what-is-azure-databricks), och med [tjänster för finjustering av avancerade hyperparametrar](how-to-tune-hyperparameters.md) kan du skapa bättre modeller snabbare genom att använda hela styrkan från molnet.

Du kan också [Automatisera modell träning och-justering](tutorial-auto-train-models.md) med hjälp av SDK.

## <a name="build-ml-models-in-the-studio"></a>Bygg ML-modeller i Studio

[Azure Machine Learning Studio](https://studio.azureml.net) är en webb portal i Azure Machine Learning för alternativ med låg kod och ingen kod för modell utbildning, distribution och till gångs hantering. Studio integreras med Azure Machine Learning SDK för en sömlös upplevelse. Mer information finns i [Vad är Azure Machine Learning Studio](overview-what-is-machine-learning-studio.md).

+ **Azure Machine Learning Designer**

  Använd [designern](concept-designer.md) för att träna och distribuera Machine Learning-modeller utan att skriva någon kod. Försök att komma igång med [design guiden](tutorial-designer-automobile-price-train-score.md) . 

  ![Animerad GIF av dra-och-släpp-gränssnittet i Azure Machine Learning designer](media/concept-designer/designer-drag-and-drop.gif)

+ **Spåra experiment**

  Lär dig hur du [spårar och visualiserar data vetenskaps experiment](tutorial-first-experiment-automated-ml.md) i Studio. 

    ![Kör information i Azure Machine Learning Studio](media/how-to-track-experiments/experimentation-tab.gif)


+ **Och mycket annat...**

  Besök Azure Machine Learning Studio på [ml.Azure.com](https://studio.azureml.net). 


## <a name="mlops-deploy--lifecycle-management"></a>MLOps: Distribuera & livs cykel hantering
När du har rätt modell kan du enkelt använda den i en webb tjänst, på en IoT-enhet eller från Power BI. Mer information finns i artikeln om [hur och var man distribuerar](how-to-deploy-and-where.md).

Sedan kan du hantera dina distribuerade modeller med hjälp av [Azure Machine Learning SDK för python](/python/api/overview/azure/ml/?preserve-view=true&view=azure-ml-py), [Azure Machine Learning Studio](https://ml.azure.com)eller [Machine Learning CLI](reference-azure-machine-learning-cli.md).

Dessa modeller kan förbrukas och returnera förutsägelser i [real tid](how-to-consume-web-service.md) eller [asynkront](./tutorial-pipeline-batch-scoring-classification.md) på stora mängder data.

Och med avancerade [maskin inlärnings pipeliner](concept-ml-pipelines.md)kan du samar beta med varje steg från förberedelse av data, modell utbildning och utvärdering, via distribution. Med pipelines kan du:

* Automatisera dator inlärnings processen från slut punkt till slut punkt i molnet
* Återanvänd komponenter och kör bara stegen igen när det behövs
* Använd olika beräknings resurser i varje steg
* Köra batch-bedömnings uppgifter

Om du vill använda skript för att automatisera arbets flödet för Machine Learning innehåller [Machine Learning CLI](reference-azure-machine-learning-cli.md) kommando rads verktyg som utför vanliga åtgärder, till exempel att skicka in en utbildning, köra eller distribuera en modell.

Information om hur du kommer igång med Azure Machine Learning finns i [Nästa steg](#next-steps).

## <a name="integration-with-other-services"></a>Integrering med andra tjänster

Azure Machine Learning fungerar med andra tjänster på Azure-plattformen och integreras med verktyg för öppen källkod som git och MLFlow.

+ Compute-mål som __Azure Kubernetes service__, __Azure Container instances__, __Azure Databricks__, __Azure Data Lake Analytics__ och __Azure HDInsight__. Mer information om Compute-mål finns i [Vad är Compute-mål?](concept-compute-target.md).
+ __Azure Event Grid__. Mer information finns i [använda Azure Machine Learning händelser](./how-to-use-event-grid.md).
+ __Azure Monitor__. Mer information finns i [övervaknings Azure Machine Learning](monitor-azure-machine-learning.md).
+ Data lager som __Azure Storage konton__, __Azure Data Lake Storage__, __Azure SQL Database__, __Azure Database for PostgreSQL__ och __Azure Open-datauppsättningar__. Mer information finns i [få åtkomst till data i Azure Storage Services](how-to-access-data.md) och [skapa data uppsättningar med Azure Open data uppsättningar](how-to-create-register-datasets.md).
+ __Virtuella Azure-nätverk__. Mer information finns i [Översikt över virtuell nätverks isolering och sekretess](how-to-network-security-overview.md).
+ __Azure-pipeliner__. Mer information finns i [träna och distribuera Machine Learning-modeller](/azure/devops/pipelines/targets/azure-machine-learning).
+ __Git-lagringsplatsens loggar__. Mer information finns i [git-integrering](concept-train-model-git-integration.md).
+ __MLFlow__. Mer information finns i [MLflow för att spåra mått](how-to-use-mlflow.md)  och [distribuera MLflow-modeller som en webb tjänst](how-to-deploy-mlflow-models.md) 
+ __Kubeflow__. Mer information finns i [bygga arbets flöden från slut punkt till slut punkt](https://www.kubeflow.org/docs/azure/).

### <a name="secure-communications"></a>Säker kommunikation

Ditt Azure Storage-konto, beräknings mål och andra resurser kan användas på ett säkert sätt i ett virtuellt nätverk för att träna modeller och utföra en härledning. Mer information finns i [Översikt över virtuell nätverks isolering och sekretess](how-to-network-security-overview.md).

## <a name="next-steps"></a>Nästa steg

- Skapa ditt första experiment med önskad metod:
- + [Kom igång i din egen utvecklings miljö](tutorial-1st-experiment-sdk-setup-local.md)
  + [Använd Jupyter notebooks på en beräknings instans för att träna & distribuera ML-modeller](tutorial-1st-experiment-sdk-setup.md)
  + [Använd automatisk maskin inlärning för att träna & distribuera ML-modeller](tutorial-first-experiment-automated-ml.md) 
  + [Använd designerns dra & släpp-funktioner för att träna & distribuera](tutorial-designer-automobile-price-train-score.md) 
  + [Använda Machine Learning CLI för att träna och distribuera en modell](tutorial-train-deploy-model-cli.md)

- Lär dig mer om [pipelines för maskininlärning](concept-ml-pipelines.md) för att skapa, optimera och hantera dina maskininlärningsscenarier.

- Läs artikeln djupgående [Azure Machine Learning arkitektur och begrepp](concept-azure-machine-learning-architecture.md) .
