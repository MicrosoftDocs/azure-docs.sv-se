---
title: Aktivera Azure Dev Spaces på AKS & installera verktygen på klientsidan
services: azure-dev-spaces
ms.date: 07/24/2019
ms.topic: conceptual
description: Lär dig hur du aktiverar Azure Dev Spaces i ett AKS-kluster och installerar verktygen på klientsidan.
ms.custom: devx-track-azurecli
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, containers, Helm, service mesh, service mesh routing, kubectl, k8s
ms.openlocfilehash: 079a9e1b28b315457ac20d3aa9e7d29ce28fa077
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2021
ms.locfileid: "107505368"
---
# <a name="enable-azure-dev-spaces-on-an-aks-cluster-and-install-the-client-side-tools"></a>Aktivera Azure Dev Spaces i ett AKS-kluster och installera verktygen på klientsidan

[!INCLUDE [Azure Dev Spaces deprecation](../../../includes/dev-spaces-deprecation.md)]

Den här artikeln visar flera sätt att aktivera Azure Dev Spaces på ett AKS-kluster samt installera verktygen på klientsidan.

## <a name="enable-azure-dev-spaces-using-the-azure-cli"></a>Aktivera Azure Dev Spaces med hjälp av Azure CLI

Innan du kan aktivera Dev Spaces med hjälp av CLI behöver du:
* En Azure-prenumeration. Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto.][az-portal-create-account]
* [Azure CLI installerade][install-cli].
* [Ett AKS-kluster][create-aks-cli] i en [region som stöds.][supported-regions]

Använd kommandot `use-dev-spaces` för att aktivera Dev Spaces i ditt AKS-kluster och följ anvisningarna.

```azurecli
az aks use-dev-spaces -g myResourceGroup -n myAKSCluster
```

Kommandot ovan aktiverar Dev Spaces på *myAKSCluster-klustret* i *gruppen myResourceGroup* och skapar ett *standardutrymme* för utveckling.

```console
'An Azure Dev Spaces Controller' will be created that targets resource 'myAKSCluster' in resource group 'myResourceGroup'. Continue? (y/N): y

Creating and selecting Azure Dev Spaces Controller 'myAKSCluster' in resource group 'myResourceGroup' that targets resource 'myAKSCluster' in resource group 'myResourceGroup'...2m 24s

Select a dev space or Kubernetes namespace to use as a dev space.
 [1] default
Type a number or a new name: 1

Kubernetes namespace 'default' will be configured as a dev space. This will enable Azure Dev Spaces instrumentation for new workloads in the namespace. Continue? (Y/n): Y

Configuring and selecting dev space 'default'...3s

Managed Kubernetes cluster 'myAKSCluster' in resource group 'myResourceGroup' is ready for development in dev space 'default'. Type `azds prep` to prepare a source directory for use with Azure Dev Spaces and `azds up` to run.
```

Kommandot `use-dev-spaces` installerar även Azure Dev Spaces CLI.

## <a name="install-the-client-side-tools"></a>Installera verktygen på klientsidan

Du kan använda Azure Dev Spaces-verktygen på klientsidan för att interagera med dev spaces i ett AKS-kluster från din lokala dator. Det finns flera sätt att installera verktygen på klientsidan:

* I [Visual Studio Code][vscode]installerar du tillägget Azure Dev [Spaces][vscode-extension].
* I [Visual Studio 2019][visual-studio]installerar du arbetsbelastningen Azure Development.
* Ladda ned och installera [Windows,][cli-win] [Mac][cli-mac]eller [Linux][cli-linux] CLI.

## <a name="remove-azure-dev-spaces-using-the-azure-cli"></a>Ta bort Azure Dev Spaces med Hjälp av Azure CLI

Om du vill ta bort Azure Dev Spaces från ditt AKS-kluster använder du `azds remove` kommandot .

```azurecli
azds remove -g MyResourceGroup -n MyAKS
```

Följande exempelutdata visar borttagning av Azure Dev Spaces från *MyAKS-klustret.*

```azurecli
$ azds remove -g MyResourceGroup -n MyAKS
Azure Dev Spaces Controller 'MyAKS' in resource group 'MyResourceGroup' that targets resource 'MyAKS' in resource group 'MyResourceGroup' will be deleted. This will remove Azure Dev Spaces instrumentation from the target resource for new workloads. Continue? (y/N): y

Deleting Azure Dev Spaces Controller 'MyAKS' in resource group 'MyResourceGroup' that targets resource 'MyAks' in resource group 'MyResourceGroup' (takes a few minutes)...
```

Alla namnrymder som du har skapat med Azure Dev Spaces kommer att finnas kvar tillsammans med deras arbetsbelastningar, men nya arbetsbelastningar i dessa namnområden kommer inte att instrumenteras med Azure Dev Spaces. Om du startar om befintliga poddar som instrumenterats med Azure Dev Spaces kan du dessutom se fel. Dessa poddar måste omdistribueras utan Azure Dev Spaces-verktyg. Om du vill ta bort Azure Dev Spaces fullständigt från klustret tar du bort alla poddar i alla namnområden där Azure Dev Spaces har aktiverats.

## <a name="next-steps"></a>Nästa steg

Läs mer om hur Azure Dev Spaces fungerar.

> [!div class="nextstepaction"]
> [Så här fungerar Azure Dev Spaces](../how-dev-spaces-works.md)

[create-aks-cli]: ../../aks/kubernetes-walkthrough.md#create-a-resource-group
[install-cli]: /cli/azure/install-azure-cli
[supported-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service
[az-portal]: https://portal.azure.com
[az-portal-create-account]: https://azure.microsoft.com/free
[cli-linux]: https://aka.ms/get-azds-linux
[cli-mac]: https://aka.ms/get-azds-mac
[cli-win]: https://aka.ms/get-azds-windows
[visual-studio]: https://aka.ms/vsdownload?utm_source=mscom&utm_campaign=msdocs
[visual-studio-k8s-tools]: https://aka.ms/get-vsk8stools
[vscode]: https://code.visualstudio.com/download
[vscode-extension]: https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds
