---
title: Nodkonfigurationer som stöds av Azure HDInsight
description: Läs om minsta och rekommenderade konfigurationer för HDInsight-klusternoder.
keywords: VM-storlekar, kluster storlekar, kluster konfiguration
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 05/14/2020
ms.openlocfilehash: d41ee2554d30a56bc2e025bbe2c93aee143d75e8
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98931642"
---
# <a name="what-are-the-default-and-recommended-node-configurations-for-azure-hdinsight"></a>Vilka är standard och rekommenderade nodkonfigurationer för Azure HDInsight?

I den här artikeln beskrivs standard-och rekommenderade nodkonfigurationer för Azure HDInsight-kluster.

## <a name="default-and-minimum-recommended-node-configuration-and-virtual-machine-sizes-for-clusters"></a>Standard och minsta rekommenderade konfiguration av noder och storlek för virtuella datorer för kluster

I följande tabell listas standard-och rekommenderade storlekar för virtuella datorer för HDInsight-kluster.  Den här informationen är nödvändig för att förstå de VM-storlekar som ska användas när du skapar PowerShell-eller Azure CLI-skript för att distribuera HDInsight-kluster.

Om du behöver fler än 32 arbetsnoder i ett kluster väljer du en head-nods storlek med minst 8 kärnor och 14 GB RAM.

De enda kluster typerna som innehåller data diskar är Kafka-och HBase-kluster med funktionen för accelererade skrivningar aktiverade. HDInsight stöder P30-och S30 disk storlekar i dessa scenarier. För alla andra kluster typer tillhandahåller HDInsight hanterat disk utrymme med klustret. Från 11/07/2019 är den hanterade disk storleken för varje nod i det nya klustret 128 GB. Detta kan inte ändras.

Specifikationerna för alla minsta rekommenderade VM-typer som används i det här dokumentet sammanfattas i följande tabell.

| Storlek              | Virtuell processor | Minne: GiB | Temporär lagring (SSD) GiB | Maximalt genomflöde för temporär lagring: IOPS / Mbit/s för läsning / M/bit/s för skrivning | Maximalt antal datadiskar/dataflöde: IOPS | Högsta antal nätverkskort/förväntad nätverks bandbredd (Mbit/s) |
|-------------------|-----------|-------------|----------------|----------------------------------------------------------|-----------------------------------|------------------------------|
| Standard_D3_v2 | 4    | 14          | 200                    | 12 000 / 187 / 93                                           | 16/16x500           | 4/3 000                                       |
| Standard_D4_v2 | 8    | 28          | 400                    | 24 000 / 375 / 187                                          | 32/32x500           | 8/6 000                                       |
| Standard_D5_v2 | 16   | 56          | 800                    | 48 000 / 750 / 375                                          | 64/64x500           | 8 / 12000                                    |
| Standard_D12_v2   | 4         | 28          | 200            | 12 000 / 187 / 93                                         | 16 / 16 x 500                         | 4/3 000                     |
| Standard_D13_v2   | 8         | 56          | 400            | 24 000 / 375 / 187                                        | 32 / 32 x 500                       | 8/6 000                     |
| Standard_D14_v2   | 16        | 112         | 800            | 48 000 / 750 / 375                                        | 64/64x500                       | 8 / 12000          |
| Standard_A1_v2  | 1         | 2           | 10             | 1 000 / 20 / 10                                           | 2 / 2 x 500               | 2/250                 |
| Standard_A2_v2  | 2         | 4           | 20             | 2 000 / 40 / 20                                           | 4 / 4 x 500               | 2/500                 |
| Standard_A4_v2  | 4         | 8           | 40             | 4 000 / 80 / 40                                           | 8 / 8 x 500               | 4/1 000                     |

Mer information om specifikationerna för varje VM-typ finns i följande dokument:

* [Generell användnings storlekar för virtuella datorer: Dv2-serien 1-5](../virtual-machines/dv2-dsv2-series.md)
* [Minnesoptimerade storlekar för virtuella datorer: Dv2-serien 11-15](../virtual-machines/dv2-dsv2-series-memory.md)
* [Generell användnings storlekar för virtuella datorer: AV2-serien 1-8](../virtual-machines/av2-series.md)

### <a name="all-supported-regions-except-brazil-south-and-japan-west"></a>Alla regioner som stöds förutom Brasilien, södra och Japan, väst

> [!Note]
> Du kan hämta SKU-identifieraren för användning i PowerShell och andra skript genom att lägga till i `Standard_` början av alla VM-SKU: er i tabellerna nedan. Till exempel `D12_v2` skulle bli `Standard_D12_v2` .

| Kluster typ | Hadoop | HBase | Interaktiv fråga | Storm | Spark | ML Server | Kafka |
|---|---|---|---|---|---|---|---|
| Huvud: storlek på virtuell dator | D12_v2 | D12_v2 | D13_v2 | A4_v2 | D12_v2, <br/>D13_v2 * | D12_v2 | D3_v2 |
| Head: minsta rekommenderade storlekar för virtuella datorer | D5_v2 | D3_v2 | D13_v2 | A4_v2 | D12_v2, <br/>D13_v2 * | D12_v2 | D3_v2 |
| Anställd: standard storlek för virtuell dator | D4_v2 | D4_v2 | D14_v2 | D3_v2 | D13_v2 | D4_v2 | 4 D12_v2 med 2 S30 diskar per Broker |
| Arbetare: minsta rekommenderade VM-storlek | D5_v2 | D3_v2 | D13_v2 | D3_v2 | D12_v2 | D4_v2 | D3_v2 |
| ZooKeeper: standard storlek för virtuell dator |  | A4_v2 | A4_v2 | A4_v2 |  | A2_v2 | A4_v2 |
| ZooKeeper: minsta rekommenderade VM-storlekar |  | A4_v2 | A4_v2 | A2_v2 |  | A2_v2 | A4_v2 |
| ML-tjänster: standard storlek för virtuell dator |  |  |  |  |  | D4_v2 |  |
| ML-tjänster: minsta rekommenderade VM-storlek |  |  |  |  |  | D4_v2 |  |

\* = VM-storlekar för Spark Enterprise Security Package (ESP)-kluster

### <a name="brazil-south-and-japan-west-only"></a>Endast Brasilien, södra och Japan, väst

| Kluster typ | Hadoop | HBase | Interaktiv fråga | Storm | Spark | ML Services |
|---|---|---|---|---|---|---|
| Huvud: storlek på virtuell dator | D12 | D12 | D13 | A4_v2 | D12 | D12 |
| Head: minsta rekommenderade storlekar för virtuella datorer | D5_v2 | D3_v2 | D13_v2 | A4_v2 | D12_v2 | D12_v2 |
| Anställd: standard storlek för virtuell dator | D4 | D4 | D14 | D3 | D13 | D4 |
| Arbetare: minsta rekommenderade VM-storlek | D5_v2 | D3_v2 | D13_v2 | D3_v2 | D12_v2 | D4_v2 |
| ZooKeeper: standard storlek för virtuell dator |  | A4_v2 | A4_v2 | A4_v2 |  | A2_v2 |
| ZooKeeper: minsta rekommenderade VM-storlekar |  | A4_v2 | A4_v2 | A4_v2 |  | A2_v2 |
| ML-tjänster: standard storlekar för virtuella datorer |  |  |  |  |  | D4 |
| ML-tjänster: minsta rekommenderade VM-storlekar |  |  |  |  |  | D4_v2 |

> [!NOTE]
> - Head är känt som *Nimbus* för Storm-kluster typen.
> - Arbetaren är känd som *administratör* för Storm-klustret.
> - Worker är känt som *region* för kluster typen HBase.

## <a name="next-steps"></a>Nästa steg

* [Vilka Apache Hadoops komponenter och versioner är tillgängliga med HDInsight?](hdinsight-component-versioning.md)
