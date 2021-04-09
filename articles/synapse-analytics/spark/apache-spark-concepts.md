---
title: Apache Spark Core-begrepp
description: Introduktion till grundläggande begrepp för Apache Spark i Azure Synapse Analytics.
services: synapse-analytics
author: euangMS
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: spark
ms.date: 04/15/2020
ms.author: euang
ms.reviewer: euang
ms.openlocfilehash: 51b2e8cd968c4c14777d196d90686b13158aef42
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "98120316"
---
# <a name="apache-spark-in-azure-synapse-analytics-core-concepts"></a>Apache Spark i Azure Synapse Analytics core-koncept

Apache Spark är ett ramverk för parallellbearbetning som stöder minnesintern bearbetning för att öka prestanda i program för stordataanalys. Apache Spark i Azure Synapse Analytics är en av Microsofts implementeringar av Apache Spark i molnet. 

Med Azure Synapse kan du enkelt skapa och konfigurera Spark-funktioner i Azure. Azure Synapse tillhandahåller en annan implementering av dessa Spark-funktioner som dokumenteras här.

## <a name="spark-pools"></a>Spark-pooler

En server lös Apache Spark-pool skapas i Azure Portal. Det är definitionen av en spark-pool som, när den instansieras, används för att skapa en spark-instans som bearbetar data. När en spark-pool skapas, finns den bara som metadata och inga resurser förbrukas, körs eller debiteras för. En spark-pool har en serie egenskaper som styr egenskaperna för en spark-instans. Dessa egenskaper omfattar men är inte begränsade till namn, storlek, skalnings beteende, Time to Live.

Eftersom det inte finns några dollar-eller resurs kostnader kopplade till att skapa Spark-pooler, kan alla siffror skapas med valfritt antal olika konfigurationer. Behörigheter kan också tillämpas på Spark-pooler som gör det möjligt för användare endast att ha åtkomst till vissa och inte andra.

Ett bra tips är att skapa mindre Spark-pooler som kan användas för utveckling och fel sökning och sedan större för att köra produktions arbets belastningar.

Du kan läsa hur du skapar en spark-pool och ser alla deras egenskaper här [komma igång med Spark-pooler i Azure Synapse Analytics](../quickstart-create-apache-spark-pool-portal.md)

## <a name="spark-instances"></a>Spark-instanser

Spark-instanser skapas när du ansluter till en spark-pool, skapar en session och kör ett jobb. Eftersom flera användare kan ha åtkomst till en enda Spark-pool skapas en ny Spark-instans för varje användare som ansluter. 

När du skickar ett andra jobb, om det finns kapacitet i poolen, har den befintliga Spark-instansen också kapacitet. Sedan bearbetar den befintliga instansen jobbet. Annars, om kapaciteten är tillgänglig på Poolnivå, skapas en ny Spark-instans.

## <a name="examples"></a>Exempel

### <a name="example-1"></a>Exempel 1

- Du skapar en spark-pool med namnet SP1; den har en fast kluster storlek på 20 noder.
- Du skickar ett anteckningsbok-jobb, J1 som använder 10 noder, en spark-instans, SI1 skapas för att bearbeta jobbet.
- Nu skickar du ett annat jobb, J2, som använder 10 noder eftersom det fortfarande finns kapacitet i poolen och instansen, J2, bearbetas av SI1.
- Om J2 hade bett om 11 noder har det inte funnits någon kapacitet i SP1 eller SI1. I det här fallet kommer jobbet att avvisas om J2 kommer från en antecknings bok. om J2 kommer från ett batch-jobb placeras det i kö.

### <a name="example-2"></a>Exempel 2

- Du skapar en spark-pool-anrop SP2; den har aktiverade 10 – 20 noder med autoskalning
- Du skickar ett anteckningsbok-jobb, J1 som använder 10 noder, en spark-instans, SI1, skapas för att bearbeta jobbet.
- Nu kan du skicka ett annat jobb, J2, som använder 10 noder, eftersom det fortfarande finns kapacitet i poolen att instansen automatiskt växer till 20 noder och bearbetar J2.

### <a name="example-3"></a>Exempel 3

- Du skapar en spark-pool med namnet SP1; den har en fast kluster storlek på 20 noder.
- Du skickar ett anteckningsbok-jobb, J1 som använder 10 noder, en spark-instans, SI1 skapas för att bearbeta jobbet.
- En annan användare, U2, skickar ett jobb, J3, som använder 10 noder, en ny Spark-instans, SI2, skapas för att bearbeta jobbet.
- Nu skickar du ett annat jobb, J2, som använder 10 noder eftersom det fortfarande finns kapacitet i poolen och instansen J2 bearbetas av SI1.

## <a name="quotas-and-resource-constraints-in-apache-spark-for-azure-synapse"></a>Kvoter och resurs begränsningar i Apache Spark för Azure-Synapse

### <a name="workspace-level"></a>Arbetsyte nivå

Varje Azure Synapse-arbetsyta levereras med en standard kvot på virtuella kärnor som kan användas för Spark. Kvoten delas mellan användar kvoten och kvoten för data flödet så att inget användnings mönster använder upp alla virtuella kärnor på arbets ytan. Kvoten är olika beroende på typen av prenumeration men är symmetrisk mellan användare och data flöde. Om du däremot begär fler virtuella kärnor än vad som återstår i arbets ytan, får du följande fel meddelande:

```console
Failed to start session: [User] MAXIMUM_WORKSPACE_CAPACITY_EXCEEDED
Your Spark job requested 480 vcores.
However, the workspace only has xxx vcores available out of quota of yyy vcores.
Try reducing the numbers of vcores requested or increasing your vcore quota. Click here for more information - https://go.microsoft.com/fwlink/?linkid=213499
```

Länken i meddelandet pekar på den här artikeln.

I följande artikel beskrivs hur du begär en ökning i vCore-kvoten för arbets ytan.

- Välj "Azure Synapse Analytics" som tjänst typ.
- I fönstret kvot information väljer du Apache Spark (vCore) per arbets yta

[Begär en kapacitets ökning via Azure Portal](../../azure-portal/supportability/per-vm-quota-requests.md#request-a-standard-quota-increase-from-help--support)

### <a name="spark-pool-level"></a>Nivå för Spark-pool

När du definierar en spark-pool definierar du effektivt en kvot per användare för poolen, om du kör flera antecknings böcker eller jobb eller en blandning av 2 är det möjligt att belasta poolens kvot. Om du gör det kommer ett fel meddelande som följande genereras

```console
Failed to start session: Your Spark job requested xx vcores.
However, the pool is consuming yy vcores out of available zz vcores.Try ending the running job(s) in the pool, reducing the numbers of vcores requested, increasing the pool maximum size or using another pool
```

För att lösa det här problemet måste du minska användningen av poolens resurser innan du skickar en ny resurs förfrågan genom att köra en bärbar dator eller ett jobb.

## <a name="next-steps"></a>Nästa steg

- [Azure Synapse Analytics](../index.yml)
- [Apache Spark dokumentation](https://spark.apache.org/docs/2.4.5/)