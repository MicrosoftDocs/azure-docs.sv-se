---
title: Apache Ambari Tez View läses långsamt i Azure HDInsight
description: Apache Ambari Tez View kan läsas in långsamt eller kan inte läsas in alls i Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 07/30/2019
ms.openlocfilehash: 4fe66b3104be0351a9b0e1df6b6545f71ff276ab
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98930764"
---
# <a name="scenario-apache-ambari-tez-view-loads-slowly-in-azure-hdinsight"></a>Scenario: Apache Ambari Tez View läses långsamt i Azure HDInsight

I den här artikeln beskrivs fel söknings steg och möjliga lösningar på problem vid användning av interaktiva frågekomponenter i Azure HDInsight-kluster.

## <a name="issue"></a>Problem

Apache Ambari Tez-vyn kan läsas in långsamt eller kan inte läsas in alls. När du läser in Ambari Tez-vyn kan du se att processer på huvudnoderna slutar svara.

## <a name="cause"></a>Orsak

Åtkomst till garn ATS-API: er kan ibland ha dåliga prestanda på kluster som skapats före okt 2017 när klustret har ett stort antal Hive-jobb som körs på det.

## <a name="resolution"></a>Lösning

Detta är ett problem som har åtgärd ATS i okt 2017. Om du återskapar klustret kommer det inte att köras i det här problemet igen.

## <a name="next-steps"></a>Nästa steg

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]