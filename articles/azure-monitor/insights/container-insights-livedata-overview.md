---
title: Visa real tids data (för hands version) med Azure Monitor för behållare | Microsoft Docs
description: Den här artikeln beskriver real tids visningen av Kubernetes-loggar, händelser och Pod mått utan att använda kubectl i Azure Monitor för behållare.
ms.topic: conceptual
ms.date: 12/17/2020
ms.custom: references_regions
ms.openlocfilehash: 3655ff8e5879aa4113753b5529c1e484fb079401
ms.sourcegitcommit: d79513b2589a62c52bddd9c7bd0b4d6498805dbe
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/18/2020
ms.locfileid: "97672876"
---
# <a name="how-to-view-kubernetes-logs-events-and-pod-metrics-in-real-time"></a>Så här visar du Kubernetes-loggar, händelser och Pod-mått i real tid

Azure Monitor for containers innehåller funktionen Live data (för hands version), som är en avancerad diagnostisk funktion som gör att du direkt kan komma åt dina AKS (Azure Kubernetes service)-behållar loggar (STDOUT/stderror), händelser och Pod mått. Den visar direkt åtkomst till `kubectl logs -c` , `kubectl get` händelser och `kubectl top pods` . I ett konsol fönster visas loggar, händelser och mått som genereras av behållar motorn för att hjälpa till med fel sökning av problem i real tid.

Den här artikeln innehåller en detaljerad översikt och hjälper dig att förstå hur du använder den här funktionen.

Om du vill ha hjälp med att ställa in eller felsöka funktionen Live data (för hands version) läser du vår [installations guide](container-insights-livedata-setup.md). Den här funktionen har direkt åtkomst till Kubernetes-API: et och ytterligare information om Authentication-modellen finns [här](https://kubernetes.io/docs/concepts/overview/kubernetes-api/).

## <a name="view-deployment-live-logs-preview"></a>Visa distributions Live-loggar (för hands version)
Använd följande procedur för att Visa Live-loggar för distributioner som ingår i AKS-kluster som inte övervakas av Azure Monitor för behållare. Om klustret använder Azure Monitor för behållare ska du använda processen nedan för att Visa real tids data för noder, styrenheter, behållare och distributioner.

1. I Azure Portal bläddrar du till kluster resurs gruppen AKS och väljer din AKS-resurs.

2. Välj **arbets belastningar** i avsnittet **Kubernetes-resurser** på menyn.

3. Välj en distribution på fliken **distributioner** .

4. Välj **Live-loggar (för hands version)** på distributions menyn.

5. Välj en POD för att starta insamling av Live-data.

    [![Distribuera Live-loggar](./media/container-insights-livedata-overview/live-data-deployment.png)](./media/container-insights-livedata-overview/live-data-deployment.png#lightbox)

## <a name="view-logs"></a>Visa loggar

Du kan visa real tids logg data när de genereras av behållar motorn från vyn **noder**, **kontrollanter** och **behållare** . Utför följande steg för att Visa loggdata.

1. I Azure Portal bläddrar du till kluster resurs gruppen AKS och väljer din AKS-resurs.

2. På instrument panelen för AKS under **övervakning** till vänster väljer du **insikter**.

3. Välj antingen fliken **noder**, **styrenheter** eller **behållare** .

4. Välj ett objekt i prestanda rutnätet och välj alternativet **Visa live data (förhands granskning)** i fönstret Egenskaper på höger sida. Om AKS-klustret har kon figurer ATS med enkel inloggning med Azure AD uppmanas du att autentisera vid första användningen under webbläsarsessionen. Välj ditt konto och slutför autentisering med Azure.

    >[!NOTE]
    >När du visar data från din Log Analytics arbets yta genom att välja **vyn i Analytics** från fönstret Egenskaper kan logg Sök resultaten Visa **noder**, **daemon-uppsättningar**, **replik uppsättningar**, **jobb**, cron- **jobb**, **poddar** och **behållare** som kanske inte längre finns. Försök att söka efter loggar för en behållare som inte är tillgänglig i `kubectl` kommer också att Miss lyckas här. Granska [vyn i analys](container-insights-log-search.md#search-logs-to-analyze-data) funktionen om du vill veta mer om att visa historiska loggar, händelser och mått.

När du har autentiserat visas konsol fönstret Live data (förhands granskning) under rutnätet för prestanda data där du kan visa loggdata i en kontinuerlig data ström. Om status indikatorn för hämtningen visar en grön bock markering, som är längst till höger i fönstret, innebär det att data kan hämtas och att det börjar strömma till konsolen.

![Fönstret Egenskaper för Node-fönstret Visa data alternativ](./media/container-insights-livedata-overview/node-properties-pane.png)

Rutans rubrik visar namnet på pod som behållaren är grupperad med.

## <a name="view-events"></a>Visa händelser

Du kan visa händelse data i real tid när de genereras av behållar motorn från **noden noder**, **styrenheter**, **behållare** och **distributioner (för hands version)** när en behållare, pod, nod, ReplicaSet, DaemonSet, Job, CronJob eller distribution har valts. Utför följande steg för att visa händelser.

1. I Azure Portal bläddrar du till kluster resurs gruppen AKS och väljer din AKS-resurs.

2. På instrument panelen för AKS under **övervakning** till vänster väljer du **insikter**.

3. Välj antingen fliken **noder**, **styrenheter**, **behållare** eller **distributioner (för hands version)** .

4. Välj ett objekt i prestanda rutnätet och välj alternativet **Visa live data (förhands granskning)** i fönstret Egenskaper på höger sida. Om AKS-klustret har kon figurer ATS med enkel inloggning med Azure AD uppmanas du att autentisera vid första användningen under webbläsarsessionen. Välj ditt konto och slutför autentisering med Azure.

    >[!NOTE]
    >När du visar data från din Log Analytics arbets yta genom att välja **vyn i Analytics** från fönstret Egenskaper kan logg Sök resultaten Visa **noder**, **daemon-uppsättningar**, **replik uppsättningar**, **jobb**, cron- **jobb**, **poddar** och **behållare** som kanske inte längre finns. Försök att söka efter loggar för en behållare som inte är tillgänglig i `kubectl` kommer också att Miss lyckas här. Granska [vyn i analys](container-insights-log-search.md#search-logs-to-analyze-data) funktionen om du vill veta mer om att visa historiska loggar, händelser och mått.

När du har autentiserat visas konsol fönstret Live data (förhands granskning) under rutnätet med prestanda data. Om status indikatorn för hämtningen visar en grön bock markering, som är längst till höger i fönstret, innebär det att data kan hämtas och att det börjar strömma till konsolen.

Om objektet du har valt är en behållare väljer du alternativet **händelser** i rutan. Om du har valt en nod, POD eller kontrollant väljs Visa händelser automatiskt.

![Fönstret Egenskaper för kontroll panelen Visa händelser](./media/container-insights-livedata-overview/controller-properties-live-event.png)

Rutans rubrik visar namnet på pod som behållaren är grupperad med.

### <a name="filter-events"></a>Filtrera händelser

När du visar händelser kan du dessutom begränsa resultaten med hjälp av **filtret** Pill som finns till höger om Sök fältet. Beroende på vilken resurs du har valt visar Pill en pod, ett namn område eller ett kluster att välja från.

## <a name="view-metrics"></a>Visa mått

Du kan visa data för real tids mått när de genereras av behållar motorn från vyn **noder** eller **kontrollanter** endast när en **Pod** har valts. Utför följande steg för att visa mått.

1. I Azure Portal bläddrar du till kluster resurs gruppen AKS och väljer din AKS-resurs.

2. På instrument panelen för AKS under **övervakning** till vänster väljer du **insikter**.

3. Välj antingen fliken **noder** eller **styrenheter** .

4. Välj ett **Pod** -objekt i prestanda rutnätet och välj alternativet **Visa live data (förhands granskning)** i fönstret Egenskaper på höger sida. Om AKS-klustret har kon figurer ATS med enkel inloggning med Azure AD uppmanas du att autentisera vid första användningen under webbläsarsessionen. Välj ditt konto och slutför autentisering med Azure.

    >[!NOTE]
    >När du visar data från din Log Analytics arbets yta genom att välja **vyn i Analytics** från fönstret Egenskaper kan logg Sök resultaten Visa **noder**, **daemon-uppsättningar**, **replik uppsättningar**, **jobb**, cron- **jobb**, **poddar** och **behållare** som kanske inte längre finns. Försök att söka efter loggar för en behållare som inte är tillgänglig i `kubectl` kommer också att Miss lyckas här. Granska [vyn i analys](container-insights-log-search.md#search-logs-to-analyze-data) funktionen om du vill veta mer om att visa historiska loggar, händelser och mått.

När du har autentiserat visas konsol fönstret Live data (förhands granskning) under rutnätet med prestanda data. Mät data hämtas och börjar strömma till konsolen för presentation i de två diagrammen. Rutans rubrik visar namnet på pod som behållaren är grupperad med.

![Visa Pod Metrics-exempel](./media/container-insights-livedata-overview/pod-properties-live-metrics.png)

## <a name="using-live-data-views"></a>Använda Live data-vyer
I följande avsnitt beskrivs funktioner som du kan använda i olika vyer för Live-data.

### <a name="search"></a>Search
Funktionen Live data (för hands version) innehåller Sök funktioner. I **Sök** fältet kan du filtrera resultaten genom att skriva ett nyckel ord eller en term, och eventuella matchande resultat markeras för att tillåta snabb granskning. När du visar händelser kan du dessutom begränsa resultaten med hjälp av **filtret** Pill som finns till höger om Sök fältet. Beroende på vilken resurs du har valt visar Pill en pod, ett namn område eller ett kluster att välja från.

![Filter exempel för Live data konsol fönster](./media/container-insights-livedata-overview/livedata-pane-filter-example.png)

![Filter exempel för Live data konsol fönster för distribution](./media/container-insights-livedata-overview/live-data-deployment-search.png)

### <a name="scroll-lock-and-pause"></a>Rulla låset och pausa

Om du vill pausa autorullningen och kontrol lera beteendet för fönstret, så att du kan bläddra manuellt genom de nya data som lästs, kan du använda **rullnings** alternativet. Om du vill aktivera autorullning igen väljer du bara **rullnings** alternativet igen. Du kan också pausa hämtningen av logg-eller händelse data genom att välja alternativet **pausa** och när du är redo att återuppta väljer du **spela** bara.

![Fönstret Live data konsol pausa Live-vyn](./media/container-insights-livedata-overview/livedata-pane-scroll-pause-example.png)

![Fönstret Live data konsol pausa Live-vyn för distribution](./media/container-insights-livedata-overview/live-data-deployment-pause.png)



>[!IMPORTANT]
>Vi rekommenderar att du bara pausar eller pausar den här snabb rullningen under en kort tids period när du felsöker ett problem. Dessa förfrågningar kan påverka tillgängligheten och begränsningen av Kubernetes-API: et i klustret.

>[!IMPORTANT]
>Inga data lagras permanent under den här funktionens funktion. All information som registreras under sessionen tas bort när du stänger webbläsaren eller navigerar bort från den. Data är bara tillgängliga för visualisering i fönstret med måtten 5 minuter i mått funktionen. alla mått som är äldre än fem minuter tas också bort. Bufferten för Live data (förhands granskning) inom rimlig minnes användnings gränser.

## <a name="next-steps"></a>Nästa steg

- Om du vill fortsätta lära dig hur du använder Azure Monitor och övervakar andra aspekter av ditt AKS-kluster kan du läsa mer i [Visa Azure Kubernetes service Health](container-insights-analyze.md).

- Visa [exempel på logg frågor](container-insights-log-search.md#search-logs-to-analyze-data) för att se fördefinierade frågor och exempel för att skapa aviseringar, visualiseringar eller utföra ytterligare analyser av klustren.
