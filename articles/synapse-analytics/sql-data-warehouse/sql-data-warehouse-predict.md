---
title: Score Machine Learning-modeller med PREDICT
description: Lär dig hur du utvärderar maskin inlärnings modeller med hjälp av funktionen T-SQL PREDICT i dedikerad SQL-pool.
services: synapse-analytics
author: anumjs
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: machine-learning
ms.date: 07/21/2020
ms.author: anjangsh
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: 9e7d45a588e60cd082f1eef43d1d1b6681b9e912
ms.sourcegitcommit: aacbf77e4e40266e497b6073679642d97d110cda
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/12/2021
ms.locfileid: "98117749"
---
# <a name="score-machine-learning-models-with-predict"></a>Score Machine Learning-modeller med PREDICT

Dedikerad SQL-pool ger dig möjlighet att Poäng modeller för maskin inlärning med det välkända T-SQL-språket. Med T-SQL [predict](/sql/t-sql/queries/predict-transact-sql?preserve-view=true&view=azure-sqldw-latest)kan du se till att dina befintliga Machine Learning-modeller tränas med historiska data och att de hamnar inom data lagrets säkra gränser. Funktionen PREDICT tar en [ONNX-modell (Open neurala Network Exchange)](https://onnx.ai/) och data som indata. Den här funktionen eliminerar steget för att flytta värdefull data utanför data lagret för poängsättning. Det syftar till att göra det möjligt för data experter att enkelt distribuera maskin inlärnings modeller med det välkända T-SQL-gränssnittet och samar beta smidigt med data experter som arbetar med rätt ramverk för deras uppgift.

> [!NOTE]
> Den här funktionen stöds för närvarande inte i SQL-poolen utan server.

Funktionen kräver att modellen tränas utanför Synapse-SQL. När du har skapat modellen kan du läsa in den i data lagret och visa den med T-SQL predict-syntaxen för att få insikter från data.

![predictoverview](./media/sql-data-warehouse-predict/datawarehouse-overview.png)

## <a name="training-the-model"></a>Träna modellen

Dedikerad SQL-pool förväntar sig en förtränad modell. Tänk på följande faktorer när du tränar en maskin inlärnings modell som används för att utföra förutsägelser i dedikerad SQL-pool.

- Dedikerad SQL-pool stöder endast ONNX format modeller. ONNX är ett modell format med öppen källkod som gör att du kan utbyta modeller mellan olika ramverk för att aktivera samverkan. Du kan konvertera befintliga modeller till ONNX-format med hjälp av ramverk som antingen stöder det internt eller som har konverterings paket tillgängliga. Till exempel [sklearn-Onnx](https://github.com/onnx/sklearn-onnx) -paketet convert scikit – lär dig modeller till Onnx. [ONNX GitHub-lagringsplatsen](https://github.com/onnx/tutorials#converting-to-onnx-format) innehåller en lista över ramverk som stöds och exempel.

   Om du använder [Automatisk ml](../../machine-learning/concept-automated-ml.md) -utbildning måste du ange *ENABLE_ONNX_COMPATIBLE_MODELS* parametern till true för att skapa en Onnx format modell. [Automatiserad Machine Learning Notebook](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-bank-marketing-all-features/auto-ml-classification-bank-marketing-all-features.ipynb) visar ett exempel på hur du använder automatisk ml för att skapa en Machine Learning-modell med ONNX-format.

- Följande data typer stöds för indata:
    - int, bigint, Real, Float
    - char, varchar, nvarchar

- Bedömnings data måste ha samma format som tränings data. Komplexa data typer som flerdimensionella matriser stöds inte av PREDICT. Så för utbildning ser du till att varje inmatning av modellen motsvarar en enda kolumn i bedömnings tabellen i stället för att skicka en enskild matris som innehåller alla indata.

- Kontrol lera att namnen och data typerna för modell indata matchar kolumn namnen och data typerna för de nya förutsägelse data. Visualisering av en ONNX-modell med olika verktyg för öppen källkod som finns tillgängliga online kan hjälpa dig med fel sökning.

## <a name="loading-the-model"></a>Läser in modellen

Modellen lagras i en dedikerad SQL-adresspool i användar tabellen som en hexadecimal sträng. Ytterligare kolumner som ID och beskrivning kan läggas till i modell tabellen för att identifiera modellen. Använd varbinary (max) som datatyp för modell kolumnen. Här är ett kod exempel för en tabell som kan användas för att lagra modeller:

```sql
-- Sample table schema for storing a model and related data
CREATE TABLE [dbo].[Models]
(
    [Id] [int] IDENTITY(1,1) NOT NULL,
    [Model] [varbinary](max) NULL,
    [Description] [varchar](200) NULL
)
WITH
(
    DISTRIBUTION = ROUND_ROBIN,
    HEAP
)
GO

```

När modellen har konverterats till en hexadecimal sträng och den angivna tabell definitionen använder du [kommandot Copy](/sql/t-sql/statements/copy-into-transact-sql?preserve-view=true&view=azure-sqldw-latest) eller PolyBase för att läsa in modellen i den DEDIKERADe SQL-adresspoolen. I följande kod exempel används kommandot Copy för att läsa in modellen.

```sql
-- Copy command to load hexadecimal string of the model from Azure Data Lake storage location
COPY INTO [Models] (Model)
FROM '<enter your storage location>'
WITH (
    FILE_TYPE = 'CSV',
    CREDENTIAL=(IDENTITY= 'Shared Access Signature', SECRET='<enter your storage key here>')
)
```

## <a name="scoring-the-model"></a>Bedömnings modellen

När modellen och data har lästs in i data lagret använder du funktionen **T-SQL predict** för att beräkna modellen. Se till att de nya indata har samma format som de utbildnings data som används för att skapa modellen. T-SQL PREDICT tar två indata: modell och nya straff indata och genererar nya kolumner för utdata. Modellen kan anges som en variabel, en literal eller en skalär sub_query. Använd [med common_table_expression](/sql/t-sql/queries/with-common-table-expression-transact-sql?preserve-view=true&view=azure-sqldw-latest) för att ange en namngiven resultat uppsättning för data parametern.

Exemplet nedan visar en exempel fråga med hjälp av funktionen förutsägelse. En ytterligare kolumn med namn *poängen* och data typen *float* skapas som innehåller förutsägelse resultatet. Alla kolumner för indata och utdatakolumner är tillgängliga för visning med SELECT-instruktionen. Mer information finns i [predict (Transact-SQL)](/sql/t-sql/queries/predict-transact-sql?preserve-view=true&view=azure-sqldw-latest).

```sql
-- Query for ML predictions
SELECT d.*, p.Score
FROM PREDICT(MODEL = (SELECT Model FROM Models WHERE Id = 1),
DATA = dbo.mytable AS d, RUNTIME = ONNX) WITH (Score float) AS p;
```

## <a name="next-steps"></a>Nästa steg

Mer information om PREDICT-funktionen finns i [predict (Transact-SQL)](/sql/t-sql/queries/predict-transact-sql?preserve-view=true&view=azure-sqldw-latest).