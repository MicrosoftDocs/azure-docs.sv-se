---
title: Data vetenskap med Spark på Azure HDInsight – team data science process
description: Spark MLlib Toolkit ger avsevärda funktioner för maskin inlärnings modellering i den distribuerade HDInsight-miljön.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 1dd82fb00c55e3676929999f204eae8755671038
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "93314742"
---
# <a name="overview-of-data-science-using-spark-on-azure-hdinsight"></a>Översikt över data vetenskap med Spark på Azure HDInsight

I det här avsnittet visas hur du använder HDInsight Spark för att utföra vanliga uppgifter för data vetenskap, till exempel data inmatning, funktioner för utveckling, modellering och modell utvärdering. De data som används är ett exempel på 2013 NYC taxi-resan och pris data uppsättningen. De modeller som är inbyggda är logistik och linjär regression, slumpmässiga skogar och tonings utökat träd. Avsnitten beskriver också hur du lagrar dessa modeller i Azure Blob Storage (WASB) och hur du utvärderar och utvärderar deras förutsägande prestanda. Mer avancerade ämnen beskriver hur modeller kan tränas med kors validering och rensning av Hyper-parameter. Det här översikts avsnittet innehåller också information om hur du konfigurerar Spark-klustret som du behöver för att slutföra stegen i de genom gångar som tillhandahålls.

## <a name="spark-and-mllib"></a>Spark och MLlib
[Spark](https://spark.apache.org/) är ett ramverk för parallell bearbetning med öppen källkod som har stöd för minnes intern bearbetning för att öka prestandan för program med stor data analys. Motorn för Spark-bearbetning är byggd för hastighet, enkel användning och avancerad analys. Spark: s InMemory-distribuerade beräknings funktioner gör det ett bra val för de iterativa algoritmerna som används i maskin inlärnings-och diagram beräkningar. [MLlib](https://spark.apache.org/mllib/) är Spark: s skalbara Machine Learning-bibliotek som ger algoritmiska modellerings funktioner till den här distribuerade miljön.

## <a name="hdinsight-spark"></a>HDInsight Spark
[HDInsight Spark](../../hdinsight/spark/apache-spark-overview.md) är det Azure-värdbaserade Spark-erbjudandet för öppen källkod. Den innehåller också stöd för **Jupyter PySpark-anteckningsböcker** i Spark-klustret som kan köra Spark SQL-interaktiva frågor för omvandling, filtrering och visualisering av data som lagras i Azure blobs (WASB). PySpark är python-API för Spark. Kodfragmenten som innehåller lösningarna och visar de relevanta observations områdena för att visualisera data här som körs i Jupyter-anteckningsböcker som är installerade på Spark-klustren. Modellerings stegen i dessa avsnitt innehåller kod som visar hur du tränar, utvärderar, sparar och använder varje typ av modell.

## <a name="setup-spark-clusters-and-jupyter-notebooks"></a>Installation: Spark-kluster och Jupyter-anteckningsböcker
Installations steg och kod beskrivs i den här genom gången av en HDInsight Spark 1,6. Men Jupyter Notebooks finns för både HDInsight Spark 1,6-kluster och Spark 2,0-kluster. En beskrivning av antecknings böckerna och länkar till dem finns i [Readme.MD](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Readme.md) för GitHub-databasen som innehåller dem. Dessutom är koden här och i de länkade antecknings böckerna generisk och bör fungera på alla Spark-kluster. Om du inte använder HDInsight Spark kan konfigurations-och hanterings stegen för klustret skilja sig något från vad som visas här. För enkelhetens skull är här länkarna till Jupyter-anteckningsböcker för Spark 1,6 (som ska köras i pySpark-kärnan i Jupyter Notebook-servern) och Spark 2,0 (som ska köras i pySpark3-kärnan i Jupyter Notebook-servern):

### <a name="spark-16-notebooks"></a>Spark 1,6-anteckningsböcker
Dessa antecknings böcker ska köras i pySpark-kärnan i Jupyter Notebook Server.

- [pySpark-Machine-Learning-data-science-Spark-data-utforskning-modellering. ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark1.6/pySpark-machine-learning-data-science-spark-data-exploration-modeling.ipynb): ger information om hur du utför data granskning, modellering och poäng med flera olika algoritmer.
- [pySpark-Machine-Learning-data-science-Spark-Advanced-data-prospektering-Modeling. ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark1.6/pySpark-machine-learning-data-science-spark-advanced-data-exploration-modeling.ipynb): innehåller avsnitt i notebook-#1 och modell utveckling med hjälp av widgeten för att justera och kors validering.
- [pySpark-Machine-Learning-data-science-Spark-Model-förbrukning. ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark1.6/pySpark-machine-learning-data-science-spark-model-consumption.ipynb): visar hur du operationalisera en sparad modell med python i HDInsight-kluster.

### <a name="spark-20-notebooks"></a>Spark 2,0-anteckningsböcker
Dessa antecknings böcker ska köras i pySpark3-kärnan i Jupyter Notebook Server.

- [Spark 2.0-pySpark3-Machine-Learning-data-science-Spark-Advanced-data-utforskning-modellering. ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark2.0/Spark2.0-pySpark3-machine-learning-data-science-spark-advanced-data-exploration-modeling.ipynb): den här filen innehåller information om hur du utför data granskning, modellering och poängsättning i Spark 2,0-kluster med NYC taxi-resa och pris data uppsättning som beskrivs [här](#the-nyc-2013-taxi-data). Den här antecknings boken kan vara en välgrundad utgångs punkt för att snabbt utforska den kod vi har tillhandahållit för Spark 2,0. För en mer detaljerad Notebook analyseras NYC taxi-data, se nästa antecknings bok i den här listan. Se anteckningarna som följer den här listan och som jämför dessa antecknings böcker.
- [Spark 2.0-pySpark3_NYC_Taxi_Tip_Regression. ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark2.0/Spark2.0_pySpark3_NYC_Taxi_Tip_Regression.ipynb): den här filen visar hur du utför data Datatransformering (Spark SQL-och dataframe-åtgärder), utforskning, modellering och bedömning med hjälp av NYC taxi resa och pris data uppsättning som beskrivs [här](#the-nyc-2013-taxi-data).
- [Spark 2.0-pySpark3_Airline_Departure_Delay_Classification. ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark2.0/Spark2.0_pySpark3_Airline_Departure_Delay_Classification.ipynb): den här filen visar hur du utför data Datatransformering (Spark SQL-och dataframe-åtgärder), utforskning, modellering och bedömning med hjälp av den välkända flyg data uppsättningen från 2011 och 2012. Vi integrerade flyg data uppsättningen med flyg platsens väder data (till exempel windspeed, temperatur, höjd osv.) före modellering, så att dessa väder funktioner kan ingå i modellen.

<!-- -->

> [!NOTE]
> Flyg data uppsättningen lades till i Spark 2,0-anteckningsbokarna för att bättre illustrera användningen av klassificerings algoritmer. Se följande länkar om du vill ha information om flyg data uppsättning och väder data uppsättning för flyg tid:
> 
> - Flyg-och avgångs tids data: [https://www.transtats.bts.gov/ONTIME/](https://www.transtats.bts.gov/ONTIME/)
> 
> - Flyg plats väder data: [https://www.ncdc.noaa.gov/](https://www.ncdc.noaa.gov/)

<!-- -->

<!-- -->

> [!NOTE]
> Anslutningarna för Spark 2,0 på NYC taxi-och flyg-flyg fördröjnings data uppsättningar kan ta 10 minuter eller mer att köra (beroende på storleken på ditt HDI-kluster). Den första bärbara datorn i listan ovan visar många aspekter av utbildningen för data utforskning, visualisering och analys i en bärbar dator som tar mindre tid att köra med insamlade NYC data uppsättningar, där taxi-och pris uppgifter har varit i förväg anslutna: [Spark 2.0-pySpark3-Machine-Learning-data-science-Spark-Advanced-data-prospektering-Modeling. ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark2.0/Spark2.0-pySpark3-machine-learning-data-science-spark-advanced-data-exploration-modeling.ipynb). Den här antecknings boken tar mycket kortare tid att slutföra (2-3 minuter) och kan vara en bra utgångs punkt för att snabbt utforska den kod vi har angett för Spark 2,0.

<!-- -->

Vägledning för driftsättning av en spark 2,0-modell och modell förbrukning för poängsättning finns i [Spark 1,6-dokumentet om förbrukningen](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark1.6/pySpark-machine-learning-data-science-spark-model-consumption.ipynb) för ett exempel som beskriver de steg som krävs. Om du vill använda det här exemplet på Spark 2,0 ersätter du python-kodfragmentet med [den här filen](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/Python/Spark2.0_ConsumeRFCV_NYCReg.py).

### <a name="prerequisites"></a>Förutsättningar

Följande procedurer är relaterade till Spark 1,6. För Spark 2,0-versionen använder du de antecknings böcker som beskrivs och länkade till tidigare.

1. Du måste ha en Azure-prenumeration. Om du inte redan har ett kan du läsa [få en kostnads fri utvärderings version av Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

2. Du behöver ett Spark 1,6-kluster för att slutföra den här genom gången. Information om hur du skapar ett finns i anvisningarna i [komma igång: skapa Apache Spark på Azure HDInsight](../../hdinsight/spark/apache-spark-jupyter-spark-sql.md). Kluster typen och-versionen anges från menyn **Välj kluster typ** .

![Konfigurera kluster](./media/spark-overview/spark-cluster-on-portal.png)

<!-- -->

> [!NOTE]
> Ett ämne som visar hur du använder Scala i stället för python för att slutföra aktiviteter för en slut punkt till slut punkt för data vetenskaps processer finns i [data vetenskap med Scala med Spark på Azure](scala-walkthrough.md).
>
>

<!-- -->

> [!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]
>
>

## <a name="the-nyc-2013-taxi-data"></a>NYC 2013 taxi-data
NYC taxi-resan är ungefär 20 GB komprimerade kommaavgränsade värden (CSV) (~ 48 GB okomprimerade), som omfattar mer än 173 000 000 enskilda resor och de biljett priser som betalas för varje resa. Varje rese post omfattar hämtnings-och DropOff plats och tid, anonymiserats Hack (driv Rutinens) licens nummer och Medallion (Taxins unika ID). Data omfattar alla resor under året 2013 och tillhandahålls i följande två data uppsättningar för varje månad:

1. CSV-filerna trip_data innehåller information om resan, till exempel antal passagerare, hämtnings-och DropOff punkter, varaktighet för resan och rese längd. Här följer några exempel poster:

   `medallion,hack_license,vendor_id,rate_code,store_and_fwd_flag,pickup_datetime,dropoff_datetime,passenger_count,trip_time_in_secs,trip_distance,pickup_longitude,pickup_latitude,dropoff_longitude,dropoff_latitude`

   `89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,1,N,2013-01-01 15:11:48,2013-01-01 15:18:10,4,382,1.00,-73.978165,40.757977,-73.989838,40.751171`

   `0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-06 00:18:35,2013-01-06 00:22:54,1,259,1.50,-74.006683,40.731781,-73.994499,40.75066`

   `0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-05 18:49:41,2013-01-05 18:54:23,1,282,1.10,-74.004707,40.73777,-74.009834,40.726002`

   `DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:54:15,2013-01-07 23:58:20,2,244,.70,-73.974602,40.759945,-73.984734,40.759388`

   `DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:25:03,2013-01-07 23:34:24,1,560,2.10,-73.97625,40.748528,-74.002586,40.747868`

2. CSV-filerna trip_fare innehåller information om avgiften som har betalats för varje resa, till exempel betalnings typ, pris belopp, tilläggs avgift, tips och avgifter, samt totalt betalat belopp. Här följer några exempel poster:

   `medallion, hack_license, vendor_id, pickup_datetime, payment_type, fare_amount, surcharge, mta_tax, tip_amount, tolls_amount, total_amount`

   `89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,2013-01-01 15:11:48,CSH,6.5,0,0.5,0,0,7`

   `0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-06 00:18:35,CSH,6,0.5,0.5,0,0,7`

   `0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-05 18:49:41,CSH,5.5,1,0.5,0,0,7`

   `DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:54:15,CSH,5,0.5,0.5,0,0,6`

   `DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:25:03,CSH,9.5,0.5,0.5,0,0,10.5`

Vi har tagit ett exempel på 0,1% av de här filerna och anslöt rese \_ data och rese- \_ CVS-filer till en enda data uppsättning som ska användas som indata-datauppsättning för den här genom gången. Den unika nyckeln för att ansluta till rese \_ data och rese \_ pris består av fälten: Medallion, Hacke \_ License och upphämtnings \_ tid. Varje post i data uppsättningen innehåller följande attribut som representerar en NYC taxi-resa:

| Fält | Kort beskrivning |
| --- | --- |
| medallion |Anonymiserats taxi-Medallion (unikt taxi-ID) |
| hack_license |Anonymiserats Hackney-gods i licens nummer |
| vendor_id |Taxi leverantörs-ID |
| rate_code |NYC Taxi pris taxa |
| store_and_fwd_flag |Flagga för lagring och vidarebefordran |
| pickup_datetime |Hämta datum & tid |
| dropoff_datetime |Datum & tid för DropOff |
| pickup_hour |Hämta timme |
| pickup_week |Hämta vecka på året |
| veckodag |Veckodag (intervall 1-7) |
| passenger_count |Antal passagerare i en taxi resa |
| trip_time_in_secs |Rese tid i sekunder |
| trip_distance |Resans avstånd i km |
| pickup_longitude |Hämta longitud |
| pickup_latitude |Hämta Latitude |
| dropoff_longitude |DropOff longitud |
| dropoff_latitude |DropOff Latitude |
| direct_distance |Direkt avstånd mellan upphämtnings-och DropOff platser |
| payment_type |Betalnings typ (kontant, kredit kort osv.) |
| fare_amount |Avgifts belopp i |
| fördelning |Fördelning |
| mta_tax |Transport avgift för MTA-tunnel |
| tip_amount |Tips mängd |
| tolls_amount |Avgifts belopp |
| total_amount |Totalmängd |
| lutad |Lutad (0/1 för nej eller Ja) |
| tip_class |Tips klass (0: $0, 1: $0-5, 2: $6-10, 3: $11-20, 4: > $20) |

## <a name="execute-code-from-a-jupyter-notebook-on-the-spark-cluster"></a>Köra kod från en Jupyter-anteckningsbok i Spark-klustret
Du kan starta Jupyter Notebook från Azure Portal. Hitta ditt Spark-kluster på instrument panelen och klicka på det för att ange hanterings sidan för klustret. Öppna den antecknings bok som är associerad med Spark-klustret genom att klicka på **kluster instrument paneler**  ->  **Jupyter Notebook**.

![Kluster instrument paneler](./media/spark-overview/spark-jupyter-on-portal.png)

Du kan också bläddra till ***`https://CLUSTERNAME.azurehdinsight.net/jupyter`*** för att få åtkomst till Jupyter-anteckningsböcker. Ersätt kluster namns delen av den här URL: en med namnet på ditt eget kluster. Du behöver lösen ordet för ditt administratörs konto för att få åtkomst till antecknings böckerna.

![Bläddra bland Jupyter-anteckningsböcker](./media/spark-overview/spark-jupyter-notebook.png)

Välj PySpark om du vill se en katalog som innehåller några exempel på förpaketerade antecknings böcker som använder PySpark-API: et. De antecknings böcker som innehåller kod exemplen för den här serien av Spark-ämne finns på [GitHub](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/Spark/pySpark)

Du kan ladda upp antecknings böckerna direkt från [GitHub](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/Spark/pySpark) till Jupyter Notebook-servern i Spark-klustret. På Start sidan för din Jupyter klickar du på knappen **överför** på den högra delen av skärmen. Den öppnar en Utforskaren. Här kan du klistra in URL: en för GitHub (RAW content) för antecknings boken och klicka på **Öppna**.

Du ser fil namnet i Jupyter-fillistan med knappen **överför** igen. Klicka på den här knappen **överför** . Nu har du importerat antecknings boken. Upprepa de här stegen för att ladda upp de andra antecknings böckerna från den här genom gången.

> [!TIP]
> Du kan högerklicka på länkarna i webbläsaren och välja **Kopiera länk** för att hämta URL: en för GitHub RAW-innehåll. Du kan klistra in denna URL i dialog rutan Jupyter upload File Explorer.
> 
> 

Nu kan du:

* Se koden genom att klicka på antecknings boken.
* Kör varje cell genom att trycka på **SKIFT-RETUR**.
* Kör hela antecknings boken genom att klicka på **cell**  ->  **körning**.
* Använd automatisk visualisering av frågor.

> [!TIP]
> PySpark-kärnan visualiserar automatiskt utdata från SQL-frågor (HiveQL). Du får välja mellan flera olika typer av visualiseringar (tabell, cirkel diagram, linje, yta eller stapel) med hjälp av meny knapparna **typ** i antecknings boken:
>
>

![Logistisk regression ROC-kurva för allmän metod](./media/spark-overview/pyspark-jupyter-autovisualization.png)

## <a name="whats-next"></a>Nästa steg
Nu när du har skapat ett HDInsight Spark-kluster och har laddat upp Jupyter notebooks, är du redo att arbeta med de avsnitt som motsvarar de tre PySpark-Notebook-datorerna. De visar hur du utforskar dina data och hur du skapar och använder modeller. Den avancerade data utforsknings-och modellerings antecknings boken visar hur du inkluderar kors validering, rensning av Hyper-parameter och modell utvärdering.

**Data utforskning och modellering med Spark:** Utforska data uppsättningen och skapa, score och utvärdera maskin inlärnings modeller genom att gå igenom [skapa binära klassificerings-och Regressions modeller för data med Spark MLlib Toolkit](spark-data-exploration-modeling.md) -avsnittet.

**Modell förbrukning:** Information om hur du visar klassificerings-och Regressions modeller som skapats i det här avsnittet finns i [Poäng och utvärdera Spark-skapade maskin inlärnings modeller](spark-model-consumption.md).

**Rensning av kors validering och Cross-parameter**: se [Avancerad data granskning och modellering med Spark](spark-advanced-data-exploration-modeling.md) om hur modeller kan tränas med kors validering och rensning av Hyper-parameter