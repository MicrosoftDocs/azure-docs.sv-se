---
title: 'Självstudie: avvikelse identifiering med Cognitive Services'
description: Lär dig hur du använder Cognitive Services för avvikelse identifiering i Azure Synapse Analytics.
services: synapse-analytics
ms.service: synapse-analytics
ms.subservice: machine-learning
ms.topic: tutorial
ms.reviewer: jrasnick, garye
ms.date: 11/20/2020
author: nelgson
ms.author: negust
ms.openlocfilehash: c54300bf37f6f4526c525b1502d902e5f4336ed7
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98943512"
---
# <a name="tutorial-anomaly-detection-with-cognitive-services-preview"></a>Självstudie: avvikelse identifiering med Cognitive Services (för hands version)

I den här självstudien får du lära dig hur du enkelt kan utöka dina data i Azure Synapse Analytics med [azure Cognitive Services](../../cognitive-services/index.yml). Du använder [avvikelse detektor](../../cognitive-services/anomaly-detector/index.yml) för att hitta avvikelser. En användare i Azure Synapse kan enkelt välja en tabell som ska utökas för identifiering av avvikelser.

Den här självstudiekursen omfattar:

> [!div class="checklist"]
> - Steg för att hämta en spark Table-datauppsättning som innehåller tids serie data.
> - Använd en guide upplevelse i Azure Synapse för att utöka data med hjälp av avvikelse igenkänning i Cognitive Services.

Om du inte har en Azure-prenumeration kan du [skapa ett kostnads fritt konto innan du börjar](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Förutsättningar

- [Azure Synapse Analytics-arbetsyta](../get-started-create-workspace.md) med ett Azure Data Lake Storage Gen2 lagrings konto konfigurerat som standard lagring. Du måste vara *data deltagare i Storage BLOB* för det data Lake Storage Gen2 fil system som du arbetar med.
- Spark-pool i din Azure Synapse Analytics-arbetsyta. Mer information finns i [skapa en spark-pool i Azure Synapse](../quickstart-create-sql-pool-studio.md).
- Slutför konfigurationen av stegen i själv studie kursen [konfigurera Cognitive Services i Azure Synapse](tutorial-configure-cognitive-services-synapse.md) .

## <a name="sign-in-to-the-azure-portal"></a>Logga in på Azure Portal

Logga in på [Azure-portalen](https://portal.azure.com/).

## <a name="create-a-spark-table"></a>Skapa en spark-tabell

Du behöver en spark-tabell för den här självstudien.

1. Ladda ned följande Notebook-fil som innehåller kod för att generera en spark-tabell: [prepare_anomaly_detector_data. ipynb](https://go.microsoft.com/fwlink/?linkid=2149577).

1. Ladda upp filen till din Azure Synapse-arbetsyta.

   ![Skärm bild som visar val för att ladda upp en bärbar dator.](media/tutorial-cognitive-services/tutorial-cognitive-services-anomaly-00a.png)

1. Öppna anteckningsbok-filen och välj **Kör alla** för att köra alla celler.

   ![Skärm bild som visar markeringar för att skapa en spark-tabell.](media/tutorial-cognitive-services/tutorial-cognitive-services-anomaly-00b.png)

1. En spark-tabell med namnet **anomaly_detector_testing_data** bör nu visas i standard Spark-databasen.

## <a name="open-the-cognitive-services-wizard"></a>Öppna guiden Cognitive Services

1. Högerklicka på Spark-tabellen som du skapade i föregående steg. Välj **Machine Learning**  >  **utöka med befintlig modell** för att öppna guiden.

   ![Skärm bild som visar markeringar för att öppna bedömnings guiden.](media/tutorial-cognitive-services/tutorial-cognitive-services-anomaly-00g.png)

2. En konfigurations panel visas och du uppmanas att välja en Cognitive Services modell. Välj **avvikelse detektor**.

   ![Skärm bild som visar val av avvikelse detektor som en modell.](media/tutorial-cognitive-services/tutorial-cognitive-services-anomaly-00c.png)

## <a name="provide-authentication-details"></a>Ange autentiseringsinformation

Om du vill autentisera till Cognitive Services måste du referera till hemligheten i ditt nyckel valv. Följande indata är beroende av de [nödvändiga stegen](tutorial-configure-cognitive-services-synapse.md) som du borde ha slutfört innan den här punkten.

- **Azure-prenumeration**: Välj den Azure-prenumeration som nyckel valvet tillhör.
- **Cognitive Services konto**: ange den textanalys resurs som du ska ansluta till.
- **Azure Key Vault länkad tjänst**: som en del av de nödvändiga stegen har du skapat en länkad tjänst till din textanalys-resurs. Välj den här.
- **Hemligt namn**: Ange namnet på hemligheten i ditt nyckel valv som innehåller den nyckel som ska autentiseras för din Cognitive Services-resurs.

![Skärm bild som visar information om autentisering för ett nyckel valv.](media/tutorial-cognitive-services/tutorial-cognitive-services-anomaly-00d.png)

## <a name="configure-anomaly-detector"></a>Konfigurera avvikelse detektor

Ange följande information för att konfigurera avvikelse detektor:

- **Granularitet**: den hastighet med vilken data samplas. Välj **varje månad**. 

- **Tidsstämpelkolumn**: kolumnen som representerar tiden för serien. Välj **timestamp (sträng)**.

- **Timeseries värde kolumn**: kolumnen som representerar serie värdet vid den tidpunkt som anges i kolumnen tidsstämpelkolumn. Välj **värde (Double)**.

- **Gruppering av kolumn**: kolumnen som grupperar serien. Det vill säga att alla rader som har samma värde i den här kolumnen utgör en tids serie. Välj **grupp (sträng)**.

När du är klar väljer du **Öppna antecknings bok**. Då skapas en antecknings bok med PySpark-kod som använder Azure Cognitive Services för att identifiera avvikelser.

![Skärm bild som visar konfigurations information för avvikelse detektor.](media/tutorial-cognitive-services/tutorial-cognitive-services-anomaly-00e.png)

## <a name="run-the-notebook"></a>Köra anteckningsboken

Den antecknings bok som du precis öppnade använder [mmlspark-biblioteket](https://github.com/Azure/mmlspark) för att ansluta till Cognitive Services. Med Azure Key Vault information som du har angett kan du på ett säkert sätt referera till dina hemligheter från den här upplevelsen utan att avslöja dem.

Du kan nu köra alla celler för att utföra avvikelse identifiering. Välj **Kör alla**. [Lär dig mer om avvikelse detektor i Cognitive Services](../../cognitive-services/anomaly-detector/index.yml).

![Skärm bild som visar avvikelse identifiering.](media/tutorial-cognitive-services/tutorial-cognitive-services-anomaly-00f.png)

## <a name="next-steps"></a>Nästa steg

- [Självstudie: sentiment-analys med Azure Cognitive Services](tutorial-cognitive-services-sentiment.md)
- [Självstudie: Machine Learning-modellens poängsättning i Azure Synapse dedikerade SQL-pooler](tutorial-sql-pool-model-scoring-wizard.md)
- [Maskininlärningsfunktioner i Azure Synapse Analytics](what-is-machine-learning.md)
