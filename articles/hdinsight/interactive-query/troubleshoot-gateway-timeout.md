---
title: Undantag vid körning av frågor från Apache Ambari Hive-vy i Azure HDInsight
description: Fel söknings steg när du kör Apache Hive frågor via Apache Ambari Hive-vyn i Azure HDInsight.
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 12/23/2019
ms.openlocfilehash: 40ef380cd2bd4743b92daf44a0a5b70ade1cbb35
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98933012"
---
# <a name="exception-when-running-queries-from-apache-ambari-hive-view-in-azure-hdinsight"></a>Undantag vid körning av frågor från Apache Ambari Hive-vy i Azure HDInsight

Den här artikeln beskriver fel söknings steg och möjliga lösningar för problem med att interagera med Azure HDInsight-kluster.

## <a name="issue"></a>Problem

När du kör en Apache Hive fråga från Apache Ambari Hive visas följande fel meddelande tillfälligt:

```error
Cannot create property 'errors' on string '<!DOCTYPE html PUBLIC '-//W3C//DTD XHTML 1.0 Strict//EN' 'http://www.w3.org/TR/xhtml1/DTD/xhtml1-strict.dtd'>
<html xmlns='http://www.w3.org/1999/xhtml'>
<head>
<title>IIS 8.5 Detailed Error - 502.3 - Bad Gateway</title>
```

## <a name="cause"></a>Orsak

En gateway-timeout.

Värdet för gateway-timeout är 2 minuter. Frågor från Ambari Hive-vy skickas till `/hive2` slut punkten via gatewayen. När frågan har kompilerats och godkänts returnerar HiveServer en `queryid` . Klienterna ska sedan söka efter status för frågan. Om HiveServer inte returnerar ett HTTP-svar inom 2 minuter under den här processen, genererar HDI-gatewayen ett timeout-fel på 502,3 Gateway till anroparen. Felen kan inträffa när frågan skickas för bearbetning (mer troligt) och även i Hämta status samtal (mindre troligt). Användarna kan se något av dem.

Http-hanterarens tråd ska vara snabb: förbereda jobbet och returnera ett `queryid` . Men på grund av flera orsaker kan alla hanterare av hanterare vara upptagna, vilket leder till timeout för nya frågor och get status anrop.

### <a name="responsibilities-of-the-http-handler-thread"></a>Ansvars områden för HTTP-hanterarens tråd

När klienten skickar en fråga till HiveServer sker följande i förgrunds tråden:

* Parsa begäran, utför semantisk verifiering
* Lås
* Metaarkiv-sökning vid behov
* Kompilera frågan (DDL eller DML)
* Förbered en frågeplan
* Utför auktorisering (kör alla tillämpliga Ranger-principer i säkra kluster)

## <a name="resolution"></a>Lösning

Några allmänna rekommendationer för att förbättra situationen:

* Om du använder en extern Hive-metaarkiv kontrollerar du DB-måtten och kontrollerar att databasen inte är överbelastad. Överväg att skala metaarkiv-databas skiktet.

* Se till att parallellt Ops är aktiverat (Detta gör att HTTP-hanterarens trådar kan köras parallellt). För att kontrol lera värdet startar du [Apache Ambari](../hdinsight-hadoop-manage-ambari.md) och navigerar till **Hive**  >  -**konfigurationer**  >  **Avancerad**  >  **anpassad Hive-plats**. Värdet för `hive.server2.parallel.ops.in.session` ska vara `true` .

* Se till att klustrets SKU för virtuell dator inte är för liten för belastningen. Överväg att dela arbetet mellan flera kluster. Mer information finns i [Välj en kluster typ](../hdinsight-capacity-planning.md#choose-a-cluster-type).

* Om Ranger är installerat i klustret kontrollerar du om det finns för många Ranger-principer som måste utvärderas för varje fråga. Sök efter dubbletter eller onödiga principer.

* Kontrol lera värdet för **HiveServer2 Heap-storlek** från Ambari. Navigera till konfiguration av **Hive**-  >    >  **Inställningar**  >  **optimering**. Kontrol lera att värdet är större än 10 GB. Justera efter behov för att optimera prestanda.

* Se till att Hive-frågan är väl justerad. Mer information finns i [optimera Apache Hive frågor i Azure HDInsight](../hdinsight-hadoop-optimize-hive-query.md).

## <a name="next-steps"></a>Nästa steg

Om du inte ser problemet eller inte kan lösa problemet kan du gå till någon av följande kanaler för mer support:

* Få svar från Azure-experter via [Azure community support](https://azure.microsoft.com/support/community/).

* Anslut till [@AzureSupport](https://twitter.com/azuresupport) – det officiella Microsoft Azure kontot för att förbättra kund upplevelsen. Att ansluta Azure-communityn till rätt resurser: svar, support och experter.

* Om du behöver mer hjälp kan du skicka en support förfrågan från [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Välj **stöd** på Meny raden eller öppna **Hjälp + Support** Hub. Mer detaljerad information finns [i så här skapar du en support förfrågan för Azure](../../azure-portal/supportability/how-to-create-azure-support-request.md). Åtkomst till prenumerations hantering och fakturerings support ingår i din Microsoft Azure prenumeration och teknisk support tillhandahålls via ett av support avtalen för [Azure](https://azure.microsoft.com/support/plans/).