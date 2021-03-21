---
title: Skapa en ingångs kontroll enhet med en ny Application Gateway
description: Den här artikeln innehåller information om hur du distribuerar en Application Gateway ingress-kontrollant med en ny Application Gateway.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: how-to
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: 8be5ac75e2da3eaeae300fd36e152a24c9777e64
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "99593747"
---
# <a name="how-to-install-an-application-gateway-ingress-controller-agic-using-a-new-application-gateway"></a>Så här installerar du en Application Gateway ingress-styrenhet (AGIC) med en ny Application Gateway

Anvisningarna nedan förutsätter Application Gateway ingress Controller (AGIC) installeras i en miljö utan befintliga komponenter.

## <a name="required-command-line-tools"></a>Nödvändiga kommando rads verktyg

Vi rekommenderar att du använder [Azure Cloud Shell](https://shell.azure.com/) för alla kommando rads åtgärder nedan. Starta ditt gränssnitt från shell.azure.com eller genom att klicka på länken:

[![Inbäddad start](https://shell.azure.com/images/launchcloudshell.png "Starta Azure Cloud Shell")](https://shell.azure.com)

Du kan också starta Cloud Shell från Azure Portal med följande ikon:

![Portal lansering](./media/application-gateway-ingress-controller-install-new/portal-launch-icon.png)

Din [Azure Cloud Shell](https://shell.azure.com/) har redan alla verktyg som krävs. Om du väljer att använda en annan miljö bör du kontrol lera att följande kommando rads verktyg är installerade:

* `az` – Azure CLI: [installations anvisningar](/cli/azure/install-azure-cli)
* `kubectl` -Kubernetes kommando rads verktyg: [installations anvisningar](https://kubernetes.io/docs/tasks/tools/install-kubectl)
* `helm` – Kubernetes Package Manager: [installations anvisningar](https://github.com/helm/helm/releases/latest)
* `jq` – kommando rads-JSON-processor: [Installationsinstruktioner](https://stedolan.github.io/jq/download/)


## <a name="create-an-identity"></a>Skapa en identitet

Följ stegen nedan för att skapa ett [huvud namns objekt](../active-directory/develop/app-objects-and-service-principals.md#service-principal-object)för en Azure Active Directory (AAD). Registrera `appId` -,- `password` och `objectId` -värden – dessa kommer att användas i följande steg.

1. Skapa AD-tjänstens huvud namn ([Läs mer om Azure RBAC](../role-based-access-control/overview.md)):
    ```azurecli
    az ad sp create-for-rbac --skip-assignment -o json > auth.json
    appId=$(jq -r ".appId" auth.json)
    password=$(jq -r ".password" auth.json)
    ```
    `appId`Värdena och `password` från JSON-utdata kommer att användas i följande steg


1. Använd `appId` från föregående kommandos utdata för att hämta `objectId` det nya tjänstens huvud namn:
    ```azurecli
    objectId=$(az ad sp show --id $appId --query "objectId" -o tsv)
    ```
    Utdata från det här kommandot är `objectId` , som kommer att användas i Azure Resource Manager mal len nedan

1. Skapa parameter filen som ska användas i Azure Resource Manager mall distributionen senare.
    ```bash
    cat <<EOF > parameters.json
    {
      "aksServicePrincipalAppId": { "value": "$appId" },
      "aksServicePrincipalClientSecret": { "value": "$password" },
      "aksServicePrincipalObjectId": { "value": "$objectId" },
      "aksEnableRBAC": { "value": false }
    }
    EOF
    ```
    Om du vill distribuera ett **KUBERNETES RBAC** -aktiverat kluster ställer du in `aksEnableRBAC` fältet på `true`

## <a name="deploy-components"></a>Distribuera komponenter
I det här steget läggs följande komponenter till i din prenumeration:

- [Azure Kubernetes Service](../aks/intro-kubernetes.md)
- [Application Gateway](./overview.md) v2
- [Virtual Network](../virtual-network/virtual-networks-overview.md) med två [undernät](../virtual-network/virtual-networks-overview.md)
- [Offentlig IP-adress](../virtual-network/virtual-network-public-ip-address.md)
- [Hanterad identitet](../active-directory/managed-identities-azure-resources/overview.md), som kommer att användas av [AAD Pod-identitet](https://github.com/Azure/aad-pod-identity/blob/master/README.md)

1. Ladda ned Azure Resource Manager-mallen och ändra mallen efter behov.
    ```bash
    wget https://raw.githubusercontent.com/Azure/application-gateway-kubernetes-ingress/master/deploy/azuredeploy.json -O template.json
    ```

1. Distribuera Azure Resource Manager-mallen med hjälp av `az cli` . Det kan ta upp till 5 minuter.
    ```azurecli
    resourceGroupName="MyResourceGroup"
    location="westus2"
    deploymentName="ingress-appgw"

    # create a resource group
    az group create -n $resourceGroupName -l $location

    # modify the template as needed
    az deployment group create \
            -g $resourceGroupName \
            -n $deploymentName \
            --template-file template.json \
            --parameters parameters.json
    ```

1. När distributionen är färdig kan du hämta distributions resultatet till en fil med namnet `deployment-outputs.json` .
    ```azurecli
    az deployment group show -g $resourceGroupName -n $deploymentName --query "properties.outputs" -o json > deployment-outputs.json
    ```

## <a name="set-up-application-gateway-ingress-controller"></a>Konfigurera Application Gateway ingress-kontrollant

Med anvisningarna i föregående avsnitt skapade vi och konfigurerade ett nytt AKS-kluster och en Application Gateway. Vi är nu redo att distribuera en exempel App och en ingångs kontroll till vår nya Kubernetes-infrastruktur.

### <a name="setup-kubernetes-credentials"></a>Konfigurera Kubernetes-autentiseringsuppgifter
För följande steg behöver vi Setup [kubectl](https://kubectl.docs.kubernetes.io/) -kommandot som vi ska använda för att ansluta till vårt nya Kubernetes-kluster. [Cloud Shell](https://shell.azure.com/) har `kubectl` redan installerats. Vi kommer `az` att använda CLI för att hämta autentiseringsuppgifter för Kubernetes.

Hämta autentiseringsuppgifter för din nyligen distribuerade AKS ([Läs mer](../aks/kubernetes-walkthrough.md#connect-to-the-cluster)):
```azurecli
# use the deployment-outputs.json created after deployment to get the cluster name and resource group name
aksClusterName=$(jq -r ".aksClusterName.value" deployment-outputs.json)
resourceGroupName=$(jq -r ".resourceGroupName.value" deployment-outputs.json)

az aks get-credentials --resource-group $resourceGroupName --name $aksClusterName
```

### <a name="install-aad-pod-identity"></a>Installera AAD Pod-identitet
  Azure Active Directory Pod identitet ger tokenbaserad åtkomst till [Azure Resource Manager (arm)](../azure-resource-manager/management/overview.md).

  [AAD Pod-identitet](https://github.com/Azure/aad-pod-identity) kommer att lägga till följande komponenter i ditt Kubernetes-kluster:
   * Kubernetes [CRDs](https://kubernetes.io/docs/tasks/access-kubernetes-api/custom-resources/custom-resource-definitions/): `AzureIdentity` , `AzureAssignedIdentity` , `AzureIdentityBinding`
   * Komponenten [hanterad identitets styrenhet (MIC)](https://github.com/Azure/aad-pod-identity#managed-identity-controllermic)
   * [NMI-komponent (Node Managed Identity)](https://github.com/Azure/aad-pod-identity#node-managed-identitynmi)


Så här installerar du AAD Pod-identiteten till klustret:

   - *KUBERNETES RBAC aktive rad* AKS-kluster

     ```bash
     kubectl create -f https://raw.githubusercontent.com/Azure/aad-pod-identity/master/deploy/infra/deployment-rbac.yaml
     ```

   - *KUBERNETES RBAC har inaktiverats* AKS-kluster

     ```bash
     kubectl create -f https://raw.githubusercontent.com/Azure/aad-pod-identity/master/deploy/infra/deployment.yaml
     ```

### <a name="install-helm"></a>Installera Helm
[Helm](../aks/kubernetes-helm.md) är en paket hanterare för Kubernetes. Vi använder den för att installera `application-gateway-kubernetes-ingress` paketet:

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

### <a name="install-ingress-controller-helm-chart"></a>Installera Helm-diagram för ingress-styrenhet

1. Använd `deployment-outputs.json` filen som skapades ovan och skapa följande variabler.
    ```bash
    applicationGatewayName=$(jq -r ".applicationGatewayName.value" deployment-outputs.json)
    resourceGroupName=$(jq -r ".resourceGroupName.value" deployment-outputs.json)
    subscriptionId=$(jq -r ".subscriptionId.value" deployment-outputs.json)
    identityClientId=$(jq -r ".identityClientId.value" deployment-outputs.json)
    identityResourceId=$(jq -r ".identityResourceId.value" deployment-outputs.json)
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
    #    secretJSON: <<Generate this value with: "az ad sp create-for-rbac --subscription <subscription-uuid> --sdk-auth | base64 -w0" >>
    
    ################################################################################
    # Specify if the cluster is Kubernetes RBAC enabled or not
    rbac:
        enabled: false # true/false
    
    # Specify aks cluster related information. THIS IS BEING DEPRECATED.
    aksClusterConfiguration:
        apiServerAddress: <aks-api-server-address>
    ```

1. Redigera den nyligen nedladdade Helm-config. yaml och fyll i avsnitten `appgw` och `armAuth` .
    ```bash
    sed -i "s|<subscriptionId>|${subscriptionId}|g" helm-config.yaml
    sed -i "s|<resourceGroupName>|${resourceGroupName}|g" helm-config.yaml
    sed -i "s|<applicationGatewayName>|${applicationGatewayName}|g" helm-config.yaml
    sed -i "s|<identityResourceId>|${identityResourceId}|g" helm-config.yaml
    sed -i "s|<identityClientId>|${identityClientId}|g" helm-config.yaml

    # You can further modify the helm config to enable/disable features
    nano helm-config.yaml
    ```

   Värden:
     - `verbosityLevel`: Anger utförlig nivå för AGIC-loggnings infrastrukturen. Se [loggnings nivåer](https://github.com/Azure/application-gateway-kubernetes-ingress/blob/463a87213bbc3106af6fce0f4023477216d2ad78/docs/troubleshooting.md#logging-levels) för möjliga värden.
     - `appgw.subscriptionId`: ID för Azure-prenumerationen där Application Gateway finns. Exempel: `a123b234-a3b4-557d-b2df-a0bc12de1234`
     - `appgw.resourceGroup`: Namnet på den Azure-resurs grupp där Application Gateway skapades. Exempel: `app-gw-resource-group`
     - `appgw.name`: Namnet på Application Gateway. Exempel: `applicationgatewayd0f0`
     - `appgw.shared`: Den här booleska flaggan ska vara standard till `false` . Ange till `true` om du behöver en [delad Application Gateway](https://github.com/Azure/application-gateway-kubernetes-ingress/blob/072626cb4e37f7b7a1b0c4578c38d1eadc3e8701/docs/setup/install-existing.md#multi-cluster--shared-app-gateway).
     - `kubernetes.watchNamespace`: Ange det namn område som AGIC ska titta på. Detta kan vara ett enskilt sträng värde eller en kommaavgränsad lista över namn områden.
    - `armAuth.type`: kan vara `aadPodIdentity` eller `servicePrincipal`
    - `armAuth.identityResourceID`: Resurs-ID för den Azure-hanterade identiteten
    - `armAuth.identityClientId`: Klient-ID för identiteten. Se nedan för mer information om identitet
    - `armAuth.secretJSON`: Krävs endast när den hemliga typen för tjänst objekt väljs (när `armAuth.type` har angetts till `servicePrincipal` ) 


   > [!NOTE]
   > `identityResourceID`Och `identityClientID` är värden som skapades under stegen för att [distribuera komponenter](ingress-controller-install-new.md#deploy-components) och kan hämtas igen med följande kommando:
   > ```azurecli
   > az identity show -g <resource-group> -n <identity-name>
   > ```
   > `<resource-group>` i kommandot ovan visas resurs gruppen för din Application Gateway. `<identity-name>` är namnet på den skapade identiteten. Alla identiteter för en angiven prenumeration kan listas med: `az identity list`


1. Installera paketet Application Gateway ingress-kontrollant:

    ```bash
    helm install -f helm-config.yaml application-gateway-kubernetes-ingress/ingress-azure
    ```

## <a name="install-a-sample-app"></a>Installera en exempel App
Nu när vi har Application Gateway, AKS och AGIC installerat kan vi installera en exempel app via [Azure Cloud Shell](https://shell.azure.com/):

```yaml
cat <<EOF | kubectl apply -f -
apiVersion: v1
kind: Pod
metadata:
  name: aspnetapp
  labels:
    app: aspnetapp
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
  name: aspnetapp
spec:
  selector:
    app: aspnetapp
  ports:
  - protocol: TCP
    port: 80
    targetPort: 80

---

apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: aspnetapp
  annotations:
    kubernetes.io/ingress.class: azure/application-gateway
spec:
  rules:
  - http:
      paths:
      - path: /
        backend:
          serviceName: aspnetapp
          servicePort: 80
EOF
```

Du kan också:

* Hämta YAML-filen ovan:

```bash
curl https://raw.githubusercontent.com/Azure/application-gateway-kubernetes-ingress/master/docs/examples/aspnetapp.yaml -o aspnetapp.yaml
```

* Använd YAML-filen:

```bash
kubectl apply -f aspnetapp.yaml
```


## <a name="other-examples"></a>Andra exempel
Den här [instruktions guiden](ingress-controller-expose-service-over-http-https.md) innehåller fler exempel på hur du exponerar en AKS-tjänst via http eller https, till Internet med Application Gateway.
