---
title: Visa distributioner av container Insights (för hands version) | Microsoft Docs
description: Den här artikeln beskriver real tids visningen av Kubernetes-distributioner utan att använda kubectl i behållar insikter.
ms.topic: conceptual
ms.date: 10/15/2019
ms.custom: references_regions
ms.openlocfilehash: c6eaac209234f7352395502c6761312cf258108f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "101713923"
---
# <a name="how-to-view-deployments-preview-in-real-time"></a>Visa distributioner (för hands version) i real tid

Med container Insights emulerar funktionen Visa distributioner (för hands version) direkt åtkomst till Kubernetes-distributions objekt i real tid genom att exponera- `kubeclt get deployments` och- `kubectl describe deployment {your deployment}` kommandon.

>[!NOTE]
>AKS-kluster som är aktiverade som [privata kluster](https://azure.microsoft.com/updates/aks-private-cluster/) stöds inte med den här funktionen. För den här funktionen krävs direktåtkomst till Kubernetes-API:et via en proxyserver från din webbläsare. Om nätverkssäkerheten blockerar Kubernetes-API:et från den här proxyn blockeras trafiken.

Läs mer i Kubernetes-dokumentationen om [distributioner](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/).

## <a name="how-it-works"></a>Så här fungerar det

Funktionen Live data (för hands version) har direkt åtkomst till Kubernetes-API: et och ytterligare information om Authentication Model finns [här](https://kubernetes.io/docs/concepts/overview/kubernetes-api/).

Funktionen distributioner (för hands version) utför en enda tidpunkt (uppdaterings bar) belastning mot distributions slut punkten `/apis/apps/v1/deployments` . Det gör att du kan välja en specifik distribution och läsa in Beskriv information för den aktuella distributionen mot distributions slut punkten `/apis/apps/v1/namespaces/${nameSpace}/deployments/${deploymentName}` .

Om du väljer **Uppdatera** högst upp till vänster på sidan uppdateras distributions listan. Detta simulerar att köra kommandot på nytt `kubectl` .

>[!IMPORTANT]
>Inga data lagras permanent under den här funktionens funktion. All information som registreras under sessionen tas bort när du stänger webbläsaren eller navigerar bort från den.

>[!NOTE]
>Du kan inte fästa Live data-data (för hands version) från-konsolen till en Azure-instrumentpanel.

## <a name="deployments-describe"></a>Distributioner beskriver

Om du vill visa mer information om en distribution, vilket är detsamma som `kubectl describe deployment` , utför du följande steg.

1. I Azure Portal bläddrar du till kluster resurs gruppen AKS och väljer din AKS-resurs.

2. På instrument panelen för AKS under **övervakning** till vänster väljer du **insikter**.

3. Välj fliken **distributioner (för hands version)** .

    ![Vyn distributioner i Azure Portal](./media/container-insights-livedata-deployments/deployment-view.png)

Vyn visar en lista över alla distributioner som körs tillsammans med namn området och annan detaljerad information, som emulerar körningen av kommandot `kubectl get deployments –all-namespaces` . Du kan sortera resultaten genom att markera någon av kolumnerna.

![Information om rutan Egenskaper för distribution](./media/container-insights-livedata-deployments/deployment-properties-pane-details.png)

När du väljer en distribution i listan visas ett egenskaps fönster automatiskt till höger på sidan. Den visar information som rör den valda distributionen som du skulle visa om du körde kommandot `kubectl describe deployment {deploymentName}` . Du kanske har lagt märke till att information saknas i beskrivningen. Framför allt är **mallen** saknas. Genom att välja fliken **RAW** kan du navigera till den avparsade informationen.

![Information om fönstret distributions egenskaper rå data](./media/container-insights-livedata-deployments/deployment-properties-pane-raw.png)

När du granskar distributions information kan du se behållar loggar och händelser i real tid. Välj konsol fönstret **Visa Live-konsolen** och live data (förhands granskning) visas under data rutnätet distributioner där du kan visa Live-loggdata i en kontinuerlig data ström. Om status indikatorn för hämtningen visar en grön bock markering, som är längst till höger i fönstret, innebär det att data kan hämtas och att det börjar strömma till konsolen.

Du kan också filtrera efter namn område eller kluster nivå händelser. Mer information om hur du visar data i real tid i-konsolen finns i [Visa real tids data (för hands version) med behållar insikter](container-insights-livedata-overview.md).

![Distributioner Visa real tids data i-konsolen](./media/container-insights-livedata-deployments/deployments-console-view-events.png)

## <a name="next-steps"></a>Nästa steg

- Om du vill fortsätta lära dig hur du använder Azure Monitor och övervakar andra aspekter av ditt AKS-kluster kan du läsa mer i [Visa Azure Kubernetes service Health](container-insights-analyze.md).

- Visa [exempel på logg frågor](container-insights-log-search.md#search-logs-to-analyze-data) för att se fördefinierade frågor och exempel för att skapa aviseringar, visualiseringar eller utföra ytterligare analyser av klustren.
