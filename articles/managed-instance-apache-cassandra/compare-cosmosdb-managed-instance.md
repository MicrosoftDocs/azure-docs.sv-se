---
title: Skillnader mellan Azure Managed instance för Apache Cassandra och Azure Cosmos DB API för Cassandra
description: Lär dig mer om skillnaderna mellan Azure Managed instance för Apache Cassandra och API för Cassandra i Azure Cosmos DB. Du lär dig också fördelarna med var och en av dessa tjänster och när du ska välja dem.
author: TheovanKraay
ms.author: thvankra
ms.service: managed-instance-apache-cassandra
ms.topic: quickstart
ms.date: 03/02/2021
ms.openlocfilehash: 3050dda4b3c0e1a05d751a4f5969bba69ad0506d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "101749016"
---
# <a name="differences-between-azure-managed-instance-for-apache-cassandra-preview-and-azure-cosmos-db-cassandra-api"></a>Skillnader mellan Azure Managed instance för Apache Cassandra (för hands version) och Azure Cosmos DB API för Cassandra 

I den här artikeln får du lära dig skillnaderna mellan Azure Managed instance för Apache Cassandra och API för Cassandra i Azure Cosmos DB. Den här artikeln innehåller rekommendationer för hur du väljer mellan de två tjänsterna eller när du ska vara värd för din egen Apache Cassandra-miljö.

> [!IMPORTANT]
> Azure Managed instance för Apache Cassandra är för närvarande en offentlig för hands version.
> Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade.
> Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="key-differences"></a>Viktiga skillnader

Den Azure-hanterade instansen för Apache Cassandra tillhandahåller automatiserad distribution, skalning och åtgärder för att underhålla nodens hälsa för Apache Cassandra-instanser med öppen källkod i Azure. Det ger också möjlighet att skala ut kapaciteten hos befintliga lokala eller molnbaserade Apache Cassandra-kluster. Den skalas ut genom att lägga till hanterade Cassandra-datacenter i den befintliga kluster ringen.

[API för Cassandra](../cosmos-db/cassandra-introduction.md) i Azure Cosmos DB är ett kompatibilitetsläge över Microsofts globalt distribuerade moln-och databas tjänst [Azure Cosmos DB](../cosmos-db/index.yml). Kombinationen av dessa tjänster i Azure innehåller flera alternativ för användare av Apache Cassandra i komplexa hybrid moln miljöer.

## <a name="how-to-choose"></a>Hur ska jag välja?

I följande tabell visas vanliga scenarier, arbets belastnings krav och aspirations där var och en av de här distributions metoderna passar:

| |Egen värd för Apache Cassandra lokalt eller i Azure | Azure-hanterad instans för Apache Cassandra | Azure Cosmos DB Cassandra API |
|---------|---------|---------|---------|
|**Distributions typ**| Du har en mycket anpassad Apache Cassandra-distribution med anpassade korrigeringsfiler eller snitches. | Du har en vanlig Apache Cassandra-distribution med öppen källkod utan någon anpassad kod. | Du är innehåll med en plattform som inte är Apache Cassandra under men som är kompatibel med alla klient driv rutiner med öppen källkod på en [överförings protokoll](../cosmos-db/cassandra-support.md) nivå. |
| **Drifts kostnader**| Du har befintliga Cassandra-experter som kan distribuera, konfigurera och underhålla dina kluster.  | Du vill sänka drifts kostnaderna för din Apache Cassandra-nods hälsa, men fortfarande behålla kontrollen över plattforms nivå konfigurationerna, till exempel replikering och konsekvens. | Du vill eliminera drifts kostnaderna genom att använda en fullständigt hanterad plattform som tjänst-databas i molnet. |
| **Operativ system krav**| Du måste ha ett krav för att underhålla anpassade eller gyllene operativ Systems avbildningar för virtuella datorer. | Du kan använda vanilj-avbildningar men vill ha kontroll över SKU: er, minne, diskar och IOPS. | Du vill att kapacitets etableringen ska förenklas och uttryckas som ett enkelt normaliserat mått, med en en-till-en-relation till data flödet, till exempel [enheter för programbegäran](../cosmos-db/request-units.md) i Azure Cosmos dB. |
| **Prismodell**| Du vill använda hanterings program som DataStax-verktyg och är nöjda med licens kostnaderna. | Du föredrar ren licens med öppen källkod och VM-baserad prissättning. | Du vill använda Cloud-inbyggd prissättning, inklusive [autoskalning](../cosmos-db/manage-scale-cassandra.md#use-autoscale) och [Server](../cosmos-db/serverless.md) lös erbjudanden. |
| **Analys**| Du vill ha fullständig kontroll över etableringen av analytiska pipeliner oavsett hur du kan bygga och underhålla dem. | Du vill använda molnbaserad analys tjänster som Azure Databricks. | Du vill att hybrid transaktions analys i real tid ska vara inbyggd i plattformen med [Azure Synapse-länken för Cosmos DB](../cosmos-db/synapse-link.md). |
| **Arbets belastnings mönster**| Arbets belastningen är ganska stabil och du behöver inte skala noder i klustret ofta. | Arbets belastningen är temporär och du måste kunna skala upp eller ned noder i ett Data Center eller lägga till/ta bort data Center enkelt. | Din arbets belastning är ofta flyktig och du måste kunna skala upp eller skala ned snabbt och på en betydande volym. |
| **Serviceavtal**| Du är nöjd med dina processer för att underhålla service avtal vid konsekvens, data flöde, tillgänglighet och haveri beredskap. | Du är nöjd med dina processer för att underhålla service avtal vid konsekvens, data flöde och tillgänglighet, men behöver hjälp med säkerhets kopieringar. | Du vill ha fullständigt omfattande service avtal om konsekvens, data flöde, tillgänglighet och haveri beredskap. |

## <a name="next-steps"></a>Nästa steg

Kom igång med en av våra snabb starter:

* [Skapa ett hanterat instans kluster från Azure Portal](create-cluster-portal.md)