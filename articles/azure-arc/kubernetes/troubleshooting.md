---
title: Felsök vanliga problem med Azure Arc-aktiverade Kubernetes
services: azure-arc
ms.service: azure-arc
ms.date: 03/02/2020
ms.topic: article
author: mlearned
ms.author: mlearned
description: Felsöka vanliga problem med ARC-aktiverade Kubernetes-kluster.
keywords: Kubernetes, båge, Azure, behållare
ms.openlocfilehash: 992ea75c48b2630032e1314610986fbc610eec7b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "105025789"
---
# <a name="azure-arc-enabled-kubernetes-troubleshooting"></a>Azure Arc-aktiverad Kubernetes-felsökning

Det här dokumentet innehåller fel söknings guider för problem med anslutning, behörigheter och agenter.

## <a name="general-troubleshooting"></a>Allmän fel sökning

### <a name="azure-cli"></a>Azure CLI

Innan `az connectedk8s` du använder eller `az k8s-configuration` CLI-kommandon kontrollerar du att Azure CLI är inställt på att fungera mot rätt Azure-prenumeration.

```azurecli
az account set --subscription 'subscriptionId'
az account show
```

### <a name="azure-arc-agents"></a>Azure-båg agenter

Alla agenter för Azure Arc-aktiverade Kubernetes distribueras som poddar i `azure-arc` namn området. Alla poddar bör köra och skicka sina hälso kontroller.

Kontrol lera först Azure Arc Helm-versionen:

```console
$ helm --namespace default status azure-arc
NAME: azure-arc
LAST DEPLOYED: Fri Apr  3 11:13:10 2020
NAMESPACE: default
STATUS: deployed
REVISION: 5
TEST SUITE: None
```

Om Helm-versionen inte hittas eller saknas kan du försöka [ansluta klustret till Azure-bågen](./quickstart-connect-cluster.md) igen.

Om Helm-versionen finns med `STATUS: deployed` kontrollerar du statusen för agenterna med hjälp av `kubectl` :

```console
$ kubectl -n azure-arc get deployments,pods
NAME                                       READY  UP-TO-DATE  AVAILABLE  AGE
deployment.apps/clusteridentityoperator     1/1       1          1       16h
deployment.apps/config-agent                1/1       1          1       16h
deployment.apps/cluster-metadata-operator   1/1       1          1       16h
deployment.apps/controller-manager          1/1       1          1       16h
deployment.apps/flux-logs-agent             1/1       1          1       16h
deployment.apps/metrics-agent               1/1       1          1       16h
deployment.apps/resource-sync-agent         1/1       1          1       16h

NAME                                            READY   STATUS  RESTART  AGE
pod/cluster-metadata-operator-7fb54d9986-g785b  2/2     Running  0       16h
pod/clusteridentityoperator-6d6678ffd4-tx8hr    3/3     Running  0       16h
pod/config-agent-544c4669f9-4th92               3/3     Running  0       16h
pod/controller-manager-fddf5c766-ftd96          3/3     Running  0       16h
pod/flux-logs-agent-7c489f57f4-mwqqv            2/2     Running  0       16h
pod/metrics-agent-58b765c8db-n5l7k              2/2     Running  0       16h
pod/resource-sync-agent-5cf85976c7-522p5        3/3     Running  0       16h
```

Alla poddar ska visas `STATUS` som `Running` med antingen `3/3` eller `2/2` under `READY` kolumnen. Hämta loggar och beskriv poddar returnerar en `Error` eller `CrashLoopBackOff` . Om några poddar har fastnat i `Pending` tillstånd kan det finnas otillräckliga resurser på klusternoderna. [Skala upp klustret](https://kubernetes.io/docs/tasks/administer-cluster/) kan få dessa poddar att övergå till `Running` tillstånd.

## <a name="connecting-kubernetes-clusters-to-azure-arc"></a>Ansluta Kubernetes-kluster till Azure-bågen

Att ansluta kluster till Azure kräver både åtkomst till en Azure-prenumeration och `cluster-admin` åtkomst till ett mål kluster. Om du inte kan komma åt klustret eller om du har otillräckliga behörigheter går det inte att ansluta klustret till Azure-bågen.

### <a name="insufficient-cluster-permissions"></a>Otillräckliga kluster behörigheter

Om den angivna kubeconfig-filen inte har tillräcklig behörighet för att installera Azure Arc-agenterna returnerar Azure CLI-kommandot ett fel.

```azurecli
$ az connectedk8s connect --resource-group AzureArc --name AzureArcCluster
Ensure that you have the latest helm version installed before proceeding to avoid unexpected errors.
This operation might take a while...

Error: list: failed to list: secrets is forbidden: User "myuser" cannot list resource "secrets" in API group "" at the cluster scope
```

Användaren som ansluter klustret till Azure-bågen måste ha `cluster-admin` rollen tilldelad till klustret.

### <a name="installation-timeouts"></a>Tids gräns för installation

Att ansluta ett Kubernetes-kluster till Azure Arc Enabled Kubernetes kräver installation av Azure Arc-agenter i klustret. Om klustret körs över en långsam Internet anslutning kan behållar avbildningen Hämta för agenter ta längre tid än tids gränsen för Azure CLI.

```azurecli
$ az connectedk8s connect --resource-group AzureArc --name AzureArcCluster
Ensure that you have the latest helm version installed before proceeding to avoid unexpected errors.
This operation might take a while...
```

### <a name="helm-issue"></a>Helm-problem

Helm- `v3.3.0-rc.1` versionen har ett [problem](https://github.com/helm/helm/pull/8527) där Helm install/Upgrade (används av `connectedk8s` CLI-tillägget) leder till att alla hookar som leder till följande fel uppstår:

```console
$ az connectedk8s connect -n shasbakstest -g shasbakstest
Ensure that you have the latest helm version installed before proceeding.
This operation might take a while...

Please check if the azure-arc namespace was deployed and run 'kubectl get pods -n azure-arc' to check if all the pods are in running state. A possible cause for pods stuck in pending state could be insufficientresources on the kubernetes cluster to onboard to arc.
ValidationError: Unable to install helm release: Error: customresourcedefinitions.apiextensions.k8s.io "connectedclusters.arc.azure.com" not found
```

Följ dessa steg om du vill återställa problemet:

1. Ta bort den Azure Arc-aktiverade Kubernetes-resursen i Azure Portal.
2. Kör följande kommandon på datorn:
    
    ```console
    kubectl delete ns azure-arc
    kubectl delete clusterrolebinding azure-arc-operator
    kubectl delete secret sh.helm.release.v1.azure-arc.v1
    ```

3. [Installera en stabil version](https://helm.sh/docs/intro/install/) av Helm 3 på datorn i stället för versions kandidat versionen.
4. Kör `az connectedk8s connect` kommandot med lämpliga värden för att ansluta klustret till Azure-bågen.

## <a name="configuration-management"></a>Konfigurationshantering

### <a name="general"></a>Allmänt
Du kan felsöka problem med konfigurations resurser genom att köra AZ-kommandon med en `--debug` angiven parameter.

```console
az provider show -n Microsoft.KubernetesConfiguration --debug
az k8s-configuration create <parameters> --debug
```

### <a name="create-configurations"></a>Skapa konfigurationer

Skriv behörigheter för den Azure Arc-aktiverade Kubernetes-resursen ( `Microsoft.Kubernetes/connectedClusters/Write` ) är nödvändiga och tillräckliga för att skapa konfigurationer på klustret.

### <a name="configuration-remains-pending"></a>Konfigurationen är kvar `Pending`

```console
kubectl -n azure-arc logs -l app.kubernetes.io/component=config-agent -c config-agent
$ k -n pending get gitconfigs.clusterconfig.azure.com  -o yaml
apiVersion: v1
items:
- apiVersion: clusterconfig.azure.com/v1beta1
  kind: GitConfig
  metadata:
    creationTimestamp: "2020-04-13T20:37:25Z"
    generation: 1
    name: pending
    namespace: pending
    resourceVersion: "10088301"
    selfLink: /apis/clusterconfig.azure.com/v1beta1/namespaces/pending/gitconfigs/pending
    uid: d9452407-ff53-4c02-9b5a-51d55e62f704
  spec:
    correlationId: ""
    deleteOperator: false
    enableHelmOperator: false
    giturl: git@github.com:slack/cluster-config.git
    helmOperatorProperties: null
    operatorClientLocation: azurearcfork8s.azurecr.io/arc-preview/fluxctl:0.1.3
    operatorInstanceName: pending
    operatorParams: '"--disable-registry-scanning"'
    operatorScope: cluster
    operatorType: flux
  status:
    configAppliedTime: "2020-04-13T20:38:43.081Z"
    isSyncedWithAzure: true
    lastPolledStatusTime: ""
    message: 'Error: {exit status 1} occurred while doing the operation : {Installing
      the operator} on the config'
    operatorPropertiesHashed: ""
    publicKey: ""
    retryCountPublicKey: 0
    status: Installing the operator
kind: List
metadata:
  resourceVersion: ""
  selfLink: ""
```
## <a name="monitoring"></a>Övervakning

DaemonSet för behållare kräver att dess körs i privilegierat läge. Azure Monitor Kör följande kommando för att konfigurera ett kanoniskt snabb Kubernetes-kluster för övervakning:

```console
juju config kubernetes-worker allow-privileged=true
```