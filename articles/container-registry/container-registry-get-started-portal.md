---
title: Snabbstart – Skapa register i portalen
description: Lär dig snabbt att skapa ett privat Azure-containerregister med hjälp av Azure Portal.
ms.date: 08/04/2020
ms.topic: quickstart
ms.custom:
- mvc
- mode-portal
ms.openlocfilehash: b59e605ea0484bd70456e278cb712683189b251d
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2021
ms.locfileid: "107533938"
---
# <a name="quickstart-create-an-azure-container-registry-using-the-azure-portal"></a>Snabbstart: Skapa ett Azure-containerregister med hjälp av Azure Portal

Ett Azure-containerregister är ett privat Docker-register i Azure där du kan lagra och hantera privata Docker-containeravbildningar och relaterade artefakter. I den här snabbstarten skapar du ett containerregister med Azure-portalen. Använd därefter Docker-kommandon för att skicka en containeravbildning till registret, och hämta och kör avbildningen slutligen från registret.

För att logga in i registret för att arbeta med containeravbildningar kräver den här snabbstarten att du kör Azure CLI (version 2.0.55 eller senare rekommenderas). Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI][azure-cli].

Du måste också ha Docker installerat lokalt. Docker innehåller paket som enkelt kan konfigurera Docker på en [Mac][docker-mac]-, [Windows][docker-windows]- eller [Linux][docker-linux]-dator.

## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på Azure Portal på https://portal.azure.com.

## <a name="create-a-container-registry"></a>Skapa ett containerregister

Välj **Skapa en resurs**  >  **Containrar**  >  **Container Registry**.

:::image type="content" source="media/container-registry-get-started-portal/qs-portal-01.png" alt-text="Navigera till containerregistret i portalen":::

På fliken **Grundläggande anger** du värden för **Resursgrupp och** **Registernamn**. Registernamnet måste vara unikt i Azure och innehålla 5–50 alfanumeriska tecken. För den här snabbstarten skapar du en ny resursgrupp på platsen `West US` med namnet `myResourceGroup`, och för **SKU** väljer du ”Basic”.

:::image type="content" source="media/container-registry-get-started-portal/qs-portal-03.png" alt-text="Skapa containerregister i portalen":::

Acceptera standardvärden för de återstående inställningarna. Välj sedan **Granska + skapa**. När du har granskat inställningarna väljer du **Skapa**.

I den här snabbstarten skapar du ett *Basic*-register, vilket är ett kostnadsoptimerat alternativ för utvecklare som lär sig om Azure Container Registry. Mer information om tillgängliga tjänstnivåer (SKU:er) finns [i Tjänstnivåer för Container Registry.][container-registry-skus]

När meddelandet **Distribueringen lyckades** visas väljer du containerregistret i portalen. 

:::image type="content" source="media/container-registry-get-started-portal/qs-portal-05.png" alt-text="Översikt över containerregister i portalen":::

Anteckna registernamnet och värdet för **inloggningsservern**. Du använder dessa värden i följande steg när du push-hämtar avbildningar med Docker.

## <a name="log-in-to-registry"></a>Logga in till registret

Innan du push-pushar och drar containeravbildningar måste du logga in på registerinstansen. [Logga in på Azure CLI][get-started-with-azure-cli] på den lokala datorn och kör sedan kommandot az [acr login.][az-acr-login] Ange bara registernamnet när du loggar in med Azure CLI. Använd inte inloggningsservernamnet, som innehåller ett domänsuffix som `azurecr.io` .

```azurecli
az acr login --name <registry-name>
```

Exempel:

```azurecli
az acr login --name mycontainerregistry
```

Kommandot returnerar `Login Succeeded` när det har slutförts. 

[!INCLUDE [container-registry-quickstart-docker-push](../../includes/container-registry-quickstart-docker-push.md)]

## <a name="list-container-images"></a>Visa lista över containeravbildningar

Om du vill visa en lista över avbildningarna i registret navigerar du till ditt register i portalen och väljer **Lagringsplatsen** och väljer sedan lagringsplatsen  **hello-world** som du skapade med `docker push` .

:::image type="content" source="media/container-registry-get-started-portal/qs-portal-09.png" alt-text="Visa en lista över containeravbildningar i portalen":::

När du väljer **lagringsplatsen hello-world** visas den `v1` taggade bilden under **Taggar**.

[!INCLUDE [container-registry-quickstart-docker-pull](../../includes/container-registry-quickstart-docker-pull.md)]

## <a name="clean-up-resources"></a>Rensa resurser

Om du vill rensa dina resurser går du till resursgruppen **myResourceGroup** i portalen. När resursgruppen har lästs in klickar du på Ta bort **resursgrupp** för att ta bort resursgruppen, containerregistret och containeravbildningarna som lagras där.

:::image type="content" source="media/container-registry-get-started-portal/qs-portal-08.png" alt-text="Ta bort resursgrupp i portalen":::


## <a name="next-steps"></a>Nästa steg

I den här snabbstarten skapade du ett Azure Container Registry med Azure-portalen, push-överförde en containeravbildning och hämtade och körde avbildningen från registret. Fortsätt till självstudien om Azure Container Registry om du vill titta närmare på ACR.

> [!div class="nextstepaction"]
> [Självstudier för Azure Container Registry][container-registry-tutorial-prepare-registry]

> [!div class="nextstepaction"]
> [Azure Container Registry-uppgifter självstudier][container-registry-tutorial-quick-task]

<!-- LINKS - external -->
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-pull]: https://docs.docker.com/engine/reference/commandline/pull/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[docker-rmi]: https://docs.docker.com/engine/reference/commandline/rmi/
[docker-run]: https://docs.docker.com/engine/reference/commandline/run/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/
[docker-windows]: https://docs.docker.com/docker-for-windows/

<!-- LINKS - internal -->
[container-registry-tutorial-prepare-registry]: container-registry-tutorial-prepare-registry.md
[container-registry-skus]: container-registry-skus.md
[azure-cli]: /cli/azure/install-azure-cli
[get-started-with-azure-cli]: /cli/azure/get-started-with-azure-cli
[az-acr-login]: /cli/azure/acr#az-acr-login
[container-registry-tutorial-quick-task]: container-registry-tutorial-quick-task.md
