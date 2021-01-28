---
title: Azure HDInsight-accelererade skrivningar för Apache HBase
description: Ger en översikt över funktionen Azure HDInsight-accelererade skrivningar, som använder Premium-hanterade diskar för att förbättra prestanda för filen Apache HBase Write Ahead.
ms.service: hdinsight
ms.topic: how-to
ms.date: 01/24/2020
ms.openlocfilehash: 1f5a9f05b94f43db9e1d883ef5e86f8621f39251
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/28/2021
ms.locfileid: "98943031"
---
# <a name="azure-hdinsight-accelerated-writes-for-apache-hbase"></a>Azure HDInsight-accelererade skrivningar för Apache HBase

Den här artikeln innehåller en bakgrund för den **accelererade skrivningar** funktionen för Apache HBase i Azure HDInsight och hur den kan användas effektivt för att förbättra skriv prestanda. **Accelererade skrivningar** använder [Azure Premium SSD-hanterade diskar](../../virtual-machines/disks-types.md#premium-ssd) för att förbättra prestandan hos Apache HBase Write Ahead log (Wal). Mer information om Apache HBase finns i [Vad är Apache HBase i HDInsight](apache-hbase-overview.md).

## <a name="overview-of-hbase-architecture"></a>Översikt över HBase-arkitekturen

I HBase består en **rad** av en eller flera **kolumner** och identifieras av en **rad nyckel**. Flera rader utgör en **tabell**. Kolumner innehåller **celler**, som är tidsstämplade versioner av värdet i den kolumnen. Kolumner grupperas i **kolumn serier** och alla kolumner i en kolumn serie lagras tillsammans i lagringsfiler som kallas **HFiles**.

**Regioner** i HBase används för att balansera belastningen på data bearbetningen. HBase lagrar först raderna i en tabell i en enda region. Raderna sprids över flera regioner när mängden data i tabellen ökar. **Region servrar** kan hantera begär Anden för flera regioner.

## <a name="write-ahead-log-for-apache-hbase"></a>Skriv loggen i förväg för Apache HBase

HBase skriver först data uppdateringar till en typ av inchecknings logg som kallas för Skriv logg (WAL). När uppdateringen har lagrats i WAL skrivs den till InMemory- **memstores**. När data i minnet når sin maximala kapacitet, skrivs de till disk som en **HFile**.

Om en **RegionServer** kraschar eller blir otillgänglig innan memstores töms, kan Skriv loggen användas för att spela upp uppdateringar igen. Utan WAL, om en **RegionServer** kraschar innan uppdateringar av en **HFile** rensas, går alla dessa uppdateringar förlorade.

## <a name="accelerated-writes-feature-in-azure-hdinsight-for-apache-hbase"></a>Accelererade skrivningar i Azure HDInsight för Apache HBase

Funktionen accelererade skrivningar löser problemet med högre Skriv-fördröjning som orsakas av att skriva loggar som finns i moln lagring.  Funktionen accelererade skrivningar för HDInsight Apache HBase-kluster, ansluter Premium SSD-hanterade diskar till varje RegionServer (arbetsnoden). Skriv loggar i förväg skrivs sedan till Hadoop-filsystemet (HDFS) som är monterat på dessa Premium-hanterade diskar i stället för till moln lagring.  Premium-hanterade – diskar använder Solid-State diskar (SSD) och erbjuder utmärkt I/O-prestanda med fel tolerans.  Till skillnad från ohanterade diskar påverkar den inte andra lagrings enheter i samma tillgänglighets uppsättning om en lagrings enhet slutar fungera.  Det innebär att hanterade diskar ger låg Skriv fördröjning och bättre återhämtning för dina program. Mer information om Azure-hanterade diskar finns i [Introduktion till Azure Managed disks](../../virtual-machines/managed-disks-overview.md).

## <a name="how-to-enable-accelerated-writes-for-hbase-in-hdinsight"></a>Så här aktiverar du påskyndade skrivningar för HBase i HDInsight

Om du vill skapa ett nytt HBase-kluster med funktionen för accelererade skrivningar följer du stegen i [Konfigurera kluster i HDInsight](../hdinsight-hadoop-provision-linux-clusters.md). På fliken **grundläggande** inställningar väljer du kluster typ som HBase, anger en komponent version och klickar sedan på kryss rutan bredvid **Aktivera HBase-accelererade skrivningar**. Fortsätt sedan med de återstående stegen för att skapa kluster.

![Aktivera alternativ för påskyndad skrivning för HDInsight Apache HBase](./media/apache-hbase-accelerated-writes/azure-portal-create-hbase-wals.png)

## <a name="other-considerations"></a>Ytterligare överväganden

Skapa ett kluster med minst tre arbetsnoder för att bevara data hållbarhet. När du har skapat det går det inte att skala upp klustret till färre än tre arbetsnoder.

Töm eller inaktivera HBase-tabellerna innan du tar bort klustret så att du inte förlorar Skriv logg data.

```
flush 'mytable'
```

```
disable 'mytable'
```

Följ liknande steg när du skalar ned klustret: Töm tabellerna och inaktivera dina tabeller för att stoppa inkommande data. Du kan inte skala ned klustret till färre än tre noder.

Genom att följa dessa steg kan du se till att det går att skala ned och undvika möjligheten för en namenode att försättas i fel säkert läge på grund av replikerade eller temporära filer.

Om din namenode går in i fel säkert läge efter en nedskalning, använder du HDFS-kommandon för att replikera de replikerade blocken på nytt och få HDFS ur fel säkert läge. Med den här omreplikeringen kan du starta om HBase.

## <a name="next-steps"></a>Nästa steg

* Officiell Apache HBase-dokumentation om [funktionen Skriv logg](https://hbase.apache.org/book.html#wal)
* Om du vill uppgradera ditt HDInsight Apache HBase-kluster för att använda accelererade skrivningar, se [Migrera ett Apache HBase-kluster till en ny version](apache-hbase-migrate-new-version.md).
