---
title: Anslut Azure HDInsight till ditt lokala nätverk
description: Lär dig hur du skapar ett HDInsight-kluster i en Azure-Virtual Network och sedan ansluter det till ditt lokala nätverk. Lär dig hur du konfigurerar namn matchning mellan HDInsight och ditt lokala nätverk med hjälp av en anpassad DNS-server.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 03/04/2020
ms.openlocfilehash: f26813176d4286a052772d2096427231759aacc2
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/23/2021
ms.locfileid: "104863385"
---
# <a name="connect-hdinsight-to-your-on-premises-network"></a>Ansluta HDInsight till det lokala nätverket

Lär dig hur du ansluter HDInsight till ditt lokala nätverk med hjälp av virtuella Azure-nätverk och en VPN-gateway. Det här dokumentet innehåller planerings information om:

* Använda HDInsight i en Azure-Virtual Network som ansluter till ditt lokala nätverk.
* Konfigurera DNS-namnmatchning mellan det virtuella nätverket och det lokala nätverket.
* Konfigurera nätverks säkerhets grupper för att begränsa Internet åtkomst till HDInsight.
* Portar som tillhandahålls av HDInsight på det virtuella nätverket.

## <a name="overview"></a>Översikt

Om du vill tillåta att HDInsight och resurser i det anslutna nätverket kommunicerar med namn måste du utföra följande åtgärder:

1. Skapa Azure-Virtual Network.
1. Skapa en anpassad DNS-server i Azure-Virtual Network.
1. Konfigurera det virtuella nätverket så att det använder den anpassade DNS-servern i stället för standard Azure-rekursiva matcharen.
1. Konfigurera vidarebefordring mellan den anpassade DNS-servern och den lokala DNS-servern.

Dessa konfigurationer gör följande:

* Begär Anden om fullständigt kvalificerade domän namn som har DNS-suffixet __för det virtuella nätverket__ vidarebefordras till den anpassade DNS-servern. Den anpassade DNS-servern vidarebefordrar sedan dessa förfrågningar till Azures rekursiva matchare, som returnerar IP-adressen.
* Alla andra begär Anden vidarebefordras till den lokala DNS-servern. Även begär Anden för offentliga Internet resurser, till exempel microsoft.com, vidarebefordras till den lokala DNS-servern för namn matchning.

I följande diagram är gröna rader begär Anden om resurser som slutar med DNS-suffixet för det virtuella nätverket. Blå rader är begär Anden för resurser i det lokala nätverket eller på det offentliga Internet.

:::image type="content" source="./media/connect-on-premises-network/on-premises-to-cloud-dns.png" alt-text="Diagram över hur DNS-begäranden löses i konfigurationen" border="false":::

## <a name="prerequisites"></a>Förutsättningar

* En SSH-klient. Mer information finns i [Ansluta till HDInsight (Apache Hadoop) med hjälp av SSH](./hdinsight-hadoop-linux-use-ssh-unix.md).
* Om du använder PowerShell behöver du AZ- [modulen](/powershell/azure/).
* Om du vill använda Azure CLI och du ännu inte har installerat det kan du läsa [Installera Azure CLI](/cli/azure/install-azure-cli).

## <a name="create-virtual-network-configuration"></a>Skapa konfiguration av virtuellt nätverk

Använd följande dokument för att lära dig hur du skapar en Azure-Virtual Network som är ansluten till ditt lokala nätverk:

* [Använda Azure Portal](../vpn-gateway/tutorial-site-to-site-portal.md)
* [Använda Azure PowerShell](../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md)
* [Använda Azure CLI](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-cli.md)

## <a name="create-custom-dns-server"></a>Skapa anpassad DNS-Server

> [!IMPORTANT]  
> Du måste skapa och konfigurera DNS-servern innan du installerar HDInsight i det virtuella nätverket.

De här stegen använder [Azure Portal](https://portal.azure.com) för att skapa en virtuell Azure-dator. Andra sätt att skapa en virtuell dator finns i [skapa VM – Azure CLI](../virtual-machines/linux/quick-create-cli.md) och [skapa VM-Azure PowerShell](../virtual-machines/linux/quick-create-powershell.md).  Använd följande steg för att skapa en virtuell Linux-dator som använder DNS-programvaran [BIND](https://www.isc.org/downloads/bind/) :

1. Logga in på [Azure-portalen](https://portal.azure.com).
  
1. Välj **+ skapa en resurs** på den översta menyn.

    :::image type="content" source="./media/connect-on-premises-network/azure-portal-create-resource.png" alt-text="Skapa en virtuell Ubuntu-dator":::

1. Välj den **beräknade**  >  **virtuella** datorn för att gå till sidan **skapa en virtuell dator** .

1. På fliken __grundläggande__ anger du följande information:  
  
    | Fält | Värde |
    | --- | --- |
    |Prenumeration |Välj lämplig prenumeration.|
    |Resursgrupp |Välj den resurs grupp som innehåller det virtuella nätverket som skapades tidigare.|
    |Namn på virtuell dator | Ange ett eget namn som identifierar den här virtuella datorn. I det här exemplet används **DNSProxy**.|
    |Region | Välj samma region som det virtuella nätverket som skapades tidigare.  Alla VM-storlekar är inte tillgängliga i alla regioner.  |
    |Alternativ för tillgänglighet |  Välj önskad tillgänglighets nivå.  Azure erbjuder en rad alternativ för att hantera tillgänglighet och återhämtning för dina program.  Skapa din lösning för att använda replikerade virtuella datorer i Tillgänglighetszoner eller tillgänglighets uppsättningar för att skydda dina appar och data från data Center avbrott och underhålls händelser. I det här exemplet används **ingen infrastrukturs-redundans krävs**. |
    |Bild | Lämna på **Ubuntu Server 18,04 LTS**. |
    |Autentiseringstyp | __Lösen ord__ eller __Offentlig SSH-nyckel__: autentiseringsmetoden för SSH-kontot. Vi rekommenderar att du använder offentliga nycklar eftersom de är säkrare. I det här exemplet används **lösen ord**.  Mer information finns i dokumentet [skapa och använda SSH-nycklar för virtuella Linux-datorer](../virtual-machines/linux/mac-create-ssh-keys.md) .|
    |Användarnamn |Ange administratörs användar namnet för den virtuella datorn.  I det här exemplet används **sshuser**.|
    |Lösen ord eller offentlig SSH-nyckel | Det tillgängliga fältet avgörs av ditt val av **Autentiseringstyp**.  Ange lämpligt värde.|
    |Offentliga inkommande portar|Välj **Tillåt valda portar**. Välj sedan **SSH (22)** i list rutan **Välj inkommande portar** .|

    :::image type="content" source="./media/connect-on-premises-network/virtual-machine-basics.png" alt-text="Grundläggande konfiguration av virtuell dator":::

    Lämna andra poster med standardvärdena och välj sedan fliken **nätverk** .

4. Ange följande information på fliken **nätverk** :

    | Fält | Värde |
    | --- | --- |
    |Virtuellt nätverk | Välj det virtuella nätverk som du skapade tidigare.|
    |Undernät | Välj standard under nätet för det virtuella nätverk som du skapade tidigare. Välj __inte__ det undernät som används av VPN-gatewayen.|
    |Offentlig IP-adress | Använd det automatiskt ifyllda värdet.  |

    :::image type="content" source="./media/connect-on-premises-network/virtual-network-settings.png" alt-text="Inställningar för virtuella HDInsight-nätverk":::

    Lämna övriga poster till standardvärdena och välj sedan **Granska + skapa**.

5. På fliken **Granska och skapa** väljer du **skapa** för att skapa den virtuella datorn.

### <a name="review-ip-addresses"></a>Granska IP-adresser

När den virtuella datorn har skapats får du ett meddelande om att **distributionen har slutförts** med knappen **gå till resurs** .  Välj **gå till resurs** för att gå till den nya virtuella datorn.  Följ de här stegen för att identifiera associerade IP-adresser från standardvyn för den nya virtuella datorn:

1. Från **Inställningar** väljer du **Egenskaper**.

2. Observera värdena för **offentlig IP-adress/DNS-namn** och **privat IP-adress** för senare användning.

   :::image type="content" source="./media/connect-on-premises-network/virtual-machine-ip-addresses.png" alt-text="Offentliga och privata IP-adresser":::

### <a name="install-and-configure-bind-dns-software"></a>Installera och konfigurera BIND (DNS-programvara)

1. Använd SSH för att ansluta till den __offentliga IP-adressen__ för den virtuella datorn. Ersätt `sshuser` med det SSH-användarkonto du angav när du skapade den virtuella datorn. I följande exempel ansluter du till en virtuell dator på 40.68.254.142:

    ```bash
    ssh sshuser@40.68.254.142
    ```

2. Om du vill installera bind använder du följande kommandon från SSH-sessionen:

    ```bash
    sudo apt-get update -y
    sudo apt-get install bind9 -y
    ```

3. Om du vill konfigurera bindning till vidarebefordran av namn matchnings begär anden till din lokala DNS-Server använder du följande text som `/etc/bind/named.conf.options` filens innehåll:

    ```DNS Zone file
    acl goodclients {
        10.0.0.0/16; # Replace with the IP address range of the virtual network
        10.1.0.0/16; # Replace with the IP address range of the on-premises network
        localhost;
        localnets;
    };

    options {
            directory "/var/cache/bind";

            recursion yes;

            allow-query { goodclients; };

            forwarders {
            192.168.0.1; # Replace with the IP address of the on-premises DNS server
            };

            dnssec-validation auto;

            auth-nxdomain no;    # conform to RFC1035
            listen-on { any; };
    };
    ```

    > [!IMPORTANT]  
    > Ersätt värdena i `goodclients` avsnittet med IP-adressintervallet för det virtuella nätverket och det lokala nätverket. Det här avsnittet definierar de adresser som den här DNS-servern accepterar begär Anden från.
    >
    > Ersätt `192.168.0.1` posten i `forwarders` avsnittet med IP-adressen för din lokala DNS-server. Den här posten dirigerar DNS-begäranden till din lokala DNS-server för lösning.

    Använd följande kommando om du vill redigera filen:

    ```bash
    sudo nano /etc/bind/named.conf.options
    ```

    Om du vill spara filen använder du __CTRL + X__, __Y__ och __anger__ sedan.

4. Från SSH-sessionen använder du följande kommando:

    ```bash
    hostname -f
    ```

    Det här kommandot returnerar ett värde som liknar följande text:

    ```output
    dnsproxy.icb0d0thtw0ebifqt0g1jycdxd.ex.internal.cloudapp.net
    ```

    `icb0d0thtw0ebifqt0g1jycdxd.ex.internal.cloudapp.net`Texten är __DNS-suffixet__ för det här virtuella nätverket. Spara det här värdet eftersom det används senare.

5. Om du vill konfigurera bind för att matcha DNS-namn för resurser i det virtuella nätverket, använder du följande text som `/etc/bind/named.conf.local` filens innehåll:

    ```DNS Zone file
    // Replace the following with the DNS suffix for your virtual network
    zone "icb0d0thtw0ebifqt0g1jycdxd.ex.internal.cloudapp.net" {
        type forward;
        forwarders {168.63.129.16;}; # The Azure recursive resolver
    };
    ```

    > [!IMPORTANT]  
    > Du måste ersätta `icb0d0thtw0ebifqt0g1jycdxd.ex.internal.cloudapp.net` med det DNS-suffix som du hämtade tidigare.

    Använd följande kommando om du vill redigera filen:

    ```bash
    sudo nano /etc/bind/named.conf.local
    ```

    Om du vill spara filen använder du __CTRL + X__, __Y__ och __anger__ sedan.

6. Använd följande kommando för att starta bind:

    ```bash
    sudo service bind9 restart
    ```

7. Om du vill kontrol lera att bindningen kan matcha namnen på resurser i ditt lokala nätverk använder du följande kommandon:

    ```bash
    sudo apt install dnsutils
    nslookup dns.mynetwork.net 10.0.0.4
    ```

    > [!IMPORTANT]  
    > Ersätt `dns.mynetwork.net` med det fullständigt kvalificerade domän namnet (FQDN) för en resurs i det lokala nätverket.
    >
    > Ersätt `10.0.0.4` med den __interna IP-adressen__ för din anpassade DNS-server i det virtuella nätverket.

    Svaret ser ut ungefär som i följande text:

    ```output
    Server:         10.0.0.4
    Address:        10.0.0.4#53

    Non-authoritative answer:
    Name:   dns.mynetwork.net
    Address: 192.168.0.4
    ```

## <a name="configure-virtual-network-to-use-the-custom-dns-server"></a>Konfigurera ett virtuellt nätverk för att använda den anpassade DNS-servern

Om du vill konfigurera det virtuella nätverket att använda den anpassade DNS-servern i stället för Azures rekursiva matchare, använder du följande steg från [Azure Portal](https://portal.azure.com):

1. I den vänstra menyn navigerar du till **alla tjänster**  >  **nätverk**  >  **virtuella nätverk**.

2. Välj ditt virtuella nätverk i listan, så öppnas standardvyn för det virtuella nätverket.  

3. I standardvyn väljer du **DNS-servrar** under **Inställningar**.  

4. Välj __anpassad__ och ange den **privata IP-adressen** för den anpassade DNS-servern.

5. Välj __Spara__.  <br />  

    :::image type="content" source="./media/connect-on-premises-network/configure-custom-dns.png" alt-text="Ange den anpassade DNS-servern för nätverket":::

## <a name="configure-on-premises-dns-server"></a>Konfigurera lokal DNS-Server

I föregående avsnitt konfigurerade du den anpassade DNS-servern så att den vidarebefordrar begär anden till den lokala DNS-servern. Sedan måste du konfigurera den lokala DNS-servern så att den vidarebefordrar begär anden till den anpassade DNS-servern.

Mer information om hur du konfigurerar DNS-servern finns i dokumentationen för din DNS-serverprogram vara. Leta efter de steg som krävs för att konfigurera en __villkorlig vidarebefordrare__.

En villkorlig vidarebefordran vidarebefordrar bara begär Anden för ett bestämt DNS-suffix. I så fall måste du konfigurera en vidarebefordrare för det virtuella nätverkets DNS-suffix. Begär Anden för det här suffixet ska vidarebefordras till den anpassade DNS-serverns IP-adress. 

Följande text är ett exempel på en villkorlig vidarebefordrare-konfiguration för **BIND** DNS-programvaran:

```DNS Zone file
zone "icb0d0thtw0ebifqt0g1jycdxd.ex.internal.cloudapp.net" {
    type forward;
    forwarders {10.0.0.4;}; # The custom DNS server's internal IP address
};
```

Information om hur du använder DNS på **Windows Server 2016** finns i dokumentationen för [Add-DnsServerConditionalForwarderZone](/powershell/module/dnsserver/add-dnsserverconditionalforwarderzone) ...

När du har konfigurerat den lokala DNS-servern kan du använda `nslookup` från det lokala nätverket för att kontrol lera att du kan matcha namn i det virtuella nätverket. Följande exempel 

```bash
nslookup dnsproxy.icb0d0thtw0ebifqt0g1jycdxd.ex.internal.cloudapp.net 196.168.0.4
```

I det här exemplet används den lokala DNS-servern på 196.168.0.4 för att matcha namnet på den anpassade DNS-servern. Ersätt IP-adressen med en för den lokala DNS-servern. Ersätt `dnsproxy` adressen med det fullständigt kvalificerade domän namnet för den anpassade DNS-servern.

## <a name="optional-control-network-traffic"></a>Valfritt: kontrol lera nätverks trafik

Du kan använda nätverks säkerhets grupper (NSG) eller användardefinierade vägar (UDR) för att kontrol lera nätverks trafiken. Med NSG: er kan du filtrera inkommande och utgående trafik, samt tillåta eller neka trafiken. Med UDR kan du styra hur trafiken flödar mellan resurser i det virtuella nätverket, Internet och det lokala nätverket.

> [!WARNING]  
> HDInsight kräver inkommande åtkomst från vissa IP-adresser i Azure-molnet och obegränsad utgående åtkomst. När du använder NSG: er eller UDR för att styra trafiken måste du utföra följande steg:

1. Hitta IP-adresserna för den plats som innehåller det virtuella nätverket. En lista över de IP-adresser som krävs efter plats finns i de [obligatoriska IP-adresserna](./hdinsight-management-ip-addresses.md).

2. För IP-adresserna som identifierades i steg 1, Tillåt inkommande trafik från de IP-adresserna.

   * Om du använder __NSG__: Tillåt __inkommande__ trafik på port __443__ för IP-adresserna.
   * Om du använder __UDR__: ange __nästa hopp__ typ för vägen till __Internet__ för IP-adresserna.

Ett exempel på hur du använder Azure PowerShell eller Azure CLI för att skapa NSG: er finns i dokumentet [utöka HDInsight med Azure Virtual Networks](hdinsight-create-virtual-network.md#hdinsight-nsg) .

## <a name="create-the-hdinsight-cluster"></a>Skapa HDInsight-klustret

> [!WARNING]  
> Du måste konfigurera den anpassade DNS-servern innan du installerar HDInsight i det virtuella nätverket.

Använd stegen i [skapa ett HDInsight-kluster med hjälp av Azure Portal](./hdinsight-hadoop-create-linux-clusters-portal.md) -dokumentet för att skapa ett HDInsight-kluster.

> [!WARNING]  
> * När klustret skapas måste du välja den plats som innehåller det virtuella nätverket.
> * I delen __Avancerade inställningar__ i konfigurationen måste du välja det virtuella nätverk och undernät som du skapade tidigare.

## <a name="connecting-to-hdinsight"></a>Ansluta till HDInsight

De flesta dokumentation i HDInsight förutsätter att du har åtkomst till klustret via Internet. Till exempel att du kan ansluta till klustret i `https://CLUSTERNAME.azurehdinsight.net`. Den här adressen använder den offentliga gatewayen, som inte är tillgänglig om du har använt NSG: er eller UDR för att begränsa åtkomsten från Internet.

Vissa dokumentations referenser är också `headnodehost` när du ansluter till klustret från en SSH-session. Den här adressen är bara tillgänglig från noder i ett kluster och kan inte användas på klienter som är anslutna via det virtuella nätverket.

Använd följande steg för att ansluta direkt till HDInsight via det virtuella nätverket:

1. Använd någon av följande metoder för att identifiera de interna fullständigt kvalificerade domän namnen för HDInsight-klusternoderna:

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

2. Information om vilka portar som en tjänst är tillgänglig på finns i [portarna som används av Apache Hadoop Services i HDInsight](./hdinsight-hadoop-port-settings-for-services.md) -dokument.

    > [!IMPORTANT]  
    > Vissa tjänster som finns på huvudnoderna är bara aktiva på en nod i taget. Om du försöker komma åt en tjänst på en huvudnod och den Miss lyckas växlar du till den andra Head-noden.
    >
    > Apache Ambari är till exempel bara aktiv på en head-nod i taget. Om du försöker komma åt Ambari på en head-nod och den returnerar ett 404-fel, körs den på den andra Head-noden.

## <a name="next-steps"></a>Nästa steg

* Mer information om hur du använder HDInsight i ett virtuellt nätverk finns i [Planera en virtuell nätverks distribution för Azure HDInsight-kluster](./hdinsight-plan-virtual-network-deployment.md).

* Mer information om virtuella Azure-nätverk finns i [Översikt över Azure-Virtual Network](../virtual-network/virtual-networks-overview.md).

* Mer information om nätverks säkerhets grupper finns i [nätverks säkerhets grupper](../virtual-network/network-security-groups-overview.md).

* Mer information om användardefinierade vägar finns i [användardefinierade vägar och IP-vidarebefordring](../virtual-network/virtual-networks-udr-overview.md).