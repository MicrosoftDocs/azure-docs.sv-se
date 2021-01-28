---
title: Köra apache Sqoop-jobb med Azure HDInsight (Apache Hadoop)
description: Lär dig hur du använder Azure PowerShell från en arbets station för att köra Sqoop import och export mellan ett Hadoop-kluster och en Azure SQL-databas.
ms.service: hdinsight
ms.topic: how-to
ms.date: 12/06/2019
ms.openlocfilehash: 1c34b673cd970a9e7577b7ff01d27eb0e4cc1ac1
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/28/2021
ms.locfileid: "98946431"
---
# <a name="use-apache-sqoop-with-hadoop-in-hdinsight"></a>Använda Apache Sqoop med Hadoop i HDInsight

[!INCLUDE [sqoop-selector](../../../includes/hdinsight-selector-use-sqoop.md)]

Lär dig hur du använder Apache Sqoop i HDInsight för att importera och exportera data mellan ett HDInsight-kluster och Azure SQL Database.

Även om Apache Hadoop är ett naturligt val för bearbetning av ostrukturerade och delvis strukturerade data, t. ex. loggar och filer, kan det också finnas behov av att bearbeta strukturerade data som lagras i Relations databaser.

[Apache Sqoop](https://sqoop.apache.org/docs/1.99.7/user.html) är ett verktyg som har utformats för att överföra data mellan Hadoop-kluster och Relations databaser. Du kan använda den för att importera data från ett relationellt databas hanterings system (RDBMS) som SQL Server, MySQL eller Oracle till Hadoop Distributed File System (HDFS), transformera data i Hadoop med MapReduce eller Apache Hive och sedan exportera data tillbaka till en RDBMS. I den här artikeln använder du Azure SQL Database för Relations databasen.

> [!IMPORTANT]  
> Den här artikeln skapar en test miljö för att utföra data överföringen. Sedan väljer du en data överförings metod för den här miljön från någon av metoderna i avsnittet [Kör Sqoop-jobb](#run-sqoop-jobs), nedan.

För Sqoop-versioner som stöds i HDInsight-kluster, se [Vad är nytt i de kluster versioner som tillhandahålls av HDInsight?](../hdinsight-component-versioning.md)

## <a name="understand-the-scenario"></a>Förstå scenariot

HDInsight-kluster innehåller vissa exempel data. Du använder följande två exempel:

* En Apache log4j-loggfil som finns på `/example/data/sample.log` . Följande loggar extraheras från filen:

```text
2012-02-03 18:35:34 SampleClass6 [INFO] everything normal for id 577725851
2012-02-03 18:35:34 SampleClass4 [FATAL] system problem at id 1991281254
2012-02-03 18:35:34 SampleClass3 [DEBUG] detail for id 1304807656
...
```

* En Hive-tabell med namnet `hivesampletable` som refererar till den data fil som finns på `/hive/warehouse/hivesampletable` . Tabellen innehåller vissa mobila enhets data.
  
  | Fält | Datatyp |
  | --- | --- |
  | clientid |sträng |
  | querytime |sträng |
  | telefonförsäljning |sträng |
  | deviceplatform |sträng |
  | devicemake |sträng |
  | devicemodel |sträng |
  | state |sträng |
  | land |sträng |
  | querydwelltime |double |
  | SessionID |bigint |
  | sessionpagevieworder |bigint |

I den här artikeln använder du dessa två data uppsättningar för att testa Sqoop import och export.

## <a name="set-up-test-environment"></a><a name="create-cluster-and-sql-database"></a>Konfigurera test miljö

Klustret, SQL-databasen och andra objekt skapas via Azure Portal med hjälp av en Azure Resource Manager mall. Mallen finns i [snabb starts mallar för Azure](https://azure.microsoft.com/resources/templates/101-hdinsight-linux-with-sql-database/). Resource Manager-mallen anropar ett BACPAC-paket för att distribuera tabell scheman till en SQL-databas.  BACPAC-paketet finns i en offentlig BLOB-behållare https://hditutorialdata.blob.core.windows.net/usesqoop/SqoopTutorial-2016-2-23-11-2.bacpac . Om du vill använda en privat behållare för BACPAC-filerna använder du följande värden i mallen:

```json
"storageKeyType": "Primary",
"storageKey": "<TheAzureStorageAccountKey>",
```

> [!NOTE]  
> Importera med hjälp av en mall eller Azure Portal har endast stöd för att importera en BACPAC-fil från Azure Blob Storage.

1. Välj följande bild för att öppna Resource Manager-mallen i Azure Portal.

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-linux-with-sql-database%2Fazuredeploy.json" target="_blank"><img src="./media/hdinsight-use-sqoop/hdi-deploy-to-azure1.png" alt="Deploy to Azure button for new cluster"></a>

2. Ange följande egenskaper:

    |Fält |Värde |
    |---|---|
    |Prenumeration |Välj din Azure-prenumeration i list rutan.|
    |Resursgrupp |Välj din resurs grupp i den nedrullningsbara listan eller skapa en ny|
    |Location |Välj en region i den nedrullningsbara listan.|
    |Klusternamn |Ange ett namn för Hadoop-klustret. Använd bara gemena bokstäver.|
    |Användarnamn för klusterinloggning |Behåll värdet i förväg `admin` .|
    |Lösenord för klusterinloggning |Ange ett lösen ord.|
    |Användar namn för SSH |Behåll värdet i förväg `sshuser` .|
    |SSH-lösenord |Ange ett lösen ord.|
    |SQL admin-inloggning |Behåll värdet i förväg `sqluser` .|
    |SQL admin-lösenord |Ange ett lösen ord.|
    |_artifacts plats | Använd standardvärdet om du inte vill använda din egen BACPAC-fil på en annan plats.|
    |SAS-token för _artifacts plats |Lämna tomt.|
    |BACPAC fil namn |Använd standardvärdet om du inte vill använda din egen BACPAC-fil.|
    |Location |Använd standardvärdet.|

    Namnet på den [logiska SQL-servern](../../azure-sql/database/logical-servers.md) är  `<ClusterName>dbserver` . Databas namnet kommer att vara `<ClusterName>db` . Standard namnet för lagrings kontot är `e6qhezrh2pdqu` .

3. Välj **Jag accepterar de villkor som anges ovan**.

4. Välj **Köp**. Du ser en ny panel med rubriken skicka distribution för Malldistribution. Det tar cirka 20 minuter att skapa klustret och SQL Database.

## <a name="run-sqoop-jobs"></a>Kör Sqoop-jobb

HDInsight kan köra Sqoop-jobb genom att använda en mängd olika metoder. Använd följande tabell för att bestämma vilken metod som passar dig bäst. Följ sedan länken för en genom gång.

| **Använd det här alternativet** om du vill... | ... ett **interaktivt** gränssnitt | ... **Batchbearbetning** | ... från det här **klient operativ systemet** |
|:--- |:---:|:---:|:--- |:--- |
| [SSH](apache-hadoop-use-sqoop-mac-linux.md) |? |? |Linux, UNIX, Mac OS X eller Windows |
| [.NET SDK för Hadoop](apache-hadoop-use-sqoop-dotnet-sdk.md) |&nbsp; |?  |Windows (för tillfället) |
| [Azure PowerShell](apache-hadoop-use-sqoop-powershell.md) |&nbsp; |? |Windows |

## <a name="limitations"></a>Begränsningar

* Mass export – med Linux-baserat HDInsight har Sqoop-anslutningen som används för att exportera data till Microsoft SQL Server eller SQL Database för närvarande inte stöd för Mass infogningar.
* Batchering – med Linux-baserat HDInsight, när du använder `-batch` växeln när du utför infogningar, utför Sqoop flera infogningar i stället för att batch-sätta in åtgärderna.

## <a name="next-steps"></a>Nästa steg

Nu har du lärt dig hur du använder Sqoop. Mer information finns i:

* [Använda Apache Hive med HDInsight](./hdinsight-use-hive.md)
* [Ladda upp data till HDInsight](../hdinsight-upload-data.md): hitta andra metoder för att ladda upp data till HDInsight/Azure Blob Storage.
* [Använda Apache Sqoop för att importera och exportera data mellan Apache Hadoop på HDInsight och SQL Database](./apache-hadoop-use-sqoop-mac-linux.md)