---
title: Vad är Apache HBase i Azure HDInsight?
description: En introduktion till Apache HBase i HDInsight en NoSQL-databas som bygger på Hadoop. Läs mer om användningsfall och jämför HBase med andra Hadoop-kluster.
ms.service: hdinsight
ms.topic: overview
ms.custom: hdinsightactive,hdiseo17may2017,seoapr2020
ms.date: 04/20/2020
ms.openlocfilehash: 98872cc1315d946c63825318d2b98460031e128a
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/28/2021
ms.locfileid: "98942906"
---
# <a name="what-is-apache-hbase-in-azure-hdinsight"></a>Vad är Apache HBase i Azure HDInsight

[Apache HBase](https://hbase.apache.org/) är en NoSQL-databas med öppen källkod som bygger på Apache Hadoop och modelleras efter [Google BigTable](https://cloud.google.com/bigtable/). HBase ger slumpmässig åtkomst och stark konsekvens för stora mängder data i en schema lös databas. Databasen är ordnad efter kolumn familjer.

HBase liknar en databas från användar perspektiv. Data lagras i rader och kolumner i en tabell och data i en rad grupperas efter kolumn serie. HBase är en schema lös databas. Kolumner och data typer kan inte definieras innan de används. Den öppna källkoden skalas linjärt för att hantera petabyte med data på tusentals noder. Den kan förlita sig på dataredundans, batchbearbetning och andra funktioner som tillhandahålls av distribuerade program i Hadoop-miljön.

## <a name="how-is-apache-hbase-implemented-in-azure-hdinsight"></a>Hur implementeras Apache HBase i Azure HDInsight?

HDInsight HBase erbjuds som ett hanterat kluster som är integrerat i Azure-miljön. Klustren har kon figurer ATS för att lagra data direkt i [Azure Storage](./../hdinsight-hadoop-use-blob-storage.md), vilket ger låg latens och ökad elastiskhet i alternativ för prestanda och kostnad. Med den här egenskapen kan kunderna bygga interaktiva webbplatser som arbetar med stora data uppsättningar. För att skapa tjänster som lagrar sensor-och telemetridata från miljon tals slut punkter. Och analysera dessa data med Hadoop-jobb. HBase och Hadoop är effektiva start punkter för Big data Project i Azure. Tjänsterna kan möjliggöra real tids program att arbeta med stora data uppsättningar.

HDInsight-implementeringen använder den skalbara arkitekturen i HBase för att tillhandahålla automatiska horisontell partitionering för tabeller. Och stark konsekvens för läsningar och skrivningar och automatisk redundans. Prestanda utökas av cachelagring i minnet för läsning och snabb strömning för skrivning. HBase-kluster kan skapas i virtuella nätverk. Mer information finns i [Create HDInsight clusters on Azure Virtual Network](./apache-hbase-provision-vnet.md) (Skapa HDInsight-kluster i Azure Virtual Network).

## <a name="how-is-data-managed-in-hdinsight-hbase"></a>Hur hanteras data i HDInsight HBase?

Data kan hanteras i HBase med hjälp av kommandona `create`, `get`, `put` och `scan` i HBase-gränssnittet. Data skrivs till databasen med hjälp av `put` och läses med hjälp av `get`. Kommandot `scan` används till att hämta data från flera rader i en tabell. Data kan också hanteras med HBase C#-API. Det ger ett klientbibliotek utöver HBase REST-API. En HBase-databas kan också frågas genom att använda [Apache Hive](https://hive.apache.org/). En introduktion till dessa programmerings modeller finns i [komma igång med Apache HBase med Apache Hadoop i HDInsight](./apache-hbase-tutorial-get-started-linux.md). Samprocessorer är också tillgängliga, vilket gör det möjligt att bearbeta data i noderna som är värdar för databasen.

> [!NOTE]  
> Thrift stöds inte av HBase i HDInsight.

## <a name="use-cases-for-apache-hbase"></a>Användnings fall för Apache HBase

Det kanoniska användnings fallet för vilken BigTable (och efter tillägg, HBase) skapades från Webbs ökningen. Sökmotorer skapar index som mappar termer till de webbsidor som innehåller dem. Men HBase passar också för många andra användningsområden och du hittar många av dem i det här avsnittet.

|Scenario |Beskrivning |
|---|---|
|Nyckelvärdeslagring|HBase kan användas som nyckel värdes lager och är lämpligt för att hantera meddelande system. Facebook använder HBase för sina meddelande system och är perfekt för att lagra och hantera Internet-kommunikation. WebTable använder HBase till att söka efter och hantera tabeller som extraheras från webbsidor.|
|Sensordata|HBase är användbart för insamling av data som samlas in inkrementellt från olika källor. Dessa data omfattar sociala analyser och tids serier. Och hålla interaktiva instrument paneler uppdaterade med trender och räknare och hantering av Gransknings logg system. Exempel på detta är Bloomberg näringsidkare-Terminal och Open Time Series-databasen (OpenTSDB). OpenTSDB lagrar och ger åtkomst till mått som samlats in om hälsan för server system.|
|Realtidsfråga|[Apache Phoenix](https://phoenix.apache.org/) är en SQL-frågemotor för Apache HBase. Den används som en JDBC-drivrutin, och den gör det möjligt att fråga och hantera HBase-tabeller med hjälp av SQL.|
|HBase som en plattform|Program kan köras ovanpå HBase genom att använda det som ett datalager. Exempel är Phoenix, OpenTSDB, `Kiji` och Titan. Program kan också integreras med HBase. Exempel är: [Apache Hive](https://hive.apache.org/), Apache gris, [solr](https://lucene.apache.org/solr/), Apache Storm, Apache FLUME, [Apache Impala](https://impala.apache.org/), Apache Spark, `Ganglia` och Apache-granskning.|

## <a name="next-steps"></a>Nästa steg

* [Kom igång med Apache HBase med Apache Hadoop i HDInsight](./apache-hbase-tutorial-get-started-linux.md)
* [Create HDInsight clusters on Azure Virtual Network](./apache-hbase-provision-vnet.md) (Skapa HDInsight-kluster i Azure Virtual Network)
* [Konfigurera Apache HBase-replikering i HDInsight](apache-hbase-replication.md)
