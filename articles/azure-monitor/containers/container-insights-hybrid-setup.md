---
title: Konfigurera Kubernetes-hybridkluster med Container Insights | Microsoft Docs
description: I den här artikeln beskrivs hur du kan konfigurera containerinsikter för att övervaka Kubernetes-kluster som finns Azure Stack i en annan miljö.
ms.topic: conceptual
ms.date: 06/30/2020
ms.openlocfilehash: 90a4c14397df8e70fc8f3d88bc339f826bb1ccc9
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107767031"
---
# <a name="configure-hybrid-kubernetes-clusters-with-container-insights"></a>Konfigurera Kubernetes-hybridkluster med Container Insights

Containerinsikter ger omfattande övervakningsupplevelse för Azure Kubernetes Service (AKS) och [AKS Engine](https://github.com/Azure/aks-engine)på Azure , som är ett självstyrt Kubernetes-kluster i Azure. I den här artikeln beskrivs hur du aktiverar övervakning av Kubernetes-kluster som finns utanför Azure och uppnår en liknande övervakningsupplevelse.

## <a name="supported-configurations"></a>Konfigurationer som stöds

Följande konfigurationer stöds officiellt med Container Insights. Om du har en annan version av Kubernetes och operativsystemversioner skickar du ett e-postmeddelande till askcoin@microsoft.com .

- Miljöer:

    - Kubernetes lokalt
    - AKS Engine på Azure och Azure Stack. Mer information finns i [AKS Engine on Azure Stack](/azure-stack/user/azure-stack-kubernetes-aks-engine-overview)
    - [OpenShift](https://docs.openshift.com/container-platform/4.3/welcome/index.html) version 4 och senare, lokalt eller i andra molnmiljöer.

- Versioner av Kubernetes och supportprinciper är samma som versioner av [AKS som stöds.](../../aks/supported-kubernetes-versions.md)

- Följande containerkörningar stöds: Docker, Moby och CRI-kompatibla körningar som CRI-O och ContainerD.

- Linux OS-versionen för huvudnoder och arbetsnoder som stöds är: Ubuntu (18.04 LTS och 16.04 LTS) och Red Hat Enterprise Linux CoreOS 43.81.

- Åtkomstkontroll stöds: Kubernetes RBAC och icke-RBAC

## <a name="prerequisites"></a>Förutsättningar

Kontrollera att du har följande innan du börjar:

- En [Log Analytics-arbetsyta](../logs/design-logs-deployment.md).

    Container insights stöder en Log Analytics-arbetsyta i de regioner som anges i [Azure-produkter efter region.](https://azure.microsoft.com/global-infrastructure/services/?regions=all&products=monitor) Om du vill skapa en egen arbetsyta kan [den skapas via Azure Resource Manager](../logs/resource-manager-workspace.md), via [PowerShell](../logs/powershell-sample-create-workspace.md?toc=%2fpowershell%2fmodule%2ftoc.json)eller i [Azure Portal](../logs/quick-create-workspace.md).

    >[!NOTE]
    >Aktivera övervakning av flera kluster med samma klusternamn till samma Log Analytics-arbetsyta stöds inte. Klusternamn måste vara unika.
    >

- Du är medlem i Log **Analytics-deltagarrollen för att** aktivera övervakning av containrar. Mer information om hur du styr åtkomsten till en Log Analytics-arbetsyta finns i [Hantera åtkomst till arbetsyta och loggdata.](../logs/manage-access.md)

- Om du vill visa övervakningsdata måste du ha [*Log Analytics-läsarrollen*](../logs/manage-access.md#manage-access-using-azure-permissions) på Log Analytics-arbetsytan, konfigurerad med Container Insights.

- [HELM-klienten](https://helm.sh/docs/using_helm/) för att publicera diagrammet Container Insights för det angivna Kubernetes-klustret.

- Följande konfigurationsinformation för proxy och brandvägg krävs för att den containeriserade versionen av Log Analytics-agenten för Linux ska kunna kommunicera med Azure Monitor:

    |Agentresurs|Portar |
    |------|---------|
    |*.ods.opinsights.azure.com |Port 443 |
    |*.oms.opinsights.azure.com |Port 443 |
    |*.dc.services.visualstudio.com |Port 443 |

- Containeragenten kräver att Kubelets eller öppnas på alla noder i klustret för `cAdvisor secure port: 10250` `unsecure port :10255` att samla in prestandamått. Vi rekommenderar att du `secure port: 10250` konfigurerar på Kubelets cAdvisor om det inte redan har konfigurerats.

- Containeragenten kräver att följande miljövariabler anges i containern för att kommunicera med Kubernetes API-tjänsten i klustret för att samla in inventeringsdata – `KUBERNETES_SERVICE_HOST` och `KUBERNETES_PORT_443_TCP_PORT` .

>[!IMPORTANT]
>Den lägsta agentversion som stöds för övervakning av Kubernetes-hybridkluster är ciprod10182019 eller senare.

## <a name="enable-monitoring"></a>Aktivera övervakning

Aktivering av Container Insights för Kubernetes-hybridklustret består av att utföra följande steg i ordning.

1. Konfigurera Log Analytics-arbetsytan med Container Insights-lösningen.   

2. Aktivera HELM-diagrammet för Container Insights med Log Analytics-arbetsytan.

Mer information om övervakningslösningar i Azure Monitor [finns här.](../../azure-monitor/insights/solutions.md)

### <a name="how-to-add-the-azure-monitor-containers-solution"></a>Så här lägger du till Azure Monitor Containers-lösningen

Du kan distribuera lösningen med den Azure Resource Manager mallen med hjälp av Azure PowerShell-cmdleten `New-AzResourceGroupDeployment` eller med Azure CLI.

Om du inte är bekant med begreppet att distribuera resurser med hjälp av en mall kan du se:

- [Distribuera resurser med Resource Manager-mallar och Azure PowerShell](../../azure-resource-manager/templates/deploy-powershell.md)

- [Distribuera resurser med Resource Manager-mallar och Azure CLI](../../azure-resource-manager/templates/deploy-cli.md)

Om du väljer att använda Azure CLI måste du först installera och använda CLI lokalt. Du måste köra Azure CLI version 2.0.59 eller senare. Kör för att identifiera din `az --version` version. Om du behöver installera eller uppgradera Azure CLI kan du gå [till Installera Azure CLI.](/cli/azure/install-azure-cli)

Den här metoden innehåller två JSON-mallar. En mall anger konfigurationen för att aktivera övervakning och den andra innehåller parametervärden som du konfigurerar för att ange följande:

- **workspaceResourceId** – det fullständiga resurs-ID:t för Log Analytics-arbetsytan.
- **workspaceRegion** – den region som arbetsytan skapas i,  vilket även kallas Plats i arbetsytans egenskaper vid visning från Azure Portal.

För att först identifiera det fullständiga resurs-ID:t för Log Analytics-arbetsytan som krävs för parametervärdet icontainerSolutionParams.json-filen utför du följande steg och kör sedan `workspaceResourceId` PowerShell-cmdleten eller Azure CLI-kommandot för att lägga till lösningen. 

1. Visa en lista över alla prenumerationer som du har åtkomst till med hjälp av följande kommando:

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

2. Växla till den prenumeration som är värd för Log Analytics-arbetsytan med följande kommando:

    ```azurecli
    az account set -s <subscriptionId of the workspace>
    ```

3. I följande exempel visas listan över arbetsytor i dina prenumerationer i JSON-standardformatet.

    ```azurecli
    az resource list --resource-type Microsoft.OperationalInsights/workspaces -o json
    ```

    Leta reda på arbetsytans namn i utdata och kopiera sedan det fullständiga resurs-ID:t för Log Analytics-arbetsytan under fältet **ID**.

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

5. Spara den här filen containerSolution.jstill en lokal mapp.

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

7. Redigera värdena för **workspaceResourceId** med det värde som du kopierade i steg 3, och för **workspaceRegion** kopierar du **regionvärdet** när du har kört Azure [CLI-kommandot az monitor log-analytics workspace show](/cli/azure/monitor/log-analytics/workspace#az_monitor-log-analytics-workspace-list&preserve-view=true).

8. Spara den här filen containerSolutionParams.jstill en lokal mapp.

9. Nu är det dags att distribuera den här mallen.

   - Om du vill Azure PowerShell med hjälp av följande kommandon i mappen som innehåller mallen:

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

       Konfigurationsändringen kan ta några minuter att slutföra. När det är klart visas ett meddelande som liknar följande och som innehåller resultatet:

       ```powershell
       provisioningState       : Succeeded
       ```

   - Kör följande kommandon för att distribuera med Azure CLI:

       ```azurecli
       az login
       az account set --name <AzureCloud | AzureChinaCloud | AzureUSGovernment>
       az login
       az account set --subscription "Subscription Name"
       # execute deployment command to add container insights solution to the specified Log Analytics workspace
       az deployment group create --resource-group <resource group of log analytics workspace> --name <deployment name> --template-file  ./containerSolution.json --parameters @./containerSolutionParams.json
       ```

       Konfigurationsändringen kan ta några minuter att slutföra. När det är klart visas ett meddelande som liknar följande och som innehåller resultatet:

       ```azurecli
       provisioningState       : Succeeded
       ```

       När du har aktiverat övervakning kan det ta ungefär 15 minuter innan du kan visa hälsomått för klustret.

## <a name="install-the-helm-chart"></a>Installera HELM-diagrammet

I det här avsnittet installerar du den containeriserade agenten för Container Insights. Innan du fortsätter måste du identifiera det arbetsyte-ID som krävs för `omsagent.secret.wsid` parametern och primärnyckeln som krävs för `omsagent.secret.key` parametern. Du kan identifiera den här informationen genom att utföra följande steg och sedan köra kommandona för att installera agenten med hjälp av HELM-diagrammet.

1. Kör följande kommando för att identifiera arbetsytans ID:

    `az monitor log-analytics workspace list --resource-group <resourceGroupName>`

    Leta reda på arbetsytans namn under fältnamnet i utdata och kopiera sedan arbetsyte-ID:t för den Log Analytics-arbetsytan under fältet **customerID**.

2. Kör följande kommando för att identifiera den primära nyckeln för arbetsytan:

    `az monitor log-analytics workspace get-shared-keys --resource-group <resourceGroupName> --workspace-name <logAnalyticsWorkspaceName>`

    Leta reda på primärnyckeln under fältet **primarySharedKey** i utdata och kopiera sedan värdet.

>[!NOTE]
>Följande kommandon gäller endast för Helm version 2. Parametern `--name` kan inte användas med Helm version 3. 

>[!NOTE]
>Om Kubernetes-klustret kommunicerar via en proxyserver konfigurerar du `omsagent.proxy` parametern med URL:en för proxyservern. Om klustret inte kommunicerar via en proxyserver behöver du inte ange den här parametern. Mer information finns i Konfigurera [proxyslutpunkt senare](#configure-proxy-endpoint) i den här artikeln.

3. Lägg till lagringsplatsen för Azure-diagram i din lokala lista genom att köra följande kommando:

    ```
    helm repo add microsoft https://microsoft.github.io/charts/repo
    ````

4. Installera diagrammet genom att köra följande kommando:

    ```
    $ helm install --name myrelease-1 \
    --set omsagent.secret.wsid=<logAnalyticsWorkspaceId>,omsagent.secret.key=<logAnalyticsWorkspaceKey>,omsagent.env.clusterName=<my_prod_cluster> microsoft/azuremonitor-containers
    ```

    Om Log Analytics-arbetsytan finns Azure China 21Vianet kör du följande kommando:

    ```
    $ helm install --name myrelease-1 \
     --set omsagent.domain=opinsights.azure.cn,omsagent.secret.wsid=<logAnalyticsWorkspaceId>,omsagent.secret.key=<logAnalyticsWorkspaceKey>,omsagent.env.clusterName=<your_cluster_name> incubator/azuremonitor-containers
    ```

    Om Log Analytics-arbetsytan finns i Azure US Government kör du följande kommando:

    ```
    $ helm install --name myrelease-1 \
    --set omsagent.domain=opinsights.azure.us,omsagent.secret.wsid=<logAnalyticsWorkspaceId>,omsagent.secret.key=<logAnalyticsWorkspaceKey>,omsagent.env.clusterName=<your_cluster_name> incubator/azuremonitor-containers
    ```

### <a name="enable-the-helm-chart-using-the-api-model"></a>Aktivera Helm-diagrammet med API-modellen

Du kan ange ett tillägg i JSON-filen för AKS Engine-klusterspecifikation, som även kallas API-modell. I det här tillägget anger du den base64-kodade versionen av och för Log Analytics-arbetsytan där `WorkspaceGUID` `WorkspaceKey` insamlade övervakningsdata lagras. Du hittar och `WorkspaceGUID` med hjälp av steg `WorkspaceKey` 1 och 2 i föregående avsnitt.

API-definitioner som stöds för Azure Stack Hub klustret finns i det här exempletkubernetes-container-monitoring_existing_workspace_id_and_key.js[ på](https://github.com/Azure/aks-engine/blob/master/examples/addons/container-monitoring/kubernetes-container-monitoring_existing_workspace_id_and_key.json). Mer specifikt hittar du **egenskapen addons** i **kubernetesConfig**:

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

## <a name="configure-agent-data-collection"></a>Konfigurera insamling av agentdata

När du använder diagramversion 1.0.0 styrs agentens datainsamlingsinställningar från ConfigMap. Läs dokumentationen om inställningar för agentdatainsamling [här.](container-insights-agent-config.md)

När du har distribuerat diagrammet kan du granska data för kubernetes-hybridklustret i Container Insights från Azure Portal.  

>[!NOTE]
>Datainmatningssvarstid är cirka fem till tio minuter från agenten för att genomföra i Azure Log Analytics-arbetsytan. Status för klustret visar värdet Inga **data eller** **Okänd förrän** alla nödvändiga övervakningsdata är tillgängliga i Azure Monitor.

## <a name="configure-proxy-endpoint"></a>Konfigurera proxyslutpunkt

Från och med diagramversion 2.7.1 stöder diagrammet att proxyslutpunkten anges med `omsagent.proxy` diagramparametern. På så sätt kan den kommunicera via proxyservern. Kommunikationen mellan Container Insights-agenten och Azure Monitor kan vara en HTTP- eller HTTPS-proxyserver, och både anonym och grundläggande autentisering (användarnamn/lösenord) stöds.

Proxykonfigurationsvärdet har följande syntax: `[protocol://][user:password@]proxyhost[:port]`

> [!NOTE]
>Om proxyservern inte kräver autentisering måste du fortfarande ange ett psuedo-användarnamn/lösenord. Det kan vara val annat användarnamn eller lösenord.

|Egenskap| Beskrivning |
|--------|-------------|
|Protokoll | http eller https |
|användare | Valfritt användarnamn för proxyautentisering |
|password | Valfritt lösenord för proxyautentisering |
|proxyhost | Adress eller FQDN för proxyservern |
|port | Valfritt portnummer för proxyservern |

Exempelvis: `omsagent.proxy=http://user01:password@proxy01.contoso.com:8080`

Om du anger protokollet som **http** skapas HTTP-begäranden med ssl/TLS säker anslutning. Proxyservern måste ha stöd för SSL/TLS-protokoll.

## <a name="troubleshooting"></a>Felsökning

Om du stöter på ett fel när du försöker aktivera övervakning för kubernetes-hybridklustret kopierar du PowerShell-skriptet [TroubleshootError_nonAzureK8s.ps1](https://aka.ms/troubleshoot-non-azure-k8s) och sparar det i en mapp på datorn. Det här skriptet tillhandahålls för att identifiera och åtgärda de problem som påträffas. De problem som den är utformad för att identifiera och försöka korrigera är följande:

- Den angivna Log Analytics-arbetsytan är giltig
- Log Analytics-arbetsytan konfigureras med lösningen Container Insights. Om inte konfigurerar du arbetsytan.
- OmsAgent replicaset-poddar körs
- OmsAgent daemonset-poddar körs
- OmsAgent Health-tjänsten körs
- Log Analytics-arbetsytans ID och nyckel som konfigurerats på den containeriserade agenten matchar arbetsytan som Insight har konfigurerats med.
- Kontrollera att alla Linux-arbetsnoder har `kubernetes.io/role=agent` en etikett för att schemalägga rs-podden. Om den inte finns lägger du till den.
- Verifiera `cAdvisor secure port:10250` eller öppnas på alla noder i `unsecure port: 10255` klustret.

Kör med Azure PowerShell genom att använda följande kommandon i mappen som innehåller skriptet:

```powershell
.\TroubleshootError_nonAzureK8s.ps1 - azureLogAnalyticsWorkspaceResourceId </subscriptions/<subscriptionId>/resourceGroups/<resourcegroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName> -kubeConfig <kubeConfigFile> -clusterContextInKubeconfig <clusterContext>
```

## <a name="next-steps"></a>Nästa steg

När övervakning har aktiverats för att samla in hälsotillstånd och resursanvändning för kubernetes-hybridklustret och arbetsbelastningar som körs på dem kan du lära dig hur [du använder](container-insights-analyze.md) Container Insights.