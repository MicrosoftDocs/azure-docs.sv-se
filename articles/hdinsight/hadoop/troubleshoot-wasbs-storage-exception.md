---
title: Kontot som används har inte stöd för HTTP-fel i Azure HDInsight
description: Den här artikeln beskriver fel söknings steg och möjliga lösningar för problem med att interagera med Azure HDInsight-kluster.
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 02/06/2020
ms.openlocfilehash: 46063d5f2d9ff4b85914ad7c4cd74a2400298db0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "98943088"
---
# <a name="the-account-being-accessed-does-not-support-http-error-in-azure-hdinsight"></a>Kontot som används har inte stöd för HTTP-fel i Azure HDInsight

Den här artikeln beskriver fel söknings steg och möjliga lösningar för problem med att interagera med Azure HDInsight-kluster.

## <a name="issue"></a>Problem

Följande fel meddelande tas emot:

```
com.microsoft.azure.storage.StorageException: The account being accessed does not support http.
```

## <a name="cause"></a>Orsak

Det finns flera orsaker till varför fel meddelandet tas emot:

* Lagrings kontot har [säker överföring](../../storage/common/storage-require-secure-transfer.md) aktiverat och felaktigt [URI-schema](../hdinsight-hadoop-linux-information.md#URI-and-scheme) används.

* Ett kluster har skapats med ett lagrings konto som hade säker överföring *inaktiverat*. Därefter aktiverades säker överföring på lagrings kontot.

## <a name="resolution"></a>Lösning

Om säker överföring har Aktiver ATS för Azure Storage eller Data Lake Storage Gen2, skulle URI: n vara `wasbs://` respektive `abfss://` .  Se även [säker överföring](../../storage/common/storage-require-secure-transfer.md).

För nya kluster använder du ett lagrings konto som redan har den önskade inställningen för säker överföring. Ändra inte inställningen för säker överföring för ett lagrings konto som används av ett befintligt kluster.

## <a name="next-steps"></a>Nästa steg

Om du inte ser problemet eller inte kan lösa problemet kan du gå till någon av följande kanaler för mer support:

* Få svar från Azure-experter via [Azure community support](https://azure.microsoft.com/support/community/).

* Anslut till [@AzureSupport](https://twitter.com/azuresupport) – det officiella Microsoft Azure kontot för att förbättra kund upplevelsen. Att ansluta Azure-communityn till rätt resurser: svar, support och experter.

* Om du behöver mer hjälp kan du skicka en support förfrågan från [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Välj **stöd** på Meny raden eller öppna **Hjälp + Support** Hub. Mer detaljerad information finns [i så här skapar du en support förfrågan för Azure](../../azure-portal/supportability/how-to-create-azure-support-request.md). Åtkomst till prenumerations hantering och fakturerings support ingår i din Microsoft Azure prenumeration och teknisk support tillhandahålls via ett av support avtalen för [Azure](https://azure.microsoft.com/support/plans/).