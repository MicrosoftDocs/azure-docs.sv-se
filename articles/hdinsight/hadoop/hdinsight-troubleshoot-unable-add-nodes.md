---
title: Det går inte att lägga till noder i Azure HDInsight-kluster
description: Felsök varför det inte går att lägga till noder i Apache Hadoop kluster i Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 07/31/2019
ms.openlocfilehash: b11d1edef2f3a6fa0fb39c76d1f25ec05ff15d07
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98944323"
---
# <a name="scenario-unable-to-add-nodes-to-azure-hdinsight-cluster"></a>Scenario: det går inte att lägga till noder i Azure HDInsight-kluster

Den här artikeln beskriver fel söknings steg och möjliga lösningar för problem med att interagera med Azure HDInsight-kluster.

## <a name="issue"></a>Problem

Det går inte att lägga till noder i Azure HDInsight-klustret.

## <a name="cause"></a>Orsak

Anledningen kan variera.

## <a name="resolution"></a>Lösning

Använd funktionen [kluster storlek](../hdinsight-scaling-best-practices.md) och beräkna antalet ytterligare kärnor som krävs för klustret. Detta baseras på det totala antalet kärnor i de nya arbetsnoderna. Prova sedan ett eller flera av följande steg:

* Kontrol lera om det finns några tillgängliga kärnor på klustrets plats.

* Ta en titt på hur många tillgängliga kärnor som finns på andra platser. Överväg att återskapa ditt kluster på en annan plats med tillräckligt många tillgängliga kärnor.

* Om du vill öka kärnkvoten för en specifik plats skapar du ett supportärende för en HDInsight-kärnkvotökning.

## <a name="next-steps"></a>Nästa steg

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]