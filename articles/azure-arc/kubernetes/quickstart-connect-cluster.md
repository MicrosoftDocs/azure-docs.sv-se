---
title: 'Snabb start: ansluta ett befintligt Kubernetes-kluster till Azure-bågen'
description: I den här snabb starten får du lära dig hur du ansluter ett Azure Arc-aktiverat Kubernetes-kluster.
author: mgoedtel
ms.author: magoedte
ms.service: azure-arc
ms.topic: quickstart
ms.date: 03/03/2021
ms.custom: template-quickstart, references_regions
keywords: Kubernetes, båge, Azure, kluster
ms.openlocfilehash: b4cbd45f8478674c7c6bacc50f068bc0ec691a14
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2021
ms.locfileid: "106449927"
---
# <a name="quickstart-connect-an-existing-kubernetes-cluster-to-azure-arc"></a>Snabb start: ansluta ett befintligt Kubernetes-kluster till Azure-bågen 

I den här snabb starten ska vi dra nytta av fördelarna med Azure Arc-aktiverade Kubernetes och ansluta ett befintligt Kubernetes-kluster till Azure-bågen. För ett koncept som tar på att ansluta kluster till Azure Arc, se [artikeln Azure Arc Enabled Kubernetes agent Architecture](./conceptual-agent-architecture.md).

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

* Ett igång Kubernetes-kluster. Om du inte har ett kan du skapa ett kluster med något av följande alternativ:
    * [Kubernetes i Docker (Natura)](https://kind.sigs.k8s.io/)
    * Skapa ett Kubernetes-kluster med Docker för [Mac](https://docs.docker.com/docker-for-mac/#kubernetes) eller [Windows](https://docs.docker.com/docker-for-windows/#kubernetes)
    * Självhanterat Kubernetes-kluster med hjälp av [kluster-API](https://cluster-api.sigs.k8s.io/user/quick-start.html)

    >[!NOTE]
    > Klustret måste ha minst en nod av operativ system och arkitektur typ `linux/amd64` . Kluster med endast `linux/arm64` noder stöds inte än.
    
* En `kubeconfig` fil och kontext som pekar på klustret.
* Läs-och Skriv behörighet för Azure-bågen aktiverade Kubernetes resurs typ ( `Microsoft.Kubernetes/connectedClusters` ).

* Installera den [senaste versionen av Helm 3](https://helm.sh/docs/intro/install).

- [Installera eller uppgradera Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) till version >= 2.16.0
* Installera `connectedk8s` Azure CLI-tillägget för version >= 1.0.0:
  
  ```azurecli
  az extension add --name connectedk8s
  ```

>[!TIP]
> Om `connectedk8s` tillägget redan är installerat uppdaterar du det till den senaste versionen med hjälp av följande kommando: `az extension update --name connectedk8s`


>[!NOTE]
>Listan över regioner som stöds av Azure Arc-aktiverade Kubernetes finns [här](https://azure.microsoft.com/global-infrastructure/services/?products=azure-arc).

>[!NOTE]
> Om du vill använda anpassade platser i klustret kan du använda regionerna östra USA eller Västeuropa för att ansluta klustret som anpassade platser är bara tillgängligt i dessa regioner från och med nu. Alla andra Azure Arc-aktiverade Kubernetes-funktioner är tillgängliga i alla regioner som anges ovan.

## <a name="meet-network-requirements"></a>Uppfylla nätverks kraven

>[!IMPORTANT]
>Azure Arc-agenter kräver att följande protokoll/portar/utgående URL: er fungerar:
>* TCP på port 443: `https://:443`
>* TCP på port 9418: `git://:9418`
  
| Slut punkt (DNS) | Beskrivning |  
| ----------------- | ------------- |  
| `https://management.azure.com`                                                                                 | Krävs för att agenten ska kunna ansluta till Azure och registrera klustret.                                                        |  
| `https://<region>.dp.kubernetesconfiguration.azure.com` | Data planens slut punkt för agenten för att push-överföra status och hämta konfigurations information.                                      |  
| `https://login.microsoftonline.com`                                                                            | Krävs för att hämta och uppdatera Azure Resource Manager tokens.                                                                                    |  
| `https://mcr.microsoft.com`                                                                            | Krävs för att hämta behållar avbildningar för Azure Arc-agenter.                                                                  |  
| `https://eus.his.arc.azure.com`, `https://weu.his.arc.azure.com`, `https://wcus.his.arc.azure.com`, `https://scus.his.arc.azure.com`, `https://sea.his.arc.azure.com`, `https://uks.his.arc.azure.com`, `https://wus2.his.arc.azure.com`, `https://ae.his.arc.azure.com`, `https://eus2.his.arc.azure.com`, `https://ne.his.arc.azure.com` |  Krävs för att hämta system tilldelade Hanterad tjänstidentitet-certifikat (MSI).                                                                  |

## <a name="register-the-two-providers-for-azure-arc-enabled-kubernetes"></a>Registrera de två providers för Azure Arc-aktiverade Kubernetes

1. Ange följande kommandon:
    ```azurecli
    az provider register --namespace Microsoft.Kubernetes
    az provider register --namespace Microsoft.KubernetesConfiguration
    az provider register --namespace Microsoft.ExtendedLocation
    ```
2. Övervaka registrerings processen. Registreringen kan ta upp till 10 minuter.
    ```azurecli
    az provider show -n Microsoft.Kubernetes -o table
    az provider show -n Microsoft.KubernetesConfiguration -o table
    az provider show -n Microsoft.ExtendedLocation -o table
    ```

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

Skapa en resursgrupp:  

```console
az group create --name AzureArcTest -l EastUS -o table
```

```output
Location    Name
----------  ------------
eastus      AzureArcTest
```

## <a name="connect-an-existing-kubernetes-cluster"></a>Anslut ett befintligt Kubernetes-kluster

1. Anslut ditt Kubernetes-kluster till Azure-bågen med följande kommando:
    ```console
    az connectedk8s connect --name AzureArcTest1 --resource-group AzureArcTest
    ```

    ```output
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

> [!TIP]
> Kommandot ovan utan parametern location skapar den Azure Arc-aktiverade Kubernetes-resursen på samma plats som resurs gruppen. Om du vill skapa en Azure Arc-aktiverad Kubernetes-resurs på en annan plats anger `--location <region>` du antingen eller `-l <region>` när du kör `az connectedk8s connect` kommandot.

## <a name="verify-cluster-connection"></a>Verifiera kluster anslutning

Visa en lista över dina anslutna kluster med följande kommando:  

```console
az connectedk8s list -g AzureArcTest -o table
```

```output
Name           Location    ResourceGroup
-------------  ----------  ---------------
AzureArcTest1  eastus      AzureArcTest
```

> [!NOTE]
> När klustret har registrerats tar det cirka 5 till 10 minuter för klustrets metadata (kluster version, agent version, antal noder osv.) till yta på sidan Översikt i den Azure Arc-aktiverade Kubernetes-resursen i Azure Portal.

## <a name="connect-using-an-outbound-proxy-server"></a>Anslut med en utgående proxyserver

Om klustret ligger bakom en utgående proxyserver, måste Azure CLI och Azure-bågen aktiverade Kubernetes-agenter dirigera sina förfrågningar via den utgående proxyservern. 


1. Ange de miljövariabler som krävs för Azure CLI för att använda den utgående proxyservern:

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

2. Kör kommandot Connect med de angivna proxyadresser:

    ```console
    az connectedk8s connect -n <cluster-name> -g <resource-group> --proxy-https https://<proxy-server-ip-address>:<port> --proxy-http http://<proxy-server-ip-address>:<port> --proxy-skip-range <excludedIP>,<excludedCIDR> --proxy-cert <path-to-cert-file>
    ```

> [!NOTE]
> * Ange `excludedCIDR` under `--proxy-skip-range` för att säkerställa att kommunikationen i klustret inte är bruten för agenterna.
> * `--proxy-http`, `--proxy-https` och `--proxy-skip-range` förväntas för de flesta utgående proxy-miljöer. `--proxy-cert` krävs *endast* om du behöver mata in betrodda certifikat som förväntas av proxyn i det betrodda certifikat arkivet för agentens poddar.

## <a name="view-azure-arc-agents-for-kubernetes"></a>Visa Azure Arc-agenter för Kubernetes

Azure Arc-aktiverade Kubernetes distribuerar några operatörer till `azure-arc` namn området. 

1. Visa dessa distributioner och poddar med:

    ```console
    kubectl -n azure-arc get deployments,pods
    ```

1. Kontrol lera att alla poddar är i ett `Running` tillstånd.

    ```output
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

## <a name="clean-up-resources"></a>Rensa resurser

Du kan ta bort den Azure Arc-aktiverade Kubernetes-resursen, eventuella associerade konfigurations resurser *och* alla agenter som körs i klustret med hjälp av Azure CLI med hjälp av följande kommando:

```azurecli
az connectedk8s delete --name AzureArcTest1 --resource-group AzureArcTest
```

>[!NOTE]
>Att ta bort den Azure Arc-aktiverade Kubernetes-resursen med Azure Portal tar bort alla associerade konfigurations resurser, men tar *inte* bort några agenter som körs i klustret. Bästa praxis är att ta bort den Azure Arc-aktiverade Kubernetes-resursen med hjälp `az connectedk8s delete` av i stället för Azure Portal.

## <a name="next-steps"></a>Nästa steg

Gå vidare till nästa artikel om du vill lära dig hur du distribuerar konfigurationer till ditt anslutna Kubernetes-kluster med hjälp av GitOps.
> [!div class="nextstepaction"]
> [Distribuera konfigurationer med Gitops](tutorial-use-gitops-connected-cluster.md)
