---
title: Ansluta till eller installera Apache Beeline – Azure HDInsight
description: Lär dig hur du ansluter till Apache Beeline-klienten för att köra Hive-frågor med Hadoop på HDInsight. Beeline är ett verktyg för att arbeta med HiveServer2 över JDBC.
ms.service: hdinsight
ms.topic: how-to
ms.custom: contperf-fy21q1
ms.date: 05/27/2020
ms.openlocfilehash: ab5dedf6718dad4f16fde59d905e2e59be5c495f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98944461"
---
# <a name="connect-to-apache-beeline-on-hdinsight-or-install-it-locally"></a>Anslut till Apache Beeline på HDInsight eller installera det lokalt

[Apache Beeline](https://cwiki.apache.org/confluence/display/Hive/HiveServer2+Clients#HiveServer2Clients-Beeline–NewCommandLineShell) är en Hive-klient som ingår i head-noderna i HDInsight-klustret. Den här artikeln beskriver hur du ansluter till Beeline-klienten som är installerad på ditt HDInsight-kluster mellan olika typer av anslutningar. Den beskriver också hur du [installerar Beeline-klienten lokalt](#install-beeline-client). 

## <a name="types-of-connections"></a>Typer av anslutningar

### <a name="from-an-ssh-session"></a>Från en SSH-session

När du ansluter från en SSH-session till ett kluster huvudnoden kan du ansluta till `headnodehost` adressen på porten `10001` :

```bash
beeline -u 'jdbc:hive2://headnodehost:10001/;transportMode=http'
```

### <a name="over-an-azure-virtual-network"></a>Över en Azure-Virtual Network

När du ansluter från en klient till HDInsight över en Azure-Virtual Network måste du ange det fullständigt kvalificerade domän namnet (FQDN) för en kluster huvud nod. Eftersom den här anslutningen görs direkt till klusternoderna använder anslutningen port `10001` :

```bash
beeline -u 'jdbc:hive2://<headnode-FQDN>:10001/;transportMode=http'
```

Ersätt `<headnode-FQDN>` med det fullständigt kvalificerade domän namnet för ett kluster huvudnoden. Om du vill hitta det fullständigt kvalificerade domän namnet för en huvudnoden använder du informationen i [Hantera HDInsight med hjälp av Apache Ambari REST API](../hdinsight-hadoop-manage-ambari-rest-api.md#get-the-fqdn-of-cluster-nodes) Document.

### <a name="to-hdinsight-enterprise-security-package-esp-cluster-using-kerberos"></a>Till HDInsight Enterprise Security Package-kluster (ESP) med Kerberos

När du ansluter från en klient till ett Enterprise Security Package-kluster (ESP) som är anslutet till Azure Active Directory (AAD)-DS på en dator i samma sfär i klustret, måste du också ange domän namnet `<AAD-Domain>` och namnet på ett domän användar konto med behörighet att komma åt klustret `<username>` :

```bash
kinit <username>
beeline -u 'jdbc:hive2://<headnode-FQDN>:10001/default;principal=hive/_HOST@<AAD-Domain>;auth-kerberos;transportMode=http' -n <username>
```

Ersätt `<username>` med namnet på ett konto i domänen med behörighet att komma åt klustret. Ersätt `<AAD-DOMAIN>` med namnet på den Azure Active Directory (AAD) som klustret är anslutet till. Använd en versal sträng för `<AAD-DOMAIN>` värdet, annars hittas inte autentiseringsuppgiften. Sök `/etc/krb5.conf` efter sfär namnen om det behövs.

Så här hittar du JDBC-URL: en från Ambari:

1. I en webbläsare går du till `https://CLUSTERNAME.azurehdinsight.net/#/main/services/HIVE/summary` , där `CLUSTERNAME` är namnet på klustret. Se till att HiveServer2 körs.

1. Använd Urklipp för att kopiera HiveServer2 JDBC-URL: en.

### <a name="over-public-or-private-endpoints"></a>Över offentliga eller privata slut punkter

När du ansluter till ett kluster med hjälp av offentliga eller privata slut punkter måste du ange konto namnet för kluster inloggning (standard `admin` ) och lösen ord. Du kan till exempel använda Beeline från ett klient system för att ansluta till `clustername.azurehdinsight.net` adressen. Den här anslutningen görs via port `443` och krypteras med TLS/SSL.

Ersätt `clustername` med namnet på HDInsight-klustret. Ersätt `admin` med kluster inloggnings kontot för klustret. För ESP-kluster använder du fullständigt UPN (till exempel user@domain.com ). Ersätt `password` med lösen ordet för klustrets inloggnings konto.

```bash
beeline -u 'jdbc:hive2://clustername.azurehdinsight.net:443/;ssl=true;transportMode=http;httpPath=/hive2' -n admin -p 'password'
```

eller för privat slut punkt:

```bash
beeline -u 'jdbc:hive2://clustername-int.azurehdinsight.net:443/;ssl=true;transportMode=http;httpPath=/hive2' -n admin -p 'password'
```

Privata slut punkter pekar på en grundläggande belastningsutjämnare som bara kan nås från virtuella nätverk-peer i samma region. Mer information finns i [begränsningar i global VNet-peering och belastningsutjämnare](../../virtual-network/virtual-networks-faq.md#what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers) . Du kan använda `curl` kommandot med `-v` alternativ för att felsöka anslutnings problem med offentliga eller privata slut punkter innan du använder Beeline.

### <a name="use-beeline-with-apache-spark"></a>Använda Beeline med Apache Spark

Apache Spark tillhandahåller en egen implementering av HiveServer2, som ibland kallas Spark Thrift-servern. Den här tjänsten använder Spark SQL för att matcha frågor i stället för Hive. Och kan ge bättre prestanda beroende på din fråga.

#### <a name="through-public-or-private-endpoints"></a>Via offentliga eller privata slut punkter

Den anslutnings sträng som används skiljer sig något åt. I stället för att innehålla `httpPath=/hive2` det används `httpPath/sparkhive2` . Ersätt `clustername` med namnet på HDInsight-klustret. Ersätt `admin` med kluster inloggnings kontot för klustret. För ESP-kluster använder du fullständigt UPN (till exempel user@domain.com ). Ersätt `password` med lösen ordet för klustrets inloggnings konto.

```bash
beeline -u 'jdbc:hive2://clustername.azurehdinsight.net:443/;ssl=true;transportMode=http;httpPath=/sparkhive2' -n admin -p 'password'
```

eller för privat slut punkt:

```bash
beeline -u 'jdbc:hive2://clustername-int.azurehdinsight.net:443/;ssl=true;transportMode=http;httpPath=/sparkhive2' -n admin -p 'password'
```

Privata slut punkter pekar på en grundläggande belastningsutjämnare som bara kan nås från virtuella nätverk-peer i samma region. Mer information finns i [begränsningar i global VNet-peering och belastningsutjämnare](../../virtual-network/virtual-networks-faq.md#what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers) . Du kan använda `curl` kommandot med `-v` alternativ för att felsöka anslutnings problem med offentliga eller privata slut punkter innan du använder Beeline.

#### <a name="from-cluster-head-or-inside-azure-virtual-network-with-apache-spark"></a>Från kluster huvud eller inom Azure Virtual Network med Apache Spark

När du ansluter direkt från klustrets huvud nod, eller från en resurs i samma Azure-Virtual Network som HDInsight-klustret, `10002` ska porten användas för Spark Thrift-server i stället för `10001` . I följande exempel visas hur du ansluter direkt till Head-noden:

```bash
/usr/hdp/current/spark2-client/bin/beeline -u 'jdbc:hive2://headnodehost:10002/;transportMode=http'
```

## <a name="install-beeline-client"></a>Installera Beeline-klient

Även om Beeline ingår i head-noderna kanske du vill installera den lokalt.  Installations stegen för en lokal dator baseras på ett Windows- [undersystem för Linux](/windows/wsl/install-win10).

1. Uppdatera paket listor. Ange följande kommando i bash-gränssnittet:

    ```bash
    sudo apt-get update
    ```

1. Installera Java om det inte är installerat. Du kan kontrol lera med `which java` kommandot.

    1. Om inget Java-paket är installerat anger du följande kommando:

        ```bash
        sudo apt install openjdk-11-jre-headless
        ```

    1. Öppna filen bashrc (finns ofta i ~/.bashrc): `nano ~/.bashrc` .

    1. Ändra bashrc-filen. Lägg till följande rad i slutet av filen:

        ```bash
        export JAVA_HOME=/usr/lib/jvm/java-1.11.0-openjdk-amd64
        ```

        Tryck sedan på **CTRL + X**, sedan på **Y** och sedan Retur.

1. Hämta Hadoop-och Beeline-Arkiv och ange följande kommandon:

    ```bash
    wget https://archive.apache.org/dist/hadoop/core/hadoop-2.7.3/hadoop-2.7.3.tar.gz
    wget https://archive.apache.org/dist/hive/hive-1.2.1/apache-hive-1.2.1-bin.tar.gz
    ```

1. Packa upp arkiven och ange följande kommandon:

    ```bash
    tar -xvzf hadoop-2.7.3.tar.gz
    tar -xvzf apache-hive-1.2.1-bin.tar.gz
    ```

1. Ändra bashrc-filen ytterligare. Du måste identifiera sökvägen till platsen där arkiven packades upp. Om du använder [Windows-undersystemet för Linux](/windows/wsl/install-win10)och du följt stegen exakt, blir sökvägen `/mnt/c/Users/user/` , där `user` är ditt användar namn.

    1. Öppna filen: `nano ~/.bashrc`

    1. Ändra kommandona nedan med lämplig sökväg och ange dem i slutet av bashrc-filen:

        ```bash
        export HADOOP_HOME=/path_where_the_archives_were_unpacked/hadoop-2.7.3
        export HIVE_HOME=/path_where_the_archives_were_unpacked/apache-hive-1.2.1-bin
        PATH=$PATH:$HIVE_HOME/bin
        ```

    1. Tryck sedan på **CTRL + X**, sedan på **Y** och sedan Retur.

1. Stäng och öppna sedan bash-sessionen igen.

1. Testa anslutningen. Använd anslutnings formatet från [över offentliga eller privata slut punkter](#over-public-or-private-endpoints)ovan.

## <a name="next-steps"></a>Nästa steg

* Exempel som använder Beeline-klienten med Apache Hive finns i [använda Apache Beeline med Apache Hive](apache-hadoop-use-hive-beeline.md)
* Mer allmän information om Hive i HDInsight finns i [använda Apache Hive med Apache Hadoop på HDInsight](hdinsight-use-hive.md)