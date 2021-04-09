---
title: Lagrings undantag efter återställning av anslutning i Azure HDInsight
description: Lagrings undantag efter återställning av anslutning i Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 08/08/2019
ms.openlocfilehash: 82cad7fc68d650e5f525a8722d3e2f3e9865f456
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "98936746"
---
# <a name="scenario-storage-exception-after-connection-reset-in-azure-hdinsight"></a>Scenario: lagrings undantag efter återställning av anslutningen i Azure HDInsight

Den här artikeln beskriver fel söknings steg och möjliga lösningar för problem med att interagera med Azure HDInsight-kluster.

## <a name="issue"></a>Problem

Det gick inte att skapa en ny Apache HBase-tabell.

## <a name="cause"></a>Orsak

Vid en tabell trunkering uppstod ett problem med lagrings anslutningen. Tabell posten har tagits bort i HBase metadata-tabell. Alla utom en BLOB-fil har tagits bort.

Även om det inte fanns någon mapp-BLOB `/hbase/data/default/ThatTable` som heter satt i lagringen. WASB-drivrutinen påträffade förekomsten av ovanstående BLOB-filen och tillåter inte att någon BLOB anropas `/hbase/data/default/ThatTable` eftersom den är förväntad, vilket innebär att det inte går att skapa tabellen.

## <a name="resolution"></a>Lösning

1. Starta om den aktiva HMaster från Apache Ambari UI. På så sätt kan en av de två vänte läges HMaster bli den aktiva och den nya aktiva HMaster kommer att läsa in informationen om metadata-tabellen. Därför visas inte `already-deleted` tabellen i HMaster-användargränssnittet.

1. Du kan hitta den överblivna BLOB-filen från UI-verktyg som Cloud Explorer eller köra kommandot som `hdfs dfs -ls /xxxxxx/yyyyy` . Kör `hdfs dfs -rmr /xxxxx/yyyy` för att ta bort denna blob. Till exempel `hdfs dfs -rmr /hbase/data/default/ThatTable/ThatFile`.

Nu kan du skapa en ny tabell med samma namn i HBase.

## <a name="next-steps"></a>Nästa steg

Om du inte ser problemet eller inte kan lösa problemet kan du gå till någon av följande kanaler för mer support:

* Få svar från Azure-experter via [Azure community support](https://azure.microsoft.com/support/community/).

* Anslut till [@AzureSupport](https://twitter.com/azuresupport) – det officiella Microsoft Azure kontot för att förbättra kund upplevelsen. Att ansluta Azure-communityn till rätt resurser: svar, support och experter.

* Om du behöver mer hjälp kan du skicka en support förfrågan från [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Välj **stöd** på Meny raden eller öppna **Hjälp + Support** Hub. Mer detaljerad information finns [i så här skapar du en support förfrågan för Azure](../../azure-portal/supportability/how-to-create-azure-support-request.md). Åtkomst till prenumerations hantering och fakturerings support ingår i din Microsoft Azure prenumeration och teknisk support tillhandahålls via ett av support avtalen för [Azure](https://azure.microsoft.com/support/plans/).