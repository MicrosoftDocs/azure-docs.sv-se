---
title: REST API att fråga Apache HBase i Azure HDInsight
description: I den här artikeln beskrivs fel söknings steg när du interagerar med Apache HBase-komponenter i Azure HDInsight-kluster.
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 04/08/2020
ms.openlocfilehash: 636f84c4c6aa097288dc2fb5481dcedd6863409d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "98942870"
---
# <a name="rest-api-to-query-apache-hbase-in-azure-hdinsight"></a>REST API att fråga Apache HBase i Azure HDInsight

Den här artikeln beskriver fel söknings steg och möjliga lösningar för problem med att interagera med Azure HDInsight-kluster.

## <a name="issue"></a>Problem

Använda Apache HBase REST-gränssnittet för att fråga en tabell under ett annat namn område än standard resultatet i ett körnings fel (HTTP-status 500).

## <a name="cause"></a>Orsak

HBase REST API stöds bara när du använder standard namn området. Detta är ett känt problem med att använda HBase-namnområden eller att göra anrop som refererar till särskilda på kolumner med kolumn familjer med REST server på HDInsight. Detta beror på ett säkerhets problem med HDInsight-gatewayen. När du använder API: et för att skapa en tabell med ett namn område, till gång till kolumner via kolumn familjer, måste du ange ett av `:` tecknen, vilket betraktas som ett säkerhets problem i IIS Gateway-modulen.

## <a name="mitigation"></a>Åtgärd

Kringgå Gateway/REST-servern genom att distribuera ditt program på en virtuell dator som finns i samma virtuella Azure-VNet som HDInsight-klustret. Sedan kan du kommunicera med HBase antingen direkt via RPC (förbigå REST-servern helt) eller till enskilda REST-servrar som körs på arbetsnoderna som kringgår HDInsight-gatewayer.

## <a name="next-steps"></a>Nästa steg

Om du inte ser problemet eller inte kan lösa problemet kan du gå till någon av följande kanaler för mer support:

* Få svar från Azure-experter via [Azure community support](https://azure.microsoft.com/support/community/).

* Anslut till [@AzureSupport](https://twitter.com/azuresupport) – det officiella Microsoft Azure kontot för att förbättra kund upplevelsen. Att ansluta Azure-communityn till rätt resurser: svar, support och experter.

* Om du behöver mer hjälp kan du skicka en support förfrågan från [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Välj **stöd** på Meny raden eller öppna **Hjälp + Support** Hub. Mer detaljerad information finns [i så här skapar du en support förfrågan för Azure](../../azure-portal/supportability/how-to-create-azure-support-request.md). Åtkomst till prenumerations hantering och fakturerings support ingår i din Microsoft Azure prenumeration och teknisk support tillhandahålls via ett av support avtalen för [Azure](https://azure.microsoft.com/support/plans/).