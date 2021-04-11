---
title: Begrepp för Apache Spark pool
description: Introduktion till Apache Spark Pools storlekar och konfigurationer i Azure Synapse Analytics.
services: synapse-analytics
author: mlee3gsd
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: spark
ms.date: 12/2/2020
ms.author: martinle
ms.reviewer: euang
ms.openlocfilehash: 6422c33f17879aa8ec4844cc6de63411528a388b
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "104606165"
---
# <a name="apache-spark-pool-configurations-in-azure-synapse-analytics"></a>Konfigurationer för Apache Spark pool i Azure Synapse Analytics

En spark-pool är en uppsättning metadata som definierar beräknings resurs kraven och associerade beteende egenskaper när en spark-instans instansieras. Dessa egenskaper inkluderar, men är inte begränsade till namn, antal noder, Node-storlek, skalnings beteende och tid till Live. En spark-pool i sig använder inte några resurser. Det kostar inget att skapa Spark-pooler. Avgifter uppkommer bara när ett Spark-jobb körs på mål Spark-poolen och Spark-instansen instansieras på begäran.

Du kan läsa hur du skapar en spark-pool och ser alla deras egenskaper här [komma igång med Spark-pooler i Synapse Analytics](../quickstart-create-apache-spark-pool-portal.md)

## <a name="nodes"></a>Noder

Apache Spark pool-instansen består av en head-nod och två eller flera arbetsnoder med minst tre noder i en spark-instans.  Head-noden kör ytterligare hanterings tjänster som livy, garn Resource Manager, Zookeeper och Spark-drivrutinen.  Alla noder kör tjänster som Node agent och garn Node Manager. Alla Worker-noder kör Spark utförar-tjänsten.

## <a name="node-sizes"></a>Node-storlekar

En spark-pool kan definieras med Node-storlekar som sträcker sig från en liten Compute-nod med 8 vCore och 64 GB minne upp till en XXLarge-datornod med 64 vCore och 432 GB minne per nod. Node-storlekar kan ändras efter att poolen har skapats, trots att instansen kanske måste startas om.

|Storlek | V-kärna | Minne|
|-----|------|-------|
|Liten|4|32 GB|
|Medel|8|64 GB|
|Stor|16|128 GB|
|XLarge|32|256 GB|
|XXLarge|64|432 GB|

## <a name="autoscale"></a>Automatisk skalning

Apache Spark pooler ger möjlighet att automatiskt skala upp och ned beräknings resurser baserat på mängden aktivitet.  När funktionen för autoskalning är aktive rad kan du ange det lägsta och högsta antalet noder som ska skalas.
När funktionen för autoskalning är inaktive rad förblir antalet noder fasta.  Den här inställningen kan ändras efter att poolen har skapats, trots att instansen kanske måste startas om.

## <a name="automatic-pause"></a>Automatisk paus

Funktionen för automatisk paus frigör resurser efter en inaktiv period som minskar den totala kostnaden för en Apache Spark pool.  Antalet minuters inaktiv tid kan anges när den här funktionen är aktive rad.  Funktionen för automatisk paus är oberoende av funktionen för automatisk skalning. Resurser kan pausas om autoskalning är aktiverat eller inaktiverat.  Den här inställningen kan ändras efter att poolen har skapats, trots att instansen kanske måste startas om.

## <a name="next-steps"></a>Nästa steg

* [Azure Synapse Analytics](../index.yml)
* [Apache Spark dokumentation](https://spark.apache.org/docs/2.4.5/)