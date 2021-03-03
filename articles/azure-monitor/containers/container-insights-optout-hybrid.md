---
title: Så här stoppar du övervakning av ditt hybrid Kubernetes-kluster | Microsoft Docs
description: Den här artikeln beskriver hur du kan sluta övervaka ditt hybrid Kubernetes-kluster med behållar insikter.
ms.topic: conceptual
ms.date: 06/16/2020
ms.openlocfilehash: e8708d6b860683cc96a806160ccc7c8e33949ab2
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/03/2021
ms.locfileid: "101713702"
---
# <a name="how-to-stop-monitoring-your-hybrid-cluster"></a>Så här stoppar du övervakning av ditt hybrid kluster

När du har aktiverat övervakning av Kubernetes-klustret kan du sluta övervaka klustret med behållar insikter om du inte längre vill övervaka det. Den här artikeln visar hur du kan göra detta i följande miljöer:

- AKS-motor på Azure och Azure Stack
- OpenShift, version 4 och högre
- Azure Arc-aktiverade Kubernetes (förhandsversion)

## <a name="how-to-stop-monitoring-using-helm"></a>Stoppa övervakning med Helm

Följande steg gäller för följande miljöer:

- AKS-motor på Azure och Azure Stack
- OpenShift, version 4 och högre

1. För att först identifiera Helm-diagrammets version av container Insights som är installerad på klustret, kör du följande Helm-kommando.

    ```
    helm list
    ```

    Utdata ser ut ungefär så här:

    ```
    NAME                            NAMESPACE       REVISION        UPDATED                                 STATUS          CHART                           APP VERSION
    azmon-containers-release-1      default         3               2020-04-21 15:27:24.1201959 -0700 PDT   deployed        azuremonitor-containers-2.7.0   7.0.0-1
    ```

    *azmon-containers-Release-1* representerar Helm-diagrammets version för behållar insikter.

2. Om du vill ta bort diagram versionen kör du följande Helm-kommando.

    `helm delete <releaseName>`

    Exempel:

    `helm delete azmon-containers-release-1`

    Detta tar bort versionen från klustret. Du kan kontrol lera genom att köra `helm list` kommandot:

    ```
    NAME                            NAMESPACE       REVISION        UPDATED                                 STATUS          CHART                           APP VERSION
    ```

Konfigurations ändringen kan ta några minuter att slutföra. Eftersom Helm spårar dina versioner även efter att du har tagit bort dem, kan du granska ett klusters historik och till och med ta bort en version med `helm rollback` .

## <a name="how-to-stop-monitoring-on-arc-enabled-kubernetes"></a>Så här stoppar du övervakning av Arc-aktiverade Kubernetes

### <a name="using-powershell"></a>Använda PowerShell

1. Hämta och Spara skriptet till en lokal mapp som konfigurerar klustret med övervaknings tillägget med följande kommandon:

    ```powershell
    wget https://aka.ms/disable-monitoring-powershell-script -OutFile disable-monitoring.ps1
    ```

2. Konfigurera `$azureArcClusterResourceId` variabeln genom att ange motsvarande värden för `subscriptionId` `resourceGroupName` och `clusterName` som representerar resurs-ID för din Azure Arc-aktiverade Kubernetes-klusterresurs.

    ```powershell
    $azureArcClusterResourceId = "/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.Kubernetes/connectedClusters/<clusterName>"
    ```

3. Konfigurera `$kubeContext` variabeln med **Kube-kontexten** för ditt kluster genom att köra kommandot `kubectl config get-contexts` . Om du vill använda den aktuella kontexten ställer du in värdet på `""` .

    ```powershell
    $kubeContext = "<kubeContext name of your k8s cluster>"
    ```

4. Kör följande kommando för att stoppa övervakningen av klustret.

    ```powershell
    .\disable-monitoring.ps1 -clusterResourceId $azureArcClusterResourceId -kubeContext $kubeContext
    ```

#### <a name="using-service-principal"></a>Använda tjänstens huvud namn
Skriptet *disable-monitoring.ps1* använder interaktiv enhets inloggning. Om du föredrar icke-interaktiv inloggning kan du använda ett befintligt huvud namn för tjänsten eller skapa en ny som har de behörigheter som krävs enligt beskrivningen i [krav](container-insights-enable-arc-enabled-clusters.md#prerequisites). Om du vill använda tjänstens huvud namn måste du skicka $servicePrincipalClientId, $servicePrincipalClientSecret och $tenantId parametrar med värden för tjänstens huvud namn som du vill använda för att enable-monitoring.ps1 skriptet.

```powershell
$subscriptionId = "<subscription Id of the Azure Arc connected cluster resource>"
$servicePrincipal = New-AzADServicePrincipal -Role Contributor -Scope "/subscriptions/$subscriptionId"

$servicePrincipalClientId =  $servicePrincipal.ApplicationId.ToString()
$servicePrincipalClientSecret = [System.Net.NetworkCredential]::new("", $servicePrincipal.Secret).Password
$tenantId = (Get-AzSubscription -SubscriptionId $subscriptionId).TenantId
```

Exempel:

```powershell
\disable-monitoring.ps1 -clusterResourceId $azureArcClusterResourceId -kubeContext $kubeContext -servicePrincipalClientId $servicePrincipalClientId -servicePrincipalClientSecret $servicePrincipalClientSecret -tenantId $tenantId
```


### <a name="using-bash"></a>Använda bash

1. Hämta och Spara skriptet till en lokal mapp som konfigurerar klustret med övervaknings tillägget med följande kommandon:

    ```bash
    curl -o disable-monitoring.sh -L https://aka.ms/disable-monitoring-bash-script
    ```

2. Konfigurera `azureArcClusterResourceId` variabeln genom att ange motsvarande värden för `subscriptionId` `resourceGroupName` och `clusterName` som representerar resurs-ID för din Azure Arc-aktiverade Kubernetes-klusterresurs.

    ```bash
    export azureArcClusterResourceId="/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.Kubernetes/connectedClusters/<clusterName>"
    ```

3. Konfigurera `kubeContext` variabeln med **Kube-kontexten** för ditt kluster genom att köra kommandot `kubectl config get-contexts` .

    ```bash
    export kubeContext="<kubeContext name of your k8s cluster>"
    ```

4. Om du vill stoppa övervakningen av klustret finns det olika kommandon som baseras på distributions scenariot.

    Kör följande kommando för att stoppa övervakningen av klustret med hjälp av den aktuella kontexten.

    ```bash
    bash disable-monitoring.sh --resource-id $azureArcClusterResourceId
    ```

    Kör följande kommando för att stoppa övervakningen av klustret genom att ange en kontext

    ```bash
    bash disable-monitoring.sh --resource-id $azureArcClusterResourceId --kube-context $kubeContext
    ```

#### <a name="using-service-principal"></a>Använda tjänstens huvud namn
Bash-skriptets *disable-Monitoring.sh* använder interaktiv enhets inloggning. Om du föredrar icke-interaktiv inloggning kan du använda ett befintligt huvud namn för tjänsten eller skapa en ny som har de behörigheter som krävs enligt beskrivningen i [krav](container-insights-enable-arc-enabled-clusters.md#prerequisites). Om du vill använda tjänstens huvud namn måste du skicka--klient-ID,--client-Secret och--Tenant-ID-värden för tjänstens huvud namn som du vill använda för att *Enable-Monitoring.sh* bash-skript.

```bash
subscriptionId="<subscription Id of the Azure Arc connected cluster resource>"
servicePrincipal=$(az ad sp create-for-rbac --role="Contributor" --scopes="/subscriptions/${subscriptionId}")
servicePrincipalClientId=$(echo $servicePrincipal | jq -r '.appId')

servicePrincipalClientSecret=$(echo $servicePrincipal | jq -r '.password')
tenantId=$(echo $servicePrincipal | jq -r '.tenant')
```

Exempel:

```bash
bash disable-monitoring.sh --resource-id $azureArcClusterResourceId --kube-context $kubeContext --client-id $servicePrincipalClientId --client-secret $servicePrincipalClientSecret  --tenant-id $tenantId
```

## <a name="next-steps"></a>Nästa steg

Om Log Analytics arbets ytan bara har skapats för att stödja övervakning av klustret och den inte längre behövs, måste du ta bort den manuellt. Om du inte är bekant med hur du tar bort en arbets yta, se [ta bort en Azure Log Analytics-arbetsyta](../logs/delete-workspace.md).