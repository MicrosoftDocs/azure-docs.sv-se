---
title: Optimera Apache Spark kluster konfiguration – Azure HDInsight
description: Lär dig hur du konfigurerar ditt Apache Spark-kluster för att maximera data flödet på Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 08/21/2020
ms.custom: contperf-fy21q1
ms.openlocfilehash: 80abc052bc8fa731a1cea0d3ecb523aedb46cf05
ms.sourcegitcommit: 3ea45bbda81be0a869274353e7f6a99e4b83afe2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/10/2020
ms.locfileid: "97031789"
---
# <a name="cluster-configuration-optimization-for-apache-spark"></a>Kluster konfigurations optimering för Apache Spark

Den här artikeln beskriver hur du optimerar konfigurationen av Apache Spark-klustret för bästa prestanda i Azure HDInsight.

## <a name="overview"></a>Översikt

Beroende på arbets belastningen i Spark-klustret kan du bestämma att en icke-standard-Spark-konfiguration skulle resultera i mer optimerat Spark-jobb.  Utför benchmark-testning med exempel arbets belastningar för att verifiera konfigurationer som inte är standard för klusterkonfigurationer.

Här följer några vanliga parametrar som du kan justera:

|Parameter |Beskrivning |
|---|---|
|--antal-körningar|Anger lämpligt antal körningar.|
|--utförar-kärnor|Anger antalet kärnor för varje utförar. Normalt bör du ha mellanstora körningar, eftersom andra processer använder en del av det tillgängliga minnet.|
|--utförar-minne|Anger minnes storleken för varje utförar, som styr heap-storleken på garn. Lämna lite minne för att utföra omkostnader.|

## <a name="select-the-correct-executor-size"></a>Välj rätt utförar-storlek

När du bestämmer din utförar-konfiguration bör du tänka på hur du ska använda ^ skräp insamlingen (GC).

* Faktorer för att minska utförar storlek:
    1. Minska heap-storleken under 32 GB för att behålla GC-omkostnader < 10%.
    2. Minska antalet kärnor för att behålla GC-omkostnader < 10%.

* Faktorer för att öka utförar storlek:
    1. Minska kommunikations kostnader mellan körningar.
    2. Minska antalet öppna anslutningar mellan körningar (N2) i större kluster (>100-körningar).
    3. Öka heap-storleken så att den passar för minnes intensiva uppgifter.
    4. Valfritt: minska minnes omkostnader per utförar.
    5. Valfritt: öka användning och samtidighet efter oversubscribing CPU.

Som en allmän regel när du väljer utförar storlek:

1. Börja med 30 GB per utförar och distribuera tillgängliga maskin kärnor.
2. Öka antalet utförar-kärnor för större kluster (> 100-körningar).
3. Ändra storlek baserat på både vid utvärderings körning och på föregående faktorer, till exempel GC-overhead.

Tänk på följande när du kör samtidiga frågor:

1. Börja med 30 GB per utförar och alla dator kärnor.
2. Skapa flera parallella Spark-program med oversubscribing CPU (cirka 30% fördröjnings förbättring).
3. Distribuera frågor över parallella program.
4. Ändra storlek baserat på både vid utvärderings körning och på föregående faktorer, till exempel GC-overhead.

Mer information om hur du använder Ambari för att konfigurera körningar finns i [Apache Spark inställningar-Spark-körningar](apache-spark-settings.md#configuring-spark-executors).

Övervaka frågornas prestanda för avvikande eller andra prestanda problem genom att titta på vyn tids linje. Även SQL Graph, jobb statistik och så vidare. Information om hur du felsöker Spark-jobb med hjälp av garn och Spark historik Server finns i avsnittet om [fel sökning Apache Spark jobb som körs på Azure HDInsight](apache-spark-job-debugging.md). Tips om hur du använder garn tids linje server finns i [Access Apache HADOOP garn program loggar](../hdinsight-hadoop-access-yarn-app-logs-linux.md).

Ibland är ett eller flera av körningarna långsammare än de andra, och uppgifter tar mycket längre tid att köra. Den här långsamma frekvensen inträffar ofta i större kluster (> 30 noder). I det här fallet delar du in arbetet i ett större antal aktiviteter så att Scheduler kan kompensera för långsamma aktiviteter. Du kan till exempel ha minst två gånger så många uppgifter som antalet utförar-kärnor i programmet. Du kan också aktivera spekulativ körning av uppgifter med `conf: spark.speculation = true` .

## <a name="next-steps"></a>Nästa steg

* [Optimera data bearbetning för Apache Spark](optimize-cluster-configuration.md)
* [Optimera data lagring för Apache Spark](optimize-data-storage.md)
* [Optimera minnes användning för Apache Spark](optimize-memory-usage.md)
