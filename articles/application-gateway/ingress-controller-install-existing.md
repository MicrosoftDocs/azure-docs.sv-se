---
title: Skapa en ingångs kontroll enhet med en befintlig Application Gateway
description: Den här artikeln innehåller information om hur du distribuerar en Application Gateway ingress-styrenhet med en befintlig Application Gateway.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: how-to
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: 2d64766c754c0ea104ae83fde799a514e9da6d68
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "97693739"
---
# <a name="install-an-application-gateway-ingress-controller-agic-using-an-existing-application-gateway"></a>Installera en Application Gateway ingress-styrenhet (AGIC) med hjälp av en befintlig Application Gateway

Application Gateway ingångs styrenhet (AGIC) är en POD i ditt Kubernetes-kluster.
AGIC övervakar Kubernetes [ingress](https://kubernetes.io/docs/concepts/services-networking/ingress/) -resurser och skapar och tillämpar Application Gateway config baserat på status för Kubernetes-klustret.

## <a name="outline"></a>Beskriver
- [Förutsättningar](#prerequisites)
- [Azure Resource Manager autentisering (ARM)](#azure-resource-manager-authentication)
    - Alternativ 1: [Konfigurera AAD-Pod – identitet](#set-up-aad-pod-identity) och skapa Azure-identitet för vapen
    - Alternativ 2: [använda ett huvud namn för tjänsten](#using-a-service-principal)
- [Installera ingress-styrenheten med Helm](#install-ingress-controller-as-a-helm-chart)
- [Flera kluster/delade Application Gateway](#multi-cluster--shared-application-gateway): installera AGIC i en miljö där Application Gateway delas mellan ett eller flera AKS-kluster och/eller andra Azure-komponenter.

## <a name="prerequisites"></a>Förutsättningar
Det här dokumentet förutsätter att du redan har följande verktyg och infrastruktur installerade:
- [AKS](https://azure.microsoft.com/services/kubernetes-service/) med [avancerade nätverksfunktioner](../aks/configure-azure-cni.md) aktiverade
- [Application Gateway v2](./tutorial-autoscale-ps.md) i samma virtuella nätverk som AKS
- [AAD Pod-identitet](https://github.com/Azure/aad-pod-identity) installerad på ditt AKS-kluster
- [Cloud Shell](https://shell.azure.com/) är Azure Shell-miljön, som har `az` CLI, `kubectl` och `helm` installerat. De här verktygen krävs för kommandona nedan.

__Säkerhetskopiera din Application gateways konfiguration__ innan du installerar AGIC:
  1. använda [Azure Portal](https://portal.azure.com/) navigera till din `Application Gateway` instans
  2. från `Export template` Klicka `Download`

Zip-filen som du laddade ned kommer att ha JSON-mallar, bash-och PowerShell-skript som du kan använda för att återställa App gatewayen, om det blir nödvändigt

## <a name="install-helm"></a>Installera Helm
[Helm](../aks/kubernetes-helm.md) är en paket hanterare för Kubernetes. Vi använder den för att installera `application-gateway-kubernetes-ingress` paketet.
Använd [Cloud Shell](https://shell.azure.com/) för att installera Helm:

1. Installera [Helm](../aks/kubernetes-helm.md) och kör följande för att lägga till `application-gateway-kubernetes-ingress` Helm-paketet:

    - *KUBERNETES RBAC aktive rad* AKS-kluster

    ```bash
    kubectl create serviceaccount --namespace kube-system tiller-sa
    kubectl create clusterrolebinding tiller-cluster-rule --clusterrole=cluster-admin --serviceaccount=kube-system:tiller-sa
    helm init --tiller-namespace kube-system --service-account tiller-sa
    ```

    - *KUBERNETES RBAC har inaktiverats* AKS-kluster

    ```bash
    helm init
    ```

1. Lägg till AGIC Helm-lagringsplatsen:
    ```bash
    helm repo add application-gateway-kubernetes-ingress https://appgwingress.blob.core.windows.net/ingress-azure-helm-package/
    helm repo update
    ```

## <a name="azure-resource-manager-authentication"></a>Azure Resource Manager autentisering

AGIC kommunicerar med Kubernetes API-servern och Azure Resource Manager. Den kräver en identitet för att få åtkomst till dessa API: er.

## <a name="set-up-aad-pod-identity"></a>Konfigurera AAD Pod-identitet

[AAD Pod-identiteten](https://github.com/Azure/aad-pod-identity) är en kontrollant som liknar AGIC, som också körs på din AKS. Den binder Azure Active Directory identiteter till din Kubernetes-poddar. Identitet krävs för att ett program i en Kubernetes-Pod ska kunna kommunicera med andra Azure-komponenter. I det här fallet behöver vi auktorisering för AGIC-Pod för att göra HTTP-förfrågningar till [arm](../azure-resource-manager/management/overview.md).

Följ [anvisningarna för AAD Pod-identitets installationen](https://github.com/Azure/aad-pod-identity#deploy-the-azure-aad-identity-infra) för att lägga till den här komponenten i din AKS.

Härnäst måste vi skapa en Azure-identitet och ge IT-behörighet ARM.
Använd [Cloud Shell](https://shell.azure.com/) för att köra alla följande kommandon och skapa en identitet:

1. Skapa en Azure-identitet **i samma resurs grupp som AKS-noderna**. Det är viktigt att du väljer rätt resurs grupp. Resurs gruppen som krävs i kommandot nedan är *inte* den som refereras till i fönstret AKS Portal. Det här är resurs gruppen för de `aks-agentpool` virtuella datorerna. Vanligt vis börjar resurs gruppen med `MC_` och innehåller namnet på din AKS. Till exempel: `MC_resourceGroup_aksABCD_westus`

    ```azurecli
    az identity create -g <agent-pool-resource-group> -n <identity-name>
    ```

1. För roll tilldelnings kommandona nedan måste du hämta `principalId` för den nya identiteten:

    ```azurecli
    az identity show -g <resourcegroup> -n <identity-name>
    ```

1. Ge identitets `Contributor` åtkomst till din Application Gateway. För detta behöver du ID: t för Application Gateway, som ser ut ungefär så här: `/subscriptions/A/resourceGroups/B/providers/Microsoft.Network/applicationGateways/C`

    Hämta listan med Application Gateway-ID: n i din prenumeration med: `az network application-gateway list --query '[].id'`

    ```azurecli
    az role assignment create \
        --role Contributor \
        --assignee <principalId> \
        --scope <App-Gateway-ID>
    ```

1. Ge identitets `Reader` åtkomst till Application Gateway resurs gruppen. Resurs gruppens ID skulle se ut så här: `/subscriptions/A/resourceGroups/B` . Du kan hämta alla resurs grupper med: `az group list --query '[].id'`

    ```azurecli
    az role assignment create \
        --role Reader \
        --assignee <principalId> \
        --scope <App-Gateway-Resource-Group-ID>
    ```

## <a name="using-a-service-principal"></a>Använda ett huvud namn för tjänsten
Det är också möjligt att ge AGIC åtkomst till ARM via en Kubernetes-hemlighet.

1. Skapa en Active Directory tjänstens huvud namn och koda med base64. Base64-kodning krävs för att JSON-blobben ska kunna sparas i Kubernetes.

```azurecli
az ad sp create-for-rbac --sdk-auth | base64 -w0
```

2. Lägg till den base64-kodade JSON-blobben i `helm-config.yaml` filen. Mer information `helm-config.yaml` finns i nästa avsnitt.
```yaml
armAuth:
    type: servicePrincipal
    secretJSON: <Base64-Encoded-Credentials>
```

## <a name="install-ingress-controller-as-a-helm-chart"></a>Installera ingress-kontrollant som ett Helm-diagram
I de första stegen installerar vi Helm till ditt Kubernetes-kluster. Använd [Cloud Shell](https://shell.azure.com/) för att installera AGIC Helm-paketet:

1. Lägg till `application-gateway-kubernetes-ingress` Helm-lagrings platsen och utför en Helm-uppdatering

    ```bash
    helm repo add application-gateway-kubernetes-ingress https://appgwingress.blob.core.windows.net/ingress-azure-helm-package/
    helm repo update
    ```

1. Ladda ned Helm-config. yaml, som kommer att konfigurera AGIC:
    ```bash
    wget https://raw.githubusercontent.com/Azure/application-gateway-kubernetes-ingress/master/docs/examples/sample-helm-config.yaml -O helm-config.yaml
    ```
    Eller kopiera YAML-filen nedan: 
    
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
    #    secretJSON: <<Generate this value with: "az ad sp create-for-rbac --sdk-auth | base64 -w0" >>
    
    ################################################################################
    # Specify if the cluster is Kubernetes RBAC enabled or not
    rbac:
        enabled: false # true/false
    
    # Specify aks cluster related information. THIS IS BEING DEPRECATED.
    aksClusterConfiguration:
        apiServerAddress: <aks-api-server-address>
    ```

1. Redigera Helm-config. yaml och fyll i värdena för `appgw` och `armAuth` .
    ```bash
    nano helm-config.yaml
    ```

    > [!NOTE] 
    > `<identity-resource-id>`Egenskaperna och `<identity-client-id>` är egenskaperna för den Azure AD-identitet som du ställer in i föregående avsnitt. Du kan hämta den här informationen genom att köra följande kommando: `az identity show -g <resourcegroup> -n <identity-name>` , där `<resourcegroup>` är den resurs grupp där AKS Cluster-objektet på den översta nivån, Application Gateway och hanterad identifiering distribueras.

1. Installera Helm-diagrammet `application-gateway-kubernetes-ingress` med `helm-config.yaml` konfigurationen från föregående steg

    ```bash
    helm install -f <helm-config.yaml> application-gateway-kubernetes-ingress/ingress-azure
    ```

    Du kan också kombinera `helm-config.yaml` kommandot och Helm i ett enda steg:
    ```bash
    helm install ./helm/ingress-azure \
         --name ingress-azure \
         --namespace default \
         --debug \
         --set appgw.name=applicationgatewayABCD \
         --set appgw.resourceGroup=your-resource-group \
         --set appgw.subscriptionId=subscription-uuid \
         --set appgw.shared=false \
         --set armAuth.type=servicePrincipal \
         --set armAuth.secretJSON=$(az ad sp create-for-rbac --sdk-auth | base64 -w0) \
         --set rbac.enabled=true \
         --set verbosityLevel=3 \
         --set kubernetes.watchNamespace=default \
         --set aksClusterConfiguration.apiServerAddress=aks-abcdefg.hcp.westus2.azmk8s.io
    ```

1. Kontrol lera loggen för den nyligen skapade Pod för att kontrol lera om den har startats korrekt

Läs [den här instruktions guiden](ingress-controller-expose-service-over-http-https.md) för att förstå hur du kan exponera en AKS-tjänst via http eller https, till Internet med hjälp av en Azure Application Gateway.



## <a name="multi-cluster--shared-application-gateway"></a>Flera kluster/delade Application Gateway
Som standard antar AGIC fullständig ägande rätt till den Application Gateway den är länkad till. AGIC version 0.8.0 och senare kan dela en enda Application Gateway med andra Azure-komponenter. Vi kan till exempel använda samma Application Gateway för en app som är värd för den virtuella datorns skalnings uppsättning samt ett AKS-kluster.

__Säkerhetskopiera din Application gateways konfiguration__ innan du aktiverar den här inställningen:
  1. använda [Azure Portal](https://portal.azure.com/) navigera till din `Application Gateway` instans
  2. från `Export template` Klicka `Download`

Zip-filen som du laddade ned kommer att ha JSON-mallar, bash-och PowerShell-skript som du kan använda för att återställa Application Gateway

### <a name="example-scenario"></a>Exempel på ett scenario
Nu ska vi titta på en tänkt Application Gateway som hanterar trafik för två webbplatser:
  - `dev.contoso.com` -värdbaserade på en ny AKS, med hjälp av Application Gateway och AGIC
  - `prod.contoso.com` -värdbaserad [skalnings uppsättning för en virtuell Azure-dator](https://azure.microsoft.com/services/virtual-machine-scale-sets/)

Med standardinställningarna antar AGIC 100% ägande av den Application Gateway den pekar på. AGIC skriver över alla app Gateway-konfigurationer. Om vi skulle skapa en lyssnare för `prod.contoso.com` (på Application Gateway) manuellt, utan att definiera den i Kubernetes ingress, kommer AGIC att ta bort `prod.contoso.com` konfigurationen inom några sekunder.

För att installera AGIC och även fungera `prod.contoso.com` från våra datorer för skalnings uppsättningar för virtuella datorer måste vi begränsa AGIC till konfiguration `dev.contoso.com` . Detta underlättar genom att instansiera följande [CRD](https://kubernetes.io/docs/concepts/extend-kubernetes/api-extension/custom-resources/):

```bash
cat <<EOF | kubectl apply -f -
apiVersion: "appgw.ingress.k8s.io/v1"
kind: AzureIngressProhibitedTarget
metadata:
  name: prod-contoso-com
spec:
  hostname: prod.contoso.com
EOF
```

Kommandot ovan skapar ett `AzureIngressProhibitedTarget` objekt. Detta gör AGIC (version 0.8.0 och senare) medveten om förekomsten av Application Gateway config för `prod.contoso.com` och uttryckligen instruerar den att undvika att ändra konfigurationen som är relaterad till värddatorn.


### <a name="enable-with-new-agic-installation"></a>Aktivera med ny AGIC-installation
Om du vill begränsa AGIC (version 0.8.0 och senare) till en delmängd av Application Gateway-konfigurationen ändrar du `helm-config.yaml` mallen.
Under `appgw:` avsnittet lägger du till `shared` nyckel och anger den till `true` .

```yaml
appgw:
    subscriptionId: <subscriptionId>    # existing field
    resourceGroup: <resourceGroupName>  # existing field
    name: <applicationGatewayName>      # existing field
    shared: true                        # <<<<< Add this field to enable shared Application Gateway >>>>>
```

Tillämpa Helm-ändringarna:
  1. Se till att `AzureIngressProhibitedTarget` CRD installeras med:
      ```bash
      kubectl apply -f https://raw.githubusercontent.com/Azure/application-gateway-kubernetes-ingress/ae695ef9bd05c8b708cedf6ff545595d0b7022dc/crds/AzureIngressProhibitedTarget.yaml
      ```
  2. Uppdatera Helm:
      ```bash
      helm upgrade \
          --recreate-pods \
          -f helm-config.yaml \
          ingress-azure application-gateway-kubernetes-ingress/ingress-azure
      ```

Som ett resultat kommer din AKS att ha en ny instans med `AzureIngressProhibitedTarget` namnet `prohibit-all-targets` :
```bash
kubectl get AzureIngressProhibitedTargets prohibit-all-targets -o yaml
```

Objektet `prohibit-all-targets` , som namnet antyder, förhindrar AGIC från att ändra config för *alla* värdar och Sök vägar.
Helm installera med `appgw.shared=true` distribuerar AGIC, men gör inga ändringar i Application Gateway.


### <a name="broaden-permissions"></a>Utöka behörigheter
Eftersom Helm med `appgw.shared=true` och standard `prohibit-all-targets` blockeras AGIC från att tillämpa någon konfiguration.

Bredda AGIC-behörigheter med:
1. Skapa en ny `AzureIngressProhibitedTarget` med din speciella konfiguration:
    ```bash
    cat <<EOF | kubectl apply -f -
    apiVersion: "appgw.ingress.k8s.io/v1"
    kind: AzureIngressProhibitedTarget
    metadata:
      name: your-custom-prohibitions
    spec:
      hostname: your.own-hostname.com
    EOF
    ```

2. När du har skapat ett eget anpassat förbud kan du ta bort standard alternativet, som är för brett:

    ```bash
    kubectl delete AzureIngressProhibitedTarget prohibit-all-targets
    ```

### <a name="enable-for-an-existing-agic-installation"></a>Aktivera för en befintlig AGIC-installation
Vi antar att vi redan har en fungerande AKS, Application Gateway och konfigurerat AGIC i vårt kluster. Vi har ett ingress för `prod.contoso.com` och betjänar trafik för den från AKS. Vi vill lägga till `staging.contoso.com` i vår befintliga Application Gateway, men du måste vara värd för den på en [virtuell dator](https://azure.microsoft.com/services/virtual-machines/). Vi kommer att återanvända den befintliga Application Gateway och manuellt konfigurera en lyssnare och backend-pooler för `staging.contoso.com` . Men att anpassa Application Gateway config manuellt (via [Portal](https://portal.azure.com), [arm-API: er](/rest/api/resources/) eller [terraform](https://www.terraform.io/)) hamnar i konflikt med AGICs antaganden om full ägande. Strax efter att vi tillämpar ändringar skriver AGIC över eller tar bort dem.

Vi kan förhindra AGIC från att göra ändringar i en delmängd av konfigurationen.

1. Skapa ett `AzureIngressProhibitedTarget` objekt:
    ```bash
    cat <<EOF | kubectl apply -f -
    apiVersion: "appgw.ingress.k8s.io/v1"
    kind: AzureIngressProhibitedTarget
    metadata:
      name: manually-configured-staging-environment
    spec:
      hostname: staging.contoso.com
    EOF
    ```

2. Visa det nyligen skapade objektet:
    ```bash
    kubectl get AzureIngressProhibitedTargets
    ```

3. Ändra Application Gateway config via portal – Lägg till lyssnare, routningsregler, Server delar osv. Det nya objektet som vi skapade ( `manually-configured-staging-environment` ) förhindrar att AGIC skriver över Application Gateway konfiguration som är relaterad till `staging.contoso.com` .
