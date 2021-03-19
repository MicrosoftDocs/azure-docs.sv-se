---
title: 'Självstudie: utveckla maskin inlärnings program med Microsoft Machine Learning för Apache Spark (för hands version)'
description: Lär dig hur du använder Microsoft Machine Learning för Apache Spark för att skapa maskin inlärnings program i Azure Synapse Analytics.
services: synapse-analytics
ms.service: synapse-analytics
ms.subservice: machine-learning
ms.topic: tutorial
ms.reviewer: ''
ms.date: 03/08/2021
author: ruxu
ms.author: ruxu
ms.openlocfilehash: a3899b83133b3f951547fae0b11c044bfa85a5fc
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "104589607"
---
# <a name="tutorial-build-machine-learning-applications-using-microsoft-machine-learning-for-apache-spark-preview"></a>Självstudie: utveckla maskin inlärnings program med Microsoft Machine Learning för Apache Spark (för hands version)

I den här artikeln får du lära dig hur du använder Microsoft Machine Learning för Apache Spark ([MMLSpark](https://github.com/Azure/mmlspark)) för att skapa maskin inlärnings program. MMLSpark utökar den distribuerade maskin inlärnings lösningen för Apache Spark genom att lägga till många djup inlärnings-och data vetenskaps verktyg, till exempel [Azure Cognitive Services](../../cognitive-services/big-data/cognitive-services-for-big-data.md), [opencv](https://opencv.org/), [LightGBM](https://github.com/Microsoft/LightGBM) och mycket annat.  Med MMLSpark kan du bygga kraftfulla och mycket skalbara förutsägelser och analys modeller från olika Spark-datakällor.
Synapse Spark tillhandahåller inbyggda MMLSpark-bibliotek inklusive:

- [Vowpal Wabbit](https://github.com/VowpalWabbit/vowpal_wabbit) – biblioteks tjänster för maskin inlärning för att aktivera text analys som sentiment-analys i tweets.
- [Cognitive Services i Spark](../../cognitive-services/big-data/cognitive-services-for-big-data.md) – för att kombinera funktionen i Azure Cognitive Services i SparkML-pipeliner för att kunna härleda lösnings design för kognitiva data modellerings tjänster som avvikelse identifiering.
- [LightBGM](https://github.com/Azure/mmlspark/blob/master/docs/lightgbm.md) – maskin inlärnings modell som gör det möjligt att träna modellen för förutsägelse analys som identifiering av ansikts-ID.
- Villkorsstyrd KNN – skalbara KNN-modeller med villkorliga frågor.
- [Http på Spark](https://github.com/Azure/mmlspark/blob/master/docs/http.md) – aktiverar distribuerade mikrotjänster-dirigering i integrerande Spark-och http-protokollbaserade hjälpmedel.

I den här självstudien beskrivs exempel med Azure Cognitive Services i MMLSpark för 

- Textanalys – Hämta sentiment (eller stämningen) för en uppsättning meningar.
- Visuellt innehåll – Hämta taggarna (beskrivningar med ett ord) som är associerade med en uppsättning bilder.
- Bildsökning i Bing – Sök på webben efter bilder som är relaterade till en fråga med naturligt språk.
- Avvikelse detektor – identifiera avvikelser inom en tids serie data.

Om du inte har en Azure-prenumeration kan du [skapa ett kostnads fritt konto innan du börjar](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Förutsättningar 

- [Azure Synapse Analytics-arbetsyta](../get-started-create-workspace.md) med ett Azure Data Lake Storage Gen2 lagrings konto konfigurerat som standard lagring. Du måste vara *data deltagare i Storage BLOB* för det data Lake Storage Gen2 fil system som du arbetar med.
- Spark-pool i din Azure Synapse Analytics-arbetsyta. Mer information finns i [skapa en spark-pool i Azure Synapse](../quickstart-create-sql-pool-studio.md).
- För konfigurations steg som beskrivs i självstudien [konfigurera Cognitive Services i Azure-Synapse](./tutorial-configure-cognitive-services-synapse.md).


## <a name="get-started"></a>Kom igång
Börja med att importera mmlspark-och configurate-tjänstenycklar.

```python
import mmlspark
mmlspark.__spark_package_version__ # current version: 1.0.0-rc3-6-a862d6b1-SNAPSHOT

from mmlspark.cognitive import *
from notebookutils import mssparkutils

# A general Cognitive Services key for Text Analytics and Computer Vision (or use separate keys that belong to each service)
service_key =  "ADD_YOUR_SUBSCRIPION_KEY" 
# A Bing Search v7 subscription key
bing_search_key = "ADD_YOUR_SUBSCRIPION_KEY" 
# An Anomaly Dectector subscription key
anomaly_key =  "ADD_YOUR_SUBSCRIPION_KEY" 


cognitive_service_key = mssparkutils.credentials.getSecret("keyvaultForSynapse", service_key)
bingsearch_service_key = mssparkutils.credentials.getSecret("keyvaultForSynapse", bing_search_key)
anomalydetector_key = mssparkutils.credentials.getSecret("keyvaultForSynapse", anomaly_key)

```

## <a name="text-analytics-sample"></a>Exempel på text analys

Tjänsten [textanalys](../../cognitive-services/text-analytics/index.yml) tillhandahåller flera algoritmer för att extrahera intelligenta insikter från text. Vi kan till exempel hitta sentiment för den angivna texten. Tjänsten returnerar ett resultat mellan 0,0 och 1,0 där låga poäng indikerar negativa sentiment och höga poäng indikerar positiv sentiment. Det här exemplet använder tre enkla meningar och returnerar sentiment för var och en.

```python
from pyspark.sql.functions import col

# Create a dataframe that's tied to it's column names
df_sentences = spark.createDataFrame([
  ("I am so happy today, its sunny!", "en-US"), 
  ("this is a dog", "en-US"), 
  ("I am frustrated by this rush hour traffic!", "en-US") 
], ["text", "language"])

# Run the Text Analytics service with options
sentiment = (TextSentiment()
    .setTextCol("text")
    .setLocation("eastasia")
    .setSubscriptionKey(cognitive_service_key)
    .setOutputCol("sentiment")
    .setErrorCol("error")
    .setLanguageCol("language"))

# Show the results of your text query in a table format

display(sentiment.transform(df_sentences).select("text", col("sentiment")[0].getItem("sentiment").alias("sentiment")))
```

### <a name="expected-results"></a>Förväntat resultat

|text | sentiment|
|--|--|
| Jag är frustrerad genom den här trafiken för skynda på den här tiden! | negativt |
| Detta är en hund | neutralt |
| Jag är så glad idag, dess solig! | positivt |

## <a name="computer-vision-sample"></a>Exempel på dator vision
[Visuellt innehåll](../../cognitive-services/computer-vision/index.yml) analyserar bilder för att identifiera strukturen som ansikten, objekt och beskrivningar av naturligt språk. I det här exemplet ska vi tagga följande bild. Taggar är en beskrivning av saker i bilden, till exempel igenkännliga objekt, människor, landskap och åtgärder.


![image](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-sample-data-files/master/ComputerVision/Images/objects.jpg)

```python
# Create a dataframe with the image URL
df_images = spark.createDataFrame([
        ("https://raw.githubusercontent.com/Azure-Samples/cognitive-services-sample-data-files/master/ComputerVision/Images/objects.jpg", )
    ], ["image", ])

# Run the Computer Vision service. Analyze Image extracts information from/about the images.
analysis = (AnalyzeImage()
    .setLocation("eastasia")
    .setSubscriptionKey(cognitive_service_key)
    .setVisualFeatures(["Categories","Color","Description","Faces","Objects","Tags"])
    .setOutputCol("analysis_results")
    .setImageUrlCol("image")
    .setErrorCol("error"))

# Show the results of what you wanted to pull out of the images.
display(analysis.transform(df_images).select("image", "analysis_results.description.tags"))
```
### <a name="expected-results"></a>Förväntat resultat

|image | tags|
|--|--|
| `https://raw.githubusercontent.com/Azure-Samples/cognitive-services-sample-data-files/master/ComputerVision/Images/objects.jpg` | [skridsko, person, man, Utomhus, händer, sport, skateboard, unga, bord, skjorta, luft, Park, pojke, sida, hopp, ramp, trick, att göra |

## <a name="bing-image-search-sample"></a>Exempel på bilds ökning i Bing
[Bildsökning i Bing](../../cognitive-services/bing-image-search/overview.md) söker på webben för att hämta avbildningar relaterade till en användares naturliga språk fråga. I det här exemplet använder vi en text fråga som söker efter bilder med citat tecken. Den returnerar en lista med bild-URL: er som innehåller foton som är relaterade till vår fråga.


```python
from pyspark.ml import PipelineModel

# Number of images Bing will return per query
imgsPerBatch = 2
# A list of offsets, used to page into the search results
offsets = [(i*imgsPerBatch,) for i in range(10)]
# Since web content is our data, we create a dataframe with options on that data: offsets
bingParameters = spark.createDataFrame(offsets, ["offset"])

# Run the Bing Image Search service with our text query
bingSearch = (BingImageSearch()
    .setSubscriptionKey(bingsearch_service_key)
    .setOffsetCol("offset")
    .setQuery("Martin Luther King Jr. quotes")
    .setCount(imgsPerBatch)
    .setOutputCol("images"))

# Transformer that extracts and flattens the richly structured output of Bing Image Search into a simple URL column
getUrls = BingImageSearch.getUrlTransformer("images", "url")
pipeline_bingsearch = PipelineModel(stages=[bingSearch, getUrls])

# Show the results of your search: image URLs
res_bingsearch = pipeline_bingsearch.transform(bingParameters)
display(res_bingsearch.dropDuplicates())
```

### <a name="expected-results"></a>Förväntat resultat

|image | 
|--|
|`http://everydaypowerblog.com/wp-content/uploads/2014/01/Martin-Luther-King-Jr.-Quotes-16.jpg` |
|`http://www.scrolldroll.com/wp-content/uploads/2017/06/6-25.png` |
| `http://abettertodaymedia.com/wp-content/uploads/2017/01/86783bd7a92960aedd058c91a1d10253.jpg`|
| `https://weneedfun.com/wp-content/uploads/2016/05/martin-luther-king-jr-quotes-11.jpg` |
| `http://www.sofreshandsogreen.com/wp-content/uploads/2012/01/martin-luther-king-jr-quote-sofreshandsogreendotcom.jpg` |
| `https://cdn.quotesgram.com/img/72/57/1104209728-martin_luther_king_jr_quotes_16.jpg` |
| `http://comicbookandbeyond.com/wp-content/uploads/2019/05/Martin-Luther-King-Jr.-Quotes.jpg` |
| `https://exposingthepain.files.wordpress.com/2015/01/martin-luther-king-jr-quotes-08.png` |
| `https://topmemes.me/wp-content/uploads/2020/01/Top-10-Martin-Luther-King-jr.-Quotes2-1024x538.jpg` |
| `http://img.picturequotes.com/2/581/580286/dr-martin-luther-king-jr-quote-1-picture-quote-1.jpg` |
| `http://parryz.com/wp-content/uploads/2017/06/Amazing-Martin-Luther-King-Jr-Quotes.jpg` |
| `http://everydaypowerblog.com/wp-content/uploads/2014/01/Martin-Luther-King-Jr.-Quotes1.jpg` |
| `https://lessonslearnedinlife.net/wp-content/uploads/2020/05/Martin-Luther-King-Jr.-Quotes-2020.jpg` |
| `https://quotesblog.net/wp-content/uploads/2015/10/Martin-Luther-King-Jr-Quotes-Wallpaper.jpg` |

## <a name="anomaly-detector-sample"></a>Exempel på avvikelse detektor

[Avvikelse detektor](../../cognitive-services/anomaly-detector/index.yml) är perfekt för att upptäcka överträdelser i dina tids serie data. I det här exemplet använder vi tjänsten för att hitta avvikelser i hela tids serien.

```python
from pyspark.sql.functions import lit

# Create a dataframe with the point data that Anomaly Detector requires
df_timeseriesdata = spark.createDataFrame([
    ("1972-01-01T00:00:00Z", 826.0),
    ("1972-02-01T00:00:00Z", 799.0),
    ("1972-03-01T00:00:00Z", 890.0),
    ("1972-04-01T00:00:00Z", 900.0),
    ("1972-05-01T00:00:00Z", 766.0),
    ("1972-06-01T00:00:00Z", 805.0),
    ("1972-07-01T00:00:00Z", 821.0),
    ("1972-08-01T00:00:00Z", 20000.0), # anomaly
    ("1972-09-01T00:00:00Z", 883.0),
    ("1972-10-01T00:00:00Z", 898.0),
    ("1972-11-01T00:00:00Z", 957.0),
    ("1972-12-01T00:00:00Z", 924.0),
    ("1973-01-01T00:00:00Z", 881.0),
    ("1973-02-01T00:00:00Z", 837.0),
    ("1973-03-01T00:00:00Z", 9000.0) # anomaly
], ["timestamp", "value"]).withColumn("group", lit("series1"))

# Run the Anomaly Detector service to look for irregular data
anamoly_detector = (SimpleDetectAnomalies()
  .setSubscriptionKey(anomalydetector_key)
  .setLocation("eastasia")
  .setTimestampCol("timestamp")
  .setValueCol("value")
  .setOutputCol("anomalies")
  .setGroupbyCol("group")
  .setGranularity("monthly"))

# Show the full results of the analysis with the anomalies marked as "True"
display(anamoly_detector.transform(df_timeseriesdata).select("timestamp", "value", "anomalies.isAnomaly"))

```

### <a name="expected-results"></a>Förväntat resultat

|timestamp | värde | isAnomaly |
|--|--|--|
| 1972-01-01T00:00:00Z|826,0|falskt|
|1972-02-01T00:00:00Z|799,0|falskt|
|1972-03-01T00:00:00Z|890,0|falskt|
|1972-04-01T00:00:00Z|900,0|falskt|
|1972-05-01T00:00:00Z|766,0|falskt|
|1972-06-01T00:00:00Z|805,0|falskt|
|1972-07-01T00:00:00Z|821,0|falskt|
|1972-08-01T00:00:00Z|20000,0|true|
|1972-09-01T00:00:00Z|883,0|falskt|
|1972-10-01T00:00:00Z|898,0|falskt|
|1972-11-01T00:00:00Z|957,0|falskt|
|1972-12-01T00:00:00Z|924,0|falskt|
|1973-01-01T00:00:00Z|881,0|falskt|
|1973-02-01T00:00:00Z|837,0|falskt|
|1973-03-01T00:00:00Z|9000,0|true|

## <a name="next-steps"></a>Nästa steg

* [Kolla Synapse-exempel Notebooks](https://github.com/Azure-Samples/Synapse/tree/main/Notebooks) 
* [MMLSpark GitHub-lagrings platsen](https://github.com/Azure/mmlspark)