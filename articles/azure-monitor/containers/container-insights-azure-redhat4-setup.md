---
title: Konfigurera Azure Red Hat OpenShift v4.x med Container Insights | Microsoft Docs
description: I den här artikeln beskrivs hur du konfigurerar övervakning för ett Kubernetes-kluster med Azure Monitor som finns på Azure Red Hat OpenShift version 4 eller senare.
ms.topic: conceptual
ms.date: 03/05/2021
ms.openlocfilehash: 11c702d1f46725a12e90a01dc1b38467344a1123
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107784649"
---
# <a name="configure-azure-red-hat-openshift-v4x-with-container-insights"></a>Konfigurera Azure Red Hat OpenShift v4.x med Container Insights

Containerinsikter ger en omfattande övervakningsupplevelse Azure Kubernetes Service AKS- och AKS-motorkluster. Den här artikeln beskriver hur du uppnår en liknande övervakningsupplevelse genom att aktivera övervakning för Kubernetes-kluster som finns [på Azure Red Hat OpenShift](../../openshift/intro-openshift.md) version 4.x.

>[!NOTE]
>Stöd för Azure Red Hat OpenShift är en funktion i offentlig förhandsversion just nu.
>

Du kan aktivera Container Insights för en eller flera befintliga distributioner av Azure Red Hat OpenShift v4.x med hjälp av de metoder som stöds som beskrivs i den här artikeln.

För ett befintligt kluster kör du det här [Bash-skriptet i Azure CLI.](/cli/azure/openshift#az_openshift_create&preserve-view=true)

## <a name="supported-and-unsupported-features"></a>Funktioner som stöds och inte stöds

Containerinsikter stöder övervakning Azure Red Hat OpenShift v4.x enligt beskrivningen i [Översikt över Container Insights,](container-insights-overview.md)förutom följande funktioner:

- Livedata (förhandsversion)
- [Samla in mått](container-insights-update-metrics.md) från klusternoder och poddar och lagra dem i Azure Monitor för mått

## <a name="prerequisites"></a>Förutsättningar

- Azure CLI version 2.0.72 eller senare  

- [Cli-verktyget Helm 3](https://helm.sh/docs/intro/install/)

- Senaste versionen av [OpenShift CLI](https://docs.openshift.com/container-platform/4.7/cli_reference/openshift_cli/getting-started-cli.html)

- [Bash version 4](https://www.gnu.org/software/bash/)

- [Kommandoradsverktyget Kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl/)

- En [Log Analytics-arbetsyta](../logs/design-logs-deployment.md).

    Container insights stöder en Log Analytics-arbetsyta i de regioner som anges i [Azure-produkter efter region.](https://azure.microsoft.com/global-infrastructure/services/?regions=all&products=monitor) Om du vill skapa en egen arbetsyta kan [den skapas via Azure Resource Manager](../logs/resource-manager-workspace.md), via [PowerShell](../logs/powershell-sample-create-workspace.md?toc=%2fpowershell%2fmodule%2ftoc.json)eller i [Azure Portal](../logs/quick-create-workspace.md).

- Om du vill aktivera och komma åt funktionerna i Container  Insights måste du minst ha en Azure-deltagarroll i Azure-prenumerationen och en [*Log Analytics-deltagarroll*](../logs/manage-access.md#manage-access-using-azure-permissions) i Log Analytics-arbetsytan, konfigurerad med Container Insights.

- Om du vill visa övervakningsdata måste du ha [*Log Analytics-läsarrollen*](../logs/manage-access.md#manage-access-using-azure-permissions) på Log Analytics-arbetsytan, konfigurerad med Container Insights.

## <a name="enable-monitoring-for-an-existing-cluster"></a>Aktivera övervakning för ett befintligt kluster

Om du vill aktivera övervakning Azure Red Hat OpenShift ett kluster av version 4 eller senare som distribueras i Azure med hjälp av det angivna Bash-skriptet gör du följande:

1. Logga in på Azure genom att köra följande kommando:

    ```azurecli
    az login
    ```

1. Ladda ned och spara skriptet som konfigurerar klustret med övervaknings-tillägget i en lokal mapp genom att köra följande kommando:

    `curl -o enable-monitoring.sh -L https://aka.ms/enable-monitoring-bash-script`

1. Anslut till ett ARO v4-kluster med hjälp av anvisningarna i [Självstudie: Ansluta till ett Azure Red Hat OpenShift 4-kluster.](../../openshift/tutorial-connect-cluster.md)


### <a name="integrate-with-an-existing-workspace"></a>Integrera med en befintlig arbetsyta

I det här avsnittet aktiverar du övervakning av klustret med hjälp av Bash-skriptet som du laddade ned tidigare. Om du vill integrera med en befintlig Log Analytics-arbetsyta börjar du med att identifiera det fullständiga resurs-ID:t för log Analytics-arbetsytan som krävs för parametern och kör sedan kommandot för att aktivera övervaknings-tillägget mot den angivna `logAnalyticsWorkspaceResourceId` arbetsytan.

Om du inte har någon arbetsyta att ange [](#integrate-with-the-default-workspace) kan du gå vidare till avsnittet Integrera med standardarbetsytan och låta skriptet skapa en ny arbetsyta åt dig.

1. Visa en lista över alla prenumerationer som du har åtkomst till genom att köra följande kommando:

    ```azurecli
    az account list --all -o table
    ```

    Utdata ser ut så här:

    ```azurecli
    Name                                  CloudName    SubscriptionId                        State    IsDefault
    ------------------------------------  -----------  ------------------------------------  -------  -----------
    Microsoft Azure                       AzureCloud   0fb60ef2-03cc-4290-b595-e71108e8f4ce  Enabled  True
    ```

1. Kopiera värdet för **SubscriptionId**.

1. Växla till den prenumeration som är värd för Log Analytics-arbetsytan genom att köra följande kommando:

    ```azurecli
    az account set -s <subscriptionId of the workspace>
    ```

1. Visa listan över arbetsytor i dina prenumerationer i JSON-standardformat genom att köra följande kommando:

    ```
    az resource list --resource-type Microsoft.OperationalInsights/workspaces -o json
    ```

1. Leta reda på arbetsytans namn i utdata och kopiera sedan det fullständiga resurs-ID:t för Log Analytics-arbetsytan under fältet **ID**.

1. Kör följande kommando för att aktivera övervakning. Ersätt värdena för `azureAroV4ClusterResourceId` parametrarna `logAnalyticsWorkspaceResourceId` och .

    ```bash
    export azureAroV4ClusterResourceId="/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.RedHatOpenShift/OpenShiftClusters/<clusterName>"
    export logAnalyticsWorkspaceResourceId="/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/microsoft.operationalinsights/workspaces/<workspaceName>" 
    ```

    Här är kommandot som du måste köra när du har fyllt i de tre variablerna med exportkommandon:

    `bash enable-monitoring.sh --resource-id $azureAroV4ClusterResourceId --workspace-id $logAnalyticsWorkspaceResourceId`

När du har aktiverat övervakning kan det ta ungefär 15 minuter innan du kan visa hälsomått för klustret.

### <a name="integrate-with-the-default-workspace"></a>Integrera med standardarbetsytan

I det här avsnittet aktiverar du övervakning för ditt Azure Red Hat OpenShift v4.x-kluster med hjälp av Bash-skriptet som du laddade ned.

I det här exemplet behöver du inte skapa eller ange en befintlig arbetsyta i förväg. Det här kommandot förenklar processen åt dig genom att skapa en standardarbetsyta i standardresursgruppen för klusterprenumerationen, om det inte redan finns någon i regionen.

Standardarbetsytan som skapas har formatet *DefaultWorkspace- \<GUID> - \<Region>*.  

Ersätt värdet för `azureAroV4ClusterResourceId` parametern .

```bash
export azureAroV4ClusterResourceId="/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.RedHatOpenShift/OpenShiftClusters/<clusterName>"
```

Exempel:

"bash enable-monitoring.sh --resource-id $azureAroV 4ClusterResourceId 

När du har aktiverat övervakning kan det ta ungefär 15 minuter innan du kan visa hälsomått för klustret.

### <a name="enable-monitoring-from-the-azure-portal"></a>Aktivera övervakning från Azure Portal

Vyn med flera kluster i Container Insights visar dina Azure Red Hat OpenShift som inte har övervakning aktiverat på fliken **Oövervakade** kluster. Alternativet  Aktivera bredvid klustret initierar inte registrering av övervakning från portalen. Du omdirigeras till den här artikeln för att aktivera övervakning manuellt genom att följa stegen som beskrevs tidigare i den här artikeln.

1. Logga in på [Azure-portalen](https://portal.azure.com).

1. I den vänstra rutan eller på startsidan väljer du **Azure Monitor**.

1. I avsnittet **Insikter** väljer du **Containrar.**

1. På sidan **Övervaka – containrar** väljer **du Oövervakade kluster.**

1. I listan över icke-övervakade kluster väljer du klustret och sedan **Aktivera**.

    Du kan identifiera resultaten i listan genom att leta efter **ARO-värdet** i **kolumnen Klustertyp.** När du har **valt** Aktivera omdirigeras du till den här artikeln.

## <a name="next-steps"></a>Nästa steg

- Nu när du har aktiverat övervakning för att samla in hälsotillstånd och resursanvändning för redHat OpenShift-klustret version 4.x och de arbetsbelastningar som körs på dem kan du lära dig hur du använder [Container](container-insights-analyze.md) Insights.

- Som standard samlar containeragenten in *stdout-* och *stderr-containerloggarna* för alla containrar som körs i alla namnområden utom kube-system. Om du vill konfigurera en containerloggsamling som är specifik för ett visst namnområde eller ett visst namnområde kan du läsa mer i [Container Insights-agentkonfigurationen](container-insights-agent-config.md) för att konfigurera de inställningar för datainsamling som du vill använda för *konfigurationsfilen ConfigMap.*

- Om du vill ta fram och analysera Prometheus-mått från klustret kan du läsa [Configure Prometheus metrics scraping (Konfigurera Prometheus-måttskrasning).](container-insights-prometheus-integration.md)

- Information om hur du stoppar övervakningen av klustret med hjälp av Container Insights finns i Så här stoppar du [övervakningen Azure Red Hat OpenShift klustret.](./container-insights-optout-openshift-v3.md)
