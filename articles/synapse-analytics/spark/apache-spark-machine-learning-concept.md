---
title: Machine Learning med Apache Spark
description: Den här artikeln innehåller en översikt över funktionerna för maskin inlärning och data vetenskap som är tillgängliga via Apache Spark på Azure Synapse Analytics.
services: synapse-analytics
author: midesa
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: machine-learning
ms.date: 11/13/2020
ms.author: midesa
ms.reviewer: jrasnick
ms.openlocfilehash: 0485f697b9360b0f2dfe94fdf07629978b5127c1
ms.sourcegitcommit: aacbf77e4e40266e497b6073679642d97d110cda
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/12/2021
ms.locfileid: "98116933"
---
# <a name="machine-learning-with-apache-spark"></a>Maskin inlärning med Apache Spark

Apache Spark i Azure Synapse Analytics möjliggör maskin inlärning med Big data, vilket ger möjlighet att få värdefulla insikter från stora mängder strukturerade, ostrukturerade och snabbt flyttade data. 

Det här avsnittet innehåller en översikt och självstudier för Machine Learning-arbetsflöden, inklusive data analys, funktions teknik, modell utbildning, modell bedömning och distribution.  

## <a name="synapse-runtime"></a>Synapse-körning 
Synapse runtime är en granskad miljö som är optimerad för data vetenskap och maskin inlärning. Synapse-körningsmiljön erbjuder en rad populära bibliotek med öppen källkod och build-versioner i Azure Machine Learning SDK som standard. Synapse-körningsmiljön innehåller också många externa bibliotek, inklusive PyTorch, Scikit – lära, XGBoost och mycket annat.

Läs mer om tillgängliga bibliotek och relaterade versioner genom att visa den publicerade [Azure Synapse Analytics-körningen](../spark/apache-spark-version-support.md).

## <a name="exploratory-data-analysis"></a>Analys av prov data
När du använder Apache Spark i Azure Synapse Analytics finns det olika inbyggda alternativ som hjälper dig att visualisera dina data, inklusive Synapse-diagram alternativ för antecknings böcker, åtkomst till populära bibliotek med öppen källkod som Seaborn och matplotlib, samt integrering med Synapse SQL och Power BI.

Läs mer om alternativen för data visualisering och data analys genom att läsa artikeln om hur du [visualiserar data med Azure Synapse Notebooks](../spark/apache-spark-data-visualization.md).

## <a name="feature-engineering"></a>Funktionsframställning
Synapse runtime innehåller som standard en uppsättning bibliotek som ofta används för funktions teknik. För stora data uppsättningar kan du använda Spark SQL, MLlib och Koalas för funktions teknik. För mindre data mängder kan bibliotek från tredje part som numpy, Pandas och Scikit – lära sig också använda användbara metoder för dessa scenarier.

## <a name="train-models"></a>Inlärningsmodeller
Det finns flera alternativ när du tränar maskin inlärnings modeller med Azure spark i Azure Synapse Analytics: Apache Spark MLlib, Azure Machine Learning och olika bibliotek med öppen källkod. 

Lär dig mer om Machine Learning-funktionerna genom att läsa artikeln om hur du [tränar modeller i Azure Synapse Analytics](../spark/apache-spark-machine-learning-training.md).

### <a name="sparkml-and-mllib"></a>SparkML och MLlib
Spark: s InMemory-distribuerade beräknings funktioner gör det ett bra val för de iterativa algoritmerna som används i maskin inlärnings-och diagram beräkningar. ```spark.ml``` tillhandahåller en enhetlig uppsättning med högnivå-API: er som hjälper användarna att skapa och finjustera pipeliner för maskin inlärning. Om du vill veta mer om ```spark.ml``` kan du gå till [programmerings guiden för Apache Spark ml](https://spark.apache.org/docs/1.2.2/ml-guide.html).

### <a name="azure-machine-learning-automated-ml"></a>Azure Machine Learning automatisk ML
[Azure Machine Learning automatisk ml](../../machine-learning/concept-automated-ml.md) (Automatisk maskin inlärning) hjälper dig att automatisera processen med att utveckla maskin inlärnings modeller. Det gör det möjligt för data experter, analytiker och utvecklare att bygga ML-modeller med hög skalbarhet, effektivitet och produktivitet samtidigt som modell kvaliteten försämras. Komponenterna för att köra Azure Machine Learning automatiserade ML SDK skapas direkt i Synapse-körningsmiljön.

### <a name="open-source-libraries"></a>Bibliotek med öppen källkod
Varje Apache Spark pool i Azure Synapse Analytics levereras med en uppsättning förinstallerade och populära Machine Learning-bibliotek.  Några av de relevanta Machine Learning-biblioteken som ingår som standard är:

- [Scikit – lär dig](https://scikit-learn.org/stable/index.html) är ett av de mest populära dator inlärnings biblioteken med en nod för klassiska ml-algoritmer. Scikit-lär stöder de flesta övervakade och oövervakade Learning-algoritmer och kan också användas för Data utvinning och data analys.
  
- [XGBoost](https://xgboost.readthedocs.io/en/latest/) är ett populärt maskin inlärnings bibliotek som innehåller optimerade algoritmer för besluts träd och slumpmässiga skogar. 
  
- [PyTorch](https://pytorch.org/)  &  [Tensorflow](https://www.tensorflow.org/) är kraftfulla python djup inlärnings bibliotek. I en Apache Spark-pool i Azure Synapse Analytics kan du använda biblioteken för att skapa modeller med en enda dator genom att ange antalet körningar på poolen till noll. Även om Apache Spark inte fungerar under den här konfigurationen är det ett enkelt och kostnads effektivt sätt att skapa modeller med en enda dator.

## <a name="track-model-development"></a>Spåra modellutveckling
[MLFlow](https://www.mlflow.org/) är ett bibliotek med öppen källkod för hantering av livs cykeln för maskin inlärnings experiment. MLFlow-spårning är en komponent i MLflow som loggar och spårar utbildningens körnings mått och modell artefakter. Mer information om hur du kan använda MLFlow spårning via Azure Synapse Analytics och Azure Machine Learning finns i den här självstudien om [hur du använder MLFlow](../../machine-learning/how-to-use-mlflow.md).

## <a name="model-scoring"></a>Modell resultat
Modell poängsättning eller inferencing är fasen där en modell används för att göra förutsägelser. För modell resultat med SparkML eller MLLib kan du utnyttja de ursprungliga Spark-metoderna för att utföra inferencing direkt på en spark-DataFrame. För andra bibliotek med öppen källkod och modell typer kan du också skapa en spark UDF för att skala ut en större data uppsättning. För mindre data uppsättningar kan du också använda de ursprungliga metoderna för den ursprungliga modellen som tillhandahålls av biblioteket.

## <a name="register-and-serve-models"></a>Registrera och hantera modeller
Genom att registrera en modell kan du lagra, version och spåra metadata om modeller i din arbets yta. När du är färdig med att träna din modell kan du registrera din modell i [Azure Machine Learning Model-registret](../../machine-learning/concept-model-management-and-deployment.md#register-package-and-deploy-models-from-anywhere). När ONNX-modeller har registrerats kan de även användas för att [utöka de data](../machine-learning/tutorial-sql-pool-model-scoring-wizard.md) som lagras i DEDIKERADe SQL-pooler.

## <a name="next-steps"></a>Nästa steg
Kom igång med Machine Learning i Azure Synapse Analytics genom att kolla in följande Självstudier:
- [Analysera data med Azure Synapse-anteckningsböcker](../spark/apache-spark-data-visualization-tutorial.md)

- [Träna en Machine Learning-modell med automatisk ML](../spark/apache-spark-azure-machine-learning-tutorial.md)

- [Träna en maskin inlärnings modell med Apache Spark MLlib](../spark/apache-spark-machine-learning-mllib-notebook.md)
