---
title: Skapa virtuella nätverk för Azure HDInsight-kluster
description: Lär dig hur du skapar en Azure-Virtual Network för att ansluta HDInsight till andra moln resurser eller resurser i ditt data Center.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive, devx-track-azurecli
ms.date: 04/16/2020
ms.openlocfilehash: 43d57eac94cabb5c648183911e0c0bf72889946d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "98946079"
---
# <a name="create-virtual-networks-for-azure-hdinsight-clusters"></a>Skapa virtuella nätverk för Azure HDInsight-kluster

Den här artikeln innehåller exempel och kod exempel för att skapa och konfigurera [virtuella Azure-nätverk](../virtual-network/virtual-networks-overview.md). För användning med Azure HDInsight-kluster. Detaljerade exempel på hur du skapar nätverks säkerhets grupper (NSG: er) och hur du konfigurerar DNS visas.

Bakgrunds information om hur du använder virtuella nätverk med Azure HDInsight finns i [planera ett virtuellt nätverk för Azure HDInsight](hdinsight-plan-virtual-network-deployment.md).

## <a name="prerequisites-for-code-samples-and-examples"></a>Krav för kod exempel och exempel

Innan du kör något av kod exemplen i den här artikeln har du en förståelse för TCP/IP-nätverk. Om du inte är bekant med TCP/IP-nätverk bör du kontakta någon innan du gör ändringar i produktions nätverken.

Andra krav för exemplen i den här artikeln innehåller följande objekt:

* Om du använder PowerShell måste du installera [AZ-modulen](/powershell/azure/).
* Om du vill använda Azure CLI och ännu inte har installerat det kan du läsa [Installera Azure CLI](/cli/azure/install-azure-cli).

> [!IMPORTANT]  
> Om du vill ha stegvisa anvisningar om hur du ansluter HDInsight till ditt lokala nätverk med hjälp av en Azure-Virtual Network, se [ansluta HDInsight till det lokala nätverks](connect-on-premises-network.md) dokumentet.

## <a name="example-network-security-groups-with-hdinsight"></a><a id="hdinsight-nsg"></a>Exempel: nätverks säkerhets grupper med HDInsight

Exemplen i det här avsnittet visar hur du skapar regler för nätverks säkerhets grupper. Reglerna tillåter att HDInsight kommunicerar med Azures hanterings tjänster. Innan du använder exemplen måste du justera IP-adresserna så att de matchar dem för den Azure-region som du använder. Du hittar den här informationen i [hanterings-IP-adresser för HDInsight](hdinsight-management-ip-addresses.md).

### <a name="azure-resource-management-template"></a>Mall för Azure-resurs hantering

Följande resurs hanterings mall skapar ett virtuellt nätverk som begränsar inkommande trafik, men tillåter trafik från de IP-adresser som krävs av HDInsight. Den här mallen skapar också ett HDInsight-kluster i det virtuella nätverket.

* [Distribuera ett säkert Azure-Virtual Network och ett HDInsight Hadoop-kluster](https://azure.microsoft.com/resources/templates/101-hdinsight-secure-vnet/)

### <a name="azure-powershell"></a>Azure PowerShell

Använd följande PowerShell-skript för att skapa ett virtuellt nätverk som begränsar inkommande trafik och tillåter trafik från IP-adresserna för regionen Nord Europa.

> [!IMPORTANT]  
> Ändra IP-adresserna för `hdirule1` och `hdirule2` i det här exemplet för att matcha Azure-regionen som du använder. Du hittar den här informationen [HDInsight Management IP-adresser](hdinsight-management-ip-addresses.md).

```azurepowershell
$vnetName = "Replace with your virtual network name"
$resourceGroupName = "Replace with the resource group the virtual network is in"
$subnetName = "Replace with the name of the subnet that you plan to use for HDInsight"

# Get the Virtual Network object
$vnet = Get-AzVirtualNetwork `
    -Name $vnetName `
    -ResourceGroupName $resourceGroupName

# Get the region the Virtual network is in.
$location = $vnet.Location

# Get the subnet object
$subnet = $vnet.Subnets | Where-Object Name -eq $subnetName

# Create a Network Security Group.
# And add exemptions for the HDInsight health and management services.
$nsg = New-AzNetworkSecurityGroup `
    -Name "hdisecure" `
    -ResourceGroupName $resourceGroupName `
    -Location $location `
    | Add-AzNetworkSecurityRuleConfig `
        -name "hdirule1" `
        -Description "HDI health and management address 52.164.210.96" `
        -Protocol "*" `
        -SourcePortRange "*" `
        -DestinationPortRange "443" `
        -SourceAddressPrefix "52.164.210.96" `
        -DestinationAddressPrefix "VirtualNetwork" `
        -Access Allow `
        -Priority 300 `
        -Direction Inbound `
    | Add-AzNetworkSecurityRuleConfig `
        -Name "hdirule2" `
        -Description "HDI health and management 13.74.153.132" `
        -Protocol "*" `
        -SourcePortRange "*" `
        -DestinationPortRange "443" `
        -SourceAddressPrefix "13.74.153.132" `
        -DestinationAddressPrefix "VirtualNetwork" `
        -Access Allow `
        -Priority 301 `
        -Direction Inbound `
    | Add-AzNetworkSecurityRuleConfig `
        -Name "hdirule3" `
        -Description "HDI health and management 168.61.49.99" `
        -Protocol "*" `
        -SourcePortRange "*" `
        -DestinationPortRange "443" `
        -SourceAddressPrefix "168.61.49.99" `
        -DestinationAddressPrefix "VirtualNetwork" `
        -Access Allow `
        -Priority 302 `
        -Direction Inbound `
    | Add-AzNetworkSecurityRuleConfig `
        -Name "hdirule4" `
        -Description "HDI health and management 23.99.5.239" `
        -Protocol "*" `
        -SourcePortRange "*" `
        -DestinationPortRange "443" `
        -SourceAddressPrefix "23.99.5.239" `
        -DestinationAddressPrefix "VirtualNetwork" `
        -Access Allow `
        -Priority 303 `
        -Direction Inbound `
    | Add-AzNetworkSecurityRuleConfig `
        -Name "hdirule5" `
        -Description "HDI health and management 168.61.48.131" `
        -Protocol "*" `
        -SourcePortRange "*" `
        -DestinationPortRange "443" `
        -SourceAddressPrefix "168.61.48.131" `
        -DestinationAddressPrefix "VirtualNetwork" `
        -Access Allow `
        -Priority 304 `
        -Direction Inbound `
    | Add-AzNetworkSecurityRuleConfig `
        -Name "hdirule6" `
        -Description "HDI health and management 138.91.141.162" `
        -Protocol "*" `
        -SourcePortRange "*" `
        -DestinationPortRange "443" `
        -SourceAddressPrefix "138.91.141.162" `
        -DestinationAddressPrefix "VirtualNetwork" `
        -Access Allow `
        -Priority 305 `
        -Direction Inbound `

# Set the changes to the security group
Set-AzNetworkSecurityGroup -NetworkSecurityGroup $nsg

# Apply the NSG to the subnet
Set-AzVirtualNetworkSubnetConfig `
    -VirtualNetwork $vnet `
    -Name $subnetName `
    -AddressPrefix $subnet.AddressPrefix `
    -NetworkSecurityGroup $nsg
$vnet | Set-AzVirtualNetwork
```

Det här exemplet visar hur du lägger till regler som tillåter inkommande trafik på de begärda IP-adresserna. Den innehåller ingen regel för att begränsa inkommande åtkomst från andra källor. Följande kod visar hur du aktiverar SSH-åtkomst från Internet:

```azurepowershell
Get-AzNetworkSecurityGroup -Name hdisecure -ResourceGroupName RESOURCEGROUP |
Add-AzNetworkSecurityRuleConfig -Name "SSH" -Description "SSH" -Protocol "*" -SourcePortRange "*" -DestinationPortRange "22" -SourceAddressPrefix "*" -DestinationAddressPrefix "VirtualNetwork" -Access Allow -Priority 306 -Direction Inbound
```

### <a name="azure-cli"></a>Azure CLI

Använd följande steg för att skapa ett virtuellt nätverk som begränsar inkommande trafik, men tillåter trafik från de IP-adresser som krävs av HDInsight.

1. Använd följande kommando för att skapa en ny nätverks säkerhets grupp med namnet `hdisecure` . Ersätt `RESOURCEGROUP` med den resurs grupp som innehåller Azure-Virtual Network. Ersätt `LOCATION` med den plats (region) som gruppen skapades i.

    ```azurecli
    az network nsg create -g RESOURCEGROUP -n hdisecure -l LOCATION
    ```

    När gruppen har skapats får du information om den nya gruppen.

2. Använd följande för att lägga till regler i den nya nätverks säkerhets gruppen som tillåter inkommande kommunikation på port 443 från Azure HDInsight-tjänsten för hälso tillstånd och hantering. Ersätt `RESOURCEGROUP` med namnet på den resurs grupp som innehåller Azure-Virtual Network.

    > [!IMPORTANT]  
    > Ändra IP-adresserna för `hdirule1` och `hdirule2` i det här exemplet för att matcha Azure-regionen som du använder. Du hittar den här informationen i [hanterings-IP-adresser för HDInsight](hdinsight-management-ip-addresses.md).

    ```azurecli
    az network nsg rule create -g RESOURCEGROUP --nsg-name hdisecure -n hdirule1 --protocol "*" --source-port-range "*" --destination-port-range "443" --source-address-prefix "52.164.210.96" --destination-address-prefix "VirtualNetwork" --access "Allow" --priority 300 --direction "Inbound"
    az network nsg rule create -g RESOURCEGROUP --nsg-name hdisecure -n hdirule2 --protocol "*" --source-port-range "*" --destination-port-range "443" --source-address-prefix "13.74.153.132" --destination-address-prefix "VirtualNetwork" --access "Allow" --priority 301 --direction "Inbound"
    az network nsg rule create -g RESOURCEGROUP --nsg-name hdisecure -n hdirule3 --protocol "*" --source-port-range "*" --destination-port-range "443" --source-address-prefix "168.61.49.99" --destination-address-prefix "VirtualNetwork" --access "Allow" --priority 302 --direction "Inbound"
    az network nsg rule create -g RESOURCEGROUP --nsg-name hdisecure -n hdirule4 --protocol "*" --source-port-range "*" --destination-port-range "443" --source-address-prefix "23.99.5.239" --destination-address-prefix "VirtualNetwork" --access "Allow" --priority 303 --direction "Inbound"
    az network nsg rule create -g RESOURCEGROUP --nsg-name hdisecure -n hdirule5 --protocol "*" --source-port-range "*" --destination-port-range "443" --source-address-prefix "168.61.48.131" --destination-address-prefix "VirtualNetwork" --access "Allow" --priority 304 --direction "Inbound"
    az network nsg rule create -g RESOURCEGROUP --nsg-name hdisecure -n hdirule6 --protocol "*" --source-port-range "*" --destination-port-range "443" --source-address-prefix "138.91.141.162" --destination-address-prefix "VirtualNetwork" --access "Allow" --priority 305 --direction "Inbound"
    ```

3. Använd följande kommando för att hämta den unika identifieraren för den här nätverks säkerhets gruppen:

    ```azurecli
    az network nsg show -g RESOURCEGROUP -n hdisecure --query "id"
    ```

    Det här kommandot returnerar ett värde som liknar följande text:

    ```output
    "/subscriptions/SUBSCRIPTIONID/resourceGroups/RESOURCEGROUP/providers/Microsoft.Network/networkSecurityGroups/hdisecure"
    ```

4. Använd följande kommando för att tillämpa nätverks säkerhets gruppen på ett undernät. Ersätt `GUID` värdena och `RESOURCEGROUP` med dem som returnerades från föregående steg. Ersätt `VNETNAME` och `SUBNETNAME` med namnet på det virtuella nätverket och under nätet som du vill skapa.

    ```azurecli
    az network vnet subnet update -g RESOURCEGROUP --vnet-name VNETNAME --name SUBNETNAME --set networkSecurityGroup.id="/subscriptions/GUID/resourceGroups/RESOURCEGROUP/providers/Microsoft.Network/networkSecurityGroups/hdisecure"
    ```

    När det här kommandot har slutförts kan du installera HDInsight i Virtual Network.

De här stegen öppnar bara åtkomst till tjänsten HDInsight Health and Management i Azure-molnet. All annan åtkomst till HDInsight-klustret utanför Virtual Network blockeras. Om du vill aktivera åtkomst från utanför det virtuella nätverket måste du lägga till ytterligare regler för nätverks säkerhets grupper.

Följande kod visar hur du aktiverar SSH-åtkomst från Internet:

```azurecli
az network nsg rule create -g RESOURCEGROUP --nsg-name hdisecure -n ssh --protocol "*" --source-port-range "*" --destination-port-range "22" --source-address-prefix "*" --destination-address-prefix "VirtualNetwork" --access "Allow" --priority 306 --direction "Inbound"
```

## <a name="example-dns-configuration"></a><a id="example-dns"></a> Exempel: DNS-konfiguration

### <a name="name-resolution-between-a-virtual-network-and-a-connected-on-premises-network"></a>Namn matchning mellan ett virtuellt nätverk och ett anslutet lokalt nätverk

Det här exemplet gör följande antaganden:

* Du har ett Azure-Virtual Network som är anslutet till ett lokalt nätverk med en VPN-gateway.

* Den anpassade DNS-servern i det virtuella nätverket kör Linux eller UNIX som operativ system.

* [BIND](https://www.isc.org/downloads/bind/) är installerat på den anpassade DNS-servern.

På den anpassade DNS-servern i det virtuella nätverket:

1. Använd antingen Azure PowerShell eller Azure CLI för att hitta det virtuella nätverkets DNS-suffix:

    Ersätt `RESOURCEGROUP` med namnet på den resurs grupp som innehåller det virtuella nätverket och ange sedan kommandot:

    ```azurepowershell
    $NICs = Get-AzNetworkInterface -ResourceGroupName "RESOURCEGROUP"
    $NICs[0].DnsSettings.InternalDomainNameSuffix
    ```

    ```azurecli
    az network nic list --resource-group RESOURCEGROUP --query "[0].dnsSettings.internalDomainNameSuffix"
    ```

1. Använd följande text som filens innehåll på den anpassade DNS-servern för det virtuella nätverket `/etc/bind/named.conf.local` :

    ```
    // Forward requests for the virtual network suffix to Azure recursive resolver
    zone "0owcbllr5hze3hxdja3mqlrhhe.ex.internal.cloudapp.net" {
        type forward;
        forwarders {168.63.129.16;}; # Azure recursive resolver
    };
    ```

    Ersätt `0owcbllr5hze3hxdja3mqlrhhe.ex.internal.cloudapp.net` värdet med DNS-suffixet för det virtuella nätverket.

    Den här konfigurationen dirigerar alla DNS-förfrågningar för det virtuella nätverkets DNS-suffix till Azures rekursiva lösare.

1. Använd följande text som filens innehåll på den anpassade DNS-servern för det virtuella nätverket `/etc/bind/named.conf.options` :

    ```
    // Clients to accept requests from
    // TODO: Add the IP range of the joined network to this list
    acl goodclients {
        10.0.0.0/16; # IP address range of the virtual network
        localhost;
        localnets;
    };

    options {
            directory "/var/cache/bind";

            recursion yes;

            allow-query { goodclients; };

            # All other requests are sent to the following
            forwarders {
                192.168.0.1; # Replace with the IP address of your on-premises DNS server
            };

            dnssec-validation auto;

            auth-nxdomain no;    # conform to RFC1035
            listen-on { any; };
    };
    ```
    
    * Ersätt `10.0.0.0/16` värdet med IP-adressintervallet för det virtuella nätverket. Den här posten tillåter att namn matchning begär adresser i det här intervallet.

    * Lägg till IP-adressintervallet för det lokala nätverket i `acl goodclients { ... }` avsnittet.  posten tillåter namn matchnings begär Anden från resurser i det lokala nätverket.
    
    * Ersätt värdet `192.168.0.1` med IP-adressen för din lokala DNS-server. Den här posten dirigerar alla andra DNS-förfrågningar till den lokala DNS-servern.

1. Starta om Bind om du vill använda konfigurationen. Till exempel `sudo service bind9 restart`.

1. Lägg till en villkorlig vidarebefordrare till den lokala DNS-servern. Konfigurera den villkorliga vidarebefordraren att skicka begär Anden för DNS-suffixet från steg 1 till den anpassade DNS-servern.

    > [!NOTE]  
    > Läs dokumentationen för DNS-programvaran om du vill ha mer information om hur du lägger till en villkorlig vidarebefordrare.

När du har slutfört de här stegen kan du ansluta till resurser i antingen nätverket med fullständigt kvalificerade domän namn (FQDN). Nu kan du installera HDInsight i det virtuella nätverket.

### <a name="name-resolution-between-two-connected-virtual-networks"></a>Namn matchning mellan två anslutna virtuella nätverk

Det här exemplet gör följande antaganden:

* Du har två virtuella Azure-nätverk som är anslutna via en VPN-gateway eller peering.

* Den anpassade DNS-servern i båda nätverken kör Linux eller UNIX som operativ system.

* [BIND](https://www.isc.org/downloads/bind/) är installerat på de anpassade DNS-servrarna.

1. Använd antingen Azure PowerShell eller Azure CLI för att hitta DNS-suffixet för båda virtuella nätverken:

    Ersätt `RESOURCEGROUP` med namnet på den resurs grupp som innehåller det virtuella nätverket och ange sedan kommandot:

    ```azurepowershell
    $NICs = Get-AzNetworkInterface -ResourceGroupName "RESOURCEGROUP"
    $NICs[0].DnsSettings.InternalDomainNameSuffix
    ```

    ```azurecli
    az network nic list --resource-group RESOURCEGROUP --query "[0].dnsSettings.internalDomainNameSuffix"
    ```

2. Använd följande text som innehållet i `/etc/bind/named.config.local` filen på den anpassade DNS-servern. Gör den här ändringen på den anpassade DNS-servern i båda de virtuella nätverken.

    ```
    // Forward requests for the virtual network suffix to Azure recursive resolver
    zone "0owcbllr5hze3hxdja3mqlrhhe.ex.internal.cloudapp.net" {
        type forward;
        forwarders {10.0.0.4;}; # The IP address of the DNS server in the other virtual network
    };
    ```

    Ersätt `0owcbllr5hze3hxdja3mqlrhhe.ex.internal.cloudapp.net` värdet med DNS-suffixet för det __andra__ virtuella nätverket. Den här posten dirigerar begär Anden om DNS-suffixet för fjärrnätverket till den anpassade DNS i nätverket.

3. På de anpassade DNS-servrarna i båda virtuella nätverken använder du följande text som `/etc/bind/named.conf.options` filens innehåll:

    ```
    // Clients to accept requests from
    acl goodclients {
        10.1.0.0/16; # The IP address range of one virtual network
        10.0.0.0/16; # The IP address range of the other virtual network
        localhost;
        localnets;
    };

    options {
            directory "/var/cache/bind";

            recursion yes;

            allow-query { goodclients; };

            forwarders {
            168.63.129.16;   # Azure recursive resolver
            };

            dnssec-validation auto;

            auth-nxdomain no;    # conform to RFC1035
            listen-on { any; };
    };
    ```

   Ersätt `10.0.0.0/16` värdena och `10.1.0.0/16` med IP-adressintervall för dina virtuella nätverk. Den här posten gör det möjligt för resurser i varje nätverk att göra förfrågningar till DNS-servrarna.

    Alla begär Anden som inte är för DNS-suffix för de virtuella nätverken (till exempel microsoft.com) hanteras av Azures rekursiva matchare.

4. Starta om Bind om du vill använda konfigurationen. Till exempel `sudo service bind9 restart` på båda DNS-servrarna.

När du har slutfört de här stegen kan du ansluta till resurser i det virtuella nätverket med hjälp av fullständigt kvalificerade domän namn (FQDN). Nu kan du installera HDInsight i det virtuella nätverket.

## <a name="next-steps"></a>Nästa steg

* Ett fullständigt exempel på hur du konfigurerar HDInsight för att ansluta till ett lokalt nätverk finns i [ansluta HDInsight till ett lokalt nätverk](./connect-on-premises-network.md).
* Information om hur du konfigurerar Apache HBase-kluster i virtuella Azure-nätverk finns i [skapa Apache HBase-kluster i HDInsight i Azure Virtual Network](hbase/apache-hbase-provision-vnet.md).
* Information om hur du konfigurerar Apache HBase geo-replikering finns i [Konfigurera Apache HBase Cluster Replication i virtuella Azure-nätverk](hbase/apache-hbase-replication.md).
* Mer information om virtuella Azure-nätverk finns i [Översikt över Azure-Virtual Network](../virtual-network/virtual-networks-overview.md).

* Mer information om nätverks säkerhets grupper finns i [nätverks säkerhets grupper](../virtual-network/network-security-groups-overview.md).

* Mer information om användardefinierade vägar finns i [användardefinierade vägar och IP-vidarebefordring](../virtual-network/virtual-networks-udr-overview.md).
