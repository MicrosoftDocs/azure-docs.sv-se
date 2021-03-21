---
title: Använda Azure Container Registry med Azure Red Hat OpenShift
description: Lär dig hur du hämtar och kör en behållare från Azure Container Registry i ditt Azure Red Hat OpenShift-kluster.
author: sakthi-vetrivel
ms.author: suvetriv
ms.service: azure-redhat-openshift
ms.topic: conceptual
ms.date: 01/10/2021
ms.openlocfilehash: 651b73db084e8090f59faeffa9991c2ac468ca08
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "100634421"
---
# <a name="use-azure-container-registry-with-azure-red-hat-openshift-aro"></a>Använda Azure Container Registry med Azure Red Hat OpenShift (ARO)

Azure Container Registry (ACR) är en hanterad behållare Registry-tjänst som du kan använda för att lagra privata Docker-behållar avbildningar med företags funktioner som geo-replikering. För att få åtkomst till ACR från ett ARO-kluster kan klustret autentisera med ACR genom att lagra inloggnings uppgifter för Docker i en Kubernetes-hemlighet.  På samma sätt kan ett ARO-kluster använda en imagePullSecret i pod-specifikationen för att autentisera mot registret när avbildningen hämtades. I den här artikeln får du lära dig hur du konfigurerar en Azure Container Registry med ett Azure Red Hat OpenShift-kluster för att lagra och hämta privata Docker-behållar avbildningar.

## <a name="prerequisites"></a>Förutsättningar

Den här guiden förutsätter att du har en befintlig Azure Container Registry. Om du inte gör det använder du Azure Portal-eller [Azure CLI-instruktionerna](../container-registry/container-registry-get-started-azure-cli.md) för att skapa ett behållar register.

Den här artikeln förutsätter också att du har ett befintligt kluster med en Red Hat OpenShift-kluster och att `oc` CLI är installerat. Annars följer du anvisningarna i [själv studie kursen skapa Aro-kluster](tutorial-create-cluster.md).

## <a name="get-a-pull-secret"></a>Hämta en pull-hemlighet

Du behöver en pull-hemlighet från ACR för att komma åt registret från ditt ARO-kluster.

Du kan använda antingen Azure Portal eller Azure CLI för att hämta hemliga autentiseringsuppgifter.

Om du använder Azure Portal navigerar du till ACR-instansen och väljer **åtkomst nycklar**.  Ditt `docker-username` namn på behållar registret använder antingen lösen ord eller password2 för `docker-password` .

![Åtkomst nycklar](./media/acr-access-keys.png)

I stället kan du använda Azure CLI för att hämta följande autentiseringsuppgifter:
```azurecli
az acr credential show -n <your registry name>
```

## <a name="create-the-kubernetes-secret"></a>Skapa Kubernetes-hemligheten

Nu ska vi använda dessa autentiseringsuppgifter för att skapa en Kubernetes-hemlighet. Kör följande kommando med dina ACR-autentiseringsuppgifter:

```
oc create secret docker-registry \
    --docker-server=<your registry name>.azurecr.io \
    --docker-username=<your registry name> \
    --docker-password=******** \
    --docker-email=unused \
    acr-secret
```

>[!NOTE]
>Den här hemligheten lagras i det aktuella OpenShift-projektet (Kubernetes-namnrymd) och kommer bara att refereras till av poddar som skapats i projektet.  I det här [dokumentet](https://docs.openshift.com/container-platform/4.4/openshift_images/managing_images/using-image-pull-secrets.html) finns mer information om hur du skapar en pull-hemlighet för ett kluster.

## <a name="create-a-pod-using-a-private-registry-image"></a>Skapa en POD med en privat register avbildning

Nu när vi har anslutit ditt ARO-kluster till din ACR kan vi hämta en bild från din ACR för att skapa en pod.

Börja med en podSpec och ange hemligheten som du skapade som imagePullSecret:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: hello-world
spec:
  containers:
  - name: hello-world
    image: <your registry name>.azurecr.io/hello-world:v1
  imagePullSecrets:
  - name: acr-secret
```

Testa att pod är igång genom att köra det här kommandot och vänta tills statusen **körs**:

```bash
$ oc get pods --watch
NAME         READY   STATUS    RESTARTS   AGE
hello-world  1/1     Running   0          30s
```

## <a name="next-steps"></a>Nästa steg

* [Azure Container Registry](../container-registry/container-registry-concepts.md)
* [Snabb start: skapa ett privat behållar register med hjälp av Azure CLI](../container-registry/container-registry-get-started-azure-cli.md)
