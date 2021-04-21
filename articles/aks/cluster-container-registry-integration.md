---
title: Integrera Azure Container Registry med Azure Kubernetes Service
description: Lär dig hur du integrerar Azure Kubernetes Service (AKS) med Azure Container Registry (ACR)
services: container-service
manager: gwallace
ms.topic: article
ms.date: 01/08/2021
ms.openlocfilehash: ab8065a14aac9e798bfe7d632aa5b33c44706190
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107775836"
---
# <a name="authenticate-with-azure-container-registry-from-azure-kubernetes-service"></a>Autentisera med Azure Container Registry från Azure Kubernetes Service

När du använder Azure Container Registry (ACR) med Azure Kubernetes Service (AKS) måste en autentiseringsmekanism upprättas. Den här åtgärden implementeras som en del av CLI- och portalupplevelsen genom att du beviljar nödvändiga behörigheter till din ACR. Den här artikeln innehåller exempel på hur du konfigurerar autentisering mellan dessa två Azure-tjänster. 

Du kan konfigurera AKS till ACR-integrering med några enkla kommandon med Azure CLI. Den här integreringen tilldelar AcrPull-rollen till den hanterade identitet som är associerad med AKS-klustret.

> [!NOTE]
> Den här artikeln beskriver automatisk autentisering mellan AKS och ACR. Om du behöver hämta en avbildning från ett privat externt register använder du en [pull-hemlighet för avbildningar.][Image Pull Secret]

## <a name="before-you-begin"></a>Innan du börjar

De här exemplen kräver:

* **Rollen** ägare **eller Azure-kontoadministratör** i **Azure-prenumerationen**
* Azure CLI version 2.7.0 eller senare

För att undvika att **behöva** ha rollen Ägare eller **Azure-kontoadministratör** kan du konfigurera en hanterad identitet manuellt eller använda en befintlig hanterad identitet för att autentisera ACR från AKS. Mer information finns i Använda en [hanterad azure-identitet för att autentisera till ett Azure-containerregister.](../container-registry/container-registry-authentication-managed-identity.md)

## <a name="create-a-new-aks-cluster-with-acr-integration"></a>Skapa ett nytt AKS-kluster med ACR-integrering

Du kan konfigurera AKS- och ACR-integrering när du skapar AKS-klustret.  Om du vill tillåta att ett AKS-kluster interagerar med ACR Azure Active Directory **en hanterad** identitet. Med följande CLI-kommando kan du auktorisera en befintlig ACR i din prenumeration och konfigurera lämplig **ACRPull-roll** för den hanterade identiteten. Ange giltiga värden för dina parametrar nedan.

```azurecli
# set this to the name of your Azure Container Registry.  It must be globally unique
MYACR=myContainerRegistry

# Run the following line to create an Azure Container Registry if you do not already have one
az acr create -n $MYACR -g myContainerRegistryResourceGroup --sku basic

# Create an AKS cluster with ACR integration
az aks create -n myAKSCluster -g myResourceGroup --generate-ssh-keys --attach-acr $MYACR
```

Du kan också ange ACR-namnet med hjälp av ett ACR-resurs-ID, som har följande format:

`/subscriptions/\<subscription-id\>/resourceGroups/\<resource-group-name\>/providers/Microsoft.ContainerRegistry/registries/\<name\>`

> [!NOTE]
> Om du använder en ACR som finns i en annan prenumeration än ditt AKS-kluster använder du ACR-resurs-ID:t när du kopplar eller kopplar från ett AKS-kluster.

```azurecli
az aks create -n myAKSCluster -g myResourceGroup --generate-ssh-keys --attach-acr /subscriptions/<subscription-id>/resourceGroups/myContainerRegistryResourceGroup/providers/Microsoft.ContainerRegistry/registries/myContainerRegistry
```

Det här steget kan ta flera minuter att slutföra.

## <a name="configure-acr-integration-for-existing-aks-clusters"></a>Konfigurera ACR-integrering för befintliga AKS-kluster

Integrera ett befintligt ACR med befintliga AKS-kluster genom att ange giltiga värden för **acr-name** **eller acr-resource-id** enligt nedan.

```azurecli
az aks update -n myAKSCluster -g myResourceGroup --attach-acr <acr-name>
```

Eller

```azurecli
az aks update -n myAKSCluster -g myResourceGroup --attach-acr <acr-resource-id>
```

Du kan också ta bort integreringen mellan ett ACR och ett AKS-kluster med följande

```azurecli
az aks update -n myAKSCluster -g myResourceGroup --detach-acr <acr-name>
```

eller

```azurecli
az aks update -n myAKSCluster -g myResourceGroup --detach-acr <acr-resource-id>
```

## <a name="working-with-acr--aks"></a>Arbeta med ACR & AKS

### <a name="import-an-image-into-your-acr"></a>Importera en avbildning till ACR

Importera en avbildning från Docker Hub till ACR genom att köra följande:


```azurecli
az acr import  -n <acr-name> --source docker.io/library/nginx:latest --image nginx:v1
```

### <a name="deploy-the-sample-image-from-acr-to-aks"></a>Distribuera exempelavbildningen från ACR till AKS

Kontrollera att du har rätt AKS-autentiseringsuppgifter

```azurecli
az aks get-credentials -g myResourceGroup -n myAKSCluster
```

Skapa en fil med **namnet acr-nginx.yaml** som innehåller följande. Ersätt resursnamnet för registret med **acr-name**. Exempel: *myContainerRegistry*.

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx0-deployment
  labels:
    app: nginx0-deployment
spec:
  replicas: 2
  selector:
    matchLabels:
      app: nginx0
  template:
    metadata:
      labels:
        app: nginx0
    spec:
      containers:
      - name: nginx
        image: <acr-name>.azurecr.io/nginx:v1
        ports:
        - containerPort: 80
```

Kör sedan den här distributionen i ditt AKS-kluster:

```console
kubectl apply -f acr-nginx.yaml
```

Du kan övervaka distributionen genom att köra:

```console
kubectl get pods
```

Du bör ha två poddar som körs.

```output
NAME                                 READY   STATUS    RESTARTS   AGE
nginx0-deployment-669dfc4d4b-x74kr   1/1     Running   0          20s
nginx0-deployment-669dfc4d4b-xdpd6   1/1     Running   0          20s
```

### <a name="troubleshooting"></a>Felsökning
* Kör kommandot [az aks check-acr](/cli/azure/aks#az_aks_check_acr) för att verifiera att registret är tillgängligt från AKS-klustret.
* Läs mer om [ACR Diagnostics](../container-registry/container-registry-diagnostics-audit-logs.md)
* Läs mer om [ACR Health](../container-registry/container-registry-check-health.md)

<!-- LINKS - external -->
[AKS AKS CLI]: /cli/azure/aks#az_aks_create
[Image Pull secret]: https://kubernetes.io/docs/tasks/configure-pod-container/pull-image-private-registry/