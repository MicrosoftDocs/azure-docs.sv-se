---
title: Felsöka WASB fil åtgärder i Azure HDInsight
description: Beskriver fel söknings steg och möjliga lösningar för problem med att interagera med Azure HDInsight-kluster.
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 02/18/2020
ms.openlocfilehash: f0f06c81906116dc278377cf9fd8871e8899a1d1
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98938743"
---
# <a name="debug-wasb-file-operations-in-azure-hdinsight"></a>Felsöka WASB fil åtgärder i Azure HDInsight

Det finns tillfällen när du kanske vill förstå vilka åtgärder som driv rutinen för WASB startade med Azure Storage. För klient sidan skapar WASB-drivrutinen loggar för varje fil Systems åtgärd på **fel söknings** nivå. WASB-drivrutinen använder Log4J för att kontrol lera loggnings nivån och standardvärdet är **informations** nivån. Azure Storage analys loggar på Server sidan finns i [Azure Storage Analytics-loggning](../../storage/common/storage-analytics-logging.md).

En genererad logg ser ut ungefär så här:

```log
18/05/13 04:15:55 DEBUG NativeAzureFileSystem: Moving wasb://xxx@yyy.blob.core.windows.net/user/livy/ulysses.txt/_temporary/0/_temporary/attempt_20180513041552_0000_m_000000_0/part-00000 to wasb://xxx@yyy.blob.core.windows.net/user/livy/ulysses.txt/part-00000
```

## <a name="turn-on-wasb-debug-log-for-file-operations"></a>Aktivera WASB-felsöknings logg för fil åtgärder

1. I en webbläsare går du till `https://CLUSTERNAME.azurehdinsight.net/#/main/services/SPARK2/configs` , där `CLUSTERNAME` är namnet på ditt Spark-kluster.

1. Gå till **avancerade spark2-log4j-Properties**.

    1. Ändra `log4j.appender.console.Threshold=INFO` till `log4j.appender.console.Threshold=DEBUG` .

    1. Lägg till `log4j.logger.org.apache.hadoop.fs.azure.NativeAzureFileSystem=DEBUG` .

1. Gå till **avancerade livy2-log4j-Properties**.

    Lägg till `log4j.logger.org.apache.hadoop.fs.azure.NativeAzureFileSystem=DEBUG` .

1. Spara ändringar.

## <a name="additional-logging"></a>Ytterligare loggning

Ovanstående loggar bör ge en övergripande förståelse för fil system åtgärderna. Om ovanstående loggar fortfarande inte ger värdefull information, eller om du vill undersöka Blob Storage API-anrop, lägger du till `fs.azure.storage.client.logging=true` i `core-site` . Med den här inställningen aktive ras Java SDK-loggar för wasb lagrings driv rutin och alla anrop till Blob Storage-servern skrivs ut. Ta bort inställningen efter utredningar eftersom den kan fylla upp disken snabbt och kan sakta ned processen.

Om Server delen är Azure Data Lake baserad använder du följande log4j-inställning för komponenten (till exempel Spark/Tez/HDFS):

```
log4j.logger.com.microsoft.azure.datalake.store=ALL,adlsFile
log4j.additivity.com.microsoft.azure.datalake.store=true
log4j.appender.adlsFile=org.apache.log4j.FileAppender
log4j.appender.adlsFile.File=/var/log/adl/adl.log
log4j.appender.adlsFile.layout=org.apache.log4j.PatternLayout
log4j.appender.adlsFile.layout.ConversionPattern=%p\t%d{ISO8601}\t%r\t%c\t[%t]\t%m%n
```

Leta efter loggarna i `/var/log/adl/adl.log` för loggarna.

## <a name="next-steps"></a>Nästa steg

Om du inte ser problemet eller inte kan lösa problemet kan du gå till någon av följande kanaler för mer support:

* Få svar från Azure-experter via [Azure community support](https://azure.microsoft.com/support/community/).

* Anslut till [@AzureSupport](https://twitter.com/azuresupport) – det officiella Microsoft Azure kontot för att förbättra kund upplevelsen. Att ansluta Azure-communityn till rätt resurser: svar, support och experter.

* Om du behöver mer hjälp kan du skicka en support förfrågan från [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Välj **stöd** på Meny raden eller öppna **Hjälp + Support** Hub. Mer detaljerad information finns [i så här skapar du en support förfrågan för Azure](../../azure-portal/supportability/how-to-create-azure-support-request.md). Åtkomst till prenumerations hantering och fakturerings support ingår i din Microsoft Azure prenumeration och teknisk support tillhandahålls via ett av support avtalen för [Azure](https://azure.microsoft.com/support/plans/).