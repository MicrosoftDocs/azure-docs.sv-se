---
title: Analys av HDInsight Spark med PySpark, Scala-team data science process
description: Exempel på en grupp data vetenskaps process som går igenom användningen av PySpark och Scala på en Azure HDInsight Spark.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 1662af6cd3499fdf851d4e1bd8a0db48da7635b4
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "93320132"
---
# <a name="hdinsight-spark-data-science-walkthroughs-using-pyspark-and-scala-on-azure"></a>Genom gång av HDInsight Spark data science-instruktioner med PySpark och Scala på Azure

I de här genom gången används PySpark och Scala på ett Azure Spark-kluster för förutsägelse analys. De följer de steg som beskrivs i processen för team data vetenskap. En översikt över processen för team data vetenskap finns i [data science process](overview.md). En översikt över Spark på HDInsight finns i [Introduktion till Spark på HDInsight](../../hdinsight/spark/apache-spark-overview.md).

Ytterligare data vetenskaps genom gångar som kör team data vetenskaps processen grupperas efter den **plattform** som de använder. Se [genom gångar som kör team data science-processen](walkthroughs.md) för en specificering av dessa exempel.

## <a name="predict-taxi-tips-using-pyspark-on-azure-spark"></a>Förutsägelse taxi-tips med PySpark på Azure Spark

Med New York taxi-data, förutsäger [use Spark på Azure HDInsight](spark-overview.md) -genom gången om ett tips betalas ut och förväntat belopp. I det här exemplet används team data vetenskaps processen i ett scenario med ett [Azure HDInsight Spark-kluster](https://azure.microsoft.com/services/hdinsight/) för att lagra, utforska och tillhandahålla tekniker data från den allmänt tillgängliga NYC taxi-resan och pris data uppsättningen. I det här översikts avsnittet används ett HDInsight Spark-kluster och Jupyter PySpark-anteckningsböcker. Dessa antecknings böcker visar hur du utforskar dina data och hur du skapar och använder modeller. Den avancerade data utforsknings-och modellerings antecknings boken visar hur du inkluderar kors validering, rensning av Hyper-parameter och modell utvärdering.

### <a name="data-exploration-and-modeling-with-spark"></a>Data utforskning och modellering med Spark 
Utforska data uppsättningen och skapa, score och utvärdera maskin inlärnings modeller genom att gå igenom [skapa binära klassificerings-och Regressions modeller för data med Spark MLlib Toolkit](spark-data-exploration-modeling.md) -avsnittet.

### <a name="model-consumption"></a>Modell förbrukning
Information om hur du visar klassificerings-och Regressions modeller som skapats i det här avsnittet finns i [Poäng och utvärdera Spark-skapade maskin inlärnings modeller](spark-model-consumption.md).

### <a name="cross-validation-and-hyperparameter-sweeping"></a>Kors validering och Cross-parameter-rensning
Se [Avancerad data granskning och modellering med Spark](spark-advanced-data-exploration-modeling.md) på hur modeller kan tränas med kors validering och rensning av Hyper-parameter.


## <a name="predict-taxi-tips-using-scala-on-azure-spark"></a>Förutsägelse taxi-tips med Scala på Azure Spark

I genom gången [Använd Scala med Spark på Azure](scala-walkthrough.md) förutsägs om ett tips betalas ut och intervallet av de belopp som förväntas betalas. Det visar hur du använder Scala för övervakade Machine Learning-uppgifter med Spark Machine Learning Library (MLlib) och SparkML-paketen på ett Azure HDInsight Spark-kluster. Den vägleder dig genom de uppgifter som utgör [data vetenskaps processen](./index.yml): data inmatning och utforskning, visualisering, funktions teknik, modellering och modell användning. De modeller som är inbyggda är logistik och linjär regression, slumpmässiga skogar och tonings utökat träd.


## <a name="next-steps"></a>Nästa steg

En översikt över team data science-processen finns i [Översikt över team data science-processen](overview.md).

En beskrivning av livs cykeln för team data science process finns i [livs cykeln för team data science process](lifecycle.md). Den här livs cykeln beskriver stegen, från början till slut, som vanligt vis följer de projekt som körs.