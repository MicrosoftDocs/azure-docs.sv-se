---
title: Apache Spark långsam när Azure HDInsight-lagring har många filer
description: Apache Spark jobbet körs långsamt när Azure Storage-behållaren innehåller många filer i Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 08/21/2019
ms.openlocfilehash: c26baec66248ca00ef212acf3d773c2566b3aea9
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/28/2021
ms.locfileid: "98946362"
---
# <a name="apache-spark-job-run-slowly-when-the-azure-storage-container-contains-many-files-in-azure-hdinsight"></a>Apache Spark-jobbet går långsamt när Azure Storage-containern innehåller många filer i Azure HDInsight

I den här artikeln beskrivs fel söknings steg och möjliga lösningar på problem när du använder Apache Spark-komponenter i Azure HDInsight-kluster.

## <a name="issue"></a>Problem

När du kör ett HDInsight-kluster blir det Apache Spark jobbet som skriver till Azure Storage-behållaren långsamt när det finns många filer/undermappar. Det tar till exempel 20 sekunder att skriva till en ny behållare, men ungefär 2 minuter när du skriver till en behållare som har funktion-filer.

## <a name="cause"></a>Orsak

Detta är ett känt Spark-problem. Den långsammaheten kommer från `ListBlob` åtgärderna och `GetBlobProperties` när Spark-jobbet körs.

För att spåra partitioner måste Spark underhålla en `FileStatusCache` som innehåller information om katalog strukturen. Med den här cachen kan Spark parsa Sök vägarna och vara medvetna om tillgängliga partitioner. Fördelen med att spåra partitioner är att Spark bara vidrör de nödvändiga filerna när du läser data. För att den här informationen ska vara aktuell när du skriver nya data, måste Spark lista alla filer i katalogen och uppdatera denna cache.

I Spark 2,1, medan vi inte behöver uppdatera cachen efter varje skrivning, kommer Spark att kontrol lera om en befintlig partitionstabell matchar den föreslagna i den aktuella skrivbegäran, så att den även leder till att åtgärder visas i början av varje skrivning.

I Spark 2,2 bör det här prestanda problemet åtgärdas när du skriver data med append-läge.

## <a name="resolution"></a>Lösning

När du skapar en partitionerad data uppsättning är det viktigt att använda ett partitionerings schema som begränsar det antal filer som Spark har till listan för att uppdatera `FileStatusCache` .

För varje N:te Micro batch där N %100 = = 0 (100 är bara ett exempel), flytta befintliga data till en annan katalog som kan läsas in av Spark.

## <a name="next-steps"></a>Nästa steg

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]