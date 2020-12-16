---
title: Felsöka vanliga problem med Azure Arc-aktiverade Kubernetes (för hands version)
services: azure-arc
ms.service: azure-arc
ms.date: 05/19/2020
ms.topic: article
author: mlearned
ms.author: mlearned
description: Felsöka vanliga problem med ARC-aktiverade Kubernetes-kluster.
keywords: Kubernetes, båge, Azure, behållare
ms.openlocfilehash: 42c90708854af6973ed1ef399b9867101a736b07
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/16/2020
ms.locfileid: "97586167"
---
# <a name="azure-arc-enabled-kubernetes-troubleshooting-preview"></a>Azure Arc Enabled Kubernetes Troubleshooting (för hands version)

Det här dokumentet innehåller några vanliga fel söknings scenarier med anslutning, behörigheter och agenter.

## <a name="general-troubleshooting"></a>Allmän fel sökning

### <a name="azure-cli-set-up"></a>Konfigurera Azure CLI
Innan du använder AZ-connectedk8s eller AZ k8sconfiguration CLI-kommandon bör du se till att AZ är inställt på att fungera mot rätt Azure-prenumeration.

```console
az account set --subscription 'subscriptionId'
az account show
```

### <a name="azure-arc-agents"></a>Azure-båg agenter
Alla agenter för Azure Arc-aktiverade Kubernetes distribueras som poddar i `azure-arc` namn området. Under normala drifts åtgärder bör alla poddar köras och att deras hälso kontroller skickas.

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

Om Helm-versionen inte hittas eller saknas kan du försöka att registrera klustret igen.

Om Helm-versionen finns och `STATUS: deployed` bestämmer status för agenterna med hjälp av `kubectl` :

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

Alla poddar ska visas `STATUS` som `Running` och `READY` ska vara antingen `3/3` eller `2/2` . Hämta loggar och beskriv poddar som returnerar `Error` eller `CrashLoopBackOff` . Om något av dessa poddar har fastnat i `Pending` tillstånd kan det bero på otillräckliga resurser på klusternoderna. När du [skalar upp klustret](https://kubernetes.io/docs/tasks/administer-cluster/) får dessa poddar över gång till `Running` status.

## <a name="connecting-kubernetes-clusters-to-azure-arc"></a>Ansluta Kubernetes-kluster till Azure-bågen

Att ansluta kluster till Azure kräver åtkomst till både en Azure-prenumeration och `cluster-admin` åtkomst till ett mål kluster. Om det inte går att nå klustret eller om det inte finns tillräckligt med onboarding-registrering kommer det att Miss Miss

### <a name="insufficient-cluster-permissions"></a>Otillräckliga kluster behörigheter

Om den angivna kubeconfig-filen inte har tillräcklig behörighet för att installera Azure Arc-agenterna returnerar Azure CLI-kommandot ett fel som försöker anropa Kubernetes-API: et.

```console
$ az connectedk8s connect --resource-group AzureArc --name AzureArcCluster
Command group 'connectedk8s' is in preview. It may be changed/removed in a future release.
Ensure that you have the latest helm version installed before proceeding to avoid unexpected errors.
This operation might take a while...

Error: list: failed to list: secrets is forbidden: User "myuser" cannot list resource "secrets" in API group "" at the cluster scope
```

Kluster ägaren bör använda en Kubernetes-användare med kluster administratörs behörighet.

### <a name="installation-timeouts"></a>Tids gräns för installation

Installationen av Azure Arc-agenten kräver att en uppsättning behållare körs på mål klustret. Om klustret körs över en långsam Internet anslutning kan behållar avbildningen ta längre tid än tids gränsen för Azure CLI.

```console
$ az connectedk8s connect --resource-group AzureArc --name AzureArcCluster
Command group 'connectedk8s' is in preview. It may be changed/removed in a future release.
Ensure that you have the latest helm version installed before proceeding to avoid unexpected errors.
This operation might take a while...
```

### <a name="helm-issue"></a>Helm-problem

Helm- `v3.3.0-rc.1` versionen har ett [problem](https://github.com/helm/helm/pull/8527) där Helm install/Upgrade (som används under connectedk8s CLI-tillägget) leder till att alla hookar som leder till följande fel uppstår:

```console
$ az connectedk8s connect -n shasbakstest -g shasbakstest
Command group 'connectedk8s' is in preview. It may be changed/removed in a future release.
Ensure that you have the latest helm version installed before proceeding.
This operation might take a while...

Please check if the azure-arc namespace was deployed and run 'kubectl get pods -n azure-arc' to check if all the pods are in running state. A possible cause for pods stuck in pending state could be insufficientresources on the kubernetes cluster to onboard to arc.
ValidationError: Unable to install helm release: Error: customresourcedefinitions.apiextensions.k8s.io "connectedclusters.arc.azure.com" not found
```

Följ dessa steg om du vill återställa problemet:

1. Ta bort den Azure Arc-aktiverade Kubernetes-resursen som är av intresse i Azure Portal.
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
Du kan felsöka problem med käll kontroll konfigurationen genom att köra AZ-kommandon med--debug-växeln.

```console
az provider show -n Microsoft.KubernetesConfiguration --debug
az k8sconfiguration create <parameters> --debug
```

### <a name="create-source-control-configuration"></a>Skapa käll kontroll konfiguration
Deltagar rollen på resursen Microsoft. Kubernetes/connectedCluster är nödvändig och tillräckligt för att skapa Microsoft. KubernetesConfiguration/sourceControlConfiguration-resursen.

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