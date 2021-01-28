---
title: Apache Tez-program låser sig i Azure HDInsight
description: Apache Tez-program låser sig i Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 08/09/2019
ms.openlocfilehash: 56c68c26ae953034283031e2427b7a4afadee94e
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/28/2021
ms.locfileid: "98935931"
---
# <a name="scenario-apache-tez-application-hangs-in-azure-hdinsight"></a>Scenario: Apache Tez Application låser sig i Azure HDInsight

Den här artikeln beskriver fel söknings steg och möjliga lösningar för problem med att interagera med Azure HDInsight-kluster.

## <a name="issue"></a>Problem

När du har skickat Apache Hive jobb, från Tez, är jobbets status "körs", men visas inte för att göra några framsteg

## <a name="cause"></a>Orsak

För många jobb har skickats; lång garn kö.

## <a name="resolution"></a>Lösning

Skala upp klustret eller vänta till garn kön töms.

Som standard `yarn.scheduler.capacity.maximum-applications` styrs det maximala antalet program som körs eller väntar, och den används som standard `10000` .

## <a name="next-steps"></a>Nästa steg

Om du inte ser problemet eller inte kan lösa problemet kan du gå till någon av följande kanaler för mer support:

* Få svar från Azure-experter via [Azure community support](https://azure.microsoft.com/support/community/).

* Anslut till [@AzureSupport](https://twitter.com/azuresupport) – det officiella Microsoft Azure kontot för att förbättra kund upplevelsen. Att ansluta Azure-communityn till rätt resurser: svar, support och experter.

* Om du behöver mer hjälp kan du skicka en support förfrågan från [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Välj **stöd** på Meny raden eller öppna **Hjälp + Support** Hub. Mer detaljerad information finns [i så här skapar du en support förfrågan för Azure](../../azure-portal/supportability/how-to-create-azure-support-request.md). Åtkomst till prenumerations hantering och fakturerings support ingår i din Microsoft Azure prenumeration och teknisk support tillhandahålls via ett av support avtalen för [Azure](https://azure.microsoft.com/support/plans/).