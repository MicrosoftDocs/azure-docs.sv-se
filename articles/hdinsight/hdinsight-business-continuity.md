---
title: Affärs kontinuitet i Azure HDInsight
description: Den här artikeln ger en översikt över bästa praxis, tillgänglighet för enskild region och optimerings alternativ för Azure HDInsight-planering för affärs kontinuitet.
keywords: Hadoop hög tillgänglighet
ms.service: hdinsight
ms.topic: conceptual
ms.date: 10/08/2020
ms.openlocfilehash: 74f8bdd26e000b89bfae84102077c241f85abf7e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "98933317"
---
# <a name="azure-hdinsight-business-continuity"></a>Affärs kontinuitet i Azure HDInsight

Azure HDInsight-kluster är beroende av många Azure-tjänster som lagring, databaser, Active Directory, Active Directory Domain Services, nätverk och Key Vault. En väl utformad, hög tillgänglig och feltolerant analys applikation bör utformas med tillräcklig redundans för att motstå regionala eller lokala störningar i en eller flera av dessa tjänster. Den här artikeln ger en översikt över bästa praxis, tillgänglighet för enskild region och optimerings alternativ för affärs kontinuitets planering.

## <a name="general-best-practices"></a>Allmän bästa praxis

I det här avsnittet beskrivs några av de bästa metoderna för att tänka på vid planering av affärs kontinuitet.

* Fastställ de minimala affärs funktioner du behöver om det finns en katastrof och varför. Du kan till exempel utvärdera om du behöver redundans för data omvandlings skiktet (visas i gult) *och* dataservning (visas i blått) eller om du bara behöver redundans för data tjänst skiktet.

   :::image type="content" source="media/hdinsight-business-continuity/data-layers.png" alt-text="data omvandling och data som betjänar lager":::

* Segmentera dina kluster baserat på arbets belastning, utvecklings livs cykel och avdelningar. Om du har fler kluster minskar risken för att ett enda stort fel påverkar flera olika affärs processer.

* Gör dina sekundära regioner skrivskyddade. Växlings regioner med både Läs-och skriv funktioner kan leda till komplexa arkitekturer.

* Det är enklare att hantera tillfälliga kluster när det finns en katastrof. Utforma dina arbets belastningar på ett sätt som kluster kan användas i och inget tillstånd bevaras i kluster.

* Arbets belastningar lämnas ofta oavslutade om det finns en katastrof och behöver startas om i den nya regionen. Utforma dina arbets belastningar så att de blir idempotenta.

* Använd Automation under kluster distributioner och se till att inställningarna för kluster konfiguration är så långt som möjligt för att säkerställa snabb och helt automatiserad distribution om det finns en katastrof.

* Använd Azures övervaknings verktyg i HDInsight för att identifiera onormalt beteende i klustret och ange motsvarande aviserings aviseringar. Du kan distribuera förkonfigurerade HDInsight-företagsspecifika hanterings lösningar som samlar in viktiga prestanda mått för den aktuella kluster typen. Mer information finns i [Azure-övervakning för HDInsight](./hdinsight-hadoop-oms-log-analytics-tutorial.md).  

* Prenumerera på Azures hälso aviseringar för att få meddelanden om service problem, planerat underhåll, hälso-och säkerhets rekommendationer för en prenumeration, tjänst eller region. Hälso aviseringar som innehåller orsaken till problemet och Resolute ETA-tjänsten hjälper dig att bättre köra redundans och förlita. Mer information finns i [Azure Service Health-dokumentationen](../service-health/index.yml).

## <a name="single-region-availability"></a>Tillgänglighet för enskild region

Ett grundläggande HDInsight-system har följande komponenter. Alla komponenter har sina egna fel tolerans metoder för en region.

* Compute (Virtual Machines): Azure HDInsight-kluster
* Metaarkiv (er): Azure SQL Database
* Lagring: Azure Data Lake Gen2-eller Blob-lagring
* Autentisering: Azure Active Directory, Azure Active Directory Domain Services, Enterprise Security Package
* Domän namns matchning: Azure DNS

Det finns andra valfria tjänster som kan användas, till exempel Azure Key Vault och Azure Data Factory.

:::image type="content" source="media/hdinsight-business-continuity/hdinsight-components.png" alt-text="HDInsight-komponenter":::

### <a name="azure-hdinsight-cluster-compute"></a>Azure HDInsight-kluster (Compute)

HDInsight erbjuder en tillgänglighets-SLA på 99,9%. För att tillhandahålla hög tillgänglighet i en enda distribution åtföljs HDInsight av många tjänster som är i läget för hög tillgänglighet som standard. Fel tolerans metoder i HDInsight tillhandahålls av både Microsoft och Apache OSS OSS-eko tjänster med hög tillgänglighet.

Följande tjänster är utformade för hög tillgänglighet:

#### <a name="infrastructure"></a>Infrastruktur

* Aktiva och vänte läge huvudnoderna
* Flera gateway-noder
* Tre Zookeeper-kvorumresurser
* Arbetsnoder distribuerade av fel-och uppdaterings domäner

#### <a name="service"></a>Tjänst

* Apache Ambari-Server
* Program tids linje Severs för garn
* Jobb historik Server för Hadoop-MapReduce
* Apache Livy
* HDFS
* GARN resurs hanterare
* HBase Master

Mer information finns i dokumentationen om [tjänster med hög tillgänglighet som stöds av Azure HDInsight](./hdinsight-high-availability-components.md) .

Det tar inte alltid en oåterkallelig händelse att påverka affärs funktionerna. Tjänst incidenter i en eller flera av följande tjänster i en enda region kan också leda till att förväntade företags funktioner går förlorade.

### <a name="hdinsight-metastore"></a>HDInsight-metaarkiv

HDInsight använder [Azure SQL Database](https://azure.microsoft.com/support/legal/sla/sql-database/v1_4/) som en metaarkiv, som tillhandahåller ett service avtal på 99,99%. Tre repliker av data finns kvar i ett Data Center med synkron replikering. Om det finns en replik förlust, hanteras en alternativ replik sömlöst. [Aktiv geo-replikering](../azure-sql/database/active-geo-replication-overview.md) stöds i rutan med högst fyra Data Center. När det finns en växling vid fel, antingen manuellt eller i Data Center, blir den första repliken i hierarkin automatiskt av Läs-och skriv kapacitet. Mer information finns i [Azure SQL Database affärs kontinuitet](../azure-sql/database/business-continuity-high-availability-disaster-recover-hadr-overview.md).

### <a name="hdinsight-storage"></a>HDInsight Storage

HDInsight rekommenderar Azure Data Lake Storage Gen2 som underliggande lagrings lager. [Azure Storage](https://azure.microsoft.com/support/legal/sla/storage/v1_5/), inklusive Azure Data Lake Storage Gen2, tillhandahåller ett service avtal på 99,9%. HDInsight använder LRS-tjänsten där tre repliker av data finns kvar i ett Data Center, och replikeringen är synkron. När det finns en replik förlust, hanteras en replik sömlöst.

### <a name="azure-active-directory"></a>Azure Active Directory

[Azure Active Directory](https://azure.microsoft.com/support/legal/sla/active-directory/v1_0/) tillhandahåller ett service avtal på 99,9%. Active Directory är en global tjänst med flera nivåer av intern redundans och automatisk återhämtning. Mer information finns i hur [Microsoft i hela tiden förbättrar tillförlitligheten för Azure Active Directory](https://azure.microsoft.com/blog/advancing-azure-active-directory-availability/).

### <a name="azure-active-directory-domain-services-ad-ds"></a>Azure Active Directory Domain Services (AD DS)

[Azure Active Directory Domain Services](https://azure.microsoft.com/support/legal/sla/active-directory-ds/v1_0/) tillhandahåller ett service avtal på 99,9%. Azure AD DS är en tjänst med hög tillgänglighet som finns i globalt distribuerade Data Center. Replik uppsättningar är en förhands gransknings funktion i Azure AD DS som möjliggör geografisk haveri beredskap om en Azure-region försätts i offlineläge. Mer information finns i avsnittet [om repliker som anger koncept och funktioner för Azure Active Directory Domain Services](../active-directory-domain-services/concepts-replica-sets.md) för mer information.  

### <a name="azure-dns"></a>Azure DNS

[Azure DNS](https://azure.microsoft.com/support/legal/sla/dns/v1_1/) tillhandahåller ett service avtal på 100%. HDInsight använder Azure DNS på olika platser för domän namns matchning.

## <a name="multi-region-cost-and-complexity-optimizations"></a>Kostnads-och komplexitets optimeringar i flera regioner

Att förbättra affärs kontinuiteten med hjälp av haveri beredskap för hög tillgänglighet i flera regioner kräver arkitektur design av högre komplexitet och högre kostnad. Följande tabeller beskriver några tekniska områden som kan öka den totala ägande kostnaden.

### <a name="cost-optimizations"></a>Kostnads optimeringar

|Område|Orsak till kostnads eskalering|Optimerings strategier|
|----|------------------------|-----------------------|
|Datalagring|Duplicera primära data/tabeller i en sekundär region|Replikera endast granskade data|
|Utgående data|Utgående data överföringar i flera regioner kommer till ett pris. Läs rikt linjerna för bandbredds prissättning|Replikera endast granskade data för att minska det utgående utrymmet för regionen|
|Kluster beräkning|Ytterligare HDInsight-kluster/s i sekundär region|Använd automatiserade skript för att distribuera sekundär beräkning efter primärt haveri. Använd autoskalning för att hålla den sekundära kluster storleken minst. Använd billigare VM-SKU: er. Skapa sekundära zoner i regioner där VM-SKU: er kan rabatteras.|
|Autentisering |Nätverks scenarier i den sekundära regionen kommer att ådra sig ytterligare Azure AD DS-installationer|Undvik inställningar för flera användare i den sekundära regionen.|

### <a name="complexity-optimizations"></a>Optimering av komplexitet

|Område|Orsak till komplexitets eskalering|Optimerings strategier|
|----|------------------------|-----------------------|
|Läs skriv mönster |Kräver både primär och sekundär för läsning och skrivning aktiverat |Designa den sekundära för att vara skrivskyddad|
|Noll & RTO |Kräver noll data förlust (utfört = 0) och noll avbrotts tid (RTO = 0) |Skapa återställnings-och RTO på olika sätt för att minska antalet komponenter som behöver redundansväxlas.|
|Affärs funktioner |Kräver fullständiga affärs funktioner för primär i sekundär |Utvärdera om du kan köra med minimalt minsta kritiska del av affärs funktionerna i sekundär.|
|Anslutning |Kräver alla överordnade och underordnade system från primär plats för att ansluta till den sekundära också|Begränsa den sekundära anslutningen till en minimal, minimal mängd.|

## <a name="next-steps"></a>Nästa steg

Mer information om de objekt som beskrivs i den här artikeln finns i:

* [Azure HDInsight affärs kontinuitets arkitekturer](./hdinsight-business-continuity-architecture.md)
* [Fallstudie av hög tillgänglighet för Azure HDInsight-lösning](./hdinsight-high-availability-case-study.md)
* [Vad är Apache Hive och HiveQL på Azure HDInsight?](./hadoop/hdinsight-use-hive.md)