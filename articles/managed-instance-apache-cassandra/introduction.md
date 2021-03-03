---
title: Introduktion till Azure Managed instance för Apache Cassandra
description: Lär dig mer om Azure Managed instance för Apache Cassandra. Den här tjänsten hanterar distributionen och skalningen av interna instanser med öppen källkod av Apache Cassandra i Azure.
author: TheovanKraay
ms.author: thvankra
ms.service: managed-instance-apache-cassandra
ms.topic: overview
ms.date: 03/02/2021
ms.openlocfilehash: d99c62e7d88510c351f87d85b4f8c5c271767cb3
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/03/2021
ms.locfileid: "101748932"
---
# <a name="what-is-azure-managed-instance-for-apache-cassandra-preview"></a>Vad är Azure Managed instance för Apache Cassandra? (Förhandsversion)

> [!IMPORTANT]
> Azure Managed instance för Apache Cassandra är för närvarande en offentlig för hands version.
> Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade.
> Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Den Azure-hanterade instansen för Apache Cassandra-tjänsten ger automatisk distribution och skalning av hanterade Apache Cassandra-datacenter med öppen källkod. Den här tjänsten hjälper dig att påskynda hybrid scenarier och minska det löpande underhållet. Den kommer att ha djupgående integrering och data flytt med [Azure Cosmos DB API för Cassandra](../cosmos-db/cassandra-introduction.md) på sin allmänna version.

:::image type="content" source="./media/introduction/icon.gif" alt-text="Den Azure-hanterade instansen för Apache Cassandra är en hanterad tjänst för Apache Cassandra." border="false":::

## <a name="key-benefits"></a>Viktiga fördelar

### <a name="hybrid-deployments"></a>Hybrid distributioner

Du kan använda den här tjänsten för att enkelt placera hanterade instanser av Apache Cassandra-datacenter, som distribueras automatiskt som skalnings uppsättningar för virtuella datorer i en ny eller befintlig Azure-Virtual Network. Dessa data Center kan läggas till i din befintliga Apache Cassandra-ring som körs lokalt via [Azure ExpressRoute](/azure/architecture/reference-architectures/hybrid-networking/expressroute) i Azure eller en annan moln miljö.

- **Förenklad distribution:** När hybrid anslutningen har upprättats är det enkelt genom Gossip-protokollet.
- **Värdbaserade mått:** Måtten är värdar i [Prometheus](https://prometheus.io/docs/introduction/overview/) för att övervaka aktiviteter i klustret.

### <a name="simplified-scaling"></a>Förenklad skalning

I den hanterade instansen hanteras upp-och nedskalning av noder i ett Data Center fullständigt. Du anger antalet noder som du behöver och skalnings tjänsten tar hand om att etablera sina åtgärder inom Cassandra-ringen.

### <a name="managed-and-cost-effective"></a>Hanterad och kostnads effektiv

Tjänsten tillhandahåller hanterings åtgärder för följande vanliga Apache Cassandra-uppgifter:

- Etablera ett kluster
- Etablera ett Data Center
- Skala ett Data Center
- Ta bort ett Data Center
- Starta en reparations åtgärd på ett tecken utrymme
- Ändra konfigurationen för ett Data Center
- Konfigurera säkerhets kopior

Pris sättnings modellen är flexibel, på begäran, instans-baserad och har inga licens avgifter. Med den här pris modellen kan du anpassa efter dina arbets belastnings behov. Du väljer hur många kärnor, vilken VM-SKU, vilken minnes storlek och disk utrymmes storlek du behöver.

## <a name="next-steps"></a>Nästa steg

Kom igång med en av våra snabb starter:

* [Skapa ett hanterat instans kluster från Azure Portal](create-cluster-portal.md)
* [Distribuera ett hanterat Apache Spark-kluster med Azure Databricks](deploy-cluster-databricks.md)
* [Hantera Azure-hanterad instans för Apache Cassandra-resurser med hjälp av Azure CLI](manage-resources-cli.md)
