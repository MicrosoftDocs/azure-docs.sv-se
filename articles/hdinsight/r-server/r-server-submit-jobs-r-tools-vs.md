---
title: Skicka jobb från R-verktyg för Visual Studio – Azure HDInsight
description: Skicka R-jobb från den lokala Visual Studio-datorn till ett HDInsight-kluster.
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 06/19/2019
ms.openlocfilehash: c5a0b2d21f7d42b8ce96f72d58e5d0a8ab0c572c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98943935"
---
# <a name="submit-jobs-from-r-tools-for-visual-studio"></a>Skicka jobb från R Tools för Visual Studio

[R Tools för Visual Studio](https://marketplace.visualstudio.com/items?itemName=MikhailArkhipov007.RTVS2019) (RTVS) är ett kostnads fritt tillägg med öppen källkod för community (kostnads fri), Professional-och Enterprise-versionerna av både [Visual Studio 2017](https://www.visualstudio.com/downloads/)och [Visual Studio 2015 Update 3](https://go.microsoft.com/fwlink/?LinkId=691129) eller senare. RTVS är inte tillgängligt för [Visual Studio 2019](/visualstudio/porting/port-migrate-and-upgrade-visual-studio-projects?preserve-view=true&view=vs-2019).

RTVS förbättrar ditt R-arbetsflöde genom att erbjuda verktyg som t. ex. [R Interactive Window](/visualstudio/rtvs/interactive-repl) (repl), IntelliSense (kod komplettering), [Rita visualisering](/visualstudio/rtvs/visualizing-data) via R-bibliotek som Ggplot2 och ggviz, [R kod fel sökning](/visualstudio/rtvs/debugging)med mera.

## <a name="set-up-your-environment"></a>Konfigurera din miljö

1. Installera [R Tools för Visual Studio](/visualstudio/rtvs/installing-r-tools-for-visual-studio).

    ![Installera RTVS i Visual Studio 2017](./media/r-server-submit-jobs-r-tools-vs/install-r-tools-for-vs.png)

2. Välj arbets belastningen *data vetenskap och analys program* och välj sedan **stöd för r-språk**, stöd **för körning av r-utveckling** och alternativ för **Microsoft r-klienten** .

3. Du måste ha offentliga och privata nycklar för SSH-autentisering.
   <!-- {TODO tbd, no such file yet}[use SSH with HDInsight](hdinsight-hadoop-linux-use-ssh-windows.md) -->

4. Installera [ml Server](/previous-versions/machine-learning-server/install/r-server-install-windows) på din dator. ML Server innehåller- [`RevoScaleR`](/machine-learning-server/r-reference/revoscaler/revoscaler) och- `RxSpark` funktionerna.

5. Installera [SparaTillFil](https://www.putty.org/) för att tillhandahålla en beräknings kontext för att köra `RevoScaleR` funktioner från din lokala klient till ditt HDInsight-kluster.

6. Du kan välja att tillämpa inställningarna för data vetenskap i Visual Studio-miljön, vilket ger en ny layout för din arbets yta för R-verktygen.
   1. Om du vill spara dina aktuella Visual Studio-inställningar använder du kommandot **verktyg > import-och export inställningar** och väljer sedan **Exportera valda miljö inställningar** och anger ett fil namn. Om du vill återställa de inställningarna använder du samma kommando och väljer **Importera valda miljö inställningar**.

   2. Gå till meny alternativet **R tools** och välj **data science-inställningar...**.

       ![Inställningar för Visual Studio data science](./media/r-server-submit-jobs-r-tools-vs/data-science-settings.png)

      > [!NOTE]  
      > Med hjälp av metoden i steg 1 kan du också spara och återställa din anpassade data expert layout i stället för att upprepa kommandot **data vetenskaps inställningar** .

## <a name="execute-local-r-methods"></a>Köra lokala R-metoder

1. Skapa ditt HDInsight ML-tjänst kluster.
2. Installera [RTVS-tillägget](/visualstudio/rtvs/installation).
3. Ladda ned [zip-filen samples](https://github.com/Microsoft/RTVS-docs/archive/master.zip).
4. Öppna `examples/Examples.sln` för att starta lösningen i Visual Studio.
5. Öppna `1-Getting Started with R.R` filen i Solution- `A first look at R` mappen.
6. Börja överst i filen och tryck på CTRL + RETUR för att skicka varje rad, en i taget, till det interaktiva R-fönstret. Det kan ta en stund att installera några rader medan paketen installeras.
    * Alternativt kan du markera alla rader i R-filen (Ctrl + A), sedan antingen köra alla (CTRL + RETUR) eller välja ikonen kör interaktiv i verktygsfältet.

        ![Visual Studio köra interaktiv](./media/r-server-submit-jobs-r-tools-vs/execute-interactive1.png)

7. När du har kört alla rader i skriptet bör du se utdata som liknar detta:

    ![Visual Studio Workspace R-verktyg](./media/r-server-submit-jobs-r-tools-vs/visual-studio-workspace.png)

## <a name="submit-jobs-to-an-hdinsight-ml-services-cluster"></a>Skicka jobb till ett HDInsight ML-tjänst kluster

Med hjälp av en Microsoft ML Server/Microsoft R-klient från en Windows-dator som är utrustad med SparaTillFil kan du skapa en beräknings kontext som kör distribuerade `RevoScaleR` funktioner från din lokala klient till ditt HDInsight-kluster. Använd `RxSpark` för att skapa beräknings kontexten, ange ditt användar namn, Apache Hadoop klustrets Edge-nod, SSH-växlar och så vidare.

1. De ML-tjänsternas Edge Node-adress i HDInsight är `CLUSTERNAME-ed-ssh.azurehdinsight.net` där `CLUSTERNAME` är namnet på ditt ml-tjänst kluster.

1. Klistra in följande kod i det interaktiva fönstret R i Visual Studio och ändra värdena för de konfigurera variablerna så att de överensstämmer med din miljö.

    ```R
    # Setup variables that connect the compute context to your HDInsight cluster
    mySshHostname <- 'r-cluster-ed-ssh.azurehdinsight.net ' # HDI secure shell hostname
    mySshUsername <- 'sshuser' # HDI SSH username
    mySshClientDir <- "C:\\Program Files (x86)\\PuTTY"
    mySshSwitches <- '-i C:\\Users\\azureuser\\r.ppk' # Path to your private ssh key
    myHdfsShareDir <- paste("/user/RevoShare", mySshUsername, sep = "/")
    myShareDir <- paste("/var/RevoShare", mySshUsername, sep = "/")
    mySshProfileScript <- "/usr/lib64/microsoft-r/3.3/hadoop/RevoHadoopEnvVars.site"

    # Create the Spark Cluster compute context
    mySparkCluster <- RxSpark(
          sshUsername = mySshUsername,
          sshHostname = mySshHostname,
          sshSwitches = mySshSwitches,
          sshProfileScript = mySshProfileScript,
          consoleOutput = TRUE,
          hdfsShareDir = myHdfsShareDir,
          shareDir = myShareDir,
          sshClientDir = mySshClientDir
    )

    # Set the current compute context as the Spark compute context defined above
    rxSetComputeContext(mySparkCluster)
    ```

   ![Apache Spark-inställning för kontexten](./media/r-server-submit-jobs-r-tools-vs/apache-spark-context.png)

1. Kör följande kommandon i det interaktiva R-fönstret:

    ```R
    rxHadoopCommand("version") # should return version information
    rxHadoopMakeDir("/user/RevoShare/newUser") # creates a new folder in your storage account
    rxHadoopCopy("/example/data/people.json", "/user/RevoShare/newUser") # copies file to new folder
    ```

    Du bör se utdata som liknar följande:

    ![Lyckade mottagnings kommando körningar ](./media/r-server-submit-jobs-r-tools-vs/successful-rx-commands.png) a
1. Kontrol lera att `rxHadoopCopy` filen har kopierats `people.json` från mappen exempel data till den nya `/user/RevoShare/newUser` mappen:

    1. Från ditt kluster i HDInsight ML-tjänster i Azure väljer du **lagrings konton** i den vänstra menyn.

        ![Azure HDInsight Storage-konton](./media/r-server-submit-jobs-r-tools-vs/hdinsight-storage-accounts.png)

    2. Välj standard lagrings konto för klustret och Anteckna namnet på behållaren/katalogen.

    3. Välj **behållare** på menyn till vänster i fönstret lagrings konto.

        ![Azure HDInsight Storage-behållare](./media/r-server-submit-jobs-r-tools-vs/hdi-storage-containers.png)

    4. Välj klustrets behållar namn, bläddra till mappen **användare** (du kanske måste klicka på *Läs in mer* längst ned i listan) och välj sedan *RevoShare*, sedan **newUser**. `people.json`Filen ska visas i `newUser` mappen.

        ![HDInsight kopierad mappsökväg](./media/r-server-submit-jobs-r-tools-vs/hdinsight-copied-file.png)

1. När du är färdig med den aktuella Apache Sparks kontexten måste du stoppa den. Det går inte att köra flera kontexter samtidigt.

    ```R
    rxStopEngine(mySparkCluster)
    ```

## <a name="next-steps"></a>Nästa steg

* [Alternativ för beräknings kontext för ML-tjänster i HDInsight](r-server-compute-contexts.md)
* Genom att [kombinera scaler och sparker](../hdinsight-hadoop-r-scaler-sparkr.md) finns ett exempel på flyg Plans fördröjnings förutsägelser.