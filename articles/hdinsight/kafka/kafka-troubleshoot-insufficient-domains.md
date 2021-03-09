---
title: Otillräckligt antal fel domäner i regions fel i Azure HDInsight
description: Det gick inte att skapa kluster på grund av otillräckliga fel domäner i region i Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 08/09/2019
ms.openlocfilehash: ed9e98750a469f78855096c3149254cf92c2788f
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/09/2021
ms.locfileid: "102501353"
---
# <a name="scenario-cluster-creation-failed-due-to-not-sufficient-fault-domains-in-region-in-azure-hdinsight"></a>Scenario: det gick inte att skapa kluster på grund av `not sufficient fault domains in region` i Azure HDInsight

Den här artikeln beskriver fel söknings steg och möjliga lösningar för problem med att interagera med Azure HDInsight-kluster.

## <a name="issue"></a>Problem

Få ett fel meddelande som liknar `not sufficient fault domains in region` när du försöker skapa Apache Kafka klustret.

## <a name="cause"></a>Orsak

En feldomän är en logisk gruppering av underliggande maskinvara i ett Azure-datacenter. Varje feldomän delar en gemensam strömkälla och nätverksbrytare. De virtuella datorer och hanterade diskar som implementerar noderna i ett HDInsight-kluster är fördelade mellan dessa feldomäner. Den här arkitekturen begränsar de potentiella problemen vid fysiska maskinvarufel.

Varje Azure-region har ett visst antal feldomäner. En lista över domäner och antalet fel domäner som de innehåller finns i dokumentationen om [tillgänglighets uppsättningar](../../virtual-machines/availability.md).

I HDInsight måste Kafka-kluster vara etablerade i en region med minst tre fel domäner.

## <a name="resolution"></a>Lösning

Om den region som du vill skapa klustret inte har tillräckligt med fel domäner kan du kontakta produkt teamet för att tillåta etablering av klustret även om det inte finns tre fel domäner.

## <a name="next-steps"></a>Nästa steg

Om du inte ser problemet eller inte kan lösa problemet kan du gå till någon av följande kanaler för mer support:

* Få svar från Azure-experter via [Azure community support](https://azure.microsoft.com/support/community/).

* Anslut till [@AzureSupport](https://twitter.com/azuresupport) – det officiella Microsoft Azure kontot för att förbättra kund upplevelsen. Att ansluta Azure-communityn till rätt resurser: svar, support och experter.

* Om du behöver mer hjälp kan du skicka en support förfrågan från [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Välj **stöd** på Meny raden eller öppna **Hjälp + Support** Hub. Mer detaljerad information finns [i så här skapar du en support förfrågan för Azure](../../azure-portal/supportability/how-to-create-azure-support-request.md). Åtkomst till prenumerations hantering och fakturerings support ingår i din Microsoft Azure prenumeration och teknisk support tillhandahålls via ett av support avtalen för [Azure](https://azure.microsoft.com/support/plans/).