---
title: Apache Hadoop komponenter och versioner – Azure HDInsight
description: Lär dig mer om Apache Hadoop-komponenter och-versioner i Azure HDInsight.
ms.service: hdinsight
ms.topic: conceptual
ms.custom: deshriva
ms.date: 02/08/2021
ms.openlocfilehash: 166c52660c9f47b8ecfce10f7e9c3b81ff067f35
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/03/2021
ms.locfileid: "101699376"
---
# <a name="azure-hdinsight-versions"></a>Azure HDInsight-versioner

HDInsight paketerar Apache Hadoop miljö komponenter och HDInsight-plattform i ett paket som har distribuerats i ett kluster. Mer information finns i [så här fungerar HDInsight-versioner](hdinsight-overview-versioning.md).

## <a name="supported-hdinsight-versions"></a>HDInsight-versioner som stöds

I den här tabellen listas de versioner av HDInsight som är tillgängliga i Azure Portal och andra distributions metoder som PowerShell, CLI och .NET SDK.

| HDInsight-version | OPERATIVSYSTEM FÖR VIRTUELL DATOR | Utgivningsdatum | Förfallo datum för support | Datum för indragning | Hög tillgänglighet |
| --- | --- | --- | --- | --- | --- |
| [HDInsight 4.0](hdinsight-40-component-versioning.md) |Ubuntu 16.0.4-LTS |September 24, 2018 | | |Ja |
| [HDInsight 3,6](hdinsight-36-component-versioning.md) |Ubuntu 16.0.4-LTS |4 april 2017      | * 30 juni 2021 |30 juni 2021 |Ja |

* Vi utökar support tidsram för vissa kluster typer för HDInsight 3,6. Se [HDInsight 3,6-komponent versioner](hdinsight-36-component-versioning.md).

## <a name="release-notes"></a>Viktig information

Mer information om den senaste versionen av HDInsight finns i [versions anmärkningar för HDInsight](hdinsight-release-notes.md).

## <a name="support-options-for-hdinsight-versions"></a>Support alternativ för HDInsight-versioner

HDInsight erbjuder standard support som definieras som en tids period som en HDInsight-version stöds av Microsofts kund tjänst och support.

**Stöd för förfallo datum** innebär att Microsoft inte längre har stöd för den aktuella HDInsight-versionen. Och det är inte längre tillgängligt via Azure Portal för skapande av kluster.

**Pensionering** innebär att befintliga kluster i en HDInsight-version fortsätter att köras som de är. Nya kluster av den här versionen kan inte skapas på något sätt, vilket inkluderar CLI och SDK: er. Andra kontroll Plans funktioner, till exempel manuell skalning och autoskalning, är inte garanterat att fungera efter indragnings datumet. Support är inte tillgängligt för invecklade versioner.

## <a name="versioning-considerations"></a>Överväganden vid versions hantering
- När ett kluster har distribuerats med en avbildning, uppgraderas inte klustret automatiskt till en nyare avbildnings version. När du skapar nya kluster distribueras den senaste avbildnings versionen.
- Kunderna bör testa och verifiera att programmen körs korrekt när de använder den nya HDInsight-versionen.
- HDInsight förbehåller sig rätten att ändra standard versionen utan föregående meddelande. Om du har ett versions beroende anger du HDInsight-versionen när du skapar klustren.
- HDInsight kan dra tillbaka en version av OSS-komponenten innan du tar ur HDInsight-versionen.

## <a name="next-steps"></a>Nästa steg

- [Kluster konfiguration för Apache Hadoop, Spark och mer på HDInsight](hdinsight-hadoop-provision-linux-clusters.md)
- [Enterprise Security Package](./enterprise-security-package.md)
- [Arbeta i Apache Hadoop på HDInsight från en Windows-dator](hdinsight-hadoop-windows-tools.md)