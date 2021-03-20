---
title: Azure Synapse Studio-notebook-filer
description: I den här självstudien får du en översikt över hur du skapar en Power BI instrument panel med Apache Spark och en server lös SQL-pool.
services: synapse-analytics
author: midesa
ms.author: midesa
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.subservice: spark
ms.topic: tutorial
ms.date: 11/16/2020
ms.openlocfilehash: 53fb256d3a0fba1cc10cafb239ff7f7bb21ac4eb
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98121081"
---
# <a name="tutorial-create-a-power-bi-report-using-apache-spark-and-azure-synapse-analytics"></a>Självstudie: skapa en Power BI rapport med Apache Spark och Azure Synapse Analytics

Organisationer behöver ofta bearbeta stora mängder data innan de betjänar viktiga affärs intressenter. I den här självstudien får du lära dig hur du utnyttjar de integrerade upplevelserna i Azure Synapse Analytics för att bearbeta data med hjälp av Apache Spark och senare hantera data till slutanvändare via Power BI och Server lös SQL.

## <a name="before-you-begin"></a>Innan du börjar
- [Azure Synapse Analytics-arbetsyta](../quickstart-create-workspace.md) med ett ADLS Gen2 lagrings konto konfigurerat som standard lagring. 
- Power BI arbets ytan och Power BI Desktop för att visualisera data. Mer information finns i [skapa en Power BI arbets yta](/power-bi/service-create-the-new-workspaces) och [Installera Power BI Desktop](https://powerbi.microsoft.com/downloads/)
- Länkad tjänst för att ansluta dina Azure Synapse-analyser och Power BI-arbetsytor. Mer information finns i [Länka till en Power BI arbets yta](../quickstart-power-bi.md)
- Server lös Apache Spark pool i din Synapse Analytics-arbetsyta. Mer information finns i [skapa en server lös Apache Spark pool](../quickstart-create-apache-spark-pool-studio.md)
  
## <a name="download-and-prepare-the-data"></a>Hämta och förbereda data
I det här exemplet ska du använda Apache Spark för att utföra en del analys av information om taxi rese tips från New York. Data är tillgängliga via [Azure Open data uppsättningar](https://azure.microsoft.com/services/open-datasets/catalog/nyc-taxi-limousine-commission-yellow-taxi-trip-records/). Denna del av data uppsättningen innehåller information om gula taxi resor, inklusive information om varje resa, start-och slut tid samt platser, kostnad och andra intressanta attribut.

1. Kör följande rader för att skapa en spark-dataframe genom att klistra in koden i en ny cell. Detta hämtar data via det öppna data uppsättnings-API: et. Att hämta alla dessa data genererar cirka 1 500 000 000 rader. I följande kod exempel används start_date och end_date för att tillämpa ett filter som returnerar en enda månad med data.
   
   ```python
    from azureml.opendatasets import NycTlcYellow
    from dateutil import parser
    from datetime import datetime

    end_date = parser.parse('2018-06-06')
    start_date = parser.parse('2018-05-01')
    nyc_tlc = NycTlcYellow(start_date=start_date, end_date=end_date)
    filtered_df = nyc_tlc.to_spark_dataframe()
   ```
2. Med Apache Spark SQL skapas en databas med namnet NycTlcTutorial. Vi kommer att använda den här databasen för att lagra resultatet av vår data bearbetning.
   ```python
   %%pyspark
    spark.sql("CREATE DATABASE IF NOT EXISTS NycTlcTutorial")
   ```
3. Härnäst ska vi använda Spark dataframe-åtgärder för att bearbeta data. I följande kod utför vi följande omvandlingar:
   1. Borttagning av kolumner som inte behövs.
   2. Borttagning av avvikande eller felaktiga värden genom filtrering.
   3. Skapa nya funktioner som ```tripTimeSecs``` och ```tipped``` för ytterligare analys.
    ```python
    from pyspark.sql.functions import unix_timestamp, date_format, col, when

    taxi_df = filtered_df.select('totalAmount', 'fareAmount', 'tipAmount', 'paymentType', 'rateCodeId', 'passengerCount'\
                                    , 'tripDistance', 'tpepPickupDateTime', 'tpepDropoffDateTime'\
                                    , date_format('tpepPickupDateTime', 'hh').alias('pickupHour')\
                                    , date_format('tpepPickupDateTime', 'EEEE').alias('weekdayString')\
                                    , (unix_timestamp(col('tpepDropoffDateTime')) - unix_timestamp(col('tpepPickupDateTime'))).alias('tripTimeSecs')\
                                    , (when(col('tipAmount') > 0, 1).otherwise(0)).alias('tipped')
                                    )\
                            .filter((filtered_df.passengerCount > 0) & (filtered_df.passengerCount < 8)\
                                    & (filtered_df.tipAmount >= 0) & (filtered_df.tipAmount <= 25)\
                                    & (filtered_df.fareAmount >= 1) & (filtered_df.fareAmount <= 250)\
                                    & (filtered_df.tipAmount < filtered_df.fareAmount)\
                                    & (filtered_df.tripDistance > 0) & (filtered_df.tripDistance <= 100)\
                                    & (filtered_df.rateCodeId <= 5)
                                    & (filtered_df.paymentType.isin({"1", "2"})))
    ```
4. Slutligen kommer vi att spara vår dataframe med hjälp av ```saveAsTable``` metoden Apache Spark. Detta gör att du senare kan fråga och ansluta till samma tabell med serverbaserade SQL-pooler.
  ```python
     taxi_df.write.mode("overwrite").saveAsTable("NycTlcTutorial.nyctaxi")
  ```
   
## <a name="query-data-using-serverless-sql-pools"></a>Fråga efter data med Server lös SQL-pooler
Med Azure Synapse Analytics kan olika beräknings motorer för arbets ytan dela databaser och tabeller mellan server lös Apache Spark pooler och SQL-pool utan server. Detta drivs genom Synapse-funktionen för [delad hantering av metadata](../metadata/overview.md) . Som ett resultat blir Spark-skapade databaser och deras Parquet tabeller synliga i SQL-poolen för arbets ytans Server.

Så här frågar du Apache Spark-tabellen med hjälp av SQL-poolen utan server:
   1. När du har sparat Apache Spark tabellen växlar du till fliken **data** .
   
   2. Under **arbets ytor**, letar du upp Apache Sparks tabellen som du nyss skapade och väljer **nytt SQL-skript** och väljer sedan de **översta 100 raderna**. 
      
      :::image type="content" source="../spark/media/apache-spark-power-bi/query-spark-table-with-sql.png" alt-text="SQL-fråga." border="true":::

   3. Du kan fortsätta att förfina frågan eller till och med visualisera dina resultat med hjälp av SQL-diagramtyper.

## <a name="connect-to-power-bi"></a>Ansluta till Power BI
Därefter ansluter vi vår SQL-pool utan server till vår Power BI-arbetsyta. När du har anslutit din arbets yta kommer du att kunna skapa Power BI rapporter både direkt från Azure Synapse Analytics och från Power BI Desktop.

>[!Note]
> Innan du börjar måste du konfigurera en länkad tjänst till din [Power BI arbets yta](../quickstart-power-bi.md) och hämta [Power BI Skriv bordet](/power-bi/service-create-the-new-workspaces).  

Så här ansluter du en server lös SQL-pool till vår Power BI arbets yta:

1.  Gå till fliken **Power BI data uppsättningar** och välj alternativet för **+ ny data mängd**. Hämta. pbids-filen från SQL Analytics-databasen som du vill använda som data Källa från prompten. 
   :::image type="content" source="../spark/media/apache-spark-power-bi/power-bi-datasets.png" alt-text="Power BI data uppsättningar." border="true":::

2.  Öppna filen med Power BI Desktop för att skapa en data uppsättning. När du har öppnat filen ansluter du till SQL Server-databasen med hjälp av alternativet **Microsoft-konto** och **import** . 
   

## <a name="create-a-power-bi-report"></a>Skapa en Power BI-rapport
1. Från Power BI Desktop kan du nu lägga till ett **nyckel inflytandes** diagram i rapporten.
   
   1. Dra kolumnen genomsnittlig *tipAmount* för att **analysera** axeln.
   
   2. Dra kolumnerna **weekdayString**, Average **TripDistance** och Average **tripTimeSecs** till **förklaringen efter** axel. 
   
   :::image type="content" source="../spark/media/apache-spark-power-bi/power-bi-desktop.png" alt-text="Power BI Desktop." border="true":::

2. På fliken Power BI Skriv bords start väljer du **publicera** och **Spara** ändringar. Ange ett fil namn och spara rapporten på *arbets ytan NycTaxiTutorial*.
   
3. Dessutom kan du också skapa Power BI visualiseringar i din Azure Synapse Analytics-arbetsyta. Det gör du genom att gå till fliken **utveckla** i din Azure Synapse-arbetsyta och öppna fliken Power BI. Härifrån kan du välja rapporten och fortsätta att skapa ytterligare visualiseringar. 
   
   :::image type="content" source="../spark/media/apache-spark-power-bi/power-bi-synapse.png" alt-text="Azure Synapse Analytics-arbetsyta." border="true":::

Mer information om hur du skapar en data uppsättning via server lös SQL och ansluter till Power BI finns i den här självstudien om [att ansluta till Power BI Desktop](../../synapse-analytics/sql/tutorial-connect-power-bi-desktop.md)

## <a name="next-steps"></a>Nästa steg
Du kan fortsätta att lära dig mer om data visualiserings funktioner i Azure Synapse Analytics genom att gå till följande dokument och självstudier:
   - [Visualisera data med Server lös Apache Spark pooler](../spark/apache-spark-data-visualization-tutorial.md)
   - [Översikt över data visualisering med Apache Spark pooler](../spark/apache-spark-data-visualization.md)