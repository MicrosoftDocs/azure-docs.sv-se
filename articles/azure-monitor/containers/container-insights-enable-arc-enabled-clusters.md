---
title: Övervaka Azure Arc-aktiverade Kubernetes-kluster
ms.date: 04/05/2021
ms.topic: article
author: shashankbarsin
ms.author: shasb
description: Samla in mått och loggar för Azure Arc-aktiverade Kubernetes-kluster med hjälp av Azure Monitor
ms.openlocfilehash: 0a983f6d7032310d02d35e713482de942bfbfd70
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2021
ms.locfileid: "106443858"
---
# <a name="azure-monitor-container-insights-for-azure-arc-enabled-kubernetes-clusters"></a>Azure Monitor behållar insikter för Azure Arc-aktiverade Kubernetes-kluster

[Azure Monitor container Insights](container-insights-overview.md) ger en omfattande övervaknings upplevelse för Azure Arc-aktiverade Kubernetes-kluster.

[!INCLUDE [preview features note](../../azure-arc/kubernetes/includes/preview/preview-callout.md)]

## <a name="supported-configurations"></a>Konfigurationer som stöds

- Azure Monitor container Insights stöder övervakning av Azure Arc-aktiverade Kubernetes (för hands version) enligt beskrivningen i [översikts](container-insights-overview.md) artikeln, förutom funktionen Live data (för hands version). Användare behöver inte heller ha [ägar](../../role-based-access-control/built-in-roles.md#owner) behörighet för att [Aktivera mått](container-insights-update-metrics.md)
- `Docker`, `Moby` och CRI-kompatibla behållar körningar, till exempel `CRI-O` och `containerd` .
- Utgående proxy utan autentisering och utgående proxy med grundläggande autentisering stöds. Utgående proxy som förväntar sig att betrodda certifikat stöds inte för närvarande.

## <a name="prerequisites"></a>Förutsättningar

- Du har uppfyllt de krav som anges i [dokumentationen för allmänna kluster tillägg](../../azure-arc/kubernetes/extensions.md#prerequisites).
- En Log Analytics arbets yta: Azure Monitor container Insights har stöd för en Log Analytics arbets yta i de regioner som visas på [sidan för Azure-produkter efter region](https://azure.microsoft.com/global-infrastructure/services/?regions=all&products=monitor). Du kan skapa en egen arbets yta via [Azure Resource Manager](../logs/resource-manager-workspace.md), [PowerShell](../logs/powershell-sample-create-workspace.md)eller [Azure Portal](../logs/quick-create-workspace.md).
- Du måste ha [deltagar](../../role-based-access-control/built-in-roles.md#contributor) roll tilldelning på den Azure-prenumeration som innehåller Azure Arc-aktiverade Kubernetes-resursen. Om Log Analytics arbets ytan finns i en annan prenumeration krävs [Log Analytics deltagar](../logs/manage-access.md#manage-access-using-azure-permissions) roll tilldelningen på Log Analytics-arbetsytan.
- Om du vill visa övervaknings data måste du ha [Log Analytics läsar](../logs/manage-access.md#manage-access-using-azure-permissions) roll tilldelning på arbets ytan Log Analytics.
- Följande slut punkter måste aktive ras för utgående åtkomst utöver de som anges under [ansluta ett Kubernetes-kluster till Azure-bågen](../../azure-arc/kubernetes/quickstart-connect-cluster.md#meet-network-requirements).

    | Slutpunkt | Port |
    |----------|------|
    | `*.ods.opinsights.azure.com` | 443 |
    | `*.oms.opinsights.azure.com` | 443 |
    | `dc.services.visualstudio.com` | 443 |
    | `*.monitoring.azure.com` | 443 |
    | `login.microsoftonline.com` | 443 |

    Om din ARC-aktiverade Kubernetes-resurs är i Azure amerikanska myndighets miljö måste följande slut punkter aktive ras för utgående åtkomst:

    | Slutpunkt | Port |
    |----------|------|
    | `*.ods.opinsights.azure.us` | 443 |
    | `*.oms.opinsights.azure.us` | 443 |
    | `dc.services.visualstudio.com` | 443 |
    

- Om du tidigare har distribuerat Azure Monitor behållar insikter i det här klustret med hjälp av skript utan kluster tillägg, följer du instruktionerna som visas [här](container-insights-optout-hybrid.md) för att ta bort det här Helm-diagrammet. Sedan kan du fortsätta att skapa en kluster tilläggs instans för Azure Monitor behållar insikter.

    >[!NOTE]
    > Den skriptbaserade versionen av Distribuera Azure Monitor behållar insikter (för hands version) ersätts av [kluster tilläggs](../../azure-arc/kubernetes/extensions.md) formuläret för distribution. Azure Monitor som distribuerats tidigare via skript stöds endast till 2021 juni och vi rekommenderar därför att migrera till kluster tilläggs formuläret för distribution tidigast.

### <a name="identify-workspace-resource-id"></a>Identifiera arbets ytans resurs-ID

Kör följande kommandon för att hitta den fullständiga Azure Resource Manager identifieraren för Log Analytics-arbetsytan. 

1. Lista alla prenumerationer som du har åtkomst till med hjälp av följande kommando:

    ```azurecli
    az account list --all -o table
    ```

2. Växla till den prenumeration som är värd för Log Analytics arbets ytan med hjälp av följande kommando:

    ```azurecli
    az account set -s <subscriptionId of the workspace>
    ```

3. I följande exempel visas listan över arbets ytor i dina prenumerationer i standardformatet JSON.

    ```
    az resource list --resource-type Microsoft.OperationalInsights/workspaces -o json
    ```

    I utdata söker du efter namnet på arbets ytans intresse. Det `id` fält som representerar Azure Resource Manager identifieraren för den Log Analytics arbets ytan.

    >[!TIP]
    > Detta `id` kan också finnas på bladet *översikt* i Log Analytics arbets ytan via Azure Portal.

## <a name="create-extension-instance-using-azure-cli"></a>Skapa tilläggs instans med hjälp av Azure CLI

### <a name="option-1---with-default-values"></a>Alternativ 1 – med standardvärden

Det här alternativet använder följande standardvärden:

- Skapar eller använder en befintlig Log Analytics-arbetsyta som motsvarar klustrets region
- Automatisk uppgradering har Aktiver ATS för kluster tillägget Azure Monitor

```console
az k8s-extension create --name azuremonitor-containers --cluster-name <cluster-name> --resource-group <resource-group> --cluster-type connectedClusters --extension-type Microsoft.AzureMonitor.Containers
```

### <a name="option-2---with-existing-azure-log-analytics-workspace"></a>Alternativ 2 – med befintlig Azure Log Analytics-arbetsyta

Du kan använda en befintlig Azure Log Analytics-arbetsyta i vilken prenumeration som helst som du har *deltagare* eller en mer tillåtande roll tilldelning.

```console
az k8s-extension create --name azuremonitor-containers --cluster-name <cluster-name> --resource-group <resource-group> --cluster-type connectedClusters --extension-type Microsoft.AzureMonitor.Containers --configuration-settings logAnalyticsWorkspaceResourceID=<armResourceIdOfExistingWorkspace>
```

### <a name="option-3---with-advanced-configuration"></a>Alternativ 3 – med Avancerad konfiguration

Om du vill justera standardvärdena för resurs begär Anden och begränsningar kan du använda inställningarna för Avancerad konfiguration:

```console
az k8s-extension create --name azuremonitor-containers --cluster-name <cluster-name> --resource-group <resource-group> --cluster-type connectedClusters --extension-type Microsoft.AzureMonitor.Containers --configuration-settings  omsagent.resources.daemonset.limits.cpu=150m omsagent.resources.daemonset.limits.memory=600Mi omsagent.resources.deployment.limits.cpu=1 omsagent.resources.deployment.limits.memory=750Mi
```

Checka ut [avsnittet resurs begär Anden och begränsningar i Helm-diagrammet](https://github.com/helm/charts/blob/master/incubator/azuremonitor-containers/values.yaml) för de tillgängliga konfigurations inställningarna.

### <a name="option-4---on-azure-stack-edge"></a>Alternativ 4 – på Azure Stack kant

Om det aktiverade Kubernetes-klustret för Azure-bågen är på Azure Stack Edge måste en anpassad monterings Sök väg `/home/data/docker` användas.

```console
az k8s-extension create --name azuremonitor-containers --cluster-name <cluster-name> --resource-group <resource-group> --cluster-type connectedClusters --extension-type Microsoft.AzureMonitor.Containers --configuration-settings omsagent.logsettings.custommountpath=/home/data/docker
```

>[!NOTE]
> Om du uttryckligen anger vilken version av tillägget som ska installeras i kommandot CREATE måste du kontrol lera att den angivna versionen är >= 2.8.2.

## <a name="create-extension-instance-using-azure-portal"></a>Skapa tilläggs instans med Azure Portal

>[!IMPORTANT]
>  Om du distribuerar Azure Monitor på ett Kubernetes-kluster som körs ovanpå Azure Stack Edge måste du använda Azure CLI-alternativet i stället för alternativet Azure Portal eftersom en anpassad monterings Sök väg måste anges för dessa kluster.    

### <a name="onboarding-from-the-azure-arc-enabled-kubernetes-resource-blade"></a>Onboarding från resurs bladet Azure Arc-aktiverade Kubernetes

1. I Azure Portal väljer du det Arc-aktiverade Kubernetes-kluster som du vill övervaka.

2. Välj objektet Insights (förhands granskning) under avsnittet övervakning på resurs bladet.

3. På sidan onboarding väljer du knappen Konfigurera Azure Monitor

4. Nu kan du välja [Log Analytics arbets ytan](../logs/quick-create-workspace.md) för att skicka mått och logga data till.

5. Klicka på Konfigurera om du vill distribuera kluster tillägget Azure Monitor container Insights.

### <a name="onboarding-from-azure-monitor-blade"></a>Onboarding från Azure Monitor blad

1. I Azure Portal navigerar du till bladet övervaka och väljer alternativet behållare under menyn insikter.

2. Välj fliken "oövervakade kluster" om du vill visa de Azure Arc-aktiverade Kubernetes-kluster som du kan aktivera övervakning för.

3. Klicka på länken "Aktivera" bredvid det kluster som du vill aktivera övervakning för.

4. Välj arbets ytan Log Analytics och välj knappen Konfigurera för att fortsätta.

## <a name="create-extension-instance-using-azure-resource-manager"></a>Skapa tilläggs instans med Azure Resource Manager

1. Ladda ned Azure Resource Manager mall och parameter:

    ```console
    curl -L https://aka.ms/arc-k8s-azmon-extension-arm-template -o arc-k8s-azmon-extension-arm-template.json
    curl -L https://aka.ms/arc-k8s-azmon-extension-arm-template-params -o  arc-k8s-azmon-extension-arm-template-params.json
    ```

2. Uppdatera parameter värden i arc-k8s-azmon-extension-arm-template-params.jsi filen. För Azures offentliga moln `opinsights.azure.com` måste användas som värde för workspaceDomain.

3. Distribuera mallen för att skapa Azure Monitor container Insights-tillägg 

    ```console
    az login
    az account set --subscription "Subscription Name"
    az deployment group create --resource-group <resource-group> --template-file ./arc-k8s-azmon-extension-arm-template.json --parameters @./arc-k8s-azmon-extension-arm-template-params.json
    ```

## <a name="delete-extension-instance"></a>Ta bort tilläggs instans

Följande kommando tar bara bort tilläggs instansen, men tar inte bort Log Analytics arbets ytan. Data i Log Analytics resursen lämnas intakta.

```bash
az k8s-extension delete --name azuremonitor-containers --cluster-type connectedClusters --cluster-name <cluster-name> --resource-group <resource-group>
```

## <a name="disconnected-cluster"></a>Frånkopplat kluster
Om klustret är frånkopplat från Azure i > 48 timmar kommer Azure Resource Graph inte att ha information om klustret. På grund av detta kan insikts bladet Visa felaktig information om klustrets tillstånd.

## <a name="next-steps"></a>Nästa steg

- När övervakning har Aktiver ATS för att samla in hälso-och resursutnyttjande för ditt Arc-aktiverade Kubernetes-kluster och arbets belastningar som körs på dem, lär [du dig hur du använder](container-insights-analyze.md) behållar insikter.

- Som standard samlar den behållareade agenten STDOUT/stderr-behållar loggarna för alla behållare som körs i alla namn områden utom Kube-system. Om du vill konfigurera samling av behållar logg samlingar som är specifika för en viss namnrymd eller namnrymder granskar du [konfiguration av container Insights](container-insights-agent-config.md) för att konfigurera önskade inställningar för data insamling till ConfigMap-konfigurations filen.

- Om du vill ta bort och analysera Prometheus-mått från klustret kan du läsa [Konfigurera Prometheus mått-kassationing](container-insights-prometheus-integration.md)
