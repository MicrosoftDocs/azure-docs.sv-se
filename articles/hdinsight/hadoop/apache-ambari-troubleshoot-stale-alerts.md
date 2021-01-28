---
title: Apache Ambari inaktuella aviseringar i Azure HDInsight
description: Diskussion och analys av möjliga orsaker och lösningar för inaktiva Apache Ambari-aviseringar i HDInsight.
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 01/22/2020
ms.openlocfilehash: e31e5d5a5b27f4dcb267905eae6329666fc3292c
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/28/2021
ms.locfileid: "98946719"
---
# <a name="scenario-apache-ambari-stale-alerts-in-azure-hdinsight"></a>Scenario: Apache Ambari inaktuella aviseringar i Azure HDInsight

Den här artikeln beskriver fel söknings steg och möjliga lösningar för problem med att interagera med Azure HDInsight-kluster.

## <a name="issue"></a>Problem

I Apache Ambari-ANVÄNDARGRÄNSSNITTET kan du se en avisering som detta:

![Exempel på Apache Ambari-inaktuell avisering](./media/apache-ambari-troubleshoot-stale-alerts/ambari-stale-alerts-example.png)

## <a name="cause"></a>Orsak

Ambari-agenter övervakar kontinuerligt hälso tillståndet för många resurser. *Aviseringar* kan konfigureras för att meddela dig om vissa kluster egenskaper ligger inom de förinställda tröskelvärdena. När varje resurs kontroll körs, om aviserings villkoret är uppfyllt, rapporterar Ambari-agenterna status tillbaka till Ambari-servern och utlöser en avisering. Om en avisering inte är markerad enligt intervallet i sin aviserings profil, utlöser servern en avisering om *inaktuella aviseringar för Ambari Server* .

Det finns olika orsaker till varför en hälso kontroll kanske inte kan köras med det definierade intervallet:

* Värdarna är hårt använda (hög CPU-användning), så att Ambari-agenten inte kan få tillräckligt med system resurser för att köra aviseringarna på tid.

* Klustret är upptaget med att köra många jobb eller tjänster under en hög belastnings period.

* Ett litet antal värdar i klustret är värd för många komponenter och det krävs för att köra många aviseringar. Om antalet komponenter är stort kan aviserings jobben sakna de schemalagda intervallen.

## <a name="resolution"></a>Lösning

Prova följande metoder för att lösa problem med Ambari inaktuella aviseringar.

### <a name="increase-the-alert-interval-time"></a>Öka tiden för aviserings intervall

Du kan öka värdet för ett enskilt aviserings intervall baserat på klustrets svars tid och belastning:

1. I Apache Ambari-ANVÄNDARGRÄNSSNITTET väljer du fliken **aviseringar** .
1. Välj det namn på aviserings definitionen som du vill använda.
1. Från definitionen väljer du **Redigera**.
1. Öka värdet för **kontroll intervall** och välj sedan **Spara**.

### <a name="increase-the-alert-interval-time-for-ambari-server-alerts"></a>Öka aviserings intervall tiden för Ambari Server-aviseringar

1. I Apache Ambari-ANVÄNDARGRÄNSSNITTET väljer du fliken **aviseringar** .
1. I list rutan **grupper** väljer du **AMBARI default**.
1. Välj aviseringen **Ambari Server aviseringar** .
1. Från definitionen väljer du **Redigera**.
1. Öka värdet för **kontroll intervallet** .
1. Öka värdet för **intervall multiplikatorn** och välj sedan **Spara**.

### <a name="disable-and-reenable-the-alert"></a>Inaktivera och återaktivera aviseringen

Om du vill ta bort en inaktuell avisering inaktiverar du den och aktiverar den igen:

1. I Apache Ambari-ANVÄNDARGRÄNSSNITTET väljer du fliken **aviseringar** .
1. Välj det namn på aviserings definitionen som du vill använda.
1. Från definitionen väljer du **aktive rad** längst till höger i användar gränssnittet.
1. I popup-fönstret **bekräftelse** väljer du **Bekräfta inaktive ring**.
1. Vänta några sekunder för alla aviseringar "instanser" som visas på sidan som ska rensas.
1. Från definitionen väljer du **inaktive rad** längst till höger i användar gränssnittet.
1. Välj **Bekräfta aktivering** i popup-fönstret **bekräftelse** .

### <a name="increase-the-alert-grace-period"></a>Öka tids perioden för aviseringar

Det finns en respitperiod innan en Ambari-agent rapporterar att en konfigurerad avisering missade sitt schema. Om aviseringen missade sin schemalagda tid men kördes inom respitperioden, genereras inte den inaktuella varningen.

`alert_grace_period`Standardvärdet är 5 sekunder. Du kan konfigurera den här inställningen i/etc/Ambari-agent/conf/ambari-agent.ini. Försök att öka värdet till 10 för värdar där inaktuella aviseringar sker med jämna mellanrum. Starta sedan om Ambari-agenten.

## <a name="next-steps"></a>Nästa steg

Om problemet inte nämns här, eller om du inte kan lösa det, kan du gå till någon av följande kanaler för mer support:

* Få svar från Azure-experter på [Azure community support](https://azure.microsoft.com/support/community/).

* Anslut till [@AzureSupport](https://twitter.com/azuresupport) på Twitter. Detta är det officiella Microsoft Azure kontot för att förbättra kund upplevelsen. Den ansluter Azure-communityn till rätt resurser: svar, support och experter.

* Om du behöver mer hjälp kan du skicka en support förfrågan från [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Välj hjälp (**?**) på Portal menyn eller öppna **Hjälp + Support** -fönstret för att komma dit. Mer information finns i [så här skapar du en support förfrågan för Azure](../../azure-portal/supportability/how-to-create-azure-support-request.md). 

  Support för prenumerations hantering och fakturering ingår i Microsoft Azure prenumerationen. Teknisk support är tillgänglig via support avtalen för [Azure](https://azure.microsoft.com/support/plans/).