---
title: Djupgående avancerad analys – Azure HDInsight
description: Lär dig hur avancerade analyser använder algoritmer för att bearbeta Big data i Azure HDInsight.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 01/01/2020
ms.openlocfilehash: 0780f66c981f0cebebc1ab327d783954753db965
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/23/2021
ms.locfileid: "104866734"
---
# <a name="deep-dive---advanced-analytics"></a>Djupgående avancerad analys

## <a name="what-is-advanced-analytics-for-hdinsight"></a>Vad är avancerad analys för HDInsight?

HDInsight ger möjlighet att få värdefulla insikter från stora mängder strukturerade, ostrukturerade och snabbt flyttade data. Avancerad analys är användningen av mycket skalbara arkitekturer, statistiska modeller och maskin inlärnings modeller och intelligenta instrument paneler för att ge dig meningsfulla insikter. Machine Learning eller *förutsägande analys* använder algoritmer som identifierar och lär sig från relationer i dina data för att göra förutsägelser och vägleda dina beslut.

## <a name="advanced-analytics-process"></a>Avancerad analys process

:::image type="content" source="./media/apache-hadoop-deep-dive-advanced-analytics/hdinsight-analytic-process.png" alt-text="Process flöde för avancerad analys" border="false":::

När du har identifierat affärs problemet och har börjat samla in och bearbeta dina data, måste du skapa en modell som representerar den fråga som du vill förutsäga. Din modell kommer att använda en eller flera Machine Learning-algoritmer för att göra den typ av förutsägelse som passar dina affärs behov bäst.  Majoriteten av dina data bör användas för att träna din modell, med resten som används för att testa eller utvärdera den.

När du har skapat, läst in, testat och utvärderat din modell är nästa steg att distribuera din modell så att den börjar tillhandahålla svar på dina frågor. Det sista steget är att övervaka modellens prestanda och finjustera den efter behov.

## <a name="common-types-of-algorithms"></a>Vanliga typer av algoritmer

Avancerade analys lösningar tillhandahåller en uppsättning Machine Learning-algoritmer. Här är en sammanfattning av olika kategorier av algoritmer och associerade vanliga företags användnings fall.

:::image type="content" source="./media/apache-hadoop-deep-dive-advanced-analytics/machine-learning-use-cases.png" alt-text="Sammanfattningar av Machine Learning kategori" border="false":::

Tillsammans med att välja de bästa passnings algoritmerna måste du fundera över om du behöver ange data för träning. Machine Learning-algoritmer kategoriseras på följande sätt:

* Övervakad – algoritm måste tränas på en uppsättning märkta data innan den kan ge resultat
* Mellanövervakade algoritmer kan utökas av extra mål genom interaktiva frågor från en utbildare, som inte var tillgängliga under det inledande övnings steget
* Ej övervakad-algoritm kräver inte tränings data
* Förstärkning av algoritmen använder program varu agenter för att fastställa ett idealiskt beteende inom en speciell kontext (används ofta i Robotics)

| Algoritms kategori| Användning | Utbildnings typ | Algoritmer |
| --- | --- | --- | -- |
| Klassificering | Klassificera personer eller saker i grupper | Övervakas | Besluts träd, Logistisk regression, neurala-nätverk |
| Klustring | Dela upp en uppsättning exempel i homogena grupper | Oövervakade | K-innebär klustring |
| Mönster identifiering | Identifiera ofta förekommande associationer i data | Oövervakade | Association Rules |
| Regression | Förutsäga numeriska resultat | Övervakas | Linjär regression, neurala nätverk |
| Förstärkt | Fastställ optimalt beteende för robots | Förstärkt | Monte Carlo-simuleringar, DeepMind |

## <a name="machine-learning-on-hdinsight"></a>Maskin inlärning på HDInsight

HDInsight har flera alternativ för maskin inlärning för ett arbets flöde för avancerad analys:

* Machine Learning och Apache Spark
* R och ML-tjänster
* Azure Machine Learning och Apache Hive
* Apache Spark och djup inlärning

### <a name="machine-learning-and-apache-spark"></a>Machine Learning och Apache Spark

[HDInsight Spark](../spark/apache-spark-overview.md) är ett Azure-värd erbjudande för [Apache Spark](https://spark.apache.org/), ett enhetligt ramverk med öppen källkod och parallell data bearbetning som använder minnes intern bearbetning för att öka Big data analyser. Motorn för Spark-bearbetning är byggd för hastighet, enkel användning och avancerad analys. Spark: s InMemory-distribuerade beräknings funktioner gör det ett bra val för de iterativa algoritmerna som används i maskin inlärnings-och diagram beräkningar.

Det finns tre skalbara Machine Learning-bibliotek med algoritmiska modellerings funktioner till den här distribuerade miljön:

* [**MLlib**](https://spark.apache.org/docs/latest/ml-guide.html) -MLlib innehåller det ursprungliga API som byggts ovanpå Spark RDD.
* [**SparkML**](https://spark.apache.org/docs/1.2.2/ml-guide.html) -SparkML är ett nyare paket som ger ett API på högre nivå som bygger på Spark-DataFrames för att konstruera ml-pipelines.
* [**MMLSpark**](https://github.com/Azure/mmlspark)  – Microsoft Machine Learning-biblioteket för Apache Spark (MMLSpark) är utformat för att göra data forskare mer produktiva i Spark, för att öka antalet experiment och för att dra nytta av teknik för den senaste maskin inlärningen, inklusive djup inlärning på mycket stora data mängder. MMLSpark-biblioteket fören klar vanliga modellerings uppgifter för att skapa modeller i PySpark.

### <a name="r-and-ml-services"></a>R och ML-tjänster

Som en del av HDInsight kan du skapa ett HDInsight-kluster med [ml-tjänster](../r-server/r-server-overview.md) som är klara att användas med enorma data uppsättningar och modeller. Den här nya funktionen ger data vetenskaps-och statistiker med ett välbekant R-gränssnitt som kan skalas på begäran via HDInsight, utan att det går att konfigurera och underhålla kluster.

### <a name="azure-machine-learning-and-apache-hive"></a>Azure Machine Learning och Apache Hive

[Azure Machine Learning Studio (klassisk)](https://studio.azureml.net/) innehåller verktyg för att modellera förutsägelse analyser och en fullständigt hanterad tjänst som du kan använda för att distribuera förutsägelse modeller som färdiga webb tjänster. Azure Machine Learning innehåller verktyg för att skapa kompletta förutsägelse analys lösningar i molnet för att snabbt skapa, testa, operationalisera och hantera förutsägelse modeller. Välj från ett bibliotek med stora algoritmer, Använd en webbaserad Studio för att skapa modeller och enkelt distribuera din modell som en webb tjänst.

### <a name="apache-spark-and-deep-learning"></a>Apache Spark och djup inlärning

[Djup inlärning](https://www.microsoft.com/research/group/dltc/) är en gren av Machine Learning som använder *djup neurala-nätverk* (hyperoptimerade), som inspireras av de biologiska processerna för mänsklig hjärna. Många forskare ser djup inlärning som en löftes metod för artificiell intelligens. Några exempel på djup inlärning är talade språk översättare, image igenkännings system och maskin vara. För att få fram sitt eget arbete i djup inlärning har Microsoft utvecklat den kostnads fria, lättanvända [Microsoft Cognitive Toolkit](https://www.microsoft.com/en-us/cognitive-toolkit/)med öppen källkod. Verktyget används mycket av en mängd olika Microsoft-produkter, av företag över hela världen med behov av att distribuera djup inlärning i stor skala och av studenter som är intresserade av de senaste algoritmerna och teknikerna.

## <a name="scenario---score-images-to-identify-patterns-in-urban-development"></a>Scenario – Poäng bilder för att identifiera mönster i tätorts utveckling

Nu ska vi titta på ett exempel på en självstudie-pipeline för avancerad analys som använder HDInsight.

I det här scenariot ser du hur Hyperoptimerade som produceras i ett ramverk för djup inlärning, Microsofts Cognitive Toolkit (CNTK), kan användas för att göra stora bild samlingar lagrade i ett Azure Blob Storage-konto med PySpark i ett HDInsight Spark-kluster. Den här metoden tillämpas på ett gemensamt DNN användnings fall, flyg bilds klassificering och kan användas för att identifiera de senaste mönstren i tätorts utveckling.  Du använder en förtränad bild klassificerings modell. Modellen är förtränad på [CIFAR-10-datauppsättningen](https://www.cs.toronto.edu/~kriz/cifar.html) och har tillämpats på 10 000-bilder.

Det finns tre viktiga uppgifter i det här scenariot för avancerad analys:

1. Skapa ett Azure HDInsight Hadoop-kluster med en Apache Spark 2.1.0-distribution.
2. Kör ett anpassat skript för att installera Microsoft Cognitive Toolkit på alla noder i ett Azure HDInsight Spark-kluster.
3. Överför en förskapad Jupyter Notebook till ditt HDInsight Spark-kluster för att tillämpa en utbildad Microsoft Cognitive Toolkit djup inlärnings modell för filer i ett Azure Blob Storage-konto med Spark python API (PySpark).

I det här exemplet används CIFAR-10-bilduppsättningen som kompileras och distribueras av Alex Krizhevsky, Nair och Geoffrey Hinton. CIFAR-10-datauppsättningen innehåller 60 000 32 × 32 färg bilder som tillhör 10 ömsesidigt uteslutande klasser:

:::image type="content" source="./media/apache-hadoop-deep-dive-advanced-analytics/machine-learning-images.png" alt-text="Machine Learning exempel bilder" border="false":::

Mer information om data uppsättningen finns i Alex Krizhevsky s [Learning flera nivåer av funktioner från små bilder](https://www.cs.toronto.edu/~kriz/learning-features-2009-TR.pdf).

Data uppsättningen har partitionerats i en tränings uppsättning på 50 000 avbildningar och en test uppsättning av 10 000-avbildningar. Den första uppsättningen användes för att träna en ResNet-modell ((convolutional rest Network) med 20 lager som använder Microsoft Cognitive Toolkit genom att följa [den här självstudien](https://github.com/Microsoft/CNTK/tree/master/Examples/Image/Classification/ResNet) från Cognitive Toolkit GitHub-lagringsplatsen. De återstående 10 000 bilderna användes för att testa modellens precision. Det är här som det distribuerade data bearbetningen kommer in i spelet: uppgiften för för bearbetning och poängsättning av bilderna är mycket kan göras parallella. Med den sparade tränade modellen i handen användes följande:

* PySpark för att distribuera avbildningarna och den tränade modellen till klustrets arbetsnoder.
* Python för att Förbearbeta avbildningarna på varje nod i HDInsight Spark-klustret.
* Cognitive Toolkit att läsa in modellen och räkna upp de förbehandlade bilderna på varje nod.
* Jupyter antecknings böcker för att köra PySpark-skriptet, samla resultaten och använda [matplotlib](https://matplotlib.org/) för att visualisera modellens prestanda.

Hela förbearbetningen/poängen för 10 000-avbildningarna tar mindre än en minut på ett kluster med fyra arbetsnoder. Modellen förutsäger korrekt etiketter på ~ 9 100 (91%) avbildningar. En förvirring mat ris illustrerar de vanligaste klassificerings felen. Matrisen visar till exempel att fel märkning av hundar som katter och vice versa inträffar oftare än för andra etikett par.

:::image type="content" source="./media/apache-hadoop-deep-dive-advanced-analytics/machine-learning-results.png" alt-text="Machine Learning resultat diagram" border="false":::

### <a name="try-it-out"></a>Prova!

Följ [den här självstudien](../spark/apache-spark-microsoft-cognitive-toolkit.md) för att implementera lösningen från slut punkt till slut punkt: Konfigurera ett HDInsight Spark-kluster, installera Cognitive Toolkit och kör Jupyter Notebook som kommer 10 000 CIFAR-avbildningar.

## <a name="next-steps"></a>Nästa steg

Apache Hive och Azure Machine Learning

* [Apache Hive och Azure Machine Learning slut punkt till slut punkt](../../machine-learning/team-data-science-process/hive-walkthrough.md)
* [Använda ett Azure HDInsight Hadoop-kluster på en data uppsättning på 1 TB](../../machine-learning/team-data-science-process/hive-criteo-walkthrough.md)

Apache Spark och MLLib

* [Maskin inlärning med Apache Spark på HDInsight](../../machine-learning/team-data-science-process/spark-overview.md)
* [Apache Spark med Machine Learning: använda Apache Spark i HDInsight för analys av bygg temperatur med HVAC-data](../spark/apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark med Machine Learning: använda Apache Spark i HDInsight för att förutsäga resultatet av livsmedels inspektionen](../spark/apache-spark-machine-learning-mllib-ipython.md)

Djup inlärning, Cognitive Toolkit och annat

* [Data vetenskap, virtuell Azure-dator](../../machine-learning/data-science-virtual-machine/overview.md)
* [Introduktion till H2O.ai på Azure HDInsight](https://azure.microsoft.com/blog/introducing-h2o-ai-with-on-azure-hdinsight-to-bring-the-most-robust-ai-platform-for-enterprises/)
