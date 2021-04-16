---
author: sabbour
ms.author: asabbour
ms.date: 4/5/2020
ms.openlocfilehash: 1fded0ad08af4b1e5d915e32e09087c1a78bd318
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/15/2021
ms.locfileid: "107520807"
---
### <a name="create-the-cluster"></a>Skapa klustret

Följ självstudien för [att skapa ett Azure Red Hat OpenShift kluster](../tutorial-create-cluster.md). Om du väljer att installera och använda CLI lokalt kräver den här självstudien att du kör Azure CLI version 2.6.0 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI](/cli/azure/install-azure-cli).

### <a name="connect-to-the-cluster"></a>Anslut till klustret

Om du Azure Red Hat OpenShift ett kluster använder du [oc](https://docs.openshift.com/container-platform/4.7/cli_reference/openshift_cli/getting-started-cli.html), openShift-kommandoradsklienten.

> [!NOTE]
> Vi rekommenderar att [du installerar OpenShift-kommandoraden](../tutorial-connect-cluster.md) [på Azure Cloud Shell](https://shell.azure.com/) och använder den för alla kommandoradsåtgärder nedan. Starta gränssnittet från shell.azure.com eller genom att klicka på länken:
>
> [![Inbäddad start](https://docs.microsoft.com/azure/includes/media/cloud-shell-try-it/hdi-launch-cloud-shell.png "Starta Azure Cloud Shell")](https://shell.azure.com/bash)

Följ självstudien för att installera CLI, hämta klustrets autentiseringsuppgifter och ansluta till klustret med hjälp av webbkonsolen och OpenShift CLI. [](../tutorial-connect-cluster.md)

När du har loggat in bör du se ett meddelande om att du använder `default` projektet.

```output
Login successful.

You have access to 61 projects, the list has been suppressed. You can list all projects with 'oc projects'

Using project "default".
```
