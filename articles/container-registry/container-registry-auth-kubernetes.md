---
title: Autentisera från Kubernetes-kluster
description: Lär dig att tillhandahålla ett Kubernetes-kluster med åtkomst till avbildningar i ditt Azure Container Registry genom att skapa en pull-hemlighet med ett huvud namn för tjänsten
ms.topic: article
author: karolz-ms
ms.author: karolz
ms.reviewer: danlep
ms.date: 05/28/2020
ms.openlocfilehash: fbf5dfd68b823b600b11cad3643e5d4004b85ff5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "84309823"
---
# <a name="pull-images-from-an-azure-container-registry-to-a-kubernetes-cluster"></a>Hämta bilder från ett Azure Container Registry till ett Kubernetes-kluster

Du kan använda ett Azure Container Registry som en källa för behållar avbildningar med valfritt Kubernetes-kluster, inklusive "lokala" Kubernetes-kluster som [minikube](https://minikube.sigs.k8s.io/) och [Natura](https://kind.sigs.k8s.io/). Den här artikeln visar hur du skapar en Kubernetes pull-hemlighet baserat på ett Azure Active Directory tjänstens huvud namn. Använd sedan hemligheten för att hämta avbildningar från ett Azure Container Registry i en Kubernetes-distribution.

> [!TIP]
> Om du använder den hanterade [Azure Kubernetes-tjänsten](../aks/intro-kubernetes.md)kan du också [integrera ditt kluster](../aks/cluster-container-registry-integration.md?toc=/azure/container-registry/toc.json&bc=/azure/container-registry/breadcrumb/toc.json) med ett Azure Container Registry-mål för avbildnings hämtningar. 

Den här artikeln förutsätter att du redan har skapat ett privat Azure Container Registry. Du måste också ha ett Kubernetes-kluster som kör och är tillgängligt via `kubectl` kommando rads verktyget.

[!INCLUDE [container-registry-service-principal](../../includes/container-registry-service-principal.md)]

Om du inte sparar eller kommer ihåg lösen ordet för tjänstens huvud namn kan du återställa det med kommandot [AZ AD SP Credential reset][az-ad-sp-credential-reset] :

```azurecli
az ad sp credential reset  --name http://<service-principal-name> --query password --output tsv
```

Det här kommandot returnerar ett nytt, giltigt lösen ord för tjänstens huvud namn.

## <a name="create-an-image-pull-secret"></a>Skapa en bilds pull-hemlighet

Kubernetes använder en *bild-pull-hemlighet* för att lagra information som behövs för att autentisera till registret. Om du vill skapa pull-hemligheten för ett Azure Container Registry, anger du tjänstens huvud namns-ID, lösen ord och register-URL. 

Skapa en bild av pull-hemlighet med följande `kubectl` kommando:

```console
kubectl create secret docker-registry <secret-name> \
    --namespace <namespace> \
    --docker-server=<container-registry-name>.azurecr.io \
    --docker-username=<service-principal-ID> \
    --docker-password=<service-principal-password>
```
där:

| Värde | Beskrivning |
| :--- | :--- |
| `secret-name` | Namnet på bildens pull-hemlighet, till exempel *ACR-Secret* |
| `namespace` | Kubernetes namn område för att flytta hemligheten till <br/> Krävs bara om du vill placera hemligheten i ett annat namn område än standard namn området |
| `container-registry-name` | Namnet på ditt Azure Container Registry, till exempel för *registret*<br/><br/>`--docker-server`Är det fullständigt kvalificerade namnet på inloggnings servern för registret  |
| `service-principal-ID` | ID för det tjänst huvud namn som ska användas av Kubernetes för att komma åt registret |
| `service-principal-password` | Lösen ord för tjänstens huvud namn |

## <a name="use-the-image-pull-secret"></a>Använd bildens pull-hemlighet

När du har skapat bildens pull-hemlighet kan du använda den för att skapa Kubernetes-poddar och-distributioner. Ange namnet på hemligheten under `imagePullSecrets` i distributions filen. Exempel:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: my-awesome-app-pod
  namespace: awesomeapps
spec:
  containers:
    - name: main-app-container
      image: myregistry.azurecr.io/my-awesome-app:v1
      imagePullPolicy: IfNotPresent
  imagePullSecrets:
    - name: acr-secret
```

I föregående exempel `my-awesome-app:v1` är namnet på avbildningen att hämta från Azure Container Registry och  `acr-secret` är namnet på den pull-hemlighet som du skapade för att få åtkomst till registret. När du distribuerar Pod hämtar Kubernetes automatiskt avbildningen från registret, om den inte redan finns i klustret.


## <a name="next-steps"></a>Nästa steg

* Mer information om hur du arbetar med tjänstens huvud namn och Azure Container Registry finns i [Azure Container Registry autentisering med tjänstens huvud namn](container-registry-auth-service-principal.md)
* Läs mer om image-pull-hemligheter i [Kubernetes-dokumentationen](https://kubernetes.io/docs/concepts/containers/images/#specifying-imagepullsecrets-on-a-pod)


<!-- IMAGES -->

<!-- LINKS - External -->
[acr-scripts-cli]: https://github.com/Azure/azure-docs-cli-python-samples/tree/master/container-registry
[acr-scripts-psh]: https://github.com/Azure/azure-docs-powershell-samples/tree/master/container-registry

<!-- LINKS - Internal -->
[az-ad-sp-credential-reset]: /cli/azure/ad/sp/credential#az-ad-sp-credential-reset