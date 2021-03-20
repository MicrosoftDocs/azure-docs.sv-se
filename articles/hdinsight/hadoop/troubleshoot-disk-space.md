---
title: Hantera disk utrymme i Azure HDInsight
description: Fel söknings steg och möjliga lösningar för att hantera problem med disk utrymme när du interagerar med Azure HDInsight-kluster.
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 02/17/2020
ms.openlocfilehash: 7164494cb08c4b419b9e4d96075ace3e52187497
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98944814"
---
# <a name="manage-disk-space-in-azure-hdinsight"></a>Hantera disk utrymme i Azure HDInsight

Den här artikeln beskriver fel söknings steg och möjliga lösningar för problem med att interagera med Azure HDInsight-kluster.

## <a name="hive-log-configurations"></a>Konfigurationer av Hive-logg

1. I en webbläsare går du till `https://CLUSTERNAME.azurehdinsight.net` , där `CLUSTERNAME` är namnet på klustret.

1. Navigera till **Hive**  >  -**konfigurationer**  >  **Advanced**  >  **Advanced Hive-log4j**. Granska följande inställningar:

    * `hive.root.logger=DEBUG,RFA`. Detta är standardvärdet, ändra [logg nivån](https://logging.apache.org/log4j/2.x/log4j-api/apidocs/org/apache/logging/log4j/Level.html) till `INFO` för att skriva ut mindre logg poster.

    * `log4jhive.log.maxfilesize=1024MB`. Detta är standardvärdet, ändra enligt önskemål.

    * `log4jhive.log.maxbackupindex=10`. Detta är standardvärdet, ändra enligt önskemål. Om parametern har utelämnats blir de genererade loggfilerna oändliga.

## <a name="yarn-log-configurations"></a>Garn logg konfiguration

Granska följande konfigurationer:

* Apache Ambari

    1. I en webbläsare går du till `https://CLUSTERNAME.azurehdinsight.net` , där `CLUSTERNAME` är namnet på klustret.

    1. Navigera till **Hive**-  >  **konfigurationer**  >  **Avancerad**  >  **Resource Manager**. Se till att **Aktivera logg agg regering** är markerat. Om det här alternativet är inaktiverat behåller Name-noderna loggarna lokalt och inte samman dem i fjärrarkivet vid slut för ande av program.

* Kontrollera att klusterstorleken är lämplig för arbetsbelastningen. Arbets belastningen kan ha ändrats nyligen eller också har klustret ändrat storlek. [Skala upp](../hdinsight-scaling-best-practices.md) klustret så att det matchar en högre arbets belastning.

* `/mnt/resource` kan fyllas med överblivna filer (som i fallet med Resource Manager omstart). Om det behövs kan du manuellt rensa `/mnt/resource/hadoop/yarn/log` och manuellt `/mnt/resource/hadoop/yarn/local` .

## <a name="next-steps"></a>Nästa steg

Om du inte ser problemet eller inte kan lösa problemet kan du gå till någon av följande kanaler för mer support:

* Få svar från Azure-experter via [Azure community support](https://azure.microsoft.com/support/community/).

* Anslut till [@AzureSupport](https://twitter.com/azuresupport) – det officiella Microsoft Azure kontot för att förbättra kund upplevelsen. Att ansluta Azure-communityn till rätt resurser: svar, support och experter.

* Om du behöver mer hjälp kan du skicka en support förfrågan från [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Välj **stöd** på Meny raden eller öppna **Hjälp + Support** Hub. Mer detaljerad information finns [i så här skapar du en support förfrågan för Azure](../../azure-portal/supportability/how-to-create-azure-support-request.md). Åtkomst till prenumerations hantering och fakturerings support ingår i din Microsoft Azure prenumeration och teknisk support tillhandahålls via ett av support avtalen för [Azure](https://azure.microsoft.com/support/plans/).