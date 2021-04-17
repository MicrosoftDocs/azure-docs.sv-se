---
title: 'Snabbstart: Ansluta ett befintligt Kubernetes-kluster till Azure Arc'
description: I den här snabbstarten lär du dig hur du ansluter ett Azure Arc aktiverat Kubernetes-kluster.
author: mgoedtel
ms.author: magoedte
ms.service: azure-arc
ms.topic: quickstart
ms.date: 03/03/2021
ms.custom: template-quickstart, references_regions, devx-track-azurecli
keywords: Kubernetes, Arc, Azure, cluster
ms.openlocfilehash: 21ec5000ed7ef9df1805fa6ec43e20efc0f82182
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2021
ms.locfileid: "107481267"
---
# <a name="quickstart-connect-an-existing-kubernetes-cluster-to-azure-arc"></a>Snabbstart: Ansluta ett befintligt Kubernetes-kluster till Azure Arc 

I den här snabbstarten drar vi nytta av fördelarna med Azure Arc Aktiverat Kubernetes och ansluter ett befintligt Kubernetes-kluster till Azure Arc. En konceptuell översikt över hur du ansluter kluster till Azure Arc finns [i artikeln Azure Arc kubernetes-agentarkitektur.](./conceptual-agent-architecture.md)

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

* Ett igång kubernetes-kluster. Om du inte har något kan du skapa ett kluster med något av följande alternativ:
    * [Kubernetes i Docker (KIND)](https://kind.sigs.k8s.io/)
    * Skapa ett Kubernetes-kluster med Docker för [Mac](https://docs.docker.com/docker-for-mac/#kubernetes) eller [Windows](https://docs.docker.com/docker-for-windows/#kubernetes)
    * Självstyrt Kubernetes-kluster med [kluster-API](https://cluster-api.sigs.k8s.io/user/quick-start.html)

    >[!NOTE]
    > Klustret måste ha minst en nod av operativsystemet och arkitekturtypen `linux/amd64` . Kluster med endast `linux/arm64` noder stöds inte ännu.
    
* En `kubeconfig` fil och kontext som pekar på klustret.
* Läs- och skrivbehörigheter på den Azure Arc Kubernetes-resurstypen ( `Microsoft.Kubernetes/connectedClusters` ).

* Installera den [senaste versionen av Helm 3.](https://helm.sh/docs/intro/install)

- [Installera eller uppgradera Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) till version >= 2.16.0
* Installera `connectedk8s` Azure CLI-tillägget för version >= 1.0.0:
  
  ```azurecli
  az extension add --name connectedk8s
  ```

>[!TIP]
> Om tillägget `connectedk8s` redan är installerat uppdaterar du det till den senaste versionen med följande kommando : `az extension update --name connectedk8s`


>[!NOTE]
>Listan över regioner som stöds av Azure Arc aktiverat Kubernetes finns [här](https://azure.microsoft.com/global-infrastructure/services/?products=azure-arc).

>[!NOTE]
> Om du vill använda anpassade platser i klustret använder du regionerna USA, östra eller Europa, västra för att ansluta klustret eftersom anpassade platser endast är tillgängliga i dessa regioner från och med nu. Alla andra Azure Arc Kubernetes-funktioner är tillgängliga i alla regioner som anges ovan.

## <a name="meet-network-requirements"></a>Uppfylla nätverkskrav

>[!IMPORTANT]
>Azure Arc kräver följande protokoll/portar/utgående URL:er för att fungera:
>* TCP på port 443: `https://:443`
>* TCP på port 9418: `git://:9418`
  
| Slutpunkt (DNS) | Beskrivning |  
| ----------------- | ------------- |  
| `https://management.azure.com`                                                                                 | Krävs för att agenten ska kunna ansluta till Azure och registrera klustret.                                                        |  
| `https://<region>.dp.kubernetesconfiguration.azure.com` | Dataplansslutpunkt för agenten för att skicka status och hämta konfigurationsinformation.                                      |  
| `https://login.microsoftonline.com`                                                                            | Krävs för att hämta och Azure Resource Manager token.                                                                                    |  
| `https://mcr.microsoft.com`                                                                            | Krävs för att hämta containeravbildningar Azure Arc agenterna.                                                                  |  
| `https://eus.his.arc.azure.com`, `https://weu.his.arc.azure.com`, `https://wcus.his.arc.azure.com`, `https://scus.his.arc.azure.com`, `https://sea.his.arc.azure.com`, `https://uks.his.arc.azure.com`, `https://wus2.his.arc.azure.com`, `https://ae.his.arc.azure.com`, `https://eus2.his.arc.azure.com`, `https://ne.his.arc.azure.com` |  Krävs för att hämta system tilldelade certifikat för hanterad tjänstidentitet (MSI).                                                                  |

## <a name="register-the-two-providers-for-azure-arc-enabled-kubernetes"></a>Registrera de två leverantörerna för Azure Arc aktiverat Kubernetes

1. Ange följande kommandon:
    ```azurecli
    az provider register --namespace Microsoft.Kubernetes
    az provider register --namespace Microsoft.KubernetesConfiguration
    az provider register --namespace Microsoft.ExtendedLocation
    ```
2. Övervaka registreringsprocessen. Registreringen kan ta upp till 10 minuter.
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

## <a name="connect-an-existing-kubernetes-cluster"></a>Ansluta ett befintligt Kubernetes-kluster

1. Anslut Kubernetes-klustret till Azure Arc med följande kommando:
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
> Kommandot ovan utan den angivna platsparametern skapar den Azure Arc Kubernetes-resursen på samma plats som resursgruppen. Om du vill Azure Arc kubernetes-resurs på en annan plats anger du antingen `--location <region>` eller när du kör `-l <region>` `az connectedk8s connect` kommandot.

## <a name="verify-cluster-connection"></a>Verifiera klusteranslutningen

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
> När klustret har integrering tar det cirka 5 till 10 minuter för klustermetadata (klusterversion, agentversion, antal noder osv.) att visas på översiktssidan för den Azure Arc-aktiverade Kubernetes-resursen i Azure Portal.

## <a name="connect-using-an-outbound-proxy-server"></a>Ansluta med en utgående proxyserver

Om klustret ligger bakom en utgående proxyserver måste Azure CLI och de Azure Arc-aktiverade Kubernetes-agenterna dirigera sina begäranden via den utgående proxyservern. 


1. Ange de miljövariabler som behövs för att Azure CLI ska kunna använda den utgående proxyservern:

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

2. Kör connect-kommandot med angivna proxyparametrar:

    ```console
    az connectedk8s connect -n <cluster-name> -g <resource-group> --proxy-https https://<proxy-server-ip-address>:<port> --proxy-http http://<proxy-server-ip-address>:<port> --proxy-skip-range <excludedIP>,<excludedCIDR> --proxy-cert <path-to-cert-file>
    ```

> [!NOTE]
> * Ange `excludedCIDR` under för att säkerställa att kommunikationen i klustret inte bryts för `--proxy-skip-range` agenterna.
> * `--proxy-http`, `--proxy-https` och förväntas för de flesta `--proxy-skip-range` utgående proxymiljöer. `--proxy-cert` krävs *endast* om du behöver mata in betrodda certifikat som förväntas av proxyn i det betrodda certifikatarkivet för agentpoddar.

## <a name="view-azure-arc-agents-for-kubernetes"></a>Visa Azure Arc för Kubernetes

Azure Arc aktiverat Kubernetes distribuerar några operatorer till `azure-arc` namnområdet. 

1. Visa dessa distributioner och poddar med hjälp av:

    ```console
    kubectl -n azure-arc get deployments,pods
    ```

1. Kontrollera att alla poddar är i `Running` ett tillstånd.

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

Du kan ta bort Azure Arc Kubernetes-resursen, eventuella  associerade konfigurationsresurser och alla agenter som körs i klustret med Hjälp av Azure CLI med hjälp av följande kommando:

```azurecli
az connectedk8s delete --name AzureArcTest1 --resource-group AzureArcTest
```

>[!NOTE]
>Om du tar Azure Arc Kubernetes-resurs med Azure Portal tar bort alla  associerade konfigurationsresurser, men inga agenter som körs i klustret tas bort. Bästa praxis är att ta bort den Azure Arc Kubernetes-resursen med hjälp `az connectedk8s delete` av i stället för Azure Portal.

## <a name="next-steps"></a>Nästa steg

Gå vidare till nästa artikel om du vill lära dig hur du distribuerar konfigurationer till ditt anslutna Kubernetes-kluster med GitOps.
> [!div class="nextstepaction"]
> [Distribuera konfigurationer med Gitops](tutorial-use-gitops-connected-cluster.md)
