---
title: 'Recept: förutsägande underhåll med Cognitive Services för Big data'
titleSuffix: Azure Cognitive Services
description: Den här snabb starten visar hur du utför distribuerad avvikelse identifiering med Cognitive Services för Big data
services: cognitive-services
author: mhamilton723
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: how-to
ms.date: 07/06/2020
ms.author: marhamil
ms.custom: devx-track-python
ms.openlocfilehash: d2995f39bc61ae5bb87abafd674f411271e57ca2
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "94366289"
---
# <a name="recipe-predictive-maintenance-with-the-cognitive-services-for-big-data"></a>Recept: förutsägande underhåll med Cognitive Services för Big data

Det här receptet visar hur du kan använda Azure Synapse Analytics och Cognitive Services på Apache Spark för förutsägande underhåll av IoT-enheter. Vi följer tillsammans med exemplet på [CosmosDB och Synapse-länken](https://github.com/Azure-Samples/cosmosdb-synapse-link-samples) . För att det ska vara enkelt bör vi i det här receptet läsa data direkt från en CSV-fil i stället för att hämta strömmade data via CosmosDB och Synapse-länken. Vi rekommenderar starkt att du tittar på exemplet på Synapse-länken.

## <a name="hypothetical-scenario"></a>Hypotetiskt scenario

Det hypotetiska scenariot är en kraft anläggning där IoT-enheter övervakar [ång turbiner](https://en.wikipedia.org/wiki/Steam_turbine). IoTSignals-samlingen har varv per minut (RPM) och megawatts-data (MW) för varje turbin. Signaler från ång turbiner analyseras och avvikande signaler identifieras.

Det kan finnas extrem värden i data med slumpmässig frekvens. I dessa situationer kommer RPM-värden att bli upp och MW-utdata går nedåt, för krets skydd. Idén är att du ska kunna se data olika samtidigt, men med olika signaler.

## <a name="prerequisites"></a>Förutsättningar

* En Azure-prenumeration – [skapa en kostnads fritt](https://azure.microsoft.com/free/cognitive-services)
* [Azure Synapse-arbetsyta](../../../synapse-analytics/quickstart-create-workspace.md) konfigurerad med en [Server lös Apache Spark pool](../../../synapse-analytics/quickstart-create-apache-spark-pool-portal.md)

## <a name="setup"></a>Konfiguration

### <a name="create-an-anomaly-detector-resource"></a>Skapa en resurs för avvikelse detektor

Azure-Cognitive Services representeras av Azure-resurser som du prenumererar på. Skapa en resurs för Translator med hjälp av [Azure Portal](../../cognitive-services-apis-create-account.md) eller [Azure CLI](../../cognitive-services-apis-create-account-cli.md). Du kan även:

- Visa en befintlig resurs i  [Azure Portal](https://portal.azure.com/).

Anteckna slut punkten och nyckeln för den här resursen. du behöver den i den här hand boken.

## <a name="enter-your-service-keys"></a>Ange dina tjänst nycklar

Vi börjar med att lägga till nyckeln och platsen.

```python
service_key = None # Paste your anomaly detector key here
location = None # Paste your anomaly detector location here

assert (service_key is not None)
assert (location is not None)
```

## <a name="read-data-into-a-dataframe"></a>Läs data till en DataFrame

Nu ska vi läsa IoTSignals-filen till en DataFrame. Öppna en ny antecknings bok på din Synapse-arbetsyta och skapa en DataFrame från filen.

```python
df_device_info = spark.read.csv("wasbs://publicwasb@mmlspark.blob.core.windows.net/iot/IoTSignals.csv", header=True, inferSchema=True)
```

### <a name="run-anomaly-detection-using-cognitive-services-on-spark"></a>Kör avvikelse identifiering med Cognitive Services i Spark

Målet är att hitta instanser där signalerna från IoT-enheterna utgjorde avvikande värden så att vi kan se när något går fel och förutsäger förebyggande underhåll. Vi gör det genom att använda avvikelse detektor på Spark:

```python
from pyspark.sql.functions import col, struct
from mmlspark.cognitive import SimpleDetectAnomalies
from mmlspark.core.spark import FluentAPI

detector = (SimpleDetectAnomalies()
    .setSubscriptionKey(service_key)
    .setLocation(location)
    .setOutputCol("anomalies")
    .setGroupbyCol("grouping")
    .setSensitivity(95)
    .setGranularity("secondly"))

df_anomaly = (df_signals
    .where(col("unitSymbol") == 'RPM')
    .withColumn("timestamp", col("dateTime").cast("string"))
    .withColumn("value", col("measureValue").cast("double"))
    .withColumn("grouping", struct("deviceId"))
    .mlTransform(detector)).cache()

df_anomaly.createOrReplaceTempView('df_anomaly')
```

Låt oss ta en titt på data:

```python
df_anomaly.select("timestamp","value","deviceId","anomalies.isAnomaly").show(3)
```

Den här cellen ska ge ett resultat som ser ut så här:

| timestamp           |   värde | deviceId   | isAnomaly   |
|:--------------------|--------:|:-----------|:------------|
| 2020-05-01 18:33:51 |    3174 | dev-7      | Falskt       |
| 2020-05-01 18:33:52 |    2976 | dev-7      | Falskt       |
| 2020-05-01 18:33:53 |    2714 | dev-7      | Falskt       |


 ## <a name="visualize-anomalies-for-one-of-the-devices"></a>Visualisera avvikelser för en av enheterna

IoTSignals.csv har signaler från flera IoT-enheter. Vi fokuserar på en speciell enhet och visualiserar avvikande utdata från enheten.

```python
df_anomaly_single_device = spark.sql("""
select
  timestamp,
  measureValue,
  anomalies.expectedValue,
  anomalies.expectedValue + anomalies.upperMargin as expectedUpperValue,
  anomalies.expectedValue - anomalies.lowerMargin as expectedLowerValue,
  case when anomalies.isAnomaly=true then 1 else 0 end as isAnomaly
from
  df_anomaly
where deviceid = 'dev-1' and timestamp < '2020-04-29'
order by timestamp
limit 200""")
```

Nu när vi har skapat en dataframe som representerar avvikelserna för en viss enhet kan vi visualisera dessa avvikelser:

```python
import matplotlib.pyplot as plt
from pyspark.sql.functions import col

adf = df_anomaly_single_device.toPandas()
adf_subset = df_anomaly_single_device.where(col("isAnomaly") == 1).toPandas()

plt.figure(figsize=(23,8))
plt.plot(adf['timestamp'],adf['expectedUpperValue'], color='darkred', linestyle='solid', linewidth=0.25, label='UpperMargin')
plt.plot(adf['timestamp'],adf['expectedValue'], color='darkgreen', linestyle='solid', linewidth=2, label='Expected Value')
plt.plot(adf['timestamp'],adf['measureValue'], 'b', color='royalblue', linestyle='dotted', linewidth=2, label='Actual')
plt.plot(adf['timestamp'],adf['expectedLowerValue'],  color='black', linestyle='solid', linewidth=0.25, label='Lower Margin')
plt.plot(adf_subset['timestamp'],adf_subset['measureValue'], 'ro', label = 'Anomaly')
plt.legend()
plt.title('RPM Anomalies with Confidence Intervals')
plt.show()
```

Om det lyckas kommer dina utdata att se ut så här:

![Avvikelse observations område](../media/anomaly-output.png)

## <a name="next-steps"></a>Nästa steg

Lär dig hur du gör förutsägelse underhåll i stor skala med Azure Cognitive Services, Azure Synapse Analytics och Azure CosmosDB. Mer information finns i det fullständiga exemplet på [GitHub](https://github.com/Azure-Samples/cosmosdb-synapse-link-samples).