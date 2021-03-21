---
title: Fel sökning av Application Gateway inkommande styrenhet
description: Den här artikeln innehåller information om hur du felsöker vanliga frågor och/eller problem med Application Gateway ingångs kontroll.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: troubleshooting
ms.date: 06/18/2020
ms.author: caya
ms.openlocfilehash: f2b9f79f0914e645c736f8a577c46baa42587332
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "94874618"
---
# <a name="troubleshoot-common-questions-or-issues-with-ingress-controller"></a>Felsök vanliga frågor eller problem med ingångs kontroll

[Azure Cloud Shell](https://shell.azure.com/) är det enklaste sättet att felsöka problem med din AKS-och AGIC-installation. Starta ditt gränssnitt från [Shell.Azure.com](https://shell.azure.com/) eller genom att klicka på länken:

[![Inbäddad start](https://shell.azure.com/images/launchcloudshell.png "Starta Azure Cloud Shell")](https://shell.azure.com)


## <a name="test-with-a-simple-kubernetes-app"></a>Testa med en enkel Kubernetes-app

Stegen nedan förutsätter:
  - Du har ett AKS-kluster med avancerade nätverksfunktioner aktiverade
  - AGIC har installerats på AKS-klustret
  - Du har redan en Application Gateway på ett VNET som delas med ditt AKS-kluster

För att kontrol lera att installationen av Application Gateway + AKS + AGIC har kon figurer ATS korrekt, distribuera den enklaste möjliga appen:

```bash
cat <<EOF | kubectl apply -f -
apiVersion: v1
kind: Pod
metadata:
  name: test-agic-app-pod
  labels:
    app: test-agic-app
spec:
  containers:
  - image: "mcr.microsoft.com/dotnet/core/samples:aspnetapp"
    name: aspnetapp-image
    ports:
    - containerPort: 80
      protocol: TCP
---
apiVersion: v1
kind: Service
metadata:
  name: test-agic-app-service
spec:
  selector:
    app: test-agic-app
  ports:
  - protocol: TCP
    port: 80
    targetPort: 80
---
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: test-agic-app-ingress
  annotations:
    kubernetes.io/ingress.class: azure/application-gateway
spec:
  rules:
    - host: test.agic.contoso.com
      http:
        paths:
          - path: /
            backend:
              serviceName: test-agic-app-service
              servicePort: 80
EOF
```

Kopiera och klistra in alla rader samtidigt från skriptet ovan i en [Azure Cloud Shell](https://shell.azure.com/). Se till att hela kommandot kopieras – från och med `cat` den sista `EOF` .

![apply](./media/application-gateway-ingress-controller-troubleshooting/tsg--apply-config.png)

Efter en lyckad distribution av appen över ditt AKS-kluster kommer att ha en ny pod, tjänst och ett ingress.

Hämta listan över poddar med [Cloud Shell](https://shell.azure.com/): `kubectl get pods -o wide` .
Vi förväntar oss att en POD med namnet "test-Agic-app-pod" ska skapas. Den kommer att ha en IP-adress. Adressen måste ligga inom det virtuella nätverket för Application Gateway, som används med AKS.

![Skärm bild av bash-fönstret i Azure Cloud Shell visar en lista över poddar som innehåller test-Agic-app-Pod i listan.](./media/application-gateway-ingress-controller-troubleshooting/tsg--get-pods.png)

Hämta listan över tjänster: `kubectl get services -o wide` . Vi förväntar oss att se en tjänst med namnet "test-Agic-App-Service".

![Skärm bild av bash-fönstret i Azure Cloud Shell visar en lista över tjänster som innehåller test-Agic-app-Pod i listan.](./media/application-gateway-ingress-controller-troubleshooting/tsg--get-services.png)

Hämta listan över ingress: `kubectl get ingress` . Vi förväntar oss att en ingress-resurs med namnet "test-Agic-app-ingress" ska skapas. Resursen har ett värdnamn ' test.agic.contoso.com '.

![Skärm bild av bash-fönstret i Azure Cloud Shell visar en lista över ingångar som innehåller test-Agic-app-ingress i listan.](./media/application-gateway-ingress-controller-troubleshooting/tsg--get-ingress.png)

En av poddar kommer att vara AGIC. `kubectl get pods` visar en lista över poddar som kommer att börja med "ingress-Azure". Hämta alla loggar för den Pod med `kubectl logs <name-of-ingress-controller-pod>` för att kontrol lera att vi har haft en lyckad distribution. Vid en lyckad distribution lades följande rader till i loggen:
```
I0927 22:34:51.281437       1 process.go:156] Applied Application Gateway config in 20.461335266s
I0927 22:34:51.281585       1 process.go:165] cache: Updated with latest applied config.
I0927 22:34:51.282342       1 process.go:171] END AppGateway deployment
```

Du kan också från [Cloud Shell](https://shell.azure.com/) Hämta bara de rader som indikerar lyckad Application Gateway konfiguration med `kubectl logs <ingress-azure-....> | grep 'Applied App Gateway config in'` , där `<ingress-azure....>` ska vara det exakta namnet på AGIC-pod.

I Application Gateway används följande konfiguration:

- Lyssnare: ![ lyssnare](./media/application-gateway-ingress-controller-troubleshooting/tsg--listeners.png)

- Regel för Routning: ![ routing_rule](./media/application-gateway-ingress-controller-troubleshooting/tsg--rule.png)

- Backend-pool:
  - Det kommer att finnas en IP-adress i backend-adresspoolen och den kommer att matcha IP-adressen för Pod som vi noterade tidigare med `kubectl get pods -o wide` 
 ![ backend_pool](./media/application-gateway-ingress-controller-troubleshooting/tsg--backendpools.png)


Slutligen kan vi använda kommandot inifrån `cURL` [Cloud Shell](https://shell.azure.com/) för att upprätta en HTTP-anslutning till den nyligen distribuerade appen:

1. Används `kubectl get ingress` för att hämta den offentliga IP-adressen för Application Gateway
2. Använda `curl -I -H 'test.agic.contoso.com' <publitc-ip-address-from-previous-command>`

![Skärm bild av bash-fönstret i Azure Cloud Shell visar att ett spiral kommando har upprättat en HTTP-anslutning till test appen.](./media/application-gateway-ingress-controller-troubleshooting/tsg--curl.png)

Resultatet av `HTTP/1.1 200 OK` indikerar att systemet Application Gateway + AKS + AGIC fungerar som förväntat.


## <a name="inspect-kubernetes-installation"></a>Inspektera Kubernetes-installationen

### <a name="pods-services-ingress"></a>Poddar, tjänster, ingress
Application Gateway inkommande styrenhet (AGIC) övervakar kontinuerligt följande Kubernetes-resurser: [distribution](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/#creating-a-deployment) eller [Pod](https://kubernetes.io/docs/concepts/workloads/pods/pod/#what-is-a-pod), [tjänst](https://kubernetes.io/docs/concepts/services-networking/service/), [ingress](https://kubernetes.io/docs/concepts/services-networking/ingress/)


Följande måste finnas för att AGIC ska fungera som förväntat:
  1. AKS måste ha en eller flera felfria **poddar**.
     Verifiera detta från [Cloud Shell](https://shell.azure.com/) med `kubectl get pods -o wide --show-labels` om du har en POD med en `apsnetapp` , kan dina utdata se ut så här:
     ```bash
     delyan@Azure:~$ kubectl get pods -o wide --show-labels

     NAME                   READY   STATUS    RESTARTS   AGE   IP          NODE                       NOMINATED NODE   READINESS GATES   LABELS
     aspnetapp              1/1     Running   0          17h   10.0.0.6    aks-agentpool-35064155-1   <none>           <none>            app=aspnetapp
     ```

  2. En eller flera **tjänster** som refererar till poddar ovan via matchande `selector` etiketter.
     Verifiera detta från [Cloud Shell](https://shell.azure.com/) med `kubectl get services -o wide`
     ```bash
     delyan@Azure:~$ kubectl get services -o wide --show-labels

     NAME                TYPE        CLUSTER-IP     EXTERNAL-IP   PORT(S)   AGE   SELECTOR        LABELS
     aspnetapp           ClusterIP   10.2.63.254    <none>        80/TCP    17h   app=aspnetapp   <none>     
     ```

  3. **Ingress**, kommenterad med `kubernetes.io/ingress.class: azure/application-gateway` , som refererar till tjänsten ovan kontrol lera detta från [Cloud Shell](https://shell.azure.com/) med `kubectl get ingress -o wide --show-labels`
     ```bash
     delyan@Azure:~$ kubectl get ingress -o wide --show-labels

     NAME        HOSTS   ADDRESS   PORTS   AGE   LABELS
     aspnetapp   *                 80      17h   <none>
     ```

  4. Visa anteckningar om ingången ovan: `kubectl get ingress aspnetapp -o yaml` (Ersätt `aspnetapp` med namnet på dina ingångar)
     ```bash
     delyan@Azure:~$ kubectl get ingress aspnetapp -o yaml

     apiVersion: extensions/v1beta1
     kind: Ingress
     metadata:
       annotations:
         kubernetes.io/ingress.class: azure/application-gateway
       name: aspnetapp
     spec:
       backend:
         serviceName: aspnetapp
         servicePort: 80
     ```

     Ingress-resursen måste vara kommenterad med `kubernetes.io/ingress.class: azure/application-gateway` .
 

### <a name="verify-observed-namespace"></a>Verifiera observerat namn område

* Hämta befintliga namn rymder i Kubernetes-kluster. Vilken namnrymd körs din app i? Ser AGIC till namn området? Mer information om hur du konfigurerar observerade namn rymder hittar du i dokumentationen för [Multiple namespace support](./ingress-controller-multiple-namespace-support.md#enable-multiple-namespace-support) .

    ```bash
    # What namespaces exist on your cluster
    kubectl get namespaces
    
    # What pods are currently running
    kubectl get pods --all-namespaces -o wide
    ```


* AGIC-Pod ska finnas i `default` namn området (se kolumnen `NAMESPACE` ). En felfri pod skulle ha `Running` i `STATUS` kolumnen. Det måste finnas minst en AGIC-pod.

    ```bash
    # Get a list of the Application Gateway Ingress Controller pods
    kubectl get pods --all-namespaces --selector app=ingress-azure
    ```


* Om AGIC-Pod inte är felfri ( `STATUS` kolumn från kommandot ovan är inte `Running` ):
  - få loggar för att förstå varför: `kubectl logs <pod-name>`
  - för föregående instans av pod: `kubectl logs <pod-name> --previous`
  - Beskriv Pod för att få mer kontext: `kubectl describe pod <pod-name>`


* Har du en Kubernetes [-tjänst](https://kubernetes.io/docs/concepts/services-networking/service/) och [ingress](https://kubernetes.io/docs/concepts/services-networking/ingress/) -resurser?
    
    ```bash
    # Get all services across all namespaces
    kubectl get service --all-namespaces -o wide
    
    # Get all ingress resources across all namespaces
    kubectl get ingress --all-namespaces -o wide
    ```


* Är dina [](https://kubernetes.io/docs/concepts/services-networking/ingress/) ingångs kommentarer med: `kubernetes.io/ingress.class: azure/application-gateway` ? AGIC kommer bara att titta på Kubernetes ingress-resurser som har anteckningen.
    
    ```bash
    # Get the YAML definition of a particular ingress resource
    kubectl get ingress --namespace  <which-namespace?>  <which-ingress?>  -o yaml
    ```


* AGIC avger Kubernetes händelser för vissa kritiska fel. Du kan visa följande:
  - i terminalen via `kubectl get events --sort-by=.metadata.creationTimestamp`
  - i webbläsaren med [Kubernetes webb gränssnitt (instrument panel)](https://kubernetes.io/docs/tasks/access-application-cluster/web-ui-dashboard/)


## <a name="logging-levels"></a>Loggningsnivåer

AGIC har 3 loggnings nivåer. Nivå 1 är standard och visar ett minimalt antal logg rader.
Nivå 5, å andra sidan, visar alla loggar, inklusive det sanerade innehållet i konfigurationen som tillämpas på ARM.

Kubernetes-communityn har upprättat 9 loggnings nivåer för [kubectl](https://kubernetes.io/docs/reference/kubectl/cheatsheet/#kubectl-output-verbosity-and-debugging) -verktyget. I den här databasen använder vi 3 av dessa, med liknande semantik:


| Utförlighet | Beskrivning |
|-----------|-------------|
|  1        | Standard loggnings nivå; visar information om Start, varningar och fel |
|  3        | Utökad information om händelser och ändringar. listor över skapade objekt |
|  5        | Loggar konverterade objekt; visar att den sanerade JSON-konfigurationen tillämpas på ARM |


Detalj nivån är justerbar via `verbosityLevel` variabeln i filen [Helm-config. yaml](#sample-helm-config-file) . Öka utförlig nivå till `5` för att hämta JSON-konfigurationen som skickas till [arm](../azure-resource-manager/management/overview.md):
  - Lägg till `verbosityLevel: 5` på en rad själva i [Helm-config. yaml](#sample-helm-config-file) och installera om
  - Hämta loggar med `kubectl logs <pod-name>`

### <a name="sample-helm-config-file"></a>Exempel på Helm konfigurations fil
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