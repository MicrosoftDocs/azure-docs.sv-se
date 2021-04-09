---
title: 'Självstudie: träna en modell med hjälp av automatisk maskin inlärning'
description: Själv studie kurs om hur du tränar en Machine Learning-modell utan kod i Azure Synapse Analytics.
services: synapse-analytics
ms.service: synapse-analytics
ms.subservice: machine-learning
ms.topic: tutorial
ms.reviewer: jrasnick, garye
ms.date: 11/20/2020
author: nelgson
ms.author: negust
ms.openlocfilehash: aaf0aab2ef600b269b9b47182aeb096ca13c7a87
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "98943516"
---
# <a name="tutorial-train-a-machine-learning-model-without-code"></a>Självstudie: träna en maskin inlärnings modell utan kod

Du kan utöka dina data i Spark-tabeller med nya maskin inlärnings modeller som du tränar med hjälp av [Automatisk maskin inlärning](../../machine-learning/concept-automated-ml.md). I Azure Synapse Analytics kan du välja en spark-tabell i arbets ytan som ska användas som en utbildnings data uppsättning för att skapa maskin inlärnings modeller, och du kan göra detta i en kod fri upplevelse.

I den här självstudien får du lära dig hur du tränar maskin inlärnings modeller genom att använda en kod fri upplevelse i Synapse Studio. Synapse Studio är en funktion i Azure Synapse Analytics. 

Du ska använda Automatisk maskin inlärning i Azure Machine Learning, i stället för att koda upplevelsen manuellt. Vilken typ av modell du tränar på beror på vilket problem du försöker lösa.

Om du inte har en Azure-prenumeration kan du [skapa ett kostnads fritt konto innan du börjar](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Förutsättningar

- En [Azure Synapse Analytics-arbetsyta](../get-started-create-workspace.md). Se till att det finns ett Azure Data Lake Storage Gen2 lagrings konto konfigurerat som standard lagrings utrymme. För det Data Lake Storage Gen2 fil system som du arbetar med kontrollerar du att du är en *data deltagare i Storage BLOB*.
- En Apache Spark pool i din Azure Synapse Analytics-arbetsyta. Mer information finns i [snabb start: skapa en dedikerad SQL-pool med hjälp av Synapse Studio](../quickstart-create-sql-pool-studio.md).
- En Azure Machine Learning länkad tjänst i din Azure Synapse Analytics-arbetsyta. Mer information finns i [snabb start: skapa en ny Azure Machine Learning länkad tjänst i Azure Synapse Analytics](quickstart-integrate-azure-machine-learning.md).

## <a name="sign-in-to-the-azure-portal"></a>Logga in på Azure Portal

Logga in på [Azure-portalen](https://portal.azure.com/).

## <a name="create-a-spark-table-for-the-training-dataset"></a>Skapa en spark-tabell för träning-datauppsättningen

I den här självstudien behöver du en spark-tabell. Följande bärbara dator skapar en:

1. Ladda ned antecknings boken [create-Spark-Table-NYCTaxi-data. ipynb](https://go.microsoft.com/fwlink/?linkid=2149229).

1. Importera antecknings boken till Synapse Studio.
![Skärm bild av Azure Synapse Analytics med alternativet importera markerat.](media/tutorial-automl-wizard/tutorial-automl-wizard-00a.png)

1. Välj den Spark-pool som du vill använda och välj sedan **Kör alla**. Det här steget hämtar New York taxi-data från den öppna data uppsättningen och sparar data till din standard Spark-databas.
![Skärm bild av Azure Synapse Analytics, där kör alla och Spark Database är markerat.](media/tutorial-automl-wizard/tutorial-automl-wizard-00b.png)

1. När Notebook-körningen har slutförts visas en ny Spark-tabell under standard Spark-databasen. Från **data** hittar du tabellen med namnet **nyc_taxi**.
![Skärm bild av fliken Azure Synapse Analytics-data med den nya tabellen markerad.](media/tutorial-automl-wizard/tutorial-automl-wizard-00c.png)

## <a name="open-the-automated-machine-learning-wizard"></a>Öppna guiden Automatisk maskin inlärning

Så här öppnar du guiden:

1. Högerklicka på Spark-tabellen som du skapade i föregående steg. Välj **Machine Learning**  >  **utöka med ny modell**.
![Skärm bild av Spark-tabellen med Machine Learning och utöka med ny modell markerad.](media/tutorial-automl-wizard/tutorial-automl-wizard-00d.png)

1. Ange konfigurations information för att skapa en automatiserad maskin inlärnings experiment som körs i Azure Machine Learning. Detta kör tågen flera modeller. Den bästa modellen från en lyckad körning registreras i Azure Machine Learning Model-registret.

   ![Skärm bild av konfigurations specifikationer för att träna en maskin inlärnings modell.](media/tutorial-automl-wizard/tutorial-automl-wizard-configure-run-00a.png)

    - **Azure Machine Learning arbets yta**: en Azure Machine Learning arbets yta krävs för att skapa en automatiserad körning av Machine Learning-experiment. Du måste också länka din Azure Synapse Analytics-arbetsyta med Azure Machine Learning arbets ytan med hjälp av en [länkad tjänst](quickstart-integrate-azure-machine-learning.md). När du har uppfyllt alla krav kan du ange den Azure Machine Learning arbets yta som du vill använda för den automatiska körningen.

    - **Experiment namn**: Ange experimentets namn. När du skickar en automatisk maskin inlärnings körning anger du ett experiment namn. Information för körningen lagras under experimentet i arbets ytan Azure Machine Learning. Den här upplevelsen skapar ett nytt experiment som standard och genererar ett föreslaget namn, men du kan också ange namnet på ett befintligt experiment.

    - **Bästa modell namn**: Ange namnet på den bästa modellen från den automatiserade körningen. Den bästa modellen får detta namn och sparas i Azure Machine Learning Model-registret automatiskt efter den här körningen. En automatiserad maskin inlärnings körning skapar många Machine Learning-modeller. Utifrån det primära mått som du väljer i ett senare steg kan dessa modeller jämföras och den bästa modellen kan väljas.

    - **Mål kolumn**: det här är det som modellen tränas för att förutsäga. Välj den kolumn som du vill förutsäga. (I den här självstudien väljer vi den numeriska kolumnen `fareAmount` som mål kolumn.)

    - **Spark-pool**: Ange den Spark-pool som du vill använda för den automatiserade experiment körningen. Beräkningarna körs på den pool som du anger.

    - **Konfigurations information för Spark**: förutom Spark-poolen har du möjlighet att tillhandahålla konfigurations information för sessionen.

1. Välj **Fortsätt**.

## <a name="choose-a-task-type"></a>Välj en uppgifts typ

Välj maskin inlärnings modell typ för experimentet, baserat på den fråga som du försöker besvara. Eftersom `fareAmount` är mål kolumnen och det är ett numeriskt värde rekommenderar vi att du väljer **regression** här. Välj sedan **Fortsätt**.

![Skärm bild av utöka med ny modell med regression markerat.](media/tutorial-automl-wizard/tutorial-automl-wizard-configure-run-00b.png)

## <a name="additional-configurations"></a>Ytterligare konfigurationer

Om du har valt **regression** eller **klassificering** som modell typ i föregående avsnitt är följande konfigurationer tillgängliga:

- **Primärt mått**: Ange måttet som mäter hur väl modellen fungerar. Du kan använda det här måttet för att jämföra olika modeller som skapats i den automatiserade körningen och avgöra vilken modell som utfördes bäst.

- **Utbildnings jobb tid (timmar)**: Ange den maximala tid i timmar som ett experiment ska köras och träna modeller. Observera att du också kan ange värden som är mindre än 1 (till exempel **0,5**).

- **Max. antal samtidiga iterationer**: Välj det maximala antalet iterationer som körs parallellt.

- **Kompatibilitet för ONNX-modell**: om du aktiverar det här alternativet konverteras de modeller som tränas av automatisk maskin inlärning till ONNX-formatet. Detta är särskilt relevant om du vill använda modellen för poängsättning i Azure Synapse Analytics SQL-pooler.

Alla inställningar har ett standardvärde som du kan anpassa.
![Skärm bild av ytterligare konfigurationer för att konfigurera en Regressions modell.](media/tutorial-automl-wizard/tutorial-automl-wizard-configure-run-00c.png)

När alla konfigurationer som krävs har slutförts kan du starta den automatiserade körningen. Du kan välja **skapa körning**, som startar din körning direkt, utan kod. Om du föredrar kod kan du också välja **Öppna i Notebook**. Med det här alternativet kan du se den kod som skapar körningen och sedan köra antecknings boken.

>[!NOTE]
>Om du har valt **tids serie prognoser** som modell typ i föregående avsnitt måste du göra ytterligare konfigurationer. Prognoser stöder inte heller ONNX-modellens kompatibilitet.

### <a name="create-a-run-directly"></a>Skapa en körning direkt

Starta din automatiserade Machine Learning-körning direkt genom att välja **Starta körning**. Du ser ett meddelande som anger att körningen startar. Sedan visas ett annat meddelande som anger att det lyckades. Du kan också kontrol lera statusen i Azure Machine Learning genom att välja länken i meddelandet.
![Skärm bild av lyckad avisering.](media/tutorial-automl-wizard/tutorial-automl-wizard-configure-run-00d.png)

### <a name="create-a-run-with-a-notebook"></a>Skapa en körning med en bärbar dator

Om du vill skapa en antecknings bok väljer du **Öppna i Notebook**. Välj sedan **Kör alla**. Det ger dig också möjlighet att lägga till inställningar till din automatiserade Machine Learning-körning.

![Skärm bild av en bärbar dator med kör alla markerat.](media/tutorial-automl-wizard/tutorial-automl-wizard-configure-run-00e.png)

När du har skickat körningen visas en länk till experimentet i arbets ytan Azure Machine Learning i antecknings bokens utdata. Välj länken för att övervaka den automatiserade körningen i Azure Machine Learning.
![Skärm bild av Azure Synapse Analytics med en länk markerad. ](media/tutorial-automl-wizard/tutorial-automl-wizard-configure-run-00f.png) )

## <a name="next-steps"></a>Nästa steg

- [Självstudie: guiden bedömnings modell för Machine Learning (för hands version) för dedikerade SQL-pooler](tutorial-sql-pool-model-scoring-wizard.md)
- [Snabb start: skapa en ny Azure Machine Learning länkad tjänst i Azure Synapse Analytics](quickstart-integrate-azure-machine-learning.md)
- [Machine Learning-funktioner i Azure Synapse Analytics](what-is-machine-learning.md)