---
title: Det gick inte att läsa Apache-Garnets logg i Azure HDInsight
description: Fel söknings steg och möjliga lösningar för problem med att interagera med Azure HDInsight-kluster.
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 01/23/2020
ms.openlocfilehash: 02a79de8aee169f5f702d5fae67194c62363e8c4
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98943042"
---
# <a name="scenario-unable-to-read-apache-yarn-log-in-azure-hdinsight"></a>Scenario: det går inte att läsa Apache-Garnets logg i Azure HDInsight

Den här artikeln beskriver fel söknings steg och möjliga lösningar för problem med att interagera med Azure HDInsight-kluster.

## <a name="issue"></a>Problem

Apache-garnen från lagrings kontot är inte läsligt. Fil parsern fungerar inte och genererar följande fel meddelande:

```
java.io.IOException: Not a valid BCFile.
```

## <a name="cause"></a>Orsak

Apache-Garnets logg sammanställs i `IndexFile` format, vilket inte stöds av fil parsern.

## <a name="resolution"></a>Lösning

1. I en webbläsare går du till `https://CLUSTERNAME.azurehdinsight.net` , där `CLUSTERNAME` är namnet på klustret.

1. Från Ambari UI navigerar du till **garn**  >  **configs**  >  **Advanced**  >  **Advanced garn-site**.

1. För WASB-lagring: standardvärdet för `yarn.log-aggregation.file-formats` är `IndexedFormat,TFile` . Ändra värdet till `TFile` .

1. För ADLS-lagring: standardvärdet för `yarn.nodemanager.log-aggregation.compression-type` är `gz` . Ändra värdet till `none` .

1. Spara ändringen och starta om alla berörda tjänster.

## <a name="next-steps"></a>Nästa steg

Om du inte ser problemet eller inte kan lösa problemet kan du gå till någon av följande kanaler för mer support:

* Få svar från Azure-experter via [Azure community support](https://azure.microsoft.com/support/community/).

* Anslut till [@AzureSupport](https://twitter.com/azuresupport) – det officiella Microsoft Azure kontot för att förbättra kund upplevelsen. Att ansluta Azure-communityn till rätt resurser: svar, support och experter.

* Om du behöver mer hjälp kan du skicka en support förfrågan från [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Välj **stöd** på Meny raden eller öppna **Hjälp + Support** Hub. Mer detaljerad information finns [i så här skapar du en support förfrågan för Azure](../../azure-portal/supportability/how-to-create-azure-support-request.md). Åtkomst till prenumerations hantering och fakturerings support ingår i din Microsoft Azure prenumeration och teknisk support tillhandahålls via ett av support avtalen för [Azure](https://azure.microsoft.com/support/plans/).