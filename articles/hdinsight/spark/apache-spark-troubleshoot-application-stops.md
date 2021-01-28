---
title: Apache Spark strömmande program stoppas efter 24 dagar i Azure HDInsight
description: Ett Apache Spark streaming-program stoppas efter att det har körts i 24 dagar och det finns inga fel i loggfilerna.
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 07/29/2019
ms.openlocfilehash: b702cbf915e4991df4c202564677ea7e0a02f9c4
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/28/2021
ms.locfileid: "98929466"
---
# <a name="scenario-apache-spark-streaming-application-stops-after-executing-for-24-days-in-azure-hdinsight"></a>Scenario: Apache Spark strömnings program stoppas efter att ha körts i 24 dagar i Azure HDInsight

I den här artikeln beskrivs fel söknings steg och möjliga lösningar på problem när du använder Apache Spark-komponenter i Azure HDInsight-kluster.

## <a name="issue"></a>Problem

Ett Apache Spark streaming-program stoppas efter att det har körts i 24 dagar och det finns inga fel i loggfilerna.

## <a name="cause"></a>Orsak

`livy.server.session.timeout`Värdet styr hur lång tid Apache livy ska vänta tills en session har slutförts. När sessionens längd når `session.timeout` värdet avslutas livy-sessionen och programmet automatiskt.

## <a name="resolution"></a>Lösning

För tids krävande jobb ökar du värdet för `livy.server.session.timeout` att använda Ambari-användargränssnittet. Du kan komma åt livy-konfigurationen från Ambari-gränssnittet med hjälp av URL: en `https://<yourclustername>.azurehdinsight.net/#/main/services/LIVY/configs` .

Ersätt `<yourclustername>` med namnet på ditt HDInsight-kluster på det sätt som visas i portalen.

## <a name="next-steps"></a>Nästa steg

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]