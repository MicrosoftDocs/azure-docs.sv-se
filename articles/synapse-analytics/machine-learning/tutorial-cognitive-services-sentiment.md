---
title: 'Självstudie: sentiment-analys med Cognitive Services'
description: Lär dig hur du använder Cognitive Services för sentiment-analys i Azure Synapse Analytics
services: synapse-analytics
ms.service: synapse-analytics
ms.subservice: machine-learning
ms.topic: tutorial
ms.reviewer: jrasnick, garye
ms.date: 11/20/2020
author: nelgson
ms.author: negust
ms.openlocfilehash: 08d5e53facce172c2287c2e341895f0ee38571f0
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98943706"
---
# <a name="tutorial-sentiment-analysis-with-cognitive-services-preview"></a>Självstudie: sentiment-analys med Cognitive Services (för hands version)

I den här självstudien får du lära dig hur du enkelt kan utöka dina data i Azure Synapse Analytics med [azure Cognitive Services](../../cognitive-services/index.yml). Du använder [textanalys](../../cognitive-services/text-analytics/index.yml) funktionerna för att utföra sentiment-analys. 

En användare i Azure Synapse kan enkelt välja en tabell som innehåller en text kolumn som ska utökas med sentiment. Dessa sentiment kan vara positiva, negativa, blandade eller neutrala. En sannolikhet kommer också att returneras.

Den här självstudiekursen omfattar:

> [!div class="checklist"]
> - Steg för att hämta en spark Table-datauppsättning som innehåller en text kolumn för sentiment-analys.
> - Använda en guide upplevelse i Azure Synapse för att utöka data med hjälp av Textanalys i Cognitive Services.

Om du inte har en Azure-prenumeration kan du [skapa ett kostnads fritt konto innan du börjar](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Förutsättningar

- [Azure Synapse Analytics-arbetsyta](../get-started-create-workspace.md) med ett Azure Data Lake Storage Gen2 lagrings konto konfigurerat som standard lagring. Du måste vara *data deltagare i Storage BLOB* för det data Lake Storage Gen2 fil system som du arbetar med.
- Spark-pool i din Azure Synapse Analytics-arbetsyta. Mer information finns i [skapa en spark-pool i Azure Synapse](../quickstart-create-sql-pool-studio.md).
- För konfigurations steg som beskrivs i självstudien [konfigurera Cognitive Services i Azure-Synapse](tutorial-configure-cognitive-services-synapse.md).

## <a name="sign-in-to-the-azure-portal"></a>Logga in på Azure Portal

Logga in på [Azure-portalen](https://portal.azure.com/).

## <a name="create-a-spark-table"></a>Skapa en spark-tabell

Du behöver en spark-tabell för den här självstudien.

1. Hämta [FabrikamComments.csv](https://github.com/Kaiqb/KaiqbRepo0731190208/blob/master/CognitiveServices/TextAnalytics/FabrikamComments.csv) -filen som innehåller en data uppsättning för text analys. 

1. Ladda upp filen till ditt Azure Synapse Storage-konto i Data Lake Storage Gen2.
  
   ![Skärm bild som visar markeringar för att ladda upp data.](media/tutorial-cognitive-services/tutorial-cognitive-services-sentiment-00a.png)

1. Skapa en spark-tabell från CSV-filen genom att högerklicka på filen och välja **ny antecknings bok**  >  **skapa Spark-tabell**.

   ![Skärm bild som visar markeringar för att skapa en spark-tabell.](media/tutorial-cognitive-services/tutorial-cognitive-services-sentiment-00b.png)

1. Namnge tabellen i cellen Code och kör antecknings boken i en spark-pool. Kom ihåg att ange `header=True` .

   ![Skärm bild som visar hur du kör en bärbar dator.](media/tutorial-cognitive-services/tutorial-cognitive-services-sentiment-00c.png)

   ```python
   %%pyspark
   df = spark.read.load('abfss://default@azuresynapsesa.dfs.core.windows.net/data/FabrikamComments.csv', format='csv'
   ## If a header exists, uncomment the line below
   , header=True
   )
   df.write.mode("overwrite").saveAsTable("default.YourTableName")
   ```

## <a name="open-the-cognitive-services-wizard"></a>Öppna guiden Cognitive Services

1. Högerklicka på Spark-tabellen som du skapade i föregående procedur. Välj **Machine Learning**  >  **utöka med befintlig modell** för att öppna guiden.

   ![Skärm bild som visar markeringar för att öppna bedömnings guiden.](media/tutorial-cognitive-services/tutorial-cognitive-services-sentiment-00d.png)

2. En konfigurations panel visas och du uppmanas att välja en Cognitive Services modell. Välj **text analys-Attitydanalys**.

   ![Skärm bild som visar markering av en Cognitive Servicess modell.](media/tutorial-cognitive-services/tutorial-cognitive-services-sentiment-00e.png)

## <a name="provide-authentication-details"></a>Ange autentiseringsinformation

För att autentisera till Cognitive Services måste du referera till hemligheten för ditt nyckel valv. Följande indata är beroende av [nödvändiga steg](tutorial-configure-cognitive-services-synapse.md) som du bör ha slutfört innan den här punkten.

- **Azure-prenumeration**: Välj den Azure-prenumeration som nyckel valvet tillhör.
- **Cognitive Services konto**: ange den textanalys resurs som du ska ansluta till.
- **Azure Key Vault länkad tjänst**: som en del av de nödvändiga stegen har du skapat en länkad tjänst till din textanalys-resurs. Välj den här.
- **Hemligt namn**: Ange namnet på hemligheten i ditt nyckel valv som innehåller den nyckel som ska autentiseras för din Cognitive Services-resurs.

![Skärm bild som visar information om autentisering för ett nyckel valv.](media/tutorial-cognitive-services/tutorial-cognitive-services-sentiment-00f.png)

## <a name="configure-sentiment-analysis"></a>Konfigurera sentiment-analys

Konfigurera sedan sentiment-analysen. Välj följande information:
- **Språk**: Välj **engelska** som språk för den text som du vill utföra sentiment analys på.
- **Text kolumn**: Välj **comment (sträng)** som text kolumn i data uppsättningen som du vill analysera för att fastställa sentiment.

När du är klar väljer du **Öppna antecknings bok**. Detta skapar en antecknings bok för dig med PySpark-kod som utför sentiment-analysen med Azure Cognitive Services.

![Skärm bild som visar alternativ för att konfigurera sentiment-analys.](media/tutorial-cognitive-services/tutorial-cognitive-services-sentiment-00g.png)

## <a name="run-the-notebook"></a>Köra anteckningsboken

Den antecknings bok som du precis öppnade använder [mmlspark-biblioteket](https://github.com/Azure/mmlspark) för att ansluta till Cognitive Services. Med Azure Key Vault information som du har angett kan du på ett säkert sätt referera till dina hemligheter från den här upplevelsen utan att avslöja dem.

Nu kan du köra alla celler för att utöka dina data med sentiment. Välj **Kör alla**. 

Sentiment returneras som **positiva**, **negativa**, **neutrala** eller **blandade**. Du får också sannolikhet per sentiment. [Läs mer om sentiment-analys i Cognitive Services](../../cognitive-services/text-analytics/how-tos/text-analytics-how-to-sentiment-analysis.md).

![Skärm bild som visar sentiment-analys.](media/tutorial-cognitive-services/tutorial-cognitive-services-sentiment-00h.png)

## <a name="next-steps"></a>Nästa steg
- [Självstudie: avvikelse identifiering med Azure Cognitive Services](tutorial-cognitive-services-sentiment.md)
- [Självstudie: Machine Learning-modellens poängsättning i Azure Synapse dedikerade SQL-pooler](tutorial-sql-pool-model-scoring-wizard.md)
- [Maskininlärningsfunktioner i Azure Synapse Analytics](what-is-machine-learning.md)