---
title: Språkidentifiering Kubernetes konfiguration och distribution av steg
titleSuffix: Azure Cognitive Services
description: Språkidentifiering Kubernetes konfiguration och distribution av steg
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 1edca9cf8449ff386d0a9920e7d80d69692536fd
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96017883"
---
### <a name="deploy-the-language-detection-container-to-an-aks-cluster"></a>Distribuera Språkidentifiering-behållaren till ett AKS-kluster

1. Öppna Azure CLI och logga in på Azure.

    ```azurecli
    az login
    ```

1. Logga in på AKS-klustret. Ersätt `your-cluster-name` och `your-resource-group` med lämpliga värden.

    ```azurecli
    az aks get-credentials -n your-cluster-name -g -your-resource-group
    ```

    När det här kommandot körs rapporterar det ett meddelande som liknar följande:

    ```output
    Merged "your-cluster-name" as current context in /home/username/.kube/config
    ```

    > [!WARNING]
    > Om du har flera prenumerationer tillgängliga på ditt Azure-konto och `az aks get-credentials` kommandot returnerar med ett fel, är det ett vanligt problem att du använder fel prenumeration. Ställ in kontexten för Azure CLI-sessionen så att den använder samma prenumeration som du skapade resurserna med och försök igen.
    > ```azurecli
    >  az account set -s subscription-id
    > ```

1. Öppna valfri text redigerare. I det här exemplet används Visual Studio Code.

    ```console
    code .
    ```

1. I text redigeraren skapar du en ny fil med namnet *Language. yaml* och klistrar in följande yaml i den. Se till att ersätta `billing/value` och `apikey/value` med din egen information.

    ```yaml
    apiVersion: apps/v1beta1
    kind: Deployment
    metadata:
      name: language
    spec:
      template:
        metadata:
          labels:
            app: language-app
        spec:
          containers:
          - name: language
            image: mcr.microsoft.com/azure-cognitive-services/language
            ports:
            - containerPort: 5000
            resources:
              requests:
                memory: 2Gi
                cpu: 1
              limits:
                memory: 4Gi
                cpu: 1
            env:
            - name: EULA
              value: "accept"
            - name: billing
              value: # {ENDPOINT_URI}
            - name: apikey
              value: # {API_KEY}
     
    --- 
    apiVersion: v1
    kind: Service
    metadata:
      name: language
    spec:
      type: LoadBalancer
      ports:
      - port: 5000
      selector:
        app: language-app
    ```

1. Spara filen och Stäng text redigeraren.
1. Kör kommandot Kubernetes `apply` med *språk. yaml* -filen som mål:

    ```console
    kubectl apply -f language.yaml
    ```

    När kommandot har tillämpat distributions konfigurationen visas ett meddelande som liknar följande utdata:

    ```output
    deployment.apps "language" created
    service "language" created
    ```
1. Kontrol lera att Pod har distribuerats:

    ```console
    kubectl get pods
    ```

    Utdata för körnings status för pod:

    ```output
    NAME                         READY     STATUS    RESTARTS   AGE
    language-5c9ccdf575-mf6k5   1/1       Running   0          1m
    ```

1. Kontrol lera att tjänsten är tillgänglig och hämta IP-adressen.

    ```console
    kubectl get services
    ```

    Utdata för *språk* tjänstens körnings status i pod:

    ```output
    NAME         TYPE           CLUSTER-IP    EXTERNAL-IP      PORT(S)          AGE
    kubernetes   ClusterIP      10.0.0.1      <none>           443/TCP          2m
    language     LoadBalancer   10.0.100.64   168.61.156.180   5000:31234/TCP   2m
    ```
