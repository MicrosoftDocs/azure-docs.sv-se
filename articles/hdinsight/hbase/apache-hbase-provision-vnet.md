---
title: Skapa HBase-kluster i en Virtual Network – Azure
description: Kom igång med HBase i Azure HDInsight. Lär dig hur du skapar HDInsight HBase-kluster på Azure Virtual Network.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 12/23/2019
ms.openlocfilehash: a9a1788473cb31f8e78aac0bbd5979b3d681ad32
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/23/2021
ms.locfileid: "104867601"
---
# <a name="create-apache-hbase-clusters-on-hdinsight-in-azure-virtual-network"></a>Skapa Apache HBase-kluster på HDInsight i Azure Virtual Network

Lär dig hur du skapar Azure HDInsight Apache HBase-kluster i ett [Azure-Virtual Network](https://azure.microsoft.com/services/virtual-network/).

Med Virtual Network-integrering kan Apache HBase-kluster distribueras till samma virtuella nätverk som dina program så att program kan kommunicera med HBase direkt. Fördelarna innefattar:

* Direkt anslutning av webb programmet till noderna i HBase-klustret, vilket möjliggör kommunikation via HBase Java API för RPC (Remote Procedure Call).
* Bättre prestanda genom att inte låta trafiken gå över flera gatewayer och belastnings utjämning.
* Möjligheten att bearbeta känslig information på ett säkrare sätt utan att exponera en offentlig slut punkt.

Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="create-apache-hbase-cluster-into-virtual-network"></a>Skapa Apache HBase-kluster i ett virtuellt nätverk

I det här avsnittet skapar du ett Linux-baserat Apache HBase-kluster med det beroende Azure Storage-kontot i ett virtuellt Azure-nätverk med hjälp av en [Azure Resource Manager-mall](../../azure-resource-manager/templates/deploy-powershell.md). För andra metoder för att skapa kluster och förstå inställningarna, se [skapa HDInsight-kluster](../hdinsight-hadoop-provision-linux-clusters.md). Mer information om hur du använder en mall för att skapa Apache Hadoop kluster i HDInsight finns i [skapa Apache Hadoop kluster i HDInsight med Azure Resource Manager mallar](../hdinsight-hadoop-create-linux-clusters-arm-templates.md)

> [!NOTE]  
> Vissa egenskaper är hårdkodade i mallen. Exempel:
>
> * **Plats**: USA, östra 2
> * **Klusterversion**: 3.6
> * **Antal kluster arbets noder**: 2
> * **Standard lagrings konto**: en unik sträng
> * **Virtuellt nätverks namn**: kluster namn – VNet
> * **Adress utrymme för virtuellt nätverk**: 10.0.0.0/16
> * **Under näts namn**: subnet1
> * **Adress intervall för under nätet**: 10.0.0.0/24
>
> `CLUSTERNAME` ersätts med det kluster namn som du anger när du använder mallen.

1. Välj följande bild för att öppna mallen i Azure Portal. Mallen finns i [snabb starts mallar för Azure](https://azure.microsoft.com/resources/templates/101-hdinsight-hbase-linux-vnet/).

   <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-hbase-linux-vnet%2Fazuredeploy.json" target="_blank"><img src="./media/apache-hbase-provision-vnet/hdi-deploy-to-azure1.png" alt="Deploy to Azure button for new cluster"></a>

1. Välj **Redigera mall** i dialog rutan **Anpassad distribution** .

1. Ändra värde till på rad 165 `Standard_A3` `Standard_A4_V2` . Välj sedan **Spara**.

1. Slutför den återstående mallen med följande information:

    |Egenskap |Värde |
    |---|---|
    |Prenumeration|Välj en Azure-prenumeration som används för att skapa HDInsight-klustret, det beroende lagrings kontot och det virtuella Azure-nätverket.|
    Resursgrupp|Välj **Skapa ny** och ange ett nytt resurs grupp namn.|
    |Location|Välj en plats för resursgruppen.|
    |Klusternamn|Ange ett namn för det Hadoop-kluster som ska skapas.|
    |Användar namn och lösen ord för kluster inloggning|Standard användar namnet är **admin**. Ange ett lösen ord.|
    |Användar namn och lösen ord för SSH|Standard användar namnet är **sshuser**.  Ange ett lösen ord.|

    Välj **Jag accepterar villkoren och villkoren som anges ovan**.

1. Välj **Köp**. Det tar cirka 20 minuter att skapa ett kluster. När klustret har skapats kan du välja klustret i portalen för att öppna det.

När du har slutfört artikeln kanske du vill ta bort klustret. Med HDInsight lagras dina data i Azure Storage så att du på ett säkert sätt kan ta bort ett kluster när det inte används. Du debiteras också för ett HDInsight-kluster, även när det inte används. Eftersom avgifterna för klustret är flera gånger större än avgifterna för lagring är det ekonomiskt sett bra att ta bort kluster när de inte används. Instruktioner för hur du tar bort ett kluster finns i [hantera Apache Hadoop kluster i HDInsight med hjälp av Azure Portal](../hdinsight-administer-use-portal-linux.md#delete-clusters).

Om du vill börja arbeta med ditt nya HBase-kluster kan du använda de procedurer som finns i [Kom igång med Apache HBase med Apache Hadoop i HDInsight](./apache-hbase-tutorial-get-started-linux.md).

## <a name="connect-to-the-apache-hbase-cluster-using-apache-hbase-java-rpc-apis"></a>Ansluta till Apache HBase-klustret med Apache HBase Java RPC API: er

### <a name="create-a-virtual-machine"></a>Skapa en virtuell dator

Skapa en virtuell IaaS-dator (Infrastructure as a Service) i samma virtuella Azure-nätverk och samma undernät. Instruktioner för hur du skapar en ny virtuell IaaS-dator finns i [skapa en virtuell dator som kör Windows Server](../../virtual-machines/windows/quick-create-portal.md). När du följer stegen i det här dokumentet måste du använda följande värden för nätverks konfigurationen:

* **Virtuellt nätverk**: kluster namn – VNet
* **Undernät**: subnet1

> [!IMPORTANT]  
> Ersätt `CLUSTERNAME` med det namn som du använde när du skapade HDInsight-klustret i föregående steg.

Med dessa värden placeras den virtuella datorn i samma virtuella nätverk och undernät som HDInsight-klustret. Med den här konfigurationen kan de kommunicera direkt med varandra. Det finns ett sätt att skapa ett HDInsight-kluster med en tom Edge-nod. Edge-noden kan användas för att hantera klustret.  Mer information finns i [använda tomma Edge-noder i HDInsight](../hdinsight-apps-use-edge-node.md).

### <a name="obtain-fully-qualified-domain-name"></a>Få fullständigt kvalificerat domän namn

När du använder ett Java-program för att fjärrans luta till HBase måste du använda det fullständigt kvalificerade domän namnet (FQDN). För att fastställa detta måste du hämta det anslutningsspecifika DNS-suffixet för HBase-klustret. Om du vill göra det kan du använda någon av följande metoder:

* Använda en webbläsare för att göra ett [Apache Ambari](https://ambari.apache.org/) -anrop:

    Gå till `https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/hosts?minimal_response=true`. Den returnerar en JSON-fil med DNS-suffix.

* Använd Ambari webbplats:

    1. Gå till `https://CLUSTERNAME.azurehdinsight.net`.
    2. Välj **värdar** på den översta menyn.

* Använd sväng för att göra REST-anrop:

    ```bash
    curl -u <username>:<password> -k https://CLUSTERNAME.azurehdinsight.net/ambari/api/v1/clusters/CLUSTERNAME.azurehdinsight.net/services/hbase/components/hbrest
    ```

Leta upp posten "host_name" i de JavaScript Object Notation (JSON) som returneras. Den innehåller FQDN för noderna i klustret. Exempel:

```json
"host_name" : "hn0-hbaseg.hjfrnszlumfuhfk4pi1guh410c.bx.internal.cloudapp.net"
```

Den del av domän namnet som börjar med kluster namnet är DNS-suffixet. Till exempel `hjfrnszlumfuhfk4pi1guh410c.bx.internal.cloudapp.net`.

### <a name="verify-communication-inside-virtual-network"></a>Verifiera kommunikation i virtuellt nätverk

Du kan kontrol lera att den virtuella datorn kan kommunicera med HBase-klustret genom att använda kommandot `ping headnode0.<dns suffix>` från den virtuella datorn. Till exempel `ping hn0-hbaseg.hjfrnszlumfuhfk4pi1guh410c.bx.internal.cloudapp.net`.

Om du vill använda den här informationen i ett Java-program kan du följa stegen i [använda Apache Maven för att skapa Java-program som använder Apache HBase med HDInsight (Hadoop)](./apache-hbase-build-java-maven-linux.md) för att skapa ett program. Om du vill att programmet ska ansluta till en fjärran sluten HBase-Server ändrar du **hbase-site.xml** filen i det här exemplet för att använda det fullständiga domän namnet för Zookeeper. Exempel:

```xml
<property>
    <name>hbase.zookeeper.quorum</name>
    <value>zookeeper0.<dns suffix>,zookeeper1.<dns suffix>,zookeeper2.<dns suffix></value>
</property>
```

> [!NOTE]  
> Mer information om namn matchning i virtuella Azure-nätverk, inklusive hur du använder din egen DNS-server, finns i [namn matchning (DNS)](../../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md).

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du lärt dig hur du skapar ett Apache HBase-kluster. Mer information finns i:

* [Kom igång med HDInsight](../hadoop/apache-hadoop-linux-tutorial-get-started.md)
* [Använd tomma Edge-noder i HDInsight](../hdinsight-apps-use-edge-node.md)
* [Konfigurera Apache HBase-replikering i HDInsight](apache-hbase-replication.md)
* [Skapa Apache Hadoop kluster i HDInsight](../hdinsight-hadoop-provision-linux-clusters.md)
* [Kom igång med Apache HBase med Apache Hadoop i HDInsight](./apache-hbase-tutorial-get-started-linux.md)
* [Översikt över Virtual Network](../../virtual-network/virtual-networks-overview.md)
