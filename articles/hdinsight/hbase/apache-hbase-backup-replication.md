---
title: Säkerhetskopiera & replikering för Apache HBase, Phoenix – Azure HDInsight
description: Konfigurera säkerhets kopiering och replikering för Apache HBase och Apache Phoenix i Azure HDInsight
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 12/19/2019
ms.openlocfilehash: 1d5bcf9c04ad02eaf297f8971aa0f4ff599888c7
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/28/2021
ms.locfileid: "98942997"
---
# <a name="set-up-backup-and-replication-for-apache-hbase-and-apache-phoenix-on-hdinsight"></a>Konfigurera säkerhets kopiering och replikering för Apache HBase och Apache Phoenix på HDInsight

Apache HBase stöder flera metoder för att skydda mot data förlust:

* Kopiera `hbase` mappen
* Exportera och importera sedan
* Kopiera tabeller
* Ögonblicksbilder
* Replikering

> [!NOTE]  
> Apache Phoenix lagrar dess metadata i HBase-tabeller, så att metadata säkerhets kopie ras när du säkerhetskopierar tabellerna i system katalogen för HBase.

I följande avsnitt beskrivs användnings scenariot för var och en av dessa metoder.

## <a name="copy-the-hbase-folder"></a>Kopiera mappen HBase

Med den här metoden kopierar du alla HBase-data, utan att kunna välja en delmängd av tabeller eller kolumn familjer. Efterföljande metoder ger bättre kontroll.

HBase i HDInsight använder standard lagringen som valts när klustret skapas, antingen Azure Storage blobbar eller Azure Data Lake Storage. I båda fallen lagrar HBase data och metadatafiler under följande sökväg:

`/hbase`

* I ett Azure Storage-konto `hbase` finns mappen i roten i BLOB-behållaren:

  `wasbs://<containername>@<accountname>.blob.core.windows.net/hbase`

* I Azure Data Lake Storage `hbase` finns mappen under den rot Sök väg som du angav när du etablerade ett kluster. Den här rot Sök vägen har vanligt vis en `clusters` mapp med en undermapp som heter efter ditt HDInsight-kluster:

  `/clusters/<clusterName>/hbase`

I båda fallen `hbase` innehåller mappen alla data som HBase har rensat till disk, men den kan inte innehålla InMemory-data. Innan du kan förlita dig på den här mappen som en korrekt representation av HBase-data måste du stänga av klustret.

När du har tagit bort klustret kan du antingen lämna data på plats eller kopiera data till en ny plats:

* Skapa en ny HDInsight-instans som pekar på den aktuella lagrings platsen. Den nya instansen skapas med alla befintliga data.

* Kopiera `hbase` mappen till en annan Azure Storage BLOB-behållare eller data Lake Storage plats och starta sedan ett nytt kluster med dessa data. För Azure Storage använder du [AzCopy](../../storage/common/storage-use-azcopy-v10.md)och för data Lake Storage Använd [AdlCopy](../../data-lake-store/data-lake-store-copy-data-azure-storage-blob.md).

## <a name="export-then-import"></a>Exportera och importera sedan

Använd [export verktyget](https://hbase.apache.org/book.html#export) (ingår i HBase) i käll HDInsight-klustret för att exportera data från en käll tabell till standard lagrings utrymmet. Du kan sedan kopiera den exporterade mappen till mål lagrings platsen och köra [import verktyget](https://hbase.apache.org/book.html#import) på målet HDInsight-kluster.

Om du vill exportera tabell data måste du först använda SSH i head-noden i ditt HDInsight-käll-kluster och sedan köra följande `hbase` kommando:

```console
hbase org.apache.hadoop.hbase.mapreduce.Export "<tableName>" "/<path>/<to>/<export>"
```

Export katalogen får inte redan finnas. Tabell namnet är Skift läges känsligt.

Importera tabell data genom att använda SSH i noden Head i HDInsight-målet och kör sedan följande `hbase` kommando:

```console
hbase org.apache.hadoop.hbase.mapreduce.Import "<tableName>" "/<path>/<to>/<export>"
```

Tabellen måste redan finnas.

Ange den fullständiga export Sök vägen till standard lagrings utrymmet eller till något av de bifogade lagrings alternativen. I Azure Storage till exempel:

`wasbs://<containername>@<accountname>.blob.core.windows.net/<path>`

I Azure Data Lake Storage Gen2 är syntaxen:

`abfs://<containername>@<accountname>.dfs.core.windows.net/<path>`

I Azure Data Lake Storage Gen1 är syntaxen:

`adl://<accountName>.azuredatalakestore.net:443/<path>`

Den här metoden erbjuder granularitet på tabell nivå. Du kan också ange ett datum intervall för de rader som ska tas med, vilket gör att du kan utföra processen stegvis. Varje datum är i millisekunder sedan UNIX-epoken.

```console
hbase org.apache.hadoop.hbase.mapreduce.Export "<tableName>" "/<path>/<to>/<export>" <numberOfVersions> <startTimeInMS> <endTimeInMS>
```

Observera att du måste ange antalet versioner av varje rad som ska exporteras. Om du vill inkludera alla versioner i datum intervallet anger `<numberOfVersions>` du ett värde som är större än maximalt antal möjliga rad versioner, till exempel 100000.

## <a name="copy-tables"></a>Kopiera tabeller

[Verktyget CopyTable](https://hbase.apache.org/book.html#copy.table) kopierar data från en käll tabell, rad för rad, till en befintlig mål tabell med samma schema som källan. Mål tabellen kan finnas i samma kluster eller ett annat HBase-kluster. Tabell namnen är Skift läges känsliga.

Om du vill använda CopyTable i ett kluster kan du använda SSH i head-noden i ditt HDInsight-kluster och sedan köra det här `hbase` kommandot:

```console
hbase org.apache.hadoop.hbase.mapreduce.CopyTable --new.name=<destTableName> <srcTableName>
```

Om du vill använda CopyTable för att kopiera till en tabell i ett annat kluster lägger du till `peer` växeln med mål klustrets adress:

```console
hbase org.apache.hadoop.hbase.mapreduce.CopyTable --new.name=<destTableName> --peer.adr=<destinationAddress> <srcTableName>
```

Mål adressen består av följande tre delar:

`<destinationAddress> = <ZooKeeperQuorum>:<Port>:<ZnodeParent>`

* `<ZooKeeperQuorum>` är en kommaavgränsad lista med Apache ZooKeeper noder, till exempel:

    zk0-hdizc 2.54 o2oqawzlwevlfxgay2500xtg. DX. Internal. cloudapp. net, zk4-hdizc 2.54 o2oqawzlwevlfxgay2500xtg. DX. Internal. cloudapp. net, zk3-hdizc2.54o2oqawzlwevlfxgay2500xtg.dx.internal.cloudapp.net

* `<Port>` i HDInsight är standardvärdet 2181 och `<ZnodeParent>` är `/hbase-unsecure` det fullständiga `<destinationAddress>` :

    zk0-hdizc 2.54 o2oqawzlwevlfxgay2500xtg. DX. Internal. cloudapp. net, zk4-hdizc 2.54 o2oqawzlwevlfxgay2500xtg. DX. Internal. cloudapp. net, zk3-hdizc 2.54 o2oqawzlwevlfxgay2500xtg. DX. Internal. cloudapp. net: 2181:/HBase-unsecure

Mer information om hur du hämtar dessa värden för ditt HDInsight-kluster finns i [samla in Apache ZooKeeper-kvorumloggen manuellt](#manually-collect-the-apache-zookeeper-quorum-list) i den här artikeln.

Verktyget CopyTable stöder också parametrar för att ange tidsintervallet för rader som ska kopieras och för att ange delmängd av kolumn familjer i en tabell som ska kopieras. Om du vill se en fullständig lista över parametrar som stöds av CopyTable kör du CopyTable utan parametrar:

```console
hbase org.apache.hadoop.hbase.mapreduce.CopyTable
```

CopyTable genomsöker hela käll tabellens innehåll som ska kopieras till mål tabellen. Detta kan minska HBase-klustrets prestanda medan CopyTable körs.

> [!NOTE]  
> Information om hur du automatiserar kopieringen av data mellan tabeller finns i `hdi_copy_table.sh` skriptet i [Azure HBase utils](https://github.com/Azure/hbase-utils/tree/master/replication) -lagringsplatsen på GitHub.

### <a name="manually-collect-the-apache-zookeeper-quorum-list"></a>Samla in listan Apache ZooKeeper-kvorum manuellt

När båda HDInsight-klustren finns i samma virtuella nätverk, så som tidigare beskrivits, är den interna värd namns matchningen automatisk. Om du vill använda CopyTable för HDInsight-kluster i två separata virtuella nätverk som är anslutna av en VPN Gateway, måste du ange värd-IP-adresserna för Zookeeper-noderna i kvorumet.

Kör följande spiral kommando för att hämta namnen på kvorumresurser:

```console
curl -u admin:<password> -X GET -H "X-Requested-By: ambari" "https://<clusterName>.azurehdinsight.net/api/v1/clusters/<clusterName>/configurations?type=hbase-site&tag=TOPOLOGY_RESOLVED" | grep "hbase.zookeeper.quorum"
```

Kommandot spiral hämtar ett JSON-dokument med konfigurations information för HBase och grep-kommandot returnerar bara posten "HBase. Zookeeper. kvorum", till exempel:

```output
"hbase.zookeeper.quorum" : "zk0-hdizc2.54o2oqawzlwevlfxgay2500xtg.dx.internal.cloudapp.net,zk4-hdizc2.54o2oqawzlwevlfxgay2500xtg.dx.internal.cloudapp.net,zk3-hdizc2.54o2oqawzlwevlfxgay2500xtg.dx.internal.cloudapp.net"
```

Värdet för kvorumloggen är hela strängen till höger om kolon.

Om du vill hämta IP-adresserna för dessa värdar använder du följande spiral-kommando för varje värd i föregående lista:

```console
curl -u admin:<password> -X GET -H "X-Requested-By: ambari" "https://<clusterName>.azurehdinsight.net/api/v1/clusters/<clusterName>/hosts/<zookeeperHostFullName>" | grep "ip"
```

I det här spiral kommandot `<zookeeperHostFullName>` är det fullständiga DNS-namnet för en ZooKeeper-värd, t `zk0-hdizc2.54o2oqawzlwevlfxgay2500xtg.dx.internal.cloudapp.net` . ex. exempel. Kommandots utdata innehåller IP-adressen för den angivna värden, till exempel:

`100    "ip" : "10.0.0.9",`

När du har samlat in IP-adresserna för alla ZooKeeper-noder i kvorumet, återskapa mål adressen:

`<destinationAddress>  = <Host_1_IP>,<Host_2_IP>,<Host_3_IP>:<Port>:<ZnodeParent>`

I vårt exempel:

`<destinationAddress> = 10.0.0.9,10.0.0.8,10.0.0.12:2181:/hbase-unsecure`

## <a name="snapshots"></a>Ögonblicksbilder

Med [ögonblicks bilder](https://hbase.apache.org/book.html#ops.snapshots) kan du ta en tidpunkts säkerhets kopia av data i ditt HBase-datalager. Ögonblicks bilder har minimala kostnader och har slutförts inom några sekunder, eftersom en ögonblicks bild åtgärd på ett effektivt sätt fångar in namnen på alla filer i lagret. När en ögonblicks bild skapas kopieras inga faktiska data. Ögonblicks bilder är beroende av den oföränderliga typen av data som lagras i HDFS, där uppdateringar, rader och infogningar visas som nya data. Du kan återställa (*klona*) en ögonblicks bild i samma kluster eller exportera en ögonblicks bild till ett annat kluster.

För att skapa en ögonblicks bild, SSH i till Head-noden i ditt HDInsight HBase-kluster och starta `hbase` gränssnittet:

```console
hbase shell
```

I HBase-gränssnittet använder du ögonblicks bild kommandot med namnen på tabellen och i den här ögonblicks bilden:

```console
snapshot '<tableName>', '<snapshotName>'
```

Om du vill återställa en ögonblicks bild efter namn i `hbase` gränssnittet inaktiverar du först tabellen och återställer sedan ögonblicks bilden och aktiverar tabellen igen:

```console
disable '<tableName>'
restore_snapshot '<snapshotName>'
enable '<tableName>'
```

Om du vill återställa en ögonblicks bild till en ny tabell använder du clone_snapshot:

```console
clone_snapshot '<snapshotName>', '<newTableName>'
```

Om du vill exportera en ögonblicks bild till HDFS för användning av ett annat kluster måste du först skapa ögonblicks bilden enligt beskrivningen ovan och sedan använda verktyget ExportSnapshot. Kör det här verktyget inifrån SSH-sessionen till Head-noden, inte inom `hbase` gränssnittet:

```console
hbase org.apache.hadoop.hbase.snapshot.ExportSnapshot -snapshot <snapshotName> -copy-to <hdfsHBaseLocation>
```

`<hdfsHBaseLocation>`Kan vara vilken som helst av de lagrings platser som är tillgängliga för ditt käll kluster och ska peka på HBase-mappen som används av ditt mål kluster. Om du till exempel har ett sekundärt Azure Storage-konto som är kopplat till ditt käll kluster och det kontot ger åtkomst till den behållare som används av standard lagringen för mål klustret, kan du använda det här kommandot:

```console
hbase org.apache.hadoop.hbase.snapshot.ExportSnapshot -snapshot 'Snapshot1' -copy-to 'wasbs://secondcluster@myaccount.blob.core.windows.net/hbase'
```

Om du inte har ett sekundärt Azure Storage-konto som är kopplat till ditt käll kluster eller om ditt käll kluster är ett lokalt kluster (eller icke-HDI kluster) kan du uppleva auktoriseringsfel när du försöker komma åt lagrings kontot för ditt HDI-kluster. Lös detta genom att ange nyckeln till ditt lagrings konto som en kommando rads parameter som visas i följande exempel. Du kan hämta nyckeln till ditt lagrings konto i Azure Portal.

```console
hbase org.apache.hadoop.hbase.snapshot.ExportSnapshot -Dfs.azure.account.key.myaccount.blob.core.windows.net=mykey -snapshot 'Snapshot1' -copy-to 'wasbs://secondcluster@myaccount.blob.core.windows.net/hbase'
```

Om mål klustret är ett ADLS gen 2-kluster ändrar du det föregående kommandot för att justera för de konfigurationer som används av ADLS gen 2:

```console
hbase org.apache.hadoop.hbase.snapshot.ExportSnapshot -Dfs.azure.account.key.<account_name>.dfs.core.windows.net=<key> -Dfs.azure.account.auth.type.<account_name>.dfs.core.windows.net=SharedKey -Dfs.azure.always.use.https.<account_name>.dfs.core.windows.net=false -Dfs.azure.account.keyprovider.<account_name>.dfs.core.windows.net=org.apache.hadoop.fs.azurebfs.services.SimpleKeyProvider -snapshot 'Snapshot1' -copy-to 'abfs://<container>@<account_name>.dfs.core.windows.net/hbase'
```

När ögonblicks bilden har exporter ATS kan du använda SSH i head-noden i mål klustret och återställa ögonblicks bilden med hjälp av `restore_snapshot` kommandot enligt beskrivningen ovan.

Ögonblicks bilder ger en fullständig säkerhets kopia av en tabell vid `snapshot` kommandots tidpunkt. Ögonblicks bilder ger inte möjlighet att utföra stegvisa ögonblicks bilder i Windows eller för att ange del mängder av kolumn familjer som ska tas med i ögonblicks bilden.

## <a name="replication"></a>Replikering

[HBase-replikering](https://hbase.apache.org/book.html#_cluster_replication) skickar automatiskt transaktioner från ett käll kluster till ett mål kluster med hjälp av en asynkron mekanism med minimal belastning på käll klustret. I HDInsight kan du konfigurera replikering mellan kluster där:

* Käll-och mål kluster finns i samma virtuella nätverk.
* Käll-och mål kluster finns i olika virtuella nätverk som är anslutna via en VPN-gateway, men båda klustren finns på samma geografiska plats.
* Kluster för käll kluster och mål grupper finns i olika virtuella nätverk som är anslutna via en VPN-gateway och varje kluster finns på en annan geografisk plats.

De allmänna stegen för att konfigurera replikering är:

1. Skapa tabellerna och fyll i data i käll klustret.
2. Skapa tomma mål tabeller med käll tabellens schema i mål klustret.
3. Registrera mål klustret som en peer till käll klustret.
4. Aktivera replikering för önskade käll tabeller.
5. Kopiera befintliga data från käll tabellerna till mål tabellerna.
6. Replikeringen kopierar automatiskt nya data ändringar till käll tabellerna till mål tabellerna.

Om du vill aktivera replikering på HDInsight använder du en skript åtgärd för att köra käll HDInsight-klustret. En genom gång av hur du aktiverar replikering i klustret eller experimenterar med replikering på exempel kluster som skapats i virtuella nätverk med hjälp av Azure Resource Management-mallar finns i [Konfigurera HBase-replikering i Apache](apache-hbase-replication.md). Artikeln innehåller också instruktioner för att aktivera replikering av Phoenix-metadata.

## <a name="next-steps"></a>Nästa steg

* [Konfigurera Apache HBase-replikering](apache-hbase-replication.md)
* [Arbeta med verktyget för import och export av HBase](/archive/blogs/data_otaku/working-with-the-hbase-import-and-export-utility)
