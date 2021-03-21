---
title: Övervaknings kostnad för container Insights | Microsoft Docs
description: I den här artikeln beskrivs övervaknings kostnaden för mått & inventerings data som samlas in av behållar insikter för att hjälpa kunderna att hantera deras användning och tillhör ande kostnader.
ms.topic: conceptual
ms.date: 05/29/2020
ms.openlocfilehash: 78387e950d476126d7c2065a530844e44fd59b4f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "101728917"
---
# <a name="understand-monitoring-costs-for-container-insights"></a>Förstå övervaknings kostnader för behållar insikter

Den här artikeln innehåller pris vägledning för behållar insikter som hjälper dig att förstå följande:

* Så här beräknar du kostnader direkt innan du aktiverar den här insikten

* Så här mäter du kostnader efter att behållar insikter har Aktiver ATS för en eller flera behållare

* Så här kontrollerar du insamling av data och gör kostnads minskningar

Azure Monitor loggar samlar in, indexerar och lagrar data som genererats av ditt Kubernetes-kluster. 

Azure Monitor prissättnings modell baseras främst på mängden data som matas in i gigabyte per dag i Log Analytics-arbetsytan. Kostnaden för en Log Analytics-arbetsyta baseras bara på mängden data som samlas in, den är också beroende av den valda planen och hur länge du väljer att lagra data som genererats från dina kluster.

>[!NOTE]
>Alla storlekar och priser är endast för prov uppskattning. På sidan med Azure Monitor [priser](https://azure.microsoft.com/pricing/details/monitor/) finns de senaste priserna baserat på din Azure Monitor Log Analytics prissättnings modell och Azure-region.

Följande är en sammanfattning av vilka typer av data som samlas in från ett Kubernetes-kluster med behållar insikter som påverkar kostnader och som kan anpassas baserat på din användning:

- STDOUT, stderr container loggar från varje övervakad behållare i varje Kubernetes-namnrymd i klustret

- Variabler för container miljö från varje övervakad behållare i klustret

- Slutförda Kubernetes-jobb/poddar i klustret som inte kräver övervakning

- Aktiv kasse ring av Prometheus-mått

- [Diagnostisk logg samling](../../aks/view-control-plane-logs.md) av Kubernetes i ditt AKS-kluster för att analysera loggdata som skapats av huvud komponenter som *Kube-apiserver* och *Kube-Controller Manager*.

## <a name="what-is-collected-from-kubernetes-clusters"></a>Det som samlas in från Kubernetes-kluster

Behållar insikter innehåller en fördefinierad uppsättning mått och insamlade inventerings objekt som skrivs som loggdata i Log Analytics-arbetsytan. Alla mått i listan nedan samlas in som standard en gång i minuten.

### <a name="node-metrics-collected"></a>Nodens mått samlas in

Följande lista är de 24 måtten per nod som samlas in:

- cpuUsageNanoCores
- cpuCapacityNanoCores
- cpuAllocatableNanoCores
- memoryRssBytes
- memoryWorkingSetBytes
- memoryCapacityBytes
- memoryAllocatableBytes
- restartTimeEpoch
- används (disk)
- ledigt (disk)
- used_percent (disk)
- io_time (diskio)
- skrivningar (diskio)
- läsningar (diskio)
- write_bytes (diskio)
- write_time (diskio)
- iops_in_progress (diskio)
- read_bytes (diskio)
- read_time (diskio)
- err_in (net)
- err_out (net)
- bytes_recv (net)
- bytes_sent (net)
- Kubelet_docker_operations (Kubelet)

### <a name="container-metrics"></a>Container mått

Följande lista är åtta mått per behållare som samlas in:

- cpuUsageNanoCores
- cpuRequestNanoCores
- cpuLimitNanoCores
- memoryRssBytes
- memoryWorkingSetBytes
- memoryRequestBytes
- memoryLimitBytes
- restartTimeEpoch

### <a name="cluster-inventory"></a>Kluster inventering

Följande lista är de kluster inventerings data som samlas in som standard:

- KubePodInventory – 1 per minut per behållare
- KubeNodeInventory – 1 per nod per minut
- KubeServices – 1 per tjänst per minut
- ContainerInventory – 1 per behållare per minut

## <a name="estimating-costs-to-monitor-your-aks-cluster"></a>Uppskatta kostnader för att övervaka ditt AKS-kluster

Uppskattningen nedan baseras på ett Azure Kubernetes service-kluster (AKS) med följande storleks exempel. Dessutom gäller beräkningen endast för mått och inventerings data som samlas in. För behållar loggar (STDOUT-, stderr-och miljövariabler) varierar det beroende på de logg storlekar som genereras av arbets belastningen och de ingår inte i beräkningen.

Om du har aktiverat övervakning av ett AKS-kluster som har kon figurer ATS på följande sätt

- Tre noder
- Två diskar per nod
- Ett nätverks gränssnitt per nod
- 20 poddar (en behållare i varje Pod = 20 behållare totalt)
- Två Kubernetes-namnrymder
- Fem Kubernetes-tjänster (inklusive Kube-system poddar, tjänster och namnrymd)
- Samlings frekvens = 60 sekunder (standard)

Du kan se tabeller och volymer med data som genereras per timme i den tilldelade Log Analytics-arbetsytan. Mer information om var och en av dessa tabeller finns i [container poster](container-insights-log-search.md#container-records).

|Tabell | Storleks uppskattning (MB/timme) |
|------|---------------|
|Prest | 12,9 |
|InsightsMetrics | 11,3 |
|KubePodInventory | 1.5 |
|KubeNodeInventory | 0,75 |
|KubeServices | 0.13 |
|ContainerInventory | 3,6 |
|KubeHealth | 0.1 |
|KubeMonAgentEvents |0,005 |

Totalt = 31 MB/timme = 23,1 GB/månad (en månad = 31 dagar)

Med standard [priset](https://azure.microsoft.com/pricing/details/monitor/) för Log Analytics, som är en modell där du betalar per användning, kan du beräkna Azure Monitor kostnaden per månad. När du har inkluderat en kapacitets reservation blir priset högre per månad beroende på vilken reservation som valts.

## <a name="controlling-ingestion-to-reduce-cost"></a>Styr inmatningen för att minska kostnaderna

Överväg ett scenario där organisationens olika affär senhet delar Kubernetes-infrastruktur och en Log Analytics arbets yta. Med varje affär senhet separerad med ett Kubernetes-namnområde. Du kan visualisera hur mycket data som matas in i varje arbets yta med hjälp av runbooken för **data användning** som är tillgängligt i list rutan **Visa arbets böcker** .

[![Visa List rutan med arbets böcker](media/container-insights-cost/workbooks-dropdown.png)](media/container-insights-cost/workbooks-dropdown.png#lightbox)


Med den här arbets boken kan du visualisera källan till dina data utan att behöva bygga egna bibliotek med frågor från vad vi delar i vår dokumentation. I den här arbets boken finns det diagram med vilka du kan visa fakturerbara data från sådana perspektiv som:

- Totalt antal fakturerbara data som matats in i GB efter lösning
- Fakturerbara data som matats in av behållar loggar (program loggar)
- Fakturerbara container loggar inmatade data per Kubernetes-namnrymd
- Fakturerbara container loggar data som matats in åtskiljda av kluster namn
- Fakturerbara behållar logg data inmatat av logsource-post
- Fakturerbara diagnostikdata som matats in av loggar för den diagnostiska huvud noden

[![Arbets bok för data användning](media/container-insights-cost/data-usage-workbook.png)](media/container-insights-cost/data-usage-workbook.png#lightbox)

Om du vill veta mer om hur du hanterar rättigheter och behörigheter till arbets boken granskar du [åtkomst kontroll](../visualize/workbooks-access-control.md).

När du har slutfört analysen för att avgöra vilken källa eller vilka källor som genererar mest data eller data som överstiger dina krav kan du konfigurera om data insamling. Information om hur du konfigurerar insamling av STDOUT-, stderr-och miljövariabler beskrivs i artikeln [Konfigurera inställningar för agent data insamling](container-insights-agent-config.md) .

Följande är exempel på vilka ändringar du kan tillämpa på klustret genom att ändra ConfigMap-filen för att kontrol lera kostnaden.

1. Inaktivera STDOUT-loggar över alla namn områden i klustret genom att ändra följande i ConfigMap-filen:

    ```
    [log_collection_settings]       
       [log_collection_settings.stdout]          
          enabled = false
    ```

2. Inaktivera insamling av stderr-loggar från namn området för utveckling (till exempel **dev-test**) och fortsätt samla in stderr-loggar från andra namn områden (till exempel **Prod** och **default**) genom att ändra följande i ConfigMap-filen:

    >[!NOTE]
    >Kube-systemets logg samling är inaktive rad som standard. Standardinställningen behålls, Lägg till namn rymder för **dev-test** i listan över undantags namn rymder tillämpas på stderr-logg samling.

    ```
    [log_collection_settings.stderr]          
       enabled = true          
          exclude_namespaces = ["kube-system", "dev-test"]
    ```

3. Inaktivera samling av miljö variabel i klustret genom att ändra följande i ConfigMap-filen. Detta gäller för alla behållare i varje Kubernetes-namnområde. 

    ```
    [log_collection_settings.env_var]
        enabled = false
    ```

4. Om du vill rensa slutförda jobb anger du rensnings principen i jobb definitionen genom att ändra följande i ConfigMap-filen:

    ```
    apiVersion: batch/v1
    kind: Job
    metadata:
      name: pi-with-ttl
    spec:
      ttlSecondsAfterFinished: 100
    ```

När du har tillämpat en eller flera av dessa ändringar i din ConfigMaps, se [tillämpa uppdaterad ConfigMap](container-insights-prometheus-integration.md#applying-updated-configmap) för att tillämpa den på klustret.

### <a name="prometheus-metrics-scraping"></a>Prometheus mått

Om du använder [Prometheus](container-insights-prometheus-integration.md), se till att du funderar på följande om du vill begränsa antalet mått som du samlar in från klustret:

- Se till att kasse rings frekvensen är optimal (Standardvärdet är 60 sekunder). Även om du kan öka frekvensen till 15 sekunder måste du se till att de mått som du kasserar publiceras med den frekvensen. Annars kommer det att finnas många duplicerade mått och skickas till din Log Analytics arbets yta med intervall som lägger till data inmatning och kostnader för kvarhållning, men som är mindre värde. 

- Behållar insikter stöder undantag & inkluderings listor efter Metric Name. Om du till exempel tar bort **kubedns** -mått i klustret, kan det finnas hundratals av dem som är inkasserade som standard, men du är mest intresse rad av en delmängd. Bekräfta att du har angett en lista över mått att kassera eller exkludera andra förutom några för att spara på data inmatnings volymen. Det är enkelt att aktivera kassation och inte använda många av dessa mått, som bara lägger till ytterligare avgifter till din Log Analytics faktura.

- När du klipper igenom Pod anteckningar bör du kontrol lera att du filtrerar efter namn område så att du utesluter Pod mått från namn områden som du inte använder (till exempel **dev-test-** namnrymd).

## <a name="next-steps"></a>Nästa steg

Mer information om hur du kan förstå vad kostnaderna kommer att baseras på de senaste användnings mönstren från data som samlas in med container Insights finns i [Hantera din användning och beräkna kostnader](../logs/manage-cost-storage.md).