---
title: Övervaka ett nytt Azure Kubernetes service-kluster (AKS) | Microsoft Docs
description: Lär dig hur du aktiverar övervakning för ett nytt Azure Kubernetes service-kluster (AKS) med container Insights-prenumerationen.
ms.topic: conceptual
ms.date: 04/25/2019
ms.custom: devx-track-terraform, devx-track-azurecli
ms.openlocfilehash: 9b6c4f8a05b8e7a350ebd5afd677e8bb2ee6e9b4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "101717578"
---
# <a name="enable-monitoring-of-a-new-azure-kubernetes-service-aks-cluster"></a>Aktivera övervakning av ett nytt Azure Kubernetes service-kluster (AKS)

Den här artikeln beskriver hur du konfigurerar behållar insikter för att övervaka hanterade Kubernetes-kluster som finns i [Azure Kubernetes-tjänsten](../../aks/index.yml) som du förbereder att distribuera i din prenumeration.

Du kan aktivera övervakning av ett AKS-kluster med hjälp av en av de metoder som stöds:

* Azure CLI
* Terraform

## <a name="enable-using-azure-cli"></a>Aktivera med Azure CLI

Om du vill aktivera övervakning av ett nytt AKS-kluster som skapats med Azure CLI följer du stegen i artikeln snabb start under avsnittet [skapa AKS-kluster](../../aks/kubernetes-walkthrough.md#create-aks-cluster).  

>[!NOTE]
>Om du väljer att använda Azure CLI måste du först installera och använda CLI lokalt. Du måste köra Azure CLI-versionen 2.0.74 eller senare. För att identifiera din version, kör `az --version` . Om du behöver installera eller uppgradera Azure CLI kan du läsa [Installera Azure CLI](/cli/azure/install-azure-cli). Om du har installerat AKS-Preview CLI-tillägget 0.4.12 eller högre, tar du bort alla ändringar som du har gjort för att aktivera ett förhands gransknings tillägg eftersom det kan åsidosätta standard beteendet för Azure CLI eftersom AKS Preview-funktioner inte är tillgängliga i Azure US Governmnet-molnet.

## <a name="enable-using-terraform"></a>Aktivera med terraform

Om du [distribuerar ett nytt AKS-kluster med terraform](/azure/developer/terraform/create-k8s-cluster-with-tf-and-aks)anger du de argument som krävs i profilen [för att skapa en Log Analytics arbets yta](https://www.terraform.io/docs/providers/azurerm/r/log_analytics_workspace.html) om du inte väljer att ange en befintlig. 

>[!NOTE]
>Om du väljer att använda terraform måste du köra terraform Azure RM-providerns version 1.17.0 eller senare.

Information om hur du lägger till behållar insikter till arbets ytan finns i [azurerm_log_analytics_solution](https://www.terraform.io/docs/providers/azurerm/r/log_analytics_solution.html) och slutföra profilen genom att inkludera [**addon_profile**](https://www.terraform.io/docs/providers/azurerm/r/kubernetes_cluster.html#addon_profile) och ange **oms_agent**. 

När du har aktiverat övervakning och alla konfigurations aktiviteter har slutförts kan du övervaka klustrets prestanda på något av två sätt:

* Direkt i AKS-klustret genom att välja **hälsa** i det vänstra fönstret.
* Genom att välja panelen **övervaka behållar insikter** på kluster sidan AKS för det valda klustret. I Azure Monitor i det vänstra fönstret väljer du **hälsa**. 

  ![Alternativ för att välja container Insights i AKS](./media/container-insights-onboard/kubernetes-select-monitoring-01.png)

När du har aktiverat övervakning kan det ta ungefär 15 minuter innan du kan visa hälso mått för klustret. 

## <a name="verify-agent-and-solution-deployment"></a>Verifiera distribution av agent och lösning
Med agent version *06072018* eller senare kan du kontrol lera att både agenten och lösningen har distribuerats. Med tidigare versioner av agenten kan du bara verifiera agent distribution.

### <a name="agent-version-06072018-or-later"></a>Agent version 06072018 eller senare
Kör följande kommando för att kontrol lera att agenten har distribuerats. 

```
kubectl get ds omsagent --namespace=kube-system
```

Utdata bör likna följande, som anger att den har distribuerats korrekt:

```
User@aksuser:~$ kubectl get ds omsagent --namespace=kube-system 
NAME       DESIRED   CURRENT   READY     UP-TO-DATE   AVAILABLE   NODE SELECTOR                 AGE
omsagent   2         2         2         2            2           beta.kubernetes.io/os=linux   1d
```  

Kör följande kommando för att kontrol lera distributionen av lösningen:

```
kubectl get deployment omsagent-rs -n=kube-system
```

Utdata bör likna följande, som anger att den har distribuerats korrekt:

```
User@aksuser:~$ kubectl get deployment omsagent-rs -n=kube-system 
NAME       DESIRED   CURRENT   UP-TO-DATE   AVAILABLE    AGE
omsagent   1         1         1            1            3h
```

### <a name="agent-version-earlier-than-06072018"></a>Agent version tidigare än 06072018

Kör följande kommando för att kontrol lera att den Log Analytics agent version som släpptes innan *06072018* distribueras korrekt:  

```
kubectl get ds omsagent --namespace=kube-system
```

Utdata bör likna följande, som anger att den har distribuerats korrekt:  

```
User@aksuser:~$ kubectl get ds omsagent --namespace=kube-system 
NAME       DESIRED   CURRENT   READY     UP-TO-DATE   AVAILABLE   NODE SELECTOR                 AGE
omsagent   2         2         2         2            2           beta.kubernetes.io/os=linux   1d
```  

## <a name="view-configuration-with-cli"></a>Visa konfiguration med CLI
Använd `aks show` kommandot för att få information om t. ex. om lösningen är aktive rad eller inte, vad är resourceID för Log Analytics-arbetsytan och sammanfattnings information om klustret.  

```azurecli
az aks show -g <resourceGroupofAKSCluster> -n <nameofAksCluster>
```

Efter några minuter slutförs kommandot och returnerar JSON-formaterad information om lösningen.  Resultatet av kommandot bör visa profilen för övervakning av tilläggsprogram och liknar följande exempel på utdata:

```
"addonProfiles": {
    "omsagent": {
      "config": {
        "logAnalyticsWorkspaceResourceID": "/subscriptions/<WorkspaceSubscription>/resourceGroups/<DefaultWorkspaceRG>/providers/Microsoft.OperationalInsights/workspaces/<defaultWorkspaceName>"
      },
      "enabled": true
    }
  }
```

## <a name="next-steps"></a>Nästa steg

* Läs [fel söknings guiden](container-insights-troubleshoot.md) om du får problem när du försöker publicera lösningen

* När övervakning har Aktiver ATS för att samla in hälso-och resursutnyttjande för ditt AKS-kluster och arbets belastningar som körs på dem, lär [du dig hur du använder](container-insights-analyze.md) behållar insikter.

