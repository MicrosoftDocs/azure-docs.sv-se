---
title: 'Recept: intelligent konst utforskning med Cognitive Services för Big data'
titleSuffix: Azure Cognitive Services
description: Det här receptet visar hur du skapar en sökbar bild databas med hjälp av Azure Search och MMLSpark.
services: cognitive-services
author: mhamilton723
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: how-to
ms.date: 07/06/2020
ms.author: marhamil
ms.custom: devx-track-python
ms.openlocfilehash: 5a65ff28a38e42e05844063a330c0325f16b2247
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "94363297"
---
# <a name="recipe-intelligent-art-exploration-with-the-cognitive-services-for-big-data"></a>Recept: intelligent konst utforskning med Cognitive Services för Big data

I det här exemplet ska vi använda Cognitive Services för Big data för att lägga till intelligenta anteckningar i Open Access-samlingen från huvudstads Museum (uppfyllt). Detta gör att vi kan skapa en intelligent sökmotor med Azure Search även utan manuella anteckningar. 

## <a name="prerequisites"></a>Förutsättningar

* Du måste ha en prenumerations nyckel för Visuellt innehåll och Kognitiv sökning. Följ instruktionerna i [skapa ett Cognitive Services konto](../../cognitive-services-apis-create-account.md) för att prenumerera på visuellt innehåll och hämta din nyckel.
  > [!NOTE]
  > Information om priser finns i [Azure kognitiv sökning](https://azure.microsoft.com/services/search/#pricing).

## <a name="import-libraries"></a>Importera bibliotek

Kör följande kommando för att importera bibliotek för det här receptet.

```python
import os, sys, time, json, requests
from pyspark.ml import Transformer, Estimator, Pipeline
from pyspark.ml.feature import SQLTransformer
from pyspark.sql.functions import lit, udf, col, split
```

## <a name="set-up-subscription-keys"></a>Konfigurera prenumerations nycklar

Kör följande kommando för att ställa in variabler för tjänst nycklar. Infoga prenumerations nycklar för Visuellt innehåll och Azure Kognitiv sökning.

```python
VISION_API_KEY = 'INSERT_COMPUTER_VISION_SUBSCRIPTION_KEY'
AZURE_SEARCH_KEY = 'INSERT_AZURE_COGNITIVE_SEARCH_SUBSCRIPTION_KEY'
search_service = "mmlspark-azure-search"
search_index = "test"
```

## <a name="read-the-data"></a>Läs data

Kör följande kommando för att läsa in data från den uppfyllda öppna åtkomst samlingen.

```python
data = spark.read\
  .format("csv")\
  .option("header", True)\
  .load("wasbs://publicwasb@mmlspark.blob.core.windows.net/metartworks_sample.csv")\
  .withColumn("searchAction", lit("upload"))\
  .withColumn("Neighbors", split(col("Neighbors"), ",").cast("array<string>"))\
  .withColumn("Tags", split(col("Tags"), ",").cast("array<string>"))\
  .limit(25)
```

<a name="AnalyzeImages"></a>

## <a name="analyze-the-images"></a>Analysera avbildningarna

Kör följande kommando för att använda Visuellt innehåll på den uppfyllde samlingen Open Access-teckningar. Det innebär att du får visuella funktioner från bilderna.

```python
from mmlspark.cognitive import AnalyzeImage
from mmlspark.stages import SelectColumns

#define pipeline
describeImage = (AnalyzeImage()
  .setSubscriptionKey(VISION_API_KEY)
  .setLocation("eastus")
  .setImageUrlCol("PrimaryImageUrl")
  .setOutputCol("RawImageDescription")
  .setErrorCol("Errors")
  .setVisualFeatures(["Categories", "Tags", "Description", "Faces", "ImageType", "Color", "Adult"])
  .setConcurrency(5))

df2 = describeImage.transform(data)\
  .select("*", "RawImageDescription.*").drop("Errors", "RawImageDescription")
```

<a name="CreateSearchIndex"></a>

## <a name="create-the-search-index"></a>Skapa Sök indexet

Kör följande kommando för att skriva resultatet till Azure Search för att skapa en sökmotor av teckningarna med förrikade metadata från Visuellt innehåll.

```python
from mmlspark.cognitive import *
df2.writeToAzureSearch(
  subscriptionKey=AZURE_SEARCH_KEY,
  actionCol="searchAction",
  serviceName=search_service,
  indexName=search_index,
  keyCol="ObjectID"
)
```

## <a name="query-the-search-index"></a>Fråga Sök indexet

Kör följande kommando för att fråga Azure Search-indexet.

```python
url = 'https://{}.search.windows.net/indexes/{}/docs/search?api-version=2019-05-06'.format(search_service, search_index)
requests.post(url, json={"search": "Glass"}, headers = {"api-key": AZURE_SEARCH_KEY}).json()
```

## <a name="next-steps"></a>Nästa steg

Lär dig hur du använder [Cognitive Services för Big data för avvikelse identifiering](anomaly-detection.md).