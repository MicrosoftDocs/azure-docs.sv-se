---
title: Använd Apache Maven för att bygga en Java HBase-klient för Azure HDInsight
description: Lär dig hur du använder Apache Maven för att bygga ett Java-baserat Apache HBase-program och sedan distribuera det till HBase på Azure HDInsight.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,seodec18, devx-track-java
ms.date: 12/24/2019
ms.openlocfilehash: 13616cf99db39ec4dac1d13e3dcd2cefc5a44614
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/28/2021
ms.locfileid: "98942968"
---
# <a name="build-java-applications-for-apache-hbase"></a>Utveckla Java-program för Apache HBase

Lär dig hur du skapar ett [Apache HBase](https://hbase.apache.org/) -program i Java. Använd sedan programmet med HBase på Azure HDInsight.

Stegen i det här dokumentet använder [Apache maven](https://maven.apache.org/) för att skapa och bygga projektet. Maven är ett verktyg för program varu projektledning och förståelse som gör att du kan bygga program vara, dokumentation och rapporter för Java-projekt.

## <a name="prerequisites"></a>Förutsättningar

* Ett Apache HBase-kluster i HDInsight. Se [Kom igång med Apache HBase](./apache-hbase-tutorial-get-started-linux.md).

* [Java Developer Kit (JDK) version 8](/azure/developer/java/fundamentals/java-jdk-long-term-support).

* [Apache maven](https://maven.apache.org/download.cgi) korrekt [installerat](https://maven.apache.org/install.html) enligt Apache.  Maven är ett projekt versions system för Java-projekt.

* En SSH-klient. Mer information finns i [Ansluta till HDInsight (Apache Hadoop) med hjälp av SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).

* Om du använder PowerShell behöver du AZ- [modulen](/powershell/azure/).

* En textredigerare. I den här artikeln används Microsoft Notepad.

## <a name="test-environment"></a>Test miljö

Miljön som används för den här artikeln var en dator som kör Windows 10.  Kommandona kördes i en kommando tolk och de olika filerna redigerades med anteckningar. Ändra detta för din miljö.

I en kommando tolk anger du följande kommandon för att skapa en fungerande miljö:

```cmd
IF NOT EXIST C:\HDI MKDIR C:\HDI
cd C:\HDI
```

## <a name="create-a-maven-project"></a>Skapa ett Maven-projekt

1. Ange följande kommando för att skapa ett Maven-projekt med namnet **hbaseapp**:

    ```cmd
    mvn archetype:generate -DgroupId=com.microsoft.examples -DartifactId=hbaseapp -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false

    cd hbaseapp
    mkdir conf
    ```

    Det här kommandot skapar en katalog med namnet `hbaseapp` på den aktuella platsen, som innehåller ett Basic Maven-projekt. Det andra kommandot ändrar arbets katalogen till `hbaseapp` . Det tredje kommandot skapar en ny katalog, `conf` som kommer att användas senare. `hbaseapp`Katalogen innehåller följande objekt:

    * `pom.xml`: Projekt objekts modellen ([Pom](https://maven.apache.org/guides/introduction/introduction-to-the-pom.html)) innehåller information och konfigurations information som används för att bygga projektet.
    * `src\main\java\com\microsoft\examples`: Innehåller din program kod.
    * `src\test\java\com\microsoft\examples`: Innehåller tester för ditt program.

2. Ta bort den genererade exempel koden. Ta bort de genererade test-och programfilerna `AppTest.java` och `App.java` genom att ange följande kommandon:

    ```cmd
    DEL src\main\java\com\microsoft\examples\App.java
    DEL src\test\java\com\microsoft\examples\AppTest.java
    ```

## <a name="update-the-project-object-model"></a>Uppdatera projekt objekts modellen

En fullständig referens till pom.xml-filen finns i https://maven.apache.org/pom.html .  Öppna `pom.xml` genom att ange kommandot nedan:

```cmd
notepad pom.xml
```

### <a name="add-dependencies"></a>Lägg till beroenden

I `pom.xml` lägger du till följande text i `<dependencies>` avsnittet:

```xml
<dependency>
    <groupId>org.apache.hbase</groupId>
    <artifactId>hbase-shaded-client</artifactId>
    <version>1.1.2</version>
</dependency>
<dependency>
    <groupId>org.apache.phoenix</groupId>
    <artifactId>phoenix-core</artifactId>
    <version>4.14.1-HBase-1.1</version>
</dependency>
```  

Det här avsnittet anger att projektet behöver **HBase-client-** och **Phoenix-Core-** komponenter. Vid kompileringen laddas dessa beroenden ned från standard lagrings platsen för maven. Du kan använda [maven Central lagrings plats](https://search.maven.org/artifact/org.apache.hbase/hbase-client/1.1.2/jar) för att lära dig mer om det här beroendet.

> [!IMPORTANT]  
> Versions numret för HBase-klienten måste matcha den version av Apache HBase som medföljer ditt HDInsight-kluster. Använd följande tabell för att hitta rätt versions nummer.

| HDInsight-kluster version | Apache HBase-version som ska användas |
| --- | --- |
| 3,6 | 1.1.2 |
| 4,0 | 2.0.0 |

Mer information om HDInsight-versioner och-komponenter finns i [Vad är de olika Apache Hadoops komponenter som är tillgängliga med HDInsight](../hdinsight-component-versioning.md).

### <a name="build-configuration"></a>Skapa konfiguration

Med maven-plugin-program kan du anpassa projektets Bygg steg. Det här avsnittet används för att lägga till plugin-program, resurser och andra Bygg konfigurations alternativ.

Lägg till följande kod i `pom.xml` filen och spara och stäng sedan filen. Texten måste vara inuti `<project>...</project>` taggarna i filen, till exempel mellan `</dependencies>` och `</project>` .

```xml
<build>
    <sourceDirectory>src</sourceDirectory>
    <resources>
    <resource>
        <directory>${basedir}/conf</directory>
        <filtering>false</filtering>
        <includes>
        <include>hbase-site.xml</include>
        </includes>
    </resource>
    </resources>
    <plugins>
    <plugin>
        <groupId>org.apache.maven.plugins</groupId>
        <artifactId>maven-compiler-plugin</artifactId>
                <version>3.8.1</version>
        <configuration>
            <source>1.8</source>
            <target>1.8</target>
        </configuration>
        </plugin>
    <plugin>
        <groupId>org.apache.maven.plugins</groupId>
        <artifactId>maven-shade-plugin</artifactId>
        <version>3.2.1</version>
        <configuration>
        <transformers>
            <transformer implementation="org.apache.maven.plugins.shade.resource.ApacheLicenseResourceTransformer">
            </transformer>
        </transformers>
        </configuration>
        <executions>
        <execution>
            <phase>package</phase>
            <goals>
            <goal>shade</goal>
            </goals>
        </execution>
        </executions>
    </plugin>
    </plugins>
</build>
```

I det här avsnittet konfigureras en resurs ( `conf/hbase-site.xml` ) som innehåller konfigurations information för HBase.

> [!NOTE]  
> Du kan också ange konfigurations värden via kod. Se kommentarerna i `CreateTable` exemplet.

I det här avsnittet konfigureras också [Apache maven compiler-plugin](https://maven.apache.org/plugins/maven-compiler-plugin/) och [Apache maven Shader](https://maven.apache.org/plugins/maven-shade-plugin/). Compiler-plugin-programmet används för att kompilera topologin. Skugga-plugin-programmet används för att förhindra licens duplicering i JAR-paketet som skapats av Maven. Det här plugin-programmet används för att förhindra fel vid körning av "dubbla licensfiler" vid körning i HDInsight-klustret. Att använda maven-Shader-plugin med `ApacheLicenseResourceTransformer` implementeringen förhindrar felet.

Maven-Shader-plugin-programmet producerar också en Uber-jar som innehåller alla beroenden som krävs av programmet.

### <a name="download-the-hbase-sitexml"></a>Ladda ned hbase-site.xml

Använd följande kommando för att kopiera HBase-konfigurationen från HBase-klustret till `conf` katalogen. Ersätt `CLUSTERNAME` med ditt HDInsight-kluster namn och ange sedan kommandot:

```cmd
scp sshuser@CLUSTERNAME-ssh.azurehdinsight.net:/etc/hbase/conf/hbase-site.xml ./conf/hbase-site.xml
```

## <a name="create-the-application"></a>Skapa programmet

### <a name="implement-a-createtable-class"></a>Implementera en CreateTable-klass

Ange kommandot nedan för att skapa och öppna en ny fil `CreateTable.java` . Välj **Ja** vid prompten om du vill skapa en ny fil.

```cmd
notepad src\main\java\com\microsoft\examples\CreateTable.java
```

Kopiera och klistra sedan in Java-koden nedan i den nya filen. Stäng sedan filen.

```java
package com.microsoft.examples;
import java.io.IOException;

import org.apache.hadoop.conf.Configuration;
import org.apache.hadoop.hbase.HBaseConfiguration;
import org.apache.hadoop.hbase.client.HBaseAdmin;
import org.apache.hadoop.hbase.HTableDescriptor;
import org.apache.hadoop.hbase.TableName;
import org.apache.hadoop.hbase.HColumnDescriptor;
import org.apache.hadoop.hbase.client.HTable;
import org.apache.hadoop.hbase.client.Put;
import org.apache.hadoop.hbase.util.Bytes;

public class CreateTable {
    public static void main(String[] args) throws IOException {
    Configuration config = HBaseConfiguration.create();

    // Example of setting zookeeper values for HDInsight
    // in code instead of an hbase-site.xml file
    //
    // config.set("hbase.zookeeper.quorum",
    //            "zookeepernode0,zookeepernode1,zookeepernode2");
    //config.set("hbase.zookeeper.property.clientPort", "2181");
    //config.set("hbase.cluster.distributed", "true");
    //
    //NOTE: Actual zookeeper host names can be found using Ambari:
    //curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/hosts"

    //Linux-based HDInsight clusters use /hbase-unsecure as the znode parent
    config.set("zookeeper.znode.parent","/hbase-unsecure");

    // create an admin object using the config
    HBaseAdmin admin = new HBaseAdmin(config);

    // create the table...
    HTableDescriptor tableDescriptor = new HTableDescriptor(TableName.valueOf("people"));
    // ... with two column families
    tableDescriptor.addFamily(new HColumnDescriptor("name"));
    tableDescriptor.addFamily(new HColumnDescriptor("contactinfo"));
    admin.createTable(tableDescriptor);

    // define some people
    String[][] people = {
        { "1", "Marcel", "Haddad", "marcel@fabrikam.com"},
        { "2", "Franklin", "Holtz", "franklin@contoso.com" },
        { "3", "Dwayne", "McKee", "dwayne@fabrikam.com" },
        { "4", "Rae", "Schroeder", "rae@contoso.com" },
        { "5", "Rosalie", "burton", "rosalie@fabrikam.com"},
        { "6", "Gabriela", "Ingram", "gabriela@contoso.com"} };

    HTable table = new HTable(config, "people");

    // Add each person to the table
    //   Use the `name` column family for the name
    //   Use the `contactinfo` column family for the email
    for (int i = 0; i< people.length; i++) {
        Put person = new Put(Bytes.toBytes(people[i][0]));
        person.add(Bytes.toBytes("name"), Bytes.toBytes("first"), Bytes.toBytes(people[i][1]));
        person.add(Bytes.toBytes("name"), Bytes.toBytes("last"), Bytes.toBytes(people[i][2]));
        person.add(Bytes.toBytes("contactinfo"), Bytes.toBytes("email"), Bytes.toBytes(people[i][3]));
        table.put(person);
    }
    // flush commits and close the table
    table.flushCommits();
    table.close();
    }
}
```

Den här koden är `CreateTable` klassen, som skapar en tabell med namnet `people` och fyller den med några fördefinierade användare.

### <a name="implement-a-searchbyemail-class"></a>Implementera en SearchByEmail-klass

Ange kommandot nedan för att skapa och öppna en ny fil `SearchByEmail.java` . Välj **Ja** vid prompten om du vill skapa en ny fil.

```cmd
notepad src\main\java\com\microsoft\examples\SearchByEmail.java
```

Kopiera och klistra sedan in Java-koden nedan i den nya filen. Stäng sedan filen.

```java
package com.microsoft.examples;
import java.io.IOException;

import org.apache.hadoop.conf.Configuration;
import org.apache.hadoop.hbase.HBaseConfiguration;
import org.apache.hadoop.hbase.client.HTable;
import org.apache.hadoop.hbase.client.Scan;
import org.apache.hadoop.hbase.client.ResultScanner;
import org.apache.hadoop.hbase.client.Result;
import org.apache.hadoop.hbase.filter.RegexStringComparator;
import org.apache.hadoop.hbase.filter.SingleColumnValueFilter;
import org.apache.hadoop.hbase.filter.CompareFilter.CompareOp;
import org.apache.hadoop.hbase.util.Bytes;
import org.apache.hadoop.util.GenericOptionsParser;

public class SearchByEmail {
    public static void main(String[] args) throws IOException {
    Configuration config = HBaseConfiguration.create();

    // Use GenericOptionsParser to get only the parameters to the class
    // and not all the parameters passed (when using WebHCat for example)
    String[] otherArgs = new GenericOptionsParser(config, args).getRemainingArgs();
    if (otherArgs.length != 1) {
        System.out.println("usage: [regular expression]");
        System.exit(-1);
    }

    // Open the table
    HTable table = new HTable(config, "people");

    // Define the family and qualifiers to be used
    byte[] contactFamily = Bytes.toBytes("contactinfo");
    byte[] emailQualifier = Bytes.toBytes("email");
    byte[] nameFamily = Bytes.toBytes("name");
    byte[] firstNameQualifier = Bytes.toBytes("first");
    byte[] lastNameQualifier = Bytes.toBytes("last");

    // Create a regex filter
    RegexStringComparator emailFilter = new RegexStringComparator(otherArgs[0]);
    // Attach the regex filter to a filter
    //   for the email column
    SingleColumnValueFilter filter = new SingleColumnValueFilter(
        contactFamily,
        emailQualifier,
        CompareOp.EQUAL,
        emailFilter
    );

    // Create a scan and set the filter
    Scan scan = new Scan();
    scan.setFilter(filter);

    // Get the results
    ResultScanner results = table.getScanner(scan);
    // Iterate over results and print  values
    for (Result result : results ) {
        String id = new String(result.getRow());
        byte[] firstNameObj = result.getValue(nameFamily, firstNameQualifier);
        String firstName = new String(firstNameObj);
        byte[] lastNameObj = result.getValue(nameFamily, lastNameQualifier);
        String lastName = new String(lastNameObj);
        System.out.println(firstName + " " + lastName + " - ID: " + id);
        byte[] emailObj = result.getValue(contactFamily, emailQualifier);
        String email = new String(emailObj);
        System.out.println(firstName + " " + lastName + " - " + email + " - ID: " + id);
    }
    results.close();
    table.close();
    }
}
```

`SearchByEmail`Klassen kan användas för att fråga efter rader per e-postadress. Eftersom det använder ett reguljärt uttrycks filter kan du antingen ange en sträng eller ett reguljärt uttryck när du använder-klassen.

### <a name="implement-a-deletetable-class"></a>Implementera en DeleteTable-klass

Ange kommandot nedan för att skapa och öppna en ny fil `DeleteTable.java` . Välj **Ja** vid prompten om du vill skapa en ny fil.

```cmd
notepad src\main\java\com\microsoft\examples\DeleteTable.java
```

Kopiera och klistra sedan in Java-koden nedan i den nya filen. Stäng sedan filen.

```java
package com.microsoft.examples;
import java.io.IOException;

import org.apache.hadoop.conf.Configuration;
import org.apache.hadoop.hbase.HBaseConfiguration;
import org.apache.hadoop.hbase.client.HBaseAdmin;

public class DeleteTable {
    public static void main(String[] args) throws IOException {
    Configuration config = HBaseConfiguration.create();

    // Create an admin object using the config
    HBaseAdmin admin = new HBaseAdmin(config);

    // Disable, and then delete the table
    admin.disableTable("people");
    admin.deleteTable("people");
    }
}
```

`DeleteTable`Klassen rensar HBase-tabellerna som skapats i det här exemplet genom att inaktivera och släppa den tabell som skapats av `CreateTable` klassen.

## <a name="build-and-package-the-application"></a>Bygg och paketera programmet

1. Från `hbaseapp` katalogen använder du följande kommando för att bygga en jar-fil som innehåller programmet:

    ```cmd
    mvn clean package
    ```

    Det här kommandot skapar och paketerar programmet till en. jar-fil.

2. När kommandot har slutförts `hbaseapp/target` innehåller katalogen en fil med namnet `hbaseapp-1.0-SNAPSHOT.jar` .

   > [!NOTE]  
   > `hbaseapp-1.0-SNAPSHOT.jar`Filen är en Uber jar. Den innehåller alla beroenden som krävs för att köra programmet.

## <a name="upload-the-jar-and-run-jobs-ssh"></a>Överför JAR-och körnings jobb (SSH)

Följande steg används `scp` för att kopiera jar till den primära huvudnoden i din Apache HBase i HDInsight-klustret. `ssh`Kommandot används sedan för att ansluta till klustret och köra exemplet direkt på Head-noden.

1. Överför jar till klustret. Ersätt `CLUSTERNAME` med ditt HDInsight-kluster namn och ange sedan följande kommando:

    ```cmd
    scp ./target/hbaseapp-1.0-SNAPSHOT.jar sshuser@CLUSTERNAME-ssh.azurehdinsight.net:hbaseapp-1.0-SNAPSHOT.jar
    ```

2. Anslut till HBase-klustret. Ersätt `CLUSTERNAME` med ditt HDInsight-kluster namn och ange sedan följande kommando:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

3. Om du vill skapa en HBase-tabell med Java-programmet använder du följande kommando i den öppna ssh-anslutningen:

    ```bash
    yarn jar hbaseapp-1.0-SNAPSHOT.jar com.microsoft.examples.CreateTable
    ```

    Det här kommandot skapar en HBase-tabell med namnet **människor** och fyller den med data.

4. Om du vill söka efter e-postadresser som lagras i tabellen använder du följande kommando:

    ```bash
    yarn jar hbaseapp-1.0-SNAPSHOT.jar com.microsoft.examples.SearchByEmail contoso.com
    ```

    Du får följande resultat:

    ```console
    Franklin Holtz - ID: 2
    Franklin Holtz - franklin@contoso.com - ID: 2
    Rae Schroeder - ID: 4
    Rae Schroeder - rae@contoso.com - ID: 4
    Gabriela Ingram - ID: 6
    Gabriela Ingram - gabriela@contoso.com - ID: 6
    ```

5. Om du vill ta bort tabellen använder du följande kommando:

    ```bash
    yarn jar hbaseapp-1.0-SNAPSHOT.jar com.microsoft.examples.DeleteTable
    ```

## <a name="upload-the-jar-and-run-jobs-powershell"></a>Överför JAR-och körnings jobben (PowerShell)

I följande steg används modulen Azure PowerShell [AZ](/powershell/azure/new-azureps-module-az) för att överföra jar till standard lagringen för ditt Apache HBase-kluster. HDInsight-cmdletar används sedan för att köra exemplen på distans.

1. När du har installerat och konfigurerat AZ-modulen skapar du en fil med namnet `hbase-runner.psm1` . Använd följande text som filens innehåll:

   ```powershell
    <#
    .SYNOPSIS
    Copies a file to the primary storage of an HDInsight cluster.
    .DESCRIPTION
    Copies a file from a local directory to the blob container for
    the HDInsight cluster.
    .EXAMPLE
    Start-HBaseExample -className "com.microsoft.examples.CreateTable"
    -clusterName "MyHDInsightCluster"

    .EXAMPLE
    Start-HBaseExample -className "com.microsoft.examples.SearchByEmail"
    -clusterName "MyHDInsightCluster"
    -emailRegex "contoso.com"

    .EXAMPLE
    Start-HBaseExample -className "com.microsoft.examples.SearchByEmail"
    -clusterName "MyHDInsightCluster"
    -emailRegex "^r" -showErr
    #>

    function Start-HBaseExample {
    [CmdletBinding(SupportsShouldProcess = $true)]
    param(
    #The class to run
    [Parameter(Mandatory = $true)]
    [String]$className,

    #The name of the HDInsight cluster
    [Parameter(Mandatory = $true)]
    [String]$clusterName,

    #Only used when using SearchByEmail
    [Parameter(Mandatory = $false)]
    [String]$emailRegex,

    #Use if you want to see stderr output
    [Parameter(Mandatory = $false)]
    [Switch]$showErr
    )

    Set-StrictMode -Version 3

    # Is the Azure module installed?
    FindAzure

    # Get the login for the HDInsight cluster
    $creds=Get-Credential -Message "Enter the login for the cluster" -UserName "admin"

    # The JAR
    $jarFile = "wasb:///example/jars/hbaseapp-1.0-SNAPSHOT.jar"

    # The job definition
    $jobDefinition = New-AzHDInsightMapReduceJobDefinition `
        -JarFile $jarFile `
        -ClassName $className `
        -Arguments $emailRegex

    # Get the job output
    $job = Start-AzHDInsightJob `
        -ClusterName $clusterName `
        -JobDefinition $jobDefinition `
        -HttpCredential $creds
    Write-Host "Wait for the job to complete ..." -ForegroundColor Green
    Wait-AzHDInsightJob `
        -ClusterName $clusterName `
        -JobId $job.JobId `
        -HttpCredential $creds
    if($showErr)
    {
    Write-Host "STDERR"
    Get-AzHDInsightJobOutput `
                -Clustername $clusterName `
                -JobId $job.JobId `
                -HttpCredential $creds `
                -DisplayOutputType StandardError
    }
    Write-Host "Display the standard output ..." -ForegroundColor Green
    Get-AzHDInsightJobOutput `
                -Clustername $clusterName `
                -JobId $job.JobId `
                -HttpCredential $creds
    }

    <#
    .SYNOPSIS
    Copies a file to the primary storage of an HDInsight cluster.
    .DESCRIPTION
    Copies a file from a local directory to the blob container for
    the HDInsight cluster.
    .EXAMPLE
    Add-HDInsightFile -localPath "C:\temp\data.txt"
    -destinationPath "example/data/data.txt"
    -ClusterName "MyHDInsightCluster"
    .EXAMPLE
    Add-HDInsightFile -localPath "C:\temp\data.txt"
    -destinationPath "example/data/data.txt"
    -ClusterName "MyHDInsightCluster"
    -Container "MyContainer"
    #>

    function Add-HDInsightFile {
        [CmdletBinding(SupportsShouldProcess = $true)]
        param(
            #The path to the local file.
            [Parameter(Mandatory = $true)]
            [String]$localPath,

            #The destination path and file name, relative to the root of the container.
            [Parameter(Mandatory = $true)]
            [String]$destinationPath,

            #The name of the HDInsight cluster
            [Parameter(Mandatory = $true)]
            [String]$clusterName,

            #If specified, overwrites existing files without prompting
            [Parameter(Mandatory = $false)]
            [Switch]$force
        )

        Set-StrictMode -Version 3

        # Is the Azure module installed?
        FindAzure

        # Get authentication for the cluster
        $creds=Get-Credential

        # Does the local path exist?
        if (-not (Test-Path $localPath))
        {
            throw "Source path '$localPath' does not exist."
        }

        # Get the primary storage container
        $storage = GetStorage -clusterName $clusterName

        # Upload file to storage, overwriting existing files if -force was used.
        Set-AzStorageBlobContent -File $localPath `
            -Blob $destinationPath `
            -force:$force `
            -Container $storage.container `
            -Context $storage.context
    }

    function FindAzure {
        # Is there an active Azure subscription?
        $sub = Get-AzSubscription -ErrorAction SilentlyContinue
        if(-not($sub))
        {
            Connect-AzAccount
        }
    }

    function GetStorage {
        param(
            [Parameter(Mandatory = $true)]
            [String]$clusterName
        )
        $hdi = Get-AzHDInsightCluster -ClusterName $clusterName
        # Does the cluster exist?
        if (!$hdi)
        {
            throw "HDInsight cluster '$clusterName' does not exist."
        }
        # Create a return object for context & container
        $return = @{}
        $storageAccounts = @{}

        # Get storage information
        $resourceGroup = $hdi.ResourceGroup
        $storageAccountName=$hdi.DefaultStorageAccount.split('.')[0]
        $container=$hdi.DefaultStorageContainer
        $storageAccountKey=(Get-AzStorageAccountKey `
            -Name $storageAccountName `
        -ResourceGroupName $resourceGroup)[0].Value
        # Get the resource group, in case we need that
        $return.resourceGroup = $resourceGroup
        # Get the storage context, as we can't depend
        # on using the default storage context
        $return.context = New-AzStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey
        # Get the container, so we know where to
        # find/store blobs
        $return.container = $container
        # Return storage accounts to support finding all accounts for
        # a cluster
        $return.storageAccount = $storageAccountName
        $return.storageAccountKey = $storageAccountKey

        return $return
    }
    # Only export the verb-phrase things
    export-modulemember *-*
   ```

    Den här filen innehåller två moduler:

   * **Add-HDInsightFile** – används för att ladda upp filer till klustret
   * **Start-HBaseExample** – används för att köra de klasser som skapades tidigare

2. Spara `hbase-runner.psm1` filen i `hbaseapp` katalogen.

3. Registrera modulerna med Azure PowerShell. Öppna ett nytt Azure PowerShell-fönster och redigera kommandot nedan genom `CLUSTERNAME` att ersätta med namnet på klustret. Ange sedan följande kommandon:

    ```powershell
    cd C:\HDI\hbaseapp
    $myCluster = "CLUSTERNAME"
    Import-Module .\hbase-runner.psm1
    ```

4. Använd följande kommando för att överföra `hbaseapp-1.0-SNAPSHOT.jar` till klustret.

    ```powershell
    Add-HDInsightFile -localPath target\hbaseapp-1.0-SNAPSHOT.jar -destinationPath example/jars/hbaseapp-1.0-SNAPSHOT.jar -clusterName $myCluster
    ```

    När du uppmanas till det anger du namn och lösen ord för kluster inloggningen (admin). Kommandot överför `hbaseapp-1.0-SNAPSHOT.jar` till `example/jars` platsen i den primära lagringen för klustret.

5. Använd följande kommando för att skapa en tabell med `hbaseapp` :

    ```powershell
    Start-HBaseExample -className com.microsoft.examples.CreateTable -clusterName $myCluster
    ```

    När du uppmanas till det anger du namn och lösen ord för kluster inloggningen (admin).

    Det här kommandot skapar en tabell med namnet **personer** i HBase i ditt HDInsight-kluster. Det här kommandot visar inga utdata i konsol fönstret.

6. Om du vill söka efter poster i tabellen använder du följande kommando:

    ```powershell
    Start-HBaseExample -className com.microsoft.examples.SearchByEmail -clusterName $myCluster -emailRegex contoso.com
    ```

    När du uppmanas till det anger du namn och lösen ord för kluster inloggningen (admin).

    Det här kommandot använder `SearchByEmail` klassen för att söka efter rader där `contactinformation` kolumn familjen och `email` kolumnen innehåller strängen `contoso.com` . Du bör få följande resultat:

    ```output
    Franklin Holtz - ID: 2
    Franklin Holtz - franklin@contoso.com - ID: 2
    Rae Schroeder - ID: 4
    Rae Schroeder - rae@contoso.com - ID: 4
    Gabriela Ingram - ID: 6
    Gabriela Ingram - gabriela@contoso.com - ID: 6
    ```

    Om du använder **fabrikam.com** för `-emailRegex` värdet returneras de användare som har **fabrikam.com** i fältet e-post. Du kan också använda reguljära uttryck som Sök villkor. **^ R** returnerar till exempel e-postadresser som börjar med bokstaven r.

7. Om du vill ta bort tabellen använder du följande kommando:

    ```PowerShell
    Start-HBaseExample -className com.microsoft.examples.DeleteTable -clusterName $myCluster
    ```

### <a name="no-results-or-unexpected-results-when-using-start-hbaseexample"></a>Inga resultat eller oväntade resultat när du använder Start-HBaseExample

Använd `-showErr` parametern för att Visa standard felet (STDERR) som skapas när jobbet körs.

## <a name="next-steps"></a>Nästa steg

[Lär dig hur du använder SQLLine med Apache HBase](apache-hbase-query-with-phoenix.md)