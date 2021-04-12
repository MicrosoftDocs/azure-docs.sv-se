---
title: Lokal HDFS fastnar i fel säkert läge på Azure HDInsight-kluster
description: Felsöka lokala Apache HDFS i fel säkert läge på Apache-kluster i Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 08/14/2019
ms.openlocfilehash: d34bf8d82aee14f5ba835f68a061555d24ee2621
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "98944448"
---
# <a name="scenario-local-hdfs-stuck-in-safe-mode-on-azure-hdinsight-cluster"></a>Scenario: lokal HDFS fastnar i fel säkert läge på Azure HDInsight-kluster

Den här artikeln beskriver fel söknings steg och möjliga lösningar för problem med att interagera med Azure HDInsight-kluster.

## <a name="issue"></a>Problem

Det lokala Apache Hadoop Distributed File System (HDFS) är fastnat i fel säkert läge i HDInsight-klustret. Du får ett fel meddelande som liknar följande:

```output
hdiuser@spark2:~$ hdfs dfs -D "fs.default.name=hdfs://mycluster/" -mkdir /temp
17/04/05 16:20:52 WARN retry.RetryInvocationHandler: Exception while invoking ClientNamenodeProtocolTranslatorPB.mkdirs over spark2.2oyzcdm4sfjuzjmj5dnmvscjpg.dx.internal.cloudapp.net/10.0.0.22:8020. Not retrying because try once and fail.
org.apache.hadoop.ipc.RemoteException(org.apache.hadoop.hdfs.server.namenode.SafeModeException): Cannot create directory /temp. Name node is in safe mode.
It was turned on manually. Use "hdfs dfsadmin -safemode leave" to turn safe mode off.
        at org.apache.hadoop.hdfs.server.namenode.FSNamesystem.checkNameNodeSafeMode(FSNamesystem.java:1359)
...
mkdir: Cannot create directory /temp. Name node is in safe mode.
```

## <a name="cause"></a>Orsak

HDInsight-klustret har skalats ned till mycket få noder nedan, eller så ligger antalet noder nära replikeringsintervallet.

## <a name="resolution"></a>Lösning

1. Rapport om status för HDFS i HDInsight-klustret med följande kommando:

    ```bash
    hdfs dfsadmin -D "fs.default.name=hdfs://mycluster/" -report
    ```

1. Kontrol lera integriteten hos HDFS i HDInsight-klustret med följande kommando:

    ```bash
    hdiuser@spark2:~$ hdfs fsck -D "fs.default.name=hdfs://mycluster/" /
    ```

1. Om du har fastställt att det inte finns några saknade, skadade eller under replikerade block eller om dessa block kan ignoreras kör du följande kommando för att ta bort namn-noden från fel säkert läge:

    ```bash
    hdfs dfsadmin -D "fs.default.name=hdfs://mycluster/" -safemode leave
    ```

## <a name="next-steps"></a>Nästa steg

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]