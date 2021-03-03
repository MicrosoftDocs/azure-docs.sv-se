---
title: Vanliga frågor om Azure Managed instance för Apache Cassandra från Azure Portal
description: Vanliga frågor om Azure Managed instance för Apache Cassandra. Den här artikeln behandlar frågor om när du ska använda hanterade instanser, fördelar, data flödes gränser, regioner som stöds och annan konfigurations information.
author: TheovanKraay
ms.author: thvankra
ms.service: managed-instance-apache-cassandra
ms.topic: quickstart
ms.date: 03/02/2021
ms.openlocfilehash: 1ba2b7d648c86912118b83a566bf2eb0800baee2
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/03/2021
ms.locfileid: "101748953"
---
# <a name="frequently-asked-questions-about-azure-managed-instance-for-apache-cassandra-preview"></a>Vanliga frågor om Azure Managed instance för Apache Cassandra (för hands version)

Den här artikeln behandlar vanliga frågor om Azure Managed instance för Apache Cassandra. Du får lära dig när du ska använda hanterade instanser, deras fördelar, data flödes gränser, regioner som stöds och deras konfigurations information.

> [!IMPORTANT]
> Azure Managed instance för Apache Cassandra är för närvarande en offentlig för hands version.
> Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade.
> Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="general-faq"></a>Allmänna vanliga frågor och svar

### <a name="what-are-the-benefits-azure-managed-instance-for-apache-cassandra"></a>Vilka är fördelarna med Azure Managed instance för Apache Cassandra?

Apache Cassandra-databasen är det rätta valet när du behöver skalbarhet och hög tillgänglighet utan att kompromissa med prestanda. Det är en bra plattform för verksamhets kritiska data på grund av den linjära skalbarheten och beprövad fel tolerans för maskin vara eller moln infrastruktur. Azure Managed instance för Apache Cassandra är en tjänst för att hantera instanser av Apache Cassandra-datacenter med öppen källkod som distribuerats i Azure.

Den kan antingen användas helt i molnet eller som en del av ett hybrid moln och ett lokalt kluster. Den här tjänsten är ett bra alternativ när du vill ha detaljerad konfiguration och kontroll i Apache-Cassandra med öppen källkod utan någon underhålls kostnad.

### <a name="why-should-i-use-this-service-instead-of-azure-cosmos-db-cassandra-api"></a>Varför ska jag använda den här tjänsten i stället för Azure Cosmos DB API för Cassandra?

Den Azure-hanterade instansen för Apache Cassandra levereras av Azure Cosmos DBs teamet. Det är en fristående hanterad tjänst för att distribuera, underhålla och skala Apache Cassandra-Data Center och kluster med öppen källkod. [Azure Cosmos DB API för Cassandra](../cosmos-db/cassandra-introduction.md) å andra sidan är en plattform som en tjänst, vilket ger ett Interoperabilitets lager för Apache Cassandra Wire-protokollet. Om du förväntar dig att plattformen ska fungera på exakt samma sätt som alla Apache Cassandra-kluster bör du välja tjänsten hanterad instans. Mer information finns i den [Azure-hanterade instansen för Apache Cassandra Vs Azure Cosmos DB API för Cassandra](compare-cosmosdb-managed-instance.md) -artikeln.

### <a name="is-azure-managed-instance-for-apache-cassandra-dependent-on-azure-cosmos-db"></a>Är Azure-hanterad instans för Apache Cassandra beroende av Azure Cosmos DB?

Nej, det finns inget arkitektur beroende mellan den Azure-hanterade instansen för Apache Cassandra och Azure Cosmos DB server del. 

#### <a name="can-i-deploy-azure-managed-instance-for-apache-cassandra-in-any-region"></a>Kan jag Distribuera Azure Managed instance för Apache Cassandra i valfri region?

Den hanterade instansen är tillgänglig i ett begränsat antal regioner under för hands versionen.

### <a name="what-are-the-storage-and-throughput-limits-of-azure-managed-instance-for-apache-cassandra"></a>Vilka är lagrings-och data flödes gränserna för Azure Managed instance för Apache Cassandra?

De här begränsningarna beror på de virtuella dator-SKU: er du väljer.

### <a name="what-is-the-cost-of-azure-managed-instance-for-apache-cassandra"></a>Vad kostar Azure Managed instance för Apache Cassandra?

De hanterade instans avgifterna baseras på den underliggande kostnaden för den virtuella datorn med en liten markering. Mer information finns på sidan med [priser](https://azure.microsoft.com/pricing/details/managed-instance-apache-cassandra/).

### <a name="can-i-use-yaml-file-settings-to-configure-behavior"></a>Kan jag använda YAML-filinställningar för att konfigurera beteendet?

Ja, du kan bädda in YAML-filkonfigurationer som en del av en Azure Resource Manager mall-distribution.

### <a name="how-can-i-monitor-infrastructure-along-with-throughput"></a>Hur kan jag övervaka infrastrukturen tillsammans med data flöde?

[Prometheus](https://prometheus.io/docs/introduction/overview/) -servern är värd för att övervaka aktivitet i klustret och exponera en slut punkt. Detta bibehåller 10 minuter eller 10 GB data (vilket tröskelvärde som uppnås först). Om du vill använda den här övervakningen måste du konfigurera en [Federation](https://prometheus.io/docs/prometheus/latest/federation/) och ett lämpligt instrument panels verktyg, till exempel Grafana.

### <a name="does-azure-managed-instance-for-apache-cassandra-provide-full-backups"></a>Tillhandahåller Azure Managed instance för Apache Cassandra fullständiga säkerhets kopior?

Ja, den innehåller fullständiga säkerhets kopieringar för att Azure Storage och återställas till ett nytt kluster

### <a name="how-can-i-migrate-data-from-my-existing-apache-cassandra-cluster-to-azure-managed-instance-for-apache-cassandra"></a>Hur migrerar jag data från mitt befintliga Apache Cassandra-kluster till Azure Managed instance för Apache Cassandra?

Azure Managed instance för Apache Cassandra har stöd för alla funktioner i Apache Cassandra för att replikera och strömma data mellan data Center.

### <a name="can-i-pair-an-on-premises-apache-cassandra-cluster-with-the-azure-managed-instance-for-apache-cassandra"></a>Kan jag para ihop ett lokalt Apache Cassandra-kluster med den Azure-hanterade instansen för Apache Cassandra?

Ja, du kan konfigurera ett hybrid kluster med Azure Virtual Network inmatade Data Center som distribueras av tjänsten. Data Center för hanterade instanser kan kommunicera med lokala data Center inom samma kluster ring.

### <a name="where-can-i-give-feedback-on-azure-managed-instance-for-apache-cassandra-features"></a>Var kan jag ge feedback om Azure Managed instance för Apache Cassandra-funktioner?

Ge feedback via [feedback från användarens röst](https://feedback.azure.com/forums/263030-azure-cosmos-db?category_id=398548) med hjälp av kategorin "hanterad Apache Cassandra".

Om du vill åtgärda ett problem med ditt konto, skickar du in ett [supportärende](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) i Azure-portalen.

## <a name="deployment-specific-faq"></a>Vanliga frågor och svar om distribution

### <a name="will-the-managed-instance-support-node-addition-cluster-status-and-node-status-commands"></a>Kommer den hanterade instansen stöd för nod-, kluster-och status kommandon för noden?

Alla *skrivskyddade* Nodetool-kommandon som `status` är tillgängliga via Azure CLI. Åtgärder som *Node addition* är dock inte tillgängliga eftersom vi hanterar hälsan för noder i den hanterade instansen. I hybrid läge kan du ansluta till klustret med *Nodetool*. Att använda Nodetool rekommenderas dock inte, eftersom det kan göra klustret. Det kan också ogiltig förklara eventuella service avtal för produktions support som rör hälsan för data Center för hanterade instanser i klustret.

### <a name="what-happens-with-various-settings-for-table-metadata"></a>Vad händer med olika inställningar för metadata i tabeller?

Inställningarna för tabellens metadata, till exempel blomma-filter, cachelagring, läsa reparation, gc_grace och komprimerings memtable_flush_period, stöds fullt ut av alla egna värdbaserade Apache Cassandra-miljöer.

## <a name="next-steps"></a>Nästa steg

Om du vill veta mer om vanliga frågor och svar i andra API: er, se:

* [Skapa ett hanterat instans kluster från Azure Portal](create-cluster-portal.md)
* [Distribuera ett hanterat Apache Spark-kluster med Azure Databricks](deploy-cluster-databricks.md)
* [Hantera Azure-hanterad instans för Apache Cassandra-resurser med hjälp av Azure CLI](manage-resources-cli.md)