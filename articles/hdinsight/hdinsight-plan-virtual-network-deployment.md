---
title: Planera ett virtuellt nätverk för Azure HDInsight
description: Lär dig hur du planerar en Azure Virtual Network-distribution för att ansluta HDInsight till andra moln resurser eller resurser i ditt data Center.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,seoapr2020
ms.date: 01/12/2021
ms.openlocfilehash: 429f0b2ffe486279a1c429fc7a153e6528642f54
ms.sourcegitcommit: 431bf5709b433bb12ab1f2e591f1f61f6d87f66c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/12/2021
ms.locfileid: "98131669"
---
# <a name="plan-a-virtual-network-for-azure-hdinsight"></a>Planera ett virtuellt nätverk för Azure HDInsight

Den här artikeln innehåller bakgrunds information om hur du använder [Azure Virtual Networks](../virtual-network/virtual-networks-overview.md) (virtuella nätverk) med Azure HDInsight. Den diskuterar också design-och implementerings beslut som måste göras innan du kan implementera ett virtuellt nätverk för ditt HDInsight-kluster. När planerings fasen är färdig kan du fortsätta med att [skapa virtuella nätverk för Azure HDInsight-kluster](hdinsight-create-virtual-network.md). Mer information om hanterings-IP-adresser för HDInsight som behövs för att konfigurera nätverks säkerhets grupper (NSG: er) och användardefinierade vägar finns i [hanterings-IP-adresser för HDInsight](hdinsight-management-ip-addresses.md).

Med hjälp av en Azure-Virtual Network kan du använda följande scenarier:

* Ansluta till HDInsight direkt från ett lokalt nätverk.
* Ansluta HDInsight till data lager i ett virtuellt Azure-nätverk.
* Direkt åtkomst till Apache Hadoop tjänster som inte är tillgängliga offentligt via Internet. Till exempel Apache Kafka API: er eller Apache HBase Java API.

> [!IMPORTANT]
> När du skapar ett HDInsight-kluster i ett virtuellt nätverk skapas flera nätverks resurser, t. ex. nätverkskort och belastningsutjämnare. Ta **inte** bort de här nätverks resurserna eftersom de behövs för att klustret ska fungera korrekt med det virtuella nätverket.
>
> Efter den 28 feb 2019 kommer nätverks resurserna (t. ex. nätverkskort, pund osv.) för nya HDInsight-kluster som skapats i ett VNET att tillhandahållas i samma HDInsight-kluster resurs grupp. Tidigare etablerades dessa resurser i resurs gruppen VNET. Det finns ingen ändring i de aktuella kluster som körs och de kluster som skapats utan VNET.

## <a name="planning"></a>Planering

Följande är de frågor som du måste besvara när du planerar att installera HDInsight i ett virtuellt nätverk:

* Behöver du installera HDInsight i ett befintligt virtuellt nätverk? Eller skapar du ett nytt nätverk?

    Om du använder ett befintligt virtuellt nätverk kan du behöva ändra nätverks konfigurationen innan du kan installera HDInsight. Mer information finns i avsnittet [Lägg till HDInsight till ett befintligt virtuellt nätverk](#existingvnet) .

* Vill du ansluta det virtuella nätverket som innehåller HDInsight till ett annat virtuellt nätverk eller till ditt lokala nätverk?

    För att enkelt kunna arbeta med resurser i flera nätverk kan du behöva skapa en anpassad DNS och konfigurera DNS-vidarebefordran. Mer information finns i avsnittet [ansluta flera nätverk](#multinet) .

* Vill du begränsa/dirigera inkommande eller utgående trafik till HDInsight?

    HDInsight måste ha obegränsad kommunikation med vissa IP-adresser i Azure Data Center. Det finns också flera portar som måste tillåtas genom brand väggar för klient kommunikation. Mer information finns i [kontrol lera nätverks trafik](./control-network-traffic.md).

## <a name="add-hdinsight-to-an-existing-virtual-network"></a><a id="existingvnet"></a>Lägg till HDInsight i ett befintligt virtuellt nätverk

Använd stegen i det här avsnittet för att upptäcka hur du lägger till en ny HDInsight till en befintlig Azure-Virtual Network.

> [!NOTE]  
> - Det går inte att lägga till ett befintligt HDInsight-kluster i ett virtuellt nätverk.
> - Det virtuella nätverket och det kluster som skapas måste vara i samma prenumeration.

1. Använder du en klassisk distributions modell eller Resource Manager-distribution för det virtuella nätverket?

    HDInsight 3,4 och senare kräver ett virtuellt Resource Manager-nätverk. Tidigare versioner av HDInsight krävde ett klassiskt virtuellt nätverk.

    Om ditt befintliga nätverk är ett klassiskt virtuellt nätverk måste du skapa ett virtuellt Resource Manager-nätverk och sedan ansluta de två. [Ansluta klassiska virtuella nätverk till nya virtuella nätverk](../vpn-gateway/vpn-gateway-connect-different-deployment-models-portal.md).

    När den har anslutits kan HDInsight som installerats i Resource Manager-nätverket interagera med resurser i det klassiska nätverket.

2. Använder du nätverks säkerhets grupper, användardefinierade vägar eller Virtual Network anordningar för att begränsa trafiken till eller från det virtuella nätverket?

    Som en hanterad tjänst kräver HDInsight obegränsad åtkomst till flera IP-adresser i Azure Data Center. Om du vill tillåta kommunikation med de här IP-adresserna uppdaterar du alla befintliga nätverks säkerhets grupper eller användardefinierade vägar.

    HDInsight är värd för flera tjänster, som använder en mängd olika portar. Blockera inte trafik till dessa portar. En lista över portar som tillåts via brand väggar för virtuella installationer finns i avsnittet säkerhet.

    Använd följande Azure PowerShell-eller Azure CLI-kommandon för att hitta din befintliga säkerhets konfiguration:

    * Nätverkssäkerhetsgrupper

        Ersätt `RESOURCEGROUP` med namnet på den resurs grupp som innehåller det virtuella nätverket och ange sedan kommandot:

        ```powershell
        Get-AzNetworkSecurityGroup -ResourceGroupName  "RESOURCEGROUP"
        ```

        ```azurecli
        az network nsg list --resource-group RESOURCEGROUP
        ```

        Mer information finns i avsnittet [Felsöka nätverks säkerhets grupper](../virtual-network/diagnose-network-traffic-filter-problem.md) .

        > [!IMPORTANT]  
        > Regler för nätverks säkerhets grupper tillämpas i ordning utifrån regel prioritet. Den första regeln som matchar trafik mönstret används och inga andra tillämpas för den trafiken. Beställ regler från de flesta tillstånd till minst tillåta. Mer information finns i dokumentet [filtrera nätverks trafik med nätverks säkerhets grupper](../virtual-network/network-security-groups-overview.md) .

    * Användardefinierade vägar

        Ersätt `RESOURCEGROUP` med namnet på den resurs grupp som innehåller det virtuella nätverket och ange sedan kommandot:

        ```powershell
        Get-AzRouteTable -ResourceGroupName "RESOURCEGROUP"
        ```

        ```azurecli
        az network route-table list --resource-group RESOURCEGROUP
        ```

        Mer information finns i [fel sökning av cirkulations](../virtual-network/diagnose-network-routing-problem.md) dokument.

3. Skapa ett HDInsight-kluster och välj Azure-Virtual Network under konfigurationen. Använd stegen i följande dokument för att förstå processen för att skapa kluster:

    * [Create HDInsight using the Azure portal](hdinsight-hadoop-create-linux-clusters-portal.md) (Skapa HDInsight med hjälp av Azure-portalen)
    * [Create HDInsight using Azure PowerShell](hdinsight-hadoop-create-linux-clusters-azure-powershell.md) (Skapa HDInsight med hjälp av Azure PowerShell)
    * [Skapa HDInsight med hjälp av den klassiska Azure CLI](hdinsight-hadoop-create-linux-clusters-azure-cli.md)
    * [Skapa HDInsight med hjälp av en Azure Resource Manager mall](hdinsight-hadoop-create-linux-clusters-arm-templates.md)

   > [!IMPORTANT]  
   > Att lägga till HDInsight i ett virtuellt nätverk är ett valfritt konfigurations steg. Se till att välja det virtuella nätverket när du konfigurerar klustret.

## <a name="connecting-multiple-networks"></a><a id="multinet"></a>Ansluta flera nätverk

Den största utmaningen med en konfiguration för flera nätverk är namn matchning mellan nätverken.

Azure tillhandahåller namn matchning för Azure-tjänster som är installerade i ett virtuellt nätverk. Med den här inbyggda namn matchningen kan HDInsight ansluta till följande resurser genom att använda ett fullständigt kvalificerat domän namn (FQDN):

* Alla resurser som är tillgängliga på Internet. Till exempel microsoft.com, windowsupdate.com.

* Alla resurser som finns i samma Azure-Virtual Network med hjälp av resursens __interna DNS-namn__ . Exempel: när du använder standard namn matchningen är följande exempel på interna DNS-namn som tilldelats till HDInsight Worker-noder:

  * wn0-hdinsi.0owcbllr5hze3hxdja3mqlrhhe.ex.internal.cloudapp.net
  * wn2-hdinsi.0owcbllr5hze3hxdja3mqlrhhe.ex.internal.cloudapp.net

    Båda dessa noder kan kommunicera direkt med varandra, och andra noder i HDInsight, genom att använda interna DNS-namn.

Standard namn matchningen tillåter __inte__ att HDInsight löser namnen på resurser i nätverk som är anslutna till det virtuella nätverket. Det är till exempel vanligt att ansluta ditt lokala nätverk till det virtuella nätverket. Med bara standard namn matchningen kan HDInsight inte komma åt resurser i det lokala nätverket efter namn. Det motsatta är också sant, resurser i det lokala nätverket kan inte komma åt resurser i det virtuella nätverket efter namn.

> [!WARNING]  
> Du måste skapa en anpassad DNS-server och konfigurera det virtuella nätverket så att det används innan du skapar HDInsight-klustret.

Om du vill aktivera namn matchning mellan det virtuella nätverket och resurserna i anslutna nätverk måste du utföra följande åtgärder:

1. Skapa en anpassad DNS-server i Azure-Virtual Network där du planerar att installera HDInsight.

2. Konfigurera det virtuella nätverket så att det använder den anpassade DNS-servern.

3. Hitta det Azure-tilldelade DNS-suffixet för det virtuella nätverket. Det här värdet liknar `0owcbllr5hze3hxdja3mqlrhhe.ex.internal.cloudapp.net` . Information om hur du hittar DNS-suffix finns i avsnittet [exempel: anpassad DNS](hdinsight-create-virtual-network.md#example-dns) .

4. Konfigurera vidarebefordring mellan DNS-servrarna. Konfigurationen beror på typen av fjärrnätverk.

   * Om fjärrnätverket är ett lokalt nätverk konfigurerar du DNS på följande sätt:

     * __Anpassad DNS__ (i det virtuella nätverket):

         * Vidarebefordra begär Anden för DNS-suffixet för det virtuella nätverket till Azures rekursiva matchare (168.63.129.16). Azure hanterar begär Anden om resurser i det virtuella nätverket

         * Vidarebefordra alla andra begär anden till den lokala DNS-servern. Den lokala DNS-tjänsten hanterar alla andra namn matchnings begär Anden, även begär Anden för Internet resurser som Microsoft.com.

     * __Lokalt DNS__: vidarebefordra begär Anden för det virtuella nätverkets DNS-suffix till den anpassade DNS-servern. Den anpassade DNS-servern vidarebefordrar sedan till den rekursiva Azure-matcharen.

       Den här konfigurationen dirigerar begär Anden om fullständigt kvalificerade domän namn som innehåller det virtuella nätverkets DNS-suffix till den anpassade DNS-servern. Alla andra begär Anden (även för offentliga Internet adresser) hanteras av den lokala DNS-servern.

   * Om fjärrnätverket är ett annat Azure-Virtual Network konfigurerar du DNS på följande sätt:

     * __Anpassad DNS__ (i varje virtuellt nätverk):

         * Begär Anden om DNS-suffix för de virtuella nätverken vidarebefordras till de anpassade DNS-servrarna. DNS i varje virtuellt nätverk ansvarar för att lösa resurser i sitt nätverk.

         * Vidarebefordra alla andra förfrågningar till den rekursiva Azure-matcharen. Den rekursiva matcharen ansvarar för att lösa lokala och Internet resurser.

       DNS-servern för varje nätverk vidarebefordrar begär anden till varandra, baserat på DNS-suffix. Andra begär Anden löses med Azures rekursiva lösare.

     Ett exempel på varje konfiguration finns i avsnittet [exempel: anpassad DNS](hdinsight-create-virtual-network.md#example-dns) .

Mer information finns i dokumentet [namn matchning för virtuella datorer och roll instanser](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md) .

## <a name="directly-connect-to-apache-hadoop-services"></a>Anslut direkt till Apache Hadoop tjänster

Du kan ansluta till klustret på `https://CLUSTERNAME.azurehdinsight.net` . Den här adressen använder en offentlig IP-adress, som kanske inte kan kommas åt om du har använt NSG: er för att begränsa inkommande trafik från Internet. När du distribuerar klustret i ett VNet kan du dessutom komma åt det med hjälp av den privata slut punkten `https://CLUSTERNAME-int.azurehdinsight.net` . Den här slut punkten matchar en privat IP-adress i VNet för kluster åtkomst.

Använd följande steg för att ansluta till Apache Ambari och andra webb sidor via det virtuella nätverket:

1. Använd någon av följande metoder för att identifiera interna fullständigt kvalificerade domän namn (FQDN) för HDInsight-klusternoderna:

    Ersätt `RESOURCEGROUP` med namnet på den resurs grupp som innehåller det virtuella nätverket och ange sedan kommandot:

    ```powershell
    $clusterNICs = Get-AzNetworkInterface -ResourceGroupName "RESOURCEGROUP" | where-object {$_.Name -like "*node*"}

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
    az network nic list --resource-group RESOURCEGROUP --output table --query "[?contains(name,'node')].{NICname:name,InternalIP:ipConfigurations[0].privateIpAddress,InternalFQDN:dnsSettings.internalFqdn}"
    ```

    I listan över noder som returneras hittar du FQDN för huvudnoderna och använder FQDN: er för att ansluta till Ambari och andra webb tjänster. Använd `http://<headnode-fqdn>:8080` till exempel för att komma åt Ambari.

    > [!IMPORTANT]  
    > Vissa tjänster som finns på huvudnoderna är bara aktiva på en nod i taget. Om du försöker komma åt en tjänst på en head-nod och den returnerar ett 404-fel växlar du till den andra Head-noden.

2. Information om vilka noder och portar som en tjänst är tillgänglig på finns i [portarna som används av Hadoop-tjänster i HDInsight](./hdinsight-hadoop-port-settings-for-services.md) -dokument.

## <a name="load-balancing"></a>Belastningsutjämning

När du skapar ett HDInsight-kluster skapas även en belastnings utjämning. Den här belastningsutjämnaren är på den [grundläggande SKU-nivån](../load-balancer/skus.md), som har vissa begränsningar. Ett av de här begränsningarna är att om du har två virtuella nätverk i olika regioner kan du inte ansluta till grundläggande belastnings utjämning. Mer information finns i [vanliga frågor och svar om virtuella nätverk: begränsningar för global VNet-peering](../virtual-network/virtual-networks-faq.md#what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers).

## <a name="next-steps"></a>Nästa steg

* Kod exempel och exempel på hur du skapar virtuella Azure-nätverk finns i [skapa virtuella nätverk för Azure HDInsight-kluster](hdinsight-create-virtual-network.md).
* Ett slut punkt till slut punkts exempel på hur du konfigurerar HDInsight för att ansluta till ett lokalt nätverk finns i [ansluta HDInsight till ett lokalt nätverk](./connect-on-premises-network.md).
* Mer information om virtuella Azure-nätverk finns i [Översikt över Azure-Virtual Network](../virtual-network/virtual-networks-overview.md).
* Mer information om nätverks säkerhets grupper finns i [nätverks säkerhets grupper](../virtual-network/network-security-groups-overview.md).
* Mer information om användardefinierade vägar finns i [användardefinierade vägar och IP-vidarebefordring](../virtual-network/virtual-networks-udr-overview.md).
* Mer information om hur du styr trafik inklusive brand Väggs integrering finns i [kontrol lera nätverks trafik](./control-network-traffic.md).
