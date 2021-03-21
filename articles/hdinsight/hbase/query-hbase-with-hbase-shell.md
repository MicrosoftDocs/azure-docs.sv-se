---
title: 'Snabb start: fråga Apache HBase i Azure HDInsight – HBase Shell'
description: I den här snabb starten får du lära dig hur du använder Apache HBase-gränssnittet för att köra apache HBase-frågor.
keywords: HDInsight, Hadoop, HBase
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: quickstart
ms.date: 06/12/2019
ms.openlocfilehash: 358e058667cb1750a6fc97dffebcbfb2c6e06f84
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98942925"
---
# <a name="quickstart-query-apache-hbase-in-azure-hdinsight-with-hbase-shell"></a>Snabb start: fråga Apache HBase i Azure HDInsight med HBase Shell

I den här snabb starten får du lära dig hur du använder Apache HBase Shell för att skapa en HBase-tabell, infogar data och sedan frågar tabellen.

Om du inte har någon Azure-prenumeration kan du [skapa ett kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

* Ett Apache HBase-kluster. Se [skapa kluster](../hadoop/apache-hadoop-linux-tutorial-get-started.md) för att skapa ett HDInsight-kluster.  Se till att du väljer kluster typen **HBase** .

* En SSH-klient. Mer information finns i [Ansluta till HDInsight (Apache Hadoop) med hjälp av SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="create-a-table-and-manipulate-data"></a>Skapa en tabell och manipulera data

För de flesta visas data i tabellformat:

![HDInsight Apache HBase tabell data](./media/query-hbase-with-hbase-shell/hdinsight-hbase-contacts-tabular.png)

I HBase (en implementering av [Cloud BigTable](https://cloud.google.com/bigtable/)) ser samma data ut så här:

![HDInsight Apache HBase BigTable-data](./media/query-hbase-with-hbase-shell/hdinsight-hbase-contacts-bigtable.png)

Du kan använda SSH för att ansluta till HBase-kluster och sedan använda Apache HBase Shell för att skapa HBase-tabeller, infoga data och fråga efter data.

1. Använd `ssh` kommandot för att ansluta till ditt HBase-kluster. Redigera kommandot nedan genom att ersätta `CLUSTERNAME` med namnet på klustret och ange sedan kommandot:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

2. Använd `hbase shell` kommandot för att starta det interaktiva HBase-gränssnittet. Ange följande kommando i SSH-anslutningen:

    ```bash
    hbase shell
    ```

3. Använd `create` kommandot för att skapa en HBase-tabell med två kolumn serier. Ange följande kommando:

    ```hbase
    create 'Contacts', 'Personal', 'Office'
    ```

4. Använd `list` kommandot för att visa en lista över alla tabeller i HBase. Ange följande kommando:

    ```hbase
    list
    ```

5. Använd `put` kommandot för att infoga värden i en angiven kolumn i en angiven rad i en viss tabell. Ange följande kommando:

    ```hbase
    put 'Contacts', '1000', 'Personal:Name', 'John Dole'
    put 'Contacts', '1000', 'Personal:Phone', '1-425-000-0001'
    put 'Contacts', '1000', 'Office:Phone', '1-425-000-0002'
    put 'Contacts', '1000', 'Office:Address', '1111 San Gabriel Dr.'
    ```

6. Använd `scan` kommandot för att genomsöka och returnera `Contacts` tabell data. Ange följande kommando:

    ```hbase
    scan 'Contacts'
    ```

7. Använd `get` kommandot för att hämta innehållet i en rad. Ange följande kommando:

    ```hbase
    get 'Contacts', '1000'
    ```

    Du ser liknande resultat som när du använder `scan` kommandot eftersom det bara finns en rad.

8. Använd `delete` kommandot för att ta bort ett cell värde i en tabell. Ange följande kommando:

    ```hbase
    delete 'Contacts', '1000', 'Office:Address'
    ```

9. Använd `disable` kommandot för att inaktivera tabellen. Ange följande kommando:

    ```hbase
    disable 'Contacts'
    ```

10. Använd `drop` kommandot för att släppa en tabell från HBase. Ange följande kommando:

    ```hbase
    drop 'Contacts'
    ```

11. Använd `exit` kommandot för att stoppa det interaktiva HBase-gränssnittet. Ange följande kommando:

    ```hbase
    exit
    ```

Mer information om HBase Table-schemat finns i [Introduktion till Apache HBase schema design](http://0b4af6cdc2f0c5998459-c0245c5c937c5dedcca3f1764ecc9b2f.r43.cf2.rackcdn.com/9353-login1210_khurana.pdf). Fler HBase-kommandon finns i [referensguiden för Apache HBase](https://hbase.apache.org/book.html#quickstart).

## <a name="clean-up-resources"></a>Rensa resurser

När du har slutfört snabb starten kanske du vill ta bort klustret. Med HDInsight lagras dina data i Azure Storage så att du på ett säkert sätt kan ta bort ett kluster när det inte används. Du debiteras också för ett HDInsight-kluster, även när det inte används. Eftersom avgifterna för klustret är flera gånger större än avgifterna för lagring är det ekonomiskt sett bra att ta bort kluster när de inte används.

Om du vill ta bort ett kluster läser du [ta bort ett HDInsight-kluster med hjälp av webbläsaren, PowerShell eller Azure CLI](../hdinsight-delete-cluster.md).

## <a name="next-steps"></a>Nästa steg

I den här snabb starten har du lärt dig hur du använder Apache HBase Shell för att skapa en HBase-tabell, infogar data och sedan frågar tabellen. Om du vill veta mer om data som lagras i HBase visar nästa artikel hur du kör frågor med Apache Spark.

> [!div class="nextstepaction"]
> [Använda Apache Spark för att läsa och skriva Apache HBase-data](../hdinsight-using-spark-query-hbase.md)