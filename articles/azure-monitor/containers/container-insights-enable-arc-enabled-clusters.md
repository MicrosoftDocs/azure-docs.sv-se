---
title: Konfigurera Azure Arc-aktiverat Kubernetes-kluster med container Insights | Microsoft Docs
description: Den här artikeln beskriver hur du konfigurerar övervakning med behållar insikter om Azure Arc-aktiverade Kubernetes-kluster.
ms.topic: conceptual
ms.date: 09/23/2020
ms.openlocfilehash: 307f9d9928042410dc9b4443aba5c019c592980c
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/03/2021
ms.locfileid: "101711305"
---
# <a name="enable-monitoring-of-azure-arc-enabled-kubernetes-cluster"></a>Aktivera övervakning av Azure Arc-aktiverade Kubernetes-kluster

Behållar insikter ger en omfattande övervaknings upplevelse för Azure Kubernetes service (AKS) och AKS-motor kluster. Den här artikeln beskriver hur du aktiverar övervakning av Kubernetes-kluster som finns utanför Azure och som är aktiverade med Azure Arc, för att uppnå en liknande övervaknings upplevelse.

Behållar insikter kan aktive ras för en eller flera befintliga distributioner av Kubernetes med antingen ett PowerShell-eller bash-skript.

## <a name="supported-configurations"></a>Konfigurationer som stöds

Container Insights stöder övervakning av Azure Arc-aktiverade Kubernetes (för hands version) enligt beskrivningen i [översikts](container-insights-overview.md) artikeln, förutom följande funktioner:

- Real tids data (förhands granskning)

Följande stöds officiellt med container Insights:

- Versioner av Kubernetes och support policy är samma som versioner av [AKS som stöds](../../aks/supported-kubernetes-versions.md).

- Följande behållar körningar stöds: Docker, Moby och CRI-kompatibla körningar som CRI-O och container.

- Linux OS-versionen för Master-och Worker-noder som stöds är: Ubuntu (18,04 LTS och 16,04 LTS).

## <a name="prerequisites"></a>Förutsättningar

Kontrol lera att du har följande innan du börjar:

- En Log Analytics-arbetsyta.

    Behållar insikter stöder en Log Analytics arbets yta i de regioner som anges i Azure- [produkter efter region](https://azure.microsoft.com/global-infrastructure/services/?regions=all&products=monitor). Om du vill skapa en egen arbets yta kan den skapas via [Azure Resource Manager](../logs/resource-manager-workspace.md), via [PowerShell](../logs/powershell-sample-create-workspace.md?toc=%2fpowershell%2fmodule%2ftoc.json)eller i [Azure Portal](../logs/quick-create-workspace.md).

- Om du vill aktivera och komma åt funktionerna i behållar insikter måste du minst vara medlem i Azure *Contributor* -rollen i Azure-prenumerationen och en medlem i rollen [*Log Analytics Contributor*](../logs/manage-access.md#manage-access-using-azure-permissions) för den Log Analytics arbets yta som kon figurer ATS med behållar insikter.

- Du är medlem i rollen [deltagare](../../role-based-access-control/built-in-roles.md#contributor) i Azure-resursens kluster resurs.

- Om du vill visa övervaknings data är du medlem i behörighets rollen [*Log Analytics läsare*](../logs/manage-access.md#manage-access-using-azure-permissions) med den Log Analytics arbets yta som kon figurer ATS med behållar insikter.

- [Helm-klienten](https://helm.sh/docs/using_helm/) för att publicera behållar insikter-diagrammet för det angivna Kubernetes-klustret.

- Följande konfigurations information för proxy och brand väggar krävs för den behållar version av Log Analytics-agenten för Linux för att kunna kommunicera med Azure Monitor:

    |Agentresurs|Portar |
    |------|---------|
    |`*.ods.opinsights.azure.com` |Port 443 |
    |`*.oms.opinsights.azure.com` |Port 443 |
    |`*.dc.services.visualstudio.com` |Port 443 |

- Den behållare som har behållar agenten kräver Kubelet `cAdvisor secure port: 10250` eller `unsecure port :10255` öppnas på alla noder i klustret för att samla in prestanda mått. Vi rekommenderar att du konfigurerar `secure port: 10250` på Kubelet-cAdvisor om den inte redan har kon figurer ATS.

- Agent agenten kräver att följande miljövariabler anges i behållaren för att kunna kommunicera med Kubernetes-API-tjänsten i klustret för att samla in inventerings data – `KUBERNETES_SERVICE_HOST` och `KUBERNETES_PORT_443_TCP_PORT` .

    >[!IMPORTANT]
    >Den lägsta agent version som stöds för övervakning av Arc-aktiverade Kubernetes-kluster är ciprod04162020 eller senare.

- [PowerShell Core](/powershell/scripting/install/installing-powershell?view=powershell-6&preserve-view=true) krävs om du aktiverar övervakning med hjälp av PowerShell-skript metoden.

- [Bash version 4](https://www.gnu.org/software/bash/) krävs om du aktiverar övervakning med hjälp av bash-skript metoden.

## <a name="identify-workspace-resource-id"></a>Identifiera arbets ytans resurs-ID

Om du vill aktivera övervakning av klustret med PowerShell-eller bash-skriptet som du laddade ned tidigare och integrera med en befintlig Log Analytics arbets yta, utför du följande steg för att först identifiera det fullständiga resurs-ID: t för din Log Analytics-arbetsyta. Detta krävs för `workspaceResourceId` parametern när du kör kommandot för att aktivera övervaknings tillägget mot den angivna arbets ytan. Om du inte har en arbets yta att ange kan du hoppa över `workspaceResourceId` parametern och låta skriptet skapa en ny arbets yta åt dig.

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

    ```
    az resource list --resource-type Microsoft.OperationalInsights/workspaces -o json
    ```

    I utdata letar du reda på arbets ytans namn och kopierar sedan det fullständiga resurs-ID: t för den Log Analytics arbets ytan under fält **-ID: t**.

## <a name="enable-monitoring-using-powershell"></a>Aktivera övervakning med PowerShell

1. Hämta och Spara skriptet till en lokal mapp som konfigurerar klustret med övervaknings tillägget med följande kommandon:

    ```powershell
    Invoke-WebRequest https://aka.ms/enable-monitoring-powershell-script -OutFile enable-monitoring.ps1
    ```

2. Konfigurera `$azureArcClusterResourceId` variabeln genom att ange motsvarande värden för `subscriptionId` `resourceGroupName` och `clusterName` som representerar resurs-ID för din Azure Arc-aktiverade Kubernetes-klusterresurs.

    ```powershell
    $azureArcClusterResourceId = "/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.Kubernetes/connectedClusters/<clusterName>"
    ```

3. Konfigurera `$kubeContext` variabeln med **Kube-kontexten** för ditt kluster genom att köra kommandot `kubectl config get-contexts` . Om du vill använda den aktuella kontexten ställer du in värdet på `""` .

    ```powershell
    $kubeContext = "<kubeContext name of your k8s cluster>"
    ```

4. Om du vill använda befintlig Azure Monitor Log Analytics arbets yta konfigurerar du variabeln `$logAnalyticsWorkspaceResourceId` med motsvarande värde som representerar arbets ytans resurs-ID. Annars ställer du in variabeln till `""` och skriptet skapar en standard arbets yta i kluster prenumerationens standard resurs grupp om det inte redan finns en sådan i regionen. Standard arbets ytan som skapats liknar formatet *DefaultWorkspace- \<SubscriptionID> - \<Region>*.

    ```powershell
    $logAnalyticsWorkspaceResourceId = "/subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>/providers/microsoft.operationalinsights/workspaces/<workspaceName>"
    ```

5. Om ditt Arc-aktiverade Kubernetes-kluster kommunicerar via en proxyserver konfigurerar du variabeln `$proxyEndpoint` med URL: en för proxyservern. Om klustret inte kommunicerar via en proxyserver kan du ange värdet till `""` .  Mer information finns i [Konfigurera proxy-slutpunkt](#configure-proxy-endpoint) längre fram i den här artikeln.

6. Kör följande kommando för att aktivera övervakning.

    ```powershell
    .\enable-monitoring.ps1 -clusterResourceId $azureArcClusterResourceId -kubeContext $kubeContext -workspaceResourceId $logAnalyticsWorkspaceResourceId -proxyEndpoint $proxyEndpoint
    ```

När du har aktiverat övervakning kan det ta ungefär 15 minuter innan du kan visa hälso mått för klustret.

### <a name="using-service-principal"></a>Använda tjänstens huvud namn
Skriptet *enable-monitoring.ps1* använder interaktiv enhets inloggning. Om du föredrar icke-interaktiv inloggning kan du använda ett befintligt huvud namn för tjänsten eller skapa en ny som har de behörigheter som krävs enligt beskrivningen i [krav](#prerequisites). Om du vill använda tjänstens huvud namn måste du skicka $servicePrincipalClientId, $servicePrincipalClientSecret och $tenantId parametrar med värden för tjänstens huvud namn som du vill använda för att *enable-monitoring.ps1* skriptet.

```powershell
$subscriptionId = "<subscription Id of the Azure Arc connected cluster resource>"
$servicePrincipal = New-AzADServicePrincipal -Role Contributor -Scope "/subscriptions/$subscriptionId"
```

Roll tilldelningen nedan kan bara användas om du använder befintlig Log Analytics arbets yta i en annan Azure-prenumeration än den anslutna K8s-klusterresursen.

```powershell
$logAnalyticsWorkspaceResourceId = "<Azure Resource Id of the Log Analytics Workspace>" # format of the Azure Log Analytics workspace should be /subscriptions/<subId>/resourcegroups/<rgName>/providers/microsoft.operationalinsights/workspaces/<workspaceName>
New-AzRoleAssignment -RoleDefinitionName 'Log Analytics Contributor'  -ObjectId $servicePrincipal.Id -Scope  $logAnalyticsWorkspaceResourceId

$servicePrincipalClientId =  $servicePrincipal.ApplicationId.ToString()
$servicePrincipalClientSecret = [System.Net.NetworkCredential]::new("", $servicePrincipal.Secret).Password
$tenantId = (Get-AzSubscription -SubscriptionId $subscriptionId).TenantId
```

Exempel:

```powershell
.\enable-monitoring.ps1 -clusterResourceId $azureArcClusterResourceId -servicePrincipalClientId $servicePrincipalClientId -servicePrincipalClientSecret $servicePrincipalClientSecret -tenantId $tenantId -kubeContext $kubeContext -workspaceResourceId $logAnalyticsWorkspaceResourceId -proxyEndpoint $proxyEndpoint
```



## <a name="enable-using-bash-script"></a>Aktivera med bash-skript

Utför följande steg för att aktivera övervakning med det tillhandahållna bash-skriptet.

1. Hämta och Spara skriptet till en lokal mapp som konfigurerar klustret med övervaknings tillägget med följande kommandon:

    ```bash
    curl -o enable-monitoring.sh -L https://aka.ms/enable-monitoring-bash-script
    ```

2. Konfigurera `azureArcClusterResourceId` variabeln genom att ange motsvarande värden för `subscriptionId` `resourceGroupName` och `clusterName` som representerar resurs-ID för din Azure Arc-aktiverade Kubernetes-klusterresurs.

    ```bash
    export azureArcClusterResourceId="/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.Kubernetes/connectedClusters/<clusterName>"
    ```

3. Konfigurera `kubeContext` variabeln med **Kube-kontexten** för ditt kluster genom att köra kommandot `kubectl config get-contexts` . Om du vill använda den aktuella kontexten ställer du in värdet på `""` .

    ```bash
    export kubeContext="<kubeContext name of your k8s cluster>"
    ```

4. Om du vill använda befintlig Azure Monitor Log Analytics arbets yta konfigurerar du variabeln `logAnalyticsWorkspaceResourceId` med motsvarande värde som representerar arbets ytans resurs-ID. Annars ställer du in variabeln till `""` och skriptet skapar en standard arbets yta i kluster prenumerationens standard resurs grupp om det inte redan finns en sådan i regionen. Standard arbets ytan som skapats liknar formatet *DefaultWorkspace- \<SubscriptionID> - \<Region>*.

    ```bash
    export logAnalyticsWorkspaceResourceId="/subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>/providers/microsoft.operationalinsights/workspaces/<workspaceName>"
    ```

5. Om ditt Arc-aktiverade Kubernetes-kluster kommunicerar via en proxyserver konfigurerar du variabeln `proxyEndpoint` med URL: en för proxyservern. Om klustret inte kommunicerar via en proxyserver kan du ange värdet till `""` . Mer information finns i [Konfigurera proxy-slutpunkt](#configure-proxy-endpoint) längre fram i den här artikeln.

6. Om du vill aktivera övervakning av klustret finns det olika kommandon som baseras på distributions scenariot.

    Kör följande kommando för att aktivera övervakning med standard alternativ, till exempel att använda aktuell Kube-kontext, skapa en standard arbets yta Log Analytics och utan att ange en proxyserver:

    ```bash
    bash enable-monitoring.sh --resource-id $azureArcClusterResourceId
    ```

    Kör följande kommando för att skapa en standard arbets yta Log Analytics och utan att ange en proxyserver:

    ```bash
   bash enable-monitoring.sh --resource-id $azureArcClusterResourceId --kube-context $kubeContext
    ```

    Kör följande kommando för att använda en befintlig Log Analytics-arbetsyta och utan att ange en proxyserver:

    ```bash
    bash enable-monitoring.sh --resource-id $azureArcClusterResourceId --kube-context $kubeContext  --workspace-id $logAnalyticsWorkspaceResourceId
    ```

    Kör följande kommando för att använda en befintlig Log Analytics arbets yta och ange en proxyserver:

    ```bash
    bash enable-monitoring.sh --resource-id $azureArcClusterResourceId --kube-context $kubeContext  --workspace-id $logAnalyticsWorkspaceResourceId --proxy $proxyEndpoint
    ```

När du har aktiverat övervakning kan det ta ungefär 15 minuter innan du kan visa hälso mått för klustret.

### <a name="using-service-principal"></a>Använda tjänstens huvud namn
Bash-skriptets *Enable-Monitoring.sh* använder interaktiv enhets inloggning. Om du föredrar icke-interaktiv inloggning kan du använda ett befintligt huvud namn för tjänsten eller skapa en ny som har de behörigheter som krävs enligt beskrivningen i [krav](#prerequisites). Om du vill använda tjänstens huvud namn måste du skicka--klient-ID,--client-Secret och--Tenant-ID-värden för tjänstens huvud namn som du vill använda för att *Enable-Monitoring.sh* bash-skript.

```bash
subscriptionId="<subscription Id of the Azure Arc connected cluster resource>"
servicePrincipal=$(az ad sp create-for-rbac --role="Contributor" --scopes="/subscriptions/${subscriptionId}")
servicePrincipalClientId=$(echo $servicePrincipal | jq -r '.appId')
```

Roll tilldelningen nedan kan bara användas om du använder befintlig Log Analytics arbets yta i en annan Azure-prenumeration än den anslutna K8s-klusterresursen.

```bash
logAnalyticsWorkspaceResourceId="<Azure Resource Id of the Log Analytics Workspace>" # format of the Azure Log Analytics workspace should be /subscriptions/<subId>/resourcegroups/<rgName>/providers/microsoft.operationalinsights/workspaces/<workspaceName>
az role assignment create --role 'Log Analytics Contributor' --assignee $servicePrincipalClientId --scope $logAnalyticsWorkspaceResourceId

servicePrincipalClientSecret=$(echo $servicePrincipal | jq -r '.password')
tenantId=$(echo $servicePrincipal | jq -r '.tenant')
```

Exempel:

```bash
bash enable-monitoring.sh --resource-id $azureArcClusterResourceId --client-id $servicePrincipalClientId --client-secret $servicePrincipalClientSecret  --tenant-id $tenantId --kube-context $kubeContext  --workspace-id $logAnalyticsWorkspaceResourceId --proxy $proxyEndpoint
```

## <a name="configure-proxy-endpoint"></a>Konfigurera proxy-slutpunkt

Med behållarens agent för container Insights kan du konfigurera en proxy-slutpunkt så att den kan kommunicera via proxyservern. Kommunikation mellan behållarens agent och Azure Monitor kan vara en HTTP-eller HTTPS-proxyserver, och både anonym och grundläggande autentisering (användar namn/lösen ord) stöds.

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

Exempelvis: `http://user01:password@proxy01.contoso.com:3128`

Om du anger protokollet som **http** skapas HTTP-begäranden med hjälp av SSL/TLS-säker anslutning. Proxyservern måste ha stöd för SSL/TLS-protokoll.

### <a name="configure-using-powershell"></a>Konfigurera med hjälp av PowerShell

Ange användar namn och lösen ord, IP-adress eller fullständigt domän namn och port nummer för proxyservern. Exempel:

```powershell
$proxyEndpoint = https://<user>:<password>@<proxyhost>:<port>
```

### <a name="configure-using-bash"></a>Konfigurera med bash

Ange användar namn och lösen ord, IP-adress eller fullständigt domän namn och port nummer för proxyservern. Exempel:

```bash
export proxyEndpoint=https://<user>:<password>@<proxyhost>:<port>
```

## <a name="next-steps"></a>Nästa steg

- När övervakning har Aktiver ATS för att samla in hälso-och resursutnyttjande för ditt Arc-aktiverade Kubernetes-kluster och arbets belastningar som körs på dem, lär [du dig hur du använder](container-insights-analyze.md) behållar insikter.

- Som standard samlar den behållareade agenten STDOUT/stderr-behållar loggarna för alla behållare som körs i alla namn områden utom Kube-system. Om du vill konfigurera samling av behållar logg samlingar som är specifika för en viss namnrymd eller namnrymder granskar du [konfiguration av container Insights](container-insights-agent-config.md) för att konfigurera önskade inställningar för data insamling till ConfigMap-konfigurations filen.

- Om du vill ta bort och analysera Prometheus-mått från klustret kan du läsa [Konfigurera Prometheus mått-kassationing](container-insights-prometheus-integration.md)

- Information om hur du stoppar övervakningen av din ARC-aktiverad Kubernetes-kluster med container Insights finns i [så här slutar du övervaka ditt hybrid kluster](container-insights-optout-hybrid.md#how-to-stop-monitoring-on-arc-enabled-kubernetes).