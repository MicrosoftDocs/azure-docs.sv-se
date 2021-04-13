---
title: Azure HDInsight för Visual Studio Code
description: Lär dig hur du använder Spark & Hive-verktyg (Azure HDInsight) för Visual Studio Code. Använd verktygen för att skapa och skicka frågor och skript.
ms.service: hdinsight
ms.topic: how-to
ms.date: 10/20/2020
ms.custom: devx-track-python
ms.openlocfilehash: d7e2bd2987e7eaf7798093f3918f72b7e09a3cb0
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2021
ms.locfileid: "107313482"
---
# <a name="use-spark--hive-tools-for-visual-studio-code"></a>Använda Spark & Hive-verktyg för Visual Studio Code

Lär dig hur du använder Apache Spark & Hive-verktyg för Visual Studio Code. Använd verktygen för att skapa och skicka Apache Hive batch-jobb, interaktiva Hive-frågor och PySpark-skript för Apache Spark. Först beskriver vi hur du installerar Spark & Hive-verktyg i Visual Studio Code. Sedan kommer vi att gå igenom hur du skickar jobb till Spark & Hive-verktyg.  

Spark & Hive-verktyg kan installeras på plattformar som stöds av Visual Studio Code. Observera följande krav för olika plattformar.

## <a name="prerequisites"></a>Förutsättningar

Följande objekt krävs för att slutföra stegen i den här artikeln:

- Ett Azure HDInsight-kluster. Information om hur du skapar ett kluster finns i [Kom igång med HDInsight](hadoop/apache-hadoop-linux-create-cluster-get-started-portal.md). Eller Använd ett Spark-och Hive-kluster som har stöd för en Apache livy-slutpunkt.
- [Visual Studio Code](https://code.visualstudio.com/).
- [Mono](https://www.mono-project.com/docs/getting-started/install/). Mono krävs bara för Linux och macOS.
- [En PySpark interaktiv miljö för Visual Studio Code](set-up-pyspark-interactive-environment.md).
- En lokal katalog. I den här artikeln används  **C:\HD\HDexample**.

## <a name="install-spark--hive-tools"></a>Installera Spark & Hive-verktyg

När du uppfyller kraven kan du installera Spark & Hive-verktyg för Visual Studio Code genom att följa dessa steg:

1. Öppna Visual Studio Code.

2. I meny raden navigerar du till **Visa**  >  **tillägg**.

3. I rutan Sök anger du **Spark & Hive**.

4. Välj **Spark & Hive-verktyg** från Sök resultaten och välj sedan **Installera**:

   :::image type="content" source="./media/hdinsight-for-vscode/install-hdInsight-plugin.png" alt-text="Spark & Hive för Visual Studio Code python-installation":::

5. Välj **Läs in igen** när det behövs.

## <a name="open-a-work-folder"></a>Öppna en arbetsmapp

Följ dessa steg om du vill öppna en arbetsmapp och skapa en fil i Visual Studio Code:

1. I meny raden navigerar du till **filen**  >  **Öppna mapp...**  >  **C:\HD\HDexample** och välj sedan knappen **Välj mapp** . Mappen visas **i trädvyn till** vänster.

2. I **Utforskarvyn** väljer du mappen **HDexample** och väljer sedan ikonen **ny fil** bredvid arbetsmappen:

   :::image type="content" source="./media/hdinsight-for-vscode/visual-studio-code-new-file.png" alt-text="ikon för ny fil i Visual Studio-kod":::

3. Namnge den nya filen genom att antingen använda `.hql` fil namns tillägget (Hive-frågor) eller `.py` (Spark-skript). I det här exemplet används **HelloWorld. HQL**.

## <a name="set-the-azure-environment"></a>Ange Azure-miljön

För en nationell moln användare följer du de här stegen för att konfigurera Azure-miljön först och använder sedan kommandot **Azure: Sign in** för att logga in på Azure:

1. Gå till inställningar för **fil**  >  **Inställningar**  >  .
2. Sök efter följande sträng: **Azure: Cloud**.
3. Välj det nationella molnet i listan:

   :::image type="content" source="./media/hdinsight-for-vscode/set-default-login-entry-configuration.png" alt-text="Ange standard konfiguration för inloggnings post":::

## <a name="connect-to-an-azure-account"></a>Ansluta till ett Azure-konto

Innan du kan skicka skript till dina kluster från Visual Studio Code kan användaren antingen logga in i Azure-prenumerationen eller [Länka ett HDInsight-kluster](#link-a-cluster). Använd Ambari användar namn/lösen ord eller domänanslutna autentiseringsuppgifter för ESP-kluster för att ansluta till HDInsight-klustret. Följ dessa steg för att ansluta till Azure:

1. I meny raden navigerar du till **Visa**  >  **kommando paletten...** och anger **Azure: Logga** in:

   :::image type="content" source="./media/hdinsight-for-vscode/hdinsight-for-vscode-extension-login.png" alt-text="Spark & Hive-verktyg för Visual Studio Code login":::

2. Följ anvisningarna i inloggningen för att logga in på Azure. När du är ansluten visas namnet på ditt Azure-konto på statusfältet längst ned i Visual Studio Code-fönstret.  

## <a name="link-a-cluster"></a>Länka ett kluster

### <a name="link-azure-hdinsight"></a>Länk: Azure HDInsight

Du kan länka ett vanligt kluster genom att använda ett [Apache Ambari](https://ambari.apache.org/)-hanterat användar namn, eller så kan du länka ett skyddat Hadoop-kluster för företags säkerhet genom att använda ett domän användar namn (t. ex.: `user1@contoso.com` ).

1. I meny raden navigerar du till **Visa**  >  **kommando paletten...** och anger **Spark/Hive: länka ett kluster**.

   :::image type="content" source="./media/hdinsight-for-vscode/link-cluster-command.png" alt-text="Kommando rads verktyget länk kluster kommando":::

2. Välj länkat kluster typ **Azure HDInsight**.

3. Ange HDInsight-klustrets URL.

4. Ange ditt Ambari-användarnamn; Standardvärdet är **admin**.

5. Ange ditt Ambari-lösenord.

6. Välj kluster typ.

7. Ange klusterets visnings namn (valfritt).

8. Granska vyn **utdata** för verifiering.

   > [!NOTE]  
   > Det länkade användar namnet och lösen ordet används om klustret både är inloggat i Azure-prenumerationen och länkat ett kluster.  

### <a name="link-generic-livy-endpoint"></a>Länk: allmän livy-slutpunkt

1. I meny raden navigerar du till **Visa**  >  **kommando paletten...** och anger **Spark/Hive: länka ett kluster**.

2. Välj den länkade kluster typen **allmän livy-slutpunkt**.

3. Ange den allmänna livy-slutpunkten. Till exempel: http \: //10.172.41.42:18080.

4. Välj typ av auktorisering **Basic** eller **none**.  Om du väljer **Basic**:  
   
   1. Ange ditt Ambari-användarnamn; Standardvärdet är **admin**.  

   2. Ange ditt Ambari-lösenord.

5. Granska vyn **utdata** för verifiering.

## <a name="list-clusters"></a>Lista kluster

1. I meny raden navigerar du till **Visa**  >  **kommando paletten...** och anger **Spark/Hive: list kluster**.

2. Välj den prenumeration som du vill använda.

3. Granska vyn **utdata** . I den här vyn visas de länkade klustren (eller klustren) och alla kluster i din Azure-prenumeration:

   :::image type="content" source="./media/hdinsight-for-vscode/list-cluster-result1.png" alt-text="Ange en standard kluster konfiguration":::

## <a name="set-the-default-cluster"></a>Ange standard kluster

1. Öppna mappen **HDexample** som diskuterades [tidigare](#open-a-work-folder), om den är stängd.  

2. Välj filen **HelloWorld. HQL** som skapades [tidigare](#open-a-work-folder). Den öppnas i skript redigeraren.

3. Högerklicka på skript redigeraren och välj sedan **Spark/Hive: Ange standard kluster**.  

4. [Anslut](#connect-to-an-azure-account) till ditt Azure-konto eller länka ett kluster om du inte redan har gjort det.

5. Välj ett kluster som standard kluster för den aktuella skript filen. Verktyget uppdaterar automatiskt **.VSCode\settings.jsi** konfigurations filen:

   :::image type="content" source="./media/hdinsight-for-vscode/set-default-cluster-configuration.png" alt-text="Ange standard kluster konfiguration":::

## <a name="submit-interactive-hive-queries-and-hive-batch-scripts"></a>Skicka interaktiva Hive-frågor och Hive-batch-skript

Med Spark & Hive-verktyg för Visual Studio Code kan du skicka interaktiva Hive-frågor och Hive-kommando skript till dina kluster.

1. Öppna mappen **HDexample** som diskuterades [tidigare](#open-a-work-folder), om den är stängd.  

2. Välj filen **HelloWorld. HQL** som skapades [tidigare](#open-a-work-folder). Den öppnas i skript redigeraren.

3. Kopiera och klistra in följande kod i Hive-filen och spara den:

   ```hiveql
   SELECT * FROM hivesampletable;
   ```

4. [Anslut](#connect-to-an-azure-account) till ditt Azure-konto eller länka ett kluster om du inte redan har gjort det.

5. Högerklicka på skript redigeraren och välj **Hive: interaktiv** för att skicka frågan, eller använd kortkommandot Ctrl + Alt + I kortkommandot.  Välj **Hive: batch** för att skicka skriptet eller använd kortkommandot CTRL + ALT + H.  

6. Om du inte har angett ett standard kluster väljer du ett kluster. Med verktygen kan du också skicka in ett kodblock i stället för hela skript filen med hjälp av snabb menyn. Efter en liten stund visas frågeresultaten på en ny flik:

   :::image type="content" source="./media/hdinsight-for-vscode/interactive-hive-result.png" alt-text="Frågeresultat för interaktiv Apache Hive":::

   - **Resultat** panel: du kan spara hela resultatet som en CSV-, JSON-eller Excel-fil till en lokal sökväg eller bara markera flera rader.

   - Panelen **meddelanden** : när du väljer ett **rad** nummer hoppar det till den första raden i skriptet som körs.

## <a name="submit-interactive-pyspark-queries"></a>Skicka interaktiva PySpark-frågor

Användare kan utföra PySpark interaktiva på följande sätt:

### <a name="using-the-pyspark-interactive-command-in-py-file"></a>Använda det interaktiva kommandot PySpark i PY-filen
Använd det interaktiva kommandot PySpark för att skicka frågorna enligt följande steg:

1. Öppna mappen **HDexample** som diskuterades [tidigare](#open-a-work-folder), om den är stängd.  

2. Skapa en ny **HelloWorld.py** -fil enligt de [tidigare](#open-a-work-folder) stegen.

3. Kopiera och klistra in följande kod i skript filen:

   ```python
   from operator import add
   lines = spark.read.text("/HdiSamples/HdiSamples/FoodInspectionData/README").rdd.map(lambda r: r[0])
   counters = lines.flatMap(lambda x: x.split(' ')) \
                .map(lambda x: (x, 1)) \
                .reduceByKey(add)

   coll = counters.collect()
   sortedCollection = sorted(coll, key = lambda r: r[1], reverse = True)

   for i in range(0, 5):
        print(sortedCollection[i])
   ```

4. Frågan om att installera PySpark/Synapse Pyspark kernel visas i det nedre högra hörnet i fönstret. Du kan klicka på knappen **Installera** om du vill fortsätta med PySpark-installationer för PySpark/Synapse. eller klicka på **hoppa över** om du vill hoppa över det här steget.

   :::image type="content" source="./media/hdinsight-for-vscode/install-the-pyspark-kernel.png" alt-text="Skärm bild som visar ett alternativ för att hoppa över PySpark-installationen.":::

5. Om du behöver installera den senare kan du gå till **fil**  >  **inställnings**  >  **Inställningar** och sedan avmarkera **HDInsight: Aktivera hoppa över Pyspark-installation** i inställningarna. 
    
    :::image type="content" source="./media/hdinsight-for-vscode/enable-skip-pyspark-installation.png" alt-text="Skärm bild som visar alternativet att aktivera hoppa över Pyspark-installationen.":::

6. Om installationen lyckas i steg 4 visas meddelande rutan "PySpark har installerats" i det nedre högra hörnet i fönstret. Klicka på knappen **Läs in** igen för att läsa in fönstret igen.

   :::image type="content" source="./media/hdinsight-for-vscode/pyspark-kernel-installed-successfully.png" alt-text="pyspark har installerats":::


7. I meny raden navigerar du till **Visa**  >  **kommando paletten...** eller använder tangenterna **Shift + Ctrl + P** och anger **python: Välj tolk för att starta Jupyter-servern**.

   :::image type="content" source="./media/hdinsight-for-vscode/select-interpreter-to-start-jupyter-server.png" alt-text="Välj tolken för att starta Jupyter-servern":::

8. Välj alternativet python nedan.

   :::image type="content" source="./media/hdinsight-for-vscode/choose-the-below-option.png" alt-text="Välj alternativet nedan":::
    
9. I meny raden navigerar du till **Visa**  >  **kommando paletten...** eller använder tangenterna **Shift + Ctrl + P** och anger **utvecklare: Läs in fönstret på nytt**.

    :::image type="content" source="./media/hdinsight-for-vscode/reload-window.png" alt-text="Läs in fönstret igen":::

10. [Anslut](#connect-to-an-azure-account) till ditt Azure-konto eller länka ett kluster om du inte redan har gjort det.

11. Markera all kod, högerklicka på skript redigeraren och välj **Spark: PySpark Interactive/Synapse: PySpark Interactive** för att skicka frågan. 

    :::image type="content" source="./media/hdinsight-for-vscode/pyspark-interactive-right-click.png" alt-text="pyspark interaktiv snabb meny":::

12. Välj klustret, om du inte har angett ett standard kluster. Efter en liten stund visas det **interaktiva python** -resultatet på en ny flik. Klicka på PySpark för att växla kernel till **PySpark/Synapse PySpark** och koden kommer att köras. Om du vill växla till Synapse Pyspark-kärnan, så uppmuntras det att inaktivera automatiska inställningar i Azure Portal. Annars kan det ta lång tid att aktivera klustret och ställa in Synapse-kärnan för första gången den används. Om verktygen också låter dig skicka in ett kodblock i stället för hela skript filen med hjälp av snabb menyn:

    :::image type="content" source="./media/hdinsight-for-vscode/pyspark-interactive-python-interactive-window.png" alt-text="pyspark interaktiva python-fönster":::

13. Ange **%% info** och tryck sedan på SKIFT + RETUR för att visa jobb informationen (valfritt):

    :::image type="content" source="./media/hdinsight-for-vscode/pyspark-interactive-view-job-information.png" alt-text="pyspark interaktiva Visa jobb information":::

Verktyget stöder även **Spark SQL** -fr åga:

  :::image type="content" source="./media/hdinsight-for-vscode/pyspark-ineteractive-select-result.png" alt-text="pyspark interaktiv vy resultat":::


### <a name="perform-interactive-query-in-py-file-using-a--comment"></a>Utför interaktiv fråga i PY-filen med en kommentar på #%%

1. Lägg till **#%%** före py-koden för att få en bärbar dator upplevelse.

   :::image type="content" source="./media/hdinsight-for-vscode/run-cell.png" alt-text="Lägg till #%%":::

2. Klicka på **Kör cell**. Efter en liten stund visas det interaktiva python-resultatet på en ny flik. Klicka på PySpark för att växla kernel till PySpark/Synapse PySpark, klicka sedan på **Kör cell** igen och koden kommer att köras.

   :::image type="content" source="./media/hdinsight-for-vscode/run-cell-get-results.png" alt-text="Kör cell resultat":::

## <a name="leverage-ipynb-support-from-python-extension"></a>Utnyttja IPYNB-stöd från python-tillägget

1. Du kan skapa en Jupyter Notebook via kommando från kommando paletten eller genom att skapa en ny. ipynb-fil på din arbets yta. Mer information finns i [arbeta med antecknings böcker i Jupyter i Visual Studio Code](https://code.visualstudio.com/docs/python/jupyter-support)

2. Klicka på knappen **Kör cell** , följ anvisningarna för att **ställa in standard Spark-poolen** (starkt uppmana att ange standard kluster/-pool varje gång innan du öppnar en antecknings bok) och Läs sedan in fönstret **igen** .

   :::image type="content" source="./media/hdinsight-for-vscode/set-the-default-spark-pool-and-reload.png" alt-text="Ange standard Spark-poolen och Läs in igen":::

3. Klicka på PySpark för att växla kernel till **PySpark/Synapse PySpark** och klicka sedan på **Kör cell** efter ett tag visas resultatet.

   :::image type="content" source="./media/hdinsight-for-vscode/run-ipynb-file-results.png" alt-text="Kör ipynb-resultat":::


> [!NOTE]
>
> [MS-toolsai. jupyter >2021.3.684299474-versionen stöds inte i det här tillägget](#known-issues) är ett känt problem. Använd Synapse-kärnan genom att gå till Microsoft Jupyter-2021.3.684299474.

## <a name="submit-pyspark-batch-job"></a>Skicka PySpark batch job

1. Öppna mappen **HDexample** som du [tidigare](#open-a-work-folder)beskrivit, om den är stängd.  

2. Skapa en ny **BatchFile.py** -fil genom att följa de [tidigare](#open-a-work-folder) stegen.

3. Kopiera och klistra in följande kod i skript filen:

   ```python
   from __future__ import print_function
   import sys
   from operator import add
   from pyspark.sql import SparkSession
   if __name__ == "__main__":
       spark = SparkSession\
           .builder\
           .appName("PythonWordCount")\
           .getOrCreate()
   
       lines = spark.read.text('/HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv').rdd.map(lambda r: r[0])
       counts = lines.flatMap(lambda x: x.split(' '))\
                  .map(lambda x: (x, 1))\
                   .reduceByKey(add)
       output = counts.collect()
       for (word, count) in output:
           print("%s: %i" % (word, count))
       spark.stop()
   ```

4. [Anslut](#connect-to-an-azure-account) till ditt Azure-konto eller länka ett kluster om du inte redan har gjort det.

5. Högerklicka på skript redigeraren och välj sedan **Spark: PySpark batch** eller * * Synapse: PySpark batch * * *.

6. Välj en kluster/Spark-pool för att skicka ditt PySpark-jobb till:

   :::image type="content" source="./media/hdinsight-for-vscode/submit-pythonjob-result.png" alt-text="Skicka python jobb resultat utdata":::

När du har skickat ett python-jobb visas sändnings loggar i fönstret **utdata** i Visual Studio Code. URL: en för Spark UI URL och garn-UI visas också. Om du skickar batch-jobbet till en Apache Spark pool visas också URL: en för Spark-historik och Spark-jobbets användar gränssnitts webb adress. Du kan öppna webb adressen i en webbläsare för att spåra jobbets status.

## <a name="integrate-with-hdinsight-identity-broker-hib"></a>Integrera med HDInsight Identity Broker (HIB)

### <a name="connect-to-your-hdinsight-esp-cluster-with-id-broker-hib"></a>Ansluta till ditt HDInsight ESP-kluster med ID-Broker (HIB)

Du kan följa de vanliga stegen för att logga in på Azure-prenumerationen för att ansluta till ditt HDInsight ESP-kluster med ID Broker (HIB). När du har loggat in visas kluster listan i Azure Explorer. Mer information finns i [ansluta till ditt HDInsight-kluster](#connect-to-an-azure-account).

### <a name="run-a-hivepyspark-job-on-an-hdinsight-esp-cluster-with-id-broker-hib"></a>Kör ett Hive/PySpark-jobb på ett HDInsight ESP-kluster med ID-Broker (HIB)

För att köra ett Hive-jobb kan du följa de normala stegen för att skicka jobb till HDInsight ESP-kluster med ID-Broker (HIB). Mer information hittar du i [Skicka interaktiva Hive-frågor och Hive-batch-skript](#submit-interactive-hive-queries-and-hive-batch-scripts) .

För att köra ett interaktivt PySpark-jobb kan du följa de normala stegen för att skicka jobb till HDInsight ESP-kluster med ID Broker (HIB). Mer information hittar du i [Skicka interaktiva PySpark-frågor](#submit-interactive-pyspark-queries) .

För att köra ett PySpark batch-jobb kan du följa de normala stegen för att skicka jobb till HDInsight ESP-kluster med ID Broker (HIB). Mer information hittar du i [batch-jobbet skicka PySpark](#submit-pyspark-batch-job) .

## <a name="apache-livy-configuration"></a>Apache livy-konfiguration

[Apache livy](https://livy.incubator.apache.org/) -konfiguration stöds. Du kan konfigurera den i **.VSCode\settings.js** filen i mappen arbets yta. För närvarande stöder livy-konfiguration endast Python-skript. Mer information finns i [LIVY viktigt](https://github.com/cloudera/livy/blob/master/README.rst ).

<a id="triggerlivyconf"></a>**Så här utlöser du livy-konfigurationen**

### <a name="method-1"></a>Metod 1  

1. I meny raden navigerar du till inställningar för **fil**  >  **Inställningar**  >  .
2. I rutan **Sök inställningar** anger du **insändningen av HDInsight-jobb: livy conf**.  
3. Välj **Redigera i settings.jspå** för det relevanta Sök resultatet.

### <a name="method-2"></a>Metod 2

Skicka in en fil och Lägg märke till att `.vscode` mappen automatiskt läggs till i arbetsmappen. Du kan se livy-konfigurationen genom att välja **.vscode\settings.jspå**.

- Projekt inställningarna:

  :::image type="content" source="./media/hdinsight-for-vscode/hdi-apache-livy-config.png" alt-text="HDInsight Apache livy-konfiguration":::

  >[!NOTE]
  >Ange värdet och enheten för inställningarna **driverMemory** och **executorMemory** . Till exempel: 1G eller 1 024.

- Livy-konfigurationer som stöds:

  **PUBLICERA/batches**
  
  **Brödtext i begäran**

  | name | beskrivning | typ |
  | --- | --- | --- |
  | file | Fil som innehåller det program som ska köras | Sökväg (obligatoriskt) |
  | proxyUser | Användare som ska personifieras när jobbet körs | Sträng |
  | className | Program java/Spark, huvud klass | Sträng |
  | args | Kommando rads argument för programmet | Lista över strängar |
  | jar v7 | Jar v7 som ska användas i den här sessionen | Lista över strängar | 
  | pyFiles | Python-filer som ska användas i den här sessionen | Lista över strängar |
  | filer | Filer som ska användas i den här sessionen | Lista över strängar |
  | driverMemory | Mängd minne som ska användas för driv rutins processen | Sträng |
  | driverCores | Antal kärnor som ska användas för driv rutins processen | Int |
  | executorMemory | Mängden minne som ska användas per utförar-process | Sträng |
  | executorCores | Antal kärnor som ska användas för varje utförar | Int |
  | numExecutors | Antal körningar som ska startas för den här sessionen | Int |
  | faxarkiv | Arkiv som ska användas i den här sessionen | Lista över strängar |
  | kö | Namnet på den garn kö som ska skickas till| Sträng |
  | name | Namnet på den här sessionen | Sträng |
  | medför | Konfigurations egenskaper för Spark | Karta över nyckel = val |

  **Svars text** Det skapade batch-objektet.

  | name | beskrivning | typ |
  | --- | ---| --- |
  | ID | Sessions-ID | Int |
  | appId | Programmets ID för den här sessionen | Sträng |
  | appInfo | Detaljerad programinformation | Karta över nyckel = val |
  | kvorumloggen | Logg rader | Lista över strängar |
  | state |Batch-tillstånd | Sträng |

  > [!NOTE]
  > Den tilldelade livy-konfigurationen visas i fönstret utdata när du skickar skriptet.

## <a name="integrate-with-azure-hdinsight-from-explorer"></a>Integrera med Azure HDInsight från Utforskaren

Du kan förhandsgranska Hive-tabellen i klustren direkt via **Azure HDInsight** Explorer:

1. [Anslut](#connect-to-an-azure-account) till ditt Azure-konto om du inte redan har gjort det.

2. Välj **Azure** -ikonen från kolumnen längst till vänster.

3. I den vänstra rutan expanderar du **Azure: HDInsight**. Tillgängliga prenumerationer och kluster visas.

4. Expandera klustret om du vill visa Hive metadata-databasen och tabell schemat.

5. Högerklicka på Hive-tabellen. Till exempel: **hivesampletable**. Välj för **hands version**.

   :::image type="content" source="./media/hdinsight-for-vscode/hdinsight-for-vscode-preview-hive-table.png" alt-text="Spark & Hive för Visual Studio Code för hands version av Hive-tabell":::

6. Fönstret för **förhands gransknings resultat** öppnas:

   :::image type="content" source="./media/hdinsight-for-vscode/hdinsight-for-vscode-preview-results-window.png" alt-text="Spark & Hive för resultat fönstret för Visual Studio Code Preview":::

- RESULTAT panel

   Du kan spara hela resultatet som en CSV-, JSON-eller Excel-fil till en lokal sökväg eller bara markera flera rader.

- Panelen meddelanden

  1. När antalet rader i tabellen är större än 100 visas följande meddelande: "de första 100 raderna visas för Hive-tabellen."
  2. När antalet rader i tabellen är mindre än eller lika med 100 visas följande meddelande: "60 rader visas för Hive-tabellen".
  3. När det inte finns något innehåll i tabellen visas följande meddelande: " `0 rows are displayed for Hive table.` "

     >[!NOTE]
     >
     >I Linux installerar du xclip för att aktivera kopiera tabell data.
     >
     >:::image type="content" source="./media/hdinsight-for-vscode/hdinsight-for-vscode-preview-linux-install-xclip.png" alt-text="Spark & Hive för Visual Studio Code i Linux":::

## <a name="additional-features"></a>Ytterligare funktioner

Spark & Hive för Visual Studio Code stöder också följande funktioner:

- **IntelliSense-komplettering**. Förslag på popup för nyckelord, metoder, variabler och andra programmerings element. Olika ikoner representerar olika typer av objekt:

    :::image type="content" source="./media/hdinsight-for-vscode/hdinsight-for-vscode-auto-complete-objects.png" alt-text="Spark & Hive-verktyg för Visual Studio Codes IntelliSense-objekt":::

- **IntelliSense-felmarkör**. Språk tjänsten stryker under redigering av fel i Hive-skriptet.     
- **Egenskaper för syntax**. Språk tjänsten använder olika färger för att särskilja variabler, nyckelord, datatyp, funktioner och andra programmerings element:

    :::image type="content" source="./media/hdinsight-for-vscode/hdinsight-for-vscode-syntax-highlights.png" alt-text="Spark & Hive-verktyg för Visual Studio Code syntax":::

## <a name="reader-only-role"></a>Roll som endast läsare

Användare som har tilldelats rollen endast läsare för klustret kan inte skicka jobb till HDInsight-klustret eller Visa Hive-databasen. Kontakta kluster administratören för att uppgradera rollen till [**HDInsight-klustrets operator**](./hdinsight-migrate-granular-access-cluster-configurations.md#add-the-hdinsight-cluster-operator-role-assignment-to-a-user) i [Azure Portal](https://portal.azure.com/). Om du har giltiga Ambari-autentiseringsuppgifter kan du manuellt länka klustret med hjälp av följande rikt linjer.

### <a name="browse-the-hdinsight-cluster"></a>Bläddra i HDInsight-klustret  

När du väljer Azure HDInsight Explorer för att expandera ett HDInsight-kluster uppmanas du att länka klustret om du har rollen som endast läsare för klustret. Använd följande metod för att länka till klustret med hjälp av dina Ambari-autentiseringsuppgifter.

### <a name="submit-the-job-to-the-hdinsight-cluster"></a>Skicka jobbet till HDInsight-klustret

När du skickar jobb till ett HDInsight-kluster uppmanas du att länka klustret om du är i den skrivskyddade rollen för klustret. Använd följande steg för att länka till klustret med hjälp av Ambari-autentiseringsuppgifter.

### <a name="link-to-the-cluster"></a>Länk till klustret

1. Ange ett giltigt Ambari-användarnamn.
2. Ange ett giltigt lösen ord.

   :::image type="content" source="./media/hdinsight-for-vscode/hdi-azure-hdinsight-azure-username.png" alt-text="Spark & Hive-verktyg för Visual Studio Code användar namn":::

   :::image type="content" source="./media/hdinsight-for-vscode/hdi-azure-hdinsight-azure-password.png" alt-text="Spark & Hive-verktyg för Visual Studio Code Password":::

   > [!NOTE]
   >
   >Du kan använda `Spark / Hive: List Cluster` för att kontrol lera det länkade klustret:
   >
   >:::image type="content" source="./media/hdinsight-for-vscode/list-cluster-result1.png" alt-text="Spark & Hive-verktyg för Visual Studio Code Reader":::

## <a name="azure-data-lake-storage-gen2"></a>Azure Data Lake Storage Gen2

### <a name="browse-a-data-lake-storage-gen2-account"></a>Bläddra i ett Data Lake Storage Gen2 konto

Välj Azure HDInsight Explorer för att expandera ett Data Lake Storage Gen2-konto. Du uppmanas att ange lagrings åtkomst nyckeln om ditt Azure-konto inte har åtkomst till Gen2-lagring. När åtkomst nyckeln har verifierats expanderas Data Lake Storage Gen2-kontot automatiskt.

### <a name="submit-jobs-to-an-hdinsight-cluster-with-data-lake-storage-gen2"></a>Skicka jobb till ett HDInsight-kluster med Data Lake Storage Gen2

Skicka ett jobb till ett HDInsight-kluster med hjälp av Data Lake Storage Gen2. Du uppmanas att ange lagrings åtkomst nyckeln om ditt Azure-konto saknar skriv åtkomst till Gen2-lagring. När du har verifierat åtkomst nyckeln kommer jobbet att skickas.

:::image type="content" source="./media/hdinsight-for-vscode/hdi-azure-hdinsight-azure-accesskey.png" alt-text="Spark & Hive-verktyg för Visual Studio Code AccessKey":::

> [!NOTE]
>
> Du kan hämta åtkomst nyckeln för lagrings kontot från Azure Portal. Mer information finns i [Hantera åtkomst nycklar för lagrings konton](../storage/common/storage-account-keys-manage.md).

## <a name="unlink-cluster"></a>Ta bort länk till kluster

1. I meny raden går du till **Visa**  >  **kommando-palett** och anger **Spark/Hive: ta bort länk till ett kluster**.  

2. Välj ett kluster att ta bort länken.  

3. Se vyn **utdata** för verifiering.  

## <a name="sign-out"></a>Logga ut  

I meny raden går du till **Visa**  >  **kommando-palett** och anger sedan **Azure: Logga ut**.

## <a name="known-issues"></a>Kända problem 

 MS-toolsai. Jupyter >2021.3.684299474-versionen stöds inte i det här tillägget, Använd Synapse-kernel genom att gå till Microsoft Jupyter 2021.3.684299474.

 1. Inaktivera tillägget för automatisk uppdatering.

      ![Inaktivera tillägg för automatisk uppdatering](./media/hdinsight-for-vscode/disable-auto-updating-extension.png)

2. Installera en vald version av Microsoft Jupyter.

      ![vald version av Microsoft Jupyter](./media/hdinsight-for-vscode/selected-version-of-microsoft-jupyter.png)

3. Installera Microsoft Jupyter version 2021.3.684299474


## <a name="next-steps"></a>Nästa steg

En video som visar hur man använder Spark & Hive för Visual Studio Code finns i [Spark & Hive för Visual Studio Code](https://go.microsoft.com/fwlink/?linkid=858706).