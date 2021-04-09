---
title: Vad är Apache Spark
description: Den här artikeln innehåller en introduktion till Apache Spark i Azure Synapse Analytics och de olika scenarier där du kan använda Spark.
services: synapse-analytics
author: euangMS
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: spark
ms.date: 04/15/2020
ms.author: euang
ms.reviewer: euang
ms.openlocfilehash: b31fe5daaa0882dc0927c1340902b20df56eb6b6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96450437"
---
# <a name="apache-spark-in-azure-synapse-analytics"></a>Apache Spark i Azure Synapse Analytics

Apache Spark är ett ramverk för parallellbearbetning som stöder minnesintern bearbetning för att öka prestanda i program för stordataanalys. Apache Spark i Azure Synapse Analytics är en av Microsofts implementeringar av Apache Spark i molnet. Med Azure Synapse kan du enkelt skapa och konfigurera en server lös Apache Spark pool i Azure. Spark-pooler i Azure Synapse är kompatibla med Azure Storage och Azure Data Lake generation 2-lagring. Så du kan använda Spark-pooler för att bearbeta dina data som lagras i Azure.

![Spark: ett enhetligt ramverk](./media/apache-spark-overview/spark-overview.png)

## <a name="what-is-apache-spark"></a>Vad är Apache Spark

Apache Spark innehåller primitiver för minnes intern kluster användning. Ett Spark-jobb kan läsa in och cachelagra data i minnet och köra frågor mot det upprepade gånger. Minnesintern databearbetning är mycket snabbare än diskbaserade program. Spark integreras också med flera programmeringsspråk så att du kan manipulera distribuerade data uppsättningar som lokala samlingar. Det finns inget behov av att strukturera det hela i mappnings- och reduceringsåtgärder.

![Traditionell MapReduce vs. Spark](./media/apache-spark-overview/map-reduce-vs-spark.png)

Spark-pooler i Azure Synapse erbjuder en fullständigt hanterad Spark-tjänst. Fördelarna med att skapa en spark-pool i Azure Synapse Analytics visas här.

| Funktion | Beskrivning |
| --- | --- |
| Hastighet och effektivitet |Spark-instanser startar på ungefär 2 minuter för färre än 60 noder och ungefär 5 minuter för fler än 60 noder. Instansen stängs av som standard, 5 minuter efter det senaste jobbet som utförts om det inte hålls alivet av en Notebook-anslutning. |
| Enkelt att skapa |Du kan skapa en ny Spark-pool i Azure Synapse på bara några minuter med hjälp av Azure Portal, Azure PowerShell eller Synapse Analytics .NET SDK. Se [Kom igång med Spark-pooler i Azure Synapse Analytics](../quickstart-create-apache-spark-pool-studio.md). |
| Enkel att använda |Synapse Analytics innehåller en anpassad Notebook som härletts från [nteract](https://nteract.io/). Du kan de här anteckningsböckerna för interaktiv databehandling och visualisering.|
| REST API:er |Spark i Azure Synapse Analytics innehåller [Apache livy](https://github.com/cloudera/hue/tree/master/apps/spark/java#welcome-to-livy-the-rest-spark-server), en REST API-baserad Spark-jobbiljett för att skicka och övervaka jobb på distans. |
| Stöd för Azure Data Lake Storage generation 2| Spark-pooler i Azure Synapse kan använda Azure Data Lake Storage generation 2 och BLOB Storage. Mer information om Data Lake Storage finns i [Översikt över Azure Data Lake Storage](../../data-lake-store/data-lake-store-overview.md). |
| Integrering med tredje parts IDEs | Azure Synapse tillhandahåller ett IDE-plugin-program för [JetBrains "IntelliJ-idé](https://www.jetbrains.com/idea/) som är användbar för att skapa och skicka program till en spark-pool. |
| Förinstallerade Anaconda-bibliotek |Spark-pooler i Azure Synapse levereras med Anaconda-bibliotek förinstallerade. [Anaconda](https://docs.continuum.io/anaconda/) innehåller närmare 200 bibliotek för Machine Learning, dataanalys, visualisering med mera. |
| Skalbarhet | Apache Spark i Azure Synapse-pooler kan aktivera automatisk skalning, så att pooler skalas genom att lägga till eller ta bort noder efter behov. Dessutom kan Spark-pooler avslutas utan dataförlust eftersom alla data lagras i Azure Storage eller Data Lake Storage. |

Spark-pooler i Azure Synapse innehåller följande komponenter som är tillgängliga på pooler som standard.

- [Spark Core](https://spark.apache.org/docs/2.4.5/). Omfattar Spark Core, Spark SQL, GraphX och MLlib.
- [Anaconda](https://docs.continuum.io/anaconda/)
- [Apache Livy](https://github.com/cloudera/hue/tree/master/apps/spark/java#welcome-to-livy-the-rest-spark-server)
- [Nteract Notebook](https://nteract.io/)

## <a name="spark-pool-architecture"></a>Arkitektur för Spark-pool

Det är enkelt att förstå komponenterna i Spark genom att förstå hur Spark körs på Azure Synapse Analytics.

Spark-program körs som oberoende uppsättningar av processer i en pool, koordineras av SparkContext-objektet i huvud programmet (kallas driv rutins program).

SparkContext kan ansluta till kluster hanteraren, som allokerar resurser mellan program. Kluster hanteraren är [Apache HADOOP garn](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html). När en anslutning har upprättats skaffar Spark körningar på noder i poolen, som är processer som kör beräkningar och lagrar data för ditt program. Därefter skickar den din programkod (definieras av JAR- eller Python-filer som överförts till SparkContext) till exekverarna. Slutligen skickar SparkContext uppgifter till de exekverare som ska köras.

SparkContext kör användarens huvud funktion och kör de olika parallella åtgärderna på noderna. Drivrutinen samlar sedan in åtgärdernas resultat. Noderna läser och skriver data från och till fil systemet. Noderna cachelagrar också transformerade data i minnet som elastiska distribuerade data uppsättningar (RDD).

SparkContext ansluter till Spark-poolen och ansvarar för att konvertera ett program till ett dirigerat acykliska diagram (DAG). Grafen består av enskilda uppgifter som körs i en utförar-process på noderna. Varje applikation får en egen körningsprocess som förblir aktiv under applikationens livslängd och kör uppgifter i flera trådar.

## <a name="apache-spark-in-azure-synapse-analytics-use-cases"></a>Apache Spark i användnings fall i Azure Synapse Analytics

Spark-pooler i Azure Synapse Analytics aktiverar följande viktiga scenarier:

### <a name="data-engineeringdata-preparation"></a>Data teknik/data förberedelse

Apache Spark innehåller många språk funktioner som stöder förberedelse och bearbetning av stora data volymer så att det kan göras mer värdefullt och sedan konsumeras av andra tjänster i Azure Synapse Analytics. Detta är aktiverat via flera språk (C#, Scala, PySpark, Spark SQL) och tillhandahållna bibliotek för bearbetning och anslutning.

### <a name="machine-learning"></a>Machine Learning

Apache Spark levereras med [MLlib](https://spark.apache.org/mllib/), ett maskin inlärnings bibliotek som byggts ovanpå Spark och som du kan använda från en spark-pool i Azure Synapse Analytics. Spark-pooler i Azure Synapse Analytics innehåller även Anaconda, en python-distribution med en rad olika paket för data vetenskap, inklusive Machine Learning. När detta kombineras med inbyggt stöd för notebook-filer har du en miljö där du kan skapa maskininlärningsprogram.

## <a name="where-do-i-start"></a>Var börjar jag

Använd följande artiklar för att lära dig mer om Apache Spark i Azure Synapse Analytics:

- [Snabb start: skapa en spark-pool i Azure Synapse](../quickstart-create-apache-spark-pool-portal.md)
- [Snabb start: skapa en Apache Spark Notebook](../quickstart-apache-spark-notebook.md)
- [Självstudie: Machine Learning med hjälp av Apache Spark](./apache-spark-machine-learning-mllib-notebook.md)
- [Apache Spark officiell dokumentation](https://spark.apache.org/docs/2.4.5/)

> [!NOTE]
> En del av den officiella Apache Spark dokumentationen är beroende av att använda Spark-konsolen, detta är inte tillgängligt på Azure Synapse Spark, Använd Notebook-eller IntelliJ-upplevelsen istället

## <a name="next-steps"></a>Nästa steg

I den här översikten får du en grundläggande förståelse för Apache Spark i Azure Synapse Analytics. Fortsätt till nästa artikel och lär dig hur du skapar en spark-pool i Azure Synapse Analytics:

- [Skapa en spark-pool i Azure Synapse](../quickstart-create-apache-spark-pool-portal.md)
