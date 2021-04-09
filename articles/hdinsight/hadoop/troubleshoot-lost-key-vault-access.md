---
title: Azure HDInsight-kluster med disk kryptering förlorar Key Vault åtkomst
description: Fel söknings steg och möjliga lösningar för Key Vault åtkomst problem när du interagerar med Azure HDInsight-kluster.
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.openlocfilehash: ce2929ca84746de1ab8b51882f3004c3699f17ca
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "98943122"
---
# <a name="scenario-azure-hdinsight-clusters-with-disk-encryption-lose-key-vault-access"></a>Scenario: Azure HDInsight-kluster med disk kryptering förlorar Key Vault åtkomst

Den här artikeln beskriver fel söknings steg och möjliga lösningar för problem med att interagera med Azure HDInsight-kluster.

## <a name="issue"></a>Problem

RHC-aviseringen (Resource Health Center) `The HDInsight cluster is unable to access the key for BYOK encryption at rest` visas för Bring Your Own Key-kluster (BYOK) där klusternoderna har förlorat åtkomst till kunder Key Vault (kV). Liknande aviseringar kan också visas på Apache Ambari UI.

## <a name="cause"></a>Orsak

Aviseringen säkerställer att KV kan nås från klusternoderna, vilket säkerställer nätverks anslutningen, KV Health och åtkomst principen för användaren som tilldelats den hanterade identiteten. Den här aviseringen är bara en varning om den förestående avslutningen av koordinatorn vid efterföljande omstarter av noder, klustret fortsätter att fungera tills noderna startas om.

Gå till Apache Ambari UI om du vill ha mer information om aviseringen från **disk kryptering Key Vault status**. Aviseringen innehåller information om orsaken till verifierings felet.

## <a name="resolution"></a>Lösning

### <a name="kvaad-outage"></a>KV/AAD-avbrott

Mer information finns på sidan [Azure Key Vault tillgänglighet och redundans](../../key-vault/general/disaster-recovery-guidance.md) och Azure-status. https://status.azure.com/

### <a name="kv-accidental-deletion"></a>KV, oavsiktlig borttagning

* Återställ borttagen nyckel för KV till automatisk återställning. Mer information finns i [Recover Deleted Key](/rest/api/keyvault/recoverdeletedkey).
* Kontakta KV-teamet för att återställa efter oavsiktlig borttagning.

### <a name="kv-access-policy-changed"></a>Princip för KV-åtkomst har ändrats

Återställ åtkomst principerna för användaren som tilldelats den hanterade identitet som har tilldelats HDI-klustret för att komma åt KV.

### <a name="key-permitted-operations"></a>Viktiga tillåtna åtgärder

För varje nyckel i KV kan du välja en uppsättning tillåtna åtgärder. Kontrol lera att du har aktiverat åtgärder för att figursätta och packa upp för BYOK-nyckeln

### <a name="expired-key"></a>Utgången nyckel

Om förfallo datumet har passerat och nyckeln inte roteras, återställer du nyckeln från backup HSM eller kontakt KV team för att ta bort utgångs datumet.

### <a name="kv-firewall-blocking-access"></a>KV-brandvägg blockerar åtkomst

Korrigera inställningarna för KV-brandväggen så att BYOK-klusternoder får åtkomst till KV.

### <a name="nsg-rules-on-virtual-network-blocking-access"></a>NSG-regler för blockering av virtuella nätverk

Kontrol lera de NSG-regler som är associerade med det virtuella nätverk som är kopplat till klustret.

## <a name="mitigation-and-prevention-steps"></a>Åtgärder för minskning och skydd

### <a name="kv-accidental-deletion"></a>KV, oavsiktlig borttagning

* Konfigurera Key Vault med [resurs lås uppsättning](../../azure-resource-manager/management/lock-resources.md).
* Säkerhetskopiera nycklar till en modul för maskin varu säkerhet.

### <a name="key-deletion"></a>Nyckel borttagning

Klustret bör tas bort innan nyckeln tas bort.

### <a name="kv-access-policy-changed"></a>Princip för KV-åtkomst har ändrats

Granska och testa åtkomst principer regelbundet.

### <a name="expired-key"></a>Utgången nyckel

* Säkerhetskopiera nycklar till HSM.
* Använd en nyckel utan att ange någon förfallo datum.
* Om förfallo datum måste anges roterar du nycklarna före förfallo datumet.

## <a name="next-steps"></a>Nästa steg

Om du inte ser problemet eller inte kan lösa problemet kan du gå till någon av följande kanaler för mer support:

* Få svar från Azure-experter via [Azure community support](https://azure.microsoft.com/support/community/).

* Anslut till [@AzureSupport](https://twitter.com/azuresupport) – det officiella Microsoft Azure kontot för att förbättra kund upplevelsen. Att ansluta Azure-communityn till rätt resurser: svar, support och experter.

* Om du behöver mer hjälp kan du skicka en support förfrågan från [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Välj **stöd** på Meny raden eller öppna **Hjälp + Support** Hub. Mer detaljerad information finns [i så här skapar du en support förfrågan för Azure](../../azure-portal/supportability/how-to-create-azure-support-request.md). Åtkomst till prenumerations hantering och fakturerings support ingår i din Microsoft Azure prenumeration och teknisk support tillhandahålls via ett av support avtalen för [Azure](https://azure.microsoft.com/support/plans/).