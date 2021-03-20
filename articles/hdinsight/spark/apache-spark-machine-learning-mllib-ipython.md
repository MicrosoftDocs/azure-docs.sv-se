---
title: Maskin inlärnings exempel med Spark MLlib i HDInsight – Azure
description: Lär dig hur du använder Spark-MLlib för att skapa en app Learning-app som analyserar en data uppsättning med hjälp av en klassificering via logistik regression.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,hdiseo17may2017,seoapr2020, devx-track-python
ms.date: 04/27/2020
ms.openlocfilehash: d2054058bb00b0801aa1c3694c73b6a2edb46c80
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98930045"
---
# <a name="use-apache-spark-mllib-to-build-a-machine-learning-application-and-analyze-a-dataset"></a>Använd Apache Spark MLlib för att bygga ett Machine Learning-program och analysera en data uppsättning

Lär dig hur du använder Apache Spark MLlib för att skapa ett Machine Learning-program. Programmet kommer att utföra förutsägelse analyser på en öppen data uppsättning. Från Spark: s inbyggda Machine Learning-bibliotek använder det här exemplet *klassificering* via logistik regression.

MLlib är ett Core Spark-bibliotek som ger många verktyg som är användbara för Machine Learning-uppgifter, till exempel:

* Klassificering
* Regression
* Klustring
* Modellering
* Sammanställning av sammansatta värden (SVD) och huvud komponent analys (PCA)
* Hypotes testning och beräkning av exempel statistik

## <a name="understand-classification-and-logistic-regression"></a>Förstå klassificering och logistik regression

*Klassificering*, en populär maskin inlärnings uppgift, är processen att sortera indata i kategorier. Det är ett jobb för en klassificerings algoritm som visar hur du tilldelar "etiketter" för indata som du anger. Du kan till exempel tänka på en Machine Learning-algoritm som tar emot aktie information som indata. Delar sedan upp lagret i två kategorier: aktier som du bör sälja och de bestånd som du bör behålla.

Logistisk regression är den algoritm som du använder för klassificering. Spark: s logistik Regressions-API är användbart för *binär klassificering* eller klassificerar indata till en av två grupper. Mer information om logistiska regressioner finns i [Wikipedia](https://en.wikipedia.org/wiki/Logistic_regression).

I sammanfattning skapar processen för Logistisk regression en *logistik funktion*. Använd funktionen för att förutsäga sannolikheten att en inmatad Vector tillhör en grupp eller en annan.  

## <a name="predictive-analysis-example-on-food-inspection-data"></a>Exempel på förutsägelse analys av inspektions data för livsmedel

I det här exemplet använder du Spark för att göra en förutsägelse analys av inspektions data för livsmedel (**Food_Inspections1.csv**). Data som hämtats via [data portalen City i Chicago](https://data.cityofchicago.org/). Den här data uppsättningen innehåller information om inspektioner för livsmedels etablering som genomfördes i Chicago. Inklusive information om varje anläggning, identifierade överträdelser (om sådana finns) och resultatet av kontrollen. CSV-datafilen finns redan i det lagrings konto som är associerat med klustret på **/HdiSamples/HdiSamples/FoodInspectionData/Food_Inspections1.csv**.

I stegen nedan utvecklar du en modell för att se vad som krävs för att kunna skicka eller underkänna en livsmedels inspektion.

## <a name="create-an-apache-spark-mllib-machine-learning-app"></a>Skapa en Apache Spark MLlib Machine Learning-appen

1. Skapa en Jupyter Notebook med PySpark-kärnan. Instruktioner finns i [skapa en Jupyter Notebook-fil](./apache-spark-jupyter-spark-sql.md#create-a-jupyter-notebook-file).

2. Importera de typer som krävs för det här programmet. Kopiera och klistra in följande kod i en tom cell och tryck sedan på **SKIFT + RETUR**.

    ```PySpark
    from pyspark.ml import Pipeline
    from pyspark.ml.classification import LogisticRegression
    from pyspark.ml.feature import HashingTF, Tokenizer
    from pyspark.sql import Row
    from pyspark.sql.functions import UserDefinedFunction
    from pyspark.sql.types import *
    ```

    På grund av PySpark-kärnan behöver du inte skapa några kontexter explicit. Spark-och Hive-kontexterna skapas automatiskt när du kör den första kod cellen.

## <a name="construct-the-input-dataframe"></a>Konstruera dataframe för indatamängd

Använd Spark-kontexten för att hämta rå CSV-data till minnet som ostrukturerad text. Använd sedan python: s CSV-bibliotek för att parsa varje rad med data.

1. Kör följande rader för att skapa en elastisk distribuerad data uppsättning (RDD) genom att importera och parsa indata.

    ```PySpark
    def csvParse(s):
        import csv
        from StringIO import StringIO
        sio = StringIO(s)
        value = csv.reader(sio).next()
        sio.close()
        return value

    inspections = sc.textFile('/HdiSamples/HdiSamples/FoodInspectionData/Food_Inspections1.csv')\
                    .map(csvParse)
    ```

2. Kör följande kod för att hämta en rad från RDD, så att du kan ta en titt på data schemat:

    ```PySpark
    inspections.take(1)
    ```

    Utdata ser ut så här:

    ```
    [['413707',
        'LUNA PARK INC',
        'LUNA PARK  DAY CARE',
        '2049789',
        "Children's Services Facility",
        'Risk 1 (High)',
        '3250 W FOSTER AVE ',
        'CHICAGO',
        'IL',
        '60625',
        '09/21/2010',
        'License-Task Force',
        'Fail',
        '24. DISH WASHING FACILITIES: PROPERLY DESIGNED, CONSTRUCTED, MAINTAINED, INSTALLED, LOCATED AND OPERATED - Comments: All dishwashing machines must be of a type that complies with all requirements of the plumbing section of the Municipal Code of Chicago and Rules and Regulation of the Board of Health. OBSEVERD THE 3 COMPARTMENT SINK BACKING UP INTO THE 1ST AND 2ND COMPARTMENT WITH CLEAR WATER AND SLOWLY DRAINING OUT. INST NEED HAVE IT REPAIR. CITATION ISSUED, SERIOUS VIOLATION 7-38-030 H000062369-10 COURT DATE 10-28-10 TIME 1 P.M. ROOM 107 400 W. SURPERIOR. | 36. LIGHTING: REQUIRED MINIMUM FOOT-CANDLES OF LIGHT PROVIDED, FIXTURES SHIELDED - Comments: Shielding to protect against broken glass falling into food shall be provided for all artificial lighting sources in preparation, service, and display facilities. LIGHT SHIELD ARE MISSING UNDER HOOD OF  COOKING EQUIPMENT AND NEED TO REPLACE LIGHT UNDER UNIT. 4 LIGHTS ARE OUT IN THE REAR CHILDREN AREA,IN THE KINDERGARDEN CLASS ROOM. 2 LIGHT ARE OUT EAST REAR, LIGHT FRONT WEST ROOM. NEED TO REPLACE ALL LIGHT THAT ARE NOT WORKING. | 35. WALLS, CEILINGS, ATTACHED EQUIPMENT CONSTRUCTED PER CODE: GOOD REPAIR, SURFACES CLEAN AND DUST-LESS CLEANING METHODS - Comments: The walls and ceilings shall be in good repair and easily cleaned. MISSING CEILING TILES WITH STAINS IN WEST,EAST, IN FRONT AREA WEST, AND BY THE 15MOS AREA. NEED TO BE REPLACED. | 32. FOOD AND NON-FOOD CONTACT SURFACES PROPERLY DESIGNED, CONSTRUCTED AND MAINTAINED - Comments: All food and non-food contact equipment and utensils shall be smooth, easily cleanable, and durable, and shall be in good repair. SPLASH GUARDED ARE NEEDED BY THE EXPOSED HAND SINK IN THE KITCHEN AREA | 34. FLOORS: CONSTRUCTED PER CODE, CLEANED, GOOD REPAIR, COVING INSTALLED, DUST-LESS CLEANING METHODS USED - Comments: The floors shall be constructed per code, be smooth and easily cleaned, and be kept clean and in good repair. INST NEED TO ELEVATE ALL FOOD ITEMS 6INCH OFF THE FLOOR 6 INCH AWAY FORM WALL.  ',
        '41.97583445690982',
        '-87.7107455232781',
        '(41.97583445690982, -87.7107455232781)']]
    ```

    Utdata ger dig en uppfattning om schemat för indatafilen. Den innehåller namnet på varje företag och typ av verksamhet. Dessutom är adressen, data för kontrollerna och platsen, bland annat.

3. Kör följande kod för att skapa en dataframe (*DF*) och en temporär tabell (*CountResults*) med några kolumner som är användbara för förutsägelse analys. `sqlContext` används för att utföra omvandlingar på strukturerade data.

    ```PySpark
    schema = StructType([
    StructField("id", IntegerType(), False),
    StructField("name", StringType(), False),
    StructField("results", StringType(), False),
    StructField("violations", StringType(), True)])

    df = spark.createDataFrame(inspections.map(lambda l: (int(l[0]), l[1], l[12], l[13])) , schema)
    df.registerTempTable('CountResults')
    ```

    De fyra intresse kolumnerna i dataframe är **ID**, **namn**, **resultat** och **överträdelser**.

4. Kör följande kod för att få ett litet exempel på data:

    ```PySpark
    df.show(5)
    ```

    Utdata ser ut så här:

    ```
    +------+--------------------+-------+--------------------+
    |    id|                name|results|          violations|
    +------+--------------------+-------+--------------------+
    |413707|       LUNA PARK INC|   Fail|24. DISH WASHING ...|
    |391234|       CAFE SELMARIE|   Fail|2. FACILITIES TO ...|
    |413751|          MANCHU WOK|   Pass|33. FOOD AND NON-...|
    |413708|BENCHMARK HOSPITA...|   Pass|                    |
    |413722|           JJ BURGER|   Pass|                    |
    +------+--------------------+-------+--------------------+
    ```

## <a name="understand-the-data"></a>Förstå data

Vi börjar med att få en uppfattning om vad data uppsättningen innehåller. 

1. Kör följande kod för att visa de distinkta värdena i kolumnen **Results** :

    ```PySpark
    df.select('results').distinct().show()
    ```

    Utdata ser ut så här:

    ```
    +--------------------+
    |             results|
    +--------------------+
    |                Fail|
    |Business Not Located|
    |                Pass|
    |  Pass w/ Conditions|
    |     Out of Business|
    +--------------------+
    ```

2. Kör följande kod för att visualisera distributionen av dessa resultat:

    ```PySpark
    %%sql -o countResultsdf
    SELECT COUNT(results) AS cnt, results FROM CountResults GROUP BY results
    ```

    `%%sql`Magic följt av `-o countResultsdf` ser till att utdata från frågan sparas lokalt på Jupyter-servern (vanligt vis huvudnoden i klustret). Utdata sparas som en [Pandas](https://pandas.pydata.org/) -dataframe med det angivna namnet **countResultsdf**. Mer information om `%%sql` Magic och andra MAGICS som är tillgängliga med PySpark-kärnan finns i [kernels som är tillgängliga på Jupyter-anteckningsböcker med Apache Spark HDInsight-kluster](apache-spark-jupyter-notebook-kernels.md#parameters-supported-with-the-sql-magic).

    Utdata ser ut så här:

    ![SQL-frågans utdata](./media/apache-spark-machine-learning-mllib-ipython/spark-machine-learning-query-output.png "SQL-frågans utdata")

3. Du kan också använda matplotlib, ett bibliotek som används för att konstruera visualisering av data, för att skapa en rityta. Eftersom området måste skapas från det lokalt sparade **countResultsdf** -dataframe måste kodfragmentet börja med `%%local` Magic. Den här åtgärden säkerställer att koden körs lokalt på Jupyter-servern.

    ```PySpark
    %%local
    %matplotlib inline
    import matplotlib.pyplot as plt

    labels = countResultsdf['results']
    sizes = countResultsdf['cnt']
    colors = ['turquoise', 'seagreen', 'mediumslateblue', 'palegreen', 'coral']
    plt.pie(sizes, labels=labels, autopct='%1.1f%%', colors=colors)
    plt.axis('equal')
    ```

    Om du vill förutsäga resultatet av en livsmedels inspektion måste du utveckla en modell utifrån överträdelserna. Eftersom Logistisk regression är en binär klassificerings metod är det klokt att gruppera resultat data i två kategorier: **misslyckande** och **pass**:

   - Godkänd
       - Godkänd
       - Pass w/villkor
   - Underkänn
       - Underkänn
   - Ignorera
       - Företaget finns inte
       - Utanför företaget

     Data med de andra resultaten ("verksamhet saknas" eller "utanför verksamheten") är inte användbara och de utgör en liten del av resultaten ändå.

4. Kör följande kod för att konvertera befintliga dataframe ( `df` ) till en ny dataframe där varje granskning representeras som ett par med etikett-överträdelser. I det här fallet representerar en etikett för ett `0.0` haveri, en etikett på `1.0` visar ett lyckat resultat och en etikett i `-1.0` representerar vissa resultat utöver dessa två resultat.

    ```PySpark
    def labelForResults(s):
        if s == 'Fail':
            return 0.0
        elif s == 'Pass w/ Conditions' or s == 'Pass':
            return 1.0
        else:
            return -1.0
    label = UserDefinedFunction(labelForResults, DoubleType())
    labeledData = df.select(label(df.results).alias('label'), df.violations).where('label >= 0')
    ```

5. Kör följande kod för att visa en rad med märkta data:

    ```PySpark
    labeledData.take(1)
    ```

    Utdata ser ut så här:

    ```
    [Row(label=0.0, violations=u"41. PREMISES MAINTAINED FREE OF LITTER, UNNECESSARY ARTICLES, CLEANING  EQUIPMENT PROPERLY STORED - Comments: All parts of the food establishment and all parts of the property used in connection with the operation of the establishment shall be kept neat and clean and should not produce any offensive odors.  REMOVE MATTRESS FROM SMALL DUMPSTER. | 35. WALLS, CEILINGS, ATTACHED EQUIPMENT CONSTRUCTED PER CODE: GOOD REPAIR, SURFACES CLEAN AND DUST-LESS CLEANING METHODS - Comments: The walls and ceilings shall be in good repair and easily cleaned.  REPAIR MISALIGNED DOORS AND DOOR NEAR ELEVATOR.  DETAIL CLEAN BLACK MOLD LIKE SUBSTANCE FROM WALLS BY BOTH DISH MACHINES.  REPAIR OR REMOVE BASEBOARD UNDER DISH MACHINE (LEFT REAR KITCHEN). SEAL ALL GAPS.  REPLACE MILK CRATES USED IN WALK IN COOLERS AND STORAGE AREAS WITH PROPER SHELVING AT LEAST 6' OFF THE FLOOR.  | 38. VENTILATION: ROOMS AND EQUIPMENT VENTED AS REQUIRED: PLUMBING: INSTALLED AND MAINTAINED - Comments: The flow of air discharged from kitchen fans shall always be through a duct to a point above the roofline.  REPAIR BROKEN VENTILATION IN MEN'S AND WOMEN'S WASHROOMS NEXT TO DINING AREA. | 32. FOOD AND NON-FOOD CONTACT SURFACES PROPERLY DESIGNED, CONSTRUCTED AND MAINTAINED - Comments: All food and non-food contact equipment and utensils shall be smooth, easily cleanable, and durable, and shall be in good repair.  REPAIR DAMAGED PLUG ON LEFT SIDE OF 2 COMPARTMENT SINK.  REPAIR SELF CLOSER ON BOTTOM LEFT DOOR OF 4 DOOR PREP UNIT NEXT TO OFFICE.")]
    ```

## <a name="create-a-logistic-regression-model-from-the-input-dataframe"></a>Skapa en logistik Regressions modell från indataframe

Den sista uppgiften är att konvertera etiketterade data. Konvertera data till ett format som kan analyseras av Logistisk regression. Indatamängden till en logistik Regressions algoritm behöver en uppsättning *etikett funktions vektor par*. Där "funktions vektor" är en Vector med tal som representerar ingångs punkten. Så du måste konvertera kolumnen "överträdelser", som är delvis strukturerad och innehåller många kommentarer i fritext. Konvertera kolumnen till en matris med reella tal som en dator lätt kan förstå.

En standard metod för Machine Learning för bearbetning av naturligt språk är att tilldela varje distinkt ord till "index". Skicka sedan en Vector till Machine Learning-algoritmen. Så att varje indexs värde innehåller den relativa frekvensen av ordet i text strängen.

MLlib är ett enkelt sätt att utföra den här åtgärden. Först, "Tokenize" varje överträdelse sträng för att hämta de enskilda orden i varje sträng. Använd sedan en `HashingTF` för att konvertera varje uppsättning token till en funktions vektor som sedan kan skickas till logistik Regressions algoritmen för att skapa en modell. Du utför alla stegen i följd med en "Pipeline".

```PySpark
tokenizer = Tokenizer(inputCol="violations", outputCol="words")
hashingTF = HashingTF(inputCol=tokenizer.getOutputCol(), outputCol="features")
lr = LogisticRegression(maxIter=10, regParam=0.01)
pipeline = Pipeline(stages=[tokenizer, hashingTF, lr])

model = pipeline.fit(labeledData)
```

## <a name="evaluate-the-model-using-another-dataset"></a>Utvärdera modellen med en annan data uppsättning

Du kan använda modellen som du skapade tidigare för att *förutsäga* vad resultaten av nya kontroller kommer att vara. Förutsägelserna baseras på de överträdelser som har observerats. Du har tränat den här modellen på data uppsättningen **Food_Inspections1.csv**. Du kan använda en andra data uppsättning, **Food_Inspections2.csv**, för att *utvärdera* den här modellens styrka på nya data. Den här andra data uppsättningen (**Food_Inspections2.csv**) finns i standard lagrings behållaren som är kopplad till klustret.

1. Kör följande kod för att skapa en ny dataframe, **predictionsDf** som innehåller den förutsägelse som genereras av modellen. Kodfragmentet skapar också en temporär tabell med namnet **förutsägelser** baserat på dataframe.

    ```PySpark
    testData = sc.textFile('wasbs:///HdiSamples/HdiSamples/FoodInspectionData/Food_Inspections2.csv')\
                .map(csvParse) \
                .map(lambda l: (int(l[0]), l[1], l[12], l[13]))
    testDf = spark.createDataFrame(testData, schema).where("results = 'Fail' OR results = 'Pass' OR results = 'Pass w/ Conditions'")
    predictionsDf = model.transform(testDf)
    predictionsDf.registerTempTable('Predictions')
    predictionsDf.columns
    ```

    Du bör se utdata som följande text:

    ```
    ['id',
        'name',
        'results',
        'violations',
        'words',
        'features',
        'rawPrediction',
        'probability',
        'prediction']
    ```

1. Titta på en av förutsägelserna. Kör det här kodfragmentet:

    ```PySpark
    predictionsDf.take(1)
    ```

   Det finns en förutsägelse för den första posten i test data uppsättningen.

1. `model.transform()`Metoden använder samma omvandling till alla nya data med samma schema och kommer till en förutsägelse av hur data klassificeras. Du kan använda viss statistik för att få en uppfattning om hur förutsägelserna var:

    ```PySpark
    numSuccesses = predictionsDf.where("""(prediction = 0 AND results = 'Fail') OR
                                            (prediction = 1 AND (results = 'Pass' OR
                                                                results = 'Pass w/ Conditions'))""").count()
    numInspections = predictionsDf.count()

    print "There were", numInspections, "inspections and there were", numSuccesses, "successful predictions"
    print "This is a", str((float(numSuccesses) / float(numInspections)) * 100) + "%", "success rate"
    ```

    Utdata ser ut som följande text:

    ```
    There were 9315 inspections and there were 8087 successful predictions
    This is a 86.8169618894% success rate
    ```

    Med hjälp av Logistisk regression med Spark får du en modell av förhållandet mellan överträdelser på engelska. Och om en specifik verksamhet skulle leda eller underkänna en livsmedels inspektion.

## <a name="create-a-visual-representation-of-the-prediction"></a>Skapa en visuell representation av förutsägelsen

Nu kan du skapa en slutgiltig visualisering som hjälper dig att få en anledning till resultatet av testet.

1. Du börjar genom att extrahera olika förutsägelser och resultat från den temporära **förutsägelse** tabellen som skapades tidigare. I följande frågor separeras utdata som *true_positive*, *false_positive*, *true_negative* och *false_negative*. I frågorna nedan stänger du av visualiseringen genom att använda `-q` och sparar även utdata (med hjälp av `-o` ) som dataframes som kan användas med `%%local` Magic.

    ```PySpark
    %%sql -q -o true_positive
    SELECT count(*) AS cnt FROM Predictions WHERE prediction = 0 AND results = 'Fail'
    ```

    ```PySpark
    %%sql -q -o false_positive
    SELECT count(*) AS cnt FROM Predictions WHERE prediction = 0 AND (results = 'Pass' OR results = 'Pass w/ Conditions')
    ```

    ```PySpark
    %%sql -q -o true_negative
    SELECT count(*) AS cnt FROM Predictions WHERE prediction = 1 AND results = 'Fail'
    ```

    ```PySpark
    %%sql -q -o false_negative
    SELECT count(*) AS cnt FROM Predictions WHERE prediction = 1 AND (results = 'Pass' OR results = 'Pass w/ Conditions')
    ```

1. Använd slutligen följande kodfragment för att generera ritningen med **matplotlib**.

    ```PySpark
    %%local
    %matplotlib inline
    import matplotlib.pyplot as plt

    labels = ['True positive', 'False positive', 'True negative', 'False negative']
    sizes = [true_positive['cnt'], false_positive['cnt'], false_negative['cnt'], true_negative['cnt']]
    colors = ['turquoise', 'seagreen', 'mediumslateblue', 'palegreen', 'coral']
    plt.pie(sizes, labels=labels, autopct='%1.1f%%', colors=colors)
    plt.axis('equal')
    ```

    Du bör se följande utdata:

    ![Spark Machine Learning Application utdata-cirkel diagram procent av misslyckade kost-kontroller.](./media/apache-spark-machine-learning-mllib-ipython/spark-machine-learning-result-output-2.png "Spark Machine Learning resultat utdata")

    I det här diagrammet refererar ett "positivt" resultat till undersökningen om misslyckad livsmedel, medan ett negativt resultat refererar till en klar inspektion.

## <a name="shut-down-the-notebook"></a>Stäng av antecknings boken

När du har kört programmet stänger du antecknings boken för att frigöra resurserna. Du gör det genom att välja **Stäng och stoppa** i anteckningsbokens **Fil**-meny. Åtgärden stänger anteckningsboken.

## <a name="next-steps"></a>Nästa steg

* [Översikt: Apache Spark i Azure HDInsight](apache-spark-overview.md)
* [Webbplats logg analys med Apache Spark i HDInsight](apache-spark-custom-library-website-log-analysis.md)
* [Microsoft Cognitive Toolkit djup inlärnings modell med Azure HDInsight](apache-spark-microsoft-cognitive-toolkit.md)
