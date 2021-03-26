---
title: Hantera ML Services-kluster i HDInsight – Azure
description: Lär dig hur du hanterar olika uppgifter i ML Services-kluster i Azure HDInsight.
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: how-to
ms.date: 06/19/2019
ms.openlocfilehash: a5e623b0429194db6d03beb674679bd10e337844
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/23/2021
ms.locfileid: "104869486"
---
# <a name="manage-ml-services-cluster-on-azure-hdinsight"></a>Hantera ML Services-kluster i Azure HDInsight

I den här artikeln får du lära dig hur du hanterar ett befintligt ML-kluster på Azure HDInsight för att utföra åtgärder som att lägga till flera samtidiga användare, fjärrans luta till ett ML Services-kluster, ändra beräknings sammanhang osv.

## <a name="prerequisites"></a>Förutsättningar

* Ett ML Services-kluster i HDInsight. Se [skapa Apache Hadoop kluster med Azure Portal](../hdinsight-hadoop-create-linux-clusters-portal.md) och välj ml- **tjänster** för **kluster typ**.

* En SSH-klient (Secure Shell): En SSH-klient används för att fjärransluta till HDInsight-klustret och köra kommandon direkt på klustret. Mer information finns i [använda SSH med HDInsight.](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="enable-multiple-concurrent-users"></a>Aktivera flera samtidiga användare

Du kan aktivera flera samtidiga användare för ML-tjänst kluster i HDInsight genom att lägga till fler användare för Edge-noden som RStudio community-versionen körs på. När du skapar ett HDInsight-kluster måste du ange två användare, en HTTP-användare och en SSH-användare:

:::image type="content" source="./media/r-server-hdinsight-manage/hdi-concurrent-users1.png" alt-text="Parametrar för HDI Azure Portal inloggning" border="true":::

- **Användarnamn för klusterinloggning**: en HTTP-användare för autentisering via HDInsight-gatewayen som används till att skydda HDInsight-klustret du skapade. Den här HTTP-användaren används för att få åtkomst till Apache Ambari-ANVÄNDARGRÄNSSNITTET, Apache Hadoop garn användar gränssnitt, samt andra GRÄNSSNITTs komponenter.
- **Secure Shell (SSH)-användarnamn**: en SSH-användare för åtkomst till klustret via SSH. Den här användaren är en användare i Linux-systemet för alla huvudnoder, arbetsnoder och kantnoder. Du kan därmed använda SSH för åtkomst till alla noder i ett fjärrkluster.

Den R Studio Server Community-version som används i ML-tjänstekluster på HDInsight accepterar bara Linux-användarnamn och-lösen ord som en mekanism för inloggning. Du kan inte skicka token. Så när du försöker komma åt R Studio för första gången på ett ML Services-kluster måste du logga in två gånger.

- Logga först in med HTTP-användarautentiseringsuppgifterna via HDInsight-gatewayen.

- Använd sedan autentiseringsuppgifterna för SSH-användare för att logga in på RStudio.
  
För närvarande kan du bara skapa ett SSH-användarkonto när du etablerar ett HDInsight-kluster. För att flera användare ska kunna komma åt ML-kluster i HDInsight måste du skapa ytterligare användare i Linux-systemet.

Eftersom RStudio körs på klustrets Edge-nod finns det flera steg här:

1. Använd den befintliga SSH-användaren för att logga in på Edge-noden
2. Lägg till fler Linux-användare på kantnoden
3. Använd RStudio Community-versionen med användaren som skapades

### <a name="step-1-use-the-created-ssh-user-to-sign-in-to-the-edge-node"></a>Steg 1: Använd den skapade SSH-användaren för att logga in på Edge-noden

Följ anvisningarna i [ansluta till HDInsight (Apache Hadoop) med SSH](../hdinsight-hadoop-linux-use-ssh-unix.md) för att få åtkomst till Edge-noden. Edge Node-adressen för ML Services-kluster i HDInsight är `CLUSTERNAME-ed-ssh.azurehdinsight.net` .

### <a name="step-2-add-more-linux-users-in-edge-node"></a>Steg 2: Lägg till fler Linux-användare på kantnoden

Kör följande kommandon när du ska lägga till en användare vid kantnoden:

```bash
# Add a user 
sudo useradd <yournewusername> -m

# Set password for the new user
sudo passwd <yournewusername>
```

På följande skärm bild visas utdata.

:::image type="content" source="./media/r-server-hdinsight-manage/hdi-concurrent-users2.png" alt-text="bildskärms utdata för samtidiga användare" border="true":::

När du uppmanas att ange "nuvarande Kerberos-lösenord:" trycker du bara på **RETUR** för att ignorera det. Alternativet `-m` i kommandot `useradd` innebär att systemet skapar en arbetsmapp för användaren, vilket krävs för RStudio Comunity-versionen.

### <a name="step-3-use-rstudio-community-version-with-the-user-created"></a>Steg 3: Använd RStudio Community-versionen med användaren som skapades

Få åtkomst till RStudio från `https://CLUSTERNAME.azurehdinsight.net/rstudio/` . Om du loggar in för första gången efter att klustret har skapats anger du autentiseringsuppgifter för kluster administratören följt av de autentiseringsuppgifter för SSH-användare som du skapade. Om detta inte är din första inloggning anger du bara autentiseringsuppgifterna för SSH-användaren du skapade.

Du kan också logga in med de ursprungliga autentiseringsuppgifterna (som standard *sshuser*) samtidigt från ett annat webbläsarfönster.

Observera också att de nya användarna inte har rotbehörighet i Linux-systemet, men att de har samma åtkomst till alla filer i HDFS- och WASB-fjärrlagringen.

## <a name="connect-remotely-to-microsoft-ml-services"></a>Fjärrans luta till Microsoft ML-tjänster

Du kan ställa in åtkomst till HDInsight Spark Compute-kontexten från en fjärran sluten instans av ML-klienten som körs på Skriv bordet. För att göra det måste du ange alternativen (hdfsShareDir, shareDir, sshUsername, sshHostname, sshSwitches och sshProfileScript) när du definierar Rxspark beräkningskontexten Compute-kontexten på Skriv bordet: till exempel:

```r
myNameNode <- "default"
myPort <- 0

mySshHostname  <- '<clustername>-ed-ssh.azurehdinsight.net'  # HDI secure shell hostname
mySshUsername  <- '<sshuser>'# HDI SSH username
mySshSwitches  <- '-i /cygdrive/c/Data/R/davec'   # HDI SSH private key

myhdfsShareDir <- paste("/user/RevoShare", mySshUsername, sep="/")
myShareDir <- paste("/var/RevoShare" , mySshUsername, sep="/")

mySparkCluster <- RxSpark(
    hdfsShareDir = myhdfsShareDir,
    shareDir     = myShareDir,
    sshUsername  = mySshUsername,
    sshHostname  = mySshHostname,
    sshSwitches  = mySshSwitches,
    sshProfileScript = '/etc/profile',
    nameNode     = myNameNode,
    port         = myPort,
    consoleOutput= TRUE
)
```

Mer information finns i avsnittet "använda Microsoft Machine Learning Server som en Apache Hadoop-klient" i [så här använder du RevoScaleR i en Apache Spark beräknings kontext](/machine-learning-server/r/how-to-revoscaler-spark#more-spark-scenarios)

## <a name="use-a-compute-context"></a>Använda en beräkningskontext

Med en beräkningskontext kan du kontrollera om beräkningen utförs lokalt på kantnoden eller om den ska distribueras mellan noderna i HDInsight-klustret.  Ett exempel på att ställa in en beräknings kontext med RStudio-servern finns i [köra ett R-skript på ett ml Services-kluster i Azure HDInsight med RStudio Server](machine-learning-services-quickstart-job-rstudio.md).

## <a name="distribute-r-code-to-multiple-nodes"></a>Distribuera R-kod till flera noder

Med ML-tjänster i HDInsight kan du ta befintlig R-kod och köra den på flera noder i klustret med hjälp av `rxExec` . Det här är praktiskt när du gör en parameterrensning eller simuleringar. Här följer ett kodexempel på hur du kan använda `rxExec`:

```r
rxExec( function() {Sys.info()["nodename"]}, timesToRun = 4 )
```

Om du fortfarande använder Spark-kontexten returnerar det här kommandot nodnamn-värdet för de arbetsnoder som koden `(Sys.info()["nodename"])` körs på. I ett kluster med fyra noder förväntar du dig till exempel att få utdata som liknar följande kodfragment:

```r
$rxElem1
    nodename
"wn3-mymlser"

$rxElem2
    nodename
"wn0-mymlser"

$rxElem3
    nodename
"wn3-mymlser"

$rxElem4
    nodename
"wn3-mymlser"
```

## <a name="access-data-in-apache-hive-and-parquet"></a>Få åtkomst till data i Apache Hive och Parquet

HDInsight ML-tjänster ger direkt åtkomst till data i Hive och Parquet för användning av skalnings funktioner i beräknings kontexten Spark. Dessa funktioner är tillgängliga via nya ScaleR-datakällafunktioner som heter RxHiveData och RxParquetData som fungerar med Spark SQL för att läsa in data direkt till en Spark DataFrame för analys av ScaleR.

Här är exempelkod där de nya funktionerna används:

```r
#Create a Spark compute context:
myHadoopCluster <- rxSparkConnect(reset = TRUE)

#Retrieve some sample data from Hive and run a model:
hiveData <- RxHiveData("select * from hivesampletable",
                       colInfo = list(devicemake = list(type = "factor")))
rxGetInfo(hiveData, getVarInfo = TRUE)

rxLinMod(querydwelltime ~ devicemake, data=hiveData)

#Retrieve some sample data from Parquet and run a model:
rxHadoopMakeDir('/share')
rxHadoopCopyFromLocal(file.path(rxGetOption('sampleDataDir'), 'claimsParquet/'), '/share/')
pqData <- RxParquetData('/share/claimsParquet',
                        colInfo = list(
                            age    = list(type = "factor"),
                            car.age = list(type = "factor"),
                            type = list(type = "factor")
                        ) )
rxGetInfo(pqData, getVarInfo = TRUE)

rxNaiveBayes(type ~ age + cost, data = pqData)

#Check on Spark data objects, cleanup, and close the Spark session:
lsObj <- rxSparkListData() # two data objs are cached
lsObj
rxSparkRemoveData(lsObj)
rxSparkListData() # it should show empty list
rxSparkDisconnect(myHadoopCluster)
```

Mer information om hur du använder dessa nya funktioner finns i onlinehjälpen i ML-tjänster med hjälp av- `?RxHivedata` och- `?RxParquetData` kommandona.  

## <a name="install-additional-r-packages-on-the-cluster"></a>Installera ytterligare R-paket på klustret

### <a name="to-install-r-packages-on-the-edge-node"></a>Installera R-paket på Edge-noden

Om du vill installera ytterligare R-paket på Edge-noden kan du använda direkt inifrån `install.packages()` R-konsolen när du är ansluten till Edge-noden via SSH. 

### <a name="to-install-r-packages-on-the-worker-node"></a>Installera R-paket på Worker-noden

Om du vill installera R-paket på arbetsnoderna i klustret måste du använda en skript åtgärd. Skriptåtgärder är bash-skript som används till att göra konfigurationsändringar i HDInsight-klustret eller till att installera ytterligare programvara, som fler R-paket. 

> [!IMPORTANT]  
> Det går bara att använda skriptåtgärder för att installera ytterligare R-paket när klustret har skapats. Använd inte den här proceduren när du skapar klustret, eftersom skriptet förlitar sig på att ML-tjänster konfigureras fullständigt.

1. Följ stegen i [Anpassa kluster med skript åtgärd](../hdinsight-hadoop-customize-cluster-linux.md).

3. Ange följande information för att **Skicka skript åtgärder**:

   * För **skript typ** väljer du **anpassad**.

   * Som **namn** anger du ett namn för skript åtgärden.

     * För **skript-URI för bash** anger du  `https://mrsactionscripts.blob.core.windows.net/rpackages-v01/InstallRPackages.sh` . Det här är det skript som installerar ytterligare R-paket på arbetsnoden

   * Markera bara kryss rutan för **Worker**.

   * **Parametrar**: R-paketen som ska installeras. Till exempel `bitops stringr arules`

   * Markera kryss rutan för att **Spara den här skript åtgärden**.  

   > [!NOTE]
   > 1. Som standard installeras alla R-paket från en ögonblicks bild av Microsoft MRAN-lagringsplatsen enligt den version av ML Server som har installerats. Om du vill installera nyare version av paket finns en risk för kompatibilitetsproblem. Den här typen av installation är däremot möjlig om du anger `useCRAN` som det första elementet i paketlistan, till exempel `useCRAN bitops, stringr, arules`.  
   > 2. För vissa R-paket krävs ytterligare Linux-bibliotek. För enkelhetens skull är HDInsight ML-tjänsterna förinstallerade med de beroenden som krävs av de översta 100 populära R-paketen. Men om R-paket som du installerar kräver bibliotek utöver dessa måste du ladda ned basskriptet som används här och lägga till steg för att installera systembiblioteken. Sedan måste du överföra de ändrade skripten till en offentlig blobcontainer i Azure Storage och använda det ändrade skriptet för att installera paketen.
   >    Mer information om hur du utvecklar skriptåtgärder finns i [Skriptåtgärdsutveckling](../hdinsight-hadoop-script-actions-linux.md).

   :::image type="content" source="./media/r-server-hdinsight-manage/submit-script-action.png" alt-text="Azure Portal skicka skript åtgärd" border="true":::

4. Välj **Skapa** för att köra skriptet. När skriptet är färdigt är R-paketen tillgängliga på alla arbetsnoder.

## <a name="next-steps"></a>Nästa steg

* [Operationalisera ML-tjänstkluster i HDInsight](r-server-operationalize.md)
* [Beräknings kontext alternativ för ML tjänst kluster i HDInsight](r-server-compute-contexts.md)
* [Alternativ för Azure Storage för ML-tjänstkluster i HDInsight](r-server-storage.md)