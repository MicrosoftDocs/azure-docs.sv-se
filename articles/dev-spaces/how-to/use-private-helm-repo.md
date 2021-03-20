---
title: Använda en privat Helm-lagringsplats i Azure dev Spaces
services: azure-dev-spaces
author: zr-msft
ms.author: zarhoads
ms.date: 08/22/2019
ms.topic: conceptual
description: Använd en privat Helm-lagringsplats i ett Azure dev-utrymme.
keywords: Docker, Kubernetes, Azure, AKS, Azure Container Service, behållare, Helm
manager: gwallace
ms.openlocfilehash: 7c5f28595df2e552fd48033b44e4e1f0ea4ec306
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "91960345"
---
# <a name="use-a-private-helm-repository-in-azure-dev-spaces"></a>Använda en privat Helm-lagringsplats i Azure dev Spaces

[!INCLUDE [Azure Dev Spaces deprecation](../../../includes/dev-spaces-deprecation.md)]

[Helm][helm] är en paket hanterare för Kubernetes. Helm använder ett [diagram][helm-chart] format för att paketera beroenden. Helm-diagram lagras i en lagrings plats, som kan vara offentliga eller privata. Azure dev Spaces hämtar bara Helm-diagram från offentliga databaser när programmet körs. I de fall där Helm-lagringsplatsen är privat eller om Azure dev Spaces inte kan komma åt den, kan du lägga till ett diagram från lagrings platsen direkt i ditt program. Genom att lägga till diagrammet direkt kan Azure dev-Spaces köra ditt program utan att behöva komma åt den privata Helm-lagringsplatsen.

## <a name="add-the-private-helm-repository-to-your-local-machine"></a>Lägg till den privata Helm-lagringsplatsen på din lokala dator

Använd [Helm lagrings platsen Add][helm-repo-add] och [Helm lagrings platsen Update][helm-repo-update] för att få åtkomst till den privata Helm-lagringsplatsen från den lokala datorn.

```cmd
helm repo add privateRepoName http://example.com/helm/v1/repo --username user --password 5tr0ng_P@ssw0rd!
helm repo update
```

## <a name="add-the-chart-to-your-application"></a>Lägg till diagrammet i ditt program

Navigera till projekt katalogen och kör `azds prep` .

```cmd
azds prep --enable-ingress
```

> [!TIP]
> `prep`Kommandot försöker generera [ett Dockerfile-och Helm-diagram](../how-dev-spaces-works-prep.md#prepare-your-code) för projektet. Azure dev Spaces använder dessa filer för att skapa och köra din kod, men du kan ändra dessa filer om du vill ändra hur projektet skapas och körs.

Skapa en yaml-fil för [krav.][helm-requirements] med ditt diagram i programmets diagram katalog. Om ditt program till exempel heter *APP1*, skulle du skapa *diagram/APP1/krav. yaml*.

```yaml
dependencies:
    - name: mychart
      version: 0.1.0
      repository:  http://example.com/helm/v1/repo
```

Navigera till programmets diagram katalog och Använd Helm- [beroende uppdatering][helm-dependency-update] för att uppdatera Helm-beroenden för ditt program och ladda ned diagrammet från den privata lagrings platsen.

```cmd
helm dependency update
```

Verifiera en under katalog i *diagram* med en *tgz* -fil som har lagts till i programmets diagram katalog. Till exempel *Charts/APP1/Charts/mychart-0.1.0. tgz*.

Diagrammet från din privata Helm-lagringsplats har laddats ned och lagts till i projektet. Ta bort filen *Requirements. yaml* så att dev Spaces inte försöker uppdatera detta beroende.

## <a name="run-your-application"></a>Köra ditt program

Navigera till rot katalogen för projektet och kör `azds up` för att kontrol lera att programmet har körts i ditt utvecklings utrymme.

```cmd
$ azds up
Using dev space 'default' with target 'MyAKS'
Synchronizing files...2s
Installing Helm chart...2s
Waiting for container image build...2m 25s
Building container image...
...
Service 'app1' port 'http' is available at http://app1.1234567890abcdef1234.eus.azds.io/
Service 'app1' port 80 (http) is available at http://localhost:54256
...
```

## <a name="next-steps"></a>Nästa steg

Lär dig mer om [Helm och hur det fungerar][helm].

[helm]: https://docs.helm.sh
[helm-chart]: https://helm.sh/docs/topics/charts/
[helm-dependency-update]: https://helm.sh/docs/topics/charts/#managing-dependencies-with-the-dependencies-field
[helm-repo-add]: https://helm.sh/docs/intro/using_helm/#helm-repo-working-with-repositories
[helm-repo-update]: https://helm.sh/docs/intro/using_helm/#helm-repo-working-with-repositories
[helm-requirements]: https://helm.sh/docs/topics/charts/#chart-dependencies
