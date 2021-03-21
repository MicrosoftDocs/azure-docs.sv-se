---
title: Vanliga frågor och svar om Apache Kafka i Azure HDInsight
description: Få svar på vanliga frågor om Apache Kafka i Azure HDInsight, en hanterad Hadoop-moln tjänst.
ms.service: hdinsight
ms.topic: conceptual
ms.date: 08/14/2019
ms.openlocfilehash: 851d7ceb44d2466ed31e26c1442fde9acea9fd22
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98939102"
---
# <a name="frequently-asked-questions-about-apache-kafka-in-azure-hdinsight"></a>Vanliga frågor och svar om Apache Kafka i Azure HDInsight

Den här artikeln handlar om vanliga frågor om att använda Apache Kafka på Azure HDInsight.

## <a name="what-kafka-versions-are-supported-by-hdinsight"></a>Vilka Kafka-versioner stöds av HDInsight?

Hittar du mer information om vilka HDInsight-komponenter som stöds officiellt i [vilka Apache Hadoop-komponenter och versioner som är tillgängliga med HDInsight?](../hdinsight-component-versioning.md#supported-hdinsight-versions). Vi rekommenderar att du alltid använder den senaste versionen för att säkerställa bästa möjliga prestanda och användar upplevelse.

## <a name="what-resources-are-provided-in-an-hdinsight-kafka-cluster-and-what-resources-am-i-charged-for"></a>Vilka resurser finns i ett HDInsight Kafka-kluster och vilka resurser debiteras jag för?

Ett HDInsight Kafka-kluster innehåller följande resurser:

* Huvudnoder
* Zookeeper-noder
* Broker (Worker)-noder 
* Azure Managed Disks kopplat till Service Broker-noderna
* Gateway-noder

Alla dessa resurser debiteras enligt vår [pris modell för HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/), förutom Gateway-noder. Du debiteras inte för gateway-noder.

En mer detaljerad beskrivning av olika typer av noder finns i [arkitekturen för virtuella Azure HDInsight-nätverk](../hdinsight-virtual-network-architecture.md). Prissättningen baseras på användning per minut-nod. Priserna varierar beroende på Node-storlek, antal noder, typ av hanterad disk som används och region.

## <a name="do-apache-kafka-apis-work-with-hdinsight"></a>Fungerar Apache Kafka-API: er med HDInsight?

Ja, HDInsight använder interna Kafka-API: er. Klient program koden behöver inte ändras. Se [självstudie: använd Apache Kafka tillverkare och konsument-API: er](./apache-kafka-producer-consumer-api.md) för att se hur du kan använda Java-baserade tillverkare/konsument-API: er med klustret.

## <a name="can-i-change-cluster-configurations"></a>Kan jag ändra klusterkonfigurationer?

Ja, via Ambari-portalen. Varje komponent i portalen har ett **konfigurations** avsnitt som kan användas för att ändra komponent konfigurationerna. Vissa ändringar kan kräva omstart av Service Broker.

## <a name="what-type-of-authentication-does-hdinsight-support-for-apache-kafka"></a>Vilken typ av autentisering stöder HDInsight för Apache Kafka?

Med hjälp av [Enterprise Security Package (ESP)](../domain-joined/apache-domain-joined-architecture.md)kan du få säkerhet på ämnes nivå för sina Kafka-kluster. Mer information finns i [Självstudier: konfigurera Apache Kafka principer i HDInsight med Enterprise Security Package (för hands version)](../domain-joined/apache-domain-joined-run-kafka.md).

## <a name="is-my-data-encrypted-can-i-use-my-own-keys"></a>Är mina data krypterade? Kan jag använda mina egna nycklar?

Alla Kafka-meddelanden på de hanterade diskarna är krypterade med [Azure Storage Service Encryption (SSE)](../../storage/common/storage-service-encryption.md). Data under överföring (till exempel data som överförs från klienter till utlösare och på annat sätt runt) är inte krypterade som standard. Det är möjligt att kryptera sådan trafik genom [att konfigurera TLS på egen hand](./apache-kafka-ssl-encryption-authentication.md). Med HDInsight kan du dessutom hantera sina egna nycklar för att kryptera data i vila. Mer information finns i [kund hanterad nyckel disk kryptering](../disk-encryption.md).

## <a name="how-do-i-connect-clients-to-my-cluster"></a>Hur gör jag för att ansluta klienter till mitt kluster?

För att Kafka-klienter ska kunna kommunicera med Kafka-utjämnare måste de kunna komma åt utsättare över nätverket. För HDInsight-kluster är Virtual Network (VNet) säkerhets gränser. Därför är det enklaste sättet att ansluta klienter till ditt HDInsight-kluster att skapa klienter inom samma VNet som klustret. Andra scenarier är:

* Ansluta klienter i ett annat Azure VNet – peer-klustrets VNet och klientens VNet och konfigurera klustret för [IP-annonsering](apache-kafka-connect-vpn-gateway.md#configure-kafka-for-ip-advertising). När IP-annonsering används måste Kafka-klienterna använda Broker IP-adresser för att ansluta till utjämnare, i stället för fullständigt kvalificerade domän namn (FQDN).

* Ansluta lokala klienter – med ett VPN-nätverk och konfigurera anpassade DNS-servrar enligt beskrivningen i [planera ett virtuellt nätverk för Azure HDInsight](../hdinsight-plan-virtual-network-deployment.md).

* Skapa en offentlig slut punkt för din Kafka-tjänst – om ditt företags säkerhets krav tillåter det kan du distribuera en offentlig slut punkt för Kafka-utjämnare eller en självhanterad REST-slutpunkt med öppen källkod med en offentlig slut punkt.

## <a name="can-i-add-more-disk-space-on-an-existing-cluster"></a>Kan jag lägga till mer disk utrymme i ett befintligt kluster?

Om du vill öka mängden utrymme som är tillgängligt för Kafka-meddelanden kan du öka antalet noder. Det finns för närvarande inte stöd för att lägga till fler diskar i ett befintligt kluster.

## <a name="can-a-kafka-cluster-work-with-databricks"></a>Kan ett Kafka-kluster arbeta med Databricks? 

Ja, Kafka-kluster kan arbeta med Databricks så länge de finns i samma VNet. Om du vill använda ett Kafka-kluster med Databricks skapar du ett VNet med ett HDInsight Kafka-kluster och anger sedan det virtuella nätverket när du skapar din Databricks-arbetsyta och använder VNet-inmatning. Mer information finns i [distribuera Azure Databricks i Azure-Virtual Network (VNet-insprutning)](/azure/databricks/administration-guide/cloud-configurations/azure/vnet-inject). Du måste ange namn på Start Broker för Kafka-klustret när du skapar Databricks-arbetsytan. Information om hur du hämtar Kafka Broker-namn finns i [Hämta värd information för Apache Zookeeper och Broker](./apache-kafka-get-started.md#getkafkainfo).

## <a name="how-can-i-have-maximum-data-durability"></a>Hur kan jag få maximal data hållbarhet?

Med data hållbarhet kan du få den lägsta risken för meddelande förlust. För att uppnå maximal data hållbarhet rekommenderar vi följande inställningar:

* Använd en lägsta replikeringsfrekvens på 3 i de flesta regioner
* Använd en lägsta replikeringsfrekvens på 4 i regioner med endast två fel domäner
* Inaktivera val av ta bort lead
* Ange **min. desync. repliker** till 2 eller mer – detta ändrar antalet repliker som måste synkroniseras fullständigt med ledaren innan en skrivning kan fortsätta
* Ange egenskapen **ack** till **all** – denna egenskap kräver att alla repliker bekräftar alla meddelanden

Att konfigurera Kafka för högre data konsekvens påverkar tillgängligheten för utjämnare att producera förfrågningar.

## <a name="can-i-replicate-my-data-to-multiple-clusters"></a>Kan jag replikera mina data till flera kluster?

Ja, data kan replikeras till flera kluster med hjälp av Kafka MirrorMaker. Mer information om hur du konfigurerar MirrorMaker finns i [speglade Apache Kafka ämnen](apache-kafka-mirroring.md). Det finns dessutom andra självhanterade tekniker och leverantörer med öppen källkod som kan hjälpa till att uppnå replikering till flera kluster, till exempel [Brooklin](https://github.com/linkedin/Brooklin/).

## <a name="can-i-upgrade-my-cluster-how-should-i-upgrade-my-cluster"></a>Kan jag uppgradera mitt kluster? Hur uppgraderar jag mitt kluster?

Vi stöder för närvarande inte uppgraderingar av kluster versioner på plats. Om du vill uppdatera klustret till en högre Kafka-version skapar du ett nytt kluster med den version som du vill använda och migrerar Kafka-klienterna så att de använder det nya klustret.

## <a name="how-do-i-monitor-my-kafka-cluster"></a>Hur gör jag för att övervaka mitt Kafka-kluster?

Använd Azure Monitor för att analysera dina [Kafka-loggar](./apache-kafka-log-analytics-operations-management.md).

## <a name="next-steps"></a>Nästa steg

* [Konfigurera TLS-kryptering och autentisering för Apache Kafka i Azure HDInsight](./apache-kafka-ssl-encryption-authentication.md)
* [Använd MirrorMaker för att replikera Apache Kafka-ämnen med Kafka i HDInsight](./apache-kafka-mirroring.md)