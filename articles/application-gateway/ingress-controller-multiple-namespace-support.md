---
title: Aktivera Multiple namespace stöder Application Gateway ingångs kontroll
description: Den här artikeln innehåller information om hur du aktiverar stöd för flera namnrymder i ett Kubernetes-kluster med en Application Gateway ingångs kontroll.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: how-to
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: c13c4410852d97f0bf4548578f40a5cc560804d7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "94874601"
---
# <a name="enable-multiple-namespace-support-in-an-aks-cluster-with-application-gateway-ingress-controller"></a>Aktivera stöd för flera namnrymder i ett AKS-kluster med Application Gateway ingress-styrenhet

## <a name="motivation"></a>Motivation
Kubernetes- [namnområden](https://kubernetes.io/docs/concepts/overview/working-with-objects/namespaces/) gör det möjligt för ett Kubernetes-kluster att partitioneras och allokeras till under grupper i ett större team. Dessa under grupper kan sedan distribuera och hantera infrastrukturen med bättre kontroller av resurser, säkerhet, konfiguration osv. Kubernetes gör det möjligt för en eller flera ingress-resurser att definieras självständigt inom varje namnrymd.

Från och med version 0,7 [Azure Application Gateway Kubernetes IngressController](https://github.com/Azure/application-gateway-kubernetes-ingress/blob/master/README.md) (AGIC) kan mata in händelser från och observera flera namn områden. Om AKS-administratören bestämmer sig för att använda [app Gateway](https://azure.microsoft.com/services/application-gateway/) som ingångs namn kommer alla namn områden att använda samma instans av Application Gateway. En enskild installation av ingångs styrenheten övervakar tillgängliga namn områden och konfigurerar Application Gateway som den är associerad med.

Version 0,7 av AGIC kommer även fortsättnings vis att observera `default` namn området, såvida det inte uttryckligen ändras till ett eller flera olika namn rymder i Helm-konfigurationen (se avsnittet nedan).

## <a name="enable-multiple-namespace-support"></a>Aktivera stöd för flera namnrymder
Så här aktiverar du stöd för flera namnrymder:
1. ändra filen [Helm-config. yaml](#sample-helm-config-file) på något av följande sätt:
   - ta bort `watchNamespace` nyckeln helt från [Helm-config. yaml](#sample-helm-config-file) -AGIC kommer att Observera alla namn områden
   - Ange `watchNamespace` till en tom sträng – AGIC kommer att Observera alla namn områden
   - Lägg till flera namn områden avgränsade med kommatecken ( `watchNamespace: default,secondNamespace` )-AGIC kommer endast att observera dessa namn områden
2. tillämpa ändringar av Helm-mal len med: `helm install -f helm-config.yaml application-gateway-kubernetes-ingress/ingress-azure`

När du har distribuerat med möjligheten att observera flera namn områden, kommer AGIC att:
  - lista inkommande resurser från alla tillgängliga namn områden
  - Filtrera för att ingressa resurser som är kommenterade med `kubernetes.io/ingress.class: azure/application-gateway`
  - Skapa kombinerad [Application Gateway konfiguration](https://github.com/Azure/azure-sdk-for-go/blob/37f3f4162dfce955ef5225ead57216cf8c1b2c70/services/network/mgmt/2016-06-01/network/models.go#L1710-L1744)
  - Använd config på den associerade Application Gateway via [arm](../azure-resource-manager/management/overview.md)

## <a name="conflicting-configurations"></a>Motstridiga konfigurationer
Flera namn områdes [ingångs resurser](https://kubernetes.io/docs/concepts/services-networking/ingress/#the-ingress-resource) kan instruera AGIC att skapa motstridiga konfigurationer för en enda Application Gateway. (Två ingress-anspråk på samma domän för instansen.)

Högst upp i hierarkin **(IP-adress** , port och värd) och regler för **routning** (bindnings lyssnare, backend-pool och http-inställningar) kan skapas och delas av flera namn rymder/ingress.

På andra platser kan backend-pooler, HTTP-inställningar och TLS-certifikat skapas med endast ett namn område och dubbletter tas bort.

Anta till exempel följande dubbla ingress-resurser definierade namn rymder `staging` och `production` för `www.contoso.com` :

```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: websocket-ingress
  namespace: staging
  annotations:
    kubernetes.io/ingress.class: azure/application-gateway
spec:
  rules:
    - host: www.contoso.com
      http:
        paths:
          - backend:
              serviceName: web-service
              servicePort: 80
```

```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: websocket-ingress
  namespace: production
  annotations:
    kubernetes.io/ingress.class: azure/application-gateway
spec:
  rules:
    - host: www.contoso.com
      http:
        paths:
          - backend:
              serviceName: web-service
              servicePort: 80
```

Trots de två ingress-resurserna som kräver trafik för `www.contoso.com` att kunna dirigeras till respektive Kubernetes-namnområden, kan endast en server del betjäna trafiken. AGIC skulle skapa en konfiguration med "First-First-First-firsted"-grunden för en av resurserna. Om två ingress-resurser skapas samtidigt, kommer det tidigare i alfabetet att ha företräde. Från exemplet ovan kommer vi bara att kunna skapa inställningar för `production` ingressen. Application Gateway kommer att konfigureras med följande resurser:

  - Lyssnare `fl-www.contoso.com-80`
  - Regel för Routning: `rr-www.contoso.com-80`
  - Backend-pool: `pool-production-contoso-web-service-80-bp-80`
  - HTTP-inställningar: `bp-production-contoso-web-service-80-80-websocket-ingress`
  - Hälso avsökning: `pb-production-contoso-web-service-80-websocket-ingress`

Observera att om du har skapat Application Gateway-resurser som har skapats med undantag för *lyssnare* och *regler*, inkluderas namnet på det namn område ( `production` ) som de skapades för.

Om de två ingångs resurserna introduceras i AKS-klustret vid olika tidpunkter, är det sannolikt för AGIC att få ett scenario där den konfigurerar om Application Gateway och dirigerar om trafik från `namespace-B` till `namespace-A` .

Om du till exempel först har lagt till `staging` konfigurerar AGIC Application Gateway för att dirigera trafik till mellanlagringsplatsen. I ett senare skede kommer det `production` att leda till att AGIC Application Gateway, vilket kommer att börja dirigera trafik till `production` backend-poolen.

## <a name="restrict-access-to-namespaces"></a>Begränsa åtkomsten till namn områden
Som standard konfigurerar AGIC Application Gateway baserat på kommenterade ingångar i ett namn område. Om du vill begränsa det här beteendet kan du välja mellan följande alternativ:
  - begränsa namn områdena genom att uttryckligen definiera namn områden AGIC bör Observera via `watchNamespace` yaml-nyckeln i [Helm-config. yaml](#sample-helm-config-file)
  - Använd [roll-RoleBinding](../aks/azure-ad-rbac.md) för att begränsa AGIC till vissa namn områden

## <a name="sample-helm-config-file"></a>Exempel på Helm konfigurations fil

```yaml
    # This file contains the essential configs for the ingress controller helm chart

    # Verbosity level of the App Gateway Ingress Controller
    verbosityLevel: 3
    
    ################################################################################
    # Specify which application gateway the ingress controller will manage
    #
    appgw:
        subscriptionId: <subscriptionId>
        resourceGroup: <resourceGroupName>
        name: <applicationGatewayName>
    
        # Setting appgw.shared to "true" will create an AzureIngressProhibitedTarget CRD.
        # This prohibits AGIC from applying config for any host/path.
        # Use "kubectl get AzureIngressProhibitedTargets" to view and change this.
        shared: false
    
    ################################################################################
    # Specify which kubernetes namespace the ingress controller will watch
    # Default value is "default"
    # Leaving this variable out or setting it to blank or empty string would
    # result in Ingress Controller observing all acessible namespaces.
    #
    # kubernetes:
    #   watchNamespace: <namespace>
    
    ################################################################################
    # Specify the authentication with Azure Resource Manager
    #
    # Two authentication methods are available:
    # - Option 1: AAD-Pod-Identity (https://github.com/Azure/aad-pod-identity)
    armAuth:
        type: aadPodIdentity
        identityResourceID: <identityResourceId>
        identityClientID:  <identityClientId>
    
    ## Alternatively you can use Service Principal credentials
    # armAuth:
    #    type: servicePrincipal
    #    secretJSON: <<Generate this value with: "az ad sp create-for-rbac --subscription <subscription-uuid> --sdk-auth | base64 -w0" >>
    
    ################################################################################
    # Specify if the cluster is Kubernetes RBAC enabled or not
    rbac:
        enabled: false # true/false
    
    # Specify aks cluster related information. THIS IS BEING DEPRECATED.
    aksClusterConfiguration:
        apiServerAddress: <aks-api-server-address>
```