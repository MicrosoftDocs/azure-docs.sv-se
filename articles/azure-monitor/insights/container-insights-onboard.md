---
title: Aktivera Azure Monitor för behållare | Microsoft Docs
description: I den här artikeln beskrivs hur du aktiverar och konfigurerar Azure Monitor för behållare så att du kan förstå hur din behållare presterar och vilka prestandarelaterade problem som har identifierats.
ms.topic: conceptual
ms.date: 06/30/2020
ms.openlocfilehash: 56f60b58cff351aa37e98cdba933c929aaaedab6
ms.sourcegitcommit: 8245325f9170371e08bbc66da7a6c292bbbd94cc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/07/2021
ms.locfileid: "99806027"
---
# <a name="enable-azure-monitor-for-containers"></a>Aktivera Azure Monitor för behållare

Den här artikeln innehåller en översikt över de alternativ som är tillgängliga för att konfigurera Azure Monitor för behållare för att övervaka prestanda för arbets belastningar som distribueras till Kubernetes-miljöer och som finns på:

- [Azure Kubernetes Service (AKS)](../../aks/index.yml)  
- [Azure Red Hat OpenShift](../../openshift/intro-openshift.md) version 3. x och 4. x  
- [Red Hat OpenShift](https://docs.openshift.com/container-platform/4.3/welcome/index.html) version 4. x  
- Ett [Arc-aktiverat Kubernetes-kluster](../../azure-arc/kubernetes/overview.md)

Du kan också övervaka prestanda för arbets belastningar som distribueras till självhanterade Kubernetes-kluster som finns på:
- Azure med hjälp av [AKS-motorn](https://github.com/Azure/aks-engine)
- [Azure Stack](/azure-stack/user/azure-stack-kubernetes-aks-engine-overview) eller lokalt, genom att använda AKS-motorn.

Du kan aktivera Azure Monitor för behållare för en ny distribution eller för en eller flera befintliga distributioner av Kubernetes med någon av följande metoder som stöds:

- Azure Portal
- Azure PowerShell
- Azure CLI
- [Terraform och AKS](/azure/developer/terraform/create-k8s-cluster-with-tf-and-aks)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Förutsättningar

Innan du börjar kontrollerar du att du uppfyller följande krav:

> [!IMPORTANT]
> Log Analytics Pod Linux-agenten (REPLICASET) gör API-anrop till alla Windows-noder på Kubelet Secure-port (10250) i klustret för att samla in värden för nod-och container prestanda. Kubelet Secure port (: 10250) måste öppnas i klustrets virtuella nätverk för att både inkommande och utgående för Windows-nod och container prestanda-insamling ska fungera.
>
> Om du har ett Kubernetes-kluster med Windows-noder kan du granska och konfigurera nätverks säkerhets gruppen och nätverks principer för att kontrol lera att den Kubelet-säkra porten (: 10250) är öppen för både inkommande och utgående i klustrets virtuella nätverk.


- Du har en Log Analytics-arbetsyta.

   Azure Monitor for containers stöder en Log Analytics arbets yta i de regioner som listas i produkter som är [tillgängliga efter region](https://azure.microsoft.com/global-infrastructure/services/?regions=all&products=monitor).

   Du kan skapa en arbets yta när du aktiverar övervakning för ditt nya AKS-kluster, eller så kan du låta onboarding-upplevelsen skapa en standard arbets yta i standard resurs gruppen för AKS-kluster prenumerationen. 
   
   Om du väljer att skapa arbets ytan själv kan du skapa den genom att: 
   - [Azure Resource Manager](../samples/resource-manager-workspace.md)
   - [PowerShell](../scripts/powershell-sample-create-workspace.md?toc=%2fpowershell%2fmodule%2ftoc.json)
   - [Azure-portalen](../learn/quick-create-workspace.md) 
   
   En lista över de mappnings par som stöds och som ska användas för standard arbets ytan finns i [regions mappning för Azure Monitor för behållare](container-insights-region-mapping.md).

- Du är medlem i gruppen *Log Analytics deltagare* för att aktivera övervakning av behållare. Mer information om hur du styr åtkomsten till en Log Analytics arbets yta finns i [hantera arbets ytor](../platform/manage-access.md).

- Du är medlem i [gruppen *ägare*](../../role-based-access-control/built-in-roles.md#owner) på kluster resursen AKS.

   [!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]

- Om du vill visa övervaknings data måste du ha [*Log Analytics läsar*](../platform/manage-access.md#manage-access-using-azure-permissions) roll på arbets ytan Log Analytics som kon figurer ats med Azure Monitor för behållare.

- Prometheus-mått samlas inte in som standard. Innan du [konfigurerar agenten](container-insights-prometheus-integration.md) för att samla in måtten är det viktigt att granska [Prometheus-dokumentationen](https://prometheus.io/) för att ta reda på vilka data som kan kasseras och vilka metoder som stöds.

## <a name="supported-configurations"></a>Konfigurationer som stöds

Azure Monitor för behållare som officiellt stöder följande konfigurationer:

- Miljöer: Azure Red Hat OpenShift, Kubernetes lokalt och AKS-motorn på Azure och Azure Stack. Mer information finns i [AKS-motorn på Azure Stack](/azure-stack/user/azure-stack-kubernetes-aks-engine-overview).
- Versionerna av Kubernetes och support policy är desamma som de som [stöds i Azure Kubernetes service (AKS)](../../aks/supported-kubernetes-versions.md). 

## <a name="network-firewall-requirements"></a>Krav för nätverks brand vägg

I följande tabell visas den konfigurations information för proxy och brand vägg som krävs för att behållarens agent ska kunna kommunicera med Azure Monitor för behållare. All nätverks trafik från agenten är utgående till Azure Monitor.

|Agentresurs|Port |
|--------------|------|
| `*.ods.opinsights.azure.com` | 443 |
| `*.oms.opinsights.azure.com` | 443 |
| `dc.services.visualstudio.com` | 443 |
| `*.monitoring.azure.com` | 443 |
| `login.microsoftonline.com` | 443 |

I följande tabell visas konfigurations information för proxy och brand vägg för Azure Kina 21Vianet:

|Agentresurs|Port |Description | 
|--------------|------|-------------|
| `*.ods.opinsights.azure.cn` | 443 | Datainhämtning |
| `*.oms.opinsights.azure.cn` | 443 | OMS-onboarding |
| `dc.services.visualstudio.com` | 443 | För telemetri som använder Azures offentliga moln Application Insights |

I följande tabell visas konfigurations information för proxy och brand vägg för Azure amerikanska myndigheter:

|Agentresurs|Port |Description | 
|--------------|------|-------------|
| `*.ods.opinsights.azure.us` | 443 | Datainhämtning |
| `*.oms.opinsights.azure.us` | 443 | OMS-onboarding |
| `dc.services.visualstudio.com` | 443 | För telemetri som använder Azures offentliga moln Application Insights |

## <a name="components"></a>Komponenter

Din möjlighet att övervaka prestanda är beroende av en container Log Analytics-agent för Linux som är särskilt utvecklad för Azure Monitor för behållare. Den här specialiserade agenten samlar in prestanda-och händelse data från alla noder i klustret och agenten distribueras automatiskt och registreras med den angivna Log Analytics arbets ytan under distributionen. 

Agent versionen är Microsoft/OMS: ciprod04202018 eller senare, och den representeras av ett datum i följande format: *mmddyyyy*.

>[!NOTE]
>Med den allmänna tillgängligheten för Windows Server-stöd för AKS, har ett AKS-kluster med Windows Server-noder en för hands version installerad som en daemonset-Pod på varje enskild Windows Server-nod för att samla in loggar och vidarebefordra den till Log Analytics. För prestanda mått samlar en Linux-nod som automatiskt distribueras i klustret som en del av standard distributionen in och vidarebefordrar data till Azure Monitor åt alla Windows-noder i klustret.

När en ny version av agenten släpps, uppgraderas den automatiskt i dina hanterade Kubernetes-kluster som finns i Azure Kubernetes service (AKS). Information om hur du spårar vilka versioner som släpps finns i [agent release-meddelanden](https://github.com/microsoft/docker-provider/tree/ci_feature_prod).

> [!NOTE]
> Om du redan har distribuerat ett AKS-kluster har du aktiverat övervakning med hjälp av antingen Azure CLI eller en tillhandahållen Azure Resource Manager mall, som det visas längre fram i den här artikeln. Du kan inte använda `kubectl` för att uppgradera, ta bort, distribuera om eller distribuera agenten.
>
> Mallen måste distribueras i samma resurs grupp som klustret.

Om du vill aktivera Azure Monitor för behållare använder du en av de metoder som beskrivs i följande tabell:

| Distributions tillstånd | Metod | Beskrivning |
|------------------|--------|-------------|
| Nytt Kubernetes-kluster | [Skapa ett AKS-kluster med hjälp av Azure CLI](../../aks/kubernetes-walkthrough.md#create-aks-cluster)| Du kan aktivera övervakning för ett nytt AKS-kluster som du skapar med hjälp av Azure CLI. |
| | [Skapa ett AKS-kluster med terraform](container-insights-enable-new-cluster.md#enable-using-terraform)| Du kan aktivera övervakning för ett nytt AKS-kluster som du skapar med hjälp av terraform med öppen källkod. |
| | [Skapa ett OpenShift-kluster med hjälp av en Azure Resource Manager mall](container-insights-azure-redhat-setup.md#enable-for-a-new-cluster-using-an-azure-resource-manager-template) | Du kan aktivera övervakning för ett nytt OpenShift-kluster som du skapar med hjälp av en förkonfigurerad Azure Resource Manager mall. |
| | [Skapa ett OpenShift-kluster med hjälp av Azure CLI](/cli/azure/openshift#az-openshift-create) | Du kan aktivera övervakning när du distribuerar ett nytt OpenShift-kluster med hjälp av Azure CLI. |
| Befintligt Kubernetes-kluster | [Aktivera övervakning av ett AKS-kluster med hjälp av Azure CLI](container-insights-enable-existing-clusters.md#enable-using-azure-cli) | Du kan aktivera övervakning av ett AKS-kluster som redan har distribuerats med hjälp av Azure CLI. |
| |[Aktivera för AKS-kluster med terraform](container-insights-enable-existing-clusters.md#enable-using-terraform) | Du kan aktivera övervakning av ett AKS-kluster som redan har distribuerats med hjälp av terraform med öppen källkod. |
| | [Aktivera för AKS-kluster från Azure Monitor](container-insights-enable-existing-clusters.md#enable-from-azure-monitor-in-the-portal)| Du kan aktivera övervakning av ett eller flera AKS-kluster som redan har distribuerats från sidan med flera kluster i Azure Monitor. |
| | [Aktivera från AKS-kluster](container-insights-enable-existing-clusters.md#enable-directly-from-aks-cluster-in-the-portal)| Du kan aktivera övervakning direkt från ett AKS-kluster i Azure Portal. |
| | [Aktivera för AKS-kluster med hjälp av en Azure Resource Manager mall](container-insights-enable-existing-clusters.md#enable-using-an-azure-resource-manager-template)| Du kan aktivera övervakning av ett AKS-kluster med hjälp av en förkonfigurerad Azure Resource Manager mall. |
| | [Aktivera för Hybrid Kubernetes-kluster](container-insights-hybrid-setup.md) | Du kan aktivera övervakning för AKS-motorn som finns på Azure Stack eller för ett Kubernetes-kluster som finns lokalt. |
| | [Aktivera för Arc-aktiverat Kubernetes-kluster](container-insights-enable-arc-enabled-clusters.md). | Du kan aktivera övervakning av Kubernetes-kluster som ligger utanför Azure och som är aktiverade med Azure Arc. |
| | [Aktivera för OpenShift-kluster med en Azure Resource Manager mall](container-insights-azure-redhat-setup.md#enable-using-an-azure-resource-manager-template) | Du kan aktivera övervakning för ett befintligt OpenShift-kluster med hjälp av en förkonfigurerad Azure Resource Manager mall. |
| | [Aktivera för OpenShift-kluster från Azure Monitor](container-insights-azure-redhat-setup.md#from-the-azure-portal) | Du kan aktivera övervakning för ett eller flera OpenShift-kluster som redan har distribuerats från sidan multikluster i Azure Monitor. |

## <a name="next-steps"></a>Nästa steg

Nu när du har aktiverat övervakning kan du börja analysera prestanda för dina Kubernetes-kluster som finns i Azure Kubernetes service (AKS), Azure Stack eller någon annan miljö. Information om hur du använder Azure Monitor för behållare finns i [Visa Kubernetes kluster prestanda](container-insights-analyze.md).
