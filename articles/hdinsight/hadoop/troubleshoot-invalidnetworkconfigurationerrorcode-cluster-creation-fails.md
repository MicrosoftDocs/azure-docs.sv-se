---
title: InvalidNetworkConfigurationErrorCode-fel – Azure HDInsight
description: Olika orsaker till att kluster skapas med InvalidNetworkConfigurationErrorCode i Azure HDInsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 01/12/2021
ms.openlocfilehash: 82e1c6bdcd508bf1844223b331bf8363efa7b573
ms.sourcegitcommit: 6628bce68a5a99f451417a115be4b21d49878bb2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/18/2021
ms.locfileid: "98555329"
---
# <a name="cluster-creation-fails-with-invalidnetworkconfigurationerrorcode-in-azure-hdinsight"></a>Det går inte att skapa kluster med InvalidNetworkConfigurationErrorCode i Azure HDInsight

Den här artikeln beskriver fel söknings steg och möjliga lösningar för problem med att interagera med Azure HDInsight-kluster.

Om du ser felkod `InvalidNetworkConfigurationErrorCode` med beskrivningen "Virtual Network-konfigurationen är inte kompatibel med HDInsight-krav", tyder det vanligt vis på ett problem med konfigurationen av det [virtuella nätverket](../hdinsight-plan-virtual-network-deployment.md) för klustret. Följ avsnitten nedan för att lösa problemet, baserat på resten av fel beskrivningen.

## <a name="hostname-resolution-failed"></a>"Det gick inte att matcha värdnamn"

### <a name="issue"></a>Problem

Fel beskrivningen innehåller "matchning av värdnamn" misslyckades ".

### <a name="cause"></a>Orsak

Felet pekar på ett problem med den anpassade DNS-konfigurationen. DNS-servrar i ett virtuellt nätverk kan vidarebefordra DNS-frågor till Azures rekursiva matchare för att matcha värdnamn i det virtuella nätverket (se [namn matchning i virtuella nätverk](../../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md) för mer information). Åtkomst till Azures rekursiva matchare tillhandahålls via den virtuella IP-168.63.129.16. Den här IP-adressen är bara tillgänglig från virtuella Azure-datorer. Det fungerar därför inte om du använder en OnPrem DNS-server, eller om DNS-servern är en virtuell Azure-dator, som inte ingår i klustrets virtuella nätverk.

### <a name="resolution"></a>Lösning

1. Använd SSH i den virtuella datorn som är en del av klustret och kör kommandot `hostname -f` . Detta returnerar värdens fullständigt kvalificerade domän namn (enligt `<host_fqdn>` anvisningarna nedan).

1. Kör sedan kommandot (till `nslookup <host_fqdn>` exempel `nslookup hn1-hditest.5h6lujo4xvoe1kprq3azvzmwsd.hx.internal.cloudapp.net` ). Om det här kommandot matchar namnet på en IP-adress innebär det att din DNS-server fungerar som den ska. I det här fallet kan du generera ett support ärende med HDInsight och vi undersöker ditt problem. I support ärendet inkluderar du de fel söknings steg som du utförde. Detta hjälper oss att lösa problemet snabbare.

1. Om kommandot ovan inte returnerar en IP-adress kan du köra `nslookup <host_fqdn> 168.63.129.16` (till exempel `nslookup hn1-hditest.5h6lujo4xvoe1kprq3azvzmwsd.hx.internal.cloudapp.net 168.63.129.16` ). Om det här kommandot kan matcha IP-adressen innebär det att antingen att DNS-servern inte vidarebefordrar frågan till Azures DNS, eller om den inte är en del av samma virtuella nätverk som klustret.

1. Om du inte har en virtuell Azure-dator som kan fungera som en anpassad DNS-server i klustrets virtuella nätverk måste du först lägga till detta. Skapa en virtuell dator i det virtuella nätverket, som kommer att konfigureras som DNS-vidarebefordrare.

1. När du har distribuerat en virtuell dator i det virtuella nätverket konfigurerar du reglerna för vidarebefordring av DNS på den här virtuella datorn. Vidarebefordra alla namn matchnings begär Anden från IDN till 168.63.129.16 och resten av DNS-servern. [Här](../hdinsight-plan-virtual-network-deployment.md) är ett exempel på den här installationen för en anpassad DNS-server.

1. Lägg till IP-adressen för den virtuella datorn som första DNS-post för Virtual Network DNS-konfiguration.

---

## <a name="failed-to-connect-to-azure-storage-account"></a>"Det gick inte att ansluta till Azure Storage kontot"

### <a name="issue"></a>Problem

Fel beskrivningen innehåller "Det gick inte att ansluta till Azure Storage kontot" eller "Det gick inte att ansluta till Azure SQL".

### <a name="cause"></a>Orsak

Azure Storage och SQL har inte fasta IP-adresser, så vi måste tillåta utgående anslutningar till alla IP-adresser för att få åtkomst till dessa tjänster. De exakta lösnings stegen beror på om du har konfigurerat en nätverks säkerhets grupp (NSG) eller User-Defined regler (UDR). Mer information om dessa konfigurationer finns i avsnittet om att [kontrol lera nätverks trafik med HDInsight med nätverks säkerhets grupper och användardefinierade vägar](../control-network-traffic.md) .

### <a name="resolution"></a>Lösning

* Om klustret använder en [nätverks säkerhets grupp (NSG)](../../virtual-network/virtual-network-vnet-plan-design-arm.md).

    Gå till Azure Portal och identifiera den NSG som är associerad med under nätet där klustret distribueras. I avsnittet **utgående säkerhets regler** tillåter du utgående åtkomst till Internet utan begränsning (Observera att ett lägre **prioritets** nummer innebär högre prioritet). I avsnittet **undernät** bekräftar du även om den här NSG tillämpas på klustrets undernät.

* Om ditt kluster använder en [användardefinierad routning (UDR)](../../virtual-network/virtual-networks-udr-overview.md).

    Gå till Azure Portal och identifiera routningstabellen som är associerad med under nätet där klustret distribueras. När du har hittat routningstabellen för under nätet, kontrollerar du avsnittet **vägar** i den.

    Om det finns definierade vägar kontrollerar du att det finns vägar för IP-adresser för den region där klustret distribuerades och att **NextHopType** för varje väg är **Internet**. Det bör finnas en väg definierad för varje obligatorisk IP-adress som dokumenteras i den tidigare artikeln.

## <a name="failed-to-establish-an-outbound-connection-from-the-cluster-for-the-communication-with-the-hdinsight-resource-provider-please-ensure-that-outbound-connectivity-is-allowed"></a>"Det gick inte att upprätta en utgående anslutning från klustret för kommunikation med HDInsight-resurs leverantören. Kontrol lera att den utgående anslutningen tillåts. "

### <a name="issue"></a>Problem

Fel beskrivningen innehåller "Det gick inte att upprätta en utgående anslutning från klustret för kommunikation med HDInsight-resurs leverantören. Kontrol lera att den utgående anslutningen tillåts. "

### <a name="cause"></a>Orsak

När du använder privata länkade HDInsight-kluster måste utgående åtkomst från klustret konfigureras för att tillåta att anslutningar görs till HDInsight-resurs leverantören.

### <a name="resolution"></a>Lösning

* För att lösa det här problemet, se installations stegen för HDInsight privat länk i [konfiguration av privat länk](../hdinsight-private-link.md)
---

## <a name="virtual-network-configuration-is-not-compatible-with-hdinsight-requirement"></a>"Konfiguration av virtuellt nätverk är inte kompatibelt med HDInsight-krav"

### <a name="issue"></a>Problem

Fel beskrivningarna innehåller meddelanden som liknar följande:

```
ErrorCode: InvalidNetworkConfigurationErrorCode
ErrorDescription: Virtual Network configuration is not compatible with HDInsight Requirement. Error: 'Failed to connect to Azure Storage Account; Failed to connect to Azure SQL; HostName Resolution failed', Please follow https://go.microsoft.com/fwlink/?linkid=853974 to fix it.
```

### <a name="cause"></a>Orsak

Förmodligen ett problem med den anpassade DNS-konfigurationen.

### <a name="resolution"></a>Lösning

Kontrol lera att 168.63.129.16 finns i den anpassade DNS-kedjan. DNS-servrar i ett virtuellt nätverk kan vidarebefordra DNS-frågor till Azures rekursiva matchare för att matcha värdnamn i det virtuella nätverket. Mer information finns i [namn matchning i virtuella nätverk](../../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server). Åtkomst till Azures rekursiva matchare tillhandahålls via den virtuella IP-168.63.129.16.

1. Använd [SSH-kommandot](../hdinsight-hadoop-linux-use-ssh-unix.md) för att ansluta till klustret. Redigera kommandot nedan genom att ersätta kluster namn med namnet på klustret och ange sedan kommandot:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Kör följande kommando:

    ```bash
    cat /etc/resolv.conf | grep nameserver*
    ```

    Du bör se något som liknar följande:

    ```output
    nameserver 168.63.129.16
    nameserver 10.21.34.43
    nameserver 10.21.34.44
    ```

    Baserat på resultatet – Välj något av följande steg för att följa:

#### <a name="1686312916-is-not-in-this-list"></a>168.63.129.16 finns inte i listan

**Alternativ 1**  
Lägg till 168.63.129.16 som första anpassade DNS för det virtuella nätverket med hjälp av stegen som beskrivs i [planera ett virtuellt nätverk för Azure HDInsight](../hdinsight-plan-virtual-network-deployment.md). De här stegen gäller endast om den anpassade DNS-servern körs på Linux.

**Alternativ 2**  
Distribuera en virtuell DNS-serverdator för det virtuella nätverket. Detta omfattar följande steg:

* Skapa en virtuell dator i det virtuella nätverket, som kommer att konfigureras som DNS-vidarebefordrare (det kan vara en virtuell Linux-eller Windows-dator).
* Konfigurera regler för vidarebefordran av DNS på den här virtuella datorn (vidarebefordra alla namn matchnings begär Anden från IDN till 168.63.129.16 och resten till din DNS-Server).
* Lägg till IP-adressen för den virtuella datorn som första DNS-post för Virtual Network DNS-konfiguration.

#### <a name="1686312916-is-in-the-list"></a>168.63.129.16 finns i listan

I det här fallet skapar du ett support ärende med HDInsight och vi undersöker ditt problem. Inkludera resultatet av nedanstående kommandon i ditt support ärende. Detta hjälper oss att undersöka och lösa problemet snabbare.

Från en SSH-session i head-noden redigerar du och kör följande:

```bash
hostname -f
nslookup <headnode_fqdn> (e.g.nslookup hn1-hditest.5h6lujo4xvoe1kprq3azvzmwsd.hx.internal.cloudapp.net)
dig @168.63.129.16 <headnode_fqdn> (e.g. dig @168.63.129.16 hn0-hditest.5h6lujo4xvoe1kprq3azvzmwsd.hx.internal.cloudapp.net)
```
### <a name="cause"></a>Orsak

En annan orsak till den här `InvalidNetworkConfigurationErrorCode` felkoden är att använda den föråldrade parametern `EnableVmProtection` i PowerShell eller en Azure-Runbook.

### <a name="resolution"></a>Lösning

Använd giltiga parametrar för `Get-AzVirtualNetwork` enligt beskrivningen i [AZ PowerShell SDK](/powershell/module/az.network/get-azvirtualnetwork)

---

### <a name="next-steps"></a>Nästa steg

Om du inte ser problemet eller inte kan lösa problemet kan du gå till någon av följande kanaler för mer support:

* Få svar från Azure-experter via [Azure community support](https://azure.microsoft.com/support/community/).

* Anslut till [@AzureSupport](https://twitter.com/azuresupport) – det officiella Microsoft Azure kontot för att förbättra kund upplevelsen genom att ansluta Azure-communityn till rätt resurser: svar, support och experter.

* Om du behöver mer hjälp kan du skicka en support förfrågan från [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Välj **stöd** på Meny raden eller öppna **Hjälp + Support** Hub. Mer detaljerad information finns [i så här skapar du en support förfrågan för Azure](../../azure-portal/supportability/how-to-create-azure-support-request.md). Åtkomst till prenumerations hantering och fakturerings support ingår i din Microsoft Azure prenumeration och teknisk support tillhandahålls via ett av support avtalen för [Azure](https://azure.microsoft.com/support/plans/).
