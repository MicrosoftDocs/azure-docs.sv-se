---
title: Migrera Apache Kafka-arbetsbelastningar till Azure HDInsight 4,0
description: Lär dig hur du migrerar Apache Kafka-arbetsbelastningar i HDInsight 3,6 till HDInsight 4,0.
ms.service: hdinsight
ms.topic: conceptual
ms.date: 12/18/2019
ms.openlocfilehash: e15ebb13aee0e5dd814688ae77edaded667d54ac
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "104864133"
---
# <a name="migrate-apache-kafka-workloads-to-azure-hdinsight-40"></a>Migrera Apache Kafka-arbetsbelastningar till Azure HDInsight 4,0

Azure HDInsight 4,0 erbjuder de senaste komponenterna med öppen källkod med betydande förbättringar av prestanda, anslutning och säkerhet. Det här dokumentet beskriver hur du migrerar Apache Kafka-arbetsbelastningar i HDInsight 3,6 till HDInsight 4,0. När du har migrerat dina arbets belastningar till HDInsight 4,0 kan du använda många av de nya funktionerna som inte är tillgängliga i HDInsight 3,6.

## <a name="hdinsight-36-kafka-migration-paths"></a>HDInsight 3,6-sökvägar för Kafka-migrering

HDInsight 3,6 har stöd för två versioner av Kafka: 1.0.0 och 1.1.0. HDInsight 4,0 stöder versioner 1.1.0 och 2.1.0. Beroende på vilken version av Kafka och vilken version av HDInsight du vill köra, finns det flera sökvägar för migrering som stöds. Dessa sökvägar förklaras nedan och illustreras i följande diagram.

* **Kör både Kafka och HDInsight i de senaste versionerna (rekommenderas)**: Migrera ett HDInsight 3,6-och Kafka 1.0.0-eller 1.1.0-program till HDInsight 4,0 med Kafka 2.1.0 (sökvägar D och E nedan).
* **Kör HDInsight på den senaste versionen, men Kafka endast på en senare version**: Migrera ett HDInsight 3,6-och Kafka 1.0.0-program till HDInsight 4,0 med Kafka 1.1.0 (sökväg B nedan).
* **Kör HDInsight på den senaste versionen, Behåll Kafka-version**: Migrera ett HDInsight 3,6-och Kafka 1.1.0-program till HDInsight 4,0 med Kafka 1.1.0 (sökväg C nedan).
* **Kör Kafka på en nyare version, Behåll HDInsight-version**: Migrera ett Kafka 1.0.0-program till 1.1.0 och stanna kvar på HDInsight 3,6 (sökväg a nedan). Observera att det här alternativet fortfarande kräver att du distribuerar ett nytt kluster. Det finns inte stöd för att uppgradera Kafka-versionen på ett befintligt kluster. När du har skapat ett kluster med den version du vill använda migrerar du Kafka-klienterna så att de använder det nya klustret.

:::image type="content" source="./media/upgrade-threesix-to-four/apache-kafka-upgrade-path.png" alt-text="Uppgraderings vägar för Apache Kafka på 3,6" border="false":::

## <a name="apache-kafka-versions"></a>Apache Kafka versioner

### <a name="kafka-110"></a>Kafka 1.1.0
  
Om du migrerar från Kafka 1.0.0 till 1.1.0 kan du dra nytta av följande nya funktioner:

* Förbättringar av Kafka Controller påskyndar en kontrollerad avstängning, så du kan starta om utlösare och återställa från problem snabbare. 
* Förbättringar i [FetchRequests-logiken](https://issues.apache.org/jira/browse/KAFKA-6254) som gör att du kan ha fler partitioner (och därmed fler ämnen) i klustret. 
* Kafka Connect stöder [post rubriker](https://issues.apache.org/jira/browse/KAFKA-5142) och [reguljära uttryck](https://issues.apache.org/jira/browse/KAFKA-3073) för ämnen. 

En fullständig lista över uppdateringar finns i [Apache Kafka 1,1 viktig information](https://archive.apache.org/dist/kafka/1.1.0/RELEASE_NOTES.html).

### <a name="apache-kafka-210"></a>Apache Kafka 2.1.0

Om du migrerar till Kafka 2,1 kan du dra nytta av följande funktioner:

* Bättre service återhämtning på grund av ett förbättrat protokoll för replikering.
* Nya funktioner i KafkaAdminClient-API: et.
* Konfigurerbar kvot hantering.
* Stöd för Zstandard-komprimering.

En fullständig lista över uppdateringar finns i [Apache Kafka 2,0 viktig information](https://archive.apache.org/dist/kafka/2.0.0/RELEASE_NOTES.html) och [Apache Kafka 2,1 viktig information](https://archive.apache.org/dist/kafka/2.1.0/RELEASE_NOTES.html).

## <a name="kafka-client-compatibility"></a>Kompatibilitet för Kafka-klient

Nya Kafka-mäklare har stöd för äldre klienter. [Oppa över-35 – hämtning av protokoll version](https://cwiki.apache.org/confluence/display/KAFKA/KIP-35+-+Retrieving+protocol+version) introducerade en mekanism för dynamisk bestämning av funktionaliteten hos en Kafka [-Broker och oppa över-97: den förbättrade-klienten för klient-RPC](https://cwiki.apache.org/confluence/display/KAFKA/KIP-97%3A+Improved+Kafka+Client+RPC+Compatibility+Policy) introducerade en ny autentiseringsprincip och garanterar Java-klienten. Tidigare var Kafka-klienten tvungen att samverka med en Broker av samma version eller en nyare version. Nu kan nyare versioner av Java-klienter och andra klienter som har stöd för oppa över-35 som `librdkafka` kan återgå till äldre typer av förfrågningar eller utlösa lämpliga fel om funktionen inte är tillgänglig.

:::image type="content" source="./media/upgrade-threesix-to-four/apache-kafka-client-compatibility.png" alt-text="Uppgradera Kafka-klientens kompatibilitet" border="false":::

Observera att det inte betyder att klienten har stöd för äldre utjämnare.  Mer information finns i [Compatibility Matrix](https://cwiki.apache.org/confluence/display/KAFKA/Compatibility+Matrix).

## <a name="general-migration-process"></a>Allmän migreringsprocessen

Följande vägledning för migrering förutsätter att ett Apache Kafka 1.0.0-eller 1.1.0-kluster har distribuerats på HDInsight 3,6 i ett enda virtuellt nätverk. Den befintliga Service Broker har vissa ämnen och används aktivt av producenter och konsumenter.

:::image type="content" source="./media/upgrade-threesix-to-four/apache-kafka-presumed-environment.png" alt-text="Aktuell Kafka förmodad miljö" border="false":::

Gör så här för att slutföra migreringen:

1. **Distribuera ett nytt HDInsight 4,0-kluster och-klienter för testning.** Distribuera ett nytt HDInsight 4,0 Kafka-kluster. Om flera Kafka-kluster versioner kan väljas rekommenderar vi att du väljer den senaste versionen. Efter distributionen ställer du in några parametrar efter behov och skapar ett ämne med samma namn som din befintliga miljö. Ange också TLS-och BYOK-kryptering vid behov. Kontrol lera sedan att det fungerar korrekt med det nya klustret.

    :::image type="content" source="./media/upgrade-threesix-to-four/deploy-new-hdinsight-clusters.png" alt-text="Distribuera nya HDInsight 4,0-kluster" border="false":::

1. **Byt kluster för Producer och vänta tills alla köade data används av de aktuella förbrukarna.** När det nya HDInsight 4,0 Kafka-klustret är klart byter du det befintliga producent målet till det nya klustret. Låt det vara kvar tills den befintliga konsument appen har förbrukat alla data från det befintliga klustret.

    :::image type="content" source="./media/upgrade-threesix-to-four/switch-cluster-producer-app.png" alt-text="Växla kluster för Producer-app" border="false":::

1. **Växla klustret i klient programmet.** När du har bekräftat att det befintliga klient programmet har förbrukat alla data från det befintliga klustret byter du anslutning till det nya klustret.

    :::image type="content" source="./media/upgrade-threesix-to-four/switch-cluster-consumer-app.png" alt-text="Växla kluster i Consumer-appen" border="false":::

1. **Ta bort det gamla klustret och testa program efter behov.** När växeln har slutförts och fungerar korrekt, tar du bort det gamla HDInsight 3,6 Kafka-klustret och de producenter och konsumenter som används i testet efter behov.

## <a name="next-steps"></a>Nästa steg

* [Optimera prestanda för Apache Kafka HDInsight-kluster](apache-kafka-performance-tuning.md)
* [Snabb start: skapa Apache Kafka kluster i Azure HDInsight med Azure Portal](apache-kafka-get-started.md)
