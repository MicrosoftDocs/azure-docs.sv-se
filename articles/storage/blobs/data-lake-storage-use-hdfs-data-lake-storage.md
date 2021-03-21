---
title: Använda HDFS CLI med Azure Data Lake Storage Gen2
description: Använd Hadoop Distributed File System (HDFS) CLI för Azure Data Lake Storage Gen2. Skapa en behållare, hämta en lista över filer eller kataloger med mera.
services: storage
author: normesta
ms.service: storage
ms.topic: how-to
ms.date: 12/06/2018
ms.author: normesta
ms.subservice: data-lake-storage-gen2
ms.reviewer: artek
ms.openlocfilehash: b73e79c2f7bbd37b640d3e83d98be68d85ed3b5e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "101735377"
---
# <a name="using-the-hdfs-cli-with-data-lake-storage-gen2"></a>Använda HDFS CLI med Data Lake Storage Gen2

Du kan komma åt och hantera data i ditt lagrings konto med hjälp av ett kommando rads gränssnitt, precis som med en [Hadoop Distributed File System (HDFS)](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsDesign.html). Den här artikeln innehåller några exempel på hur du kommer igång.

HDInsight ger åtkomst till den distribuerade behållaren som är lokalt kopplad till Compute-noderna. Du kan komma åt den här behållaren med hjälp av gränssnittet som direkt interagerar med HDFS och de andra fil system som Hadoop stöder.

Mer information om HDFS CLI finns i den [officiella dokumentationen](https://hadoop.apache.org/docs/r2.4.1/hadoop-project-dist/hadoop-common/FileSystemShell.html) och [HDFS Permissions guide](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsPermissionsGuide.html)

>[!NOTE]
>Om du använder Azure Databricks i stället för HDInsight och du vill interagera med dina data med hjälp av ett kommando rads gränssnitt kan du använda Databricks CLI för att interagera med fil systemet Databricks. Se [DATABRICKS CLI](/azure/databricks/dev-tools/cli/).

## <a name="use-the-hdfs-cli-with-an-hdinsight-hadoop-cluster-on-linux"></a>Använda HDFS CLI med ett HDInsight Hadoop-kluster på Linux

Börja [med att upprätta fjärråtkomst till tjänster](../../hdinsight/hdinsight-hadoop-linux-information.md#remote-access-to-services). Om du väljer [SSH](../../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md) skulle PowerShell-exemplet se ut så här:

```powershell
#Connect to the cluster via SSH.
ssh sshuser@clustername-ssh.azurehdinsight.net
#Execute basic HDFS commands. Display the hierarchy.
hdfs dfs -ls /
#Create a sample directory.
hdfs dfs -mkdir /samplefolder
```
Du hittar anslutnings strängen i avsnittet "SSH + kluster inloggning" på bladet HDInsight-kluster i Azure Portal. SSH-autentiseringsuppgifter angavs vid tidpunkten för skapandet av klustret.

>[!IMPORTANT]
>HDInsight Cluster-faktureringen startar efter att ett kluster har skapats och stoppas när klustret tas bort. Debiteringen görs i förväg per minut, så du ska alltid ta bort ditt kluster när det inte används. Information om hur du tar bort ett kluster finns i vår [artikel i avsnittet](../../hdinsight/hdinsight-delete-cluster.md). Men data som lagras i ett lagrings konto med Data Lake Storage Gen2 aktiverat kvarstår även efter att ett HDInsight-kluster har tagits bort.

## <a name="create-a-container"></a>Skapa en container

`hdfs dfs -D "fs.azure.createRemoteFileSystemDuringInitialization=true" -ls abfs://<container-name>@<storage-account-name>.dfs.core.windows.net/`

* Ersätt `<container-name>` plats hållaren med det namn som du vill ge din behållare.

* Ersätt platshållaren `<storage-account-name>` med namnet på ditt lagringskonto.

## <a name="get-a-list-of-files-or-directories"></a>Hämta en lista över filer eller kataloger

`hdfs dfs -ls <path>`

Ersätt `<path>` plats hållaren med URI: n för behållaren eller container-mappen.

Exempelvis: `hdfs dfs -ls abfs://my-file-system@mystorageaccount.dfs.core.windows.net/my-directory-name`

## <a name="create-a-directory"></a>Skapa en katalog

`hdfs dfs -mkdir [-p] <path>`

Ersätt `<path>` plats hållaren med rot behållar namnet eller en mapp i din behållare.

Exempelvis: `hdfs dfs -mkdir abfs://my-file-system@mystorageaccount.dfs.core.windows.net/`

## <a name="delete-a-file-or-directory"></a>Ta bort en fil eller katalog

`hdfs dfs -rm <path>`

Ersätt `<path>` plats hållaren med URI: n för den fil eller mapp som du vill ta bort.

Exempelvis: `hdfs dfs -rmdir abfs://my-file-system@mystorageaccount.dfs.core.windows.net/my-directory-name/my-file-name`

## <a name="display-the-access-control-lists-acls-of-files-and-directories"></a>Visa Access Control listor (ACL: er) för filer och kataloger

`hdfs dfs -getfacl [-R] <path>`

Exempel:

`hdfs dfs -getfacl -R /dir`

Se [getfacl](https://hadoop.apache.org/docs/r2.4.1/hadoop-project-dist/hadoop-common/FileSystemShell.html#getfacl)

## <a name="set-acls-of-files-and-directories"></a>Ange ACL: er för filer och kataloger

`hdfs dfs -setfacl [-R] [-b|-k -m|-x <acl_spec> <path>]|[--set <acl_spec> <path>]`

Exempel:

`hdfs dfs -setfacl -m user:hadoop:rw- /file`

Se [setfacl](https://hadoop.apache.org/docs/r2.4.1/hadoop-project-dist/hadoop-common/FileSystemShell.html#setfacl)

## <a name="change-the-owner-of-files"></a>Ändra ägare till filer

`hdfs dfs -chown [-R] <new_owner>:<users_group> <URI>`

Se [chown](https://hadoop.apache.org/docs/r2.4.1/hadoop-project-dist/hadoop-common/FileSystemShell.html#chown)

## <a name="change-group-association-of-files"></a>Ändra grupp koppling för filer

`hdfs dfs -chgrp [-R] <group> <URI>`

Se [chgrp](https://hadoop.apache.org/docs/r2.4.1/hadoop-project-dist/hadoop-common/FileSystemShell.html#chgrp)

## <a name="change-the-permissions-of-files"></a>Ändra behörigheter för filer

`hdfs dfs -chmod [-R] <mode> <URI>`

Se [chmod](https://hadoop.apache.org/docs/r2.4.1/hadoop-project-dist/hadoop-common/FileSystemShell.html#chmod)

Du kan visa en fullständig lista över kommandon på webbplatsen [Apache Hadoop 2.4.1 Shell guide för fil system](https://hadoop.apache.org/docs/r2.4.1/hadoop-project-dist/hadoop-common/FileSystemShell.html) .

## <a name="next-steps"></a>Nästa steg

* [Använd ett Azure Data Lake Storage Gen2 kompatibelt konto i Azure Databricks](./data-lake-storage-use-databricks-spark.md)

* [Lär dig mer om åtkomst kontrol listor på filer och kataloger](./data-lake-storage-access-control.md)