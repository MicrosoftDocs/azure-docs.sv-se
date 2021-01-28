---
title: Självstudie – Spark & Hive-verktyg för VSCode (Spark-program)
description: Självstudie – Använd Spark & Hive-verktyg för VSCode för att utveckla Spark-program som är skrivna i python och skicka dem till en server lös Apache Spark-pool.
services: synapse-analytics
author: jejiang
ms.author: jejiang
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.topic: tutorial
ms.subservice: spark
ms.date: 09/03/2020
ms.openlocfilehash: e5d335ee14709ec330405419f5be8ac5fbd6ce75
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/28/2021
ms.locfileid: "98943766"
---
# <a name="tutorial-create-an-apache-spark-applications-with-vscode-using-a-synapse-workspace"></a>Självstudie: skapa ett Apache Spark program med VSCode med hjälp av en Synapse-arbetsyta

Lär dig hur du använder Apache Spark & Hive-verktyg för Visual Studio Code. Använd verktygen för att skapa och skicka Apache Hive batch-jobb, interaktiva Hive-frågor och PySpark-skript för Apache Spark. Först beskriver vi hur du installerar Spark & Hive-verktyg i Visual Studio Code. Sedan kommer vi att gå igenom hur du skickar jobb till Spark & Hive-verktyg.

Spark & Hive-verktyg kan installeras på plattformar som stöds av Visual Studio Code. Observera följande krav för olika plattformar.

## <a name="prerequisites"></a>Förutsättningar

Följande objekt krävs för att slutföra stegen i den här artikeln:

- En server lös Apache Spark pool. Om du vill skapa en server lös Apache Spark pool, se [skapa Apache Spark pool med Azure Portal](../../synapse-analytics/quickstart-create-apache-spark-pool-portal.md).
- [Visual Studio Code](https://code.visualstudio.com/).
- [Mono](https://www.mono-project.com/docs/getting-started/install/). Mono krävs bara för Linux och macOS.
- [En PySpark interaktiv miljö för Visual Studio Code](../../hdinsight/set-up-pyspark-interactive-environment.md).
- En lokal katalog. I den här artikeln används  **C:\HD\Synaseexample**.

## <a name="install-spark--hive-tools"></a>Installera Spark & Hive-verktyg

När du uppfyller kraven kan du installera Spark & Hive-verktyg för Visual Studio Code genom att följa dessa steg:

1. Öppna Visual Studio Code.

2. I meny raden navigerar du till **Visa**  >  **tillägg**.

3. I rutan Sök anger du **Spark & Hive**.

4. Välj **Spark & Hive-verktyg** från Sök resultaten och välj sedan **Installera**:

     ![Spark & Hive för Visual Studio Code python-installation](./media/vscode-tool-synapse/install-hdInsight-plugin.png)

5. Välj **Läs in igen** när det behövs.

## <a name="open-a-work-folder"></a>Öppna en arbetsmapp

Följ dessa steg om du vill öppna en arbetsmapp och skapa en fil i Visual Studio Code:

1. I meny raden navigerar du till **filen**  >  **Öppna mapp...**  >  **C:\HD\Synaseexample** och välj sedan knappen **Välj mapp** . Mappen visas **i trädvyn till** vänster.

2. I **Utforskarvyn** väljer du mappen **Synaseexample** och väljer sedan ikonen **ny fil** bredvid arbetsmappen:

     ![ikon för ny fil i Visual Studio-kod](./media/vscode-tool-synapse/visual-studio-code-new-file.png)

3. Ge den nya filen namnet med `.py` fil namns tillägget (Spark-skript). I det här exemplet används **HelloWorld.py**.

## <a name="connect-to-your-spark-pools"></a>Anslut till dina Spark-pooler

Logga in på Azure-prenumerationen för att ansluta till dina Spark-pooler.

### <a name="sign-in-to-your-azure-subscription"></a>Logga in på din Azure-prenumeration

Följ dessa steg för att ansluta till Azure:

1. I meny raden navigerar du till **Visa**  >  **kommando paletten...** och anger **Azure: Logga** in:

     ![Spark & Hive-verktyg för Visual Studio Code login](./media/vscode-tool-synapse/hdinsight-for-vscode-extension-login.png)

2. Följ anvisningarna i inloggningen för att logga in på Azure. När du är ansluten visas namnet på ditt Azure-konto på statusfältet längst ned i Visual Studio Code-fönstret.

## <a name="set-the-default-spark-pool"></a>Ange standard Spark-poolen

1. Öppna mappen **Synaseexample** som diskuterades [tidigare](#open-a-work-folder), om den är stängd.  

2. Välj den **HelloWorld.py** -fil som skapades [tidigare](#open-a-work-folder). Den öppnas i skript redigeraren.

3. Högerklicka på skript redigeraren och välj sedan **Synapse: Ange standard Spark-pool**.  

4. [Anslut](#connect-to-your-spark-pools) till ditt Azure-konto om du inte redan har gjort det.

5. Välj en spark-pool som standard-Spark-pool för den aktuella skript filen. Verktyget uppdaterar automatiskt **.VSCode\settings.jsi** konfigurations filen:

     ![Ange standard kluster konfiguration](./media/vscode-tool-synapse/set-default-cluster-configuration.png)

## <a name="submit-interactive-synapse-pyspark-queries-to-spark-pool"></a>Skicka interaktiva Synapse PySpark-frågor till Spark-poolen

Användare kan utföra Synapse PySpark Interactive på Spark-poolen på följande sätt:

### <a name="using-the-synapse-pyspark-interactive-command-in-py-file"></a>Använda det interaktiva kommandot Synapse PySpark i PY-filen
Använd det interaktiva kommandot PySpark för att skicka frågorna enligt följande steg:

1. Öppna mappen **Synaseexample** som diskuterades [tidigare](#open-a-work-folder), om den är stängd.  

2. Skapa en ny **HelloWorld.py** -fil enligt de [tidigare](#open-a-work-folder) stegen.

3. Kopiera och klistra in följande kod i skript filen:

```python
import sys
from operator import add
from pyspark.sql import SparkSession, Row
 
spark = SparkSession\
 .builder\
 .appName("PythonWordCount")\
 .getOrCreate()
 
data = [Row(col1='pyspark and spark', col2=1), Row(col1='pyspark', col2=2), Row(col1='spark vs hadoop', col2=2), Row(col1='spark', col2=2), Row(col1='hadoop', col2=2)]
df = spark.createDataFrame(data)
lines = df.rdd.map(lambda r: r[0])
 
counters = lines.flatMap(lambda x: x.split(' ')) \
 .map(lambda x: (x, 1)) \
 .reduceByKey(add)
 
output = counters.collect()
sortedCollection = sorted(output, key = lambda r: r[1], reverse = True)
 
for (word, count) in sortedCollection:
 print("%s: %i" % (word, count))
```

4. Frågan om att installera PySpark/Synapse Pyspark kernel visas i det nedre högra hörnet i fönstret. Du kan klicka på knappen **Installera** om du vill fortsätta med PySpark-installationer för PySpark/Synapse. eller klicka på **hoppa över** om du vill hoppa över det här steget.

     ![Installera pyspark-kernel](./media/vscode-tool-synapse/install-the-pyspark-kernel.png)

5. Om du behöver installera den senare kan du gå till **fil**  >  **inställnings**  >  **Inställningar** och sedan avmarkera **HDInsight: Aktivera hoppa över Pyspark-installation** i inställningarna. 
    
     ![Aktivera hoppa över pyspark-installation](./media/vscode-tool-synapse/enable-skip-pyspark-installation.png)

6. Om installationen lyckas i steg 4 visas meddelande rutan "PySpark/Synapse Pyspark har installerats" i det nedre högra hörnet i fönstret. Klicka på knappen **Läs in** igen för att läsa in fönstret igen.

     ![pyspark har installerats](./media/vscode-tool-synapse/pyspark-kernel-installed-successfully.png)

7. I meny raden navigerar du till **Visa**  >  **kommando paletten...** eller använder tangenterna **Shift + Ctrl + P** och anger **python: Välj tolk för att starta Jupyter-servern**.

     ![Välj tolken för att starta Jupyter-servern](./media/vscode-tool-synapse/select-interpreter-to-start-jupyter-server.png)

8. Välj alternativet python nedan.

     ![Välj alternativet nedan](./media/vscode-tool-synapse/choose-the-below-option.png)
    
9. I meny raden navigerar du till **Visa**  >  **kommando paletten...** eller använder tangenterna **Shift + Ctrl + P** och anger **utvecklare: Läs in fönstret på nytt**.

     ![Läs in fönstret igen](./media/vscode-tool-synapse/reload-window.png)

10. [Anslut](#connect-to-your-spark-pools) till ditt Azure-konto om du inte redan har gjort det.

11. Markera all kod, högerklicka på skript redigeraren och välj **Synapse: Pyspark Interactive** för att skicka frågan. 

     ![pyspark interaktiv snabb meny](./media/vscode-tool-synapse/pyspark-interactive-right-click.png)

12. Välj Spark-poolen, om du inte har angett en standard Spark-pool. Efter en liten stund visas det **interaktiva python** -resultatet på en ny flik. Klicka på PySpark för att växla kernel till **Synapse PySpark**, sedan, skicka den markerade koden igen och koden kommer att köras. Med verktygen kan du också skicka in ett kodblock i stället för hela skript filen med hjälp av snabb menyn:

     ![ej](./media/vscode-tool-synapse/pyspark-interactive-python-interactive-window.png)

### <a name="perform-interactive-query-in-py-file-using-a--comment"></a>Utför interaktiv fråga i PY-filen med en kommentar på #%%

1. Lägg till **#%%** före koden för att få en bärbar dator upplevelse.

     ![Lägg till #%%](./media/vscode-tool-synapse/run-cell.png)

2. Klicka på **Kör cell**. Efter en liten stund visas det interaktiva python-resultatet på en ny flik. Klicka på PySpark för att växla kernel till **Synapse PySpark**, klicka sedan på **Kör cell** igen och koden kommer att köras. 

     ![Kör cell resultat](./media/vscode-tool-synapse/run-cell-get-results.png)

## <a name="leverage-ipynb-support-from-python-extension"></a>Utnyttja IPYNB-stöd från python-tillägget

1. Du kan skapa en Jupyter Notebook via kommando från kommando paletten eller genom att skapa en ny. ipynb-fil på din arbets yta. Mer information finns i [arbeta med antecknings böcker i Jupyter i Visual Studio Code](https://code.visualstudio.com/docs/python/jupyter-support)

2. Klicka på knappen **Kör cell** , följ anvisningarna för att **ställa in standard Spark-poolen** (starkt uppmana att ange standard kluster/-pool varje gång innan du öppnar en antecknings bok) och Läs sedan in fönstret **igen** .

     ![Ange standard Spark-poolen och Läs in igen](./media/vscode-tool-synapse/set-the-default-spark-pool-and-reload.png)

3. Klicka på PySpark för att växla kernel till **Synapse PySpark** och klicka sedan på **Kör cell** efter en while, så visas resultatet.

     ![Kör ipynb-resultat](./media/vscode-tool-synapse/run-ipynb-file-results.png)


> [!NOTE]
>
>1. MS-python >= 2020.5.78807-versionen stöds inte för den här omfattningen är ett [känt problem](#known-issues).
>  
>2. Växla till Synapse Pyspark-kärnan och inaktivera automatiska inställningar i Azure-portalen uppmuntras. Annars kan det ta lång tid att aktivera klustret och ställa in Synapse-kärnan för första gången den används. 
>
>    ![automatiska inställningar](./media/vscode-tool-synapse/auto-settings.png)

## <a name="submit-pyspark-batch-job-to-spark-pool"></a>Skicka PySpark batch job till Spark-poolen

1. Öppna mappen **Synaseexample** som du [tidigare](#open-a-work-folder)beskrivit, om den är stängd.  

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

4. [Anslut](#connect-to-your-spark-pools) till ditt Azure-konto om du inte redan har gjort det.

5. Högerklicka på skript redigeraren och välj sedan **Synapse: PySpark batch**.

6. Välj en spark-pool att skicka ditt PySpark-jobb till:

     ![Skicka python jobb resultat utdata](./media/vscode-tool-synapse/submit-pythonjob-result.png)

När du har skickat ett batch-jobb till Spark-poolen visas sändnings loggar i fönstret **utdata** i Visual Studio Code. URL: en för **Spark-användargränssnittet** och **Spark-jobbobjektet** visas också. Du kan öppna webb adressen i en webbläsare för att spåra jobbets status.

## <a name="access-and-manage-synapse-workspace"></a>Komma åt och hantera Synapse-arbetsyta

Du kan utföra olika åtgärder i Azure Explorer i Spark & Hive-verktyg för VSCode. Från Azure Explorer.

![Azure-avbildning](./media/vscode-tool-synapse/azure.png)

### <a name="launch-workspace"></a>Starta arbets yta

1. Gå till **SYNAPSE** i Azure Explorer, expandera den och Visa SYNAPSE-prenumerations listan.

     ![Synapse Explorer](./media/vscode-tool-synapse/synapse-explorer.png)

2. Klicka på prenumerationen för Synapse-arbetsytan, expandera den och Visa listan med arbets ytor.

3. Högerklicka på en arbets yta och välj sedan **visa Apache Spark program**, sidan Apache Spark program på webbplatsen för Synapse Studio kommer att öppnas.

     ![Högerklicka på arbets yta](./media/vscode-tool-synapse/right-click-on-workspace.png)

     ![Synapse Studio-program](./media/vscode-tool-synapse/synapse-studio-application.png)

4. Expandera en arbets yta, **standard lagring** och **Spark-pooler** visas.

5. Högerklicka på **standard lagring**, så visas den **fullständiga sökvägen för kopiering** och **Öppna i Synapse Studio** . 

     ![Högerklicka på standard lagring](./media/vscode-tool-synapse/right-click-on-default-storage.png)

     - Klicka på **Kopiera fullständig sökväg**, den primära ADLS Gen2s kontots URL kommer att kopieras, du kan klistra in den där du behöver från en omfattande

     - Klicka på **Öppna i Synapse Studio**, det primära lagrings kontot kommer att öppnas i Synapse Studio.

     ![standard lagring i Synapse Studio](./media/vscode-tool-synapse/default-storage-in-synapse-studio.png)

6. Expandera **standard lagringen**, det primära lagrings kontot visas.

7. Expandera **Spark-poolerna** visas alla Spark-pooler i arbets ytan.

     ![Expandera lagringspool](./media/vscode-tool-synapse/expand-storage-pool.png)


## <a name="known-issues"></a>Kända problem

### <a name="ms-python-2020578807-version-is-not-supported-on-this-extention"></a>MS-python >= 2020.5.78807-versionen stöds inte för den här omfattningen 

"Det gick inte att ansluta till Jupyter Notebook." är ett känt problem för python-version >= 2020.5.78807. Vi rekommenderar att användarna använder **[2020.4.76186](https://github.com/microsoft/vscode-python/releases/download/2020.4.76186/ms-python-release.vsix)** -versionen av MS-python för att undvika det här problemet.

![kända problem](./media/vscode-tool-synapse/known-issue.png)


## <a name="next-steps"></a>Nästa steg

- [Azure Synapse Analytics](../overview-what-is.md)
- [Skapa en ny Apache Spark pool för en Azure Synapse Analytics-arbetsyta](../../synapse-analytics/quickstart-create-apache-spark-pool-studio.md)
