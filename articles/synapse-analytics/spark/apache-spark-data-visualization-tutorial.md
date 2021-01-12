---
title: Visualisera data med Apache Spark
description: Skapa omfattande data visualiseringar med Apache Spark och Azure Synapse Analytics-anteckningsböcker
services: synapse-analytics
author: midesa
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: machine-learning
ms.date: 10/20/2020
ms.author: midesa
ms.openlocfilehash: 8768b8f8c7bf70b184971abc6ce27e2193823dea
ms.sourcegitcommit: aacbf77e4e40266e497b6073679642d97d110cda
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/12/2021
ms.locfileid: "98121557"
---
# <a name="analyze-data-with-apache-spark"></a>Analysera data med Apache Spark

I den här självstudien får du lära dig hur du utför analys av exempel data med hjälp av Azure Open data uppsättningar och Apache Spark och visualiserar sedan resultatet i en bärbar Azure Synapse Studio-anteckningsbok.

I synnerhet kommer vi att analysera [New York-datauppsättningen Göteborg (NYC)](https://azure.microsoft.com/en-us/services/open-datasets/catalog/nyc-taxi-limousine-commission-yellow-taxi-trip-records/) . Data är tillgängliga via Azure Open data uppsättningar. Den här del mängden av data uppsättningen innehåller information om gula taxi resor, inklusive information om varje resa, start-och slut tid samt platser, kostnad och andra intressanta attribut.
  
## <a name="before-you-begin"></a>Innan du börjar
- Skapa en Apache Spark pool genom att följa [själv studie kursen skapa en Apache Spark pool](../articles/../quickstart-create-apache-spark-pool-studio.md) 

## <a name="download-and-prepare-the-data"></a>Hämta och förbereda data
1. Skapa en bärbar dator med PySpark-kärnan. Instruktioner finns i [skapa en antecknings bok](../quickstart-apache-spark-notebook.md#create-a-notebook). 
   
> [!Note]
> 
> På grund av PySpark-kärnan behöver du inte skapa några kontexter explicit. Spark-kontexten skapas automatiskt åt dig när du kör den första kod cellen.
>

2. I den här självstudien kommer vi att använda flera olika bibliotek som hjälp oss att visualisera data uppsättningen. För att utföra den här analysen måste du importera följande bibliotek: 

```python
import matplotlib.pyplot as plt
import seaborn as sns
import pandas as pd
```

3. Eftersom rå data är i ett Parquet-format kan du använda Spark-kontexten för att hämta filen till minnet som en dataframe direkt. Skapa en spark-dataframe genom att hämta data via Open data uppsättnings-API: et. Här använder vi Spark dataframe- *schemat på Läs* egenskaper för att härleda data typerna och schemat.

```python
from azureml.opendatasets import NycTlcYellow
from datetime import datetime
from dateutil import parser

end_date = parser.parse('2018-06-06')
start_date = parser.parse('2018-05-01')
nyc_tlc = NycTlcYellow(start_date=start_date, end_date=end_date)
df = nyc_tlc.to_spark_dataframe()
```

4. När data har lästs, vill vi göra en del inledande filtrering för att rensa data uppsättningen. Vi kan ta bort onödiga kolumner och lägga till ytterligare kolumner som extraherar viktig information. Dessutom kan vi även filtrera ut avvikelser i data uppsättningen.

```python
# Filter the dataset 
from pyspark.sql.functions import *

filtered_df = df.select('vendorID', 'passengerCount', 'tripDistance','paymentType', 'fareAmount', 'tipAmount'\
                                , date_format('tpepPickupDateTime', 'hh').alias('hour_of_day')\
                                , dayofweek('tpepPickupDateTime').alias('day_of_week')\
                                , dayofmonth(col('tpepPickupDateTime')).alias('day_of_month'))\
                            .filter((df.passengerCount > 0)\
                                & (df.tipAmount >= 0)\
                                & (df.fareAmount >= 1) & (df.fareAmount <= 250)\
                                & (df.tripDistance > 0) & (df.tripDistance <= 200))

filtered_df.createOrReplaceTempView("taxi_dataset")
```

## <a name="analyze-data"></a>Analysera data
 Som data analytiker har du en mängd olika verktyg som du kan använda för att extrahera insikter från data. I den här delen av självstudien kommer vi att gå igenom några användbara verktyg i Azure Synapse Analytics-anteckningsböcker. I den här analysen vill vi förstå de faktorer som ger högre taxi-tips för den valda perioden.

###  <a name="apache-spark-sql-magic"></a>Apache Spark SQL Magic 
Först ska vi utföra analys av undersöknings data genom att Apache Spark SQL-och Magic-kommandon med Synapse Notebook. När vi har vår fråga kommer vi att visualisera resultaten med hjälp av den inbyggda ```chart options``` funktionen. 

1. I din antecknings bok skapar du en ny cell och kopierar koden nedan. Med den här frågan vill vi förstå hur de genomsnittliga Tip-beloppen har ändrats under den period som vi har valt. Den här frågan hjälper också oss att identifiera andra användbara insikter, inklusive minsta/högsta tips belopp per dag och den genomsnittliga pris nivån.
   
```sql
%%sql
SELECT 
    day_of_month
    , MIN(tipAmount) AS minTipAmount
    , MAX(tipAmount) AS maxTipAmount
    , AVG(tipAmount) AS avgTipAmount
    , AVG(fareAmount) as fareAmount
FROM taxi_dataset 
GROUP BY day_of_month
ORDER BY day_of_month ASC
```

2. När vår fråga har körts klart kan vi visualisera resultaten genom att växla till **diagramvyn**. I det här exemplet skapar vi ett **linje diagram** genom att ange ```day_of_month``` fältet som **nyckel** och ```avgTipAmount``` som **värde**. När du har gjort inställningarna klickar du på **Använd** för att uppdatera diagrammet. 
   
## <a name="visualize-data"></a>Visualisera data
Förutom de inbyggda diagram alternativen för antecknings böcker kan du också använda populära bibliotek med öppen källkod för att skapa dina egna visualiseringar. I följande exempel kommer vi att använda Seaborn och matplotlib som ofta använder python-bibliotek för data visualisering. 

> [!Note]
> 
> Som standard innehåller alla Azure Synapse Analytics Apache Spark-poolen en uppsättning ofta använda och standard bibliotek. Du kan visa en fullständig lista över bibliotek i [Azure Synapse runtime](../spark/apache-spark-version-support.md). handlingar. För att göra tredje part eller lokalt skapad kod tillgänglig för dina program kan du dessutom [Installera ett bibliotek](../spark/apache-spark-azure-portal-add-libraries.md) på någon av Spark-poolerna.
>

1. För att göra utvecklingen enklare och billigare kommer vi att se exempel data uppsättningen. Vi kommer att använda den inbyggda Apache Spark samplings funktionen. Dessutom kräver både Seaborn och matplotlib en Pandas-dataframe eller numpy-matris. För att få en Pandas-dataframe kommer vi att använda ```toPandas()``` kommandot för att konvertera vårt dataframe.

```python
# To make development easier, faster and less expensive down sample for now
sampled_taxi_df = filtered_df.sample(True, 0.001, seed=1234)

# The charting package needs a Pandas dataframe or numpy array do the conversion
sampled_taxi_pd_df = sampled_taxi_df.toPandas()
```

1. Först vill vi förstå fördelningen av tips i vår data uppsättning. Vi använder matplotlib för att skapa ett histogram som visar distributionen av Tip-mängden och antalet. Utifrån fördelningen kan vi se att tipsen skevas mot belopp som är mindre än eller lika med $10.

```python
# Look at tips by count histogram using Matplotlib

ax1 = sampled_taxi_pd_df['tipAmount'].plot(kind='hist', bins=25, facecolor='lightblue')
ax1.set_title('Tip amount distribution')
ax1.set_xlabel('Tip Amount ($)')
ax1.set_ylabel('Counts')
plt.suptitle('')
plt.show()
```

![Histogram med tips](./media/apache-spark-machine-learning-mllib-notebook/histogram.png)

1. Nu vill vi förstå relationen mellan tipsen för en specifik resa och veckodag. Vi använder Seaborn för att skapa ett låddiagram som sammanfattar trender för varje veckodag. 

```python
# View the distribution of tips by day of week using Seaborn
ax = sns.boxplot(x="day_of_week", y="tipAmount",data=sampled_taxi_pd_df, showfliers = False)
ax.set_title('Tip amount distribution per day')
ax.set_xlabel('Day of Week')
ax.set_ylabel('Tip Amount ($)')
plt.show()

```
![Fördelning av tips per dag](./media/apache-spark-data-viz/data-analyst-tutorial-per-day.png)

4. Nu kan en annan hypotes som vi kan vara på att det finns en positiv relation mellan antalet passagerare och det totala taxi spets beloppet. För att verifiera den här relationen kommer vi att köra följande kod för att generera en box som illustrerar fördelningen av tips för varje antal passagerare. 
   
```python
# How many passengers tipped by various amounts 
ax2 = sampled_taxi_pd_df.boxplot(column=['tipAmount'], by=['passengerCount'])
ax2.set_title('Tip amount by Passenger count')
ax2.set_xlabel('Passenger count')
ax2.set_ylabel('Tip Amount ($)')
ax2.set_ylim(0,30)
plt.suptitle('')
plt.show()
```
![Box-rityta](./media/apache-spark-machine-learning-mllib-notebook/box-whisker-plot.png)

1. Sist vill vi förstå relationen mellan priss rabatten. Utifrån resultaten kan vi se att det finns flera observationer där andra inte är tips. Vi kan dock också se en positiv relation mellan de totala avgifterna för pris och pris.
   
```python
# Look at the relationship between fare and tip amounts

ax = sampled_taxi_pd_df.plot(kind='scatter', x= 'fareAmount', y = 'tipAmount', c='blue', alpha = 0.10, s=2.5*(sampled_taxi_pd_df['passengerCount']))
ax.set_title('Tip amount by Fare amount')
ax.set_xlabel('Fare Amount ($)')
ax.set_ylabel('Tip Amount ($)')
plt.axis([-2, 80, -2, 20])
plt.suptitle('')
plt.show()
```
![Punkt diagram](./media/apache-spark-machine-learning-mllib-notebook/scatter.png)

## <a name="shut-down-the-spark-instance"></a>Stäng av Spark-instansen

När du är klar med att köra programmet stänger du antecknings boken för att frigöra resurserna genom att stänga fliken eller välja **Avsluta session** från status panelen längst ned i antecknings boken.

## <a name="see-also"></a>Se även

- [Översikt: Apache Spark på Azure Synapse Analytics](apache-spark-overview.md)
- [Bygg en Machine Learning-modell med Apache SparkML](../spark/apache-spark-machine-learning-mllib-notebook.md)

## <a name="next-steps"></a>Nästa steg

- [Azure Synapse Analytics](../index.yml)
- [Apache Spark officiell dokumentation](https://spark.apache.org/docs/latest/)