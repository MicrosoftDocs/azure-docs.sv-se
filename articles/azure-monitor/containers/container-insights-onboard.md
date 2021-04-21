---
title: Aktivera containerinsikter | Microsoft Docs
description: Den här artikeln beskriver hur du aktiverar och konfigurerar containerinsikter så att du kan förstå hur din container presterar och vilka prestandarelaterade problem som har identifierats.
ms.topic: conceptual
ms.date: 06/30/2020
ms.openlocfilehash: e0544232f40e93cce0705fff6814d29697a96218
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107782125"
---
# <a name="enable-container-insights"></a>Aktivera containerinsikter

Den här artikeln innehåller en översikt över de alternativ som är tillgängliga för att konfigurera Container Insights för att övervaka prestanda för arbetsbelastningar som distribueras till Kubernetes-miljöer och som finns på:

- [Azure Kubernetes Service (AKS)](../../aks/index.yml)  
- [Azure Red Hat OpenShift](../../openshift/intro-openshift.md) versionerna 3.x och 4.x  
- [Red Hat OpenShift](https://docs.openshift.com/container-platform/4.3/welcome/index.html) version 4.x  
- Ett [Arc-aktiverat Kubernetes-kluster](../../azure-arc/kubernetes/overview.md)

Du kan också övervaka prestanda för arbetsbelastningar som distribueras till själv hanterade Kubernetes-kluster som finns i:
- Azure med hjälp av [AKS-motorn](https://github.com/Azure/aks-engine)
- [Azure Stack](/azure-stack/user/azure-stack-kubernetes-aks-engine-overview) eller lokalt med hjälp av AKS-motorn.

Du kan aktivera Container Insights för en ny distribution eller för en eller flera befintliga distributioner av Kubernetes med någon av följande metoder som stöds:

- Azure Portal
- Azure PowerShell
- Azure CLI
- [Terraform och AKS](/azure/developer/terraform/create-k8s-cluster-with-tf-and-aks)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Förutsättningar

Kontrollera att du har uppfyllt följande krav innan du börjar:

> [!IMPORTANT]
> Log Analytics Containerized Linux Agent (replicaset pod) gör API-anrop till alla Windows-noder på Kubelet Secure Port (10250) i klustret för att samla in prestandarelaterade mått för noder och containrar. Kubelet-säker port (:10250) bör öppnas i klustrets virtuella nätverk för både inkommande och utgående för att prestandainsamling för Windows Node- och containerprestanda ska fungera.
>
> Om du har ett Kubernetes-kluster med Windows-noder granskar och konfigurerar du principerna för nätverkssäkerhetsgrupp och nätverk för att se till att kubelet-säker port (:10250) är öppen för både inkommande och utgående i klustrets virtuella nätverk.


- Du har en Log Analytics-arbetsyta.

   Container insights stöder en Log Analytics-arbetsyta i de regioner som anges i [Produkt tillgänglig per region.](https://azure.microsoft.com/global-infrastructure/services/?regions=all&products=monitor)

   Du kan skapa en arbetsyta när du aktiverar övervakning för ditt nya AKS-kluster, eller så kan du låta onboarding-upplevelsen skapa en standardarbetsyta i standardresursgruppen för AKS-klusterprenumerationen. 
   
   Om du väljer att skapa arbetsytan själv kan du skapa den via: 
   - [Azure Resource Manager](../logs/resource-manager-workspace.md)
   - [PowerShell](../logs/powershell-sample-create-workspace.md?toc=%2fpowershell%2fmodule%2ftoc.json)
   - [Azure-portalen](../logs/quick-create-workspace.md) 
   
   En lista över de mappningspar som stöds för standardarbetsytan finns i [Regionmappning för Container insights.](container-insights-region-mapping.md)

- Du är medlem i *Log Analytics-deltagargruppen för* att aktivera containerövervakning. Mer information om hur du styr åtkomsten till en Log Analytics-arbetsyta finns i [Hantera arbetsytor.](../logs/manage-access.md)

- Du är medlem i gruppen [ *Ägare* på](../../role-based-access-control/built-in-roles.md#owner) AKS-klusterresursen.

   [!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]

- Om du vill visa övervakningsdata måste du ha [*Log Analytics-läsarrollen*](../logs/manage-access.md#manage-access-using-azure-permissions) på Log Analytics-arbetsytan, konfigurerad med Container Insights.

- Prometheus-mått samlas inte in som standard. Innan du [konfigurerar agenten](container-insights-prometheus-integration.md) för att samla in måtten är det viktigt att läsa [Prometheus-dokumentationen](https://prometheus.io/) för att förstå vilka data som kan samlas in och vilka metoder som stöds.
- Ett AKS-kluster kan kopplas till en Log Analytics-arbetsyta i en annan Azure-prenumeration i samma Azure AD-klientorganisation. Detta kan för närvarande inte göras med Azure Portal, men det kan göras med Azure CLI eller Resource Manager mallen.

## <a name="supported-configurations"></a>Konfigurationer som stöds

Container insights stöder officiellt följande konfigurationer:

- Miljöer: Azure Red Hat OpenShift, Kubernetes lokalt och AKS-motorn i Azure och Azure Stack. Mer information finns i [AKS-motorn på Azure Stack](/azure-stack/user/azure-stack-kubernetes-aks-engine-overview).
- Versionerna av Kubernetes och supportprincipen är desamma som de [som stöds i Azure Kubernetes Service (AKS).](../../aks/supported-kubernetes-versions.md) 

## <a name="network-firewall-requirements"></a>Krav för nätverksbrandväggen

I följande tabell visas konfigurationsinformation för proxy och brandvägg som krävs för att containeragenten ska kunna kommunicera med Container Insights. All nätverkstrafik från agenten är utgående till Azure Monitor.

|Agentresurs|Port |
|--------------|------|
| `*.ods.opinsights.azure.com` | 443 |
| `*.oms.opinsights.azure.com` | 443 |
| `dc.services.visualstudio.com` | 443 |
| `*.monitoring.azure.com` | 443 |
| `login.microsoftonline.com` | 443 |

I följande tabell visas konfigurationsinformation för proxy och brandvägg för Azure China 21Vianet:

|Agentresurs|Port |Description | 
|--------------|------|-------------|
| `*.ods.opinsights.azure.cn` | 443 | Datainhämtning |
| `*.oms.opinsights.azure.cn` | 443 | OMS-registrering |
| `dc.services.visualstudio.com` | 443 | För agenttelemetri som använder Azure Public Cloud Application Insights |

I följande tabell visas konfigurationsinformation för proxy och brandvägg för Azure US Government:

|Agentresurs|Port |Description | 
|--------------|------|-------------|
| `*.ods.opinsights.azure.us` | 443 | Datainhämtning |
| `*.oms.opinsights.azure.us` | 443 | OMS-registrering |
| `dc.services.visualstudio.com` | 443 | För agenttelemetri som använder Azure Public Cloud Application Insights |

## <a name="components"></a>Komponenter

Din förmåga att övervaka prestanda förlitar sig på en Containerized Log Analytics-agent för Linux som är särskilt utformad för Container Insights. Den här specialiserade agenten samlar in prestanda- och händelsedata från alla noder i klustret, och agenten distribueras och registreras automatiskt på den angivna Log Analytics-arbetsytan under distributionen. 

Agentversionen är microsoft/oms:ciprod04202018 eller senare och representeras av ett datum i följande format: *mmddyyyy*.

>[!NOTE]
>Eftersom Windows Server-stöd för AKS är allmänt tillgängligt har ett AKS-kluster med Windows Server-noder en förhandsgranskningsagent installerad som en daemonset-pod på varje enskild Windows-servernod för att samla in loggar och vidarebefordra dem till Log Analytics. För prestandamått samlar en Linux-nod som distribueras automatiskt i klustret som en del av standarddistributionen in och vidarebefordrar data till Azure Monitor åt alla Windows-noder i klustret.

När en ny version av agenten släpps uppgraderas den automatiskt i dina hanterade Kubernetes-kluster som finns på Azure Kubernetes Service (AKS). Information om hur du spårar vilka versioner som släpps finns i [agentversionsmeddelanden.](https://github.com/microsoft/docker-provider/tree/ci_feature_prod)

> [!NOTE]
> Om du redan har distribuerat ett AKS-kluster har du aktiverat övervakning med hjälp av antingen Azure CLI eller en antingen Azure Resource Manager mall, vilket visas senare i den här artikeln. Du kan inte använda `kubectl` för att uppgradera, ta bort, distribuera om eller distribuera agenten.
>
> Mallen måste distribueras i samma resursgrupp som klustret.

Om du vill aktivera containerinsikter använder du någon av de metoder som beskrivs i följande tabell:

| Distributionstillstånd | Metod | Beskrivning |
|------------------|--------|-------------|
| Nytt Kubernetes-kluster | [Skapa ett AKS-kluster med hjälp av Azure CLI](../../aks/kubernetes-walkthrough.md#create-aks-cluster)| Du kan aktivera övervakning för ett nytt AKS-kluster som du skapar med hjälp av Azure CLI. |
| | [Skapa ett AKS-kluster med Terraform](container-insights-enable-new-cluster.md#enable-using-terraform)| Du kan aktivera övervakning för ett nytt AKS-kluster som du skapar med verktyget Terraform med öppen källkod. |
| | [Skapa ett OpenShift-kluster med hjälp av en Azure Resource Manager mall](container-insights-azure-redhat-setup.md#enable-for-a-new-cluster-using-an-azure-resource-manager-template) | Du kan aktivera övervakning för ett nytt OpenShift-kluster som du skapar med hjälp av en förkonfigurerad Azure Resource Manager mall. |
| | [Skapa ett OpenShift-kluster med hjälp av Azure CLI](/cli/azure/openshift#az_openshift_create) | Du kan aktivera övervakning när du distribuerar ett nytt OpenShift-kluster med hjälp av Azure CLI. |
| Befintligt Kubernetes-kluster | [Aktivera övervakning av ett AKS-kluster med hjälp av Azure CLI](container-insights-enable-existing-clusters.md#enable-using-azure-cli) | Du kan aktivera övervakning för ett AKS-kluster som redan har distribuerats med hjälp av Azure CLI. |
| |[Aktivera för AKS-kluster med Terraform](container-insights-enable-existing-clusters.md#enable-using-terraform) | Du kan aktivera övervakning för ett AKS-kluster som redan har distribuerats med verktyget Terraform med öppen källkod. |
| | [Aktivera för AKS-kluster från Azure Monitor](container-insights-enable-existing-clusters.md#enable-from-azure-monitor-in-the-portal)| Du kan aktivera övervakning för ett eller flera AKS-kluster som redan har distribuerats från sidan med flera kluster i Azure Monitor. |
| | [Aktivera från AKS-kluster](container-insights-enable-existing-clusters.md#enable-directly-from-aks-cluster-in-the-portal)| Du kan aktivera övervakning direkt från ett AKS-kluster i Azure Portal. |
| | [Aktivera för AKS-kluster med en Azure Resource Manager mall](container-insights-enable-existing-clusters.md#enable-using-an-azure-resource-manager-template)| Du kan aktivera övervakning för ett AKS-kluster med hjälp av en förkonfigurerad Azure Resource Manager mall. |
| | [Aktivera för Kubernetes-hybridkluster](container-insights-hybrid-setup.md) | Du kan aktivera övervakning för AKS-motorn som finns på Azure Stack eller för ett Kubernetes-kluster som finns lokalt. |
| | [Aktivera för Arc-aktiverat Kubernetes-kluster](container-insights-enable-arc-enabled-clusters.md). | Du kan aktivera övervakning för dina Kubernetes-kluster som finns utanför Azure och som har aktiverats med Azure Arc. |
| | [Aktivera för OpenShift-kluster med en Azure Resource Manager mall](container-insights-azure-redhat-setup.md#enable-using-an-azure-resource-manager-template) | Du kan aktivera övervakning för ett befintligt OpenShift-kluster med hjälp av en förkonfigurerad Azure Resource Manager mall. |
| | [Aktivera för OpenShift-kluster från Azure Monitor](container-insights-azure-redhat-setup.md#from-the-azure-portal) | Du kan aktivera övervakning för ett eller flera OpenShift-kluster som redan har distribuerats från sidan med flera kluster i Azure Monitor. |

## <a name="next-steps"></a>Nästa steg

Nu när du har aktiverat övervakning kan du börja analysera prestanda för dina Kubernetes-kluster som finns på Azure Kubernetes Service (AKS), Azure Stack eller någon annan miljö. Information om hur du använder Container Insights finns i [Visa Kubernetes-klusterprestanda.](container-insights-analyze.md)
