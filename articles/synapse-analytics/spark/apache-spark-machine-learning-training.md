---
title: Träna maskin inlärnings modeller med Apache Spark
description: Använd Apache Spark i Azure Synapse Analytics för att träna maskin inlärnings modeller
author: midesa
ms.author: midesa
ms.reviewer: euang
services: synapse-analytics
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: machine-learning
ms.date: 09/13/2020
ms.openlocfilehash: 805bc2aecda37599234c81dd928868d8c14aea4e
ms.sourcegitcommit: 5db975ced62cd095be587d99da01949222fc69a3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/10/2020
ms.locfileid: "97091997"
---
# <a name="train-machine-learning-models"></a>Träna Machine Learning-modeller
Apache Spark i Azure Synapse Analytics möjliggör maskin inlärning med Big data, vilket ger möjlighet att få värdefulla insikter från stora mängder strukturerade, ostrukturerade och snabbt flyttade data. Det finns flera alternativ när du tränar maskin inlärnings modeller med Azure spark i Azure Synapse Analytics: Apache Spark MLlib, Azure Machine Learning och olika bibliotek med öppen källkod. 

## <a name="apache-sparkml-and-mllib"></a>Apache SparkML och MLlib
Apache Spark i Azure Synapse Analytics är en av Microsofts implementeringar av Apache Spark i molnet. Det ger ett enhetligt, parallellt data bearbetnings ramverk med öppen källkod som stöder minnes bearbetning för att förbättra Big data analyser. Motorn för Spark-bearbetning är byggd för hastighet, enkel användning och avancerad analys. Spark: s InMemory-distribuerade beräknings funktioner gör det ett bra val för de iterativa algoritmerna som används i maskin inlärnings-och diagram beräkningar. 

Det finns två skalbara Machine Learning-bibliotek som ger algoritmiska modellerings funktioner till den här distribuerade miljön: MLlib och SparkML. MLlib innehåller det ursprungliga API som byggts ovanpå RDD. SparkML är ett nyare paket som ger ett API på högre nivå som bygger på DataFrames för att konstruera ML-pipeliner. SparkML har ännu inte stöd för alla funktioner i MLlib, men ersätter MLlib som Spark standard Machine Learning-bibliotek.

> [!NOTE]
> 
> Du kan lära dig mer om hur du skapar en SparkML-modell genom att följa den här [självstudien](../spark/apache-spark-azure-machine-learning-tutorial.md).

## <a name="popular-libraries"></a>Populära bibliotek
Varje Apache Spark pool i Azure Synapse Analytics levereras med en uppsättning förinstallerade och populära Machine Learning-bibliotek. Dessa bibliotek ger återanvändbar kod som du kanske vill inkludera i dina program eller projekt. Några av de relevanta Machine Learning-biblioteken som ingår som standard är:
- [Scikit – lär dig](https://scikit-learn.org/stable/index.html) är ett av de mest populära dator inlärnings biblioteken med en nod för klassiska ml-algoritmer. Scikit-lär stöder de flesta övervakade och oövervakade Learning-algoritmer och kan också användas för Data utvinning och data analys.
  
- [XGBoost](https://xgboost.readthedocs.io/en/latest/) är ett populärt maskin inlärnings bibliotek som innehåller optimerade algoritmer för besluts träd och slumpmässiga skogar. 
  
- [PyTorch](https://pytorch.org/)  &  [Tensorflow](https://www.tensorflow.org/) är kraftfulla python djup inlärnings bibliotek. I en Apache Spark-pool i Azure Synapse Analytics kan du använda biblioteken för att skapa modeller med en enda dator genom att ange antalet körningar på poolen till noll. Även om Apache Spark inte fungerar under den här konfigurationen är det ett enkelt och kostnads effektivt sätt att skapa modeller med en enda dator.

Du kan lära dig mer om tillgängliga bibliotek och relaterade versioner genom att visa den publicerade [Azure Synapse Analytics-körningsmiljön](../spark/apache-spark-version-support.md).

## <a name="mmlspark"></a>MMLSpark
Microsoft Machine Learning-biblioteket för Apache Spark är [MMLSpark](https://github.com/Azure/mmlspark). Det här biblioteket är utformat för att göra data experter mer produktiva i Spark, öka frekvensen för experimentering och utnyttja de senaste teknikerna för maskin inlärning, inklusive djup inlärning, på stora data uppsättningar. 

MMLSpark tillhandahåller ett lager ovanpå SparkMLs lågnivå-API: er när du skapar skalbara ML-modeller, t. ex. indexering av strängar, data som ska förväntas av Machine Learning-algoritmer och sammansättning av funktions vektorer. MMLSpark-biblioteket fören klar dessa och andra vanliga uppgifter för att skapa modeller i PySpark.

## <a name="automated-ml-in-azure-machine-learning"></a>Automatiserad ML i Azure Machine Learning 
Azure Machine Learning är en molnbaserad miljö som gör det möjligt att träna, distribuera, automatisera, hantera och spåra maskin inlärnings modeller. Automatiserad ML i Azure Machine Learning godkänner tränings data och konfigurations inställningar och upprepas automatiskt genom kombinationer av olika metoder för att normalisera/standardisera metoder, modeller och kompatibilitetsinställningar för att komma till den bästa modellen. 

När du använder automatisk ML i Azure Synapse Analytics kan du utnyttja den djup integreringen mellan de olika tjänsterna för att förenkla autentiseringen & modell träning. 

> [!NOTE]
> 
> Du kan lära dig mer om hur du skapar ett Azure Machine Learning automatiserat ML experiment genom att följa den här [självstudien](./spark/../apache-spark-azure-machine-learning-tutorial.md).

## <a name="azure-cognitive-services"></a>Azure Cognitive Services
[Azure Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/what-are-cognitive-services) tillhandahåller funktioner för maskin inlärning för att lösa allmänna problem, till exempel analys av text för känslomässig sentiment eller analys av bilder för att identifiera objekt eller ansikten. Du behöver inte ha några speciella kunskaper om maskininlärning eller datavetenskap för att använda de här tjänsterna. En kognitiv tjänst tillhandahåller delar av eller alla komponenter i en maskin inlärnings lösning: data, algoritm och tränad modell. Dessa tjänster är avsedda att kräva allmän kunskap om dina data utan att behöva uppleva maskin inlärning eller data vetenskap. Du kan utnyttja dessa förtränade Cognitive Services automatiskt i Azure Synapse Analytics.

## <a name="next-steps"></a>Nästa steg
Den här artikeln innehåller en översikt över de olika alternativen för att träna maskin inlärnings modeller i Apache Spark pooler i Azure Synapse Analytics. Du kan lära dig mer om modell utbildning genom att följa själv studie kursen nedan:

- Köra automatiserade ML-experiment med Azure Machine Learning och Azure Synapse Analytics: [Automatisk ml-självstudie](./spark/../apache-spark-autoscale.md) 
- Köra SparkML experiment: [Apache SparkML-självstudie](../spark/apache-spark-azure-machine-learning-tutorial.md)
- Visa standard biblioteken: [Azure Synapse Analytics runtime](../spark/apache-spark-version-support.md)