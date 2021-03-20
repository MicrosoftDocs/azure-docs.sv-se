---
title: Så här väljer du rätt VM-storlek för ditt Azure HDInsight-kluster
description: Lär dig hur du väljer rätt VM-storlek för ditt HDInsight-kluster.
keywords: VM-storlekar, kluster storlekar, kluster konfiguration
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 10/09/2019
ms.openlocfilehash: 51043f0a1009994528783a1b56ec5ccec68e99b3
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98931788"
---
# <a name="selecting-the-right-vm-size-for-your-azure-hdinsight-cluster"></a>Välja rätt VM-storlek för ditt Azure HDInsight-kluster

Den här artikeln beskriver hur du väljer rätt VM-storlek för de olika noderna i ditt HDInsight-kluster. 

Börja med att förstå hur egenskaperna för en virtuell dator, till exempel processor bearbetning, RAM-storlek och nätverks fördröjning påverkar bearbetningen av dina arbets belastningar. Sedan ska du tänka på ditt program och hur det matchar med vilka olika VM-familjer är optimerade för. Kontrol lera att den VM-familj som du vill använda är kompatibel med den kluster typ som du planerar att distribuera. En lista över alla stödda och rekommenderade storlekar för virtuella datorer för varje kluster typ finns i [konfigurationer för Azure HDInsight-stödda noder](hdinsight-supported-node-configuration.md). Slutligen kan du använda en benchmarking-process för att testa vissa exempel arbets belastningar och kontrol lera vilka SKU: er som ingår i familjen.

Mer information om hur du planerar andra aspekter av klustret, till exempel att välja lagrings typ eller kluster storlek, finns i [kapacitets planering för HDInsight-kluster](hdinsight-capacity-planning.md).

## <a name="vm-properties-and-big-data-workloads"></a>VM-egenskaper och stora data arbets belastningar

VM-storlek och-typ bestäms av processor processor kraft, RAM-storlek och nätverks fördröjning:

- CPU: storleken på den virtuella datorn avgör antalet kärnor. Fler kärnor, desto högre grad av parallell beräkning som varje nod kan uppnå. Vissa VM-typer har dessutom snabbare kärnor.

- RAM: den virtuella datorns storlek avgör också mängden tillgängligt RAM-minne på den virtuella datorn. För arbets belastningar som lagrar data i minnet för bearbetning, i stället för att läsa från disk, se till att arbetsnoderna har tillräckligt med minne för att passa data.

- Nätverk: för de flesta kluster typer finns inte de data som bearbetas av klustret på en lokal disk, utan i stället i en extern lagrings tjänst som Data Lake Storage eller Azure Storage. Överväg nätverks bandbredden och data flödet mellan den virtuella noden och lagrings tjänsten. Nätverks bandbredden som är tillgänglig för en virtuell dator ökar vanligt vis med större storlekar. Mer information finns i [Översikt över VM-storlekar](../virtual-machines/sizes.md).

## <a name="understanding-vm-optimization"></a>Förstå optimering av virtuella datorer

Virtuella dator familjer i Azure är optimerade för att passa olika användnings fall. I tabellen nedan kan du hitta några av de mest populära användnings fallen och de VM-familjer som matchar dem.

| Typ                     | Storlekar           |    Beskrivning       |
|--------------------------|-------------------|------------------------------------------------------------------------------------------------------------------------------------|
| [Inmatnings nivå](../virtual-machines/sizes-general.md)          | A, AV2  | Ha processor prestanda-och minneskonfigurationer som är bäst lämpade för arbets belastningar på ingångs nivå som utveckling och testning. De är ekonomiska och ger ett billigt alternativ för att komma igång med Azure. |
| [Generell användning](../virtual-machines/sizes-general.md)          | D, DSv2, Dv2  | Balanserat förhållande mellan processor och minne. Utmärkt för testning och utveckling, små till medelstora databaser och webbservrar med låg till medelhög trafik. |
| [Beräkningsoptimerad](../virtual-machines/sizes-compute.md)        | F           | Högt förhållande mellan processor och minne. Passar för medel stora trafik webb servrar, nätverks enheter, batch-processer och program servrar.        |
| [Minnesoptimerad](../virtual-machines/sizes-memory.md)         | Esv3, Ev3  | Högt förhållande mellan minne och processor. Utmärkt för relationsdatabasservrar, mellanstora till stora cacheminnen och minnesinterna analyser.                 |

- Information om priser för tillgängliga VM-instanser i regioner som stöds av HDInsight finns i avsnittet om [priser för HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/).

## <a name="cost-saving-vm-types-for-light-workloads"></a>Kostnads besparing av VM-typer för lätta arbets belastningar

Om du har behov av ljus behandling kan [F-serien](https://azure.microsoft.com/blog/f-series-vm-size/) vara ett bra val för att komma igång med HDInsight. F-serien har ett lägre listpris per timme och har bästa prisprestanda i Azure-portföljen baserat på Azure-beräkningsenhet (ACU, Azure Compute Unit) per virtuell processor.

I följande tabell beskrivs de olika typerna av kluster och nodtyper som kan skapas med virtuella datorer i Fsv2-serien.

| Typ av kluster | Version | Arbetsnoden | Head-nod | Zookeeper-nod |
|---|---|---|---|---|
| Spark | Alla | F4 och över | nej | nej |
| Hadoop | Alla | F4 och över | nej | nej |
| Kafka | Alla | F4 och över | nej | nej |
| HBase | Alla | F4 och över | nej | nej |
| LLAP | inaktiverats | nej | nej | nej |
| Storm | inaktiverats | nej | nej | nej |
| ML-tjänst | HDI 3,6 ENDAST | F4 och över | nej | nej |

För att se specifikationerna för varje SKU för F-serien, se [VM-storlekar i f-serien](https://azure.microsoft.com/blog/f-series-vm-size/).

## <a name="benchmarking"></a>Prestanda mätningar

Benchmarking är en process för att köra simulerade arbets belastningar på olika virtuella datorer för att mäta hur bra de ska utföras för dina produktions arbets belastningar. 

Mer information om benchmarking för VM SKU: er och kluster storlekar finns i [kluster kapacitets planering i Azure HDInsight ](hdinsight-capacity-planning.md#choose-the-vm-size-and-type).

## <a name="next-steps"></a>Nästa steg

- [Nodkonfigurationer som stöds av Azure HDInsight](hdinsight-supported-node-configuration.md)
- [Sizes for Linux virtual machines in Azure](../virtual-machines/sizes.md)