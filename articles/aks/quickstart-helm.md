---
title: Utveckla på Azure Kubernetes service (AKS) med Helm
description: Använd Helm med AKS och Azure Container Registry för att paketera och köra program behållare i ett kluster.
services: container-service
author: zr-msft
ms.topic: article
ms.date: 03/15/2021
ms.author: zarhoads
ms.openlocfilehash: 4f5232920853908aa5ad714313ead201494caa0d
ms.sourcegitcommit: 4bda786435578ec7d6d94c72ca8642ce47ac628a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/16/2021
ms.locfileid: "103493091"
---
# <a name="quickstart-develop-on-azure-kubernetes-service-aks-with-helm"></a>Snabb start: utveckla på Azure Kubernetes service (AKS) med Helm

[Helm][helm] är ett paket med öppen källkod som hjälper dig att installera och hantera livs cykeln för Kubernetes-program. På liknande sätt som med Linux-paket som *apt* och *yum* hanterar Helm Kubernetes-diagram, som är paket med förkonfigurerade Kubernetes-resurser.

I den här snabb starten använder du Helm för att paketera och köra ett program på AKS. Mer information om hur du installerar ett befintligt program med hjälp av Helm finns i [Installera befintliga program med Helm i AKS][helm-existing] instruktions guide.

## <a name="prerequisites"></a>Förutsättningar

* En Azure-prenumeration. Om du inte har någon Azure-prenumeration kan du skapa ett [kostnads fritt konto](https://azure.microsoft.com/free).
* [Azure CLI installerat](/cli/azure/install-azure-cli).
* [Helm v3 installerat][helm-install].

## <a name="create-an-azure-container-registry"></a>Skapa ett Azure Container Registry
Du måste lagra behållar avbildningarna i en Azure Container Registry (ACR) för att köra ditt program i ditt AKS-kluster med hjälp av Helm. Ange ett eget register namn som är unikt i Azure och som innehåller 5-50 alfanumeriska tecken. Den *grundläggande* SKU:n är en kostnadsoptimerad startpunkt för utvecklingsändamål som ger en bra balans mellan lagring och dataflöde.

I exemplet nedan används [AZ ACR Create][az-acr-create] för att skapa en ACR med namnet *MyHelmACR* i *MyResourceGroup* med *Basic* SKU.

```azurecli
az group create --name MyResourceGroup --location eastus
az acr create --resource-group MyResourceGroup --name MyHelmACR --sku Basic
```

Utdata ser ut ungefär som i följande exempel. Anteckna ditt *namnet* -värde för ditt ACR eftersom du kommer att använda det i ett senare steg. I exemplet nedan är *Myhelmacr.azurecr.io* *namnet* för *myhelmacr*.

```console
{
  "adminUserEnabled": false,
  "creationDate": "2019-06-11T13:35:17.998425+00:00",
  "id": "/subscriptions/<ID>/resourceGroups/MyResourceGroup/providers/Microsoft.ContainerRegistry/registries/MyHelmACR",
  "location": "eastus",
  "loginServer": "myhelmacr.azurecr.io",
  "name": "MyHelmACR",
  "networkRuleSet": null,
  "provisioningState": "Succeeded",
  "resourceGroup": "MyResourceGroup",
  "sku": {
    "name": "Basic",
    "tier": "Basic"
  },
  "status": null,
  "storageAccount": null,
  "tags": {},
  "type": "Microsoft.ContainerRegistry/registries"
}
```

## <a name="create-an-aks-cluster"></a>Skapa ett AKS-kluster

Ditt nya AKS-kluster behöver åtkomst till din ACR för att hämta behållar avbildningarna och köra dem. Använd följande kommando för att:
* Skapa ett AKS-kluster med namnet *MyAKS* och bifoga *MyHelmACR*.
* Bevilja *MyAKS* -klustret åtkomst till din *MyHelmACR* -ACR.


```azurecli
az aks create -g MyResourceGroup -n MyAKS --location eastus  --attach-acr MyHelmACR --generate-ssh-keys
```

## <a name="connect-to-your-aks-cluster"></a>Anslut till ditt AKS-kluster

Om du vill ansluta ett Kubernetes-kluster lokalt använder du kommando rads klienten Kubernetes, [kubectl][kubectl]. `kubectl` är redan installerat om du använder Azure Cloud Shell. 

1. Installera `kubectl` lokalt med `az aks install-cli` kommandot:

    ```azurecli
    az aks install-cli
    ```

2. Konfigurera `kubectl` för att ansluta till ditt Kubernetes-kluster med hjälp av `az aks get-credentials` kommandot. Följande kommando exempel hämtar autentiseringsuppgifter för AKS-klustret med namnet *MyAKS* i *MyResourceGroup*:  

    ```azurecli
    az aks get-credentials --resource-group MyResourceGroup --name MyAKS
    ```

## <a name="download-the-sample-application"></a>Hämta exempelprogrammet

I den här snabb starten används [ett exempel Node.js program från Azure dev Spaces-lagringsplatsen][example-nodejs]. Klona programmet från GitHub och navigera till `dev-spaces/samples/nodejs/getting-started/webfrontend` katalogen.

```console
git clone https://github.com/Azure/dev-spaces
cd dev-spaces/samples/nodejs/getting-started/webfrontend
```

## <a name="create-a-dockerfile"></a>Hur du skapar en Dockerfile

Skapa en ny *Dockerfile* -fil med hjälp av följande kommandon:

```dockerfile
FROM node:latest

WORKDIR /webfrontend

COPY package.json ./

RUN npm install

COPY . .

EXPOSE 80
CMD ["node","server.js"]
```

## <a name="build-and-push-the-sample-application-to-the-acr"></a>Bygg och skicka exempel programmet till ACR

Med hjälp av föregående Dockerfile kör du kommandot [AZ ACR build][az-acr-build] för att skapa och skicka en avbildning till registret. I `.` slutet av kommandot anges platsen för Dockerfile (i det här fallet den aktuella katalogen).

```azurecli
az acr build --image webfrontend:v1 \
  --registry MyHelmACR \
  --file Dockerfile .
```

## <a name="create-your-helm-chart"></a>Skapa ditt Helm-diagram

Generera ditt Helm-diagram med `helm create` kommandot.

```console
helm create webfrontend
```

Uppdatera *webfrontend/Values. yaml*:
* Ersätt namnet i ditt register som du antecknade i ett tidigare steg, till exempel *myhelmacr.azurecr.io*.
* Ändra `image.repository` till `<loginServer>/webfrontend`
* Ändra `service.type` till `LoadBalancer`

Exempel:

```yml
# Default values for webfrontend.
# This is a YAML-formatted file.
# Declare variables to be passed into your templates.

replicaCount: 1

image:
  repository: myhelmacr.azurecr.io/webfrontend
  pullPolicy: IfNotPresent
...
service:
  type: LoadBalancer
  port: 80
...
```

Uppdatera `appVersion` till `v1` i *webfrontend/Chart. yaml*. Till exempel

```yml
apiVersion: v2
name: webfrontend
...
# This is the version number of the application being deployed. This version number should be
# incremented each time you make changes to the application.
appVersion: v1
```

## <a name="run-your-helm-chart"></a>Kör ditt Helm-diagram

Installera ditt program med hjälp av Helm-diagrammet med hjälp av `helm install` kommandot.

```console
helm install webfrontend webfrontend/
```

Det tar några minuter för tjänsten att returnera en offentlig IP-adress. Övervaka förloppet med `kubectl get service` kommandot med `--watch` argumentet.

```console
$ kubectl get service --watch

NAME                TYPE          CLUSTER-IP    EXTERNAL-IP   PORT(S)        AGE
webfrontend         LoadBalancer  10.0.141.72   <pending>     80:32150/TCP   2m
...
webfrontend         LoadBalancer  10.0.141.72   <EXTERNAL-IP> 80:32150/TCP   7m
```

Navigera till programmets belastningsutjämnare i en webbläsare med hjälp av `<EXTERNAL-IP>` för att se exempel programmet.

## <a name="delete-the-cluster"></a>Ta bort klustret

Använd kommandot [AZ Group Delete][az-group-delete] för att ta bort resurs gruppen, AKS-klustret, behållar registret, behållar avbildningarna som lagras i ACR och alla relaterade resurser.

```azurecli-interactive
az group delete --name MyResourceGroup --yes --no-wait
```

> [!NOTE]
> När du tar bort klustret tas Azure Active Directory-tjänstens huvudnamn, som används av AKS-klustret, inte bort. Stegvisa instruktioner om hur du tar bort tjänstens huvudnamn finns i dokumentationen om [viktiga överväganden och borttagning av AKS-tjänsten][sp-delete].
> 
> Om du använde en hanterad identitet hanteras identiteten av plattformen och kräver inte borttagning.

## <a name="next-steps"></a>Nästa steg

Mer information om hur du använder Helm finns i Helm-dokumentationen.

> [!div class="nextstepaction"]
> [Helm-dokumentation][helm-documentation]

[az-acr-create]: /cli/azure/acr#az-acr-create
[az-acr-build]: /cli/azure/acr#az-acr-build
[az-group-delete]: /cli/azure/group#az-group-delete
[az aks get-credentials]: /cli/azure/aks#az-aks-get-credentials
[az aks install-cli]: /cli/azure/aks#az-aks-install-cli
[example-nodejs]: https://github.com/Azure/dev-spaces/tree/master/samples/nodejs/getting-started/webfrontend
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[helm]: https://helm.sh/
[helm-documentation]: https://helm.sh/docs/
[helm-existing]: kubernetes-helm.md
[helm-install]: https://helm.sh/docs/intro/install/
[sp-delete]: kubernetes-service-principal.md#additional-considerations
