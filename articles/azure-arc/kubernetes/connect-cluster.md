---
title: Ansluta ett Azure Arc-aktiverat Kubernetes-kluster (förhandsversion)
services: azure-arc
ms.service: azure-arc
ms.date: 05/19/2020
ms.topic: article
author: mlearned
ms.author: mlearned
description: Anslut ett Azure Arc-aktiverat Kubernetes-kluster med Azure Arc
keywords: Kubernetes, båge, Azure, K8s, behållare
ms.custom: references_regions, devx-track-azurecli
ms.openlocfilehash: b4ab84153eaaf81c668d8589fec7516853aca5f9
ms.sourcegitcommit: 49ea056bbb5957b5443f035d28c1d8f84f5a407b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/09/2021
ms.locfileid: "100008119"
---
# <a name="connect-an-azure-arc-enabled-kubernetes-cluster-preview"></a>Ansluta ett Azure Arc-aktiverat Kubernetes-kluster (förhandsversion)

Den här artikeln beskriver processen för att ansluta alla CNCF-certifierade Kubernetes-kluster, till exempel AKS-motor på Azure, AKS-motorn på Azure Stack Hub, GKE, EKS och VMware vSphere Cluster till Azure Arc.

## <a name="before-you-begin"></a>Innan du börjar

Kontrol lera att du har för berett följande krav:

* Ett igång Kubernetes-kluster. Om du inte har ett befintligt Kubernetes-kluster kan du använda någon av följande guider för att skapa ett test kluster:
  * Skapa ett Kubernetes-kluster med [Kubernetes i Docker (Natura)](https://kind.sigs.k8s.io/).
  * Skapa ett Kubernetes-kluster med Docker för [Mac](https://docs.docker.com/docker-for-mac/#kubernetes) eller [Windows](https://docs.docker.com/docker-for-windows/#kubernetes).
* En kubeconfig-fil för att komma åt rollen kluster och kluster administratör i klustret för distribution av Arc-aktiverade Kubernetes-agenter.
* Användaren eller tjänstens huvud namn som används med `az login` och- `az connectedk8s connect` kommandon måste ha behörigheterna Läs och skriv för resurs typen Microsoft. Kubernetes/connectedclusters. Rollen "Kubernetes kluster – Azure Arc onboarding" har dessa behörigheter och kan användas för roll tilldelningar för användarens eller tjänstens huvud namn.
* Helm 3 för onboarding av klustret med ett connectedk8s-tillägg. [Installera den senaste versionen av Helm 3](https://helm.sh/docs/intro/install) för att uppfylla det här kravet.
* Azure CLI-version 2.15 + för att installera Azure Arc-aktiverade Kubernetes CLI-tillägg. [Installera Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest&preserve-view=true) eller uppdatera till den senaste versionen.
* Installera Arc-aktiverade Kubernetes CLI-tillägg:
  
  * Installera `connectedk8s` tillägget, som hjälper dig att ansluta Kubernetes-kluster till Azure:
  
  ```azurecli
  az extension add --name connectedk8s
  ```
  
   * Installera `k8sconfiguration` tillägget:
  
  ```azurecli
  az extension add --name k8sconfiguration
  ```

  * Om du vill uppdatera tilläggen senare kör du följande kommandon:
  
  ```azurecli
  az extension update --name connectedk8s
  az extension update --name k8sconfiguration
  ```

## <a name="supported-regions"></a>Regioner som stöds

* East US
* Europa, västra

## <a name="network-requirements"></a>Nätverkskrav

Azure Arc-agenter kräver att följande protokoll/portar/utgående URL: er fungerar:

* TCP på port 443: `https://:443`
* TCP på port 9418: `git://:9418`

| Slut punkt (DNS)                                                                                               | Description                                                                                                                 |
| ------------------------------------------------------------------------------------------------------------ | --------------------------------------------------------------------------------------------------------------------------- |
| `https://management.azure.com`                                                                                 | Krävs för att agenten ska kunna ansluta till Azure och registrera klustret.                                                        |
| `https://eastus.dp.kubernetesconfiguration.azure.com`, `https://westeurope.dp.kubernetesconfiguration.azure.com` | Data planens slut punkt för agenten för att push-överföra status och hämta konfigurations information.                                      |
| `https://login.microsoftonline.com`                                                                            | Krävs för att hämta och uppdatera Azure Resource Manager tokens.                                                                                    |
| `https://mcr.microsoft.com`                                                                            | Krävs för att hämta behållar avbildningar för Azure Arc-agenter.                                                                  |
| `https://eus.his.arc.azure.com`, `https://weu.his.arc.azure.com`                                                                            |  Krävs för att hämta system tilldelade hanterade identitets certifikat.                                                                  |

## <a name="register-the-two-providers-for-azure-arc-enabled-kubernetes"></a>Registrera de två providers för Azure Arc-aktiverade Kubernetes:

```console
az provider register --namespace Microsoft.Kubernetes

az provider register --namespace Microsoft.KubernetesConfiguration
```

Registreringen är en asynkron process och kan ta cirka 10 minuter. Du kan övervaka registrerings processen med följande kommandon:

```console
az provider show -n Microsoft.Kubernetes -o table
```

```console
az provider show -n Microsoft.KubernetesConfiguration -o table
```

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

Använd en resurs grupp för att lagra metadata för klustret.

Skapa först en resurs grupp som ska innehålla den anslutna kluster resursen.

```console
az group create --name AzureArcTest -l EastUS -o table
```

**Utdataparametrar**

```console
Location    Name
----------  ------------
eastus      AzureArcTest
```

## <a name="connect-a-cluster"></a>Anslut ett kluster

Nu ska vi ansluta vårt Kubernetes-kluster till Azure med `az connectedk8s connect` :

1. Kontrol lera anslutningen till ditt Kubernetes-kluster via något av följande:
   1. `KUBECONFIG`
   1. `~/.kube/config`
   1. `--kube-config`
1. Distribuera Azure Arc-agenter för Kubernetes med Helm 3 i `azure-arc` namn rymden:

```console
az connectedk8s connect --name AzureArcTest1 --resource-group AzureArcTest
```

**Utdataparametrar**

```console
Command group 'connectedk8s' is in preview. It may be changed/removed in a future release.
Helm release deployment succeeded

{
  "aadProfile": {
    "clientAppId": "",
    "serverAppId": "",
    "tenantId": ""
  },
  "agentPublicKeyCertificate": "...",
  "agentVersion": "0.1.0",
  "id": "/subscriptions/57ac26cf-a9f0-4908-b300-9a4e9a0fb205/resourceGroups/AzureArcTest/providers/Microsoft.Kubernetes/connectedClusters/AzureArcTest1",
  "identity": {
    "principalId": null,
    "tenantId": null,
    "type": "None"
  },
  "kubernetesVersion": "v1.15.0",
  "location": "eastus",
  "name": "AzureArcTest1",
  "resourceGroup": "AzureArcTest",
  "tags": {},
  "totalNodeCount": 1,
  "type": "Microsoft.Kubernetes/connectedClusters"
}
```

## <a name="verify-connected-cluster"></a>Verifiera anslutet kluster

Använd följande kommando för att lista de anslutna klustren:

```console
az connectedk8s list -g AzureArcTest -o table
```

**Utdataparametrar**

```console
Command group 'connectedk8s' is in preview. It may be changed/removed in a future release.
Name           Location    ResourceGroup
-------------  ----------  ---------------
AzureArcTest1  eastus      AzureArcTest
```

Du kan också visa den här resursen på [Azure Portal](https://portal.azure.com/). Öppna portalen i webbläsaren och navigera till resurs gruppen och den Azure Arc-aktiverade Kubernetes-resursen baserat på de resurs namn och resurs grupp namn som användes tidigare i `az connectedk8s connect` kommandot.

> [!NOTE]
> När klustret har registrerats tar det cirka 5 till 10 minuter för klustrets metadata (kluster version, agent version, antal noder osv.) till Surface på sidan Översikt i den Azure Arc-aktiverade Kubernetes-resursen i Azure Portal.

## <a name="connect-using-an-outbound-proxy-server"></a>Anslut med en utgående proxyserver

Om klustret ligger bakom en utgående proxyserver, måste Azure CLI och de Arc-aktiverade Kubernetes-agenterna dirigera sina begär Anden via den utgående proxyservern:

1. Kontrol lera vilken version av tillägget som är `connectedk8s` installerad på datorn:

    ```console
    az -v
    ```

    Du behöver `connectedk8s` tillägget version 0.2.5 + för att ställa in agenter med utgående proxy. Om du har version 0.2.3 eller äldre på datorn följer du [uppdaterings stegen](#before-you-begin) för att hämta den senaste versionen av tillägget på din dator.

2. Ange de miljövariabler som krävs för Azure CLI för att använda den utgående proxyservern:

    * Om du använder bash kör du följande kommando med lämpliga värden:

        ```bash
        export HTTP_PROXY=<proxy-server-ip-address>:<port>
        export HTTPS_PROXY=<proxy-server-ip-address>:<port>
        export NO_PROXY=<cluster-apiserver-ip-address>:<port>
        ```

    * Om du använder PowerShell kör du följande kommando med lämpliga värden:

        ```powershell
        $Env:HTTP_PROXY = "<proxy-server-ip-address>:<port>"
        $Env:HTTPS_PROXY = "<proxy-server-ip-address>:<port>"
        $Env:NO_PROXY = "<cluster-apiserver-ip-address>:<port>"
        ```

3. Kör kommandot Connect med de angivna proxyadresser:

    ```console
    az connectedk8s connect -n <cluster-name> -g <resource-group> --proxy-https https://<proxy-server-ip-address>:<port> --proxy-http http://<proxy-server-ip-address>:<port> --proxy-skip-range <excludedIP>,<excludedCIDR> --proxy-cert <path-to-cert-file>
    ```

> [!NOTE]
> 1. Att ange `excludedCIDR` under `--proxy-skip-range` är viktigt för att säkerställa att kommunikationen i klustret inte är bruten för agenterna.
> 2. Medan `--proxy-http` , `--proxy-https` , och `--proxy-skip-range` förväntas för de flesta utgående proxyservrar, `--proxy-cert` krävs det bara om betrodda certifikat från proxyn måste matas in i det betrodda certifikat arkivet för agent poddar.
> 3. Ovanstående proxy-specifikation används för närvarande endast för båg agenter och inte för flödes poddar som används i sourceControlConfiguration. Det Arc-aktiverade Kubernetes-teamet arbetar aktivt med den här funktionen och kommer snart att vara tillgänglig.

## <a name="azure-arc-agents-for-kubernetes"></a>Azure Arc-agenter för Kubernetes

Azure Arc-aktiverade Kubernetes distribuerar några operatörer till `azure-arc` namn området. Du kan visa dessa distributioner och poddar med hjälp av:

```console
kubectl -n azure-arc get deployments,pods
```

**Utdataparametrar**

```console
NAME                                        READY      UP-TO-DATE  AVAILABLE  AGE
deployment.apps/cluster-metadata-operator     1/1             1        1      16h
deployment.apps/clusteridentityoperator       1/1             1        1      16h
deployment.apps/config-agent                  1/1             1        1      16h
deployment.apps/controller-manager            1/1             1        1      16h
deployment.apps/flux-logs-agent               1/1             1        1      16h
deployment.apps/metrics-agent                 1/1             1        1      16h
deployment.apps/resource-sync-agent           1/1             1        1      16h

NAME                                           READY    STATUS   RESTART AGE
pod/cluster-metadata-operator-7fb54d9986-g785b  2/2     Running  0       16h
pod/clusteridentityoperator-6d6678ffd4-tx8hr    3/3     Running  0       16h
pod/config-agent-544c4669f9-4th92               3/3     Running  0       16h
pod/controller-manager-fddf5c766-ftd96          3/3     Running  0       16h
pod/flux-logs-agent-7c489f57f4-mwqqv            2/2     Running  0       16h
pod/metrics-agent-58b765c8db-n5l7k              2/2     Running  0       16h
pod/resource-sync-agent-5cf85976c7-522p5        3/3     Running  0       16h
```

Azure Arc-aktiverade Kubernetes består av några agenter (operatörer) som körs i ditt kluster och som har distribuerats till `azure-arc` namn området.

| Agenter (operatörer)                                                                                               | Description                                                                                                                 |
| ------------------------------------------------------------------------------------------------------------ | --------------------------------------------------------------------------------------------------------------------------- |
| `deployment.apps/config-agent`                                                                                 | Bevakar det anslutna klustret för käll kontrollens konfigurations resurser som tillämpas på klustret och uppdaterar kompatibilitetstillstånd.                                                        |
| `deployment.apps/controller-manager` | Operatorer som dirigerar interaktioner mellan Azure båg-komponenter.                                      |
| `deployment.apps/metrics-agent`                                                                            | Samlar in prestanda värden hos andra Arc-agenter.                                                                                    |
| `deployment.apps/cluster-metadata-operator`                                                                            | Samlar in kluster-metadata, till exempel kluster version, antal noder och version av Azure Arc-agenten.                                                                  |
| `deployment.apps/resource-sync-agent`                                                                            |  Synkroniserar ovanstående klustrade metadata till Azure.                                                                  |
| `deployment.apps/clusteridentityoperator`                                                                            |  Azure Arc-aktiverade Kubernetes har för närvarande stöd för systemtilldelad identitet. `clusteridentityoperator` upprätthåller det hanterade tjänst identitets certifikatet (MSI) som används av andra agenter för kommunikation med Azure.                                                                  |
| `deployment.apps/flux-logs-agent`                                                                            |  Samlar in loggar från flödes operatörer som distribueras som en del av käll kontroll konfigurationen.                                                                  |

## <a name="delete-a-connected-cluster"></a>Ta bort ett anslutet kluster

Du kan ta bort en `Microsoft.Kubernetes/connectedcluster` resurs med hjälp av Azure CLI eller Azure Portal.


* **Ta bort med Azure CLI**: Använd följande Azure CLI-kommando för att initiera borttagning av den Azure Arc-aktiverade Kubernetes-resursen.
  ```console
  az connectedk8s delete --name AzureArcTest1 --resource-group AzureArcTest
  ```
  Det här kommandot tar bort `Microsoft.Kubernetes/connectedCluster` resursen och eventuella associerade `sourcecontrolconfiguration` resurser i Azure. Azure CLI använder `helm uninstall` för att ta bort de agenter som körs i klustret också.

* **Borttagning på Azure Portal**: om du tar bort den Azure Arc-aktiverade Kubernetes-resursen på Azure Portal tas `Microsoft.Kubernetes/connectedcluster` resursen och eventuella associerade `sourcecontrolconfiguration` resurser i Azure bort, men de agenter som körs i klustret tas *inte* bort. 

  Om du vill ta bort agenterna som körs i klustret kör du följande kommando:

  ```console
  az connectedk8s delete --name AzureArcTest1 --resource-group AzureArcTest
  ```

## <a name="next-steps"></a>Nästa steg

* [Använd GitOps i ett anslutet kluster](./use-gitops-connected-cluster.md)
* [Använd Azure Policy för att styra kluster konfigurationen](./use-azure-policy.md)
