---
title: Felsöka garn i Azure HDInsight
description: Få svar på vanliga frågor om att arbeta med Apache Hadoop garn och Azure HDInsight.
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 08/15/2019
ms.openlocfilehash: b5b8ccbfd3e39415416cfc8fabcf9614f5934042
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/28/2021
ms.locfileid: "98931409"
---
# <a name="troubleshoot-apache-hadoop-yarn-by-using-azure-hdinsight"></a>Felsöka Apache Hadoop YARN med Azure HDInsight

Lär dig mer om de vanligaste problemen och deras lösningar när du arbetar med Apache Hadoop garn nytto laster i Apache Ambari.

## <a name="how-do-i-create-a-new-yarn-queue-on-a-cluster"></a>Hur gör jag för att skapa en ny garn kö i ett kluster?

### <a name="resolution-steps"></a>Lösningssteg

Använd följande steg i Ambari för att skapa en ny garn-kö och sedan utjämna kapacitets tilldelningen bland alla köer.

I det här exemplet ändras två befintliga köer (**standard** -och **thriftsvr**) från 50% kapacitet till 25% kapacitet, vilket ger den nya kön (Spark) 50% kapacitet.

| Kö | Kapacitet | Maximal kapacitet |
| --- | --- | --- |
| standard | 25 % | 50 % |
| thrftsvr | 25 % | 50 % |
| spark | 50 % | 50 % |

1. Välj ikonen **Ambari views** och välj sedan rutnäts mönstret. Välj sedan **garn Queue Manager**.

    ![Apache Ambari Dashboard garn Queue Manager](media/hdinsight-troubleshoot-yarn/apache-yarn-create-queue-1.png)
2. Välj **standard** kön.

    ![Apache Ambari-garn Välj standard kön](media/hdinsight-troubleshoot-yarn/apache-yarn-create-queue-2.png)
3. Ändra **kapaciteten** från 50% till 25% för **standard** kön. Ändra **kapaciteten** till 25% för **thriftsvr** -kön.

    ![Ändra kapaciteten till 25% för standard-och thriftsvr-köerna](media/hdinsight-troubleshoot-yarn/apache-yarn-create-queue-3.png)
4. Om du vill skapa en ny kö väljer du **Lägg till kö**.

    ![Apache Ambari garn Dashboard Lägg till kö](media/hdinsight-troubleshoot-yarn/apache-yarn-create-queue-4.png)

5. Namnge den nya kön.

    ![Apache Ambari garn instrument panel, namn kö](media/hdinsight-troubleshoot-yarn/apache-yarn-create-queue-5.png)  

6. Lämna **kapacitets** värden på 50% och välj sedan knappen **åtgärder** .

    ![Apache Ambari-garn Välj åtgärd](media/hdinsight-troubleshoot-yarn/apache-yarn-create-queue-6.png)  
7. Välj **Spara och uppdatera köer**.

    ![Välj Spara och uppdatera köer](media/hdinsight-troubleshoot-yarn/apache-yarn-create-queue-7.png)  

Dessa ändringar visas omedelbart i användar gränssnittet för garn Scheduler.

### <a name="additional-reading"></a>Mer att läsa

- [Apache Hadoop garn CapacityScheduler](https://hadoop.apache.org/docs/r2.7.2/hadoop-yarn/hadoop-yarn-site/CapacityScheduler.html)

## <a name="how-do-i-download-yarn-logs-from-a-cluster"></a>Hur gör jag för att hämta garn loggar från ett kluster?

### <a name="resolution-steps"></a>Lösningssteg

1. Anslut till HDInsight-klustret med hjälp av en SSH-klient (Secure Shell). Mer information finns i [Ytterligare läsning](#additional-reading-2).

1. Om du vill visa alla program-ID: n för de garn program som körs för närvarande kör du följande kommando:

    ```apache
    yarn top
    ```

    ID: na visas i kolumnen **APPLICATIONID** . Du kan hämta loggar från kolumnen **APPLICATIONID** .

    ```apache
    YARN top - 18:00:07, up 19d, 0:14, 0 active users, queue(s): root
    NodeManager(s): 4 total, 4 active, 0 unhealthy, 0 decommissioned, 0 lost, 0 rebooted
    Queue(s) Applications: 2 running, 10 submitted, 0 pending, 8 completed, 0 killed, 0 failed
    Queue(s) Mem(GB): 97 available, 3 allocated, 0 pending, 0 reserved
    Queue(s) VCores: 58 available, 2 allocated, 0 pending, 0 reserved
    Queue(s) Containers: 2 allocated, 0 pending, 0 reserved

                      APPLICATIONID USER             TYPE      QUEUE   #CONT  #RCONT  VCORES RVCORES     MEM    RMEM  VCORESECS    MEMSECS %PROGR       TIME NAME
     application_1490377567345_0007 hive            spark  thriftsvr       1       0       1       0      1G      0G    1628407    2442611  10.00   18:20:20 Thrift JDBC/ODBC Server
     application_1490377567345_0006 hive            spark  thriftsvr       1       0       1       0      1G      0G    1628430    2442645  10.00   18:20:20 Thrift JDBC/ODBC Server
    ```

1. Använd följande kommando för att hämta loggar för garn behållare för alla program hanterare:

    ```apache
    yarn logs -applicationIdn logs -applicationId <application_id> -am ALL > amlogs.txt
    ```

    Det här kommandot skapar en loggfil med namnet amlogs.txt.

1. Använd följande kommando för att hämta loggar för garn behållare för endast den senaste program hanteraren:

    ```apache
    yarn logs -applicationIdn logs -applicationId <application_id> -am -1 > latestamlogs.txt
    ```

    Det här kommandot skapar en loggfil med namnet latestamlogs.txt.

1. Använd följande kommando för att hämta loggar för garn behållare för de första två program huvuden:

    ```apache
    yarn logs -applicationIdn logs -applicationId <application_id> -am 1,2 > first2amlogs.txt
    ```

    Det här kommandot skapar en loggfil med namnet first2amlogs.txt.

1. Använd följande kommando för att hämta alla garn container loggar:

    ```apache
    yarn logs -applicationIdn logs -applicationId <application_id> > logs.txt
    ```

    Det här kommandot skapar en loggfil med namnet logs.txt.

1. Använd följande kommando för att hämta loggen för garn behållare för en speciell behållare:

    ```apache
    yarn logs -applicationIdn logs -applicationId <application_id> -containerId <container_id> > containerlogs.txt
    ```

    Det här kommandot skapar en loggfil med namnet containerlogs.txt.

### <a name="additional-reading"></a><a name="additional-reading-2"></a>Mer att läsa

- [Ansluta till HDInsight (Apache Hadoop) med hjälp av SSH](./hdinsight-hadoop-linux-use-ssh-unix.md)
- [Apache Hadoop garn koncept och program](https://hadoop.apache.org/docs/r2.7.4/hadoop-yarn/hadoop-yarn-site/WritingYarnApplications.html#Concepts_and_Flow)

## <a name="next-steps"></a>Nästa steg

[!INCLUDE [troubleshooting next steps](../../includes/hdinsight-troubleshooting-next-steps.md)]