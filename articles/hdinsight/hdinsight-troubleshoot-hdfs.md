---
title: Felsöka HDFS i Azure HDInsight
description: Få svar på vanliga frågor om att arbeta med HDFS och Azure HDInsight.
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 04/27/2020
ms.custom: seodec18
ms.openlocfilehash: 0be7805493e5acc41254c57ca912b5a2ecf02dae
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/28/2021
ms.locfileid: "98931460"
---
# <a name="troubleshoot-apache-hadoop-hdfs-by-using-azure-hdinsight"></a>Felsöka Apache Hadoop HDFS med Azure HDInsight

Lär dig de vanligaste problemen och lösningarna när du arbetar med Hadoop Distributed File System (HDFS). En fullständig lista över kommandon finns i [kommando](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HDFSCommands.html) [tolken HDFS och fil systemets gränssnitts guide](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-common/FileSystemShell.html).

## <a name="how-do-i-access-the-local-hdfs-from-inside-a-cluster"></a><a name="how-do-i-access-local-hdfs-from-inside-a-cluster"></a>Hur gör jag för att åtkomst till den lokala HDFS inifrån ett kluster?

### <a name="issue"></a>Problem

Få åtkomst till den lokala HDFS från kommando raden och program koden i stället för att använda Azure Blob Storage eller Azure Data Lake Storage inifrån HDInsight-klustret.

### <a name="resolution-steps"></a>Lösningssteg

1. I kommando tolken använder du `hdfs dfs -D "fs.default.name=hdfs://mycluster/" ...` bokstavligen, som i följande kommando:

    ```output
    hdfs dfs -D "fs.default.name=hdfs://mycluster/" -ls /
    Found 3 items
    drwxr-xr-x   - hdiuser hdfs          0 2017-03-24 14:12 /EventCheckpoint-30-8-24-11102016-01
    drwx-wx-wx   - hive    hdfs          0 2016-11-10 18:42 /tmp
    drwx------   - hdiuser hdfs          0 2016-11-10 22:22 /user
    ```

2. Från käll koden använder du URI `hdfs://mycluster/` : n, som i följande exempel program:

    ```Java
    import java.io.IOException;
    import java.net.URI;
    import org.apache.commons.io.IOUtils;
    import org.apache.hadoop.conf.Configuration;
    import org.apache.hadoop.fs.*;

    public class JavaUnitTests {

        public static void main(String[] args) throws Exception {

            Configuration conf = new Configuration();
            String hdfsUri = "hdfs://mycluster/";
            conf.set("fs.defaultFS", hdfsUri);
            FileSystem fileSystem = FileSystem.get(URI.create(hdfsUri), conf);
            RemoteIterator<LocatedFileStatus> fileStatusIterator = fileSystem.listFiles(new Path("/tmp"), true);
            while(fileStatusIterator.hasNext()) {
                System.out.println(fileStatusIterator.next().getPath().toString());
            }
        }
    }
    ```

3. Kör den kompilerade. jar-filen (till exempel en fil med namnet `java-unit-tests-1.0.jar` ) i HDInsight-klustret med följande kommando:

    ```apache
    hadoop jar java-unit-tests-1.0.jar JavaUnitTests
    hdfs://mycluster/tmp/hive/hive/5d9cf301-2503-48c7-9963-923fb5ef79a7/inuse.info
    hdfs://mycluster/tmp/hive/hive/5d9cf301-2503-48c7-9963-923fb5ef79a7/inuse.lck
    hdfs://mycluster/tmp/hive/hive/a0be04ea-ae01-4cc4-b56d-f263baf2e314/inuse.info
    hdfs://mycluster/tmp/hive/hive/a0be04ea-ae01-4cc4-b56d-f263baf2e314/inuse.lck
    ```

## <a name="storage-exception-for-write-on-blob"></a>Lagrings undantag för skrivning i BLOB

### <a name="issue"></a>Problem

När du använder `hadoop` `hdfs dfs` kommandona eller för att skriva filer som är ~ 12 GB eller större i ett HBase-kluster, kan du komma över följande fel:

```error
ERROR azure.NativeAzureFileSystem: Encountered Storage Exception for write on Blob : example/test_large_file.bin._COPYING_ Exception details: null Error Code : RequestBodyTooLarge
copyFromLocal: java.io.IOException
        at com.microsoft.azure.storage.core.Utility.initIOException(Utility.java:661)
        at com.microsoft.azure.storage.blob.BlobOutputStream$1.call(BlobOutputStream.java:366)
        at com.microsoft.azure.storage.blob.BlobOutputStream$1.call(BlobOutputStream.java:350)
        at java.util.concurrent.FutureTask.run(FutureTask.java:262)
        at java.util.concurrent.Executors$RunnableAdapter.call(Executors.java:471)
        at java.util.concurrent.FutureTask.run(FutureTask.java:262)
        at java.util.concurrent.ThreadPoolExecutor.runWorker(ThreadPoolExecutor.java:1145)
        at java.util.concurrent.ThreadPoolExecutor$Worker.run(ThreadPoolExecutor.java:615)
        at java.lang.Thread.run(Thread.java:745)
Caused by: com.microsoft.azure.storage.StorageException: The request body is too large and exceeds the maximum permissible limit.
        at com.microsoft.azure.storage.StorageException.translateException(StorageException.java:89)
        at com.microsoft.azure.storage.core.StorageRequest.materializeException(StorageRequest.java:307)
        at com.microsoft.azure.storage.core.ExecutionEngine.executeWithRetry(ExecutionEngine.java:182)
        at com.microsoft.azure.storage.blob.CloudBlockBlob.uploadBlockInternal(CloudBlockBlob.java:816)
        at com.microsoft.azure.storage.blob.CloudBlockBlob.uploadBlock(CloudBlockBlob.java:788)
        at com.microsoft.azure.storage.blob.BlobOutputStream$1.call(BlobOutputStream.java:354)
        ... 7 more
```

### <a name="cause"></a>Orsak

HBase på HDInsight-kluster standardvärdet är en block storlek på 256 KB vid skrivning till Azure Storage. Även om det fungerar för HBase-API: er eller REST API: er, resulterar det i ett fel när du använder `hadoop` `hdfs dfs` kommando rads verktygen eller.

### <a name="resolution"></a>Lösning

Används `fs.azure.write.request.size` för att ange en större block storlek. Du kan göra den här ändringen per användning med hjälp av- `-D` parametern. Följande kommando är ett exempel som använder den här parametern med `hadoop` kommandot:

```bash
hadoop -fs -D fs.azure.write.request.size=4194304 -copyFromLocal test_large_file.bin /example/data
```

Du kan också öka värdet för `fs.azure.write.request.size` globalt genom att använda Apache Ambari. Följande steg kan användas för att ändra värdet i Ambari-webbgränssnittet:

1. I webbläsaren går du till Ambari-webbgränssnittet för klustret. URL-adressen är `https://CLUSTERNAME.azurehdinsight.net` , där `CLUSTERNAME` är namnet på klustret. När du uppmanas till det anger du administratörens namn och lösen ord för klustret.
2. Välj **HDFS** på vänster sida av skärmen och välj sedan fliken **konfigurationer** .
3. I fältet **filter...** anger du `fs.azure.write.request.size` .
4. Ändra värdet från 262144 (256 KB) till det nya värdet. Till exempel 4194304 (4 MB).

    ![Bild av att ändra värdet via Ambari-webbgränssnittet](./media/hdinsight-troubleshoot-hdfs/hbase-change-block-write-size.png)

Mer information om hur du använder Ambari finns i [Hantera HDInsight-kluster med hjälp av Apache Ambari Web UI](hdinsight-hadoop-manage-ambari.md).

## <a name="du"></a>Gaz

[`-du`](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-common/FileSystemShell.html#du)Kommandot visar fil storlekar och kataloger i den aktuella katalogen eller filens längd, om det bara är en fil.

`-s`Alternativet genererar en sammanställd Sammanfattning av fil längder som visas.  
`-h`Alternativet formaterar fil storlekarna.

Exempel:

```bash
hdfs dfs -du -s -h hdfs://mycluster/
hdfs dfs -du -s -h hdfs://mycluster/tmp
```

## <a name="rm"></a>RM

Kommandot [-RM](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-common/FileSystemShell.html#rm) tar bort filer som angetts som argument.

Exempel:

```bash
hdfs dfs -rm hdfs://mycluster/tmp/testfile
```

## <a name="next-steps"></a>Nästa steg

[!INCLUDE [troubleshooting next steps](../../includes/hdinsight-troubleshooting-next-steps.md)]