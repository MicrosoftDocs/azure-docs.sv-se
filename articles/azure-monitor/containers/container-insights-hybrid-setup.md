---
title: Konfigurera hybrid Kubernetes-kluster med container Insights | Microsoft Docs
description: I den här artikeln beskrivs hur du kan konfigurera behållar insikter för att övervaka Kubernetes-kluster som finns på Azure Stack eller annan miljö.
ms.topic: conceptual
ms.date: 06/30/2020
ms.openlocfilehash: d2692b4a634d60ef62339f68277591d711260712
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/03/2021
ms.locfileid: "101711254"
---
# <a name="configure-hybrid-kubernetes-clusters-with-container-insights"></a>Konfigurera hybrid Kubernetes-kluster med container Insights

Behållar insikter ger en omfattande övervaknings upplevelse för Azure Kubernetes service (AKS) och [AKS-motorn på Azure](https://github.com/Azure/aks-engine), som är ett självhanterat Kubernetes-kluster som finns på Azure. I den här artikeln beskrivs hur du aktiverar övervakning av Kubernetes-kluster som finns utanför Azure och uppnår en liknande övervaknings upplevelse.

## <a name="supported-configurations"></a>Konfigurationer som stöds

Följande konfigurationer stöds officiellt med behållar insikter. Om du har en annan version av Kubernetes och operativ system versioner skickar du ett e-postmeddelande till askcoin@microsoft.com .

- Utrymmen

    - Kubernetes lokalt
    - AKS-motorn på Azure och Azure Stack. Mer information finns i [AKS-motorn på Azure Stack](/azure-stack/user/azure-stack-kubernetes-aks-engine-overview)
    - [OpenShift](https://docs.openshift.com/container-platform/4.3/welcome/index.html) , version 4 och högre, lokalt eller i andra moln miljöer.

- Versioner av Kubernetes och support policy är samma som versioner av [AKS som stöds](../../aks/supported-kubernetes-versions.md).

- Följande behållar körningar stöds: Docker, Moby och CRI-kompatibla körningar som CRI-O och container.

- Linux OS-versionen för Master-och Worker-noder som stöds är: Ubuntu (18,04 LTS och 16,04 LTS) och Red Hat Enterprise Linux Core 43,81.

- Åtkomst kontroll som stöds: Kubernetes RBAC och icke-RBAC

## <a name="prerequisites"></a>Förutsättningar

Kontrol lera att du har följande innan du börjar:

- En [Log Analytics-arbetsyta](../logs/design-logs-deployment.md).

    Behållar insikter stöder en Log Analytics arbets yta i de regioner som anges i Azure- [produkter efter region](https://azure.microsoft.com/global-infrastructure/services/?regions=all&products=monitor). Om du vill skapa en egen arbets yta kan den skapas via [Azure Resource Manager](../logs/resource-manager-workspace.md), via [PowerShell](../logs/powershell-sample-create-workspace.md?toc=%2fpowershell%2fmodule%2ftoc.json)eller i [Azure Portal](../logs/quick-create-workspace.md).

    >[!NOTE]
    >Det finns inte stöd för att övervaka flera kluster med samma kluster namn till samma Log Analytics-arbetsyta. Kluster namn måste vara unika.
    >

- Du är medlem i **rollen Log Analytics Contributor** för att aktivera övervakning av behållare. Mer information om hur du styr åtkomsten till en Log Analytics arbets yta finns i [Hantera åtkomst till arbets ytan och loggdata](../logs/manage-access.md).

- Om du vill visa övervaknings data måste du ha [*Log Analytics läsar*](../logs/manage-access.md#manage-access-using-azure-permissions) roll på arbets ytan Log Analytics som kon figurer ATS med behållar insikter.

- [Helm-klienten](https://helm.sh/docs/using_helm/) för att publicera behållar insikter-diagrammet för det angivna Kubernetes-klustret.

- Följande konfigurations information för proxy och brand väggar krävs för den behållar version av Log Analytics-agenten för Linux för att kunna kommunicera med Azure Monitor:

    |Agentresurs|Portar |
    |------|---------|
    |*.ods.opinsights.azure.com |Port 443 |
    |*.oms.opinsights.azure.com |Port 443 |
    |*. dc.services.visualstudio.com |Port 443 |

- Den behållare som har behållar agenten kräver Kubelet `cAdvisor secure port: 10250` eller `unsecure port :10255` öppnas på alla noder i klustret för att samla in prestanda mått. Vi rekommenderar att du konfigurerar `secure port: 10250` på Kubelet-cAdvisor om den inte redan har kon figurer ATS.

- Agent agenten kräver att följande miljövariabler anges i behållaren för att kunna kommunicera med Kubernetes-API-tjänsten i klustret för att samla in inventerings data – `KUBERNETES_SERVICE_HOST` och `KUBERNETES_PORT_443_TCP_PORT` .

>[!IMPORTANT]
>Den lägsta agent version som stöds för övervakning av hybrid Kubernetes-kluster är ciprod10182019 eller senare.

## <a name="enable-monitoring"></a>Aktivera övervakning

Att aktivera behållar insikter för Hybrid Kubernetes-klustret består av att utföra följande steg i ordning.

1. Konfigurera din Log Analytics arbets yta med container Insights-lösning.   

2. Aktivera HELM-diagrammet för container Insights med Log Analytics arbets ytan.

Mer information om övervakning av lösningar i Azure Monitor [här](../../azure-monitor/insights/solutions.md).

### <a name="how-to-add-the-azure-monitor-containers-solution"></a>Lägga till Azure Monitor containers-lösningen

Du kan distribuera lösningen med den tillhandahållna Azure Resource Manager-mallen med hjälp av Azure PowerShell-cmdlet `New-AzResourceGroupDeployment` eller med Azure CLI.

Om du inte känner till konceptet att distribuera resurser med hjälp av en mall, se:

- [Distribuera resurser med Resource Manager-mallar och Azure PowerShell](../../azure-resource-manager/templates/deploy-powershell.md)

- [Distribuera resurser med Resource Manager-mallar och Azure CLI](../../azure-resource-manager/templates/deploy-cli.md)

Om du väljer att använda Azure CLI måste du först installera och använda CLI lokalt. Du måste köra Azure CLI-versionen 2.0.59 eller senare. För att identifiera din version, kör `az --version` . Om du behöver installera eller uppgradera Azure CLI kan du läsa [Installera Azure CLI](/cli/azure/install-azure-cli).

Den här metoden inkluderar två JSON-mallar. En mall anger konfigurationen för att aktivera övervakning och den andra innehåller parameter värden som du konfigurerar för att ange följande:

- **workspaceResourceId** – det fullständiga resurs-ID: t för din Log Analytics-arbetsyta.
- **workspaceRegion** – den region som arbets ytan skapas i, vilket även kallas **plats** i egenskaperna för arbets ytan vid visning från Azure Portal.

För att först identifiera det fullständiga resurs-ID: t för din Log Analytics arbets yta som krävs för `workspaceResourceId` parametervärdet i **containerSolutionParams.jspå** filen, utför följande steg och kör sedan PowerShell-cmdleten eller Azure CLI-kommandot för att lägga till lösningen.

1. Lista alla prenumerationer som du har åtkomst till med hjälp av följande kommando:

    ```azurecli
    az account list --all -o table
    ```

    Utdata ser ut ungefär så här:

    ```azurecli
    Name                                  CloudName    SubscriptionId                        State    IsDefault
    ------------------------------------  -----------  ------------------------------------  -------  -----------
    Microsoft Azure                       AzureCloud   0fb60ef2-03cc-4290-b595-e71108e8f4ce  Enabled  True
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

4. Kopiera och klistra in följande JSON-syntax i filen:

    ```json
    {
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "workspaceResourceId": {
            "type": "string",
            "metadata": {
                "description": "Azure Monitor Log Analytics Workspace Resource ID"
            }
        },
        "workspaceRegion": {
            "type": "string",
            "metadata": {
                "description": "Azure Monitor Log Analytics Workspace region"
            }
        }
    },
    "resources": [
        {
            "type": "Microsoft.Resources/deployments",
            "name": "[Concat('ContainerInsights', '-',  uniqueString(parameters('workspaceResourceId')))]",
            "apiVersion": "2017-05-10",
            "subscriptionId": "[split(parameters('workspaceResourceId'),'/')[2]]",
            "resourceGroup": "[split(parameters('workspaceResourceId'),'/')[4]]",
            "properties": {
                "mode": "Incremental",
                "template": {
                    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
                    "contentVersion": "1.0.0.0",
                    "parameters": {},
                    "variables": {},
                    "resources": [
                        {
                            "apiVersion": "2015-11-01-preview",
                            "type": "Microsoft.OperationsManagement/solutions",
                            "location": "[parameters('workspaceRegion')]",
                            "name": "[Concat('ContainerInsights', '(', split(parameters('workspaceResourceId'),'/')[8], ')')]",
                            "properties": {
                                "workspaceResourceId": "[parameters('workspaceResourceId')]"
                            },
                            "plan": {
                                "name": "[Concat('ContainerInsights', '(', split(parameters('workspaceResourceId'),'/')[8], ')')]",
                                "product": "[Concat('OMSGallery/', 'ContainerInsights')]",
                                "promotionCode": "",
                                "publisher": "Microsoft"
                            }
                        }
                    ]
                },
                "parameters": {}
            }
         }
      ]
   }
    ```

5. Spara den här filen som containerSolution.jspå en lokal mapp.

6. Klistra in följande JSON-syntax i filen:

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "workspaceResourceId": {
          "value": "<workspaceResourceId>"
      },
      "workspaceRegion": {
        "value": "<workspaceRegion>"
      }
     }
    }
    ```

7. Redigera värdena för **workspaceResourceId** med värdet som du kopierade i steg 3 **och kopiera sedan** värdet **region** efter att du kört kommandot AZ i Azure CLI för att [övervaka logg analys arbets ytan Visa](/cli/azure/monitor/log-analytics/workspace#az-monitor-log-analytics-workspace-list&preserve-view=true).

8. Spara den här filen som containerSolutionParams.jspå en lokal mapp.

9. Nu är det dags att distribuera den här mallen.

   - Om du vill distribuera med Azure PowerShell använder du följande kommandon i mappen som innehåller mallen:

       ```powershell
       # configure and login to the cloud of Log Analytics workspace.Specify the corresponding cloud environment of your workspace to below command.
       Connect-AzureRmAccount -Environment <AzureCloud | AzureChinaCloud | AzureUSGovernment>
       ```

       ```powershell
       # set the context of the subscription of Log Analytics workspace
       Set-AzureRmContext -SubscriptionId <subscription Id of Log Analytics workspace>
       ```

       ```powershell
       # execute deployment command to add Container Insights solution to the specified Log Analytics workspace
       New-AzureRmResourceGroupDeployment -Name OnboardCluster -ResourceGroupName <resource group of Log Analytics workspace> -TemplateFile .\containerSolution.json -TemplateParameterFile .\containerSolutionParams.json
       ```

       Konfigurations ändringen kan ta några minuter att slutföra. När det är klart visas ett meddelande som liknar följande och som innehåller resultatet:

       ```powershell
       provisioningState       : Succeeded
       ```

   - Om du vill distribuera med Azure CLI kör du följande kommandon:

       ```azurecli
       az login
       az account set --name <AzureCloud | AzureChinaCloud | AzureUSGovernment>
       az login
       az account set --subscription "Subscription Name"
       # execute deployment command to add container insights solution to the specified Log Analytics workspace
       az deployment group create --resource-group <resource group of log analytics workspace> --name <deployment name> --template-file  ./containerSolution.json --parameters @./containerSolutionParams.json
       ```

       Konfigurations ändringen kan ta några minuter att slutföra. När det är klart visas ett meddelande som liknar följande och som innehåller resultatet:

       ```azurecli
       provisioningState       : Succeeded
       ```

       När du har aktiverat övervakning kan det ta ungefär 15 minuter innan du kan visa hälso mått för klustret.

## <a name="install-the-helm-chart"></a>Installera HELM-diagrammet

I det här avsnittet installerar du behållarens agent för behållar insikter. Innan du fortsätter måste du identifiera det arbetsyte-ID som krävs för `omsagent.secret.wsid` parametern och den primära nyckel som krävs för `omsagent.secret.key` parametern. Du kan identifiera den här informationen genom att utföra följande steg och sedan köra kommandona för att installera agenten med hjälp av HELM-diagrammet.

1. Kör följande kommando för att identifiera arbetsyte-ID:

    `az monitor log-analytics workspace list --resource-group <resourceGroupName>`

    I utdata letar du reda på arbets ytans namn under fält **namnet** och kopierar sedan arbetsyte-ID: t för den Log Analytics arbets ytan under fältet **Kundnr**.

2. Kör följande kommando för att identifiera den primära nyckeln för arbets ytan:

    `az monitor log-analytics workspace get-shared-keys --resource-group <resourceGroupName> --workspace-name <logAnalyticsWorkspaceName>`

    Leta upp primär nyckeln under fältet **primarySharedKey** i utdata och kopiera värdet.

>[!NOTE]
>Följande kommandon gäller endast för Helm version 2. `--name`Parametern kan inte användas med Helm version 3. 

>[!NOTE]
>Om ditt Kubernetes-kluster kommunicerar via en proxyserver konfigurerar du parametern `omsagent.proxy` med URL: en för proxyservern. Om klustret inte kommunicerar via en proxyserver, behöver du inte ange den här parametern. Mer information finns i [Konfigurera proxy-slutpunkt](#configure-proxy-endpoint) längre fram i den här artikeln.

3. Lägg till Azure Charts-lagringsplatsen i din lokala lista genom att köra följande kommando:

    ```
    helm repo add microsoft https://microsoft.github.io/charts/repo
    ````

4. Installera diagrammet genom att köra följande kommando:

    ```
    $ helm install --name myrelease-1 \
    --set omsagent.secret.wsid=<logAnalyticsWorkspaceId>,omsagent.secret.key=<logAnalyticsWorkspaceKey>,omsagent.env.clusterName=<my_prod_cluster> microsoft/azuremonitor-containers
    ```

    Om arbets ytan Log Analytics är i Azure Kina 21Vianet kör du följande kommando:

    ```
    $ helm install --name myrelease-1 \
     --set omsagent.domain=opinsights.azure.cn,omsagent.secret.wsid=<logAnalyticsWorkspaceId>,omsagent.secret.key=<logAnalyticsWorkspaceKey>,omsagent.env.clusterName=<your_cluster_name> incubator/azuremonitor-containers
    ```

    Om arbets ytan Log Analytics är i Azure amerikanska myndigheter kör du följande kommando:

    ```
    $ helm install --name myrelease-1 \
    --set omsagent.domain=opinsights.azure.us,omsagent.secret.wsid=<logAnalyticsWorkspaceId>,omsagent.secret.key=<logAnalyticsWorkspaceKey>,omsagent.env.clusterName=<your_cluster_name> incubator/azuremonitor-containers
    ```

### <a name="enable-the-helm-chart-using-the-api-model"></a>Aktivera Helm-diagrammet med API-modellen

Du kan ange ett tillägg i JSON-filen för AKS-motorns kluster specifikation, även kallat API-modellen. I det här tillägget anger du den base64-kodade versionen av `WorkspaceGUID` och `WorkspaceKey` på arbets ytan Log Analytics där de insamlade övervaknings data lagras. Du kan hitta `WorkspaceGUID` och `WorkspaceKey` använda steg 1 och 2 i föregående avsnitt.

API-definitioner som stöds för Azure Stack Hub-klustret finns i det här exemplet – [kubernetes-container-monitoring_existing_workspace_id_and_key.jspå](https://github.com/Azure/aks-engine/blob/master/examples/addons/container-monitoring/kubernetes-container-monitoring_existing_workspace_id_and_key.json). Mer specifikt hittar du egenskapen **addons** i **kubernetesConfig**:

```json
"orchestratorType": "Kubernetes",
       "kubernetesConfig": {
         "addons": [
           {
             "name": "container-monitoring",
             "enabled": true,
             "config": {
               "workspaceGuid": "<Azure Log Analytics Workspace Id in Base-64 encoded>",
               "workspaceKey": "<Azure Log Analytics Workspace Key in Base-64 encoded>"
             }
           }
         ]
       }
```

## <a name="configure-agent-data-collection"></a>Konfigurera agent data insamling

Stjärnor med diagram version 1.0.0 styrs inställningarna för agent data insamlingen från ConfigMap. Läs dokumentationen om inställningarna för insamling av agent data [här](container-insights-agent-config.md).

När du har distribuerat diagrammet kan du granska data för ditt hybrid Kubernetes-kluster i behållar insikter från Azure Portal.  

>[!NOTE]
>Inmatnings fördröjningen är cirka fem till tio minuter från agenten som ska genomföras i Azure Log Analytics-arbetsytan. Status för klustret visar värdet **inga data** eller **okända** förrän alla nödvändiga övervaknings data är tillgängliga i Azure Monitor.

## <a name="configure-proxy-endpoint"></a>Konfigurera proxy-slutpunkt

Från och med diagram version 2.7.1 stöder diagrammet att du anger proxy-slutpunkten med `omsagent.proxy` diagram parametern. Detta gör det möjligt att kommunicera via proxyservern. Kommunikationen mellan container Insights-agenten och Azure Monitor kan vara en HTTP-eller HTTPS-proxyserver, och både anonym och grundläggande autentisering (användar namn/lösen ord) stöds.

Konfiguration svärdet för proxyn har följande syntax: `[protocol://][user:password@]proxyhost[:port]`

> [!NOTE]
>Om proxyservern inte kräver autentisering måste du fortfarande ange ett psuedo användar namn/lösen ord. Detta kan vara valfritt användar namn eller lösen ord.

|Egenskap| Beskrivning |
|--------|-------------|
|Protokoll | http eller https |
|användare | Valfritt användar namn för proxyautentisering |
|password | Valfritt lösen ord för proxyautentisering |
|proxyhost | Adress eller FQDN för proxyservern |
|port | Valfritt port nummer för proxyservern |

Exempelvis: `omsagent.proxy=http://user01:password@proxy01.contoso.com:8080`

Om du anger protokollet som **http** skapas HTTP-begäranden med hjälp av SSL/TLS-säker anslutning. Proxyservern måste ha stöd för SSL/TLS-protokoll.

## <a name="troubleshooting"></a>Felsökning

Om det uppstår ett fel vid försök att aktivera övervakning för ditt hybrid Kubernetes-kluster, kopierar du PowerShell-skriptet [TroubleshootError_nonAzureK8s.ps1](https://aka.ms/troubleshoot-non-azure-k8s) och sparar det i en mapp på datorn. Det här skriptet används för att identifiera och åtgärda de problem som uppstått. Problemen som är utformade för att identifiera och försöka korrigera är följande:

- Den angivna Log Analyticss arbets ytan är giltig
- Log Analytics arbets ytan konfigureras med container Insights-lösningen. Annars konfigurerar du arbets ytan.
- OmsAgent REPLICASET-poddar körs
- OmsAgent daemonset-poddar körs
- OmsAgent-tjänsten för hälso tillstånd körs
- Log Analytics arbetsyte-ID och nyckel som kon figurer ATS på behållarens agent matchar i arbets ytan som insikten är konfigurerad med.
- Verifiera att alla Linux Worker-noder har `kubernetes.io/role=agent` etikett att schemalägga RS-pod. Lägg till den om den inte finns.
- Verifiera `cAdvisor secure port:10250` eller `unsecure port: 10255` har öppnats på alla noder i klustret.

Om du vill köra med Azure PowerShell använder du följande kommandon i mappen som innehåller skriptet:

```powershell
.\TroubleshootError_nonAzureK8s.ps1 - azureLogAnalyticsWorkspaceResourceId </subscriptions/<subscriptionId>/resourceGroups/<resourcegroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName> -kubeConfig <kubeConfigFile> -clusterContextInKubeconfig <clusterContext>
```

## <a name="next-steps"></a>Nästa steg

När övervakning har Aktiver ATS för att samla in hälso-och resursutnyttjande för ditt hybrid Kubernetes-kluster och arbets belastningar som körs på dem, lär [du dig hur du använder](container-insights-analyze.md) behållar insikter.