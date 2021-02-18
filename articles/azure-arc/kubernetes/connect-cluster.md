---
title: Ansluta ett Azure Arc-aktiverat Kubernetes-kluster (för hands version)
services: azure-arc
ms.service: azure-arc
ms.date: 02/17/2021
ms.topic: article
author: mlearned
ms.author: mlearned
description: Anslut ett Azure Arc-aktiverat Kubernetes-kluster med Azure Arc
keywords: Kubernetes, båge, Azure, K8s, behållare
ms.custom: references_regions, devx-track-azurecli
ms.openlocfilehash: 876bc15a3f4db1d12afec37c69656c431e5e6773
ms.sourcegitcommit: 227b9a1c120cd01f7a39479f20f883e75d86f062
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/18/2021
ms.locfileid: "100652521"
---
# <a name="connect-an-azure-arc-enabled-kubernetes-cluster-preview"></a>Ansluta ett Azure Arc-aktiverat Kubernetes-kluster (för hands version)

Den här artikeln vägleder dig genom att ansluta ett befintligt Kubernetes-kluster till Azure-bågen. För ett koncept som tar på att ansluta kluster, se [artikeln Azure Arc Enabled Kubernetes agent Architecture](./conceptual-agent-architecture.md).

## <a name="before-you-begin"></a>Innan du börjar

Kontrol lera att du har för berett följande krav:

* Ett igång Kubernetes-kluster. Om du inte har ett befintligt Kubernetes-kluster kan du använda någon av följande guider för att skapa ett test kluster:
  * Skapa ett Kubernetes-kluster med [Kubernetes i Docker (Natura)](https://kind.sigs.k8s.io/).
  * Skapa ett Kubernetes-kluster med Docker för [Mac](https://docs.docker.com/docker-for-mac/#kubernetes) eller [Windows](https://docs.docker.com/docker-for-windows/#kubernetes).
* En kubeconfig-fil för att komma åt rollen kluster och kluster administratör i klustret för distribution av Arc-aktiverade Kubernetes-agenter.
* Läs-och skriv behörigheter för användaren eller tjänstens huvud namn som används med `az login` och- `az connectedk8s connect` kommandon i `Microsoft.Kubernetes/connectedclusters` resurs typen. Rollen "Kubernetes kluster – Azure Arc onboarding" har dessa behörigheter och kan användas för roll tilldelningar för användarens eller tjänstens huvud namn.
* Helm 3 för onboarding av klustret med hjälp av ett `connectedk8s` tillägg. [Installera den senaste versionen av Helm 3](https://helm.sh/docs/intro/install) för att uppfylla det här kravet.
* Azure CLI-version 2.15 + för att installera Azure Arc-aktiverade Kubernetes CLI-tillägg. [Installera Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest&preserve-view=true) eller uppdatera till den senaste versionen.
* Azure-bågen aktiverade Kubernetes CLI-tillägg:
  
  * Installera `connectedk8s` tillägget så att du kan ansluta Kubernetes-kluster till Azure:
  
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
| `https://eus.his.arc.azure.com`, `https://weu.his.arc.azure.com`                                                                            |  Krävs för att hämta system tilldelade Hanterad tjänstidentitet-certifikat (MSI).                                                                  |

## <a name="register-the-two-providers-for-azure-arc-enabled-kubernetes"></a>Registrera de två providers för Azure Arc-aktiverade Kubernetes

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
   * `KUBECONFIG`
   * `~/.kube/config`
   * `--kube-config`
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
  "agentPublicKeyCertificate": "xxxxxxxxxxxxxxxxxxx",
  "agentVersion": null,
  "connectivityStatus": "Connecting",
  "distribution": "gke",
  "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/AzureArcTest/providers/Microsoft.Kubernetes/connectedClusters/AzureArcTest1",
  "identity": {
    "principalId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "tenantId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "type": "SystemAssigned"
  },
  "infrastructure": "gcp",
  "kubernetesVersion": null,
  "lastConnectivityTime": null,
  "location": "eastus",
  "managedIdentityCertificateExpirationTime": null,
  "name": "AzureArcTest1",
  "offering": null,
  "provisioningState": "Succeeded",
  "resourceGroup": "AzureArcTest",
  "tags": {},
  "totalCoreCount": null,
  "totalNodeCount": null,
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

Du kan också visa den här resursen på [Azure Portal](https://portal.azure.com/).
1. Öppna portalen i webbläsaren.
1. Navigera till resurs gruppen och den Azure Arc-aktiverade Kubernetes-resursen baserat på de resurs namn och resurs grupp namn som användes tidigare i `az connectedk8s connect` kommandot.  
> [!NOTE]
> När klustret har registrerats tar det cirka 5 till 10 minuter för klustrets metadata (kluster version, agent version, antal noder osv.) till yta på sidan Översikt i den Azure Arc-aktiverade Kubernetes-resursen i Azure Portal.

## <a name="connect-using-an-outbound-proxy-server"></a>Anslut med en utgående proxyserver

Om klustret ligger bakom en utgående proxyserver, måste Azure CLI och Azure-bågen aktiverade Kubernetes-agenter dirigera sina begär Anden via den utgående proxyservern:

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
> * Att ange `excludedCIDR` under `--proxy-skip-range` är viktigt för att säkerställa att kommunikationen i klustret inte är bruten för agenterna.
> * Medan `--proxy-http` , `--proxy-https` , och `--proxy-skip-range` förväntas för de flesta utgående proxyservrar, `--proxy-cert` krävs det bara om betrodda certifikat från proxyn måste matas in i det betrodda certifikat arkivet för agent poddar.
> * Ovanstående proxy-specifikation används för närvarande endast för Azure-båg agenter och inte för flödes poddar som används i `sourceControlConfiguration` . Azure-Arc-aktiverad Kubernetes-teamet arbetar aktivt med den här funktionen.

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

Kontrol lera att alla poddar är i ett `Running` tillstånd.

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
