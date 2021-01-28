---
title: Ansluta till Kafka med virtuella nätverk – Azure HDInsight
description: Lär dig hur du ansluter direkt till Kafka i HDInsight via en Azure-Virtual Network. Lär dig hur du ansluter till Kafka från utvecklings klienter med en VPN-gateway eller från klienter i ditt lokala nätverk med hjälp av en VPN gateway-enhet.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive, devx-track-python
ms.date: 03/04/2020
ms.openlocfilehash: ad802b2bdf08a8e43179beece5f52d869513aff3
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/28/2021
ms.locfileid: "98933038"
---
# <a name="connect-to-apache-kafka-on-hdinsight-through-an-azure-virtual-network"></a>Ansluta till Apache Kafka på HDInsight via Azure Virtual Network

Lär dig hur du ansluter direkt till Apache Kafka i HDInsight via en Azure-Virtual Network. Det här dokumentet innehåller information om hur du ansluter till Kafka med hjälp av följande konfigurationer:

* Från resurser i ett lokalt nätverk. Den här anslutningen upprättas med hjälp av en VPN-enhet (program vara eller maskin vara) i det lokala nätverket.
* Från en utvecklings miljö med hjälp av en VPN-programklient.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="architecture-and-planning"></a>Arkitektur och planering

HDInsight tillåter inte direkt anslutning till Kafka via det offentliga Internet. I stället måste Kafka-klienter (producenter och konsumenter) använda någon av följande anslutnings metoder:

* Kör klienten i samma virtuella nätverk som Kafka i HDInsight. Den här konfigurationen används i dokumentet [starta med Apache Kafka i HDInsight](apache-kafka-get-started.md) . Klienten körs direkt på HDInsight-klusternoderna eller på en annan virtuell dator i samma nätverk.

* Anslut ett privat nätverk, till exempel ditt lokala nätverk, till det virtuella nätverket. Med den här konfigurationen kan klienter i ditt lokala nätverk direkt arbeta med Kafka. Om du vill aktivera den här konfigurationen utför du följande uppgifter:

  1. Skapa ett virtuellt nätverk.
  2. Skapa en VPN-gateway som använder en plats-till-plats-konfiguration. Konfigurationen som används i det här dokumentet ansluter till en VPN gateway-enhet i ditt lokala nätverk.
  3. Skapa en DNS-server i det virtuella nätverket.
  4. Konfigurera vidarebefordring mellan DNS-servern i varje nätverk.
  5. Skapa en Kafka på HDInsight-kluster i det virtuella nätverket.

     Mer information finns i avsnittet [ansluta till Apache Kafka från ett lokalt nätverk](#on-premises) .

* Anslut enskilda datorer till det virtuella nätverket med en VPN-gateway och en VPN-klient. Om du vill aktivera den här konfigurationen utför du följande uppgifter:

  1. Skapa ett virtuellt nätverk.
  2. Skapa en VPN-gateway som använder en punkt-till-plats-konfiguration. Den här konfigurationen kan användas med både Windows-och MacOS-klienter.
  3. Skapa en Kafka på HDInsight-kluster i det virtuella nätverket.
  4. Konfigurera Kafka för IP-annonsering. Med den här konfigurationen kan klienten ansluta med IP-adresser för Broker i stället för domän namn.
  5. Hämta och Använd VPN-klienten i utvecklings systemet.

     Mer information finns i avsnittet [Anslut till Apache Kafka med en VPN-klient](#vpnclient) .

     > [!WARNING]  
     > Den här konfigurationen rekommenderas endast i utvecklings syfte av följande begränsningar:
     >
     > * Varje klient måste ansluta via en VPN-programklient.
     > * VPN-klienten skickar inte namn matchnings begär anden till det virtuella nätverket, så du måste använda IP-adresser för att kommunicera med Kafka. IP-kommunikation kräver ytterligare konfiguration av Kafka-klustret.

Mer information om hur du använder HDInsight i ett virtuellt nätverk finns i [planera ett virtuellt nätverk för Azure HDInsight-kluster](../hdinsight-plan-virtual-network-deployment.md).

## <a name="connect-to-apache-kafka-from-an-on-premises-network"></a><a id="on-premises"></a> Ansluta till Apache Kafka från ett lokalt nätverk

Om du vill skapa ett Kafka-kluster som kommunicerar med ditt lokala nätverk följer du stegen i [ansluta HDInsight till ditt lokala nätverks](./../connect-on-premises-network.md) dokument.

> [!IMPORTANT]  
> När du skapar HDInsight-klustret väljer du kluster typen __Kafka__ .

De här stegen skapar följande konfiguration:

* Azure Virtual Network
* Plats-till-plats-VPN-gateway
* Azure Storage konto (används av HDInsight)
* Kafka på HDInsight

Du kan kontrol lera att en Kafka-klient kan ansluta till klustret lokalt genom att följa stegen i avsnittet [exempel: python-klient](#python-client) .

## <a name="connect-to-apache-kafka-with-a-vpn-client"></a><a id="vpnclient"></a> Ansluta till Apache Kafka med en VPN-klient

Använd stegen i det här avsnittet för att skapa följande konfiguration:

* Azure Virtual Network
* Punkt-till-plats-VPN-gateway
* Azure Storage konto (används av HDInsight)
* Kafka på HDInsight

1. Följ stegen i dokumentet [arbeta med självsignerade certifikat för punkt-till-plats-anslutningar](../../vpn-gateway/vpn-gateway-certificates-point-to-site.md) . Det här dokumentet skapar de certifikat som krävs för gatewayen.

2. Öppna en PowerShell-prompt och Använd följande kod för att logga in på din Azure-prenumeration:

    ```powershell
    Connect-AzAccount
    # If you have multiple subscriptions, uncomment to set the subscription
    #Select-AzSubscription -SubscriptionName "name of your subscription"
    ```

3. Använd följande kod för att skapa variabler som innehåller konfigurations information:

    ```powershell
    # Prompt for generic information
    $resourceGroupName = Read-Host "What is the resource group name?"
    $baseName = Read-Host "What is the base name? It is used to create names for resources, such as 'net-basename' and 'kafka-basename':"
    $location = Read-Host "What Azure Region do you want to create the resources in?"
    $rootCert = Read-Host "What is the file path to the root certificate? It is used to secure the VPN gateway."

    # Prompt for HDInsight credentials
    $adminCreds = Get-Credential -Message "Enter the HTTPS user name and password for the HDInsight cluster" -UserName "admin"
    $sshCreds = Get-Credential -Message "Enter the SSH user name and password for the HDInsight cluster" -UserName "sshuser"

    # Names for Azure resources
    $networkName = "net-$baseName"
    $clusterName = "kafka-$baseName"
    $storageName = "store$baseName" # Can't use dashes in storage names
    $defaultContainerName = $clusterName
    $defaultSubnetName = "default"
    $gatewaySubnetName = "GatewaySubnet"
    $gatewayPublicIpName = "GatewayIp"
    $gatewayIpConfigName = "GatewayConfig"
    $vpnRootCertName = "rootcert"
    $vpnName = "VPNGateway"

    # Network settings
    $networkAddressPrefix = "10.0.0.0/16"
    $defaultSubnetPrefix = "10.0.0.0/24"
    $gatewaySubnetPrefix = "10.0.1.0/24"
    $vpnClientAddressPool = "172.16.201.0/24"

    # HDInsight settings
    $HdiWorkerNodes = 4
    $hdiVersion = "3.6"
    $hdiType = "Kafka"
    ```

4. Använd följande kod för att skapa Azure-resurs gruppen och det virtuella nätverket:

    ```powershell
    # Create the resource group that contains everything
    New-AzResourceGroup -Name $resourceGroupName -Location $location

    # Create the subnet configuration
    $defaultSubnetConfig = New-AzVirtualNetworkSubnetConfig -Name $defaultSubnetName `
        -AddressPrefix $defaultSubnetPrefix
    $gatewaySubnetConfig = New-AzVirtualNetworkSubnetConfig -Name $gatewaySubnetName `
        -AddressPrefix $gatewaySubnetPrefix

    # Create the subnet
    New-AzVirtualNetwork -Name $networkName `
        -ResourceGroupName $resourceGroupName `
        -Location $location `
        -AddressPrefix $networkAddressPrefix `
        -Subnet $defaultSubnetConfig, $gatewaySubnetConfig

    # Get the network & subnet that were created
    $network = Get-AzVirtualNetwork -Name $networkName `
        -ResourceGroupName $resourceGroupName
    $gatewaySubnet = Get-AzVirtualNetworkSubnetConfig -Name $gatewaySubnetName `
        -VirtualNetwork $network
    $defaultSubnet = Get-AzVirtualNetworkSubnetConfig -Name $defaultSubnetName `
        -VirtualNetwork $network

    # Set a dynamic public IP address for the gateway subnet
    $gatewayPublicIp = New-AzPublicIpAddress -Name $gatewayPublicIpName `
        -ResourceGroupName $resourceGroupName `
        -Location $location `
        -AllocationMethod Dynamic
    $gatewayIpConfig = New-AzVirtualNetworkGatewayIpConfig -Name $gatewayIpConfigName `
        -Subnet $gatewaySubnet `
        -PublicIpAddress $gatewayPublicIp

    # Get the certificate info
    # Get the full path in case a relative path was passed
    $rootCertFile = Get-ChildItem $rootCert
    $cert = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2($rootCertFile)
    $certBase64 = [System.Convert]::ToBase64String($cert.RawData)
    $p2sRootCert = New-AzVpnClientRootCertificate -Name $vpnRootCertName `
        -PublicCertData $certBase64

    # Create the VPN gateway
    New-AzVirtualNetworkGateway -Name $vpnName `
        -ResourceGroupName $resourceGroupName `
        -Location $location `
        -IpConfigurations $gatewayIpConfig `
        -GatewayType Vpn `
        -VpnType RouteBased `
        -EnableBgp $false `
        -GatewaySku Standard `
        -VpnClientAddressPool $vpnClientAddressPool `
        -VpnClientRootCertificates $p2sRootCert
    ```

    > [!WARNING]  
    > Det kan ta flera minuter för den här processen att slutföras.

5. Använd följande kod för att skapa Azure Storage konto och blob-behållare:

    ```powershell
    # Create the storage account
    New-AzStorageAccount `
        -ResourceGroupName $resourceGroupName `
        -Name $storageName `
        -SkuName Standard_GRS `
        -Location $location `
        -Kind StorageV2 `
        -EnableHttpsTrafficOnly 1

    # Get the storage account keys and create a context
    $defaultStorageKey = (Get-AzStorageAccountKey -ResourceGroupName $resourceGroupName `
        -Name $storageName)[0].Value
    $storageContext = New-AzStorageContext -StorageAccountName $storageName `
        -StorageAccountKey $defaultStorageKey

    # Create the default storage container
    New-AzStorageContainer -Name $defaultContainerName `
        -Context $storageContext
    ```

6. Använd följande kod för att skapa HDInsight-klustret:

    ```powershell
    # Create the HDInsight cluster
    New-AzHDInsightCluster `
        -ResourceGroupName $resourceGroupName `
        -ClusterName $clusterName `
        -Location $location `
        -ClusterSizeInNodes $hdiWorkerNodes `
        -ClusterType $hdiType `
        -OSType Linux `
        -Version $hdiVersion `
        -HttpCredential $adminCreds `
        -SshCredential $sshCreds `
        -DefaultStorageAccountName "$storageName.blob.core.windows.net" `
        -DefaultStorageAccountKey $defaultStorageKey `
        -DefaultStorageContainer $defaultContainerName `
        -DisksPerWorkerNode 2 `
        -VirtualNetworkId $network.Id `
        -SubnetName $defaultSubnet.Id
    ```

   > [!WARNING]  
   > Den här processen tar cirka 15 minuter att slutföra.

### <a name="configure-kafka-for-ip-advertising"></a>Konfigurera Kafka för IP-annonsering

Som standard returnerar Apache Zookeeper domän namnet för Kafka-utjämnarna till klienter. Den här konfigurationen fungerar inte med VPN-klientprogrammet eftersom den inte kan använda namn matchning för entiteter i det virtuella nätverket. Använd följande steg för att konfigurera Kafka för att annonsera IP-adresser i stället för domän namn för den här konfigurationen:

1. Via en webbläsare går du till `https://CLUSTERNAME.azurehdinsight.net`. Ersätt `CLUSTERNAME` med namnet på Kafka i HDInsight-klustret.

    När du uppmanas använder du HTTPS-användarnamnet och lösen ordet för klustret. Ambari webb gränssnitt för klustret visas.

2. Om du vill visa information om Kafka väljer du __Kafka__ i listan till vänster.

    ![Tjänst lista med Kafka markerat](./media/apache-kafka-connect-vpn-gateway/select-kafka-service.png)

3. Om du vill visa Kafka-konfiguration väljer du __konfiguration__ från det översta mitten.

    ![Konfiguration av Apache Ambari Services](./media/apache-kafka-connect-vpn-gateway/select-kafka-config1.png)

4. Du hittar __Kafka-kuvert-__ konfigurationen genom att ange `kafka-env` i fältet __filter__ längst upp till höger.

    ![Kafka-konfiguration, för Kafka-miljö](./media/apache-kafka-connect-vpn-gateway/search-for-kafka-env.png)

5. Om du vill konfigurera Kafka för att annonsera IP-adresser lägger du till följande text längst ned i fältet __Kafka-kuvert-mall__ :

    ```
    # Configure Kafka to advertise IP addresses instead of FQDN
    IP_ADDRESS=$(hostname -i)
    echo advertised.listeners=$IP_ADDRESS
    sed -i.bak -e '/advertised/{/advertised@/!d;}' /usr/hdp/current/kafka-broker/conf/server.properties
    echo "advertised.listeners=PLAINTEXT://$IP_ADDRESS:9092" >> /usr/hdp/current/kafka-broker/conf/server.properties
    ```

6. Om du vill konfigurera gränssnittet som Kafka lyssnar på anger du `listeners` i fältet __filter__ längst upp till höger.

7. Om du vill konfigurera Kafka för att lyssna på alla nätverks gränssnitt ändrar du värdet i fältet __Listeners__ till `PLAINTEXT://0.0.0.0:9092` .

8. Använd knappen __Spara__ om du vill spara konfigurations ändringarna. Ange ett textmeddelande som beskriver ändringarna. Välj __OK__ när ändringarna har sparats.

    ![Apache Ambari-Spara konfiguration](./media/apache-kafka-connect-vpn-gateway/save-configuration-button.png)

9. Om du vill förhindra fel när du startar om Kafka använder du knappen __service åtgärder__ och väljer __Aktivera underhålls läge__. Klicka på OK för att slutföra åtgärden.

    ![Tjänst åtgärder, med aktivera underhåll markerat](./media/apache-kafka-connect-vpn-gateway/turn-on-maintenance-mode.png)

10. Om du vill starta om Kafka använder du knappen __starta om__ och väljer __starta om alla berörda__. Bekräfta omstarten och Använd sedan __OK__ -knappen när åtgärden har slutförts.

    ![Knappen starta om med starta om alla berörda markerade](./media/apache-kafka-connect-vpn-gateway/restart-required-button.png)

11. Om du vill inaktivera underhålls läget använder du knappen __service åtgärder__ och väljer __inaktivera underhålls läge__. Klicka på **OK** för att slutföra åtgärden.

### <a name="connect-to-the-vpn-gateway"></a>Ansluta till VPN-gatewayen

Om du vill ansluta till VPN-gatewayen använder du avsnittet __Anslut till Azure__ i dokumentet [Konfigurera en punkt-till-plats-anslutning](../../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md#connect) .

## <a name="example-python-client"></a><a id="python-client"></a> Exempel: python-klient

Om du vill verifiera anslutningen till Kafka kan du använda följande steg för att skapa och köra en python-tillverkare och-konsument:

1. Använd någon av följande metoder för att hämta det fullständigt kvalificerade domän namnet (FQDN) och IP-adresserna för noderna i Kafka-klustret:

    ```powershell
    $resourceGroupName = "The resource group that contains the virtual network used with HDInsight"

    $clusterNICs = Get-AzNetworkInterface -ResourceGroupName $resourceGroupName | where-object {$_.Name -like "*node*"}

    $nodes = @()
    foreach($nic in $clusterNICs) {
        $node = new-object System.Object
        $node | add-member -MemberType NoteProperty -name "Type" -value $nic.Name.Split('-')[1]
        $node | add-member -MemberType NoteProperty -name "InternalIP" -value $nic.IpConfigurations.PrivateIpAddress
        $node | add-member -MemberType NoteProperty -name "InternalFQDN" -value $nic.DnsSettings.InternalFqdn
        $nodes += $node
    }
    $nodes | sort-object Type
    ```

    ```azurecli
    az network nic list --resource-group <resourcegroupname> --output table --query "[?contains(name,'node')].{NICname:name,InternalIP:ipConfigurations[0].privateIpAddress,InternalFQDN:dnsSettings.internalFqdn}"
    ```

    Det här skriptet förutsätter att `$resourceGroupName` är namnet på den Azure-resurs grupp som innehåller det virtuella nätverket.

    Spara den information som du returnerade för användning i nästa steg.

2. Använd följande för att installera [Kafka-python-](https://kafka-python.readthedocs.io/) klienten:

    ```bash
    pip install kafka-python
    ```

3. Använd följande python-kod om du vill skicka data till Kafka:

   ```python
   from kafka import KafkaProducer
   # Replace the `ip_address` entries with the IP address of your worker nodes
   # NOTE: you don't need the full list of worker nodes, just one or two.
   producer = KafkaProducer(bootstrap_servers=['kafka_broker_1','kafka_broker_2'])
   for _ in range(50):
      producer.send('testtopic', b'test message')
   ```

    Ersätt `'kafka_broker'` posterna med adresserna som returneras från steg 1 i det här avsnittet:

   * Om du använder en __Software VPN-klient__ ersätter du `kafka_broker` posterna med IP-adressen för dina arbetsnoder.

   * Om du har __aktiverat namn matchning via en anpassad DNS-Server__ ersätter du `kafka_broker` posterna med det fullständiga domän namnet för arbetsnoderna.

     > [!NOTE]
     > Den här koden skickar strängen `test message` till ämnet `testtopic` . Standard konfigurationen för Kafka i HDInsight är att skapa ämnet om det inte finns.

4. Om du vill hämta meddelandena från Kafka använder du följande python-kod:

   ```python
   from kafka import KafkaConsumer
   # Replace the `ip_address` entries with the IP address of your worker nodes
   # Again, you only need one or two, not the full list.
   # Note: auto_offset_reset='earliest' resets the starting offset to the beginning
   #       of the topic
   consumer = KafkaConsumer(bootstrap_servers=['kafka_broker_1','kafka_broker_2'],auto_offset_reset='earliest')
   consumer.subscribe(['testtopic'])
   for msg in consumer:
     print (msg)
   ```

    Ersätt `'kafka_broker'` posterna med adresserna som returneras från steg 1 i det här avsnittet:

    * Om du använder en __Software VPN-klient__ ersätter du `kafka_broker` posterna med IP-adressen för dina arbetsnoder.

    * Om du har __aktiverat namn matchning via en anpassad DNS-Server__ ersätter du `kafka_broker` posterna med det fullständiga domän namnet för arbetsnoderna.

## <a name="next-steps"></a>Nästa steg

Mer information om hur du använder HDInsight med ett virtuellt nätverk finns i avsnittet [Planera en distribution av virtuella nätverk för Azure HDInsight-kluster](../hdinsight-plan-virtual-network-deployment.md) .

Mer information om hur du skapar en Azure-Virtual Network med punkt-till-plats-VPN-gateway finns i följande dokument:

* [Konfigurera en punkt-till-plats-anslutning med hjälp av Azure Portal](../../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md)

* [Konfigurera en punkt-till-plats-anslutning med hjälp av Azure PowerShell](../../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md)

Mer information om hur du arbetar med Apache Kafka i HDInsight finns i följande dokument:

* [Kom igång med Apache Kafka på HDInsight](apache-kafka-get-started.md)
* [Använda spegling med Apache Kafka på HDInsight](apache-kafka-mirroring.md)
