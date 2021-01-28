---
title: Övervaka kluster tillgänglighet med Azure Monitor loggar i HDInsight
description: Lär dig hur du använder Azure Monitor loggar för att övervaka kluster hälsa och tillgänglighet.
ms.service: hdinsight
ms.topic: how-to
ms.date: 08/12/2020
ms.openlocfilehash: d52cb1c5f3b1dd1b23adb39f2f65d0e66968e482
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/28/2021
ms.locfileid: "98946949"
---
# <a name="how-to-monitor-cluster-availability-with-azure-monitor-logs-in-hdinsight"></a>Övervaka kluster tillgänglighet med Azure Monitor loggar i HDInsight

HDInsight-kluster inkluderar Azure Monitor loggar integrering, som tillhandahåller frågor som kan ha frågor och länkar, samt konfigurerbara aviseringar. Den här artikeln visar hur du använder Azure Monitor för att övervaka klustret.

## <a name="azure-monitor-logs-integration"></a>Azure Monitor loggar integrering

Azure Monitor loggar aktiverar data som genereras av flera resurser, till exempel HDInsight-kluster, som ska samlas in och aggregeras på ett ställe för att uppnå en enhetlig övervaknings upplevelse.

Som en förutsättning måste du ha en Log Analytics arbets yta för att lagra insamlade data. Om du inte redan har skapat en, kan du följa anvisningarna här: [skapa en Log Analytics-arbetsyta](../azure-monitor/learn/quick-create-workspace.md).

## <a name="enable-hdinsight-azure-monitor-logs-integration"></a>Aktivera integrering av HDInsight Azure Monitor-loggar

Välj **Azure Monitor** på sidan HDInsight-kluster resurs i portalen. Välj sedan **Aktivera** och välj din Log Analytics arbets yta i list rutan.

![HDInsight Operations Management Suite](media/cluster-availability-monitor-logs/azure-portal-monitoring.png)

Som standard installeras OMS-agenten på alla klusternoder utom för Edge-noder. Eftersom ingen OMS-agent är installerad på klustrets Edge-noder finns det ingen telemetri om Edge-noder som finns i Log Analytics som standard.

## <a name="query-metrics-and-logs-tables"></a>Fråga mått och logga tabeller

När Azure Monitor logg integrering har Aktiver ATS (det här kan ta några minuter) navigerar du till din **Log Analytics arbets ytans** resurs och väljer **loggar**.

![Log Analytics arbets ytans loggar](media/cluster-availability-monitor-logs/hdinsight-portal-logs.png)

Loggar visar ett antal exempel frågor, till exempel:

| Frågenamn                      | Description                                                               |
|---------------------------------|---------------------------------------------------------------------------|
| Tillgänglighet för datorer idag    | Rita ett diagram över antalet datorer som skickar loggar, varje timme                     |
| Visa pulsslag                 | Visa en lista över alla dator pulsslag från den senaste timmen                           |
| Senaste pulsslag för varje dator | Visa det sista pulsslaget som skickats av varje dator                             |
| Ej tillgängliga datorer           | Visa en lista med alla kända datorer som inte skickade några pulsslag under de senaste 5 timmarna |
| Tillgänglighets hastighet               | Beräkna tillgänglighets frekvensen för varje ansluten dator                |

Du kan till exempel köra frågan **tillgänglighets frekvens** genom att välja **Kör** på den frågan, som visas i skärm bilden ovan. Då visas tillgänglighets takten för varje nod i klustret i procent. Om du har aktiverat flera HDInsight-kluster för att skicka mått till samma Log Analytics arbets yta, ser du tillgänglighets takten för alla noder (exklusive Edge-noder) i de kluster som visas.

![Exempel fråga för "tillgänglighets hastighet" för Log Analytics arbets ytan](media/cluster-availability-monitor-logs/portal-availability-rate.png)

> [!NOTE]  
> Tillgänglighets takten mäts under en 24-timmarsperiod, så klustret måste köras i minst 24 timmar innan du kan se korrekt tillgänglighets taxa.

Du kan fästa den här tabellen på en delad instrument panel genom att klicka på **Fäst** i det övre högra hörnet. Om du inte har några skrivbara, delade instrument paneler kan du se hur du skapar en här: [skapa och dela instrument paneler i Azure Portal](../azure-portal/azure-portal-dashboards.md#publish-and-share-a-dashboard).

## <a name="azure-monitor-alerts"></a>Azure Monitor aviseringar

Du kan också ställa in Azure Monitor aviseringar som ska utlösas när värdet för ett mått eller resultatet av en fråga uppfyller vissa villkor. Vi kan till exempel skapa en avisering för att skicka ett e-postmeddelande när en eller flera noder inte har skickat ett pulsslag på 5 timmar (dvs. förmodas vara otillgänglig).

Från **loggar** kör du exempel frågan **otillgängliga datorer** genom att välja **Kör** på den frågan, som visas nedan.

![Exempel på Log Analytics arbets yta loggar "ej tillgängliga datorer"](media/cluster-availability-monitor-logs/portal-unavailable-computers.png)

Om alla noder är tillgängliga ska den här frågan returnera noll resultat för tillfället. Klicka på **ny varnings regel** för att börja konfigurera aviseringen för den här frågan.

![Log Analytics arbets ytans nya aviserings regel](media/cluster-availability-monitor-logs/portal-logs-new-alert-rule.png)

Det finns tre komponenter i en avisering: den *resurs* för vilken du vill skapa regeln (Log Analytics arbets ytan i det här fallet), *villkoret* för att utlösa aviseringen och de *Åtgärds grupper* som avgör vad som ska hända när aviseringen utlöses.
Klicka på **villkors rubriken** som visas nedan för att slutföra konfigurationen av signal logiken.

![Portal varning Skapa regel villkor](media/cluster-availability-monitor-logs/portal-condition-title.png)

Då öppnas **Konfigurera signal logik**.

Ange **aviserings logik** avsnittet enligt följande:

*Baserat på: antal resultat, villkor: är större än, tröskelvärde: 0.*

Eftersom den här frågan bara returnerar otillgängliga noder som resultat, bör aviseringen utlösas om antalet resultat någonsin är större än 0.

I avsnittet **utvärdera baserat på** anger du **period** och **frekvens** baserat på hur ofta du vill söka efter otillgängliga noder.

För den här aviseringen ska du se till att **period = frekvens.** Mer information om period, frekvens och andra aviserings parametrar hittar du [här](../azure-monitor/platform/alerts-unified-log.md#alert-logic-definition).

Välj **klar** när du är klar med konfigurationen av signal logiken.

![Varnings regeln konfigurerar signal logik](media/cluster-availability-monitor-logs/portal-configure-signal-logic.png)

Om du inte redan har en befintlig åtgärds grupp klickar du på **Skapa ny** under avsnittet **Åtgärds grupper** .

![Varnings regeln skapar en ny åtgärds grupp](media/cluster-availability-monitor-logs/portal-create-new-action-group.png)

Då öppnas **Lägg till åtgärds grupp**. Välj ett **Åtgärds grupp namn**, **kort namn**, **prenumeration** och **resurs grupp.** Under avsnittet **åtgärder** väljer du ett **Åtgärds namn** och väljer **e-post/SMS/push/röst** som **Åtgärds typ.**

> [!NOTE]
> Det finns flera andra åtgärder som en avisering kan utlösa förutom e-post/SMS/push/Voice, till exempel en Azure Function-, LogicApp-, webhook-, ITSM-och Automation-Runbook. [Lära sig mer.](../azure-monitor/platform/action-groups.md#action-specific-information)

Då öppnas **e-post/SMS/push/röst**. Välj ett **namn** för mottagaren, **Markera** rutan **e-** postadress och skriv en e-postadress som du vill att aviseringen ska skickas till. Välj **OK** i  **e-post/SMS/push/Voice**, sedan i **Lägg till åtgärds grupp** för att slutföra konfigurationen av åtgärds gruppen.

![Varnings regeln skapar Lägg till åtgärds grupp](media/cluster-availability-monitor-logs/portal-add-action-group.png)

När bladen stängs bör du se din åtgärds grupp i avsnittet **Åtgärds grupper** . Slutligen fyller du i avsnittet **aviserings information** genom att ange ett namn och en **Beskrivning** för **varnings regeln** och välja en **allvarlighets grad**. Klicka på **skapa aviserings regel** för att slutföra.

![Portalen skapar varnings regeln slutförd](media/cluster-availability-monitor-logs/portal-create-alert-rule-finish.png)

> [!TIP]
> Möjligheten att ange **allvarlighets grad** är ett kraftfullt verktyg som kan användas när du skapar flera aviseringar. Du kan till exempel skapa en avisering för att utlösa en varning (allvarlighets grad 1) om en enda Head-nod går ned och en annan avisering som aktiverar kritisk (allvarlighets grad 0) i förmodad händelse att båda huvudnoderna går ned.

När villkoret för den här aviseringen uppfylls, utlöses aviseringen och du får ett e-postmeddelande med aviserings informationen så här:

![Exempel på Azure Monitor aviserings meddelande](media/cluster-availability-monitor-logs/portal-oms-alert-email.png)

Du kan också Visa alla aviseringar som har utlösts, grupperade efter allvarlighets grad, genom att gå till **aviseringar** i **arbets ytan Log Analytics**.

![Aviseringar för Log Analytics arbets ytan](media/cluster-availability-monitor-logs/hdi-portal-oms-alerts.png)

Om du väljer en allvarlighets GRADS gruppering (t. ex. **allvarlighets grad 1 som är** markerad ovan) visas poster för alla aviseringar med den allvarlighets grad som har utlösts som nedan:

![Log Analytics arbets yta allvarlighets grad en avisering](media/cluster-availability-monitor-logs/portal-oms-alerts-sev1.png)

## <a name="next-steps"></a>Nästa steg

* [Klustertillgänglighet – Apache Ambari](./hdinsight-cluster-availability.md)
* [Använda Azure Monitor loggar](hdinsight-hadoop-oms-log-analytics-tutorial.md)
