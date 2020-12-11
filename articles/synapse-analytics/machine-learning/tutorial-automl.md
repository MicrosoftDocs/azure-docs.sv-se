---
title: 'Självstudie: träna en modell med automatisk ML'
description: Själv studie kurs om hur du tränar en Machine Learning-modell utan kod i Azure Synapse med hjälp av Apache Spark och automatisk ML.
services: synapse-analytics
ms.service: synapse-analytics
ms.subservice: machine-learning
ms.topic: tutorial
ms.reviewer: jrasnick, garye
ms.date: 11/20/2020
author: nelgson
ms.author: negust
ms.openlocfilehash: 4967d5305b4b438f3baa6fca078d7b3169612590
ms.sourcegitcommit: 5db975ced62cd095be587d99da01949222fc69a3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/10/2020
ms.locfileid: "97093408"
---
# <a name="tutorial-train-a-machine-learning-model-code-free-in-azure-synapse-with-apache-spark-and-automated-ml"></a>Självstudie: träna en maskin inlärnings modell kod kostnads fritt i Azure Synapse med Apache Spark och automatisk ML

Lär dig hur du enkelt kan utöka dina data i Spark-tabeller med nya maskin inlärnings modeller som du tränar med att använda [automatiserade ml i Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/concept-automated-ml).  En användare i Synapse kan helt enkelt välja en spark-tabell i Azure Synapse-arbetsytan som används som en utbildnings data uppsättning för att skapa maskin inlärnings modeller i en kod fri upplevelse.

I den här självstudien får du lära dig att:

> [!div class="checklist"]
> - Träna maskin inlärnings modeller med en kod fri upplevelse i Azure Synapse Studio som använder automatiserad ML i Azure Machine Learning. Vilken typ av modell du tränar beror på vilket problem du försöker lösa.

Om du inte har en Azure-prenumeration kan du [skapa ett kostnads fritt konto innan du börjar](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Förutsättningar

- [Synapse Analytics-arbetsyta](../get-started-create-workspace.md) med ett ADLS Gen2 lagrings konto konfigurerat som standard lagring. Du måste vara **data deltagare i Storage BLOB** för det ADLS Gen2-filsystem som du arbetar med.
- Spark-pool i din Azure Synapse Analytics-arbetsyta. Mer information finns i [skapa en spark-pool i Azure Synapse](../quickstart-create-sql-pool-studio.md).
- Azure Machine Learning länkad tjänst i din Azure Synapse Analytics-arbetsyta. Mer information finns i [skapa en Azure Machine Learning länkad tjänst i Azure DataSynapses](quickstart-integrate-azure-machine-learning.md).

## <a name="sign-in-to-the-azure-portal"></a>Logga in på Azure Portal

Logga in på [Azure-portalen](https://portal.azure.com/)

## <a name="create-a-spark-table-for-training-dataset"></a>Skapa en spark-tabell för tränings data uppsättning

Du behöver en spark-tabell för den här självstudien. Följande bärbara dator kommer att skapa en spark-tabell.

1. Ladda ned antecknings boken [create-Spark-Table-NYCTaxi-data. ipynb](https://go.microsoft.com/fwlink/?linkid=2149229)

1. Importera antecknings boken till Azure Synapse Studio.
![Importera antecknings bok](media/tutorial-automl-wizard/tutorial-automl-wizard-00a.png)

1. Välj den Spark-pool som du vill använda och klicka på `Run all` . Kör den här antecknings boken för att hämta New York taxi-data från öppen data uppsättning och spara till din standard Spark-databas.
![Kör alla](media/tutorial-automl-wizard/tutorial-automl-wizard-00b.png)

1. När Notebook-körningen har slutförts skapas en ny Spark-tabell under standard Spark-databasen. Gå till data hubben och hitta tabellen med namnet med `nyc_taxi` .
![Spark-tabell](media/tutorial-automl-wizard/tutorial-automl-wizard-00c.png)

## <a name="launch-automated-ml-wizard-to-train-a-model"></a>Starta guiden automatiserad ML för att träna en modell

Högerklicka på Spark-tabellen som du skapade i föregående steg. Välj "Machine Learning-> utöka med ny modell" för att öppna guiden.
![Starta guiden automatiserad ML](media/tutorial-automl-wizard/tutorial-automl-wizard-00d.png)

En konfigurations panel visas och du uppmanas att ange konfigurations information för att skapa en automatiserad ML experiment körning i Azure Machine Learning. Den här körningen kommer att träna flera modeller och den bästa modellen från en lyckad körning registreras i Azure Machine Learning Model-registret:

![Konfigurera kör steg 1](media/tutorial-automl-wizard/tutorial-automl-wizard-configure-run-00a.png)

- **Azure Machine Learning arbets yta**: en Azure Machine Learning arbets yta krävs för att skapa den automatiserade ml experiment körningen. Du måste också länka din Azure Synapse-arbetsyta med Azure Machine Learning arbets ytan med hjälp av en [länkad tjänst](quickstart-integrate-azure-machine-learning.md). När du har alla krav för för hands version kan du ange Azure Machine Learning arbets ytan som du vill använda för den här automatiserade ML-körningen.

- **Experiment namn**: Ange experimentets namn. När du skickar en automatisk ML-körning anger du ett experiment namn. Information för körningen lagras under experimentet i arbets ytan Azure Machine Learning. Den här funktionen kommer att skapa ett nytt experiment som standard och genererar ett föreslaget namn, men du kan också ange ett namn på ett befintligt experiment.

- **Bästa modell**: Ange namnet på den bästa modellen från den AUTOMATISERAde ml-körningen. Den bästa modellen får detta namn och sparas i Azure Machine Learning Model-registret automatiskt efter den här körningen. Med en automatiserad ML-körning skapas många maskin inlärnings modeller. Utifrån det primära måttet som du väljer i ett senare steg kan dessa modeller jämföras och den bästa modellen kan väljas.

- **Mål kolumn**: det här är det som modellen tränas för att förutsäga. Välj den kolumn som du vill förutsäga.

- **Spark-pool**: den Spark-pool som du vill använda för den automatiserade ml experiment körningen. Beräkningarna utförs på den pool som du anger.

- **Konfigurations information för Spark**: förutom Spark-poolen har du också möjlighet att tillhandahålla konfigurations information för sessionen.

I den här självstudien väljer vi den numeriska kolumnen `fareAmount` som mål kolumn.

Klicka på Fortsätt.

## <a name="choose-task-type"></a>Välj typ av aktivitet

Välj maskin inlärnings modell typ för experimentet baserat på den fråga som du försöker besvara. Eftersom vi valde `fareAmount` som mål kolumn och det är ett numeriskt värde väljer vi *regression*.

Klicka på Fortsätt om du vill konfigurera ytterligare inställningar.

![Val av aktivitets typ](media/tutorial-automl-wizard/tutorial-automl-wizard-configure-run-00b.png)

## <a name="additional-configurations"></a>Ytterligare konfigurationer

Om du väljer *klassificering* eller *Regressions* typ är de ytterligare konfigurationerna:

- **Primärt mått**: det mått som används för att mäta hur väl modellen fungerar. Detta är det mått som ska användas för att jämföra olika modeller som skapats i den automatiserade ML-körningen och avgöra vilken modell som utfördes bäst.

- **Utbildnings jobb tid (timmar)**: maximal tid i timmar, för ett experiment att köra och träna modeller. Observera att du också kan ange värden som är mindre än 1. Till exempel `0.5`.

- **Max. antal samtidiga iterationer**: representerar det maximala antalet iterationer som kan köras parallellt.

- **Kompatibilitet för ONNX-modell**: om den är aktive rad konverteras de modeller som har tränats av automatisk ml till ONNX-formatet. Detta är särskilt relevant om du vill använda modellen för poängsättning i Azure Synapse SQL-pooler.

Alla inställningar har ett standardvärde som du kan anpassa.
![ytterligare konfigurationer](media/tutorial-automl-wizard/tutorial-automl-wizard-configure-run-00c.png)

> Observera att om du väljer "tids serie prognoser" krävs det fler konfigurationer. Prognosticering stöder inte heller ONNX-modellens kompatibilitet.

När alla nödvändiga konfigurationer är klara kan du starta automatisk ML-körning.

Det finns två sätt att starta en automatisk ML-körning i Azure Azure-Synapse. För en kod fri upplevelse kan du välja att **skapa kör** direkt. Om du föredrar kod kan du välja **Öppna i antecknings boken**, så att du kan se koden som skapar körnings-och körnings antecknings boken.

### <a name="create-run-directly"></a>Skapa kör direkt

Klicka på Starta körning för att starta automatisk ML-körning direkt. Ett meddelande visas som anger att automatisk ML-körning startar.

När den automatiserade ML-körningen har startats visas en annan lyckad avisering. Du kan också klicka på meddelande knappen för att kontrol lera statusen för körnings sändningen.
Azure Machine Learning genom att klicka på länken i meddelandet lyckades.
![Aviseringen har slutförts](media/tutorial-automl-wizard/tutorial-automl-wizard-configure-run-00d.png)

### <a name="create-run-with-notebook"></a>Skapa kör med Notebook

Välj *Öppna i Notebook* för att skapa en antecknings bok. Klicka på *Kör alla* för att köra antecknings boken.
Det ger dig också möjlighet att lägga till ytterligare inställningar till din automatiserade ML-körning.

![Öppna antecknings boken](media/tutorial-automl-wizard/tutorial-automl-wizard-configure-run-00e.png)

När körningen från antecknings boken har skickats får du en länk till experimentet i arbets ytan Azure Machine Learning i antecknings bokens utdata. Du kan klicka på länken för att övervaka din automatiserade ML-körning i Azure Machine Learning.
![Notebook-kör alla ](media/tutorial-automl-wizard/tutorial-automl-wizard-configure-run-00f.png) )

## <a name="next-steps"></a>Nästa steg

- [Självstudie: Machine Learning-modellens poängsättning i Azure Synapse-dedikerade SQL-pooler](tutorial-sql-pool-model-scoring-wizard.md).
- [Snabb start: skapa en ny Azure Machine Learning länkad tjänst i Azure Synapse](quickstart-integrate-azure-machine-learning.md)
- [Machine Learning funktioner i Azure Synapse Analytics](what-is-machine-learning.md)
