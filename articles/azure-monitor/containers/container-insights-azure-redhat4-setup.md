---
title: Konfigurera Azure Red Hat OpenShift v4. x med container Insights | Microsoft Docs
description: Den här artikeln beskriver hur du konfigurerar övervakning för ett Kubernetes-kluster med Azure Monitor som finns i Azure Red Hat OpenShift version 4 eller senare.
ms.topic: conceptual
ms.date: 03/05/2021
ms.openlocfilehash: 02cb794463b965ebafef0b6861477dbf69227511
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "102506420"
---
# <a name="configure-azure-red-hat-openshift-v4x-with-container-insights"></a>Konfigurera Azure Red Hat OpenShift v4. x med container Insights

Behållar insikter ger en omfattande övervaknings upplevelse för Azure Kubernetes service (AKS) och AKS-motor kluster. Den här artikeln beskriver hur du uppnår en liknande övervaknings upplevelse genom att aktivera övervakning av Kubernetes-kluster som finns i [Azure Red Hat OpenShift](../../openshift/intro-openshift.md) version 4. x.

>[!NOTE]
>Stöd för Azure Red Hat OpenShift är en funktion i offentlig för hands version för tillfället.
>

Du kan aktivera behållar insikter för en eller flera befintliga distributioner av Azure Red Hat OpenShift v4. x med de metoder som beskrivs i den här artikeln.

För ett befintligt kluster kör du det här [bash-skriptet i Azure CLI](/cli/azure/openshift#az-openshift-create&preserve-view=true).

## <a name="supported-and-unsupported-features"></a>Funktioner som stöds och som inte stöds

Container Insights stöder övervakning av Azure Red Hat OpenShift v4. x enligt beskrivningen i [Översikt över behållar insikter](container-insights-overview.md), förutom följande funktioner:

- Real tids data (förhands granskning)
- [Samla in mått](container-insights-update-metrics.md) från klusternoder och poddar och lagra dem i Azure Monitor Metrics-databasen

## <a name="prerequisites"></a>Förutsättningar

- Azure CLI-version 2.0.72 eller senare  

- [Helm 3](https://helm.sh/docs/intro/install/) CLI-verktyget

- [Bash-version 4](https://www.gnu.org/software/bash/)

- Kommando rads verktyget [Kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl/)

- En [Log Analytics-arbetsyta](../logs/design-logs-deployment.md).

    Behållar insikter stöder en Log Analytics arbets yta i de regioner som anges i Azure- [produkter efter region](https://azure.microsoft.com/global-infrastructure/services/?regions=all&products=monitor). Om du vill skapa en egen arbets yta kan den skapas via [Azure Resource Manager](../logs/resource-manager-workspace.md), via [PowerShell](../logs/powershell-sample-create-workspace.md?toc=%2fpowershell%2fmodule%2ftoc.json)eller i [Azure Portal](../logs/quick-create-workspace.md).

- Om du vill aktivera och få åtkomst till funktionerna i behållar insikter måste du ha minst en Azure *Contributor* -roll i Azure-prenumerationen och en [*Log Analytics deltagar*](../logs/manage-access.md#manage-access-using-azure-permissions) roll på arbets ytan Log Analytics som kon figurer ATS med behållar insikter.

- Om du vill visa övervaknings data måste du ha [*Log Analytics läsar*](../logs/manage-access.md#manage-access-using-azure-permissions) roll på arbets ytan Log Analytics som kon figurer ATS med behållar insikter.

## <a name="enable-monitoring-for-an-existing-cluster"></a>Aktivera övervakning för ett befintligt kluster

Gör så här för att aktivera övervakning av ett Azure Red Hat OpenShift-kluster med version 4 eller senare som har distribuerats i Azure med hjälp av det tillhandahållna bash-skriptet:

1. Logga in på Azure genom att köra följande kommando:

    ```azurecli
    az login
    ```

1. Ladda ned och spara till en lokal mapp det skript som konfigurerar klustret med övervaknings tillägget genom att köra följande kommando:

    `curl -o enable-monitoring.sh -L https://aka.ms/enable-monitoring-bash-script`

1. Anslut till ARO v4-kluster med hjälp av anvisningarna i [Självstudier: Anslut till ett Azure Red Hat OpenShift 4-kluster](../../openshift/tutorial-connect-cluster.md).


### <a name="integrate-with-an-existing-workspace"></a>Integrera med en befintlig arbets yta

I det här avsnittet aktiverar du övervakning av klustret med hjälp av bash-skriptet som du laddade ned tidigare. Om du vill integrera med en befintlig Log Analytics arbets yta börjar du med att identifiera det fullständiga resurs-ID: t för din Log Analytics arbets yta som krävs för `logAnalyticsWorkspaceResourceId` parametern och kör sedan kommandot för att aktivera övervaknings tillägget mot den angivna arbets ytan.

Om du inte har en arbets yta att ange kan du gå till avsnittet [integrera med standard arbets ytan](#integrate-with-the-default-workspace) och låta skriptet skapa en ny arbets yta åt dig.

1. Lista alla prenumerationer som du har åtkomst till genom att köra följande kommando:

    ```azurecli
    az account list --all -o table
    ```

    Utdata kommer att se ut så här:

    ```azurecli
    Name                                  CloudName    SubscriptionId                        State    IsDefault
    ------------------------------------  -----------  ------------------------------------  -------  -----------
    Microsoft Azure                       AzureCloud   0fb60ef2-03cc-4290-b595-e71108e8f4ce  Enabled  True
    ```

1. Kopiera värdet för **SubscriptionId**.

1. Växla till den prenumeration som är värd för Log Analytics arbets ytan genom att köra följande kommando:

    ```azurecli
    az account set -s <subscriptionId of the workspace>
    ```

1. Visa listan över arbets ytor i dina prenumerationer i standardvärdet JSON-format genom att köra följande kommando:

    ```
    az resource list --resource-type Microsoft.OperationalInsights/workspaces -o json
    ```

1. I utdata letar du reda på arbets ytans namn och kopierar sedan det fullständiga resurs-ID: t för den Log Analytics arbets ytan under fält **-ID: t**.

1. Kör följande kommando för att aktivera övervakning. Ersätt värdena för `azureAroV4ClusterResourceId` `logAnalyticsWorkspaceResourceId` parametrarna och.

    ```bash
    export azureAroV4ClusterResourceId="/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.RedHatOpenShift/OpenShiftClusters/<clusterName>"
    export logAnalyticsWorkspaceResourceId="/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/microsoft.operationalinsights/workspaces/<workspaceName>" 
    ```

    Här är kommandot som du måste köra när du har fyllt i tre variabler med export kommandon:

    `bash enable-monitoring.sh --resource-id $azureAroV4ClusterResourceId --workspace-id $logAnalyticsWorkspaceResourceId`

När du har aktiverat övervakning kan det ta ungefär 15 minuter innan du kan visa hälso måtten för klustret.

### <a name="integrate-with-the-default-workspace"></a>Integrera med standard arbets ytan

I det här avsnittet aktiverar du övervakning för ditt Azure Red Hat OpenShift v4. x-kluster med hjälp av det bash-skript som du laddade ned.

I det här exemplet är du inte tvungen att skapa eller ange en befintlig arbets yta. Det här kommandot fören klar processen åt dig genom att skapa en standard arbets yta i kluster prenumerationens standard resurs grupp, om det inte redan finns en sådan i regionen.

Standard arbets ytan som skapas har formatet *DefaultWorkspace- \<GUID> - \<Region>*.  

Ersätt värdet för `azureAroV4ClusterResourceId` parametern.

```bash
export azureAroV4ClusterResourceId="/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.RedHatOpenShift/OpenShiftClusters/<clusterName>"
```

Exempel:

"bash enable-monitoring.sh--Resource-ID $azureAroV 4ClusterResourceId 

När du har aktiverat övervakning kan det ta ungefär 15 minuter innan du kan visa hälso mått för klustret.

### <a name="enable-monitoring-from-the-azure-portal"></a>Aktivera övervakning från Azure Portal

Vyn flera kluster i behållar insikter visar dina Azure Red Hat OpenShift-kluster som inte har övervakning aktiverat under fliken **oövervakade kluster** . Alternativet **Aktivera** bredvid klustret initierar inte registrering av övervakning från portalen. Du omdirigeras till den här artikeln för att aktivera övervakning manuellt genom att följa anvisningarna som beskrivs tidigare i den här artikeln.

1. Logga in på [Azure-portalen](https://portal.azure.com).

1. I den vänstra rutan eller på Start sidan väljer du **Azure Monitor**.

1. I avsnittet **insikter** väljer du **behållare**.

1. På sidan **övervaka – behållare** väljer du **oövervakade kluster**.

1. I listan över icke-övervakade kluster väljer du klustret och väljer sedan **Aktivera**.

    Du kan identifiera resultaten i listan genom att leta efter värdet **Aro** i kolumnen **kluster typ** . När du har valt **Aktivera** omdirigeras du till den här artikeln.

## <a name="next-steps"></a>Nästa steg

- Nu när du har aktiverat övervakning för att samla in hälso-och resursutnyttjande för ditt RedHat OpenShift version 4. x-kluster och de arbets belastningar som körs på dem, lär [du dig hur du använder](container-insights-analyze.md) behållar insikter.

- Som standard samlar den behållareade agenten *STDOUT* -och *stderr* -behållar loggarna för alla behållare som körs i alla namn områden utom Kube-system. Om du vill konfigurera en behållar logg samling som är specifik för en viss namnrymd eller namnrymd, granskar du [agent konfigurationen för container Insights](container-insights-agent-config.md) för att konfigurera de data insamlings inställningar som du vill använda för din *ConfigMap* konfigurations fil.

- Om du vill ta bort och analysera Prometheus-mått från klustret kan du läsa [Konfigurera Prometheus mått](container-insights-prometheus-integration.md).

- Information om hur du stoppar övervakningen av klustret med hjälp av behållar insikter finns i [så här slutar du övervaka ditt Azure Red Hat OpenShift-kluster](./container-insights-optout-openshift-v3.md).
