---
title: HBase kluster replikering i virtuella nätverk – Azure HDInsight
description: Lär dig hur du konfigurerar HBase-replikering från en HDInsight-version till en annan för belastnings utjämning, hög tillgänglighet, migrering av noll-nedtid och uppdateringar samt haveri beredskap.
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: how-to
ms.date: 12/06/2019
ms.openlocfilehash: cfcb3a5a601afadb9f3fcd71c24e18a9d7f27b9e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98946419"
---
# <a name="set-up-apache-hbase-cluster-replication-in-azure-virtual-networks"></a>Konfigurera Apache HBase Cluster Replication i Azure Virtual Networks

Lär dig hur du konfigurerar [Apache HBase](https://hbase.apache.org/) -replikering i ett virtuellt nätverk, eller mellan två virtuella nätverk i Azure.

Kluster replikeringen använder en metod för käll-push. Ett HBase-kluster kan vara en källa eller ett mål, eller så kan det uppfylla båda rollerna samtidigt. Replikeringen är asynkron. Målet för replikering är en eventuell konsekvens. När källan får en redigering till en kolumn serie när replikering är aktive rad, sprids redigeringen till alla mål kluster. När data replikeras från ett kluster till ett annat spåras käll klustret och alla kluster som redan har förbrukat data, för att förhindra replikeringskonflikter.

I den här artikeln ställer du in en replikering av käll-mål. För andra kluster topologier, se [Apache HBase Reference Guide](https://hbase.apache.org/book.html#_cluster_replication).

Följande är HBase för användning av replikering för ett enda virtuellt nätverk:

* Belastningsbalansering. Du kan till exempel köra genomsökningar eller MapReduce-jobb i mål klustret och mata in data i käll klustret.
* Lägger till hög tillgänglighet.
* Migrera data från ett HBase-kluster till ett annat.
* Uppgradera ett Azure HDInsight-kluster från en version till en annan.

Följande är HBase-användnings fall för replikering för två virtuella nätverk:

* Konfigurera katastrof återställning.
* Belastnings utjämning och partitionering av programmet.
* Lägger till hög tillgänglighet.

Du kan replikera kluster genom att använda skript [Åtgärds](../hdinsight-hadoop-customize-cluster-linux.md) skript från [GitHub](https://github.com/Azure/hbase-utils/tree/master/replication).

## <a name="prerequisites"></a>Förutsättningar
Innan du börjar den här artikeln måste du ha en Azure-prenumeration. Se [Hämta en kostnads fri utvärderings version av Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

## <a name="set-up-the-environments"></a>Konfigurera miljöer

Du har tre konfigurations alternativ:

- Två Apache HBase-kluster i ett virtuellt Azure-nätverk.
- Två Apache HBase-kluster i två olika virtuella nätverk i samma region.
- Två Apache HBase-kluster i två olika virtuella nätverk i två olika regioner (geo-replikering).

Den här artikeln beskriver scenariot för geo-replikering.

Vi har skapat några [Azure Resource Manager mallar](../../azure-resource-manager/management/overview.md)för att hjälpa dig att konfigurera miljöer. Om du föredrar att konfigurera miljöer med hjälp av andra metoder, se:

- [Skapa Apache Hadoop kluster i HDInsight](../hdinsight-hadoop-provision-linux-clusters.md)
- [Skapa Apache HBase-kluster i Azure Virtual Network](apache-hbase-provision-vnet.md)

### <a name="set-up-two-virtual-networks-in-two-different-regions"></a>Konfigurera två virtuella nätverk i två olika regioner

Om du vill använda en mall som skapar två virtuella nätverk i två olika regioner och VPN-anslutningen mellan virtuella nätverk väljer du knappen för **att distribuera till Azure** . Definitionen av mallen lagras i en [offentlig blob-lagring](https://hditutorialdata.blob.core.windows.net/hbaseha/azuredeploy.json).

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Fhbaseha%2Fazuredeploy.json" target="_blank"><img src="./media/apache-hbase-replication/hdi-deploy-to-azure1.png" alt="Deploy to Azure button for new cluster"></a>

Några hårdkodade värden i mallen:

**VNet 1**

| Egenskap | Värde |
|----------|-------|
| Location | USA, västra |
| VNet-namn | &lt;ClusterNamePrevix>-vnet1 |
| Prefix för adress utrymme | 10.1.0.0/16 |
| Namn på undernät | undernät 1 |
| Undernätsprefixet | 10.1.0.0/24 |
| Namn på undernät (Gateway) | GatewaySubnet (kan inte ändras) |
| Prefix för undernät (Gateway) | 10.1.255.0/27 |
| Gateway-namn | vnet1gw |
| Gateway-typ | Vpn |
| Gateway VPN-typ | Routningsbaserad |
| Gateway-SKU | Grundläggande |
| Gateway-IP | vnet1gwip |

**VNet 2**

| Egenskap | Värde |
|----------|-------|
| Plats | USA, östra |
| VNet-namn | &lt;ClusterNamePrevix>-vnet2 |
| Prefix för adress utrymme | 10.2.0.0/16 |
| Namn på undernät | undernät 1 |
| Undernätsprefixet | 10.2.0.0/24 |
| Namn på undernät (Gateway) | GatewaySubnet (kan inte ändras) |
| Prefix för undernät (Gateway) | 10.2.255.0/27 |
| Gateway-namn | vnet2gw |
| Gateway-typ | Vpn |
| Gateway VPN-typ | Routningsbaserad |
| Gateway-SKU | Grundläggande |
| Gateway-IP | vnet1gwip |

## <a name="setup-dns"></a>Konfigurera DNS

I det sista avsnittet skapar mallen en virtuell Ubuntu-dator i vart och ett av de två virtuella nätverken.  I det här avsnittet ska du installera Bind på de två virtuella DNS-datorerna och sedan konfigurera DNS-vidarebefordran på de två virtuella datorerna.

För att kunna installera bind måste Yon hitta den offentliga IP-adressen för de två virtuella DNS-datorerna.

1. Öppna [Azure-portalen](https://portal.azure.com).
2. Öppna den virtuella DNS-datorn genom att välja **resurs grupper > [resurs gruppens namn] > [vnet1DNS]**.  Resurs gruppens namn är det som du skapar i den senaste proceduren. Standard namnen för virtuella DNS-datorer är *vnet1DNS* och *vnet2NDS*.
3. Välj **Egenskaper** för att öppna egenskaps sidan för det virtuella nätverket.
4. Skriv ned den **offentliga IP-adressen** och kontrol lera också den **privata IP-adressen**.  Den privata IP-adressen är **10.1.0.4** för vnet1DNS och **10.2.0.4** för vnet2DNS.  
5. Ändra DNS-servrarna för båda virtuella nätverken så att de använder standard-(Azure-tillhandahållna) DNS-servrar för att tillåta inkommande och utgående åtkomst att ladda ned paket för att installera bind i följande steg.

Använd följande procedur för att installera bind:

1. Använd SSH för att ansluta till den __offentliga IP-adressen__ för den virtuella DNS-datorn. I följande exempel ansluter du till en virtuell dator på 40.68.254.142:

    ```bash
    ssh sshuser@40.68.254.142
    ```

    Ersätt `sshuser` med det SSH-användarkonto du angav när du skapade den virtuella DNS-datorn.

    > [!NOTE]  
    > Det finns flera olika sätt att hämta `ssh` verktyget. På Linux, UNIX och macOS tillhandahålls det som en del av operativ systemet. Om du använder Windows kan du överväga något av följande alternativ:
    >
    > * [Azure Cloud Shell](../../cloud-shell/quickstart.md)
    > * [Bash på Ubuntu i Windows 10](/windows/wsl/about)
    > * [Githttps://git-scm.com/)](https://git-scm.com/)
    > * [OpenSSHhttps://github.com/PowerShell/Win32-OpenSSH/wiki/Install-Win32-OpenSSH)](https://github.com/PowerShell/Win32-OpenSSH/wiki/Install-Win32-OpenSSH)

2. Om du vill installera bind använder du följande kommandon från SSH-sessionen:

    ```bash
    sudo apt-get update -y
    sudo apt-get install bind9 -y
    ```

3. Konfigurera bindning för att vidarebefordra namn matchnings begär anden till din lokala DNS-server. Det gör du genom att använda följande text som `/etc/bind/named.conf.options` filens innehåll:

    ```
    acl goodclients {
        10.1.0.0/16; # Replace with the IP address range of the virtual network 1
        10.2.0.0/16; # Replace with the IP address range of the virtual network 2
        localhost;
        localhost;
    };
    
    options {
        directory "/var/cache/bind";
        recursion yes;
        allow-query { goodclients; };

        forwarders {
            168.63.129.16; #This is the Azure DNS server
        };

        dnssec-validation auto;

        auth-nxdomain no;    # conform to RFC1035
        listen-on-v6 { any; };
    };
    ```
    
    > [!IMPORTANT]  
    > Ersätt värdena i `goodclients` avsnittet med IP-adressintervallet för de två virtuella nätverken. Det här avsnittet definierar de adresser som den här DNS-servern accepterar begär Anden från.

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
    vnet1DNS.icb0d0thtw0ebifqt0g1jycdxd.ex.internal.cloudapp.net
    ```

    `icb0d0thtw0ebifqt0g1jycdxd.ex.internal.cloudapp.net`Texten är __DNS-suffixet__ för det här virtuella nätverket. Du bör spara det här värdet eftersom det används senare.

    Du måste också ta reda på DNS-suffixet från den andra DNS-servern. Du behöver det i nästa steg.

5. Om du vill konfigurera bind för att matcha DNS-namn för resurser i det virtuella nätverket, använder du följande text som `/etc/bind/named.conf.local` filens innehåll:

    ```
    // Replace the following with the DNS suffix for your virtual network
    zone "v5ant3az2hbe1edzthhvwwkcse.bx.internal.cloudapp.net" {
            type forward;
            forwarders {10.2.0.4;}; # The Azure recursive resolver
    };
    ```

    > [!IMPORTANT]  
    > Du måste ersätta `v5ant3az2hbe1edzthhvwwkcse.bx.internal.cloudapp.net` med DNS-suffixet för det andra virtuella nätverket. Och IP-adressen vidarebefordrare är den privata IP-adressen för DNS-servern i det andra virtuella nätverket.

    Använd följande kommando om du vill redigera filen:

    ```bash
    sudo nano /etc/bind/named.conf.local
    ```

    Om du vill spara filen använder du __CTRL + X__, __Y__ och __anger__ sedan.

6. Använd följande kommando för att starta bind:

    ```bash
    sudo service bind9 restart
    ```

7. Om du vill kontrol lera att bindningen kan matcha namnen på resurser i det andra virtuella nätverket använder du följande kommandon:

    ```bash
    sudo apt install dnsutils
    nslookup vnet2dns.v5ant3az2hbe1edzthhvwwkcse.bx.internal.cloudapp.net
    ```

    > [!IMPORTANT]  
    > Ersätt `vnet2dns.v5ant3az2hbe1edzthhvwwkcse.bx.internal.cloudapp.net` med det fullständigt kvalificerade domän namnet (FQDN) för den virtuella DNS-datorn i det andra nätverket.
    >
    > Ersätt `10.2.0.4` med den __interna IP-adressen__ för din anpassade DNS-server i det andra virtuella nätverket.

    Svaret ser ut ungefär som i följande text:

    ```output
    Server:         10.2.0.4
    Address:        10.2.0.4#53
    
    Non-authoritative answer:
    Name:   vnet2dns.v5ant3az2hbe1edzthhvwwkcse.bx.internal.cloudapp.net
    Address: 10.2.0.4
    ```

    Fram till nu kan du inte leta upp IP-adressen från det andra nätverket utan den angivna IP-adressen för DNS-servern.

### <a name="configure-the-virtual-network-to-use-the-custom-dns-server"></a>Konfigurera det virtuella nätverket så att det använder den anpassade DNS-servern

Använd följande steg om du vill konfigurera det virtuella nätverket så att det använder den anpassade DNS-servern i stället för den rekursiva Azure-matcharen:

1. I [Azure Portal](https://portal.azure.com)väljer du det virtuella nätverket och väljer sedan __DNS-servrar__.

2. Välj __anpassad__ och ange den __interna IP-adressen__ för den anpassade DNS-servern. Välj slutligen __Spara__.

6. Öppna den virtuella datorn för DNS-servern i vnet1 och klicka på **starta om**.  Du måste starta om alla virtuella datorer i det virtuella nätverket för att DNS-konfigurationen ska börja gälla.
7. Upprepa steg för att konfigurera den anpassade DNS-servern för vnet2.

Om du vill testa DNS-konfigurationen kan du ansluta till de två virtuella DNS-datorerna med SSH och pinga DNS-servern för det andra virtuella nätverket med hjälp av dess värdnamn. Om det inte fungerar använder du följande kommando för att kontrol lera DNS-status:

```bash
sudo service bind9 status
```

## <a name="create-apache-hbase-clusters"></a>Skapa Apache HBase-kluster

Skapa ett [Apache HBase](https://hbase.apache.org/) -kluster i vart och ett av de två virtuella nätverken med följande konfiguration:

- **Resurs grupp namn**: Använd samma resurs grupp namn som du skapade de virtuella nätverken.
- **Kluster typ**: HBase
- **Version**: HBase 1.1.2 (HDI 3,6)
- **Plats**: Använd samma plats som det virtuella nätverket.  Som standard är vnet1 *västra USA*, och Vnet2 är *östra USA*.
- **Lagring**: skapa ett nytt lagrings konto för klustret.
- **Virtuellt nätverk** (från avancerade inställningar på portalen): Välj vnet1 som du skapade i den senaste proceduren.
- **Undernät**: standard namnet som används i mallen är **subnet1**.

För att säkerställa att miljön är korrekt konfigurerad måste du kunna pinga huvudnoden-FQDN mellan de två klustren.

## <a name="load-test-data"></a>Läsa in testdata

När du replikerar ett kluster måste du ange de tabeller som du vill replikera. I det här avsnittet läser du in data i käll klustret. I nästa avsnitt kommer du att aktivera replikering mellan de två klustren.

Om du vill skapa en **kontakt** tabell och infoga data i tabellen, följer du anvisningarna i [själv studie kursen om Apache HBase: komma igång med Apache HBase i HDInsight](apache-hbase-tutorial-get-started-linux.md).

> [!NOTE]
> Om du vill replikera tabeller från en anpassad namnrymd måste du också se till att lämpliga anpassade namn områden definieras i mål klustret.
>

## <a name="enable-replication"></a>Aktivera replikering

Följande steg beskriver hur du anropar skript åtgärds skriptet från Azure Portal. Information om hur du kör en skript åtgärd genom att använda Azure PowerShell och den klassiska Azure CLI finns i [Anpassa HDInsight-kluster med hjälp av skript åtgärder](../hdinsight-hadoop-customize-cluster-linux.md).

**Så här aktiverar du HBase-replikering från Azure Portal**

1. Logga in på [Azure-portalen](https://portal.azure.com).
2. Öppna käll HBases klustret.
3. I menyn kluster väljer du **skript åtgärder**.
4. Längst upp på sidan väljer du **Skicka ny**.
5. Välj eller ange följande information:

   1. **Namn**: ange **Aktivera replikering**.
   2. **Bash-skript-URL**: ange **https://raw.githubusercontent.com/Azure/hbase-utils/master/replication/hdi_enable_replication.sh** .
   3. **Head**: kontrol lera att detta är markerat. Ta bort de andra nodtypen.
   4. **Parametrar**: följande exempel parametrar aktiverar replikering för alla befintliga tabeller och kopierar sedan alla data från käll klustret till mål klustret:

    `-m hn1 -s <source hbase cluster name> -d <destination hbase cluster name> -sp <source cluster Ambari password> -dp <destination cluster Ambari password> -copydata`
    
      > [!NOTE]
      > Använd hostname i stället för FQDN för både käll-och mål klustrets DNS-namn.
      >
      > I den här genom gången förutsätts HN1 som aktiva huvudnoden. Kontrol lera klustret för att identifiera den aktiva Head-noden.

6. Välj **Skapa**. Det kan ta en stund att köra skriptet, särskilt när du använder argumentet **-COPYDATA** .

Obligatoriska argument:

|Name|Beskrivning|
|----|-----------|
|-s,--src-Cluster | Anger DNS-namnet för HBase-klustret. Exempel:-s hbsrccluster,--src-Cluster = hbsrccluster |
|-d,--sommar-Cluster | Anger DNS-namnet för mål-HBase-klustret (Replica). Exempel:-s dsthbcluster,--src-Cluster = dsthbcluster |
|-SP,--src-Ambari-Password | Anger administratörs lösen ordet för Ambari på HBase-klustret. |
|-DP,--DST-Ambari-Password | Anger administratörs lösen ordet för Ambari på mål HBase-klustret.|

Valfria argument:

|Name|Beskrivning|
|----|-----------|
|-Su,--src-Ambari-User | Anger administratörs användar namnet för Ambari i HBase-klustret. Standardvärdet är **admin**. |
|-du,--DST-Ambari-User | Anger administratörs användar namnet för Ambari på mål HBase-klustret. Standardvärdet är **admin**. |
|-t,--tabell-lista | Anger de tabeller som ska replikeras. Exempel:--Table-List = "TABLE1; tabell2; TABLE3". Om du inte anger tabeller replikeras alla befintliga HBase-tabeller.|
|-m,--Machine | Anger den head-nod där skript åtgärden körs. Värdet ska väljas baserat på vilket är den aktiva Head-noden. Använd det här alternativet när du kör skriptet $0 som en skript åtgärd från HDInsight-portalen eller Azure PowerShell.|
|-CP,-COPYDATA | Aktiverar migrering av befintliga data i tabeller där replikering är aktiverat. |
|-RPM,-replikera-Phoenix-meta | Aktiverar replikering i Phoenix system-tabeller. <br><br>*Använd det här alternativet med försiktighet.* Vi rekommenderar att du återskapar Phoenix-tabeller på replik kluster innan du använder det här skriptet. |
|-h,--hjälp | Visar användnings information. |

`print_usage()`Avsnittet i [skriptet](https://github.com/Azure/hbase-utils/blob/master/replication/hdi_enable_replication.sh) innehåller en detaljerad förklaring av parametrarna.

När skript åtgärden har distribuerats kan du använda SSH för att ansluta till mål HBase-klustret och sedan verifiera att data har repliker ATS.

### <a name="replication-scenarios"></a>Replikeringsscenarier

I följande lista visas några allmänna användnings fall och deras parameter inställningar:

- **Aktivera replikering på alla tabeller mellan de två klustren**. Det här scenariot kräver inte kopiering eller migrering av befintliga data i tabellerna och använder inte Phoenix-tabeller. Använd följande parametrar:

  `-m hn1 -s <source hbase cluster name> -d <destination hbase cluster name> -sp <source cluster Ambari password> -dp <destination cluster Ambari password>`

- **Aktivera replikering för vissa tabeller**. Använd följande parametrar om du vill aktivera replikering på TABLE1, tabell2 och TABLE3:

  `-m hn1 -s <source hbase cluster name> -d <destination hbase cluster name> -sp <source cluster Ambari password> -dp <destination cluster Ambari password> -t "table1;table2;table3"`

- **Aktivera replikering för vissa tabeller och kopiera befintliga data**. Använd följande parametrar om du vill aktivera replikering på TABLE1, tabell2 och TABLE3:

  `-m hn1 -s <source hbase cluster name> -d <destination hbase cluster name> -sp <source cluster Ambari password> -dp <destination cluster Ambari password> -t "table1;table2;table3" -copydata`

- **Aktivera replikering på alla tabeller och replikera Phoenix-metadata från källa till mål**. Phoenix metadata-replikering är inte perfekt. Använd den med försiktighet. Använd följande parametrar:

  `-m hn1 -s <source hbase cluster name> -d <destination hbase cluster name> -sp <source cluster Ambari password> -dp <destination cluster Ambari password> -t "table1;table2;table3" -replicate-phoenix-meta`

## <a name="copy-and-migrate-data"></a>Kopiera och migrera data

Det finns två separata skript åtgärds skript som är tillgängliga för att kopiera eller migrera data när replikering har Aktiver ATS:

- [Skript för små tabeller](https://raw.githubusercontent.com/Azure/hbase-utils/master/replication/hdi_copy_table.sh) (tabeller som är några gigabyte i storlek och den övergripande kopian förväntas slutföras på mindre än en timme)

- [Skript för stora tabeller](https://raw.githubusercontent.com/Azure/hbase-utils/master/replication/nohup_hdi_copy_table.sh) (tabeller som förväntas ta längre tid än en timme att kopiera)

Du kan följa samma procedur som beskrivs i [Aktivera replikering](#enable-replication) för att anropa skript åtgärden. Använd följande parametrar:

`-m hn1 -t <table1:start_timestamp:end_timestamp;table2:start_timestamp:end_timestamp;...> -p <replication_peer> [-everythingTillNow]`

`print_usage()`Avsnittet i [skriptet](https://github.com/Azure/hbase-utils/blob/master/replication/hdi_copy_table.sh) innehåller en detaljerad beskrivning av parametrarna.

### <a name="scenarios"></a>Scenarier

- **Kopiera vissa tabeller (TEST1, TEST2 och test3) för alla rader som redige ras tills nu (aktuell tidstämpel)**:

  `-m hn1 -t "test1::;test2::;test3::" -p "zk5-hbrpl2;zk1-hbrpl2;zk5-hbrpl2:2181:/hbase-unsecure" -everythingTillNow`

  Eller

  `-m hn1 -t "test1::;test2::;test3::" --replication-peer="zk5-hbrpl2;zk1-hbrpl2;zk5-hbrpl2:2181:/hbase-unsecure" -everythingTillNow`

- **Kopiera specifika tabeller med ett angivet tidsintervall**:

  `-m hn1 -t "table1:0:452256397;table2:14141444:452256397" -p "zk5-hbrpl2;zk1-hbrpl2;zk5-hbrpl2:2181:/hbase-unsecure"`

## <a name="disable-replication"></a>Inaktivera replikering

Om du vill inaktivera replikering använder du ett annat skript åtgärds skript från [GitHub](https://raw.githubusercontent.com/Azure/hbase-utils/master/replication/hdi_disable_replication.sh). Du kan följa samma procedur som beskrivs i [Aktivera replikering](#enable-replication) för att anropa skript åtgärden. Använd följande parametrar:

`-m hn1 -s <source hbase cluster name> -sp <source cluster Ambari password> <-all|-t "table1;table2;...">`

`print_usage()`Avsnittet i [skriptet](https://raw.githubusercontent.com/Azure/hbase-utils/master/replication/hdi_disable_replication.sh) innehåller en detaljerad förklaring av parametrarna.

### <a name="scenarios"></a>Scenarier

- **Inaktivera replikering i alla tabeller**:

  `-m hn1 -s <source hbase cluster name> -sp Mypassword\!789 -all`

  eller

  `--src-cluster=<source hbase cluster name> --dst-cluster=<destination hbase cluster name> --src-ambari-user=<source cluster Ambari user name> --src-ambari-password=<source cluster Ambari password>`

- **Inaktivera replikering för angivna tabeller (TABLE1, tabell2 och TABLE3)**:

  `-m hn1 -s <source hbase cluster name> -sp <source cluster Ambari password> -t "table1;table2;table3"`

> [!NOTE]
> Om du tänker ta bort mål klustret, se till att du tar bort det från peer-listan i käll klustret. Detta kan göras genom att köra kommandot remove_peer 1 i HBase-gränssnittet i käll klustret. Det kan hända att det inte går att köra käll klustret på rätt sätt.
>

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du lärt dig hur du konfigurerar Apache HBase-replikering i ett virtuellt nätverk, eller mellan två virtuella nätverk. Mer information om HDInsight och Apache HBase finns i följande artiklar:

* [Kom igång med Apache HBase i HDInsight](./apache-hbase-tutorial-get-started-linux.md)
* [HDInsight Apache HBase-översikt](./apache-hbase-overview.md)
* [Skapa Apache HBase-kluster i Azure Virtual Network](./apache-hbase-provision-vnet.md)