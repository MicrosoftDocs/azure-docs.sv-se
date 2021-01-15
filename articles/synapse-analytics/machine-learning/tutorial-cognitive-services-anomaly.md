---
title: 'Självstudie: avvikelse identifiering med Cognitive Services'
description: Själv studie kurs om hur du utnyttjar Cognitive Services för avvikelse identifiering i Synapse
services: synapse-analytics
ms.service: synapse-analytics
ms.subservice: machine-learning
ms.topic: tutorial
ms.reviewer: jrasnick, garye
ms.date: 11/20/2020
author: nelgson
ms.author: negust
ms.openlocfilehash: 5e7b914d459d2452704f93987ce1bf91bfba988c
ms.sourcegitcommit: d59abc5bfad604909a107d05c5dc1b9a193214a8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/14/2021
ms.locfileid: "98222215"
---
# <a name="tutorial-anomaly-detection-with-cognitive-services-preview"></a>Självstudie: avvikelse identifiering med Cognitive Services (för hands version)

I den här självstudien får du lära dig hur du enkelt kan utöka dina data i Azure Synapse med [Cognitive Services](../../cognitive-services/index.yml). Vi kommer att använda [avvikelse detektor](../../cognitive-services/anomaly-detector/index.yml) för att utföra avvikelse identifiering. En användare i Azure Synapse kan enkelt välja en tabell som ska utökas för identifiering av avvikelser.

Den här självstudiekursen omfattar:

> [!div class="checklist"]
> - Steg för att hämta en spark Table-datauppsättning som innehåller tids serie data.
> - Använd en guide upplevelse i Azure Synapse för att utöka data med hjälp av tjänsten för avvikelser detektor.

Om du inte har en Azure-prenumeration kan du [skapa ett kostnads fritt konto innan du börjar](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Förutsättningar

- [Azure Synapse Analytics-arbetsyta](../get-started-create-workspace.md) med ett ADLS Gen2 lagrings konto konfigurerat som standard lagring. Du måste vara **data deltagare i Storage BLOB** för det ADLS Gen2-filsystem som du arbetar med.
- Spark-pool i din Azure Synapse Analytics-arbetsyta. Mer information finns i [skapa en spark-pool i Azure Synapse](../quickstart-create-sql-pool-studio.md).
- Innan du kan använda den här självstudien måste du också slutföra de för konfigurerings steg som beskrivs i den här självstudien. [Konfigurera Cognitive Services i Azure-Synapse](tutorial-configure-cognitive-services-synapse.md).

## <a name="sign-in-to-the-azure-portal"></a>Logga in på Azure Portal

Logga in på [Azure-portalen](https://portal.azure.com/)

## <a name="create-a-spark-table"></a>Skapa en spark-tabell

Du behöver en spark-tabell för den här självstudien.

1. Ladda ned följande Notebook-fil som innehåller kod för att generera en spark-tabell: [prepare_anomaly_detector_data. ipynb](https://go.microsoft.com/fwlink/?linkid=2149577)

1. Ladda upp filen till din Azure Synapse-arbetsyta.
![Ladda upp antecknings bok](media/tutorial-cognitive-services/tutorial-cognitive-services-anomaly-00a.png)

1. Öppna anteckningsbok-filen och välj att **köra alla** celler.
![Skapa Spark-tabell](media/tutorial-cognitive-services/tutorial-cognitive-services-anomaly-00b.png)

1. En spark-tabell med namnet **anomaly_detector_testing_data** bör nu visas i standard Spark-databasen.

## <a name="launch-cognitive-services-wizard"></a>Guiden starta Cognitive Services

1. Högerklicka på Spark-tabellen som du skapade i föregående steg. Välj Machine Learning-> utöka med befintlig modell för att öppna guiden.
![Starta bedömnings guiden](media/tutorial-cognitive-services/tutorial-cognitive-services-anomaly-00g.png)

2. En konfigurations panel visas och du uppmanas att välja en Cognitive Services modell. Välj avvikelse detektor.

![Starta bedömnings guiden – steg 1](media/tutorial-cognitive-services/tutorial-cognitive-services-anomaly-00c.png)

## <a name="provide-authentication-details"></a>Ange autentiseringsinformation

För att kunna autentisera till Cognitive Services måste du referera till hemligheten som ska användas i Key Vault. Nedanstående indata är beroende av [nödvändiga steg](tutorial-configure-cognitive-services-synapse.md) som du bör ha slutfört innan det här steget.

- **Azure-prenumeration**: Välj den Azure-prenumeration som nyckel valvet tillhör.
- **Cognitive Services konto**: det här är den textanalys resurs som du ska ansluta till.
- **Azure Key Vault länkad tjänst**: som en del av de nödvändiga stegen har du skapat en länkad tjänst till din textanalys-resurs. Välj den här.
- **Hemligt namn**: det här är namnet på hemligheten i ditt nyckel valv som innehåller nyckeln för att autentisera till din Cognitive Services-resurs.

![Ange Azure Key Vault information](media/tutorial-cognitive-services/tutorial-cognitive-services-anomaly-00d.png)

## <a name="configure-anomaly-detection"></a>Konfigurera avvikelse identifiering

Sedan måste du konfigurera avvikelse identifiering. Ange följande information:

- Granularitet: den hastighet som data samplas på. Om dina data till exempel har ett värde i varje minut, blir din kornig het i minuter. Välj **månatlig** 

- Timestamp: kolumnen som representerar tiden för serien. Välj kolumn- **tidsstämpel**

- Timeseries värde: kolumn som representerar serie värdet vid den tidpunkt som anges i kolumnen tidsstämpelkolumn. Välj kolumn **värde**

- Gruppering: kolumn som grupperar serien. Det vill säga att alla rader som har samma värde i den här kolumnen utgör en tids serie. Välj kolumn **grupp**

När du är färdig väljer du **Öppna antecknings bok**. Då skapas en antecknings bok med PySpark-kod som utför avvikelse identifiering med Azure Cognitive Services.

![Konfigurera avvikelse detektor](media/tutorial-cognitive-services/tutorial-cognitive-services-anomaly-00e.png)

## <a name="open-notebook-and-run"></a>Öppna Notebook och kör

Den bärbara dator som du nyss öppnade använder [mmlspark-biblioteket](https://github.com/Azure/mmlspark) för att ansluta till kognitiva tjänster.

Med Azure Key Vault information som du har angett kan du på ett säkert sätt referera till dina hemligheter från den här upplevelsen utan att avslöja dem.

Du kan nu **köra alla** celler för att utföra avvikelse identifiering. Läs mer om [Cognitive Services-avvikelse detektor](../../cognitive-services/anomaly-detector/index.yml).

![Kör avvikelse identifiering](media/tutorial-cognitive-services/tutorial-cognitive-services-anomaly-00f.png)

## <a name="next-steps"></a>Nästa steg

- [Självstudie: sentiment-analys med Azure Cognitive Services](tutorial-cognitive-services-sentiment.md)
- [Självstudie: Machine Learning-modellens poängsättning i Azure Synapse dedikerade SQL-pooler](tutorial-sql-pool-model-scoring-wizard.md)
- [Machine Learning funktioner i Azure Synapse Analytics](what-is-machine-learning.md)