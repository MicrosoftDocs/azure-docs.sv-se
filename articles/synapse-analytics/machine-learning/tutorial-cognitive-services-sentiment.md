---
title: 'Självstudie: sentiment-analys med Cognitive Services'
description: Självstudie för hur du utnyttjar Cognitive Services för sentiment-analys i Synapse
services: synapse-analytics
ms.service: synapse-analytics
ms.subservice: machine-learning
ms.topic: tutorial
ms.reviewer: jrasnick, garye
ms.date: 11/20/2020
author: nelgson
ms.author: negust
ms.openlocfilehash: 6a4833cf0d73939e01fd3e3e7263c6cba3c0a28a
ms.sourcegitcommit: d59abc5bfad604909a107d05c5dc1b9a193214a8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/14/2021
ms.locfileid: "98222198"
---
# <a name="tutorial-sentiment-analysis-with-cognitive-services-preview"></a>Självstudie: sentiment-analys med Cognitive Services (för hands version)

I den här självstudien får du lära dig hur du enkelt kan utöka dina data i Azure Synapse med [Cognitive Services](../../cognitive-services/index.yml). Vi använder [textanalys](../../cognitive-services/text-analytics/index.yml) funktionerna för att utföra sentiment-analys. En användare i Azure Synapse kan enkelt välja en tabell som innehåller en text kolumn som ska utökas med sentiment. Dessa sentiment kan vara positiva, negativa, blandade eller neutrala och en sannolikhet kommer också att returneras.

Den här självstudiekursen omfattar:

> [!div class="checklist"]
> - Steg för att hämta en spark Table-datauppsättning som innehåller text kolumn för sentiment-analys.
> - Använd en guide upplevelse i Azure Synapse för att utöka data med hjälp av Textanalys Cognitive Services.

Om du inte har en Azure-prenumeration kan du [skapa ett kostnads fritt konto innan du börjar](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Förutsättningar

- [Azure Synapse Analytics-arbetsyta](../get-started-create-workspace.md) med ett ADLS Gen2 lagrings konto konfigurerat som standard lagring. Du måste vara **data deltagare i Storage BLOB** för det ADLS Gen2-filsystem som du arbetar med.
- Spark-pool i din Azure Synapse Analytics-arbetsyta. Mer information finns i [skapa en spark-pool i Azure Synapse](../quickstart-create-sql-pool-studio.md).
- Innan du kan använda den här självstudien måste du också slutföra de för konfigurerings steg som beskrivs i den här självstudien. [Konfigurera Cognitive Services i Azure-Synapse](tutorial-configure-cognitive-services-synapse.md).

## <a name="sign-in-to-the-azure-portal"></a>Logga in på Azure Portal

Logga in på [Azure-portalen](https://portal.azure.com/)

## <a name="create-a-spark-table"></a>Skapa en spark-tabell

Du behöver en spark-tabell för den här självstudien.

1. Ladda ned följande CSV-fil som innehåller en data uppsättning för text analys: [FabrikamComments.csv](https://github.com/Kaiqb/KaiqbRepo0731190208/blob/master/CognitiveServices/TextAnalytics/FabrikamComments.csv)

1. Ladda upp filen till ditt Azure Synapse ADLSGen2-lagrings konto.
![Ladda upp data](media/tutorial-cognitive-services/tutorial-cognitive-services-sentiment-00a.png)

1. Skapa en spark-tabell från CSV-filen genom att högerklicka på filen och välja **ny anteckningsbok-> skapa Spark-tabell**.
![Skapa Spark-tabell](media/tutorial-cognitive-services/tutorial-cognitive-services-sentiment-00b.png)

1. Namnge tabellen i cellen Code och kör antecknings boken i en spark-pool. Kom ihåg att ange "header = true".
![Kör antecknings boken](media/tutorial-cognitive-services/tutorial-cognitive-services-sentiment-00c.png)

```python
%%pyspark
df = spark.read.load('abfss://default@azuresynapsesa.dfs.core.windows.net/data/FabrikamComments.csv', format='csv'
## If header exists uncomment line below
, header=True
)
df.write.mode("overwrite").saveAsTable("default.YourTableName")
```

## <a name="launch-cognitive-services-wizard"></a>Guiden starta Cognitive Services

1. Högerklicka på Spark-tabellen som du skapade i föregående steg. Välj Machine Learning-> utöka med befintlig modell för att öppna guiden.
![Starta bedömnings guiden](media/tutorial-cognitive-services/tutorial-cognitive-services-sentiment-00d.png)

2. En konfigurations panel visas och du uppmanas att välja en Cognitive Services modell. Välj text analys-Attitydanalys.

![Starta bedömnings guiden – steg 1](media/tutorial-cognitive-services/tutorial-cognitive-services-sentiment-00e.png)

## <a name="provide-authentication-details"></a>Ange autentiseringsinformation

För att kunna autentisera till Cognitive Services måste du referera till hemligheten som ska användas i Key Vault. Nedanstående indata är beroende av [nödvändiga steg](tutorial-configure-cognitive-services-synapse.md) som du bör ha slutfört innan det här steget.

- **Azure-prenumeration**: Välj den Azure-prenumeration som nyckel valvet tillhör.
- **Cognitive Services konto**: det här är den textanalys resurs som du ska ansluta till.
- **Azure Key Vault länkad tjänst**: som en del av de nödvändiga stegen har du skapat en länkad tjänst till din textanalys-resurs. Välj den här.
- **Hemligt namn**: det här är namnet på hemligheten i ditt nyckel valv som innehåller nyckeln för att autentisera till din Cognitive Services-resurs.

![Ange Azure Key Vault information](media/tutorial-cognitive-services/tutorial-cognitive-services-sentiment-00f.png)

## <a name="configure-sentiment-analysis"></a>Konfigurera Attitydanalys

Sedan måste du konfigurera sentiment-analysen. Välj följande information:
- **Språk**: Välj språket för den text som du vill utföra sentiment analys på. Välj **en**.
- **Text kolumn**: det här är text kolumnen i din data uppsättning som du vill analysera för att fastställa sentiment. Markera tabell kolumn **kommentar**.

När du är färdig klickar du på **Öppna antecknings bok**. Då skapas en antecknings bok med PySpark-kod som utför sentiment-analysen med Azure Cognitive Services.

![Konfigurera Attitydanalys](media/tutorial-cognitive-services/tutorial-cognitive-services-sentiment-00g.png)

## <a name="open-notebook-and-run"></a>Öppna Notebook och kör

Den bärbara dator som du nyss öppnade använder [mmlspark-biblioteket](https://github.com/Azure/mmlspark) för att ansluta till kognitiva tjänster.

Med Azure Key Vault information som du har angett kan du på ett säkert sätt referera till dina hemligheter från den här upplevelsen utan att avslöja dem.

Nu kan du **köra alla** celler för att utöka dina data med sentiment. Sentiment kommer att returneras som positiv/negativ/neutral/Mixed, och du får även sannolikhet per sentiment. Läs mer om [analys av Cognitive Services-sentiment](../../cognitive-services/text-analytics/how-tos/text-analytics-how-to-sentiment-analysis.md).

![Kör Attitydanalys](media/tutorial-cognitive-services/tutorial-cognitive-services-sentiment-00h.png)

## <a name="next-steps"></a>Nästa steg
- [Självstudie: avvikelse identifiering med Azure Cognitive Services](tutorial-cognitive-services-sentiment.md)
- [Självstudie: Machine Learning-modellens poängsättning i Azure Synapse dedikerade SQL-pooler](tutorial-sql-pool-model-scoring-wizard.md)
- [Machine Learning funktioner i Azure Azure Synapse Analytics](what-is-machine-learning.md)