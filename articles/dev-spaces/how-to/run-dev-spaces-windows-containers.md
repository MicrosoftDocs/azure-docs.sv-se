---
title: Interagera med Windows-containrar
services: azure-dev-spaces
ms.date: 01/16/2020
ms.topic: conceptual
description: Lär dig hur du kör Azure Dev Spaces på ett befintligt kluster med Windows-containrar
keywords: Azure Dev Spaces, Dev Spaces, Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, containers, Windows-containrar
ms.openlocfilehash: bbef5eafe44e38691327714c14c6a6026d45a3c7
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107777445"
---
# <a name="interact-with-windows-containers-using-azure-dev-spaces"></a>Interagera med Windows-containrar med Azure Dev Spaces

[!INCLUDE [Azure Dev Spaces deprecation](../../../includes/dev-spaces-deprecation.md)]

Du kan aktivera Azure Dev Spaces på både nya och befintliga Kubernetes-namnområden. Azure Dev Spaces kommer att köra och instrumentera tjänster som körs på Linux-containrar. Dessa tjänster kan också interagera med program som körs på Windows-containrar i samma namnområde. Den här artikeln visar hur du använder Dev Spaces för att köra tjänster i ett namnområde med befintliga Windows-containrar. Just nu kan du inte felsöka eller ansluta till Windows-containrar med Azure Dev Spaces.

## <a name="set-up-your-cluster"></a>Konfigurera klustret

Den här artikeln förutsätter att du redan har ett kluster med både Linux- och Windows-nodpooler. Om du behöver skapa ett kluster med Linux- och Windows-nodpooler kan du följa anvisningarna [här.][windows-container-cli]

Anslut till klustret med [kubectl][kubectl], Kubernetes kommandoradsklient. För att konfigurera `kubectl` till att ansluta till ditt Kubernetes-kluster använder du kommandot [az aks get-credentials][az-aks-get-credentials]. Det här kommandot laddar ned autentiseringsuppgifter och konfigurerar Kubernetes CLI för att använda dem.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

Du kan kontrollera anslutningen till klustret genom att köra kommandot [kubectl get][kubectl-get] för att returnera en lista över klusternoderna.

```azurecli-interactive
kubectl get nodes
```

Följande exempelutdata visar ett kluster med både en Windows- och Linux-nod. Kontrollera att statusen är *Klar för* varje nod innan du fortsätter.

```console
NAME                                STATUS   ROLES   AGE    VERSION
aks-nodepool1-12345678-vmss000000   Ready    agent   13m    v1.14.8
aks-nodepool1-12345678-vmss000001   Ready    agent   13m    v1.14.8
aksnpwin000000                      Ready    agent   108s   v1.14.8
```

Tillämpa en [taint på][using-taints] dina Windows-noder. Taint på Dina Windows-noder förhindrar Dev Spaces från att schemalägga Linux-containrar så att de körs på Dina Windows-noder. Följande kommandoexempelkommando tillämpar en taint på *Windows-noden aksnpwin987654* från föregående exempel.

```azurecli-interactive
kubectl taint node aksnpwin987654 sku=win-node:NoSchedule
```

> [!IMPORTANT]
> När du tillämpar en taint på en nod måste du konfigurera en matchande tolerans i tjänstens distributionsmall för att köra tjänsten på noden. Exempelprogrammet har redan konfigurerats med en [matchande tolerans för][sample-application-toleration-example] taint som du konfigurerade i föregående kommando.

## <a name="run-your-windows-service"></a>Kör Din Windows-tjänst

Kör Windows-tjänsten i AKS-klustret och  kontrollera att den körs. Den här artikeln använder [ett exempelprogram][sample-application] för att demonstrera en Windows- och Linux-tjänst som körs i klustret.

Klona exempelprogrammet från GitHub och navigera till `dev-spaces/samples/existingWindowsBackend/mywebapi-windows` katalogen:

```console
git clone https://github.com/Azure/dev-spaces
cd dev-spaces/samples/existingWindowsBackend/mywebapi-windows
```

Exempelprogrammet använder [Helm 3 för][helm-installed] att köra Windows-tjänsten i klustret. Gå till katalogen `charts` och använd Helm för att köra Windows-tjänsten:

```console
cd charts/
kubectl create ns dev
helm install windows-service . --namespace dev
```

Kommandot ovan använder Helm för att köra Windows-tjänsten i *dev-namnområdet.* Om du inte har ett namnområde med namnet *dev* skapas det.

Använd kommandot `kubectl get pods` för att kontrollera att Windows-tjänsten körs i klustret. 

```console
$ kubectl get pods --namespace dev --watch
NAME                     READY   STATUS              RESTARTS   AGE
myapi-4b9667d123-1a2b3   0/1     ContainerCreating   0          47s
...
myapi-4b9667d123-1a2b3   1/1     Running             0          98s
```

## <a name="enable-azure-dev-spaces"></a>Aktivera Azure Dev Spaces

Aktivera Dev Spaces i samma namnområde som du använde för att köra Windows-tjänsten. Följande kommando aktiverar Dev Spaces i *namnområdet* dev:

```console
az aks use-dev-spaces -g myResourceGroup -n myAKSCluster --space dev --yes
```

## <a name="update-your-windows-service-for-dev-spaces"></a>Uppdatera Din Windows-tjänst för Dev Spaces

När du aktiverar Dev Spaces på ett befintligt namnområde med containrar som redan körs försöker Dev Spaces som standard instrumentera alla nya containrar som körs i det namnområdet. Dev Spaces kommer också att försöka instrumentera alla nya containrar som skapats för tjänsten som redan körs i namnområdet. För att förhindra att Dev Spaces instrumenterar en container som körs i namnområdet lägger du *till no-proxy-huvudet* i `deployment.yaml` .

Lägg `azds.io/no-proxy: "true"` till i `existingWindowsBackend/mywebapi-windows/charts/templates/deployment.yaml` filen:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  ...
spec:
  replicas: {{ .Values.replicaCount }}
  selector:
    ...
  template:
    metadata:
      labels:
        app.kubernetes.io/name: {{ include "mywebapi.name" . }}
        app.kubernetes.io/instance: {{ .Release.Name }}
        azds.io/no-proxy: "true"
```

Använd `helm list` för att visa en lista över distributionen för Din Windows-tjänst:

```cmd
$ helm list --namespace dev
NAME             REVISION   UPDATED                    STATUS    CHART            APP VERSION    NAMESPACE
windows-service    1        Wed Jul 24 15:45:59 2019   DEPLOYED  mywebapi-0.1.0   1.0            dev
```

I exemplet ovan är namnet på distributionen *windows-service*. Uppdatera Windows-tjänsten med den nya konfigurationen med hjälp av `helm upgrade` :

```cmd
helm upgrade windows-service . --namespace dev
```

Eftersom du uppdaterade `deployment.yaml` din kommer Dev Spaces inte att försöka instrumentera din tjänst.

## <a name="run-your-linux-application-with-azure-dev-spaces"></a>Köra ditt Linux-program med Azure Dev Spaces

Gå till katalogen `webfrontend` och använd `azds prep` kommandona och för att köra `azds up` Linux-programmet i klustret.

```console
cd ../../webfrontend-linux/
azds prep --enable-ingress
azds up
```

Kommandot `azds prep --enable-ingress` genererar Helm-diagrammet och Dockerfiles för ditt program.

> [!TIP]
> [Dockerfile-](../how-dev-spaces-works-prep.md#prepare-your-code) och Helm-diagrammet för projektet används av Azure Dev Spaces för att skapa och köra din kod, men du kan ändra de här filerna om du vill ändra hur projektet byggs och körs.

Kommandot `azds up` kör din tjänst i namnområdet .

```console
$ azds up
Using dev space 'dev' with target 'myAKSCluster'
Synchronizing files...4s
Installing Helm chart...11s
Waiting for container image build...6s
Building container image...
Step 1/12 : FROM mcr.microsoft.com/dotnet/core/sdk:2.2
...
Step 12/12 : ENTRYPOINT ["/bin/bash", "/entrypoint.sh"]
Built container image in 36s
Waiting for container...2s
Service 'webfrontend' port 'http' is available at http://dev.webfrontend.abcdef0123.eus.azds.io/
Service 'webfrontend' port 80 (http) is available via port forwarding at http://localhost:57648
```

Du kan se att tjänsten körs genom att öppna den offentliga URL:en, som visas i utdata från kommandot azds up. I det här exemplet är den offentliga URL:en `http://dev.webfrontend.abcdef0123.eus.azds.io/` . Gå till tjänsten i en webbläsare och klicka på *Om* högst upp. Kontrollera att du ser ett meddelande från *tjänsten mywebapi* som innehåller den version av Windows som containern använder.

![Exempelapp som visar Windows-versionen från mywebapi](../media/run-dev-spaces-windows-containers/sample-app.png)

## <a name="next-steps"></a>Nästa steg

Läs mer om hur Azure Dev Spaces fungerar.

> [!div class="nextstepaction"]
> [Så här fungerar Azure Dev Spaces](../how-dev-spaces-works.md)

[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[helm-installed]: https://helm.sh/docs/intro/install/
[sample-application]: https://github.com/Azure/dev-spaces/tree/master/samples/existingWindowsBackend
[sample-application-toleration-example]: https://github.com/Azure/dev-spaces/blob/master/samples/existingWindowsBackend/mywebapi-windows/charts/templates/deployment.yaml#L24-L27
[az-aks-get-credentials]: /cli/azure/aks#az_aks_get_credentials
[using-taints]: ../../aks/use-multiple-node-pools.md#setting-nodepool-taints
[windows-container-cli]: ../../aks/windows-container-cli.md
