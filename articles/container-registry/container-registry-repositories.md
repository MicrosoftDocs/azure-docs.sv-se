---
title: Visa databaser i portalen
description: Använd Azure Portal för att Visa Azure Container Registry-databaser, som är värd för Docker-behållar avbildningar och andra artefakter som stöds.
ms.topic: article
ms.date: 01/05/2018
ms.openlocfilehash: 1da72706d2554610a685f71199ab14af5e30ce1a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "74456291"
---
# <a name="view-container-registry-repositories-in-the-azure-portal"></a>Visa behållare register Arkiv i Azure Portal

Med Azure Container Registry kan du lagra Docker-behållar avbildningar i-databaser. Genom att lagra avbildningar i lagrings utrymmen kan du lagra grupper med bilder (eller versioner av avbildningar) i isolerade miljöer. Du kan ange dessa databaser när du push-överför avbildningar till registret och visa innehållet i Azure Portal.

## <a name="prerequisites"></a>Förutsättningar

* **Behållar register**: skapa ett behållar register i din Azure-prenumeration. Använd till exempel [Azure Portal](container-registry-get-started-portal.md) eller [Azure CLI](container-registry-get-started-azure-cli.md).
* **Docker CLI**: installera [Docker][docker-install] på den lokala datorn, som ger dig kommando rads gränssnittet Docker.
* **Behållar avbildning**: skicka en avbildning till behållar registret. Information om hur du push-överför och hämtar bilder finns i [skicka och hämta en avbildning](container-registry-get-started-docker-cli.md).

## <a name="view-repositories-in-azure-portal"></a>Visa databaser i Azure Portal

Du kan se en lista över de databaser som är värdar för dina avbildningar, samt avbildnings taggarna i Azure Portal.

Om du följde stegen i [skicka och hämta en avbildning](container-registry-get-started-docker-cli.md) (och sedan inte har tagit bort avbildningen) bör du ha en nginx-avbildning i ditt container Registry. Anvisningarna i artikeln anger att du taggar avbildningen med ett namn område, "Samples" i `/samples/nginx` . Som en uppdaterare var [Docker push][docker-push] -kommandot som anges i artikeln:

```Bash
docker push myregistry.azurecr.io/samples/nginx
```

 Eftersom Azure Container Registry stöder lagrings utrymmen för flera platser kan du omfattnings samlingar med avbildningar relaterade till en särskild app, eller en samling av appar, till olika utvecklings-eller drift team. Om du vill läsa mer om databaser i behållar register, se [privata Docker-behållar register i Azure](container-registry-intro.md).

Så här visar du en lagrings plats:

1. Logga in på [Azure-portalen][portal]
1. Välj den **Azure Container Registry** som du skickade nginx-avbildningen till
1. Välj **databaser** om du vill visa en lista över de databaser som innehåller avbildningarna i registret
1. Välj en lagrings plats för att se avbildnings taggarna i den lagrings platsen

Om du till exempel har push-överfört nginx-avbildningen enligt anvisningarna i [skicka och hämta en avbildning](container-registry-get-started-docker-cli.md), bör du se något som liknar:

![Databaser i portalen](./media/container-registry-repositories/container-registry-repositories.png)

## <a name="next-steps"></a>Nästa steg

Nu när du vet grunderna för att visa och arbeta med databaser i portalen kan du prova att använda Azure Container Registry med ett [AKS-kluster (Azure Kubernetes service)](../aks/tutorial-kubernetes-prepare-app.md) .

<!-- LINKS - External -->
[docker-install]: https://docs.docker.com/engine/installation/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[portal]: https://portal.azure.com
