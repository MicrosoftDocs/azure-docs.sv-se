---
title: Överskridna tidsgränser med kommandot ”hbase hbck” i Azure HDInsight
description: Timeout-fel med kommandot HBase hbck vid korrigering av region tilldelningar
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 08/16/2019
ms.openlocfilehash: acf4d1237841f8c20c014598e00a5e8961e2a012
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "98936696"
---
# <a name="scenario-timeouts-with-hbase-hbck-command-in-azure-hdinsight"></a>Scenario: timeout med kommandot HBase hbck i Azure HDInsight

Den här artikeln beskriver fel söknings steg och möjliga lösningar för problem med att interagera med Azure HDInsight-kluster.

## <a name="issue"></a>Problem

Utlöser timeout med `hbase hbck` kommando när du korrigerar områdes tilldelningar.

## <a name="cause"></a>Orsak

En möjlig orsak till timeout-problem när du använder `hbck` kommandot kan vara att flera regioner är i läget "i över gång" under en längre tid. Du kan se dessa regioner som offline i HBase Master gränssnittet. Eftersom ett stort antal regioner försöker över gången kan HBase Master tids gräns och det går inte att ansluta dessa regioner online igen.

## <a name="resolution"></a>Lösning

1. Logga in på HDInsight HBase-klustret med SSH.

1. Kör `hbase zkcli` kommando för att ansluta till Apache ZooKeeper Shell.

1. Kör `rmr /hbase/regions-in-transition` eller- `rmr /hbase-unsecure/regions-in-transition` kommando.

1. Stäng från `hbase zkcli` Shell med hjälp av `exit` kommandot.

1. Starta om tjänsten Active HBase Master från Apache Ambari-ANVÄNDARGRÄNSSNITTET.

1. Kör kommandot `hbase hbck -fixAssignments`.

1. Övervaka den HBase Master användar gränssnitts region "i över gång" som avsnittet för att se till att ingen region fastnar.

## <a name="next-steps"></a>Nästa steg

Om du inte ser problemet eller inte kan lösa problemet kan du gå till någon av följande kanaler för mer support:

- Få svar från Azure-experter via [Azure community support](https://azure.microsoft.com/support/community/).

- Anslut till [@AzureSupport](https://twitter.com/azuresupport) – det officiella Microsoft Azure kontot för att förbättra kund upplevelsen. Att ansluta Azure-communityn till rätt resurser: svar, support och experter.

- Om du behöver mer hjälp kan du skicka en support förfrågan från [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Välj **stöd** på Meny raden eller öppna **Hjälp + Support** Hub. Mer detaljerad information finns [i så här skapar du en support förfrågan för Azure](../../azure-portal/supportability/how-to-create-azure-support-request.md). Åtkomst till prenumerations hantering och fakturerings support ingår i din Microsoft Azure prenumeration och teknisk support tillhandahålls via ett av support avtalen för [Azure](https://azure.microsoft.com/support/plans/).