---
title: Distribuera Helm-diagram med GitOps on Arc Enabled Kubernetes Cluster
services: azure-arc
ms.service: azure-arc
ms.date: 03/03/2021
ms.topic: article
author: mlearned
ms.author: mlearned
description: Använda GitOps med Helm för en Azure Arc-aktiverad kluster konfiguration
keywords: GitOps, Kubernetes, K8s, Azure, Helm, Arc, AKS, Azure Kubernetes service, containers
ms.openlocfilehash: df9b40764ec463553659803749f282bbc4587bde
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2021
ms.locfileid: "106449551"
---
# <a name="deploy-helm-charts-using-gitops-on-an-arc-enabled-kubernetes-cluster"></a>Distribuera Helm-diagram med GitOps på ett Arc-aktiverat Kubernetes-kluster

Helm är ett paketeringsverktyg med öppen källkod som hjälper dig att installera och hantera livscykeln för Kubernetes-program. På liknande sätt som med Linux-paket som APT och yum används Helm för att hantera Kubernetes-diagram, som är paket med förkonfigurerade Kubernetes-resurser.

Den här artikeln visar hur du konfigurerar och använder Helm med Azure Arc-aktiverade Kubernetes.

## <a name="prerequisites"></a>Förutsättningar

- Ett Azure-konto med en aktiv prenumeration. [Skapa ett konto kostnads fritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Ett befintligt Azure Arc-aktiverat Kubernetes-kopplat kluster.
    - Om du inte har anslutit ett kluster ännu kan du gå igenom vår [Anslut en Azure-båge som är aktive rad Kubernetes kluster snabb start](quickstart-connect-cluster.md).
- En förståelse för den här funktionens fördelar och arkitektur. Läs mer i [konfigurationer och GitOps-Azure Arc-aktiverad Kubernetes-artikel](conceptual-configurations.md).
- Installera `k8s-configuration` Azure CLI-tillägget för version >= 1.0.0:
  
  ```azurecli
  az extension add --name k8s-configuration
  ```

## <a name="overview-of-using-gitops-and-helm-with-azure-arc-enabled-kubernetes"></a>Översikt över användning av GitOps och Helm med Azure Arc Enabled Kubernetes

 Helm-operatören ger ett tillägg till flöde som automatiserar Helm-diagram versioner. En Helm diagram version beskrivs via en Kubernetes-anpassad resurs med namnet HelmRelease. Flödet synkroniserar dessa resurser från git till klustret, medan Helm-operatorn ser till att Helm-diagram släpps på det sätt som anges i resurserna.

 [Exempel lagrings platsen](https://github.com/Azure/arc-helm-demo) som används i den här artikeln är strukturerad på följande sätt:

```console
├── charts
│   └── azure-arc-sample
│       ├── Chart.yaml
│       ├── templates
│       │   ├── NOTES.txt
│       │   ├── deployment.yaml
│       │   └── service.yaml
│       └── values.yaml
└── releases
    └── app.yaml
```

I git-lagrings platsen har vi två kataloger: en som innehåller ett Helm-diagram och en som innehåller de versioner som är konfigurerade. I `releases` katalogen `app.yaml` innehåller HelmRelease-konfigurationen, som visas nedan:

```yaml
apiVersion: helm.fluxcd.io/v1
kind: HelmRelease
metadata:
  name: azure-arc-sample
  namespace: arc-k8s-demo
spec:
  releaseName: arc-k8s-demo
  chart:
    git: https://github.com/Azure/arc-helm-demo
    ref: master
    path: charts/azure-arc-sample
  values:
    serviceName: arc-k8s-demo
```

Helm-versions konfigurationen innehåller följande fält:

| Fält | Beskrivning |
| ------------- | ------------- | 
| `metadata.name` | Obligatoriskt fält. Måste följa Kubernetes namngivnings konventioner. |
| `metadata.namespace` | Valfritt fält. Anger var versionen skapas. |
| `spec.releaseName` | Valfritt fält. Om det inte anges kommer versions namnet att vara `$namespace-$name` . |
| `spec.chart.path` | Den katalog som innehåller diagrammet (i förhållande till databas roten). |
| `spec.values` | Användar anpassningar av standard parameter värden från själva diagrammet. |

Alternativen som anges i HelmRelease `spec.values` åsidosätter de alternativ som anges i `values.yaml` från diagrammets källa.

Du kan läsa mer om HelmRelease i den officiella [Helm operatörs dokumentationen](https://docs.fluxcd.io/projects/helm-operator/en/stable/).

## <a name="create-a-configuration"></a>Skapa en konfiguration

Använd Azure CLI-tillägget för `k8s-configuration` och länka det anslutna klustret till exempel git-lagringsplatsen. Ge den här konfigurationen namnet `azure-arc-sample` och distribuera flödes operatorn i `arc-k8s-demo` namn området.

```console
az k8s-configuration create --name azure-arc-sample --cluster-name AzureArcTest1 --resource-group AzureArcTest --operator-instance-name flux --operator-namespace arc-k8s-demo --operator-params='--git-readonly --git-path=releases' --enable-helm-operator --helm-operator-chart-version='1.2.0' --helm-operator-params='--set helm.versions=v3' --repository-url https://github.com/Azure/arc-helm-demo.git --scope namespace --cluster-type connectedClusters
```

### <a name="configuration-parameters"></a>Konfigurations parametrar

Om du vill anpassa skapandet av konfigurationen kan du [läsa mer om ytterligare parametrar](./tutorial-use-gitops-connected-cluster.md#additional-parameters).

## <a name="validate-the-configuration"></a>Verifiera konfigurationen

Kontrol lera att konfigurationen har skapats med hjälp av Azure CLI.

```console
az k8s-configuration show --name azure-arc-sample --cluster-name AzureArcTest1 --resource-group AzureArcTest --cluster-type connectedClusters
```

Konfigurations resursen uppdateras med kompatibilitetsstatus, meddelanden och fel söknings information.

```output
{
  "complianceStatus": {
    "complianceState": "Installed",
    "lastConfigApplied": "2019-12-05T05:34:41.481000",
    "message": "{\"OperatorMessage\":null,\"ClusterState\":null}",
    "messageLevel": "3"
  },
  "enableHelmOperator": "True",
  "helmOperatorProperties": {
    "chartValues": "--set helm.versions=v3",
    "chartVersion": "1.2.0"
  },
  "id": "/subscriptions/57ac26cf-a9f0-4908-b300-9a4e9a0fb205/resourceGroups/AzureArcTest/providers/Microsoft.Kubernetes/connectedClusters/AzureArcTest1/providers/Microsoft.KubernetesConfiguration/sourceControlConfigurations/azure-arc-sample",
  "name": "azure-arc-sample",
  "operatorInstanceName": "flux",
  "operatorNamespace": "arc-k8s-demo",
  "operatorParams": "--git-readonly --git-path=releases",
  "operatorScope": "namespace",
  "operatorType": "Flux",
  "provisioningState": "Succeeded",
  "repositoryPublicKey": "",
  "repositoryUrl": "https://github.com/Azure/arc-helm-demo.git",
  "resourceGroup": "AzureArcTest",
  "type": "Microsoft.KubernetesConfiguration/sourceControlConfigurations"
}
```

## <a name="validate-application"></a>Verifiera program

Kör följande kommando och navigera till `localhost:8080` i webbläsaren för att kontrol lera att programmet körs.

```console
kubectl port-forward -n arc-k8s-demo svc/arc-k8s-demo 8080:8080
```

## <a name="next-steps"></a>Nästa steg

Använd klusterkonfigurationer i skala med [Azure policy](./use-azure-policy.md).
