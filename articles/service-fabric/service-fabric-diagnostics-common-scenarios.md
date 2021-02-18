---
title: Azure Service Fabric diagnostisera vanliga scenarier
description: Lär dig mer om fel sökning av vanliga övervaknings-och diagnostiska scenarier i Azure Service Fabric-program.
ms.topic: article
ms.date: 02/25/2019
ms.openlocfilehash: 3083348948ce97ae9d99098896878d79081a15f0
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/17/2021
ms.locfileid: "100580484"
---
# <a name="diagnose-common-scenarios-with-service-fabric"></a>Diagnostisera vanliga scenarier med Service Fabric

Den här artikeln visar vanliga scenarier som användare har påträffat i övervaknings-och diagnostik-avsnittet med Service Fabric. De scenarier som presenteras gäller alla tre lager av Service Fabric: program, kluster och infrastruktur. Varje lösning använder Application Insights och Azure Monitor loggar, Azure övervaknings verktyg, för att slutföra varje scenario. Stegen i varje lösning ger användarna en introduktion till hur du använder Application Insights och Azure Monitor loggar i samband med Service Fabric.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites-and-recommendations"></a>Krav och rekommendationer

Lösningarna i den här artikeln kommer att använda följande verktyg. Vi rekommenderar att du har dessa inställningar och konfigurerade:

* [Application Insights med Service Fabric](service-fabric-tutorial-monitoring-aspnet.md)
* [Aktivera Azure-diagnostik i ditt kluster](service-fabric-diagnostics-event-aggregation-wad.md)
* [Konfigurera en Log Analytics-arbetsyta](service-fabric-diagnostics-oms-setup.md)
* [Log Analytics agent för att spåra prestanda räknare](service-fabric-diagnostics-oms-agent.md)

## <a name="how-can-i-see-unhandled-exceptions-in-my-application"></a>Hur kan jag se ohanterade undantag i mitt program?

1. Navigera till Application Insights resurs som ditt program har kon figurer ATS med.
2. Klicka på *Sök* längst upp till vänster. Klicka sedan på filter på nästa panel.

    ![Översikt över AI](media/service-fabric-diagnostics-common-scenarios/ai-search-filter.png)

3. Du kan se flera typer av händelser (spår, begär Anden, anpassade händelser). Välj "undantag" som filter.

    ![Filter lista för AI](media/service-fabric-diagnostics-common-scenarios/ai-filter-list.png)

    Genom att klicka på ett undantag i listan kan du titta närmare på mer information, inklusive tjänstens kontext om du använder Service Fabric Application Insights SDK.

    ![AI-undantag](media/service-fabric-diagnostics-common-scenarios/ai-exception.png)

## <a name="how-do-i-view-which-http-calls-are-used-in-my-services"></a>Hur gör jag för att visar vilka HTTP-anrop som används i mina tjänster?

1. I samma Application Insights resurs kan du filtrera efter "förfrågningar" i stället för undantag och Visa alla begär Anden som gjorts
2. Om du använder Service Fabric Application Insights SDK kan du se en visuell representation av dina tjänster som är anslutna till varandra och antalet lyckade och misslyckade förfrågningar. Till vänster klickar du på "program karta"

    ![AI app Map blad ](media/service-fabric-diagnostics-common-scenarios/app-map-blade.png) ![ AI-app Map](media/service-fabric-diagnostics-common-scenarios/app-map-new.png)

    Mer information om program kartan finns i [dokumentationen till program kartan](../azure-monitor/app/app-map.md)

## <a name="how-do-i-create-an-alert-when-a-node-goes-down"></a>Hur gör jag för att skapa en avisering när en nod slutar fungera

1. Node-händelser spåras av Service Fabric-klustret. Navigera till Service Fabric-analys lösnings resurs med namnet **ServiceFabric (NameofResourceGroup)**
2. Klicka på diagrammet längst ned på bladet "Sammanfattning"

    ![Lösning för Azure Monitor loggar](media/service-fabric-diagnostics-common-scenarios/oms-solution-azure-portal.png)

3. Här finns många grafer och paneler som visar olika mått. Klicka på ett av diagrammen så tas du till loggs ökningen. Här kan du fråga efter eventuella kluster händelser eller prestanda räknare.
4. Ange följande fråga. Dessa händelse-ID: n finns i [noden händelse referens](service-fabric-diagnostics-event-generation-operational.md#application-events)

    ```kusto
    ServiceFabricOperationalEvent
    | where EventID >= 25622 and EventID <= 25626
    ```

5. Klicka på "ny varnings regel" längst upp och när som helst när en händelse tas emot baserat på den här frågan får du en avisering i den valda kommunikations metoden.

    ![Azure Monitor loggar ny avisering](media/service-fabric-diagnostics-common-scenarios/oms-create-alert.png)

## <a name="how-can-i-be-alerted-of-application-upgrade-rollbacks"></a>Hur kan jag få aviseringar om återställningar av program uppgraderingar?

1. I samma logg Sök fönster som innan du anger följande fråga för återställnings återställningar. Dessa händelse-ID: n finns under [program händelse referens](service-fabric-diagnostics-event-generation-operational.md#application-events)

    ```kusto
    ServiceFabricOperationalEvent
    | where EventID == 29623 or EventID == 29624
    ```

2. Klicka på "ny varnings regel" längst upp och när som helst när en händelse tas emot baserat på den här frågan får du en avisering.

## <a name="how-do-i-see-container-metrics"></a>Hur gör jag för att se container mått?

I samma vy med alla grafer visas vissa paneler för dina behållares prestanda. Du behöver en [övervaknings lösning](service-fabric-diagnostics-oms-containers.md) för Log Analytics agent och behållare för att dessa paneler ska fyllas i.

![Log Analytics container mått](media/service-fabric-diagnostics-common-scenarios/containermetrics.png)

>[!NOTE]
>För att kunna instrumentera telemetri inifrån **din behållare** måste du lägga till [Application Insights NuGet-paketet för behållare](https://github.com/Microsoft/ApplicationInsights-servicefabric#microsoftapplicationinsightsservicefabric--for-service-fabric-lift-and-shift-scenarios).

## <a name="how-can-i-monitor-performance-counters"></a>Hur kan jag övervaka prestanda räknare?

1. När du har lagt till Log Analytics agenten i klustret måste du lägga till de aktuella prestanda räknare som du vill spåra. Gå till sidan Log Analytics arbets yta i portalen – från lösningens sida finns fliken arbets yta på den vänstra menyn.

    ![Fliken Log Analytics arbets yta](media/service-fabric-diagnostics-common-scenarios/workspacetab.png)

2. När du är på arbets ytans sida klickar du på "avancerade inställningar" på samma vänstra meny.

    ![Log Analytics avancerade inställningar](media/service-fabric-diagnostics-common-scenarios/advancedsettingsoms.png)

3. Klicka på data > prestanda räknare i Windows (data > Linux prestanda räknare för Linux-datorer) för att börja samla in vissa räknare från noderna via Log Analytics agenten. Här följer exempel på formatet för räknare som ska läggas till

   * `.NET CLR Memory(<ProcessNameHere>)\\# Total committed Bytes`
   * `Processor(_Total)\\% Processor Time`

     I snabb starten är VotingData och VotingWeb de process namn som används, så spårning av dessa räknare skulle se ut

   * `.NET CLR Memory(VotingData)\\# Total committed Bytes`
   * `.NET CLR Memory(VotingWeb)\\# Total committed Bytes`

     ![Log Analytics prestanda räknare](media/service-fabric-diagnostics-common-scenarios/omsperfcounters.png)

4. På så sätt kan du se hur infrastrukturen hanterar dina arbets belastningar och ange relevanta aviseringar baserat på resursutnyttjande. Till exempel – du kanske vill ange en avisering om den totala processor belastningen sträcker sig över 90% eller lägre än 5%. Det räknar namn som du skulle använda för detta är "% processor tid". Du kan göra detta genom att skapa en varnings regel för följande fråga:

    ```kusto
    Perf | where CounterName == "% Processor Time" and InstanceName == "_Total" | where CounterValue >= 90 or CounterValue <= 5.
    ```

## <a name="how-do-i-track-performance-of-my-reliable-services-and-actors"></a>Hur gör jag för att du spåra prestanda för mina Reliable Services och aktörer?

Om du vill spåra prestanda för Reliable Services eller aktörer i dina program, bör du samla in Service Fabric aktör, aktörs metod, tjänst och tjänst metod räknare också. Här följer några exempel på pålitliga prestanda räknare för tjänst och skådespelare som samlas in

>[!NOTE]
>Service Fabric prestanda räknare kan inte samlas in av Log Analytics agent för närvarande, men kan samlas in av [andra diagnostiska lösningar](service-fabric-diagnostics-partners.md)

* `Service Fabric Service(*)\\Average milliseconds per request`
* `Service Fabric Service Method(*)\\Invocations/Sec`
* `Service Fabric Actor(*)\\Average milliseconds per request`
* `Service Fabric Actor Method(*)\\Invocations/Sec`

Se dessa länkar för en fullständig lista över prestanda räknare för pålitliga [tjänster](service-fabric-reliable-serviceremoting-diagnostics.md) och [aktörer](service-fabric-reliable-actors-diagnostics.md)

## <a name="next-steps"></a>Nästa steg

* [Leta upp vanliga aktiverings fel för kod paket](./service-fabric-diagnostics-code-package-errors.md)
* [Konfigurera aviseringar i AI](../azure-monitor/alerts/alerts-log.md) för att få meddelanden om förändringar i prestanda eller användning
* [Smart identifiering i Application Insights](../azure-monitor/app/proactive-diagnostics.md) utför en proaktiv analys av telemetri som skickas till AI för att varna dig om potentiella prestanda problem
* Läs mer om hur du Azure Monitor loggar [varningar](../azure-monitor/alerts/alerts-overview.md) till hjälp vid identifiering och diagnostik.
* För lokala kluster erbjuder Azure Monitor-loggar en gateway (HTTP Forward proxy) som kan användas för att skicka data till Azure Monitor loggar. Läs mer om det i [ansluta datorer utan Internet åtkomst till Azure Monitor loggar med hjälp av Log Analytics Gateway](../azure-monitor/agents/gateway.md)
* Bekanta dig med funktionerna för [loggs ökning och frågor](../azure-monitor/logs/log-query-overview.md) som erbjuds som en del av Azure Monitor loggar
* Få en mer detaljerad översikt över Azure Monitor loggar och vad den erbjuder, Läs [Vad är Azure Monitor loggar?](../azure-monitor/overview.md)
