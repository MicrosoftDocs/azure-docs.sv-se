---
title: Övervaka ett Azure Kubernetes service (AKS)-kluster distribuerat | Microsoft Docs
description: Lär dig hur du aktiverar övervakning av ett Azure Kubernetes service-kluster (AKS) med Azure Monitor för behållare som redan har distribuerats i din prenumeration.
ms.topic: conceptual
ms.date: 09/12/2019
ms.custom: devx-track-terraform, devx-track-azurecli
ms.openlocfilehash: 547c22e4d82aa728009a2fdb42f2c3b481b7a625
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/17/2021
ms.locfileid: "100625295"
---
# <a name="enable-monitoring-of-azure-kubernetes-service-aks-cluster-already-deployed"></a>Aktivera övervakning av AKS-kluster (Azure Kubernetes service) redan distribuerat

Den här artikeln beskriver hur du konfigurerar Azure Monitor för behållare för att övervaka hanterade Kubernetes-kluster som finns i [Azure Kubernetes-tjänsten](../../aks/index.yml) som redan har distribuerats i din prenumeration.

Du kan aktivera övervakning av ett AKS-kluster som redan har distribuerats med hjälp av en av de metoder som stöds:

* Azure CLI
* Terraform
* [Från Azure Monitor](#enable-from-azure-monitor-in-the-portal) eller [direkt från AKS-klustret](#enable-directly-from-aks-cluster-in-the-portal) i Azure Portal
* Med den [tillhandahållna Azure Resource Manager-mallen](#enable-using-an-azure-resource-manager-template) med hjälp av Azure PowerShell-cmdlet `New-AzResourceGroupDeployment` eller med Azure CLI.

## <a name="sign-in-to-the-azure-portal"></a>Logga in på Azure Portal

Logga in på [Azure-portalen](https://portal.azure.com).

## <a name="enable-using-azure-cli"></a>Aktivera med Azure CLI

Följande steg aktiverar övervakning av ditt AKS-kluster med hjälp av Azure CLI. I det här exemplet behöver du inte skapa eller ange en befintlig arbets yta. Med det här kommandot kan du förenkla processen åt dig genom att skapa en standard arbets yta i standard resurs gruppen för AKS-kluster prenumerationen om en sådan inte redan finns i regionen.  Standard arbets ytan som skapats liknar formatet *DefaultWorkspace- \<GUID> - \<Region>*.

```azurecli
az aks enable-addons -a monitoring -n MyExistingManagedCluster -g MyExistingManagedClusterRG
```

Utdata ser ut ungefär så här:

```output
provisioningState       : Succeeded
```

### <a name="integrate-with-an-existing-workspace"></a>Integrera med en befintlig arbets yta

Om du hellre vill integrera med en befintlig arbets yta utför du följande steg för att först identifiera det fullständiga resurs-ID: t för din Log Analytics arbets yta som krävs för `--workspace-resource-id` parametern och kör sedan kommandot för att aktivera övervaknings tillägget mot den angivna arbets ytan.

1. Lista alla prenumerationer som du har åtkomst till med hjälp av följande kommando:

    ```azurecli
    az account list --all -o table
    ```

    Utdata ser ut ungefär så här:

    ```output
    Name                                  CloudName    SubscriptionId                        State    IsDefault
    ------------------------------------  -----------  ------------------------------------  -------  -----------
    Microsoft Azure                       AzureCloud   68627f8c-91fO-4905-z48q-b032a81f8vy0  Enabled  True
    ```

    Kopiera värdet för **SubscriptionId**.

2. Växla till den prenumeration som är värd för Log Analytics arbets ytan med hjälp av följande kommando:

    ```azurecli
    az account set -s <subscriptionId of the workspace>
    ```

3. I följande exempel visas listan över arbets ytor i dina prenumerationer i standardformatet JSON.

    ```azurecli
    az resource list --resource-type Microsoft.OperationalInsights/workspaces -o json
    ```

    I utdata letar du reda på arbets ytans namn och kopierar sedan det fullständiga resurs-ID: t för den Log Analytics arbets ytan under fält **-ID: t**.

4. Kör följande kommando för att aktivera övervaknings tillägget, och Ersätt värdet för `--workspace-resource-id` parametern. Strängvärdet måste vara inom dubbla citat tecken:

    ```azurecli
    az aks enable-addons -a monitoring -n ExistingManagedCluster -g ExistingManagedClusterRG --workspace-resource-id "/subscriptions/<SubscriptionId>/resourceGroups/<ResourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<WorkspaceName>"
    ```

    Utdata ser ut ungefär så här:

    ```output
    provisioningState       : Succeeded
    ```

## <a name="enable-using-terraform"></a>Aktivera med terraform

1. Lägg till profilen för **oms_agent** -tillägg i den befintliga [azurerm_kubernetes_cluster resursen](https://www.terraform.io/docs/providers/azurerm/d/kubernetes_cluster.html#addon_profile)

   ```
   addon_profile {
    oms_agent {
      enabled                    = true
      log_analytics_workspace_id = "${azurerm_log_analytics_workspace.test.id}"
     }
   }
   ```

2. Lägg till [azurerm_log_analytics_solution](https://www.terraform.io/docs/providers/azurerm/r/log_analytics_solution.html) följa stegen i terraform-dokumentationen.

## <a name="enable-from-azure-monitor-in-the-portal"></a>Aktivera från Azure Monitor i portalen

Om du vill aktivera övervakning av ditt AKS-kluster i Azure Portal från Azure Monitor gör du följande:

1. I Azure Portal väljer du **övervaka**.

2. Välj **behållare** i listan.

3. På sidan **övervaka – behållare** väljer du **oövervakade kluster**.

4. I listan med oövervakade kluster letar du reda på behållaren i listan och klickar på **Aktivera**.

5. På sidan **onboarding to Azure Monitor for containers** , om du har en befintlig Log Analytics arbets yta i samma prenumeration som klustret, väljer du den i list rutan.
    I listan förväljs standard arbets ytan och platsen som AKS-behållaren distribueras till i prenumerationen.

    ![Aktivera övervakning av AKS container Insights](./media/container-insights-onboard/kubernetes-onboard-brownfield-01.png)

    >[!NOTE]
    >Om du vill skapa en ny Log Analytics-arbetsyta för lagring av övervaknings data från klustret, följer du anvisningarna i [skapa en Log Analytics arbets yta](../learn/quick-create-workspace.md). Se till att skapa arbets ytan i samma prenumeration som AKS-behållaren distribueras till.

När du har aktiverat övervakning kan det ta ungefär 15 minuter innan du kan visa hälso mått för klustret.

## <a name="enable-directly-from-aks-cluster-in-the-portal"></a>Aktivera direkt från AKS-kluster i portalen

Gör så här för att aktivera övervakning direkt från ett av dina AKS-kluster i Azure Portal:

1. Välj **Alla tjänster** i Azure-portalen.

2. Börja skriva **behållare** i listan över resurser.  List filtren baserat på dina inaktuella inaktuella.

3. Välj **Kubernetes-tjänster**.
    
4. Välj en tjänst i listan över Kubernetes-tjänster.

5. På sidan Översikt över Kubernetes-tjänsten väljer du **övervakning-Insights**.

6. På sidan **onboarding to Azure Monitor for containers** , om du har en befintlig Log Analytics arbets yta i samma prenumeration som klustret, väljer du den i list rutan.
    I listan förväljs standard arbets ytan och platsen som AKS-behållaren distribueras till i prenumerationen.

    ![Aktivera övervakning av hälso tillstånd för AKS-behållare](./media/container-insights-onboard/kubernetes-onboard-brownfield-02.png)

    >[!NOTE]
    >Om du vill skapa en ny Log Analytics-arbetsyta för lagring av övervaknings data från klustret, följer du anvisningarna i [skapa en Log Analytics arbets yta](../learn/quick-create-workspace.md). Se till att skapa arbets ytan i samma prenumeration som AKS-behållaren distribueras till.

När du har aktiverat övervakning kan det ta ungefär 15 minuter innan du kan visa användnings data för klustret.

## <a name="enable-using-an-azure-resource-manager-template"></a>Aktivera med hjälp av en Azure Resource Manager mall

Den här metoden inkluderar två JSON-mallar. En mall anger konfigurationen för att aktivera övervakning och den andra innehåller parameter värden som du konfigurerar för att ange följande:

* Resurs-ID för AKS-behållare.
* Resurs gruppen som klustret har distribuerats i.

>[!NOTE]
>Mallen måste distribueras i samma resurs grupp som klustret.
>

Log Analytics arbets ytan måste skapas innan du aktiverar övervakning med Azure PowerShell eller CLI. Om du vill skapa arbets ytan kan du konfigurera den genom [Azure Resource Manager](../samples/resource-manager-workspace.md), via [PowerShell](../scripts/powershell-sample-create-workspace.md?toc=%2fpowershell%2fmodule%2ftoc.json)eller i [Azure Portal](../learn/quick-create-workspace.md).

Om du inte känner till konceptet att distribuera resurser med hjälp av en mall, se:

* [Distribuera resurser med Resource Manager-mallar och Azure PowerShell](../../azure-resource-manager/templates/deploy-powershell.md)

* [Distribuera resurser med Resource Manager-mallar och Azure CLI](../../azure-resource-manager/templates/deploy-cli.md)

Om du väljer att använda Azure CLI måste du först installera och använda CLI lokalt. Du måste köra Azure CLI-versionen 2.0.59 eller senare. För att identifiera din version, kör `az --version` . Om du behöver installera eller uppgradera Azure CLI kan du läsa [Installera Azure CLI](/cli/azure/install-azure-cli).

### <a name="create-and-execute-a-template"></a>Skapa och köra en mall

1. Kopiera och klistra in följande JSON-syntax i filen:

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "aksResourceId": {
          "type": "string",
          "metadata": {
            "description": "AKS Cluster Resource ID"
          }
        },
        "aksResourceLocation": {
          "type": "string",
          "metadata": {
            "description": "Location of the AKS resource e.g. \"East US\""
          }
        },
        "aksResourceTagValues": {
          "type": "object",
          "metadata": {
            "description": "Existing all tags on AKS Cluster Resource"
          }
        },
        "workspaceResourceId": {
          "type": "string",
          "metadata": {
            "description": "Azure Monitor Log Analytics Resource ID"
          }
        }
      },
      "resources": [
        {
          "name": "[split(parameters('aksResourceId'),'/')[8]]",
          "type": "Microsoft.ContainerService/managedClusters",
          "location": "[parameters('aksResourceLocation')]",
          "tags": "[parameters('aksResourceTagValues')]",
          "apiVersion": "2018-03-31",
          "properties": {
            "mode": "Incremental",
            "id": "[parameters('aksResourceId')]",
            "addonProfiles": {
              "omsagent": {
                "enabled": true,
                "config": {
                  "logAnalyticsWorkspaceResourceID": "[parameters('workspaceResourceId')]"
                }
              }
            }
          }
        }
      ]
    }
    ```

2. Spara den här filen som **existingClusterOnboarding.jspå** en lokal mapp.

3. Klistra in följande JSON-syntax i filen:

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "aksResourceId": {
          "value": "/subscriptions/<SubscriptionId>/resourcegroups/<ResourceGroup>/providers/Microsoft.ContainerService/managedClusters/<ResourceName>"
        },
        "aksResourceLocation": {
          "value": "<aksClusterLocation>"
        },
        "workspaceResourceId": {
          "value": "/subscriptions/<SubscriptionId>/resourceGroups/<ResourceGroup>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>"
        },
        "aksResourceTagValues": {
          "value": {
            "<existing-tag-name1>": "<existing-tag-value1>",
            "<existing-tag-name2>": "<existing-tag-value2>",
            "<existing-tag-nameN>": "<existing-tag-valueN>"
          }
        }
      }
    }
    ```

4. Redigera värdena för **aksResourceId** och **aksResourceLocation** med hjälp av värdena på **AKS översikts** sida för AKS-klustret. Värdet för **workspaceResourceId** är det fullständiga resurs-ID: t för din Log Analytics-arbetsyta, som innehåller namnet på arbets ytan.

    Redigera värdena för **aksResourceTagValues** så att de matchar de befintliga taggvärde som angetts för AKS-klustret.

5. Spara den här filen som **existingClusterParam.jspå** en lokal mapp.

6. Nu är det dags att distribuera den här mallen.

   * Om du vill distribuera med Azure PowerShell använder du följande kommandon i mappen som innehåller mallen:

       ```powershell
       New-AzResourceGroupDeployment -Name OnboardCluster -ResourceGroupName <ResourceGroupName> -TemplateFile .\existingClusterOnboarding.json -TemplateParameterFile .\existingClusterParam.json
       ```

       Konfigurations ändringen kan ta några minuter att slutföra. När det är klart visas ett meddelande som liknar följande och som innehåller resultatet:

       ```output
       provisioningState       : Succeeded
       ```

   * Om du vill distribuera med Azure CLI kör du följande kommandon:

       ```azurecli
       az login
       az account set --subscription "Subscription Name"
       az deployment group create --resource-group <ResourceGroupName> --template-file ./existingClusterOnboarding.json --parameters @./existingClusterParam.json
       ```

       Konfigurations ändringen kan ta några minuter att slutföra. När det är klart visas ett meddelande som liknar följande och som innehåller resultatet:

       ```output
       provisioningState       : Succeeded
       ```

       När du har aktiverat övervakning kan det ta ungefär 15 minuter innan du kan visa hälso mått för klustret.

## <a name="verify-agent-and-solution-deployment"></a>Verifiera distribution av agent och lösning

Med agent version *06072018* eller senare kan du kontrol lera att både agenten och lösningen har distribuerats. Med tidigare versioner av agenten kan du bara verifiera agent distribution.

### <a name="agent-version-06072018-or-later"></a>Agent version 06072018 eller senare

Kör följande kommando för att kontrol lera att agenten har distribuerats.

```
kubectl get ds omsagent --namespace=kube-system
```

Utdata bör likna följande, som anger att den har distribuerats korrekt:

```output
User@aksuser:~$ kubectl get ds omsagent --namespace=kube-system
NAME       DESIRED   CURRENT   READY     UP-TO-DATE   AVAILABLE   NODE SELECTOR                 AGE
omsagent   2         2         2         2            2           beta.kubernetes.io/os=linux   1d
```

Om det finns Windows Server-noder i klustret kan du köra följande kommando för att kontrol lera att agenten har distribuerats.

```
kubectl get ds omsagent-win --namespace=kube-system
```

Utdata bör likna följande, som anger att den har distribuerats korrekt:

```output
User@aksuser:~$ kubectl get ds omsagent-win --namespace=kube-system
NAME                   DESIRED   CURRENT   READY     UP-TO-DATE   AVAILABLE   NODE SELECTOR                   AGE
omsagent-win           2         2         2         2            2           beta.kubernetes.io/os=windows   1d
```

Kör följande kommando för att kontrol lera distributionen av lösningen:

```
kubectl get deployment omsagent-rs -n=kube-system
```

Utdata bör likna följande, som anger att den har distribuerats korrekt:

```output
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

```output
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

```output
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

* När övervakning har Aktiver ATS för att samla in hälso-och resursutnyttjande för ditt AKS-kluster och arbets belastningar som körs på dem, lär [du dig hur du använder](container-insights-analyze.md) Azure Monitor för behållare.
