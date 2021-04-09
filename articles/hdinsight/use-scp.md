---
title: Använd SCP med Apache Hadoop i Azure HDInsight
description: Det här dokumentet innehåller information om hur du ansluter till HDInsight med hjälp av SSH-och SCP-kommandona.
ms.service: hdinsight
ms.topic: how-to
ms.custom: seoapr2020
ms.date: 04/22/2020
ms.openlocfilehash: 927b8c55008c3e01d8ff1dd09c46cfa3c6618026
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "98946278"
---
# <a name="use-scp-with-apache-hadoop-in-azure-hdinsight"></a>Använd SCP med Apache Hadoop i Azure HDInsight

Den här artikeln innehåller information om hur du överför filer på ett säkert sätt med ditt HDInsight-kluster.

## <a name="copy-files"></a>Kopiera filer

Verktyget `scp` kan användas för att kopiera filer till och från enskilda noder i ett kluster. Med följande kommando kopieras till exempel katalogen `test.txt` från det lokala systemet till den primära huvudnoden:

```bash
scp test.txt sshuser@clustername-ssh.azurehdinsight.net:
```

Eftersom ingen sökväg har angetts efter `:` placeras filen i hemkatalogen `sshuser`.

I följande exempel kopieras filen `test.txt` från hemkatalogen `sshuser` på den primära huvudnoden till det lokala systemet:

```bash
scp sshuser@clustername-ssh.azurehdinsight.net:test.txt .
```

`scp` har bara åtkomst tull filsystemet för enskilda noder i klustret. Det kan inte användas för att komma åt data i HDFS-kompatibla lagrings utrymmen för klustret.

Använd `scp` när du behöver ladda upp en resurs för en SSH-session. Ladda exempelvis upp ett Python-skript och kör det sedan från en SSH-session.

I följande dokument hittar du information om att läsa in data direkt till det HDFS-kompatibla lagringsutrymmet:

* [HDInsight med Azure Storage](hdinsight-hadoop-use-blob-storage.md).
* [HDInsight med Azure Data Lake Storage gen1](../hdinsight/hdinsight-hadoop-use-data-lake-storage-gen1.md).

## <a name="next-steps"></a>Nästa steg

* [Använda SSH med HDInsight](./hdinsight-hadoop-linux-use-ssh-unix.md)
* [Använda kantnoder i HDInsight](hdinsight-apps-use-edge-node.md#access-an-edge-node)
