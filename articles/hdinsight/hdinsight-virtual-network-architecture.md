---
title: Azure HDInsight Virtual Network-arkitektur
description: Lär dig mer om de resurser som är tillgängliga när du skapar ett HDInsight-kluster i en Azure-Virtual Network.
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/14/2020
ms.openlocfilehash: 43640a9de91b6b44a236090ff029a49313ee0247
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "104871681"
---
# <a name="azure-hdinsight-virtual-network-architecture"></a>Azure HDInsight Virtual Network-arkitektur

I den här artikeln förklaras de resurser som finns när du distribuerar ett HDInsight-kluster till en anpassad Azure-Virtual Network. Med den här informationen kan du ansluta lokala resurser till ditt HDInsight-kluster i Azure. Mer information om virtuella Azure-nätverk finns i [Vad är azure Virtual Network?](../virtual-network/virtual-networks-overview.md).

## <a name="resource-types-in-azure-hdinsight-clusters"></a>Resurs typer i Azure HDInsight-kluster

Azure HDInsight-kluster har olika typer av virtuella datorer eller noder. Varje nodtyp spelar en roll i systemets funktion. I följande tabell sammanfattas de olika nodtyper och deras roller i klustret.

| Typ | Beskrivning |
| --- | --- |
| Huvudnod |  För alla kluster typer förutom Apache Storm är huvudnoderna värdar för de processer som hanterar körningen av det distribuerade programmet. Head-noden är också den nod som du kan använda SSH i och köra program som sedan koordineras för att köras över kluster resurserna. Antalet huvudnoder är fast på två för alla kluster typer. |
| ZooKeeper-nod | Zookeeper samordnar aktiviteter mellan noderna som utför data bearbetning. Det påverkar också Head-noden och håller reda på vilken Head-nod som kör en speciell huvud tjänst. Antalet ZooKeeper-noder är fasta på tre. |
| Arbetsnoden | Representerar de noder som stöder data bearbetnings funktioner. Arbetsnoder kan läggas till eller tas bort från klustret för att skala data behandlings kapacitet och hantera kostnader. |
| R Server Edge-nod | Noden R Server Edge representerar noden som du kan använda SSH i och köra program som sedan koordineras för att köras över kluster resurserna. En Edge-nod deltar inte i data analysen i klustret. Den här noden är också värd för R Studio Server, så att du kan köra R-program med hjälp av en webbläsare. |
| Region nod | Regions-noden (kallas även en datanode) för HBase-kluster typen kör region servern. Region servrar hanterar och hanterar en del av de data som hanteras av HBase. Det går att lägga till eller ta bort regionfiler från klustret för att skala data behandlings kapacitet och hantera kostnader.|
| Nimbus-nod | För Storm-klustret tillhandahåller Nimbus-noden funktioner som liknar Head-noden. Nimbus-noden tilldelar uppgifter till andra noder i ett kluster via Zookeeper, vilket samordnar körningen av storm-topologier. |
| Ansvarig nod | För Storm-klustret kör den överordnade noden de instruktioner som tillhandahålls av Nimbus-noden för att utföra bearbetningen. |

## <a name="resource-naming-conventions"></a>Namngivnings konventioner för resurser

Använd fullständigt kvalificerade domän namn (FQDN) vid adressering av noder i klustret. Du kan hämta FQDN: er för olika nodtyper i klustret med hjälp av [Ambari-API: et](hdinsight-hadoop-manage-ambari-rest-api.md).

Dessa FQDN kommer att ha formen `<node-type-prefix><instance-number>-<abbreviated-clustername>.<unique-identifier>.cx.internal.cloudapp.net` .

`<node-type-prefix>`Kommer att vara *HN* för huvudnoderna,  till och med för arbetsnoder och *Zn* för Zookeeper-noder.

Om du bara behöver värd namnet använder du bara den första delen av FQDN: `<node-type-prefix><instance-number>-<abbreviated-clustername>`

## <a name="basic-virtual-network-resources"></a>Grundläggande virtuella nätverks resurser

Följande diagram visar placeringen av HDInsight-noder och nätverks resurser i Azure.

:::image type="content" source="./media/hdinsight-virtual-network-architecture/hdinsight-vnet-diagram.png" alt-text="Diagram över HDInsight-entiteter som skapats i Azure anpassat VNET" border="false":::

Standard resurserna i en Azure-Virtual Network omfattar de typer av klusternoder som anges i föregående tabell. Och nätverks enheter som stöder kommunikation mellan det virtuella nätverket och externa nätverk.

I följande tabell sammanfattas de nio klusternoder som skapas när HDInsight distribueras till en anpassad Azure-Virtual Network.

| Resurstyp | Antal närvarande | Information |
| --- | --- | --- |
|Huvudnod | två |    |
|Zookeeper-nod | tre | |
|Arbetsnoden | två | Det här antalet kan variera beroende på kluster konfiguration och skalning. Minst tre arbetsnoder behövs för Apache Kafka.  |
|Gateway-nod | två | Gateway-noder är virtuella Azure-datorer som skapas på Azure, men som inte är synliga i din prenumeration. Kontakta supporten om du behöver starta om noderna. |

Följande nätverks resurser som finns skapas automatiskt i det virtuella nätverk som används med HDInsight:

| Nätverks resurs | Antal närvarande | Information |
| --- | --- | --- |
|Lastbalanserare | tre | |
|Nätverksgränssnitt | 9 | Det här värdet baseras på ett vanligt kluster där varje nod har sitt eget nätverks gränssnitt. De nio gränssnitten är: två huvudnoder, tre Zookeeper-noder, två arbetsnoder och två Gateway-noder som nämns i föregående tabell. |
|Offentliga IP-adresser | två |    |

## <a name="endpoints-for-connecting-to-hdinsight"></a>Slut punkter för att ansluta till HDInsight

Du kan komma åt ditt HDInsight-kluster på tre sätt:

- En HTTPS-slutpunkt utanför det virtuella nätverket på `CLUSTERNAME.azurehdinsight.net` .
- En SSH-slutpunkt för direkt anslutning till huvudnoden på `CLUSTERNAME-ssh.azurehdinsight.net` .
- En HTTPS-slutpunkt i det virtuella nätverket `CLUSTERNAME-int.azurehdinsight.net` . Lägg märke till " `-int` " i den här URL: en. Den här slut punkten kommer att matcha en privat IP-adress i det virtuella nätverket och är inte tillgänglig från det offentliga Internet.

Dessa tre slut punkter är varje tilldelad belastningsutjämnare.

Offentliga IP-adresser tillhandahålls också till de två slut punkter som tillåter anslutning från utanför det virtuella nätverket.

1. En offentlig IP-adress tilldelas belastningsutjämnaren för det fullständigt kvalificerade domän namnet (FQDN) som ska användas vid anslutning till klustret från Internet `CLUSTERNAME.azurehdinsight.net` .
1. Den andra offentliga IP-adressen används för domän namnet endast SSH `CLUSTERNAME-ssh.azurehdinsight.net` .

## <a name="next-steps"></a>Nästa steg

- [Skydda inkommande trafik till HDInsight-kluster i ett virtuellt nätverk med privat slut punkt](https://azure.microsoft.com/blog/secure-incoming-traffic-to-hdinsight-clusters-in-a-vnet-with-private-endpoint/)
