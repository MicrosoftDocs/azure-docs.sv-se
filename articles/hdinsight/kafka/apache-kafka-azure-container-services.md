---
title: Använda Azure Kubernetes-tjänsten med Kafka på HDInsight
description: Lär dig hur du använder Kafka på HDInsight från behållar avbildningar som finns i Azure Kubernetes service (AKS).
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 12/04/2019
ms.openlocfilehash: 2216eb5893b77761f4d31c5819d152ceeb985abc
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/23/2021
ms.locfileid: "104869658"
---
# <a name="use-azure-kubernetes-service-with-apache-kafka-on-hdinsight"></a>Använda Azure Kubernetes-tjänsten med Apache Kafka på HDInsight

Lär dig hur du använder Azure Kubernetes service (AKS) med [Apache Kafka](https://kafka.apache.org/) på HDInsight-kluster. Stegen i det här dokumentet använder ett Node.js-program som finns i AKS för att verifiera anslutningen med Kafka. Det här programmet använder [Kafka-Node-](https://www.npmjs.com/package/kafka-node) paketet för att kommunicera med Kafka. Den använder [socket.io](https://socket.io/) för händelse drivna meddelanden mellan webb läsar klienten och Server delen som finns i AKS.

[Apache Kafka](https://kafka.apache.org) är en distribuerad direktuppspelningsplattform med öppen källkod som kan användas för att skapa realtidsuppspelade datapipelines och program. Azure Kubernetes-tjänsten hanterar din värdbaserade Kubernetes-miljö och gör det snabbt och enkelt att distribuera program i behållare. Med hjälp av en Azure-Virtual Network kan du ansluta de två tjänsterna.

> [!NOTE]  
> Det här dokumentet fokuserar på de steg som krävs för att aktivera Azure Kubernetes-tjänsten för att kommunicera med Kafka på HDInsight. Själva exemplet är bara en grundläggande Kafka-klient för att demonstrera att konfigurationen fungerar.

## <a name="prerequisites"></a>Förutsättningar

* [Azure CLI](/cli/azure/install-azure-cli)
* En Azure-prenumeration

Det här dokumentet förutsätter att du är van att skapa och använda följande Azure-tjänster:

* Kafka på HDInsight
* Azure Kubernetes Service
*  Azure Virtual Networks

Det här dokumentet förutsätter också att du har gick genom [självstudien för Azure Kubernetes-tjänsten](../../aks/tutorial-kubernetes-prepare-app.md). Den här artikeln skapar en behållar tjänst, skapar ett Kubernetes-kluster, ett behållar register och konfigurerar `kubectl` verktyget.

## <a name="architecture"></a>Arkitektur

### <a name="network-topology"></a>Nätverkstopologi

Både HDInsight och AKS använder en Azure-Virtual Network som en behållare för beräknings resurser. Om du vill aktivera kommunikation mellan HDInsight och AKS måste du aktivera kommunikation mellan sina nätverk. Stegen i det här dokumentet använder Virtual Network peering till nätverken. Andra anslutningar, till exempel VPN, bör också fungera. Mer information om peering finns i det [virtuella nätverkets peering](../../virtual-network/virtual-network-peering-overview.md) -dokument.

Följande diagram illustrerar nätverk sto pol Ogin som används i det här dokumentet:

:::image type="content" source="./media/apache-kafka-azure-container-services/kafka-aks-architecture.png" alt-text="HDInsight i ett virtuellt nätverk, AKS i ett annat, med peering" border="false":::

> [!IMPORTANT]  
> Namn matchning är inte aktiverat mellan peer-kompatibla nätverk, så IP-adressering används. Som standard är Kafka på HDInsight konfigurerat att returnera värdnamn i stället för IP-adresser när klienter ansluter. Stegen i det här dokumentet ändrar Kafka för att använda IP-annonsering i stället.

## <a name="create-an-azure-kubernetes-service-aks"></a>Skapa en Azure Kubernetes-tjänst (AKS)

Om du inte redan har ett AKS-kluster kan du använda något av följande dokument för att lära dig hur du skapar ett:

* [Distribuera ett Azure Kubernetes service (AKS)-kluster – Portal](../../aks/kubernetes-walkthrough-portal.md)
* [Distribuera ett Azure Kubernetes service (AKS)-kluster – CLI](../../aks/kubernetes-walkthrough.md)

> [!IMPORTANT]  
> AKS skapar ett virtuellt nätverk under installationen i en **ytterligare** resurs grupp. Den ytterligare resurs gruppen följer namngivnings konventionen för **MC_resourceGroup_AKSclusterName_location**.  
> Det här nätverket är peer-kopplat till det som skapats för HDInsight i nästa avsnitt.

## <a name="configure-virtual-network-peering"></a>Konfigurera peering för virtuellt nätverk

### <a name="identify-preliminary-information"></a>Identifiera preliminär information

1. Leta upp den ytterligare **resurs grupp** som innehåller det virtuella nätverket för ditt AKS-kluster från [Azure Portal](https://portal.azure.com).

2. Välj den __virtuella nätverks__ resursen i resurs gruppen. Spara namnet för senare användning.

3. Gå till **Inställningar** och välj __Adressutrymme__. Observera adress utrymmet i listan.

### <a name="create-virtual-network"></a>Skapa det virtuella nätverket

1. Om du vill skapa ett virtuellt nätverk för HDInsight går du till __+ skapa en resurs__  >  __nätverk__  >  __virtuellt nätverk__.

1. Skapa nätverket med följande rikt linjer för vissa egenskaper:

    |Egenskap | Värde |
    |---|---|
    |Adressutrymme|Du måste använda ett adress utrymme som inte överlappar det som används av AKS-kluster nätverket.|
    |Location|Använd samma __plats__ för det virtuella nätverk som du använde för AKS-klustret.|

1. Vänta tills det virtuella nätverket har skapats innan du går vidare till nästa steg.

### <a name="configure-peering"></a>Konfigurera peering

1. Om du vill konfigurera peering mellan HDInsight-nätverket och AKS-klustret väljer du det virtuella nätverket och väljer sedan __peering__.

1. Välj __+ Lägg till__ och Använd följande värden för att fylla i formuläret:

    |Egenskap |Värde |
    |---|---|
    |Peer-kopplingens namn från \<this VN> till det virtuella fjärrnätverket|Ange ett unikt namn för peering-konfigurationen.|
    |Virtuellt nätverk|Välj det virtuella nätverket för **AKS-klustret**.|
    |Namn på peer koppling från \<AKS VN> till \<this VN>|Ange ett unikt namn.|

    Lämna alla andra fält i standardvärdet och välj sedan __OK__ för att konfigurera peer koppling.

## <a name="create-apache-kafka-cluster-on-hdinsight"></a>Skapa Apache Kafka kluster i HDInsight

När du skapar Kafka i HDInsight-klustret måste du ansluta det virtuella nätverket som skapades tidigare för HDInsight. Mer information om hur du skapar ett Kafka-kluster finns i [skapa ett kluster](apache-kafka-get-started.md) dokument för Apache Kafka.

## <a name="configure-apache-kafka-ip-advertising"></a>Konfigurera Apache Kafka IP-annonsering

Använd följande steg för att konfigurera Kafka för att annonsera IP-adresser i stället för domän namn:

1. Via en webbläsare går du till `https://CLUSTERNAME.azurehdinsight.net`. Ersätt kluster namn med namnet på Kafka i HDInsight-klustret.

    När du uppmanas använder du HTTPS-användarnamnet och lösen ordet för klustret. Ambari webb gränssnitt för klustret visas.

2. Om du vill visa information om Kafka väljer du __Kafka__ i listan till vänster.

    :::image type="content" source="./media/apache-kafka-azure-container-services/select-kafka-service.png" alt-text="Tjänst lista med Kafka markerat" border="true":::

3. Om du vill visa Kafka-konfiguration väljer du __konfiguration__ från det översta mitten.

    :::image type="content" source="./media/apache-kafka-azure-container-services/select-kafka-config1.png" alt-text="Konfiguration av Apache Ambari Services" border="true":::

4. Du hittar __Kafka-kuvert-__ konfigurationen genom att ange `kafka-env` i fältet __filter__ längst upp till höger.

    :::image type="content" source="./media/apache-kafka-azure-container-services/search-for-kafka-env.png" alt-text="Kafka-konfiguration, för Kafka-miljö" border="true":::

5. Om du vill konfigurera Kafka för att annonsera IP-adresser lägger du till följande text längst ned i fältet __Kafka-kuvert-mall__ :

    ```bash
    # Configure Kafka to advertise IP addresses instead of FQDN
    IP_ADDRESS=$(hostname -i)
    echo advertised.listeners=$IP_ADDRESS
    sed -i.bak -e '/advertised/{/advertised@/!d;}' /usr/hdp/current/kafka-broker/conf/server.properties
    echo "advertised.listeners=PLAINTEXT://$IP_ADDRESS:9092" >> /usr/hdp/current/kafka-broker/conf/server.properties
    ```

6. Om du vill konfigurera gränssnittet som Kafka lyssnar på anger du `listeners` i fältet __filter__ längst upp till höger.

7. Om du vill konfigurera Kafka för att lyssna på alla nätverks gränssnitt ändrar du värdet i fältet __Listeners__ till `PLAINTEXT://0.0.0.0:9092` .

8. Använd knappen __Spara__ om du vill spara konfigurations ändringarna. Ange ett textmeddelande som beskriver ändringarna. Välj __OK__ när ändringarna har sparats.

    :::image type="content" source="./media/apache-kafka-azure-container-services/save-configuration-button.png" alt-text="Apache Ambari-Spara konfiguration" border="true":::

9. Om du vill förhindra fel när du startar om Kafka använder du knappen __service åtgärder__ och väljer __Aktivera underhålls läge__. Klicka på OK för att slutföra åtgärden.

    :::image type="content" source="./media/apache-kafka-azure-container-services/turn-on-maintenance-mode.png" alt-text="Tjänst åtgärder, med aktivera underhåll markerat" border="true":::

10. Om du vill starta om Kafka använder du knappen __starta om__ och väljer __starta om alla berörda__. Bekräfta omstarten och Använd sedan __OK__ -knappen när åtgärden har slutförts.

    :::image type="content" source="./media/apache-kafka-azure-container-services/restart-required-button.png" alt-text="Knappen starta om med starta om alla berörda markerade" border="true":::

11. Om du vill inaktivera underhålls läget använder du knappen __service åtgärder__ och väljer __inaktivera underhålls läge__. Klicka på **OK** för att slutföra åtgärden.

## <a name="test-the-configuration"></a>Testa konfigurationen

I det här läget kommunicerar Kafka och Azure Kubernetes-tjänsten via de peer-peer-virtuella nätverken. Använd följande steg för att testa anslutningen:

1. Skapa ett Kafka-ämne som används av test programmet. Information om hur du skapar Kafka-ämnen finns i [skapa ett kluster](apache-kafka-get-started.md) dokument för Apache Kafka.

2. Ladda ned exempel programmet från [https://github.com/Blackmist/Kafka-AKS-Test](https://github.com/Blackmist/Kafka-AKS-Test) .

3. Redigera `index.js` filen och ändra följande rader:

    * `var topic = 'mytopic'`: Ersätt `mytopic` med namnet på Kafka-ämnet som används av det här programmet.
    * `var brokerHost = '176.16.0.13:9092`: Ersätt `176.16.0.13` med den interna IP-adressen för en av Service Broker-värdarna för klustret.

        För att hitta den interna IP-adressen för Service Broker-värdarna (workernodes) i klustret, se [Apache Ambari REST API](../hdinsight-hadoop-manage-ambari-rest-api.md#get-the-internal-ip-address-of-cluster-nodes) Document. Välj IP-adressen för en av posterna där domän namnet börjar med `wn` .

4. Från en kommando rad i `src` katalogen installerar du beroenden och använder Docker för att bygga en avbildning för distribution:

    ```bash
    docker build -t kafka-aks-test .
    ```

    > [!NOTE]  
    > Paket som krävs av det här programmet kontrol leras i lagrings platsen, så du behöver inte använda `npm` verktyget för att installera dem.

5. Logga in på din Azure Container Registry (ACR) och leta upp namnet på namnet:

    ```azurecli
    az acr login --name <acrName>
    az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table
    ```

    > [!NOTE]  
    > Om du inte känner till Azure Container Registry namn eller inte är bekant med att använda Azure CLI för att arbeta med Azure Kubernetes-tjänsten kan du läsa mer i [AKS-självstudierna](../../aks/tutorial-kubernetes-prepare-app.md).

6. Tagga den lokala `kafka-aks-test` avbildningen med namnet i din ACR. Lägg också `:v1` till i slutet för att ange avbildnings versionen:

    ```bash
    docker tag kafka-aks-test <acrLoginServer>/kafka-aks-test:v1
    ```

7. Push-överför avbildningen till registret:

    ```bash
    docker push <acrLoginServer>/kafka-aks-test:v1
    ```

    Den här åtgärden tar flera minuter att slutföra.

8. Redigera manifest filen Kubernetes ( `kafka-aks-test.yaml` ) och Ersätt `microsoft` med ACR namnet-namnet som hämtades i steg 4.

9. Använd följande kommando för att distribuera program inställningarna från manifestet:

    ```bash
    kubectl create -f kafka-aks-test.yaml
    ```

10. Använd följande kommando för att se för `EXTERNAL-IP` programmet:

    ```bash
    kubectl get service kafka-aks-test --watch
    ```

    När en extern IP-adress har tilldelats använder du __CTRL + C__ för att avsluta klockan

11. Öppna en webbläsare och ange den externa IP-adressen för tjänsten. Du kommer till en sida som liknar följande bild:

    :::image type="content" source="./media/apache-kafka-azure-container-services/test-web-page-image1.png" alt-text="Bild av Apache Kafka-test på webb sidan" border="true":::

12. Ange text i fältet och välj sedan knappen __Skicka__ . Data skickas till Kafka. Sedan läser Kafka-konsumenten i programmet meddelandet och lägger till det i avsnittet __meddelanden från Kafka__ .

    > [!WARNING]  
    > Du kan få flera kopior av ett meddelande. Det här problemet uppstår vanligt vis när du uppdaterar din webbläsare efter att ha anslutit eller öppnar flera webb läsar anslutningar till programmet.

## <a name="next-steps"></a>Nästa steg

Använd följande länkar om du vill veta om hur du använder Apache Kafka på HDInsight:

* [Kom igång med Apache Kafka på HDInsight](apache-kafka-get-started.md)

* [Använd MirrorMaker för att skapa en replik av Apache Kafka på HDInsight](apache-kafka-mirroring.md)

* [Använda Apache Storm med Apache Kafka på HDInsight](../hdinsight-apache-storm-with-kafka.md)

* [Använda Apache Spark med Apache Kafka på HDInsight](../hdinsight-apache-spark-with-kafka.md)

* [Ansluta till Apache Kafka via en Azure-Virtual Network](apache-kafka-connect-vpn-gateway.md)
