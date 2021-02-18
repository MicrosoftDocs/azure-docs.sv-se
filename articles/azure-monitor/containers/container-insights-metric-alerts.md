---
title: Mått varningar från Azure Monitor för behållare
description: Den här artikeln granskar rekommenderade mått varningar som är tillgängliga från Azure Monitor för behållare i offentlig för hands version.
ms.topic: conceptual
ms.date: 10/28/2020
ms.openlocfilehash: 59c8d7b58809c981130d2ce92406fb5b1ce146ff
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/17/2021
ms.locfileid: "100624533"
---
# <a name="recommended-metric-alerts-preview-from-azure-monitor-for-containers"></a>Rekommenderade mått varningar (förhands granskning) från Azure Monitor för behållare

Om du vill varna vid system resurs problem när de har hög efter frågan och köra nära kapacitet, med Azure Monitor för behållare, skapar du en logg avisering baserat på prestanda data som lagras i Azure Monitor loggarna. Azure Monitor för behållare innehåller nu förkonfigurerade mått aviserings regler för ditt AKS-och Azure Arc-Kubernetes-kluster, som finns i offentlig för hands version.

Den här artikeln granskar erfarenheten och ger vägledning om hur du konfigurerar och hanterar dessa aviserings regler.

Om du inte är bekant med Azure Monitor aviseringar, se [Översikt över aviseringar i Microsoft Azure](../platform/alerts-overview.md) innan du börjar. Om du vill veta mer om mått aviseringar, se [mått varningar i Azure Monitor](../alerts/alerts-metric-overview.md).

## <a name="prerequisites"></a>Förutsättningar

Innan du börjar ska du kontrol lera följande:

* Anpassade mått är bara tillgängliga i en delmängd av Azure-regioner. En lista över regioner som stöds finns dokumenterade i [regioner som stöds](../essentials/metrics-custom-overview.md#supported-regions).

* För att stödja mått aviseringar och införandet av ytterligare mått, är den lägsta agent version som krävs **MCR.Microsoft.com/azuremonitor/containerinsights/ciprod:ciprod05262020** för AKS och **MCR.Microsoft.com/azuremonitor/containerinsights/ciprod:ciprod09252020** för Azure Arc-aktiverade Kubernetes-kluster.

    För att verifiera att klustret kör den nyare versionen av agenten kan du antingen:

    * Kör kommandot: `kubectl describe <omsagent-pod-name> --namespace=kube-system` . Observera värdet under **bild** för omsagent i avsnittet *behållare* i utdata i status returnerat. 
    * På fliken **noder** väljer du noden kluster och i rutan **Egenskaper** till höger, noterar du värdet under **agent avbildnings tag gen**.

    Värdet som visas för AKS ska vara version **ciprod05262020** eller senare. Värdet som visas för Azure Arc Enabled Kubernetes-kluster ska vara version **ciprod09252020** eller senare. Om du har en äldre version av klustret läser du så här [uppgraderar du Azure Monitor för behållare agent](container-insights-manage-agent.md#upgrade-agent-on-aks-cluster) för att hämta den senaste versionen.

    Mer information om agent versionen finns i [agentens versions historik](https://github.com/microsoft/docker-provider/tree/ci_feature_prod). Du kan kontrol lera att måtten samlas in genom att använda Azure Monitor Metrics Explorer och kontrol lera från **mått namn området** som **insikter** visas. Om så är fallet kan du gå vidare och börja konfigurera aviseringarna. Om du inte ser några mått som samlas in, saknar kluster tjänstens huvud namn eller MSI de nödvändiga behörigheterna. Kontrol lera att SPN eller MSI är medlem i rollen **övervaknings mått utgivar** roll genom att följa stegen som beskrivs i avsnittet [Uppgradera per kluster med Azure CLI](container-insights-update-metrics.md#upgrade-per-cluster-using-azure-cli) för att bekräfta och ange roll tilldelning.

## <a name="alert-rules-overview"></a>Översikt över aviserings regler

För att varna om vad som är viktigt, innehåller Azure Monitor för behållare följande mått aviseringar för dina AKS-och Azure Arc-aktiverade Kubernetes-kluster:

|Name| Beskrivning |Standard tröskel |
|----|-------------|------------------|
|Genomsnittlig container-CPU% |Beräknar Genomsnittlig CPU-användning per behållare.|När den genomsnittliga CPU-användningen per container är större än 95%.| 
|Genomsnittlig container i arbets minnet% |Beräknar Genomsnittligt arbets minne som används per container.|När genomsnittlig användning av arbets minne per behållare är större än 95%. |
|Genomsnittlig CPU % |Beräknar Genomsnittlig CPU som används per nod. |När Genomsnittlig CPU-användning på noden är större än 80% |
|Genomsnittlig disk användning% |Beräknar genomsnittlig disk användning för en nod.|När disk användningen för en nod är större än 80%. |
|Genomsnittlig användning av beständiga volymer% |Beräknar genomsnittlig användning av PV per POD. |När den genomsnittliga PV-användningen per POD är större än 80%.|
|Genomsnittligt minne för arbets minne% |Beräknar Genomsnittligt arbets minne för en nod. |När Genomsnittligt arbets minne för en nod är större än 80%. |
|Startar om container antal |Beräknar antalet omstarter av behållare. | När omstarter av behållare är större än 0. |
|Antal misslyckade Pod |Beräknar om en pod är i ett felaktigt tillstånd.|När ett antal poddar i felaktigt tillstånd är större än 0. |
|Status för nod notrappsteg |Beräknar om en nod är i notrappstegs tillstånd.|När ett antal noder i notrappstegs tillstånd är större än 0. |
|OOM avlivade behållare |Beräknar antalet OOM-stoppade behållare. |När ett antal stoppade OOM-behållare är större än 0. |
|Poddar redo% |Beräknar Genomsnittligt klart läge för poddar. |När klar status för poddar är mindre än 80%.|
|Antal slutförda jobb |Beräknar antalet jobb som slutförts för mer än sex timmar sedan. |När antalet inaktuella jobb som är äldre än sex timmar är större än 0.|

Det finns gemensamma egenskaper för alla dessa aviserings regler:

* Alla varnings regler är mått baserade.

* Alla varnings regler är inaktiverade som standard.

* Alla varnings regler utvärderas en gång per minut och de ser tillbaka de senaste 5 minuterna data.

* Aviserings regler har inte tilldelats någon åtgärds grupp som standard. Du kan lägga till en [Åtgärds grupp](../alerts/action-groups.md) i aviseringen antingen genom att välja en befintlig åtgärds grupp eller skapa en ny åtgärds grupp när du redigerar varnings regeln.

* Du kan ändra tröskelvärdet för varnings regler genom att redigera dem direkt. Se dock de rikt linjer som finns i varje varnings regel innan du ändrar tröskelvärdet.

Följande aviserings mått har unika beteende egenskaper jämfört med de andra måtten:

* *completedJobsCount* -måttet skickas endast när det finns jobb som är slutförda längre än sex timmar sedan.

* *containerRestartCount* -måttet skickas endast när omstarter av behållare.

* *oomKilledContainerCount* -måttet skickas endast när det finns OOM avlivade behållare.

* *cpuExceededPercentage*-, *MemoryRssExceededPercentage*-och *memoryWorkingSetExceededPercentage* -mått skickas när värdena för processor, minne-RSS och minne som arbetar överskrider den konfigurerade tröskeln (standard tröskelvärdet är 95%). De här tröskelvärdena är exklusiva från tröskelvärdet för aviserings villkor som angetts för motsvarande aviserings regel. Om du vill samla in dessa mått och analysera dem från [Metrics Explorer](../essentials/metrics-getting-started.md)rekommenderar vi att du konfigurerar tröskelvärdet till ett värde som är lägre än tröskelvärdet för aviseringar. Konfigurationen som är relaterad till samlings inställningarna för deras användnings tröskelvärden för container resurser kan åsidosättas i ConfigMaps-filen under avsnittet `[alertable_metrics_configuration_settings.container_resource_utilization_thresholds]` . Mer information om hur du konfigurerar din ConfigMap-konfigurationsfil finns i avsnittet [Konfigurera aviserings bara statistik ConfigMaps](#configure-alertable-metrics-in-configmaps) .

* *pvUsageExceededPercentage* -måttet skickas när den permanenta volym användnings procenten överskrider den konfigurerade tröskeln (standard tröskelvärdet är 60%). Tröskelvärdet är exklusivt för aviserings villkors tröskelvärdet som angetts för motsvarande aviserings regel. Om du vill samla in dessa mått och analysera dem från [Metrics Explorer](../essentials/metrics-getting-started.md)rekommenderar vi att du konfigurerar tröskelvärdet till ett värde som är lägre än tröskelvärdet för aviseringar. Konfigurationen som rör samlings inställningarna för gränser för permanent användning av volymer kan åsidosättas i ConfigMaps-filen under avsnittet `[alertable_metrics_configuration_settings.pv_utilization_thresholds]` . Mer information om hur du konfigurerar din ConfigMap-konfigurationsfil finns i avsnittet [Konfigurera aviserings bara statistik ConfigMaps](#configure-alertable-metrics-in-configmaps) . Insamling av permanenta volym mått med anspråk i *Kube-systemets* namnrymd utesluts som standard. Om du vill aktivera samling i det här namn området använder du avsnittet `[metric_collection_settings.collect_kube_system_pv_metrics]` i ConfigMap-filen. Mer information finns i [Inställningar för mått samling](./container-insights-agent-config.md#metric-collection-settings) .

## <a name="metrics-collected"></a>Insamlade mått

Följande mått är aktiverade och samlas in, om inget annat anges som en del av den här funktionen:

|Mått namn område |Metric |Beskrivning |
|---------|----|------------|
|Insights. container/Nodes |cpuUsageMillicores |CPU-användning i millicores av värden.|
|Insights. container/Nodes |cpuUsagePercentage |Procent andel CPU-användning per nod.|
|Insights. container/Nodes |memoryRssBytes |Minnes användning i byte av minne i byte per värd.|
|Insights. container/Nodes |memoryRssPercentage |Procent andel RSS-användning per värd.|
|Insights. container/Nodes |memoryWorkingSetBytes |Minnes användning i byte per värd.|
|Insights. container/Nodes |memoryWorkingSetPercentage |Minnes aktiv sidmängd i procent per värd.|
|Insights. container/Nodes |nodesCount |Antal noder efter status.|
|Insights. container/Nodes |diskUsedPercentage |Procent andel av disken som används på noden av enheten.|
|Insights. container/poddar |podCount |Antalet poddar per kontrollant, namnrymd, nod och fas.|
|Insights. container/poddar |completedJobsCount |Slutförda jobb antal äldre användar konfigurerbara tröskelvärde (Standardvärdet är sex timmar) av kontrollant, Kubernetes-namnrymd. |
|Insights. container/poddar |restartingContainerCount |Antal omstarter av behållare efter kontrollant, Kubernetes-namnområde.|
|Insights. container/poddar |oomKilledContainerCount |Antal OOMkilled-behållare efter kontrollant, Kubernetes-namnrymd.|
|Insights. container/poddar |podReadyPercentage |Procent andel poddar i klar läge med kontrollant, Kubernetes-namnrymd.|
|Insights. container/containers |cpuExceededPercentage |Procent andel CPU-användning för behållare som överskrider användar konfigurerbart tröskelvärde (Standardvärdet är 95,0) enligt behållar namn, kontrollantens namn, Kubernetes-namnrymd, Pod namn.<br> Ställ  |
|Insights. container/containers |memoryRssExceededPercentage |RSS-procent för behållare som överskrider användar konfigurerbart tröskelvärde (Standardvärdet är 95,0) enligt behållar namn, kontrollantens namn, Kubernetes-namnrymd, Pod namn.|
|Insights. container/containers |memoryWorkingSetExceededPercentage |Procent andel ledigt minne för behållare som överstiger användar konfigurerbart tröskelvärde (Standardvärdet är 95,0) enligt behållar namn, kontrollantens namn, Kubernetes-namnrymd, Pod namn.|
|Insights. container/persistentvolumes |pvUsageExceededPercentage |Procent andel nuvärde för beständiga volymer som överstiger användar konfigurerbart tröskelvärde (standard är 60,0) efter anspråks namn, Kubernetes-namnrymd, volym namn, Pod-namn och nodnamn.

## <a name="enable-alert-rules"></a>Aktivera varnings regler

Följ dessa steg om du vill aktivera mått varningar i Azure Monitor från Azure Portal. Om du vill aktivera med hjälp av en Resource Manager-mall, se [Aktivera med en Resource Manager-mall](#enable-with-a-resource-manager-template).

### <a name="from-the-azure-portal"></a>Från Azure-portalen

Det här avsnittet beskriver hur du aktiverar Azure Monitor för behållare mått avisering (förhands granskning) från Azure Portal.

1. Logga in på [Azure-portalen](https://portal.azure.com/).

2. Åtkomst till funktionen Azure Monitor för behållare mått avisering (förhands granskning) är tillgänglig direkt från ett AKS-kluster genom att välja **insikter** i den vänstra rutan i Azure Portal.

3. I kommando fältet väljer du **rekommenderade aviseringar**.

    ![Alternativ för rekommenderade aviseringar i Azure Monitor för behållare](./media/container-insights-metric-alerts/command-bar-recommended-alerts.png)

4. Egenskaps fönstret för **rekommenderade aviseringar** visas automatiskt till höger på sidan. Som standard är alla varnings regler i listan inaktiverade. När du har valt **Aktivera** skapas varnings regeln och regel namnet uppdateras för att inkludera en länk till aviserings resursen.

    ![Fönstret Egenskaper för rekommenderade aviseringar](./media/container-insights-metric-alerts/recommended-alerts-pane.png)

    När du har valt **Aktivera/inaktivera** växling för att aktivera aviseringen skapas en varnings regel och regel namnet uppdateras för att inkludera en länk till den faktiska aviserings resursen.

    ![Aktivera aviseringsregel](./media/container-insights-metric-alerts/recommended-alerts-pane-enable.png)

5. Aviserings regler är inte kopplade till en [Åtgärds grupp](../alerts/action-groups.md) för att meddela användare om att en avisering har utlösts. Välj **ingen åtgärds grupp tilldelad** och på sidan **Åtgärds grupper** anger du en befintlig eller skapar en åtgärds grupp genom att välja **Lägg till** eller **skapa**.

    ![Välj en åtgärds grupp](./media/container-insights-metric-alerts/select-action-group.png)

### <a name="enable-with-a-resource-manager-template"></a>Aktivera med en Resource Manager-mall

Du kan använda en Azure Resource Manager mall och parameter fil för att skapa de inkluderade mått meddelandena i Azure Monitor.

De grundläggande stegen är följande:

1. Ladda ned en eller flera av de tillgängliga mallarna som beskriver hur du skapar aviseringen från [GitHub](https://github.com/microsoft/Docker-Provider/tree/ci_dev/alerts/recommended_alerts_ARM).

2. Skapa och Använd en [parameter fil](../../azure-resource-manager/templates/parameter-files.md) som JSON för att ange de värden som krävs för att skapa varnings regeln.

3. Distribuera mallen från Azure Portal, PowerShell eller Azure CLI.

#### <a name="deploy-through-azure-portal"></a>Distribuera via Azure Portal

1. Hämta och spara till en lokal mapp, Azure Resource Manager mall och parameter fil för att skapa aviserings regeln med hjälp av följande kommandon:

2. Om du vill distribuera en anpassad mall via portalen väljer du **skapa en resurs** från [Azure Portal](https://portal.azure.com).

3. Sök efter **mall** och välj sedan **malldistribution**.

4. Välj **Skapa**.

5. Du ser flera alternativ för att skapa en mall genom att välja **skapa en egen mall i redigeraren**.

6. På **sidan Redigera mall** väljer du **Läs in fil** och väljer sedan mallfilen.

7. På sidan **Redigera mall** väljer du **Spara**.

8. På sidan **Anpassad distribution** anger du följande och när du har slutfört Välj **köp** för att distribuera mallen och skapa varnings regeln.

    * Resursgrupp
    * Plats
    * Aviseringsnamn
    * Resurs-ID för kluster

#### <a name="deploy-with-azure-powershell-or-cli"></a>Distribuera med Azure PowerShell eller CLI

1. Hämta och spara till en lokal mapp, Azure Resource Manager mall och parameter fil för att skapa aviserings regeln med hjälp av följande kommandon:

2. Du kan skapa mått aviseringen med hjälp av mallen mall och parametrar med hjälp av PowerShell eller Azure CLI.

    Använda Azure PowerShell

    ```powershell
    Connect-AzAccount

    Select-AzSubscription -SubscriptionName <yourSubscriptionName>
    New-AzResourceGroupDeployment -Name CIMetricAlertDeployment -ResourceGroupName ResourceGroupofTargetResource `
    -TemplateFile templateFilename.json -TemplateParameterFile templateParameterFilename.parameters.json
    ```

    Använda Azure CLI

    ```azurecli
    az login

    az deployment group create \
    --name AlertDeployment \
    --resource-group ResourceGroupofTargetResource \
    --template-file templateFileName.json \
    --parameters @templateParameterFilename.parameters.json
    ```

    >[!NOTE]
    >Även om mått aviseringen kan skapas i en annan resurs grupp till mål resursen rekommenderar vi att du använder samma resurs grupp som mål resursen.

## <a name="edit-alert-rules"></a>Redigera aviserings regler

Du kan visa och hantera Azure Monitor för behållares aviserings regler, för att redigera tröskelvärdet eller konfigurera en [Åtgärds grupp](../alerts/action-groups.md) för ditt AKS-kluster. Även om du kan utföra dessa åtgärder från Azure Portal och Azure CLI kan du också göra det direkt från ditt AKS-kluster i Azure Monitor for containers.

1. I kommando fältet väljer du **rekommenderade aviseringar**.

2. Om du vill ändra tröskelvärdet väljer du den aktiverade aviseringen i fönstret **rekommenderade aviseringar** . I **Redigera regel** väljer du de **aviserings villkor** som du vill redigera.

    * Om du vill ändra tröskelvärdet för varnings regeln väljer du **villkoret**.
    * Om du vill ange en befintlig eller skapa en åtgärds grupp väljer du **Lägg till** eller **skapa** under **Åtgärds grupp**

Om du vill visa aviseringar som skapats för de aktiverade reglerna väljer du **Visa i aviseringar** i fönstret **rekommenderade aviseringar** . Du omdirigeras till aviserings menyn för AKS-klustret där du kan se alla aviseringar som för närvarande har skapats för klustret.

## <a name="configure-alertable-metrics-in-configmaps"></a>Konfigurera aviserings bara mått i ConfigMaps

Utför följande steg för att konfigurera konfigurations filen för ConfigMap för att åsidosätta standard tröskelvärdena för användning. De här stegen gäller endast för följande aviserings bara mått:

* *cpuExceededPercentage*
* *memoryRssExceededPercentage*
* *memoryWorkingSetExceededPercentage*
* *pvUsageExceededPercentage*

1. Redigera ConfigMap YAML-filen under avsnittet `[alertable_metrics_configuration_settings.container_resource_utilization_thresholds]` eller `[alertable_metrics_configuration_settings.pv_utilization_thresholds]` .

   - Om du vill ändra tröskelvärdet för *cpuExceededPercentage* till 90% och börja samla in det här måttet när tröskelvärdet är uppfyllt och överskridet, konfigurerar du ConfigMap-filen med hjälp av följande exempel:

     ```
     [alertable_metrics_configuration_settings.container_resource_utilization_thresholds]
         # Threshold for container cpu, metric will be sent only when cpu utilization exceeds or becomes equal to the following percentage
         container_cpu_threshold_percentage = 90.0
         # Threshold for container memoryRss, metric will be sent only when memory rss exceeds or becomes equal to the following percentage
         container_memory_rss_threshold_percentage = 95.0
         # Threshold for container memoryWorkingSet, metric will be sent only when memory working set exceeds or becomes equal to the following percentage
         container_memory_working_set_threshold_percentage = 95.0
     ```

   - Om du vill ändra tröskelvärdet för *pvUsageExceededPercentage* till 80% och börja samla in det här måttet när tröskelvärdet är uppfyllt och överskridet, konfigurerar du ConfigMap-filen med hjälp av följande exempel:

     ```
     [alertable_metrics_configuration_settings.pv_utilization_thresholds]
         # Threshold for persistent volume usage bytes, metric will be sent only when persistent volume utilization exceeds or becomes equal to the following percentage
         pv_usage_threshold_percentage = 80.0
     ```

2. Kör följande kubectl-kommando: `kubectl apply -f <configmap_yaml_file.yaml>` .

    Exempel: `kubectl apply -f container-azm-ms-agentconfig.yaml`.

Konfigurations ändringen kan ta några minuter innan den börjar gälla, och alla omsagent-poddar i klustret kommer att startas om. Omstarten är en rullande omstart för alla omsagent poddar; de startar inte om på samma tid. När omstarterna är klara visas ett meddelande som liknar följande exempel och innehåller resultatet: `configmap "container-azm-ms-agentconfig" created` .

## <a name="next-steps"></a>Nästa steg

- Visa [exempel på logg frågor](container-insights-log-search.md#search-logs-to-analyze-data) för att se fördefinierade frågor och exempel för att utvärdera eller anpassa för aviseringar, visualisering eller analys av klustren.

- Mer information om Azure Monitor och hur du övervakar andra aspekter av ditt Kubernetes-kluster finns i [Visa Kubernetes-kluster prestanda](container-insights-analyze.md).