---
title: Zeppelin Notebook & Apache Spark-kluster – Azure HDInsight
description: Stegvisa instruktioner om hur du använder Zeppelin-anteckningsböcker med Apache Spark-kluster på Azure HDInsight.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,seoapr2020
ms.date: 04/23/2020
ms.openlocfilehash: 969dda4b57deb35d745cf7c7d263b78e6fe577d2
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "104867550"
---
# <a name="use-apache-zeppelin-notebooks-with-apache-spark-cluster-on-azure-hdinsight"></a>Använda Apache Zeppelin-anteckningsböcker med Apache Spark-kluster i Azure HDInsight

HDInsight Spark-kluster innehåller [Apache Zeppelin](https://zeppelin.apache.org/) -anteckningsböcker. Använd antecknings böckerna för att köra Apache Spark-jobb. I den här artikeln får du lära dig hur du använder Zeppelin Notebook i ett HDInsight-kluster.

## <a name="prerequisites"></a>Förutsättningar

* Ett Apache Spark-kluster i HDInsight. Anvisningar finns i [Skapa Apache Spark-kluster i Azure HDInsight](apache-spark-jupyter-spark-sql.md).
* URI-schemat för klustrets primära lagring. Schemat är `wasb://` för Azure Blob Storage, `abfs://` för Azure Data Lake Storage Gen2 eller `adl://` för Azure Data Lake Storage gen1. Om säker överföring har Aktiver ATS för Blob Storage är URI: n `wasbs://` .  Mer information finns i [Kräv säker överföring i Azure Storage](../../storage/common/storage-require-secure-transfer.md) .

## <a name="launch-an-apache-zeppelin-notebook"></a>Starta en Apache Zeppelin-anteckningsbok

1. I **översikten** Spark-kluster väljer du **Zeppelin Notebook** från **kluster instrument paneler**. Ange administratörs behörighet för klustret.  

   > [!NOTE]  
   > Du kan också komma åt Zeppelin Notebook för klustret genom att öppna följande URL i webbläsaren. Ersätt **CLUSTERNAME** med namnet på klustret:
   >
   > `https://CLUSTERNAME.azurehdinsight.net/zeppelin`

2. Skapa en ny anteckningsbok. I fönstret sidhuvud navigerar du till **anteckningsbok**  >  **Skapa ny anteckning**.

    :::image type="content" source="./media/apache-spark-zeppelin-notebook/hdinsight-create-zeppelin-notebook.png " alt-text="Skapa en ny Zeppelin-anteckningsbok" border="true":::

    Ange ett namn för antecknings boken och välj sedan **Skapa anteckning**.

3. Se till att antecknings bokens huvud visar en ansluten status. Den betecknas av en grön prick i det övre högra hörnet.

    :::image type="content" source="./media/apache-spark-zeppelin-notebook/hdinsight-zeppelin-connected.png " alt-text="Status för Zeppelin Notebook" border="true":::

4. Läs in exempeldata i en tillfällig tabell. När du skapar ett Spark-kluster i HDInsight kopieras exempel data filen `hvac.csv` till det associerade lagrings kontot under `\HdiSamples\SensorSampleData\hvac` .

    I det tomma stycket som skapas som standard i den nya antecknings boken klistrar du in följande kodfragment.

    ```scala
    %livy2.spark
    //The above magic instructs Zeppelin to use the Livy Scala interpreter

    // Create an RDD using the default Spark context, sc
    val hvacText = sc.textFile("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")

    // Define a schema
    case class Hvac(date: String, time: String, targettemp: Integer, actualtemp: Integer, buildingID: String)

    // Map the values in the .csv file to the schema
    val hvac = hvacText.map(s => s.split(",")).filter(s => s(0) != "Date").map(
        s => Hvac(s(0),
                s(1),
                s(2).toInt,
                s(3).toInt,
                s(6)
        )
    ).toDF()

    // Register as a temporary table called "hvac"
    hvac.registerTempTable("hvac")
    ```

    Tryck på **SKIFT + RETUR** eller Välj **uppspelnings** knappen för stycket för att köra kodfragmentet. Status i det högra hörnet av stycket bör förloppet från klart, väntar, som körs till SLUTFÖRt. Utdata visas längst ned i samma stycke. Skärm bilden ser ut ungefär så här:

    :::image type="content" source="./media/apache-spark-zeppelin-notebook/hdinsight-zeppelin-load-data.png " alt-text="Skapa en tillfällig tabell från rå data" border="true":::

    Du kan också ange en rubrik för varje stycke. I det högra hörnet av stycket väljer du **inställnings** ikonen (Sprocket) och väljer sedan **Visa rubrik**.  

    > [!NOTE]  
    > % spark2-tolken stöds inte i Zeppelin Notebook-versioner i alla HDInsight-versioner och% sh-tolken stöds inte från HDInsight 4,0 och senare.

5. Du kan nu köra Spark SQL-uttryck i `hvac` tabellen. Klistra in följande fråga i ett nytt stycke. Frågan hämtar Bygg-ID: t. Även skillnaden mellan målet och de faktiska temperaturerna för varje byggnad vid ett visst datum. Tryck på **SKIFT + RETUR**.

    ```sql
    %sql
    select buildingID, (targettemp - actualtemp) as temp_diff, date from hvac where date = "6/1/13"
    ```  

    **% SQL** -instruktionen i början talar om för antecknings boken att använda livy Scala-tolken.

6. Välj **stapeldiagrammet** för att ändra visningen.  **inställningarna** visas när du har valt **stapeldiagram**, som du kan använda för att välja **nycklar** och **värden**.  På följande skärm bild visas utdata.

    :::image type="content" source="./media/apache-spark-zeppelin-notebook/hdinsight-zeppelin-spark-query-1.png " alt-text="Köra ett Spark SQL-uttryck med hjälp av notebook1" border="true":::

7. Du kan också köra Spark SQL-uttryck med variabler i frågan. Nästa fragment visar hur du definierar en variabel, `Temp` , i frågan med de möjliga värden som du vill fråga efter. Första gången du kör frågan fylls en listruta automatiskt med de värden som du har angett för variabeln.

    ```sql
    %sql  
    select buildingID, date, targettemp, (targettemp - actualtemp) as temp_diff from hvac where targettemp > "${Temp = 65,65|75|85}"
    ```

    Klistra in det här kodfragmentet i ett nytt stycke och tryck på **SKIFT + RETUR**. Välj sedan **65** i list rutan **Temp** .

8. Välj **stapeldiagrammet** för att ändra visningen.  Välj sedan **Inställningar** och gör följande ändringar:

   * **Grupper:**  Lägg till **targettemp**.  
   * **Värden:** 81.1. Ta bort **datum**.  2. Lägg till **temp_diff**.  3.  Ändra Aggregator från **Sum** till **AVG**.  

     På följande skärm bild visas utdata.

     :::image type="content" source="./media/apache-spark-zeppelin-notebook/hdinsight-zeppelin-spark-query-2.png " alt-text="Köra ett Spark SQL-uttryck med hjälp av notebook2" border="true":::

## <a name="how-do-i-use-external-packages-with-the-notebook"></a>Hur gör jag för att använda externa paket med antecknings boken?

Zeppelin Notebook i Apache Spark kluster i HDInsight kan använda externa paket som har bidragit till communityn som inte ingår i klustret. Sök i [maven-lagringsplatsen](https://search.maven.org/) efter den fullständiga listan med tillgängliga paket. Du kan också hämta en lista över tillgängliga paket från andra källor. Till exempel finns en fullständig lista över community-paket som har bidragit till i [Spark-paket](https://spark-packages.org/).

I den här artikeln får du se hur du använder [Spark-CSV-](https://search.maven.org/#artifactdetails%7Ccom.databricks%7Cspark-csv_2.10%7C1.4.0%7Cjar) paketet med Jupyter Notebook.

1. Öppna tolknings inställningar. Välj det inloggade användar namnet i det övre högra hörnet och välj sedan **tolkar**.

    :::image type="content" source="./media/apache-spark-zeppelin-notebook/zeppelin-launch-interpreter.png " alt-text="Starta tolken" border="true":::

2. Bläddra till **livy2** och välj sedan **Redigera**.

    :::image type="content" source="./media/apache-spark-zeppelin-notebook/zeppelin-use-external-package-1.png " alt-text="Ändra tolknings settings1" border="true":::

3. Navigera till nyckel `livy.spark.jars.packages` och ange dess värde i formatet `group:id:version` . Så om du vill använda [Spark-CSV-](https://search.maven.org/#artifactdetails%7Ccom.databricks%7Cspark-csv_2.10%7C1.4.0%7Cjar) paketet måste du ange värdet för nyckeln till `com.databricks:spark-csv_2.10:1.4.0` .

    :::image type="content" source="./media/apache-spark-zeppelin-notebook/zeppelin-use-external-package-2.png " alt-text="Ändra tolknings settings2" border="true":::

    Välj **Spara** och sedan **OK** för att starta om livy-tolken.

4. Gör så här om du vill veta hur du kommer till värdet för nyckeln som anges ovan.

    a. Leta upp paketet i maven-lagringsplatsen. I den här artikeln använde vi [Spark-CSV](https://search.maven.org/#artifactdetails%7Ccom.databricks%7Cspark-csv_2.10%7C1.4.0%7Cjar).

    b. Samla in **värdena för** **ArtifactId** och **version** från databasen.

    :::image type="content" source="./media/apache-spark-zeppelin-notebook/use-external-packages-with-jupyter.png " alt-text="Använda externa paket med Jupyter Notebook" border="true":::

    c. Sammanfoga de tre värdena, avgränsade med kolon (**:**).

    ```
    com.databricks:spark-csv_2.10:1.4.0
    ```

## <a name="where-are-the-zeppelin-notebooks-saved"></a>Var sparas antecknings böckerna för Zeppelin?

Antecknings böckerna för Zeppelin sparas i klustrets huvudnoderna. Så om du tar bort klustret tas antecknings böckerna också bort. Om du vill bevara dina antecknings böcker för senare användning i andra kluster måste du exportera dem när du är färdig med jobb körningen. Om du vill exportera en antecknings bok väljer du **export** ikonen som visas på bilden nedan.

:::image type="content" source="./media/apache-spark-zeppelin-notebook/zeppelin-download-notebook.png " alt-text="Hämta antecknings bok" border="true":::

Den här åtgärden sparar antecknings boken som en JSON-fil på nedladdnings platsen.

## <a name="use-shiro-to-configure-access-to-zeppelin-interpreters-in-enterprise-security-package-esp-clusters"></a>Använd `Shiro` för att konfigurera åtkomst till Zeppelin-tolkar i Enterprise Security Package-kluster (ESP)

Som anges ovan `%sh` stöds inte tolken från HDInsight 4,0 och senare. Eftersom `%sh` tolkaren ger upphov till potentiella säkerhets problem, t. ex. åtkomst till flikar med Shell-kommandon, har den tagits bort från HDInsight 3,6 ESP-kluster också. Det innebär `%sh` att tolken inte är tillgänglig när du klickar på **Skapa ny anteckning** eller i tolknings gränssnittet som standard.

Privilegierade domän användare kan använda `Shiro.ini` filen för att kontrol lera åtkomsten till tolknings gränssnittet. Endast dessa användare kan skapa nya `%sh` tolkar och ange behörigheter för varje ny `%sh` tolk. Använd följande steg för att kontrol lera åtkomsten med hjälp av `shiro.ini` filen:

1. Definiera en ny roll med hjälp av ett befintligt domän grupp namn. I följande exempel `adminGroupName` är en grupp privilegierade användare i AAD. Använd inte specialtecken eller blank steg i grupp namnet. Tecknen efter `=` ger behörighet för den här rollen. `*` innebär att gruppen har fullständig behörighet.

    ```
    [roles]
    adminGroupName = *
    ```

2. Lägg till den nya rollen för åtkomst till Zeppelin-tolkar. I följande exempel får alla användare i `adminGroupName` åtkomst till Zeppelin-tolkar och kan skapa nya tolkar. Du kan infoga flera roller mellan hakparenteserna i `roles[]` , avgränsade med kommatecken. Användare som har nödvändig behörighet kan komma åt Zeppelin-tolkar.

    ```
    [urls]
    /api/interpreter/** = authc, roles[adminGroupName]
    ```

## <a name="livy-session-management"></a>Hantering av livy-sessioner

Det första kod stycket i din Zeppelin-anteckningsbok skapar en ny livy-session i klustret. Den här sessionen delas över alla Zeppelin-anteckningsböcker som du senare skapar. Om livy-sessionen avslutas av någon anledning går det inte att köra jobb från Zeppelin Notebook.

I sådana fall måste du utföra följande steg innan du kan börja köra jobb från en Zeppelin Notebook.  

1. Starta om livy-tolken från Zeppelin Notebook. Det gör du genom att öppna tolknings inställningar genom att välja det inloggade användar namnet i det övre högra hörnet och sedan välja **tolkaren**.

    :::image type="content" source="./media/apache-spark-zeppelin-notebook/zeppelin-launch-interpreter.png " alt-text="Starta tolken" border="true":::

2. Bläddra till **livy2** och välj **starta om**.

    :::image type="content" source="./media/apache-spark-zeppelin-notebook/hdinsight-zeppelin-restart-interpreter.png " alt-text="Starta om livy-tolken" border="true":::

3. Kör en Code-cell från en befintlig Zeppelin Notebook. Den här koden skapar en ny livy-session i HDInsight-klustret.

## <a name="general-information"></a>Allmän information

### <a name="validate-service"></a>Verifiera tjänst

Om du vill validera tjänsten från Ambari navigerar du till `https://CLUSTERNAME.azurehdinsight.net/#/main/services/ZEPPELIN/summary` där kluster namn är namnet på klustret.

För att verifiera tjänsten från en kommando rad, SSH till Head-noden. Växla användare till Zeppelin med kommandot `sudo su zeppelin` . Status kommandon:

|Kommando |Beskrivning |
|---|---|
|`/usr/hdp/current/zeppelin-server/bin/zeppelin-daemon.sh status`|Tjänst status.|
|`/usr/hdp/current/zeppelin-server/bin/zeppelin-daemon.sh --version`|Tjänst version.|
|`ps -aux | grep zeppelin`|Identifiera PID.|

### <a name="log-locations"></a>Logg platser

|Tjänst |Sökväg |
|---|---|
|Zeppelin-Server|/usr/hdp/current/zeppelin-server/|
|Serverloggar|/var/log/zeppelin|
|Konfigurations tolk, `Shiro` site.xml, log4j|/usr/HDP/Current/Zeppelin-Server/conf eller/etc/Zeppelin/conf|
|PID-katalog|/var/run/zeppelin|

### <a name="enable-debug-logging"></a>Aktivera fel söknings loggning

1. Navigera till `https://CLUSTERNAME.azurehdinsight.net/#/main/services/ZEPPELIN/summary` där kluster namn är namnet på klustret.

1. Navigera till **config**  >  **Advanced Zeppelin-log4j-Properties**  >  **log4j_properties_content**.

1. Ändra `log4j.appender.dailyfile.Threshold = INFO` till `log4j.appender.dailyfile.Threshold = DEBUG` .

1. Lägg till `log4j.logger.org.apache.zeppelin.realm=DEBUG` .

1. Spara ändringar och starta om tjänsten.

## <a name="next-steps"></a>Nästa steg

* [Översikt: Apache Spark i Azure HDInsight](apache-spark-overview.md)
* [Kernels som är tillgängliga för Jupyter Notebook i Apache Spark-kluster för HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Installera Jupyter på datorn och ansluta till ett HDInsight Spark-kluster](apache-spark-jupyter-notebook-install-locally.md)
