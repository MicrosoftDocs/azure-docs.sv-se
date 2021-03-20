---
title: Autoskalning av AKS-poddar med Azure Application Gateway-mått
description: Den här artikeln innehåller anvisningar om hur du skalar din AKS-backend-poddar med Application Gateway mått och Azure Kubernetes Metric adapter
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: how-to
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: a8f015085baa8fffa6f208e9d8dd749e397c76c3
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "93397441"
---
# <a name="autoscale-your-aks-pods-using-application-gateway-metrics-beta"></a>Autoskala din AKS-poddar med hjälp av Application Gateway mått (beta)

Allteftersom inkommande trafik ökar är det viktigt att skala upp dina program baserat på efter frågan.

I följande självstudie förklarar vi hur du kan använda Application Gateways `AvgRequestCountPerHealthyHost` mått för att skala upp ditt program. `AvgRequestCountPerHealthyHost` mäter Genomsnittligt antal begär Anden som skickas till en bestämd inställnings kombination för en server del och en server del.

Vi ska använda följande två komponenter:

* [`Azure Kubernetes Metric Adapter`](https://github.com/Azure/azure-k8s-metrics-adapter) – Vi använder mått kortet för att Visa Application Gateway mått via mått servern. Azure Kubernetes Metric adapter är ett projekt med öppen källkod under Azure, som liknar Application Gateway ingångs kontroll. 
* [`Horizontal Pod Autoscaler`](../aks/concepts-scale.md#horizontal-pod-autoscaler) – Vi använder HPA för att använda Application Gateway mått och rikta en distribution för skalning.

## <a name="setting-up-azure-kubernetes-metric-adapter"></a>Konfigurera Azure Kubernetes Metric adapter

1. Vi börjar med att skapa ett huvud namn för Azure AAD-tjänsten och tilldela det `Monitoring Reader` åtkomst över Application Gateway resurs gruppen. 

    ```azurecli
        applicationGatewayGroupName="<application-gateway-group-id>"
        applicationGatewayGroupId=$(az group show -g $applicationGatewayGroupName -o tsv --query "id")
        az ad sp create-for-rbac -n "azure-k8s-metric-adapter-sp" --role "Monitoring Reader" --scopes applicationGatewayGroupId
    ```

1. Nu ska vi distribuera [`Azure Kubernetes Metric Adapter`](https://github.com/Azure/azure-k8s-metrics-adapter) med hjälp av AAD-tjänstens huvud namn som skapats ovan.

    ```bash
    kubectl create namespace custom-metrics
    # use values from service principal created above to create secret
    kubectl create secret generic azure-k8s-metrics-adapter -n custom-metrics \
        --from-literal=azure-tenant-id=<tenantid> \
        --from-literal=azure-client-id=<clientid> \
        --from-literal=azure-client-secret=<secret>
    kubectl apply -f kubectl apply -f https://raw.githubusercontent.com/Azure/azure-k8s-metrics-adapter/master/deploy/adapter.yaml -n custom-metrics
    ```

1. Vi kommer att skapa en `ExternalMetric` resurs med namnet `appgw-request-count-metric` . Den här resursen instruerar Metric-kortet att exponera `AvgRequestCountPerHealthyHost` mått för `myApplicationGateway` resursen i `myResourceGroup` resurs gruppen. Du kan använda `filter` fältet för att rikta in dig på en bestämd Server del och HTTP-inställning för Server delen i Application Gateway.

    ```yaml
    apiVersion: azure.com/v1alpha2
    kind: ExternalMetric
    metadata:
    name: appgw-request-count-metric
    spec:
        type: azuremonitor
        azure:
            resourceGroup: myResourceGroup # replace with your application gateway's resource group name
            resourceName: myApplicationGateway # replace with your application gateway's name
            resourceProviderNamespace: Microsoft.Network
            resourceType: applicationGateways
        metric:
            metricName: AvgRequestCountPerHealthyHost
            aggregation: Average
            filter: BackendSettingsPool eq '<backend-pool-name>~<backend-http-setting-name>' # optional
    ```

Nu kan du göra en begäran till mått servern för att se om vårt nya mått visas:
```bash
kubectl get --raw "/apis/external.metrics.k8s.io/v1beta1/namespaces/default/appgw-request-count-metric"
# Sample Output
# {
#   "kind": "ExternalMetricValueList",
#   "apiVersion": "external.metrics.k8s.io/v1beta1",
#   "metadata":
#     {
#       "selfLink": "/apis/external.metrics.k8s.io/v1beta1/namespaces/default/appgw-request-count-metric",
#     },
#   "items":
#     [
#       {
#         "metricName": "appgw-request-count-metric",
#         "metricLabels": null,
#         "timestamp": "2019-11-05T00:18:51Z",
#         "value": "30",
#       },
#     ],
# }
```

## <a name="using-the-new-metric-to-scale-up-the-deployment"></a>Använda det nya måttet för att skala upp distributionen

När vi kan exponera `appgw-request-count-metric` via mått servern är vi redo att använda [`Horizontal Pod Autoscaler`](../aks/concepts-scale.md#horizontal-pod-autoscaler) för att skala upp vår mål distribution.

I följande exempel ska vi rikta in sig på en exempel distribution `aspnet` . Vi kommer att skala upp poddar när `appgw-request-count-metric` > 200 per POD upp till högst `10` poddar.

Ersätt ditt mål distributions namn och Använd följande konfiguration för automatisk skalning:
```yaml
apiVersion: autoscaling/v2beta1
kind: HorizontalPodAutoscaler
metadata:
  name: deployment-scaler
spec:
  scaleTargetRef:
    apiVersion: extensions/v1beta1
    kind: Deployment
    name: aspnet # replace with your deployment's name
  minReplicas: 1
  maxReplicas: 10
  metrics:
  - type: External
    external:
      metricName: appgw-request-count-metric
      targetAverageValue: 200
```

Testa installationen med hjälp av ett belastnings test verktyg som Apache bänk:
```bash
ab -n10000 http://<applicaiton-gateway-ip-address>/
```

## <a name="next-steps"></a>Nästa steg
- [**Felsök problem med inkommande styrenheter**](ingress-controller-troubleshoot.md): Felsök eventuella problem med ingångs styrenheten.