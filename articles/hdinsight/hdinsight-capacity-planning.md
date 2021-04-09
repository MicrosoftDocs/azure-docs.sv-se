---
title: Kapacitets planering för kluster i Azure HDInsight
description: Identifiera viktiga frågor för kapacitets-och prestanda planering för ett Azure HDInsight-kluster.
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 05/07/2020
ms.openlocfilehash: 45cfa90f2156dba87cbec2b6313bc24e5d030572
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "98933227"
---
# <a name="capacity-planning-for-hdinsight-clusters"></a>Kapacitetsplanering för HDInsight-kluster

Innan du distribuerar ett HDInsight-kluster bör du planera för den avsedda kluster kapaciteten genom att fastställa nödvändiga prestanda och skalning. Den här planeringen hjälper till att optimera både användbarhet och kostnader. Vissa kluster kapacitets beslut kan inte ändras efter distributionen. Om prestanda parametrarna ändras kan ett kluster demonteras och återskapas utan att lagra data går förlorade.

Viktiga frågor att be om kapacitets planering är:

* I vilken geografisk region ska du distribuera klustret?
* Hur mycket lagrings utrymme behöver du?
* Vilken kluster typ ska du distribuera?
* Vilken storlek och vilken typ av virtuell dator (VM) ska använda när klusternoderna används?
* Hur många arbetsnoder bör ditt kluster ha?

## <a name="choose-an-azure-region"></a>Välj en Azure-region

Azure-regionen fastställer var klustret är fysiskt allokerat. För att minimera svars tiden för läsningar och skrivningar bör klustret vara nära dina data.

HDInsight är tillgängligt i många Azure-regioner. För att hitta den närmaste regionen, se [produkt tillgänglighet per region](https://azure.microsoft.com/global-infrastructure/services/?products=hdinsight).

## <a name="choose-storage-location-and-size"></a>Välj lagrings plats och storlek

### <a name="location-of-default-storage"></a>Placering av standard lagring

Standard lagringen, antingen ett Azure Storage konto eller Azure Data Lake Storage, måste finnas på samma plats som klustret. Azure Storage är tillgängligt på alla platser. Data Lake Storage Gen1 är tillgängligt i vissa regioner – se aktuell [data Lake Storage tillgänglighet](https://azure.microsoft.com/global-infrastructure/services/?products=storage).

### <a name="location-of-existing-data"></a>Plats för befintliga data

Om du vill använda ett befintligt lagrings konto eller Data Lake Storage som ditt klusters standard lagrings utrymme måste du distribuera klustret på samma plats.

### <a name="storage-size"></a>Lagrings storlek

I ett distribuerat kluster kan du koppla ytterligare Azure Storage-konton eller komma åt andra Data Lake Storage. Alla dina lagrings konton måste finnas på samma plats som klustret. En Data Lake Storage kan vara på en annan plats, men fantastiska avstånd kan leda till en viss fördröjning.

Azure Storage har vissa [kapacitets begränsningar](../azure-resource-manager/management/azure-subscription-service-limits.md#storage-limits), medan data Lake Storage gen1 är nästan obegränsat.

Ett kluster har åtkomst till en kombination av olika lagrings konton. Vanliga exempel är:

* När mängden data är troligt vis större än lagrings kapaciteten för en enskild Blob Storage-behållare.
* När frekvensen för åtkomst till BLOB-behållaren kan överskrida tröskelvärdet där begränsning sker.
* När du vill göra data har du redan laddat upp till en BLOB-behållare som är tillgänglig för klustret.
* När du vill isolera olika delar av lagringen av säkerhets skäl eller för att förenkla administrationen.

Använd endast en behållare per lagrings konto för bättre prestanda.

## <a name="choose-a-cluster-type"></a>Välj en kluster typ

Kluster typen bestämmer arbets belastningen som HDInsight-klustret har kon figurer ATS för att köras. Typerna är [Apache Hadoop](./hadoop/apache-hadoop-introduction.md), [Apache Storm](./storm/apache-storm-overview.md), [Apache Kafka](./kafka/apache-kafka-introduction.md)eller [Apache Spark](./spark/apache-spark-overview.md). En detaljerad beskrivning av tillgängliga kluster typer finns i [Introduktion till Azure HDInsight](hdinsight-overview.md#cluster-types-in-hdinsight). Varje kluster typ har en angiven distributions sto pol Ogin som innehåller krav för storlek och antal noder.

## <a name="choose-the-vm-size-and-type"></a>Välj storlek och typ för virtuell dator

Varje kluster typ har en uppsättning nodtyper och varje nodtyp har olika alternativ för deras VM-storlek och-typ.

För att fastställa den optimala kluster storleken för ditt program kan du öka kluster kapaciteten och öka storleken enligt vad som anges. Du kan till exempel använda en simulerad arbets belastning eller en *Kanarie-fråga*. Kör dina simulerade arbets belastningar i olika storleks kluster. Öka storleken gradvis tills de avsedda prestandan har uppnåtts. En Kanarie-fråga kan infogas med jämna mellanrum bland de andra produktions frågorna för att visa om klustret har tillräckligt med resurser.

Mer information om hur du väljer rätt VM-familj för din arbets belastning finns i [välja rätt VM-storlek för klustret](hdinsight-selecting-vm-size.md).

## <a name="choose-the-cluster-scale"></a>Välj kluster skala

Ett klusters skala bestäms av antalet VM-noder. För alla kluster typer finns det nodtyper som har en bestämd skala, och nodtyper som stöder skalbarhet. Ett kluster kan till exempel kräva exakt tre [Apache ZooKeeper](https://zookeeper.apache.org/) noder eller två huvudnoder. Arbetsnoder som utför data bearbetning i en distribuerad mode-förmån från ytterligare arbetsnoder.

Beroende på din kluster typ ökar antalet arbetsnoder ytterligare beräknings kapacitet (till exempel fler kärnor). Fler noder kommer att öka det totala minne som krävs för hela klustret för att stödja minnes lagring av data som bearbetas. Precis som med valet av VM-storlek och-typ, uppnås vanligt vis att du väljer rätt kluster skala. Använd simulerade arbets belastningar eller Kanarie-frågor.

Du kan skala ut klustret så att det uppfyller kraven för hög belastning. Skala sedan tillbaka den när de extra noderna inte längre behövs. Med [funktionen](hdinsight-autoscale-clusters.md) för automatisk skalning kan du skala klustret automatiskt baserat på fördefinierade mått och tids inställningar. Mer information om hur du skalar klustren manuellt finns i [skala HDInsight-kluster](hdinsight-scaling-best-practices.md).

### <a name="cluster-lifecycle"></a>Kluster livs cykel

Du debiteras för ett klusters livstid. Om det bara finns vissa gånger som du behöver klustret, [skapar du kluster på begäran med hjälp av Azure Data Factory](hdinsight-hadoop-create-linux-clusters-adf.md). Du kan också skapa PowerShell-skript som etablerar och tar bort ditt kluster och sedan schemalägga dessa skript med hjälp av [Azure Automation](https://azure.microsoft.com/services/automation/).

> [!NOTE]  
> När ett kluster tas bort, tas även dess standard Hive-metaarkiv bort. Använd ett externt metadatalagret som Azure Database eller [Apache Oozie](https://oozie.apache.org/)om du vill spara metaarkiv för nästa kluster som skapas på nytt.

### <a name="isolate-cluster-job-errors"></a>Isolera fel i kluster jobb

Ibland kan fel uppstå på grund av parallell körningen av flera Maps och minska komponenter i ett kluster med flera noder. Testa distribuerad testning för att hjälpa till att isolera problemet. Köra samtidiga flera jobb på ett enda arbets nods kluster. Expandera sedan den här metoden för att köra flera jobb samtidigt i kluster som innehåller mer än en nod. Om du vill skapa ett HDInsight-kluster med en nod i Azure använder du *`Custom(size, settings, apps)`* alternativet och använder värdet 1 för *antal arbetsnoder* i avsnittet **kluster storlek** när du konfigurerar ett nytt kluster i portalen.

## <a name="quotas"></a>Kvoter

Mer information om hur du hanterar prenumerations kvoter finns i [begär kvot ökningar](quota-increase-request.md).

## <a name="next-steps"></a>Nästa steg

* [Konfigurera kluster i HDInsight med Apache Hadoop, Spark, Kafka och mycket mer](hdinsight-hadoop-provision-linux-clusters.md): Lär dig hur du konfigurerar och konfigurerar kluster i HDInsight.
* [Övervaka kluster prestanda](hdinsight-key-scenarios-to-monitor.md): Lär dig mer om viktiga scenarier för att övervaka ditt HDInsight-kluster som kan påverka klustrets kapacitet.
