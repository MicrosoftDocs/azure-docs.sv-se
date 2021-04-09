---
title: Minskning är långsam i Azure HDInsight
description: Minskning är långsam i Azure HDInsight från möjlig data skevning
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 07/30/2019
ms.openlocfilehash: f9b6e0d862d17badb1caa672852214cdd86abb49
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "98930824"
---
# <a name="scenario-reducer-is-slow-in-azure-hdinsight"></a>Scenario: en minskning är långsam i Azure HDInsight

I den här artikeln beskrivs fel söknings steg och möjliga lösningar på problem vid användning av interaktiva frågekomponenter i Azure HDInsight-kluster.

## <a name="issue"></a>Problem

När du kör en fråga, till exempel `insert into table1 partition(a,b) select a,b,c from table2` en frågeplan, startar en mängd olika Minskare, men data från varje partition går till en enda minsknings punkt. Detta gör att frågan blir så låg som den tid det tar för den största partitionens minskning.

## <a name="cause"></a>Orsak

Öppna [Beeline](../hadoop/apache-hadoop-use-hive-beeline.md) och kontrol lera värdet för set `hive.optimize.sort.dynamic.partition` .

Värdet för den här variabeln är avsett att ställas in på True/false baserat på data typen.

Om partitionerna i inmatnings tabellen är mindre (t. ex. mindre än 10), och så är antalet utgående partitioner, och variabeln är inställd på `true` , så gör det att data globalt sorteras och skrivs med hjälp av en enda minskning per partition. Även om antalet tillgängliga minsknings bara enheter är större, kan det hända att ett fåtal dämpare slutar på grund av data skevningen och att maximal parallellitet inte kan uppnås. När det ändras till `false` kan mer än en minskning hantera en enda partition och flera mindre filer skrivs ut, vilket resulterar i snabbare infogning. Detta kan påverka ytterligare frågor på grund av förekomsten av mindre filer.

Värdet är `true` meningsfullt när antalet partitioner är större och data inte är sneda. I sådana fall skrivs resultatet av kart fasen ut så att varje partition hanteras av en enda minskning som resulterar i bättre efterföljande frågeresultat.

## <a name="resolution"></a>Lösning

1. Försök att partitionera om data för att normalisera till flera partitioner.

1. Om #1 inte är möjligt ställer du in värdet för config på false i Beeline-sessionen och försöker köra frågan igen. `set hive.optimize.sort.dynamic.partition=false`. Att ställa in värdet på false på en kluster nivå rekommenderas inte. Värdet för `true` är optimalt och anger parametern efter behov baserat på typen av data och frågor.

## <a name="next-steps"></a>Nästa steg

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]