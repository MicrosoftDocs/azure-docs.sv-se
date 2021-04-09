---
title: Python UDF med Apache Hive och Apache gris – Azure HDInsight
description: Lär dig hur du använder python-användardefinierade funktioner (UDF) från Apache Hive och Apache gris i HDInsight, Apache Hadoop Technology stack på Azure.
ms.service: hdinsight
ms.topic: how-to
ms.date: 11/15/2019
ms.custom: H1Hack27Feb2017,hdinsightactive, devx-track-python
ms.openlocfilehash: 593b809813f949cd1d0bcc17e1d1b7255ea19130
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "98944275"
---
# <a name="use-python-user-defined-functions-udf-with-apache-hive-and-apache-pig-in-hdinsight"></a>Använda python-användardefinierade funktioner (UDF) med Apache Hive och Apache-gris i HDInsight

Lär dig hur du använder python-användardefinierade funktioner (UDF) med Apache Hive och Apache gris i Apache Hadoop på Azure HDInsight.

## <a name="python-on-hdinsight"></a><a name="python"></a>Python på HDInsight

Python 2.7 installeras som standard på HDInsight 3,0 och senare. Apache Hive kan användas med den här versionen av python för bearbetning av data strömmar. Stream-bearbetningen använder STDOUT och STDIN för att skicka data mellan Hive och UDF.

HDInsight innehåller även jython, som är en python-implementering som skrivits i Java. Jython körs direkt på Java Virtual Machine och använder inte direkt uppspelning. Jython är den rekommenderade python-tolken när du använder python med gris.

## <a name="prerequisites"></a>Förutsättningar

* **Ett Hadoop-kluster i HDInsight**. Se [Kom igång med HDInsight på Linux](apache-hadoop-linux-tutorial-get-started.md).
* **En SSH-klient**. Mer information finns i [Ansluta till HDInsight (Apache Hadoop) med hjälp av SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).
* [URI-schemat](../hdinsight-hadoop-linux-information.md#URI-and-scheme) för klustrets primära lagring. Detta gäller `wasb://` Azure Storage för `abfs://` Azure Data Lake Storage Gen2 eller adl://för Azure Data Lake Storage gen1. Om säker överföring har Aktiver ATS för Azure Storage skulle URI: n bli wasbs://.  Se även [säker överföring](../../storage/common/storage-require-secure-transfer.md).
* **Möjlig ändring av lagrings konfigurationen.**  Se [lagrings konfiguration](#storage-configuration) om du använder typ av lagrings konto `BlobStorage` .
* Valfritt.  Om du planerar att använda PowerShell behöver du AZ- [modulen](/powershell/azure/new-azureps-module-az) installerad.

> [!NOTE]  
> Lagrings kontot som används i den här artikeln var Azure Storage med [säker överföring](../../storage/common/storage-require-secure-transfer.md) aktiverat och `wasbs` används därför i hela artikeln.

## <a name="storage-configuration"></a>Storage-konfiguration

Ingen åtgärd krävs om det lagrings konto som används är av typen `Storage (general purpose v1)` eller `StorageV2 (general purpose v2)` .  Processen i den här artikeln kommer att generera utdata till minst `/tezstaging` .  En standard-Hadoop-konfiguration kommer att innehålla `/tezstaging` i `fs.azure.page.blob.dir` konfigurations variabeln `core-site.xml` för-tjänsten `HDFS` .  Den här konfigurationen innebär att utdata till katalogen är sid-blobbar som inte stöds för lagrings konto typen `BlobStorage` .  `BlobStorage`Ta bort `/tezstaging` från konfigurations variabeln för att använda den här artikeln `fs.azure.page.blob.dir` .  Konfigurationen kan nås från [Ambari-användargränssnittet](../hdinsight-hadoop-manage-ambari.md).  Annars får du fel meddelandet: `Page blob is not supported for this account type.`

> [!WARNING]  
> Stegen i det här dokumentet gör följande antaganden:  
>
> * Du skapar python-skripten i din lokala utvecklings miljö.
> * Du överför skripten till HDInsight med antingen `scp` kommandot eller det angivna PowerShell-skriptet.
>
> Om du vill använda [Azure Cloud Shell (bash)](../../cloud-shell/overview.md) för att arbeta med HDInsight måste du:
>
> * Skapa skripten i Cloud Shell-miljön.
> * Används `scp` för att ladda upp filerna från Cloud Shell till HDInsight.
> * Använd `ssh` från Cloud Shell för att ansluta till HDInsight och kör exemplen.

## <a name="apache-hive-udf"></a><a name="hivepython"></a>Apache Hive UDF

Python kan användas som UDF från Hive via HiveQL- `TRANSFORM` instruktionen. Följande HiveQL anropar till exempel den fil som `hiveudf.py` lagras i standard Azure Storages kontot för klustret.

```hiveql
add file wasbs:///hiveudf.py;

SELECT TRANSFORM (clientid, devicemake, devicemodel)
    USING 'python hiveudf.py' AS
    (clientid string, phoneLabel string, phoneHash string)
FROM hivesampletable
ORDER BY clientid LIMIT 50;
```

Det här exemplet gör:

1. `add file`Instruktionen i början av filen lägger till `hiveudf.py` filen i den distribuerade cachen så att den är tillgänglig för alla noder i klustret.
2. `SELECT TRANSFORM ... USING`Instruktionen väljer data från `hivesampletable` . Den skickar även ClientID-, devicemake-och devicemodel-värdena till `hiveudf.py` skriptet.
3. `AS`Satsen beskriver fälten som returneras från `hiveudf.py` .

<a name="streamingpy"></a>

### <a name="create-file"></a>Skapa fil

Skapa en textfil med namnet i utvecklings miljön `hiveudf.py` . Använd följande kod som filens innehåll:

```python
#!/usr/bin/env python
import sys
import string
import hashlib

while True:
    line = sys.stdin.readline()
    if not line:
        break

    line = string.strip(line, "\n ")
    clientid, devicemake, devicemodel = string.split(line, "\t")
    phone_label = devicemake + ' ' + devicemodel
    print "\t".join([clientid, phone_label, hashlib.md5(phone_label).hexdigest()])
```

Det här skriptet utför följande åtgärder:

1. Läser en rad med data från STDIN.
2. Det avslutande rad matnings tecknet tas bort med hjälp av `string.strip(line, "\n ")` .
3. Vid data bearbetning innehåller en enskild rad alla värden med ett tabbtecken mellan varje värde. `string.split(line, "\t")`Du kan använda den för att dela upp inmatade värden på varje flik och bara returnera fälten.
4. När bearbetningen är klar måste utdata skrivas till STDOUT som en enskild linje, med en flik mellan varje fält. Till exempel `print "\t".join([clientid, phone_label, hashlib.md5(phone_label).hexdigest()])`.
5. `while`Loopen upprepas tills ingen `line` läses.

Skriptets utdata är en sammanfogning av indatavärdena för `devicemake` och `devicemodel` , och en hash av det sammanfogade värdet.

### <a name="upload-file-shell"></a>Ladda upp fil (Shell)

I kommandona nedan ersätter `sshuser` du med det faktiska användar namnet om det skiljer sig.  Ersätt `mycluster` med det faktiska kluster namnet.  Se till att din arbets katalog är där filen finns.

1. Används `scp` för att kopiera filerna till ditt HDInsight-kluster. Redigera och ange kommandot nedan:

    ```cmd
    scp hiveudf.py sshuser@mycluster-ssh.azurehdinsight.net:
    ```

2. Använd SSH för att ansluta till klustret.  Redigera och ange kommandot nedan:

    ```cmd
    ssh sshuser@mycluster-ssh.azurehdinsight.net
    ```

3. Från SSH-sessionen lägger du till de python-filer som överfördes tidigare till lagrings utrymmet för klustret.

    ```bash
    hdfs dfs -put hiveudf.py /hiveudf.py
    ```

### <a name="use-hive-udf-shell"></a>Använd Hive UDF (Shell)

1. Om du vill ansluta till Hive använder du följande kommando från den öppna SSH-sessionen:

    ```bash
    beeline -u 'jdbc:hive2://headnodehost:10001/;transportMode=http'
    ```

    Det här kommandot startar Beeline-klienten.

2. Ange följande fråga i `0: jdbc:hive2://headnodehost:10001/>` prompten:

   ```hive
   add file wasbs:///hiveudf.py;
   SELECT TRANSFORM (clientid, devicemake, devicemodel)
       USING 'python hiveudf.py' AS
       (clientid string, phoneLabel string, phoneHash string)
   FROM hivesampletable
   ORDER BY clientid LIMIT 50;
   ```

3. När du har angett den sista raden ska jobbet starta. När jobbet har slutförts returneras utdata som liknar följande exempel:

    ```output
    100041    RIM 9650    d476f3687700442549a83fac4560c51c
    100041    RIM 9650    d476f3687700442549a83fac4560c51c
    100042    Apple iPhone 4.2.x    375ad9a0ddc4351536804f1d5d0ea9b9
    100042    Apple iPhone 4.2.x    375ad9a0ddc4351536804f1d5d0ea9b9
    100042    Apple iPhone 4.2.x    375ad9a0ddc4351536804f1d5d0ea9b9
    ```

4. Om du vill avsluta Beeline anger du följande kommando:

    ```hive
    !q
    ```

### <a name="upload-file-powershell"></a>Ladda upp fil (PowerShell)

PowerShell kan också användas för att fjärrköra Hive-frågor. Se till att din arbets katalog finns `hiveudf.py` .  Använd följande PowerShell-skript för att köra en Hive-fråga som använder `hiveudf.py` skriptet:

```PowerShell
# Login to your Azure subscription
# Is there an active Azure subscription?
$sub = Get-AzSubscription -ErrorAction SilentlyContinue
if(-not($sub))
{
    Connect-AzAccount
}

# If you have multiple subscriptions, set the one to use
# Select-AzSubscription -SubscriptionId "<SUBSCRIPTIONID>"

# Revise file path as needed
$pathToStreamingFile = ".\hiveudf.py"

# Get cluster info
$clusterName = Read-Host -Prompt "Enter the HDInsight cluster name"
$clusterInfo = Get-AzHDInsightCluster -ClusterName $clusterName
$resourceGroup = $clusterInfo.ResourceGroup
$storageAccountName=$clusterInfo.DefaultStorageAccount.split('.')[0]
$container=$clusterInfo.DefaultStorageContainer
$storageAccountKey=(Get-AzStorageAccountKey `
   -ResourceGroupName $resourceGroup `
   -Name $storageAccountName)[0].Value

# Create an Azure Storage context
$context = New-AzStorageContext `
    -StorageAccountName $storageAccountName `
    -StorageAccountKey $storageAccountKey

# Upload local files to an Azure Storage blob
Set-AzStorageBlobContent `
    -File $pathToStreamingFile `
    -Blob "hiveudf.py" `
    -Container $container `
    -Context $context
```

> [!NOTE]  
> Mer information om hur du laddar upp filer finns i [överföra data för Apache Hadoop jobb i HDInsight](../hdinsight-upload-data.md) -dokument.

#### <a name="use-hive-udf"></a>Använd Hive UDF

```PowerShell
# Script should stop on failures
$ErrorActionPreference = "Stop"

# Login to your Azure subscription
# Is there an active Azure subscription?
$sub = Get-AzSubscription -ErrorAction SilentlyContinue
if(-not($sub))
{
    Connect-AzAccount
}

# If you have multiple subscriptions, set the one to use
# Select-AzSubscription -SubscriptionId "<SUBSCRIPTIONID>"

# Get cluster info
$clusterName = Read-Host -Prompt "Enter the HDInsight cluster name"
$creds=Get-Credential -UserName "admin" -Message "Enter the login for the cluster"

$HiveQuery = "add file wasbs:///hiveudf.py;" +
                "SELECT TRANSFORM (clientid, devicemake, devicemodel) " +
                "USING 'python hiveudf.py' AS " +
                "(clientid string, phoneLabel string, phoneHash string) " +
                "FROM hivesampletable " +
                "ORDER BY clientid LIMIT 50;"

# Create Hive job object
$jobDefinition = New-AzHDInsightHiveJobDefinition `
    -Query $HiveQuery

# For status bar updates
$activity="Hive query"

# Progress bar (optional)
Write-Progress -Activity $activity -Status "Starting query..."

# Start defined Azure HDInsight job on specified cluster.
$job = Start-AzHDInsightJob `
    -ClusterName $clusterName `
    -JobDefinition $jobDefinition `
    -HttpCredential $creds

# Progress bar (optional)
Write-Progress -Activity $activity -Status "Waiting on query to complete..."

# Wait for completion or failure of specified job
Wait-AzHDInsightJob `
    -JobId $job.JobId `
    -ClusterName $clusterName `
    -HttpCredential $creds

# Uncomment the following to see stderr output
<#
Get-AzHDInsightJobOutput `
   -Clustername $clusterName `
   -JobId $job.JobId `
   -HttpCredential $creds `
   -DisplayOutputType StandardError
#>

# Progress bar (optional)
Write-Progress -Activity $activity -Status "Retrieving output..."

# Gets the log output
Get-AzHDInsightJobOutput `
    -Clustername $clusterName `
    -JobId $job.JobId `
    -HttpCredential $creds
```

Utdata för **Hive** -jobbet bör se ut ungefär som i följande exempel:

```output
100041    RIM 9650    d476f3687700442549a83fac4560c51c
100041    RIM 9650    d476f3687700442549a83fac4560c51c
100042    Apple iPhone 4.2.x    375ad9a0ddc4351536804f1d5d0ea9b9
100042    Apple iPhone 4.2.x    375ad9a0ddc4351536804f1d5d0ea9b9
100042    Apple iPhone 4.2.x    375ad9a0ddc4351536804f1d5d0ea9b9
```

## <a name="apache-pig-udf"></a><a name="pigpython"></a>Apache gris UDF

Ett Python-skript kan användas som ett UDF-skript från gris- `GENERATE` instruktionen. Du kan köra skriptet med antingen jython eller C python.

* Jython körs på JVM och kan internt anropas från gris.
* C python är en extern process, så data från gris på JVM skickas till skriptet som körs i en python-process. Utdata från python-skriptet skickas tillbaka till gris.

Om du vill ange python-tolken använder du `register` när du refererar till python-skriptet. I följande exempel registreras skript med gris som `myfuncs` :

* **Så här använder du jython**: `register '/path/to/pigudf.py' using jython as myfuncs;`
* **Använda C python**: `register '/path/to/pigudf.py' using streaming_python as myfuncs;`

> [!IMPORTANT]  
> När du använder jython kan sökvägen till pig_jython-filen vara antingen en lokal sökväg eller en WASBS://-sökväg. Men när du använder C python måste du referera till en fil i det lokala fil systemet på den nod som du använder för att skicka gris-jobbet.

När du har registrerat det här exemplet är det samma som för båda:

```pig
LOGS = LOAD 'wasbs:///example/data/sample.log' as (LINE:chararray);
LOG = FILTER LOGS by LINE is not null;
DETAILS = FOREACH LOG GENERATE myfuncs.create_structure(LINE);
DUMP DETAILS;
```

Det här exemplet gör:

1. Den första raden läser in exempel data filen `sample.log` i `LOGS` . Den definierar även varje post som en `chararray` .
2. Nästa rad filtrerar bort alla null-värden och lagrar resultatet av åtgärden i `LOG` .
3. Därefter itererar den över posterna i `LOG` och använder `GENERATE` för att anropa `create_structure` metoden i python/jython-skriptet som läses in som `myfuncs` . `LINE` används för att skicka den aktuella posten till funktionen.
4. Slutligen dumpas utdata till STDOUT med hjälp av `DUMP` kommandot. Det här kommandot visar resultatet när åtgärden har slutförts.

### <a name="create-file"></a>Skapa fil

Skapa en textfil med namnet i utvecklings miljön `pigudf.py` . Använd följande kod som filens innehåll:

<a name="streamingpy"></a>

```python
# Uncomment the following if using C Python
#from pig_util import outputSchema


@outputSchema("log: {(date:chararray, time:chararray, classname:chararray, level:chararray, detail:chararray)}")
def create_structure(input):
    if (input.startswith('java.lang.Exception')):
        input = input[21:len(input)] + ' - java.lang.Exception'
    date, time, classname, level, detail = input.split(' ', 4)
    return date, time, classname, level, detail
```

I det latinska exemplet i gris `LINE` definieras indatamängden som en chararray eftersom det inte finns något konsekvent schema för indatamängden. Python-skriptet omvandlar data till ett konsekvent schema för utdata.

1. `@outputSchema`Instruktionen definierar formatet på de data som returneras till gris. I det här fallet är det en **data uppsättning**, som är en data typ av gris. Säcken innehåller följande fält, som alla är chararray (strängar):

   * Datum – datum då logg posten skapades
   * tid – tiden då logg posten skapades
   * className – klass namnet som posten skapades för
   * nivå – logg nivån
   * information – utförlig information för logg posten

2. Därefter `def create_structure(input)` definierar funktionen som gris skickar rad objekt till.

3. Exempel data, `sample.log` som huvudsakligen följer schemat för datum, tid, className, nivå och detaljer. Det innehåller dock några rader som börjar med `*java.lang.Exception*` . Dessa rader måste ändras för att matcha schemat. `if`Instruktionen söker efter dem och massages sedan indata för att flytta `*java.lang.Exception*` strängen till slutet, och data tas i rad med det förväntade schemat.

4. Sedan `split` används kommandot för att dela upp data vid de första fyra blank stegs tecknen. Utdata tilldelas,,, `date` `time` `classname` `level` och `detail` .

5. Slutligen returneras värdena till gris.

När data returneras till gris, har den ett konsekvent schema som definieras i `@outputSchema` instruktionen.

### <a name="upload-file-shell"></a>Ladda upp fil (Shell)

I kommandona nedan ersätter `sshuser` du med det faktiska användar namnet om det skiljer sig.  Ersätt `mycluster` med det faktiska kluster namnet.  Se till att din arbets katalog är där filen finns.

1. Används `scp` för att kopiera filerna till ditt HDInsight-kluster. Redigera och ange kommandot nedan:

    ```cmd
    scp pigudf.py sshuser@mycluster-ssh.azurehdinsight.net:
    ```

2. Använd SSH för att ansluta till klustret.  Redigera och ange kommandot nedan:

    ```cmd
    ssh sshuser@mycluster-ssh.azurehdinsight.net
    ```

3. Från SSH-sessionen lägger du till de python-filer som överfördes tidigare till lagrings utrymmet för klustret.

    ```bash
    hdfs dfs -put pigudf.py /pigudf.py
    ```

### <a name="use-pig-udf-shell"></a>Använda UDF UDF (Shell)

1. Om du vill ansluta till gris använder du följande kommando från den öppna SSH-sessionen:

    ```bash
    pig
    ```

2. Ange följande instruktioner i `grunt>` prompten:

   ```pig
   Register wasbs:///pigudf.py using jython as myfuncs;
   LOGS = LOAD 'wasbs:///example/data/sample.log' as (LINE:chararray);
   LOG = FILTER LOGS by LINE is not null;
   DETAILS = foreach LOG generate myfuncs.create_structure(LINE);
   DUMP DETAILS;
   ```

3. När du har angett följande rad bör jobbet starta. När jobbet har slutförts returneras utdata som liknar följande data:

    ```output
    ((2012-02-03,20:11:56,SampleClass5,[TRACE],verbose detail for id 990982084))
    ((2012-02-03,20:11:56,SampleClass7,[TRACE],verbose detail for id 1560323914))
    ((2012-02-03,20:11:56,SampleClass8,[DEBUG],detail for id 2083681507))
    ((2012-02-03,20:11:56,SampleClass3,[TRACE],verbose detail for id 1718828806))
    ((2012-02-03,20:11:56,SampleClass3,[INFO],everything normal for id 530537821))
    ```

4. Använd `quit` för att avsluta grunt-gränssnittet och Använd sedan följande för att redigera pigudf.py-filen i det lokala fil systemet:

    ```bash
    nano pigudf.py
    ```

5. Ta bort kommentaren till följande rad i redigeraren genom att ta bort det `#` från början av raden:

    ```bash
    #from pig_util import outputSchema
    ```

    Den här raden ändrar python-skriptet så att det fungerar med C python i stället för jython. När ändringen har gjorts använder du **CTRL + X** för att avsluta redigeraren. Välj **Y** och sedan **RETUR** för att spara ändringarna.

6. Använd `pig` kommandot för att starta gränssnittet igen. När du är i `grunt>` kommando tolken använder du följande för att köra python-skriptet med hjälp av C python-tolken.

   ```pig
   Register 'pigudf.py' using streaming_python as myfuncs;
   LOGS = LOAD 'wasbs:///example/data/sample.log' as (LINE:chararray);
   LOG = FILTER LOGS by LINE is not null;
   DETAILS = foreach LOG generate myfuncs.create_structure(LINE);
   DUMP DETAILS;
   ```

    När jobbet har slutförts bör du se samma utdata som när du tidigare körde skriptet med jython.

### <a name="upload-file-powershell"></a>Ladda upp fil (PowerShell)

PowerShell kan också användas för att fjärrköra Hive-frågor. Se till att din arbets katalog finns `pigudf.py` .  Använd följande PowerShell-skript för att köra en Hive-fråga som använder `pigudf.py` skriptet:

```PowerShell
# Login to your Azure subscription
# Is there an active Azure subscription?
$sub = Get-AzSubscription -ErrorAction SilentlyContinue
if(-not($sub))
{
    Connect-AzAccount
}

# If you have multiple subscriptions, set the one to use
# Select-AzSubscription -SubscriptionId "<SUBSCRIPTIONID>"

# Revise file path as needed
$pathToJythonFile = ".\pigudf.py"


# Get cluster info
$clusterName = Read-Host -Prompt "Enter the HDInsight cluster name"
$clusterInfo = Get-AzHDInsightCluster -ClusterName $clusterName
$resourceGroup = $clusterInfo.ResourceGroup
$storageAccountName=$clusterInfo.DefaultStorageAccount.split('.')[0]
$container=$clusterInfo.DefaultStorageContainer
$storageAccountKey=(Get-AzStorageAccountKey `
   -ResourceGroupName $resourceGroup `
   -Name $storageAccountName)[0].Value

# Create an Azure Storage context
$context = New-AzStorageContext `
    -StorageAccountName $storageAccountName `
    -StorageAccountKey $storageAccountKey

# Upload local files to an Azure Storage blob
Set-AzStorageBlobContent `
    -File $pathToJythonFile `
    -Blob "pigudf.py" `
    -Container $container `
    -Context $context
```

### <a name="use-pig-udf-powershell"></a>Använd gris UDF (PowerShell)

> [!NOTE]  
> När du skickar ett jobb från en fjärrdator med hjälp av PowerShell går det inte att använda C python som tolk.

PowerShell kan också användas för att köra latinska jobb i gris. Använd följande PowerShell-skript om du vill köra ett gris-latinskt jobb som använder `pigudf.py` skriptet:

```PowerShell
# Script should stop on failures
$ErrorActionPreference = "Stop"

# Login to your Azure subscription
# Is there an active Azure subscription?
$sub = Get-AzSubscription -ErrorAction SilentlyContinue
if(-not($sub))
{
    Connect-AzAccount
}

# Get cluster info
$clusterName = Read-Host -Prompt "Enter the HDInsight cluster name"
$creds=Get-Credential -UserName "admin" -Message "Enter the login for the cluster"


$PigQuery = "Register wasbs:///pigudf.py using jython as myfuncs;" +
            "LOGS = LOAD 'wasbs:///example/data/sample.log' as (LINE:chararray);" +
            "LOG = FILTER LOGS by LINE is not null;" +
            "DETAILS = foreach LOG generate myfuncs.create_structure(LINE);" +
            "DUMP DETAILS;"

# Create Pig job object
$jobDefinition = New-AzHDInsightPigJobDefinition -Query $PigQuery

# For status bar updates
$activity="Pig job"

# Progress bar (optional)
Write-Progress -Activity $activity -Status "Starting job..."

# Start defined Azure HDInsight job on specified cluster.
$job = Start-AzHDInsightJob `
    -ClusterName $clusterName `
    -JobDefinition $jobDefinition `
    -HttpCredential $creds

# Progress bar (optional)
Write-Progress -Activity $activity -Status "Waiting for the Pig job to complete..."

# Wait for completion or failure of specified job
Wait-AzHDInsightJob `
    -Job $job.JobId `
    -ClusterName $clusterName `
    -HttpCredential $creds

# Uncomment the following to see stderr output
<#
Get-AzHDInsightJobOutput `
    -Clustername $clusterName `
    -JobId $job.JobId `
    -HttpCredential $creds `
    -DisplayOutputType StandardError
#>

# Progress bar (optional)
Write-Progress -Activity $activity "Retrieving output..."

# Gets the log output
Get-AzHDInsightJobOutput `
    -Clustername $clusterName `
    -JobId $job.JobId `
    -HttpCredential $creds
```

Utdata för jobbet **gris** bör se ut ungefär så här:

```output
((2012-02-03,20:11:56,SampleClass5,[TRACE],verbose detail for id 990982084))
((2012-02-03,20:11:56,SampleClass7,[TRACE],verbose detail for id 1560323914))
((2012-02-03,20:11:56,SampleClass8,[DEBUG],detail for id 2083681507))
((2012-02-03,20:11:56,SampleClass3,[TRACE],verbose detail for id 1718828806))
((2012-02-03,20:11:56,SampleClass3,[INFO],everything normal for id 530537821))
```

## <a name="troubleshooting"></a><a name="troubleshooting"></a>Felsökning

### <a name="errors-when-running-jobs"></a>Fel vid körning av jobb

När du kör Hive-jobbet kan du stöta på ett fel som liknar följande text:

```output
Caused by: org.apache.hadoop.hive.ql.metadata.HiveException: [Error 20001]: An error occurred while reading or writing to your custom script. It may have crashed with an error.
```

Det här problemet kan bero på att raden slutar i python-filen. Många Windows-redigerare använder som standard CRLF som linje slut, men Linux-program förväntar sig vanligt vis LF.

Du kan använda följande PowerShell-uttryck för att ta bort CR-tecknen innan du laddar upp filen till HDInsight:

[!code-powershell[main](../../../powershell_scripts/hdinsight/run-python-udf/run-python-udf.ps1?range=148-150)]

### <a name="powershell-scripts"></a>PowerShell-skript

Båda de PowerShell-skript som används för att köra exemplen innehåller en kommenterad linje som visar fel utdata för jobbet. Om du inte ser förväntat utdata för jobbet kan du ta bort kommentaren till följande rad och se om fel informationen indikerar ett problem.

[!code-powershell[main](../../../powershell_scripts/hdinsight/run-python-udf/run-python-udf.ps1?range=135-139)]

Fel informationen (STDERR) och resultatet av jobbet (STDOUT) loggas också i HDInsight-lagringen.

| För det här jobbet... | Titta på de här filerna i BLOB-behållaren |
| --- | --- |
| Hive |/HivePython/stderr<p>/HivePython/stdout |
| Pig |/PigPython/stderr<p>/PigPython/stdout |

## <a name="next-steps"></a><a name="next"></a>Nästa steg

Om du behöver läsa in Python-moduler som inte tillhandahålls som standard, se [så här distribuerar du en modul till Azure HDInsight](/archive/blogs/benjguin/how-to-deploy-a-python-module-to-windows-azure-hdinsight).

Andra sätt att använda gris, Hive och lära dig mer om hur du använder MapReduce finns i följande dokument:

* [Använda Apache Hive med HDInsight](hdinsight-use-hive.md)
* [Använda MapReduce med HDInsight](hdinsight-use-mapreduce.md)