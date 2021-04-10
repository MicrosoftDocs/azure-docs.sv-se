---
title: Kontrol lera nätverks trafik i Azure HDInsight
description: Lär dig mer om tekniker för att kontrol lera inkommande och utgående trafik till Azure HDInsight-kluster.
ms.service: hdinsight
ms.topic: conceptual
ms.date: 09/02/2020
ms.openlocfilehash: 66b078e8dfa1daa100978f04283e9bba7158bddf
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "104867040"
---
# <a name="control-network-traffic-in-azure-hdinsight"></a>Kontrol lera nätverks trafik i Azure HDInsight

Nätverks trafik i virtuella Azure-nätverk kan kontrol leras med hjälp av följande metoder:

* Med **nätverks säkerhets grupper** (NSG) kan du filtrera inkommande och utgående trafik till nätverket. Mer information finns i dokumentet [filtrera nätverks trafik med nätverks säkerhets grupper](../virtual-network/network-security-groups-overview.md) .

* **Virtuella nätverks enheter** (NVA) kan endast användas med utgående trafik. NVA replikerar funktionaliteten för enheter som brand väggar och routrar. Mer information finns i dokumentet om [nätverks](https://azure.microsoft.com/solutions/network-appliances) installationer.

Som en hanterad tjänst kräver HDInsight obegränsad åtkomst till HDInsight-hälso-och hanterings tjänsterna både för inkommande och utgående trafik från det virtuella nätverket. När du använder NSG: er måste du se till att dessa tjänster fortfarande kan kommunicera med HDInsight-kluster.

:::image type="content" source="./media/control-network-traffic/hdinsight-vnet-diagram.png" alt-text="Diagram över HDInsight-entiteter som skapats i Azure anpassat VNET" border="false":::

## <a name="hdinsight-with-network-security-groups"></a>HDInsight med nätverks säkerhets grupper

Om du planerar att använda **nätverks säkerhets grupper** för att kontrol lera nätverks trafiken utför du följande åtgärder innan du installerar HDInsight:

1. Identifiera den Azure-region som du planerar att använda för HDInsight.

2. Identifiera de tjänst koder som krävs av HDInsight för din region. Det finns flera sätt att hämta dessa service märken:
    1. Se listan över publicerade service-Taggar i [NSG (Network Security Group) för Azure HDInsight](hdinsight-service-tags.md). 
    2. Om din region inte finns i listan använder du [API: et för identifiering av service tag](../virtual-network/service-tags-overview.md#use-the-service-tag-discovery-api-public-preview) för att hitta en service tag för din region.
    3. Om du inte kan använda API: t kan du hämta [JSON-filen för service tag](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files) och söka efter önskad region.


3. Skapa eller ändra nätverks säkerhets grupper för det undernät som du planerar att installera HDInsight i.

    * __Nätverks säkerhets grupper__: Tillåt __inkommande__ trafik på port __443__ från IP-adresserna. Detta säkerställer att HDInsight Management Services kan komma åt klustret utanför det virtuella nätverket. För __KAFKA rest proxy__ -aktiverade kluster tillåter du även __inkommande__ trafik på port __9400__ . Detta säkerställer att Kafka REST-proxyservern kan kontaktas.

Mer information om nätverks säkerhets grupper finns i [Översikt över nätverks säkerhets grupper](../virtual-network/network-security-groups-overview.md).

## <a name="controlling-outbound-traffic-from-hdinsight-clusters"></a>Kontrol lera utgående trafik från HDInsight-kluster

Mer information om hur du styr utgående trafik från HDInsight-kluster finns i [Konfigurera begränsning av utgående nätverks trafik för Azure HDInsight-kluster](hdinsight-restrict-outbound-traffic.md).

### <a name="forced-tunneling-to-on-premises"></a>Tvingad tunnel trafik till lokalt

Tvingad tunnel trafik är en användardefinierad routningstabell där all trafik från ett undernät tvingas till ett nätverk eller en viss plats, t. ex. ditt lokala nätverk eller brand vägg. Tvingad tunnel trafik av all data överföring tillbaka till lokalt rekommenderas _inte_ på grund av stora mängder data överföring och potentiell prestanda påverkan.

Kunder som är intresserade av att konfigurera Tvingad tunnel trafik bör använda [anpassade metastores](./hdinsight-use-external-metadata-stores.md) och konfigurera lämplig anslutning från klustrets undernät eller lokalt nätverk till dessa anpassade metastores.

Om du vill se ett exempel på UDR-installationen med Azure Firewall, se [Konfigurera begränsning av utgående nätverks trafik för Azure HDInsight-kluster](hdinsight-restrict-outbound-traffic.md).

## <a name="required-ports"></a>Portar som krävs

Om du planerar att använda en **brand vägg** och komma åt klustret utifrån vissa portar kan du behöva tillåta trafik på de portar som behövs för ditt scenario. Som standard behövs ingen särskild filtrering av portar så länge som den Azure-hanteringsserver som beskrivs i föregående avsnitt kan komma åt klustret på port 443.

En lista över portar för vissa tjänster finns i [portarna som används av Apache Hadoop Services i HDInsight](hdinsight-hadoop-port-settings-for-services.md) -dokument.

Mer information om brand Väggs regler för virtuella enheter finns i [scenario](../virtual-network/virtual-network-scenario-udr-gw-nva.md) dokumentet för Virtual-installation.

## <a name="next-steps"></a>Nästa steg

* Kod exempel och exempel på hur du skapar virtuella Azure-nätverk finns i [skapa virtuella nätverk för Azure HDInsight-kluster](hdinsight-create-virtual-network.md).
* Ett slut punkt till slut punkts exempel på hur du konfigurerar HDInsight för att ansluta till ett lokalt nätverk finns i [ansluta HDInsight till ett lokalt nätverk](./connect-on-premises-network.md).
* Mer information om virtuella Azure-nätverk finns i [Översikt över Azure-Virtual Network](../virtual-network/virtual-networks-overview.md).
* Mer information om nätverks säkerhets grupper finns i [nätverks säkerhets grupper](../virtual-network/network-security-groups-overview.md).
* Mer information om användardefinierade vägar finns i [användardefinierade vägar och IP-vidarebefordring](../virtual-network/virtual-networks-udr-overview.md).
* Mer information om virtuella nätverk finns i [Planera virtuella nätverk för HDInsight](./hdinsight-plan-virtual-network-deployment.md).