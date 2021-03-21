---
title: Apache Spark prestanda – Azure HDInsight IO-cache (för hands version)
description: Lär dig mer om Azure HDInsight IO-cache och hur du använder den för att förbättra Apache Spark prestanda.
ms.service: hdinsight
ms.topic: how-to
ms.date: 12/23/2019
ms.openlocfilehash: 32f0756e59f81ad2c5d155581feed8c4fef09808
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98940127"
---
# <a name="improve-performance-of-apache-spark-workloads-using-azure-hdinsight-io-cache"></a>Förbättra prestanda för Apache Spark arbets belastningar med Azure HDInsight IO-cache

I/o-cache är en datacache-tjänst för Azure HDInsight som förbättrar prestanda för Apache Spark jobb. IO-cachen fungerar också med [Apache TEZ](https://tez.apache.org/) och [Apache Hive](https://hive.apache.org/) arbets belastningar som kan köras på [Apache Spark](https://spark.apache.org/) -kluster. I/o-cache använder en caching-komponent med öppen källkod som heter RubiX. RubiX är ett lokalt disk-cacheminne som används med Big data analys motorer som har åtkomst till data från moln lagrings system. RubiX är unikt bland caching-system, eftersom den använder Solid-State-enheter (SSD) i stället för att reservera operativ minne för cachelagring. I/o-Cache-tjänsten startar och hanterar RubiX-metadatafiler på varje arbets nod i klustret. Den konfigurerar även alla tjänster i klustret för genomskinlig användning av RubiX cache.

De flesta SSD tillhandahåller mer än 1 GByte per sekund för bandbredden. Den här bandbredden, kompletterad med operativ systemets minnesbaserade filcache, ger tillräckligt med bandbredd för att kunna läsa in bearbetnings motorer för Big data Compute, till exempel Apache Spark. Operativ minnet är tillgängligt för Apache Spark att bearbeta kraftigt minnes beroende aktiviteter, till exempel blandade. Med exklusiv användning av operativ minne kan Apache Spark uppnå optimal resursanvändning.  

> [!Note]  
> I/o-cachen använder för närvarande RubiX som en caching-komponent, men detta kan ändras i framtida versioner av tjänsten. Använd gränssnitt för IO-cachen och ta inga beroenden direkt i RubiX-implementeringen.
>IO-cache stöds bara med Azure BLOB Storage för tillfället.

## <a name="benefits-of-azure-hdinsight-io-cache"></a>Fördelar med Azure HDInsight IO-cache

Med hjälp av IO-cache får du bättre prestanda för jobb som läser data från Azure Blob Storage.

Du behöver inte göra några ändringar i Spark-jobben för att se prestandan ökar när du använder IO-cache. När IO-cachen är inaktive rad skulle den här Spark-koden läsa data via fjärr anslutning från Azure Blob Storage: `spark.read.load('wasbs:///myfolder/data.parquet').count()` . När IO-cachen har Aktiver ATS orsakar samma kodrad en cachelagrad läsning via IO-cache. I följande läsningar läses data lokalt från SSD. Arbetsnoder i HDInsight-kluster är utrustade med lokalt anslutna SSD-enheter. HDInsight IO-cachen använder de här lokala SSD för cachelagring, som tillhandahåller den lägsta svars nivån och maximerar bandbredden.

## <a name="getting-started"></a>Komma igång

Azure HDInsight IO-cachen inaktive ras som standard i för hands versionen. I/o-cache finns i Azure HDInsight 3.6 + Spark-kluster som kör Apache Spark 2,3.  Gör så här för att aktivera IO-cache i HDInsight 4,0:

1. I en webbläsare går du till `https://CLUSTERNAME.azurehdinsight.net` , där `CLUSTERNAME` är namnet på klustret.

1. Välj Cache-tjänsten för **IO** till vänster.

1. Välj **åtgärder** (**tjänst åtgärder** i HDI 3,6) och **Aktivera**.

    ![Aktivera IO-Cache-tjänsten i Ambari](./media/apache-spark-improve-performance-iocache/ambariui-enable-iocache.png "Aktivera IO-Cache-tjänsten i Ambari")

1. Bekräfta omstart av alla berörda tjänster i klustret.

> [!NOTE]  
> Även om förlopps indikatorn visar aktive rad aktive ras inte IO-cache förrän du startar om de andra berörda tjänsterna.

## <a name="troubleshooting"></a>Felsökning
  
Du kan få disk utrymmes fel som kör Spark-jobb när du har aktiverat IO-cache. Felen uppstår eftersom Spark också använder lokal disk lagring för att lagra data under blandning åtgärder. Spark-utrymmet kan ta slut i SSD när IO-cache är aktiverat och utrymmet för Spark-lagring minskas. Mängden utrymme som används av IO-cache-standardvärdet är hälften av det totala SSD-utrymmet. Disk utrymmes användningen för IO-cache kan konfigureras i Ambari. Om du får disk utrymmes fel minskar du mängden SSD-utrymme som används för IO-cache och startar om tjänsten. Gör så här om du vill ändra utrymmes uppsättningen för IO-cache:

1. I Apache Ambari väljer du tjänsten **HDFS** till vänster.

1. Välj flikarna **configs** och **Avancerat** .

    ![Redigera HDFS, Avancerad konfiguration](./media/apache-spark-improve-performance-iocache/ambariui-hdfs-service-configs-advanced.png "Redigera HDFS, Avancerad konfiguration")

1. Rulla nedåt och expandera området för **anpassad kärn webbplats** .

1. Leta upp egenskapen **Hadoop. cache. Fully. procent**.

1. Ändra värdet i rutan.

    ![Redigera full procent andel av IO-cachen](./media/apache-spark-improve-performance-iocache/ambariui-cache-data-fullness-percentage-property.png "Redigera full procent andel av IO-cachen")

1. Välj **Spara** längst upp till höger.

1. Välj **Starta** om  >  **omstart alla påverkade**.

    ![Apache Ambari startar om alla berörda](./media/apache-spark-improve-performance-iocache/ambariui-restart-all-affected.png "Starta om alla berörda")

1. Välj **Bekräfta omstart av alla**.

Om det inte fungerar inaktiverar du IO-cache.

## <a name="next-steps"></a>Nästa steg

Läs mer om IO-cache, inklusive prestandatest i det här blogg inlägget: [Apache Spark jobb får upp till 9x snabbare med HDInsight IO-cache](https://azure.microsoft.com/blog/apache-spark-speedup-with-hdinsight-io-cache/)
