---
title: Visa mått i real tid med container Insights | Microsoft Docs
description: I den här artikeln beskrivs real tids visningen av mått utan att använda kubectl med behållar insikter.
ms.topic: conceptual
ms.date: 10/15/2019
ms.custom: references_regions
ms.openlocfilehash: 2e3a8a417a934374c2c0b256bb65a471d98fdebe
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "101731858"
---
# <a name="how-to-view-metrics-in-real-time"></a>Visa mått i real tid

Med funktionen för behållar insikter Live data (för hands version) kan du visualisera mått om Node-och Pod-tillstånd i ett kluster i real tid. Den emulerar direkt åtkomst till `kubectl top nodes` kommandon, `kubectl get pods –all-namespaces` och `kubectl get nodes` för att anropa, parsa och visualisera data i prestanda diagram som ingår i den här insikten.

Den här artikeln innehåller en detaljerad översikt och hjälper dig att förstå hur du använder den här funktionen.

>[!NOTE]
>AKS-kluster som är aktiverade som [privata kluster](https://azure.microsoft.com/updates/aks-private-cluster/) stöds inte med den här funktionen. För den här funktionen krävs direktåtkomst till Kubernetes-API:et via en proxyserver från din webbläsare. Om nätverkssäkerheten blockerar Kubernetes-API:et från den här proxyn blockeras trafiken.

Om du vill ha hjälp med att ställa in eller felsöka funktionen Live data (för hands version) läser du vår [installations guide](container-insights-livedata-setup.md).

## <a name="how-it-works"></a>Hur det fungerar

Funktionen Live data (för hands version) har direkt åtkomst till Kubernetes-API: et och ytterligare information om Authentication Model finns [här](https://kubernetes.io/docs/concepts/overview/kubernetes-api/).

Den här funktionen utför en avsöknings åtgärd mot Mät slut punkter (inklusive `/api/v1/nodes` , `/apis/metrics.k8s.io/v1beta1/nodes` och `/api/v1/pods` ), som är var femte sekund som standard. Dessa data cachelagras i webbläsaren och ritas i de fyra prestanda diagram som ingår i behållar insikter på fliken **kluster** genom att välja **Go Live (för hands version)**. Varje efterföljande avsökning är i ett diagram till en rullande fem minuters visualiserings period.

![Alternativet gå live i vyn kluster](./media/container-insights-livedata-metrics/cluster-view-go-live-example-01.png)

Avsöknings intervallet konfigureras från List rutan **uppsättnings intervall** så att du kan ställa in sökning efter nya data varje 1, 5, 15 och 30 sekunder.

![Gå in i list rutan för Live-avsöknings intervall](./media/container-insights-livedata-metrics/cluster-view-polling-interval-dropdown.png)

>[!IMPORTANT]
>Vi rekommenderar att du ställer in avsöknings intervallet på en sekund medan du felsöker ett problem under en kort tids period. Dessa förfrågningar kan påverka tillgängligheten och begränsningen av Kubernetes-API: et i klustret. Konfigurera därefter om till ett längre avsöknings intervall.

>[!IMPORTANT]
>Inga data lagras permanent under den här funktionens funktion. All information som samlas in under den här sessionen tas genast bort när du stänger webbläsaren eller navigerar bort från funktionen. Data är bara tillgängliga för visualisering i fem minuter-fönstret. alla mått som är äldre än fem minuter tas också bort permanent.

De här diagrammen kan inte fästas på den sista Azure-instrumentpanelen som du visade i Live-läge.

## <a name="metrics-captured"></a>Insamlade mått

### <a name="node-cpu-utilization---node-memory-utilization-"></a>Nod-CPU-användning%/nod minnes användning%

Dessa två prestanda diagram mappar till en motsvarighet till att anropa `kubectl top nodes` och samla in resultaten från kolumnerna **CPU%** och **minne%** till respektive diagram.

![Exempel resultat för Kubectl översta noder](./media/container-insights-livedata-metrics/kubectl-top-nodes-example.png)

![Diagram över processor användning i procent för noder](./media/container-insights-livedata-metrics/cluster-view-node-cpu-util.png)

![Diagram över användning av nod-minne i procent](./media/container-insights-livedata-metrics/cluster-view-node-memory-util.png)

Percentils beräkningarna fungerar i större kluster för att hjälpa till att identifiera avvikare-noder i klustret. Om du till exempel vill förstå om noder används under användning i skala nedåt. Om du använder den **minsta** agg regeringen kan du se vilka noder som har låg belastning i klustret. Om du vill undersöka ytterligare väljer du fliken **noder** och sorterar rutnätet efter processor-eller minnes användning.

Detta hjälper dig också att förstå vilka noder som håller på att flyttas till deras gränser och om det kan krävas en utskalning. Användning av både **Max** -och **P95** -aggregeringarna kan hjälpa dig att se om det finns noder i klustret med hög resursutnyttjande. För ytterligare undersökning skulle du återigen växla till fliken **noder** .

### <a name="node-count"></a>Nodantal

Det här prestanda diagrammet mappar till en motsvarighet till att anropa `kubectl get nodes` och mappa kolumnen **status** till ett diagram, grupperat efter status typer.

![Exempel resultat för Kubectl get Nodes](./media/container-insights-livedata-metrics/kubectl-get-nodes-example.png)

![Diagram över antal noder](./media/container-insights-livedata-metrics/cluster-view-node-count-01.png)

Noder rapporteras antingen i ett **klart** eller **inte klart** tillstånd. De räknas (och ett totalt antal skapas) och resultaten av dessa två agg regeringar visas i diagram.
Om du till exempel vill förstå om noderna hamnar i fel tillstånd. Om du använder den **ej färdiga** agg regeringen kan du snabbt se antalet noder i klustret för närvarande i läget **inte redo** .

### <a name="active-pod-count"></a>Antal aktiva Pod

Det här prestanda diagrammet mappar till en motsvarighet till att anropa `kubectl get pods –all-namespaces` och mappar **status** kolumnen till diagrammet grupperat efter status typer.

![Kubectl Hämta poddar exempel resultat](./media/container-insights-livedata-metrics/kubectl-get-pods-example.png)

![Diagram över antal Pod](./media/container-insights-livedata-metrics/cluster-view-node-pod-count.png)

>[!NOTE]
>Namn på status som tolkas av `kubectl` kanske inte exakt matchar i diagrammet.

## <a name="next-steps"></a>Nästa steg

Visa [exempel på logg frågor](container-insights-log-search.md#search-logs-to-analyze-data) för att se fördefinierade frågor och exempel för att skapa aviseringar, visualiseringar eller utföra ytterligare analyser av klustren.
