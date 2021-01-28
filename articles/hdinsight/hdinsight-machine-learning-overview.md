---
title: Översikt över Machine Learning – Azure HDInsight
description: Översikt över utbildnings alternativ för Big data Machine för kluster i Azure HDInsight.
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 12/06/2019
ms.openlocfilehash: 18be3fa8851c2dbaab622c770d02f4db252a231c
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/28/2021
ms.locfileid: "98945263"
---
# <a name="machine-learning-on-hdinsight"></a>Maskin inlärning på HDInsight

HDInsight möjliggör maskin inlärning med Big data, vilket ger möjlighet att få värdefulla insikter från stora mängder (petabyte, eller till och med exabyte) av strukturerade, ostrukturerade och snabbt föränderliga data. Det finns flera maskin inlärnings alternativ i HDInsight: SparkML och Apache Spark MLlib, R, Apache Hive och Microsoft Cognitive Toolkit.

## <a name="sparkml-and-mllib"></a>SparkML och MLlib

[HDInsight Spark](spark/apache-spark-overview.md) är ett Azure-värd erbjudande för [Apache Spark](https://spark.apache.org/), en enhetlig, öppen källa, Parallel Data processing Framework som stöder minnes intern bearbetning för att förbättra stor data analys. Motorn för Spark-bearbetning är byggd för hastighet, enkel användning och avancerad analys. Spark: s InMemory-distribuerade beräknings funktioner gör det ett bra val för de iterativa algoritmerna som används i maskin inlärnings-och diagram beräkningar. Det finns två skalbara Machine Learning-bibliotek som ger algoritmiska modellerings funktioner till den här distribuerade miljön: MLlib och SparkML. MLlib innehåller det ursprungliga API som byggts ovanpå RDD. SparkML är ett nyare paket som ger ett API på högre nivå som bygger på DataFrames för att konstruera ML-pipeliner. SparkML har ännu inte stöd för alla funktioner i MLlib, men ersätter MLlib som Spark standard Machine Learning-bibliotek.

Microsoft Machine Learning-biblioteket för Apache Spark är [MMLSpark](https://github.com/Azure/mmlspark). Det här biblioteket är utformat för att göra data experter mer produktiva i Spark, öka frekvensen för experimentering och utnyttja de senaste teknikerna för maskin inlärning, inklusive djup inlärning, på mycket stora data uppsättningar. MMLSpark tillhandahåller ett lager ovanpå SparkMLs lågnivå-API: er när du skapar skalbara ML-modeller, t. ex. indexering av strängar, data som ska förväntas av Machine Learning-algoritmer och sammansättning av funktions vektorer. MMLSpark-biblioteket fören klar dessa och andra vanliga uppgifter för att skapa modeller i PySpark.

## <a name="r"></a>R

[R](https://www.r-project.org/) är för närvarande det vanligaste statistiska programmeringsspråket i världen. Det är ett data visualiserings verktyg med öppen källkod med en community över 2 500 000 användare och växer. Med sin blomstrande-användar bas och över 8 000-paket är R ett sannolikt val för många företag som behöver maskin inlärning. Du kan skapa ett HDInsight-kluster med ML-tjänster som är klara att användas med enorma data uppsättningar och modeller. Den här funktionen ger data vetenskaps-och statistiker med ett välbekant R-gränssnitt som kan skalas på begäran via HDInsight, utan att det går att konfigurera och underhålla kluster.

![Utbildning för förutsägelse med R Server](./media/hdinsight-machine-learning-overview/training-for-prediction.png)

Edge-noden i ett kluster är en praktisk plats för att ansluta till klustret och köra R-skript.  Du kan också köra R-skript över noderna i klustret med hjälp av skalnings programmets Hadoop-karta minska eller Spark Compute-kontexter.

Med ML-tjänster i HDInsight med Spark kan du parallellisera utbildning över noderna i ett kluster med hjälp av en spark Compute-kontext. Du kan köra R-skript direkt på Edge-noden med alla tillgängliga kärnor parallellt efter behov. Alternativt kan du köra din kod från Edge-noden för att starta bearbetningen som distribueras över alla noder i klustret. ML-tjänster i HDInsight med Spark möjliggör också parallella funktioner från R-paket med öppen källkod, om så önskas.

## <a name="azure-machine-learning-and-apache-hive"></a>Azure Machine Learning och Apache Hive

Azure Machine Learning innehåller verktyg för att modellera förutsägelse analyser och en fullständigt hanterad tjänst som du kan använda för att distribuera förutsägelse modeller som färdiga webb tjänster. Azure Machine Learning är en komplett lösning för förutsägelse analys i molnet som du kan använda för att skapa, testa, operationalisera och hantera förutsägelse modeller. Välj från ett bibliotek med stora algoritmer, Använd en webbaserad Studio för att skapa modeller och enkelt distribuera din modell som en webb tjänst.

![Översikt över Microsoft Azure Machine Learning](./media/hdinsight-machine-learning-overview/azure-machine-learning.png)

Skapa funktioner för data i ett HDInsight Hadoop-kluster med [Hive-frågor](../machine-learning/team-data-science-process/create-features-hive.md). *Funktions teknikerna* försöker öka förutsägelse kraften hos Learning-algoritmer genom att skapa funktioner från rå data som underlättar inlärnings processen. Du kan köra HiveQL-frågor från Azure Machine Learning Studio (klassisk) och komma åt data som bearbetas i Hive och lagras i Blob Storage med hjälp av [modulen importera data](../machine-learning/classic/import-data.md).

## <a name="microsoft-cognitive-toolkit"></a>Microsoft Cognitive Toolkit

[Djup inlärning](https://www.microsoft.com/en-us/research/group/dltc/) är en gren av Machine Learning som använder neurala-nätverk, som inspireras av de biologiska processerna för mänsklig hjärna. Många forskare ser djup inlärning som en löftes metod för att förbättra artificiell intelligens. Exempel på djup inlärning är talade språk översättare, image igenkännings system och maskin vara.

För att få fram sitt eget arbete i djup inlärning har Microsoft utvecklat den kostnads fria, lättanvända [Microsoft Cognitive Toolkit](https://www.microsoft.com/en-us/cognitive-toolkit/)med öppen källkod. Det här verktyget används av en mängd olika Microsoft-produkter, av företag över hela världen med behov av att distribuera djup inlärning i stor skala och av studenter som är intresserade av de senaste algoritmerna och teknikerna.

## <a name="see-also"></a>Se även

### <a name="scenarios"></a>Scenarier

* [Apache Spark med Machine Learning: använda spark i HDInsight för analys av byggnads temperatur med HVAC-data](spark/apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark med Machine Learning: använda spark i HDInsight för att förutsäga resultatet av livsmedels inspektionen](spark/apache-spark-machine-learning-mllib-ipython.md)
* [Skapa film rekommendationer med Apache Mahout](hadoop/apache-hadoop-mahout-linux-mac.md)
* [Apache Hive och Azure Machine Learning](../machine-learning/team-data-science-process/create-features-hive.md)
* [Apache Hive och Azure Machine Learning slut punkt till slut punkt](../machine-learning/team-data-science-process/hive-walkthrough.md)
* [Maskin inlärning med Apache Spark på HDInsight](../machine-learning/team-data-science-process/spark-overview.md)

### <a name="deep-learning-resources"></a>Djup inlärnings resurser

* [Använd Microsoft Cognitive Toolkit djup inlärnings modell med Azure HDInsight Spark kluster](spark/apache-spark-microsoft-cognitive-toolkit.md)
* [Djup inlärning och AI-ramverk på Data Science Virtual Machine (DSVM)](../machine-learning/data-science-virtual-machine/dsvm-tools-deep-learning-frameworks.md)
