---
title: Apache Hadoop komponenter och versioner – Azure HDInsight
description: Lär dig mer om Apache Hadoop-komponenter och-versioner i Azure HDInsight.
ms.service: hdinsight
ms.topic: conceptual
author: deshriva
ms.author: deshriva
ms.date: 02/08/2021
ms.openlocfilehash: dbd5b507fd4a7b2434158dbdc80584a7fd348732
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "105726591"
---
# <a name="azure-hdinsight-versions"></a>Azure HDInsight-versioner

HDInsight paketerar Apache Hadoop miljö komponenter och HDInsight-plattform i ett paket som har distribuerats i ett kluster. Mer information finns i [så här fungerar HDInsight-versioner](hdinsight-overview-versioning.md).

## <a name="supported-hdinsight-versions"></a>HDInsight-versioner som stöds

I den här tabellen listas de versioner av HDInsight som är tillgängliga i Azure Portal och andra distributions metoder som PowerShell, CLI och .NET SDK.

| HDInsight-version | OPERATIVSYSTEM FÖR VIRTUELL DATOR | Utgivningsdatum| Support typ | Förfallo datum för support | Datum för indragning | Hög tillgänglighet |
| --- | --- | --- | --- | --- | --- | ---|
| [HDInsight 4.0](hdinsight-40-component-versioning.md) |Ubuntu 16.0.4-LTS |September 24, 2018 | [Standard](hdinsight-component-versioning.md#support-options-for-hdinsight-versions) | | |Ja |
| [HDInsight 3,6](hdinsight-36-component-versioning.md) |Ubuntu 16.0.4-LTS |4 april 2017      | [Basic](hdinsight-component-versioning.md#support-options-for-hdinsight-versions) | Förfallo datum för standard support – 30 juni 2021 <br> Basic Support förfallo datum – 3 april 2022 |4 april 2022 |Ja |

* Från den 1 juli 2021 kommer Microsoft att erbjuda Basic Support för vissa typer av HDI 3,6-kluster. Se [HDInsight 3,6-komponent versioner](hdinsight-36-component-versioning.md).

## <a name="release-notes"></a>Viktig information

Mer information om den senaste versionen av HDInsight finns i [versions anmärkningar för HDInsight](hdinsight-release-notes.md).

## <a name="support-options-for-hdinsight-versions"></a>Support alternativ för HDInsight-versioner

Support definieras som en tids period som en HDInsight-version stöds av Microsofts kund tjänst och support. HDInsight erbjuder två typer av support: 
- **Standard Support** är en tids period då Microsoft tillhandahåller uppdateringar och support på HDInsight-kluster.  
    Vi rekommenderar att du skapar lösningar med den senaste versionen som stöds helt och hållet. 
- **Basic support** är en tids period under vilken Microsoft tillhandahåller begränsad service till HDInsight-resurs leverantören. HDInsight-avbildningar och program med öppen källkod (OSS) kommer inte att servas.   Endast viktiga säkerhets korrigeringar kommer att korrigeras i HDInsight-kluster.  
  Microsoft uppmuntrar inte att skapa nya kluster eller skapa nya lösningar när en version är i Basic Support. Vi rekommenderar att du migrerar befintliga kluster till den senaste fullständigt versionen som stöds. 

**Stöd för förfallo datum** innebär att Microsoft inte längre har stöd för den aktuella HDInsight-versionen. Och den kanske inte längre är tillgänglig via Azure Portal för skapande av kluster.

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
